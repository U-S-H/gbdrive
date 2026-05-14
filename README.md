<html lang="ur" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>ویسٹی فائی ایلیٹ ٹرمینل</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Noto+Nastaliq+Urdu:wght@400;700&family=Plus+Jakarta+Sans:wght@400;700;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', 'Noto Nastaliq Urdu', sans-serif; background-color: #010409; color: white; margin: 0; padding: 0; overflow-x: hidden; text-align: right; }
        .glass { background: rgba(22, 27, 34, 0.85); backdrop-filter: blur(15px); border: 1px solid rgba(255,255,255,0.08); border-radius: 24px; }
        .page { display: none; padding: 20px; animation: fadeIn 0.4s ease-out; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        #login-screen { position: fixed; inset: 0; display: flex; flex-direction: column; align-items: center; justify-content: center; background: #010409; z-index: 10000; }
        .nav-bar { position: fixed; bottom: 20px; left: 20px; right: 20px; height: 75px; display: flex; justify-content: space-around; align-items: center; z-index: 5000; border: 1px solid rgba(255,255,255,0.05); border-radius: 25px; background: rgba(22, 27, 34, 0.9); flex-direction: row-reverse; }
        .nav-btn { color: rgba(255,255,255,0.4); transition: all 0.3s; }
        .nav-active { color: #3b82f6; transform: translateY(-5px); }
        .admin-badge { background: linear-gradient(90deg, #3b82f6, #8b5cf6); padding: 3px 10px; border-radius: 6px; font-size: 8px; font-weight: 900; text-transform: uppercase; }
        input { text-align: right; }
    </style>
</head>
<body class="pb-28">

    <div id="login-screen">
        <div class="text-center">
            <h1 class="text-5xl font-black italic mb-2 tracking-tighter text-white">VESTIFY</h1>
            <p class="text-blue-500 font-bold tracking-[0.4em] text-[10px] uppercase mb-12">اعلیٰ ٹرمینل</p>
            <button onclick="login()" class="w-72 h-20 bg-white text-black font-black rounded-[2rem] uppercase tracking-widest shadow-2xl transition-all active:scale-95">ٹرمینل سے منسلک ہوں</button>
        </div>
    </div>

    <div id="admin-ui" class="hidden fixed inset-0 z-[11000] bg-[#010409] overflow-y-auto p-6">
        <div class="flex justify-between items-center mb-10">
            <h2 class="text-2xl font-black italic text-blue-500">ایڈمن <span class="text-white">کمانڈ</span></h2>
            <button onclick="closeAdmin()" class="w-12 h-12 glass flex items-center justify-center text-white text-2xl">&times;</button>
        </div>
        <div class="space-y-6" id="adm-tx-list"></div>
    </div>

    <main id="app-ui" class="hidden">
        <header class="p-6 flex justify-between items-center flex-row-reverse">
            <div class="flex items-center gap-4 flex-row-reverse">
                <div class="relative">
                    <img id="user-photo" onclick="handleAdminTap()" class="w-12 h-12 rounded-2xl border-2 border-white/5 cursor-pointer shadow-lg">
                    <div class="absolute -bottom-1 -left-1 w-4 h-4 bg-green-500 border-4 border-[#010409] rounded-full"></div>
                </div>
                <div class="text-right">
                    <div class="flex items-center gap-2 flex-row-reverse">
                        <p id="user-name" class="text-xs font-black uppercase">لوڈنگ...</p>
                        <span id="owner-tag" class="admin-badge hidden">مالک</span>
                    </div>
                    <p class="text-[8px] font-bold text-blue-500 uppercase tracking-widest">کوانٹم نوڈ: فعال</p>
                </div>
            </div>
            <button onclick="showPage('menu')" class="w-12 h-12 glass flex items-center justify-center text-blue-500"><i class="fa-solid fa-bars-staggered"></i></button>
        </header>

        <div id="p-home" class="page active-page">
            <div class="glass p-8 mb-6 border-blue-500/20 bg-gradient-to-br from-blue-600/10 to-transparent text-center">
                <p class="text-[10px] font-black opacity-40 uppercase mb-2">کل رقم</p>
                <h2 id="v-bal" class="text-5xl font-black italic mb-8 tracking-tighter">₨ 0</h2>
                <div class="flex gap-3">
                    <button onclick="dailyCheckIn()" class="flex-1 bg-blue-600 text-white py-4 rounded-2xl font-black text-[11px]">روزانہ بونس</button>
                    <button onclick="showPage('finance')" class="flex-1 glass py-4 rounded-2xl font-black text-[11px]">فنانس</button>
                </div>
            </div>
            <h4 class="text-[11px] font-black uppercase opacity-30 mb-5 tracking-[0.2em]">انویسٹمنٹ پلانز</h4>
            <div id="plans-grid" class="space-y-4 pb-12"></div>
        </div>

        <div id="p-finance" class="page">
            <div class="glass p-7 mb-8 border-blue-500/10">
                <div class="flex border-b border-white/5 mb-6 flex-row-reverse">
                    <button onclick="toggleFinanceTab('dep')" id="tab-dep" class="flex-1 pb-4 text-[12px] font-black text-blue-500">ڈیپازٹ</button>
                    <button onclick="toggleFinanceTab('wit')" id="tab-wit" class="flex-1 pb-4 text-[12px] font-black opacity-40">ودرا (نکلوائیں)</button>
                </div>

                <div id="dep-form" class="space-y-4">
                    <div class="bg-black/40 p-4 rounded-2xl text-center border border-white/5">
                        <p class="text-[10px] opacity-40 mb-1">ایزی پیسہ اکاؤنٹ</p>
                        <p class="text-sm font-black tracking-widest text-white">03379827882</p>
                    </div>
                    <input id="d-amt" type="number" placeholder="رقم (PKR)" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/5 outline-none focus:border-blue-500">
                    <input id="d-tid" type="text" placeholder="ٹرانزیکشن آئی ڈی (TID)" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/5 outline-none focus:border-blue-500">
                    <button onclick="submitTx('Deposit')" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[12px]">ڈیپازٹ جمع کریں</button>
                </div>

                <div id="wit-form" class="hidden space-y-4">
                    <input id="w-amt" type="number" placeholder="نکلوانے کی رقم" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/5 outline-none focus:border-blue-500">
                    <input id="w-num" type="text" placeholder="ایزی پیسہ / جیز کیش نمبر" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/5 outline-none focus:border-blue-500">
                    <button onclick="submitTx('Withdraw')" class="w-full bg-white text-black py-5 rounded-2xl font-black text-[12px]">رقم نکلوانے کی درخواست</button>
                </div>
            </div>
            <h4 class="text-[11px] font-black uppercase opacity-30 mb-5">ٹرانزیکشن ہسٹری</h4>
            <div id="tx-history" class="space-y-3 pb-12"></div>
        </div>

        <div id="p-team" class="page">
            <h2 class="text-3xl font-black italic mb-6 text-center">آپ کا <span class="text-blue-500">نیٹ ورک</span></h2>
            <div class="glass p-7 mb-8 text-center">
                <p class="text-[10px] font-black opacity-40 mb-4">آپ کا ریفرل لنک</p>
                <input id="ref-link" readonly class="w-full bg-black/40 p-4 rounded-xl text-[10px] border border-white/5 mb-4 text-center text-blue-400">
                <button onclick="copyRef()" class="w-full bg-white text-black py-4 rounded-xl font-black text-[11px]">لنک کاپی کریں</button>
            </div>
            <div class="grid grid-cols-2 gap-4 text-center flex-row-reverse">
                <div class="glass p-6"><p class="text-[10px] opacity-40 mb-1">کل ممبرز</p><p id="ref-count" class="text-2xl font-black italic">0</p></div>
                <div class="glass p-6"><p class="text-[10px] opacity-40 mb-1">کمیشن</p><p class="text-2xl font-black italic text-green-500">₨ 0</p></div>
            </div>
        </div>

        <nav class="nav-bar">
            <button onclick="showPage('home')" class="nav-btn p-5" id="nav-home"><i class="fa-solid fa-layer-group text-xl"></i></button>
            <button onclick="showPage('spin')" class="nav-btn p-5" id="nav-spin"><i class="fa-solid fa-dharmachakra text-xl"></i></button>
            <button onclick="showPage('finance')" class="nav-btn p-5" id="nav-finance"><i class="fa-solid fa-wallet text-xl"></i></button>
            <button onclick="showPage('team')" class="nav-btn p-5" id="nav-team"><i class="fa-solid fa-users text-xl"></i></button>
        </nav>
    </main>

    <script>
        // Firebase Config and Logic (Same as previous, just updated alerts)
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
                if(user.email === "khankhail@gmail.com") document.getElementById('owner-tag').classList.remove('hidden');
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

        async function dailyCheckIn() {
            const today = new Date().toDateString();
            if(userObj.lastCheckIn === today) return alert("کل دوبارہ آئیں، پیارے!");
            await db.collection("users").doc(auth.currentUser.displayName).set({ balance: firebase.firestore.FieldValue.increment(50), lastCheckIn: today }, { merge: true });
            alert("₨ 50 روزانہ بونس شامل کر دیا گیا!");
        }

        async function submitTx(type) {
            const amt = type === 'Deposit' ? document.getElementById('d-amt').value : document.getElementById('w-amt').value;
            const extra = type === 'Deposit' ? document.getElementById('d-tid').value : document.getElementById('w-num').value;
            if(!amt || !extra) return alert("تمام خانے پُر کریں!");
            if(type === 'Withdraw' && userObj.balance < amt) return alert("بیلنس کم ہے!");
            
            await db.collection("txs").add({ user: auth.currentUser.displayName, amount: parseInt(amt), details: extra, status: "Pending", type: type === 'Deposit' ? 'ڈیپازٹ' : 'ودرا', time: Date.now() });
            alert("درخواست بھیج دی گئی ہے!");
        }

        function renderNodes() {
            let h = ""; for(let i=1; i<=10; i++) {
                let cost = i * 200;
                h += `<div class="glass p-6 flex justify-between items-center flex-row-reverse"><div class="text-right"><h5 class="text-[11px] font-black">پلان ورژن ${i}</h5><p class="text-[9px] font-black text-green-500">منافع: ₨ ${Math.floor(cost*0.1)}/روزانہ</p></div><button onclick="buyNode(${cost})" class="bg-white text-black px-5 py-2.5 rounded-xl text-[10px] font-black">خریدیں ₨ ${cost}</button></div>`;
            }
            document.getElementById('plans-grid').innerHTML = h;
        }

        // Logic functions continue... (Rest of syncData, Admin logic as before)
        function syncData(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                userObj = doc.data() || { balance: 0, lastCheckIn: 0 };
                document.getElementById('v-bal').innerText = "₨ " + (userObj.balance || 0).toLocaleString();
            });
            db.collection("txs").where("user", "==", name).orderBy("time", "desc").limit(10).onSnapshot(s => {
                let h = ""; s.forEach(d => {
                    const t = d.data();
                    const clr = t.status === 'Approved' ? 'text-green-500' : 'text-yellow-500';
                    h += `<div class="glass p-5 flex justify-between items-center flex-row-reverse"><p class="text-[10px] font-black">${t.type}</p><div class="text-left"><p class="text-xs font-black">₨ ${t.amount}</p><p class="text-[9px] font-black ${clr}">${t.status === 'Approved' ? 'منظور شدہ' : 'پینڈنگ'}</p></div></div>`;
                });
                document.getElementById('tx-history').innerHTML = h || `<p class="text-center opacity-20 text-[10px] py-4">کوئی ریکارڈ نہیں</p>`;
            });
        }
    </script>
</body>
</html>
