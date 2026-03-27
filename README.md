<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>نادي تاريشت - اتصال الأسماء</title>
    <script src="https://unpkg.com/peerjs@1.5.2/dist/peerjs.min.js"></script>
    <style>
        :root { --primary: #1e3c72; --bt-blue: #0082fc; --bg: #f0f2f5; }
        body { font-family: sans-serif; background: var(--bg); display: flex; justify-content: center; align-items: center; height: 100vh; margin: 0; }
        .card { background: white; padding: 20px; border-radius: 20px; box-shadow: 0 10px 30px rgba(0,0,0,0.1); width: 350px; text-align: center; }
        input { width: 100%; padding: 12px; margin: 10px 0; border: 1px solid #ddd; border-radius: 10px; box-sizing: border-box; }
        .btn { background: var(--bt-blue); color: white; border: none; padding: 12px; width: 100%; border-radius: 10px; cursor: pointer; font-weight: bold; }
        #chat-box { display: none; flex-direction: column; height: 400px; }
        #messages { flex: 1; overflow-y: auto; padding: 10px; background: #f9f9f9; border-radius: 10px; margin-bottom: 10px; text-align: right; }
        .msg { margin-bottom: 8px; padding: 8px; border-radius: 8px; max-width: 80%; font-size: 14px; }
        .my-msg { background: var(--bt-blue); color: white; margin-right: auto; }
        .their-msg { background: #e4e6eb; color: black; margin-left: auto; }
    </style>
</head>
<body>

<div class="card">
    <h3 id="status">نادي تاريشت الذكي</h3>
    
    <div id="login-area">
        <p>اختر الاسم الذي سيظهر للآخرين:</p>
        <input type="text" id="myUsername" placeholder="مثلاً: مروان_123">
        <button class="btn" onclick="startSystem()">تفعيل رادار الاسم</button>
    </div>

    <div id="chat-box">
        <div id="messages"></div>
        <div id="connection-controls">
            <input type="text" id="targetId" placeholder="اكتب اسم الصديق للاتصال به">
            <button class="btn" style="background: green;" onclick="connectToFriend()">اتصال بالصديق</button>
        </div>
        <div id="typing-area" style="display:none; margin-top:10px;">
            <input type="text" id="msgInput" placeholder="اكتب رسالة...">
            <button class="btn" onclick="sendMsg()">إرسال</button>
        </div>
    </div>
</div>

<script>
    let peer;
    let conn;

    function startSystem() {
        const name = document.getElementById('myUsername').value;
        if(!name) return alert("يرجى كتابة اسم");

        // إنشاء معرف (ID) هو نفسه الاسم الذي اختاره المستخدم
        peer = new Peer(name);

        peer.on('open', (id) => {
            document.getElementById('login-area').style.display = 'none';
            document.getElementById('chat-box').style.display = 'flex';
            document.getElementById('status').innerText = "هويتك الآن: " + id;
            document.getElementById('status').style.color = "green";
        });

        // استقبال اتصال من شخص آخر
        peer.on('connection', (c) => {
            conn = c;
            setupChatListeners();
            alert("اتصل بك: " + conn.peer);
        });

        peer.on('error', (err) => {
            alert("هذا الاسم مستخدم بالفعل أو حدث خطأ.");
            console.error(err);
        });
    }

    function connectToFriend() {
        const target = document.getElementById('targetId').value;
        if(!target) return alert("اكتب اسم الشخص أولاً");

        conn = peer.connect(target);
        setupChatListeners();
    }

    function setupChatListeners() {
        conn.on('open', () => {
            document.getElementById('connection-controls').style.display = 'none';
            document.getElementById('typing-area').style.display = 'block';
            document.getElementById('status').innerText = "متصل مع: " + conn.peer;
            
            conn.on('data', (data) => {
                addMessage(data, 'their-msg');
            });
        });
    }

    function sendMsg() {
        const input = document.getElementById('msgInput');
        const text = input.value;
        if(!text) return;

        conn.send(text);
        addMessage(text, 'my-msg');
        input.value = "";
    }

    function addMessage(text, className) {
        const msgDiv = document.createElement('div');
        msgDiv.className = 'msg ' + className;
        msgDiv.innerText = text;
        document.getElementById('messages').appendChild(msgDiv);
        document.getElementById('messages').scrollTop = document.getElementById('messages').scrollHeight;
    }
</script>

</body>
</html>
