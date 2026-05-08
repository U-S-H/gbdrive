<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Jinnah School & Degree College Astore | Smart Portal</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;600;700&display=swap');
        
        body { font-family: 'Poppins', sans-serif; background-color: #f1f5f9; scroll-behavior: smooth; }

        .glass-nav {
            background: rgba(255, 255, 255, 0.85);
            backdrop-filter: blur(12px);
            border-bottom: 1px solid rgba(255, 255, 255, 0.3);
        }

        .hero-section {
            background: linear-gradient(135deg, #0f172a 0%, #1e3a8a 100%);
            min-height: 80vh;
            display: flex;
            align-items: center;
        }

        .animate-marquee {
            display: inline-block;
            white-space: nowrap;
            animation: marquee 30s linear infinite;
        }

        @keyframes marquee {
            0% { transform: translateX(100%); }
            100% { transform: translateX(-100%); }
        }

        .card-modern {
            background: white;
            border-radius: 24px;
            transition: all 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
            border: 1px solid #e2e8f0;
        }

        .card-modern:hover {
            transform: translateY(-12px);
            box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.15);
        }

        .fab-whatsapp {
            position: fixed;
            bottom: 30px;
            right: 30px;
            background: #25D366;
            width: 65px;
            height: 65px;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            box-shadow: 0 10px 20px rgba(37, 211, 102, 0.4);
            color: white;
            font-size: 32px;
            z-index: 1000;
            cursor: pointer;
            animation: pulse 2s infinite;
        }

        @keyframes pulse {
            0% { transform: scale(1); }
            50% { transform: scale(1.1); }
            100% { transform: scale(1); }
        }

        /* Hidden Admin Icon Style */
        #secretTap {
            transition: all 0.3s ease;
            cursor: pointer;
        }
    </style>
