# 🎓 Education Recommendation System

> **AI-Powered Career Path Recommendation System**  
> Helping students discover their ideal career path through machine learning

---

## 📋 Overview

The Education Recommendation System is an intelligent web application that analyzes student performance, study habits, and personal attributes to recommend the most suitable career paths. Built with Flask and machine learning algorithms, it provides personalized career recommendations based on comprehensive student data.

## ✨ Features

- 🎯 **Personalized Recommendations** - Get top 3 career recommendations based on your profile
- 📊 **Comprehensive Analysis** - Considers academic scores, study habits, and extracurricular activities
- 🚀 **Easy-to-Use Interface** - Simple web form for inputting student information
- 🤖 **ML-Powered** - Uses trained machine learning models for accurate predictions
- 💼 **17 Career Paths** - Recommendations across diverse fields including:
  - Lawyer, Doctor, Software Engineer, Teacher
  - Business Owner, Scientist, Designer, and more!

## 🛠️ Tech Stack

- **Backend**: Flask (Python)
- **Machine Learning**: scikit-learn
- **Data Processing**: NumPy
- **Frontend**: HTML, CSS, JavaScript

## 📦 Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/widushan/-Education-Recommendation-System.git
   cd -Education-Recommendation-System
   ```

2. **Create a virtual environment**
   ```bash
   python -m venv venv
   ```

3. **Activate the virtual environment**
   ```bash
   # Windows
   venv\Scripts\activate
   
   # Linux/Mac
   source venv/bin/activate
   ```

4. **Install dependencies**
   ```bash
   pip install scikit-learn==1.3.2
   pip install numpy
   pip install flask
   ```

## 🚀 Usage

1. **Start the Flask application**
   ```bash
   python app.py
   ```

2. **Open your browser** and navigate to:
   ```
   http://localhost:5000
   ```

3. **Fill in the student information**:
   - Personal details (gender, part-time job status)
   - Academic performance (scores in various subjects)
   - Study habits (weekly self-study hours, absence days)
   - Extracurricular activities

4. **Get your career recommendations!** 🎉

## Screenshots

<img width="1917" height="1025" alt="Image" src="https://github.com/user-attachments/assets/99a9e5e6-ea16-4a42-96fd-f5fad93c9e1a" />

<img width="1915" height="1023" alt="Image" src="https://github.com/user-attachments/assets/83b352db-5a53-48c7-9db2-6520aa7d58d3" />

<img width="1913" height="1025" alt="Image" src="https://github.com/user-attachments/assets/7d57b3d7-c5b8-45da-aa08-040f47ffa16c" />

<img width="1919" height="1024" alt="Image" src="https://github.com/user-attachments/assets/c0613d5d-22c9-48e1-aab4-6318e4975960" />

## 📁 Project Structure

```
-Education-Recommendation-System/
├── app.py                 # Main Flask application
├── Models/                # Trained ML models
│   ├── model.pkl
│   └── scaler.pkl
├── templates/             # HTML templates
│   ├── home.html
│   ├── recommend.html
│   └── results.html
├── static/                # Static files (CSS, images)
├── student-scores.csv     # Dataset
└── README.md
```

## 🔗 GitHub Repository

**🔗 [View on GitHub](https://github.com/widushan/-Education-Recommendation-System)**

[![GitHub](https://img.shields.io/badge/GitHub-Repository-blue?style=flat-square&logo=github)](https://github.com/widushan/-Education-Recommendation-System)

## 📝 License

This project is open source and available for educational purposes.

## 🤝 Contributing

Contributions, issues, and feature requests are welcome! Feel free to check the issues page.

---

<div align="center">

**Made with ❤️ using Flask & Machine Learning**

⭐ Star this repo if you find it helpful!

</div>
