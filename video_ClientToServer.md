# Video Upload from React to Backend

This repository provides different methods to send video files from a React frontend to a backend efficiently using various libraries and third-party services.

## 1. Uploading Video Using FormData and Fetch API

A simple way to send video files to the backend using FormData and Fetch API.

### **Frontend (React.js) Example**
```jsx
const uploadVideo = async (file) => {
  const formData = new FormData();
  formData.append("video", file);

  try {
    const response = await fetch("http://localhost:5000/upload", {
      method: "POST",
      body: formData,
    });

    const data = await response.json();
    console.log("Upload success:", data);
  } catch (error) {
    console.error("Error uploading video:", error);
  }
};
```

---

## 2. Uploading Video Using Axios

Axios provides better error handling and progress tracking for video uploads.

### **Frontend Example**
```jsx
import axios from "axios";

const uploadVideo = async (file) => {
  const formData = new FormData();
  formData.append("video", file);

  try {
    const response = await axios.post("http://localhost:5000/upload", formData, {
      headers: { "Content-Type": "multipart/form-data" },
      onUploadProgress: (progressEvent) => {
        let percentCompleted = Math.round(
          (progressEvent.loaded * 100) / progressEvent.total
        );
        console.log(\`Upload Progress: \${percentCompleted}%\`);
      },
    });

    console.log("Upload success:", response.data);
  } catch (error) {
    console.error("Error uploading video:", error);
  }
};
```

---

## 3. Uploading Video Using Firebase Storage

Firebase Storage provides scalable hosting for video files.

### **Installation**
```sh
npm install firebase
```

### **Setup Firebase**
```js
import { initializeApp } from "firebase/app";
import { getStorage, ref, uploadBytesResumable, getDownloadURL } from "firebase/storage";

const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_AUTH_DOMAIN",
  projectId: "YOUR_PROJECT_ID",
  storageBucket: "YOUR_STORAGE_BUCKET",
  messagingSenderId: "YOUR_MESSAGING_SENDER_ID",
  appId: "YOUR_APP_ID",
};

const app = initializeApp(firebaseConfig);
const storage = getStorage(app);
```

### **Upload Video to Firebase**
```jsx
const uploadVideo = (file) => {
  const storageRef = ref(storage, \`videos/\${file.name}\`);
  const uploadTask = uploadBytesResumable(storageRef, file);

  uploadTask.on(
    "state_changed",
    (snapshot) => {
      const progress = (snapshot.bytesTransferred / snapshot.totalBytes) * 100;
      console.log(\`Upload Progress: \${progress}%\`);
    },
    (error) => console.error("Upload error:", error),
    async () => {
      const downloadURL = await getDownloadURL(uploadTask.snapshot.ref);
      console.log("Download URL:", downloadURL);
    }
  );
};
```

---

## 4. Uploading Video Using Cloudinary

Cloudinary provides optimized video uploads and transformations.

### **Installation**
```sh
npm install @cloudinary/react @cloudinary/url-gen
```

### **Upload Using Cloudinary API**
```jsx
const uploadVideo = async (file) => {
  const formData = new FormData();
  formData.append("file", file);
  formData.append("upload_preset", "YOUR_UPLOAD_PRESET");

  try {
    const response = await fetch("https://api.cloudinary.com/v1_1/YOUR_CLOUD_NAME/video/upload", {
      method: "POST",
      body: formData,
    });

    const data = await response.json();
    console.log("Uploaded video URL:", data.secure_url);
  } catch (error) {
    console.error("Error uploading video:", error);
  }
};
```

---

## 5. Uploading Video Using Socket.io (Real-Time Upload)

Socket.io can be used to stream video chunks in real-time.

### **Installation**
```sh
npm install socket.io-client
```

### **Frontend Example**
```jsx
import { io } from "socket.io-client";

const socket = io("http://localhost:5000");

const uploadVideo = (file) => {
  const reader = new FileReader();

  reader.onload = () => {
    const arrayBuffer = reader.result;
    socket.emit("video-upload", { data: arrayBuffer, fileName: file.name });
  };

  reader.readAsArrayBuffer(file);
};
```

---

## **Comparison of Libraries for Video Upload**

| Library | Features |
|---------|----------|
| **Axios** | Better error handling, progress tracking |
| **Firebase Storage** | Scalable, free tier, easy integration |
| **Cloudinary** | Optimized media storage, transformations |
| **Tus.js** | Resumable uploads, good for large files |
| **Socket.io** | Real-time streaming |

---

## **Best Approach Based on Use Case**

- **For small uploads** → Fetch API or Axios  
- **For large file uploads** → Cloudinary, Firebase, or `Tus.js`  
- **For real-time streaming** → Socket.io  
- **For resumable uploads** → `Tus.js` or AWS S3 Multipart Upload  

---

## **Conclusion**

This README provides multiple approaches to uploading videos efficiently from a React frontend to a backend using Fetch API, Axios, Firebase, Cloudinary, and Socket.io. Choose the best method based on your project's needs.

---

### **Happy Coding! 🚀**
