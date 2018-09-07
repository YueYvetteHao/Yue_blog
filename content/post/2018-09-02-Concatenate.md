---
title: "Using Biopython to concatenate (aligned) sequences with the same name"
date: 2018-09-02
categories:
- study_notes
- python
tags:
- Biopython
- fasta
thumbnailImagePosition: left
thumbnailImage: https://i.imgur.com/BKidQJH.png
comments: true
showSocial: true
---

[Biopython](https://biopython.org/) is awesome! :)
<!--more-->

<!-- toc -->

# Concatenate fasta files

I need to merge hundreds of fasta files into one big file by concatenating sequences with the same identifier. <br>
My fasta files look like the following:
![exampleFiles](https://i.imgur.com/Cxx7FaF.png)

[Biopython](https://biopython.org/) makes it so easy to merge these sequences together. <br>

First, cat all the fasta files:
```
cat *.fasta > all.fasta
```
Then we can use a [dictionary](https://docs.python.org/2/library/stdtypes.html#typesmapping) to map all the sequence names (the code was adapted from [here](http://lists.open-bio.org/pipermail/biopython/2015-July/015711.html)):

{{< codeblock "concat_seq.py "  "python" >}}
#!/usr/bin/python

from Bio import SeqIO
from collections import defaultdict

sequence_map = defaultdict(str)

for sequence in SeqIO.parse('all.fasta', "fasta"):
  sequence_map[sequence.name] += str(sequence.seq)

for key in sorted(sequence_map.iterkeys()):
  print '>' + key
  print sequence_map[key]
{{< /codeblock >}}

And we will get:
```
>Seq1
TTTCTTATT
>Seq2
TCTCCTACT
>Seq3
TATCATAAT
```

# Concatenate alignment files

Similarly, use [AlignIO](https://biopython.org/wiki/AlignIO) to read in sequence alignment files and concatenate them into a [meta-alignment](http://biopython.org/DIST/docs/tutorial/Tutorial.html#htoc77) file. For example, these files are in [relaxed phylip](https://biopython.org/wiki/AlignIO) format (the sequence identifiers can be longer than 10 characters):
```
# algn1.phy
     3    15
homo_sapiens                 GATAATGCTG ACTAC
ailuropoda_melanoleuca       GATAATGCTG ACTAA
felis_catus                  GATAATGCTG ACTAT
# algn2.phy
     3    15
homo_sapiens                 AGATTATTTC AGAAA
ailuropoda_melanoleuca       AGATTATTTC AGAAA
felis_catus                  AGATTATTTC AGAAA
```
Python code to concatenate them:
{{< codeblock "concat_algn.py "  "python" >}}
#!/usr/bin/python

from Bio import AlignIO
import glob

filelist = glob.glob('./*.phy')
i = 0
for file in filelist:
  print file
  alignment = AlignIO.read(file, "phylip-relaxed")
  alignment.sort()  # sort the sequence identifiers so they match in all files
  print("Alignment of length %i" % alignment.get_alignment_length())
  if i==0:
    cat_algn = alignment
  else:
    cat_algn += alignment
  i += 1

print "Concatenated:"
print("Alignment of length %i" % cat_algn.get_alignment_length())

outfh = open("superalgn.phy", "w")
AlignIO.write(cat_algn, outfh, "phylip-relaxed")

outfh.close()
{{< /codeblock >}}
And the output looks like:
```
 3 30
ailuropoda_melanoleuca  GATAATGCTG ACTAAAGATT ATTTCAGAAA 
felis_catus             GATAATGCTG ACTATAGATT ATTTCAGAAA 
homo_sapiens            GATAATGCTG ACTACAGATT ATTTCAGAAA 
```

