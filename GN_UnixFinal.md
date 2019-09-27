#UNIX Assignment

##Data Inspection

###Attributes of `fang_et_al_genotypes`

```
$ du -h fang_et_al_genotypes.txt
# inspection of the columns and file structure
$ head -n1 fang_et_al_genotypes.txt #this showed me it was a massive file with a lot of columns. Way too many to fit on a single screen hence it just wrapped it around.
$ tail -n +100 fang_et_al_genotypes.txt | awk -F "\t" '{print NF; exit}' #I tested using different numbers on the -n
$ wc  fang_et_al_genotypes.txt
$ grep "^#" fang_et_al_genotypes.txt
```

By inspecting this file I learned that:

1. This file is 11M
2.  The head call returned so many columns even for just the first line that it was difficult to make sense of most of it. It appears to have all of the SNP locations in this first row. The SNP locations either begin with ZA, PZA, ZB, PZB, PZD, or ZD. There are some other SNP locations at the end part of this row.
3. I tested using different numbers on the -n to find the column amount and all gave the same output of 986. Thus I'm confident this is the true column amount and it's not being affected by headers.
4. The wc told me that the file has 2,783 lines, 2,744,038 words, and 11,054,722 characters.
5. Grepping for the "^#" was my way of checking if there were any lines that started with the # character. This sometimes is included in the beginning of files that have comments and information. I found no lines that started with this character.



###Attributes of `snp_position.txt`

```
$ du -h snp_position.txt
$ tail -n +50 snp_position.txt | awk -F "\t" '{print NF; exit}' #again I tried using different numbers on the -n
$ wc snp_position.txt
$ head -n1 snp_position.txt
$ grep "^#" snp_position.txt
```

By inspecting this file I learned that:

1. This file is 85K
2. The file has 15 columns, which is way less than the other file. I again tried different -n values using in +5, +20, and +50, all of which gave back 15 columns.
3. The wc told me that the file has 984 lines, 13198 words, and 83747 characters. It is much smaller than the other file.
4. This showed me that the file has headers for the columns. Some of them include things like "Chromosome" and "Position"
5. Grepping for the "^#" was my way of checking if there were any lines that started with the # character. I found no lines that started with this character.

##Data Processing

###SNP_position and Fang Processing

`vi fang_et_al_genotypes.txt
cut -f1 fang_et_al_genotypes.txt | head`

I changed the name Sample ID to SNP ID in the fang file that way the headers both matched up.

`grep "SNP_ID" snp_position.txt > SNP_headers.txt`

This removed the headers and saved them into a new file.

`cut -f 1,3,4 snp_position.txt > short_snp_position.txt`

I then cut out the unnecessary data from the SNP position file. For this project we just care about the chromosome and nucleotide locations.

`grep "SNP_ID" fang_et_al_genotypes.txt > maize_genotypes.txt`

```
grep "ZMM[IL,LR,MR]" fang_et_al_genotypes.txt >> maize_genotypes.txt
```

`grep "SNP_ID" fang_et_al_genotypes.txt > teosinte_genotypes.txt`

`grep "ZMP[JA,IL,BA]" fang_et_al_genotypes.txt >> teosinte_genotypes.txt`

I grepped out the maize and teosinte genotypes from the fang file. They still have the head at this point. I grepped the parts out separately and then appended them together.

`awk -f transpose.awk maize_genotypes.txt > transposed_maize_genotypes.txt`

``awk -f transpose.awk teosinte_genotypes.txt > transposed_teosinte_genotypes.txt``

`join -1 1 -2 1 short_snp_position.txt transposed_maize_genotypes.txt > joined_maize.txt`

`join -1 1 -2 1 short_snp_position.txt transposed_teosinte_genotypes.txt > joined_teosinte.txt`

Next I had to transpose the data using the awk one liner that was provided to us. After this, since I changed the column names with vim I was able to join the data. 

`mv joined_teosinte.txt teosinte`
`mv joined_maize.txt maize`

The last step was moving the finished files into the maize and teosinte folders. Yay! Now I can start working with cutting out the files I want. Maize first.

`grep "Group" fang_et_al_genotypes.txt > header.txt`
This is just did to have a file with the headers saved seperatly. I use this later on when I need to append the data to the header.

###Maize Data

`cp joined_maize.txt Increasing/`

`sed 's/?/-/g' joined_maize.txt > Decreasing/Djoined_maize.txt`

I copied the joined data to the increasing folder and changed the ? characters to - for the decreasing folder. These will be used to make the 10 chromosomal locations.

`grep "unknown" joined_maize.txt > unknown_positions_maize.txt`
`grep "multiple" joined_maize.txt > multiple_positions_maize.txt`

The next step was to grep out the unknown and multiple location files. But I also have to cat on the header to these files (see below).

`cat header.txt multiple_positions_maize.txt > F_mulpos_maize.txt`

