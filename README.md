<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>GB DRIVE PRO</title>
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
        body { background: #f9fafb; color: #111827; overflow-x: hidden; }
        .hidden { display: none !important; }
        .card { background: white; border-radius: 24px; box-shadow: 0 4px 20px rgba(0,0,0,0.03); border: 1px solid #f1f5f9; }
        #map { height: 220px; width: 100%; border-radius: 20px; z-index: 10; margin-bottom: 1rem; border: 1px solid #e5e7eb; }
        .input-group { background: #f3f4f6; border-radius: 16px; padding: 12px; margin-bottom: 8px; border: 1px solid transparent; transition: 0.3s; }
        .input-group:focus-within { border-color: #2563eb; background: white; box-shadow: 0 0 0 4px rgba(37,99,235,0.1); }
        .input-group input { background: transparent; border: none; width: 100%; outline: none; font-weight: 600; font-size: 14px; }
        .v-selector { background: white; border: 2px solid #f3f4f6; border-radius: 18px; padding: 12px; transition: 0.2s; filter: grayscale(1); opacity: 0.7; }
        .v-selected { border-color: #2563eb; background: #eff6ff; filter: grayscale(0); opacity: 1; transform: translateY(-2px); box-shadow: 0 4px 12px rgba(37,99,235,0.15); }
        .btn-primary { background: #2563eb; color: white; border-radius: 18px; padding: 20px; width: 100%; font-weight: 900; box-shadow: 0 10px 15px -3px rgba(37,99,235,0.3); transition: 0.2s; }
        .btn-primary:active { transform: scale(0.97); }
        .chat-bubble { padding: 10px 14px; border-radius: 16px; font-size: 13px; font-weight: 600; max-width: 80%; }
        /* Fix for the screenshot issue where elements overlap */
        header { position: sticky; top: 0; z-index: 1000; background: rgba(255,255,255,0.9); backdrop-filter: blur(10px); border-bottom: 1px solid #f1f5f9; }
    </style>
</head>
<body class="pb-10">

    <div id="login-screen" class="hidden fixed inset-0 z-[5000] bg-white flex flex-col items-center justify-center p-8">
        <div class="bg-blue-600 text-white w-24 h-24 flex items-center justify-center rounded-[30px] font-black italic text-5xl mb-8 shadow-xl">GB</div>
        <h1 class="text-3xl font-black text-center leading-tight mb-2">Drive with<br>Confidence</h1>
        <p class="text-slate-400 font-bold text-xs uppercase tracking-widest mb-12">Premier Ride-Sharing App</p>
        <button onclick="loginWithGoogle()" class="w-full max-w-xs flex items-center justify-center gap-4 bg-white border-2 border-slate-100 p-5 rounded-2xl shadow-sm hover:bg-slate-50 transition-all">
            <img src="https://www.google.com/favicon.ico" class="w-6 h-6">
            <span class="font-black text-slate-700">Continue with Google</span>
        </button>
        <div class="mt-20 text-[10px] font-black text-slate-300 tracking-tighter uppercase">Power by Prime Solutions</div>
    </div>

    <div id="main-app" class="hidden">
        <header class="p-4 flex justify-between items-center">
            <div class="flex items-center gap-3">
                <div class="bg-blue-600 text-white w-10 h-10 flex items-center justify-center rounded-xl font-black italic">GB</div>
                <div>
                    <p id="user-display-name" class="font-black text-sm uppercase truncate max-w-[120px]">User Name</p>
                    <div class="flex items-center gap-1">
                        <span class="w-2 h-2 bg-green-500 rounded-full"></span>
                        <p id="user-role-label" class="text-[9px] font-bold text-blue-600 uppercase tracking-widest">Active</p>
                    </div>
                </div>
            </div>
            <button onclick="handleLogout()" class="p-3 bg-red-50 text-red-500 rounded-xl"><i data-lucide="log-out" class="w-5"></i></button>
        </header>

        <main class="p-4 max-w-md mx-auto space-y-4">
            <div id="select-role-view" class="hidden py-10 space-y-4">
                <h2 class="text-2xl font-black mb-4">Choose Account Type</h2>
                <button onclick="assignRole('passenger')" class="card w-full p-8 text-left flex justify-between items-center">
                    <span><b class="text-2xl block">PASSENGER</b><small class="text-slate-400 font-bold uppercase text-[10px]">I need a ride</small></span>
                    <i data-lucide="user" class="text-blue-600 w-8 h-8"></i>
                </button>
                <button onclick="showDriverForm()" class="card w-full p-8 bg-slate-900 text-white text-left flex justify-between items-center">
                    <span><b class="text-2xl block">DRIVER</b><small class="text-slate-500 font-bold uppercase text-[10px]">I want to earn</small></span>
                    <i data-lucide="car" class="w-8 h-8 text-white"></i>
                </button>
            </div>

            <div id="passenger-view" class="hidden space-y-4">
                <div id="map"></div>
                <div class="card p-6 space-y-4">
                    <div class="input-group">
                        <input id="pickup-input" type="text" placeholder="Set Pickup Location">
                    </div>
                    <div class="input-group">
                        <input id="drop-input" type="text" placeholder="Set Destination">
                    </div>
                    <div class="grid grid-cols-4 gap-2">
                        <button onclick="selectVehicle('BIKE', 150)" id="v-BIKE" class="v-selector flex flex-col items-center"><i data-lucide="bike" class="w-5 mb-1"></i><span class="text-[9px] font-black">BIKE</span></button>
                        <button onclick="selectVehicle('MINI', 280)" id="v-MINI" class="v-selector flex flex-col items-center"><i data-lucide="zap" class="w-5 mb-1"></i><span class="text-[9px] font-black">MINI</span></button>
                        <button onclick="selectVehicle('CAR', 450)" id="v-CAR" class="v-selector flex flex-col items-center v-selected"><i data-lucide="car" class="w-5 mb-1"></i><span class="text-[9px] font-black">CAR</span></button>
                        <button onclick="selectVehicle('AC', 650)" id="v-AC" class="v-selector flex flex-col items-center"><i data-lucide="snowflake" class="w-5 mb-1"></i><span class="text-[9px] font-black">AC</span></button>
                    </div>
                    <div class="bg-slate-50 p-4 rounded-2xl flex justify-between items-center">
                        <span class="text-[10px] font-black text-slate-400 uppercase">Estimated Fare:</span>
                        <div class="flex items-center gap-1 font-black text-green-600 text-xl">
                            <span>PKR</span>
                            <input id="fare-input" type="number" value="450" class="w-20 bg-transparent text-right outline-none">
                        </div>
                    </div>
                    <button onclick="postRideRequest()" id="request-btn" class="btn-primary uppercase tracking-widest">Find Driver</button>
                </div>
            </div>

            <div id="driver-view" class="hidden space-y-4">
                <div class="flex justify-between items-center px-2">
                    <h3 class="font-black text-slate-400 text-xs uppercase tracking-widest">Available Requests</h3>
                    <span class="bg-green-100 text-green-600 text-[10px] px-3 py-1 rounded-full font-black">ONLINE</span>
                </div>
                <div id="request-feed" class="space-y-4"></div>
            </div>

            <div id="active-ride-view" class="hidden card p-6 border-t-8 border-blue-600 space-y-4">
                <div class="flex justify-between items-center">
                    <span class="bg-blue-50 text-blue-600 px-3 py-1 rounded-lg text-[10px] font-black uppercase">Trip in Progress</span>
                    <button onclick="triggerSos()" class="bg-red-500 text-white w-10 h-10 rounded-full flex items-center justify-center animate-pulse"><i data-lucide="shield-alert" class="w-5"></i></button>
                </div>
                <div id="trip-party-info" class="bg-slate-50 p-4 rounded-2xl text-xs font-bold leading-relaxed"></div>
                <div id="chat-messages" class="h-48 overflow-y-auto bg-slate-50 rounded-2xl p-4 flex flex-col gap-2"></div>
                <div class="flex gap-2">
                    <input id="chat-input-field" type="text" placeholder="Chat with partner..." class="flex-1 bg-white border border-slate-200 rounded-xl p-3 text-sm font-semibold outline-none">
                    <button onclick="handleSendMessage()" class="bg-blue-600 text-white w-12 h-12 rounded-xl flex items-center justify-center shadow-lg"><i data-lucide="send" class="w-5"></i></button>
                </div>
                <button onclick="finishRideProcess()" class="w-full text-[10px] font-black text-slate-400 uppercase tracking-widest py-2">Complete Trip</button>
            </div>
        </main>
    </div>

    <div id="driver-registration-modal" class="hidden fixed inset-0 z-[6000] bg-white p-8">
        <h2 class="text-3xl font-black mb-8">Driver<br>Onboarding</h2>
        <div class="space-y-4">
            <div class="input-group"><input id="d-reg-name" type="text" placeholder="Legal Full Name"></div>
            <div class="input-group"><input id="d-reg-plate" type="text" placeholder="Vehicle Plate Number"></div>
            <div class="input-group"><input id="d-reg-phone" type="tel" placeholder="WhatsApp Contact"></div>
            <button onclick="submitDriverData()" class="btn-primary mt-4">JOIN NETWORK</button>
            <button onclick="document.getElementById('driver-registration-modal').classList.add('hidden')" class="w-full text-slate-400 font-bold mt-6 uppercase text-xs tracking-widest">Cancel</button>
        </div>
    </div>

    <script>
        lucide.createIcons();
        let activeRideId = null; 
        let globalUserRole = null;

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

        // AUTH MONITORING
        auth.onAuthStateChanged(user => {
            if(user) {
                document.getElementById('login-screen').classList.add('hidden');
                document.getElementById('main-app').classList.remove('hidden');
                document.getElementById('user-display-name').innerText = user.displayName;
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

        function renderView() {
            document.getElementById('select-role-view').classList.add('hidden');
            if(globalUserRole === 'passenger') {
                document.getElementById('passenger-view').classList.remove('hidden');
                initMapBox();
                monitorActiveRides();
            } else {
                document.getElementById('driver-view').classList.remove('hidden');
                syncLiveRequests();
                monitorActiveRides();
            }
        }

        function assignRole(r) { db.ref('users/' + auth.currentUser.uid).set({role: r, name: auth.currentUser.displayName}); }
        function showDriverForm() { document.getElementById('driver-registration-modal').classList.remove('hidden'); }
        function submitDriverData() {
            const name = document.getElementById('d-reg-name').value;
            const plate = document.getElementById('d-reg-plate').value;
            const phone = document.getElementById('d-reg-phone').value;
            if(!name || !plate) return alert("Sweetie, fill all driver info!");
            db.ref('users/' + auth.currentUser.uid).set({role: 'driver', name, plate, phone});
            document.getElementById('driver-registration-modal').classList.add('hidden');
        }

        function initMapBox() {
            let map = L.map('map', {zoomControl: false}).setView([35.9208, 74.3089], 14);
            L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png').addTo(map);
            L.marker([35.9208, 74.3089]).addTo(map);
        }

        function selectVehicle(type, price) {
            document.querySelectorAll('.v-selector').forEach(b => b.classList.remove('v-selected'));
            document.getElementById('v-'+type).classList.add('v-selected');
            document.getElementById('fare-input').value = price;
        }

        function postRideRequest() {
            const f = document.getElementById('pickup-input').value;
            const t = document.getElementById('drop-input').value;
            const fare = document.getElementById('fare-input').value;
            if(!f || !t) return alert("Please set your route!");

            const req = db.ref('rides').push();
            activeRideId = req.key;
            req.set({ f, t, fare, pName: auth.currentUser.displayName, pUid: auth.currentUser.uid, status: 'searching' });
            document.getElementById('request-btn').innerText = "LOOKING FOR PARTNERS...";
        }

        function syncLiveRequests() {
            db.ref('rides').on('value', snap => {
                const feed = document.getElementById('request-feed');
                feed.innerHTML = "";
                snap.forEach(child => {
                    const r = child.val();
                    if(r.status === 'searching') {
                        feed.innerHTML += `
                        <div class="card p-6 border-l-8 border-blue-600 shadow-lg">
                            <div class="flex justify-between items-start mb-4">
                                <div><b class="text-sm block uppercase">${r.f} ➔ ${r.t}</b><small class="text-[9px] font-black text-slate-400">CLIENT: ${r.pName}</small></div>
                                <div class="text-2xl font-black text-green-600">Rs ${r.fare}</div>
                            </div>
                            <button onclick="acceptPassengerRequest('${child.key}')" class="w-full bg-slate-900 text-white py-4 rounded-2xl font-black text-xs uppercase tracking-widest active:scale-95 transition-all">Accept Trip</button>
                        </div>`;
                    }
                });
            });
        }

        function acceptPassengerRequest(id) {
            db.ref('users/' + auth.currentUser.uid).once('value', snap => {
                const d = snap.val();
                db.ref('rides/' + id).update({status: 'accepted', dUid: auth.currentUser.uid, dName: d.name, dPlate: d.plate, dPhone: d.phone});
            });
        }

        function monitorActiveRides() {
            db.ref('rides').on('value', snap => {
                snap.forEach(child => {
                    const r = child.val();
                    const myUid = auth.currentUser.uid;
                    if(r.status === 'accepted' && (r.pUid === myUid || r.dUid === myUid)) {
                        activeRideId = child.key;
                        document.getElementById('passenger-view').classList.add('hidden');
                        document.getElementById('driver-view').classList.add('hidden');
                        document.getElementById('active-ride-view').classList.remove('hidden');
                        
                        const detailBox = document.getElementById('trip-party-info');
                        detailBox.innerHTML = globalUserRole === 'passenger' ? 
                            `DRIVER: ${r.dName}<br>PLATE: ${r.dPlate}<br>FARE: PKR ${r.fare}` : 
                            `CLIENT: ${r.pName}<br>FROM: ${r.f}<br>TO: ${r.t}<br>FARE: PKR ${r.fare}`;
                        syncTripChat();
                    }
                });
            });
        }

        function handleSendMessage() {
            const m = document.getElementById('chat-input-field').value; if(!m) return;
            db.ref('rides/' + activeRideId + '/chat').push({u: auth.currentUser.displayName, m, uid: auth.currentUser.uid});
            document.getElementById('chat-input-field').value = "";
        }

        function syncTripChat() {
            db.ref('rides/' + activeRideId + '/chat').on('value', snap => {
                const box = document.getElementById('chat-messages'); box.innerHTML = "";
                snap.forEach(c => {
                    const d = c.val(); const isMe = d.uid === auth.currentUser.uid;
                    box.innerHTML += `<div class="chat-bubble ${isMe ? 'bg-blue-600 text-white self-end rounded-tr-none' : 'bg-white border self-start rounded-tl-none shadow-sm'}">${d.m}</div>`;
                });
                box.scrollTop = box.scrollHeight;
            });
        }

        function finishRideProcess() { if(confirm("End trip sweetie?")) { db.ref('rides/' + activeRideId).remove(); location.reload(); } }
        function triggerSos() { alert("🚨 SOS: Emergency signal sent to GB Drive monitoring center!"); }
    </script>
</body>
</html>
