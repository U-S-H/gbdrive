<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>Vestify Elite | Institutional Terminal</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700;800&display=swap');
        :root { --primary: #2563eb; --bg: #0b0f1a; --card: #161b2c; --text: #f1f5f9; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background-color: var(--bg); color: var(--text); overflow-x: hidden; -webkit-tap-highlight-color: transparent; }
        .glass { background: rgba(22, 27, 44, 0.7); backdrop-filter: blur(12px); border: 1px solid rgba(255,255,255,0.05); border-radius: 24px; }
        .hero-gradient { background: linear-gradient(135deg, #1e293b 0%, #0f172a 100%); border: 1px solid rgba(255,255,255,0.08); }
        .page { display: none; animation: slideUp 0.5s cubic-bezier(0.4, 0, 0.2, 1); }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(30px); } to { opacity: 1; transform: translateY(0); } }
        .ticker-wrap { width: 100%; overflow: hidden; background: rgba(37, 99, 235, 0.1); padding: 8px 0; border-bottom: 1px solid rgba(255,255,255,0.05); }
        .ticker { display: inline-block; white-space: nowrap; animation: ticker 30s linear infinite; }
        @keyframes ticker { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
        .notif-toast { position: fixed; top: 20px; left: 50%; transform: translateX(-50%); z-index: 9999; animation: toastIn 0.5s ease-out forwards; }
        @keyframes toastIn { 0% { opacity: 0; transform: translate(-50%, -50px); } 100% { opacity: 1; transform: translate(-50%, 0); } }
        #wheel { width: 280px; height: 280px; transition: transform 4s cubic-bezier(0.15, 0, 0.15, 1); border-radius: 50%; box-shadow: 0 0 40px rgba(59, 130, 246, 0.2); }
        ::-webkit-scrollbar { width: 0; }
    </style>
</head>
<body class="min-h-screen pb-32">

    <div class="ticker-wrap fixed top-0 w-full z-[1000] backdrop-blur-md">
        <div class="ticker text-[10px] font-bold uppercase tracking-widest text-blue-400">
            BTC/USDT $64,231.50 (+2.4%) • ETH/USDT $3,452.12 (-0.5%) • VESTIFY ELITE VOLUME: $4.2M • PROFIT DISTRIBUTED: ₨ 1.2M • LIVE TERMINAL ACTIVE
        </div>
    </div>

    <div id="admin-panel" class="hidden fixed inset-0 z-[9000] bg-black p-8 overflow-y-auto">
        <div class="flex justify-between items-center mb-10">
            <h2 class="text-3xl font-black text-blue-500 italic uppercase">Admin Protocol</h2>
            <button onclick="closeAdmin()" class="text-4xl text-white">&times;</button>
        </div>
        <div class="space-y-6">
            <div class="glass p-6">
                <p class="text-xs font-bold text-blue-400 mb-4 uppercase">Injection Protocol</p>
                <input id="adm-amt" type="number" placeholder="Enter Amount" class="w-full bg-slate-800 p-4 rounded-xl mb-4 outline-none border border-white/10">
                <button onclick="injectFunds()" class="w-full bg-blue-600 py-4 rounded-xl font-black uppercase text-xs">Inject Balance</button>
            </div>
            <p class="text-center opacity-20 text-[10px]">VESTIFY_OS_SECURE</p>
        </div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[5000] bg-[#0b0f1a] flex flex-col items-center justify-center p-10 text-center">
        <div id="main-logo" onclick="tapLogo()" class="w-24 h-24 bg-blue-600 rounded-[2.5rem] flex items-center justify-center mb-8 shadow-2xl rotate-3 cursor-pointer active:scale-95 transition-transform">
            <i class="fa-solid fa-vault text-white text-4xl"></i>
        </div>
        <h1 class="text-4xl font-black italic tracking-tighter text-white">VESTIFY <span class="text-blue-500">ELITE</span></h1>
        <p class="text-slate-500 text-xs mt-3 uppercase tracking-widest font-bold">Institutional Terminal</p>
        <button onclick="login()" class="w-full max-w-xs mt-16 bg-white text-black py-5 rounded-[2rem] font-extrabold flex items-center justify-center gap-4 active:scale-95 shadow-2xl transition-all">
            <i class="fa-brands fa-google text-xl"></i> GOOGLE SYNC
        </button>
    </section>

    <main id="app-ui" class="hidden pt-16 px-6">
        <div id="p-home" class="page active-page">
            <div class="hero-gradient rounded-[2.5rem] p-8 mb-8 relative overflow-hidden shadow-2xl">
                <p class="text-[10px] text-blue-400 font-black uppercase tracking-widest mb-2">Net Portfolio</p>
                <h2 class="text-5xl font-black tracking-tighter" id="v-bal">₨ 0.00</h2>
                <div class="flex gap-4 mt-10">
                    <button onclick="changePage('deposit')" class="flex-1 bg-white text-black py-4 rounded-2xl font-black text-[10px] uppercase">Deposit</button>
                    <button onclick="changePage('withdraw')" class="flex-1 bg-slate-800 text-white py-4 rounded-2xl font-black text-[10px] uppercase border border-white/5">Withdraw</button>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-8">
                <div class="glass p-5 text-center">
                    <p class="text-[8px] opacity-50 uppercase font-black mb-1">Daily Yield</p>
                    <p class="text-lg font-black text-green-400">₨ 4,200</p>
                </div>
                <div class="glass p-5 text-center">
                    <p class="text-[8px] opacity-50 uppercase font-black mb-1">Active Nodes</p>
                    <p class="text-lg font-black text-blue-400">12</p>
                </div>
            </div>

            <h3 class="text-xl font-black italic mb-6 px-2 uppercase">Institutional Nodes</h3>
            <div id="plans-list" class="space-y-4 pb-10"></div>
        </div>

        <div id="p-spin" class="page text-center pt-6">
            <h2 class="text-3xl font-black italic mb-12">Lucky Terminal</h2>
            <div class="relative inline-block mb-16">
                <div class="absolute -top-6 left-1/2 -translate-x-1/2 w-10 h-12 bg-red-600 z-50 shadow-xl" style="clip-path: polygon(50% 100%, 0 0, 100% 0);"></div>
                <img id="wheel" src="IMG_20260513_123757.jpg" alt="Wheel">
            </div>
            <button onclick="doSpin()" class="w-full max-w-sm bg-blue-600 text-white py-6 rounded-[2.5rem] font-black uppercase text-xs tracking-widest shadow-2xl active:scale-95 transition-all">Execute Spin</button>
        </div>

        <div id="p-deposit" class="page">
            <h2 class="text-3xl font-black italic mb-10">Funding Protocol</h2>
            <div class="glass p-8">
                <div class="grid grid-cols-2 gap-4 mb-8">
                    <div class="border-2 border-blue-600 p-6 rounded-3xl text-center bg-blue-600/5"><i class="fa-solid fa-mobile-screen text-3xl text-blue-500 mb-2"></i><p class="text-[10px] font-black uppercase">EasyPaisa</p></div>
                    <div class="border-2 border-slate-800 p-6 rounded-3xl text-center"><i class="fa-solid fa-wallet text-3xl text-slate-600 mb-2"></i><p class="text-[10px] font-black uppercase">JazzCash</p></div>
                </div>
                <input type="number" placeholder="Min ₨ 500" class="w-full bg-slate-900/50 p-5 rounded-2xl outline-none font-black text-lg border border-white/5 mb-6">
                <button class="w-full bg-blue-600 text-white py-5 rounded-2xl font-black uppercase text-xs">Proceed</button>
            </div>
        </div>
    </main>

    <div id="notif-container"></div>

    <nav id="bottom-nav" class="hidden fixed bottom-6 left-6 right-6 h-22 glass flex justify-around items-center z-[4000] border border-white/10">
        <button onclick="changePage('home')" class="flex flex-col items-center p-4 text-blue-500"><i class="fa-solid fa-house-chimney text-xl"></i><span class="text-[8px] font-black uppercase mt-1">Vault</span></button>
        <button onclick="changePage('spin')" class="flex flex-col items-center p-4 text-slate-500"><i class="fa-solid fa-dharmachakra text-xl"></i><span class="text-[8px] font-black uppercase mt-1">Spin</span></button>
        <button onclick="changePage('deposit')" class="flex flex-col items-center p-4 text-slate-500"><i class="fa-solid fa-wallet text-xl"></i><span class="text-[8px] font-black uppercase mt-1">Funding</span></button>
        <button onclick="alert('Promo Code Section')" class="flex flex-col items-center p-4 text-slate-500"><i class="fa-solid fa-ticket text-xl"></i><span class="text-[8px] font-black uppercase mt-1">Promo</span></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyC9ofJ1KxRXHnxilpU9gyI87D3BSOZ9v1g", authDomain: "vestify-991f2.firebaseapp.com", projectId: "vestify-991f2", storageBucket: "vestify-991f2.firebasestorage.app", messagingSenderId: "799007097733", appId: "1:799007097733:web:ed3b35b6c4e51dc2e7baec" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        const auth = firebase.auth();
        const provider = new firebase.auth.GoogleAuthProvider();

        let userObj = null, logoTaps = 0, spinActive = false, rotation = 0;

        function tapLogo() { logoTaps++; if(logoTaps >= 5) { const p = prompt("ADMIN_KEY:"); if(p === "net204") document.getElementById('admin-panel').classList.remove('hidden'); logoTaps = 0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        async function injectFunds() { const a = parseInt(document.getElementById('adm-amt').value); if(a && userObj) { await db.collection("users").doc(userObj.name).update({ balance: (userObj.balance || 0) + a }); alert("CAPITAL_INJECTED"); } }

        async function login() { try { const r = await auth.signInWithPopup(provider); localStorage.setItem('v_elite_user', r.user.displayName); startApp(r.user.displayName); } catch(e) { alert("Error"); } }

        function startApp(name) {
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            db.collection("users").doc(name).onSnapshot(doc => {
                userObj = doc.data() || { name: name, balance: 0 };
                document.getElementById('v-bal').innerText = "₨ " + (userObj.balance || 0).toLocaleString();
            });
            generatePlans(); runFakeNotifs();
        }

        function changePage(p) {
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.getElementById('p-'+p).classList.add('active-page');
        }

        function generatePlans() {
            let h = '';
            for(let i=1; i<=20; i++) {
                h += `<div class="glass p-6 flex justify-between items-center">
                    <div class="flex items-center gap-4">
                        <div class="w-12 h-12 bg-blue-600/10 rounded-xl flex items-center justify-center text-blue-500"><i class="fa-solid fa-microchip"></i></div>
                        <div><p class="font-black text-xs uppercase italic">Node v.${i}</p><p class="text-[8px] text-green-400 font-bold uppercase">ROI: ₨ ${i*150}/day</p></div>
                    </div>
                    <button class="bg-blue-600 text-white px-5 py-2 rounded-xl text-[9px] font-black uppercase tracking-widest">₨ ${(i*1200).toLocaleString()}</button>
                </div>`;
            }
            document.getElementById('plans-list').innerHTML = h;
        }

        function runFakeNotifs() {
            const n = ["Ali", "Sara", "Hamza", "Zain", "Sobia"], m = ["deposited ₨ 5,000", "withdrew ₨ 1,200", "unlocked Node v.5"];
            setInterval(() => {
                const t = document.createElement('div');
                t.className = "notif-toast glass bg-slate-900 px-6 py-3 text-white shadow-2xl flex items-center gap-2";
                t.innerHTML = `<div class="w-2 h-2 bg-green-500 rounded-full animate-ping"></div><p class="text-[8px] font-bold uppercase">${n[Math.floor(Math.random()*n.length)]} ${m[Math.floor(Math.random()*m.length)]}</p>`;
                document.getElementById('notif-container').appendChild(t);
                setTimeout(() => t.remove(), 4000);
            }, 6000);
        }

        async function doSpin() {
            if(spinActive) return; spinActive = true;
            rotation += Math.floor(Math.random() * 3600) + 1800;
            document.getElementById('wheel').style.transform = `rotate(${rotation}deg)`;
            setTimeout(async () => {
                spinActive = false;
                const wins = [5, 10, 50, 2, 0, 0];
                const w = wins[Math.floor(Math.random()*wins.length)];
                if(w > 0) { await db.collection("users").doc(userObj.name).update({ balance: (userObj.balance || 0) + w }); alert("Win: ₨ "+w); }
                else alert("Try Again!");
                document.getElementById('wheel').style.transform = "rotate(0deg)"; rotation = 0;
            }, 4000);
        }

        window.onload = () => { if(localStorage.getItem('v_elite_user')) startApp(localStorage.getItem('v_elite_user')); };
    </script>
</body>
</html>
