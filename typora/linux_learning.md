1. - **WSL Features ** :

     -WSL creates a `/home/<username>` directory by default, and the user is usually set as the owner of this directory. This means that within the `/home/<username>` directory and its subdirectories, you can create files and directories freely.

     - If you are trying to create files or directories in other locations, such as'/' root or other non-personal directories, then 'sudo' permissions may be required.

     and usually, `~` refer to the dir `/home/<username>`, they are the same. you can also see `/home/others`, but `~` always be your home, execute `pwd` to see.

     use `su - <username>` to change user.