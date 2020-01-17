# DeepCDR
Cancer Drug Response Prediction via a Hybrid Graph Convolutional Network
 
 ![model](https://github.com/kimmo1019/DeepCDR/blob/master/model.png)
 
 DeepCDR is a hybrid graph convolutional network for cancer drug response prediction. It takes both multi-omics data of cancer cell lines and drug structure as inputs and predicts the drug sensitivity (binary or contineous IC50 value). 
 
 # Requirements
- Keras==2.1.4
- TensorFlow==1.13.1
- hickle >= 2.1.0

# Installation
DeepCDR can be downloaded by
```shell
git clone https://github.com/kimmo1019/DeepCDR
```
Installation has been tested in a Linux/MacOS platform.

# Instructions
We provide detailed step-by-step instructions for running DeepCDR model including data preprocessing, model training, and model test.

## Model implementation
**Step 1**: Generating genomic mutation matrix, gene expression matrix and DNA methylation matrix from raw data in CCLE database

```python
python DataPreprocess.py [drug info file] [drug screening file] [genomic mutation file] [gene expression file] [DNA methylation file]
[drug info file] - Drug information including name, pubchem id, etc
[drug screening file] - Drug information and IC50 values from GDSC database
[genomic mutation file] - Genomic profile from CCLE database
[gene expression file] - Transcritomic profile from CCLE database
[DNA methylation file] - Epigenomic profile from CCLE database
```
The preprocessed data will be in located in `data` folder.

`genomic_mutation_34673_demap_features.csv` --  genomic mutation matrix where each column denotes mutation locus and each row denotes a cell line

`genomic_expression_561celllines_697genes_demap_features.csv` -- gene expression matrix where each column denotes a coding gene and each row denotes a cell line

`genomic_methylation_561celllines_808genes_demap_features.csv` -- DNA methylation matrix where each column denotes a methylation locus and each row denotes a cell line

Note that we also directly provided the above files in the `data` folder. 

**Step 2**: Drugs structure extraction

Each drug in our study will be represented as a graph containing nodes and edges. From the GDSC database, we collected 223 drugs that have unique Pubchem ids. Note that a drug under different screening condition (different GDSC drug id) may share the same Pubchem id.
Here, we used [deepchem](https://github.com/deepchem/deepchem) library for extracting node features and gragh of a drug. The node feature  (75 dimension) corresponds to a stom in within a drug, which includes atom type, degree and hybridization, etc. 

We recorded three types of features in a list as following

```python
drug_feat = [node_feature, adj_list, degree_list]
node_feature - features of all atoms within a drug with size (nb_atom, 75)
adj_list - adjacent list of all atoms within a drug. It denotes the all the neighboring atoms indexs
degree_list - degree list of all atoms within a drug. It denotes the number of neighboring atoms 
```

The above feature list will be further compressed as `pubchem_id.hkl` using hickle library.

Please note that we provided the extracted features of 223 drugs from GDSC database, just unzip the `drug_graph_feat.zip` file in `data/GDSC` folder




**Step 3**: DeepCDR model training and testing

Here, we provide a DeepCDR regression model here as an example.

```python
python run_DeepCDR.py -gpu_id [gpu_id] -use_mut [use_mut] -use_expr [use_gexp] -use_methy [use_methy] -checkpoint_dir [checkpoint_dir]
[gpu_id] - set GPU card id (default:0)
[use_mut] - whether use genomic mutation data (default: True)
[use_gexp] - whether use gene expression data (default: True)
[use_mut] - whether use DNA methylation data (default: True)
[log_dir] - location to save the trained model
```

The prediction outcome will be saved in `data/outcome` folder. The overall Pearson's correlation will be calculated. Besides, the correlation across different drugs and across different cell lines will also be calculated.

# Contact
If you have any question regard our code or data, please do not hesitate to open a issue or directly contact me (liu-q16@mails.tsinghua.edu.cn)


# License
This project is licensed under the MIT License - see the LICENSE.md file for details


























