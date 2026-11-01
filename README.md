<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Private GPS Manhunt</title>
    <!-- Leaflet CSS for Map -->
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <style>
        * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; }
        body, html { height: 100%; width: 100%; }
        #map { height: 100vh; width: 100vw; z-index: 1; }
        
        /* Modern UI Overlay Box Styling */
        #ui-overlay {
            position: absolute; top: 20px; left: 20px; z-index: 1000;
            background: rgba(255, 255, 255, 0.95); padding: 20px; 
            border-radius: 12px; box-shadow: 0 8px 16px rgba(0,0,0,0.25);
            width: 300px;
        }
        #ui-overlay h2 { margin-bottom: 15px; font-size: 20px; color: #333; text-align: center; }
        input, select, button {
            width: 100%; padding: 10px; margin-top: 10px; 
            border: 1px solid #ccc; border-radius: 6px; font-size: 14px;
        }
        button {
            background: #2563eb; color: white; border: none; font-weight: bold; cursor: pointer;
            transition: background 0.2s;
        }
        button:hover { background: #1d4ed8; }
        #status { margin-top: 10px; font-weight: bold; color: #16a34a; text-align: center; }
    </style>
</head>
<body>

    <div id="ui-overlay">
        <h2>GPS Manhunt</h2>
        <div id="login-box">
            <input type="text" id="username" placeholder="Enter Your Name" />
            <select id="role">
                <option value="hunter">Hunter 🔍</option>
                <option value="shadow">Shadow 👥</option>
            </select>
            <button onclick="joinGame()">Start Game</button>
        </div>
        <div id="status" style="display:none;">Role: <span id="my-role"></span></div>
    </div>

    <div id="map"></div>

    <!-- Leaflet JS -->
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    <script>
        let map, myMarker;

        function joinGame() {
            const name = document.getElementById('username').value;
            const role = document.getElementById('role').value;
            if(!name) return alert('Please enter your name!');

            document.getElementById('login-box').style.display = 'none';
            document.getElementById('status').style.display = 'block';
            document.getElementById('my-role').innerText = role.toUpperCase();

            initMap();
        }

        function initMap() {
            // Default center (Delhi coordinates)
            map = L.map('map').setView([28.6139, 77.2090], 16);

            L.tileLayer('https://{s}.tile.openstreetmap.org/{z}s/{x}/{y}.png', {
                maxZoom: 19,
                attribution: '© OpenStreetMap contributors'
            }).addTo(map);

            // Real-time GPS Tracker using HTML5 Geolocation
            if (navigator.geolocation) {
                navigator.geolocation.watchPosition((position) => {
                    const lat = position.coords.latitude;
                    const lng = position.coords.longitude;

                    if (!myMarker) {
                        myMarker = L.marker([lat, lng]).addTo(map).bindPopup("Aap yahan hain").openPopup();
                        map.setView([lat, lng], 17);
                    } else {
                        myMarker.setLatLng([lat, lng]);
                    }
                }, (err) => {
                    alert('GPS Error: ' + err.message);
                }, { enableHighAccuracy: true, maximumAge: 0, timeout: 5000 });
            } else {
                alert("Geolocation is not supported by your browser");
            }
        }
    </script>
</body>
</html>
