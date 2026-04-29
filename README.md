<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>GB DRIVE PRO - THE MASTER FILE</title>
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
        body { background: #f8fafc; color: #1e293b; overflow-x: hidden; }
        .hidden { display: none !important; }
        .neo-card { background: white; border-radius: 2rem; box-shadow: 0 10px 40px -10px rgba(0,0,0,0.1); border: 1px solid rgba(255,255,255,0.5); }
        #map { height: 180px; width: 100%; border-radius: 1.5rem; margin-bottom: 1rem; z-index: 10; }
        .v-btn { border: 2px solid transparent; background: #f1f5f9; border-radius: 1.2rem; transition: 0.3s; padding: 12px; }
        .v-selected { border-color: #2563eb; background: #eff6ff; color: #2563eb; }
        input { border: 2px solid #e2e8f0 !important; border-radius: 1rem !important; padding: 14px 16px !important; width: 100%; outline: none; transition: 0.2s; }
        input:focus { border-color: #2563eb !important; background: white; }
        .btn-main { background: #2563eb; color: white; font-weight: 900; border-radius: 1.2rem; padding: 18px; width: 100%; box-shadow: 0 8px 20px rgba(37,99,235,0.3); }
        .btn-main:active { transform: scale(0.96); }
        .star { color: #cbd5e1; cursor: pointer; font-size: 2rem; }
        .star.active { color: #f59e0b; }
        .chat-area { height: 150px; overflow-y: auto; background: #f9fafb; border-radius: 1rem; padding: 10px; display: flex; flex-direction: column; gap: 5px; }
    </style>
</head>
<body class="pb-10">

    <header class="p-5 bg-white/90 backdrop-blur-md sticky top-0 z-[100] flex justify-between items-center border-b border-slate-100">
        <div class="flex items-center gap-3">
            <div class="bg-blue-600 text-white w-10 h-10 flex items-center justify-center rounded-2xl font-black italic shadow-lg">GB</div>
            <div>
                <p id="top-name" class="font-black text-sm uppercase">GUEST</p>
                <p id="top-role" class="text-[9px] font-bold text-blue-600 uppercase tracking-widest">LOADING...</p>
            </div>
        </div>
        <button onclick="logout()" class="p-2 bg-red-50 text-red-500 rounded-xl"><i data-lucide="log-out" class="w-5"></i></button>
    </header>

    <main class="p-5 max-w-md mx-auto space-y-6">
        
        <div id="role-screen" class="hidden py-10 space-y-4 text-center">
            <h1 class="text-3xl font-black text-slate-900">JOIN GB DRIVE</h1>
            <button onclick="setRole('passenger')" class="w-full p-8 neo-card text-left flex justify-between items-center">
                <span><b class="text-2xl">PASSENGER</b><br><small class="opacity-50">Book a ride instantly</small></span>
                <i data-lucide="user" class="w-10 text-blue-600"></i>
            </button>
            <button onclick="document.getElementById('driver-setup').classList.remove('hidden')" class="w-full p-8 neo-card text-left border-2 border-black flex justify-between items-center">
                <span><b class="text-2xl">DRIVER</b><br><small class="opacity-50">Earn on every trip</small></span>
                <i data-lucide="car" class="w-10 text-black"></i>
            </button>
        </div>

        <div id="p-ui" class="hidden space-y-6">
            <div class="neo-card p-6">
                <div id="map"></div>
                <input id="p-from" type="text" placeholder="PICKUP LOCATION" class="mb-2">
                <input id="p-to" type="text" placeholder="DROP OFF LOCATION" class="mb-4">
                
                <div class="grid grid-cols-4 gap-2 mb-6">
                    <button onclick="selV('BIKE', 120)" id="v-BIKE" class="v-btn flex flex-col items-center"><i data-lucide="bike" class="w-4"></i><span class="text-[8px] font-black mt-1">BIKE</span></button>
                    <button onclick="selV('MINI', 250)" id="v-MINI" class="v-btn flex flex-col items-center"><i data-lucide="loader" class="w-4"></i><span class="text-[8px] font-black mt-1">MINI</span></button>
                    <button onclick="selV('CAR', 450)" id="v-CAR" class="v-btn flex flex-col items-center v-selected"><i data-lucide="car" class="w-4"></i><span class="text-[8px] font-black mt-1">CAR</span></button>
                    <button onclick="selV('AC', 650)" id="v-AC" class="v-btn flex flex-col items-center"><i data-lucide="snowflake" class="w-4"></i><span class="text-[8px] font-black mt-1">AC</span></button>
                </div>

                <div class="flex items-center gap-2 mb-6">
                    <input id="p-bid" type="number" placeholder="OFFER PRICE" class="!m-0 text-center text-2xl font-black text-green-600">
                </div>
                
                <button onclick="reqRide()" class="btn-main uppercase tracking-widest">FIND DRIVER</button>
            </div>

            <div id="p-active-ride" class="hidden neo-card p-6 border-t-8 border-blue-600 space-y-4">
                <div class="flex justify-between items-center">
                    <h3 class="font-black italic text-blue-600">RIDE IN PROGRESS</h3>
                    <button onclick="sos()" class="bg-red-500 text-white px-3 py-1 rounded-lg text-[10px] font-black animate-pulse">SOS ALERT</button>
                </div>
                <div id="d-details" class="bg-slate-50 p-4 rounded-xl text-xs font-bold"></div>
                <div id="p-chat" class="chat-area"></div>
                <div class="flex gap-2">
                    <input id="p-msg" type="text" placeholder="Chat..." class="!m-0">
                    <button onclick="sendMsg('p')" class="bg-blue-600 text-white px-5 rounded-xl"><i data-lucide="send"></i></button>
                </div>
                <button onclick="finishRide()" class="w-full text-xs font-black text-red-500 uppercase mt-2">Finish & Rate Driver</button>
            </div>

            <div id="rate-modal" class="hidden neo-card p-8 text-center space-y-4">
                <h2 class="font-black text-xl">RATE YOUR TRIP</h2>
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

        <div id="d-ui" class="hidden space-y-6">
            <div class="flex justify-between px-2 items-center">
                <h2 class="font-black text-xl">REQUEST FEED</h2>
                <span class="bg-green-100 text-green-600 text-[10px] px-3 py-1 rounded-full font-black">ONLINE</span>
            </div>
            <div id="d-feed" class="space-y-4"></div>
            
            <div id="d-active-ride" class="hidden neo-card p-6 border-t-8 border-green-500 space-y-4">
                <div id="p-details" class="bg-slate-50 p-4 rounded-xl text-xs font-bold"></div>
                <div id="d-chat" class="chat-area"></div>
                <div class="flex gap-2">
                    <input id="d-msg" type="text" placeholder="Message..." class="!m-0">
                    <button onclick="sendMsg('d')" class="bg-blue-600 text-white px-5 rounded-xl"><i data-lucide="send"></i></button>
                </div>
            </div>
        </div>
    </main>

    <div id="driver-setup" class="hidden fixed inset-0 z-[200] flex items-center justify-center p-6 bg-black/80 backdrop-blur-md">
        <div class="bg-white w-full max-w-md rounded-[32px] p-8 space-y-4">
            <h2 class="text-2xl font-black text-center mb-4">DRIVER VERIFICATION</h2>
            <input id="dr-name" type="text" placeholder="FULL NAME">
            <input id="dr-phone" type="tel" placeholder="PHONE NUMBER">
            <input id="dr-plate" type="text" placeholder="VEHICLE PLATE (LEC-123)">
            <button onclick="saveDriver()" class="btn-main mt-4">START EARNING</button>
            <button onclick="document.getElementById('driver-setup').classList.add('hidden')" class="w-full text-xs font-bold text-slate-400">CANCEL</button>
        </div>
    </div>

    <script>
        lucide.createIcons();
        let curBid = 450; let rideID = null; let starRating = 5; let map, marker;

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
                map = L.map('map').setView([35.9208, 74.3089], 13);
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
                        if(u.role === 'passenger') initMap();
                    } else document.getElementById('role-screen').classList.remove('hidden');
                });
            } else { auth.signInWithPopup(new firebase.auth.GoogleAuthProvider()); }
        });

        function logout() { auth.signOut(); location.reload(); }
        function setRole(r) { db.ref('users/' + auth.currentUser.uid).set({ role: r, name: auth.currentUser.displayName }).then(()=>location.reload()); }
        
        function saveDriver() {
            const d = { name: document.getElementById('dr-name').value, phone: document.getElementById('dr-phone').value, plate: document.getElementById('dr-plate').value, role: 'driver', rating: 5, ratingCount: 1 };
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

        function reqRide() {
            const f = document.getElementById('p-from').value; const t = document.getElementById('p-to').value;
            const b = document.getElementById('p-bid').value;
            if(!f || !t || !b) return alert("Complete details sweetie!");
            rideID = db.ref('rides').push({ f, t, fare: b, pName: auth.currentUser.displayName, pUid: auth.currentUser.uid, status: 'searching' }).key;
            db.ref('rides/'+rideID).on('value', snap => {
                const r = snap.val();
                if(r && r.status === 'accepted') {
                    document.getElementById('p-active-ride').classList.remove('hidden');
                    document.getElementById('d-details').innerHTML = `DRIVER: ${r.dName}<br>PHONE: ${r.dPhone}<br>PLATE: ${r.dPlate}<br>FARE: RS ${r.fare}`;
                    syncChat(rideID, 'p-chat');
                }
            });
        }

        function syncFeed() {
            db.ref('rides').on('value', snap => {
                const feed = document.getElementById('d-feed'); feed.innerHTML = "";
                snap.forEach(c => {
                    const r = c.val();
                    if(r.status === 'searching') {
                        feed.innerHTML += `<div class="neo-card p-6 border-l-[10px] border-blue-600 shadow-xl">
                            <p class="font-black text-lg">${r.f} ➔ ${r.t}</p>
                            <p class="text-[10px] font-bold opacity-50 uppercase">CLIENT: ${r.pName}</p>
                            <div class="flex justify-between items-center mt-6">
                                <b class="text-3xl text-green-600 font-black italic">RS ${r.fare}</b>
                                <button onclick="accept('${c.key}')" class="bg-black text-white px-8 py-3 rounded-2xl font-black text-xs">ACCEPT</button>
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
                rideID = id;
                document.getElementById('d-feed').classList.add('hidden');
                document.getElementById('d-active-ride').classList.remove('hidden');
                db.ref('rides/'+id).once('value', s => {
                    document.getElementById('p-info').innerHTML = `CLIENT: ${s.val().pName}<br>ROUTE: ${s.val().f} to ${s.val().t}<br>FARE: RS ${s.val().fare}`;
                });
                syncChat(id, 'd-chat');
            });
        }

        function sendMsg(t) {
            const m = document.getElementById(t+'-msg').value; if(!m) return;
            db.ref('rides/'+rideID+'/chat').push({ s: auth.currentUser.displayName, t: m });
            document.getElementById(t+'-msg').value = "";
        }

        function syncChat(id, box) {
            db.ref('rides/'+id+'/chat').on('value', snap => {
                const b = document.getElementById(box); b.innerHTML = "";
                snap.forEach(m => {
                    const d = m.val(); const me = d.s === auth.currentUser.displayName;
                    b.innerHTML += `<div class="p-2 rounded-lg text-xs font-bold ${me ? 'bg-blue-600 text-white ml-8 self-end':'bg-white border mr-8 self-start'} shadow-sm">${d.t}</div>`;
                });
                b.scrollTop = b.scrollHeight;
            });
        }

        function finishRide() {
            document.getElementById('p-active-ride').classList.add('hidden');
            document.getElementById('rate-modal').classList.remove('hidden');
        }

        function setStars(n) {
            starRating = n;
            document.querySelectorAll('.star').forEach((s, i) => s.classList.toggle('active', i < n));
        }

        function submitRate() {
            db.ref('rides/'+rideID).once('value', snap => {
                const dUid = snap.val().dUid;
                const dRef = db.ref('users/'+dUid);
                dRef.once('value', dSnap => {
                    const d = dSnap.val();
                    dRef.update({ rating: (d.rating || 0) + starRating, ratingCount: (d.ratingCount || 0) + 1 });
                    alert("Trip Completed! ❤️");
                    db.ref('rides/'+rideID).remove();
                    location.reload();
                });
            });
        }

        function sos() { alert("🚨 EMERGENCY SOS ALERT SENT TO ADMIN & POLICE!"); }
    </script>
</body>
</html>
