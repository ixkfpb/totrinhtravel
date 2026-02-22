# travel
<!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="UTF-8">
  <title>My Travel Map</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <!-- Leaflet CSS -->
  <link
    rel="stylesheet"
    href="https://unpkg.com/leaflet/dist/leaflet.css"
  />

  <style>
    body {
      margin: 0;
      font-family: Arial, sans-serif;
    }

    #map {
      height: 100vh;
      width: 100%;
    }

    .popup-content img {
      width: 100%;
      margin-top: 10px;
      border-radius: 8px;
    }

    .upload-input {
      margin-top: 10px;
    }
  </style>
</head>
<body>

<div id="map"></div>

<!-- Leaflet JS -->
<script src="https://unpkg.com/leaflet/dist/leaflet.js"></script>

<script>
  // Tạo bản đồ
  const map = L.map('map').setView([20, 0], 2);

  L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
    attribution: '&copy; OpenStreetMap contributors'
  }).addTo(map);

  let locations = JSON.parse(localStorage.getItem('travelLocations')) || [];

  // Load lại các địa điểm đã lưu
  locations.forEach(loc => {
    createMarker(loc);
  });

  // Khi click bản đồ
  map.on('click', function(e) {
    const name = prompt("Nhập tên địa điểm:");

    if (!name) return;

    const newLocation = {
      id: Date.now(),
      name: name,
      lat: e.latlng.lat,
      lng: e.latlng.lng,
      image: null
    };

    locations.push(newLocation);
    saveData();
    createMarker(newLocation);
  });

  function createMarker(location) {
    const marker = L.marker([location.lat, location.lng]).addTo(map);

    let popupContent = `
      <div class="popup-content">
        <strong>${location.name}</strong><br/>
        ${location.image ? `<img src="${location.image}" />` : ''}
        <input type="file" class="upload-input" accept="image/*" />
      </div>
    `;

    marker.bindPopup(popupContent);

    marker.on('popupopen', function() {
      const input = document.querySelector('.upload-input');

      input.addEventListener('change', function(event) {
        const file = event.target.files[0];
        const reader = new FileReader();

        reader.onload = function(e) {
          location.image = e.target.result;
          saveData();
          marker.setPopupContent(`
            <div class="popup-content">
              <strong>${location.name}</strong><br/>
              <img src="${location.image}" />
              <input type="file" class="upload-input" accept="image/*" />
            </div>
          `);
        };

        reader.readAsDataURL(file);
      });
    });
  }

  function saveData() {
    localStorage.setItem('travelLocations', JSON.stringify(locations));
  }
</script>

</body>
</html>
