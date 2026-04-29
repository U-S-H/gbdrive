<html lang="EN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>GB DRIVE - ULTIMATE PRO</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-auth-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-database-compat.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Outfit:wght@400;700;900&display=swap');
        BODY { font-family: 'Outfit', sans-serif; background: #F4F7F6; }
        .HIDDEN { display: none !important; }
        .MODAL { background: rgba(0,0,0,0.8); backdrop-filter: blur(8px); }
        .NEO-CARD { background: white; border-radius: 2rem; box-shadow: 0 10px 30px rgba(0,0,0,0.05); }
        .CHAT-BOX { height: 250px; overflow-y: auto; background: #F9FAFB; border-radius: 1.5rem; padding: 15px; }
        .V-SELECTED { border: 3px solid #2563EB !important; background: #EFF6FF !important; }
        INPUT { border: 2px solid #E2E8F0 !important; border-radius: 1rem !important; padding: 1rem !important; width: 100%; margin-bottom: 0.8rem; font-weight: 600; outline: none; }
        INPUT:focus { border-color: #2563EB !important; }
        .BTN-PRIMARY { background: #2563EB; color: white; font-weight: 900; border-radius: 1.2rem; padding: 1rem; width: 100%; transition: 0.3s; }
        .BTN-PRIMARY:active { transform: scale(0.95); }
    </style>
</head>
<body class="PB-20">

    <div id="D-MODAL" class="HIDDEN FIXED INSET-0 Z-[100] MODAL FLEX ITEMS-CENTER JUSTIFY-CENTER P-6">
        <div class="BG-WHITE W-FULL MAX-W-MD ROUNDED-[2.5rem] P-8">
            <h2 class="FONT-BLACK TEXT-2xl TEXT-CENTER MB-6">DRIVER VERIFICATION</h2>
            <input id="D-FULLNAME" type="text" placeholder="REAL FULL NAME">
            <input id="D-NUMBER" type="tel" placeholder="PHONE NUMBER">
            <input id="D-CNIC" type="text" placeholder="CNIC NUMBER">
            <input id="D-PLATE" type="text" placeholder="VEHICLE PLATE NO">
            <button onclick="SAVE_DRIVER()" class="BTN-PRIMARY MT-4">SAVE & START EARNING</button>
        </div>
    </div>

    <div id="MAIN-APP" class="HIDDEN">
        <header class="P-5 BG-WHITE BORDER-B STICKY TOP-0 Z-50 FLEX JUSTIFY-BETWEEN ITEMS-CENTER">
            <div class="FLEX ITEMS-CENTER GAP-3">
                <div class="BG-BLUE-600 TEXT-WHITE W-10 H-10 FLEX ITEMS-CENTER JUSTIFY-CENTER ROUNDED-2xl FONT-BLACK ITALIC shadow-lg">GB</div>
                <div>
                    <p id="U-NAME-TXT" class="FONT-BLACK TEXT-sm text-blue-900"></p>
                    <p id="U-ROLE-TXT" class="TEXT-[9PX] FONT-BOLD OPACITY-50 uppercase"></p>
                </div>
            </div>
            <button onclick="logout()" class="TEXT-RED-500"><i data-lucide="log-out"></i></button>
        </header>

        <main class="P-5 MAX-W-MD MX-AUTO">
            
            <div id="ROLE-SCREEN" class="HIDDEN PY-20 SPACE-Y-6 text-center">
                <h1 class="TEXT-3xl FONT-BLACK TEXT-BLUE-900">JOIN THE DRIVE</h1>
                <button onclick="setRole('passenger')" class="W-FULL P-8 NEO-CARD FONT-BLACK TEXT-2xl">PASSENGER</button>
                <button onclick="document.getElementById('D-MODAL').classList.remove('HIDDEN')" class="W-FULL P-8 NEO-CARD FONT-BLACK TEXT-2xl BORDER-2 BORDER-BLACK">DRIVER</button>
            </div>

            <div id="P-UI" class="HIDDEN SPACE-Y-6">
                <div class="NEO-CARD P-6">
                    <input id="P-FROM" type="text" placeholder="PICKUP LOCATION">
                    <input id="P-TO" type="text" placeholder="DESTINATION">
                    
                    <div class="GRID GRID-COLS-3 GAP-3 MB-6">
                        <button onclick="SEL_V('BIKE', 150)" id="V-BIKE" class="BG-GRAY-50 P-4 ROUNDED-2xl FLEX FLEX-COL ITEMS-CENTER BORDER-2 BORDER-TRANSPARENT"><i data-lucide="bike"></i><span class="TEXT-[10PX] FONT-BLACK mt-1">BIKE</span></button>
                        <button onclick="SEL_V('CAR', 400)" id="V-CAR" class="BG-GRAY-50 P-4 ROUNDED-2xl FLEX FLEX-COL ITEMS-CENTER BORDER-2 BORDER-TRANSPARENT V-SELECTED"><i data-lucide="car"></i><span class="TEXT-[10PX] FONT-BLACK mt-1">CAR</span></button>
                        <button onclick="SEL_V('AUTO', 250)" id="V-AUTO" class="BG-GRAY-50 P-4 ROUNDED-2xl FLEX FLEX-COL ITEMS-CENTER BORDER-2 BORDER-TRANSPARENT"><i data-lucide="loader"></i><span class="TEXT-[10PX] FONT-BLACK mt-1">AUTO</span></button>
                    </div>

                    <div class="BG-BLUE-600 P-8 ROUNDED-[2.5rem] TEXT-WHITE TEXT-CENTER SHADOW-xl MB-6">
                        <p id="FARE-TXT" class="TEXT-5xl FONT-BLACK">400</p>
                        <p class="TEXT-[10PX] FONT-BOLD OPACITY-80 mt-2">AUTO-CALCULATED PKR</p>
                    </div>
                    <button onclick="REQ_RIDE()" class="BTN-PRIMARY TEXT-xl">BOOK NOW</button>
                </div>

                <div id="P-RIDE-ACTIVE" class="HIDDEN NEO-CARD P-6 BORDER-T-8 BORDER-GREEN-500">
                    <div id="D-DETAILS-BOX" class="BG-GRAY-50 P-4 ROUNDED-2xl TEXT-sm FONT-BOLD MB-4"></div>
                    <div id="P-CHAT" class="CHAT-BOX"></div>
                    <div class="FLEX GAP-2 MT-4">
                        <input id="P-MSG-IN" type="text" placeholder="CHAT WITH DRIVER..." class="!m-0">
                        <button onclick="S_MSG('P')" class="BG-BLUE-600 TEXT-WHITE PX-6 ROUNDED-2xl"><i data-lucide="send"></i></button>
                    </div>
                </div>
            </div>

            <div id="D-UI" class="HIDDEN SPACE-Y-6">
                <h2 class="FONT-BLACK TEXT-xl">RIDE REQUESTS</h2>
                <div id="D-FEED" class="SPACE-Y-4"></div>

                <div id="D-RIDE-ACTIVE" class="HIDDEN NEO-CARD P-6 BORDER-T-8 BORDER-BLUE-600">
                    <div id="P-DETAILS-BOX" class="BG-GRAY-50 P-4 ROUNDED-2xl TEXT-sm FONT-BOLD MB-4"></div>
                    <div id="D-CHAT" class="CHAT-BOX"></div>
                    <div class="FLEX GAP-2 MT-4">
                        <input id="D-MSG-IN" type="text" placeholder="CHAT WITH CLIENT..." class="!m-0">
                        <button onclick="S_MSG('D')" class="BG-BLUE-600 TEXT-WHITE PX-6 ROUNDED-2xl"><i data-lucide="send"></i></button>
                    </div>
                </div>
            </div>
        </main>
    </div>

    <script>
        lucide.createIcons();
        let petP = 280; let curF = 400; let curV = 'CAR'; let rideID = null;

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

        function logout() { auth.signOut(); location.reload(); }
        
        auth.onAuthStateChanged(user => {
            if(user) {
                document.getElementById('MAIN-APP').classList.remove('HIDDEN');
                document.getElementById('U-NAME-TXT').innerText = user.displayName.toUpperCase();
                db.ref('users/' + user.uid).on('value', snap => {
                    if(snap.exists()) {
                        const r = snap.val().role;
                        document.getElementById('U-ROLE-TXT').innerText = r;
                        showUI(r);
                    } else document.getElementById('ROLE-SCREEN').classList.remove('HIDDEN');
                });
            } else { auth.signInWithPopup(new firebase.auth.GoogleAuthProvider()); }
        });

        function SAVE_DRIVER() {
            const d = { 
                name: document.getElementById('D-FULLNAME').value,
                phone: document.getElementById('D-NUMBER').value,
                cnic: document.getElementById('D-CNIC').value,
                plate: document.getElementById('D-PLATE').value,
                role: 'driver'
            };
            db.ref('users/' + auth.currentUser.uid).set(d).then(() => location.reload());
        }

        function showUI(v) {
            document.getElementById('ROLE-SCREEN').classList.add('HIDDEN');
            if(v === 'passenger') document.getElementById('P-UI').classList.remove('HIDDEN');
            else { document.getElementById('D-UI').classList.remove('HIDDEN'); SYNC_FEED(); }
        }

        function SEL_V(t, b) {
            curV = t;
            document.querySelectorAll('[id^="V-"]').forEach(btn => btn.classList.remove('V-SELECTED'));
            document.getElementById('V-'+t).classList.add('V-SELECTED');
            curF = Math.round(b * (petP/280));
            document.getElementById('FARE-TXT').innerText = curF;
        }

        function REQ_RIDE() {
            const f = document.getElementById('P-FROM').value;
            const t = document.getElementById('P-TO').value;
            if(!f || !t) return alert("LOCATIONS MISSING!");
            rideID = db.ref('rides').push({
                f, t, fare: curF, vType: curV, pName: auth.currentUser.displayName, pUid: auth.currentUser.uid, status: 'searching'
            }).key;
            alert("SEARCHING DRIVER...");
            LISTEN_RIDE(rideID);
        }

        function LISTEN_RIDE(id) {
            db.ref('rides/'+id).on('value', snap => {
                const r = snap.val();
                if(r && r.status === 'accepted') {
                    document.getElementById('P-RIDE-ACTIVE').classList.remove('HIDDEN');
                    document.getElementById('D-DETAILS-BOX').innerHTML = `DRIVER: ${r.dName}<br>PHONE: ${r.dPhone}<br>PLATE: ${r.dPlate}`;
                    SYNC_CHAT(id, 'P-CHAT');
                }
            });
        }

        function SYNC_FEED() {
            db.ref('rides').on('value', snap => {
                const f = document.getElementById('D-FEED'); f.innerHTML = "";
                snap.forEach(c => {
                    const r = c.val();
                    if(r.status === 'searching') {
                        f.innerHTML += `
                        <div class="NEO-CARD P-6 BORDER-L-8 BORDER-BLUE-600">
                            <p class="FONT-BLACK">${r.f} ➔ ${r.t}</p>
                            <p class="TEXT-[10px] FONT-BOLD OPACITY-40 mt-1">${r.vType} • ${r.pName}</p>
                            <div class="MT-4 FLEX JUSTIFY-BETWEEN ITEMS-CENTER">
                                <b class="TEXT-3xl TEXT-GREEN-600">RS ${r.fare}</b>
                                <button onclick="ACCEPT_RIDE('${c.key}')" class="BG-BLACK TEXT-WHITE PX-8 PY-3 ROUNDED-2xl FONT-BLACK text-xs">ACCEPT</button>
                            </div>
                        </div>`;
                    }
                });
            });
        }

        function ACCEPT_RIDE(id) {
            db.ref('users/'+auth.currentUser.uid).once('value', snap => {
                const d = snap.val();
                db.ref('rides/'+id).update({
                    status: 'accepted', dName: d.name, dPhone: d.phone, dPlate: d.plate, dUid: auth.currentUser.uid
                });
                rideID = id;
                document.getElementById('D-FEED').classList.add('HIDDEN');
                document.getElementById('D-RIDE-ACTIVE').classList.remove('HIDDEN');
                db.ref('rides/'+id).once('value', s => {
                    document.getElementById('P-DETAILS-BOX').innerHTML = `CLIENT: ${s.val().pName}<br>ROUTE: ${s.val().f} to ${s.val().t}`;
                });
                SYNC_CHAT(id, 'D-CHAT');
            });
        }

        function S_MSG(type) {
            const m = document.getElementById(type+'-MSG-IN').value; if(!m) return;
            db.ref('rides/'+rideID+'/chat').push({ s: auth.currentUser.displayName, t: m });
            document.getElementById(type+'-MSG-IN').value = "";
        }

        function SYNC_CHAT(id, boxID) {
            db.ref('rides/'+id+'/chat').on('value', snap => {
                const b = document.getElementById(boxID); b.innerHTML = "";
                snap.forEach(m => {
                    const d = m.val();
                    const isMe = d.s === auth.currentUser.displayName;
                    b.innerHTML += `<div class="mb-2 p-3 rounded-2xl text-xs font-bold ${isMe ? 'bg-blue-600 text-white ml-10':'bg-white text-black mr-10 shadow-sm'}">${d.t}</div>`;
                });
                b.scrollTop = b.scrollHeight;
            });
        }

        db.ref('settings/petrol').on('value', snap => { petP = snap.val() || 280; });
    </script>
</body>
</html>
