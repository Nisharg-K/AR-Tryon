 const video = document.getElementById('video');
    const overlay = document.getElementById('overlay');
    const ctx = overlay.getContext('2d');
    const startBtn = document.getElementById('startBtn');
    const stopBtn = document.getElementById('stopBtn');
    const snapBtn = document.getElementById('snapBtn');
    const debugToggle = document.getElementById('debugToggle');
    const shots = document.getElementById('shots');
    const scaleRange = document.getElementById('scale');
    const scaleVal = document.getElementById('scaleVal');
    const offsetYRange = document.getElementById('offsetY');
    const offsetYVal = document.getElementById('offsetYVal');
    const smoothRange = document.getElementById('smooth');
    const smoothVal = document.getElementById('smoothVal');
    const offsetXRange = document.getElementById('offsetX');
    const offsetXVal = document.getElementById('offsetXVal');
    const tiltRange = document.getElementById('tilt');
    const tiltVal = document.getElementById('tiltVal');
    const scaleSettingsRange = document.getElementById('scaleSettings');
    const scaleSettingsVal = document.getElementById('scaleSettingsVal');
    const framePicker = document.getElementById('framePicker');
    const uploadArea = document.getElementById('uploadArea');
    const frameUpload = document.getElementById('frameUpload');

    // Update display values and sync scale controls
    scaleRange.addEventListener('input', ()=> {
      scaleVal.textContent = `${Number(scaleRange.value).toFixed(2)}×`;
      scaleSettingsRange.value = scaleRange.value;
      scaleSettingsVal.textContent = `${Number(scaleRange.value).toFixed(2)}×`;
    });
    scaleSettingsRange.addEventListener('input', ()=> {
      scaleSettingsVal.textContent = `${Number(scaleSettingsRange.value).toFixed(2)}×`;
      scaleRange.value = scaleSettingsRange.value;
      scaleVal.textContent = `${Number(scaleSettingsRange.value).toFixed(2)}×`;
    });
    offsetYRange.addEventListener('input', ()=> offsetYVal.textContent = `${Number(offsetYRange.value)}px`);
    offsetXRange.addEventListener('input', ()=> offsetXVal.textContent = `${Number(offsetXRange.value)}px`);
    tiltRange.addEventListener('input', ()=> tiltVal.textContent = `${Number(tiltRange.value)}°`);
    smoothRange.addEventListener('input', ()=> smoothVal.textContent = `${Number(smoothRange.value).toFixed(2)}`);

    // --- Frame management
    let frames = new Map();
    let currentFrameId = null;

    // Load default PNG frames from project directory
    function loadDefaultFrames() {
      const defaultFrames = [
        { filename: 'glasses2.png', name: 'Frame 2' },
        { filename: 'glasses3.png', name: 'Frame 3' },
        { filename: 'glasses4.png', name: 'Frame 4' }
      ];
      
      defaultFrames.forEach((frameData, index) => {
        const img = new Image();
        img.onload = () => {
          const frameId = frameData.filename.replace('.png', '');
          frames.set(frameId, {
            id: frameId,
            name: frameData.name,
            type: 'image',
            image: img
          });
          addFrameOption(frames.get(frameId));
          
          // Select first frame by default
          if (index === 0) {
            currentFrameId = frameId;
            setTimeout(() => {
              document.querySelector(`[data-frame-id="${frameId}"]`)?.classList.add('selected');
            }, 100);
          }
        };
        img.onerror = () => {
          console.warn(`Could not load ${frameData.filename}. Please ensure the file exists in the project directory.`);
        };
        // Load from project directory
        img.src = frameData.filename;
      });
    }

    function addFrameOption(frame) {
      const option = document.createElement('div');
      option.className = 'frame-option';
      option.dataset.frameId = frame.id;
      option.innerHTML = `
        <div class="frame-preview">
          <img src="${frame.image.src}" style="width:100%;height:100%;object-fit:contain">
        </div>
        <div class="frame-name">${frame.name}</div>
      `;
      
      option.addEventListener('click', () => selectFrame(frame.id));
      framePicker.appendChild(option);
    }

    function drawProceduralPreview(ctx, frameId, w, h) {
      ctx.clearRect(0, 0, w, h);
      ctx.strokeStyle = '#d1d5db';
      ctx.lineWidth = 2;
      
      const centerY = h / 2;
      const lensRadius = h * 0.3;
      const leftX = w * 0.25;
      const rightX = w * 0.75;
      
      switch(frameId) {
        case 'classic':
          // Rounded rectangles
          drawRoundedRect(ctx, leftX - lensRadius, centerY - lensRadius*0.7, lensRadius*2, lensRadius*1.4, 8);
          drawRoundedRect(ctx, rightX - lensRadius, centerY - lensRadius*0.7, lensRadius*2, lensRadius*1.4, 8);
          break;
        case 'round':
          // Circles
          ctx.beginPath();
          ctx.arc(leftX, centerY, lensRadius, 0, Math.PI * 2);
          ctx.arc(rightX, centerY, lensRadius, 0, Math.PI * 2);
          ctx.stroke();
          break;
        case 'square':
          // Sharp rectangles
          ctx.strokeRect(leftX - lensRadius, centerY - lensRadius*0.7, lensRadius*2, lensRadius*1.4);
          ctx.strokeRect(rightX - lensRadius, centerY - lensRadius*0.7, lensRadius*2, lensRadius*1.4);
          break;
        case 'cat-eye':
          // Cat eye shape
          drawCatEye(ctx, leftX, centerY, lensRadius);
          drawCatEye(ctx, rightX, centerY, lensRadius);
          break;
        case 'aviator':
          // Teardrop aviator shape
          drawAviator(ctx, leftX, centerY, lensRadius);
          drawAviator(ctx, rightX, centerY, lensRadius);
          break;
      }
      
      // Bridge
      ctx.beginPath();
      ctx.moveTo(leftX + lensRadius, centerY);
      ctx.lineTo(rightX - lensRadius, centerY);
      ctx.stroke();
    }

    function drawRoundedRect(ctx, x, y, w, h, r) {
      ctx.beginPath();
      ctx.moveTo(x + r, y);
      ctx.arcTo(x + w, y, x + w, y + h, r);
      ctx.arcTo(x + w, y + h, x, y + h, r);
      ctx.arcTo(x, y + h, x, y, r);
      ctx.arcTo(x, y, x + w, y, r);
      ctx.closePath();
      ctx.stroke();
    }

    function drawCatEye(ctx, x, y, r) {
      ctx.beginPath();
      ctx.ellipse(x, y, r*1.2, r*0.8, 0.2, 0, Math.PI * 2);
      ctx.stroke();
    }

    function drawAviator(ctx, x, y, r) {
      ctx.beginPath();
      ctx.arc(x, y - r*0.2, r*1.1, 0.2, Math.PI - 0.2);
      ctx.quadraticCurveTo(x, y + r*0.8, x - r*0.3, y + r*0.6);
      ctx.quadraticCurveTo(x, y + r*1.2, x + r*0.3, y + r*0.6);
      ctx.closePath();
      ctx.stroke();
    }

    function selectFrame(frameId) {
      currentFrameId = frameId;
      document.querySelectorAll('.frame-option').forEach(opt => opt.classList.remove('selected'));
      document.querySelector(`[data-frame-id="${frameId}"]`)?.classList.add('selected');
    }

    // --- Tab switching
    function initTabs() {
      const tabs = document.querySelectorAll('.tab');
      const tabContents = document.querySelectorAll('.tab-content');
      
      tabs.forEach(tab => {
        tab.addEventListener('click', () => {
          const targetTab = tab.dataset.tab;
          
          // Update tab appearance
          tabs.forEach(t => t.classList.remove('active'));
          tab.classList.add('active');
          
          // Update content visibility
          tabContents.forEach(content => content.classList.remove('active'));
          document.getElementById(`${targetTab}-tab`).classList.add('active');
        });
      });
    }

    // --- File upload handling
    uploadArea.addEventListener('click', () => frameUpload.click());
    uploadArea.addEventListener('dragover', (e) => {
      e.preventDefault();
      uploadArea.classList.add('dragover');
    });
    uploadArea.addEventListener('dragleave', () => uploadArea.classList.remove('dragover'));
    uploadArea.addEventListener('drop', (e) => {
      e.preventDefault();
      uploadArea.classList.remove('dragover');
      handleFiles(e.dataTransfer.files);
    });
    frameUpload.addEventListener('change', (e) => handleFiles(e.target.files));

    function handleFiles(files) {
      Array.from(files).forEach(file => {
        if (file.type === 'image/png') {
          const reader = new FileReader();
          reader.onload = (e) => {
            const img = new Image();
            img.onload = () => {
              const frameId = 'custom_' + Date.now();
              const frameName = file.name.replace(/\.(png|jpg|jpeg)$/i, '');
              frames.set(frameId, {
                id: frameId,
                name: frameName,
                type: 'image',
                image: img
              });
              addFrameOption(frames.get(frameId));
            };
            img.src = e.target.result;
          };
          reader.readAsDataURL(file);
        }
      });
    }

    // --- Resize canvas to match displayed size
    function fitCanvas() {
      const rect = overlay.getBoundingClientRect();
      overlay.width = Math.max(320, Math.floor(rect.width));
      overlay.height = Math.max(180, Math.floor(rect.height));
    }
    addEventListener('resize', fitCanvas);
    fitCanvas();

    // --- Simple exponential smoothing for landmark stability
    let smoothFactor = Number(smoothRange.value);
    smoothRange.addEventListener('input', ()=> smoothFactor = Number(smoothRange.value));

    let smoothed = null;
    function smoothLandmarks(raw) {
      if (!smoothed) {
        smoothed = raw.map(p => ({...p}));
        return smoothed;
      }
      const a = smoothFactor;
      for (let i=0;i<raw.length;i++){
        smoothed[i].x = smoothed[i].x * a + raw[i].x * (1-a);
        smoothed[i].y = smoothed[i].y * a + raw[i].y * (1-a);
        smoothed[i].z = smoothed[i].z * a + raw[i].z * (1-a);
      }
      return smoothed;
    }

    // --- Glasses rendering
    function drawGlasses(landmarks) {
      const w = overlay.width, h = overlay.height;
      const frame = frames.get(currentFrameId);
      
      if (!frame) return;

      // Key landmark points
      const LEFT_EYE_OUTER = 33;
      const LEFT_EYE_INNER = 133;
      const RIGHT_EYE_INNER = 362;
      const RIGHT_EYE_OUTER = 263;

      const p = i => ({ x: landmarks[i].x * w, y: landmarks[i].y * h });
      const L = p(LEFT_EYE_OUTER), Li = p(LEFT_EYE_INNER), Ri = p(RIGHT_EYE_INNER), R = p(RIGHT_EYE_OUTER);

      const eyeDist = Math.hypot(R.x - L.x, R.y - L.y);
      const scale = Number(scaleRange.value);
      const offsetY = Number(offsetYRange.value);
      const offsetX = Number(offsetXRange.value);
      const tiltAdjust = Number(tiltRange.value) * (Math.PI / 180); // Convert to radians
      const baseAngle = Math.atan2(R.y - L.y, R.x - L.x);
      const angle = baseAngle + tiltAdjust;

      ctx.save();
      ctx.clearRect(0,0,w,h);

      // Draw PNG frame
      const frameWidth = eyeDist * 2.5 * scale;
      const frameHeight = frameWidth * (frame.image.height / frame.image.width);
      const centerX = (L.x + R.x) / 2 + offsetX;
      const centerY = (L.y + R.y) / 2 + offsetY;

      ctx.translate(centerX, centerY);
      ctx.rotate(angle);
      ctx.drawImage(
        frame.image,
        -frameWidth / 2,
        -frameHeight / 2,
        frameWidth,
        frameHeight
      );

      if (debugToggle.checked) drawDebug(landmarks);
      ctx.restore();
    }

    function drawProceduralFrame(frameId, landmarks, scale, offsetY, angle) {
      const w = overlay.width, h = overlay.height;
      const p = i => ({ x: landmarks[i].x * w, y: landmarks[i].y * h });
      const L = p(33), Li = p(133), Ri = p(362), R = p(263), N = p(6);

      const eyeDist = Math.hypot(R.x - L.x, R.y - L.y);
      const lensRadiusY = eyeDist * 0.26 * scale;
      const lensRadiusX = lensRadiusY * 1.3;
      const centerL = { x: (L.x + Li.x)/2, y: (L.y + Li.y)/2 + offsetY };
      const centerR = { x: (R.x + Ri.x)/2, y: (R.y + Ri.y)/2 + offsetY };

      ctx.lineWidth = Math.max(2, eyeDist * 0.04);
      ctx.strokeStyle = '#d1d5db';
      ctx.fillStyle = 'rgba(229, 231, 235, 0.1)';

      switch(frameId) {
        case 'classic':
          drawLens(centerL, lensRadiusX, lensRadiusY, angle, true);
          drawLens(centerR, lensRadiusX, lensRadiusY, angle, true);
          break;
        case 'round':
          drawCircleLens(centerL, lensRadiusY, angle);
          drawCircleLens(centerR, lensRadiusY, angle);
          break;
        case 'square':
          drawSquareLens(centerL, lensRadiusX, lensRadiusY, angle);
          drawSquareLens(centerR, lensRadiusX, lensRadiusY, angle);
          break;
        case 'cat-eye':
          drawCatEyeLens(centerL, lensRadiusX, lensRadiusY, angle);
          drawCatEyeLens(centerR, lensRadiusX, lensRadiusY, angle);
          break;
        case 'aviator':
          drawAviatorLens(centerL, lensRadiusX, lensRadiusY, angle);
          drawAviatorLens(centerR, lensRadiusX, lensRadiusY, angle);
          break;
      }

      // Bridge
      const bridgeY = (centerL.y + centerR.y) / 2;
      ctx.beginPath();
      ctx.moveTo(centerL.x + Math.cos(angle)*lensRadiusX*0.8, bridgeY);
      ctx.lineTo(centerR.x - Math.cos(angle)*lensRadiusX*0.8, bridgeY);
      ctx.stroke();

      // Temples
      const armLen = eyeDist * 0.9 * scale;
      ctx.beginPath();
      ctx.moveTo(centerL.x - Math.cos(angle)*lensRadiusX, centerL.y);
      ctx.lineTo(centerL.x - Math.cos(angle)*lensRadiusX - Math.cos(angle + 0.1)*armLen, centerL.y - Math.sin(angle + 0.1)*armLen);
      ctx.moveTo(centerR.x + Math.cos(angle)*lensRadiusX, centerR.y);
      ctx.lineTo(centerR.x + Math.cos(angle)*lensRadiusX + Math.cos(angle - 0.1)*armLen, centerR.y + Math.sin(angle - 0.1)*armLen);
      ctx.stroke();
    }

    function drawLens(center, rx, ry, angle, rounded=true) {
      ctx.save();
      ctx.translate(center.x, center.y);
      ctx.rotate(angle);
      ctx.beginPath();
      if (rounded) {
        const k = 0.55;
        const w = rx*2, h = ry*2;
        roundedRectPath(-rx, -ry, w, h, Math.min(rx*k, ry*k));
      } else {
        ctx.ellipse(0, 0, rx, ry, 0, 0, Math.PI * 2);
      }
      ctx.fill();
      ctx.stroke();
      ctx.restore();
    }

    function drawCircleLens(center, radius, angle) {
      ctx.beginPath();
      ctx.arc(center.x, center.y, radius, 0, Math.PI * 2);
      ctx.fill();
      ctx.stroke();
    }

    function drawSquareLens(center, rx, ry, angle) {
      ctx.save();
      ctx.translate(center.x, center.y);
      ctx.rotate(angle);
      ctx.fillRect(-rx, -ry, rx*2, ry*2);
      ctx.strokeRect(-rx, -ry, rx*2, ry*2);
      ctx.restore();
    }

    function drawCatEyeLens(center, rx, ry, angle) {
      ctx.save();
      ctx.translate(center.x, center.y);
      ctx.rotate(angle);
      ctx.beginPath();
      ctx.ellipse(0, 0, rx*1.2, ry*0.8, 0.3, 0, Math.PI * 2);
      ctx.fill();
      ctx.stroke();
      ctx.restore();
    }

    function drawAviatorLens(center, rx, ry, angle) {
      ctx.save();
      ctx.translate(center.x, center.y);
      ctx.rotate(angle);
      ctx.beginPath();
      ctx.arc(0, -ry*0.2, ry*1.1, 0.2, Math.PI - 0.2);
      ctx.quadraticCurveTo(0, ry*0.8, -rx*0.3, ry*0.6);
      ctx.quadraticCurveTo(0, ry*1.2, rx*0.3, ry*0.6);
      ctx.closePath();
      ctx.fill();
      ctx.stroke();
      ctx.restore();
    }

    function roundedRectPath(x,y,w,h,r){
      const rr = Math.min(r, w/2, h/2);
      ctx.moveTo(x+rr,y);
      ctx.arcTo(x+w,y,x+w,y+h,rr);
      ctx.arcTo(x+w,y+h,x,y+h,rr);
      ctx.arcTo(x,y+h,x,y,rr);
      ctx.arcTo(x,y,x+w,y,rr);
      ctx.closePath();
    }

    function drawDebug(landmarks){
      const w = overlay.width, h = overlay.height;
      const pts = landmarks.map(p => ({x:p.x*w, y:p.y*h}));
      const importantIdx = [33,133,362,263,6,168,1,10,152];
      ctx.save();
      ctx.globalCompositeOperation = 'source-over';
      ctx.fillStyle = '#60a5fa';
      importantIdx.forEach(i=>{ 
        const p = pts[i]; 
        ctx.beginPath(); 
        ctx.arc(p.x,p.y,3,0,Math.PI*2); 
        ctx.fill(); 
      });
      ctx.restore();
    }

    // --- MediaPipe setup
    let camera = null;
    const faceMesh = new FaceMesh({locateFile: (file) => `https://cdn.jsdelivr.net/npm/@mediapipe/face_mesh@0.4/${file}`});
    faceMesh.setOptions({
      maxNumFaces: 1,
      refineLandmarks: true,
      minDetectionConfidence: 0.5,
      minTrackingConfidence: 0.5
    });

    faceMesh.onResults((results) => {
      fitCanvas();
      const w = overlay.width, h = overlay.height;
      ctx.clearRect(0,0,w,h);

      if (!results.multiFaceLandmarks || results.multiFaceLandmarks.length === 0) {
        ctx.fillStyle = '#9ca3af';
        ctx.textAlign = 'center';
        ctx.font = '14px system-ui, -apple-system, Segoe UI, Roboto';
        ctx.fillText('No face detected. Step closer & face the camera.', w/2, h/2);
        return;
      }

      const raw = results.multiFaceLandmarks[0];
      const landmarks = smoothLandmarks(raw);
      drawGlasses(landmarks);
    });

    async function startCamera(){
      try{
        const stream = await navigator.mediaDevices.getUserMedia({ 
          video: { facingMode: 'user', width: {ideal: 1280}, height:{ideal:720} }, audio:false });
        video.srcObject = stream;
        await video.play();
        video.classList.remove('hidden');

        camera = new Camera(video, {
          onFrame: async () => {
            await faceMesh.send({image: video});
          },
          width: 1280, height: 720
        });
        camera.start();

        startBtn.disabled = true;
        stopBtn.disabled = false;
        snapBtn.disabled = false;
        startBtn.textContent = 'Running…';
        startBtn.classList.add('blink');
      }catch(err){
        alert('Camera access failed: ' + err.message);
        console.error(err);
      }
    }

    function stopCamera(){
      if (camera) { camera.stop(); camera = null; }
      if (video.srcObject){
        video.srcObject.getTracks().forEach(t => t.stop());
        video.srcObject = null;
      }
      startBtn.disabled = false;
      stopBtn.disabled = true;
      snapBtn.disabled = true;
      startBtn.textContent = 'Start Camera';
      startBtn.classList.remove('blink');
      ctx.clearRect(0,0,overlay.width, overlay.height);
    }

    function takeSnapshot(){
      const w = overlay.width, h = overlay.height;
      const tmp = document.createElement('canvas');
      tmp.width = w; tmp.height = h;
      const tctx = tmp.getContext('2d');
      tctx.drawImage(video, 0, 0, w, h);
      tctx.drawImage(overlay, 0, 0);
      const url = tmp.toDataURL('image/png');
      const img = new Image();
      img.src = url; img.alt = 'Snapshot';

      const div = document.createElement('div');
      div.className = 'shot';
      const a = document.createElement('a');
      a.href = url; a.download = `glasses_tryon_${Date.now()}.png`;
      a.title = 'Download snapshot';
      a.appendChild(img);
      div.appendChild(a);
      shots.prepend(div);
    }

    startBtn.addEventListener('click', startCamera);
    stopBtn.addEventListener('click', stopCamera);
    snapBtn.addEventListener('click', takeSnapshot);

    // Initialize tabs and load frames
    initTabs();
    loadDefaultFrames();

    // Pre-warm MediaPipe
    faceMesh.initialize();