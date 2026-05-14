<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>Vestify Elite | Premium Cluster</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --primary: #3b82f6; --bg: #050810; --card: #0f172a; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: #f1f5f9; overflow-x: hidden; }
        .glass { background: rgba(15, 23, 42, 0.8); backdrop-filter: blur(20px); border: 1px solid rgba(255,255,255,0.08); border-radius: 28px; }
        .page { display: none; animation: fadeIn 0.4s ease-out; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        .vip-gold { border: 2px solid #fbbf24; box-shadow: 0 0 15px rgba(251, 191, 36, 0.2); }
        .vip-silver { border: 2px solid #94a3b8; }
        .nav-active { color: #3b82f6; position: relative; }
        .nav-active::after { content: ''; position: absolute; bottom: -5px; width: 5px; height: 5px; background: #3b82f6; border-radius: 50%; }
        ::-webkit-scrollbar { width: 0; }
    </style>
</head>
<body class="min-h-screen pb-32">

    <div id="maintenance-ui" class="hidden fixed inset-0 z-[10000] bg-black flex flex-col items-center justify-center text-center p-10">
        <i class="fa-solid fa-gears text-6xl text-blue-500 mb-6 animate-pulse"></i>
        <h2 class="text-2xl font-black uppercase">System Maintenance</h2>
        <p class="text-xs opacity-50 mt-2">Institutional servers are being optimized. Back soon.</p>
    </div>

    <div class="fixed top-6 right-6 z-[4500] flex gap-4">
        <div id="notif-bell" class="relative cursor-pointer" onclick="alert('System Update: New Mining Nodes are now online!')">
            <i class="fa-solid fa-bell text-xl opacity-40"></i>
            <span class="absolute -top-1 -right-1 w-2 h-2 bg-red-500 rounded-full"></span>
        </div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[6000] bg-[#050810] flex flex-col items-center justify-center p-10 text-center">
        <div onclick="handleLogoTap()" class="w-24 h-24 bg-blue-600 rounded-[2.5rem] flex items-center justify-center mb-8 rotate-3 shadow-2xl">
            <i class="fa-solid fa-shield-halved text-white text-4xl"></i>
        </div>
        <h1 class="text-4xl font-black italic tracking-tighter uppercase">Vestify <span class="text-blue-500">Elite</span></h1>
        <button onclick="login()" class="w-full max-w-xs mt-16 bg-white text-black py-5 rounded-[2rem] font-black uppercase text-xs tracking-widest shadow-2xl active:scale-95 transition-all">Secure Google Sync</button>
    </section>

    <main id="app-ui" class="hidden pt-12 px-6">
        
        <div class="flex items-center justify-between mb-8">
            <div>
                <p class="text-[10px] font-black opacity-40 uppercase tracking-widest">Institutional Cluster</p>
                <h3 id="user-display-name" class="text-lg font-black italic">--</h3>
            </div>
            <img id="user-photo" src="" class="w-12 h-12 rounded-2xl border-2 border-blue-500/20 shadow-xl">
        </div>

        <div id="p-home" class="page active-page">
            <div id="balance-card" class="glass p-8 mb-8 bg-gradient-to-br from-slate-900 to-black relative overflow-hidden">
                <div class="absolute -right-10 -top-10 w-32 h-32 bg-blue-600/10 rounded-full blur-3xl"></div>
                <p class="text-[9px] text-blue-400 font-black uppercase tracking-widest">Total Liquidity</p>
                <h2 class="text-5xl font-black italic mt-1 tracking-tighter" id="v-bal">₨ 0.00</h2>
                <div id="vip-status" class="mt-4 text-[8px] font-black uppercase px-3 py-1 bg-blue-500/10 inline-block rounded-lg text-blue-400">Bronze Member</div>
            </div>
            
            <h3 class="font-black text-xs uppercase tracking-widest opacity-40 mb-6">Active Mining Nodes</h3>
            <div id="active-nodes-list" class="space-y-4"></div>
        </div>

        <div id="p-finance" class="page">
            <h2 class="text-3xl font-black italic mb-8 uppercase">Finance Hub</h2>
            
            <div class="glass p-4 mb-6 flex items-center gap-3">
                <input id="promo-input" type="text" placeholder="APPLY PROMO" class="flex-1 bg-transparent border-none text-xs font-bold uppercase focus:outline-none">
                <button onclick="applyPromocode()" class="text-blue-500 font-black text-[10px] uppercase">Verify</button>
            </div>

            <div class="glass p-6 space-y-6">
                <div class="flex gap-2">
                    <button onclick="toggleFinance('dep')" class="flex-1 py-4 rounded-2xl bg-blue-600 text-[10px] font-black uppercase">Deposit</button>
                    <button onclick="toggleFinance('wd')" class="flex-1 py-4 rounded-2xl bg-red-600 text-[10px] font-black uppercase">Withdraw</button>
                </div>
                <div id="finance-dep" class="space-y-4">
                    <input id="dep-amt" type="number" placeholder="Min 200 PKR" class="w-full p-4 rounded-xl bg-slate-900 text-xs">
                    <input id="dep-tid" type="text" placeholder="TID (e.g 123456789)" class="w-full p-4 rounded-xl bg-slate-900 text-xs">
                    <input id="dep-img" type="file" accept="image/*" class="w-full text-[10px] text-slate-500">
                    <button onclick="submitDeposit()" class="w-full bg-white text-black py-4 rounded-xl font-black uppercase text-xs">Confirm Influx</button>
                </div>
            </div>
        </div>

    </main>

    <nav id="bottom-nav" class="hidden fixed bottom-6 left-6 right-6 h-20 glass flex justify-around items-center z-[4000]">
        <button onclick="changePage('home')" class="nav-btn flex flex-col items-center p-4 nav-active"><i class="fa-solid fa-vault"></i><span class="text-[8px] font-black mt-1">VAULT</span></button>
        <button onclick="changePage('finance')" class="nav-btn flex flex-col items-center p-4"><i class="fa-solid fa-wallet"></i><span class="text-[8px] font-black mt-1">BANK</span></button>
        <button onclick="window.open('https://wa.me/923379827882')" class="flex flex-col items-center p-4 text-green-500"><i class="fa-brands fa-whatsapp"></i><span class="text-[8px] font-black mt-1">HELP</span></button>
        <button onclick="logout()" class="flex flex-col items-center p-4 text-red-500"><i class="fa-solid fa-power-off"></i><span class="text-[8px] font-black mt-1">EXIT</span></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyC9ofJ1KxRXHnxilpU9gyI87D3BSOZ9v1g", authDomain: "vestify-991f2.firebaseapp.com", projectId: "vestify-991f2", storageBucket: "vestify-991f2.firebasestorage.app", messagingSenderId: "799007097733", appId: "1:799007097733:web:ed3b35b6c4e51dc2e7baec" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        const auth = firebase.auth();
        const provider = new firebase.auth.GoogleAuthProvider();

        let userObj = null, tapCount = 0;

        // Maintenance Check
        db.collection("settings").doc("system").onSnapshot(doc => {
            if(doc.exists && doc.data().maintenance) document.getElementById('maintenance-ui').classList.remove('hidden');
            else document.getElementById('maintenance-ui').classList.add('hidden');
        });

        async function login() {
            const r = await auth.signInWithPopup(provider);
            initApp(r.user);
        }

        function initApp(user) {
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            document.getElementById('user-display-name').innerText = user.displayName;
            document.getElementById('user-photo').src = user.photoURL;
            syncData(user.displayName);
        }

        function syncData(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                userObj = doc.data() || { name: name, balance: 0, plans: [], usedPromos: [] };
                document.getElementById('v-bal').innerText = "₨ " + (userObj.balance || 0).toLocaleString();
                updateVIP(userObj.balance);
            });
        }

        function updateVIP(bal) {
            const el = document.getElementById('vip-status');
            const card = document.getElementById('balance-card');
            if(bal >= 10000) { 
                el.innerText = "GOLD ELITE MEMBER"; 
                el.className = "mt-4 text-[8px] font-black uppercase px-3 py-1 bg-yellow-500/20 inline-block rounded-lg text-yellow-500";
                card.classList.add('vip-gold');
            } else if(bal >= 5000) {
                el.innerText = "SILVER MEMBER";
                el.className = "mt-4 text-[8px] font-black uppercase px-3 py-1 bg-slate-400/20 inline-block rounded-lg text-slate-400";
                card.classList.add('vip-silver');
            }
        }

        async function applyPromocode() {
            const code = document.getElementById('promo-input').value.trim().toUpperCase();
            const codes = { 'J5B82': 100, 'G7J99': 250 };
            if(!codes[code]) return alert("Invalid Code!");
            if(userObj.usedPromos?.includes(code)) return alert("Already Used!");
            
            await db.collection("users").doc(userObj.name).update({
                balance: userObj.balance + codes[code],
                usedPromos: firebase.firestore.FieldValue.arrayUnion(code)
            });
            alert("Institutional Bonus Credited!");
        }

        async function submitDeposit() {
            const amt = parseInt(document.getElementById('dep-amt').value);
            const tid = document.getElementById('dep-tid').value;
            const file = document.getElementById('dep-img').files[0];
            if(amt < 200 || !tid || !file) return alert("Missing Data!");

            const reader = new FileReader();
            reader.onloadend = async () => {
                await db.collection("requests").add({
                    user: userObj.name, type: 'Deposit', amount: amt, tid: tid, 
                    proof: reader.result, status: 'Pending', time: Date.now()
                });
                alert("Protocol Logged!");
            };
            reader.readAsDataURL(file);
        }

        function changePage(p) {
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.getElementById('p-'+p).classList.add('active-page');
            document.querySelectorAll('.nav-btn').forEach(btn => btn.classList.remove('nav-active'));
            event.currentTarget.classList.add('nav-active');
        }

        function handleLogoTap() { tapCount++; if(tapCount>=5){ const p=prompt("Key:"); if(p==="net204") alert("Admin system active in Firestore console."); tapCount=0; } }
        function logout() { auth.signOut().then(() => location.reload()); }
    </script>
</body>
</html>
