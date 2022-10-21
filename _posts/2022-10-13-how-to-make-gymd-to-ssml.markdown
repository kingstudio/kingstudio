---
layout: post
title:  怎样将GYMD转换成SSML
date:   2022-10-13 10:05:55 +0300
image:  /assets/images/blog/post-1.jpg
author: kingstudio
tags:   ssml
---

本文将介绍一种用于语音合成的MarkDown文档（GYMD），同时提供一种C#语言编写的转换器，可将GYMD文件转换为SSML，该方法已用在一款免费的语音合成软件。

**什么是语音合成标记语言（SSML）？**

语音合成标记语言 (SSML) 是一种基于 XML 的标记语言，可让开发人员指定如何使用文本转语音将输入文本转换为合成语音。 与纯文本相比，SSML 可让开发人员微调音节、发音、语速、音量以及文本转语音输出的其他属性。 SSML 可自动处理正常的停顿（例如，在句号后面暂停片刻），或者在以问号结尾的句子中使用正确的音调。SSML 的语音服务实现基于万维网联合会的[语音合成标记语言版本 1.0](https://www.w3.org/TR/2004/REC-speech-synthesis-20040907/)。本文以微软System.Speech.Synthesis类来实现语音合成，SSML语音合成标记语言参考[这里](https://learn.microsoft.com/en-us/previous-versions/office/developer/speech-technologies/hh361578(v=office.14))的链接。

**什么是谷雨语音合成标记(GYMD)**

谷雨语音合成标记语言（GYMD）是内容作者的文本到语音格式。它作为开源发布，以便所有人都能受益。GYMD是面向内容作者、设计者和开发人员的语音合成格式。其有以下优点：

- 比SSML更简单。不知道韵律和音素之间的区别吗？不喜欢SSML的冗长。没问题，语音标记（SSMD）比语音合成标记语言（SSML）更容易。
- 转换为SSML或文本。仅仅因为今天的语音平台使用SSML，并不意味着内容作者必须这样做。写内容是语音标记，让工具和框架将其转换为SSML或纯文本。
- 跨平台支持。不要担心每个平台如何支持（或不支持）SSML规范v1.0和v1.1。使用单独的格式化程序将语音标记转换为特定于平台的SSML风格，如Amazon Alexa和Google Assistant。

**GYMD修饰符**

修饰符是改变特定单词或短语发音方式的语音标记。

修饰符的典型格式是：!(text)[key:“value”；…]

并非所有修饰符都有值。可以对同一文本应用多个修饰符。

**GYMD节**

节标记在节的开始。该节将继续，直到找到下一个节标记或到达内容的结尾。

节的格式为：#[key:“value”, …]

根据键的不同，该值可能是可选的。

注：根据平台支持，可能不支持某些节和文本修饰符的组合。在一起使用这些标签时，请确保进行测试。

谷雨语音Markdown的用法。GYMD支持的元素有：

- audio
- speak
- break
- emphasis
- sub
- repeat
- question
- answer
- voice
- cardinal (number)
- ordinal
- characters
- date
- time
- telephone (phone)
- pitch
- volume
- rate

**audio**

播放预先录制的简短音频。

GYMD语法标准格式：!(text)[audio:"src"]

其他简短用法：![src], !["src"], ![audio:src], ![audio:"src"], !(text)[audio:"src"]

修饰符:![]是必须存在的元素。

参数text：被简短音频替代的文字，为可选参数。

参数src: 设置音频的URL路径。

音频格式：
- 音频文件必须是有效的WAV文件。
- 源URL可以使用https://、http://、file:///的协议。
- 音频文件为远程源时尽量不能超过120秒。
- 比特率可以为352 kbps。
- 采样率可以为22050Hz。

audio对应SSML的语法，请参考 [这里](https://learn.microsoft.com/en-us/previous-versions/office/developer/speech-technologies/hh361684(v=office.14)) 的链接。

GYMD
```
!["https://www.demo.com/intro.wav"]
Welcome back.
```
转换为SSML

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
<voice name="VW Paul" xml:lang="en-US">
<p><audio src="https://www.demo.com/intro.wav" />
Welcome back.</p>
</voice>
</speak>
```

**break**

语音中的停顿。

GYMD语法标准格式：![break:"time"]

其他简短用法：[2000ms], [5s], [break:"200ms"], [break:1s], ![break:"2000ms"]

修饰符:[]是必须存在的元素。

参数time: 设置中断长度（秒或毫秒）

- 250ms
- 0.5s
- 3s
- 10s
- 10000ms

break对应SSML的语法，请参考 [这里](https://learn.microsoft.com/en-us/previous-versions/office/developer/speech-technologies/hh361590(v=office.14)) 的链接。

GYMD
```
Step 1, take a deep breath. [200ms]
Step 2, exhale.
Step 3, take a deep breath again. [break:"2000ms"]
Step 4, exhale.
```
转换为SSML

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
<voice name="VW Paul" xml:lang="en-US">
<p>Step 1, take a deep breath. <break time="200ms" /></p>
<p>Step 2, exhale.</p>
<p>Step 3, take a deep breath again. <break time="2000ms" /></p>
<p>Step 4, exhale.</p>
</voice>
</speak>
```

**emphasis**

在单词或短语中添加或删除强调。

GYMD语法标准格式：!(word)[emphasis:"level"]

其他简短用法：(word)[emphasis] , (word)[emphasis:"strong"] , (word)[emphasis:"none"] , (word)[emphasis:"none"]

修饰符:()[emphasis]是必须存在的元素。

参数word: 要强调的单词或短语

参数level: 设置强调的程度

- strong
- moderate (default)
- none
- reduced

emphasis对应SSML的语法，请参考 [这里](https://learn.microsoft.com/en-us/previous-versions/office/developer/speech-technologies/hh361617(v=office.14)) 的链接。

GYMD
```
A (strong)[emphasis:"strong"] level

A (moderate)[emphasis] level

A (moderate)[emphasis:"moderate"] level

A (none)[emphasis:"none"] level

A !(reduced)[emphasis:"reduced"] level
```
转换为SSML

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
<voice name="VW Paul" xml:lang="en-US">
<p>A <emphasis level="strong">strong</emphasis> level</p>
<p>A <emphasis level="moderate">moderate</emphasis> level</p>
<p>A <emphasis level="moderate">moderate</emphasis> level</p>
<p>A <emphasis level="none">none</emphasis> level</p>
<p>A <emphasis level="reduced">reduced</emphasis> level</p>
</voice>
</speak>
```

**sub**

用不同的单词或短语替换一个单词或短语。常用于扩展/澄清缩写。

GYMD语法标准格式：!(word)[sub:"alias"]

其他简短用法：(word)["alias word"], (word)[sub:"alias word"], !(word)[sub:"alias word"]

修饰符:()[""]是必须存在的元素。

参数word: 要替换的单词或短语

参数alias: 用于替换原文的单词或短语

sub对应SSML的语法，请参考 [这里](https://learn.microsoft.com/en-us/previous-versions/office/developer/speech-technologies/hh361630(v=office.14)) 的链接。

GYMD
```
My favorite chemical element is (Al)[sub:"aluminum"],

but !(Al)[sub:"aluminum"] prefers (Mg)["magnesium"].
```
转换为SSML

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
<voice name="VW Paul" xml:lang="en-US">
<p>My favorite chemical element is <sub alias="aluminum">Al</sub>,</p>
<p>but <sub alias="aluminum">Al</sub> prefers <sub alias="magnesium">Mg</sub>.</p>
</voice>
</speak>
```

**repeat**

将单词或短语重说一遍或几遍。

GYMD语法标准格式：!(word)[repeat:number;break:"time"]

其他简短用法：(word)[number], (word)[repeat:number], !(word)[repeat:number;break:"time"]

修饰符:()[]是必须存在的元素。

参数number：必须的参数。重说的次数，为大于零的整数。

参数break：重说之间停顿的时间（秒或毫秒）。若未填写参数则无停顿。

- 250ms
- 0.5s
- 3s
- 10s
- 10000ms

repeat对应SSML的s元素和break元素的组合语法，s元素请参考 [这里](https://learn.microsoft.com/en-us/previous-versions/office/developer/speech-technologies/hh361585(v=office.14)) 的链接。

GYMD
```
You will hear the word 3 times: (congratulations)[3].

You will hear the word 3 times: (congratulations)[repeat:3].

You will hear the word three times every three seconds: (congratulations)[repeat:3;break:3s].
```
转换为SSML

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
<voice name="VW Paul" xml:lang="en-US">
<p>You will hear the word 3 times: <s>congratulations</s><break time="1s" /><s>congratulations</s><break time="1s" /><s>congratulations</s><break time="1s" />.</p>
<p>You will hear the word 3 times: <s>congratulations</s><break time="1s" /><s>congratulations</s><break time="1s" /><s>congratulations</s><break time="1s" />.</p>
<p>You will hear the word three times every three seconds: <s>congratulations</s><break time="3s" /><s>congratulations</s><break time="3s" /><s>congratulations</s><break time="3s" />.</p>
</voice>
</speak>
```

**comment**

单行或多行注释。

GYMD语法标准格式：//some text 或 /* some text */

其他简短用法：无

修饰符:// 或 /* */ 是必须存在的元素。

GYMD
```
Notice,  //that this is a one-line comment

/*Notice, that this is a multi-line comment*/ 
```
转换为SSML

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
<voice name="VW Paul" xml:lang="en-US">
<p>Notice,  <!-- that this is a one-line comment --></p>
<p>
<![CDATA[
Notice, that this is a multi-line comment
]]>
 </p>
</voice>
</speak>
```

**question**

标节这节内容为试题标题，不予语音合成。

GYMD语法标准格式：#[question:""] 

其他简短用法：#[question] 

修饰符:#[] 是必须存在的元素，注意在]后面有一个空格。

GYMD
```
Oh, I'm so sorry I forgot to bring along the book you borrowed from the library.

What a terrible memory you have! Anyway, I won't need it until Friday night. As long as I can get it by then, OK?

Question, What do we learn from this conversation?

#[question] 
A) The man failed to keep his promise.
B) The woman has a poor memory.
C) The man borrowed the book from the library.
D) The woman does not need the book any more.

```
转换为SSML

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
<voice name="VW Paul" xml:lang="en-US">
<p>Oh, I&#39;m so sorry I forgot to bring along the book you borrowed from the library.</p><break time="1s" />
<p>What a terrible memory you have! Anyway, I won&#39;t need it until Friday night. As long as I can get it by then, OK?</p><break time="1s" />
<p>Question, What do we learn from this conversation?</p><break time="1s" />

<![CDATA[
**The following areas are the test questions**
 
A) The man failed to keep his promise.
B) The woman has a poor memory.
C) The man borrowed the book from the library.
D) The woman does not need the book any more.
]]>

