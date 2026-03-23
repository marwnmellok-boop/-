<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>مراسلة إدارة تاريشت</title>
    <style>
        :root {
            --primary: #1e3c72;
            --secondary: #ffd700;
            --white: #ffffff;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, var(--primary) 0%, #2a5298 100%);
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
            padding: 20px;
        }

        .card {
            background: var(--white);
            padding: 30px;
            border-radius: 25px;
            box-shadow: 0 15px 35px rgba(0,0,0,0.3);
            width: 100%;
            max-width: 380px;
            text-align: center;
            border-bottom: 5px solid var(--secondary);
        }

        .profile-avatar-main {
            width: 110px;
            height: 110px;
            border-radius: 50%;
            margin: 0 auto 15px;
            border: 4px solid var(--primary);
            overflow: hidden;
            background: #eee;
        }

        .profile-avatar-main img { width: 100%; height: 100%; object-fit: cover; }

        .avatar-options { display: flex; justify-content: center; gap: 10px; margin-bottom: 20px; }
        
        .avatar-item {
            width: 45px;
            height: 45px;
            border-radius: 50%;
            cursor: pointer;
            border: 2px solid transparent;
            transition: 0.3s;
            overflow: hidden;
        }

        .avatar-item.active { border-color: var(--secondary); transform: scale(1.1); }
        .avatar-item img { width: 100%; height: 100%; object-fit: cover; }

        h2 { color: var(--primary); margin-bottom: 5px; }
        p.subtitle { font-size: 13px; color: #666; margin-bottom: 20px; }
        
        input, textarea {
            width: 100%;
            padding: 12px;
            margin-bottom: 15px;
            border: 2px solid #eee;
            border-radius: 10px;
            box-sizing: border-box;
            outline: none;
            font-family: inherit;
        }

        button {
            background: var(--primary);
            color: white;
            border: none;
            padding: 14px;
            border-radius: 10px;
            width: 100%;
            font-weight: bold;
            cursor: pointer;
            font-size: 16px;
            transition: 0.3s;
        }

        button:hover { background: #15305e; }

        .footer { margin-top: 20px; font-size: 11px; color: #999; }
    </style>
</head>
<body>

    <div class="card">
        <div class="profile-avatar-main" id="display-avatar">
            <img src="https://cdn-icons-png.flaticon.com/512/53/53283.png" alt="Avatar">
        </div>

        <h2>نادي تاريشت</h2>
        <p class="subtitle">اختر شخصيتك واكتب رسالتك للإدارة</p>
        
        <div class="avatar-options">
            <div class="avatar-item" onclick="changeAvatar(this, 'ميسي', 'https://cdn-icons-png.flaticon.com/512/3135/3135715.png')">
                <img src="https://cdn-icons-png.flaticon.com/512/3135/3135715.png">
            </div>
            <div class="avatar-item" onclick="changeAvatar(this, 'رونالدو', 'https://cdn-icons-png.flaticon.com/512/3135/3135768.png')">
                <img src="https://cdn-icons-png.flaticon.com/512/3135/3135768.png">
            </div>
            <div class="avatar-item active" onclick="changeAvatar(this, 'شعار الفريق', 'https://cdn-icons-png.flaticon.com/512/53/53283.png')">
                <img src="https://cdn-icons-png.flaticon.com/512/53/53283.png">
            </div>
        </div>

        <form id="mail-form">
            <input type="text" id="user-name" placeholder="اسمك الكامل" required>
            <textarea id="user-msg" rows="3" placeholder="اكتب رسالتك هنا..." required></textarea>
            
            <button type="button" onclick="sendMail()">فتح بريد الإرسال</button>
        </form>

        <div class="footer">
            سيتم فتح تطبيق البريد الخاص بك لإتمام الإرسال
        </div>
    </div>

    <script>
        let selectedChar = "شعار الفريق";

        function changeAvatar(el, name, url) {
            document.querySelectorAll('.avatar-item').forEach(i => i.classList.remove('active'));
            el.classList.add('active');
            document.querySelector('#display-avatar img').src = url;
            selectedChar = name;
        }

        function sendMail() {
            const name = document.getElementById('user-name').value;
            const msg = document.getElementById('user-msg').value;
            const adminEmail = "marwnmellok@gmail.com";
            
            if(!name || !msg) {
                alert("يرجى ملء كافة الحقول");
                return;
            }

            // تجهيز نص الرسالة وتنسيقه للبريد
            const subject = encodeURIComponent("رسالة جديدة من: " + name);
            const body = encodeURIComponent(
                "اسم المرسل: " + name + "\n" +
                "الشخصية المختارة: " + selectedChar + "\n\n" +
                "الرسالة:\n" + msg
            );

            // فتح رابط mailto الذي يحول المستخدم لتطبيق البريد
            window.location.href = `mailto:${adminEmail}?subject=${subject}&body=${body}`;
        }
    </script>
</body>
</html>
