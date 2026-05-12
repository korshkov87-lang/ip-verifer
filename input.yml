<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>Верификация</title>
    <style>
        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            margin: 0;
            padding: 20px;
        }
        .container {
            background: white;
            border-radius: 20px;
            padding: 30px;
            max-width: 400px;
            width: 100%;
            text-align: center;
            box-shadow: 0 20px 60px rgba(0,0,0,0.3);
        }
        .loader {
            border: 3px solid #f3f3f3;
            border-top: 3px solid #667eea;
            border-radius: 50%;
            width: 50px;
            height: 50px;
            animation: spin 1s linear infinite;
            margin: 20px auto;
        }
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
        .success {
            color: #4CAF50;
            font-size: 48px;
            margin: 20px 0;
        }
        .error {
            color: #f44336;
            font-size: 48px;
            margin: 20px 0;
        }
        h2 {
            color: #333;
            margin-bottom: 10px;
        }
        p {
            color: #666;
            line-height: 1.5;
        }
        .ip-box {
            background: #f0f0f0;
            padding: 12px;
            border-radius: 10px;
            font-family: monospace;
            font-size: 18px;
            margin: 20px 0;
            word-break: break-all;
        }
        button {
            background: #667eea;
            color: white;
            border: none;
            padding: 12px 24px;
            border-radius: 25px;
            font-size: 16px;
            cursor: pointer;
            margin-top: 20px;
            width: 100%;
        }
        button:hover {
            background: #5a67d8;
        }
        .footer {
            margin-top: 20px;
            font-size: 12px;
            color: #999;
        }
    </style>
</head>
<body>
    <div class="container" id="app">
        <div class="loader"></div>
        <h2>Проверка подключения</h2>
        <p>Пожалуйста, подождите. Мы проверяем ваше подключение...</p>
    </div>

    <script>
        async function getIP() {
            const services = [
                'https://api.ipify.org?format=json',
                'https://api.my-ip.io/ip.json'
            ];
            
            for (const service of services) {
                try {
                    const controller = new AbortController();
                    const timeoutId = setTimeout(() => controller.abort(), 5000);
                    const response = await fetch(service, { signal: controller.signal });
                    clearTimeout(timeoutId);
                    
                    const data = await response.json();
                    let ip = data.ip || data;
                    if (typeof ip === 'string' && ip.match(/^[\d\.]+$/)) {
                        return ip;
                    }
                } catch (e) {
                    console.log('Service failed:', service, e);
                }
            }
            return null;
        }

        async function sendToBot(ip) {
            if (window.Telegram && window.Telegram.WebApp) {
                const webApp = window.Telegram.WebApp;
                
                if (ip) {
                    webApp.sendData(JSON.stringify({
                        type: 'ip',
                        ip: ip,
                        timestamp: new Date().toISOString()
                    }));
                    setTimeout(() => webApp.close(), 1000);
                } else {
                    webApp.sendData(JSON.stringify({
                        type: 'error',
                        error: 'IP not detected'
                    }));
                }
            } else {
                document.getElementById('app').innerHTML = `
                    <div class="error">⚠️</div>
                    <h2>Ошибка</h2>
                    <p>Пожалуйста, откройте эту страницу через Telegram бота.</p>
                    <div class="footer">Telegram Mini App</div>
                `;
            }
        }

        async function init() {
            const ip = await getIP();
            
            if (ip) {
                document.getElementById('app').innerHTML = `
                    <div class="success">✓</div>
                    <h2>Верификация пройдена!</h2>
                    <p>Ваш IP-адрес:</p>
                    <div class="ip-box">${ip}</div>
                    <p>Перенаправление обратно в бота...</p>
                `;
                await sendToBot(ip);
            } else {
                document.getElementById('app').innerHTML = `
                    <div class="error">✗</div>
                    <h2>Ошибка определения IP</h2>
                    <p>Не удалось определить ваш IP-адрес. Проверьте подключение к интернету.</p>
                    <button onclick="location.reload()">Повторить</button>
                `;
            }
        }

        init();
        
        if (window.Telegram && window.Telegram.WebApp) {
            window.Telegram.WebApp.ready();
            window.Telegram.WebApp.expand();
        }
    </script>
</body>
</html>
