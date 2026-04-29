<html lang="ur">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>GB Drive - Professional Setup</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    <style>
        .hidden { display: none; }
        .preview-img { width: 100%; height: 100%; object-fit: cover; border-radius: 12px; }
    </style>
</head>
<body class="bg-gray-50 font-sans">

    <header class="bg-white p-4 shadow-sm flex justify-between items-center sticky top-0 z-50">
        <div onclick="handleLogoTap()" class="cursor-pointer flex items-center gap-1 select-none active:scale-95 transition-transform">
            <span class="bg-blue-600 text-white px-2 py-1 rounded font-bold text-lg">GB</span>
            <span class="text-blue-900 font-extrabold text-lg tracking-tight">DRIVE</span>
        </div>
        <button id="logout-btn" onclick="location.reload()" class="hidden text-gray-400"><i data-lucide="log-out"></i></button>
    </header>

    <main class="max-w-md mx-auto p-6 flex flex-col items-center justify-center min-h-[80vh]">
        
        <div id="login-section" class="text-center w-full">
            <h1 class="text-3xl font-black text-blue-900 mb-2">GB DRIVE</h1>
            <p class="text-gray-500 mb-8 text-sm">Base64 Secure Image System Enabled</p>
            <button onclick="showDriverForm()" class="w-full bg-white border-2 border-gray-100 flex items-center justify-center gap-3 py-4 rounded-3xl shadow-xl font-bold active:scale-95 transition-all">
                <img src="https://www.gstatic.com/firebasejs/ui/2.0.0/images/auth/google.svg" width="20">
                Google se Login Karen
            </button>
        </div>

        <div id="driver-form" class="hidden w-full space-y-4">
            <h2 class="text-xl font-bold text-gray-800 text-center mb-4">Driver Details</h2>
            <input type="text" id="name" placeholder="Asli Naam" class="w-full p-4 rounded-2xl bg-white border border-gray-100 outline-none">
            <input type="tel" id="phone" placeholder="Phone Number" class="w-full p-4 rounded-2xl bg-white border border-gray-100 outline-none">
            
            <div class="grid grid-cols-2 gap-3">
                <div class="relative h-32 border-2 border-dashed border-gray-300 rounded-2xl bg-white flex flex-col items-center justify-center overflow-hidden">
                    <input type="file" accept="image/*" class="absolute inset-0 opacity-0 cursor-pointer" onchange="processImage(this, 'cnic-prev', 'cnic-base64')">
                    <div id="cnic-prev-ui" class="text-center">
                        <i data-lucide="credit-card" class="mx-auto text-gray-400"></i>
                        <p class="text-[10px] font-bold text-gray-400">CNIC PHOTO</p>
                    </div>
                    <img id="cnic-prev" class="preview-img hidden">
                </div>

                <div class="relative h-32 border-2 border-dashed border-gray-300 rounded-2xl bg-white flex flex-col items-center justify-center overflow-hidden">
                    <input type="file" accept="image/*" class="absolute inset-0 opacity-0 cursor-pointer" onchange="processImage(this, 'selfie-prev', 'selfie-base64')">
                    <div id="selfie-prev-ui" class="text-center">
                        <i data-lucide="camera" class="mx-auto text-gray-400"></i>
                        <p class="text-[10px] font-bold text-gray-400">LIVE SELFIE</p>
                    </div>
                    <img id="selfie-prev" class="preview-img hidden">
                </div>
            </div>

            <input type="hidden" id="cnic-base64">
            <input type="hidden" id="selfie-base64">

            <button onclick="submitData()" class="w-full bg-blue-600 text-white py-4 rounded-2xl font-bold shadow-lg shadow-blue-200 active:scale-95 transition-transform">
                Data Submit Karen
            </button>
        </div>

        <div id="admin-panel" class="hidden fixed inset-0 bg-white z-[100] p-6 overflow-y-auto">
            <div class="flex justify-between items-center mb-6">
                <h2 class="text-2xl font-black text-blue-900">Admin Panel</h2>
                <button onclick="closeAdmin()" class="bg-gray-100 p-2 rounded-full">X</button>
            </div>
            <div id="admin-content" class="space-y-4">
                <p class="text-gray-400 text-center">Data check karne ke liye download par click karen.</p>
                <button onclick="downloadJSON()" class="w-full bg-green-600 text-white py-4 rounded-2xl font-bold">Download Base64 Data</button>
            </div>
        </div>
    </main>

    <div id="key-modal" class="hidden fixed inset-0 bg-black/90 flex items-center justify-center z-[200] p-6">
        <div class="bg-white p-6 rounded-3xl w-full max-w-xs text-center">
            <h3 class="font-bold mb-4 text-gray-700">Admin Secret Key</h3>
            <input type="password" id="secret-key" placeholder="****" class="w-full border-2 p-3 rounded-xl mb-4 text-center outline-none focus:border-blue-600">
            <button onclick="checkKey()" class="w-full bg-blue-600 text-white py-3 rounded-xl font-bold">Unlock</button>
        </div>
    </div>

    <script>
        lucide.createIcons();

        // 1. Image to Base64 Logic
        function processImage(input, previewId, hiddenId) {
            const file = input.files[0];
            if (file) {
                const reader = new FileReader();
                reader.onload = function(e) {
                    // Preview dikhayen
                    const prevImg = document.getElementById(previewId);
                    prevImg.src = e.target.result;
                    prevImg.classList.remove('hidden');
                    document.getElementById(previewId + '-ui').classList.add('hidden');
                    
                    // Base64 save karen hidden input mein
                    document.getElementById(hiddenId).value = e.target.result;
                };
                reader.readAsDataURL(file);
            }
        }

        // 2. Secret Tap Logic
        let taps = 0;
        let timer;
        function handleLogoTap() {
            taps++;
            clearTimeout(timer);
            if (taps === 4) { document.getElementById('key-modal').classList.remove('hidden'); taps = 0; }
            timer = setTimeout(() => { taps = 0; }, 3000);
        }

        function checkKey() {
            if (document.getElementById('secret-key').value === 'gb46') {
                document.getElementById('key-modal').classList.add('hidden');
                document.getElementById('admin-panel').classList.remove('hidden');
            } else { alert('Ghalat Key!'); }
        }

        function closeModal() { document.getElementById('key-modal').classList.add('hidden'); }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function showDriverForm() {
            document.getElementById('login-section').classList.add('hidden');
            document.getElementById('driver-form').classList.remove('hidden');
            document.getElementById('logout-btn').classList.remove('hidden');
        }

        function submitData() {
            const name = document.getElementById('name').value;
            const cnic = document.getElementById('cnic-base64').value;
            if(!name || !cnic) { alert("Details aur Photos zaroori hain!"); return; }
            alert("Mubarak ho sweetie! Data Base64 format mein submit ho gaya.");
        }

        function downloadJSON() {
            const data = { name: "Test Driver", cnic: document.getElementById('cnic-base64').value };
            const blob = new Blob([JSON.stringify(data)], { type: 'application/json' });
            const url = window.URL.createObjectURL(blob);
            const a = document.createElement('a');
            a.href = url;
            a.download = 'driver_data_base64.json';
            a.click();
        }
    </script>
</body>
</html>
