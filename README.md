# VSB-Powerlines-Fault-Prediction

Please refer to the ipython notebook or the pdf file for the code, visualisations and results.
The work in this repository refers to the output of a kaggle data competition detailed below.

# Challenge Statement
Medium voltage overhead power lines run for hundreds of miles to supply power to cities. These great distances make it expensive to manually inspect the lines for damage that doesn't immediately lead to a power outage, such as a tree branch hitting the line or a flaw in the insulator. These modes of damage lead to a phenomenon known as partial discharge — an electrical discharge which does not bridge the electrodes between an insulation system completely. Partial discharges slowly damage the power line, so left unrepaired they will eventually lead to a power outage or start a fire.

Your challenge is to detect partial discharge patterns in signals acquired from these power lines with a new meter designed at the ENET Centre at VŠB. Effective classifiers using this data will make it possible to continuously monitor power lines for faults.

ENET Centre researches and develops renewable energy resources with the goal of reducing or eliminating harmful environmental impacts. Their efforts focus on developing technology solutions around transportation and processing of energy raw materials.

By developing a solution to detect partial discharge you’ll help reduce maintenance costs, and prevent power outages.

# Data Exploration and Preparation
# Data Description
Faults in electric transmission lines can lead to a destructive phenomenon called partial discharge. If left alone, partial discharges can damage equipment to the point that it stops functioning entirely. Your challenge is to detect partial discharges so that repairs can be made before any lasting harm occurs.

Each signal contains 800,000 measurements of a power line's voltage, taken over 20 milliseconds. As the underlying electric grid operates at 50 Hz, this means each signal covers a single complete grid cycle. The grid itself operates on a 3-phase power scheme, and all three phases are measured simultaneously.

# File Descriptions
metadata_[train/test].csv

id_measurement: the ID code for a trio of signals recorded at the same time.
signal_id: the foreign key for the signal data. Each signal ID is unique across both train and test, so the first ID in train is '0' but the first ID in test is '8712'.
phase: the phase ID code within the signal trio. The phases may or may not all be impacted by a fault on the line.
target: 0 if the power line is undamaged, 1 if there is a fault.
[train/test].parquet : The signal data. Each column contains one signal; 800,000 int8 measurements as exported with pyarrow.parquet version 0.11. Please note that this is different than our usual data orientation of one row per observation; the switch makes it possible loading a subset of the signals efficiently. If you haven't worked with Apache Parquet before, please refer to either the Python data loading starter kernel.

sample_submission.csv: a valid sample submission.

# Data Background Information
As you may notice, the signal data comes from the real environment, not a lab, and they contain a lot of background noise. These signals are measured by our patented device with lower sampling rate (cost efficiency purpose) therefore I do not recommend to use any other publicly available dataset containing partial discharge patterns (PD patterns). - also we deployed the metering devices on more than 20 different locations. This implies that the spectrum of noise and quality of PD's are so different from each other, that the correct and robust classification is a still ongoing problem (the main motivation of this competition). The comparison and broadening of our view is also considered as beneficial and necessary in our research.

PD pattern is therefore the main star tonight and there is a lot of literature about this phenomenon. I would recommend to read some papers, during my dissertation I tried a lot of different feature extraction models, but those based on fundamentals worked the best. In general the imbalanced dataset is very natural because PD pattern implies some degradation or damage of the observed system which is happening (fortunately) less often than the states when the system is operating correctly.

In our case, the measurements on the medium voltage overhead lines, PD pattern may look like this (pd_pattern.png). But because of a lot of various noise interference (overhead lines work as a huge antena grabbing all signals around), a lot of interpolated patterns may look similar (see samples.png).

To use any kind of wavelet transformation is very reasonable, butterworth filter was helpful for me to suppress the sine shape, DWT to obtain its close approximation - sometimes it is disrupted, and denoising with feature extractions are the alchemy of this competition.
