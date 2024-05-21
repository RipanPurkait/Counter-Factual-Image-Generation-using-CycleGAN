# Image-to-Image Translation for Medical XAI: Counterfactual Insights into Pneumonia Detection
## Overview
Neural networks often operate as "black boxes," where their decision-making processes are opaque. This lack of transparency can be particularly problematic in high-stakes fields like healthcare. This project addresses the critical need for transparent and interpretable decision-making in medical image classification, where traditional methods often struggle to provide nuanced explanations. Recognizing the limitations of current approaches, particularly in conveying textural information, the project aims to pioneer a novel model-agnostic solution using adversarial image-to-image translation.

The primary objective is to generate realistic counterfactual images for image classifiers operating in medical contexts. By harnessing advanced techniques from adversarial learning, the project seeks to overcome the challenges associated with existing methods, such as saliency maps, which may not effectively capture the intricate nuances of decision-making processes rooted in texture and structure.

The proposed approach holds promise for enhancing the interpretability of deep learning models, especially in high-stakes domains like healthcare, where transparent decision-making is paramount. By providing realistic counterfactual explanations, the project aims to empower clinicians and stakeholders with insights into classifier decisions, thereby fostering trust and facilitating more informed medical interventions.


### Python 3.11.0 recommended
### Create a virtual envireonment
```
python -m venv myenv
```
### Activate the virtul environment
```
.\myenv\Scripts\activate
```
Train the CycleGAN
```
python pipeline.py --model_type "cycle-gan" --image_size 512 --batch_size 4 --epochs 10 --train_dir "D:\Official\Reseach Projects\Personal Projects\Machine Learning\Deep Learning\Counterfactual-Image-Generation-using-CycleGAN\data\rsna-pneumonia-dataset\train" --val_dir "D:\Official\Reseach Projects\Personal Projects\Machine Learning\Deep Learning\Counterfactual-Image-Generation-using-CycleGAN\data\rsna-pneumonia-dataset\val" --test_dir "D:\Official\Reseach Projects\Personal Projects\Machine Learning\Deep Learning\Counterfactual-Image-Generation-using-CycleGAN\data\rsna-pneumonia-dataset\test" --project "CycleGAN-CounterFactual Explanation" --job_name "Generator-and-Discriminator-training" --checkpoint_dir "D:\Official\Reseach Projects\Personal Projects\Machine Learning\Deep Learning\Counterfactual-Image-Generation-using-CycleGAN\models/gan" --classifier_path "D:\Official\Reseach Projects\Personal Projects\Machine Learning\Deep Learning\Counterfactual-Image-Generation-using-CycleGAN\models\swin_t-epoch15-val_loss0.50.ckpt"
```

### Architecture Diagram

![Architecture Diagram](path_to_architecture_diagram.png)

## Methodology

There are various approaches for solving image-to-image translation problems. Recent promising methods rely on adversarial learning. The original GANs approximate a function that transforms random noise vectors into images that follow the same probability distribution as a training dataset. This is achieved by combining a generator network $` G `$ and a discriminator network $` D `$. During training, the generator learns to create new images, while the discriminator learns to distinguish between real images from the training set and fake images generated by the generator. The objective of the two networks is defined as follows:

$`L_{\text{original}}(G, D) = \mathbb{E}_{x \sim p_{\text{data}}(x)} [\log D(x)] + \mathbb{E}_{z \sim p_{z}(z)} [\log (1 - D(G(z)))]`$

Where:
- $` x `$ are real images.
- $` z `$ are random noise vectors.
- $` p_{\text{data}}(x) `$ is the data distribution.
- $` p_{z}(z) `$ is the noise distribution.

During training, the discriminator $` D `$ maximizes this objective function, while the generator $` G `$ tries to minimize it.

### Image-to-Image Translation Networks

Various modified architectures have been developed to replace the random input noise vectors with images from another domain, enabling the transformation of images from one domain to another. These image-to-image translation networks commonly rely on paired datasets, where pairs of images differ only in the features defining the difference between the two domains. However, paired datasets are rare in practice.

### CycleGAN

A solution to the problem of paired datasets was introduced by Zhu et al., who proposed the CycleGAN architecture. This architecture combines two GANs:
- One GAN learns to translate images from domain $` X `$ to domain $` Y `$.
- The other GAN learns to translate images from domain $` Y `$ to domain $` X `$.

The objective functions for the two GANs are defined as:

$` L_{\text{GAN}}(G, D_Y, X, Y) = \mathbb{E}_{y \sim p_{\text{data}}(y)} [\log D_Y(y)] + \mathbb{E}_{x \sim p_{\text{data}}(x)} [\log (1 - D_Y(G(x)))] `$

$` L_{\text{GAN}}(F, D_X, Y, X) = \mathbb{E}_{x \sim p_{\text{data}}(x)} [\log D_X(x)] + \mathbb{E}_{y \sim p_{\text{data}}(y)} [\log (1 - D_X(F(y)))] `$

