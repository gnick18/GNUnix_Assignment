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

###Maize Data

```
here is my snippet of code used for data processing
```

Here is my brief description of what this code does


###Teosinte Data

```
here is my snippet of code used for data processing
```

Here is my brief description of what this code does