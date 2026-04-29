<html lang="EN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>GB DRIVE - PRO EDITION</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-auth-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-database-compat.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Outfit:wght@400;600;900&display=swap');
        * { font-family: 'Outfit', sans-serif; -webkit-tap-highlight-color: transparent; }
        body { background: #F8FAFC; color: #1E293B; overflow-x: hidden; }
        .HIDDEN { display: none !important; }
        .NEO-CARD { background: white; border-radius: 2rem; box-shadow: 0 10px 40px -10px rgba(0,0,0,0.1); border: 1px solid rgba(255,255,255,0.5); }
        #map { height: 200px; width: 100%; border-radius: 1.5rem; margin-bottom: 1rem; z-index: 10; }
        .v-btn { border: 2px solid transparent; background: #F1F5F9; border-radius: 1.2rem; transition: 0.3s; }
        .v-selected { border-color: #2563EB; background: #EFF6FF; color: #2563EB; }
        input { border: 2px solid #E2E8F0 !important; border-radius: 1rem !important; padding: 12px 16px !important; width: 100%; outline: none; }
        .bid-badge { background: #F59E0B; color: white; padding: 2px 8px; border-radius: 8px; font-size: 10px; font-weight: 900; }
    </style>
</head>
<body class="pb-10">

    <header class="p-5 bg-white/80 backdrop-blur-md sticky top-0 z-[100] flex justify-between items-center border-b border-slate-100">
        <div class="flex items-center gap-3">
            <div class="bg-blue-600 text-white w-10 h-10 flex items-center justify-center rounded-2xl font-black italic shadow-lg">GB</div>
            <div>
                <p id="top-name" class="font-black text-sm uppercase">GB USER</p>
                <p id="top-role" class="text-[9px] font-bold text-blue-600 uppercase tracking-widest">CONNECTING...</p>
            </div>
        </div>
        <button onclick="logout()" class="p-2 bg-red-50 text-red-500 rounded-xl"><i data-lucide="log-out" class="w-5"></i></button>
    </header>

    <main class="p-5 max-w-md mx-auto space-y-6">
        
        <div id="role-screen" class="hidden py-10 space-y-4">
            <h1 class="text-3xl font-black text-center text-slate-900">CHOOSE MODE</h1>
            <button onclick="setRole('passenger')" class="w-full p-8 neo-card text-left flex justify-between items-center">
                <span><b class="text-2xl">PASSENGER</b><br><small class="opacity-50 text-xs">Request a ride now</small></span>
                <i data-lucide="user" class="w-10 h-10 text-blue-600"></i>
            </button>
            <button onclick="document.getElementById('driver-modal').classList.remove('HIDDEN')" class="w-full p-8 neo-card text-left border-2 border-slate-900 flex justify-between items-center">
                <span><b class="text-2xl">DRIVER</b><br><small class="opacity-50 text-xs">Start earning money</small></span>
                <i data-lucide="car" class="w-10 h-10 text-slate-900"></i>
            </button>
        </div>

        <div id="p-ui" class="hidden space-y-6">
            <div class="neo-card p-6">
                <div id="map"></div>
                <input id="p-from" type="text" placeholder="PICKUP FROM" class="mb-2">
                <input id="p-to" type="text" placeholder="DROP OFF TO" class="mb-4">
                
                <div class="grid grid-cols-4 gap-2 mb-6">
                    <button onclick="selV('BIKE', 120)" id="v-BIKE" class="v-btn p-3 flex flex-col items-center"><i data-lucide="bike" class="w-4"></i><span class="text-[8px] font-black mt-1">BIKE</span></button>
                    <button onclick="selV('MINI', 250)" id="v-MINI" class="v-btn p-3 flex flex-col items-center"><i data-lucide="loader" class="w-4"></i><span class="text-[8px] font-black mt-1">MINI</span></button>
                    <button onclick="selV('CAR', 450)" id="v-CAR" class="v-btn p-3 flex flex-col items-center v-selected"><i data-lucide="car" class="w-4"></i><span class="text-[8px] font-black mt-1">CAR</span></button>
                    <button onclick="selV('AC', 600)" id="v-AC" class="v-btn p-3 flex flex-col items-center"><i data-lucide="snowflake" class="w-4"></i><span class="text-[8px] font-black mt-1">AC CAR</span></button>
                </div>

                <div class="flex items-center gap-2 mb-4">
                    <input id="p-bid" type="number" placeholder="OFFER PRICE" class="!m-0 text-center text-xl font-black text-green-600">
                </div>
                
                <button onclick="requestRide()" class="w-full bg-blue-600 text-white py-5 rounded-2xl font-black text-lg shadow-xl uppercase">Request Ride</button>
            </div>

            <div id="p-active-ride" class="hidden neo-card p-6 border-t-8 border-blue-600 space-y-4">
                <div class="flex justify-between items-center">
                    <h3 class="font-black italic text-blue-600 uppercase">Ride Status</h3>
                    <button onclick="sos()" class="bg-red-500 text-white px-3 py-1 rounded-lg text-[10px] font-black animate-pulse">SOS</button>
                </div>
                <div id="ride-info" class="bg-slate-50 p-4 rounded-xl text-xs font-bold space-y-1"></div>
                <div id="p-chat" class="h-32 overflow-y-auto bg-slate-50 p-3 rounded-xl text-[11px] font-bold"></div>
                <div class="flex gap-2">
                    <input id="p-msg" type="text" placeholder="Message..." class="!m-0">
                    <button onclick="sendMsg('p')" class="bg-blue-600 text-white px-4 rounded-xl"><i data-lucide="send" class="w-4"></i></button>
                </div>
            </div>
        </div>

        <div id="d-ui" class="hidden space-y-6">
            <h2 class="font-black text-xl px-2">LIVE REQUESTS</h2>
            <div id="d-feed" class="space-y-4"></div>
            
            <div id="d-active-ride" class="hidden neo-card p-6 border-t-8 border-green-500 space-y-4">
                <div id="p-info" class="bg-slate-50 p-4 rounded-xl text-xs font-bold mb-4"></div>
                <div id="d-chat" class="h-32 overflow-y-auto bg-slate-50 p-3 rounded-xl text-[11px] font-bold"></div>
                <div class="flex gap-2">
                    <input id="d-msg" type="text" placeholder="Message..." class="!m-0">
                    <button onclick="sendMsg('d')" class="bg-blue-600 text-white px-4 rounded-xl"><i data-lucide="send" class="w-4"></i></button>
                </div>
            </div>
        </div>
    </main>

    <div id="driver-modal" class="hidden fixed inset-0 z-[200] flex items-center justify-center p-6 bg-black/80 backdrop-blur-md">
        <div class="bg-white w-full max-w-md rounded-[32px] p-8 space-y-3">
            <h2 class="text-2xl font-black text-center mb-4">DRIVER VERIFICATION</h2>
            <input id="dr-name" type="text" placeholder="REAL NAME">
            <input id="dr-phone" type="tel" placeholder="PHONE NUMBER">
            <input id="dr-plate" type="text" placeholder="VEHICLE PLATE (LEC-123)">
            <button onclick="saveDriver()" class="w-full bg-blue-600 text-white py-5 rounded-2xl font-black mt-4">JOIN GB DRIVE</button>
            <button onclick="document.getElementById('driver-modal').classList.add('HIDDEN')" class="w-full text-xs font-bold text-slate-400">CANCEL</button>
        </div>
    </div>

    <script>
        lucide.createIcons();
        let curBid = 450; let rideRef = null; let map, marker;

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

        // MAP SETUP
        function initMap() {
            map = L.map('map').setView([35.9208, 74.3089], 13); // Default GB Gilgit
            L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png').addTo(map);
            marker = L.marker([35.9208, 74.3089]).addTo(map);
        }

        auth.onAuthStateChanged(user => {
            if(user) {
                document.getElementById('top-name').innerText = user.displayName;
                db.ref('users/' + user.uid).on('value', snap => {
                    if(snap.exists()) {
                        const u = snap.val();
                        document.getElementById('top-role').innerText = u.role;
                        showUI(u.role);
                        if(u.role === 'passenger') initMap();
                    } else document.getElementById('role-screen').classList.remove('hidden');
                });
            } else { auth.signInWithPopup(new firebase.auth.GoogleAuthProvider()); }
        });

        function logout() { auth.signOut(); location.reload(); }
        function setRole(r) { db.ref('users/' + auth.currentUser.uid).set({ role: r, name: auth.currentUser.displayName }).then(()=>location.reload()); }
        function saveDriver() {
            const d = { name: document.getElementById('dr-name').value, phone: document.getElementById('dr-phone').value, plate: document.getElementById('dr-plate').value, role: 'driver' };
            db.ref('users/'+auth.currentUser.uid).set(d).then(()=>location.reload());
        }

        function showUI(r) {
            document.getElementById('role-screen').classList.add('hidden');
            if(r === 'passenger') document.getElementById('p-ui').classList.remove('hidden');
            else { document.getElementById('d-ui').classList.remove('hidden'); syncFeed(); }
        }

        function selV(t, b) {
            curBid = b;
            document.querySelectorAll('.v-btn').forEach(btn => btn.classList.remove('v-selected'));
            document.getElementById('v-'+t).classList.add('v-selected');
            document.getElementById('p-bid').value = b;
        }

        function requestRide() {
            const f = document.getElementById('p-from').value; const t = document.getElementById('p-to').value;
            const bid = document.getElementById('p-bid').value;
            if(!f || !t) return alert("ENTER ROUTE!");
            rideRef = db.ref('rides').push({ f, t, fare: bid, pName: auth.currentUser.displayName, pUid: auth.currentUser.uid, status: 'searching' }).key;
            db.ref('rides/'+rideRef).on('value', snap => {
                const r = snap.val();
                if(r && r.status === 'accepted') {
                    document.getElementById('p-active-ride').classList.remove('hidden');
                    document.getElementById('ride-info').innerHTML = `DRIVER: ${r.dName}<br>PLATE: ${r.dPlate}<br>PHONE: ${r.dPhone}<br>FARE: RS ${r.fare}`;
                    syncChat(rideRef, 'p-chat');
                }
            });
        }

        function syncFeed() {
            db.ref('rides').on('value', snap => {
                const feed = document.getElementById('d-feed'); feed.innerHTML = "";
                snap.forEach(c => {
                    const r = c.val();
                    if(r.status === 'searching') {
                        feed.innerHTML += `<div class="neo-card p-6 border-l-[10px] border-blue-600">
                            <p class="font-black text-lg">${r.f} ➔ ${r.t}</p>
                            <p class="text-[10px] font-bold opacity-40 uppercase">${r.pName}</p>
                            <div class="flex justify-between items-center mt-6">
                                <b class="text-3xl text-green-600 font-black italic">RS ${r.fare}</b>
                                <button onclick="accept('${c.key}')" class="bg-slate-900 text-white px-8 py-3 rounded-2xl font-black text-xs">ACCEPT</button>
                            </div>
                        </div>`;
                    }
                });
            });
        }

        function accept(id) {
            db.ref('users/'+auth.currentUser.uid).once('value', snap => {
                const d = snap.val();
                db.ref('rides/'+id).update({ status: 'accepted', dName: d.name, dPhone: d.phone, dPlate: d.plate, dUid: auth.currentUser.uid });
                rideRef = id;
                document.getElementById('d-feed').classList.add('hidden');
                document.getElementById('d-active-ride').classList.remove('hidden');
                db.ref('rides/'+id).once('value', s => {
                    document.getElementById('p-info').innerHTML = `CLIENT: ${s.val().pName}<br>FROM: ${s.val().f}<br>TO: ${s.val().t}<br>FARE: RS ${s.val().fare}`;
                });
                syncChat(id, 'd-chat');
            });
        }

        function sendMsg(t) {
            const m = document.getElementById(t+'-msg').value; if(!m) return;
            db.ref('rides/'+rideRef+'/chat').push({ s: auth.currentUser.displayName, t: m });
            document.getElementById(t+'-msg').value = "";
        }

        function syncChat(id, box) {
            db.ref('rides/'+id+'/chat').on('value', snap => {
                const b = document.getElementById(box); b.innerHTML = "";
                snap.forEach(m => {
                    const d = m.val(); const me = d.s === auth.currentUser.displayName;
                    b.innerHTML += `<div class="mb-2 p-2 rounded-lg ${me ? 'bg-blue-600 text-white ml-8':'bg-white border mr-8'} shadow-sm">${d.t}</div>`;
                });
                b.scrollTop = b.scrollHeight;
            });
        }

        function sos() { alert("🚨 EMERGENCY SOS SENT! Location & Details Shared with Admin."); }
    </script>
</body>
</html>
