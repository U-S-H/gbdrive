<html lang="ur">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>GB Drive - Final Master</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;700;900&display=swap');
        body { font-family: 'Inter', sans-serif; background-color: #f3f4f6; }
        .hidden { display: none; }
        .active-tab { border-color: #1e40af; background-color: #eff6ff; color: #1e40af; }
        .preview-img { width: 100%; height: 100%; object-fit: cover; border-radius: 12px; }
    </style>
</head>
<body>

    <header class="bg-white p-4 shadow-md flex justify-between items-center sticky top-0 z-50">
        <div onclick="handleLogoTap()" class="cursor-pointer flex items-center gap-1 select-none active:scale-95 transition-transform">
            <div class="bg-blue-600 text-white px-2 py-1 rounded-lg font-black text-xl">GB</div>
            <div class="text-blue-900 font-black text-xl tracking-tighter">DRIVE</div>
        </div>
        <div id="user-profile" class="hidden flex items-center gap-2">
            <button onclick="toggleSOS()" class="bg-red-100 text-red-600 p-2 rounded-full">
                <i data-lucide="shield-alert"></i>
            </button>
            <button onclick="location.reload()" class="text-gray-400"><i data-lucide="log-out"></i></button>
        </div>
    </header>

    <main class="max-w-md mx-auto p-4 space-y-4">

        <div id="auth-section" class="bg-white p-8 rounded-[2.5rem] shadow-xl text-center mt-10">
            <div class="w-20 h-20 bg-blue-50 rounded-full flex items-center justify-center mx-auto mb-6">
                <i data-lucide="car" class="text-blue-600 w-10 h-10"></i>
            </div>
            <h1 class="text-3xl font-black text-gray-900 mb-2">Chalo GB!</h1>
            <p class="text-gray-500 mb-8 text-sm">Apni ride choose karen aur apni marzi ka kiraya offer karen.</p>
            <button onclick="showMainApp()" class="w-full bg-blue-600 text-white py-4 rounded-2xl font-bold shadow-lg shadow-blue-200 flex items-center justify-center gap-3 active:scale-95 transition-all">
                <img src="https://www.gstatic.com/firebasejs/ui/2.0.0/images/auth/google.svg" width="20" class="bg-white rounded-full p-0.5">
                Google se Login Karen
            </button>
        </div>

        <div id="app-content" class="hidden space-y-4">
            <div class="flex bg-white p-1 rounded-2xl shadow-sm border border-gray-100">
                <button onclick="switchTab('passenger')" id="tab-pass" class="flex-1 py-3 rounded-xl font-bold text-sm active-tab">Passenger</button>
                <button onclick="switchTab('driver')" id="tab-drive" class="flex-1 py-3 rounded-xl font-bold text-sm text-gray-400">Driver Mode</button>
            </div>

            <div id="passenger-mode" class="space-y-4">
                <div class="bg-white p-5 rounded-[2rem] shadow-lg space-y-4">
                    <div class="space-y-2">
                        <div class="flex items-center gap-3 bg-gray-50 p-4 rounded-2xl border border-gray-100">
                            <i data-lucide="map-pin" class="text-green-500 w-5"></i>
                            <input type="text" placeholder="Pickup Point" class="bg-transparent w-full outline-none font-medium">
                        </div>
                        <div class="flex items-center gap-3 bg-gray-50 p-4 rounded-2xl border border-gray-100">
                            <i data-lucide="navigation" class="text-blue-600 w-5"></i>
                            <input type="text" placeholder="Drop-off Destination" class="bg-transparent w-full outline-none font-medium">
                        </div>
                    </div>

                    <div class="bg-blue-50 p-6 rounded-[2rem] text-center border border-blue-100">
                        <span class="text-[10px] font-black text-blue-400 uppercase tracking-widest">Offer Your Fare</span>
                        <div class="flex items-center justify-center gap-6 mt-2">
                            <button onclick="changeFare(-50)" class="w-12 h-12 bg-white rounded-full shadow-md flex items-center justify-center text-2xl font-bold text-blue-600 active:scale-90">-</button>
                            <div class="flex flex-col">
                                <span class="text-xs font-bold text-gray-400">PKR</span>
                                <span id="fare-val" class="text-4xl font-black text-blue-900 leading-none">350</span>
                            </div>
                            <button onclick="changeFare(50)" class="w-12 h-12 bg-white rounded-full shadow-md flex items-center justify-center text-2xl font-bold text-blue-600 active:scale-90">+</button>
                        </div>
                    </div>

                    <div class="grid grid-cols-3 gap-3">
                        <div class="p-4 bg-blue-600 rounded-2xl text-center text-white shadow-lg active:scale-95 transition-all">
                            <i data-lucide="car" class="mx-auto mb-1"></i>
                            <span class="text-[10px] font-bold block">AC Ride</span>
                        </div>
                        <div class="p-4 bg-white rounded-2xl text-center text-gray-400 border border-gray-100">
                            <i data-lucide="bike" class="mx-auto mb-1"></i>
                            <span class="text-[10px] font-bold block">Moto</span>
                        </div>
                        <div class="p-4 bg-white rounded-2xl text-center text-gray-400 border border-gray-100">
                            <i data-lucide="package" class="mx-auto mb-1"></i>
                            <span class="text-[10px] font-bold block">Mini</span>
                        </div>
                    </div>

                    <button class="w-full bg-blue-900 text-white py-5 rounded-3xl font-black text-lg shadow-xl active:scale-95 transition-all">Find a Driver</button>
                </div>
            </div>

            <div id="driver-mode" class="hidden space-y-4">
                <div class="bg-white p-6 rounded-[2rem] shadow-lg space-y-4">
                    <h2 class="text-xl font-black text-gray-800">Become a Driver</h2>
                    <input type="text" placeholder="Full Name" class="w-full p-4 rounded-2xl bg-gray-50 border-none outline-none">
                    <input type="text" placeholder="Vehicle No (e.g. GBA-786)" class="w-full p-4 rounded-2xl bg-gray-50 border-none outline-none font-mono">
                    
                    <div class="grid grid-cols-2 gap-3">
                        <div class="relative h-32 bg-gray-50 rounded-2xl border-2 border-dashed border-gray-200 flex flex-col items-center justify-center overflow-hidden">
                            <input type="file" accept="image/*" class="absolute inset-0 opacity-0 z-10 cursor-pointer" onchange="previewImg(this, 'c-p')">
                            <i data-lucide="credit-card" id="c-p-icon" class="text-gray-300"></i>
                            <span id="c-p-txt" class="text-[10px] font-bold text-gray-300">CNIC PHOTO</span>
                            <img id="c-p" class="preview-img hidden">
                        </div>
                        <div class="relative h-32 bg-gray-50 rounded-2xl border-2 border-dashed border-gray-200 flex flex-col items-center justify-center overflow-hidden">
                            <input type="file" accept="image/*" class="absolute inset-0 opacity-0 z-10 cursor-pointer" onchange="previewImg(this, 's-p')">
                            <i data-lucide="camera" id="s-p-icon" class="text-gray-300"></i>
                            <span id="s-p-txt" class="text-[10px] font-bold text-gray-300">SELFIE</span>
                            <img id="s-p" class="preview-img hidden">
                        </div>
                    </div>
                    <button class="w-full bg-blue-600 text-white py-4 rounded-2xl font-black shadow-lg">Submit Verification</button>
                </div>
            </div>
        </div>

    </main>

    <div id="admin-modal" class="hidden fixed inset-0 bg-black/95 flex items-center justify-center z-[1000] p-6">
        <div class="bg-white p-8 rounded-[2.5rem] w-full max-w-xs text-center">
            <h3 class="font-black text-gray-800 text-xl mb-4">Admin Access</h3>
            <input type="password" id="admin-key" placeholder="****" class="w-full bg-gray-50 p-4 rounded-2xl mb-4 text-center text-2xl outline-none">
            <button onclick="verifyAdmin()" class="w-full bg-blue-600 text-white py-4 rounded-2xl font-black">Unlock</button>
            <button onclick="closeModal()" class="mt-4 text-gray-400 text-sm font-bold">Cancel</button>
        </div>
    </div>

    <script>
        lucide.createIcons();
        let taps = 0; let timer; let fare = 350;

        function showMainApp() {
            document.getElementById('auth-section').classList.add('hidden');
            document.getElementById('app-content').classList.remove('hidden');
            document.getElementById('user-profile').classList.remove('hidden');
        }

        function switchTab(mode) {
            const pass = document.getElementById('passenger-mode');
            const drive = document.getElementById('driver-mode');
            const tP = document.getElementById('tab-pass');
            const tD = document.getElementById('tab-drive');
            if(mode === 'passenger') {
                pass.classList.remove('hidden'); drive.classList.add('hidden');
                tP.classList.add('active-tab'); tD.classList.remove('active-tab');
            } else {
                drive.classList.remove('hidden'); pass.classList.add('hidden');
                tD.classList.add('active-tab'); tP.classList.remove('active-tab');
            }
        }

        function changeFare(amt) {
            fare += amt; if(fare < 100) fare = 100;
            document.getElementById('fare-val').innerText = fare;
        }

        function previewImg(input, targetId) {
            const file = input.files[0];
            if(file) {
                const reader = new FileReader();
                reader.onload = function(e) {
                    const img = document.getElementById(targetId);
                    img.src = e.target.result;
                    img.classList.remove('hidden');
                    document.getElementById(targetId + '-icon').classList.add('hidden');
                    document.getElementById(targetId + '-txt').classList.add('hidden');
                }
                reader.readAsDataURL(file);
            }
        }

        function handleLogoTap() {
            taps++; clearTimeout(timer);
            if(taps === 4) { document.getElementById('admin-modal').classList.remove('hidden'); taps = 0; }
            timer = setTimeout(() => taps = 0, 3000);
        }

        function verifyAdmin() {
            if(document.getElementById('admin-key').value === 'gb46') {
                alert('Admin Dashboard Unlocked! (Redirecting...)');
                location.reload(); // Replace with dashboard logic
            } else { alert('Access Denied!'); closeModal(); }
        }

        function closeModal() { document.getElementById('admin-modal').classList.add('hidden'); }
        function toggleSOS() { alert('SOS: Live Location shared with GB Police & Emergency Contacts!'); }
    </script>
</body>
</html>
