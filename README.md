<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>My Drive - CRUD</title>
  <style>
    body {
      margin: 0;
      font-family: "Google Sans", Arial, sans-serif;
      background: #f8f9fa;
      height: 100vh;
    }

    .hidden {
      display: none !important;
    }

    /* --- LOGIN PAGE --- */
    #loginPage {
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      background: #f1f3f4;
    }

    .login-container {
      width: 100%;
      max-width: 400px;
      background: white;
      padding: 30px;
      border-radius: 12px;
      box-shadow: 0 2px 10px rgba(0,0,0,0.2);
    }

    .login-container h2 {
      text-align: center;
      margin-bottom: 20px;
      color: #1a73e8;
    }

    .login-container input {
      padding: 12px;
      margin: 10px 0;
      width: 100%;
      border: 1px solid #ccc;
      border-radius: 8px;
    }

    .login-container button {
      width: 100%;
      padding: 12px;
      margin-top: 10px;
      border: none;
      background: #1a73e8;
      color: white;
      border-radius: 8px;
      cursor: pointer;
      font-weight: bold;
      font-size: 16px;
    }

    .login-container button:hover {
      background: #1557b0;
    }

    /* --- DASHBOARD --- */
    #dashboardPage {
      height: 100vh;
      display: flex;
      flex-direction: column;
    }

    .navbar {
      height: 60px;
      background: white;
      border-bottom: 1px solid #ddd;
      display: flex;
      align-items: center;
      justify-content: space-between;
      padding: 0 20px;
      box-shadow: 0 1px 3px rgba(0,0,0,0.1);
    }

    .navbar h2 {
      margin: 0;
      font-size: 20px;
      color: #1a73e8;
    }

    .main {
      flex: 1;
      display: flex;
    }

    .sidebar {
      width: 220px;
      background: #f1f3f4;
      padding: 20px;
      border-right: 1px solid #ddd;
    }

    .sidebar button {
      width: 100%;
      margin-top: 10px;
      background: #1a73e8;
      color: white;
      padding: 12px;
      border-radius: 8px;
      border: none;
      cursor: pointer;
      font-weight: bold;
    }

    .sidebar button:hover {
      background: #1557b0;
    }

    .content {
      flex: 1;
      padding: 20px;
      overflow-y: auto;
    }

    .content h2 {
      margin-top: 0;
    }

    /* --- FILE CARDS --- */
    .file-list {
      display: grid;
      grid-template-columns: repeat(auto-fill, minmax(180px, 1fr));
      gap: 20px;
    }

    .file {
      position: relative;
      border: 1px solid #ddd;
      padding: 15px;
      text-align: center;
      border-radius: 10px;
      background: #fff;
      box-shadow: 0 1px 4px rgba(0,0,0,0.1);
      font-size: 14px;
      transition: box-shadow 0.2s ease;
    }

    .file:hover {
      box-shadow: 0 4px 10px rgba(0,0,0,0.15);
    }

    .file img {
      width: 50px;
      height: 50px;
      object-fit: contain;
      margin-bottom: 10px;
    }

    .file-actions {
      margin-top: 10px;
      display: flex;
      justify-content: center;
      gap: 10px;
    }

    .file button {
      border: none;
      border-radius: 6px;
      padding: 6px 10px;
      cursor: pointer;
      font-size: 12px;
      font-weight: bold;
    }

    .rename-btn {
      background: #ff9800;
      color: white;
    }
    .rename-btn:hover {
      background: #e68900;
    }

    .delete-btn {
      background: #f44336;
      color: white;
    }
    .delete-btn:hover {
      background: #c62828;
    }
  </style>
</head>
<body>
  <!-- Login Page -->
  <div id="loginPage">
    <div class="login-container">
      <h2>Login</h2>
      <form onsubmit="goToDashboard(event)">
        <input type="email" placeholder="Email" required />
        <input type="password" placeholder="Password" required />
        <button type="submit">Login</button>
      </form>
    </div>
  </div>

  <!-- Dashboard -->
  <div id="dashboardPage" class="hidden">
    <div class="navbar">
      <h2>My Drive</h2>
      <button onclick="logout()">Logout</button>
    </div>
    <div class="main">
      <div class="sidebar">
        <button onclick="document.getElementById('fileInput').click()">+ New</button>
        <input type="file" id="fileInput" multiple class="hidden" onchange="handleFiles(event)" />
      </div>
      <div class="content">
        <h2>Your Files</h2>
        <div class="file-list" id="fileList"></div>
      </div>
    </div>
  </div>

  <script>
    let filesData = []; // acts as our "database" for CRUD

    function goToDashboard(e) {
      e.preventDefault();
      document.getElementById("loginPage").classList.add("hidden");
      document.getElementById("dashboardPage").classList.remove("hidden");
      renderFiles();
    }

    function logout() {
      document.getElementById("dashboardPage").classList.add("hidden");
      document.getElementById("loginPage").classList.remove("hidden");
    }

    function handleFiles(event) {
      const files = event.target.files;
      for (let file of files) {
        filesData.push({ id: Date.now() + Math.random(), name: file.name, type: file.type, file });
      }
      renderFiles();
    }

    function renderFiles() {
      const fileList = document.getElementById("fileList");
      fileList.innerHTML = "";

      filesData.forEach(fileObj => {
        const fileDiv = document.createElement("div");
        fileDiv.className = "file";

        const img = document.createElement("img");
        if (fileObj.type.startsWith("image/")) {
          img.src = URL.createObjectURL(fileObj.file);
        } else {
          img.src = "https://cdn-icons-png.flaticon.com/512/337/337946.png";
        }

        const name = document.createElement("div");
        name.textContent = fileObj.name;

        // Actions
        const actions = document.createElement("div");
        actions.className = "file-actions";

        const renameBtn = document.createElement("button");
        renameBtn.className = "rename-btn";
        renameBtn.textContent = "Rename";
        renameBtn.onclick = () => renameFile(fileObj.id);

        const deleteBtn = document.createElement("button");
        deleteBtn.className = "delete-btn";
        deleteBtn.textContent = "Delete";
        deleteBtn.onclick = () => deleteFile(fileObj.id);

        actions.appendChild(renameBtn);
        actions.appendChild(deleteBtn);

        fileDiv.appendChild(img);
        fileDiv.appendChild(name);
        fileDiv.appendChild(actions);
        fileList.appendChild(fileDiv);
      });
    }

    function renameFile(id) {
      const file = filesData.find(f => f.id === id);
      const newName = prompt("Enter new name:", file.name);
      if (newName) {
        file.name = newName;
        renderFiles();
      }
    }

    function deleteFile(id) {
      filesData = filesData.filter(f => f.id !== id);
      renderFiles();
    }
  </script>
</body>
</html>
