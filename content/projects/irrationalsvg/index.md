---
title: "Irrational svg"
date: 2022-08-15T20:36:47+03:00
description: "Create svg from irrational number digits in Rust --- Aug 2022"
resources:
- name: "featured-image"
  src: "pisvg.png"
---

I created an svg out of 1 million digits of pi. The direction of the path depends on the digit: If the digit is 1 turn 36 degrees, if it is 2 turn 72 degrees, and so on. I took the idea from [numberphile](https://www.youtube.com/watch?v=tkC1HHuuk7c). The [result](/img/pi.svg) is about 20Mb.
