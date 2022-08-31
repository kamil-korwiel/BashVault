# Brackets
---

## ( Single Parentheses )

Single parenthesis will run the commands inside in a **subshell**. This means that they run through all of the **commands inside**, and then return a **single exit code**. Any **variables** declared or **environment changes** will get **cleaned up and disappeared**. Because it's within a subshell, if you have it inside a **loop**, it will run a **little slower** than if you called the commands _without_ the parentheses.  

``` shell
a='This string'
( a=banana; mkdir $a )
echo $a
# => 'This string'
ls
# => ...
# => banana/
```


## (( Double Parentheses ))

This is for use in integer arithmetic. You can perform assignments, logical operations, and mathematic operations like multiplication or modulo inside these parentheses. However, do note that **there is no output**. Any **variable changes** that happen inside them will stick, but **don't expect to be able to assign the result to anything**.
_If the result inside is **non-zero**, it returns a **zero** (success) exit code. If the result inside is **zero**, it returns an exit code of **1**. _

```shell
i=4
(( i += 3 ))
echo $i
# => 7
(( 4 + 8 ))
# => No Output
echo $?  # Check the exit code of the last command
# => 0
(( 5 - 5 ))
echo $?
# => 1

# Strings inside get considered 'zero'.
(( i += POO ))
echo $i
# => 7

# You can't use it in an expression
a=(( 4 + 1 ))
# => bash: syntax error near unexpected token '('
```

## <( Angle Parentheses )
This is known as a _process substitution_. It's a lot like a pipe, except you can use it anywhere a command expects a file argument. And you can use multiple at once!  

```shell
sort -nr -k 5 <( ls -l /bin ) <( ls -l /usr/bin ) <( ls -l /sbin )

# => Like a billion lines of output that contain many of the
# => executables on your computer, sorted in order of descending size.

# Just in case you don't magically remember all bash flags,
# -nr  means sort numerically in reverse (descending) order
# -k 5 means sort by Kolumn 5.  In this case, for `ls -l`, that is the "file size"
```

This works because the sort command expects one or many filenames as arguments. Behind the scenes, the `<( stuff )` actually outputs the name of a temporary file (unnamed pipe file) for the `sort` command to use.

Another example of where this comes in handy is the use of the `comm` command, which spits out the lines that the files have in common. Because `comm` needs its input files to be sorted, you could either do this:

```shell
# The lame way
sort file1 > file1.sorted
sort file2 > file2.sorted
comm -12 file1.sorted file2.sorted
```
Ooooor, you can be a total BAshMF and do it this way:  

``` shell
# The baller way
comm -12 <( sort file1 ) <( sort file2 )
```

## $( Dollar Single Parentheses )

This is for interpolating a subshell command output into a string. The command inside gets run inside a subshell, and then any output gets placed into whatever string you're building.  

``` Shell
intro="My name is $( whoami )"
echo $intro
# => My name is ryan

# And just to prove that it's a subshell...
a=5
b=$( a=1000; echo $a )
echo $b
# => 1000
echo $a
# => 5
```

## \$( Dollar Single Parentheses Dollar Q )\$?

If you want to interpolate a command, but only the exit code and not the value, this is what you use.

When you care about how a command exited but not its output
```shell
if [[ $( grep -q PATTERN FILE )$? ]]
then
  echo "Dat pattern was totally in dat file!"
else
  echo "NOPE."
fi
```

Although, really, this isn't so much a special bracket pattern as it is an interesting use of `$?`, since the above works even if there is a space between the `$( stuff )` and the `$?`. But a neat tip, nonetheless.

## ($( Single Parentheses Dollar Single Parentheses )) 
which takes the output from `COMMAND` and creates an array-variable from it.
```shell
VAR=($( ls ))
```


## \[\[ Double Square Brackets \]\]

True/false testing. Read through the section above for an explanation of the differences between single and double square brackets. Additionally, double square brackets support extended regular expression matching. Use quotes around the second argument to force a raw match instead of a regex match.  

