<html lang="ur">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>GB Drive - Pro Master</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Outfit:wght@300;500;800&display=swap');
        body { font-family: 'Outfit', sans-serif; background: #f8fafc; color: #1e293b; }
        .hidden { display: none; }
        .glass { background: rgba(255, 255, 255, 0.9); backdrop-filter: blur(12px); border-bottom: 1px solid #e2e8f0; }
        .neo-card { background: white; border-radius: 2.5rem; shadow: 0 10px 30px rgba(0,0,0,0.05); }
        .active-tab { background: #1e40af; color: white; transform: scale(1.05); box-shadow: 0 10px 20px rgba(30, 64, 175, 0.2); }
        @keyframes drive { 0% { left: -50px; } 100% { left: 100%; } }
        .car-anim { position: absolute; animation: drive 12s linear infinite; }
    </style>
</head>
<body class="pb-24">

    <header class="p-4 flex justify-between items-center sticky top-0 z-[100] glass">
        <div onclick="handleLogoTap()" class="flex items-center gap-2 cursor-pointer active:scale-95 transition-all">
            <div class="bg-blue-600 text-white p-2 rounded-xl font-black italic shadow-lg">GB</div>
            <div>
                <p class="font-black text-blue-900 text-lg leading-none">DRIVE</p>
                <p class="text-[7px] font-bold text-blue-400 tracking-widest uppercase">Everything Edition</p>
            </div>
        </div>
        <div id="nav-actions" class="hidden flex items-center gap-3">
            <button onclick="triggerSOS()" class="bg-red-50 text-red-500 p-2 rounded-full border border-red-100"><i data-lucide="shield-alert" class="w-5 h-5"></i></button>
            <div class="w-10 h-10 rounded-full border-2 border-white shadow-md bg-blue-50 overflow-hidden">
                <img src="https://api.dicebear.com/7.x/avataaars/svg?seed=Sweetie" alt="User">
            </div>
        </div>
    </header>

    <main class="max-w-md mx-auto p-4 space-y-6">

        <div id="auth-screen" class="min-h-[70vh] flex flex-col items-center justify-center text-center space-y-8">
            <div class="w-24 h-24 bg-blue-100 rounded-full flex items-center justify-center animate-bounce">
                <i data-lucide="navigation" class="w-12 h-12 text-blue-600"></i>
            </div>
            <h1 class="text-4xl font-black text-gray-900">Welcome, <br><span class="text-blue-600">Sweetie!</span></h1>
            <button onclick="startApp()" class="w-full bg-blue-900 text-white py-5 rounded-[2rem] font-black text-xl shadow-2xl active:scale-95 transition-all">GET STARTED</button>
        </div>

        <div id="main-app" class="hidden space-y-5">
            
            <div class="flex bg-gray-200/50 p-1.5 rounded-2xl gap-2">
                <button onclick="switchTab('p')" id="tab-p" class="flex-1 py-3 rounded-xl font-black text-sm active-tab">PASSENGER</button>
                <button onclick="switchTab('d')" id="tab-d" class="flex-1 py-3 rounded-xl font-black text-sm text-gray-500">DRIVER</button>
            </div>

            <div id="p-panel" class="space-y-4">
                <div class="h-44 bg-blue-50 rounded-[2.5rem] relative overflow-hidden border border-blue-100 shadow-inner">
                    <i data-lucide="car" class="car-anim top-20 text-blue-600 w-8 h-8"></i>
                    <div class="absolute top-4 left-4 bg-white/80 px-3 py-1 rounded-full text-[10px] font-black flex items-center gap-2">
                        <span class="w-2 h-2 bg-green-500 rounded-full"></span> 3 Drivers Nearby
                    </div>
                </div>

                <div class="bg-white p-6 rounded-[2.5rem] shadow-xl space-y-4 border-b-8 border-blue-600">
                    <div class="space-y-2">
                        <div class="flex items-center gap-3 bg-gray-50 p-4 rounded-2xl border border-gray-100">
                            <i data-lucide="map-pin" class="text-green-500 w-5"></i>
                            <input type="text" placeholder="Pick from?" class="bg-transparent w-full outline-none font-bold text-sm">
                        </div>
                        <div class="flex items-center gap-3 bg-gray-50 p-4 rounded-2xl border border-gray-100">
                            <i data-lucide="navigation-2" class="text-blue-600 w-5"></i>
                            <input type="text" placeholder="Where to go?" class="bg-transparent w-full outline-none font-bold text-sm">
                        </div>
                    </div>

                    <div class="bg-blue-600 p-6 rounded-[2rem] text-white text-center shadow-lg">
                        <p class="text-[9px] font-black opacity-60 mb-1 uppercase tracking-widest">OFFER YOUR FARE</p>
                        <div class="flex items-center justify-between px-2">
                            <button onclick="setFare(-50)" class="w-10 h-10 rounded-full bg-white/20 text-2xl font-bold">-</button>
                            <div><span id="fare-val" class="text-4xl font-black">400</span><span class="text-xs font-bold block opacity-70">PKR</span></div>
                            <button onclick="setFare(50)" class="w-10 h-10 rounded-full bg-white/20 text-2xl font-bold">+</button>
                        </div>
                    </div>

                    <button class="w-full bg-blue-900 text-white py-5 rounded-[2rem] font-black text-xl shadow-xl active:scale-95">REQUEST RIDE</button>
                </div>
            </div>

            <div id="d-panel" class="hidden space-y-4">
                <div class="grid grid-cols-2 gap-4">
                    <div class="bg-white p-5 rounded-3xl shadow-sm border border-gray-100">
                        <p class="text-[10px] text-gray-400 font-bold uppercase">Profit</p>
                        <p class="text-2xl font-black text-green-600">Rs 4,200</p>
                    </div>
                    <div class="bg-white p-5 rounded-3xl shadow-sm border border-gray-100">
                        <p class="text-[10px] text-gray-400 font-bold uppercase">Rating</p>
                        <p class="text-2xl font-black text-orange-400">4.9 ★</p>
                    </div>
                </div>
                
                <div class="bg-white p-8 rounded-[3rem] shadow-xl text-center space-y-6">
                    <h2 class="text-2xl font-black">Join as Partner</h2>
                    <input type="text" placeholder="Full Name" class="w-full p-4 rounded-2xl bg-gray-50 outline-none">
                    <input type="text" placeholder="Vehicle Number" class="w-full p-4 rounded-2xl bg-gray-50 outline-none font-black text-blue-600">
                    
                    <div class="grid grid-cols-2 gap-3">
                        <div class="relative h-32 bg-blue-50 border-2 border-dashed border-blue-100 rounded-3xl flex flex-col items-center justify-center overflow-hidden">
                            <input type="file" accept="image/*" class="absolute inset-0 opacity-0 z-20 cursor-pointer" onchange="previewImg(this, 'c-img')">
                            <i data-lucide="credit-card" class="text-blue-300"></i>
                            <img id="c-img" class="absolute inset-0 w-full h-full object-cover hidden">
                        </div>
                        <div class="relative h-32 bg-blue-50 border-2 border-dashed border-blue-100 rounded-3xl flex flex-col items-center justify-center overflow-hidden">
                            <input type="file" accept="image/*" class="absolute inset-0 opacity-0 z-20 cursor-pointer" onchange="previewImg(this, 's-img')">
                            <i data-lucide="camera" class="text-blue-300"></i>
                            <img id="s-img" class="absolute inset-0 w-full h-full object-cover hidden">
                        </div>
                    </div>
                    <button class="w-full bg-blue-600 text-white py-4 rounded-2xl font-black shadow-lg">Submit Data</button>
                </div>
            </div>
        </div>
    </main>

    <nav id="footer-nav" class="hidden fixed bottom-0 left-0 right-0 glass p-5 flex justify-around items-center z-[100] rounded-t-[2.5rem]">
        <button class="text-blue-600"><i data-lucide="home"></i></button>
        <button class="text-gray-300"><i data-lucide="history"></i></button>
        <button class="text-gray-300" onclick="alert('Wallet: 1,500 PKR')"><i data-lucide="wallet"></i></button>
        <button class="text-gray-300"><i data-lucide="settings"></i></button>
    </nav>

    <div id="admin-modal" class="hidden fixed inset-0 bg-black/90 backdrop-blur-md flex items-center justify-center z-[1000] p-6">
        <div class="bg-white p-8 rounded-[3rem] w-full max-w-xs text-center">
            <h3 class="font-black text-xl mb-4 italic">GB DRIVE ADMIN</h3>
            <input type="password" id="admin-pin" placeholder="PIN" class="w-full bg-gray-100 p-4 rounded-2xl mb-4 text-center text-3xl font-black outline-none border-2 border-transparent focus:border-blue-600">
            <button onclick="checkAdmin()" class="w-full bg-blue-600 text-white py-4 rounded-2xl font-black shadow-lg">LOGIN</button>
            <button onclick="closeAdmin()" class="mt-4 text-gray-300 font-bold text-xs uppercase">Cancel</button>
        </div>
    </div>

    <script>
        lucide.createIcons();
        let fare = 400; let taps = 0; let timer;

        function startApp() {
            document.getElementById('auth-screen').classList.add('hidden');
            document.getElementById('main-app').classList.remove('hidden');
            document.getElementById('nav-actions').classList.remove('hidden');
            document.getElementById('footer-nav').classList.remove('hidden');
        }

        function switchTab(m) {
            const p = document.getElementById('p-panel'); const d = document.getElementById('d-panel');
            const tp = document.getElementById('tab-p'); const td = document.getElementById('tab-d');
            if(m === 'p') {
                p.classList.remove('hidden'); d.classList.add('hidden');
                tp.classList.add('active-tab'); td.classList.remove('active-tab');
            } else {
                d.classList.remove('hidden'); p.classList.add('hidden');
                td.classList.add('active-tab'); tp.classList.remove('active-tab');
            }
        }

        function setFare(v) { fare += v; if(fare < 150) fare = 150; document.getElementById('fare-val').innerText = fare; }

        function previewImg(input, tid) {
            const f = input.files[0];
            if(f) {
                const r = new FileReader();
                r.onload = (e) => { const i = document.getElementById(tid); i.src = e.target.result; i.classList.remove('hidden'); };
                r.readAsDataURL(f);
            }
        }

        function handleLogoTap() {
            taps++; clearTimeout(timer);
            if(taps === 4) { document.getElementById('admin-modal').classList.remove('hidden'); taps = 0; }
            timer = setTimeout(() => taps = 0, 3000);
        }

        function checkAdmin() {
            if(document.getElementById('admin-pin').value === 'gb46') { alert('Dashboard Unlocked!'); location.reload(); }
            else { alert('Access Denied!'); closeAdmin(); }
        }

        function closeAdmin() { document.getElementById('admin-modal').classList.add('hidden'); }
        function triggerSOS() { alert('EMERGENCY: Location sent to GB Police!'); }
    </script>
</body>
</html>
