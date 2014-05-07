---
name: TestPackage.md
title: ARI Test Package Organization
layout: default
---

# ARI Test Package Organization

An ARI test package is a set of assessment items in [Embedded JavaScript](EmbeddedJavaScript.html) format and all of their associated resource files such as images, video, stylesheets (CSS), and JavaScript. The files are organized in such a way that the items can be discovered and their metadata retrieved.

*The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](https://www.ietf.org/rfc/rfc2119.txt).* 

## Package Layout

ARI test packages are composed of files in a hierarchical directory structure. For the Proof of Concept, the root of the test package is the "SamplePackage" folder. Here's an example of how the package hierarchy is organized. Names in {braces} are examples and could be any name. All other folder names MUST be exactly as stated.

* root
  * items (contains one folder for each assessment item.
    * {item1} (an item folder.)
      * item.ejs  (The Encapsulated JavaScript file that renders the item.)
      * score.ejs (The Encapsulated JavaScript file that scores the item.)
      * res (A folder that contains resources such as graphics, JavaScript, stylesheets and so forth that can be accessed from the browser side.)
        * {somegraphic.jpg}
        * {somescript.js}
        * {somestyle.css}
      * srv-res (A folder that contains resources for server-side inclusion such as EJS and JavaScript).
	    * {popup.ejs} 
    * {item2} (another item folder)
      * main.ejs
      * score.ejs
      * res
      * srv-res
  * common-res (a folder containing common browser-side resources usable by all items in the package)
  * common-srv-res (a folder containing common server-side resources usable by all items in the package)

Only resources in the "res" and "common-res" folders (and subfolders thereof) are made available over the internet to the browser. For security reasons, all other files in the package are protected against browser access.

To generate URLs that reference shared resources in the package, you SHOULD use *ari_s.toLocalPath* or *ari_b.toLocalPath* to convert from package-relative paths to server-relative paths.

Filenames MAY or MAY NOT be case-sensitive depending on the hosting platform. Accordingly, references to files MUST use the same case as the filename. Likewise, there MUST NOT be two filenames that differ only in case.

## *items* Folder

The *items* folder contains one folder for each assessment item. Names of the subfolders MAY have any value so long as they are unique. However, the folder name of each SHOULD be the same as the assessment item name metadata tag in the header of item.  

## File *item.ejs*

*item.ejs* is an Embedded JavaScript file that generates an HTML fragment containing the assessment item. The primary purpose of the server-side JavaScript is to apply accessibility features according to student-specific settings. However, the JavaScript can also be used for other purposes such as generating items dynamically from JSON templates

Items also have browser-side JavaScript to manage student interaction, and package the student response.

Server-side JavaScript has access to the [ari_s](RuntimeObjects.html#object-aris) object including the [ari_s.a11y](RuntimeObjects.html#property-a11y) property containing current accessibility settings.

The browser-side HTML will be embedded in a &lt;div&gt; element that has the [ariA11y](RuntimeObjects.html#class-ariA11y) CSS class applied. This class will be set to the foreground and background colors specified by the student's accessibility settings.

Browser-side JavaScript has access to the [ari_b](RuntimeObjects.html#object-arib) object. It should respond to the [ari_b.doupdate](RuntimeObjects.html#event-doupdate) event and should set the student response in the [ari_b.response](RuntimeObjects.html#property-response) object.

## File *score.ejs*

*score.ejs* is an Embedded JavaScript file that scores items. As with *item.ejs*, the server-side JavaScript has access to the [ari_b](RuntimeObjects.html#object-arib) object. In this case, the server-side JavaScript is responsible for scoring the student response that is pre-loaded into [ari_b.response](RuntimeObjects.html#property-response). The scored result MUST be placed into [ari_b.score](RuntimeObjects.html#property-score).

It may seem like *score.ejs* could simply be a JavaScript file. However, bare JavaScript does not have the ability to include other file. In many cases, items will leverage shared JavaScript code to manage scoring of a particular item type so the EJS [include](EmbeddedJavaScript.html#include-directive) directive is important.

As an EJS file, *score.ejs* also generates HTML output. In the Proof of Concept, this is used to present the student's response and the score in a polished way. Other potential uses might be preparing the student response for human scoring or for providing feedback to the student when items are used in a formative context. 

# Naming Conventions for Shared Resources

If ARI evolves from a proof-of-concept into a full implementation, it will be important to prevent collisions between resources in the common folders (*common-res* and *common-srv-res*). In that context, the Item Banking system will assemble test packages composed of hundreds of items. For efficiency, it's valuable to use share common scripts and graphics in the common folders. But if two items expect different versions of the common resource there could be problems.

This issue is addressed by using naming conventions. All common resources SHOULD be placed in subfolders with a version number in the subfolder name. Here are some examples:

* common-res/JQuery-2.35/jquery.js
* common-srv-res/PopupGloss-1.0/PopupGlossary.ejs 