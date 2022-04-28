# Study of attitude determination accuracy based on STT and Gyro spec

## orbit data analysis
- main.py
  - Main file for attitude estimation by using orbit data. Please execute this file.
  - Log data are saved in the datetime named folder under the 'log/' directory.
- attitude_estimation.py
  - File for AttitudeEstimation class which includes filtering process.
  - Setting parameters for Kalman Filter are written in initialize section, please revise them if the filtering performance is not good. In addition, "Adaptive Kalman Filter" is one of the solutions that release from the hard parameter tuning task.
  - Reference
    - https://ieeexplore.ieee.org/document/555664
    - https://www.researchgate.net/publication/286650453_Spacecraft_attitude_rate_estimation_by_an_adaptive_unscented_Kalman_filter
- gyro.py
  - File for Gyro class. This class can treat the gyro orbit data and user can get the angular velocity data for specified time by 'get_omega_deg_s' function.
- star_tracker.py
  - File for StarTracker class. This class manages the specific information for each STT like alignment angle and measurement accuracy. User can get the measurement euler angle for specified time.
- plot_data.py
  - Script for ploting the log data. Please edit the log folder name when you execute this file.
## plot steady state
Script for plotting the attitude estimation accuracy in steady state with gyro and star tracker. Equations used in this script comes from following papers.
- Analytic Steady-State Accuracy Solutions for Two Common Spacecraft Attitude Estimators
- Analytic Steady-State Accuracy of a Spacecraft Attitude Estimator

Those papers are placed in \\axelnas2\intern\nishimoto\Input\from_kurata\20211210_reference_paper
