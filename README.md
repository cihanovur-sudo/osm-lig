
<!DOCTYPE html>
<html lang="tr" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>OSM Tactical League Simulator</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <script>
        tailwind.config = {
            darkMode: 'class',
            theme: {
                extend: {
                    colors: {
                        pitch: {
                            DEFAULT: '#1b4332',
                            dark: '#081c15',
                            lines: '#2d6a4f'
                        },
                        accent: {
                            500: '#10b981',
                            600: '#059669',
                            400: '#34d399'
                        }
                    }
                }
            }
        }
    </script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800&display=swap" rel="stylesheet">
    <script src="https://unpkg.com/lucide@latest"></script>
    <style>
        body { font-family: 'Inter', sans-serif; }
        .pitch-bg {
            background-color: #1b4332;
            background-image: radial-gradient(#2d6a4f 1px, transparent 1px);
            background-size: 20px 20px;
        }
    </style>
</head>
<body class="bg-slate-950 text-slate-100 min-h-screen flex flex-col antialiased selection:bg-accent-500 selection:text-white">

    <!-- Top Navigation -->
    <header class="border-b border-slate-800 bg-slate-900/80 backdrop-blur sticky top-0 z-40">
        <div class="max-w-7xl mx-auto px-4 h-16 flex items-center justify-between">
            <div class="flex items-center space-x-3">
                <div class="p-2 bg-accent-500/20 text-accent-400 rounded-xl border border-accent-500/30">
                    <i data-lucide="trophy" class="w-6 h-6"></i>
                </div>
                <div>
                    <h1 class="font-extrabold text-lg tracking-tight text-white flex items-center gap-2">
                        OSM Ligi <span class="text-xs font-semibold px-2 py-0.5 rounded bg-emerald-500/20 text-emerald-400 border border-emerald-500/30">6 Takım</span>
                    </h1>
                    <p class="text-xs text-slate-400">Çok Oyunculu Taktik & Gelişim Simülatörü</p>
                </div>
            </div>

            <!-- Active User / Team Banner -->
            <div class="flex items-center gap-3">
                <div id="currentTeamBadge" class="hidden sm:flex items-center gap-2 bg-slate-800/90 border border-slate-700 px-3 py-1.5 rounded-xl text-sm font-medium">
                    <span class="w-2 h-2 rounded-full bg-emerald-400 animate-pulse"></span>
                    <span id="activeTeamName" class="text-emerald-400 font-semibold">Takım Seçilmedi</span>
                </div>
                <button onclick="openTeamSelectModal()" class="px-3.5 py-1.5 bg-slate-800 hover:bg-slate-700 border border-slate-700 rounded-xl text-xs font-semibold transition-all flex items-center gap-2">
                    <i data-lucide="users" class="w-4 h-4"></i> Takım Değiştir / Gir
                </button>
            </div>
        </div>
    </header>

    <!-- Main Dashboard Container -->
    <main class="max-w-7xl mx-auto px-4 py-6 flex-1 w-full grid grid-cols-1 lg:grid-cols-12 gap-6">

        <!-- Left Column: Navigation & League Table (4 cols) -->
        <div class="lg:col-span-4 space-y-6">
            
            <!-- League Info & Sim Trigger Card -->
            <div class="bg-slate-900 border border-slate-800 rounded-2xl p-5 shadow-xl relative overflow-hidden">
                <div class="absolute -right-10 -bottom-10 opacity-10 text-emerald-500 pointer-events-none">
                    <i data-lucide="zap" class="w-48 h-48"></i>
                </div>
                <div class="flex items-center justify-between mb-4">
                    <div>
                        <span class="text-xs font-semibold text-slate-400 uppercase tracking-wider">Lig Durumu</span>
                        <h2 id="currentMatchdayText" class="text-xl font-bold text-white">Hafta 1 / 10</h2>
                    </div>
                    <span id="syncBadge" class="text-xs bg-slate-800 text-slate-300 px-2.5 py-1 rounded-full border border-slate-700 flex items-center gap-1">
                        <i data-lucide="cloud" class="w-3.5 h-3.5 text-emerald-400"></i> Senkronize
                    </span>
                </div>

                <div class="space-y-3">
                    <button onclick="simulateNextMatchday()" class="w-full py-3 bg-gradient-to-r from-emerald-500 to-teal-600 hover:from-emerald-600 hover:to-teal-700 text-white font-bold rounded-xl shadow-lg shadow-emerald-500/20 transition-all flex items-center justify-center gap-2">
                        <i data-lucide="play" class="w-5 h-5 fill-current"></i> Haftayı Simüle Et
                    </button>
                    <button onclick="resetLeague()" class="w-full py-2 bg-slate-800/80 hover:bg-slate-800 text-slate-400 hover:text-red-400 border border-slate-700/60 rounded-xl text-xs font-medium transition-all">
                        Ligi Sıfırla
                    </button>
                </div>
            </div>

            <!-- Standings Table -->
            <div class="bg-slate-900 border border-slate-800 rounded-2xl p-5 shadow-xl">
                <div class="flex items-center justify-between mb-4">
                    <h3 class="font-bold text-base text-white flex items-center gap-2">
                        <i data-lucide="list-ordered" class="w-4 h-4 text-emerald-400"></i> Puan Durumu
                    </h3>
                </div>
                <div class="overflow-x-auto">
                    <table class="w-full text-left text-xs">
                        <thead>
                            <tr class="text-slate-400 border-b border-slate-800">
                                <th class="pb-2 font-medium">#</th>
                                <th class="pb-2 font-medium">Takım</th>
                                <th class="pb-2 font-medium text-center">O</th>
                                <th class="pb-2 font-medium text-center">G</th>
                                <th class="pb-2 font-medium text-center">A/Y</th>
                                <th class="pb-2 font-medium text-center font-bold text-emerald-400">P</th>
                            </tr>
                        </thead>
                        <tbody id="standingsTableBody" class="divide-y divide-slate-800/60">
                            <!-- JS Injected -->
                        </tbody>
                    </table>
                </div>
            </div>

            <!-- Match Logs Accordion / Recent Results -->
            <div class="bg-slate-900 border border-slate-800 rounded-2xl p-5 shadow-xl">
                <h3 class="font-bold text-base text-white mb-3 flex items-center gap-2">
                    <i data-lucide="history" class="w-4 h-4 text-emerald-400"></i> Son Maç Sonuçları
                </h3>
                <div id="recentMatchesList" class="space-y-2 max-h-60 overflow-y-auto pr-1 text-xs">
                    <p class="text-slate-500 italic text-center py-4">Henüz maç oynanmadı.</p>
                </div>
            </div>

        </div>

        <!-- Right Column: Active Team Panel (Tactics, Squad, Roles) (8 cols) -->
        <div class="lg:col-span-8 space-y-6">

            <!-- Tab Navigation for Team Management -->
            <div class="bg-slate-900 border border-slate-800 rounded-2xl p-2 flex gap-2">
                <button onclick="switchTab('tactics')" id="tabBtnTactics" class="flex-1 py-2.5 rounded-xl font-semibold text-xs transition-all bg-emerald-500 text-white shadow-md flex items-center justify-center gap-2">
                    <i data-lucide="sliders" class="w-4 h-4"></i> Taktik ve Stil
                </button>
                <button onclick="switchTab('squad')" id="tabBtnSquad" class="flex-1 py-2.5 rounded-xl font-semibold text-xs text-slate-400 hover:text-white transition-all flex items-center justify-center gap-2">
                    <i data-lucide="users" class="w-4 h-4"></i> Kadro & Oyuncu Gelişimi
                </button>
                <button onclick="switchTab('roles')" id="tabBtnRoles" class="flex-1 py-2.5 rounded-xl font-semibold text-xs text-slate-400 hover:text-white transition-all flex items-center justify-center gap-2">
                    <i data-lucide="user-check" class="w-4 h-4"></i> Görev Atamaları
                </button>
            </div>

            <!-- TAB 1: TACTICS & STYLE -->
            <div id="tabContentTactics" class="bg-slate-900 border border-slate-800 rounded-2xl p-6 shadow-xl space-y-6">
                <div class="flex items-center justify-between border-b border-slate-800 pb-4">
                    <div>
                        <h3 class="text-lg font-bold text-white flex items-center gap-2">
                            <i data-lucide="shield" class="w-5 h-5 text-emerald-400"></i> Takım Taktikleri
                        </h3>
                        <p class="text-xs text-slate-400">Taktik ve tempo rakibe göre simülasyon şansınızı etkiler.</p>
                    </div>
                    <button onclick="saveTactics()" class="px-4 py-2 bg-emerald-500 hover:bg-emerald-600 text-white font-semibold rounded-xl text-xs shadow-lg transition-all flex items-center gap-1.5">
                        <i data-lucide="save" class="w-4 h-4"></i> Taktikleri Kaydet
                    </button>
                </div>

                <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                    <!-- Playing Style -->
                    <div class="space-y-2">
                        <label class="block text-xs font-medium text-slate-300">Oyun Stili</label>
                        <select id="tacticStyle" class="w-full bg-slate-800 border border-slate-700 rounded-xl px-3 py-2.5 text-sm text-white focus:outline-none focus:ring-2 focus:ring-emerald-500">
                            <option value="Tiki-Taka">Tiki-Taka (Pas & Kontrol)</option>
                            <option value="Counter">Kontra Atak (Hızlı Çıkış)</option>
                            <option value="LongBall">Uzun Top (Fiziksel)</option>
                            <option value="ParkTheBus">Otobüsü Çek (Aşırı Savunma)</option>
                        </select>
                        <p class="text-[11px] text-slate-500">Tiki-Taka topa sahip olmaya yarar; Kontra Atak hızlı hücumlara odaklanır.</p>
                    </div>

                    <!-- Tempo -->
                    <div class="space-y-2">
                        <label class="block text-xs font-medium text-slate-300">Oyun Temposu</label>
                        <select id="tacticTempo" class="w-full bg-slate-800 border border-slate-700 rounded-xl px-3 py-2.5 text-sm text-white focus:outline-none focus:ring-2 focus:ring-emerald-500">
                            <option value="Yavaş">Yavaş & Sabırlı Pas</option>
                            <option value="Dengeli">Dengeli Tempo</option>
                            <option value="Yüksek">Yüksek Baskı & Tempolu</option>
                        </select>
                        <p class="text-[11px] text-slate-500">Yüksek tempo hücumu artırır ama yorgunluk/kart riskini yükseltir.</p>
                    </div>

                    <!-- Mentality Slider -->
                    <div class="space-y-2">
                        <div class="flex justify-between text-xs font-medium">
                            <span class="text-slate-300">Oyun Anlayışı</span>
                            <span id="mentalityValue" class="text-emerald-400 font-bold">Dengeli (%50)</span>
                        </div>
                        <input type="range" id="tacticMentality" min="10" max="90" value="50" oninput="updateMentalityLabel(this.value)" class="w-full accent-emerald-500">
                        <div class="flex justify-between text-[10px] text-slate-500">
                            <span>Aşırı Savunmacı</span>
                            <span>Aşırı Hücumcu</span>
                        </div>
                    </div>

                    <!-- Aggression Slider -->
                    <div class="space-y-2">
                        <div class="flex justify-between text-xs font-medium">
                            <span class="text-slate-300">Mücadele Hırsı / Pres</span>
                            <span id="aggressionValue" class="text-emerald-400 font-bold">Normal (%50)</span>
                        </div>
                        <input type="range" id="tacticAggression" min="10" max="90" value="50" oninput="updateAggressionLabel(this.value)" class="w-full accent-emerald-500">
                        <div class="flex justify-between text-[10px] text-slate-500">
                            <span>Sakin / Sakatlanma Az</span>
                            <span>Agresif / Kart Riski Yüksek</span>
                        </div>
                    </div>
                </div>

                <!-- Visual Pitch Preview -->
                <div class="pitch-bg rounded-2xl p-6 border border-emerald-900/60 flex flex-col items-center justify-center min-h-[160px] relative">
                    <div class="text-center bg-slate-950/80 backdrop-blur px-4 py-2 rounded-xl border border-slate-800">
                        <span class="text-xs text-slate-400 block">Saha Diziliş Özeti</span>
                        <span id="pitchSummaryText" class="text-sm font-bold text-emerald-400">4-3-3 Dengeli Tiki-Taka</span>
                    </div>
                </div>
            </div>

            <!-- TAB 2: SQUAD & PLAYER DEVELOPMENT -->
            <div id="tabContentSquad" class="hidden bg-slate-900 border border-slate-800 rounded-2xl p-6 shadow-xl space-y-4">
                <div class="flex items-center justify-between border-b border-slate-800 pb-4">
                    <div>
                        <h3 class="text-lg font-bold text-white flex items-center gap-2">
                            <i data-lucide="users" class="w-5 h-5 text-emerald-400"></i> Oyuncu Kadrosu ve Reytingler
                        </h3>
                        <p class="text-xs text-slate-400">Maç performanslarına ve istikrara göre oyuncuların OVR seviyeleri dinamik artar/düşer.</p>
                    </div>
                </div>

                <div class="overflow-x-auto">
                    <table class="w-full text-left text-xs">
                        <thead>
                            <tr class="text-slate-400 border-b border-slate-800">
                                <th class="pb-2 font-medium">Mevki</th>
                                <th class="pb-2 font-medium">Oyuncu Adı</th>
                                <th class="pb-2 font-medium text-center">Genel (OVR)</th>
                                <th class="pb-2 font-medium text-center">Potansiyel (POT)</th>
                                <th class="pb-2 font-medium text-center">İstikrar / Form</th>
                                <th class="pb-2 font-medium text-center">Son Performans</th>
                            </tr>
                        </thead>
                        <tbody id="squadTableBody" class="divide-y divide-slate-800/60">
                            <!-- JS Injected -->
                        </tbody>
                    </table>
                </div>
            </div>

            <!-- TAB 3: ROLES -->
            <div id="tabContentRoles" class="hidden bg-slate-900 border border-slate-800 rounded-2xl p-6 shadow-xl space-y-6">
                <div class="border-b border-slate-800 pb-4">
                    <h3 class="text-lg font-bold text-white flex items-center gap-2">
                        <i data-lucide="user-check" class="w-5 h-5 text-emerald-400"></i> Oyuncu Görev Atamaları
                    </h3>
                    <p class="text-xs text-slate-400">Maç içindeki kritik anlar için sorumluları belirleyin.</p>
                </div>

                <div class="grid grid-cols-1 sm:grid-cols-2 gap-6">
                    <div class="space-y-2">
                        <label class="block text-xs font-medium text-slate-300">Takım Kaptanı</label>
                        <select id="roleCaptain" class="w-full bg-slate-800 border border-slate-700 rounded-xl px-3 py-2.5 text-sm text-white focus:outline-none focus:ring-2 focus:ring-emerald-500">
                            <!-- JS Injected -->
                        </select>
                    </div>

                    <div class="space-y-2">
                        <label class="block text-xs font-medium text-slate-300">Penaltıcı</label>
                        <select id="rolePenalty" class="w-full bg-slate-800 border border-slate-700 rounded-xl px-3 py-2.5 text-sm text-white focus:outline-none focus:ring-2 focus:ring-emerald-500">
                            <!-- JS Injected -->
                        </select>
                    </div>

                    <div class="space-y-2">
                        <label class="block text-xs font-medium text-slate-300">Serbest Vuruşçu</label>
                        <select id="roleFreeKick" class="w-full bg-slate-800 border border-slate-700 rounded-xl px-3 py-2.5 text-sm text-white focus:outline-none focus:ring-2 focus:ring-emerald-500">
                            <!-- JS Injected -->
                        </select>
                    </div>

                    <div class="space-y-2">
                        <label class="block text-xs font-medium text-slate-300">Kornerci</label>
                        <select id="roleCorner" class="w-full bg-slate-800 border border-slate-700 rounded-xl px-3 py-2.5 text-sm text-white focus:outline-none focus:ring-2 focus:ring-emerald-500">
                            <!-- JS Injected -->
                        </select>
                    </div>
                </div>

                <div class="flex justify-end pt-4">
                    <button onclick="saveRoles()" class="px-5 py-2.5 bg-emerald-500 hover:bg-emerald-600 text-white font-semibold rounded-xl text-xs shadow-lg transition-all flex items-center gap-1.5">
                        <i data-lucide="check" class="w-4 h-4"></i> Görevleri Kaydet
                    </button>
                </div>
            </div>

        </div>
    </main>

    <!-- Modal: Select / Login to Team -->
    <div id="teamSelectModal" class="fixed inset-0 z-50 bg-black/80 backdrop-blur-sm flex items-center justify-center p-4 hidden">
        <div class="bg-slate-900 border border-slate-800 rounded-2xl max-w-lg w-full p-6 shadow-2xl relative">
            <h2 class="text-xl font-bold text-white mb-2">Takımınızı Seçin veya Yönetin</h2>
            <p class="text-xs text-slate-400 mb-6">6 Kişilik Ligde kontrol etmek istediğiniz takıma tıklayın.</p>

            <div id="teamSelectionGrid" class="grid grid-cols-2 gap-3 mb-6">
                <!-- JS Injected Teams Buttons -->
            </div>

            <div class="flex justify-end">
                <button onclick="closeTeamSelectModal()" class="px-4 py-2 bg-slate-800 hover:bg-slate-700 text-slate-300 rounded-xl text-xs font-semibold">Kapat</button>
            </div>
        </div>
    </div>

    <!-- Firebase Standard Imports -->
    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, signInWithCustomToken, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, doc, setDoc, getDoc, onSnapshot } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        // Firebase Global Environment Variables
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'osm-league-app';
        const firebaseConfig = typeof __firebase_config !== 'undefined' ? JSON.parse(__firebase_config) : null;

        let db = null;
        let auth = null;
        let currentUserId = null;

        // Default Initial League Data (6 Teams)
        const DEFAULT_LEAGUE_DATA = {
            matchday: 1,
            teams: [
                {
                    id: 'rm',
                    name: 'Real Madrid',
                    coach: 'Boş',
                    tactics: { style: 'Tiki-Taka', tempo: 'Yüksek', mentality: 70, aggression: 50 },
                    roles: { captain: 'Vinicius Jr', penalty: 'Mbappe', freekick: 'Bellingham', corner: 'Modric' },
                    stats: { played: 0, won: 0, drawn: 0, lost: 0, gf: 0, ga: 0, points: 0 },
                    players: [
                        { name: 'Mbappe', pos: 'SNT', ovr: 91, pot: 95, form: 85, lastRating: 7.5 },
                        { name: 'Vinicius Jr', pos: 'SLK', ovr: 90, pot: 94, form: 88, lastRating: 8.0 },
                        { name: 'Bellingham', pos: 'OS', ovr: 89, pot: 94, form: 82, lastRating: 7.2 },
                        { name: 'Valverde', pos: 'OS', ovr: 88, pot: 91, form: 80, lastRating: 7.0 },
                        { name: 'Courtois', pos: 'KL', ovr: 89, pot: 90, form: 85, lastRating: 7.8 }
                    ]
                },
                {
                    id: 'mci',
                    name: 'Manchester City',
                    coach: 'Boş',
                    tactics: { style: 'Tiki-Taka', tempo: 'Dengeli', mentality: 65, aggression: 40 },
                    roles: { captain: 'De Bruyne', penalty: 'Haaland', freekick: 'De Bruyne', corner: 'Foden' },
                    stats: { played: 0, won: 0, drawn: 0, lost: 0, gf: 0, ga: 0, points: 0 },
                    players: [
                        { name: 'Haaland', pos: 'SNT', ovr: 91, pot: 94, form: 86, lastRating: 7.6 },
                        { name: 'De Bruyne', pos: 'OS', ovr: 90, pot: 90, form: 84, lastRating: 7.4 },
                        { name: 'Foden', pos: 'SĞK', ovr: 88, pot: 92, form: 81, lastRating: 7.1 },
                        { name: 'Rodri', pos: 'MDO', ovr: 90, pot: 92, form: 87, lastRating: 7.9 },
                        { name: 'Ederson', pos: 'KL', ovr: 88, pot: 89, form: 80, lastRating: 6.9 }
                    ]
                },
                {
                    id: 'bay',
                    name: 'Bayern München',
                    coach: 'Boş',
                    tactics: { style: 'LongBall', tempo: 'Yüksek', mentality: 60, aggression: 60 },
                    roles: { captain: 'Neuer', penalty: 'Kane', freekick: 'Kane', corner: 'Kimmich' },
                    stats: { played: 0, won: 0, drawn: 0, lost: 0, gf: 0, ga: 0, points: 0 },
                    players: [
                        { name: 'Kane', pos: 'SNT', ovr: 90, pot: 91, form: 83, lastRating: 7.3 },
                        { name: 'Musiala', pos: 'MO', ovr: 87, pot: 93, form: 85, lastRating: 7.7 },
                        { name: 'Kimmich', pos: 'OS', ovr: 87, pot: 88, form: 82, lastRating: 7.2 },
                        { name: 'Sané', pos: 'SĞK', ovr: 85, pot: 87, form: 78, lastRating: 6.8 },
                        { name: 'Neuer', pos: 'KL', ovr: 86, pot: 86, form: 80, lastRating: 7.0 }
                    ]
                },
                {
                    id: 'bar',
                    name: 'FC Barcelona',
                    coach: 'Boş',
                    tactics: { style: 'Tiki-Taka', tempo: 'Dengeli', mentality: 60, aggression: 45 },
                    roles: { captain: 'ter Stegen', penalty: 'Lewandowski', freekick: 'Raphinha', corner: 'Pedri' },
                    stats: { played: 0, won: 0, drawn: 0, lost: 0, gf: 0, ga: 0, points: 0 },
                    players: [
                        { name: 'Lamine Yamal', pos: 'SĞK', ovr: 86, pot: 96, form: 90, lastRating: 8.2 },
                        { name: 'Lewandowski', pos: 'SNT', ovr: 88, pot: 88, form: 81, lastRating: 7.1 },
                        { name: 'Pedri', pos: 'OS', ovr: 86, pot: 92, form: 84, lastRating: 7.5 },
                        { name: 'Gavi', pos: 'OS', ovr: 84, pot: 90, form: 83, lastRating: 7.4 },
                        { name: 'ter Stegen', pos: 'KL', ovr: 87, pot: 88, form: 79, lastRating: 6.8 }
                    ]
                },
                {
                    id: 'psg',
                    name: 'Paris Saint-Germain',
                    coach: 'Boş',
                    tactics: { style: 'Counter', tempo: 'Yüksek', mentality: 65, aggression: 55 },
                    roles: { captain: 'Marquinhos', penalty: 'Dembele', freekick: 'Hakimi', corner: 'Vitinha' },
                    stats: { played: 0, won: 0, drawn: 0, lost: 0, gf: 0, ga: 0, points: 0 },
                    players: [
                        { name: 'Dembele', pos: 'SĞK', ovr: 86, pot: 88, form: 82, lastRating: 7.2 },
                        { name: 'Hakimi', pos: 'SĞB', ovr: 85, pot: 89, form: 84, lastRating: 7.5 },
                        { name: 'Vitinha', pos: 'OS', ovr: 85, pot: 89, form: 83, lastRating: 7.3 },
                        { name: 'Marquinhos', pos: 'STP', ovr: 86, pot: 87, form: 80, lastRating: 7.0 },
                        { name: 'Donnarumma', pos: 'KL', ovr: 87, pot: 90, form: 81, lastRating: 7.1 }
                    ]
                },
                {
                    id: 'ars',
                    name: 'Arsenal',
                    coach: 'Boş',
                    tactics: { style: 'Tiki-Taka', tempo: 'Dengeli', mentality: 55, aggression: 50 },
                    roles: { captain: 'Odegaard', penalty: 'Saka', freekick: 'Odegaard', corner: 'Rice' },
                    stats: { played: 0, won: 0, drawn: 0, lost: 0, gf: 0, ga: 0, points: 0 },
                    players: [
                        { name: 'Saka', pos: 'SĞK', ovr: 88, pot: 92, form: 86, lastRating: 7.8 },
                        { name: 'Odegaard', pos: 'MO', ovr: 88, pot: 91, form: 84, lastRating: 7.6 },
                        { name: 'Rice', pos: 'MDO', ovr: 87, pot: 90, form: 85, lastRating: 7.7 },
                        { name: 'Saliba', pos: 'STP', ovr: 87, pot: 92, form: 83, lastRating: 7.4 },
                        { name: 'Raya', pos: 'KL', ovr: 84, pot: 86, form: 81, lastRating: 7.0 }
                    ]
                }
            ],
            recentMatches: []
        };

        let leagueData = JSON.parse(JSON.stringify(DEFAULT_LEAGUE_DATA));
        let selectedTeamId = 'rm';

        // Initialize App & Firebase
        async function initApp() {
            if (firebaseConfig) {
                const app = initializeApp(firebaseConfig);
                db = getFirestore(app);
                auth = getAuth(app);

                // Auth Handler
                if (typeof __initial_auth_token !== 'undefined' && __initial_auth_token) {
                    await signInWithCustomToken(auth, __initial_auth_token);
                } else {
                    await signInAnonymously(auth);
                }

                currentUserId = auth.currentUser ? auth.currentUser.uid : 'anon_user';

                // Firestore Realtime Listener
                const docRef = doc(db, 'artifacts', appId, 'public', 'data', 'osm_league_data');
                onSnapshot(docRef, (snapshot) => {
                    if (snapshot.exists()) {
                        leagueData = snapshot.data();
                        updateUI();
                    } else {
                        // First time creation
                        setDoc(docRef, DEFAULT_LEAGUE_DATA);
                    }
                }, (error) => {
                    console.error("Firebase Sync Error:", error);
                });
            } else {
                // Local fallback
                updateUI();
            }
        }

        // Save State to Firebase
        async function syncToFirebase() {
            if (db) {
                const docRef = doc(db, 'artifacts', appId, 'public', 'data', 'osm_league_data');
                await setDoc(docRef, leagueData);
            } else {
                updateUI();
            }
        }

        // UI Renderer
        function updateUI() {
            // Update Matchday Text
            document.getElementById('currentMatchdayText').textContent = `Hafta ${leagueData.matchday} / 10`;

            // Active Team Badge
            const myTeam = leagueData.teams.find(t => t.id === selectedTeamId);
            if (myTeam) {
                document.getElementById('activeTeamName').textContent = `${myTeam.name}`;
                
                // Fill Tactics UI
                document.getElementById('tacticStyle').value = myTeam.tactics.style;
                document.getElementById('tacticTempo').value = myTeam.tactics.tempo;
                document.getElementById('tacticMentality').value = myTeam.tactics.mentality;
                document.getElementById('tacticAggression').value = myTeam.tactics.aggression;
                updateMentalityLabel(myTeam.tactics.mentality);
                updateAggressionLabel(myTeam.tactics.aggression);

                // Fill Squad Table
                renderSquadTable(myTeam);

                // Fill Roles Selects
                renderRolesSelects(myTeam);
            }

            // Standings Table
            renderStandings();

            // Match Logs
            renderMatchLogs();

            // Refresh Icons
            lucide.createIcons();
        }

        function renderStandings() {
            const tbody = document.getElementById('standingsTableBody');
            tbody.innerHTML = '';

            // Sort teams by Points, Goal Difference, Goals For
            const sorted = [...leagueData.teams].sort((a, b) => {
                if (b.stats.points !== a.stats.points) return b.stats.points - a.stats.points;
                const gdA = a.stats.gf - a.stats.ga;
                const gdB = b.stats.gf - b.stats.ga;
                if (gdB !== gdA) return gdB - gdA;
                return b.stats.gf - a.stats.gf;
            });

            sorted.forEach((team, index) => {
                const tr = document.createElement('tr');
                const isMyTeam = team.id === selectedTeamId;
                tr.className = `${isMyTeam ? 'bg-emerald-950/30 font-semibold' : ''} hover:bg-slate-800/40 transition-colors`;
                tr.innerHTML = `
                    <td class="py-2.5 px-1">${index + 1}</td>
                    <td class="py-2.5 px-1 flex items-center gap-1.5">
                        <span class="${isMyTeam ? 'text-emerald-400 font-bold' : 'text-slate-200'}">${team.name}</span>
                    </td>
                    <td class="text-center py-2.5">${team.stats.played}</td>
                    <td class="text-center py-2.5">${team.stats.won}</td>
                    <td class="text-center py-2.5 text-slate-400">${team.stats.gf}/${team.stats.ga}</td>
                    <td class="text-center py-2.5 font-bold text-emerald-400">${team.stats.points}</td>
                `;
                tbody.appendChild(tr);
            });
        }

        function renderSquadTable(team) {
            const tbody = document.getElementById('squadTableBody');
            tbody.innerHTML = '';

            team.players.forEach(p => {
                const tr = document.createElement('tr');
                tr.className = 'hover:bg-slate-800/40 transition-colors';
                
                // Form color badge
                let formBadge = 'bg-emerald-500/20 text-emerald-400';
                if (p.form < 75) formBadge = 'bg-yellow-500/20 text-yellow-400';
                if (p.form < 60) formBadge = 'bg-red-500/20 text-red-400';

                tr.innerHTML = `
                    <td class="py-2.5 font-semibold text-slate-400">${p.pos}</td>
                    <td class="py-2.5 font-bold text-white">${p.name}</td>
                    <td class="text-center py-2.5">
                        <span class="px-2 py-0.5 bg-slate-800 border border-slate-700 rounded font-bold text-emerald-400">${p.ovr}</span>
                    </td>
                    <td class="text-center py-2.5 text-slate-400">${p.pot}</td>
                    <td class="text-center py-2.5">
                        <span class="px-2 py-0.5 rounded text-[10px] font-bold ${formBadge}">${p.form} %</span>
                    </td>
                    <td class="text-center py-2.5 font-medium text-slate-300">${p.lastRating ? p.lastRating.toFixed(1) : '-'}</td>
                `;
                tbody.appendChild(tr);
            });
        }

        function renderRolesSelects(team) {
            const selectIds = ['roleCaptain', 'rolePenalty', 'roleFreeKick', 'roleCorner'];
            const keys = ['captain', 'penalty', 'freekick', 'corner'];

            selectIds.forEach((id, idx) => {
                const select = document.getElementById(id);
                select.innerHTML = '';
                const currentVal = team.roles[keys[idx]];

                team.players.forEach(p => {
                    const opt = document.createElement('option');
                    opt.value = p.name;
                    opt.textContent = `${p.name} (${p.pos} - OVR: ${p.ovr})`;
                    if (p.name === currentVal) opt.selected = true;
                    select.appendChild(opt);
                });
            });
        }

        function renderMatchLogs() {
            const container = document.getElementById('recentMatchesList');
            container.innerHTML = '';

            if (leagueData.recentMatches.length === 0) {
                container.innerHTML = `<p class="text-slate-500 italic text-center py-4">Henüz maç oynanmadı.</p>`;
                return;
            }

            leagueData.recentMatches.slice(-6).reverse().forEach(m => {
                const div = document.createElement('div');
                div.className = 'bg-slate-800/60 border border-slate-700/60 rounded-xl p-2.5 flex items-center justify-between';
                div.innerHTML = `
                    <div class="flex items-center gap-2 flex-1 justify-end font-semibold text-right">
                        <span>${m.home}</span>
                    </div>
                    <div class="px-3 py-1 bg-slate-900 rounded-lg border border-slate-700 font-bold text-emerald-400 text-center min-w-[50px] mx-2">
                        ${m.homeScore} - ${m.awayScore}
                    </div>
                    <div class="flex items-center gap-2 flex-1 font-semibold text-left">
                        <span>${m.away}</span>
                    </div>
                `;
                container.appendChild(div);
            });
        }

        // --- SIMULATION ENGINE & PLAYER DYNAMICS ---
        window.simulateNextMatchday = async function() {
            if (leagueData.matchday > 10) {
                alert("Lig sezonu tamamlandı! Yeni bir sezon başlatmak için Ligi Sıfırlayın.");
                return;
            }

            // Shuffle teams to create 3 fixture pairs
            const teams = [...leagueData.teams];
            const pairs = [
                [teams[0], teams[1]],
                [teams[2], teams[3]],
                [teams[4], teams[5]]
            ];

            let newLogs = [];

            pairs.forEach(([teamA, teamB]) => {
                // Calculate Team Strengths based on Player OVR + Tactic compatibility
                const ovrA = teamA.players.reduce((acc, p) => acc + p.ovr, 0) / teamA.players.length;
                const ovrB = teamB.players.reduce((acc, p) => acc + p.ovr, 0) / teamB.players.length;

                // Mentality & Aggression modifier
                let scoreA = Math.floor(Math.random() * 2) + Math.round((ovrA - 80) * 0.2);
                let scoreB = Math.floor(Math.random() * 2) + Math.round((ovrB - 80) * 0.2);

                if (teamA.tactics.mentality > 65) scoreA += Math.random() > 0.5 ? 1 : 0;
                if (teamB.tactics.mentality > 65) scoreB += Math.random() > 0.5 ? 1 : 0;

                scoreA = Math.max(0, scoreA);
                scoreB = Math.max(0, scoreB);

                // Update Team Stats
                teamA.stats.played += 1;
                teamB.stats.played += 1;
                teamA.stats.gf += scoreA;
                teamA.stats.ga += scoreB;
                teamB.stats.gf += scoreB;
                teamB.stats.ga += scoreA;

                if (scoreA > scoreB) {
                    teamA.stats.won += 1;
                    teamA.stats.points += 3;
                    teamB.stats.lost += 1;
                } else if (scoreB > scoreA) {
                    teamB.stats.won += 1;
                    teamB.stats.points += 3;
                    teamA.stats.lost += 1;
                } else {
                    teamA.stats.drawn += 1;
                    teamB.stats.drawn += 1;
                    teamA.stats.points += 1;
                    teamB.stats.points += 1;
                }

                // Player Performance & Dynamics Progression
                updatePlayerRatingsAndOVR(teamA, scoreA > scoreB ? 1 : (scoreA === scoreB ? 0 : -1));
                updatePlayerRatingsAndOVR(teamB, scoreB > scoreA ? 1 : (scoreA === scoreB ? 0 : -1));

                newLogs.push({
                    home: teamA.name,
                    away: teamB.name,
                    homeScore: scoreA,
                    awayScore: scoreB
                });
            });

            leagueData.matchday += 1;
            leagueData.recentMatches.push(...newLogs);

            await syncToFirebase();
        };

        function updatePlayerRatingsAndOVR(team, resultFactor) {
            // resultFactor: +1 Win, 0 Draw, -1 Loss
            team.players.forEach(p => {
                // Generate Match Rating (e.g. 5.5 to 9.5)
                const baseRating = 6.5 + (resultFactor * 0.8) + ((Math.random() * 2) - 1);
                p.lastRating = Math.min(10, Math.max(5.0, baseRating));

                // Form dynamics
                if (p.lastRating >= 7.5) {
                    p.form = Math.min(100, p.form + Math.floor(Math.random() * 4) + 1);
                } else {
                    p.form = Math.max(40, p.form - Math.floor(Math.random() * 4) - 1);
                }

                // OVR Progression (Potential gap & consistent high performance)
                if (p.lastRating >= 8.0 && p.ovr < p.pot) {
                    if (Math.random() < 0.4) { // 40% chance to upgrade OVR
                        p.ovr += 1;
                    }
                } else if (p.lastRating < 5.5 && p.ovr > 75) {
                    if (Math.random() < 0.2) { // 20% chance to drop OVR on poor form
                        p.ovr -= 1;
                    }
                }
            });
        }

        // TACTICS & ROLES SAVERS
        window.saveTactics = async function() {
            const team = leagueData.teams.find(t => t.id === selectedTeamId);
            if (team) {
                team.tactics.style = document.getElementById('tacticStyle').value;
                team.tactics.tempo = document.getElementById('tacticTempo').value;
                team.tactics.mentality = parseInt(document.getElementById('tacticMentality').value);
                team.tactics.aggression = parseInt(document.getElementById('tacticAggression').value);

                await syncToFirebase();
                alert(`${team.name} taktikleri başarıyla kaydedildi!`);
            }
        };

        window.saveRoles = async function() {
            const team = leagueData.teams.find(t => t.id === selectedTeamId);
            if (team) {
                team.roles.captain = document.getElementById('roleCaptain').value;
                team.roles.penalty = document.getElementById('rolePenalty').value;
                team.roles.freekick = document.getElementById('roleFreeKick').value;
                team.roles.corner = document.getElementById('roleCorner').value;

                await syncToFirebase();
                alert(`${team.name} görevleri güncellendi!`);
            }
        };

        window.resetLeague = async function() {
            if (confirm("Ligi tamamen sıfırlamak istediğinize emin misiniz? Bütün puanlar silinecek.")) {
                leagueData = JSON.parse(JSON.stringify(DEFAULT_LEAGUE_DATA));
                await syncToFirebase();
            }
        };

        // --- UI HELPERS & MODALS ---
        window.updateMentalityLabel = function(val) {
            const el = document.getElementById('mentalityValue');
            if (val < 35) el.textContent = `Savunmacı (%${val})`;
            else if (val > 65) el.textContent = `Hücumcu (%${val})`;
            else el.textContent = `Dengeli (%${val})`;
            updatePitchSummary();
        };

        window.updateAggressionLabel = function(val) {
            const el = document.getElementById('aggressionValue');
            if (val < 35) el.textContent = `Sakin (%${val})`;
            else if (val > 65) el.textContent = `AgresifPres (%${val})`;
            else el.textContent = `Normal (%${val})`;
        };

        function updatePitchSummary() {
            const style = document.getElementById('tacticStyle')?.value || 'Tiki-Taka';
            const mentality = document.getElementById('tacticMentality')?.value || 50;
            document.getElementById('pitchSummaryText').textContent = `4-3-3 Anlayış: %${mentality} (${style})`;
        }

        window.switchTab = function(tabName) {
            document.getElementById('tabContentTactics').classList.add('hidden');
            document.getElementById('tabContentSquad').classList.add('hidden');
            document.getElementById('tabContentRoles').classList.add('hidden');

            document.getElementById('tabBtnTactics').className = 'flex-1 py-2.5 rounded-xl font-semibold text-xs text-slate-400 hover:text-white transition-all flex items-center justify-center gap-2';
            document.getElementById('tabBtnSquad').className = 'flex-1 py-2.5 rounded-xl font-semibold text-xs text-slate-400 hover:text-white transition-all flex items-center justify-center gap-2';
            document.getElementById('tabBtnRoles').className = 'flex-1 py-2.5 rounded-xl font-semibold text-xs text-slate-400 hover:text-white transition-all flex items-center justify-center gap-2';

            if (tabName === 'tactics') {
                document.getElementById('tabContentTactics').classList.remove('hidden');
                document.getElementById('tabBtnTactics').className = 'flex-1 py-2.5 rounded-xl font-semibold text-xs bg-emerald-500 text-white shadow-md flex items-center justify-center gap-2';
            } else if (tabName === 'squad') {
                document.getElementById('tabContentSquad').classList.remove('hidden');
                document.getElementById('tabBtnSquad').className = 'flex-1 py-2.5 rounded-xl font-semibold text-xs bg-emerald-500 text-white shadow-md flex items-center justify-center gap-2';
            } else if (tabName === 'roles') {
                document.getElementById('tabContentRoles').classList.remove('hidden');
                document.getElementById('tabBtnRoles').className = 'flex-1 py-2.5 rounded-xl font-semibold text-xs bg-emerald-500 text-white shadow-md flex items-center justify-center gap-2';
            }
        };

        window.openTeamSelectModal = function() {
            const grid = document.getElementById('teamSelectionGrid');
            grid.innerHTML = '';

            leagueData.teams.forEach(t => {
                const btn = document.createElement('button');
                btn.className = `p-4 rounded-xl border text-left flex flex-col justify-between transition-all ${
                    t.id === selectedTeamId 
                        ? 'bg-emerald-950/60 border-emerald-500/80 text-white ring-2 ring-emerald-500/30' 
                        : 'bg-slate-800/80 border-slate-700 hover:border-slate-600 text-slate-200'
                }`;
                btn.onclick = () => {
                    selectedTeamId = t.id;
                    closeTeamSelectModal();
                    updateUI();
                };
                btn.innerHTML = `
                    <div class="font-bold text-sm mb-1">${t.name}</div>
                    <div class="text-[11px] text-slate-400">Ort. OVR: ${Math.round(t.players.reduce((a,b)=>a+b.ovr,0)/t.players.length)}</div>
                `;
                grid.appendChild(btn);
            });

            document.getElementById('teamSelectModal').classList.remove('hidden');
        };

        window.closeTeamSelectModal = function() {
            document.getElementById('teamSelectModal').classList.add('hidden');
        };

        // Initialize App on Window Load
        window.onload = () => {
            initApp();
        };
    </script>
</body>
</html>
```