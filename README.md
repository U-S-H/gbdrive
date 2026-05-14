<html lang="ur" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>ویسٹی فائی الٹیمیٹ</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Noto+Nastaliq+Urdu:wght@400;700&family=Plus+Jakarta+Sans:wght@400;700;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', 'Noto Nastaliq Urdu', sans-serif; background-color: #010409; color: white; margin: 0; padding: 0; overflow-x: hidden; text-align: right; }
        .glass { background: rgba(22, 27, 34, 0.7); backdrop-filter: blur(20px); border: 1px solid rgba(255,255,255,0.08); border-radius: 28px; }
        .page { display: none; padding: 20px; animation: slideUp 0.4s ease-out; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        .nav-bar { position: fixed; bottom: 15px; left: 15px; right: 15px; height: 70px; display: flex; justify-content: space-around; align-items: center; z-index: 5000; border-radius: 25px; background: rgba(22, 27, 34, 0.95); border: 1px solid rgba(255,255,255,0.05); flex-direction: row-reverse; }
        .nav-btn { color: rgba(255,255,255,0.3); transition: 0.3s; font-size: 20px; }
        .nav-active { color: #3b82f6; transform: translateY(-5px); text-shadow: 0 0 15px #3b82f6; }
        .plan-card { position: relative; overflow: hidden; background: linear-gradient(145deg, #161b22, #0d1117); transition: 0.3s; }
        .plan-card:active { scale: 0.97; }
        .spin-wheel { width: 280px; height: 280px; border-radius: 50%; border: 8px solid #1f2937; position: relative; transition: transform 4s cubic-bezier(0.15, 0, 0.15, 1); background: conic-gradient(#3b82f6 0deg 45deg, #1e293b 45deg 90deg, #3b82f6 90deg 135deg, #1e293b 135deg 180deg, #3b82f6 180deg 225deg, #1e293b 225deg 270deg, #3b82f6 270deg 315deg, #1e293b 315deg 360deg); }
        .pin-input { letter-spacing: 1.5em; text-align: center; font-size: 24px; font-weight: 900; color: #3b82f6; }
    </style>
</head>
<body class="pb-32">

    <div id="pin-modal" class="hidden fixed inset-0 z-[15000] bg-black/90 flex items-center justify-center p-6">
        <div class="glass p-8 w-full max-w-sm text-center">
            <h3 class="text-xl font-black mb-4">والٹ پن سیٹ کریں</h3>
            <p class="text-[10px] opacity-50 mb-6 text-blue-400 font-bold uppercase tracking-widest">یہ پن ودرا کے لیے ضروری ہے</p>
            <input id="set-pin" type="password" maxlength="4" placeholder="••••" class="pin-input w-full bg-black/40 p-5 rounded-2xl border border-white/10 outline-none mb-6">
            <button onclick="saveWalletPin()" class="w-full bg-blue-600 py-4 rounded-2xl font-black text-sm shadow-lg shadow-blue-600/20">پن محفوظ کریں</button>
        </div>
    </div>

    <main id="app-ui">
        <header class="p-6 flex justify-between items-center flex-row-reverse">
            <div class="flex items-center gap-4 flex-row-reverse">
                <div class="relative">
                    <img id="user-photo" class="w-14 h-14 rounded-2xl border-2 border-white/5 shadow-xl">
                    <span class="absolute -top-1 -right-1 bg-green-500 w-4 h-4 rounded-full border-4 border-[#010409]"></span>
                </div>
                <div class="text-right">
                    <p id="user-name" class="text-sm font-black uppercase tracking-tighter">لوڈنگ...</p>
                    <p class="text-[8px] font-bold text-blue-500 flex items-center gap-1 justify-end">VERIFIED USER <i class="fa-solid fa-circle-check"></i></p>
                </div>
            </div>
            <div class="flex gap-2">
                <button onclick="showPage('spin')" class="w-12 h-12 glass flex items-center justify-center text-yellow-500 shadow-lg"><i class="fa-solid fa-dharmachakra"></i></button>
            </div>
        </header>

        <div id="p-home" class="page active-page">
            <div class="glass p-8 mb-8 text-center bg-gradient-to-br from-blue-600/20 to-transparent border-blue-500/20">
                <p class="text-[10px] font-black opacity-40 uppercase mb-2 tracking-[0.2em]">کرنٹ فنڈز</p>
                <h2 id="v-bal" class="text-6xl font-black italic mb-8 tracking-tighter">₨ 0</h2>
                <div class="flex gap-4">
                    <button onclick="showPage('finance')" class="flex-1 bg-white text-black py-4 rounded-2xl font-black text-xs">جمع کریں</button>
                    <button onclick="showPage('withdraw')" class="flex-1 glass py-4 rounded-2xl font-black text-xs border-white/10">نکلوائیں</button>
                </div>
            </div>

            <h4 class="text-[11px] font-black uppercase opacity-30 mb-5 tracking-widest text-right">پریمیم انویسٹمنٹ نوڈز</h4>
            <div id="plans-grid" class="space-y-6 pb-12"></div>
        </div>

        <div id="p-finance" class="page">
            <h2 class="text-2xl font-black italic mb-8 text-right">ڈیپازٹ <span class="text-blue-500">فنڈز</span></h2>
            <div class="glass p-7 space-y-5">
                <select id="dep-method" class="w-full bg-black/40 p-5 rounded-2xl text-sm border border-white/10 outline-none">
                    <option value="Easypaisa">ایزی پیسہ (Easypaisa)</option>
                    <option value="JazzCash">جیز کیش (JazzCash)</option>
                    <option value="USDT">Binance (USDT - TRC20)</option>
                </select>
                <div class="bg-blue-600/10 p-5 rounded-2xl border border-blue-500/20 text-center">
                    <p class="text-[10px] opacity-50 mb-1 uppercase">آفیشل اکاؤنٹ نمبر</p>
                    <p class="text-lg font-black tracking-widest text-blue-400">نمبر یہاں لکھیں</p>
                </div>
                <input id="d-amt" type="number" placeholder="رقم (PKR)" class="w-full bg-black/40 p-5 rounded-2xl text-sm border border-white/10 outline-none focus:border-blue-500">
                <input id="d-tid" type="text" placeholder="ٹرانزیکشن آئی ڈی (TID)" class="w-full bg-black/40 p-5 rounded-2xl text-sm border border-white/10 outline-none focus:border-blue-500">
                <button onclick="submitTx('Deposit')" class="w-full bg-blue-600 py-5 rounded-3xl font-black text-sm">ڈیپازٹ کنفرم کریں</button>
            </div>
        </div>

        <div id="p-withdraw" class="page">
            <h2 class="text-2xl font-black italic mb-8 text-right">رقم <span class="text-blue-500">نکلوائیں</span></h2>
            <div class="glass p-7 space-y-5">
                <input id="w-amt" type="number" placeholder="رقم" class="w-full bg-black/40 p-5 rounded-2xl text-sm border border-white/10 outline-none">
                <input id="w-num" type="text" placeholder="اکاؤنٹ نمبر" class="w-full bg-black/40 p-5 rounded-2xl text-sm border border-white/10 outline-none">
                <input id="w-pin" type="password" maxlength="4" placeholder="والٹ پن (4 ہندسے)" class="w-full bg-black/40 p-5 rounded-2xl text-sm border border-white/10 outline-none text-center pin-input">
                <button onclick="handleWithdraw()" class="w-full bg-white text-black py-5 rounded-3xl font-black text-sm">ودرا ریکویسٹ بھیجیں</button>
            </div>
        </div>

        <div id="p-spin" class="page text-center">
            <h2 class="text-3xl font-black italic mb-10">لکی <span class="text-blue-500">سپن</span></h2>
            <div class="flex justify-center mb-12 relative">
                <div class="absolute -top-6 text-red-500 text-3xl z-10 animate-bounce"><i class="fa-solid fa-caret-down"></i></div>
                <div id="wheel" class="spin-wheel"></div>
            </div>
            <button id="spin-btn" onclick="spinNow()" class="w-64 bg-blue-600 py-5 rounded-full font-black text-lg shadow-xl shadow-blue-600/20 active:scale-90 transition-all">ابھی گھمائیں</button>
            <p class="text-[10px] mt-6 opacity-30 uppercase font-black tracking-widest">روزانہ صرف ایک سپن دستیاب ہے</p>
        </div>

        <nav class="nav-bar">
            <button onclick="showPage('home')" class="nav-btn p-4" id="nav-home"><i class="fa-solid fa-grid-2"></i></button>
            <button onclick="showPage('finance')" class="nav-btn p-4" id="nav-finance"><i class="fa-solid fa-plus-circle"></i></button>
            <button onclick="showPage('withdraw')" class="nav-btn p-4" id="nav-withdraw"><i class="fa-solid fa-money-bill-transfer"></i></button>
            <button onclick="showPage('spin')" class="nav-btn p-4" id="nav-spin"><i class="fa-solid fa-dharmachakra"></i></button>
        </nav>
    </main>

    <script>
        // Firebase Configuration (Aapki purani config)
        const firebaseConfig = { apiKey: "AIzaSyC9ofJ1KxRXHnxilpU9gyI87D3BSOZ9v1g", authDomain: "vestify-991f2.firebaseapp.com", projectId: "vestify-991f2", storageBucket: "vestify-991f2.firebasestorage.app", messagingSenderId: "799007097733", appId: "1:799007097733:web:ed3b35b6c4e51dc2e7baec" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore(), auth = firebase.auth();
        let userObj = null;

        auth.onAuthStateChanged(user => {
            if(user) {
                document.getElementById('app-ui').classList.remove('hidden');
                document.getElementById('user-photo').src = user.photoURL;
                document.getElementById('user-name').innerText = user.displayName;
                checkUserPin(user.displayName);
                syncData(user.displayName);
                renderModernPlans();
            } else {
                auth.signInWithPopup(new firebase.auth.GoogleAuthProvider());
            }
        });

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
            alert("والٹ پن سیٹ کر دیا گیا ہے!");
        }

        function renderModernPlans() {
            const plans = [
                { id: 1, cost: 500, daily: 40, days: 30, img: "https://images.unsplash.com/photo-1639762681485-074b7f938ba0?q=80&w=400&auto=format&fit=crop" },
                { id: 2, cost: 2000, daily: 180, days: 30, img: "https://images.unsplash.com/photo-1621761126064-fdd86782a778?q=80&w=400&auto=format&fit=crop" },
                { id: 3, cost: 5000, daily: 500, days: 30, img: "https://images.unsplash.com/photo-1642104704074-907c0698cbd9?q=80&w=400&auto=format&fit=crop" }
            ];
            let h = "";
            plans.forEach(p => {
                h += `
                <div class="plan-card glass p-2">
                    <img src="${p.img}" class="w-full h-40 object-cover rounded-[20px] mb-4 opacity-80">
                    <div class="p-4">
                        <div class="flex justify-between items-center mb-4 flex-row-reverse">
                            <h3 class="text-lg font-black uppercase tracking-tighter">ورژن ${p.id}</h3>
                            <span class="bg-blue-600/20 text-blue-400 px-3 py-1 rounded-full text-[10px] font-black">فعال</span>
                        </div>
                        <div class="grid grid-cols-2 gap-4 text-right mb-6 flex-row-reverse">
                            <div><p class="text-[9px] opacity-40 uppercase">روزانہ منافع</p><p class="font-black text-green-500 italic text-sm">₨ ${p.daily}</p></div>
                            <div><p class="text-[9px] opacity-40 uppercase">ٹوٹل منافع</p><p class="font-black text-white italic text-sm">₨ ${p.daily * p.days}</p></div>
                            <div><p class="text-[9px] opacity-40 uppercase">میعاد</p><p class="font-black text-white italic text-sm">${p.days} دن</p></div>
                            <div><p class="text-[9px] opacity-40 uppercase">کاؤنٹ ڈاؤن</p><p class="font-black text-blue-400 italic text-sm">24:00:00</p></div>
                        </div>
                        <button onclick="alert('انویسٹمنٹ جمع کرائیں!')" class="w-full bg-white text-black py-4 rounded-2xl font-black text-xs">ابھی خریدیں ₨ ${p.cost}</button>
                    </div>
                </div>`;
            });
            document.getElementById('plans-grid').innerHTML = h;
        }

        async function handleWithdraw() {
            const amt = document.getElementById('w-amt').value;
            const num = document.getElementById('w-num').value;
            const pin = document.getElementById('w-pin').value;

            if(!amt || !num || !pin) return alert("تمام خانے پُر کریں!");
            if(pin !== userObj.walletPin) return alert("غلط والٹ پن!");
            if(amt > userObj.balance) return alert("بیلنس کم ہے!");

            await db.collection("txs").add({
                user: auth.currentUser.displayName,
                amount: parseInt(amt),
                number: num,
                status: "Pending",
                type: "Withdrawal",
                time: Date.now()
            });
            alert("آپ کی ودرا درخواست ایڈمن کو بھیج دی گئی ہے!");
        }

        async function spinNow() {
            const today = new Date().toDateString();
            if(userObj.lastSpin === today) return alert("آج کا سپن مکمل ہو چکا ہے!");
            
            const btn = document.getElementById('spin-btn');
            const wheel = document.getElementById('wheel');
            btn.disabled = true;
            
            const rand = Math.floor(Math.random() * 360) + 1800; // 5 full rotations
            wheel.style.transform = `rotate(${rand}deg)`;
            
            setTimeout(async () => {
                const bonus = 50; // Dynamic bonus logic
                await db.collection("users").doc(auth.currentUser.displayName).set({ 
                    balance: firebase.firestore.FieldValue.increment(bonus),
                    lastSpin: today
                }, { merge: true });
                alert(`مبارک ہو! آپ نے ₨ ${bonus} جیتے!`);
                btn.disabled = false;
            }, 4000);
        }

        function showPage(p) {
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.getElementById('p-'+p).classList.add('active-page');
            document.querySelectorAll('.nav-btn').forEach(btn => btn.classList.remove('nav-active'));
            document.getElementById('nav-'+p).classList.add('nav-active');
        }

        function syncData(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                userObj = doc.data() || { balance: 0 };
                document.getElementById('v-bal').innerText = "₨ " + (userObj.balance || 0).toLocaleString();
            });
        }

        async function submitTx(type) {
            const amt = document.getElementById('d-amt').value;
            const tid = document.getElementById('d-tid').value;
            const method = document.getElementById('dep-method').value;
            if(!amt || !tid) return alert("تمام خانے پُر کریں!");
            await db.collection("txs").add({
                user: auth.currentUser.displayName,
                amount: parseInt(amt),
                tid: tid,
                method: method,
                status: "Pending",
                type: "Deposit",
                time: Date.now()
            });
            alert("درخواست موصول ہوگئی، ایڈمن جلد تصدیق کرے گا!");
        }
    </script>
</body>
</html>
