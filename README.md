<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>GB DRIVE</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-auth-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-database-compat.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Outfit:wght@400;900&display=swap');
        body { font-family: 'Outfit', sans-serif; background: #f1f5f9; }
        .hidden { display: none !important; }
        .neo-card { background: white; border-radius: 2rem; box-shadow: 0 10px 30px rgba(0,0,0,0.05); padding: 1.5rem; }
        .v-btn { border: 2px solid transparent; transition: 0.3s; background: #f8fafc; border-radius: 1rem; }
        .v-btn.selected { border-color: #2563eb; background: #eff6ff; color: #2563eb; }
        .main-title { font-weight: 900; letter-spacing: -1px; }
        input { border: 1px solid #e2e8f0 !important; border-radius: 0.75rem !important; padding: 0.75rem !important; width: 100%; margin-bottom: 1rem; }
    </style>
</head>
<body class="pb-20">

    <div id="login-screen" class="min-h-screen flex flex-col items-center justify-center p-6 text-center">
        <div onclick="adminTap()" class="bg-blue-600 text-white w-20 h-20 flex items-center justify-center rounded-3xl font-black text-4xl shadow-xl italic mb-6 cursor-pointer">GB</div>
        <h1 class="text-4xl main-title text-blue-900">GB DRIVE</h1>
        <button onclick="login()" class="mt-10 w-full max-w-xs bg-white py-4 rounded-2xl shadow-lg flex items-center justify-center gap-3 font-bold border hover:bg-gray-50 active:scale-95 transition-all">
            <img src="https://www.gstatic.com/firebasejs/ui/2.0.0/images/auth/google.svg" width="20"> CONTINUE WITH GOOGLE
        </button>
    </div>

    <div id="main-app" class="hidden">
        <header class="p-4 flex justify-between items-center bg-white border-b sticky top-0 z-50">
            <div class="flex items-center gap-3">
                <div onclick="adminTap()" class="bg-blue-600 text-white w-8 h-8 flex items-center justify-center rounded-lg font-black italic cursor-pointer text-xs">GB</div>
                <p id="u-name" class="font-bold text-sm text-blue-900"></p>
            </div>
            <button onclick="logout()" class="text-red-500"><i data-lucide="log-out"></i></button>
        </header>

        <main class="p-4 max-w-md mx-auto space-y-6">
            <div id="role-screen" class="hidden py-10 space-y-4">
                <h2 class="text-xl font-black text-center">CHOOSE YOUR ROLE</h2>
                <button onclick="setRole('passenger')" class="w-full p-6 neo-card font-bold text-lg">PASSENGER</button>
                <button onclick="setRole('driver')" class="w-full p-6 neo-card font-bold text-lg border-2 border-green-500">DRIVER</button>
            </div>

            <div id="p-ui" class="hidden space-y-4">
                <div class="neo-card">
                    <label class="text-[10px] font-black text-gray-400">PICKUP POINT</label>
                    <input id="p-from" type="text" placeholder="Enter Pickup">
                    <label class="text-[10px] font-black text-gray-400">DESTINATION</label>
                    <input id="p-to" type="text" placeholder="Enter Destination">
                    
                    <div class="grid grid-cols-3 gap-2 mb-4">
                        <button onclick="selV('bike')" id="v-bike" class="v-btn p-3 flex flex-col items-center"><i data-lucide="bike" class="w-5"></i><span class="text-[8px] font-bold mt-1">BIKE</span></button>
                        <button onclick="selV('car')" id="v-car" class="v-btn p-3 flex flex-col items-center selected"><i data-lucide="car" class="w-5"></i><span class="text-[8px] font-bold mt-1">CAR</span></button>
                        <button onclick="selV('auto')" id="v-auto" class="v-btn p-3 flex flex-col items-center"><i data-lucide="loader" class="w-5"></i><span class="text-[8px] font-bold mt-1">AUTO</span></button>
                    </div>

                    <div class="bg-blue-600 p-6 rounded-3xl text-white text-center mb-4">
                        <p id="fare-txt" class="text-4xl font-black">400</p>
                        <p class="text-[10px] font-bold opacity-70">ESTIMATED FARE (PKR)</p>
                    </div>
                    <button onclick="reqRide()" class="w-full bg-blue-900 text-white py-4 rounded-2xl font-black text-lg shadow-lg">FIND RIDE</button>
                </div>
            </div>

            <div id="d-ui" class="hidden space-y-4">
                <h3 class="font-black text-lg">LIVE RIDE REQUESTS</h3>
                <div id="d-feed" class="space-y-4"></div>
            </div>

            <div id="admin-ui" class="hidden space-y-4">
                <div class="bg-slate-900 p-6 rounded-3xl text-white">
                    <h2 class="text-xl font-black mb-4">ADMIN PANEL</h2>
                    <div class="grid grid-cols-2 gap-4">
                        <div class="bg-white/10 p-3 rounded-xl">
                            <p class="text-[8px] font-bold opacity-60">PETROL PRICE</p>
                            <input id="pet-in" type="number" onchange="updFuel(this.value)" class="bg-transparent border-none p-0 m-0 font-bold text-lg w-full outline-none">
                        </div>
                        <div class="bg-white/10 p-3 rounded-xl">
                            <p class="text-[8px] font-bold opacity-60">RIDES</p>
                            <p id="adm-count" class="text-lg font-bold">0</p>
                        </div>
                    </div>
                </div>
                <div id="admin-feed" class="space-y-3"></div>
            </div>
        </main>
    </div>

    <script>
        lucide.createIcons();
        let tapCount = 0;
        let petPrice = 280;
        let vType = 'car';

        const firebaseConfig = {
            apiKey: "AIzaSyB2etNdujWulCIa6-bk0P6yaxYPgNlzzto",
            authDomain: "vibes-643ec.firebaseapp.com",
            databaseURL: "https://vibes-643ec-default-rtdb.firebaseio.com",
            projectId: "vibes-643ec",
            storageBucket: "vibes-643ec.firebasestorage.app",
            messagingSenderId: "1073588694472",
            appId: "1:1073588694472:web:7a87ed21652f303e3164fd"
        };

        firebase.initializeApp(firebaseConfig);
        const auth = firebase.auth();
        const db = firebase.database();
        const provider = new firebase.auth.GoogleAuthProvider();

        function login() { auth.signInWithPopup(provider); }
        function logout() { auth.signOut(); location.reload(); }

        function adminTap() {
            tapCount++;
            if(tapCount === 4) {
                tapCount = 0;
                let key = prompt("ENTER SECRET KEY:");
                if(key === "gb46") showView('admin');
            }
        }

        auth.onAuthStateChanged(user => {
            if(user) {
                document.getElementById('login-screen').classList.add('hidden');
                document.getElementById('main-app').classList.remove('hidden');
                document.getElementById('u-name').innerText = user.displayName;
                db.ref('users/' + user.uid).once('value').then(snap => {
                    if(snap.exists()) showView(snap.val().role);
                    else document.getElementById('role-screen').classList.remove('hidden');
                });
            } else {
                document.getElementById('login-screen').classList.remove('hidden');
                document.getElementById('main-app').classList.add('hidden');
            }
        });

        function setRole(role) {
            db.ref('users/' + auth.currentUser.uid).set({ role, name: auth.currentUser.displayName }).then(() => location.reload());
        }

        function showView(v) {
            ['p-ui', 'd-ui', 'admin-ui', 'role-screen'].forEach(id => document.getElementById(id).classList.add('hidden'));
            if(v === 'passenger') document.getElementById('p-ui').classList.remove('hidden');
            else if(v === 'driver') { document.getElementById('d-ui').classList.remove('hidden'); sync('d-feed'); }
            else if(v === 'admin') { document.getElementById('admin-ui').classList.remove('hidden'); sync('admin-feed', true); }
        }

        function sync(id, isAdmin = false) {
            db.ref('rides').on('value', snap => {
                const f = document.getElementById(id); f.innerHTML = "";
                let count = 0;
                snap.forEach(c => {
                    const r = c.val(); count++;
                    f.innerHTML += `<div class="neo-card border-l-4 ${isAdmin ? 'border-red-500':'border-blue-600'} mb-3">
                        <p class="font-bold text-sm">${r.from} ➔ ${r.to}</p>
                        <div class="mt-4 flex justify-between items-center">
                            <span class="font-black text-green-600">PKR ${r.fare}</span>
                            <button onclick="rem('${c.key}')" class="bg-blue-600 text-white px-4 py-1 rounded-lg text-xs font-bold">${isAdmin ? 'DELETE':'ACCEPT'}</button>
                        </div>
                    </div>`;
                });
                if(isAdmin) document.getElementById('adm-count').innerText = count;
            });
        }

        function rem(rid) { db.ref('rides/' + rid).remove(); }
        function updFuel(v) { db.ref('settings/petrol').set(v); }
        db.ref('settings/petrol').on('value', snap => { petPrice = snap.val() || 280; document.getElementById('pet-in').value = petPrice; });

        function selV(t) {
            vType = t;
            document.querySelectorAll('.v-btn').forEach(b => b.classList.remove('selected'));
            document.getElementById('v-'+t).classList.add('selected');
            let base = t === 'bike' ? 150 : (t === 'auto' ? 250 : 400);
            document.getElementById('fare-txt').innerText = Math.round(base * (petPrice/280));
        }

        function reqRide() {
            const f = document.getElementById('p-from').value;
            const t = document.getElementById('p-to').value;
            if(!f || !t) return alert("Fill details!");
            db.ref('rides').push({ from: f, to: t, fare: document.getElementById('fare-txt').innerText, vType, user: auth.currentUser.displayName });
            alert("RIDE POSTED!");
        }
    </script>
</body>
</html>
