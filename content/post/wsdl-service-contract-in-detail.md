---
title: WSDL Service Contract in Detail
toc: true
id: 981
categories:
  - SOAP Web Service
date: "2015-04-21T14:47:44+00:00"
---

### Concepts in WSDL definition



```java
&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;definitions xmlns:soap="http://schemas.xmlsoap.org/wsdl/soap/"
             xmlns:tns="http://rand/"
             xmlns:xsd="http://www.w3.org/2001/XMLSchema"
             xmlns="http://schemas.xmlsoap.org/wsdl/"
             targetNamespace="http://rand/" name="RandServiceService"&gt;
  &lt;types&gt;
    &lt;xsd:schema&gt;
      &lt;xsd:import namespace="http://rand/"
                  schemaLocation="http://localhost:8888/rs?xsd=1"&gt;&lt;/xsd:import&gt;
    &lt;/xsd:schema&gt;
  &lt;/types&gt;
  &lt;message name="next1"&gt;
    &lt;part name="parameters" element="tns:next1"&gt;&lt;/part&gt;
  &lt;/message&gt;
  &lt;message name="next1Response"&gt;
    &lt;part name="parameters" element="tns:next1Response"&gt;&lt;/part&gt;
  &lt;/message&gt;
  &lt;message name="nextN"&gt;
    &lt;part name="parameters" element="tns:nextN"&gt;&lt;/part&gt;
  &lt;/message&gt;
  &lt;message name="nextNResponse"&gt;
    &lt;part name="parameters" element="tns:nextNResponse"&gt;&lt;/part&gt;
  &lt;/message&gt;
  &lt;portType name="RandService"&gt;
    &lt;operation name="next1"&gt;
      &lt;input message="tns:next1"&gt;&lt;/input&gt;
      &lt;output message="tns:next1Response"&gt;&lt;/output&gt;
    &lt;/operation&gt;
    &lt;operation name="nextN"&gt;
      &lt;input message="tns:nextN"&gt;&lt;/input&gt;
      &lt;output message="tns:nextNResponse"&gt;&lt;/output&gt;
    &lt;/operation&gt;
  &lt;/portType&gt;

  &lt;!-- implementation dependent definition --&gt;
  &lt;binding name="RandServicePortBinding" type="tns:RandService"&gt;
    &lt;soap:binding transport="http://schemas.xmlsoap.org/soap/http"
                  style="document"&gt;&lt;/soap:binding&gt;
    &lt;operation name="next1"&gt;
      &lt;soap:operation soapAction=""&gt;&lt;/soap:operation&gt;
      &lt;input&gt;
        &lt;soap:body use="literal"&gt;&lt;/soap:body&gt;
      &lt;/input&gt;
      &lt;output&gt;
        &lt;soap:body use="literal"&gt;&lt;/soap:body&gt;
      &lt;/output&gt;
    &lt;/operation&gt;
    &lt;operation name="nextN"&gt;
      &lt;soap:operation soapAction=""&gt;&lt;/soap:operation&gt;
      &lt;input&gt;
        &lt;soap:body use="literal"&gt;&lt;/soap:body&gt;
      &lt;/input&gt;
      &lt;output&gt;
        &lt;soap:body use="literal"&gt;&lt;/soap:body&gt;
      &lt;/output&gt;
    &lt;/operation&gt;
  &lt;/binding&gt;
  &lt;service name="RandServiceService"&gt;
    &lt;port name="RandServicePort" binding="tns:RandServicePortBinding"&gt;
      &lt;soap:address location="http://localhost:8888/rs"&gt;&lt;/soap:address&gt;
    &lt;/port&gt;
    &lt;port name="StandbyServicePort" binding="tns:RandServicePortBinding"&gt;
      &lt;soap:address location="http://localhost:9999/rs"&gt;&lt;/soap:address&gt;
    &lt;/port&gt;
  &lt;/service&gt;
&lt;/definitions&gt;
```

In the following diagram, please observe the relationship:

*   A web service can has only one PortType, but has multiple Bindings and Ports.
*   Multiple Bindings refer to the ONLY one PortType.
*   Multiple Ports can map into one Binding.
![wsdl](/media/wsdl.png)

#### Port Type

**Port type** is an implementation-neutral definition. You can regard it as the equivalent of a Java Class.

It contains multiple **operations with MEP(message exchange pattern)**, which is similar as class methods. A parameter of a operation is called a **part**.

#### Binding

**Binding** is a wire/transport protocol definition. It includes the **message format **and **transport** combination.

#### Port

**Port** is a term related deployment. Basically, it connects a **Binding** with a **Endpoint**.

#### Target namespace

A namespace must be a URI. Sometimes using URL as the namespace may make others confused, because they may use this URL to download your WSDL but often failed. Instead using URN as the namespace can effectively avoid this case.

### WSDL sections

#### types section

Here we can define XML Schema types or import types definitions from separate XSD files.

#### message section

Every message can have one or more parts. A part can be specify by _type_ or _element_ attribute.

#### portType section

PortType basically defines an abstract service interface: **_operations_ **and the **Message Exchange Patterns (MEP)**.

##### **Message Exchange Patterns (MEP)**

