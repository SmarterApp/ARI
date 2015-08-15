---
name: EmbeddedJavaScript.md
title: ARI Embedded JavaScript
layout: default
---

# ARI Embedded JavaScript File Format

The ARI model is to pre-render assessment items from the original format (such as APIP or Smarter Balanced Assessment Item Format) into Accessible Rendered Item Format. This has a number of advantages over rendering at runtime:

* New item types can be introduced without requiring updates to the assessment delivery engine.
* Consistent rendering of items across implementations.
* Support for custom or prototype assessment item types.
* Simplified rendering code in the assessment delivery engine.

The structured encoding (APIP, Smarter Balanced, etc.) should still be retained in the item bank because the structure information is needed if the item is to be edited, accessibility accommodations are to be added, or if the item is to be used as a model for a new item.

The ARI EJS format is based on the [Embedded JavaScript](http://embeddedjs.com/) syntax with the addition of a header and include directives.

*The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](https://www.ietf.org/rfc/rfc2119.txt).* 

## Encoding
Embedded JavaScript (EJS) files are text files similar to other source code formats. Encoding is [UTF-8](http://en.wikipedia.org/wiki/UTF-8).

Newlines can either be Microsoft style ([CR+LF](http://en.wikipedia.org/wiki/Newline)) or UNIX style (LF only).

Because UTF-8 encoding is required, a [byte-order mark](http://en.wikipedia.org/wiki/Byte_order_mark) is not required and SHOULD not be included. However, robust implementations SHOULD tolerate a byte-order mark at the beginning of the file. 

## Header
Consistent with the [Filemeta Manifesto](http://www.filemeta.org/Manifesto.html), EJS files MUST have a detectable file type ID and embedded metadata. The file type is denoted by a signature at the beginning of the file which is followed by metadata in [Mini YAML](https://code.google.com/p/mini-yaml-parser/) format.

Here is a sample header:

    <%#ejs
    name: tx-math-4312
    title: Lunar Lander
    description: |
       A simulation of a Luner Lander in which the student must calculate
       the amount of thrust required for a soft touchdown on the moon.
    uuid: 5ca84a80-d4bc-11e3-9c1a-0800200c9a66
    version: 3
    %>

### Signature 

The file MUST begin with this signature:

    <%#ejs

The signature MUST be immediately followed by a newline (either CR+LF or LF).

The signature serves two purposes. First, as the very first content in the file it allows simple detection of the file type. Second, it's an EJS directive that indicates the beginning of the file header.

### Mini YAML Metadata

The signature is followed by one or more lines of metadata in [Mini YAML](https://code.google.com/p/mini-yaml-parser/) format. The Proof of Concept uses the following fields:

* name: A unique name that identifies this particular assessment item.
* title: The friendly title that is used to describe this item.
* description: A detailed description of the assessment item.

Other fields may also be included. The example above includes "uuid" and "version".

### Closing delimiter

The header is concluded with a line containing just the following text. The closing delimiter MUST be isolated on a line by itself without leading or trailing spaces.

    %>

## Body

The body of an EJS file consists of HTML with embedded JavaScript. Here's an example:

    <div>
    <%#include "/common-res/layout-2.3/helpers.js" %>
        <h1>Lunar Lander</h1>
        <ul>
            <% for (var i=1; i<=10; ++i) { %>
                <li><%= i %> Something</li>
            <% } %>
        </ul>
    <div>

### HTML content

HTML content is all file contents outside of EJS delimiters, `<%` and `%>`. It is emitted unchanged though JavaScript conditionals and loops may affect the output as described below.

See the [Test Package Documentation](TestPackage.html) for details on how the emitted HTML is treated.

### JavaScript segments

A `<%` delimiter indicates the beginning of a JavaScript segment; `%>` indicates the end. Between the two delimiters can be any amount of JavaScript including function definitions, data definitions and so forth.

HTML content that appears within a JavaScript loop will be repeated as many times as the loop executes. HTML that appears within a JavaScript conditional block (e.g. an "if" statement) will be conditionally emitted.

JavaScript can also send content directly to the output by calling the [ari_s.write](RuntimeObjects.html#method-write) method.

A `<%=` delimiter indicates a JavaScript expression that is terminated with a `%>` delimiter. The JavaScript expression between the two delimiters will be evaluated and the result is embedded in the generated HTML code.

### Include Directive

EJS directives begin with the `<%#` delimiter followed by a directive name and the parameters of the directive. Presently the only EJS directives are the header (initiated by `<%#ejs` and documented above) and the "include" directive.

Here are two sample include directives:

    <%#include "srv-res/layout.js" %>
    <%#include "/common-srv-res/vocab-1.0/popupvocab.ejs" %>

The opening delimiter, `<%#` is followed immediately by the directive name, `include`, one or more spaces and then the filename in full quotes. The filename followed by the `%>` closing delimiter.

Filenames are relative to the test package with a leading slash indicating the root of the package. For details on how files are organized into a package, see the [ARI Test Package](TestPackage.html) documentation.

Include files may be EJS or plain JavaScript. No other include file types are supported. Keep in mind that these are server-side includes. Resource files such as images, browser-side JavaScript, video and so forth can be referenced in the HTML and will be integrated on the browser side.

The type of the include file (EJS or JavaScript) is determined by the file header, NOT by the filename extension. An EJS file must have the EJS header as described in the [Header](#header) topic above.

A JavaScript file included on the server side MUST begin with a header like the following example:

    /*javascript
    name: layout.js
    description: Functions that assist in the layout of drag-and-drop assessment items.
    */

The JavaScript header must be the very first thing in the file. The metadata (name and description in the above example) is not used by the Proof of Concept implementation but the name, at a minimum, is recommended. Since this is a multi-line comment in JavaScript syntax it will be ignored by JavaScript parsers.

## Implementation Notes

The EJS file along with all includes is translated into one large JavaScript function. The sole parameter to the function is the "ari_s" object as described in the [Runtime Object](RuntimeObjects.html) documentation. All HTML content (everything outside of <% and %> delimiters) is converted into [ari_s.write](RuntimeObjects.html#function-writestring) statements. Conveniently, JavaScript supports the definition of functions within other functions so functions embedded within EJS files (or their includes) require no special treatment.

Once the EJS has been converted into straight JavaScript, the function is executed to generate the HTML stream that is sent to the browser. The Proof of Concept uses the Microsoft JScript.net compiler to compile the JavaScript into machine code. However, the syntax and concept are entirely compatible with other JavaScript/ECMAScript implementations.

To reduce server load and increase performance in an actual deployment, the Compiled JavaScript and the generated HTML it produces could both be cached. Since the generated HTML will change according to accessibility, the accessibility settings would have to be included in the key to the rendered HTML cache whereas the compiled EJS would be valid regardless of variations in accessibility settings.

The HTML page that hosts the assessment item must include JavaScript that implements the [ari_b](RuntimeObjects.html#object-ari_b) object. It would be convenient to use a JavaScript library such as JQuery to implement that page. However, it's possible, and likely that the assessment item itself uses JQuery. In order to minimize collisions between library use by the host page and the assessment item, JavaScript in the host page does not use JQuery. It is a minimal set of code sufficient to supply services needed by the item and to communicate the student response to with the server.