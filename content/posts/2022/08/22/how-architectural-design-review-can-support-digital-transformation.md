+++
title = 'How architectural design review can support digital transformation'
date = 2022-08-22T10:06:03+03:00
tags = ['digital-transformation', 'adr', 'architecture-design-review', 'agile']
+++

![Photo by John Schnobrich on Unsplash](/posts/2022/08/22/how-architecture-design-review-can-support-digital-transformation.webp "Photo by John Schnobrich on Unsplash")

## Intro

Successful digital transformation requires a focus on enterprise architecture, whether improving the system’s or application’s architecture or implementing strategies like API standardization.

For an enterprise to practice architecture in an agile environment, several architects and designers must support the project’s agile value streams, which are the actions taken from conception to delivery and support that add value to a product or service. One organization may have a different team of architects producing solution designs and templates. Another might place the architect inside the agile squad in a dual senior engineer role.

No matter the operating model, designing and implementing features in scaled agile environments is a song sung by many voices that may differ in tone, views, opinions, and experience. Therefore, you need to develop processes that:

1. Ensure consistency
2. Avoid redesigning solutions for the same problems
3. Steer implementations towards target architecture goals

A design review process can help you create a model to achieve those goals.

## What is design review?

Architecture is one of my favorite words due to the different things it can refer to. Here is what architecture and design mean in the scope of this article:

* Every system, from the simplest to the most complex, has an inherent architecture.
* An architect is a role (engineer or not) that combines abstract elements — different angles, views, and requirements — to develop a solution.
* Design is the practice of compacting the abstract to the specific. This practice includes the architecture of the systems.

Design review is a process that can step in to align, homogenize, and enhance the architects’ ideas and opinions. In enterprise architecture terms, it is a tool from the governance arsenal. In software engineering terms, it is like a code review for design. Code review is a well-established software engineering practice that helps assess the code, identify bugs, increase code quality and help developers learn the source code. An architecture team can reap these benefits from a design review.

## Elements of a design review

The things involved in a design review include:

* The designer is the person who wants to solve a problem.
* The documentation is the document at the center of attention. It contains information regarding all aspects of the problem and the proposed solution.
* The reviewer is the person who will review the documentation.
* The process includes the agreed-upon rules and interactions that define the designer’s and reviewer’s communications. It may stand alone or be part of a more extensive process. For example, in a software development life cycle, it could precede development, or in an API specification, it could include evaluating changes.
* The review scope is the area the reviewer tries to cover when reviewing the documentation (technical or not).

I’ll explain this with an example.

>An architect needs to design an e-commerce feature for adding a coupon during purchase. The designer documents the benefits, the areas of impact, and the low-level details of the changes. Then, they submit the document to a fellow architect for review. The reviewer may have remarks about the security of the APIs and potential fraud risks or provide a reference to coupon management in loyalty features. Once the designer and the reviewer agree on the final design, the feature can proceed to implementation.

The parties must also consider the time factor in a design review. You cannot endlessly discuss a feature necessary for the business either from a practical or philosophical (we are architects, after all) aspect. Deliverability is essential. Therefore, you should set metrics to measure and control the process performance. Some examples are:

* The number of items in the design review backlog
* The time elapsed from submission until the reviewer made comments
* The time elapsed for a designer to address the comments
* Rate of technical debt

The design review process must be a priority for the designer and the reviewer, as the features are urgent for the business. Someone needs to prioritize the design review backlog and correlate it with deliverability.

## Design review benefits

A traditional agile delivery stream without a design review process might look like this:

![Agile without review](/posts/2022/08/22/agile-without-review.jpg "Agile without review")

This figure shows an agile delivery stream with a design review process:

![Agile with review](/posts/2022/08/22/agile-with-review.jpg "Agile with review")

Following are some benefits of using a design review process to evaluate your architectural designs.

* Quality: As with code review, having an (experienced) peer look and provide feedback will cover more angles and avoid common mistakes.
* Communication: The thoughts and ideas exchanged during the review process create alignment and fuel innovation.
* Documentation: Keeping a consistent, documented history of solution designs is challenging, mainly if many small changes contribute to the current state. Design review will produce documentation that another architect can easily understand and connect to the broader picture. This documentation will also last the test of time after its writers leave the company.
* Standardization: Having a common language and set of tools increases productivity and saves money and time. API standardization — designing APIs with common principles and adhering to a shared information model — is an excellent example in our API-driven economy.
* Raising the bar: Digital transformation and agile are hungry for results, with many obstacles in their way. Utilize the reviewer’s experience and feedback to push for better architecture solutions.
* Tracking technical debt: Sometimes, a workaround is required to achieve time to market. The design review process acts as a tracker for technical debt and ensures any debt pays off.
* Reusability: Different teams and departments can share solutions and apply standard guidelines and best practices. The design review process is a common denominator for this sharing.

## Wrap up

Design review has a clear value that far outweighs the overhead it introduces, much like code review in software releases. Organizations should consider it part of their governance model in conjunction with other tools and practices, including architecture review boards. The two processes can co-exist, with the design review focusing on constant calibration and course correction of the agile teams’ value streams.

The result should be a digital transformation that encompasses freedom, autonomy, reusability, cost-saving, and strong alignment. Design review is essential for the agile squads’ motivation and speed of innovation, while the architecture review board provides helpful guidelines and promotes collaboration.