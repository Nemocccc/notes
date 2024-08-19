***[2.shell工具和脚本_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1XN4y187zp/?p=3&vd_source=8a614553fd42ac4044499e810a8166aa)***







``ll [dictionary]``:

 - to see detail information of files in the dictionary

 - example: 

   ```bash
   root@server-235-a4000:/anything# ll .
   total 4
   authority links owner group blocks last-interview
   drwxrwxrwx 4 1021 1021   47 Jul  5 08:10 ./
   drwxr-xr-x 1 root root 4096 Jul  6 03:45 ../
   drwxrwxrwx 2 1021 1021   50 Jul  5 08:10 config/
   drwxrwxrwx 2 root root   30 Jul  5 07:53 project/
   ```

 - in the first colume:
   'd' means "dictionary"
   'r' means "read"
   'w' means "write"
   "x" means "exec"
   '-' means "no authority"

---

`cd -` : 

 rapid change of work dictionary

---

`echo`

-  `ech o   <string>` : print <string> directly on console.

- `echo <string> > <file>` : overlay write <string> into files. 

> ` >> ` : readdition mode


---

`cat`

- `cat <files>` : print the content of files.  mostly use.

- `cat < <files>` : similar to the former, seldom use, when use , mostly combined with  other commands likes `sort < hello.txt | cat`

- `cat < <file1>  > <file2>` : reload input to file1, and reload output to file2, overwrite.

---

**shell script** : `touch ****.sh`

- function:

```bash
**** () {
	cd try_something/config/
	docker exec -it anything_ubuntu22basic_image bash
	# here, $0 is the name of function, $1~$9...refer to the parameters. 
}
```

---


```bash
mkdir test
cd $_
```

is equal to:

```bash
mkdir test
cd test
```

`$_` is equal to `$1` (the first parameter) of the former expression.

---

`!!` , will be replaced by the former expression.

for example, if your operation not permitted, you can `sudo !!`.

```bash
$ rmdir test
operation not permitted
$ sudo !! # is equal to sudo rmdir test here.
```

---

`$?` refers to the exit status of your former commands

```bash
grep foobar sd.sh
echo $? #if no foobar in sd.sh, it would be 1, if not, it would be 0.
```

---

- `$#` :  The number of parameters of the command

- `$$` : process id of the command of what is running.

- `$@` : all  the arguments of the command.

---

`grep`

- `docker-compose ps || grep Up` : find if the output of `docker-compose ps` contains 'Up'.

- `grep <string> <filename>` : find <string> in <file>
- `grep "pattern" file1 file2 file3` : find same "pattern0" in multiple files.
- `grep -r "pattern" /path/to/directory` : find "pattern" in dir and subdir.
- `grep --help`

---

`tail` : 

- `tail --help`
- `tail <file>` : print the last 10 lines of file(by default) -> in reverse order
- `tail -n -20 <file>` : in order
- `tail -f <file>` : always trace the latest content of file.

---

- `> / <` standard reload
- `>> / <<` add reload
- `2>` standard error *output* reload (no`2<`, not legal)
- `&>`  standard *output* reload and standard error *output* reload (by default /dev/null)

---

`ls` : 

- `ls *.sh` : find files with special suffix.

- ```bash
    mkdir project1 project2
    mkdir project23
    ls project?  #project1 project2
    ls project?*   #project1 project2 project23
    ```

---

`convert` : image processing

- `convert input.jpg output.png` : change format.

- `convert input.jpg -resize 800x600 output.jpg` : change height and width.

- `convert input.jpg -crop 100x100+50+50 output.jpg` : cut image
- `convert input.jpg -rotate 90 output.jpg` : rotate the image.
- `convert input.jpg -colorspace Gray output.jpg` change color.
- `convert input.jpg watermark.png -gravity south -composite output.jpg` use another iamge as watermark.
- `convert image1.jpg image2.jpg -append output.jpg` : combine mult image into one.

---

`{}` : 

1. **brace expansion**

- `touch project{1, 2}/src/test/test{1, 2, 3}.py `: this can touch totally 6 files at two different directory.

- `convert input.{png, jpg}` : ?
- `touch {foo,bar}/{a..j}` : touch foo/a ~ foo/j , bar/a~bar/j
- ```bash
  touch foo/s bar/y
  diff <(ls foo) <(ls bar)
  ```
- 

---

`diff` : 

- `diff file1.txt file2.txt` : find the differents of two files.
- `diff -rq dir1 dir2` : find the differents of files in two directories.
- `diff --help`

---

`shellcheck` : shell/bash script debug tool

- ```bash
  sudo apt-get update && sudo apt-get upgrade
  sudo apt-get install -y shellcheck
  ```

- `shellcheck script.py`

- `shellcheck script1.sh script2.sh`

- `shellcheck $(find . -name "*.sh")`

- `shellcheck -s bash -e SC2046 script.sh`

- `shellcheck script.sh > check_results.txt`

---

`man` : manual of commands.

- `man ffmpeg` , `man rg` , `man tldr` and so on , run and u will know.

---

`find` :

- `find . -name src -type d` : find dir that named as src in current dir

- `find . -path '**/test/*.py' -type f` 
- ` find . -mtime -1` : -mtime -> modify time | -1 -> last day.
- `find . -name "*.tmp" -exec rm {} \` : `{}` -> all files find matched | `\` -> claim the end of `rm` command

- **fd-find** : better then find

---

`$()` : Used for command substitution, that is, to assign the output of a command to a variable

`${}` : Used for variable expansion, that is, inserting the value of a variable into a string.

---

`seq` : to generate a set of numbers.

- `seq <options> <start> <end>`
- `seq -f "%02g" 0 9`  : "%02g" => numbers is formatted as the smallest two-digit number

---















shell script practice : [shell脚本一天一练-day1_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1ih4y1Y7nh/?spm_id_from=333.788&vd_source=8a614553fd42ac4044499e810a8166aa)
