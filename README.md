# YOLOv11 Tea Leaf Disease Detection - GitHub Ready Project

## 📁 Project Structure

```bash
tea-leaf-yolov11/
│
├── app.py
├── train.py
├── requirements.txt
├── Procfile
├── README.md
├── best.pt
├── data.yaml
│
├── uploads/
├── static/
│   └── result.jpg
│
├── templates/
│   └── index.html
│
└── dataset/
    ├── images/
    └── labels/
```

---

# 📄 requirements.txt

```txt
flask
ultralytics
torch
torchvision
pillow
gunicorn
opencv-python-headless
```

---

# 📄 Procfile

```txt
web: gunicorn app:app
```

---

# 📄 data.yaml

```yaml
train: dataset/images/train
val: dataset/images/val

nc: 4
names: ["healthy", "brown_spot", "leaf_blight", "fungal"]
```

---

# 📄 train.py

```python
from ultralytics import YOLO

# Load pretrained YOLOv11 model
model = YOLO("yolo11n.pt")

# Train model
model.train(
    data="data.yaml",
    epochs=100,
    imgsz=640,
    batch=8,
    device="cpu",
    name="tea_leaf_detection"
)

# Validate model
metrics = model.val()
print(metrics)
```

---

# 📄 app.py

```python
from flask import Flask, render_template, request
from ultralytics import YOLO
import os

app = Flask(__name__)

UPLOAD_FOLDER = "uploads"
os.makedirs(UPLOAD_FOLDER, exist_ok=True)
os.makedirs("static", exist_ok=True)

# Load trained model
model = YOLO("best.pt")

@app.route('/', methods=['GET', 'POST'])
def index():
    result_img = None
    predictions = []

    if request.method == 'POST':
        file = request.files['image']

        if file:
            filepath = os.path.join(UPLOAD_FOLDER, file.filename)
            file.save(filepath)

            results = model(filepath)

            # Save result image
            result_path = os.path.join("static", "result.jpg")
            results[0].save(filename=result_path)

            # Get prediction information
            for box in results[0].boxes:
                cls = int(box.cls[0])
                conf = float(box.conf[0])
                label = model.names[cls]

                predictions.append({
                    "label": label,
                    "confidence": round(conf * 100, 2)
                })

            result_img = result_path

    return render_template(
        'index.html',
        result_img=result_img,
        predictions=predictions
    )

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000, debug=True)
```

---

# 📄 templates/index.html

```html
<!DOCTYPE html>
<html>
<head>
    <title>Tea Leaf Disease Detection</title>
    <style>
        body {
            font-family: Arial;
            text-align: center;
            background: #f4f4f4;
            padding: 20px;
        }

        .container {
            background: white;
            padding: 20px;
            border-radius: 10px;
            width: 600px;
            margin: auto;
            box-shadow: 0 0 10px rgba(0,0,0,0.1);
        }

        img {
            margin-top: 20px;
            border-radius: 10px;
        }

        button {
            padding: 10px 20px;
            background: green;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }

        button:hover {
            background: darkgreen;
        }
    </style>
</head>
<body>

<div class="container">
    <h2>🌱 Tea Leaf Disease Detection - YOLOv11</h2>

    <form method="POST" enctype="multipart/form-data">
        <input type="file" name="image" required>
        <br><br>
        <button type="submit">Detect Disease</button>
    </form>

    {% if result_img %}
        <h3>Detection Result</h3>
        <img src="{{ result_img }}" width="500">

        <h3>Predictions</h3>
        <ul style="list-style:none;">
        {% for pred in predictions %}
            <li>
                <strong>{{ pred.label }}</strong>
                - {{ pred.confidence }}%
            </li>
        {% endfor %}
        </ul>
    {% endif %}
</div>

</body>
</html>
```

---

# 📄 README.md

````md
# 🌱 Tea Leaf Disease Detection using YOLOv11

This project uses YOLOv11 to detect diseases on tea leaves.

## Features
- Upload tea leaf image
- Detect disease regions
- Display bounding boxes
- Show disease labels and confidence scores

## Technologies
- Python
- Flask
- YOLOv11
- Ultralytics

## Installation

```bash
pip install -r requirements.txt
````

## Train Model

```bash
python train.py
```

## Run Web App

```bash
python app.py
```

## Deploy

Deployable on:

* Render
* Railway
* HuggingFace Spaces

## Author

Tea Leaf AI Project

````

---

# 🚀 GitHub Upload Commands

```bash
git init
git add .
git commit -m "Tea leaf disease detection using YOLOv11"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/tea-leaf-yolov11.git
git push -u origin main
````

---

# 🌐 Render Deployment

Build Command:

```bash
pip install -r requirements.txt
```

Start Command:

```bash
gunicorn app:app
```

---

# ✅ Final Result

The system allows users to:

* Upload tea leaf images
* Detect diseases automatically
* View detection results online
* Use the system in real agricultural environments
