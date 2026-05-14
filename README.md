<html lang="ur" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>Vestify Pro - No Image Edition</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@300;500;700&display=swap');
        :root { --binance-yellow: #F3BA2F; --bg-dark: #0B0E11; --card-bg: #1E2329; }
        body { font-family: 'Space Grotesk', sans-serif; background: var(--bg-dark); color: #EAECEF; margin: 0; text-align: right; }
        
        .glass-card { background: var(--card-bg); border: 1px solid rgba(255,255,255,0.05); border-radius: 20px; }
        .trading-btn { background: var(--binance-yellow); color: black; font-weight: 700; border-radius: 12px; transition: 0.3s; }
        
        /* CSS Driven Spin Wheel - No External Image Needed */
        .wheel-container { position: relative; width: 300px; height: 300px; margin: 0 auto; }
        #wheel-svg { width: 100%; height: 100%; transform: rotate(0deg); transition: transform 5s cubic-bezier(0.1, 0, 0.1, 1); filter: drop-shadow(0 0 15px rgba(243,186,47,0.3)); }
        .wheel-pointer { position: absolute; top: -15px; left: 50%; transform: translateX(-50%); z-index: 10; color: #ef4444; font-size: 30px; }

        .page { display: none; padding: 15px; animation: fadeIn 0.4s ease; padding-bottom: 100px; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; } }

        .nav-bar { background: rgba(30, 35, 41, 0.98); border-top: 1px solid rgba(255,255,255,0.1); position: fixed; bottom: 0; width: 100%; height: 75px; display: flex; justify-content: space-around; align-items: center; flex-direction: row-reverse; z-index: 1000; }
        .nav-active { color: var(--binance-yellow); }
    </style>
</head>
<body>

    <header class="p-4 flex justify-between items-center sticky top-0 bg-[#0B0E11] z-[1001]">
        <div class="flex items-center gap-3 flex-row-reverse">
            <div id="user-photo-container" onclick="handleAdminTap()" class="w-10 h-10 rounded-full border-2 border-yellow-500/50 flex items-center justify-center bg-white/5 overflow-hidden">
                <i class="fa-solid fa-user text-yellow-500"></i>
            </div>
            <div class="text-right">
                <p id="user-name" class="text-[10px] font-bold opacity-60">WELCOME</p>
                <p class="text-xs font-black text-yellow-500 uppercase">Vestify Pro</p>
            </div>
        </div>
        <div class="bg-green-500/10 text-green-500 text-[10px] px-3 py-1 rounded-full font-bold">● LIVE MARKET</div>
    </header>

    <main>
        <div id="p-home" class="page active-page">
            <div class="glass-card p-6 mb-6 text-center">
                <p class="text-[10px] font-bold opacity-40 mb-2 uppercase">Account Balance</p>
                <h1 id="v-bal" class="text-4xl font-black italic mb-2">₨ 0.00</h1>
                <p class="text-[10px] text-green-500 font-bold mb-6 italic">+₨ <span id="live-profit">0.0000</span> Profit Accumulating...</p>
                <div class="grid grid-cols-2 gap-3">
                    <button onclick="showPage('finance')" class="trading-btn py-3 text-xs">DEPOSIT</button>
                    <button onclick="showPage('withdraw')" class="bg-white/5 py-3 rounded-xl text-xs font-bold">WITHDRAW</button>
                </div>
            </div>

            <div class="flex gap-4 mb-4 border-b border-white/5 pb-2">
                <button id="btn-n" onclick="renderPlans('normal')" class="text-sm font-bold text-yellow-500">Standard</button>
                <button id="btn-s" onclick="renderPlans('special')" class="text-sm font-bold opacity-30">VIP Special</button>
            </div>
            <div id="plans-grid" class="space-y-4"></div>
        </div>

        <div id="p-spin" class="page text-center">
            <h2 class="text-xl font-black mb-10 text-yellow-500 uppercase tracking-widest">Golden Spin</h2>
            
            <div class="wheel-container mb-10">
                <div class="wheel-pointer"><i class="fa-solid fa-caret-down"></i></div>
                <svg id="wheel-svg" viewBox="0 0 100 100">
                    <circle cx="50" cy="50" r="48" fill="#1E2329" stroke="#F3BA2F" stroke-width="2"/>
                    <path d="M50,50 L50,2 A48,48 0 0,1 84,16 Z" fill="#F3BA2F"/>
                    <path d="M50,50 L84,16 A48,48 0 0,1 98,50 Z" fill="#2B3139"/>
                    <path d="M50,50 L98,50 A48,48 0 0,1 84,84 Z" fill="#F3BA2F"/>
                    <path d="M50,50 L84,84 A48,48 0 0,1 50,98 Z" fill="#2B3139"/>
                    <path d="M50,50 L50,98 A48,48 0 0,1 16,84 Z" fill="#F3BA2F"/>
                    <path d="M50,50 L16,84 A48,48 0 0,1 2,50 Z" fill="#2B3139"/>
                    <path d="M50,50 L2,50 A48,48 0 0,1 16,16 Z" fill="#F3BA2F"/>
                    <path d="M50,50 L16,16 A48,48 0 0,1 50,2 Z" fill="#2B3139"/>
                    <circle cx="50" cy="50" r="5" fill="white"/>
                </svg>
            </div>

            <div id="timer-box" class="mb-6 hidden">
                <p class="text-[10px] opacity-40 mb-2 uppercase">Next Free Spin In</p>
                <span id="spin-timer" class="bg-yellow-500/10 text-yellow-500 px-4 py-2 rounded-lg font-bold text-xs">00:00:00</span>
            </div>
            <button id="spin-btn" onclick="executeSpin()" class="w-full trading-btn py-5 text-sm uppercase">Spin Now (₨ 50)</button>
        </div>

        <div id="p-finance" class="page">
            <h2 class="text-xl font-black mb-6">Add Funds</h2>
            <div class="glass-card p-6 space-y-4">
                <div class="bg-yellow-500/10 p-5 rounded-2xl border border-yellow-500/20 text-center">
                    <p class="text-[10px] opacity-50 mb-1 uppercase">EasyPaisa Account</p>
                    <p class="text-xl font-black text-yellow-500 tracking-widest">03379827882</p>
                </div>
                <input id="d-amt" type="number" placeholder="رقم درج کریں" class="w-full bg-[#2B3139] p-4 rounded-xl text-sm outline-none">
                <input id="d-tid" type="text" placeholder="Transaction ID (TID)" class="w-full bg-[#2B3139] p-4 rounded-xl text-sm outline-none">
                <button onclick="submitTx('Deposit')" class="w-full trading-btn py-4 text-xs">SUBMIT DEPOSIT</button>
            </div>
        </div>

        <div id="p-history" class="page">
            <h2 class="text-xl font-black mb-6">Transaction History</h2>
            <div id="history-list" class="space-y-3"></div>
        </div>
    </main>

    <div id="admin-ui" class="hidden fixed inset-0 z-[5000] bg-black p-6 overflow-y-auto">
        <div class="flex justify-between items-center mb-8">
            <h2 class="text-xl font-black">ADMIN MODE</h2>
            <button onclick="closeAdmin()" class="bg-red-500 px-4 py-1 rounded text-xs">CLOSE</button>
        </div>
        <div id="adm-list" class="space-y-4"></div>
    </div>

    <nav class="nav-bar">
        <button onclick="showPage('home')" class="nav-active"><i class="fa-solid fa-chart-line"></i><p class="text-[8px] font-bold uppercase">Market</p></button>
        <button onclick="showPage('spin')"><i class="fa-solid fa-dharmachakra opacity-40"></i><p class="text-[8px] font-bold opacity-40 uppercase">Spin</p></button>
        <button onclick="showPage('history')"><i class="fa-solid fa-receipt opacity-40"></i><p class="text-[8px] font-bold opacity-40 uppercase">History</p></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyC9ofJ1KxRXHnxilpU9gyI87D3BSOZ9v1g", authDomain: "vestify-991f2.firebaseapp.com", projectId: "vestify-991f2", storageBucket: "vestify-991f2.firebasestorage.app", messagingSenderId: "799007097733", appId: "1:799007097733:web:ed3b35b6c4e51dc2e7baec" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore(), auth = firebase.auth();
        let userObj = null, currentRot = 0, tapCount = 0;

        auth.onAuthStateChanged(async user => {
            if(user) {
                if(user.photoURL) document.getElementById('user-photo-container').innerHTML = `<img src="${user.photoURL}" class="w-full h-full object-cover">`;
                document.getElementById('user-name').innerText = user.displayName;
                syncUser(user.displayName);
                renderPlans('normal');
                loadHistory(user.displayName);
            } else { auth.signInWithPopup(new firebase.auth.GoogleAuthProvider()); }
        });

        function syncUser(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                userObj = doc.data() || { balance: 0, lastSpin: 0, dailyProfit: 0 };
                document.getElementById('v-bal').innerText = "₨ " + (userObj.balance || 0).toLocaleString();
                startLiveProfit(userObj.dailyProfit || 0);
            });
        }

        function renderPlans(type) {
            let h = ""; let start = type === 'normal' ? 200 : 5000;
            for(let i=1; i<=6; i++) {
                let cost = start + (i * (type === 'normal' ? 500 : 5000));
                let daily = Math.floor(cost * (type === 'normal' ? 0.12 : 0.20));
                h += `
                <div class="glass-card p-5 border-r-4 border-yellow-500">
                    <div class="flex justify-between items-center mb-4">
                        <div><p class="text-[9px] text-yellow-500 font-bold uppercase">Node v.${i}</p><h3 class="text-2xl font-black">₨ ${cost.toLocaleString()}</h3></div>
                        <div class="text-left"><p class="text-xs text-green-500 font-bold">₨ ${daily}/Day</p><p class="text-[8px] opacity-40">Profit Fixed</p></div>
                    </div>
                    <button onclick="invest(${cost}, ${daily})" class="w-full bg-white/5 py-3 rounded-xl text-[10px] font-bold hover:bg-yellow-500">ACTIVATE NODE</button>
                </div>`;
            }
            document.getElementById('plans-grid').innerHTML = h;
            document.getElementById('btn-n').style.opacity = type === 'normal' ? '1' : '0.3';
            document.getElementById('btn-s').style.opacity = type === 'special' ? '1' : '0.3';
        }

        async function executeSpin() {
            const now = Date.now(), last = userObj.lastSpin || 0, isFree = (now - last) > 86400000, cost = isFree ? 0 : 50;
            if(!isFree && userObj.balance < 50) return alert("Not enough balance!");

            const prizes = [100, 10, 50, 5, 20, 2, 10, 2];
            const idx = Math.floor(Math.random() * 8);
            currentRot += 3600 + (idx * 45);
            document.getElementById('wheel-svg').style.transform = `rotate(${currentRot}deg)`;

            setTimeout(async () => {
                await db.collection("users").doc(auth.currentUser.displayName).update({
                    balance: firebase.firestore.FieldValue.increment(prizes[idx] - cost),
                    lastSpin: isFree ? now : last
                });
                alert(`You Won ₨ ${prizes[idx]}!`);
            }, 5000);
        }

        function startLiveProfit(daily) {
            const secProfit = daily / 86400;
            setInterval(() => {
                let curr = parseFloat(document.getElementById('live-profit').innerText);
                document.getElementById('live-profit').innerText = (curr + secProfit).toFixed(4);
            }, 1000);
        }

        function updateTimer() {
            if(!userObj || !userObj.lastSpin) return;
            const diff = (userObj.lastSpin + 86400000) - Date.now();
            if(diff > 0) {
                document.getElementById('timer-box').classList.remove('hidden');
                document.getElementById('spin-btn').innerText = "Paid Spin (₨ 50)";
                const h = Math.floor(diff/3600000), m = Math.floor((diff%3600000)/60000), s = Math.floor((diff%60000)/1000);
                document.getElementById('spin-timer').innerText = `${h}:${m}:${s}`;
            } else {
                document.getElementById('timer-box').classList.add('hidden');
                document.getElementById('spin-btn').innerText = "Free Spin Available";
            }
        }
        setInterval(updateTimer, 1000);

        async function submitTx(type) {
            const amt = Number(document.getElementById('d-amt').value), tid = document.getElementById('d-tid').value;
            if(!amt || !tid) return alert("All fields required");
            await db.collection("txs").add({ user: auth.currentUser.displayName, amount: amt, tid: tid, type: type, status: "Pending", time: Date.now() });
            alert("Request submitted!");
        }

        function loadHistory(name) {
            db.collection("txs").where("user","==",name).orderBy("time","desc").onSnapshot(s => {
                let h = ""; s.forEach(doc => {
                    const d = doc.data();
                    h += `<div class="glass-card p-4 flex justify-between"><div><p class="text-xs font-bold uppercase">${d.type}</p></div><div class="text-left"><p class="text-sm font-black">₨ ${d.amount}</p><p class="text-[8px] ${d.status==='Approved'?'text-green-500':'text-yellow-500'}">${d.status}</p></div></div>`;
                });
                document.getElementById('history-list').innerHTML = h || "No transactions";
            });
        }

        function showPage(p) {
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.getElementById('p-'+p).classList.add('active-page');
        }

        function handleAdminTap() { tapCount++; if(tapCount>=10) { if(prompt("Enter Admin Key:")==="net204") loadAdmin(); tapCount=0; } }
        function loadAdmin() {
            document.getElementById('admin-ui').classList.remove('hidden');
            db.collection("txs").where("status","==","Pending").onSnapshot(s => {
                let h = ""; s.forEach(doc => {
                    const d = doc.data();
                    h += `<div class="glass-card p-4 mb-3"><p class="text-xs">${d.user}</p><h2 class="text-xl font-bold">₨ ${d.amount}</h2><button onclick="approve('${doc.id}','${d.user}',${d.amount})" class="w-full bg-green-600 py-2 mt-2 rounded-lg text-xs font-bold">APPROVE</button></div>`;
                });
                document.getElementById('adm-list').innerHTML = h || "No pending requests";
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
