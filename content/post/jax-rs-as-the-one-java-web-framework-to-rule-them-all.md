---
title: JAX-RS as the one Java web framework to rule them all?
toc: true
tags:
  - JAX-RS
  - Jersey
  - Quoted
id: 5
categories:
  - JAX-RS
date: "2015-01-09T06:40:09+00:00"
---

One of the things about Java that sometimes gets made fun of is the huge number of Java based web frameworks. There certainly are many, of all shapes and sizes! I suppose there are quite a lot of different shapes and sizes of web applications out there but it sometimes seems like there's a 1-1 mapping between applications and frameworks :).

Picking the right web framework is probably a managers nightmare (Which one to pick? Pick the wrong one and we might end up using a duff dead framework that few developers know etc?). But it has lead to a ton of innovation in the web framework space. On balance I think competition and innovation are good things.

I'm a big Rails fan, I think its a stunning piece of work and one of the most impressive open source frameworks created in the last 10 years which together with Ruby, Erb and Rake is a good alternative to Servlets + JSP + JSTL + Spring + Hibernate + WebFrameworkOfYourChoice + SiteMesh/Tiles + Ant/Maven + a few other bits all in a surpringly small and easy to grok codebase.

JAX-RS came along initially as a way of writing RESTful services on the Java platform; using annotations and loose coupling to bind resource beans and their public methods to URIs, HTTP methods and MIME content type negotiation. I've said before I think its [awesome](http://macstrac.blogspot.com/2009/01/jax-rs-as-basis-of-dry-web-framework.html), one of the most impressive JSRs we've had.

From the perspective of the controller layer I actually prefer JAX-RS to Rails routes.rb &amp; controllers as

*   the URI bindings are local to the resource beans which can be arbitrarily nested which makes refactoring much easier and avoids the complex routes.rb file with regex switches (I try and avoid regex whenever I can :)
*   the loose coupling between the objects returned by the resource methods and the actual entity providers is clean; helping the application programmer focus on returning DTOs and letting the framework deal with the XML / JSON /Atom / multi-part-form marshalling &amp; data binding stuff really helps. e.g. its easy to drop in support for new representations in a DRY way without changing the code of your resource beans (controllers), you can just modify an annotation
*   static typing can be a handy thing when binding URIs and parameters to your controller. e.g. having String, integer, Date fields and parameters helps you having to explicitly convert things in your controller
There's lots of other stuff thats great in Rails though :-) but this post isn't a JAX-RS versus Rails post really - its focussing on the world of Java web frameworks for Java developers and the impact of JAX-RS.

So whats interesting is - if you want to build a web application with RESTful services (e.g. a human facing website with computer facing XML/JSON APIs) why would you use JAX-RS andanother web framework? Why not just use JAX-RS as the web framework? So recently I've been musing, could JAX-RS be the one Java web framework to rule them all?

JAX-RS works well with dependency injection frameworks such as [Spring](http://www.springframework.org/), [Guice](http://code.google.com/p/google-guice/),[GuiceyFruit](http://code.google.com/p/guiceyfruit/) or [JBossMC](http://www.jboss.org/jbossmc/) - you can basically pick whichever one you prefer. It also does all the heavily lifting of binding URIs and HTTP methods to resource beans and their methods along with supporting content type negotiation, headers and etags elegantly. For implementing great RESTful services in Java I've never seen anything close to touching it. The main question is what features are missing from JAX-RS being the main web framework?

Incidentally in this post I'm ignoring the server-side UI type frameworks like Wicket, Tapestry, JSF et al. I'm focussing on web frameworks that spend most of their time rendering HTML / XML / JSON and not building complex server side UI stuff and treating the browser as a kinda dumb terminal with the real UI work being done on the server side. Having had a horrid time using Tapestry and Hibernate together on some projects in the past, I'm kinda over the whole concept of server side UI web frameworks personally (I'm putting my flame-proof suite on now). I kinda think if you want to do complex rich web UIs, use wizards or complex flows, just use GWT or JavaScript on the client (or Flex/Flash for video or crazy highly graphical widgets) and keep the server side fairly simple and very RESTful. (But lets leave that discussion for another day... :)

In this post I'm only really considering frameworks like Struts, Stripes, SpringMVC etc.Thankfully WebWork and Struts merged together so at least there's been someconsolidation in the space - and certainly Struts and Stripes are now kinda similar (and not too disimilar from SpringMVC). I wonder if JAX-RS will lead to further consolidation with web frameworks adding themselves as JAX-RS extensions?

So whats is missing from JAX-RS to be able to use just it for your entire web app and set of RESTful services and not have to use it with Struts/Stripes/SpringMVC and have to map some URIs into the web framework and some to JAX-RS?

Right now today there are definitely some holes; though currently I don't think there's thatmuch missing. Here's my list of things I think are missing and how we could add them - I'm very interested in hearing if there's anything you'd particularly miss from Struts/Stripes/SpringMVC/whatever; please blog about it or post a comment and I'll do a follow up post!

