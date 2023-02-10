---
title: "Getting to Know the Testing Automation Pyramid"
date: 2023-02-10T17:46:07+07:00
tags: ["blog", "tech"]
author: "Aghits Nidallah"
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: true
description: "A scheme that should be known and adopted by professional software development organizations."
canonicalURL: ""
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: false
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowRssButtonInSectionTermList: true
UseHugoToc: true
cover:
    image: "/images/article/testing-automation-pyramid/thumbnail.jpg" # image path/url
    alt: "Testing Thumbnail" # alt text
    caption: "" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
editPost:
    URL: "https://github.com/NikarashiHatsu/shiroyuki.dev/tree/main/content"
    Text: "Suggest Changes" # edit text
    appendFilePath: true # to append file path to Edit link
---

![Testing Thumbnail](/images/article/testing-automation-pyramid/thumbnail.jpg)
Photo by <a href="https://unsplash.com/@alvarordesign?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Alvaro Reyes</a> on <a href="https://unsplash.com/photos/qWwpHwip31M?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>

The Automation Testing Pyramid, introduced in the book The Clean Coder by
Robert C. Martin, is a representation of the types of tests that professional
software development organizations need.

There are 5 parts to this pyramid, each requiring at least a certain percentage
coverage of each test. Here is the list:
1. Exploratory Test - 5%
1. System Test - 10%
1. Integration Test - 20%
1. Component Test - 50%
1. Unit Test - 100%

## Unit Test

In this section, tests written in the programming language used to build the
system are written by the programmer and returned to the programmer. The tests
written should be able to specify the code requirements at the lowest level, if
possible at the abstraction level.

These unit tests are usually written before creating the production code, which
will then be automatically run by Continuous Integration before being sent to
production.

Unit tests must cover the entire system at least 90%. Robert says that 100% is
impossible, but at least we reach an asymptotic point approaching 100%."

## Component Test

Component Test is a test written by Quality Assurance and Business Analysts,
with the assistance of a programmer. This test must be understood by all
stakeholders, QA, BA, as well as the programmer who writes the test or those who
do not.

This test covers at least 50% of the system. The nature of this test is more
reflective of the "happy path" rather than the "unhappy path".

"Happy path" => Reflects how the component is supposed to function, usually
written by the BA or stakeholder who wants the component to run as desired.

"Unhappy path" => Worst-case scenarios that may occur in the component being
tested, for example, the possibility of errors that may occur, exceptions that
will be thrown. "Unhappy path" is usually used in Test-Driven Development and
Behaviour-Driven Development.

## Integration Test

Integration Test is a type of test that is very important in larger systems with
many components. This testing will gather several components at once, and check
the compatibility of communication between components. This test is usually
written by the System Architect or Lead Designer.

This test is also referred to as the Choreography Test, as it depicts "how each
component dances in harmony with other components".

This test does not test the business flow at all, only testing communication
between components. Usually, Integration Test is not run alongside CI, but run
every night or weekly because it takes a long time. At this level, we test the
performance and throughput of the system.

## System Test

This type of test is an automated test that checks the entire system integrally. 
This test does not directly check the business flow, but tests whether the
system has been connected together correctly. We test the performance and
throughput of the system.

This test is written by the System Architect and Technical Lead, which is
usually written in the same language and environment as the Integration Test for
UI. This test is rarely run, due to its long testing duration.

The System Test covers at least 10% of the system. The purpose of this test is
only to ensure the arrangement of the system, not its behavior. The assumption
is that the behavior testing has already been confirmed in Unit Test and
Component Test.

## Manual Exploratory Test

This is the part where humans or users are directly involved. Since as
developers we cannot write tests for the entire system perfectly, human
involvement is needed.

Humans are creative beings who will always think about destroying a
systematically designed system. It is this human creativity that will be used
to explore how the system's behavior "should" occur according to their
expectations.

This is not an automated test, but rather a scenario that is executed manually
by each individual with a unique usage style. Specifically, the work in this
field is a Bug Hunter.

All bugs found at this level will be written back to the Unit Test, Component
Test, and Integration Test to ensure that the bugs already found will not occur
again in the future.
