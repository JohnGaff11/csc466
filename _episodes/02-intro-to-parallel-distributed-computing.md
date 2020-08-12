---
title: "Introduction to paralel and distributed computing"
teaching: 0
exercises: 0
questions:
- "What is parallel computing?"
objectives:
- " "
keypoints:
- " "
---

> ## How do we measure speed in computation?
> - Floating-Point Operation per Second (FLOPS)
>   - Count number of floating-point calculations (arithmetic operations) per second.
> - Not MIPS (millions of instructions per second) as MIPS also count non-arithmetic
> operations such as data movement or condition.
> $$
>   FLOPS = sockets \times \frac{cores}{socket} \times \frac{cycles}{second} \times \frac{FLOPS}{cycle}
> $$
> - MFLOPS (megaFLOPS) = 1,000,000 FLOPS
> - GGLOPS (gigaFLOPS) = 1,000,000,000 FLOPS
>
{: .slide}



{% include links.md %}




