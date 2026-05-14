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
        #wheel { width: 280px; height: 280px; border-radius: 50%; border: 8px solid #d4af37; transition: transform 5s cubic-bezier(0.15, 0, 0.15, 1); box-shadow: 0 0 30px rgba(212, 175, 55, 0.3); }
        .payout-alert { background: rgba(34, 197, 94, 0.1); color: #22c55e; padding: 10px 20px; border-radius: 50px; font-size: 10px; font-weight: bold; margin-bottom: 20px; display: inline-block; animation: pulse 2s infinite; }
        @keyframes pulse { 0% { opacity: 0.6; } 50% { opacity: 1; } 100% { opacity: 0.6; } }
        #login-screen { position: fixed; inset: 0; display: flex; flex-direction: column; align-items: center; justify-content: center; background: #010409; z-index: 10000; }
        .pin-input { letter-spacing: 1em; text-align: center; font-size: 24px; font-weight: 900; color: #3b82f6; }
    </style>
</head>
<body class="pb-32">

    <div id="login-screen">
        <h1 class="text-6xl font-black italic mb-2 tracking-tighter">VESTIFY</h1>
        <p class="text-blue-500 font-bold tracking-[0.5em] text-[10px] uppercase mb-16">گولڈن ایڈیشن</p>
        <button onclick="login()" class="w-72 h-20 bg-white text-black font-black rounded-full shadow-2xl active:scale-95 transition-all">گوگل سے لاگ ان کریں</button>
    </div>

    <div id="pin-modal" class="hidden fixed inset-0 z-[15000] bg-black/95 flex items-center justify-center p-6 text-center">
        <div class="glass p-8 w-full max-w-xs">
            <h3 class="text-xl font-black mb-2 italic">سیکیورٹی والٹ</h3>
            <p class="text-[10px] text-blue-400 mb-6 font-bold uppercase">4 ہندسوں کا ودرا پن سیٹ کریں</p>
            <input id="set-pin" type="password" maxlength="4" placeholder="••••" class="w-full bg-black/40 p-5 rounded-2xl border border-white/10 outline-none mb-6 pin-input">
            <button onclick="saveWalletPin()" class="w-full bg-blue-600 py-4 rounded-2xl font-black shadow-lg shadow-blue-600/20">پن محفوظ کریں</button>
        </div>
    </div>

    <main id="app-ui" class="hidden">
        <header class="p-6 flex justify-between items-center flex-row-reverse">
            <div class="flex items-center gap-4 flex-row-reverse">
                <img id="user-photo" onclick="handleAdminTap()" class="w-12 h-12 rounded-2xl border-2 border-white/5 cursor-pointer">
                <div class="text-right">
                    <p id="user-name" class="text-xs font-black uppercase tracking-tight">Syncing...</p>
                    <p class="text-[8px] font-bold text-green-500 flex items-center gap-1 justify-end uppercase">Verified Node <i class="fa-solid fa-circle-check"></i></p>
                </div>
            </div>
            <button onclick="showPage('support')" class="w-11 h-11 glass flex items-center justify-center text-blue-500"><i class="fa-solid fa-headset"></i></button>
        </header>

        <div id="p-home" class="page active-page text-center">
            <div class="payout-alert"><i class="fa-solid fa-bolt ml-2"></i> احمد نے ابھی ₨ 5,000 نکال لیے</div>
            
            <div class="glass p-8 mb-6 bg-gradient-to-br from-blue-600/15 to-transparent border-blue-500/20">
                <p class="text-[9px] font-black opacity-40 uppercase mb-2 tracking-widest">ٹوٹل اثاثے</p>
                <h2 id="v-bal" class="text-5xl font-black italic mb-8 tracking-tighter">₨ 0</h2>
                <div class="flex gap-4">
                    <button onclick="showPage('finance')" class="flex-1 bg-white text-black py-4 rounded-2xl font-black text-[10px] uppercase">جمع کریں</button>
                    <button onclick="showPage('withdraw')" class="flex-1 glass py-4 rounded-2xl font-black text-[10px] uppercase border-white/10">نکلوائیں</button>
                </div>
            </div>

            <div class="glass p-5 mb-6 flex justify-between items-center flex-row-reverse border-white/5">
                <div class="text-right">
                    <p class="text-[10px] font-black uppercase">کلاؤڈ مائننگ ایکٹو ہے</p>
                    <p id="timer" class="text-[9px] text-blue-500 font-bold">اگلا پرافٹ: 23:59:59</p>
                </div>
                <div class="w-8 h-8 rounded-full border-2 border-blue-500 border-t-transparent animate-spin"></div>
            </div>

            <h4 class="text-[10px] font-black opacity-30 mb-5 text-right tracking-widest uppercase">انویسٹمنٹ نوڈز</h4>
            <div id="plans-grid" class="space-y-6 pb-12"></div>
        </div>

        <div id="p-finance" class="page">
            <h2 class="text-2xl font-black italic mb-8 text-right">فنڈز <span class="text-blue-500">ڈیپازٹ</span></h2>
            <div class="glass p-7 space-y-5">
                <select id="dep-method" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/10 outline-none">
                    <option value="Easypaisa">Easypaisa (ایزی پیسہ)</option>
                    <option value="JazzCash">JazzCash (جیز کیش)</option>
                    <option value="USDT">Binance USDT (TRC20)</option>
                </select>
                <div class="bg-blue-600/10 p-5 rounded-2xl border border-blue-500/10 text-center">
                    <p class="text-[9px] opacity-40 mb-1 uppercase tracking-widest">آفیشل اکاؤنٹ نمبر</p>
                    <p id="acc-num" class="text-lg font-black text-blue-400 tracking-tighter">03XXXXXXXXX</p>
                </div>
                <input id="d-amt" type="number" placeholder="رقم درج کریں (PKR)" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/10 outline-none">
                <input id="d-tid" type="text" placeholder="ٹرانزیکشن آئی ڈی (TID)" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/10 outline-none">
                <button onclick="submitTx('Deposit')" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-sm shadow-xl shadow-blue-600/20">ڈیپازٹ کنفرم کریں</button>
            </div>
        </div>

        <div id="p-withdraw" class="page">
            <h2 class="text-2xl font-black italic mb-8 text-right">رقم <span class="text-blue-500">نکلوائیں</span></h2>
            <div class="glass p-7 space-y-5 text-center">
                <input id="w-amt" type="number" placeholder="نکلوائی جانے والی رقم" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/10 outline-none">
                <input id="w-num" type="text" placeholder="ایزی پیسہ / جیز کیش نمبر" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/10 outline-none">
                <input id="w-pin" type="password" maxlength="4" placeholder="والٹ پن" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/10 outline-none pin-input">
                <button onclick="handleWithdraw()" class="w-full bg-white text-black py-5 rounded-2xl font-black text-sm shadow-xl active:scale-95 transition-all">ودرا درخواست بھیجیں</button>
            </div>
        </div>

        <div id="p-spin" class="page text-center">
            <h2 class="text-3xl font-black italic mb-10">گولڈن <span class="text-yellow-500">سپن</span></h2>
            <div class="flex justify-center mb-12 relative">
                <div class="absolute -top-4 text-red-500 text-3xl z-10 animate-bounce"><i class="fa-solid fa-caret-down"></i></div>
                <img id="wheel" src="IMG_20260515_012434.jpg" onerror="this.src='https://via.placeholder.com/300?text=Golden+Wheel'">
            </div>
            <button id="spin-btn" onclick="spinNow()" class="w-64 bg-blue-600 py-5 rounded-full font-black text-lg shadow-xl shadow-blue-600/20 active:scale-90 transition-all">قسمت آزمائیں</button>
            <p class="text-[10px] mt-6 opacity-30 uppercase font-black tracking-widest">روزانہ صرف ایک سپن دستیاب ہے</p>
        </div>

        <div id="p-team" class="page text-center">
            <h2 class="text-2xl font-black italic mb-8">نیٹ ورک <span class="text-blue-500">پروگرام</span></h2>
            <div class="glass p-8 mb-6">
                <p class="text-[10px] opacity-40 mb-4 uppercase tracking-widest">آپ کا ریفرل لنک</p>
                <input id="ref-link" readonly class="w-full bg-black/40 p-4 rounded-xl text-[10px] border border-white/10 mb-4 text-center text-blue-400 outline-none">
                <button onclick="copyRef()" class="w-full bg-white text-black py-4 rounded-xl font-black text-[11px] uppercase">لنک کاپی کریں</button>
            </div>
        </div>

        <nav class="nav-bar">
            <button onclick="showPage('home')" class="nav-btn p-4" id="nav-home"><i class="fa-solid fa-house-chimney text-xl"></i></button>
            <button onclick="showPage('finance')" class="nav-btn p-4" id="nav-finance"><i class="fa-solid fa-circle-plus text-xl"></i></button>
            <button onclick="showPage('spin')" class="nav-btn p-4" id="nav-spin"><i class="fa-solid fa-dharmachakra text-xl"></i></button>
            <button onclick="showPage('team')" class="nav-btn p-4" id="nav-team"><i class="fa-solid fa-users-viewfinder text-xl"></i></button>
        </nav>
    </main>

    <script>
        // Firebase Configuration (Same as before)
        const firebaseConfig = { apiKey: "AIzaSyC9ofJ1KxRXHnxilpU9gyI87D3BSOZ9v1g", authDomain: "vestify-991f2.firebaseapp.com", projectId: "vestify-991f2", storageBucket: "vestify-991f2.firebasestorage.app", messagingSenderId: "799007097733", appId: "1:799007097733:web:ed3b35b6c4e51dc2e7baec" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore(), auth = firebase.auth();
        let userObj = null, tapCount = 0, currentRot = 0;

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
                document.getElementById('ref-link').value = "https://vestify.net/join?ref=" + btoa(user.displayName);
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
            alert("والٹ پن محفوظ کر لیا گیا ہے!");
        }

        function renderPlans() {
            const plans = [
                { id: 1, cost: 500, daily: 50, days: 30, img: "https://images.unsplash.com/photo-1621761126064-fdd86782a778?auto=format&fit=crop&w=400" },
                { id: 2, cost: 2000, daily: 240, days: 30, img: "https://images.unsplash.com/photo-1639762681485-074b7f938ba0?auto=format&fit=crop&w=400" },
                { id: 3, cost: 5000, daily: 650, days: 30, img: "https://images.unsplash.com/photo-1642104704074-907c0698cbd9?auto=format&fit=crop&w=400" }
            ];
            let h = "";
            plans.forEach(p => {
                h += `
                <div class="glass p-2 overflow-hidden text-right">
                    <img src="${p.img}" class="w-full h-40 object-cover rounded-[22px] mb-4 opacity-70">
                    <div class="p-4">
                        <div class="flex justify-between items-center mb-5 flex-row-reverse">
                            <h3 class="text-sm font-black italic uppercase">نوڈ ورژن ${p.id}</h3>
                            <span class="bg-blue-600/20 text-blue-400 px-3 py-1 rounded-full text-[9px] font-black tracking-widest">ACTIVE</span>
                        </div>
                        <div class="grid grid-cols-2 gap-3 mb-6">
                            <div><p class="text-[8px] opacity-40 uppercase">روزانہ منافع</p><p class="text-green-500 font-black text-xs italic">₨ ${p.daily}</p></div>
                            <div><p class="text-[8px] opacity-40 uppercase">ٹوٹل ریٹرن</p><p class="text-white font-black text-xs italic">₨ ${p.daily*p.days}</p></div>
                            <div><p class="text-[8px] opacity-40 uppercase">میعاد</p><p class="text-white font-black text-[10px] italic">${p.days} دن</p></div>
                            <div><p class="text-[8px] opacity-40 uppercase">کاؤنٹ ڈاؤن</p><p class="text-blue-400 font-black text-[10px] italic">23:59:59</p></div>
                        </div>
                        <button onclick="alert('پہلے ڈیپازٹ کریں!')" class="w-full bg-white text-black py-4 rounded-xl font-black text-[10px] uppercase shadow-lg">ابھی ایکٹو کریں ₨ ${p.cost}</button>
                    </div>
                </div>`;
            });
            document.getElementById('plans-grid').innerHTML = h;
        }

        function spinNow() {
            const today = new Date().toDateString();
            if(userObj.lastSpin === today) return alert("آج کا لکی سپن مکمل ہو چکا ہے!");
            
            const btn = document.getElementById('spin-btn');
            const wheel = document.getElementById('wheel');
            btn.disabled = true;
            
            currentRot += 1800 + Math.floor(Math.random() * 360); 
            wheel.style.transform = `rotate(${currentRot}deg)`;
            
            setTimeout(async () => {
                const bonus = 100; // Jackpot bonus
                await db.collection("users").doc(auth.currentUser.displayName).set({ 
                    balance: firebase.firestore.FieldValue.increment(bonus),
                    lastSpin: today
                }, { merge: true });
                alert(`مبارک ہو! آپ نے ₨ ${bonus} کا جیک پاٹ جیت لیا!`);
                btn.disabled = false;
            }, 5000);
        }

        async function handleWithdraw() {
            const amt = document.getElementById('w-amt').value;
            const num = document.getElementById('w-num').value;
            const pin = document.getElementById('w-pin').value;

            if(!amt || !num || !pin) return alert("تمام تفصیلات درج کریں!");
            if(pin !== userObj.walletPin) return alert("غلط والٹ پن!");
            if(amt > userObj.balance) return alert("بیلنس ناکافی ہے!");

            await db.collection("txs").add({
                user: auth.currentUser.displayName,
                amount: parseInt(amt),
                number: num,
                status: "Pending",
                type: "Withdrawal",
                time: Date.now()
            });
            alert("آپ کی ودرا درخواست ایڈمن کو موصول ہو گئی ہے!");
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
                document.getElementById('timer').innerText = `اگلا پرافٹ: ${h < 10 ? '0'+h : h}:${m < 10 ? '0'+m : m}:${s < 10 ? '0'+s : s}`;
            }, 1000);
        }

        function handleAdminTap() {
            tapCount++;
            if(tapCount >= 10) {
                let key = prompt("ایڈمن کی درج کریں:");
                if(key === "net204") alert("ایڈمن موڈ آن!");
            }
        }

        async function submitTx(type) {
            const a = document.getElementById('d-amt').value, t = document.getElementById('d-tid').value, m = document.getElementById('dep-method').value;
            if(!a || !t) return alert("تمام تفصیلات درج کریں!");
            await db.collection("txs").add({ user: auth.currentUser.displayName, amount: parseInt(a), tid: t, method: m, status: "Pending", type: "Deposit", time: Date.now() });
            alert("ڈیپازٹ ڈیٹا بھیج دیا گیا ہے!");
        }

        function copyRef() { const el = document.getElementById('ref-link'); el.select(); document.execCommand('copy'); alert("لنک کاپی ہو گیا!"); }
    </script>
</body>
</html>
