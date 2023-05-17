# Intro

|Linux|Docker|
| :--: | :--: |
|Boots in minutes|Boots in seconds|
|More resource usage|Less resource usage|
|Creating VMs take a long|Docker can be created in seconds|


# Types and features of Linux Files
|Symbols|File types|
|:-:|:-:|
|-|Regular File|
|d|Directory File|
|b|Block Device File|
|c|Character device File|

#### Device files:
are special files that represent hardware devices

```sh
ls -l /dev/full/
```

#### Pipes:
special files, process communication between different processes 

```sh
mkfifo pipe1
file pipe1

ls -al pipe1

mknod pipe2 p 
file pipe2
```

#### Symbolic LInk:
(like a shortcut in windows)

```sh
ln -s file1 file2
```

| |Hard Link|Symbolic Link|
|:-:|:-:|:-:|
|Command|`ln [originaFlile] [linkFile]`| `ln -s [originalFile [linkFile]`
|Types|Creates files and directories|Create files only|
|Function|Points to a link to file or directory name|A reference or pointer to the original file|

We can also create Hard links to directories

```sh
ln -d /path/to/dir link-to-dir
```




