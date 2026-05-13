<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>Vestify Elite | Professional Terminal</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background-color: #F9FAFB; color: #111827; }
        
        .bento-card { background: #FFFFFF; border: 1px solid #F3F4F6; border-radius: 24px; box-shadow: 0 4px 15px rgba(0,0,0,0.02); }
        .vip-diamond { background: linear-gradient(135deg, #0F172A 0%, #334155 100%); color: white; }
        
        /* Live Payout Ticker */
        @keyframes scroll-up { 0% { transform: translateY(100%); } 100% { transform: translateY(-100%); } }
        .payout-item { animation: slideUpFade 4s infinite; opacity: 0; position: absolute; width: 100%; }
        @keyframes slideUpFade { 
            0% { transform: translateY(20px); opacity: 0; }
            10% { transform: translateY(0); opacity: 1; }
            90% { transform: translateY(0); opacity: 1; }
            100% { transform: translateY(-20px); opacity: 0; }
        }

        .page { display: none; animation: fadeIn 0.4s ease; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
    </style>
</head>
<body class="min-h-screen flex flex-col">

    <div class="h-8 bg-blue-600 flex items-center justify-center overflow-hidden relative">
        <div id="payout-feed" class="text-[9px] font-bold text-white uppercase tracking-widest text-center w-full">
            </div>
    </div>

    <header class="p-6 flex justify-between items-center bg-white/80 backdrop-blur-md sticky top-0 z-[1000]">
        <div class="flex items-center gap-2">
            <div class="w-9 h-9 bg-blue-600 rounded-xl flex items-center justify-center shadow-lg shadow-blue-100 rotate-3">
                <i class="fa-solid fa-gem text-white text-sm"></i>
            </div>
            <span class="font-black text-xl italic tracking-tighter">VESTIFY</span>
        </div>
        <div id="vip-badge" class="px-3 py-1 bg-slate-100 text-[8px] font-black rounded-full uppercase border border-slate-200">Standard User</div>
    </header>

    <main id="app-ui" class="flex-1 overflow-y-auto pb-32 px-6 pt-4">
        
        <div id="p-home" class="page active-page">
            <div class="vip-diamond p-8 rounded-[2.5rem] mb-6 relative overflow-hidden shadow-2xl">
                <canvas id="balanceChart" class="absolute -right-4 -bottom-4 w-32 h-32 opacity-20"></canvas>
                <div class="relative z-10">
                    <p class="text-[9px] font-bold opacity-60 uppercase tracking-widest mb-1">Active Portfolio</p>
                    <h2 class="text-4xl font-extrabold tracking-tighter mb-10" id="v-bal">₨ 0.00</h2>
                    
                    <div class="flex justify-between items-end">
                        <div>
                            <p class="text-[8px] opacity-40 uppercase font-black">Daily Check-in</p>
                            <button onclick="dailyBonus()" id="bonus-btn" class="mt-1 px-4 py-2 bg-white/10 hover:bg-white/20 rounded-xl text-[10px] font-bold transition-all">Claim ₨ 2.00</button>
                        </div>
                        <div class="text-right">
                            <p class="text-[8px] opacity-40 uppercase font-black">Hourly Yield</p>
                            <p id="v-profit" class="text-lg font-bold text-emerald-400">₨ 0.0000</p>
                        </div>
                    </div>
                </div>
            </div>

            <div class="grid grid-cols-3 gap-3 mb-8">
                <div class="bento-card p-4 text-center">
                    <p class="text-[7px] font-bold text-slate-400 uppercase">Team Size</p>
                    <p class="text-sm font-black" id="team-size">0</p>
                </div>
                <div class="bento-card p-4 text-center">
                    <p class="text-[7px] font-bold text-slate-400 uppercase">Commish</p>
                    <p class="text-sm font-black text-blue-600" id="team-earn">₨ 0</p>
                </div>
                <div onclick="copyRef()" class="bento-card p-4 text-center active:scale-95 transition-all bg-blue-50">
                    <i class="fa-solid fa-share-nodes text-blue-600 text-xs"></i>
                    <p class="text-[7px] font-bold text-blue-600 uppercase mt-1">Invite</p>
                </div>
            </div>

            <h3 class="text-xs font-black uppercase tracking-widest text-slate-400 mb-6">Investment Terminal</h3>
            <div id="plans-list" class="space-y-6"></div>
        </div>
    </main>

    <nav id="bottom-nav" class="fixed bottom-6 left-6 right-6 h-20 bento-card flex justify-around items-center px-4 shadow-2xl z-[1000]">
        <button onclick="changePage('home')" id="n-home" class="text-blue-600 flex flex-col items-center gap-1"><i class="fa-solid fa-house text-xl"></i><span class="text-[8px] font-bold uppercase">Vault</span></button>
        <button onclick="changePage('refer')" class="text-slate-400 flex flex-col items-center gap-1"><i class="fa-solid fa-users text-xl"></i><span class="text-[8px] font-bold uppercase">Team</span></button>
        <button onclick="logout()" class="text-rose-400 flex flex-col items-center gap-1"><i class="fa-solid fa-power-off text-xl"></i><span class="text-[8px] font-bold uppercase">Exit</span></button>
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

        let userData = { balance: 0, profit: 0, tierROI: 0, teamSize: 0, totalTeamEarned: 0 };

        // 1. Live Payout Feed Simulator
        const payouts = [
            "Ali just withdrew ₨ 1,200 via Easypaisa",
            "M. Akbar upgraded to Diamond VIP",
            "Sara claimed team commission of ₨ 4,500",
            "System: Weekend Booster is now Active!"
        ];
        let pIndex = 0;
        setInterval(() => {
            const feed = document.getElementById('payout-feed');
            feed.innerHTML = `<div class="payout-item">${payouts[pIndex]}</div>`;
            pIndex = (pIndex + 1) % payouts.length;
        }, 4000);

        // 2. Chart Integration
        function initChart() {
            const ctx = document.getElementById('balanceChart').getContext('2d');
            new Chart(ctx, {
                type: 'doughnut',
                data: {
                    datasets: [{
                        data: [70, 30],
                        backgroundColor: ['#3B82F6', 'transparent'],
                        borderWidth: 0
                    }]
                },
                options: { cutout: '80%', events: [] }
            });
        }

        // 3. Daily Bonus Logic
        async function dailyBonus() {
            const btn = document.getElementById('bonus-btn');
            if(btn.innerText === "Claimed") return;
            userData.balance += 2;
            await db.collection("users").doc(userData.name).update({ balance: userData.balance });
            btn.innerText = "Claimed";
            btn.classList.add('opacity-50');
            alert("Daily Reward of ₨ 2.00 added to your vault!");
        }

        // 4. Referral Copy
        function copyRef() {
            const link = `https://vestify.pages.dev/?ref=${userData.name}`;
            navigator.clipboard.writeText(link);
            alert("Referral Link Copied! Send it to your team.");
        }

        // 5. VIP Badge Update
        function updateVIP(bal) {
            const badge = document.getElementById('vip-badge');
            if(bal > 100000) { badge.innerText = "Diamond VIP"; badge.classList.add('bg-blue-600', 'text-white'); }
            else if(bal > 10000) { badge.innerText = "Gold Member"; badge.classList.add('bg-amber-100', 'text-amber-600'); }
            else { badge.innerText = "Standard Member"; }
        }

        async function syncData(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                userData = doc.data();
                userData.name = name;
                document.getElementById('v-bal').innerText = "₨ " + (userData.balance||0).toLocaleString();
                document.getElementById('team-size').innerText = userData.teamSize || 0;
                document.getElementById('team-earn').innerText = "₨ " + (userData.totalTeamEarned || 0);
                updateVIP(userData.balance);
            });
        }

        window.onload = () => {
            const saved = localStorage.getItem('v_user');
            if(saved) {
                initChart();
                syncData(saved);
                renderPlans();
            }
        };

        function renderPlans() {
            const plans = [
                { n: "Quantum Node", p: 200, r: 2.5, img: "https://images.unsplash.com/photo-1639762681485-074b7f938ba0?w=400" },
                { n: "Silver Vault", p: 10000, r: 6.5, img: "https://images.unsplash.com/photo-1621761191319-c6fb62004040?w=400" }
            ];
            const list = document.getElementById('plans-list');
            list.innerHTML = plans.map(p => `
                <div class="bento-card overflow-hidden group">
                    <img src="${p.img}" class="h-32 w-full object-cover filter brightness-75 group-hover:brightness-100 transition-all">
                    <div class="p-6">
                        <div class="flex justify-between items-start">
                            <div><h4 class="font-bold">${p.n}</h4><p class="text-[9px] text-blue-600 font-black">${p.r}% Daily</p></div>
                            <p class="font-black">₨ ${p.p.toLocaleString()}</p>
                        </div>
                        <button onclick="buyPlan(${p.p})" class="w-full mt-4 bg-slate-900 text-white py-3 rounded-xl font-bold text-[10px] uppercase">Activate</button>
                    </div>
                </div>
            `).join('');
        }

        function logout() { localStorage.clear(); location.reload(); }
        function changePage(p) { /* Logic to switch tabs */ }
    </script>
</body>
</html>