</voice>
</speak>
```

**answer**

标节这节内容为试题答案，不予语音合成。

GYMD语法标准格式：#[answer:""] 

其他简短用法：#[answer]

修饰符:#[] 是必须存在的元素，注意在]后面有一个空格。

GYMD
```
Oh, I'm so sorry I forgot to bring along the book you borrowed from the library.

What a terrible memory you have! Anyway, I won't need it until Friday night. As long as I can get it by then, OK?

Question, What do we learn from this conversation?

#[question] 
A) The man failed to keep his promise.
B) The woman has a poor memory.
C) The man borrowed the book from the library.
D) The woman does not need the book any more.

#[answer] 
A

```
转换为SSML

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
<voice name="VW Paul" xml:lang="en-US">
<p>Oh, I&#39;m so sorry I forgot to bring along the book you borrowed from the library.</p><break time="1s" />
<p>What a terrible memory you have! Anyway, I won&#39;t need it until Friday night. As long as I can get it by then, OK?</p><break time="1s" />
<p>Question, What do we learn from this conversation?</p><break time="1s" />

<![CDATA[
**The following areas are the test questions**
 
A) The man failed to keep his promise.
B) The woman has a poor memory.
C) The man borrowed the book from the library.
D) The woman does not need the book any more.
]]>

<![CDATA[
**The following areas are the test answer**
 
A
]]>

</voice>
</speak>
```

