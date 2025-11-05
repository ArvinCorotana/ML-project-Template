# ML Project Template

This is a template folder and file structure to be used for ML projects. Code within files is for understanding what sort of code should be written only.

## Folders and Files Explained

### Configuration Files
These seperate parameters from the code making it possible to run different settings for different environments, experiments or deployment scenarios. The directoryshould contain environment-specific config files. The files should specify data paths, model architectures, training procedures, evaulation metrics, and deployment settings. This makes the entire ML pipeline configurable without code changes, which is essential for experimentation and A/B testing.

local.yaml: Contains settings optimised for local development, smaller datasets, fewer epochs, simpler models that train quickly\
prod.yaml: Contains production settings, full datasets, optimised hyperparameters, and configs that prioritise accuracy over development speed

---

## Project Structure
![ML Project Structure](./images/project_structure.png)
