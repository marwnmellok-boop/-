<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>نادي تاريشت - فيديو الميراث</title>
    <style>
        :root {
            --primary: #1e3c72;
            --accent: #ffd700;
            --white: #ffffff;
        }

        body, html {
            height: 100%;
            margin: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            background-color: var(--white);
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        .card {
            background: #fff;
            padding: 25px;
            border-radius: 25px;
            box-shadow: 0 15px 50px rgba(0,0,0,0.1);
            width: 90%;
            max-width: 420px;
            text-align: center;
            border: 1px solid #f0f0f0;
        }

        /* تنسيق الفيديو */
        .video-container {
            width: 100%;
            border-radius: 15px;
            overflow: hidden;
            margin-bottom: 20px;
            aspect-ratio: 9/16; /* ليتناسب مع فيديوهات Shorts */
            background: #000;
            box-shadow: 0 5px 15px rgba(0,0,0,0.2);
        }

        .video-container iframe {
            width: 100%;
            height: 100%;
            border: none;
        }

        .char-grid {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 10px;
            margin-bottom: 20px;
        }

        .char-item {
            cursor: pointer;
            padding: 8px;
            border-radius: 12px;
            border: 2px solid transparent;
            transition: 0.3s;
        }

        .char-item.active { border-color: var(--primary); background: #f0f4ff; }
        .char-item img { width: 45px; height: 45px; border-radius: 50%; }
        .char-item span { font-size: 11px; display: block; margin-top: 5px; }

        input, textarea {
            width: 100%;
            padding: 12px;
            margin-bottom: 12px;
            border: 1px solid #ddd;
            border-radius: 10px;
            box-sizing: border-box;
            background: #fafafa;
        }

        button {
            background: var(--primary);
            color: white;
            border: none;
            padding: 15px;
            border-radius: 12px;
            width: 100%;
            font-weight: bold;
            cursor: pointer;
            transition: 0.3s;
        }

        button:disabled { background: #ccc; cursor: not-allowed; }

        #block-msg { color: red; font-size: 12px; display: none; margin-top: 10px; font-weight: bold; }
        .tap-hint { position: fixed; bottom: 15px; font-size: 12px; color: #aaa; }
    </style>
</head>
<body onclick="welcomeVoice()">

    <div class="card">
        <div class="video-container">
            <iframe src="https://www.youtube.com/embed/inhEapj87R8?autoplay=0&rel=0" 
                    allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
                    allowfullscreen>
            </iframe>
        </div>

        <h3 style="color: var(--primary); margin: 0 0 15px 0;">تواصل مع فريق تاريشت</h3>
        
        <div class="char-grid">
            <div class="char-item active" onclick="selChar(this, 'شعار الفريق', 'https://cdn-icons-png.flaticon.com/512/53/53283.png')">
                <img src="https://cdn-icons-png.flaticon.com/512/53/53283.png"><span>الفريق</span>
            </div>
            <div class="char-item" onclick="selChar(this, 'ميسي', 'https://cdn-icons-png.flaticon.com/512/3135/3135715.png')">
                <img src="https://cdn-icons-png.flaticon.com/512/3135/3135715.png"><span>ميسي</span>
            </div>
            <div class="char-item" onclick="selChar(this, 'رونالدو', 'https://cdn-icons-png.flaticon.com/512/3135/3135768.png')">
                <img src="https://cdn-icons-png.flaticon.com/512/3135/3135768.png"><span>رونالدو</span>
            </div>
            <div class="char-item" onclick="selChar(this, 'نيمار', 'https://cdn-icons-png.flaticon.com/512/3135/3135789.png')">
                <img src="https://cdn-icons-png.flaticon.com/512/3135/3135789.png"><span>نيمار</span>
            </div>
            <div class="char-item" onclick="selChar(this, 'مبابي', 'https://cdn-icons-png.flaticon.com/512/3135/3135823.png')">
                <img src="https://cdn-icons-png.flaticon.com/512/3135/3135823.png"><span>مبابي</span>
            </div>
            <div class="char-item" onclick="selChar(this, 'هالاند', 'https://cdn-icons-png.flaticon.com/512/4140/4140047.png')">
                <img src="https://cdn-icons-png.flaticon.com/512/4140/4140047.png"><span>هالاند</span>
            </div>
        </div>

        <input type="text" id="name" placeholder="الاسم الكامل">
        <textarea id="msg" rows="2" placeholder="رسالتك للإدارة..."></textarea>
        
        <button id="sendBtn" onclick="handleSend()">إرسال الرسالة ⚽</button>
        <div id="block-msg">⚠️ تم حظرك مؤقتاً! يرجى المحاولة لاحقاً.</div>
    </div>

    <div class="tap-hint">انقر في أي مكان لتفعيل الترحيب الصوتي 🔊</div>

    <script>
        let char = "شعار الفريق";
        let sendCount = 0;
        let hasWelcomed = false;

        function welcomeVoice() {
            if (!hasWelcomed) {
                const speech = new SpeechSynthesisUtterance("مرحباً بك في تاريشت");
                speech.lang = "ar-SA";
                window.speechSynthesis.speak(speech);
                hasWelcomed = true;
                document.querySelector('.tap-hint').style.display = 'none';
            }
        }

        function selChar(el, name) {
            document.querySelectorAll('.char-item').forEach(i => i.classList.remove('active'));
            el.classList.add('active');
            char = name;
        }

        function handleSend() {
            const name = document.getElementById('name').value;
            const msg = document.getElementById('msg').value;
            const btn = document.getElementById('sendBtn');
            const blockMsg = document.getElementById('block-msg');

            if(!name || !msg) return alert("يرجى ملء كافة البيانات");

            sendCount++;

            if (sendCount >= 5) {
                btn.style.display = "none";
                blockMsg.style.display = "block";
                setTimeout(() => {
                    sendCount = 0;
                    btn.style.display = "block";
                    blockMsg.style.display = "none";
                }, 3600000); // حظر لمدة ساعة
                return;
            }

            // فتح البريد
            window.location.href = `mailto:marwnmellok@gmail.com?subject=تواصل من ${name}&body=المرسل: ${name}%0Aالشخصية: ${char}%0Aالرسالة: ${msg}`;

            // عد تنازلي 3 ثواني
            let timeLeft = 3;
            btn.disabled = true;
            const timer = setInterval(() => {
                btn.innerText = `انتظر ${timeLeft}...`;
                timeLeft--;
                if (timeLeft < 0) {
                    clearInterval(timer);
                    btn.innerText = "إرسال الرسالة ⚽";
                    btn.disabled = false;
                }
            }, 1000);
        }
    </script>
</body>
</html>
