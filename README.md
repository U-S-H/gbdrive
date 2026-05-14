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
        .glass { background: rgba(22, 27, 34, 0.7); backdrop-filter: blur(25px); border: 1px solid rgba(255,255,255,0.1); border-radius: 30px; }
        .page { display: none; padding: 20px; animation: slideUp 0.4s cubic-bezier(0.4, 0, 0.2, 1); }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        
        .nav-bar { position: fixed; bottom: 15px; left: 15px; right: 15px; height: 75px; display: flex; justify-content: space-around; align-items: center; z-index: 5000; border-radius: 25px; background: rgba(13, 17, 23, 0.95); border: 1px solid rgba(59, 130, 246, 0.2); backdrop-filter: blur(15px); }
        .nav-active { color: #3b82f6; text-shadow: 0 0 15px rgba(59, 130, 246, 0.5); }
        
        #wheel { width: 280px; height: 280px; border-radius: 50%; border: 8px solid #ffd700; transition: transform 5s cubic-bezier(0.1, 0, 0.1, 1); }
        .premium-btn { background: linear-gradient(135deg, #3b82f6 0%, #1d4ed8 100%); box-shadow: 0 10px 25px rgba(37, 99, 235, 0.4); }
        
        /* Admin Overlay */
        #admin-ui { position: fixed; inset: 0; background: #010409; z-index: 9999; display: none; overflow-y: auto; padding: 20px; }
    </style>
</head>
<body class="pt-16 pb-32">

    <div class="fixed top-0 w-full bg-blue-600/10 border-b border-blue-500/20 py-2 z-[6000] backdrop-blur-md overflow-hidden">
        <div id="ticker-content" class="whitespace-nowrap text-[10px] font-bold text-blue-400 text-center uppercase tracking-widest animate-pulse">
            Loading Live Updates...
        </div>
    </div>

    <header class="p-6 flex justify-between items-center flex-row-reverse">
        <div class="flex items-center gap-4 flex-row-reverse">
            <img id="user-photo" onclick="handleAdminTap()" class="w-14 h-14 rounded-2xl border-2 border-blue-500/30 cursor-pointer object-cover">
            <div class="text-right">
                <p id="user-name" class="text-xs font-black uppercase">Syncing...</p>
                <span id="v-tier" class="px-3 py-1 rounded-full text-[8px] font-black uppercase bg-blue-500/20 text-blue-400">Standard</span>
            </div>
        </div>
        <button onclick="toggleChat()" class="w-12 h-12 glass flex items-center justify-center text-blue-400 shadow-lg">
            <i class="fa-solid fa-headset text-xl"></i>
        </button>
    </header>

    <main id="app-ui">
        <div id="p-home" class="page active-page">
            <div class="glass p-10 mb-8 text-center border-blue-500/20 bg-gradient-to-br from-blue-600/10 to-transparent">
                <p class="text-[9px] opacity-50 uppercase tracking-widest mb-2">Available Balance</p>
                <h2 id="v-bal" class="text-5xl font-black italic mb-10 tracking-tighter">₨ 0.00</h2>
                <div class="flex gap-4">
                    <button onclick="showPage('finance')" class="flex-1 premium-btn py-5 rounded-2xl font-black text-[10px] uppercase">Deposit</button>
                    <button onclick="showPage('withdraw')" class="flex-1 bg-white/5 py-5 rounded-2xl font-black text-[10px] uppercase border border-white/10">Withdraw</button>
                </div>
            </div>
            <div id="plans-grid" class="space-y-4"></div>
        </div>

        <div id="p-spin" class="page text-center">
            <h2 class="text-2xl font-black italic mb-10">LUCKY <span class="text-yellow-500">SPIN</span></h2>
            <div class="flex justify-center mb-10">
                <img id="wheel" src="https://i.ibb.co/V9X9999/wheel.png" onerror="this.src='https://via.placeholder.com/300/FFD700/000000?text=SPIN+WHEEL'">
            </div>
            <button onclick="executeSpin()" class="w-full max-w-xs premium-btn py-6 rounded-full font-black text-xs uppercase">Spin (₨ 50)</button>
        </div>

        <div id="p-finance" class="page">
            <h2 class="text-2xl font-black italic mb-8">ADD <span class="text-blue-500">FUNDS</span></h2>
            <div class="glass p-8 space-y-5">
                <div class="bg-blue-600/10 p-5 rounded-2xl border border-blue-500/20 text-center">
                    <p class="text-[10px] opacity-50 mb-1">EasyPaisa Account</p>
                    <p class="text-xl font-black text-blue-400">03379827882</p>
                </div>
                <input id="d-amt" type="number" placeholder="رقم" class="w-full bg-black/50 p-5 rounded-2xl text-xs border border-white/10 outline-none">
                <input id="d-tid" type="text" placeholder="Transaction ID (TID)" class="w-full bg-black/50 p-5 rounded-2xl text-xs border border-white/10 outline-none">
                <button onclick="submitTx('Deposit')" class="w-full premium-btn py-5 rounded-2xl font-black uppercase">Submit Deposit</button>
            </div>
        </div>

        <div id="p-withdraw" class="page">
            <h2 class="text-2xl font-black italic mb-8">CASH <span class="text-red-500">OUT</span></h2>
            <div class="glass p-8 space-y-5">
                <input id="w-amt" type="number" placeholder="رقم" class="w-full bg-black/50 p-5 rounded-2xl text-xs border border-white/10 outline-none">
                <input id="w-pin" type="password" maxlength="4" placeholder="والٹ پن" class="w-full bg-black/50 p-5 rounded-2xl text-center font-black tracking-[1.5em] border border-white/10 outline-none">
                <button onclick="handleWithdraw()" class="w-full bg-white text-black py-5 rounded-2xl font-black uppercase">Withdraw Now</button>
            </div>
        </div>
    </main>

    <div id="admin-ui">
        <div class="flex justify-between items-center mb-8">
            <h1 class="text-2xl font-black">ADMIN <span class="text-blue-500">PANEL</span></h1>
            <button onclick="closeAdmin()" class="text-red-500 text-xl"><i class="fa-solid fa-circle-xmark"></i></button>
        </div>
        <div id="adm-tx-list" class="space-y-4"></div>
    </div>

    <nav class="nav-bar">
        <button onclick="showPage('home')" id="nav-home" class="nav-active transition-all"><i class="fa-solid fa-house-chimney text-xl"></i></button>
        <button onclick="showPage('spin')" id="nav-spin" class="opacity-50"><i class="fa-solid fa-circle-notch text-xl"></i></button>
        <button onclick="showPage('finance')" id="nav-finance" class="opacity-50"><i class="fa-solid fa-plus-circle text-xl"></i></button>
        <button onclick="showPage('withdraw')" id="nav-withdraw" class="opacity-50"><i class="fa-solid fa-wallet text-xl"></i></button>
    </nav>

    <script>
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
        let userObj = null, tapCount = 0;

        auth.onAuthStateChanged(async user => {
            if(user) {
                document.getElementById('user-photo').src = user.photoURL;
                document.getElementById('user-name').innerText = user.displayName;
                syncData(user.displayName);
                renderPlans();
                startTicker();
            } else {
                auth.signInWithPopup(new firebase.auth.GoogleAuthProvider());
            }
        });

        function syncData(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                if(doc.exists) {
                    userObj = doc.data();
                    document.getElementById('v-bal').innerText = "₨ " + (userObj.balance || 0).toLocaleString();
                } else {
                    db.collection("users").doc(name).set({ balance: 0, walletPin: "1234", lastSpin: 0 });
                }
            });
        }

        function renderPlans() {
            let h = "";
            const costs = [200, 500, 1000, 2500, 5000];
            costs.forEach(cost => {
                let daily = Math.floor(cost * 0.12);
                h += `
                <div class="glass p-6 flex justify-between items-center border-white/5">
                    <div class="text-right">
                        <h4 class="text-xl font-black italic">₨ ${cost.toLocaleString()}</h4>
                        <p class="text-[8px] text-blue-400 font-bold uppercase">Daily: ₨ ${daily} | 30 Days</p>
                    </div>
                    <button onclick="invest(${cost})" class="bg-blue-600 px-6 py-2 rounded-xl font-black text-[9px] uppercase shadow-lg">Invest</button>
                </div>`;
            });
            document.getElementById('plans-grid').innerHTML = h;
        }

        async function invest(cost) {
            if(userObj.balance < cost) return alert("بیلنس کم ہے!");
            await db.collection("users").doc(auth.currentUser.displayName).update({
                balance: firebase.firestore.FieldValue.increment(-cost)
            });
            alert("انویسٹمنٹ کامیاب!");
        }

        function handleAdminTap() {
            tapCount++;
            if(tapCount >= 10) {
                let key = prompt("Enter Key:");
                if(key === "net204") openAdmin();
                tapCount = 0;
            }
        }

        function openAdmin() {
            document.getElementById('admin-ui').style.display = 'block';
            db.collection("txs").where("status","==","Pending").onSnapshot(s => {
                let h = ""; s.forEach(doc => {
                    const d = doc.data();
                    h += `<div class="glass p-6 border-blue-500/20"><p class="text-xs font-bold">${d.user} (${d.type})</p><h2 class="text-2xl font-black italic my-2">₨ ${d.amount}</h2><button onclick="approveTx('${doc.id}','${d.user}',${d.amount})" class="w-full bg-green-600 py-3 rounded-xl font-black text-[10px]">APPROVE</button></div>`;
                });
                document.getElementById('adm-tx-list').innerHTML = h || "No Pending Requests.";
            });
        }

        async function approveTx(id, user, amount) {
            await db.collection("txs").doc(id).update({status: "Approved"});
            await db.collection("users").doc(user).update({ balance: firebase.firestore.FieldValue.increment(amount) });
            alert("Approved!");
        }

        function closeAdmin() { document.getElementById('admin-ui').style.display = 'none'; }

        function showPage(p) {
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.getElementById('p-'+p).classList.add('active-page');
            document.querySelectorAll('nav button').forEach(btn => {
                btn.classList.remove('nav-active');
                btn.classList.add('opacity-50');
            });
            document.getElementById('nav-'+p).classList.add('nav-active');
            document.getElementById('nav-'+p).classList.remove('opacity-50');
        }

        function startTicker() {
            const msgs = ["🔥 @Ali ne ₨ 2,000 انویسٹ کیے", "⚡ ودرا ریکویسٹ اپروو ہو گئی", "✅ ایجنٹ 204 آن لائن ہے"];
            let i = 0;
            setInterval(() => {
                document.getElementById('ticker-content').innerText = msgs[i];
                i = (i + 1) % msgs.length;
            }, 4000);
        }

        async function submitTx(type) {
            const amt = document.getElementById('d-amt').value;
            const tid = document.getElementById('d-tid').value;
            if(!amt || !tid) return alert("تمام خانے پُر کریں");
            await db.collection("txs").add({ user: auth.currentUser.displayName, amount: parseInt(amt), tid: tid, type: type, status: "Pending", time: Date.now() });
            alert("ڈیپازٹ ریکویسٹ بھیج دی گئی!");
        }

        async function handleWithdraw() {
            const pin = document.getElementById('w-pin').value;
            const amt = parseInt(document.getElementById('w-amt').value);
            if(pin !== userObj.walletPin) return alert("غلط پن!");
            if(amt > userObj.balance) return alert("بیلنس کم ہے!");
            await db.collection("txs").add({ user: auth.currentUser.displayName, amount: amt, type: "Withdraw", status: "Pending", time: Date.now() });
            alert("ودرا ریکویسٹ بھیج دی گئی!");
        }
    </script>
</body>
</html>
