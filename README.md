<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>Vestify Elite | Institutional Terminal</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --primary: #3b82f6; --bg: #0b0f1a; --card: #161b2c; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: #f1f5f9; overflow-x: hidden; }
        .glass { background: rgba(22, 27, 44, 0.8); backdrop-filter: blur(10px); border: 1px solid rgba(255,255,255,0.05); border-radius: 24px; }
        .page { display: none; animation: slideUp 0.4s ease; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        input, select { background: #0f172a !important; border: 1px solid rgba(255,255,255,0.1) !important; color: white !important; outline: none; }
    </style>
</head>
<body class="min-h-screen pb-32">

    <div id="admin-panel" class="hidden fixed inset-0 z-[9000] bg-black p-6 overflow-y-auto">
        <div class="flex justify-between items-center mb-6">
            <h2 class="text-2xl font-black text-blue-500 italic uppercase">System Control</h2>
            <button onclick="closeAdmin()" class="text-3xl">&times;</button>
        </div>
        <div id="admin-requests" class="space-y-4"></div>
    </div>

    <main id="app-ui" class="px-6 pt-10">
        <div id="p-home" class="page active-page">
            <div class="glass p-8 mb-6 text-center shadow-2xl border-blue-500/20">
                <p class="text-[10px] text-blue-400 font-bold uppercase tracking-widest mb-1">Available Capital</p>
                <h2 class="text-5xl font-black italic" id="v-bal">₨ 0.00</h2>
            </div>
            
            <div class="grid grid-cols-2 gap-4 mb-8">
                <button onclick="changePage('deposit')" class="glass p-4 font-black text-xs uppercase bg-blue-600/10 text-blue-400 border-blue-600/30">Deposit</button>
                <button onclick="changePage('withdraw')" class="glass p-4 font-black text-xs uppercase bg-red-600/10 text-red-400 border-red-600/30">Withdraw</button>
            </div>

            <h3 class="font-black text-lg mb-4 italic uppercase">Transaction History</h3>
            <div id="history-list" class="space-y-3 opacity-80"></div>
        </div>

        <div id="p-deposit" class="page">
            <h2 class="text-2xl font-black mb-6 italic uppercase">Funding Protocol</h2>
            <div class="glass p-6 space-y-4">
                <div>
                    <label class="text-[10px] font-bold opacity-50 uppercase">Select Method</label>
                    <select id="dep-method" class="w-full p-4 rounded-xl mt-1" onchange="updateDepInfo()">
                        <option value="easypaisa">EasyPaisa</option>
                        <option value="jazzcash">JazzCash</option>
                        <option value="sadapay">SadaPay</option>
                    </select>
                </div>
                <div class="bg-blue-600/10 p-4 rounded-xl border border-blue-600/20">
                    <p class="text-[10px] font-bold text-blue-400 uppercase">Send Payment To:</p>
                    <p id="dep-num" class="text-xl font-black mt-1">03379827882</p>
                    <p id="dep-name" class="text-[10px] opacity-60 uppercase">Account Name: Vestify Elite</p>
                </div>
                <input id="dep-amt" type="number" placeholder="Amount (Min 200)" class="w-full p-4 rounded-xl">
                <input id="dep-tid" type="text" placeholder="TID Number" class="w-full p-4 rounded-xl">
                <div>
                    <label class="text-[10px] font-bold opacity-50 uppercase">Upload Proof Screen</label>
                    <input id="dep-img" type="file" accept="image/*" class="w-full mt-1 text-xs">
                </div>
                <button onclick="submitDeposit()" class="w-full bg-blue-600 py-4 rounded-2xl font-black uppercase text-xs">Submit Request</button>
            </div>
        </div>

        <div id="p-withdraw" class="page">
            <h2 class="text-2xl font-black mb-6 italic uppercase">Withdraw Capital</h2>
            <div class="glass p-6 space-y-4">
                <select id="wd-method" class="w-full p-4 rounded-xl">
                    <option value="easypaisa">EasyPaisa</option>
                    <option value="jazzcash">JazzCash</option>
                    <option value="sadapay">SadaPay</option>
                </select>
                <input id="wd-amt" type="number" placeholder="Amount (Min 100)" class="w-full p-4 rounded-xl">
                <input id="wd-acc" type="text" placeholder="Account Number" class="w-full p-4 rounded-xl">
                <input id="wd-name" type="text" placeholder="Account Holder Name" class="w-full p-4 rounded-xl">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-4 rounded-2xl font-black uppercase text-xs">Confirm Withdrawal</button>
            </div>
        </div>
    </main>

    <nav id="bottom-nav" class="fixed bottom-6 left-6 right-6 h-20 glass flex justify-around items-center z-[4000]">
        <button onclick="changePage('home')" class="flex flex-col items-center"><i class="fa-solid fa-house"></i><span class="text-[8px] font-bold mt-1">Home</span></button>
        <button onclick="changePage('deposit')" class="flex flex-col items-center"><i class="fa-solid fa-plus"></i><span class="text-[8px] font-bold mt-1">Add</span></button>
        <button onclick="changePage('withdraw')" class="flex flex-col items-center"><i class="fa-solid fa-minus"></i><span class="text-[8px] font-bold mt-1">Take</span></button>
        <button onclick="tapLogo()" class="flex flex-col items-center"><i class="fa-solid fa-gear"></i><span class="text-[8px] font-bold mt-1">Admin</span></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyC9ofJ1KxRXHnxilpU9gyI87D3BSOZ9v1g", authDomain: "vestify-991f2.firebaseapp.com", projectId: "vestify-991f2", storageBucket: "vestify-991f2.firebasestorage.app", messagingSenderId: "799007097733", appId: "1:799007097733:web:ed3b35b6c4e51dc2e7baec" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        let userObj = { name: "User", balance: 0 };
        let tapCount = 0;

        // Admin Secret
        function tapLogo() { tapCount++; if(tapCount>=5){ const p = prompt("Key:"); if(p==="net204"){ document.getElementById('admin-panel').classList.remove('hidden'); loadAdminRequests(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); }

        function updateDepInfo() {
            const m = document.getElementById('dep-method').value;
            const n = document.getElementById('dep-num');
            if(m === 'easypaisa') n.innerText = "03379827882";
            else n.innerText = "03705519562";
        }

        async function submitDeposit() {
            const amt = parseInt(document.getElementById('dep-amt').value);
            const tid = document.getElementById('dep-tid').value;
            const file = document.getElementById('dep-img').files[0];
            if(amt < 200) return alert("Min 200 PKR");
            if(!tid || !file) return alert("Fill all details");

            const reader = new FileReader();
            reader.onloadend = async () => {
                await db.collection("requests").add({
                    type: 'Deposit', amount: amt, tid: tid, proof: reader.result, method: document.getElementById('dep-method').value,
                    user: "User", status: 'Pending', time: Date.now()
                });
                alert("Deposit Pending Approval!");
                changePage('home');
            };
            reader.readAsDataURL(file);
        }

        async function submitWithdraw() {
            const amt = parseInt(document.getElementById('wd-amt').value);
            const acc = document.getElementById('wd-acc').value;
            const name = document.getElementById('wd-name').value;
            if(amt < 100) return alert("Min 100 PKR");
            if(!acc || !name) return alert("Fill details");

            await db.collection("requests").add({
                type: 'Withdraw', amount: amt, acc: acc, accName: name, method: document.getElementById('wd-method').value,
                user: "User", status: 'Pending', time: Date.now()
            });
            alert("Withdrawal Pending!");
            changePage('home');
        }

        function loadAdminRequests() {
            db.collection("requests").where("status", "==", "Pending").onSnapshot(snap => {
                let html = '';
                snap.forEach(doc => {
                    const data = doc.data();
                    html += `<div class="glass p-4 text-[10px]">
                        <p class="font-black text-blue-400 uppercase">${data.type} Request</p>
                        <p>User: ${data.user} | Amount: ₨ ${data.amount}</p>
                        ${data.type==='Deposit' ? `<p>TID: ${data.tid}</p><img src="${data.proof}" class="w-full mt-2 rounded-lg">` : `<p>Acc: ${data.acc} (${data.accName})</p>`}
                        <div class="flex gap-2 mt-4">
                            <button onclick="processReq('${doc.id}', 'Approved', ${data.amount})" class="bg-green-600 px-4 py-2 rounded">Approve</button>
                            <button onclick="processReq('${doc.id}', 'Rejected', 0)" class="bg-red-600 px-4 py-2 rounded">Reject</button>
                        </div>
                    </div>`;
                });
                document.getElementById('admin-requests').innerHTML = html || '<p class="text-center opacity-30">No pending requests</p>';
            });
        }

        async function processReq(id, status, amt) {
            await db.collection("requests").doc(id).update({ status: status });
            if(status === 'Approved') {
                const userRef = db.collection("users").doc("User");
                const current = (await userRef.get()).data()?.balance || 0;
                await userRef.set({ balance: current + amt }, { merge: true });
            }
            alert("Request " + status);
        }

        // Real-time Updates
        db.collection("users").doc("User").onSnapshot(doc => {
            const bal = doc.data()?.balance || 0;
            document.getElementById('v-bal').innerText = "₨ " + bal.toLocaleString();
        });

        db.collection("requests").orderBy("time", "desc").limit(10).onSnapshot(snap => {
            let html = '';
            snap.forEach(doc => {
                const d = doc.data();
                const color = d.status === 'Approved' ? 'text-green-400' : (d.status === 'Rejected' ? 'text-red-400' : 'text-yellow-400');
                html += `<div class="glass p-4 flex justify-between items-center text-[10px]">
                    <div><p class="font-bold">${d.type} - ${d.method}</p><p class="opacity-50">₨ ${d.amount}</p></div>
                    <div class="font-black ${color} uppercase">${d.status}</div>
                </div>`;
            });
            document.getElementById('history-list').innerHTML = html;
        });

    </script>
</body>
</html>
