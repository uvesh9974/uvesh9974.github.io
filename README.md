<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Code 128 Barcode Generator</title>
    <script src="https://cdn.jsdelivr.net/npm/jsbarcode@3.11.5/dist/JsBarcode.all.min.js"></script>
    <style>
        body {
            font-family: Arial, sans-serif;
            max-width: 800px;
            margin: 0 auto;
            padding: 20px;
            background-color: #f5f5f5;
        }
        h1 {
            color: #333;
            text-align: center;
        }
        .container {
            background-color: white;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
        }
        .input-group {
            margin-bottom: 20px;
        }
        label {
            display: block;
            margin-bottom: 5px;
            font-weight: bold;
        }
        input[type="text"] {
            width: 100%;
            padding: 8px;
            border: 1px solid #ddd;
            border-radius: 4px;
            box-sizing: border-box;
        }
        button {
            background-color: #4CAF50;
            color: white;
            border: none;
            padding: 10px 15px;
            border-radius: 4px;
            cursor: pointer;
            font-size: 16px;
        }
        button:hover {
            background-color: #45a049;
        }
        #barcode-container {
            margin-top: 20px;
            text-align: center;
            padding: 20px;
            border: 1px dashed #ddd;
            min-height: 100px;
        }
        .options {
            display: flex;
            gap: 15px;
            margin-bottom: 15px;
        }
        .option-group {
            flex: 1;
        }
        select {
            width: 100%;
            padding: 8px;
            border: 1px solid #ddd;
            border-radius: 4px;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Code 128 Barcode Generator</h1>
        
        <div class="input-group">
            <label for="barcode-text">Enter text to encode:</label>
            <input type="text" id="barcode-text" placeholder="Enter text for barcode">
        </div>
        
        <div class="options">
            <div class="option-group">
                <label for="barcode-format">Barcode Format:</label>
                <select id="barcode-format">
                    <option value="CODE128">CODE128 (Auto)</option>
                    <option value="CODE128A">CODE128A (Uppercase, numbers, control chars)</option>
                    <option value="CODE128B">CODE128B (Standard alphanumeric)</option>
                    <option value="CODE128C">CODE128C (Numbers only, pairs of digits)</option>
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

    <script>
        document.addEventListener('DOMContentLoaded', function() {
            const generateBtn = document.getElementById('generate-btn');
            const downloadBtn = document.getElementById('download-btn');
            const barcodeText = document.getElementById('barcode-text');
            const barcodeFormat = document.getElementById('barcode-format');
            const barcodeWidth = document.getElementById('barcode-width');
            const barcodeHeight = document.getElementById('barcode-height');
            
            // Generate barcode when page loads (with default text)
            generateBarcode();
            
            generateBtn.addEventListener('click', generateBarcode);
            
            downloadBtn.addEventListener('click', function() {
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
            
            function generateBarcode() {
                const text = barcodeText.value || 'EXAMPLE';
                
                JsBarcode('#barcode', text, {
                    format: barcodeFormat.value,
                    width: parseInt(barcodeWidth.value),
                    height: parseInt(barcodeHeight.value),
                    displayValue: true,
                    fontSize: 16,
                    margin: 10,
                    lineColor: '#000000',
                    background: '#ffffff'
                });
            }
        });
    </script>
</body>
</html>
