<!doctype html>
<html lang="ar">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>منطقة التوصيل</title>
  <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
  <style>
    html,body,#map { height:100%; margin:0; padding:0; direction: rtl; font-family: "Segoe UI", Tahoma, Arial, "Noto Sans Arabic", sans-serif;}
    .top-label {
      position: absolute;
      top: 12px;
      right: 12px;
      background: rgba(255,255,255,0.95);
      padding: 8px 12px;
      border-radius: 8px;
      box-shadow: 0 2px 6px rgba(0,0,0,0.2);
      z-index: 999;
      font-size: 14px;
      text-align: right;
    }
    .leaflet-control-container { display: none; }
  </style>
</head>
<body>
  <div class="top-label">منطقة التوصيل المجاني</div>
  <div id="map"></div>

  <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
  <script>
    // مركز المطعم
    const center = [15.4083286, 44.2226482]; 
    const radius = 3000; // نصف القطر بالمتر (٣ كم)

    const map = L.map('map', {
      center: center,
      zoom: 13,
      dragging: false,
      zoomControl: false,
      scrollWheelZoom: false,
      doubleClickZoom: false,
      boxZoom: false,
      keyboard: false,
      attributionControl: false
    });

    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
      maxZoom: 19
    }).addTo(map);

    // دائرة التوصيل
    const innerCircle = L.circle(center, {
      radius: radius,
      color: '#ff5722',
      weight: 3,
      fill: false
    }).addTo(map);

    // مركز الدائرة
    L.circleMarker(center, { radius:6, fillColor:'#ff5722', color:'#fff', weight:2, fillOpacity:1, interactive:false}).addTo(map);

    // ضبط التكبير
    map.fitBounds(innerCircle.getBounds(), { padding: [30,30] });

    // قناع خارج الدائرة
    function circleToPolygon(latlng, radiusMeters, points = 128) {
      const earthRadius = 6378137;
      const lat = latlng[0] * Math.PI/180;
      const lng = latlng[1] * Math.PI/180;
      const d = radiusMeters / earthRadius;
      const coords = [];
      for (let i = 0; i <= points; i++) {
        const bearing = i * 2 * Math.PI / points;
        const latI = Math.asin(Math.sin(lat)*Math.cos(d) + Math.cos(lat)*Math.sin(d)*Math.cos(bearing));
        const lngI = lng + Math.atan2(Math.sin(bearing)*Math.sin(d)*Math.cos(lat), Math.cos(d)-Math.sin(lat)*Math.sin(latI));
        coords.push([latI*180/Math.PI, lngI*180/Math.PI]);
      }
      return coords;
    }
    const hole = circleToPolygon(center, radius, 128);
    const outer = [[85,-180],[85,180],[-85,180],[-85,-180]];
    L.polygon([outer, hole], {
      color: '#000',
      fillColor: '#000',
      fillOpacity: 0.55,
      stroke: false,
      interactive: false
    }).addTo(map);
  </script>
</body>
</html>
