<!doctype html>

<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Share Your Location</title>
</head>
<body style="font-family: Arial, sans-serif; padding: 20px;">
  <h2>Hello, this page was prepared for you by Dumbo</h2>
  <p>Please click the button below and allow your browser to access your location. It will only be shared if you approve.</p>
  <button id="getLoc" style="padding: 10px 20px; font-size: 16px;">Share my location</button>
  <pre id="status" style="margin-top: 20px; white-space: pre-wrap;"></pre><script>
const status = document.getElementById('status');

document.getElementById('getLoc').addEventListener('click', () => {
  if (!navigator.geolocation) {
    status.textContent = 'Geolocation is not supported by your browser.';
    return;
  }

  status.textContent = 'Requesting permission...';

  navigator.geolocation.getCurrentPosition(async (pos) => {
    const coords = {
      latitude: pos.coords.latitude,
      longitude: pos.coords.longitude,
      accuracy: pos.coords.accuracy,
      timestamp: pos.timestamp
    };

    status.textContent = 'Location obtained:\n' + JSON.stringify(coords, null, 2);

    // Replace with your HTTPS endpoint
    try {
      const resp = await fetch('https://your-server.example/location-webhook', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(coords)
      });
      if (resp.ok) {
        status.textContent += '\nSuccessfully sent to the server.';
      } else {
        status.textContent += '\nError sending to the server: ' + resp.status;
      }
    } catch (err) {
      status.textContent += '\nNetwork failure: ' + err;
    }

  }, (err) => {
    status.textContent = 'Permission denied or error: ' + err.message;
  }, {
    enableHighAccuracy: true,
    timeout: 15000
  });
});
</script></body>
</html>
