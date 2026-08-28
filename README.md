# AI-Based Identification of Novel Drug Targets for Depression

This repository contains the code, notebooks, data, and results for my dissertation project on identifying and prioritising potential drug-target interactions associated with depression, using a combination of classical machine learning, protein language models, graph neural networks, and molecular docking.

## Project overview

The workflow is organised into ten stages, each corresponding to a notebook and a stage in the reproducible pipeline script:

1. **Depression-associated gene analysis** - exploratory analysis of genes linked to depression
2. **Protein characterisation** - analysis of proteins associated with those genes
3. **Drug-target interaction analysis** - preparing a clean drug-target interaction dataset
4. **Baseline Random Forest model** - first machine learning model for predicting drug-target interactions
5. **Protein embedding generation** - generating protein embeddings using the pretrained ESM-2 protein language model
6. **Feature integration** - combining drug molecular fingerprints with protein ESM-2 embeddings
6A. **AlphaFold structure preparation** - retrieving AlphaFold protein structures needed for graph-based modelling
7. **Graph Neural Network development** - building a GNN that uses protein structural information
8. **Novel drug-target prediction** - using the trained GNN to identify new candidate drug-target pairs
9. **Molecular docking validation** - validating high-confidence candidates using fpocket, Open Babel, and AutoDock Vina
10. **Model explainability** - interpreting predictions (SHAP, feature importance) and producing the final ranked candidate table

## Repository structure

```
Depression-Drug-Target-AI/
├── data/
│   ├── raw/            # Original input datasets (genes, proteins, interactions, structures)
│   └── processed/      # Cleaned datasets, embeddings, graphs, and model-ready features
├── notebooks/           # Notebooks 01–10, one per pipeline stage
├── scripts/
│   └── depression_drug_target_pipeline_final.py   # Reproducible end-to-end pipeline script
├── models/              # Saved trained models (Random Forest, GNN)
├── docking/             # Docking inputs, ligands, cleaned proteins, receptors, and docked poses
├── results/             # Evaluation metrics, predictions, and explainability outputs
├── figures/             # All generated plots and figures used in the dissertation
├── requirements.txt
├── .gitignore
└── README.md
```

## Key results

- **Baseline Random Forest model:** Accuracy 0.857, Precision 0.850, Recall 0.926, F1 0.886, ROC-AUC 0.930
- **Graph Neural Network model:** Accuracy 0.861, Precision 0.876, Recall 0.896, F1 0.886
- **Molecular docking validation:** the top 10 GNN-predicted candidates were all successfully docked with valid negative binding affinities, ranging from -5.25 to -7.69 kcal/mol
- The final ranked candidate table combining GNN prediction probability, docking affinity, and pocket druggability is in `results/explainability/dissertation_final_candidate_table.csv`

## Running the pipeline

The notebooks were originally developed and run in Google Colab (they import `google.colab.drive` and `google.colab.files` for mounting storage and file uploads). To run them locally, remove or replace those Colab-specific cells with local file paths.

The standalone script can also run the whole workflow, or one stage at a time:

```bash
python scripts/depression_drug_target_pipeline_final.py --stage all
```

```bash
python scripts/depression_drug_target_pipeline_final.py --stage 07
```

Stages should be run in numerical order on a first run, since later stages depend on outputs from earlier ones (for example, stage 07 needs the integrated features produced in stage 06).

The project root can be overridden with the `DEPRESSION_PROJECT_ROOT` environment variable, otherwise it is inferred from the script's location.

## External tools required

Some stages need software beyond the Python packages listed in `requirements.txt`:

- **fpocket** - pocket detection on AlphaFold protein structures (stage 09)
- **Open Babel** - file format conversion for docking (stage 09)
- **AutoDock Vina** - molecular docking (stage 09)

In the original workflow, fpocket was run through WSL. These tools need to be installed and available on the system path separately; they are not pip-installable.

## Data sources

- Depression-associated gene and protein information, and drug-target interaction data, are in `data/raw/`
- Protein structures were retrieved from AlphaFold
- Ligand structures (SDF files) were retrieved by ChEMBL ID

## Notes

- A fixed random seed is used where applicable, for reproducibility.
- Trained models are saved in `models/` (`random_forest_model.joblib`, `drug_target_gnn_model.pth`) so predictions and explainability stages can be re-run without retraining from scratch.
- This is a dissertation project and the docking results and candidate predictions are computational hypotheses. They have not been experimentally validated.

### License

The original source code in this repository is licensed under the MIT License. See the [LICENSE](LICENSE) file for the full license text.

Third-party datasets, databases, structures, and other external resources used in this project remain subject to their respective terms of use and licences. These include resources from UniProt, ChEMBL, Ensembl, and AlphaFold Database.
