<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>نادي تاريشت | Tarisht FC</title>
    <link rel="icon" href="https://cdn-icons-png.flaticon.com/512/53/53283.png">
    <style>
        :root {
            --primary-color: #1e3c72;
            --secondary-color: #2a5298;
            --accent-color: #ffd700; /* لون ذهبي رياضي */
            --text-dark: #333;
            --white: #ffffff;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, var(--primary-color) 0%, var(--secondary-color) 100%);
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
            padding: 20px;
        }

        /* تصميم البطاقة */
        .card {
            background: var(--white);
            padding: 40px 30px;
            border-radius: 25px;
            box-shadow: 0 15px 35px rgba(0,0,0,0.3);
            width: 100%;
            max-width: 380px;
            text-align: center;
            border-bottom: 5px solid var(--accent-color);
        }

        .logo-container {
            width: 100px;
            height: 100px;
            background: #f8f9fa;
            border-radius: 50%;
            margin: 0 auto 20px;
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 50px;
            border: 3px solid var(--primary-color);
            box-shadow: 0 5px 15px rgba(0,0,0,0.1);
        }

        h1 {
            color: var(--primary-color);
            margin: 0 0 10px 0;
            font-size: 28px;
            font-weight: 800;
        }

        p {
            font-size: 15px;
            color: #555;
            line-height: 1.6;
            margin-bottom: 30px;
        }

        /* تنسيق النموذج */
        .form-group {
            text-align: right;
            margin-bottom: 20px;
        }

        label {
            display: block;
            margin-bottom: 8px;
            font-weight: bold;
            color: var(--primary-color);
        }

        input[type="text"] {
            width: 100%;
            padding: 14px;
            border: 2px solid #eee;
            border-radius: 12px;
            box-sizing: border-box;
            outline: none;
            font-size: 16px;
            transition: all 0.3s ease;
        }

        input[type="text"]:focus {
            border-color: var(--secondary-color);
            box-shadow: 0 0 8px rgba(42, 82, 152, 0.2);
        }

        button {
            background: var(--primary-color);
            color: var(--white);
            border: none;
            padding: 15px;
            border-radius: 12px;
            cursor: pointer;
            width: 100%;
            font-size: 18px;
            font-weight: bold;
            transition: all 0.3s ease;
            box-shadow: 0 4px 10px rgba(30, 60, 114, 0.3);
        }

        button:hover {
            background: var(--secondary-color);
            transform: translateY(-2px);
            box-shadow: 0 6px 15px rgba(30, 60, 114, 0.4);
        }

        .footer {
            margin-top: 25px;
            font-size: 13px;
            color: #888;
        }

        /* تأثيرات الاستجابة للهواتف */
        @media (max-width: 480px) {
            .card {
                padding: 30px 20px;
            }
        }
    </style>
</head>
<body>

    <div class="card">
        <div class="logo-container">⚽</div>
        <h1>فريق تاريشت</h1>
        <p>مرحباً بك في البوابة الرسمية للفريق. <br> سجل بياناتك لتصل للإدارة فوراً.</p>

        <form action="https://formspree.io/f/marwnmellok@gmail.com" method="POST">
            <div class="form-group">
                <label for="name">الاسم الكامل:</label>
                <input type="text" id="name" name="الاسم" placeholder="اكتب اسمك هنا..." required>
            </div>
            
            <input type="hidden" name="_subject" value="طلب انضمام جديد: فريق تاريشت">
            <input type="hidden" name="_next" value="https://github.com"> <button type="submit">إرسال الطلب</button>
        </form>

        <div class="footer">
            تم التطوير بواسطة <b>Marwan Mellouk</b><br>
            © 2026 Tarisht Football Team
        </div>
    </div>

</body>
</html>

