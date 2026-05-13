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
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --primary: #2563eb; --bg: #f8fafc; --card: #ffffff; --text: #0f172a; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background-color: var(--bg); color: var(--text); overflow-x: hidden; }
        .glass { background: var(--card); border: 1px solid rgba(0,0,0,0.04); border-radius: 30px; box-shadow: 0 10px 30px -10px rgba(0,0,0,0.05); }
        .hero-card { background: linear-gradient(135deg, #0f172a 0%, #1e293b 100%); border-radius: 40px; padding: 2rem; color: white; margin-bottom: 2rem; position: relative; overflow: hidden; }
        .page { display: none; animation: slideIn 0.4s ease-out; }
        .active-page { display: block; }
        @keyframes slideIn { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        #wheel { width: 280px; height: 280px; transition: transform 4s cubic-bezier(0.15, 0, 0.15, 1); border-radius: 50%; }
        .notif { position: fixed; top: 20px; left: 50%; transform: translateX(-50%); z-index: 10000; animation: bounceIn 0.5s ease; }
        @keyframes bounceIn { 0% { top: -100px; } 70% { top: 30px; } 100% { top: 20px; } }
    </style>
</head>
<body class="min-h-screen pb-32">

    <div id="notif-area"></div>

    <div id="admin-panel" class="hidden fixed inset-0 z-[6000] bg-slate-900 text-white p-6 overflow-y-auto">
        <div class="flex justify-between items-center mb-10">
            <h2 class="text-2xl font-black">Admin Protocol</h2>
            <button onclick="closeAdmin()" class="text-2xl">&times;</button>
        </div>
        <div class="space-y-6">
            <div class="glass bg-white/10 p-4">
                <p class="text-xs opacity-50 mb-2">Manual Balance Injection</p>
                <input id="adm-amt" type="number" placeholder="Amount" class="w-full bg-transparent border-b border-white/20 py-2 mb-4 outline-none">
                <button onclick="addAdmBal()" class="w-full bg-blue-600 py-3 rounded-xl font-bold">Inject Capital</button>
            </div>
            <div class="glass bg-white/10 p-4">
                <p class="text-xs opacity-50 mb-2">Promo Code Creator</p>
                <input id="adm-promo" type="text" placeholder="CODE2026" class="w-full bg-transparent border-b border-white/20 py-2 outline-none">
                <button onclick="alert('Promo Saved')" class="w-full mt-4 bg-green-600 py-3 rounded-xl font-bold">Deploy Code</button>
            </div>
        </div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[5000] bg-white flex flex-col items-center justify-center p-10">
        <div id="logo-tap" onclick="handleLogoTap()" class="w-24 h-24 bg-blue-600 rounded-[2.5rem] flex items-center justify-center mx-auto mb-8 shadow-2xl rotate-3 cursor-pointer active:scale-90 transition-transform">
            <i class="fa-solid fa-vault text-white text-4xl"></i>
        </div>
        <h1 class="text-4xl font-black italic tracking-tighter">VESTIFY <span class="text-blue-600">ELITE</span></h1>
        <p class="text-slate-400 text-sm mt-2">Institutional Grade Trading Terminal</p>
        <button onclick="loginWithGoogle()" class="w-full max-w-xs mt-12 bg-[#0f172a] text-white py-5 rounded-[2rem] font-bold flex items-center justify-center gap-3 active:scale-95 transition-all shadow-xl">
            <i class="fa-brands fa-google"></i> Secure Google Sync
        </button>
    </section>

    <main id="app-ui" class="hidden px-6 pt-6">
        
        <div id="p-home" class="page active-page">
            <div class="hero-card shadow-2xl">
                <div class="flex justify-between items-start mb-6">
                    <div>
                        <p class="text-[10px] opacity-60 uppercase font-bold tracking-widest">Active Portfolio</p>
                        <h2 class="text-4xl font-black mt-1" id="v-bal">₨ 0.00</h2>
                    </div>
                    <div class="bg-white/10 px-3 py-1 rounded-full text-[10px] font-bold border border-white/20">ELITE MEMBER</div>
                </div>
                <div class="flex gap-4">
                    <button onclick="changePage('deposit')" class="flex-1 bg-white text-slate-900 py-3 rounded-2xl font-bold text-xs">Deposit</button>
                    <button onclick="changePage('withdraw')" class="flex-1 bg-white/20 backdrop-blur-md py-3 rounded-2xl font-bold text-xs border border-white/10">Withdraw</button>
                </div>
            </div>

            <div class="flex justify-between items-center mb-6 px-2">
                <h3 class="font-black text-lg">Investment Nodes</h3>
                <span class="text-[10px] text-blue-600 font-bold">20+ ACTIVE PLANS</span>
            </div>
            <div id="plans-list" class="space-y-4"></div>
        </div>

        <div id="p-spin" class="page text-center">
            <h2 class="text-2xl font-black mb-10">Lucky Terminal</h2>
            <div class="relative inline-block mb-12">
                <div class="absolute -top-4 left-1/2 -translate-x-1/2 w-8 h-10 bg-red-600 z-10" style="clip-path: polygon(50% 100%, 0 0, 100% 0);"></div>
                <img id="wheel" src="IMG_20260513_123757.jpg" alt="Wheel">
            </div>
            <button onclick="handleSpin()" class="w-full bg-blue-600 text-white py-5 rounded-[2rem] font-black uppercase text-xs tracking-widest shadow-xl active:scale-95 transition-all">Execute Spin</button>
        </div>

        <div id="p-deposit" class="page">
            <h2 class="text-2xl font-black mb-6">Funding Protocol</h2>
            <div class="glass p-6 mb-4">
                <p class="text-xs font-bold opacity-50 mb-4">SELECT GATEWAY</p>
                <div class="grid grid-cols-2 gap-4">
                    <div class="border-2 border-blue-600 p-4 rounded-2xl text-center"><i class="fa-solid fa-mobile-screen text-2xl mb-2"></i><p class="text-[10px] font-bold">EasyPaisa</p></div>
                    <div class="border-2 border-slate-100 p-4 rounded-2xl text-center"><i class="fa-solid fa-building-columns text-2xl mb-2"></i><p class="text-[10px] font-bold">JazzCash</p></div>
                </div>
                <input type="number" placeholder="Enter Amount (min. 500)" class="w-full mt-6 bg-slate-50 p-4 rounded-xl outline-none font-bold text-sm">
                <button class="w-full mt-4 bg-blue-600 text-white py-4 rounded-2xl font-bold">Generate Invoice</button>
            </div>
        </div>

    </main>

    <nav id="bottom-nav" class="hidden fixed bottom-6 left-6 right-6 h-20 glass flex justify-around items-center z-[4000]">
        <button onclick="changePage('home')" class="nav-btn text-blue-600 flex flex-col items-center"><i class="fa-solid fa-house-chimney text-lg"></i><span class="text-[8px] font-bold uppercase mt-1">Terminal</span></button>
        <button onclick="changePage('spin')" class="nav-btn text-slate-400 flex flex-col items-center"><i class="fa-solid fa-dharmachakra text-lg"></i><span class="text-[8px] font-bold uppercase mt-1">Spin</span></button>
        <button onclick="changePage('deposit')" class="nav-btn text-slate-400 flex flex-col items-center"><i class="fa-solid fa-wallet text-lg"></i><span class="text-[8px] font-bold uppercase mt-1">Funding</span></button>
        <button onclick="openPromo()" class="nav-btn text-slate-400 flex flex-col items-center"><i class="fa-solid fa-ticket text-lg"></i><span class="text-[8px] font-bold uppercase mt-1">Promo</span></button>
    </nav>

    <script>
        // Firebase Configuration (Aapka wahi purana)
        const firebaseConfig = {
            apiKey: "AIzaSyC9ofJ1KxRXHnxilpU9gyI87D3BSOZ9v1g",
            authDomain: "vestify-991f2.firebaseapp.com",
            projectId: "vestify-991f2",
            storageBucket: "vestify-991f2.firebasestorage.app",
            messagingSenderId: "799007097733",
            appId: "1:799007097733:web:ed3b35b6c4e51dc2e7baec"
        };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        const auth = firebase.auth();
        const provider = new firebase.auth.GoogleAuthProvider();

        let userObj = null;
        let tapCount = 0;

        // Admin Protocol (Hidden Tap)
        function handleLogoTap() {
            tapCount++;
            if(tapCount === 5) {
                const key = prompt("Enter Protocol Key:");
                if(key === "net204") {
                    document.getElementById('admin-panel').classList.remove('hidden');
                }
                tapCount = 0;
            }
        }

        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }

        async function addAdmBal() {
            const amt = parseInt(document.getElementById('adm-amt').value);
            await db.collection("users").doc(userObj.name).update({ balance: (userObj.balance || 0) + amt });
            alert("Capital Injected Successfully, Sweetie!");
        }

        // Auth Logic
        async function loginWithGoogle() {
            const res = await auth.signInWithPopup(provider);
            const name = res.user.displayName;
            localStorage.setItem('v_user', name);
            initApp(name);
        }

        function initApp(name) {
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            syncData(name);
            render20Plans();
            startFakeNotifications();
        }

        function syncData(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                userObj = doc.data() || { name: name, balance: 0 };
                document.getElementById('v-bal').innerText = "₨ " + (userObj.balance||0).toLocaleString();
            });
        }

        function changePage(p) {
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.getElementById('p-'+p).classList.add('active-page');
        }

        // 20+ Modern Plans
        function render20Plans() {
            const icons = ['bolt', 'shield', 'gem', 'crown', 'rocket', 'fire', 'vault'];
            let html = '';
            for(let i=1; i<=20; i++) {
                const price = i * 1000;
                const daily = (price * 0.1).toFixed(0);
                const icon = icons[i % icons.length];
                html += `
                <div class="glass p-5 flex justify-between items-center group active:scale-95 transition-all">
                    <div class="flex items-center gap-4">
                        <div class="w-12 h-12 bg-blue-50 rounded-2xl flex items-center justify-center text-blue-600">
                            <i class="fa-solid fa-${icon} text-xl"></i>
                        </div>
                        <div>
                            <p class="font-black text-xs uppercase italic tracking-tighter">Elite Node ${i}</p>
                            <p class="text-[9px] text-green-600 font-bold">Daily Return: ₨ ${daily}</p>
                        </div>
                    </div>
                    <button class="bg-slate-900 text-white px-4 py-2 rounded-xl text-[10px] font-bold">₨ ${price.toLocaleString()}</button>
                </div>`;
            }
            document.getElementById('plans-list').innerHTML = html;
        }

        // 25+ Fake Notifications Logic
        function startFakeNotifications() {
            const users = ["Ali", "Sara", "Zain", "Hamza", "Ayesha", "M. Ahmed", "Sobia", "Bilal", "Usman", "Danish"];
            const actions = ["deposited", "withdrew", "earned", "unlocked Elite Node", "received bonus"];
            const amounts = ["₨ 5,000", "₨ 12,500", "₨ 1,200", "₨ 50,000", "₨ 800"];

            setInterval(() => {
                const u = users[Math.floor(Math.random() * users.length)];
                const a = actions[Math.floor(Math.random() * actions.length)];
                const am = amounts[Math.floor(Math.random() * amounts.length)];
                
                const div = document.createElement('div');
                div.className = "notif glass bg-slate-900 text-white px-6 py-3 rounded-full flex items-center gap-3 shadow-2xl border border-white/20";
                div.innerHTML = `<div class="w-2 h-2 bg-green-500 rounded-full animate-pulse"></div><p class="text-[10px] font-bold">${u} just ${a} ${am}</p>`;
                
                document.getElementById('notif-area').appendChild(div);
                setTimeout(() => div.remove(), 4000);
            }, 8000);
        }

        async function handleSpin() {
            const wheel = document.getElementById('wheel');
            const deg = Math.floor(Math.random() * 3600) + 1800;
            wheel.style.transform = `rotate(${deg}deg)`;
            setTimeout(() => {
                alert("Spin Locked! Balance updated, Sweetie!");
                wheel.style.transform = "rotate(0deg)";
            }, 4000);
        }

        function openPromo() {
            const code = prompt("Protocol Activation Code:");
            if(code) alert("Processing Code...");
        }

        window.onload = () => { if(localStorage.getItem('v_user')) initApp(localStorage.getItem('v_user')); };
    </script>
</body>
</html>
