
<!-- ABOUT THE PROJECT -->
## Constructing haplotype blocks for tetraploids using 10x-reads -- PotatoTools
### For QGGP's RIBS meeting only

This repository provides the bash script , I used for my 10x datasets to get the haplotype blocks for tetraploid genotypes using Hap++ pipeline in combination with H-PoPG (see the reference for github profile and paper).

```diff
@@ Main steps of the pipeline inludes: @@ 
```
* Splitting the vcf file (multi-sample VCF) into per sample per chromosome VCF as the Hap10 pipeline works for 1 chromosome at a time
* Filtering the VCF by removing homozygous and non-SNP variants in tetraploids
* Runnng the Hap10 to get the haplotype relevant information
* Haplotype assembly using H-PoPG: For Haplotype assembly Hap10 pipeline uses SDhap tool but,it is not easy to install. 
  -  (&#x1F534;)  It may takes few days!
```diff
+ It needs dependency ATLAS (Automatically Tuned Linear Algebra Software) libraries and also need lapack. 
- TO INSTALL it on our institutes server it needs disabling CPU throttling. 
- From our institute 'Intel MKL' is available, but you need to bulid it by yourself  using module load  "intel/xe2020.4".
- (Not tried this alterantive by myself-- In case anyone is interested try your luck)..
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
3. Download the jar file for haplotype assembly
```sh
https://github.com/MinzhuXie/H-PoPG.git
```

<!-- USAGE EXAMPLES -->
## Usage
### VCF splitting and filtering
```sh
!/bin/bash

#PBS -l select=1:ncpus=1:mem=5G:arch=skylake
#PBS -l walltime=50:00:00
#PBS -A  "your_project"

module load bcftools/1.10.2

cd $PBS_O_WORKDIR

bcftools query -l 07_new_formated.vcf > IDlist.txt  #give you a list of genotypes in your multi-VCF file
wait

for file in yourvcf.vcf; do  
  for sample in `bcftools view -h $file | grep "^#CHROM" | cut -f10-`; do
    bcftools view -c1 -s $sample -o ${file/.vcf*/.$sample.vcf} $file
  done
done
```
### VCF and BAM file spliting per Chromosome per Genotype + Hap10 pipeline

```sh
#!/bin/bash
#PBS -l select=1:ncpus=1:mem=10G:arch=skylake
#PBS -l walltime=10:00:00
#PBS -A  "project"
cd $PBS_O_WORKDIR

##Path of tools in the project folder which aren't available on Hpc 
ext_poly=/gpfs/--/src/extract_poly/build/extractHAIRS
util=/gpfs/--/src/extract_poly/LinkFragments_brcd_based.py
p2=/gpfs/--/src/Hap10/utilities
path=/gpfs/--/Hap10_analysis  #path where I want to save results

bam1=Chr01.bam bam2=Chr02.bam bam3=Chr03.bam 
vcf1=Chr01.vcf vcf2=Chr02.vcf vcf3=Chr03.vcf
 
##create genotype folders
mkdir Agria Semlo
wait

for x in {Agria, Semlo};
do
cd /gpfs/project/--/Hap10_analysis
echo "#!/bin/bash
#PBS -l select=1:ncpus=2:mem=70G
#PBS -l walltime=167:00:00
#PBS -A "project"
module load Python/3.8.3 HTSlib/1.7 Java/1.8.0 gcc/4.8.1 bzip2/1.0.6
module load bcftools/1.10.2
module load Perl/5.32.1
module load SamTools/1.6

cd /gpfs/project/--/Hap10_analysis/$x   

##filtering VCF file and splitting it into individual Chromosome VCFs

cat $path/07_new_formated.${x}.vcf | grep -v "1/1/1/1" | grep -v "0/0/0/0" > $path/$x/var_het.vcf   #This will generate filtered vcf and save it into respective genotype folder

wait
bgzip -c $path/$x/var_het.vcf > var_het.vcf.gz  #compress vcf
wait
tabix -f -p vcf var_het.vcf.gz  # index compressed vcf
wait  #Note: for loop was slow,skipped loop here
tabix  $path/$x/var_het.vcf.gz Chr01 > $path/$x/Chr01.vcf &
tabix  $path/$x/var_het.vcf.gz Chr02 > $path/$x/Chr02.vcf &
tabix  $path/$x/var_het.vcf.gz Chr03 > $path/$x/Chr03.vcf &

wait
##splitting bams
samtools view  -hb $path/filtered_realigned_${x}_reads.bam Chr01 > $path/$x/Chr01.bam 
samtools view  -hb $path/filtered_realigned_${x}_reads.bam Chr02 > $path/$x/Chr02.bam 
samtools view  -hb $path/filtered_realigned_${x}_reads.bam Chr03 > $path/$x/Chr03.bam 
wait
mkdir Chr01 Chr02 Chr03 #will create subfolders inside each genotype
wait

##Now, we are ready to use Hap10 pipeline
##Step1:extracting unlinked fragment files for each chromosome using BAM and VCF files

$ext_poly --10X 1 --bam $bam1 --VCF $vcf1 --out $path/$x/Chr01/unlinked_fragment_file.txt &
$ext_poly --10X 1 --bam $bam2 --VCF $vcf2 --out $path/$x/Chr02/unlinked_fragment_file.txt &
$ext_poly --10X 1 --bam $bam3 --VCF $vcf3 --out $path/$x/Chr03/unlinked_fragment_file.txt &

wait

python3 $util $path/$x/Chr01/unlinked_fragment_file.txt $path/$x/Chr01/linked_fragment_file.txt &
python3 $util $path/$x/Chr02/unlinked_fragment_file.txt $path/$x/Chr02/linked_fragment_file.txt &
python3 $util $path/$x/Chr03/unlinked_fragment_file.txt $path/$x/Chr03/linked_fragment_file.txt &

wait

#Step2: Extracting the molecule-specific fragments in which m is the mean 10X molecule length (in Kb) which can be set as 50. (github Hap10)

python3 $p2/splitter.py $path/$x/Chr01/linked_fragment_file.txt $vcf1 50 &
python3 $p2/splitter.py $path/$x/Chr02/linked_fragment_file.txt $vcf2 50 &
python3 $p2/splitter.py $path/$x/Chr03/linked_fragment_file.txt $vcf3 50 &

" > /gpfs/--/Hap10_analysis/Hap10_fragments.${x}.sh

cd /gpfs/--/Hap10_analysis
qsub Hap10_fragments.${x}.sh

done
```

```diff
+ Note: Save the afforementioned script in a global.sh file and do qsub global.sh, it will generate 1 script for each genotype and each script will run automatically
```
### Stongly connected components extraction and haplotype assembly

```diff
 ! I have divided the pipeline into 3 scripts as the script2 and current script requires more memory
```

```sh
!/bin/bash

#PBS -l select=1:ncpus=1:mem=1G
#PBS -l walltime=1:00:00
#PBS -A  "PotatoTool-BI"

module load Python/3.8.3 HTSlib/1.7 Java/1.8.0 gcc/4.8.1 bzip2/1.0.6
module load bcftools/1.10.2
module load Perl/5.32.1
module load SamTools/1.6

file=Semlo
cd /gpfs/--/Hap10_analysis/$file
p2="/gpfs/project/baign/Final_analysis_array/src/Hap10/utilities"
javat2=/gpfs/--/src/H-PoPG-master/H-PoPGv0.2.0.jar
path=/gpfs/--/Hap10_analysis/$file

for x in {Chr01,Chr02,Chr03};
do
cd /gpfs/--/Hap10_analysis/$file/$x
echo "#!/bin/bash
#PBS -l select=1:ncpus=1:mem=40G
#PBS -l walltime=167:00:00
#PBS -A "PotatoTool-BI"
module load Python/3.8.3 HTSlib/1.7 Java/1.8.0 gcc/4.8.1 bzip2/1.0.6
module load bcftools/1.10.2
module load Perl/5.32.1
module load SamTools/1.6
cd /gpfs/project/baign/Project_2_population_structure/VCFS_22_april_2021/samtools/01_All_chrs_16_juli_2021/Hap10_analysis/$file/$x

##Getting strongly connected components (last step of Hap10 pipeline)

python3 $p2/extract_scc.py linked_fragment_file_sp.txt cc $path/$x/frag_cc

wait

ls frag_cc* > list.txt
wait

sort -V list.txt > out.txt
wait

java -jar $javat2 -p 4 -v $path/${x}.vcf -b $path/${x}.bam -f out.txt  -d phased_${x}_cc_allins_haps.txt
" > /gpfs/--/Hap10_analysis/$file/assembly.${x}.sh

cd /gpfs/--Hap10_analysis/$file
qsub assembly.${x}.sh
done
```
```diff
+ Note: Save the afforementioned script in a global_assembly.sh file and do qsub global.sh, it will generate 1 script for each Chromosome
```

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
