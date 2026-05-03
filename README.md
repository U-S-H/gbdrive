<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>GB DRIVE PRO | Professional Logistics</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-auth-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-database-compat.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;800&display=swap');
        * { font-family: 'Plus Jakarta Sans', sans-serif; -webkit-tap-highlight-color: transparent; box-sizing: border-box; }
        body { background: #f8fafc; color: #0f172a; }
        .hidden { display: none !important; }
        .glass-card { background: white; border: 1px solid #e2e8f0; border-radius: 16px; box-shadow: 0 4px 12px rgba(0,0,0,0.03); }
        .input-group { background: #f1f5f9; border-radius: 12px; padding: 12px 16px; border: 1px solid transparent; transition: 0.2s; }
        .input-group:focus-within { border-color: #2563eb; background: white; box-shadow: 0 0 0 4px rgba(37,99,235,0.05); }
        .input-group input, .input-group select { background: transparent; border: none; width: 100%; outline: none; font-size: 14px; font-weight: 600; }
        .btn-primary { background: #1e40af; color: white; border-radius: 12px; padding: 16px; width: 100%; font-weight: 800; text-transform: uppercase; letter-spacing: 0.5px; transition: 0.3s; }
        .btn-primary:active { transform: scale(0.97); }
        #map { height: 200px; border-radius: 16px; border: 1px solid #e2e8f0; margin-bottom: 16px; }
        .badge { padding: 4px 10px; border-radius: 6px; font-size: 10px; font-weight: 800; text-transform: uppercase; }
        ::-webkit-scrollbar { display: none; }
    </style>
</head>
<body class="pb-10">

    <div id="loader" class="fixed inset-0 z-[9999] bg-white flex flex-col items-center justify-center">
        <div class="w-12 h-12 border-4 border-blue-600 border-t-transparent rounded-full animate-spin"></div>
        <p class="mt-4 text-xs font-bold text-slate-400 uppercase tracking-widest">Securing Connection</p>
    </div>

    <div id="login-screen" class="hidden fixed inset-0 z-[5000] bg-white flex flex-col items-center justify-center p-8 text-center">
        <div class="bg-blue-800 text-white px-4 py-2 rounded-xl font-black italic text-3xl mb-4">GB</div>
        <h1 class="text-2xl font-extrabold text-slate-900">Professional Transport</h1>
        <p class="text-slate-400 text-xs font-bold mb-10 uppercase tracking-widest">Gilgit Baltistan Network</p>
        <button onclick="loginWithGoogle()" class="w-full max-w-xs flex items-center justify-center gap-3 border-2 border-slate-100 p-4 rounded-xl font-bold text-slate-700 active:bg-slate-50 transition-all">
            <img src="https://www.google.com/favicon.ico" class="w-5 h-5"> Google Authentication
        </button>
    </div>

    <div id="driver-reg" class="hidden fixed inset-0 z-[4500] bg-white p-6 overflow-y-auto">
        <h2 class="text-xl font-extrabold mb-2">Driver Verification</h2>
        <p class="text-[10px] font-bold text-slate-400 uppercase mb-6">Security Compliance Required</p>
        
        <div class="space-y-4">
            <div class="space-y-2">
                <label class="text-[10px] font-bold text-slate-500 uppercase">Live Profile & CNIC Photos</label>
                <div class="grid grid-cols-3 gap-2">
                    <div onclick="triggerCapture('live')" id="pre-live" class="h-24 bg-slate-100 rounded-lg flex items-center justify-center text-[8px] font-bold text-center p-2 border-2 border-dashed">PROFILE</div>
                    <div onclick="triggerCapture('front')" id="pre-front" class="h-24 bg-slate-100 rounded-lg flex items-center justify-center text-[8px] font-bold text-center p-2 border-2 border-dashed">CNIC FRONT</div>
                    <div onclick="triggerCapture('back')" id="pre-back" class="h-24 bg-slate-100 rounded-lg flex items-center justify-center text-[8px] font-bold text-center p-2 border-2 border-dashed">CNIC BACK</div>
                </div>
                <input type="file" id="file-input" class="hidden" accept="image/*">
            </div>
            <div class="input-group"><input id="reg-plate" placeholder="Vehicle Plate Number"></div>
            <div class="input-group"><input id="reg-phone" placeholder="WhatsApp Number"></div>
            <button onclick="saveDriverVerification()" class="btn-primary mt-4">Submit Verification</button>
        </div>
    </div>

    <div id="main-app" class="hidden">
        <header class="p-4 flex justify-between items-center bg-white sticky top-0 z-[1000] border-b">
            <div class="flex items-center gap-3">
                <div class="bg-blue-800 text-white px-2 py-1 rounded font-black italic text-sm">GB</div>
                <div>
                    <p id="ui-name" class="font-extrabold text-[10px] uppercase">User Name</p>
                    <p id="ui-city" class="text-[8px] font-bold text-blue-600 uppercase tracking-widest"></p>
                </div>
            </div>
            <div class="flex gap-2">
                <button onclick="showSection('history')" class="p-2 text-slate-400"><i data-lucide="history" class="w-5"></i></button>
                <button onclick="handleLogout()" class="p-2 text-red-500"><i data-lucide="power" class="w-5"></i></button>
            </div>
        </header>

        <main class="p-4 max-w-md mx-auto space-y-4">
            <div id="p-view" class="hidden space-y-4">
                <div id="map"></div>
                <div class="glass-card p-5 space-y-3">
                    <div class="input-group"><input id="p-from" placeholder="Pickup Location"></div>
                    <div class="input-group"><input id="p-to" placeholder="Destination"></div>
                    <div class="flex items-center justify-between bg-slate-50 p-3 rounded-lg">
                        <span class="text-[10px] font-bold text-slate-400 uppercase">Offer Price (PKR)</span>
                        <input id="p-fare" type="number" value="500" class="w-20 bg-transparent text-right font-extrabold text-blue-700 outline-none">
                    </div>
                    <button onclick="requestRide()" id="req-btn" class="btn-primary">Search Drivers</button>
                </div>
                <div id="bids-section" class="hidden space-y-3">
                    <h3 class="text-[10px] font-bold text-slate-400 uppercase px-1">Driver Counter-Offers</h3>
                    <div id="bids-list" class="space-y-3"></div>
                </div>
            </div>

            <div id="d-view" class="hidden space-y-4">
                <div class="flex justify-between items-center px-1">
                    <h3 class="text-[10px] font-bold text-slate-400 uppercase">Active Requests</h3>
                    <span class="badge bg-green-100 text-green-700">Online</span>
                </div>
                <div id="ride-feed" class="space-y-4"></div>
            </div>

            <div id="history-section" class="hidden space-y-4">
                <h3 class="text-sm font-extrabold">Ride History</h3>
                <div id="history-list" class="space-y-3"></div>
                <button onclick="location.reload()" class="text-[10px] font-bold text-blue-600 uppercase underline">Back to Home</button>
            </div>
        </main>
    </div>

    <script>
        lucide.createIcons();
        let myData = {}; let activeRideId = null; let base64Store = {};

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

        // AUTH & PROFILE LOGIC
        auth.onAuthStateChanged(user => {
            document.getElementById('loader').classList.add('hidden');
            if(user) {
                db.ref('users/' + user.uid).once('value', snap => {
                    myData = snap.val() || {};
                    if(myData.role && myData.city) {
                        if(myData.role === 'driver' && !myData.verified) {
                            showScreen('driver-reg');
                        } else {
                            launchApp();
                        }
                    } else {
                        // Redirect to initial setup if missing
                        location.href = "initial_setup_file.html"; 
                    }
                });
            } else { showScreen('login-screen'); }
        });

        // DRIVER VERIFICATION
        let currentTarget = '';
        function triggerCapture(type) {
            currentTarget = type;
            document.getElementById('file-input').click();
        }

        document.getElementById('file-input').onchange = (e) => {
            const file = e.target.files[0];
            const reader = new FileReader();
            reader.onloadend = () => {
                base64Store[currentTarget] = reader.result;
                document.getElementById('pre-' + currentTarget).style.background = `url(${reader.result}) center/cover`;
                document.getElementById('pre-' + currentTarget).innerText = "";
            };
            if(file) reader.readAsDataURL(file);
        };

        function saveDriverVerification() {
            const plate = document.getElementById('reg-plate').value;
            const phone = document.getElementById('reg-phone').value;
            if(!plate || !phone || !base64Store.live) return alert("All documents required");
            
            db.ref('users/' + auth.currentUser.uid).update({
                plate, phone, verified: true, docs: base64Store
            }).then(() => location.reload());
        }

        // APP CORE
        function launchApp() {
            showScreen('main-app');
            document.getElementById('ui-name').innerText = myData.name;
            document.getElementById('ui-city').innerText = myData.city;
            
            if(myData.role === 'passenger') {
                document.getElementById('p-view').classList.remove('hidden');
                initMap();
            } else {
                document.getElementById('d-view').classList.remove('hidden');
                syncFeed();
            }
        }

        // PASSENGER ACTIONS
        function requestRide() {
            const f = document.getElementById('p-from').value;
            const t = document.getElementById('p-to').value;
            if(!f || !t) return alert("Enter route");

            const ref = db.ref('rides').push();
            activeRideId = ref.key;
            ref.set({
                f, t, fare: document.getElementById('p-fare').value,
                pUid: auth.currentUser.uid, pName: auth.currentUser.displayName,
                city: myData.city, status: 'searching', time: Date.now()
            });
            document.getElementById('req-btn').innerText = "Finding Drivers...";
            listenForBids();
        }

        function listenForBids() {
            document.getElementById('bids-section').classList.remove('hidden');
            db.ref(`rides/${activeRideId}/bids`).on('value', snap => {
                const list = document.getElementById('bids-list'); list.innerHTML = "";
                snap.forEach(c => {
                    const b = c.val();
                    list.innerHTML += `
                        <div class="glass-card p-4 flex justify-between items-center border-l-4 border-blue-600">
                            <div>
                                <b class="text-xs uppercase block">${b.dName}</b>
                                <span class="text-blue-700 font-extrabold">PKR ${b.offer}</span>
                            </div>
                            <button onclick="confirmRide('${c.key}', ${b.offer})" class="bg-blue-800 text-white px-4 py-2 rounded-lg text-[10px] font-black">ACCEPT</button>
                        </div>`;
                });
            });
        }

        // DRIVER ACTIONS (DYNAMIC BIDDING)
        function syncFeed() {
            db.ref('rides').orderByChild('city').equalTo(myData.city).on('value', snap => {
                const feed = document.getElementById('ride-feed'); feed.innerHTML = "";
                snap.forEach(c => {
                    const r = c.val();
                    if(r.status === 'searching') {
                        feed.innerHTML += `
                            <div class="glass-card p-5 border-l-4 border-slate-900">
                                <div class="flex justify-between mb-4">
                                    <span class="text-xs font-extrabold uppercase">${r.f} ➔ ${r.t}</span>
                                    <span class="text-green-700 font-black">Rs ${r.fare}</span>
                                </div>
                                <div class="flex gap-2">
                                    <button onclick="submitBid('${c.key}', ${r.fare})" class="flex-1 bg-slate-900 text-white py-3 rounded-lg text-[10px] font-bold">ACCEPT ${r.fare}</button>
                                    <div class="flex-1 flex bg-slate-100 rounded-lg p-1">
                                        <input id="bid-${c.key}" type="number" placeholder="Offer" class="w-full bg-transparent px-2 text-[10px] font-bold outline-none">
                                        <button onclick="submitBid('${c.key}', document.getElementById('bid-${c.key}').value)" class="bg-blue-600 text-white px-2 rounded-md"><i data-lucide="send" class="w-3"></i></button>
                                    </div>
                                </div>
                            </div>`;
                        lucide.createIcons();
                    }
                });
            });
        }

        function submitBid(rideId, amount) {
            if(!amount) return alert("Enter your price offer");
            db.ref(`rides/${rideId}/bids`).push({
                dUid: auth.currentUser.uid, dName: myData.name, dPlate: myData.plate, offer: amount
            });
            alert("Offer sent professionally.");
        }

        // HISTORY LOGIC
        function showSection(type) {
            if(type === 'history') {
                document.getElementById('p-view').classList.add('hidden');
                document.getElementById('d-view').classList.add('hidden');
                document.getElementById('history-section').classList.remove('hidden');
                loadHistory();
            }
        }

        function loadHistory() {
            db.ref('history').orderByChild(myData.role === 'passenger' ? 'pUid' : 'dUid').equalTo(auth.currentUser.uid).once('value', snap => {
                const list = document.getElementById('history-list'); list.innerHTML = "";
                snap.forEach(c => {
                    const h = c.val();
                    list.innerHTML += `
                        <div class="bg-white p-3 rounded-xl border text-[10px] flex justify-between">
                            <span><b>${h.f} to ${h.t}</b><br>${new Date(h.time).toLocaleDateString()}</span>
                            <b class="text-blue-600">PKR ${h.fare}</b>
                        </div>`;
                });
            });
        }

        // HELPERS
        function showScreen(id) {
            ['login-screen', 'main-app', 'driver-reg'].forEach(s => document.getElementById(s).classList.add('hidden'));
            document.getElementById(id).classList.remove('hidden');
        }
        function initMap() {
            const map = L.map('map', {zoomControl: false}).setView([35.9208, 74.3089], 13);
            L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png').addTo(map);
        }
        function loginWithGoogle() { auth.signInWithPopup(new firebase.auth.GoogleAuthProvider()); }
        function handleLogout() { auth.signOut().then(() => location.reload()); }
    </script>
</body>
</html>
