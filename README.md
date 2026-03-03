# 🛡️ Multi-Class Threat Detection System

> An AI-powered content moderation system that classifies online text into Safe, Hate Speech, and Real Threats using dual-model architecture (SVM + BERT) with interactive web deployment.

[![Python](https://img.shields.io/badge/Python-3.8+-blue.svg)](https://www.python.org/)
[![Scikit-learn](https://img.shields.io/badge/Scikit--learn-1.0+-orange.svg)](https://scikit-learn.org/)
[![Transformers](https://img.shields.io/badge/Transformers-4.0+-green.svg)](https://huggingface.co/transformers/)
[![Gradio](https://img.shields.io/badge/Gradio-3.0+-red.svg)](https://gradio.app/)

---

## 📖 Project Overview

Online platforms face increasing challenges in moderating user-generated content. Manual review is neither scalable nor cost-effective for millions of daily messages. This project builds an automated threat detection system that distinguishes between harmless content, offensive language, and actual violent threats.

**Key Features:**
- 3-class classification: Safe, Hate Speech, Real Threat
- Dual-model approach: Fast SVM + Accurate BERT
- Trained on 159,571 Wikipedia comments
- Interactive web application with Gradio
- Batch CSV processing (up to 500 messages)
- Model comparison interface

---

## 🎯 Problem Statement

**Challenge:** Social media platforms receive millions of messages daily. Current systems either:
- Use keyword blocking (easily bypassed)
- Apply binary toxic/safe classification (insufficient granularity)
- Cannot balance speed and accuracy

**Solution:** A dual-model system that lets users choose between:
- **SVM**: Lightning-fast inference (0.001s), 88-91% accuracy
- **BERT**: Context-aware analysis (0.1s), 94% accuracy

---

## 📊 Dataset

**Source:** Jigsaw Toxic Comment Classification Challenge  
**Size:** 159,571 Wikipedia comments  
**Original Labels:** 6 binary toxicity indicators (toxic, severe_toxic, obscene, threat, insult, identity_hate)  

**3-Class Conversion:**
- **Class 0 (Safe)**: All labels = 0 → 143,346 samples (89.8%)
- **Class 1 (Hate Speech)**: Any toxic label BUT threat = 0 → 15,747 samples (9.9%)
- **Class 2 (Real Threat)**: threat = 1 → 478 samples (0.3%)

**Final Split:**
- Training: 29,997 samples (balanced: 10k per class)
- Testing: 31,912 samples (natural distribution)

---

## 🛠️ Technologies Used

| Category | Tools |
|----------|-------|
| **Language** | Python 3.8+ |
| **ML Libraries** | Scikit-learn, Transformers, PyTorch |
| **Data Processing** | Pandas, NumPy |
| **Visualization** | Matplotlib, Seaborn |
| **NLP** | NLTK, TF-IDF Vectorizer, BERT Tokenizer |
| **Deployment** | Gradio |
| **Platform** | Google Colab (Tesla T4 GPU) |

---

## 🔬 Methodology

### 1. Data Preprocessing
- Combined 6 binary labels into 3-class taxonomy
- Stratified train-test split (80/20) **before** balancing
- Balanced training data only (prevents data leakage)
- Text cleaning: lowercase, URL removal, special character handling

### 2. Feature Engineering

**For SVM:**
- TF-IDF vectorization (10,000 features)
- 1-3 word n-grams
- Captures threat phrases like "will kill", "bomb threat"

**For BERT:**
- Tokenization (max 128 tokens)
- Pre-trained bert-base-uncased (110M parameters)
- Fine-tuned for 3 epochs on threat classification

### 3. Model Training

**SVM Model:**
```python
LinearSVC(C=0.5, class_weight='balanced')
CalibratedClassifierCV(cv=3)  # For probability estimates
```
- Training time: 5 seconds
- Inference: 0.001s per message

**BERT Model:**
```python
BertForSequenceClassification(num_labels=3)
Training: 3 epochs, batch_size=16, Tesla T4 GPU
```
- Training time: 43 minutes
- Inference: 0.1s per message

---

## 📈 Results

### Model Performance

| Metric | SVM | BERT | Improvement |
|--------|-----|------|-------------|
| **Accuracy** | 88-91% | 94.07% | +3-6% |
| **Macro F1** | 0.72 | 0.74 | +2.8% |
| **Threat Recall** | 85-90% | 85-90% | Similar |
| **Inference Speed** | 0.001s | 0.1s | 100x faster (SVM) |
| **Model Size** | 10 MB | 440 MB | 44x smaller (SVM) |

### Sample Predictions

| Input Text | SVM | BERT | Correct? |
|------------|-----|------|----------|
| "I will kill you tomorrow" | Threat (98.7%) | Threat (99.6%) | ✅ |
| "You're a fucking idiot" | Hate (99.9%) | Hate (99.9%) | ✅ |
| "Thanks for your help!" | Safe (98.5%) | Safe (99.9%) | ✅ |
| "You're killing it!" (slang) | Hate (65%) ❌ | Safe (89%) ✅ | Context matters! |

### Key Insights

✅ **BERT understands context better** (e.g., figurative language)  
✅ **SVM is 100x faster** for high-volume processing  
✅ **Both catch 85-90% of real threats** with <1% false alarms  
✅ **Dual-model approach** enables use-case flexibility  

---

## 🌐 Live Demo

The system is deployed using **Gradio** with three main features:

### 1. Single Text Classification
- Real-time prediction
- Confidence scores for all 3 classes
- Choose between SVM (fast) or BERT (accurate)

### 2. Batch CSV Processing
- Upload CSV files (up to 500 messages)
- Auto-detects text column
- Download results with predictions

### 3. Model Comparison
- Side-by-side SVM vs BERT predictions
- Compare performance on same input

---

## 🚀 Quick Start

### Prerequisites
```bash
Python 3.8+
pip
Google Colab (optional, for GPU training)
```

### Installation
```bash
# Clone the repository
git clone https://github.com/yourusername/threat-detection.git
cd threat-detection

# Install dependencies
pip install -r requirements.txt
```

### Run Locally
```bash
# Launch Gradio app
python app.py

# Open browser at http://localhost:7860
```

### Test with Sample Data
```python
from predict import predict_threat

# Test SVM
result = predict_threat("I will attack tomorrow", model="SVM")
print(result)

# Test BERT
result = predict_threat("You're killing it!", model="BERT")
print(result)
```

---

## 📂 Project Structure
```
threat-detection/
│
├── notebooks/
│   └── threat_detection_complete.ipynb    # Full training pipeline
│
├── models/
│   ├── threat_detector_svm.pkl            # Trained SVM model
│   ├── tfidf_vectorizer.pkl               # TF-IDF vectorizer
│   ├── bert_model/                        # Saved BERT model
│   └── bert_tokenizer/                    # BERT tokenizer
│
├── app.py                                  # Gradio web app
├── predict.py                              # Prediction functions
├── requirements.txt                        # Dependencies
├── README.md                               # This file
│
└── data/
    └── sample_test.csv                    # Sample test data
```

---

## 📊 Data Handling Best Practices

### ⚠️ Important: Preventing Data Leakage

**WRONG Approach (causes leakage):**
```python
# ❌ Balance first, then split
balanced_data = balance_classes(data)
train, test = split(balanced_data)  # Same samples in train & test!
```

**CORRECT Approach (used in this project):**
```python
# ✅ Split first, then balance training only
train, test = split(data)           # Separate samples
train_balanced = balance_classes(train)
# Test set keeps natural distribution
```

This ensures:
- No overlap between train and test
- Realistic performance estimates
- Model generalizes, not memorizes

---

## 🎓 Academic Context
**Project Type:** Final Year BSc Data Science Project   
 
---

## 💡 Use Cases

This system can be applied to:

- **Social Media Platforms**: Automated content moderation
- **Online Forums**: Flag dangerous content for review
- **Customer Support**: Identify threatening messages
- **Gaming Platforms**: Detect toxic behavior
- **Corporate Communications**: Monitor employee communications

**Business Impact:**
- 85-90% threat detection rate
- <1% false positive rate
- Process 1000+ messages/second (SVM)
- Reduce manual moderation by 70%

---

## 🔮 Future Enhancements

1. **Multi-language Support**: Extend beyond English
2. **Explainability**: Add LIME/SHAP for interpretability
3. **Active Learning**: Continuous model improvement
4. **Severity Scoring**: Fine-grained threat levels
5. **Real-time Streaming**: Process live social media feeds
6. **Ensemble Methods**: Combine multiple models
7. **User Feedback Loop**: Learn from corrections

---

## 📝 Research References

This project builds upon recent research in:

- Hate Speech Detection using BERT/RoBERTa (2023-2024)
- Multi-task Learning for Toxic Comment Classification (2023)
- Ensemble Methods for Online Threat Detection (2022-2024)
- Lightweight Transformers for Real-time Moderation (2024-2025)

**Key Gap Addressed:** Most systems use single model approach. This project enables users to choose between speed (SVM) and accuracy (BERT) based on operational requirements.

---

## 🤝 Contributing

Contributions are welcome! Please:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit changes (`git commit -m 'Add AmazingFeature'`)
4. Push to branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 👨‍💻 Author

**Rakesh Y** 
 

📧 Email: rakeshrollans123@gmail.com 
🔗 LinkedIn: [linkedin.com/in/rakesh0910](https://www.linkedin.com/in/rakesh0910)  
💻 GitHub: [github.com/rakeshrollans123-ship-it](https://github.com/rakeshrollans123-ship-it)

---

## 🙏 Acknowledgments

- **Jigsaw/Conversation AI** for the Toxic Comment dataset
- **Hugging Face** for BERT pre-trained models
- **Gradio Team** for the deployment framework
- **Google Colab** for free GPU access

---



## ⭐ Star This Repository

If you found this project useful, please consider giving it a star! It helps others discover the project.

---

**Last Updated:** March 2026  
**Project Status:** 🟡 In Progress (60% Complete)
