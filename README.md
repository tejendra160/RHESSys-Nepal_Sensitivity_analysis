Parameters_value_range: 
The file "Parameters_range_value.csv" contains three columns: Parameter Name, Lower Limit, and Upper Limit.
The first column lists the names of the parameters to be considered for sensitivity analysis. Each name is formatted as (Def_file_name)_(Parameter_name).
The "Lower Limit" and "Upper Limit" columns define the boundary values for each parameter. These boundaries can be adjusted according to the requirements of your study area.

“Morris Global Sensitivity Analysis: Workflow, Parameter Constraints, and Elementary Effects”.

Definition_files_generations: 
This code sets up and generates input files for a Morris global sensitivity analysis workflow, automating sampling, parameter adjustment, and definition file creation. It reads parameter bounds from a CSV, applies Morris sampling to generate parameter sets, enforces model-specific parameter constraints, and creates configuration folders for each sample. The process ensures all input files are consistently and correctly adjusted for model runs, enabling systematic sensitivity analysis across many scenarios.

NUM_TRAJECTORIES: NUM_TRAJECTORIES refers to the number of sampling paths (trajectories) used in the Morris global sensitivity analysis method. Each trajectory is a series of model runs where one parameter is varied at a time along a prescribed path to estimate elementary effects at different points across the parameter space. More trajectories enhance the statistical power and reliability of sensitivity rankings. 
NUM_LEVELS: NUM_LEVELS defines the resolution of the parameter domain, i.e., how many distinct values each parameter is allowed to take during sampling. The input space for all parameters is discretized into NUM_LEVELS equally spaced steps between the lower and upper bounds, ensuring a uniform grid for analysis. Typical values range from 4 to 100, balancing computational complexity and sensitivity detection

Files: Unchanged vs. Files to Change
Unchanged files are those that remain constant and should not be edited, such as model executables, fixed code scripts, or reference datasets that support the analysis. Files to change include configuration files (such as 'Parameters_range_value.csv'), parameter definition scripts, or sample generators in which study-specific parameters, bounds, and constraints need to be set to fit the study area and research objectives. These files encode the sampling space, criteria, and analysis design.




Parameter Constraints
Many sensitivity analysis codes apply further constraints to parameter sets to maintain ecological or mathematical realism:
sum to one groups: Forces sets of parameters (such as fractional cover types) to sum to one, enforcing a simplex constraint on related variables.
multi1000_params: Refers to sets of parameters sampled thousands of times as part of high-dimensional screening or multi-model ensembles; these may also reflect sub-group or block-wise parameter variations.
cn_ranges: Enforces allowable ranges for carbon:nitrogen ratio parameters, which are key in biogeochemical and ecohydrological modeling.
day_int_params: Specifies allowable integer-based day parameters (e.g., timing of events) to avoid nonphysical values in temporal model components.

Parameters Mapping and Elementary Effects
In Morris OAT (One-At-a-Time) sampling, parameter mapping is accomplished by constructing a grid of possible parameter values using the specified NUM_LEVELS and then generating several random starting points (trajectories) across the grid. Along each trajectory, only one parameter is perturbed at a time, and the model is run repeatedly, recording the resulting changes in output.
These changes produce elementary effects (EEs), which measure the impact of incremental changes in individual parameters on model outputs. EEs are calculated for every parameter across every trajectory, summarized by their mean (often the mean absolute value, denoted μ⋆μ⋆) and standard deviation (σσ), providing a robust global ranking of parameter sensitivity. This process offers an efficient way to explore large, complex models with many inputs and is particularly valuable in ecohydrology for screening which parameters warrant calibration or further analysis

For further reading and reference, see Smith et al. (2022): “Guidance on evaluating parametric model uncertainty at decision-relevant scales” in Hydrol. Earth Syst. Sci., 26, 2519–2539, https://doi.org/10.5194/hess-26-2519-2022 


World_HeaderFiles_generations: 
This script creates multiple world header files for model simulations, organizing definitions and references needed for each run. It generates the files in a specified directory and customizes every file so that it points to the correct set of definition files according to its sequence number. The files serve as configuration templates, ensuring each simulation uses the appropriate parameter settings and land-cover, soil, climate, and vegetation definitions. The number of files is set by NUM_DEFS_FOLDERS, enabling batch setup for sensitivity analysis workflows.

Job_Files_generations: 
This code automates the creation of SLURM job scripts to run a hydrologic model (RHESSys) with different configuration files generated for sensitivity analysis. For each simulation setup, it writes a unique job script that sets memory, partition, runtime, model parameters (all currently set to fixed values), and input/output file paths. Scripts are saved in a dedicated jobs directory, streamlining submission to a computing cluster for batch processing of multiple model scenarios

Results_to_csv_file_generations: 
This code batch-processes simulation result files by extracting daily values for key variables (streamflow, LAI, evaporation, and transpiration) from each model output, specifically ensuring that dates are valid and that missing data is excluded. This approach is particularly useful for cases like the Nepal watershed, where model outputs may have multiple dates with missing data; the code automatically filters out these incomplete records to improve the robustness of the analysis. The code is flexible—if there are no missing data, it will still function correctly without altering complete datasets. By reading each result file, verifying data integrity, and saving the cleaned time series as new CSV files, this workflow automates pre-processing and quality control for sensitivity analysis outputs, streamlining subsequent analysis steps and enhancing overall reliability. 


Optimization_function_calculations: 
This code compares simulated streamflow output with observed runoff, calculates performance metrics (NSE, RMSE, R², logNSE) for each scenario, and saves the results in a summary file. It loads observed and simulated data within a set date range, matches data by day, filters out missing and invalid entries, and computes the metrics to assess how well the model replicates real watershed behavior. The final output allows for quick assessment and comparison of different model parameterizations in the Nepal watershed study.

Merging_metrics_with_parameters_values:
This code merges two summary tables—one with model performance metrics and one with parameter sets—by matching each scenario’s identifier (“defs”). It combines the evaluation results and corresponding input parameters for each simulation into a single CSV file, streamlining further analysis, ranking, or calibration. The result gives a comprehensive view of how every parameter set performed, making comparison and interpretation straightforward.

Sensitivity_Analysis(Morris): 
This code identifies which model parameters most strongly influence hydrologic simulation performance, using Morris global sensitivity analysis. Each "parameter" represents an input to the model (such as soil properties, vegetation characteristics, etc.) that can be varied to test its effect on model outputs. The Morris method systematically varies each parameter across its range and calculates summary metrics (μ*: mean absolute sensitivity, σ: variability, μ: mean effect) to rank their importance. In brief, Morris works by sampling parameter sets along specific paths (trajectories) and evaluating the "elementary effects"—the impact on output when one parameter changes at a time. This enables efficient screening of influential parameters, guiding model improvement and calibration by highlighting which variables have the biggest impact on results.
