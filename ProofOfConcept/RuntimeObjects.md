---
name: RuntimeObjects.md
title: ARI Runtime Object Reference
layout: default
---

# ARI Object Reference

ARI defines two global object classes: "ari_s" is globally available to the server-side JavaScript running within .ejs files and their includes. "ari_b" is globally available to the browser-side JavaScript.

*The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](https://www.ietf.org/rfc/rfc2119.txt).* 

## Members common to both *ari_s* and *ari_b*

### <a name="ari_s.a11y"></a><a name="ari_b.a11y"></a>Property *a11y*

An object containing the current accessibility settings as properties. The properties and values used in the Proof of Concept are similar to but not the same as those used in the operational Smarter Balanced assessment system.

Properties and values may be viewed by selecting certain accessibility options and viewing the "Trace Sample" item.

### Property *response*

This will be set to an empty Javascript *Object* the first time the item is rendered in a particular assessment session. If the student returns to the item, *response* will be filled in with the value that *ari_b.response* was set to on the browser side during the previous interaction. This allows the item to be pre-initialized according to the previous interaction so that the student can review and/or update the response.

*response* is set to the same value in both server-side JavaScript (*ari_s.response*) and browser-side JavaScript. Depending on the item type, it may be more convenient to fill in the values on the server or the browser side. 

In *score.ejs*, *ari_s.response* is set to the student response (as applied to browser-side *arip_b.response*. This is the response that should be scored.

### Method toLocalPath(packagePath)

Converts a path relative to the package (e.g. "/common_res/someimage.png") into a server-local path (e.g. "/packages/package-1234/common-res/someimage.png"). These paths are for use when referencing resources from the browser side regardless of whether generated on the server or the browser side. (Server side includes are always package-relative.)

Example:

  &lt;img src="<%= ari_s.toLocalPath('/common_res/okbutton.png') %>" alt="OK Button"/&gt;

## Object *ari_s*

Contains properties and functions useful on the server side when rendering and scoring assessment items. In addition to the common members listed above, ari_s has the following members: 

### Function *write(String)*

Write a literal string to the generated page output.

### Property *score*

_Only available to score.ejs and its includes._ An object to which the score should be set. Score values are numeric with 1 being a perfect score, 0 being no credit and fractional values such as 0.5 representing partial credit.

Certain items may generate multiple scores from a single interaction. These could correspond to subitems or to different aspects of the item. When that is the case, Score should be set to an object with one property per score. The names of the properties are the ids of the subitems/subscores and the values should be in the range of 0.0 to 1.0.

## Object *ari_b*

Contains properties and functions useful on the server side when rendering and scoring assessment items. In addition to the common members listed above, ari_b has the following members: 

### Event *doupdate()*

Indicates that the item should update the *response* property. This usually indicates that the student is navigating away from the item. It also fires periodically so that a partial student response can be stored on the server in case of a network failure.

# CSS Styles

## Class ariA11y

The *ariA11y* CSS class is pre-defined to match the chosen accessibility colors. It defaults to black on white but may be one of several other selections some of which are reverse contrast (light colors on dark).