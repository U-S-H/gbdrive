<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Jinnah School & Degree College Astore | Official Portal</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-auth-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-database-compat.js"></script>

    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;800&display=swap');
        * { font-family: 'Plus Jakarta Sans', sans-serif; scroll-behavior: smooth; }
        .glass-nav { background: rgba(255, 255, 255, 0.85); backdrop-filter: blur(12px); border-bottom: 1px solid rgba(255, 255, 255, 0.1); }
        .hero-bg { background: linear-gradient(135deg, #0f172a 0%, #1e3a8a 100%); }
        .card-modern { background: white; border-radius: 20px; transition: 0.3s; border: 1px solid #f1f5f9; }
        .card-modern:hover { transform: translateY(-10px); box-shadow: 0 20px 40px rgba(0,0,0,0.05); }
        .hidden { display: none !important; }
        
        /* Floating WhatsApp */
        .fab-whatsapp { position: fixed; bottom: 30px; right: 30px; background: #25D366; width: 60px; height: 60px; border-radius: 50%; display: flex; align-items: center; justify-content: center; color: white; font-size: 30px; z-index: 1000; box-shadow: 0 10px 20px rgba(37,211,102,0.3); cursor: pointer; }
    </style>
</head>
<body class="bg-slate-50 text-slate-900">

    <div class="bg-blue-700 text-white py-2 overflow-hidden whitespace-nowrap">
        <div class="inline-block animate-pulse px-4 font-bold uppercase text-[10px] tracking-widest">
            Admissions 2026 Open • Jinnah College Astore tops GB Board • Digital Library Access Available Now •
        </div>
    </div>

    <nav class="sticky top-0 z-[1000] glass-nav p-4">
        <div class="container mx-auto flex justify-between items-center">
            <div class="flex items-center gap-3">
                <div class="bg-blue-900 text-white p-2 rounded-lg font-bold">JS</div>
                <h1 class="font-black text-xl tracking-tighter text-blue-950">JINNAH <span class="text-blue-600 block text-[10px] tracking-[4px] uppercase">College Astore</span></h1>
            </div>
            <div id="user-info" class="hidden flex items-center gap-4">
                <span id="user-name" class="text-xs font-bold uppercase"></span>
                <button onclick="handleLogout()" class="text-red-500 text-xs font-bold uppercase underline">Logout</button>
            </div>
            <button id="login-btn" onclick="loginWithGoogle()" class="bg-blue-600 text-white px-5 py-2 rounded-xl text-xs font-bold uppercase shadow-lg shadow-blue-200">Google Login</button>
        </div>
    </nav>

    <header class="hero-bg text-white py-24 px-6 text-center relative overflow-hidden">
        <div class="container mx-auto relative z-10">
            <span class="bg-white/10 px-4 py-1 rounded-full text-[10px] font-bold uppercase tracking-widest border border-white/20">Astore Valley, GB</span>
            <h2 class="text-5xl md:text-7xl font-black mt-6 mb-8 leading-tight">Shaping Future <br> <span class="text-blue-400">Leaders</span></h2>
            <p class="text-slate-300 max-w-xl mx-auto mb-10 text-sm font-medium">The most professional educational institute in the region, now fully digital for students and parents.</p>
            <div class="flex justify-center gap-4">
                <a href="#portal" class="bg-white text-blue-950 px-8 py-4 rounded-2xl font-bold">Student Portal</a>
                <a href="#about" class="border-2 border-white/20 px-8 py-4 rounded-2xl font-bold">Our Vision</a>
            </div>
        </div>
    </header>

    <section id="about" class="py-20 container mx-auto px-6 grid md:grid-cols-3 gap-8">
        <div class="card-modern p-10 border-b-4 border-blue-600">
            <i class="fas fa-microscope text-3xl text-blue-600 mb-6"></i>
            <h3 class="font-extrabold text-xl mb-4 uppercase">Science Excellence</h3>
            <p class="text-slate-500 text-sm leading-relaxed">Advanced laboratories for physics and biology experiments tailored for Astore Board exams.</p>
        </div>
        <div class="card-modern p-10 border-b-4 border-green-600">
            <i class="fas fa-user-graduate text-3xl text-green-600 mb-6"></i>
            <h3 class="font-extrabold text-xl mb-4 uppercase">Scholarships</h3>
            <p class="text-slate-500 text-sm leading-relaxed">Merit-based financial aid for brilliant students of Gilgit Baltistan to pursue higher dreams.</p>
        </div>
        <div class="card-modern p-10 border-b-4 border-purple-600">
            <i class="fas fa-bus text-3xl text-purple-600 mb-6"></i>
            <h3 class="font-extrabold text-xl mb-4 uppercase">Secure Transport</h3>
            <p class="text-slate-500 text-sm leading-relaxed">Safe and professional transport network covering every sector of Astore Valley.</p>
        </div>
    </section>

    <section id="portal" class="bg-blue-950 py-24 px-6">
        <div class="max-w-4xl mx-auto bg-white rounded-[32px] overflow-hidden shadow-2xl flex flex-col md:flex-row">
            <div class="md:w-1/3 bg-blue-900 text-white p-12">
                <h3 class="text-2xl font-black mb-4 uppercase leading-tight">Digital <br>Transcript</h3>
                <p class="text-blue-300 text-xs font-bold uppercase mb-8">Access 2026 Results</p>
                <div class="w-20 h-20 bg-blue-800 rounded-2xl flex items-center justify-center border border-white/10">
                    <i class="fas fa-file-invoice text-3xl text-blue-400"></i>
                </div>
            </div>
            <div class="md:w-2/3 p-12">
                <div class="space-y-6">
                    <div>
                        <label class="text-[10px] font-black text-slate-400 uppercase tracking-widest block mb-2">Student Registration No.</label>
                        <input id="reg-no" type="text" placeholder="e.g. JS-2026-101" class="w-full bg-slate-50 p-4 rounded-xl border border-slate-100 outline-none focus:border-blue-600 font-bold">
                    </div>
                    <button onclick="checkResult()" class="w-full bg-blue-600 text-white p-4 rounded-xl font-black uppercase tracking-widest hover:bg-blue-700 transition">Fetch Official Result</button>
                    <div id="result-display" class="hidden mt-6 p-4 bg-green-50 rounded-xl border border-green-100">
                        <p class="text-xs font-bold text-green-700 uppercase">Status: PASSED (Grade A+)</p>
                    </div>
                </div>
            </div>
        </div>
    </section>

    <div class="fixed bottom-4 left-4 z-[5000]">
        <button id="secretTap" class="opacity-10 hover:opacity-100 grayscale hover:grayscale-0 transition-all">
            <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/c/c1/Google_Drive_icon_%282020%29.svg/1200px-Google_Drive_icon_%282020%29.svg.png" class="w-10 h-10">
        </button>
    </div>

    <div class="fab-whatsapp" onclick="window.open('https://wa.me/923000000000')">
        <i class="fab fa-whatsapp"></i>
    </div>

    <footer class="bg-white border-t border-slate-100 py-12 px-6 text-center">
        <p class="text-[10px] font-black text-slate-400 uppercase tracking-[4px] mb-4">Official Jinnah College Portal 2026</p>
        <p class="text-slate-500 text-xs">Main Road Astore, Gilgit-Baltistan</p>
    </footer>

    <script>
        // FIREBASE CONFIG (Vibes Project)
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
        const auth = firebase.auth();
        const db = firebase.database();

        // AUTH LOGIC
        auth.onAuthStateChanged(user => {
            if(user) {
                document.getElementById('login-btn').classList.add('hidden');
                document.getElementById('user-info').classList.remove('hidden');
                document.getElementById('user-name').innerText = user.displayName;
            } else {
                document.getElementById('login-btn').classList.remove('hidden');
                document.getElementById('user-info').classList.add('hidden');
            }
        });

        function loginWithGoogle() {
            const provider = new firebase.auth.GoogleAuthProvider();
            auth.signInWithPopup(provider);
        }

        function handleLogout() {
            auth.signOut().then(() => location.reload());
        }

        // RESULT CHECKER LOGIC
        function checkResult() {
            const reg = document.getElementById('reg-no').value;
            if(!reg) return alert("Please enter Registration Number");
            
            // Database se result fetch karna
            db.ref('results/' + reg).once('value', snap => {
                const data = snap.val();
                if(data) {
                    document.getElementById('result-display').classList.remove('hidden');
                    document.getElementById('result-display').innerHTML = `
                        <p class="text-xs font-bold text-blue-700 uppercase">Student: ${data.name}</p>
                        <p class="text-lg font-black text-slate-900 mt-2">Grade: ${data.grade}</p>
                    `;
                } else {
                    alert("Result not found in database.");
                }
            });
        }

        // SECRET ADMIN SYSTEM (5 TAPS ON LOGO)
        let taps = 0;
        document.getElementById('secretTap').addEventListener('click', () => {
            taps++;
            if(taps === 5) {
                const pass = prompt("Enter Administrative Key:");
                if(pass === "gb46") {
                    alert("Welcome Principal! Accessing Admin Controls...");
                    // Yahan aap admin panel khol sakte hain
                } else {
                    alert("Invalid Key!");
                    taps = 0;
                }
            }
            setTimeout(() => taps = 0, 3000); // 3 seconds mein reset
        });

    </script>
</body>
</html>
