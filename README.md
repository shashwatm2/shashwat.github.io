# BioEC131-FinalProject
COVID Variants Genome Analysis Data Package

## 1. Install jBrowse
Follow the instructions on https://currentprotocols.onlinelibrary.wiley.com/doi/10.1002/cpz1.1120 to install jbrowse.

## 2. Download the necessary COVID Variant files for analysis
In order to download the FASTA and GFF3 files, we will be fetching the NCBI data using their EDirect tools. Depending on your system, follow the instructions below to install Entrez Direct Tools:

For Linux/Unix:
```
sudo apt update
sudo apt install -y perl wget
```

For macOS, use Homebrew:
```
brew install perl wget
```

Install EDirect Tools
```
cd ~
wget https://ftp.ncbi.nlm.nih.gov/entrez/entrezdirect/edirect.tar.gz
tar -xzf edirect.tar.gz
rm edirect.tar.gz
export PATH=$PATH:$HOME/edirect
```

Access your shell configuration file ```nano ~/.zshrc``` for Zsh or ```nano ~/.bashrc``` for Bash
and add the following line to the file, save, and exit
```
export PATH=$PATH:$HOME/edirect
```
Then, enter ```source ~/.zshrc``` or ```source ~/.bashrc``` to reload your shell config file.


Now, you can use the following commands to fetch the 5 FASTA files and 5 GFF3 files for the COVID Variants. Ensure that you are in the correct directory/workspace for your project, so that the files download in the correct location. You can use ```cd ~/yourpath``` and enter in the appriopriate path to your project folder prior to running this code.
```
efetch -db nucleotide -format fasta -id OQ204161 > Alpha.fasta
efetch -db nuccore -format gff3 -id OQ204161 > Alpha.gff3
efetch -db nucleotide -format fasta -id OL678795 > Beta.fasta
efetch -db nuccore -format gff3 -id OL678795 > Beta.gff3
efetch -db nucleotide -format fasta -id OL852672 > Gamma.fasta
efetch -db nuccore -format gff3 -id OL852672 > Gamma.gff3
efetch -db nucleotide -format fasta -id OL873994 > Delta.fasta
efetch -db nuccore -format gff3 -id OL873994 > Delta.gff3
efetch -db nucleotide -format fasta -id OL845686 > Omicron2.fasta
efetch -db nuccore -format gff3 -id OL845686 > Omicron2.gff3
```

## 3. Process and load the data into jBrowse
For the fasta files, we will need to index them via the commands below and then load them as an assembly into jBrowse. Be sure to replace with the correct path to where your jbrowse folder is. It is typically located in a 'www' folder. Repeat for each of the 5 FASTA files (Alpha, Beta, Gamma, Delta, Omicron).
```
samtools faidx Alpha.fasta
jbrowse add-assembly Alpha.fasta --out /YOUR-PATH/jbrowse2 --load copy
```

For the GFF3 files, we want to sort the annotations, compress the GFF3 file, and then index with tabix. Once those are done, we can load the annotation track. Be sure to replace with your path to jbrowse folder, and repeat for each of the 5 FASTA files (Alpha, Beta, Gamma, Delta, Omicron).
```
jbrowse sort-gff Alpha.gff3 > sortedAlpha.gff
bgzip sortedAlpha.gff
tabix sortedAlpha.gff.gz

jbrowse add-track sortedAlpha.gff.gz --assemblyNames "Alpha" --out /YOUR-PATH/jbrowse2 --load copy
```

Finally, in order to allow the user to search for the genes we need to run the text-index command.
```
jbrowse text-index --out /YOUR-PATH/jbrowse2
```
Now all your data should be loaded into your jbrowse! You can look at genome annotations for each variants and analyze the sequence of each variant.

## 4. Download synteny analysis data and visualize in jBrowse
Download the Synteny folder from this repository and place it in your appropriate directory/workspace. This folder should contain 10 .paf files, which contain synteny analysis of the 5 different variants and were generated through minimap2. Similar to how the GFF3 files were loaded into jBrowse, we will load in these files as synteny tracks. Be sure to replace with your path to jbrowse folder, and repeat for each file.
```
jbrowse add-track "Beta_Alpha.paf" --name "Beta-Alpha Synteny" --assemblyNames "Alpha,Beta" --out "/YOUR-PATH/jbrowse2" --load copy 
```
Now you should be able to utilize the linear synteny view tool in jBrowse to analyze the synteny of the different variants.

## 5. Download MSA and phylogenetic tree data and visualize in jBrowse
Download the MSA_tree folder from this repository and place it in your appropriate directory/workspace. This folder should contain 2 files, a fasta file and a treefile. Next, add the MSA View Plugin from the jBrowse plugin store in the Tools section of jBrowse, and then upload these two files into the plugin directly to visualize a phylogenetic tree and multiple sequence alignment data of the 5 variants.

## 6. Download PDB data and visualize 3D protein structures in jBrowse
Download the folder labeled "Spike Protein PDB Files". It contains PDB files that are labeled by variant and whether it contains the ACE2 receptor bound in the structure. In JBrowse, add the Protein3D plugin and launch Protein View from the navigation bar. You should be able to upload the PDB file of interest and examine the structure of the spike protein and how it interacts with the ACE2 receptor.
