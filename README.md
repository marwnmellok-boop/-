<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>نادي تاريشت Mobile</title>
    <style>
        :root {
            --primary: #1e3c72;
            --bt-blue: #0082fc;
            --bg-color: #f0f2f5;
            --card-bg: #ffffff;
            --sent-msg: #dcf8c6;
            --read-blue: #34b7f1;
        }

        body, html { 
            height: 100%; margin: 0; padding: 0; 
            background: var(--bg-color); font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
            overflow: hidden;
        }

        /* الشاشة الرئيسية - الشخصيات */
        .home-screen {
            height: 100vh; display: flex; flex-direction: column; align-items: center; justify-content: center; padding: 20px; box-sizing: border-box;
        }

        .char-grid {
            display: grid; grid-template-columns: repeat(4, 1fr); gap: 10px; margin: 20px 0; width: 100%;
        }
        .char-item { 
            background: white; padding: 10px; border-radius: 15px; text-align: center; 
            box-shadow: 0 4px 10px rgba(0,0,0,0.05); transition: 0.3s; border: 2px solid transparent;
        }
        .char-item.active { border-color: var(--primary); background: #f0f7ff; }
        .char-item img { width: 50px; height: 50px; border-radius: 50%; object-fit: cover; }
        .char-item span { display: block; font-size: 11px; margin-top: 5px; font-weight: bold; }

        /* أيقونة الشات العائمة (ستايل جوال) */
        .fab-chat {
            position: fixed; bottom: 30px; left: 30px;
            background: var(--bt-blue); color: white; width: 65px; height: 65px;
            border-radius: 50%; display: flex; justify-content: center; align-items: center;
            font-size: 28px; box-shadow: 0 8px 20px rgba(0, 130, 252, 0.4); z-index: 1000;
        }

        /* نافذة الشات (كامل الشاشة للجوال) */
        .chat-full-screen {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: white; z-index: 2000; display: none; flex-direction: column;
            transform: translateY(100%); transition: transform 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
        }
        .chat-full-screen.open { display: flex; transform: translateY(0); }

        .chat-header { 
            background: var(--primary); color: white; padding: 15px; 
            display: flex; justify-content: space-between; align-items: center; font-weight: bold;
        }

        .msg-list { flex: 1; padding: 15px; overflow-y: auto; background: #e5ddd5; display: flex; flex-direction: column; gap: 10px; }
        .msg { padding: 10px 15px; border-radius: 15px; font-size: 15px; max-width: 85%; position: relative; box-shadow: 0 1px 2px rgba(0,0,0,0.1); }
        .msg.sent { background: var(--sent-msg); align-self: flex-end; border-top-left-radius: 2px; }
        .ticks { font-size: 11px; color: #999; margin-top: 3px; display: block; text-align: left; }
        .ticks.read { color: var(--read-blue); }

        .audio-wave { display: flex; align-items: center; gap: 10px; min-width: 140px; }
        
        .chat-input-bar { 
            padding: 10px; background: #f0f0f0; display: flex; align-items: center; gap: 10px; 
            padding-bottom: env(safe-area-inset-bottom); /* لدعم هواتف آيفون الحديثة */
        }
        .chat-input-bar input { flex: 1; border: none; padding: 12px; border-radius: 25px; outline: none; font-size: 16px; }

        input, textarea { width: 100%; padding: 12px; margin-bottom: 10px; border-radius: 12px; border: 1px solid #ddd; box-sizing: border-box; font-size: 16px; }
        .main-btn { background: var(--primary); color: white; border: none; padding: 15px; border-radius: 15px; width: 100%; font-weight: bold; font-size: 16px; cursor: pointer; }

        @keyframes pulse { 0% { transform: scale(1); } 50% { transform: scale(1.05); } 100% { transform: scale(1); } }
    </style>
</head>
<body>

    <div class="fab-chat" onclick="toggleChat(true)">📡</div>

    <div class="home-screen">
        <h2 style="color:var(--primary); margin:0;">نادي تاريشت</h2>
        <p style="font-size:14px; color:#666;">اختر الشخصية للمراسلة</p>

        <div class="char-grid">
            <div class="char-item active" onclick="selChar(this, 'الفريق')"><img src="https://cdn-icons-png.flaticon.com/512/53/53283.png"><span>الفريق</span></div>
            <div class="char-item" onclick="selChar(this, 'ميسي')"><img src="https://cdn-icons-png.flaticon.com/512/3135/3135715.png"><span>ميسي</span></div>
            <div class="char-item" onclick="selChar(this, 'رونالدو')"><img src="https://cdn-icons-png.flaticon.com/512/3135/3135768.png"><span>رونالدو</span></div>
            <div class="char-item" onclick="selChar(this, 'حكيمي')"><img src="https://cdn-icons-png.flaticon.com/512/3135/3135823.png"><span>حكيمي</span></div>
        </div>

        <input type="text" id="userName" placeholder="الاسم بالكامل">
        <textarea id="userMsg" rows="3" placeholder="اكتب رسالتك للإدارة هنا..."></textarea>
        <button class="main-btn" onclick="sendToEmail()">ارسال عبر البريد الالكتروني</button>
    </div>

    <div class="chat-full-screen" id="chatScreen">
        <div class="chat-header">
            <span onclick="toggleChat(false)" style="font-size:24px;">✕</span>
            <span id="connStatus">رادار البلوتوث</span>
            <span onclick="connectBT()" style="font-size:12px; border:1px solid; padding:4px 10px; border-radius:15px;">إقران 🔗</span>
        </div>

        <div id="msgBox" class="msg-list">
            <div style="text-align:center; font-size:11px; color:#888; margin-bottom:20px;">أهلاً بك في شات تاريشت الآمن. قم بالإقران لبدء التواصل.</div>
        </div>

        <div class="chat-input-bar">
            <span style="font-size:22px; cursor:pointer;" onclick="recordVoice()" id="micBtn">🎤</span>
            <label style="font-size:22px; cursor:pointer;">📷<input type="file" hidden accept="image/*" onchange="sendImg(this)"></label>
            <input type="text" id="chatInp" placeholder="اكتب رسالة...">
            <span style="font-size:22px; color:var(--bt-blue); cursor:pointer;" onclick="sendText()">◀</span>
        </div>
    </div>

    <script>
        let selectedChar = "الفريق";
        let mediaRecorder;
        let audioChunks = [];

        function toggleChat(show) {
            const screen = document.getElementById('chatScreen');
            if(show) { screen.style.display = 'flex'; setTimeout(() => screen.classList.add('open'), 10); }
            else { screen.classList.remove('open'); setTimeout(() => screen.style.display = 'none', 400); }
        }

        function selChar(el, name) {
            document.querySelectorAll('.char-item').forEach(i => i.classList.remove('active'));
            el.classList.add('active');
            selectedChar = name;
        }

        function sendToEmail() {
            const name = document.getElementById('userName').value;
            const msg = document.getElementById('userMsg').value;
            if(!name || !msg) return alert("اكمل البيانات");
            window.location.href = `mailto:marwnmellok@gmail.com?subject=رسالة تاريشت من ${name}&body=الشخصية: ${selectedChar}%0Aالرسالة: ${msg}`;
        }

        async function connectBT() {
            try {
                if (navigator.bluetooth) {
                    const device = await navigator.bluetooth.requestDevice({ acceptAllDevices: true });
                    document.getElementById('connStatus').innerText = device.name;
                    speak("تم الاقتران بنجاح مع " + device.name);
                } else {
                    alert("البلوتوث مدعوم فقط عبر https");
                }
            } catch(e) { console.log(e); }
        }

        function sendText() {
            const inp = document.getElementById('chatInp');
            if(inp.value) { addMsg(inp.value, 'sent'); inp.value = ""; }
        }

        async function recordVoice() {
            const mic = document.getElementById('micBtn');
            if(!mediaRecorder || mediaRecorder.state === "inactive") {
                const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
                mediaRecorder = new MediaRecorder(stream);
                audioChunks = [];
                mediaRecorder.ondataavailable = e => audioChunks.push(e.data);
                mediaRecorder.onstop = () => {
                    const blob = new Blob(audioChunks, { type: 'audio/webm' });
                    const url = URL.createObjectURL(blob);
                    const html = `<div class="audio-wave" onclick="new Audio('${url}').play()"><span>▶️</span> <div style="width:80px; height:4px; background:var(--bt-blue); border-radius:2px;"></div> <small>صوت</small></div>`;
                    addMsg(html, 'sent');
                };
                mediaRecorder.start();
                mic.style.color = "red";
                speak("بدأ التسجيل");
            } else {
                mediaRecorder.stop();
                mic.style.color = "black";
                speak("تم الإرسال");
            }
        }

        function sendImg(input) {
            if(input.files[0]) {
                const reader = new FileReader();
                reader.onload = e => addMsg(`<img src="${e.target.result}" style="width:100%; border-radius:10px;">`, 'sent');
                reader.readAsDataURL(input.files[0]);
            }
        }

        function addMsg(content, type) {
            const box = document.getElementById('msgBox');
            const div = document.createElement('div');
            div.className = `msg ${type}`;
            div.innerHTML = `${content} <span class="ticks">✓✓</span>`;
            box.appendChild(div);
            box.scrollTop = box.scrollHeight;
            
            // نطق الرسالة عند الضغط عليها
            div.onclick = () => { if(typeof content === 'string' && !content.includes('<')) speak(content); };

            setTimeout(() => {
                const ticks = div.querySelector('.ticks');
                if(ticks) ticks.classList.add('read');
            }, 2000);
        }

        function speak(t) {
            const m = new SpeechSynthesisUtterance(t);
            m.lang = "ar-SA";
            window.speechSynthesis.speak(m);
        }
    </script>
</body>
</html>
