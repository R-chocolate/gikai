<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>題目搜尋器</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 20px;
            text-align: center;
        }
        input[type="file"] {
            margin: 10px 0;
        }
        #results {
            margin-top: 20px;
            text-align: left;
            white-space: pre-wrap;
        }
    </style>
</head>
<body>
    <h1>題目搜尋器</h1>
    <p>請上傳 Excel 檔案，然後輸入關鍵字進行搜尋。</p>

    <input type="file" id="fileInput" accept=".xlsx" />
    <br>
    <input type="text" id="keywordInput" placeholder="輸入關鍵字" />
    <button onclick="searchQuestions()">搜尋</button>

    <div id="results"></div>

    <script>
        let workbook;

        // 處理檔案上傳
        document.getElementById('fileInput').addEventListener('change', (event) => {
            const file = event.target.files[0];
            if (!file) return;

            const reader = new FileReader();
            reader.onload = (e) => {
                const data = new Uint8Array(e.target.result);
                workbook = XLSX.read(data, { type: 'array' });
                alert('檔案上傳成功！');
            };
            reader.readAsArrayBuffer(file);
        });

        function searchQuestions() {
            const keyword = document.getElementById('keywordInput').value.trim();
            const resultsDiv = document.getElementById('results');
            resultsDiv.innerHTML = '';

            if (!workbook) {
                resultsDiv.innerHTML = '請先上傳檔案！';
                return;
            }

            if (!keyword) {
                resultsDiv.innerHTML = '請輸入關鍵字！';
                return;
            }

            const sheetName = workbook.SheetNames[0]; // 假設使用第一個工作表
            const sheet = workbook.Sheets[sheetName];
            const data = XLSX.utils.sheet_to_json(sheet, { header: 1 }); // 將表格轉為陣列

            let results = '';
            for (let row of data) {
                if (row.some(cell => cell && cell.toString().includes(keyword))) {
                    results += row.join('\n') + '\n\n'; // 將符合的題目輸出
                }
            }

            if (results) {
                resultsDiv.innerText = results;
            } else {
                resultsDiv.innerText = '未找到匹配的題目或選項。';
            }
        }
    </script>
</body>
</html>
