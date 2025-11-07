# 598APE-HW3

This repository contains code for homework 3 of 598APE.

This assignment is relatively simple in comparison to HW1 and HW2 to ensure you have enough time to work on the course project.

In particular, this repository implements a 2D Ising model Monte Carlo simulator (with Metropolis–Hastings algorithm) on an L×L lattice with periodic boundary conditions.

# Branch Information

The `main` branch represents our fastest solution and optimized code. In this project, we executed 3 optimizations as detailed below:

- MHS Work Reduction: In the metropolisHastingsStep(), we noticed that we calculate the total energy change each time we flip a cell in the lattice in an O(L^2) run but we don't need to go through a full array for that we can simply calculate the difference in energy by looking at the changed cell's neighbors. This code is represented in the `metropolis-hastings-step-opt` branch.

- Compiler Parallelization: In the calculateTotalEnergy() and calculateMagnetization() functions, we noticed that the loops simply reduce and do calculations onto a single global variable across the whole lattice which is a highly parallelizable task. This code is represented in the `compiler-parallelism branch.

- Precomputed Acceptance Probabilities: In the metropolisHastingsStep(), we noticed that the exp() call simply calculates the acceptance probability based on the difference in energy for each step. If you examine this further, you realize that this could only output 1 of 5 values (-8J, -4J, 0, 4J, 8J) which could easily be precomputed and indexed based on the difference in energy. The difference in energy is easily calculated in Optimization 1 and can be used to index through the precomputed values which means optimization 1 is a prerequisite for this. This code is represented in the `metropolis-hastings-step-opt-and-precompute-exp-values` branch.

NOTE: We tried combining all three optimizations which did not yield better results than `metropolis-hastings-step-opt-and-precompute-exp-values`. For completeness, the combination of the 3 is outlined in the `mhs-opt-precomputed-parallelized` branch.

NOTE: The `metropolis-hastings-and-compiler-parallelism` branch represents the combination of Optimizations 1 and 2. 

To compile the program run:
```bash
make -j
```

To clean existing build artifacts run:
```bash
make clean
```

This program assumes the following are installed on your machine:
* A working C compiler (gcc is assumed in the Makefile)
* make
* ImageMagick `convert` for PNG output

Usage (after compiling):
```bash
./main.exe <L> <temperature> <steps>
```

In particular, consider speeding up simple run like the following (which runs ~5 seconds on my local laptop under the default setup):
```bash
./main.exe 100 2.269 100000
```

Exact bitwise reproducibility is not required; sanity checks on energy/magnetization bounds must pass. In addition, at the critical temperature (T ≈ 2.269) the energy per spin should approach -1.414 in equilibrium.