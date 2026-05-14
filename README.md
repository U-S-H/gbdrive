<html lang="ur" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>ویسٹی فائی الٹیمیٹ ماسٹر</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Noto+Nastaliq+Urdu:wght@400;700&family=Plus+Jakarta+Sans:wght@400;700;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', 'Noto Nastaliq Urdu', sans-serif; background-color: #010409; color: white; margin: 0; padding: 0; overflow-x: hidden; text-align: right; }
        .glass { background: rgba(22, 27, 34, 0.75); backdrop-filter: blur(20px); border: 1px solid rgba(255,255,255,0.08); border-radius: 28px; }
        .page { display: none; padding: 20px; animation: slideUp 0.4s ease-out; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(30px); } to { opacity: 1; transform: translateY(0); } }
        
        /* Fake Notifications Style */
        #notification-box { position: fixed; top: 20px; left: 20px; right: 20px; z-index: 20000; pointer-events: none; }
        .pop-notify { background: rgba(255, 255, 255, 0.95); color: #000; padding: 12px 20px; border-radius: 20px; margin-bottom: 10px; box-shadow: 0 10px 30px rgba(0,0,0,0.3); display: flex; align-items: center; gap: 10px; animation: popInOut 4s forwards; font-size: 11px; font-weight: 800; border-right: 5px solid #3b82f6; }
        @keyframes popInOut { 0% { opacity: 0; transform: translateY(-50px); } 10% { opacity: 1; transform: translateY(0); } 90% { opacity: 1; transform: translateY(0); } 100% { opacity: 0; transform: translateY(-50px); } }

        .nav-bar { position: fixed; bottom: 15px; left: 15px; right: 15px; height: 75px; display: flex; justify-content: space-around; align-items: center; z-index: 5000; border-radius: 25px; background: rgba(22, 27, 34, 0.95); border: 1px solid rgba(255,255,255,0.05); flex-direction: row-reverse; }
        .nav-btn { color: rgba(255,255,255,0.3); transition: 0.3s; }
        .nav-active { color: #3b82f6; transform: translateY(-5px); text-shadow: 0 0 15px #3b82f6; }
        #wheel { width: 280px; transition: transform 5s cubic-bezier(0.15, 0, 0.15, 1); border-radius: 50%; box-shadow: 0 0 40px rgba(59, 130, 246, 0.2); }
        .tab-btn { padding: 12px 24px; border-radius: 18px; font-size: 12px; font-weight: 900; transition: 0.4s; }
        .tab-active { background: #3b82f6; color: white; box-shadow: 0 0 15px rgba(59, 130, 246, 0.4); }
    </style>
</head>
<body class="pb-32">

    <div id="notification-box"></div>

    <div id="pin-modal" class="hidden fixed inset-0 z-[15000] bg-black/98 flex items-center justify-center p-6 text-center">
        <div class="glass p-8 w-full max-w-xs border-blue-500/30">
            <h3 class="text-xl font-black mb-2 italic">سیکیورٹی والٹ</h3>
            <p class="text-[10px] text-blue-400 mb-6 font-bold uppercase">4 ہندسوں کا ودرا پن سیٹ کریں</p>
            <input id="set-pin" type="password" maxlength="4" placeholder="••••" class="w-full bg-black/40 p-5 rounded-2xl border border-white/10 outline-none mb-6 text-center text-2xl tracking-[1em] text-blue-500 font-black">
            <button onclick="saveWalletPin()" class="w-full bg-blue-600 py-4 rounded-2xl font-black">محفوظ کریں</button>
        </div>
    </div>

    <main id="app-ui">
        <header class="p-6 flex justify-between items-center flex-row-reverse">
            <div class="flex items-center gap-4 flex-row-reverse">
                <img id="user-photo" onclick="handleAdminTap()" class="w-12 h-12 rounded-2xl border-2 border-white/5">
                <div class="text-right">
                    <p id="user-name" class="text-xs font-black uppercase tracking-tight">Syncing...</p>
                    <p class="text-[8px] font-bold text-green-500 uppercase">Pro Trader <i class="fa-solid fa-crown"></i></p>
                </div>
            </div>
            <h1 class="text-2xl font-black italic tracking-tighter">VESTIFY</h1>
        </header>

        <div id="p-home" class="page active-page">
            <div class="glass p-8 mb-8 bg-gradient-to-br from-blue-600/20 to-transparent border-blue-500/20 text-center">
                <p class="text-[9px] font-black opacity-40 uppercase mb-2 tracking-widest">ٹوٹل اثاثے</p>
                <h2 id="v-bal" class="text-5xl font-black italic mb-8 tracking-tighter">₨ 0</h2>
                <div class="flex gap-4">
                    <button onclick="showPage('finance')" class="flex-1 bg-white text-black py-4 rounded-2xl font-black text-[10px] uppercase">جمع کریں</button>
                    <button onclick="showPage('withdraw')" class="flex-1 glass py-4 rounded-2xl font-black text-[10px] uppercase border-white/10">ودرا</button>
                </div>
            </div>

            <div class="flex gap-3 mb-8 justify-center">
                <button id="btn-normal" onclick="renderPlans('normal')" class="tab-btn tab-active">سٹینڈرڈ نوڈز</button>
                <button id="btn-special" onclick="renderPlans('special')" class="tab-btn bg-white/5 text-blue-400">سپیشل آفرز</button>
            </div>

            <div id="plans-grid" class="space-y-6 pb-12"></div>
        </div>

        <div id="p-withdraw" class="page">
            <h2 class="text-2xl font-black italic mb-8 text-right">فنڈز <span class="text-blue-500">نکلوائیں</span></h2>
            <div class="glass p-7 space-y-5">
                <select id="w-method" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/10 outline-none">
                    <option value="Easypaisa">Easypaisa</option>
                    <option value="JazzCash">JazzCash</option>
                    <option value="USDT">Binance (USDT)</option>
                </select>
                <input id="w-num" type="text" placeholder="اکاؤنٹ نمبر / ایڈریس" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/10 outline-none">
                <input id="w-amt" type="number" placeholder="رقم درج کریں" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/10 outline-none">
                <input id="w-pin" type="password" maxlength="4" placeholder="والٹ پن درج کریں" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/10 outline-none text-center font-black tracking-[1em]">
                <button onclick="handleWithdraw()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-sm shadow-xl shadow-blue-600/20 uppercase">درخواست بھیجیں</button>
            </div>
        </div>

        <div id="p-finance" class="page">
            <h2 class="text-2xl font-black italic mb-8 text-right">رقم <span class="text-blue-500">جمع کریں</span></h2>
            <div class="glass p-7 space-y-5">
                <select id="dep-method" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/10 outline-none">
                    <option value="Easypaisa">Easypaisa</option>
                    <option value="JazzCash">JazzCash</option>
                    <option value="USDT">USDT (TRC20)</option>
                </select>
                <div class="bg-blue-600/10 p-5 rounded-2xl border border-blue-500/10 text-center">
                    <p class="text-[9px] opacity-40 mb-1 uppercase tracking-widest">آفیشل اکاؤنٹ</p>
                    <p id="acc-num" class="text-lg font-black text-blue-400">03XXXXXXXXX</p>
                </div>
                <input id="d-amt" type="number" placeholder="رقم لکھیں" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/10 outline-none">
                <input id="d-tid" type="text" placeholder="Transaction ID (TID)" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/10 outline-none">
                <button onclick="submitTx()" class="w-full bg-white text-black py-5 rounded-2xl font-black text-sm uppercase">جمع کر دیا</button>
            </div>
        </div>

        <div id="p-spin" class="page text-center">
            <h2 class="text-3xl font-black italic mb-12 uppercase">Lucky <span class="text-yellow-500">Extraction</span></h2>
            <div class="flex justify-center mb-12 relative">
                <div class="absolute -top-4 text-red-500 text-4xl z-10 animate-bounce"><i class="fa-solid fa-location-pin"></i></div>
                <img id="wheel" src="IMG_20260515_012434.jpg" class="w-72 h-72 rounded-full border-4 border-white/5" onerror="this.src='https://via.placeholder.com/300?text=Golden+Wheel'">
            </div>
            <button id="spin-btn" onclick="spinNow()" class="w-full max-w-xs bg-blue-600 py-5 rounded-full font-black text-lg uppercase shadow-xl shadow-blue-600/30">گھمائیں</button>
        </div>

        <nav class="nav-bar">
            <button onclick="showPage('home')" class="nav-btn p-4" id="nav-home"><i class="fa-solid fa-microchip text-xl"></i></button>
            <button onclick="showPage('finance')" class="nav-btn p-4" id="nav-finance"><i class="fa-solid fa-plus-circle text-xl"></i></button>
            <button onclick="showPage('spin')" class="nav-btn p-4" id="nav-spin"><i class="fa-solid fa-dharmachakra text-xl"></i></button>
            <button onclick="showPage('withdraw')" class="nav-btn p-4" id="nav-withdraw"><i class="fa-solid fa-hand-holding-dollar text-xl"></i></button>
        </nav>
    </main>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyC9ofJ1KxRXHnxilpU9gyI87D3BSOZ9v1g", authDomain: "vestify-991f2.firebaseapp.com", projectId: "vestify-991f2", storageBucket: "vestify-991f2.firebasestorage.app", messagingSenderId: "799007097733", appId: "1:799007097733:web:ed3b35b6c4e51dc2e7baec" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore(), auth = firebase.auth();
        let userObj = null, currentRot = 0;

        auth.onAuthStateChanged(user => {
            if(user) {
                document.getElementById('user-photo').src = user.photoURL;
                document.getElementById('user-name').innerText = user.displayName;
                syncData(user.displayName);
                checkUserPin(user.displayName);
                renderPlans('normal');
                startFakePopups();
            } else {
                auth.signInWithPopup(new firebase.auth.GoogleAuthProvider());
            }
        });

        function renderPlans(type) {
            document.getElementById('btn-normal').classList.toggle('tab-active', type === 'normal');
            document.getElementById('btn-special').classList.toggle('tab-active', type === 'special');
            let h = "";
            let count = type === 'normal' ? 15 : 6;
            for(let i=1; i<=count; i++) {
                let cost = type === 'normal' ? 200 + (i*500) : 5000 + (i*2000);
                let daily = Math.floor(cost * (type === 'normal' ? 0.12 : 0.25));
                let days = type === 'normal' ? 30 : 15;
                h += `
                <div class="glass p-2 overflow-hidden text-right">
                    <img src="https://images.unsplash.com/photo-1621761126064-fdd86782a778?q=80&w=400&auto=format&fit=crop" class="w-full h-40 object-cover rounded-[22px] mb-4 opacity-70">
                    <div class="p-4">
                        <div class="flex justify-between items-center mb-5 flex-row-reverse">
                            <h3 class="text-sm font-black italic uppercase">${type} Node v.${i}</h3>
                            <span class="bg-green-500/20 text-green-400 px-3 py-1 rounded-full text-[8px] font-black uppercase">Active</span>
                        </div>
                        <div class="grid grid-cols-2 gap-4 mb-6">
                            <div><p class="text-[8px] opacity-40 uppercase mb-1">روزانہ پرافٹ</p><p class="text-green-500 font-black text-xs">₨ ${daily}</p></div>
                            <div><p class="text-[8px] opacity-40 uppercase mb-1">ٹوٹل ریٹرن</p><p class="text-white font-black text-xs">₨ ${daily*days}</p></div>
                            <div><p class="text-[8px] opacity-40 uppercase mb-1">میعاد</p><p class="text-white font-black text-[10px]">${days} دن</p></div>
                            <div><p class="text-[8px] opacity-40 uppercase mb-1">نیکسٹ کلیم</p><p class="text-blue-400 font-black text-[10px]">23:59:59</p></div>
                        </div>
                        <button onclick="alert('Insufficient Balance!')" class="w-full bg-white text-black py-4 rounded-xl font-black text-[11px] uppercase shadow-lg">ایکٹو کریں ₨ ${cost}</button>
                    </div>
                </div>`;
            }
            document.getElementById('plans-grid').innerHTML = h;
        }

        function startFakePopups() {
            const names = ["احمد", "علی", "عمران", "حمزہ", "ذیشان", "کاشف", "بلال", "یاسر", "ارسلان", "طلحہ"];
            const amounts = ["2,000", "5,000", "10,000", "500", "1,200", "3,500", "15,000"];
            setInterval(() => {
                const name = names[Math.floor(Math.random() * names.length)];
                const amt = amounts[Math.floor(Math.random() * amounts.length)];
                const type = Math.random() > 0.5 ? "نے ₨ " + amt + " کا ودرا حاصل کیا" : "نے ₨ " + amt + " ڈیپازٹ کیے";
                const icon = type.includes("ودرا") ? "fa-check-circle text-green-500" : "fa-arrow-up text-blue-500";
                
                const div = document.createElement('div');
                div.className = "pop-notify";
                div.innerHTML = `<i class="fa-solid ${icon}"></i> <span>${name} ${type}</span>`;
                document.getElementById('notification-box').appendChild(div);
                setTimeout(() => div.remove(), 4000);
            }, 5000);
        }

        async function handleWithdraw() {
            const amt = document.getElementById('w-amt').value, pin = document.getElementById('w-pin').value;
            if(pin !== userObj.walletPin) return alert("Wrong PIN!");
            if(amt > userObj.balance) return alert("Low Balance!");
            await db.collection("txs").add({ user: auth.currentUser.displayName, amount: parseInt(amt), type: "Withdrawal", status: "Pending", time: Date.now() });
            alert("درخواست بھیج دی گئی!");
        }

        function showPage(p) {
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.getElementById('p-'+p).classList.add('active-page');
            document.querySelectorAll('.nav-btn').forEach(btn => btn.classList.remove('nav-active'));
            document.getElementById('nav-'+p).classList.add('nav-active');
        }

        async function checkUserPin(name) {
            const doc = await db.collection("users").doc(name).get();
            if(!doc.exists || !doc.data().walletPin) document.getElementById('pin-modal').classList.remove('hidden');
        }

        async function saveWalletPin() {
            const pin = document.getElementById('set-pin').value;
            if(pin.length !== 4) return alert("4 Digits required!");
            await db.collection("users").doc(auth.currentUser.displayName).set({ walletPin: pin }, { merge: true });
            document.getElementById('pin-modal').classList.add('hidden');
        }

        function syncData(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                userObj = doc.data() || { balance: 0 };
                document.getElementById('v-bal').innerText = "₨ " + (userObj.balance || 0).toLocaleString();
            });
        }

        function spinNow() {
            const btn = document.getElementById('spin-btn');
            const wheel = document.getElementById('wheel');
            btn.disabled = true;
            currentRot += 1800 + Math.floor(Math.random() * 360);
            wheel.style.transform = `rotate(${currentRot}deg)`;
            setTimeout(() => { alert("Extra Bonus Added to Wallet!"); btn.disabled = false; }, 5500);
        }
    </script>
</body>
</html>
