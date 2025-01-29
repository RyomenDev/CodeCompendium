
# Continuous Video Upload with React

This project demonstrates how to record video continuously in the browser and upload it in chunks to the backend. The React application uses the `react-media-recorder` library for video recording, `axios` for asynchronous uploads, and a simple backend to handle file uploads.

## Table of Contents
- [Installation](#installation)
- [Usage](#usage)
- [Backend Setup](#backend-setup)
- [Chunk Uploading](#chunk-uploading)
- [Considerations](#considerations)

## Installation

### Frontend
To set up the frontend for continuous video recording and chunk uploading:

1. Clone this repository.
2. Navigate to the project directory:
   ```bash
   cd path/to/project
   ```
3. Install the dependencies:
   ```bash
   npm install
   ```

### Backend
To set up the backend (Node.js example):

1. Create a new directory for your backend (e.g., `backend`).
2. Initialize a new Node.js project:
   ```bash
   npm init -y
   ```
3. Install the required packages:
   ```bash
   npm install express multer
   ```
4. Create an `index.js` file for your server (see Backend Setup section below).
5. Run the server:
   ```bash
   node index.js
   ```

## Usage

### Frontend
To start the frontend application, use:
```bash
npm start
```

Visit [http://localhost:3000](http://localhost:3000) to access the application. The user can click to start and stop recording, with video uploaded in chunks to the backend.

### Backend
Ensure that your backend is running on the appropriate port (`3000` by default) to receive the video uploads.

## Backend Setup

To handle the video chunk uploads, set up a simple Express server with `multer` to manage file uploads:

```js
const express = require('express');
const multer = require('multer');
const path = require('path');

const app = express();
const upload = multer({ dest: 'uploads/' });

app.post('/upload', upload.single('video'), (req, res) => {
  console.log('Received video chunk:', req.file);
  res.send('Upload successful');
});

app.listen(3000, () => {
  console.log('Server running on port 3000');
});
```

This example uses `multer` to handle the file upload, saving video chunks to the `uploads/` directory.

## Chunk Uploading

### Frontend
In the frontend, the `ReactMediaRecorder` component handles the video recording. The video is recorded in chunks and uploaded via `axios` to the backend. Here is an example of chunk handling:

```jsx
import React, { useState, useEffect } from 'react';
import { ReactMediaRecorder } from 'react-media-recorder';
import axios from 'axios';

const VideoRecorder = () => {
  const [recording, setRecording] = useState(false);
  const [videoChunks, setVideoChunks] = useState([]);
  const [uploading, setUploading] = useState(false);

  const handleUploadChunk = async (chunk) => {
    const formData = new FormData();
    formData.append('video', chunk, 'video.mp4');

    try {
      setUploading(true);
      await axios.post('/upload', formData, {
        headers: { 'Content-Type': 'multipart/form-data' },
      });
      setUploading(false);
    } catch (error) {
      console.error('Error uploading video chunk', error);
      setUploading(false);
    }
  };

  const handleStopRecording = (blobUrl) => {
    const videoBlob = new Blob(videoChunks, { type: 'video/mp4' });
    handleUploadChunk(videoBlob);  // Send the chunk to the backend
  };

  return (
    <div>
      <ReactMediaRecorder
        video
        onStop={handleStopRecording}
        onDataAvailable={(data) => setVideoChunks((prevChunks) => [...prevChunks, data])}
        render={({ startRecording, stopRecording, mediaBlobUrl }) => (
          <div>
            <button onClick={startRecording} disabled={recording}>
              Start Recording
            </button>
            <button onClick={stopRecording} disabled={!recording}>
              Stop Recording
            </button>
            {mediaBlobUrl && <video src={mediaBlobUrl} controls />}
          </div>
        )}
      />
    </div>
  );
};

export default VideoRecorder;
```

## Considerations

- **Uploading Continuously**: Uploading chunks while recording helps ensure the backend receives data even during a long recording session.
- **Concurrency**: For handling multiple uploads or retries, you can use `react-query` or `axios` interceptors to manage retries and concurrency issues.
- **Chunk Size**: You can adjust the chunk size based on your network performance, but smaller chunks (5-20MB) are usually optimal for faster uploads.
