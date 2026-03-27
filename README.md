<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>نادي تاريشت - النسخة الذكية المتطورة</title>
    <style>
        :root {
            --primary: #1e3c72;
            --accent: #ffd700;
            --white: #ffffff;
            --heart: #e74c3c;
            --bg-color: #f8f9fa;
            --text-color: #1a1a1a;
            --card-bg: #ffffff;
            --bt-blue: #0082fc;
        }

        .dark-mode {
            --bg-color: #121212;
            --text-color: #e0e0e0;
            --card-bg: #1e1e1e;
            --white: #2d2d2d;
        }

        body, html {
            height: 100%; margin: 0; display: flex; justify-content: center;
            align-items: center; background-color: var(--bg-color);
            font-family: 'Segoe UI', system-ui, sans-serif;
            transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
            color: var(--text-color);
        }

        /* --- شريط الإعدادات --- */
        .settings-bar { position: absolute; top: 20px; right: 20px; z-index: 1000; }
        .settings-icon { 
            font-size: 26px; cursor: pointer; transition: 0.5s; 
            background: var(--card-bg); padding: 10px; border-radius: 50%;
            box-shadow: 0 4px 10px rgba(0,0,0,0.1);
        }
        
        /* --- البطاقة الأساسية --- */
        .card {
            background: var(--card-bg); padding: 25px; border-radius: 30px;
            box-shadow: 0 20px 60px rgba(0,0,0,0.15); width: 90%;
            max-width: 420px; text-align: center; position: relative;
        }

        /* --- أيقونة شات البلوتوث العائمة --- */
        .bt-hub-btn {
            position: fixed; bottom: 30px; right: 30px;
            background: var(--bt-blue); color: white; width: 65px; height: 65px;
            border-radius: 50%; display: flex; justify-content: center; align-items: center;
            font-size: 28px; cursor: pointer; box-shadow: 0 10px 20px rgba(0, 130, 252, 0.4);
            z-index: 2001; transition: 0.3s;
        }
        .bt-hub-btn:hover { transform: scale(1.1) rotate(15deg); }

        /* --- نافذة شات البلوتوث --- */
        .bt-window {
            display: none; position: fixed; bottom: 110px; right: 30px;
            width: 340px; height: 500px; background: var(--card-bg); border-radius: 20px;
            box-shadow: 0 15px 40px rgba(0,0,0,0.2); flex-direction: column; overflow: hidden; z-index: 2000;
        }
        .bt-header { background: var(--bt-blue); color: white; padding: 15px; font-weight: bold; display: flex; justify-content: space-between; }
        
        /* --- الرادار وقائمة المستخدمين القريبين --- */
        .radar-box { background: rgba(0, 130, 252, 0.05); padding: 10px; border-bottom: 1px solid #eee; }
        .user-item { 
            display: flex; align-items: center; justify-content: space-between; 
            background: var(--white); margin: 5px 0; padding: 8px 12px; border-radius: 12px;
            font-size: 13px; border: 1px solid #f0f0f0;
        }
        .connect-btn { background: var(--bt-blue); color: white; border: none; padding: 4px 10px; border-radius: 8px; cursor: pointer; font-size: 11px; }

        /* --- واجهة الدردشة الفعالة --- */
        .chat-active { flex: 1; display: none; flex-direction: column; background: #f9f9f9; }
        .msg-container { flex: 1; padding: 10px; overflow-y: auto; }
        .msg { background: white; padding: 8px 12px; border-radius: 12px; margin-bottom: 8px; width: fit-content; max-width: 80%; box-shadow: 0 2px 4px rgba(0,0,0,0.05); }

        /* --- المكونات التقليدية --- */
        input, textarea { width: 100%; padding: 12px; margin-bottom: 12px; border: 1.5px solid #eee; border-radius: 12px; box-sizing: border-box; background: var(--bg-color); color: var(--text-color); }
        .main-btn { background: var(--primary); color: white; border: none; padding: 15px; border-radius: 15px; width: 100%; font-weight: bold; cursor: pointer; }
        .ai-bot-btn { position: fixed; bottom: 30px; left: 30px; background: linear-gradient(135deg, var(--primary), #4a90e2); color: white; width: 65px; height: 65px; border-radius: 50%; display: flex; justify-content: center; align-items: center; font-size: 30px; cursor: pointer; z-index: 2001; }
    </style>
</head>
<body onclick="initVoice()">

    <div class="bt-hub-btn" onclick="toggleBtWindow()">📡</div>

    <div class="bt-window" id="btWindow">
        <div class="bt-header">
            <span>رادار تاريشت (Bluetooth)</span>
            <span onclick="toggleBtWindow()" style="cursor:pointer">×</span>
        </div>

        <div id="btLogin" style="padding: 20px; text-align: center;">
            <p style="font-size: 14px;">أدخل اسمك لتفعيل رادار البلوتوث</p>
            <input type="text" id="btNameInput" placeholder="اسمك المستعار...">
            <button class="main-btn" style="background: var(--bt-blue);" onclick="activateBluetooth()">تفعيل رادار البلوتوث</button>
        </div>

        <div id="btRadar" style="display: none; flex-direction: column; height: 100%;">
            <div class="radar-box">
                <div style="font-size: 11px; color: #666; margin-bottom: 5px;">جاري البحث عن أجهزة قريبة...</div>
                <div id="nearbyUsersList">
                    </div>
            </div>
            <div id="btPlaceholder" style="flex:1; display:flex; align-items:center; justify-content:center; color:#999; font-size:13px; text-align:center; padding:20px;">
                اضغط على "طلب مراسلة" لبدء دردشة بلوتوث مشفرة مع الأصدقاء القريبين
            </div>
            
            <div id="activeChatArea" class="chat-active">
                <div id="btMessages" class="msg-container"></div>
                <div style="padding: 10px; display: flex; gap: 5px; background: white;">
                    <input type="text" id="btMsgInput" placeholder="اكتب رسالة..." style="margin:0;">
                    <button class="main-btn" style="width: 50px; padding:0; background: var(--bt-blue);" onclick="sendBtMsg()">◀</button>
                </div>
            </div>
        </div>
    </div>

    <div class="card">
        <div class="friend-request" style="background:var(--primary); color:white; padding:10px; border-radius:50px; cursor:pointer; margin-bottom:15px;" onclick="requestFriendship()">👤 طلب صداقة مع الفريق</div>
        <div class="char-grid">
            <div class="char-item active"><img src="https://cdn-icons-png.flaticon.com/512/53/53283.png"><br><span>الفريق</span></div>
        </div>
        <input type="text" id="mainName" placeholder="الاسم الكامل">
        <textarea id="mainMsg" rows="2" placeholder="رسالتك للإدارة..."></textarea>
        <button class="main-btn" onclick="handleSend()">إرسال الرسالة</button>
    </div>

    <div class="ai-bot-btn" onclick="toggleAI()">🤖</div>

    <script>
        let currentUser = "";
        let connectedWith = "";

        function toggleBtWindow() {
            const win = document.getElementById('btWindow');
            win.style.display = win.style.display === 'flex' ? 'none' : 'flex';
        }

        async function activateBluetooth() {
            const name = document.getElementById('btNameInput').value;
            if(!name) return alert("يرجى إدخال اسمك أولاً");
            
            currentUser = name;
            
            try {
                // طلب تفعيل البلوتوث (Web Bluetooth API)
                if (navigator.bluetooth) {
                    await navigator.bluetooth.requestDevice({ acceptAllDevices: true });
                }
                
                document.getElementById('btLogin').style.display = 'none';
                document.getElementById('btRadar').style.display = 'flex';
                
                speak(`تم تفعيل الرادار يا ${name}. جاري البحث عن مستخدمين.`);
                simulateNearbyUsers();
            } catch (e) {
                alert("تنبيه: محاكاة الرادار ستعمل لأن المتصفح يحتاج موافقة يدوية.");
                document.getElementById('btLogin').style.display = 'none';
                document.getElementById('btRadar').style.display = 'flex';
                simulateNearbyUsers();
            }
        }

        function simulateNearbyUsers() {
            const users = [
                { name: "ياسين (Galaxy)", dist: "2m" },
                { name: "هاتف مجهول", dist: "5m" }
            ];
            const list = document.getElementById('nearbyUsersList');
            list.innerHTML = "";
            users.forEach(u => {
                list.innerHTML += `
                    <div class="user-item">
                        <span><b>${u.name}</b> (${u.dist})</span>
                        <button class="connect-btn" onclick="sendChatRequest('${u.name}')">طلب مراسلة</button>
                    </div>
                `;
            });
        }

        function sendChatRequest(targetName) {
            alert(`تم إرسال طلب مراسلة إلى ${targetName}. في انتظار القبول عبر البلوتوث...`);
            
            // محاكاة قبول الطلب بعد ثانيتين
            setTimeout(() => {
                if(confirm(`وافق ${targetName} على طلب المراسلة. هل تريد البدء؟`)) {
                    startActiveChat(targetName);
                }
            }, 2000);
        }

        function startActiveChat(target) {
            connectedWith = target;
            document.getElementById('btPlaceholder').style.display = 'none';
            document.getElementById('activeChatArea').style.display = 'flex';
            document.querySelector('.radar-box').innerHTML = `<div style="text-align:center; font-size:12px; color:green;">● متصل الآن مع: ${target}</div>`;
            speak(`أنت الآن متصل مع ${target} عبر شات البلوتوث`);
        }

        function sendBtMsg() {
            const input = document.getElementById('btMsgInput');
            const box = document.getElementById('btMessages');
            if(!input.value) return;

            box.innerHTML += `<div class="msg" style="margin-right: auto; background: var(--bt-blue); color: white;">${input.value}</div>`;
            input.value = "";
            box.scrollTop = box.scrollHeight;
        }

        // --- وظائف المساعد الصوتي والـ AI ---
        let voiceEnabled = false;
        function initVoice() {
            if(!voiceEnabled) {
                speak("مرحباً بك في نادي تاريشت المطور. رادار البلوتوث جاهز للعمل.");
                voiceEnabled = true;
            }
        }

        function speak(text) {
            window.speechSynthesis.cancel();
            const msg = new SpeechSynthesisUtterance(text);
            msg.lang = "ar-SA";
            window.speechSynthesis.speak(msg);
        }

        function handleSend() {
            const n = document.getElementById('mainName').value;
            const m = document.getElementById('mainMsg').value;
            if(!n || !m) return alert("اكمل البيانات");
            window.location.href = `mailto:marwnmellok@gmail.com?subject=رسالة من ${n}&body=${m}`;
        }
    </script>
</body>
</html>
