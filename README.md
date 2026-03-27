<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>لوحة تحكم الشبكة والنظام</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        :root {
            --primary-color: #3498db;
            --secondary-color: #2c3e50;
            --bg-color: #ecf0f1;
            --card-bg: #ffffff;
            --text-color: #333;
            --sucess-color: #2ecc71;
        }

        * { box-sizing: border-box; margin: 0; padding: 0; }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: var(--bg-color);
            color: var(--text-color);
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            padding: 20px;
        }

        .container {
            background-color: var(--card-bg);
            padding: 30px;
            border-radius: 20px;
            box-shadow: 0 10px 25px rgba(0,0,0,0.1);
            width: 100%;
            max-width: 450px;
            text-align: center;
        }

        h1 {
            font-size: 1.5rem;
            margin-bottom: 25px;
            color: var(--secondary-color);
        }

        /* تصميم مقياس السرعة (الردار) */
        .gauge-container {
            position: relative;
            width: 200px;
            height: 100px; /* نصف دائرة */
            margin: 0 auto 30px auto;
            overflow: hidden; /* لإخفاء النصف السفلي للدائرة */
        }

        .gauge-body {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 200%;
            border-radius: 50%;
            background-color: #ddd;
            border: 20px solid #eee;
            border-bottom-color: var(--primary-color);
            border-right-color: var(--primary-color);
            transform: rotate(45deg); /* نقطة البداية الصفرية */
            transition: transform 0.5s ease-out;
        }

        .gauge-cover {
            position: absolute;
            width: 80%;
            height: 160%;
            background-color: var(--card-bg);
            border-radius: 50%;
            top: 10%;
            left: 10%;
            display: flex;
            align-items: center;
            justify-content: center;
            padding-bottom: 80%; /* دفع النص للأعلى */
        }

        .gauge-cover::after {
            content: "Mbps";
            position: absolute;
            bottom: 65px;
            font-size: 0.8rem;
            color: #7f8c8d;
        }

        #speed-text {
            font-size: 2.5rem;
            font-weight: bold;
            color: var(--secondary-color);
            position: absolute;
            bottom: 75px;
        }

        /* تصميم قسم الإحصائيات */
        .stats-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 15px;
            margin-bottom: 25px;
        }

        .stat-card {
            background-color: #f8f9fa;
            padding: 15px;
            border-radius: 12px;
            border: 1px solid #eee;
            display: flex;
            align-items: center;
            gap: 10px;
        }

        .stat-card i {
            font-size: 1.5rem;
            color: var(--primary-color);
            width: 30px;
            text-align: center;
        }

        .stat-info { display: flex; flex-direction: column; align-items: flex-start; }
        .stat-label { font-size: 0.8rem; color: #7f8c8d; }
        .stat-value { font-weight: bold; color: var(--secondary-color); font-size: 0.95rem;}

        /* الأزرار والملاحظات */
        .btn {
            width: 100%;
            padding: 12px;
            background-color: var(--primary-color);
            color: white;
            border: none;
            border-radius: 10px;
            font-size: 1rem;
            font-weight: bold;
            cursor: pointer;
            transition: background 0.3s;
        }

        .btn:hover { background-color: #2980b9; }
        .btn:disabled { background-color: #bdc3c7; cursor: not-allowed; }

        .note {
            font-size: 0.75rem;
            color: #95a5a6;
            margin-top: 20px;
            line-height: 1.4;
        }

        /* تأثير الحركة للردار عند الفحص */
        @keyframes radar-pulse {
            0% { box-shadow: 0 0 0 0 rgba(52, 152, 219, 0.4); }
            70% { box-shadow: 0 0 0 15px rgba(52, 152, 219, 0); }
            100% { box-shadow: 0 0 0 0 rgba(52, 152, 219, 0); }
        }
        .scanning { animation: radar-pulse 1.5s infinite; border-radius: 50%; }

    </style>
</head>
<body>

<div class="container">
    <h1><i class="fas fa-satellite-dish"></i> داشبورد النظام</h1>
    
    <div class="gauge-container" id="gauge-zone">
        <div class="gauge-body" id="gauge-body"></div>
        <div class="gauge-cover">
            <span id="speed-text">0.0</span>
        </div>
    </div>

    <div class="stats-grid">
        <div class="stat-card">
            <i class="fas fa-battery-three-quarters" id="batt-icon"></i>
            <div class="stat-info">
                <span class="stat-label">البطارية</span>
                <span class="stat-value" id="battery-level">--</span>
            </div>
        </div>
        <div class="stat-card">
            <i class="fas fa-network-wired"></i>
            <div class="stat-info">
                <span class="stat-label">نوع الشبكة</span>
                <span class="stat-value" id="network-type">--</span>
            </div>
        </div>
    </div>

    <button class="btn" id="start-btn" onclick="runSpeedTest()">
        <i class="fas fa-play"></i> ابدأ فحص السرعة
    </button>
    
    <div class="note">
        <i class="fas fa-info-circle"></i> تنبيه تقني: قياس السرعة تقريبي. الوصول لسجل تصفحك أو استهلاك المواقع الأخرى محظور أمنياً داخل المتصفح.
    </div>
</div>

<script>
    // --- 1. وظائف البطارية (Battery API) ---
    function updateBatteryInfo(battery) {
        const levelElement = document.getElementById('battery-level');
        const iconElement = document.getElementById('batt-icon');
        const level = Math.round(battery.level * 100);
        
        levelElement.innerText = level + '%';
        
        // تغيير الأيقونة بناءً على المستوى
        if (battery.charging) {
            iconElement.className = "fas fa-battery-charging";
            iconElement.style.color = "var(--sucess-color)";
        } else if (level <= 20) {
            iconElement.className = "fas fa-battery-empty";
            iconElement.style.color = "#e74c3c"; // أحمر للبطارية المنخفضة
        } else {
            iconElement.className = "fas fa-battery-three-quarters";
            iconElement.style.color = "var(--primary-color)";
        }
    }

    if ('getBattery' in navigator) {
        navigator.getBattery().then(function(battery) {
            updateBatteryInfo(battery);
            // تحديث عند التغيير
            battery.addEventListener('levelchange', () => updateBatteryInfo(battery));
            battery.addEventListener('chargingchange', () => updateBatteryInfo(battery));
        });
    }

    // --- 2. وظائف الشبكة (Network Information API) ---
    const connection = navigator.connection || navigator.mozConnection || navigator.webkitConnection;
    function updateNetworkInfo() {
        const typeElement = document.getElementById('network-type');
        if (connection) {
            // effectiveType تعطي فكرة عن السرعة (4g, 3g, etc.)
            typeElement.innerText = connection.effectiveType.toUpperCase();
        } else {
            typeElement.innerText = "غير معروف";
        }
    }
    updateNetworkInfo();
    if (connection) {
        connection.addEventListener('change', updateNetworkInfo);
    }

    // --- 3. وظائف مقياس السرعة والردار ---
    function setGaugeValue(value) {
        // نعتبر أن أقصى سرعة للمقياس هي 100 Mbps للتصميم
        const maxSpeedForGauge = 100; 
        const speed = Math.min(value, maxSpeedForGauge);
        
        // تحويل السرعة لدرجات دوران (من 45 درجة إلى 225 درجة)
        // 45 deg = 0 Mbps, 225 deg = 100+ Mbps
        const degree = 45 + (speed / maxSpeedForGauge) * 180;
        
        document.getElementById('gauge-body').style.transform = `rotate(${degree}deg)`;
        document.getElementById('speed-text').innerText = value.toFixed(1);
    }

    // --- 4. فحص السرعة (تحميل صورة حقيقية) ---
    function runSpeedTest() {
        const startBtn = document.getElementById('start-btn');
        const speedText = document.getElementById('speed-text');
        const gaugeZone = document.getElementById('gauge-zone');

        // تجهيز الواجهة للفحص
        startBtn.disabled = true;
        startBtn.innerHTML = `<i class="fas fa-spinner fa-spin"></i> جاري الفحص...`;
        speedText.innerText = "0.0";
        setGaugeValue(0);
        gaugeZone.classList.add('scanning'); // بدء حركة الردار

        // رابط صورة كبيرة نوعاً ما (حوالي 5 ميجابايت) لضمان دقة القياس
        // نستخدم رابط عشوائي لتجنب الكاش (Cache)
        const imageAddr = "https://upload.wikimedia.org/wikipedia/commons/4/4e/Pleiades_large.jpg?n=" + Math.random();
        const download = new Image();
        let startTime, endTime;

        startTime = new Date().getTime();

        download.onload = function () {
            endTime = new Date().getTime();
            showResults(startTime, endTime);
        };

        download.onerror = function () {
            speedText.innerText = "خطأ";
            resetUI();
        }

        download.src = imageAddr;
    }

    function showResults(startTime, endTime) {
        const duration = (endTime - startTime) / 1000; // بالثواني
        const bitsLoaded = 5211913 * 8; // حجم الصورة الفعلي بالبِت (بناءً على رابط ويكيبيديا)
        const speedBps = bitsLoaded / duration;
        const speedMbps = (speedBps / (1024 * 1024));

        // تحديث العداد الفني
        setGaugeValue(speedMbps);
        resetUI();
    }

    function resetUI() {
        const startBtn = document.getElementById('start-btn');
        const gaugeZone = document.getElementById('gauge-zone');
        startBtn.disabled = false;
        startBtn.innerHTML = `<i class="fas fa-play"></i> ابدأ فحص السرعة`;
        gaugeZone.classList.remove('scanning'); // إيقاف حركة الردار
    }
</script>

</body>
</html>
