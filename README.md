# GMQL-Python
API for calling interactively the GMQL Engine from Python

## Architecture
<img src="./images/PyGML - Architecture.png">

## Example of usage
```
# LOADING OF DATA FROM A REPOSITORY

# Mutations comes from a genome wide association study
GWAS = SELECT ( ) GWAS;

# We select the experiments related to acetylation at the 27th lysine residue of the histone protein 3 (H3K27Ac)
# from a genomic assembly for humans (HG19)
Ac = SELECT ( experiment_target == ’H3K27ac−human’ ) HG19_ENCODE_BROAD;

# PREPROCESSING

# I take the middle of the regions
peaked = PROJECT ( region_update : peak AS RIGHT/2 + LEFT / 2 ) Ac ;
# Extend the region center by +- 1.5Kb
large = PROJECT( region_update : LEFT AS peak - 1500,
                                 RIGHT AS peak + 1500) peaked;

# merge replicats together
rep = COVER(groupby: biosample_term_name) large;

# find the cell-line specific enhancers
S = COVER(1,2) REP;
RepCount = MAP() REP S;
CSE = SELECT(region : count_REP_S > 0) RepCount;

# find the mutation occurring in those enhancers
M = MAP(bag AS BAG(trait)) CSE GWAS;
N = SELECT(region : count_Z_GWAS > 0) M;
P = PROJECT(count_Z_GWAS, bag) N;

MATERIALIZE P int test_final;
```
