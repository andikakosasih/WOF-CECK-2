
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Wheel of Fortune</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/Winwheel.js/2.7.0/Winwheel.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/howler/2.2.3/howler.min.js"></script>
    <style>
        body { text-align: center; font-family: Arial, sans-serif; }
        canvas { margin-top: 20px; }
        button { margin-top: 20px; padding: 10px 20px; font-size: 18px; cursor: pointer; }
    </style>
</head>
<body>
    <h1>Wheel of Fortune</h1>
    <canvas id="canvas" width="500" height="500"></canvas>
    <br>
    <button onclick="startSpin()">Putar Roda</button>
    <p id="winner"></p>

    <script>
        const sheetId = "1aBcD_EFGHIJKlmNOPQRS_tUVWXYZ"; // Ganti dengan ID Spreadsheet Anda
        const apiKey = "YOUR_GOOGLE_SHEETS_API_KEY"; // Ganti dengan API Key Anda
        const range = "Sheet1!A:A"; // Pastikan ini sesuai dengan sheet dan kolom nama peserta

        let participants = [];

        async function fetchParticipants() {
            const url = `https://sheets.googleapis.com/v4/spreadsheets/${sheetId}/values/${range}?key=${apiKey}`;
            try {
                const response = await fetch(url);
                const data = await response.json();
                console.log("Data dari Google Sheets:", data);
                
                if (data.values) {
                    participants = data.values.flat();
                    initializeWheel();
                } else {
                    console.error("Data tidak ditemukan atau format salah.");
                }
            } catch (error) {
                console.error("Error fetching participants:", error);
            }
        }

        function initializeWheel() {
            if (participants.length === 0) {
                console.error("Tidak ada peserta yang ditemukan!");
                return;
            }

            window.wheel = new Winwheel({
                'canvasId': 'canvas',
                'numSegments': participants.length,
                'outerRadius': 200,
                'textFontSize': 18,
                'segments': participants.map(name => ({ 'fillStyle': getRandomColor(), 'text': name })),
                'animation': {
                    'type': 'spinToStop',
                    'duration': 5,
                    'spins': 10,
                    'callbackFinished': alertWinner
                }
            });
        }

        function getRandomColor() {
            return '#' + Math.floor(Math.random()*16777215).toString(16);
        }

        function startSpin() {
            if (!wheel) {
                console.error("Wheel belum diinisialisasi!");
                return;
            }
            wheel.startAnimation();
        }

        function alertWinner() {
            let winner = wheel.getIndicatedSegment().text;
            document.getElementById("winner").innerText = "Pemenang: " + winner;
        }

        fetchParticipants(); // Ambil peserta dari Google Sheets saat halaman dimuat
    </script>
</body>
</html>
