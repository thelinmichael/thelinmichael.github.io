---
layout: post
title: 'Web Security: Cross-site scripting attacks using UTF-7'
date: 2014-06-08 18:42:34.000000000 +02:00
type: post
published: true
status: publish
categories:
- security
tags: []
---
This post describes a type of cross-site scripting attack that works to varying degree in older versions of Internet Explorer  (tested with IE9), and very old versions of Chrome, Safari and Firefox.

### First, a shallow dive into character encoding

How does the browser you're using to read this know that this _a_ is actually an _a_? Because the browser knows which character encoding to use to translate the zeroes and ones that make up the file into characters. It knows the encoding because Wordpress was kind enough to declare it in the Content-Type header.

In the [most common encoding](http://unicode.org/faq/utf_bom.html "http://unicode.org/faq/utf_bom.html") used in web applications, UTF-8, an _a_ has a binary representation of 01100001. UTF-8 needs eight, 16 or up to 32 bits to represent a character. So in the case of _a_, it only needs eight bits, or a byte. The Braille character ⠟ is a bit more cumbersome, represented by 11100010 10100000 10011111 (3 bytes).

Not all texts uses UTF-8 however. ASCII was the most commonly used encoding for web pages [until 2007](http://4.bp.blogspot.com/-O4jXmTm7WWI/Tyw1As8jt7I/AAAAAAAAI9E/nxxi1T21IH4/s1600/unicode.png "http://4.bp.blogspot.com/-O4jXmTm7WWI/Tyw1As8jt7I/AAAAAAAAI9E/nxxi1T21IH4/s1600/unicode.png"), when it was surpassed by UTF-8\. ([UTF-8 is undoubtedly increasing its lead.](http://w3techs.com/technologies/history_overview/character_encoding/ms/y "http://w3techs.com/technologies/history_overview/character_encoding/ms/y")) ASCII uses only seven bits to represent a character, which allows for 128 different combinations. More importantly, ASCII is a subset of UTF-8, so the ASCII representation of _a_ is exactly the same as in UTF-8 with the only difference that UTF-8 prepends the seven bits with a 0.

UTF-7, a character encoding that similarly to ASCII also using seven bits, was originally designed to be used in email. 7 bits isn't a lot, and in order to use more characters than 128, a technique called shifted encoding is used. This means that a few characters which are representable using 7 bits have special meaning. [This tutorial on character code issues](https://www.cs.tut.fi/~jkorpela/chars.html#utf7ex "https://www.cs.tut.fi/~jkorpela/chars.html#utf7ex") has a good example: The Finnish word _Tämä_ isn't directly representable using UTF-7 since there's no code point (sequence of bits) corresponding to _ä_. To solve this, _ä_ is written as ```+AOQ-```, turning _Tämä_ into ```T+AOQ-m+AOQ-```. As you can see,the _+_ character enables shifted encoding and _-_ disables it.

Shifted encoding is what enables UTF-7 to be used as a way to make cross site scripting attacks.

### What can a UTF-7 cross-site scripting payload look like and why is it effective?

Using shifted encoding, ```<``` and ```>``` can also be represented as ```+ADw-``` and ```+AD4-```. The payload ```<script>alert()</script>``` could thus also look like ```+ADw-script+AD4- alert()+ADw-/script+AD4-```.

It's a common security practice to sanitise user input before reflecting it to back to the user, or potentially other users. The purpose of the sanitisation is often to replace potentially harmful characters, such as < and >, with [escaped equivalents](http://dev.w3.org/html5/html-author/charref "http://dev.w3.org/html5/html-author/charref"), in this case _&lt;_ and _&gt;_.

Importantly for our example, + and - characters are not considered to pose any security concerns when sanitising user input, so they won't be replaced. An example of such a sanitiser is PHP's [htmlentities](http://php.net/manual/en/function.htmlentities.php "http://php.net/manual/en/function.htmlentities.php") method, which uses UTF-8 as the default encoding, and would thus pass the aforementioned UTF-7 string with shifted encoding through unchanged. (If it had encoded the text as UTF-7, it would have read and escaped the payload correctly. The htmlentities method does not support UTF-7 however.)

Whenever a page is loaded with the input ```+ADw-script+AD4- alert()+ADw-/script+AD4-``` from the user, it will interpret it as ```<script>alert()</script>```, given that the browser uses UTF-7 encoding.

### How do you make the browser use UTF-7 encoding instead of UTF-8?

This exploit only works if the browser can be tricked into encoding the page as UTF-7 instead of UTF-8\. An obvious prerequisite is that the browser actually supports UTF-7, which most modern browser won't in order to be [compatible with the HTML5 specification](http://www.w3.org/html/wg/drafts/html/master/document-metadata.html#charset "http://www.w3.org/html/wg/drafts/html/master/document-metadata.html#charset").

Firefox hasn't supported UTF-7 since version 5, from back in 2011\. Chrome stopped supporting it in 2009 (if you're overlooking [a bug](https://bugs.webkit.org/show_bug.cgi?id=74610 "https://bugs.webkit.org/show_bug.cgi?id=74610") - otherwise it's 2013). Interestingly, [only one modern browser supports UTF-7](http://l0.cm/encodings/table/ "http://l0.cm/encodings/table/") - Internet Explorer 11\. (According to the linked support matrix, I've only tested IE9 myself.)

There are a number of ways that can be used to tell the browser that it should use UTF-7 to encode a page. If there are conflicting declarations, some have higher priority than others. The list below is in priority order.

**1\. Byte Object Marker**

A Byte Object Mark (BOM) is sometimes present in the beginning of the document, and is used to determine the encoding and it's endianness. [According to the HTML5 specification](http://www.w3.org/TR/html5/syntax.html#determining-the-character-encoding "http://www.w3.org/TR/html5/syntax.html#determining-the-character-encoding"), the fact that the BOM is preferred over other declarations is a so called willful violation of the HTTP 1.1 specification.

So if there's a Byte Order Mark in the document that is used for cross-site scripting, it won't work simply because the browser won't be tricked into encoding it as UTF-7.

**2\. Content-Type header**

Declaring the encoding in the [charset parameter ](http://tools.ietf.org/html/rfc1521#section-7.1.1 "http://tools.ietf.org/html/rfc1521#section-7.1.1")of the Content-Type header.

```Content-Type: text/html; charset=utf-8```

As with the BOM, the Content-Type header isn't something that can be changed using cross-site scripting.

**3\. Meta tag**

The HTML5 specification also says that [the element declaring the type of character encoding must be found in the first 1024 bytes](http://www.w3.org/html/wg/drafts/html/master/document-metadata.html#charset "http://www.w3.org/html/wg/drafts/html/master/document-metadata.html#charset") of the document ([Mozilla says first 512](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/meta#attr-charset "https://developer.mozilla.org/en-US/docs/Web/HTML/Element/meta#attr-charset")). This is done through the meta tag in the header, preferably before the title element.

```
  <meta charset="utf-7">
```

If the document reflects user input in a way that allows the attacker to change the charset attribute of the metatag, a cross-site scripting attack is possible. As mentioned, UTF-7 is not supported by modern browsers. I tested this in Internet Explorer 9, which encodes the page as UTF-7.

**4\. Browser's detection algorithm**

In case the encoding hasn't been declared either through the meta-tag or the header, the browser will make an attempt to detect the document's encoding. Except writing [an informative blog post](http://nedbatchelder.com/blog/200704/xss_with_utf7.html "http://nedbatchelder.com/blog/200704/xss_with_utf7.html") about UTF-7 cross site scripting, Ned Batcheider is nice enough to provide [a page for those too lazy](http://nedbatchelder.com/utf7.html "http://nedbatchelder.com/utf7.html") to write their own HTML document that uses browser's detection algorithm to be interpreted. I tested this page in IE9, and it did **not** detect the page as UTF-7. The key here is that the document contains a shifted encoded string in order for the User Agent to determine its encoding.

If the encoding hasn't been set anywhere and user input is reflected, Internet Explorer 8 may be open for this type of attack, without manipulating the meta tag.