<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>نادي تاريشت - النظام الشامل</title>
    <style>
        :root {
            --primary: #1e3c72;
            --bt-blue: #0082fc;
            --bg-color: #f4f7f6;
            --card-bg: #ffffff;
            --text-color: #1a1a1a;
            --sent-msg: #dcf8c6;
        }

        body, html {
            height: 100%; margin: 0; display: flex; justify-content: center;
            align-items: center; background-color: var(--bg-color);
            font-family: 'Segoe UI', Tahoma, sans-serif; color: var(--text-color);
        }

        /* البطاقة الرئيسية */
        .card {
            background: var(--card-bg); padding: 20px; border-radius: 25px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.1); width: 95%; max-width: 420px;
            text-align: center; position: relative;
        }

        /* شبكة الشخصيات */
        .char-grid {
            display: grid; grid-template-columns: repeat(4, 1fr);
            gap: 10px; margin: 15px 0; max-height: 160px; overflow-y: auto;
            padding: 10px; border: 1px solid #eee; border-radius: 15px;
        }
        .char-item { cursor: pointer; padding: 5px; border-radius: 10px; transition: 0.2s; }
        .char-item.active { background: rgba(30, 60, 114, 0.1); border: 1.5px solid var(--primary); }
        .char-item img { width: 45px; height: 45px; border-radius: 50%; object-fit: cover; }
        .char-item span { font-size: 10px; display: block; margin-top: 3px; }

        /* أيقونة الشات العائمة */
        .bt-toggle {
            position: fixed; bottom: 25px; right: 25px;
            background: var(--bt-blue); color: white; width: 60px; height: 60px;
            border-radius: 50%; display: flex; justify-content: center; align-items: center;
            font-size: 24px; cursor: pointer; box-shadow: 0 5px 15px rgba(0,0,0,0.2);
            z-index: 2001;
        }

        /* نافذة الشات */
        .chat-window {
            display: none; position: fixed; bottom: 95px; right: 25px;
            width: 340px; height: 500px; background: white; border-radius: 20px;
            box-shadow: 0 10px 40px rgba(0,0,0,0.2); flex-direction: column; z-index: 2000; overflow: hidden;
        }
        .chat-header { background: var(--bt-blue); color: white; padding: 12px; font-weight: bold; display: flex; justify-content: space-between; }
        
        .chat-body { flex: 1; display: flex; flex-direction: column; background: #e5ddd5; }
        .msg-list { flex: 1; padding: 10px; overflow-y: auto; display: flex; flex-direction: column; gap: 8px; }
        .msg { padding: 8px 12px; border-radius: 12px; font-size: 14px; max-width: 80%; cursor: pointer; }
        .msg.sent { background: var(--sent-msg); align-self: flex-end; }

        .chat-footer { display: flex; gap: 8px; padding: 10px; background: #f0f0f0; align-items: center; }
        .tool-btn { cursor: pointer; font-size: 18px; }
        .rec-active { color: red; animation: blink 1s infinite; }

        input, textarea { width: 100%; padding: 10px; margin-bottom: 10px; border-radius: 10px; border: 1px solid #ddd; box-sizing: border-box; }
        .main-btn { background: var(--primary); color: white; border: none; padding: 12px; border-radius: 12px; width: 100%; cursor: pointer; font-weight: bold; }
        @keyframes blink { 50% { opacity: 0; } }
    </style>
</head>
<body onclick="enableAudio()">

    <div class="bt-toggle" onclick="toggleChat()">📡</div>

    <div class="card">
        <h2 style="color:var(--primary); margin-top:0;">نادي تاريشت</h2>
        <p style="font-size:12px; color:#666;">اختر شخصيتك المفضلة للمراسلة</p>
        
        <div class="char-grid" id="charGrid">
            <div class="char-item active" onclick="selChar(this, 'الفريق')"><img src="https://cdn-icons-png.flaticon.com/512/53/53283.png"><span>الفريق</span></div>
            <div class="char-item" onclick="selChar(this, 'ميسي')"><img src="https://cdn-icons-png.flaticon.com/512/3135/3135715.png"><span>ميسي</span></div>
            <div class="char-item" onclick="selChar(this, 'رونالدو')"><img src="https://cdn-icons-png.flaticon.com/512/3135/3135768.png"><span>رونالدو</span></div>
            <div class="char-item" onclick="selChar(this, 'حكيمي')"><img src="https://cdn-icons-png.flaticon.com/512/3135/3135823.png"><span>حكيمي</span></div>
        </div>

        <input type="text" id="userName" placeholder="الاسم الكامل">
        <textarea id="userMsg" rows="2" placeholder="اكتب رسالتك للإدارة..."></textarea>
        <button class="main-btn" onclick="sendToEmail()">إرسال عبر البريد الإلكتروني</button>
    </div>

    <div class="chat-window" id="chatWin">
        <div class="chat-header">
            <span>شات البلوتوث الذكي</span>
            <span onclick="toggleChat()" style="cursor:pointer">×</span>
        </div>
        <div class="chat-body">
            <div id="msgBox" class="msg-list"></div>
            <div class="chat-footer">
                <label class="tool-btn">📷<input type="file" hidden accept="image/*" onchange="sendImg(this)"></label>
                <span class="tool-btn" id="mic" onclick="recordVoice()">🎤</span>
                <input type="text" id="chatInp" placeholder="اكتب..." style="flex:1; margin:0;">
                <span class="tool-btn" style="color:var(--bt-blue)" onclick="sendText()">◀</span>
            </div>
        </div>
    </div>

    <script>
        let selectedChar = "الفريق";
        let mediaRecorder;
        let audioChunks = [];
        let audioEnabled = false;

        function enableAudio() { if(!audioEnabled) { speak("مرحباً بك في نادي تاريشت"); audioEnabled = true; } }

        function toggleChat() {
            const win = document.getElementById('chatWin');
            win.style.display = win.style.display === 'flex' ? 'none' : 'flex';
        }

        function selChar(el, name) {
            document.querySelectorAll('.char-item').forEach(i => i.classList.remove('active'));
            el.classList.add('active');
            selectedChar = name;
        }

        // إرسال البريد الإلكتروني (الخاصية القديمة المستعادة)
        function sendToEmail() {
            const name = document.getElementById('userName').value;
            const msg = document.getElementById('userMsg').value;
            if(!name || !msg) return alert("يرجى ملء البيانات");
            const mailto = `mailto:marwnmellok@gmail.com?subject=رسالة من ${name}&body=الشخصية المختارة: ${selectedChar}%0Aالرسالة: ${msg}`;
            window.location.href = mailto;
        }

        // إرسال نص في الشات
        function sendText() {
            const inp = document.getElementById('chatInp');
            if(inp.value) { addMsg(inp.value, 'sent'); inp.value = ""; }
        }

        // إرسال صورة حقيقية
        function sendImg(input) {
            if(input.files[0]) {
                const reader = new FileReader();
                reader.onload = e => addMsg(`<img src="${e.target.result}" style="width:100%; border-radius:8px;">`, 'sent');
                reader.readAsDataURL(input.files[0]);
            }
        }

        // تسجيل صوت حقيقي
        async function recordVoice() {
            const mic = document.getElementById('mic');
            if(!mediaRecorder || mediaRecorder.state === "inactive") {
                const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
                mediaRecorder = new MediaRecorder(stream);
                audioChunks = [];
                mediaRecorder.ondataavailable = e => audioChunks.push(e.data);
                mediaRecorder.onstop = () => {
                    const blob = new Blob(audioChunks, { type: 'audio/wav' });
                    const url = URL.createObjectURL(blob);
                    addMsg(`<audio src="${url}" controls style="width:100%;"></audio>`, 'sent');
                };
                mediaRecorder.start();
                mic.classList.add('rec-active');
                speak("بدأ التسجيل الآن");
            } else {
                mediaRecorder.stop();
                mic.classList.remove('rec-active');
                speak("تم إرسال التسجيل");
            }
        }

        // إضافة رسالة للشات مع ميزة النطق عند النقر
        function addMsg(content, type) {
            const box = document.getElementById('msgBox');
            const div = document.createElement('div');
            div.className = `msg ${type}`;
            div.innerHTML = content;
            
            // جعل الرسالة تنطق عند النقر عليها (لسماع الرسائل)
            div.onclick = () => { if(typeof content === 'string' && !content.includes('<')) speak(content); };
            
            box.appendChild(div);
            box.scrollTop = box.scrollHeight;
        }

        function speak(text) {
            window.speechSynthesis.cancel();
            const m = new SpeechSynthesisUtterance(text);
            m.lang = "ar-SA";
            window.speechSynthesis.speak(m);
        }
    </script>
</body>
</html>
