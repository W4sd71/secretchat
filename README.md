[lad messenger wasche online wasche.html](https://github.com/user-attachments/files/21922623/lad.messenger.wasche.online.wasche.html)
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>🔒 Секретный Чат с Режимом Рации</title>
    <script src="https://unpkg.com/peerjs@1.5.0/dist/peerjs.min.js"></script>
    <style>
        * {
            box-sizing: border-box;
            transition: all 0.3s ease;
        }
        
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            max-width: 600px;
            margin: 0 auto;
            padding: 20px;
            background: linear-gradient(135deg, #1a2a6c, #b21f1f, #fdbb2d);
            color: #333;
            min-height: 100vh;
        }
        
        .container {
            display: flex;
            flex-direction: column;
            gap: 20px;
        }
        
        h2 {
            text-align: center;
            color: white;
            text-shadow: 0 2px 4px rgba(0,0,0,0.3);
            margin-bottom: 10px;
        }
        
        .card {
            background: rgba(255, 255, 255, 0.95);
            border-radius: 12px;
            padding: 20px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.15);
            backdrop-filter: blur(10px);
        }
        
        #chat-box {
            height: 250px;
            overflow-y: auto;
            border: 1px solid #ddd;
            padding: 15px;
            margin: 15px 0;
            background: white;
            border-radius: 8px;
            display: flex;
            flex-direction: column;
            gap: 10px;
        }
        
        .message {
            padding: 10px 15px;
            border-radius: 18px;
            max-width: 85%;
            word-break: break-word;
            animation: fadeIn 0.3s ease;
        }
        
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }
        
        .my-message {
            background: #dcf8c6;
            margin-left: auto;
            border-bottom-right-radius: 5px;
        }
        
        .their-message {
            background: #e6f3ff;
            border-bottom-left-radius: 5px;
        }
        
        .system-message {
            background: #fff9e6;
            text-align: center;
            font-style: italic;
            border-radius: 8px;
            margin: 5px 0;
        }
        
        input, button, select {
            padding: 12px 15px;
            margin: 5px 0;
            border: 1px solid #ddd;
            border-radius: 8px;
            font-size: 16px;
        }
        
        input, select {
            width: 100%;
            background: #f9f9f9;
        }
        
        input:focus, select:focus {
            outline: none;
            border-color: #6e8efb;
            box-shadow: 0 0 0 2px rgba(110, 142, 251, 0.2);
        }
        
        button {
            background: #4CAF50;
            color: white;
            border: none;
            cursor: pointer;
            font-weight: bold;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 5px;
        }
        
        button:hover {
            opacity: 0.9;
            transform: translateY(-2px);
        }
        
        button:active {
            transform: translateY(0);
        }
        
        button.secondary {
            background: #2196F3;
        }
        
        button.danger {
            background: #f44336;
        }
        
        button.walkie {
            background: #ff9800;
        }
        
        button.walkie-active {
            background: #ff5722;
            animation: pulse 0.5s infinite;
        }
        
        button:disabled {
            background: #ccc;
            cursor: not-allowed;
            transform: none;
        }
        
        .flex {
            display: flex;
            gap: 12px;
            align-items: center;
        }
        
        #connection-status, #call-status, #walkie-status {
            padding: 12px;
            border-radius: 8px;
            text-align: center;
            margin: 12px 0;
            font-weight: bold;
        }
        
        .connected {
            background: #e8f5e9;
            color: #2e7d32;
        }
        
        .disconnected {
            background: #ffebee;
            color: #c62828;
        }
        
        .calling {
            background: #e3f2fd;
            color: #1565c0;
        }
        
        .in-call {
            background: #e8f5e9;
            color: #2e7d32;
        }
        
        .call-ended {
            background: #fff3e0;
            color: #ef6c00;
        }
        
        .walkie-active {
            background: #ff9800;
            color: white;
            animation: pulse 1s infinite;
        }
        
        @keyframes pulse {
            0% { opacity: 1; transform: scale(1); }
            50% { opacity: 0.8; transform: scale(1.05); }
            100% { opacity: 1; transform: scale(1); }
        }
        
        .status-container {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 15px;
        }
        
        .tooltip {
            position: relative;
            display: inline-block;
            cursor: pointer;
        }
        
        .tooltip .tooltiptext {
            visibility: hidden;
            width: 200px;
            background-color: #555;
            color: #fff;
            text-align: center;
            border-radius: 6px;
            padding: 8px;
            position: absolute;
            z-index: 1;
            bottom: 125%;
            left: 50%;
            margin-left: -100px;
            opacity: 0;
            transition: opacity 0.3s;
        }
        
        .tooltip:hover .tooltiptext {
            visibility: visible;
            opacity: 1;
        }
        
        .notification {
            position: fixed;
            top: 20px;
            right: 20px;
            padding: 15px;
            border-radius: 8px;
            background: #4CAF50;
            color: white;
            box-shadow: 0 4px 15px rgba(0,0,0,0.2);
            transform: translateX(100%);
            opacity: 0;
            transition: all 0.3s ease;
            z-index: 1000;
        }
        
        .notification.show {
            transform: translateX(0);
            opacity: 1;
        }
        
        .key-button {
            display: inline-block;
            padding: 5px 10px;
            background: #eee;
            border-radius: 4px;
            border: 1px solid #ddd;
            font-family: monospace;
            font-weight: bold;
        }
        
        .talk-indicator {
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 10px;
            margin: 15px 0;
            padding: 10px;
            background: #fff3e0;
            border-radius: 8px;
            border: 2px solid #ff9800;
        }
        
        .indicator-dot {
            width: 20px;
            height: 20px;
            border-radius: 50%;
            background: #ff5722;
            animation: talkPulse 1s infinite;
        }
        
        @keyframes talkPulse {
            0% { opacity: 0.3; transform: scale(0.8); }
            50% { opacity: 1; transform: scale(1.2); }
            100% { opacity: 0.3; transform: scale(0.8); }
        }
        
        .indicator-text {
            font-weight: bold;
            color: #ff5722;
        }
        
        @media (max-width: 600px) {
            body {
                padding: 15px;
            }
            
            .card {
                padding: 15px;
            }
            
            .flex {
                flex-direction: column;
                gap: 8px;
            }
            
            button {
                width: 100%;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <h2>🔒 Секретный Чат с Режимом Рации</h2>
        
        <div class="card">
            <div class="status-container">
                <div id="connection-status" class="disconnected">❌ Не подключено</div>
                <div class="tooltip">ℹ️
                    <span class="tooltiptext">Для подключения обменивайтесь ID с другом</span>
                </div>
            </div>
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
        
        <!-- Обычный звонок с режимом рации -->
        <div class="card">
            <div id="call-status" class="call-ended">Звонок: Неактивен</div>
            
            <!-- Индикатор разговора -->
            <div id="talk-indicator" class="talk-indicator" style="display: none;">
                <div class="indicator-dot"></div>
                <div class="indicator-text">ИДЕТ ПЕРЕДАЧА...</div>
            </div>
            
            <div class="flex">
                <button id="call-button" onclick="startAudioCall()" class="secondary" disabled>📞 Начать звонок</button>
                <button id="talk-button" class="walkie" onmousedown="startTalking()" onmouseup="stopTalking()" ontouchstart="startTalking()" touchend="stopTalking()" disabled>🎤 Говорить</button>
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
        </div>
        
        <div class="card">
            <div id="chat-box"></div>
            <div class="flex">
                <input type="text" id="message-input" placeholder="Введите сообщение..." onkeypress="if(event.key=='Enter')sendMessage()" disabled>
                <button onclick="sendMessage()" class="secondary" id="send-button" disabled>Отправить</button>
            </div>
        </div>
    </div>

    <div class="notification" id="notification"></div>

    <script>
        // Конфигурация
        const config = { 
            iceServers: [{ 
                urls: [
                    'stun:stun.l.google.com:19302',
                    'stun:stun1.l.google.com:19302'
                ] 
            }] 
        };
        
        let peer = null;
        let activeConnection = null;
        let activeCall = null;
        let myPeerId = null;
        let localStream = null;
        let isAudioCallActive = false;
        let isCalling = false;
        let isTalking = false;
        let selectedWalkieKey = ' ';
        let audioTrack = null;

        // Генерация ID
        function generateId() {
            const adj = ['fast', 'happy', 'smart', 'brave', 'bright', 'quiet', 'cool', 'kind'];
            const animals = ['tiger', 'lion', 'eagle', 'wolf', 'bear', 'dolphin', 'fox', 'panda'];
            return `${adj[Math.floor(Math.random()*adj.length)]}-${animals[Math.floor(Math.random()*animals.length)]}-${Math.floor(Math.random()*90+10)}`;
        }

        // Показать уведомление
        function showNotification(message, isSuccess = true) {
            const notification = document.getElementById('notification');
            notification.textContent = message;
            notification.style.background = isSuccess ? '#4CAF50' : '#f44336';
            notification.classList.add('show');
            
            setTimeout(() => {
                notification.classList.remove('show');
            }, 3000);
        }

        // Запуск чата
        function startChat() {
            myPeerId = generateId();
            document.getElementById('my-id').textContent = myPeerId;
            
            peer = new Peer(myPeerId, { config: config });

            peer.on('open', () => {
                addSystemMessage('✅ Чат готов к использованию!');
                document.getElementById('copy-id-button').disabled = false;
                
                // Настройка обработчика клавиш для режима рации
                document.getElementById('walkie-key').addEventListener('change', function() {
                    selectedWalkieKey = this.value;
                    addSystemMessage(`Клавиша для разговора изменена на: ${this.value}`);
                });
                
                // Обработка нажатия клавиш для разговора
                document.addEventListener('keydown', handleKeyDown);
                document.addEventListener('keyup', handleKeyUp);
            });
            
            peer.on('connection', (conn) => {
                if (activeConnection && activeConnection.peer !== conn.peer) {
                    conn.on('open', () => {
                        conn.send({ type: 'system', text: 'У меня уже есть активное соединение' });
                        conn.close();
                    });
                    return;
                }
                
                if (activeConnection) {
                    activeConnection.close();
                }
                
                activeConnection = conn;
                setupConnection(conn);
                updateConnectionStatus(true);
                document.getElementById('call-button').disabled = false;
                document.getElementById('message-input').disabled = false;
                document.getElementById('send-button').disabled = false;
                
                addSystemMessage('✅ ' + conn.peer + ' подключился!');
            });
            
            peer.on('call', (call) => {
                // Обычный звонок
                if (isCalling || isAudioCallActive) {
                    return;
                }
                
                if (confirm('Входящий звонок. Принять?')) {
                    navigator.mediaDevices.getUserMedia({ audio: true, video: false })
                        .then((stream) => {
                            localStream = stream;
                            audioTrack = stream.getAudioTracks()[0];
                            // Изначально выключаем микрофон
                            audioTrack.enabled = false;
                            
                            call.answer(stream);
                            activeCall = call;
                            setupCall(call);
                            updateCallStatus('in-call', 'Звонок: Активен (режим рации)');
                            updateAudioStatus(false); // Микрофон выключен
                            isAudioCallActive = true;
                            document.getElementById('talk-button').disabled = false;
                            document.getElementById('end-call-button').disabled = false;
                            addSystemMessage('📞 Звонок начат (режим рации)');
                        })
                        .catch((err) => {
                            console.error('Ошибка микрофона:', err);
                            addSystemMessage('❌ Не удалось получить доступ к микрофону');
                            updateCallStatus('call-ended', 'Ошибка микрофона');
                        });
                }
            });
            
            peer.on('error', (err) => {
                console.error('Ошибка Peer:', err);
                addSystemMessage('❌ Ошибка соединения: ' + err.type);
            });
        }

        // Обработка нажатия клавиш для разговора
        function handleKeyDown(event) {
            if (event.key === selectedWalkieKey && isAudioCallActive && !isTalking) {
                event.preventDefault();
                startTalking();
            }
        }

        function handleKeyUp(event) {
            if (event.key === selectedWalkieKey && isTalking) {
                event.preventDefault();
                stopTalking();
            }
        }

        // Начать говорить (включить микрофон)
        function startTalking() {
            if (!isAudioCallActive || isTalking || !audioTrack) return;
            
            isTalking = true;
            // Включаем микрофон
            audioTrack.enabled = true;
            updateAudioStatus(true);
            updateTalkIndicator(true);
            document.getElementById('talk-button').classList.add('walkie-active');
            
            // Отправляем сигнал другому пользователю, что мы начали говорить
            if (activeConnection) {
                activeConnection.send({ type: 'talking', status: true });
            }
            
            addSystemMessage('🎤 Передача...');
        }

        // Закончить говорить (выключить микрофон)
        function stopTalking() {
            if (!isTalking || !audioTrack) return;
            
            isTalking = false;
            // Выключаем микрофон
            audioTrack.enabled = false;
            updateAudioStatus(false);
            updateTalkIndicator(false);
            document.getElementById('talk-button').classList.remove('walkie-active');
            
            // Отправляем сигнал другому пользователю, что мы закончили говорить
            if (activeConnection) {
                activeConnection.send({ type: 'talking', status: false });
            }
            
            addSystemMessage('🔇 Пауза');
        }

        // Обновить индикатор разговора
        function updateTalkIndicator(talking) {
            const indicator = document.getElementById('talk-indicator');
            if (talking) {
                indicator.style.display = 'flex';
            } else {
                indicator.style.display = 'none';
            }
        }

        // Обновить статус микрофона
        function updateAudioStatus(isActive) {
            document.getElementById('audio-status').textContent = `Микрофон: ${isActive ? '🟢 Вкл' : '🔴 Выкл'}`;
        }

        // Настройка соединения
        function setupConnection(conn) {
            conn.on('data', (data) => {
                if (data.type === 'message') {
                    addMessage(data.text, false);
                } else if (data.type === 'call-request') {
                    handleIncomingCallRequest();
                } else if (data.type === 'system') {
                    addSystemMessage(data.text);
                } else if (data.type === 'talking') {
                    // Получаем статус разговора от другого пользователя
                    updateTalkIndicator(data.status);
                    if (data.status) {
                        addSystemMessage('🔊 ' + conn.peer + ' говорит...');
                    } else {
                        addSystemMessage('🔇 ' + conn.peer + ' закончил говорить');
                    }
                }
            });
            
            conn.on('close', () => {
                activeConnection = null;
                updateConnectionStatus(false);
                document.getElementById('call-button').disabled = true;
                document.getElementById('talk-button').disabled = true;
                document.getElementById('message-input').disabled = true;
                document.getElementById('send-button').disabled = true;
                addSystemMessage('❌ Соединение разорвано');
                if (isAudioCallActive) endAudioCall();
            });
            
            conn.on('error', (err) => {
                console.error('Ошибка соединения:', err);
                addSystemMessage('❌ Ошибка соединения с peer');
            });
        }

        // Подключиться к другу
        function connectToFriend() {
            const friendId = document.getElementById('friend-id').value.trim();
            if (!friendId) {
                showNotification('Введите ID друга!', false);
                return;
            }
            if (activeConnection) {
                showNotification('Уже есть активное соединение!', false);
                return;
            }
            
            const conn = peer.connect(friendId);
            
            conn.on('open', () => {
                activeConnection = conn;
                setupConnection(conn);
                updateConnectionStatus(true);
                document.getElementById('call-button').disabled = false;
                document.getElementById('message-input').disabled = false;
                document.getElementById('send-button').disabled = false;
                addSystemMessage('✅ Подключено к ' + friendId);
                showNotification('Подключение установлено!');
            });
            
            conn.on('error', (err) => {
                console.error('Ошибка подключения:', err);
                addSystemMessage('❌ Не удалось подключиться к ' + friendId);
                showNotification('Не удалось подключиться', false);
                activeConnection = null;
                updateConnectionStatus(false);
            });
        }

        // Начать аудиозвонок с режимом рации
        function startAudioCall() {
            if (!activeConnection) {
                showNotification('Сначала подключитесь к другу!', false);
                return;
            }
            if (isAudioCallActive) {
                showNotification('Звонок уже активен!', false);
                return;
            }
            
            isCalling = true;
            activeConnection.send({ type: 'call-request' });
            updateCallStatus('calling', 'Звонок: Вызов...');
            
            navigator.mediaDevices.getUserMedia({ audio: true, video: false })
                .then((stream) => {
                    localStream = stream;
                    audioTrack = stream.getAudioTracks()[0];
                    // Изначально выключаем микрофон
                    audioTrack.enabled = false;
                    
                    updateAudioStatus(false); // Микрофон выключен
                    const call = peer.call(activeConnection.peer, stream);
                    activeCall = call;
                    setupCall(call);
                    isCalling = false;
                })
                .catch((err) => {
                    console.error('Ошибка микрофона:', err);
                    addSystemMessage('❌ Не удалось получить доступ к микрофону');
                    updateCallStatus('call-ended', 'Ошибка микрофона');
                    isCalling = false;
                    showNotification('Ошибка доступа к микрофону', false);
                });
        }
        
        function handleIncomingCallRequest() {
            if (!isAudioCallActive) {
                showNotification('Входящий звонок');
            }
        }
        
        function setupCall(call) {
            call.on('stream', (remoteStream) => {
                document.getElementById('remote-audio').srcObject = remoteStream;
                isAudioCallActive = true;
                document.getElementById('talk-button').disabled = false;
                document.getElementById('end-call-button').disabled = false;
                updateCallStatus('in-call', 'Звонок: Активен (режим рации)');
                showNotification('Звонок начат! Нажмите кнопку "Говорить" или клавишу для разговора');
                addSystemMessage('📞 Звонок начат. Используйте кнопку "Говорить" для передачи');
            });
            
            call.on('close', () => {
                endAudioCall();
                addSystemMessage('📞 Звонок завершен');
            });
            
            call.on('error', (err) => {
                console.error('Ошибка звонка:', err);
                addSystemMessage('❌ Ошибка звонка');
                endAudioCall();
            });
        }
        
        function endAudioCall() {
            if (isTalking) {
                stopTalking();
            }
            
            if (activeCall) {
                activeCall.close();
                activeCall = null;
            }
            if (localStream) {
                localStream.getTracks().forEach(track => track.stop());
                localStream = null;
                audioTrack = null;
            }
            isAudioCallActive = false;
            isTalking = false;
            document.getElementById('talk-button').disabled = true;
            document.getElementById('end-call-button').disabled = true;
            updateAudioStatus(false);
            updateTalkIndicator(false);
            document.getElementById('talk-button').classList.remove('walkie-active');
            document.getElementById('remote-audio').srcObject = null;
            updateCallStatus('call-ended', 'Звонок: Завершен');
        }
        
        function updateCallStatus(status, message) {
            const el = document.getElementById('call-status');
            el.className = status;
            el.textContent = message;
        }

        // Сообщения
        function sendMessage() {
            const input = document.getElementById('message-input');
            const message = input.value.trim();
            if (!message) return;
            if (!activeConnection) {
                showNotification('Нет активного соединения!', false);
                return;
            }
            
            activeConnection.send({ type: 'message', text: message });
            addMessage(message, true);
            input.value = '';
            input.focus();
        }

        function addMessage(text, isMyMessage) {
            const div = document.createElement('div');
            div.className = 'message ' + (isMyMessage ? 'my-message' : 'their-message');
            div.textContent = text;
            document.getElementById('chat-box').appendChild(div);
            document.getElementById('chat-box').scrollTop = document.getElementById('chat-box').scrollHeight;
        }

        function addSystemMessage(text) {
            const div = document.createElement('div');
            div.className = 'message system-message';
            div.textContent = text;
            document.getElementById('chat-box').appendChild(div);
            document.getElementById('chat-box').scrollTop = document.getElementById('chat-box').scrollHeight;
        }

        function updateConnectionStatus(connected) {
            const el = document.getElementById('connection-status');
            el.className = connected ? 'connected' : 'disconnected';
            el.innerHTML = connected ? '✅ Подключено' : '❌ Не подключено';
        }

        function copyMyId() {
            navigator.clipboard.writeText(myPeerId).then(() => {
                showNotification('ID скопирован в буфер обмена!');
            }).catch(() => {
                prompt('Не удалось скопировать. Скопируйте ID вручную:', myPeerId);
            });
        }

        // Подтверждение при закрытии страницы
        window.addEventListener('beforeunload', (e) => {
            if (activeConnection || isAudioCallActive) {
                e.preventDefault();
                e.returnValue = '';
                return '';
            }
        });

        // Запуск при загрузке
        window.onload = startChat;
    </script>
</body>
</html>
