<html lang="ur">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>GB Drive - The Complete App</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-database-compat.js"></script>

    <style>
        @import url('https://fonts.googleapis.com/css2?family=Outfit:wght@300;600;900&display=swap');
        body { font-family: 'Outfit', sans-serif; background: #f0f4f8; overflow-x: hidden; }
        .hidden { display: none; }
        .glass { background: rgba(255, 255, 255, 0.95); backdrop-filter: blur(12px); border-bottom: 1px solid #e2e8f0; }
        .neo-card { background: white; border-radius: 2.5rem; box-shadow: 0 15px 35px -5px rgba(0,0,0,0.05); }
        .active-tab { background: #1e40af; color: white; box-shadow: 0 10px 20px rgba(30, 64, 175, 0.2); }
        .btn-press { transition: all 0.2s; }
        .btn-press:active { transform: scale(0.95); }
    </style>
</head>
<body class="pb-28">

    <header class="p-4 flex justify-between items-center sticky top-0 z-[100] glass">
        <div onclick="location.reload()" class="flex items-center gap-2 cursor-pointer">
            <div class="bg-blue-600 text-white p-2 rounded-xl font-black italic shadow-lg">GB</div>
            <p class="font-black text-blue-900 text-lg leading-none">DRIVE</p>
        </div>
        <div class="flex items-center gap-3">
            <button onclick="triggerSOS()" class="bg-red-50 text-red-500 p-2 rounded-full border border-red-100"><i data-lucide="shield-alert" class="w-5 h-5"></i></button>
            <img src="https://api.dicebear.com/7.x/avataaars/svg?seed=Sweetie" class="w-10 h-10 rounded-full border-2 border-white shadow-md">
        </div>
    </header>

    <main class="max-w-md mx-auto p-4 space-y-6">

        <div class="flex bg-gray-200/50 p-1.5 rounded-2xl gap-2">
            <button onclick="switchTab('p')" id="tab-p" class="flex-1 py-3 rounded-xl font-black text-sm active-tab transition-all">PASSENGER</button>
            <button onclick="switchTab('d')" id="tab-d" class="flex-1 py-3 rounded-xl font-black text-sm text-gray-500 transition-all">DRIVER</button>
        </div>

        <div id="p-panel" class="space-y-4">
            <div class="h-32 bg-blue-600 rounded-[2rem] p-6 text-white relative overflow-hidden shadow-xl">
                <div class="relative z-10">
                    <h2 class="text-xl font-black">Safar Mubarak!</h2>
                    <p class="text-xs opacity-80">GB ki sabse sasti aur fast ride.</p>
                </div>
                <i data-lucide="map" class="absolute -right-4 -bottom-4 w-32 h-32 opacity-10"></i>
            </div>

            <div class="neo-card p-6 space-y-4 border-b-8 border-blue-600">
                <div class="space-y-3">
                    <div class="flex items-center gap-3 bg-gray-50 p-4 rounded-2xl">
                        <i data-lucide="map-pin" class="text-green-500 w-5"></i>
                        <input id="p-from" type="text" placeholder="Kahan se? (Location)" class="bg-transparent w-full outline-none font-bold text-sm">
                    </div>
                    <div class="flex items-center gap-3 bg-gray-50 p-4 rounded-2xl">
                        <i data-lucide="navigation" class="text-blue-600 w-5"></i>
                        <input id="p-to" type="text" placeholder="Kahan tak? (Destination)" class="bg-transparent w-full outline-none font-bold text-sm">
                    </div>
                </div>

                <div class="bg-blue-600 p-6 rounded-[2rem] text-white text-center shadow-lg">
                    <p class="text-[9px] font-black opacity-60 mb-1 tracking-widest">APNA KIRAYA OFFER KAREIN</p>
                    <div class="flex items-center justify-between px-4">
                        <button onclick="changeFare(-50)" class="text-2xl font-bold w-10 h-10 bg-white/20 rounded-full btn-press">-</button>
                        <div>
                            <span id="fare-val" class="text-4xl font-black">350</span>
                            <span class="text-xs font-bold block opacity-70 leading-none">PKR</span>
                        </div>
                        <button onclick="changeFare(50)" class="text-2xl font-bold w-10 h-10 bg-white/20 rounded-full btn-press">+</button>
                    </div>
                </div>

                <button onclick="requestRide()" class="w-full bg-blue-900 text-white py-5 rounded-[2rem] font-black text-xl shadow-2xl btn-press">FIND DRIVER</button>
            </div>
        </div>

        <div id="d-panel" class="hidden space-y-4">
            <div class="grid grid-cols-2 gap-4">
                <div class="bg-white p-5 rounded-3xl border-l-4 border-green-500 shadow-sm">
                    <p class="text-[10px] text-gray-400 font-black">EARNINGS</p>
                    <p class="text-2xl font-black">Rs 5,240</p>
                </div>
                <div class="bg-white p-5 rounded-3xl border-l-4 border-orange-400 shadow-sm">
                    <p class="text-[10px] text-gray-400 font-black">RATING</p>
                    <p class="text-2xl font-black flex items-center gap-1">4.9 <i data-lucide="star" class="w-4 h-4 fill-orange-400 text-orange-400"></i></p>
                </div>
            </div>

            <div class="flex justify-between items-center px-2">
                <h2 class="font-black text-xl">Live Requests</h2>
                <div class="flex items-center gap-1"><span class="w-2 h-2 bg-green-500 rounded-full animate-pulse"></span><span class="text-[10px] font-bold text-gray-500 uppercase">Searching...</span></div>
            </div>

            <div id="ride-requests" class="space-y-4 pb-10">
                <div class="text-center py-20 text-gray-400">Rides load ho rahi hain sweetie...</div>
            </div>
        </div>

    </main>

    <nav class="fixed bottom-0 left-0 right-0 glass p-5 flex justify-around items-center z-[100] rounded-t-[3rem] shadow-lg">
        <button onclick="switchTab('p')" class="text-blue-600 flex flex-col items-center gap-1"><i data-lucide="home"></i><span class="text-[8px] font-bold">HOME</span></button>
        <button onclick="alert('History coming soon!')" class="text-gray-300 flex flex-col items-center gap-1"><i data-lucide="calendar"></i><span class="text-[8px] font-bold">RIDES</span></button>
        <button onclick="alert('Wallet Balance: 1250 PKR')" class="text-gray-300 flex flex-col items-center gap-1"><i data-lucide="wallet"></i><span class="text-[8px] font-bold">WALLET</span></button>
        <button onclick="switchTab('d')" class="text-gray-300 flex flex-col items-center gap-1"><i data-lucide="car"></i><span class="text-[8px] font-bold">DRIVER</span></button>
    </nav>

    <script>
        lucide.createIcons();
        let fare = 350;

        // Firebase Config (Aapki Config)
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
        const db = firebase.database();

        function switchTab(mode) {
            const isDriver = mode === 'd';
            document.getElementById('p-panel').classList.toggle('hidden', isDriver);
            document.getElementById('d-panel').classList.toggle('hidden', !isDriver);
            document.getElementById('tab-p').className = !isDriver ? 'flex-1 py-3 rounded-xl font-black text-sm active-tab' : 'flex-1 py-3 rounded-xl font-black text-sm text-gray-500';
            document.getElementById('tab-d').className = isDriver ? 'flex-1 py-3 rounded-xl font-black text-sm active-tab' : 'flex-1 py-3 rounded-xl font-black text-sm text-gray-500';
        }

        function changeFare(amt) {
            fare += amt;
            if(fare < 150) fare = 150;
            document.getElementById('fare-val').innerText = fare;
        }

        // PASSENGER: Request Ride
        function requestRide() {
            const from = document.getElementById('p-from').value;
            const to = document.getElementById('p-to').value;

            if(!from || !to) {
                alert("Location toh likho sweetie! 😘");
                return;
            }

            const rideId = Date.now();
            db.ref('rides/' + rideId).set({
                pickup: from,
                dropoff: to,
                fare: fare,
                status: 'pending',
                time: new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' })
            }).then(() => {
                alert("Ride Request Live! Driver ka intezar karein. 🔥");
            });
        }

        // DRIVER: Listen for Rides
        db.ref('rides').on('value', (snapshot) => {
            const container = document.getElementById('ride-requests');
            container.innerHTML = "";
            const data = snapshot.val();

            if(!data) {
                container.innerHTML = '<div class="text-center py-20 text-gray-400 font-bold">Abhi koi ride nahi hai... Intezar karein!</div>';
                return;
            }

            Object.keys(data).reverse().forEach(id => {
                const ride = data[id];
                container.innerHTML += `
                    <div class="bg-white p-6 rounded-[2.5rem] shadow-md border-l-8 border-blue-600 animate-in slide-in-from-right duration-300">
                        <div class="flex justify-between mb-4">
                            <span class="text-[10px] font-black text-blue-600 bg-blue-50 px-2 py-1 rounded-full uppercase">Pending Request</span>
                            <span class="text-[10px] font-bold text-gray-400">${ride.time}</span>
                        </div>
                        <div class="space-y-2">
                            <div class="flex items-center gap-2"><i data-lucide="circle" class="w-2 h-2 text-green-500"></i><p class="font-bold text-sm">${ride.pickup}</p></div>
                            <div class="flex items-center gap-2"><i data-lucide="map-pin" class="w-2 h-2 text-blue-600"></i><p class="font-bold text-sm">${ride.dropoff}</p></div>
                        </div>
                        <div class="mt-6 flex items-center justify-between">
                            <div><p class="text-[8px] font-black text-gray-400 uppercase">Offered Fare</p><p class="text-2xl font-black text-green-600">Rs ${ride.fare}</p></div>
                            <button onclick="acceptRide('${id}')" class="bg-gray-900 text-white px-8 py-3 rounded-2xl font-black text-xs btn-press">ACCEPT</button>
                        </div>
                    </div>
                `;
                lucide.createIcons();
            });
        });

        function acceptRide(id) {
            alert("Safar Shuru! Passenger ko call karein. 🚗");
            db.ref('rides/' + id).remove(); // Ride khatam (real app mein status change hota hai)
        }

        function triggerSOS() {
            alert("EMERGENCY! Aapki location GB Police ko bhej di gayi hai.");
        }
    </script>
</body>
</html>
