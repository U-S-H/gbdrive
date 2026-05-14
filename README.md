<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>Vestify Elite | Quantum Institutional LLC</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --primary: #3b82f6; --bg: #020617; --card: #0f172a; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: #f8fafc; overflow-x: hidden; }
        .glass { background: rgba(15, 23, 42, 0.7); backdrop-filter: blur(20px); border: 1px solid rgba(255,255,255,0.05); border-radius: 32px; }
        .loader-bg { background: #020617; z-index: 99999; }
        .page { display: none; animation: slideUp 0.5s cubic-bezier(0.16, 1, 0.3, 1); }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        .nav-item.active { color: #3b82f6; background: rgba(59, 130, 246, 0.1); border-radius: 20px; }
        .shimmer { background: linear-gradient(90deg, transparent, rgba(255,255,255,0.05), transparent); background-size: 200% 100%; animation: shimmer 2s infinite; }
        @keyframes shimmer { 0% { background-position: -200% 0; } 100% { background-position: 200% 0; } }
        #wheel { width: 280px; height: 280px; transition: transform 4s cubic-bezier(0.15, 0, 0.15, 1); border-radius: 50%; border: 8px solid rgba(255,255,255,0.05); }
    </style>
</head>
<body class="min-h-screen pb-32">

    <div id="global-loader" class="loader-bg fixed inset-0 flex flex-col items-center justify-center">
        <div class="w-16 h-16 border-4 border-blue-500/20 border-t-blue-500 rounded-full animate-spin"></div>
        <p class="mt-4 text-[10px] font-black uppercase tracking-[0.3em] text-blue-500">Initializing Quantum Protocol</p>
    </div>

    <section id="auth-ui" class="hidden fixed inset-0 z-[6000] bg-[#020617] flex flex-col items-center justify-center p-10">
        <div onclick="handleLogoTap()" class="w-24 h-24 bg-blue-600 rounded-[2.5rem] flex items-center justify-center mb-8 rotate-3 shadow-[0_20px_50px_rgba(59,130,246,0.3)]">
            <i class="fa-solid fa-shield-halved text-white text-4xl"></i>
        </div>
        <h1 class="text-4xl font-black italic tracking-tighter uppercase mb-2">Vestify <span class="text-blue-500 underline decoration-blue-500/30">Elite</span></h1>
        <p class="text-[10px] opacity-30 uppercase tracking-[0.4em] mb-16">Global Asset Management</p>
        <button onclick="login()" class="w-full max-w-xs bg-white text-black py-5 rounded-[2rem] font-black uppercase text-xs tracking-widest active:scale-95 transition-all">Secure Login with Google</button>
    </section>

    <main id="app-ui" class="hidden pt-12 px-6">
        
        <div class="flex items-center justify-between mb-10">
            <div class="flex items-center gap-4">
                <img id="user-photo" class="w-12 h-12 rounded-2xl border border-white/10 shadow-xl">
                <div>
                    <h4 id="user-name" class="font-black text-sm uppercase italic">User</h4>
                    <div class="flex items-center gap-1">
                        <span class="w-2 h-2 bg-green-500 rounded-full animate-pulse"></span>
                        <p class="text-[8px] font-bold opacity-40 uppercase">Quantum Active</p>
                    </div>
                </div>
            </div>
            <button onclick="logout()" class="w-10 h-10 glass flex items-center justify-center text-red-500"><i class="fa-solid fa-power-off"></i></button>
        </div>

        <div id="p-home" class="page active-page">
            <div class="glass p-8 bg-gradient-to-br from-blue-600/10 to-transparent mb-6">
                <p class="text-[9px] font-black opacity-40 uppercase tracking-widest mb-1">Total Liquidity</p>
                <h2 class="text-5xl font-black italic tracking-tighter mb-4" id="v-bal">₨ 0.00</h2>
                <div class="flex gap-3">
                    <button onclick="claimProfit()" class="flex-1 bg-white text-black py-3 rounded-2xl font-black text-[10px] uppercase">Claim Hourly Profit</button>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-10">
                <div class="glass p-4"><p class="text-[8px] opacity-40 uppercase font-black">Daily Salary</p><p class="text-sm font-black text-blue-400" id="v-salary">₨ 0</p></div>
                <div class="glass p-4"><p class="text-[8px] opacity-40 uppercase font-black">Team Bonus</p><p class="text-sm font-black text-green-400" id="v-bonus">₨ 0</p></div>
            </div>

            <h3 class="font-black text-[10px] uppercase opacity-30 mb-4 tracking-[0.2em]">Active Nodes</h3>
            <div id="active-nodes" class="space-y-4"></div>
        </div>

        <div id="p-support" class="page">
            <h2 class="text-3xl font-black italic mb-6 uppercase">Help Desk</h2>
            <div id="chat-box" class="glass h-80 overflow-y-auto p-4 mb-4 flex flex-col gap-3">
                </div>
            <div class="flex gap-2">
                <input id="chat-msg" type="text" placeholder="Direct message to admin..." class="flex-1 bg-slate-900 rounded-2xl p-4 text-xs outline-none">
                <button onclick="sendMsg()" class="w-14 h-14 btn-primary rounded-2xl flex items-center justify-center"><i class="fa-solid fa-paper-plane"></i></button>
            </div>
        </div>

        <div id="p-spin" class="page text-center">
            <h2 class="text-3xl font-black italic mb-10 uppercase">Lucky Terminal</h2>
            <div class="relative inline-block mb-10">
                <div class="absolute -top-6 left-1/2 -translate-x-1/2 w-10 h-12 bg-red-600 z-50 shadow-xl" style="clip-path: polygon(50% 100%, 0 0, 100% 0);"></div>
                <img id="wheel" src="IMG_20260515_012434.jpg" alt="Spin">
            </div>
            <button id="spin-btn" onclick="executeSpin()" class="w-full max-w-xs mx-auto bg-blue-600 py-5 rounded-[2rem] font-black uppercase text-xs italic shadow-2xl">Execute extraction</button>
        </div>

    </main>

    <div id="admin-panel" class="hidden fixed inset-0 z-[20000] bg-[#020617] p-8 overflow-y-auto">
        <div class="flex justify-between items-center mb-10">
            <h2 class="text-2xl font-black italic text-blue-500 uppercase">Master Admin</h2>
            <button onclick="closeAdmin()" class="text-3xl">&times;</button>
        </div>
        <div class="space-y-6">
            <div class="glass p-6">
                <h4 class="text-xs font-black uppercase mb-4">Broadcast Reply</h4>
                <div id="admin-chat-list" class="space-y-3 mb-4"></div>
            </div>
            <div class="glass p-6">
                <h4 class="text-xs font-black uppercase mb-4">Global Controls</h4>
                <button onclick="toggleMaintenance()" class="w-full bg-red-600 py-4 rounded-xl font-black text-xs uppercase">Toggle Maintenance</button>
            </div>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden fixed bottom-6 left-6 right-6 h-20 glass flex justify-around items-center z-[4000]">
        <button onclick="changePage('home')" class="nav-item flex flex-col items-center p-3 active"><i class="fa-solid fa-home"></i></button>
        <button onclick="changePage('spin')" class="nav-item flex flex-col items-center p-3"><i class="fa-solid fa-dharmachakra"></i></button>
        <button onclick="changePage('support')" class="nav-item flex flex-col items-center p-3"><i class="fa-solid fa-headset"></i></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyC9ofJ1KxRXHnxilpU9gyI87D3BSOZ9v1g", authDomain: "vestify-991f2.firebaseapp.com", projectId: "vestify-991f2", storageBucket: "vestify-991f2.firebasestorage.app", messagingSenderId: "799007097733", appId: "1:799007097733:web:ed3b35b6c4e51dc2e7baec" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        const auth = firebase.auth();
        const provider = new firebase.auth.GoogleAuthProvider();

        let userObj = null, spinActive = false, rot = 0, tapCount = 0;

        // Init Sequence
        window.addEventListener('load', () => {
            setTimeout(() => {
                document.getElementById('global-loader').style.display = 'none';
                auth.onAuthStateChanged(u => u ? initApp(u) : document.getElementById('auth-ui').classList.remove('hidden'));
            }, 2000);
        });

        async function login() {
            await auth.signInWithPopup(provider);
        }

        function logout() {
            auth.signOut().then(() => location.reload());
        }

        function initApp(u) {
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            document.getElementById('user-photo').src = u.photoURL;
            document.getElementById('user-name').innerText = u.displayName;
            syncData(u.displayName);
            loadMessages();
        }

        function syncData(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                userObj = doc.data() || { name: name, balance: 0, salary: 0, bonus: 0, activePlans: [], isAdmin: false };
                document.getElementById('v-bal').innerText = "₨ " + userObj.balance.toLocaleString();
                document.getElementById('v-salary').innerText = "₨ " + (userObj.salary || 0);
                document.getElementById('v-bonus').innerText = "₨ " + (userObj.bonus || 0);
                renderUserPlans();
            });
        }

        async function sendMsg() {
            const m = document.getElementById('chat-msg').value;
            if(!m) return;
            await db.collection("support").add({
                user: userObj.name,
                msg: m,
                reply: "",
                timestamp: Date.now()
            });
            document.getElementById('chat-msg').value = "";
        }

        function loadMessages() {
            db.collection("support").where("user", "==", userObj.name).onSnapshot(snap => {
                let h = '';
                snap.forEach(doc => {
                    const d = doc.data();
                    h += `<div class="bg-blue-600/20 self-end p-3 rounded-2xl rounded-tr-none max-w-[80%] text-[11px]">${d.msg}</div>`;
                    if(d.reply) h += `<div class="bg-white/5 self-start p-3 rounded-2xl rounded-tl-none max-w-[80%] text-[11px]"><span class="text-blue-500 font-bold">Admin:</span> ${d.reply}</div>`;
                });
                document.getElementById('chat-box').innerHTML = h;
                document.getElementById('chat-box').scrollTop = document.getElementById('chat-box').scrollHeight;
            });
        }

        // --- Admin Functions ---
        function handleLogoTap() { 
            tapCount++; 
            if(tapCount >= 5) { 
                const p = prompt("Quantum Master Key:"); 
                if(p === "net204") { 
                    document.getElementById('admin-panel').classList.remove('hidden');
                    loadAdminData();
                } 
                tapCount = 0; 
            } 
        }

        function loadAdminData() {
            db.collection("support").where("reply", "==", "").onSnapshot(snap => {
                let h = '';
                snap.forEach(doc => {
                    h += `<div class="bg-white/5 p-4 rounded-xl">
                        <p class="text-[10px] font-bold text-blue-500">${doc.data().user}</p>
                        <p class="text-xs mb-3">${doc.data().msg}</p>
                        <div class="flex gap-2">
                            <input id="rep-${doc.id}" type="text" placeholder="Reply..." class="flex-1 bg-black p-2 rounded text-[10px]">
                            <button onclick="adminReply('${doc.id}')" class="bg-blue-600 px-3 rounded text-[10px]">SEND</button>
                            <button onclick="deleteMsg('${doc.id}')" class="text-red-500"><i class="fa-solid fa-trash"></i></button>
                        </div>
                    </div>`;
                });
                document.getElementById('admin-chat-list').innerHTML = h;
            });
        }

        async function adminReply(id) {
            const r = document.getElementById('rep-'+id).value;
            await db.collection("support").doc(id).update({ reply: r });
        }

        async function deleteMsg(id) {
            await db.collection("support").doc(id).delete();
        }

        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function changePage(p) {
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.getElementById('p-'+p).classList.add('active-page');
            document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('active'));
            event.currentTarget.classList.add('active');
        }

        // --- Basic Spin Logic (Refined) ---
        async function executeSpin() {
            if(spinActive) return;
            spinActive = true;
            const stop = Math.floor(Math.random()*360);
            rot += (3600 + stop - (rot%360));
            document.getElementById('wheel').style.transform = `rotate(${rot}deg)`;
            setTimeout(() => {
                spinActive = false;
                alert("Protocol Result Synced with Terminal.");
            }, 4000);
        }
    </script>
</body>
</html>
