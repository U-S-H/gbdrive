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
        @import url('https://fonts.googleapis.com/css2?family=Noto+Nastaliq+Urdu:wght@400;700&family=Plus+Jakarta+Sans:wght@400;700;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', 'Noto Nastaliq Urdu', sans-serif; background-color: #010409; color: white; margin: 0; padding: 0; overflow-x: hidden; text-align: right; }
        .glass { background: rgba(22, 27, 34, 0.7); backdrop-filter: blur(25px); border: 1px solid rgba(255,255,255,0.08); border-radius: 32px; }
        .page { display: none; padding: 20px; animation: slideUp 0.5s cubic-bezier(0.4, 0, 0.2, 1); }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(40px); } to { opacity: 1; transform: translateY(0); } }
        
        #notification-box { position: fixed; top: 20px; left: 20px; right: 20px; z-index: 20000; pointer-events: none; }
        .pop-notify { background: #fff; color: #000; padding: 12px 20px; border-radius: 20px; margin-bottom: 12px; box-shadow: 0 20px 40px rgba(0,0,0,0.4); display: flex; align-items: center; gap: 12px; animation: popInOut 4s forwards; font-size: 11px; font-weight: 800; border-right: 6px solid #3b82f6; }
        @keyframes popInOut { 0% { opacity: 0; transform: scale(0.5) translateY(-100px); } 15% { opacity: 1; transform: scale(1) translateY(0); } 85% { opacity: 1; transform: scale(1) translateY(0); } 100% { opacity: 0; transform: scale(0.5) translateY(-100px); } }

        .nav-bar { position: fixed; bottom: 15px; left: 15px; right: 15px; height: 80px; display: flex; justify-content: space-around; align-items: center; z-index: 5000; border-radius: 30px; background: rgba(22, 27, 34, 0.98); border: 1px solid rgba(255,255,255,0.05); flex-direction: row-reverse; box-shadow: 0 -10px 40px rgba(0,0,0,0.5); }
        .nav-btn { color: rgba(255,255,255,0.3); transition: 0.4s; font-size: 22px; }
        .nav-active { color: #3b82f6; transform: translateY(-8px); text-shadow: 0 0 20px rgba(59, 130, 246, 0.6); }
        
        #wheel { width: 300px; height: 300px; border-radius: 50%; border: 12px solid #d4af37; transition: transform 5s cubic-bezier(0.15, 0, 0.15, 1); box-shadow: 0 0 60px rgba(212, 175, 55, 0.4); }
        .plan-card { position: relative; overflow: hidden; background: linear-gradient(145deg, rgba(30,35,45,1) 0%, rgba(15,18,25,1) 100%); border: 1px solid rgba(255,255,255,0.05); }
        .plan-badge { position: absolute; top: 15px; left: -30px; background: #3b82f6; padding: 5px 35px; transform: rotate(-45deg); font-size: 8px; font-weight: 900; box-shadow: 0 5px 15px rgba(0,0,0,0.3); }
        .admin-btn { background: rgba(255,0,0,0.1); border: 1px solid rgba(255,0,0,0.2); color: #ff4444; }
    </style>
</head>
<body class="pb-36">

    <div id="notification-box"></div>

    <main id="app-ui">
        <header class="p-6 flex justify-between items-center flex-row-reverse">
            <div class="flex items-center gap-4 flex-row-reverse">
                <div class="relative">
                    <img id="user-photo" onclick="handleAdminTap()" class="w-14 h-14 rounded-2xl border-2 border-blue-500/30 cursor-pointer shadow-lg shadow-blue-500/10">
                    <div class="absolute -bottom-1 -right-1 w-4 h-4 bg-green-500 border-4 border-[#010409] rounded-full"></div>
                </div>
                <div class="text-right">
                    <p id="user-name" class="text-sm font-black uppercase tracking-tight">لوڈنگ...</p>
                    <p class="text-[9px] font-bold text-blue-500 uppercase tracking-widest">Verified Investor</p>
                </div>
            </div>
            <button onclick="showPage('history')" class="w-12 h-12 glass flex items-center justify-center text-blue-500 active:scale-90 transition-all"><i class="fa-solid fa-receipt"></i></button>
        </header>

        <div id="p-home" class="page active-page">
            <div class="glass p-10 mb-8 text-center bg-gradient-to-br from-blue-600/20 via-transparent to-transparent border-blue-500/20">
                <p class="text-[10px] font-black opacity-40 uppercase mb-3 tracking-widest text-blue-400">کل دستیاب بیلنس</p>
                <h2 id="v-bal" class="text-6xl font-black italic mb-10 tracking-tighter">₨ 0</h2>
                <div class="flex gap-4">
                    <button onclick="showPage('finance')" class="flex-1 bg-white text-black py-5 rounded-2xl font-black text-[11px] uppercase shadow-2xl active:scale-95 transition-all">جمع کریں</button>
                    <button onclick="showPage('withdraw')" class="flex-1 glass py-5 rounded-2xl font-black text-[11px] uppercase active:scale-95 transition-all">نکلوائیں</button>
                </div>
            </div>

            <div class="flex gap-3 mb-8 justify-center">
                <button id="tab-n" onclick="switchPlans('normal')" class="flex-1 py-4 rounded-2xl text-[10px] font-black uppercase bg-blue-600 shadow-lg shadow-blue-600/20">Normal Plans</button>
                <button id="tab-s" onclick="switchPlans('special')" class="flex-1 py-4 rounded-2xl text-[10px] font-black uppercase bg-white/5 opacity-50">Special Offers</button>
            </div>

            <div id="plans-container" class="space-y-6 pb-10"></div>
        </div>

        <div id="p-spin" class="page text-center">
            <h2 class="text-3xl font-black italic uppercase mb-12">گولڈن <span class="text-yellow-500">جیک پاٹ</span></h2>
            <div class="flex justify-center mb-12 relative">
                <div class="absolute -top-10 left-1/2 -translate-x-1/2 text-red-600 text-5xl z-10 animate-bounce"><i class="fa-solid fa-caret-down"></i></div>
                <img id="wheel" src="IMG_20260515_012434.jpg" onerror="this.src='https://via.placeholder.com/300?text=Golden+Wheel'">
            </div>
            <p id="spin-cost-text" class="text-[11px] mb-8 font-black text-blue-400 uppercase tracking-widest">پہلا سپن بالکل فری ہے!</p>
            <button id="spin-btn" onclick="executeSpin()" class="w-full max-w-xs bg-gradient-to-r from-blue-600 to-blue-400 py-6 rounded-[2.5rem] font-black text-xs uppercase shadow-2xl active:scale-95 transition-all">سپن گھمائیں</button>
        </div>

        <div id="p-team" class="page text-center">
            <h2 class="text-3xl font-black italic uppercase mb-10">نیٹ ورک <span class="text-blue-500">ٹیم</span></h2>
            <div class="glass p-8 border-blue-500/10">
                <div class="mb-8">
                    <p class="text-[10px] opacity-40 mb-4 uppercase tracking-widest">آپ کا ریفرل لنک</p>
                    <div class="flex gap-2">
                        <input id="ref-link" readonly class="flex-1 bg-black/40 p-5 rounded-2xl text-[10px] text-blue-400 border border-white/5 outline-none text-center font-bold">
                        <button onclick="copyRef()" class="w-16 bg-white text-black rounded-2xl flex items-center justify-center text-xl"><i class="fa-solid fa-copy"></i></button>
                    </div>
                </div>
                <div class="grid grid-cols-2 gap-4">
                    <div class="bg-white/5 p-6 rounded-3xl border border-white/5">
                        <p class="text-[9px] opacity-40 uppercase mb-2">ٹیم سائز</p>
                        <p id="team-count" class="text-2xl font-black text-blue-500">0</p>
                    </div>
                    <div class="bg-white/5 p-6 rounded-3xl border border-white/5">
                        <p class="text-[9px] opacity-40 uppercase mb-2">ٹیم بونس</p>
                        <p class="text-2xl font-black text-green-500">₨ 0</p>
                    </div>
                </div>
            </div>
        </div>

        <div id="p-finance" class="page">
            <h2 class="text-3xl font-black italic uppercase mb-8 text-right">رقم <span class="text-blue-500">جمع کریں</span></h2>
            <div class="glass p-8 space-y-5 border-blue-500/10">
                <div class="bg-blue-600/10 p-6 rounded-3xl border border-blue-500/20 text-center">
                    <p class="text-[10px] opacity-40 mb-2 uppercase tracking-widest">EasyPaisa Account</p>
                    <p class="text-xl font-black text-blue-400 tracking-widest">03379827882</p>
                    <button onclick="navigator.clipboard.writeText('03379827882'); alert('Number Copied!')" class="mt-3 text-[10px] font-bold text-white bg-blue-600 px-4 py-2 rounded-full">Copy Number</button>
                </div>
                <input id="d-amt" type="number" placeholder="رقم (کم از کم 200)" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/10 outline-none focus:border-blue-500/50 transition-all">
                <input id="d-tid" type="text" placeholder="Transaction ID (TID)" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/10 outline-none focus:border-blue-500/50 transition-all">
                <button onclick="submitTx('Deposit')" class="w-full bg-blue-600 py-6 rounded-2xl font-black text-xs uppercase tracking-widest shadow-xl active:scale-95 transition-all">ڈیپازٹ کنفرم کریں</button>
            </div>
        </div>

        <div id="p-withdraw" class="page">
            <h2 class="text-3xl font-black italic uppercase mb-8 text-right">رقم <span class="text-red-500">نکلوائیں</span></h2>
            <div class="glass p-8 space-y-5 border-red-500/10">
                <input id="w-num" type="text" placeholder="ایزی پیسہ / جاز کیش نمبر" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/10 outline-none focus:border-red-500/50">
                <input id="w-amt" type="number" placeholder="رقم (کم از کم 100)" class="w-full bg-black/40 p-5 rounded-2xl text-xs border border-white/10 outline-none focus:border-red-500/50">
                <input id="w-pin" type="password" maxlength="4" placeholder="والٹ پن" class="w-full bg-black/40 p-5 rounded-2xl text-center font-black tracking-[1.5em] border border-white/10">
                <button onclick="submitTx('Withdrawal')" class="w-full bg-white text-black py-6 rounded-2xl font-black text-xs uppercase tracking-widest shadow-xl active:scale-95 transition-all">ودرا ریکویسٹ بھیجیں</button>
            </div>
        </div>

        <div id="p-history" class="page">
            <h2 class="text-3xl font-black italic uppercase mb-8 text-right">ٹرانزیکشن <span class="text-blue-500">ہسٹری</span></h2>
            <div id="history-list" class="space-y-4 pb-12"></div>
        </div>

        <div id="admin-ui" class="hidden fixed inset-0 z-[10000] bg-[#010409] p-6 overflow-y-auto">
            <div class="flex justify-between items-center mb-10">
                <button onclick="closeAdmin()" class="admin-btn px-6 py-3 rounded-xl font-bold">CLOSE</button>
                <h2 class="text-2xl font-black text-red-500">ADMIN CONTROL</h2>
            </div>
            <div id="adm-tx-list" class="space-y-4"></div>
        </div>

        <nav class="nav-bar">
            <button onclick="showPage('home')" class="nav-btn" id="nav-home"><i class="fa-solid fa-house-chimney"></i></button>
            <button onclick="showPage('spin')" class="nav-btn" id="nav-spin"><i class="fa-solid fa-dharmachakra"></i></button>
            <button onclick="showPage('team')" class="nav-btn" id="nav-team"><i class="fa-solid fa-users-rays"></i></button>
            <button onclick="showPage('finance')" class="nav-btn" id="nav-finance"><i class="fa-solid fa-wallet"></i></button>
        </nav>
    </main>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyC9ofJ1KxRXHnxilpU9gyI87D3BSOZ9v1g", authDomain: "vestify-991f2.firebaseapp.com", projectId: "vestify-991f2", storageBucket: "vestify-991f2.firebasestorage.app", messagingSenderId: "799007097733", appId: "1:799007097733:web:ed3b35b6c4e51dc2e7baec" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore(), auth = firebase.auth();
        let userObj = null, currentRot = 0, tapCount = 0;

        // Auth & Referral Logic
        auth.onAuthStateChanged(async user => {
            if(user) {
                document.getElementById('user-photo').src = user.photoURL;
                document.getElementById('user-name').innerText = user.displayName;
                
                const params = new URLSearchParams(window.location.search);
                const refBy = params.get('ref');
                const userRef = db.collection("users").doc(user.displayName);
                const doc = await userRef.get();
                
                if(!doc.exists) {
                    await userRef.set({ balance: 0, spinCount: 0, teamCount: 0, referredBy: refBy || "Direct", walletPin: "1234" });
                    if(refBy && refBy !== user.displayName.replace(/\s/g, '')) {
                        const upliner = await db.collection("users").where("displayName", "==", refBy).get();
                        if(!upliner.empty) {
                            upliner.docs[0].ref.update({ balance: firebase.firestore.FieldValue.increment(50), teamCount: firebase.firestore.FieldValue.increment(1) });
                        }
                    }
                }
                syncData(user.displayName);
                switchPlans('normal');
                loadHistory(user.displayName);
                startFakePopups();
                document.getElementById('ref-link').value = `https://u-s-h.github.io/vestify/?ref=${user.displayName.replace(/\s/g, '')}`;
            } else {
                auth.signInWithPopup(new firebase.auth.GoogleAuthProvider());
            }
        });

        function syncData(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                userObj = doc.data();
                document.getElementById('v-bal').innerText = "₨ " + (userObj.balance || 0).toLocaleString();
                document.getElementById('team-count').innerText = userObj.teamCount || 0;
                document.getElementById('spin-cost-text').innerText = userObj.spinCount > 0 ? "Cost: ₨ 50 per Spin" : "First Spin is FREE!";
            });
        }

        // Modern 20+ Plans Logic
        function switchPlans(type) {
            document.getElementById('tab-n').className = type === 'normal' ? "flex-1 py-4 rounded-2xl text-[10px] font-black bg-blue-600 shadow-lg" : "flex-1 py-4 rounded-2xl text-[10px] font-black bg-white/5 opacity-50";
            document.getElementById('tab-s').className = type === 'special' ? "flex-1 py-4 rounded-2xl text-[10px] font-black bg-blue-600 shadow-lg" : "flex-1 py-4 rounded-2xl text-[10px] font-black bg-white/5 opacity-50";
            
            let h = "";
            let count = type === 'normal' ? 12 : 8;
            let startPrice = type === 'normal' ? 200 : 5000;

            for(let i=1; i<=count; i++) {
                let cost = startPrice + (i * (type === 'normal' ? 500 : 2000));
                let daily = Math.floor(cost * (type === 'normal' ? 0.15 : 0.25));
                let total = daily * 30;
                
                h += `
                <div class="plan-card glass p-7 rounded-[2.5rem]">
                    <div class="plan-badge">${type === 'normal' ? 'LITE' : 'VIP'}</div>
                    <div class="flex justify-between items-start mb-6">
                        <div class="text-right">
                            <h4 class="text-xs font-black uppercase text-blue-400">Node Cluster v.${i}</h4>
                            <p class="text-2xl font-black mt-1 tracking-tighter">₨ ${cost.toLocaleString()}</p>
                        </div>
                        <img src="https://cdn-icons-png.flaticon.com/512/2091/2091665.png" class="w-12 h-12 opacity-20 grayscale invert">
                    </div>
                    <div class="grid grid-cols-3 gap-2 mb-6">
                        <div class="text-center bg-black/30 p-3 rounded-2xl border border-white/5">
                            <p class="text-[7px] opacity-40 uppercase">Daily</p>
                            <p class="text-[10px] font-bold text-green-500">₨ ${daily}</p>
                        </div>
                        <div class="text-center bg-black/30 p-3 rounded-2xl border border-white/5">
                            <p class="text-[7px] opacity-40 uppercase">Total</p>
                            <p class="text-[10px] font-bold text-blue-400">₨ ${total}</p>
                        </div>
                        <div class="text-center bg-black/30 p-3 rounded-2xl border border-white/5">
                            <p class="text-[7px] opacity-40 uppercase">Days</p>
                            <p class="text-[10px] font-bold">30D</p>
                        </div>
                    </div>
                    <button onclick="buyPlan(${cost})" class="w-full bg-white text-black py-4 rounded-2xl font-black text-[10px] uppercase tracking-widest active:scale-95 transition-all">Buy Investment</button>
                </div>`;
            }
            document.getElementById('plans-container').innerHTML = h;
        }

        async function buyPlan(c) {
            if(userObj.balance < c) return alert("Insufficient Balance! Please Deposit.");
            await db.collection("users").doc(auth.currentUser.displayName).update({ balance: firebase.firestore.FieldValue.increment(-c) });
            alert("Plan Activated Successfully!");
        }

        // Exact Spin Logic based on Image
        async function executeSpin() {
            const cost = userObj.spinCount > 0 ? 50 : 0;
            if(userObj.balance < cost) return alert("Balance low!");
            
            const btn = document.getElementById('spin-btn');
            btn.disabled = true;

            const prizes = [
                { label: "₨ 100", angle: 0, win: 100 },
                { label: "₨ 50", angle: 45, win: 50 },
                { label: "₨ 10", angle: 90, win: 10 },
                { label: "₨ 5", angle: 135, win: 5 },
                { label: "₨ 2", angle: 180, win: 2 },
                { label: "₨ 10", angle: 225, win: 10 },
                { label: "₨ 2", angle: 270, win: 2 },
                { label: "₨ 5", angle: 315, win: 5 }
            ];

            const p = prizes[Math.floor(Math.random() * prizes.length)];
            const finalRot = 3600 + (360 - p.angle);
            currentRot += finalRot;
            
            document.getElementById('wheel').style.transform = `rotate(${currentRot}deg)`;
            
            setTimeout(async () => {
                await db.collection("users").doc(auth.currentUser.displayName).update({
                    balance: firebase.firestore.FieldValue.increment(p.win - cost),
                    spinCount: firebase.firestore.FieldValue.increment(1)
                });
                alert(`Wheel stopped at ${p.label}! Winner!`);
                btn.disabled = false;
            }, 5000);
        }

        async function submitTx(type) {
            const amt = (type === 'Deposit') ? document.getElementById('d-amt').value : document.getElementById('w-amt').value;
            if(!amt || amt < 100) return alert("Min amount is 100!");
            
            await db.collection("txs").add({
                user: auth.currentUser.displayName,
                amount: parseInt(amt),
                type: type,
                status: "Pending",
                time: Date.now(),
                tid: (type === 'Deposit') ? document.getElementById('d-tid').value : "W-REQ"
            });
            alert("Request broadcasted to terminal.");
        }

        function loadHistory(name) {
            db.collection("txs").where("user", "==", name).orderBy("time", "desc").onSnapshot(s => {
                let h = "";
                s.forEach(doc => {
                    const d = doc.data();
                    const color = d.status === "Approved" ? "text-green-500" : (d.status === "Pending" ? "text-yellow-500" : "text-red-500");
                    h += `<div class="glass p-5 flex justify-between items-center border-white/5">
                        <div class="text-right"><p class="text-[10px] font-black">${d.type}</p><p class="text-[8px] opacity-30">${new Date(d.time).toLocaleDateString()}</p></div>
                        <div class="text-left"><p class="font-black text-sm">₨ ${d.amount.toLocaleString()}</p><p class="text-[8px] font-bold ${color}">${d.status}</p></div>
                    </div>`;
                });
                document.getElementById('history-list').innerHTML = h || "<p class='text-center opacity-20 py-10 text-xs'>No Activity Found</p>";
            });
        }

        // Admin Security
        function handleAdminTap() { tapCount++; if(tapCount >= 10) { let k = prompt("Master Access Key:"); if(k === "net204") loadAdmin(); tapCount = 0; } }
        function loadAdmin() {
            document.getElementById('admin-ui').classList.remove('hidden');
            db.collection("txs").where("status", "==", "Pending").onSnapshot(s => {
                let h = "";
                s.forEach(doc => {
                    const d = doc.data();
                    h += `<div class="glass p-5 mb-4 border-red-500/20">
                        <p class="text-[10px] font-black text-blue-400 uppercase">${d.user} (${d.type})</p>
                        <p class="text-2xl font-black my-2">₨ ${d.amount}</p>
                        <p class="text-[9px] opacity-40 mb-4">TID: ${d.tid}</p>
                        <div class="flex gap-2">
                            <button onclick="approveTx('${doc.id}', '${d.user}', ${d.amount}, '${d.type}')" class="bg-green-600 flex-1 py-3 rounded-xl font-black text-[10px]">APPROVE</button>
                            <button onclick="db.collection('txs').doc('${doc.id}').update({status:'Rejected'})" class="bg-red-600 flex-1 py-3 rounded-xl font-black text-[10px]">REJECT</button>
                        </div>
                    </div>`;
                });
                document.getElementById('adm-tx-list').innerHTML = h || "All clear, sweetie!";
            });
        }

        async function approveTx(id, u, a, type) {
            await db.collection("txs").doc(id).update({ status: "Approved" });
            const val = type === "Deposit" ? a : -a;
            await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(val) });
        }

        function startFakePopups() {
            const names = ["احمد", "علی", "فاطمہ", "عمر", "زین", "حمزہ", "سارہ"];
            setInterval(() => {
                const n = names[Math.floor(Math.random()*names.length)];
                const div = document.createElement('div');
                div.className = "pop-notify";
                div.innerHTML = `<i class="fa-solid fa-circle-check text-green-500"></i> <span>${n} نے ₨ ${Math.floor(Math.random()*5000+500)} کا منافع نکلوایا</span>`;
                document.getElementById('notification-box').appendChild(div);
                setTimeout(() => div.remove(), 4000);
            }, 8000);
        }

        function showPage(p) {
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.getElementById('p-'+p).classList.add('active-page');
            document.querySelectorAll('.nav-btn').forEach(btn => btn.classList.remove('nav-active'));
            if(document.getElementById('nav-'+p)) document.getElementById('nav-'+p).classList.add('nav-active');
            window.scrollTo(0,0);
        }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }
        function copyRef() { const el = document.getElementById('ref-link'); el.select(); document.execCommand('copy'); alert("Link Copied!"); }
    </script>
</body>
</html>
