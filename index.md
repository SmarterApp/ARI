---
name: index.md
title: ARI Introduction
layout: default
---

#ARI Introduction

ARI is an experimental format for storing and transmitting pre-rendered assessment items while preserving the ability to apply accessibility features. A Proof of Concept is underway to validate whether the concept is viable and worth pursuing. Distributing pre-rendered assessment items in a test package has potential to facilitate innovation in assessment item types, increase fidelity of assessment delivery, and reduce the cost and complexity of compatible assessment delivery systems.

**Phase 1 of the Proof of Concept is complete and the code is available at [http://www.github.com/SmarterApp/ARI_POC](http://www.github.com/SmarterApp/ARI_POC)**.

## The Problem

Smarter Balanced has approved a [dual-format strategy](http://www.smarterapp.org/spec/2013/11/25/specs-AssessmentItemFormatBrief.html) for the encoding of assessment items. It's a pragmatic strategy. The existing Smarter Balanced Assessment Item Format (SBAIF) supports all of the item types and accessibility features required by Smarter Balanced assessments. Compatibility with IMS APIP facilitates the exchange of assessment items between systems.

However, this strategy greatly increases the cost of developing new assessment item types. In order to deploy a new interaction type, Smarter Balanced would have to define how the new type will be encoded in both Smarter Balanced and APIP formats; the specification changes have to be reviewed and approved; the open source assessment delivery system would have to be updated to support the new type; and all of the assessment delivery vendors who serve the Smarter Balanced states would have to include the new enhancement.

All of this effort requires a great deal of confidence that the new interaction type is effective and worthwhile. It becomes prohibitively complicated to field-test an experimental interaction type.

## Pre-Rendering Assessment Items

Like other web applications, online assessments are delivered through a web browser in the form of HTML plus JavaScript. Assessment delivery systems *render* assessment items from their original format -- be that IMS APIP, SBAIF, or something else -- into HTML plus JavaScript.

If items are pre-rendered before being assembled into a test package the task of the assessment delivery system is greatly simplified. Instead of having to render myriad different formats into HTML, it just delivers the pre-rendered content and collects the student response. Pre-rendering has a number of advantages:

* New item types can be introduced without requiring updates to the assessment delivery engine. New development is isolated to the item authoring system.
* Items display consistently across implementations.
* Custom or prototype assessment item types can be used without updates to the assessment delivery system.
* The assessment delivery engine is greatly simplified by the removal of rendering code.

Pre-rendering is not a new idea. IMS QTI includes [Portable Custom Interactions](http://www.imsglobal.org/assessment/interactions.html). the SCORM format is based on HTML and JavaScript. And many proprietary assessment systems also take this approach. However, there are three issues that have prevented pre-rendering from being used in high-stakes assessment:

1. Customizing accessibility features according to student needs.
2. Packaging student responses for unanticipated interaction types.
3. Scoring custom student response types.

The [Accessible Rendered Item (Format) Proof of Concept](http://https://github.com/SmarterApp/ARI_POC) is an experiment to test whether JSON and server-side JavaScript can be applied to address these three issues. Among the design goals is to keep the format and specification as simple as possible. Phase 1 has successfully proven the ability to render and score a complex assessment item with multiple accessibility features.

The three problems are solved by using JavaScript both on the server and on the browser side:

1. On the server side, [Embedded JavaScript](EmbeddedJavaScript.html) is used to customize the rendered HTML to according to accessibility settings.
2. Browser-side JavaScript manages the student interaction and collects the student response into a JavaScript object. The JavaScript object, containing the student response, is encoded in JSON and transmitted back to the server.
3. On the server side, the JSON is reconstituted into a JavaScript Object and passed to a JavaScript function that manages the scoring of the object.

The ARI format is intended to be complementary to structured formats such as APIP. When authoring items it's important to retain the structure of the item so that it can be edited and updated throughout the authoring and review process. Frequently the tools used to author items are different from the tools used to add accessibility features. APIP and similar formats are a superior choice because structural information is lost when the item is rendered.

Metaphorically, it's like the difference between a Word document, which can be easily edited, and a PDF docmument, which faithfully reproduces the look of the document across many platforms. APIP is like a Word document while ARI is like a PDF.

## Project Status

ARI is still an experimental format. Further validation and development is required before Smarter Balanced could commit to using it in operational assessments. The initial results are quite promising and Smarter Balanced is seeking funding for continued work.

Future work would be divided into the following phases.

* Phase 1: **Proof of Concept:** Encode one rich, interactive assessment item with accessibility features that can be optionally enabled. **The proof of concept phase is complete and the code has been posted with an open source license on GitHub at [http://www.github.com/SmarterApp/ARI_POC](http://www.github.com/SmarterApp/ARI_POC)**.
* Phase 2: **Concept Validation:** Formalize the accessibility settings to match Smarter Balanced Specifications and hand-convert all items in the Smarter Balanced Training Tests. Show that all Smarter Balanced item types and accessibility features can be rendered. Show that all Smarter Balanced item types can be scored.
* Phase 3: **Rendering:** Develop code to automatically render all Smarter Balanced item types from SBAIF to ARI.
* Phase 4: **Specification:** Refine the draft documentation into a formal specification. Form a review committee and approve the specification. Update the code and encoded items to match the approved specification and release in open source as a reference implementation.
* Phase 5: **Strategic Approval:** Gain approval from the Smarter Balanced Architecture Review Board to commit operational assessments to the ARI format.
* Phase 5: **Integration:** Integrate ARI into the operational Smarter Balanced open source implementation.
