<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>CleanCity - Map Your City's Cleanliness</title>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet"/>
  <script src="https://maps.googleapis.com/maps/api/js?key=YOUR_GOOGLE_MAPS_API_KEY&libraries=places"></script>
  <style>
    :root {
      --primary: #4a6fa5;
      --success: #4caf50;
      --danger: #f44336;
      --light-gray: #f8f9fa;
      --dark-gray: #343a40;
      --border-radius: 12px;
      --shadow: 0 4px 12px rgba(0,0,0,0.1);
      --transition: all 0.3s ease;
    }

    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
    }

    body {
      font-family: 'Inter', sans-serif;
      background-color: var(--light-gray);
      color: var(--dark-gray);
      line-height: 1.6;
    }

    .container {
      max-width: 1200px;
      margin: 0 auto;
      padding: 1rem;
      height: 100vh;
      display: flex;
      flex-direction: column;
    }

    header {
      text-align: center;
      margin-bottom: 1.5rem;
      padding: 1rem;
      background-color: white;
      border-radius: var(--border-radius);
      box-shadow: var(--shadow);
    }

    header h1 {
      font-size: 1.8rem;
      color: var(--primary);
      margin-bottom: 0.5rem;
    }

    header p {
      color: #6c757d;
      font-size: 0.95rem;
    }

    .controls {
      display: flex;
      justify-content: center;
      gap: 1rem;
      margin-bottom: 1.5rem;
      flex-wrap: wrap;
    }

    .switch-container {
      display: flex;
      align-items: center;
      gap: 0.5rem;
      background-color: white;
      padding: 0.5rem 1rem;
      border-radius: 20px;
      box-shadow: var(--shadow);
    }

    .switch {
      position: relative;
      display: inline-block;
      width: 60px;
      height: 34px;
    }

    .switch input { display: none; }

    .slider {
      position: absolute;
      cursor: pointer;
      top: 0;
      left: 0;
      right: 0;
      bottom: 0;
      background-color: #ccc;
      transition: var(--transition);
      border-radius: 34px;
    }

    .slider:before {
      position: absolute;
      content: "";
      height: 26px;
      width: 26px;
      left: 4px;
      bottom: 4px;
      background-color: white;
      transition: var(--transition);
      border-radius: 50%;
    }

    input:checked + .slider {
      background-color: var(--success);
    }

    input:checked + .slider:before {
      transform: translateX(26px);
    }

    .switch-label {
      font-weight: 500;
      color: var(--dark-gray);
    }

    .view-label {
      font-size: 0.9rem;
      font-weight: 600;
      color: var(--primary);
    }

    .map-container {
      flex: 1;
      border-radius: var(--border-radius);
      overflow: hidden;
      box-shadow: var(--shadow);
      background-color: white;
      position: relative;
    }

    #map {
      width: 100%;
      height: 100%;
      border-radius: var(--border-radius);
    }

    .upload-btn {
      background-color: var(--primary);
      color: white;
      border: none;
      padding: 0.75rem 1.5rem;
      font-size: 1rem;
      font-weight: 600;
      border-radius: var(--border-radius);
      cursor: pointer;
      transition: var(--transition);
      display: flex;
      align-items: center;
      gap: 0.5rem;
      margin-top: 1rem;
    }

    .upload-btn:hover {
      background-color: #3a5a80;
      transform: translateY(-2px);
    }

    .modal {
      display: none;
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background-color: rgba(0,0,0,0.7);
      z-index: 1000;
      justify-content: center;
      align-items: center;
    }

    .modal-content {
      background-color: white;
      width: 90%;
      max-width: 500px;
      border-radius: var(--border-radius);
      overflow: hidden;
      box-shadow: 0 10px 30px rgba(0,0,0,0.3);
      max-height: 90vh;
      overflow-y: auto;
    }

    .modal-header {
      background-color: var(--primary);
      color: white;
      padding: 1rem;
      display: flex;
      justify-content: space-between;
      align-items: center;
    }

    .modal-header h2 {
      font-size: 1.2rem;
    }

    .close-btn {
      background: none;
      border: none;
      font-size: 1.5rem;
      color: white;
      cursor: pointer;
      font-weight: bold;
    }

    .modal-body {
      padding: 1.5rem;
    }

    .form-group {
      margin-bottom: 1rem;
    }

    label {
      display: block;
      margin-bottom: 0.5rem;
      font-weight: 500;
      color: var(--dark-gray);
    }

    input, textarea, select {
      width: 100%;
      padding: 0.75rem;
      border: 1px solid #ddd;
      border-radius: var(--border-radius);
      font-size: 1rem;
    }

    textarea {
      height: 100px;
      resize: vertical;
    }

    .btn-group {
      display: flex;
      gap: 0.75rem;
      margin-top: 1rem;
    }

    .btn {
      padding: 0.75rem 1.25rem;
      border: none;
      border-radius: var(--border-radius);
      font-size: 1rem;
      font-weight: 500;
      cursor: pointer;
      transition: var(--transition);
    }

    .btn-primary {
      background-color: var(--primary);
      color: white;
    }

    .btn-secondary {
      background-color: #6c757d;
      color: white;
    }

    .btn:hover {
      transform: translateY(-2px);
    }

    .info-card {
      background-color: white;
      border-radius: var(--border-radius);
      padding: 1.25rem;
      margin-top: 1rem;
      box-shadow: var(--shadow);
      border: 1px solid #eee;
    }

    .info-card h3 {
      margin-bottom: 0.75rem;
      color: var(--primary);
      font-size: 1.1rem;
    }

    .info-card p {
      margin-bottom: 0.5rem;
      color: #555;
    }

    .votes {
      display: flex;
      align-items: center;
      gap: 0.5rem;
      margin-top: 0.75rem;
      font-size: 0.9rem;
      color: #6c757d;
    }

    .vote-btn {
      background-color: #f0f0f0;
      border: 1px solid #ddd;
      padding: 0.25rem 0.5rem;
      border-radius: 6px;
      font-size: 0.9rem;
      cursor: pointer;
    }

    .vote-btn:hover {
      background-color: #e0e0e0;
    }

    .report-btn {
      background-color: #f8d7da;
      color: #721c24;
      border: 1px solid #f5c6cb;
      padding: 0.25rem 0.5rem;
      border-radius: 6px;
      font-size: 0.9rem;
      cursor: pointer;
    }

    .report-btn:hover {
      background-color: #f1aeb5;
    }

    .image-preview {
      width: 100%;
      height: 200px;
      background-color: #f0f0f0;
      border-radius: var(--border-radius);
      margin-top: 0.75rem;
      display: flex;
      align-items: center;
      justify-content: center;
      overflow: hidden;
    }

    .image-preview img {
      max-width: 100%;
      max-height: 100%;
      object-fit: cover;
    }

    .map-marker {
      width: 20px;
      height: 20px;
      border-radius: 50%;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 0.75rem;
      font-weight: bold;
      color: white;
      box-shadow: 0 0 5px rgba(0,0,0,0.3);
    }

    .marker-dirty {
      background-color: var(--danger);
      border: 2px solid white;
    }

    .marker-clean {
      background-color: var(--success);
      border: 2px solid white;
    }

    .login-section {
      text-align: center;
      margin-top: 1.5rem;
      padding: 1rem;
      background-color: white;
      border-radius: var(--border-radius);
      box-shadow: var(--shadow);
    }

    .login-btn {
      background-color: #db4437;
      color: white;
      border: none;
      padding: 0.75rem 1.5rem;
      font-size: 1rem;
      font-weight: 600;
      border-radius: var(--border-radius);
      cursor: pointer;
      display: flex;
      align-items: center;
      justify-content: center;
      gap: 0.5rem;
      margin-top: 0.5rem;
    }

    .login-btn:hover {
      background-color: #c1352b;
    }

    @media (max-width: 768px) {
      .container {
        padding: 0.5rem;
      }

      header h1 {
        font-size: 1.5rem;
      }

      .controls {
        flex-direction: column;
        align-items: center;
      }

      .switch-container {
        width: 100%;
        justify-content: center;
      }

      .modal-body {
        padding: 1rem;
      }

      .btn-group {
        flex-direction: column;
      }

      .upload-btn {
        width: 100%;
        justify-content: center;
      }
    }
  </style>
