# BML-RangeSeries-Archive-Generator

These scripts help to create an archive of rangeseries data.

1	Create a list of all files that need to go into the archive.

`./makelist volume site`

The makelist script searches for all relevant range series and configuration files.
	volume is the path to a radial site archive
	site is the four letter site name
You may provide multiple volumes separated by commas and you may provide several sites separated by commas,
in which case it will search all combinations of volume and site names for relevant files. See mymakelist.
If your volume name contains spaces, be sure to quote the volume name or escape the spaces, e.g.:
	'/Volumes/RAID 1' or /Volumes/RAID\ 1
The script searches for relevant files using a list of filenames that is defined in the script.
To change the list of relevant files, edit the makelist script.
The site name is used to match range series filenames and to name the output file: filelist_site.
You may check and edit the filelist, primarily to remove files you dont want to archive before the next step.

2	Build an archive from a filelist.

`./runlist site archive filelist`

The runlist script builds or extends an archive directory with files listed in filelist.
	site is the four letter site code used in filenames.
	archive is the path to the archive root and may be an absolute path or a relative path.
	filelist is the name of the file produced by makelist.
The script then runs "build.pl site archive file" to copy each file into the archive.
Rangeseries files are sorted into a tree structure according to their date.

Configuration files are copied into a temporary directory called 'sortdir' which contains subdirectories named
after the configuration file and its modification date. This allows the configuration files to be grouped by date using the
'regroup' script. The configuration file modification date is taken from the OSX metadata attributes (mdls) using the
attribute named "kMDItemContentModificationDate". If the MD attribute is not available then the file's mtime is read
from the directory entry.

If you are not interested in the configuration files, simply delete the sortdir subdirectory and you're done.

3	Regroup config files.

`./regroup archive`

The regroup script examines all configuration files within the 'sortdir' subdirectory and builds new subdirectories
under the 'Configs' subdirectory according to their modification dates. Each Configs subdirectory contains all
configuration files for a given date range, to simplify reprocessing.

4	Clean up.

`./cleanup archive`

Shows file count data for the given archive and deletes the sortdir subdirectory.


TODO

1. There are several minor issues related to regrouping configuration files that need to be fixed at specification level.
For example, currently any change to any config file triggers a new Configs subdirectory, sometimes not justified.
A better approach is needed to ignore insignificant changes to config files.
2. The current regroup script has an issue in that it uses only the files under sortme and ignores those under Configs.
This makes is potentially bad at updating an archive that has already been regrouped, i.e. it might create more
Configs subdirectories than it needs to.
3. Currently all files involved in processing an APM are ignored.


END
