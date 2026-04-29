<html lang="ur">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>GB Drive - The Ultimate Ride App</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Outfit:wght@300;500;800&display=swap');
        body { font-family: 'Outfit', sans-serif; background: #f0f4f8; overflow-x: hidden; }
        .hidden { display: none; }
        .glass { background: rgba(255, 255, 255, 0.8); backdrop-filter: blur(10px); border: 1px solid rgba(255,255,255,0.3); }
        .shimmer { background: linear-gradient(90deg, #eff6ff 25%, #dbeafe 50%, #eff6ff 75%); background-size: 200% 100%; animation: shimmer 2s infinite; }
        @keyframes shimmer { 0% { background-position: 200% 0; } 100% { background-position: -200% 0; } }
        .active-mode { background: #1e40af; color: white; transform: scale(1.05); box-shadow: 0 10px 20px rgba(30, 64, 175, 0.2); }
    </style>
</head>
<body class="pb-10">

    <header class="p-5 flex justify-between items-center sticky top-0 z-[100] glass">
        <div onclick="handleLogoTap()" class="flex items-center gap-2 cursor-pointer select-none active:scale-95 transition-all">
            <div class="w-10 h-10 bg-blue-600 rounded-xl flex items-center justify-center shadow-lg rotate-3">
                <span class="text-white font-black text-xl">GB</span>
            </div>
            <div class="leading-none">
                <p class="font-black text-blue-900 text-xl tracking-tighter">DRIVE</p>
                <p class="text-[8px] font-bold text-blue-400 tracking-[0.2em] uppercase">Gilgit Baltistan</p>
            </div>
        </div>
        <div id="top-nav" class="hidden flex items-center gap-3">
            <button onclick="triggerSOS()" class="bg-red-500 text-white p-2.5 rounded-full shadow-lg shadow-red-200 animate-pulse">
                <i data-lucide="shield-alert" class="w-5 h-5"></i>
            </button>
            <div class="w-10 h-10 rounded-full bg-blue-100 border-2 border-white overflow-hidden shadow-md">
                <img src="https://api.dicebear.com/7.x/avataaars/svg?seed=Felix" alt="avatar">
            </div>
        </div>
    </header>

    <main class="max-w-md mx-auto px-4 mt-4 space-y-5">

        <div id="auth-box" class="bg-white p-10 rounded-[3rem] shadow-2xl text-center space-y-6 border-b-8 border-blue-600">
            <div class="relative inline-block">
                <div class="w-24 h-24 bg-blue-50 rounded-full flex items-center justify-center mx-auto">
                    <i data-lucide="map" class="w-12 h-12 text-blue-600"></i>
                </div>
                <span class="absolute -top-2 -right-2 bg-green-500 text-white text-[10px] font-bold px-2 py-1 rounded-full uppercase">Online</span>
            </div>
            <div>
                <h1 class="text-4xl font-black text-gray-900 leading-tight">Safar Mubarak, <span class="text-blue-600">Sweetie!</span></h1>
                <p class="text-gray-400 mt-2 text-sm">Ab pure GB mein travel karna hua aur bhi asaan.</p>
            </div>
            <button onclick="loginApp()" class="w-full bg-gray-900 text-white py-5 rounded-2xl font-black flex items-center justify-center gap-4 hover:bg-black transition-all active:scale-95 shadow-2xl">
                <img src="https://www.gstatic.com/firebasejs/ui/2.0.0/images/auth/google.svg" width="22" class="bg-white p-0.5 rounded-full">
                Google Login
            </button>
        </div>

        <div id="main-app" class="hidden space-y-4">
            
            <div class="grid grid-cols-2 gap-2 bg-gray-200 p-1.5 rounded-2xl">
                <button onclick="setMode('pass')" id="btn-pass" class="py-3 rounded-xl font-black text-sm active-mode transition-all">PASSENGER</button>
                <button onclick="setMode('driver')" id="btn-driver" class="py-3 rounded-xl font-black text-sm text-gray-500 transition-all uppercase">Driver Mode</button>
            </div>

            <div id="pass-panel" class="space-y-4 animate-in fade-in duration-500">
                <div class="h-44 bg-blue-100 rounded-[2.5rem] relative overflow-hidden shadow-inner shimmer">
                    <div class="absolute inset-0 opacity-20 bg-[url('https://www.transparenttextures.com/patterns/road.png')]"></div>
                    <div class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 flex flex-col items-center">
                        <i data-lucide="map-pin" class="text-blue-600 w-8 h-8 animate-bounce"></i>
                        <p class="text-[10px] font-bold text-blue-800 bg-white/80 px-2 py-0.5 rounded-full">Locating you...</p>
                    </div>
                </div>

                <div class="bg-white p-6 rounded-[2.5rem] shadow-xl space-y-4">
                    <div class="relative space-y-3">
                        <div class="absolute left-6 top-7 bottom-7 w-0.5 bg-dashed bg-blue-200 border-l-2 border-dashed"></div>
                        <div class="flex items-center gap-4 bg-gray-50 p-4 rounded-2xl border border-gray-100">
                            <div class="w-3 h-3 rounded-full bg-green-500 shadow-lg shadow-green-200"></div>
                            <input type="text" placeholder="Where from?" class="bg-transparent w-full outline-none font-bold text-sm">
                        </div>
                        <div class="flex items-center gap-4 bg-gray-50 p-4 rounded-2xl border border-gray-100">
                            <div class="w-3 h-3 rounded-full bg-blue-600 shadow-lg shadow-blue-200"></div>
                            <input type="text" placeholder="Where to?" class="bg-transparent w-full outline-none font-bold text-sm">
                        </div>
                    </div>

                    <div class="bg-blue-600 p-6 rounded-[2rem] text-white shadow-lg shadow-blue-200 text-center relative overflow-hidden">
                        <div class="absolute -right-5 -top-5 w-20 h-20 bg-white/10 rounded-full"></div>
                        <p class="text-[10px] font-black opacity-70 uppercase tracking-tighter mb-2">Offer Your Fare (Negotiable)</p>
                        <div class="flex items-center justify-between px-4">
                            <button onclick="updateFare(-50)" class="w-12 h-12 rounded-full bg-white/20 hover:bg-white/30 text-2xl font-bold transition-all">-</button>
                            <div class="flex flex-col">
                                <span class="text-4xl font-black" id="fare-txt">400</span>
                                <span class="text-[10px] font-bold opacity-80">PKR TOTAL</span>
                            </div>
                            <button onclick="updateFare(50)" class="w-12 h-12 rounded-full bg-white/20 hover:bg-white/30 text-2xl font-bold transition-all">+</button>
                        </div>
                    </div>

                    <div class="grid grid-cols-3 gap-2">
                        <button class="p-3 rounded-2xl border-2 border-blue-600 bg-blue-50 flex flex-col items-center">
                            <i data-lucide="car" class="text-blue-600 w-5 h-5"></i>
                            <span class="text-[9px] font-black mt-1 uppercase">AC Car</span>
                        </button>
                        <button class="p-3 rounded-2xl border-2 border-gray-100 bg-white flex flex-col items-center opacity-50">
                            <i data-lucide="bike" class="text-gray-400 w-5 h-5"></i>
                            <span class="text-[9px] font-black mt-1 uppercase">Bike</span>
                        </button>
                        <button class="p-3 rounded-2xl border-2 border-gray-100 bg-white flex flex-col items-center opacity-50">
                            <i data-lucide="truck" class="text-gray-400 w-5 h-5"></i>
                            <span class="text-[9px] font-black mt-1 uppercase">Mini</span>
                        </button>
                    </div>

                    <button class="w-full bg-blue-900 text-white py-5 rounded-[2rem] font-black text-xl shadow-2xl active:scale-95 transition-all">REQUEST RIDE</button>
                </div>
            </div>

            <div id="driver-panel" class="hidden space-y-4 animate-in slide-in-from-right duration-500">
                <div class="bg-white p-8 rounded-[3rem] shadow-xl text-center space-y-5">
                    <h2 class="text-2xl font-black text-gray-800">Earn with GB Drive</h2>
                    <p class="text-xs text-gray-400 px-5 uppercase font-bold">Registration takes only 2 minutes</p>
                    
                    <div class="space-y-3">
                        <input type="text" placeholder="Asli Naam" class="w-full p-4 rounded-2xl bg-gray-50 border-none outline-none font-bold">
                        <input type="text" placeholder="Gari ka Number" class="w-full p-4 rounded-2xl bg-gray-50 border-none outline-none font-black text-blue-600">
                    </div>

                    <div class="grid grid-cols-2 gap-4">
                        <div class="relative h-36 bg-blue-50/50 rounded-3xl border-2 border-dashed border-blue-200 flex flex-col items-center justify-center group overflow-hidden">
                            <input type="file" accept="image/*" class="absolute inset-0 opacity-0 z-20 cursor-pointer" onchange="handleImg(this, 'cnic-img')">
                            <i data-lucide="credit-card" class="text-blue-300 w-8 h-8 group-hover:scale-110 transition-transform"></i>
                            <p class="text-[10px] font-black text-blue-300 mt-2">CNIC PHOTO</p>
                            <img id="cnic-img" class="absolute inset-0 w-full h-full object-cover hidden">
                        </div>
                        <div class="relative h-36 bg-blue-50/50 rounded-3xl border-2 border-dashed border-blue-200 flex flex-col items-center justify-center group overflow-hidden">
                            <input type="file" accept="image/*" class="absolute inset-0 opacity-0 z-20 cursor-pointer" onchange="handleImg(this, 'face-img')">
                            <i data-lucide="camera" class="text-blue-300 w-8 h-8 group-hover:scale-110 transition-transform"></i>
                            <p class="text-[10px] font-black text-blue-300 mt-2">LIVE SELFIE</p>
                            <img id="face-img" class="absolute inset-0 w-full h-full object-cover hidden">
                        </div>
                    </div>

                    <button class="w-full bg-blue-600 text-white py-5 rounded-[2rem] font-black shadow-xl shadow-blue-100">JOIN THE TEAM</button>
                </div>
            </div>
        </div>

    </main>

    <div id="admin-modal" class="hidden fixed inset-0 bg-blue-900/90 backdrop-blur-xl flex items-center justify-center z-[1000] p-6">
        <div class="bg-white p-8 rounded-[3rem] w-full max-w-xs text-center shadow-2xl">
            <h3 class="font-black text-gray-800 text-2xl mb-2">Secret Entry</h3>
            <p class="text-[10px] font-bold text-gray-400 mb-6 tracking-widest uppercase">Authorized Personnel Only</p>
            <input type="password" id="admin-key" placeholder="••••" class="w-full bg-gray-50 p-5 rounded-2xl mb-5 text-center text-3xl font-black outline-none border-2 border-transparent focus:border-blue-600 transition-all">
            <button onclick="verifyAdmin()" class="w-full bg-blue-600 text-white py-4 rounded-2xl font-black shadow-lg">UNLOCK ACCESS</button>
            <button onclick="closeModal()" class="mt-4 text-gray-300 font-bold text-sm">Dismiss</button>
        </div>
    </div>

    <script>
        lucide.createIcons();
        let taps = 0; let timer; let fare = 400;

        function loginApp() {
            document.getElementById('auth-box').classList.add('hidden');
            document.getElementById('main-app').classList.remove('hidden');
            document.getElementById('top-nav').classList.remove('hidden');
        }

        function setMode(mode) {
            const p = document.getElementById('pass-panel');
            const d = document.getElementById('driver-panel');
            const bp = document.getElementById('btn-pass');
            const bd = document.getElementById('btn-driver');
            
            if(mode === 'pass') {
                p.classList.remove('hidden'); d.classList.add('hidden');
                bp.className = 'py-3 rounded-xl font-black text-sm active-mode transition-all';
                bd.className = 'py-3 rounded-xl font-black text-sm text-gray-500 transition-all uppercase';
            } else {
                d.classList.remove('hidden'); p.classList.add('hidden');
                bd.className = 'py-3 rounded-xl font-black text-sm active-mode transition-all';
                bp.className = 'py-3 rounded-xl font-black text-sm text-gray-400 transition-all uppercase';
            }
        }

        function updateFare(amt) {
            fare += amt; if(fare < 150) fare = 150;
            document.getElementById('fare-txt').innerText = fare;
        }

        function handleImg(input, target) {
            const file = input.files[0];
            if(file) {
                const reader = new FileReader();
                reader.onload = (e) => {
                    const img = document.getElementById(target);
                    img.src = e.target.result;
                    img.classList.remove('hidden');
                };
                reader.readAsDataURL(file);
            }
        }

        function handleLogoTap() {
            taps++; clearTimeout(timer);
            if(taps === 4) { document.getElementById('admin-modal').classList.remove('hidden'); taps = 0; }
            timer = setTimeout(() => taps = 0, 3000);
        }

        function verifyAdmin() {
            const key = document.getElementById('admin-key').value;
            if(key === 'gb46') { alert('SYSTEM UNLOCKED! Welcome Boss.'); location.reload(); }
            else { alert('ACCESS DENIED!'); closeModal(); }
        }

        function closeModal() { document.getElementById('admin-modal').classList.add('hidden'); }
        function triggerSOS() { alert('EMERGENCY: Your live GPS coordinates have been sent to GB Police Control Room and your 5 trusted contacts.'); }
    </script>
</body>
</html>