**voice**

voice的第一种用法，用于节的开头。

通过声音元素的参数，可以更改在语音合成中使用的声音，还可以指定声音的属性，如语言。

GYMD语法标准格式：#[voice:"name",lang:"language"] 

其他简短用法：无

修饰符:#[] 是必须存在的元素，注意在]后面有一个空格。

参数name：必须的参数。指定将说出所包含文本的已安装语音的名称。
- IVONA Brian
- IVONA Amy
- VW Kate
- VW Paul
- VW Liang
- VW Lily

参数language：必须的参数。指定语音必须支持的语言。该值可以包含小写、两个字母的语言代码（例如英语中的en），也可以选择除语言代码外还包含大写、国家/地区或其他变体（例如zh-CN）。
- en-US
- en-GB
- cn-ZH

GYMD
```
#[voice:"IVONA Brian",lang:"en-GB"] 
Oh, I'm so sorry I forgot to bring along the book you borrowed from the library.

#[voice:"IVONA Amy",lang:"en-GB"] 
What a terrible memory you have! Anyway, I won't need it until Friday night. As long as I can get it by then, OK?

#[voice:"VW Kate",lang:"en-US"] 
Question, What do we learn from this conversation?
```
转换为SSML

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
<voice name="VW Paul" xml:lang="en-US">
<voice name="IVONA Brian" xml:lang="en-GB">
Oh, I&#39;m so sorry I forgot to bring along the book you borrowed from the library.
</voice>
<voice name="IVONA Amy" xml:lang="en-GB">
What a terrible memory you have! Anyway, I won&#39;t need it until Friday night. As long as I can get it by then, OK?
</voice>
<voice name="VW Kate" xml:lang="en-US">
Question, What do we learn from this conversation?
</voice>
</voice>
</speak>

