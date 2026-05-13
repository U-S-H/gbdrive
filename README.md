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
    <title>Vestify Ultra-Max | Global Terminal</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --primary: #2563eb; --bg: #f9fafb; --card: #ffffff; --text: #111827; }
        .dark-mode { --bg: #0f172a; --card: #1e293b; --text: #f8fafc; }
        
        body { font-family: 'Plus Jakarta Sans', sans-serif; background-color: var(--bg); color: var(--text); transition: 0.3s; }
        .bento { background: var(--card); border: 1px solid rgba(0,0,0,0.05); border-radius: 24px; }
        
        /* Spin Wheel Style */
        #wheel-container { position: relative; width: 200px; height: 200px; margin: auto; }
        #wheel { width: 100%; height: 100%; border-radius: 50%; border: 5px solid #2563eb; transition: transform 4s cubic-bezier(0.15, 0, 0.15, 1); }
        .pointer { position: absolute; top: -10px; left: 50%; transform: translateX(-50%); width: 20px; height: 20px; background: red; clip-path: polygon(50% 100%, 0 0, 100% 0); z-index: 10; }

        /* Achievement Badge Glow */
        .badge-glow { box-shadow: 0 0 15px rgba(37, 99, 235, 0.3); border: 1px solid #2563eb; }
        
        .page { display: none; animation: slideUp 0.5s ease-out; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
    </style>
</head>
<body class="min-h-screen">

    <header class="p-6 flex justify-between items-center sticky top-0 z-[1000] bg-inherit/80 backdrop-blur-md border-b border-slate-100">
        <div class="flex items-center gap-2">
            <div class="w-10 h-10 bg-blue-600 rounded-2xl flex items-center justify-center shadow-lg"><i class="fa-solid fa-v text-white"></i></div>
            <span class="font-black text-xl tracking-tighter uppercase">Vestify</span>
        </div>
        <div class="flex items-center gap-4">
            <button onclick="toggleDarkMode()" class="w-10 h-10 rounded-full bento flex items-center justify-center"><i class="fa-solid fa-moon"></i></button>
            <div class="relative"><i class="fa-solid fa-bell text-slate-400 text-xl"></i><div class="absolute -top-1 -right-1 w-2 h-2 bg-red-500 rounded-full"></div></div>
        </div>
    </header>

    <main class="p-6 pb-32">
        <div id="p-home" class="page active-page">
            
            <div class="bento p-6 mb-6">
                <div class="flex justify-between items-center mb-4">
                    <h3 class="text-[10px] font-black uppercase tracking-widest text-slate-400">Market Performance</h3>
                    <span class="text-[10px] font-bold text-emerald-500">+12.5% Up</span>
                </div>
                <canvas id="growthChart" height="120"></canvas>
            </div>

            <div class="bg-blue-600 p-8 rounded-[2.5rem] text-white mb-6 shadow-xl shadow-blue-200 relative overflow-hidden">
                <div class="relative z-10">
                    <p class="text-[9px] font-bold opacity-60 uppercase tracking-widest">Global Capital</p>
                    <h2 class="text-4xl font-extrabold tracking-tighter mb-8" id="v-bal">₨ 0.00</h2>
                    <div class="flex justify-between border-t border-white/10 pt-4">
                        <p class="text-[8px] font-black uppercase">Account Level: <span id="rank">Bronze</span></p>
                        <p class="text-[8px] font-black uppercase">SAFE Fund: <span class="text-emerald-300">Active</span></p>
                    </div>
                </div>
            </div>

            <div class="flex gap-4 overflow-x-auto pb-4 mb-6 no-scrollbar">
                <div class="min-w-[120px] bento p-4 flex flex-col items-center badge-glow">
                    <i class="fa-solid fa-trophy text-blue-600 mb-2"></i>
                    <span class="text-[8px] font-black uppercase text-center">First Deposit</span>
                </div>
                <div class="min-w-[120px] bento p-4 flex flex-col items-center opacity-40">
                    <i class="fa-solid fa-users-gear text-slate-400 mb-2"></i>
                    <span class="text-[8px] font-black uppercase text-center">Team Leader</span>
                </div>
                <div class="min-w-[120px] bento p-4 flex flex-col items-center opacity-40">
                    <i class="fa-solid fa-crown text-slate-400 mb-2"></i>
                    <span class="text-[8px] font-black uppercase text-center">Millionaire</span>
                </div>
            </div>

            <div class="bento p-6 mb-6 text-center">
                <h4 class="text-xs font-black uppercase tracking-widest mb-4">Lucky Wheel Rewards</h4>
                <div id="wheel-container">
                    <div class="pointer"></div>
                    <img id="wheel" src="https://i.ibb.co/3Ym9G2F/wheel-bg.png" alt="wheel">
                </div>
                <button onclick="spinWheel()" id="spin-btn" class="mt-6 bg-blue-600 text-white px-8 py-3 rounded-2xl font-black text-[10px] uppercase shadow-lg shadow-blue-100">Spin for ₨ 10</button>
            </div>

            <div id="plans-list" class="space-y-4"></div>
        </div>
    </main>

    <nav class="fixed bottom-0 left-0 w-full p-6 flex justify-around items-center bg-white/90 backdrop-blur-md border-t border-slate-100 z-[1000]">
        <button class="text-blue-600"><i class="fa-solid fa-house-chimney text-xl"></i></button>
        <button class="text-slate-400"><i class="fa-solid fa-gamepad text-xl"></i></button>
        <button class="text-slate-400"><i class="fa-solid fa-wallet text-xl"></i></button>
        <button class="text-slate-400"><i class="fa-solid fa-user-astronaut text-xl"></i></button>
    </nav>

    <script>
        // 1. Dark Mode Toggle
        function toggleDarkMode() { document.body.classList.toggle('dark-mode'); }

        // 2. Growth Chart
        const ctx = document.getElementById('growthChart').getContext('2d');
        new Chart(ctx, {
            type: 'line',
            data: {
                labels: ['1h', '2h', '3h', '4h', '5h', '6h'],
                datasets: [{
                    label: 'Yield Rate',
                    data: [12, 19, 15, 25, 22, 30],
                    borderColor: '#2563eb',
                    tension: 0.4,
                    fill: true,
                    backgroundColor: 'rgba(37, 99, 235, 0.05)'
                }]
            },
            options: { plugins: { legend: { display: false } }, scales: { y: { display: false }, x: { grid: { display: false } } } }
        });

        // 3. Lucky Spin Logic
        let isSpinning = false;
        function spinWheel() {
            if (isSpinning) return;
            isSpinning = true;
            const wheel = document.getElementById('wheel');
            const randomDegree = Math.floor(Math.random() * 3600) + 720; // At least 2 rotations
            wheel.style.transform = `rotate(${randomDegree}deg)`;
            
            setTimeout(() => {
                isSpinning = false;
                alert("Congratulations! You won a mystery reward!");
                // Yahan aap Firebase mein balance update kar sakte hain
            }, 4000);
        }

        // 4. Firebase & App Logic
        // (Yahan aap apna purana Firebase logic merge kar sakte hain)
        function renderPlans() {
            // ... (Purana render logic)
        }
    </script>
</body>
</html>