WSDL supports four basic patterns of operation:
<table style="height: 184px;" border="1" width="744">
<tbody>
<tr>
<td></td>
<td>use scenarios</td>
<td></td>
</tr>
<tr>
<td>In-Out</td>
<td>most commonly used: request / response</td>
<td>*****</td>
</tr>
<tr>
<td>In-Only</td>
<td>send without expecting response: one-way messaging</td>
<td>***</td>
</tr>
<tr>
<td>Out-In</td>
<td><span id="solicit_response">solicit / response</span></td>
<td>*</td>
</tr>
<tr>
<td>Out-Only</td>
<td>one-way server push: notification or (asynchronous) callback</td>
<td>**</td>
</tr>
</tbody>
</table>

##### In-Out



```java
...
&lt;operation name="query"&gt;
  &lt;input message="tns:query"&gt;&lt;/input&gt;
  &lt;output message="tns:queryResponse"&gt;&lt;/output&gt;
&lt;/operation&gt;
...
```


#####  Out-In



```java
...
&lt;operation name="questionnaire"&gt;
  &lt;output message="tns:questionnaire"&gt;&lt;/output&gt;
  &lt;input message="tns:questionnaireFeedback"&gt;&lt;/input&gt;
&lt;/operation&gt;
...
```


##### In-Only



```java
...
&lt;operation name="ping"&gt;
  &lt;input message="tns:ping"&gt;&lt;/input&gt;
&lt;/operation&gt;
...
```


##### Out-Only



```java
...
&lt;operation name="notification"&gt;
  &lt;output message="tns:notificationResponse"&gt;&lt;/output&gt;
&lt;/operation&gt;
...
```


#### bindings section

There can be many bindings sections.

Bindings defines a concrete Web service implementation, like transport (HTTP, STMP..), service style and SOAP version.

##### transport

transport options: http, stmp...

##### document-style vs. rpc-style

service styles: **document-style**, **rpc-style**

*   `document` style means that SOAP body contains a XML document which can be validated against pre-defined XML schema document.
(There's also a variation called _wrapped_ that is still specified as document)

*   `rpc` style really means that SOAP message body contains an XML representation of a method call and uses the names of the method and its parameters to generate XML structures that represent a method’s call stack. It adds extra elements to simulate a method call.
(rpc is a misleading name, which has nothing to do with a programming model)

##### literal vs. encoded

operation parts "use" has two options: **encoded**, **literal**. It determines how a data value should be encoded in an XML format.

*   literal means that the data is serialized according to a XML schema
*   encoded means that the service’s type definitions come from implicit encoding rules, typically the rules in the SOAP 1.1 specification.
The soapAction attribute specifies the value of the SOAPAction header for this operation as an absolute URL.

For the HTTP protocol binding of SOAP, this is value required (it has no default value). For other SOAP protocol bindings, it MUST NOT be specified.

#### service section

Defines a port, or Web service destination, in terms of the above binding plus a URL.

The `service` element has `port` subelements, where a `port` is linked to a `binding. `

The address subelement of a port specifies a `location`, whose value is commonly called the **service endpoint.**

### Common confusions

#### service styles: document vs. rpc

<table border="1">
<tbody>
<tr bgcolor="#990033">
<td></td>
<td>RPC</td>
<td>Document</td>
</tr>
<tr bgcolor="#cccccc">
<td>`&lt;soapbind:binding&gt;`element</td>
<td>`style="rpc"`</td>
<td>`style="document"`</td>
</tr>
<tr bgcolor="#ffffff">
<td>`&lt;wsdl:part&gt;`element(s)</td>
<td><span style="text-decoration: underline;">Any number</span> of `&lt;part&gt;` elements, each containing a _type_ attribute</td>
<td><span style="text-decoration: underline;">Single</span> `&lt;part&gt;` element containing an _element_attribute; zero also allowed</td>
</tr>
</tbody>
</table>
RPC-style SOAP:


```java
&lt;tns:matchNoteAndNote xmlns:tns="urn:outline.demo"&gt;
    &lt;in0 xsi:type="xsd:string"&gt;0000000000&lt;/in0&gt;
    &lt;in1 xsi:type="xsd:string"&gt;000000000B&lt;/in1&gt;
&lt;/tns:matchNoteAndNote&gt;
```



```java
&lt;tns:matchNoteAndNoteResponse xmlns:tns="urn:outline.demo"&gt;
    &lt;matchNoteAndNoteReturn xsi:type="xsd:string"&gt;yes&lt;/in0&gt;
&lt;/tns:matchNoteAndNoteResponse&gt;
```

Document-style SOAP:


```java
&lt;out:getNoteResponse xmlns:out="urn:outline.demo"&gt;
    &lt;out:note key="000000000B" &gt;
        &lt;out:content&gt;test&lt;/out:content&gt;
    &lt;/out:note&gt;
&lt;/out:getNoteResponse&gt;
```


#### uses: literal vs. encoded

<table border="1">
<tbody>
<tr bgcolor="#990033">
<td></td>
<td>**SOAP encoded**</td>
<td>**Literal**</td>
</tr>
<tr bgcolor="#cccccc">
<td>`&lt;soapbind:body&gt;` `use`attribute</td>
<td>`use="encoded"`</td>
<td>`use="literal"`</td>
</tr>
<tr bgcolor="#ffffff">
<td>Other `&lt;soapbind:body&gt;`attributes</td>
<td>`encodingStyle="http://schemas.xmlsoap.org/soap/encoding/"`</td>
<td>Optional: `parts` attribute referring to a`&lt;wsdl:part&gt;` name</td>
</tr>
</tbody>
</table>

#### SoapAction

//TODO
