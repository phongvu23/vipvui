<!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="UTF-8">
  <title>MD5 (BY SUNEO)</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #f8f8f8;
      color: #000;
      transition: background 0.5s ease, color 0.5s ease;
      padding: 40px;
      text-align: center;
    }
    input, button {
      padding: 8px;
      font-size: 16px;
      margin: 5px 4px;
      transition: background 0.5s ease, color 0.5s ease, border 0.5s ease;
    }
    input {
      width: 300px;
    }
    button {
      padding: 8px 12px;
      cursor: pointer;
      border: none;
      border-radius: 4px;
    }
    .box {
      background: #fff;
      border-radius: 8px;
      padding: 20px;
      margin: 20px auto;
      width: 500px;
      box-shadow: 0 0 10px rgba(0,0,0,0.1);
      transition: background 0.5s ease, color 0.5s ease;
    }
    .title {
      font-size: 18px;
      font-weight: bold;
      margin-bottom: 10px;
    }
    .result {
      margin-top: 12px;
      font-size: 18px;
      font-weight: bold;
    }
    .debug {
      margin-top: 6px;
      font-size: 14px;
      color: #555;
      display: none; /* ẩn mặc định */
    }

    .tai-theme {
      background: #ffe5e5;
      color: #900;
    }
    .tai-theme .box {
      background: #ffdddd;
      color: #900;
    }
    .tai-theme button {
      background: #ff9999;
      color: #600;
    }
    .tai-theme input {
      background: #fff0f0;
      color: #600;
      border: 1px solid #faa;
    }

    .xiu-theme {
      background: #e5f0ff;
      color: #003366;
    }
    .xiu-theme .box {
      background: #dceeff;
      color: #003366;
    }
    .xiu-theme button {
      background: #99ccff;
      color: #002244;
    }
    .xiu-theme input {
      background: #eef7ff;
      color: #002244;
      border: 1px solid #a9d4ff;
    }
  </style>
</head>
<body>
  <h1>Dự đoán MD5 By Suneo</h1>
  <div class="box">
    <div class="title">
      Nhập chuỗi MD5:
      <span id="clock" style="font-weight: normal; font-size: 16px; color: #555; display: block; margin-top: 4px;"></span>
    </div>
    <input type="text" id="md5_fixed" placeholder=" Chúc bạn may mắn " />

    <div class="title">Vị trí cắt (0-32):</div>
    <input type="number" id="start_index" min="0" max="31" style="width: 80px;" />
    <input type="number" id="end_index" min="1" max="32" style="width: 80px;" />
    <button onclick="saveCutDefaults()">💾 Lưu vị trí </button>
    
    <br><br>
    <button onclick="calculateFixed()">🎲 Dự đoán</button>
    <button onclick="toggleDebug()">👁️ Xem chi tiết</button>
    <button onclick="location.reload()">🔄 Làm mới</button>

    <div id="result_fixed" class="result"></div>
    <div id="debug_fixed" class="debug"></div>
  </div>

  <script>
    let lastDebugText = "";

    function calculateFixed() {
      const md5Input = document.getElementById("md5_fixed");
      const md5 = md5Input.value.trim().toLowerCase();
      const resultEl = document.getElementById("result_fixed");
      const debugEl = document.getElementById("debug_fixed");
      const start = parseInt(document.getElementById("start_index").value);
      const end = parseInt(document.getElementById("end_index").value);

      if (!/^[0-9a-f]{32}$/.test(md5)) {
        resultEl.innerText = "❌ Mã MD5 không hợp lệ.";
        debugEl.innerText = "";
        document.body.classList.remove("tai-theme", "xiu-theme");
        return;
      }

      if (start < 0 || end > 32 || start >= end) {
        resultEl.innerText = "⚠️ Vị trí cắt không hợp lệ.";
        debugEl.innerText = "";
        document.body.classList.remove("tai-theme", "xiu-theme");
        return;
      }

      const short = md5.substring(start, end);
      let value = parseInt(short, 16);
      const d1 = (value % 6) + 1;
      const d2 = (Math.floor(value / 6) % 6) + 1;
      const d3 = (Math.floor(value / 36) % 6) + 1;
      const sum = d1 + d2 + d3;
      const result = sum >= 11 ? "🔴 Tài nhé" : "🔵 Xỉu nhé";

      resultEl.innerText = result;
      lastDebugText = `🎲 Chi tiết: ${d1}-${d2}-${d3} = ${sum} (${short})`;
      if (debugEl.style.display !== "none") {
        debugEl.innerText = lastDebugText;
      } else {
        debugEl.innerText = "";
      }

      document.body.classList.remove("tai-theme", "xiu-theme");
      document.body.classList.add(sum >= 11 ? "tai-theme" : "xiu-theme");
    }

    function toggleDebug() {
      const debugEl = document.getElementById("debug_fixed");
      if (debugEl.style.display === "none") {
        debugEl.style.display = "block";
        debugEl.innerText = lastDebugText;
      } else {
        debugEl.style.display = "none";
      }
    }

    function updateClock() {
      const now = new Date();
      const dateStr = now.toLocaleDateString('vi-VN');
      const timeStr = now.toLocaleTimeString('vi-VN', { hour12: false });
      document.getElementById("clock").innerText = `${dateStr} ${timeStr}`;
    }

    function loadCutDefaults() {
      const savedStart = localStorage.getItem('md5_start_index');
      const savedEnd = localStorage.getItem('md5_end_index');
      document.getElementById("start_index").value = savedStart !== null ? savedStart : 1;
      document.getElementById("end_index").value = savedEnd !== null ? savedEnd : 7;
    }

    function saveCutDefaults() {
      const start = document.getElementById("start_index").value;
      const end = document.getElementById("end_index").value;
      if (start >= 0 && end <= 32 && parseInt(start) < parseInt(end)) {
        localStorage.setItem('md5_start_index', start);
        localStorage.setItem('md5_end_index', end);
        alert("✅ Đã lưu vị trí cắt mặc định!");
      } else {
        alert("⚠️ Vị trí không hợp lệ, không thể lưu.");
      }
    }

    async function readClipboardAndCalculate() {
      try {
        const text = await navigator.clipboard.readText();
        if (/^[0-9a-f]{32}$/i.test(text.trim())) {
          document.getElementById("md5_fixed").value = text.trim();
          calculateFixed();
        }
      } catch (err) {
        console.warn("Không đọc được clipboard:", err);
      }
    }

    document.addEventListener("DOMContentLoaded", () => {
      loadCutDefaults();
      updateClock();
      setInterval(updateClock, 1000);

      document.getElementById("md5_fixed").addEventListener("input", calculateFixed);
      setTimeout(readClipboardAndCalculate, 300);
    });
  </script>
</body>
</html>
