<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Système de Gestion de Cas - VBG & Juridique</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <style>
        body { font-family: 'Inter', sans-serif; }
        .card-shadow { box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1); }
        .status-en-cours { @apply bg-blue-100 text-blue-800; }
        .status-resolu { @apply bg-green-100 text-green-800; }
        .status-en-attente { @apply bg-yellow-100 text-yellow-800; }
        .status-juge { @apply bg-green-100 text-green-800; }
        .no-print { @media print { display: none; } }
    </style>
</head>
<body class="bg-gray-50 min-h-screen">

    <div id="loginScreen" class="min-h-screen flex items-center justify-center bg-gradient-to-br from-purple-700 via-blue-800 to-indigo-900 p-4">
        <div class="bg-white p-8 rounded-2xl shadow-2xl w-full max-w-md">
            <div class="text-center mb-8">
                <div class="bg-blue-100 w-16 h-16 rounded-full flex items-center justify-center mx-auto mb-4">
                    <i data-lucide="shield-check" class="text-blue-600 w-10 h-10"></i>
                </div>
                <h1 class="text-2xl font-bold text-gray-900">Gestion de Cas</h1>
                <p class="text-gray-500">Sécurisé & Confidentiel</p>
            </div>
            <form id="loginForm" class="space-y-4">
                <div>
                    <label class="block text-sm font-medium text-gray-700 mb-1">Identifiant</label>
                    <input type="text" id="loginUsername" required class="w-full px-4 py-2 border rounded-lg outline-none focus:ring-2 focus:ring-blue-500">
                </div>
                <div>
                    <label class="block text-sm font-medium text-gray-700 mb-1">Mot de passe</label>
                    <input type="password" id="loginPassword" required class="w-full px-4 py-2 border rounded-lg outline-none focus:ring-2 focus:ring-blue-500">
                </div>
                <div id="loginError" class="hidden text-red-500 text-sm text-center font-medium"></div>
                <button type="submit" class="w-full bg-blue-600 text-white py-2 rounded-lg font-bold hover:bg-blue-700 transition">Se connecter</button>
            </form>
        </div>
    </div>

    <div id="mainApp" class="hidden flex flex-col min-h-screen">
        <header class="bg-white border-b border-gray-200 px-6 py-4 flex justify-between items-center sticky top-0 z-30">
            <div class="flex items-center space-x-3">
                <i data-lucide="layout-dashboard" class="text-blue-600"></i>
                <h1 class="text-xl font-bold text-gray-800">Portail Interne</h1>
            </div>
            <div class="flex items-center space-x-4">
                <div class="text-right hidden sm:block">
                    <p id="userDisplayName" class="text-sm font-bold text-gray-900"></p>
                    <p id="userRoleDisplay" class="text-xs text-gray-500"></p>
                </div>
                <button onclick="logout()" class="text-gray-400 hover:text-red-500 transition">
                    <i data-lucide="log-out"></i>
                </button>
            </div>
        </header>

        <main class="flex-1 p-6 overflow-y-auto">
            
            <div id="moduleSelection" class="max-w-4xl mx-auto grid md:grid-cols-2 gap-6 mt-10">
                <div onclick="selectModule('vbg')" class="bg-white p-8 rounded-2xl shadow hover:shadow-xl transition cursor-pointer border-t-4 border-purple-600 group">
                    <div class="bg-purple-100 w-16 h-16 rounded-xl flex items-center justify-center mb-6 group-hover:scale-110 transition">
                        <i data-lucide="heart" class="text-purple-600 w-8 h-8"></i>
                    </div>
                    <h2 class="text-2xl font-bold mb-2">Module VBG</h2>
                    <p class="text-gray-600">Gestion des cas de violences basées sur le genre et suivis de protection.</p>
                </div>
                <div onclick="selectModule('juridique')" class="bg-white p-8 rounded-2xl shadow hover:shadow-xl transition cursor-pointer border-t-4 border-blue-600 group">
                    <div class="bg-blue-100 w-16 h-16 rounded-xl flex items-center justify-center mb-6 group-hover:scale-110 transition">
                        <i data-lucide="gavel" class="text-blue-600 w-8 h-8"></i>
                    </div>
                    <h2 class="text-2xl font-bold mb-2">Module Juridique</h2>
                    <p class="text-gray-600">Assistance légale, suivi judiciaire et accompagnement devant les tribunaux.</p>
                </div>
            </div>

            <div id="vbgModule" class="hidden">
                 <div class="flex items-center justify-between mb-6">
                    <button onclick="backToModules()" class="text-gray-500 hover:text-gray-700 flex items-center">
                        <i data-lucide="chevron-left" class="mr-1"></i> Retour
                    </button>
                    <button onclick="openNewCaseModal('vbg')" class="bg-purple-600 text-white px-4 py-2 rounded-lg flex items-center">
                        <i data-lucide="plus" class="mr-2"></i> Nouveau Cas VBG
                    </button>
                 </div>
                 <div class="bg-white rounded-xl shadow overflow-x-auto">
                    <table class="w-full text-left">
                        <thead class="bg-gray-50 border-b">
                            <tr>
                                <th class="p-4 font-semibold">ID</th>
                                <th class="p-4 font-semibold">Nom</th>
                                <th class="p-4 font-semibold">Type</th>
                                <th class="p-4 font-semibold">Statut</th>
                                <th class="p-4 font-semibold text-right">Actions</th>
                            </tr>
                        </thead>
                        <tbody id="vbgTableBody"></tbody>
                    </table>
                 </div>
            </div>

            <div id="juridiqueModule" class="hidden">
                 <div class="flex items-center justify-between mb-6">
                    <button onclick="backToModules()" class="text-gray-500 hover:text-gray-700 flex items-center">
                        <i data-lucide="chevron-left" class="mr-1"></i> Retour
                    </button>
                    <button onclick="openNewCaseModal('juridique')" class="bg-blue-600 text-white px-4 py-2 rounded-lg flex items-center">
                        <i data-lucide="plus" class="mr-2"></i> Nouveau Cas Juridique
                    </button>
                 </div>
                 <div class="bg-white rounded-xl shadow overflow-x-auto">
                    <table class="w-full text-left">
                        <thead class="bg-gray-50 border-b">
                            <tr>
                                <th class="p-4 font-semibold">ID</th>
                                <th class="p-4 font-semibold">Nom</th>
                                <th class="p-4 font-semibold">Infraction</th>
                                <th class="p-4 font-semibold">Statut</th>
                                <th class="p-4 font-semibold text-right">Actions</th>
                            </tr>
                        </thead>
                        <tbody id="juridiqueTableBody"></tbody>
                    </table>
                 </div>
            </div>

        </main>
    </div>

    <div id="caseModal" class="hidden fixed inset-0 bg-black/50 z-50 flex items-center justify-center p-4">
        <div class="bg-white w-full max-w-2xl rounded-2xl p-6 shadow-2xl max-h-[90vh] overflow-y-auto">
            <h2 id="modalTitle" class="text-xl font-bold mb-4">Nouveau Cas</h2>
            <form id="caseForm" class="space-y-4">
                <input type="hidden" id="caseModule">
                <input type="hidden" id="caseId">
                <div class="grid grid-cols-2 gap-4">
                    <div>
                        <label class="block text-sm font-medium">Nom Complet</label>
                        <input type="text" id="benefNom" required class="w-full p-2 border rounded">
                    </div>
                    <div>
                        <label class="block text-sm font-medium">Téléphone</label>
                        <input type="text" id="benefTel" class="w-full p-2 border rounded">
                    </div>
                </div>
                <div id="vbgFields" class="hidden space-y-4">
                    <label class="block text-sm font-medium">Type de Violence</label>
                    <select id="vbgType" class="w-full p-2 border rounded">
                        <option value="Physique">Physique</option>
                        <option value="Sexuelle">Sexuelle</option>
                        <option value="Psychologique">Psychologique</option>
                    </select>
                </div>
                <div id="juridiqueFields" class="hidden space-y-4">
                    <label class="block text-sm font-medium">Infraction</label>
                    <input type="text" id="jurInfraction" class="w-full p-2 border rounded">
                </div>
                <div class="flex justify-end space-x-3 mt-6">
                    <button type="button" onclick="closeModal()" class="px-4 py-2 border rounded">Annuler</button>
                    <button type="submit" class="px-4 py-2 bg-blue-600 text-white rounded">Enregistrer</button>
                </div>
            </form>
        </div>
    </div>

    <script>
        // --- PERSISTENCE LOGIC ---
        let users = JSON.parse(localStorage.getItem('cas_users')) || [
            { username: 'admin', password: 'admin123', role: 'admin' }
        ];
        let vbgCases = JSON.parse(localStorage.getItem('cas_vbg')) || [];
        let juridiqueCases = JSON.parse(localStorage.getItem('cas_jur')) || [];

        function saveToDisk() {
            localStorage.setItem('cas_users', JSON.stringify(users));
            localStorage.setItem('cas_vbg', JSON.stringify(vbgCases));
            localStorage.setItem('cas_jur', JSON.stringify(juridiqueCases));
        }

        let currentUser = null;

        // Login
        document.getElementById('loginForm').addEventListener('submit', (e) => {
            e.preventDefault();
            const u = document.getElementById('loginUsername').value;
            const p = document.getElementById('loginPassword').value;
            const found = users.find(user => user.username === u && user.password === p);

            if(found) {
                currentUser = found;
                document.getElementById('loginScreen').classList.add('hidden');
                document.getElementById('mainApp').classList.remove('hidden');
                document.getElementById('userDisplayName').innerText = found.username;
                document.getElementById('userRoleDisplay').innerText = found.role;
                lucide.createIcons();
            } else {
                document.getElementById('loginError').classList.remove('hidden');
                document.getElementById('loginError').innerText = "Erreur de connexion";
            }
        });

        function logout() {
            location.reload();
        }

        function selectModule(mod) {
            document.getElementById('moduleSelection').classList.add('hidden');
            document.getElementById('vbgModule').classList.toggle('hidden', mod !== 'vbg');
            document.getElementById('juridiqueModule').classList.toggle('hidden', mod !== 'juridique');
            renderTables();
        }

        function backToModules() {
            document.getElementById('moduleSelection').classList.remove('hidden');
            document.getElementById('vbgModule').classList.add('hidden');
            document.getElementById('juridiqueModule').classList.add('hidden');
        }

        function openNewCaseModal(mod) {
            document.getElementById('caseModule').value = mod;
            document.getElementById('vbgFields').classList.toggle('hidden', mod !== 'vbg');
            document.getElementById('juridiqueFields').classList.toggle('hidden', mod !== 'juridique');
            document.getElementById('caseModal').classList.remove('hidden');
        }

        function closeModal() {
            document.getElementById('caseModal').classList.add('hidden');
            document.getElementById('caseForm').reset();
        }

        document.getElementById('caseForm').addEventListener('submit', (e) => {
            e.preventDefault();
            const mod = document.getElementById('caseModule').value;
            const newCase = {
                id: Date.now(),
                nom: document.getElementById('benefNom').value,
                tel: document.getElementById('benefTel').value,
                statut: 'En cours'
            };

            if(mod === 'vbg') {
                newCase.type = document.getElementById('vbgType').value;
                vbgCases.push(newCase);
            } else {
                newCase.infraction = document.getElementById('jurInfraction').value;
                juridiqueCases.push(newCase);
            }

            saveToDisk();
            closeModal();
            renderTables();
        });

        function deleteCase(mod, id) {
            if(!confirm("Supprimer ce cas ?")) return;
            if(mod === 'vbg') vbgCases = vbgCases.filter(c => c.id !== id);
            else juridiqueCases = juridiqueCases.filter(c => c.id !== id);
            saveToDisk();
            renderTables();
        }

        function renderTables() {
            document.getElementById('vbgTableBody').innerHTML = vbgCases.map(c => `
                <tr class="border-b hover:bg-gray-50">
                    <td class="p-4 text-xs text-gray-400">#${c.id}</td>
                    <td class="p-4 font-medium">${c.nom}</td>
                    <td class="p-4">${c.type}</td>
                    <td class="p-4"><span class="px-2 py-1 bg-blue-100 text-blue-800 rounded-full text-xs">${c.statut}</span></td>
                    <td class="p-4 text-right">
                        <button onclick="deleteCase('vbg', ${c.id})" class="text-red-500"><i data-lucide="trash-2" class="w-4 h-4"></i></button>
                    </td>
                </tr>
            `).join('');

            document.getElementById('juridiqueTableBody').innerHTML = juridiqueCases.map(c => `
                <tr class="border-b hover:bg-gray-50">
                    <td class="p-4 text-xs text-gray-400">#${c.id}</td>
                    <td class="p-4 font-medium">${c.nom}</td>
                    <td class="p-4">${c.infraction}</td>
                    <td class="p-4"><span class="px-2 py-1 bg-blue-100 text-blue-800 rounded-full text-xs">${c.statut}</span></td>
                    <td class="p-4 text-right">
                        <button onclick="deleteCase('jur', ${c.id})" class="text-red-500"><i data-lucide="trash-2" class="w-4 h-4"></i></button>
                    </td>
                </tr>
            `).join('');
            lucide.createIcons();
        }

        lucide.createIcons();
    </script>
</body>
</html>
