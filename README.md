<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>رادار نادي تاريشت - النسخة الحقيقية</title>
    <script src="https://unpkg.com/peerjs@1.5.2/dist/peerjs.min.js"></script>
    <style>
        :root { --primary: #1e3c72; --accent: #ffd700; --bg: #f4f7f6; }
        body { font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; background: var(--bg); display: flex; justify-content: center; align-items: center; height: 100vh; margin: 0; }
        .card { background: white; padding: 30px; border-radius: 25px; box-shadow: 0 15px 35px rgba(0,0,0,0.1); width: 90%; max-width: 400px; text-align: center; }
        input { width: 100%; padding: 12px; margin: 10px 0; border: 2px solid #eee; border-radius: 12px; outline: none; transition: 0.3s; box-sizing: border-box; }
        input:focus { border-color: var(--primary); }
        .btn { background: var(--primary); color: white; border: none; padding: 12px 20px; width: 100%; border-radius: 12px; cursor: pointer; font-weight: bold; font-size: 16px; margin-top: 10px; }
        #chat-area { display: none; }
        #messages { height: 200px; overflow-y: auto; background: #fafafa; border: 1px solid #eee; border-radius: 10px; padding: 10px; margin-bottom: 10px; display: flex; flex-direction: column; }
        .msg { margin-bottom: 10px; padding: 8px 12px; border-radius: 15px; font-size: 14px; width: fit-content; max-width: 80%; }
        .sent { background: var(--primary); color: white; align-self: flex-start; }
        .received { background: #e0e0e0; color: black; align-self: flex-end; }
        .status-dot { width: 10px; height: 10px; background: #ccc; border-radius: 50%; display: inline-block; margin-left: 5px; }
        .online { background: #2ecc71; }
    </style>
</head>
<body>

<div class="card">
    <h2 id="titleText">نادي تاريشت 📡</h2>
    <p id="statusInfo" style="font-size: 13px; color: #666;">انتظر قليلاً لتفعيل الرادار...</p>

    <div id="login-screen">
        <input type="text" id="usernameInput" placeholder="اكتب اسمك هنا (مثلاً: marwan)">
        <button class="btn" onclick="initRadar()">تفعيل الرادار باسمي</button>
    </div>

    <div id="chat-area">
        <div id="connection-box">
            <input type="text" id="peerIdInput" placeholder="اكتب اسم الصديق للبحث عنه">
            <button class="btn" style="background: #0082fc;" onclick="connectToPeer()">اتصال بالصديق</button>
        </div>
        
        <div id="active-chat" style="display: none; margin-top: 20px;">
            <div id="messages"></div>
            <div style="display: flex; gap: 5px;">
                <input type="text" id="messageInput" placeholder="اكتب رسالة..." style="margin:0;">
                <button class="btn" style="width: 70px; margin:0;" onclick="sendMessage()">إرسال</button>
            </div>
        </div>
    </div>
</div>

<script>
    let peer;
    let connection;

    // 1. تفعيل الرادار بالاسم المختار
    function initRadar() {
        const myName = document.getElementById('usernameInput').value.trim();
        if (!myName) return alert("يرجى إدخال اسمك!");

        // إنشاء معرف مستخدم بناءً على الاسم
        peer = new Peer(myName);

        peer.on('open', (id) => {
            document.getElementById('login-screen').style.display = 'none';
            document.getElementById('chat-area').style.display = 'block';
            document.getElementById('statusInfo').innerHTML = `<span class="status-dot online"></span> رادارك نشط الآن باسم: <b>${id}</b>`;
        });

        // استقبال اتصال من شخص آخر
        peer.on('connection', (conn) => {
            connection = conn;
            setupChat();
            alert("هناك شخص يحاول الاتصال بك: " + conn.peer);
        });

        peer.on('error', (err) => {
            if(err.type === 'unavailable-id') {
                alert("هذا الاسم محجوز حالياً، اختر اسماً آخر.");
            } else {
                alert("حدث خطأ في الرادار.");
                console.error(err);
            }
        });
    }

    // 2. الاتصال بصديق عن طريق اسمه
    function connectToPeer() {
        const targetName = document.getElementById('peerIdInput').value.trim();
        if (!targetName) return alert("اكتب اسم الصديق أولاً");

        connection = peer.connect(targetName);
        setupChat();
    }

    // 3. إعداد واجهة الدردشة بعد الاتصال
    function setupChat() {
        connection.on('open', () => {
            document.getElementById('connection-box').style.display = 'none';
            document.getElementById('active-chat').style.display = 'block';
            document.getElementById('statusInfo').innerHTML = `<span class="status-dot online"></span> متصل الآن مع: <b>${connection.peer}</b>`;

            connection.on('data', (data) => {
                displayMessage(data, 'received');
            });
        });
    }

    // 4. إرسال الرسالة
    function sendMessage() {
        const msgInput = document.getElementById('messageInput');
        const text = msgInput.value.trim();
        if (!text || !connection) return;

        connection.send(text);
        displayMessage(text, 'sent');
        msgInput.value = "";
    }

    // 5. عرض الرسائل في الشاشة
    function displayMessage(text, type) {
        const msgDiv = document.createElement('div');
        msgDiv.className = `msg ${type}`;
        msgDiv.innerText = text;
        const container = document.getElementById('messages');
        container.appendChild(msgDiv);
        container.scrollTop = container.scrollHeight;
    }
</script>
</body>
</html>
