---
title: cppcheck manual
date: 2018-10-16 23:49:32
tags: cppcheck
categories: 静态代码分析
---

#### Cppcheck

##### first test

- cppcheck file1.c //默认是 error 选项

##### check all files in a folder

- cppcheck path

##### exclude a file or folder from checking

- 第一种：cppcheck -isrc/b -isrc/c // -i 代表 ignore
- 第二种：只 include 需要的目录

##### severities

- error，warning，style，performance，portability，information

##### enable messages

- cppcheck --enable=performance,warning file.c //=两边没空格

##### save results in file

- cppcheck file.c 2> err.txt  // **2>**

##### multithread check

- cppcheck -j 4 path            // -j是多线程开关，4是线程数

##### cmake

- example:

  ```bash
  $ cmake -DCMAKE_EXPORT_COMPILE_COMMANDS=ON
  #the file compile_commands.json is created in the current folder.
  #then run cppcheck like this:
  $ cppcheck --project=compile_commands.json
  ```

##### visual studio

- you can run cppcheck on individual project files(*.vcxproj) or on a whole solution( *.sln)

  ```bash
  #run cppcheck on a whole solution
  $ cppcheck --project=foobar.sln
  #run cppcheck on a individual project
  $ cppcheck --project=foobar.vcxproj
  ```

##### XML output

- cppcheck can generate output in XML format. Use --xml to enable this format.

##### example

- cppcheck -j 4 --enable=warning,performance --inconclusive --xml --xml-version=2 --project=*path*\servers.sln 2> *path*\cppcheck.xml