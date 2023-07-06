# An open tool for generating synthetic datasets for validating and testing control-oriented neural networks for building dynamics prediction

## Introduction
![alt text](https://github.com/gaurav306/NatureSD_paper_temporary/blob/master/README_utils/new_wf1.jpg)
Applying model-based predictive control in buildings requires a control-oriented model capable of learning how various control actions influence building dynamics, such as indoor air temperature and energy use. However, there is currently a shortage of empirical or synthetic datasets with the appropriate features, variability, quality and volume to properly benchmark these control-oriented models. Addressing this need, a flexible, open-source, Python-based tool capable of generating synthetic building operation data using EnergyPlus as the main building energy simulation engine is introduced. The uniqueness of this tool lies in its capability to automate multiple aspects of the simulation process, guided by user inputs drawn from a text-based configuration file. It generates various kinds of unique random signals for control inputs, performs co-simulation to create unique occupancy schedules, and acquires weather data. Additionally, it simplifies the typically tedious and complex task of configuring EnergyPlus files with all user inputs. Unlike other synthetic datasets for building operations, this tool offers a user-friendly generator that selectively creates data based on user inputs, preventing overwhelming data overproduction.

## High-level workflow
The codebase for the data generator comprises three main folders: *‘Dataset_output/’*, *‘Offline_weather_file_input/’*, and *‘src/*’. The *‘Dataset_output/’* folder contains the final time series data generated, which is further described in the 'Data availability' section. The ‘src/’ folder is dedicated to all the scripts and auxiliary files required by the data generator. As discussed before this tool enables the user to create customized datasets for a variety of settings. These options are summarized in the ‘Config_input.yaml’ configuration file, in which users can modify various variables according to the instructions provided in the comments. This configuration file is critical to the tool's functionality, as all the scripts depend on its inputs. 
The *‘Run_main.py’* script triggers the data creation pipeline, depicted in Figure below, which provides a high-level overview of the tool's workflow. This script transforms the text data from ‘Config_input.yaml’ into a dictionary that serves as the settings for the entire pipeline. The main script calls *‘src/OB_GEN/OB_generator.py’* to generate stochastic occupancy schedules depending on user settings. It then calls ‘src/Signal_generator.py’ to generate different random signals used as schedules in EnergyPlus simulation. Finally, it calls *‘src/EP_Generator.py’*, which handles the complex task of manipulating and editing the EnergyPlus input data file (IDF). It also downloads weather files, creates the necessary directories, and finally initiates the EnergyPlus program to perform the final simulations. The components of this pipeline will be discussed in greater detail in the subsequent sub-sections.

![High-level workflow of the data generator](https://github.com/gaurav306/NatureSD_paper_temporary/blob/master/README_utils/new_wf2.jpg "High-level workflow of the data generator")

## File structure of the codebase
![File structure of the codebase for data generator](https://github.com/gaurav306/NatureSD_paper_temporary/blob/master/README_utils/new_filestruc.jpg "File structure of the codebase for data generator")
## Output dataset structure
Time-series data. The time-series dataset generated by the EnergyPlus simulation engine is provided in CSV file format. The first column is labelled *"Data/Time"*, representing the date and time of each record, while the remaining columns correspond to different variables and measurements related to building dynamics. The dataset covers a full year of simulation, with 15-minute time steps, resulting in a file size ranging from 25 MBs to 27 MBs. All variables in the dataset are reported in SI units, with the exception of a few dimensionless variables (see Table 1). The generated time series dataset can be conveniently analysed using any comma-delimited viewer software or programming tools.

The file structure of the dataset. The dataset generator produces a structured set of output files, input files, and weather data to ensure that simulations are repeatable and that all CSV dataset files are easily accessible through basic scripts. Figure above illustrates the file structure and naming conventions used in the *‘Dataset_output/’* folder. The folder structure includes a unique identifier *(%city_name%)* based on the name of the weather files used by the user, a user-chosen prefix identifier *(%prefix_identifier%)* to categorize different simulation runs, and an identifier for simulation timestep intervals chosen by the user *(%interval%)*. For instance, if a simulation is conducted with an interval of 15 minutes, HVAC mode as 1 and with no random window openings, the %prefix_identifier% could be *'HVAC-1_Windows-0'* and *%interval%* would be 15. The folder structure also includes a simulation index *(%n%)* for the user-chosen setting *‘number_of_stochastic_EP_cases_to_generate'*, where *%n%* represents the index number for the same type of simulation run with the same %prefix_identifier%. 
It is important to note that all the simulations in the folder with varying *%n%* will have the different randomness, including input signals for HVAC mode, HVAC setpoints and window opening signals and stochastic occupancy for the five zones. Whereas all the simulations in the folder with varying *%prefix_identifier%* will have different simulations settings like simulation timestep and enable/disable use of random HVAC mode/HVAC setpoints/window opening signals. The final time-series dataset output from EnergyPlus is *%City_name%_%n%_output.csv*. The simulation can be re-run again from the *'temp'* folder where all the required input files for EnergyPlus are copied.

![Final Time-series dataset file summary](https://github.com/gaurav306/NatureSD_paper_temporary/blob/master/README_utils/data_headers.jpg "Final Time-series dataset file summary")