</head>
<body>

    <div class="bg-blue-600 py-2 overflow-hidden border-b border-blue-400">
        <div class="animate-marquee text-white font-medium">
            <span class="mx-10"><i class="fas fa-bullhorn mr-2"></i> Admissions Open for 2026!</span>
            <span class="mx-10"><i class="fas fa-trophy mr-2"></i> Top Position in Astore Board Exams!</span>
            <span class="mx-10"><i class="fas fa-calendar-check mr-2"></i> Parent-Teacher Meeting on Saturday.</span>
        </div>
    </div>

    <nav class="sticky top-0 z-50 glass-nav p-4">
        <div class="container mx-auto flex justify-between items-center">
            <div class="flex items-center gap-2">
                <div class="bg-blue-900 text-white p-2 rounded-lg font-bold">JS</div>
                <h1 class="text-xl font-bold text-blue-950 uppercase tracking-tighter">Jinnah College <span class="text-blue-600 block text-xs tracking-widest uppercase">Astore Valley</span></h1>
            </div>
            <div class="hidden md:flex space-x-8 font-semibold text-gray-700">
                <a href="#" class="hover:text-blue-600">Overview</a>
                <a href="#academics" class="hover:text-blue-600">Academics</a>
                <a href="#portal" class="hover:text-blue-600">Student Portal</a>
                <a href="#contact" class="hover:text-blue-600">Contact</a>
            </div>
            <button class="bg-blue-600 text-white px-6 py-2 rounded-xl font-bold hover:bg-blue-700 shadow-md">Apply 2026</button>
        </div>
    </nav>

    <header class="hero-section text-white px-6">
        <div class="container mx-auto grid md:grid-cols-2 gap-10 items-center">
            <div class="space-y-6">
                <span class="bg-blue-500/20 text-blue-300 px-4 py-1 rounded-full text-sm font-bold uppercase tracking-widest border border-blue-500/30">Since 1998</span>
                <h2 class="text-5xl md:text-7xl font-bold leading-tight">Elevate Your <span class="text-blue-400">Future</span> in Astore</h2>
                <p class="text-lg opacity-80 max-w-md">The most prestigious educational institute in Gilgit-Baltistan, providing excellence in Science, Arts, and Leadership.</p>
                <div class="flex gap-4">
                    <button class="bg-white text-blue-950 px-8 py-4 rounded-2xl font-bold hover:bg-blue-50 transition shadow-xl">Explore Courses</button>
                    <button class="border-2 border-white/30 px-8 py-4 rounded-2xl font-bold hover:bg-white/10 transition">Virtual Tour</button>
                </div>
            </div>
            <div class="hidden md:block">
                <div class="bg-blue-800/30 w-full h-[400px] rounded-[40px] border border-white/10 backdrop-blur-sm flex items-center justify-center">
                    <i class="fas fa-university text-9xl text-white/20"></i>
                </div>
            </div>
        </div>
    </header>

    <section class="py-12 -mt-10 relative z-10">
        <div class="container mx-auto px-6 grid grid-cols-2 md:grid-cols-4 gap-4">
            <div class="bg-white p-6 rounded-3xl shadow-lg text-center border border-gray-100">
                <h3 class="text-3xl font-bold text-blue-600">2.5k+</h3>
                <p class="text-sm font-semibold text-gray-500">Graduates</p>
            </div>
            <div class="bg-white p-6 rounded-3xl shadow-lg text-center border border-gray-100">
                <h3 class="text-3xl font-bold text-blue-600">50+</h3>
                <p class="text-sm font-semibold text-gray-500">Gold Medals</p>
            </div>
            <div class="bg-white p-6 rounded-3xl shadow-lg text-center border border-gray-100">
                <h3 class="text-3xl font-bold text-blue-600">100%</h3>
                <p class="text-sm font-semibold text-gray-500">Secure Campus</p>
            </div>
            <div class="bg-white p-6 rounded-3xl shadow-lg text-center border border-gray-100">
                <h3 class="text-3xl font-bold text-blue-600">A+</h3>
                <p class="text-sm font-semibold text-gray-500">Board Grade</p>
            </div>
        </div>
    </section>

    <section id="academics" class="py-24 container mx-auto px-6">
        <div class="text-center mb-16">
            <h2 class="text-4xl font-bold text-gray-900 mb-4">Why Choose Jinnah College?</h2>
            <div class="h-1 w-20 bg-blue-600 mx-auto rounded-full"></div>
        </div>
        <div class="grid grid-cols-1 md:grid-cols-3 gap-8">
            <div class="card-modern p-10">
                <div class="bg-blue-100 w-16 h-16 rounded-2xl flex items-center justify-center text-blue-600 text-2xl mb-6">
                    <i class="fas fa-microscope"></i>
                </div>
                <h3 class="text-2xl font-bold mb-4">Modern Science Labs</h3>
                <p class="text-gray-600 leading-relaxed">Fully equipped physics, chemistry, and biology labs following international standards.</p>
            </div>
            <div class="card-modern p-10">
                <div class="bg-green-100 w-16 h-16 rounded-2xl flex items-center justify-center text-green-600 text-2xl mb-6">
                    <i class="fas fa-laptop-code"></i>
                </div>
                <h3 class="text-2xl font-bold mb-4">IT Excellence</h3>
                <p class="text-gray-600 leading-relaxed">Dedicated computer suites with high-speed internet for digital learning and programming.</p>
            </div>
            <div class="card-modern p-10">
                <div class="bg-purple-100 w-16 h-16 rounded-2xl flex items-center justify-center text-purple-600 text-2xl mb-6">
                    <i class="fas fa-bus"></i>
                </div>
                <h3 class="text-2xl font-bold mb-4">Safe Transport</h3>
                <p class="text-gray-600 leading-relaxed">A fleet of well-maintained buses covering every corner of the Astore valley for students.</p>
            </div>
        </div>
    </section>

    <section id="portal" class="bg-blue-950 py-24 px-6 relative overflow-hidden">
        <div class="absolute top-0 right-0 w-64 h-64 bg-blue-800 rounded-full blur-3xl opacity-20 -mr-32 -mt-32"></div>
        
        <div class="max-w-5xl mx-auto bg-white/5 backdrop-blur-md rounded-[40px] border border-white/10 p-8 md:p-16">
            <div class="grid md:grid-cols-2 gap-16 items-center">
                <div class="text-white">
                    <h2 class="text-4xl font-bold mb-6">E-Result & Portal</h2>
                    <p class="text-blue-200 mb-8 leading-relaxed">Parents can now check their children's academic performance, attendance, and fee history online with one click.</p>
                    <ul class="space-y-4">
                        <li class="flex items-center gap-3"><i class="fas fa-check-circle text-green-400"></i> Instant Score Updates</li>
                        <li class="flex items-center gap-3"><i class="fas fa-check-circle text-green-400"></i> Fee Status Tracking</li>
                        <li class="flex items-center gap-3"><i class="fas fa-check-circle text-green-400"></i> Online Assignments</li>
                    </ul>
                </div>
                <div class="bg-white p-8 rounded-3xl shadow-2xl">
                    <h4 class="text-blue-950 font-bold text-xl mb-6">Access Student Profile</h4>
                    <form onsubmit="event.preventDefault(); alert('Searching database for results...')">
                        <div class="mb-4">
                            <label class="block text-sm font-bold text-gray-500 mb-2 uppercase">Registration No.</label>
                            <input type="text" placeholder="e.g. JAC-2026-001" class="w-full p-4 bg-gray-50 border border-gray-200 rounded-xl focus:ring-2 focus:ring-blue-500 outline-none">
                        </div>
                        <button class="w-full bg-blue-600 text-white p-4 rounded-xl font-bold hover:bg-blue-700 transition flex items-center justify-center gap-2">
                            <i class="fas fa-search"></i> Check Results
                        </button>
                    </form>
                </div>
            </div>
        </div>
    </section>

    <div class="fixed bottom-4 left-4 z-50">
        <button id="secretTap" class="opacity-10 hover:opacity-100 transform hover:scale-110">
            <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/c/c1/Google_Drive_icon_%282020%29.svg/1200px-Google_Drive_icon_%282020%29.svg.png" class="w-10 h-10" alt="Drive">
        </button>
    </div>

    <div class="fab-whatsapp" onclick="window.open('https://wa.me/923000000000')">
        <i class="fab fa-whatsapp"></i>
    </div>

    <footer id="contact" class="bg-white pt-20 pb-10 px-6 border-t border-gray-100">
        <div class="container mx-auto grid md:grid-cols-3 gap-12 mb-16">
            <div class="space-y-4">
                <h4 class="text-2xl font-bold text-blue-900">Jinnah College</h4>
                <p class="text-gray-500">Leading the educational revolution in Astore since 1998. Join us to shape your destiny.</p>
                <div class="flex gap-4 text-2xl text-gray-400">
                    <i class="fab fa-facebook hover:text-blue-600 cursor-pointer transition"></i>
                    <i class="fab fa-instagram hover:text-pink-500 cursor-pointer transition"></i>
                    <i class="fab fa-twitter hover:text-blue-400 cursor-pointer transition"></i>
                </div>
            </div>
            <div>
                <h4 class="font-bold text-gray-900 mb-6 uppercase tracking-widest text-sm">Quick Links</h4>
                <ul class="space-y-3 text-gray-600">
                    <li class="hover:text-blue-600 cursor-pointer">Admission Forms</li>
                    <li class="hover:text-blue-600 cursor-pointer">Academic Calendar</li>
                    <li class="hover:text-blue-600 cursor-pointer">Scholarship Details</li>
                    <li class="hover:text-blue-600 cursor-pointer">Faculty Profiles</li>
                </ul>
            </div>
            <div>
                <h4 class="font-bold text-gray-900 mb-6 uppercase tracking-widest text-sm">Office Info</h4>
                <div class="space-y-4 text-gray-600 text-sm">
                    <p class="flex items-center gap-3"><i class="fas fa-map-marker-alt text-blue-600"></i> Main Road, Astore, Gilgit-Baltistan</p>
                    <p class="flex items-center gap-3"><i class="fas fa-phone-alt text-blue-600"></i> +92 5817-XXXXXX</p>
                    <p class="flex items-center gap-3"><i class="fas fa-envelope text-blue-600"></i> contact@jinnahastore.edu.pk</p>
                </div>
            </div>
        </div>
        <div class="text-center text-gray-400 text-sm border-t border-gray-100 pt-8">
            &copy; 2026 Jinnah School & Degree College Astore. All rights reserved.
        </div>
    </footer>

    <script>
        // Secret Admin Logic (5 Taps on Drive Logo)
        let tapCount = 0;
        const secretBtn = document.getElementById('secretTap');
        
        secretBtn.addEventListener('click', () => {
            tapCount++;
            if (tapCount === 5) {
                const key = prompt("⚠️ Secure Access Required. Enter Admin Key:");
                if (key === "gb46") {
                    alert("✅ Access Granted! Redirecting to College Management System...");
                    // window.location.href = "admin_panel.html";
                } else {
                    alert("❌ Invalid Access Key!");
                    tapCount = 0;
                }
            }
            // Reset tap count if not finished within 3 seconds
            setTimeout(() => { tapCount = 0; }, 3000);
        });
    </script>
</body>
</html>
