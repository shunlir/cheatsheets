[TOC]

# sed cheat sheet

`sed`, **s**tream **ed**itor.

This cheat sheet applies GNU `sed`

Usage: `sed OPTIONS... [SCRIPT] [INPUTFILE...]`

The `SCRIPT` can be consisted of one more commands.: `command[; command[...]]`

##### Often-used commands

- `d`:             delete the pattern space
- `a text`:   append `text` after line
- `i text`:   insert `text` before line
- `c text`:   replace line(s) with `text` 
- `s/regexp/replacement/[flags]` (substitute) Match the regular-expression against the content of the
  pattern space.  If found, replace matched string with `replacement`. 
- `p`:                      print out the pattern space 
- `{commands}`:   a group of commands

##### Specify multiple commands

```shell
sed [-e] 'command1; command2;' INPUTFILE
```

```shell
sed -e 'command1' -e 'command2' INPUTFILE
```

##### Addresses

Commands are executed only after address condition supplies.

```
[address[, address]]command
```

`address` can be specified by line number:

- `n`, where `n` in `[1, max_line_no]`, the `n`-th line 
- `$` the last line
- `n,m`, where `1 <=n <=m <=max_line_no`, the `n`-th(including) to `m`-th(including) lines
- `n,+x`: the `n`-th to `n+x`-th lines
- `n,~x`: starting from `n`-th line, then every `x` line

`address` can also be specified by pattern.

##### Print lines

```shell
seq 1 10 | sed -n '1,2p' #print line 1 to 2. -n is required, otherwise line 1 and 2 are duplicated
seq 1 10 | sed -n '1~2p' #print every other line
echo -e '1ab\n2cd\n3ef' | sed -n -e '/ab/,/cd/ p'
```

##### Insert text

```shell
echo -e '1ab\n2cd\n3ef' | sed -e '1a new line'     #append `new line` after line 1
echo -e '1ab\n2cd\n3ef' | sed -e '/1ab/a new line' #append `new line` after pattern
echo -e '1ab\n2cd\n3ef' | sed -e '1i new line'
```

##### Substitute

```shell
echo -e '1ab\n2cd\n3ab' | sed -e '1,2 s/ab/AB' #substitute applies in line range [1, 2]
echo -e '1abab\n2cd\n3ef' | sed -e 's/ab/AB/'  #only first match in line
echo -e '1abab\n2cd\n3ef' | sed -e 's/ab/AB/g' #all matches in line
```

###### Referencing Matched Text

```shell
echo -e '1abab\n2cd\n3ef' | sed -e 's/ab/(&)/g'  #& means the matched text, same with \0
echo -e 'var=value' | sed -r -e 's/var=(.*)/\1/' #\1 means text of matched group 1
```

##### More Examples

###### Configure `vi-mode` in command line editing for Oh-my-zsh

```shell
sed -i -r '/plugins\s*=\s*\(/,/\)/{s/git/&\n  vi-mode/}' ~/.zshrc   #in-place edit
cat ~/.zshrc | sed -n -r '/plugins\s*=\s*\(/,/\)/{s/git/&\n  vi-mode/; p}' #print result
```

###### Delete lines between pattern (including)

```shell
sed -i '/pattern1/,/pattern2/d' FILE
```

###### Delete lines between (excluding)

```shell
sed -i '/pattern1/,/pattern2/{//!d} FILE
```

Note: The empty regular expression `//` repeats the last regular expression match (the same holds if the empty regular expression is passed to the `s` command).

###### Delete all blank lines from a file

```shell
sed '/^$/d' FILE
```

###### Delete trailing whitespace from end of each line

```shell
sed -r '/\s+$//' FILE
```

###### Change `PermitRootLogin` to `yes` no matter what is already set to

```
sed '/PermitRootLogin / s/ .*/ yes/' /etc/ssh/sshd_config
```



##### How sed works

`sed` maintains two data buffers: the active *pattern* space, and the auxiliary *hold* space. Both are initially empty. 

`sed` operates by performing the following cycle on each line of input: first, `sed` reads one line from the input stream, removes any trailing newline, and places it in the pattern space. Then commands are executed; each command can have an address associated to it: addresses are a kind of condition code, and a command is only executed if the condition is verified before the command is to be executed. 

When the end of the script is reached, unless the -n option is in use, the contents of pattern space are printed out to the output stream, adding back the trailing newline if it was removed.[8](https://www.gnu.org/software/sed/manual/sed.html#FOOT8) Then the next cycle starts for the next input line. 

Unless special commands (like ‘D’) are used, the pattern space is deleted between two cycles. The hold space, on the other hand, keeps its data between cycles (see commands ‘h’, ‘H’, ‘x’, ‘g’, ‘G’ to move data between both buffers). 

[1]: https://www.gnu.org/software/sed/manual/sed.html	"Gnu sed maunal"
[2]: http://sed.sf.net/sed1line.txt	"The sed one-liners"

[3]: https://thornelabs.net/2016/02/01/sed-commands-cheat-sheet.html	"sed Commands Cheat Sheet"

