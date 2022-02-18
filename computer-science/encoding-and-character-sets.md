# Encoding and Character Sets

## What are ASCII(provounced /ˈaski/), UTF-8, UTF-16? Why do we have them?
(editted from this [answer](https://stackoverflow.com/a/53647515))

ASCII - Software allocates ONLY 8-bit (ONE byte) for a givin character, its decimal value falls below 128. Works well for English.

UTF-8 - Software allocates 1 to 4 variable 8-bit bytes for a given character. 1 byte, 2 bytes, 3 bytes or 4 bytes are okay in UTF-8. For example, character 'A' has decimal value 65, when converts to Binary, its value is 0100 0001. This 8 bits value means it requires only 1 byte to store. However, when storing European chracters, it requires 2 bytes, now ASCII is not enough, so we need UTF-8 here. However, when go for Asian characters, it requires minimum of 2 bytes and maximum of 4 bytes. Even for Emoji, it requires 3 to 4 bytes. In these situations, UTF-8 will solve all your needs. (p.s. 1 byte memory is allocated even for special adopted English characters like 'ç' in a word façade.)

UTF-16 - Software allocates MINIMUM 2 bytes and MAXIMUM of 4 bytes per character. It will not allocate 1 or 3 bytes, and each character is either represented in 16 bits or 32 bits.

### Then why exists UTF-16?
Originally, Unicode was 16 bit not 8 bit. Java adopted the original version of UTF-16.

In a nutshell, you don't need UTF-16 anywhere unless it has been already been adopted by the language or platform you are working on.

Java program invoked by web browsers uses UTF-16 but the web browser sends characters using UTF-8.

see [ASCII Table](https://www.cs.cmu.edu/~pattis/15-1XX/common/handouts/ascii.html)
see [Stackoverflow Qs](https://stackoverflow.com/questions/2241348/what-is-unicode-utf-8-utf-16)
see [Kunststube's article](http://kunststube.net/encoding/)


