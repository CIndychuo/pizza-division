<!DOCTYPE html>
<html lang="zh">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>🍭 甜心大厨：零 Bug 完美派对 🍕</title>
    <style>
        :root { --p1: #ff75a0; --p2: #64b5f6; --gold: #ffd54f; }
        body {
            margin: 0; padding: 0; overflow: hidden; height: 100vh; width: 100vw;
            background: linear-gradient(180deg, #fff5f7 0%, #eef7ff 100%);
            font-family: 'ZCOOL KuaiLe', 'Microsoft YaHei', sans-serif;
            display: flex; flex-direction: column;
        }

        /* 顶部状态栏 (15vh) */
        .header {
            height: 15vh; display: flex; align-items: center; justify-content: space-between;
            padding: 0 40px; background: rgba(255,255,255,0.85); border-bottom: 4px solid #ffeef2; z-index: 100;
        }
        .energy-box { flex: 2; text-align: center; }
        .candy-bar {
            width: 250px; height: 24px; background: #eee; border-radius: 12px;
            margin: 5px auto; overflow: hidden; border: 3px solid #fff; box-shadow: 0 4px 8px rgba(0,0,0,0.05);
        }
        #rainbow-fill { width: 100%; height: 100%; background: linear-gradient(90deg, #ff9a9e, #a18ed1, #84fab0); transition: width 1s linear; }
        .star-score { flex: 1; font-size: 30px; display: flex; gap: 5px; align-items: center; }

        /* 中间舞台 (42vh) 保持原样 */
        .stage {
            height: 42vh; position: relative; display: flex; flex-direction: column;
            justify-content: center; align-items: center; z-index: 10;
        }
        .pizza-layout { display: flex; width: 100%; justify-content: space-around; align-items: center; }
        .plate { display: flex; flex-wrap: wrap; justify-content: center; gap: 15px; width: 42%; min-height: 140px; }
        
        .pizza {
            width: 90px; height: 90px; background: #ffcc33; border: 6px solid #e67e22;
            border-radius: 50%; position: relative; box-shadow: 0 6px 0 #d35400; background-image: radial-gradient(#ffd65a 20%, transparent 20%); background-size: 15px 15px;
        }
        .chef-box { width: 100px; text-align: center; }
        #chef-emoji { font-size: 80px; animation: bounce 0.6s infinite alternate; }
        @keyframes bounce { from { transform: translateY(0); } to { transform: translateY(-15px); } }

        /* 题目：确保居中且不被遮挡 */
        .quest-display {
            margin-top: 10px; font-size: 48px; color: #ff6b6b; font-weight: 900;
            background: white; padding: 10px 50px; border-radius: 50px;
            border: 4px dashed #ff9a9e; box-shadow: 0 5px 20px rgba(255,107,107,0.15);
            z-index: 50;
        }

        /* 底部键盘 (43vh) —— 重点调整：将键盘整体向上移动一小段距离 */
        .footer {
            height: 43vh; display: flex; justify-content: space-around; align-items: center;
            /* 关键修改：增加负的margin-top，让键盘区域轻微上移，但不影响整体布局 */
            margin-top: -20px;
            padding: 0 20px; background: rgba(255,255,255,0.4); border-top: 2px solid #eee;
        }
        /* 为了让上移效果更自然，调整内部容器的垂直位置，同时保持按钮可点区域不变 */
        .pad-container {
            background: white; padding: 15px; border-radius: 30px; box-shadow: 0 8px 30px rgba(0,0,0,0.08);
            /* 可选：稍微减小下边距，使整体更紧凑，但原设计保持原样，只通过父级上移实现需求 */
        }
        .display-screen {
            background: #fffafa; height: 55px; line-height: 55px; font-size: 38px;
            text-align: center; margin-bottom: 12px; border-radius: 15px; border: 3px solid #ffdeeb;
            color: #444; font-weight: bold; overflow: hidden;
        }
        .key-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 10px; }
        .btn {
            width: 75px; padding: 12px 0; border-radius: 15px; font-size: 24px; font-weight: 900;
            cursor: pointer; border: none; background: #fff; box-shadow: 0 5px 0 #eee;
            transition: 0.1s; color: #555; border: 1px solid #f0f0f0;
        }
        .btn:active { transform: translateY(3px); box-shadow: 0 2px 0 #eee; }
        .btn.c { background: #ff7675; color: white; box-shadow: 0 5px 0 #d63031; }
        .btn.go { background: #55efc4; color: white; box-shadow: 0 5px 0 #00b894; }

        /* 显眼的馅料 */
        .topping {
            width: 20px; height: 20px; border-radius: 50%; position: absolute;
            animation: land 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275) forwards;
            border: 2px solid rgba(0,0,0,0.1); box-shadow: 0 2px 4px rgba(0,0,0,0.2);
        }
        @keyframes land { 
            0% { transform: translate(var(--sx), var(--sy)) scale(0); opacity: 0; }
            100% { transform: translate(0, 0) scale(1); opacity: 1; }
        }

        #mask { position: fixed; inset: 0; background: var(--p1); z-index: 2000; display: flex; flex-direction: column; justify-content: center; align-items: center; color: white; }
        /* 响应式微调：确保在超小屏幕上键盘依然可点且不会重叠严重，但保持需求不变 */
        @media (max-width: 700px) {
            .btn { width: 55px; padding: 8px 0; font-size: 20px; }
            .pad-container { padding: 12px; }
            .footer { margin-top: -15px; } /* 小屏幕下稍微减少上移幅度，保证布局舒适 */
            .pizza { width: 70px; height: 70px; }
            .quest-display { font-size: 36px; padding: 8px 25px; }
            .star-score { font-size: 24px; }
        }
    </style>
</head>
<body>

<div id="mask">
    <div style="font-size: 120px; margin-bottom: 20px;">🍎</div>
    <h1 style="font-size: 45px; margin: 0;">披萨大师大比拼</h1>
    <p style="font-size: 24px; opacity: 0.9;">让数学变得和披萨一样美味！</p>
    <button onclick="start()" style="margin-top: 40px; padding: 15px 80px; font-size: 32px; border-radius: 50px; border:none; background:white; color:var(--p1); font-weight:900; cursor:pointer; box-shadow: 0 10px 0 #d63031;">出发！✨</button>
</div>

<div class="header">
    <div class="star-score" id="s1" style="justify-content: flex-end;"></div>
    <div class="energy-box">
        <div style="color:#ff75a0; font-weight:bold; font-size: 20px;"></div>
        <div class="candy-bar"><div id="rainbow-fill"></div></div>
    </div>
    <div class="star-score" id="s2"></div>
</div>

<div class="stage">
    <div class="pizza-layout">
        <div id="p1-area" class="plate"></div>
        <div class="chef-box"><div id="chef-emoji">👨‍🍳</div></div>
        <div id="p2-area" class="plate"></div>
    </div>
    <div id="quest" class="quest-display">魔法加载中...</div>
</div>

<div class="footer">
    <div class="pad-container">
        <div id="screen1" class="display-screen"></div>
        <div id="keys1" class="key-grid"></div>
    </div>
    <div class="pad-container">
        <div id="screen2" class="display-screen"></div>
        <div id="keys2" class="key-grid"></div>
    </div>
</div>

<script>
    let ans, divNum, timeLeft = 120, playing = false;
    let inputs = ["", ""];
    const COLORS = ['#e74c3c', '#2ecc71', '#3498db', '#f1c40f', '#9b59b6', '#e67e22'];
    const audio = new (window.AudioContext || window.webkitAudioContext)();

    function beep(f, t='sine', d=0.2) {
        // 确保音频上下文未被挂起（用户首次交互后自动恢复）
        if (audio.state === 'suspended') {
            audio.resume();
        }
        const o = audio.createOscillator(); const g = audio.createGain();
        o.type = t; o.frequency.value = f; g.gain.value = 0.1;
        o.connect(g); g.connect(audio.destination); o.start(); o.stop(audio.currentTime+d);
    }

    function start() {
        document.getElementById('mask').style.display = 'none';
        playing = true;
        newTurn();
        setInterval(() => {
            if(!playing) return;
            timeLeft--;
            document.getElementById('rainbow-fill').style.width = (timeLeft/120*100) + "%";
            if(timeLeft <= 0) {
                playing = false;
                alert("🎉 时间到！你是最棒的披萨小天才！🎉");
                location.reload();
            }
        }, 1000);
    }

    function newTurn() {
        inputs = ["", ""];
        document.getElementById('screen1').innerText = "";
        document.getElementById('screen2').innerText = "";
        
        // 确保题目能整除且难度适中
        divNum = Math.floor(Math.random()*5) + 2; // 除数 2-6
        ans = Math.floor(Math.random()*7) + 2;    // 商 2-8
        let total = divNum * ans;                 // 被除数
        
        document.getElementById('quest').innerText = `✨ ${total} ÷ ${divNum} = ?`;
        
        ['p1-area', 'p2-area'].forEach(id => {
            const area = document.getElementById(id); area.innerHTML = "";
            for(let i=0; i<divNum; i++) area.innerHTML += `<div class="pizza"></div>`;
        });
        playing = true;
    }

    function press(p, key) {
        if(!playing) return;
        let idx = p-1;
        if(key === 'C') {
            inputs[idx] = "";
            beep(300);
            document.getElementById('screen'+p).innerText = "";
        } else if(key === 'Go') {
            // 如果输入为空，直接提示再试一次，不算错误但友好提醒
            if(inputs[idx] === "") {
                beep(150, 'sawtooth', 0.3);
                document.getElementById('screen'+p).innerText = "🍕 输入答案呀";
                setTimeout(()=> { if(playing) document.getElementById('screen'+p).innerText = ""; }, 700);
                return;
            }
            if(parseInt(inputs[idx]) === ans) {
                win(p);
            } else {
                beep(150, 'sawtooth', 0.4);
                inputs[idx] = "";
                document.getElementById('screen'+p).innerText = "❌ 再试一次";
                setTimeout(()=> { if(playing) document.getElementById('screen'+p).innerText = ""; }, 600);
            }
        } else {
            if(inputs[idx].length < 2) {
                inputs[idx] += key;
                beep(440 + (parseInt(key)*20));
            }
        }
        if(key !== 'Go' && key !== 'C') document.getElementById('screen'+p).innerText = inputs[idx];
    }

    function win(p) {
        playing = false;
        beep(880, 'triangle', 0.5);
        document.getElementById('s'+p).innerHTML += "🍕";
        const pizzas = document.getElementById('p'+p+'-area').children;
        const color = COLORS[Math.floor(Math.random()*COLORS.length)];
        const chefRect = document.getElementById('chef-emoji').getBoundingClientRect();

        for(let i=0; i<pizzas.length; i++) {
            setTimeout(() => {
                const pizzaRect = pizzas[i].getBoundingClientRect();
                // 极坐标防重叠算法，在每个披萨上撒 ans 个馅料
                for(let j=0; j<ans; j++) {
                    const t = document.createElement('div');
                    t.className = 'topping';
                    t.style.background = color;
                    
                    // 将圆划分为扇区分布
                    const angle = (j / ans) * Math.PI * 2 + (Math.random() * 0.5);
                    const dist = 15 + (Math.random() * 15); // 限制在中间环形区域
                    
                    const x = 45 + Math.cos(angle) * dist - 10;
                    const y = 45 + Math.sin(angle) * dist - 10;
                    
                    t.style.left = x + 'px';
                    t.style.top = y + 'px';
                    t.style.setProperty('--sx', (chefRect.left - pizzaRect.left) + 'px');
                    t.style.setProperty('--sy', (chefRect.top - pizzaRect.top) + 'px');
                    pizzas[i].appendChild(t);
                }
            }, i * 150);
        }
        setTimeout(() => {
            if(playing === false) newTurn();
        }, 2200);
    }

    function init() {
        [1,2].forEach(p => {
            const box = document.getElementById('keys'+p);
            ['1','2','3','4','5','6','7','8','9','C','0','Go'].forEach(k => {
                const b = document.createElement('button');
                b.className = 'btn' + (k==='C'?' c':(k==='Go'?' go':''));
                b.innerText = k;
                b.onclick = () => press(p, k);
                box.appendChild(b);
            });
        });
        // 预恢复音频上下文，避免第一次点击时无声音，但不影响功能
        document.body.addEventListener('click', () => {
            if(audio.state === 'suspended') audio.resume();
        }, { once: true });
    }
    init();
</script>
</body>
</html>
