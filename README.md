<html lang="ur" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>Vestify Pro - Global Trading</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;800&display=swap');
        
        :root { --binance-yellow: #F3BA2F; --bg-dark: #0B0E11; --card-bg: #1E2329; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg-dark); color: white; margin: 0; overflow-x: hidden; text-align: right; }
        
        /* Glassmorphism & Modern UI */
        .glass-card { background: var(--card-bg); border: 1px solid rgba(255,255,255,0.05); border-radius: 24px; }
        .trading-btn { background: var(--binance-yellow); color: black; font-weight: 800; border-radius: 14px; transition: 0.3s; }
        .trading-btn:active { transform: scale(0.95); }
        
        .page { display: none; padding: 15px; animation: slideIn 0.4s ease-out; padding-bottom: 100px; }
        .active-page { display: block; }
        @keyframes slideIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

        /* Navigation */
        .nav-bar { position: fixed; bottom: 0; width: 100%; height: 75px; background: rgba(30, 35, 41, 0.98); backdrop-filter: blur(15px); border-top: 1px solid rgba(255,255,255,0.08); display: flex; justify-content: space-around; align-items: center; z-index: 5000; flex-direction: row-reverse; }
        .nav-item { color: #848E9C; font-size: 20px; display: flex; flex-direction: column; align-items: center; gap: 4px; }
        .nav-active { color: var(--binance-yellow); }

        /* Ticker */
        .ticker { background: rgba(243, 186, 47, 0.1); color: var(--binance-yellow); padding: 8px; font-size: 10px; font-weight: bold; overflow: hidden; white-space: nowrap; }
        .ticker-text { display: inline-block; animation: scroll 20s linear infinite; }
        @keyframes scroll { from { transform: translateX(100%); } to { transform: translateX(-100%); } }

        /* Spin Wheel */
        #wheel { width: 280px; height: 280px; border-radius: 50%; border: 8px solid var(--binance-yellow); transition: transform 5s cubic-bezier(0.15, 0, 0.15, 1); box-shadow: 0 0 30px rgba(243, 186, 47, 0.2); }
        .timer-badge { background: #2B3139; color: var(--binance-yellow); padding: 4px 12px; border-radius: 8px; font-size: 11px; font-weight: bold; }
    </style>
</head>
<body class="pt-2">

    <header class="p-4 flex justify-between items-center">
        <div class="flex items-center gap-3 flex-row-reverse">
            <img id="user-photo" onclick="checkAdmin()" class="w-10 h-10 rounded-full border-2 border-yellow-500/30" src="https://via.placeholder.com/40">
            <div class="text-right">
                <p id="user-name" class="text-[10px] font-bold opacity-50 uppercase">Loading...</p>
                <p class="text-xs font-black text-yellow-500">VESTIFY PRO</p>
            </div>
        </div>
        <button onclick="showPage('finance')" class="w-10 h-10 glass-card flex items-center justify-center text-yellow-500"><i class="fa-solid fa-wallet"></i></button>
    </header>

    <div class="ticker">
        <div class="ticker-text">⚡ ممبر @User492 نے ₨ 12,500 کا منافع نکلوایا • مارکیٹ اپ ڈیٹ: ویسٹی فائی نوڈز کی قیمت میں اضافہ متوقع ہے ⚡</div>
    </div>

    <main id="app">
        
        <div id="p-home" class="page active-page">
            <div class="glass-card p-6 mb-6 text-center">
                <p class="text-[10px] font-bold opacity-40 mb-2">AVAILABLE BALANCE (PKR)</p>
                <h2 id="v-bal" class="text-4xl font-black italic mb-2 tracking-tighter">₨ 0.00</h2>
                <div class="flex justify-center items-center gap-2 mb-6">
                    <span class="w-2 h-2 bg-green-500 rounded-full animate-pulse"></span>
                    <p class="text-[10px] text-green-500 font-bold">Live Profit: ₨ <span id="profit-counter">0.00</span></p>
                </div>
                <div class="grid grid-cols-2 gap-3">
                    <button onclick="showPage('finance')" class="trading-btn py-3 text-[11px] uppercase">Deposit</button>
                    <button onclick="showPage('withdraw')" class="bg-white/5 py-3 rounded-xl text-[11px] font-bold uppercase">Withdraw</button>
                </div>
            </div>

            <div class="flex gap-4 mb-4 border-b border-white/5 pb-2">
                <button id="btn-n" onclick="renderPlans('normal')" class="text-sm font-bold text-yellow-500">Normal</button>
                <button id="btn-s" onclick="renderPlans('special')" class="text-sm font-bold opacity-30">VIP Special</button>
            </div>
            <div id="plans-grid" class="space-y-4"></div>
        </div>

        <div id="p-spin" class="page text-center">
            <h2 class="text-xl font-black mb-8 text-yellow-500 uppercase">Lucky Spin</h2>
            <div class="relative flex justify-center mb-8">
                <i class="fa-solid fa-caret-down absolute -top-4 text-3xl text-yellow-500 z-10"></i>
                <img id="wheel" src="https://i.ibb.co/3sS7z9y/spin-wheel-pro.png">
            </div>
            <div id="timer-box" class="mb-6 hidden">
                <p class="text-[10px] opacity-40 mb-2">NEXT FREE SPIN IN</p>
                <span id="countdown" class="timer-badge">00:00:00</span>
            </div>
            <button id="spin-btn" onclick="executeSpin()" class="w-full trading-btn py-5 text-xs uppercase">SPIN (₨ 50)</button>
        </div>

        <div id="p-finance" class="page">
            <h3 class="text-lg font-black mb-6">Deposit Funds</h3>
            <div class="glass-card p-6 space-y-4">
                <div class="bg-yellow-500/10 p-4 rounded-xl border border-yellow-500/20">
                    <p class="text-[10px] opacity-50 mb-1">Official EasyPaisa</p>
                    <p class="text-lg font-black text-yellow-500 select-all">03379827882</p>
                </div>
                <input id="d-amt" type="number" placeholder="رقم درج کریں" class="w-full bg-white/5 p-4 rounded-xl text-sm outline-none border border-white/5">
                <input id="d-tid" type="text" placeholder="Transaction ID (TID)" class="w-full bg-white/5 p-4 rounded-xl text-sm outline-none border border-white/5">
                <button onclick="handleTx('Deposit')" class="w-full trading-btn py-4 text-xs">SUBMIT DEPOSIT</button>
            </div>
        </div>

        <div id="p-withdraw" class="page">
            <h3 class="text-lg font-black mb-6">Withdraw Profit</h3>
            <div class="glass-card p-6 space-y-4">
                <input id="w-num" type="text" placeholder="EasyPaisa/JazzCash Number" class="w-full bg-white/5 p-4 rounded-xl text-sm outline-none">
                <input id="w-amt" type="number" placeholder="رقم" class="w-full bg-white/5 p-4 rounded-xl text-sm outline-none">
                <input id="w-pin" type="password" placeholder="4-Digit Wallet PIN" class="w-full bg-white/5 p-4 rounded-xl text-center tracking-[10px] font-black outline-none">
                <button onclick="handleWithdraw()" class="w-full trading-btn py-4 text-xs">WITHDRAW NOW</button>
            </div>
        </div>

        <div id="p-history" class="page">
            <h3 class="text-lg font-black mb-6">History</h3>
            <div id="history-list" class="space-y-3"></div>
        </div>

    </main>

    <nav class="nav-bar">
        <button onclick="showPage('home')" class="nav-item nav-active"><i class="fa-solid fa-chart-line"></i><span class="text-[8px] font-bold">MARKET</span></button>
        <button onclick="showPage('spin')" class="nav-item"><i class="fa-solid fa-dharmachakra"></i><span class="text-[8px] font-bold">SPIN</span></button>
        <button onclick="showPage('history')" class="nav-item"><i class="fa-solid fa-clock-rotate-left"></i><span class="text-[8px] font-bold">HISTORY</span></button>
    </nav>

    <div id="admin-ui" class="hidden fixed inset-0 z-[6000] bg-black p-6 overflow-y-auto">
        <div class="flex justify-between items-center mb-8">
            <h2 class="text-xl font-black">ADMIN CONTROL</h2>
            <button onclick="closeAdmin()" class="bg-red-500 px-4 py-1 rounded text-xs">EXIT</button>
        </div>
        <div id="adm-list" class="space-y-4"></div>
    </div>

    <script>
        // Firebase Config
        const firebaseConfig = { 
            apiKey: "AIzaSyC9ofJ1KxRXHnxilpU9gyI87D3BSOZ9v1g", 
            authDomain: "vestify-991f2.firebaseapp.com", 
            projectId: "vestify-991f2", 
            storageBucket: "vestify-991f2.firebasestorage.app", 
            messagingSenderId: "799007097733", 
            appId: "1:799007097733:web:ed3b35b6c4e51dc2e7baec" 
        };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore(), auth = firebase.auth();
        
        let userObj = null, currentRot = 0, tapCount = 0;

        // Auth & Initial Load
        auth.onAuthStateChanged(async user => {
            if(user) {
                document.getElementById('user-photo').src = user.photoURL;
                document.getElementById('user-name').innerText = user.displayName;
                const userRef = db.collection("users").doc(user.displayName);
                const doc = await userRef.get();
                if(!doc.exists) {
                    await userRef.set({ balance: 0, lastSpin: 0, walletPin: "1234", dailyProfit: 0 });
                }
                syncUser(user.displayName);
                renderPlans('normal');
                loadHistory(user.displayName);
            } else {
                auth.signInWithPopup(new firebase.auth.GoogleAuthProvider());
            }
        });

        // Sync User Data
        function syncUser(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                userObj = doc.data();
                document.getElementById('v-bal').innerText = "₨ " + (userObj.balance || 0).toLocaleString();
                updateTimer();
                startProfitCounter(userObj.dailyProfit || 0);
            });
        }

        // Render Plans
        function renderPlans(type) {
            let h = "";
            let start = type === 'normal' ? 200 : 5000;
            for(let i=1; i<=6; i++) {
                let cost = start + (i * (type === 'normal' ? 500 : 5000));
                let daily = Math.floor(cost * (type === 'normal' ? 0.12 : 0.20));
                h += `
                <div class="glass-card p-5 border-r-4 border-yellow-500">
                    <div class="flex justify-between items-center mb-4">
                        <div>
                            <p class="text-[9px] font-bold text-yellow-500">NODE PRO v${i}</p>
                            <h3 class="text-2xl font-black">₨ ${cost.toLocaleString()}</h3>
                        </div>
                        <div class="text-right">
                            <p class="text-[10px] text-green-500 font-bold">Daily: ₨ ${daily}</p>
                            <p class="text-[8px] opacity-40">Term: 30 Days</p>
                        </div>
                    </div>
                    <button onclick="invest(${cost}, ${daily})" class="w-full bg-white/5 py-3 rounded-xl text-[10px] font-bold hover:bg-yellow-500 hover:text-black">ACTIVATE NODE</button>
                </div>`;
            }
            document.getElementById('plans-grid').innerHTML = h;
            document.getElementById('btn-n').style.opacity = type === 'normal' ? '1' : '0.3';
            document.getElementById('btn-s').style.opacity = type === 'special' ? '1' : '0.3';
        }

        // Investment Logic
        async function invest(cost, daily) {
            if(userObj.balance < cost) return alert("بیلنس کم ہے!");
            if(confirm("Confirm Investment?")) {
                await db.collection("users").doc(auth.currentUser.displayName).update({
                    balance: firebase.firestore.FieldValue.increment(-cost),
                    dailyProfit: firebase.firestore.FieldValue.increment(daily)
                });
                await db.collection("txs").add({
                    user: auth.currentUser.displayName, amount: cost, type: "Investment", status: "Approved", time: Date.now()
                });
                alert("Activated!");
            }
        }

        // Spin Logic
        async function executeSpin() {
            const now = Date.now();
            const last = userObj.lastSpin || 0;
            const isFree = (now - last) > (24*60*60*1000);
            const cost = isFree ? 0 : 50;

            if(!isFree && userObj.balance < 50) return alert("Need ₨ 50 for extra spin!");

            const prizes = [2, 10, 5, 100, 2, 20, 5, 50];
            const idx = Math.floor(Math.random()*prizes.length);
            const win = prizes[idx];

            currentRot += 3600 + (idx * 45);
            document.getElementById('wheel').style.transform = `rotate(${currentRot}deg)`;

            setTimeout(async () => {
                await db.collection("users").doc(auth.currentUser.displayName).update({
                    balance: firebase.firestore.FieldValue.increment(win - cost),
                    lastSpin: isFree ? now : last
                });
                alert(`You Won ₨ ${win}!`);
            }, 5000);
        }

        // Profit Counter Logic
        function startProfitCounter(daily) {
            const secProfit = daily / 86400;
            setInterval(() => {
                let current = parseFloat(document.getElementById('profit-counter').innerText);
                document.getElementById('profit-counter').innerText = (current + secProfit).toFixed(4);
            }, 1000);
        }

        // Timer Update
        function updateTimer() {
            if(!userObj.lastSpin) return;
            const diff = (userObj.lastSpin + 86400000) - Date.now();
            if(diff > 0) {
                document.getElementById('timer-box').classList.remove('hidden');
                document.getElementById('spin-btn').innerText = "SPIN (₨ 50)";
                const h = Math.floor(diff/3600000), m = Math.floor((diff%3600000)/60000), s = Math.floor((diff%60000)/1000);
                document.getElementById('countdown').innerText = `${h}:${m}:${s}`;
            } else {
                document.getElementById('timer-box').classList.add('hidden');
                document.getElementById('spin-btn').innerText = "FREE SPIN AVAILABLE";
            }
        }
        setInterval(updateTimer, 1000);

        // UI Helpers
        function showPage(p) {
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.getElementById('p-'+p).classList.add('active-page');
            document.querySelectorAll('.nav-item').forEach(ni => ni.classList.remove('nav-active'));
        }

        async function handleTx(type) {
            const amt = Number(document.getElementById('d-amt').value);
            const tid = document.getElementById('d-tid').value;
            if(!amt || !tid) return alert("Fill all fields");
            await db.collection("txs").add({ user: auth.currentUser.displayName, amount: amt, tid: tid, type: type, status: "Pending", time: Date.now() });
            alert("Request Sent!");
        }

        function loadHistory(name) {
            db.collection("txs").where("user","==",name).orderBy("time","desc").onSnapshot(s => {
                let h = ""; s.forEach(doc => {
                    const d = doc.data();
                    h += `<div class="glass-card p-4 flex justify-between"><div><p class="text-xs font-bold">${d.type}</p></div><div class="text-left"><p class="text-sm font-black">₨ ${d.amount}</p><p class="text-[8px] ${d.status==='Approved'?'text-green-500':'text-yellow-500'}">${d.status}</p></div></div>`;
                });
                document.getElementById('history-list').innerHTML = h || "No Records";
            });
        }

        // Admin
        function checkAdmin() { tapCount++; if(tapCount>=10) { if(prompt("Key")==="net204") loadAdmin(); tapCount=0; } }
        function loadAdmin() {
            document.getElementById('admin-ui').classList.remove('hidden');
            db.collection("txs").where("status","==","Pending").onSnapshot(s => {
                let h = ""; s.forEach(doc => {
                    const d = doc.data();
                    h += `<div class="glass-card p-4"><p class="text-xs">${d.user} (${d.type})</p><h2 class="text-xl font-bold">₨ ${d.amount}</h2><button onclick="approve('${doc.id}','${d.user}',${d.amount})" class="w-full bg-green-600 py-2 mt-2 rounded-lg text-xs font-bold">APPROVE</button></div>`;
                });
                document.getElementById('adm-list').innerHTML = h || "All Clean";
            });
        }
        async function approve(id, u, a) {
            await db.collection("txs").doc(id).update({status:"Approved"});
            await db.collection("users").doc(u).update({balance: firebase.firestore.FieldValue.increment(a)});
        }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }
    </script>
</body>
</html>
