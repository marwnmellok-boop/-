<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>مسرع الإنترنت الذكي</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        :root {
            --primary-color: #2980b9;
            --secondary-color: #2c3e50;
            --bg-color: #f4f7f6;
            --card-bg: #ffffff;
            --text-color: #333;
            --success-color: #27ae60;
            --danger-color: #e74c3c;
            --orange-accent: #f39c12;
        }

        * { box-sizing: border-box; margin: 0; padding: 0; }
        body { font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; background-color: var(--bg-color); color: var(--text-color); display: flex; justify-content: center; align-items: center; min-height: 100vh; padding: 20px; }

        .container { 
            background-color: var(--card-bg); 
            padding: 30px; 
            border-radius: 20px; 
            box-shadow: 0 10px 30px rgba(0,0,0,0.15); 
            width: 100%; 
            max-width: 450px; 
            text-align: center; 
            position: relative;
        }

        /* --- نظام الإشعارات المدمج --- */
        .header-actions {
            display: flex;
            justify-content: flex-end;
            position: absolute;
            top: 20px;
            left: 20px;
            z-index: 1000;
        }

        .notification-bell {
            position: relative;
            cursor: pointer;
            font-size: 1.4rem;
            color: var(--secondary-color);
        }

        .bell-ring { animation: ring 2s ease-in-out infinite; transform-origin: top center; }
        @keyframes ring { 0%, 100% { transform: rotate(0); } 10%, 30%, 50%, 70%, 90% { transform: rotate(15deg); } 20%, 40%, 60%, 80% { transform: rotate(-15deg); } }

        .badge {
            position: absolute;
            top: -5px;
            right: -5px;
            background: var(--danger-color);
            color: white;
            border-radius: 50%;
            padding: 2px 6px;
            font-size: 0.7rem;
            font-weight: bold;
            border: 2px solid white;
        }

        .profile-popup {
            display: none;
            position: absolute;
            top: 40px;
            right: 0;
            background: white;
            border-radius: 15px;
            box-shadow: 0 15px 35px rgba(0,0,0,0.2);
            width: 280px;
            padding: 20px;
            border: 1px solid #eee;
            animation: fadeIn 0.3s ease-out;
            text-align: right;
        }

        @keyframes fadeIn { from { opacity: 0; transform: translateY(-10px); } to { opacity: 1; transform: translateY(0); } }

        .profile-header {
            display: flex;
            align-items: center;
            gap: 10px;
            margin-bottom: 15px;
            padding-bottom: 10px;
            border-bottom: 1px solid #f0f0f0;
        }

        .robot-circle {
            width: 35px;
            height: 35px;
            background: #e8f5e9;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            color: var(--success-color);
            font-size: 1rem;
            border: 1px solid var(--success-color);
            position: relative;
            overflow: hidden;
        }

        .owner-name { color: var(--orange-accent); font-weight: bold; font-size: 1rem; }

        .system-msg {
            font-size: 0.85rem;
            color: #555;
            line-height: 1.5;
            background: #fffcf0;
            padding: 10px;
            border-radius: 8px;
            border-right: 3px solid var(--orange-accent);
        }

        .contact-link {
            display: block;
            margin-top: 10px;
            color: var(--success-color);
            text-decoration: none;
            font-weight: bold;
            font-size: 0.9rem;
        }

        .stars-container { position: absolute; top: 0; left: 0; width: 100%; height: 100%; pointer-events: none; overflow: hidden; border-radius: 15px;}
        .star { position: absolute; background: #f1c40f; width: 4px; height: 4px; border-radius: 50%; animation: twinkle 1.5s infinite; }
        @keyframes twinkle { 0%, 100% { opacity: 0; } 50% { opacity: 1; } }

        /* --- واجهة قياس السرعة --- */
        h1 { font-size: 1.5rem; margin-top: 20px; margin-bottom: 25px; color: var(--secondary-color); }
        .gauge-container { position: relative; width: 200px; height: 100px; margin: 0 auto 30px auto; overflow: hidden; }
        .gauge-body { position: absolute; top: 0; left: 0; width: 100%; height: 200%; border-radius: 50%; background-color: #dfe6e9; border: 20px solid #eee; border-bottom-color: var(--primary-color); border-right-color: var(--primary-color); transform: rotate(45deg); transition: transform 1s cubic-bezier(0.17, 0.67, 0.83, 0.67); }
        .gauge-cover { position: absolute; width: 80%; height: 160%; background-color: var(--card-bg); border-radius: 50%; top: 10%; left: 10%; display: flex; align-items: center; justify-content: center; padding-bottom: 80%; }
        #speed-text { font-size: 2.5rem; font-weight: bold; color: var(--secondary-color); position: absolute; bottom: 75px; }
        .stats-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; margin-bottom: 20px; }
        .stat-card { background-color: #f9f9f9; padding: 12px; border-radius: 12px; border: 1px solid #eee; display: flex; align-items: center; gap: 8px; }
        .stat-card i { color: var(--primary-color); }
        .btn { width: 100%; padding: 15px; background: linear-gradient(135deg, var(--primary-color), #2980b9); color: white; border: none; border-radius: 12px; font-size: 1.1rem; font-weight: bold; cursor: pointer; transition: 0.3s; }
        .scanning { animation: pulse 1.5s infinite; }
        @keyframes pulse { 0% { box-shadow: 0 0 0 0 rgba(52, 152, 219, 0.4); } 70% { box-shadow: 0 0 0 15px rgba(52, 152, 219, 0); } }
    </style>
</head>
<body>

<div class="container">
    <div class="header-actions">
        <div class="notification-bell bell-ring" id="bell-container" onclick="toggleNotif()">
            <i class="fas fa-bell"></i>
            <span class="badge" id="notif-badge">1</span>
        </div>
        
        <div class="profile-popup" id="notif-popup">
            <div class="stars-container" id="stars-box"></div>
            <div class="profile-header">
                <div class="robot-circle">
                    <i class="fab fa-android"></i>
                </div>
                <div>
                    <span class="owner-name">مروان ملوك</span>
                    <div style="font-size: 0.7rem; color: #888;">مدير النظام</div>
                </div>
            </div>
            <div class="system-msg">
                السلام عليكم ورحمة الله وبركاته، أنا صاحب الموقع. إذا كنت تريد شراء عضوية ثمنها 5 آلاف درهم راسلنا على:
                <a href="tel:0679872832" class="contact-link"><i class="fas fa-phone-alt"></i> 0679872832</a>
            </div>
        </div>
    </div>

    <h1><i class="fas fa-tachometer-alt" style="color:var(--primary-color)"></i> مسرع وسرعة الإنترنت</h1>
    
    <div class="gauge-container" id="gauge-zone">
        <div class="gauge-body" id="gauge-body"></div>
        <div class="gauge-cover">
            <span id="speed-text">0.0</span>
        </div>
    </div>

    <div class="stats-grid">
        <div class="stat-card" style="grid-column: span 2;">
            <i id="batt-icon" class="fas fa-battery-full"></i>
            <div style="text-align: right">
                <div style="font-size: 0.7rem; color: #7f8c8d;">الطاقة</div>
                <div id="battery-level" style="font-weight: bold;">--%</div>
            </div>
        </div>
        <div class="stat-card">
            <i class="fas fa-wifi"></i>
            <div style="text-align: right">
                <div style="font-size: 0.7rem; color: #7f8c8d;">الشبكة</div>
                <div id="network-type" style="font-weight: bold; font-size: 0.8rem;">نشط</div>
            </div>
        </div>
        <div class="stat-card">
            <i class="fas fa-microchip"></i>
            <div style="text-align: right">
                <div style="font-size: 0.7rem; color: #7f8c8d;">النظام</div>
                <div id="sys-status" style="font-weight: bold; font-size: 0.8rem;">مستقر</div>
            </div>
        </div>
    </div>

    <button class="btn" id="start-btn" onclick="startSpeedTest()">
        <i class="fas fa-rocket"></i> تحسين وزيادة السرعة الآن
    </button>
</div>

<script>
    const notifSound = new Audio('https://assets.mixkit.co/active_storage/sfx/2869/2869-preview.mp3');

    window.onload = () => {
        const starsBox = document.getElementById('stars-box');
        for(let i=0; i<20; i++) {
            const star = document.createElement('div');
            star.className = 'star';
            star.style.top = Math.random() * 100 + '%';
            star.style.left = Math.random() * 100 + '%';
            star.style.animationDelay = Math.random() * 2 + 's';
            starsBox.appendChild(star);
        }
        
        document.body.addEventListener('click', () => {
            if(document.getElementById('notif-badge').style.display !== 'none') {
                notifSound.play().catch(()=>{});
            }
        }, {once: true});
    };

    function toggleNotif() {
        const popup = document.getElementById('notif-popup');
        const badge = document.getElementById('notif-badge');
        const bell = document.getElementById('bell-container');
        
        const isVisible = popup.style.display === 'block';
        popup.style.display = isVisible ? 'none' : 'block';
        
        if(!isVisible) {
            badge.style.display = 'none';
            bell.classList.remove('bell-ring');
        }
    }

    // وظيفة قياس سرعة الإنترنت الحقيقية
    function startSpeedTest() {
        const btn = document.getElementById('start-btn');
        const gauge = document.getElementById('gauge-body');
        const text = document.getElementById('speed-text');
        const zone = document.getElementById('gauge-zone');
        const sysStatus = document.getElementById('sys-status');
        
        btn.disabled = true;
        btn.innerHTML = `<i class="fas fa-spinner fa-spin"></i> جاري القياس الحقيقي...`;
        zone.classList.add('scanning');
        sysStatus.innerText = "جاري التحميل...";

        // رابط صورة للاختبار (حجمها معروف تقريباً 5 ميجابايت لضمان دقة الحساب)
        const imageAddr = "https://upload.wikimedia.org/wikipedia/commons/4/4e/Pleiades_large.jpg?n=" + Math.random();
        const downloadSize = 5211913; // 5.2 MB تقريباً بالبايت
        
        const download = new Image();
        const startTime = new Date().getTime();

        download.onload = function () {
            const endTime = new Date().getTime();
            const duration = (endTime - startTime) / 1000; // الوقت بالثواني
            const bitsLoaded = downloadSize * 8;
            const speedBps = bitsLoaded / duration;
            const speedKbps = speedBps / 1024;
            const speedMbps = (speedKbps / 1024).toFixed(1);

            // تحديث الواجهة بالنتيجة الحقيقية
            text.innerText = speedMbps;
            const deg = 45 + (Math.min(speedMbps, 100) / 100) * 180;
            gauge.style.transform = `rotate(${deg}deg)`;
            
            finishTest(speedMbps);
        };

        download.onerror = function () {
            text.innerText = "Err";
            btn.disabled = false;
            btn.innerHTML = `<i class="fas fa-redo"></i> فشل القياس، حاول ثانية`;
            zone.classList.remove('scanning');
            sysStatus.innerText = "خطأ في الاتصال";
        };

        download.src = imageAddr;
    }

    function finishTest(speed) {
        const btn = document.getElementById('start-btn');
        const status = document.getElementById('sys-status');
        const zone = document.getElementById('gauge-zone');
        
        btn.innerHTML = `<i class="fas fa-check-circle"></i> تم القياس: ${speed} Mbps`;
        status.innerText = speed > 10 ? "أداء ممتاز" : "أداء مستقر";
        status.style.color = "var(--success-color)";
        zone.classList.remove('scanning');
        
        setTimeout(() => {
            btn.disabled = false;
            btn.innerHTML = `<i class="fas fa-rocket"></i> تحسين وزيادة السرعة الآن`;
        }, 5000);
    }

    if ('getBattery' in navigator) {
        navigator.getBattery().then(batt => {
            const update = () => {
                document.getElementById('battery-level').innerText = Math.round(batt.level * 100) + '%';
                document.getElementById('batt-icon').style.color = batt.level > 0.2 ? "var(--success-color)" : "var(--danger-color)";
            };
            update();
            batt.onlevelchange = update;
        });
    }
</script>

</body>
</html>
