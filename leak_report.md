# Leak report

At the start of the lab, there's one memory leak in `check_whitespace.c`. The
`strip()` function may return a `calloc`ed string, but the caller,
`is_clean()`, doesn't free this string after it's done using it.

The fix is to change `is_clean()`, so that `is_clean()` always frees the string
it gets from `strip()`.

But, there's a slight catch: the way `strip()` is written at the start of the
lab, it sometimes returns a `calloc`ed string (line 49) and sometimes returns a
string literal (line 36). We need to free `calloc`ed strings, but freeing
string literals is Bad. String literals are [put in static, read-only
storage][1]; they last the whole lifetime of the program.

So, we should change `strip()` so that it always returns a `calloc`ed string,
never a literal. That way the caller can treat the return value consistently.

[1]: https://stackoverflow.com/a/9970305
