# Tomato Yield Predictor

Spring Boot + React + Python model integration for tomato yield prediction.

Users enter greenhouse and crop-management data on the webpage, Spring Boot forwards the request to a local Python model API, and the predicted yield is displayed on the same page.

## Tech Stack

- Java 21
- Spring Boot 3.5.13
- React + Vite
- Python FastAPI
- scikit-learn / pandas / numpy
- H2 database for local development

## Project Structure

```text
demo/
  src/                    Spring Boot backend
  frontend/               React source code
  model_service/          Python model API
  start-services.cmd      One-click startup script
  start-services.ps1      PowerShell startup script
  pom.xml                 Maven config
```

## Prerequisites

Install these before running the project:

- JDK 21
- Python 3.12
- Node.js 20+ and npm

Recommended checks:

```powershell
java -version
python --version
node -v
npm -v
```

## First-Time Setup

### 1. Set up the Python model environment

```powershell
cd C:\demo\model_service
python -m venv .venv
.\.venv\Scripts\python.exe -m pip install --upgrade pip
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
```

### 2. Install frontend dependencies

```powershell
cd C:\demo\frontend
npm install
```

### 3. Optional: verify Maven wrapper

```powershell
cd C:\demo
$env:MAVEN_USER_HOME="C:\demo\.m2"
.\mvnw.cmd -v
```

## Start the Application

### Option A: one-click start

From the project root:

```powershell
cd your workspace
.\start-services.cmd
```

This will:

1. Start the Python model service on `http://127.0.0.1:8001`
2. Start Spring Boot on `http://localhost:8080`
3. Open the browser automatically

Open:

```text
http://localhost:8080/#/
```

### Option B: start services manually

#### Start Python model service

```powershell
cd C:\demo\model_service
.\.venv\Scripts\python.exe -m uvicorn api:app --host 127.0.0.1 --port 8001
```

#### Start Spring Boot

Open another terminal:

```powershell
cd C:\demo
$env:MAVEN_USER_HOME="C:\demo\.m2"
.\mvnw.cmd spring-boot:run
```

Open:

```text
http://localhost:8080/#/
```

## Rebuild the Frontend

If you change files in `frontend/src`, rebuild the frontend and restart Spring Boot.

```powershell
cd C:\demo\frontend
npm run build
```

The built assets are then used by Spring Boot from `src/main/resources/static`.

## API Flow

Web input goes through this path:

```text
Browser -> Spring Boot (/api/predict) -> Python FastAPI (/predict) -> Spring Boot -> Browser
```

### Spring Boot endpoint

```text
POST /api/predict
```

### Python model endpoint

```text
POST http://127.0.0.1:8001/predict
```

## Example Prediction Request

```json
{
  "avgTemperatureC": 25,
  "minTemperatureC": 24,
  "maxTemperatureC": 27,
  "humidityPercent": 70,
  "co2Ppm": 800,
  "lightIntensityLux": 30000,
  "photoperiodHours": 12,
  "irrigationMm": 7,
  "fertilizerNKgHa": 140,
  "fertilizerPKgHa": 60,
  "fertilizerKKgHa": 140,
  "pestSeverity": 1,
  "pH": 6.5,
  "variety": "Roma"
}
```

## Common Problems

### Port 8080 already in use

```powershell
netstat -ano | findstr :8080
Stop-Process -Id <PID>
```

### Python service not found

Make sure the Python model is running:

```powershell
cd C:\demo\model_service
.\.venv\Scripts\python.exe -m uvicorn api:app --host 127.0.0.1 --port 8001
```

### Frontend changes not visible

1. Run `npm run build` in `frontend`
2. Restart Spring Boot
3. Hard refresh the browser with `Ctrl + F5`

## Before Uploading to GitHub

Do not upload generated or local-only files such as:

- `.m2/`
- `target/`
- `frontend/node_modules/`
- `model_service/.venv/`
- `python-installer.exe`

If needed, delete them before pushing.

