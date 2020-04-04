# 502FinalProjScripts
### MonthlyCongressCSVs.ipynb:

This script is taking all of the congress tweet json files, and converting them into monthly DataFrames for dask. 

The directories in the bucket for the csv data are named {June17.csv, July17.csv, Aug17.csv, ..., Jan18.csv, Feb18.csv, ..., Nov19.csv, Dec19.csv}. To read a month into a new notebook, you should use the command

"DF = dd.read_csv('s3://502finalprojbucky/congresstweets/data/\<csv Directory Name\>/*.part')"
 
They were broken into .part files by dask when written to the bucket. The "\*.part" is just collecting all of the files in the directory, then dask.dataframe (imported as dd usually), has a read_csv() that puts them all together into one big DataFrame that you can work with virtually the same as a pandas DF. 
