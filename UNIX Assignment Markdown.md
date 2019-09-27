#Amy Pollpeter - UNIX Exercise Markdown File

##Checking for updates
Make sure my files are up-to-date.

```
$ git pull origin master
```

Files are up-to-date.

##Data Inspection  

### Attributes of ```fang_et_al_genotypes.txt``` file.

	$ du -h fang_et_al_genotypes.txt
	$ head fang_et_al_genotypes.txt
	$ head -n 1 fang_et_al_genotypes.txt
	$ wc fang_et_al_genotypes.txt
	$ head -n 1 fang_et_al_genotypes.txt | wc
	$ awk -F "\t" '{print NF; exit}' fang_et_al_genotypes.txt
	$ file fang_et_al_genotypes.txt

####By Inspecting this file, I've learned:


- - The file 6.1 Mb in size.
- There are 2783 lines in the file.
- The first line has 986 "words" and 10847.  I suspect the number of words corresponds to the number of columns.
- "Awk" confirmed that the number of columns is 986.
- The file is ASCII text.


### Attributes of ```snp.position.txt``` file
	
	$ du -h snp_position.txt
	$ wc snp_position.txt	
	$ head -n 1 snp_position.txt
	$ awk -F "\t" '{print NF; exit}' snp_position.txt
	$ file snp_position.txt


####By Inspecting this file, I've learned:
- 

- The file is 38 kb in size.
- The file has 984 lines of data.
- The first line has "15" words in it.
- Confirmed that the number of words in the first line equaled the number of columns.
- The file is ASCII text

##Processing Data Files

###Separating the maize and teosinte data into separate files
	$ grep -i zmm* fang_et_al_genotypes.txt > fang_maize.txt
	$ grep -i zmp* fang_et_al_genotypes.txt > fang_teosinte.txt
	$ wc fang_maize.txt fang_teosinte.txt



- I used ```grep``` to remove the maize and teosinte groups to separate files.
- Each of the separated files has 2729 lines.


### Transposing the maize and teosinte files

	$ awk -f transpose.awk fang_maize.txt > transposed_fang_maize.txt
	$ awk -f transpose.awk fang_teosinte.txt > transposed_fant_teosinte.txt
	$ wc transposed_fang_maize.txt transposed_fant_teosinte.txt
	$ awk -F "\t" '{print NF; exit}' transposed_fang_maize.txt
	$ awk -F "\t" '{print NF; exit}' transposed_fant_teosinte.txt


- This code transposes the columns and rows in the data set so rows become columns, etc.
- I used the ```awk transpose``` command on each of the files separated from the ```fang_et_al``` data.
- I used a ```wc``` command to determine there were 986 lines in each of these files.  The original file had 986 columns of data.
- I then used the ```awk column``` command to determine that the number of columns in the transposed files is 2729.  Recall that this is the same as the number of lines in the untranposed files. 

###Preparing the file to be joined: ```snp_position.txt```
	$ cut -f 1,3,4 snp_position.txt > snp_position_alt.txt
	$ head snp_position_alt.txt
	$ tail -n +2 snp_position_alt.txt > snp_position_edit.txt
	$ tail -n +2 transposed_genotypes.txt > transposed_genotypes_edit.txt
	$ sort -k1,1 snp_position_edit.txt > Snp_position_sorted.txt
	$ sort -k1,1 transposed_genotypes_edit.txt > sorted_genotypes.txt
	$ head -n 1 Snp_position_sorted.txt
	$ head -n 1 sorted_genotypes.txt

- Looking at the column headings of the ```snp_position_alt.txt``` file, I see that columns 1, 3 and 4 contain the information specified in the assignment instructions (SNP_ID, Chromosome, and Position).  I use the ```cut``` command to move these three columns to a new file: ```snp_position_alt.txt```.
- Using the head command on this resulting file allows me to confirm that I cut the correct columns into the new file.
- Sorted each file by the common column (column one in each file) and saved the sorted files as new files.
- Used the ```tail``` command to remove the headers of each of the files that I want to join.  I saved these "headerless" files to a new file.
- sorted the resulting files by the first column in each file (this is the common column between them).
- Used the ```head``` command on each file to confirm that the headers were removed and they were sorted correctly.

###Joining the maize data into one file.
	$ sort -k1,1 transposed_fang_maize.txt > sorted_maize.txt
	$ join -1 1 -2 1 -a 2 -t $'\t' Snp_position_sorted.txt sorted_maize.txt > combined_maize.txt
	$ awk -F "\t" '{print NF; exit}' combined_maize.txt
	$ wc combined_maize.txt

- I ran the ```join``` command using the option -a to include even unpairable lines.
- I ran the ```awk column``` command and ```wc``` command to determine the number of columns and lines in each file.
Number of columns =2731
Number of lines = 986


###Joining the teosinte data into one file
	$ sort -k1,1 transposed_fant_teosinte.txt > sorted_teosinte.txt
	$ join -1 1 -2 1 -a 2 -t $'\t' Snp_position_sorted.txt sorted_teosinte.txt > combined_teosinte.txt
	$ awk -F "\t" '{print NF; exit}' combined_teosinte.txt
	$ wc combined_teosinte.txt

- I ran the ```join``` command using the option -a to include even unpairable lines.
- I ran the ```awk column``` command and ```wc``` command to determine the number of columns and lines in each file.
Number of columns = 2731
Number of lines = 986

###Sorting files by chromosome - maize data
	$ cut -f 2 combined_maize.txt | sort| uniq -c
	$ awk '$2==1 {print $0}' combined_maize.txt > maize/maize_chr1.txt
	$ awk '$2 ~ /unknown/ {print $0}' combined_maize.txt > maize/maize_chr_unk.txt
	$ awk '$2 ~ /multiple/ {print $0}' combined_maize.txt > maize/maize_chr_mult.txt
	$ wc maize*
	$ sort -k3,3n maize_chr1.txt > sorted_increasing/maize_chr1_sort.txt
	$ sort -k3,3nr maize_chr1.txt > sorted_decreasing/maize_chr1_sort_dec.txt
	





