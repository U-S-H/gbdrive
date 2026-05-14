<html lang="ur" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/animate.css/4.1.1/animate.min.css"/>
    <title>ویسٹی فائی الٹیمیٹ پرو</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;700;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #010409; color: white; margin: 0; overflow-x: hidden; text-align: right; }
        .glass { background: rgba(22, 27, 34, 0.7); backdrop-filter: blur(20px); border: 1px solid rgba(255,255,255,0.08); border-radius: 28px; transition: 0.3s; }
        .glass:hover { border-color: rgba(59, 130, 246, 0.4); transform: translateY(-5px); }
        .page { display: none; padding: 20px; animation: fadeIn 0.4s ease-in-out; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: scale(0.98); } to { opacity: 1; transform: scale(1); } }
        
        .premium-btn { background: linear-gradient(135deg, #3b82f6 0%, #1d4ed8 100%); box-shadow: 0 10px 20px rgba(37, 99, 235, 0.3); border: none; }
        .premium-btn:active { transform: scale(0.95); }

        .nav-bar { position: fixed; bottom: 15px; left: 15px; right: 15px; height: 75px; display: flex; justify-content: space-around; align-items: center; z-index: 5000; border-radius: 25px; background: rgba(13, 17, 23, 0.95); border: 1px solid rgba(255,255,255,0.1); backdrop-filter: blur(15px); }
        .nav-active { color: #3b82f6; text-shadow: 0 0 15px rgba(59, 130, 246, 0.6); }

        #wheel { width: 280px; height: 280px; border-radius: 50%; border: 8px solid #ffd700; transition: transform 5s cubic-bezier(0.1, 0, 0.1, 1); box-shadow: 0 0 40px rgba(255,215,0,0.2); }
    </style>
</head>
<body class="pt-16 pb-32">

    <div class="fixed top-0 w-full bg-blue-600/10 border-b border-blue-500/20 py-2 z-[6000] backdrop-blur-md overflow-hidden">
        <div id="ticker-content" class="whitespace-nowrap animate-pulse text-[10px] font-bold text-blue-400 text-center">
            Loading Live Updates...
        </div>
    </div>

    <header class="p-6 flex justify-between items-center flex-row-reverse">
        <div class="flex items-center gap-4 flex-row-reverse">
            <div class="relative group">
                <img id="user-photo" onclick="handleAdminTap()" class="w-14 h-14 rounded-2xl border-2 border-blue-500/30 cursor-pointer object-cover">
                <div class="absolute -bottom-1 -right-1 w-4 h-4 bg-green-500 border-2 border-[#010409] rounded-full animate-pulse"></div>
            </div>
            <div class="text-right">
                <p id="user-name" class="text-xs font-black uppercase">User Name</p>
                <span id="v-tier" class="px-3 py-1 rounded-full text-[8px] font-black uppercase bg-blue-500/20 text-blue-400 border border-blue-500/30">Bronze Node</span>
            </div>
        </div>
        <button onclick="toggleChat()" class="w-12 h-12 glass flex items-center justify-center text-blue-400">
            <i class="fa-solid fa-headset text-xl"></i>
        </button>
    </header>

    <main id="app-ui">
        <div id="p-home" class="page active-page">
            <div class="glass p-8 mb-6 text-center border-blue-500/20 bg-gradient-to-br from-blue-600/10 to-transparent">
                <p class="text-[9px] opacity-50 uppercase tracking-widest mb-2">Available Balance</p>
                <h2 id="v-bal" class="text-5xl font-black italic mb-8">₨ 0.00</h2>
                <div class="flex gap-3">
                    <button onclick="showPage('finance')" class="flex-1 premium-btn py-4 rounded-xl font-black text-[10px]">DEPOSIT</button>
                    <button onclick="showPage('withdraw')" class="flex-1 bg-white/5 py-4 rounded-xl font-black text-[10px] border border-white/10">WITHDRAW</button>
                </div>
            </div>

            <div class="flex gap-2 mb-6">
                <button id="btn-n" onclick="renderPlans('normal')" class="flex-1 py-3 rounded-xl bg-blue-600 text-[9px] font-black">STANDARD</button>
                <button id="btn-s" onclick="renderPlans('special')" class="flex-1 py-3 rounded-xl bg-white/5 text-[9px] font-black opacity-50">PREMIUM</button>
            </div>
            <div id="plans-grid" class="space-y-4"></div>
        </div>

        <div id="p-spin" class="page text-center">
            <h2 class="text-2xl font-black italic mb-10">GOLDEN <span class="text-yellow-500">WHEEL</span></h2>
            <div class="flex justify-center mb-10">
                <img id="wheel" src="https://i.ibb.co/V9X9999/wheel.png" onerror="this.src='https://via.placeholder.com/300/FFD700/000000?text=SPIN+WHEEL'">
            </div>
            <button id="spin-btn" onclick="executeSpin()" class="w-full max-w-xs premium-btn py-5 rounded-full font-black text-xs">SPIN NOW (₨ 50)</button>
        </div>

        <div id="p-finance" class="page">
            <h2 class="text-2xl font-black italic mb-6">RECHARGE <span class="text-blue-500">ACCOUNT</span></h2>
            <div class="glass p-6 space-y-4">
                <div class="bg-blue-600/10 p-4 rounded-2xl border border-blue-500/20">
                    <p class="text-[10px] opacity-50 mb-1">EasyPaisa Account</p>
                    <p class="text-xl font-black text-blue-400">03379827882</p>
                </div>
                <input id="d-amt" type="number" placeholder="رقم درج کریں" class="w-full bg-black/50 p-4 rounded-xl text-xs border border-white/10 focus:border-blue-500 outline-none">
                <input id="d-tid" type="text" placeholder="ٹرانزیکشن آئی ڈی (TID)" class="w-full bg-black/50 p-4 rounded-xl text-xs border border-white/10 focus:border-blue-500 outline-none">
                <button onclick="submitTx('Deposit')" class="w-full premium-btn py-5 rounded-xl font-black">SUBMIT DEPOSIT</button>
            </div>
        </div>

        <div id="p-withdraw" class="page">
            <h2 class="text-2xl font-black italic mb-6">CASH <span class="text-red-500">OUT</span></h2>
            <div class="glass p-6 space-y-4">
                <input id="w-num" type="text" placeholder="Account Number" class="w-full bg-black/50 p-4 rounded-xl text-xs border border-white/10 outline-none">
                <input id="w-amt" type="number" placeholder="Amount" class="w-full bg-black/50 p-4 rounded-xl text-xs border border-white/10 outline-none">
                <input id="w-pin" type="password" maxlength="4" placeholder="Wallet PIN" class="w-full bg-black/50 p-4 rounded-xl text-center font-black tracking-[1em] border border-white/10 outline-none">
                <button onclick="handleWithdraw()" class="w-full bg-white text-black py-5 rounded-xl font-black">WITHDRAW NOW</button>
            </div>
        </div>
    </main>

    <nav class="nav-bar">
        <button onclick="showPage('home')" class="text-xl opacity-50 transition-all" id="nav-home"><i class="fa-solid fa-house"></i></button>
        <button onclick="showPage('spin')" class="text-xl opacity-50 transition-all" id="nav-spin"><i class="fa-solid fa-circle-dot"></i></button>
        <button onclick="showPage('finance')" class="text-xl opacity-50 transition-all" id="nav-finance"><i class="fa-solid fa-plus-circle"></i></button>
        <button onclick="showPage('withdraw')" class="text-xl opacity-50 transition-all" id="nav-withdraw"><i class="fa-solid fa-wallet"></i></button>
    </nav>

    <script>
        // Firebase Configuration
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
                renderPlans('normal');
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

        function renderPlans(type) {
            document.getElementById('btn-n').style.opacity = type === 'normal' ? '1' : '0.4';
            document.getElementById('btn-s').style.opacity = type === 'special' ? '1' : '0.4';
            let h = "";
            const costs = type === 'normal' ? [200, 500, 1000, 2500] : [5000, 10000, 25000];
            
            costs.forEach((cost, i) => {
                let daily = Math.floor(cost * (type === 'normal' ? 0.12 : 0.18));
                h += `
                <div class="glass p-5 flex justify-between items-center animate__animated animate__fadeInUp" style="animation-delay: ${i * 0.1}s">
                    <div class="text-right">
                        <h4 class="text-lg font-black italic">₨ ${cost.toLocaleString()}</h4>
                        <p class="text-[8px] text-blue-400 font-bold uppercase">Daily: ₨ ${daily} | 30 Days</p>
                    </div>
                    <button onclick="invest(${cost})" class="bg-blue-600 px-6 py-2 rounded-lg font-black text-[9px] shadow-lg active:scale-90">INVEST</button>
                </div>`;
            });
            document.getElementById('plans-grid').innerHTML = h;
        }

        async function invest(cost) {
            if(userObj.balance < cost) return alert("بیلنس کم ہے! براہ کرم ڈیپازٹ کریں۔");
            if(confirm(`کیا آپ ₨ ${cost} انویسٹ کرنا چاہتے ہیں؟`)) {
                await db.collection("users").doc(auth.currentUser.displayName).update({
                    balance: firebase.firestore.FieldValue.increment(-cost)
                });
                alert("انویسٹمنٹ کامیاب! آپ کا روزانہ منافع شروع ہو چکا ہے۔");
            }
        }

        function showPage(p) {
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.getElementById('p-'+p).classList.add('active-page');
            document.querySelectorAll('nav button').forEach(btn => btn.classList.remove('nav-active', 'opacity-100'));
            document.getElementById('nav-'+p).classList.add('nav-active', 'opacity-100');
        }

        function handleAdminTap() {
            tapCount++;
            if(tapCount >= 10) {
                let key = prompt("Enter Admin Key:");
                if(key === "net204") alert("Admin Access Granted (Redirecting...)");
                tapCount = 0;
            }
        }

        function startTicker() {
            const msgs = ["⚡ @Umar ne ₨ 4,500 withdraw کیے", "🔥 New VIP Node Active", "✅ Agent 204 Online", "💰 Profit Added to All Wallets"];
            let i = 0;
            setInterval(() => {
                document.getElementById('ticker-content').innerText = msgs[i];
                i = (i + 1) % msgs.length;
            }, 5000);
        }

        async function submitTx(type) {
            const amt = document.getElementById('d-amt').value;
            const tid = document.getElementById('d-tid').value;
            if(!amt || !tid) return alert("تمام خانے پُر کریں");
            await db.collection("txs").add({ 
                user: auth.currentUser.displayName, 
                amount: parseInt(amt), 
                tid: tid, 
                type: type, 
                status: "Pending", 
                time: Date.now() 
            });
            alert("ڈیپازٹ ریکویسٹ بھیج دی گئی ہے!");
        }

        async function handleWithdraw() {
            const pin = document.getElementById('w-pin').value;
            const amt = parseInt(document.getElementById('w-amt').value);
            if(pin !== userObj.walletPin) return alert("غلط والٹ پن!");
            if(amt > userObj.balance) return alert("بیلنس ناکافی ہے!");
            await db.collection("txs").add({ 
                user: auth.currentUser.displayName, 
                amount: amt, 
                type: "Withdrawal", 
                status: "Pending", 
                time: Date.now() 
            });
            alert("ودرا ریکویسٹ وصول ہو گئی ہے۔");
        }
    </script>
</body>
</html>
