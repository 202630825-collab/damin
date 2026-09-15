<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>날씨 맞춤 옷 추천 앱</title>
    <style>
        body { font-family: sans-serif; text-align: center; background: #f4f7f6; padding: 30px; }
        .card { background: white; max-width: 350px; margin: 0 auto; padding: 20px; border-radius: 15px; box-shadow: 0 4px 10px rgba(0,0,0,0.1); }
        .temp { font-size: 2.5rem; font-weight: bold; margin: 10px 0; }
        .outfit-box { background: #eef2f5; padding: 15px; border-radius: 10px; margin-top: 15px; }
        .emojis { font-size: 3rem; margin-top: 15px; }
    </style>
</head>
<body>

<div class="card">
    <h2 id="location">위치 불러오는 중...</h2>
    <div class="temp" id="temp">--°C</div>
    <p id="weather-desc">날씨 정보를 가져오고 있습니다.</p>

    <div class="outfit-box">
        <h3>오늘의 추천 코디</h3>
        <p id="outfit-text">분석 중...</p>
    </div>

    <div class="emojis" id="emoji-box">⏳</div>
</div>

<script>
// 1. 기온별 옷차림 데이터 정의
function getOutfitAdvice(temp) {
    if (temp >= 28) {
        return { text: "민소매, 반팔, 반바지, 린넨 옷", emojis: "☀️ 🎽 🩳 🩴" };
    } else if (temp >= 23) {
        return { text: "반팔, 얇은 셔츠, 반바지, 면바지", emojis: "🌤️ 👕 👖 👟" };
    } else if (temp >= 20) {
        return { text: "블라우스, 긴팔 티, 면바지, 슬랙스", emojis: "⛅ 👔 👖 👞" };
    } else if (temp >= 17) {
        return { text: "얇은 가디건, 니트, 맨투맨, 청바지", emojis: "☁️ 🧥 👕 👖" };
    } else if (temp >= 12) {
        return { text: "자켓, 가디건, 야상, 스타킹", emojis: "🍂 🧥 👔 👖" };
    } else if (temp >= 9) {
        return { text: "트렌치코트, 야상, 니트, 청바지", emojis: "🌬️ 🧥 🧶 👖" };
    } else if (temp >= 5) {
        return { text: "울 코트, 히트텍, 가죽 자켓", emojis: "🍁 🧥 🧣 👖" };
    } else {
        return { text: "패딩, 두꺼운 코트, 목도리, 장갑", emojis: "❄️ 🧥 🧣 🧤" };
    }
}

// 2. Open-Meteo 무료 API로 날씨 정보 가져오기
function fetchWeather(lat, lon) {
    const url = `https://api.open-meteo.com/v1/forecast?latitude=${lat}&longitude=${lon}&current_weather=true`;

    fetch(url)
        .then(response => response.json())
        .then(data => {
            const temp = Math.round(data.current_weather.temperature);
            const outfit = getOutfitAdvice(temp);

            document.getElementById('location').innerText = "현재 위치 날씨";
            document.getElementById('temp').innerText = `${temp}°C`;
            document.getElementById('weather-desc').innerText = `현재 기온은 ${temp}도입니다.`;
            document.getElementById('outfit-text').innerText = outfit.text;
            document.getElementById('emoji-box').innerText = outfit.emojis;
        })
        .catch(() => {
            document.getElementById('weather-desc').innerText = "날씨 정보를 가져올 수 없습니다.";
        });
}

// 3. 사용자 위치(GPS) 파악
if (navigator.geolocation) {
    navigator.geolocation.getCurrentPosition(
        pos => fetchWeather(pos.coords.latitude, pos.coords.longitude),
        () => {
            // 위치 권한 거부 시 서울 기준(37.5665, 126.9780) 기본 설정
            fetchWeather(37.5665, 126.9780);
        }
    );
} else {
    fetchWeather(37.5665, 126.9780);
}
</script>

</body>
</html>
