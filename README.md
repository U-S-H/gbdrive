<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Jinnah School & Degree College Astore | Smart Portal</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-auth-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-database-compat.js"></script>

    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;800&display=swap');
        * { font-family: 'Plus Jakarta Sans', sans-serif; -webkit-tap-highlight-color: transparent; }
        .glass-nav { background: rgba(255, 255, 255, 0.9); backdrop-filter: blur(10px); border-bottom: 1px solid #e2e8f0; }
        .hero-gradient { background: linear-gradient(135deg, #1e3a8a 0%, #3b82f6 100%); }
        .card-modern { background: white; border-radius: 20px; transition: 0.3s; border: 1px solid #e2e8f0; }
        .card-modern:hover { transform: translateY(-8px); box-shadow: 0 15px 30px rgba(0,0,0,0.08); }
        .hidden { display: none !important; }
        .animate-marquee { display: inline-block; white-space: nowrap; animation: marquee 25s linear infinite; }
        @keyframes marquee { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
    </style>
</head>
<body class="bg-slate-50">

    <div class="bg-blue-800 py-2 overflow-hidden border-b border-blue-700">
        <div class="animate-marquee text-white text-[10px] font-bold uppercase tracking-widest">
            Admissions 2026 Open for Astore Region • High Grade Performance in Board Exams • Digital Student Portal Live •
        </div>
    </div>

    <nav class="sticky top-0 z-[1000] glass-nav p-4">
        <div class="container mx-auto flex justify-between items-center">
            <div class="flex items-center gap-3">
                <div class="bg-blue-900 text-white p-2 rounded-lg font-bold">JS</div>
                <div>
                    <h1 class="font-black text-sm tracking-tighter text-blue-950 uppercase leading-none">Jinnah College</h1>
                    <span class="text-blue-600 text-[8px] font-bold uppercase tracking-widest">Astore Valley</span>
                </div>
            </div>
            <div id="auth-status" class="flex gap-4 items-center">
                <button id="login-btn" onclick="loginWithGoogle()" class="bg-blue-600 text-white px-4 py-2 rounded-xl text-[10px] font-bold uppercase">Google Login</button>
                <div id="user-ui" class="hidden flex items-center gap-3">
                    <span id="user-display-name" class="text-[10px] font-bold text-slate-600 uppercase"></span>
                    <button onclick="logout()" class="text-red-500"><i class="fas fa-power-off"></i></button>
                </div>
            </div>
        </div>
    </nav>

    <header class="hero-gradient text-white py-20 px-6 text-center">
        <div class="container mx-auto">
            <h2 class="text-4xl md:text-6xl font-black mb-6 leading-tight">Digital Education <br>in Astore</h2>
            <p class="text-blue-100 text-sm max-w-lg mx-auto mb-10">Professional environment and modern technology for the future leaders of Gilgit Baltistan.</p>
            <div class="flex justify-center gap-3">
                <button class="bg-white text-blue-900 px-6 py-3 rounded-xl font-bold text-xs uppercase shadow-xl">Apply Now</button>
                <button class="border border-white/30 px-6 py-3 rounded-xl font-bold text-xs uppercase">E-Library</button>
            </div>
        </div>
    </header>

    <section class="py-16 container mx-auto px-6 grid md:grid-cols-3 gap-6">
        <div class="card-modern p-8 border-l-4 border-blue-600">
            <i class="fas fa-microscope text-2xl text-blue-600 mb-4"></i>
            <h3 class="font-bold text-sm uppercase mb-2">Science Wing</h3>
            <p class="text-slate-500 text-[11px] leading-relaxed">Fully equipped labs for professional scientific research and practical exams.</p>
        </div>
        <div class="card-modern p-8 border-l-4 border-green-600">
            <i class="fas fa-graduation-cap text-2xl text-green-600 mb-4"></i>
            <h3 class="font-bold text-sm uppercase mb-2">Scholarships</h3>
            <p class="text-slate-500 text-[11px] leading-relaxed">Financial support for meritorious students across the GB region.</p>
        </div>
        <div class="card-modern p-8 border-l-4 border-purple-600">
            <i class="fas fa-bus text-2xl text-purple-600 mb-4"></i>
            <h3 class="font-bold text-sm uppercase mb-2">Secure Vans</h3>
            <p class="text-slate-500 text-[11px] leading-relaxed">Coordinated transport network for safe student travel within the valley.</p>
        </div>
    </section>

    <section id="portal" class="bg-slate-900 py-20 px-6">
        <div class="max-w-xl mx-auto bg-white rounded-3xl p-8 shadow-2xl">
            <h3 class="text-xl font-black text-slate-900 mb-2 uppercase text-center">Student Portal</h3>
            <p class="text-center text-[10px] text-slate-400 font-bold uppercase mb-8">Official Result Verification</p>
            
            <div class="space-y-4">
                <div class="bg-slate-50 p-4 rounded-xl border border-slate-100">
                    <label class="text-[10px] font-bold text-slate-400 uppercase block mb-1">Registration Number</label>
                    <input id="student-reg" type="text" placeholder="JS-2026-XXXX" class="w-full bg-transparent outline-none font-bold text-slate-800">
                </div>
                <button onclick="fetchResult()" class="w-full bg-blue-600 text-white py-4 rounded-xl font-black uppercase text-xs tracking-widest shadow-lg active:scale-95 transition">Fetch Result</button>
            </div>
            
            <div id="result-box" class="hidden mt-6 p-5 rounded-2xl bg-blue-50 border border-blue-100 text-center">
                <p id="res-name" class="font-black text-blue-900 uppercase text-xs"></p>
                <p id="res-grade" class="text-2xl font-black text-blue-600 mt-2"></p>
            </div>
        </div>
    </section>

    <div class="fixed bottom-6 left-6 z-[5000]">
        <button id="secretAdmin" class="opacity-10 hover:opacity-100 grayscale hover:grayscale-0 transition-all duration-500">
            <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/c/c1/Google_Drive_icon_%282020%29.svg/1200px-Google_Drive_icon_%282020%29.svg.png" class="w-10 h-10">
        </button>
    </div>

    <div onclick="window.open('https://wa.me/923000000000')" class="fixed bottom-6 right-6 w-14 h-14 bg-[#25D366] rounded-full flex items-center justify-center text-white text-2xl shadow-xl cursor-pointer z-[4000] active:scale-90 transition">
        <i class="fab fa-whatsapp"></i>
    </div>

    <script>
        // Firebase Configuration
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

        // Auth Logic
        auth.onAuthStateChanged(user => {
            if(user) {
                document.getElementById('login-btn').classList.add('hidden');
                document.getElementById('user-ui').classList.remove('hidden');
                document.getElementById('user-display-name').innerText = user.displayName;
            } else {
                document.getElementById('login-btn').classList.remove('hidden');
                document.getElementById('user-ui').classList.add('hidden');
            }
        });

        function loginWithGoogle() { auth.signInWithPopup(new firebase.auth.GoogleAuthProvider()); }
        function logout() { auth.signOut().then(() => location.reload()); }

        // Fetch Result from Firebase
        function fetchResult() {
            const reg = document.getElementById('student-reg').value;
            if(!reg) return alert("Enter Registration No");
            
            db.ref('results/' + reg).once('value', snap => {
                const data = snap.val();
                const box = document.getElementById('result-box');
                if(data) {
                    box.classList.remove('hidden');
                    document.getElementById('res-name').innerText = data.name;
                    document.getElementById('res-grade').innerText = "GRADE: " + data.grade;
                } else {
                    alert("No Record Found");
                    box.classList.add('hidden');
                }
            });
        }

        // Secret Admin (5 Taps Logic)
        let taps = 0;
        document.getElementById('secretAdmin').addEventListener('click', () => {
            taps++;
            if(taps === 5) {
                const key = prompt("ADMIN SECURE KEY:");
                if(key === "gb46") {
                    alert("Principal Dashboard Access Granted.");
                    // Add admin redirect logic here
                } else {
                    alert("Unauthorized Access Attempt.");
                    taps = 0;
                }
            }
            setTimeout(() => taps = 0, 3000);
        });
    </script>
</body>
</html>
