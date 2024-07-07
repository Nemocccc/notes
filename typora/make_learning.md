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

**make `.cpp` files**

examples:

- `hello.h`:

  ```cpp
  // hello.h
  
  #ifndef HELLO_H
  #define HELLO_H
  
  int hello();
  
  #endif
  ```

- `hello.cpp`:

  ```cpp
  #include<iostream>
  #include "hello.h"
  
  int hello(){
  	std::cout << "hello, world" << std::endl;
      
      return 0;
  }
  ```

- `test.cpp`:

  ```cpp
  #include<iostream>
  #include "hello.h"
  
  int main(){    //only one main function is allowed
      std::cout << "preparing" << std:endl;
      hello():
      std::cout << "ok" << std::endl;
      
      return 0;
  }
  ```

- `Makefile`:

  ```makefile
  test.out: test.o hello.o
  	g++ -o test.out test.o hello.o    #the first file after `-o` should be the file to be generate
  	
  hello.o: hello.h hello.cpp
  	g++ -c hello.cpp  #not similar to `-o`, the `-c` could automatically generate .o files
  	
  test.o: test.cpp hello.h
  	g++ -c test.cpp
  	
  clean:
  	rm -f *.o #remove all files with `.o` file suffix
  	rm -f test.out
  ```

---

similar to `Dockerfile`,  make only build and compile files that had changed(if you had run `make` before),such as:

```bash
root@server-235-a4000:/anything/project# make
g++ -c test.cpp
g++ -c hello.cpp
g++ -o test.out test.o hello.o
root@server-235-a4000:/anything/project# make
make: 'test.out' is up to date.
```

if u change `hello.cpp`, it only re-compile `g++ -c hello.cpp`.

**implicit rule**:

use the former project as example, if you delete `.o` rules, just run:

```makefile
test.out: test.o hello.o
	g++ -o test.out test.o hello.o
	
clean:
	rm -f *.o test.out
```

it okay and compile successfully:

```bash
root@server-235-a4000:/anything/project# make
g++    -c -o test.o test.cpp
g++    -c -o hello.o hello.cpp
g++ -o test.out test.o hello.o
```

it is because make had built-in implicit rules for `c`, `cpp`, `asm`, `fortran` and so on. if commands in makefiles lacks of defines of rules,it will automatically use implicit rules, however not all commands have implicit rules.

such as for `cpp`, if `.o` lacks of rules, it will run:

```makefile
xxx.o: xxx.cpp
	g++ -c -o xxx.o xxx.cpp
```

but for c/cpp, it can cause some problems: if we change the define of `.h`, as implicit rule of .o could not trace the .h files, at last the .o would not re-compile.
for more, 
[GNU Make - 内置规则目录](https://runebook.dev/zh/docs/gnu_make/catalogue-of-rule)

[Implicit Rules (GNU make)](https://www.gnu.org/software/make/manual/html_node/Implicit-Rules.html)

**variables**: to solve the repetition problems.

use the former Makefile as example, we can change it to this form:

```makefile
TARGET = test.out

$(TARGET): test.o hello.o
	g++ -o $(TARGET) hello.o test.o
	
clean:
	rm -f *.o $(TARGET)
```

define format: `variable name = value` or `variable name = value`.

usually define variable name with capital letter.

---

automatically generate object list:

```makefile
# $(wildcard *.cpp)list all files with .cpp cuffix in current dictionary
# use function patsubst to mode-replace so we can get hello.o and test,o
OBJS = $(patsubst %.cpp, %.o, $(wildcard *.cpp))
TARGET = test.out

$(TARGET): $(OBJS)
	g++ -o $(TARGET) $(OBJS)
	
clean:
	rm -f *.o $(TARGET)
```

**built-in variables**: ...

**automatic variables**

some variables in makefiles such as `$@` , `$^` and `$<` called automatic variables,they automatically refer to relevant values. for example:

```makefile
# %@ refer to target files, $^ refer to prerequisites.
test.out: test.o hello,o
	$(++) -o $@ $^ #can cause ambiguity
	# $(++) -o $(@) $(^) #avoid ambiguity
```

for better debug:

```makefile
test.out: test.o hello.o
	@echo '$$@ = $@' //print target files
	@echo '$$< = $<' //print the first prerequisites
	@echo '$$^ = $^' // print all prerequisites
	g++ -o $@ $^
```

in this case i guess $ in makefile may work like \ in cpp(\n and \\n).

**mode-rule**

> define by yourself, more flexible than implicit rules

```makefile
OBJS = $(patsubst %.cpp, %.o, $(wildcard *.cpp))
TARGET = test.out

$(TARGET): $(OBJS)
	g++ -o $(TARGET) $(OBJS)
	
#mode-rules: when make **need** target xxx.o, automatically generate a xxx.o: xxx.c rule
%.o: %.cpp
	@echo 'compiling $<...'
	g++ -c -o $@ $^

clean:
	rm -f *.o $(TARGET)
```

however, this still cant solve the problem of changing .h could not make .cpp update

**automatically prerequisites generation**

parameter `-MM` can automatically find out the .h prerequisites of .cpp,with using `g++ -MM test.cpp`

```makefile
SRCS = $(wildcard *.cpp)

OBJS = $(SRCS:.cpp=.o) # generate .o list base on SRCS
DEPS = $(SRCS:.cpp=.d) # generate .d list base on SRCS

TARGET = test.out

$(TARGET): $(OBJS) #default target files
	g++ -o $@ $^
	
%.d: %.c # rules of .d generate by xxx.c
	rm -f $@; \
	g++ -MM $< >$@.tmp; \ # generate prerequisites infomation and save them to %.d(target).tmp, .tmp represent temporary files
	sed 's,\($*\)\.o[ :]*,\1.o $@ : ,g' < $@.tmp > $@; \ # sed(stream editor)
	rm -f $@.tmp

%.o:%.c
	g++ -c -o $@ $<
	
clean:
	rm -f *.o *.d $(TARGET)
	
include $(DEPS) #include all .d files
```

