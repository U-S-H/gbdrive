<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>Vestify Elite | Terminal</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --primary: #2563eb; --bg: #f8fafc; --card: #ffffff; --text: #0f172a; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background-color: var(--bg); color: var(--text); overflow-x: hidden; }
        .glass { background: var(--card); border: 1px solid rgba(0,0,0,0.04); border-radius: 30px; box-shadow: 0 10px 30px -10px rgba(0,0,0,0.05); }
        .hero-card { background: linear-gradient(135deg, #0f172a 0%, #1e293b 100%); border-radius: 40px; padding: 2rem; color: white; margin-bottom: 2rem; }
        .page { display: none; animation: slideIn 0.4s ease-out; }
        .active-page { display: block; }
        @keyframes slideIn { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        #wheel-container { position: relative; width: 300px; height: 300px; margin: 0 auto; }
        #wheel { width: 100%; height: 100%; transition: transform 4s cubic-bezier(0.15, 0, 0.15, 1); border-radius: 50%; }
        .pointer { position: absolute; top: -10px; left: 50%; transform: translateX(-50%); width: 0; height: 0; border-left: 15px solid transparent; border-right: 15px solid transparent; border-top: 30px solid #ef4444; z-index: 20; }
    </style>
</head>
<body class="min-h-screen pb-32">

    <section id="auth-ui" class="fixed inset-0 z-[5000] bg-white flex flex-col items-center justify-center p-10">
        <div class="w-full max-w-sm text-center">
            <div class="w-20 h-20 bg-blue-600 rounded-[2rem] flex items-center justify-center mx-auto mb-8 shadow-2xl rotate-3">
                <i class="fa-solid fa-vault text-white text-3xl"></i>
            </div>
            <h1 class="text-4xl font-black italic tracking-tighter uppercase">VESTIFY ELITE</h1>
            <button onclick="loginWithGoogle()" class="w-full mt-10 bg-[#0f172a] text-white py-5 rounded-3xl font-bold active:scale-95 transition-all">Google Sync</button>
        </div>
    </section>

    <main id="app-ui" class="hidden px-6 pt-6">
        <div id="p-home" class="page active-page">
            <div class="hero-card shadow-2xl">
                <p class="text-[10px] opacity-50 uppercase text-center mb-1">Portfolio Balance</p>
                <h2 class="text-4xl font-extrabold text-center mb-10" id="v-bal">₨ 0.00</h2>
            </div>
            <div id="plans-list" class="space-y-4"></div>
        </div>

        <div id="p-spin" class="page text-center">
            <h2 class="text-2xl font-black mb-10">Lucky Terminal</h2>
            <div id="wheel-container" class="mb-12">
                <div class="pointer"></div>
                <img id="wheel" src="IMG_20260513_123757.jpg" alt="Wheel">
            </div>
            <button onclick="handleSpin()" class="w-full bg-blue-600 text-white py-5 rounded-3xl font-black uppercase text-xs tracking-widest shadow-xl active:scale-95 transition-all">Spin Protocol</button>
        </div>
    </main>

    <nav id="bottom-nav" class="hidden fixed bottom-6 left-6 right-6 h-20 glass flex justify-around items-center z-[3000]">
        <button onclick="changePage('home')" id="n-home" class="text-blue-600 flex flex-col items-center"><i class="fa-solid fa-house-chimney text-lg"></i><span class="text-[8px] font-bold uppercase">Vault</span></button>
        <button onclick="changePage('spin')" id="n-spin" class="text-slate-400 flex flex-col items-center"><i class="fa-solid fa-dharmachakra text-lg"></i><span class="text-[8px] font-bold uppercase">Games</span></button>
    </nav>

    <script>
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
        let isSpinning = false;
        let totalRotation = 0;

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
            renderPlans();
        }

        function syncData(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                userObj = doc.data();
                if(userObj) document.getElementById('v-bal').innerText = "₨ " + (userObj.balance||0).toLocaleString();
            });
        }

        function changePage(p) {
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.querySelectorAll('nav button').forEach(b => b.classList.replace('text-blue-600', 'text-slate-400'));
            document.getElementById('p-'+p).classList.add('active-page');
            document.getElementById('n-'+p).classList.replace('text-slate-400', 'text-blue-600');
        }

        async function handleSpin() {
            if (isSpinning) return;
            isSpinning = true;

            // Aapki image ke mutabiq 6 segments ka logic
            const rewards = [
                { label: "Rs 5", value: 5, angle: 0 },
                { label: "Rs 10", value: 10, angle: 300 },
                { label: "Rs 50", value: 50, angle: 240 },
                { label: "Rs 2", value: 2, angle: 180 },
                { label: "Try Again", value: 0, angle: 120 },
                { label: "Try Again", value: 0, angle: 60 }
            ];

            const prize = rewards[Math.floor(Math.random() * rewards.length)];
            const spins = (Math.floor(Math.random() * 5) + 5) * 360; 
            totalRotation += spins + prize.angle - (totalRotation % 360);

            const wheel = document.getElementById('wheel');
            wheel.style.transform = `rotate(${totalRotation}deg)`;

            setTimeout(async () => {
                isSpinning = false;
                if(prize.value > 0) {
                    await db.collection("users").doc(userObj.name).update({
                        balance: (userObj.balance || 0) + prize.value
                    });
                    alert(`Mubarak ho sweetie! Aapne ${prize.label} jeet liye!`);
                } else {
                    alert("Oh ho! Try Again!");
                }
            }, 4000);
        }

        function renderPlans() {
            const ps = [{ n: "Standard Node", p: 500 }];
            document.getElementById('plans-list').innerHTML = ps.map(p => `
                <div class="glass p-6 flex justify-between items-center">
                    <p class="font-bold text-xs uppercase">${p.n}</p>
                    <button class="px-5 py-2 bg-slate-900 text-white text-[8px] font-bold rounded-xl uppercase">₨ ${p.p}</button>
                </div>`).join('');
        }

        window.onload = () => { if(localStorage.getItem('v_user')) initApp(localStorage.getItem('v_user')); };
    </script>
</body>
</html>