Implict &amp; Explicit Views
One great feature of [Jersey](https://jersey.dev.java.net/) is the support for Implicit and Explicit Views. Update: here's a[link describing implict/explicit views](http://blogs.sun.com/sandoz/entry/mvcj). It basically allows the controller to delegate to the view layer (JSP/Velocity/Freemarker/[GXP](http://code.google.com/p/gxp/) or whatever) using a default naming convention to find the template files in a directory named after the resource bean's class name and if required - the URI being requested. So a given Resource bean could have an index.jsp and edit.jsp templates for example - and referring to ${it.foo} inside the template would extract the foo property of the resource bean (you can override what 'it' is if you like but the resource bean is a very reasonable default).

Maybe the easiest way to understand implicit/explicit views is to look at the bookstoreexample in the Jersey distro; its basically the glue between JAX-RS resources and templates.

However there are some issues (which are being addressed particularly by the helpful folks on the Jersey list, particularly Marc and Paul) which basically revolve around having implicit/explicit views and (say) XML/JSON representations on the same resource bean using the same URIs and getting JAX-RS to pick the right one.

For example the URI "/customers" might return a HTML page for most web browsers but return XML/JSON if folks want to specify those MIME types in their Accept header. Right now Jersey tends to favour returning XML/JSON (long story but basically more specific URI paths are preferred over implicit views).

I'm pretty sure some kind of @ImplicitProduces annotation to allow implicit views to be associated to MIME types along with a higher priority/quality ranking specified in the next feature would solve pretty much all the issues with implicit/explicit views though am sure folks can think of other improvements...

Better Content Negotiation Support
It would be nice to see [these](https://jsr311.dev.java.net/issues/show_bug.cgi?id=46) [issues](https://jsr311.dev.java.net/issues/show_bug.cgi?id=65) resolved to be able to use [RFC 2296](http://gewis.win.tue.nl/~koen/conneg/rfc2296.txt) to be able to raise and lower the priorities (or quality) of the different representations.

e.g. you might want to prefer to return HTML over XML/JSON so unless folks ask specifically just for XML or JSON you return HTML.

Bizarrely Safari uses an Accept header of
> text/xml,application/xml,application/xhtml+xml,text/html;q=0.9,text/plain;q=0.8,image/png,*/*;q=0.5
which kinda means it prefers to render XML over HTML (despite rendering XML as plain text! Truly bizarre - what was Steve smoking that day :). Most web apps I know of would rather return HTML by default :)

So you might want to specify something like this on a resource to declare that you want to return HTML by default for most users...
> @ImplicitProduces("text/html;q=1.0")
> 
> @Produces("text/xml;q=0.5, application/xml;q=0.5; application/json;q=0.5")
> 
> public class MyResource {
> 
> ...
> 
> }
Those MIME Strings could be static final constants BTW to keep things DRY and not have to repeat a ton of MIME expressions throughout your resource beans and making it real easy to add a new MIME type to your code without changing your controller methods.

You could argue the out of the box rankings for HTML should be 1.0 and XML/JSON something lower, say 0.9 or 0.5 as thats what most folks would want.

Dealing with static content
The Servlet spec is a tad smelly in this area (and even in servlet 3.0) of not easily allowing you to mark URI patterns as being handled by the container (e.g. for static content or for JSP files etc) and [mapping all of the rest to your default servlet](http://n2.nabble.com/Static-references-from-JSP-td794843.html). Hopefully as [Paul suggests](http://n2.nabble.com/Static-references-from-JSP-td794843.html#a2223598)we can get around this using servlet filters in Jersey; but it'd be nicer if there was a better fix for this. (Rails wins here hands down with using regex to map URIs to controllers). But this is more a servlet issue than JAX-RS / web framework.

Multipart support
Having support for Struts style form beans; binding a multipart to a bean is very handy.[MrStruts himself has contributed jersey-multipart](http://n2.nabble.com/Hello-World%21-and-Welcome-to-jersey-multipart-td1343189.html#a1343189) which goes most of the way there. Allowing direct binding with any bean to avoid having to use the MultiPart class directly would certainly help.

Standard JAX-RS client API like Jersey's
While not that relevant directly to web applications; being able to implement a REST service by invoking other RESTful services - or being able to test easily any RESTful service using an API like Jersey's client - while reusing the cool entity providers on both client and server side - would rock.

GWT and JAX-RS client integration
If you're building JAX-RS services and want a rich web client then [GWT](http://code.google.com/webtoolkit/) is a great solution; you can reuse all your Java code on the client and server side and its very easy to debug the whole application in a single JVM in your Java IDE.

GWT ships with its own RPC mechanism and there are various REST libraries for GWT such as [gwt-rest](http://code.google.com/p/gwt-rest/) it'd be nice if there was an easy way to reuse the same DTOs used on the server side like you can with Jersey's client library from inside GWT. Using JAXB inside GWT is probably non-feasaible :) but it should be pretty easy to just use GWT serialization and then support it as an entity provider in the JAX-RS runtime. (Is there a MIME type for GWT serialisation I wonder :).

Basically as an application developer it should be trivial to be able to reuse RESTful resources in JAX-RS inside the GWT client reusing all those DTOs if you want to.

Update: some other suggestions
Julio Faerman [mentioned](http://n2.nabble.com/JAX-RS---Jersey-as-the-main-Java-web-framework-going-forward...-td2226448.html#a2227796) on the Jersey list mentioned the need for better validation. So maybe we could integrate the validation layer from Struts/Stripes into JAX-RS? Or use the the Bean Validation API ([JSR 303](http://jcp.org/en/jsr/detail?id=303))?

Also Julio and Jon in the comments mentioned the need for managing conversational state so integrating Web Beans ([JSR 299](http://jcp.org/en/jsr/detail?id=299)) as an option for bijection of conversational state on resource beans sounds a good possibility.

What else?
Those are the things I can think of so far. Can you think of any others? What are there features in your favourite Struts/Stripes/SpringMVC/whatever framework on the server that you really can't live without?

I'd love to hear others thoughts; do you think I'm smoking crack or do you agree? :)
