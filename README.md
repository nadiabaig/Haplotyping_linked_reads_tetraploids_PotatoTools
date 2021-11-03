
<!-- ABOUT THE PROJECT -->
## Constructing haplotype blocks for tetraploids using 10x-reads -- PotatoTools
### For QGGP's RIBS meeting only

This repository provides the bash script , I used for my 10x datasets to get the haplotype blocks for tetraploid genotypes using Hap++ pipeline in combination with H-PoPG (see the reference for github profile and paper).


Main steps of the pipeline inludes:
* Splitting the vcf file (multi-sample VCF) into per sample per chromosome VCF as the Hap10 pipeline works for 1 chromosome at a time
* Filtering the VCF by removing homozygous and non-SNP variants in tetraploids
* Runnng the Hap10 to get the haplotype relevant information
* Haplotype assembly using H-PoPG: For Haplotype assembly Hap10 pipeline uses SDhap tool but,it is not easy to install. 
  - It may takes few days! (&#x1F534;) 
```diff
+ It needs dependency ATLAS (Automatically Tuned Linear Algebra Software) libraries and also need lapack. 
- TO INSTALL it on our institutes server it needs disabling CPU throttling. From our institute 'Intel MKL' is available, but you need to bulid it by yourself  using module load  "intel/xe2020.4". (Not tried this alterantive-- In case anyone is interested try your luck)..
```
  


<!-- GETTING STARTED -->
## Getting Started

### Installation

1. See Hap10 github or else do,

```sh
git clone https://github.com/smajidian/extract_poly
cd extract_poly
make 
```
2. Clone the Hap10 package
```sh
git clone https://github.com/smajidian/Hap10.git
cd Hap10
```

<!-- USAGE EXAMPLES -->
## Usage

qsub yourscript_name.sh


<!-- ROADMAP -->
## 🚧 Roadmap

See the [issues ](https://github.com/smajidian/Hap10/issues?q=is%3Aissue+is%3Aclosed) new and old issues of Hap10.
See the [issues ](https://github.com/MinzhuXie/H-PoPG/issues) new and old issues of /H-PoPG.



<!-- CONTACT -->
<!--## 📫 Contact -->

<!--Your Name - [@your_twitter](https://twitter.com/your_username) - email@example.com -->

<!--Project Link: [https://github.com/your_username/repo_name](https://github.com/your_username/repo_name) -->

# References
1. https://github.com/smajidian/Hap10
2. Majidian, S., Kahaei, M.H. & de Ridder, D. Hap10: reconstructing accurate and long polyploid haplotypes using linked reads. BMC Bioinformatics 21, 253 (2020). https://doi.org/10.1186/s12859-020-03584-5
3. https://github.com/MinzhuXie/H-PoPG
4. https://github.com/vibansal/HapCUT2
