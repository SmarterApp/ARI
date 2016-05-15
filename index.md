---
name: index.md
title: ARI Introduction
layout: default
---

# ARI Introduction

The Accessible Rendered Item (ARI) format is an developmental format for storing and transmitting pre-rendered assessment items with optional accessibility features. Distributing pre-rendered assessment items in a test package can facilitate innovation in assessment item types, increase fidelity of assessment delivery, and reduce the cost and complexity of compatible assessment delivery systems.

## Status

 * A [Proof of Concept](ProofOfConcept) was completed in May 2014.
 * The [Technical Working Group](2014-11-13_ARI_Technical_Working_Group_Report.pdf) met in November 2014.
 * A [Prototype](Prototype) has been developed by [UCLA CRESST](http://www.cse.ucla.edu/) under commission from [Smarter Balanced](http://www.smarterbalanced.org) and the [CCSSO](http://ccsso.org/).

## Other Resources
 * Dan Rehak contributed a set of [general recommendations for developing specifications standards](DevelopingSpecificationsForTheAccessibleRenderingItemFormat.pdf).

## The Problem

Innovation in assessment is a core value at Smarter Balanced but the existing framework makes field testing experimental item types or deploying new item types complicated and expensive.

Smarter Balanced has approved a [dual-format strategy](http://www.smarterapp.org/spec/2013/11/25/specs-AssessmentItemFormatBrief.html) for the encoding of assessment items. It's a pragmatic strategy; the existing Smarter Balanced Assessment Item Format (SBAIF) supports all of the item types and accessibility features required by Smarter Balanced assessments. Compatibility with IMS APIP facilitates the exchange of assessment items between systems.

However, in order to deploy a new interaction type, Smarter Balanced would have to define how the new type will be encoded in both Smarter Balanced and APIP formats; the specification changes would have to be reviewed and approved; the open source assessment delivery system would have to be updated to support the new type; and all of the assessment delivery vendors who serve the Smarter Balanced states would have to include the new enhancement.

All of this effort requires a great deal of confidence that a new interaction type is effective and worthwhile. But it's difficult to gain confidence in a new interaction type without testing it at scale in the field. The existing Smarter Balanced technology framework is doesn't facilitate the innovation we seek.

## A Delivery-Optimized Format

Structured item formats like IMS QTI are optimized for authoring and maintenance of assessment items. Much like document formats for Microsoft Word, Excel, or Open Office, QTI preserves information about how the item is structured so that a variety of authoring tools can be used to create the item, add accessibility features, and review the results.

However, structured formats are more challenging for test delivery systems. A test delivery system must implement the *all* features of the item format specification.

In contrast, ARI is a Delivery-Optimized format. In this way it's more like  a PDF document which preserves the visual layout and interaction while making is simpler to implement a viewer. Just as Microsoft Office and PDF are used synergistically, so also ARI is intended to be synergistic with IMS QTI.

## Pre-Rendering Assessment Items

Like other web applications, online assessments are delivered through a web browser in the form of HTML plus JavaScript. Assessment delivery systems *render* assessment items from their original format -- be that IMS APIP, SBAIF, or something else -- into HTML plus JavaScript.

If items are pre-rendered before being assembled into a test package the task of the assessment delivery system is greatly simplified. Instead of having to render myriad different formats into HTML, it just delivers the pre-rendered content and collects the student response. Pre-rendering has a number of advantages:

* New item interaction types can be introduced without requiring updates to the assessment delivery engine. New development is isolated to the item authoring system.
* Items display consistently across implementations.
* Custom or prototype assessment item types can be used without updates to the assessment delivery system.
* The assessment delivery engine is greatly simplified by the removal of rendering code.

Pre-rendering is not an idea unique to ARI. IMS QTI includes [Portable Custom Interactions](http://www.imsglobal.org/assessment/interactions.html). the SCORM format is based on HTML and JavaScript. And many proprietary assessment systems also take this approach. However, there are three issues that have prevented pre-rendering from being used in high-stakes assessment:

1. Customizing accessibility features according to student needs.
2. Packaging student responses for unanticipated interaction types.
3. Scoring custom student response types.

The Accessible Rendered Item format addresses these issues. JavaScript is used to customize the item rendering according to the designated student needs. Student responses are packaged in JSON format for convenient storage and transmission. And server-side JavaScript is used for scoring items.
