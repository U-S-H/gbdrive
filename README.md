<html lang="ur">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>GB Drive - Auth Edition</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-auth-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-database-compat.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Outfit:wght@300;600;900&display=swap');
        body { font-family: 'Outfit', sans-serif; background: #f8fafc; }
        .hidden { display: none; }
        .neo-card { background: white; border-radius: 2.5rem; box-shadow: 0 15px 30px rgba(0,0,0,0.05); }
    </style>
</head>
<body class="pb-20">

    <div id="auth-section" class="min-h-screen flex flex-col items-center justify-center p-6 space-y-6">
        <div class="text-center">
            <div class="bg-blue-600 text-white w-16 h-16 flex items-center justify-center rounded-2xl font-black text-3xl mx-auto shadow-xl italic">GB</div>
            <h1 class="text-3xl font-black text-blue-900 mt-4">Safar Shuru Karein</h1>
            <p class="text-gray-400 text-sm">Please select your account type</p>
        </div>

        <div class="w-full max-w-sm space-y-4">
            <div class="flex gap-4 mb-6">
                <button onclick="setRole('passenger')" id="role-p" class="flex-1 py-4 rounded-2xl font-black border-2 border-blue-600 bg-blue-50 text-blue-600">PASSENGER</button>
                <button onclick="setRole('driver')" id="role-d" class="flex-1 py-4 rounded-2xl font-black border-2 border-gray-100 text-gray-400">DRIVER</button>
            </div>

            <input id="auth-email" type="email" placeholder="Email Address" class="w-full p-5 rounded-2xl bg-white shadow-sm outline-none border-none font-bold">
            <input id="auth-pass" type="password" placeholder="Password" class="w-full p-5 rounded-2xl bg-white shadow-sm outline-none border-none font-bold">
            
            <div class="grid grid-cols-2 gap-4">
                <button onclick="handleAuth('login')" class="bg-blue-900 text-white py-4 rounded-2xl font-black shadow-lg active:scale-95 transition-all">LOGIN</button>
                <button onclick="handleAuth('signup')" class="bg-white text-blue-900 py-4 rounded-2xl font-black border border-blue-100 shadow-sm active:scale-95 transition-all">SIGNUP</button>
            </div>
        </div>
    </div>

    <div id="app-section" class="hidden">
        <header class="p-4 flex justify-between items-center glass sticky top-0 z-[100] bg-white/80 backdrop-blur-md">
            <div class="flex items-center gap-2">
                <div class="bg-blue-600 text-white p-1.5 rounded-lg font-black italic">GB</div>
                <p class="font-black text-blue-900" id="user-display-name">Welcome!</p>
            </div>
            <button onclick="logout()" class="text-gray-400"><i data-lucide="log-out" class="w-5 h-5"></i></button>
        </header>

        <main class="p-4 max-w-md mx-auto">
            <div id="p-view" class="hidden space-y-6">
                <div class="neo-card p-6 space-y-4 border-b-8 border-blue-600">
                    <h2 class="font-black text-xl text-blue-900">Book Your Ride</h2>
                    <input id="ride-from" type="text" placeholder="Pickup point" class="w-full p-4 bg-gray-50 rounded-2xl outline-none font-bold text-sm">
                    <input id="ride-to" type="text" placeholder="Destination" class="w-full p-4 bg-gray-50 rounded-2xl outline-none font-bold text-sm">
                    <button onclick="postRide()" class="w-full bg-blue-900 text-white py-5 rounded-[2rem] font-black text-xl shadow-xl">FIND DRIVER</button>
                </div>
            </div>

            <div id="d-view" class="hidden space-y-4">
                <h2 class="font-black text-xl px-2">Available Requests</h2>
                <div id="ride-list" class="space-y-4">
                    <p class="text-center py-10 text-gray-400">Searching for rides...</p>
                </div>
            </div>
        </main>
    </div>

    <script>
        lucide.createIcons();

        // FIREBASE CONFIG
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
        let selectedRole = 'passenger';

        function setRole(role) {
            selectedRole = role;
            document.getElementById('role-p').className = role === 'passenger' ? 'flex-1 py-4 rounded-2xl font-black border-2 border-blue-600 bg-blue-50 text-blue-600' : 'flex-1 py-4 rounded-2xl font-black border-2 border-gray-100 text-gray-400';
            document.getElementById('role-d').className = role === 'driver' ? 'flex-1 py-4 rounded-2xl font-black border-2 border-blue-600 bg-blue-50 text-blue-600' : 'flex-1 py-4 rounded-2xl font-black border-2 border-gray-100 text-gray-400';
        }

        function handleAuth(type) {
            const email = document.getElementById('auth-email').value;
            const pass = document.getElementById('auth-pass').value;
            if(!email || !pass) { alert("Details fill karein!"); return; }

            if(type === 'signup') {
                auth.createUserWithEmailAndPassword(email, pass).then(cred => {
                    db.ref('users/' + cred.user.uid).set({ email: email, role: selectedRole });
                    alert("Account Created! Login karein.");
                }).catch(err => alert(err.message));
            } else {
                auth.signInWithEmailAndPassword(email, pass).catch(err => alert(err.message));
            }
        }

        auth.onAuthStateChanged(user => {
            if(user) {
                document.getElementById('auth-section').classList.add('hidden');
                document.getElementById('app-section').classList.remove('hidden');
                db.ref('users/' + user.uid).once('value').then(snap => {
                    const data = snap.val();
                    if(data.role === 'driver') {
                        document.getElementById('d-view').classList.remove('hidden');
                        loadRides();
                    } else {
                        document.getElementById('p-view').classList.remove('hidden');
                    }
                });
            } else {
                document.getElementById('auth-section').classList.remove('hidden');
                document.getElementById('app-section').classList.add('hidden');
            }
        });

        function postRide() {
            const from = document.getElementById('ride-from').value;
            const to = document.getElementById('ride-to').value;
            db.ref('active_rides').push({ from, to, status: 'pending', user: auth.currentUser.uid });
            alert("Ride Posted!");
        }

        function loadRides() {
            db.ref('active_rides').on('value', snap => {
                const list = document.getElementById('ride-list');
                list.innerHTML = "";
                snap.forEach(child => {
                    const r = child.val();
                    list.innerHTML += `
                        <div class="neo-card p-5 border-l-8 border-blue-600">
                            <p class="font-black">${r.from} ➔ ${r.to}</p>
                            <button class="mt-3 bg-blue-900 text-white w-full py-2 rounded-xl text-xs font-bold">ACCEPT RIDE</button>
                        </div>`;
                });
            });
        }

        function logout() { auth.signOut(); location.reload(); }
    </script>
</body>
</html>
