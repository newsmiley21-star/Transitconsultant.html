<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Transit Pro Gabon - Système de Gestion Logistique</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf-autotable/3.5.28/jspdf.plugin.autotable.min.js"></script>
    
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
        .custom-scrollbar::-webkit-scrollbar { width: 4px; }
        .custom-scrollbar::-webkit-scrollbar-thumb { background: #cbd5e1; border-radius: 10px; }
        .modal-animate { animation: modalIn 0.2s ease-out; }
        @keyframes modalIn { from { opacity: 0; transform: scale(0.95); } to { opacity: 1; transform: scale(1); } }
    </style>
</head>
<body class="min-h-screen pb-20">

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
                <p class="text-[10px] font-black text-slate-500 uppercase tracking-widest">Bureau de Douane Principal</p>
                <p class="text-xs font-bold text-blue-400 uppercase">Owendo Port (GALBV)</p>
            </div>
        </div>
    </nav>

    <main class="max-w-5xl mx-auto mt-6 px-4">
        
        <div class="flex bg-slate-200/50 p-1 rounded-2xl mb-8 card-shadow border border-slate-200/60 text-xs md:text-sm">
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

        <section id="section-simulator" class="space-y-6">
            <div class="bg-white rounded-3xl shadow-sm border border-slate-200 p-8">
                <div class="grid grid-cols-1 lg:grid-cols-3 gap-8">
                    <div class="space-y-5">
                        <h3 class="flex items-center gap-2 text-[11px] font-black text-blue-600 uppercase tracking-widest border-b pb-3"><i data-lucide="user-check" class="w-4 h-4"></i> Informations Client</h3>
                        <div><label class="form-label">Nom Client</label><input type="text" id="client-name" class="input-pro font-bold"></div>
                        <div><label class="form-label">NIF</label><input type="text" id="client-nif" class="input-pro uppercase font-mono"></div>
                        <div><label class="form-label">Téléphone</label><input type="tel" id="client-tel" class="input-pro"></div>
                    </div>
                    <div class="space-y-5">
                        <h3 class="flex items-center gap-2 text-[11px] font-black text-emerald-600 uppercase tracking-widest border-b pb-3"><i data-lucide="ship" class="w-4 h-4"></i> Logistique</h3>
                        <div><label class="form-label">Compagnie</label><select id="vessel" class="input-pro font-bold"><option value="CMA CGM">CMA CGM</option><option value="Maersk Line">Maersk Line</option><option value="DHL Express">DHL Express</option></select></div>
                        <div><label class="form-label">Référence BL/LTA</label><input type="text" id="ref-log" class="input-pro uppercase font-mono"></div>
                        <div class="grid grid-cols-2 gap-3">
                            <div><label class="form-label">Colis</label><input type="number" id="qty" class="input-pro"></div>
                            <div><label class="form-label">Poids (kg)</label><input type="number" id="weight" class="input-pro"></div>
                        </div>
                        <div class="p-3 bg-slate-50 rounded-xl border border-dashed border-slate-300 flex justify-around">
                            <label class="flex items-center gap-2 cursor-pointer"><input type="radio" name="mode" value="SEA" checked> <span class="text-[10px] font-black uppercase">Maritime</span></label>
                            <label class="flex items-center gap-2 cursor-pointer"><input type="radio" name="mode" value="AIR"> <span class="text-[10px] font-black uppercase">Aérien</span></label>
                        </div>
                    </div>
                    <div class="space-y-5">
                        <h3 class="flex items-center gap-2 text-[11px] font-black text-orange-600 uppercase tracking-widest border-b pb-3"><i data-lucide="calculator" class="w-4 h-4"></i> Douane</h3>
                        <div><label class="form-label">Valeur CFR (FCFA)</label><input type="number" id="cfr-value" class="input-pro text-blue-700 font-black text-lg"></div>
                        <div><label class="form-label">Catégorie</label><select id="category" class="input-pro font-bold"><option value="sante">Santé (5%)</option><option value="pieces" selected>Divers (20%)</option><option value="vehicules">Luxe (30%)</option></select></div>
                        <div><label class="form-label">Origine</label><input type="text" id="origin" class="input-pro"></div>
                    </div>
                </div>
                <div class="mt-8 pt-8 border-t flex gap-4">
                    <button onclick="calculateOnly()" class="flex-1 bg-slate-100 py-4 rounded-2xl font-black uppercase text-[10px]">Aperçu</button>
                    <button onclick="createMission()" class="flex-[2] bg-blue-600 text-white py-4 rounded-2xl font-black shadow-xl shadow-blue-200 uppercase text-[10px]">Créer Mission</button>
                </div>
            </div>
            <div id="quick-res" class="hidden bg-slate-900 rounded-3xl p-8 text-white"><h3 id="quick-tax" class="text-4xl font-black">0 FCFA</h3></div>
        </section>

        <section id="section-missions" class="hidden space-y-4"><div id="list-active" class="grid gap-4"></div></section>
        <section id="section-archive" class="hidden space-y-4"><div id="list-archive" class="grid gap-4"></div></section>
    </main>

    <div id="modal-view" class="hidden fixed inset-0 bg-slate-900/90 backdrop-blur-sm z-[110] flex items-center justify-center p-4">
        <div class="bg-white w-full max-w-2xl rounded-3xl overflow-hidden shadow-2xl modal-animate">
            <div id="view-content" class="p-8 max-h-[85vh] overflow-y-auto custom-scrollbar">
                </div>
            <div class="p-6 bg-slate-50 border-t flex justify-end">
                <button onclick="closeView()" class="px-8 py-3 bg-slate-900 text-white rounded-xl text-[10px] font-black uppercase">Fermer la fiche</button>
            </div>
        </div>
    </div>

    <div id="modal-docs" class="hidden fixed inset-0 bg-slate-900/80 backdrop-blur-sm z-[100] flex items-center justify-center p-4">
        <div class="bg-white w-full max-w-lg rounded-3xl p-6">
            <div class="flex justify-between items-center mb-6">
                <h3 class="font-black text-xs uppercase">Pièces Jointes</h3>
                <button onclick="closeDocs()"><i data-lucide="x"></i></button>
            </div>
            <input type="file" multiple accept="image/*" class="w-full mb-4" onchange="handleFiles(this)">
            <div id="doc-list" class="space-y-2 max-h-64 overflow-y-auto"></div>
        </div>
    </div>

    <script>
        const CONFIG = { sante: { dd: 0.05, label: "SANTÉ (5%)" }, machines: { dd: 0.10, label: "ÉQUIPEMENT (10%)" }, pieces: { dd: 0.20, label: "DIVERS (20%)" }, vehicules: { dd: 0.30, label: "LUXE (30%)" } };
        let missions = JSON.parse(localStorage.getItem('transit_gabon_vfinal')) || [];
        let activeMissionId = null;

        window.onload = () => { lucide.createIcons(); render(); };

        function switchTab(t) {
            ['simulator', 'missions', 'archive'].forEach(id => { document.getElementById('section-'+id).classList.add('hidden'); document.getElementById('tab-'+id).classList.remove('tab-active'); });
            document.getElementById('section-'+t).classList.remove('hidden'); document.getElementById('tab-'+t).classList.add('tab-active');
            render();
        }

        function getTaxes(cfr, cat) {
            const c = CONFIG[cat] || CONFIG.pieces;
            const caf = cfr * 1.01;
            const dd = caf * c.dd;
            const rs = caf * 0.015;
            const tva = (caf + dd + rs) * 0.18;
            return { total: Math.round(dd + rs + tva + 35000), dd_v: Math.round(dd), tva_v: Math.round(tva), rs_v: Math.round(rs), label: c.label };
        }

        function createMission() {
            const client = document.getElementById('client-name').value;
            const val = parseFloat(document.getElementById('cfr-value').value);
            if (!client || !val) return alert("Remplissez les champs obligatoires.");
            missions.push({
                id: Date.now(), client, nif: document.getElementById('client-nif').value || "N/A", tel: document.getElementById('client-tel').value || "N/A",
                cfr: val, cat: document.getElementById('category').value, ref: document.getElementById('ref-log').value || "SANS-REF",
                carrier: document.getElementById('vessel').value, mode: document.querySelector('input[name="mode"]:checked').value,
                qty: document.getElementById('qty').value || "0", weight: document.getElementById('weight').value || "0",
                origin: document.getElementById('origin').value || "N/A", status: 'init', date: new Date().toLocaleDateString('fr-FR'), docs: []
            });
            save(); switchTab('missions');
        }

        // --- NOUVELLE FONCTION : VISIONNAGE DES DÉTAILS ---
        function openView(id) {
            const m = missions.find(x => x.id === id);
            const r = getTaxes(m.cfr, m.cat);
            const container = document.getElementById('view-content');
            
            container.innerHTML = `
                <div class="flex justify-between items-start mb-8">
                    <div>
                        <h2 class="text-3xl font-black text-slate-900 tracking-tighter uppercase leading-tight">${m.client}</h2>
                        <p class="text-blue-600 font-bold text-[10px] uppercase tracking-widest mt-1">Dossier Logistique #${m.id}</p>
                    </div>
                    <div class="text-right">
                        ${getStatusBadge(m.status)}
                        <p class="text-[9px] font-bold text-slate-400 mt-2 uppercase">Créé le ${m.date}</p>
                    </div>
                </div>

                <div class="grid grid-cols-2 md:grid-cols-3 gap-6 mb-10">
                    <div class="bg-slate-50 p-4 rounded-2xl border border-slate-100">
                        <p class="form-label">Identité Fiscale</p>
                        <p class="font-black text-sm uppercase">NIF: ${m.nif}</p>
                        <p class="text-[10px] text-slate-500 font-medium">Contact: ${m.tel}</p>
                    </div>
                    <div class="bg-slate-50 p-4 rounded-2xl border border-slate-100">
                        <p class="form-label">Transporteur</p>
                        <p class="font-black text-sm uppercase">${m.carrier}</p>
                        <p class="text-[10px] text-slate-500 font-medium">${m.mode === 'SEA' ? 'Fret Maritime' : 'Fret Aérien'}</p>
                    </div>
                    <div class="bg-slate-50 p-4 rounded-2xl border border-slate-100">
                        <p class="form-label">Référence BL/LTA</p>
                        <p class="font-black text-sm uppercase">${m.ref}</p>
                        <p class="text-[10px] text-slate-500 font-medium">Origine: ${m.origin}</p>
                    </div>
                    <div class="bg-slate-50 p-4 rounded-2xl border border-slate-100">
                        <p class="form-label">Volume / Poids</p>
                        <p class="font-black text-sm uppercase">${m.qty} Colis</p>
                        <p class="text-[10px] text-slate-500 font-medium">${m.weight} Kilogrammes (Brut)</p>
                    </div>
                </div>

                <div class="bg-slate-900 rounded-3xl p-6 text-white relative overflow-hidden">
                    <div class="relative z-10">
                        <p class="text-blue-400 font-black text-[10px] uppercase tracking-widest mb-6 border-b border-white/10 pb-2">Décompte Douanier Estimatif</p>
                        <div class="space-y-4">
                            <div class="flex justify-between text-xs">
                                <span class="text-slate-400">Valeur CFR (Base de calcul)</span>
                                <span class="font-mono font-bold">${format(m.cfr)} FCFA</span>
                            </div>
                            <div class="flex justify-between text-xs">
                                <span class="text-slate-400">Droits de Douane (${r.label})</span>
                                <span class="font-mono">+ ${format(r.dd_v)} FCFA</span>
                            </div>
                            <div class="flex justify-between text-xs">
                                <span class="text-slate-400">Taxes Diverses (TVA 18% + RS)</span>
                                <span class="font-mono">+ ${format(r.tva_v + r.rs_v)} FCFA</span>
                            </div>
                            <div class="flex justify-between text-xs">
                                <span class="text-slate-400">Frais Sydonia / PHS</span>
                                <span class="font-mono">+ 35 000 FCFA</span>
                            </div>
                            <div class="pt-4 border-t border-white/20 flex justify-between items-center">
                                <span class="text-sm font-black uppercase">Montant Total TTC</span>
                                <span class="text-2xl font-black text-blue-400">${format(r.total)} FCFA</span>
                            </div>
                        </div>
                    </div>
                </div>
            `;
            document.getElementById('modal-view').classList.remove('hidden');
        }

        function closeView() { document.getElementById('modal-view').classList.add('hidden'); }

        // --- CORE FUNCTIONS ---
        function render() {
            const act = document.getElementById('list-active'); const arc = document.getElementById('list-archive');
            act.innerHTML = ""; arc.innerHTML = "";

            missions.forEach(m => {
                const r = getTaxes(m.cfr, m.cat);
                const html = `
                    <div class="bg-white rounded-3xl border border-slate-200 shadow-sm overflow-hidden hover:border-blue-400 transition-all p-5 cursor-pointer" onclick="openView(${m.id})">
                        <div class="flex flex-col md:flex-row gap-5 items-center">
                            <div class="${m.mode === 'SEA' ? 'bg-blue-600' : 'bg-emerald-600'} w-12 h-12 flex items-center justify-center rounded-2xl text-white">
                                <i data-lucide="${m.mode === 'SEA' ? 'ship' : 'plane'}" class="w-6 h-6"></i>
                            </div>
                            <div class="flex-1 text-center md:text-left">
                                <h4 class="font-black text-slate-900 uppercase text-sm">${m.client}</h4>
                                <p class="text-[10px] font-bold text-slate-400 uppercase tracking-widest">${m.carrier} • ${m.ref}</p>
                            </div>
                            <div class="flex items-center gap-3" onclick="event.stopPropagation()">
                                <button onclick="exportToPDF(${m.id})" class="p-2.5 bg-blue-50 text-blue-600 rounded-xl hover:bg-blue-100 transition-all"><i data-lucide="download" class="w-5 h-5"></i></button>
                                <button onclick="openDocs(${m.id})" class="p-2.5 bg-slate-100 text-slate-600 rounded-xl hover:bg-slate-200 transition-all"><i data-lucide="paperclip" class="w-5 h-5"></i></button>
                                <div class="text-right border-l pl-5 border-slate-100 min-w-[120px]">
                                    <p class="text-lg font-black text-slate-900">${format(r.total)}</p>
                                    <p class="text-[8px] font-black text-blue-500 uppercase">CFA (ESTIMÉ)</p>
                                </div>
                            </div>
                        </div>
                        <div class="mt-4 pt-4 border-t flex justify-between items-center" onclick="event.stopPropagation()">
                            <div class="flex items-center gap-2">${getStatusBadge(m.status)}<span class="text-[9px] font-bold text-slate-400 uppercase">${m.date}</span></div>
                            <div class="flex gap-2">
                                ${m.status !== 'fini' ? `<button onclick="setStatus(${m.id}, 'fini')" class="p-1.5 bg-emerald-100 text-emerald-600 rounded-lg"><i data-lucide="check" class="w-4 h-4"></i></button>` : `<button onclick="deleteM(${m.id})" class="p-1.5 bg-rose-50 text-rose-500 rounded-lg"><i data-lucide="trash-2" class="w-4 h-4"></i></button>`}
                            </div>
                        </div>
                    </div>
                `;
                if(m.status === 'fini') arc.innerHTML += html; else act.innerHTML += html;
            });
            lucide.createIcons();
        }

        function getStatusBadge(s) {
            if(s === 'init') return '<span class="status-badge bg-amber-50 text-amber-600 border-amber-200">En Attente</span>';
            return '<span class="status-badge bg-slate-200 text-slate-500 border-slate-300">Clôturé</span>';
        }
        function setStatus(id, s) { missions = missions.map(m => m.id === id ? {...m, status: s} : m); save(); render(); }
        function deleteM(id) { if(confirm("Supprimer ?")) { missions = missions.filter(m => m.id !== id); save(); render(); } }
        function save() { localStorage.setItem('transit_gabon_vfinal', JSON.stringify(missions)); }
        function format(n) { return new Intl.NumberFormat('fr-FR').format(n); }
        function openDocs(id) { activeMissionId = id; document.getElementById('modal-docs').classList.remove('hidden'); renderDocs(); }
        function closeDocs() { document.getElementById('modal-docs').classList.add('hidden'); }
    </script>
</body>
</html>
