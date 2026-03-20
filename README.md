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
                        <h3 class="flex items-center gap-2 text-[11px] font-black text-blue-600 uppercase tracking-widest border-b pb-3">
                            <i data-lucide="user-check" class="w-4 h-4"></i> Informations Client
                        </h3>
                        <div>
                            <label class="form-label">Nom / Raison Sociale</label>
                            <input type="text" id="client-name" placeholder="Ex: SOGADA Gabon" class="input-pro font-bold">
                        </div>
                        <div>
                            <label class="form-label">NIF (Identifiant Fiscal)</label>
                            <input type="text" id="client-nif" placeholder="Ex: 700000Z" class="input-pro uppercase font-mono">
                        </div>
                        <div>
                            <label class="form-label">Téléphone / WhatsApp</label>
                            <input type="tel" id="client-tel" placeholder="+241 06 00 00 00" class="input-pro">
                        </div>
                    </div>

                    <div class="space-y-5">
                        <h3 class="flex items-center gap-2 text-[11px] font-black text-emerald-600 uppercase tracking-widest border-b pb-3">
                            <i data-lucide="ship" class="w-4 h-4"></i> Détails Logistiques
                        </h3>
                        <div>
                            <label class="form-label">Compagnie</label>
                            <select id="vessel" class="input-pro font-bold">
                                <optgroup label="MARITIME">
                                    <option value="CMA CGM">CMA CGM</option>
                                    <option value="Maersk Line">Maersk Line</option>
                                    <option value="MSC Gabon">MSC Gabon</option>
                                    <option value="Grimaldi Lines">Grimaldi Lines</option>
                                </optgroup>
                                <optgroup label="AÉRIEN">
                                    <option value="Sky Gabon">Sky Gabon</option>
                                    <option value="DHL Express">DHL Express</option>
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

                    <div class="space-y-5">
                        <h3 class="flex items-center gap-2 text-[11px] font-black text-orange-600 uppercase tracking-widest border-b pb-3">
                            <i data-lucide="calculator" class="w-4 h-4"></i> Calcul Douane (CEMAC)
                        </h3>
                        <div>
                            <label class="form-label">Valeur CFR (FCFA)</label>
                            <input type="number" id="cfr-value" placeholder="Montant Facture + Fret" class="input-pro text-blue-700 font-black text-lg">
                        </div>
                        <div>
                            <label class="form-label">Catégorie Tarifaire</label>
                            <select id="category" class="input-pro font-bold">
                                <option value="sante">Cat 1 : Santé & Médicaments (5%)</option>
                                <option value="machines">Cat 2 : Machines & Équipements (10%)</option>
                                <option value="pieces" selected>Cat 3 : Pièces & Divers (20%)</option>
                                <option value="vehicules">Cat 4 : Électroménager / Véhicules (30%)</option>
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

            <div id="quick-res" class="hidden bg-slate-900 rounded-3xl p-8 shadow-2xl border border-slate-800">
                <div class="flex flex-col md:flex-row justify-between items-center gap-8">
                    <div class="text-center md:text-left">
                        <p id="res-cat-label" class="text-blue-500 font-bold text-[10px] uppercase tracking-[0.2em] mb-2">CHARGEMENT...</p>
                        <h3 id="quick-tax" class="text-5xl font-black tracking-tighter text-white leading-none">0 FCFA</h3>
                        <p class="text-slate-500 text-[10px] font-bold mt-4 uppercase">Estimation Totale (CEMAC 2026)</p>
                    </div>
                    <div class="grid grid-cols-2 gap-x-12 gap-y-4 text-[10px] font-bold text-slate-500 border-t md:border-t-0 md:border-l border-slate-800 pt-6 md:pt-0 md:pl-12">
                        <div>DROITS DOUANE: <span id="res-dd" class="text-white block font-mono text-xs mt-1">--</span></div>
                        <div>TVA (18%): <span id="res-tva" class="text-white block font-mono text-xs mt-1">--</span></div>
                        <div>REDEVANCE STAT.: <span id="res-rs" class="text-white block font-mono text-xs mt-1">--</span></div>
                        <div>SYDONIA / PHS: <span id="res-sys" class="text-white block font-mono text-xs mt-1">--</span></div>
                    </div>
                </div>
            </div>
        </section>

        <section id="section-missions" class="hidden space-y-6">
            <div id="list-active" class="grid grid-cols-1 gap-4">
                </div>
        </section>

        <section id="section-archive" class="hidden space-y-6">
            <div id="list-archive" class="grid grid-cols-1 gap-4 opacity-75">
                </div>
        </section>
    </main>

    <div id="modal-docs" class="hidden fixed inset-0 bg-slate-900/80 backdrop-blur-sm z-[100] flex items-center justify-center p-4">
        <div class="bg-white w-full max-w-lg rounded-3xl overflow-hidden shadow-2xl">
            <div class="p-6 bg-slate-50 border-b flex justify-between items-center">
                <div>
                    <h3 class="font-black text-slate-900 uppercase text-xs tracking-widest">Dossier Numérique</h3>
                    <p class="text-[9px] text-slate-400 font-bold uppercase mt-1">Stockage WebP Optimisé</p>
                </div>
                <button onclick="closeDocs()" class="text-slate-400 hover:text-slate-900"><i data-lucide="x" class="w-5 h-5"></i></button>
            </div>
            <div class="p-6 space-y-4">
                <div class="border-2 border-dashed border-slate-200 rounded-2xl p-8 text-center hover:bg-slate-50 transition-all cursor-pointer relative group">
                    <input type="file" id="file-input" multiple accept="image/*" class="absolute inset-0 opacity-0 cursor-pointer" onchange="handleFiles(this)">
                    <i data-lucide="upload-cloud" class="w-10 h-10 text-blue-500 mx-auto mb-2"></i>
                    <p class="text-xs font-bold text-slate-600">Ajouter des photos (Factures, BL...)</p>
                </div>
                <div id="doc-list" class="space-y-2 max-h-64 overflow-y-auto custom-scrollbar"></div>
            </div>
        </div>
    </div>

    <script>
        // Configuration Tarifaire CEMAC
        const CONFIG = {
            sante: { dd: 0.05, label: "SANTÉ / INDISPENSABLE (5%)" },
            machines: { dd: 0.10, label: "BIENS D'ÉQUIPEMENT (10%)" },
            pieces: { dd: 0.20, label: "MATIÈRES / DIVERS (20%)" },
            vehicules: { dd: 0.30, label: "CONSOMMATION FINALE (30%)" }
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

        // --- CALCULS ---
        function getTaxes(cfr, cat) {
            const c = CONFIG[cat] || CONFIG.pieces;
            const caf = cfr * 1.01; // Simulation Assurance & Ajustements
            const dd = caf * c.dd;
            const rs = caf * 0.015;
            const tva = (caf + dd + rs) * 0.18;
            const sys = 35000; // Forfait Sydonia + PHS
            
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
            if (!v) return alert("Veuillez saisir une valeur CFR.");
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
            if (!client || !val) return alert("Nom client et Valeur CFR requis.");

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
            alert("Mission créée avec succès !");
        }

        // --- EXPORT PDF ---
        function exportToPDF(id) {
            const { jsPDF } = window.jspdf;
            const doc = new jsPDF();
            const m = missions.find(mission => mission.id === id);
            const r = getTaxes(m.cfr, m.cat);

            // En-tête
            doc.setFillColor(30, 41, 59);
            doc.rect(0, 0, 210, 40, 'F');
            doc.setTextColor(255, 255, 255);
            doc.setFontSize(22);
            doc.text("TRANSIT PRO GABON", 14, 25);
            doc.setFontSize(10);
            doc.text(`Owendo Port - Fiche de Cotisation Logistique`, 14, 33);

            // Corps
            doc.setTextColor(30, 41, 59);
            doc.setFontSize(12);
            doc.setFont(undefined, 'bold');
            doc.text("INFORMATIONS DOSSIER", 14, 55);
            doc.setFont(undefined, 'normal');
            doc.setFontSize(10);
            doc.text(`Client: ${m.client} (NIF: ${m.nif})`, 14, 65);
            doc.text(`Référence: ${m.ref}`, 14, 72);
            doc.text(`Transporteur: ${m.carrier} (${m.mode})`, 14, 79);
            doc.text(`Provenance: ${m.origin}`, 14, 86);

            // Tableau
            doc.autoTable({
                startY: 95,
                head: [['Libellé Taxe', 'Taux', 'Montant (FCFA)']],
                body: [
                    ['Valeur CFR (Base)', '-', format(m.cfr)],
                    ['Droits de Douane (DD)', r.label.split('(')[1].replace(')',''), format(r.dd_v)],
                    ['Redevance Statistique (RS)', '1.5%', format(r.rs_v)],
                    ['TVA', '18%', format(r.tva_v)],
                    ['Sydonia / PHS', 'Forfait', format(r.sys_v)],
                ],
                foot: [['TOTAL ESTIMÉ', '', `${format(r.total)} FCFA`]],
                theme: 'striped',
                headStyles: { fillColor: [37, 99, 235] },
                footStyles: { fillColor: [30, 41, 59] }
            });

            doc.save(`TransitPro_${m.client}_${m.ref}.pdf`);
        }

        // --- GESTION DOCS ---
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
                const compressed = await compressImage(file);
                mission.docs.push({ name: file.name, data: compressed });
            }
            save(); renderDocs(); render();
        }

        function compressImage(file) {
            return new Promise((resolve) => {
                const reader = new FileReader();
                reader.readAsDataURL(file);
                reader.onload = (e) => {
                    const img = new Image();
                    img.src = e.target.result;
                    img.onload = () => {
                        const canvas = document.createElement('canvas');
                        const ctx = canvas.getContext('2d');
                        canvas.width = 600; canvas.height = (img.height / img.width) * 600;
                        ctx.drawImage(img, 0, 0, canvas.width, canvas.height);
                        resolve(canvas.toDataURL('image/webp', 0.6));
                    };
                };
            });
        }

        function renderDocs() {
            const list = document.getElementById('doc-list');
            const m = missions.find(m => m.id === activeMissionId);
            list.innerHTML = m.docs.length ? '' : '<p class="text-center text-[10px] py-4">Aucun document.</p>';
            m.docs.forEach((d, i) => {
                list.innerHTML += `
                    <div class="flex items-center gap-3 p-2 bg-slate-50 rounded-xl border border-slate-100">
                        <img src="${d.data}" class="doc-preview">
                        <span class="flex-1 text-[10px] font-bold truncate">${d.name}</span>
                        <button onclick="removeDoc(${i})" class="text-rose-500 p-2"><i data-lucide="trash-2" class="w-4 h-4"></i></button>
                    </div>
                `;
            });
            lucide.createIcons();
        }

        function removeDoc(i) {
            missions.find(m => m.id === activeMissionId).docs.splice(i, 1);
            save(); renderDocs(); render();
        }

        // --- CORE ---
        function setStatus(id, s) { missions = missions.map(m => m.id === id ? {...m, status: s} : m); save(); render(); }
        function deleteM(id) { if(confirm("Supprimer définitivement ?")) { missions = missions.filter(m => m.id !== id); save(); render(); } }
        function save() { localStorage.setItem('transit_gabon_vfinal', JSON.stringify(missions)); }
        function format(n) { return new Intl.NumberFormat('fr-FR').format(n); }

        function render() {
            const act = document.getElementById('list-active');
            const arc = document.getElementById('list-archive');
            act.innerHTML = ""; arc.innerHTML = "";

            missions.forEach(m => {
                const r = getTaxes(m.cfr, m.cat);
                const html = `
                    <div class="bg-white rounded-3xl border border-slate-200 shadow-sm overflow-hidden hover:border-blue-400 transition-all p-5">
                        <div class="flex flex-col md:flex-row gap-5 items-center">
                            <div class="${m.mode === 'SEA' ? 'bg-blue-600' : 'bg-emerald-600'} w-12 h-12 flex items-center justify-center rounded-2xl text-white">
                                <i data-lucide="${m.mode === 'SEA' ? 'ship' : 'plane'}" class="w-6 h-6"></i>
                            </div>
                            <div class="flex-1 text-center md:text-left">
                                <h4 class="font-black text-slate-900 uppercase text-sm">${m.client}</h4>
                                <p class="text-[10px] font-bold text-slate-400 uppercase tracking-widest">${m.carrier} • ${m.ref} • ${m.origin}</p>
                            </div>
                            <div class="flex items-center gap-3">
                                <button onclick="exportToPDF(${m.id})" class="p-2.5 bg-blue-50 text-blue-600 rounded-xl hover:bg-blue-100 transition-all" title="Télécharger PDF">
                                    <i data-lucide="download" class="w-5 h-5"></i>
                                </button>
                                <button onclick="openDocs(${m.id})" class="relative p-2.5 bg-slate-100 text-slate-600 rounded-xl hover:bg-slate-200 transition-all">
                                    <i data-lucide="paperclip" class="w-5 h-5"></i>
                                    ${m.docs.length ? `<span class="absolute -top-1 -right-1 bg-blue-600 text-white text-[8px] w-4 h-4 flex items-center justify-center rounded-full">${m.docs.length}</span>` : ''}
                                </button>
                                <div class="text-right border-l pl-5 border-slate-100 min-w-[120px]">
                                    <p class="text-lg font-black text-slate-900">${format(r.total)}</p>
                                    <p class="text-[8px] font-black text-blue-500 uppercase">CFA (ESTIMÉ)</p>
                                </div>
                            </div>
                        </div>
                        <div class="mt-4 pt-4 border-t flex justify-between items-center">
                            <div class="flex items-center gap-2">
                                ${getStatusBadge(m.status)}
                                <span class="text-[9px] font-bold text-slate-400 uppercase">${m.date}</span>
                            </div>
                            <div class="flex gap-2">
                                ${m.status !== 'fini' ? `
                                    <button onclick="setStatus(${m.id}, 'douane')" class="px-3 py-1 bg-slate-900 text-white rounded-lg text-[9px] font-black uppercase">En Douane</button>
                                    <button onclick="setStatus(${m.id}, 'fini')" class="p-1.5 bg-emerald-100 text-emerald-600 rounded-lg"><i data-lucide="check" class="w-4 h-4"></i></button>
                                ` : `
                                    <button onclick="deleteM(${m.id})" class="p-1.5 bg-rose-50 text-rose-500 rounded-lg"><i data-lucide="trash-2" class="w-4 h-4"></i></button>
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
            if(s === 'init') return '<span class="status-badge bg-amber-50 text-amber-600 border-amber-200">En Attente</span>';
            if(s === 'douane') return '<span class="status-badge bg-blue-100 text-blue-600 border-blue-200 animate-pulse">Déclaration...</span>';
            return '<span class="status-badge bg-slate-200 text-slate-500 border-slate-300">Clôturé</span>';
        }
    </script>
</body>
</html>
