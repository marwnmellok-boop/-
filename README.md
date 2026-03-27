<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>نادي تاريشت - النظام الاحترافي المتكامل</title>
    <style>
        :root {
            --primary: #1e3c72;
            --bt-blue: #0082fc;
            --bg-color: #f0f2f5;
            --card-bg: #ffffff;
            --text-color: #1a1a1a;
            --sent-msg: #dcf8c6;
            --danger: #ff4757;
        }

        body, html {
            height: 100%; margin: 0; display: flex; justify-content: center;
            align-items: center; background-color: var(--bg-color);
            font-family: 'Segoe UI', Tahoma, sans-serif; color: var(--text-color);
        }

        /* شاشة الترحيب */
        #welcomeOverlay {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: linear-gradient(45deg, var(--primary), #2a5298);
            color: white; z-index: 9999; display: flex; flex-direction: column;
            align-items: center; justify-content: center; transition: 1s ease;
        }

        /* البطاقة الرئيسية */
        .card {
            background: var(--card-bg); padding: 25px; border-radius: 25px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.1); width: 90%; max-width: 400px;
            text-align: center; border: 1px solid #ddd;
        }

        /* أيقونة البلوتوث العائمة */
        .bt-float-btn {
            position: fixed; bottom: 25px; right: 25px;
            background: var(--bt-blue); color: white; width: 60px; height: 60px;
            border-radius: 50%; display: flex; justify-content: center; align-items: center;
            font-size: 24px; cursor: pointer; box-shadow: 0 5px 15px rgba(0,0,0,0.3);
            z-index: 1000; transition: 0.3s;
        }

        /* نافذة الشات */
        .bt-window {
            display: none; position: fixed; bottom: 95px; right: 25px;
            width: 340px; height: 500px; background: var(--card-bg); border-radius: 20px;
            box-shadow: 0 10px 40px rgba(0,0,0,0.2); flex-direction: column; z-index: 2000; overflow: hidden;
        }

        .bt-header { 
            background: var(--bt-blue); color: white; padding: 12px; 
            display: flex; justify-content: space-between; align-items: center; font-weight: bold;
        }

        .chat-area { flex: 1; display: none; flex-direction: column; background: #e5ddd5; position: relative; }
        .msg-container { flex: 1; padding: 10px; overflow-y: auto; display: flex; flex-direction: column; gap: 8px; }
        .msg { padding: 8px 12px; border-radius: 12px; max-width: 80%; font-size: 14px; position: relative; }
        .msg.sent { background: var(--sent-msg); align-self: flex-end; }
        .msg.received { background: white; align-self: flex-start; }

        /* شريط الأدوات */
        .chat-tools { display: flex; gap: 10px; padding: 10px; background: #f0f0f0; align-items: center; border-top: 1px solid #ddd; }
        .tool-btn { cursor: pointer; font-size: 18px; color: #555; }
        .recording { color: var(--danger); animation: blink 1s infinite; }

        /* قائمة الإيموجي */
        .emoji-picker {
            display: none; position: absolute; bottom: 60px; left: 10px;
            background: white; border: 1px solid #ddd; padding: 10px;
            border-radius: 10px; grid-template-columns: repeat(5, 1fr); gap: 5px; box-shadow: 0 5px 15px rgba(0,0,0,0.1);
        }

        .main-btn { background: var(--primary); color: white; border: none; padding: 12px; border-radius: 12px; width: 100%; cursor: pointer; font-weight: bold; }
        @keyframes blink { 50% { opacity: 0; } }
    </style>
</head>
<body onclick="startAudioContext()">

    <div id="welcomeOverlay">
        <h1>نادي تاريشت</h1>
        <p>مرحباً بك في الجيل الجديد من التواصل</p>
        <button class="main-btn" style="width: 180px; background: white; color: var(--primary);" onclick="closeWelcome()">دخول</button>
    </div>

    <div class="bt-float-btn" onclick="toggleChat()">📡</div>

    <div class="card">
        <h3>تخصيص الهوية</h3>
        <input type="text" id="userName" placeholder="أدخل اسمك المستعار..." style="width:100%; padding:10px; margin-bottom:10px; border-radius:8px; border:1px solid #ccc;">
        <button class="main-btn" onclick="saveUser()">حفظ البيانات</button>
    </div>

    <div class="bt-window" id="btWindow">
        <div class="bt-header">
            <span id="btStatus">البلوتوث: غير متصل</span>
            <span onclick="toggleChat()" style="cursor:pointer">×</span>
        </div>

        <div id="pairingArea" style="padding: 40px 20px; text-align: center;">
            <p>للحصول على IP افتراضي، يرجى الاقتران:</p>
            <button class="main-btn" style="background: var(--bt-blue);" onclick="connectBluetooth()">بدء الاقتران والربط</button>
        </div>

        <div id="chatInterface" class="chat-area">
            <div style="background:#fff3cd; padding:5px; font-size:10px; text-align:center;">إشعار: نظام الحظر مفعل لهذا الاتصال</div>
            <div id="msgBox" class="msg-container"></div>
            
            <div class="emoji-picker" id="emojiPicker">
                <span onclick="addEmoji('😊')">😊</span><span onclick="addEmoji('😂')">😂</span>
                <span onclick="addEmoji('🔥')">🔥</span><span onclick="addEmoji('⚽')">⚽</span>
                <span onclick="addEmoji('❤️')">❤️</span><span onclick="addEmoji('👍')">👍</span>
            </div>

            <div class="chat-tools">
                <span class="tool-btn" onclick="toggleEmoji()">😀</span>
                <label class="tool-btn">📷<input type="file" hidden accept="image/*" onchange="sendImage(this)"></label>
                <span class="tool-btn" id="mic" onclick="handleVoice()">🎤</span>
                <input type="text" id="msgInput" placeholder="اكتب..." style="flex:1; border:none; padding:5px; border-radius:5px;">
                <span class="tool-btn" style="color:var(--bt-blue)" onclick="sendText()">◀</span>
            </div>
            <div style="text-align: center; padding: 5px; background: #eee;">
                <span style="color:var(--danger); font-size:11px; cursor:pointer;" onclick="blockCurrentDevice()">🚫 حظر هذا الجهاز</span>
            </div>
        </div>
    </div>

    <script>
        let mediaRecorder;
        let audioChunks = [];
        let isBlocked = false;

        function closeWelcome() {
            document.getElementById('welcomeOverlay').style.transform = 'translateY(-100%)';
            speak("مرحباً بك في نادي تاريشت. نظام البلوتوث جاهز.");
        }

        async function connectBluetooth() {
            try {
                if (navigator.bluetooth) {
                    await navigator.bluetooth.requestDevice({ acceptAllDevices: true });
                }
                const ip = `10.0.0.${Math.floor(Math.random() * 254) + 1}`;
                document.getElementById('btStatus').innerText = `متصل [IP: ${ip}]`;
                document.getElementById('pairingArea').style.display = 'none';
                document.getElementById('chatInterface').style.display = 'flex';
                speak("تم ربط الجهاز وتخصيص عنوان أي بي افتراضي.");
                loadChat();
            } catch (e) {
                alert("تم تشغيل الوضع التجريبي (لا يوجد دعم بلوتوث).");
                document.getElementById('chatInterface').style.display = 'flex';
                document.getElementById('pairingArea').style.display = 'none';
            }
        }

        function sendText() {
            if(checkBlock()) return;
            const inp = document.getElementById('msgInput');
            if(inp.value) { appendMsg(inp.value, 'sent'); inp.value = ""; }
        }

        function sendImage(input) {
            if(checkBlock()) return;
            if(input.files[0]) {
                const reader = new FileReader();
                reader.onload = e => appendMsg(`<img src="${e.target.result}" style="width:100%; border-radius:8px;">`, 'sent');
                reader.readAsDataURL(input.files[0]);
            }
        }

        async function handleVoice() {
            if(checkBlock()) return;
            const mic = document.getElementById('mic');
            if(!mediaRecorder || mediaRecorder.state === "inactive") {
                const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
                mediaRecorder = new MediaRecorder(stream);
                audioChunks = [];
                mediaRecorder.ondataavailable = e => audioChunks.push(e.data);
                mediaRecorder.onstop = () => {
                    const blob = new Blob(audioChunks, { type: 'audio/wav' });
                    appendMsg(`<audio src="${URL.createObjectURL(blob)}" controls style="width:100%;"></audio>`, 'sent');
                };
                mediaRecorder.start();
                mic.classList.add('recording');
            } else {
                mediaRecorder.stop();
                mic.classList.remove('recording');
            }
        }

        function blockCurrentDevice() {
            if(confirm("هل تريد حظر هذا الجهاز من المراسلة نهائياً؟")) {
                localStorage.setItem('tariشت_blocked', 'true');
                alert("تم حظر الجهاز.");
                location.reload();
            }
        }

        function checkBlock() {
            if(localStorage.getItem('tariشت_blocked') === 'true') {
                alert("عذراً، هذا الجهاز محظور من استخدام الشات.");
                return true;
            }
            return false;
        }

        function appendMsg(content, type) {
            const box = document.getElementById('msgBox');
            const html = `<div class="msg ${type}">${content}</div>`;
            box.innerHTML += html;
            box.scrollTop = box.scrollHeight;
            saveChat(content, type);
        }

        function toggleChat() {
            const win = document.getElementById('btWindow');
            win.style.display = win.style.display === 'flex' ? 'none' : 'flex';
        }

        function toggleEmoji() {
            const p = document.getElementById('emojiPicker');
            p.style.display = p.style.display === 'grid' ? 'none' : 'grid';
        }

        function addEmoji(e) {
            document.getElementById('msgInput').value += e;
            toggleEmoji();
        }

        function saveChat(c, t) {
            let h = JSON.parse(localStorage.getItem('tariشت_history')) || [];
            h.push({c, t});
            localStorage.setItem('tariشت_history', JSON.stringify(h));
        }

        function loadChat() {
            let h = JSON.parse(localStorage.getItem('tariشت_history')) || [];
            h.forEach(m => {
                document.getElementById('msgBox').innerHTML += `<div class="msg ${m.t}">${m.c}</div>`;
            });
        }

        function speak(t) {
            const m = new SpeechSynthesisUtterance(t);
            m.lang = "ar-SA";
            window.speechSynthesis.speak(m);
        }

        function saveUser() {
            const n = document.getElementById('userName').value;
            if(n) { alert("تم حفظ المستخدم: " + n); speak("أهلاً بك يا " + n); }
        }

        function startAudioContext() {
            // لتفعيل الصوت في المتصفحات الحديثة
            if (window.AudioContext) new AudioContext();
        }
    </script>
</body>
</html>
