# ML Project Template

This is a template folder and file structure to be used for ML projects. Code within files is for understanding what sort of code should be written only.

## Folders and Files Explained

### Configuration Files
These seperate parameters from the code making it possible to run different settings for different environments, experiments or deployment scenarios. The directoryshould contain environment-specific config files. The files should specify data paths, model architectures, training procedures, evaulation metrics, and deployment settings. This makes the entire ML pipeline configurable without code changes, which is essential for experimentation and A/B testing.

Most ML projects need mutiple config strategies. You may have different configs for different data sources, model types, or business use cases. The key principle is that no parameter is hard coded into the source code and that everything that may need to change like file paths, model parameters, processing options...should be externalised to config files making i more flexible and experiments reproducible.

local.yaml: Contains settings optimised for local development, smaller datasets, fewer epochs, simpler models that train quickly

prod.yaml: Contains production settings, full datasets, optimised hyperparameters, and configs that prioritise accuracy over development speed

### Data Files
The data directory structure makes your data processing pipeline visible and understandable. The data folders are number from 01, 02...to create a clear flow from raw data to processing, transformations, and to predictions. This staged approach to data management prevents common issues like data leakage, makes debugging easier and enables efficient pipeline optimisation. You can cache expensive computations at each stage and only re-run what's necessary when upstream changes occur.

01-raw/: Contains the original, unmodified data exactly as received from source systems. This data should not be modified. This immutability is crucial for debugging and reprodibility as when something goes wrong downstream you can trace back to the original data

02-preprocessed/: Contains data that has been cleaned and standardised, but not yet feature-engineered. This may include missing value imputation, outlier removal, data type conversions, and basic normalisation. Pre-processingand feature engineering should be kept seperate

03-features/: Contains the final feature matrices ready for model training. This includes all feature engineering, encoding, reductions, scaling and selection. Having this seperate allows you to experiment with different models on the same features without re-running the entire feature engieering pipeline

04-predictions/: Stores the model outputs, predictions and evaluation results. This creates a clear audit trails of what the model predicts and when. It also enables offline evaluation and comparison between different models or model versions

### Entry Point Scripts
The entry point scripts are the publinc interface to the ML system. They should be simple, focused and provide clear command-line interfaces for training models and making predictions. These scripts orchestrate the ML pipelines but don't contain business logic themselves. They are responsible for setting up the runtime environment that your ML pipelines need to execute successfully.

train.py: Should handle the complete model training workflow-data loading, preprocessing, feature engineering, model training, evaluation and artifact saving. It should accept config files as arguments and provide clear feedback about training progress and results

inference.py: Handles prediction workflows. This may include batch prediction on large datasets, real-time inference APIs, or interactive prediction interfaces depending on the intended use.

### Notebooks
These serve a specific prupose in the ML workflow of exploration of the dataset, prototyping and communication. They are perfect for understanding the data, trying new approaches and sharing insights with stakeholders. However, they should never contain production logic or be part of the deployment pipeline.

Notebook management requires discipline. Clear outputs before commiting to version control to avoid merge conflicts and repo bloat. Use descriptive names that explain what the notebook investigates. Make sure to add markdown cells that explain the thinking process not just code.

Remeber the notebooks are for documenting the thought process and investigations. When you discover something valuable in a notebook, you need to immediately extract the logic into a proper module with tests and documentation.

### Source Pipelines
The src/pipelines/ directory contains the reusable, production-ready ML code organised into logical pipelines. This is where the notebook exploration becomes robust, testable, production code. Each pipeline should be independent testable and should not have hidden dependencies on other components. They should accept standardised inputs and produce standardised outputs making them reusable. This modular design enables parallel development, easier debugging and more flexible deployment.

Pipelines should also implement proper error handing and logging. They should fail fast when given invalid inputs, provide error messages whenthings go wrong and long enough info to debug issues without being too long. They should also implement circuit breaker patterns for external dependencies and graceful degradation when optional components fail.

