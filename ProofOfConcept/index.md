---
name: index.md
title: ARI Proof of Concept
layout: default
---

The ARI Proof of Concept was developed in the winter and spring of 2014. It proved the viability of the ARI concept - that assessment items could be pre-rendered and that delivery of items could be achieved with minimal test delivery code.

The ARI Technical Working Group (TWG) met in November of 2014. Among their recommendations was that all item rendering should be performed on the browser side. This includes customization according to accessibility requirements. This represents a fundamental design change as the proof of concept uses server-side JavaScript to customize the content before it is delivered to the browser.

The proof of concept documentation and source code are archived here for reference purposes. A prototype incorporating the TWG recommendations and incorporating more item types will be posted in fall 2015.

# Introduction to the Proof of Concept

ARI is an experimental format for storing and transmitting pre-rendered assessment items with optional accessibility features. Distributing pre-rendered assessment items in a test package can facilitate innovation in assessment item types, increase fidelity of assessment delivery, and reduce the cost and complexity of compatible assessment delivery systems.

* [Proof of Concept Source Code](http://www.github.com/SmarterApp/ARI_POC)
* [Test Package Documentation](TestPackage.html)
* [Runtime Object Documentation](RuntimeObjects.html)
* [Known Design Issues](DesignIssues.html)

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

The [Accessible Rendered Item (Format) Proof of Concept](http://github.com/SmarterApp/ARI_POC) is an experiment to test whether JSON and server-side JavaScript can be applied to address these three issues. Among the design goals is to keep the format and specification as simple as possible. Phase 1 has successfully proven the ability to render and score a complex assessment item with multiple accessibility features.

The three issues are resolved by using JavaScript both on the server and on the browser side:

1. On the server side, [Embedded JavaScript](EmbeddedJavaScript.html) is used to customize the rendered HTML to according to accessibility settings.
2. Browser-side JavaScript manages the student interaction and collects the student response into a JavaScript object. The JavaScript object, containing the student response, is encoded in JSON and transmitted back to the server.
3. On the server side, the JSON is reconstituted into a JavaScript Object and passed to a JavaScript function that manages the scoring of the object.

The ARI format is intended to be complementary to structured formats such as APIP. When authoring items it's important to retain the structure of the item so that it can be edited and updated throughout the authoring and review process. Frequently the tools used to author items are different from the tools used to add accessibility features. APIP and similar formats are a superior choice because structural information is lost when the item is rendered.

Metaphorically, it's like the difference between a Word document, which can be easily edited, and a PDF docmument, which faithfully reproduces the look of the document across many platforms. APIP is like a Word document while ARI is like a PDF.

In addition to facilitating innovation, using the ARI format has the following potential advantages:

* **Increased fidelity of implementation:** Since items are pre-rendered, they will be delivered in the same form regardless of the assessment platform and regain common appearance and interaction.  
* **Reduced cost of implementation:** Since Smarter Balanced states independently procure assessment delivery services, there will be a variety of implementations. Reducing this cost will save money for states and vendors and encourage more vendor participation.
* **Reduced cost of code maintenance:** Support for new for new APIP or SBAIF features including new item types and new accessibility features is isolated to the item authoring tool rather than carrying through item banking, test packaging and assessment delivery.
