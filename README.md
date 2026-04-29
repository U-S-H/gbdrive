<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>GB DRIVE - INDRIVE EDITION</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-auth-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-database-compat.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Outfit:wght@400;900&display=swap');
        body { font-family: 'Outfit', sans-serif; background: #f4f7f6; }
        .hidden { display: none !important; }
        .neo-card { background: white; border-radius: 1.5rem; box-shadow: 0 4px 20px rgba(0,0,0,0.05); padding: 1.5rem; }
        .indrive-yellow { background: #bfff00; } /* inDrive signature color */
        .btn-primary { background: #bfff00; color: black; font-weight: 900; border-radius: 1rem; padding: 1rem; transition: 0.2s; }
        .btn-primary:active { transform: scale(0.98); }
        input { border: 1px solid #ddd !important; border-radius: 0.8rem !important; padding: 0.8rem !important; width: 100%; margin-bottom: 1rem; font-weight: 600; }
    </style>
</head>
<body class="pb-24">

    <div id="login-screen" class="min-h-screen flex flex-col items-center justify-center p-6 text-center">
        <div onclick="adminTap()" class="bg-black text-white w-20 h-20 flex items-center justify-center rounded-3xl font-black text-4xl italic mb-6 cursor-pointer">GB</div>
        <h1 class="text-4xl font-black text-black">GB DRIVE</h1>
        <p class="text-gray-500 font-bold mt-2">YOUR PRICE, YOUR RULES</p>
        <button onclick="login()" class="mt-10 w-full max-w-xs bg-white py-4 rounded-2xl shadow-lg flex items-center justify-center gap-3 font-black border-2 border-black active:scale-95 transition-all">
            <img src="https://www.gstatic.com/firebasejs/ui/2.0.0/images/auth/google.svg" width="20"> GOOGLE LOGIN
        </button>
    </div>

    <div id="main-app" class="hidden">
        <header class="p-4 flex justify-between items-center bg-white border-b sticky top-0 z-50">
            <div class="flex items-center gap-3">
                <div onclick="adminTap()" class="bg-black text-white w-8 h-8 flex items-center justify-center rounded-lg font-black italic cursor-pointer text-xs">GB</div>
                <p id="u-name" class="font-black text-sm"></p>
            </div>
            <button onclick="logout()" class="text-red-500"><i data-lucide="log-out"></i></button>
        </header>

        <main class="p-4 max-w-md mx-auto space-y-6">
            
            <div id="role-screen" class="hidden py-10 space-y-4 text-center">
                <h2 class="text-2xl font-black">CHOOSE MODE</h2>
                <button onclick="setRole('passenger')" class="w-full p-6 neo-card font-black text-xl hover:bg-lime-50">PASSENGER</button>
                <button onclick="setRole('driver')" class="w-full p-6 neo-card font-black text-xl border-2 border-black">DRIVER</button>
            </div>

            <div id="p-ui" class="hidden space-y-4">
                <div class="neo-card">
                    <input id="p-from" type="text" placeholder="Pickup location">
                    <input id="p-to" type="text" placeholder="Destination">
                    <label class="text-[10px] font-black opacity-50">OFFER YOUR FARE (PKR)</label>
                    <input id="p-fare" type="number" value="300" class="text-2xl text-center text-green-600">
                    
                    <button onclick="requestRide()" class="w-full btn-primary text-xl mt-2">FIND A DRIVER</button>
                </div>
                <div id="p-offers" class="space-y-3">
                    <h3 class="font-black text-sm opacity-50">INCOMING OFFERS</h3>
                    </div>
            </div>

            <div id="d-ui" class="hidden space-y-4">
                <h3 class="font-black text-xl">AVAILABLE RIDES</h3>
                <div id="d-feed" class="space-y-4"></div>
            </div>

            <div id="admin-ui" class="hidden space-y-4">
                <div class="bg-black p-6 rounded-3xl text-white">
                    <h2 class="text-xl font-black mb-2 italic uppercase">Admin Control</h2>
                    <p class="text-[10px] opacity-50">MANAGE ALL SYSTEM RIDES</p>
                </div>
                <div id="admin-feed" class="space-y-3"></div>
            </div>

        </main>
    </div>

    <script>
        lucide.createIcons();
        let tapCount = 0;
        let userRole = '';

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

        function login() { auth.signInWithPopup(provider); }
        function logout() { auth.signOut(); location.reload(); }

        function adminTap() {
            tapCount++;
            if(tapCount === 4) {
                tapCount = 0;
                let key = prompt("ENTER SECRET KEY:");
                if(key === "gb46") showView('admin');
            }
        }

        auth.onAuthStateChanged(user => {
            if(user) {
                document.getElementById('login-screen').classList.add('hidden');
                document.getElementById('main-app').classList.remove('hidden');
                document.getElementById('u-name').innerText = user.displayName.toUpperCase();
                db.ref('users/' + user.uid).once('value').then(snap => {
                    if(snap.exists()) {
                        userRole = snap.val().role;
                        showView(userRole);
                    } else {
                        document.getElementById('role-screen').classList.remove('hidden');
                    }
                });
            } else {
                document.getElementById('login-screen').classList.remove('hidden');
            }
        });

        function setRole(role) {
            db.ref('users/' + auth.currentUser.uid).set({ role, name: auth.currentUser.displayName }).then(() => location.reload());
        }

        function showView(v) {
            ['p-ui', 'd-ui', 'admin-ui', 'role-screen'].forEach(id => document.getElementById(id).classList.add('hidden'));
            if(v === 'passenger') {
                document.getElementById('p-ui').classList.remove('hidden');
                listenForOffers();
            } else if(v === 'driver') {
                document.getElementById('d-ui').classList.remove('hidden');
                syncRides('d-feed');
            } else if(v === 'admin') {
                document.getElementById('admin-ui').classList.remove('hidden');
                syncRides('admin-feed', true);
            }
        }

        function requestRide() {
            const f = document.getElementById('p-from').value;
            const t = document.getElementById('p-to').value;
            const fare = document.getElementById('p-fare').value;
            if(!f || !t) return alert("Enter Locations!");

            const rideRef = db.ref('rides').push();
            rideRef.set({
                from: f, to: t, fare: fare, 
                user: auth.currentUser.displayName,
                uid: auth.currentUser.uid,
                status: 'pending'
            });
            alert("RIDE REQUESTED AT PKR " + fare);
        }

        function syncRides(id, isAdmin = false) {
            db.ref('rides').on('value', snap => {
                const f = document.getElementById(id); f.innerHTML = "";
                snap.forEach(c => {
                    const r = c.val();
                    if(r.status === 'pending' || isAdmin) {
                        f.innerHTML += `
                        <div class="neo-card border-l-4 ${isAdmin ? 'border-red-500':'border-lime-500'} mb-3">
                            <p class="font-black text-sm">${r.from} ➔ ${r.to}</p>
                            <div class="mt-4 flex justify-between items-center">
                                <span class="font-black text-lg">PKR ${r.fare}</span>
                                ${isAdmin ? `<button onclick="deleteRide('${c.key}')" class="bg-red-500 text-white px-4 py-2 rounded-lg text-[10px] font-black">DELETE</button>` : 
                                `<button onclick="makeOffer('${c.key}', ${r.fare})" class="bg-black text-white px-4 py-2 rounded-lg text-[10px] font-black uppercase tracking-widest">Accept / Counter</button>`}
                            </div>
                        </div>`;
                    }
                });
            });
        }

        function makeOffer(rideId, passengerFare) {
            let driverFare = prompt("Accept PKR " + passengerFare + " or enter your counter offer:", passengerFare);
            if(driverFare) {
                db.ref('rides/' + rideId + '/offers').push({
                    driverName: auth.currentUser.displayName,
                    driverUid: auth.currentUser.uid,
                    fare: driverFare,
                    rating: '4.9 ⭐'
                });
                alert("OFFER SENT!");
            }
        }

        function listenForOffers() {
            db.ref('rides').on('value', snap => {
                const container = document.getElementById('p-offers');
                container.innerHTML = `<h3 class="font-black text-sm opacity-50 uppercase">Incoming Driver Offers</h3>`;
                snap.forEach(rideSnap => {
                    const ride = rideSnap.val();
                    if(ride.uid === auth.currentUser.uid && ride.offers) {
                        Object.keys(ride.offers).forEach(offerId => {
                            const offer = ride.offers[offerId];
                            container.innerHTML += `
                            <div class="neo-card flex justify-between items-center border-2 border-lime-400">
                                <div>
                                    <p class="font-black text-sm">${offer.driverName}</p>
                                    <p class="text-[10px] font-bold text-gray-400">${offer.rating}</p>
                                </div>
                                <div class="text-right">
                                    <p class="font-black text-xl text-green-600">Rs ${offer.fare}</p>
                                    <button onclick="acceptOffer('${rideSnap.key}')" class="bg-black text-white px-4 py-1 rounded-lg text-[10px] font-black mt-1">ACCEPT</button>
                                </div>
                            </div>`;
                        });
                    }
                });
            });
        }

        function acceptOffer(rideId) {
            db.ref('rides/' + rideId).update({ status: 'accepted' });
            alert("DRIVER IS ON THE WAY! 🚗");
        }

        function deleteRide(id) { db.ref('rides/' + id).remove(); }
    </script>
</body>
</html>
