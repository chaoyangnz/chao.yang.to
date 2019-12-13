---
title: HTML 5
toc: true
id: 1105
categories:
  - HTML5
date: "2015-04-28T23:27:29+00:00"
---

### new elements

#### `&lt;figure&gt;` and &lt;`figcaption&gt;`



```java
&lt;figure id="fig1"&gt;
    &lt;figcaption&gt;Fig.1 Screen Reader Support for WAI-ARIA&lt;/figcaption&gt;
    &lt;img src="http://img2.cache.netease.com/cnews/2015/4/28/201504281107026599c.jpg" alt="JAWS: Landmarks 1/1, Forms 4/5 … "&gt;
&lt;/figure&gt;
```


#### `&lt;mark&gt;`

This tag is very useful for the highlight of searching result.


```java
&lt;h1&gt;Yes, You Can Use &lt;mark&gt;HTML5&lt;/mark&gt; Today!&lt;/h1&gt;
```


#### `&lt;progress&gt;` and &lt;`meter&gt;`



```java
&lt;progress max="100" value="0"&gt;
```



```java
&lt;meter value="50" min="0" max="400" low="60" high="300" title="gigabytes"&gt;
&lt;meter value="150" min="0" max="400" low="60" high="300" title="gigabytes"&gt;
&lt;meter value="350" min="0" max="400" low="60" high="300" title="gigabytes"&gt;
```

the low and high are the thresholds. When in the range, the color is green, otherwise the color is yellow.

#### &lt;time&gt;



```java
&lt;time datetime="2015-10-12"&gt;12 October of this year&lt;/time&gt;
```



```java
&lt;!-- month --&gt;
&lt;time&gt;2015-11&lt;/time&gt;

&lt;!-- date --&gt;
&lt;time&gt;2015-11-12&lt;/time&gt;

&lt;!-- yearless date --&gt;
&lt;time&gt;11-12&lt;/time&gt;

&lt;!-- time --&gt;
&lt;time&gt;14:54:39&lt;/time&gt;

&lt;!-- floating date and time --&gt;
&lt;time&gt;2015-11-12T14:54:39&lt;/time&gt;

&lt;!-- time-zone offset --&gt;
&lt;time&gt;-0800&lt;/time&gt;

&lt;!-- global date and time --&gt;
&lt;time&gt;2015-11-12T06:54:39.929-0800&lt;/time&gt;

&lt;!-- week --&gt;
&lt;time&gt;2015-W46&lt;/time&gt;

&lt;!-- duration --&gt;
&lt;time&gt;4h 18m 3s&lt;/time&gt;
```


#### &lt;dl&gt; &lt;dt&gt; &lt;dd&gt;

This is called “description lists” or “association lists.”


```java
&lt;dl&gt;
  &lt;dt&gt;Selector:&lt;/dt&gt;
  &lt;dd&gt;The element(s) targeted.&lt;/dd&gt;
  &lt;dt&gt;Property:&lt;/dd&gt;
  &lt;dd&gt;The feature used to add styling to the targeted element, defined before a colon.&lt;/dd&gt;
  &lt;dt&gt;Value:&lt;/dd&gt;
  &lt;dd&gt;The value given to the specified property, declared after the colon.&lt;/dd&gt;
&lt;/dl&gt;
```


#### &lt;details&gt;

This tag is very useful to implement initially hidden "see more" .


```java
&lt;details&gt;
  &lt;summary&gt;Some Magazines of Note&lt;/summary&gt;
  &lt;ul&gt;
    &lt;li&gt;&lt;cite&gt;Bird Watcher's Digest&lt;/cite&gt;&lt;/li&gt;
    &lt;li&gt;&lt;cite&gt;Rower's Weekly&lt;/cite&gt;&lt;/li&gt;
    &lt;li&gt;&lt;cite&gt;Fishing Monthly&lt;/cite&gt;&lt;/li&gt;
  &lt;/ul&gt;
&lt;/details&gt;
```


#### &lt;ol reversed start="3"&gt;



```java
&lt;ol reversed&gt;
    &lt;li&gt;&lt;cite&gt;Bird Watcher's Digest&lt;/cite&gt;&lt;/li&gt;
    &lt;li&gt;&lt;cite&gt;Rower's Weekly&lt;/cite&gt;&lt;/li&gt;
    &lt;li&gt;&lt;cite&gt;Fishing Monthly&lt;/cite&gt;&lt;/li&gt;
&lt;/ol&gt;

&lt;ol start="4"&gt;
    &lt;li&gt;&lt;cite&gt;Bird Watcher's Digest&lt;/cite&gt;&lt;/li&gt;
    &lt;li&gt;&lt;cite&gt;Rower's Weekly&lt;/cite&gt;&lt;/li&gt;
    &lt;li&gt;&lt;cite&gt;Fishing Monthly&lt;/cite&gt;&lt;/li&gt;
&lt;/ol&gt;

&lt;ol start="4" reversed&gt;
    &lt;li&gt;&lt;cite&gt;Bird Watcher's Digest&lt;/cite&gt;&lt;/li&gt;
    &lt;li&gt;&lt;cite&gt;Rower's Weekly&lt;/cite&gt;&lt;/li&gt;
    &lt;li&gt;&lt;cite&gt;Fishing Monthly&lt;/cite&gt;&lt;/li&gt;
&lt;/ol&gt;
```


#### Scoped Styles

The scoped style is only valid for the specified element and its children.


```java
&lt;h1&gt;Page Title&lt;/h1&gt;
&lt;article&gt;
  &lt;style scoped&gt;
    h1 {
      color: blue;
    }
  &lt;/style&gt;
  &lt;h1&gt;Article Title&lt;/h1&gt;
  &lt;p&gt;Article content.&lt;/p&gt;
&lt;/article&gt;
```


#### the `deley` and `async` attribute of Scripts

Using `defer` specifies that the browser should wait until the page’s markup is parsed before loading the script.

The new `async` attribute allows you to specify that a script should load asynchronously.


```java
&lt;script type="text/javascript" src="xxx.js" async&gt;&lt;/script&gt;
```


#### responsive image



```java
&lt;picture src="" srcset="" sizes="" &gt;

&lt;img src="" srcset="" size="" &gt;
```


#### others

*   &lt;dialog&gt;
*   &lt;a href="1.pdf" dowload&gt;download this document&lt;/a&gt;
*   &lt;iframe sandbox seemless&gt;&lt;/iframe&gt;
*   &lt;menu&gt; &lt;menulist&gt;
*   &lt;address&gt;

### HTML5 form

#### &lt;input required&gt;

#### &lt;input placeholder=""&gt;

#### &lt;input pattern=""&gt;

#### &lt;input disabled&gt;

#### &lt;input readonly&gt;

#### &lt;datalist&gt;



```java
&lt;input type="color" list="colors" id="favcolor" name="favcolor"/&gt;
&lt;datalist id="colors"&gt;
    &lt;option value="#0000FF" label="blue"/&gt;
    &lt;option value="#008000" label="green"/&gt;
    &lt;option value="#ff0000" label="red"/&gt;
    &lt;option value="#663399" label="RebeccaPurple"/&gt;
&lt;/datalist&gt;
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

#### &lt;form `novalidate&gt;`
