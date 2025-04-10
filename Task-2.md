
# 🚀 Deploying a FastAPI Agent Backend on Google Cloud Run (with Firebase Frontend)

## Overview

This guide helps you containerize a sample FastAPI backend (acting as an AI agent API), push it to Google Artifact Registry, and deploy it on **Google Cloud Run**. It also shows how to connect it to a frontend built with **React + Vite**, hosted via **Firebase Hosting**.


##  Project Structure

Assumed FastAPI app file structure:

```
fastapi-app/
│
├── main.py
├── requirements.txt
├── Dockerfile
└── README.md (this file)
```


## 1. FastAPI Backend Setup

### main.py

```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware

app = FastAPI()

# Enable CORS for frontend communication
app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],  # Replace with your frontend domain in production
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

@app.get("/")
def read_root():
    return {"message": "Hello from FastAPI!"}
```

### requirements.txt

```
fastapi
uvicorn
```

## 2. Create Dockerfile

```Dockerfile
FROM python:3.10-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8080"]
```


## 3. Build and Test Docker Image Locally

### Build the image:

```bash
docker build -t fastapi-cloudrun .
```

### Run locally:

```bash
docker run -p 8080:8080 fastapi-cloudrun
```
- ![image](https://github.com/user-attachments/assets/89aeda61-2e89-4648-b26c-5693157bc5f8)


Visit: `http://localhost:8080`



## 4. Push Docker Image to Google Artifact Registry

### Step 1: Enable Artifact Registry

```bash
gcloud services enable artifactregistry.googleapis.com
```

### Step 2: Create a Docker repository

```bash
gcloud artifacts repositories create fastapi-repo \
    --repository-format=docker \
    --location=us-central1
```
- ![image](https://github.com/user-attachments/assets/8aa91308-910a-4086-9fa2-89dae12dbe93)


### Step 3: Authenticate Docker with GCP

```bash
gcloud auth configure-docker us-central1-docker.pkg.dev
```

- ![image](https://github.com/user-attachments/assets/fc8c4057-a751-4b59-aa8f-175e9312e76b)


### Step 4: Tag and Push the Image

```bash
docker tag fastapi-cloudrun us-central1-docker.pkg.dev/YOUR_PROJECT_ID/fastapi-repo/fastapi-cloudrun
docker push us-central1-docker.pkg.dev/YOUR_PROJECT_ID/fastapi-repo/fastapi-cloudrun
```
- ![image](https://github.com/user-attachments/assets/58c1403c-ecc0-47d4-9415-64fd121ca589)


## 5. Deploy to Google Cloud Run

```bash
gcloud run deploy fastapi-backend \
  --image us-central1-docker.pkg.dev/YOUR_PROJECT_ID/fastapi-repo/fastapi-cloudrun \
  --platform managed \
  --region us-central1 \
  --allow-unauthenticated
```
- ![image](https://github.com/user-attachments/assets/9935592f-3272-426e-91a5-f8f447a945c4)

-  After deployment, note the public URL (e.g., `https://fastapi-backend-xyz123.run.app`) — this will be used in your frontend.


## 6. Frontend Setup using React + Vite

### Step 1: Create Vite React App

```bash
mkdir fastapi-app && cd fastapi-app
mkdir my-frontend && cd my-frontend
npm create vite@latest .
```

> Choose `React` and `JavaScript + SWC` when prompted.

### Step 2: Install and Start

```bash
npm install
npm run dev
```

Visit: `http://localhost:5173`

- ![image](https://github.com/user-attachments/assets/d197d6a8-e5b5-41ab-ab60-2748a75f0c5d)


## 7. Deploy React App on Firebase Hosting

### Step 1: Initialize Firebase Hosting

```bash
firebase login
firebase init hosting
```
- ![image](https://github.com/user-attachments/assets/df590a4c-97ed-4214-8f98-b6c89c12de41)


Steps during init:
- Select your GCP Firebase project.
- Choose `dist` or `build` as your hosting folder (after running `npm run build`).
- Configure as a single-page app: **Yes**.

### Step 2: Build and Deploy

```bash
npm run build
firebase deploy
```

![image](https://github.com/user-attachments/assets/d1fda9b1-80c6-486c-b38a-753d53ad53f7)


-  Now your frontend is publicly accessible via Firebase Hosting.

-  ![image](https://github.com/user-attachments/assets/4b557ca5-418a-41ab-b138-7c489ccb0822)




## 8. CORS Handling (Backend)

In `main.py`, make sure CORS settings allow communication from your frontend:

```python
app.add_middleware(
    CORSMiddleware,
    allow_origins=["https://your-frontend.web.app"],  # Firebase URL
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
```


## 9. Optional: Monitor LLM APIs with Helicone

If your FastAPI agent uses LLMs (OpenAI, etc.), you can monitor them with Helicone:

- Sign up at: [https://www.helicone.ai](https://www.helicone.ai)
- Follow the SDK setup instructions for your language.


## 10. React Dashboard to Manage Agents (Optional Extension)

To create a dashboard:

```bash
npx create-react-app agent-dashboard
cd agent-dashboard
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

Then, set up Tailwind and build out your admin UI as needed.


## Conclusion

-  You now have a complete cloud-native FastAPI app running on Google Cloud Run, communicating with a Vite-based frontend hosted via Firebase. This architecture is scalable, serverless, and production-ready with minimal effort.


## Author
### Rajpreet Jahagiridhar
