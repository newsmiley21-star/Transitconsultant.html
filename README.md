<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Transit Pro Gabon - Cloud Sync</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    
    <!-- SDK Firebase -->
    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInWithEmailAndPassword, onAuthStateChanged, signOut } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, collection, addDoc, onSnapshot, query, doc, updateDoc, deleteDoc, orderBy } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        // --- CONFIGURATION FIREBASE ---
        const firebaseConfig = {
            apiKey: "AIzaSyBtXUVcAaJ7UlOUB7VwVlNC6TSA9fbUymQ",
            authDomain: "transitpro-gabon.firebaseapp.com",
            projectId: "transitpro-gabon",
            storageBucket: "transitpro-gabon.firebasestorage.app",
            messagingSenderId: "995083780841",
            appId: "1:995083780841:web:1d811f5bbba5bfdee22678"
        };

        // Initialisation
        const app = initializeApp(firebaseConfig);
        const auth = getAuth(app);
        const db = getFirestore(app);

        // Variables Globales
        let currentMissions = [];
        let userRole = "Agent"; 

        // --- AUTHENTIFICATION ---
        window.handleAuth = async (event) => {
            event.preventDefault();
            const email = document.getElementById('auth-email').value;
            const pass = document.getElementById('auth-password').value;
            const errorMsg = document.getElementById('auth-error');
            const btn = event.target.querySelector('button');

            btn.disabled = true;
            btn.innerHTML = '<div class="loader w-5 h-5 border-2 rounded-full border-white/30 border-t-white animate-spin mx-auto"></div>';

            try {
                await signInWithEmailAndPassword(auth, email, pass);
            } catch (error) {
                console.error(error);
                errorMsg.classList.remove('hidden');
                btn.disabled = false;
                btn.innerText = "Réessayer";
            }
        };

        window.logout = () => signOut(auth);

        onAuthStateChanged(auth, (user) => {
            if (user) {
                document.getElementById('auth-screen').classList.add('hidden');
                document.getElementById('app-content').classList.remove('hidden');
                document.getElementById('user-display-email').innerText = user.email;
                
                if(user.email.includes('logistique')) userRole = "Logistique";
                else if(user.email.includes('douane')) userRole = "Inspecteur Douane";
                else userRole = "Agent Transit";
                
                document.getElementById('user-role-tag').innerText = "Rôle: " + userRole;
                startDataSync(); 
            } else {
                document.getElementById('auth-screen').classList.remove('hidden');
                document.getElementById('app-content').classList.add('hidden');
            }
        });

        // --- SYNCHRONISATION DES DONNÉES ---
        function startDataSync() {
            // Tri par date de création (le plus récent en haut)
            const q = query(collection(db, "missions"), orderBy("createdAt", "desc"));
            onSnapshot(q, (snapshot) => {
                currentMissions = [];
                snapshot.forEach((doc) => {
                    currentMissions.push({ id: doc.id, ...doc.data() });
                });
                render();
            }, (error) => {
                console.error("Erreur Firestore:", error);
            });
        }

        // --- ACTIONS MÉTIER ---
        window.createMission = async () => {
            const client = document.getElementById('client-name').value;
            const val = parseFloat(document.getElementById('cfr-value').value);
            
            if (!client || !val) {
                alert("Veuillez remplir le nom du client et la valeur CFR.");
                return;
            }

            const missionData = {
                client,
                nif: document.getElementById('client-nif').value || "7000Z",
                tel: document.getElementById('client-tel').value || "",
                cfr: val,
                cat: document.getElementById('category').value,
                ref: document.getElementById('ref-log').value || "SANS-REF",
                carrier: document.getElementById('vessel').value,
                mode: document.querySelector('input[name="mode"]:checked').value,
                qty: document.getElementById('qty').value || "1",
                weight: document.getElementById('weight').value || "100",
                origin: document.getElementById('origin').value || "EU",
                status: 'init',
                createdAt: Date.now(),
                date: new Date().toLocaleDateString('fr-FR'),
                createdBy: auth.currentUser.email
            };

            try {
                await addDoc(collection(db, "missions"), missionData);
                resetForm();
                switchTab('missions');
            } catch (e) {
                alert("Erreur Cloud : " + e.message);
            }
        };

        window.setStatus = async (id, newStatus) => {
            try {
                const missionRef = doc(db, "missions", id);
                await updateDoc(missionRef, { status: newStatus });
            } catch (e) { console.error(e); }
        };

        window.deleteM = async (id) => {
            if(confirm("Supprimer ce dossier du Cloud ?")) {
                try {
                    await deleteDoc(doc(db, "missions", id));
                } catch (e) { console.error(e); }
            }
        };

        // --- UI RENDER ---
        const CONFIG = {
            sante: { dd: 0.05, label: "SANTÉ (5%)" },
            machines: { dd: 0.10, label: "MACHINES (10%)" },
            pieces: { dd: 0.20, label: "PIÈCES (20%)" },
            vehicules: { dd: 0.30, label: "VÉHICULES (30%)" }
        };

        function getTaxes(cfr, cat) {
            const c = CONFIG[cat] || CONFIG.pieces;
            const caf = cfr * 1.01;
            const dd = caf * c.dd;
            const rs = caf * 0.015;
            const tva = (caf + dd + rs) * 0.18;
            return {
                total: Math.round(dd + rs + tva + 35000),
                label: c.label
            };
        }

        window.render = () => {
            const act = document.getElementById('list-active');
            const arc = document.getElementById('list-archive');
            act.innerHTML = ""; arc.innerHTML = "";

            if (currentMissions.length === 0) {
                act.innerHTML = `<div class="text-center py-20 text-slate-300 font-bold uppercase text-[10px] tracking-widest border-2 border-dashed border-slate-200 rounded-3xl">Aucun dossier sur le cloud</div>`;
            }

            currentMissions.forEach(m => {
                const r = getTaxes(m.cfr, m.cat);
                const html = `
                    <div class="bg-white rounded-3xl border border-slate-200 shadow-sm overflow-hidden mb-4 hover:border-blue-400 transition-all">
                        <div class="p-6 flex flex-col md:flex-row gap-6 items-center">
                            <div class="${m.mode === 'SEA' ? 'bg-blue-600' : 'bg-emerald-600'} w-12 h-12 flex items-center justify-center rounded-2xl text-white">
                                <i data-lucide="${m.mode === 'SEA' ? 'ship' : 'plane'}" class="w-6 h-6"></i>
                            </div>
                            <div class="flex-1 text-center md:text-left">
                                <h4 class="font-black text-slate-900 uppercase text-sm">${m.client}</h4>
                                <p class="text-[10px] font-bold text-slate-400 uppercase tracking-widest">${m.carrier} • REF: ${m.ref}</p>
                            </div>
                            <div class="text-right">
                                <p class="text-xl font-black text-slate-900">${format(r.total)} <span class="text-xs">FCFA</span></p>
                                <p class="text-[8px] font-black text-blue-500 uppercase">${r.label}</p>
                            </div>
                        </div>
                        <div class="bg-slate-50 px-6 py-3 flex justify-between items-center border-t border-slate-100">
                            <div class="flex items-center gap-3">
                                ${getStatusBadge(m.status)}
                                <span class="text-[9px] font-bold text-slate-400 uppercase italic">${m.createdBy.split('@')[0]}</span>
                            </div>
                            <div class="flex gap-2">
                                ${m.status !== 'fini' ? `
                                    <button onclick="setStatus('${m.id}', 'douane')" class="px-3 py-1.5 bg-slate-900 text-white rounded-xl text-[9px] font-black uppercase hover:bg-blue-600 transition-colors">Passer en Douane</button>
                                    <button onclick="setStatus('${m.id}', 'fini')" class="p-1.5 bg-emerald-100 text-emerald-600 rounded-xl hover:bg-emerald-200 transition-colors"><i data-lucide="check" class="w-4 h-4"></i></button>
                                ` : `
                                    <button onclick="deleteM('${m.id}')" class="p-1.5 bg-rose-50 text-rose-500 rounded-xl hover:bg-rose-100"><i data-lucide="trash-2" class="w-4 h-4"></i></button>
                                `}
                            </div>
                        </div>
                    </div>
                `;
                if(m.status === 'fini') arc.innerHTML += html;
                else act.innerHTML += html;
            });
            lucide.createIcons();
        };

        function resetForm() {
            document.getElementById('client-name').value = "";
            document.getElementById('cfr-value').value = "";
            document.getElementById('ref-log').value = "";
        }

        window.switchTab = (t) => {
            ['simulator', 'missions', 'archive'].forEach(id => {
                document.getElementById('section-'+id).classList.add('hidden');
                document.getElementById('tab-'+id).classList.remove('border-blue-600', 'text-blue-600');
            });
            document.getElementById('section-'+t).classList.remove('hidden');
            document.getElementById('tab-'+t).classList.add('border-blue-600', 'text-blue-600');
        };

        function format(n) { return new Intl.NumberFormat('fr-FR').format(n); }
        function getStatusBadge(s) {
            if(s === 'init') return '<span class="text-[8px] font-black px-2 py-0.5 rounded bg-amber-100 text-amber-600 uppercase">Ouvert</span>';
            if(s === 'douane') return '<span class="text-[8px] font-black px-2 py-0.5 rounded bg-blue-100 text-blue-600 uppercase animate-pulse">En Douane</span>';
            return '<span class="text-[8px] font-black px-2 py-0.5 rounded bg-slate-200 text-slate-500 uppercase">Terminé</span>';
        }

        document.addEventListener('DOMContentLoaded', () => lucide.createIcons());
    </script>

    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;700;900&display=swap');
        body { font-family: 'Inter', sans-serif; }
        .input-pro { width: 100%; padding: 0.85rem; border: 1px solid #e2e8f0; border-radius: 1rem; font-size: 0.875rem; outline: none; transition: all 0.2s; background: white; }
        .input-pro:focus { border-color: #3b82f6; box-shadow: 0 0 0 4px rgba(59, 130, 246, 0.1); }
        .loader { border-top-color: #3b82f6; animation: spinner 0.6s linear infinite; }
        @keyframes spinner { to { transform: rotate(360deg); } }
    </style>
</head>
<body class="bg-slate-50 min-h-screen">

    <!-- LOGIN SCREEN -->
    <div id="auth-screen" class="fixed inset-0 bg-slate-900 z-[100] flex items-center justify-center p-4">
        <div class="bg-white w-full max-w-md rounded-[3rem] p-12 shadow-2xl text-center">
            <div class="w-16 h-16 bg-blue-600 rounded-2xl flex items-center justify-center mx-auto mb-6 shadow-xl shadow-blue-200">
                <i data-lucide="cloud-lightning" class="text-white w-8 h-8"></i>
            </div>
            <h2 class="font-black text-2xl uppercase tracking-tighter italic mb-2">Transit<span class="text-blue-600">Pro</span></h2>
            <p class="text-[10px] font-bold text-slate-400 uppercase tracking-widest mb-10">Connexion Cloud Gabon</p>
            
            <form onsubmit="handleAuth(event)" class="space-y-4 text-left">
                <div>
                    <label class="text-[9px] font-black text-slate-400 uppercase mb-1.5 block ml-1">E-mail Professionnel</label>
                    <input type="email" id="auth-email" required class="input-pro font-bold" placeholder="agent@entreprise.ga">
                </div>
                <div>
                    <label class="text-[9px] font-black text-slate-400 uppercase mb-1.5 block ml-1">Mot de passe</label>
                    <input type="password" id="auth-password" required class="input-pro" placeholder="••••••••">
                </div>
                <button type="submit" class="w-full bg-slate-900 hover:bg-blue-600 text-white font-black py-4 rounded-2xl uppercase text-xs tracking-widest mt-4 shadow-xl transition-all">Accéder au Cloud</button>
                <p id="auth-error" class="hidden text-rose-500 text-[10px] font-black text-center mt-4 uppercase italic">Identifiants incorrects.</p>
            </form>
        </div>
    </div>

    <!-- MAIN APP -->
    <div id="app-content" class="hidden">
        <nav class="bg-slate-900 text-white p-4 sticky top-0 z-50">
            <div class="max-w-6xl mx-auto flex justify-between items-center px-4">
                <div class="flex items-center gap-2">
                    <i data-lucide="anchor" class="text-blue-500 w-5 h-5"></i>
                    <h1 class="font-black text-xl italic uppercase tracking-tighter">Transit<span class="text-blue-500">Pro</span></h1>
                </div>
                <div class="flex items-center gap-4">
                    <div class="text-right hidden sm:block">
                        <p id="user-display-email" class="text-[10px] font-black text-blue-400"></p>
                        <p id="user-role-tag" class="text-[8px] font-bold text-slate-500 uppercase"></p>
                    </div>
                    <button onclick="logout()" class="p-2.5 bg-slate-800 hover:bg-rose-900/30 text-slate-400 hover:text-rose-500 rounded-xl transition-all">
                        <i data-lucide="log-out" class="w-5 h-5"></i>
                    </button>
                </div>
            </div>
        </nav>

        <main class="max-w-4xl mx-auto mt-8 px-4 pb-20">
            <!-- TABS -->
            <div class="flex bg-slate-200/50 p-1.5 rounded-2xl mb-8 border border-slate-200 overflow-x-auto no-scrollbar">
                <button onclick="switchTab('simulator')" id="tab-simulator" class="flex-1 px-4 py-3 font-black text-[10px] uppercase rounded-xl border-b-2 border-blue-600 text-blue-600 bg-white shadow-sm whitespace-nowrap">Nouveau Dossier</button>
                <button onclick="switchTab('missions')" id="tab-missions" class="flex-1 px-4 py-3 font-black text-[10px] uppercase rounded-xl border-b-2 border-transparent text-slate-500 whitespace-nowrap">Missions Cloud</button>
                <button onclick="switchTab('archive')" id="tab-archive" class="flex-1 px-4 py-3 font-black text-[10px] uppercase rounded-xl border-b-2 border-transparent text-slate-500 whitespace-nowrap">Archives</button>
            </div>

            <!-- SIMULATEUR -->
            <section id="section-simulator" class="animate-in slide-in-from-bottom-4 duration-500">
                <div class="bg-white rounded-[2.5rem] p-10 shadow-sm border border-slate-200">
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-8">
                        <div class="space-y-6">
                            <div>
                                <label class="text-[10px] font-black text-slate-400 uppercase block mb-2 ml-1">Client / Importateur</label>
                                <input type="text" id="client-name" class="input-pro font-bold" placeholder="Nom complet">
                            </div>
                            <div>
                                <label class="text-[10px] font-black text-slate-400 uppercase block mb-2 ml-1">Valeur CFR (FCFA)</label>
                                <input type="number" id="cfr-value" class="input-pro font-black text-blue-600 text-lg" placeholder="Valeur Douane">
                            </div>
                        </div>
                        <div class="space-y-6">
                            <div>
                                <label class="text-[10px] font-black text-slate-400 uppercase block mb-2 ml-1">Catégorie Douanière</label>
                                <select id="category" class="input-pro font-bold">
                                    <option value="machines">Équipements & Machines (10%)</option>
                                    <option value="pieces">Consommables & Divers (20%)</option>
                                    <option value="vehicules">Véhicules de Transport (30%)</option>
                                    <option value="sante">Médicaments & Santé (5%)</option>
                                </select>
                            </div>
                            <div>
                                <label class="text-[10px] font-black text-slate-400 uppercase block mb-2 ml-1">Transporteur</label>
                                <select id="vessel" class="input-pro font-bold">
                                    <option>CMA CGM GABON</option>
                                    <option>MSC LIBREVILLE</option>
                                    <option>MAERSK LINE</option>
                                    <option>SKY GABON (AIR)</option>
                                    <option>DHL EXPRESS</option>
                                </select>
                            </div>
                        </div>
                    </div>
                    
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-4 mt-8 pt-8 border-t border-slate-100">
                        <div class="flex gap-4 p-2 bg-slate-100 rounded-2xl">
                            <label class="flex-1 flex items-center justify-center gap-2 cursor-pointer p-3 rounded-xl has-[:checked]:bg-white has-[:checked]:shadow-sm transition-all">
                                <input type="radio" name="mode" value="SEA" checked class="hidden">
                                <span class="text-[10px] font-black uppercase">Maritime</span>
                            </label>
                            <label class="flex-1 flex items-center justify-center gap-2 cursor-pointer p-3 rounded-xl has-[:checked]:bg-white has-[:checked]:shadow-sm transition-all">
                                <input type="radio" name="mode" value="AIR" class="hidden">
                                <span class="text-[10px] font-black uppercase">Aérien</span>
                            </label>
                        </div>
                        <input type="text" id="ref-log" class="input-pro uppercase font-mono font-bold" placeholder="REF BL ou LTA">
                    </div>

                    <button onclick="createMission()" class="w-full mt-8 bg-blue-600 hover:bg-blue-700 text-white font-black py-5 rounded-[1.5rem] uppercase text-xs tracking-widest shadow-2xl shadow-blue-200 transition-all flex justify-center items-center gap-3">
                        <i data-lucide="upload-cloud"></i> Synchroniser sur le Cloud
                    </button>
                    
                    <input type="hidden" id="qty" value="1"><input type="hidden" id="weight" value="100"><input type="hidden" id="origin" value="EU"><input type="hidden" id="client-nif" value="7000Z"><input type="hidden" id="client-tel" value="">
                </div>
            </section>

            <!-- MISSIONS LIST -->
            <section id="section-missions" class="hidden space-y-4">
                <div id="list-active" class="space-y-4">
                    <div class="text-center py-20 text-slate-400">
                        <div class="loader w-8 h-8 border-4 rounded-full border-slate-200 border-t-blue-500 animate-spin mx-auto mb-4"></div>
                        <p class="text-[10px] font-black uppercase tracking-widest">Récupération des dossiers...</p>
                    </div>
                </div>
            </section>

            <!-- ARCHIVES LIST -->
            <section id="section-archive" class="hidden space-y-4 opacity-70">
                <div id="list-archive"></div>
            </section>
        </main>
    </div>

</body>
</html>
