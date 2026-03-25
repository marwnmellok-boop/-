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

        /* شريط الإعدادات المتطور */
        .settings-bar { position: absolute; top: 20px; right: 20px; z-index: 1000; }
        .settings-icon { 
            font-size: 26px; cursor: pointer; transition: 0.5s; 
            background: var(--card-bg); padding: 10px; border-radius: 50%;
            box-shadow: 0 4px 10px rgba(0,0,0,0.1);
        }
        .settings-icon:hover { transform: rotate(90deg); }
        
        .settings-menu {
            display: none; position: absolute; top: 55px; right: 0; 
            background: var(--card-bg); border-radius: 15px; 
            box-shadow: 0 10px 25px rgba(0,0,0,0.2); padding: 15px; width: 200px;
        }

        /* زر الوضع المظلم المتطور */
        .theme-switch {
            display: flex; align-items: center; justify-content: space-between;
            cursor: pointer; padding: 10px; border-radius: 10px; background: rgba(0,0,0,0.05);
        }

        .card {
            background: var(--card-bg); padding: 25px; border-radius: 30px;
            box-shadow: 0 20px 60px rgba(0,0,0,0.15); width: 90%;
            max-width: 420px; text-align: center; position: relative;
        }

        .friend-request {
            background: var(--primary); color: white; padding: 10px 20px;
            border-radius: 50px; font-size: 14px; font-weight: 600;
            display: inline-flex; align-items: center; gap: 8px;
            margin-bottom: 20px; cursor: pointer; border: none;
            transition: 0.3s;
        }

        .char-grid {
            display: grid; grid-template-columns: repeat(4, 1fr);
            gap: 12px; margin: 20px 0; max-height: 180px; overflow-y: auto;
            padding: 10px; border: 1px solid rgba(0,0,0,0.1); border-radius: 15px;
        }

        .char-item { cursor: pointer; padding: 8px; border-radius: 12px; transition: 0.3s; border: 2px solid transparent; }
        .char-item.active { border-color: var(--primary); background: rgba(30, 60, 114, 0.1); }
        .char-item img { width: 50px; height: 50px; border-radius: 50%; object-fit: cover; }
        .char-item span { font-size: 11px; display: block; margin-top: 5px; }

        input, textarea {
            width: 100%; padding: 12px; margin-bottom: 12px;
            border: 1.5px solid #eee; border-radius: 12px; box-sizing: border-box;
            background: var(--bg-color); color: var(--text-color);
        }

        .main-btn {
            background: var(--primary); color: white; border: none;
            padding: 15px; border-radius: 15px; width: 100%;
            font-weight: bold; cursor: pointer; font-size: 16px;
        }

        /* واجهة الـ AI المتطورة */
        .ai-bot-btn {
            position: fixed; bottom: 30px; left: 30px;
            background: linear-gradient(135deg, var(--primary), #4a90e2);
            color: white; width: 65px; height: 65px;
            border-radius: 50%; display: flex; justify-content: center; align-items: center;
            font-size: 30px; cursor: pointer; box-shadow: 0 10px 20px rgba(30, 60, 114, 0.3);
            animation: pulse 2s infinite;
        }

        @keyframes pulse { 0% { transform: scale(1); } 50% { transform: scale(1.05); } 100% { transform: scale(1); } }

        .ai-window {
            display: none; position: fixed; bottom: 110px; left: 30px;
            width: 320px; background: var(--card-bg); border-radius: 20px;
            box-shadow: 0 15px 40px rgba(0,0,0,0.2); flex-direction: column; overflow: hidden; z-index: 2000;
        }
        .ai-header { background: var(--primary); color: white; padding: 15px; font-weight: bold; display: flex; justify-content: space-between; }
        .ai-chat { height: 250px; overflow-y: auto; padding: 15px; font-size: 14px; line-height: 1.6; }
        .ai-controls { display: flex; padding: 10px; border-top: 1px solid rgba(0,0,0,0.1); gap: 5px; }
        .ai-controls input { margin-bottom: 0; flex: 1; border-radius: 20px; }
        .mic-btn { background: #e74c3c; color: white; border: none; width: 40px; border-radius: 50%; cursor: pointer; }

        .video-section { margin-top: 25px; border-top: 1px solid rgba(0,0,0,0.1); padding-top: 20px; }
        .video-container { width: 100%; border-radius: 15px; overflow: hidden; aspect-ratio: 16/9; background: #000; }
        .like-area { margin-top: 12px; display: flex; justify-content: center; align-items: center; gap: 8px; cursor: pointer; font-weight: 600; }
        .like-icon { font-size: 22px; color: #ccc; transition: 0.3s; }
        .active .like-icon { color: var(--heart); transform: scale(1.2); }
    </style>
</head>
<body onclick="initVoice()">

    <div class="settings-bar">
        <div class="settings-icon" onclick="toggleSettings()">⚙️</div>
        <div class="settings-menu" id="settingsMenu">
            <div class="theme-switch" onclick="toggleDarkMode()">
                <span>الوضع المظلم</span>
                <span id="themeIcon">🌙</span>
            </div>
        </div>
    </div>

    <div class="card">
        <div class="friend-request" onclick="requestFriendship()">
            👤 <span>أريد صداقة مع الفريق</span>
        </div>

        <div class="char-grid" id="charGrid">
            <div class="char-item active" onclick="selChar(this, 'شعار الفريق')">
                <img src="https://cdn-icons-png.flaticon.com/512/53/53283.png"><span>الفريق</span>
            </div>
            <div class="char-item" onclick="selChar(this, 'ميسي')">
                <img src="https://cdn-icons-png.flaticon.com/512/3135/3135715.png"><span>ميسي</span>
            </div>
            <div class="char-item" onclick="selChar(this, 'رونالدو')">
                <img src="https://cdn-icons-png.flaticon.com/512/3135/3135768.png"><span>رونالدو</span>
            </div>
            <div class="char-item" onclick="selChar(this, 'صلاح')">
                <img src="https://cdn-icons-png.flaticon.com/512/3135/3135707.png"><span>صلاح</span>
            </div>
        </div>

        <input type="text" id="name" placeholder="الاسم الكامل">
        <textarea id="msg" rows="2" placeholder="رسالتك للإدارة..."></textarea>
        
        <button class="main-btn" onclick="handleSend()">إرسال الرسالة</button>

        <div class="video-section">
            <div class="video-container">
                <iframe src="https://www.youtube.com/embed/inhEapj87R8" width="100%" height="100%" frameborder="0"></iframe>
            </div>
            <div class="like-area" id="likeArea" onclick="toggleLike()">
                <span class="like-icon">❤</span>
                <span>أعجبني</span>
            </div>
        </div>
    </div>

    <div class="ai-bot-btn" onclick="toggleAI()">🤖</div>
    <div class="ai-window" id="aiWindow">
        <div class="ai-header">
            <span>مساعد تاريشت الذكي</span>
            <span onclick="toggleAI()" style="cursor:pointer">×</span>
        </div>
        <div class="ai-chat" id="aiChat">
            <div style="background: #eee; padding: 8px; border-radius: 10px; margin-bottom: 10px;">
                مرحباً بك! أنا مساعدك. اضغط على الميكروفون للتحدث معي، أو اسألني: "كيف أستخدم الموقع؟"
            </div>
        </div>
        <div class="ai-controls">
            <input type="text" id="aiInput" placeholder="اكتب سؤالك هنا..." onkeypress="if(event.key==='Enter') askAI()">
            <button class="mic-btn" id="micBtn" onclick="startListen()">🎤</button>
            <button class="main-btn" style="width: 45px; padding:0" onclick="askAI()">◀</button>
        </div>
    </div>

    <script>
        let char = "شعار الفريق";
        let isLiked = false;
        let voiceEnabled = false;

        // تفعيل الصوت عند أول نقرة
        function initVoice() {
            if(!voiceEnabled) {
                speak("مرحباً بك في نادي تاريشت. أنا مساعدك الذكي، كيف أخدمك؟");
                voiceEnabled = true;
            }
        }

        function speak(text) {
            window.speechSynthesis.cancel();
            const msg = new SpeechSynthesisUtterance(text);
            msg.lang = "ar-SA";
            window.speechSynthesis.speak(msg);
        }

        // التعرف على الصوت
        function startListen() {
            const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
            if (!SpeechRecognition) return alert("متصفحك لا يدعم التعرف على الصوت");
            
            const recognition = new SpeechRecognition();
            recognition.lang = 'ar-SA';
            document.getElementById('micBtn').style.background = '#2ecc71';
            
            recognition.onresult = (event) => {
                const transcript = event.results[0][0].transcript;
                document.getElementById('aiInput').value = transcript;
                askAI();
            };
            recognition.onend = () => {
                document.getElementById('micBtn').style.background = '#e74c3c';
            };
            recognition.start();
        }

        function toggleSettings() {
            const m = document.getElementById('settingsMenu');
            m.style.display = m.style.display === 'block' ? 'none' : 'block';
        }

        function toggleDarkMode() {
            document.body.classList.toggle('dark-mode');
            const isDark = document.body.classList.contains('dark-mode');
            document.getElementById('themeIcon').innerText = isDark ? '☀️' : '🌙';
        }

        function toggleAI() {
            const w = document.getElementById('aiWindow');
            w.style.display = w.style.display === 'flex' ? 'none' : 'flex';
        }

        function askAI() {
            const input = document.getElementById('aiInput');
            const chat = document.getElementById('aiChat');
            const val = input.value.trim();
            if(!val) return;

            chat.innerHTML += `<div style="text-align:left; color:var(--primary); margin-bottom:5px;"><b>أنت:</b> ${val}</div>`;
            
            let reply = "أنا هنا لمساعدتك! يمكنك سؤالي عن كيفية استخدام الموقع.";
            
            if(val.includes("كيف") && val.includes("الموقع")) {
                reply = "سهل جداً! أولاً اختر لاعبك المفضل من المربعات، ثم اكتب اسمك ورسالتك واضغط إرسال. لا تنسَ مشاهدة الفيديو والضغط على زر أعجبني!";
            } else if(val.includes("من أنت")) {
                reply = "أنا وكيل ذكاء اصطناعي مخصص لنادي تاريشت، وظيفتي إرشادك وتسهيل تواصلك مع الإدارة.";
            } else if(val.includes("صداقة")) {
                reply = "لطلب الصداقة، اضغط على الزر الأزرق الموجود في أعلى البطاقة البيضاء.";
            }

            setTimeout(() => {
                chat.innerHTML += `<div style="background:var(--primary); color:white; padding:8px; border-radius:10px; margin-bottom:10px;"><b>AI:</b> ${reply}</div>`;
                speak(reply);
                chat.scrollTop = chat.scrollHeight;
            }, 500);
            input.value = "";
        }

        function selChar(el, name) {
            document.querySelectorAll('.char-item').forEach(i => i.classList.remove('active'));
            el.classList.add('active');
            char = name;
        }

        function toggleLike() {
            isLiked = !isLiked;
            document.getElementById('likeArea').classList.toggle('active', isLiked);
        }

        function requestFriendship() {
            const name = document.getElementById('name').value || "زائر";
            window.location.href = `mailto:marwnmellok@gmail.com?subject=طلب صداقة&body=المرسل: ${name}`;
        }

        function handleSend() {
            const name = document.getElementById('name').value;
            const msg = document.getElementById('msg').value;
            if(!name || !msg) return alert("يرجى إدخال اسمك ورسالتك");
            window.location.href = `mailto:marwnmellok@gmail.com?subject=رسالة من ${name}&body=اللاعب المختار: ${char}%0Aالرسالة: ${msg}`;
        }
    </script>
</body>
</html>