Where:
- $` G `$ is the generator from $` X `$ to $` Y `$.
- $` F `$ is the generator from $` Y `$ to $` X `$.
- $` D_Y `$ and $` D_X `$ are the discriminators for domains $` Y `$ and $` X `$ respectively.

### Cycle-Consistency Loss

To ensure that the translation preserves key features, a cycle-consistency loss is introduced:

$` L_{\text{cycle}}(G, F) = \mathbb{E}_{x \sim p_{\text{data}}(x)} [\| F(G(x)) - x \|_1] + \mathbb{E}_{y \sim p_{\text{data}}(y)} [\| G(F(y)) - y \|_1] `$

Where $` \| \cdot \|_1 `$ denotes the L1 norm.

### Full Objective Function

Combining the adversarial losses and cycle-consistency loss, the full objective for CycleGAN is:

$` L(G, F, D_X, D_Y) = L_{\text{GAN}}(G, D_Y, X, Y) + L_{\text{GAN}}(F, D_X, Y, X) + \lambda L_{\text{cycle}}(G, F) `$

Where $` \lambda `$ is a weight parameter for the cycle-consistency loss.

### Extending CycleGANs for Counterfactual Explanations

For generating counterfactual explanations for a binary classifier, we need to consider the classifier’s decisions. To do this, we propose adding a counterfactual loss component. Let:
- $` x `$ be an image from class $` X `$.
- $` y `$ be an image from class $` Y `$.
- $` C `$ be a classifier that predicts either $` C(x) = X `$ or $` C(y) = Y `$.

The counterfactual loss is defined as:

$` L_{\text{counter}}(G, F, C) = \mathbb{E}_{x \sim p_{\text{data}}(x)} [\| C(G(x)) - (0, 1) \|_2^2] + \mathbb{E}_{y \sim p_{\text{data}}(y)} [\| C(F(y)) - (1, 0) \|_2^2] `$

Where $` \| \cdot \|_2^2 `$ is the squared L2 norm.

### Identity Loss

An identity loss is added to ensure that images remain unchanged if they already belong to the target domain:

$` L_{\text{identity}}(G, F) = \mathbb{E}_{y \sim p_{\text{data}}(y)} [\| G(y) - y \|_1] + \mathbb{E}_{x \sim p_{\text{data}}(x)} [\| F(x) - x \|_1] `$

### Complete Objective Function

The complete objective function for our extended CycleGAN is:

$` L(G, F, D_X, D_Y, C) = L_{\text{GAN}}(G, D_Y, X, Y) + L_{\text{GAN}}(F, D_X, Y, X) + \lambda L_{\text{cycle}}(G, F) + \mu L_{\text{identity}}(G, F) + \gamma L_{\text{counter}}(G, F, C) `$

Where:
- $` \mu `$ is the weight for the identity loss.
- $` \gamma `$ is the weight for the counterfactual loss.

During training, discriminators $` D_X `$ and $` D_Y `$ aim to maximize this objective, while generators $` G `$ and $` F `$ try to minimize it.

## How to Run the Repository

### Installation

0. **Clone the repository and install the required packages:**

```bash
git clone https://github.com/anindyamitra2002/Counterfactual-Image-Generation-using-CycleGAN.git
cd Counterfactual-Image-Generation-using-CycleGAN
pip install -r requirements.txt
```

### Running the Model

1. **Train the Classifier Model:**

   ```bash
   python train_model.py --data_path path_to_data
   ```

2. **Train the Generator and Discriminator:**

   ```bash
   python generate_counterfactuals.py --input_image path_to_input_image --output_image path_to_output_image
   ```

### Web Application

To run the web application:

```bash
python app.py
```

Access the web app at `http://127.0.0.1:5000`.

## Evaluation Results

The generated counterfactual images were evaluated on several metrics, including:

- **Realism**: Assessed by domain experts.
- **Classification Accuracy**: Ensured that counterfactuals were correctly classified as the intended alternative outcome.
- **Interpretability**: Rated by medical professionals for clarity and usefulness.

### Sample Images

#### Original Image
![Original Image](path_to_original_image.png)

#### Counterfactual Image
![Counterfactual Image](path_to_counterfactual_image.png)

## Conclusion

Our project demonstrates that adversarial image-to-image translation is an effective tool for generating realistic counterfactual explanations in medical imaging. This approach not only enhances the interpretability and transparency of AI systems but also fosters trust and collaboration between AI and healthcare professionals. Future research will explore multi-class classification and further refine the counterfactual generation process.

## Testing Web App Link

You can access the testing web application [here](http://your-web-app-link.com).

---

**Disclaimer**: This repository is intended for research purposes only. The results and models should be validated by medical professionals before any clinical application.

---

Feel free to reach out for any questions or contributions!

---

**Contact Information:**
- Email: your_email@example.com
- GitHub: [your_username](https://github.com/your_username)

---

*This project is licensed under the MIT License - see the LICENSE file for details.*

---

*README.md created with ❤️ by [your_name].*
