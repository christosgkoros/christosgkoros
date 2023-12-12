+++
title = 'A guide to creating your API design guidelines'
date = 2023-10-21T10:06:03+03:00
tags = ['apis', 'api-design', 'api-design-guidelines', 'api-governance']
+++

![Photo by Javier Allegue Barros on Unsplash](/posts/2023/10/21/a-guide-to-creating-your-api-design-guidelines.jpg "Photo by Javier Allegue Barros on Unsplash")

For the last year, I have been working on what “better API design” means. How can we better design the public and our internal APIs?

The term that comes to my mind to define this is maturity, thus API design maturity.

What is API design maturity, then? When I searched on Google, most of the results were about the Richardson Maturity Model. Some results may refer to API-First and how an organization generally treats APIs. But those are not helpful when you have the task of creating a new API.

Let’s take a step back. What is API Design

>API design aims to make an interface that is simple, quick, safe and matches other APIs and the application context.
>
>*Definitions are hard

The book [The Design of Web APIS](https://www.manning.com/books/the-design-of-web-apis)by [Arnaud Lauret](https://apihandyman.io/) helps you understand and create a checklist for API design.

I define API Design maturity as how well we check all the items in our API Design checklist. We must also consider how well we assist our Software Engineers in checking those items.

As I kept researching, my approach changed from generic and rigid to more flexible.

*Facts*

* There are some very well-educated people in API Design
* Organizations always have strategies to improve their APIs

*Problems*

* Difficulty in realizing those strategies.
* Bandwidth — Fit API Design along with roadmaps.
* Day-to-day — Fit API Design along with day-to-day activities and ad hoc requests.
* A lack of clear API design guides means more decisions must be made and, as a result, time lost.

We should aim for our APIS to add value by helping with business by:

* Elevating reliability
* Streamlining development & delivery
* Expanding API & technology footprint

My approach evolved to adapt to those specific problems. How can we translate these goals to requirements for API design maturity?

## ADM

Being an architect, I naturally view API design as an architectural capability. To structure and plan my API design journey, I used an Architecture Development Method (ADM).

There are various frameworks, like TOGAF, for creating architectures. All these frameworks have a similar approach to:

* Define the requirements
* Establish the current status
* Establish the future status
* Draft a plan to go from current to future.

Of course, these frameworks don’t have the best reputation. In an attempt to cut every possible risk, they are very abstract and very detailed at the same time. They are hard to understand and even harder to apply.

For API design, we needed something simpler; a feedback loop like what we use for API or product design.

### Development Method Phases

![API design guidelines ADM](/posts/2023/10/21/adm.jpg "API design guidelines ADM")

#### Preliminary

* Revisit who is concerned and who works on API design.
* Identify (revisit) how we do API design.
* Identify our improvement opportunities.

#### Definition

* Define our API design principles.
* Define the API Design Content (Guides, Checklists, etc) that we need to create
* Define the interactions/communications

#### Development

* Create the content that we need or enhance the existing
* Adopt the lifecycle during current development projects

#### Continuous Management

* Measure
* Observe
* Confirm
* Expand the footprint and address issues
* Set priorities and a plan for the existing APIs

## Getting to Action

I am using the API design maturity development method to improve APIs and make teams happier.
