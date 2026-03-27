<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>رادار تاريشت - خريطة المحادثة اللحظية 🛰️</title>
    
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-database-compat.js"></script>

    <style>
        :root { --primary: #1e3c72; --white: #ffffff; }
        body, html { margin: 0; padding: 0; height: 100%; font-family: sans-serif; overflow: hidden; }
        #map { height: 100vh; width: 100%; position: absolute; z-index: 1; }
        
        /* واجهة المستخدم */
        .radar-panel {
            position: absolute; bottom: 20px; left: 50%; transform: translateX(-50%);
            z-index: 1000; width: 90%; max-width: 400px;
            background: rgba(255, 255, 255, 0.9); padding: 20px;
            border-radius: 20px; box-shadow: 0 5px 25px rgba(0,0,0,0.2); text-align: center;
        }

        /* تصميم الأفاتار */
        .avatar-marker {
            background: white; border: 3px solid var(--primary);
            border-radius: 50%; width: 50px !important; height: 50px !important;
            box-shadow: 0 4px 10px rgba(0,0,0,0.3);
        }
        .avatar-marker img { width: 100%; height: 100%; border-radius: 50%; }

        /* فقاعة الدردشة فوق الخريطة */
        .chat-bubble {
            background: var(--primary); color: white; padding: 5px 10px;
            border-radius: 10px; font-size: 12px; font-weight: bold;
            position: absolute; top: -40px; left: 50%; transform: translateX(-50%);
            white-space: nowrap; box-shadow: 0 2px 5px rgba(0,0,0,0.2);
        }

        input { width: 80%; padding: 10px; border-radius: 10px; border: 1px solid #ddd; }
        button { padding: 10px 20px; border-radius: 10px; border: none; background: var(--primary); color: white; cursor: pointer; }
    </style>
</head>
<body>

<div id="map"></div>

<div class="radar-panel">
    <div id="login">
        <h3>رادار تاريشت 📡</h3>
        <input type="text" id="username" placeholder="اسمك المستعار">
        <button onclick="start()">دخول</button>
    </div>
    <div id="chat-box" style="display:none;">
        <p id="my-info"></p>
        <input type="text" id="msgInput" placeholder="اكتب رسالة للجميع...">
        <button onclick="broadcastMsg()">نشر</button>
    </div>
</div>

<script>
    const firebaseConfig = { databaseURL: "https://tarisht-radar-default-rtdb.firebaseio.com/" };
    firebase.initializeApp(firebaseConfig);
    const db = firebase.database();

    let map, myMarker, myName, myAvatar;
    let markers = {};

    map = L.map('map', { zoomControl: false }).setView([33.5731, -7.5898], 13);
    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png').addTo(map);

    function start() {
        myName = document.getElementById('username').value.trim();
        if(!myName) return;
        myAvatar = `https://api.dicebear.com/7.x/avataaars/svg?seed=${encodeURIComponent(myName)}`;

        if (navigator.geolocation) {
            navigator.geolocation.watchPosition(pos => {
                const { latitude: lat, longitude: lng } = pos.coords;
                updateMap(lat, lng);
            }, null, { enableHighAccuracy: true });

            document.getElementById('login').style.display = 'none';
            document.getElementById('chat-box').style.display = 'block';
            document.getElementById('my-info').innerText = "أنت الآن متاح للأصدقاء كـ: " + myName;
            
            listen();
        }
    }

    function updateMap(lat, lng) {
        if (!myMarker) {
            myMarker = L.marker([lat, lng], {
                icon: L.divIcon({ className: 'avatar-marker', html: `<img src="${myAvatar}">`, iconSize: [50, 50] })
            }).addTo(map);
            map.setView([lat, lng], 15);
        } else {
            myMarker.setLatLng([lat, lng]);
        }

        db.ref('players/' + myName).update({ name: myName, lat, lng, avatar: myAvatar });
        db.ref('players/' + myName).onDisconnect().remove();
    }

    // إرسال رسالة تظهر فوق الأفاتار
    function broadcastMsg() {
        const txt = document.getElementById('msgInput').value;
        if(!txt) return;
        db.ref('players/' + myName).update({ lastMsg: txt });
        document.getElementById('msgInput').value = "";
        // تختفي الرسالة بعد 5 ثوانٍ
        setTimeout(() => db.ref('players/' + myName + '/lastMsg').remove(), 5000);
    }

    function listen() {
        db.ref('players').on('value', snap => {
            const players = snap.val();
            for (let id in players) {
                if (id === myName) continue;
                const p = players[id];
                
                // إضافة الرسالة في فقاعة فوق الأفاتار إذا وجدت
                const chatHtml = p.lastMsg ? `<div class="chat-bubble">${p.lastMsg}</div>` : '';
                const html = `<div style="position:relative">${chatHtml}<img src="${p.avatar}"></div>`;

                const icon = L.divIcon({ className: 'avatar-marker', html: html, iconSize: [50, 50] });

                if (markers[id]) {
                    markers[id].setLatLng([p.lat, p.lng]).setIcon(icon);
                } else {
                    markers[id] = L.marker([p.lat, p.lng], { icon }).addTo(map).bindPopup(p.name);
                }
            }
        });
    }
</script>
</body>
</html>
