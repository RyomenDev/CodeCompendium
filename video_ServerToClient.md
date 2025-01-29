# Video Streaming in React.js

This project provides a guide on how to send videos effectively from the backend to the frontend in a React.js application.

## 📌 Methods for Video Streaming

1. **Progressive Streaming**: Serve videos using simple HTTP servers (e.g., Express, Flask) where the frontend fetches the file progressively.
2. **Adaptive Bitrate Streaming**: Uses HLS (HTTP Live Streaming) or DASH (Dynamic Adaptive Streaming) for smooth playback based on user bandwidth.
3. **WebRTC**: Ideal for real-time video transmission.
4. **RTMP (Real-Time Messaging Protocol)**: Used for live streaming services.

## 🔧 Backend Libraries & Tools

- **Node.js + Express**: Serve video files efficiently.
- **FFmpeg**: Convert videos into streamable formats.
- **Multer**: Handle video uploads.
- **Flask/Django** (for Python-based servers)

## ⚙️ Frontend Libraries & Tools

- **React Player**: Simple video player.
- **Video.js**: Highly customizable player.
- **HLS.js**: Enables HLS playback in browsers.
- **Dash.js**: Supports MPEG-DASH video streaming.

## 🚀 Example Implementation

### Backend (Node.js + Express)
```js
const express = require('express');
const fs = require('fs');
const app = express();

app.get('/video', (req, res) => {
    const path = 'video.mp4';
    const stat = fs.statSync(path);
    const fileSize = stat.size;
    const range = req.headers.range;

    if (range) {
        const parts = range.replace(/bytes=/, "").split("-");
        const start = parseInt(parts[0], 10);
        const end = parts[1] ? parseInt(parts[1], 10) : fileSize - 1;
        const chunksize = end - start + 1;
        const file = fs.createReadStream(path, { start, end });
        const head = {
            'Content-Range': `bytes ${start}-${end}/${fileSize}`,
            'Accept-Ranges': 'bytes',
            'Content-Length': chunksize,
            'Content-Type': 'video/mp4',
        };
        res.writeHead(206, head);
        file.pipe(res);
    } else {
        const head = {
            'Content-Length': fileSize,
            'Content-Type': 'video/mp4',
        };
        res.writeHead(200, head);
        fs.createReadStream(path).pipe(res);
    }
});

app.listen(5000, () => console.log('Server running on port 5000'));
```

### Frontend (React)
```js
import React from 'react';

const VideoPlayer = () => {
    return (
        <video controls width="600">
            <source src="http://localhost:5000/video" type="video/mp4" />
            Your browser does not support the video tag.
        </video>
    );
};

export default VideoPlayer;
```

## 📌 Third-Party Services
- **Cloudflare Stream** (For scalable video delivery)
- **MUX** (For adaptive streaming)
- **AWS S3 + CloudFront** (For storing and serving videos)
- **Firebase Storage** (For hosting video content)

## ✅ Conclusion

By using appropriate streaming techniques and libraries, we can efficiently deliver videos in a React application while optimizing performance.

---
🚀 **Happy Coding!**