```

voice的第二种用法，用于段落内。

GYMD语法标准格式：!(word)[voice:name,lang:language]

其他简短用法：无

修饰符:()[] 是必须存在的元素。

GYMD
```
Oh, I'm so sorry I forgot to bring along the book you borrowed from the library.

What a terrible memory you have!  (Anyway, I won't need it until Friday night. As long as I can get it by then, OK?
)[voice:"VW Kate",lang:"en-US"] 
Question, What do we learn from this conversation?
```
转换为SSML

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
<voice name="VW Paul" xml:lang="en-US">
<p>Oh, I&#39;m so sorry I forgot to bring along the book you borrowed from the library.</p><break time="1s" />
<p>What a terrible memory you have!  <voice name="VW Kate" xml:lang="en-US">Anyway, I won&#39;t need it until Friday night. As long as I can get it by then, OK?
</voice> 
Question, What do we learn from this conversation?</p><break time="1s" />
</voice>
</speak>
```


**cardinal**

将数字按基数拼读：一、二十、二千三百四十五等等。
与数字相同。

GYMD语法标准格式：!(number)[cardinal|number]

其他简短用法：(number)[cardinal], !(number)[cardinal], (number)[number], !(number)[number]

修饰符:()[] 是必须存在的元素。

GYMD
```
One, two, (3)[cardinal].
Your balance is: (12345)[cardinal].
(801)[cardinal] is the same as (801)[number]
```
转换为SSML

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
<voice name="VW Paul" xml:lang="en-US">
<p>One, two, <say-as interpret-as="cardinal" >3</say-as>.</p><break time="1s" />
<p>Your balance is: <say-as interpret-as="cardinal" >12345</say-as>.</p><break time="1s" />
</voice>
</speak>
```

**ordinal**

将数字按序数拼读：第一、第二、第三等等。

GYMD语法标准格式：!(number)[ordinal]

其他简短用法：(number)[ordinal], !(number)[ordinal]

修饰符:()[] 是必须存在的元素。

GYMD
```
The others came in 2nd and (3)[ordinal].

