# How to Setup a new Macbook

## Homebrew

Homebrew Page:
https://brew.sh/

issue with installing homebrew on M1 Macbook Pro:
https://github.com/Homebrew/discussions/discussions/668

## Java - OpenJDK

manual install azul zulu openjdk 8, with Apple M1(ARM) Support:
https://www.azul.com/downloads/?version=java-8-lts&os=macos&architecture=arm-64-bit&package=jdk

OpenJDK Variants(distributions) Differences:
https://whichjdk.com/

Set JAVA_HOME after installation in ~/.zprofile, for example:

```sh
# Set HOME Paths
$ export JAVA_HOME=/Library/Java/JavaVirtualMachines/zulu-8.jdk/Contents/Home

$ export PATH=$JAVA_HOME/bin:$PATH
```

## Github

setup a new Github Personal Token for new laptop