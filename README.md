[deepseek_html_20250822_9cb2e9.html](https://github.com/user-attachments/files/21940899/deepseek_html_20250822_9cb2e9.html)
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>🔒 Секретный Чат с Режимом Рации и Демонстрацией Экрана</title>
    <script src="https://unpkg.com/peerjs@1.5.0/dist/peerjs.min.js"></script>
    <style>
        * { box-sizing: border-box; transition: all 0.3s ease; }
        body { font-family: 'Segoe UI', sans-serif; max-width: 600px; margin: 0 auto; padding: 20px; 
               background: linear-gradient(135deg, #1a2a6c, #b21f1f, #fdbb2d); color: #333; min-height: 100vh; }
        .container { display: flex; flex-direction: column; gap: 20px; }
        h2 { text-align: center; color: white; text-shadow: 0 2px 4px rgba(0,0,0,0.3); margin-bottom: 10px; }
        .card { background: rgba(255, 255, 255, 0.95); border-radius: 12px; padding: 20px; 
                box-shadow: 0 4px 15px rgba(0,0,0,0.15); backdrop-filter: blur(10px); }
        #chat-box { height: 250px; overflow-y: auto; border: 1px solid #ddd; padding: 15px; margin: 15px 0; 
                    background: white; border-radius: 8px; display: flex; flex-direction: column; gap: 10px; }
        .message { padding: 10px 15px; border-radius: 18px; max-width: 85%; word-break: break-word; 
                   animation: fadeIn 0.3s ease; }
        .my-message { background: #dcf8c6; margin-left: auto; border-bottom-right-radius: 5px; }
        .their-message { background: #e6f3ff; border-bottom-left-radius: 5px; }
        .system-message { background: #fff9e6; text-align: center; font-style: italic; border-radius: 8px; margin: 5px 0; }
        input, button, select { padding: 12px 15px; margin: 5px 0; border: 1px solid #ddd; border-radius: 8px; font-size: 16px; }
        input, select { width: 100%; background: #f9f9f9; }
        input:focus, select:focus { outline: none; border-color: #6e8efb; box-shadow: 0 0 0 2px rgba(110, 142, 251, 0.2); }
        button { background: #4CAF50; color: white; border: none; cursor: pointer; font-weight: bold; 
                 display: flex; align-items: center; justify-content: center; gap: 5px; }
        button:hover { opacity: 0.9; transform: translateY(-2px); }
        button:active { transform: translateY(0); }
        button.secondary { background: #2196F3; }
        button.danger { background: #f44336; }
        button.walkie { background: #ff9800; }
        button.walkie-active { background: #ff5722; animation: pulse 0.5s infinite; }
        button.screen-share { background: #9c27b0; }
        button.screen-share-active { background: #7b1fa2; animation: pulse 0.5s infinite; }
        button:disabled { background: #ccc; cursor: not-allowed; transform: none; }
        .flex { display: flex; gap: 12px; align-items: center; }
        #connection-status, #call-status { padding: 12px; border-radius: 8px; text-align: center; margin: 12px 0; font-weight: bold; }
        .connected { background: #e8f5e9; color: #2e7d32; }
        .disconnected { background: #ffebee; color: #c62828; }
        .calling { background: #e3f2fd; color: #1565c0; }
        .in-call { background: #e8f5e9; color: #2e7d32; }
        .call-ended { background: #fff3e0; color: #ef6c00; }
        .talk-indicator { display: flex; align-items: center; justify-content: center; gap: 10px; margin: 15px 0; 
                          padding: 10px; background: #fff3e0; border-radius: 8px; border: 2px solid #ff9800; }
        .indicator-dot { width: 20px; height: 20px; border-radius: 50%; background: #ff5722; animation: talkPulse 1s infinite; }
        #screen-container { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; 
                            background: rgba(0,0,0,0.8); z-index: 1000; justify-content: center; align-items: center; }
        #remote-screen { max-width: 90%; max-height: 80%; border: 2px solid white; border-radius: 8px; }
        #close-screen { position: absolute; top: 20px; right: 20px; background: #f44336; color: white; 
                        border: none; border-radius: 50%; width: 50px; height: 50px; font-size: 24px; }
        .screen-controls { position: absolute; bottom: 20px; left: 0; width: 100%; display: flex; justify-content: center; gap: 10px; 
                           transition: opacity 0.3s ease; }
        .screen-controls button { padding: 10px 15px; }
        .fullscreen-mode #remote-screen { width: 100%; height: 100%; max-width: 100%; max-height: 100%; border-radius: 0; }
        .fullscreen-mode .screen-controls { bottom: 30px; }
        
        /* Стили для скрытия элементов в полноэкранном режиме */
        .fullscreen-mode #close-screen,
        .fullscreen-mode .screen-controls {
            opacity: 0;
            pointer-events: none;
            transition: opacity 0.3s ease;
        }
        
        .fullscreen-mode:hover #close-screen,
        .fullscreen-mode:hover .screen-controls {
            opacity: 1;
            pointer-events: auto;
        }
        
        /* Скрываем элементы управления после нескольких секунд в полноэкранном режиме */
        .fullscreen-mode.hide-controls #close-screen,
        .fullscreen-mode.hide-controls .screen-controls {
            opacity: 0;
            pointer-events: none;
        }
        
        /* Стили для настроек качества видео */
        .video-settings { 
            margin-top: 15px; 
            padding: 15px; 
            background: #f5f5f5; 
            border-radius: 8px; 
            border: 1px solid #ddd; 
        }
        .video-settings h3 { 
            margin-top: 0; 
            margin-bottom: 10px; 
            font-size: 16px; 
            color: #333; 
        }
        .settings-row { 
            display: flex; 
            gap: 10px; 
            margin-bottom: 10px; 
            align-items: center; 
        }
        .settings-row label { 
            min-width: 120px; 
            font-weight: bold; 
        }
        .settings-row select { 
            flex: 1; 
            padding: 8px 12px; 
        }
        .settings-info { 
            font-size: 12px; 
            color: #666; 
            margin-top: 5px; 
            font-style: italic; 
        }
        
        @keyframes pulse { 0% { opacity: 1; transform: scale(1); } 50% { opacity: 0.8; transform: scale(1.05); } 100% { opacity: 1; transform: scale(1); } }
        @keyframes talkPulse { 0% { opacity: 0.3; transform: scale(0.8); } 50% { opacity: 1; transform: scale(1.2); } 100% { opacity: 0.3; transform: scale(0.8); } }
        @media (max-width: 600px) { 
            body { padding: 15px; } 
            .card { padding: 15px; } 
            .flex { flex-direction: column; gap: 8px; } 
            button { width: 100%; } 
            .settings-row { flex-direction: column; align-items: flex-start; }
            .settings-row label { min-width: auto; }
        }
    </style>
</head>
<body>
    <div class="container">
        <h2>🔒 Секретный Чат с Режимом Рации и Демонстрацией Экрана</h2>
        
        <div class="card">
            <div id="connection-status" class="disconnected">❌ Не подключено</div>
            <div class="flex">
                <b>Твой ID:</b> 
                <span id="my-id" style="background:#eee; padding:8px 12px; border-radius:5px; font-family: monospace;">...</span>
                <button id="copy-id-button" onclick="copyMyId()">📋</button>
            </div>
        </div>

        <div class="card">
            <div class="flex">
                <input type="text" id="friend-id" placeholder="Введите ID друга">
                <button onclick="connectToFriend()">Подключиться</button>
            </div>
        </div>
        
        <div class="card">
            <div id="call-status" class="call-ended">Звонок: Неактивен</div>
            
            <div id="talk-indicator" class="talk-indicator" style="display: none;">
                <div class="indicator-dot"></div>
                <div class="indicator-text">ИДЕТ ПЕРЕДАЧА...</div>
            </div>
            
            <div class="flex">
                <button id="call-button" onclick="startAudioCall()" class="secondary" disabled>📞 Начать звонок</button>
                <button id="talk-button" class="walkie" onmousedown="startTalking()" onmouseup="stopTalking()" ontouchstart="startTalking()" touchend="stopTalking()" disabled>🎤 Говорить</button>
                <button id="screen-share-button" onclick="toggleScreenShare()" class="screen-share" disabled>🖥️ Демонстрация экрана</button>
                <button id="end-call-button" onclick="endAudioCall()" class="danger" disabled>📞 Завершить</button>
            </div>
            
            <div style="margin-top: 15px;">
                <label for="walkie-key">Клавиша для разговора:</label>
                <select id="walkie-key">
                    <option value=" ">Пробел</option>
                    <option value="Control">Ctrl</option>
                    <option value="Alt">Alt</option>
                    <option value="Shift">Shift</option>
                    <option value="`">Клавиша ` (Ё)</option>
                </select>
            </div>
            
            <div id="audio-status">Микрофон: 🔴 Выкл</div>
            <audio id="remote-audio" autoplay></audio>
            
            <!-- Настройки качества видео -->
            <div class="video-settings">
                <h3>⚙️ Настройки качества видео</h3>
                
                <div class="settings-row">
                    <label for="video-resolution">Разрешение:</label>
                    <select id="video-resolution">
                        <option value="low">Низкое (640x480)</option>
                        <option value="medium" selected>Среднее (1280x720)</option>
                        <option value="high">Высокое (1920x1080)</option>
                        <option value="ultra">Ультра (2560x1440)</option>
                        <option value="native">Нативное (оригинал)</option>
                    </select>
                </div>
                
                <div class="settings-row">
                    <label for="video-framerate">Частота кадров:</label>
                    <select id="video-framerate">
                        <option value="5">5 FPS (очень низкая)</option>
                        <option value="15">15 FPS (низкая)</option>
                        <option value="30" selected>30 FPS (стандартная)</option>
                        <option value="60">60 FPS (высокая)</option>
                    </select>
                </div>
                
                <div class="settings-row">
                    <label for="video-bitrate">Битрейт:</label>
                    <select id="video-bitrate">
                        <option value="low">Низкий (1 Мбит/с)</option>
                        <option value="medium" selected>Средний (3 Мбит/с)</option>
                        <option value="high">Высокий (5 Мбит/с)</option>
                        <option value="ultra">Ультра (8 Мбит/с)</option>
                        <option value="auto">Авто (рекомендуется)</option>
                    </select>
                </div>
                
                <div class="settings-info">
                    Настройки применяются при следующей демонстрации экрана
                </div>
            </div>
        </div>
        
        <div class="card">
            <div id="chat-box"></div>
            <div class="flex">
                <input type="text" id="message-input" placeholder="Введите сообщение..." onkeypress="if(event.key=='Enter')sendMessage()" disabled>
                <button onclick="sendMessage()" class="secondary" id="send-button" disabled>Отправить</button>
            </div>
        </div>
    </div>

    <div id="screen-container">
        <video id="remote-screen" autoplay></video>
        <button id="close-screen" onclick="closeScreen()">✕</button>
        <div class="screen-controls">
            <button id="mute-screen-audio" onclick="toggleScreenAudio()">🔇 Выкл звук</button>
            <button id="fullscreen-button" onclick="toggleFullscreen()">⛶ Полный экран</button>
            <button onclick="closeScreen()">Закрыть</button>
        </div>
    </div>

    <script>
        const config = { iceServers: [{ urls: ['stun:stun.l.google.com:19302', 'stun:stun1.l.google.com:19302'] }] };
        let peer = null, activeConnection = null, activeCall = null, myPeerId = null;
        let localStream = null, isAudioCallActive = false, isCalling = false, isTalking = false;
        let audioTrack = null, selectedWalkieKey = ' ';
        let screenStream = null, isScreenSharing = false, screenCall = null;
        let isScreenAudioMuted = false;
        let isFullscreen = false;
        let controlsTimeout = null;

        // Настройки качества видео по умолчанию
        const videoSettings = {
            resolution: 'medium',
            framerate: 30,
            bitrate: 'medium'
        };

        function generateId() {
            const adj = ['fast', 'happy', 'smart', 'brave', 'bright', 'quiet', 'cool', 'kind'];
            const animals = ['tiger', 'lion', 'eagle', 'wolf', 'bear', 'dolphin', 'fox', 'panda'];
            return `${adj[Math.floor(Math.random()*adj.length)]}-${animals[Math.floor(Math.random()*animals.length)]}-${Math.floor(Math.random()*90+10)}`;
        }

        function startChat() {
            myPeerId = generateId();
            document.getElementById('my-id').textContent = myPeerId;
            peer = new Peer(myPeerId, { config: config });

            peer.on('open', () => {
                document.getElementById('copy-id-button').disabled = false;
                document.getElementById('walkie-key').addEventListener('change', function() {
                    selectedWalkieKey = this.value;
                });
                
                // Инициализация настроек качества видео
                initVideoSettings();
                
                document.addEventListener('keydown', handleKeyDown);
                document.addEventListener('keyup', handleKeyUp);
                // Обработка клавиши F11 для полноэкранного режима
                document.addEventListener('keydown', handleFullscreenKey);
                // Обработка выхода из полноэкранного режима
                document.addEventListener('fullscreenchange', handleFullscreenChange);
            });
            
            peer.on('connection', (conn) => {
                if (activeConnection) activeConnection.close();
                activeConnection = conn;
                setupConnection(conn);
                updateConnectionStatus(true);
                document.getElementById('call-button').disabled = false;
                document.getElementById('screen-share-button').disabled = false;
                document.getElementById('message-input').disabled = false;
                document.getElementById('send-button').disabled = false;
            });
            
            peer.on('call', (call) => {
                // Обработка аудиозвонка
                if (call.metadata && call.metadata.type === 'audio') {
                    if (isCalling || isAudioCallActive) return;
                    if (confirm('Входящий звонок. Принять?')) {
                        navigator.mediaDevices.getUserMedia({ audio: true, video: false })
                            .then((stream) => {
                                localStream = stream;
                                audioTrack = stream.getAudioTracks()[0];
                                audioTrack.enabled = false;
                                call.answer(stream);
                                activeCall = call;
                                setupCall(call);
                                updateCallStatus('in-call', 'Звонок: Активен (режим рации)');
                                updateAudioStatus(false);
                                isAudioCallActive = true;
                                document.getElementById('talk-button').disabled = false;
                                document.getElementById('screen-share-button').disabled = false;
                                document.getElementById('end-call-button').disabled = false;
                            })
                            .catch((err) => {
                                updateCallStatus('call-ended', 'Ошибка микрофона');
                            });
                    }
                }
                // Обработка демонстрации экрана
                else if (call.metadata && call.metadata.type === 'screen') {
                    if (confirm('Пользователь хочет показать свой экран. Принять?')) {
                        call.answer(null); // Принимаем без отправки своего потока
                        screenCall = call;
                        setupScreenCall(call);
                    } else {
                        call.close();
                    }
                }
            });
        }

        function initVideoSettings() {
            // Загрузка сохраненных настроек
            const savedSettings = localStorage.getItem('videoSettings');
            if (savedSettings) {
                Object.assign(videoSettings, JSON.parse(savedSettings));
            }
            
            // Установка значений в выпадающих списках
            document.getElementById('video-resolution').value = videoSettings.resolution;
            document.getElementById('video-framerate').value = videoSettings.framerate;
            document.getElementById('video-bitrate').value = videoSettings.bitrate;
            
            // Обработчики изменений настроек
            document.getElementById('video-resolution').addEventListener('change', function() {
                videoSettings.resolution = this.value;
                saveVideoSettings();
            });
            
            document.getElementById('video-framerate').addEventListener('change', function() {
                videoSettings.framerate = parseInt(this.value);
                saveVideoSettings();
            });
            
            document.getElementById('video-bitrate').addEventListener('change', function() {
                videoSettings.bitrate = this.value;
                saveVideoSettings();
            });
        }

        function saveVideoSettings() {
            localStorage.setItem('videoSettings', JSON.stringify(videoSettings));
        }

        function getVideoConstraints() {
            let width, height, frameRate, bitrate;
            
            // Настройки разрешения
            switch(videoSettings.resolution) {
                case 'low':
                    width = { ideal: 640 };
                    height = { ideal: 480 };
                    break;
                case 'medium':
                    width = { ideal: 1280 };
                    height = { ideal: 720 };
                    break;
                case 'high':
                    width = { ideal: 1920 };
                    height = { ideal: 1080 };
                    break;
                case 'ultra':
                    width = { ideal: 2560 };
                    height = { ideal: 1440 };
                    break;
                case 'native':
                default:
                    width = { max: 4096 };
                    height = { max: 2160 };
                    break;
            }
            
            // Настройки частоты кадров
            frameRate = { ideal: videoSettings.framerate, max: 60 };
            
            return {
                width: width,
                height: height,
                frameRate: frameRate
            };
        }

        function getBitrateSettings() {
            switch(videoSettings.bitrate) {
                case 'low':
                    return 1000000; // 1 Мбит/с
                case 'medium':
                    return 3000000; // 3 Мбит/с
                case 'high':
                    return 5000000; // 5 Мбит/с
                case 'ultra':
                    return 8000000; // 8 Мбит/с
                case 'auto':
                default:
                    return null; // Автоматический битрейт
            }
        }

        function handleKeyDown(event) {
            const key = event.code === 'Backquote' ? '`' : event.key;
            if (key === selectedWalkieKey && isAudioCallActive && !isTalking) {
                event.preventDefault();
                startTalking();
            }
        }

        function handleKeyUp(event) {
            const key = event.code === 'Backquote' ? '`' : event.key;
            if (key === selectedWalkieKey && isTalking) {
                event.preventDefault();
                stopTalking();
            }
        }

        function handleFullscreenKey(event) {
            // F11 для переключения полноэкранного режима
            if (event.key === 'F11' && document.getElementById('screen-container').style.display === 'flex') {
                event.preventDefault();
                toggleFullscreen();
            }
            // ESC для выхода из полноэкранного режима
            else if (event.key === 'Escape' && isFullscreen) {
                exitFullscreen();
            }
        }

        function handleFullscreenChange() {
            if (!document.fullscreenElement) {
                exitFullscreen();
            }
        }

        function startTalking() {
            if (!isAudioCallActive || isTalking || !audioTrack) return;
            isTalking = true;
            audioTrack.enabled = true;
            updateAudioStatus(true);
            updateTalkIndicator(true);
            document.getElementById('talk-button').classList.add('walkie-active');
            if (activeConnection) activeConnection.send({ type: 'talking', status: true });
        }

        function stopTalking() {
            if (!isTalking || !audioTrack) return;
            isTalking = false;
            audioTrack.enabled = false;
            updateAudioStatus(false);
            updateTalkIndicator(false);
            document.getElementById('talk-button').classList.remove('walkie-active');
            if (activeConnection) activeConnection.send({ type: 'talking', status: false });
        }

        function updateTalkIndicator(talking) {
            document.getElementById('talk-indicator').style.display = talking ? 'flex' : 'none';
        }

        function updateAudioStatus(isActive) {
            document.getElementById('audio-status').textContent = `Микрофон: ${isActive ? '🟢 Вкл' : '🔴 Выкл'}`;
        }

        function setupConnection(conn) {
            conn.on('data', (data) => {
                if (data.type === 'message') addMessage(data.text, false);
                else if (data.type === 'call-request') {}
                else if (data.type === 'system') addSystemMessage(data.text);
                else if (data.type === 'talking') updateTalkIndicator(data.status);
                else if (data.type === 'screen-sharing') {
                    if (data.status) {
                        addSystemMessage('Пользователь начал демонстрацию экрана');
                    } else {
                        addSystemMessage('Пользователь остановил демонстрацию экрана');
                        closeScreen();
                    }
                }
            });
            
            conn.on('close', () => {
                activeConnection = null;
                updateConnectionStatus(false);
                document.getElementById('call-button').disabled = true;
                document.getElementById('talk-button').disabled = true;
                document.getElementById('screen-share-button').disabled = true;
                document.getElementById('message-input').disabled = true;
                document.getElementById('send-button').disabled = true;
                if (isAudioCallActive) endAudioCall();
                if (isScreenSharing) stopScreenShare();
            });
        }

        function connectToFriend() {
            const friendId = document.getElementById('friend-id').value.trim();
            if (!friendId) return;
            if (activeConnection) return;
            
            const conn = peer.connect(friendId);
            conn.on('open', () => {
                activeConnection = conn;
                setupConnection(conn);
                updateConnectionStatus(true);
                document.getElementById('call-button').disabled = false;
                document.getElementById('screen-share-button').disabled = false;
                document.getElementById('message-input').disabled = false;
                document.getElementById('send-button').disabled = false;
            });
        }

        function startAudioCall() {
            if (!activeConnection || isAudioCallActive) return;
            isCalling = true;
            updateCallStatus('calling', 'Звонок: Вызов...');
            
            navigator.mediaDevices.getUserMedia({ audio: true, video: false })
                .then((stream) => {
                    localStream = stream;
                    audioTrack = stream.getAudioTracks()[0];
                    audioTrack.enabled = false;
                    updateAudioStatus(false);
                    const call = peer.call(activeConnection.peer, stream, { metadata: { type: 'audio' } });
                    activeCall = call;
                    setupCall(call);
                    isCalling = false;
                })
                .catch((err) => {
                    updateCallStatus('call-ended', 'Ошибка микрофона');
                    isCalling = false;
                });
        }
        
        function setupCall(call) {
            call.on('stream', (remoteStream) => {
                document.getElementById('remote-audio').srcObject = remoteStream;
                isAudioCallActive = true;
                document.getElementById('talk-button').disabled = false;
                document.getElementById('screen-share-button').disabled = false;
                document.getElementById('end-call-button').disabled = false;
                updateCallStatus('in-call', 'Звонок: Активен (режим рации)');
            });
            
            call.on('close', () => endAudioCall());
            call.on('error', () => endAudioCall());
        }
        
        function endAudioCall() {
            if (isTalking) stopTalking();
            if (activeCall) activeCall.close();
            if (localStream) {
                localStream.getTracks().forEach(track => track.stop());
            }
            activeCall = null;
            localStream = null;
            audioTrack = null;
            isAudioCallActive = false;
            document.getElementById('talk-button').disabled = true;
            document.getElementById('screen-share-button').disabled = true;
            document.getElementById('end-call-button').disabled = true;
            updateAudioStatus(false);
            updateTalkIndicator(false);
            document.getElementById('talk-button').classList.remove('walkie-active');
            document.getElementById('remote-audio').srcObject = null;
            updateCallStatus('call-ended', 'Звонок: Завершен');
        }
        
        // Функции для демонстрации экрана со звуком
        async function toggleScreenShare() {
            if (isScreenSharing) {
                stopScreenShare();
            } else {
                startScreenShare();
            }
        }

        async function startScreenShare() {
            if (!activeConnection) return;
            
            try {
                // Получаем настройки видео
                const videoConstraints = getVideoConstraints();
                const bitrate = getBitrateSettings();
                
                // Запрос доступа к экрану со звуком
                screenStream = await navigator.mediaDevices.getDisplayMedia({
                    video: {
                        cursor: "always",
                        ...videoConstraints
                    },
                    audio: {
                        echoCancellation: false,
                        noiseSuppression: false,
                        sampleRate: 44100
                    }
                });
                
                // Применяем настройки битрейта, если указано
                if (bitrate) {
                    const videoTrack = screenStream.getVideoTracks()[0];
                    if (videoTrack) {
                        const settings = videoTrack.getSettings();
                        const constraints = {
                            width: settings.width,
                            height: settings.height,
                            frameRate: settings.frameRate,
                            bitrate: bitrate
                        };
                        
                        try {
                            await videoTrack.applyConstraints({
                                advanced: [constraints]
                            });
                        } catch (err) {
                            console.warn('Не удалось применить настройки битрейта:', err);
                        }
                    }
                }
                
                // Создаем вызов для демонстрации экрана
                screenCall = peer.call(activeConnection.peer, screenStream, {
                    metadata: { type: 'screen' }
                });
                
                isScreenSharing = true;
                document.getElementById('screen-share-button').classList.add('screen-share-active');
                document.getElementById('screen-share-button').textContent = '🖥️ Остановить демонстрацию';
                
                // Уведомляем другого пользователя
                activeConnection.send({ type: 'screen-sharing', status: true });
                
                // Обрабатываем закрытие демонстрации экрана
                const videoTrack = screenStream.getVideoTracks()[0];
                if (videoTrack) {
                    videoTrack.onended = stopScreenShare;
                }
                
                const audioTracks = screenStream.getAudioTracks();
                if (audioTracks.length > 0) {
                    audioTracks[0].onended = stopScreenShare;
                }
                
                setupScreenCall(screenCall);
                
            } catch (err) {
                console.error('Ошибка при демонстрации экрана:', err);
                addSystemMessage('Не удалось начать демонстрацию экрана');
            }
        }

        function stopScreenShare() {
            if (screenStream) {
                screenStream.getTracks().forEach(track => track.stop());
                screenStream = null;
            }
            
            if (screenCall) {
                screenCall.close();
                screenCall = null;
            }
            
            isScreenSharing = false;
            document.getElementById('screen-share-button').classList.remove('screen-share-active');
            document.getElementById('screen-share-button').textContent = '🖥️ Демонстрация экрана';
            
            // Уведомляем другого пользователя
            if (activeConnection) {
                activeConnection.send({ type: 'screen-sharing', status: false });
            }
        }

        function setupScreenCall(call) {
            call.on('stream', (remoteStream) => {
                // Показываем экран другого пользователя
                const remoteScreen = document.getElementById('remote-screen');
                remoteScreen.srcObject = remoteStream;
                document.getElementById('screen-container').style.display = 'flex';
                
                // Воспроизводим звук с экрана
                const remoteAudio = document.getElementById('remote-audio');
                if (remoteAudio.srcObject !== remoteStream) {
                    remoteAudio.srcObject = remoteStream;
                }
            });
            
            call.on('close', () => {
                closeScreen();
            });
            
            call.on('error', (err) => {
                console.error('Ошибка при демонстрации экрана:', err);
                closeScreen();
            });
        }

        function closeScreen() {
            // Выходим из полноэкранного режима при закрытии
            if (isFullscreen) {
                exitFullscreen();
            }
            
            document.getElementById('screen-container').style.display = 'none';
            const remoteScreen = document.getElementById('remote-screen');
            remoteScreen.srcObject = null;
            
            // Если мы принимали экран, закрываем вызов
            if (screenCall && !isScreenSharing) {
                screenCall.close();
                screenCall = null;
            }
        }

        function toggleScreenAudio() {
            const remoteScreen = document.getElementById('remote-screen');
            if (remoteScreen.srcObject) {
                const audioTracks = remoteScreen.srcObject.getAudioTracks();
                if (audioTracks.length > 0) {
                    isScreenAudioMuted = !isScreenAudioMuted;
                    audioTracks[0].enabled = !isScreenAudioMuted;
                    document.getElementById('mute-screen-audio').textContent = 
                        isScreenAudioMuted ? '🔈 Вкл звук' : '🔇 Выкл звук';
                }
            }
        }

        function toggleFullscreen() {
            const screenContainer = document.getElementById('screen-container');
            const remoteScreen = document.getElementById('remote-screen');
            
            if (!isFullscreen) {
                // Вход в полноэкранный режим
                if (screenContainer.requestFullscreen) {
                    screenContainer.requestFullscreen();
                } else if (screenContainer.webkitRequestFullscreen) {
                    screenContainer.webkitRequestFullscreen();
                } else if (screenContainer.msRequestFullscreen) {
                    screenContainer.msRequestFullscreen();
                }
                
                screenContainer.classList.add('fullscreen-mode');
                document.getElementById('fullscreen-button').textContent = '⛶ Обычный режим';
                isFullscreen = true;
                
                // Автоматически разворачиваем видео на весь экран
                remoteScreen.style.width = '100%';
                remoteScreen.style.height = '100%';
                remoteScreen.style.maxWidth = '100%';
                remoteScreen.style.maxHeight = '100%';
                
                // Запускаем таймер для скрытия элементов управления
                hideControlsAfterDelay();
                
                // Добавляем обработчики для показа/скрытия элементов управления
                screenContainer.addEventListener('mousemove', showControlsTemporarily);
                screenContainer.addEventListener('click', showControlsTemporarily);
                
            } else {
                // Выход из полноэкранного режима
                exitFullscreen();
            }
        }

        function exitFullscreen() {
            if (document.exitFullscreen) {
                document.exitFullscreen();
            } else if (document.webkitExitFullscreen) {
                document.webkitExitFullscreen();
            } else if (document.msExitFullscreen) {
                document.msExitFullscreen();
            }
            
            const screenContainer = document.getElementById('screen-container');
            const remoteScreen = document.getElementById('remote-screen');
            
            screenContainer.classList.remove('fullscreen-mode');
            screenContainer.classList.remove('hide-controls');
            document.getElementById('fullscreen-button').textContent = '⛶ Полный экран';
            isFullscreen = false;
            
            // Возвращаем обычные размеры видео
            remoteScreen.style.width = '';
            remoteScreen.style.height = '';
            remoteScreen.style.maxWidth = '90%';
            remoteScreen.style.maxHeight = '80%';
            
            // Убираем обработчики
            screenContainer.removeEventListener('mousemove', showControlsTemporarily);
            screenContainer.removeEventListener('click', showControlsTemporarily);
            
            // Очищаем таймер
            if (controlsTimeout) {
                clearTimeout(controlsTimeout);
                controlsTimeout = null;
            }
        }
        
        function hideControlsAfterDelay() {
            if (controlsTimeout) {
                clearTimeout(controlsTimeout);
            }
            
            controlsTimeout = setTimeout(() => {
                const screenContainer = document.getElementById('screen-container');
                screenContainer.classList.add('hide-controls');
            }, 3000); // Скрываем элементы управления через 3 секунды
        }
        
        function showControlsTemporarily() {
            const screenContainer = document.getElementById('screen-container');
            screenContainer.classList.remove('hide-controls');
            
            hideControlsAfterDelay();
        }

        function sendMessage() {
            const input = document.getElementById('message-input');
            const message = input.value.trim();
            if (!message || !activeConnection) return;
            activeConnection.send({ type: 'message', text: message });
            addMessage(message, true);
            input.value = '';
        }

        function addMessage(text, isMyMessage) {
            const chatBox = document.getElementById('chat-box');
            const messageDiv = document.createElement('div');
            messageDiv.className = `message ${isMyMessage ? 'my-message' : 'their-message'}`;
            messageDiv.textContent = text;
            chatBox.appendChild(messageDiv);
            chatBox.scrollTop = chatBox.scrollHeight;
        }

        function addSystemMessage(text) {
            const chatBox = document.getElementById('chat-box');
            const messageDiv = document.createElement('div');
            messageDiv.className = 'system-message';
            messageDiv.textContent = text;
            chatBox.appendChild(messageDiv);
            chatBox.scrollTop = chatBox.scrollHeight;
        }

        function updateConnectionStatus(connected) {
            const status = document.getElementById('connection-status');
            if (connected) {
                status.textContent = '✅ Подключено';
                status.className = 'connected';
            } else {
                status.textContent = '❌ Не подключено';
                status.className = 'disconnected';
            }
        }

        function updateCallStatus(statusClass, text) {
            const status = document.getElementById('call-status');
            status.textContent = text;
            status.className = statusClass;
        }

        function copyMyId() {
            const myId = document.getElementById('my-id').textContent;
            navigator.clipboard.writeText(myId).then(() => {
                alert('ID скопирован в буфер обмена!');
            });
        }

        // Инициализация при загрузке страницы
        window.onload = startChat;
    </script>
</body>
</html>