</head>
<body>
  <div class="container">
    <header>
      <h1>🌍 CleanCity</h1>
      <p>Help us make your city cleaner. Upload photos of dirty or clean spots!</p>
    </header>

    <div class="controls">
      <div class="switch-container">
        <span class="switch-label">Dirty Places</span>
        <label class="switch">
          <input type="checkbox" id="view-toggle" />
          <span class="slider"></span>
        </label>
        <span class="switch-label">Clean Places</span>
      </div>
    </div>

    <div class="map-container">
      <div id="map"></div>
    </div>

    <button class="upload-btn" id="upload-btn">
      📸 Upload Photo
    </button>

    <div class="login-section">
      <p>Sign in to upload and vote</p>
      <button class="login-btn">
        <span>📱</span> Sign in with Google
      </button>
    </div>
  </div>

  <!-- Upload Modal -->
  <div class="modal" id="upload-modal">
    <div class="modal-content">
      <div class="modal-header">
        <h2>Upload a Photo</h2>
        <button class="close-btn" id="close-modal">&times;</button>
      </div>
      <div class="modal-body">
        <form id="upload-form">
          <div class="form-group">
            <label for="place-type">Category</label>
            <select id="place-type" required>
              <option value="dirty">🗑️ Dirty / Polluted Place</option>
              <option value="clean">🌳 Clean / Good Place</option>
            </select>
          </div>

          <div class="form-group">
            <label for="location-name">Location Name</label>
            <input type="text" id="location-name" placeholder="e.g., Central Park, Main Street" required />
          </div>

          <div class="form-group">
            <label for="photo">Photo</label>
            <input type="file" id="photo" accept="image/*" required />
            <div class="image-preview" id="image-preview">
              <span>📷 Preview</span>
            </div>
          </div>

          <div class="form-group">
            <label for="comment">Comment (optional)</label>
            <textarea id="comment" placeholder="Describe the place..."></textarea>
          </div>

          <div class="form-group">
            <label for="location-lat">Location (Auto-detected)</label>
            <input type="text" id="location-lat" placeholder="Latitude" readonly />
            <input type="text" id="location-lng" placeholder="Longitude" readonly />
          </div>

          <div class="btn-group">
            <button type="button" class="btn btn-secondary" id="cancel-upload">Cancel</button>
            <button type="submit" class="btn btn-primary">Upload</button>
          </div>
        </form>
      </div>
    </div>
  </div>

  <!-- Info Modal -->
  <div class="modal" id="info-modal">
    <div class="modal-content">
      <div class="modal-header">
        <h2 id="modal-title">Location Info</h2>
        <button class="close-btn" id="close-info-modal">&times;</button>
      </div>
      <div class="modal-body">
        <div class="info-card">
          <h3 id="info-name">Location Name</h3>
          <p><strong>Date:</strong> <span id="info-date">-</span></p>
          <p><strong>Category:</strong> <span id="info-category">-</span></p>
          <p><strong>Comment:</strong> <span id="info-comment">-</span></p>
          <div class="image-preview">
            <img id="info-image" src="" alt="Location photo" />
          </div>
          <div class="votes">
            <span>👍 Upvotes: <span id="info-votes">0</span></span>
            <button class="vote-btn" id="vote-btn">Vote Up</button>
            <button class="report-btn" id="report-btn">Report</button>
          </div>
        </div>
      </div>
    </div>
  </div>

  <script>
    // Initialize Google Maps
    let map;
    let markers = [];
    let userLocation = null;
    let selectedLocation = null;

    // Mock data for demo
    const mockLocations = [
      {
        id: 1,
        type: 'dirty',
        name: 'Central Park Trash Zone',
        lat: 40.785091,
        lng: -73.968285,
        image: 'https://via.placeholder.com/400x300/ff6b6b/ffffff?text=Dirty+Park',
        comment: 'Litter everywhere, especially near the playground.',
        date: '2025-03-15',
        votes: 12
      },
      {
        id: 2,
        type: 'clean',
        name: 'Greenway Trail',
        lat: 40.781732,
        lng: -73.957092,
        image: 'https://via.placeholder.com/400x300/4caf50/ffffff?text=Clean+Trail',
        comment: 'Beautiful path with clean benches and flowers.',
        date: '2025-02-28',
        votes: 45
      },
      {
        id: 3,
        type: 'dirty',
        name: 'Main Street Intersection',
        lat: 40.779059,
        lng: -73.968259,
        image: 'https://via.placeholder.com/400x300/ff6b6b/ffffff?text=Busy+Street+Litter',
        comment: 'Bottles and food wrappers on the sidewalk.',
        date: '2025-02-10',
        votes: 8
      },
      {
        id: 4,
        type: 'clean',
        name: 'Riverside Park',
        lat: 40.782848,
        lng: -73.954075,
        image: 'https://via.placeholder.com/400x300/4caf50/ffffff?text=Riverside+Park',
        comment: 'Well-maintained park with clean restrooms.',
        date: '2025-01-20',
        votes: 67
      }
    ];

    // Initialize map
    function initMap() {
      map = new google.maps.Map(document.getElementById("map"), {
        zoom: 14,
        center: { lat: 40.785091, lng: -73.968285 },
        mapTypeId: 'roadmap',
        mapTypeControl: false,
        streetViewControl: false,
        zoomControl: true,
        fullscreenControl: false
      });

      // Add mock markers
      renderMarkers();

      // Set up click listener for map
      map.addListener('click', function(event) {
        selectedLocation = {
          lat: event.latLng.lat(),
          lng: event.latLng.lng()
        };
        document.getElementById('location-lat').value = selectedLocation.lat.toFixed(6);
        document.getElementById('location-lng').value = selectedLocation.lng.toFixed(6);
      });

      // Get user location
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(
          (position) => {
            userLocation = {
              lat: position.coords.latitude,
              lng: position.coords.longitude
            };
            map.setCenter(userLocation);
            map.setZoom(15);
          },
          () => {
            console.log('Could not get user location');
          }
        );
      }
    }

    // Render markers on map
    function renderMarkers() {
      // Clear existing markers
      markers.forEach(marker => marker.setMap(null));
      markers = [];

      const viewToggle = document.getElementById('view-toggle').checked;
      const filteredLocations = viewToggle 
        ? mockLocations.filter(loc => loc.type === 'dirty')
        : mockLocations.filter(loc => loc.type === 'clean');

      filteredLocations.forEach(loc => {
        const marker = new google.maps.Marker({
          position: { lat: loc.lat, lng: loc.lng },
          map: map,
          icon: {
            path: google.maps.SymbolPath.CIRCLE,
            scale: 12,
            fillColor: loc.type === 'dirty' ? '#f44336' : '#4caf50',
            fillOpacity: 1,
            strokeColor: 'white',
            strokeWeight: 3
          },
          animation: google.maps.Animation.DROP
        });

        // Add click listener
        marker.addListener('click', () => {
          showLocationInfo(loc);
        });

        markers.push(marker);
      });
    }

    // Show location info modal
    function showLocationInfo(location) {
      const modal = document.getElementById('info-modal');
      const title = document.getElementById('modal-title');
      const name = document.getElementById('info-name');
      const date = document.getElementById('info-date');
      const category = document.getElementById('info-category');
      const comment = document.getElementById('info-comment');
      const image = document.getElementById('info-image');
      const votes = document.getElementById('info-votes');

      title.textContent = location.name;
      name.textContent = location.name;
      date.textContent = location.date;
      category.textContent = location.type === 'dirty' ? 'Dirty / Polluted Place' : 'Clean / Good Place';
      comment.textContent = location.comment || 'No comment provided.';
      image.src = location.image;
      votes.textContent = location.votes;

      modal.style.display = 'flex';
    }

    // Close modals
    document.getElementById('close-modal').addEventListener('click', () => {
      document.getElementById('upload-modal').style.display = 'none';
    });

    document.getElementById('close-info-modal').addEventListener('click', () => {
      document.getElementById('info-modal').style.display = 'none';
    });

    // Upload form submission
    document.getElementById('upload-form').addEventListener('submit', function(e) {
      e.preventDefault();
      const type = document.getElementById('place-type').value;
      const name = document.getElementById('location-name').value;
      const lat = document.getElementById('location-lat').value;
      const lng = document.getElementById('location-lng').value;
      const comment = document.getElementById('comment').value;
      const file = document.getElementById('photo').files[0];

      if (!file) return;

      // Create new location
      const newLocation = {
        id: mockLocations.length + 1,
        type,
        name,
        lat: parseFloat(lat),
        lng: parseFloat(lng),
        image: URL.createObjectURL(file),
        comment,
        date: new Date().toISOString().split('T')[0],
        votes: 0
      };

      mockLocations.push(newLocation);
      renderMarkers();

      // Close modal
      document.getElementById('upload-modal').style.display = 'none';
      alert('Photo uploaded successfully!');

      // Reset form
      document.getElementById('upload-form').reset();
      document.getElementById('image-preview').innerHTML = '<span>📷 Preview</span>';
    });

    // Cancel upload
    document.getElementById('cancel-upload').addEventListener('click', () => {
      document.getElementById('upload-modal').style.display = 'none';
    });

    // Image preview
    document.getElementById('photo').addEventListener('change', function(e) {
      const file = e.target.files[0];
      if (file) {
        const reader = new FileReader();
        reader.onload = function(event) {
          const preview = document.getElementById('image-preview');
          preview.innerHTML = '';
          const img = document.createElement('img');
          img.src = event.target.result;
          preview.appendChild(img);
        };
        reader.readAsDataURL(file);
      }
    });

    // View toggle
    document.getElementById('view-toggle').addEventListener('change', function() {
      renderMarkers();
    });

    // Upload button
    document.getElementById('upload-btn').addEventListener('click', function() {
      document.getElementById('upload-modal').style.display = 'flex';
      // Set default location if available
      if (userLocation) {
        document.getElementById('location-lat').value = userLocation.lat.toFixed(6);
        document.getElementById('location-lng').value = userLocation.lng.toFixed(6);
      }
    });

    // Vote button
    document.getElementById('vote-btn').addEventListener('click', function() {
      const votesEl = document.getElementById('info-votes');
      let currentVotes = parseInt(votesEl.textContent);
      votesEl.textContent = (currentVotes + 1).toString();
      alert('Thank you for voting!');
    });

    // Report button
    document.getElementById('report-btn').addEventListener('click', function() {
      alert('This location has been reported. Thank you for helping us keep the city clean!');
    });

    // Initialize on load
    window.onload = function() {
      initMap();
    };
  </script>
</body>
</html>
