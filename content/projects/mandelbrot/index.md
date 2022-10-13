---
title: "Mandelbrot"
date: 2022-10-10T20:36:47+03:00
description: "The Mandelbrot set in Rust and WebAssembly -- [demo](https://dikatrio.xyz/mandelbrot) --- Oct 2022"
resources:
- name: "featured-image"
  src: "mandelbrot.png"
---


I started this as an introduction to WebAssembly with Rust, but as I made progress I got fascinated by it. As some point it hit me that I can simulate infinite zoom: I could update the coordinates of the complex plane and recalculate it fast since all heavy caclulations are done in Rust. JS and its canvas api are just the user interface. The flamegraph in the Firefox profiler helps to improve perfomance if you build in debug mode so that wasm functions are named. The result is that zoom is infinite up to the numerical precision of 64 bit floating point numbers, only then the colors are distored. It is awesome that npm and webkit are not necessary to deploy, and the final web files are just some js script and a 49Kb wasm binary. I had some fun with different color palettes and [plotting algorithms](https://en.wikipedia.org/wiki/Plotting_algorithms_for_the_Mandelbrot_set), but not too much. It can be even more spectatular!
