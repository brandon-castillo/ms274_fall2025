# Davis Strait

## Project Description
In this project, I will investigate the heat transport in the Davis Strait at two different resolution, coarse and fine. In particular, I will investigate the following science question:

*How does the change capturing higher resolution features in the Davis Strait impact heat transport?*

To investigate this question, I will construct 2 models spanning the Davis Strait at differing resolutions. I will run my experiment during the year 1997. The reason to use 1997 is this was the year before sea ice melt dramatically increased, thus being a more "normal" year of Arctic data to use and compare against my model results. 

For initial conditions, I will use the state of the ECCO Version 4 Model in January of 1997 and 1998. Similarly, I will construct boundary and external forcing conditions for this model from the ECCO Version 4 model output. To analyze the results, I will particularly focus on Theta, Sea Ice, and heat flux charts, comparing these results through a time series going through a year of data. I will also create a movie capturing how these variables change over time between the resolution levels. 

## Reproducing Model Results
The following steps outline how to construct the model files, configure and run the model, and assess the model results. As I am running two different model resolution levels, the same steps will need to be repeated between the low resolution and high resolution folders.

### Step 1: Create the Model Files
Several input files need to be created to run the model. Generate the following list of files using the notebooks indicated in paratheses:
- Model Grid (notebooks/Creating the Model Grid.ipynb)
- Bathymetry (notebooks/Creating the Bathymetry.ipynb)
- Initial Conditions (notebooks/Creating the Initial Conditions.ipynb)
- External Forcing Conditions (notebooks/Creating the External Forcing Conditions.ipynb)
- Boundary Conditions (notebooks/Creating the Boundary Conditions.ipynb)
The model files should be placed into the  `input` directory.

### Step 2: Add files to the computing cluster
Once the input files have been created, the model files can be transferred to the computing cluster. Begin by cloning a copy of [MITgcm](https://github.com/MITgcm/MITgcm) into your scratch directory and make a folder for the configuration, .e.g.
```
mkdir MITgcm/configurations/davis_strait/low_res
```
Then, use the `scp` command to send the `code`, `input`, and `namelist` directories to your configuration directory. 

### Step 3: Compile the model
Once all of the files are on the computing cluster, the model can be compiled. Make a `build` directory in the configuration directory and run the following lines:
```
../../../../tools/genmake2 -of ../../../../tools/build_options/darwin_amd64_gfortran -mods ../code -mpi
make depend
make
```

### Step 4.1: running the low_res model 
After the compilation is complete, run the model with the wind. Move to the run directory, link everything from `input` and `namelist`, and the submit the job script:
```
sbatch bcastillo.slm
```

### Step 4.2: Run the high_res model 
Steps for high_res are similar, running a similar sbatch script, but due to the resolution level it will require an initialization period with a very low timestep value. For initialization, in the data file:
```
deltaT=5.,
nTimeSteps = 17280,
```
This will get the first day of data complete. after this, set the niter0 to the latest pickup and modify data file to be:
```
 deltaT=60.,
 nTimeSteps = 1036800,
 pChkptFreq=172800.,
```

### Step 5: Analyze the Results
There are two notebooks provided for analysis:
1. Compare Observations 

   Located in the low_res folder. This notebook provides most of the analysis, including looking at timestep data for theta, heat transport, sea ice, generating movies from these files, and comparing theta results against Davis Mooring observation data. There is also a heat transport comparison against high res model results.
   
2. high_res_data_analysis
   
   This notebook has is essentially some of the timestep analysis and movie creation code from above, except applied to high resolution model results. 
