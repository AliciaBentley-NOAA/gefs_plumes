# gefs_plumes
Scripts used to create/populate the EMC GEFS plumes webpage (static 2021 version).

/----------------

Prior to running the code in this GitHub repository, you will need to download the 0.5-degree GEFS forecast files and GFS forecast files from NOMADS using your own code.

Location of 0.5-degree GEFS forecast files on NOMADS:
https://nomads.ncep.noaa.gov/pub/data/nccf/com/gens/prod/ 

Location of 0.5-degree GFS forecast files on NOMADS:
https://nomads.ncep.noaa.gov/pub/data/nccf/com/gfs/prod/

/-----------------

Here is a brief description of what is contained in the GEFS plumes repository and how it works:

1) The GEFS plumes webpage is updated 4 times a day (00, 06, 12, 18 cycles of the GEFS) via a cronjob. Example cronjobs can be found in example_cronjobs, where each cronjob starts the drive_gefs script for a different initialization time (00, 06, 12, 18). Output is sent to an initialization-time specific log file. Cronjobs that create the observations displayed on the GEFS plumes webpage (gefsobs and ygefsobs) are also included.  

2) The drive_gefs script creates temporary directories where the log files will be stored and where the .csv files read in by the GEFS plumes webpage will be created. drive_gefs then cd's into the temporary directory where the .csv files will be created and copies the list of stations displayed on the webpage. drive_gefs also makes short text files of the current date and yesterday's date before submitting jobs to run the various run_gefs scripts. 

3) Each run_gefs script creates a POE script that starts launchgefs, launchgefs2, etc.

4) Each launchgefs script starts several python scripts that create the .csv files that are read in by the GEFS plumes webpage (e.g., makegefs2mtcsv.py). Please change the paths to the locations of the GEFS ensemble member and GFS deterministic forecasts that you downloaded from NOMADS (see above).

5) Please note that several of the makegefsXXXcsv.py scripts are hardcoded to extract specific variable numbers from the GEFS and GFS forecast files (e.g., GFScape=grbs[602].values). This was done in order to cut down on run time. It is likely that you will have to update these variable numbers to work with the GEFS and GFS data from NOMADS. You can identify the variable numbers you need by running: wgrib2 "name of file" > list.out. The output will look like the contents of example_wgribout.txt, where the first number is the variable number you need.     

5) The last submitted job to finish running must always be run_gefs. This is because a) the final step of launchgefs (which run_gefs started) edits the GEFS plumes webpage (EMCGEFSplumes.html) to display the current date using edit_gefs.ksh, and b) the final step in run_gefs itself is to submit run_ftp_gefs and run_ftp_gefs1, which transfer all of the .csv files made to the web directory where they will be displayed. 

6) In addition to .csv files, run_ftp_gefs also transfers the GEFS plumes webpage that was modified to display the current date. It only transfers the webpage if greater than 3700 csv files have been created.

7) All of the contents of the /webpage directory need to copied into the same directory where you intend to display the GEFS plumes webpage. This will allow it to be formatted correctly.   
