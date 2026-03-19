<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Transit Pro Gabon - Version Intégrale</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;700;900&display=swap');
        body { font-family: 'Inter', sans-serif; background-color: #f8fafc; color: #1e293b; }
        .tab-active { border-bottom: 3px solid #2563eb; color: #2563eb; background: white; }
        .status-badge { padding: 4px 10px; border-radius: 999px; font-size: 0.65rem; font-weight: 800; text-transform: uppercase; border: 1px solid currentColor; }
        .form-label { font-size: 0.65rem; font-weight: 800; color: #64748b; text-transform: uppercase; margin-bottom: 0.2rem; display: block; letter-spacing: 0.05em; }
        .input-pro { width: 100%; padding: 0.65rem 0.8rem; border: 1px solid #e2e8f0; border-radius: 0.6rem; outline: none; transition: all 0.2s; font-size: 0.9rem; background: #ffffff; }
        .input-pro:focus { border-color: #3b82f6; box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.1); }
        .card-shadow { box-shadow: 0 1px 3px 0 rgb(0 0 0 / 0.1), 0 1px 2px -1px rgb(0 0 0 / 0.1); }
        .doc-preview { width: 40px; height: 40px; object-fit: cover; border-radius: 8px; border: 1px solid #e2e8f0; }
    </style>
</head>
<body class="min-h-screen pb-20">

    <!-- Navigation -->
    <nav class="bg-slate-900 text-white p-4 shadow-2xl sticky top-0 z-50">
        <div class="max-w-6xl mx-auto flex justify-between items-center">
            <div class="flex items-center gap-3">
                <div class="bg-blue-600 p-2 rounded-xl shadow-inner">
                    <i data-lucide="anchor" class="w-5 h-5 text-white"></i>
                </div>
                <div>
                    <h1 class="font-black text-xl tracking-tighter uppercase italic leading-none">Transit<span class="text-blue-500">Pro</span></h1>
                    <p class="text-[9px] uppercase tracking-[0.2em] font-bold text-slate-400 mt-1">Gabon Logistique 2026</p>
                </div>
            </div>
            <div class="hidden md:block text-right">
                <p class="text-[10px] font-black text-slate-500 uppercase tracking-widest">Bureau de Douane</p>
                <p class="text-xs font-bold text-blue-400 uppercase">Owendo Port (GALBV)</p>
            </div>
        </div>
    </nav>

    <main class="max-w-5xl mx-auto mt-6 px-4">
        
        <!-- Onglets -->
        <div class="flex bg-slate-200/50 p-1 rounded-2xl mb-8 card-shadow border border-slate-200/60">
            <button onclick="switchTab('simulator')" id="tab-simulator" class="flex-1 py-3.5 font-bold text-slate-500 rounded-xl transition-all tab-active flex justify-center items-center gap-2">
                <i data-lucide="plus-circle" class="w-4 h-4"></i> Nouveau Dossier
            </button>
            <button onclick="switchTab('missions')" id="tab-missions" class="flex-1 py-3.5 font-bold text-slate-500 rounded-xl transition-all flex justify-center items-center gap-2">
                <i data-lucide="activity" class="w-4 h-4"></i> Missions Actives
            </button>
            <button onclick="switchTab('archive')" id="tab-archive" class="flex-1 py-3.5 font-bold text-slate-500 rounded-xl transition-all flex justify-center items-center gap-2">
                <i data-lucide="archive" class="w-4 h-4"></i> Historique
            </button>
        </div>

        <!-- SECTION : SIMULATEUR -->
        <section id="section-simulator" class="space-y-6">
            <div class="bg-white rounded-3xl shadow-sm border border-slate-200 p-8">
                <div class="grid grid-cols-1 lg:grid-cols-3 gap-8">
                    
                    <!-- CLIENT & CONTACT -->
                    <div class="space-y-5">
                        <h3 class="flex items-center gap-2 text-[11px] font-black text-blue-600 uppercase tracking-widest border-b pb-3">
                            <i data-lucide="user-check" class="w-4 h-4"></i> Informations Client
                        </h3>
                        <div>
                            <label class="form-label">Nom / Raison Sociale</label>
                            <input type="text" id="client-name" placeholder="Ex: SOGADA Gabon" class="input-pro font-bold">
                        </div>
                        <div>
                            <label class="form-label">NIF (Identifiant Fiscal)</label>
                            <input type="text" id="client-nif" placeholder="Format: 700000Z" class="input-pro uppercase font-mono">
                        </div>
                        <div>
                            <label class="form-label">Téléphone / WhatsApp</label>
                            <input type="tel" id="client-tel" placeholder="+241 06 00 00 00" class="input-pro">
                        </div>
                    </div>

                    <!-- LOGISTIQUE -->
                    <div class="space-y-5">
                        <h3 class="flex items-center gap-2 text-[11px] font-black text-emerald-600 uppercase tracking-widest border-b pb-3">
                            <i data-lucide="ship" class="w-4 h-4"></i> Détails Logistiques
                        </h3>
                        <div>
                            <label class="form-label">Compagnie / Transporteur</label>
                            <select id="vessel" class="input-pro font-bold">
                                <optgroup label="MARITIME">
                                    <option value="CMA CGM">CMA CGM</option>
                                    <option value="Maersk">Maersk Line</option>
                                    <option value="MSC">MSC Gabon</option>
                                    <option value="Grimaldi">Grimaldi Lines</option>
                                </optgroup>
                                <optgroup label="AÉRIEN">
                                    <option value="Sky Gabon">Sky Gabon</option>
                                    <option value="DHL">DHL Express</option>
                                </optgroup>
                            </select>
                        </div>
                        <div>
                            <label class="form-label">Réf. BL / LTA / Conteneur</label>
                            <input type="text" id="ref-log" placeholder="ZIMU / MAEU..." class="input-pro uppercase font-mono">
                        </div>
                        <div class="grid grid-cols-2 gap-3">
                            <div>
                                <label class="form-label">Colis</label>
                                <input type="number" id="qty" placeholder="0" class="input-pro">
                            </div>
                            <div>
                                <label class="form-label">Poids (kg)</label>
                                <input type="number" id="weight" placeholder="0" class="input-pro">
                            </div>
                        </div>
                        <div class="p-3 bg-slate-50 rounded-xl border border-dashed border-slate-300 flex justify-around">
                            <label class="flex items-center gap-2 cursor-pointer">
                                <input type="radio" name="mode" value="SEA" checked class="w-4 h-4">
                                <span class="text-[10px] font-black text-slate-600 uppercase">Maritime</span>
                            </label>
                            <label class="flex items-center gap-2 cursor-pointer">
                                <input type="radio" name="mode" value="AIR" class="w-4 h-4">
                                <span class="text-[10px] font-black text-slate-600 uppercase">Aérien</span>
                            </label>
                        </div>
                    </div>

                    <!-- DOUANE -->
                    <div class="space-y-5">
                        <h3 class="flex items-center gap-2 text-[11px] font-black text-orange-600 uppercase tracking-widest border-b pb-3">
                            <i data-lucide="calculator" class="w-4 h-4"></i> Calcul Douane (CEMAC)
                        </h3>
                        <div>
                            <label class="form-label">Valeur CFR (FCFA)</label>
                            <input type="number" id="cfr-value" placeholder="Montant Facture + Fret" class="input-pro text-blue-700 font-black text-lg">
                        </div>
                        <div>
                            <label class="form-label">Catégorie Tarifaire CEMAC</label>
                            <select id="category" class="input-pro font-bold">
                                <optgroup label="CATÉGORIE 1 (5%)">
                                    <option value="sante">Santé & Médicaments (5%)</option>
                                    <option value="livres">Éducation & Livres (5%)</option>
                                </optgroup>
                                <optgroup label="CATÉGORIE 2 (10%)">
                                    <option value="machines">Machines & Équipements (10%)</option>
                                    <option value="matieres">Matières Premières (10%)</option>
                                </optgroup>
                                <optgroup label="CATÉGORIE 3 (20%)">
                                    <option value="pieces">Pièces Détachées / Divers (20%)</option>
                                    <option value="alimentation">Alimentation (20%)</option>
                                </optgroup>
                                <optgroup label="CATÉGORIE 4 (30%)">
                                    <option value="electromenager">Électroménager (30%)</option>
                                    <option value="vehicules">Véhicules de Tourisme (30%)</option>
                                </optgroup>
                            </select>
                        </div>
                        <div>
                            <label class="form-label">Pays de Provenance</label>
                            <input type="text" id="origin" placeholder="Ex: France, Chine, UAE" class="input-pro">
                        </div>
                    </div>
                </div>

                <div class="mt-8 pt-8 border-t flex flex-col md:flex-row gap-4">
                    <button onclick="calculateOnly()" class="flex-1 bg-slate-100 hover:bg-slate-200 text-slate-700 font-black py-4 rounded-2xl transition-all flex justify-center items-center gap-3 uppercase text-[10px] tracking-widest">
                        <i data-lucide="eye"></i> Aperçu Rapide
                    </button>
                    <button onclick="createMission()" class="flex-[2] bg-blue-600 hover:bg-blue-700 text-white font-black py-4 rounded-2xl shadow-xl shadow-blue-200 flex justify-center items-center gap-3 transition-all uppercase text-[10px] tracking-widest">
                        <i data-lucide="save"></i> Créer la Mission
                    </button>
                </div>
            </div>

            <!-- RÉSULTATS DÉTAILLÉS -->
            <div id="quick-res" class="hidden bg-slate-900 rounded-3xl p-8 shadow-2xl border border-slate-800 animate-in fade-in duration-500">
                <div class="flex flex-col md:flex-row justify-between items-center gap-8">
                    <div class="text-center md:text-left">
                        <p id="res-cat-label" class="text-blue-500 font-bold text-[10px] uppercase tracking-[0.2em] mb-2">CATÉGORIE --</p>
                        <h3 id="quick-tax" class="text-5xl font-black tracking-tighter text-white leading-none">0 FCFA</h3>
                        <p class="text-slate-500 text-[10px] font-bold mt-4 uppercase">Total Estimation (DD + TVA + RS + SYDONIA)</p>
                    </div>
                    <div class="grid grid-cols-2 gap-x-12 gap-y-4 text-[10px] font-bold text-slate-500 border-t md:border-t-0 md:border-l border-slate-800 pt-6 md:pt-0 md:pl-12">
                        <div>DROITS DOUANE (DD): <span id="res-dd" class="text-white block font-mono text-xs mt-1">--</span></div>
                        <div>TVA (18%): <span id="res-tva" class="text-white block font-mono text-xs mt-1">--</span></div>
                        <div>REDEVANCE STAT. (1.5%): <span id="res-rs" class="text-white block font-mono text-xs mt-1">--</span></div>
                        <div>SYDONIA / PHS: <span id="res-sys" class="text-white block font-mono text-xs mt-1">--</span></div>
                    </div>
                </div>
            </div>
        </section>

        <!-- SECTION : MISSIONS -->
        <section id="section-missions" class="hidden space-y-6">
            <div id="list-active" class="grid grid-cols-1 gap-4"></div>
        </section>

        <!-- SECTION : ARCHIVE -->
        <section id="section-archive" class="hidden space-y-6">
            <div id="list-archive" class="grid grid-cols-1 gap-4 opacity-70"></div>
        </section>
    </main>

    <!-- Modal Pièces Jointes -->
    <div id="modal-docs" class="hidden fixed inset-0 bg-slate-900/80 backdrop-blur-sm z-[100] flex items-center justify-center p-4">
        <div class="bg-white w-full max-w-lg rounded-3xl overflow-hidden shadow-2xl">
            <div class="p-6 bg-slate-50 border-b flex justify-between items-center">
                <div>
                    <h3 class="font-black text-slate-900 uppercase text-xs tracking-widest">Dossier Numérique</h3>
                    <p class="text-[9px] text-slate-400 font-bold uppercase mt-1">Stockage ultra-compressé WebP</p>
                </div>
                <button onclick="closeDocs()" class="text-slate-400 hover:text-slate-900"><i data-lucide="x" class="w-5 h-5"></i></button>
            </div>
            <div class="p-6 space-y-4">
                <div class="border-2 border-dashed border-slate-200 rounded-2xl p-8 text-center hover:bg-slate-50 transition-all cursor-pointer relative group">
                    <input type="file" id="file-input" multiple accept="image/*,application/pdf" class="absolute inset-0 opacity-0 cursor-pointer" onchange="handleFiles(this)">
                    <i data-lucide="upload-cloud" class="w-10 h-10 text-blue-500 mx-auto mb-2 group-hover:scale-110 transition-transform"></i>
                    <p class="text-xs font-bold text-slate-600">Ajouter des documents (Images/PDF)</p>
                    <p class="text-[9px] text-slate-400 mt-2 uppercase font-black tracking-tighter">Facture, BL, Liste de Colisage...</p>
                </div>
                <div id="doc-list" class="space-y-2 max-h-64 overflow-y-auto pr-2 custom-scrollbar">
                    <!-- Liste documents -->
                </div>
            </div>
        </div>
    </div>

    <script>
        const CONFIG = {
            sante: { dd: 0.05, label: "SANTÉ (5%)" },
            livres: { dd: 0.05, label: "ÉDUCATION (5%)" },
            machines: { dd: 0.10, label: "MACHINES (10%)" },
            matieres: { dd: 0.10, label: "MATIÈRES PREMIÈRES (10%)" },
            pieces: { dd: 0.20, label: "PIÈCES DÉTACHÉES (20%)" },
            alimentation: { dd: 0.20, label: "ALIMENTATION (20%)" },
            electromenager: { dd: 0.30, label: "ÉLECTROMÉNAGER (30%)" },
            vehicules: { dd: 0.30, label: "VÉHICULES (30%)" }
        };

        let missions = JSON.parse(localStorage.getItem('transit_gabon_vfinal')) || [];
        let activeMissionId = null;

        window.onload = () => { lucide.createIcons(); render(); };

        function switchTab(t) {
            ['simulator', 'missions', 'archive'].forEach(id => {
                document.getElementById('section-'+id).classList.add('hidden');
                document.getElementById('tab-'+id).classList.remove('tab-active');
            });
            document.getElementById('section-'+t).classList.remove('hidden');
            document.getElementById('tab-'+t).classList.add('tab-active');
            render();
        }

        function getTaxes(cfr, cat) {
            const c = CONFIG[cat] || CONFIG.pieces;
            const caf = cfr * 1.01; // Simulation Fret/Assurance (1%)
            const dd = caf * c.dd;
            const rs = caf * 0.015;
            const tva = (caf + dd + rs) * 0.18;
            const sys = 35000; // Frais Sydonia + PHS forfaitaires
            
            return {
                total: Math.round(dd + rs + tva + sys),
                dd_v: Math.round(dd),
                tva_v: Math.round(tva),
                rs_v: Math.round(rs),
                sys_v: sys,
                label: c.label
            };
        }

        function calculateOnly() {
            const v = parseFloat(document.getElementById('cfr-value').value);
            const k = document.getElementById('category').value;
            if (!v) return;
            const r = getTaxes(v, k);
            document.getElementById('quick-res').classList.remove('hidden');
            document.getElementById('quick-tax').innerText = format(r.total) + " FCFA";
            document.getElementById('res-dd').innerText = format(r.dd_v) + " FCFA";
            document.getElementById('res-tva').innerText = format(r.tva_v) + " FCFA";
            document.getElementById('res-rs').innerText = format(r.rs_v) + " FCFA";
            document.getElementById('res-sys').innerText = format(r.sys_v) + " FCFA";
            document.getElementById('res-cat-label').innerText = r.label;
            document.getElementById('quick-res').scrollIntoView({ behavior: 'smooth' });
        }

        function createMission() {
            const client = document.getElementById('client-name').value;
            const val = parseFloat(document.getElementById('cfr-value').value);
            if (!client || !val) return alert("Remplissez au moins le nom du client et la valeur.");

            missions.push({
                id: Date.now(),
                client,
                nif: document.getElementById('client-nif').value || "N/A",
                tel: document.getElementById('client-tel').value || "N/A",
                cfr: val,
                cat: document.getElementById('category').value,
                ref: document.getElementById('ref-log').value || "SANS-REF",
                carrier: document.getElementById('vessel').value,
                mode: document.querySelector('input[name="mode"]:checked').value,
                qty: document.getElementById('qty').value || "0",
                weight: document.getElementById('weight').value || "0",
                origin: document.getElementById('origin').value || "N/A",
                status: 'init',
                date: new Date().toLocaleDateString('fr-FR'),
                docs: []
            });
            save();
            switchTab('missions');
        }

        // --- GESTION DES DOCUMENTS ---
        function openDocs(id) {
            activeMissionId = id;
            document.getElementById('modal-docs').classList.remove('hidden');
            renderDocs();
        }

        function closeDocs() {
            document.getElementById('modal-docs').classList.add('hidden');
            activeMissionId = null;
        }

        async function handleFiles(input) {
            const files = Array.from(input.files);
            const mission = missions.find(m => m.id === activeMissionId);
            for (const file of files) {
                if (file.type.startsWith('image/')) {
                    const compressed = await compressImage(file);
                    mission.docs.push({ name: file.name, data: compressed, type: 'image' });
                } else {
                    const reader = new FileReader();
                    reader.onload = (e) => {
                        mission.docs.push({ name: file.name, data: e.target.result, type: file.type.includes('pdf') ? 'pdf' : 'file' });
                        save(); renderDocs(); render();
                    };
                    reader.readAsDataURL(file);
                }
            }
            save(); renderDocs(); render();
            input.value = "";
        }

        function compressImage(file) {
            return new Promise((resolve) => {
                const reader = new FileReader();
                reader.readAsDataURL(file);
                reader.onload = (event) => {
                    const img = new Image();
                    img.src = event.target.result;
                    img.onload = () => {
                        const canvas = document.createElement('canvas');
                        const MAX_WIDTH = 800;
                        let width = img.width, height = img.height;
                        if (width > MAX_WIDTH) { height *= MAX_WIDTH / width; width = MAX_WIDTH; }
                        canvas.width = width; canvas.height = height;
                        const ctx = canvas.getContext('2d');
                        ctx.drawImage(img, 0, 0, width, height);
                        resolve(canvas.toDataURL('image/webp', 0.55));
                    };
                };
            });
        }

        function renderDocs() {
            const list = document.getElementById('doc-list');
            const mission = missions.find(m => m.id === activeMissionId);
            list.innerHTML = "";
            if (!mission.docs.length) {
                list.innerHTML = `<p class="text-center text-[10px] text-slate-400 py-6 font-bold uppercase tracking-widest">Aucun document attaché</p>`;
                return;
            }
            mission.docs.forEach((doc, idx) => {
                list.innerHTML += `
                    <div class="flex items-center gap-3 p-3 bg-slate-50 rounded-2xl border border-slate-100 group animate-in slide-in-from-right-2">
                        ${doc.type === 'image' ? `<img src="${doc.data}" class="doc-preview">` : `<div class="w-10 h-10 bg-red-100 text-red-600 flex items-center justify-center rounded-lg"><i data-lucide="file-text" class="w-5 h-5"></i></div>`}
                        <div class="flex-1 overflow-hidden">
                            <p class="text-[10px] font-black text-slate-700 truncate uppercase">${doc.name}</p>
                            <p class="text-[8px] text-slate-400 font-bold">${Math.round(doc.data.length / 1024)} KB • OPTIMISÉ</p>
                        </div>
                        <div class="flex gap-1">
                            <button onclick="removeDoc(${idx})" class="p-2 text-rose-500 hover:bg-rose-50 rounded-lg transition-colors"><i data-lucide="trash-2" class="w-4 h-4"></i></button>
                        </div>
                    </div>
                `;
            });
            lucide.createIcons();
        }

        function removeDoc(idx) {
            const mission = missions.find(m => m.id === activeMissionId);
            mission.docs.splice(idx, 1);
            save(); renderDocs(); render();
        }

        function setStatus(id, s) { missions = missions.map(m => m.id === id ? {...m, status: s} : m); save(); render(); }
        function deleteM(id) { if(confirm("Supprimer ce dossier ?")) { missions = missions.filter(m => m.id !== id); save(); render(); } }
        function save() { localStorage.setItem('transit_gabon_vfinal', JSON.stringify(missions)); }
        function format(n) { return new Intl.NumberFormat('fr-FR').format(n); }

        function render() {
            const act = document.getElementById('list-active');
            const arc = document.getElementById('list-archive');
            act.innerHTML = ""; arc.innerHTML = "";

            missions.forEach(m => {
                const r = getTaxes(m.cfr, m.cat);
                const docCount = m.docs.length;
                const html = `
                    <div class="bg-white rounded-3xl border border-slate-200 shadow-sm overflow-hidden hover:border-blue-300 transition-all">
                        <div class="p-6 flex flex-col md:flex-row gap-6 items-center">
                            <div class="${m.mode === 'SEA' ? 'bg-blue-600' : 'bg-emerald-600'} w-14 h-14 flex items-center justify-center rounded-2xl text-white shadow-lg">
                                <i data-lucide="${m.mode === 'SEA' ? 'ship' : 'plane'}" class="w-7 h-7"></i>
                            </div>
                            <div class="flex-1 text-center md:text-left">
                                <div class="flex flex-wrap justify-center md:justify-start items-center gap-2 mb-1">
                                    <h4 class="font-black text-slate-900 uppercase text-sm">${m.client}</h4>
                                    <span class="text-[8px] font-black bg-slate-100 text-slate-500 px-2 py-0.5 rounded">NIF: ${m.nif}</span>
                                </div>
                                <p class="text-[10px] font-bold text-slate-400 uppercase tracking-widest">${m.carrier} • ${m.ref} • ${m.origin}</p>
                            </div>
                            <div class="flex items-center gap-5">
                                <button onclick="openDocs(${m.id})" class="relative p-2.5 bg-slate-100 hover:bg-blue-100 text-slate-600 hover:text-blue-600 rounded-xl transition-all">
                                    <i data-lucide="paperclip" class="w-5 h-5"></i>
                                    ${docCount > 0 ? `<span class="absolute -top-1 -right-1 bg-blue-600 text-white text-[8px] font-black w-4 h-4 flex items-center justify-center rounded-full border-2 border-white">${docCount}</span>` : ''}
                                </button>
                                <div class="text-right border-l pl-5 border-slate-100">
                                    <p class="text-xl font-black text-slate-900">${format(r.total)} <span class="text-[10px] text-slate-400 font-normal">FCFA</span></p>
                                    <p class="text-[8px] font-black text-blue-500 uppercase">${r.label}</p>
                                </div>
                            </div>
                        </div>
                        <div class="bg-slate-50 px-6 py-3 border-t border-slate-100 flex justify-between items-center">
                            <div class="flex items-center gap-3">
                                ${getStatusBadge(m.status)}
                                <span class="text-[9px] font-bold text-slate-400 uppercase">${m.date}</span>
                            </div>
                            <div class="flex gap-2">
                                ${m.status !== 'fini' ? `
                                    <button onclick="setStatus(${m.id}, 'douane')" class="px-4 py-1.5 bg-slate-900 text-white rounded-xl text-[9px] font-black uppercase hover:bg-blue-600 transition-colors">Douane</button>
                                    <button onclick="setStatus(${m.id}, 'fini')" class="p-1.5 bg-emerald-100 text-emerald-600 rounded-xl hover:bg-emerald-200 transition-colors"><i data-lucide="check" class="w-4 h-4"></i></button>
                                ` : `
                                    <button onclick="deleteM(${m.id})" class="p-1.5 bg-rose-50 text-rose-500 rounded-xl hover:bg-rose-100 transition-colors"><i data-lucide="trash-2" class="w-4 h-4"></i></button>
                                `}
                            </div>
                        </div>
                    </div>
                `;
                if(m.status === 'fini') arc.innerHTML += html;
                else act.innerHTML += html;
            });
            lucide.createIcons();
        }

        function getStatusBadge(s) {
            if(s === 'init') return '<span class="status-badge bg-amber-50 text-amber-600 border-amber-200">Ouvert</span>';
            if(s === 'douane') return '<span class="status-badge bg-blue-100 text-blue-600 border-blue-200 animate-pulse">En Douane</span>';
            return '<span class="status-badge bg-slate-200 text-slate-500 border-slate-300">Clôturé</span>';
        }
    </script>
</body>
</html>
