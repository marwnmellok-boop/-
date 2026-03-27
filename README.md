<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>رادار تاريشت - النسخة النهائية 🛰️</title>
    
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-database-compat.js"></script>

    <style>
        :root { --primary: #1e3c72; --accent: #00d2ff; --white: #ffffff; }
        body, html { margin: 0; padding: 0; height: 100%; font-family: 'Segoe UI', Tahoma, sans-serif; overflow: hidden; }
        
        /* تصميم الخريطة */
        #map { height: 100vh; width: 100%; position: absolute; z-index: 1; }
        
        /* واجهة المستخدم العائمة */
        .radar-ui {
            position: absolute; bottom: 30px; left: 50%; transform: translateX(-50%);
            z-index: 1000; width: 90%; max-width: 400px;
            background: rgba(255, 255, 255, 0.9); backdrop-filter: blur(10px);
            padding: 20px; border-radius: 30px; box-shadow: 0 10px 40px rgba(0,0,0,0.2);
            text-align: center; border: 1px solid rgba(255,255,255,0.3);
        }

        /* تصميم أيقونة الأفاتار على الخريطة */
        .avatar-marker {
            background: var(--white); border: 3px solid var(--primary);
            border-radius: 50%; width: 50px !important; height: 50px !important;
            box-shadow: 0 4px 15px rgba(0,0,0,0.3); transition: all 0.3s ease;
        }
        .avatar-marker img { width: 100%; height: 100%; border-radius: 50%; object-fit: cover; }

        input { width: 100%; padding: 12px; margin: 10px 0; border: 2px solid #ddd; border-radius: 15px; outline: none; text-align: center; font-size: 16px; }
        input:focus { border-color: var(--primary); }
        
        button { background: var(--primary); color: white; border: none; padding: 15px; width: 100%; border-radius: 15px; cursor: pointer; font-weight: bold; font-size: 16px; transition: 0.3s; }
        button:hover { background: #152a50; transform: scale(1.02); }

        .info-bar { font-size: 12px; color: #444; margin-top: 10px; display: none; }
        .pulse { display: inline-block; width: 10px; height: 10px; background: #2ecc71; border-radius: 50%; margin-left: 5px; animation: blink 1.5s infinite; }
        @keyframes blink { 0% { opacity: 1; } 50% { opacity: 0.3; } 100% { opacity: 1; } }
    </style>
</head>
<body>

<div id="map"></div>

<div class="radar-ui">
    <div id="login-screen">
        <h2 style="margin: 0 0 10px 0; color: var(--primary);">رادار تاريشت 📡</h2>
        <p style="font-size: 13px; color: #666;">أدخل اسمك لتظهر للأعضاء الآخرين على الخريطة</p>
        <input type="text" id="username" placeholder="مثلاً: مروان أو ياسين">
        <button onclick="initRadar()">دخول الرادار الجغرافي</button>
    </div>

    <div id="active-screen" style="display: none;">
        <div id="status-msg">
            <span class="pulse"></span> متصل الآن كـ: <b id="player-name"></b>
        </div>
        <div class="info-bar" id="nearby-status" style="display: block;">جاري البحث عن لاعبين...</div>
    </div>
</div>

<script>
    // 1. إعدادات قاعدة البيانات (Firebase Realtime Database)
    const firebaseConfig = {
        databaseURL: "https://tarisht-radar-default-rtdb.firebaseio.com/" 
    };
    firebase.initializeApp(firebaseConfig);
    const db = firebase.database();

    let map, myMarker, myName, myAvatar;
    let otherPlayers = {}; // لتخزين علامات اللاعبين الآخرين

    // 2. تهيئة الخريطة الأساسية
    map = L.map('map', { zoomControl: false }).setView([33.5731, -7.5898], 13);
    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
        attribution: '© OpenStreetMap'
    }).addTo(map);

    // 3. دالة الدخول وتفعيل الرادار
    function initRadar() {
        myName = document.getElementById('username').value.trim();
        if (!myName) return alert("من فضلك أدخل اسمك أولاً!");

        // توليد أفاتار فريد بناءً على الاسم
        myAvatar = `https://api.dicebear.com/7.x/avataaars/svg?seed=${encodeURIComponent(myName)}`;

        if (navigator.geolocation) {
            // تتبع الموقع اللحظي (Live Tracking)
            navigator.geolocation.watchPosition(updatePosition, handleError, {
                enableHighAccuracy: true,
                maximumAge: 0
            });

            // تحديث الواجهة
            document.getElementById('login-screen').style.display = 'none';
            document.getElementById('active-screen').style.display = 'block';
            document.getElementById('player-name').innerText = myName;

            // البدء في مراقبة اللاعبين الآخرين
            listenForOtherPlayers();
        } else {
            alert("عذراً، متصفحك لا يدعم خاصية تحديد الموقع GPS.");
        }
    }

    // 4. تحديث موقعي وإرساله لـ Firebase
    function updatePosition(position) {
        const lat = position.coords.latitude;
        const lng = position.coords.longitude;

        const myIcon = L.divIcon({
            className: 'avatar-marker',
            html: `<img src="${myAvatar}" alt="me">`,
            iconSize: [50, 50],
            iconAnchor: [25, 25]
        });

        if (!myMarker) {
            myMarker = L.marker([lat, lng], { icon: myIcon }).addTo(map).bindPopup("أنت").openPopup();
            map.setView([lat, lng], 16);
        } else {
            myMarker.setLatLng([lat, lng]);
        }

        // إرسال البيانات للمزامنة مع المتصفحات الأخرى
        db.ref('players/' + myName).set({
            name: myName,
            lat: lat,
            lng: lng,
            avatar: myAvatar,
            lastSeen: Date.now()
        });

        // حذف اللاعب تلقائياً عند إغلاق الصفحة
        db.ref('players/' + myName).onDisconnect().remove();
    }

    // 5. مراقبة المتصفحات الأخرى وعرضهم على الخريطة
    function listenForOtherPlayers() {
        db.ref('players').on('value', (snapshot) => {
            const players = snapshot.val();
            let count = 0;

            for (let id in players) {
                if (id === myName) continue; // تخطي اسمي أنا
                count++;

                const p = players[id];
                const otherIcon = L.divIcon({
                    className: 'avatar-marker',
                    html: `<img src="${p.avatar}" alt="${p.name}">`,
                    iconSize: [50, 50],
                    iconAnchor: [25, 25]
                });

                if (otherPlayers[id]) {
                    // تحديث مكان اللاعب إذا كان موجوداً مسبقاً
                    otherPlayers[id].setLatLng([p.lat, p.lng]);
                } else {
                    // إضافة لاعب جديد للخريطة
                    otherPlayers[id] = L.marker([p.lat, p.lng], { icon: otherIcon })
                        .addTo(map)
                        .bindPopup(`اللاعب: ${p.name}`);
                }
            }
            document.getElementById('nearby-status').innerText = `يوجد ${count} لاعبين بالقرب منك حالياً`;
        });
    }

    function handleError(error) {
        console.error(error);
        alert("يرجى التأكد من تفعيل الموقع الجغرافي واستخدام رابط آمن (HTTPS).");
    }
</script>

</body>
</html>
