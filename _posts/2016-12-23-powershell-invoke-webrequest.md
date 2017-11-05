---
layout: post
title: Powershell Invoke-WeqRequest parameter Body
category: windows
tags: powershell
---

Wanted to use Invoke-WebRequest uploads a binary file by multiple http requests, in each of which one chunk of the original file is pushed to server. First, get the all bytes of the binary file:

```
    $contents = [System.IO.File]::ReadAllBytes("/path/to/binaryFile")
``` 

Then in each round, upload a chunk of the binary file:

```
    Invoke-WebRequest $url -ContentType "application/octet-stream" -Method Post -Body $contents[$begin..$end]
```

However, the server will receive a corruptted upload.

Curious about what type parameter -Body accepts. After examining method WebRequestPSCmdlet.FillRequestStream (file WebRequestPSCmdlet.CoreClr.cs), it's found that powershell will check if body can be casted into FormObject, IDictionary, XmlNode, Stream, byte[] and string in sequence and do the cast if possible. Check the type of input:

```
    $contents[$begin..$end].gettype().FullName
```

Got "System.Object[]", which does not match any types mentioned above. So we have solutions:

* Simple solution is to cast it to byte[].

```
    $body = [byte[]]$contents[$begin..$end]
    Invoke-WebRequest $url -ContentType "application/octet-stream" -Method Post -Body $body
```

* Can also pass string to body. Have to use ISO-8859-1 to encode the bytes here, because **ISO-8859-1 is the only encoding where byte value is equal to code point value**.

```
    $content = [System.IO.File]::ReadAllBytes($binaryFile)
    $encoding = [System.Text.Encoding]::GetEncoding('ISO-8859-1')
    $uploadContent = '{0}' -f $encoding.GetString($content)
    Invoke-WebRequest $url -ContentType "application/octet-stream" -Method Post -Body $uploadContent.SubString($begin, $contentLength)
```

## Extension: Encoding
This link has great details for encoding: [What Every Programmer Absolutely, Positively Needs To Know About Encodings and Character Sets to Work with Text](http://kunststube.net/encoding/).

In summary:
* Unicode is the computing industry __standard__ for consistent encoding, representation and handling of text expressed in most of the world's writing systems. 
* UTF-8 is a character encoding capable of encoding all possible characters or code points, defined by Unicode and originally designed by Ken Thompson and Rob Pike. UTF-8 is variable-length, backward compatibility with ASCII, and uses 8-bit code units.
* UTF-8 and UTF-16 implemented Unicode.
* Characters are referred to by their "Unicode code point", which are written in hexadecimal, preceded by a "U+". The character Ḁ has the Unicode code point U+1E00. In other (decimal) words, it is the 7680th character of the Unicode table.