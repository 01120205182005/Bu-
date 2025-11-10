<!doctype html>
<html lang="pt">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Partilhar localização</title>
</head>
<body>
  <h2>Partilhe a sua localização</h2>
  <p>Clique no botão e permita que o navegador aceda à sua localização. Só será enviada se você aceitar.</p>
  <button id="getLoc">Partilhar localização</button>
  <pre id="status"></pre>

<script>
const status = document.getElementById('status');

document.getElementById('getLoc').addEventListener('click', () => {
  if (!navigator.geolocation) {
    status.textContent = 'Geolocalização não suportada pelo navegador.';
    return;
  }

  status.textContent = 'A pedir permissão...';

  navigator.geolocation.getCurrentPosition(async (pos) => {
    const coords = {
      latitude: pos.coords.latitude,
      longitude: pos.coords.longitude,
      accuracy: pos.coords.accuracy,
      timestamp: pos.timestamp
    };

    status.textContent = 'Local obtido: ' + JSON.stringify(coords, null, 2);

    // Enviar para o seu servidor/webhook (substitua pela sua URL HTTPS)
    try {
      const resp = await fetch('https://seu-servidor.exemplo/webhook-localizacao', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(coords)
      });
      if (resp.ok) {
        status.textContent += '\nEnviado com sucesso ao servidor.';
      } else {
        status.textContent += '\nErro ao enviar ao servidor: ' + resp.status;
      }
    } catch (err) {
      status.textContent += '\nFalha na rede: ' + err;
    }

  }, (err) => {
    status.textContent = 'Permissão negada ou erro: ' + err.message;
  }, {
    enableHighAccuracy: true,
    timeout: 15000
  });
});
</script>
</body>
</html>
