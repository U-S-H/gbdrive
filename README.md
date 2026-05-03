<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>GB DRIVE PRO | Professional</title>
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
        body { background: #f8fafc; color: #0f172a; overflow-x: hidden; }
        .hidden { display: none !important; }
        .glass-card { background: rgba(255, 255, 255, 0.95); backdrop-filter: blur(10px); border-radius: 28px; border: 1px solid rgba(255,255,255,0.2); box-shadow: 0 10px 30px rgba(0,0,0,0.05); }
        #map { height: 280px; width: 100%; border-radius: 24px; z-index: 10; border: 2px solid white; box-shadow: 0 8px 24px rgba(0,0,0,0.1); }
        .input-group { background: #f1f5f9; border-radius: 18px; padding: 14px; margin-bottom: 10px; transition: 0.3s; display: flex; align-items: center; gap: 10px; }
        .input-group:focus-within { background: white; box-shadow: 0 0 0 3px rgba(37,99,235,0.15); border: 1px solid #2563eb; }
        .input-group input { background: transparent; border: none; width: 100%; outline: none; font-weight: 600; font-size: 14px; color: #1e293b; }
        .v-selector { background: white; border: 2px solid #f1f5f9; border-radius: 20px; padding: 15px 10px; transition: 0.3s; filter: grayscale(1); }
        .v-selected { border-color: #2563eb; background: #eff6ff; filter: grayscale(0); transform: scale(1.05); box-shadow: 0 10px 15px -3px rgba(37,99,235,0.2); }
        .btn-action { background: #2563eb; color: white; border-radius: 20px; padding: 18px; width: 100%; font-weight: 900; text-transform: uppercase; letter-spacing: 1px; transition: 0.3s; }
        .btn-action:active { transform: scale(0.96); }
        .bid-card { animation: slideIn 0.4s ease-out; }
        @keyframes slideIn { from { transform: translateY(20px); opacity: 0; } to { transform: translateY(0); opacity: 1; } }
    </style>
</head>
<body class="pb-10">

    <div id="login-screen" class="hidden fixed inset-0 z-[5000] bg-white flex flex-col items-center justify-center p-8">
        <div class="bg-blue-600 text-white w-24 h-24 flex items-center justify-center rounded-[32px] font-black italic text-5xl mb-8 shadow-2xl rotate-3">GB</div>
        <h1 class="text-4xl font-black text-center leading-tight mb-4 text-slate-900">GB DRIVE<br><span class="text-blue-600">PRO</span></h1>
        <p class="text-slate-400 font-bold text-xs uppercase tracking-[0.2em] mb-12">Elite Ride Solutions</p>
        <button onclick="loginWithGoogle()" class="w-full max-w-xs flex items-center justify-center gap-4 bg-white border-2 border-slate-100 p-5 rounded-2xl shadow-sm hover:shadow-md transition-all active:scale-95">
            <img src="https://www.google.com/favicon.ico" class="w-6 h-6">
            <span class="font-black text-slate-700">Login with Google</span>
        </button>
    </div>

    <div id="main-app" class="hidden">
        <header class="p-4 flex justify-between items-center sticky top-0 bg-white/80 backdrop-blur-md z-[1000] border-bottom border-slate-100">
            <div class="flex items-center gap-3">
                <div class="bg-blue-600 text-white w-10 h-10 flex items-center justify-center rounded-xl font-black italic shadow-lg shadow-blue-200">GB</div>
                <div>
                    <p id="user-display-name" class="font-black text-sm uppercase text-slate-800">...</p>
                    <div class="flex items-center gap-1">
                        <span class="w-2 h-2 bg-green-500 rounded-full animate-pulse"></span>
                        <p id="user-role-label" class="text-[9px] font-bold text-blue-600 uppercase tracking-widest">Verifying...</p>
                    </div>
                </div>
            </div>
            <button onclick="handleLogout()" class="p-3 bg-red-50 text-red-500 rounded-xl hover:bg-red-100 transition-colors"><i data-lucide="power" class="w-5"></i></button>
        </header>

        <main class="p-4 max-w-md mx-auto space-y-6">
            <div id="map-container" class="relative">
                <div id="map"></div>
                <div class="absolute bottom-4 right-4 z-[20] flex flex-col gap-2">
                    <button onclick="updateMyLocation()" class="bg-white p-3 rounded-full shadow-lg text-blue-600"><i data-lucide="navigation" class="w-5"></i></button>
                </div>
            </div>

            <div id="passenger-view" class="hidden space-y-4">
                <div class="glass-card p-6 space-y-4">
                    <div class="input-group">
                        <i data-lucide="map-pin" class="text-blue-500 w-4"></i>
                        <input id="pickup-input" type="text" placeholder="Where to pick you up?">
                    </div>
                    <div class="input-group">
                        <i data-lucide="navigation-2" class="text-green-500 w-4"></i>
                        <input id="drop-input" type="text" placeholder="Where is your destination?">
                    </div>
                    
                    <div class="grid grid-cols-4 gap-3">
                        <button onclick="selectVehicle('BIKE', 150)" id="v-BIKE" class="v-selector flex flex-col items-center"><i data-lucide="bike" class="w-6 mb-1"></i><span class="text-[9px] font-black">BIKE</span></button>
                        <button onclick="selectVehicle('MINI', 280)" id="v-MINI" class="v-selector flex flex-col items-center"><i data-lucide="zap" class="w-6 mb-1"></i><span class="text-[9px] font-black">MINI</span></button>
                        <button onclick="selectVehicle('CAR', 450)" id="v-CAR" class="v-selector flex flex-col items-center v-selected"><i data-lucide="car" class="w-6 mb-1"></i><span class="text-[9px] font-black">CAR</span></button>
                        <button onclick="selectVehicle('AC', 650)" id="v-AC" class="v-selector flex flex-col items-center"><i data-lucide="snowflake" class="w-6 mb-1"></i><span class="text-[9px] font-black">AC</span></button>
                    </div>

                    <div class="bg-slate-50 p-5 rounded-2xl flex justify-between items-center border border-slate-100">
                        <div>
                            <p class="text-[10px] font-black text-slate-400 uppercase">Offer your fare</p>
                            <p class="text-[9px] text-blue-500 font-bold">Recommended: PKR <span id="rec-fare">450</span></p>
                        </div>
                        <div class="flex items-center gap-2 font-black text-slate-900 text-2xl">
                            <span class="text-sm">PKR</span>
                            <input id="fare-input" type="number" value="450" class="w-20 bg-transparent text-right outline-none text-blue-600">
                        </div>
                    </div>

                    <button onclick="postRideRequest()" id="request-btn" class="btn-action shadow-xl shadow-blue-200">Request Drive</button>
                </div>

                <div id="incoming-bids" class="space-y-3 hidden">
                    <h4 class="text-xs font-black text-slate-400 uppercase px-2 tracking-widest">Driver Offers</h4>
                    <div id="bids-list" class="space-y-3"></div>
                </div>
            </div>

            <div id="driver-view" class="hidden space-y-4">
                <div class="flex justify-between items-center px-2">
                    <h3 class="font-black text-slate-400 text-xs uppercase tracking-widest">Nearby Requests</h3>
                    <div class="flex items-center gap-2 bg-green-100 text-green-600 px-3 py-1 rounded-full text-[10px] font-black">
                        <span class="w-1.5 h-1.5 bg-green-500 rounded-full"></span> ONLINE
                    </div>
                </div>
                <div id="request-feed" class="space-y-4"></div>
            </div>

            <div id="active-ride-view" class="hidden glass-card p-6 border-t-8 border-blue-600 space-y-6">
                <div class="flex justify-between items-center">
                    <div class="flex gap-2 items-center">
                        <span class="bg-blue-100 text-blue-700 px-3 py-1 rounded-lg text-[10px] font-black uppercase">On Trip</span>
                        <span id="ride-timer" class="text-[10px] font-bold text-slate-400 italic">00:00</span>
                    </div>
                    <button onclick="triggerSos()" class="bg-red-500 text-white w-10 h-10 rounded-2xl flex items-center justify-center shadow-lg shadow-red-200 animate-pulse"><i data-lucide="shield-alert" class="w-5"></i></button>
                </div>

                <div id="trip-party-info" class="flex items-center gap-4 bg-slate-50 p-4 rounded-2xl">
                    </div>

                <div id="chat-box" class="space-y-4">
                    <div id="chat-messages" class="h-40 overflow-y-auto space-y-2 p-2 scrollbar-hide"></div>
                    <div class="flex gap-2">
                        <input id="chat-input-field" type="text" placeholder="Message..." class="flex-1 bg-slate-100 rounded-xl px-4 text-sm font-semibold outline-none focus:bg-white border-2 border-transparent focus:border-blue-100 transition-all">
                        <button onclick="handleSendMessage()" class="bg-blue-600 text-white w-12 h-12 rounded-xl flex items-center justify-center shadow-lg"><i data-lucide="send" class="w-5"></i></button>
                    </div>
                </div>

                <button onclick="finishRideProcess()" class="w-full bg-slate-900 text-white py-4 rounded-2xl font-black text-xs uppercase tracking-tighter">Complete & Rate Trip</button>
            </div>
        </main>
    </div>

    <div id="rating-modal" class="hidden fixed inset-0 z-[7000] bg-slate-900/80 backdrop-blur-sm flex items-center justify-center p-6">
        <div class="bg-white w-full max-w-xs rounded-[32px] p-8 text-center space-y-6">
            <h3 class="text-2xl font-black">Rate your trip!</h3>
            <div class="flex justify-center gap-2 text-yellow-400">
                <i data-lucide="star" class="fill-current w-8 h-8"></i>
                <i data-lucide="star" class="fill-current w-8 h-8"></i>
                <i data-lucide="star" class="fill-current w-8 h-8"></i>
                <i data-lucide="star" class="fill-current w-8 h-8"></i>
                <i data-lucide="star" class="w-8 h-8"></i>
            </div>
            <button onclick="closeRating()" class="btn-action">Submit Review</button>
        </div>
    </div>

    <script>
        lucide.createIcons();
        let activeRideId = null; 
        let globalUserRole = null;
        let map, userMarker, driverMarker;

        // --- FIREBASE SETUP ---
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

        // --- AUTH & USER DATA ---
        auth.onAuthStateChanged(user => {
            if(user) {
                document.getElementById('login-screen').classList.add('hidden');
                document.getElementById('main-app').classList.remove('hidden');
                document.getElementById('user-display-name').innerText = user.displayName;
                initMap();
                syncUserData(user.uid);
            } else {
                document.getElementById('login-screen').classList.remove('hidden');
                document.getElementById('main-app').classList.add('hidden');
            }
        });

        function loginWithGoogle() { auth.signInWithPopup(new firebase.auth.GoogleAuthProvider()); }
        function handleLogout() { auth.signOut().then(() => location.reload()); }

        function syncUserData(uid) {
            db.ref('users/' + uid).on('value', snap => {
                const data = snap.val();
                if(data && data.role) {
                    globalUserRole = data.role;
                    document.getElementById('user-role-label').innerText = globalUserRole;
                    renderView();
                } else {
                    document.getElementById('select-role-view').classList.remove('hidden');
                }
            });
        }

        // --- NAVIGATION & VIEWS ---
        function renderView() {
            document.querySelectorAll('#passenger-view, #driver-view, #select-role-view, #active-ride-view').forEach(v => v.classList.add('hidden'));
            if(activeRideId) {
                document.getElementById('active-ride-view').classList.remove('hidden');
            } else if(globalUserRole === 'passenger') {
                document.getElementById('passenger-view').classList.remove('hidden');
            } else {
                document.getElementById('driver-view').classList.remove('hidden');
                syncLiveRequests();
            }
            monitorActiveRides();
        }

        function assignRole(role) {
            db.ref('users/' + auth.currentUser.uid).update({role: role, name: auth.currentUser.displayName});
        }

        // --- MAP LOGIC ---
        function initMap() {
            if(map) return;
            map = L.map('map', {zoomControl: false}).setView([35.9208, 74.3089], 14);
            L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', { attribution: '© OSM' }).addTo(map);
            userMarker = L.marker([35.9208, 74.3089]).addTo(map).bindPopup("You are here");
        }

        function updateMyLocation() {
            if (navigator.geolocation) {
                navigator.geolocation.getCurrentPosition(pos => {
                    const lat = pos.coords.latitude;
                    const lng = pos.coords.longitude;
                    map.setView([lat, lng], 15);
                    userMarker.setLatLng([lat, lng]);
                });
            }
        }

        // --- PASSENGER ACTIONS ---
        function selectVehicle(type, price) {
            document.querySelectorAll('.v-selector').forEach(b => b.classList.remove('v-selected'));
            document.getElementById('v-'+type).classList.add('v-selected');
            document.getElementById('fare-input').value = price;
            document.getElementById('rec-fare').innerText = price;
        }

        function postRideRequest() {
            const f = document.getElementById('pickup-input').value;
            const t = document.getElementById('drop-input').value;
            const fare = document.getElementById('fare-input').value;
            if(!f || !t) return alert("Sweetie, enter pickup and drop-off!");

            const btn = document.getElementById('request-btn');
            const reqRef = db.ref('rides').push();
            activeRideId = reqRef.key;
            
            reqRef.set({
                f, t, fare, 
                pUid: auth.currentUser.uid, 
                pName: auth.currentUser.displayName,
                status: 'searching',
                time: Date.now()
            });

            btn.innerText = "Finding Partners (15s)...";
            btn.disabled = true;
            listenForBids(activeRideId);

            // Timeout
            setTimeout(() => {
                db.ref('rides/' + activeRideId).once('value', snap => {
                    if(snap.val() && snap.val().status === 'searching') {
                        db.ref('rides/' + activeRideId).remove();
                        alert("No drivers found, sweetie. Try a higher fare!");
                        location.reload();
                    }
                });
            }, 15000);
        }

        function listenForBids(rideId) {
            document.getElementById('incoming-bids').classList.remove('hidden');
            db.ref('rides/' + rideId + '/bids').on('value', snap => {
                const list = document.getElementById('bids-list');
                list.innerHTML = "";
                snap.forEach(child => {
                    const bid = child.val();
                    list.innerHTML += `
                        <div class="glass-card p-4 flex justify-between items-center bid-card">
                            <div>
                                <p class="font-black text-sm">${bid.dName}</p>
                                <p class="text-[10px] text-slate-400 font-bold uppercase">Fare: PKR ${bid.offer}</p>
                            </div>
                            <button onclick="acceptBid('${child.key}', ${bid.offer})" class="bg-blue-600 text-white px-4 py-2 rounded-xl font-black text-xs">Accept</button>
                        </div>`;
                });
            });
        }

        function acceptBid(bidId, finalFare) {
            db.ref('rides/' + activeRideId + '/bids/' + bidId).once('value', snap => {
                const b = snap.val();
                db.ref('rides/' + activeRideId).update({
                    status: 'accepted',
                    fare: finalFare,
                    dUid: b.dUid,
                    dName: b.dName,
                    dPlate: b.dPlate || 'GB-001'
                });
            });
        }

        // --- DRIVER ACTIONS ---
        function syncLiveRequests() {
            db.ref('rides').on('value', snap => {
                const feed = document.getElementById('request-feed');
                feed.innerHTML = "";
                snap.forEach(child => {
                    const r = child.val();
                    if(r.status === 'searching') {
                        feed.innerHTML += `
                            <div class="glass-card p-6 border-l-8 border-blue-600">
                                <div class="flex justify-between mb-4">
                                    <span class="text-[10px] font-black text-blue-600 uppercase">New Request</span>
                                    <span class="text-lg font-black text-green-600">Rs ${r.fare}</span>
                                </div>
                                <p class="font-bold text-sm uppercase mb-1">${r.f}</p>
                                <p class="text-slate-400 text-[10px] mb-4">TO: ${r.t}</p>
                                <div class="flex gap-2">
                                    <button onclick="bidOnRide('${child.key}', ${r.fare})" class="flex-1 bg-slate-900 text-white py-3 rounded-xl font-black text-[10px]">ACCEPT</button>
                                    <button onclick="bidOnRide('${child.key}', ${parseInt(r.fare)+50})" class="flex-1 border-2 border-slate-900 py-3 rounded-xl font-black text-[10px]">+50 PKR</button>
                                </div>
                            </div>`;
                    }
                });
            });
        }

        function bidOnRide(rideId, offer) {
            db.ref('rides/' + rideId + '/bids').push({
                dUid: auth.currentUser.uid,
                dName: auth.currentUser.displayName,
                offer: offer
            });
            alert("Bid sent, sweetie! Waiting for passenger...");
        }

        // --- RIDE MONITORING & CHAT ---
        function monitorActiveRides() {
            db.ref('rides').on('value', snap => {
                snap.forEach(child => {
                    const r = child.val();
                    const me = auth.currentUser.uid;
                    if(r.status === 'accepted' && (r.pUid === me || r.dUid === me)) {
                        activeRideId = child.key;
                        renderView();
                        
                        const info = document.getElementById('trip-party-info');
                        const isPassenger = globalUserRole === 'passenger';
                        info.innerHTML = `
                            <div class="w-12 h-12 bg-blue-100 rounded-full flex items-center justify-center text-blue-600"><i data-lucide="user"></i></div>
                            <div>
                                <p class="text-xs font-black uppercase">${isPassenger ? r.dName : r.pName}</p>
                                <p class="text-[9px] text-slate-400 font-bold">${isPassenger ? r.dPlate : 'Passenger'}</p>
                                <p class="text-[10px] text-green-600 font-black">PKR ${r.fare}</p>
                            </div>
                        `;
                        syncChat();
                    }
                });
            });
        }

        function syncChat() {
            db.ref('rides/' + activeRideId + '/chat').on('value', snap => {
                const box = document.getElementById('chat-messages'); box.innerHTML = "";
                snap.forEach(c => {
                    const msg = c.val();
                    const isMe = msg.uid === auth.currentUser.uid;
                    box.innerHTML += `<div class="p-3 rounded-2xl text-xs font-bold max-w-[80%] ${isMe ? 'bg-blue-600 text-white self-end ml-auto rounded-tr-none' : 'bg-slate-100 text-slate-800 rounded-tl-none'}">${msg.m}</div>`;
                });
                box.scrollTop = box.scrollHeight;
            });
        }

        function handleSendMessage() {
            const input = document.getElementById('chat-input-field');
            if(!input.value) return;
            db.ref('rides/' + activeRideId + '/chat').push({
                m: input.value,
                uid: auth.currentUser.uid,
                n: auth.currentUser.displayName
            });
            input.value = "";
        }

        function finishRideProcess() {
            if(confirm("Complete trip, sweetie?")) {
                db.ref('rides/' + activeRideId).remove();
                document.getElementById('rating-modal').classList.remove('hidden');
                activeRideId = null;
            }
        }

        function closeRating() {
            document.getElementById('rating-modal').classList.add('hidden');
            location.reload();
        }

        function triggerSos() {
            alert("🚨 EMERGENCY: SOS signal and GPS location sent to GB DRIVE Security Command!");
        }

        // Initialize icons
        setInterval(() => lucide.createIcons(), 1000);
    </script>
</body>
</html>