```shell
pie=good
[[ $pie =~ d ]]; echo $?
# => 0, it matches the regex!

[[ $pie =~ [aeiou]d ]]; echo $?
# => 0, still matches

[[ $pie =~ [aei]d ]]; echo $?
# => 1, no match

[[ $pie =~ "[aeiou]d" ]]; echo $?
# => 1, no match because there's no literal '[aeoiu]d' inside the word "good"
```

Also, inside double square brackets, `<` and `>` sort by your locale. Inside single square brackets, it's by your machine's sorting order, which is usually ASCII.

## { Single Curly Braces }

Single curly braces are used for expansion.  

```shell
echo h{a,e,i,o,u}p
# => hap hep hip hop hup
echo "I am "{cool,great,awesome}
# => I am cool I am great I am awesome

mv friends.txt{,.bak}
# => braces are expanded first, so the command is `mv friends.txt friends.txt.bak`
```

The cool thing is that you can make ranges as well! With leading zeros!

```shell
echo {01..10}
# => 01 02 03 04 05 06 07 08 09 10
echo {01..10..3}
# => 01 04 07 10
```


## ${dollar braces}

Note that there are no spaces around the contents. This is for variable interpolation. You use it when normal string interpolation could get weird  

```shell
# I want to say 'bananaification'
fruit=banana
echo $fruitification
# => "" No output, because $fruitification is not a variable.
echo ${fruit}ification
# => bananaification
```

The other thing you can use **${Dollar Braces}** for is variable manipulation. Here are a few common uses.

Using a default value if the variable isn't defined.

```shell
function hello() {
  echo "Hello, ${1:-World}!"
}
hello Kamil
# => Hello Kamil!
hello
# => Hello World!

function sign_in() {
    name=$1
  echo "Signing in as ${name:-$( whoami )}"
}
sign_in
# => Signing in as kamil
sign_in coolguy
# => Signing in as coolguy
```

Getting the length of a variable.  

```shell
name="Ryan"
echo "Your name is ${#name} letters long!"
# => Your name is 4 letters long!
```

Chopping off pieces that match a pattern.  

```shell
url=https://assertnotmagic.com/about
echo ${url#*/}     # Remove from the front, matching the pattern */, non-greedy
# => /assertnotmagic.com/about
echo ${url##*/}    # Same, but greedy
# => about
echo ${url%/*}     # Remove from the back, matching the pattern /*, non-greedy
# => https://assertnotmagic.com
echo ${url%%/*}    # Same, but greedy
# => https:
```

You can uppercase matching letters!  

```shell
echo ${url^^a}
# => https://AssertnotmAgic.com/About
```

You can get slices of strings.  

```shell
echo ${url:2:5}  # the pattern is ${var:start:len}.  Start is zero-based.
# => tps://
```

You can replace patterns.  

```shell
echo ${url/https/ftp}
# => ftp://assertnotmagic.com

# Use a double slash for the first slash to do a global replace
echo ${url//[aeiou]/X}
# => https://XssXrtnXtmXgXc.cXm
```

And, you can use variables indirectly _as the name of other variables_.  

```shell
function grades() {
  name=$1
  alice=A
  beth=B
  charles=C
  doofus=D
  echo ${!name}
}

grades alice
# => A
grades doofus
# => D
grades "Valoth the Unforgiving"
# => bash: : bad substitution.   
# There is no variable called Valoth the Unforgiving,
# so it defaults to a blank value.  
# Then, bash looks for a variable with a name of "" and errors out.
```

## \<\<Double Angle Heredocs

This is how you make multiline strings in Bash (one method). Two arrows and then a word -- any word that you choose -- to signal the start of the string. The string doesn't end until you repeat your magic word.  

``` shell
read -r -d '' nice_message <<MESSAGE
Hi there!  I really like the way you look
when you are teaching newbies things
with empathy and compassion!
You rock!
MESSAGE

echo "$nice_message"
# => Hi there!  I really like the way you look
# => when you are teaching newbies things
# => with empathy and compassion!
# => You rock!

# Note, if you store a string with newlines in a variable, 
# you have to quote it to get the newlines to actually print.
# If you just use `echo $nice_message`, it won't reflect newlines.
```