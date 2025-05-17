<script type="text/javascript">
        var gk_isXlsx = false;
        var gk_xlsxFileLookup = {};
        var gk_fileData = {};
        function filledCell(cell) {
          return cell !== '' && cell != null;
        }
        function loadFileData(filename) {
        if (gk_isXlsx && gk_xlsxFileLookup[filename]) {
            try {
                var workbook = XLSX.read(gk_fileData[filename], { type: 'base64' });
                var firstSheetName = workbook.SheetNames[0];
                var worksheet = workbook.Sheets[firstSheetName];

                // Convert sheet to JSON to filter blank rows
                var jsonData = XLSX.utils.sheet_to_json(worksheet, { header: 1, blankrows: false, defval: '' });
                // Filter out blank rows (rows where all cells are empty, null, or undefined)
                var filteredData = jsonData.filter(row => row.some(filledCell));

                // Heuristic to find the header row by ignoring rows with fewer filled cells than the next row
                var headerRowIndex = filteredData.findIndex((row, index) =>
                  row.filter(filledCell).length >= filteredData[index + 1]?.filter(filledCell).length
                );
                // Fallback
                if (headerRowIndex === -1 || headerRowIndex > 25) {
                  headerRowIndex = 0;
                }

                // Convert filtered JSON back to CSV
                var csv = XLSX.utils.aoa_to_sheet(filteredData.slice(headerRowIndex)); // Create a new sheet from filtered array of arrays
                csv = XLSX.utils.sheet_to_csv(csv, { header: 1 });
                return csv;
            } catch (e) {
                console.error(e);
                return "";
            }
        }
        return gk_fileData[filename] || "";
        }
        </script><!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>DATA CONTAINER SURVEY</title>
    <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@400;600;700&display=swap" rel="stylesheet">
    <style>
        * {
            box-sizing: border-box;
        }

        body {
            margin: 0;
            padding: 0;
            font-family: 'Poppins', Arial, sans-serif;
            background-color: #4682B4;
            background-image: url('assets/aerial-view-container-cargo-ship-sea.jpg'); /* Path tetap sama */
            background-size: cover;
            background-position: center;
            background-repeat: no-repeat;
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: flex-start;
            padding: 20px;
            overflow: auto;
        }

        form {
            background: linear-gradient(135deg, rgba(255, 255, 255, 0.97), rgba(200, 230, 255, 0.9));
            padding: 20px;
            border-radius: 15px;
            box-shadow: 0 10px 25px rgba(0, 0, 0, 0.3);
            width: 100%;
            max-width: 500px;
            backdrop-filter: blur(8px);
            animation: slideIn 0.8s ease-out;
        }

        @keyframes slideIn {
            from { opacity: 0; transform: translateY(-30px); }
            to { opacity: 1; transform: translateY(0); }
        }

        h1 {
            text-align: center;
            color: #1C2526;
            font-size: clamp(20px, 5vw, 26px);
            font-weight: 700;
            margin-bottom: 20px;
            text-transform: uppercase;
            letter-spacing: 1.5px;
            text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.1);
        }

        .form-group {
            background: rgba(255, 255, 255, 0.9);
            padding: 12px;
            margin-bottom: 12px;
            border-radius: 8px;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
            transition: transform 0.3s, box-shadow 0.3s;
        }

        .form-group:hover {
            transform: translateY(-2px);
            box-shadow: 0 6px 15px rgba(0, 0, 0, 0.2);
        }

        label {
            display: block;
            font-weight: 600;
            color: #1C2526;
            margin-bottom: 6px;
            font-size: clamp(12px, 3vw, 14px);
        }

        input[type="text"], select {
            width: 100%;
            padding: 10px;
            border: 2px solid #4682B4;
            border-radius: 6px;
            background-color: rgba(255, 255, 255, 0.8);
            font-size: clamp(14px, 3.5vw, 16px);
            font-family: 'Poppins', sans-serif;
            transition: border-color 0.3s, box-shadow 0.3s, background-color 0.3s;
        }

        input[type="text"]:focus, select:focus {
            outline: none;
            border-color: #1E90FF;
            background-color: #fff;
            box-shadow: 0 0 8px rgba(30, 144, 255, 0.5);
        }

        .radio-group {
            margin-bottom: 12px;
            padding: 12px;
            background: rgba(255, 255, 255, 0.9);
            border-radius: 8px;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
            display: flex;
            flex-wrap: wrap;
            gap: 10px;
        }

        .radio-group label {
            font-weight: normal;
            font-size: clamp(12px, 3vw, 14px);
            margin-right: 15px;
        }

        input[type="radio"] {
            margin: 0 5px 0 0;
            accent-color: #1E90FF;
        }

        #container-details {
            border-top: 2px dashed #4682B4;
            padding-top: 15px;
            margin-top: 15px;
        }

        .container-group {
            position: relative;
            margin-bottom: 15px;
            padding: 12px;
            border: 1px solid #4682B4;
            border-radius: 8px;
            background: rgba(255, 255, 255, 0.95);
        }

        .remove-container {
            position: absolute;
            top: 8px;
            right: 8px;
            background: #ff4d4d;
            color: white;
            border: none;
            border-radius: 50%;
            width: 20px;
            height: 20px;
            cursor: pointer;
            font-size: 12px;
            line-height: 20px;
            text-align: center;
            transition: background 0.3s;
        }

        .remove-container:hover {
            background: #cc0000;
        }

        .set-temp-group {
            display: none;
        }

        button {
            background: linear-gradient(to right, #1E90FF, #4682B4);
            color: white;
            padding: 10px 20px;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            font-size: clamp(14px, 3.5vw, 16px);
            font-weight: 600;
            font-family: 'Poppins', sans-serif;
            margin: 8px 8px 0 0;
            transition: transform 0.3s, box-shadow 0.3s, background 0.3s;
        }

        button:hover {
            background: linear-gradient(to right, #4682B4, #1E90FF);
            transform: translateY(-2px);
            box-shadow: 0 6px 15px rgba(0, 0, 0, 0.2);
        }

        button:active {
            transform: translateY(0);
        }

        #thank-you-message {
            display: none;
            text-align: center;
            color: #1E90FF;
            font-size: clamp(14px, 3.5vw, 16px);
            font-weight: 600;
            margin-top: 15px;
            animation: fadeInMessage 1s ease-in;
        }

        @keyframes fadeInMessage {
            from { opacity: 0; }
            to { opacity: 1; }
        }

        @media (max-width: 600px) {
            body {
                padding: 10px;
            }

            form {
                padding: 15px;
                max-width: 100%;
            }

            h1 {
                font-size: clamp(18px, 4.5vw, 22px);
            }

            .form-group, .radio-group, .container-group {
                padding: 10px;
            }

            input[type="text"], select, button {
                font-size: clamp(12px, 3.2vw, 14px);
                padding: 8px;
            }
        }

        @media (min-width: 601px) and (max-width: 900px) {
            form {
                max-width: 450px;
            }
        }
    </style>
</head>
<body>
    <form id="survey-form">
        <h1>DATA CONTAINER SURVEY</h1>
        <div class="form-group">
            <label for="booking-number">Booking Number</label>
            <input type="text" id="booking-number" name="booking-number">
        </div>

        <div class="form-group">
            <label for="shipper-name">Shipper Name</label>
            <input type="text" id="shipper-name" name="shipper-name">
        </div>

        <div class="form-group">
            <label for="vessel-name">Vessel Name</label>
            <input type="text" id="vessel-name" name="vessel-name">
        </div>

        <div class="form-group">
            <label for="whatsapp-number">WhatsApp Number</label>
            <input type="text" id="whatsapp-number" name="whatsapp-number">
        </div>

        <div class="radio-group">
            <label>DG Status</label>
            <input type="radio" id="dg" name="dg-status" value="DG">
            <label for="dg">DG</label>
            <input type="radio" id="non-dg" name="dg-status" value="NON DG">
            <label for="non-dg">NON DG</label>
        </div>

        <div id="container-details">
            <div class="container-group" id="container-group-1">
                <button type="button" class="remove-container" onclick="removeContainer(1)" style="display: none;">×</button>
                <div class="form-group">
                    <label for="container-number-1">Container Number</label>
                    <input type="text" id="container-number-1" name="container-number-1">
                </div>
                <div class="form-group">
                    <label for="container-type-1">Container Type</label>
                    <select id="container-type-1" name="container-type-1" onchange="toggleSetTemp(1)">
                        <option value="20'GP">20'GP</option>
                        <option value="40'GP">40'GP</option>
                        <option value="40'HC">40'HC</option>
                        <option value="45'HC">45'HC</option>
                        <option value="20'RF">20'RF</option>
                        <option value="40'RH">40'RH</option>
                        <option value="25'RF">25'RF</option>
                    </select>
                </div>
                <div class="form-group set-temp-group" id="set-temp-group-1">
                    <label for="set-temp-1">Set Temp (°C)</label>
                    <input type="text" id="set-temp-1" name="set-temp-1">
                </div>
            </div>
        </div>

        <button type="button" onclick="addContainer()">Add Container</button>
        <button type="submit">Submit</button>
        <div id="thank-you-message">Thank you to submit Data Survey, Enjoy!!</div>
    </form>

    <script>
        let containerCount = 1;

        function toggleSetTemp(containerId) {
            const setTempGroup = document.getElementById(`set-temp-group-${containerId}`);
            const containerType = document.getElementById(`container-type-${containerId}`).value;
            const refrigeratedTypes = ["20'RF", "40'RH", "25'RF"];
            setTempGroup.style.display = refrigeratedTypes.includes(containerType) ? 'block' : 'none';
        }

        function addContainer() {
            containerCount++;
            const containerDetails = document.getElementById('container-details');
            const newContainerGroup = document.createElement('div');
            newContainerGroup.className = 'container-group';
            newContainerGroup.id = `container-group-${containerCount}`;
            newContainerGroup.innerHTML = `
                <button type="button" class="remove-container" onclick="removeContainer(${containerCount})">×</button>
                <div class="form-group">
                    <label for="container-number-${containerCount}">Container Number</label>
                    <input type="text" id="container-number-${containerCount}" name="container-number-${containerCount}">
                </div>
                <div class="form-group">
                    <label for="container-type-${containerCount}">Container Type</label>
                    <select id="container-type-${containerCount}" name="container-type-${containerCount}" onchange="toggleSetTemp(${containerCount})">
                        <option value="20'GP">20'GP</option>
                        <option value="40'GP">40'GP</option>
                        <option value="40'HC">40'HC</option>
                        <option value="45'HC">45'HC</option>
                        <option value="20'RF">20'RF</option>
                        <option value="40'RH">40'RH</option>
                        <option value="25'RF">25'RF</option>
                    </select>
                </div>
                <div class="form-group set-temp-group" id="set-temp-group-${containerCount}">
                    <label for="set-temp-${containerCount}">Set Temp (°C)</label>
                    <input type="text" id="set-temp-${containerCount}" name="set-temp-${containerCount}">
                </div>
            `;
            containerDetails.appendChild(newContainerGroup);
            toggleSetTemp(containerCount);
        }

        function removeContainer(containerId) {
            const containerGroup = document.getElementById(`container-group-${containerId}`);
            if (containerGroup) {
                containerGroup.remove();
            }
        }

        document.getElementById('survey-form').addEventListener('submit', async function(event) {
            event.preventDefault();
            
            // Kumpulkan data utama dari form
            const formData = {
                timestamp: new Date().toISOString(),
                bookingNumber: this.querySelector('#booking-number').value,
                shipperName: this.querySelector('#shipper-name').value,
                vesselName: this.querySelector('#vessel-name').value,
                whatsapp: this.querySelector('#whatsapp-number').value,
                dgStatus: this.querySelector('input[name="dg-status"]:checked')?.value || ''
            };

            // Kumpulkan data container
            const containers = [];
            for (let i = 1; i <= containerCount; i++) {
                const containerNumber = this.querySelector(`#container-number-${i}`)?.value || '';
                const containerType = this.querySelector(`#container-type-${i}`)?.value || '';
                const setTemp = this.querySelector(`#set-temp-${i}`)?.value || '';
                if (containerNumber || containerType) { // Hanya tambahkan jika ada data
                    containers.push({
                        containerNumber,
                        containerType,
                        setTemp
                    });
                }
            }
            formData.containers = containers;

            // Validasi sederhana
            if (!formData.bookingNumber || !formData.shipperName || !formData.vesselName || !formData.whatsapp || !formData.dgStatus) {
                alert('Please fill in all required fields.');
                return;
            }
            if (containers.length === 0) {
                alert('Please add at least one container.');
                return;
            }

            // Kirim data ke Google Apps Script
            try {
                const response = await fetch('https://script.google.com/macros/s/AKfycbzCtzYpRu4fJ19EJVAhW8tVf_vn6t9XvtzbeXtNhLoB4b-NWCw8SLVx02t5ttzu4jXS/exec', {
                    method: 'POST',
                    body: JSON.stringify(formData),
                    headers: { 'Content-Type': 'application/json' }
                });
                const result = await response.json();
                if (result.status === 'success') {
                    const thankYouMessage = document.getElementById('thank-you-message');
                    thankYouMessage.style.display = 'block';
                    setTimeout(() => {
                        thankYouMessage.style.display = 'none';
                        this.reset();
                        const containerDetails = document.getElementById('container-details');
                        containerDetails.innerHTML = `
                            <div class="container-group" id="container-group-1">
                                <button type="button" class="remove-container" onclick="removeContainer(1)" style="display: none;">×</button>
                                <div class="form-group">
                                    <label for="container-number-1">Container Number</label>
                                    <input type="text" id="container-number-1" name="container-number-1">
                                </div>
                                <div class="form-group">
                                    <label for="container-type-1">Container Type</label>
                                    <select id="container-type-1" name="container-type-1" onchange="toggleSetTemp(1)">
                                        <option value="20'GP">20'GP</option>
                                        <option value="40'GP">40'GP</option>
                                        <option value="40'HC">40'HC</option>
                                        <option value="45'HC">45'HC</option>
                                        <option value="20'RF">20'RF</option>
                                        <option value="40'RH">40'RH</option>
                                        <option value="25'RF">25'RF</option>
                                    </select>
                                </div>
                                <div class="form-group set-temp-group" id="set-temp-group-1">
                                    <label for="set-temp-1">Set Temp (°C)</label>
                                    <input type="text" id="set-temp-1" name="set-temp-1">
                                </div>
                            </div>
                        `;
                        containerCount = 1;
                        toggleSetTemp(1);
                    }, 3000);
                } else {
                    alert('Error submitting data: ' + result.message);
                }
            } catch (error) {
                alert('Error submitting data: ' + error.message);
            }
        });
    </script>
</body>
</html>
