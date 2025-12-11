# Green Video Streaming

## 📁 Project Structure

```
.
├── backend/                    # Flask API server
├── frontend/                   # Web UI
├── local_video_training_generator.ipynb  # ML training pipeline
├── training_data_local.csv    # Training dataset
├── crf_model.pkl              # Trained CRF model
├── preset_model.pkl           # Trained preset model
├── docker-compose.yml         # Container orchestration
├── k8s-deployment.yaml        # Kubernetes config
└── AWS-DEPLOYMENT-GUIDE.md    # Cloud deployment
```

## 🚀 Quick Start

### Prerequisites
- Python 3.8+
- FFmpeg ([installation guide](https://ffmpeg.org/download.html))

```bash
# Install dependencies
pip install opencv-python pandas numpy scikit-learn joblib flask flask-cors

# Verify FFmpeg
ffmpeg -version
```

### Train Models

1. Add training videos to `training_videos/` folder
2. Open [`local_video_training_generator.ipynb`](local_video_training_generator.ipynb)
3. Update video path:
   ```python
   video_dir = Path(r'training_videos/')
   ```
4. Run all cells to generate `crf_model.pkl` and `preset_model.pkl`

**Time**: ~2-5 minutes per video

### Run API Server

```bash
cd backend
python app.py
```

API available at `http://localhost:5000`

## 🐳 Docker Deployment

```bash
# Single container
docker build -t video-encoder .
docker run -p 5000:5000 video-encoder

# Full stack
docker-compose up
```

## 📊 Video Features

The system extracts 7 features:
- **Edge Density**: Visual complexity
- **Motion Score**: Frame-to-frame movement
- **Brightness**: Average luminance
- **Color Variance**: Color distribution
- **Resolution**: Width × height
- **FPS**: Frames per second
- **Duration**: Video length

## 🔌 API Usage

### POST `/predict`

```json
{
  "edge_density": 0.25,
  "motion_score": 0.18,
  "brightness": 0.45,
  "color_variance": 0.32,
  "resolution": 2073600,
  "fps": 30,
  "duration": 120
}
```

**Response:**
```json
{
  "predicted_crf": 26,
  "predicted_preset": "fast",
  "quality_estimate": 92.5,
  "file_size_estimate_mb": 45.2
}
```

## ☁️ Cloud Deployment

- **AWS EC2**: See [`AWS-DEPLOYMENT-GUIDE.md`](AWS-DEPLOYMENT-GUIDE.md)
- **Kubernetes**: `kubectl apply -f k8s-deployment.yaml`

## 📈 How It Works

1. **Training Phase**: Videos are encoded with multiple CRF/preset combinations to find optimal settings
2. **Feature Extraction**: Each video's characteristics are analyzed
3. **Model Training**: RandomForest learns patterns between features and optimal settings
4. **Prediction**: New videos get instant recommendations without encoding tests

## 🎓 ML Models

- **CRF Model**: Predicts optimal CRF (24, 26, 28, or 30)
- **Preset Model**: Predicts optimal preset (faster, fast, medium)
- **Algorithm**: Random Forest Classifier (100 trees, max depth 10)

## 🔧 Configuration

Create `.env` file:
```env
FLASK_ENV=production
API_PORT=5000
QUALITY_THRESHOLD=90
```

## 📝 Training Data Format

The [`training_data_local.csv`](training_data_local.csv) contains:
- Video features (7 columns)
- Optimal CRF value
- Optimal preset
- Quality score (SSIM-based)
- File size

## 🆘 Troubleshooting

**FFmpeg not found:**
```bash
# Windows
winget install ffmpeg

# macOS
brew install ffmpeg

# Linux
sudo apt-get install ffmpeg
```

**Low accuracy:** Train with 50+ diverse videos for best results

## 📄 License

Open source - use freely for educational and commercial purposes.
