---
title: Character encoding and charset in Java
toc: true
id: 460
categories:
  - Basics
date: "2015-03-16T19:08:24+00:00"
---

# Concepts

At the beginning, we need to distinguish some key concepts.

## character

Character (aka. abstract character) is a textual representation of character.

## glyph

glyph is the particular images representing a character or part of a character. Glyphs for the same character may have very different shapes

## abstract character repertoire (ACR)

A _character repertoire_ is the full set of unordered abstract characters that a system supports.

## coded character set (CCS)

A _coded character set_ (CCS) specifies how to represent a repertoire of characters using a number of (typically non-negative) integer values called <span style="color: #ffffff; font-size: 20px; background-color: #000000;">code points</span>.

A complete set of characters and corresponding integers is a _coded character set_.

Multiple coded character sets may share the same repertoire.

## _character encoding form_ (CEF)

A _character encoding form_ (CEF) specifies the conversion of a coded character set's integer codes into a set of limited-size integer _code values_ that facilitate storage in a system that represents numbers in binary form using a fixed number of bits.

This is what a CEF accommodates: it defines a way of mapping a _single_ code _point to a sequences of octets._

## character encoding scheme (CES)

a _character encoding scheme_ (CES) specifies how the fixed-size integer code values should be mapped into an octet sequence suitable for saving on an octet-based file system or transmitting over an octet-based network.

|  Character encoding|  Code unit width|
| --- | --- |
|  US-ASCII | 7 |
|  UTF-8 | 8 |
|  EBCDIC | 8 |
|  UTF-16| 16 |
|  UTF-32 | 32 |	


Notice: although UTF-8 use the eight-unit _code unit_, but it doesn't necessarily mean that each character is encoded as a single code unit. Actually, a character can be encoded as one or more than one _code units_ in UTF-8.

