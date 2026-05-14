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
        .nav-bar { position: fixed; bottom: 15px; left: 15px; right: 15px; height: 75px; display: flex; justify-content: space-around; align-items: center; z-index: 5000; border-radius: 25px; background: rgba(22, 27, 34, 0.95); border: 1px solid rgba(255,255,255,0.05); flex-direction: row-reverse; }
        .nav-btn { color: rgba(255,255,255,0.3); transition: 0.3s; }
        .nav-active { color: #3b82f6; transform: translateY(-5px); text-shadow: 0 0 15px #3b82f6; }
        .spin-wheel { width: 280px; height: 280px; border-radius: 50%; border: 8px solid #1f2937; position: relative; transition: transform 4s cubic-bezier(0.15, 0, 0.15, 1); background: conic-gradient(#3b82f6 0deg 45deg, #1e293b 45deg 90deg, #3b82f6 90deg 135deg, #1e293b 135deg 180deg, #3b82f6 180deg 225deg, #1e293b 225deg 270deg, #3b82f6 270deg 315deg, #1e293b 315deg 360deg); }
        .payout-alert { background: rgba(34, 197, 94, 0.1); color: #22c55e; padding: 10px 20px; border-radius: 50px; font-size: 10px; font-weight: bold; margin-bottom: 20px; display: inline-block; animation: pulse 2s infinite; }
        @keyframes pulse { 0% { opacity: 0.6; } 50% { opacity: 1; } 100% { opacity: 0.6; } }
        #login-screen { position: fixed; inset: 0; display: flex; flex-direction: column; align-items: center; justify-content: center; background: #010409; z-index: 10000; }
        .pin-input { letter-spacing: 1em; text-align: center; font-size: 24px; font-weight: 900; }
    </style>
</head>
<body class="pb-32">

    <div id="login-screen">
        <h1 class="text-6xl font-black italic mb-2 tracking-tighter">VESTIFY</h1>
        <p class="text-blue-500 font-bold tracking-[0.5em] text-[10px] uppercase mb-16">الٹیمیٹ ٹرمینل</p>
        <button onclick="login()" class="w-72 h-20 bg-white text-black font-black rounded-full shadow-2xl active:scale-95 transition-all">گوگل سے لاگ ان کریں</button>
    </div>

    <div id="pin-modal" class="hidden fixed inset-0 z-[15000] bg-black/95 flex items-center justify-center p-6 text-center">
        <div class="glass p-8 w-full max-w-xs">
            <h3 class="text-xl font-black mb-2">سیکیورٹی پن</h3>
            <p class="text-[10px] text-blue-400 mb-6 font-bold">رقم نکالنے کے لیے 4 ہندسوں کا پن سیٹ کریں</p>
            <input id="set-pin" type="password" maxlength="4" placeholder="••••" class="w-full bg-black/40 p-5 rounded-2xl border border-white/10 outline-none mb-6 pin-input">
            <button onclick="saveWalletPin()" class="w-full bg-blue-600 py-4 rounded-2xl font-black">پن محفوظ کریں</button>
        </div>
    </div>

    <main id="app-ui" class="hidden">
        <header class="p-6 flex justify-between items-center flex-row-reverse">
            <div class="flex items-center gap-4 flex-row-reverse">
                <img id="user-photo" onclick="handleAdminTap()" class="w-12 h-12 rounded-2xl border-2 border-white/5">
                <div class="text-right">
                    <p id="user-name" class="text-xs font-black uppercase">لوڈنگ...</p>
                    <p class="text-[8px] font-bold text-green-500">اکاؤنٹ فعال ہے</p>
                </div>
            </div>
            <button onclick="showPage('support')" class="w-11 h-11 glass flex items-center justify-center text-blue-500"><i class="fa-solid fa-headset"></i></button>
        </header>

        <div id="p-home" class="page active-page text-center">
            <div class="payout-alert"><i class="fa-solid fa-check-circle ml-2"></i> عمران نے ابھی ₨ 5,000 نکال لیے</div>
            
            <div class="glass p-8 mb-6 bg-gradient-to-br from-blue-600/10 to-transparent border-blue-500/20">
                <p class="text-[9px] font-black opacity-40 uppercase mb-2">موجودہ فنڈز</p>
                <h2 id="v-bal" class="text-5xl font-black italic mb-8 tracking-tighter">₨ 0</h2>
                <div class="flex gap-4">
                    <button onclick="showPage('finance')" class="flex-1 bg-white text-black py-4 rounded-2xl font-black text-[11px]">جمع کریں</button>
                    <button onclick="showPage('withdraw')" class="flex-1 glass py-4 rounded-2xl font-black text-[11px]">ودرا</button>
                </div>
            </div>

            <div class="glass p-5 mb-6 flex justify-between items-center flex-row-reverse border-white/5">
                <div class="text-right">
                    <p class="text-[10px] font-black uppercase">کلاؤڈ مائننگ جاری ہے</p>
                    <p id="timer" class="text-[9px] text-blue-500 font-bold">اگلا پرافٹ: 23:59:59</p>
                </div>
                <div class="w-8 h-8 rounded-full border-2 border-blue-500 border-t-transparent animate-spin"></div>
            </div>

            <div class="glass p-4 mb-6 h-48 overflow-hidden">
                <iframe src="https://s.tradingview.com/widgetembed/?symbol=BINANCE%3ABTCUSDT&theme=dark" width="100%" height="100%" frameborder="0"></iframe>
            </div>

            <h4 class="text-[10px] font-black opacity-30 mb-5 text-right tracking-widest uppercase">انویسٹمنٹ پلانز</h4>
            <div id="plans-grid" class="space-y-6 pb-12"></div>
        </div>

        <div id="p-finance" class="page">
            <h2 class="text-2xl font-black italic mb-8 text-right">رقم <span class="text-blue-500">جمع کریں</span></h2>
            <div class="glass p-7 space-y-5">
                <select id="dep-method" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/10 outline-none">
                    <option value="Easypaisa">Easypaisa</option>
                    <option value="JazzCash">JazzCash</option>
                    <option value="USDT">USDT (Binance)</option>
                </select>
                <div class="bg-blue-600/10 p-5 rounded-2xl border border-blue-500/10 text-center">
                    <p class="text-[9px] opacity-40 mb-1 uppercase">آفیشل اکاؤنٹ</p>
                    <p id="acc-num" class="text-lg font-black text-blue-400">03XXXXXXXXX</p>
                </div>
                <input id="d-amt" type="number" placeholder="رقم درج کریں" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/10 outline-none">
                <input id="d-tid" type="text" placeholder="ٹرانزیکشن آئی ڈی (TID)" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/10 outline-none">
                <button onclick="submitTx('Deposit')" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-sm">ڈیپازٹ بھیجیں</button>
            </div>
        </div>

        <div id="p-withdraw" class="page">
            <h2 class="text-2xl font-black italic mb-8 text-right">رقم <span class="text-blue-500">نکلوائیں</span></h2>
            <div class="glass p-7 space-y-5 text-center">
                <input id="w-amt" type="number" placeholder="نکلوائی جانے والی رقم" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/10 outline-none">
                <input id="w-num" type="text" placeholder="اکاؤنٹ نمبر لکھیں" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/10 outline-none">
                <input id="w-pin" type="password" maxlength="4" placeholder="والٹ پن" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/10 outline-none pin-input">
                <button onclick="handleWithdraw()" class="w-full bg-white text-black py-5 rounded-2xl font-black text-sm">ودرا ریکویسٹ</button>
            </div>
        </div>

        <div id="p-spin" class="page text-center">
            <h2 class="text-3xl font-black italic mb-10">لکی <span class="text-blue-500">سپن</span></h2>
            <div class="flex justify-center mb-12 relative">
                <div class="absolute -top-4 text-red-500 text-3xl z-10 animate-bounce"><i class="fa-solid fa-caret-down"></i></div>
                <div id="wheel" class="spin-wheel"></div>
            </div>
            <button id="spin-btn" onclick="spinNow()" class="w-64 bg-blue-600 py-5 rounded-full font-black text-lg">گھمائیں</button>
        </div>

        <div id="p-team" class="page text-center">
            <h2 class="text-2xl font-black italic mb-8">ریفرل <span class="text-blue-500">نیٹ ورک</span></h2>
            <div class="glass p-8 mb-6">
                <p class="text-[10px] opacity-40 mb-4 uppercase">آپ کا انوائٹ لنک</p>
                <input id="ref-link" readonly class="w-full bg-black/40 p-4 rounded-xl text-[10px] border border-white/10 mb-4 text-center text-blue-400 outline-none">
                <button onclick="copyRef()" class="w-full bg-white text-black py-4 rounded-xl font-black text-[11px]">لنک کاپی کریں</button>
            </div>
        </div>

        <nav class="nav-bar">
            <button onclick="showPage('home')" class="nav-btn p-4" id="nav-home"><i class="fa-solid fa-home text-xl"></i></button>
            <button onclick="showPage('finance')" class="nav-btn p-4" id="nav-finance"><i class="fa-solid fa-plus-circle text-xl"></i></button>
            <button onclick="showPage('spin')" class="nav-btn p-4" id="nav-spin"><i class="fa-solid fa-dharmachakra text-xl"></i></button>
            <button onclick="showPage('team')" class="nav-btn p-4" id="nav-team"><i class="fa-solid fa-users text-xl"></i></button>
        </nav>
    </main>

    <script>
        // Firebase Config
        const firebaseConfig = { apiKey: "AIzaSyC9ofJ1KxRXHnxilpU9gyI87D3BSOZ9v1g", authDomain: "vestify-991f2.firebaseapp.com", projectId: "vestify-991f2", storageBucket: "vestify-991f2.firebasestorage.app", messagingSenderId: "799007097733", appId: "1:799007097733:web:ed3b35b6c4e51dc2e7baec" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore(), auth = firebase.auth();
        let userObj = null, tapCount = 0;

        auth.onAuthStateChanged(user => {
            if(user) {
                document.getElementById('login-screen').style.display = 'none';
                document.getElementById('app-ui').classList.remove('hidden');
                document.getElementById('user-photo').src = user.photoURL;
                document.getElementById('user-name').innerText = user.displayName;
                checkUserPin(user.displayName);
                syncData(user.displayName);
                renderPlans();
                startCountdown();
            }
        });

        function login() { auth.signInWithPopup(new firebase.auth.GoogleAuthProvider()); }

        async function checkUserPin(name) {
            const doc = await db.collection("users").doc(name).get();
            if(!doc.exists || !doc.data().walletPin) {
                document.getElementById('pin-modal').classList.remove('hidden');
            }
        }

        async function saveWalletPin() {
            const pin = document.getElementById('set-pin').value;
            if(pin.length !== 4) return alert("براہ کرم 4 ہندسوں کا پن لکھیں!");
            await db.collection("users").doc(auth.currentUser.displayName).set({ walletPin: pin }, { merge: true });
            document.getElementById('pin-modal').classList.add('hidden');
            alert("پن محفوظ کر لیا گیا!");
        }

        function renderPlans() {
            const plans = [
                { id: 1, cost: 500, daily: 50, days: 30, img: "https://images.unsplash.com/photo-1621761126064-fdd86782a778?auto=format&fit=crop&w=400" },
                { id: 2, cost: 2000, daily: 250, days: 30, img: "https://images.unsplash.com/photo-1639762681485-074b7f938ba0?auto=format&fit=crop&w=400" },
                { id: 3, cost: 5000, daily: 700, days: 30, img: "https://images.unsplash.com/photo-1642104704074-907c0698cbd9?auto=format&fit=crop&w=400" }
            ];
            let h = "";
            plans.forEach(p => {
                h += `<div class="glass p-2 overflow-hidden text-right">
                    <img src="${p.img}" class="w-full h-32 object-cover rounded-2xl mb-4 opacity-80">
                    <div class="p-4">
                        <div class="flex justify-between mb-4 flex-row-reverse">
                            <h3 class="text-sm font-black italic uppercase">ورژن ${p.id}</h3>
                            <span class="text-blue-500 font-bold text-[10px]">PREMIUM</span>
                        </div>
                        <div class="grid grid-cols-2 gap-2 mb-6">
                            <div><p class="text-[8px] opacity-40 uppercase">روزانہ منافع</p><p class="text-green-500 font-black text-xs">₨ ${p.daily}</p></div>
                            <div><p class="text-[8px] opacity-40 uppercase">ٹوٹل منافع</p><p class="text-white font-black text-xs">₨ ${p.daily*p.days}</p></div>
                        </div>
                        <button onclick="alert('رقم جمع کرائیں!')" class="w-full bg-white text-black py-4 rounded-xl font-black text-[10px]">خریدیں ₨ ${p.cost}</button>
                    </div>
                </div>`;
            });
            document.getElementById('plans-grid').innerHTML = h;
        }

        function spinNow() {
            const wheel = document.getElementById('wheel');
            const deg = Math.floor(Math.random() * 360) + 1800;
            wheel.style.transform = `rotate(${deg}deg)`;
            setTimeout(() => {
                alert("مبارک ہو! آپ نے بونس جیت لیا۔");
                db.collection("users").doc(auth.currentUser.displayName).update({ balance: firebase.firestore.FieldValue.increment(50) });
            }, 4000);
        }

        async function handleWithdraw() {
            const pin = document.getElementById('w-pin').value;
            const amt = document.getElementById('w-amt').value;
            if(pin !== userObj.walletPin) return alert("غلط پن!");
            if(amt > userObj.balance) return alert("بیلنس کم ہے!");
            await db.collection("txs").add({ user: auth.currentUser.displayName, amount: parseInt(amt), type: "Withdrawal", status: "Pending", time: Date.now() });
            alert("درخواست بھیج دی گئی!");
        }

        function showPage(p) {
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.getElementById('p-'+p).classList.add('active-page');
            document.querySelectorAll('.nav-btn').forEach(btn => btn.classList.remove('nav-active'));
            document.getElementById('nav-'+p).classList.add('nav-active');
        }

        function syncData(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                userObj = doc.data();
                document.getElementById('v-bal').innerText = "₨ " + (userObj.balance || 0).toLocaleString();
            });
        }

        function startCountdown() {
            setInterval(() => {
                let now = new Date();
                let h = 23 - now.getHours(), m = 59 - now.getMinutes(), s = 59 - now.getSeconds();
                document.getElementById('timer').innerText = `اگلا پرافٹ: ${h}:${m}:${s}`;
            }, 1000);
        }

        function handleAdminTap() {
            tapCount++;
            if(tapCount >= 10) {
                let key = prompt("ADMIN KEY:");
                if(key === "net204") alert("Welcome Admin!");
            }
        }
        
        async function submitTx(type) {
            const amt = document.getElementById('d-amt').value;
            const tid = document.getElementById('d-tid').value;
            if(!amt || !tid) return alert("تمام خانے پُر کریں!");
            await db.collection("txs").add({ user: auth.currentUser.displayName, amount: parseInt(amt), tid: tid, type: "Deposit", status: "Pending", time: Date.now() });
            alert("ڈیپازٹ موصول ہو گیا، جلد فعال ہوگا!");
        }
    </script>
</body>
</html>
