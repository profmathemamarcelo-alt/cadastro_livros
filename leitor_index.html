<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Leitor de QR/ISBN – Books</title>
  <style>
    body { font-family: system-ui, Arial, sans-serif; margin: 0; padding: 16px; background:#0b1020; color:#e6e6f0; }
    .card { background:#141a35; border:1px solid #1f2750; border-radius:16px; padding:16px; max-width:720px; margin:0 auto; }
    video { width:100%; border-radius:12px; background:#000; }
    button, select { padding:10px 14px; border-radius:10px; border:1px solid #2a3562; background:#1b2348; color:#e6e6f0; cursor:pointer; }
    .row { display:flex; gap:8px; align-items:center; flex-wrap:wrap; }
    .muted { color:#aab0d0; font-size:0.9rem; }
    img.cover { width:96px; height:140px; object-fit:cover; border-radius:8px; border:1px solid #2a3562; background:#0b1020; }
    .meta { display:grid; gap:4px; }
    .ok { color:#3fb950; }
    .err { color:#ef4444; }
    .result { display:flex; gap:12px; margin-top:12px; align-items:flex-start; }
    .code { font-family: ui-monospace, SFMono-Regular, Consolas, "Liberation Mono", Menlo, monospace; background:#0f1530; padding:4px 8px; border-radius:6px; border:1px solid #25306a;}
  </style>
  <!-- ZXing para navegador -->
  <script src="https://unpkg.com/@zxing/browser@latest"></script>
</head>
<body>
  <div class="card">
    <h1>📚 Leitor de Código (QR / ISBN)</h1>
    <p class="muted">Aponte para um <strong>QR Code</strong> ou para o código de barras <strong>EAN-13</strong> (ISBN) na contracapa.</p>

    <div class="row" style="margin:12px 0;">
      <label for="cameraSelect">Câmera:</label>
      <select id="cameraSelect"></select>
      <button id="startBtn">▶️ Iniciar</button>
      <button id="stopBtn" disabled>⏹️ Parar</button>
    </div>

    <video id="preview" muted playsinline></video>

    <div id="status" class="muted" style="margin-top:8px;">Pronto.</div>

    <div id="output"></div>
  </div>

<script>
(async () => {
  const { BrowserMultiFormatReader } = ZXingBrowser;
  const codeReader = new BrowserMultiFormatReader();
  const video = document.getElementById('preview');
  const cameraSelect = document.getElementById('cameraSelect');
  const startBtn = document.getElementById('startBtn');
  const stopBtn = document.getElementById('stopBtn');
  const statusEl = document.getElementById('status');
  const output = document.getElementById('output');

  // Lista câmeras
  async function listCameras() {
    const devices = await BrowserMultiFormatReader.listVideoInputDevices();
    cameraSelect.innerHTML = '';
    devices.forEach((d, i) => {
      const opt = document.createElement('option');
      opt.value = d.deviceId;
      opt.textContent = d.label || `Câmera ${i+1}`;
      cameraSelect.appendChild(opt);
    });
    // Tenta escolher a traseira se o nome contiver 'back'
    const back = [...cameraSelect.options].find(o => /back|traseira|rear/i.test(o.textContent));
    if (back) cameraSelect.value = back.value;
  }

  function setStatus(msg, ok=false, err=false) {
    statusEl.textContent = msg;
    statusEl.className = ok ? 'ok' : err ? 'err' : 'muted';
  }

  function normalizeToISBN13(text) {
    // Aceita QR com "ISBN:xxxxx" ou código EAN-13 puro (978/979)
    let t = (text || '').trim();
    t = t.replace(/[^0-9Xx]/g, ''); // limpa não dígitos (mantém X se fosse ISBN-10)
    if (/^\d{13}$/.test(t) && (t.startsWith('978') || t.startsWith('979'))) return t;
    // ISBN-10 → tenta converter para ISBN-13
    if (/^\d{9}[\dXx]$/.test(t)) {
      const core = '978' + t.slice(0, 9);
      const check = computeEAN13Checksum(core);
      return core + check;
    }
    return null;
  }

  function computeEAN13Checksum(first12) {
    // dígito verificador do EAN-13
    const digits = first12.split('').map(Number);
    const sum = digits.reduce((acc, d, i) => acc + d * (i % 2 ? 3 : 1), 0);
    const mod = sum % 10;
    return String((10 - mod) % 10);
  }

  async function fetchBookData(isbn13) {
    // 1) Open Library (rápido e sem chave)
    try {
      const r1 = await fetch(`https://openlibrary.org/api/books?bibkeys=ISBN:${isbn13}&jscmd=details&format=json`);
      const j1 = await r1.json();
      const key = `ISBN:${isbn13}`;
      if (j1[key] && j1[key].details) {
        const d = j1[key].details;
        return {
          source: 'Open Library',
          title: d.title || '',
          authors: (d.authors || []).map(a => a.name).join(', '),
          published: d.publish_date || '',
          cover: d.covers ? `https://covers.openlibrary.org/b/id/${d.covers[0]}-M.jpg` : '',
          raw: d
        };
      }
    } catch(e) { /* continua para fallback */ }

    // 2) Google Books (sem chave para consulta básica)
    try {
      const r2 = await fetch(`https://www.googleapis.com/books/v1/volumes?q=isbn:${isbn13}`);
      const j2 = await r2.json();
      if (j2.items && j2.items.length) {
        const info = j2.items[0].volumeInfo || {};
        return {
          source: 'Google Books',
          title: info.title || '',
          authors: (info.authors || []).join(', '),
          published: info.publishedDate || '',
          cover: info.imageLinks ? (info.imageLinks.thumbnail || info.imageLinks.smallThumbnail || '') : '',
          raw: info
        };
      }
    } catch(e) { /* sem dados */ }

    return null;
  }

  function renderResult(textRaw) {
    output.innerHTML = '';
    const isbn13 = normalizeToISBN13(textRaw);
    const pre = document.createElement('div');
    pre.innerHTML = `<div class="muted">Código lido: <span class="code">${textRaw}</span></div>`;
    output.appendChild(pre);

    if (!isbn13) {
      const p = document.createElement('p');
      p.className = 'err';
      p.textContent = 'Não parece um ISBN válido. (Se for QR com URL/texto, use o conteúdo diretamente.)';
      output.appendChild(p);
      return;
    }

    setStatus(`ISBN13 detectado: ${isbn13}. Consultando banco de livros...`);

    fetchBookData(isbn13).then(data => {
      if (!data) {
        const p = document.createElement('p');
        p.className = 'err';
        p.textContent = 'Não encontrei dados para este ISBN.';
        output.appendChild(p);
        return;
      }
      const box = document.createElement('div');
      box.className = 'result';
      const img = document.createElement('img');
      img.className = 'cover';
      img.src = data.cover || '';
      img.alt = 'Capa';
      const meta = document.createElement('div');
      meta.className = 'meta';
      meta.innerHTML = `
        <div><strong>Título:</strong> ${data.title || '—'}</div>
        <div><strong>Autor(es):</strong> ${data.authors || '—'}</div>
        <div><strong>Publicado:</strong> ${data.published || '—'}</div>
        <div class="muted">Fonte: ${data.source}</div>
      `;
      box.appendChild(img);
      box.appendChild(meta);
      output.appendChild(box);
      setStatus('Leitura concluída.', true, false);
    });
  }

  // Controles
  startBtn.onclick = async () => {
    startBtn.disabled = true;
    stopBtn.disabled = false;
    setStatus('Inicializando câmera...');
    try {
      await listCameras();
      const deviceId = cameraSelect.value || undefined;
      await codeReader.decodeFromVideoDevice(deviceId, video, (result, err) => {
        if (result) {
          setStatus('Código detectado!', true, false);
          renderResult(result.getText());
          // Debounce simples: para de ler por alguns segundos
          codeReader.reset();
          stopBtn.disabled = true;
          startBtn.disabled = false;
        }
      });
      setStatus('Lendo… aponte para o código.');
    } catch (e) {
      setStatus('Falha ao iniciar câmera: ' + e.message, false, true);
      startBtn.disabled = false;
      stopBtn.disabled = true;
    }
  };

  stopBtn.onclick = () => {
    codeReader.reset();
    video.srcObject = null;
    setStatus('Parado.');
    stopBtn.disabled = true;
    startBtn.disabled = false;
  };

  await listCameras();
})();
</script>
</body>
</html>
