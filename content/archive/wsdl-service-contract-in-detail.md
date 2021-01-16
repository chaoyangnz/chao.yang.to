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
<?xml version="1.0" encoding="UTF-8"?>;
<definitions xmlns:soap="http://schemas.xmlsoap.org/wsdl/soap/"
             xmlns:tns="http://rand/"
             xmlns:xsd="http://www.w3.org/2001/XMLSchema"
             xmlns="http://schemas.xmlsoap.org/wsdl/"
             targetNamespace="http://rand/" name="RandServiceService">;
  <types>;
    <xsd:schema>;
      <xsd:import namespace="http://rand/"
                  schemaLocation="http://localhost:8888/rs?xsd=1">;</xsd:import>;
    </xsd:schema>;
  </types>;
  <message name="next1">;
    <part name="parameters" element="tns:next1">;</part>;
  </message>;
  <message name="next1Response">;
    <part name="parameters" element="tns:next1Response">;</part>;
  </message>;
  <message name="nextN">;
    <part name="parameters" element="tns:nextN">;</part>;
  </message>;
  <message name="nextNResponse">;
    <part name="parameters" element="tns:nextNResponse">;</part>;
  </message>;
  <portType name="RandService">;
    <operation name="next1">;
      <input message="tns:next1">;</input>;
      <output message="tns:next1Response">;</output>;
    </operation>;
    <operation name="nextN">;
      <input message="tns:nextN">;</input>;
      <output message="tns:nextNResponse">;</output>;
    </operation>;
  </portType>;

  <!-- implementation dependent definition -->;
  <binding name="RandServicePortBinding" type="tns:RandService">;
    <soap:binding transport="http://schemas.xmlsoap.org/soap/http"
                  style="document">;</soap:binding>;
    <operation name="next1">;
      <soap:operation soapAction="">;</soap:operation>;
      <input>;
        <soap:body use="literal">;</soap:body>;
      </input>;
      <output>;
        <soap:body use="literal">;</soap:body>;
      </output>;
    </operation>;
    <operation name="nextN">;
      <soap:operation soapAction="">;</soap:operation>;
      <input>;
        <soap:body use="literal">;</soap:body>;
      </input>;
      <output>;
        <soap:body use="literal">;</soap:body>;
      </output>;
    </operation>;
  </binding>;
  <service name="RandServiceService">;
    <port name="RandServicePort" binding="tns:RandServicePortBinding">;
      <soap:address location="http://localhost:8888/rs">;</soap:address>;
    </port>;
    <port name="StandbyServicePort" binding="tns:RandServicePortBinding">;
      <soap:address location="http://localhost:9999/rs">;</soap:address>;
    </port>;
  </service>;
</definitions>;
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
<operation name="query">;
  <input message="tns:query">;</input>;
  <output message="tns:queryResponse">;</output>;
</operation>;
...
```


#####  Out-In



```java
...
<operation name="questionnaire">;
  <output message="tns:questionnaire">;</output>;
  <input message="tns:questionnaireFeedback">;</input>;
</operation>;
...
```


##### In-Only



```java
...
<operation name="ping">;
  <input message="tns:ping">;</input>;
</operation>;
...
```


##### Out-Only



```java
...
<operation name="notification">;
  <output message="tns:notificationResponse">;</output>;
</operation>;
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
<td>`<soapbind:binding>;`element</td>
<td>`style="rpc"`</td>
<td>`style="document"`</td>
</tr>
<tr bgcolor="#ffffff">
<td>`<wsdl:part>;`element(s)</td>
<td><span style="text-decoration: underline;">Any number</span> of `<part>;` elements, each containing a _type_ attribute</td>
<td><span style="text-decoration: underline;">Single</span> `<part>;` element containing an _element_attribute; zero also allowed</td>
</tr>
</tbody>
</table>
RPC-style SOAP:


```java
<tns:matchNoteAndNote xmlns:tns="urn:outline.demo">;
    <in0 xsi:type="xsd:string">;0000000000</in0>;
    <in1 xsi:type="xsd:string">;000000000B</in1>;
</tns:matchNoteAndNote>;
```



```java
<tns:matchNoteAndNoteResponse xmlns:tns="urn:outline.demo">;
    <matchNoteAndNoteReturn xsi:type="xsd:string">;yes</in0>;
</tns:matchNoteAndNoteResponse>;
```

Document-style SOAP:


```java
<out:getNoteResponse xmlns:out="urn:outline.demo">;
    <out:note key="000000000B" >;
        <out:content>;test</out:content>;
    </out:note>;
</out:getNoteResponse>;
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
<td>`<soapbind:body>;` `use`attribute</td>
<td>`use="encoded"`</td>
<td>`use="literal"`</td>
</tr>
<tr bgcolor="#ffffff">
<td>Other `<soapbind:body>;`attributes</td>
<td>`encodingStyle="http://schemas.xmlsoap.org/soap/encoding/"`</td>
<td>Optional: `parts` attribute referring to a`<wsdl:part>;` name</td>
</tr>
</tbody>
</table>

#### SoapAction

//TODO
