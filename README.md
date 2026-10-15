
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <title>Mini Happy Farm - Nông Trại Vui Vẻ</title>
    <style>
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            text-align: center;
            background: linear-gradient(to bottom, #c8e6c9, #e8f5e9);
            margin: 0;
            padding: 20px;
        }
        h1 { color: #2e7d32; margin-bottom: 5px; }
        
        /* Khu vực nhân vật nông dân */
        #farmer-container {
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 15px;
            background: #ffffff;
            max-width: 350px;
            margin: 15px auto;
            padding: 10px 20px;
            border-radius: 20px;
            box-shadow: 0 4px 6px rgba(0,0,0,0.1);
        }
        #farmer-avatar { font-size: 40px; }
        #farmer-dialog { text-align: left; font-size: 14px; color: #333; }

        #stats {
            font-size: 18px;
            font-weight: bold;
            color: #388e3c;
            margin: 15px 0;
        }

        /* Bảng chọn hạt giống */
        #shop {
            margin: 15px 0;
        }
        .seed-btn {
            background-color: #81c784;
            border: none;
            padding: 8px 15px;
            margin: 0 5px;
            border-radius: 8px;
            cursor: pointer;
            font-weight: bold;
            color: white;
            transition: 0.2s;
        }
        .seed-btn.active {
            background-color: #2e7d32;
            border: 2px solid #fff;
        }
        .seed-btn:hover { opacity: 0.9; }

        /* Lưới ô đất */
        #farm {
            display: grid;
            grid-template-columns: repeat(3, 120px);
            gap: 15px;
            justify-content: center;
            margin: 20px auto;
        }
        .plot {
            width: 120px;
            height: 120px;
            background-color: #8d6e63;
            border: 4px solid #5d4037;
            border-radius: 12px;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            font-size: 15px;
            font-weight: bold;
            color: #fff;
            cursor: pointer;
            user-select: none;
            transition: 0.2s;
            box-shadow: 0 4px 6px rgba(0,0,0,0.15);
        }
        .plot:hover {
            transform: scale(1.05);
        }
        .instruction {
            font-size: 13px;
            color: #555;
            margin-top: 10px;
        }
    </style>
</head>
<body>

    <h1>🌾 Mini Happy Farm 🌾</h1>
    
    <!-- Nhân vật quản lý nông trại -->
    <div id="farmer-container">
        <div id="farmer-avatar">👨‍🌾</div>
        <div id="farmer-dialog">
            <b>Bác nông dân Tín:</b><br>
            <span id="message">Chào mừng bạn đến nông trại! Hãy chọn hạt giống và trồng nhé.</span>
        </div>
    </div>

    <div id="stats">Tiền vàng: <span id="gold">100</span> 🪙</div>

    <!-- Chọn hạt giống -->
    <div id="shop">
        <span>Chọn hạt: </span>
        <button class="seed-btn active" id="btn-rice" onclick="selectSeed('rice')">🌾 Lúa (Giá: 5🪙)</button>
        <button class="seed-btn" id="btn-tomato" onclick="selectSeed('tomato')">🍅 Cà chua (Giá: 10🪙)</button>
    </div>

    <div id="farm">
        <div class="plot" id="plot-0" onclick="handlePlot(0)">Đất trống</div>
        <div class="plot" id="plot-1" onclick="handlePlot(1)">Đất trống</div>
        <div class="plot" id="plot-2" onclick="handlePlot(2)">Đất trống</div>
    </div>

    <div class="instruction">💡 <b>Cách chơi:</b> Click ô đất trống để trồng $\rightarrow$ Click lần nữa để <b>tưới nước</b> $\rightarrow$ Chờ cây lớn để thu hoạch!</div>

    <script>
        let gold = 100;
        let selectedSeed = 'rice'; // 'rice' hoặc 'tomato'

        // Trạng thái ô đất:
        // 0: Đất trống
        // 1: Đã gieo hạt (Chờ tưới nước)
        // 2: Đang lớn (Đã tưới nước)
        // 3: Đã chín (Thu hoạch)
        let plots = [
            { state: 0, type: '', timer: null },
            { state: 0, type: '', timer: null },
            { state: 0, type: '', timer: null }
        ];

        function setMsg(text) {
            document.getElementById("message").innerText = text;
        }

        function selectSeed(type) {
            selectedSeed = type;
            document.getElementById("btn-rice").classList.remove("active");
            document.getElementById("btn-tomato").classList.remove("active");
            if(type === 'rice') {
                document.getElementById("btn-rice").classList.add("active");
                setMsg("Đã chọn hạt giống Lúa. Sẵn sàng gieo trồng!");
            } else {
                document.getElementById("btn-tomato").classList.add("active");
                setMsg("Đã chọn hạt giống Cà chua. Loại này thu hoạch lời hơn đấy!");
            }
        }

        function updateView() {
            document.getElementById("gold").innerText = gold;
            
            for (let i = 0; i < plots.length; i++) {
                let el = document.getElementById("plot-i" in window ? ... : "plot-" + i); // fix selector
                let plotEl = document.getElementById("plot-" + i);
                let p = plots[i];

                if (p.state === 0) {
                    plotEl.innerHTML = "🌱<br>Đất trống";
                    plotEl.style.backgroundColor = "#8d6e63";
                } else if (p.state === 1) {
                    plotEl.innerHTML = "💧<br>Cần tưới nước!";
                    plotEl.style.backgroundColor = "#a1887f";
                } else if (p.state === 2) {
                    let name = p.type === 'rice' ? '🌾 Lúa' : '🍅 Cà chua';
                    plotEl.innerHTML = "🌿<br>" + name + " đang lớn";
                    plotEl.style.backgroundColor = "#c8e6c9";
                } else if (p.state === 3) {
                    let name = p.type === 'rice' ? '🌾 Lúa' : '🍅 Cà chua';
                    plotEl.innerHTML = "✨<br>" + name + " chín!";
                    plotEl.style.backgroundColor = "#fff59d";
                }
            }
        }

        function handlePlot(index) {
            let p = plots[index];

            if (p.state === 0) {
                // Trồng cây
                let cost = selectedSeed === 'rice' ? 5 : 10;
                if (gold >= cost) {
                    gold -= cost;
                    p.state = 1; // Chờ tưới nước
                    p.type = selectedSeed;
                    setMsg("Đã gieo hạt! Hãy bấm vào ô này lần nữa để tưới nước nhé.");
                    updateView();
                } else {
                    setMsg("Oẹc! Bạn không đủ tiền vàng để mua hạt giống này.");
                }
            } 
            else if (p.state === 1) {
                // Tưới nước
                p.state = 2; // Bắt đầu lớn
                setMsg("Đã tưới nước! Cây đang phát triển...");
                updateView();

                // Thời gian sinh trưởng (4 giây)
                let growTime = p.type === 'rice' ? 4000 : 6000;
                p.timer = setTimeout(() => {
                    p.state = 3; // Chín
                    setMsg("Yay! Cây đã chín rồi, mau thu hoạch nào!");
                    updateView();
                }, growTime);
            } 
            else if (p.state === 2) {
                setMsg("Cây đang lớn, bạn đừng nôn nóng quá!");
            } 
            else if (p.state === 3) {
                // Thu hoạch
                let reward = p.type === 'rice' ? 15 : 30;
                gold += reward;
                p.state = 0;
                p.type = '';
                clearTimeout(p.timer);
                setMsg("Thu hoạch thành công! Bạn nhận được " + reward + " vàng.");
                updateView();
            }
        }
    </script>
</body>
</html>
