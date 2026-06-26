---
title: "OpenAI"
date:  2017-10-01 17:23:11 +0300
---

[OpenAI's article](https://blog.openai.com/evolution-strategies/) on evolution strategies algorithm as a scalable alternative to reinforcement learning. For unsupervised learning, the gradients can be computed with finite differences instead of backpropagation. 

![openAI](/img/ES-RL.gif)

Apparently, using this algorithm in tensorflow which works with [denormal numbers](https://en.wikipedia.org/wiki/Denormal_number) off, [nonlinearity](https://blog.openai.com/nonlinear-computation-in-linear-networks/) arises from deep linear networks.

<img src="/img/non-linear-nn.png" alt="non-linear-nn" style="width: 400px;"/>
