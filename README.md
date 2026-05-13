<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>Vestify | Elite Asset Vault</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #F8FAFC; color: #1E293B; }
        .vibrant-bg { background: linear-gradient(-45deg, #F0F9FF, #FAF5FF, #F0FDF4, #FFFBEB); background-size: 400% 400%; animation: gradientBG 15s ease infinite; }
        @keyframes gradientBG { 0% { background-position: 0% 50%; } 50% { background-position: 100% 50%; } 100% { background-position: 0% 50%; } }
        .glass-light { background: rgba(255, 255, 255, 0.7); backdrop-filter: blur(15px); border: 1px solid rgba(255, 255, 255, 0.5); box-shadow: 0 10px 30px -10px rgba(0,0,0,0.05); }
        .card-blue { background: linear-gradient(135deg, #2563EB 0%, #1D4ED8 100%); color: white; box-shadow: 0 20px 40px -10px rgba(37, 99, 235, 0.3); }
        .active-tab { color: #2563EB; font-weight: 800; transform: translateY(-3px); }
        .page { display: none; animation: slideUp 0.4s cubic-bezier(0.16, 1, 0.3, 1); }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        .badge-pending { background: #FEF3C7; color: #92400E; padding: 2px 8px; border-radius: 8px; font-size: 8px; font-weight: bold; }
        .badge-approved { background: #DCFCE7; color: #166534; padding: 2px 8px; border-radius: 8px; font-size: 8px; font-weight: bold; }
    </style>
</head>
<body class="h-screen flex flex-col vibrant-bg">

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-white flex items-center justify-center p-8 text-center">
        <div class="w-full max-w-sm">
            <h1 class="text-4xl font-black italic tracking-tighter mb-2 uppercase text-slate-800">VEST<span class="text-blue-600">IFY</span></h1>
            <p class="text-slate-400 text-[8px] uppercase tracking-[0.4em] mb-12 font-bold italic">Official Digital Asset Vault</p>
            <div class="glass-light p-10 rounded-[3rem] border-t-4 border-blue-600">
                <button onclick="loginWithGoogle()" class="w-full bg-white text-slate-800 py-5 rounded-2xl font-black text-[10px] uppercase tracking-widest active:scale-95 shadow-xl border border-slate-200 flex items-center justify-center gap-3">
                    <img src="https://www.gstatic.com/firebasejs/ui/2.0.0/images/action/google.svg" width="18">
                    Continue with Google
                </button>
            </div>
            <p class="mt-8 text-[7px] text-slate-400 uppercase font-bold tracking-widest">© 2026 Vestify Finance Group</p>
        </div>
    </section>

    <header class="p-6 flex justify-between items-center sticky top-0 z-[500] bg-white/60 backdrop-blur-md border-b border-gray-200/50">
        <h1 class="text-xl font-black italic tracking-tighter uppercase text-slate-800">VEST<span class="text-blue-600 text-2xl">IFY</span></h1>
        <div onclick="adminTap()" class="bg-blue-100 p-2.5 rounded-xl border border-blue-200 active:scale-90 transition-all">
            <i class="fa-solid fa-fingerprint text-blue-600 text-lg"></i>
        </div>
    </header>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        <div id="p-home" class="page active-page p-6">
            <div class="card-blue p-8 rounded-[2.5rem] mb-6 relative overflow-hidden">
                <p class="text-[10px] opacity-80 font-black mb-1 uppercase tracking-widest">Active Balance</p>
                <h2 class="text-5xl font-black tracking-tighter mb-2" id="v-bal">₨ 0</h2>
                <div id="countdown-display" class="text-[9px] font-black uppercase text-blue-200 italic mb-6">Secure System Active</div>
                <div class="flex items-center justify-between border-t border-white/20 pt-6">
                    <div>
                        <p class="text-[8px] opacity-70 font-black uppercase">Net Profit</p>
                        <p id="v-profit" class="text-lg font-black text-white">₨ 0</p>
                    </div>
                    <div class="text-right">
                        <p class="text-[8px] opacity-70 font-black uppercase tracking-tighter">Current Plan</p>
                        <p id="tier-tag" class="text-[10px] font-black uppercase italic text-blue-100">None</p>
                    </div>
                </div>
            </div>
            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass-light p-6 rounded-[2rem] flex flex-col items-center gap-2 border-b-4 border-blue-500 active:scale-95 transition-all">
                    <i class="fa-solid fa-plus text-blue-600"></i>
                    <span class="text-[9px] font-black uppercase text-slate-600">Deposit</span>
                </button>
                <button onclick="changePage('withdraw')" class="glass-light p-6 rounded-[2rem] flex flex-col items-center gap-2 border-b-4 border-rose-500 active:scale-95 transition-all">
                    <i class="fa-solid fa-paper-plane text-rose-600"></i>
                    <span class="text-[9px] font-black uppercase text-slate-600">Withdraw</span>
                </button>
            </div>
        </div>
        
        </main>

    <nav id="bottom-nav" class="hidden bg-white/80 backdrop-blur-xl border-t border-slate-100 p-6 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[3.5rem] shadow-[0_-10px_40px_rgba(0,0,0,0.03)]">
        <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center transition-all active-tab"><i class="fa-solid fa-house-user text-xl"></i><span class="text-[8px] font-black uppercase mt-1">Vault</span></button>
        <button onclick="changePage('invest')" id="n-invest" class="flex flex-col items-center transition-all opacity-40"><i class="fa-solid fa-chart-line text-xl"></i><span class="text-[8px] font-black uppercase mt-1">Growth</span></button>
        <button onclick="changePage('activity')" id="n-activity" class="flex flex-col items-center transition-all opacity-40"><i class="fa-solid fa-receipt text-xl"></i><span class="text-[8px] font-black uppercase mt-1">Ledger</span></button>
        <button onclick="changePage('more')" id="n-more" class="flex flex-col items-center transition-all opacity-40"><i class="fa-solid fa-circle-info text-xl"></i><span class="text-[8px] font-black uppercase mt-1">Info</span></button>
    </nav>

    <script>
        // Aapka naya Vestify Config
        const firebaseConfig = {
            apiKey: "AIzaSyC9ofJ1KxRXHnxilpU9gyI87D3BSOZ9v1g",
            authDomain: "vestify-991f2.firebaseapp.com",
            databaseURL: "https://vestify-991f2-default-rtdb.firebaseio.com",
            projectId: "vestify-991f2",
            storageBucket: "vestify-991f2.firebasestorage.app",
            messagingSenderId: "799007097733",
            appId: "1:799007097733:web:ed3b35b6c4e51dc2e7baec"
        };
        
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        const auth = firebase.auth();
        const provider = new firebase.auth.GoogleAuthProvider();

        let user = null;

        // Login with Google Function
        async function loginWithGoogle() {
            try {
                const result = await auth.signInWithPopup(provider);
                const gUser = result.user;
                const name = gUser.displayName;

                const ref = db.collection("users").doc(name);
                const d = await ref.get();
                
                if(!d.exists) {
                    await ref.set({ 
                        name: name, 
                        email: gUser.email, 
                        balance: 0, 
                        profit: 0, 
                        time: Date.now(), 
                        activeTier: 0, 
                        tierROI: 0, 
                        lastReqTime: Date.now(), 
                        tierName: "Inactive" 
                    });
                }
                
                startApp(name);
            } catch (e) {
                alert("Login Failed! Please check your Google account.");
            }
        }

        function startApp(name) {
            localStorage.setItem('mc_user', name);
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            startSync(name);
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { 
                if(doc.exists) { 
                    user = doc.data(); 
                    document.getElementById('v-bal').innerText = "₨ " + (user.balance||0).toLocaleString(); 
                    document.getElementById('v-profit').innerText = "₨ " + (user.profit||0).toLocaleString(); 
                    document.getElementById('tier-tag').innerText = user.tierName; 
                } 
            });
        }

        function changePage(p) {
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.querySelectorAll('nav button').forEach(b => {
                b.classList.add('opacity-40');
                b.classList.remove('active-tab');
            });
            document.getElementById('p-' + p).classList.add('active-page');
            document.getElementById('n-' + p).classList.remove('opacity-40');
            document.getElementById('n-' + p).classList.add('active-tab');
        }

        // Auto-login if session exists
        window.onload = () => {
            const saved = localStorage.getItem('mc_user');
            if(saved) startApp(saved);
        };
    </script>
</body>
</html>
