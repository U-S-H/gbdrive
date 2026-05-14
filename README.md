<html lang="ur" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>ویسٹی فائی پرو میکس</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;700;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background-color: #010409; color: white; margin: 0; padding: 0; text-align: right; overflow-x: hidden; }
        .glass { background: rgba(22, 27, 34, 0.85); backdrop-filter: blur(25px); border: 1px solid rgba(255,255,255,0.08); border-radius: 30px; }
        .page { display: none; padding: 20px; animation: slideUp 0.4s cubic-bezier(0.4, 0, 0.2, 1); }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        
        /* Navigation */
        .nav-bar { position: fixed; bottom: 15px; left: 15px; right: 15px; height: 80px; display: flex; justify-content: space-around; align-items: center; z-index: 5000; border-radius: 30px; background: rgba(13, 17, 23, 0.98); border: 1px solid rgba(59, 130, 246, 0.2); flex-direction: row-reverse; }
        .nav-active { color: #3b82f6; transform: translateY(-8px); text-shadow: 0 0 15px rgba(59, 130, 246, 0.4); }

        /* Real-time Chat Agent 204 */
        #chat-window { position: fixed; bottom: 110px; left: 20px; right: 20px; height: 450px; z-index: 10000; display: none; flex-direction: column; box-shadow: 0 20px 60px rgba(0,0,0,0.6); }
        .msg { max-width: 80%; padding: 12px; margin: 6px; font-size: 11px; font-weight: 500; line-height: 1.5; }
        .msg-user { background: #3b82f6; border-radius: 20px 20px 0 20px; align-self: flex-end; color: white; }
        .msg-admin { background: #2d333b; border-radius: 20px 20px 20px 0; align-self: flex-start; border: 1px solid #444c56; }

        /* Professional Details */
        .node-img { height: 140px; width: 100%; object-fit: cover; border-radius: 20px 20px 0 0; }
        .timer-badge { background: rgba(239, 68, 68, 0.15); color: #f87171; padding: 4px 10px; border-radius: 10px; font-size: 9px; font-weight: 800; border: 1px solid rgba(239, 68, 68, 0.2); }
        #wheel { width: 290px; height: 290px; border-radius: 50%; border: 12px solid #d4af37; transition: transform 5s cubic-bezier(0.15, 0, 0.15, 1); box-shadow: 0 0 50px rgba(212,175,55,0.2); }
    </style>
</head>
<body class="pb-36">

    <div id="chat-window" class="glass overflow-hidden border-blue-500/30">
        <div class="p-4 bg-gradient-to-r from-blue-700 to-blue-500 flex justify-between items-center">
            <button onclick="clearChat()" class="text-[9px] bg-black/20 px-4 py-1.5 rounded-full font-black tracking-widest uppercase">Clear Chat</button>
            <div class="text-right">
                <p class="font-black text-xs uppercase tracking-tighter">Support Agent 204</p>
                <div class="flex items-center gap-1 justify-end">
                    <span class="w-1.5 h-1.5 bg-green-400 rounded-full animate-pulse"></span>
                    <p class="text-[8px] font-bold opacity-80">Online & Ready</p>
                </div>
            </div>
            <button onclick="toggleChat()" class="w-8 h-8 rounded-full bg-black/10 flex items-center justify-center"><i class="fa-solid fa-times"></i></button>
        </div>
        <div id="chat-msgs" class="flex-1 overflow-y-auto p-4 flex flex-col bg-[#0d1117]"></div>
        <div class="p-4 bg-[#1c2128] flex gap-2 border-t border-white/5">
            <input id="chat-input" type="text" placeholder="پیغام لکھیں..." class="flex-1 bg-white/5 border border-white/10 rounded-2xl p-4 text-xs outline-none focus:border-blue-500">
            <button onclick="sendMsg()" class="bg-blue-600 w-14 rounded-2xl flex items-center justify-center shadow-lg active:scale-90"><i class="fa-solid fa-paper-plane"></i></button>
        </div>
    </div>

    <header class="p-6 flex justify-between items-center flex-row-reverse">
        <div class="flex items-center gap-4 flex-row-reverse">
            <div class="relative">
                <img id="user-photo" onclick="handleAdminTap()" class="w-14 h-14 rounded-2xl border-2 border-blue-500/30 cursor-pointer shadow-2xl">
                <div class="absolute -bottom-1 -right-1 w-4 h-4 bg-green-500 border-4 border-[#010409] rounded-full"></div>
            </div>
            <div class="text-right">
                <p id="user-name" class="text-xs font-black uppercase tracking-tight">Syncing...</p>
                <p class="text-[8px] font-extrabold text-blue-500 uppercase tracking-widest">Verified Member</p>
            </div>
        </div>
        <button onclick="toggleChat()" class="w-12 h-12 glass flex items-center justify-center text-blue-500 relative border-blue-500/20">
            <i class="fa-solid fa-headset text-xl"></i>
            <span class="absolute top-2 right-2 w-2.5 h-2.5 bg-red-500 rounded-full border-2 border-[#010409]"></span>
        </button>
    </header>

    <main id="app-ui">
        <div id="p-home" class="page active-page">
            <div class="glass p-10 mb-8 text-center bg-gradient-to-br from-blue-600/20 via-transparent to-transparent border-blue-500/30">
                <p class="text-[10px] font-black opacity-40 uppercase tracking-widest mb-3">Net Available Balance</p>
                <h2 id="v-bal" class="text-6xl font-black italic mb-10 tracking-tighter text-white">₨ 0.00</h2>
                <div class="flex gap-4">
                    <button onclick="showPage('finance')" class="flex-1 bg-white text-black py-5 rounded-2xl font-black text-[11px] shadow-2xl active:scale-95 transition-all">DEPOSIT</button>
                    <button onclick="showPage('withdraw')" class="flex-1 glass py-5 rounded-2xl font-black text-[11px] border-white/10">WITHDRAW</button>
                </div>
            </div>

            <h3 class="text-sm font-black mb-6 px-2 flex items-center gap-2">
                <i class="fa-solid fa-bolt-lightning text-blue-500"></i>
                PREMIUM NODES (START ₨ 200)
            </h3>
            <div id="plans-grid" class="space-y-6 pb-12"></div>
        </div>

        <div id="p-spin" class="page text-center">
            <h2 class="text-3xl font-black italic mb-12 uppercase tracking-tighter">گولڈن <span class="text-yellow-500">لکی سپن</span></h2>
            <div class="flex justify-center mb-12 relative">
                <div class="absolute -top-10 left-1/2 -translate-x-1/2 text-red-600 text-5xl z-10 animate-bounce"><i class="fa-solid fa-caret-down"></i></div>
                <img id="wheel" src="IMG_20260515_012434.jpg" onerror="this.src='https://via.placeholder.com/300?text=Lucky+Wheel'">
            </div>
            <div class="glass p-6 mb-10 border-yellow-500/10 max-w-xs mx-auto">
                <p id="spin-status" class="text-[11px] font-black text-blue-400 uppercase tracking-widest">تحقیق ہو رہی ہے...</p>
                <p class="text-[8px] opacity-40 mt-2 uppercase">Paid Spin: ₨ 50 | Max Prize: ₨ 100</p>
            </div>
            <button id="spin-btn" onclick="executeSpin()" class="w-full max-w-xs bg-gradient-to-r from-blue-600 to-blue-400 py-6 rounded-full font-black text-xs shadow-2xl active:scale-95">SPIN NOW</button>
        </div>

        <div id="p-history" class="page">
            <h2 class="text-2xl font-black italic mb-8 text-right uppercase">ہسٹری <span class="text-blue-500">ریکارڈز</span></h2>
            <div id="history-list" class="space-y-4 pb-12"></div>
        </div>

        <div id="p-team" class="page text-center">
            <h2 class="text-2xl font-black italic mb-8 uppercase">نیٹ ورک <span class="text-blue-500">ٹیم</span></h2>
            <div class="glass p-8">
                <p class="text-[10px] opacity-40 mb-4 uppercase tracking-widest">Referral Link</p>
                <input id="ref-link" readonly class="w-full bg-black/40 p-5 rounded-2xl text-[10px] text-blue-400 border border-white/5 text-center mb-10 font-bold">
                <div class="grid grid-cols-2 gap-4">
                    <div class="bg-blue-600/5 p-6 rounded-3xl border border-blue-500/10">
                        <p class="text-[8px] opacity-50 uppercase">Team Size</p>
                        <p id="team-count" class="text-2xl font-black">0</p>
                    </div>
                    <div class="bg-green-600/5 p-6 rounded-3xl border border-green-500/10">
                        <p class="text-[8px] opacity-50 uppercase">Referral Income</p>
                        <p class="text-2xl font-black text-green-500">₨ 0</p>
                    </div>
                </div>
            </div>
        </div>

        <div id="admin-ui" class="hidden fixed inset-0 z-[20000] bg-[#010409] p-6 overflow-y-auto">
            <div class="flex justify-between items-center mb-10 border-b border-white/5 pb-5">
                <button onclick="closeAdmin()" class="bg-red-500/10 text-red-500 px-6 py-3 rounded-xl font-bold text-[10px]">CLOSE TERMINAL</button>
                <h2 class="text-xl font-black italic">VESTIFY ADMIN</h2>
            </div>
            <div id="adm-tx-list" class="space-y-4"></div>
        </div>
    </main>

    <nav class="nav-bar">
        <button onclick="showPage('home')" class="nav-btn" id="nav-home"><i class="fa-solid fa-microchip"></i></button>
        <button onclick="showPage('spin')" class="nav-btn" id="nav-spin"><i class="fa-solid fa-dharmachakra"></i></button>
        <button onclick="showPage('team')" class="nav-btn" id="nav-team"><i class="fa-solid fa-users-rays"></i></button>
        <button onclick="showPage('history')" class="nav-btn" id="nav-history"><i class="fa-solid fa-list-check"></i></button>
    </nav>

    <script>
        // Firebase Auth & Config
        const firebaseConfig = { apiKey: "AIzaSyC9ofJ1KxRXHnxilpU9gyI87D3BSOZ9v1g", authDomain: "vestify-991f2.firebaseapp.com", projectId: "vestify-991f2", storageBucket: "vestify-991f2.firebasestorage.app", messagingSenderId: "799007097733", appId: "1:799007097733:web:ed3b35b6c4e51dc2e7baec" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore(), auth = firebase.auth();
        let userObj = null, currentRot = 0, tapCount = 0;

        auth.onAuthStateChanged(async user => {
            if(user) {
                document.getElementById('user-photo').src = user.photoURL;
                document.getElementById('user-name').innerText = user.displayName;
                const userRef = db.collection("users").doc(user.displayName);
                const doc = await userRef.get();
                if(!doc.exists) await userRef.set({ balance: 0, lastSpin: 0, teamCount: 0, walletPin: "1234" });
                
                syncData(user.displayName);
                renderPlans();
                loadHistory(user.displayName);
                loadChat(user.displayName);
                document.getElementById('ref-link').value = `https://u-s-h.github.io/vestify/?ref=${user.displayName.replace(/\s/g, '')}`;
            } else {
                auth.signInWithPopup(new firebase.auth.GoogleAuthProvider());
            }
        });

        // 15+ Detailed Nodes starting 200 PKR
        function renderPlans() {
            let h = "";
            const samples = [
                { price: 200, daily: 45, days: 10, img: "https://images.unsplash.com/photo-1639762681485-074b7f938ba0?w=500" },
                { price: 500, daily: 120, days: 15, img: "https://images.unsplash.com/photo-1642104704074-907c0698bcd9?w=500" },
                { price: 1000, daily: 250, days: 30, img: "https://images.unsplash.com/photo-1621761191319-c6fb62004040?w=500" },
                { price: 2500, daily: 600, days: 30, img: "https://images.unsplash.com/photo-1633156199268-dc304a298966?w=500" }
            ];

            for(let i=0; i<15; i++) {
                let p = samples[i] || { price: 3000 + (i*1000), daily: 700 + (i*200), days: 30, img: "https://images.unsplash.com/photo-1614064641938-3bbee52942c7?w=500" };
                h += `
                <div class="glass overflow-hidden border-white/5 bg-gradient-to-b from-white/5 to-transparent">
                    <img src="${p.img}" class="node-img">
                    <div class="p-6">
                        <div class="flex justify-between items-start mb-5">
                            <div class="text-right">
                                <h4 class="text-2xl font-black italic tracking-tighter">₨ ${p.price.toLocaleString()}</h4>
                                <p class="text-[9px] text-blue-500 font-bold uppercase tracking-widest">VESTIFY CLUSTER V.${i+1}</p>
                            </div>
                            <span class="timer-badge">VALIDITY: ${p.days} DAYS</span>
                        </div>
                        <div class="grid grid-cols-2 gap-3 mb-6">
                            <div class="bg-black/40 p-4 rounded-2xl border border-white/5">
                                <p class="text-[7px] opacity-40 uppercase mb-1">Daily Net</p>
                                <p class="text-sm font-black text-green-400">₨ ${p.daily}</p>
                            </div>
                            <div class="bg-black/40 p-4 rounded-2xl border border-white/5">
                                <p class="text-[7px] opacity-40 uppercase mb-1">Total Net</p>
                                <p class="text-sm font-black text-blue-400">₨ ${p.daily * p.days}</p>
                            </div>
                        </div>
                        <button class="w-full bg-white text-black py-4 rounded-2xl font-black text-[10px] uppercase shadow-xl active:scale-95 transition-all">ACTIVATE THIS NODE</button>
                    </div>
                </div>`;
            }
            document.getElementById('plans-grid').innerHTML = h;
        }

        // Real-time Help Desk logic Agent 204
        async function sendMsg() {
            const input = document.getElementById('chat-input');
            if(!input.value.trim()) return;
            const text = input.value;
            input.value = "";
            
            await db.collection("chats").add({
                user: auth.currentUser.displayName,
                msg: text,
                sender: 'user',
                time: firebase.firestore.FieldValue.serverTimestamp()
            });
        }

        function loadChat(name) {
            db.collection("chats").where("user", "==", name).orderBy("time", "asc").onSnapshot(s => {
                let h = "";
                s.forEach(doc => {
                    const d = doc.data();
                    const cls = d.sender === 'user' ? 'msg-user' : 'msg-admin';
                    h += `<div class="msg ${cls}">${d.msg}</div>`;
                });
                const box = document.getElementById('chat-msgs');
                box.innerHTML = h;
                box.scrollTop = box.scrollHeight;
            });
        }

        // Lucky Spin with Daily Free Logic
        async function executeSpin() {
            const now = Date.now();
            const last = userObj.lastSpin || 0;
            const isFree = (now - last) > (24*60*60*1000);
            const cost = isFree ? 0 : 50;

            if(!isFree && userObj.balance < cost) return alert("Paid spin requires ₨ 50 balance!");

            const prizes = [{a:0,w:100},{a:45,w:50},{a:90,w:10},{a:135,w:5},{a:180,w:2},{a:225,w:10},{a:270,w:2},{a:315,w:5}];
            const p = prizes[Math.floor(Math.random()*prizes.length)];
            currentRot += 3600 + (360 - p.a);
            document.getElementById('wheel').style.transform = `rotate(${currentRot}deg)`;
            
            setTimeout(async () => {
                await db.collection("users").doc(auth.currentUser.displayName).update({
                    balance: firebase.firestore.FieldValue.increment(p.w - cost),
                    lastSpin: isFree ? now : last
                });
                alert(`WOW! You won ₨ ${p.w}. ${isFree ? 'Daily Free Spin claimed.' : '₨ 50 paid spin used.'}`);
            }, 5000);
        }

        function syncData(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                userObj = doc.data();
                document.getElementById('v-bal').innerText = "₨ " + (userObj.balance || 0).toLocaleString();
                const isFree = (Date.now() - (userObj.lastSpin || 0)) > (24*60*60*1000);
                document.getElementById('spin-status').innerText = isFree ? "DAILY FREE SPIN IS READY!" : "NEXT SPIN COST: ₨ 50";
            });
        }

        function loadHistory(name) {
            db.collection("txs").where("user", "==", name).orderBy("time", "desc").onSnapshot(s => {
                let h = "";
                s.forEach(doc => {
                    const d = doc.data();
                    const color = d.status === "Approved" ? "text-green-500" : (d.status === "Pending" ? "text-yellow-500" : "text-red-500");
                    h += `<div class="glass p-6 flex justify-between items-center border-white/5">
                        <div class="text-right">
                            <p class="text-[10px] font-black uppercase text-blue-500">${d.type}</p>
                            <p class="text-[8px] opacity-40 mt-1">${new Date(d.time).toLocaleDateString()}</p>
                        </div>
                        <div class="text-left">
                            <p class="font-black text-lg italic">₨ ${d.amount}</p>
                            <p class="text-[8px] font-bold uppercase ${color}">${d.status}</p>
                        </div>
                    </div>`;
                });
                document.getElementById('history-list').innerHTML = h || "<p class='text-center opacity-20 py-20 uppercase tracking-widest'>No History Records</p>";
            });
        }

        function showPage(p) {
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.getElementById('p-'+p).classList.add('active-page');
            document.querySelectorAll('.nav-btn').forEach(b => b.classList.remove('nav-active'));
            if(document.getElementById('nav-'+p)) document.getElementById('nav-'+p).classList.add('nav-active');
            window.scrollTo(0,0);
        }

        function toggleChat() { 
            const chat = document.getElementById('chat-window');
            chat.style.display = (chat.style.display === 'flex') ? 'none' : 'flex';
        }

        async function clearChat() {
            if(confirm("Confirm: Delete all chat records?")) {
                const s = await db.collection("chats").where("user", "==", auth.currentUser.displayName).get();
                s.forEach(doc => doc.ref.delete());
            }
        }

        function handleAdminTap() { tapCount++; if(tapCount >= 10) { let k = prompt("Key:"); if(k === "net204") loadAdmin(); tapCount=0; } }
        function loadAdmin() {
            document.getElementById('admin-ui').classList.remove('hidden');
            db.collection("txs").where("status","==","Pending").onSnapshot(s => {
                let h = ""; s.forEach(doc => {
                    const d = doc.data();
                    h += `<div class="glass p-6 border-red-500/20">
                        <p class="text-[10px] font-black text-blue-500">${d.user}</p>
                        <h2 class="text-3xl font-black italic my-4">₨ ${d.amount}</h2>
                        <button onclick="approveTx('${doc.id}','${d.user}',${d.amount})" class="w-full bg-green-600 py-4 rounded-2xl font-black text-[10px]">APPROVE NOW</button>
                    </div>`;
                });
                document.getElementById('adm-tx-list').innerHTML = h || "<p class='text-center opacity-30'>Queue Clear</p>";
            });
        }
        async function approveTx(id, u, a) {
            await db.collection("txs").doc(id).update({status:"Approved"});
            await db.collection("users").doc(u).update({balance: firebase.firestore.FieldValue.increment(a)});
        }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }
    </script>
</body>
</html>
