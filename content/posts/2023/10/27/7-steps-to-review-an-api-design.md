+++
title = '7 Steps to Review an Api Design'
date = 2023-10-27T10:06:03+03:00
+++

## Lay out the Context
An API does not live in isolation. It is part of business, involves people, and may sit next to other APIs. All these are necessary to scope and design the API. Instead of that, you could create many integration pipes. It would be difficult to understand them all in a few years.

*Input: Interviews, relevant api designs, business & architecture diagrams, engineering docs.*

## Identify the nonfunctional requirements
Review the security model & performance expectations.
Input: business & architecture diagrams, Context from step 1
Check against global best practices — which are the styles everyone is using. If your API doesn’t follow them, it will seem strange. f.e. The Richardson Maturity Model LV2 is a good choice for REST APIs. It focuses on resources and HTTP verbs.

*Input: Internet Research, Experience*

## Check against Standards & Guidelines
Hopefully, the organization has some API Design Guidelines. Some industries have specific standards (f.e. Banking — BIAN, Telecoms — TM Forum).

Input: API Design Guidelines & industry standard specifications

## Check the Paths
Go through each “path” and its name and verify it fits the design.

*Input: Internet Research, Experience, Context*

## Check the Behaviors
Go through the API and consumer interactions and behaviors and confirm that it fulfills #1 & #2.

*Input: Context, Non-functional requirements, Paths*

## Check every field in detail
Go through each field and verify that it fits the design and adds to the extensibility of the API. Also, its name.

*Input: Internet Research, Experience*