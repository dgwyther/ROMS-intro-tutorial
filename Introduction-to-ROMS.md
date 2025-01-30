# Introduction and Refresher for ROMS

This tutorial serves as an introduction and refresher for using the **Regional Ocean Modeling System (ROMS)**. The tutorial is structured to cover the basics of obtaining and running ROMS, exploring its folder structure, editing and compiling the code, and working with output files.

Total duration: ~1.5 hours

---

## 1. Online resources

The ROMS Wiki is an essential resource for understanding and troubleshooting:
- **URL**: [ROMS Wiki](https://www.myroms.org/wiki)
- Key pages to review:
  - **Getting Started**: Step-by-step instructions for downloading, compiling, and running ROMS for the first time.
  - **Compiling and Running**: Detailed guides on the compilation process and how to set up and execute ROMS simulations.
  - **UNSW2008 tutorial**: This tutorial is the best (only?) step-by-step to get an example ROMS simulation running [UNSW2008 tutorial](https://www.myroms.org/wiki/ROMS_UNSW2008)
  - **Input and Output Files**: Comprehensive descriptions of the configuration files, output formats, and diagnostic tools. Note that some of the pages aren't finished, and probably won't ever be..
  - **S-coordinate**: This is what the vertical coordinate looks like. It shows some results of different parameter settings.
  - **Numerical solution**: Contains a very useful plot of the u- v- and rho-point grid.
  - **Boundary conditions**: Different BC settings.
  - **FAQ**: Solutions to common issues during setup and runtime. Slightly useful..
 
  
The ROMS forum is useful for giving clues to problems, but is not always useful. Not very active, sometimes hostile, many red herrings.
- **URL**: [ROMS forums](https://www.myroms.org/forum/index.php)
- I find it is best to just do a google search with `myroms` in the search expression, e.g. `myroms blow up`.

Kate Hedstrom's ROMS manual is the de facto manual for ROMS.
- **URL**: [ROMS manual from Kate Hedstrom](https://github.com/kshedstrom/roms_manual/blob/master/roms_manual.pdf)
- Very detailed, lots of information.

Deepak Cherian's 'Living with ROMS'
- **URL**: [Living with ROMS](https://cherian.net/static/living-with-roms.pdf)
- Not an official document, but still very nice.
- It is more of a guidebook with ears of ROMS wisdom put into it. I suspect it was written out of frustration of weird ROMS things, and so it has lots of practical 'set this to this in order for it to work'.

Kevin Rosa's ROMS notes
- **URL**: [Kevin Rosa's ROMS notes](https://gist.github.com/kevinrosa/4a51c4040b5a82d665b48a558b980db6)
- Another collection of random ROMS tips.
  
---

## 2. How to Get ROMS

### Cloning the Repository

1. **Clone the ROMS repository using Git**:

   ```bash
   git clone git@github.com:myroms/roms.git ROMS_example
   cd ROMS_example
   ```

   or for the ice shelf version, we could get the version from Ben's github (`bkgf` at github):

   ```bash
   git clone git@github.com:bkgf/ROMSIceShelf.git ROMS_example
   cd ROMS_example
   ```

2. **Check the current repository status**:

   ```bash
   git status
   ```

   This shows if there are any uncommitted changes in your working directory.

3. **Inspect changes (if any)**:

   ```bash
   git diff
   ```

   Use this to view differences between your files and the repository.

   Let's try modifying a file to see how it looks with git:


   ```bash
   echo "testing" >> testfile
   cat testfile
   ```   
   Now what does git say?
   ```bash
   git status
   ```
   
  ---

## 3. ROMS Folder Structure

The ROMS repository is organized as follows:

- **ROMS/**: Contains the main source code for the ocean model.
- **Functionals/**: Houses libraries and auxiliary functions used for (mainly) setting up *analytic* forcing.
- **External/**: Contains input files, test cases, and example setups for users to explore.
- **Include/**: Contains configuration header files (e.g., `cppdefs.h`).
- **Nonlinear/**: Code modules specifically for nonlinear simulations.
- **Utility/**: Pre- and post-processing tools, such as scripts for handling forcing files.

Explore these folders to understand their purpose. For instance:

```bash
ls -R ROMS
```

---

## 4. Makefile and Compilers

### Understanding the Makefile

1. **Examine the `Makefile`**:

   ```bash
   less Makefile
   ```

   Key sections:
   - Which application? Set this to decide which cppdef.h to use.
   - Flags for debugging and optimization.
   - Parallel or serial?
   - Which compiler?
   - Paths to required libraries - need to set paths to netcdf installs.

### Setting the Compiler & checking netcdf versions

Ensure you are using the appropriate compiler for your system. For example:

```bash
module avail "intel"
module avail "gcc"
```

Now look for which netcdf versions:
```bash
module avail "netcdf"
```

Both the compiler and netcdf need to be loaded in order to compile and run.
---

## 5. Compile and Run ROMS

1. **Build ROMS**:

   Ensure your `ocean.in` and configuration files are ready. Then, compile ROMS:

   ```bash
   make
   ```

   To speed up compilation, we can parallelise the compilation:
   
   ```bash
   make -j 8
   ```
   Will compile across 8 nodes (faster).

   We might also want to do a clean compile, that is, by deleting the previous compiled version and starting from scratch:
   ```bash
   make -j 8 -clean
   ```

   If there are errors, refer to the log output and cross-check your configuration files.

1. **Run the model**:
   In serial:
   ```bash
   ./romsS < path/to/the/*.in
   ```

   In parallel:
   ```bash
   mpirun romsM path/to/the/*.in
   ```

   Monitor the terminal output to confirm the simulation is running as expected.

---

## 6. Editing Code

### Using `vi` or `vim`

Basic commands for `vi`/`vim`:

- **Open a file**:
  ```bash
  vi filename.F
  ```
- **Enter Insert Mode**: Press `i`.
- **Save and Exit**: Press `Esc`, then type `:wq`. The `wq` stands for 'write and quit'
- **Exit Without Saving**: Press `Esc`, then type `:q!`. The 'q!' stands for 'quit damnit, and don''t save!'
- **Search for a string**: Press `/`, type the string, and press `Enter`.
- **Jump to a line number"": Press `:X`, where `X` is the line number and press enter.

Practice by opening and editing `cppdefs.h` or a `.in` file.

---

## 7. Working with Log and Output Files

### Log Files

1. **Inspect the log file**:

   ```bash
   vi ocean.log
   ```

   Look for error messages or warnings to identify issues in the simulation.
   Other valuable information:
   - What is the model computed `rx0` and `rx1`.
   - How well obeyed is the CFL criterion?
   - What model forcing (boundary, initial, etc) is being loaded?
   - What settings did we define?

### Restart and History Files

2. **Visualize the restart file (`rst`) with `ncview`**:

   ```bash
   ncview ocean_rst.nc
   ```

3. **Analyze the history file (`his`)**:

   ```bash
   ncview ocean_his.nc
   ```

   Use `ncdump` to inspect the file’s metadata and variables:

   ```bash
   ncdump -h ocean_his.nc
   ```

---

## 8. Realistic Setup

### Configuration Files

1. **Review `.in` files**:

   ```bash
   vi ocean.in
   ```

   Pay attention to sections like grid parameters, forcing inputs, and time-stepping configurations.

2. **Explore header (`*.h`) files**:

   ```bash
   vi cppdefs.h
   ```

   Enable or disable specific features by uncommenting the appropriate macros.

### Time-Stepping Parameters

Time-stepping controls in `ocean.in`:

- `DT`: Timestep size in seconds.
- `NTIMES`: Total number of timesteps.
- `NDTFAST`: Number of fast-mode sub-timesteps. The fast time step is calculated as `DT/NDTFAST`.
- `NHIS`: How many timesteps between writing of history to output history file.
- `NDEFHIS`: This is the total number of snapshot histories to save to the history file, before a new history file is defined.

Grid Smoothness Parameters: rx0 and rx1

- rx0: This parameter measures the steepness of the grid bathymetry. It is a non-dimensional metric used to quantify the topographic slope in the model grid. Values below 0.7~0.8 are generally recommended for numerical stability.

- rx1: This parameter quantifies the local bathymetry variation between adjacent grid cells. Lower values (typically less than 5) indicate smoother transitions and help maintain numerical accuracy, but I've run it with VERY much larger values (up to 200-300).

---

## 9. Forcing Files

Forcing files provide external conditions like wind, tides, and heat fluxes. Examples include `forcing.nc`.

1. **Inspect a forcing file**:

   ```bash
   ncdump -h forcing.nc
   ```

2. Key variables to examine:
   - `sustr`, `svstr`: Surface stresses in x and y directions.
   - `shflux`: Surface heat flux.
   - `swflux`: Surface freshwater flux.

To visualize forcing data:

```bash
ncview forcing.nc
```

---

## Summary

By the end of this tutorial, you should:

1. Understand how to navigate ROMS documentation and folder structure.
2. Be able to clone, compile, and run ROMS.
3. Know how to edit code and review output files.
4. Gain familiarity with realistic setups and forcing files.

Feel free to refer back to the commands and structure as needed!

