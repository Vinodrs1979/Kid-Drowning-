<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Kid Color Book</title>
    <style>
        /* --- CSS STYLES --- */
        :root {
            --primary-color: #FF6B6B;
            --secondary-color: #4ECDC4;
            --accent-color: #FFE66D;
            --dark-text: #2C3E50;
            --light-bg: #F7F9FC;
        }

        body {
            font-family: 'Comic Sans MS', 'Chalkboard SE', sans-serif;
            margin: 0;
            padding: 0;
            background-color: var(--light-bg);
            color: var(--dark-text);
            display: flex;
            flex-direction: column;
            min-height: 100vh;
        }

        /* Header & Footer */
        header, footer {
            background-color: var(--primary-color);
            color: white;
            text-align: center;
            padding: 1rem;
            box-shadow: 0 4px 6px rgba(0,0,0,0.1);
        }

        header h1 { margin: 0; font-size: 2.5rem; text-shadow: 2px 2px 0px rgba(0,0,0,0.2); }
        footer p { margin: 0; }
        
        .admin-link {
            color: rgba(255,255,255,0.6);
            text-decoration: none;
            font-size: 0.8rem;
            margin-top: 10px;
            display: inline-block;
            cursor: pointer;
        }

        /* Navigation */
        #nav-bar {
            padding: 10px 20px;
            background: white;
            display: none; 
        }
        .back-btn {
            background: var(--secondary-color);
            border: none;
            padding: 8px 16px;
            border-radius: 20px;
            color: white;
            cursor: pointer;
            font-weight: bold;
            font-size: 1rem;
        }

        /* Main Container */
        main {
            flex: 1;
            padding: 20px;
            max-width: 1200px;
            margin: 0 auto;
            width: 100%;
            box-sizing: border-box;
        }

        /* Grids */
        .grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
            gap: 20px;
        }

        /* Cards */
        .card {
            background: white;
            border-radius: 15px;
            overflow: hidden;
            box-shadow: 0 4px 15px rgba(0,0,0,0.1);
            transition: transform 0.3s ease, box-shadow 0.3s ease;
            cursor: pointer;
            border: 3px solid transparent;
            text-align: center;
            position: relative;
        }

        .card:hover {
            transform: translateY(-5px);
            box-shadow: 0 8px 25px rgba(0,0,0,0.15);
            border-color: var(--secondary-color);
        }

        .card-img {
            width: 100%;
            height: 150px;
            object-fit: cover;
            background-color: #eee;
        }

        .card-title {
            padding: 15px;
            font-size: 1.2rem;
            font-weight: bold;
            color: var(--dark-text);
        }

        /* Modals (Editor & Admin) */
        .modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,0.8);
            z-index: 1000;
            justify-content: center;
            align-items: center;
        }

        /* Editor Specific */
        .editor-container {
            background: white;
            padding: 10px;
            border-radius: 10px;
            display: flex;
            flex-direction: column;
            width: 95%;
            height: 95%;
            box-sizing: border-box;
        }

        .editor-toolbar {
            display: flex;
            gap: 10px;
            padding: 10px;
            background: #f0f0f0;
            border-radius: 10px;
            margin-bottom: 10px;
            align-items: center;
            flex-wrap: wrap;
        }

        canvas {
            background: white;
            box-shadow: 0 0 10px rgba(0,0,0,0.1);
            cursor: crosshair;
            touch-action: none;
            flex: 1;
            width: 100%;
            height: 100%; 
            border: 2px dashed #ccc;
        }

        .tool-group {
            display: flex;
            align-items: center;
            gap: 5px;
            margin-right: 15px;
        }
        
        .tool-btn {
            background: white;
            border: 2px solid #ccc;
            padding: 5px 10px;
            border-radius: 5px;
            cursor: pointer;
            font-size: 1.2rem;
        }
        
        .tool-btn.active {
            background: var(--accent-color);
            border-color: #e6c200;
        }

        input[type="color"] { border: none; width: 40px; height: 40px; cursor: pointer; background: none; }
        .brush-size { width: 100px; }
        .action-btn { padding: 10px 20px; border: none; border-radius: 5px; color: white; font-weight: bold; cursor: pointer; font-size: 1rem; }
        .btn-save { background-color: #27ae60; }
        .btn-clear { background-color: #e74c3c; }
        .btn-close { background-color: #95a5a6; }

        /* Admin Panel Styles */
        .admin-panel {
            background: white;
            width: 90%;
            max-width: 600px;
            padding: 30px;
            border-radius: 10px;
            max-height: 90vh;
            overflow-y: auto;
        }
        
        .admin-login-form { text-align: center; }
        .admin-dashboard { display: none; }
        
        .form-group { margin-bottom: 15px; }
        .form-group label { display: block; margin-bottom: 5px; font-weight: bold; }
        .form-group input, .form-group select { width: 100%; padding: 10px; border: 1px solid #ccc; border-radius: 5px; box-sizing: border-box;}
        
        .item-list { list-style: none; padding: 0; margin-top: 20px; border-top: 1px solid #eee; }
        .item-list li { display: flex; align-items: center; justify-content: space-between; padding: 10px 0; border-bottom: 1px solid #eee; }
        .item-list img { width: 50px; height: 50px; object-fit: cover; border-radius: 5px; margin-right: 10px; }
        .btn-delete { background: #e74c3c; color: white; border: none; padding: 5px 10px; border-radius: 3px; cursor: pointer; }

    </style>
</head>
<body>

    <header>
        <h1>🎨 Kid Color Book</h1>
    </header>

    <div id="nav-bar">
        <button class="back-btn" onclick="goHome()">⬅ Back to Categories</button>
        <span id="current-category-title" style="margin-left: 15px; font-weight: bold; font-size: 1.2rem;"></span>
    </div>

    <main id="app-content">
        </main>

    <footer>
        <p>© 2026 Kid Color Book</p>
        <a href="#" class="admin-link" onclick="openAdmin()">Admin</a>
    </footer>

    <div id="editor-modal" class="modal">
        <div class="editor-container">
            <div class="editor-toolbar">
                <div class="tool-group">
                    <button class="tool-btn active" id="btnBrush" onclick="setTool('brush')">🖌️ Brush</button>
                    <button class="tool-btn" id="btnFill" onclick="setTool('fill')">🪣 Fill</button>
                </div>

                <div class="tool-group">
                    <label>Color:</label>
                    <input type="color" id="colorPicker" value="#FF0000">
                </div>
                
                <div class="tool-group" id="brushSizeGroup">
                    <label>Size:</label>
                    <input type="range" id="brushSize" class="brush-size" min="1" max="50" value="5">
                </div>

                <div class="tool-group" style="margin-left: auto;">
                    <button class="action-btn btn-clear" onclick="clearCanvas()">Clear</button>
                    <button class="action-btn btn-save" onclick="saveDrawing()">💾 Save</button>
                    <button class="action-btn btn-close" onclick="closeEditor()">❌ Close</button>
                </div>
            </div>
            <canvas id="drawingCanvas"></canvas>
        </div>
    </div>

    <div id="admin-modal" class="modal">
        <div class="admin-panel">
            <div class="header-row" style="display:flex; justify-content:space-between; margin-bottom:20px;">
                <h2>Admin Panel</h2>
                <button onclick="document.getElementById('admin-modal').style.display='none'" style="border:none; background:none; font-size:1.5rem; cursor:pointer;">❌</button>
            </div>

            <div id="admin-login" class="admin-login-form">
                <p>Please enter password to access settings.</p>
                <div class="form-group">
                    <input type="password" id="admin-pass" placeholder="Password">
                </div>
                <button class="action-btn btn-save" onclick="checkAdmin()">Login</button>
            </div>

            <div id="admin-dashboard" class="admin-dashboard">
                <div class="form-group">
                    <label>Select Category to Manage:</label>
                    <select id="admin-cat-select" onchange="loadAdminList()"></select>
                </div>

                <div style="background:#f9f9f9; padding:15px; border-radius:8px; margin-bottom:20px;">
                    <h3 style="margin-top:0;">Add New Drawing</h3>
                    <div class="form-group">
                        <input type="text" id="new-img-title" placeholder="Drawing Title (e.g. Cute Dog)">
                    </div>
                    <div class="form-group">
                        <input type="text" id="new-img-url" placeholder="Image URL (http://...)">
                    </div>
                    <button class="action-btn btn-save" onclick="addDrawing()">+ Add Drawing</button>
                </div>

                <h3>Existing Drawings</h3>
                <ul id="admin-list" class="item-list"></ul>
            </div>
        </div>
    </div>

    <script>
        // --- CONFIG & DATA ---
        const ADMIN_PASS = "1985";
        
        // Initial Data (Using simple placeholders)
        const categories = [
            { id: 'animals', name: 'Animals', icon: '🐶' },
            { id: 'cars', name: 'Cars', icon: '🚗' },
            { id: 'princess', name: 'Princess', icon: '👑' },
            { id: 'space', name: 'Space', icon: '🚀' },
            { id: 'nature', name: 'Nature', icon: '🌳' },
            { id: 'dinosaurs', name: 'Dinosaurs', icon: '🦖' },
            { id: 'robots', name: 'Robots', icon: '🤖' },
            { id: 'food', name: 'Yummy Food', icon: '🍔' },
            { id: 'ocean', name: 'Ocean Life', icon: '🐬' },
            { id: 'superheroes', name: 'Heroes', icon: '🦸' }
        ];

        // Placeholder Image (Outline)
        const defaultImg = "data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHdpZHRoPSI0MDAiIGhlaWdodD0iNDAwIiB2aWV3Qm94PSIwIDAgNDAwIDQwMCI+PHJlY3Qgd2lkdGg9IjEwMCUiIGhlaWdodD0iMTAwJSIgZmlsbD0id2hpdGUiLz48Y2lyY2xlIGN4PSIyMDAiIGN5PSIyMDAiIHI9IjEwMCIgc3Ryb2tlPSJibGFjayIgc3Ryb2tlLXdpZHRoPSI1IiBmaWxsPSJub25lIi8+PHBhdGggZD0iTTE1MCAxNTAgTDI1MCAxNTAgTDIwMCAyNTAgWiIgc3Ryb2tlPSJibGFjayIgc3Ryb2tlLXdpZHRoPSI1IiBmaWxsPSJub25lIi8+PC9zdmc+";

        // Load Data from LocalStorage or Init
        let contentData = JSON.parse(localStorage.getItem('kidColorBookData')) || {};

        // If empty, populate with defaults
        if (Object.keys(contentData).length === 0) {
            categories.forEach(cat => {
                contentData[cat.id] = [];
                for (let i = 1; i <= 10; i++) {
                    contentData[cat.id].push({
                        id: Date.now() + Math.random(),
                        title: `${cat.name} ${i}`,
                        src: defaultImg
                    });
                }
            });
            saveData();
        }

        function saveData() {
            localStorage.setItem('kidColorBookData', JSON.stringify(contentData));
        }

        // --- APP STATE ---
        const main = document.getElementById('app-content');
        const navBar = document.getElementById('nav-bar');
        const catTitle = document.getElementById('current-category-title');
        let currentTool = 'brush'; // 'brush' or 'fill'
        
        // --- DRAWING ENGINE VARS ---
        const canvas = document.getElementById('drawingCanvas');
        const ctx = canvas.getContext('2d', { willReadFrequently: true });
        let isDrawing = false;
        let currentImageSrc = '';

        // --- INIT ---
        window.onload = () => {
            renderCategories();
            setupCanvasEvents();
            setupAdmin();
        };

        // --- VIEWS ---
        function renderCategories() {
            navBar.style.display = 'none';
            main.innerHTML = `<div class="grid" id="cat-grid"></div>`;
            const grid = document.getElementById('cat-grid');

            categories.forEach(cat => {
                const card = document.createElement('div');
                card.className = 'card';
                card.onclick = () => renderDrawingList(cat.id);
                card.innerHTML = `
                    <div class="card-img" style="display:flex;align-items:center;justify-content:center;font-size:4rem;">${cat.icon}</div>
                    <div class="card-title">${cat.name}</div>
                `;
                grid.appendChild(card);
            });
        }

        function renderDrawingList(catId) {
            const category = categories.find(c => c.id === catId);
            navBar.style.display = 'block';
            catTitle.textContent = category.name;
            
            main.innerHTML = `<div class="grid" id="draw-grid"></div>`;
            const grid = document.getElementById('draw-grid');

            if(contentData[catId].length === 0) {
                grid.innerHTML = "<p>No drawings here yet! Use Admin panel to add some.</p>";
                return;
            }

            contentData[catId].forEach(drawing => {
                const card = document.createElement('div');
                card.className = 'card';
                card.onclick = () => openEditor(drawing.src);
                card.innerHTML = `
                    <img src="${drawing.src}" class="card-img" alt="${drawing.title}">
                    <div class="card-title">${drawing.title}</div>
                `;
                grid.appendChild(card);
            });
        }

        function goHome() { renderCategories(); }

        // --- EDITOR LOGIC ---
        function openEditor(imageSrc) {
            document.getElementById('editor-modal').style.display = 'flex';
            currentImageSrc = imageSrc;
            setTool('brush'); // Reset to brush
            resizeCanvas();
            loadCanvasImage(imageSrc);
        }

        function closeEditor() { document.getElementById('editor-modal').style.display = 'none'; }

        function resizeCanvas() {
            const container = canvas.parentElement;
            canvas.width = container.clientWidth;
            canvas.height = container.clientHeight - 80; 
        }

        function loadCanvasImage(src) {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            ctx.fillStyle = "white";
            ctx.fillRect(0,0, canvas.width, canvas.height);

            const img = new Image();
            img.crossOrigin = "Anonymous"; // Crucial for external images
            img.onload = () => {
                const hRatio = canvas.width / img.width;
                const vRatio = canvas.height / img.height;
                const ratio  = Math.min(hRatio, vRatio) * 0.9;
                const centerShift_x = (canvas.width - img.width*ratio) / 2;
                const centerShift_y = (canvas.height - img.height*ratio) / 2;  
                ctx.drawImage(img, 0,0, img.width, img.height, centerShift_x, centerShift_y, img.width*ratio, img.height*ratio);
            }
            img.src = src;
        }

        function setTool(tool) {
            currentTool = tool;
            document.getElementById('btnBrush').classList.toggle('active', tool === 'brush');
            document.getElementById('btnFill').classList.toggle('active', tool === 'fill');
            document.getElementById('brushSizeGroup').style.display = tool === 'brush' ? 'flex' : 'none';
        }

        function clearCanvas() {
            if(confirm('Start over?')) loadCanvasImage(currentImageSrc);
        }

        function saveDrawing() {
            const link = document.createElement('a');
            link.download = 'my-art.png';
            link.href = canvas.toDataURL('image/png');
            link.click();
        }

        // --- DRAWING ALGORITHMS (FLOOD FILL) ---
        function hexToRgb(hex) {
            const result = /^#?([a-f\d]{2})([a-f\d]{2})([a-f\d]{2})$/i.exec(hex);
            return result ? { r: parseInt(result[1], 16), g: parseInt(result[2], 16), b: parseInt(result[3], 16) } : null;
        }

        function floodFill(x, y, fillColor) {
            const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);
            const data = imageData.data;
            
            const startPos = (y * canvas.width + x) * 4;
            const startR = data[startPos];
            const startG = data[startPos + 1];
            const startB = data[startPos + 2];
            const startA = data[startPos + 3];

            // Don't fill if color is same or clicking on black outline
            if (startR === fillColor.r && startG === fillColor.g && startB === fillColor.b) return;
            if (startR < 50 && startG < 50 && startB < 50) return; // Simple outline detection

            const stack = [[x, y]];
            const width = canvas.width;
            const height = canvas.height;

            while (stack.length) {
                const [cx, cy] = stack.pop();
                const pos = (cy * width + cx) * 4;

                if (cx < 0 || cx >= width || cy < 0 || cy >= height) continue;
                
                // Check if current pixel matches start color
                if (data[pos] === startR && data[pos+1] === startG && data[pos+2] === startB) {
                    
                    // Color it
                    data[pos] = fillColor.r;
                    data[pos+1] = fillColor.g;
                    data[pos+2] = fillColor.b;
                    data[pos+3] = 255;

                    stack.push([cx + 1, cy]);
                    stack.push([cx - 1, cy]);
                    stack.push([cx, cy + 1]);
                    stack.push([cx, cy - 1]);
                }
            }
            ctx.putImageData(imageData, 0, 0);
        }

        function setupCanvasEvents() {
            const colorPicker = document.getElementById('colorPicker');
            const brushSize = document.getElementById('brushSize');

            function getPos(e) {
                const rect = canvas.getBoundingClientRect();
                const clientX = e.clientX || e.touches[0].clientX;
                const clientY = e.clientY || e.touches[0].clientY;
                return { x: Math.floor(clientX - rect.left), y: Math.floor(clientY - rect.top) };
            }

            // MOUSE EVENTS
            canvas.addEventListener('mousedown', (e) => {
                const pos = getPos(e);
                if(currentTool === 'fill') {
                    floodFill(pos.x, pos.y, hexToRgb(colorPicker.value));
                } else {
                    isDrawing = true;
                    ctx.beginPath();
                    ctx.moveTo(pos.x, pos.y);
                }
            });

            canvas.addEventListener('mousemove', (e) => {
                if (!isDrawing || currentTool === 'fill') return;
                const pos = getPos(e);
                ctx.lineWidth = brushSize.value;
                ctx.lineCap = 'round';
                ctx.lineJoin = 'round';
                ctx.strokeStyle = colorPicker.value;
                ctx.lineTo(pos.x, pos.y);
                ctx.stroke();
            });

            canvas.addEventListener('mouseup', () => isDrawing = false);
            
            // TOUCH EVENTS
            canvas.addEventListener('touchstart', (e) => { 
                e.preventDefault(); // Stop scroll
                const pos = getPos(e);
                if(currentTool === 'fill') {
                    floodFill(pos.x, pos.y, hexToRgb(colorPicker.value));
                } else {
                    isDrawing = true;
                    ctx.beginPath();
                    ctx.moveTo(pos.x, pos.y);
                }
            });
            canvas.addEventListener('touchmove', (e) => {
                if (!isDrawing || currentTool === 'fill') return;
                e.preventDefault();
                const pos = getPos(e);
                ctx.lineWidth = brushSize.value;
                ctx.lineCap = 'round';
                ctx.lineJoin = 'round';
                ctx.strokeStyle = colorPicker.value;
                ctx.lineTo(pos.x, pos.y);
                ctx.stroke();
            });
            canvas.addEventListener('touchend', () => isDrawing = false);
        }

        // --- ADMIN PANEL LOGIC ---

        function openAdmin() {
            document.getElementById('admin-modal').style.display = 'flex';
        }

        function checkAdmin() {
            const pass = document.getElementById('admin-pass').value;
            if (pass === ADMIN_PASS) {
                document.getElementById('admin-login').style.display = 'none';
                document.getElementById('admin-dashboard').style.display = 'block';
                loadAdminDashboard();
            } else {
                alert("Incorrect Password!");
            }
        }

        function setupAdmin() {
            const select = document.getElementById('admin-cat-select');
            categories.forEach(cat => {
                const option = document.createElement('option');
                option.value = cat.id;
                option.text = cat.name;
                select.appendChild(option);
            });
        }

        function loadAdminDashboard() {
            loadAdminList();
        }

        function loadAdminList() {
            const catId = document.getElementById('admin-cat-select').value;
            const list = document.getElementById('admin-list');
            list.innerHTML = '';

            contentData[catId].forEach((item, index) => {
                const li = document.createElement('li');
                li.innerHTML = `
                    <div style="display:flex;align-items:center;">
                        <img src="${item.src}">
                        <span>${item.title}</span>
                    </div>
                    <button class="btn-delete" onclick="deleteDrawing('${catId}', ${index})">Delete</button>
                `;
                list.appendChild(li);
            });
        }

        function addDrawing() {
            const catId = document.getElementById('admin-cat-select').value;
            const url = document.getElementById('new-img-url').value;
            const title = document.getElementById('new-img-title').value;

            if(!url || !title) { alert("Please fill both fields"); return; }

            contentData[catId].push({
                id: Date.now(),
                title: title,
                src: url
            });
            saveData(); // Save to LocalStorage
            
            // Reset form
            document.getElementById('new-img-url').value = '';
            document.getElementById('new-img-title').value = '';
            
            loadAdminList(); // Refresh list
            alert("Drawing Added!");
        }

        function deleteDrawing(catId, index) {
            if(confirm("Are you sure you want to delete this drawing?")) {
                contentData[catId].splice(index, 1);
                saveData();
                loadAdminList();
            }
        }

    </script>
</body>
</html>