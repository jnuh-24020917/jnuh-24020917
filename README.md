<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Excel File Upload and Display with Search (Single Upload)</title>
    <style>
        table {
            width: 100%;
            border-collapse: collapse;
        }
        th, td {
            border: 1px solid #ddd;
            padding: 8px;
        }
        th {
            background-color: #f2f2f2;
            text-align: left;
        }
        #searchInput {
            margin-bottom: 10px;
            padding: 8px;
            width: 100%;
            box-sizing: border-box;
        }
    </style>
</head>
<body>
    <h1>Upload and Display Excel File 업로드제한</h1>
    <input type="file" id="fileInput" accept=".xlsx, .xls">
    <input type="text" id="searchInput" placeholder="Search..." oninput="searchTable()">
    <div id="tableContainer"></div>

    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.17.5/xlsx.full.min.js"></script>
    <script>
        let fileUploaded = false; // 파일이 업로드되었는지 여부를 나타내는 변수

        document.getElementById('fileInput').addEventListener('change', handleFile, false);

        function handleFile(event) {
            if (fileUploaded) {
                alert('You can upload only one file.');
                return;
            }

            const file = event.target.files[0];
            const reader = new FileReader();

            reader.onload = function(e) {
                const data = new Uint8Array(e.target.result);
                const workbook = XLSX.read(data, { type: 'array' });

                const sheetName = workbook.SheetNames[0];
                const worksheet = workbook.Sheets[sheetName];
                const html = XLSX.utils.sheet_to_html(worksheet);

                
                document.getElementById('tableContainer').innerHTML = html;
                localStorage.setItem('excelTable', html); // 로컬 저장소에 HTML 저장
                fileUploaded = true;
                document.getElementById('fileInput').disabled = true; // 파일 업로드 비활성화
            };

            reader.readAsArrayBuffer(file);
        }

        // Load the table HTML from localStorage if it exists
        window.onload = function() {
            const savedTable = localStorage.getItem('excelTable');
            if (savedTable) {
                document.getElementById('tableContainer').innerHTML = savedTable;
                fileUploaded = true;
                document.getElementById('fileInput').disabled = true; // 파일 업로드 비활성화
            }
        };

        // Function to search within the table
        function searchTable() {
            const input = document.getElementById('searchInput');
            const filter = input.value.toLowerCase();
            const table = document.querySelector('#tableContainer table');
            const rows = table.getElementsByTagName('tr');

            for (let i = 1; i < rows.length; i++) { // Skip the header row
                const cells = rows[i].getElementsByTagName('td');
                let match = false;
                for (let j = 0; j < cells.length; j++) {
                    if (cells[j].textContent.toLowerCase().includes(filter)) {
                        match = true;
                        break;
                    }
                }
                rows[i].style.display = match ? '' : 'none';
            }
        }
    </script>
</body>
</html>
