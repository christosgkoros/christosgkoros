+++ 
title = "Contract testing"
date = "2024-05-05T15:57:43.601Z"
tags = [ "APIs","testing","contract-testing" ]
draft = "false"
+++
I had a conversation recently involving contract testing between partners. The provider provides the contract during their ci/cd process before the deployment. The consumer will validate it and if something fails, the change is rejected and the deployment never occurs. The concept of consumers signing off and blocking deployments is very interesting as it empowers and handles the control to the consumer.


