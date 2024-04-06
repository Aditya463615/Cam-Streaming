# Cam-Streaming
Stream Cam video to website using python Flask
```python
from flask import Flask, render_template, Response
import cv2

app = Flask(__name__)

# Replace <URL> with the actual URL of your Dahua camera
Username = 'admin'
Password = 'admin@123'
IP = ''
Port = ''

url = f"rtsp://{Username}:{Password}@{IP}:{Port}/cam/realmonitor?channel=4&subtype=0"

def generate_frames():
    cap = cv2.VideoCapture(url)
    while True:
        ret, frame = cap.read()
        if not ret:
            break
        _, buffer = cv2.imencode(".jpg", frame)
        frame_bytes = buffer.tobytes()
        yield (b"--frame\r\nContent-Type: image/jpeg\r\n\r\n" + frame_bytes + b"\r\n")

@app.route("/")
def index():
    return render_template("index.html")

@app.route("/video_feed")
def video_feed():
    return Response(generate_frames(), mimetype="multipart/x-mixed-replace; boundary=frame")

if __name__ == "__main__":
    app.run(port='80')
```
