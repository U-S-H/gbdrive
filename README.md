<html lang="ur" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>ویسٹی فائی پرو ٹرمینل</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Noto+Nastaliq+Urdu:wght@400;700&family=Plus+Jakarta+Sans:wght@400;700;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', 'Noto Nastaliq Urdu', sans-serif; background-color: #010409; color: white; margin: 0; padding: 0; overflow-x: hidden; text-align: right; }
        .glass { background: rgba(22, 27, 34, 0.85); backdrop-filter: blur(15px); border: 1px solid rgba(255,255,255,0.08); border-radius: 24px; }
        .page { display: none; padding: 20px; animation: fadeIn 0.4s ease-out; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        .nav-bar { position: fixed; bottom: 20px; left: 20px; right: 20px; height: 75px; display: flex; justify-content: space-around; align-items: center; z-index: 5000; border: 1px solid rgba(255,255,255,0.05); border-radius: 25px; background: rgba(22, 27, 34, 0.9); flex-direction: row-reverse; }
        .nav-btn { color: rgba(255,255,255,0.4); transition: all 0.3s; }
        .nav-active { color: #3b82f6; transform: translateY(-5px); }
        #login-screen { position: fixed; inset: 0; display: flex; flex-direction: column; align-items: center; justify-content: center; background: #010409; z-index: 10000; }
        ::-webkit-scrollbar { width: 0; }
    </style>
</head>
<body class="pb-28">

    <div id="login-screen">
        <div class="text-center">
            <h1 class="text-5xl font-black italic mb-2 text-white">VESTIFY</h1>
            <p class="text-blue-500 font-bold tracking-[0.4em] text-[10px] uppercase mb-12">پرو ٹرمینل</p>
            <button onclick="login()" class="w-72 h-20 bg-white text-black font-black rounded-[2rem] uppercase shadow-2xl transition-all active:scale-95">کنیکٹ ٹرمینل</button>
        </div>
    </div>

    <main id="app-ui" class="hidden">
        <header class="p-6 flex justify-between items-center flex-row-reverse">
            <div class="flex items-center gap-4 flex-row-reverse">
                <img id="user-photo" onclick="handleAdminTap()" class="w-12 h-12 rounded-2xl border-2 border-white/5 cursor-pointer shadow-lg">
                <div class="text-right">
                    <p id="user-name" class="text-xs font-black uppercase">لوڈنگ...</p>
                    <p class="text-[8px] font-bold text-green-500 uppercase tracking-widest">کوانٹم نوڈ: فعال</p>
                </div>
            </div>
            <button onclick="showPage('support')" class="w-11 h-11 glass flex items-center justify-center text-blue-500"><i class="fa-solid fa-headset"></i></button>
        </header>

        <div id="p-home" class="page active-page">
            <div class="glass p-8 mb-6 border-blue-500/20 bg-gradient-to-br from-blue-600/10 to-transparent text-center">
                <p class="text-[10px] font-black opacity-40 uppercase mb-2">کل رقم</p>
                <h2 id="v-bal" class="text-5xl font-black italic mb-6">₨ 0</h2>
                
                <div class="bg-black/20 p-4 rounded-xl mb-6 border border-white/5">
                    <p class="text-[9px] mb-2 opacity-50">منافع کیلکولیٹر (30 دن)</p>
                    <input type="number" id="calc-in" oninput="calcProfit()" placeholder="رقم لکھیں" class="bg-transparent text-center w-full outline-none text-blue-400 font-bold text-sm">
                    <p id="calc-out" class="text-[10px] mt-2 text-green-500 font-bold">متوقع منافع: ₨ 0</p>
                </div>
                <div class="flex gap-3">
                    <button onclick="dailyCheckIn()" class="flex-1 bg-blue-600 text-white py-4 rounded-2xl font-black text-[11px]">روزانہ بونس</button>
                </div>
            </div>

            <div class="glass p-4 mb-6 h-64 overflow-hidden border-white/5">
                <p class="text-[9px] font-black opacity-30 mb-3 uppercase">مارکیٹ لائیو</p>
                <iframe src="https://s.tradingview.com/widgetembed/?frameElementId=tradingview_7626a&symbol=BINANCE%3ABTCUSDT&interval=D&hidesidetoolbar=1&symboledit=1&saveimage=1&toolbarbg=f1f3f6&studies=%5B%5D&theme=dark&style=1&timezone=Etc%2FUTC" width="100%" height="100%" frameborder="0"></iframe>
            </div>

            <h4 class="text-[11px] font-black uppercase opacity-30 mb-5 tracking-[0.2em]">انویسٹمنٹ نوڈز</h4>
            <div id="plans-grid" class="space-y-4 pb-12"></div>
        </div>

        <div id="p-finance" class="page">
            <div class="glass p-7 mb-8 border-blue-500/10">
                <div class="flex border-b border-white/5 mb-6 flex-row-reverse">
                    <button onclick="toggleFinanceTab('dep')" id="tab-dep" class="flex-1 pb-4 text-[12px] font-black text-blue-500">ڈیپازٹ</button>
                    <button onclick="toggleFinanceTab('wit')" id="tab-wit" class="flex-1 pb-4 text-[12px] font-black opacity-40">ودرا</button>
                </div>

                <div id="dep-form" class="space-y-4">
                    <select id="dep-method" class="w-full bg-black/40 p-4 rounded-2xl text-xs border border-white/5 outline-none">
                        <option value="Easypaisa">Easypaisa (03379827882)</option>
                        <option value="USDT">USDT (TRC20)</option>
                    </select>
                    <input id="d-amt" type="number" placeholder="رقم (PKR)" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/5 outline-none">
                    <input id="d-tid" type="text" placeholder="TID / ٹرانزیکشن ہیش" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/5 outline-none">
                    <button onclick="submitTx('Deposit')" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[12px]">جمع کرائیں</button>
                </div>

                <div id="wit-form" class="hidden space-y-4">
                    <input id="w-amt" type="number" placeholder="نکلوانے کی رقم" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/5 outline-none">
                    <input id="w-num" type="text" placeholder="ایزی پیسہ / جیز کیش نمبر" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/5 outline-none">
                    <button onclick="submitTx('Withdraw')" class="w-full bg-white text-black py-5 rounded-2xl font-black text-[12px]">ودرا ریکویسٹ</button>
                </div>
            </div>
            <div id="tx-history" class="space-y-3 pb-12"></div>
        </div>

        <div id="p-team" class="page">
            <h2 class="text-3xl font-black italic mb-6 text-center">آپ کا <span class="text-blue-500">نیٹ ورک</span></h2>
            <div class="glass p-7 mb-8 text-center">
                <p class="text-[10px] font-black opacity-40 mb-4">ریفرل لنک</p>
                <input id="ref-link" readonly class="w-full bg-black/40 p-4 rounded-xl text-[10px] border border-white/5 mb-4 text-center text-blue-400 outline-none">
                <button onclick="copyRef()" class="w-full bg-white text-black py-4 rounded-xl font-black text-[11px]">لنک کاپی کریں</button>
            </div>
            <div id="p-kyc" class="glass p-6 text-center mt-4">
                <p class="text-[10px] opacity-50 mb-3">شناخت کی تصدیق (KYC)</p>
                <button onclick="alert('سسٹم اپ ڈیٹ ہو رہا ہے!')" class="text-blue-500 text-[11px] font-black">تصویر اپ لوڈ کریں</button>
            </div>
        </div>

        <div id="p-support" class="page">
            <h2 class="text-2xl font-black italic mb-8 text-center">ہیلپ <span class="text-blue-500">سینٹر</span></h2>
            <div class="glass p-6 space-y-4">
                <textarea id="sup-msg" placeholder="اپنا مسئلہ یہاں لکھیں..." class="w-full bg-black/40 p-4 rounded-2xl text-xs border border-white/5 h-32 outline-none"></textarea>
                <button onclick="sendSupport()" class="w-full bg-blue-600 py-4 rounded-2xl font-black text-[11px]">درخواست بھیجیں</button>
            </div>
            <div class="mt-8 flex justify-center gap-10">
                <a href="https://wa.me/03379827882" class="text-3xl text-green-500"><i class="fa-brands fa-whatsapp"></i></a>
                <a href="#" class="text-3xl text-blue-400"><i class="fa-brands fa-telegram"></i></a>
            </div>
        </div>

        <nav class="nav-bar">
            <button onclick="showPage('home')" class="nav-btn p-5" id="nav-home"><i class="fa-solid fa-house text-xl"></i></button>
            <button onclick="showPage('finance')" class="nav-btn p-5" id="nav-finance"><i class="fa-solid fa-wallet text-xl"></i></button>
            <button onclick="showPage('team')" class="nav-btn p-5" id="nav-team"><i class="fa-solid fa-users text-xl"></i></button>
            <button onclick="showPage('support')" class="nav-btn p-5" id="nav-support"><i class="fa-solid fa-headset text-xl"></i></button>
        </nav>
    </main>

    <script>
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
                document.getElementById('ref-link').value = "https://vestify.net/join?ref=" + btoa(user.displayName);
                syncData(user.displayName);
                renderNodes();
                showPage('home');
            } else {
                document.getElementById('login-screen').style.display = 'flex';
            }
        });

        function login() { auth.signInWithPopup(new firebase.auth.GoogleAuthProvider()); }

        function showPage(p) {
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.getElementById('p-'+p).classList.add('active-page');
            document.querySelectorAll('.nav-btn').forEach(btn => btn.classList.remove('nav-active'));
            if(document.getElementById('nav-'+p)) document.getElementById('nav-'+p).classList.add('nav-active');
        }

        function syncData(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                userObj = doc.data() || { balance: 0, lastCheckIn: 0 };
                document.getElementById('v-bal').innerText = "₨ " + (userObj.balance || 0).toLocaleString();
            });
            db.collection("txs").where("user", "==", name).orderBy("time", "desc").limit(10).onSnapshot(s => {
                let h = ""; s.forEach(d => {
                    const t = d.data();
                    const clr = t.status === 'Approved' ? 'text-green-500' : 'text-yellow-500';
                    h += `<div class="glass p-5 flex justify-between items-center flex-row-reverse"><p class="text-[10px] font-black">${t.type}</p><div class="text-left"><p class="text-xs font-black">₨ ${t.amount}</p><p class="text-[9px] font-black ${clr}">${t.status === 'Approved' ? 'کامیاب' : 'پینڈنگ'}</p></div></div>`;
                });
                document.getElementById('tx-history').innerHTML = h || `<p class="text-center opacity-20 text-[10px] py-4">ریکارڈ موجود نہیں</p>`;
            });
        }

        function calcProfit() {
            const val = document.getElementById('calc-in').value;
            document.getElementById('calc-out').innerText = "متوقع منافع: ₨ " + Math.floor(val * 1.4);
        }

        function sendSupport() {
            const msg = document.getElementById('sup-msg').value;
            if(!msg) return alert("پیغام لکھیں!");
            db.collection("tickets").add({ user: auth.currentUser.displayName, message: msg, time: Date.now() });
            alert("شکریہ! آپ کا پیغام ایڈمن کو موصول ہو گیا ہے۔");
            document.getElementById('sup-msg').value = "";
        }

        async function dailyCheckIn() {
            const today = new Date().toDateString();
            if(userObj.lastCheckIn === today) return alert("کل دوبارہ آئیں، پیارے!");
            await db.collection("users").doc(auth.currentUser.displayName).set({ balance: firebase.firestore.FieldValue.increment(50), lastCheckIn: today }, { merge: true });
            alert("₨ 50 بونس شامل کر دیا گیا!");
        }

        function renderNodes() {
            let h = ""; for(let i=1; i<=10; i++) {
                let cost = i * 200;
                h += `<div class="glass p-6 flex justify-between items-center flex-row-reverse"><div class="text-right"><h5 class="text-[11px] font-black">پلان v.${i}</h5><p class="text-[9px] font-black text-green-500">روزانہ: ₨ ${Math.floor(cost*0.08)}</p></div><button class="bg-white text-black px-5 py-2.5 rounded-xl text-[10px] font-black">خریدیں ₨ ${cost}</button></div>`;
            }
            document.getElementById('plans-grid').innerHTML = h;
        }

        function copyRef() { const el = document.getElementById('ref-link'); el.select(); document.execCommand('copy'); alert("لنک کاپی کر لیا گیا ہے!"); }
        function toggleFinanceTab(t) {
            document.getElementById('dep-form').classList.toggle('hidden', t === 'wit');
            document.getElementById('wit-form').classList.toggle('hidden', t === 'dep');
            document.getElementById('tab-dep').classList.toggle('opacity-40', t === 'wit');
            document.getElementById('tab-wit').classList.toggle('opacity-40', t === 'dep');
        }

        async function submitTx(type) {
            const amt = type === 'Deposit' ? document.getElementById('d-amt').value : document.getElementById('w-amt').value;
            const tid = type === 'Deposit' ? document.getElementById('d-tid').value : document.getElementById('w-num').value;
            if(!amt || !tid) return alert("تمام خانے پُر کریں!");
            await db.collection("txs").add({ user: auth.currentUser.displayName, amount: parseInt(amt), tid: tid, type: type === 'Deposit' ? 'ڈیپازٹ' : 'ودرا', status: "Pending", time: Date.now() });
            alert("درخواست بھیج دی گئی ہے!");
        }
    </script>
</body>
</html>
