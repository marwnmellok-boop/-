<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>نادي تاريشت - النسخة المطورة</title>
    <style>
        :root {
            --primary: #1e3c72;
            --accent: #ffd700;
            --white: #ffffff;
            --heart: #e74c3c;
        }

        body, html {
            height: 100%; margin: 0; display: flex; justify-content: center;
            align-items: center; background-color: var(--white);
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        .card {
            background: #fff; padding: 20px; border-radius: 25px;
            box-shadow: 0 15px 50px rgba(0,0,0,0.1); width: 95%;
            max-width: 450px; text-align: center; border: 1px solid #f0f0f0;
            position: relative;
        }

        /* زر طلب الصداقة في الأعلى */
        .friend-request {
            background: #f0f4ff; color: var(--primary); padding: 8px 15px;
            border-radius: 20px; font-size: 13px; font-weight: bold;
            display: inline-flex; align-items: center; gap: 5px;
            margin-bottom: 15px; cursor: pointer; border: 1px solid var(--primary);
        }

        .char-grid {
            display: grid; grid-template-columns: repeat(4, 1fr);
            gap: 10px; margin: 15px 0; max-height: 200px; overflow-y: auto;
            padding: 5px; border: 1px inset #eee; border-radius: 10px;
        }

        .char-item {
            cursor: pointer; padding: 5px; border-radius: 10px;
            border: 2px solid transparent; transition: 0.3s;
        }

        .char-item.active { border-color: var(--primary); background: #f0f4ff; }
        .char-item img { width: 45px; height: 45px; border-radius: 50%; object-fit: cover; }
        .char-item span { font-size: 10px; display: block; margin-top: 3px; }

        input, textarea {
            width: 100%; padding: 10px; margin-bottom: 10px;
            border: 1px solid #ddd; border-radius: 8px; box-sizing: border-box;
        }

        button {
            background: var(--primary); color: white; border: none;
            padding: 12px; border-radius: 10px; width: 100%;
            font-weight: bold; cursor: pointer;
        }

        .video-section { margin-top: 20px; border-top: 2px solid #f0f0f0; padding-top: 15px; }
        .video-container { width: 100%; border-radius: 12px; overflow: hidden; aspect-ratio: 16/9; background: #000; }
        .video-container iframe { width: 100%; height: 100%; border: none; }

        .like-area { margin-top: 8px; display: flex; justify-content: center; align-items: center; gap: 8px; cursor: pointer; }
        .like-icon { font-size: 20px; color: #ccc; }
        .like-area.active .like-icon { color: var(--heart); }

        .tap-hint { position: fixed; bottom: 10px; font-size: 11px; color: #ccc; }
    </style>
</head>
<body onclick="welcomeVoice()">

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
            <div class="char-item" onclick="selChar(this, 'نيمار')">
                <img src="https://cdn-icons-png.flaticon.com/512/3135/3135789.png"><span>نيمار</span>
            </div>
            <div class="char-item" onclick="selChar(this, 'مارادونا')">
                <img src="https://cdn-icons-png.flaticon.com/512/3135/3135755.png"><span>مارادونا</span>
            </div>
            <div class="char-item" onclick="selChar(this, 'بيليه')">
                <img src="https://cdn-icons-png.flaticon.com/512/3135/3135801.png"><span>بيليه</span>
            </div>
            <div class="char-item" onclick="selChar(this, 'مبابي')">
                <img src="https://cdn-icons-png.flaticon.com/512/3135/3135823.png"><span>مبابي</span>
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

    <div class="tap-hint">انقر لتفعيل الترحيب 🔊</div>

    <script>
        let char = "شعار الفريق";
        let isLiked = false;
        let hasWelcomed = false;

        function welcomeVoice() {
            if (!hasWelcomed) {
                const speech = new SpeechSynthesisUtterance("مرحباً بك في تاريشت مولاي علي");
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

            const reaction = isLiked ? "تم الإعجاب بالفيديو ❤️" : "بدون إعجاب";
            const body = encodeURIComponent(`اللاعب: ${name}\nالشخصية المختارة: ${char}\nالتفاعل: ${reaction}\nالرسالة: ${msg}`);
            
            window.location.href = `mailto:marwnmellok@gmail.com?subject=رسالة من ${name}&body=${body}`;
        }
    </script>
</body>
</html>