Your rank is (123)[ordinal].
```
转换为SSML

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
<voice name="VW Paul" xml:lang="en-US">
<p>The others came in 2nd and <say-as interpret-as="ordinal" >3</say-as>.</p><break time="1s" />
<p>Your rank is <say-as interpret-as="ordinal" >123</say-as>.</p><break time="1s" />
</voice>
</speak>
```

**characters**

将数字或文字按单字符拼读。

GYMD语法标准格式：!(word)[characters]

其他简短用法：(word)[characters], !(word)[characters]

修饰符:()[] 是必须存在的元素。

GYMD
```
Countdown: (321)[characters]

The word is spelled: (park)[characters]
```
转换为SSML

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
<voice name="VW Paul" xml:lang="en-US">
<p>Countdown: <say-as interpret-as="characters" >321</say-as></p><break time="1s" />
<p>The word is spelled: <say-as interpret-as="characters" >park</say-as></p><break time="1s" />
</voice>
</speak>
```

**date**

将文字按单日期拼读。

GYMD语法标准格式：!(word)[date:"format"]

其他简短用法：(word)[date:"format"], !(word)[date:"format"]

修饰符:()[] 是必须存在的元素。

参数word：word必须是日期格式的文字，例如：
- 用/分隔的日期：10/11/12
- 用-分隔的日期：10-11-12

参数format：必须的参数。日期的格式。
- mdy
- dmy
- ymd
- md
- dm
- ym
- my
- y
- m
- d

GYMD
```
The date is (10-11-2012)[date:"mdy"].     // October 11th, 2012
The date is (10-11-2012)[date:"dmy"].     // November 10th, 2012
The date is (2010-11-12)[date:"ymd"].     // Nov 12th, 2010
The date is (10-11)[date:"md"].         // October 11th
The date is (10-11)[date:"dm"].         // November 10th
The date is (10-11)[date:"ym"].         // November 2010
The date is (10-11)[date:"my"].         // October 2011
The date is (10)[date:"y"].             // 2010
The date is (10)[date:"m"].             // October
The date is (10)[date:"d"].             // 10th
```
转换为SSML

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
<voice name="VW Paul" xml:lang="en-US">
<p>
The date is <say-as interpret-as="date" format="mdy" > 10-11-2012 </say-as>.     <!--  October 11th, 2012 -->
The date is <say-as interpret-as="date" format="dmy" > 10-11-2012 </say-as>.     <!--  November 10th, 2012 -->
The date is <say-as interpret-as="date" format="ymd" > 2010-11-12 </say-as>.     <!--  Nov 12th, 2010 -->
The date is <say-as interpret-as="date" format="md" > 10-11 </say-as>.         <!--  October 11th -->
The date is <say-as interpret-as="date" format="dm" > 10-11 </say-as>.         <!--  November 10th -->
The date is <say-as interpret-as="date" format="ym" > 10-11 </say-as>.         <!--  November 2010 -->
The date is <say-as interpret-as="date" format="my" > 10-11 </say-as>.         <!--  October 2011 -->
The date is <say-as interpret-as="date" format="y" > 10 </say-as>.             <!--  2010 -->
The date is <say-as interpret-as="date" format="m" > 10 </say-as>.             <!--  October -->
The date is <say-as interpret-as="date" format="d" > 10 </say-as>.             <!--  10th -->
</p>
</voice>
</speak>

```

**telephone**

将数字按单电话号码拼读。

GYMD语法标准格式：!(word)[telephone|phone:"format"]

其他简短用法：(word)[telephone:"format"], !(word)[telephone:"format"], (word)[phone:"format"], !(word)[phone:"format"]

修饰符:()[] 是必须存在的元素。

参数word：word必须是电话号码的格式，包括数字、（）、+和-，例如：
- 0838-5180000
- (888) 555-1212

参数format：可选参数。为国际电话区号。
- 86 代表中国
- 1 代表美国
- 39 代表意大利

