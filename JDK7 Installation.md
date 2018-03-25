# JDK 7 Installation

First download Oracle's official RPM package suited for your architecture and convert it with the **alien** command.

```
$ sudo alien --scripts jdk-7u11-linux-x64.rpm 
jdk_1.7.011-1_amd64.deb generated
```

This may take a while. Once ready, install this package with the **dpkg** command:

```
$ java -version
The program 'java' can be found in the following packages:
```

at this point no Java is available on the system.

```
$ sudo dpkg -i jdk_1.7.011-1_amd64.deb 
Selecting previously unselected package jdk.
(Reading database ... 48744 files and directories currently installed.)
Unpacking jdk (from jdk_1.7.011-1_amd64.deb) ...
```

Now test for the Java version:

```
$ java -version
java version "1.7.0_11"
Java(TM) SE Runtime Environment (build 1.7.0_11-b21)
Java HotSpot(TM) 64-Bit Server VM (build 23.6-b04, mixed mode)
```