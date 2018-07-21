# Ancestral sequence reconstruction using TreeTime

At the core of TreeTime is a class that models how sequences change along the tree.
This class allows to reconstruct likely sequences of internal nodes of the tree.
On the command-line, ancestral reconstruction can be done via the command
```bash
treetime ancestral --aln data/h3n2_na/h3n2_na_20.fasta --tree data/h3n2_na/h3n2_na_20.nwk --outdir ancestral_results
```
This command will generate the output
```
0.00	-TreeAnc: set-up

Inferred GTR model:
Substitution rate (mu): 1.0

Equilibrium frequencies (pi_i):
  A: 0.2986
  C: 0.1985
  G: 0.235
  T: 0.2578
  -: 0.01

Symmetrized rates from j->i (W_ij):
	A	C	G	T	-
  A	0	0.8235	2.8227	0.4505	1.024
  C	0.8235	0	0.5666	2.8316	1.0489
  G	2.8227	0.5666	0	0.6066	1.0391
  T	0.4505	2.8316	0.6066	0	1.0347
  -	1.024	1.0489	1.0391	1.0347	0

Actual rates from j->i (Q_ij):
	A	C	G	T	-
  A	0	0.2459	0.8429	0.1345	0.3058
  C	0.1635	0	0.1125	0.5622	0.2082
  G	0.6633	0.1331	0	0.1426	0.2442
  T	0.1162	0.7301	0.1564	0	0.2668
  -	0.0103	0.0105	0.0104	0.0104	0

--- alignment including ancestral nodes saved as
	 h3n2_na_20_ancestral.fasta

--- tree saved in nexus format as
	 h3n2_na_20.fasta_mutation.nexus
```
TreeTime has inferred a GTR model and used it to reconstruct the most likely ancestral sequences.
The reconstructed sequences will be written to a file ending in `_ancestral.fasta` and a tree with mutations mapped to branches will be saved in nexus format in a file ending on `_mutation.nexus`.
Mutations are added as comments to the nexus file like `[&mutations="G27A_A58G_A745G_G787A_C1155T_G1247A_G1272A"]`.


## Using VCF files
In addition to standard fasta files, TreeTime can ingest sequence data in form of vcf files which is common for bacterial data sets where short reads are mapped against a reference and only variable sites are reported.
In this case, an additional argument specifying the mapping reference is required.
```bash
treetime ancestral --aln data/tb/lee_2015.vcf.gz --vcf-reference data/tb/tb_ref.fasta --tree data/tb/lee_2015.nwk
```


## Additional options

 * `--marginal`: by default, TreeTime will determine the *jointly* most likely sequences. In some cases, the *marginally* most likely sequences (that is after summing over all possible sequences at all other nodes) are more appropriate. Typically marginal and joint reconstruction will give similar results.
 * `--gtr` and `--gtr-params`: by default, TreeTime will infer a GTR model. A few default ones can be specified via command-line arguments, for example `--gtr K80 --gtr-params kappa=0.2 pis=0.3,0.25,0.25,0.2` would specify the model K80 with a tv/ts ratio of 0.2 and a set of nucleotide frequencies.
 * `--report-ambiguous`: by default, sequence changes involving characters indicating ambiguous states (like 'N') are not included in the output. To include them, add this flag.

Documentation of the complete set of options is available by typing
```bash
treetime ancestral -h
```
which in this case yields
```
usage: TreeTime: Maximum Likelihood Phylodynamics ancestral
       [-h] [--aln ALN] [--tree TREE] [--gtr GTR]
       [--gtr-params GTR_PARAMS [GTR_PARAMS ...]] [--marginal] [--zero-based]
       [--keep-overhangs] [--verbose VERBOSE] [--vcf-reference VCF_REFERENCE]
       [--report-ambiguous]

Reconstructs ancestral sequences and maps mutations to the tree. The output
consists of a file ending with _ancestral.fasta with ancestral sequences and a
tree ending with _mutation.nexus with mutations added as comments like
_A45G_..., number in SNPs used 1-based index by default. The inferred GTR
model is written to stdout.

optional arguments:
  -h, --help            show this help message and exit
  --aln ALN             fasta file with input sequences
  --tree TREE           Name of file containing the tree in newick, nexus, or
                        phylip format. If none is provided, treetime will
                        attempt to build a tree from the alignment using
                        fasttree, iqtree, or raxml (assuming they are
                        installed)
  --gtr GTR             GTR model to use. Type 'infer' to infer the model from
                        the data. Alternatively, specify the model type. If
                        the specified model requires additional options, use '
                        --gtr-params' to specify those.
  --gtr-params GTR_PARAMS [GTR_PARAMS ...]
                        GTR parameters for the model specified by the --gtr
                        argument. The parameters should be feed as 'key=value'
                        list of parameters. Example: '--gtr K80 --gtr-params
                        kappa=0.2 pis=0.25,0.25,0.25,0.25'. See the exact
                        definitions of the parameters in the GTR creation
                        methods in treetime/nuc_models.py or
                        treetime/aa_models.py
  --marginal            marginal reconstruction of ancestral sequences
  --zero-based          zero based mutation indexing
  --keep-overhangs      do not fill terminal gaps
  --verbose VERBOSE     verbosity of output 0-6
  --vcf-reference VCF_REFERENCE
                        fasta file of the sequence the VCF was mapped to
  --report-ambiguous    include transitions involving ambiguous states

```
