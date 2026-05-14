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
        @import url('https://fonts.googleapis.com/css2?family=Noto+Nastaliq+Urdu:wght@400;700&family=Plus+Jakarta+Sans:wght@400;700;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', 'Noto Nastaliq Urdu', sans-serif; background-color: #010409; color: white; margin: 0; padding: 0; overflow-x: hidden; text-align: right; }
        .glass { background: rgba(22, 27, 34, 0.75); backdrop-filter: blur(20px); border: 1px solid rgba(255,255,255,0.08); border-radius: 28px; }
        .page { display: none; padding: 20px; animation: slideUp 0.4s ease-out; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(30px); } to { opacity: 1; transform: translateY(0); } }
        
        /* Notifications */
        #notification-box { position: fixed; top: 20px; left: 20px; right: 20px; z-index: 20000; pointer-events: none; }
        .pop-notify { background: #fff; color: #000; padding: 10px 15px; border-radius: 15px; margin-bottom: 8px; box-shadow: 0 10px 20px rgba(0,0,0,0.2); display: flex; align-items: center; gap: 8px; animation: popInOut 4s forwards; font-size: 10px; font-weight: 800; border-right: 4px solid #3b82f6; }
        @keyframes popInOut { 0% { opacity: 0; transform: translateY(-50px); } 10% { opacity: 1; transform: translateY(0); } 90% { opacity: 1; transform: translateY(0); } 100% { opacity: 0; transform: translateY(-50px); } }

        /* Admin UI Special */
        .admin-glass { background: rgba(30, 0, 0, 0.8); border: 1px solid #ff4444; }
        .nav-bar { position: fixed; bottom: 15px; left: 15px; right: 15px; height: 75px; display: flex; justify-content: space-around; align-items: center; z-index: 5000; border-radius: 25px; background: rgba(22, 27, 34, 0.95); border: 1px solid rgba(255,255,255,0.05); flex-direction: row-reverse; }
        .nav-btn { color: rgba(255,255,255,0.3); transition: 0.3s; }
        .nav-active { color: #3b82f6; transform: translateY(-5px); }
        #wheel { width: 280px; height: 280px; border-radius: 50%; transition: transform 5s cubic-bezier(0.15, 0, 0.15, 1); }
    </style>
</head>
<body class="pb-32">

    <div id="notification-box"></div>

    <div id="login-screen" class="fixed inset-0 bg-[#010409] z-[20000] flex flex-col items-center justify-center p-6">
        <h1 class="text-6xl font-black italic mb-2 tracking-tighter">VESTIFY</h1>
        <p class="text-blue-500 font-bold tracking-[0.5em] text-[10px] uppercase mb-16">الٹیمیٹ ورژن</p>
        <button onclick="login()" class="w-72 h-20 bg-white text-black font-black rounded-full shadow-2xl active:scale-95 transition-all">گوگل سے سائن ان کریں</button>
    </div>

    <div id="admin-ui" class="hidden fixed inset-0 z-[30000] bg-black overflow-y-auto p-6">
        <div class="flex justify-between items-center mb-10">
            <button onclick="closeAdmin()" class="text-red-500 font-bold">بند کریں</button>
            <h2 class="text-xl font-black text-red-500">ایڈمن کنٹرول پینل</h2>
        </div>
        <div class="space-y-6">
            <h3 class="text-sm font-bold opacity-50">پینڈنگ ٹرانزیکشنز</h3>
            <div id="adm-tx-list" class="space-y-4"></div>
        </div>
    </div>

    <main id="app-ui" class="hidden">
        <header class="p-6 flex justify-between items-center flex-row-reverse">
            <div class="flex items-center gap-4 flex-row-reverse">
                <img id="user-photo" onclick="handleAdminTap()" class="w-11 h-11 rounded-2xl border-2 border-white/5 cursor-pointer">
                <div class="text-right">
                    <p id="user-name" class="text-xs font-black">Syncing...</p>
                    <p class="text-[8px] font-bold text-blue-500">V.I.P MEMBER</p>
                </div>
            </div>
            <button onclick="showPage('history')" class="w-10 h-10 glass flex items-center justify-center text-blue-500"><i class="fa-solid fa-clock-rotate-left"></i></button>
        </header>

        <div id="p-home" class="page active-page">
            <div class="glass p-8 mb-6 text-center border-blue-500/20 bg-gradient-to-br from-blue-600/10 to-transparent">
                <p class="text-[9px] opacity-40 uppercase mb-2">ٹوٹل بیلنس</p>
                <h2 id="v-bal" class="text-5xl font-black italic mb-8 tracking-tighter">₨ 0</h2>
                <div class="flex gap-3">
                    <button onclick="showPage('finance')" class="flex-1 bg-white text-black py-4 rounded-2xl font-black text-[10px] uppercase">جمع کریں</button>
                    <button onclick="showPage('withdraw')" class="flex-1 glass py-4 rounded-2xl font-black text-[10px] uppercase">نکلوائیں</button>
                </div>
            </div>
            <div id="plans-grid" class="space-y-6 pb-10"></div>
        </div>

        <div id="p-history" class="page">
            <h2 class="text-2xl font-black italic mb-6 text-right">ٹرانزیکشن <span class="text-blue-500">ہسٹری</span></h2>
            <div id="history-list" class="space-y-4 pb-10"></div>
        </div>

        <div id="p-finance" class="page">
            <h2 class="text-2xl font-black italic mb-6 text-right">رقم <span class="text-blue-500">جمع کریں</span></h2>
            <div class="glass p-6 space-y-4">
                <select id="d-method" class="w-full bg-black/40 p-5 rounded-xl text-xs border border-white/10 outline-none">
                    <option>Easypaisa</option>
                    <option>JazzCash</option>
                    <option>USDT (Binance)</option>
                </select>
                <div class="bg-blue-600/10 p-5 rounded-2xl border border-blue-500/10 text-center">
                    <p class="text-[9px] opacity-40 mb-1 uppercase">آفیشل اکاؤنٹ</p>
                    <p class="text-lg font-black text-blue-400">03XXXXXXXXX</p>
                </div>
                <input id="d-amt" type="number" placeholder="رقم" class="w-full bg-black/40 p-5 rounded-xl text-xs border border-white/10 outline-none">
                <input id="d-tid" type="text" placeholder="Transaction ID (TID)" class="w-full bg-black/40 p-5 rounded-xl text-xs border border-white/10 outline-none">
                <button onclick="submitTx('Deposit')" class="w-full bg-blue-600 py-5 rounded-xl font-black text-xs uppercase">ڈیپازٹ کنفرم کریں</button>
            </div>
        </div>

        <div id="p-withdraw" class="page">
            <h2 class="text-2xl font-black italic mb-6 text-right">رقم <span class="text-blue-500">نکلوائیں</span></h2>
            <div class="glass p-6 space-y-4">
                <select id="w-method" class="w-full bg-black/40 p-5 rounded-xl text-xs border border-white/10 outline-none">
                    <option>Easypaisa</option>
                    <option>JazzCash</option>
                    <option>USDT (Binance)</option>
                </select>
                <input id="w-num" type="text" placeholder="اکاؤنٹ نمبر" class="w-full bg-black/40 p-5 rounded-xl text-xs border border-white/10 outline-none">
                <input id="w-amt" type="number" placeholder="رقم" class="w-full bg-black/40 p-5 rounded-xl text-xs border border-white/10 outline-none">
                <input id="w-pin" type="password" maxlength="4" placeholder="والٹ پن" class="w-full bg-black/40 p-5 rounded-xl text-center font-black tracking-[1em] border border-white/10 outline-none">
                <button onclick="submitTx('Withdraw')" class="w-full bg-white text-black py-5 rounded-xl font-black text-xs uppercase">ودرا ریکویسٹ بھیجیں</button>
            </div>
        </div>

        <div id="p-spin" class="page text-center">
            <h2 class="text-2xl font-black italic mb-10">لکی <span class="text-yellow-500">سپن</span></h2>
            <div class="flex justify-center mb-10 relative">
                <div class="absolute -top-4 text-red-500 text-3xl z-10 animate-bounce"><i class="fa-solid fa-caret-down"></i></div>
                <img id="wheel" src="IMG_20260515_012434.jpg" onerror="this.src='https://via.placeholder.com/300'">
            </div>
            <p id="spin-info" class="text-[10px] mb-6 font-black text-blue-400 uppercase">پہلا سپن بالکل مفت ہے!</p>
            <button id="spin-btn" onclick="handleSpin()" class="w-64 bg-blue-600 py-5 rounded-full font-black shadow-xl">قسمت آزمائیں</button>
        </div>

        <div id="p-team" class="page text-center">
            <h2 class="text-2xl font-black italic mb-8">ریفرل <span class="text-blue-500">سسٹم</span></h2>
            <div class="glass p-8">
                <p class="text-[9px] opacity-40 mb-4 uppercase">آپ کا لنک</p>
                <input id="ref-link" readonly class="w-full bg-black/40 p-4 rounded-xl text-[9px] text-blue-400 border border-white/10 mb-4 text-center">
                <button onclick="copyRef()" class="w-full bg-white text-black py-4 rounded-xl font-black text-[10px] uppercase">لنک کاپی کریں</button>
                <div class="mt-10 flex justify-between items-center bg-blue-600/10 p-5 rounded-2xl">
                    <p class="text-xs font-black">ٹوٹل ٹیم ممبرز:</p>
                    <p id="team-count" class="text-xl font-black text-blue-500">0</p>
                </div>
            </div>
        </div>

        <nav class="nav-bar">
            <button onclick="showPage('home')" class="nav-btn p-4" id="nav-home"><i class="fa-solid fa-house"></i></button>
            <button onclick="showPage('spin')" class="nav-btn p-4" id="nav-spin"><i class="fa-solid fa-dharmachakra"></i></button>
            <button onclick="showPage('team')" class="nav-btn p-4" id="nav-team"><i class="fa-solid fa-users"></i></button>
        </nav>
    </main>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyC9ofJ1KxRXHnxilpU9gyI87D3BSOZ9v1g", authDomain: "vestify-991f2.firebaseapp.com", projectId: "vestify-991f2", storageBucket: "vestify-991f2.firebasestorage.app", messagingSenderId: "799007097733", appId: "1:799007097733:web:ed3b35b6c4e51dc2e7baec" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore(), auth = firebase.auth();
        let userObj = null, currentRot = 0, tapCount = 0;

        // Login & Referral Handling
        auth.onAuthStateChanged(async user => {
            if(user) {
                document.getElementById('login-screen').style.display = 'none';
                document.getElementById('app-ui').classList.remove('hidden');
                document.getElementById('user-photo').src = user.photoURL;
                document.getElementById('user-name').innerText = user.displayName;
                
                const params = new URLSearchParams(window.location.search);
                const refBy = params.get('ref');
                const userRef = db.collection("users").doc(user.displayName);
                const doc = await userRef.get();
                
                if(!doc.exists) {
                    await userRef.set({ balance: 0, spinCount: 0, teamCount: 0, referredBy: refBy || "Direct", walletPin: "0000" });
                    if(refBy && refBy !== user.displayName) {
                        await db.collection("users").doc(refBy).update({
                            balance: firebase.firestore.FieldValue.increment(50),
                            teamCount: firebase.firestore.FieldValue.increment(1)
                        });
                    }
                }
                
                syncData(user.displayName);
                renderPlans();
                loadHistory(user.displayName);
                startFakePopups();
                document.getElementById('ref-link').value = `https://u-s-h.github.io/vestify/?ref=${user.displayName.replace(/\s/g, '')}`;
            }
        });

        function login() { auth.signInWithPopup(new firebase.auth.GoogleAuthProvider()); }

        function syncData(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                userObj = doc.data();
                document.getElementById('v-bal').innerText = "₨ " + (userObj.balance || 0).toLocaleString();
                document.getElementById('team-count').innerText = userObj.teamCount || 0;
                document.getElementById('spin-info').innerText = userObj.spinCount > 0 ? "قیمت فی سپن: ₨ 50" : "پہلا سپن بالکل مفت ہے!";
            });
        }

        async function submitTx(type) {
            const amt = (type === 'Deposit') ? document.getElementById('d-amt').value : document.getElementById('w-amt').value;
            if(!amt || amt < 100) return alert("Min amount 100!");
            
            await db.collection("txs").add({
                user: auth.currentUser.displayName,
                amount: parseInt(amt),
                type: type,
                status: "Pending",
                time: Date.now(),
                tid: (type === 'Deposit') ? document.getElementById('d-tid').value : "W-PIN-OK"
            });
            alert("درخواست بھیج دی گئی!");
        }

        function loadHistory(name) {
            db.collection("txs").where("user", "==", name).orderBy("time", "desc").onSnapshot(s => {
                let h = "";
                s.forEach(doc => {
                    const d = doc.data();
                    const color = d.status === "Approved" ? "text-green-500" : (d.status === "Pending" ? "text-yellow-500" : "text-red-500");
                    h += `<div class="glass p-5 flex justify-between items-center flex-row-reverse border-white/5">
                        <div class="text-right"><p class="text-[10px] font-black">${d.type}</p><p class="text-[8px] opacity-40">${new Date(d.time).toLocaleDateString()}</p></div>
                        <div class="text-left"><p class="font-black text-sm">₨ ${d.amount}</p><p class="text-[8px] font-bold ${color}">${d.status}</p></div>
                    </div>`;
                });
                document.getElementById('history-list').innerHTML = h || "<p class='text-center opacity-30 py-10 text-xs'>ہسٹری خالی ہے</p>";
            });
        }

        async function handleSpin() {
            const cost = userObj.spinCount > 0 ? 50 : 0;
            if(cost > userObj.balance) return alert("بیلنس کم ہے!");
            
            const btn = document.getElementById('spin-btn');
            btn.disabled = true;
            currentRot += 1800 + Math.floor(Math.random() * 360);
            document.getElementById('wheel').style.transform = `rotate(${currentRot}deg)`;
            
            setTimeout(async () => {
                const prize = 100;
                await db.collection("users").doc(auth.currentUser.displayName).update({
                    balance: firebase.firestore.FieldValue.increment(prize - cost),
                    spinCount: firebase.firestore.FieldValue.increment(1)
                });
                alert(`آپ نے ₨ ${prize} جیت لیے!`);
                btn.disabled = false;
            }, 5000);
        }

        // Admin Secret Mode
        function handleAdminTap() {
            tapCount++;
            if(tapCount >= 10) {
                let key = prompt("ADMIN KEY:");
                if(key === "net204") loadAdmin();
                tapCount = 0;
            }
        }

        function loadAdmin() {
            document.getElementById('admin-ui').classList.remove('hidden');
            db.collection("txs").where("status", "==", "Pending").onSnapshot(s => {
                let h = "";
                s.forEach(doc => {
                    const d = doc.data();
                    h += `<div class="glass admin-glass p-5 flex flex-col gap-3">
                        <p class="text-xs font-bold">${d.user} (${d.type})</p>
                        <p class="text-2xl font-black">₨ ${d.amount}</p>
                        <div class="flex gap-2">
                            <button onclick="approveTx('${doc.id}', '${d.user}', ${d.amount}, '${d.type}')" class="bg-green-600 flex-1 py-3 rounded-xl font-black text-[10px]">APPROVE</button>
                            <button onclick="db.collection('txs').doc('${doc.id}').update({status:'Rejected'})" class="bg-red-600 flex-1 py-3 rounded-xl font-black text-[10px]">REJECT</button>
                        </div>
                    </div>`;
                });
                document.getElementById('adm-tx-list').innerHTML = h || "No pending tasks.";
            });
        }

        async function approveTx(id, u, a, type) {
            await db.collection("txs").doc(id).update({ status: "Approved" });
            if(type === "Deposit") {
                await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(a) });
            } else {
                await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(-a) });
            }
        }

        function startFakePopups() {
            const names = ["احمد", "علی", "فاطمہ", "عمر", "بلال", "زین", "حمزہ"];
            setInterval(() => {
                if(Math.random() > 0.5) {
                    const n = names[Math.floor(Math.random()*names.length)];
                    const div = document.createElement('div');
                    div.className = "pop-notify";
                    div.innerHTML = `<i class="fa-solid fa-circle-check text-green-500"></i> <span>${n} نے ₨ ${Math.floor(Math.random()*5000 + 500)} نکال لیے</span>`;
                    document.getElementById('notification-box').appendChild(div);
                    setTimeout(() => div.remove(), 4000);
                }
            }, 6000);
        }

        function renderPlans() {
            let h = "";
            for(let i=1; i<=20; i++) {
                let cost = 200 + (i*400);
                h += `<div class="glass p-5 flex justify-between items-center flex-row-reverse border-white/5">
                    <div class="text-right"><p class="text-[9px] font-black text-blue-400">Node v.${i}</p><p class="text-xl font-black">₨ ${cost}</p></div>
                    <button class="bg-white text-black px-6 py-3 rounded-xl font-black text-[10px] uppercase">خریدیں</button>
                </div>`;
            }
            document.getElementById('plans-grid').innerHTML = h;
        }

        function showPage(p) {
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.getElementById('p-'+p).classList.add('active-page');
            document.querySelectorAll('.nav-btn').forEach(btn => btn.classList.remove('nav-active'));
            if(document.getElementById('nav-'+p)) document.getElementById('nav-'+p).classList.add('nav-active');
        }

        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }
        function copyRef() { const el = document.getElementById('ref-link'); el.select(); document.execCommand('copy'); alert("Link Copied!"); }
    </script>
</body>
</html>
