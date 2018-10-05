---
title: "Parsing nested XML files"
date: 2018-10-05
categories:
- study_notes
- python
tags:
- xml
thumbnailImagePosition: left
thumbnailImage: https://i.imgur.com/eNyhevp.jpg
comments: true
showSocial: true
---

How to parse nested xml files with the [`xml.etree.ElementTree`](https://docs.python.org/3/library/xml.etree.elementtree.html) module.
<!--more-->

XML is a data format that is similar to html: information was organized hierarchically between layers of paired brackets. 
For example, here is a file containing metabolites data and the pathways each metabolite participates in:
{{< codeblock "metab.xml" "xml" >}}
<?xml version="1.0"?>
<data>
<metabolite id="1">
  <accession >METAB0001</accession>
  <secondary_accessions>
    <accession>001</accession>
    <accession>002</accession>
  </secondary_accessions>
  <name>MET1</name>
  <pathways>
    <pathway>
      <name>path1</name>
      <smpdb_id>SMP1</smpdb_id>
      <kegg_map_id>map1</kegg_map_id>
    </pathway>
    <pathway>
      <name>path2</name>
      <smpdb_id>SMP2</smpdb_id>
      <kegg_map_id/>
    </pathway>
  </pathways>
</metabolite>
<metabolite id="2">
  <accession>METAB0002</accession>
  <name>MET2</name>
  <pathways>
    <pathway>
      <name>path2</name>
      <smpdb_id>SMP2</smpdb_id>
      <kegg_map_id>map2</kegg_map_id>
    </pathway>
    <pathway>
      <name>path3</name>
      <smpdb_id>SMP3</smpdb_id>
      <kegg_map_id/>
    </pathway>
  </pathways>
</metabolite>
</data>
{{< /codeblock >}}
We want to extract the id and name of each metabolite, as well as all the pathway names for each metabolite. 
Here is a short python script for parsing nested xml files:
{{< codeblock "nested_xml_parser.py" "python" >}}
#!/usr/bin/python

import sys
from xml.etree import cElementTree as ET

def main():
  file = sys.argv[1]
  tree = ET.parse(file)
  root = tree.getroot()
  for child in root:
    metname = child.find('name').text
    metid = child.find('accession').text
    sys.stdout.write(metid + '\t' + metname + '\t')
    for node in child.findall('.//secondary_accessions'):
      for snode in node.findall('accession'):
        sys.stdout.write(snode.text + '\t')
    print '\r'
    for node in child.findall('.//pathways'):
      for snode in node.getchildren():
        pathname = snode.find('name').text
        print pathname
    print '\r'   
     
if __name__ == '__main__':
  main()

{{< /codeblock >}}

The output will be:
```
METAB0001	MET1	001	002	
path1
path2

METAB0002	MET2	
path2
path3

```