For code unit including more thant 8 bits(byte), there is a problem named as [endian](http://en.wikipedia.org/wiki/Endianness "Endianness") (byte order). BE is big-endian, while LE is little-endian.


# Java internal string representation

Java uses UTF-16BE encoding as its internal representation.
> UTF-16 developed from an earlier fixed-width 16-bit encoding known as **UCS-2** (for 2-byte Universal Character Set) once it became clear that a fixed-width 2-byte encoding could not encode enough characters to be truly universal.
> 
> 
> The encoding is [variable-length](http://en.wikipedia.org/wiki/Variable-width_encoding "Variable-width encoding"), as [code points](http://en.wikipedia.org/wiki/Code_point "Code point") are encoded with one or two 16-bit _code units_.
So, in Java:

* a _code point_ is a 32-bit `int` data type, where the lower 21 bits represent a valid code point value and the upper 11 bits are 0. (thus no negative code point)
* a _code unit_ is a 16-bit `char` value.
To express a Unicode character, the hexadecimal value is prefixed with the string U+.

| Character       | Unicode Code Point | Glyph |
|-----------------|--------------------|-------|
| Latin A         | U+0041             |![The Latin character A](https://docs.oracle.com/javase/tutorial/figures/i18n/000041.gif)       |
| Latin sharp S   | U+00DF             |![The Latin small letter sharp S](https://docs.oracle.com/javase/tutorial/figures/i18n/0000df.gif)       |
| Han for East    | U+6771             |![The Han character for east, eastern or eastward](https://docs.oracle.com/javase/tutorial/figures/i18n/006771.gif)       |
| Deseret, LONG I | U+10400            |![The Han character for east, eastern or eastward](https://docs.oracle.com/javase/tutorial/figures/i18n/006771.gif)       |

Characters that are in the range U+10000 to U+10FFFF are called supplementary characters. The set of characters from U+0000 to U+FFFF are sometimes referred to as the _Basic Multilingual Plane (BMP)_.
> U+10FFFF is the current upper limit of unicode defined code points.
Obviously, supplementary characters cannot be represented by a 16-bit `char.`

To support supplementary characters without changing the char primitive data type and causing incompatibility with previous Java programs, supplementary characters are defined by a pair of code point values that are called _surrogates_. The first code point is from the _high surrogates_ range of `U+D800` to `U+DBFF`, and the second code point is from the _low surrogates_ range of `U+DC00` to `U+DFFF`. You can simply think surogates ranges are the blocks reserved in BMP.

**Therefore, a unicode character can be represented by one `char` (BMP), or a surrogate pair (2 elements array of `char`).**

Until now, you shold be able to answer these questions:
- can any unicode character be represented by a Java char? No
- how do we initialize a char exceeding BMP with char literals? No

Simply, char ≠ unicode character

![Character_encoding_and_charset_in_Java___Richard_Yang](/media/Character_encoding_and_charset_in_Java___Richard_Yang.png)
The table above shows some of the interesting things we have to look out for.

1. Stored characters can take up an inconsistent number of bytes.
A UTF-8 encoded character might take between one (LATIN_CAPITAL_LETTER_A) and four (MATHEMATICAL_FRAKTUR_CAPITAL_G) bytes.[Variable width encoding](http://en.wikipedia.org/wiki/Variable-width_encoding) has implications for reading into and decoding from byte arrays.

1. Not all code points can be stored in a `char`.
The MATHEMATICAL_FRAKTUR_CAPITAL_G example lies in the supplementary range of characters and cannot be stored in 16 bits. It must be represented by two sequential `char` values, neither of which is meaningful by itself. The [Character](http://java.sun.com/javase/6/docs/api/java/lang/Character.html) class provides methods for working with 32-bit code points.



```java
// Unicode code point to char array
char[] math_fraktur_cap_g = Character.toChars(0x1D50A);
```

1. The relationship between the grapheme visible to the user and the code point type may not be 1:1.

This can be seen in the combining character sequences (the e-acute example). As the Devenagari example shows, combining sequences can get quite complex.

![java-utf-16be](/media/java-utf-16be.png)

# Character and String API

![character-api](/media/character-api.png)

![string-api](/media/string-api.png)


## How long is a string?

- [String.length()](http://java.sun.com/javase/6/docs/api/java/lang/String.html#length()) returns the number of `char`s in the String. That's also the number of code units.
- [String.codePointCount(int, int)](http://java.sun.com/javase/6/docs/api/java/lang/String.html#codePointCount(int,%20int)) returns the number of Unicode **code points** in the String.
- [BreakIterator.getCharacterInstance()](http://java.sun.com/javase/6/docs/api/java/text/BreakIterator.html#getCharacterInstance(java.util.Locale)) can be used to count the number of **graphemes** in a String.

![](/media/how-long-string.png)
# Play with charsets and encodings in Java

Here, the name of charset is misleading, actually it is defined as the combination of one or more coded character sets and a character-encoding scheme. If you are a serious programmer, you should read the Javadoc of class `Charset`.

Many non-unicode charsets are subsets of unicode, while others use completely different mapping between a character and its code point.

## Charset, CharsetEncoder/CharsetDecoder

Java provides a Charset class representing supported charset, by which we can encode Java string into bytes or decode bytes into Java String.



```java
Charset.defaultCharset(); // underlying OS encoding, you can modified it by System.setProperty("file.encoding", "your encoding here")
```


> The "file.encoding" property is not required by the J2SE platform specification; it's an internal detail of Sun's implementations and should not be examined or modified by user code. It's also intended to be read-only; it's technically impossible to support the setting of this property to arbitrary values on the command line or at any other time during program execution.
Charset can get the CharsetEncoder and CharsetDecoder, by which you can control the entire encoding/decoding process.

Charset.encode(..) and Charset.decode(..) are just the shortcut methods of using CharsetEncoder and CharsetDecoder.

Any place where you need to do byte-character conversion, you should specify the charset explicitly.

Some cases:

* String.getBytes()
* Stream
* file I/O

## BOM (Byte Order Marker)

The code point for BOM is U+FEFF.

| Charset | BOM byte sequence |
| --- | --- |
| UTF-8 ef | bb bf |
| UTF-16BE | fe ff |
| UTF-16LE | ff fe |
| UTF-32BE | 00 00 fe ff |
| UTF-32LE | ff fe 00 00 |

**Some encodings will automatically emit byte order marks on encode and read them on decode.**

let's test which charset insert BOM automatically:


```java
public void testBOM() {
        // Encode this to get bytes
        final String bomChar = "\u0041";
        // Unicode encodings
        String[] unicodeEncodings = {"UTF-16", "UTF-8", "UTF-16BE", "UTF-16LE", "UTF-32BE", "UTF-32LE"};
        // Print the byte order marks
        for (String encName : unicodeEncodings) {
            Charset charset = Charset.forName(encName);
            byte[] byteOrderMark = bomChar.getBytes(charset);
            System.out.format("%10s encoding: ", charset.toString());
            for (byte b : byteOrderMark) {
                System.out.format("%02x ", b);
            }
            System.out.println();
        }
    }
```

Output:
UTF-16 encoding: fe ff 00 41
UTF-8 encoding: 41
UTF-16BE encoding: 00 41
UTF-16LE encoding: 41 00
UTF-32BE encoding: 00 00 00 41
UTF-32LE encoding: 41 00 00 00

You can see, UTF-16 encoding scheme inserts the UTF-16BE BOM automatically, while others don't.

UTF-16 is also called _UTF-16BE with BOM_.

## pitfalls of encoding and decoding - data corruption

When decoding not on one go, the character may become corrupted as two halves.

When encoding incrementally, some problems may happen: the `UTF-16` encoding scheme used adds a byte order mark to encoded data. Every time a string is written, another BOM is added, littering the content with unwanted data. When the data is decoded, extra characters end up in the text. All text would need to be concatenated and encoded in one go.

# environments and boundaries JVM interacts with

If all your code is just running in JVM, you are OK. Once your JVM starts to interact with outer environment through I/O or network, that's the boundary place that character en/decoding will happen.

## OS locale

A **locale** is a set of parameters that defines the user's language, country and any special variant preferences that the user wants to see in their user interface. Usually a locale identifier consists of at least a language identifier and a region identifier.

In Unix, Linux or like, the locale identifier is defined in this format: <tt>[language[_territory][.codeset][@modifier]]</tt>

The locale settings usually include the following display (output) format settings:

*   Number format setting
*   Character classification, case conversion settings
*   Date-time format setting
*   String [collation](http://en.wikipedia.org/wiki/Collation "Collation") setting
*   Currency format setting
*   Paper size setting
*   other minor settings ...
In Windows, ......

//TODO

## File encoding

We say file encoding meas its content's encoding (if it's a text file) or it's regarded as a storage of binary bytes.

Actually, file has no knowledge about its content encoding. Some tools can guess the encoding.

When you store the file in an encoding, and then open to show or interpret it in another encoding, I'm sure you'll be frustrated with the [Mojibake](http://en.wikipedia.org/wiki/Mojibake).

## HTML charset with meta

There are two ways to specify the charset using HTML meta tag:

- <meta charset=“utf-8”>
- <meta http-equiv=“Content-Type” content=“text/html; charset=utf-8”>
These two are equivalent.

## HTTP charset with Content-Type

HTTP header Content-Type can be set as "text/html; charset=UTF-8". This is an indicator for browsers. It tells browsers to render or interpret this response context as UTF-8 HTML text.

## Servlet/JSP

You tell the browser what encoding you are using, and you also need your server to produce the response of that encoding, whatever static html file, dynamic JSP or in-memory response stream.

JSP uses the directive:


```html
<%@ page contentType="text/html;charset=UTF-8" pageEncoding="GBK" %>
```

This means that this file is edited and stored using GBK encoding, but the server resonse as UTF-8 encoding.

So the pageEncoding attribute is for JVM to read the JSP source file content when run-time compiling.

![jsp-directive-encoding](/media/jsp-directive-encoding.png)

For input data, you can use



```java
request.setCharacterEncoding("UTF-8");
```


##  database codepage/collation


# Some useful sites
https://codepoints.net

