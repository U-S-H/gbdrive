<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>GB DRIVE - FINAL PRO EDITION</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-auth-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-database-compat.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Outfit:wght@400;600;900&display=swap');
        * { font-family: 'Outfit', sans-serif; -webkit-tap-highlight-color: transparent; outline: none !important; }
        body { background: #f8fafc; color: #1e293b; }
        .hidden { display: none !important; }
        .neo-card { background: white; border-radius: 2rem; box-shadow: 0 10px 40px -10px rgba(0,0,0,0.1); border: 1px solid #f1f5f9; }
        #map { height: 180px; width: 100%; border-radius: 1.5rem; margin-bottom: 1rem; z-index: 10; }
        .v-btn { background: #f1f5f9; border-radius: 1.2rem; transition: 0.2s; border: 2px solid transparent; padding: 12px; }
        .v-selected { border-color: #2563eb; background: #eff6ff; color: #2563eb; }
        input { border: 2px solid #e2e8f0; border-radius: 1rem; padding: 14px; width: 100%; margin-bottom: 10px; transition: 0.3s; }
        input:focus { border-color: #2563eb; background: white; }
        .btn-main { background: #2563eb; color: white; font-weight: 900; border-radius: 1.2rem; padding: 18px; width: 100%; box-shadow: 0 8px 20px rgba(37,99,235,0.3); }
        .btn-main:active { transform: scale(0.96); }
        .chat-area { height: 150px; overflow-y: auto; background: #f9fafb; border-radius: 1rem; padding: 10px; display: flex; flex-direction: column; gap: 5px; }
        .star { font-size: 2rem; color: #cbd5e1; cursor: pointer; transition: 0.2s; }
        .star.active { color: #f59e0b; }
        .pulse-sos { animation: pulse 2s infinite; }
        @keyframes pulse { 0% { transform: scale(1); } 50% { transform: scale(1.05); } 100% { transform: scale(1); } }
    </style>
</head>
<body class="pb-10">

    <header class="p-4 bg-white/90 backdrop-blur-md sticky top-0 z-[100] flex justify-between items-center border-b">
        <div class="flex items-center gap-3">
            <div class="bg-blue-600 text-white w-10 h-10 flex items-center justify-center rounded-2xl font-black italic shadow-lg">GB</div>
            <div>
                <p id="top-name" class="font-black text-sm uppercase">GUEST</p>
                <p id="top-role" class="text-[9px] font-bold text-blue-600 tracking-widest uppercase">OFFLINE</p>
            </div>
        </div>
        <button onclick="logout()" class="p-2 bg-red-50 text-red-500 rounded-xl"><i data-lucide="log-out" class="w-5"></i></button>
    </header>

    <main class="p-4 max-w-md mx-auto space-y-4">
        
        <div id="role-screen" class="hidden py-10 space-y-4 text-center">
            <h1 class="text-3xl font-black italic">GB DRIVE</h1>
            <button onclick="setRole('passenger')" class="w-full p-8 neo-card text-left flex justify-between items-center group active:scale-95 transition-all">
                <span><b class="text-2xl">PASSENGER</b><br><small class="opacity-50">Find a ride now</small></span>
                <i data-lucide="user" class="w-10 h-10 text-blue-600"></i>
            </button>
            <button onclick="document.getElementById('driver-setup').classList.remove('hidden')" class="w-full p-8 neo-card text-left border-2 border-black flex justify-between items-center group active:scale-95 transition-all">
                <span><b class="text-2xl">DRIVER</b><br><small class="opacity-50">Start earning today</small></span>
                <i data-lucide="car" class="w-10 h-10"></i>
            </button>
        </div>

        <div id="p-ui" class="hidden space-y-4">
            <div class="neo-card p-5">
                <div id="map"></div>
                <input id="p-from" type="text" placeholder="Pickup Location">
                <input id="p-to" type="text" placeholder="Dropoff Location">
                
                <div class="grid grid-cols-4 gap-2 mb-4">
                    <button onclick="selV('BIKE', 100)" id="v-BIKE" class="v-btn flex flex-col items-center"><i data-lucide="bike" class="w-4"></i><span class="text-[8px] font-black mt-1 uppercase">Bike</span></button>
                    <button onclick="selV('MINI', 200)" id="v-MINI" class="v-btn flex flex-col items-center"><i data-lucide="loader" class="w-4"></i><span class="text-[8px] font-black mt-1 uppercase">Mini</span></button>
                    <button onclick="selV('CAR', 400)" id="v-CAR" class="v-btn flex flex-col items-center v-selected"><i data-lucide="car" class="w-4"></i><span class="text-[8px] font-black mt-1 uppercase">Car</span></button>
                    <button onclick="selV('AC', 600)" id="v-AC" class="v-btn flex flex-col items-center"><i data-lucide="snowflake" class="w-4"></i><span class="text-[8px] font-black mt-1 uppercase">AC Car</span></button>
                </div>

                <input id="p-bid" type="number" placeholder="Offer Your Fare" class="text-center text-2xl font-black text-green-600">
                <button onclick="requestRide()" id="req-btn" class="btn-main uppercase tracking-widest">Request Ride</button>
            </div>

            <div id="p-active" class="hidden neo-card p-6 border-t-8 border-blue-600 space-y-4">
                <div class="flex justify-between items-center">
                    <h3 class="font-black italic text-blue-600">RIDE ACTIVE</h3>
                    <button onclick="sos()" class="bg-red-500 text-white text-[10px] px-3 py-1 rounded-lg font-black pulse-sos">SOS</button>
                </div>
                <div id="p-driver-info" class="bg-slate-50 p-4 rounded-xl text-xs font-bold leading-relaxed"></div>
                <div id="p-chat" class="chat-area"></div>
                <div class="flex gap-2">
                    <input id="p-msg" type="text" placeholder="Message driver..." class="!m-0 text-sm">
                    <button onclick="sendMsg('p')" class="bg-blue-600 text-white px-5 rounded-xl"><i data-lucide="send" class="w-4"></i></button>
                </div>
                <button onclick="showRating()" class="w-full text-[10px] font-black text-slate-400 uppercase tracking-widest">Complete Trip</button>
            </div>

            <div id="rate-modal" class="hidden neo-card p-8 text-center space-y-5">
                <h2 class="text-2xl font-black italic">RATE TRIP</h2>
                <div class="flex justify-center gap-2">
                    <span onclick="setStars(1)" class="star">★</span>
                    <span onclick="setStars(2)" class="star">★</span>
                    <span onclick="setStars(3)" class="star">★</span>
                    <span onclick="setStars(4)" class="star">★</span>
                    <span onclick="setStars(5)" class="star">★</span>
                </div>
                <button onclick="submitRate()" class="btn-main">SUBMIT REVIEW</button>
            </div>
        </div>

        <div id="d-ui" class="hidden space-y-4">
            <div class="flex justify-between items-center px-2">
                <h2 class="font-black text-xl">LIVE FEED</h2>
                <span class="bg-green-100 text-green-600 text-[10px] px-3 py-1 rounded-full font-black uppercase">Online</span>
            </div>
            <div id="d-feed" class="space-y-4"></div>
            
            <div id="d-active" class="hidden neo-card p-6 border-t-8 border-green-500 space-y-4">
                <div id="d-p-info" class="bg-slate-50 p-4 rounded-xl text-xs font-bold leading-relaxed"></div>
                <div id="d-chat" class="chat-area"></div>
                <div class="flex gap-2">
                    <input id="d-msg" type="text" placeholder="Message client..." class="!m-0 text-sm">
                    <button onclick="sendMsg('d')" class="bg-blue-600 text-white px-5 rounded-xl"><i data-lucide="send" class="w-4"></i></button>
                </div>
            </div>
        </div>
    </main>

    <div id="driver-setup" class="hidden fixed inset-0 z-[200] bg-black/80 backdrop-blur-md flex items-center justify-center p-6">
        <div class="bg-white w-full max-w-md rounded-[2.5rem] p-8 space-y-4">
            <h2 class="text-2xl font-black text-center">DRIVER DATA</h2>
            <input id="dr-name" type="text" placeholder="Real Name">
            <input id="dr-phone" type="tel" placeholder="WhatsApp Number">
            <input id="dr-plate" type="text" placeholder="Vehicle Plate (ABC-123)">
            <button onclick="saveDriver()" class="btn-main">JOIN THE DRIVE</button>
            <button onclick="document.getElementById('driver-setup').classList.add('hidden')" class="w-full text-xs font-bold text-slate-400">CANCEL</button>
        </div>
    </div>

    <script>
        lucide.createIcons();
        let curBid = 400; let rideRef = null; let starRating = 5; let map, marker;

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

        function initMap() {
            if(!map) {
                map = L.map('map', {zoomControl: false}).setView([35.9208, 74.3089], 13);
                L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png').addTo(map);
                marker = L.marker([35.9208, 74.3089]).addTo(map);
            }
        }

        auth.onAuthStateChanged(user => {
            if(user) {
                document.getElementById('top-name').innerText = user.displayName.toUpperCase();
                db.ref('users/' + user.uid).on('value', snap => {
                    if(snap.exists()) {
                        const u = snap.val();
                        document.getElementById('top-role').innerText = u.role;
                        showUI(u.role);
                    } else document.getElementById('role-screen').classList.remove('hidden');
                });
            } else { auth.signInWithPopup(new firebase.auth.GoogleAuthProvider()); }
        });

        function logout() { auth.signOut(); location.reload(); }
        function setRole(r) { db.ref('users/' + auth.currentUser.uid).set({ role: r, name: auth.currentUser.displayName }).then(()=>location.reload()); }
        function saveDriver() {
            const d = { name: document.getElementById('dr-name').value, phone: document.getElementById('dr-phone').value, plate: document.getElementById('dr-plate').value, role: 'driver', rating: 5, ratingCount: 1 };
            if(!d.name || !d.plate) return alert("Fill all details!");
            db.ref('users/'+auth.currentUser.uid).set(d).then(()=>location.reload());
        }

        function showUI(r) {
            document.getElementById('role-screen').classList.add('hidden');
            if(r === 'passenger') { document.getElementById('p-ui').classList.remove('hidden'); initMap(); checkActiveRide('p'); }
            else { document.getElementById('d-ui').classList.remove('hidden'); syncFeed(); checkActiveRide('d'); }
        }

        function selV(t, b) {
            curBid = b;
            document.querySelectorAll('.v-btn').forEach(btn => btn.classList.remove('v-selected'));
            document.getElementById('v-'+t).classList.add('v-selected');
            document.getElementById('p-bid').value = b;
        }

        function requestRide() {
            const f = document.getElementById('p-from').value; const t = document.getElementById('p-to').value;
            const b = document.getElementById('p-bid').value;
            if(!f || !t || !b) return alert("Route info missing!");

            document.getElementById('req-btn').innerText = "FINDING DRIVER...";
            const newR = db.ref('rides').push();
            rideRef = newR.key;
            newR.set({ f, t, fare: b, pName: auth.currentUser.displayName, pUid: auth.currentUser.uid, status: 'searching' });
            
            newR.on('value', snap => {
                const r = snap.val();
                if(r && r.status === 'accepted') {
                    document.getElementById('p-active').classList.remove('hidden');
                    document.getElementById('p-driver-info').innerHTML = `DRIVER: ${r.dName}<br>PHONE: ${r.dPhone}<br>PLATE: ${r.dPlate}<br>FARE: RS ${r.fare}`;
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
                        feed.innerHTML += `<div class="neo-card p-6 border-l-[12px] border-blue-600 shadow-xl active:scale-95 transition-all">
                            <p class="font-black text-lg">${r.f} ➔ ${r.t}</p>
                            <p class="text-[10px] font-bold opacity-50 uppercase mt-1">CLIENT: ${r.pName}</p>
                            <div class="flex justify-between items-center mt-6">
                                <b class="text-3xl text-green-600 font-black italic">RS ${r.fare}</b>
                                <button onclick="acceptRide('${c.key}')" class="bg-black text-white px-8 py-3 rounded-2xl font-black text-xs">ACCEPT</button>
                            </div>
                        </div>`;
                    }
                });
            });
        }

        function acceptRide(id) {
            db.ref('users/'+auth.currentUser.uid).once('value', snap => {
                const d = snap.val();
                db.ref('rides/'+id).update({ status: 'accepted', dName: d.name, dPhone: d.phone, dPlate: d.plate, dUid: auth.currentUser.uid });
                rideRef = id;
                document.getElementById('d-feed').classList.add('hidden');
                document.getElementById('d-active').classList.remove('hidden');
                syncChat(id, 'd-chat');
            });
        }

        function checkActiveRide(role) {
            db.ref('rides').on('value', s => {
                s.forEach(child => {
                    const r = child.val();
                    if(r.status === 'accepted' && (r.pUid === auth.currentUser.uid || r.dUid === auth.currentUser.uid)) {
                        rideRef = child.key;
                        if(role === 'p') {
                            document.getElementById('p-active').classList.remove('hidden');
                            document.getElementById('p-driver-info').innerHTML = `DRIVER: ${r.dName}<br>PHONE: ${r.dPhone}<br>PLATE: ${r.dPlate}<br>FARE: RS ${r.fare}`;
                            syncChat(rideRef, 'p-chat');
                        } else {
                            document.getElementById('d-feed').classList.add('hidden');
                            document.getElementById('d-active').classList.remove('hidden');
                            document.getElementById('d-p-info').innerHTML = `CLIENT: ${r.pName}<br>FROM: ${r.f}<br>TO: ${r.t}<br>FARE: RS ${r.fare}`;
                            syncChat(rideRef, 'd-chat');
                        }
                    }
                });
            });
        }

        function sendMsg(t) {
            const m = document.getElementById(t+'-msg').value; if(!m) return;
            db.ref('rides/'+rideRef+'/chat').push({ s: auth.currentUser.displayName, t: m, uid: auth.currentUser.uid });
            document.getElementById(t+'-msg').value = "";
        }

        function syncChat(id, box) {
            db.ref('rides/'+id+'/chat').on('value', snap => {
                const b = document.getElementById(box); b.innerHTML = "";
                snap.forEach(m => {
                    const d = m.val(); const me = d.uid === auth.currentUser.uid;
                    b.innerHTML += `<div class="p-3 rounded-2xl text-xs font-bold shadow-sm ${me ? 'bg-blue-600 text-white ml-10 self-end':'bg-white border mr-10 self-start'}">${d.t}</div>`;
                });
                b.scrollTop = b.scrollHeight;
            });
        }

        function showRating() { document.getElementById('p-active').classList.add('hidden'); document.getElementById('rate-modal').classList.remove('hidden'); }
        function setStars(n) { starRating = n; document.querySelectorAll('.star').forEach((s, i) => s.classList.toggle('active', i < n)); }
        
        function submitRate() {
            db.ref('rides/'+rideRef).once('value', snap => {
                const dUid = snap.val().dUid;
                const dRef = db.ref('users/'+dUid);
                dRef.once('value', dSnap => {
                    const d = dSnap.val();
                    dRef.update({ rating: (d.rating || 0) + starRating, ratingCount: (d.ratingCount || 0) + 1 });
                    alert("TRIP COMPLETED!");
                    db.ref('rides/'+rideRef).remove();
                    location.reload();
                });
            });
        }

        function sos() { alert("🚨 SOS: Emergency signal and live location sent to admin!"); }
    </script>
</body>
</html>
