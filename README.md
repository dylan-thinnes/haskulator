# haskulator
A quick haskell scratchpad bash script, useful for calculations but extensible to quite a bit more.

## What It Does
The haskulator is very simple. You give it a single line command to execute, and it will compile and run that for you in Haskell.
By using Haskell's rather powerful type system, we have access to a wide range of useful number types and operations for them.
By using Haskell's rather terse yet visually obvious nature, it is easy to compose calculations while maintaining readability.
You can define dependencies off which to work.

## Installation
haskulator is a bash script which is dependent on runghc, which should come installed with the Glasgow Haskell Compiler.
Clone or download the file named "haskulator" from this repository.
Place it somewhere on your PATH and make sure it is executable.

## Usage
```
$ haskulate <computation> [<dependency files>...]
```
The first argument defines the computation to be made.
All future arguments define files to concatenate before the computation, which simplifies pulling in dependencies or pre-defined functions. File concatenation is done using `cat` under the hood, so the `-` stdin option is available to this program.

This allows you to define simple initial computations such as `a` to be evaluated and then define the meat of your calculation in other files.

## Simple Example
```sh
$ haskulate 100+100
200
$ haskulate 2^1000
1071508607186267320948425049060001810561404811705
5336074437503883703510511249361224931983788156958
5812759467291755314682518714528569231404359845775
7469857480393456777482423098542107460506237114187
7954182153046474983581941267398767559165543946077
0629145711964776865421676604298316526243868372056
68069376
```

## Defining Dependencies
By creating dependency files, common functions and imported libraries can be easily used.
**`/home/me/utils.hs`**
```haskell
-- definition of the fibonacci sequence
f=1:scanl(+)1f
```
After defining your files, you require them as follows:
```sh
$ haskulate "take 10 f" ~/utils.hs
[1,1,2,3,5,8,13,21,34,55]
```
The files are concatenated before one another in order.
The definition of the first file passed is concatenated, then the definition of the second, etc, then the last is the first argument passed.

## Using Standard Input
```sh
$ haskulate a - # "-" options listens for input which will be added before the final evaluation
a=100+b
b=2
^D
```
Behind the scenes, the following program is produced
```haskell
a=100+b
b=2
main = putStrLn . show $ a
```
This program is then piped to `runghc` to produce the output `102`

A more realistic yet still simple use case for this would be as follows, 
to compute the sum of the first 100 elements of a geometric progression.
```sh
$ haskulate "sum shortL" -
l = iterate (*(2/3)) 1
shortL = take 100 l
```
