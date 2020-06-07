
`${varname:offset:length}` | perform substring expansion, returns the substring of $varname starting at offset & up to length characters
`${variable#pattern}` | if pattern matches the beginning of the variable's value, delete the shortest part that matches & return the rest
`${variable##pattern}` | if pattern matches the beginning of the variable's value, delete the longest part that matches & return the rest
`${variable%pattern}` | if pattern matches the end of the variable's value, delete the shortest part that matches & return the rest
`${variable%%pattern}` | if pattern matches the end of the variable's value, delete the longest part that matches & return the rest
`${variable/pattern/string}` | the longest match to pattern in variable is replaced by string, only the first match is replaced
`${variable//pattern/string}` | the longest match to pattern in variable is replaced by string, all matches are replaced
`${#varname}` | return the length of the value of the variable as a character string
`*(patternlist)` | matches zero or more occurrences of the given patterns
`+(patternlist)` | matches one or more occurrences of the given patterns
`?(patternlist)` | matches zero or one occurrence of the given patterns
`@(patternlist)` | matches exactly one of the given patterns
`!(patternlist)` | matches anything except one of the given patterns
`$(UNIX command)` | command substitution: runs the command & returns standard output

### String quotes

`NAME="John"`
| | |
---|---
`echo "Hi $NAME"` | Hi John
`echo 'Hi $NAME'` | Hi $NAME

### Brace expansion

`echo {A,B}.js`
| | |
---|---
`{A,B}` | Same as `A B`
`{A,B}.js` | Same as `A.js B.js`
`{1..5}` | Same as `1 2 3 4 5`

