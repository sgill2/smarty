# Conversion of modified frcmod files into SMIRFFF FFXML format

Amber parm99 with a multitude of additional parameters in parm@Frosst can be effectively used on a wide spectrum of pharmaceutically relevant small molecules. An attempt is made here to convert these into smirff format.
These are all files associated with converting and testing the combined SMIRFF  *smirff99Frosst*


## Manifest
* `convert_frcmod.py` - python script to convert a frcmod file (with SMIRKS i.e. `example.frcmod`) into a SMIRFF ffxml file by parsing the parameters and inserting them into a template ffxml.
* `example.frcmod` - example modified frcmod file which contains SMIRKS in the place of normal AMBER atom types in the parameter definitions
* `template.ffxml` - template SMIRFF FFXML file which the parameters from the frcmod file will be inserted into; already contains relevant force sections but not the paramters themselves.
* `example.ffxml` - converted SMIRFF FFXML as generated by convert_frcmod.py
* `example_plusgeneric.ffxml` - example which takes a generated file created by convert_frcmod.py and adds some generic types as parameter 0 under each force that ensure the result covers all parameter types.
* `parameter_usage.ipynb` - example notebook (requested and updated by Bayly) which shows how to determine which molecules do/do not contain a specific parameter and then visualized those molecules and where that parameter is assigned. Used to check the use of generic bond, angle, or torsion SMIRKS in the ffxml to determine what chemistry is not covered by the relevant SMIRFF 
* smirffishFrcmod.parm99Frosst.txt - the key source file for the smirff ffxml file, this file has the format of an amber .frcmod file except the initial field giving bonded amber atomtypes is replaced with a smirks string for use with the smirff format for that parameter. 
* smirff99Frosst.ffxml - this is the smirff format file containing the best efforts of C.I.Bayly during his sabbatical in summer 2016 to convert amber parm99.dat plus the parm@Frosst frcmod file into a single small-molecule smirff ffxml.
* smirffishFrcmod.parm99Frosst_AlkEthOH.txt - a version of smirffishFrcmod.parm99Frosst.txt which has been extended to include the same parameters as parm@frosst on the AlkEthOH set, including in cases where Bayly deliberately deviated from it (e.g. for the CT-OH-HO angle/force constant where he went to the generic). Created by David Mobley for energy checking purposes.

## Usage Examples

#### convert_frcmod.py

This python script convers the frcmod type files, with SMIRKS strings instead of combined atomtypes into a SMIRFF ffxml file. It can be imported or called from the commandline. 

```
Usage:     Convert specified SMIRKS-ified AMBER frcmod file into SMIRFF FFXML format, inserting converted parameters into a template FFXML file and writing to a new output file.

    usage: convert_frcmod.py --frcmod test.frcmod --template template.ffxml --xml test.ffxml


Options:
  -h, --help            show this help message and exit
  -f INFILE, --frcmod=INFILE
                        Name of input smirks-ified frcmod file.
  -t INXML, --template=INXML
                        Name of template SMIRFF ffxml file.
  -o OUTXML, --xml=OUTXML
                        Name of output SMIRFF ffxml file.
```

For exmple, smirff99Fross.ffxml was created by calling:

```
python convert_frcmod.py -f smirffishFrcmod.parm99Frosst.txt -t template.ffxml -o smirff99Frosst.ffxml
```

#### parameter_usage.ipynb

parameter_usage.ipynb - this ipython notebook originally written by David Mobley carries out several independent steps:
a) Reading in a smirffishFrcmod file (here smirffishFrcmod.parm99Frosst.txt) and converting it to smirffparm99Frosst
b) Reading in a database of molecules and applying to them the smirff ffxml from part (a).
c) Analyzing the result of (b) to see which molecules get which parameters, including a depiction function which will depict a molecule highlighting the atoms associated with a specific parameter.

## Intermediate Files
A variety of intermediate files were generated during the conversion process. 
Only updated "smirff" frcmod files and resulting ffxml files are stored here, we have kept the intermediate files for future reference.
They are stored in `smirffish_archive` 

The supporting intermediate files consist mostly of the building up of smirffishFrcmod.parm99Frosst.txt by gradually adding Bonds, VdW, and Angles in that order. Then Impropers (a very few parameters) were added. With Torsions, the intermediate files were done first for parm99 and then parm@Frosst. Then the Torsions were combined into smirffishFrcmod.Torsions.parm99withParmFrosst.txt and harmonized. This file was then introduced into smirffishFrcmod.parm99Frosst.txt, replacing the parm99-only torsions. This was then iteratively improved based on how well the parm@Frosst zinc 7505 molecule set was parameterized in parameter_usage.ipynb. With the 2016sep06 version, there were only 17 molecules requiring thegeneric torsion, most of those having broken chemistry (e.g. pentavalent nitrogens or carbons).

