# mri-shortcut-audit

Investigating whether CNNs trained on brain MRI tumor classification 
rely on clinically meaningful features or dataset-specific artifacts.

## Setup
pip install torch torchvision scikit-learn opencv-python matplotlib

## Usage
1. Place dataset in data/yes/ and data/no/
2. python train.py
3. python evaluate.py
4. python gradcam.py
5. python intervention_train.py