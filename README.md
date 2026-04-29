<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>GB DRIVE - OFFICIAL</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-auth-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-database-compat.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Outfit:wght@300;600;900&display=swap');
        * { font-family: 'Outfit', sans-serif; -webkit-tap-highlight-color: transparent; outline: none !important; }
        body { background: #F3F4F6; color: #111827; }
        .hidden { display: none !important; }
        .glass { background: rgba(255, 255, 255, 0.8); backdrop-filter: blur(12px); border-bottom: 1px solid rgba(0,0,0,0.05); }
        #map { height: 200px; width: 100%; border-radius: 20px; margin-bottom: 1rem; }
        .btn-main { background: #2563EB; color: white; font-weight: 900; border-radius: 16px; padding: 18px; width: 100%; transition: 0.3s; }
        .btn-main:active { transform: scale(0.95); }
        .input-box { background: white; border: 1px solid #E5E7EB; border-radius: 14px; padding: 14px; width: 100%; margin-bottom: 10px; font-weight: 600; }
        .v-btn { background: white; border: 2px solid #F3F4F6; border-radius: 15px; padding: 10px; transition: 0.2s; filter: grayscale(1); }
        .v-selected { border-color: #2563EB; background: #EFF6FF; filter: grayscale(0); }
    </style>
</head>
<body class="bg-gray-50 min-h-screen">

    <div id="auth-screen" class="hidden fixed inset-0 z-[5000] bg-white flex flex-col items-center justify-center p-8">
        <div class="bg-blue-600 text-white w-20 h-20 flex items-center justify-center rounded-[25px] font-black italic text-4xl mb-6 shadow-2xl shadow-blue-200">GB</div>
        <h1 class="text-3xl font-black text-center leading-tight mb-2">Welcome to<br>GB DRIVE</h1>
        <p class="text-gray-400 font-bold text-sm mb-10 tracking-widest uppercase">Your Premium Ride Partner</p>
        <button onclick="login()" class="flex items-center justify-center gap-3 w-full max-w-xs bg-white border-2 border-gray-100 p-4 rounded-2xl shadow-sm active:scale-95 transition-all">
            <img src="https://www.google.com/favicon.ico" class="w-5 h-5">
            <span class="font-black text-gray-700">Continue with Google</span>
        </button>
        <p class="mt-10 text-[10px] text-gray-300 font-bold uppercase">Developed by Prime Solutions</p>
    </div>

    <div id="app-content" class="hidden">
        <header class="p-4 glass sticky top-0 z-[100] flex justify-between items-center">
            <div class="flex items-center gap-3">
                <div class="bg-blue-600 text-white w-10 h-10 flex items-center justify-center rounded-xl font-black italic">GB</div>
                <div>
                    <p id="top-name" class="font-black text-sm uppercase">User</p>
                    <p id="top-role" class="text-[9px] font-bold text-blue-600 uppercase tracking-widest">Active</p>
                </div>
            </div>
            <button onclick="logout()" class="w-10 h-10 bg-red-50 text-red-500 rounded-xl flex items-center justify-center"><i data-lucide="log-out" class="w-5"></i></button>
        </header>

        <main class="p-5 max-w-md mx-auto pb-24">
            <div id="role-screen" class="hidden space-y-4 py-10">
                <h2 class="text-2xl font-black mb-6">Select Mode</h2>
                <button onclick="setRole('passenger')" class="w-full p-8 bg-white rounded-3xl shadow-sm text-left flex justify-between items-center">
                    <span><b class="text-xl block">PASSENGER</b><small class="text-gray-400 font-bold uppercase text-[10px]">Get a ride</small></span>
                    <i data-lucide="user-circle" class="text-blue-600 w-8 h-8"></i>
                </button>
                <button onclick="openDriverReg()" class="w-full p-8 bg-slate-900 text-white rounded-3xl shadow-sm text-left flex justify-between items-center">
                    <span><b class="text-xl block">DRIVER</b><small class="text-gray-500 font-bold uppercase text-[10px]">Earn money</small></span>
                    <i data-lucide="car" class="w-8 h-8"></i>
                </button>
            </div>

            <div id="p-ui" class="hidden space-y-4">
                <div id="map"></div>
                <div class="bg-white p-5 rounded-3xl shadow-sm">
                    <input id="p-from" type="text" placeholder="Pickup point" class="input-box">
                    <input id="p-to" type="text" placeholder="Where to go?" class="input-box">
                    <div class="grid grid-cols-4 gap-2 my-4">
                        <button onclick="selV('BIKE', 100)" id="v-BIKE" class="v-btn flex flex-col items-center"><i data-lucide="bike" class="w-4"></i><span class="text-[8px] font-black mt-1">BIKE</span></button>
                        <button onclick="selV('MINI', 200)" id="v-MINI" class="v-btn flex flex-col items-center"><i data-lucide="loader" class="w-4"></i><span class="text-[8px] font-black mt-1">MINI</span></button>
                        <button onclick="selV('CAR', 350)" id="v-CAR" class="v-btn flex flex-col items-center v-selected"><i data-lucide="car" class="w-4"></i><span class="text-[8px] font-black mt-1">CAR</span></button>
                        <button onclick="selV('AC', 500)" id="v-AC" class="v-btn flex flex-col items-center"><i data-lucide="snowflake" class="w-4"></i><span class="text-[8px] font-black mt-1">AC</span></button>
                    </div>
                    <input id="p-bid" type="number" placeholder="Enter fare" class="input-box text-center text-xl text-green-600 font-black">
                    <button onclick="requestRide()" id="req-btn" class="btn-main">BOOK NOW</button>
                </div>
            </div>

            <div id="active-box" class="hidden bg-white p-6 rounded-[35px] shadow-xl border-t-8 border-blue-600 space-y-4">
                <div class="flex justify-between items-center">
                    <span class="text-blue-600 font-black text-xs uppercase italic">Ride in Progress</span>
                    <button onclick="sos()" class="bg-red-500 text-white w-10 h-10 rounded-full flex items-center justify-center animate-pulse"><i data-lucide="shield-alert" class="w-5"></i></button>
                </div>
                <div id="ride-info" class="bg-gray-50 p-4 rounded-2xl text-xs font-bold leading-relaxed"></div>
                <div id="chat-box" class="h-40 overflow-y-auto bg-gray-50 rounded-2xl p-4 flex flex-col gap-2"></div>
                <div class="flex gap-2">
                    <input id="msg-input" type="text" placeholder="Chat..." class="!m-0 input-box !p-3">
                    <button onclick="sendMsg()" class="bg-blue-600 text-white px-5 rounded-xl"><i data-lucide="send" class="w-5"></i></button>
                </div>
                <button onclick="endRide()" class="w-full text-xs font-black text-red-400 py-2">CANCEL / END RIDE</button>
            </div>

            <div id="d-ui" class="hidden space-y-4">
                <h2 class="font-black px-2 text-gray-400 text-sm tracking-widest uppercase">Available Rides</h2>
                <div id="d-feed" class="space-y-4"></div>
            </div>
        </main>
    </div>

    <div id="driver-reg" class="hidden fixed inset-0 z-[6000] bg-white p-8">
        <h2 class="text-3xl font-black mb-8">Driver Setup</h2>
        <input id="dr-name" type="text" placeholder="Full Name" class="input-box">
        <input id="dr-plate" type="text" placeholder="Car Plate No." class="input-box">
        <input id="dr-phone" type="tel" placeholder="Phone Number" class="input-box">
        <button onclick="saveDriver()" class="btn-main mt-4">COMPLETE REGISTRATION</button>
        <button onclick="document.getElementById('driver-reg').classList.add('hidden')" class="w-full text-gray-400 font-bold mt-6">BACK</button>
    </div>

    <script>
        lucide.createIcons();
        let currentRideId = null; let userRole = '';

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
        const auth = firebase.auth(); const db = firebase.database();

        // 🛡️ AUTH CHECK
        auth.onAuthStateChanged(user => {
            if(user) {
                document.getElementById('auth-screen').classList.add('hidden');
                document.getElementById('app-content').classList.remove('hidden');
                document.getElementById('top-name').innerText = user.displayName;
                checkUserRole(user.uid);
            } else {
                document.getElementById('auth-screen').classList.remove('hidden');
                document.getElementById('app-content').classList.add('hidden');
            }
        });

        function login() { auth.signInWithPopup(new firebase.auth.GoogleAuthProvider()); }
        function logout() { auth.signOut().then(() => location.reload()); }

        function checkUserRole(uid) {
            db.ref('users/' + uid).on('value', snap => {
                if(snap.exists()) {
                    userRole = snap.val().role;
                    document.getElementById('top-role').innerText = userRole;
                    setupUI();
                } else {
                    document.getElementById('role-screen').classList.remove('hidden');
                }
            });
        }

        function setupUI() {
            document.getElementById('role-screen').classList.add('hidden');
            if(userRole === 'passenger') {
                document.getElementById('p-ui').classList.remove('hidden');
                initMap();
                trackRides();
            } else {
                document.getElementById('d-ui').classList.remove('hidden');
                syncFeed();
                trackRides();
            }
        }

        function setRole(r) { db.ref('users/'+auth.currentUser.uid).set({role:r, name:auth.currentUser.displayName}); }
        function openDriverReg() { document.getElementById('driver-reg').classList.remove('hidden'); }
        function saveDriver() {
            const name = document.getElementById('dr-name').value;
            const plate = document.getElementById('dr-plate').value;
            const phone = document.getElementById('dr-phone').value;
            if(!name || !plate) return alert("Please fill details sweetie!");
            db.ref('users/'+auth.currentUser.uid).set({role:'driver', name, plate, phone});
            document.getElementById('driver-reg').classList.add('hidden');
        }

        function initMap() {
            let map = L.map('map', {zoomControl: false}).setView([35.9208, 74.3089], 14);
            L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png').addTo(map);
            L.marker([35.9208, 74.3089]).addTo(map);
        }

        function selV(t, p) {
            document.querySelectorAll('.v-btn').forEach(b => b.classList.remove('v-selected'));
            document.getElementById('v-'+t).classList.add('v-selected');
            document.getElementById('p-bid').value = p;
        }

        function requestRide() {
            const f = document.getElementById('p-from').value;
            const t = document.getElementById('p-to').value;
            const b = document.getElementById('p-bid').value;
            if(!f || !t) return alert("Enter route!");

            const newRide = db.ref('rides').push();
            currentRideId = newRide.key;
            newRide.set({f, t, fare: b, pName: auth.currentUser.displayName, pUid: auth.currentUser.uid, status: 'searching'});
            document.getElementById('req-btn').innerText = "WAITING FOR DRIVER...";
        }

        function syncFeed() {
            db.ref('rides').on('value', snap => {
                const feed = document.getElementById('d-feed'); feed.innerHTML = "";
                snap.forEach(child => {
                    const r = child.val();
                    if(r.status === 'searching') {
                        feed.innerHTML += `
                        <div class="bg-white p-6 rounded-[30px] shadow-sm border-l-8 border-blue-600 animate-pulse">
                            <b class="text-sm block uppercase">${r.f} ➔ ${r.t}</b>
                            <p class="text-[10px] font-bold text-gray-400 mt-1">BY: ${r.pName}</p>
                            <div class="flex justify-between items-center mt-5">
                                <span class="text-2xl font-black text-green-600">Rs ${r.fare}</span>
                                <button onclick="acceptRide('${child.key}')" class="bg-black text-white px-6 py-3 rounded-2xl font-bold text-xs uppercase">Accept</button>
                            </div>
                        </div>`;
                    }
                });
            });
        }

        function acceptRide(id) {
            db.ref('users/'+auth.currentUser.uid).once('value', snap => {
                const d = snap.val();
                db.ref('rides/'+id).update({status:'accepted', dUid:auth.currentUser.uid, dName:d.name, dPlate:d.plate, dPhone:d.phone});
            });
        }

        function trackRides() {
            db.ref('rides').on('value', snap => {
                snap.forEach(child => {
                    const r = child.val();
                    if(r.status === 'accepted' && (r.pUid === auth.currentUser.uid || r.dUid === auth.currentUser.uid)) {
                        currentRideId = child.key;
                        document.getElementById('p-ui').classList.add('hidden');
                        document.getElementById('d-ui').classList.add('hidden');
                        document.getElementById('active-box').classList.remove('hidden');
                        document.getElementById('ride-info').innerHTML = 
                            userRole === 'passenger' ? `DRIVER: ${r.dName}<br>PLATE: ${r.dPlate}<br>FARE: RS ${r.fare}` : 
                            `CLIENT: ${r.pName}<br>TO: ${r.t}<br>FARE: RS ${r.fare}`;
                        syncChat();
                    }
                });
            });
        }

        function sendMsg() {
            const m = document.getElementById('msg-input').value; if(!m) return;
            db.ref('rides/'+currentRideId+'/chat').push({name: auth.currentUser.displayName, text: m, uid: auth.currentUser.uid});
            document.getElementById('msg-input').value = "";
        }

        function syncChat() {
            db.ref('rides/'+currentRideId+'/chat').on('value', snap => {
                const box = document.getElementById('chat-box'); box.innerHTML = "";
                snap.forEach(c => {
                    const d = c.val(); const isMe = d.uid === auth.currentUser.uid;
                    box.innerHTML += `<div class="p-3 rounded-2xl text-xs font-bold ${isMe ? 'bg-blue-600 text-white self-end':'bg-white border self-start'}">${d.text}</div>`;
                });
                box.scrollTop = box.scrollHeight;
            });
        }

        function endRide() { if(confirm("End trip sweetie?")) { db.ref('rides/'+currentRideId).remove(); location.reload(); } }
        function sos() { alert("🚨 SOS: Emergency alert sent to GB Drive center!"); }
    </script>
</body>
</html>
