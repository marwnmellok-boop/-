<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>شات زوار تاريشت 💬</title>
    
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-database-compat.js"></script>

    <style>
        :root { --primary: #1e3c72; --bg: #f0f2f5; }
        body { font-family: 'Segoe UI', sans-serif; background: var(--bg); margin: 0; display: flex; flex-direction: column; height: 100vh; }
        
        /* واجهة الدخول */
        #login-overlay { position: fixed; inset: 0; background: var(--primary); z-index: 2000; display: flex; align-items: center; justify-content: center; color: white; }
        .login-box { background: white; padding: 30px; border-radius: 20px; color: #333; text-align: center; width: 300px; }

        /* الهيكل الرئيسي */
        header { background: var(--primary); color: white; padding: 15px; text-align: center; font-weight: bold; }
        main { flex: 1; display: flex; overflow: hidden; }
        
        /* قائمة الزوار */
        #user-list { width: 120px; background: #fff; border-left: 1px solid #ddd; padding: 10px; overflow-y: auto; font-size: 13px; }
        .user-online { color: green; margin-bottom: 5px; font-weight: bold; }

        /* منطقة الرسائل */
        #chat-area { flex: 1; display: flex; flex-direction: column; background: #e5ddd5; }
        #messages { flex: 1; padding: 15px; overflow-y: auto; display: flex; flex-direction: column; gap: 10px; }
        .msg { background: white; padding: 8px 12px; border-radius: 10px; max-width: 80%; position: relative; box-shadow: 0 1px 2px rgba(0,0,0,0.1); }
        .msg b { display: block; font-size: 11px; color: var(--primary); margin-bottom: 3px; }
        .msg.me { align-self: flex-end; background: #dcf8c6; }

        /* إدخال الرسالة */
        .input-bar { background: #f0f0f0; padding: 10px; display: flex; gap: 10px; }
        input[type="text"] { flex: 1; padding: 10px; border-radius: 20px; border: 1px solid #ddd; outline: none; }
        button { background: var(--primary); color: white; border: none; padding: 10px 20px; border-radius: 20px; cursor: pointer; }
    </style>
</head>
<body>

<div id="login-overlay">
    <div class="login-box">
        <h3>نادي تاريشت</h3>
        <p>أدخل اسمك المستعار للدخول</p>
        <input type="text" id="username" placeholder="مثلاً: زائر_55">
        <button onclick="enterChat()" style="width:100%; margin-top:10px;">دخول الشات</button>
    </div>
</div>

<header>رادار الزوار المتصلين 📡</header>

<main>
    <div id="user-list">
        <b>متصل الآن:</b>
        <div id="online-count"></div>
    </div>
    
    <div id="chat-area">
        <div id="messages"></div>
        <div class="input-bar">
            <input type="text" id="msgInput" placeholder="اكتب رسالة..." onkeypress="if(event.key==='Enter') sendMsg()">
            <button onclick="sendMsg()">إرسال</button>
        </div>
    </div>
</main>

<script>
    // إعداد Firebase (قاعدة بيانات عامة للتجربة)
    const firebaseConfig = { databaseURL: "https://tarisht-radar-default-rtdb.firebaseio.com/" };
    firebase.initializeApp(firebaseConfig);
    const db = firebase.database();

    let myName = "";

    function enterChat() {
        myName = document.getElementById('username').value.trim();
        if(!myName) return alert("اكتب اسماً مستعاراً");

        document.getElementById('login-overlay').style.display = 'none';

        // 1. تسجيل الدخول في قائمة المتصلين
        const statusRef = db.ref('online_status/' + myName);
        statusRef.set({ name: myName, lastSeen: Date.now() });
        statusRef.onDisconnect().remove();

        // 2. البدء في مراقبة الزوار والرسائل
        listenForUsers();
        listenForMessages();
    }

    function sendMsg() {
        const input = document.getElementById('msgInput');
        const text = input.value.trim();
        if(!text) return;

        db.ref('chat_messages').push({
            sender: myName,
            text: text,
            time: Date.now()
        });
        input.value = "";
    }

    function listenForMessages() {
        db.ref('chat_messages').limitToLast(20).on('child_added', (snapshot) => {
            const data = snapshot.val();
            const msgDiv = document.createElement('div');
            msgDiv.className = `msg ${data.sender === myName ? 'me' : ''}`;
            msgDiv.innerHTML = `<b>${data.sender}</b> ${data.text}`;
            const container = document.getElementById('messages');
            container.appendChild(msgDiv);
            container.scrollTop = container.scrollHeight;
        });
    }

    function listenForUsers() {
        db.ref('online_status').on('value', (snapshot) => {
            const users = snapshot.val();
            const list = document.getElementById('online-count');
            list.innerHTML = "";
            let count = 0;
            for (let id in users) {
                count++;
                list.innerHTML += `<div class="user-online">👤 ${users[id].name}</div>`;
            }
        });
    }
</script>

</body>
</html>
