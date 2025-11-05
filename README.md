# ML Project Template

This is a template folder and file structure to be used for ML projects. Code within files is for understanding what sort of code should be written only.

## Folders and Files Explained

### Configuration Files
These seperate parameters from the code making it possible to run different settings for different environments, experiments or deployment scenarios. The directoryshould contain environment-specific config files. The files should specify data paths, model architectures, training procedures, evaulation metrics, and deployment settings. This makes the entire ML pipeline configurable without code changes, which is essential for experimentation and A/B testing.\

Most ML projects need mutiple config strategies. You may have different configs for different data sources, model types, or business use cases. The key principle is that no parameter is hard coded into the source code and that everything that may need to change like file paths, model parameters, processing options...should be externalised to config files making i more flexible and experiments reproducible.

local.yaml: Contains settings optimised for local development, smaller datasets, fewer epochs, simpler models that train quickly

prod.yaml: Contains production settings, full datasets, optimised hyperparameters, and configs that prioritise accuracy over development speed

### Data Files
The data directory structure makes your data processing pipeline visible and understandable. The data folders are number from 01, 02...to create a clear flow from raw data to processing, transformations, and to predictions. This staged approach to data management prevents common issues like data leakage, makes debugging easier and enables efficient pipeline optimisation. You can cache expensive computations at each stage and only re-run what's necessary when upstream changes occur.

01-raw/: Contains the original, unmodified data exactly as received from source systems. This data should not be modified. This immutability is crucial for debugging and reprodibility as when something goes wrong downstream you can trace back to the original data

02-preprocessed/: Contains data that has been cleaned and standardised, but not yet feature-engineered. This may include missing value imputation, outlier removal, data type conversions, and basic normalisation. Pre-processingand feature engineering should be kept seperate

03-features/: Contains the final feature matrices ready for model training. This includes all feature engineering, encoding, reductions, scaling and selection. Having this seperate allows you to experiment with different models on the same features without re-running the entire feature engieering pipeline

04-predictions/: Stores the model outputs, predictions and evaluation results. This creates a clear audit trails of what the model predicts and when. It also enables offline evaluation and comparison between different models or model versions

---

## Project Structure
![ML Project Structure](./images/project_structure.png)
