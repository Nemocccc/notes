### make

for C/C++, u define commands in ``Makefile``, like ``Dockerfile``

***when you run ``make``, it will try to find Makefile in current dictionary,and run all commands in Makefile***

***if u just want to run one of the commands, run``make [target]``***

**Rule** : 

```makefile
target-file: prerequisites-1 prerequisites-1
	commands
```

such as:

```makefile
x.txt: a.txt b.txt
	cat a.txt b.txt > x.txt #合并a.txt,b.txt作为x.txt
```

**phony-target**

```makefile
# ...other commands

clean:
	rm -f xx.xxx   #ponder: could we run other commands in ``clean`` command?
	
# ...
```

such as:

```makefile
clean:
	rm -f a.txt
	rm -f b.txt
```

if u had made a ``clean.file`` it can disturb ``make`` recognizing ``clean`` command, or you can explicit declare:

```makefile
.PHONY: clean #tell make: do not recognize clean as a file
clean:
	...
```

other phony targets: ``install``

**multi-rules**

first, example:

```makefile
cd:
	pwd
	cd ..
	pwd
```

result:

```bash
$ make cd
pwd
/home/ubuntu/makefile-tutorial/v1
cd ..
pwd
/home/ubuntu/makefile-tutorial/v1
```

why the result of ``pwd`` is the same before and after the command ``cd ..``?

because make isolate a shell environment for every commands, command like single `cd ..` would not influence the environment of other commands.

to see right result, you can separate each successive command with `;` like:

```makefile
cd_ok:
	pwd; \
	cd .. ; \
	pwd
	echo "right way"
```

**do not print command(still print execute info)**

add `@` before the target command:

```makefile
no_output:
	@echo 'not display'
	echo 'display'
```

run `make` and u will see:

```bash
$ make no_output
not display
echo 'will display'
will display
```

**control ERROR**

when make is executing commands, if an ERROR occur(return none-zero-value), it will stop working.

such as you delete an not exist file.

---

in some moments, we want to ignore some ERROR and keep executing latter commands, add `-` before the command like:

```makefile
ignore_error:
	-rm zzz.xxx
	echo 'OK'
```

you can ignore commands that can cause ERROR but not disturb logic.

