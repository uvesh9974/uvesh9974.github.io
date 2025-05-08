<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Bulk Code 128 Barcode Generator</title>
    <script src="https://cdn.jsdelivr.net/npm/jsbarcode@3.11.5/dist/JsBarcode.all.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jszip/3.10.1/jszip.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/FileSaver.js/2.0.5/FileSaver.min.js"></script>
    <style>
        body {
            font-family: Arial, sans-serif;
            max-width: 800px;
            margin: 0 auto;
            padding: 20px;
            background-color: #f5f5f5;
        }
        .container {
            background-color: white;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
        }
        .tab-container {
            display: flex;
            margin-bottom: 20px;
        }
        .tab {
            padding: 10px 20px;
            cursor: pointer;
            background-color: #f1f1f1;
            border: 1px solid #ddd;
            margin-right: 5px;
        }
        .tab.active {
            background-color: #4CAF50;
            color: white;
        }
        .tab-content {
            display: none;
        }
        .tab-content.active {
            display: block;
        }
        .input-group {
            margin-bottom: 15px;
        }
        label {
            display: block;
            margin-bottom: 5px;
            font-weight: bold;
        }
        input[type="text"], input[type="file"], select {
            width: 100%;
            padding: 8px;
            border: 1px solid #ddd;
            border-radius: 4px;
            box-sizing: border-box;
            margin-bottom: 10px;
        }
        button {
            background-color: #4CAF50;
            color: white;
            border: none;
            padding: 10px 15px;
            border-radius: 4px;
            cursor: pointer;
            font-size: 16px;
            margin-right: 10px;
        }
        button:hover {
            background-color: #45a049;
        }
        #barcode-container, #bulk-results, #google-results {
            margin-top: 20px;
            text-align: center;
            padding: 20px;
            border: 1px dashed #ddd;
            min-height: 100px;
        }
        #bulk-results, #google-results {
            display: flex;
            flex-wrap: wrap;
            gap: 20px;
        }
        .barcode-item {
            border: 1px solid #ddd;
            padding: 10px;
            text-align: center;
        }
        .progress-container {
            width: 100%;
            background-color: #f1f1f1;
            margin: 20px 0;
        }
        #progress-bar, #google-progress-bar {
            width: 0%;
            height: 30px;
            background-color: #4CAF50;
            text-align: center;
            line-height: 30px;
            color: white;
        }
        .options {
            display: flex;
            gap: 15px;
            margin-bottom: 15px;
        }
        .option-group {
            flex: 1;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Bulk Code 128 Barcode Generator</h1>
        
        <div class="tab-container">
            <div class="tab active" onclick="openTab('single-tab')">Single Barcode</div>
            <div class="tab" onclick="openTab('bulk-tab')">Bulk from Excel</div>
            <div class="tab" onclick="openTab('google-tab')">Bulk from Google Sheets</div>
        </div>
        
        <!-- Single Barcode Tab -->
        <div id="single-tab" class="tab-content active">
            <div class="input-group">
                <label for="barcode-text">Enter text to encode:</label>
                <input type="text" id="barcode-text" placeholder="Enter text for barcode" value="EXAMPLE">
            </div>
            
            <div class="options">
                <div class="option-group">
                    <label for="barcode-format">Barcode Format:</label>
                    <select id="barcode-format">
                        <option value="CODE128">CODE128 (Auto)</option>
                        <option value="CODE128A">CODE128A (Uppercase)</option>
                        <option value="CODE128B">CODE128B (Standard)</option>
                        <option value="CODE128C">CODE128C (Numbers only)</option>
                    </select>
                </div>
                
                <div class="option-group">
                    <label for="barcode-width">Line Width:</label>
                    <select id="barcode-width">
                        <option value="1">1 (Thin)</option>
                        <option value="2" selected>2 (Normal)</option>
                        <option value="3">3 (Thick)</option>
                    </select>
                </div>
                
                <div class="option-group">
                    <label for="barcode-height">Height (px):</label>
                    <select id="barcode-height">
                        <option value="50">50</option>
                        <option value="100" selected>100</option>
                        <option value="150">150</option>
                    </select>
                </div>
            </div>
            
            <button id="generate-btn">Generate Barcode</button>
            <button id="download-btn">Download PNG</button>
            
            <div id="barcode-container">
                <svg id="barcode"></svg>
            </div>
        </div>
        
        <!-- Bulk Excel Tab -->
        <div id="bulk-tab" class="tab-content">
            <div class="input-group">
                <label for="excel-file">Upload Excel/CSV File:</label>
                <input type="file" id="excel-file" accept=".xlsx,.xls,.csv">
            </div>
            
            <div class="input-group">
                <label for="column-name">Column containing barcode data:</label>
                <input type="text" id="column-name" placeholder="Enter column name (e.g., 'ProductID')">
            </div>
            
            <button id="generate-bulk-btn">Generate Barcodes</button>
            
            <div class="progress-container">
                <div id="progress-bar">0%</div>
            </div>
            
            <div id="bulk-results"></div>
            <button id="download-all-btn" style="display:none; margin-top:20px;">Download All as ZIP</button>
        </div>
        
        <!-- Google Sheets Tab -->
        <div id="google-tab" class="tab-content">
            <div class="input-group">
                <label for="google-sheet-link">Google Sheets Shareable Link:</label>
                <input type="text" id="google-sheet-link" placeholder="https://docs.google.com/spreadsheets/d/...">
            </div>
            
            <div style="display: flex; justify-content: space-between;">
                <div style="width: 48%;">
                    <label for="sheet-name">Sheet Name:</label>
                    <input type="text" id="sheet-name" placeholder="Sheet1">
                </div>
                <div style="width: 48%;">
                    <label for="range">Data Range (e.g., A2:B100):</label>
                    <input type="text" id="range" placeholder="A2:B100">
                </div>
            </div>
            
            <div class="input-group">
                <label for="google-column-name">Column containing barcode data:</label>
                <input type="text" id="google-column-name" placeholder="Enter column name (e.g., 'ProductID')">
            </div>
            
            <button id="generate-google-btn">Generate from Google Sheet</button>
            
            <div class="progress-container">
                <div id="google-progress-bar">0%</div>
            </div>
            
            <div id="google-results"></div>
            <button id="download-google-btn" style="display:none; margin-top:20px;">Download All as ZIP</button>
        </div>
    </div>

    <script>
        // Tab functionality
        function openTab(tabId) {
            // Hide all tab contents
            document.querySelectorAll('.tab-content').forEach(tab => {
                tab.classList.remove('active');
            });
            
            // Show the selected tab content
            document.getElementById(tabId).classList.add('active');
            
            // Update tab styling
            document.querySelectorAll('.tab').forEach(tab => {
                tab.classList.remove('active');
            });
            event.currentTarget.classList.add('active');
        }
        
        // Single barcode generation
        function generateBarcode() {
            const text = document.getElementById('barcode-text').value || 'EXAMPLE';
            
            try {
                JsBarcode('#barcode', text, {
                    format: document.getElementById('barcode-format').value,
                    width: parseInt(document.getElementById('barcode-width').value),
                    height: parseInt(document.getElementById('barcode-height').value),
                    displayValue: true,
                    fontSize: 16,
                    margin: 10,
                    lineColor: '#000000',
                    background: '#ffffff'
                });
            } catch (e) {
                console.error("Barcode generation error:", e);
                alert("Error generating barcode. Please check your input.");
            }
        }
        
        // Bulk Excel Generation
        document.getElementById('generate-bulk-btn').addEventListener('click', generateBulkBarcodes);
        
        async function generateBulkBarcodes() {
            const fileInput = document.getElementById('excel-file');
            const columnName = document.getElementById('column-name').value;
            const resultsContainer = document.getElementById('bulk-results');
            const progressBar = document.getElementById('progress-bar');
            
            if (!fileInput.files.length || !columnName) {
                alert('Please select a file and specify the column name');
                return;
            }
            
            resultsContainer.innerHTML = '';
            progressBar.style.width = '0%';
            progressBar.textContent = '0%';
            
            const file = fileInput.files[0];
            const data = await readExcelFile(file);
            
            if (!data.length) {
                alert('No data found in the file');
                return;
            }
            
            // Check if column exists
            if (!data[0].hasOwnProperty(columnName)) {
                alert(`Column "${columnName}" not found in the file`);
                return;
            }
            
            const barcodes = [];
            const totalItems = data.length;
            let processed = 0;
            
            for (const row of data) {
                const barcodeValue = row[columnName];
                if (!barcodeValue) continue;
                
                const barcodeId = `barcode-${Math.random().toString(36).substr(2, 9)}`;
                const container = document.createElement('div');
                container.className = 'barcode-item';
                container.innerHTML = `
                    <div>${barcodeValue}</div>
                    <svg id="${barcodeId}"></svg>
                `;
                resultsContainer.appendChild(container);
                
                try {
                    JsBarcode(`#${barcodeId}`, barcodeValue, getBarcodeOptions());
                    barcodes.push({
                        element: document.getElementById(barcodeId),
                        value: barcodeValue
                    });
                } catch (e) {
                    console.error(`Error generating barcode for ${barcodeValue}:`, e);
                }
                
                processed++;
                const progress = Math.floor((processed / totalItems) * 100);
                progressBar.style.width = `${progress}%`;
                progressBar.textContent = `${progress}%`;
                
                // Small delay to prevent UI freeze
                await new Promise(resolve => setTimeout(resolve, 10));
            }
            
            document.getElementById('download-all-btn').style.display = 'block';
            document.getElementById('download-all-btn').onclick = () => downloadAllBarcodes(barcodes);
        }
        
        // Google Sheets Integration
        document.getElementById('generate-google-btn').addEventListener('click', generateFromGoogleSheet);
        
        async function generateFromGoogleSheet() {
            const sheetLink = document.getElementById('google-sheet-link').value;
            const sheetName = document.getElementById('sheet-name').value || 'Sheet1';
            const range = document.getElementById('range').value;
            const columnName = document.getElementById('google-column-name').value;
            const resultsContainer = document.getElementById('google-results');
            const progressBar = document.getElementById('google-progress-bar');
            
            if (!sheetLink || !columnName) {
                alert('Please provide Google Sheet link and column name');
                return;
            }
            
            // Extract sheet ID from URL
            const sheetId = extractGoogleSheetId(sheetLink);
            if (!sheetId) {
                alert('Invalid Google Sheets URL');
                return;
            }
            
            resultsContainer.innerHTML = '';
            progressBar.style.width = '0%';
            progressBar.textContent = '0%';
            
            try {
                const url = `https://docs.google.com/spreadsheets/d/${sheetId}/gviz/tq?tqx=out:csv&sheet=${encodeURIComponent(sheetName)}${range ? '&range=' + encodeURIComponent(range) : ''}`;
                const response = await fetch(url);
                const csvData = await response.text();
                const data = await parseCSV(csvData);
                
                if (!data.length) {
                    alert('No data found in the sheet');
                    return;
                }
                
                // Check if column exists
                if (!data[0].hasOwnProperty(columnName)) {
                    alert(`Column "${columnName}" not found in the sheet`);
                    return;
                }
                
                const barcodes = [];
                const totalItems = data.length;
                let processed = 0;
                
                for (const row of data) {
                    const barcodeValue = row[columnName];
                    if (!barcodeValue) continue;
                    
                    const barcodeId = `google-barcode-${Math.random().toString(36).substr(2, 9)}`;
                    const container = document.createElement('div');
                    container.className = 'barcode-item';
                    container.innerHTML = `
                        <div>${barcodeValue}</div>
                        <svg id="${barcodeId}"></svg>
                    `;
                    resultsContainer.appendChild(container);
                    
                    try {
                        JsBarcode(`#${barcodeId}`, barcodeValue, getBarcodeOptions());
                        barcodes.push({
                            element: document.getElementById(barcodeId),
                            value: barcodeValue
                        });
                    } catch (e) {
                        console.error(`Error generating barcode for ${barcodeValue}:`, e);
                    }
                    
                    processed++;
                    const progress = Math.floor((processed / totalItems) * 100);
                    progressBar.style.width = `${progress}%`;
                    progressBar.textContent = `${progress}%`;
                    
                    // Small delay to prevent UI freeze
                    await new Promise(resolve => setTimeout(resolve, 10));
                }
                
                document.getElementById('download-google-btn').style.display = 'block';
                document.getElementById('download-google-btn').onclick = () => downloadAllBarcodes(barcodes);
                
            } catch (error) {
                console.error('Error fetching Google Sheet data:', error);
                alert('Error fetching data from Google Sheets. Make sure the sheet is shared publicly.');
            }
        }
        
        // Helper functions
        function extractGoogleSheetId(url) {
            const match = url.match(/\/spreadsheets\/d\/([a-zA-Z0-9-_]+)/);
            return match ? match[1] : null;
        }
        
        async function readExcelFile(file) {
            return new Promise((resolve, reject) => {
                const reader = new FileReader();
                reader.onload = (e) => {
                    try {
                        const data = new Uint8Array(e.target.result);
                        const workbook = XLSX.read(data, { type: 'array' });
                        const firstSheet = workbook.Sheets[workbook.SheetNames[0]];
                        const jsonData = XLSX.utils.sheet_to_json(firstSheet);
                        resolve(jsonData);
                    } catch (error) {
                        reject(error);
                    }
                };
                reader.onerror = reject;
                reader.readAsArrayBuffer(file);
            });
        }
        
        function parseCSV(csv) {
            return new Promise((resolve) => {
                const lines = csv.split('\n');
                const headers = lines[0].split(',').map(h => h.trim().replace(/^"|"$/g, ''));
                const result = [];
                
                for (let i = 1; i < lines.length; i++) {
                    if (!lines[i]) continue;
                    const obj = {};
                    const currentline = lines[i].split(/,(?=(?:(?:[^"]*"){2})*[^"]*$)/);
                    
                    for (let j = 0; j < headers.length; j++) {
                        let value = currentline[j] || '';
                        value = value.trim().replace(/^"|"$/g, '');
                        obj[headers[j]] = value;
                    }
                    
                    result.push(obj);
                }
                
                resolve(result);
            });
        }
        
        function getBarcodeOptions() {
            return {
                format: document.getElementById('barcode-format').value,
                width: parseInt(document.getElementById('barcode-width').value),
                height: parseInt(document.getElementById('barcode-height').value),
                displayValue: true,
                fontSize: 16,
                margin: 10,
                lineColor: '#000000',
                background: '#ffffff'
            };
        }
        
        async function downloadAllBarcodes(barcodes) {
            const zip = new JSZip();
            const imgFolder = zip.folder("barcodes");
            let processed = 0;
            
            for (const barcode of barcodes) {
                try {
                    const svg = barcode.element;
                    const serializer = new XMLSerializer();
                    const svgStr = serializer.serializeToString(svg);
                    const canvas = document.createElement('canvas');
                    const ctx = canvas.getContext('2d');
                    const img = new Image();
                    
                    await new Promise((resolve) => {
                        img.onload = function() {
                            canvas.width = img.width;
                            canvas.height = img.height;
                            ctx.drawImage(img, 0, 0);
                            canvas.toBlob((blob) => {
                                imgFolder.file(`${barcode.value}.png`, blob);
                                processed++;
                                resolve();
                            }, 'image/png');
                        };
                        img.src = 'data:image/svg+xml;base64,' + btoa(unescape(encodeURIComponent(svgStr)));
                    });
                } catch (e) {
                    console.error(`Error processing barcode ${barcode.value}:`, e);
                }
            }
            
            const content = await zip.generateAsync({ type: 'blob' });
            saveAs(content, 'barcodes.zip');
        }
        
        // Download single barcode
        document.getElementById('download-btn').addEventListener('click', function() {
            const svg = document.getElementById('barcode');
            const serializer = new XMLSerializer();
            const svgStr = serializer.serializeToString(svg);
            
            const canvas = document.createElement('canvas');
            const ctx = canvas.getContext('2d');
            const img = new Image();
            
            img.onload = function() {
                canvas.width = img.width;
                canvas.height = img.height;
                ctx.drawImage(img, 0, 0);
                
                const png = canvas.toDataURL('image/png');
                const downloadLink = document.createElement('a');
                downloadLink.href = png;
                downloadLink.download = 'barcode.png';
                document.body.appendChild(downloadLink);
                downloadLink.click();
                document.body.removeChild(downloadLink);
            };
            
            img.src = 'data:image/svg+xml;base64,' + btoa(unescape(encodeURIComponent(svgStr)));
        });
        
        // Initialize
        document.addEventListener('DOMContentLoaded', function() {
            // Set up event listeners
            document.getElementById('generate-btn').addEventListener('click', generateBarcode);
            
            // Generate initial barcode
            generateBarcode();
        });
    </script>
</body>
</html>