See: [Brace expansion](http://wiki.bash-hackers.org/syntax/expansion/brace)

## Parameter expansions
----

`name="John"`
| | |
---|---
`echo ${name}` | "John"
`echo ${name/J/j}` | "john" (substitution)
`echo ${name:0:2}` | "Jo" (slicing)
`echo ${name::2}` | "Jo" (slicing)
`echo ${name::-1}` | "Joh" (slicing)
`echo ${name:(-1)}` | "n" (slicing from right)
`echo ${name:(-2):1}` | "h" (slicing from right)
`echo ${food:-Cake}` | $food or "Cake"

`length=2`
| | |
---|---
`echo ${name:0:length}` | "Jo"

See: [Parameter expansion](http://wiki.bash-hackers.org/syntax/pe)

`STR="/path/to/foo.cpp"`
| | |
---|---
`echo ${STR%.cpp}` | /path/to/foo
`echo ${STR%.cpp}.o` | /path/to/foo.o
`echo ${STR##*.}` | cpp (extension)
`echo ${STR##*/}` | foo.cpp (basepath)
`echo ${STR#*/}` | path/to/foo.cpp
`echo ${STR##*/}` | foo.cpp
`echo ${STR/foo/bar}` | /path/to/bar.cpp

`STR="Hello world"`
| | |
---|---
`echo ${STR:6:5}` | "world"
`echo ${STR:-5:5}` | "world"


`SRC="/path/to/foo.cpp"`
| | |
---|---
`BASE=${SRC##*/}` | "foo.cpp" (basepath)
`DIR=${SRC%$BASE}` | "/path/to/" (dirpath)

### Substitution

| | |
--- | ---
`${FOO%suffix}` | Remove suffix
`${FOO#prefix}` | Remove prefix
`${FOO%%suffix}` | Remove long suffix
`${FOO##prefix}` | Remove long prefix
`${FOO/from/to}` | Replace first match
`${FOO//from/to}` | Replace all
`${FOO/%from/to}` | Replace suffix
`${FOO/#from/to}` | Replace prefix
# FUNCTIONS

The function refers to passed arguments by position (as if they were positional parameters), that is, `$1`, `$2`, and so forth.

`$@` is equal to `"$1" "$2"... "$N"`, where 'N' is the number of positional parameters. `$#` holds the number of positional parameters.

```bash
function functname() {
  shell commands
}
```
`unset -f functname` deletes a function definition
`declare -f`  displays all defined functions in your login session

### Example:

```bash
get_name() {
  echo "John"
}

echo "You are $(get_name)"
```

## Arrays
----

### Defining arrays

```bash
Fruits=('Apple' 'Banana' 'Orange')
```

```bash
Fruits[0]="Apple"
Fruits[1]="Banana"
Fruits[2]="Orange"
```

### Working with arrays

| | |
---|---
`echo ${Fruits[0]}` | Element #0
`echo ${Fruits[@]}` | All elements, space-separated
`echo ${#Fruits[@]}` | Number of elements
`echo ${#Fruits}` | String length of the 1st element
`echo ${#Fruits[3]}` | String length of the Nth element
`echo ${Fruits[@]:3:2}` | Range (from position 3, length 2)

### Operations

| | |
---|---
`Fruits=("${Fruits[@]}" "Watermelon")` | Push
`Fruits+=('Watermelon')` | Also Push
`Fruits=( ${Fruits[@]/Ap*/} )` | Remove by regex match
`unset Fruits[2]` | Remove one item
`Fruits=("${Fruits[@]}")` | Duplicate
`Fruits=("${Fruits[@]}""${Veggies[@]}")` | Concatenate
```lines=(`cat "logfile"`)``` | Read from file

### Iteration

```bash
for i in "${arrayName[@]}"; do
  echo $i
done
```

## Dictionaries
----

### Defining

```bash
declare -A sounds
```

```bash
sounds[dog]="bark"
sounds[cow]="moo"
sounds[bird]="tweet"
sounds[wolf]="howl"
```

Declares `sound` as a Dictionary object (aka associative array).

### Working with dictionaries

| | |
---|---
`echo ${sounds[dog]}` | Dog's sound
`echo ${sounds[@]}` | All values
`echo ${!sounds[@]}` | All keys
`echo ${#sounds[@]}` | Number of elements
`unset sounds[dog]` | Delete dog

### Iteration

#### Iterate over values

```bash
for val in "${sounds[@]}"; do
  echo $val
done
```

#### Iterate over keys

```bash
for key in "${!sounds[@]}"; do
  echo $key
done
```

## Functions
----

### Defining functions

```bash
myfunc() {
    echo "hello $1"
}
```

```bash
# Same as above (alternate syntax)
function myfunc() {
    echo "hello $1"
}
```

```bash
myfunc "John"
```

### Returning values

```bash
myfunc() {
    local myresult='some value'
    echo $myresult
}
```

```bash
result="$(myfunc)"
```

### Raising errors

```bash
myfunc() {
  return 1
}
```

```bash
if myfunc; then
  echo "success"
else
  echo "failure"
fi
```

# FLOW CONTROLS

| | |
---|---
`statement1 && statement2` | 'and' operator
`statement1 || statement2` | 'or' operator
`-a` | 'and' operator inside a test conditional expression
`-o` | 'or' operator inside a test conditional expression

# STRINGS

| | |
---|---
`str1 == str2` | str1 matches str2
`str1 != str2` | str1 does not match str2
`str1 < str2` | str1 is less than str2 (alphabetically)
`str1 > str2` | str1 is greater than str2 (alphabetically)
`str1 \> str2` | str1 is sorted after str2
`str1 \< str2` | str1 is sorted before str2
`-n str1` | str1 is not null (has length greater than 0)
`-z str1` | str1 is null (has length 0)

### Substrings

| | |
---|---
`${FOO:0:3}`  | Substring _(position, length)_
`${FOO:-3:3}` | Substring from the right

### Length

| | |
---|---
| `${#FOO}` | Length of `$FOO` |

### Manipulation

`STR="HELLO WORLD!"`
| | |
---|---
`echo ${STR,}` | "hELLO WORLD!" (lowercase 1st letter)
`echo ${STR,,}` | "hello world!" (all lowercase)

`STR="hello world!"`
| | |
---|---
`echo ${STR^}` | "Hello world!" (uppercase 1st letter)
`echo ${STR^^}` | "HELLO WORLD!" (all uppercase)

### Default values

| | |
---|---
`${FOO:-val}` | `$FOO`, or `val` if not set
`${FOO:=val}` | Set `$FOO` to `val` if not set
`${FOO:+val}` | `val` if `$FOO` is set
`${FOO:?message}` | Show error message & exit if `$FOO` is not set

The `:` is optional.
eg; `${FOO=word}` works too.

# FILES

| | |
---|---
`-a file` | file exists or its compilation is successful
`-d file` | file exists & is a directory
`-e file`  |  file exists; similar to `-a`
`-f file`  | file exists & is a regular file (i.e. not a directory or other special type of file)
`-r file`  | you have read permission
`-s file` | file exists & is not empty
`-w file`  | you have write permission
`-x file` | you have execute permission on *file* or search perms if a *directory*
`-N file` | file was modified since it was last read
`-O file`  |  you own file
`-G file` | file's group ID matches yours (or one of yours, if you are in multiple groups)
`file1 -nt file2` | file1 is newer than file2
`file1 -ot file2` | file1 is older than file2

# NUMBERS

| | |
---|---
`-lt`  |  less than
`-le`  | less than or equal
`-eq`  |  equal
`-ge`  |  greater than or equal
`-gt` | greater than
`-ne` | not equal

### Conditionals

### Conditions

Note that `[[` is actually a command/program that returns either `0` (true) or `1` (false). Any program that obeys the same logic (like all base utils, such as `grep(1)` or `ping(1)`) can be used as condition, see examples.

| | |
---|---
`[[ -z STRING ]]` | Empty string          
`[[ -n STRING ]]` | Not empty string
`[[ STRING == STRING ]]` | Equal                
`[[ STRING != STRING ]]` | Not Equal                
`[[ NUM -eq NUM ]]` | Equal                 
`[[ NUM -ne NUM ]]` | Not equal
`[[ NUM -lt NUM ]]` | Less than
`[[ NUM -le NUM ]]` | Less than or equal
`[[ NUM -gt NUM ]]` | Greater than          
`[[ NUM -ge NUM ]]` | Greater than or equal               
`[[ STRING =~ STRING ]]` | Regexp                
`(( NUM < NUM ))` | Numeric conditions      
`[[ -o noclobber ]]` | If OPTIONNAME is enabled                  
`[[ ! EXPR ]]` | Not                      
`[[ X ]] && [[ Y ]]` | And                      
`[[ X ]] || [[ Y ]]` | Or                       

### File conditions

| | |
---|---
`[[ -e FILE ]]` | Exists                 
`[[ -r FILE ]]` | Readable               
`[[ -h FILE ]]` | Symlink                
`[[ -d FILE ]]` | Directory              
`[[ -w FILE ]]` | Writable               
`[[ -s FILE ]]` | Size is > 0 bytes      
`[[ -f FILE ]]` | File                    
`[[ -x FILE ]]` | Executable          
`[[ FILE1 -nt FILE2 ]]` | 1 is more recent than 2
`[[ FILE1 -ot FILE2 ]]` | 2 is more recent than 1
`[[ FILE1 -ef FILE2 ]]` | Same files

### Example

```bash
# String
if [[ -z "$string" ]]; then
  echo "String is empty"
elif [[ -n "$string" ]]; then
  echo "String is not empty"
fi
```

```bash
# Combinations
if [[ X ]] && [[ Y ]]; then
  ...
fi
```

```bash
# Equal
if [[ "$A" == "$B" ]]
```

```bash
# Regex
if [[ "A" =~ . ]]
```

```bash
if (( $a < $b )); then
   echo "$a is smaller than $b"
fi
```

```bash
if [[ -e "file.txt" ]]; then
  echo "file exists"
fi
```

```bash
if [[ -z "$string" ]]; then
  echo "String is empty"
elif [[ -n "$string" ]]; then
  echo "String is not empty"
fi
```

```bash
if condition
then
  statements
[elif condition
  then statements...]
[else
  statements]
fi

for x in {1..10}
do
  statements
done

for name [in list]
do
  statements that can use $name
done

for (( initialisation ; ending condition ; update ))
do
  statements...
done

case expression in
  pattern1 )
    statements ;;
  pattern2 )
    statements ;;
esac

select name [in list]
do
  statements that can use $name
done

while condition; do
  statements
done

until condition; do
  statements
done
```

## Loops

### Basic for loop

```bash
for i in /etc/rc.*; do
  echo $i
done
```

### C-like for loop

```bash
for ((i = 0 ; i < 100 ; i++)); do
  echo $i
done
```

### Ranges

```bash
for i in {1..5}; do
    echo "Welcome $i"
done
```

#### With step size

```bash
for i in {5..50..5}; do
    echo "Welcome $i"
done
```

### Reading lines

```bash
cat file.txt | while read line; do
  echo $line
done
```

### Forever

```bash
while true; do
  ···
done
```

### Conditional execution

`git commit && git push` and
`git commit || echo "Commit failed"` or

### Arguments

| | |
---|---                                                 
`$#` | Number of arguments                    
`$*` | All arguments                          
`$@` | All arguments, starting from first
`$1` | First argument                         
`$_` | Last argument of the previous command

See [Special parameters](http://wiki.bash-hackers.org/syntax/shellvars#special_parameters_and_shell_variables).

# COMMAND-LINE PROCESSING CYCLE

The default order for command lookup is:
Functions > Built-ins > Scripts > executables.

There are three built-ins that you can use to override this order: `command`, `builtin` and `enable`.

| | |
---|---
`command` | remove alias & function lookup. Only built-ins & commands found in the search path are executed
`builtin` | look up only built-in commands, ignoring functions & commands found in PATH
`enable` |  enable / disable shell built-ins
`eval`  | take arguments & run them through the command-line processing steps all over again

# INPUT/OUTPUT REDIRECTORS

| | |
---|---
`cmd1|cmd2` | pipe; take standard output of cmd1 as standard input to cmd2
`< file` | take standard input from file
`> file` | direct standard output to file
`>> file` | direct standard output to file; append to file if it exists
`>|file` | force standard output to file even if noclobber is set
`n>|file` | force output to file from file descriptor n even if noclobber is set
`<> file` | use file as both standard input & standard output
`n<>file` | use file as both input & output for file descriptor n
`n>file` | direct file descriptor n to file
`n<file` | take file descriptor n from file
`n>>file` | direct file description n to file; append to file if it exists
`n>&` | duplicate standard output to file descriptor n
`n<&` | duplicate standard input from file descriptor n
`n>&m` | file descriptor n is made to be a copy of the output file descriptor
`n<&m` | file descriptor n is made to be a copy of the input file descriptor
`&>file` | direct standard output & standard error to file
`<&-` | close the standard input
`>&-` | close the standard output
`n>&-` | close the ouput from file descriptor n
`n<&-` | close the input from file descripor n

examples:
`python hello.py > output.txt` stdout to (file)
`python hello.py >> output.txt` stdout to (file), append
`python hello.py 2> error.log` stderr to (file)
`python hello.py 2>&1` stderr to stdout
`python hello.py 2>/dev/null` stderr to (null)
`python hello.py &>/dev/null` stdout and stderr to (null)
`python hello.py < foo.txt`  feed foo.txt to stdin for python

# PROCESS HANDLING

| | |
---|---
`CTRL+C` | Interrupt a job
`CTRL+Z` | Suspend job while it is running
`CTRL+Y` | Also suspend a job, its slightly different from `CTRL+Z`, the process is only stopped when it attempts to read input from terminal.

| | |
---|---
`myCommand &` | run job in the background & prompts back the shell
`jobs` | list all jobs (use with `-l` to see associated PID)
`fg` | bring a background job into the foreground
`fg %+` | bring most recently invoked background job
`fg %-` | bring second most recently invoked background job
`fg %N`  | bring job number N
`fg %string` | bring job whose command begins with string
`fg %?string` | bring job whose command contains string
`kill -l` | a list of all signals on the system, by name and number
`kill PID` | terminate process with specified PID
`kill -s SIGKILL 4500` | send a signal to force or terminate the process
`kill -15 913` | Ending PID 913 process with signal 15 (TERM)
`ps` | print a line of information about the current running login shell & any processes running under it
`ps -a` | select all processes with a tty except session leaders
`trap cmd sig1 sig2` | execute a command when a signal is received by the script
`trap "" sig1 sig2` | ignores that signals
`trap - sig1 sig2` | reset the action taken when the signal is received to the default
`disown <PID|JID>` | remove the process from the list of jobs
`wait` | wait until all background jobs have finished

# TIPS & TRICKS

Set an alias:
```bash
cd; nano .bash_profile
> alias gentlenode='ssh admin@gentlenode.com -p 3404'  # add your alias in .bash_profile
```

Go quickly to a specific directory:
```bash
cd; nano .bashrc
> shopt -s cdable_vars
> export websites="/Users/mac/Documents/websites"

source .bashrc
cd $websites
```

# DEBUGGING SHELL PROGRAMS

| | |
---|---
`bash -n scriptname` | don't run commands; check for syntax errors only
`set -o noexec` | alternative (set option in script)
`bash -v scriptname` | echo commands before running them
`set -o verbose` | alternative (set option in script)
`bash -x scriptname` | echo commands after command-line processing
`set -o xtrace` | alternative (set option in script)
`trap 'echo $varname' EXIT` | useful when you want to print out the values of variables at the point that your script exits

### Trap errors

`trap 'echo Error at about $LINENO' ERR`

or:

```bash
traperr() {
  echo "ERROR: ${BASH_SOURCE[1]} at about ${BASH_LINENO[0]}"
}

set -o errtrace
trap traperr ERR
```

or using a function:

```bash
function errtrap {
  es=$?
  echo "ERROR line $1: Command exited with status $es."
}
```
`trap 'errtrap $LINENO' ERR` Is run whenever a command in the surrounding script or function exits with non-zero status
```bash
function dbgtrap {
  echo "badvar is $badvar"
}
```
`trap dbgtrap DEBUG` causes the trap code to be executed before every statement in a function or script
...section of code in which the problem occurs...
`trap - DEBUG` turn off the DEBUG trap
```bash
function returntrap {
  echo "A return occurred"
}
```
`trap returntrap RETURN` is executed each time a shell function or a script executed with the . or source commands finishes executing

# COLORS AND BACKGROUNDS 

`Color_Off='\033[0m'` Text Reset

Regular Text Colours:
| code | colour |
---|---
`Black='\033[0;30m'` | Black
`Red='\033[0;31m'` | Red
`Green='\033[0;32m'` | Green
`Yellow='\033[0;33m'` | Yellow
`Blue='\033[0;34m'` | Blue
`Purple='\033[0;35m'` | Purple
`Cyan='\033[0;36m'` | Cyan
`White='\033[0;97m'` | White
`LGrey='\033[0;37m'` | Light Gray
`DGrey='\033[0;90m'` | Dark Gray
`LRed='\033[0;91m'` | Light Red
`LGreen='\033[0;92m'` | Light Green
`LYellow='\033[0;93m'` | Light Yellow
`LBlue='\033[0;94m'` | Light Blue
`LPurple='\033[0;95m'` | Light Purple
`LCyan='\033[0;96m'` | Light Cyan

Bold:
| code | colour |
---|---
`BBlack='\033[1;30m'` | Black
`BRed='\033[1;31m'` | Red
`BGreen='\033[1;32m'` | Green
`BYellow='\033[1;33m'`| Yellow
`BBlue='\033[1;34m'` | Blue
`BPurple='\033[1;35m'` | Purple
`BCyan='\033[1;36m'` | Cyan
`BWhite='\033[1;37m'` | White

Underline:
| code | colour |
---|---
`UBlack='\033[4;30m'` | Black
`URed='\033[4;31m'` | Red
`UGreen='\033[4;32m'` | Green
`UYellow='\033[4;33m'` | Yellow
`UBlue='\033[4;34m'` | Blue
`UPurple='\033[4;35m'` | Purple
`UCyan='\033[4;36m'` | Cyan
`UWhite='\033[4;37m'` | White

Background:
| code | colour |
---|---
`On_Black='\033[40m'` | Black
`On_Red='\033[41m'` | Red
`On_Green='\033[42m'` | Green
`On_Yellow='\033[43m'` | Yellow
`On_Blue='\033[44m'` | Blue
`On_Purple='\033[45m'` | Purple
`On_Cyan='\033[46m'` | Cyan
`On_White='\033[47m'` | White

# Example of usage
```bash
echo -e "${Green}This is GREEN text${Color_Off} and normal text"`
```
```bash
echo -e "${Red}${On_White}This is Red test on White background${Color_Off}"` 
```
option `-s` is mandatory, it enable interpretation of backslash escapes.

### Strict mode

```bash
set -euo pipefail
IFS=$'\n\t'
```

See: [Unofficial bash strict mode](http://redsymbol.net/articles/unofficial-bash-strict-mode/)

### Comments

`# Single line comment`

```bash
: '
This is a
multi line
comment
'
```

## Options
----

### Options

| | |
---|---
`set -o noclobber` | Avoid overlay files (echo "hi" > foo)
`set -o errexit` | Used to exit upon error, avoiding cascading errors
`set -o pipefail` | Shows hidden failures
`set -o nounset` | Shows unset variables

### Glob options

| | |
---|---
`shopt -s nullglob` | Non-matching globs are removed  ('*.foo' => '')
`shopt -s failglob` | Non-matching globs throw errors
`shopt -s nocaseglob` | Case insensitive globs
`shopt -s dotglob` | Wildcards match dotfiles ("*.sh" => ".foo.sh")
`shopt -s globstar` | Allow ** for recursive matches ('lib/**/*.rb' => 'lib/a/b/c.rb')

Set `GLOBIGNORE` as a colon-separated list of patterns to be removed from glob
matches.

## History
----

### Commands

| | |
---|---
`history` | Show history
`shopt -s histverify` | Don't execute expanded result immediately

### Expansions

| | |
---|---
`!$` | Expand last parameter of most recent command
`!*` | Expand all parameters of most recent command
`!-n` | Expand `n`th most recent command
`!n` | Expand `n`th command in history
`!<command>` | Expand most recent invocation of command `<command>`

### Slices

| | |
---|---
`!!:n` | Expand only `n`th token from most recent command (command is `0`; first argument is `1`)
`!^` | Expand first argument from most recent command
`!$` | Expand last token from most recent command
`!!:n-m` | Expand range of tokens from most recent command
`!!:n-$` | Expand `n`th token to last from most recent command

`!!` can be replaced with any valid expansion i.e. `!cat`, `!-2`, `!42`, etc.

### Operations

| | |
---|---
`!!` | Execute last command again        
`!!:s/<FROM>/<TO>/` | Replace first occurrence of `<FROM>` to `<TO>` in most recent command
`!!:gs/<FROM>/<TO>/` | Replace all occurrences of `<FROM>` to `<TO>` in most recent command
`!$:t` | Expand only basename from last parameter of most recent command
`!$:h` | Expand only directory from last parameter of most recent command

`!!` and `!$` can be replaced with any valid expansion.

## Miscellaneous
----

### Numeric calculations

`$((a + 200))` Add 200 to $a
`$((RANDOM%=200))` Random number 0..200


### Subshells

`(cd somedir; echo "I'm now in $PWD")`
`pwd` still in first directory

### Inspecting commands

`command -V cd` "cd is a function/alias/whatever"

### Case/switch

```bash
case "$1" in
  start | up)
    vagrant up
    ;;

  *)
    echo "Usage: $0 {start|stop|ssh}"
    ;;
esac
```

### Source relative

```bash
source "${0%/*}/../share/foo.sh"
```

### printf
| | |
---|---
`printf "Hello %s, I'm %s" Sven Olga` | "Hello Sven, I'm Olga"
`printf "1 + 1 = %d" 2` | "1 + 1 = 2"
`printf "This is how you print a float: %f" 2` | "This is how you print a float: 2.000000"

### Directory of script

```bash
DIR="${0%/*}"
```

### Getting options

```bash
while [[ "$1" =~ ^- && ! "$1" == "--" ]]; do case $1 in
  -V | --version )
    echo $version
    exit
    ;;
  -s | --string )
    shift; string=$1
    ;;
  -f | --flag )
    flag=1
    ;;
esac; shift; done
if [[ "$1" == '--' ]]; then shift; fi
```

### Reading input

```bash
echo -n "Proceed? [y/n]: "
read ans
echo $ans
```

`read -n 1 ans` Just one character

### Special variables

| | |
---|---
`$?` | Exit status of last task
`$!` | PID of last background task
`$$` | PID of shell
`$0` | Filename of the shell script

See [Special parameters](http://wiki.bash-hackers.org/syntax/shellvars#special_parameters_and_shell_variables).

### Check for command's result

```bash
if ping -c 1 google.com; then
  echo "It appears you have a working internet connection"
fi
```

### Grep check

```bash
if grep -q 'foo' ~/.bash_history; then
  echo "You appear to have typed 'foo' in the past"
fi
```

### Heredoc

```sh
cat <<END
hello world
END
```

## Also see:

* [Bash-hackers wiki](http://wiki.bash-hackers.org/) _(bash-hackers.org)_
* [Shell vars](http://wiki.bash-hackers.org/syntax/shellvars) _(bash-hackers.org)_
* [Learn bash in y minutes](https://learnxinyminutes.com/docs/bash/) _(learnxinyminutes.com)_
* [Bash Guide](http://mywiki.wooledge.org/BashGuide) _(mywiki.wooledge.org)_
* [ShellCheck](https://www.shellcheck.net/) _(shellcheck.net)_
