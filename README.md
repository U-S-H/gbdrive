<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>Vestify Elite | Quantum LLC Singapore</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --primary: #3b82f6; --bg: #010409; --card: #0d1117; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: #f0f6fc; overflow-x: hidden; -webkit-user-select: none; }
        
        .glass { background: rgba(22, 27, 34, 0.85); backdrop-filter: blur(25px); border: 1px solid rgba(255,255,255,0.05); border-radius: 28px; }
        .page { display: none; animation: slideIn 0.4s cubic-bezier(0.16, 1, 0.3, 1); }
        .active-page { display: block; }
        @keyframes slideIn { from { opacity: 0; transform: translateY(15px); } to { opacity: 1; transform: translateY(0); } }
        
        #wheel { width: 280px; height: 280px; border-radius: 50%; transition: transform 5s cubic-bezier(0.15, 0, 0.15, 1); box-shadow: 0 0 50px rgba(59, 130, 246, 0.15); }
        .nav-item.active { color: #3b82f6; background: rgba(59, 130, 246, 0.08); border-radius: 20px; }
        .sidebar { transform: translateX(100%); transition: 0.4s cubic-bezier(0.16, 1, 0.3, 1); }
        .sidebar-open { transform: translateX(0); }
        ::-webkit-scrollbar { width: 0; }
        .status-badge { font-size: 8px; padding: 2px 8px; border-radius: 6px; font-weight: 800; text-transform: uppercase; }
    </style>
</head>
<body class="min-h-screen pb-28">

    <div id="loader" class="fixed inset-0 z-[99999] bg-[#010409] flex flex-col items-center justify-center">
        <div class="w-12 h-12 border-2 border-blue-500/20 border-t-blue-500 rounded-full animate-spin"></div>
        <p class="mt-4 text-[9px] font-black uppercase tracking-[0.4em] text-blue-500/60">Singapore Node Sync...</p>
    </div>

    <section id="auth-ui" class="hidden fixed inset-0 z-[8000] bg-[#010409] flex flex-col items-center justify-center p-12">
        <div onclick="handleLogoTap()" class="w-20 h-20 bg-blue-600 rounded-[2rem] flex items-center justify-center mb-6 rotate-3 shadow-2xl"><i class="fa-solid fa-shield-halved text-white text-3xl"></i></div>
        <h1 class="text-3xl font-black italic uppercase tracking-tighter">Vestify <span class="text-blue-500">Elite</span></h1>
        <p class="text-[8px] opacity-30 uppercase tracking-[0.4em] mb-12 text-center leading-loose">Quantum Institutional LLC<br>Singapore Terminal</p>
        <button onclick="login()" class="w-full max-w-xs bg-white text-black py-5 rounded-3xl font-black uppercase text-[11px] tracking-widest active:scale-95 transition-all">Establish Connection</button>
    </section>

    <div id="side-menu" class="sidebar fixed inset-0 z-[9000] bg-black/95 backdrop-blur-2xl p-10 flex flex-col">
        <div class="flex justify-between items-center mb-12">
            <h2 class="text-xl font-black italic uppercase">Institutional <span class="text-blue-500">Menu</span></h2>
            <button onclick="toggleMenu()" class="text-4xl">&times;</button>
        </div>
        <div class="flex flex-col gap-6 text-[11px] font-black uppercase tracking-[0.2em]">
            <button onclick="changePage('company')" class="text-left py-4 border-b border-white/5 opacity-70">Corporate Intel</button>
            <button onclick="changePage('team')" class="text-left py-4 border-b border-white/5 opacity-70">Team Network</button>
            <button onclick="changePage('legal')" class="text-left py-4 border-b border-white/5 opacity-70">Privacy & Terms</button>
            <button onclick="logout()" class="text-left py-4 text-red-500 mt-auto">Disconnect Terminal</button>
        </div>
    </div>

    <main id="app-ui" class="hidden pt-10 px-6">
        
        <div class="flex items-center justify-between mb-8">
            <div class="flex items-center gap-3">
                <img id="user-photo" class="w-10 h-10 rounded-xl border border-white/10 shadow-lg">
                <div>
                    <p id="user-name" class="text-[11px] font-black uppercase italic">User</p>
                    <p class="text-[7px] font-bold text-green-500 uppercase tracking-widest">● Secure Cluster</p>
                </div>
            </div>
            <button onclick="toggleMenu()" class="w-10 h-10 glass flex items-center justify-center text-blue-500"><i class="fa-solid fa-bars-staggered"></i></button>
        </div>

        <div id="p-home" class="page active-page">
            <div class="glass p-8 bg-gradient-to-br from-blue-600/10 to-transparent mb-6 border-blue-500/10">
                <p class="text-[8px] font-black opacity-40 uppercase tracking-[0.2em] mb-1">Portfolio Liquidity</p>
                <h3 class="text-4xl font-black italic tracking-tighter mb-8" id="v-bal">₨ 0.00</h3>
                <div class="flex gap-2">
                    <button onclick="claimDaily()" class="flex-1 bg-white text-black py-3.5 rounded-2xl font-black text-[9px] uppercase">Claim Gains</button>
                    <button onclick="changePage('finance')" class="flex-1 glass py-3.5 rounded-2xl font-black text-[9px] uppercase">Finance</button>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-3 mb-8 text-center">
                <div class="glass p-4"><p class="text-[7px] opacity-40 uppercase font-black">Monthly Salary</p><p id="v-salary" class="text-xs font-black text-blue-400">₨ 0</p></div>
                <div class="glass p-4"><p class="text-[7px] opacity-40 uppercase font-black">Referral Bonus</p><p id="v-bonus" class="text-xs font-black text-green-400">₨ 0</p></div>
            </div>

            <h4 class="text-[9px] font-black uppercase opacity-30 mb-4 tracking-[0.3em]">Quantum Mining Clusters</h4>
            <div id="plans-grid" class="space-y-4 pb-8"></div>
        </div>

        <div id="p-spin" class="page text-center">
            <h2 class="text-2xl font-black italic mb-2 uppercase">Extraction Terminal</h2>
            <p class="text-[9px] text-blue-500 uppercase font-bold italic mb-10 tracking-[0.3em]">Institutional Extractions</p>
            <div class="relative inline-block mb-10">
                <div class="absolute -top-6 left-1/2 -translate-x-1/2 w-8 h-10 bg-red-600 z-50 shadow-xl" style="clip-path: polygon(50% 100%, 0 0, 100% 0);"></div>
                <img id="wheel" src="IMG_20260515_012434.jpg" alt="Spin">
            </div>
            <div class="max-w-xs mx-auto space-y-3">
                <button id="spin-btn" onclick="executeSpin()" class="w-full bg-blue-600 py-4 rounded-3xl font-black text-xs uppercase shadow-xl italic">Execute Cycle</button>
                <button onclick="buySpins()" class="w-full glass py-3 rounded-3xl font-black text-[9px] uppercase border-blue-500/10">Buy 2 Spins (₨ 20)</button>
                <div id="spin-timer" class="text-[8px] font-black text-blue-400 uppercase tracking-widest">READY</div>
            </div>
        </div>

        <div id="p-finance" class="page">
            <h2 class="text-2xl font-black italic mb-6 uppercase">Banking</h2>
            <div class="flex gap-2 mb-6">
                <button onclick="toggleFinance('dep')" id="btn-dep" class="flex-1 py-3 rounded-xl bg-blue-600 font-black text-[10px] uppercase">Deposit</button>
                <button onclick="toggleFinance('wd')" id="btn-wd" class="flex-1 py-3 rounded-xl glass font-black text-[10px] uppercase text-white/40">Withdraw</button>
            </div>
            <div id="fin-form" class="glass p-6 mb-8"></div>
            <h3 class="text-[9px] font-black uppercase opacity-30 mb-4 tracking-[0.3em]">Recent Transactions</h3>
            <div id="fin-history" class="space-y-3 pb-10"></div>
        </div>

        <div id="p-company" class="page">
            <h2 class="text-2xl font-black italic mb-6 uppercase text-blue-500">Corporate Intel</h2>
            <div class="glass p-7 space-y-6 text-[11px] leading-relaxed opacity-80">
                <div class="border-l-2 border-blue-500 pl-4"><h5 class="font-black uppercase mb-1">Entity</h5><p>Vestify Elite Quantum Institutional LLC</p></div>
                <div class="border-l-2 border-blue-500 pl-4"><h5 class="font-black uppercase mb-1">HQ Location</h5><p>8 Marina View, Asia Square Tower 1, Singapore 018960</p></div>
                <div class="border-l-2 border-blue-500 pl-4"><h5 class="font-black uppercase mb-1">Regulatory</h5><p>MAS FinTech Sandbox Compliance UEN: 2026150522L</p></div>
                <p>Vestify Elite uses high-frequency cross-border liquidity clusters to generate yield for institutional and retail partners. We specialize in digital asset extraction and portfolio automation.</p>
            </div>
        </div>

        <div id="p-team" class="page">
            <h2 class="text-2xl font-black italic mb-6 uppercase">Team Network</h2>
            <div class="glass p-6 mb-6">
                <h4 class="text-[9px] font-black uppercase mb-4 opacity-40">Monthly Targets</h4>
                <div class="space-y-4 text-[10px] font-bold">
                    <div class="flex justify-between"><span>5 Active Referrals</span><span class="text-green-500">₨ 2,000/mo</span></div>
                    <div class="flex justify-between"><span>20 Active Referrals</span><span class="text-green-500">₨ 10,000/mo</span></div>
                </div>
            </div>
            <div class="glass p-6 text-center">
                <p class="text-[8px] opacity-30 uppercase font-black mb-3">Referral Link</p>
                <input id="ref-link" readonly class="w-full bg-black/50 p-3.5 rounded-xl text-[10px] mb-4 text-center">
                <button onclick="copyRef()" class="w-full bg-blue-600 py-3.5 rounded-xl font-black text-[10px] uppercase">Copy Network Link</button>
            </div>
        </div>

    </main>

    <div id="admin-ui" class="hidden fixed inset-0 z-[10000] bg-[#010409] p-8 overflow-y-auto">
        <div class="flex justify-between items-center mb-10 text-blue-500">
            <h2 class="text-xl font-black uppercase italic">Master Console</h2>
            <button onclick="closeAdmin()" class="text-4xl">&times;</button>
        </div>
        <div class="space-y-6" id="admin-content">
             <div class="glass p-6"><h4 class="text-[10px] font-black uppercase opacity-40 mb-4">Pending Requests</h4><div id="admin-pending-list" class="space-y-4"></div></div>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden fixed bottom-6 left-6 right-6 h-18 glass flex justify-around items-center z-[7000] px-4">
        <button onclick="changePage('home')" class="nav-item p-4"><i class="fa-solid fa-layer-group"></i></button>
        <button onclick="changePage('spin')" class="nav-item p-4"><i class="fa-solid fa-dharmachakra"></i></button>
        <button onclick="changePage('finance')" class="nav-item p-4"><i class="fa-solid fa-wallet"></i></button>
        <button onclick="changePage('team')" class="nav-item p-4"><i class="fa-solid fa-users-viewfinder"></i></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyC9ofJ1KxRXHnxilpU9gyI87D3BSOZ9v1g", authDomain: "vestify-991f2.firebaseapp.com", projectId: "vestify-991f2", storageBucket: "vestify-991f2.firebasestorage.app", messagingSenderId: "799007097733", appId: "1:799007097733:web:ed3b35b6c4e51dc2e7baec" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        const auth = firebase.auth();
        const provider = new firebase.auth.GoogleAuthProvider();

        let userObj = null, spinActive = false, rot = 0, tapCount = 0, b64 = "";

        const SPIN_WINS = { 0: 100, 45: 50, 90: 10, 135: 5, 180: 2, 225: 10, 270: 2, 315: 1 };

        window.onload = () => {
            setTimeout(() => {
                document.getElementById('loader').style.display = 'none';
                auth.onAuthStateChanged(u => u ? initApp(u) : document.getElementById('auth-ui').classList.remove('hidden'));
            }, 1500);
        };

        async function login() { await auth.signInWithPopup(provider); }
        function logout() { auth.signOut().then(() => location.reload()); }

        function initApp(u) {
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            document.getElementById('user-photo').src = u.photoURL;
            document.getElementById('user-name').innerText = u.displayName;
            document.getElementById('ref-link').value = `https://vestify.sg/node?ref=${u.displayName.split(' ')[0]}`;
            syncUser(u.displayName);
            renderPlans();
            toggleFinance('dep');
            setInterval(updateSpinTimer, 1000);
        }

        function syncUser(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                userObj = doc.data() || { name: name, balance: 0, lastSpin: 0, extraSpins: 0, salary: 0, bonus: 0, team: 0 };
                document.getElementById('v-bal').innerText = "₨ " + userObj.balance.toLocaleString();
                document.getElementById('v-salary').innerText = "₨ " + (userObj.salary || 0);
                document.getElementById('v-bonus').innerText = "₨ " + (userObj.bonus || 0);
                loadHistory();
            });
        }

        function renderPlans() {
            let h = '';
            for(let i=1; i<=20; i++) {
                const cost = i * 1500;
                const daily = Math.floor(cost * 0.12);
                h += `
                <div class="glass p-6 flex justify-between items-center bg-white/[0.02]">
                    <div><h5 class="font-black text-[10px] uppercase mb-1">Quantum Cluster v.${i}</h5><p class="text-[8px] font-bold text-green-500 uppercase">₨ ${daily}/Day ● 30 Days</p></div>
                    <div class="text-right"><p class="text-xs font-black mb-3">₨ ${cost}</p><button class="bg-white text-black px-4 py-1.5 rounded-xl font-black text-[8px] uppercase">Activate</button></div>
                </div>`;
            }
            document.getElementById('plans-grid').innerHTML = h;
        }

        // FINANCE SYSTEM
        function toggleFinance(t) {
            const f = document.getElementById('fin-form');
            const isDep = t === 'dep';
            document.getElementById('btn-dep').className = isDep ? "flex-1 py-3 rounded-xl bg-blue-600 font-black text-[10px] uppercase shadow-lg" : "flex-1 py-3 rounded-xl glass font-black text-[10px] uppercase text-white/40";
            document.getElementById('btn-wd').className = !isDep ? "flex-1 py-3 rounded-xl bg-red-600 font-black text-[10px] uppercase shadow-lg" : "flex-1 py-3 rounded-xl glass font-black text-[10px] uppercase text-white/40";
            
            f.innerHTML = isDep ? `
                <div class="grid grid-cols-3 gap-2 mb-6 text-[9px] font-black uppercase tracking-widest">
                    <button onclick="selM('Easypaisa','03379827882')" class="bg-white/5 py-3 rounded-xl border border-white/5 focus:border-blue-500">Easypaisa</button>
                    <button onclick="selM('JazzCash','03705519562')" class="bg-white/5 py-3 rounded-xl border border-white/5 focus:border-blue-500">JazzCash</button>
                    <button onclick="selM('SadaPay','03705519562')" class="bg-white/5 py-3 rounded-xl border border-white/5 focus:border-blue-500">SadaPay</button>
                </div>
                <div id="m-info" class="hidden glass bg-blue-600/5 p-4 mb-6 text-center">
                    <p id="m-name" class="text-[9px] font-black uppercase text-blue-400 mb-1"></p>
                    <p id="m-num" class="text-lg font-black tracking-widest mb-1"></p>
                    <p class="text-[7px] opacity-40">Copy and transfer amount</p>
                </div>
                <div class="space-y-4">
                    <input id="d-amt" type="number" placeholder="Amount (PKR)" class="w-full bg-black/40 p-4 rounded-xl text-xs outline-none border border-white/5">
                    <input id="d-tid" type="text" placeholder="Transaction ID (TID)" class="w-full bg-black/40 p-4 rounded-xl text-xs outline-none border border-white/5">
                    <label class="block bg-white/5 p-4 rounded-xl text-xs border border-dashed border-white/20 cursor-pointer text-center">
                        <span id="p-label">Upload Payment Screenshot</span>
                        <input type="file" class="hidden" onchange="hImg(event)">
                    </label>
                    <button onclick="subDep()" class="w-full bg-blue-600 py-4 rounded-xl font-black text-[10px] uppercase shadow-xl">Submit Deposit</button>
                </div>` : `
                <div class="space-y-4">
                    <select id="w-method" class="w-full bg-black/40 p-4 rounded-xl text-xs outline-none border border-white/5 appearance-none">
                        <option value="">Select Method</option>
                        <option>Easypaisa</option><option>JazzCash</option><option>SadaPay</option><option>Bank Transfer</option>
                    </select>
                    <input id="w-name" type="text" placeholder="Account Holder Name" class="w-full bg-black/40 p-4 rounded-xl text-xs outline-none border border-white/5">
                    <input id="w-num" type="text" placeholder="Account Number" class="w-full bg-black/40 p-4 rounded-xl text-xs outline-none border border-white/5">
                    <input id="w-amt" type="number" placeholder="Withdrawal Amount" class="w-full bg-black/40 p-4 rounded-xl text-xs outline-none border border-white/5">
                    <button onclick="subWd()" class="w-full bg-red-600 py-4 rounded-xl font-black text-[10px] uppercase">Request Withdrawal</button>
                </div>`;
        }

        function selM(n, num) { document.getElementById('m-info').classList.remove('hidden'); document.getElementById('m-name').innerText = n; document.getElementById('m-num').innerText = num; }
        
        function hImg(e) {
            const f = e.target.files[0];
            document.getElementById('p-label').innerText = f.name;
            const r = new FileReader();
            r.onloadend = () => b64 = r.result;
            r.readAsDataURL(f);
        }

        async function subDep() {
            const a = document.getElementById('d-amt').value, t = document.getElementById('d-tid').value;
            if(!a || !t || !b64) return alert("Fill all details & upload proof!");
            await db.collection("txs").add({ user: userObj.name, type: "Deposit", amount: parseInt(a), tid: t, proof: b64, status: "Pending", time: Date.now() });
            alert("Deposit Request Submitted!"); location.reload();
        }

        async function subWd() {
            const m = document.getElementById('w-method').value, a = document.getElementById('w-amt').value, n = document.getElementById('w-name').value, num = document.getElementById('w-num').value;
            if(!m || !a || !n || !num) return alert("Fill all fields!");
            if(a > userObj.balance) return alert("Insufficient Liquidity!");
            await db.collection("txs").add({ user: userObj.name, type: "Withdrawal", method: m, name: n, acc: num, amount: parseInt(a), status: "Pending", time: Date.now() });
            alert("Withdrawal Requested!"); location.reload();
        }

        function loadHistory() {
            db.collection("txs").where("user", "==", userObj.name).orderBy("time", "desc").limit(10).onSnapshot(s => {
                let h = "";
                s.forEach(doc => {
                    const d = doc.data();
                    h += `<div class="glass p-4 flex justify-between items-center border-l-4 ${d.status === 'Pending' ? 'border-yellow-500' : 'border-green-500'}">
                        <div><p class="text-[9px] font-black uppercase">${d.type}</p><p class="text-[7px] opacity-40">${new Date(d.time).toLocaleDateString()}</p></div>
                        <div class="text-right"><p class="text-[10px] font-black">₨ ${d.amount}</p><span class="status-badge ${d.status === 'Pending' ? 'text-yellow-500' : 'text-green-500'}">${d.status}</span></div>
                    </div>`;
                });
                document.getElementById('fin-history').innerHTML = h || '<p class="text-center opacity-20 py-10 text-[9px]">No logs found</p>';
            });
        }

        // SPIN LOGIC
        async function executeSpin() {
            if(spinActive) return;
            const free = (Date.now() - (userObj.lastSpin || 0)) >= 86400000;
            if(!free && (userObj.extraSpins || 0) <= 0) return alert("Next cycle in 24h!");

            spinActive = true;
            const degs = Object.keys(SPIN_WINS);
            const stop = parseInt(degs[Math.floor(Math.random()*degs.length)]);
            rot += (3600 + stop - (rot % 360));
            document.getElementById('wheel').style.transform = `rotate(${rot}deg)`;

            setTimeout(async () => {
                spinActive = false;
                const win = SPIN_WINS[stop];
                const up = { balance: userObj.balance + win };
                if(free) up.lastSpin = Date.now(); else up.extraSpins = userObj.extraSpins - 1;
                await db.collection("users").doc(userObj.name).update(up);
                alert(`Extraction Complete: ₨ ${win}`);
            }, 5500);
        }

        function updateSpinTimer() {
            if(!userObj) return;
            const diff = Date.now() - (userObj.lastSpin || 0);
            const el = document.getElementById('spin-timer');
            if(diff < 86400000 && (userObj.extraSpins || 0) <= 0) {
                const rem = 86400000 - diff;
                const h = Math.floor(rem/3600000), m = Math.floor((rem%3600000)/60000);
                el.innerText = `LOCKED: ${h}H ${m}M`;
            } else { el.innerText = userObj.extraSpins > 0 ? `${userObj.extraSpins} ELITE SPINS READY` : "FREE CYCLE READY"; }
        }

        async function buySpins() {
            if(userObj.balance < 20) return alert("Low Liquidity!");
            await db.collection("users").doc(userObj.name).update({ balance: userObj.balance - 20, extraSpins: (userObj.extraSpins || 0) + 2 });
            alert("2 Extra Extractions added!");
        }

        // UTILS
        function changePage(p) {
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.getElementById('p-'+p).classList.add('active-page');
            document.getElementById('side-menu').classList.remove('sidebar-open');
        }
        function toggleMenu() { document.getElementById('side-menu').classList.toggle('sidebar-open'); }
        function handleLogoTap() { tapCount++; if(tapCount >= 5) { const k = prompt("Master Key:"); if(k === "net204") loadAdmin(); tapCount = 0; } }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }
        function copyRef() { navigator.clipboard.writeText(document.getElementById('ref-link').value); alert("Network link copied!"); }

        function loadAdmin() {
            document.getElementById('admin-ui').classList.remove('hidden');
            db.collection("txs").where("status", "==", "Pending").onSnapshot(s => {
                let h = "";
                s.forEach(doc => {
                    const d = doc.data();
                    h += `<div class="glass p-5 border border-white/5">
                        <p class="text-[8px] font-black text-blue-400 mb-2">${d.user} | ${d.type}</p>
                        <p class="text-xs font-black">₨ ${d.amount}</p>
                        <p class="text-[9px] opacity-40 mb-3">${d.tid || d.method + ' ' + d.acc}</p>
                        ${d.proof ? `<img src="${d.proof}" class="w-20 rounded mb-3" onclick="window.open(this.src)">` : ''}
                        <div class="flex gap-2">
                            <button onclick="approve('${doc.id}', '${d.user}', ${d.amount}, '${d.type}')" class="bg-green-600 px-4 py-2 rounded text-[8px] font-black uppercase">Approve</button>
                            <button onclick="reject('${doc.id}')" class="bg-red-600 px-4 py-2 rounded text-[8px] font-black uppercase">Reject</button>
                        </div>
                    </div>`;
                });
                document.getElementById('admin-pending-list').innerHTML = h || '<p class="text-center opacity-20 py-10 text-[9px]">CLEAN JURISDICTION</p>';
            });
        }

        async function approve(id, user, amt, type) {
            await db.collection("txs").doc(id).update({ status: "Approved" });
            if(type === "Deposit") {
                await db.collection("users").doc(user).update({ balance: firebase.firestore.FieldValue.increment(amt) });
            } else {
                // Withdrawal amount is usually deducted on request, but you can adjust logic here
                await db.collection("users").doc(user).update({ balance: firebase.firestore.FieldValue.increment(-amt) });
            }
            alert("Transaction Validated!");
        }
        async function reject(id) { await db.collection("txs").doc(id).update({ status: "Rejected" }); alert("Transaction Voided!"); }
    </script>
</body>
</html>