GYMD
```
My number is (867-5309)[phone].
My number is (158 8888 8888)[phone:"86"]. 
The number is ((888) 555-1212)[phone].
```
转换为SSML

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
<voice name="VW Paul" xml:lang="en-US">
<p>My number is <say-as interpret-as="telephone" > 867-5309 </say-as>.
My number is <say-as interpret-as="telephone" format="86" > 158 8888 8888 </say-as>. 
The number is <say-as interpret-as="telephone" > (888) 555-1212 </say-as>.</p><break time="1s" />
</voice>
</speak>
```


**time**

将数字按单电话号码拼读。

GYMD语法标准格式：!(word)[time:"format"]

其他简短用法：(word)[time:"format"], !(word)[time:"format"]

修饰符:()[] 是必须存在的元素。

参数word：word必须是时间的格式，包括数字、:、pm和am，例如：
- 2:30pm

参数format：可选参数。为国际电话区号。
- hms12
- hms24

GYMD
```
The time is (2:30pm)[time:"hms12"].
The time is (2:30pm)[time:"hms24"].
```
转换为SSML

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
<voice name="VW Paul" xml:lang="en-US">
<p>My number is <say-as interpret-as="telephone" > 867-5309 </say-as>.
My number is <say-as interpret-as="telephone" format="86" > 158 8888 8888 </say-as>. 
The number is <say-as interpret-as="telephone" > (888) 555-1212 </say-as>.</p><break time="1s" />
</voice>
</speak>
```


**pitch**

升高或降低语调。

GYMD语法标准格式：!(word)[pitch:"format"]

其他简短用法：(word)[pitch:"format"], !(word)[pitch:"format"]

修饰符:()[] 是必须存在的元素。

参数word：为被升高或降低语调的单词或句子。

参数format：必选参数。
- x-low
- low
- medium
- high
- x-high

GYMD
```
I can speak with my normal pitch, (but also with a much higher pitch)[pitch:"x-high"].
```
转换为SSML

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
<voice name="VW Paul" xml:lang="en-US" >
<p>I can speak with my normal pitch, <prosody pitch="x-high" >but also with a much higher pitch</prosody>.</p>
</voice>
</speak>
```


**rate**

调节语速。

GYMD语法标准格式：!(word)[rate:"format"]

其他简短用法：(word)[rate:"format"], !(word)[rate:"format"]

修饰符:()[] 是必须存在的元素。

参数word：为被调节语速的单词或句子。

参数format：必选参数。
- x-slow
- slow
- medium
- fast
- x-fast

GYMD
```
When I wake up, (I speak quite slowly)[rate:"x-slow"].
```
转换为SSML

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
<voice name="VW Paul" xml:lang="en-US" >
<p>When I wake up, <prosody rate="x-slow" >I speak quite slowly</prosody>.</p>
</voice>
</speak>
```


**volume**

调节音量。

GYMD语法标准格式：!(word)[volume|vol:"format"]

其他简短用法：(word)[volume|vol:"format"], !(word)[volume|vol:"format"]

修饰符:()[] 是必须存在的元素。

参数word：为被调节语速的单词或句子。

参数format：必选参数。
- x-soft
- soft
- medium
- loud
- x-loud

GYMD
```
When I wake up, (I speak quite slowly)[vol:"x-slow"].
```
转换为SSML

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
<voice name="VW Paul" xml:lang="en-US" >
<p>When I wake up, <prosody volume="x-slow" >I speak quite slowly</prosody>.</p>
</voice>
</speak>
```


**phoneme**

音标，指定所包含文本的拼音发音。

GYMD语法标准格式：!(word)[phoneme:"format"]

其他简短用法：(word)[phoneme:"format"], !(word)[phoneme:"format"]

修饰符:()[] 是必须存在的元素。

参数word：为被调整发音的单词。

参数format：必选参数。

GYMD
```
His name is Mike (Zhou)[phoneme:"JH AU"].
```
转换为SSML

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-GB">
<voice name="IVONA Brian" xml:lang="en-GB" >
<p> His name is Mike <phoneme ph="JH AU">Zhou</phoneme>.</p>
</voice>
</speak>
```
