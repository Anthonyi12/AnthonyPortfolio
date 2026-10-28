> **Note:** The results I reproduced are from Delaney's 2004 paper, *"ESOL: Estimating Aqueous Solubility Directly from Molecular Structure"*, an influential paper that changed how scientists quickly screen molecules for solubility before fully developing a drug.

## Data

I used the ESOL dataset from Delaney (2004), a standard dataset for cheminformatics and machine learning. It contains **1,128 organic molecules**, each with an experimentally measured aqueous solubility and a **SMILES string** (a short ASCII-based notation for a molecule's chemical structure).

I converted the SMILES into **canonical SMILES**, which gives each molecule a single standard representation, so I could identify duplicates. **11 molecules appeared twice**; I averaged each pair's solubility, leaving **1,117 molecules**.

I also dropped the `ESOL predicted log solubility in mols per litre` column, since it contains Delaney's predictions and would leak data into the model.

## Features

I used the same four features as Delaney. He initially considered nine features but narrowed them down to four core features:

1. **clogP**: calculated octanol–water partition coefficient
   > *Note:* I used RDKit's Crippen LogP, whereas Delaney used Daylight's clogP. This is the main reason our linear regression coefficients differ.
2. **Molecular Weight (MW)**
3. **Number of Rotatable Bonds (RB)**
4. **Aromatic Proportion (AP)**: the proportion of heavy atoms in aromatic rings

## Method

I split the dataset randomly into **80% training (893 molecules)** and **20% testing (224 molecules)**, using a fixed random state so the results are reproducible.

I used scikit-learn's `LinearRegression`, an **ordinary least squares** linear regression model, the same type of model Delaney used. As a baseline, a model predicted the training mean for every molecule.

## Results

| Model | RMSE | MAE | R² |
|---|---|---|---|
| Mean baseline | 2.040 | 1.641 | 0.000 |
| Delaney's 2004 LR (published equation) | 1.099 | 0.841 | 0.710 |
| **My model** | **1.025** | **0.775** | **0.748** |

My model explained about **75% of the variance** (R²) and reproduced Delaney's findings. Delaney reported a **5- to 8-fold** error, while my model's typical error was about **6-fold**.
