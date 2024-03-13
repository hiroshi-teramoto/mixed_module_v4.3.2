# mixed_module_v4.3.2
The mixed algorithm implemented in the latest version of Singular (v.4.3.2) of the paper [Comprehensive Standard System for Generalized Mixed Module and its Application to Singularity Theory](https://www.worldscientific.com/doi/abs/10.1142/S0219498824502219?journalCode=jaa) by Hiroshi Teramoto and Katsusuke Nabeshima (In what follows, we denote this paper as the paper.)

To use the libraries, download all the files and place them into a single directory. You will need Singular installed on your machine. We have prepared three examples to demonstrate the libraries:

* [example_codimension_transverse_fold.exe](https://github.com/hiroshi-teramoto/mixed_module_v4.3.2/blob/main/example_codimension_transverse_fold.md)
  - See 3.1.1 Example (transverse fold) in the paper
* example_codimension_beak_to_beak.exe
  - See 3.1.2 Example (beak to beak) in the paper
* [example_divergent_diagram_j1.exe](https://github.com/hiroshi-teramoto/mixed_module_v4.3.2/blob/main/example_divergent_diagram_j1.md)
  - See 4.1.1 Example: Mather's lemma
* [example_complete_transversal.exe](https://github.com/hiroshi-teramoto/mixed_module_v4.3.2/blob/main/example_complete_transversal.md)
  - See 4.1.2 Example: [complete transversal](https://iopscience.iop.org/article/10.1088/0951-7715/10/1/017) (with the degree filtration for class 3 in Table.~1 in the paper)
* [example_Ae_quotients.exe](https://github.com/hiroshi-teramoto/mixed_module_v4.3.2/blob/main/example_Ae_codimension.md)
  - Examples to illustrate the computation of the quotients of Ae-tangent space (coming soom!)

To run the codes, in the directory, type 

```
singular example_codimension_transverse_fold.exe
```

We assume you are familiar with Singular. If not please refer to [the singular manual](https://www.singular.uni-kl.de/Manual/4-3-2/index.htm#SEC_Top). If you use windows, one of the ways to install Singular is to install [cygwin](https://www.cygwin.com/) inclusing Singular packages. You can download Singular from [here](https://www.singular.uni-kl.de/index.php/singular-download.html).
