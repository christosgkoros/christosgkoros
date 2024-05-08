+++ 
title = "Werner Vogel's API rules"
date = "2024-05-07T17:52:54.961Z"
tags = [ "APIs","api-design","architecture" ]
draft = "false"
+++
1. APIs are Forever
When a social media post graces the internet, it leaves a permanent impression on web users thereafter — that’s to say, it remains visible and “on the record” until the end of days. A similar thing happens with AWS’ APIs, though those outcomes are more intentional.

When AWS introduces an API, Vogels believes that this API must remain ever-present and largely unchanged. Once companies fundamentally change or remove a longstanding API from general availability, business customers who’ve built atop it will suffer. These breaking changes can interrupt daily operations and impact end-users. Pulling the rug out from underneath technical teams can undermine brand trust and loyalty.

There are both business and somewhat altruistic reasons behind preserving an API over time. However, what if you absolutely need to alter or develop your APIs to satisfy new frameworks and feature requests?

2. Never Break Backwards Compatibility
Many customers will want to leverage older API versions. Accordingly, calls from older API versions shouldn’t encounter problems. Leave the infrastructure in place to handle legacy versions. At worst, offer essential resources that ease upgrades or transitions to newer versions.

In fact, this philosophy mirrors those of other industry behemoths like Google. While Vogels and Amazon have made promises around their own APIs, Google’s own API Improvement Proposals documentation outlines best practices. Again, guidelines like these highlight just how crucial API longevity is to customer success.

3. Work Backwards from Customer Use Cases
Instead of letting engineers determine what an API should do, Vogels states that customers should be the driving force. Your users will regularly share feedback and make requests based on their collective needs, which naturally evolve over time. When a product changes too much, you risk alienating established users. Accordingly, offering a product that’s as simple as possible while providing core functionality will eliminate learning curves associated with continuous development.

“Use your customers, work backwards from their use cases, and then come up with a minimal and simplest form of an API that you can actually offer them,” says Vogels. That’s not to say that engineers are putting in minimal effort. Instead, you should aim to design user-friendly APIs that don’t overwhelm through complexity. Assign one call to one function, and expand your API to support other specialized calls. This prevents over-engineering and means that engineers don’t have to package everything together haphazardly.

4. Create APIs with Explicit and Well-Documented Failure Modes
Life’s good when an API works well, but what happens when it doesn’t? When a core function must execute despite roadblocks, APIs often leverage failure modes to prevent errors. CRUD APIs should be able to support a number of key operations when conditions aren’t optimal. It’s vital to document what happens when your API fails, and how exactly it does this. That way, API responses won’t be unpredictable, and developer users won’t be frustrated. Make these URI structures readily available for troubleshooting purposes. Input parameters and security concerns might also drive these inclusions.

5. Create Self-Describing APIs that Serve Clear Purposes
Simply put, developers shouldn’t be puzzled when inspecting your API. What the API actually does should be immediately apparent. Additionally, what can the API help your customers accomplish? It’s important to remember that APIs are productivity tools — ones that power user engagement, entertainment, business, or otherwise. Knowing and communicating which “lane” your API resides in will only boost its marketability.

6. Avoid Leaking Implementation Details at all Costs
APIs have immense business value, and the innovations behind them should be closely guarded secrets — especially as the microservices market grows more saturated. Keeping your implementations close to the vest is key. Otherwise, your customers who learn these details may become dependent on them.

Vogels warns, “they will start to figure out how you’ve actually implemented this under the covers, and will start to actually use that knowledge.” This also isn’t optimal because you’ll be unable to change your implementation. Customers can become reliant on “non-functional information,” which can disrupt your development pipeline, a principle we’ve covered on the blog known as Hyrum’s Law.