training_pipeline.py: Orchestrates the complete model training process. Pulls together data loading, preprocessing, feature engineering, model training, evaulation and artifcant persistance. The pipeline should be configurable through config files and should handle errors with comprehensive logging. It should also resume from checkpoints for long-running training jobs


inference_pipeline.py: Handles prediction workflows with different requirements that training. Inference needs to be fast, reliable and handle edge cases that may not appear in training data. The pipeline should validate input data, impute missing features and provide confidence estimates along with predictions. I should also support both batch and streaming inference patterns

feature_eng_pipeline.py: Manages feature creation and transformation. This pipeline should be deterministic and reproducible, creating identical features from identicial inputs. It should handle both training-time feature engineering (where you can compute stats from the full dataset) and inference-time feature engineering (where you can only use info available at prediction time)

### Tests
Testing ML code presents unique challenges compared to normal software testing. testing ML systems requires careful though about test data. You need datasets that are large enough to be meaningful but small enough to run quickly in your test suite. You need data that covers important edge cases without including sensitive info. Many teams maintain curated test datasets that are specifically designed for automated testing.

Unit tests: Focus on deterministic components of the ML system like data processing functions, feature engineering logic, evaluation metrics and utility functions. These tests should run quikcly and should not depend on external data or services. They should cover edge cases and error conditions that are difficult to test in end-toend workflows.

Integration tests: Verify that your pipelines work together correctly. These tests might use small sample datasets to verify that training produces valid models and that inference produces sensible predictions. They should test the complete workflow from data loading to prediction output.

### Docker Files
Docker is a containerisation platform that lets you package apps with all their dependencies into standardised, isolated units called containers. Unlike virtual machines, containsers share the host OS while remaining portable, lightweight and constistent across environments. This makes Docker useful for ML projects where reproduciblility and environment constency are critical.

Dockerfile: Defines the app container with all needed dependencies, system packages and runtime config. Multi-stage builds can seperate development tools from production runtime, keeping production images lean and secure. The Dockerfile should be deterministic, producing identical containers from the same source code regardless of where it is built

docker-compose.yml: Defines multi-service development ebvironments, including databases, message queues, and external APIs the system depends on. This enables developers to spin up complete development envs with a single command, ensuring everyone works with the same architecture. Will do one for the training service, inference service...

### Continuous Integration Workflows
When the ML prokect grows beyond a single person, testing and development can't rely on manual effort anymore. There were CI come in. A CI workflow auto checks the project whenever code changes are pushed-running tests, linting code, building docker images or even evaluating data pipelines.

The ci/ directory contains config files for CI/CD tools like GitHib Actions, GitLab CI or Jenkins. These Yaml-based files describe jobs that should run automatically like installing dependencies, running unit tests, building artifacts or deploying models to staging environments. Typical jobs you may include in a CI file for ML projects may be code quality (run linting), unit and integration tests, docker builds, artifact versioning and deployment triggers.

A good CI setup makes sure the ML pipeline remains reproducible and stable. It catches errors early, enforces coding standards across the team and makes sure every change is tested in the same way. This is especially critical in ML systems, where experiments can break silently if preprocessing, feature engineering or evaluation logic is altered.

### Environment and Requirement Files
Env conistency is one of the biggest challenges in ML development. Models that work on one machine may not work on another due to different package versions, hardware configs or OS differences. The solution is rigorous env management through containerisation and dependency specification.

env.yaml and env-dev.yaml define envs for different usage scenarios (conda envs). Normally in development you may need more packages than in production due to different models potentially tried, visualisation....

requirements-prod.txt and requirements-dev.txt serve similar purposes for pip-based envs. Production reqs should be minimal and locked to specific versions. Development reqs can include additional tolls for testing, debugging and analysis. It's crucial to specify the exact version of the libraries that you use. Otherwise, your results will be non-reproducible and you'll have dependencies conflicts across different machines and environments.

---

## Project Structure
![ML Project Structure](./images/project_structure.png)
