<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Transit Pro Gabon - Expert ERP</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;500;600;700;800&display=swap');
        
        :root { --primary: #2563eb; --secondary: #0f172a; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background-color: #f8fafc; color: #1e293b; }

        .glass-card { background: white; border: 1px solid #e2e8f0; box-shadow: 0 4px 6px -1px rgb(0 0 0 / 0.05); }
        .tab-active { background: #2563eb; color: white; box-shadow: 0 10px 15px -3px rgba(37, 99, 235, 0.2); }
        
        @media print {
            body * { visibility: hidden; }
            #print-section, #print-section * { visibility: visible; }
            #print-section { position: absolute; left: 0; top: 0; width: 100%; }
        }

        .input-pro { @apply w-full px-4 py-2 bg-slate-50 border border-slate-200 rounded-lg outline-none focus:border-blue-500 focus:ring-2 focus:ring-blue-500/10 transition-all text-sm; }
        .btn-primary { @apply bg-blue-600 hover:bg-blue-700 text-white font-bold py-2 px-6 rounded-lg transition-all active:scale-95 flex items-center gap-2; }
        
        .doc-pill { @apply flex items-center gap-2 px-3 py-1.5 rounded-full text-[10px] font-bold border transition-all; }
        .doc-missing { @apply bg-rose-50 border-rose-200 text-rose-600; }
        .doc-ok { @apply bg-emerald-50 border-emerald-200 text-emerald-600; }
    </style>
</head>
<body class="min-h-screen pb-20">

    <!-- AUTHENTIFICATION -->
    <div id="lock-screen" class="fixed inset-0 bg-slate-900 z-[9999] flex items-center justify-center p-6">
        <div class="w-full max-w-sm bg-white rounded-3xl p-8 text-center shadow-2xl">
            <div class="w-16 h-16 bg-blue-600 rounded-2xl flex items-center justify-center mx-auto mb-6">
                <i data-lucide="shield-check" class="text-white w-8 h-8"></i>
            </div>
            <h2 class="text-xl font-black italic">TRANSIT<span class="text-blue-500">PRO</span></h2>
            <p class="text-slate-400 text-[10px] font-bold uppercase tracking-widest mt-1 mb-8">Expert Console</p>
            <div class="grid grid-cols-3 gap-3" id="keypad"></div>
        </div>
    </div>

    <!-- UI PRINCIPALE -->
    <div id="app-interface" class="hidden">
        <nav class="bg-white border-b border-slate-200 p-4 sticky top-0 z-50 no-print">
            <div class="max-w-7xl mx-auto flex justify-between items-center">
                <div class="flex items-center gap-4">
                    <span class="font-black italic text-xl tracking-tighter">TRANSIT<span class="text-blue-500">PRO</span></span>
                    <span class="hidden md:block h-6 w-px bg-slate-200 mx-2"></span>
                    <span id="date-now" class="hidden md:block text-[10px] font-black text-slate-400 uppercase"></span>
                </div>
                <div class="flex items-center gap-3">
                    <button onclick="switchTab('new')" class="btn-primary py-1.5 text-xs"><i data-lucide="plus" class="w-4 h-4"></i> Nouveau</button>
                    <button onclick="location.reload()" class="p-2 text-slate-400 hover:text-slate-900"><i data-lucide="log-out" class="w-5 h-5"></i></button>
                </div>
            </div>
        </nav>

        <!-- DASHBOARD STATS -->
        <div class="max-w-7xl mx-auto px-4 lg:px-8 mt-8 grid grid-cols-1 md:grid-cols-4 gap-4 no-print">
            <div class="glass-card p-4 rounded-2xl border-l-4 border-l-blue-500">
                <p class="text-[10px] font-black text-slate-400 uppercase">En cours</p>
                <p id="stat-active" class="text-2xl font-black">0</p>
            </div>
            <div class="glass-card p-4 rounded-2xl border-l-4 border-l-orange-500">
                <p class="text-[10px] font-black text-slate-400 uppercase">Douane</p>
                <p id="stat-douane" class="text-2xl font-black">0</p>
            </div>
            <div class="glass-card p-4 rounded-2xl border-l-4 border-l-rose-500">
                <p class="text-[10px] font-black text-slate-400 uppercase">Documents Manquants</p>
                <p id="stat-docs" class="text-2xl font-black">0</p>
            </div>
            <div class="glass-card p-4 rounded-2xl border-l-4 border-l-emerald-500">
                <p class="text-[10px] font-black text-slate-400 uppercase">Terminés</p>
                <p id="stat-done" class="text-2xl font-black">0</p>
            </div>
        </div>

        <main class="max-w-7xl mx-auto p-4 lg:p-8 space-y-6">
            
            <!-- TABS NAVIGATION -->
            <div class="flex gap-2 no-print overflow-x-auto pb-2">
                <button onclick="switchTab('list')" id="btn-list" class="px-6 py-2 rounded-full text-xs font-bold transition-all border border-slate-200 hover:bg-slate-50 whitespace-nowrap">📋 Missions Actives</button>
                <button onclick="switchTab('new')" id="btn-new" class="px-6 py-2 rounded-full text-xs font-bold transition-all border border-slate-200 hover:bg-slate-50 whitespace-nowrap">✨ Nouveau Dossier</button>
                <button onclick="switchTab('clients')" id="btn-clients" class="px-6 py-2 rounded-full text-xs font-bold transition-all border border-slate-200 hover:bg-slate-50 whitespace-nowrap">👥 Clients</button>
                <button onclick="switchTab('archive')" id="btn-archive" class="px-6 py-2 rounded-full text-xs font-bold transition-all border border-slate-200 hover:bg-slate-50 whitespace-nowrap">📦 Archives</button>
            </div>

            <!-- SECTIONS -->
            <section id="sec-new" class="hidden space-y-6">
                <div class="glass-card rounded-3xl p-8">
                    <h2 class="text-lg font-black mb-6 uppercase tracking-tight flex items-center gap-2">
                        <i data-lucide="file-plus" class="text-blue-600"></i> Ouverture de Dossier
                    </h2>
                    <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
                        <div class="space-y-4">
                            <h3 class="text-[10px] font-black text-slate-400 uppercase">Identité</h3>
                            <select id="f-client" class="input-pro" onchange="fillNif()">
                                <option value="">-- Client --</option>
                            </select>
                            <input type="text" id="f-nif" class="input-pro" placeholder="NIF Automatique" readonly>
                            <input type="text" id="f-ref" class="input-pro uppercase" placeholder="Réf BL / LTA">
                        </div>
                        <div class="space-y-4">
                            <h3 class="text-[10px] font-black text-slate-400 uppercase">Logistique</h3>
                            <div class="grid grid-cols-2 gap-2">
                                <select id="f-mode" class="input-pro">
                                    <option value="Maritime">🚢 Maritime</option>
                                    <option value="Aérien">✈️ Aérien</option>
                                    <option value="Terrestre">🚛 Terrestre</option>
                                </select>
                                <input type="text" id="f-units" class="input-pro" placeholder="Ex: 1x20' TC">
                            </div>
                            <div class="grid grid-cols-2 gap-2">
                                <input type="number" id="f-weight" class="input-pro" placeholder="Poids (kg)">
                                <input type="number" id="f-volume" class="input-pro" placeholder="Vol (m³)">
                            </div>
                            <input type="date" id="f-arrival" class="input-pro">
                        </div>
                        <div class="space-y-4">
                            <h3 class="text-[10px] font-black text-slate-400 uppercase">Douane & Valeur</h3>
                            <input type="number" id="f-cfr" class="input-pro font-bold text-blue-600" placeholder="Valeur CFR (FCFA)">
                            <select id="f-cat" class="input-pro">
                                <option value="cat1">Cat 1 (5%) - Basique</option>
                                <option value="cat2">Cat 2 (10%) - Mat. Prem</option>
                                <option value="cat3">Cat 3 (20%) - Équipement</option>
                                <option value="cat4">Cat 4 (30%) - Luxe</option>
                            </select>
                        </div>
                    </div>
                    <div class="mt-8 flex justify-end">
                        <button onclick="saveMission()" class="btn-primary">Créer le dossier</button>
                    </div>
                </div>
            </section>

            <section id="sec-list" class="space-y-4">
                <div id="mission-grid" class="grid grid-cols-1 lg:grid-cols-2 gap-6"></div>
            </section>

            <section id="sec-clients" class="hidden grid grid-cols-1 md:grid-cols-3 gap-6">
                <div class="glass-card p-6 rounded-2xl h-fit">
                    <h3 class="font-black text-xs uppercase mb-4">Ajouter Client</h3>
                    <div class="space-y-3">
                        <input type="text" id="c-name" class="input-pro" placeholder="Raison Sociale">
                        <input type="text" id="c-nif" class="input-pro" placeholder="NIF">
                        <button onclick="saveClient()" class="btn-primary w-full text-xs">Enregistrer</button>
                    </div>
                </div>
                <div class="md:col-span-2 glass-card rounded-2xl overflow-hidden">
                    <table class="w-full text-left text-xs">
                        <thead class="bg-slate-50 border-b">
                            <tr><th class="p-4">CLIENT</th><th class="p-4">NIF</th><th class="p-4">ACTIONS</th></tr>
                        </thead>
                        <tbody id="client-table"></tbody>
                    </table>
                </div>
            </section>

        </main>
    </div>

    <!-- ZONE D'IMPRESSION -->
    <div id="print-section" class="bg-white"></div>

    <script>
        const PIN_ADMIN = "6122";
        let currentPin = "";
        let missions = JSON.parse(localStorage.getItem('tp_expert_m')) || [];
        let clients = JSON.parse(localStorage.getItem('tp_expert_c')) || [];

        const DOCS_REQUIRED = ["BL / LTA", "Facture Fournisseur", "Liste de Colisage", "Certificat d'Origine", "Assurance"];

        window.onload = () => {
            renderKeypad();
            document.getElementById('date-now').innerText = new Date().toLocaleDateString('fr-FR', { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' });
            updateDropdowns();
            lucide.createIcons();
        };

        function renderKeypad() {
            const kp = document.getElementById('keypad');
            [1,2,3,4,5,6,7,8,9,'C',0,'⌫'].forEach(n => {
                const b = document.createElement('button');
                b.className = "h-14 rounded-xl bg-slate-100 font-bold hover:bg-blue-600 hover:text-white transition-all";
                b.innerText = n;
                b.onclick = () => {
                    if(n === 'C') currentPin = "";
                    else if(n === '⌫') currentPin = currentPin.slice(0,-1);
                    else currentPin += n;
                    if(currentPin === PIN_ADMIN) {
                        document.getElementById('lock-screen').classList.add('hidden');
                        document.getElementById('app-interface').classList.remove('hidden');
                        switchTab('list');
                    }
                };
                kp.appendChild(b);
            });
        }

        function switchTab(t) {
            ['new', 'list', 'archive', 'clients'].forEach(s => {
                document.getElementById('sec-'+s)?.classList.add('hidden');
                document.getElementById('btn-'+s)?.classList.remove('tab-active');
            });
            document.getElementById('sec-'+(t === 'archive' ? 'list' : t)).classList.remove('hidden');
            document.getElementById('btn-'+t).classList.add('tab-active');
            if(t === 'list' || t === 'archive') renderMissions();
            if(t === 'clients') renderClients();
            updateStats();
        }

        function saveClient() {
            const n = document.getElementById('c-name').value;
            const ni = document.getElementById('c-nif').value;
            if(!n || !ni) return;
            clients.push({ id: Date.now(), name: n.toUpperCase(), nif: ni.toUpperCase() });
            localStorage.setItem('tp_expert_c', JSON.stringify(clients));
            document.getElementById('c-name').value = "";
            document.getElementById('c-nif').value = "";
            renderClients();
            updateDropdowns();
        }

        function updateDropdowns() {
            const sel = document.getElementById('f-client');
            sel.innerHTML = '<option value="">-- Client --</option>' + clients.map(c => `<option value="${c.id}">${c.name}</option>`).join('');
        }

        function fillNif() {
            const cid = document.getElementById('f-client').value;
            const c = clients.find(x => x.id == cid);
            document.getElementById('f-nif').value = c ? c.nif : "";
        }

        function saveMission() {
            const cid = document.getElementById('f-client').value;
            const c = clients.find(x => x.id == cid);
            if(!c) return;
            const m = {
                id: Date.now(),
                client: c.name,
                nif: c.nif,
                ref: document.getElementById('f-ref').value || "S/REF",
                mode: document.getElementById('f-mode').value,
                units: document.getElementById('f-units').value || "N/A",
                weight: document.getElementById('f-weight').value || 0,
                volume: document.getElementById('f-volume').value || 0,
                arrival: document.getElementById('f-arrival').value,
                cfr: parseFloat(document.getElementById('f-cfr').value) || 0,
                cat: document.getElementById('f-cat').value,
                status: 'ouvert',
                docs: {} // Format: { "Nom Doc": true/false }
            };
            missions.unshift(m);
            localStorage.setItem('tp_expert_m', JSON.stringify(missions));
            switchTab('list');
        }

        function renderMissions() {
            const grid = document.getElementById('mission-grid');
            grid.innerHTML = "";
            const isArchive = document.getElementById('btn-archive').classList.contains('tab-active');
            const filtered = missions.filter(m => isArchive ? m.status === 'fini' : m.status !== 'fini');

            filtered.forEach(m => {
                const taxes = calculateTaxes(m.cfr, m.cat);
                const delay = getDelay(m.arrival);
                const docCount = DOCS_REQUIRED.filter(d => m.docs[d]).length;

                const div = document.createElement('div');
                div.className = "glass-card rounded-3xl p-6 relative overflow-hidden";
                div.innerHTML = `
                    <div class="flex justify-between items-start mb-6">
                        <div>
                            <span class="text-[9px] font-black text-blue-600 bg-blue-50 px-2 py-1 rounded uppercase tracking-tighter">DOSSIER #${m.id.toString().slice(-4)}</span>
                            <h3 class="text-lg font-black mt-2">${m.client}</h3>
                            <p class="text-[10px] font-bold text-slate-400 uppercase">${m.ref} • ${m.mode}</p>
                        </div>
                        <div class="text-right">
                            <p class="text-xl font-black text-slate-900">${new Intl.NumberFormat('fr-FR').format(taxes.total)} <span class="text-[10px]">CFA</span></p>
                            <p class="text-[9px] font-bold text-slate-400 uppercase">Estimation Douane</p>
                        </div>
                    </div>

                    <div class="grid grid-cols-3 gap-2 mb-6">
                        <div class="p-3 bg-slate-50 rounded-2xl text-center">
                            <p class="text-[8px] font-black text-slate-400 uppercase">Docs</p>
                            <p class="text-xs font-black ${docCount < DOCS_REQUIRED.length ? 'text-rose-600' : 'text-emerald-600'}">${docCount}/${DOCS_REQUIRED.length}</p>
                        </div>
                        <div class="p-3 bg-slate-50 rounded-2xl text-center">
                            <p class="text-[8px] font-black text-slate-400 uppercase">ETA/Arr</p>
                            <p class="text-xs font-black ${delay < 0 ? 'text-rose-600' : 'text-slate-900'}">${delay} Jours</p>
                        </div>
                        <div class="p-3 bg-slate-50 rounded-2xl text-center">
                            <p class="text-[8px] font-black text-slate-400 uppercase">Statut</p>
                            <p class="text-[9px] font-black uppercase text-blue-600">${m.status}</p>
                        </div>
                    </div>

                    <div class="flex flex-wrap gap-1 mb-6">
                        ${DOCS_REQUIRED.map(d => `
                            <button onclick="toggleDoc(${m.id}, '${d}')" class="doc-pill ${m.docs[d] ? 'doc-ok' : 'doc-missing'}">
                                ${d}
                            </button>
                        `).join('')}
                    </div>

                    <div class="flex justify-between items-center pt-4 border-t border-slate-100">
                        <div class="flex gap-2">
                            <button onclick="printM(${m.id})" class="p-2 hover:bg-slate-100 rounded-lg"><i data-lucide="printer" class="w-4 h-4"></i></button>
                            <button onclick="deleteM(${m.id})" class="p-2 hover:bg-rose-50 text-rose-500 rounded-lg"><i data-lucide="trash-2" class="w-4 h-4"></i></button>
                        </div>
                        <div class="flex gap-2">
                            ${m.status === 'ouvert' ? `<button onclick="setStatus(${m.id}, 'douane')" class="px-4 py-1.5 bg-slate-900 text-white rounded-lg text-[10px] font-black uppercase">Passer en Douane</button>` : ''}
                            ${m.status === 'douane' ? `<button onclick="setStatus(${m.id}, 'fini')" class="px-4 py-1.5 bg-emerald-600 text-white rounded-lg text-[10px] font-black uppercase">Dossier Livré</button>` : ''}
                        </div>
                    </div>
                `;
                grid.appendChild(div);
            });
            lucide.createIcons();
        }

        function calculateTaxes(cfr, cat) {
            const rates = { cat1: 0.05, cat2: 0.10, cat3: 0.20, cat4: 0.30 };
            const caf = cfr * 1.01;
            const dd = caf * rates[cat];
            const rs = caf * 0.015;
            const tva = (caf + dd + rs) * 0.18;
            return { total: Math.round(dd + rs + tva + 45000), dd, rs, tva };
        }

        function getDelay(dateStr) {
            if(!dateStr) return 0;
            const diff = new Date(dateStr) - new Date();
            return Math.ceil(diff / (1000 * 60 * 60 * 24));
        }

        function toggleDoc(mid, doc) {
            missions = missions.map(m => {
                if(m.id === mid) {
                    const newDocs = {...m.docs};
                    newDocs[doc] = !newDocs[doc];
                    return {...m, docs: newDocs};
                }
                return m;
            });
            saveAndRender();
        }

        function setStatus(id, s) {
            missions = missions.map(m => m.id === id ? {...m, status: s} : m);
            saveAndRender();
        }

        function deleteM(id) {
            if(confirm("Supprimer ce dossier définitivement ?")) {
                missions = missions.filter(m => m.id !== id);
                saveAndRender();
            }
        }

        function saveAndRender() {
            localStorage.setItem('tp_expert_m', JSON.stringify(missions));
            renderMissions();
            updateStats();
        }

        function updateStats() {
            document.getElementById('stat-active').innerText = missions.filter(m => m.status === 'ouvert').length;
            document.getElementById('stat-douane').innerText = missions.filter(m => m.status === 'douane').length;
            document.getElementById('stat-done').innerText = missions.filter(m => m.status === 'fini').length;
            
            let missing = 0;
            missions.filter(m => m.status !== 'fini').forEach(m => {
                if(DOCS_REQUIRED.filter(d => !m.docs[d]).length > 0) missing++;
            });
            document.getElementById('stat-docs').innerText = missing;
        }

        function renderClients() {
            const table = document.getElementById('client-table');
            table.innerHTML = clients.map(c => `
                <tr class="border-b hover:bg-slate-50">
                    <td class="p-4 font-bold">${c.name}</td>
                    <td class="p-4 font-mono text-slate-500">${c.nif}</td>
                    <td class="p-4"><button onclick="deleteC(${c.id})" class="text-rose-500"><i data-lucide="trash-2" class="w-4 h-4"></i></button></td>
                </tr>
            `).join('');
            lucide.createIcons();
        }

        function deleteC(id) {
            clients = clients.filter(c => c.id !== id);
            localStorage.setItem('tp_expert_c', JSON.stringify(clients));
            renderClients();
            updateDropdowns();
        }

        function printM(id) {
            const m = missions.find(x => x.id === id);
            const taxes = calculateTaxes(m.cfr, m.cat);
            const ps = document.getElementById('print-section');
            ps.innerHTML = `
                <div class="p-16 text-slate-900 bg-white min-h-screen">
                    <div class="flex justify-between border-b-2 border-slate-900 pb-8 mb-10">
                        <div>
                            <h1 class="text-4xl font-black italic">TRANSITPRO <span class="text-blue-600">GABON</span></h1>
                            <p class="text-xs font-bold text-slate-500 mt-2 uppercase">Conseil & Commissionnaire Agrée en Douane</p>
                        </div>
                        <div class="text-right">
                            <p class="font-black text-xl">DOSSIER EXPERT</p>
                            <p class="text-xs font-bold uppercase">N° REF: ${m.ref}</p>
                            <p class="text-xs font-bold uppercase">OUVERT LE: ${new Date(m.id).toLocaleDateString()}</p>
                        </div>
                    </div>

                    <div class="grid grid-cols-2 gap-20 mb-12">
                        <div>
                            <h4 class="text-[10px] font-black text-blue-600 uppercase border-b mb-2">Importateur</h4>
                            <p class="text-2xl font-black">${m.client}</p>
                            <p class="text-sm font-bold uppercase">NIF: ${m.nif}</p>
                        </div>
                        <div class="text-right">
                            <h4 class="text-[10px] font-black text-blue-600 uppercase border-b mb-2">Spécifications</h4>
                            <p class="text-sm font-bold uppercase">Mode: ${m.mode} / ${m.units}</p>
                            <p class="text-sm font-bold uppercase">Poids: ${m.weight} KG</p>
                            <p class="text-sm font-bold uppercase">Volume: ${m.volume} M³</p>
                        </div>
                    </div>

                    <h4 class="text-[10px] font-black text-blue-600 uppercase border-b mb-4">Analyse Documentaire</h4>
                    <div class="grid grid-cols-2 gap-4 mb-12">
                        ${DOCS_REQUIRED.map(d => `
                            <div class="flex justify-between items-center border-b border-slate-100 py-1">
                                <span class="text-xs uppercase font-bold">${d}</span>
                                <span class="text-xs font-black ${m.docs[d] ? 'text-emerald-600' : 'text-rose-600'}">${m.docs[d] ? 'RECU' : 'MANQUANT'}</span>
                            </div>
                        `).join('')}
                    </div>

                    <h4 class="text-[10px] font-black text-blue-600 uppercase border-b mb-4">Estimation Prévisionnelle</h4>
                    <table class="w-full text-left mb-12">
                        <thead class="bg-slate-100">
                            <tr><th class="p-3 text-[10px]">DESCRIPTION</th><th class="p-3 text-right text-[10px]">MONTANT (FCFA)</th></tr>
                        </thead>
                        <tbody class="text-xs font-bold">
                            <tr class="border-b">
                                <td class="p-3">Droits de Douane (Catégorie choisie)</td>
                                <td class="p-3 text-right font-mono">${new Intl.NumberFormat('fr-FR').format(taxes.dd)}</td>
                            </tr>
                            <tr class="border-b">
                                <td class="p-3">Redevance Statistique (1.5%)</td>
                                <td class="p-3 text-right font-mono">${new Intl.NumberFormat('fr-FR').format(taxes.rs)}</td>
                            </tr>
                            <tr class="border-b">
                                <td class="p-3">TVA Importation (18%)</td>
                                <td class="p-3 text-right font-mono">${new Intl.NumberFormat('fr-FR').format(taxes.tva)}</td>
                            </tr>
                            <tr class="border-b">
                                <td class="p-3">Frais de Dossier Fixes</td>
                                <td class="p-3 text-right font-mono">45 000</td>
                            </tr>
                            <tr class="bg-slate-900 text-white">
                                <td class="p-4 text-sm uppercase">Total Prévisionnel TTC</td>
                                <td class="p-4 text-right text-2xl font-black font-mono">${new Intl.NumberFormat('fr-FR').format(taxes.total)}</td>
                            </tr>
                        </tbody>
                    </table>

                    <div class="mt-20 flex justify-between text-center italic text-[9px] text-slate-400">
                        <div>Document édité via TransitPro Expert - Version Conseil.</div>
                        <div>Validité de l'estimation : 15 jours sous réserve de fluctuation monétaire.</div>
                    </div>
                </div>
            `;
            window.print();
        }
    </script>
</body>
</html>
