---
title: "Using Biopython to concatenate sequences with the same name"
date: 2018-09-02
categories:
- study_notes
- python
tags:
- Biopython
- fasta
thumbnailImagePosition: left
thumbnailImage: https://i.imgur.com/BKidQJH.png
comments: false
showSocial: false
---

[Biopython](https://biopython.org/) is awesome! :)
<!--more-->

I need to merge hundreds of fasta files into one big file by concatenating sequences with the same identifier. <br>
My fasta files look like the following:
![exampleFiles](https://i.imgur.com/Cxx7FaF.png)

[Biopython](https://biopython.org/) makes it so easy to merge these sequences together. <br>

First, cat all the fasta files:
```
cat *.fasta > all.fasta
```
Then we can use a [dictionary](https://docs.python.org/2/library/stdtypes.html#typesmapping) to map all the sequence names:

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
The code was adapted from [here](http://lists.open-bio.org/pipermail/biopython/2015-July/015711.html). :)
