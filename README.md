<html lang="UR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>GB DRIVE - THE ULTIMATE MASTER EDITION</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-auth-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-database-compat.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=OUTFIT:wght@300;600;900&display=swap');
        BODY { font-family: 'OUTFIT', sans-serif; background: #F8FAFC; text-transform: uppercase; }
        .HIDDEN { display: none; }
        .NEO-CARD { background: white; border-radius: 2.5rem; box-shadow: 0 10px 30px rgba(0,0,0,0.05); }
        .V-BTN.SELECTED { border: 4px solid #2563EB; background: #EFF6FF; }
        .ADMIN-BG { background: linear-gradient(135deg, #0F172A 0%, #1E293B 100%); }
        INPUT::placeholder { text-transform: uppercase; }
    </style>
</head>
<body class="PB-24">

    <div id="LOGIN-SCREEN" class="MIN-H-SCREEN FLEX FLEX-COL ITEMS-CENTER JUSTIFY-CENTER P-6 TEXT-CENTER">
        <div onclick="ADMINTAP()" class="BG-BLUE-600 TEXT-WHITE W-24 H-24 FLEX ITEMS-CENTER JUSTIFY-CENTER ROUNDED-[2.5REM] FONT-BLACK TEXT-5XL SHADOW-2XL ITALIC MB-6 CURSOR-POINTER">GB</div>
        <h1 class="TEXT-4XL FONT-BLACK TEXT-BLUE-900 TRACKING-TIGHTER">GB DRIVE PRO</h1>
        <p class="TEXT-GRAY-400 MT-2 TEXT-SM ITALIC">TAP LOGO 4 TIMES FOR ADMIN</p>
        <button onclick="LOGIN()" class="MT-10 W-FULL MAX-W-XS BG-WHITE PY-5 PX-6 ROUNDED-3XL SHADOW-XL FLEX ITEMS-CENTER JUSTIFY-CENTER GAP-4 FONT-BLACK BORDER ACTIVE:SCALE-95 TRANSITION-ALL">
            <img src="https://www.gstatic.com/firebasejs/ui/2.0.0/images/auth/google.svg" width="22"> GOOGLE LOGIN
        </button>
    </div>

    <div id="MAIN-APP" class="HIDDEN">
        <header class="P-4 FLEX JUSTIFY-BETWEEN ITEMS-CENTER BG-WHITE BORDER-B STICKY TOP-0 Z-50">
            <div class="FLEX ITEMS-CENTER GAP-3">
                <div onclick="ADMINTAP()" class="BG-BLUE-600 TEXT-WHITE W-8 H-8 FLEX ITEMS-CENTER JUSTIFY-CENTER ROUNDED-LG FONT-BLACK ITALIC CURSOR-POINTER TEXT-XS">GB</div>
                <p id="U-NAME" class="FONT-BLACK TEXT-SM TEXT-BLUE-900"></p>
            </div>
            <button onclick="LOGOUT()" class="TEXT-RED-500 P-2"><i data-lucide="log-out" class="W-5"></i></button>
        </header>

        <main class="P-4 MAX-W-MD MX-AUTO SPACE-Y-6">
            <div id="ROLE-SCREEN" class="HIDDEN SPACE-Y-4 PY-10">
                <h2 class="TEXT-2XL FONT-BLACK TEXT-BLUE-900 TEXT-CENTER">CHOOSE YOUR ROLE</h2>
                <button onclick="SETROLE('PASSENGER')" class="W-FULL P-6 NEO-CARD TEXT-LEFT FONT-BLACK BORDER-2 BORDER-TRANSPARENT HOVER:BORDER-BLUE-600">PASSENGER 🙋‍♂️</button>
                <button onclick="SETROLE('DRIVER')" class="W-FULL P-6 NEO-CARD TEXT-LEFT FONT-BLACK BORDER-2 BORDER-TRANSPARENT HOVER:BORDER-GREEN-600">DRIVER 🚗</button>
            </div>

            <div id="P-UI" class="HIDDEN SPACE-Y-4">
                <div class="NEO-CARD P-6 SPACE-Y-4 BORDER-B-8 BORDER-BLUE-600">
                    <input id="P-FROM" type="text" placeholder="PICKUP POINT" class="W-FULL P-4 BG-GRAY-50 ROUNDED-2XL OUTLINE-NONE FONT-BOLD">
                    <input id="P-TO" type="text" placeholder="DESTINATION" class="W-FULL P-4 BG-GRAY-50 ROUNDED-2XL OUTLINE-NONE FONT-BOLD">
                    <div class="GRID GRID-COLS-3 GAP-2">
                        <button onclick="SELV('BIKE')" id="V-BIKE" class="V-BTN P-3 ROUNDED-2XL BG-GRAY-50 FLEX FLEX-COL ITEMS-CENTER"><i data-lucide="bike" class="W-5"></i><span class="TEXT-[8PX] FONT-BLACK MT-1">BIKE</span></button>
                        <button onclick="SELV('CAR')" id="V-CAR" class="V-BTN P-3 ROUNDED-2XL BG-GRAY-50 FLEX FLEX-COL ITEMS-CENTER SELECTED"><i data-lucide="car" class="W-5"></i><span class="TEXT-[8PX] FONT-BLACK MT-1">CAR</span></button>
                        <button onclick="SELV('AUTO')" id="V-AUTO" class="V-BTN P-3 ROUNDED-2XL BG-GRAY-50 FLEX FLEX-COL ITEMS-CENTER"><i data-lucide="loader" class="W-5"></i><span class="TEXT-[8PX] FONT-BLACK MT-1">AUTO</span></button>
                    </div>
                    <div class="BG-BLUE-600 P-6 ROUNDED-[2REM] TEXT-WHITE TEXT-CENTER SHADOW-LG">
                        <p id="FARE-TXT" class="TEXT-4XL FONT-BLACK">400</p>
                        <p class="TEXT-[9PX] FONT-BOLD OPACITY-60">ESTIMATED FARE (PKR)</p>
                    </div>
                    <button onclick="REQRIDE()" class="W-FULL BG-BLUE-900 TEXT-WHITE PY-5 ROUNDED-[2REM] FONT-BLACK TEXT-XL SHADOW-XL">FIND RIDE</button>
                </div>
            </div>

            <div id="D-UI" class="HIDDEN SPACE-Y-4">
                <h3 class="FONT-BLACK TEXT-XL PX-2">LIVE RIDE REQUESTS</h3>
                <div id="D-FEED" class="SPACE-Y-4"></div>
            </div>

            <div id="ADMIN-UI" class="HIDDEN SPACE-Y-6">
                <div class="ADMIN-BG P-6 ROUNDED-[2.5REM] TEXT-WHITE SHADOW-XL">
                    <h2 class="TEXT-2xl FONT-BLACK MB-4 ITALIC">ADMIN CONTROL CENTER</h2>
                    <div class="GRID GRID-COLS-2 GAP-4">
                        <div class="BG-WHITE/10 P-4 ROUNDED-2XL">
                            <p class="TEXT-[9PX] FONT-BLACK OPACITY-60">PETROL PRICE</p>
                            <input id="PET-IN" type="number" onchange="UPDFUEL(this.value)" class="BG-TRANSPARENT TEXT-XL FONT-BLACK W-FULL OUTLINE-NONE">
                        </div>
                        <div class="BG-WHITE/10 P-4 ROUNDED-2XL">
                            <p class="TEXT-[9PX] FONT-BLACK OPACITY-60">ACTIVE RIDES</p>
                            <p id="ADM-COUNT" class="TEXT-XL FONT-BLACK">0</p>
                        </div>
                    </div>
                    <button onclick="location.reload()" class="W-FULL MT-4 BG-WHITE/20 PY-2 ROUNDED-XL TEXT-[10PX] FONT-BLACK">CLOSE ADMIN</button>
                </div>
                <div id="ADMIN-FEED" class="SPACE-Y-3"></div>
            </div>
        </main>
    </div>

    <script>
        lucide.createIcons();
        let TAPCOUNT = 0;
        let PETROL = 280;
        let VEHICLE = 'CAR';

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
        const provider = new firebase.auth.GoogleAuthProvider();

        function LOGIN() { auth.signInWithPopup(provider); }
        function LOGOUT() { auth.signOut(); location.reload(); }

        function ADMINTAP() {
            TAPCOUNT++;
            if(TAPCOUNT === 4) {
                TAPCOUNT = 0;
                let KEY = prompt("ENTER SECRET KEY:");
                if(KEY === "GB46" || KEY === "gb46") { SHOWVIEW('ADMIN'); }
                else { alert("WRONG KEY SWEETIE! 😘"); }
            }
        }

        auth.onAuthStateChanged(user => {
            if(user) {
                document.getElementById('LOGIN-SCREEN').classList.add('HIDDEN');
                document.getElementById('MAIN-APP').classList.remove('HIDDEN');
                document.getElementById('U-NAME').innerText = user.displayName.toUpperCase();
                db.ref('users/' + user.uid).once('value').then(snap => {
                    if(snap.exists()) SHOWVIEW(snap.val().role.toLowerCase());
                    else document.getElementById('ROLE-SCREEN').classList.remove('HIDDEN');
                });
            } else {
                document.getElementById('LOGIN-SCREEN').classList.remove('HIDDEN');
                document.getElementById('MAIN-APP').classList.add('HIDDEN');
            }
        });

        function SETROLE(role) {
            db.ref('users/' + auth.currentUser.uid).set({ role, name: auth.currentUser.displayName }).then(() => location.reload());
        }

        function SHOWVIEW(v) {
            ['P-UI', 'D-UI', 'ADMIN-UI', 'ROLE-SCREEN'].forEach(id => document.getElementById(id).classList.add('HIDDEN'));
            if(v === 'passenger') document.getElementById('P-UI').classList.remove('HIDDEN');
            else if(v === 'driver') { document.getElementById('D-UI').classList.remove('HIDDEN'); SYNC('D-FEED'); }
            else if(v === 'admin') { document.getElementById('ADMIN-UI').classList.remove('HIDDEN'); SYNC('ADMIN-FEED', true); }
        }

        function SYNC(id, isAdmin = false) {
            db.ref('rides').on('value', snap => {
                const f = document.getElementById(id); f.innerHTML = "";
                let count = 0;
                snap.forEach(c => {
                    const r = c.val(); count++;
                    f.innerHTML += `<div class="NEO-CARD P-5 BORDER-L-8 ${isAdmin ? 'BORDER-RED-500':'BORDER-BLUE-600'} MB-4">
                        <p class="FONT-BLACK TEXT-SM">${r.FROM} ➔ ${r.TO}</p>
                        <p class="TEXT-[10PX] TEXT-GRAY-400 FONT-BOLD UP-CASE MT-1">${r.VTYPE} | RS ${r.FARE}</p>
                        ${isAdmin ? `<button onclick="REM('${c.key}')" class="TEXT-RED-500 FONT-BLACK TEXT-[10PX] MT-2">DELETE</button>` : `<button onclick="REM('${c.key}')" class="W-FULL BG-BLUE-900 TEXT-WHITE PY-2 ROUNDED-XL FONT-BLACK TEXT-[10PX] MT-3">ACCEPT RIDE</button>`}
                    </div>`;
                });
                if(isAdmin) document.getElementById('ADM-COUNT').innerText = count;
            });
        }

        function REM(rid) { db.ref('rides/' + rid).remove(); if(!isAdminView()) alert("RIDE ACCEPTED! 🚗"); }
        function isAdminView() { return !document.getElementById('ADMIN-UI').classList.contains('HIDDEN'); }
        function UPDFUEL(v) { db.ref('settings/petrol').set(v); }
        db.ref('settings/petrol').on('value', snap => { PETROL = snap.val() || 280; document.getElementById('PET-IN').value = PETROL; });

        function SELV(t) {
            VEHICLE = t;
            document.querySelectorAll('.V-BTN').forEach(b => b.classList.remove('SELECTED'));
            document.getElementById('V-'+t).classList.add('SELECTED');
            let base = t === 'BIKE' ? 150 : (t === 'AUTO' ? 250 : 400);
            document.getElementById('FARE-TXT').innerText = Math.round(base * (PETROL/280));
        }

        function REQRIDE() {
            const f = document.getElementById('P-FROM').value.toUpperCase();
            const t = document.getElementById('P-TO').value.toUpperCase();
            if(!f || !t) return alert("ENTER LOCATIONS!");
            db.ref('rides').push({ FROM: f, TO: t, FARE: document.getElementById('FARE-TXT').innerText, VTYPE: VEHICLE, USER: auth.currentUser.displayName });
            alert("RIDE POSTED SUCCESSFULLY! 🔥");
        }
    </script>
</body>
</html>
