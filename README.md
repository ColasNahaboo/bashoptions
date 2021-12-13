# Bashoptions
## What?
**Bashoptions** is a **powerful but light**  option parsing system for **bash shell** scripts, consisting of just 9 lines of bash code to **paste** in your scripts, providing options parsing à la [getopt](https://www.mankier.com/1/getopt) getopt (or using the builtin [getopts](https://www.mankier.com/1/bash#getopts) getopts) but without call nor dependencies to any external programs, and with some useful convenience functions for bash scripts. 

It comes in different flavors:
- `bashoptions`, parses long and short options, and provides my convenience functions. It is similar in power to getopt, but its use is a bit simpler, notably because you do not have to pre-declare options in an options string.
- `bashoptions-lite`, same as above but does not provides any additional convenience functions.
- `bashoptions-getopts`, parses only short options via the bash built-in standard getopts, but also provide the same convenience functions as bashoptions. 
- `bashoptions-getopts-lite`, for completeness, but is just basically getopts.

It is totally free of use, under the MIT License.

It passes shellcheck.

It can be viewed - in all modesty - as Concrete Poetry applied to bash coding: Since the bashoptions source code was to be copy/pasted into your source code, I tried to minimize its visual bulk, but compacting it in as few lines of 80 chars as possibles, to give the impression of a "decorative" block

## How?
Choose your file flavor, and copy-paste its code into your program. You will see the blocks of code (between comments), and the places you have to fill (contents of the USAGE and OPTIONS variables), and some examples of use as indented lines you must replace by your code. 
Note: running the file prints only what you have to copy, stripped of the examples.
### bashoptions & bashoptions-lite
Before the first code block, define the bash variable `USAGE` to be the text printed for the options `--help`, `-h`, or `-?`. Defining your own options of the same name  will override this behavior.
Initialize there also the variables that could be changed by options.

Between the 1rst and 2nd code block, define your options in the form of case choices. If the option expects an argument, you access its value as `$OPTARG`. The name of the option itself being in the variable `$_o`.

E.g, to accept options: `--silent`, `--level=1`, or `--level 1` use the following code:
```
>    silent) set_verbosity 0;;       # accepts no argument
>    level) level="$OPTARG";;        # with an argument
```
Between the 2nd and 3rd code block, define your short options, the ones consisting of only one letter in the same way as for the long options above. Single letter options can be grouped, as long as the group is prefixed by only one dash, not two.

E.g, to accept options: `-s -l 1` or `-sl 1` use the following code:
```
>    s) set_verbosity 0;;            # accepts no argument
>    l) $HASARG; level="$OPTARG";;   # with an argument
```
And after the last block, you can run your code, knowing that the rest of non-option arguments to your commands can be found as the normal parameters ,$1 ,$2, $3, ... (options have been shifted away)

Bashoptions-lite provides only this functionality above, while bashoptions add the "Goodies" below.

Note that you do not have to pre-declare the list of accepted options in an options string. The options not catched by one of your case statements will automatically trigger the error message "***ERROR: Bad option: " and abort the program.

### bashoptions-getopts
Before the first code block, define the bash variable `USAGE` to be the text printed for the options `-h`, or `-?`. Defining your own  options of the same letters will override this behavior.
Define in the bash variable `OPTIONS` the options string that is normally passed to [getopts](https://www.mankier.com/1/bash#getopts), that is all the option letters concatenated, with a colon (:) appended to the ones requiring an argument. There is no need to add `h?` to this string.
Initialize there also the variables that could be changed by options.

Between the 1st and 2nd code block, define your options as case choices for each letter. If they accept an argument, it can be found in the `$OPTARG` variable. The name of the option itself being in `$_o`.

E.g, to accept options: `-s -l 1` or `-sl 1` use the following code:
```
>    OPTIONS="sl:"
>    ...
>    s) set_verbosity 0;;            # accepts no argument
>    l) level="$OPTARG";;       # with an argument
```
And after the last block, you can run your code, knowing that the rest of non-option arguments to your commands can be found as the normal parameters ,$1 ,$2, $3, ... (options have been shifted away)

`bashoptions-getopts` also provides the "Goodies" below.

 `bashoptions-getopts-lite`... is just basically getopts.

## Why?
I have been using them in my scripts for decades, but I decided to package them more formally, with the set of convenience functions I use everywhere. In all honesty, they are not so useful now that `getopts` and `getopt` exist, but they may appeal to people wanting long options but not wanting to fork an external program for them. And they can be a demonstration of the power of bash programming.

## Goodies
`Bashoptions` and `bashoptions-getopts` define some useful functions and variables for your bash scripting needs. If you do not want them, just use `bashoptions-lite` or `getopt`.

### predefined options

- **-v** verbose mode: enable some functions (see below)
- **-h** or **-?** help: displays the contents of the USAGE shall variable and exit

#### verbose mode functions

Some provided convenience functions do nothing normally, but are activated when **-v** is used:

- **V** Displays (via `echo`) its arguments on the stderr prefixed by `== `

  E.g. a line `V "The value of foo is $foo"` in your code will do nothing (but will evaluate its arguments), unless `-v` is used, then it will echo: `== The value of foo is ...`

- **T** traces variables: it prints its argument, with their value if they are variable names or verbatim otherwise, as labels.
  E.g a line `T pass2: X Y Z` will print "pass2:" as is because of the colon, and the 3 variables with their values:

  `== pass2: X=1 Y=2 Z=3`

- Also, the variable **v** is set to `false` if `-v` was not specified, but set to `true` if `-v` was used.

### functions

- **err** displays its arguments prefixed by `***ERROR:` on standard error, and exits with return code 1
  E.g: `[[ $x =~ ^[[:digit:]]+$ ]] || err "x is not an integer: \"$x\"`
  displays: `***ERROR: x is not an integer: e2`

- **warn** displays its arguments on standard error prefixed by `###Warning: `

  E.g: `[[ -d $file ]] && warn "\"$file\" is a directory!"`
  displays: `###Warning: "data" is a directory!`

- `E` and `En` are synonyms for `echo` and `echo -n`

### variables

- **_o** is the current option name, without the leading dash
- **OPTARG** is the current option argument, if any
- **nl** just holds the newline character, often useful

## Versions
- v1.0 2021-12-13 First version published on GitHub
