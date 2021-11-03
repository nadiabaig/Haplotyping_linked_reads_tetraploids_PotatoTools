<br />
<p align="center">
  <a href="https://github.com/roshanlam/ReadMeTemplate/">
    <img src="./logo.png" alt="Logo" width="80" height="80">
  </a>

  <h3 align="center">Read Me Template</h3>

  <p align="center">
   Haplotyping_linked_reads_tetraploids_PotatoTools
    <br />
    <a href="https://github.com/roshanlam/ReadMeTemplate/"><strong>Explore the docs »</strong></a>
    <br />
    <br />
  </p>
</p>

<!-- ABOUT THE PROJECT -->
## Haplotyping SNP data from linked reads for tetraploids
### For QGGP's RIBS meeting only

This repository provides the bash script , I used for my 10x datasets to get the haplotype blocks for tetraploid genotypes using Hap++ pipeline in combination with H-PoPG (see the reference for github profile and paper).


Main steps of the pipeline inludes:
* Splitting the vcf file (multi-sample VCF) into per sample per chromosome VCF as the Hap10 pipeline works for 1 chromosome at a time
* Filtering the VCF by removing homozygous and non-SNP variants in tetraploids
* 
* For Haplotype assembly Hap10 pipeline uses SDhap tool but,it is not easy to install. It may takes few days! 
It needs dependency ATLAS (Automatically Tuned Linear Algebra Software) libraries and also need lapack. TO INSTALL it on our institutes server it needs disabling CPU throttling. From our institute 'Intel MKL' is available, but you need to bulid it by yourself using module load  "intel/xe2020.4". (Not tried this alterantive-- In case anyone is interested try your luck)..
* Haplotype assembly using H-PoPG



<!-- GETTING STARTED -->
## Getting Started

This is an example of how you may give instructions on setting up your project locally.
To get a local copy up and running follow these simple example steps.

### Prerequisites

This is an example of how to list things you need to use the software and how to install them.
* npm
```sh
npm install npm@latest -g
```

### Installation

1. Get a free API Key at [https://example.com](https://example.com)
2. Clone the repo
```sh
git clone https://github.com/your_username_/Project-Name.git
```
3. Install NPM packages
```sh
npm install
```
4. Enter your API in `config.js`
```JS
const API_KEY = 'ENTER YOUR API';
```



<!-- USAGE EXAMPLES -->
## Usage

Use this space to show useful examples of how a project can be used. Additional screenshots, code examples and demos work well in this space. You may also link to more resources.

_For more examples, please refer to the [Documentation](https://example.com)_



<!-- ROADMAP -->
## 🚧 Roadmap

See the [open issues](https://github.com/roshanlam/ReadMeTemplate/issues) for a list of proposed features (and known issues).



<!-- CONTRIBUTING -->
## 🤝 Contributing

Contributions are what make the open source community such an amazing place to be learn, inspire, and create. Any contributions you make are **extremely appreciated**.

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request



<!-- LICENSE -->
## 📝 License
Describe your License for your project. 

Distributed under the MIT License. See `LICENSE` for more information.



<!-- CONTACT -->
## 📫 Contact

Your Name - [@your_twitter](https://twitter.com/your_username) - email@example.com

Project Link: [https://github.com/your_username/repo_name](https://github.com/your_username/repo_name)



<!-- ACKNOWLEDGEMENTS -->
## Acknowledgements
* [Img Shields](https://shields.io)
* [GitHub Pages](https://pages.github.com)
* [Font Awesome](https://fontawesome.com)
* blah blah blah....





<!-- MARKDOWN LINKS & IMAGES -->
<!-- https://www.markdownguide.org/basic-syntax/#reference-style-links -->
[forks-shield]: https://img.shields.io/github/forks/roshanlam/ReadMeTemplate?style=for-the-badge
[forks-url]: https://github.com/roshanlam/ReadMeTemplate/network/members
[stars-shield]: https://img.shields.io/github/stars/roshanlam/ReadMeTemplate?style=for-the-badge
[stars-url]: https://github.com/roshanlam/ReadMeTemplate/stargazers
[issues-shield]: https://img.shields.io/github/issues/roshanlam/ReadMeTemplate?style=for-the-badge
[issues-url]: https://github.com/roshanlam/ReadMeTemplate/issues
[linkedin-shield]: https://img.shields.io/badge/-LinkedIn-black.svg?style=flat-square&logo=linkedin&colorB=555
[linkedin-url]: https://linkedin.com/in/roshan-lamichhane


Note: In case you have diploid samples you can go with the following pipeline.
https://github.com/vibansal/HapCUT2



# References
1. https://github.com/smajidian/Hap10
2. Majidian, S., Kahaei, M.H. & de Ridder, D. Hap10: reconstructing accurate and long polyploid haplotypes using linked reads. BMC Bioinformatics 21, 253 (2020). https://doi.org/10.1186/s12859-020-03584-5
3. https://github.com/MinzhuXie/H-PoPG
4. 
