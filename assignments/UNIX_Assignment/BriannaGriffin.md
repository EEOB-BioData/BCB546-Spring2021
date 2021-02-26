# UNIX-Assignment
#Brianna D. Griffin UNIX Assignment 
##Data Inspection 
###Attributes of fang_et_al_genotypes.txt


```
$ wc fang_et_al_genotypes.txt
```
2783  2744038 11051939 fang_et_al_genotypes.txt
```
$ wc -l fang_et_al_genotypes.txt
```
2783 fang_et_al_genotypes.txt
```
$ du -h fang_et_al_genotypes.txt
```
6.1M	fang_et_al_genotypes.txt
```
$ tail -n +6 fang_et_al_genotypes.txt | awk -F "\t" '{print NF; exit}'
```
986

By inspecting the files I learned:
 1. There is 2783 lines
 2. There is 2744038 words and 11051939 bytes or characters
 3. The file is 6.1M 
 4. There are 986 columns 


###Attributes of snp_position.txt

```
$ wc snp_position.txt
```
984 13198 82763 snp_position.txt
```
$ wc -l snp_position.txt
```
984 snp_position.txt 
```
$ du -h snp_position.txt
```
38K	snp_position.txt
```
$ tail -n +6 snp_position.txt | awk -F "\t" '{print NF; exit}'
```
15

By inspecting the files I learned:
 1. There is 984 lines 
 2. There is 13198 words and 82796 bytes or characters. 
 3. The file is 38K
 4. There are 15 columns. 

##Data Processing 
###Maize Data 

```
#copied the og file as not to mess it up (I'm new to this)
$ cp fang_et_al_genotypes.txt fang.txt

#parce out ZMMIL to maize1 new file, then add ZMMLR and ZMMMR to maize1 file to have all maize data in one file
$ grep -E "(Sample_ID|ZMMIL|ZMMLR|ZMMMR)" fang.txt > maize1.txt 

#did the same for teosinte
$ grep -E "(Sample_ID|ZMPBA|ZMPIL|ZMPJA)" fang.txt > teosinte1.txt

#transpose maize1.txt
$ awk -f transpose.awk maize1.txt > trans_maize_genotypes.txt

#did the same for teosinte
$ awk -f transpose.awk teosinte1.txt > trans_teosinte_genotypes.txt

#removed header from files
$ awk 'NR > 2' snp_position.txt > snp_nohead.txt
$ awk 'NR > 4' trans_maize_genotypes.txt > trans_maize_nohead.txt

#did the same for teosinte
$ awk 'NR > 4' trans_teosinte_genotypes.txt > trans_teosinte_nohead.txt


#sorted the files for joining and renamed 
#was having trouble so looked it up https://www.gnu.org/software/coreutils/manual/html_node/Sorting-files-for-join.html
#still having issues, am removing line: see below
$ sort -k 1,1 trans_maize_nohead.txt > orderedmaize.txt
$ sort -k 1,1 snp_nohead.txt > orderedsnp.txt

#did the same for teosinte
$ sort -k 1,1 trans_teosinte_nohead.txt > orderedteosinte.txt

#joined the files together 
$ join -1 1 -2 1 -t $'\t' orderedsnp.txt orderedmaize.txt > joinedmaize.txt

#did the same for teosinte
$ join -1 1 -2 1 -t $'\t' orderedsnp.txt orderedteosinte.txt > joinedteosinte.txt
 
#made a backup just in case...
$ cp joinedmaize.txt joinedmaize_bu.txt

#did the same for teosinte
$ cp joinedteosinte.txt joinedteosinte_bu.txt

#reordered columns 
$ cat joinedmaize_bu.txt | awk -v OSF="\t" '{print $1,$3,$4,$2,$5,$6,$7,$8,$9,$10,$11,$12,$13,$14,$15}' > joinedmaize.txt

#did the same for teosinte
$ cat joinedteosinte_bu.txt | awk -v OSF="\t" '{print $1,$3,$4,$2,$5,$6,$7,$8,$9,$10,$11,$12,$13,$14,$15}' > joinedteosinte.txt

#in order
$ awk '$2 == 1' joinedmaize.txt | sort -k 3 > maizechr1.txt

#did the same for teosinte
$ awk '$2 == 1' joinedteosinte.txt | sort -k 3 > teosintechr1.txt

#reverse order
$ awk '$2 == 1' joinedmaize.txt | sort -r -k 3 

#change charcater ? to - 
$ awk '$2 == 1' joinedmaize.txt | sort -r -k 3 | sed 's/?/-/g' > maizechr1rev.txt

#did the same for teosinte
$ awk '$2 == 1' joinedteosinte.txt | sort -r -k 3 | sed 's/?/-/g' > teosintechr1rev.txt

#file with maize snps
$ grep -E "\?" joinedmaize.txt > snps_unknown.txt

#did the same for teosinte
$ grep -E "\?" joinedteosinte.txt > snps_unknown_teosinte.txt

```

```
#I could not figure out how to do the '1 file with all SNPs with multiple positions in the genome (these need not be ordered in any particular way)'
I tried many different methods and was focused on trying to pull out the unique SNP IDs (I defined unique as what was after the period on the ID)
#in the end I could not fugure that one out
```

###END RESULT FILE NAMES 

For maize (Group = ZMMIL, ZMMLR, and ZMMMR in the third column of the fang_et_al_genotypes.txt file) we want 20 files in total:
 10 files (1 for each chromosome) with SNPs ordered based on increasing position values and with missing data encoded by this symbol: ?
 
#BDG File Names for maize

 maizechr1.txt
 maizechr2.txt
 maizechr3.txt
 maizechr4.txt
 maizechr5.txt
 maizechr6.txt
 maizechr7.txt
 maizechr8.txt
 maizechr9.txt
 maizechr10.txt
 
10 files (1 for each chromosome) with SNPs ordered based on decreasing position values and with missing data encoded by this symbol: -

 maizechr1rev.txt
 maizechr2rev.txt
 maizechr3rev.txt
 maizechr4rev.txt
 maizechr5rev.txt
 maizechr6rev.txt
 maizechr7rev.txt
 maizechr8rev.txt
 maizechr9rev.txt
 maizechr10rev.txt
 
1 file with all SNPs with unknown positions in the genome (these need not be ordered in any particular way)

snps_unknown.txt

1 file with all SNPs with multiple positions in the genome (these need not be ordered in any particular way)

#N/A

For teosinte (Group = ZMPBA, ZMPIL, and ZMPJA in the third column of the fang_et_al_genotypes.txt file) we want 20 files in total:
10 files (1 for each chromosome) with SNPs ordered based on increasing position values and with missing data encoded by this symbol: ?

#BDG File names for Teosinte:

 teosintechr1.txt
 teosintechr2.txt
 teosintechr3.txt
 teosintechr4.txt
 teosintechr5.txt
 teosintechr6.txt
 teosintechr7.txt
 teosintechr8.txt
 teosintechr9.txt
 teosintechr10.txt
 
10 files (1 for each chromosome) with SNPs ordered based on decreasing position values and with missing data encoded by this symbol: -

 teosintechr1rev.txt
 teosintechr2rev.txt
 teosintechr3rev.txt
 teosintechr4rev.txt
 teosintechr5rev.txt
 teosintechr6rev.txt
 teosintechr7rev.txt
 teosintechr8rev.txt
 teosintechr9rev.txt
 teosintechr10rev.txt
 
1 file with all SNPs with unknown positions in the genome (these need not be ordered in any particular way)

 snps_unknown_teosinte.txt
 
1 file with all SNPs with multiple positions in the genome (these need not be ordered in any particular way)

 N/A
