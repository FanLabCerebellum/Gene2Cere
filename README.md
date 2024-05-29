# Protocol to uncover the spatio-molecular profiles shape the imaging-derived property of the human cerebellum

# References
Wang Y, Wang Y, Wang H, et al. Spatio-molecular profiles shape the human cerebellar hierarchy along the sensorimotor-association axis. Cell Rep. 2024;43(2):113770. doi:10.1016/j.celrep.2024.113770

# Abstract
Extensive imaging-derived phenotypes (IDPs) of the human cerebellum have been explored but lack evidence from different modalities and scales to explore the spatio-molecular profiles that might be engaged in their formation. Here, we detail procedures from obtaining cerebellar transcriptomic samples from Allen Human Brain Atlas (AHBA), assignment of IDPs into samples, predictions of IDPs using gene expression, significance evaluation of genes, linking the gene to IDPs using gene set variation analysis (GSVA). 
![](https://github.com/FanLabCerebellum/Gene2Cere/blob/main/abstract.png)  

# Code Release
## Download
To download the version of the code that is last tested, you can clone this repository:


    git clone https://github.com/FanLabCerebellum/Gene2Cere.git
    cd script
## Example

We provide an example to show how to uncover the spatio-molecular profiles shape the imaging-derived property of the human cerebellum:


# Usage
## Step1: Set up environment

This protocol is compatible with Windows and Unix-based systems (Mac OSX and Linux) and requires Python version 3.10 and R version 4.2 or higher. Running this protocol in a separate Anaconda environment is advisable for optimal performance and to prevent potential conflicts with other scripts or libraries. Establishing a dedicated environment minimizes the risk of inadvertently causing conflicts with other installed software or different Python or R versions. It is important to note that the amount of disk space required may vary slightly depending on the operating system used due to differences in filesystem architecture.

### 1. Anaconda can be downloaded from its official website (https://www.anaconda.com). Follow the installation instructions tailored to your computer's specifications.
### 2. After installing Anaconda, restart any open terminals. Then, create a dedicated environment that includes Python (version 3.10) and R (version 4.2) by executing the command provided below:

    conda create --name Gene2Cere python=3.10 R=4.2 pip (10 min)

To set up your dedicated Anaconda environment, follow the step-by-step instructions provided by the prompts during the creation process. This ensures the environment is configured correctly. For comprehensive guidance on how to create, manage, and work with Anaconda environments, you can refer to the official Anaconda documentation (https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html). 

### 3. Activating the environment using the following command

    conda activate Gene2Cere
    
### 4. Install the Python and R dependency packages necessary to run the scripts.
Note: All Python dependency packages are listed in the key resources table. We have also provided a YML file named ‘‘Gene2Cere-env.yml’’ in the repository with the minimum environment to run the scripts.
  
## Step2: Input preparation
Select the path of your input file and obtain the gene expression matrix and the sample information matrix with IDPs would be generated in the example output file '/home/user/Gene2Cere/Output/' which could be changed directly by providing output_dir=yourpath or changed in the Gene2Cere.py
Note: For the input, either common neuroimaging scan formats (NIfTI - .nii, .nii.gz) can be used. It represents the voxel-wise IDPs of the human cerebellum. The example input was provided in the “Gene2Cere/Data/Input_example/.” The example path is provided as a relative path (e.g., ‘‘Input_example/Input_example.nii’, and the default father file is data_dir, which could be changed directly by providing data_dir=yourpath or changed in the Gene2Cere.py). 

    import sys

    sys.path.append('/home/user/Gene2Cere/Toolbox/')

    import Gene2Cere.Gene2Cere as G2C

    G2C.Step01_Input(input_file_name='Input_example/Input_example.nii')

## Step3: PLSR prediction the imaging features using gene expression

### 1. Evaluation of the optimal PLSR component and visualization 
Note: The num_repeat is the repeat time during the evaluation.

     all_best_comps, all_r = G2C.Step02_Comp_eval_run(num_repeat) 
     
     G2C.Step02_Comp_eval_visualization(all_best_comps, all_r)

### 2. Evaluation of the prediction performance 

Note: The n_components are the best component, as can be found in the figure output by G2C.Step02_Comp_eval_visualization (Figure 1), the cv_repeat is the repeat time of the cross-validation. 
     median_index, median_score = G2C.Step02_Model_eval(n_components,cv_repeat) 
     
     G2C.Step02_Brainsmash(input_file_name, permutation_times) 
     
     G2C.Step02_Brainsmash2FG(permutation_times)
     
     preds_name, score_name = G2C.Step02_Brainsmash2PLSR(n_components, median_index,permutation_times) 
     
     G2C.Step02_Model_eval_visulization(n_components,cv_repeat, median_index, median_score) 

# Step4: Definition of GCIsig

Prediction using the optimal PLSR model based on all samples. Then, we would get the coefficient of each gene. The coefficient represents the contribution index of each gene in predicting IDP, so we call it the gene contribution indicator (GCI). We evaluate the significance of GCI by refitting the PLSR model using the 10,000 surrogate maps. The gene set with significant GCI is named GCIsig. 

    G2C.Step03_GCIsig(n_components, illustrative, permutation_times)
# Step5: GSVA link GCIsig to IDPs 

## 1. Preparation of the mgt file

    R

    source("./Toolbox/GSVA/GSVA_prep.R")

## 2. Run GSVA and visualization

    source("./Script/Step04_GSVA.R", encoding = "UTF-8")

# Expected outcomes
1. A csv file ` (Step01_Gene_expression.csv) `  containing the gene expression data for all cerebellar samples

2. A csv file ` (Step01_Sample_info.csv) `  that includes information for all cerebellar samples from AHBA, along with the IDP values for each sample

3. An npy file ` (Step02_Comp_eval_run_100x10cv_all_best_comps.npy) `  providing information on the optimal component number from the initial 9-fold training of the nested 10-fold cross-validation

4. An npy file ` (Step02_Comp_eval_run_100x10cv_all_r.npy) `  containing the correlation between observed and predicted IDP values for the initial 1-fold testing samples, based on the optimal component number within the initial 9-fold training samples

5. A png file ` (Step02_Comp_eval_visualization.png) `  illustrating the optimal component number from the nested 10-fold cross-validation

6. A csv file ` (Step02_PLSR_101x10cv_preds.csv) `  detailing the predictions from 101 repetitions of 10-fold cross-validation

7. A png file ` (Step02_PLSR_101x10cv_r2median_20.png) `  showing the prediction performance

8. A csv file ` (Step03_GCIsig.csv) `  containing the GCI values and their significance after applying BrainSMASH with multiple comparison test

9. A folder named ` "BrainSmash" `  containing data constructed during the BrainSMASH analysis

10. A folder named ` "GSVA" `  containing data generated during GSVA, including example GSVA results








