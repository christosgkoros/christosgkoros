+++ 
title = "idempotency"
date = "2024-05-07T18:08:16.323Z"
tags = [ "apis","api-design" ]
draft = "true"
+++
An idempotent POST is a concept, useful in payment API scenarios. The API provider wants to ensure that if an API call to make a payment fails during execution – such as in timeout scenarios where no discernable HTTP response is returned to the client – subsequent attempts do not result in a duplicate payment instruction submitted. Idempotency is achieved by requiring the client to submit the same instruction with an identifier that signals to the API provider that they might have seen this instruction before.