This is my **final multiple positions document**. It has headers added back to it.

`cat header.txt unknown_positions_maize.txt > F_unkpos_maize.txt`

`mv multiple_positions_maize.txt unknown_positions_maize.txt misc/`

Next I made my **final unknown positions document**. Now for the 10 files, first I'm going to tackle the increasing position one. Additionally, to make everything easier to find I'm moving some intermediate files to the misc folder.

**increasing**

`grep -v -E "(unknown|multiple)" joined_maize.txt > joined_maize_cut.txt`

I removed the unknown and multiple data rows as they are already in a different folder.

`sort -k2,2n -k3,3n joined_maize_cut.txt > sorted_maize.txt`

Now the metadata is sorted by chromosome first and position second. Everything is in the perfect order to be cut and made into separate files.

` for i in {1..10}; do awk '$2=='$i'' sorted_maize.txt | sort -k3,3n > maize_chr"$i"_increasing.txt; done`

I made a for loop to cut out the files by there chromosome numbers. Under inspection of less -S they didn't keep the ordering. I added in the sort to the for loop to fix that. Now I just need to make a for look that cat's on the header to the files.

` for i in {1..10}; do cat header.txt maize_chr"$i"_increasing.txt > Final/maize_chr"$i"_FinalInc.txt; done`

I made another for loop the cat's on the header to the data. For organizational purposes **I put all of this finished data in the Final/ folder.** Using less -S to inspect the data they stayed in sorted order and all have the header added on top.

**decreasing**

`grep -v -E "(SNP_ID|unknown|multiple)" Djoined_maize.txt > Djoined_maize_cut.txt`

I removed the data that I don't need. This time I had to removed the SNP_ID as the decreasing numerical sorting puts that on the bottom. 

`sort -k2,2nr -k3,3nr Djoined_maize_cut.txt > test.txt`

The files are all sorted and ready to be cut out by chromosome. For the decreasing order I used the -k_,_nr option .

`for i in {1..10}; do awk '$2=='$i'' Djoined_maize_cut.txt | sort -k3,3nr > maize_chr"$i"_decreasing.txt; done`

After initially using the awk and then not sorting, I noticed they were not sorted correctly anymore. I added a sorting command in the for loop.

` for i in {1..10}; do cat header.txt maize_chr"$i"_increasing.txt > Final/maize_chr"$i"_FilenalDnc.txt; done`

**The final sorted decreasing data is also in its own Final/ folder.** I used the same for loop as the increasing.

###Teosinte Data

**Now that I have gone through the flow plan of making the files I will put less descriptions under exactly what I'm doing. 90% of it is recycled code which I will still include below.**

`cp joined_maize.txt Increasing/`

` sed 's/?/-/g' joined_teosinte.txt > Decreasing/Djoined_teosinte.txt`

copying the files into the decreasing and increasing folders for later.

`grep "unknown" joined_teosinte.txt > unknown_positions_teosinte.txt`
`grep "multiple" joined_teosinte.txt > multiple_positions_teosinte.txt`

`cat header.txt multiple_positions_teosinte.txt > F_mulpos_teosinte.txt`

`cat header.txt unknown_positions_teosinte.txt > F_unkpos_teosinte.txt`

`mv multiple_positions_teosinte.txt unknown_positions_teosinte.txt misc/`

These are my **final documents just like for maize for the unknown and multiple location files.** Like last time I moved two of the intermediate files just to tidy up the folder. Now I will move onto the increasing files.

**increasing**

`grep -v -E "(unknown|multiple)" joined_teosinte.txt > joined_teosinte_cut.txt`

I am not doing the sort like I did for the maize since I know it is included in the for loop later on.  I'll be using the same for loops I did on the Maize files.

` for i in {1..10}; do awk '$2=='$i'' joined_teosinte_cut.txt | sort -k3,3n > teosinte_chr"$i"_increasing.txt; done`

` for i in {1..10}; do cat header.txt teosinte_chr"$i"_increasing.txt > Final/teosinte_chr"$i"_FinalInc.txt; done`

With the unneeded metadata removed I used two for loops. As last time **the final data is in the Final/** folder.

**decreasing**

`grep -v -E "(SNP_ID|unknown|multiple)" Djoined_teosinte.txt > Djoined_teosinte_cut.txt`

I removed the data that I don't need. This time I had to removed the SNP_ID as the decreasing numerical sorting puts that on the bottom.  I also didn't include the sorting step since it is in the for loop.

`for i in {1..10}; do awk '$2=='$i'' Djoined_teosinte_cut.txt | sort -k3,3nr > teosinte_chr"$i"_decreasing.txt; done`

` for i in {1..10}; do cat header.txt teosinte_chr"$i"_decreasing.txt > Final/teosinte_chr"$i"_FinalDec.txt; done`

**I checked the outputs with less -S and they appear good!** The decreasing data is all in the Final/ folder.
