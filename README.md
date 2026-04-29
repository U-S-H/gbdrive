<html lang="ur">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>GB Drive - The Final Master</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-auth-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-database-compat.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Outfit:wght@300;600;900&display=swap');
        body { font-family: 'Outfit', sans-serif; background: #f1f5f9; color: #1e293b; }
        .hidden { display: none; }
        .glass { background: rgba(255, 255, 255, 0.9); backdrop-filter: blur(12px); border-bottom: 1px solid #e2e8f0; }
        .neo-card { background: white; border-radius: 2.5rem; box-shadow: 0 10px 30px rgba(0,0,0,0.05); }
        .btn-blue { background: #1e40af; color: white; transition: all 0.2s; }
        .btn-blue:active { transform: scale(0.95); }
    </style>
</head>
<body class="pb-24">

    <div id="login-screen" class="min-h-screen flex flex-col items-center justify-center p-6 space-y-10">
        <div class="text-center">
            <div class="bg-blue-600 text-white w-24 h-24 flex items-center justify-center rounded-[2.5rem] font-black text-5xl mx-auto shadow-2xl italic animate-bounce">GB</div>
            <h1 class="text-4xl font-black text-blue-900 mt-6 tracking-tight">GB DRIVE</h1>
            <p class="text-gray-400 font-medium">Safar asaan, mehfooz aur apna!</p>
        </div>
        <button onclick="login()" class="w-full max-w-xs bg-white text-gray-700 font-black py-5 px-6 rounded-3xl shadow-xl flex items-center justify-center gap-4 border border-gray-100 btn-blue bg-white !text-gray-800 hover:shadow-2xl">
            <img src="https://www.gstatic.com/firebasejs/ui/2.0.0/images/auth/google.svg" width="22">
            Continue with Google
        </button>
    </div>

    <div id="role-screen" class="hidden min-h-screen flex flex-col items-center justify-center p-6 space-y-8">
        <div class="text-center space-y-2">
            <h2 class="text-3xl font-black text-blue-900">Aap kaun hain?</h2>
            <p class="text-gray-400">Select karein taake dashboard khul sakay</p>
        </div>
        <div class="w-full max-w-sm space-y-4">
            <button onclick="setRole('passenger')" class="w-full p-8 neo-card border-4 border-transparent hover:border-blue-600 transition-all flex items-center gap-6 group">
                <div class="bg-blue-100 p-4 rounded-2xl text-blue-600 group-hover:bg-blue-600 group-hover:text-white"><i data-lucide="user-round"></i></div>
                <div class="text-left"><p class="font-black text-xl text-blue-900">Passenger</p><p class="text-xs text-gray-400 font-bold uppercase">Ride leni hai</p></div>
            </button>
            <button onclick="setRole('driver')" class="w-full p-8 neo-card border-4 border-transparent hover:border-blue-600 transition-all flex items-center gap-6 group">
                <div class="bg-green-100 p-4 rounded-2xl text-green-600 group-hover:bg-green-600 group-hover:text-white"><i data-lucide="car"></i></div>
                <div class="text-left"><p class="font-black text-xl text-blue-900">Driver</p><p class="text-xs text-gray-400 font-bold uppercase">Gari chalani hai</p></div>
            </button>
        </div>
    </div>

    <div id="main-app" class="hidden">
        <header class="p-4 flex justify-between items-center glass sticky top-0 z-[100]">
            <div class="flex items-center gap-3">
                <img id="u-img" src="" class="w-10 h-10 rounded-full border-2 border-blue-600 shadow-md">
                <div>
                    <p id="u-name" class="font-black text-blue-900 text-sm leading-none"></p>
                    <p id="u-role" class="text-[8px] font-bold text-blue-400 uppercase tracking-widest mt-1"></p>
                </div>
            </div>
            <button onclick="logout()" class="p-2 text-red-400 bg-red-50 rounded-xl"><i data-lucide="log-out" class="w-5 h-5"></i></button>
        </header>

        <main class="p-4 max-w-md mx-auto space-y-6">
            <div id="p-ui" class="hidden space-y-4">
                <div class="neo-card p-6 space-y-4 border-b-8 border-blue-600">
                    <h3 class="text-xl font-black text-blue-900">Kahan jana hai sweetie?</h3>
                    <div class="space-y-2">
                        <input id="from" type="text" placeholder="Pickup Location" class="w-full p-4 bg-gray-50 rounded-2xl outline-none font-bold text-sm border border-gray-100">
                        <input id="to" type="text" placeholder="Destination" class="w-full p-4 bg-gray-50 rounded-2xl outline-none font-bold text-sm border border-gray-100">
                    </div>
                    <div class="bg-blue-600 p-6 rounded-[2rem] text-white text-center shadow-lg">
                        <p class="text-[10px] font-black opacity-60 mb-2">OFFER FARE (PKR)</p>
                        <div class="flex items-center justify-between px-6">
                            <button onclick="adjFare(-50)" class="text-2xl font-bold w-10 h-10 bg-white/20 rounded-full">-</button>
                            <span id="fare" class="text-4xl font-black">400</span>
                            <button onclick="adjFare(50)" class="text-2xl font-bold w-10 h-10 bg-white/20 rounded-full">+</button>
                        </div>
                    </div>
                    <button onclick="postRide()" class="w-full bg-blue-900 text-white py-5 rounded-[2.5rem] font-black text-xl shadow-xl btn-blue">FIND DRIVER</button>
                </div>
            </div>

            <div id="d-ui" class="hidden space-y-4">
                <div class="flex justify-between items-center px-2">
                    <h3 class="text-xl font-black">Live Requests</h3>
                    <span class="flex items-center gap-1 text-[10px] font-black text-green-500 animate-pulse"><i data-lucide="radio" class="w-3"></i> ONLINE</span>
                </div>
                <div id="ride-feed" class="space-y-4"></div>
            </div>
        </main>
    </div>

    <script>
        lucide.createIcons();
        let currentFare = 400;

        // Firebase Setup
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
        function adjFare(v) { currentFare += v; if(currentFare < 150) currentFare = 150; document.getElementById('fare').innerText = currentFare; }

        auth.onAuthStateChanged(user => {
            if(user) {
                document.getElementById('login-screen').classList.add('hidden');
                db.ref('users/' + user.uid).once('value').then(snap => {
                    if(snap.exists()) showApp(user, snap.val().role);
                    else document.getElementById('role-screen').classList.remove('hidden');
                });
            } else {
                document.getElementById('login-screen').classList.remove('hidden');
            }
        });

        function setRole(role) {
            const u = auth.currentUser;
            db.ref('users/' + u.uid).set({ name: u.displayName, role: role }).then(() => location.reload());
        }

        function showApp(user, role) {
            document.getElementById('role-screen').classList.add('hidden');
            document.getElementById('main-app').classList.remove('hidden');
            document.getElementById('u-name').innerText = user.displayName;
            document.getElementById('u-img').src = user.photoURL;
            document.getElementById('u-role').innerText = role;
            if(role === 'passenger') document.getElementById('p-ui').classList.remove('hidden');
            else { document.getElementById('d-ui').classList.remove('hidden'); listenRides(); }
        }

        function postRide() {
            const f = document.getElementById('from').value;
            const t = document.getElementById('to').value;
            if(!f || !t) return alert("Pehle location dalo sweetie! 😘");
            db.ref('rides').push({ from: f, to: t, fare: currentFare, user: auth.currentUser.displayName, time: new Date().toLocaleTimeString() });
            alert("Ride request bhej di gayi hai! 🔥");
        }

        function listenRides() {
            db.ref('rides').on('value', snap => {
                const feed = document.getElementById('ride-feed');
                feed.innerHTML = "";
                snap.forEach(child => {
                    const r = child.val();
                    feed.innerHTML += `
                        <div class="neo-card p-6 border-l-8 border-blue-600 animate-in slide-in-from-right">
                            <div class="flex justify-between mb-3"><span class="text-[10px] font-black text-blue-600 bg-blue-50 px-2 py-1 rounded-full uppercase">${r.time}</span></div>
                            <p class="font-black text-gray-800 text-lg">${r.from} ➔ ${r.to}</p>
                            <p class="text-xs text-gray-400 font-bold italic">Passenger: ${r.user}</p>
                            <div class="mt-5 flex items-center justify-between">
                                <span class="text-2xl font-black text-green-600">Rs ${r.fare}</span>
                                <button onclick="accept('${child.key}')" class="bg-gray-900 text-white px-8 py-3 rounded-2xl font-black text-xs btn-blue">ACCEPT</button>
                            </div>
                        </div>`;
                });
                if(!snap.exists()) feed.innerHTML = '<p class="text-center py-20 text-gray-400 font-bold">Abhi koi ride nahi hai... 🚗</p>';
            });
        }

        function accept(id) { alert("Ride Accepted! Safar shuru karein. 🚗"); db.ref('rides/' + id).remove(); }
    </script>
</body>
</html>
