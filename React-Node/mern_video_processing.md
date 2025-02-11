# MERN Video Processing with Offline Storage

## 1️⃣ Frontend (React)

### Install Dependencies:
```sh
npm install axios mime-db
```

### `VideoRecorder.jsx`
```javascript
import { useEffect, useRef, useState } from "react";
import axios from "axios";

const VideoRecorder = () => {
    const [recording, setRecording] = useState(false);
    const [mediaRecorder, setMediaRecorder] = useState(null);
    const [videoChunks, setVideoChunks] = useState([]);
    const videoRef = useRef(null);

    useEffect(() => {
        const getPermissions = async () => {
            try {
                const stream = await navigator.mediaDevices.getUserMedia({ video: true, audio: true });
                videoRef.current.srcObject = stream;
                const recorder = new MediaRecorder(stream, { mimeType: "video/webm" });
                setMediaRecorder(recorder);
            } catch (error) {
                console.error("Permission Denied:", error);
            }
        };

        getPermissions();
    }, []);

    const startRecording = () => {
        if (!mediaRecorder) return;
        setRecording(true);
        setVideoChunks([]);

        mediaRecorder.start();
        mediaRecorder.ondataavailable = (event) => {
            if (event.data.size > 0) {
                setVideoChunks((prev) => [...prev, event.data]);
            }
        };
    };

    const stopRecording = () => {
        setRecording(false);
        mediaRecorder.stop();
        saveAndUpload();
    };

    const saveAndUpload = async () => {
        const videoBlob = new Blob(videoChunks, { type: "video/webm" });
        const file = new File([videoBlob], `video_${Date.now()}.webm`, { type: "video/webm" });

        // Save locally in IndexedDB in case of network failure
        if (!navigator.onLine) {
            saveLocally(file);
            return;
        }

        // Upload to backend
        const formData = new FormData();
        formData.append("video", file);

        try {
            await axios.post("http://localhost:5000/upload", formData, {
                headers: { "Content-Type": "multipart/form-data" },
            });
        } catch (error) {
            console.error("Upload Failed:", error);
            saveLocally(file); // Save if network fails
        }
    };

    const saveLocally = (file) => {
        const request = indexedDB.open("VideoStorage", 1);
        request.onupgradeneeded = (event) => {
            const db = event.target.result;
            if (!db.objectStoreNames.contains("videos")) {
                db.createObjectStore("videos", { autoIncrement: true });
            }
        };
        request.onsuccess = (event) => {
            const db = event.target.result;
            const transaction = db.transaction("videos", "readwrite");
            const store = transaction.objectStore("videos");
            store.add(file);
        };
    };

    return (
        <div>
            <video ref={videoRef} autoPlay playsInline />
            <button onClick={recording ? stopRecording : startRecording}>
                {recording ? "Stop Recording" : "Start Recording"}
            </button>
        </div>
    );
};

export default VideoRecorder;
```

---

## 2️⃣ Backend (Node.js + Express)

### Install Dependencies:
```sh
npm install express multer cors mongoose gridfs-stream
```

### `server.js`
```javascript
const express = require("express");
const mongoose = require("mongoose");
const multer = require("multer");
const { GridFsStorage } = require("multer-gridfs-storage");
const cors = require("cors");

const app = express();
app.use(cors());

// MongoDB Connection
const mongoURI = "mongodb://localhost:27017/videoDB";
mongoose.connect(mongoURI, { useNewUrlParser: true, useUnifiedTopology: true });

const storage = new GridFsStorage({ url: mongoURI, file: (req, file) => ({ filename: file.originalname }) });
const upload = multer({ storage });

app.post("/upload", upload.single("video"), (req, res) => {
    res.json({ message: "Video uploaded successfully", file: req.file });
});

app.listen(5000, () => console.log("Server running on port 5000"));
```

---

## 3️⃣ Handling Offline Data Sync

### Syncing Local Storage on Network Recovery

```javascript
window.addEventListener("online", async () => {
    const request = indexedDB.open("VideoStorage", 1);
    request.onsuccess = async (event) => {
        const db = event.target.result;
        const transaction = db.transaction("videos", "readwrite");
        const store = transaction.objectStore("videos");

        const allVideos = store.getAll();
        allVideos.onsuccess = async () => {
            for (const file of allVideos.result) {
                const formData = new FormData();
                formData.append("video", file);

                try {
                    await axios.post("http://localhost:5000/upload", formData);
                    store.delete(file);
                } catch (error) {
                    console.error("Retry Failed:", error);
                }
            }
        };
    };
});
```

---

## 📌 Features Implemented:
✅ **Asks for camera & mic permissions**  
✅ **Captures video in chunks**  
✅ **Saves locally if offline**  
✅ **Automatically uploads on network recovery**  
✅ **Uses GridFS for scalable storage**

---

🚀 **Now your video processing is robust, efficient, and network fault-tolerant!**
