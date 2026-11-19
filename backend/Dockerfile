FROM python:3.15-slim

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1

# OpenCV / MediaPipe runtime libraries
RUN apt-get update \
    && apt-get install -y --no-install-recommends libgl1 libglib2.0-0 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt

# model_complexity=2 downloads its model on first use into site-packages, which the non-root
# user can't write to (or may have no internet). Fetch it once at build time instead.
RUN python -c "import mediapipe as mp; mp.solutions.pose.Pose(static_image_mode=True, model_complexity=2, enable_segmentation=True).close()"

COPY app ./app

RUN useradd --create-home appuser
USER appuser

EXPOSE 8000
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000", "--workers", "2"]
