# Notes: Eines Informàtiques

Python blocks are code cells; bash, Fortran, gnuplot and make blocks are text cells (copy them into a terminal or file). Templates read files that may not exist here, so don't use *Run all*.

# Contents

## 0.1 Method `[METHOD]`

**For every question:**

1.  **Classify it** (≈ 30 s): which template is it? Use the "question says → you type" table in each section.
2.  **Copy the template** (Copy button on each code block). Don't start from a blank file.
3.  **Adapt it:** names, formula, range, file names, formats. Change one thing, run, then change the next.
4.  **Verify it** (≈ 30 s): row count, a value you can check by hand, the output file exists, the "prove it" line.
5.  Move on. Come back later to polish if there's time.

**Time:** split the time across questions in proportion to their points, and do the questions you're surest about first. Leave about 10 minutes at the end to re-run everything from clean (`make clean && make`, re-run the notebook from the top) and check what you'll hand in.

**Feeding input without typing:** `bash s.sh <<< "answer"` · `printf "a\nb\n" | bash s.sh` · `echo 10 | ./prog`. (Colab: `%%bash` = shell cell, `%%writefile name` = create a file, `!cmd` = one shell command.)

# 1. Linux and bash `[BASH]`

## 1.1 Command anatomy, paths, globs

`command -flags arguments` · flags can be combined (`-la`) · help: `cmd --help` (quick), `man cmd` (`q` quits)

| Symbol           | Meaning                                                                                 |
|------------------|-----------------------------------------------------------------------------------------|
| `.` / `..` / `~` | current directory / parent directory / home directory (`$HOME`)                         |
| `*`              | any characters (`*.f90`) · `?` exactly one character · `*.{txt,doc}` several extensions |
| `./prog`         | run the program in this folder                                                          |

## 1.2 Everyday commands

| Command                                                                   | Meaning                                                                                                                                                        |
|---------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ls -l` `-a` `-t` `-S` `-r` `-h` `-d`                                     | long format · include hidden · sort by time (newest first) · by size (largest first) · reverse · human-readable sizes · the directory itself, not its contents |
| `cat f` · `cat -n f`                                                      | print · with line numbers                                                                                                                                      |
| `head -n 7 f` · `tail -n 5 f`                                             | first 7 / last 5 lines                                                                                                                                         |
| `wc -l f` / `-w` / `-c` · `wc -l < f`                                     | lines/words/bytes · **just the number** (use this in scripts)                                                                                                  |
| `cut -c 10-15 f` · `cut -d',' -f2 f`                                      | characters 10–15 of each line · 2nd comma-separated field                                                                                                      |
| `sort` · `sort -n -k3` · `sort -r` · `uniq`                               | sort · numerically on column 3 · reversed · remove adjacent duplicate lines                                                                                    |
| `cp a b` · `cp *.f90 dir/` · `mv a b` · `rm f` · `rm -r d` · `mkdir -p d` | copy · copy into a directory · move/rename · delete · delete a directory · make a directory (no error if it exists)                                            |
| `ln -s target link`                                                       | symbolic link (it breaks if the target is deleted: `No such file or directory`)                                                                                |
| `diff f1 f2`                                                              | `3c3` = line 3 changed; `<` lines are from f1, `>` from f2                                                                                                     |
| `chmod 660 f` · `chmod u+x f`                                             | permissions (see below) · make it executable                                                                                                                   |
| `history | grep ssh`                                                      | earlier commands containing ssh                                                                                                                                |
| `scp file user@host:dir/new` · `scp user@host:path .`                     | copy to / from a remote machine                                                                                                                                |
| `find . -name "*.f90"`                                                    | find files recursively                                                                                                                                         |
| `echo "text"` · `printf "a\nb\n"`                                         | print text                                                                                                                                                     |
| `tr -d ' '` · `tr 'a-z' 'A-Z'`                                            | delete characters · translate characters                                                                                                                       |

**chmod digits:** r=4, w=2, x=1, summed for **u**ser, **g**roup, **o**thers. `660` = rw-rw---- · `644` = rw-r--r-- · `700` = rwx------ · `755` = rwxr-xr-x

## 1.3 Redirection and pipes

`>` into a file (overwrite) · `>>` append · `<` from a file · `|` pipe · `2>/dev/null` hide errors · `&&` run next if this succeeded · `||` run next if this failed  
**A multi-line file:**

```bash
cat > poem.txt << 'END'
I went down to the river,
I set down on the bank.
END
```

## 1.4 grep: matching lines

| Flag        | Meaning                                                                   |
|-------------|---------------------------------------------------------------------------|
| `-v`        | lines that do **not** match                                               |
| `-c`        | count matching **lines**                                                  |
| `-o`        | each match on its own line → `grep -o X f | wc -l` counts **occurrences** |
| `-w`        | whole word only (`for` ≠ `format`)                                        |
| `-i`        | ignore case                                                               |
| `-n`        | line numbers                                                              |
| `-l` / `-L` | names of files that match / don't match                                   |
| `-q`        | silent (just success or failure, for use in `if`)                         |
| `-r`        | recursive                                                                 |
| `-E`        | extended patterns: `-E 'a|b'` · `-E '\.(txt|doc|odt)$'`                   |

Patterns (in **single quotes**): `^I` starts with I · `x$` ends with x · `.` any character · `\.` a literal dot · `*` the previous character repeated

## 1.5 sed: print / delete / substitute

| Command                                                                   | Effect                                                                    |
|---------------------------------------------------------------------------|---------------------------------------------------------------------------|
| `sed -n '3p' f` · `sed -n '3,7p' f` · `sed -n '$p' f` · `sed -n '4,$p' f` | line 3 · lines 3–7 · last line · line 4 to the end                        |
| `sed -n '/pat/p' f`                                                       | lines matching a pattern                                                  |
| `sed '1d' f` · `sed '1d;$d' f` · `sed '/^#/d' f`                          | without line 1 · without the first and last lines · without comment lines |
| `sed 's/a/b/' f` · `sed 's/a/b/g' f`                                      | replace the first match per line · replace all matches                    |
| `sed -i 's/a/b/g' f`                                                      | edit the file in place (Mac: `sed -i ''`)                                 |
| `sed -n "${n}p" f`                                                        | a shell variable inside sed → **double** quotes                           |

**Reorder the lines of a file** (temp file + `mv`, because `> f` would empty f before sed reads it):

```bash
{ sed -n '1p' f; sed -n '3p' f; sed -n '2p' f; sed -n '4,$p' f; } > tmp && mv tmp f
```

## 1.6 awk: columns

`awk '{print $1, $3}' f` · `awk 'NR==3' f` · `awk 'NR>1 && $4>80 {print $1}' f` · `awk '{s+=$2} END {print s}' f` · `awk '$1 == 20 {print $2}'` · `$NF` = last field · `NR` = line number

## 1.7 Variables, quotes, arithmetic

```bash
name="x"                 # NO spaces around =
echo "$name"             # double quotes expand variables; 'single' quotes don't
n=$(wc -l < "$f")        # capture a command's output
k=$((n + 1))             # integer arithmetic
dir=${1:-.}              # first argument, or "." if none was given
```

`$1 $2` = arguments · `$#` = number of arguments · `exit 1` = stop with an error

## 1.8 Scripts: skeleton, tests, if, loops

```bash
#!/bin/bash                       # shebang: MUST be line 1
# name.sh - what it does
read -p "File name: " f           # ask the user
if [ -f "$f" ]; then              # spaces inside [ ] are REQUIRED
    echo "$f: $(wc -l < "$f") lines"
elif [ -d "$f" ]; then
    echo "$f is a directory"
else
    echo "$f does not exist"
fi
```

Run it with `bash s.sh`, or `chmod +x s.sh` once and then `./s.sh`. Debug with `bash -x s.sh`.

| Test             | True if        |     | Test                      | True if                                     |
|------------------|----------------|-----|---------------------------|---------------------------------------------|
| `-f "$x"`        | a regular file |     | `"$a" -eq "$b"`           | integers equal (also `-ne -lt -le -gt -ge`) |
| `-d "$x"`        | a directory    |     | `"$s" = "$t"` / `!=`      | strings equal / different                   |
| `-e "$x"`        | exists         |     | `-z "$s"`                 | empty string                                |
| `-s "$x"`        | non-empty      |     | `! -f "$x"`               | NOT a file                                  |
| `[ A ] && [ B ]` | both true      |     | `if grep -q w "$f"; then` | a command as the condition                  |

```bash
for f in *.py; do                 # each matching file in turn
    [ -f "$f" ] || continue       # guard: no matches -> the literal "*.py"
    echo "$f"
done > out.txt                    # (optional) the whole loop's output into a file

for i in 1 2 3; do ...; done      # for i in $(seq 1 2 9) · for ((i=1;i<=5;i++))
count=0; count=$((count + 1))     # counter
while read -r a b c; do ...; done < file   # read a file line by line, split into columns
```

## 1.9 Script templates (tested)

**Three-way: file / directory / neither** (2022 pattern; this version: words / copy .sh files / a report file)

```bash
#!/bin/bash
read -p "Name: " name
if [ -f "$name" ]; then
    echo "$name has $(wc -w < "$name") words"
elif [ -d "$name" ]; then
    cp *.sh "$name"
    echo "copied .sh files to $name"
else
    for f in *.f90; do
        echo "$f $(grep -ow real "$f" | wc -l)"
    done > "$name"
    echo "created $name"
fi
```

**Reorder lines in every file with an extension** (2022 3.1: swap lines 2 and 3)

```bash
#!/bin/bash
# swap the 2nd and 3rd line of every .swap23 file
for f in *.swap23; do
    [ -f "$f" ] || continue
    { sed -n '1p' "$f"; sed -n '3p' "$f"; sed -n '2p' "$f"; sed -n '4,$p' "$f"; } > tmp && mv tmp "$f"
done
```

**Swap the first and last lines** (class ex2, `.alter` files)

```bash
#!/bin/bash
# swap first and last line of every .alter file
for f in *.alter; do
    n=$(wc -l < "$f")
    if [ "$n" -lt 2 ]; then
        echo "$f: fewer than 2 lines, skipped"
        continue
    fi
    {
        sed -n '$p' "$f"           # last line first
        sed -n "2,$((n-1))p" "$f"  # middle lines (prints nothing if n=2)
        sed -n '1p' "$f"           # first line last
    } > tmp && mv tmp "$f"
    echo "$f: swapped"
done
```

**Compare the sizes of two files** (2021 3.2)

```bash
#!/bin/bash
read -p "First file: " f1
read -p "Second file: " f2
if [ "$(wc -c < "$f1")" -gt "$(wc -c < "$f2")" ]; then
    echo "$f1 is larger"
else
    echo "$f2 is larger"
fi
```

**Loop over files with if-grep and counters**

```bash
#!/bin/bash
ok=0; bad=0
for f in *.log; do
    if grep -q "Normal termination" "$f"; then
        echo "$f OK"; ok=$((ok+1))
    else
        echo "$f FAILED"; bad=$((bad+1))
    fi
done
echo "OK: $ok  FAILED: $bad"
```

**Lines with a word into different files depending on a count** (class ex3)

```bash
#!/bin/bash
# lines containing the word "for" of every .py file -> name.forY.txt (>=10 times) or name.forN.txt
for f in *.py; do
    count=$(grep -ow 'for' "$f" | wc -l)
    if [ "$count" -ge 10 ]; then
        out="$f.forY.txt"
    else
        out="$f.forN.txt"
    fi
    grep -w 'for' "$f" > "$out"
    echo "$f: $count -> $out"
done
```

**Folder as an argument, with a default and an error exit**

```bash
#!/bin/bash
# usage: bash count_ext.sh [folder]   (default: current folder)
dir=${1:-.}
if [ ! -d "$dir" ]; then
    echo "$dir is not a directory"
    exit 1
fi
for ext in f90 py txt; do
    n=$(ls "$dir"/*."$ext" 2>/dev/null | wc -l)
    echo "$ext: $n"
done
```

**Compare numbers** (class ex1)

```bash
#!/bin/bash
# numbers.sh - sign of a number, then compare a second number with it
read -p "Enter a number: " a
if [ "$a" -lt 0 ]; then
    echo "$a is negative"
elif [ "$a" -eq 0 ]; then
    echo "$a is zero"
else
    echo "$a is positive"
fi
read -p "Enter a second number: " b
if [ "$b" -eq "$a" ]; then
    echo "$b is equal to $a"
elif [ "$b" -lt "$a" ]; then
    echo "$b is less than $a"
else
    echo "$b is greater than $a"
fi
```

**Keep track of the maximum in a loop**

```bash
read -p "Word: " word
best_file=""; best_n=-1
for f in *.f90 *.py; do
    n=$(grep -ow "$word" "$f" | wc -l)
    echo "$f $n"
    if [ "$n" -gt "$best_n" ]; then best_n=$n; best_file=$f; fi
done
echo "Most: $best_file ($best_n)"
```

## 1.10 The question says → you type `[BASH]`

| The question says…                                    | You type…                                            |
|-------------------------------------------------------|------------------------------------------------------|
| lines containing A but not B                          | `grep A f | grep -v B`                               |
| how many times the **word** X appears                 | `grep -ow X f | wc -l`                               |
| how many **lines** contain X                          | `grep -c X f`                                        |
| which files contain X / how many                      | `grep -l X *.ext` / `| wc -l`                        |
| lines N to M                                          | `sed -n 'N,Mp' f` (or `head -n M f | tail -n M-N+1`) |
| lines starting with I, into a file "me"               | `grep '^I' poem.txt > me`                            |
| characters 10–15 of the lines without the word "to"   | `grep -vw 'to' f | cut -c 10-15`                     |
| list sorted by time, newest first, into a file        | `ls -lt /etc > etc.out`                              |
| list incl. hidden files with extension txt/doc/odt    | `ls -a ~ | grep -E '\.(txt|doc|odt)$'`               |
| the five largest files                                | `ls -lS | grep '^-' | head -n 5`                     |
| how many files end in .txt                            | `ls ~/*.txt | wc -l`                                 |
| .f90 files with exactly 20 lines                      | `wc -l ~/*.f90 | awk '$1 == 20 {print $2}'`          |
| readable and writable only by you and your group      | `chmod 660 f`                                        |
| the number of lines (in a script)                     | `n=$(wc -l < "$f")`                                  |
| file size (in a script)                               | `s=$(wc -c < "$f")`                                  |
| ask the user for a name                               | `read -p "Name: " name`                              |
| if it's a file / a directory / neither                | `if [ -f ] … elif [ -d ] … else … fi`                |
| copy the .f90 files into that directory               | `cp *.f90 "$name"`                                   |
| generate a file with the name + a count for each file | `for f in *.py; do echo "$f $(…)"; done > "$name"`   |
| swap / reorder lines of every \*.ext file             | the temp-file block in 1.5 inside `for f in *.ext`   |
| commands in your history with ssh                     | `history | grep ssh`                                 |
| copy to the cluster under a new name                  | `scp poem.txt user@cluster:Copied/poem_remote.txt`   |

## 1.11 Errors → fix `[BASH]`

| Error                                       | Cause                                                   |
|---------------------------------------------|---------------------------------------------------------|
| `x: command not found` on `x = 5`           | spaces around `=`                                       |
| `[: missing ']'` · `[-f: command not found` | missing spaces inside `[ ]`                             |
| `unexpected end of file`                    | a missing `fi` / `done`                                 |
| `integer expression expected`               | an empty or non-numeric variable (check it with `echo`) |
| `Permission denied` on `./s.sh`             | `chmod +x s.sh`, or use `bash s.sh`                     |
| the loop prints `*.xyz`                     | no file matched → `[ -f "$f" ] || continue`             |
| the file is empty after `sed ... f > f`     | use a temp file + `mv`                                  |

### \[PAST\] Bash solutions: 2021-22 Ex 3 · 2022-23 Ex 1

Moved to the worked examples, with the full statements: [EX-SH-02](#ex-sh-02) (2022-23 exam, Ex 1.1) · [EX-SH-01](#ex-sh-01) (2022-23 exam, Ex 1.2) · [EX-SH-03](#ex-sh-03) (2021-22 exam, Ex 3.2) · [EX-SH-04](#ex-sh-04) (2021-22 exam, Ex 3.1).

### \[PAST\] Class Linux exercise sheet (1–14)

The class numbering 1–14, compact. Full statements and more variants: [EX-SH-16](#ex-sh-16) (Linux drills Section 1 (class exercises 1, 2, 10, 11)) · [EX-SH-17](#ex-sh-17) (Linux drills Section 2 (class exercises 3, 6, 12, 13, 14)) · [EX-SH-18](#ex-sh-18) (Linux drills Section 3 (class exercises 4, 5, 7, 8, 9 + past exams)).

```bash
# 1
ls -lt /etc > etc.out
# 2
ls -a ~ | grep -E '\.(txt|doc|odt)$'
# 3
chmod 660 etc.out
# 4
sed -n '3,7p' llistat.llarg            # or: head -n 7 llistat.llarg | tail -n 5
# 5
wc -l ~/*.f90 | awk '$1 == 20 {print $2}'
# 6
cat > poem.txt << 'END'
I went down to the river,
I set down on the bank.
I tried to think but couldn't,
So I jumped in and sank.
END
# 7
grep '^I' poem.txt > me
# 8
grep -vw 'to' poem.txt | cut -c 10-15
# 9
history | grep ssh
# 10
ls -lS | grep '^-' | head -n 5
# 11
ls ~/*.txt | wc -l
# 12
cp poem.txt poem2.txt; sed -i 's/think/swim/' poem2.txt; diff poem.txt poem2.txt
# 13  -> afterwards "cat poem_link" gives: No such file or directory (dangling link)
ln -s poem.txt poem_link; rm poem.txt; cat poem_link
# 14
scp poem.txt user@cluster:Copied/poem_remote.txt
```

# 2. Gnuplot `[GNU]`

Run a script with `gnuplot script.gp`. `set` commands only change settings; nothing is drawn until `plot` / `splot`. Settings carry over to later plots until you `unset` them.

| Need                    | Command                                                                                                                                     |
|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| output                  | `set terminal pngcairo size 900,700` (or `png`, `jpeg`, `postscript eps`) · `set output 'f.png'` (a new one for each figure)                |
| function                | `f(x,y) = sin(x)*cos(y) + 0.2*x*y` · power is `**` · `pi` is built in · **`1/2 = 0`** (integers!) → write `1.0/2`                           |
| ranges / labels / title | `set xrange [-5:5]` · `set xlabel 'x/nm'` · `set zlabel 'z'` · `set title '...'`                                                            |
| grid density            | `set isosamples 50,30` + `set samples 50,30` (50 x-values × 30 y-values)                                                                    |
| surface                 | `set hidden3d` (opaque mesh) · gray: `set pm3d implicit at s; set palette gray; unset colorbox` + `splot f(x,y) with lines lc rgb 'gray50'` |
| contours                | `set contour base` (also `surface`, `both`) · `set cntrparam levels incremental -2,0.5,2` · `set cntrparam levels 10`                       |
| view                    | `set view 50,60` (rotation about x, about z) · 2D map: `set view map; unset surface`                                                        |
| legend                  | `set key title 'Waves' box` · `set key outside left top` · `notitle` · `title 'surface'`                                                    |
| arrow / label           | `set arrow 1 from x1,y1,z1 to x2,y2,z2 lw 2` · `set label 1 'Max' at x,y,z`                                                                 |
| grid to a file          | `set table 'values.dat'; splot f(x,y); unset table` (**before** `set contour`)                                                              |
| plot a data file        | `plot 'f.out' using 1:2 with linespoints title 'a', '' using 1:3 with lines dt 2`                                                           |
| line styles             | `with lines` / `points` / `linespoints` · `lw 2` · `dt 2` (dashed) · `lc rgb 'red'`                                                         |

**Question says → you type:** "contours separated by 0.5" → `levels incremental start,0.5,end` · "contours at the base" → `set contour base` · "2D, only the level curves" → `set view map; unset surface` · "a file with the x y z values of a 50×30 grid" → `isosamples 50,30` + `set table` · "surface in gray" → the pm3d gray line · "View: 50,60" → `set view 50,60`

## 2.1 Templates (tested)

**3D surface + base contours + arrow/label + data file + a second 2D figure** (2021 Ex 4)

```gnuplot
set terminal pngcairo size 900,700
f(x,y) = sin(x)*cos(y) + 0.2*x*y
set xrange [-5:5]
set yrange [-3:3]
set xlabel 'x'
set ylabel 'y'
set zlabel 'z'

# 1) values.dat on a 50x30 grid -- BEFORE 'set contour'
set samples 50,30
set isosamples 50,30
set table 'values.dat'
splot f(x,y)
unset table

# 2) 3D surface + contours on the base, every 0.5
set contour base
set cntrparam levels incremental -4,0.5,4
set hidden3d
set key outside
set arrow 1 from 3.5,2.5,5 to 1.64,0.34,1.05 lw 2
set label 1 'Max' at 3.6,2.6,5.3
set output 'surface3d.png'
splot f(x,y) title 'z(x,y)'

# 3) 2D: only the level curves
set output 'contours2d.png'
unset surface
set view map
unset hidden3d
set arrow 1 from 3.5,2.5 to 1.64,0.34 lw 2
set label 1 'Max' at 3.6,2.6
splot f(x,y) notitle
```

**Gray surface + contours + key title + view** (2022 Ex 2)

```gnuplot
set terminal pngcairo size 900,700
set output 'waves.png'
f(x,y) = cos(x**3-y) - sin(x+y)
set xrange [0:2]
set yrange [0:5]
set isosamples 40,40
set samples 40,40
set xlabel 'x/nm'
set ylabel 'y/nm'
set zlabel 'A/s'
set title 'cos(x**3-y)-sin(x+y). View: 50,60'
set view 50,60
set key outside left top title 'Waves' box
set contour base
set cntrparam levels incremental -2,0.2,2
set pm3d implicit at s
set palette gray
unset colorbox
splot f(x,y) with lines lc rgb 'gray50' title 'surface'
```

**Plot columns of a data file** (e.g. a Fortran output)

```gnuplot
set terminal pngcairo size 800,500
set output 'tables.png'
set xlabel 'x'; set ylabel 'value'
set key top left
plot 'two/table1.out' using 1:2 with linespoints title 'f1 (set 1)', \
     'two/table1.out' using 1:3 with linespoints dt 2 title 'f2 (set 1)'
```

**Finding the maximum to point an arrow at:** read it off the 2D map, or use Python on `values.dat`: `d = np.loadtxt('values.dat', usecols=(0,1,2)); d[np.argmax(d[:,2])]`. For a *relative* (interior) maximum, ignore the edges of the grid.

# 3. Fortran and make `[F90]` `[MAKE]`

## 3.1 Compile and run

```bash
gfortran -Wall prog.f90 -o prog      # compile + link (-Wall: warnings that catch bugs)
./prog                               # run
echo "3 4" | ./prog                  # feed the values that read(*,*) asks for
gfortran -c f1.f90                   # compile only -> f1.o
gfortran main.o f1.o f2.o -o prog    # link the objects
```

## 3.2 Program skeleton

```fortran
program name
  implicit none                      ! ALWAYS: every variable must be declared
  integer :: i                       ! 1) ALL declarations first
  real(8) :: x
  x = 1.0d0                          ! 2) then the statements
  print *, x
contains                             ! (optional) internal functions/subroutines
end program name
```

`!` comment · case-insensitive · `&` continues a line · `;` separates statements on one line

## 3.3 Declarations and traps

| Code                                                                                    | Meaning                         |
|-----------------------------------------------------------------------------------------|---------------------------------|
| `integer :: i, n` · `real(8) :: x` · `character(len=20) :: s` · `logical :: ok`         | types                           |
| `real(8), parameter :: g = 9.81d0`                                                      | constant                        |
| `real(8) :: x(10)` · `real(8) :: a(3,3)` · `integer :: v(3) = [1,2,3]`                  | arrays (indices start at **1**) |
| `real(8), allocatable :: x(:), xyz(:,:)` → `allocate(x(n), xyz(3,n))` → `deallocate(x)` | size known only at run time     |
| `character(len=2), allocatable :: sym(:)`                                               | array of strings                |

**Traps:** `0.1` is single precision → `0.1d0` · `7/2 = 3` → `dble(7)/2` · `1/n` with integer n → `1.0d0/n` · never compare reals with `==` → `abs(a-b) < 1d-12` · no implicit multiplication: `a(r-re)` must be `a*(r-re)`

## 3.4 Intrinsics

`+ - * / **` · `sqrt exp log log10 sin cos tan acos abs mod(i,2) min max` · `dble(i) int(x) nint(x)` · π = `acos(-1.0d0)` · `huge(1.0d0)`  
Arrays: `sum size maxval minval maxloc product` · whole-array maths `2*x`, `sqrt(sum((a-b)**2))` · `a(i,:)` row, `a(:,j)` column, `xyz(:,i)` all coordinates of atom i  
Strings: `trim(s)` · `len_trim(s)` · `//` concatenation · `trim(sym(i)) == 'C'`  
Comparisons: `== /= < <= > >=` · `.and. .or. .not.`

## 3.5 Input / output

| Code                                                                    | Meaning                               |
|-------------------------------------------------------------------------|---------------------------------------|
| `print *, 'x =', x` · `write(*,*) x`                                    | screen, default format                |
| `write(*,'(A,F10.4)') 'x =', x`                                         | formatted                             |
| `write(*,'(A)', advance='no') 'n: '`                                    | prompt on the same line               |
| `read(*,*) a, b` · `read(*,*,iostat=ios) x`                             | keyboard · `ios /= 0` → invalid input |
| `open(unit=10, file='o.out', status='replace')`                         | a new file to write (units ≥ 10)      |
| `open(unit=10, file='in.dat', status='old')`                            | an existing file to read              |
| `write(10,'(2F12.6)') x, y` · `read(10,*) a, b` · `read(10,'(A)') line` | to/from a file · a whole line as text |
| `close(10)` · `stop 'msg'`                                              | close · end the program               |

**Formats:** `I5` `I0` (minimum width) · `F10.4` · `ES14.5` (scientific; use it for values that span orders of magnitude) · `A` · `L3` · `2X` · `/` (new line) · `3F12.5` (repeat) · `'(10F7.2)'` (a whole array on one line)

**Read until the end of a file:**

```fortran
n = 0
do
   read(10,*,iostat=ios) x, y
   if (ios /= 0) exit
   n = n + 1
end do
```

## 3.6 Control flow

```fortran
if (g >= 9.0d0) then ... else if (g >= 7.0d0) then ... else ... end if
if (x < 0.0d0) print *, 'neg'            ! one-line if
do i = 1, 10 ... end do                  ! do i = 10, 0, -2 (step)
   if (...) cycle                        ! next i
   if (...) exit                         ! leave the loop
rows: do i = 1, 3 ... end do rows        ! labelled loop
do while (nfact <= 1000000) ... end do
```

"x from a to b in steps of h" → `do i = 0, n` with `x = a + h*i` (loop over an integer, never over a real) · all pairs once → `do i = 1, n-1` / `do j = i+1, n`

## 3.7 Functions, subroutines, modules

```fortran
real(8) function f(x, a)            ! the type in front = the type of the RESULT
  implicit none
  real(8), intent(in) :: x, a       ! declare every argument
  f = a*x**2                        ! return the value by assigning to the NAME
end function f

subroutine solve(a, b, r1, r2)      ! several results -> subroutine; called with "call solve(...)"
  implicit none
  real(8), intent(in)  :: a, b
  real(8), intent(out) :: r1, r2    ! (inout = read and modified)
  ...
end subroutine solve
```

| Where the procedure lives      | How                                                                             | Use it when                                            |
|--------------------------------|---------------------------------------------------------------------------------|--------------------------------------------------------|
| after `contains`               | inside the program, before `end program`                                        | everything in one file; arrays `x(:)` work             |
| **a separate file + external** | `f.f90`; in main: `real(8), external :: f`                                      | **the standard pattern "functions in separate files"** |
| a module                       | `module m … contains … end module m`; in main: `use m` (before `implicit none`) | separate files + array arguments; no `external`        |

## 3.8 make

```makefile
FC = gfortran
FFLAGS = -O2 -Wall
OBJ = main.o f1.o f2.o

prog: $(OBJ)                        # target: prerequisites
	$(FC) $(FFLAGS) -o prog $(OBJ)   # recipe: starts with a TAB

main.o: main.f90                    # with a module: main.o: main.f90 m.o
	$(FC) $(FFLAGS) -c main.f90

f1.o: f1.f90
	$(FC) $(FFLAGS) -c f1.f90

f2.o: f2.f90
	$(FC) $(FFLAGS) -c f2.f90

clean:
	rm -f *.o *.mod prog *.out
```

- `make` builds the first target · `make clean` · `make -n` (show the commands without running them) · after `touch f1.f90`, only `f1.o` is rebuilt and the program relinked (timestamps).
- **"At least 4 rules"** = the program + one rule per `.o` + `clean` (+ `run`).
- Pasted text can lose its TABs. Fix them all at once: `sed -i 's/^ \+/\t/' Makefile`

### make without variables (everything typed out)

The same Makefile as above, with every `$(…)` replaced by what it stands for. Both versions run exactly the same commands. A variable is only a name for a piece of text, and `$(NAME)` means “paste NAME’s text here”.

| Variable version                    | What it is                                    | Typed out          |
|-------------------------------------|-----------------------------------------------|--------------------|
| `FC = gfortran` → `$(FC)`           | the Fortran compiler                          | `gfortran`         |
| `FFLAGS = -O2 -Wall` → `$(FFLAGS)`  | compiler options: optimise, show all warnings | `-O2 -Wall`        |
| `OBJ = main.o f1.o f2.o` → `$(OBJ)` | the list of object files                      | `main.o f1.o f2.o` |

**Program + 3 object files + run + clean (tested):**

```makefile
prog: main.o f1.o f2.o
	gfortran -O2 -Wall -o prog main.o f1.o f2.o

main.o: main.f90
	gfortran -O2 -Wall -c main.f90

f1.o: f1.f90
	gfortran -O2 -Wall -c f1.f90

f2.o: f2.f90
	gfortran -O2 -Wall -c f2.f90

run: prog
	./prog

clean:
	rm -f *.o *.mod prog *.out
```

**With a module (tested):** `main.o` also needs `energies.o`, because compiling `energies.f90` creates `energies.mod`, which `use energies` in `main.f90` reads.

```makefile
kinetic: main.o energies.o
	gfortran -O2 -Wall -o kinetic main.o energies.o

main.o: main.f90 energies.o
	gfortran -O2 -Wall -c main.f90

energies.o: energies.f90
	gfortran -O2 -Wall -c energies.f90

clean:
	rm -f *.o *.mod kinetic
```

- Read every rule as: **target: the files it needs**, then on the next line a **TAB** and the exact command you’d type in the terminal.
- **Translate any variable Makefile into typed-out commands:** run `make -n` in its folder. It prints every command with the variables already filled in, without running anything.
- If the question asks you to **use variables**, write it typed out first, check it works, then add the `FC` / `FFLAGS` / `OBJ` lines at the top and swap each repeated piece of text for its `$(NAME)`.

## 3.9 Templates (tested)

**Two functions in separate files + two tables (screen + files) + Makefile.** This is the standard pattern; edit the lines marked EDIT.

```fortran
program two_tables
  ! TEMPLATE: two functions in separate files, two tables on screen + in files.
  ! EDIT: names, parameters, range (x0, dx, n), formats, file names.
  implicit none
  real(8), external :: f1, f2          ! one entry per function file
  real(8) :: x, x0, dx, a, b
  integer :: i, n

  x0 = 0.1d0; dx = 0.1d0; n = 9        ! x = x0, x0+dx, ..., x0+n*dx  (here 0.1 .. 1.0)
  ! (if the question says "ask for n":  write(*,'(A)',advance='no') 'n: '; read(*,*) n)

  ! ---------- table 1 ----------
  a = 2.0d0; b = 1.0d0
  open(unit=10, file='table1.out', status='replace')
  write(*,'(A)') '=== table 1'
  write(*,'(3A14)')  'x',  'f1', 'f2'
  write(10,'(3A14)') '#x', 'f1', 'f2'
  do i = 0, n
     x = x0 + dx*i
     write(*,'(F14.4,2ES14.5)')  x, f1(x, a), f2(x, a, b)
     write(10,'(F14.4,2ES14.5)') x, f1(x, a), f2(x, a, b)
  end do
  close(10)

  ! ---------- table 2 ----------
  a = 0.5d0; b = 3.0d0
  open(unit=11, file='table2.out', status='replace')
  write(*,'(/,A)') '=== table 2'
  write(*,'(3A14)')  'x',  'f1', 'f2'
  write(11,'(3A14)') '#x', 'f1', 'f2'
  do i = 0, n
     x = x0 + dx*i
     write(*,'(F14.4,2ES14.5)')  x, f1(x, a), f2(x, a, b)
     write(11,'(F14.4,2ES14.5)') x, f1(x, a), f2(x, a, b)
  end do
  close(11)
end program two_tables
```

```fortran
real(8) function f1(x, a)
  ! EDIT: formula 1
  implicit none
  real(8), intent(in) :: x, a
  f1 = a*x**2
end function f1
```

```fortran
real(8) function f2(x, a, b)
  ! EDIT: formula 2
  implicit none
  real(8), intent(in) :: x, a, b
  f2 = a*exp(-b*x)
end function f2
```

```makefile
FC = gfortran
FFLAGS = -O2 -Wall
OBJ = main.o f1.o f2.o

prog: $(OBJ)
	$(FC) $(FFLAGS) -o prog $(OBJ)

main.o: main.f90
	$(FC) $(FFLAGS) -c main.f90

f1.o: f1.f90
	$(FC) $(FFLAGS) -c f1.f90

f2.o: f2.f90
	$(FC) $(FFLAGS) -c f2.f90

run: prog
	./prog

clean:
	rm -f *.o *.mod prog *.out
```

**Read an .xyz file → print it → largest distance (indices + symbols) → atom counts + molecular mass** (class evaluation)

```fortran
program molecule
  implicit none
  integer :: nat, i, j, imax, jmax, nC, nH, nO
  character(len=80) :: title
  character(len=2), allocatable :: sym(:)
  real(8), allocatable :: xyz(:,:)
  real(8) :: d, dmax, mass

  ! ---------- a) read and print ----------
  open(unit=10, file='molecule_B.xyz', status='old')
  read(10,*) nat                      ! line 1: number of atoms
  read(10,'(A)') title                ! line 2: the whole line as text
  allocate(sym(nat), xyz(3,nat))
  do i = 1, nat
     read(10,*) sym(i), xyz(:,i)      ! symbol, then x y z
  end do
  close(10)
  print *, nat
  print *, trim(title)
  do i = 1, nat
     write(*,'(A3,3F12.5)') sym(i), xyz(:,i)
  end do

  ! ---------- b) largest distance between any two atoms ----------
  dmax = 0.0d0
  do i = 1, nat - 1
     do j = i + 1, nat                ! each pair once
        d = sqrt(sum((xyz(:,i) - xyz(:,j))**2))
        if (d > dmax) then
           dmax = d; imax = i; jmax = j
        end if
     end do
  end do
  write(*,'(/,A,F8.4,A)') 'Largest distance:', dmax, ' Angstrom'
  write(*,'(A,I0,3A,I0,3A)') ' between atom ', imax, ' (', trim(sym(imax)), ') and atom ', &
                             jmax, ' (', trim(sym(jmax)), ')'
  ! ---------- c) count atoms and molecular mass ----------
  nC = 0; nH = 0; nO = 0
  do i = 1, nat
     if (trim(sym(i)) == 'C') nC = nC + 1
     if (trim(sym(i)) == 'H') nH = nH + 1
     if (trim(sym(i)) == 'O') nO = nO + 1
  end do
  mass = 12.0d0*nC + 1.0d0*nH + 16.0d0*nO
  print '(/,A,I3,A,I3,A,I3)', 'C:', nC, '   H:', nH, '   O:', nO
  print '(A,F8.2,A)', 'Molecular mass:', mass, ' g/mol'
end program molecule
```

**More .xyz analysis:** centre of mass, shortest C–H distance, bond count, report written to screen + file

```fortran
program report
  implicit none
  integer :: nat, i, j, ic, ih, nbonds
  character(len=80) :: title
  character(len=2), allocatable :: sym(:)
  real(8), allocatable :: xyz(:,:), m(:)
  real(8) :: com(3), d, dmin

  ! ---------- read ----------
  open(unit=10, file='molecule_B.xyz', status='old')
  read(10,*) nat
  read(10,'(A)') title
  allocate(sym(nat), xyz(3,nat), m(nat))
  do i = 1, nat
     read(10,*) sym(i), xyz(:,i)
  end do
  close(10)

  ! ---------- a) centre of mass ----------
  do i = 1, nat
     if (trim(sym(i)) == 'C') m(i) = 12.0d0
     if (trim(sym(i)) == 'H') m(i) = 1.0d0
     if (trim(sym(i)) == 'O') m(i) = 16.0d0
  end do
  do j = 1, 3
     com(j) = sum(m*xyz(j,:))/sum(m)
  end do

  ! ---------- b) shortest C-H distance ----------
  dmin = huge(1.0d0)
  do i = 1, nat
     do j = 1, nat
        if (trim(sym(i)) == 'C' .and. trim(sym(j)) == 'H') then
           d = sqrt(sum((xyz(:,i) - xyz(:,j))**2))
           if (d < dmin) then
              dmin = d; ic = i; ih = j
           end if
        end if
     end do
  end do

  ! ---------- c) bonds: pairs closer than 1.6 A ----------
  nbonds = 0
  do i = 1, nat - 1
     do j = i + 1, nat
        if (sqrt(sum((xyz(:,i) - xyz(:,j))**2)) < 1.6d0) nbonds = nbonds + 1
     end do
  end do

  ! ---------- d) write the report to screen and file ----------
  open(unit=20, file='report.txt', status='replace')
  write(*,'(A,3F10.4)')  'Centre of mass:', com
  write(20,'(A,3F10.4)') 'Centre of mass:', com
  write(*,'(A,F8.4,A,I0,A,I0)')  'Shortest C-H:', dmin, '  atoms ', ic, ' and ', ih
  write(20,'(A,F8.4,A,I0,A,I0)') 'Shortest C-H:', dmin, '  atoms ', ic, ' and ', ih
  write(*,'(A,I0)')  'Bonds (< 1.6 A): ', nbonds
  write(20,'(A,I0)') 'Bonds (< 1.6 A): ', nbonds
  close(20)
end program report
```

**Module version** (functions in a module; the Makefile needs `main.o: main.f90 energies.o`)

```fortran
module energies
  implicit none
  real(8), parameter :: c = 2.998d8
contains
  real(8) function e_classic(m, v)
    real(8), intent(in) :: m, v
    e_classic = 0.5d0*m*v**2
  end function e_classic

  real(8) function e_relat(m, v)
    real(8), intent(in) :: m, v
    e_relat = (1.0d0/sqrt(1.0d0 - (v/c)**2) - 1.0d0)*m*c**2
  end function e_relat
end module energies
```

```fortran
program kinetic_mod
  use energies                 ! gives access to e_classic, e_relat and c (no 'external' needed)
  implicit none
  real(8) :: m, v
  integer :: i
  m = 9.109d-31
  do i = 1, 9
     v = 0.1d0*i*c
     write(*,'(F6.2,2ES14.5)') v/c, e_classic(m, v), e_relat(m, v)
  end do
end program kinetic_mod
```

```makefile
FC = gfortran
FFLAGS = -O2 -Wall

kinetic: main.o energies.o
	$(FC) $(FFLAGS) -o kinetic main.o energies.o

main.o: main.f90 energies.o
	$(FC) $(FFLAGS) -c main.f90

energies.o: energies.f90
	$(FC) $(FFLAGS) -c energies.f90

clean:
	rm -f *.o *.mod kinetic
```

**Subroutine with intent(out) after contains + keyboard input**

```fortran
program quad
  implicit none
  real(8) :: a, b, c, disc, x1, x2
  integer :: nroots
  print *, 'a, b, c ='
  read(*,*) a, b, c
  call solve(a, b, c, disc, x1, x2, nroots)
  print *, 'discriminant =', disc
  if (nroots == 2) then
     print *, 'two roots:', x1, x2
  else if (nroots == 1) then
     print *, 'one root:', x1
  else
     print *, 'no real roots'
  end if
contains
  subroutine solve(a, b, c, disc, x1, x2, nroots)
    real(8), intent(in)  :: a, b, c
    real(8), intent(out) :: disc, x1, x2
    integer, intent(out) :: nroots
    disc = b*b - 4.0d0*a*c
    x1 = 0.0d0; x2 = 0.0d0
    if (disc > 0.0d0) then
       nroots = 2
       x1 = (-b + sqrt(disc))/(2.0d0*a)
       x2 = (-b - sqrt(disc))/(2.0d0*a)
    else if (disc == 0.0d0) then
       nroots = 1
       x1 = -b/(2.0d0*a)
    else
       nroots = 0
    end if
  end subroutine solve
end program quad
```

**Subroutine taking an array of any length**

```fortran
program statistics
  implicit none
  real(8) :: data(6) = [2.0d0, 4.0d0, 4.0d0, 5.0d0, 7.0d0, 9.0d0]
  real(8) :: m, sd
  call stats(data, m, sd)
  print '(A,F8.4,A,F8.4)', 'mean =', m, '   std dev =', sd
contains
  subroutine stats(x, mean, sd)
    real(8), intent(in)  :: x(:)          ! assumed shape: any length
    real(8), intent(out) :: mean, sd
    integer :: n
    n = size(x)
    mean = sum(x)/n
    sd = sqrt(sum((x - mean)**2)/n)
  end subroutine stats
end program statistics
```

**Read a file until the end**

```fortran
program readsin
  implicit none
  integer :: ios, n
  real(8) :: x, y, ymax, xmax
  n = 0
  ymax = -huge(1.0d0)
  open(unit=10, file='sin.dat', status='old')
  do
     read(10,*,iostat=ios) x, y
     if (ios /= 0) exit                 ! end of file (or a bad line)
     n = n + 1
     if (y > ymax) then
        ymax = y; xmax = x
     end if
  end do
  close(10)
  print *, 'lines read:', n
  print *, 'max y =', ymax, ' at x =', xmax
end program readsin
```

**Validate input with iostat + if/else if** (class ex1)

```fortran
program grade
  implicit none
  real(8) :: g
  integer :: ios
  print *, 'Enter a grade between 0 and 10:'
  read(*,*,iostat=ios) g
  if (ios /= 0) then
     print *, 'Error: not a number'
  else if (g < 0.0d0 .or. g > 10.0d0) then
     print *, 'Error: out of range'
  else if (g >= 9.0d0) then
     print *, 'Excellent'
  else if (g >= 7.0d0) then
     print *, 'Good'
  else if (g >= 5.0d0) then
     print *, 'Pass'
  else
     print *, 'Fail'
  end if
end program grade
```

**Arrays: sum / mean / maxval / maxloc / slices; allocatable**

```fortran
program arrays
  implicit none
  real(8) :: x(10)
  integer :: i
  do i = 1, 10
     x(i) = i**2/10.0d0
  end do
  print '(10F7.2)', x
  print *, 'sum  =', sum(x)
  print *, 'mean =', sum(x)/size(x)
  print *, 'max  =', maxval(x), ' at index', maxloc(x)
  print '(10F7.2)', 2*x            ! whole-array operation
end program arrays
```

```fortran
program alloc
  implicit none
  real(8), allocatable :: x(:)
  integer :: n, i
  print *, 'n ='
  read(*,*) n
  allocate(x(n))
  do i = 1, n
     x(i) = 1.0d0/i
  end do
  print '(A,F12.6)', 'sum of 1/i =', sum(x)
  deallocate(x)
end program alloc
```

## 3.10 The question says → you write `[F90]`

| The question says…                                   | You write…                                                     |
|------------------------------------------------------|----------------------------------------------------------------|
| functions written in separate files                  | `f1.f90`, `f2.f90` + `real(8), external :: f1, f2` in main     |
| different functions / separate files **with arrays** | a module (+ `use`)                                             |
| x from a to b in steps of h                          | `do i = 0, n` with `x = a + h*i`                               |
| write a table on the screen and in a file            | `open` + two `write`s per row (`*` and `10`) + `close`         |
| two tables with different parameters                 | the loop twice (units 10 and 11), or a subroutine called twice |
| ask for the number of terms n                        | `read(*,*) n` → test with `echo 100 | ./prog`                  |
| the value of π from the arccosine of −1              | `acos(-1.0d0)`                                                 |
| read the file X                                      | `open(..., status='old')` + `read(10,*)`                       |
| the largest / smallest distance between any two      | double loop `i<j` + `dmax, imax, jmax`                         |
| number of atoms of each type                         | `if (trim(sym(i)) == 'C') nC = nC + 1`                         |
| compilation with make, at least 4 rules              | the template Makefile (3.8)                                    |

## 3.11 Errors → fix `[F90]` `[MAKE]`

| Error                                                                       | Cause                                                                    |
|-----------------------------------------------------------------------------|--------------------------------------------------------------------------|
| `Function 'f' at (1) has no IMPLICIT type`                                  | missing `real(8), external :: f` in main                                 |
| `Unclassifiable statement`                                                  | a typo, or a declaration placed after an executable statement            |
| `Symbol 'x' has no IMPLICIT type`                                           | the variable isn't declared                                              |
| `Rank mismatch` / `Type mismatch in argument`                               | `0.1` passed where `0.1d0` is needed, or an array vs a scalar            |
| Explicit interface required (assumed-shape `x(:)` in an external procedure) | put it after `contains` or in a module                                   |
| `Fortran runtime error: End of file`                                        | reading past the end → `iostat` loop, or a wrong count                   |
| garbage / 0 results                                                         | integer division, or a single-precision literal                          |
| `missing separator. Stop.`                                                  | spaces instead of a TAB in the Makefile → `sed -i 's/^ \+/\t/' Makefile` |
| `No rule to make target 'x.f90'`                                            | a misspelled file name in a rule                                         |
| `undefined reference to 'f_'`                                               | an object is missing from the link line                                  |
| `Cannot open module file 'm.mod'`                                           | main.o doesn't depend on m.o                                             |

### \[PAST\] Fortran solutions: 2021-22 Ex 5 (π) · 2022-23 Ex 3 (enzyme) · class evaluation (.xyz is in 3.9)

Moved to the worked examples, with the full statements: [EX-F90-01](#ex-f90-01) (2022-23 exam, Ex 3 (2 points)) · [EX-F90-02](#ex-f90-02) (2021-22 exam, Ex 5 (1.5 points)) · [EX-F90-07](#ex-f90-07) (Class assignment \`Fortran_Assignment_Updated.pdf\`).

# 4. Python: NumPy `[NP]`

## 4.1 Shapes and axes

`v.shape == (5, 7, 3)` = 5 systems × 7 particles × xyz. **The axis you reduce over disappears:** `v.mean(axis=1)` → (5, 3) · `np.linalg.norm(v, axis=2)` → (5, 7) · `axis=-1` = the last axis (xyz)

| Task            | Code                                                                                                                                                                                                                                              |
|-----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| random data     | `rng = np.random.default_rng(12345)` (**once**; re-run from here) · `rng.random((N,3)) * np.array([Lx,Ly,Lz])` · `rng.random(size=(5,7,3))` · `rng.uniform(a,b,n)` · `rng.normal(m,s,n)` · `rng.integers(lo,hi,n)` · `rng.choice([-1,1], size=n)` |
| indexing        | `v[2,4]` · `v[:,:,2]` (all z) · `pos[i]` · `pos[:,0]` · `a[-1]`                                                                                                                                                                                   |
| masks           | `m = pos[:,2] > 5` → `pos[m]` · `np.where(m)[0]` (indices) · `m.sum()` (count) · `(a>1) & (b<2)` · `(M>1).any(axis=1)` · `.all(axis=1)`                                                                                                           |
| set with a mask | `w[w > 0.9] = 0.9` · `np.clip(v, lo, hi)`                                                                                                                                                                                                         |
| reductions      | `sum mean std min max` with `axis=` · `axis=(0,1)`                                                                                                                                                                                                |
| which one       | `np.argmax`, `np.argmin` · matrix: `np.unravel_index(np.argmax(M), M.shape)`                                                                                                                                                                      |
| broadcasting    | `v - v.mean(axis=1, keepdims=True)` · `q[:, None] * pos`                                                                                                                                                                                          |
| sort            | `np.argsort(d)` (indices) · `np.sort(d)`                                                                                                                                                                                                          |
| print           | `f"{x:.3f}"` · `np.round(a,3)` · `", ".join(f"{c:.3f}" for c in vec)` · `int(i)`                                                                                                                                                                  |

## 4.2 Vectors

`unit = v / np.linalg.norm(v, axis=-1, keepdims=True)` · `a @ b` dot product · `X @ y` one per row · `A @ B.T` all pairs · `(a*b).sum(axis=1)` row-wise · angle `np.degrees(np.arccos(np.clip(cos,-1,1)))` · `np.cross(a,b)`

## 4.3 All pairs

```python
from scipy.spatial.distance import pdist, squareform
d = pdist(pos)                      # each pair once (i<j)
D = squareform(d)                   # (N,N), zeros on the diagonal
np.fill_diagonal(D, np.inf)         # for MIN / closest / neighbours
i, j = np.unravel_index(np.argmin(D), D.shape)
print(D[i,j], np.linalg.norm(pos[i]-pos[j]))       # prove it
ii, jj = np.triu_indices(N, k=1)    # the index pairs in pdist order
np.outer(q, q)                      # q_i*q_j for every pair
diff = pos[:,None,:] - pos[None,:,:]; D = np.sqrt((diff**2).sum(-1))   # without SciPy
```

**Fallback double loop:** `for a in range(N): for b in range(a+1, N): d = np.linalg.norm(pos[a]-pos[b])`, keeping `best, ba, bb`.

## 4.4 The question says → you type `[NP]`

| The question says…                                                            | You type…                                                        |
|-------------------------------------------------------------------------------|------------------------------------------------------------------|
| use this random seed                                                          | `rng = np.random.default_rng(seed)`                              |
| N particles in a box (a, b, c)                                                | `rng.random((N,3)) * np.array([a,b,c])`                          |
| normalise / module 1                                                          | `v / np.linalg.norm(v, axis=-1, keepdims=True)`                  |
| vector (2,4) with 3 decimals                                                  | `", ".join(f"{c:.3f}" for c in unit[2,4])`                       |
| the vector of system 1 with the largest dot product with vector 3 of system 0 | `np.argmax(v[1] @ v[0,3])`                                       |
| dot product of all pairs between systems 3 and 4                              | `prods = v[3] @ v[4].T`                                          |
| rows with a value \> 1 → their first element                                  | `for r in np.where((prods>1).any(axis=1))[0]: print(prods[r,0])` |
| minimum distance between all pairs                                            | `squareform(pdist)` + `fill_diagonal(inf)` + `.min()`            |
| which pair (indices) + prove it                                               | `unravel_index(argmin)` + `np.linalg.norm(pos[i]-pos[j])`        |
| a function for the energy U = Σ zᵢ²                                           | `def U(pos): return np.sum(pos[:,2]**2)`                         |
| Σᵢ\<ⱼ f(rᵢⱼ)                                                                  | `r = pdist(pos); np.sum(f(r))`                                   |
| neighbours within a cutoff                                                    | `(D < rc).sum(axis=1)` (with inf on the diagonal)                |

## 4.5 Templates (tested) `[NP]`

**Shapes, centres, lengths, the index of the maximum**

```python
import numpy as np
rng = np.random.default_rng(2025)
v = rng.random((4, 10, 3))
print(v.shape)                                   # (4, 10, 3)
centres = v.mean(axis=1)                         # average over particles -> (4, 3)
print(centres.shape); print(np.round(centres, 3))
lengths = np.linalg.norm(v, axis=2)              # one number per particle -> (4, 10)
print(lengths.shape)
idx = np.unravel_index(np.argmax(v), v.shape)    # (system, particle, coordinate)
print("largest value", v[idx].round(4), "at", tuple(int(k) for k in idx))
```

**Masks**

```python
print(np.where(v[0, :, 2] > 0.5)[0])             # system 0, particles with z > 0.5
print((v < 0.5).all(axis=2).sum(axis=1))         # per system: particles with x,y,z all < 0.5
w = v.copy()
w[w > 0.9] = 0.9                                 # or: np.clip(v, None, 0.9)
print(w.max())
```

**Broadcasting: centring, scaling**

```python
centred = v - v.mean(axis=1, keepdims=True)      # (4,10,3) - (4,1,3)
print(np.abs(centred.mean(axis=1)).max() < 1e-12)
box = v * np.array([20, 20, 5])                  # scale each coordinate
print(box.max(axis=(0, 1)).round(2))             # largest x, y, z
```

**Normalise**

```python
unit = v / np.linalg.norm(v, axis=2, keepdims=True)
print(np.allclose(np.linalg.norm(unit, axis=2), 1))
print(", ".join(f"{c:.3f}" for c in unit[3, 7]))
```

**Angles to one vector, the most parallel**

```python
u = unit[2]                                      # system 2, already normalised
cosang = np.clip(u @ u[0], -1, 1)                # 10 cosines (clip guards against 1.0000000002)
ang = np.degrees(np.arccos(cosang))
print(ang.round(1))
ang[0] = np.inf                                  # exclude vector 0 itself
print("most parallel to vector 0:", np.argmin(ang), f"({ang.min():.1f} deg)")
```

**Cross product**

```python
c = np.cross(v[0, 0], v[0, 1])
print(c.round(4), np.dot(c, v[0, 0]).round(12), np.dot(c, v[0, 1]).round(12))
```

**Maximum distance pair**

```python
from scipy.spatial.distance import pdist, squareform
rng = np.random.default_rng(99)
pos = rng.random((25, 3)) * np.array([40, 40, 10])
D = squareform(pdist(pos))
i, j = np.unravel_index(np.argmax(D), D.shape)
print(f"max {D[i, j]:.4f} between {i} and {j}; check {np.linalg.norm(pos[i] - pos[j]):.4f}")
```

**Minimum distance pair + double loop**

```python
Dm = D.copy(); np.fill_diagonal(Dm, np.inf)
i, j = np.unravel_index(np.argmin(Dm), Dm.shape)
print(f"min {Dm[i, j]:.4f} between {i} and {j}; check {np.linalg.norm(pos[i] - pos[j]):.4f}")
best = np.inf
for a in range(len(pos)):
    for b in range(a + 1, len(pos)):
        d = np.linalg.norm(pos[a] - pos[b])
        if d < best:
            best, ba, bb = d, a, b
print("loop:", round(best, 4), ba, bb)
```

**Neighbours**

```python
print("pairs < 8:", (pdist(pos) < 8).sum())
nb = (Dm < 8).sum(axis=1)                        # inf on the diagonal -> itself not counted
print(nb)
k = np.argmax(nb)
print("most neighbours:", k, "->", np.where(Dm[k] < 8)[0])
```

**Dot-product matrix between two systems**

```python
prods = v[1] @ v[3].T                            # (10,10)
i, j = np.unravel_index(np.argmax(prods), prods.shape)
print("largest dot product", prods[i, j].round(4), "at", (int(i), int(j)))
for r in np.where(prods.max(axis=1) > 1.5)[0]:
    print(f"row {r}: first element {prods[r, 0]:.4f}")
```

**Distance matrix without SciPy**

```python
diff = pos[:, None, :] - pos[None, :, :]         # (25,25,3)
D2 = np.sqrt((diff**2).sum(axis=-1))
print(np.allclose(D, D2))
```

**A formula as a function (tested on a small case)**

```python
def trap_energy(pos, k=0.5, centre=(20.0, 20.0)):
    """U = 1/2 k sum[(x-cx)^2 + (y-cy)^2]"""
    dx = pos[:, 0] - centre[0]
    dy = pos[:, 1] - centre[1]
    return 0.5 * k * np.sum(dx**2 + dy**2)
print(trap_energy(np.array([[21.0, 20.0, 0.0], [20.0, 22.0, 5.0]])))   # 0.25*(1+4) = 1.25
print(f"U = {trap_energy(pos):.3f}")
```

**Pair sum Σᵢ\<ⱼ qᵢqⱼ/rᵢⱼ**

```python
def coulomb(pos, q):
    """U = sum_{i<j} q_i q_j / r_ij"""
    i, j = np.triu_indices(len(pos), k=1)        # same pair order as pdist
    return np.sum(q[i] * q[j] / pdist(pos))
q = np.array([1 if n % 2 == 0 else -1 for n in range(len(pos))])
print(coulomb(np.array([[0.0, 0, 0], [2.0, 0, 0]]), np.array([1, -1])))   # -0.5
print(f"U = {coulomb(pos, q):.4f}")
```

**A function returning a tuple**

```python
def closest_pair(pos):
    D = squareform(pdist(pos))
    np.fill_diagonal(D, np.inf)
    i, j = np.unravel_index(np.argmin(D), D.shape)
    return D[i, j], i, j
d, i, j = closest_pair(pos)
print(f"{d:.4f} {i} {j}")
```

**Closest opposite-charge pair (np.outer mask) + dipole + count inside a sphere**

```python
rng = np.random.default_rng(31415)
coords = rng.random((15, 3)) * 12
qs = rng.choice([-1, 1], size=15)
D = squareform(pdist(coords))
opp = np.outer(qs, qs) < 0                       # True where the charges are opposite
D[~opp] = np.inf                                 # also removes the diagonal (q*q = +1)
i, j = np.unravel_index(np.argmin(D), D.shape)
print(f"closest opposite pair {i} ({qs[i]:+d}) - {j} ({qs[j]:+d}): {D[i, j]:.4f}; check {np.linalg.norm(coords[i]-coords[j]):.4f}")
def dipole(coords, q):
    return (q[:, None] * coords).sum(axis=0)
mu = dipole(coords, qs)
print("dipole", mu.round(4), f"|mu| = {np.linalg.norm(mu):.4f}")
print("within 5 of the centre:", (np.linalg.norm(coords - 6, axis=1) < 5).sum())
```

### \[PAST\] NumPy solutions: 2021-22 Ex 1 · 2022-23 Ex 4

Moved to the worked examples, with the full statements: [EX-PY-01](#ex-py-01) (2022-23 exam, Ex 4 (3 points)) · [EX-PY-02](#ex-py-02) (2021-22 exam, Ex 1 (3 points)).

# 5. Python: parsing output files + matplotlib `[PARSE]` `[PLT]`

## 5.1 Choose the pattern

| The file looks like…                                 | Pattern                                                                                                   |
|------------------------------------------------------|-----------------------------------------------------------------------------------------------------------|
| a clean numeric table (`#` comments)                 | **E0** `np.loadtxt('f', comments='#', usecols=(0,2), skiprows=k)`                                         |
| one value on a marker line, once per step            | **E1** `if 'MARKER' in line: vals.append(float(line.split()[k]))`                                         |
| a block between a start marker and an end marker     | **E2** an `inside` flag; rows with `p and p[0].isdigit()`; `break` (first block only) or store each block |
| several lines of values per step                     | **E3** `steps.append([])` when a step starts; `steps[-1] += values`                                       |
| N / title / N lines, repeated (multi-frame .xyz)     | **E4** a `while True:` loop with `f.readline()`                                                           |
| a table of labelled rows per step (e.g. convergence) | `line.startswith((...))` + `.reshape(-1, k)`                                                              |

**Step 0, always:** `!grep -n "MARKER" file | head`, and `print(list(enumerate(line.split())))` to find the index of the field you need.

| String tool                                                         | Use                                                                |
|---------------------------------------------------------------------|--------------------------------------------------------------------|
| `'X' in line` · `line.startswith(' Max')` · `startswith(('a','b'))` | find marker lines                                                  |
| `line.split()` · `line.split('--')[1]` · `line.strip()`             | words · the text after a separator · remove surrounding whitespace |
| `p and p[0].isdigit()`                                              | a data row starting with a number                                  |
| `float(x)` · `int(x)`                                               | convert                                                            |
| `f.readline()`                                                      | one line (returns `''` at the end of the file)                     |
| `line[28:]`, chunks of 10                                           | fixed-width columns (numbers that run together)                    |

**Gaussian:** `Alpha occ. eigenvalues` (two spaces) · HOMO = the **last** value of the last occ line of a step · LUMO = the **first** virt value of that step · energies are in hartree (1 Ha = 627.5095 kcal/mol = 27.2114 eV)  
**ORCA:** `SCF ITERATIONS` → rows `ITER Energy Delta-E ...` (Delta-E = index 2) → ends at `Energy Check` / `SUCCESS` · the header changes when SOSCF starts (column positions after Delta-E move) · `FINAL SINGLE POINT ENERGY` once per point

## 5.2 Parsing templates (tested) `[PARSE]`

**E1: one value per line (energies + step numbers)**

```python
energies, steps = [], []
with open('data/opt_mock.log') as f:
    for line in f:
        if 'SCF Done' in line:
            energies.append(float(line.split()[4]))
        elif 'Step number' in line:
            steps.append(int(line.split()[2]))
energies, steps = np.array(energies), np.array(steps)
print(steps); print(energies)
```

**A table per step: the convergence criteria, the first step where all are YES**

```python
conv, flags = [], []
with open('data/opt_mock.log') as f:
    for line in f:
        if line.startswith((' Maximum Force', ' RMS     Force', ' Maximum Displacement', ' RMS     Displacement')):
            p = line.split()
            conv.append(float(p[2]))
            flags.append(p[4] == 'YES')
conv = np.array(conv).reshape(-1, 4)             # one row per step
flags = np.array(flags).reshape(-1, 4)
print(conv)
first = np.where(flags.all(axis=1))[0][0]
print("first fully converged step:", steps[first])
```

**E2: a block per step (Mulliken charges) → array (steps × atoms)**

```python
charges, labels, inside = [], [], False
with open('data/opt_mock.log') as f:
    for line in f:
        if 'Mulliken charges:' in line:
            inside, block = True, []
            continue
        if inside:
            p = line.split()
            if 'Sum of Mulliken' in line:
                charges.append(block); inside = False
            elif len(p) == 3 and p[0].isdigit():
                block.append(float(p[2]))
                if len(charges) == 0:
                    labels.append(p[1])
charges = np.array(charges)                      # (nsteps, natoms)
print(charges.shape)
for lab, qq in zip(labels, charges[-1]):
    print(f"{lab:3s}{qq:10.6f}")
print("sums:", charges.sum(axis=1).round(6))
```

**E3 + fixed width: orbital energies grouped per step**

```python
def read_values(line):
    """Gaussian eigenvalue line -> list of floats, using the fixed 10-character columns."""
    data = line[28:].rstrip('\n')
    return [float(data[k:k+10]) for k in range(0, len(data), 10)]

occ_steps, virt_steps = [], []
with open('data/opt_mock.log') as f:
    for line in f:
        if 'Population analysis' in line:
            occ_steps.append([]); virt_steps.append([])
        elif 'Alpha  occ. eigenvalues' in line:
            occ_steps[-1] += read_values(line)
        elif 'Alpha virt. eigenvalues' in line:
            virt_steps[-1] += read_values(line)
for s, (o, vv) in enumerate(zip(occ_steps, virt_steps), start=1):
    neg = sum(1 for x in vv if x < 0)
    print(f"step {s}: {len(o)} occ, lowest {o[0]:.5f}, highest virt {vv[-1]:.5f}, negative virt {neg}")
```

**E4: multi-frame .xyz → (frames, atoms, 3) + distances and angles**

```python
frames = []
with open('data/traj.xyz') as f:
    while True:
        line = f.readline()
        if not line.strip():
            break                                # end of file
        nat = int(line)
        f.readline()                             # title line
        frame = [[float(x) for x in f.readline().split()[1:4]] for _ in range(nat)]
        frames.append(frame)
traj = np.array(frames)                          # (nframes, natoms, 3)
print(traj.shape)
oh1 = np.linalg.norm(traj[:, 1] - traj[:, 0], axis=1)
oh2 = np.linalg.norm(traj[:, 2] - traj[:, 0], axis=1)
a = traj[:, 1] - traj[:, 0]; b = traj[:, 2] - traj[:, 0]
cosang = (a * b).sum(axis=1) / (oh1 * oh2)
angle = np.degrees(np.arccos(np.clip(cosang, -1, 1)))
for k in range(len(traj)):
    print(f"frame {k}: OH1 {oh1[k]:.4f}  OH2 {oh2[k]:.4f}  angle {angle[k]:.2f}")
```

**E0: np.loadtxt + linear fit**

```python
md = np.loadtxt('data/md.dat')                   # '#' lines are skipped automatically
t, T, Etot = md[:, 0], md[:, 1], md[:, 2]
print(md.shape, f"mean T = {T.mean():.2f} K, std = {T.std():.2f}")
slope, intercept = np.polyfit(t, Etot, 1)
print(f"Etot drift = {slope:.4f} kJ/mol/ps")
```

## 5.3 matplotlib `[PLT]`

| Need                 | Code                                                                               |
|----------------------|------------------------------------------------------------------------------------|
| line / points        | `plt.plot(x, y, 'o--', color='k', label='...')`: markers `o s ^ .`, lines `- -- :` |
| labels               | `plt.xlabel()` `plt.ylabel()` `plt.title(f'slope = {m:.3f}')` `plt.legend()`       |
| ticks / limits / log | `plt.xticks(steps)` · `plt.xlim(a,b)` · `plt.yscale('log')`                        |
| reference lines      | `plt.axhline(y0, ls='--', color='gray')` · `plt.axvline(x0)`                       |
| fit                  | `m, b = np.polyfit(x, y, 1)` → `plt.plot(x, m*x+b, '--')`                          |
| histogram / scatter  | `plt.hist(d, bins=20, edgecolor='k')` · `plt.scatter(x, y)`                        |
| two panels           | `fig, ax = plt.subplots(1, 2, figsize=(10,4))` → `ax[0].plot`, `ax[0].set_xlabel`  |
| finish               | `plt.tight_layout()` · `plt.savefig('f.png', dpi=150)` **before** `plt.show()`     |
| maths in labels      | `'E$_{tot}$'` · `'log10(|ΔE|)'` · `'Distance (Å)'`                                 |

**Copying a target figure, a checklist:** marker · line style · axis labels and units · integer ticks · legend? · scale (log?) · one panel or two?

## 5.4 Plot templates (tested) `[PLT]`

**Points + dashed line, integer ticks**

```python
import matplotlib.pyplot as plt
rel = (energies - energies[-1]) * 627.5095
plt.figure(figsize=(6, 4))
plt.plot(steps, rel, 'o--', color='k')
plt.xlabel('Optimization step'); plt.ylabel('E - E$_{final}$ (kcal/mol)')
plt.xticks(steps)
plt.tight_layout(); plt.savefig('fig1.png', dpi=110); plt.show()
```

**Several series, log scale, threshold line**

```python
names = ['Max force', 'RMS force', 'Max disp.', 'RMS disp.']
plt.figure(figsize=(6, 4))
for col in range(4):
    plt.plot(steps, conv[:, col], 'o-', label=names[col])
plt.axhline(0.00045, ls='--', color='gray', label='Max force threshold')
plt.yscale('log')
plt.xlabel('Optimization step'); plt.ylabel('Value (a.u.)'); plt.xticks(steps)
plt.legend(); plt.tight_layout(); plt.savefig('fig2.png', dpi=110); plt.show()
```

**Two panels**

```python
fig, ax = plt.subplots(1, 2, figsize=(10, 4))
frame = np.arange(len(traj))
ax[0].plot(frame, oh1, 'o-', label='O-H1')
ax[0].plot(frame, oh2, 's--', label='O-H2')
ax[0].set_xlabel('Frame'); ax[0].set_ylabel('Distance (Å)'); ax[0].legend()
ax[1].plot(frame, angle, 'o-', color='C2')
ax[1].set_xlabel('Frame'); ax[1].set_ylabel('H-O-H angle (deg)')
plt.tight_layout(); plt.savefig('fig3.png', dpi=110); plt.show()
```

**Data + fit line, slope in the title**

```python
plt.figure(figsize=(6, 4))
plt.plot(t, Etot, '.', label='data')
plt.plot(t, slope*t + intercept, '-', color='r', label='fit')
plt.title(f'Energy drift: {slope:.4f} kJ/mol/ps')
plt.xlabel('time (ps)'); plt.ylabel('E$_{tot}$ (kJ/mol)'); plt.legend()
plt.tight_layout(); plt.savefig('fig4.png', dpi=110); plt.show()
```

**Histogram + vertical line**

```python
plt.figure(figsize=(6, 4))
plt.hist(pdist(pos), bins=20, edgecolor='k')
plt.axvline(8, color='r', ls='--', label='cutoff 8')
plt.xlabel('Pair distance'); plt.ylabel('Count'); plt.legend()
plt.tight_layout(); plt.savefig('fig5.png', dpi=110); plt.show()
```

**A quantity per frame**

```python
hh = np.linalg.norm(traj[:, 1] - traj[:, 2], axis=1)
print("max H-H in frame", np.argmax(hh), f"({hh.max():.4f} Å)")
plt.plot(np.arange(len(hh)), hh, 'o-')
plt.xlabel('Frame'); plt.ylabel('H-H distance (Å)'); plt.xticks(np.arange(len(hh)))
plt.tight_layout(); plt.show()
```

### \[PAST\] Parsing + plot solutions: 2021-22 Ex 2 (ORCA ΔE fit) · 2022-23 Ex 5 (HOMO–LUMO gap)

Moved to the worked examples, with the full statements: [EX-PY-03](#ex-py-03) (2022-23 exam, Ex 5 (2 points)) · [EX-PY-04](#ex-py-04) (2021-22 exam, Ex 2 (2 points)).

# 6. The traps list `[GOTCHA]`

**Bash:** no spaces around `=` · spaces inside `[ ]` · quote `"$f"` · `wc -l < f` for a bare number · `grep -c` counts lines, `grep -o … | wc -l` counts occurrences · `-w` for whole words · never `sed … f > f` (use a temp file + `mv`) · `[ -f "$f" ] || continue` when a glob might match nothing · use `-gt`, not `>`, inside `[ ]` · `read` in Colab needs `<<<` **Gnuplot:** `**` for powers · `1/2 = 0` · a new `set output` for each figure · `set table` **before** `set contour` · `with pm3d` alone draws no contour lines (use `pm3d implicit` + `with lines`) · 2D map = `set view map; unset surface` **Fortran:** `implicit none` · `d0` literals · integer division · `real(8), external :: f` · declarations before statements · loop over integers · `trim()` when comparing strings · `iostat` for the end of a file · allocate after reading `nat` **make:** TABs (not spaces) · every object on the link line · `main.o` depends on the module's `.o` · `rm -f *.mod` in `clean` · `make clean && make` before handing in **NumPy:** create `rng` once (re-run from it) · `keepdims=True` when dividing by norms · `fill_diagonal(inf)` before min/closest · `unravel_index` for matrix positions · `clip` before `arccos` · 0-based indices (the .xyz exercise wants 1-based: add 1) **Parsing:** look at the file first · double spaces in the markers (`'Alpha occ.'`) · `enumerate(line.split())` to find the index · numbers that run together → fixed width · `p and p[0].isdigit()` to skip headers · first block vs all blocks **Plots:** `savefig` before `show` · `xticks(steps)` for integer steps · labels with units

# 7. Worked examples `[EX]`

**How to use:** walk down the list for the language until you find the problem closest to yours, read **How it works** to see which lines answer which part, copy the solution, and change what the **Adapt it** box lists. Within each language the order is: past exam questions `[PAST]` → the mock exam → exam-style drills → warm-ups and class homework. Every example has a **Search** line, so Ctrl+F a few words from your question (e.g. `minimum distance`, `swap lines`, `two tables`, `contour`, `Max-DP`).

All solutions were run and checked. The parsing examples read the practice files (`data/orca_mock.out`, `data/piri_mock.log`, …); in the exam, use the real file name (`orca.out`, `piri.log`). Python examples start with the imports they need.

**Check** boxes show what the solution prints when run unchanged (only where the output doesn't depend on a practice file). Run the template once before adapting it: if the numbers match, your paste, TABs and environment are fine, and any later error comes from your changes.

## 7.1 Python \[EX-PY\]

| ID                    | Problem                                                                                                         | From                                                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------|
| [EX-PY-01](#ex-py-01) | Particles in a box: minimum distance, the pair, verify, energy function                                         | 2022-23 exam, Ex 4 (3 points) \[PAST\]                                                                         |
| [EX-PY-02](#ex-py-02) | Vectors: normalise, largest dot product, dot-product matrix of two systems                                      | 2021-22 exam, Ex 1 (3 points) \[PAST\]                                                                         |
| [EX-PY-03](#ex-py-03) | Gaussian log: HOMO–LUMO gap per optimisation step + plot                                                        | 2022-23 exam, Ex 5 (2 points) \[PAST\]                                                                         |
| [EX-PY-04](#ex-py-04) | ORCA output: Delta-E of the first SCF block, fit log10\|ΔE\| with a line, plot                                  | 2021-22 exam, Ex 2 (2 points) \[PAST\]                                                                         |
| [EX-PY-05](#ex-py-05) | Neighbours within a cutoff, particle with most neighbours, Lennard-Jones energy                                 | Mock exam (Day 7), Ex 4                                                                                        |
| [EX-PY-06](#ex-py-06) | ORCA output: Max-DP of the second SCF block when the column order changes (SOSCF)                               | Mock exam (Day 7), Ex 5                                                                                        |
| [EX-PY-07](#ex-py-07) | Particles drill: max distance + pair, pairs closer than X, centre, energy, distance matrix without SciPy        | Week notebook 4.2 (exam-style)                                                                                 |
| [EX-PY-08](#ex-py-08) | Speed drill: masks, sort by distance, minimum pair, mean nearest-neighbour distance                             | Week notebook 4.3 (uses `pos` and `D` from EX-PY-07)                                                           |
| [EX-PY-09](#ex-py-09) | All pairs: max and min pair (NumPy + double loop), neighbours, dot-product matrix, broadcasting                 | Python 4-hour drill, B3 (3.1–3.5) ⭐                                                                            |
| [EX-PY-10](#ex-py-10) | Formulas as functions: harmonic trap, Coulomb pair energy, closest_pair returning a tuple                       | Python 4-hour drill, B4 (4.1–4.3)                                                                              |
| [EX-PY-11](#ex-py-11) | Mini mock: closest opposite-charge pair, dipole moment, atoms within a sphere; H–H distance per frame           | Python 4-hour drill, B7 (M1, M2)                                                                               |
| [EX-PY-12](#ex-py-12) | NumPy foundations: shapes, axes, centres, masks, broadcasting                                                   | Python 4-hour drill, B1 (1.1–1.3)                                                                              |
| [EX-PY-13](#ex-py-13) | Vectors: normalise, angles between vectors, most parallel, cross product                                        | Python 4-hour drill, B2 (2.1–2.3)                                                                              |
| [EX-PY-14](#ex-py-14) | Gaussian log: SCF Done energy per step, relative energy in kcal/mol, plot                                       | Week notebook 5.1                                                                                              |
| [EX-PY-15](#ex-py-15) | ORCA output: SCF iterations and final energy for every optimisation cycle, two-panel figure                     | Week notebook 5.3                                                                                              |
| [EX-PY-16](#ex-py-16) | Reusable reader: one (iterations, ΔE) pair per SCF block, all cycles on one plot                                | Week notebook 5.4                                                                                              |
| [EX-PY-17](#ex-py-17) | Gaussian optimisation log: energies + step numbers, convergence table (YES/NO)                                  | Python 4-hour drill, B5 (5.1, 5.2)                                                                             |
| [EX-PY-18](#ex-py-18) | Block per step: Mulliken charges of every step                                                                  | Python 4-hour drill, B5 (5.3)                                                                                  |
| [EX-PY-19](#ex-py-19) | Fixed-width columns: occupied and virtual orbital energies per step                                             | Python 4-hour drill, B5 (5.4)                                                                                  |
| [EX-PY-20](#ex-py-20) | Multi-frame .xyz trajectory: frames × atoms × xyz, distances and angle per frame                                | Python 4-hour drill, B5 (5.5)                                                                                  |
| [EX-PY-21](#ex-py-21) | Plain numeric table: np.loadtxt, mean/std, linear drift                                                         | Python 4-hour drill, B5 (5.6)                                                                                  |
| [EX-PY-22](#ex-py-22) | matplotlib: five target figures (relative energy, log scale + threshold, two panels, fit in title, histogram)   | Python 4-hour drill, B6 (6.1–6.5)                                                                              |
| [EX-PY-23](#ex-py-23) | Homework (Assignment 1): read a .cif file, distances to Mg, bound atoms, element counts, water check            | Class assignment `20_EI_2026.ipynb` (your submitted solution)                                                  |
| [EX-PY-24](#ex-py-24) | Homework (Assignment 2): Euler integrator — NumPy, Numba, f2py timings, energy drift, velocity Verlet           | Class assignment `22_assignment2.ipynb` + `fortran_utils.f90` / `fortran_utils2.f90` (your submitted solution) |
| [EX-PY-25](#ex-py-25) | Zinc site (.cif): read_cif, distances to Zn, bound atoms + counts, closest bound pair (map back), mean + centre | Mock exam 2 (11 Nov), Ex 4 (2.5 points)                                                                        |
| [EX-PY-26](#ex-py-26) | MD log: four values per block, total energy, linear drift, plot with fit line, mean/max T and its time          | Mock exam 2 (11 Nov), Ex 5 (2 points)                                                                          |

### EX-PY-01 · Particles in a box: minimum distance, the pair, verify, energy function

**From:** 2022-23 exam, Ex 4 (3 points) \[PAST\]  
**Search:** min distance · closest pair · pdist · squareform · fill_diagonal · unravel_index · argmin · total energy

Hand in a notebook or a python script.

**4.1.** (0.5 point) Create an array of 20 particles named `pos` in a box of dimensions (100, 10, 10). If possible, use this random seed:  
`rng = np.random.default_rng(12345)`

**4.2.** (0.5 point) Find the minimum distance between all pairs of particles.

**4.3.** (1 point) Find which particles pair (give particle indices) has the minimum distance that you have found. Prove your result by explicitly calculating the distance between these two particles and printing the result. The function `squareform` in the `scipy.spatial.distance` may be useful, but you do not need to use it.

**4.4.** (1 point) Imagine that all particles are in a field so that the energy of the system is U = Σᵢ zᵢ², where zᵢ is the third component of the particle positions rᵢ = (xᵢ, yᵢ, zᵢ). Create a function that calculates the total energy of the system and use it to print the total energy.

Python

```python
import numpy as np
from scipy.spatial.distance import pdist, squareform
# 4.1
rng = np.random.default_rng(12345)
pos = rng.random((20, 3)) * np.array([100, 10, 10])
# 4.2
D = squareform(pdist(pos))
np.fill_diagonal(D, np.inf)
print(f"minimum distance: {D.min():.4f}")
# 4.3
i, j = np.unravel_index(np.argmin(D), D.shape)
print(f"pair: {i} and {j}")
print(f"check: |r_{i} - r_{j}| = {np.linalg.norm(pos[i] - pos[j]):.4f}")
# 4.4
def total_energy(pos):
    """U = sum_i z_i**2"""
    return np.sum(pos[:, 2]**2)
print(f"U = {total_energy(pos):.4f}")
```

**How it works:**

- **4.1** `rng.random((20, 3))` gives numbers in \[0, 1); multiplying by `np.array([100, 10, 10])` scales each column to its box side (broadcasting).
- **4.2** `pdist` returns each pair's distance once; `squareform` turns that into the full 20×20 matrix `D`. The diagonal is 0 (a particle with itself), so it's set to `inf` before taking the minimum.
- **4.3** `argmin` gives a flat index into `D`; `unravel_index` turns it into (row, column) = the two particle indices. `np.linalg.norm(pos[i] - pos[j])` recomputes that one distance as proof.
- **4.4** `pos[:, 2]` is the z column of every particle; the function squares and sums it.

**Check:** run it unchanged and it prints:

``` plaintext
minimum distance: 3.2736
pair: 1 and 4
check: |r_1 - r_4| = 3.2736
U = 599.2358
```

**Adapt it:**

Max instead of min → no `fill_diagonal`, use `argmax`. Different box → change `np.array([100, 10, 10])`. Different energy formula → only the `return` line (x is `pos[:, 0]`, y `pos[:, 1]`, z `pos[:, 2]`). Pair formula (Σᵢ\<ⱼ) → `pdist(pos)` gives each pair once (see EX-PY-05).

### EX-PY-02 · Vectors: normalise, largest dot product, dot-product matrix of two systems

**From:** 2021-22 exam, Ex 1 (3 points) \[PAST\]  
**Search:** normalise · unit vectors · norm axis keepdims · dot product · argmax · prods matrix · row with value \> 1 · any(axis=1)

Create an array of vectors for 5 systems with 7 particles in xyz coordinates in the following way:  
`rng = np.random.default_rng(12345)`  
`vectors = rng.random(size=(5,7,3))`

1.  Create a new array with these vectors normalized, i.e. all its (x,y,z) vectors should have a module of 1. Print the components of vector (2,4) with 3 decimals. You can use `np.linalg.norm`.
2.  Give the index of the vector of system 1 that has a larger dot product with vector 3 of system 0.
3.  For system 3 and 4 calculate the dot product of all pairs of vectors (a total of 7x7=49). Call this matrix `prods`. If there is any row of this matrix which has a value larger than 1, print the first element of this row.

Python

```python
import numpy as np
rng = np.random.default_rng(12345)
vectors = rng.random(size=(5,7,3))

# 1. normalise every xyz vector
norms = np.linalg.norm(vectors, axis=2, keepdims=True)   # shape (5,7,1)
unit = vectors / norms
assert np.allclose(np.linalg.norm(unit, axis=2), 1)
print("1) unit[2,4] =", ", ".join(f"{c:.3f}" for c in unit[2,4]))

# 2. vector of system 1 with the largest dot product with vector 3 of system 0
dots = vectors[1] @ vectors[0,3]            # 7 numbers
print("2) dots:", np.round(dots, 3), "-> index", np.argmax(dots))
print("   (with normalised vectors:", np.argmax(unit[1] @ unit[0,3]), ")")

# 3. 7x7 matrix of dot products, prods[i,j] = v(3,i) . v(4,j)
prods = vectors[3] @ vectors[4].T
rows = np.where((prods > 1).any(axis=1))[0]
print("3) rows with a value > 1:", rows)
for r in rows:
    print(f"   row {r}: first element = {prods[r,0]:.3f}")
print("   normalised max:", (unit[3] @ unit[4].T).max().round(3), "(never > 1, so the question means the raw vectors)")
```

**How it works:**

- **1** `np.linalg.norm(..., axis=2, keepdims=True)` gives the length of each xyz vector with shape (5,7,1), so dividing broadcasts over x, y, z. Vector (2,4) = system 2, particle 4 (0-based).
- **2** `vectors[1] @ vectors[0, 3]` is the dot product of all 7 vectors of system 1 with one vector; `argmax` picks the largest.
- **3** `vectors[3] @ vectors[4].T` gives the 7×7 matrix of every pair. `(prods > 1).any(axis=1)` marks rows with at least one value \> 1; the loop prints the first element of each. (Unit vectors can never give \> 1, so the question means the raw vectors.)

**Check:** run it unchanged and it prints:

``` plaintext
1) unit[2,4] = 0.547, 0.792, 0.270
2) dots: [0.676 0.637 0.713 1.839 1.778 1.217 1.001] -> index 3
   (with normalised vectors: 3 )
3) rows with a value > 1: [3 5]
   row 3: first element = 0.685
   row 5: first element = 0.792
   normalised max: 0.987 (never > 1, so the question means the raw vectors)
```

**Adapt it:**

“Vector (i, j)” = `a[i, j]` (0-based). Largest/smallest → `argmax`/`argmin`. Matrix of all pairs between two systems → `A @ B.T`. “Rows with a value \> X” → `np.where((M > X).any(axis=1))[0]`; “rows whose max \> X” → `M.max(axis=1) > X`. Say which array (raw or normalised) you used.

### EX-PY-03 · Gaussian log: HOMO–LUMO gap per optimisation step + plot

**From:** 2022-23 exam, Ex 5 (2 points) \[PAST\] · the practice file is data/piri_mock.log  
**Search:** HOMO LUMO gap · Alpha occ. eigenvalues · Alpha virt. eigenvalues · fixed width · read_values · line\[28:\] · optimisation step · o-- plot

Hand in a notebook or a python script.

The Gaussian output `piri.log` is an optimization run. For each optimization step, read the energy of the HOMO and the LUMO orbitals (look for *Alpha occ. eigenvalues* and *Alpha virt. eigenvalues*) and keep them in a container (for example a list or an array). Then plot the difference between the HOMO and LUMO in a plot similar to this one:

*Target plot:* black points joined by a black dashed line; x axis `Optimization step` with integer ticks 0–4; y axis `Energy gap (ha)`; values ≈ 0.211, 0.224, 0.089, 0.096, 0.094.

Python

```python
import numpy as np
import matplotlib.pyplot as plt
def read_values(line):
    data = line[28:].rstrip('\n')                     # numbers start after the 28-character label
    return [float(data[k:k+10]) for k in range(0, len(data), 10)]
occ, virt = [], []                                    # one list per optimisation step
new_step = True
with open('data/piri_mock.log') as f:                 # paper: 'piri.log'
    for line in f:
        if 'Alpha  occ. eigenvalues' in line:
            if new_step:                              # first occ line of a new step
                occ.append([]); virt.append([])
                new_step = False
            occ[-1] += read_values(line)
        elif 'Alpha virt. eigenvalues' in line:
            virt[-1] += read_values(line)
            new_step = True                           # the next occ line starts a new step
homo = np.array([o[-1] for o in occ])                 # last occupied of each step
lumo = np.array([v[0] for v in virt])                 # first virtual of each step
gap = lumo - homo
print(gap)
steps = np.arange(len(gap))
plt.plot(steps, gap, 'o--', color='k')
plt.xlabel('Optimization step'); plt.ylabel('Energy gap (ha)'); plt.xticks(steps)
plt.show()
```

**How it works:**

- **`read_values`** cuts the numbers out of the line 10 characters at a time after the 28-character label, because Gaussian prints them in fixed columns that can touch (`-10.12345-10.23456`).
- **One list per step:** the first `Alpha occ.` line after a `virt.` line starts a new step (`new_step`); all occ/virt lines of that step are added to the current list.
- **HOMO** = last value of the occupied list (`o[-1]`), **LUMO** = first virtual (`v[0]`); the gap is LUMO − HOMO, one per step.
- **Plot:** `'o--'` with `color='k'` = black points joined by a dashed line; `xticks(steps)` puts integer ticks.

**Adapt it:**

Eigenvalues are printed in **fixed 10-character columns** and can run together (`-100.12345-10.23456`), so slice instead of `split()`. Another marker → change the strings in the `if`s (keep the double spaces: `'Alpha occ.'`). Gap in eV → multiply by 27.2114. Beta orbitals → `'Beta occ.'` / `'Beta virt.'`.

### EX-PY-04 · ORCA output: Delta-E of the first SCF block, fit log10\|ΔE\| with a line, plot

**From:** 2021-22 exam, Ex 2 (2 points) \[PAST\] · the practice file is data/orca_mock.out  
**Search:** SCF ITERATIONS · Delta-E · state machine · inside flag · isdigit · polyfit · log10 · fit line dashed · SOSCF header

Read the Delta-E column of the SCF ITERATIONS of the first point of the `orca.out` file. Do a linear fit from iteration 5 to the last iteration (14) of log10(abs(deltaE)). Print the slope and plot the linear fit and the deltaE values (with points). The output should look approximately like this:

*Target plot:* blue points, an orange dashed fit line, x axis `Iteration` (5–14), y axis `log10(|ΔE|)` (about −3 to −8).

Remark that you can choose to read all delta-E values and discard the first 5 or read the values starting from iteration 5. I will accept both solutions as correct.

Python

```python
import numpy as np
import matplotlib.pyplot as plt
it, dE = [], []
inside = False
with open('data/orca_mock.out') as f:
    for line in f:
        if 'SCF ITERATIONS' in line:
            inside = True
            continue
        if inside:
            p = line.split()
            if p and p[0].isdigit():          # data rows start with the iteration number
                it.append(int(p[0]))
                dE.append(float(p[2]))        # Delta-E is the 3rd column in BOTH header styles
            elif 'Energy Check' in line:      # end of the FIRST block -> stop reading
                break
it, dE = np.array(it), np.array(dE)

m = it >= 5
x, y = it[m], np.log10(np.abs(dE[m]))
slope, intercept = np.polyfit(x, y, 1)
print(f"slope = {slope:.4f}")

plt.plot(x, y, 'o')
plt.plot(x, slope*x + intercept, '--')
plt.xlabel('Iteration'); plt.ylabel('log10(|ΔE|)')
plt.show()
```

**How it works:**

- **Reading:** `inside` turns on at the first `SCF ITERATIONS`; only lines whose first word is a number are data rows, so headers and blank lines are skipped. `Energy Check` ends the first block → `break`.
- **ΔE** is word 2 of each data row (ITER = 0, Energy = 1, Delta-E = 2).
- **Fit:** the mask `it >= 5` keeps iterations 5–14; `np.polyfit(x, y, 1)` returns slope and intercept of the straight line through log10\|ΔE\|.
- **Plot:** the points with `'o'`, the fit line `slope*x + intercept` with `'--'`.

**Adapt it:**

Iteration 0 has Delta-E = 0, and log10(0) = -inf, so it would break the fit. The early iterations are also not in the linear convergence regime.  
The parser survives the SOSCF header change because it only keeps lines whose first token is a number.

Another column → change `p[2]` (count the header words: ITER=0, Energy=1, Delta-E=2…), but check the header after SOSCF (see EX-PY-06). All blocks instead of the first → see EX-PY-16. Fit range → change `it >= 5`.

### EX-PY-05 · Neighbours within a cutoff, particle with most neighbours, Lennard-Jones energy

**From:** Mock exam (Day 7), Ex 4 · solution written and tested for these notes  
**Search:** neighbours · cutoff · count per particle · sum(axis=1) · most neighbours · Lennard-Jones · lj_energy · pdist pairs i\<j

4.1 (0.5) `rng = np.random.default_rng(7)`. Create `pos`: 50 particles in a box (20, 20, 20).  
4.2 (1) For each particle, count its neighbours closer than 5 (excluding itself). Print the array.  
4.3 (0.5) Which particle has the most neighbours? Print its index and the indices of its neighbours.  
4.4 (1) Write a function `lj_energy(pos, eps=1.0, sigma=1.0)` returning U = Σᵢ\<ⱼ 4ε\[(σ/r)¹² − (σ/r)⁶\], and print the total.

Python

```python
import numpy as np
from scipy.spatial.distance import pdist, squareform

# 4.1
rng = np.random.default_rng(7)
pos = rng.random((50, 3)) * np.array([20, 20, 20])

# 4.2 neighbours closer than 5, not counting itself
D = squareform(pdist(pos))
np.fill_diagonal(D, np.inf)              # a particle is not its own neighbour
nb = (D < 5).sum(axis=1)
print("neighbours:", nb)

# 4.3 particle with the most neighbours
k = np.argmax(nb)
print("most neighbours:", k, "->", np.where(D[k] < 5)[0])

# 4.4 Lennard-Jones, every pair i<j once (pdist gives exactly that)
def lj_energy(pos, eps=1.0, sigma=1.0):
    """U = sum_{i<j} 4 eps [(sigma/r)^12 - (sigma/r)^6]"""
    r = pdist(pos)
    sr6 = (sigma / r)**6
    return np.sum(4 * eps * (sr6**2 - sr6))
print(f"U_LJ = {lj_energy(pos):.6f}")
print("test, one pair at r = 2^(1/6):", lj_energy(np.array([[0, 0, 0], [2**(1/6), 0, 0]])))   # -1
```

**How it works:**

- **4.2** Full distance matrix, `inf` on the diagonal so a particle isn't its own neighbour; `(D < 5)` is a True/False matrix and `.sum(axis=1)` counts the True values in each row.
- **4.3** `argmax` of the counts = the particle with most neighbours; `np.where(D[k] < 5)[0]` lists them.
- **4.4** `pdist` already gives each pair i\<j once, so the Lennard-Jones sum is just a formula applied to that array. The 2-particle test at r = 2^(1/6)σ must give −ε = −1.

**Check:** run it unchanged and it prints:

``` plaintext
neighbours: [3 4 3 2 2 3 1 1 1 1 1 2 2 4 7 1 3 2 5 4 1 3 3 2 1 0 3 1 1 3 1 1 1 3 4 3 4
 0 2 4 3 1 3 5 4 4 5 3 0 3]
most neighbours: 14 -> [ 0  5 19 20 43 45 46]
U_LJ = -0.873632
test, one pair at r = 2^(1/6): -1.0
```

**Adapt it:**

Neighbour counting: put `inf` on the diagonal first, so a particle isn’t its own neighbour. Any **pair** sum Σᵢ\<ⱼ f(rᵢⱼ) → `r = pdist(pos)` then `np.sum(f(r))`; pair sums with charges → `i, j = np.triu_indices(N, k=1)` (same order as `pdist`). Test a pair formula on 2 particles you can do by hand.

### EX-PY-06 · ORCA output: Max-DP of the second SCF block when the column order changes (SOSCF)

**From:** Mock exam (Day 7), Ex 5 · solution written and tested for these notes  
**Search:** Max-DP · second block · block counter · header changes · SOSCF · index of column from header · o-- plot

From `data/orca_mock.out`, read the **Max-DP** values of every iteration of the **second** SCF block. Careful: the column order changes when SOSCF starts, so check the header lines. Plot log10(Max-DP) against iteration, with points joined by a dashed line, and print the iteration with the largest Max-DP.

Python

```python
import numpy as np
import matplotlib.pyplot as plt

it, maxdp = [], []
block = 0
col = None
with open('data/orca_mock.out') as f:
    for line in f:
        if 'SCF ITERATIONS' in line:
            block += 1                        # 1st, 2nd, 3rd SCF block ...
            continue
        if block != 2:
            continue                          # only the SECOND block
        if line.startswith('ITER'):           # a header: find where Max-DP is NOW
            col = line.split().index('Max-DP')    # 3 before SOSCF, 5 after
            continue
        p = line.split()
        if p and p[0].isdigit():
            it.append(int(p[0]))
            maxdp.append(float(p[col]))
        elif 'Energy Check' in line:
            break                             # end of the second block
it, maxdp = np.array(it), np.array(maxdp)
print(np.column_stack([it, maxdp]))
print("largest Max-DP at iteration", it[np.argmax(maxdp)])

plt.plot(it, np.log10(maxdp), 'o--')
plt.xlabel('Iteration'); plt.ylabel('log10(Max-DP)'); plt.xticks(it)
plt.show()
```

**How it works:**

- **Block counter:** every `SCF ITERATIONS` adds 1; lines are ignored unless `block == 2`.
- **Column from the header:** each `ITER` header line is split and `.index('Max-DP')` finds where Max-DP is *now* (3 before SOSCF, 5 after).
- **Data rows** start with a number; `Energy Check` ends the block.
- **Plot** log10 of Max-DP against the iteration; `argmax` gives the iteration with the largest value.

**Adapt it:**

The trick: read the column position **from each header line** (`line.split().index('Max-DP')`), because after SOSCF the header gains `Grad` and `Rot` and Max-DP moves from column 3 to 5. Another block → change `block != 2`. Another column → change `'Max-DP'` (e.g. `'RMS-DP'`).

### EX-PY-07 · Particles drill: max distance + pair, pairs closer than X, centre, energy, distance matrix without SciPy

**From:** Week notebook 4.2 (exam-style)  
**Search:** max distance · pairs closer than · geometric centre · closest to centre · energy function · broadcasting distance matrix

a\) `rng = np.random.default_rng(2024)`. Create `pos`: 30 particles in a box of size (50, 50, 5).  
b) Find the **maximum** distance between any pair and the indices of that pair. Verify it by computing `np.linalg.norm(pos[i]-pos[j])` explicitly.  
c) How many pairs are closer than 10?  
d) Find the geometric centre and the index of the particle closest to it.  
e) Write a function `energy(pos, k=0.5, z0=2.5)` returning U = Σᵢ k(zᵢ − z₀)², and print U.  
f) Build the distance matrix **without SciPy** using broadcasting (`pos[:,None,:] - pos[None,:,:]`) and check that it matches `squareform(pdist(pos))`.

Python

```python
import numpy as np
from scipy.spatial.distance import pdist, squareform
rng = np.random.default_rng(2024)
box = np.array([50, 50, 5])
pos = rng.random((30, 3)) * box                       # a)

D = squareform(pdist(pos))                            # b)
i, j = np.unravel_index(np.argmax(D), D.shape)
print(f"b) max distance {D.max():.3f} between {i} and {j}; check: {np.linalg.norm(pos[i]-pos[j]):.3f}")

print("c) pairs closer than 10:", np.sum(pdist(pos) < 10))   # pdist = each pair once

center = pos.mean(axis=0)                             # d)
k = np.argmin(np.linalg.norm(pos - center, axis=1))
print("d) centre", np.round(center, 2), "closest particle", k)

def energy(pos, k=0.5, z0=2.5):                       # e)
    return np.sum(k * (pos[:, 2] - z0)**2)
print(f"e) U = {energy(pos):.4f}")

diff = pos[:, None, :] - pos[None, :, :]              # f) shape (30,30,3)
D2 = np.sqrt((diff**2).sum(axis=-1))
print("f) same as scipy:", np.allclose(D, D2))
```

**How it works:**

- **a)** random positions scaled by the box (50, 50, 5).
- **b)** max of the full matrix (no `inf` needed for a maximum); `unravel_index` → the pair; `norm` checks it.
- **c)** `pdist(pos) < 10` is True for each close pair (each pair once); `np.sum` counts them.
- **d)** centre = mean of each column; distance of every particle to it; `argmin` → the closest.
- **e)** the formula on the z column; defaults `k=0.5, z0=2.5` in the function signature.
- **f)** `pos[:, None, :] - pos[None, :, :]` has shape (30, 30, 3): every difference vector. Square, sum over xyz, square root = the distance matrix without SciPy.

**Check:** run it unchanged and it prints:

``` plaintext
b) max distance 57.276 between 21 and 25; check: 57.276
c) pairs closer than 10: 50
d) centre [21.09 20.49  2.23] closest particle 27
e) U = 31.4120
f) same as scipy: True
```

**Adapt it:**

Counting pairs → `np.sum(pdist(pos) < X)` (each pair once). Centre of mass instead of geometric centre → `(m[:, None]*pos).sum(axis=0)/m.sum()`.

### EX-PY-08 · Speed drill: masks, sort by distance, minimum pair, mean nearest-neighbour distance

**From:** Week notebook 4.3 (uses `pos` and `D` from EX-PY-07)  
**Search:** mask & · argsort · nearest neighbour · min(axis=1) · fill_diagonal inf

\(i\) indices of particles with x \> 25 **and** z \< 2 · (ii) particle indices sorted by distance from the origin · (iii) the **minimum** distance and its pair (careful with the diagonal!) · (iv) the mean nearest-neighbour distance.

Python

```python
import numpy as np
print("(i)  ", np.where((pos[:,0] > 25) & (pos[:,2] < 2))[0])
print("(ii) ", np.argsort(np.linalg.norm(pos, axis=1)))
Dn = D.copy(); np.fill_diagonal(Dn, np.inf)          # otherwise the minimum is 0 (i with itself)
a, b = np.unravel_index(np.argmin(Dn), Dn.shape)
print(f"(iii) min distance {Dn.min():.3f} between {a} and {b}")
print(f"(iv) mean nearest-neighbour distance {Dn.min(axis=1).mean():.3f}")
```

**How it works:**

- **(i)** two conditions joined with `&`, each in brackets; `np.where(...)[0]` gives the indices.
- **(ii)** `argsort` of the distances to the origin = particle indices from nearest to farthest.
- **(iii)** copy `D`, put `inf` on the diagonal, then argmin/unravel_index as usual.
- **(iv)** `Dn.min(axis=1)` is each particle's nearest-neighbour distance; `.mean()` averages them.

**Check:** run it unchanged (after EX-PY-07) and it prints:

``` plaintext
(i)   [ 0  1  6 14 21 22 29]
(ii)  [ 2 16 23 13  8  9 19 17 28 24  7 10 27  4 26  3 18  6  0 29 25 20 22 11
 15 21 14 12  5  1]
(iii) min distance 0.742 between 5 and 12
(iv) mean nearest-neighbour distance 5.623
```

**Adapt it:**

Two conditions → `(cond1) & (cond2)` with brackets. Any “minimum over pairs” → `inf` on the diagonal first.

### EX-PY-09 · All pairs: max and min pair (NumPy + double loop), neighbours, dot-product matrix, broadcasting

**From:** Python 4-hour drill, B3 (3.1–3.5) ⭐  
**Search:** max pair · min pair · double loop · neighbours \< 8 · prods · row max \> 1.5 · broadcasting distance matrix

**3.1** `rng = np.random.default_rng(99)`; `pos` = 25 particles in a box of (40, 40, 10). Find the **maximum** distance, the pair involved, and verify it. *Expected:* 46.7687, between 11 and 24.

**3.2** Find the **minimum** distance and its pair with NumPy, then again with the double loop, and check that the two agree. *Expected:* 1.4463, between 2 and 23.

**3.3** Neighbours:

- how many pairs are closer than 8?
- the number of neighbours (\< 8) of each particle, as an array;
- the particle with the most neighbours, and the indices of those neighbours.

*Expected:* 26 pairs · particle 2 → \[4 8 21 22 23\].

**3.4** Using `v` from B1, compute the dot products of every vector of system 1 with every vector of system 3, as a 10×10 matrix `prods`. Print the largest value and its (i, j) position. Then, for each row whose maximum is \> 1.5, print the row number and the first element of that row.

*Expected:* 1.9569 at (9, 4) · rows 1, 2, 8, 9.

**3.5** Build the distance matrix of `pos` **without SciPy** (broadcasting) and check that it equals `squareform(pdist(pos))`.

*(3.4 uses `v` from EX-PY-12.)*

Python

```python
import numpy as np
# 3.1
from scipy.spatial.distance import pdist, squareform
rng = np.random.default_rng(99)
pos = rng.random((25, 3)) * np.array([40, 40, 10])
D = squareform(pdist(pos))
i, j = np.unravel_index(np.argmax(D), D.shape)
print(f"max {D[i, j]:.4f} between {i} and {j}; check {np.linalg.norm(pos[i] - pos[j]):.4f}")

# 3.2
Dm = D.copy(); np.fill_diagonal(Dm, np.inf)
i, j = np.unravel_index(np.argmin(Dm), Dm.shape)
print(f"min {Dm[i, j]:.4f} between {i} and {j}; check {np.linalg.norm(pos[i] - pos[j]):.4f}")
best = np.inf
for a in range(len(pos)):
    for b in range(a + 1, len(pos)):
        d = np.linalg.norm(pos[a] - pos[b])
        if d < best:
            best, ba, bb = d, a, b
print("loop:", round(best, 4), ba, bb)

# 3.3
print("pairs < 8:", (pdist(pos) < 8).sum())
nb = (Dm < 8).sum(axis=1)                        # inf on the diagonal -> itself not counted
print(nb)
k = np.argmax(nb)
print("most neighbours:", k, "->", np.where(Dm[k] < 8)[0])

# 3.4
prods = v[1] @ v[3].T                            # (10,10)
i, j = np.unravel_index(np.argmax(prods), prods.shape)
print("largest dot product", prods[i, j].round(4), "at", (int(i), int(j)))
for r in np.where(prods.max(axis=1) > 1.5)[0]:
    print(f"row {r}: first element {prods[r, 0]:.4f}")

# 3.5
diff = pos[:, None, :] - pos[None, :, :]         # (25,25,3)
D2 = np.sqrt((diff**2).sum(axis=-1))
print(np.allclose(D, D2))
```

**How it works:**

- **3.2** NumPy way: `inf` diagonal + `argmin` + `unravel_index`. Loop way: two loops with the inner one starting at `a + 1`, so each pair is tested once, keeping the best so far. Both must agree.
- **3.3** `pdist < 8` counts pairs; the boolean matrix summed along rows counts neighbours per particle.
- **3.4** `A @ B.T` = all dot products between two systems; the row condition uses `.max(axis=1)`.
- **3.5** broadcasting difference (N, N, 3) → distance matrix; `np.allclose` compares it with SciPy's.

**Check:** run it unchanged (after EX-PY-12) and it prints:

``` plaintext
max 46.7687 between 11 and 24; check 46.7687
min 1.4463 between 2 and 23; check 1.4463
loop: 1.4463 2 23
pairs < 8: 26
[4 0 5 0 3 0 1 3 5 5 1 1 2 0 4 2 4 1 0 0 1 3 3 4 0]
most neighbours: 2 -> [ 4  8 21 22 23]
largest dot product 1.9569 at (9, 4)
row 1: first element 0.8570
row 2: first element 0.9236
row 8: first element 0.9084
row 9: first element 0.9229
True
```

**Adapt it:**

3.3: with `inf` on the diagonal, `(Dm < 8).sum(axis=1)` doesn't count each particle as its own neighbour.

### EX-PY-10 · Formulas as functions: harmonic trap, Coulomb pair energy, closest_pair returning a tuple

**From:** Python 4-hour drill, B4 (4.1–4.3) · uses `pos` from EX-PY-09  
**Search:** function with defaults · trap energy · Coulomb · charges alternating · triu_indices · return tuple

**4.1** Write `trap_energy(pos, k=0.5, centre=(20.0, 20.0))` returning U = ½k Σᵢ\[(xᵢ−cx)² + (yᵢ−cy)²\]. Test it on a case you can do by hand first: \[\[21, 20, 0\], \[20, 22, 5\]\] gives 1.25. Then evaluate it on `pos`. *Expected:* U = 1362.163.

**4.2** Write `coulomb(pos, q)` returning U = Σᵢ\<ⱼ qᵢqⱼ / rᵢⱼ, with the charges alternating +1, −1, +1, … (particle 0 is +1). Test: two charges +1 and −1 at distance 2 give −0.5. Hint: `pdist` + `np.triu_indices`. *Expected:* U = −1.7846.

**4.3** Write `closest_pair(pos)`, which **returns** a tuple (distance, i, j), and unpack it: `d, i, j = closest_pair(pos)`.

Python

```python
import numpy as np
from scipy.spatial.distance import pdist, squareform
# 4.1
def trap_energy(pos, k=0.5, centre=(20.0, 20.0)):
    """U = 1/2 k sum[(x-cx)^2 + (y-cy)^2]"""
    dx = pos[:, 0] - centre[0]
    dy = pos[:, 1] - centre[1]
    return 0.5 * k * np.sum(dx**2 + dy**2)
print(trap_energy(np.array([[21.0, 20.0, 0.0], [20.0, 22.0, 5.0]])))   # 0.25*(1+4) = 1.25
print(f"U = {trap_energy(pos):.3f}")

# 4.2
def coulomb(pos, q):
    """U = sum_{i<j} q_i q_j / r_ij"""
    i, j = np.triu_indices(len(pos), k=1)        # same pair order as pdist
    return np.sum(q[i] * q[j] / pdist(pos))
q = np.array([1 if n % 2 == 0 else -1 for n in range(len(pos))])
print(coulomb(np.array([[0.0, 0, 0], [2.0, 0, 0]]), np.array([1, -1])))   # -0.5
print(f"U = {coulomb(pos, q):.4f}")

# 4.3
def closest_pair(pos):
    D = squareform(pdist(pos))
    np.fill_diagonal(D, np.inf)
    i, j = np.unravel_index(np.argmin(D), D.shape)
    return D[i, j], i, j
d, i, j = closest_pair(pos)
print(f"{d:.4f} {i} {j}")
```

**How it works:**

- **4.1** default arguments in the signature; the formula uses columns `pos[:, 0]` and `pos[:, 1]` minus the centre.
- **4.2** `np.triu_indices(N, k=1)` lists every pair i\<j once (same order as `pdist`), so `q[i]*q[j]/r` sums the Coulomb energy over pairs.
- **4.3** the function **returns** a tuple `(d, i, j)`, and the caller unpacks it: `d, i, j = closest_pair(pos)`.

**Check:** run it unchanged (after EX-PY-09) and it prints:

``` plaintext
1.25
U = 1362.163
-0.5
U = -1.7846
1.4463 2 23
```

**Adapt it:**

Always test the formula on a 2-particle case you can do by hand. Charges ±1 alternating → `np.where(np.arange(N) % 2 == 0, 1, -1)` also works.

### EX-PY-11 · Mini mock: closest opposite-charge pair, dipole moment, atoms within a sphere; H–H distance per frame

**From:** Python 4-hour drill, B7 (M1, M2) · M2 uses `traj` from EX-PY-20  
**Search:** opposite charges · np.outer · dipole · sum q r · within radius · H-H distance per frame

**M1 (3 pts).** `rng = np.random.default_rng(31415)`. Create `coords` = 15 atoms in a cube of side 12, **then** `qs = rng.choice([-1, 1], size=15)` (in that order).

- (1 pt) Find the closest pair of atoms with **opposite** charges: their indices, charges and distance, and verify the distance.
- (1 pt) Write a function `dipole(coords, q)` returning μ = Σ qᵢ·rᵢ, and print μ and \|μ\|.
- (1 pt) How many atoms lie within 5 of the centre of the cube, (6, 6, 6)?

**M2 (2 pts).** Using the trajectory from 5.5, compute the H–H distance in each frame, print the frame with the largest one, and plot H–H distance against frame (`'o-'`, labelled axes, integer ticks).

*Expected values are in the answer key, so check only after you finish.*

M1

```python
import numpy as np
import matplotlib.pyplot as plt
from scipy.spatial.distance import pdist, squareform
# M1
rng = np.random.default_rng(31415)
coords = rng.random((15, 3)) * 12
qs = rng.choice([-1, 1], size=15)
D = squareform(pdist(coords))
opp = np.outer(qs, qs) < 0                       # True where the charges are opposite
D[~opp] = np.inf                                 # also removes the diagonal (q*q = +1)
i, j = np.unravel_index(np.argmin(D), D.shape)
print(f"closest opposite pair {i} ({qs[i]:+d}) - {j} ({qs[j]:+d}): {D[i, j]:.4f}; check {np.linalg.norm(coords[i]-coords[j]):.4f}")
def dipole(coords, q):
    return (q[:, None] * coords).sum(axis=0)
mu = dipole(coords, qs)
print("dipole", mu.round(4), f"|mu| = {np.linalg.norm(mu):.4f}")
print("within 5 of the centre:", (np.linalg.norm(coords - 6, axis=1) < 5).sum())
```

M2

```python
# M2
hh = np.linalg.norm(traj[:, 1] - traj[:, 2], axis=1)
print("max H-H in frame", np.argmax(hh), f"({hh.max():.4f} Å)")
plt.plot(np.arange(len(hh)), hh, 'o-')
plt.xlabel('Frame'); plt.ylabel('H-H distance (Å)'); plt.xticks(np.arange(len(hh)))
plt.tight_layout(); plt.show()
```

**How it works:**

- **M1 closest opposite pair:** `np.outer(qs, qs)` is the matrix of qᵢqⱼ; it's negative exactly where the charges are opposite. Every other entry of `D` (including the diagonal, where q² = +1) becomes `inf`, so the minimum is the closest opposite pair.
- **M1 dipole:** `q[:, None] * coords` multiplies each row by its charge; summing over atoms gives μ.
- **M1 within a sphere:** distance of each atom to (6, 6, 6) (`coords - 6` broadcasts), then count `< 5`.
- **M2:** H–H distance in every frame at once: `traj[:, 1] - traj[:, 2]` has one row per frame.

**Check:** run it unchanged (M1 only) and it prints:

``` plaintext
closest opposite pair 6 (+1) - 7 (-1): 1.6065; check 1.6065
dipole [ -7.1907   5.3121 -28.2591] |mu| = 29.6395
within 5 of the centre: 1
```

**Adapt it:**

**Expected:** M1 → closest opposite pair 6 (+1) and 7 (−1), 1.6065 · μ = \[−7.1907, 5.3121, −28.2591\], \|μ\| = 29.6395 · 1 atom within 5 of the centre. M2 → the maximum H–H distance is in frame 0 (1.5484 Å). **Trick in M1:** `np.outer(qs, qs) < 0` is True only for opposite-charge pairs, and setting the rest to `inf` also takes care of the diagonal.

### EX-PY-12 · NumPy foundations: shapes, axes, centres, masks, broadcasting

**From:** Python 4-hour drill, B1 (1.1–1.3)  
**Search:** shape · mean axis · norm axis · unravel_index 3D · mask · all(axis=2) · clip · keepdims · scale box

**1.1** `rng = np.random.default_rng(2025)`; `v = rng.random((4, 10, 3))` (4 systems × 10 particles × xyz). Print:

- the shape of `v`;
- the centre of each system (shape (4, 3));
- the length of every vector (shape (4, 10));
- the largest value in `v` and its (system, particle, coordinate) index.

*Expected:* centre of system 0 = \[0.449 0.513 0.559\]; largest value 0.9945 at (0, 0, 0).

**1.2** Masks:

- the indices of the particles in system 0 with z \> 0.5;
- for each system, how many particles have **all three** coordinates \< 0.5;
- a copy of `v` in which every value \> 0.9 is replaced by 0.9.

*Expected:* \[0 2 4 7 9\] · \[4 1 1 1\] · max of the copy = 0.9.

**1.3** Broadcasting:

- shift every system so that its centre is at the origin, and verify that all the centres are now ≈ 0;
- scale `v` into a box of (20, 20, 5) and print the largest x, y and z.

*Expected:* True · \[19.89 19.8 4.94\].

Python

```python
# 1.1
import numpy as np
rng = np.random.default_rng(2025)
v = rng.random((4, 10, 3))
print(v.shape)                                   # (4, 10, 3)
centres = v.mean(axis=1)                         # average over particles -> (4, 3)
print(centres.shape); print(np.round(centres, 3))
lengths = np.linalg.norm(v, axis=2)              # one number per particle -> (4, 10)
print(lengths.shape)
idx = np.unravel_index(np.argmax(v), v.shape)    # (system, particle, coordinate)
print("largest value", v[idx].round(4), "at", tuple(int(k) for k in idx))

# 1.2
print(np.where(v[0, :, 2] > 0.5)[0])             # system 0, particles with z > 0.5
print((v < 0.5).all(axis=2).sum(axis=1))         # per system: particles with x,y,z all < 0.5
w = v.copy()
w[w > 0.9] = 0.9                                 # or: np.clip(v, None, 0.9)
print(w.max())

# 1.3
centred = v - v.mean(axis=1, keepdims=True)      # (4,10,3) - (4,1,3)
print(np.abs(centred.mean(axis=1)).max() < 1e-12)
box = v * np.array([20, 20, 5])                  # scale each coordinate
print(box.max(axis=(0, 1)).round(2))             # largest x, y, z
```

**How it works:**

- **1.1** `mean(axis=1)` averages over the particle axis → one centre per system; `norm(axis=2)` → one length per vector; `argmax` + `unravel_index` with the 3D shape gives (system, particle, coordinate).
- **1.2** a mask selects values; `.all(axis=2)` asks “all three coordinates”; `np.clip` caps values.
- **1.3** subtract the centres with `keepdims=True` (shape (4, 1, 3)) so they broadcast over the 10 particles; multiply by the box to rescale.

**Check:** run it unchanged and it prints:

``` plaintext
(4, 10, 3)
(4, 3)
[[0.449 0.513 0.559]
 [0.663 0.662 0.59 ]
 [0.374 0.574 0.588]
 [0.463 0.601 0.385]]
(4, 10)
largest value 0.9945 at (0, 0, 0)
[0 2 4 7 9]
[4 1 1 1]
0.9
True
[19.89 19.8   4.94]
```

**Adapt it:**

Rule: the axis you name is the one that disappears. `(4,10,3)` → `mean(axis=1)` → `(4,3)`.

### EX-PY-13 · Vectors: normalise, angles between vectors, most parallel, cross product

**From:** Python 4-hour drill, B2 (2.1–2.3) · uses `v` from EX-PY-12  
**Search:** normalise · angle · arccos clip · degrees · most parallel · cross product · perpendicular

**2.1** Normalise all the vectors of `v`, check that they all have length 1, and print vector (3, 7) with 3 decimals. *Expected:* 0.439, 0.713, 0.547.

**2.2** In system 2, compute the angle (in degrees) between vector 0 and every vector, then find the **most parallel** to vector 0, excluding vector 0 itself. *Expected:* vector 7, at 22.3°.

**2.3** Compute the cross product of vectors 0 and 1 of system 0, and show that it is perpendicular to both (the dot products are ≈ 0). *Expected:* \[-0.7776 0.6157 0.6506\].

Python

```python
import numpy as np
# 2.1
unit = v / np.linalg.norm(v, axis=2, keepdims=True)
print(np.allclose(np.linalg.norm(unit, axis=2), 1))
print(", ".join(f"{c:.3f}" for c in unit[3, 7]))

# 2.2
u = unit[2]                                      # system 2, already normalised
cosang = np.clip(u @ u[0], -1, 1)                # 10 cosines (clip guards against 1.0000000002)
ang = np.degrees(np.arccos(cosang))
print(ang.round(1))
ang[0] = np.inf                                  # exclude vector 0 itself
print("most parallel to vector 0:", np.argmin(ang), f"({ang.min():.1f} deg)")

# 2.3
c = np.cross(v[0, 0], v[0, 1])
print(c.round(4), np.dot(c, v[0, 0]).round(12), np.dot(c, v[0, 1]).round(12))
```

**How it works:**

- **2.1** divide by the lengths with `keepdims=True`; check all lengths are 1 with `np.allclose`.
- **2.2** cos θ = (a·b)/(\|a\|\|b\|); `np.clip` to \[−1, 1\] protects `arccos` from rounding; `np.degrees` converts. The most parallel vector has the smallest angle (skip vector 0 itself).
- **2.3** `np.cross` gives a vector perpendicular to both, so its dot product with each is 0.

**Check:** run it unchanged (after EX-PY-12) and it prints:

``` plaintext
True
0.439, 0.713, 0.547
[ 0.  32.7 38.7 37.2 40.7 36.3 44.  22.3 54.5 25.7]
most parallel to vector 0: 7 (22.3 deg)
[-0.7776  0.6157  0.6506] -0.0 -0.0
```

**Adapt it:**

Always `np.clip(cos, -1, 1)` before `arccos`. Most **anti**-parallel → `argmax` of the angle.

### EX-PY-14 · Gaussian log: SCF Done energy per step, relative energy in kcal/mol, plot

**From:** Week notebook 5.1  
**Search:** SCF Done · split()\[4\] · relative energy · kcal/mol 627.5095 · o- plot · xticks

From `data/piri_mock.log`, extract the `SCF Done` energy of each optimisation step. Plot the energy relative to the last step, in kcal/mol, against step number (`'o-'`, labelled axes).

Python

```python
import numpy as np
import matplotlib.pyplot as plt
E = []
with open('data/piri_mock.log') as f:
    for line in f:
        if 'SCF Done' in line:
            E.append(float(line.split()[4]))     # [SCF, Done:, E(RB3LYP), =, value, ...]
E = np.array(E)
rel = (E - E[-1]) * 627.5095
steps = np.arange(len(E))
plt.plot(steps, rel, 'o-')
plt.xlabel('Optimization step'); plt.ylabel('E - E_final (kcal/mol)'); plt.xticks(steps)
plt.show()
```

**How it works:**

- Every `SCF Done` line holds one energy; `line.split()[4]` is the number (words: SCF, Done:, E(RB3LYP), =, value).
- Relative energy: subtract the last value, multiply by 627.5095 to convert Ha → kcal/mol.
- `xticks(steps)` puts one tick per step.

**Adapt it:**

Relative to the first step → `E - E[0]`. eV → `* 27.2114`. Another “one value per line” quantity → change the marker and the index after `split()` (find it with `enumerate(line.split())`).

### EX-PY-15 · ORCA output: SCF iterations and final energy for every optimisation cycle, two-panel figure

**From:** Week notebook 5.3  
**Search:** every block · count iterations · FINAL SINGLE POINT ENERGY · subplots(1, 2) · bar chart

For **every** geometry optimisation cycle in `orca_mock.out`, get (a) the number of SCF iterations and (b) the `FINAL SINGLE POINT ENERGY`. Make one figure with two side-by-side panels (`fig, ax = plt.subplots(1, 2, figsize=(10,4))`): a bar chart of iterations per cycle, and energy versus cycle (`'o--'`).

Python

```python
import numpy as np
import matplotlib.pyplot as plt
n_iter, energies = [], []
inside = False
with open('data/orca_mock.out') as f:
    for line in f:
        if 'SCF ITERATIONS' in line:
            inside, n = True, 0
            continue
        if inside:
            p = line.split()
            if p and p[0].isdigit():
                n += 1
            elif 'Energy Check' in line:
                n_iter.append(n); inside = False
        if 'FINAL SINGLE POINT ENERGY' in line:
            energies.append(float(line.split()[-1]))
cycles = np.arange(1, len(n_iter) + 1)
print("iterations:", n_iter, " energies:", energies)

fig, ax = plt.subplots(1, 2, figsize=(10, 4))
ax[0].bar(cycles, n_iter); ax[0].set_xlabel('Opt cycle'); ax[0].set_ylabel('SCF iterations')
ax[1].plot(cycles, energies, 'o--'); ax[1].set_xlabel('Opt cycle'); ax[1].set_ylabel('Energy (Ha)')
for a in ax: a.set_xticks(cycles)
plt.tight_layout(); plt.show()
```

**How it works:**

- **Iterations:** at each `SCF ITERATIONS` a counter restarts at 0; every data row (first word is a number) adds 1; `Energy Check` stores the count and closes the block.
- **Energies:** each `FINAL SINGLE POINT ENERGY` line ends with the energy → `split()[-1]`.
- **Two panels:** `fig, ax = plt.subplots(1, 2)`; draw on `ax[0]` and `ax[1]`; `tight_layout` stops labels overlapping.

### EX-PY-16 · Reusable reader: one (iterations, ΔE) pair per SCF block, all cycles on one plot

**From:** Week notebook 5.4  
**Search:** read_scf_blocks · function returning list of arrays · all blocks · legend · loop over blocks

Write `read_scf_blocks(filename)`, which returns a list with one `(iterations, deltaE)` pair of arrays per SCF block. Use it to plot log10\|ΔE\| of **all** cycles on one axis, with a legend. (Skip iteration 0.)

Python

```python
import numpy as np
import matplotlib.pyplot as plt
def read_scf_blocks(filename):
    """Return a list of (iterations, deltaE) arrays, one per SCF ITERATIONS block."""
    blocks, it, dE, inside = [], [], [], False
    with open(filename) as f:
        for line in f:
            if 'SCF ITERATIONS' in line:
                inside, it, dE = True, [], []
                continue
            if inside:
                p = line.split()
                if p and p[0].isdigit():
                    it.append(int(p[0])); dE.append(float(p[2]))
                elif 'Energy Check' in line:
                    blocks.append((np.array(it), np.array(dE)))
                    inside = False
    return blocks

for c, (it, dE) in enumerate(read_scf_blocks('data/orca_mock.out'), start=1):
    plt.plot(it[1:], np.log10(np.abs(dE[1:])), 'o-', label=f'cycle {c}')
plt.xlabel('Iteration'); plt.ylabel('log10(|ΔE|)'); plt.legend(); plt.show()
```

**How it works:**

- The same block-reading logic as EX-PY-04, but instead of stopping at the first block, every `Energy Check` appends that block's `(iterations, ΔE)` arrays to a list and resets.
- The function **returns** the list, so any later question can reuse it: `blocks = read_scf_blocks('orca.out')`, `blocks[0]` = first block.
- The loop plots each block with its own legend label (`dE[1:]` skips iteration 1, whose ΔE is not a difference).

### EX-PY-17 · Gaussian optimisation log: energies + step numbers, convergence table (YES/NO)

**From:** Python 4-hour drill, B5 (5.1, 5.2) · file `data/opt_mock.log`  
**Search:** SCF Done · Step number · Maximum Force · RMS Force · Displacement · YES NO · startswith tuple · reshape(-1, 4) · converged step

**5.1 (E1)** Read the `SCF Done` energies and the step numbers (from the `Step number` lines) into two arrays. *Expected:* steps \[1 … 6\]; last energy −959.148733.

**5.2 (a table per step)** For each step, read the 4 convergence **values** (Maximum Force, RMS Force, Maximum Displacement, RMS Displacement) into an array of shape (6, 4), plus the YES/NO flags. Which step is the **first** where all 4 criteria are YES? Hint: `line.startswith((...))` with a tuple of 4 prefixes, then `.reshape(-1, 4)`. *Expected:* step 6.

Python

```python
import numpy as np
# 5.1
energies, steps = [], []
with open('data/opt_mock.log') as f:
    for line in f:
        if 'SCF Done' in line:
            energies.append(float(line.split()[4]))
        elif 'Step number' in line:
            steps.append(int(line.split()[2]))
energies, steps = np.array(energies), np.array(steps)
print(steps); print(energies)

# 5.2
conv, flags = [], []
with open('data/opt_mock.log') as f:
    for line in f:
        if line.startswith((' Maximum Force', ' RMS     Force', ' Maximum Displacement', ' RMS     Displacement')):
            p = line.split()
            conv.append(float(p[2]))
            flags.append(p[4] == 'YES')
conv = np.array(conv).reshape(-1, 4)             # one row per step
flags = np.array(flags).reshape(-1, 4)
print(conv)
first = np.where(flags.all(axis=1))[0][0]
print("first fully converged step:", steps[first])
```

**How it works:**

- **5.1** two markers in one pass: `SCF Done` → energy (word 4), `Step number` → step (word 2).
- **5.2** the 4 convergence lines start with fixed text, so `line.startswith((...))` with a tuple catches all four. Word 2 is the value, word 4 is YES/NO. Collected flat, then `.reshape(-1, 4)` → one row per step. `flags.all(axis=1)` is True where all four are YES; the first such row is the converged step.

**Adapt it:**

Several lines per step, a fixed number each → collect them flat, then `.reshape(-1, n)`.

### EX-PY-18 · Block per step: Mulliken charges of every step

**From:** Python 4-hour drill, B5 (5.3)  
**Search:** Mulliken charges · block · Sum of Mulliken · labels · charges per step · state machine

**5.3 (E2, every block)** Read the **Mulliken charges** of every step into an array of shape (6, 5) (steps × atoms), with the atom labels. Print the last step's charges next to their labels, and check that each step's charges sum to ≈ 0.

*Expected:* last step C −0.300000, H 0.195000, H 0.195200, Cl −0.042000, Cl −0.048200.

Python

```python
import numpy as np
# 5.3
charges, labels, inside = [], [], False
with open('data/opt_mock.log') as f:
    for line in f:
        if 'Mulliken charges:' in line:
            inside, block = True, []
            continue
        if inside:
            p = line.split()
            if 'Sum of Mulliken' in line:
                charges.append(block); inside = False
            elif len(p) == 3 and p[0].isdigit():
                block.append(float(p[2]))
                if len(charges) == 0:
                    labels.append(p[1])
charges = np.array(charges)                      # (nsteps, natoms)
print(charges.shape)
for lab, qq in zip(labels, charges[-1]):
    print(f"{lab:3s}{qq:10.6f}")
print("sums:", charges.sum(axis=1).round(6))
```

**How it works:**

- A block starts at `Mulliken charges:` and ends at `Sum of Mulliken`. Inside, data rows have exactly 3 words and start with a number: (index, element, charge).
- Each block becomes one list of charges; the element labels are taken only from the first block.
- The result is a (steps × atoms) array; each row should sum to the total charge (0 for a neutral molecule).

**Adapt it:**

Same skeleton for any table that repeats per step: start marker → collect rows with the right number of tokens → end marker.

### EX-PY-19 · Fixed-width columns: occupied and virtual orbital energies per step

**From:** Python 4-hour drill, B5 (5.4)  
**Search:** fixed width · read_values · 10 characters · numbers run together · Population analysis · occ virt

**5.4 (E3 + fixed width)** For each step, collect all the **occupied** and all the **virtual** orbital energies.

1.  First try `line.split()` and see it crash. Why does it crash?
2.  Write `read_values(line)`, which slices the fixed 10-character columns.
3.  For each step, print the number of occupied orbitals, the lowest occupied energy, the highest virtual energy, and how many virtual energies are negative.

*Expected (step 1):* 21 occupied, lowest −101.56032, highest virtual 0.79371, 1 negative virtual.

Python

```python
# 5.4
def read_values(line):
    """Gaussian eigenvalue line -> list of floats, using the fixed 10-character columns."""
    data = line[28:].rstrip('\n')
    return [float(data[k:k+10]) for k in range(0, len(data), 10)]

occ_steps, virt_steps = [], []
with open('data/opt_mock.log') as f:
    for line in f:
        if 'Population analysis' in line:
            occ_steps.append([]); virt_steps.append([])
        elif 'Alpha  occ. eigenvalues' in line:
            occ_steps[-1] += read_values(line)
        elif 'Alpha virt. eigenvalues' in line:
            virt_steps[-1] += read_values(line)
for s, (o, vv) in enumerate(zip(occ_steps, virt_steps), start=1):
    neg = sum(1 for x in vv if x < 0)
    print(f"step {s}: {len(o)} occ, lowest {o[0]:.5f}, highest virt {vv[-1]:.5f}, negative virt {neg}")
```

**How it works:**

- `split()` fails because negative numbers in fixed 10-character columns can touch (`-101.56032-101.56030` has no space).
- `read_values` slices `line[28:]` into 10-character pieces and converts each.
- The occ/virt lists are grouped per step the same way as in EX-PY-03.

**Adapt it:**

`split()` breaks when numbers touch (`-101.56032-101.56030`). Slice `line[28:]` in 10-character pieces. Check the label length with `print(repr(line[:30]))`.

### EX-PY-20 · Multi-frame .xyz trajectory: frames × atoms × xyz, distances and angle per frame

**From:** Python 4-hour drill, B5 (5.5) · file `data/traj.xyz`  
**Search:** xyz trajectory · frames · readline · nat · O-H distance · H-O-H angle · arccos

**5.5 (E4, multi-frame xyz)** Read `data/traj.xyz` into an array of shape (8, 3, 3) (frames × atoms × xyz), without assuming the number of frames. Then, for each frame, compute the two O–H distances and the H–O–H angle.

*Expected:* frame 0 → 0.9600, 0.9600, 107.50° · frame 2 → 0.9900, 0.9300, 104.50°.

Python

```python
import numpy as np
# 5.5
frames = []
with open('data/traj.xyz') as f:
    while True:
        line = f.readline()
        if not line.strip():
            break                                # end of file
        nat = int(line)
        f.readline()                             # title line
        frame = [[float(x) for x in f.readline().split()[1:4]] for _ in range(nat)]
        frames.append(frame)
traj = np.array(frames)                          # (nframes, natoms, 3)
print(traj.shape)
oh1 = np.linalg.norm(traj[:, 1] - traj[:, 0], axis=1)
oh2 = np.linalg.norm(traj[:, 2] - traj[:, 0], axis=1)
a = traj[:, 1] - traj[:, 0]; b = traj[:, 2] - traj[:, 0]
cosang = (a * b).sum(axis=1) / (oh1 * oh2)
angle = np.degrees(np.arccos(np.clip(cosang, -1, 1)))
for k in range(len(traj)):
    print(f"frame {k}: OH1 {oh1[k]:.4f}  OH2 {oh2[k]:.4f}  angle {angle[k]:.2f}")
```

**How it works:**

- Each frame is: a line with the number of atoms, a title line, then that many atom lines. The `while True` loop reads one frame per pass and stops when `readline()` returns an empty line (end of file).
- `split()[1:4]` takes x, y, z (word 0 is the element).
- The array has shape (frames, atoms, 3), so `traj[:, 1] - traj[:, 0]` is the O→H vector in every frame at once; the angle uses the dot product of the two O→H vectors.

**Adapt it:**

Unknown number of frames → `while True` + `readline()` until it returns an empty line. Angle at atom B between A and C → vectors `A-B` and `C-B`.

### EX-PY-21 · Plain numeric table: np.loadtxt, mean/std, linear drift

**From:** Python 4-hour drill, B5 (5.6) · file `data/md.dat`  
**Search:** loadtxt · columns · mean · std · polyfit drift

**5.6 (E0)** Load `data/md.dat` with `np.loadtxt`. Print its shape, the mean and standard deviation of T, and the slope of a linear fit of Etot against time (the energy drift).

*Expected:* (50, 4) · mean T = 300.12 K, std 3.01 · drift 0.0591 kJ/mol/ps.

Python

```python
import numpy as np
# 5.6
md = np.loadtxt('data/md.dat')                   # '#' lines are skipped automatically
t, T, Etot = md[:, 0], md[:, 1], md[:, 2]
print(md.shape, f"mean T = {T.mean():.2f} K, std = {T.std():.2f}")
slope, intercept = np.polyfit(t, Etot, 1)
print(f"Etot drift = {slope:.4f} kJ/mol/ps")
```

**How it works:**

- `np.loadtxt` reads a whole numeric table and skips `#` lines; each column is `data[:, k]`.
- `polyfit(t, E, 1)[0]` is the slope = the drift per time unit.

**Adapt it:**

`#` lines are skipped automatically. Other comment character → `comments='%'`; header lines without `#` → `skiprows=n`.

### EX-PY-22 · matplotlib: five target figures (relative energy, log scale + threshold, two panels, fit in title, histogram)

**From:** Python 4-hour drill, B6 (6.1–6.5) · uses the data of EX-PY-17, 20, 21 and 09  
**Search:** plot styles · o-- · log scale · axhline · subplots · title f-string · hist bins · axvline · savefig

**6.1** Energy relative to the final step, in kcal/mol (1 Ha = 627.5095 kcal/mol), against the optimisation step (data from 5.1).

**6.2** The 4 convergence values against step on a **log** scale, with a dashed line at the Maximum Force threshold (0.00045) (data from 5.2).

**6.3** Two panels: both O–H distances, and the H–O–H angle, against frame (data from 5.5).

**6.4** Etot against time with the linear fit, and the slope in the title (data from 5.6).

**6.5** A histogram (20 bins) of all the pair distances of `pos`, with a vertical line at the cutoff 8 (data from B3).

Python

```python
import numpy as np
from scipy.spatial.distance import pdist, squareform
# 6.1
import matplotlib.pyplot as plt
rel = (energies - energies[-1]) * 627.5095
plt.figure(figsize=(6, 4))
plt.plot(steps, rel, 'o--', color='k')
plt.xlabel('Optimization step'); plt.ylabel('E - E$_{final}$ (kcal/mol)')
plt.xticks(steps)
plt.tight_layout(); plt.savefig('fig1.png', dpi=110); plt.show()

# 6.2
names = ['Max force', 'RMS force', 'Max disp.', 'RMS disp.']
plt.figure(figsize=(6, 4))
for col in range(4):
    plt.plot(steps, conv[:, col], 'o-', label=names[col])
plt.axhline(0.00045, ls='--', color='gray', label='Max force threshold')
plt.yscale('log')
plt.xlabel('Optimization step'); plt.ylabel('Value (a.u.)'); plt.xticks(steps)
plt.legend(); plt.tight_layout(); plt.savefig('fig2.png', dpi=110); plt.show()

# 6.3
fig, ax = plt.subplots(1, 2, figsize=(10, 4))
frame = np.arange(len(traj))
ax[0].plot(frame, oh1, 'o-', label='O-H1')
ax[0].plot(frame, oh2, 's--', label='O-H2')
ax[0].set_xlabel('Frame'); ax[0].set_ylabel('Distance (Å)'); ax[0].legend()
ax[1].plot(frame, angle, 'o-', color='C2')
ax[1].set_xlabel('Frame'); ax[1].set_ylabel('H-O-H angle (deg)')
plt.tight_layout(); plt.savefig('fig3.png', dpi=110); plt.show()

# 6.4
plt.figure(figsize=(6, 4))
plt.plot(t, Etot, '.', label='data')
plt.plot(t, slope*t + intercept, '-', color='r', label='fit')
plt.title(f'Energy drift: {slope:.4f} kJ/mol/ps')
plt.xlabel('time (ps)'); plt.ylabel('E$_{tot}$ (kJ/mol)'); plt.legend()
plt.tight_layout(); plt.savefig('fig4.png', dpi=110); plt.show()

# 6.5
plt.figure(figsize=(6, 4))
plt.hist(pdist(pos), bins=20, edgecolor='k')
plt.axvline(8, color='r', ls='--', label='cutoff 8')
plt.xlabel('Pair distance'); plt.ylabel('Count'); plt.legend()
plt.tight_layout(); plt.savefig('fig5.png', dpi=110); plt.show()
```

**How it works:**

- One block per figure; each starts a new figure (or `subplots`) and ends with `savefig` then `show`.
- **6.2** `plt.yscale('log')` + `axhline` for the threshold. **6.3** `subplots(1, 2)`. **6.4** the slope in the title with an f-string. **6.5** `plt.hist(pdist(pos), bins=20)` + `axvline` at the mean.

**Adapt it:**

`savefig` **before** `show`. Integer steps on the x axis → `plt.xticks(steps)`.

### EX-PY-23 · Homework (Assignment 1): read a .cif file, distances to Mg, bound atoms, element counts, water check

**From:** Class assignment `20_EI_2026.ipynb` (your submitted solution)  
**Search:** cif · ATOM HETATM · startswith · columns · distances to one atom · np.nan self · np.where · count dict · HOH water · residues

a\) Write a function called `read_cif` that, given a pdbname (including the path) returns 3 arrays called `atoms`, `residues` and `xyz` containing the atoms, residues and coordinates of the cid fle.

The function should read the lines starting with `ATOM` or `HETATM`. `ATOM` lines are protein atoms and `HETATM` refer no non-protein atoms, but they share the same format:

- column 2 contains the element symbol (in upper case)
- column 5 contains the residue name
- columns 10,11,12 contain the x,y,z coordinates

b\) Call the function with the downloaded `34HX.cif` cif file from the [Protein Data Bank](https://www.rcsb.org/)

c\) Now create a function that given an array of dimension (N,3) containing the xyz coordinates and a index indetifying a given atom, return an array with the distances of that atom to all the others atoms.

As it is convinient to keep distances as having the same number of rows as the coordinate array, set the value of the distance of the atom to itself as `np.nan`.

d\) Check that `MG` is one of the atoms. Identify the index of the Mg atom and calculate the distances with all the other atoms.

e\) Now we define that an atom that is \<2.8A of the Mg ion is bound to it.  
Identify those atoms that are bound to Mg and store them in a container called `close_atoms` (it can be a list, an array or what you prefer).

f\) Now, using the algorithm we did in class, create a function that given `close_atoms` returns a dictionary counting how many atom of each element are in `close_atoms`. For example `{"O":4, "N:2}`

g\) Finally, check if any of the residues binding Mg is a water molecule. Water molecules are identified as `HOH` in the cif file. If there are print "At least one Mg binding residue is a water molecule" otherwise print "None of the Mg binding residues are water molecules".

imports

```python
# Justin Boyd
import numpy as np
import matplotlib.pyplot as plt
```

a\) read_cif

```python
def read_cif(cifname):
    atoms = []
    residues = []
    xyz = []
    with open(cifname) as f:
        for line in f:
            if line.startswith("ATOM") or line.startswith("HETATM"):
                cols = line.split()
                atoms.append(cols[2])
                residues.append(cols[5])
                xyz.append([float(cols[10]), float(cols[11]), float(cols[12])])
    return np.array(atoms), np.array(residues), np.array(xyz)
```

b\) call it and check the shapes

```python
atoms, residues, xyz = read_cif("Downloads/34HX.cif")
print(len(atoms), atoms.shape, residues.shape, xyz.shape)
print(atoms[0], residues[0], xyz[0])
print(atoms[-1], residues[-1], xyz[-1])
```

c\) distances from one atom to all the others

```python
def distances(array, index):
    diff = array - array[index]
    d = np.sqrt((diff**2).sum(axis=1))
    d[index] = np.nan
    return d
```

d\) is MG there, which index, distances from it

```python
print("MG is one of the atoms:", "MG" in atoms)

mg_indices = np.where(atoms == "MG")[0]
print("Number of MG atoms:", len(mg_indices))

index_Mg = mg_indices[0]
print("Index of MG:", index_Mg, atoms[index_Mg], residues[index_Mg], xyz[index_Mg])

dists = distances(xyz, index_Mg)
```

e\) atoms closer than 2.8 Å (bound to Mg)

```python
close_atoms = np.where(dists < 2.8)[0]

for k in close_atoms:
    print(k, atoms[k], residues[k], round(dists[k], 3))
```

f\) count the elements with a dictionary

```python
def count_atoms(close):
    counts = {}
    for k in close:
        element = str(atoms[k])
        if element in counts:
            counts[element] += 1
        else:
            counts[element] = 1
    return counts
    

print(count_atoms(close_atoms))
```

g\) is one of the binding residues a water?

```python
if "HOH" in residues[close_atoms]:
    print("At least one Mg binding residue is a water molecule")
else:
    print("None of the Mg binding residues are water molecules")
```

**How it works:**

- **a)** keep only `ATOM`/`HETATM` lines; `split()` and take word 2 (element), word 5 (residue), words 10–12 (x, y, z); return three NumPy arrays.
- **c)** `array - array[index]` subtracts one atom from every row (broadcasting); square, sum over xyz, square root = all distances. The atom's distance to itself is set to `np.nan`, so it never counts as “close”.
- **d)** `"MG" in atoms` checks it exists; `np.where(atoms == "MG")[0]` gives its index.
- **e)** `np.where(dists < 2.8)[0]` = indices of the bound atoms (`nan < 2.8` is False, so Mg itself is excluded).
- **f)** the class algorithm: a dict; if the element is already a key add 1, else start it at 1.
- **g)** `residues[close_atoms]` = the residues of the bound atoms; `"HOH" in ...` checks for water.

**Check:** run it unchanged on `34HX.cif` (the output saved in your notebook) and it prints:

``` plaintext
4936 (4936,) (4936,) (4936, 3)
N ASN [-41.559   7.225  -9.649]
O HOH [ 3.993  0.23  -9.416]
MG is one of the atoms: True
Number of MG atoms: 1
Index of MG: 4519 MG MG [-24.561  23.273 -38.2  ]
185 O ASP 2.408
198 O THR 2.514
210 O ASN 2.405
219 O TYR 2.282
239 O ASP 2.463
4714 O HOH 2.381
{'O': 6}
At least one Mg binding residue is a water molecule
```

**Adapt it:**

Another ion → change `"MG"`; another cutoff → `2.8`; another file → the path in b). Other columns of the cif (chain, residue number) → print `line.split()` of one ATOM line with `enumerate` and pick the index. The class counting “algorithm” is the dict with `if element in counts`; `collections.Counter` gives the same result.

### EX-PY-24 · Homework (Assignment 2): Euler integrator — NumPy, Numba, f2py timings, energy drift, velocity Verlet

**From:** Class assignment `22_assignment2.ipynb` + `fortran_utils.f90` / `fortran_utils2.f90` (your submitted solution)  
**Search:** harmonic oscillator · integrator · timeit · numba njit · f2py · logspace · total energy · velocity verlet · allclose

Optimise the Euler integrator from class for a harmonic potential (k = m = 1, x₀ = 1, v₀ = 0), with `nsteps = 100000` and `dt = 1e-2` for all the tests.

1.  Time the pure-Python integrator with `%timeit`. Then: a NumPy version (arrays instead of lists), Numba (`@njit`) versions, and Fortran versions compiled with `f2py -c fortran_utils.f90 -m fortran_utils`.
2.  Generate 7 logarithmically spaced `nsteps` from 10 to 1000, converted to integers. Use `%timeit -o` to store the results for at least 2 methods, and plot all of them in one plot with a legend.
3.  Call again the integrate functions and store the values in `traj_x` and `traj_v`. In the same plot, plot to first 1000 values of `traj_x` calculated with pure python and `f2py`. (`%timeit` does not store the values, so run again the results) Check also with a numpy function that all the 10.000 values are close.
4.  Remember that this is an unstable integrator and that it should not be used for Molecular Dynamics. Calculate the total energy of the system for both trajectories (with the 2 different integrators) and Check that if you half the time-step (and double the number of steps to get the same total time) the errors are smaller, but divergence persists.
5.  Velocity Verlet: a(t) = f(x(t))/m; x(t+Δt) = x(t) + v(t)Δt + ½a(t)Δt²; a(t+Δt) = f(x(t+Δt))/m; v(t+Δt) = v(t) + ½(a(t+Δt) + a(t))Δt. Integrate the equation of motions with the same parameters and plot total energy: Remark that the energy is not numerically constant, but it oscillates. However, there is not a net drift in total energy, which is crucial for molecular dynamics.

setup: the class integrator

```python
# Justin Boyd
import matplotlib.pyplot as plt
import numpy as np

def harmonic_force(x,k=1.0):
    return -k*x

def integrate(x0, v0, nsteps, f, deltat, m=1.0):
    x_prev = x0
    v_prev = v0
    x_traj = [x0, ]
    v_traj = [v0, ]
    for i in range(nsteps):
        a_prev = f(x_prev)/m
        x_new = x_prev + v_prev*deltat + 0.5*a_prev*deltat**2
        v_new = v_prev + a_prev*deltat
        x_prev = x_new
        v_prev = v_new
        x_traj.append(x_new)
        v_traj.append(v_new)
    return x_traj, v_traj

nsteps=100000
dt=1e-2
```

1\) NumPy version

```python
def integrate_numpy(x0, v0, nsteps, f, deltat, m=1.0):
    x_prev = x0
    v_prev = v0
    x_traj = np.zeros(nsteps+1)
    v_traj = np.zeros(nsteps+1)
    x_traj[0] = x0
    v_traj[0] = v0
    for i in range(nsteps):
        a_prev = f(x_prev)/m
        x_new = x_prev + v_prev*deltat + 0.5*a_prev*deltat**2
        v_new = v_prev + a_prev*deltat
        x_prev = x_new
        v_prev = v_new
        x_traj[i+1] = x_new
        v_traj[i+1] = v_new
    return x_traj, v_traj
```

1\) Numba versions

```python
from numba import njit

@njit
def harmonic_force_jit(x,k=1.0):
    return -k*x

@njit
def integrate_jit(x0, v0, nsteps, f, deltat, m=1.0):
    x_prev = x0
    v_prev = v0
    x_traj = [x0, ]
    v_traj = [v0, ]
    for i in range(nsteps):
        a_prev = f(x_prev)/m
        x_new = x_prev + v_prev*deltat + 0.5*a_prev*deltat**2
        v_new = v_prev + a_prev*deltat
        x_prev = x_new
        v_prev = v_new
        x_traj.append(x_new)
        v_traj.append(v_new)
    return x_traj, v_traj

@njit
def integrate_numpy_jit(x0, v0, nsteps, f, deltat, m=1.0):
    x_prev = x0
    v_prev = v0
    x_traj = np.zeros(nsteps+1)
    v_traj = np.zeros(nsteps+1)
    x_traj[0] = x0
    v_traj[0] = v0
    for i in range(nsteps):
        a_prev = f(x_prev)/m
        x_new = x_prev + v_prev*deltat + 0.5*a_prev*deltat**2
        v_new = v_prev + a_prev*deltat
        x_prev = x_new
        v_prev = v_new
        x_traj[i+1] = x_new
        v_traj[i+1] = v_new
    return x_traj, v_traj
```

1\) fortran_utils2.f90 (shell: f2py -c fortran_utils2.f90 -m fortran_utils2)

```fortran
! 1. The Force Function
function harmonic_force(x) 
    implicit none
    real, intent(in) :: x
    real :: harmonic_force
    real :: k = 1.0
    harmonic_force = -k * x
end function harmonic_force

! 2. The Integrator
subroutine integrate_fast(x0, v0, nsteps, deltat, x_traj, v_traj, m)
    implicit none
    real, intent(in) :: x0, v0, deltat
    integer, intent(in) :: nsteps
    real, intent(in) :: m
    real, intent(out) :: x_traj(0:nsteps), v_traj(0:nsteps)
    
    ! Declare the Fortran function we are going to call internally
    real :: harmonic_force
    external harmonic_force

    real :: x_prev, v_prev, x_new, v_new, a_prev
    integer :: i

    x_prev = x0
    v_prev = v0
    x_traj(0) = x0
    v_traj(0) = v0

    do i = 1, nsteps
        ! Direct Fortran-to-Fortran call. Python is completely bypassed.
        a_prev = harmonic_force(x_prev) / m
        
        x_new = x_prev + v_prev * deltat + 0.5 * a_prev * deltat**2
        v_new = v_prev + a_prev * deltat
        
        x_prev = x_new
        v_prev = v_new
        x_traj(i) = x_new
        v_traj(i) = v_new
    end do
end subroutine integrate_fast
```

1\) time each method (notebook cells)

```python
%timeit integrate(1.0, 0.0, nsteps, harmonic_force, dt)
%timeit integrate_numpy(1.0, 0.0, nsteps, harmonic_force, dt)
%timeit integrate_jit(1.0, 0.0, nsteps, harmonic_force_jit, dt)
%timeit integrate_numpy_jit(1.0, 0.0, nsteps, harmonic_force_jit, dt)
import fortran_utils2
%timeit fortran_utils2.integrate_fast(x0=1.0,v0=0.0, nsteps=nsteps, deltat=dt, m = 1.0)
```

2\) timings of all methods on one log-log plot (notebook: uses %timeit -o)

```python
nsteps_list = np.logspace(1, 3, 7).astype(int)
print(nsteps_list)

methods = {
    "pure Python":             lambda n: integrate(1.0, 0.0, n, harmonic_force, dt),
    "NumPy":                   lambda n: integrate_numpy(1.0, 0.0, n, harmonic_force, dt),
    "Numba (NumPy arrays)":    lambda n: integrate_numpy_jit(1.0, 0.0, n, harmonic_force_jit, dt),
    "f2py (force in Fortran)": lambda n: fortran_utils2.integrate_fast(x0=1.0, v0=0.0, nsteps=n, deltat=dt, m=1.0),
}

for name, run in methods.items():
    times = []
    for n in nsteps_list:
        t = %timeit -o -q run(n)
        times.append(t.best * 1e6)
    plt.plot(nsteps_list, times, "o-", label=name)

plt.xscale("log")
plt.yscale("log")
plt.xlabel("number of steps")
plt.ylabel("time (µs)")
plt.legend()
```

3\) Python vs f2py trajectories

```python
traj_x, traj_v = integrate(1.0, 0.0, nsteps, harmonic_force, dt)
traj_x_f, traj_v_f = fortran_utils2.integrate_fast(x0=1.0, v0=0.0, nsteps=nsteps, deltat=dt, m=1.0)

plt.plot(traj_x[:1000], label="pure Python")
plt.plot(traj_x_f[:1000], "--", label="f2py")
plt.xlabel("step")
plt.ylabel("x")
plt.legend()
plt.show()

# the Fortran code uses single precision (real), so the trajectories agree to ~1e-6 at first;
# the rounding error grows as the amplitude grows (the energy diverges), up to ~2.5e-4 at the end
x_py = np.asarray(traj_x)
print("first 10 000 values close (atol=1e-5):", np.allclose(x_py[:10000], traj_x_f[:10000], atol=1e-5))
print("largest difference, first 10 000:     ", np.max(np.abs(x_py[:10000] - traj_x_f[:10000])))
print("all values close (default tolerance): ", np.allclose(x_py, traj_x_f))
print("largest difference, all values:       ", np.max(np.abs(x_py - traj_x_f)))
```

4\) total energy, and with half the time step

```python
# total energy = kinetic + potential: E = 1/2 m v^2 + 1/2 k x^2  (m = k = 1)
def total_energy(x, v, m=1.0, k=1.0):
    x, v = np.asarray(x), np.asarray(v)
    return 0.5*m*v**2 + 0.5*k*x**2

time = np.arange(nsteps + 1) * dt
E_python = total_energy(traj_x, traj_v)
E_f2py = total_energy(traj_x_f, traj_v_f)

plt.plot(time, E_python, label="pure Python")
plt.plot(time, E_f2py, "--", label="f2py")
plt.xlabel("time")
plt.ylabel("Total energy")
plt.legend()
plt.show()
print(f"energy at t=0: {E_python[0]:.3f}, at t={time[-1]:.0f}: Python {E_python[-1]:.3f}, f2py {E_f2py[-1]:.3f}")

# half the time step, double the steps: same total time (1000)
x_half, v_half = integrate(1.0, 0.0, 2*nsteps, harmonic_force, dt/2)
time_half = np.arange(2*nsteps + 1) * (dt/2)
E_half = total_energy(x_half, v_half)

plt.plot(time, E_python, label=f"dt = {dt}")
plt.plot(time_half, E_half, label=f"dt = {dt/2}")
plt.xlabel("time")
plt.ylabel("Total energy")
plt.legend()
plt.show()
print(f"energy gained by t={time[-1]:.0f}:  dt={dt}: {E_python[-1] - E_python[0]:.3f}   dt={dt/2}: {E_half[-1] - E_half[0]:.3f}")
```

5\) velocity Verlet

```python
def verlet(x0, v0, nsteps, f, deltat, m=1.0):
    x_traj = np.zeros(nsteps + 1)
    v_traj = np.zeros(nsteps + 1)
    x_traj[0] = x0
    v_traj[0] = v0
    a_prev = f(x0) / m                                    # a(t)
    for i in range(nsteps):
        x_new = x_traj[i] + v_traj[i]*deltat + 0.5*a_prev*deltat**2   # x(t+dt)
        a_new = f(x_new) / m                                          # a(t+dt), at the NEW position
        v_traj[i+1] = v_traj[i] + 0.5*(a_prev + a_new)*deltat         # average of old and new a
        x_traj[i+1] = x_new
        a_prev = a_new                                    # reuse: one force call per step
    return x_traj, v_traj

x_verlet, v_verlet = verlet(1.0, 0.0, nsteps, harmonic_force, dt)
E_verlet = total_energy(x_verlet, v_verlet)

plt.plot(time, E_python, label="Euler-type integrator (class)")
plt.plot(time, E_verlet, label="velocity Verlet")
plt.xlabel("time")
plt.ylabel("Total energy")
plt.legend()
plt.show()
print(f"Verlet energy at t=0: {E_verlet[0]:.6f}, at t={time[-1]:.0f}: {E_verlet[-1]:.6f}")

# zoom in on Verlet only: the energy oscillates around 0.5 but does not drift
plt.plot(time, E_verlet)
plt.axhline(0.5, color="gray", linestyle=":", label="exact energy 0.5")
plt.xlim(0, 50)
plt.ticklabel_format(axis="y", useOffset=False)   # show plain values, not an offset
plt.xlabel("time")
plt.ylabel("Total energy")
plt.legend()
plt.show()
print(f"Verlet energy: min {E_verlet.min():.7f}, max {E_verlet.max():.7f}, "
      f"mean over first half {E_verlet[:nsteps//2].mean():.7f}, second half {E_verlet[nsteps//2:].mean():.7f}")
```

**How it works:**

- **Euler step:** x ← x + vΔt, v ← v + (F/m)Δt. The pure-Python version appends to lists; the NumPy version pre-allocates arrays.
- **Numba:** `@njit` compiles the function on its first call; it's fastest when the force function is also `@njit` and the trajectory uses arrays.
- **f2py:** `fortran_utils2.f90` runs the whole loop and the force in Fortran, so Python is only called once.
- **Energy:** E = ½mv² + ½kx² should stay at 0.5. Euler gains energy every step (unstable); halving Δt only slows the drift.
- **Velocity Verlet:** updates x with the current acceleration, computes the new acceleration, then updates v with the average of old and new. The energy oscillates slightly but doesn't drift.

**Adapt it:**

Lines starting with `%timeit` only work in a notebook; in a script use `import time; t0 = time.perf_counter(); f(); print(time.perf_counter() - t0)`. Why the first f2py version was slow: `fortran_utils.f90` calls the force function back in Python at every step; `fortran_utils2.f90` calls a Fortran force directly. Verlet: one force call per step, reuse `a_new` as the next `a_prev`.

### EX-PY-25 · Zinc site in a .cif file: read_cif, distances with nan, bound atoms + count dict, closest bound pair, mean distance + centre

**From:** Mock exam 2 (11 Nov), Ex 4 (2.5 points) · the practice file is mock_exam/ex4/zn_site.cif  
**Search:** read_cif · ATOM HETATM · startswith · distances(array, index) · np.nan · np.where · cutoff 2.5 · bound atoms · count dictionary · get(el, 0) · HOH water · closest pair among a subset · map back bound\[a\] · mean distance function · geometric centre · axis=0

The file `zn_site.cif` contains a protein fragment with a zinc ion in mmCIF format. As in Python assignment 1, the lines that start with `ATOM` or `HETATM` contain the atoms: column 2 is the element, column 5 the residue name and columns 10, 11, 12 the x, y, z coordinates (counting from 0).

**4.1.** (0.5) Write a function `read_cif(cifname)` that returns three arrays `atoms`, `residues` and `xyz`. Print the number of atoms and the shape of `xyz`.

**4.2.** (0.5) Write a function `distances(array, index)` that returns the distances from atom `index` to all the atoms, with `np.nan` for the atom itself. Find the index of the zinc atom and compute its distances to all the other atoms.

**4.3.** (0.5) An atom closer than 2.5 Å to the zinc is bound to it. Print the index, element, residue and distance of every bound atom. Make a dictionary that counts the bound atoms of each element, and say whether any bound residue is a water (`HOH`).

**4.4.** (0.5) Among the bound atoms only, find the pair of atoms that are closest to each other. Give their indices **in the full atom array**, their elements, and the distance. Prove the result by calculating that distance explicitly.

**4.5.** (0.5) Write a function that returns the mean distance between the zinc and its bound atoms, and use it to print that value. Also print the geometric centre of the bound atoms and its distance to the zinc.

**Cues (the prompt says → you write):**

| Part | The prompt says                               | You write                                                                          |
|------|-----------------------------------------------|------------------------------------------------------------------------------------|
| 4.1  | “lines that start with ATOM or HETATM”        | `if line.startswith('ATOM') or line.startswith('HETATM'):`                         |
| 4.1  | “column 2 / 5 / 10, 11, 12 (counting from 0)” | `cols = line.split()` → `cols[2]`, `cols[5]`, `float(cols[10])` …                  |
| 4.1  | “returns three arrays”                        | lists while reading → `return np.array(atoms), np.array(residues), np.array(xyz)`  |
| 4.1  | “number of atoms and the shape”               | `len(atoms)`, `xyz.shape`                                                          |
| 4.2  | “distances from atom index to all the atoms”  | `np.linalg.norm(array - array[index], axis=1)` (one length per row)                |
| 4.2  | “np.nan for the atom itself”                  | `d[index] = np.nan`                                                                |
| 4.2  | “find the index of the zinc atom”             | `np.where(atoms == 'ZN')[0][0]`                                                    |
| 4.3  | “closer than 2.5 Å … bound”                   | `bound = np.where(dists < 2.5)[0]` (nan \< 2.5 is False, so Zn is left out)        |
| 4.3  | “dictionary that counts”                      | `counts[el] = counts.get(el, 0) + 1`                                               |
| 4.3  | “whether any bound residue is a water”        | `'HOH' in residues[bound]`                                                         |
| 4.4  | “pair … closest to each other”                | `squareform(pdist(...))` + `fill_diagonal(D, np.inf)` + `argmin` + `unravel_index` |
| 4.4  | “among the bound atoms only”                  | use `xyz[bound]` (a small 5×5 matrix)                                              |
| 4.4  | “indices in the full atom array”              | map back: `i, j = bound[a], bound[b]`                                              |
| 4.4  | “prove the result”                            | `np.linalg.norm(xyz[i] - xyz[j])` gives the same number                            |
| 4.5  | “write a function that returns the mean”      | `def ...: return dists[idx].mean()`                                                |
| 4.5  | “geometric centre of the bound atoms”         | `xyz[bound].mean(axis=0)` (average down the rows → one point)                      |

Python

```python
import numpy as np
from scipy.spatial.distance import pdist, squareform

# 4.1
def read_cif(cifname):
    atoms, residues, xyz = [], [], []
    with open(cifname) as f:
        for line in f:
            if line.startswith("ATOM") or line.startswith("HETATM"):
                cols = line.split()
                atoms.append(cols[2])
                residues.append(cols[5])
                xyz.append([float(cols[10]), float(cols[11]), float(cols[12])])
    return np.array(atoms), np.array(residues), np.array(xyz)

atoms, residues, xyz = read_cif("zn_site.cif")
print(len(atoms), xyz.shape)

# 4.2
def distances(array, index):
    d = np.linalg.norm(array - array[index], axis=1)   # axis=1: one length per row (per atom)
    d[index] = np.nan
    return d

zn = np.where(atoms == "ZN")[0][0]     # where -> (array_of_positions,) ; [0][0] = the first one
dists = distances(xyz, zn)
print("ZN index:", zn)

# 4.3
bound = np.where(dists < 2.5)[0]       # positions of the bound atoms (nan < 2.5 is False -> Zn excluded)
for k in bound:
    print(k, atoms[k], residues[k], f"{dists[k]:.3f}")

counts = {}
for k in bound:
    el = str(atoms[k])
    counts[el] = counts.get(el, 0) + 1   # get(el, 0): 0 if not seen yet
print(counts)
print("water bound:", "HOH" in residues[bound])

# 4.4
D = squareform(pdist(xyz[bound]))       # 5x5 matrix of distances between the bound atoms
np.fill_diagonal(D, np.inf)             # the diagonal is 0 (an atom to itself); hide it
a, b = np.unravel_index(np.argmin(D), D.shape)   # argmin gives a flat index -> (row, col)
i, j = bound[a], bound[b]               # a, b are 0-4 in the SMALL matrix -> full indices
print(i, atoms[i], j, atoms[j], f"{D[a, b]:.3f}")
print("check:", np.linalg.norm(xyz[i] - xyz[j]))       # the same number = the proof

# 4.5
def mean_bound_distance(dists, idx):
    return dists[idx].mean()

print(f"mean Zn-ligand: {mean_bound_distance(dists, bound):.3f}")
c = xyz[bound].mean(axis=0)              # axis=0: average down the rows -> one (x,y,z)
print(c, np.linalg.norm(c - xyz[zn]))
```

**How it works:**

- **4.1** `startswith` works here because `ATOM`/`HETATM` are the very first characters of the line (compare the MD log in EX-PY-26, whose lines start with spaces). `split()` cuts on any run of spaces; the question counts columns from 0, which is exactly the `cols[k]` index. Collect in lists (fast `append`), convert to arrays once at the end.
- **4.2** `array - array[index]`: (244, 3) minus (3,) is broadcast to every row, so each row becomes the vector from the chosen atom to that atom. `norm(..., axis=1)` takes the length of each row → 244 distances. The atom to itself is 0, so the question asks for `np.nan` instead.
- **4.2** `np.where(atoms == 'ZN')` returns a tuple holding one array of positions; `[0]` takes that array, the second `[0]` its first element (there is only one zinc).
- **4.3** `dists < 2.5` is a True/False array; `np.where(...)[0]` turns it into the indices. Comparisons with `nan` are always False, which is why the zinc itself never appears. Loop over `bound` to print index, element, residue, distance.
- **4.3** The count dictionary: `counts.get(el, 0)` returns the current count, or 0 the first time the element appears; add 1 and store it back. `str(...)` turns NumPy strings into plain strings so the dict prints cleanly.
- **4.4** The trap: `pdist`/`squareform` on `xyz[bound]` gives a matrix whose rows are 0–4, **positions inside `bound`**, not atom numbers. `bound[a]` and `bound[b]` translate them back to the full array, which is what the question asks for.
- **4.4** `fill_diagonal(D, np.inf)` hides the zeros on the diagonal so `argmin` finds a real pair. `argmin` returns a flat index (0–24); `unravel_index(..., D.shape)` turns it into (row, column).
- **4.5** The function only needs the distances and the indices; `dists[idx]` picks the 5 bound distances. `xyz[bound].mean(axis=0)` averages the 5 rows → the centre point; its distance to Zn is one more `norm`.

**Check:** with the practice file mock_exam/ex4/zn_site.cif it prints:

``` plaintext
244 (244, 3)
ZN index: 234
40 S CYS 2.311
56 S CYS 2.290
172 N HIS 2.070
188 S CYS 2.330
235 O HOH 2.420
{'S': 3, 'N': 1, 'O': 1}
water bound: True
56 S 235 O 2.697
check: 2.696812006796174
mean Zn-ligand: 2.284
[12.6946 -3.4698 22.0066] 0.5320383068915239
```

**Adapt it:**

Another metal or cutoff → change `'ZN'` and `2.5`. Count residues instead of elements → use `residues[k]` as the key. Farthest pair → no `fill_diagonal`, use `argmax`. Mean of the bound distances by hand: `np.nanmean(dists[bound])`. More files of the same kind → wrap 4.2–4.5 in a loop over file names (see EX-PY-23).

`axis` reminder: `axis=1` collapses the columns (one value per atom, as in 4.2); `axis=0` collapses the rows (one mean point, as in 4.5).

### EX-PY-26 · MD log: time, kinetic, potential, temperature per block → total energy, linear drift (slope), plot with fit, mean/max T and the time of the max

**From:** Mock exam 2 (11 Nov), Ex 5 (2 points) · the practice file is mock_exam/ex5/md.log  
**Search:** MD log · Time (ps) · Kinetic energy · Potential energy · Temperature · one value per block · in line · split index · total energy · Euler drift · polyfit · slope · o-- black · fit line · legend · savefig · argmax · time of the max

`md.log` is the output of a molecular dynamics run. Every 25 steps the program prints a block with the time, the kinetic energy, the potential energy and the temperature:

``` plaintext
---------------- STEP     25 ----------------
  Time (ps)          =      0.050
  Kinetic energy     =     579.3477  kcal/mol
  Potential energy   =   -2719.4501  kcal/mol
  Temperature        =     299.94  K
```

**5.1.** (0.75) Read the time, the kinetic energy, the potential energy and the temperature of every block, and keep them in four arrays. Print how many blocks you read.

**5.2.** (0.5) Compute the total energy (kinetic + potential) of each block. The run used the Euler integrator, so the total energy drifts: fit the total energy against time with a straight line and print the drift (the slope) in kcal/mol/ps.

**5.3.** (0.5) Plot the total energy against time as black points joined by a dashed line, plus the fitted line, with a legend and the axis labels `Time (ps)` and `Total energy (kcal/mol)`. Save it as `energy.png`.

**5.4.** (0.25) Print the mean temperature, the maximum temperature, and the time at which the maximum occurs.

**Cues (the prompt says → you write):**

| Part | The prompt says                                               | You write                                                                                                                                     |
|------|---------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------|
| 5.1  | “every block … the time, the kinetic …” (one value per block) | pattern E1 (§5.1): loop over lines, one `if` per label, `append` the number                                                                   |
| 5.1  | lines start with spaces                                       | `'Time (ps)' in line`, not `line.startswith('Time')`                                                                                          |
| 5.1  | where the number is                                           | print `line.split()` once: `['Time', '(ps)', '=', '0.050']` → `[3]`; Kinetic/Potential → `[3]`; `['Temperature', '=', '299.94', 'K']` → `[2]` |
| 5.1  | “keep them in four arrays”                                    | four lists → `np.array(...)` each; `float()` on every value                                                                                   |
| 5.1  | “print how many blocks”                                       | `len(t)`                                                                                                                                      |
| 5.2  | “kinetic + potential … of each block”                         | `etot = ek + ep` (element by element)                                                                                                         |
| 5.2  | “fit … against time with a straight line”                     | `np.polyfit(t, etot, 1)`: x = time first, degree 1                                                                                            |
| 5.2  | “print the drift (the slope)”                                 | first value returned: `slope, intercept = ...`                                                                                                |
| 5.3  | “black points joined by a dashed line”                        | `plt.plot(t, etot, 'o--', color='k')`                                                                                                         |
| 5.3  | “plus the fitted line”                                        | `plt.plot(t, slope*t + intercept, '-')`                                                                                                       |
| 5.3  | “with a legend”                                               | `label=` on each `plot` + `plt.legend()`                                                                                                      |
| 5.3  | “save it as energy.png”                                       | `plt.savefig('energy.png')` **before** `plt.show()`                                                                                           |
| 5.4  | “mean / maximum temperature”                                  | `T.mean()`, `T[k]`                                                                                                                            |
| 5.4  | “the time at which the maximum occurs”                        | the **position** of the max: `k = np.argmax(T)` → `t[k]`                                                                                      |

Python

```python
import numpy as np
import matplotlib.pyplot as plt

# 5.1
def read_md(fname):
    time = []
    kinetic_en = []
    potential_en = []
    temp = []
    with open(fname) as f:
        for line in f:
            if "Time (ps)" in line:
                cols = line.split()
                time.append(float(cols[3]))
            elif "Kinetic energy" in line:
                cols = line.split()
                kinetic_en.append(float(cols[3]))
            elif "Potential energy" in line:
                cols = line.split()
                potential_en.append(float(cols[3]))
            elif "Temperature" in line:
                cols = line.split()
                temp.append(float(cols[2]))
    return np.array(time), np.array(kinetic_en), np.array(potential_en), np.array(temp)

t, ek, ep, T = read_md("md.log")
print(len(t), "blocks")

# 5.2
etot = ek + ep                                  # NumPy adds element by element: one total per block
slope, intercept = np.polyfit(t, etot, 1)       # x = time, y = energy, 1 = degree (a line)
print(f"drift = {slope:.3f} kcal/mol/ps")

# 5.3
plt.plot(t, etot, "o--", color="k", label="total energy")   # o = points, -- = dashed line
plt.plot(t, slope*t + intercept, "-", label="linear fit")    # y = m*x + b, solid line
plt.xlabel("Time (ps)")
plt.ylabel("Total energy (kcal/mol)")
plt.legend()                                    # shows the label= texts
plt.savefig("energy.png")
plt.show()                                      # after savefig, or you save a blank figure

# 5.4
k = np.argmax(T)                                # index of the largest temperature
print(f"mean T = {T.mean():.2f} K")
print(f"max T = {T[k]:.2f} K at t = {t[k]:.3f} ps")
```

**How it works:**

- **Calling the reader:** the file name is a string, so `read_md("md.log")` needs the quotes; without them Python looks for a variable called `md` (`NameError`).
- **5.1 Finding the lines:** every data line starts with two spaces, so `line.startswith("Time")` is never True; `"Time (ps)" in line` searches anywhere in the line. Use a label long enough to be unique (`"Temperature"` appears only once per block).
- **5.1 Picking the number:** `split()` cuts on spaces, so the label words count too: `Time (ps) = 0.050` → `['Time', '(ps)', '=', '0.050']` → index 3. `Kinetic energy = 579.3 kcal/mol` → index 3 (the unit is index 4). `Temperature = 299.94 K` → index 2. `float()` turns the text into a number; without it you get strings and `ek + ep` would join text.
- **5.1 Extra lines** (`>>> writing restart file`) contain none of the labels, so the `if`/`elif` chain skips them. `elif` stops checking once a label matched.
- **5.2** `ek + ep` adds two arrays of the same length position by position (block k with block k). `np.polyfit(x, y, 1)` fits y = slope·x + intercept and returns `[slope, intercept]`; unpack into two names. *“Against time”* means time is x, so it goes first. The slope is in energy units per time unit = kcal/mol/ps.
- **5.3** The format string `'o--'` = circle markers + dashed line; `color='k'` = black. The fit line is the straight-line formula evaluated at every `t`. `label=` gives each line its legend text; `plt.legend()` draws the box. `savefig` must come before `show`, because `show` clears the figure.
- **5.4** `T.max()` gives the value but not *where*; `np.argmax(T)` gives the position `k`. `t` and `T` were filled in the same loop, so `t[k]` is the time of that same block.

**Check:** with the practice file mock_exam/ex5/md.log it prints:

``` plaintext
41 blocks
drift = 3.411 kcal/mol/ps
mean T = 300.06 K
max T = 309.41 K at t = 1.150 ps
```

**Adapt it:**

Another quantity per block (pressure, volume…) → one more list + `elif` with its label; check its `split()` index by printing it once. Relative energy → `etot - etot[0]`. Units kcal/mol → kJ/mol: multiply by 4.184. Minimum and its time → `np.argmin`. Fit only part of the run → a mask, e.g. `m = t > 0.5` then `np.polyfit(t[m], etot[m], 1)`. Slope in the legend → `label=f"fit, slope {slope:.2f}"`.

No plot window on the exam machine (or a script) → `import matplotlib; matplotlib.use("Agg")` before `import matplotlib.pyplot`, and only `savefig`.

## 7.2 Fortran + make \[EX-F90\]

| ID                      | Problem                                                                                                        | From                                              |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------------------------------------------|
| [EX-F90-01](#ex-f90-01) | Enzyme kinetics: Michaelis–Menten vs competitive inhibition, two tables on screen + files                      | 2022-23 exam, Ex 3 (2 points) \[PAST\]            |
| [EX-F90-02](#ex-f90-02) | π two ways (Leibniz and Euler series), n read from the keyboard                                                | 2021-22 exam, Ex 5 (1.5 points) \[PAST\]          |
| [EX-F90-03](#ex-f90-03) | Ideal gas vs van der Waals pressure, two gases, two tables                                                     | Mock exam (Day 7), Ex 3                           |
| [EX-F90-04](#ex-f90-04) | Arrhenius vs modified Arrhenius: two tables with an internal subroutine                                        | Week notebook 2.3 (exam-style)                    |
| [EX-F90-05](#ex-f90-05) | Adsorption isotherms (Langmuir vs Freundlich), number of points read from the keyboard                         | Fortran 2-hour drill, B4 (exam-style, ≈ 2 points) |
| [EX-F90-06](#ex-f90-06) | Morse vs harmonic bond potential (H₂ and HCl), built step by step                                              | Day 0 notebook, B8 (worked exam-style problem)    |
| [EX-F90-07](#ex-f90-07) | Homework (Fortran assignment): read an .xyz file, largest distance, composition and mass, ≥ 3 files + Makefile | Class assignment `Fortran_Assignment_Updated.pdf` |
| [EX-F90-08](#ex-f90-08) | Molecule file, new questions: centre of mass, shortest C–H distance, bond count, report file                   | Fortran 2-hour drill, B5                          |
| [EX-F90-09](#ex-f90-09) | Warm-up: one function in its own file, table on screen + file, 4-rule Makefile                                 | Week notebook 2.1                                 |
| [EX-F90-10](#ex-f90-10) | Tables: read n, write i, i², √i to screen + file; then adapt to a real-valued x loop                           | Fortran 2-hour drill, B1 (1a, 1b)                 |
| [EX-F90-11](#ex-f90-11) | Functions and subroutines: Gaussian in its own file; a subroutine returning min, max, mean                     | Fortran 2-hour drill, B2 (2a, 2b)                 |
| [EX-F90-12](#ex-f90-12) | make: write a 5-rule Makefile; debug three broken Makefiles (TABs, module order, link line)                    | Fortran 2-hour drill, B3 (3a, 3b)                 |
| [EX-F90-13](#ex-f90-13) | Morse vs harmonic with parameters read from a file, two tables, lowest energy per table                        | Mock exam 2 (11 Nov), Ex 3 (2 points)             |

### EX-F90-01 · Enzyme kinetics: Michaelis–Menten vs competitive inhibition, two tables on screen + files

**From:** 2022-23 exam, Ex 3 (2 points) \[PAST\]  
**Search:** two functions separate files · two tables · screen and file · ES14.5 · Michaelis-Menten · inhibition · Makefile 4 rules

Make a Fortran program that calculates the following expressions of the rate of the enzymatic activity (v):

Expression 1: v₁ = k₂\[E₀\] · \[S\] / (K_m + \[S\])  
Expression 2: v₂ = k₂\[E₀\] · \[S\] / (K_m(1 + \[I\]/K_I) + \[S\])

The calculation of v₁ and v₂ expressions should be done by different functions and saved in separated files. The main program has to call the two functions and write two tables (on the screen and on separated files: *table1.out* and *table2.out*) of values with three columns (\[S\], v₁ and v₂). In both tables, \[S\] goes from 0.01 M to 0.1 M using increments of 0.01 M.  
For the first table, use the values of k₂=0.002, E₀=0.04 M, K_M=0.005, I=0.1 M, K_I=0.002  
For the second table, use the values of k₂=0.0005, E₀=0.06M, K_M=0.01, I=0.03 M, K_I=0.03  
Compilation must be done using make. The makefile must have a minimum of 4 rules.

v1.f90

```fortran
real(8) function v1(s, k2, e0, km)
  ! Michaelis-Menten: k2*[E0]*[S]/(Km + [S])
  implicit none
  real(8), intent(in) :: s, k2, e0, km
  v1 = k2*e0*s/(km + s)
end function v1
```

v2.f90

```fortran
real(8) function v2(s, k2, e0, km, ci, ki)
  ! competitive inhibition: k2*[E0]*[S]/(Km*(1 + [I]/KI) + [S])
  implicit none
  real(8), intent(in) :: s, k2, e0, km, ci, ki
  v2 = k2*e0*s/(km*(1.0d0 + ci/ki) + s)
end function v2
```

main.f90

```fortran
program enzyme
  implicit none
  real(8), external :: v1, v2
  real(8) :: s, k2, e0, km, ci, ki
  integer :: i

  ! ---------- table 1 ----------
  k2 = 0.002d0; e0 = 0.04d0; km = 0.005d0; ci = 0.1d0; ki = 0.002d0
  open(unit=10, file='table1.out', status='replace')
  write(*,'(A)') '=== table 1'
  write(*,'(A8,2A14)')  '[S](M)',  'v1', 'v2'
  write(10,'(A8,2A14)') '#[S](M)', 'v1', 'v2'
  do i = 1, 10
     s = 0.01d0*i
     write(*,'(F8.3,2ES14.5)')  s, v1(s, k2, e0, km), v2(s, k2, e0, km, ci, ki)
     write(10,'(F8.3,2ES14.5)') s, v1(s, k2, e0, km), v2(s, k2, e0, km, ci, ki)
  end do
  close(10)

  ! ---------- table 2 ----------
  k2 = 0.0005d0; e0 = 0.06d0; km = 0.01d0; ci = 0.03d0; ki = 0.03d0
  open(unit=11, file='table2.out', status='replace')
  write(*,'(/,A)') '=== table 2'
  write(*,'(A8,2A14)')  '[S](M)',  'v1', 'v2'
  write(11,'(A8,2A14)') '#[S](M)', 'v1', 'v2'
  do i = 1, 10
     s = 0.01d0*i
     write(*,'(F8.3,2ES14.5)')  s, v1(s, k2, e0, km), v2(s, k2, e0, km, ci, ki)
     write(11,'(F8.3,2ES14.5)') s, v1(s, k2, e0, km), v2(s, k2, e0, km, ci, ki)
  end do
  close(11)
end program enzyme
```

Makefile

```makefile
FC = gfortran
FFLAGS = -O2 -Wall
OBJ = main.o v1.o v2.o

enzyme: $(OBJ)
	$(FC) $(FFLAGS) -o enzyme $(OBJ)

main.o: main.f90
	$(FC) $(FFLAGS) -c main.f90

v1.o: v1.f90
	$(FC) $(FFLAGS) -c v1.f90

v2.o: v2.f90
	$(FC) $(FFLAGS) -c v2.f90

clean:
	rm -f *.o enzyme *.out
```

**How it works:**

- **v1.f90 / v2.f90:** each formula is a `real(8) function` in its own file; every input is an argument with `intent(in)`.
- **main.f90:** `real(8), external :: v1, v2` tells main these functions live in other files. For each table: set the parameters, open the file, write a header, loop `i = 1, 10` with `s = 0.01d0*i`, and write the same line to the screen (`*`) and the file (unit 10/11).
- **Makefile:** one rule per object file + the link rule + `clean` = ≥ 4 rules; `make` rebuilds only what changed.

**Check:** run it unchanged and it prints:

``` plaintext
=== table 1
  [S](M)            v1            v2
   0.010   5.33333E-05   3.01887E-06
   0.020   6.40000E-05   5.81818E-06
   0.030   6.85714E-05   8.42105E-06
...
=== table 2
  [S](M)            v1            v2
   0.010   1.50000E-05   1.00000E-05
   0.020   2.00000E-05   1.50000E-05
   0.030   2.25000E-05   1.80000E-05
...
table1.out
table2.out
```

**Adapt it:**

**This is the master template** (same shape as EX-F90-03, 04, 05, 06). Change: the two function bodies and arguments; the loop (`do i = 1, N` + `x = start + step*i`); the parameter values; file names; column headers. Values spanning orders of magnitude → `ES14.5`, otherwise `F12.5`.

### EX-F90-02 · π two ways (Leibniz and Euler series), n read from the keyboard

**From:** 2021-22 exam, Ex 5 (1.5 points) \[PAST\]  
**Search:** pi · Leibniz · Euler series · read n · acos(-1) · factorial overflow · term recurrence · advance=no

Make a Fortran program that calculates the number π with these two expressions:

Expression 1 (Leibniz): Σₙ₌₀^∞ (−1)ⁿ/(2n+1) = 1/1 − 1/3 + 1/5 − 1/7 + 1/9 − … = π/4  
Expression 2 (Euler): Σₙ₌₀^∞ 2ⁿ n!² / (2n+1)! = 1 + 1/3 + (1·2)/(3·5) + (1·2·3)/(3·5·7) + … = π/2

The main program must ask for the number of terms (*n*) to use to estimate the value of pi number from both expressions.  
The main program has to call two functions (pi_1 and pi_2) written in two different files and write the result of both approximations to the screen. The value of pi obtained from arccosine of -1 should be also written on the screen.  
Compilation must be done using make. The makefile must have a minimum of 4 rules.

*Extra from the week notebook:* run with n = 20 and n = 1 000 000. Which series converges faster? (Euler's terms: each term = previous × n/(2n+1), so no factorials are needed.)

main.f90

```fortran
program pi_main
  implicit none
  real(8), external :: pi_1, pi_2
  integer :: n
  write(*,'(A)', advance='no') 'Number of terms n: '
  read(*,*) n
  write(*,*)
  write(*,'(A,F18.15)') 'Leibniz  pi_1(n) = ', pi_1(n)
  write(*,'(A,F18.15)') 'Euler    pi_2(n) = ', pi_2(n)
  write(*,'(A,F18.15)') 'acos(-1)         = ', acos(-1.0d0)
end program pi_main
```

pi1.f90

```fortran
real(8) function pi_1(n)
  ! Leibniz: sum_{k=0}^{n-1} (-1)^k/(2k+1) = pi/4      (n terms)
  implicit none
  integer, intent(in) :: n
  integer :: k
  real(8) :: s
  s = 0.0d0
  do k = 0, n-1
     s = s + (-1.0d0)**k / (2.0d0*k + 1.0d0)
  end do
  pi_1 = 4.0d0*s
end function pi_1
```

pi2.f90

```fortran
real(8) function pi_2(n)
  ! Euler: sum_{k=0}^{n-1} 2^k (k!)^2/(2k+1)! = pi/2   (n terms)
  ! term_k = term_{k-1} * k/(2k+1)  -> no factorials, no overflow
  implicit none
  integer, intent(in) :: n
  integer :: k
  real(8) :: s, t
  t = 1.0d0
  s = t
  do k = 1, n-1
     t = t * dble(k)/dble(2*k + 1)
     s = s + t
  end do
  pi_2 = 2.0d0*s
end function pi_2
```

Makefile

```makefile
FC = gfortran
FFLAGS = -O2 -Wall
OBJ = main.o pi1.o pi2.o

pi: $(OBJ)
	$(FC) $(FFLAGS) -o pi $(OBJ)

main.o: main.f90
	$(FC) $(FFLAGS) -c main.f90

pi1.o: pi1.f90
	$(FC) $(FFLAGS) -c pi1.f90

pi2.o: pi2.f90
	$(FC) $(FFLAGS) -c pi2.f90

clean:
	rm -f *.o pi
```

**How it works:**

- **main.f90** asks for n (`advance='no'` keeps the prompt on the same line), calls both functions, and prints `acos(-1.0d0)` as the exact value.
- **pi1.f90 (Leibniz):** sum of (−1)ᵏ/(2k+1) for k = 0…n−1, times 4. The sign alternates with `(-1)**k` (or a variable that flips).
- **pi2.f90 (Euler):** each term is the previous one times k/(2k+1), starting from 1. That avoids computing n! (which overflows after ~20); the sum is times 2.
- **Makefile:** 3 object rules + link + clean.

**Check:** run it unchanged with n = 20 and with n = 1000000 (`echo 20 | ./pi`) and it prints:

``` plaintext
Leibniz  pi_1(n) =  3.091623806667840
Euler    pi_2(n) =  3.141591927675146
acos(-1)         =  3.141592653589793
--- n = 1000000:
Leibniz  pi_1(n) =  3.141591653589774
Euler    pi_2(n) =  3.141592653589792
acos(-1)         =  3.141592653589793
```

**Adapt it:**

Euler gains about 0.3 digits per term (each term is roughly half the previous one), so about 20 terms give 6 digits.  
Leibniz's error is about 1/n, so 6 correct digits need about a million terms.

Run: `make && echo 20 | ./pi`. Any series with factorials → build each term from the previous one (never compute n! directly).

### EX-F90-03 · Ideal gas vs van der Waals pressure, two gases, two tables

**From:** Mock exam (Day 7), Ex 3 · solution written and tested for these notes  
**Search:** ideal gas · van der Waals · pressure · volume loop 0.1 to 1.0 · CO2 He · two tables · Makefile run rule

Pressure of 1 mol of gas at T = 300 K (R = 0.082057 L·atm·mol⁻¹·K⁻¹):  
P1 = nRT/V (ideal) P2 = nRT/(V − nb) − a·n²/V² (van der Waals).  
P1 and P2 are computed by functions in **separate files**. Main writes two tables (V, P1, P2) for V = 0.1 to 1.0 L in steps of 0.1 L, on screen and in `co2.out` / `he.out`.  
CO₂: a = 3.640 L²·atm·mol⁻², b = 0.04267 L·mol⁻¹. He: a = 0.0346, b = 0.0238. Use make with ≥ 4 rules.

p1.f90

```fortran
real(8) function p1(v, n, t)
  ! ideal gas: P = nRT/V   (atm, with V in L)
  implicit none
  real(8), intent(in) :: v, n, t
  real(8), parameter :: R = 0.082057d0
  p1 = n*R*t/v
end function p1
```

p2.f90

```fortran
real(8) function p2(v, n, t, a, b)
  ! van der Waals: P = nRT/(V - nb) - a n^2/V^2
  implicit none
  real(8), intent(in) :: v, n, t, a, b
  real(8), parameter :: R = 0.082057d0
  p2 = n*R*t/(v - n*b) - a*n**2/v**2
end function p2
```

main.f90

```fortran
program gases
  implicit none
  real(8), external :: p1, p2
  real(8) :: v, n, t, a, b
  integer :: i

  n = 1.0d0
  t = 300.0d0

  ! ---------- table 1: CO2 ----------
  a = 3.640d0; b = 0.04267d0
  open(unit=10, file='co2.out', status='replace')
  write(*,'(A)') '=== CO2'
  write(*,'(A8,2A14)')  'V(L)',  'P_ideal', 'P_vdW'
  write(10,'(A8,2A14)') '#V(L)', 'P_ideal', 'P_vdW'
  do i = 1, 10
     v = 0.1d0*i
     write(*,'(F8.2,2F14.5)')  v, p1(v, n, t), p2(v, n, t, a, b)
     write(10,'(F8.2,2F14.5)') v, p1(v, n, t), p2(v, n, t, a, b)
  end do
  close(10)

  ! ---------- table 2: He ----------
  a = 0.0346d0; b = 0.0238d0
  open(unit=11, file='he.out', status='replace')
  write(*,'(/,A)') '=== He'
  write(*,'(A8,2A14)')  'V(L)',  'P_ideal', 'P_vdW'
  write(11,'(A8,2A14)') '#V(L)', 'P_ideal', 'P_vdW'
  do i = 1, 10
     v = 0.1d0*i
     write(*,'(F8.2,2F14.5)')  v, p1(v, n, t), p2(v, n, t, a, b)
     write(11,'(F8.2,2F14.5)') v, p1(v, n, t), p2(v, n, t, a, b)
  end do
  close(11)
end program gases
```

Makefile

```makefile
FC = gfortran
FFLAGS = -O2 -Wall
OBJ = main.o p1.o p2.o

gases: $(OBJ)
	$(FC) $(FFLAGS) -o gases $(OBJ)

main.o: main.f90
	$(FC) $(FFLAGS) -c main.f90

p1.o: p1.f90
	$(FC) $(FFLAGS) -c p1.f90

p2.o: p2.f90
	$(FC) $(FFLAGS) -c p2.f90

run: gases
	./gases

clean:
	rm -f *.o gases *.out
```

**How it works:**

- **p1.f90 / p2.f90:** one formula per function; R is a `parameter` inside each.
- **main.f90:** n and T are fixed; for each gas set a and b, open its file, write a header, loop `i = 1, 10` with `v = 0.1d0*i` (V = 0.1 … 1.0 L), and write V, P₁, P₂ to the screen and the file.
- **Makefile:** object rules, link rule, `run`, `clean`.

**Check:** run it unchanged and it prints:

``` plaintext
=== CO2
    V(L)       P_ideal         P_vdW
    0.10     246.17100      65.39299
    0.20     123.08550      65.46793
    0.30      82.05700      55.21910
...
=== He
    V(L)       P_ideal         P_vdW
    0.10     246.17100     319.59906
    0.20     123.08550     138.84612
    0.30      82.05700      88.74336
...
co2.out
he.out
```

**Adapt it:**

Check: CO₂ at V = 0.1 L gives P_ideal = 246.17100, P_vdW = 65.39299 atm. Constants that never change (R) → `real(8), parameter` inside the function.

### EX-F90-04 · Arrhenius vs modified Arrhenius: two tables with an internal subroutine

**From:** Week notebook 2.3 (exam-style)  
**Search:** Arrhenius · rate constant · temperature loop · internal subroutine contains · table subroutine · run rule

This has the same structure as an exam question. Two expressions for a rate constant:

k₁(T) = A·exp(−Eₐ/(R·T)) k₂(T) = A·(T/T₀)ᵐ·exp(−Eₐ/(R·T)), with R = 8.314 J mol⁻¹K⁻¹ and T₀ = 298 K.

- `k1` and `k2` are functions in **separate files** that take the parameters as arguments.
- Main writes **two tables** with three columns (T, k₁, k₂), for T = 300 to 400 K in steps of 10 K, on the screen **and** in `table1.out` / `table2.out`.
- Table 1: A = 1.0×10¹³ s⁻¹, Eₐ = 80 000 J/mol, m = 0.5. Table 2: A = 5.0×10¹⁰ s⁻¹, Eₐ = 50 000 J/mol, m = 1.5.
- Makefile with ≥ 4 rules. Bonus: a `run` rule.

Self-check: `make clean && make` works from scratch, and after `touch k1.f90`, `make` recompiles only `k1.o` and relinks.

main.f90

```fortran
program arrhenius
  implicit none
  call table('table1.out', 1.0d13, 8.0d4, 0.5d0)
  call table('table2.out', 5.0d10, 5.0d4, 1.5d0)
contains
  subroutine table(fname, A, Ea, m)
    character(len=*), intent(in) :: fname
    real(8), intent(in) :: A, Ea, m
    real(8), external :: k1, k2
    real(8) :: T
    integer :: i
    open(unit=10, file=fname, status='replace')
    write(*,'(/,A)') '=== ' // fname
    write(*,'(A10,2A14)')  'T(K)',  'k1(s-1)', 'k2(s-1)'
    write(10,'(A10,2A14)') '#T(K)', 'k1(s-1)', 'k2(s-1)'
    do i = 0, 10
       T = 300.0d0 + 10.0d0*i
       write(*,'(F10.1,2ES14.5)')  T, k1(T, A, Ea), k2(T, A, Ea, m)
       write(10,'(F10.1,2ES14.5)') T, k1(T, A, Ea), k2(T, A, Ea, m)
    end do
    close(10)
  end subroutine table
end program arrhenius
```

k1.f90

```fortran
real(8) function k1(T, A, Ea)
  implicit none
  real(8), intent(in) :: T, A, Ea
  real(8), parameter :: R = 8.314d0
  k1 = A*exp(-Ea/(R*T))
end function k1
```

k2.f90

```fortran
real(8) function k2(T, A, Ea, m)
  implicit none
  real(8), intent(in) :: T, A, Ea, m
  real(8), parameter :: R = 8.314d0, T0 = 298.0d0
  k2 = A*(T/T0)**m * exp(-Ea/(R*T))
end function k2
```

Makefile

```makefile
FC = gfortran
FFLAGS = -O2 -Wall
OBJ = main.o k1.o k2.o

arrh: $(OBJ)
	$(FC) $(FFLAGS) -o arrh $(OBJ)

main.o: main.f90
	$(FC) $(FFLAGS) -c main.f90

k1.o: k1.f90
	$(FC) $(FFLAGS) -c k1.f90

k2.o: k2.f90
	$(FC) $(FFLAGS) -c k2.f90

run: arrh
	./arrh

clean:
	rm -f *.o arrh *.out
```

**How it works:**

- **k1/k2** are functions in their own files, R (and T₀) as parameters.
- **main.f90** calls an internal subroutine `table(fname, A, Ea, m)` twice. The subroutine (after `contains`) opens the file, writes the header, loops T = 300…400 K in steps of 10, and writes to screen and file. A new parameter set = one more `call table(...)` line.

**Check:** run it unchanged and it prints:

``` plaintext
=== table1.out
      T(K)       k1(s-1)       k2(s-1)
     300.0   1.17560E-01   1.17954E-01
     310.0   3.30831E-01   3.37426E-01
...
=== table2.out
      T(K)       k1(s-1)       k2(s-1)
     300.0   9.83744E+01   9.93664E+01
     310.0   1.87812E+02   1.99270E+02
...
table1.out
table2.out
```

**Adapt it:**

Writing the loop twice in main, without the internal subroutine, is also perfectly acceptable in the exam. It's just longer.

With `call table(...)`, a third parameter set is one more line.

### EX-F90-05 · Adsorption isotherms (Langmuir vs Freundlich), number of points read from the keyboard

**From:** Fortran 2-hour drill, B4 (exam-style, ≈ 2 points)  
**Search:** Langmuir · Freundlich · read N · step 1/N · two tables · p\*\*(1/n) integer division trap

Surface coverage θ of a gas adsorbed on a solid, from two models:

Langmuir: θ₁ = K·P / (1 + K·P) Freundlich: θ₂ = K_F·P^(1/n)

- θ₁ and θ₂ are computed by **functions written in separate files** (`langmuir.f90`, `freundlich.f90`).
- The main program **asks for the number of points N**. P goes from 1/N to 1.0 atm in steps of 1/N.
- It writes **two tables** with three columns (P, θ₁, θ₂), **on the screen and in the files** `iso1.out` and `iso2.out`.
- Set 1: K = 2.0, K_F = 0.8, n = 2. Set 2: K = 10.0, K_F = 1.2, n = 3.
- Compile with **make; the Makefile must have at least 4 rules**.

Check: with N = 10, the first row of set 1 is 0.10000 0.16667 0.25298.

langmuir.f90

```fortran
real(8) function langmuir(p, k)
  ! Langmuir coverage: K*P/(1 + K*P)
  implicit none
  real(8), intent(in) :: p, k
  langmuir = k*p/(1.0d0 + k*p)
end function langmuir
```

freundlich.f90

```fortran
real(8) function freundlich(p, kf, n)
  ! Freundlich coverage: KF*P**(1/n)
  implicit none
  real(8), intent(in) :: p, kf, n
  freundlich = kf*p**(1.0d0/n)
end function freundlich
```

main.f90

```fortran
program isotherms
  implicit none
  real(8), external :: langmuir, freundlich
  real(8) :: p, dp, k, kf, n
  integer :: npts, i

  write(*,'(A)', advance='no') 'Number of points: '
  read(*,*) npts
  dp = 1.0d0/npts                       ! P goes from dp to 1.0 atm

  ! ---------- table 1 ----------
  k = 2.0d0; kf = 0.8d0; n = 2.0d0
  open(unit=10, file='iso1.out', status='replace')
  write(*,'(/,A)') '=== set 1'
  write(*,'(3A12)')  'P(atm)',  'Langmuir', 'Freundlich'
  write(10,'(3A12)') '#P(atm)', 'Langmuir', 'Freundlich'
  do i = 1, npts
     p = dp*i
     write(*,'(3F12.5)')  p, langmuir(p, k), freundlich(p, kf, n)
     write(10,'(3F12.5)') p, langmuir(p, k), freundlich(p, kf, n)
  end do
  close(10)

  ! ---------- table 2 ----------
  k = 10.0d0; kf = 1.2d0; n = 3.0d0
  open(unit=11, file='iso2.out', status='replace')
  write(*,'(/,A)') '=== set 2'
  write(*,'(3A12)')  'P(atm)',  'Langmuir', 'Freundlich'
  write(11,'(3A12)') '#P(atm)', 'Langmuir', 'Freundlich'
  do i = 1, npts
     p = dp*i
     write(*,'(3F12.5)')  p, langmuir(p, k), freundlich(p, kf, n)
     write(11,'(3F12.5)') p, langmuir(p, k), freundlich(p, kf, n)
  end do
  close(11)
end program isotherms
```

Makefile

```makefile
FC = gfortran
FFLAGS = -O2 -Wall
OBJ = main.o langmuir.o freundlich.o

iso: $(OBJ)
	$(FC) $(FFLAGS) -o iso $(OBJ)

main.o: main.f90
	$(FC) $(FFLAGS) -c main.f90

langmuir.o: langmuir.f90
	$(FC) $(FFLAGS) -c langmuir.f90

freundlich.o: freundlich.f90
	$(FC) $(FFLAGS) -c freundlich.f90

clean:
	rm -f *.o iso *.out
```

**How it works:**

- **main** reads N, then P = i/N for i = 1…N (so P goes from 1/N to 1).
- **freundlich.f90:** `P**(1.0d0/n)`. Writing `1/n` with an integer n would be integer division (= 0), so the exponent must be real.
- Two tables (two parameter sets), each to the screen and its own file; Makefile with ≥ 4 rules.

**Check:** run it unchanged with N = 10 (`echo 10 | ./iso`) and it prints:

``` plaintext
=== set 1
      P(atm)    Langmuir  Freundlich
     0.10000     0.16667     0.25298
     0.20000     0.28571     0.35777
...
=== set 2
      P(atm)    Langmuir  Freundlich
     0.10000     0.50000     0.55699
     0.20000     0.66667     0.70176
...
iso1.out
iso2.out
```

**Adapt it:**

Run with `make && echo 10 | ./iso`. Note `p**(1.0d0/n)`: with an integer `n`, `1/n` would be 0 (integer division), so n is real(8) here.

### EX-F90-06 · Morse vs harmonic bond potential (H₂ and HCl), built step by step

**From:** Day 0 notebook, B8 (worked exam-style problem)  
**Search:** Morse · harmonic · potential · r loop 0.5 to 2.5 · two molecules · a\*(r-re) implicit multiplication

Write a Fortran program that compares two models of a chemical bond's potential energy:

Morse: V1(r) = De·(1 − exp(−a(r − re)))² Harmonic: V2(r) = ½·k·(r − re)², with k = 2·De·a²

Each expression is a function in a separate file. The main program writes two tables (r, V1, V2) for r = 0.5 to 2.5 Å in steps of 0.1 Å, on the screen and in `h2.out` / `hcl.out`. H₂: De = 4.75 eV, a = 1.94 Å⁻¹, re = 0.741 Å. HCl: De = 4.62 eV, a = 1.87 Å⁻¹, re = 1.275 Å. Compile with make (≥ 4 rules).

morse.f90

```fortran
real(8) function morse(r, De, a, re)
  ! Morse potential: De*(1 - exp(-a*(r-re)))**2
  implicit none
  real(8), intent(in) :: r, De, a, re
  morse = De*(1.0d0 - exp(-a*(r - re)))**2
end function morse
```

harm.f90

```fortran
real(8) function harm(r, De, a, re)
  ! Harmonic approximation of the Morse potential, k = 2*De*a**2
  implicit none
  real(8), intent(in) :: r, De, a, re
  real(8) :: k
  k = 2.0d0*De*a**2
  harm = 0.5d0*k*(r - re)**2
end function harm
```

main.f90

```fortran
program bond
  implicit none
  real(8), external :: morse, harm
  real(8) :: r, De, a, re
  integer :: i

  ! ---------- table 1: H2 ----------
  De = 4.75d0; a = 1.94d0; re = 0.741d0
  open(unit=10, file='h2.out', status='replace')
  write(*,'(A)') '=== H2'
  write(*,'(A8,2A12)')  'r(A)',  'V_morse', 'V_harm'
  write(10,'(A8,2A12)') '#r(A)', 'V_morse', 'V_harm'
  do i = 0, 20
     r = 0.5d0 + 0.1d0*i
     write(*,'(F8.3,2F12.5)')  r, morse(r, De, a, re), harm(r, De, a, re)
     write(10,'(F8.3,2F12.5)') r, morse(r, De, a, re), harm(r, De, a, re)
  end do
  close(10)

  ! ---------- table 2: HCl ----------
  De = 4.62d0; a = 1.87d0; re = 1.275d0
  open(unit=11, file='hcl.out', status='replace')
  write(*,'(/,A)') '=== HCl'
  write(*,'(A8,2A12)')  'r(A)',  'V_morse', 'V_harm'
  write(11,'(A8,2A12)') '#r(A)', 'V_morse', 'V_harm'
  do i = 0, 20
     r = 0.5d0 + 0.1d0*i
     write(*,'(F8.3,2F12.5)')  r, morse(r, De, a, re), harm(r, De, a, re)
     write(11,'(F8.3,2F12.5)') r, morse(r, De, a, re), harm(r, De, a, re)
  end do
  close(11)
end program bond
```

Makefile

```makefile
FC = gfortran
FFLAGS = -O2 -Wall
OBJ = main.o morse.o harm.o

bond: $(OBJ)
	$(FC) $(FFLAGS) -o bond $(OBJ)

main.o: main.f90
	$(FC) $(FFLAGS) -c main.f90

morse.o: morse.f90
	$(FC) $(FFLAGS) -c morse.f90

harm.o: harm.f90
	$(FC) $(FFLAGS) -c harm.f90

run: bond
	./bond

clean:
	rm -f *.o bond *.out
```

**How it works:**

- **morse.f90:** De\*(1 − exp(−a\*(r−re)))\*\*2. The `*` before the bracket is required, or Fortran reads `a(...)` as an array.
- **harm.f90** computes k = 2·De·a² inside, then ½k(r−re)².
- **main.f90:** 21 points r = 0.5 … 2.5 via `do i = 0, 20` and `r = 0.5d0 + 0.1d0*i`; two molecules = two parameter sets = two tables.

**Check:** run it unchanged and it prints:

``` plaintext
=== H2
    r(A)     V_morse      V_harm
   0.500     1.68763     1.03832
   0.600     0.47015     0.35541
   0.700     0.03256     0.03005
   0.800     0.05556     0.06223
...
=== HCl
    r(A)     V_morse      V_harm
   0.500    49.09705     9.70350
   0.600    29.65082     7.36093
...
h2.out
hcl.out
```

**Adapt it:**

Math → Fortran: `a(r − re)` must be `a*(r - re)`; otherwise Fortran reads `a(...)` as an array. 21 values from 0.5 to 2.5 → `do i = 0, 20` with `r = 0.5d0 + 0.1d0*i`. Sanity check: both ≈ 0 at r = re.

### EX-F90-07 · Homework (Fortran assignment): read an .xyz file, largest distance, composition and mass, ≥ 3 files + Makefile

**From:** Class assignment `Fortran_Assignment_Updated.pdf` · modular version written and tested for these notes; your single-file submission is below it  
**Search:** xyz · read molecule · allocatable · largest interatomic distance · count C H O · molecular mass · modules · use · Makefile module dependency

A .xyz file has the number of atoms, a title line, then one line per atom: symbol x y z.

- **a)** (1 pt) Read `molecule_B.xyz` and print its contents.
- **b)** (3 pts) The largest distance between any two atoms: the atom indices, symbols and the distance in Å.
- **c)** (3 pts) Count the C, H and O atoms and compute the molecular mass (C = 12.0, H = 1.0, O = 16.0).
- **d)** (3 pts) At least **three** source files, modular code, compiled with a Makefile; `make` builds the executable.

xyz_io.f90

```fortran
module xyz_io
  ! reading and printing an .xyz file
  implicit none
contains
  subroutine read_xyz(fname, nat, title, sym, xyz)
    character(len=*), intent(in)               :: fname
    integer, intent(out)                       :: nat
    character(len=100), intent(out)            :: title
    character(len=2), allocatable, intent(out) :: sym(:)
    real(8), allocatable, intent(out)          :: xyz(:,:)   ! xyz(3, nat)
    integer :: i, ios
    open(unit=10, file=fname, status='old', action='read', iostat=ios)
    if (ios /= 0) then
       print *, 'Error: cannot open ', trim(fname)
       stop
    end if
    read(10,*) nat
    read(10,'(A)') title
    allocate(sym(nat), xyz(3,nat))
    do i = 1, nat
       read(10,*) sym(i), xyz(:,i)
    end do
    close(10)
  end subroutine read_xyz

  subroutine print_xyz(nat, title, sym, xyz)
    integer, intent(in)            :: nat
    character(len=*), intent(in)   :: title
    character(len=2), intent(in)   :: sym(:)
    real(8), intent(in)            :: xyz(:,:)
    integer :: i
    write(*,'(I0)') nat
    write(*,'(A)') trim(title)
    do i = 1, nat
       write(*,'(I3,2X,A2,3F12.5)') i, sym(i), xyz(:,i)
    end do
  end subroutine print_xyz
end module xyz_io
```

geometry.f90

```fortran
module geometry
  ! distances between atoms
  implicit none
contains
  real(8) function distance(a, b)
    real(8), intent(in) :: a(3), b(3)
    distance = sqrt(sum((a - b)**2))
  end function distance

  subroutine largest_distance(xyz, imax, jmax, dmax)
    real(8), intent(in)  :: xyz(:,:)        ! xyz(3, nat)
    integer, intent(out) :: imax, jmax
    real(8), intent(out) :: dmax
    integer :: i, j, nat
    real(8) :: d
    nat = size(xyz, 2)
    dmax = 0.0d0; imax = 0; jmax = 0
    do i = 1, nat - 1
       do j = i + 1, nat                    ! every pair once
          d = distance(xyz(:,i), xyz(:,j))
          if (d > dmax) then
             dmax = d; imax = i; jmax = j
          end if
       end do
    end do
  end subroutine largest_distance
end module geometry
```

composition.f90

```fortran
module composition
  ! counting atoms and the molecular mass
  implicit none
contains
  subroutine count_atoms(sym, nC, nH, nO)
    character(len=2), intent(in) :: sym(:)
    integer, intent(out)         :: nC, nH, nO
    integer :: i
    nC = 0; nH = 0; nO = 0
    do i = 1, size(sym)
       select case (trim(sym(i)))
       case ('C')
          nC = nC + 1
       case ('H')
          nH = nH + 1
       case ('O')
          nO = nO + 1
       case default
          print *, 'Warning: unexpected symbol ', sym(i), ' at atom ', i
       end select
    end do
  end subroutine count_atoms

  real(8) function molar_mass(nC, nH, nO)
    integer, intent(in) :: nC, nH, nO
    molar_mass = 12.0d0*nC + 1.0d0*nH + 16.0d0*nO
  end function molar_mass
end module composition
```

main.f90

```fortran
program molecule
  use xyz_io
  use geometry
  use composition
  implicit none
  integer :: nat, imax, jmax, nC, nH, nO
  character(len=100) :: title
  character(len=2), allocatable :: sym(:)
  real(8), allocatable :: xyz(:,:)
  real(8) :: dmax

  ! a) read and print
  call read_xyz('molecule_B.xyz', nat, title, sym, xyz)
  call print_xyz(nat, title, sym, xyz)

  ! b) largest interatomic distance
  call largest_distance(xyz, imax, jmax, dmax)
  write(*,*)
  write(*,'(A,I0,3A,I0,3A)') 'Largest distance: atoms ', imax, ' (', trim(sym(imax)), ') and ', &
                             jmax, ' (', trim(sym(jmax)), ')'
  write(*,'(A,F10.6,A)') 'Distance: ', dmax, ' Angstrom'

  ! c) composition and mass
  call count_atoms(sym, nC, nH, nO)
  write(*,*)
  write(*,'(A,I0)') 'C atoms: ', nC
  write(*,'(A,I0)') 'H atoms: ', nH
  write(*,'(A,I0)') 'O atoms: ', nO
  write(*,'(A,F8.2)') 'Molecular mass: ', molar_mass(nC, nH, nO)
end program molecule
```

Makefile

```makefile
FC = gfortran
FFLAGS = -O2 -Wall
OBJ = xyz_io.o geometry.o composition.o main.o

molecule: $(OBJ)
	$(FC) $(FFLAGS) -o molecule $(OBJ)

xyz_io.o: xyz_io.f90
	$(FC) $(FFLAGS) -c xyz_io.f90

geometry.o: geometry.f90
	$(FC) $(FFLAGS) -c geometry.f90

composition.o: composition.f90
	$(FC) $(FFLAGS) -c composition.f90

main.o: main.f90 xyz_io.o geometry.o composition.o
	$(FC) $(FFLAGS) -c main.f90

run: molecule
	./molecule

clean:
	rm -f *.o *.mod molecule
```

Your submitted single-file version: molecule.f90

```fortran
! Molecular geometry analysis program - Justin Boyd

program molecule
  implicit none

  integer                       :: natoms, ios, i, j, imax, jmax
  integer                       :: nC, nH, nO
  character(len=100)            :: title
  character(len=2), allocatable :: symbol(:)
  real(8),          allocatable :: xyz(:,:)
  real(8)                       :: d, dmax, mass

  open(unit=10, file="molecule_B.xyz", status="old", action="read", iostat=ios)
  if (ios /= 0) then
     print *, "Error: cannot open molecule_B.xyz"
     stop
  end if

  read(10, *) natoms
  read(10, '(a)') title

  allocate(symbol(natoms), xyz(3, natoms))

  do i = 1, natoms
     read(10, *) symbol(i), xyz(1, i), xyz(2, i), xyz(3, i)
  end do

  close(10)

  print *, "Number of atoms:", natoms
  print *, "Title: ", trim(title)
  do i = 1, natoms
     print '(i3, 2x, a2, 3f12.5)', i, symbol(i), xyz(:, i)
  end do

  dmax = 0.0d0
  imax = 0
  jmax = 0
  do i = 1, natoms - 1
     do j = i + 1, natoms
        d = sqrt(sum((xyz(:, i) - xyz(:, j))**2))
        if (d > dmax) then
           dmax = d
           imax = i
           jmax = j
        end if
     end do
  end do

  print *
  print '(a, i0, 3a, i0, 3a)', "Largest distance: atoms ", imax, " (", trim(symbol(imax)), ") and ", &
        jmax, " (", trim(symbol(jmax)), ")"
  print '(a, f10.6, a)', "Distance: ", dmax, " Angstrom"

  nC = 0
  nH = 0
  nO = 0
  do i = 1, natoms
     select case (trim(symbol(i)))
     case ("C")
        nC = nC + 1
     case ("H")
        nH = nH + 1
     case ("O")
        nO = nO + 1

     case default
        print *, "Warning: atom ", i, " has an unexpected symbol: ", symbol(i)
     end select
  end do

  mass = 12.0d0 * nC + 1.0d0 * nH + 16.0d0 * nO

  print *
  print '(a, i0)', "C atoms: ", nC
  print '(a, i0)', "H atoms: ", nH
  print '(a, i0)', "O atoms: ", nO
  print '(a, f8.2)', "Molecular mass: ", mass

end program molecule
```

Your submitted Makefile

```makefile
FC = gfortran
FFLAGS = -Wall -O2

molecule: molecule.f90
	$(FC) $(FFLAGS) molecule.f90 -o molecule

run: molecule
	./molecule

clean:
	rm -f molecule

.PHONY: run clean
```

**How it works:**

- **xyz_io.f90** (module): `read_xyz` reads the atom count, the title, allocates the arrays, then reads `symbol x y z` per atom; `print_xyz` prints them (part a).
- **geometry.f90** (module): `distance` between two points; `largest_distance` loops over every pair i\<j and keeps the largest (part b).
- **composition.f90** (module): `count_atoms` uses `select case` on each symbol; `molar_mass` = 12·nC + 1·nH + 16·nO (part c).
- **main.f90** `use`s the three modules and calls each step. **Makefile:** `main.o` depends on the module objects, because compiling a module creates the `.mod` file main needs (part d).

**Check:** run it unchanged on the class file `molecule_B.xyz` (ethanol); last lines and it prints:

``` plaintext
Largest distance: atoms 2 (H) and 9 (H)
Distance:   4.069570 Angstrom

C atoms: 2
H atoms: 6
O atoms: 1
Molecular mass:    46.00
```

**Adapt it:**

Result for ethanol (`molecule_B.xyz`): largest distance atoms 2 (H) and 9 (H), 4.069570 Å; C 2, H 6, O 1; mass 46.00. Both versions give the same output. Your submission is one file, so it doesn’t meet part d); the modular version does. With modules: `main.o` must depend on the module `.o` files, and `clean` removes `*.mod`.

### EX-F90-08 · Molecule file, new questions: centre of mass, shortest C–H distance, bond count, report file

**From:** Fortran 2-hour drill, B5  
**Search:** centre of mass · masses · shortest C-H · pair of different elements · bonds \< 1.6 · report.txt · huge

Reuse your reader from the class evaluation (or the reference). Read `b5/molecule_B.xyz` (ethanol), then:

- **a)** Compute the **centre of mass** (masses C = 12, H = 1, O = 16). Hint: an array `m(nat)`, then `sum(m*xyz(j,:))/sum(m)` for each j.
- **b)** Find the **shortest C–H distance**, and which atoms (indices) are involved.
- **c)** Count the **bonds**: atom pairs closer than 1.6 Å (each pair once).
- **d)** Write the three results to the screen **and** to `report.txt`.

Expected: centre of mass ≈ (3.1791, −0.2469, −0.1483) · shortest C–H 1.1105 Å (atoms 1 and 2) · 8 bonds.

report.f90

```fortran
program report
  implicit none
  integer :: nat, i, j, ic, ih, nbonds
  character(len=80) :: title
  character(len=2), allocatable :: sym(:)
  real(8), allocatable :: xyz(:,:), m(:)
  real(8) :: com(3), d, dmin

  ! ---------- read ----------
  open(unit=10, file='molecule_B.xyz', status='old')
  read(10,*) nat
  read(10,'(A)') title
  allocate(sym(nat), xyz(3,nat), m(nat))
  do i = 1, nat
     read(10,*) sym(i), xyz(:,i)
  end do
  close(10)

  ! ---------- a) centre of mass ----------
  do i = 1, nat
     if (trim(sym(i)) == 'C') m(i) = 12.0d0
     if (trim(sym(i)) == 'H') m(i) = 1.0d0
     if (trim(sym(i)) == 'O') m(i) = 16.0d0
  end do
  do j = 1, 3
     com(j) = sum(m*xyz(j,:))/sum(m)
  end do

  ! ---------- b) shortest C-H distance ----------
  dmin = huge(1.0d0)
  do i = 1, nat
     do j = 1, nat
        if (trim(sym(i)) == 'C' .and. trim(sym(j)) == 'H') then
           d = sqrt(sum((xyz(:,i) - xyz(:,j))**2))
           if (d < dmin) then
              dmin = d; ic = i; ih = j
           end if
        end if
     end do
  end do

  ! ---------- c) bonds: pairs closer than 1.6 A ----------
  nbonds = 0
  do i = 1, nat - 1
     do j = i + 1, nat
        if (sqrt(sum((xyz(:,i) - xyz(:,j))**2)) < 1.6d0) nbonds = nbonds + 1
     end do
  end do

  ! ---------- d) write the report to screen and file ----------
  open(unit=20, file='report.txt', status='replace')
  write(*,'(A,3F10.4)')  'Centre of mass:', com
  write(20,'(A,3F10.4)') 'Centre of mass:', com
  write(*,'(A,F8.4,A,I0,A,I0)')  'Shortest C-H:', dmin, '  atoms ', ic, ' and ', ih
  write(20,'(A,F8.4,A,I0,A,I0)') 'Shortest C-H:', dmin, '  atoms ', ic, ' and ', ih
  write(*,'(A,I0)')  'Bonds (< 1.6 A): ', nbonds
  write(20,'(A,I0)') 'Bonds (< 1.6 A): ', nbonds
  close(20)
end program report
```

**How it works:**

- **a)** centre of mass = Σ mᵢrᵢ / Σ mᵢ, with the mass chosen from the symbol.
- **b)** loop over all pairs, keep only those where one atom is C and the other is H, track the smallest distance (start from `huge(1.0d0)`).
- **c)** count pairs i\<j with distance \< 1.6 Å.
- **d)** each result is written twice: to `*` and to the report file's unit.

**Check:** run it unchanged on the class file `molecule_B.xyz` (ethanol) and it prints:

``` plaintext
Centre of mass:    3.1791   -0.2469   -0.1483
Shortest C-H:  1.1105  atoms 1 and 2
Bonds (< 1.6 A): 8
```

**Adapt it:**

b\) uses a full i×j loop with a symbol test (C first, H second), because here the order of the pair matters. c) uses `j = i+1` so each pair is counted once.

### EX-F90-09 · Warm-up: one function in its own file, table on screen + file, 4-rule Makefile

**From:** Week notebook 2.1  
**Search:** function separate file · real(8) external · table 0.0 to 1.0 · header line \# · Makefile

Make a table of x and g(x) = sin(x)·e^(−x) for x = 0.0, 0.1, …, 1.0 (11 rows), on screen **and** in `warmup.out`. Put `g` in its own file, `g.f90`. Write a Makefile with ≥ 4 rules.

main.f90

```fortran
program main
  implicit none
  real(8), external :: g
  real(8) :: x
  integer :: i
  open(unit=10, file='warmup.out', status='replace')
  write(*,'(2A12)')  'x', 'g(x)'
  write(10,'(2A12)') '#x', 'g(x)'
  do i = 0, 10
     x = 0.1d0*i
     write(*,'(2F12.6)')  x, g(x)
     write(10,'(2F12.6)') x, g(x)
  end do
  close(10)
end program main
```

g.f90

```fortran
real(8) function g(x)
  implicit none
  real(8), intent(in) :: x
  g = sin(x)*exp(-x)
end function g
```

Makefile

```makefile
FC = gfortran
FFLAGS = -O2 -Wall

prog: main.o g.o
	$(FC) $(FFLAGS) -o prog main.o g.o

main.o: main.f90
	$(FC) $(FFLAGS) -c main.f90

g.o: g.f90
	$(FC) $(FFLAGS) -c g.f90

clean:
	rm -f *.o prog *.out
```

**How it works:**

- **g.f90:** a `real(8) function` with one argument.
- **main.f90:** `external` declaration, open the file, header line starting with `#` (so gnuplot ignores it), loop `i = 0, 10` with `x = 0.1d0*i`, write to the screen and the file.
- **Makefile:** two object rules, the link rule, `clean`.

**Check:** run it unchanged and it prints:

``` plaintext
           x        g(x)
    0.000000    0.000000
    0.100000    0.090333
    0.200000    0.162657
```

### EX-F90-10 · Tables: read n, write i, i², √i to screen + file; then adapt to a real-valued x loop

**From:** Fortran 2-hour drill, B1 (1a, 1b)  
**Search:** read n · table · integer loop · sqrt(dble(i)) · adapt loop · x step 0.05

**1a** Read n from the keyboard. Write a table with columns i, i², √i for i = 1..n, **on the screen and in `t1.out`**, each with a header line (in the file, start the header with `#`).

**1b — adaptation (the key exam skill).** Copy your 1a into a new file and change it to: x **from 0.05 to 0.5 in steps of 0.05**, with columns x, e^(−x), x·e^(−x), written to `t2.out`, and **no keyboard input**. Time yourself: aim for under 4 minutes.

1a

```fortran
program table1
  implicit none
  integer :: n, i
  write(*,'(A)', advance='no') 'n = '
  read(*,*) n
  open(unit=10, file='t1.out', status='replace')
  write(*,'(A5,2A12)')  'i', 'i**2', 'sqrt(i)'
  write(10,'(A5,2A12)') '#i', 'i**2', 'sqrt(i)'
  do i = 1, n
     write(*,'(I5,I12,F12.5)')  i, i**2, sqrt(dble(i))
     write(10,'(I5,I12,F12.5)') i, i**2, sqrt(dble(i))
  end do
  close(10)
end program table1
```

1b

```fortran
program table2
  implicit none
  integer :: i
  real(8) :: x
  open(unit=10, file='t2.out', status='replace')
  write(*,'(3A12)')  'x', 'exp(-x)', 'x*exp(-x)'
  write(10,'(3A12)') '#x', 'exp(-x)', 'x*exp(-x)'
  do i = 1, 10
     x = 0.05d0*i
     write(*,'(3F12.5)')  x, exp(-x), x*exp(-x)
     write(10,'(3F12.5)') x, exp(-x), x*exp(-x)
  end do
  close(10)
end program table2
```

**How it works:**

- **1a:** read n; integer loop; `sqrt` needs a real argument, so `sqrt(dble(i))`; each row written to screen and file.
- **1b** (adaptation): the loop counter stays an integer and the real x is computed from it (`x = 0.05d0*i`), which avoids rounding drift from adding 0.05 repeatedly.

**Adapt it:**

1b, what changed from 1a: no `read`; the loop is `do i = 1, 10` with `x = 0.05d0*i`; the formats became `3F12.5`; and the file name.

### EX-F90-11 · Functions and subroutines: Gaussian in its own file; a subroutine returning min, max, mean

**From:** Fortran 2-hour drill, B2 (2a, 2b)  
**Search:** gauss · function separate file · compile by hand · subroutine contains · intent(out) · assumed shape a(:) · minval maxval

**2a — exam pattern.** Write `gauss(x, mu, sigma)` = exp(−½((x−μ)/σ)²) / (σ√(2π)) in **its own file**, `b2/sep/gauss.f90`. In `b2/sep/main.f90`, print x and gauss(x, 0, 1) for x = −2.0 to 2.0 in steps of 0.5. Compile both files by hand. Check: gauss(0, 0, 1) ≈ 0.398942.

**2b** Write a subroutine `summary(a, lo, hi, avg)` placed after `contains`, which takes an array of **any length** and returns its min, max and mean (`intent(out)`). Test it on \[3.1, −2.0, 7.5, 0.4, 5.5, −1.2, 2.2, 6.0\]; expected: −2.000, 7.500, 2.688.

gauss.f90

```fortran
real(8) function gauss(x, mu, sigma)
  ! normalised Gaussian
  implicit none
  real(8), intent(in) :: x, mu, sigma
  real(8), parameter :: pi = 3.14159265358979d0
  gauss = exp(-0.5d0*((x - mu)/sigma)**2) / (sigma*sqrt(2.0d0*pi))
end function gauss
```

main.f90

```fortran
program gtable
  implicit none
  real(8), external :: gauss
  real(8) :: x
  integer :: i
  do i = -4, 4
     x = 0.5d0*i
     write(*,'(2F12.6)') x, gauss(x, 0.0d0, 1.0d0)
  end do
end program gtable
```

2b.f90

```fortran
program arraystats
  implicit none
  real(8) :: x(8) = [3.1d0, -2.0d0, 7.5d0, 0.4d0, 5.5d0, -1.2d0, 2.2d0, 6.0d0]
  real(8) :: lo, hi, avg
  call summary(x, lo, hi, avg)
  write(*,'(3(A,F8.3))') 'min =', lo, '   max =', hi, '   mean =', avg
contains
  subroutine summary(a, lo, hi, avg)
    real(8), intent(in)  :: a(:)
    real(8), intent(out) :: lo, hi, avg
    lo  = minval(a)
    hi  = maxval(a)
    avg = sum(a)/size(a)
  end subroutine summary
end program arraystats
```

**How it works:**

- **2a:** `gauss.f90` holds the function; `main.f90` declares it `external` and loops over x. Compiled by hand: `-c` each file, then link both `.o` files.
- **2b:** a subroutine after `contains` returns three values through `intent(out)` arguments; `a(:)` (assumed shape) accepts an array of any length; `minval`, `maxval`, `sum(a)/size(a)` do the work.

**Adapt it:**

2a: the loop is `do i = -4, 4` with `x = 0.5d0*i` (negative loop bounds are fine).

Compile by hand: `gfortran -c gauss.f90 && gfortran -c main.f90 && gfortran main.o gauss.o -o gtable`.

### EX-F90-12 · make: write a 5-rule Makefile; debug three broken Makefiles (TABs, module order, link line)

**From:** Fortran 2-hour drill, B3 (3a, 3b)  
**Search:** Makefile · missing separator · Cannot open module file · undefined reference · touch · make run · make clean

**3a** Write a Makefile for **2a** (`b2/sep/`) with variables `FC` and `FFLAGS`, a rule per object file, the link rule, a `run` rule and `clean` (5 rules). Then: `rm -f *.o gtable` (you compiled by hand in 2a) → `make` → `make` again (nothing happens) → `touch gauss.f90; make` (what gets rebuilt?) → `make run` → `make clean`.

**3b — debug.** `b3/` contains a module `mathmod.f90` (function `sq`), an external function `cube.f90`, and `main.f90` (which does `use mathmod`). Run the three broken Makefiles below and read each error. Write down **what's wrong** with each one, then write a correct `b3/Makefile` (using variables, plus `clean`) and run it.

3a Makefile

```makefile
FC = gfortran
FFLAGS = -O2 -Wall
OBJ = main.o gauss.o

gtable: $(OBJ)
	$(FC) $(FFLAGS) -o gtable $(OBJ)

main.o: main.f90
	$(FC) $(FFLAGS) -c main.f90

gauss.o: gauss.f90
	$(FC) $(FFLAGS) -c gauss.f90

run: gtable
	./gtable

clean:
	rm -f *.o gtable
```

3a commands

```bash
rm -f *.o gtable
make
make                 # 'gtable' is up to date.
touch gauss.f90
make                 # recompiles gauss.o only, then relinks
make run
make clean
```

3b fixed Makefile (module + external function)

```makefile
FC = gfortran
FFLAGS = -O2 -Wall
OBJ = mathmod.o main.o cube.o

powers: $(OBJ)
	$(FC) $(FFLAGS) -o powers $(OBJ)

mathmod.o: mathmod.f90
	$(FC) $(FFLAGS) -c mathmod.f90

main.o: main.f90 mathmod.o
	$(FC) $(FFLAGS) -c main.f90

cube.o: cube.f90
	$(FC) $(FFLAGS) -c cube.f90

clean:
	rm -f *.o *.mod powers
```

**How it works:**

- **3a:** variables `FC`/`FFLAGS`, one rule per `.o`, the link rule, `run` and `clean`. `touch` a file and re-run `make` to see that only that file recompiles.
- **3b** fixes the three classic errors: recipe lines must start with a **TAB** (`missing separator`); the module must compile before the files that `use` it (`Cannot open module file`); the link line must include **every** object file (`undefined reference`).

**Adapt it:**

**3b:** a) `missing separator`: the recipes are indented with **spaces**, not a TAB. b) `Cannot open module file 'mathmod.mod'`: `main.o` doesn't depend on `mathmod.o`, so make compiles main first. Fix: `main.o: main.f90 mathmod.o`. c) `undefined reference to 'cube_'`: `cube.o` is missing from the **link** line. Remember `rm -f *.mod` in `clean` when there's a module.

### EX-F90-13 · Morse vs harmonic: parameters read from molecules.dat (comment line), two tables r = 0.8–4.0, lowest Morse energy after each table

**From:** Mock exam 2 (11 Nov), Ex 3 (2 points)  
**Search:** read parameters from a file · molecules.dat · skip comment line read(10,\*) · two functions separate files · real(8) external · two tables screen and file · r 0.8 to 4.0 step 0.2 · integer loop · lowest energy · track minimum · huge · Makefile clean

Make a Fortran program that calculates two expressions for the energy of a chemical bond as a function of the distance r:

Morse: V_M(r) = De · (1 − exp(−a·(r − re)))²
Harmonic: V_H(r) = ½ · k · (r − re)², with k = 2·De·a²

The calculation of V_M and V_H must be done by different functions saved in separate files. The parameters De, a and re of two molecules must be **read from the file `molecules.dat`** (the first line is a comment; then one line per molecule: name, De, a, re). For each molecule the main program writes a table (on the screen and in the files `table1.out` and `table2.out`) with three columns: r, V_M and V_H, with r going from 0.8 to 4.0 bohr in steps of 0.2 bohr. After each table, print on the screen the value of r with the lowest Morse energy in the table and that energy.

Compilation must be done using make. The makefile must have a minimum of 4 rules, including one that removes the compiled files.

``` plaintext
# name   De (hartree)   a (1/bohr)   re (bohr)
H2       0.1745         1.0282       1.4011
HCl      0.1696         0.9892       2.4086
```

**Cues (the prompt says → you write):**

| The prompt says                                          | You write                                                                                               |
|----------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
| “different functions saved in separate files”            | `morse.f90`, `harmonic.f90`, each `real(8) function …`; in main: `real(8), external :: morse, harmonic` |
| the formulas                                             | `de*(1.0d0 - exp(-a*(r - re)))**2` and `0.5d0*(2.0d0*de*a**2)*(r - re)**2` (`0.5d0`, never `1/2`)       |
| “read from the file molecules.dat”                       | `open(unit=10, file='molecules.dat', status='old')`                                                     |
| “the first line is a comment”                            | an empty `read(10, *)` skips one line                                                                   |
| “one line per molecule: name, De, a, re”                 | `read(10, *) name, de, a, re` inside `do m = 1, 2` (`character(len=10) :: name`)                        |
| “a table … on the screen and in the files”               | every `write` twice: `write(*, fmt)` and `write(u, fmt)`; header line in the file starts with `#`       |
| “r from 0.8 to 4.0 in steps of 0.2”                      | integer loop `do i = 0, 16` + `r = 0.8d0 + 0.2d0*i` (count: (4.0 − 0.8)/0.2 + 1 = 17 rows)              |
| “the value of r with the lowest Morse energy”            | track a minimum: `vmin = huge(1.0d0)`, then `if (vm < vmin)` remember `vmin` and `rmin`                 |
| “make … minimum 4 rules, one removes the compiled files” | link + 3 compile rules + `run` + `clean`, `.PHONY: run clean`                                           |

morse.f90

```fortran
real(8) function morse(r, de, a, re)
  implicit none
  real(8), intent(in) :: r, de, a, re       ! intent(in): the function only reads them
  morse = de*(1.0d0 - exp(-a*(r - re)))**2  ! the result is assigned to the function's name
end function morse
```

harmonic.f90

```fortran
real(8) function harmonic(r, de, a, re)
  implicit none
  real(8), intent(in) :: r, de, a, re
  harmonic = 0.5d0*(2.0d0*de*a**2)*(r - re)**2   ! k = 2*De*a**2 computed inline
end function harmonic
```

main.f90

```fortran
program bonds
  implicit none
  real(8), external :: morse, harmonic        ! tells main these live in other files
  character(len=10) :: name
  real(8) :: de, a, re, r, vm, rmin, vmin
  integer :: m, i, u

  open(unit=10, file='molecules.dat', status='old')
  read(10, *)                                 ! empty read = skip the comment line

  do m = 1, 2                                 ! one pass per molecule
     read(10, *) name, de, a, re              ! list-directed: splits on spaces
     u = 20 + m                               ! unit 21 for table1, 22 for table2
     if (m == 1) open(unit=u, file='table1.out', status='replace')
     if (m == 2) open(unit=u, file='table2.out', status='replace')

     write(*,*) '=== ', trim(name)
     write(*,'(A8,2A14)') 'r(bohr)', 'Morse', 'harmonic'
     write(u,'(A8,2A14)') '#r(bohr)', 'Morse', 'harmonic'   ! # = comment for gnuplot
     vmin = huge(1.0d0)                       ! start the minimum at "infinity"
     do i = 0, 16                             ! 17 points: 0.8, 1.0, ..., 4.0
        r = 0.8d0 + 0.2d0*i                   ! integer loop avoids real rounding errors
        vm = morse(r, de, a, re)
        write(*,'(F8.2,2F14.6)') r, vm, harmonic(r, de, a, re)
        write(u,'(F8.2,2F14.6)') r, vm, harmonic(r, de, a, re)
        if (vm < vmin) then                   ! new lowest -> remember it
           vmin = vm
           rmin = r
        end if
     end do
     close(u)
     write(*,'(A,F5.2,A,F10.6)') 'Lowest Morse: r = ', rmin, '  V = ', vmin
  end do
  close(10)
end program bonds
```

Makefile

```makefile
FC = gfortran
OBJ = main.o morse.o harmonic.o

bonds: $(OBJ)
	$(FC) -o bonds $(OBJ)

main.o: main.f90
	$(FC) -c main.f90

morse.o: morse.f90
	$(FC) -c morse.f90

harmonic.o: harmonic.f90
	$(FC) -c harmonic.f90

run: bonds
	./bonds

clean:
	rm -f *.o bonds *.out

.PHONY: run clean
```

**How it works:**

- **The functions:** `real(8) function morse(...)` declares the return type in the first line; the value is returned by assigning to the function's own name. `intent(in)` says the arguments are only read. Write every constant with `d0` (`1.0d0`, `0.5d0`): `1/2` is integer division and gives 0, and `0.5` alone is single precision.
- **`real(8), external :: morse, harmonic`** in main: the functions are compiled from other files, so main must be told their type, otherwise implicit typing (or `implicit none`) gives an error or a wrong type.
- **Reading the file:** `status='old'` = the file must already exist. `read(10, *)` with no variables reads one line and throws it away, which skips the comment. `read(10, *) name, de, a, re` is list-directed: it splits the next line on spaces into the four variables. `name` must be `character(len=…)`.
- **Two tables with one loop:** `do m = 1, 2` reads one molecule per pass. `u = 20 + m` gives a different unit for each output file; the two `if` lines open the right file name. `status='replace'` overwrites old results.
- **Screen and file:** each line is written twice with the same format: `*` = the screen, `u` = the file. The file's header starts with `#` so gnuplot skips it. `(A8,2A14)` = one text field 8 wide, then two 14 wide; `(F8.2,2F14.6)` = one real 8 wide with 2 decimals, then two reals 14 wide with 6 decimals, so the columns line up under the headers.
- **The r loop:** loop over an integer and compute r from it; a real-valued loop (`r = r + 0.2`) collects rounding errors and can miss the last point 4.0. From 0.8 to 4.0 in steps of 0.2 is 16 steps, so `i = 0, 16`.
- **The minimum:** start `vmin` at the largest possible number (`huge(1.0d0)`) so the first value always replaces it; every smaller value updates both `vmin` and the `r` where it happened. Reset `vmin` for each molecule (inside the `m` loop).
- **Makefile:** the first rule (`bonds`) is what `make` builds; it depends on the three `.o` files, and each `.o` has its own compile rule (`-c` = compile only). `run` and `clean` are not files, hence `.PHONY`. The command lines must start with a **TAB**. That makes 6 rules (≥ 4 asked).

**Check:** `make && ./bonds` with the molecules.dat above prints (17 rows per table):

``` plaintext
 === H2
 r(bohr)         Morse      harmonic
    0.80      0.127656      0.066657
    ...
Lowest Morse: r =  1.40  V =   0.000000
 === HCl
    ...
Lowest Morse: r =  2.40  V =   0.000012
```

**Adapt it:**

Parameters typed in the code instead of read (EX-F90-06) → replace the `open`/`read` lines with assignments. More molecules → `do m = 1, n` and build the file name with `write(fname,'(A,I0,A)') 'table', m, '.out'`. Highest instead of lowest → `vmin = -huge(1.0d0)` and `>`. Different r range → recompute the number of steps: (end − start)/step, and use it as the loop's upper limit.

Error `Unclassifiable statement` at a formula → a missing `*` (`a(r-re)` must be `a*(r-re)`). `Missing separator` from make → spaces instead of a TAB.

## 7.3 Bash \[EX-SH\]

| ID                    | Problem                                                                                                                    | From                                                                |
|-----------------------|----------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------|
| [EX-SH-01](#ex-sh-01) | Three-way script: file → lines; directory → copy .f90 into it; neither → report of “import” counts                         | 2022-23 exam, Ex 1.2 \[PAST\]                                       |
| [EX-SH-02](#ex-sh-02) | Swap lines 2 and 3 of every .swap23 file, in place                                                                         | 2022-23 exam, Ex 1.1 \[PAST\]                                       |
| [EX-SH-03](#ex-sh-03) | Ask for two file names and print the larger one                                                                            | 2021-22 exam, Ex 3.2 \[PAST\]                                       |
| [EX-SH-04](#ex-sh-04) | One line: lines containing x_pos but not y_pos                                                                             | 2021-22 exam, Ex 3.1 \[PAST\]                                       |
| [EX-SH-05](#ex-sh-05) | One line: count .log files with “Normal termination”; script: lines per file for an extension, total, backup folder        | Mock exam (Day 7), Ex 1                                             |
| [EX-SH-06](#ex-sh-06) | Three-way script: file → words; directory → copy .sh into it; neither → report of “real” counts                            | Linux drills 4.6 (exam pattern)                                     |
| [EX-SH-07](#ex-sh-07) | Three-way script: file → words; directory → number of .f90 files in it; neither → report of lines with “real”              | Week notebook 1.4                                                   |
| [EX-SH-08](#ex-sh-08) | Swap lines 1 and 3 of every .swap13 file                                                                                   | Linux drills 4.4 (exam pattern)                                     |
| [EX-SH-09](#ex-sh-09) | Swap the first and last line of every .alter file (class exercise 2)                                                       | Class exercise 2 — your file `ex2_swap_lines.sh`                    |
| [EX-SH-10](#ex-sh-10) | Lines with the word “for” of every .py file → name.forY.txt (≥ 10 times) or name.forN.txt (class exercise 3)               | Class exercise 3 — your file `ex3_for_lines.sh`                     |
| [EX-SH-11](#ex-sh-11) | Sign of a number, then compare a second number with it (class exercise 1)                                                  | Class exercise 1 — your file `ex1_numbers.sh`                       |
| [EX-SH-12](#ex-sh-12) | Job checker: OK / FAILED per .log file, then the totals                                                                    | Week notebook 1.5                                                   |
| [EX-SH-13](#ex-sh-13) | Folder as an argument (\$1, default .): count .f90, .py and .txt files; error + exit 1 if not a directory                  | Linux drills 4.8                                                    |
| [EX-SH-14](#ex-sh-14) | Ask for a word, count it in every .f90 and .py file, report the file with the most (built step by step)                    | Day 0 notebook, A12 (worked example)                                |
| [EX-SH-15](#ex-sh-15) | One-liners: lines per file, A but not B, lines vs occurrences of a word, the Nth line                                      | Week notebook 1.1                                                   |
| [EX-SH-16](#ex-sh-16) | Class Linux sheet, part 1: listing and counting                                                                            | Linux drills Section 1 (class exercises 1, 2, 10, 11)               |
| [EX-SH-17](#ex-sh-17) | Class Linux sheet, part 2: creating files, permissions, diff                                                               | Linux drills Section 2 (class exercises 3, 6, 12, 13, 14)           |
| [EX-SH-18](#ex-sh-18) | Class Linux sheet, part 3: filtering and cutting                                                                           | Linux drills Section 3 (class exercises 4, 5, 7, 8, 9 + past exams) |
| [EX-SH-19](#ex-sh-19) | Move the first line of every .rot file to the end, in place                                                                | Mock exam 2 (11 Nov), Ex 1.1 (0.75 points)                          |
| [EX-SH-20](#ex-sh-20) | Three-way script: file → lines + compare a number; directory → def lines of every .py → defY/defN (≥ 3); neither → message | Mock exam 2 (11 Nov), Ex 1.2 (1 point)                              |
| [EX-SH-21](#ex-sh-21) | One line: count the lines with the word do but not end                                                                     | Mock exam 2 (11 Nov), Ex 1.3 (0.25 points)                          |

### EX-SH-01 · Three-way script: file → lines; directory → copy .f90 into it; neither → report of “import” counts

**From:** 2022-23 exam, Ex 1.2 \[PAST\]  
**Search:** read -p · if -f · elif -d · else · cp \*.f90 · create a file · for loop redirected done \> · grep -ow \| wc -l

**3.2.** Make a script asking for the name of a file. Is the file exists, the script should display the number of lines of this file. If the name of file corresponds to an existing directory, the script should copy of the files with .f90 extension to that directory. And if the name is neither a file nor a directory, then the script should generate a file with this name with the following content: the name of all files with extension “.py” with the number of times that the word “import” appears.

*(The sheet numbers it 3.2 under Exercise 1.)*

script

```bash
#!/bin/bash
# file -> number of lines; directory -> copy the .f90 files into it;
# neither -> create a file listing every .py file and how many times the word "import" appears
read -p "Name: " name
if [ -f "$name" ]; then
    echo "$name has $(wc -l < "$name") lines"
elif [ -d "$name" ]; then
    cp *.f90 "$name"
    echo "copied the .f90 files to $name"
else
    for f in *.py; do
        echo "$f $(grep -ow import "$f" | wc -l)"
    done > "$name"
    echo "created $name"
fi
```

**How it works:**

- `read -p` asks for the name. `[ -f ]` tests for a file → `wc -l <` prints only the line count.
- `[ -d ]` tests for a directory → `cp *.f90 "$name"/`.
- Otherwise, the `for` loop prints one line per `.py` file with `grep -ow import | wc -l` (each occurrence on its own line, then counted), and `done > "$name"` sends the whole loop's output into the new file.

**Adapt it:**

**This is the master template** for script questions (also EX-SH-06, 07). Swap the three bodies: words `wc -w`, lines `wc -l`, bytes `wc -c`; “number of times the word X” → `grep -ow X "$f" | wc -l`; “number of lines containing X” → `grep -c X "$f"`. `done > "$name"` writes the whole loop’s output to the file.

### EX-SH-02 · Swap lines 2 and 3 of every .swap23 file, in place

**From:** 2022-23 exam, Ex 1.1 \[PAST\]  
**Search:** swap lines · sed -n Np · temp file · mv · every file with an extension · rearrange lines

**3.1.** Make a script that swaps the position of the second and third line for all the files that have an “.swap23” extension.

*(The sheet numbers it 3.1 under Exercise 1.)*

script

```bash
#!/bin/bash
# swap the 2nd and 3rd line of every .swap23 file
for f in *.swap23; do
    [ -f "$f" ] || continue
    { sed -n '1p' "$f"; sed -n '3p' "$f"; sed -n '2p' "$f"; sed -n '4,$p' "$f"; } > tmp && mv tmp "$f"
done
```

**How it works:**

- For each `.swap23` file, `sed -n` prints line 1, then 3, then 2, then lines 4 to the end (`4,$p`) into a temporary file.
- `mv` replaces the original with the reordered copy.

**Adapt it:**

Any rearrangement → print the pieces in the new order into a temp file, then `mv`. Lines 1 and 3 → `3p; 2p; 1p; 4,$p` (EX-SH-08). First and last → EX-SH-09.

### EX-SH-03 · Ask for two file names and print the larger one

**From:** 2021-22 exam, Ex 3.2 \[PAST\]  
**Search:** larger file · size in bytes · wc -c \< · compare integers -gt · read -p two inputs

**3.2** Write a script asking for the name of two files. You should then display the name of the file that has a larger size. (For simplicity, you can make a script where it is assumed that the files entered by the user exist in the directory, and that the files always have different sizes).

larger.sh

```bash
#!/bin/bash
read -p "First file: " f1
read -p "Second file: " f2
s1=$(wc -c < "$f1")
s2=$(wc -c < "$f2")
if [ "$s1" -gt "$s2" ]; then
    echo "$f1 is larger ($s1 bytes vs $s2)"
else
    echo "$f2 is larger ($s2 bytes vs $s1)"
fi
```

**How it works:**

- Two `read -p` prompts store the names.
- `wc -c < "$f"` prints only the number of bytes (with `<`, `wc` doesn't print the file name).
- `-gt` compares the two integers; the larger name is printed.

**Adapt it:**

Bigger by lines → `wc -l <`; by words → `wc -w <`. With `<`, `wc` prints only the number. Test with `printf "a\nb\n" | bash larger.sh`.

### EX-SH-04 · One line: lines containing x_pos but not y_pos

**From:** 2021-22 exam, Ex 3.1 \[PAST\]  
**Search:** grep A but not B · grep -v · pipe

**3.1** Write a command (in one line) that identifies all the lines in a file called *program.f90* that contains the *x_pos* variable, and at the same time do not contain the *y_pos* variable.

command

```bash
grep x_pos program.f90 | grep -v y_pos
```

**How it works:**

- The first `grep` keeps lines with `x_pos`; the pipe passes them to `grep -v y_pos`, which removes any that also contain `y_pos`.

**Adapt it:**

Both words → `grep A f | grep B`. Whole word only → add `-w`. Count them → add `| wc -l` or use `grep -c` on the last grep.

### EX-SH-05 · One line: count .log files with “Normal termination”; script: lines per file for an extension, total, backup folder

**From:** Mock exam (Day 7), Ex 1 · solutions written and tested for these notes  
**Search:** grep -l \| wc -l · read extension · no files message · total lines · arithmetic \$(( )) · mkdir if not exists · cp

1.1 Write a **one-line** command that prints how many `.log` files in `bash_play/` contain the text `Normal termination`.  
1.2 Write a script that asks for a file extension (e.g. `f90`). If there are no files with that extension, it prints a message. Otherwise it prints the name and number of lines of each file, then the **total** number of lines. Finally it creates a directory `<ext>_backup` (only if it doesn't exist yet) and copies the files into it.

1.1

```bash
grep -l "Normal termination" bash_play/*.log | wc -l
```

1.2 ext_report.sh

```bash
#!/bin/bash
# ext_report.sh - lines of every file with a given extension, the total, then a backup copy
read -p "Extension (without the dot, e.g. f90): " ext
if ! ls *."$ext" > /dev/null 2>&1; then
    echo "There are no .$ext files here"
    exit 0
fi
total=0
for f in *."$ext"; do
    n=$(wc -l < "$f")
    echo "$f $n"
    total=$((total + n))
done
echo "Total: $total lines"
if [ ! -d "${ext}_backup" ]; then
    mkdir "${ext}_backup"
fi
cp *."$ext" "${ext}_backup"
echo "Copied the .$ext files to ${ext}_backup/"
```

**How it works:**

- **1.1** `grep -l` prints each matching file name once, so `wc -l` counts files.
- **1.2** `ls *."$ext" > /dev/null 2>&1` fails if no file matches → message and exit. The loop prints each file with `wc -l <` and adds it to `total` with `$(( ))`. The backup folder is created only if `[ ! -d ]`, then everything is copied in.

**Adapt it:**

`grep -l` lists each matching file once, so `wc -l` counts files. “Only if it doesn’t exist” → `if [ ! -d dir ]` (or `mkdir -p dir`). “No files with that extension” → test with `ls *."$ext" > /dev/null 2>&1`.

### EX-SH-06 · Three-way script: file → words; directory → copy .sh into it; neither → report of “real” counts

**From:** Linux drills 4.6 (exam pattern)  
**Search:** three-way · wc -w · cp \*.sh · word count real · create report file

`three_way.sh` — *(exam pattern)* Ask for a name. If it is a **file**, print its number of words. If it is a **directory**, copy every `.sh` file into it. Otherwise, **create a file** with that name containing, for each `.f90` file, its name and the number of times the word `real` appears.

three_way.sh

```bash
# 4.6 three_way.sh
#!/bin/bash
read -p "Name: " name
if [ -f "$name" ]; then
    echo "$name has $(wc -w < "$name") words"
elif [ -d "$name" ]; then
    cp *.sh "$name"
    echo "copied .sh files to $name"
else
    for f in *.f90; do
        echo "$f $(grep -ow real "$f" | wc -l)"
    done > "$name"
    echo "created $name"
fi
```

**How it works:**

- Same three branches as EX-SH-01: `[ -f ]` → `wc -w <` (words); `[ -d ]` → `cp *.sh`; else a loop over `.py` files counting the word `real` with `grep -ow | wc -l`, redirected into a new file with `done >`.

### EX-SH-07 · Three-way script: file → words; directory → number of .f90 files in it; neither → report of lines with “real”

**From:** Week notebook 1.4  
**Search:** three-way · count files in a directory · ls dir/\*.f90 \| wc -l · grep -c

Write `inspect.sh`. It asks for a name, then:

- if it is a **file**: prints its number of words;
- if it is a **directory**: prints how many `.f90` files it contains;
- otherwise: **creates a file** with that name containing, for every `.f90` file in the current directory, the file name followed by the number of lines containing `real`.

inspect.sh

```bash
#!/bin/bash
read -p "Name: " name
if [ -f "$name" ]; then
    echo "$name has $(wc -w < "$name") words"
elif [ -d "$name" ]; then
    n=$(ls "$name"/*.f90 2>/dev/null | wc -l)
    echo "$name contains $n .f90 files"
else
    for f in *.f90; do
        echo "$f $(grep -c real "$f")"
    done > "$name"
    echo "created $name"
fi
```

**How it works:**

- `[ -f ]` → words with `wc -w <`.
- `[ -d ]` → `ls "$name"/*.f90 | wc -l` counts the `.f90` files inside (errors hidden with `2>/dev/null` when there are none).
- Otherwise one line per `.f90` file with `grep -c real` (lines containing `real`), all redirected into the new file with `done >`.

### EX-SH-08 · Swap lines 1 and 3 of every .swap13 file

**From:** Linux drills 4.4 (exam pattern)  
**Search:** swap lines 1 and 3 · sed -n · temp file · mv

`swap13.sh` — *(exam pattern)* In every `.swap13` file, swap **lines 1 and 3**; all the other lines stay where they are.

swap13.sh

```bash
# 4.4 swap13.sh
#!/bin/bash
# swap lines 1 and 3 of every .swap13 file
for f in *.swap13; do
    { sed -n '3p' "$f"; sed -n '2p' "$f"; sed -n '1p' "$f"; sed -n '4,$p' "$f"; } > tmp && mv tmp "$f"
done
```

**How it works:**

- Same idea as EX-SH-02 with a different order: `3p; 2p; 1p; 4,$p` into a temp file, then `mv` it back.

**Adapt it:**

the same template solves *any* line rearrangement. Only the order of the `sed -n 'Np'` commands changes.

### EX-SH-09 · Swap the first and last line of every .alter file (class exercise 2)

**From:** Class exercise 2 — your file `ex2_swap_lines.sh` · plus the shorter drill version (Linux drills 4.3, week 1.3)  
**Search:** swap first and last line · sed 1d;\$d · head -n 1 · tail -n 1 · mktemp · folder argument \${1:-.} · fewer than 2 lines

`swap_alter.sh` — *(class ex2)* In **every** `.alter` file, swap the **first and last** line, editing the file in place. Skip files with fewer than 2 lines (`short.alter`) and print a message for them.

ex2_swap_lines.sh (your version: optional folder argument, handles a missing final newline)

```bash
#!/bin/bash
# Exercise 2: in every file with the .alter extension in a folder (default: the current
# one), swap the first and the last line.
# Usage: ./ex2_swap_lines.sh [folder]

cd "${1:-.}" || exit 1

for file in *.alter
do
    if [ ! -f "$file" ]; then                 # no .alter files: the pattern stays literal
        echo "no .alter files in $(pwd)"
        break
    fi
    lines=$(sed -n '$=' "$file")              # number of lines (also counts a last line without newline)
    if [ -z "$lines" ] || [ "$lines" -lt 2 ]; then
        echo "$file: fewer than 2 lines, nothing to swap"
        continue
    fi
    first=$(head -n 1 "$file")
    last=$(tail -n 1 "$file")
    tmp=$(mktemp)
    {
        printf '%s\n' "$last"                 # new first line
        sed '1d;$d' "$file"                   # the lines in between, unchanged
        printf '%s\n' "$first"                # new last line
    } > "$tmp"
    cat "$tmp" > "$file"                      # write back into the same file (keeps its permissions)
    rm "$tmp"
    echo "$file: swapped line 1 and line $lines"
done
exit 0
```

swap_alter.sh (short exam version)

```bash
# 4.3 swap_alter.sh
#!/bin/bash
# swap first and last line of every .alter file
for f in *.alter; do
    n=$(wc -l < "$f")
    if [ "$n" -lt 2 ]; then
        echo "$f: fewer than 2 lines, skipped"
        continue
    fi
    {
        sed -n '$p' "$f"           # last line first
        sed -n "2,$((n-1))p" "$f"  # middle lines (prints nothing if n=2)
        sed -n '1p' "$f"           # first line last
    } > tmp && mv tmp "$f"
    echo "$f: swapped"
done
```

**How it works:**

- Each file: `head -n 1` = first line, `tail -n 1` = last line, `sed '1d;$d'` = everything in between.
- Write last + middle + first into a temp file, then copy it back. Files with fewer than 2 lines are skipped with a message.
- Your version also accepts a folder argument and handles a last line without a newline (`sed -n '$='` counts lines reliably).

**Adapt it:**

`wc -l` counts newline characters, so a last line without a final newline isn't counted. The files here are fine. Your class version uses `sed -n '$='`, which handles that case too.

### EX-SH-10 · Lines with the word “for” of every .py file → name.forY.txt (≥ 10 times) or name.forN.txt (class exercise 3)

**From:** Class exercise 3 — your file `ex3_for_lines.sh` · plus the shorter drill version (Linux drills 4.5)  
**Search:** forY forN · count word occurrences · grep -ow \| wc -l · threshold -ge 10 · output file name from variable

`for_split.sh` — *(class ex3)* For each `.py` file, write the lines containing the **word** `for` into a new file: `<name>.py.forY.txt` if `for` appears **10 or more** times in the file, otherwise `<name>.py.forN.txt`.

ex3_for_lines.sh (your version)

```bash
#!/bin/bash
# Exercise 3: for each .py file in the current folder, write the lines that contain the
# keyword "for" to a new file:
#     <name>.py.forY.txt  if "for" appears 10 or more times in the file
#     <name>.py.forN.txt  if it appears fewer than 10 times

for file in *.py
do
    if [ ! -f "$file" ]; then                 # no .py files: the pattern stays literal
        echo "no .py files in $(pwd)"
        break
    fi
    # -w: only the whole word "for" (not "format", "before", "for_each"); -o: one line per
    # occurrence, so wc -l counts occurrences, not lines (a line can contain "for" twice)
    count=$(grep -ow 'for' "$file" | wc -l)
    count=$((count))                          # drop the spaces that wc puts around the number

    if [ "$count" -ge 10 ]; then
        out="$file.forY.txt"
    else
        out="$file.forN.txt"
    fi
    rm -f "$file.forY.txt" "$file.forN.txt"   # remove the result of an earlier run, if any
    grep -w 'for' "$file" > "$out"
    echo "$file: 'for' appears $count times -> $out ($(wc -l < "$out" | tr -d ' ') lines)"
done
exit 0
```

for_split.sh (short exam version)

```bash
# 4.5 for_split.sh
#!/bin/bash
# lines containing the word "for" of every .py file -> name.forY.txt (>=10 times) or name.forN.txt
for f in *.py; do
    count=$(grep -ow 'for' "$f" | wc -l)
    if [ "$count" -ge 10 ]; then
        out="$f.forY.txt"
    else
        out="$f.forN.txt"
    fi
    grep -w 'for' "$f" > "$out"
    echo "$f: $count -> $out"
done
```

**How it works:**

- `grep -ow 'for' file | wc -l` counts **occurrences** of the whole word (`-w` skips `format`, `before`; `-o` puts each on its own line).
- `-ge 10` chooses the output name; `grep -w for file > "$out"` writes the matching lines into it.

**Adapt it:**

delete old outputs (`rm -f *.for?.txt`) before re-running, or a file can end up with both a forY and a forN version.

### EX-SH-11 · Sign of a number, then compare a second number with it (class exercise 1)

**From:** Class exercise 1 — your file `ex1_numbers.sh` · plus the shorter drill version (Linux drills 4.1)  
**Search:** read number · negative zero positive · -lt -eq -gt · compare two numbers · validate integer regex

`numbers.sh` — *(class ex1)* Ask for a number and say whether it is **negative, zero or positive**. Then ask for a second number and say whether it is **equal to, less than or greater than** the first one.

ex1_numbers.sh (your version: rejects non-integers)

```bash
#!/bin/bash
# Exercise 1: say whether a number is negative, zero or positive; then read a second
# number and say whether it is equal to, less than or greater than the first one.
# The tests -lt -eq -gt compare integers, so only integers are accepted; anything else
# is rejected and asked for again.

read_integer() {                  # usage: n=$(read_integer "prompt")
    local n
    while true; do
        read -p "$1" n || exit 1                  # input ended (Ctrl-D): give up
        if [[ $n =~ ^-?[0-9]+$ ]]; then           # optional minus sign, then digits only
            echo "$n"
            return
        fi
        echo "'$n' is not an integer number, try again." >&2
    done
}

first=$(read_integer "Enter a number: ") || exit 1
if [ "$first" -lt 0 ]; then
    echo "$first is negative"
elif [ "$first" -eq 0 ]; then
    echo "$first is zero"
else
    echo "$first is positive"
fi

second=$(read_integer "Enter a second number: ") || exit 1
if [ "$second" -eq "$first" ]; then
    echo "$second is equal to $first"
elif [ "$second" -lt "$first" ]; then
    echo "$second is less than $first"
else
    echo "$second is greater than $first"
fi
exit 0
```

numbers.sh (short exam version)

```bash
# 4.1 numbers.sh
#!/bin/bash
# numbers.sh - sign of a number, then compare a second number with it
read -p "Enter a number: " a
if [ "$a" -lt 0 ]; then
    echo "$a is negative"
elif [ "$a" -eq 0 ]; then
    echo "$a is zero"
else
    echo "$a is positive"
fi
read -p "Enter a second number: " b
if [ "$b" -eq "$a" ]; then
    echo "$b is equal to $a"
elif [ "$b" -lt "$a" ]; then
    echo "$b is less than $a"
else
    echo "$b is greater than $a"
fi
```

**How it works:**

- `read -p` reads the number; `-lt 0` / `-eq 0` / else → negative / zero / positive.
- The second number is compared with `-eq`, `-lt` or else. These tests only work on integers.
- Your version adds a loop that rejects anything that isn't an integer, using the regex `^-?[0-9]+$`.

**Adapt it:**

Test with `printf -- "-5\n3\n" | bash numbers.sh` (the `--` stops printf reading `-5` as an option). Decimals: `-lt` only compares integers; use `bc` or `awk` if decimals are required.

### EX-SH-12 · Job checker: OK / FAILED per .log file, then the totals

**From:** Week notebook 1.5 · Linux drills 4.7  
**Search:** grep -q in if · counters · ok=\$((ok+1)) · Normal termination

Write `check_jobs.sh`. For each `job*.log`, it prints `OK` or `FAILED` depending on whether the file contains `Normal termination`, and at the end it prints how many were OK and how many FAILED. Use `if grep -q ...`.

check_jobs.sh

```bash
#!/bin/bash
ok=0; bad=0
for f in job*.log; do
    if grep -q "Normal termination" "$f"; then
        echo "$f OK";     ok=$((ok+1))
    else
        echo "$f FAILED"; bad=$((bad+1))
    fi
done
echo "OK: $ok   FAILED: $bad"
```

**How it works:**

- `grep -q` prints nothing; it only sets success/failure, which `if` uses directly.
- Two counters are increased with `$((ok+1))` and printed after the loop.

### EX-SH-13 · Folder as an argument (\$1, default .): count .f90, .py and .txt files; error + exit 1 if not a directory

**From:** Linux drills 4.8  
**Search:** script argument \$1 · default \${1:-.} · exit 1 · loop over extensions · 2\>/dev/null

`count_ext.sh` — Takes a folder as an **argument** (`$1`), defaulting to the current folder when none is given. If it isn't a directory, print an error and `exit 1`. Otherwise print how many `.f90`, `.py` and `.txt` files it contains (hint: loop over the three extensions).

count_ext.sh

```bash
# 4.8 count_ext.sh
#!/bin/bash
# usage: bash count_ext.sh [folder]   (default: current folder)
dir=${1:-.}
if [ ! -d "$dir" ]; then
    echo "$dir is not a directory"
    exit 1
fi
for ext in f90 py txt; do
    n=$(ls "$dir"/*."$ext" 2>/dev/null | wc -l)
    echo "$ext: $n"
done
```

**How it works:**

- `${1:-.}` = the first argument, or `.` if none was given.
- `[ ! -d ]` → error message and `exit 1` (a non-zero exit means failure).
- The loop goes over the three extensions and counts `ls "$dir"/*.$ext | wc -l` for each.

### EX-SH-14 · Ask for a word, count it in every .f90 and .py file, report the file with the most (built step by step)

**From:** Day 0 notebook, A12 (worked example)  
**Search:** word count per file · best so far · maximum · two extensions in one loop

Write a script that asks for a word. For every `.f90` and `.py` file, print the file name and how many times the word appears. At the end, print which file had the most.

wordcount.sh

```bash
#!/bin/bash
# wordcount.sh - occurrences of a word in every .f90 and .py file
read -p "Word: " word
best_file=""
best_n=-1
for f in *.f90 *.py; do
    n=$(grep -ow "$word" "$f" | wc -l)
    echo "$f $n"
    if [ "$n" -gt "$best_n" ]; then
        best_n=$n
        best_file=$f
    fi
done
echo "Most: $best_file ($best_n times)"
```

**How it works:**

- The loop goes over `*.f90 *.py` together; for each file it counts the word with `grep -ow | wc -l`.
- “Best so far”: `best_n` starts at −1; whenever a file's count is greater, it becomes the new best and its name is saved.

**Adapt it:**

“Best so far”: start with `best_n=-1`, update inside the loop with `-gt`. Fewest → start high and use `-lt`.

### EX-SH-15 · One-liners: lines per file, A but not B, lines vs occurrences of a word, the Nth line

**From:** Week notebook 1.1  
**Search:** wc -l \*.f90 · grep -c vs grep -ow \| wc -l · sed -n 3p · awk NR==3

a\) Print the number of lines of every `.f90` file.  
b) ⭐ *(2021, 3.1)* Print the lines of `program.f90` that contain `x_pos` but do **not** contain `y_pos`.  
c) For every `.py` file, print its name and the number of **lines** containing `import`.  
d) Same as c), but count **occurrences of the word** `import`. Compare with c) for `tools.py` and `analysis.py`. Why do they differ?  
e) Print the 3rd line of `notes1.txt`, in two different ways.

commands

```bash
echo "--- a) lines per .f90 file"
for f in *.f90; do echo "$f $(wc -l < "$f")"; done        # or simply: wc -l *.f90
echo "--- b) x_pos but not y_pos"
grep x_pos program.f90 | grep -v y_pos
echo "--- c) LINES containing import"
for f in *.py; do echo "$f $(grep -c import "$f")"; done  # or: grep -c import *.py
echo "--- d) OCCURRENCES of the word import"
for f in *.py; do echo "$f $(grep -ow import "$f" | wc -l)"; done
echo "--- e) 3rd line"
sed -n '3p' notes1.txt
awk 'NR==3' notes1.txt
```

**How it works:**

- **a)** `wc -l` on several files prints one line each plus a total.
- **b)** `grep A | grep -v B`.
- **c)** `grep -c` counts **lines** containing the word; **d)** `grep -ow | wc -l` counts **occurrences**, so a line with the word twice counts twice.
- **e)** `sed -n 3p file` or `head -n 3 file | tail -n 1`.

**Adapt it:**

Why c) and d) differ: in `tools.py` one line holds two imports.  
`empty.py` scores 1 in c) because "imports" contains "import"; the `-w` flag in d) excludes it. `-c` counts it once, `-o` prints each match so `wc -l` counts 2.  
In `analysis.py` the comment contains "important". Plain `grep` matches it, but `-w` (whole word) does not.  
For an exam question like "number of times the word import appears", use `grep -ow import f | wc -l`.

### EX-SH-16 · Class Linux sheet, part 1: listing and counting

**From:** Linux drills Section 1 (class exercises 1, 2, 10, 11)  
**Search:** ls -lt newest first · ls -ltr oldest · hidden files ls -a · extensions grep -E · ls -S largest · count files wc -l

**1.1** Long listing of `/etc`, sorted by modification time with the **newest first**, redirected to `etc.out`. Then show the first 3 lines of `etc.out`.  
**1.2** Same listing of `/etc`, but with the **oldest first**. Show only the first 3 lines.  
**1.3** List the files in `home_sim` (**including hidden files**) whose extension is `txt`, `doc` or `odt`. Careful: `notes.txt.bak` must NOT appear.  
**1.4** Show the **five largest** files in the current directory.  
**1.5** How many files ending in `.txt` are there in `home_sim`? (Not counting hidden ones.)  
**1.6** How many entries are there in `/etc`?

commands

```bash
# 1.1
ls -lt /etc > etc.out
head -n 3 etc.out
# 1.2
ls -ltr /etc | head -n 3
# 1.3   (-a includes hidden files; $ anchors the extension at the end of the name)
ls -a home_sim | grep -E '\.(txt|doc|odt)$'
# 1.4   (-S sorts by size; add -l to see the sizes; grep '^-' keeps only regular files)
ls -S | head -n 5
ls -lS | grep '^-' | head -n 5
# 1.5
ls home_sim/*.txt | wc -l
# 1.6
ls /etc | wc -l
```

**How it works:**

- `ls -l` long listing; `-t` sort by time (newest first), `-r` reverses; `-a` includes hidden files; `-S` sorts by size.
- `grep -E '\.(txt|doc)$'` keeps names ending in those extensions (`$` = end of line).
- Counting entries: pipe the listing into `wc -l`.

### EX-SH-17 · Class Linux sheet, part 2: creating files, permissions, diff

**From:** Linux drills Section 2 (class exercises 3, 6, 12, 13, 14)  
**Search:** chmod 660 · chmod u+x · heredoc cat \> file \<\< END · apostrophe · sed -i · diff

**2.1** Change the permissions of `etc.out` so it is readable and writable **only** by you and your group. Check with `ls -l`. (Run 1.1 first.)  
**2.2** Make `run.sh` executable for you, and run it with `./`.  
**2.3** Create `poem.txt` with exactly these 4 lines, then `cat` it:

    I went down to the river,
    I set down on the bank.
    I tried to think but couldn't,
    So I jumped in and sank.

(Tip: the apostrophe in *couldn't* breaks single-quoted `echo '...'`. Use double quotes, or a heredoc with `'END'`.)  
**2.4** Create `poem2.txt` as a copy of `poem.txt` with **one** line changed (e.g. use `sed` to replace `think` with `swim`), then show the differences with `diff`.

commands

```bash
# 2.1   6 = rw for user, 6 = rw for group, 0 = nothing for others
chmod 660 etc.out
ls -l etc.out          # -rw-rw----
# 2.2
chmod u+x run.sh
./run.sh
# 2.3
cat > poem.txt << 'END'
I went down to the river,
I set down on the bank.
I tried to think but couldn't,
So I jumped in and sank.
END
cat poem.txt
#   (or: echo "I went down to the river," > poem.txt ; echo "..." >> poem.txt ; ...)
# 2.4
cp poem.txt poem2.txt
sed -i 's/think/swim/' poem2.txt
diff poem.txt poem2.txt      # 3c3 = line 3 changed
```

**How it works:**

- `chmod 660` = read+write for user and group, nothing for others (r=4, w=2, x=1, summed per digit). `chmod u+x` adds execute for you.
- `cat > file << 'END'` … `END` writes the lines in between into the file (quoting `'END'` keeps `$` and `'` literal).
- `diff a b` prints only the lines that differ.

### EX-SH-18 · Class Linux sheet, part 3: filtering and cutting

**From:** Linux drills Section 3 (class exercises 4, 5, 7, 8, 9 + past exams)  
**Search:** lines 3 to 7 · grep ^I · cut -c · grep -vw · last line · sed s///g · exactly 20 lines · grep -l · symbolic link ln -s · history \| grep · scp

**3.1** Display lines 3 to 7 of `llistat.llarg`, in **two different ways**.  
**3.2** Create a file named `me` containing the lines of `poem.txt` that **start** with `I`. Show it.  
**3.3** Using a pipe, show **characters 10 to 15** of the lines of `poem.txt` that do **not** contain the word `to`.  
**3.4** ⭐ *(exam 2021)* One line: the lines of `program.f90` that contain `x_pos` but not `y_pos`.  
**3.5** For `loops.py`: (a) how many **lines** contain `for`? (b) how many times does the **word** `for` appear? Why are the two numbers different?  
**3.6** Print the last line of `llistat.llarg` in two ways, then print just its number of lines (only the number).  
**3.7** Print `llistat.llarg` with every `line` replaced by `LINE` (without changing the file). Then make the same change **in place** on a copy, `ll.copy`.  
**3.8** List the `.f90` files in `home_sim` that have **exactly 20 lines**. (A loop with a test, or `wc -l` + `awk`.)  
**3.9** Which `.log` files contain `Normal termination`? Then, in a second line: how many are there?  
**3.10** Create a symbolic link `poem_link` to `poem.txt`. `cat` the link, then delete `poem.txt` and try to `cat` the link again. What happens, and why? (Re-run 2.3 afterwards.)  
**3.11** *(write only)* The command that finds every command in your history containing `ssh`. *(History is empty in Colab, so just type it.)*  
**3.12** *(write only)* The `scp` command that copies `poem.txt` to the directory `Copied` on the cluster, saved as `poem_remote.txt`. Use `user@cluster` as the address.

commands

```bash
# 3.1
sed -n '3,7p' llistat.llarg
head -n 7 llistat.llarg | tail -n 5
# 3.2
grep '^I' poem.txt > me
cat me
# 3.3   (-w so that "to" only matches the whole word)
grep -vw 'to' poem.txt | cut -c 10-15
# 3.4
grep x_pos program.f90 | grep -v y_pos
# 3.5   (a) 10 lines   (b) 13 occurrences: some lines hold several "for"s; -w also skips format/before
grep -c for loops.py
grep -ow for loops.py | wc -l
# 3.6
sed -n '$p' llistat.llarg
tail -n 1 llistat.llarg
wc -l < llistat.llarg
# 3.7
sed 's/line/LINE/g' llistat.llarg
cp llistat.llarg ll.copy
sed -i 's/line/LINE/g' ll.copy
# 3.8
for f in home_sim/*.f90; do
    if [ "$(wc -l < "$f")" -eq 20 ]; then echo "$f"; fi
done
wc -l home_sim/*.f90 | awk '$1 == 20 {print $2}'     # alternative
# 3.9
grep -l "Normal termination" *.log
grep -l "Normal termination" *.log | wc -l
# 3.10  the link stores only the NAME poem.txt; once the file is gone, the link is "dangling":
#       cat poem_link -> "No such file or directory"
ln -s poem.txt poem_link
cat poem_link
rm poem.txt
cat poem_link
# 3.11
history | grep ssh
# 3.12
scp poem.txt user@cluster:Copied/poem_remote.txt
```

**How it works:**

- Lines 3 to 7: `sed -n '3,7p'` or `head -n 7 | tail -n 5`. Lines starting with I: `grep '^I'`. Characters 10–15: `cut -c 10-15`.
- `sed 's/old/new/g'` replaces everywhere and prints the result without changing the file (`-i` would change it).
- Exactly 20 lines: `wc -l` on every file, then `awk '$1 == 20 {print $2}'`. Files containing a text: `grep -l`.
- `ln -s target link` makes a symbolic link; deleting the target leaves it dangling.

### EX-SH-19 · Move the first line of every .rot file to the end (rotate the lines), in place

**From:** Mock exam 2 (11 Nov), Ex 1.1 (0.75 points)  
**Search:** every file with extension · for f in \*.rot · first line to the end · head -n 1 · tail -n +2 · tmp file · mv · \> vs \>\> · in place · rotate lines

Make a script that, for all the files with a ".rot" extension in the current folder, moves the first line of the file to the end of the file. The other lines keep their order. Files with other extensions must not change.

**Cues (the prompt says → you write):**

| The prompt says                                           | You write                                            |
|-----------------------------------------------------------|------------------------------------------------------|
| “for all the files with a .rot extension”                 | `for f in *.rot; do … done`                          |
| “the first line”                                          | `head -n 1 "$f"`, saved in a variable                |
| “the other lines keep their order”                        | `tail -n +2 "$f"` = from line 2 to the end           |
| “moves … to the end of the file” (change the file itself) | build a tmp file (`>` then `>>`), then `mv tmp "$f"` |
| “other extensions must not change”                        | the glob `*.rot` only matches .rot files             |

rotate.sh

```bash
#!/bin/bash
for f in *.rot; do
    first=$(head -n 1 "$f")        # save line 1 in a variable
    tail -n +2 "$f" >  tmp         # lines 2..end go into tmp (> creates/empties tmp)
    echo "$first"   >> tmp         # >> APPENDS line 1 at the end
    mv tmp "$f"                    # replace the original
done
```

**How it works:**

- `for f in *.rot` loops over the file names; `"$f"` is quoted so names with spaces still work.
- `$(...)` runs a command and stores its output: `first` holds line 1.
- `tail -n +2` means "starting **from** line 2" (the plus sign). `tail -n 2` would be the last 2 lines.
- `>` creates (or empties) `tmp` and writes lines 2..end; `>>` adds the saved first line after them.
- Why the tmp file: `cmd "$f" > "$f"` empties the file **before** the command reads it, so the content is lost. Writing elsewhere and then `mv` replaces the original safely.

**Check:** with the mock files:

``` plaintext
alpha.rot: one two three four  ->  two three four one
beta.rot:  first line / second line  ->  second line / first line
keep.txt   unchanged
```

**Adapt it:**

Last line to the front → `last=$(tail -n 1 "$f")`, `echo "$last" > tmp`, `head -n -1 "$f" >> tmp` (all but the last; on macOS use `sed '$d' "$f"`). Swap two lines → EX-SH-02 / EX-SH-09. Folder as an argument → `for f in "${1:-.}"/*.rot`. Reverse the whole file → `tac "$f" > tmp`.

### EX-SH-20 · Three-way script: file → lines + compare a number; directory → lines with the word def of every .py into name.py.defY.txt (≥ 3 times) or .defN.txt; neither → message

**From:** Mock exam 2 (11 Nov), Ex 1.2 (1 point)  
**Search:** read -p · if -f · elif -d · else · wc -l \< · -lt -eq -gt · compare with number of lines · for f in dir/\*.py · grep -o -w def \| wc -l · occurrences · -ge 3 · defY defN · new file inside the directory

Make a script asking for a name.

- If it is an existing file, the script displays the number of lines of the file. Then it asks for a number and says whether that number is less than, equal to or greater than the number of lines.
- If it is an existing directory, then for each file with ".py" extension inside that directory the script generates a new file containing only the lines where the word "def" appears. The new file is named `<name>.py.defY.txt` if the word "def" appears 3 or more times in the .py file, and `<name>.py.defN.txt` otherwise. The new files are created inside that directory.
- If it is neither, the script prints "`<name>` is neither a file nor a directory".

**Cues (the prompt says → you write):**

| The prompt says                                      | You write                                                   |
|------------------------------------------------------|-------------------------------------------------------------|
| “asking for a name”                                  | `read -p "Name: " name`                                     |
| “if it is an existing file / directory / neither”    | `if [ -f "$name" ]` · `elif [ -d "$name" ]` · `else`        |
| “the number of lines”                                | `wc -l < "$name"` (the `<` gives only the number)           |
| “less than, equal to or greater than”                | `-lt` · `-eq` · else                                        |
| “each file with .py extension inside that directory” | `for f in "$name"/*.py`                                     |
| “only the lines where the word def appears”          | `grep -w "def" "$f" > "$out"`                               |
| “appears 3 or more times” (occurrences, not lines)   | `grep -o -w "def" "$f" \| wc -l`, then `[ "$count" -ge 3 ]` |
| “named name.py.defY.txt … inside that directory”     | `out="$f.defY.txt"` ($f already holds dir/name.py)          |

inspect.sh

```bash
#!/bin/bash
read -p "Name: " name

if [ -f "$name" ]; then
    nl=$(wc -l < "$name")                  # < gives just the number, no filename
    echo "$name has $nl lines"
    read -p "Number: " n
    if [ "$n" -lt "$nl" ]; then
        echo "$n is less than $nl"
    elif [ "$n" -eq "$nl" ]; then
        echo "$n is equal to $nl"
    else
        echo "$n is greater than $nl"
    fi
elif [ -d "$name" ]; then
    for f in "$name"/*.py; do              # the .py files INSIDE that directory
        count=$(grep -o -w "def" "$f" | wc -l)
        if [ "$count" -ge 3 ]; then
            out="$f.defY.txt"              # $f is already "scripts/tools.py" -> file lands in the dir
        else
            out="$f.defN.txt"
        fi
        grep -w "def" "$f" > "$out"        # only the lines containing the word def
    done
else
    echo "$name is neither a file nor a directory"
fi
```

**How it works:**

- `read -p "Name: " name` prints the prompt and stores the answer in `name`.
- `[ -f ]` is true for an existing regular file, `[ -d ]` for a directory; the order matters only in that `else` catches everything else. The spaces inside `[ ]` are required: `[ -f "$name" ]`, never `[-f "$name"]`.
- `wc -l < "$name"` reads the file from standard input, so `wc` prints only the number (`wc -l "$name"` would also print the name, and the comparison would fail).
- `-lt`, `-eq`, `-gt`, `-ge` compare **integers**; `<` inside `[ ]` would be a redirection.
- `"$name"/*.py` expands to `scripts/tools.py scripts/run.py`; so `$f.defY.txt` = `scripts/tools.py.defY.txt`, already inside the directory as asked.
- Counting occurrences: `grep -o` prints each match on its own line, so `wc -l` counts every `def`, even two on one line. `grep -c` would count *lines*. `-w` = whole word, so `undefined` is not counted.
- `grep -w "def" "$f" > "$out"` writes the matching lines into the new file (one `>` per file, so each file is written fresh).

**Check:** with the mock folder (feed the answers with echo/printf):

``` plaintext
$ echo scripts | bash inspect.sh
scripts/tools.py.defY.txt   4 lines  (area, volume, describe, undefined_case)
scripts/run.py.defN.txt     1 line
$ printf "keep.txt\n5\n" | bash inspect.sh
keep.txt has 2 lines
5 is greater than 2
```

**Adapt it:**

Another word or threshold → change `"def"` and `3`. Copy files into the directory instead (EX-SH-01) → `cp *.f90 "$name"/`. Count words instead of lines → `wc -w <`. Neither → a report file (EX-SH-01): put `> report.txt` after `done`. Name as an argument instead of asking → `name=$1`.

Test all three branches: `echo keep.txt | bash inspect.sh`, `echo scripts | bash inspect.sh`, `echo nothing | bash inspect.sh`.

### EX-SH-21 · One line: count the lines that contain the word do but not the word end

**From:** Mock exam 2 (11 Nov), Ex 1.3 (0.25 points)  
**Search:** one line · contains A but not B · grep -w · grep -v · count lines · wc -l · pipe

Write a command (in one line) that counts the lines of `program.f90` that contain the word `do` and at the same time do not contain the word `end`.

**Cues (the prompt says → you write):**

| The prompt says               | You write                |
|-------------------------------|--------------------------|
| “contain the word do”         | `grep -w do program.f90` |
| “do not contain the word end” | `\| grep -v end`         |
| “counts the lines”            | `\| wc -l`               |

bash

```bash
grep -w do program.f90 | grep -v end | wc -l
```

**How it works:**

- `grep -w do` keeps the lines with the whole word `do` (not `done`, `double`).
- `grep -v end` keeps the lines **without** `end`: it removes `end do`, and the comment line that mentions both.
- `wc -l` counts what is left. Each `|` feeds the previous output into the next command.

**Check:** with the mock program.f90:

``` plaintext
3
```

**Adapt it:**

Contains both A and B → `grep -w A f | grep -w B`. Count occurrences instead of lines → `grep -o -w A f | wc -l`. Case-insensitive → add `-i`. Print the line numbers → `grep -n`.

## 7.4 Gnuplot \[EX-GP\]

| ID                    | Problem                                                                                                    | From                                                   |
|-----------------------|------------------------------------------------------------------------------------------------------------|--------------------------------------------------------|
| [EX-GP-01](#ex-gp-01) | 3D surface with base contours every 0.5, arrow + label to the relative maximum, 2D contour map, values.dat | 2021-22 exam, Ex 4 (1.5 points) \[PAST\]               |
| [EX-GP-02](#ex-gp-02) | Make this picture: gray surface cos(x\*\*3−y)−sin(x+y), view 50,60, key titled “Waves”, base contours      | 2022-23 exam, Ex 2 (1.5 points) \[PAST\]               |
| [EX-GP-03](#ex-gp-03) | Surface with 8 contour levels, arrows + labels to the maximum and the minimum, 2D map, grid.dat            | Mock exam (Day 7), Ex 2                                |
| [EX-GP-04](#ex-gp-04) | Gray shaded surface, contours from −1 to 1 every 0.2, exact title, units on axes, key title                | Week notebook 3.3 (exam-style variant of 2022-23 Ex 2) |
| [EX-GP-05](#ex-gp-05) | One script, two figures: surface with contours, then a 2D level-curve map                                  | Day 0 notebook, Part C                                 |
| [EX-GP-06](#ex-gp-06) | 2D plot of two functions with labels, title, legend and a dashed line                                      | Week notebook 3.1                                      |
| [EX-GP-07](#ex-gp-07) | Gray surface, contours every 0.1, key box titled Levels, arrow to the maximum, 2D map, 40×40 grid.dat      | Mock exam 2 (11 Nov), Ex 2.1 (1 point)                 |
| [EX-GP-08](#ex-gp-08) | Michaelis–Menten fit of a data file, fitted values in the title with sprintf, key below in a box           | Mock exam 2 (11 Nov), Ex 2.2 (0.5 points)              |

### EX-GP-01 · 3D surface with base contours every 0.5, arrow + label to the relative maximum, 2D contour map, values.dat

**From:** 2021-22 exam, Ex 4 (1.5 points) \[PAST\]  
**Search:** splot · contour base · cntrparam incremental · set arrow · set label · set table · isosamples 50,30 · view map · relative maximum

Perform the representation of  
z(x,y) = sin(x) \* cos(y) + 0.2 \* x \* y  
at the range of x of \[−5, 5\] and for y of \[−3, 3\]. Make a script to represent the function with level curves separated by values of 0.5. Indicates the labels of the axes (x, y, z, respectively). Shows an arrow pointing (approximately) towards the relative maximum of the figure, includes a label indicating ‘Max’. Generates two figures in a graphic format (jpg, eps or png), a first figure where the graph is represented in 3D surface type (x, y, z format) with the contours at the base, and another in 2D flat format (x, y format) where only the level curves are displayed. Generate a file called ‘values.dat’ with the x, y and z coordinates of a 50x30 grid.

Finding the maximum (Python, optional)

```python
import numpy as np
from scipy.ndimage import maximum_filter
x = np.linspace(-5, 5, 501); y = np.linspace(-3, 3, 301)
X, Y = np.meshgrid(x, y); Z = np.sin(X)*np.cos(Y) + 0.2*X*Y
is_max = (Z == maximum_filter(Z, size=21))            # point is the largest in its neighbourhood
is_max[[0, -1], :] = False; is_max[:, [0, -1]] = False  # drop points on the border
for j, i in zip(*np.where(is_max)):
    print(f"local max at x={x[i]:.2f}, y={y[j]:.2f}, z={Z[j,i]:.3f}")
```

ex4.gp

```gnuplot
set terminal pngcairo size 900,700
f(x,y) = sin(x)*cos(y) + 0.2*x*y
set xrange [-5:5]
set yrange [-3:3]
set xlabel 'x'
set ylabel 'y'
set zlabel 'z'

# 1) values.dat on a 50x30 grid -- BEFORE 'set contour'
set samples 50,30
set isosamples 50,30
set table 'values.dat'
splot f(x,y)
unset table

# 2) 3D surface + contours on the base, every 0.5
set contour base
set cntrparam levels incremental -4,0.5,4
set hidden3d
set key outside
set arrow 1 from 3.5,2.5,5 to 1.64,0.34,1.05 lw 2
set label 1 'Max' at 3.6,2.6,5.3
set output 'surface3d.png'
splot f(x,y) title 'z(x,y)'

# 3) 2D: only the level curves
set output 'contours2d.png'
unset surface
set view map
unset hidden3d
set arrow 1 from 3.5,2.5 to 1.64,0.34 lw 2
set label 1 'Max' at 3.6,2.6
splot f(x,y) notitle
```

**How it works:**

- **values.dat:** `set table` writes the plotted points to a file instead of drawing them; `isosamples 50,30` sets the grid. This must come before `set contour`, or the contour lines get written into the file too.
- **3D figure:** `set contour base` + `cntrparam levels incremental start,0.5,end` gives level curves every 0.5; `set arrow from ... to x,y,z` and `set label 'Max' at ...` mark the maximum (found with the optional Python cell).
- **2D figure:** `set view map` + `unset surface` keeps only the level curves; arrows need only x,y here.

**Adapt it:**

### 3.2 (2021, Ex 4)

Finding the maximum: the **global** maximum on this domain is at the corner (5, 3), z = 3.95. That's on the boundary, so it isn't a *relative* (local) maximum.  
The clear interior **relative maximum** is near (1.64, 0.34), z = 1.05. The cell below finds the local maxima, and the arrow points to that one. Reading it off the 2D contour map is also fine in the exam.

**This is the master gnuplot template.** Order matters: `set table` **before** `set contour`. In the 2D map the arrow needs 2 coordinates, not 3.

### EX-GP-02 · Make this picture: gray surface cos(x\*\*3−y)−sin(x+y), view 50,60, key titled “Waves”, base contours

**From:** 2022-23 exam, Ex 2 (1.5 points) \[PAST\] · solution written for these notes and compared with the exam picture  
**Search:** reproduce a picture · gray surface · pm3d · palette gray · set view 50,60 · key title Waves · x/nm y/nm A/s · cntrparam incremental · xtics ytics ztics

Make a *gnuplot* script that generates the following picture. Surface is depicted in gray. Line width and color of contour lines have default values.

*What the picture shows:* title `cos(x**3-y)-sin(x+y). View: 50,60`; x from 0 to 2 (ticks every 0.4), label `x/nm`; y from 0 to 5 (ticks every 0.5), label `y/nm`; z from −2 to 2 (ticks every 0.4), label `A/s`; a boxed key at the top left titled `Waves`, with `surface` and the contour levels 0, −0.2, …, −1.8; contour lines on the base.

ex2.gp

```gnuplot
set terminal pngcairo size 900,700
set output 'waves.png'
f(x,y) = cos(x**3 - y) - sin(x + y)
set xrange [0:2]
set yrange [0:5]
set zrange [-2:2]
set xtics 0.4
set ytics 0.5
set ztics 0.4
set xlabel 'x/nm'
set ylabel 'y/nm'
set zlabel 'A/s'
set title 'cos(x**3-y)-sin(x+y). View: 50,60'
set view 50,60
set isosamples 40,40
set samples 40,40
set key outside left top box title 'Waves'
# contours at the base, default width and colour, levels as in the picture
set contour base
set cntrparam levels incremental -1.8,0.2,0
# gray surface
set pm3d implicit at s depthorder
set palette gray
unset colorbox
splot f(x,y) with lines lc rgb 'gray40' title 'surface'
```

**How it works:**

- Each visible feature is one setting: `set title`, `set view 50,60`, the ranges and tick steps (`set xtics 0.4` …), the axis labels, and `set key outside left top box title 'Waves'`.
- Contour levels from the key: `levels incremental -1.8,0.2,0`. Default width/colour = don't set any `lw`/`lc` for the contours.
- Gray surface: `pm3d` shading with a gray palette and no colour bar; `depthorder` draws the near parts last.

**Adapt it:**

Read the picture like a checklist: title, view, ranges, tick steps, axis labels, key position/title/entry names, which contour levels appear in the key, gray or colour. `depthorder` makes the shaded surface draw cleanly. If the picture also shows contour lines on the surface itself → `set contour both`. A gray mesh instead of a shaded surface → `set hidden3d` + `with lines lc rgb 'gray'`.

### EX-GP-03 · Surface with 8 contour levels, arrows + labels to the maximum and the minimum, 2D map, grid.dat

**From:** Mock exam (Day 7), Ex 2 · solution written and tested for these notes  
**Search:** x\*exp(-x\*\*2-y\*\*2) · two extrema · Max Min arrows · cntrparam levels 8 · set table grid.dat · hidden3d

Represent f(x,y) = x·exp(−x²−y²) for x, y ∈ \[−2, 2\] on a 40×40 grid: a hidden-line 3D surface with 8 contour levels at the base, axis labels x, y, z, and **arrows with labels** `Max` and `Min` pointing at the two extrema. Save `fig3d.png`, plus a second figure `fig2d.png` with only the level curves in 2D. Write `grid.dat` with x, y, z on the 40×40 grid.

ex2.gp

```gnuplot
set terminal pngcairo size 900,700
f(x,y) = x*exp(-x**2 - y**2)
set xrange [-2:2]
set yrange [-2:2]
set xlabel 'x'
set ylabel 'y'
set zlabel 'z'
set samples 40,40
set isosamples 40,40

# 1) grid.dat: x, y, z on the 40x40 grid -- BEFORE 'set contour'
set table 'grid.dat'
splot f(x,y)
unset table

# 2) 3D: hidden-line surface, 8 contour levels at the base, arrows to the extrema
#    max at (1/sqrt(2), 0), z = 0.429 ; min at (-1/sqrt(2), 0), z = -0.429
set hidden3d
set contour base
set cntrparam levels 8
set arrow 1 from 1.5,1.5,0.8 to 0.707,0,0.429 lw 2
set label 1 'Max' at 1.55,1.55,0.85
set arrow 2 from -1.5,-1.5,-0.8 to -0.707,0,-0.429 lw 2
set label 2 'Min' at -1.95,-1.55,-0.85
set output 'fig3d.png'
splot f(x,y) title 'x exp(-x^2-y^2)'

# 3) 2D: only the level curves (arrows redrawn without z)
set output 'fig2d.png'
set view map
unset surface
unset hidden3d
set arrow 1 from 1.5,1.5 to 0.707,0 lw 2
set label 1 'Max' at 1.55,1.55
set arrow 2 from -1.5,-1.5 to -0.707,0 lw 2
set label 2 'Min' at -1.95,-1.6
splot f(x,y) notitle
```

**How it works:**

- `set table 'grid.dat'` … `unset table` writes the 40×40 grid before any contour setting.
- `set hidden3d` hides the lines behind the surface; `set contour base` + `levels 8` adds the level curves.
- The extrema come from the derivative: x = ±1/√2, y = 0, z = ±0.429; one arrow and label each.
- 2D figure: `view map`, `unset surface`, `unset hidden3d`, and the arrows redefined with 2 coordinates.

**Adapt it:**

Extrema of x·e^(−x²−y²): ∂/∂x = 0 → x = ±1/√2, y = 0, z = ±0.429. `set cntrparam levels 8` asks for ~8 levels (gnuplot rounds to nice values); exactly 8 → `set cntrparam levels incremental -0.4,0.1,0.3` or `levels discrete` with 8 values.

### EX-GP-04 · Gray shaded surface, contours from −1 to 1 every 0.2, exact title, units on axes, key title

**From:** Week notebook 3.3 (exam-style variant of 2022-23 Ex 2)  
**Search:** pm3d implicit at s · palette gray · unset colorbox · set view 60,30 · key title · exact title string · x/nm

Reproduce a "make this picture" style question:

- Function g(x,y) = cos(x·y/2)·exp(−0.1(x²+y²)) on x, y ∈ \[−4, 4\], with a 40×40 grid.
- The surface is drawn in **gray**. Contours at the base, from −1 to 1 in steps of 0.2.
- `set view 60,30`. Title exactly: `cos(x*y/2)*exp(-0.1*(x**2+y**2)). View: 60,30`.
- Axis labels `x/nm`, `y/nm`, `E/eV`. Key (legend) title `Energy`, with the surface entry called `surface`.
- Output: `variant.png`.

variant.gp

```gnuplot
set terminal pngcairo size 900,700
set output 'variant.png'
g(x,y) = cos(x*y/2)*exp(-0.1*(x**2+y**2))
set xrange [-4:4]
set yrange [-4:4]
set samples 40,40
set isosamples 40,40
set xlabel 'x/nm'
set ylabel 'y/nm'
set zlabel 'E/eV'
set title 'cos(x*y/2)*exp(-0.1*(x**2+y**2)). View: 60,30'
set view 60,30
set key outside left title 'Energy' box
set contour base
set cntrparam levels incremental -1,0.2,1
set pm3d implicit at s     # shade every surface ('with pm3d' alone draws NO contour lines)
set palette gray
unset colorbox
splot g(x,y) with lines lc rgb 'gray40' title 'surface'
```

**How it works:**

- `set pm3d implicit at s` shades the surface, `set palette gray` makes it gray, `unset colorbox` removes the colour scale.
- The surface is drawn `with lines` so the contours still appear (`with pm3d` alone would drop them).
- The key title, the exact title string and the units on the axes are each one `set` command.

**Adapt it:**

An alternative gray surface: `set hidden3d` plus `splot g(x,y) with lines lc rgb 'gray' title 'surface'` (a gray mesh instead of a shaded one). Either is acceptable unless the question shows a specific style.

### EX-GP-05 · One script, two figures: surface with contours, then a 2D level-curve map

**From:** Day 0 notebook, Part C  
**Search:** set output twice · second figure · view map · unset surface · splot

Plot f(x,y) = sin(x)·cos(y) on \[−3, 3\]²: a 3D surface with contours on the base every 0.25 from −1 to 1 (`surf.png`), then a 2D map with only the level curves (`map.png`).

surf.gp

```gnuplot
set terminal pngcairo size 800,600
set output 'surf.png'
f(x,y) = sin(x)*cos(y)
set xrange [-3:3]; set yrange [-3:3]
set xlabel 'x'; set ylabel 'y'; set zlabel 'z'
set title 'sin(x)cos(y)'
set isosamples 30,30
set hidden3d
set contour base
set cntrparam levels incremental -1,0.25,1
splot f(x,y) title 'surface'

# second figure: 2D map with only the level curves
set output 'map.png'
set view map
unset surface
unset hidden3d
splot f(x,y) notitle
```

**How it works:**

- Figure 1: surface + base contours every 0.25.
- Figure 2: a new `set output`, then `set view map` + `unset surface`. Settings carry over, so only the differences are needed before the second `splot`.

**Adapt it:**

Settings persist: for the 2nd figure only change what differs, set a new output, and `splot` again.

### EX-GP-06 · 2D plot of two functions with labels, title, legend and a dashed line

**From:** Week notebook 3.1  
**Search:** plot 2D · xrange 4\*pi · dt 2 dashed · lw · key · title

On \[0, 4π\], plot sin(x) and e^(−x/5)·cos(2x) with axis labels, a title, a legend, and two different line styles (one dashed). Save the result as `warmup.png`.

warmup.gp

```gnuplot
set terminal pngcairo size 800,500
set output 'warmup.png'
set xrange [0:4*pi]
set xlabel 'x'
set ylabel 'f(x)'
set title 'Warm-up'
set key top right
plot sin(x) title 'sin(x)' with lines lw 2, \
     exp(-x/5)*cos(2*x) title 'exp(-x/5)cos(2x)' with lines dt 2 lw 2
```

**How it works:**

- `plot f(x) title '...' with lines, g(x) ...` draws both curves in one command, separated by a comma.
- `dt 2` = dashed, `lw 2` = thicker; `set key` places the legend; `pi` is built into gnuplot.

**Adapt it:**

Data from a file instead of a formula → `plot 'table1.out' using 1:2 with linespoints title 'v1'`.

### EX-GP-07 · Egg-box potential: gray surface, base contours every 0.1, key outside in a box titled Levels, arrow + label to the maximum, 2D map, grid.dat on 40×40

**From:** Mock exam 2 (11 Nov), Ex 2.1 (1 point)  
**Search:** splot · pm3d · palette gray · set view 60,40 · contour base · cntrparam levels incremental · cntrlabel format · key outside box title · set arrow · set label Max · set table grid.dat · isosamples 40,40 · view map · unset surface

Represent

E(x,y) = cos(x)·cos(y)·exp(−(x² + y²)/8)

for x in \[−4, 4\] and y in \[−4, 4\]. Label the axes `x/Å`, `y/Å` and `E/eV`, and use the title `Egg-box potential. View: 60,40` with that view. The surface is depicted in gray, and the level curves, separated by 0.1, are drawn at the base. Put the key outside the plot in a box titled `Levels`. Show an arrow pointing to the global maximum with a label `Max`. Generate two png figures: `surface3d.png` (the 3D surface with the contours at the base) and `map2d.png` (a 2D map with only the level curves and the arrow). Also generate a file `grid.dat` with the x, y and E values on a 40×40 grid.

**Cues (the prompt says → you write):**

| The prompt says                                    | You write                                                                                           |
|----------------------------------------------------|-----------------------------------------------------------------------------------------------------|
| E(x,y) = …                                         | `f(x,y) = cos(x)*cos(y)*exp(-(x**2 + y**2)/8)` (powers are `**`)                                    |
| “x in \[−4, 4\] and y in \[−4, 4\]”                | `set xrange [-4:4]` · `set yrange [-4:4]`                                                           |
| labels and title                                   | `set xlabel 'x/Å'` · `set ylabel` · `set zlabel` · `set title '…'` (copy the text exactly)          |
| “with that view”                                   | `set view 60,40`                                                                                    |
| “depicted in gray”                                 | `set pm3d` + `set palette gray`                                                                     |
| “level curves separated by 0.1 at the base”        | `set contour base` + `set cntrparam levels incremental -0.4,0.1,1.0`                                |
| “key outside … in a box titled Levels”             | `set key outside right top box title 'Levels'`                                                      |
| “arrow pointing to the global maximum … label Max” | find the max: (0,0), E = 1 → `set arrow 1 from … to 0,0,1` + `set label 1 'Max' at …`               |
| “two png figures”                                  | `set terminal pngcairo`; `set output 'name.png'` before each `splot`                                |
| “2D map with only the level curves”                | `unset pm3d` · `unset surface` · `set view map`                                                     |
| “grid.dat … on a 40×40 grid”                       | `set isosamples 40,40` + `set table 'grid.dat'` · `splot` · `unset table`, **before** `set contour` |

surface.gp

```gnuplot
set terminal pngcairo
f(x,y) = cos(x)*cos(y)*exp(-(x**2 + y**2)/8)
set xrange [-4:4]
set yrange [-4:4]
set xlabel 'x/Å'
set ylabel 'y/Å'
set zlabel 'E/eV'
set title 'Egg-box potential. View: 60,40'

# 1) the data file FIRST, before set contour (otherwise the contour lines go into the file too)
set samples 40,40
set isosamples 40,40
set table 'grid.dat'
splot f(x,y)
unset table

# 2) the 3D picture
set view 60,40
set contour base
set cntrparam levels incremental -0.4,0.1,1.0     # start, step, end
set cntrlabel format "%.1f"                       # stops a "5.55e-17" label for 0
set key outside right top box title 'Levels'
set pm3d                                          # coloured surface
set palette gray
set arrow 1 from 2.5,2.5,1.3 to 0,0,1 lw 2        # max of cos*cos*exp is at (0,0), E = 1
set label 1 'Max' at 2.6,2.6,1.35
set output 'surface3d.png'
splot f(x,y) title 'E(x,y)'

# 3) the 2D map: same contours, seen from above, no surface
set output 'map2d.png'
unset pm3d
unset surface
set view map
set arrow 1 from 2.5,2.5 to 0,0 lw 2              # redefine arrow 1 in 2D
set label 1 'Max' at 2.6,2.6
splot f(x,y) notitle
```

**How it works:**

- **Order of the script:** function, ranges, labels and title (shared by both pictures) → the data file → the 3D settings → `set output` + `splot` → change what differs for the map → `set output` + `splot` again.
- **grid.dat first:** `set table 'grid.dat'` redirects the next `splot` into a text file (x, y, E per line) instead of a picture. If `set contour` were already on, the contour lines would be written into the file too. `isosamples 40,40` (and `samples`) set the 40×40 grid.
- **Where is the maximum?** cos(0) = 1 and exp(0) = 1, so E(0,0) = 1, and |cos| ≤ 1 with exp ≤ 1 elsewhere, so nothing is larger. The arrow goes `from` any free point `to 0,0,1`; the label sits next to the arrow's tail.
- **Contours:** `levels incremental start,step,end` draws a curve every 0.1. The function runs from about −0.4 to 1, so those are the ends. `set cntrlabel format "%.1f"` prints the 0 level as 0.0 in the key instead of 5.55e-17.
- **Gray surface:** `set pm3d` colours the surface by height; `set palette gray` makes that colour scale black-to-white.
- **The key:** `outside` puts it beside the plot, `box` draws a frame, `title 'Levels'` heads it.
- **The 2D map:** `unset pm3d` and `unset surface` remove the surface so only the contour curves remain; `set view map` looks from above. The arrow and label are redefined with only x, y (same number 1, so they replace the 3D ones).
- `set output` must come **before** the `splot` that draws into it; each new `set output` starts a new png file.

**Check:** after `gnuplot surface.gp`:

``` plaintext
grep -v '^#' grid.dat | grep -c .     ->  1600   (40 x 40 data lines)
ls *.png                               ->  map2d.png  surface3d.png
```

**Adapt it:**

Another view → `set view rot_x,rot_z` and the title. Contour step → change the middle number of `incremental`. Fixed number of levels → `set cntrparam levels 8`. Colour instead of gray → leave out `set palette gray`. Minimum too → a second arrow (`set arrow 2 …`). Wireframe instead of pm3d → `splot f(x,y) with lines`. Look at the picture on the exam machine: `display surface3d.png &` or `eog`.

How do you know the view, isosamples, levels? The view and step are given by the question; the grid size is your choice unless the question gives it (here 40×40 for the data file).

### EX-GP-08 · Michaelis–Menten fit of rates.dat with fit … via a, b, fitted values in the title (sprintf), key below in a box, line width 2

**From:** Mock exam 2 (11 Nov), Ex 2.2 (0.5 points)  
**Search:** fit via · rates.dat · Michaelis-Menten · starting values · sprintf title · key below horizontal box · using 1:2 · with points · lw 2

The file `rates.dat` contains measured reaction rates v (column 2) for several substrate concentrations \[S\] (column 1). Fit the data with the Michaelis–Menten function v(x) = a·x/(b + x), and plot the data as points and the fitted function as a line of width 2 in `fit.png`. The title must show the fitted values of a (Vmax) and b (Km). The key goes below the plot, horizontal and in a box. Label the axes `[S] (M)` and `v (M/s)`.

**Cues (the prompt says → you write):**

| The prompt says                                         | You write                                                                                    |
|---------------------------------------------------------|----------------------------------------------------------------------------------------------|
| “fit the data with v(x) = a·x/(b + x)”                  | define `v(x) = a*x/(b + x)`, give starting values, `fit v(x) 'rates.dat' using 1:2 via a, b` |
| “column 1 … column 2”                                   | `using 1:2`                                                                                  |
| “data as points … fitted function as a line of width 2” | `with points` · `v(x) with lines lw 2`                                                       |
| “the title must show the fitted values”                 | `set title sprintf('… %.3e … %.4f', a, b)` **after** `fit`                                   |
| “key below, horizontal and in a box”                    | `set key below horizontal box`                                                               |
| “in fit.png”                                            | `set terminal pngcairo` + `set output 'fit.png'`                                             |

fit.gp

```gnuplot
set terminal pngcairo
set output 'fit.png'
v(x) = a*x/(b + x)
a = 1e-4                                  # starting guesses (roughly the max v, and an x in the middle)
b = 0.01
fit v(x) 'rates.dat' using 1:2 via a, b   # "via" = the parameters to adjust
set xlabel '[S] (M)'
set ylabel 'v (M/s)'
set title sprintf('Vmax = %.3e M/s, Km = %.4f M', a, b)   # sprintf puts the numbers into the title
set key below horizontal box
plot 'rates.dat' using 1:2 with points title 'data', \
     v(x) with lines lw 2 title 'fit'
```

**How it works:**

- `v(x) = a*x/(b + x)` defines the model with two unknown parameters.
- **Starting values:** `fit` improves guesses step by step; with the default value 1 it can fail for numbers this small. A good guess for a (Vmax) is about the largest v in the file; for b (Km) a concentration in the middle of column 1.
- `fit v(x) 'rates.dat' using 1:2 via a, b`: x from column 1, y from column 2, adjust a and b. Afterwards a and b hold the fitted values (gnuplot also prints them with their errors).
- `sprintf('%.3e', a)` formats a number into text: `%.3e` = scientific with 3 decimals, `%.4f` = fixed with 4 decimals. The title is set after `fit`, so it shows the fitted values.
- `set key below horizontal box`: below the plot, entries side by side, framed.
- `\` at the end of a line continues the command on the next line; the two curves are separated by a comma.

**Check:** with the mock rates.dat, `gnuplot fit.gp` prints (among the fit log):

``` plaintext
a               = 7.99075e-05      +/- 6.172e-07    (0.7723%)
b               = 0.00499627       +/- 0.0001931    (3.864%)
```

**Adapt it:**

Another model → change the function and the `via` list (e.g. a line: `f(x) = m*x + c` … `via m, c`; exponential: `f(x) = A*exp(-k*x)` … `via A, k`). Fit only part of the data → `fit [0:0.02] v(x) …`. Error bars in a column 3 → `using 1:2:3 with yerrorbars`. Fit fails (“Singular matrix”, nonsense values) → better starting values.

# 8. Exam day `[DAY]`

## 8.1 First 5 minutes: check the machine

```bash
python3 -c "import numpy, scipy, matplotlib; print('python OK')"
which python3 jupyter gfortran make gnuplot      # every tool you'll need, one line each
gnuplot -e "set terminal" 2>&1 | grep -i png     # is pngcairo there?
mkdir -p ~/exam && cd ~/exam && mkdir -p ex1 ex2 ex3 ex4 ex5   # one folder per exercise
```

| Problem                                                                  | Fix                                                                                                                    |
|--------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------|
| gnuplot: `unknown or ambiguous terminal type` for `pngcairo`             | `set terminal png size 900,700`                                                                                        |
| gnuplot opens a window instead of writing a file                         | `set terminal ...` and `set output 'name.png'` must come **before** `plot`/`splot`                                     |
| Python: no plot window appears                                           | `plt.savefig('fig.png')` (before `plt.show()`), then open the png                                                      |
| Python over ssh / no display: `cannot connect to X server` or a Qt error | first two lines of the script: `import matplotlib` and `matplotlib.use('Agg')`; then only `savefig`                    |
| no `jupyter`                                                             | write `ex4.py` and run `python3 ex4.py`: the exams accept “a notebook or a python script”                              |
| no `scipy`                                                               | distance matrix by broadcasting: `D = np.sqrt(((pos[:, None, :] - pos[None, :, :])**2).sum(axis=-1))` (EX-PY-07 f)     |
| a tool is missing on the lab machine                                     | check the cluster (`ssh cmm4@cerqt2.qt.ub.es`, then `which gfortran gnuplot`), and work there with `scp` to move files |

## 8.2 Editors, images, keeping the terminal free

| Action                           | nano            | vim                                                | gedit          |
|----------------------------------|-----------------|----------------------------------------------------|----------------|
| open                             | `nano f.py`     | `vim f.py`                                         | `gedit f.py &` |
| start typing                     | (just type)     | `i` (Esc to stop)                                  | (just type)    |
| save                             | Ctrl+O, Enter   | `:w`                                               | Ctrl+S         |
| quit                             | Ctrl+X          | `:q` (`:q!` = without saving, `:wq` = save + quit) | Ctrl+Q         |
| search                           | Ctrl+W          | `/word`, then `n`                                  | Ctrl+F         |
| go to line 42                    | Ctrl+\_ then 42 | `:42`                                              | Ctrl+I         |
| undo                             | Alt+U           | `u`                                                | Ctrl+Z         |
| paste without broken indentation | usually fine    | `:set paste` before, `:set nopaste` after          | fine           |

- `&` at the end (`gedit f.py &`, `eog fig.png &`) keeps the terminal usable while the window is open.
- Images: `eog fig.png &` or `xdg-open fig.png`. Text tables: `cat table1.out`, `column -t table1.out`.
- After pasting a Makefile: `sed -i 's/^ \+/\t/' Makefile` (copying from a terminal turns TABs into spaces). Check with `cat -A Makefile`: recipe lines must start with `^I`.

## 8.3 Running things

| What                                                 | Command                                               |
|------------------------------------------------------|-------------------------------------------------------|
| Python script                                        | `python3 ex4.py`                                      |
| …and stay inside afterwards to inspect variables     | `python3 -i ex4.py`                                   |
| notebook                                             | `jupyter notebook ex4.ipynb` (or `jupyter lab`)       |
| gnuplot script                                       | `gnuplot ex2.gp`, then `eog fig.png &`                |
| Fortran with make                                    | `make && ./prog` · from scratch: `make clean && make` |
| program that reads from the keyboard, without typing | `echo 10 | ./prog`                                    |
| bash script                                          | `bash ex1.sh` · or `chmod +x ex1.sh && ./ex1.sh`      |
| script that asks two questions, without typing       | `printf "a.dat\nb.dat\n" | bash larger.sh`            |
| script that asks one question                        | `bash inspect.sh <<< "results"`                       |

## 8.4 First look at an unfamiliar output file

```bash
wc -l piri.log                                   # how big is it?
grep -n 'SCF Done' piri.log | head               # each marker: where, and how many times?
grep -c 'Alpha  occ. eigenvalues' piri.log       # double space after Alpha!
grep -n -m1 -A6 'SCF ITERATIONS' orca.out        # the first block, with 6 lines after it
sed -n '120,140p' orca.out                       # print any range of lines
```

```python
with open('orca.out') as f:
    lines = f.readlines()
for k, line in enumerate(lines[120:140], start=120):
    print(k, repr(line))                         # repr shows spaces, tabs, numbers stuck together
print(list(enumerate(lines[130].split())))       # which index is the column I need?
```

Checklist before writing the parser:

- the exact marker text (copy it from the file; watch double spaces);
- how many times it appears (one per step? one per block?);
- what a data line looks like vs a header or blank line (`p and p[0].isdigit()`);
- whether the header changes inside a block (ORCA SOSCF);
- where a block ends;
- whether numbers can touch each other (fixed-width columns → slice, don’t `split()`).

## 8.5 Python errors → fix

| Message                                                                       | Usual cause                                         | Fix                                                                                                   |
|-------------------------------------------------------------------------------|-----------------------------------------------------|-------------------------------------------------------------------------------------------------------|
| `IndexError: list index out of range`                                         | `line.split()[k]` on a blank, short or header line  | only use rows you’ve checked: `p = line.split()` then `if p and p[0].isdigit():` or `if len(p) == n:` |
| `ValueError: could not convert string to float: 'Energy'`                     | converting a header word                            | skip non-data lines (as above)                                                                        |
| `ValueError: could not convert string to float: '-101.56032-101.56030'`       | fixed-width numbers that touch                      | slice: `[float(s[k:k+10]) for k in range(0, len(s), 10)]` (EX-PY-19)                                  |
| `ValueError: invalid literal for int() with base 10: '3.0'`                   | `int()` on a float string                           | `int(float(x))`                                                                                       |
| `ValueError: too many values to unpack (expected 2)`                          | `a, b = ...` with 3 values                          | count what the right-hand side returns                                                                |
| `FileNotFoundError: [Errno 2] No such file or directory: 'orca.out'`          | wrong folder or name                                | `import os; print(os.getcwd(), os.listdir())` · use the full path                                     |
| `ValueError: operands could not be broadcast together with shapes (5,3) (5,)` | dividing (N,3) by (N,)                              | `keepdims=True` in the norm, or `x[:, None]`                                                          |
| `ValueError: matmul: ... mismatch in its core dimension`                      | `A @ B` with (7,3) @ (7,3)                          | transpose one: `A @ B.T`                                                                              |
| `ValueError: index 5 is out of bounds for array with size 4`                  | a flat index used directly on a matrix              | `np.unravel_index(k, M.shape)`                                                                        |
| `RuntimeWarning: divide by zero encountered in log10` (gives `-inf`)          | log of 0 (e.g. ΔE at iteration 0)                   | drop it: `m = it >= 1` or `dE != 0`                                                                   |
| `RuntimeWarning: invalid value encountered in arccos` (gives `nan`)           | cosine slightly \> 1                                | `np.arccos(np.clip(c, -1, 1))`                                                                        |
| minimum distance is `0.0`                                                     | the diagonal (a particle with itself)               | `np.fill_diagonal(D, np.inf)` first                                                                   |
| results change every time you re-run a cell                                   | `rng.random` called again without re-creating `rng` | re-run from the cell that creates `rng`, or restart and run all                                       |

## 8.6 Units and constants

| Quantity             | Value                                                                     |
|----------------------|---------------------------------------------------------------------------|
| 1 hartree (Ha, a.u.) | 627.5095 kcal/mol = 2625.500 kJ/mol = 27.211386 eV = 219474.63 cm⁻¹       |
| 1 eV                 | 23.0605 kcal/mol = 96.4853 kJ/mol = 8065.54 cm⁻¹                          |
| 1 kcal               | 4.184 kJ                                                                  |
| 1 bohr (a₀)          | 0.529177 Å · 1 Å = 1.889726 bohr                                          |
| R                    | 8.314462 J mol⁻¹ K⁻¹ = 0.0820574 L atm mol⁻¹ K⁻¹ = 1.987204 cal mol⁻¹ K⁻¹ |
| k_B                  | 1.380649×10⁻²³ J/K = 8.617333×10⁻⁵ eV/K                                   |
| N_A                  | 6.02214076×10²³ mol⁻¹                                                     |
| h · c                | 6.62607015×10⁻³⁴ J s · 2.99792458×10⁸ m/s                                 |
| pressure             | 1 atm = 101325 Pa = 1.01325 bar · 1 L atm = 101.325 J                     |
| π                    | Python `np.pi` · Fortran `acos(-1.0d0)` · gnuplot `pi`                    |

In Fortran, write constants in double precision: `8.314d0`, `6.022d23`.

## 8.7 Handing in

**Python:** a notebook or a script is accepted.

```bash
jupyter nbconvert --to python ex4.ipynb          # ex4.ipynb -> ex4.py
python3 ex4.py                                   # must run top to bottom with no error
```

In a notebook: Kernel → Restart & Run All before saving, so every cell has run in order.

**Fortran:** sources + Makefile, tested from scratch.

```bash
cd ~/exam/ex3 && make clean                      # hand in sources, not .o / .mod / executables
cd ~/exam
tar czf Boyd_ex3.tar.gz ex3                      # or: zip -r Boyd_ex3.zip ex3
tar tzf Boyd_ex3.tar.gz                          # list what's inside
mkdir -p /tmp/check && tar xzf Boyd_ex3.tar.gz -C /tmp/check && cd /tmp/check/ex3 && make && ./prog
```

The last line unpacks your archive somewhere else and builds it there, exactly as the teacher will.

**Gnuplot:** the `.gp` script, plus the png files and data file it writes. **Bash:** the `.sh` files, each starting with `#!/bin/bash`.

**Before you leave:**

- Every file the question names exists with exactly that name (`table1.out`, `values.dat`, …).
- The output files are included if the question asks for them.
- Your name is in a comment at the top of each file.
- If allowed, keep a backup of the whole folder on the cluster: `scp -r ~/exam cmm4@cerqt2.qt.ub.es:~/exam_backup`
