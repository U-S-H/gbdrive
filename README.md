<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>Vestify Elite | Quantum Institutional Singapore</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --primary: #3b82f6; --bg: #010409; --card: #0d1117; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: #f0f6fc; overflow-x: hidden; -webkit-user-select: none; }
        
        /* Modern UI Elements */
        .glass { background: rgba(22, 27, 34, 0.8); backdrop-filter: blur(25px); border: 1px solid rgba(255,255,255,0.05); border-radius: 30px; }
        .page { display: none; animation: pageIn 0.5s cubic-bezier(0.16, 1, 0.3, 1); }
        .active-page { display: block; }
        @keyframes pageIn { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        
        /* Spin Wheel Style */
        #wheel { width: 280px; height: 280px; border-radius: 50%; border: 8px solid rgba(59, 130, 246, 0.1); transition: transform 5s cubic-bezier(0.15, 0, 0.15, 1); box-shadow: 0 0 50px rgba(59, 130, 246, 0.2); }
        
        /* Navigation */
        .nav-active { color: #3b82f6; background: rgba(59, 130, 246, 0.1); border-radius: 20px; }
        .sidebar { transform: translateX(100%); transition: 0.4s cubic-bezier(0.16, 1, 0.3, 1); }
        .sidebar-open { transform: translateX(0); }
        
        /* Animations */
        .loader-dots div { width: 8px; height: 8px; background: #3b82f6; border-radius: 50%; animation: bounce 0.5s infinite alternate; }
        @keyframes bounce { to { transform: translateY(-10px); opacity: 0.3; } }
        ::-webkit-scrollbar { width: 0; }
    </style>
</head>
<body class="min-h-screen pb-28">

    <div id="loader" class="fixed inset-0 z-[99999] bg-[#010409] flex flex-col items-center justify-center">
        <div class="loader-dots flex gap-2">
            <div style="animation-delay: 0.1s"></div>
            <div style="animation-delay: 0.2s"></div>
            <div style="animation-delay: 0.3s"></div>
        </div>
        <p class="mt-6 text-[10px] font-black uppercase tracking-[0.5em] text-blue-500/50">Quantum Syncing...</p>
    </div>

    <section id="auth-ui" class="hidden fixed inset-0 z-[8000] bg-[#010409] flex flex-col items-center justify-center p-12">
        <div onclick="handleLogoTap()" class="w-24 h-24 bg-blue-600 rounded-[2.5rem] flex items-center justify-center mb-8 rotate-3 shadow-[0_20px_60px_rgba(59,130,246,0.3)] cursor-pointer">
            <i class="fa-solid fa-shield-halved text-white text-4xl"></i>
        </div>
        <h1 class="text-4xl font-black italic tracking-tighter uppercase mb-2">Vestify <span class="text-blue-500">Elite</span></h1>
        <p class="text-[9px] opacity-30 uppercase tracking-[0.4em] mb-16">Institutional Asset Management</p>
        <button onclick="login()" class="w-full max-w-xs bg-white text-black py-5 rounded-[2rem] font-black uppercase text-xs tracking-widest active:scale-95 transition-all">Secure Sync</button>
    </section>

    <div id="side-menu" class="sidebar fixed inset-0 z-[9000] bg-black/90 backdrop-blur-2xl p-10 flex flex-col">
        <div class="flex justify-between items-center mb-12">
            <h2 class="text-2xl font-black italic">ELITE <span class="text-blue-500">MENU</span></h2>
            <button onclick="toggleMenu()" class="text-4xl">&times;</button>
        </div>
        <div class="flex flex-col gap-6 text-sm font-bold uppercase tracking-widest">
            <button onclick="changePage('company')" class="text-left py-4 border-b border-white/5"><i class="fa-solid fa-building-columns mr-4 text-blue-500"></i> Corporate Intel</button>
            <button onclick="changePage('history')" class="text-left py-4 border-b border-white/5"><i class="fa-solid fa-clock-rotate-left mr-4 text-blue-500"></i> Transactions</button>
            <button onclick="changePage('legal')" class="text-left py-4 border-b border-white/5"><i class="fa-solid fa-scale-balanced mr-4 text-blue-500"></i> Privacy Policy</button>
            <button onclick="changePage('faq')" class="text-left py-4 border-b border-white/5"><i class="fa-solid fa-circle-question mr-4 text-blue-500"></i> Global FAQ</button>
            <button onclick="logout()" class="text-left py-4 text-red-500 mt-auto"><i class="fa-solid fa-power-off mr-4"></i> Shutdown Terminal</button>
        </div>
    </div>

    <main id="app-ui" class="hidden pt-10 px-6">
        
        <div class="flex items-center justify-between mb-10">
            <div class="flex items-center gap-4">
                <img id="user-photo" src="" class="w-11 h-11 rounded-2xl border border-white/10 shadow-lg">
                <div>
                    <p id="user-name" class="text-xs font-black uppercase italic">User</p>
                    <p class="text-[8px] font-bold text-green-500 uppercase tracking-widest">● Active Node</p>
                </div>
            </div>
            <button onclick="toggleMenu()" class="w-11 h-11 glass flex items-center justify-center text-blue-500 active:rotate-90 transition-transform"><i class="fa-solid fa-bars-staggered"></i></button>
        </div>

        <div id="p-home" class="page active-page">
            <div class="glass p-8 bg-gradient-to-br from-blue-600/10 to-transparent mb-6 border-blue-500/10">
                <p class="text-[9px] font-black opacity-40 uppercase tracking-[0.2em] mb-1">Total Liquidity</p>
                <h3 class="text-5xl font-black italic tracking-tighter mb-8" id="v-bal">₨ 0.00</h3>
                <div class="flex gap-3">
                    <button onclick="claimProfit()" class="flex-1 bg-white text-black py-4 rounded-2xl font-black text-[10px] uppercase shadow-xl">Claim Gains</button>
                    <button onclick="changePage('finance')" class="flex-1 glass py-4 rounded-2xl font-black text-[10px] uppercase">Finance</button>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-10">
                <div class="glass p-5"><p class="text-[8px] opacity-40 uppercase font-black mb-1">Monthly Salary</p><p id="v-salary" class="text-sm font-black text-blue-400">₨ 0</p></div>
                <div class="glass p-5"><p class="text-[8px] opacity-40 uppercase font-black mb-1">Referral Bonus</p><p id="v-bonus" class="text-sm font-black text-green-400">₨ 0</p></div>
            </div>

            <h4 class="text-[10px] font-black uppercase opacity-30 mb-5 tracking-[0.3em]">Market Clusters</h4>
            <div id="plans-grid" class="space-y-4 pb-10"></div>
        </div>

        <div id="p-spin" class="page text-center">
            <h2 class="text-3xl font-black italic mb-2 uppercase">Lucky Terminal</h2>
            <p class="text-[10px] text-blue-500 uppercase font-bold italic mb-10 tracking-[0.3em]">Quantum Extraction Protocol</p>
            <div class="relative inline-block mb-12">
                <div class="absolute -top-6 left-1/2 -translate-x-1/2 w-10 h-12 bg-red-600 z-50 shadow-xl" style="clip-path: polygon(50% 100%, 0 0, 100% 0);"></div>
                <img id="wheel" src="IMG_20260515_012434.jpg" alt="Spin">
            </div>
            <div class="max-w-xs mx-auto space-y-4">
                <button id="spin-btn" onclick="executeSpin()" class="w-full bg-blue-600 py-5 rounded-3xl font-black uppercase text-xs italic shadow-2xl">Execute Spin</button>
                <button onclick="buySpins()" class="w-full glass py-4 rounded-3xl font-black text-[10px] uppercase border-blue-500/20">Buy 2 Spins (₨ 20)</button>
                <div class="glass py-2 px-4 inline-block text-[9px] font-black text-blue-400 uppercase tracking-widest" id="spin-timer">READY</div>
            </div>
        </div>

        <div id="p-team" class="page">
            <h2 class="text-3xl font-black italic mb-8 uppercase">Network Team</h2>
            <div class="glass p-6 mb-6">
                <p class="text-[9px] font-black opacity-40 uppercase mb-4 tracking-widest">Active Referrals: <span id="v-team" class="text-blue-500">0</span></p>
                <div class="space-y-4">
                    <div class="flex justify-between items-center text-[11px] font-bold">
                        <p>5 Active Member Target</p>
                        <p class="text-green-500">₨ 2,000 Salary</p>
                    </div>
                    <div class="flex justify-between items-center text-[11px] font-bold">
                        <p>20 Active Member Target</p>
                        <p class="text-green-500">₨ 10,000 Salary</p>
                    </div>
                </div>
            </div>
            <div class="glass p-6 text-center">
                <p class="text-[8px] opacity-40 uppercase font-black mb-3">Your Referral Node</p>
                <input id="ref-link" readonly class="w-full bg-black/50 p-4 rounded-xl text-[10px] mb-4 text-center border border-white/5">
                <button onclick="copyRef()" class="w-full bg-blue-600 py-4 rounded-xl font-black text-xs">COPY NODE LINK</button>
            </div>
        </div>

        <div id="p-company" class="page">
            <h2 class="text-3xl font-black italic mb-8 uppercase text-blue-500">LLC Details</h2>
            <div class="glass p-8 space-y-8 text-[12px] leading-relaxed opacity-80">
                <div><h5 class="font-black uppercase text-blue-400 mb-1">Entity Name</h5><p>Vestify Elite Quantum Institutional LLC</p></div>
                <div><h5 class="font-black uppercase text-blue-400 mb-1">HQ Location</h5><p>8 Marina View, Asia Square Tower 1, Singapore 018960</p></div>
                <div><h5 class="font-black uppercase text-blue-400 mb-1">Regulatory Body</h5><p>Monetary Authority of Singapore (FinTech Sandbox)</p></div>
                <p>Vestify Elite operates as a high-frequency liquidity provider, utilizing cross-border clusters to generate sustainable returns for retail and institutional partners globally.</p>
            </div>
        </div>

    </main>

    <div id="admin-panel" class="hidden fixed inset-0 z-[20000] bg-[#010409] p-10 overflow-y-auto">
        <div class="flex justify-between items-center mb-12">
            <h2 class="text-2xl font-black italic text-blue-500">ADMIN CONTROL</h2>
            <button onclick="closeAdmin()" class="text-5xl">&times;</button>
        </div>
        <div class="space-y-6">
            <div class="glass p-6">
                <h4 class="text-[10px] font-black uppercase opacity-40 mb-4">Pending Message Requests</h4>
                <div id="admin-chat-list" class="space-y-3"></div>
            </div>
            <div class="glass p-6">
                <h4 class="text-[10px] font-black uppercase opacity-40 mb-4">Quick Actions</h4>
                <div class="grid grid-cols-2 gap-3">
                    <button onclick="alert('Maintenance Enabled')" class="bg-red-600 py-4 rounded-xl font-black text-[9px] uppercase">Lock Server</button>
                    <button onclick="location.reload()" class="bg-slate-800 py-4 rounded-xl font-black text-[9px] uppercase">Refresh Data</button>
                </div>
            </div>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden fixed bottom-6 left-6 right-6 h-20 glass flex justify-around items-center z-[7000] border-t border-white/5">
        <button onclick="changePage('home')" class="nav-item p-4 text-xl"><i class="fa-solid fa-layer-group"></i></button>
        <button onclick="changePage('spin')" class="nav-item p-4 text-xl"><i class="fa-solid fa-dharmachakra"></i></button>
        <button onclick="changePage('team')" class="nav-item p-4 text-xl"><i class="fa-solid fa-users-viewfinder"></i></button>
        <button onclick="changePage('support')" class="nav-item p-4 text-xl"><i class="fa-solid fa-headset"></i></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyC9ofJ1KxRXHnxilpU9gyI87D3BSOZ9v1g", authDomain: "vestify-991f2.firebaseapp.com", projectId: "vestify-991f2", storageBucket: "vestify-991f2.firebasestorage.app", messagingSenderId: "799007097733", appId: "1:799007097733:web:ed3b35b6c4e51dc2e7baec" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        const auth = firebase.auth();
        const provider = new firebase.auth.GoogleAuthProvider();

        let userObj = null, spinActive = false, rot = 0, tapCount = 0;

        // Pricing based on your image
        const SPIN_WINS = { 0: 100, 45: 50, 90: 10, 135: 5, 180: 2, 225: 10, 270: 2, 315: 1 };

        window.onload = () => {
            setTimeout(() => {
                document.getElementById('loader').style.display = 'none';
                auth.onAuthStateChanged(u => u ? initApp(u) : document.getElementById('auth-ui').classList.remove('hidden'));
            }, 2000);
        };

        async function login() { await auth.signInWithPopup(provider); }
        function logout() { auth.signOut().then(() => location.reload()); }

        function initApp(u) {
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            document.getElementById('user-photo').src = u.photoURL;
            document.getElementById('user-name').innerText = u.displayName;
            document.getElementById('ref-link').value = `https://vestify.elite/node?ref=${u.displayName.split(' ')[0]}`;
            syncUserData(u.displayName);
            renderPlans();
            setInterval(updateSpinTimer, 1000);
        }

        function syncUserData(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                userObj = doc.data() || { name: name, balance: 0, lastSpin: 0, extraSpins: 0, salary: 0, bonus: 0, team: 0 };
                document.getElementById('v-bal').innerText = "₨ " + userObj.balance.toLocaleString();
                document.getElementById('v-salary').innerText = "₨ " + (userObj.salary || 0);
                document.getElementById('v-bonus').innerText = "₨ " + (userObj.bonus || 0);
                document.getElementById('v-team').innerText = (userObj.team || 0);
            });
        }

        function renderPlans() {
            let h = '';
            for(let i=1; i<=20; i++) {
                const cost = i * 1500;
                const daily = Math.floor(cost * 0.12);
                h += `
                <div class="glass p-6 flex justify-between items-center bg-white/[0.02]">
                    <div>
                        <h5 class="font-black uppercase text-[10px] italic mb-1">Quantum Node v.${i}</h5>
                        <p class="text-[9px] font-bold text-green-400 uppercase tracking-widest">₨ ${daily}/Day ● 30 Days</p>
                    </div>
                    <div class="text-right">
                        <p class="text-xs font-black mb-3 italic">₨ ${cost}</p>
                        <button onclick="buyPlan(${cost}, ${daily})" class="bg-white text-black px-5 py-2 rounded-xl font-black text-[9px] uppercase active:scale-90 transition-transform">Activate</button>
                    </div>
                </div>`;
            }
            document.getElementById('plans-grid').innerHTML = h;
        }

        // --- Spin Logic ---
        async function executeSpin() {
            if(spinActive) return;
            const isFree = (Date.now() - (userObj.lastSpin || 0)) >= 86400000;
            if(!isFree && (userObj.extraSpins || 0) <= 0) return alert("Next Free Spin in 24h!");

            spinActive = true;
            const keys = Object.keys(SPIN_WINS);
            const stopDeg = parseInt(keys[Math.floor(Math.random()*keys.length)]);
            rot += (3600 + stopDeg - (rot % 360));
            document.getElementById('wheel').style.transform = `rotate(${rot}deg)`;

            setTimeout(async () => {
                spinActive = false;
                const win = SPIN_WINS[stopDeg];
                const update = { balance: userObj.balance + win };
                if(isFree) update.lastSpin = Date.now();
                else update.extraSpins = userObj.extraSpins - 1;
                
                await db.collection("users").doc(userObj.name).update(update);
                alert(`Protocol Successful: ₨ ${win} Extracted!`);
            }, 5500);
        }

        function updateSpinTimer() {
            const diff = Date.now() - (userObj.lastSpin || 0);
            const el = document.getElementById('spin-timer');
            if(diff < 86400000 && (userObj.extraSpins || 0) <= 0) {
                const rem = 86400000 - diff;
                const h = Math.floor(rem/3600000), m = Math.floor((rem%3600000)/60000);
                el.innerText = `NEXT FREE: ${h}H ${m}M`;
            } else {
                el.innerText = userObj.extraSpins > 0 ? `${userObj.extraSpins} ELITE SPINS LEFT` : "FREE SPIN READY";
            }
        }

        async function buySpins() {
            if(userObj.balance < 20) return alert("Low Liquidity!");
            await db.collection("users").doc(userObj.name).update({
                balance: userObj.balance - 20,
                extraSpins: (userObj.extraSpins || 0) + 2
            });
            alert("2 Extra Spins Purchased!");
        }

        // --- Core Helpers ---
        function toggleMenu() { document.getElementById('side-menu').classList.toggle('sidebar-open'); }
        function changePage(p) {
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.getElementById('p-'+p).classList.add('active-page');
            document.getElementById('side-menu').classList.remove('sidebar-open');
        }

        function handleLogoTap() { 
            tapCount++; 
            if(tapCount >= 5) { 
                const k = prompt("Master Access:"); 
                if(k === "net204") {
                    document.getElementById('admin-panel').classList.remove('hidden');
                    loadAdminRequests();
                }
                tapCount = 0; 
            } 
        }

        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function copyRef() {
            navigator.clipboard.writeText(document.getElementById('ref-link').value);
            alert("Ref Link Copied to Clipboard!");
        }
    </script>
</body>
</html>