- I used a combination of ```cut```, ```sort```, and ```uniq``` to determined the values included in the chromosome column.  Here are the results of this command:
	- 155 1
	- 53 10
	- 127 2
	- 107 3
	- 91 4
	- 122 5
	- 76 6
	- 97 7
	- 62 8
	- 60 9
	- 6 multiple
	- 27 unknown
- I used ```awk``` to search each file for the specified chromosome # in column 2 and print the entire record, then write that to a new file.  I repeated this command for each of the 10 chromosomes.
- I modified the ```awk``` command slightly for the multiple and unknown options.
- I ran a ```wc``` command using the wildcard * to get a wc (really line count) in each of the files to compare to the number of records that should be in each file. The results were:
 	 - 53   144743   579582 maize_chr10.txt
	-      155   423305  1694845 maize_chr1.txt
	-      127   346837  1388655 maize_chr2.txt
	-      107   292217  1169979 maize_chr3.txt
	-       91   248521   995021 maize_chr4.txt
	-      122   333182  1334006 maize_chr5.txt
	-       76   207556   831045 maize_chr6.txt
	-       97   264907  1060683 maize_chr7.txt
	-       62   169322   677945 maize_chr8.txt
	-       60   163860   656064 maize_chr9.txt
	-        6    16380    65600 maize_chr_mult.txt
	-       27    73737   295353 maize_chr_unk.txt
	-      983  2684567 10748778 total
-This command confirmed the number of records for the specific chromosomes.
I sorted each file based on column 3 (position) in an increasing fashion.
I then checked the first file to see if the sort was working how I expected it was.
I sorted each file based on column 3 (position) in an decreasing fashion.
I checked the first file to see if the sort was working how I expected it was.

###Sorting file by chromosome - teosinte data
	$ cut -f 2 combined_teosinte.txt | sort | uniq -c
	$ awk '$2==1 {print $0}' combined_teosinte.txt > teosinte/teosinte_chr_1.txt
	$ awk '$2 ~/multiple/ {print $0}' combined_teosinte.txt > teosinte/teosinte_chr_mult.txt
	$ sort -k3,3n teosinte_chr_1.txt > sorted/teosinte_chr1_sort.txt
	$ cut -f 3 teosinte_chr1_sort.txt | head
	$ sort -k3,3nr teosinte_chr_1.txt > sorted_decreasing/teosinte_chr1_sort_dec.txt
	$ cut -f 3 teosinte_chr1_sort_dec.txt | head




- I used the combined ```cut```, ```sort```, and ```uniq``` to determine the number of records for each of the different chromosomes in the teosinte file.  The results were:
	- 155 1
	- 53 10
	- 127 2
	- 107 3
	- 91 4
	- 122 5
	- 76 6
	- 97 7
	- 62 8
	- 60 9
	- 6 multiple
	- 27 unknown

- I then used the ```awk``` command to sort the teosinte data into separate files based on chromosome.
- I used a modified ```awk``` command to sort the unknown and multiple chromosome options.
- I ran a ```wc``` command using the wildcard (*) to get a wc/line count for each of the files to compare to the results I obtained from the combined file.
	- 53   144743   579582 teosinte_chr_10.txt
	- 155   423305  1694845 teosinte_chr_1.txt
	- 127   346837  1388655 teosinte_chr_2.txt
	- 107   292217  1169979 teosinte_chr_3.txt
	- 91   248521   995021 teosinte_chr_4.txt
	- 122   333182  1334006 teosinte_chr_5.txt
	- 76   207556   831045 teosinte_chr_6.txt
	- 97   264907  1060683 teosinte_chr_7.txt
	- 62   169322   677945 teosinte_chr_8.txt
	- 60   163860   656064 teosinte_chr_9.txt
	- 6    16380    65600 teosinte_chr_mult.txt
	- 27    73737   295353 teosinte_chr_unk.txt
	- 983  2684567 10748778 total
- I sorted each file based on column 3 (position) in an increasing fashion.
- I then checked the first file to see if the sort was working how I expected - it was.
- I sorted each file based on column 3 (position) in an decreasing fashion.
- I checked the first file to see if the sort was working how I expected - it was.

###Replacing missing data indicators
	$ sed 's/?/-/g' maize_chr1_sort_dec.txt > Edited/maize_chr1_sort_dec_edit.txt
	$ sed 's/?/-/g' teosinte_chr1_sort_dec.txt > Edited/teosinte_chr1_sort_dec_edit.txt


- The files sorted in increasing order already had the missing data indicated by a ? as specified.  I used the ```sed``` command to replace the '?' with a '-' in the files sorted in decreasing order.

###Adding Headers back onto files
	$ head -n 1 snp_position_alt.txt > Final/header
	$ cp header > Final/Maize_data/maize_chr_mult_final.txt
	$ cat teosinte_chr1_sort.txt >> /home/amyrp/BCB546X-Fall2019/assignments/UNIX_Assignment/current_files/teosinte/Final/Sorted_increasing/teosinte_chr1_incr.txt
	
In order to put headers back onto my files, I copied (using the ```head``` command) the first line of the one of my intermediate ```snp_position``` files (before I had removed the header.  I sent this line to a new file name, that would become my final file.
I then used ```cat``` with my completed processed files to append (using the ```>>```) them to the file containing the header.

### Final Steps
I compiled all of my processed files into a single directory with the file structure I had chosen for my repository and pushed the changes to the online github repository.