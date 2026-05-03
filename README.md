<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>GB DRIVE PRO | Elite</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-auth-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-database-compat.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Outfit:wght@300;600;900&display=swap');
        * { font-family: 'Outfit', sans-serif; -webkit-tap-highlight-color: transparent; box-sizing: border-box; }
        body { background: #f8fafc; color: #0f172a; }
        .hidden { display: none !important; }
        .glass-card { background: white; border-radius: 28px; border: 1px solid #f1f5f9; box-shadow: 0 10px 25px rgba(0,0,0,0.03); }
        #map { height: 260px; width: 100%; border-radius: 24px; z-index: 10; margin-bottom: 1rem; }
        .input-group { background: #f1f5f9; border-radius: 18px; padding: 12px 16px; margin-bottom: 10px; display: flex; align-items: center; gap: 10px; }
        .input-group input { background: transparent; border: none; width: 100%; outline: none; font-weight: 600; font-size: 14px; }
        .role-btn { transition: 0.3s; border: 2px solid transparent; }
        .role-btn:hover { border-color: #2563eb; transform: translateY(-3px); }
        .btn-main { background: #2563eb; color: white; border-radius: 20px; padding: 18px; width: 100%; font-weight: 900; text-transform: uppercase; shadow: 0 10px 20px rgba(37,99,235,0.2); }
    </style>
</head>
<body class="pb-10">

    <div id="loader" class="fixed inset-0 z-[9999] bg-white flex items-center justify-center">
        <div class="animate-spin rounded-full h-12 w-12 border-t-4 border-blue-600"></div>
    </div>

    <div id="login-screen" class="hidden fixed inset-0 z-[5000] bg-white flex flex-col items-center justify-center p-8">
        <div class="bg-blue-600 text-white w-20 h-20 flex items-center justify-center rounded-[28px] font-black italic text-4xl mb-6 shadow-xl">GB</div>
        <h1 class="text-3xl font-black mb-2">GB DRIVE PRO</h1>
        <p class="text-slate-400 font-bold text-[10px] uppercase tracking-[0.3em] mb-12 text-center">Your Region's Premier Network</p>
        <button onclick="loginWithGoogle()" class="w-full max-w-xs flex items-center justify-center gap-4 border-2 border-slate-100 p-4 rounded-2xl font-black text-slate-700 active:scale-95 transition-all">
            <img src="https://www.google.com/favicon.ico" class="w-5 h-5"> Continue with Google
        </button>
    </div>

    <div id="main-app" class="hidden">
        <header class="p-4 flex justify-between items-center bg-white/80 backdrop-blur-md sticky top-0 z-[1000]">
            <div class="flex items-center gap-3">
                <div class="bg-blue-600 text-white w-9 h-9 flex items-center justify-center rounded-xl font-black italic">GB</div>
                <div>
                    <p id="display-name" class="font-black text-xs uppercase text-slate-800">User</p>
                    <p id="display-role" class="text-[8px] font-black text-blue-600 uppercase tracking-widest">Active</p>
                </div>
            </div>
            <button onclick="handleLogout()" class="p-2.5 bg-red-50 text-red-500 rounded-xl"><i data-lucide="log-out" class="w-4"></i></button>
        </header>

        <main class="p-4 max-w-md mx-auto space-y-4">
            
            <div id="role-selection" class="hidden space-y-4 py-10">
                <h2 class="text-2xl font-black text-slate-900 leading-tight">Welcome, sweetie!<br>How will you use the app?</h2>
                <div class="grid grid-cols-1 gap-4">
                    <button onclick="setupRole('passenger')" class="role-btn glass-card p-6 text-left flex justify-between items-center">
                        <div><b class="text-xl block">Passenger</b><span class="text-[10px] font-bold text-slate-400 uppercase">I want to book rides</span></div>
                        <i data-lucide="user" class="text-blue-600 w-8"></i>
                    </button>
                    <button onclick="document.getElementById('driver-reg-modal').classList.remove('hidden')" class="role-btn bg-slate-900 text-white p-6 rounded-[28px] text-left flex justify-between items-center">
                        <div><b class="text-xl block text-white">Driver</b><span class="text-[10px] font-bold text-slate-500 uppercase">I want to earn money</span></div>
                        <i data-lucide="car" class="text-white w-8"></i>
                    </button>
                </div>
            </div>

            <div id="passenger-dashboard" class="hidden space-y-4">
                <div id="map"></div>
                <div class="glass-card p-6 space-y-4">
                    <div class="input-group"><i data-lucide="map-pin" class="w-4 text-blue-500"></i><input id="p-from" placeholder="Pickup point"></div>
                    <div class="input-group"><i data-lucide="navigation" class="w-4 text-green-500"></i><input id="p-to" placeholder="Destination"></div>
                    <div class="bg-slate-50 p-4 rounded-2xl flex justify-between items-center">
                        <span class="text-[10px] font-black text-slate-400 uppercase">Estimated Fare</span>
                        <input id="p-fare" type="number" value="300" class="bg-transparent text-right font-black text-xl text-blue-600 w-24 outline-none">
                    </div>
                    <button onclick="requestRide()" class="btn-main">Request Ride</button>
                </div>
            </div>

            <div id="driver-dashboard" class="hidden space-y-4">
                <div class="flex justify-between items-center px-1">
                    <h3 class="text-[10px] font-black text-slate-400 uppercase tracking-[0.2em]">Live Requests</h3>
                    <div class="w-2 h-2 bg-green-500 rounded-full animate-ping"></div>
                </div>
                <div id="ride-requests-feed" class="space-y-4">
                    </div>
            </div>

        </main>
    </div>

    <div id="driver-reg-modal" class="hidden fixed inset-0 z-[6000] bg-white p-8 overflow-y-auto">
        <h2 class="text-3xl font-black mb-8">Driver Profile</h2>
        <div class="space-y-4">
            <div class="input-group"><input id="d-name" placeholder="Full Name"></div>
            <div class="input-group"><input id="d-vehicle" placeholder="Vehicle Model (e.g. Civic)"></div>
            <div class="input-group"><input id="d-plate" placeholder="License Plate No."></div>
            <div class="input-group"><input id="d-phone" placeholder="WhatsApp Number"></div>
            <button onclick="setupRole('driver')" class="btn-main mt-6">Complete Setup</button>
            <button onclick="document.getElementById('driver-reg-modal').classList.add('hidden')" class="w-full text-slate-400 font-bold mt-4 uppercase text-[10px]">Back</button>
        </div>
    </div>

    <script>
        lucide.createIcons();
        const fbConfig = {
            apiKey: "AIzaSyB2etNdujWulCIa6-bk0P6yaxYPgNlzzto",
            authDomain: "vibes-643ec.firebaseapp.com",
            databaseURL: "https://vibes-643ec-default-rtdb.firebaseio.com",
            projectId: "vibes-643ec",
            storageBucket: "vibes-643ec.firebasestorage.app",
            messagingSenderId: "1073588694472",
            appId: "1:1073588694472:web:7a87ed21652f303e3164fd"
        };
        firebase.initializeApp(fbConfig);
        const auth = firebase.auth(); const db = firebase.database();

        // --- AUTO-REDIRECT LOGIC ---
        auth.onAuthStateChanged(user => {
            const loader = document.getElementById('loader');
            if(user) {
                document.getElementById('display-name').innerText = user.displayName;
                // Check database for existing role
                db.ref('users/' + user.uid).once('value', snap => {
                    loader.classList.add('hidden');
                    const data = snap.val();
                    if(data && data.role) {
                        launchApp(data.role);
                    } else {
                        showScreen('role-selection');
                    }
                });
            } else {
                loader.classList.add('hidden');
                showScreen('login-screen');
            }
        });

        function launchApp(role) {
            showScreen('main-app');
            document.getElementById('display-role').innerText = role;
            if(role === 'passenger') {
                document.getElementById('passenger-dashboard').classList.remove('hidden');
                initMap();
            } else {
                document.getElementById('driver-dashboard').classList.remove('hidden');
                syncRequests();
            }
        }

        function setupRole(role) {
            const uid = auth.currentUser.uid;
            let userData = { role: role, name: auth.currentUser.displayName };

            if(role === 'driver') {
                userData.vModel = document.getElementById('d-vehicle').value;
                userData.plate = document.getElementById('d-plate').value;
                userData.phone = document.getElementById('d-phone').value;
                if(!userData.plate || !userData.phone) return alert("Fill all fields, sweetie!");
            }

            db.ref('users/' + uid).set(userData).then(() => location.reload());
        }

        // --- UI HELPERS ---
        function showScreen(id) {
            ['login-screen', 'main-app', 'role-selection'].forEach(s => document.getElementById(s).classList.add('hidden'));
            document.getElementById(id).classList.remove('hidden');
        }

        function loginWithGoogle() { auth.signInWithPopup(new firebase.auth.GoogleAuthProvider()); }
        function handleLogout() { auth.signOut().then(() => location.reload()); }

        // --- MAP ---
        function initMap() {
            let m = L.map('map', {zoomControl: false}).setView([35.9208, 74.3089], 14);
            L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png').addTo(m);
            L.marker([35.9208, 74.3089]).addTo(m);
        }

        // --- RIDE SYSTEM ---
        function requestRide() {
            const f = document.getElementById('p-from').value;
            const t = document.getElementById('p-to').value;
            if(!f || !t) return alert("Enter route!");
            
            const rideId = db.ref('rides').push().key;
            db.ref('rides/' + rideId).set({
                f, t, fare: document.getElementById('p-fare').value,
                pUid: auth.currentUser.uid, pName: auth.currentUser.displayName,
                status: 'searching'
            });
            alert("Searching for drivers, sweetie...");
        }

        function syncRequests() {
            db.ref('rides').on('value', snap => {
                const feed = document.getElementById('ride-requests-feed');
                feed.innerHTML = "";
                snap.forEach(c => {
                    const r = c.val();
                    if(r.status === 'searching') {
                        feed.innerHTML += `
                        <div class="glass-card p-5 border-l-8 border-blue-600">
                            <div class="flex justify-between mb-2">
                                <b class="text-sm uppercase">${r.f} ➔ ${r.t}</b>
                                <span class="text-green-600 font-black">Rs ${r.fare}</span>
                            </div>
                            <button class="w-full bg-slate-900 text-white py-3 rounded-xl text-[10px] font-black uppercase">Accept Ride</button>
                        </div>`;
                    }
                });
            });
        }
    </script>
</body>
</html>
