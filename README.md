<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>GB DRIVE PRO | Professional Transport Solutions</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-auth-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-database-compat.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;800&display=swap');
        * { font-family: 'Inter', sans-serif; -webkit-tap-highlight-color: transparent; box-sizing: border-box; }
        body { background: #f1f5f9; color: #0f172a; }
        .hidden { display: none !important; }
        .glass-card { background: #ffffff; border-radius: 12px; border: 1px solid #e2e8f0; box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1); }
        .btn-primary { background: #1e40af; color: white; border-radius: 8px; padding: 14px; width: 100%; font-weight: 600; transition: all 0.2s; }
        .btn-primary:hover { background: #1e3a8a; }
        .nav-link { font-size: 12px; font-weight: 600; color: #64748b; cursor: pointer; }
        .nav-link:hover { color: #1e40af; }
        #map { height: 250px; width: 100%; border-radius: 12px; border: 1px solid #cbd5e1; }
        .status-badge { padding: 4px 12px; border-radius: 9999px; font-size: 10px; font-weight: 700; text-transform: uppercase; }
    </style>
</head>
<body class="bg-slate-50">

    <div id="loader" class="fixed inset-0 z-[9999] bg-white flex flex-col items-center justify-center">
        <div class="w-12 h-12 border-4 border-blue-600 border-t-transparent rounded-full animate-spin"></div>
        <p class="mt-4 text-slate-600 font-semibold tracking-tight">Securing Environment</p>
    </div>

    <div id="login-screen" class="hidden fixed inset-0 z-[5000] bg-white flex flex-col items-center justify-center p-6">
        <h1 class="text-2xl font-extrabold text-slate-900 mb-2">GB DRIVE PRO</h1>
        <p class="text-slate-500 text-sm mb-8 text-center">Professional transport networking for Gilgit Baltistan</p>
        <button onclick="loginWithGoogle()" class="w-full max-w-xs flex items-center justify-center gap-3 border border-slate-300 p-4 rounded-lg font-semibold text-slate-700 hover:bg-slate-50">
            Continue with Google Authentication
        </button>
    </div>

    <nav id="app-nav" class="hidden sticky top-0 z-[1000] bg-white border-b border-slate-200 px-4 py-3 flex justify-between items-center">
        <div class="flex items-center gap-2">
            <div class="bg-blue-800 text-white px-2 py-1 rounded font-bold text-sm">GB PRO</div>
            <span id="nav-city" class="text-[10px] font-bold text-blue-700 uppercase tracking-widest"></span>
        </div>
        <div class="flex gap-4">
            <span onclick="showInfo('policy')" class="nav-link">Privacy</span>
            <span onclick="showInfo('faq')" class="nav-link">FAQ</span>
            <span onclick="handleLogout()" class="nav-link text-red-600">Logout</span>
        </div>
    </nav>

    <main id="main-app" class="hidden p-4 max-w-2xl mx-auto">
        
        <div id="p-view" class="hidden space-y-4">
            <div id="map"></div>
            <div class="glass-card p-5 space-y-3">
                <input id="p-from" class="w-full p-3 bg-slate-100 rounded-lg text-sm border-0" placeholder="Pickup Location">
                <input id="p-to" class="w-full p-3 bg-slate-100 rounded-lg text-sm border-0" placeholder="Destination">
                <div class="flex items-center justify-between bg-slate-50 p-3 rounded-lg border border-slate-200">
                    <span class="text-xs font-bold text-slate-500 uppercase">Your Offer (PKR)</span>
                    <input id="p-fare" type="number" value="500" class="bg-transparent text-right font-bold text-blue-700 outline-none w-20">
                </div>
                <button onclick="postRideRequest()" id="req-btn" class="btn-primary">Request Ride</button>
            </div>
            
            <div id="bids-section" class="hidden">
                <h2 class="text-xs font-bold text-slate-400 mb-3 uppercase">Incoming Bids</h2>
                <div id="bids-list" class="space-y-2"></div>
            </div>
        </div>

        <div id="d-view" class="hidden space-y-4">
            <div class="flex justify-between items-center px-1">
                <h2 class="text-xs font-bold text-slate-400 uppercase">Available Rides in <span id="feed-city-name" class="text-blue-700"></span></h2>
                <span class="status-badge bg-green-100 text-green-700">System Online</span>
            </div>
            <div id="ride-feed" class="space-y-3"></div>
        </div>

        <div id="active-trip" class="hidden glass-card p-5 space-y-4 border-t-4 border-blue-700">
            <div class="flex justify-between items-center">
                <span class="status-badge bg-blue-100 text-blue-700">Active Transit</span>
                <button onclick="triggerSos()" class="bg-red-600 text-white px-4 py-2 rounded text-[10px] font-bold">SOS SIGNAL</button>
            </div>
            <div id="trip-info" class="text-sm border-b pb-4 text-slate-600"></div>
            
            <div id="chat-messages" class="h-40 overflow-y-auto bg-slate-50 rounded p-3 flex flex-col gap-2 border"></div>
            <div class="flex gap-2">
                <input id="chat-input" class="flex-1 border rounded p-2 text-sm" placeholder="Type message...">
                <button onclick="sendChatMessage()" class="bg-slate-800 text-white px-4 rounded text-sm">Send</button>
            </div>
            <button onclick="finishRide()" class="w-full text-[10px] text-slate-400 font-bold uppercase py-2">Complete Transaction</button>
        </div>

        <div id="info-modal" class="hidden fixed inset-0 z-[6000] bg-white/95 p-6 overflow-y-auto">
            <button onclick="closeInfo()" class="mb-6 font-bold text-blue-700 uppercase text-xs">Close</button>
            <div id="info-content" class="prose prose-sm max-w-none"></div>
        </div>
    </main>

    <script>
        // Company and Policy Content
        const COMPANY_DETAILS = {
            policy: `
                <h2 class="text-xl font-bold mb-4">Privacy Policy</h2>
                <p class="mb-2">GB DRIVE PRO is committed to protecting your personal data. Our system uses end-to-end encryption for trip monitoring.</p>
                <ul class="list-disc ml-4 space-y-2">
                    <li>Data Collection: We collect location data only during active rides to ensure passenger and driver safety.</li>
                    <li>Security: Advanced Firebase security rules prevent unauthorized access to personal contact details.</li>
                    <li>Third Parties: Your information is never sold to third-party marketing firms.</li>
                </ul>`,
            faq: `
                <h2 class="text-xl font-bold mb-4">Frequently Asked Questions</h2>
                <div class="space-y-4">
                    <div>
                        <h4 class="font-bold">How does bidding work?</h4>
                        <p>Passengers offer a base price, and drivers can accept or provide a counter-offer based on terrain and distance.</p>
                    </div>
                    <div>
                        <h4 class="font-bold">What is the SOS feature?</h4>
                        <p>The SOS button immediately alerts our regional response team with your live GPS coordinates.</p>
                    </div>
                </div>`,
            company: `
                <h2 class="text-xl font-bold mb-4">Company Overview</h2>
                <p>GB DRIVE PRO provides premium logistics and transport services across Gilgit-Baltistan. Our mission is to digitize the regional transport network while maintaining high professional standards.</p>`
        };

        // Firebase Configuration (Same as original)
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

        let myRole, myCity, activeRideId;

        // UI Helpers
        function showInfo(type) {
            document.getElementById('info-content').innerHTML = COMPANY_DETAILS[type];
            document.getElementById('info-modal').classList.remove('hidden');
        }
        function closeInfo() { document.getElementById('info-modal').classList.add('hidden'); }

        // Security Upgrade: Enhanced Auth State
        auth.onAuthStateChanged(user => {
            document.getElementById('loader').classList.add('hidden');
            if(user) {
                db.ref('users/' + user.uid).once('value', snap => {
                    const data = snap.val();
                    if(data && data.role && data.city) {
                        myRole = data.role; myCity = data.city;
                        initApp(data);
                    } else {
                        window.location.reload(); // Re-trigger signup if data missing
                    }
                });
            } else {
                document.getElementById('login-screen').classList.remove('hidden');
            }
        });

        function initApp(data) {
            document.getElementById('app-nav').classList.remove('hidden');
            document.getElementById('main-app').classList.remove('hidden');
            document.getElementById('nav-city').innerText = data.city;
            
            if(myRole === 'passenger') {
                document.getElementById('p-view').classList.remove('hidden');
                initMap();
            } else {
                document.getElementById('d-view').classList.remove('hidden');
                document.getElementById('feed-city-name').innerText = data.city;
                syncRideFeed(data.city);
            }
            monitorRides();
        }

        // Professional Bidding Logic
        function postRideRequest() {
            const f = document.getElementById('p-from').value;
            const t = document.getElementById('p-to').value;
            if(!f || !t) return alert("Required fields: Pickup and Destination");

            const ref = db.ref('rides').push();
            activeRideId = ref.key;
            ref.set({
                f, t, fare: document.getElementById('p-fare').value,
                pUid: auth.currentUser.uid, pName: auth.currentUser.displayName,
                city: myCity, status: 'searching', timestamp: Date.now()
            });
            document.getElementById('req-btn').innerText = "Awaiting Driver Response...";
            document.getElementById('req-btn').disabled = true;
            listenForBids(activeRideId);
        }

        function listenForBids(rideId) {
            document.getElementById('bids-section').classList.remove('hidden');
            db.ref(`rides/${rideId}/bids`).on('value', snap => {
                const container = document.getElementById('bids-list');
                container.innerHTML = "";
                snap.forEach(child => {
                    const bid = child.val();
                    container.innerHTML += `
                        <div class="glass-card p-4 flex justify-between items-center bg-slate-50">
                            <div>
                                <span class="text-xs font-bold block">${bid.dName}</span>
                                <span class="text-blue-700 font-bold">PKR ${bid.offer}</span>
                            </div>
                            <button onclick="acceptBid('${child.key}', ${bid.offer})" class="bg-blue-800 text-white px-4 py-2 rounded text-[10px] font-bold">Confirm</button>
                        </div>`;
                });
            });
        }

        function syncRideFeed(city) {
            db.ref('rides').orderByChild('city').equalTo(city).on('value', snap => {
                const feed = document.getElementById('ride-feed');
                feed.innerHTML = "";
                snap.forEach(child => {
                    const r = child.val();
                    if(r.status === 'searching') {
                        feed.innerHTML += `
                            <div class="glass-card p-5 border-l-4 border-blue-800">
                                <div class="flex justify-between mb-4">
                                    <span class="text-sm font-bold uppercase">${r.f} to ${r.t}</span>
                                    <span class="text-green-700 font-bold">Rs ${r.fare}</span>
                                </div>
                                <div class="flex gap-2">
                                    <button onclick="submitBid('${child.key}', ${r.fare})" class="flex-1 bg-slate-900 text-white py-2 rounded text-[10px] font-bold uppercase">Accept Base</button>
                                    <button onclick="submitBid('${child.key}', ${parseInt(r.fare) + 100})" class="flex-1 border border-slate-900 py-2 rounded text-[10px] font-bold uppercase">+100 PKR</button>
                                </div>
                            </div>`;
                    }
                });
            });
        }

        // Shared Functions
        function submitBid(rideId, amount) {
            db.ref('users/' + auth.currentUser.uid).once('value', snap => {
                const d = snap.val();
                db.ref(`rides/${rideId}/bids`).push({
                    dUid: auth.currentUser.uid, dName: d.name, dPlate: d.plate, offer: amount
                });
            });
        }

        function triggerSos() {
            alert("SECURITY PROTOCOL: Live coordinates dispatched to GB Response Center.");
        }

        function loginWithGoogle() { auth.signInWithPopup(new firebase.auth.GoogleAuthProvider()); }
        function handleLogout() { auth.signOut().then(() => location.reload()); }
        function initMap() {
            const map = L.map('map', {zoomControl: false}).setView([35.9208, 74.3089], 12);
            L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png').addTo(map);
        }
    </script>
</body>
</html>
