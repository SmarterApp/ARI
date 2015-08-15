---
name: DesignIssues.md
title: ARI Proof of Concept Issues
layout: default
---

# Design Issues to be Addressed

The ARI Proof of Concept shows has proven that Embedded JavaScript (EJS) is a viable approach for pre-rendering assessment items and adapting them at runtime according to accessibility settings. The following design issues linger and should be addressed in the process of going from Proof of Concept to full implementation.

## YAML Metadata

**Issue**
The metadata information in EJS file headers uses [Mini YAML](https://code.google.com/p/mini-yaml-parser/) syntax. This adds one more syntax to the those already in use (HTML, JavaScript, JSON, and CSS). The syntax count could be reduced by using JSON syntax in the header instead of YAML.

**Discussion**
EJS files and server-side JavaScript includes start with a header that includes a filetype signature and metadata in [Mini YAML](https://code.google.com/p/mini-yaml-parser/) format. This choice was inspired by the use of YAML headers in [GitHub Pages]() and other [Jekyll](http://jekyllrb.com/) deployments. Indeed, this document includes such a YAML header for use of Jekyll.

Mini YAML is robust and intuitive; most programmers get it right without studying the syntax. It does not support escaping so there are some unusual character sequences that can't be embedded in Mini YAML values. Mini YAML could be expanded to [full YAML](http://yaml.org/) syntax to gain character escaping, hierarchical structure and lists.

The alternative would be to use JSON syntax in the headers. This would reduce the parser count by one and gain hierarchical metadata, character escaping and lists all at once. The costs would be readability and greater risk of syntax errors in the metadata.

Here's a sample header in the existing YAML syntax:

    <%#ejs
    name: tx-math-4312
	title: Lunar Lander
	description: |
       A simulation of a Luner Lander in which the student must calculate
       the amount of thrust required for a soft touchdown on the moon.
    uuid: 5ca84a80-d4bc-11e3-9c1a-0800200c9a66
    version: 3
	%>	

Here's an equivalent header that uses JSON syntax:

    <%#ejs
    {
	name: "tx-math-4312",
	title: "Lunar Lander,
	description: "A simulation of a Luner Lander in which the student must calculate \nthe amount of thrust required for a soft touchdown on the moon.",
    uuid: "5ca84a80-d4bc-11e3-9c1a-0800200c9a66",
    version: 3
    }
	%>	

My (Brandt Redd's) preference remains YAML because I hope to extend the convention of metadata headers to other source code files. YAML remains intuitive and JSON doesn't have any particular tie to C++, PHP, Ruby, etc.

## Depends-On Metadata

**Issue**
The Proof of Concept implementation does not have any way for an item bank to determine the resources on which an particular item depends.

**Discussion**
The proposed use pattern is for the assessment item authoring system to pre-render assessment items into ARI format and then store those items in an assessment item bank. (Items would also be stored in their original, structured format in the same bank.) When a test is constructed, a pool of items would be retrieved from the item bank and assembled into a test package. In assembling that test package, the assessment system needs to know the common files on which a particular assessment item depends so that they may be included in the test package.

Potential solutions:
1. Each item could be assembled as a single-item package (according to the [Test Package](TestPackage.html) specification). When items are imported into the item bank, it would either keep the single-item package intact (redundantly storing common resources) or it would keep a database of the files that were included in the package with the assumption that the item depends on all other resources. This approach has the disadvantage that dependency information is lost when more than one item is included in a package.

2. Dependencies could be discovered by searching through the HTML and JavaScript to find all references to resources in the package. This can work fairly well for HTML in which all references are well-structured. But it can be difficult for an algorithm to detect dependency files that are constructed in JavaScript. Hence, this method is unreliable.

3. Files on which an item depends could be stored separately for every item -- there would be no shared resources and common files would be stored and transmitted redundantly.

4. The metadata section of EJS files could include a "DependsOn" property which lists all of the resources on which an item depends. This would allow structured handling of common files by the item banking system.

I (Brandt Redd) prefer option 4.

## Relative URLs
EJS files can use relative URLs such as "res/image.png" to reference resources associated with the item. They can also use [ari_s.toLocalPath](RuntimeObjects.html#method-tolocalpath) and [ari_b.toLocalPath](RuntimeObjects.html#method-tolocalpath) to generate server-local paths for shared resource. However, relative URLs depend on the base URL of the page in which the item is embedded. This means that only one item can be embedded in any particular HTML page (unless frames are used). In order to support multiple items per page (at the discretion of the assessment delivery system) it may be appropriate to have functions for generating paths to item-specific resources and to shared resources.

However, there are other, more complicated, problems when rendering multiple items on a page. For example, items may use the same global variable names, or they may use conflicting JavaScript libraries. Therefore, frames approach (e.g. iFrames) may be the better option regardless.

## CSS Links

ARI EJS files generate HTML Fragments. As a result, they don't have access to the HTML header. One problem with this is that an EJS file cannot specify a link to .css stylesheet file because such links must appear in the HTML header.

The current solution is to use the [include directive](EmbeddedJavaScript.html#include-directive) to include embed the literal CSS within &lt;style&gt; tags in the generated HTML. However, this negates the ability of the browser to cache stylesheets.

One potential remedy would be to add an "addLink" method to the [ari_s](RuntimeObjects.html#object-arib) object. Such a method would allow the EJS to specify CSS, JavaScript, and other links to be included in the page header.
