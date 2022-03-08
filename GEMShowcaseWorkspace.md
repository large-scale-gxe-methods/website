---
driveId: 1xu6dHTX70CMyM2ER3LEn0m8fZ5nNNR1Q/preview
---
# GEM Showcase Workspace 

We have created a showcase worskapce that's featured on the Terra platform to illustrate an entire workflow from VCF file to summary plots. The workspace uses genotype data from 1000 Genomes Project, which is publically available on Terra.

[GEM Showcase Workspace on Terra](https://app.terra.bio/#workspaces/largescale-gxe-methods/GEM-showcase)

[Learn more about Terra](https://terra.bio/resources/getting-started/)

# GEM Showcase Demo

{% include googleDrivePlayer.html id=page.driveId %}

# Examples and Debugging 
## Examples 

**Running GEM**

At minimum, GEM **requires** the following parameters to have an input value:

```
./GEM --bgen example.bgen \
      --pheno-file example.pheno \
      --sampleid-name sampleid \
      --pheno-name pheno2 \
      --pheno-type 1 \
      --exposure-names cov1
```

**Exposures**

Multiple exposures can be included for testing by passing the exposure names separated by a single space.
GEM will then output coefficient estimates and variances associated with each exposure term.

```
./GEM --bgen example.bgen --pheno-file example.pheno --sampleid-name sampleid --pheno-name pheno2 --pheno-type 1 \
      --exposure-names cov1 cov2 cov3
```

**Covariates**

Covariates can be adjusted for by using the --covar-names parameter. To include multiple covariates,
pass the covariate names separated by a single space like below.

```
./GEM --bgen example.bgen --pheno-file example.pheno --sampleid-name sampleid --pheno-name pheno2 --pheno-type 1 \
      --exposure-names cov1 \
      --covar-names cov2 cov3
```

**Interaction Covariates**

Interaction covariates can also be adjusted for using the --int-covar-names parameter. To include multiple
interaction covariates, pass the interaction covariate names separated by a single space.

```
./GEM --bgen example.bgen --pheno-file example.pheno --sampleid-name sampleid --pheno-name pheno2 --pheno-type 1 \
      --exposure-names cov1 \
      --int-covar-names cov2 cov3
```


Both the covariates and interaction covariates can be included in the model for adjusting.

```
./GEM --bgen example.bgen --pheno-file example.pheno --sampleid-name sampleid --pheno-name pheno2 --pheno-type 1 \
      --exposure-names cov1 \
      --covar-names cov2 \
      --int-covar-names cov3
```


**Multithreading**

GEM can perform multithreading by using the --threads parameter. By default, GEM will use half the number of
logical cores/ threads detected. Each thread will get roughly an equal amount of variants for association testing.

We recommend using the total number of physical cores / processing units for optimize performance.

```
./GEM --bgen example.bgen --pheno-file example.pheno --sampleid-name sampleid --pheno-name pheno2 --pheno-type 1 \
      --exposure-names cov1 \
      --covar-names cov2 \
      --int-covar-names cov3 \
      --threads 4
```    

## Debugging

**Sample size changes from N to 0**

This error occurs when GEM cannot match the sample identifiers in the genotype file to sample identifiers in the phenotype file.

**BGEN files**

For BGEN files, the sample identifier matching process happens between the phenotype file and the sample identifier block within the BGEN file [(see here)](https://www.well.ox.ac.uk/~gav/bgen_format/spec/latest.html)  **OR** the phenotype file and an external .sample file [(see here)](https://www.well.ox.ac.uk/~gav/qctool_v2/documentation/sample_file_formats.html).

Solutions:

1. If a .sample file (--sample) is included, be sure that the sample identifiers in the .sample file matches the sample identifiers in the phenotype file. Also make sure that **the number of sample identifiers in the .sample file matches with the .bgen file** or another error will occur. Finally, the first column in the .sample file should contain the sample identifiers as shown [(here)](https://www.well.ox.ac.uk/~gav/qctool_v2/documentation/sample_file_formats.html).
2. If no .sample file is included and the BGEN file contains a sample identifier block, a .sample file can be used instead. GEM will prioritize a .sample file over the BGEN sample identifier block for ID matching so that users do not have to reconstruct the entire BGEN file to modify the sample identifier block. A .sample file should be included using the --sample parameter. Please also see **Solution 1** if a .sample file is used instead.
3. If the user does not want to include a .sample file, ensure that the program used to generate the .bgen file is placing the expected sample identifiers in the BGEN sample identifier block.

We have generally come across this error when using [PLINK 2.0](https://www.cog-genomics.org/plink/2.0/data#export) to convert different genotype file formats     (vcf/bed/pgen) to bgen format. As shown in the link, PLINK 2.0 by default construct sample IDs as
    "id-paste=maybefid,iid,maybesid".

This can be solved by passing "id-paste=iid" instead as shown below.
```
./plink2.exe --vcf my_vcf_file.vcf --export 'bgen-1.2' 'id-paste=iid' --out my_bgen_file
```

