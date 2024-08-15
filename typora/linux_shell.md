 **``ll [dictionary]``** :

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

 `cd -`

 rapid change of work dictionary

---

 `echo`

 `echo <string>` : print <string> directly on console.

 `echo <string> > <file>` : overlay write <string> into files. 

> ` >> ` : readdition mode


---

 `cat`

 `cat <files>` : print the content of files.  mostly use.

 `cat < <files>` : similar to the former, seldom use, when use , mostly combined with other commands likes `sort < hello.txt | cat`

 `cat < <file1>  > <file2>` : reload input to file1, and reload output to file2, overwrite.