<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Absensi Pramuka - Kehadiran Anak Didik dan Pengajar</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #228B22; /* Hijau pramuka */
            color: white;
            margin: 0;
            padding: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            background-image: url('https://upload.wikimedia.org/wikipedia/commons/thumb/5/5a/Pramuka_Indonesia_logo.svg/200px-Pramuka_Indonesia_logo.svg.png'); /* Logo pramuka sebagai background watermark */
            background-repeat: no-repeat;
            background-position: center;
            background-size: 300px;
        }
        .container {
            background-color: rgba(0, 0, 0, 0.7);
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.5);
            width: 400px;
            text-align: center;
        }
        h1 {
            color: #FFD700; /* Emas untuk judul */
        }
        label {
            display: block;
            margin: 10px 0 5px;
        }
        input, select, textarea {
            width: 100%;
            padding: 8px;
            margin-bottom: 10px;
            border: none;
            border-radius: 5px;
        }
        button {
            background-color: #FFD700;
            color: black;
            padding: 10px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            margin: 5px;
        }
        button:hover {
            background-color: #FFA500;
        }
        #video {
            width: 100%;
            height: 200px;
            border: 2px solid white;
            display: none;
        }
        #alasan {
            display: none;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Absensi Pramuka</h1>
        <p>Selamat datang di sistem absensi anak didik dan pengajar. Pastikan lokasi dan wajah diverifikasi untuk kejujuran!</p>
        
        <form id="absensiForm">
            <label for="nama">Nama:</label>
            <input type="text" id="nama" required>
            
            <label for="status">Status Kehadiran:</label>
            <select id="status" required>
                <option value="">Pilih Status</option>
                <option value="hadir">Hadir</option>
                <option value="izin">Izin</option>
                <option value="sakit">Sakit</option>
            </select>
            
            <div id="alasan">
                <label for="alasanText">Alasan:</label>
                <textarea id="alasanText" rows="3"></textarea>
            </div>
            
            <button type="button" id="getLocation">Dapatkan Lokasi (GPS)</button>
            <p id="locationDisplay"></p>
            
            <button type="button" id="startCamera">Mulai Verifikasi Wajah</button>
            <video id="video" autoplay></video>
            <button type="button" id="captureFace" style="display:none;">Capture & Verifikasi Wajah</button>
            
            <button type="submit">Submit Absensi</button>
        </form>
        
        <div id="result"></div>
    </div>

    <script>
        const statusSelect = document.getElementById('status');
        const alasanDiv = document.getElementById('alasan');
        const getLocationBtn = document.getElementById('getLocation');
        const locationDisplay = document.getElementById('locationDisplay');
        const startCameraBtn = document.getElementById('startCamera');
        const video = document.getElementById('video');
        const captureFaceBtn = document.getElementById('captureFace');
        const form = document.getElementById('absensiForm');
        const resultDiv = document.getElementById('result');
        
        let stream;
        let latitude, longitude;
        
        // Tampilkan alasan jika izin atau sakit
        statusSelect.addEventListener('change', () => {
            if (statusSelect.value === 'izin' || statusSelect.value === 'sakit') {
                alasanDiv.style.display = 'block';
            } else {
                alasanDiv.style.display = 'none';
            }
        });
        
        // Dapatkan lokasi GPS
        getLocationBtn.addEventListener('click', () => {
            if (navigator.geolocation) {
                navigator.geolocation.getCurrentPosition((position) => {
                    latitude = position.coords.latitude;
                    longitude = position.coords.longitude;
                    locationDisplay.textContent = `Lokasi: ${latitude}, ${longitude}`;
                }, (error) => {
                    alert('Gagal mendapatkan lokasi: ' + error.message);
                });
            } else {
                alert('Geolocation tidak didukung oleh browser ini.');
            }
        });
        
        // Mulai kamera untuk verifikasi wajah
        startCameraBtn.addEventListener('click', async () => {
            try {
                stream = await navigator.mediaDevices.getUserMedia({ video: true });
                video.srcObject = stream;
                video.style.display = 'block';
                captureFaceBtn.style.display = 'inline-block';
            } catch (error) {
                alert('Gagal mengakses kamera: ' + error.message);
            }
        });
        
        // Capture dan simulasi verifikasi wajah
        captureFaceBtn.addEventListener('click', () => {
            // Simulasi verifikasi (dalam dunia nyata, kirim gambar ke API AI)
            alert('Wajah berhasil diverifikasi! (Simulasi - dalam produksi, gunakan Face API)');
            video.style.display = 'none';
            captureFaceBtn.style.display = 'none';
            if (stream) {
                stream.getTracks().forEach(track => track.stop());
            }
        });
        
        // Submit form
        form.addEventListener('submit', (e) => {
            e.preventDefault();
            const nama = document.getElementById('nama').value;
            const status = statusSelect.value;
            const alasan = document.getElementById('alasanText').value;
            
            if (!latitude || !longitude) {
                alert('Harap dapatkan lokasi terlebih dahulu!');
                return;
            }
            
            // Simpan ke localStorage (dalam produksi, kirim ke server)
            const absensi = { nama, status, alasan, latitude, longitude, timestamp: new Date().toISOString() };
            let absensiList = JSON.parse(localStorage.getItem('absensiPramuka')) || [];
            absensiList.push(absensi);
            localStorage.setItem('absensiPramuka', JSON.stringify(absensiList));
            
            resultDiv.innerHTML = `<p>Absensi berhasil disimpan untuk ${nama}!</p>`;
            form.reset();
            locationDisplay.textContent = '';
        });
    </script>
</body>
</html>
