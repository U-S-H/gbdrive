<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>GB DRIVE PRO | Professional Edition</title>
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
        #map { height: 220px; width: 100%; border-radius: 24px; z-index: 10; border: 4px solid white; box-shadow: 0 4px 15px rgba(0,0,0,0.05); }
        .btn-main { background: #2563eb; color: white; border-radius: 20px; padding: 18px; width: 100%; font-weight: 900; text-transform: uppercase; transition: 0.3s; box-shadow: 0 10px 20px rgba(37,99,235,0.2); }
        .btn-main:active { transform: scale(0.96); }
        .input-group { background: #f1f5f9; border-radius: 18px; padding: 12px 16px; margin-bottom: 10px; display: flex; align-items: center; gap: 10px; border: 2px solid transparent; }
        .input-group:focus-within { border-color: #2563eb; background: white; }
        .input-group select, .input-group input { background: transparent; border: none; width: 100%; outline: none; font-weight: 600; font-size: 14px; color: #1e293b; appearance: none; }
        .chat-bubble { padding: 12px 16px; border-radius: 18px; font-size: 13px; font-weight: 600; max-width: 80%; }
        ::-webkit-scrollbar { display: none; }
    </style>
</head>
<body class="pb-10">

    <div id="loader" class="fixed inset-0 z-[9999] bg-white flex flex-col items-center justify-center text-center p-6">
        <div class="bg-blue-600 text-white w-20 h-20 flex items-center justify-center rounded-[28px] font-black italic text-4xl mb-4 shadow-xl animate-bounce">GB</div>
        <h2 class="text-xl font-black text-slate-800">GB DRIVE PRO</h2>
        <p class="text-blue-600 font-black tracking-widest text-[9px] mt-2 uppercase animate-pulse">Initializing Secure Connection...</p>
    </div>

    <div id="login-screen" class="hidden fixed inset-0 z-[5000] bg-white flex flex-col items-center justify-center p-8 text-center">
        <div class="bg-blue-600 text-white w-24 h-24 flex items-center justify-center rounded-[32px] font-black italic text-5xl mb-8 shadow-2xl">GB</div>
        <h1 class="text-3xl font-black mb-2 text-slate-900">Elite Transport</h1>
        <p class="text-slate-400 font-bold text-xs mb-12 uppercase tracking-widest">Premium Rides for the Region</p>
        <button onclick="loginWithGoogle()" class="w-full max-w-xs flex items-center justify-center gap-4 border-2 border-slate-100 p-5 rounded-2xl font-black text-slate-700 active:bg-slate-50 transition-all">
            <img src="https://www.google.com/favicon.ico" class="w-6 h-6"> Login with Google
        </button>
    </div>

    <div id="signup-profile" class="hidden fixed inset-0 z-[4000] bg-white p-8 overflow-y-auto">
        <h2 class="text-3xl font-black mb-6 mt-10 text-slate-900 leading-tight">Complete Your<br><span class="text-blue-600">Profile Sweetie</span></h2>
        
        <div class="space-y-4">
            <p class="text-[10px] font-black text-slate-400 uppercase px-1">Where are you located?</p>
            <div class="input-group">
                <i data-lucide="map-pin" class="w-4 text-blue-600"></i>
                <select id="user-city">
                    <option value="" disabled selected>Select your city...</option>
                    <option value="Gilgit">Gilgit City</option>
                    <option value="Skardu">Skardu</option>
                    <option value="Hunza">Hunza</option>
                    <option value="Diamer">Diamer (Chilas)</option>
                    <option value="Ghizer">Ghizer</option>
                    <option value="Ghanche">Ghanche</option>
                    <option value="Astore">Astore</option>
                    <option value="Nagar">Nagar</option>
                </select>
            </div>

            <p class="text-[10px] font-black text-slate-400 uppercase px-1 mt-4">Account Type</p>
            <div class="grid grid-cols-2 gap-3">
                <button id="role-p" onclick="setRegRole('passenger')" class="glass-card p-4 text-center font-black text-[10px] border-2">PASSENGER</button>
                <button id="role-d" onclick="setRegRole('driver')" class="glass-card p-4 text-center font-black text-[10px] border-2">DRIVER</button>
            </div>

            <div id="driver-details" class="hidden space-y-4 mt-4">
                <div class="input-group"><input id="d-plate" placeholder="Vehicle Number Plate"></div>
                <div class="input-group"><input id="d-phone" placeholder="WhatsApp Contact"></div>
            </div>

            <button onclick="finishSignup()" class="btn-main mt-6">Get Started</button>
        </div>
    </div>

    <div id="main-app" class="hidden">
        <header class="p-4 flex justify-between items-center bg-white sticky top-0 z-[1000] border-b border-slate-50">
            <div class="flex items-center gap-3">
                <div class="bg-blue-600 text-white w-10 h-10 flex items-center justify-center rounded-xl font-black italic shadow-lg">GB</div>
                <div>
                    <p id="ui-name" class="font-black text-xs uppercase">User</p>
                    <p id="ui-city-tag" class="text-[8px] font-black text-blue-600 uppercase tracking-widest italic">Location</p>
                </div>
            </div>
            <button onclick="handleLogout()" class="p-3 bg-red-50 text-red-500 rounded-xl"><i data-lucide="power" class="w-5"></i></button>
        </header>

        <main class="p-4 max-w-md mx-auto space-y-4">
            
            <div id="p-view" class="hidden space-y-4">
                <div id="map"></div>
                <div class="glass-card p-6 space-y-3">
                    <div class="input-group"><i data-lucide="map-pin" class="w-4 text-blue-600"></i><input id="p-from" placeholder="Pick me up from..."></div>
                    <div class="input-group"><i data-lucide="navigation" class="w-4 text-green-600"></i><input id="p-to" placeholder="Drop me off at..."></div>
                    <div class="bg-slate-50 p-4 rounded-2xl flex justify-between items-center border border-slate-100">
                        <span class="text-[10px] font-black text-slate-400 uppercase">Offer Price (PKR)</span>
                        <input id="p-fare" type="number" value="300" class="bg-transparent text-right font-black text-xl text-blue-600 w-24 outline-none">
                    </div>
                    <button onclick="postRideRequest()" id="req-btn" class="btn-main">Find Driver in <span id="btn-city">City</span></button>
                </div>
                <div id="bids-section" class="hidden space-y-3 pb-10">
                    <h3 class="text-[10px] font-black text-slate-400 px-2 uppercase tracking-widest">Driver Counter-Offers</h3>
                    <div id="bids-list" class="space-y-3"></div>
                </div>
            </div>

            <div id="d-view" class="hidden space-y-4">
                <div class="flex justify-between items-center px-1">
                    <h3 class="text-[10px] font-black text-slate-400 uppercase tracking-widest">Requests in <span id="feed-city" class="text-blue-600">...</span></h3>
                    <div class="flex items-center gap-2"><span class="w-2 h-2 bg-green-500 rounded-full animate-ping"></span><b class="text-[10px] font-black text-green-600">ONLINE</b></div>
                </div>
                <div id="ride-feed" class="space-y-4"></div>
            </div>

            <div id="active-trip" class="hidden glass-card p-6 border-t-8 border-blue-600 space-y-6">
                <div class="flex justify-between items-center">
                    <span class="bg-blue-100 text-blue-600 px-3 py-1 rounded-lg text-[10px] font-black uppercase">Ride Active</span>
                    <button onclick="triggerSos()" class="bg-red-500 text-white w-10 h-10 rounded-full flex items-center justify-center shadow-lg animate-pulse"><i data-lucide="shield-alert" class="w-5"></i></button>
                </div>
                <div id="trip-info" class="bg-slate-50 p-4 rounded-2xl text-xs font-bold text-slate-600 leading-relaxed border border-slate-100"></div>
                
                <div id="chat-messages" class="h-44 overflow-y-auto bg-slate-50 rounded-2xl p-4 flex flex-col gap-2 border border-slate-100"></div>
                <div class="flex gap-2">
                    <input id="chat-input" type="text" placeholder="Message partner..." class="flex-1 bg-white border border-slate-200 rounded-xl px-4 text-sm font-semibold outline-none focus:border-blue-500 transition-all">
                    <button onclick="sendChatMessage()" class="bg-blue-600 text-white w-12 h-12 rounded-xl flex items-center justify-center shadow-lg shadow-blue-100"><i data-lucide="send" class="w-5"></i></button>
                </div>
                <button onclick="finishRide()" class="w-full text-slate-400 font-black text-[10px] uppercase hover:text-red-500 transition-colors">Finish Trip Sweetie</button>
            </div>

        </main>
    </div>

    <script>
        lucide.createIcons();
        let myRole = null; let myCity = null; let activeRideId = null; let map;

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

        // --- AUTH LOGIC (AUTO-REDIRECT) ---
        auth.onAuthStateChanged(user => {
            if(user) {
                db.ref('users/' + user.uid).once('value', snap => {
                    document.getElementById('loader').classList.add('hidden');
                    const data = snap.val();
                    if(data && data.role && data.city) {
                        myRole = data.role; myCity = data.city;
                        launchApp(data);
                    } else {
                        showScreen('signup-profile');
                    }
                });
            } else {
                document.getElementById('loader').classList.add('hidden');
                showScreen('login-screen');
            }
        });

        // --- REGISTRATION LOGIC ---
        let selectedRegRole = null;
        function setRegRole(r) {
            selectedRegRole = r;
            document.getElementById('role-p').classList.toggle('border-blue-600', r === 'passenger');
            document.getElementById('role-d').classList.toggle('border-blue-600', r === 'driver');
            document.getElementById('driver-details').classList.toggle('hidden', r !== 'driver');
        }

        function finishSignup() {
            const city = document.getElementById('user-city').value;
            if(!city || !selectedRegRole) return alert("Select your city and role sweetie!");
            
            let data = { role: selectedRegRole, city: city, name: auth.currentUser.displayName };
            if(selectedRegRole === 'driver') {
                data.plate = document.getElementById('d-plate').value;
                data.phone = document.getElementById('d-phone').value;
                if(!data.plate || !data.phone) return alert("Fill driver info sweetie!");
            }
            db.ref('users/' + auth.currentUser.uid).set(data).then(() => location.reload());
        }

        // --- APP LAUNCH ---
        function launchApp(data) {
            showScreen('main-app');
            document.getElementById('ui-name').innerText = data.name;
            document.getElementById('ui-city-tag').innerText = data.city;
            
            if(myRole === 'passenger') {
                document.getElementById('p-view').classList.remove('hidden');
                document.getElementById('btn-city').innerText = data.city;
                initMap();
            } else {
                document.getElementById('d-view').classList.remove('hidden');
                document.getElementById('feed-city').innerText = data.city;
                syncCityFeed(data.city);
            }
            monitorTripStatus();
        }

        // --- PASSENGER ACTIONS ---
        function postRideRequest() {
            const f = document.getElementById('p-from').value;
            const t = document.getElementById('p-to').value;
            if(!f || !t) return alert("Enter route!");

            const reqRef = db.ref('rides').push();
            activeRideId = reqRef.key;
            reqRef.set({
                f, t, fare: document.getElementById('p-fare').value,
                pUid: auth.currentUser.uid, pName: auth.currentUser.displayName,
                city: myCity, status: 'searching'
            });

            document.getElementById('req-btn').innerText = "Finding Drivers...";
            document.getElementById('req-btn').disabled = true;
            listenForDriverBids(activeRideId);
        }

        function listenForDriverBids(rideId) {
            document.getElementById('bids-section').classList.remove('hidden');
            db.ref('rides/' + rideId + '/bids').on('value', snap => {
                const list = document.getElementById('bids-list'); list.innerHTML = "";
                snap.forEach(c => {
                    const b = c.val();
                    list.innerHTML += `<div class="glass-card p-5 flex justify-between items-center animate-pulse">
                        <div><b class="text-sm">${b.dName}</b><p class="text-green-600 font-black text-xs">Offers PKR ${b.offer}</p></div>
                        <button onclick="acceptOffer('${c.key}', ${b.offer})" class="bg-blue-600 text-white px-5 py-2 rounded-xl text-[10px] font-black">ACCEPT</button>
                    </div>`;
                });
            });
        }

        function acceptOffer(bidId, finalFare) {
            db.ref('rides/' + activeRideId + '/bids/' + bidId).once('value', snap => {
                const b = snap.val();
                db.ref('rides/' + activeRideId).update({
                    status: 'accepted', fare: finalFare,
                    dUid: b.dUid, dName: b.dName, dPlate: b.dPlate, dPhone: b.dPhone
                });
            });
        }

        // --- DRIVER ACTIONS ---
        function syncCityFeed(city) {
            db.ref('rides').orderByChild('city').equalTo(city).on('value', snap => {
                const feed = document.getElementById('ride-feed'); feed.innerHTML = "";
                snap.forEach(c => {
                    const r = c.val();
                    if(r.status === 'searching') {
                        feed.innerHTML += `<div class="glass-card p-6 border-l-8 border-blue-600">
                            <div class="flex justify-between items-start mb-4">
                                <span><b class="text-sm block uppercase">${r.f} ➔ ${r.t}</b><small class="text-slate-400 font-bold uppercase text-[9px]">${r.pName}</small></span>
                                <span class="text-green-600 font-black">Rs ${r.fare}</span>
                            </div>
                            <div class="flex gap-2">
                                <button onclick="sendBid('${c.key}', ${r.fare})" class="flex-1 bg-slate-900 text-white py-3 rounded-xl font-black text-[9px]">ACCEPT</button>
                                <button onclick="sendBid('${c.key}', ${parseInt(r.fare)+50})" class="flex-1 border-2 border-slate-900 py-3 rounded-xl font-black text-[9px]">+50 PKR</button>
                            </div>
                        </div>`;
                    }
                });
            });
        }

        function sendBid(rideId, offer) {
            db.ref('users/' + auth.currentUser.uid).once('value', snap => {
                const d = snap.val();
                db.ref('rides/' + rideId + '/bids').push({
                    dUid: auth.currentUser.uid, dName: d.name, dPlate: d.plate, dPhone: d.phone, offer: offer
                });
                alert("Offer sent sweetie! Wait for passenger.");
            });
        }

        // --- GLOBAL TRIP MONITORING ---
        function monitorTripStatus() {
            db.ref('rides').on('value', snap => {
                snap.forEach(c => {
                    const r = c.val(); const me = auth.currentUser.uid;
                    if(r.status === 'accepted' && (r.pUid === me || r.dUid === me)) {
                        activeRideId = c.key;
                        showScreen('main-app');
                        document.getElementById('p-view').classList.add('hidden');
                        document.getElementById('d-view').classList.add('hidden');
                        document.getElementById('active-trip').classList.remove('hidden');
                        
                        const info = document.getElementById('trip-info');
                        info.innerHTML = myRole === 'passenger' ? 
                            `DRIVER: ${r.dName}<br>PLATE: ${r.dPlate}<br>WHATSAPP: ${r.dPhone}<br>FARE: PKR ${r.fare}` :
                            `PASSENGER: ${r.pName}<br>ROUTE: ${r.f} to ${r.t}<br>FINAL FARE: PKR ${r.fare}`;
                        syncChat();
                    }
                });
            });
        }

        function syncChat() {
            db.ref('rides/' + activeRideId + '/chat').on('value', snap => {
                const box = document.getElementById('chat-messages'); box.innerHTML = "";
                snap.forEach(c => {
                    const d = c.val(); const isMe = d.uid === auth.currentUser.uid;
                    box.innerHTML += `<div class="chat-bubble ${isMe ? 'bg-blue-600 text-white self-end rounded-tr-none' : 'bg-white border self-start rounded-tl-none shadow-sm'}">${d.m}</div>`;
                });
                box.scrollTop = box.scrollHeight;
            });
        }

        function sendChatMessage() {
            const inp = document.getElementById('chat-input'); if(!inp.value) return;
            db.ref('rides/' + activeRideId + '/chat').push({ m: inp.value, uid: auth.currentUser.uid });
            inp.value = "";
        }

        // --- HELPERS ---
        function showScreen(id) {
            ['login-screen', 'main-app', 'signup-profile'].forEach(s => document.getElementById(s).classList.add('hidden'));
            document.getElementById(id).classList.remove('hidden');
        }
        function initMap() {
            if(map) return;
            map = L.map('map', {zoomControl: false}).setView([35.9208, 74.3089], 14);
            L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png').addTo(map);
            L.marker([35.9208, 74.3089]).addTo(map);
        }
        function loginWithGoogle() { auth.signInWithPopup(new firebase.auth.GoogleAuthProvider()); }
        function handleLogout() { auth.signOut().then(() => location.reload()); }
        function finishRide() { if(confirm("End trip sweetie?")) { db.ref('rides/' + activeRideId).remove(); location.reload(); } }
        function triggerSos() { alert("🚨 SOS: Your live GPS and Trip ID sent to GB Drive Security Team!"); }
    </script>
</body>
</html>
