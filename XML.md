# XML
Extensible Markup Language

## Introduction
### XML Separates Data from HTML
XML is designed for transport and store data.
HTML is designed for present data.

### Custom tag
no XML standard to define any tag. all tag is designed by user.

### Using XML to seperate data from HTML
With XML, data can be stored in separated XML file. 

With a few lines of Javascript code, read the data from an XML file and update the content of any HTML page.

### Data is stored as plain text in XML
Easy way to share the data from different application and different operating system.

## XML Tree
### Tree structure
XML documents are fromed as element tree, and started with **root element** and branched from root to **child elements**
```
<root>
    <child>
        <subchild>....</subchild>
    </child>
</root>
```

## XML Syntax
### XML Documents Must Have a Root Element
XML documents must contain one root element that is the parent of all other elements:

In this example `<note>` is the root element:
```
<?xml version="1.0" encoding="UTF-8"?>
<note>
    <to>Tove</to>
    <from>Jani</from>
    <heading>Reminder</heading>
    <body>Don't forget me this weekend!</body>
</note>
```

### XML prolog
A prolog defines the XML version and character encoding:
```
<?xml version="1.0" encoding="ISO-8859-1"?>
```
The XML prolog is optional. If it exists, it must come first in the document.

### ALL XML elements must have a closing tag
It is illegal to omit the closing tag. All elements must have a closing tag:
```
<p>This is a paragraph.</p>
<br />  <!-- This is a self-closing tag -->
```

### XML Elements are Case Sensitive
1. XML tags are case sensitive. The tag `<Letter>` is different from the tag `<letter>`
2. Opening and Closing tags must be written with same case:
```
<message>This is correct</message>
```
"Opening and closing tags" are often referred to as "Start and end tags". Use whatever you prefer. It is exactly the same thing.

### XML Elements Must be Properly Nested
In HTML, you might see improperly nested element:
```
<b><i>This text is bold and italic</b></i>
```
Howerver, all elments in XML must be properly nested within each other:
```
<b><i>This text is bold and italic</i></b>
```
In the example above, "Properly nested" simply means that since the `<i>` element is opened inside the `<b>` element, it must be closed inside the `<b>` element.

### XML Attribute Values Must Always be Quoted
XML elements can have attributes in name/value pairs just like in HTML.
In XML, the attribute values must always be quoted:
```
<note date="12/11/2007">
  <to>Tove</to>
  <from>Jani</from>
</note>
```

### Enitiy References
Some characters have a special meaning in XML.

This will generate an XML error:
```
<message> salary < 1000</message>
```

There are 5 pre-defined entity references in XML:

entity references | character | Meaning
---|---|---
&lt; | < | less than
&gt; | > | greater than
&amp; | & | ampersand
&apos; | ' | apostrophe
&quot; | " | quotation mark

Note: Markdown owns same pre-defined entity refrenece as XML

### Comments in XML
The synatx for writting comments in XML is similar to that of HTML:
```
<!-- This is a comment -->
```

Two dashes `--` in the middle of a comment are not allowed:
```
<!-- This is an invalide -- comment -->
```

### White-space in Preserved in XML
XML does not truncate multiple white-spaces (HTML truncates multiple white-spaces to one single white-space).

### XML Stores New Line as LF
Windows applications store a new line as: carriage return and line feed (CR+LF).

Unix and Mac OSX use LF.

Old Mac systems use CR.

XML stores a new line as LF.

## XML Elements
### XML element
An XML element is everythign from (including)  the element's start tag to (including) the element's end tag.
```
<price>29.99</price>
```
An element can contain:
- text
- attributes
- other elements
- or a mix of the above

### Empty XML Elements
```
<element></element>
```
self-closing tag:
```
<element />
<br />
```
Empty elements can have attributes.

## XML Attributes
### XML Attributes Must be Quoted
Attribute values must always be quoted. Either single or double quotes can be used.
```
<person gender="female">
```
or
```
<person gender='female'>
```

if the attributes value itself contians double quotes you can use single quotes:
```
<gangster name='George "Shotgun" Ziegler'>
```
or 
```
<gangster name="George &quot;Shotgun&quot; Ziegler">
```

## XML Namespaces
XML namespaces provide a method to avoid element name conflicts.

### Sloving the name conflict Using a Prefix
Name conflict in XML can easily be avoided using a name prefix.
```
<h:table>
  <h:tr>
    <h:td>Apples</h:td>
    <h:td>Bananas</h:td>
  </h:tr>
</h:table>

<f:table>
  <f:name>African Coffee Table</f:name>
  <f:width>80</f:width>
  <f:length>120</f:length>
</f:table>
```

### XML Namespaces - The xmlns Attribute
When using profixes in XML, a namespace for the prefix must be defined.
The namespace can be defined by an xmlns attribute in the start tag of an element.
The namespace declaration has the following syntax. `xmlns:prefix="URI"`.

```
<root>

<h:table xmlns:h="http://www.w3.org/TR/html4/">
  <h:tr>
    <h:td>Apples</h:td>
    <h:td>Bananas</h:td>
  </h:tr>
</h:table>

<f:table xmlns:f="https://www.w3schools.com/furniture">
  <f:name>African Coffee Table</f:name>
  <f:width>80</f:width>
  <f:length>120</f:length>
</f:table>

</root>
```
In the example above:

The xmlns attribute in the first <table> element gives the h: prefix a qualified namespace.

The xmlns attribute in the second <table> element gives the f: prefix a qualified namespace.

When a namespace is defined for an element, all child elements with the same prefix are associated with the same namespace.

Namespaces can also be declared in the XML root element:
```
<root xmlns:h="http://www.w3.org/TR/html4/"
xmlns:f="https://www.w3schools.com/furniture">

<h:table>
  <h:tr>
    <h:td>Apples</h:td>
    <h:td>Bananas</h:td>
  </h:tr>
</h:table>

<f:table>
  <f:name>African Coffee Table</f:name>
  <f:width>80</f:width>
  <f:length>120</f:length>
</f:table>

</root>
```
Note: The namespace URI is not used by the parser to look up information.

The purpose of using an URI is to give the namespace a unique name.

However, companies often use the namespace as a pointer to a web page containing namespace information.

### Uniform Resource Identifier(URI)
A **Uniform Resource Identifier** (URI) is a string of characters which identifies an Internet Resource.

The most common URI is the **Uniform Resource Locator** (URL) which identifies an Internet domain address. Another, not so common type of URI is the **Uniform Resource Name** (URN).