---
title: HTML 5
toc: true
id: 1105
categories:
  - HTML5
date: "2015-04-28T23:27:29+00:00"
---

### new elements

#### `<figure>` and <`figcaption>`



```java
<figure id="fig1">
    <figcaption>Fig.1 Screen Reader Support for WAI-ARIA</figcaption>
    <img src="http://img2.cache.netease.com/cnews/2015/4/28/201504281107026599c.jpg" alt="JAWS: Landmarks 1/1, Forms 4/5 … ">
</figure>
```


#### `<mark>`

This tag is very useful for the highlight of searching result.


```java
<h1>Yes, You Can Use <mark>HTML5</mark> Today!</h1>
```


#### `<progress>` and <`meter>`



```java
<progress max="100" value="0">
```



```java
<meter value="50" min="0" max="400" low="60" high="300" title="gigabytes">
<meter value="150" min="0" max="400" low="60" high="300" title="gigabytes">
<meter value="350" min="0" max="400" low="60" high="300" title="gigabytes">
```

the low and high are the thresholds. When in the range, the color is green, otherwise the color is yellow.

#### <time>



```java
<time datetime="2015-10-12">12 October of this year</time>
```



```java
<!-- month -->
<time>2015-11</time>

<!-- date -->
<time>2015-11-12</time>

<!-- yearless date -->
<time>11-12</time>

<!-- time -->
<time>14:54:39</time>

<!-- floating date and time -->
<time>2015-11-12T14:54:39</time>

<!-- time-zone offset -->
<time>-0800</time>

<!-- global date and time -->
<time>2015-11-12T06:54:39.929-0800</time>

<!-- week -->
<time>2015-W46</time>

<!-- duration -->
<time>4h 18m 3s</time>
```


#### <dl> <dt> <dd>

This is called “description lists” or “association lists.”


```java
<dl>
  <dt>Selector:</dt>
  <dd>The element(s) targeted.</dd>
  <dt>Property:</dd>
  <dd>The feature used to add styling to the targeted element, defined before a colon.</dd>
  <dt>Value:</dd>
  <dd>The value given to the specified property, declared after the colon.</dd>
</dl>
```


#### <details>

This tag is very useful to implement initially hidden "see more" .


```java
<details>
  <summary>Some Magazines of Note</summary>
  <ul>
    <li><cite>Bird Watcher's Digest</cite></li>
    <li><cite>Rower's Weekly</cite></li>
    <li><cite>Fishing Monthly</cite></li>
  </ul>
</details>
```


#### <ol reversed start="3">



```java
<ol reversed>
    <li><cite>Bird Watcher's Digest</cite></li>
    <li><cite>Rower's Weekly</cite></li>
    <li><cite>Fishing Monthly</cite></li>
</ol>

<ol start="4">
    <li><cite>Bird Watcher's Digest</cite></li>
    <li><cite>Rower's Weekly</cite></li>
    <li><cite>Fishing Monthly</cite></li>
</ol>

<ol start="4" reversed>
    <li><cite>Bird Watcher's Digest</cite></li>
    <li><cite>Rower's Weekly</cite></li>
    <li><cite>Fishing Monthly</cite></li>
</ol>
```


#### Scoped Styles

The scoped style is only valid for the specified element and its children.


```java
<h1>Page Title</h1>
<article>
  <style scoped>
    h1 {
      color: blue;
    }
  </style>
  <h1>Article Title</h1>
  <p>Article content.</p>
</article>
```


#### the `deley` and `async` attribute of Scripts

Using `defer` specifies that the browser should wait until the page’s markup is parsed before loading the script.

The new `async` attribute allows you to specify that a script should load asynchronously.


```java
<script type="text/javascript" src="xxx.js" async></script>
```


#### responsive image



```java
<picture src="" srcset="" sizes="" >

<img src="" srcset="" size="" >
```


#### others

*   <dialog>
*   <a href="1.pdf" dowload>download this document</a>
*   <iframe sandbox seemless></iframe>
*   <menu> <menulist>
*   <address>

### HTML5 form

#### <input required>

#### <input placeholder="">

#### <input pattern="">

#### <input disabled>

#### <input readonly>

#### <datalist>



```java
<input type="color" list="colors" id="favcolor" name="favcolor"/>
<datalist id="colors">
    <option value="#0000FF" label="blue"/>
    <option value="#008000" label="green"/>
    <option value="#ff0000" label="red"/>
    <option value="#663399" label="RebeccaPurple"/>
</datalist>
```

&nbsp;

#### `autofocus` Attribute

#### new input type

*   `search`
*   `email`
*   `url`
*   `tel`
*   `date`
*   `time`
*   `number`
*   `range`
*   `color`

#### <form `novalidate>`
