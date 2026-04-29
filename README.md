<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>GB DRIVE PRO</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-auth-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-database-compat.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Outfit:wght@400;600;900&display=swap');
        * { font-family: 'Outfit', sans-serif; -webkit-tap-highlight-color: transparent; }
        body { background-color: #f8fafc; color: #1e293b; overflow-x: hidden; }
        .hidden { display: none !important; }
        .neo-card { background: white; border-radius: 24px; box-shadow: 0 10px 30px -5px rgba(0,0,0,0.1); border: 1px solid rgba(255,255,255,0.3); }
        .v-btn { background: #f1f5f9; border: 2px solid transparent; border-radius: 20px; transition: 0.2s; padding: 12px; }
        .v-selected { background: #eff6ff; border-color: #2563eb; color: #2563eb; box-shadow: 0 4px 12px rgba(37,99,235,0.2); }
        input { border: 2px solid #e2e8f0 !important; border-radius: 16px !important; padding: 14px !important; width: 100%; font-weight: 600; transition: 0.2s; }
        input:focus { border-color: #2563eb !important; background: #fff; }
        .chat-msg { padding: 10px 14px; border-radius: 18px; margin-bottom: 8px; font-size: 14px; max-width: 80%; }
        .msg-me { background: #2563eb; color: white; margin-left: auto; border-bottom-right-radius: 4px; }
        .msg-them { background: #e2e8f0; color: #1e293b; margin-right: auto; border-bottom-left-radius: 4px; }
        .btn-main { background: #2563eb; color: white; font-weight: 900; border-radius: 18px; padding: 18px; width: 100%; box-shadow: 0 8px 20px rgba(37,99,235,0.3); }
        .btn-main:active { transform: scale(0.97); }
    </style>
</head>
<body class="pb-10">

    <div id="login-screen" class="min-h-screen flex flex-col items-center justify-center p-8 bg-white">
        <div onclick="adminTap()" class="bg-blue-600 text-white w-24 h-24 flex items-center justify-center rounded-[32px] font-black text-5xl italic shadow-2xl mb-8 cursor-pointer">GB</div>
        <h1 class="text-4xl font-black tracking-tighter text-slate-900">GB DRIVE</h1>
        <p class="text-slate-400 font-bold mt-2 uppercase tracking-widest text-xs">Modern Ride Sharing</p>
        <button onclick="login()" class="mt-12 w-full max-w-xs bg-slate-900 text-white py-5 rounded-2xl flex items-center justify-center gap-3 font-black shadow-xl">
            <img src="https://www.gstatic.com/firebasejs/ui/2.0.0/images/auth/google.svg" width="20" class="bg-white rounded-full p-0.5"> LOGIN WITH GOOGLE
        </button>
    </div>

    <div id="main-app" class="hidden">
        <header class="p-5 bg-white/80 backdrop-blur-md sticky top-0 z-50 flex justify-between items-center border-b border-slate-100">
            <div class="flex items-center gap-3">
                <div class="bg-blue-600 text-white p-2 rounded-xl font-black text-xs italic">GB</div>
                <div>
                    <p id="u-name" class="font-black text-sm text-slate-900 uppercase"></p>
                    <p id="u-role" class="text-[9px] font-bold text-blue-600 uppercase tracking-tighter"></p>
                </div>
            </div>
            <button onclick="logout()" class="p-2 bg-red-50 text-red-500 rounded-xl"><i data-lucide="log-out" class="w-5"></i></button>
        </header>

        <main class="p-5 max-w-md mx-auto">
            
            <div id="role-screen" class="hidden py-16 space-y-6">
                <h2 class="text-2xl font-black text-center text-slate-900">WHO ARE YOU?</h2>
                <button onclick="setRole('passenger')" class="w-full p-8 neo-card text-left">
                    <span class="text-3xl">🙋‍♂️</span><br><b class="text-xl">PASSENGER</b>
                    <p class="text-xs text-slate-400 mt-1">I want a ride right now</p>
                </button>
                <button onclick="document.getElementById('driver-modal').classList.remove('hidden')" class="w-full p-8 neo-card text-left border-2 border-slate-900">
                    <span class="text-3xl">🚗</span><br><b class="text-xl">DRIVER</b>
                    <p class="text-xs text-slate-400 mt-1">I want to earn money</p>
                </button>
            </div>

            <div id="p-ui" class="hidden space-y-6">
                <div class="neo-card p-6">
                    <div class="space-y-4 mb-6">
                        <div class="relative">
                            <i data-lucide="map-pin" class="absolute left-4 top-4 w-5 text-blue-600"></i>
                            <input id="p-from" type="text" placeholder="PICKUP POINT" class="pl-12">
                        </div>
                        <div class="relative">
                            <i data-lucide="navigation" class="absolute left-4 top-4 w-5 text-red-500"></i>
                            <input id="p-to" type="text" placeholder="DESTINATION" class="pl-12">
                        </div>
                    </div>

                    <div class="grid grid-cols-3 gap-3 mb-6">
                        <button onclick="selV('BIKE', 150)" id="v-BIKE" class="v-btn flex flex-col items-center"><i data-lucide="bike"></i><span class="text-[10px] font-black mt-2">BIKE</span></button>
                        <button onclick="selV('CAR', 400)" id="v-CAR" class="v-btn flex flex-col items-center v-selected"><i data-lucide="car"></i><span class="text-[10px] font-black mt-2">CAR</span></button>
                        <button onclick="selV('AUTO', 250)" id="v-AUTO" class="v-btn flex flex-col items-center"><i data-lucide="loader"></i><span class="text-[10px] font-black mt-2">AUTO</span></button>
                    </div>

                    <div class="bg-blue-600 p-8 rounded-[28px] text-white text-center shadow-lg mb-6">
                        <p id="fare-txt" class="text-5xl font-black">400</p>
                        <p class="text-[11px] font-bold opacity-70 tracking-widest mt-2 uppercase">ESTIMATED PRICE (PKR)</p>
                    </div>
                    <button onclick="reqRide()" class="btn-main uppercase tracking-widest text-lg">BOOK RIDE</button>
                </div>

                <div id="p-active-ride" class="hidden neo-card p-6 border-t-8 border-blue-600">
                    <div id="d-details" class="mb-4 bg-slate-50 p-4 rounded-2xl"></div>
                    <div id="p-chat-box" class="h-48 overflow-y-auto mb-4 bg-slate-50 p-3 rounded-xl flex flex-col"></div>
                    <div class="flex gap-2">
                        <input id="p-msg" type="text" placeholder="Chat with driver..." class="!m-0">
                        <button onclick="sendMsg('p')" class="bg-blue-600 text-white px-5 rounded-xl"><i data-lucide="send"></i></button>
                    </div>
                </div>
            </div>

            <div id="d-ui" class="hidden space-y-6">
                <div class="flex justify-between items-center px-2">
                    <h2 class="text-xl font-black">ACTIVE REQUESTS</h2>
                    <span class="bg-green-100 text-green-600 text-[10px] px-3 py-1 rounded-full font-black">ONLINE</span>
                </div>
                <div id="d-feed" class="space-y-4"></div>

                <div id="d-active-ride" class="hidden neo-card p-6 border-t-8 border-green-500">
                    <div id="p-details" class="mb-4 bg-slate-50 p-4 rounded-2xl"></div>
                    <div id="d-chat-box" class="h-48 overflow-y-auto mb-4 bg-slate-50 p-3 rounded-xl flex flex-col"></div>
                    <div class="flex gap-2">
                        <input id="d-msg" type="text" placeholder="Chat with passenger..." class="!m-0">
                        <button onclick="sendMsg('d')" class="bg-blue-600 text-white px-5 rounded-xl"><i data-lucide="send"></i></button>
                    </div>
                </div>
            </div>
        </main>
    </div>

    <div id="driver-modal" class="hidden fixed inset-0 z-[100] flex items-center justify-center p-6 bg-slate-900/90 backdrop-blur-sm">
        <div class="bg-white w-full max-w-md rounded-[32px] p-8 space-y-4">
            <h2 class="text-2xl font-black text-center mb-4">DRIVER SETUP</h2>
            <input id="dr-name" type="text" placeholder="REAL NAME">
            <input id="dr-phone" type="tel" placeholder="PHONE NUMBER">
            <input id="dr-cnic" type="text" placeholder="CNIC NUMBER">
            <input id="dr-plate" type="text" placeholder="VEHICLE PLATE NO">
            <button onclick="saveDriver()" class="btn-main">FINISH SETUP</button>
            <button onclick="document.getElementById('driver-modal').classList.add('hidden')" class="w-full text-sm font-black text-slate-400">CANCEL</button>
        </div>
    </div>

    <script>
        lucide.createIcons();
        let curFare = 400; let pet = 280; let vType = 'CAR'; let rId = null;

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

        function login() { auth.signInWithPopup(new firebase.auth.GoogleAuthProvider()); }
        function logout() { auth.signOut(); location.reload(); }

        auth.onAuthStateChanged(user => {
            if(user) {
                document.getElementById('login-screen').classList.add('hidden');
                document.getElementById('main-app').classList.remove('hidden');
                document.getElementById('u-name').innerText = user.displayName;
                db.ref('users/' + user.uid).once('value', snap => {
                    if(snap.exists()) {
                        const r = snap.val().role;
                        document.getElementById('u-role').innerText = r;
                        showUI(r);
                    } else document.getElementById('role-screen').classList.remove('hidden');
                });
            }
        });

        function setRole(r) { db.ref('users/' + auth.currentUser.uid).set({ role: r, name: auth.currentUser.displayName }).then(() => location.reload()); }
        
        function saveDriver() {
            const d = { 
                name: document.getElementById('dr-name').value, 
                phone: document.getElementById('dr-phone').value, 
                cnic: document.getElementById('dr-cnic').value, 
                plate: document.getElementById('dr-plate').value,
                role: 'driver' 
            };
            db.ref('users/' + auth.currentUser.uid).set(d).then(() => location.reload());
        }

        function showUI(v) {
            document.getElementById('role-screen').classList.add('hidden');
            if(v === 'passenger') document.getElementById('p-ui').classList.remove('hidden');
            else if(v === 'driver') { document.getElementById('d-ui').classList.remove('hidden'); syncFeed(); }
        }

        function selV(t, b) {
            vType = t;
            document.querySelectorAll('.v-btn').forEach(btn => btn.classList.remove('v-selected'));
            document.getElementById('v-'+t).classList.add('v-selected');
            curFare = Math.round(b * (pet/280));
            document.getElementById('fare-txt').innerText = curFare;
        }

        function reqRide() {
            const f = document.getElementById('p-from').value; const t = document.getElementById('p-to').value;
            if(!f || !t) return alert("ENTER LOCATIONS");
            rId = db.ref('rides').push({ f, t, fare: curFare, vType, pName: auth.currentUser.displayName, pUid: auth.currentUser.uid, status: 'searching' }).key;
            db.ref('rides/'+rId).on('value', snap => {
                const r = snap.val();
                if(r && r.status === 'accepted') {
                    document.getElementById('p-active-ride').classList.remove('hidden');
                    document.getElementById('d-details').innerHTML = `<p class="font-black text-blue-600 uppercase text-xs">DRIVER ASSIGNED</p><p class="font-black">${r.dName}</p><p class="text-xs font-bold">${r.dPlate} | ${r.dPhone}</p>`;
                    syncChat(rId, 'p-chat-box');
                }
            });
        }

        function syncFeed() {
            db.ref('rides').on('value', snap => {
                const feed = document.getElementById('d-feed'); feed.innerHTML = "";
                snap.forEach(c => {
                    const r = c.val();
                    if(r.status === 'searching') {
                        feed.innerHTML += `<div class="neo-card p-6 border-l-[10px] border-blue-600">
                            <p class="font-black text-lg">${r.f} ➔ ${r.t}</p>
                            <p class="text-[10px] font-bold text-slate-400 mt-1">${r.vType} • ${r.pName}</p>
                            <div class="flex justify-between items-center mt-6">
                                <b class="text-2xl text-green-600">RS ${r.fare}</b>
                                <button onclick="accept('${c.key}')" class="bg-slate-900 text-white px-8 py-3 rounded-2xl font-black text-xs">ACCEPT</button>
                            </div>
                        </div>`;
                    }
                });
            });
        }

        function accept(id) {
            db.ref('users/' + auth.currentUser.uid).once('value', snap => {
                const d = snap.val();
                db.ref('rides/'+id).update({ status: 'accepted', dName: d.name, dPhone: d.phone, dPlate: d.plate, dUid: auth.currentUser.uid });
                rId = id;
                document.getElementById('d-feed').classList.add('hidden');
                document.getElementById('d-active-ride').classList.remove('hidden');
                db.ref('rides/'+id).once('value', s => {
                    document.getElementById('p-details').innerHTML = `<p class="font-black text-green-600 uppercase text-xs">CLIENT DETAILS</p><p class="font-black">${s.val().pName}</p><p class="text-xs font-bold">${s.val().f} ➔ ${s.val().t}</p>`;
                });
                syncChat(id, 'd-chat-box');
            });
        }

        function sendMsg(type) {
            const m = document.getElementById(type+'-msg').value; if(!m) return;
            db.ref('rides/'+rId+'/chat').push({ s: auth.currentUser.displayName, t: m });
            document.getElementById(type+'-msg').value = "";
        }

        function syncChat(id, boxId) {
            db.ref('rides/'+id+'/chat').on('value', snap => {
                const b = document.getElementById(boxId); b.innerHTML = "";
                snap.forEach(m => {
                    const d = m.val();
                    const me = d.s === auth.currentUser.displayName;
                    b.innerHTML += `<div class="chat-msg ${me ? 'msg-me':'msg-them'}">${d.t}</div>`;
                });
                b.scrollTop = b.scrollHeight;
            });
        }

        db.ref('settings/petrol').on('value', snap => { pet = snap.val() || 280; });
        function adminTap() { tapCount = (window.tapCount || 0) + 1; window.tapCount = tapCount; if(tapCount === 4) { let k = prompt("KEY:"); if(k === "gb46") alert("ADMIN UNLOCKED"); window.tapCount = 0; } }
    </script>
</body>
</html>
