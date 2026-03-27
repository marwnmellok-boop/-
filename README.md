<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>نادي تاريشت - النسخة الاجتماعية المتطورة</title>
    <style>
        :root {
            --primary: #1e3c72;
            --bt-blue: #0082fc;
            --bg-color: #f8f9fa;
            --card-bg: #ffffff;
            --text-color: #1a1a1a;
            --danger: #e74c3c;
        }

        .dark-mode {
            --bg-color: #121212;
            --card-bg: #1e1e1e;
            --text-color: #e0e0e0;
        }

        body, html {
            height: 100%; margin: 0; display: flex; justify-content: center;
            align-items: center; background-color: var(--bg-color);
            font-family: 'Segoe UI', system-ui, sans-serif;
            color: var(--text-color); transition: 0.4s;
        }

        /* المساعد الآلي - أعلى اليسار */
        .ai-bot-btn {
            position: fixed; top: 20px; left: 20px;
            background: linear-gradient(135deg, var(--primary), #4a90e2);
            color: white; width: 55px; height: 55px;
            border-radius: 50%; display: flex; justify-content: center; align-items: center;
            font-size: 25px; cursor: pointer; box-shadow: 0 5px 15px rgba(0,0,0,0.2);
            z-index: 3000; animation: pulse 2s infinite;
        }

        /* شبكة الشخصيات الموسعة */
        .char-grid {
            display: grid; grid-template-columns: repeat(4, 1fr);
            gap: 10px; margin: 15px 0; max-height: 200px; overflow-y: auto;
            padding: 10px; border: 1px solid rgba(0,0,0,0.1); border-radius: 15px;
        }
        .char-item { cursor: pointer; padding: 5px; border-radius: 10px; transition: 0.2s; text-align: center; }
        .char-item.active { background: rgba(30, 60, 114, 0.15); border: 1px solid var(--primary); }
        .char-item img { width: 45px; height: 45px; border-radius: 50%; object-fit: cover; }
        .char-item span { font-size: 10px; display: block; margin-top: 3px; }

        .card {
            background: var(--card-bg); padding: 20px; border-radius: 25px;
            box-shadow: 0 15px 50px rgba(0,0,0,0.1); width: 90%;
            max-width: 400px; position: relative;
        }

        /* أيقونة البلوتوث - أسفل اليمين */
        .bt-hub-btn {
            position: fixed; bottom: 30px; right: 30px;
            background: var(--bt-blue); color: white; width: 65px; height: 65px;
            border-radius: 50%; display: flex; justify-content: center; align-items: center;
            font-size: 28px; cursor: pointer; box-shadow: 0 8px 20px rgba(0, 130, 252, 0.4);
            z-index: 2001;
        }

        /* نافذة الشات المتقدمة */
        .bt-window {
            display: none; position: fixed; bottom: 110px; right: 30px;
            width: 350px; height: 550px; background: var(--card-bg); border-radius: 20px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.2); flex-direction: column; z-index: 2000; overflow: hidden;
        }
        .bt-header { background: var(--bt-blue); color: white; padding: 12px; font-weight: bold; display: flex; justify-content: space-between; align-items: center; }
        
        .chat-active { flex: 1; display: none; flex-direction: column; background: #f0f2f5; }
        .msg-container { flex: 1; padding: 10px; overflow-y: auto; display: flex; flex-direction: column; gap: 8px; }
        .msg { padding: 8px 12px; border-radius: 15px; font-size: 14px; max-width: 80%; position: relative; }
        .msg.sent { background: var(--bt-blue); color: white; align-self: flex-end; border-bottom-right-radius: 2px; }
        .msg.received { background: white; color: black; align-self: flex-start; border-bottom-left-radius: 2px; }

        /* أدوات الشات (صور، صوت، حظر) */
        .chat-tools { display: flex; gap: 10px; padding: 10px; background: white; border-top: 1px solid #eee; align-items: center; }
        .tool-btn { cursor: pointer; font-size: 18px; color: #555; transition: 0.2s; }
        .tool-btn:hover { color: var(--bt-blue); }
        .block-btn { color: var(--danger); font-size: 12px; font-weight: bold; cursor: pointer; }

        .main-btn { background: var(--primary); color: white; border: none; padding: 12px; border-radius: 12px; width: 100%; cursor: pointer; font-weight: bold; }
        @keyframes pulse { 0% { transform: scale(1); } 50% { transform: scale(1.1); } 100% { transform: scale(1); } }
    </style>
</head>
<body onclick="initVoice()">

    <div class="ai-bot-btn" onclick="toggleAI()">🤖</div>

    <div class="bt-hub-btn" onclick="toggleBtWindow()">📡</div>

    <div class="card">
        <button class="main-btn" style="border-radius: 50px; margin-bottom: 10px;" onclick="sendFriendRequest()">👤 طلب صداقة مع الفريق</button>
        
        <div class="char-grid" id="charGrid">
            <div class="char-item active" onclick="selChar(this, 'الفريق')"><img src="https://cdn-icons-png.flaticon.com/512/53/53283.png"><span>الفريق</span></div>
            <div class="char-item" onclick="selChar(this, 'ميسي')"><img src="https://cdn-icons-png.flaticon.com/512/3135/3135715.png"><span>ميسي</span></div>
            <div class="char-item" onclick="selChar(this, 'رونالدو')"><img src="https://cdn-icons-png.flaticon.com/512/3135/3135768.png"><span>رونالدو</span></div>
            <div class="char-item" onclick="selChar(this, 'بنزيمة')"><img src="https://cdn-icons-png.flaticon.com/512/3135/3135789.png"><span>بنزيمة</span></div>
            <div class="char-item" onclick="selChar(this, 'حكيمي')"><img src="https://cdn-icons-png.flaticon.com/512/3135/3135823.png"><span>حكيمي</span></div>
            <div class="char-item" onclick="selChar(this, 'مبابي')"><img src="https://cdn-icons-png.flaticon.com/512/3135/3135755.png"><span>مبابي</span></div>
            <div class="char-item" onclick="selChar(this, 'صلاح')"><img src="https://cdn-icons-png.flaticon.com/512/3135/3135707.png"><span>صلاح</span></div>
            <div class="char-item" onclick="selChar(this, 'نيمار')"><img src="https://cdn-icons-png.flaticon.com/512/3135/3135764.png"><span>نيمار</span></div>
        </div>

        <input type="text" id="mainName" placeholder="الاسم الكامل">
        <textarea id="mainMsg" rows="2" placeholder="رسالة سريعة..."></textarea>
        <button class="main-btn" onclick="handleMainSend()">إرسال للإدارة</button>
    </div>

    <div class="bt-window" id="btWindow">
        <div class="bt-header">
            <span>رادار تاريشت الذكي</span>
            <span onclick="toggleBtWindow()" style="cursor:pointer">×</span>
        </div>

        <div id="btLogin" style="padding: 20px; text-align: center;">
            <input type="text" id="btNameInput" placeholder="اسمك للدخول...">
            <button class="main-btn" style="background: var(--bt-blue);" onclick="activateBt()">تفعيل الرادار</button>
        </div>

        <div id="btRadar" style="display: none; flex-direction: column; height: 100%;">
            <div style="background:#f0f7ff; padding:10px; font-size:12px;" id="nearbyList">جاري البحث عن أجهزة...</div>
            
            <div id="activeChat" class="chat-active">
                <div style="padding: 5px 10px; background: #eee; display: flex; justify-content: space-between; align-items: center;">
                    <span id="chatTitle" style="font-size: 12px; font-weight: bold;"></span>
                    <span class="block-btn" onclick="blockUser()">🚫 حظر الجهاز</span>
                </div>
                <div id="msgBox" class="msg-container"></div>
                
                <div class="chat-tools">
                    <label class="tool-btn">📷<input type="file" hidden accept="image/*" onchange="sendImage(this)"></label>
                    <span class="tool-btn" onclick="sendVoiceNote()">🎤</span>
                    <input type="text" id="btMsgInput" placeholder="اكتب..." style="flex:1; margin:0; padding:8px;">
                    <span class="tool-btn" onclick="sendText()">◀</span>
                </div>
            </div>
        </div>
    </div>

    <script>
        let selectedChar = "الفريق";
        let isBlocked = false;

        function toggleBtWindow() {
            const w = document.getElementById('btWindow');
            w.style.display = w.style.display === 'flex' ? 'none' : 'flex';
        }

        function activateBt() {
            const name = document.getElementById('btNameInput').value;
            if(!name) return;
            document.getElementById('btLogin').style.display = 'none';
            document.getElementById('btRadar').style.display = 'flex';
            
            // محاكاة رادار
            setTimeout(() => {
                document.getElementById('nearbyList').innerHTML = `
                    <div style="display:flex; justify-content:space-between; align-items:center; background:white; padding:8px; border-radius:10px;">
                        <span>📱 جهاز ياسين (قريب جداً)</span>
                        <button onclick="requestChat('ياسين')" style="background:var(--bt-blue); color:white; border:none; padding:5px 10px; border-radius:8px; cursor:pointer;">طلب مراسلة</button>
                    </div>`;
            }, 1500);
        }

        function requestChat(target) {
            alert(`تم إرسال طلب مراسلة إلى ${target}...`);
            setTimeout(() => {
                if(confirm(`وافق ${target} على طلبك. ابدأ الدردشة الآن؟`)) {
                    document.getElementById('nearbyList').style.display = 'none';
                    document.getElementById('activeChat').style.display = 'flex';
                    document.getElementById('chatTitle').innerText = "متصل مع: " + target;
                    speak("تم الاتصال بنجاح. يمكنك إرسال الصور والصوت الآن.");
                }
            }, 1500);
        }

        function sendText() {
            if(isBlocked) return alert("لقد قمت بحظر هذا الجهاز!");
            const input = document.getElementById('btMsgInput');
            if(!input.value) return;
            addMsg(input.value, 'sent');
            input.value = "";
        }

        function sendImage(input) {
            if(input.files && input.files[0]) {
                addMsg(`<img src="${URL.createObjectURL(input.files[0])}" style="width:100px; border-radius:10px;">`, 'sent');
            }
        }

        function sendVoiceNote() {
            addMsg("🎤 رسالة صوتية (0:05)", 'sent');
            speak("جاري تسجيل وإرسال الصوت عبر البلوتوث");
        }

        function addMsg(content, type) {
            const box = document.getElementById('msgBox');
            box.innerHTML += `<div class="msg ${type}">${content}</div>`;
            box.scrollTop = box.scrollHeight;
        }

        function blockUser() {
            if(confirm("هل تريد حظر هذا الجهاز نهائياً؟ لن تتمكن من مراسلته مجدداً.")) {
                isBlocked = true;
                document.getElementById('activeChat').innerHTML = "<div style='padding:50px; text-align:center;'>تم حظر هذا المستخدم والجهاز.</div>";
                speak("تم حظر المستخدم بنجاح.");
            }
        }

        function sendFriendRequest() {
            const name = document.getElementById('mainName').value || "زائر";
            window.location.href = `mailto:marwnmellok@gmail.com?subject=طلب صداقة&body=الاسم: ${name}`;
            speak("تم تجهيز طلب الصداقة، يرجى إرسال الإيميل.");
        }

        function selChar(el, name) {
            document.querySelectorAll('.char-item').forEach(i => i.classList.remove('active'));
            el.classList.add('active');
            selectedChar = name;
        }

        function initVoice() {
            speak("نادي تاريشت يرحب بك. رادار البلوتوث والمساعد الذكي في خدمتك.");
        }

        function speak(t) {
            window.speechSynthesis.cancel();
            const m = new SpeechSynthesisUtterance(t);
            m.lang = "ar-SA";
            window.speechSynthesis.speak(m);
        }

        function toggleAI() { alert("مساعد تاريشت الذكي جاهز لمساعدتك!"); }

        function handleMainSend() {
            const n = document.getElementById('mainName').value;
            const m = document.getElementById('mainMsg').value;
            if(!n || !m) return alert("اكمل البيانات أولاً");
            window.location.href = `mailto:marwnmellok@gmail.com?subject=رسالة من ${n}&body=الشخصية: ${selectedChar}%0Aالرسالة: ${m}`;
        }
    </script>
</body>
</html>
