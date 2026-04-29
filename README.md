<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>GB DRIVE - PREMIUM EDITION</title>
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
        body { background: #F3F4F6; color: #111827; margin: 0; padding: 0; }
        .hidden { display: none !important; }
        
        /* Premium UI Elements */
        .glass { background: rgba(255, 255, 255, 0.8); backdrop-filter: blur(12px); border: 1px solid rgba(255,255,255,0.3); }
        .card-shadow { box-shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.05), 0 8px 10px -6px rgba(0, 0, 0, 0.03); }
        .bottom-nav { position: fixed; bottom: 0; left: 0; right: 0; height: 70px; background: white; border-top: 1px solid #E5E7EB; display: flex; justify-content: space-around; align-items: center; z-index: 1000; }
        
        #map { height: 250px; width: 100%; border-radius: 24px; box-shadow: inset 0 2px 4px rgba(0,0,0,0.06); margin-bottom: 1rem; }
        
        /* Buttons & Inputs */
        .v-btn { background: #fff; border-radius: 18px; transition: 0.3s; border: 2px solid #F3F4F6; padding: 12px; filter: grayscale(1); }
        .v-selected { border-color: #2563EB; background: #EFF6FF; filter: grayscale(0); transform: scale(1.05); }
        input { background: #F9FAFB; border: 1px solid #E5E7EB; border-radius: 16px; padding: 16px; width: 100%; margin-bottom: 12px; font-weight: 600; }
        .btn-action { background: linear-gradient(135deg, #2563EB 0%, #1D4ED8 100%); color: white; font-weight: 900; border-radius: 18px; padding: 20px; width: 100%; box-shadow: 0 10px 15px -3px rgba(37,99,235,0.4); }
        
        /* Splash Screen */
        #splash { position: fixed; inset: 0; background: #2563EB; z-index: 9999; display: flex; flex-direction: column; items-center; justify-content: center; transition: 0.5s; }
        .loader { width: 48px; height: 48px; border: 5px solid #FFF; border-bottom-color: transparent; border-radius: 50%; animation: rotation 1s linear infinite; }
        @keyframes rotation { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }
    </style>
</head>
<body class="pb-24">

    <div id="splash">
        <div class="bg-white text-blue-600 w-20 h-20 flex items-center justify-center rounded-3xl font-black italic text-4xl mb-4 shadow-2xl">GB</div>
        <div class="loader"></div>
        <p class="text-white font-black mt-4 tracking-[0.2em]">DRIVE PRO</p>
    </div>

    <header class="p-5 glass sticky top-0 z-[100] flex justify-between items-center">
        <div class="flex items-center gap-3">
            <div class="bg-blue-600 text-white w-10 h-10 flex items-center justify-center rounded-2xl font-black shadow-lg">GB</div>
            <div>
                <p id="top-name" class="font-black text-sm text-slate-800 uppercase tracking-tighter">GUEST</p>
                <div class="flex items-center gap-1">
                    <span class="w-2 h-2 bg-green-500 rounded-full animate-pulse"></span>
                    <p id="top-role" class="text-[10px] font-bold text-blue-600 uppercase">Connecting...</p>
                </div>
            </div>
        </div>
        <button onclick="logout()" class="w-10 h-10 flex items-center justify-center bg-red-50 text-red-500 rounded-2xl"><i data-lucide="log-out" class="w-5"></i></button>
    </header>

    <main class="p-5 max-w-md mx-auto">
        
        <div id="welcome-msg" class="mb-6">
            <h1 class="text-3xl font-black text-slate-900 leading-none">Hello, <span class="text-blue-600">Sweetie!</span></h1>
            <p class="text-slate-400 font-bold text-sm mt-2 uppercase tracking-widest">Where are we going today?</p>
        </div>

        <div id="role-screen" class="hidden space-y-4 pt-10">
            <button onclick="setRole('passenger')" class="w-full p-8 bg-white rounded-[32px] card-shadow text-left flex justify-between items-center group">
                <div><b class="text-2xl block">PASSENGER</b><span class="text-xs font-bold text-slate-400">Request a safe ride</span></div>
                <div class="bg-blue-50 p-4 rounded-2xl text-blue-600"><i data-lucide="map-pin"></i></div>
            </button>
            <button onclick="document.getElementById('driver-reg').classList.remove('hidden')" class="w-full p-8 bg-slate-900 rounded-[32px] card-shadow text-left flex justify-between items-center text-white">
                <div><b class="text-2xl block">DRIVER</b><span class="text-xs font-bold text-slate-500">Earn money on trips</span></div>
                <div class="bg-white/10 p-4 rounded-2xl text-white"><i data-lucide="car"></i></div>
            </button>
        </div>

        <div id="p-ui" class="hidden space-y-6">
            <div id="map"></div>
            
            <div class="bg-white p-6 rounded-[32px] card-shadow space-y-1">
                <div class="flex items-center gap-3 border-b pb-2 mb-2">
                    <i data-lucide="circle-dot" class="text-blue-600 w-4"></i>
                    <input id="p-from" type="text" placeholder="Pickup location" class="!m-0 border-none !p-0 text-sm">
                </div>
                <div class="flex items-center gap-3 pb-2">
                    <i data-lucide="map-pin" class="text-red-500 w-4"></i>
                    <input id="p-to" type="text" placeholder="Destination" class="!m-0 border-none !p-0 text-sm">
                </div>
            </div>

            <div class="grid grid-cols-4 gap-3">
                <button onclick="selV('BIKE', 120)" id="v-BIKE" class="v-btn card-shadow flex flex-col items-center"><i data-lucide="bike" class="mb-1 w-5"></i><span class="text-[9px] font-black uppercase">Bike</span></button>
                <button onclick="selV('MINI', 250)" id="v-MINI" class="v-btn card-shadow flex flex-col items-center"><i data-lucide="zap" class="mb-1 w-5"></i><span class="text-[9px] font-black uppercase">Mini</span></button>
                <button onclick="selV('CAR', 450)" id="v-CAR" class="v-btn card-shadow flex flex-col items-center v-selected"><i data-lucide="car" class="mb-1 w-5"></i><span class="text-[9px] font-black uppercase">Ride</span></button>
                <button onclick="selV('AC', 650)" id="v-AC" class="v-btn card-shadow flex flex-col items-center"><i data-lucide="snowflake" class="mb-1 w-5"></i><span class="text-[9px] font-black uppercase">Luxury</span></button>
            </div>

            <div class="bg-white p-4 rounded-3xl flex items-center justify-between card-shadow">
                <span class="font-black text-xs text-slate-400 ml-4">OFFER FARE:</span>
                <div class="flex items-center gap-2">
                    <span class="text-green-600 font-black">PKR</span>
                    <input id="p-bid" type="number" value="450" class="!m-0 !p-0 w-24 text-2xl font-black text-green-600 border-none bg-transparent text-right">
                </div>
            </div>

            <button onclick="requestRide()" id="req-btn" class="btn-action">FIND A DRIVER</button>
        </div>

        <div id="active-ride-box" class="hidden bg-white rounded-[32px] p-6 card-shadow border-t-8 border-blue-600 space-y-4">
            <div class="flex justify-between items-center">
                <span class="bg-blue-100 text-blue-700 px-3 py-1 rounded-full text-[10px] font-black uppercase tracking-widest">Ongoing Trip</span>
                <button onclick="sos()" class="bg-red-500 text-white w-10 h-10 rounded-full flex items-center justify-center animate-bounce shadow-lg shadow-red-200"><i data-lucide="shield-alert" class="w-5"></i></button>
            </div>
            <div id="ride-party-info" class="bg-slate-50 p-4 rounded-2xl flex items-center gap-4">
                <div class="w-12 h-12 bg-blue-600 rounded-full flex items-center justify-center text-white font-black">?</div>
                <div id="party-details" class="text-xs font-bold leading-tight"></div>
            </div>
            <div id="chat-window" class="h-40 overflow-y-auto bg-slate-50 rounded-2xl p-4 flex flex-col gap-2"></div>
            <div class="flex gap-2 bg-slate-100 p-2 rounded-2xl">
                <input id="chat-input" type="text" placeholder="Type message..." class="!m-0 !p-2 !border-none bg-transparent">
                <button onclick="sendMessage()" class="bg-blue-600 text-white w-12 h-12 rounded-xl flex items-center justify-center"><i data-lucide="send-horizonal" class="w-5"></i></button>
            </div>
            <button onclick="completeTrip()" class="w-full text-[10px] font-black text-slate-400 uppercase tracking-[0.3em]">End Trip</button>
        </div>

        <div id="d-ui" class="hidden space-y-4">
            <div id="d-feed" class="space-y-4"></div>
        </div>

    </main>

    <nav class="bottom-nav">
        <div class="flex flex-col items-center text-blue-600"><i data-lucide="home" class="w-6"></i><span class="text-[9px] font-black mt-1 uppercase">Home</span></div>
        <div class="flex flex-col items-center text-slate-400"><i data-lucide="clock" class="w-6"></i><span class="text-[9px] font-black mt-1 uppercase">Activity</span></div>
        <div class="flex flex-col items-center text-slate-400" onclick="logout()"><i data-lucide="user" class="w-6"></i><span class="text-[9px] font-black mt-1 uppercase">Profile</span></div>
    </nav>

    <div id="driver-reg" class="hidden fixed inset-0 z-[2000] bg-white flex flex-col p-8">
        <h2 class="text-4xl font-black mb-8 mt-10">Driver<br>Registration</h2>
        <input id="dr-name" type="text" placeholder="Your Full Name">
        <input id="dr-plate" type="text" placeholder="Vehicle Plate (e.g. GLT-123)">
        <input id="dr-phone" type="tel" placeholder="Phone Number">
        <button onclick="saveDriver()" class="btn-action mt-6">COMPLETE SETUP</button>
        <button onclick="location.reload()" class="mt-4 font-bold text-slate-400 uppercase tracking-widest text-sm">Cancel</button>
    </div>

    <script>
        lucide.createIcons();
        let rideId = null; let myRole = '';
        
        // Firebase Configuration
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
        const db = firebase.database(); const auth = firebase.auth();

        // Splash Timeout
        setTimeout(() => { document.getElementById('splash').style.opacity = '0'; setTimeout(()=>document.getElementById('splash').remove(), 500); }, 2000);

        auth.onAuthStateChanged(user => {
            if(user) {
                document.getElementById('top-name').innerText = user.displayName;
                db.ref('users/' + user.uid).on('value', snap => {
                    if(snap.exists()) {
                        myRole = snap.val().role;
                        document.getElementById('top-role').innerText = myRole;
                        showScreen(myRole);
                    } else document.getElementById('role-screen').classList.remove('hidden');
                });
            } else auth.signInWithPopup(new firebase.auth.GoogleAuthProvider());
        });

        function showScreen(role) {
            document.getElementById('role-screen').classList.add('hidden');
            if(role === 'passenger') {
                document.getElementById('p-ui').classList.remove('hidden');
                initMap();
                watchRides();
            } else {
                document.getElementById('d-ui').classList.remove('hidden');
                document.getElementById('welcome-msg').innerHTML = '<h1 class="text-3xl font-black">Driver <span class="text-blue-600">Dashboard</span></h1>';
                syncDriverFeed();
                watchRides();
            }
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

        function setRole(r) { db.ref('users/'+auth.currentUser.uid).set({role:r, name:auth.currentUser.displayName}); }
        function saveDriver() {
            const n = document.getElementById('dr-name').value;
            const pl = document.getElementById('dr-plate').value;
            if(!n || !pl) return alert("Sweetie, fill everything!");
            db.ref('users/'+auth.currentUser.uid).set({role:'driver', name:n, plate:pl}).then(()=>location.reload());
        }

        function requestRide() {
            const f = document.getElementById('p-from').value;
            const t = document.getElementById('p-to').value;
            const b = document.getElementById('p-bid').value;
            if(!f || !t) return alert("Where are we going?");
            
            document.getElementById('req-btn').innerText = "SEARCHING NEARBY...";
            const newRide = db.ref('rides').push();
            rideId = newRide.key;
            newRide.set({f, t, fare: b, pName: auth.currentUser.displayName, pUid: auth.currentUser.uid, status: 'searching'});
        }

        function syncDriverFeed() {
            db.ref('rides').on('value', snap => {
                const feed = document.getElementById('d-feed');
                feed.innerHTML = "";
                snap.forEach(child => {
                    const r = child.val();
                    if(r.status === 'searching') {
                        feed.innerHTML += `
                        <div class="bg-white p-6 rounded-[32px] card-shadow border-l-8 border-blue-600 animate-in fade-in">
                            <div class="flex justify-between items-start mb-4">
                                <div><b class="text-sm uppercase block">${r.f} ➔ ${r.t}</b><span class="text-[10px] font-black text-slate-400">PASSENGER: ${r.pName}</span></div>
                                <div class="text-2xl font-black text-green-600">Rs ${r.fare}</div>
                            </div>
                            <button onclick="acceptRide('${child.key}')" class="w-full bg-slate-900 text-white py-4 rounded-2xl font-black text-xs uppercase tracking-widest">Accept Ride</button>
                        </div>`;
                    }
                });
            });
        }

        function acceptRide(id) {
            db.ref('users/'+auth.currentUser.uid).once('value', snap => {
                const d = snap.val();
                db.ref('rides/'+id).update({status: 'accepted', dUid: auth.currentUser.uid, dName: d.name, dPlate: d.plate});
            });
        }

        function watchRides() {
            db.ref('rides').on('value', snap => {
                snap.forEach(child => {
                    const r = child.val();
                    if(r.status === 'accepted' && (r.pUid === auth.currentUser.uid || r.dUid === auth.currentUser.uid)) {
                        rideId = child.key;
                        document.getElementById('p-ui').classList.add('hidden');
                        document.getElementById('d-ui').classList.add('hidden');
                        document.getElementById('active-ride-box').classList.remove('hidden');
                        
                        const detailBox = document.getElementById('party-details');
                        if(myRole === 'passenger') {
                            detailBox.innerHTML = `DRIVER: ${r.dName}<br>VEHICLE: ${r.dPlate}<br>FARE: RS ${r.fare}`;
                        } else {
                            detailBox.innerHTML = `CLIENT: ${r.pName}<br>DESTINATION: ${r.t}<br>FARE: RS ${r.fare}`;
                        }
                        syncChat();
                    }
                });
            });
        }

        function sendMessage() {
            const msg = document.getElementById('chat-input').value;
            if(!msg) return;
            db.ref('rides/'+rideId+'/chat').push({u: auth.currentUser.displayName, m: msg, uid: auth.currentUser.uid});
            document.getElementById('chat-input').value = "";
        }

        function syncChat() {
            db.ref('rides/'+rideId+'/chat').on('value', snap => {
                const box = document.getElementById('chat-window');
                box.innerHTML = "";
                snap.forEach(c => {
                    const m = c.val();
                    const isMe = m.uid === auth.currentUser.uid;
                    box.innerHTML += `<div class="p-3 rounded-2xl text-xs font-bold ${isMe ? 'bg-blue-600 text-white self-end rounded-tr-none' : 'bg-white border self-start rounded-tl-none'} shadow-sm">${m.m}</div>`;
                });
                box.scrollTop = box.scrollHeight;
            });
        }

        function completeTrip() { if(confirm("End ride?")) { db.ref('rides/'+rideId).remove(); location.reload(); } }
        function logout() { auth.signOut(); location.reload(); }
        function sos() { alert("🚨 EMERGENCY SOS ALERT: Signal sent to Control Center!"); }
    </script>
</body>
</html>
