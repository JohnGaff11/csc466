---
title: "Introduction to XSEDE"
teaching: 0
exercises: 0
questions:
- "What is XSEDE?"
objectives:
- "Know the history, organization, and impact of XSEDE"
- "Be able to access one XSEDE resource"
keypoints:
- " "
---

> ## XSEDE
> - Extreme Science and Engineering Discovery Enrironment 
>   - 2011 - now
>   - 17 institutions at the beginning. 
> - Successor of the TeraGrid program
>   - 2001 - 2011.  
>   - 9 sites (UIUC, SDSC, ANL, CalTech, PSC, ORNL, Purdue, Indiana, TACC)
> - Unit of computation: SU (Service Unit)
>   - 1 SU = 1 core-hour of computing time. 
{: .slide}

> ## What is currently part of XSEDE
> - Stampede2 (TACC): 18 PFLOPS
>   - 4,200 Intel Knights Landing (68 cores), 96GB memory
>   - 1,736 Intel Xeon Skylake (48 cores), 192GB memory. 
>   - 31 PB storage. 
> - Comet (SDSC): 2.76 PFLOPS
>   - 1944 Intel Haswell (24 cores), 320 GB memory. 
>   - 72 Intel Haswell (24 cores) with GPUs (36 K80, 36 P100), 128GB memory.
>   - 4 large memory nodes (1.5TB memory each), Intel CPU (64 cores). 
>   - 7.6 PB storage. 
> - Bridges (PSC) - we use this. 
>   - 752 Intel Haswell (28 cores), 128GB memory. 
>   - 42 Intel Broadwell (36-40 cores), 3TB memory.
>   - 4 Intel Broadwell (36-40 cores), 12 TB memory. 
>   - 16 Haswell nodes with dual K80 GPU, 32 Haswell nodes with dual P100 GPU. 
>   - 9 Intel Skylake nodes with 8 V100 GPUs per node, 128GB memory. 
>   - 1 DGX-2 (Intel Cascadelake) with 16 V100 GPU, 1.5 TB memory. 
> - Jetstream (IU)
>   - Cloud provider (baremetal) for academic research and education. 
>   - 640 nodes, totaling 7680 cores, 40TB memory, and 640 TB local storage. 
{: .slide}


> ## What will be part of XSEDE in the next five years?
> - Jetstream2 (IU) - $10M
>   - Predicted to be 8 PFLOPS. 
>   - $10M. 
> - Delta (NCSA) - $10M
>   - TBD  
> - Anvil (Purdue) - $10M
>   - 1,000 AMD Epyc Milan (128 cores), 256GB memory. 
>   - 32 AMD Epyc Milan (128 cores), 1TB memory
>   - 16 AMD Epyc Milan (128 cores), 256GB memory, 4 NVIDIA A100 GPUs. 
> - Neocortex (PSC) - $5M. 
>   - Special purpose supercomputer built to support AI research. 
>   - 2 Cerebras CS-1: largest chip ever built with 400,000 cores, 16GB on-chip memory, and 9.6PB/s memory bandwidth. 
>   - 1 HPE Superdome gateway: 24 TB memory, 204.6 local storage, 24 100GB network cards, 16 Infiniband network cards. 
> - Voyager (SDSC) - $5M. 
>   - TBD.
{: .slide}


> ## What do we have access to?
> - 50,000 SUs on Bridges RSM. 
> - 500GB total storage on Bridges Pylon. 
{: .slide}


> ## Hands-on 1: Login to PSC Bridges
>
> - Open a browser and go to `https://ondemand.bridges.psc.edu`.
> - Enter the `Bridges` user name and password from the confirmation email.  
> 
> <img src="../assets/figure/04-intro-xsede/01.png" alt="Lc" style="height:400px">
{: .slide}


> ## Hands-on 2: Login to PSC Bridges
>
> - Open a browser and go to `https://ondemand.bridges.psc.edu`.
> - Enter the `Bridges` user name and password from the confirmation email.  
> 
> <img src="../assets/figure/04-intro-xsede/01.png" alt="Login to ondemand" style="height:400px">
{: .slide}


> ## Hands-on 3: OnDemand navigation
>
> - Click on the `Files` and then `Home Directory`. 
> 
> <img src="../assets/figure/04-intro-xsede/02.png" alt="Go to Files " style="height:400px">
>
> - Top button row (`Go To`, `Open in Terminal`, `New File`, `New Dir`, `Upload`) operates
> at directory level. 
> - Next row (`View`, `Edit`, `Rename/Move`, `Copy`, `Paste`) operates on files. 
>
>  <img src="../assets/figure/04-intro-xsede/03.png" alt="Files and Directory" style="height:400px">
>
{: .slide}


> ## Hands-on 4: Trapezoid implementation with timing
>
> - Click `New Dir` and enter `openmp`. Hit `OK`. 
>
> <img src="../assets/figure/04-intro-xsede/04.png" alt="Create the openmp directory" style="height:100px">
>
> - Double click on `openmp`. 
> - Click `New File` and enter `trapezoid.c`. Hit `OK`. 
> - Select `trapezoid.c` and click `Edit`. 
> - Type/copy the code from the below into the editor windows. 
> - Click `Save` to save the file. 
>
> ~~~
> #include <omp.h>
> #include <stdio.h>
> #include <stdlib.h>
> #include <time.h>
>
> int main (int argc, char *argv[]) {
>   //init parameters and evaluators
>   double a = atof(argv[1]);
>   double b = atof(argv[2]);
>   int N = atoi(argv[3]);
>   int nthreads = atoi(argv[4]);
>   double partial_sum[nthreads];
>   int h = (int)((b - a) / nthreads);  
>   clock_t start, end;  
> 
>   start = clock();
>   #pragma omp parallel 
>   {
>     omp_set_thread_nums(nthreads);
>     int tid = omp_get_thread_num();
>     /* number of trapezoids per thread */
>     int partial_n = N / nthreads;
>     double delta = (b - a)/N;
>     double local_a = a + h * tid;
>     double local_b = local_a + delta;
>     for (int i = 0; i < partial_n; i++) {
>       partial_sum[tid] += (local_a * local_a + local_b * local_b) * delta / 2;
>       local_a = local_b;
>       local_b += delta;
>     }
>   } 
>   end = clock();
>   double sum = 0;
>   for (int i = 0; i < nthreads; i++) {
>     sum += partial_sum[i];
>   }
>   printf("The integral is: %.4f\n", sum);
>   printf("The run time is: %.4f\n", ((double) (end - start)) / CLOCKS_PER_SEC);
>   return 0;
> }
> ~~~
> {: .language-c}
> 
> <img src="../assets/figure/04-intro-xsede/05.png" alt="Editingg trapezoid.c" style="height:700px">
>
{: .slide}