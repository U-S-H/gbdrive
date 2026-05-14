<html lang="ur" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>ویسٹی فائی الٹیمیٹ پرو</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;700;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background-color: #010409; color: white; margin: 0; padding: 0; overflow-x: hidden; text-align: right; }
        .glass { background: rgba(22, 27, 34, 0.75); backdrop-filter: blur(25px); border: 1px solid rgba(255,255,255,0.08); border-radius: 30px; }
        .page { display: none; padding: 20px; animation: slideUp 0.5s cubic-bezier(0.4, 0, 0.2, 1); }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(30px); } to { opacity: 1; transform: translateY(0); } }
        
        /* Chat UI */
        #chat-window { position: fixed; bottom: 100px; left: 20px; right: 20px; height: 420px; z-index: 10000; display: none; flex-direction: column; box-shadow: 0 20px 50px rgba(0,0,0,0.5); }
        .msg-user { background: #3b82f6; border-radius: 18px 18px 0 18px; padding: 12px; margin: 5px; align-self: flex-end; font-size: 11px; max-width: 80%; }
        .msg-admin { background: #1f2937; border-radius: 18px 18px 18px 0; padding: 12px; margin: 5px; align-self: flex-start; font-size: 11px; border: 1px solid #374151; max-width: 80%; }

        .nav-bar { position: fixed; bottom: 15px; left: 15px; right: 15px; height: 80px; display: flex; justify-content: space-around; align-items: center; z-index: 5000; border-radius: 30px; background: rgba(22, 27, 34, 0.98); border: 1px solid rgba(255,255,255,0.05); flex-direction: row-reverse; }
        .nav-btn { color: rgba(255,255,255,0.3); transition: 0.4s; font-size: 20px; }
        .nav-active { color: #3b82f6; transform: translateY(-8px); text-shadow: 0 0 15px rgba(59, 130, 246, 0.5); }
        
        #wheel { width: 290px; height: 290px; border-radius: 50%; border: 12px solid #d4af37; transition: transform 5s cubic-bezier(0.15, 0, 0.15, 1); box-shadow: 0 0 50px rgba(212, 175, 55, 0.3); }
        .plan-card { border: 1px solid rgba(59, 130, 246, 0.1); transition: 0.3s; }
        .plan-card:hover { border-color: rgba(59, 130, 246, 0.5); }
    </style>
</head>
<body class="pb-36">

    <div id="chat-window" class="glass overflow-hidden border-blue-500/30">
        <div class="p-4 bg-gradient-to-r from-blue-600 to-blue-400 flex justify-between items-center">
            <button onclick="clearChat()" class="text-[9px] bg-black/20 px-3 py-1 rounded-full font-bold">CLEAR</button>
            <div class="text-right">
                <p class="font-black text-xs uppercase tracking-tighter">Agent 204</p>
                <div class="flex items-center gap-1 justify-end">
                    <span class="w-1.5 h-1.5 bg-green-400 rounded-full animate-pulse"></span>
                    <p class="text-[8px] font-bold opacity-90">Live Support</p>
                </div>
            </div>
            <button onclick="toggleChat()" class="w-8 h-8 rounded-full bg-black/10 flex items-center justify-center"><i class="fa-solid fa-times"></i></button>
        </div>
        <div id="chat-msgs" class="flex-1 overflow-y-auto p-4 flex flex-col gap-2 bg-[#0d1117]"></div>
        <div class="p-4 bg-[#161b22] border-t border-white/5 flex gap-2">
            <input id="chat-input" type="text" placeholder="پیغام لکھیں..." class="flex-1 bg-white/5 border border-white/10 rounded-2xl p-3 text-xs outline-none focus:border-blue-500">
            <button onclick="sendMsg()" class="bg-blue-600 w-12 rounded-2xl flex items-center justify-center shadow-lg shadow-blue-600/20"><i class="fa-solid fa-paper-plane"></i></button>
        </div>
    </div>

    <main id="app-ui">
        <header class="p-6 flex justify-between items-center flex-row-reverse">
            <div class="flex items-center gap-4 flex-row-reverse">
                <div class="relative">
                    <img id="user-photo" onclick="handleAdminTap()" class="w-14 h-14 rounded-2xl border-2 border-blue-500/20 cursor-pointer shadow-xl">
                    <div class="absolute -bottom-1 -right-1 w-4 h-4 bg-green-500 border-4 border-[#010409] rounded-full"></div>
                </div>
                <div class="text-right">
                    <p id="user-name" class="text-xs font-black uppercase tracking-tight">Syncing...</p>
                    <p class="text-[8px] font-bold text-blue-500 uppercase tracking-widest">Premium Node</p>
                </div>
            </div>
            <button onclick="toggleChat()" class="w-12 h-12 glass flex items-center justify-center text-blue-500 relative">
                <i class="fa-solid fa-headset text-xl"></i>
                <span class="absolute -top-1 -right-1 w-3 h-3 bg-red-500 rounded-full border-2 border-[#010409]"></span>
            </button>
        </header>

        <div id="p-home" class="page active-page">
            <div class="glass p-10 mb-8 text-center bg-gradient-to-br from-blue-600/15 via-transparent to-transparent border-blue-500/20">
                <p class="text-[10px] font-black opacity-40 uppercase mb-3 tracking-widest text-blue-400">کل بیلنس</p>
                <h2 id="v-bal" class="text-6xl font-black italic mb-10 tracking-tighter">₨ 0</h2>
                <div class="flex gap-4">
                    <button onclick="showPage('finance')" class="flex-1 bg-white text-black py-5 rounded-2xl font-black text-[11px] shadow-xl active:scale-95 transition-all">جمع کریں</button>
                    <button onclick="showPage('withdraw')" class="flex-1 glass py-5 rounded-2xl font-black text-[11px] active:scale-95 transition-all">نکلوائیں</button>
                </div>
            </div>
            
            <div class="flex gap-3 mb-8">
                <button id="btn-n" onclick="renderPlans('normal')" class="flex-1 py-4 rounded-2xl bg-blue-600 text-[10px] font-black shadow-lg shadow-blue-600/20">NORMAL PLANS</button>
                <button id="btn-s" onclick="renderPlans('special')" class="flex-1 py-4 rounded-2xl bg-white/5 text-[10px] font-black opacity-50">SPECIAL OFFERS</button>
            </div>
            <div id="plans-grid" class="grid grid-cols-1 gap-5 pb-10"></div>
        </div>

        <div id="p-spin" class="page text-center">
            <h2 class="text-3xl font-black italic mb-12 uppercase">گولڈن <span class="text-yellow-500">جیک پاٹ</span></h2>
            <div class="flex justify-center mb-12 relative">
                <div class="absolute -top-10 left-1/2 -translate-x-1/2 text-red-600 text-5xl z-10 animate-bounce"><i class="fa-solid fa-location-dot"></i></div>
                <img id="wheel" src="IMG_20260515_012434.jpg" onerror="this.src='https://via.placeholder.com/300?text=Vestify+Wheel'">
            </div>
            <p id="spin-cost-text" class="text-[11px] mb-8 font-black text-blue-400 uppercase tracking-widest">پہلا سپن بالکل مفت ہے!</p>
            <button id="spin-btn" onclick="executeSpin()" class="w-full max-w-xs bg-gradient-to-r from-blue-600 to-blue-400 py-6 rounded-full font-black text-xs uppercase shadow-2xl active:scale-95 transition-all">گھمائیں</button>
        </div>

        <div id="p-history" class="page">
            <h2 class="text-3xl font-black italic mb-8 text-right uppercase">ہسٹری <span class="text-blue-500">ریکارڈز</span></h2>
            <div id="history-list" class="space-y-4 pb-12"></div>
        </div>

        <div id="p-team" class="page text-center">
            <h2 class="text-3xl font-black italic mb-8 uppercase">نیٹ ورک <span class="text-blue-500">ٹیم</span></h2>
            <div class="glass p-8 border-blue-500/10">
                <p class="text-[10px] opacity-40 mb-4 uppercase tracking-widest text-right">آپ کا ریفرل لنک</p>
                <div class="flex gap-2 mb-10">
                    <input id="ref-link" readonly class="flex-1 bg-black/40 p-5 rounded-2xl text-[10px] text-blue-400 border border-white/10 text-center font-bold">
                    <button onclick="copyRef()" class="w-16 bg-white text-black rounded-2xl flex items-center justify-center text-xl active:scale-90 transition-all"><i class="fa-solid fa-copy"></i></button>
                </div>
                <div class="flex justify-between items-center bg-blue-600/10 p-6 rounded-3xl border border-blue-500/20">
                    <div class="text-right">
                        <p class="text-[9px] font-black text-blue-500 uppercase">Total Referrals</p>
                        <p id="team-count" class="text-3xl font-black">0</p>
                    </div>
                    <i class="fa-solid fa-users-rays text-4xl text-blue-500/30"></i>
                </div>
            </div>
        </div>

        <div id="admin-ui" class="hidden fixed inset-0 z-[20000] bg-[#010409] p-6 overflow-y-auto">
            <div class="flex justify-between items-center mb-10 border-b border-white/5 pb-5">
                <button onclick="closeAdmin()" class="bg-red-500/10 text-red-500 px-6 py-3 rounded-xl font-bold text-xs">EXIT ADMIN</button>
                <h2 class="text-2xl font-black text-white">ADMIN TERMINAL</h2>
            </div>
            <div id="adm-tx-list" class="space-y-4"></div>
        </div>

        <nav class="nav-bar">
            <button onclick="showPage('home')" class="nav-btn" id="nav-home"><i class="fa-solid fa-microchip"></i></button>
            <button onclick="showPage('spin')" class="nav-btn" id="nav-spin"><i class="fa-solid fa-dharmachakra"></i></button>
            <button onclick="showPage('team')" class="nav-btn" id="nav-team"><i class="fa-solid fa-users-rays"></i></button>
            <button onclick="showPage('history')" class="nav-btn" id="nav-history"><i class="fa-solid fa-list-ul"></i></button>
        </nav>
    </main>

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
                if(!doc.exists) await userRef.set({ balance: 0, spinCount: 0, teamCount: 0, walletPin: "1234" });
                
                syncData(user.displayName);
                renderPlans('normal');
                loadHistory(user.displayName);
                loadChat(user.displayName);
                document.getElementById('ref-link').value = `https://u-s-h.github.io/vestify/?ref=${user.displayName.replace(/\s/g, '')}`;
            } else {
                auth.signInWithPopup(new firebase.auth.GoogleAuthProvider());
            }
        });

        // 20+ Professional Plans starting from 200 PKR
        function renderPlans(type) {
            document.getElementById('btn-n').style.opacity = type === 'normal' ? '1' : '0.5';
            document.getElementById('btn-s').style.opacity = type === 'special' ? '1' : '0.5';
            
            let h = "";
            let startPrice = type === 'normal' ? 200 : 5000;
            let totalPlans = type === 'normal' ? 15 : 10;

            for(let i=1; i<=totalPlans; i++) {
                let cost = startPrice + (i * (type === 'normal' ? 400 : 2500));
                let daily = Math.floor(cost * (type === 'normal' ? 0.12 : 0.20));
                h += `
                <div class="plan-card glass p-7 rounded-[2.5rem] bg-gradient-to-l from-white/5 to-transparent">
                    <div class="flex justify-between items-start mb-6">
                        <div class="text-right">
                            <p class="text-[10px] font-black text-blue-500 uppercase tracking-tighter">Level Cluster v.${i}</p>
                            <h4 class="text-2xl font-black mt-1 tracking-tighter">₨ ${cost.toLocaleString()}</h4>
                        </div>
                        <div class="w-10 h-10 rounded-full bg-blue-500/10 flex items-center justify-center text-blue-500"><i class="fa-solid fa-bolt"></i></div>
                    </div>
                    <div class="grid grid-cols-2 gap-3 mb-6">
                        <div class="bg-black/40 p-4 rounded-2xl border border-white/5">
                            <p class="text-[8px] opacity-40 uppercase">Daily Profit</p>
                            <p class="text-xs font-bold text-green-400">₨ ${daily}</p>
                        </div>
                        <div class="bg-black/40 p-4 rounded-2xl border border-white/5">
                            <p class="text-[8px] opacity-40 uppercase">Total Return</p>
                            <p class="text-xs font-bold text-blue-400">₨ ${daily*30}</p>
                        </div>
                    </div>
                    <button class="w-full bg-white text-black py-4 rounded-2xl font-black text-[10px] uppercase active:scale-95 transition-all">INVEST NOW</button>
                </div>`;
            }
            document.getElementById('plans-grid').innerHTML = h;
        }

        // Exact Golden Spin Logic
        async function executeSpin() {
            const cost = userObj.spinCount > 0 ? 50 : 0;
            if(userObj.balance < cost) return alert("Balance is low for Spin!");
            
            const btn = document.getElementById('spin-btn');
            btn.disabled = true;

            const prizes = [
                { angle: 0, win: 100 }, { angle: 45, win: 50 }, { angle: 90, win: 10 },
                { angle: 135, win: 5 }, { angle: 180, win: 2 }, { angle: 225, win: 10 },
                { angle: 270, win: 2 }, { angle: 315, win: 5 }
            ];

            const p = prizes[Math.floor(Math.random() * prizes.length)];
            currentRot += 3600 + (360 - p.angle);
            document.getElementById('wheel').style.transform = `rotate(${currentRot}deg)`;
            
            setTimeout(async () => {
                await db.collection("users").doc(auth.currentUser.displayName).update({
                    balance: firebase.firestore.FieldValue.increment(p.win - cost),
                    spinCount: firebase.firestore.FieldValue.increment(1)
                });
                alert(`Wheel stopped! You won ₨ ${p.win}`);
                btn.disabled = false;
            }, 5000);
        }

        // Help Desk: Agent 204
        function toggleChat() { 
            const chat = document.getElementById('chat-window');
            chat.style.display = chat.style.display === 'flex' ? 'none' : 'flex';
        }

        async function sendMsg() {
            const input = document.getElementById('chat-input');
            if(!input.value) return;
            await db.collection("chats").add({
                user: auth.currentUser.displayName,
                msg: input.value,
                sender: 'user',
                time: Date.now()
            });
            input.value = "";
        }

        function loadChat(name) {
            db.collection("chats").where("user", "==", name).orderBy("time", "asc").onSnapshot(s => {
                let h = "";
                s.forEach(doc => {
                    const d = doc.data();
                    h += `<div class="${d.sender === 'user' ? 'msg-user' : 'msg-admin'}">${d.msg}</div>`;
                });
                const box = document.getElementById('chat-msgs');
                box.innerHTML = h;
                box.scrollTop = box.scrollHeight;
            });
        }

        async function clearChat() {
            if(confirm("Delete all messages?")) {
                const s = await db.collection("chats").where("user", "==", auth.currentUser.displayName).get();
                s.forEach(doc => doc.ref.delete());
            }
        }

        // Fixed History (Firestore OnSnapshot)
        function loadHistory(name) {
            db.collection("txs").where("user", "==", name).orderBy("time", "desc").onSnapshot(s => {
                let h = "";
                s.forEach(doc => {
                    const d = doc.data();
                    const color = d.status === "Approved" ? "text-green-500" : (d.status === "Pending" ? "text-yellow-500" : "text-red-500");
                    h += `<div class="glass p-6 flex justify-between items-center border-white/5 bg-white/5">
                        <div class="text-right">
                            <p class="text-[10px] font-black uppercase tracking-widest text-blue-400">${d.type}</p>
                            <p class="text-[8px] opacity-40 mt-1">${new Date(d.time).toLocaleString()}</p>
                        </div>
                        <div class="text-left">
                            <p class="font-black text-lg italic">₨ ${d.amount.toLocaleString()}</p>
                            <p class="text-[8px] font-black uppercase ${color}">${d.status}</p>
                        </div>
                    </div>`;
                });
                document.getElementById('history-list').innerHTML = h || "<p class='text-center opacity-20 py-20 text-xs tracking-widest'>NO ACTIVITY LOGGED</p>";
            });
        }

        function syncData(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                userObj = doc.data();
                document.getElementById('v-bal').innerText = "₨ " + (userObj.balance || 0).toLocaleString();
                document.getElementById('team-count').innerText = userObj.teamCount || 0;
            });
        }

        function handleAdminTap() { tapCount++; if(tapCount >= 10) { let k = prompt("Secret Key:"); if(k === "net204") loadAdmin(); tapCount=0; } }
        function loadAdmin() {
            document.getElementById('admin-ui').classList.remove('hidden');
            db.collection("txs").where("status", "==", "Pending").onSnapshot(s => {
                let h = "";
                s.forEach(doc => {
                    const d = doc.data();
                    h += `<div class="glass p-6 border-red-500/20">
                        <p class="text-[10px] font-black text-blue-500">${d.user}</p>
                        <p class="text-3xl font-black my-4 italic">₨ ${d.amount}</p>
                        <div class="flex gap-2">
                            <button onclick="approveTx('${doc.id}', '${d.user}', ${d.amount}, '${d.type}')" class="bg-green-600 flex-1 py-4 rounded-2xl font-black text-[10px]">APPROVE</button>
                            <button onclick="db.collection('txs').doc('${doc.id}').update({status:'Rejected'})" class="bg-red-600 flex-1 py-4 rounded-2xl font-black text-[10px]">REJECT</button>
                        </div>
                    </div>`;
                });
                document.getElementById('adm-tx-list').innerHTML = h || "<p class='text-center opacity-30'>Queue Clear</p>";
            });
        }

        async function approveTx(id, u, a, type) {
            await db.collection("txs").doc(id).update({ status: "Approved" });
            const multiplier = (type === "Deposit" || type === "Deposit") ? 1 : -1;
            await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(a * multiplier) });
        }

        function showPage(p) {
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.getElementById('p-'+p).classList.add('active-page');
            document.querySelectorAll('.nav-btn').forEach(b => b.classList.remove('nav-active'));
            if(document.getElementById('nav-'+p)) document.getElementById('nav-'+p).classList.add('nav-active');
            window.scrollTo(0,0);
        }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }
        function copyRef() { const el = document.getElementById('ref-link'); el.select(); document.execCommand('copy'); alert("Link Copied!"); }
    </script>
</body>
</html>
