Please note this is a learning project, and does not constitute for established and verified scientific findings.

Description:
This repository contains an end-to-end neuroimaging data analysis pipeline using the Human Connectome Project (HCP) parcellated task-fMRI dataset provided by neuromatch academy Computational Neuroscience Summer School 2026. 

Dataset: The HCP dataset comprises task-based fMRI from a large sample of human subjects. The NMA-curated dataset includes time series data that has been preprocessed and spatially-downsampled by aggregating within 360 regions of interest. In their google colab notebook, NMA provides code for downloading the data and doing some basic visualisation and processing.
The data folder has the following organisation:
* hcp
    * regions.npy (information on the brain parcellation)
    * subjects_list.txt (list of subject IDs)
    * subjects (main data folder)
        * [subjectID] (subject-specific subfolder)
            * EXPERIMENT (one folder per experiment)
                * RUN (one folder per run)
                    * data.npy (the parcellated time series data)
                    * EVs (EVs folder)
                        * [ev1.txt] (one file per condition)
                        * [ev2.txt]
                        * Stats.txt (behavioural data [where available] - averaged per run)
                        * Sync.txt (ignore this file)

Experimental design: The category specific representation task and the working memory task are combined into a
single task paradigm. Participants were presented with blocks of trials that consisted of pictures
of places, tools, faces and body parts (non-mutilated parts of bodies with no “nudity”). Within
each run, the 4 different stimulus types were presented in separate blocks. Also, within each
run, ½ of the blocks use a 2-back working memory task and ½ use a 0-back working memory
task (as a working memory comparison). A 2.5 second cue indicates the task type (and target
for 0-back) at the start of the block. Each of the two runs contains 8 task blocks (10 trials of 2.5
seconds each, for 25 seconds) and 4 fixation blocks (15 seconds). On each trial, the stimulus is
presented for 2 seconds, followed by a 500 ms inter-task interval (ITI).
The data shared for NMA projects is a subset of the full HCP dataset with N = 100. The data have already been aggregated into ROIs from the Glasser parcellation. Each experiment was repeated twice in each subject, from Right to Left and Left to Right.
In this study, I focused on places stimulus category.

Reference for parcels used: https://raw.githubusercontent.com/ansteeg/NeuroMatchProject/master/atlas/hcp_parcels.csv

Workflow:
Loading the dataset per subject that includes event files for working memory for RL and LR runs
↓

Loading the NMA provided Glasser parcellation (N = 360, 180 per hs)
↓

Condition-level GLM separately for each participant 
↓

Estimate beta coefficients for the eight category-by-load conditions across 360 parcels.
↓

Average RL and LR beta estimates for each condition (participants * conditions * parcels)
↓

For participants * conditions * parcels * load, calculate βplace - mean β (faces, bodies, tools)
↓

2bk-0bk change in category selectivity for participants * conditions * parcels and t-test
↓

one-sample t-tests across participants to test whether the load-related selectivity change differs from zero at each parcel.
↓

FDR correction (0.05) across participants * conditions * parcels
↓

Surface mapping and visualisation for identifying parcels with place stimulus selectivity changes
↓

2bk-0bk for selected parcels for places and mean changes for body, faces and tools
↓ 

Testing participant level changes in each category specific β and applying FDR
↓

Plotting 0bk and 2bk β estimates

Results:
1. After FDR correction, RM_ANOVA was  p < 0.05
2. Parcel level results:  284 / 360 parcels showed a significant main effect of Stimulus Category (predominantly visual visual streams, FFC, and high-level ventral temporal regions). 272 / 360 parcels showed a significant main effect of Cognitive Load (frontoparietal control and default networks). 171 / 360 parcels demonstrated a significant interaction, showing that cognitive load dynamically influences category representation.
3. Selectivity Changes (2-Back - 0-Back): Dynamic changes were observed globally. For Places, 54 parcels changed their selectivity profile significantly between conditions. Specific target areas  `R_LO1`, `L_LO2`, `L_V3CD`, `L_PIT` demonstrated marked decreases in selectivity under higher loads, indicating a potential resource-bottleneck or suppression of sensory specificity. In contrast, target regions like `L_v23ab`, `L_10d`, and `R_23d` showed a load-dependent increase in place activation selectivity.

Figures:
1. Design matrix heat maps
2. Individual trajectory plots for raw 0bk vs 2bk beta values for target parcels per subject.
3. Cortical Projection Maps

Limitations:
1. The primary outcome was defined as the target-category beta minus the mean beta of the other three categories. Consequently, a change in category selectivity does not necessarily indicate an increase or decrease in the target category's response.
2. The primary analysis involved 1,440 category-by-parcel tests with FDR = 0.05. The correction does not eliminate the uncertainty in individual findings completely. 
3. RL and LR runs were averaged for the main analysis and may impact the analysis due to run specific differences.
4. The results are exploratory and need follow-up analysis for validation.
5. Gemini in Colab was used to write some code and debug errors.

References:
1. HCP Manual: https://www.humanconnectome.org/storage/app/media/documentation/s1200/HCP_S1200_Release_Reference_Manual.pdf
2. Neuromatch Academy colab notebook: https://colab.research.google.com/github/NeuromatchAcademy/course-content/blob/main/projects/fMRI/load_hcp_task_with_behaviour.ipynb#scrollTo=ydn-OI9qowuI
3. Neuromatch Project Guide: https://compneuro.neuromatch.io/projects/fMRI/README.html, https://www.youtube.com/watch?v=i30qePz_OGY
4. Barch, D. M., Burgess, G. C., Harms, M. P., Petersen, S. E., Schlaggar, B. L., Corbetta, M., … and Van Essen, D. C. (2013). Function in the human connectome: task-fMRI and individual differences in behavior. Neuroimage, 80: 169-189. doi: 10.1016/j.neuroimage.2013.05.033
5. Glasser et al. (2016). A multi-modal parcellation of human cerebral cortex. Nature, 536, 171–178. https://doi.org/10.1038/nature18933
6. Nilearn documentation: https://nilearn.github.io/stable/index.html
7. Handbook of Functional MRI (Poldrack et al)
8. Principles of fMRI: https://www.youtube.com/@principlesoffmri6920
9. Andy’s Brain Book on SPM: https://andysbrainbook.readthedocs.io/en/latest/index.html
