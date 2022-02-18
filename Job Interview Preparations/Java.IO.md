# Java IO / NIO

## Basics 基础

- Java IO 主要关注输入与输出，针对于 Bytes 字节流使用 ByteArray 相关的 Input/OutputStream，针对于 Character 字符流可以使用 Reader/Writer 相关类；
  + 在使用输入/输出的时候，尽可能带上 BufferedReader/InputStream 这样的装饰类，以提供缓冲功能，提升大容量的输入输出效率；
- Java IO 用到了 Decoration Pattern 装饰器模式 来提供接口输入和输出；

## Issues

- Q: 如何在 JVM 中处理大文件？
- A: 内存有限的情况下，就需要使用 Stream 流 的方式来处理大文件，比如 压缩/解压缩，加解密的处理，都有通用的针对 Stream 的接口来处理，但如果没有 Stream 接口的话，就需要从业务角度来考虑截断处理部分/分段处理了；