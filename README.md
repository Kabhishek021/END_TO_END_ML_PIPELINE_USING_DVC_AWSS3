

# DVC Pipeline Setup and Experimentation

This repository demonstrates the setup of a **DVC (Data Version Control)** pipeline along with integration for tracking experiments and adding remote storage on **S3**. Follow the steps below to replicate the pipeline setup and experiment with model training.

## Prerequisites

Before you begin, ensure that you have the following tools installed:

* **Git**: Version control for the repository.
* **DVC**: For data version control and pipeline management.
* **AWS CLI**: For accessing AWS services, including S3.
* **dvclive**: For tracking and visualizing experiments.

Install DVC and AWS CLI using the following commands:

```bash
pip install dvc[s3]
pip install awscli
pip install dvclive
```

## Steps to Set Up the Pipeline

### 1. **Create GitHub Repository and Clone Locally**

Create a new repository on GitHub and clone it to your local machine. Add necessary experiments to the repository.

```bash
git init
git clone <your-repo-url>
```

### 2. **Add `src` Folder and Components**

Create a `src` folder to house the different components of your pipeline. You can run them individually as needed.

### 3. **Add Directories to `.gitignore`**

To prevent large files and model data from being version-controlled, add the following directories to `.gitignore`:

```txt
data/
models/
reports/
```

### 4. **Commit and Push Changes**

After setting up the directories and files, add, commit, and push your changes:

```bash
git add .
git commit -m "Initial setup"
git push
```

---

## Setting Up DVC Pipeline

### 5. **Create `dvc.yaml` File**

Create a `dvc.yaml` file and define the stages of the pipeline. This will track the data flow from ingestion, feature engineering, model building, etc.

### 6. **Initialize DVC and Test Pipeline**

Run the following commands to initialize DVC and test pipeline automation.

```bash
dvc init
dvc repro  # Test the pipeline
dvc dag    # Check the pipeline visualization
```

### 7. **Commit and Push DVC Setup**

```bash
git add .
git commit -m "DVC pipeline setup"
git push
```

---

## Setting Up DVC Pipeline with Parameters

### 8. **Add `params.yaml` File**

Create a `params.yaml` file to define hyperparameters for your pipeline (e.g., test size, max features).

### 9. **Modify the Pipeline to Use Params**

Update your pipeline code to use the parameters from `params.yaml`. Example:

```python
import yaml

def load_params(params_path: str) -> dict:
    """Load parameters from a YAML file."""
    try:
        with open(params_path, 'r') as file:
            params = yaml.safe_load(file)
        logger.debug('Parameters retrieved from %s', params_path)
        return params
    except FileNotFoundError:
        logger.error('File not found: %s', params_path)
        raise
    except yaml.YAMLError as e:
        logger.error('YAML error: %s', e)
        raise
    except Exception as e:
        logger.error('Unexpected error: %s', e)
        raise

params = load_params(params_path='params.yaml')
test_size = params['data_ingestion']['test_size']
max_features = params['feature_engineering']['max_features']
```

### 10. **Re-run the Pipeline**

Test the pipeline with the parameters loaded from `params.yaml`.

```bash
dvc repro
```

### 11. **Commit and Push Changes**

```bash
git add .
git commit -m "DVC pipeline with parameters"
git push
```

---

## Experimentation with DVC

### 12. **Install `dvclive` for Experiment Tracking**

```bash
pip install dvclive
```

### 13. **Add Experiment Code**

Add the following code block to track your experiments with DVC.

```python
from dvclive import Live
import yaml

# Load parameters
params = load_params('params.yaml')

with Live(save_dvc_exp=True) as live:
    live.log_metric('accuracy', accuracy_score(y_test, y_pred))
    live.log_metric('precision', precision_score(y_test, y_pred))
    live.log_metric('recall', recall_score(y_test, y_pred))
    
    live.log_params(params)
```

### 14. **Run DVC Experiment**

Run the experiment and create a new `dvc.yaml` file, along with the `dvclive` directory.

```bash
dvc exp run
```

### 15. **View Experiments**

Use the following command to view the experiments:

```bash
dvc exp show
```

Alternatively, you can install the DVC extension for VSCode to view experiments in the editor.

### 16. **Remove/Apply Experiments**

To remove an experiment:

```bash
dvc exp remove <exp-name>
```

To reproduce a previous experiment:

```bash
dvc exp apply <exp-name>
```

### 17. **Modify Parameters and Re-run Experiments**

Make changes to the `params.yaml` file, then re-run the pipeline to produce new experiments.

### 18. **Commit and Push Experiment Changes**

```bash
git add .
git commit -m "Experiment with new parameters"
git push
```

---

## Adding Remote S3 Storage to DVC

### 19. **Login to AWS Console**

Log in to the AWS console and create an **IAM user** and an **S3 bucket**.

### 20. **Install DVC and AWS CLI**

```bash
pip install dvc[s3]
pip install awscli
```

### 21. **Configure AWS CLI**

Run the following command to configure AWS CLI with your credentials:

```bash
aws configure
```

### 22. **Add S3 Remote Storage to DVC**

```bash
dvc remote add -d dvcstore s3://<your-bucket-name>
dvc remote modify dvcstore access_key_id <your-access-key-id>
dvc remote modify dvcstore secret_access_key <your-secret-access-key>
```

### 23. **Commit and Push Experiment Results**

After running the experiments and achieving the desired outcomes, commit and push the results:

```bash
dvc commit
dvc push
git add .
git commit -m "Pushed experiment results to S3"
git push
```

---

## Conclusion

This setup demonstrates how to build and manage a DVC pipeline, experiment tracking with **dvclive**, and integrate remote S3 storage for storing model and data artifacts. Following these steps will help you manage your data science and machine learning workflows in a more organized and reproducible manner.

---

Let me know if you'd like to make any adjustments or add more details!

