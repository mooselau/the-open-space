### XML Nodes
XML means the "eXtensible Markup Language".  Understanding XML helps a lot when handling them. 
XML is made up of various kinds of node, which contain different information about this XML file. In this notes, I will talk something about the XML nodes. 

#### XML Structure and Nodes
XML has a structure, which looks like:
``` xml
<root>
    <header>
        <title>I am title.</title>
    </header>
    <body>
        <content>I am content.</content>
    </body>
</root>
```
A XML Structure is just like a HTML page structure, in which different content and information goes to different part in the XML file.

Speaking of the XML Nodes, the entire document is a document node, which starts at "<root>"" and ends at "</root>" in the example above;
And, every element in this XML is an element node, such as: "header", "title", "body", "content".
The text in the XML elements are called "text nodes", e.g. "I am title", "I am content." .

If a node is wrote in a stucture like below:
````xml
<body>
    <content font-size="20"> I am content.</content>
    <!-- <info>This is hidden info.</info> -->
    <![CDATA["This is cdata-section content."]]>
</body>
````
As can be seen, the attribute inside "content" node is *attribute node*. The comment line under the "content" node is *comment node*, followed by a *CData section node* in the last line inside the body node.

 <?xml-stylesheet type="text/xsl" href="style.xsl"?>
This is a *processing instrction node*, which enclosed within "<?" and "?>", and usually shows at the beginning or the ending part in a XMl.

 <!DOCTYPE HTML>
This is a *Document Type Node*.

Except the types mentioned above, there are totally 12 types of XML nodes in a XML file, which includes: 
 - 1. Element node; 
 - 2. Attribute Node;
 - 3. Text Node;
 - 4. CDATA Section Node;
 - 5. Entity Referene Node;
 - 6. Entity Node;
 - 7. Processing Instruction Node;
 - 8. Comment Node;
 - 9. Document Node;
 - 10. Document Type Node; 
 - 11. Document Fragment Node;
 - 12. Notation Node.

The Nodes with number 10, 11 usually come in the header part in a XML as part of document declaration, and moreover, node type of 5 and 6 usually show up inside a Document declaration.

Look at the usage below:
```xml
<!DOCTYPE filename [
<!ENTITY entity-name "entity-content"
]>
```
And an example:
```xml
<!DOCTYPE note [
<!ENTITY copyright "Moose Liu"
]>
<content>
    <text>This is text.</text>
    <writer>&copyright;</writer>
</content>
```
Still have no clue on the notation node :-(, but these are already enough for processing most of the standard XML files.

#### Use JAVA to process XML as DOM object
One way to process XML is using DOM object.

```java
// Create a Document Builder
DocumentBuilderFactory dbf = DocumentBuilderFactory.newInstance();
DocumentBuilder db = dbf.newDocumentBuilder();

// Get the input stream from a String
InputStream is = new ByteArrayInputStream(str.getBytes("UTF-8"));
// Parse the input stream
Document doc = db.parse(is);
```
First to convert a XML content to a document, and then we can use "doc" to manipulate the whole xml content.

There perhaps has another faster approach to do this, I will add when I see it..
