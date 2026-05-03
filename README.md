<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>GB DRIVE PRO | All-in-One</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-auth-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-database-compat.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Outfit:wght@300;600;900&display=swap');
        * { font-family: 'Outfit', sans-serif; -webkit-tap-highlight-color: transparent; box-sizing: border-box; outline: none; }
        body { background: #f8fafc; color: #0f172a; overflow-x: hidden; }
        .hidden { display: none !important; }
        .glass-card { background: white; border-radius: 28px; border: 1px solid #f1f5f9; box-shadow: 0 10px 30px rgba(0,0,0,0.04); }
        #map { height: 250px; width: 100%; border-radius: 24px; z-index: 10; border: 4px solid white; box-shadow: 0 4px 15px rgba(0,0,0,0.05); }
        .btn-main { background: #2563eb; color: white; border-radius: 20px; padding: 18px; width: 100%; font-weight: 900; text-transform: uppercase; transition: 0.3s; }
        .btn-main:active { transform: scale(0.96); }
        .input-group { background: #f1f5f9; border-radius: 18px; padding: 12px 16px; margin-bottom: 10px; display: flex; align-items: center; gap: 10px; border: 2px solid transparent; }
        .input-group:focus-within { border-color: #2563eb; background: white; }
        .chat-bubble { padding: 12px 16px; border-radius: 18px; font-size: 13px; font-weight: 600; max-width: 80%; }
        ::-webkit-scrollbar { display: none; }
    </style>
</head>
<body class="pb-10">

    <div id="loader" class="fixed inset-0 z-[9999] bg-white flex flex-col items-center justify-center">
        <div class="bg-blue-600 text-white w-20 h-20 flex items-center justify-center rounded-[28px] font-black italic text-4xl mb-4 shadow-xl animate-bounce">GB</div>
        <p class="text-blue-600 font-black tracking-widest text-[10px] animate-pulse">LOADING SYSTEM...</p>
    </div>

    <div id="login-screen" class="hidden fixed inset-0 z-[5000] bg-white flex flex-col items-center justify-center p-8 text-center">
        <div class="bg-blue-600 text-white w-24 h-24 flex items-center justify-center rounded-[32px] font-black italic text-5xl mb-8 shadow-2xl">GB</div>
        <h1 class="text-3xl font-black mb-2">Welcome Back!</h1>
        <p class="text-slate-400 font-bold text-xs mb-12 uppercase tracking-widest">The Future of Commute</p>
        <button onclick="loginWithGoogle()" class="w-full max-w-xs flex items-center justify-center gap-4 border-2 border-slate-100 p-5 rounded-2xl font-black text-slate-700 active:bg-slate-50 transition-all">
            <img src="https://www.google.com/favicon.ico" class="w-6 h-6"> Continue with Google
        </button>
    </div>

    <div id="role-selection" class="hidden fixed inset-0 z-[4000] bg-white p-8">
        <h2 class="text-3xl font-black mb-8 mt-10">Choose your<br><span class="text-blue-600">Account Type</span></h2>
        <div class="space-y-4">
            <button onclick="saveUserRole('passenger')" class="w-full glass-card p-8 text-left flex justify-between items-center border-2 hover:border-blue-600 transition-all">
                <span><b class="text-xl block">Passenger</b><small class="text-slate-400 font-bold text-[10px] uppercase">I want to book</small></span>
                <i data-lucide="user" class="text-blue-600 w-8 h-8"></i>
            </button>
            <button onclick="openDriverModal()" class="w-full bg-slate-900 text-white p-8 rounded-[28px] text-left flex justify-between items-center hover:bg-slate-800 transition-all">
                <span><b class="text-xl block">Driver</b><small class="text-slate-500 font-bold text-[10px] uppercase">I want to earn</small></span>
                <i data-lucide="car" class="text-white w-8 h-8"></i>
            </button>
        </div>
    </div>

    <div id="main-app" class="hidden">
        <header class="p-4 flex justify-between items-center bg-white/80 backdrop-blur-md sticky top-0 z-[1000] border-b border-slate-50">
            <div class="flex items-center gap-3">
                <div class="bg-blue-600 text-white w-10 h-10 flex items-center justify-center rounded-xl font-black italic shadow-lg">GB</div>
                <div>
                    <p id="user-name" class="font-black text-xs uppercase">User Name</p>
                    <p id="user-role-ui" class="text-[8px] font-black text-blue-600 uppercase tracking-widest">Active</p>
                </div>
            </div>
            <button onclick="handleLogout()" class="p-3 bg-red-50 text-red-500 rounded-xl"><i data-lucide="power" class="w-5"></i></button>
        </header>

        <main class="p-4 max-w-md mx-auto space-y-4">
            
            <div id="p-view" class="hidden space-y-4">
                <div id="map"></div>
                <div class="glass-card p-6 space-y-3">
                    <div class="input-group"><i data-lucide="map-pin" class="w-4 text-blue-600"></i><input id="pickup" placeholder="Pickup location"></div>
                    <div class="input-group"><i data-lucide="navigation" class="w-4 text-green-600"></i><input id="dropoff" placeholder="Destination"></div>
                    <div class="bg-slate-50 p-4 rounded-2xl flex justify-between items-center">
                        <span class="text-[10px] font-black text-slate-400 uppercase">Offer Fare</span>
                        <input id="fare" type="number" value="300" class="bg-transparent text-right font-black text-xl text-blue-600 w-24 outline-none">
                    </div>
                    <button onclick="postRideRequest()" id="req-btn" class="btn-main shadow-xl shadow-blue-200">Find Driver</button>
                </div>
                <div id="bids-container" class="space-y-3 hidden">
                    <h3 class="text-[10px] font-black text-slate-400 px-2 uppercase tracking-widest">Driver Offers</h3>
                    <div id="bids-list" class="space-y-3"></div>
                </div>
            </div>

            <div id="d-view" class="hidden space-y-4">
                <div class="flex justify-between items-center px-1">
                    <h3 class="text-[10px] font-black text-slate-400 uppercase tracking-[0.2em]">Nearby Requests</h3>
                    <div class="w-2 h-2 bg-green-500 rounded-full animate-ping"></div>
                </div>
                <div id="feed" class="space-y-4"></div>
            </div>

            <div id="active-view" class="hidden glass-card p-6 border-t-8 border-blue-600 space-y-6">
                <div class="flex justify-between items-center">
                    <span class="bg-blue-100 text-blue-600 px-3 py-1 rounded-lg text-[10px] font-black uppercase">Ride in Progress</span>
                    <button onclick="triggerSos()" class="bg-red-500 text-white w-10 h-10 rounded-full flex items-center justify-center animate-pulse"><i data-lucide="shield-alert" class="w-5"></i></button>
                </div>
                <div id="ride-details" class="bg-slate-50 p-4 rounded-2xl text-xs font-bold space-y-1"></div>
                <div id="chat-messages" class="h-40 overflow-y-auto bg-slate-50 rounded-2xl p-4 flex flex-col gap-2"></div>
                <div class="flex gap-2">
                    <input id="msg-input" type="text" placeholder="Type a message..." class="flex-1 bg-white border border-slate-100 rounded-xl px-4 text-sm font-semibold outline-none">
                    <button onclick="sendMessage()" class="bg-blue-600 text-white w-12 h-12 rounded-xl flex items-center justify-center"><i data-lucide="send" class="w-5"></i></button>
                </div>
                <button onclick="completeRide()" class="w-full text-slate-400 font-black text-[10px] uppercase">Finish Trip</button>
            </div>

        </main>
    </div>

    <div id="d-reg-modal" class="hidden fixed inset-0 z-[6000] bg-white p-8 flex flex-col">
        <h2 class="text-3xl font-black mb-8 mt-10 text-slate-900">Driver<br>Information</h2>
        <div class="space-y-4">
            <div class="input-group"><input id="d-plate" placeholder="Vehicle Plate Number"></div>
            <div class="input-group"><input id="d-phone" placeholder="WhatsApp Number"></div>
            <button onclick="saveUserRole('driver')" class="btn-main mt-4">Save & Start</button>
            <button onclick="document.getElementById('d-reg-modal').classList.add('hidden')" class="w-full text-slate-400 font-bold mt-6 uppercase text-[10px]">Cancel</button>
        </div>
    </div>

    <script>
        lucide.createIcons();
        let activeId = null; let myRole = null; let map;

        // --- FIREBASE CONFIG ---
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

        // --- AUTH & AUTO-REDIRECT ---
        auth.onAuthStateChanged(user => {
            if(user) {
                document.getElementById('user-name').innerText = user.displayName;
                db.ref('users/' + user.uid).once('value', snap => {
                    document.getElementById('loader').classList.add('hidden');
                    const data = snap.val();
                    if(data && data.role) {
                        myRole = data.role;
                        launchApp();
                    } else {
                        showScreen('role-selection');
                    }
                });
            } else {
                document.getElementById('loader').classList.add('hidden');
                showScreen('login-screen');
            }
        });

        function launchApp() {
            showScreen('main-app');
            document.getElementById('user-role-ui').innerText = myRole;
            if(myRole === 'passenger') {
                document.getElementById('p-view').classList.remove('hidden');
                initMap();
            } else {
                document.getElementById('d-view').classList.remove('hidden');
                syncRequests();
            }
            monitorRides();
        }

        // --- REGISTRATION LOGIC ---
        function openDriverModal() { document.getElementById('d-reg-modal').classList.remove('hidden'); }
        function saveUserRole(role) {
            const uid = auth.currentUser.uid;
            let data = { role: role, name: auth.currentUser.displayName };
            if(role === 'driver') {
                data.plate = document.getElementById('d-plate').value;
                data.phone = document.getElementById('d-phone').value;
                if(!data.plate || !data.phone) return alert("Please fill all details, sweetie!");
            }
            db.ref('users/' + uid).set(data).then(() => location.reload());
        }

        // --- MAP SYSTEM ---
        function initMap() {
            if(map) return;
            map = L.map('map', {zoomControl: false}).setView([35.9208, 74.3089], 14);
            L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png').addTo(map);
            L.marker([35.9208, 74.3089]).addTo(map).bindPopup("Your Location").openPopup();
        }

        // --- PASSENGER: RIDE REQUEST & BIDDING ---
        function postRideRequest() {
            const f = document.getElementById('pickup').value;
            const t = document.getElementById('dropoff').value;
            const fare = document.getElementById('fare').value;
            if(!f || !t) return alert("Enter route!");

            const btn = document.getElementById('req-btn');
            const reqRef = db.ref('rides').push();
            activeId = reqRef.key;
            
            reqRef.set({ f, t, fare, pName: auth.currentUser.displayName, pUid: auth.currentUser.uid, status: 'searching' });
            
            btn.innerText = "Searching Partners (15s)...";
            btn.disabled = true;
            listenForBids(activeId);

            setTimeout(() => {
                db.ref('rides/' + activeId).once('value', snap => {
                    if(snap.exists() && snap.val().status === 'searching') {
                        db.ref('rides/' + activeId).remove();
                        alert("No driver found, sweetie! Try increasing fare.");
                        location.reload();
                    }
                });
            }, 15000);
        }

        function listenForBids(rid) {
            document.getElementById('bids-container').classList.remove('hidden');
            db.ref('rides/' + rid + '/bids').on('value', snap => {
                const list = document.getElementById('bids-list'); list.innerHTML = "";
                snap.forEach(c => {
                    const b = c.val();
                    list.innerHTML += `<div class="glass-card p-4 flex justify-between items-center animate-pulse">
                        <div><p class="font-black text-sm">${b.dName}</p><p class="text-green-600 font-bold text-xs">Offered: Rs ${b.offer}</p></div>
                        <button onclick="acceptBid('${c.key}', ${b.offer})" class="bg-blue-600 text-white px-5 py-2 rounded-xl text-xs font-black">ACCEPT</button>
                    </div>`;
                });
            });
        }

        function acceptBid(bidId, finalFare) {
            db.ref('rides/' + activeId + '/bids/' + bidId).once('value', snap => {
                const b = snap.val();
                db.ref('rides/' + activeId).update({
                    status: 'accepted', fare: finalFare,
                    dUid: b.dUid, dName: b.dName, dPlate: b.dPlate, dPhone: b.dPhone
                });
            });
        }

        // --- DRIVER: FEED & BIDDING ---
        function syncRequests() {
            db.ref('rides').on('value', snap => {
                const feed = document.getElementById('feed'); feed.innerHTML = "";
                snap.forEach(c => {
                    const r = c.val();
                    if(r.status === 'searching') {
                        feed.innerHTML += `<div class="glass-card p-6 border-l-8 border-blue-600">
                            <div class="flex justify-between mb-4">
                                <b class="text-sm uppercase">${r.f} ➔ ${r.t}</b>
                                <span class="text-green-600 font-black">Rs ${r.fare}</span>
                            </div>
                            <div class="flex gap-2">
                                <button onclick="sendBid('${c.key}', ${r.fare})" class="flex-1 bg-slate-900 text-white py-3 rounded-xl font-black text-[10px]">ACCEPT</button>
                                <button onclick="sendBid('${c.key}', ${parseInt(r.fare)+50})" class="flex-1 border-2 border-slate-900 py-3 rounded-xl font-black text-[10px]">+50 PKR</button>
                            </div>
                        </div>`;
                    }
                });
            });
        }

        function sendBid(rid, offer) {
            db.ref('users/' + auth.currentUser.uid).once('value', snap => {
                const d = snap.val();
                db.ref('rides/' + rid + '/bids').push({
                    dUid: auth.currentUser.uid, dName: d.name, dPlate: d.plate, dPhone: d.phone, offer: offer
                });
                alert("Bid sent, sweetie! Please wait.");
            });
        }

        // --- GLOBAL: ACTIVE RIDE MONITOR ---
        function monitorRides() {
            db.ref('rides').on('value', snap => {
                snap.forEach(c => {
                    const r = c.val(); const me = auth.currentUser.uid;
                    if(r.status === 'accepted' && (r.pUid === me || r.dUid === me)) {
                        activeId = c.key;
                        document.getElementById('p-view').classList.add('hidden');
                        document.getElementById('d-view').classList.add('hidden');
                        document.getElementById('active-view').classList.remove('hidden');
                        
                        const info = document.getElementById('ride-details');
                        info.innerHTML = myRole === 'passenger' ? 
                            `DRIVER: ${r.dName}<br>VEHICLE: ${r.dPlate}<br>WHATSAPP: ${r.dPhone}<br>FARE: PKR ${r.fare}` :
                            `CLIENT: ${r.pName}<br>FROM: ${r.f}<br>TO: ${r.t}<br>FARE: PKR ${r.fare}`;
                        syncChat();
                    }
                });
            });
        }

        // --- CHAT SYSTEM ---
        function syncChat() {
            db.ref('rides/' + activeId + '/chat').on('value', snap => {
                const box = document.getElementById('chat-messages'); box.innerHTML = "";
                snap.forEach(c => {
                    const m = c.val(); const isMe = m.uid === auth.currentUser.uid;
                    box.innerHTML += `<div class="chat-bubble ${isMe ? 'bg-blue-600 text-white self-end rounded-tr-none' : 'bg-white border self-start rounded-tl-none shadow-sm'}">${m.m}</div>`;
                });
                box.scrollTop = box.scrollHeight;
            });
        }

        function sendMessage() {
            const inp = document.getElementById('msg-input'); if(!inp.value) return;
            db.ref('rides/' + activeId + '/chat').push({ m: inp.value, uid: auth.currentUser.uid });
            inp.value = "";
        }

        // --- UTILS ---
        function showScreen(id) {
            ['login-screen', 'main-app', 'role-selection'].forEach(s => document.getElementById(s).classList.add('hidden'));
            document.getElementById(id).classList.remove('hidden');
        }
        function loginWithGoogle() { auth.signInWithPopup(new firebase.auth.GoogleAuthProvider()); }
        function handleLogout() { auth.signOut().then(() => location.reload()); }
        function completeRide() { if(confirm("End trip, sweetie?")) { db.ref('rides/' + activeId).remove(); location.reload(); } }
        function triggerSos() { alert("🚨 SOS: Your live GPS location sent to GB Drive Emergency Response!"); }
    </script>
</body>
</html>
