<html lang="EN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>GB DRIVE - MASTER EDITION</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-auth-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-database-compat.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Outfit:wght@400;700;900&display=swap');
        BODY { font-family: 'Outfit', sans-serif; background: #F8FAFC; }
        .HIDDEN { display: none !important; }
        .NEO-CARD { background: white; border-radius: 2rem; box-shadow: 0 15px 35px rgba(0,0,0,0.05); transition: 0.3s; }
        .V-BTN { border: 2px solid transparent; background: #F1F5F9; border-radius: 1.5rem; transition: 0.3s; padding: 1rem; }
        .V-SELECTED { border-color: #2563EB; background: #EFF6FF; color: #2563EB; }
        .CHAT-AREA { height: 250px; overflow-y: auto; background: #F9FAFB; border-radius: 1.5rem; padding: 15px; border: 1px solid #E2E8F0; }
        .MODAL-OVERLAY { background: rgba(0,0,0,0.85); backdrop-filter: blur(8px); }
        INPUT, SELECT { border: 1px solid #E2E8F0 !important; border-radius: 1rem !important; padding: 1rem !important; width: 100%; margin-bottom: 1rem; font-weight: 600; outline: none; }
        INPUT:focus { border-color: #2563EB !important; }
    </style>
</head>
<body class="PB-20">

    <div id="DRIVER-SETUP" class="HIDDEN FIXED INSET-0 Z-[100] MODAL-OVERLAY FLEX ITEMS-CENTER JUSTIFY-CENTER P-6">
        <div class="BG-WHITE W-FULL MAX-W-MD ROUNDED-[3rem] P-8 SPACE-Y-2">
            <h2 class="FONT-BLACK TEXT-2xl TEXT-CENTER MB-4">DRIVER VERIFICATION</h2>
            <p class="TEXT-[10PX] FONT-BOLD TEXT-GRAY-400 TEXT-CENTER MB-4">UPLOAD YOUR REAL DETAILS FOR SECURITY</p>
            <input id="D-REALNAME" type="text" placeholder="FULL LEGAL NAME">
            <input id="D-PHONE" type="tel" placeholder="ACTIVE PHONE NUMBER">
            <input id="D-CNIC" type="text" placeholder="CNIC NUMBER (XXXXX-XXXXXXX-X)">
            <input id="D-VEHICLE" type="text" placeholder="VEHICLE PLATE NO (E.G. ABC-123)">
            <select id="D-VTYPE" class="W-FULL">
                <option value="CAR">CAR</option>
                <option value="BIKE">BIKE</option>
                <option value="AUTO">AUTO</option>
            </select>
            <button onclick="SAVE_DRIVER_PROFILE()" class="W-FULL BG-BLUE-600 TEXT-WHITE PY-5 ROUNDED-2xl FONT-BLACK SHADOW-LG HOVER:BG-BLACK TRANSITION-ALL">SUBMIT PROFILE</button>
        </div>
    </div>

    <header class="P-5 BG-WHITE BORDER-B STICKY TOP-0 Z-50 FLEX JUSTIFY-BETWEEN ITEMS-CENTER">
        <div class="FLEX ITEMS-CENTER GAP-3">
            <div onclick="adminTap()" class="BG-BLUE-600 TEXT-WHITE W-10 H-10 FLEX ITEMS-CENTER JUSTIFY-CENTER ROUNDED-2xl FONT-BLACK ITALIC CURSOR-POINTER SHADOW-MD">GB</div>
            <div>
                <p id="DISPLAY-NAME" class="FONT-BLACK TEXT-sm text-blue-900"></p>
                <p id="USER-ROLE-TXT" class="TEXT-[8PX] FONT-BOLD OPACITY-50 uppercase"></p>
            </div>
        </div>
        <button onclick="logout()" class="TEXT-RED-500 BG-RED-50 P-3 ROUNDED-2xl"><i data-lucide="log-out" class="W-5"></i></button>
    </header>

    <main class="P-5 MAX-W-MD MX-AUTO SPACE-Y-6">
        
        <div id="ROLE-SCREEN" class="HIDDEN PY-20 SPACE-Y-6">
            <h1 class="TEXT-3xl FONT-BLACK TEXT-CENTER TEXT-BLUE-900">WELCOME TO GB DRIVE</h1>
            <button onclick="setRole('passenger')" class="W-FULL P-8 NEO-CARD FONT-BLACK TEXT-2xl HOVER:BORDER-BLUE-600 BORDER-2 BORDER-TRANSPARENT">I'M A PASSENGER 🙋‍♂️</button>
            <button onclick="document.getElementById('DRIVER-SETUP').classList.remove('HIDDEN')" class="W-FULL P-8 NEO-CARD FONT-BLACK TEXT-2xl BORDER-2 BORDER-BLACK">I'M A DRIVER 🚗</button>
        </div>

        <div id="P-UI" class="HIDDEN SPACE-Y-6">
            <div class="NEO-CARD P-6">
                <div class="SPACE-Y-1 MB-4">
                    <label class="TEXT-[10PX] FONT-BLACK TEXT-GRAY-400 ML-2">YOUR JOURNEY</label>
                    <input id="P-FROM" type="text" placeholder="PICKUP LOCATION">
                    <input id="P-TO" type="text" placeholder="DESTINATION">
                </div>
                
                <div class="GRID GRID-COLS-3 GAP-3 MB-6">
                    <button onclick="SELECT_V('BIKE', 150)" id="V-BIKE" class="V-BTN FLEX FLEX-COL ITEMS-CENTER"><i data-lucide="bike"></i><span class="TEXT-[10PX] FONT-BLACK MT-2">BIKE</span></button>
                    <button onclick="SELECT_V('CAR', 400)" id="V-CAR" class="V-BTN FLEX FLEX-COL ITEMS-CENTER V-SELECTED"><i data-lucide="car"></i><span class="TEXT-[10PX] FONT-BLACK MT-2">CAR</span></button>
                    <button onclick="SELECT_V('AUTO', 250)" id="V-AUTO" class="V-BTN FLEX FLEX-COL ITEMS-CENTER"><i data-lucide="loader"></i><span class="TEXT-[10PX] FONT-BLACK MT-2">AUTO</span></button>
                </div>

                <div class="BG-BLUE-600 P-8 ROUNDED-[2.5rem] TEXT-WHITE TEXT-CENTER SHADOW-xl MB-6">
                    <p id="FARE-DISPLAY" class="TEXT-5xl FONT-BLACK">400</p>
                    <p class="TEXT-[10PX] FONT-BOLD OPACITY-80 MT-2">AUTO-ESTIMATED FARE (PKR)</p>
                </div>
                <button onclick="REQUEST_RIDE()" class="W-FULL BG-BLACK TEXT-WHITE PY-6 ROUNDED-full FONT-BLACK TEXT-xl SHADOW-2xl">FIND DRIVER</button>
            </div>

            <div id="ACTIVE-RIDE-BOX" class="HIDDEN NEO-CARD P-6 BORDER-T-8 BORDER-GREEN-500">
                <div class="FLEX JUSTIFY-BETWEEN ITEMS-CENTER MB-4">
                    <h3 class="FONT-BLACK TEXT-GREEN-600 ITALIC">RIDE IN PROGRESS</h3>
                    <button onclick="cancelRide()" class="TEXT-[10PX] FONT-BLACK TEXT-RED-500">CANCEL</button>
                </div>
                <div id="DRIVER-INFO" class="BG-GRAY-50 P-4 ROUNDED-2xl TEXT-sm FONT-BOLD SPACE-Y-1 MB-4"></div>
                
                <div class="CHAT-AREA" id="CHAT-MESSAGES"></div>
                <div class="FLEX GAP-2 MT-4">
                    <input id="CHAT-INPUT" type="text" placeholder="MESSAGE DRIVER..." class="!MB-0">
                    <button onclick="SEND_MSG()" class="BG-BLUE-600 TEXT-WHITE PX-6 ROUNDED-2xl"><i data-lucide="send"></i></button>
                </div>
            </div>
        </div>

        <div id="D-UI" class="HIDDEN SPACE-Y-6">
            <div class="FLEX JUSTIFY-BETWEEN ITEMS-CENTER PX-2">
                <h2 class="FONT-BLACK TEXT-xl">LIVE RIDE REQUESTS</h2>
                <div class="W-3 H-3 BG-GREEN-500 ROUNDED-FULL ANIMATE-PULSE"></div>
            </div>
            <div id="D-FEED" class="SPACE-Y-4"></div>
            
            <div id="D-ACTIVE-RIDE" class="HIDDEN NEO-CARD P-6 BORDER-T-8 BORDER-BLUE-600">
                <div class="FLEX JUSTIFY-BETWEEN ITEMS-CENTER MB-4">
                    <h3 class="FONT-BLACK TEXT-BLUE-600 ITALIC">ACTIVE CLIENT CHAT</h3>
                </div>
                <div id="PASSENGER-INFO" class="BG-GRAY-50 P-4 ROUNDED-2xl TEXT-sm FONT-BOLD MB-4"></div>
                <div class="CHAT-AREA" id="D-CHAT-MESSAGES"></div>
                <div class="FLEX GAP-2 MT-4">
                    <input id="D-CHAT-INPUT" type="text" placeholder="REPLY TO CLIENT..." class="!MB-0">
                    <button onclick="SEND_MSG_DRIVER()" class="BG-BLUE-600 TEXT-WHITE PX-6 ROUNDED-2xl"><i data-lucide="send"></i></button>
                </div>
            </div>
        </div>

    </main>

    <script>
        lucide.createIcons();
        let currentFare = 400;
        let petPrice = 280;
        let selectedV = 'CAR';
        let activeRideId = null;

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
        const auth = firebase.auth();
        const db = firebase.database();

        function SAVE_DRIVER_PROFILE() {
            const d = {
                realName: document.getElementById('D-REALNAME').value,
                phone: document.getElementById('D-PHONE').value,
                cnic: document.getElementById('D-CNIC').value,
                vehicleNo: document.getElementById('D-VEHICLE').value,
                vType: document.getElementById('D-VTYPE').value,
                role: 'driver'
            };
            if(!d.realName || !d.phone) return alert("COMPLETE ALL FIELDS SWEETIE!");
            db.ref('users/' + auth.currentUser.uid).set(d).then(() => location.reload());
        }

        function SELECT_V(type, base) {
            selectedV = type;
            document.querySelectorAll('.V-BTN').forEach(b => b.classList.remove('V-SELECTED'));
            document.getElementById('V-'+type).classList.add('V-SELECTED');
            currentFare = Math.round(base * (petPrice / 280));
            document.getElementById('FARE-DISPLAY').innerText = currentFare;
        }

        function REQUEST_RIDE() {
            const f = document.getElementById('P-FROM').value;
            const t = document.getElementById('P-TO').value;
            if(!f || !t) return alert("ENTER PICKUP & DROP OFF!");
            
            const ride = {
                from: f.toUpperCase(),
                to: t.toUpperCase(),
                fare: currentFare,
                vType: selectedV,
                pName: auth.currentUser.displayName,
                pUid: auth.currentUser.uid,
                status: 'searching',
                time: Date.now()
            };
            activeRideId = db.ref('rides').push(ride).key;
            document.querySelector('.BG-BLACK').innerText = "SEARCHING...";
            listenToRideStatus(activeRideId);
        }

        function listenToRideStatus(id) {
            db.ref('rides/' + id).on('value', snap => {
                const r = snap.val();
                if(r && r.status === 'accepted') {
                    document.getElementById('ACTIVE-RIDE-BOX').classList.remove('HIDDEN');
                    document.getElementById('DRIVER-INFO').innerHTML = `
                        <p class="text-blue-600">DRIVER: ${r.dName}</p>
                        <p>PHONE: ${r.dPhone}</p>
                        <p>VEHICLE: ${r.dVeh}</p>
                    `;
                    syncChat(id, 'CHAT-MESSAGES');
                }
            });
        }

        function ACCEPT_RIDE(rid) {
            db.ref('users/' + auth.currentUser.uid).once('value').then(snap => {
                const d = snap.val();
                db.ref('rides/' + rid).update({
                    status: 'accepted',
                    dName: d.realName,
                    dPhone: d.phone,
                    dVeh: d.vehicleNo,
                    dUid: auth.currentUser.uid
                });
                activeRideId = rid;
                document.getElementById('D-FEED').classList.add('HIDDEN');
                document.getElementById('D-ACTIVE-RIDE').classList.remove('HIDDEN');
                syncChat(rid, 'D-CHAT-MESSAGES');
            });
        }

        function SEND_MSG() {
            const m = document.getElementById('CHAT-INPUT').value;
            if(!m) return;
            db.ref('rides/' + activeRideId + '/chat').push({ s: auth.currentUser.displayName, t: m });
            document.getElementById('CHAT-INPUT').value = "";
        }

        function SEND_MSG_DRIVER() {
            const m = document.getElementById('D-CHAT-INPUT').value;
            if(!m) return;
            db.ref('rides/' + activeRideId + '/chat').push({ s: auth.currentUser.displayName, t: m });
            document.getElementById('D-CHAT-INPUT').value = "";
        }

        function syncChat(id, boxId) {
            db.ref('rides/' + id + '/chat').on('value', snap => {
                const b = document.getElementById(boxId); b.innerHTML = "";
                snap.forEach(m => {
                    const d = m.val();
                    b.innerHTML += `<div class="MB-2 p-2 rounded-lg ${d.s === auth.currentUser.displayName ? 'bg-blue-100 ml-8':'bg-white mr-8'} shadow-sm">
                        <p class="text-[9px] font-black opacity-40">${d.s}</p>
                        <p class="text-xs font-bold">${d.t}</p>
                    </div>`;
                });
                b.scrollTop = b.scrollHeight;
            });
        }

        function syncDriverFeed() {
            db.ref('rides').on('value', snap => {
                const f = document.getElementById('D-FEED'); f.innerHTML = "";
                snap.forEach(c => {
                    const r = c.val();
                    if(r.status === 'searching') {
                        f.innerHTML += `
                        <div class="NEO-CARD P-6 BORDER-L-[10px] BORDER-BLUE-600">
                            <div class="FLEX JUSTIFY-BETWEEN MB-2"><span class="BG-BLUE-100 TEXT-BLUE-700 PX-3 PY-1 ROUNDED-FULL TEXT-[10PX] FONT-BLACK">${r.vType}</span></div>
                            <p class="FONT-BLACK TEXT-lg">${r.from} ➔ ${r.to}</p>
                            <p class="TEXT-[10px] FONT-BOLD TEXT-GRAY-400 mt-1">CLIENT: ${r.pName}</p>
                            <div class="MT-6 FLEX JUSTIFY-BETWEEN ITEMS-CENTER">
                                <span class="TEXT-3xl FONT-BLACK TEXT-GREEN-600">RS ${r.fare}</span>
                                <button onclick="ACCEPT_RIDE('${c.key}')" class="BG-BLACK TEXT-WHITE PX-8 PY-3 ROUNDED-FULL FONT-BLACK TEXT-sm shadow-lg">ACCEPT</button>
                            </div>
                        </div>`;
                    }
                });
            });
        }

        auth.onAuthStateChanged(user => {
            if(user) {
                document.getElementById('DISPLAY-NAME').innerText = user.displayName.toUpperCase();
                db.ref('users/' + user.uid).once('value').then(snap => {
                    if(snap.exists()) {
                        const role = snap.val().role;
                        document.getElementById('USER-ROLE-TXT').innerText = role;
                        showView(role);
                    } else document.getElementById('ROLE-SCREEN').classList.remove('HIDDEN');
                });
            } else { login(); }
        });

        function showView(v) {
            document.getElementById('ROLE-SCREEN').classList.add('HIDDEN');
            if(v === 'passenger') document.getElementById('P-UI').classList.remove('HIDDEN');
            else if(v === 'driver') { document.getElementById('D-UI').classList.remove('HIDDEN'); syncDriverFeed(); }
        }

        function setRole(r) { db.ref('users/' + auth.currentUser.uid).set({ role: r, name: auth.currentUser.displayName }).then(()=>location.reload()); }
        function login() { auth.signInWithPopup(new firebase.auth.GoogleAuthProvider()); }
        function logout() { auth.signOut(); location.reload(); }
        db.ref('settings/petrol').on('value', snap => { petPrice = snap.val() || 280; });
    </script>
</body>
</html>
