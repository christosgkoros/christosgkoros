+++
title = '7 Steps to Review an Api Design'
date = 2023-10-27T10:06:03+03:00
tags = ['apis', 'api-design', 'api-design-review', 'api-governance']
+++

![Photo by Edho Pratama on Unsplash](/posts/2023/10/27/7-steps-to-review-an-api-design.jpg "Photo by Edho Pratamaash on Unsplash")

## Lay out the Context

An API does not live in isolation. It is part of business, involves people, and may sit next to other APIs. All these are necessary to scope and design the API. Instead of that, you could create many integration pipes. It would be difficult to understand them all in a few years.

*Input: Interviews, relevant api designs, business & architecture diagrams, engineering docs.*

## Identify the nonfunctional requirements

Review the security model & performance expectations.
Input: business & architecture diagrams, Context from step 1
Check against global best practices — which are the styles everyone is using. If your API doesn’t follow them, it will seem strange. f.e. The Richardson Maturity Model LV2 is a good choice for REST APIs. It focuses on resources and HTTP verbs.

*Input: Research*

## Check against Standards & Guidelines

Hopefully, the organization has some API Design Guidelines. Some industries have specific standards (f.e. Banking — BIAN, Telecoms — TM Forum).

Input: API Design Guidelines & industry standard specifications

## Check the Paths

Go through each “path” and its name and verify it fits the design. Naming things can be tricky, but thinking of how the end-user will perceive the name can help.

*Input: Research, Context*

## Check the Behaviors

Go through the API and consumer interactions and behaviors and confirm that it fulfills #1 & #2.

*Input: Context, Non-functional requirements, Paths*

## Check every field in detail

 Go through each field and verify that it fits the design and adds to the extensibility of the API.
A boolean is a non-extensible type, and if the condition may have more than one option in the future, you will be in trouble. For example, the classic field `enabled true/false` may be difficult to work with when the pending email confirmation and several other flags are introduced. A simple string field may serve your purpose better.
Naming fields is equally important with paths and what is mentioned above applies here as well. For fields, it is also helpful to try to remove unnecessary parts of the name. For example, in an `Accounts` resource and given a field `accountName`. The account suffix does not add anything to the field; the field could be just `name`.

*Input: Research*
