<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Jinnah School & Degree College Astore | Official</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;600;700&display=swap');
        
        body {
            font-family: 'Poppins', sans-serif;
            background-color: #f8fafc;
        }

        .glass-effect {
            background: rgba(255, 255, 255, 0.8);
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 0.3);
        }

        .hero-gradient {
            background: linear-gradient(135deg, #1e3a8a 0%, #3b82f6 100%);
        }

        .card-hover:hover {
            transform: translateY(-10px);
            transition: all 0.3s ease;
            box-shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.1);
        }
    </style>
</head>
<body class="text-gray-800">

    <nav class="sticky top-0 z-50 glass-effect p-4 shadow-sm">
        <div class="container mx-auto flex justify-between items-center">
            <h1 class="text-2xl font-bold text-blue-900">JINNAH <span class="text-blue-500 text-lg">ASTORE</span></h1>
            <div class="hidden md:flex space-x-8 font-medium">
                <a href="#" class="hover:text-blue-600">Home</a>
                <a href="#about" class="hover:text-blue-600">About</a>
                <a href="#admission" class="hover:text-blue-600">Admissions</a>
                <a href="#portal" class="hover:text-blue-600">Student Portal</a>
            </div>
            <button class="bg-blue-700 text-white px-6 py-2 rounded-full font-semibold hover:bg-blue-800 transition">Apply Now</button>
        </div>
    </nav>

    <section class="hero-gradient text-white py-24 px-6 text-center">
        <div class="container mx-auto">
            <h2 class="text-5xl font-bold mb-4 animate-bounce">Excellence in the Heart of Astore</h2>
            <p class="text-xl mb-8 opacity-90">Providing Quality Education & Empowering Future Leaders since 1998.</p>
            <div class="flex justify-center gap-4">
                <button class="bg-white text-blue-900 px-8 py-3 rounded-lg font-bold shadow-lg">Our Courses</button>
                <button class="border-2 border-white px-8 py-3 rounded-lg font-bold">Virtual Tour</button>
            </div>
        </div>
    </section>

    <section id="about" class="py-20 container mx-auto px-6">
        <div class="grid grid-cols-1 md:grid-cols-3 gap-10">
            <div class="bg-white p-8 rounded-2xl shadow-sm card-hover border-b-4 border-blue-600">
                <div class="text-4xl text-blue-600 mb-4"><i class="fas fa-microscope"></i></div>
                <h3 class="text-xl font-bold mb-2">Modern Labs</h3>
                <p class="text-gray-600">Fully equipped Science and Computer labs for practical excellence.</p>
            </div>
            <div class="bg-white p-8 rounded-2xl shadow-sm card-hover border-b-4 border-green-600">
                <div class="text-4xl text-green-600 mb-4"><i class="fas fa-user-graduate"></i></div>
                <h3 class="text-xl font-bold mb-2">Scholarships</h3>
                <p class="text-gray-600">Rewarding talent with financial aid and merit-based programs.</p>
            </div>
            <div class="bg-white p-8 rounded-2xl shadow-sm card-hover border-b-4 border-yellow-600">
                <div class="text-4xl text-yellow-600 mb-4"><i class="fas fa-bus"></i></div>
                <h3 class="text-xl font-bold mb-2">Transportation</h3>
                <p class="text-gray-600">Safe and reliable transport coverage across Astore valley.</p>
            </div>
        </div>
    </section>

    <section id="portal" class="bg-gray-100 py-20 px-6">
        <div class="max-w-4xl mx-auto bg-white rounded-3xl shadow-2xl overflow-hidden flex flex-col md:flex-row">
            <div class="md:w-1/3 bg-blue-900 text-white p-10">
                <h3 class="text-2xl font-bold mb-4">Student Portal</h3>
                <p class="opacity-75 mb-6">Check results, attendance, and fee status instantly.</p>
                <img src="https://cdn-icons-png.flaticon.com/512/2991/2991148.png" alt="Portal" class="w-32 mx-auto">
            </div>
            <div class="md:w-2/3 p-10">
                <label class="block mb-2 font-semibold">Enter Roll Number</label>
                <input type="text" placeholder="e.g. JS-2024-101" class="w-full p-4 bg-gray-50 border border-gray-200 rounded-xl mb-4 focus:ring-2 focus:ring-blue-500 outline-none">
                <button class="w-full bg-blue-600 text-white p-4 rounded-xl font-bold hover:bg-blue-700 transition">View Digital Transcript</button>
            </div>
        </div>
    </section>

    <footer class="bg-blue-950 text-white py-12 px-6">
        <div class="container mx-auto grid grid-cols-1 md:grid-cols-2 gap-12 items-center">
            <div>
                <h4 class="text-2xl font-bold mb-4">Contact College Office</h4>
                <p class="mb-2"><i class="fas fa-map-marker-alt mr-2"></i> Main Road, Astore, Gilgit-Baltistan</p>
                <p class="mb-2"><i class="fas fa-phone mr-2"></i> +92 XXX XXXXXXX</p>
                <p><i class="fas fa-envelope mr-2"></i> info@jinnahastore.edu.pk</p>
            </div>
            <div class="text-right">
                <p class="opacity-50">Designed for a Professional Future</p>
                <div class="flex justify-end gap-4 mt-4 text-2xl">
                    <i class="fab fa-facebook hover:text-blue-400 cursor-pointer"></i>
                    <i class="fab fa-instagram hover:text-pink-400 cursor-pointer"></i>
                    <i class="fab fa-whatsapp hover:text-green-400 cursor-pointer"></i>
                </div>
            </div>
        </div>
    </footer>

</body>
</html>
