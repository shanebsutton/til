# Single and Double Quotes

In the bash shell (and most others?), single and double quotes behave differently.

## Single quotes

Single quotes "preserve the literal value of each character within the quotes". They prevent the shell from interpreting special characters.

```bash
$ echo 'Home directory: $HOME'
Home directory: $HOME
```

## Double quotes

Double quotes "preserve the literal value of all characters within the quotes, with the exception of `$`, `` ` `` (back tick), and `\`".

```bash
$ echo "Home directory: $HOME"
Home directory: /home/shane_sutton

$ echo "Single ' and double \" quotes can be used within double quotes by using the backslash to escape the double."
Single ' and double " quotes can be used within double quotes by using the backslash to escape the double.

$ echo "The backslash \ is used to escape \$, \`, and \" within double quotes."
The backslash \ is used to escape $, `, and " within double quotes.
```

## References

- [bash manual - quoting](https://www.gnu.org/software/bash/manual/html_node/Quoting.html)
