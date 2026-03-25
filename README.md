<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>نادي تاريشت - النسخة الذكية</title>
    <style>
        :root {
            --primary: #1e3c72;
            --accent: #ffd700;
            --white: #ffffff;
            --heart: #e74c3c;
            --bg-color: #f4f7f6;
            --text-color: #333;
        }

        /* وضع السطوع المنخفض */
        .dark-mode {
            --bg-color: #1a1a1a;
            --white: #2d2d2d;
            --text-color: #eee;
        }

        body, html {
            height: 100%; margin: 0; display: flex; justify-content: center;
            align-items: center; background-color: var(--bg-color);
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            transition: 0.3s; color: var(--text-color);
        }

        /* الإعدادات */
        .settings-bar {
            position: absolute; top: 20px; right: 20px; z-index: 100;
        }
        .settings-icon { font-size: 24px; cursor: pointer; animation: rotate 5s linear infinite; }
        @keyframes rotate { from { transform: rotate(0deg); } to { transform: rotate(360deg); } }
        
        .settings-menu {
            display: none; position: absolute; top: 40px; right: 0; 
            background: white; border-radius: 10px; box-shadow: 0 5px 15px rgba(0,0,0,0.2);
            padding: 10px; width: 180px; text-align: right; color: #333;
        }

        .card {
            background: var(--white); padding: 20px; border-radius: 25px;
            box-shadow: 0 15px 50px rgba(0,0,0,0.1); width: 95%;
            max-width: 450px; text-align: center; border: 1px solid #f0f0f0;
            position: relative; transition: 0.3s;
        }

        .friend-request {
            background: #f0f4ff; color: var(--primary); padding: 8px 15px;
            border-radius: 20px; font-size: 13px; font-weight: bold;
            display: inline-flex; align-items: center; gap: 5px;
            margin-bottom: 15px; cursor: pointer; border: 1px solid var(--primary);
        }

        .char-grid {
            display: grid; grid-template-columns: repeat(4, 1fr);
            gap: 10px; margin: 15px 0; max-height: 150px; overflow-y: auto;
            padding: 5px; border: 1px inset #eee; border-radius: 10px;
        }

        .char-item { cursor: pointer; padding: 5px; border-radius: 10px; transition: 0.3s; }
        .char-item.active { border: 2px solid var(--primary); background: #f0f4ff; }
        .char-item img { width: 45px; height: 45px; border-radius: 50%; }
        .char-item span { font-size: 10px; display: block; }

        input, textarea {
            width: 100%; padding: 10px; margin-bottom: 10px;
            border: 1px solid #ddd; border-radius: 8px; box-sizing: border-box;
            background: var(--white); color: var(--text-color);
        }

        button {
            background: var(--primary); color: white; border: none;
            padding: 12px; border-radius: 10px; width: 100%;
            font-weight: bold; cursor: pointer;
        }

        /* وكيل الذكاء الاصطناعي */
        .ai-bot-btn {
            position: fixed; bottom: 20px; left: 20px;
            background: var(--primary); color: white; width: 60px; height: 60px;
            border-radius: 50%; display: flex; justify-content: center; align-items: center;
            font-size: 30px; cursor: pointer; box-shadow: 0 5px 15px rgba(0,0,0,0.3);
        }

        .ai-window {
            display: none; position: fixed; bottom: 90px; left: 20px;
            width: 300px; background: white; border-radius: 15px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.2); overflow: hidden;
            flex-direction: column; color: #333; z-index: 1000;
        }
        .ai-header { background: var(--primary); color: white; padding: 10px; font-weight: bold; }
        .ai-chat { height: 200px; overflow-y: auto; padding: 10px; font-size: 14px; text-align: right; }
        .ai-input { display: flex; border-top: 1px solid #eee; }
        .ai-input input { border: none; flex: 1; padding: 10px; border-radius: 0; margin: 0; }

        .video-section { margin-top: 20px; border-top: 2px solid #f0f0f0; padding-top: 15px; }
        .video-container { width: 100%; border-radius: 12px; overflow: hidden; aspect-ratio: 16/9; background: #000; }
        .video-container iframe { width: 100%; height: 100%; border: none; }

        .like-area { margin-top: 8px; display: flex; justify-content: center; align-items: center; gap: 8px; cursor: pointer; }
        .like-icon { font-size: 20px; color: #ccc; }
        .like-area.active .like-icon { color: var(--heart); }
    </style>
</head>
<body onclick="welcomeVoice()">

    <div class="settings-bar">
        <div class="settings-icon" onclick="toggleSettings()">⚙️</div>
        <div class="settings-menu" id="settingsMenu">
            <div style="cursor: pointer; margin-bottom: 10px;" onclick="toggleDarkMode()">🌓 وضع السطوع</div>
            <div style="color: #ccc; font-size: 12px;">👤 الحساب (قريباً)</div>
        </div>
    </div>

    <div class="card">
        <div class="friend-request" onclick="requestFriendship()">
            👤 <span>اريد صداقة مع الفريق</span>
        </div>

        <div class="char-grid">
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
        
        <button id="sendBtn" onclick="handleSend()">إرسال الرسالة</button>

        <div class="video-section">
            <div class="video-container">
                <iframe src="https://www.youtube.com/embed/inhEapj87R8"></iframe>
            </div>
            <div class="like-area" id="likeArea" onclick="toggleLike()">
                <span class="like-icon">❤</span>
                <span style="font-size: 12px;">أعجبني</span>
            </div>
        </div>
    </div>

    <div class="ai-bot-btn" onclick="toggleAI()">🤖</div>
    <div class="ai-window" id="aiWindow">
        <div class="ai-header">مساعد تاريشت الذكي</div>
        <div class="ai-chat" id="aiChat">
            <div>مرحباً! أنا وكيل تاريشت. كيف يمكنني مساعدتك اليوم؟</div>
        </div>
        <div class="ai-input">
            <input type="text" id="aiQuery" placeholder="اسألني أي شيء..." onkeypress="checkEnter(event)">
            <button style="width: 50px; border-radius: 0;" onclick="askAI()">💬</button>
        </div>
    </div>

    <script>
        let char = "شعار الفريق";
        let isLiked = false;
        let hasWelcomed = false;

        // ترحيب صوتي
        function welcomeVoice() {
            if (!hasWelcomed) {
                speak("مرحباً بك في تاريشت مولاي علي");
                hasWelcomed = true;
            }
        }

        function speak(text) {
            const speech = new SpeechSynthesisUtterance(text);
            speech.lang = "ar-SA";
            window.speechSynthesis.speak(speech);
        }

        // الإعدادات
        function toggleSettings() {
            const menu = document.getElementById('settingsMenu');
            menu.style.display = menu.style.display === 'block' ? 'none' : 'block';
        }

        function toggleDarkMode() {
            document.body.classList.toggle('dark-mode');
        }

        // وكيل الذكاء الاصطناعي
        function toggleAI() {
            const win = document.getElementById('aiWindow');
            win.style.display = win.style.display === 'flex' ? 'none' : 'flex';
        }

        function askAI() {
            const input = document.getElementById('aiQuery');
            const chat = document.getElementById('aiChat');
            if(!input.value) return;

            const userMsg = input.value;
            chat.innerHTML += `<div style="color: blue;">أنت: ${userMsg}</div>`;
            
            // رد مبرمج بناءً على بيانات الموقع
            let response = "عذراً، أنا أتعلم حالياً عن نادي تاريشت. يمكنك مراسلة الإدارة عبر النموذج!";
            
            if(userMsg.includes("من أنت")) response = "أنا المساعد الذكي لنادي تاريشت، مبرمج لمساعدتك في تصفح الموقع.";
            if(userMsg.includes("صداقة")) response = "يمكنك طلب الصداقة بالضغط على الزر الأزرق في أعلى الصفحة.";
            if(userMsg.includes("ميسي") || userMsg.includes("رونالدو")) response = "نحن نحب أساطير كرة القدم! يمكنك اختيار لاعبك المفضل من القائمة.";

            setTimeout(() => {
                chat.innerHTML += `<div style="font-weight: bold;">تاريشت AI: ${response}</div>`;
                speak(response);
                chat.scrollTop = chat.scrollHeight;
            }, 500);

            input.value = "";
        }

        function checkEnter(e) { if(e.key === 'Enter') askAI(); }

        // الوظائف الأساسية
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
            window.location.href = `mailto:marwnmellok@gmail.com?subject=طلب صداقة&body=المرسل: ${name}%0Aالموضوع: أريد صداقة مع فريق تاريشت`;
        }

        function handleSend() {
            const name = document.getElementById('name').value;
            const msg = document.getElementById('msg').value;
            if(!name || !msg) return alert("يرجى ملء البيانات");
            const reaction = isLiked ? "تم الإعجاب ❤️" : "بدون إعجاب";
            window.location.href = `mailto:marwnmellok@gmail.com?subject=رسالة من ${name}&body=اللاعب: ${name}%0Aالشخصية: ${char}%0Aالتفاعل: ${reaction}%0Aالرسالة: ${msg}`;
        }
    </script>
</body>
</html>
