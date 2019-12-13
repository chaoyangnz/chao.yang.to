---
title: Behavior of polymorphic methods inside constructors
toc: true
id: 38
categories:
  - OOP
date: "2015-01-12T03:31:57+00:00"
---



```java

class Glyph {
    void draw() {
        System.out.println(&quot;Glyph.draw()&quot;);
    }

    Glyph() {
        System.out.println(&quot;Glyph() before draw()&quot;);
        draw();
        System.out.println(&quot;Glyph() after draw()&quot;);
    }
}

class RoundGlyph extends Glyph {
    private int radius = 1;

    RoundGlyph(int r) {
        radius = r;
        System.out.println(&quot;RoundGlyph.RoundGlyph(), radius = &quot; + radius);
    }

    void draw() {
        System.out.println(&quot;RoundGlyph.draw(), radius = &quot; + radius);
    }

    public static void main(String[] args) {
        new RoundGlyph(5);
    }
}
```


Output:
> Glyph() before draw()
> 
> RoundGlyph.draw(), radius = 0
> 
> Glyph() after draw()
> 
> RoundGlyph.RoundGlyph(), radius = 5

在构造器中调用多态方法，虽然对象没有完全构造好，但是多态也会发生！!

