---
title: "Mandelbrot"
date: 2022-10-10T20:36:47+03:00
description: "The Mandelbrot set in Rust and WebAssembly -- [demo](https://dikatrio.xyz/mandelbrot) -- [code](https://github.com/kkatrio/mandelbrot) --- Oct 2022"
resources:
- name: "featured-image"
  src: "mandelbrot.png"
---


I started this as an introduction to WebAssembly with Rust, but as I made progress I got fascinated by it. I liked the idea that I could do infinite zoom just by updating the coordinates of the complex plane. Recaclulation of the set is not a problem since it is all done in Rust. The flamegraph in the Firefox profiler helps to improve perfomance if you build in debug mode so that wasm functions are named. Zoom is _infinite_ up to the numerical precision of 64 bit floating point numbers, only then the colors are distored. It is awesome that npm and webkit are not necessary to deploy, and the final web files are just some js script and a 49Kb wasm binary. I had some fun with different color palettes and [plotting algorithms](https://en.wikipedia.org/wiki/Plotting_algorithms_for_the_Mandelbrot_set), but not too much. With a few more features, like zooming with the wheel or preview without calculation it can be even more spectatular!
