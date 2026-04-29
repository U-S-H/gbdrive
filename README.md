<html lang="ur">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>GB Drive - Gilgit-Baltistan</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    <style>
        body { background-color: #f8fafc; }
        .hidden { display: none; }
    </style>
</head>
<body class="font-sans">

    <header class="bg-white p-4 shadow-sm flex justify-between items-center sticky top-0 z-50">
        <div id="logo-trigger" onclick="handleLogoTap()" class="cursor-pointer flex items-center gap-1 select-none active:scale-95 transition-transform">
            <span class="bg-blue-600 text-white px-2 py-1 rounded font-bold text-lg">GB</span>
            <span class="text-blue-900 font-extrabold text-lg tracking-tight">DRIVE</span>
        </div>
        <div id="logout-btn" class="hidden">
            <button onclick="location.reload()" class="text-gray-400 p-2"><i data-lucide="log-out"></i></button>
        </div>
    </header>

    <main class="max-w-md mx-auto p-6 flex flex-col items-center justify-center min-h-[80vh]">
        
        <div id="login-section" class="text-center w-full">
            <h1 class="text-3xl font-black text-blue-900 mb-2">GB DRIVE</h1>
            <p class="text-gray-500 mb-8 text-sm">Gilgit-Baltistan ki apni mehfooz ride service</p>
            <button onclick="showDriverForm()" class="w-full bg-white border-2 border-gray-100 flex items-center justify-center gap-3 py-4 rounded-3xl shadow-xl font-bold hover:bg-gray-50 transition-all active:scale-95">
                <img src="https://www.gstatic.com/firebasejs/ui/2.0.0/images/auth/google.svg" width="20">
                Google se Login Karen
            </button>
        </div>

        <div id="driver-form" class="hidden w-full space-y-4">
            <h2 class="text-xl font-bold text-gray-800 text-center mb-4">Driver Registration</h2>
            <input type="text" placeholder="Asli Naam" class="w-full p-4 rounded-2xl bg-white border border-gray-200 outline-none focus:border-blue-500">
            <input type="tel" placeholder="Mobile Number" class="w-full p-4 rounded-2xl bg-white border border-gray-200 outline-none focus:border-blue-500">
            <input type="text" placeholder="Gari ka Number (e.g. GBA-1234)" class="w-full p-4 rounded-2xl bg-white border border-gray-200 outline-none font-mono focus:border-blue-500">
            
            <div class="grid grid-cols-2 gap-3">
                <div class="border-2 border-dashed border-gray-300 rounded-2xl p-4 text-center bg-white cursor-pointer hover:bg-blue-50 transition-colors">
                    <i data-lucide="credit-card" class="mx-auto text-gray-400 mb-1"></i>
                    <p class="text-[10px] font-bold text-gray-400 uppercase">CNIC Photo</p>
                </div>
                <div class="border-2 border-dashed border-gray-300 rounded-2xl p-4 text-center bg-white cursor-pointer hover:bg-blue-50 transition-colors">
                    <i data-lucide="camera" class="mx-auto text-gray-400 mb-1"></i>
                    <p class="text-[10px] font-bold text-gray-400 uppercase">Live Selfie</p>
                </div>
            </div>
            
            <button onclick="alert('Data Submitted Sweetie!')" class="w-full bg-blue-600 text-white py-4 rounded-2xl font-bold shadow-lg shadow-blue-200 active:scale-95 transition-transform">
                Complete Registration
            </button>
        </div>

        <div id="admin-panel" class="hidden fixed inset-0 bg-white z-[100] p-6 overflow-y-auto">
            <div class="flex justify-between items-center mb-8">
                <h2 class="text-2xl font-black text-blue-900 underline underline-offset-4 decoration-blue-200">Admin Dashboard</h2>
                <button onclick="closeAdmin()" class="bg-gray-100 p-2 rounded-full font-bold">X</button>
            </div>
            <button onclick="downloadData()" class="w-full bg-green-600 text-white py-4 rounded-2xl font-bold flex items-center justify-center gap-2 mb-6">
                <i data-lucide="download"></i> Download All Driver Data
            </button>
            <div id="driver-list" class="space-y-4">
                <p class="text-center text-gray-400">Filhal koi data mojood nahi hai.</p>
            </div>
        </div>

    </main>

    <div id="key-modal" class="hidden fixed inset-0 bg-black/90 flex items-center justify-center z-[200] p-6">
        <div class="bg-white p-6 rounded-3xl w-full max-w-xs shadow-2xl">
            <h3 class="text-center font-bold text-gray-700 mb-4">Verification Required</h3>
            <input type="password" id="secret-key" placeholder="Enter Key" class="w-full border-2 p-3 rounded-xl mb-4 text-center outline-none focus:border-blue-600">
            <button onclick="checkKey()" class="w-full bg-blue-600 text-white py-3 rounded-xl font-bold active:scale-95">Unlock Admin</button>
            <button onclick="closeModal()" class="w-full text-gray-400 text-sm mt-3">Cancel</button>
        </div>
    </div>

    <script>
        // Icons Load
        lucide.createIcons();

        let taps = 0;
        let timer;

        // Logo Tapping Logic
        function handleLogoTap() {
            taps++;
            clearTimeout(timer);
            if (taps === 4) {
                document.getElementById('key-modal').classList.remove('hidden');
                taps = 0;
            }
            timer = setTimeout(() => { taps = 0; }, 3000); // 3 sec reset
        }

        // Key Check logic
        function checkKey() {
            const key = document.getElementById('secret-key').value;
            if (key === 'gb46') {
                document.getElementById('key-modal').classList.add('hidden');
                document.getElementById('admin-panel').classList.remove('hidden');
            } else {
                alert('Ghalat Key Sweetie! Koshish na karen.');
                closeModal();
            }
        }

        function closeModal() {
            document.getElementById('key-modal').classList.add('hidden');
            document.getElementById('secret-key').value = '';
        }

        function closeAdmin() {
            document.getElementById('admin-panel').classList.add('hidden');
        }

        function showDriverForm() {
            document.getElementById('login-section').classList.add('hidden');
            document.getElementById('driver-form').classList.remove('hidden');
            document.getElementById('logout-btn').classList.remove('hidden');
        }

        function downloadData() {
            const csvData = "Name,Phone,Vehicle_Number\\nAli Ahmed,03451234567,GB-9900\\nZubair Khan,03409876543,GBA-4422";
            const blob = new Blob([csvData], { type: 'text/csv' });
            const url = window.URL.createObjectURL(blob);
            const a = document.createElement('a');
            a.href = url;
            a.download = 'GB_Drive_Drivers.csv';
            a.click();
        }
    </script>
</body>
</html>
