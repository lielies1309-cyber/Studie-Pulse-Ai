
!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>StudiePulse AI | Smart Learning</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;500;600;700;800&display=swap');
        
        :root { --primary: #4f46e5; }
        
        body { 
            font-family: 'Plus Jakarta Sans', sans-serif; 
            transition: background 0.3s; 
        }
        
        .dark { background-color: #020617; color: #f8fafc; }

        /* High-Visibility Viewer Content - Fixes invisible text and formatting */
        #viewer-content { color: inherit; }
        #viewer-content h1 { font-size: 2.25rem; font-weight: 800; margin-bottom: 1.5rem; color: #4f46e5; display: block; }
        #viewer-content h2 { font-size: 1.5rem; font-weight: 700; margin-top: 2rem; margin-bottom: 1rem; color: #6366f1; border-bottom: 2px solid #cbd5e1; padding-bottom: 0.5rem; display: block; }
        #viewer-content p { margin-bottom: 1.25rem; line-height: 1.8; font-size: 1.15rem; display: block; color: inherit; }
        #viewer-content ul { margin-bottom: 1.5rem; }
        #viewer-content li { margin-bottom: 0.75rem; list-style-type: disc; margin-left: 1.5rem; color: inherit; line-height: 1.6; }
        #viewer-content b, #viewer-content strong { color: #4f46e5; font-weight: 700; }
        
        .dark #viewer-content h2 { border-color: #334155; }
        .dark #viewer-content b, .dark #viewer-content strong { color: #818cf8; }

        .active-nav { 
            background: var(--primary); 
            color: white !important; 
            box-shadow: 0 10px 20px -5px rgba(79, 70, 229, 0.4); 
        }

        .custom-scrollbar::-webkit-scrollbar { width: 6px; }
        .custom-scrollbar::-webkit-scrollbar-thumb { background: #475569; border-radius: 10px; }

        #setup-modal { backdrop-filter: blur(8px); }

        /* Error Toast */
        #error-toast {
            position: fixed;
            bottom: 2rem;
            right: 2rem;
            background: #ef4444;
            color: white;
            padding: 1rem 1.5rem;
            border-radius: 1rem;
            box-shadow: 0 10px 15px -3px rgba(0,0,0,0.2);
            z-index: 1000;
            display: none;
            animation: slideIn 0.3s ease-out;
        }
        @keyframes slideIn { from { transform: translateX(100%); } to { transform: translateX(0); } }
    </style>
</head>
<body class="bg-slate-50 text-slate-900 dark">

    <div id="app" class="min-h-screen flex flex-col">
        
        <!-- Error Display -->
        <div id="error-toast"></div>

        <!-- Grade Selection Modal (Onboarding) -->
        <div id="setup-modal" class="fixed inset-0 z-[100] bg-slate-950/80 flex items-center justify-center p-6 hidden">
            <div class="max-w-2xl w-full text-center space-y-8 bg-slate-900 p-10 rounded-[2.5rem] border border-slate-800 shadow-2xl">
                <div class="w-20 h-20 bg-indigo-600 rounded-3xl mx-auto flex items-center justify-center shadow-2xl">
                    <i class="fas fa-graduation-cap text-3xl text-white"></i>
                </div>
                <h1 class="text-4xl font-black text-white">Choose Your Level</h1>
                <p class="text-slate-400">Personalized study material based on your exact grade.</p>
                <div class="grid grid-cols-2 sm:grid-cols-3 lg:grid-cols-4 gap-3" id="grade-grid"></div>
            </div>
        </div>

        <!-- Header -->
        <nav class="h-16 border-b border-slate-200 dark:border-slate-800 bg-white/80 dark:bg-slate-950/80 backdrop-blur-md px-6 flex items-center justify-between sticky top-0 z-50">
            <div class="flex items-center gap-3">
                <div class="bg-indigo-600 p-1.5 rounded-xl"><i class="fas fa-bolt text-white text-sm"></i></div>
                <span class="font-black text-xl tracking-tighter text-slate-900 dark:text-white">StudiePulse <span class="text-indigo-500">AI</span></span>
            </div>
            <div class="flex items-center gap-4">
                <button onclick="toggleTheme()" class="p-2.5 rounded-xl border border-slate-200 dark:border-slate-800 text-slate-900 dark:text-white">
                    <i class="fas fa-moon dark:hidden"></i><i class="fas fa-sun hidden dark:block"></i>
                </button>
            </div>
        </nav>

        <div class="flex flex-1 max-w-[1600px] mx-auto w-full relative">
            <!-- Desktop Sidebar -->
            <aside class="hidden lg:flex flex-col w-72 border-r border-slate-200 dark:border-slate-800 p-6 space-y-2 sticky top-16 h-[calc(100vh-64px)]">
                <button onclick="switchView('home')" class="nav-item active-nav flex items-center gap-3 px-4 py-3 rounded-xl font-bold text-sm" data-view="home">
                    <i class="fas fa-history w-5"></i> Library
                </button>
                <button onclick="switchView('create')" class="nav-item flex items-center gap-3 px-4 py-3 rounded-xl font-bold text-sm hover:bg-slate-100 dark:hover:bg-slate-900 transition-all" data-view="create">
                    <i class="fas fa-plus-circle w-5"></i> New Session
                </button>
                <div class="pt-8 pb-2 px-4 text-[10px] font-black opacity-30 uppercase tracking-widest">Settings</div>
                <button onclick="switchView('profile')" class="nav-item flex items-center gap-3 px-4 py-3 rounded-xl font-bold text-sm hover:bg-slate-100 dark:hover:bg-slate-900 transition-all" data-view="profile">
                    <i class="fas fa-user-circle w-5"></i> My Profile
                </button>

                <div class="mt-auto p-5 rounded-3xl bg-indigo-600/5 border border-indigo-500/10">
                    <p class="text-[10px] font-black text-indigo-500 uppercase tracking-widest mb-1">Status</p>
                    <p id="grade-display-sidebar" class="font-bold text-sm">Detecting...</p>
                </div>
            </aside>

            <!-- Main Screens -->
            <main class="flex-1 p-6 lg:p-10 pb-28 min-w-0">
                
                <!-- History Library -->
                <div id="view-home" class="view space-y-8">
                    <div>
                        <h2 class="text-4xl font-black">My Library</h2>
                        <p class="opacity-50">Saved summaries and tests</p>
                    </div>
                    <div id="history-grid" class="grid grid-cols-1 md:grid-cols-2 xl:grid-cols-3 gap-6"></div>
                </div>

                <!-- Create Section -->
                <div id="view-create" class="view hidden space-y-8 max-w-5xl mx-auto">
                    <div>
                        <h2 class="text-4xl font-black">New Session</h2>
                        <p class="opacity-50">Create study material in Afrikaans or English</p>
                    </div>
                    <div class="bg-white dark:bg-slate-900 border border-slate-200 dark:border-slate-800 rounded-[2rem] p-8 shadow-sm space-y-6">
                        <textarea id="input-text" placeholder="Paste your notes or a prompt here..." class="w-full h-64 bg-transparent outline-none resize-none text-lg placeholder:opacity-30 text-slate-900 dark:text-white p-2 font-medium"></textarea>
                        
                        <div class="flex flex-wrap gap-4" id="image-previews">
                            <button onclick="document.getElementById('file-input').click()" class="w-20 h-20 rounded-2xl border-2 border-dashed border-slate-700 flex flex-col items-center justify-center gap-1 text-slate-500 hover:text-indigo-500 hover:border-indigo-500 transition-all">
                                <i class="fas fa-camera text-xl"></i><span class="text-[9px] font-black uppercase">Add Photo</span>
                            </button>
                            <input type="file" id="file-input" class="hidden" multiple accept="image/*">
                        </div>

                        <div class="grid grid-cols-2 gap-4">
                            <div class="flex flex-col gap-2">
                                <label class="text-[10px] font-black uppercase opacity-40 px-1">Tool Type</label>
                                <select id="tool-type" class="p-4 rounded-2xl bg-slate-100 dark:bg-slate-950 border border-slate-200 dark:border-slate-800 font-bold text-slate-900 dark:text-white outline-none cursor-pointer">
                                    <option value="summary">Summary</option>
                                    <option value="test">Practice Test</option>
                                    <option value="notes">Detailed Notes</option>
                                    <option value="flashcards">Flashcards</option>
                                </select>
                            </div>
                            <div class="flex flex-col gap-2">
                                <label class="text-[10px] font-black uppercase opacity-40 px-1">Difficulty</label>
                                <select id="difficulty" class="p-4 rounded-2xl bg-slate-100 dark:bg-slate-950 border border-slate-200 dark:border-slate-800 font-bold text-slate-900 dark:text-white outline-none cursor-pointer">
                                    <option value="Easy">Easy</option>
                                    <option value="Medium" selected>Medium</option>
                                    <option value="Hard">Hard</option>
                                </select>
                            </div>
                        </div>
                        <button id="generate-btn" class="w-full bg-indigo-600 hover:bg-indigo-500 py-6 rounded-[2.5rem] font-black text-white text-xl shadow-xl transition-all active:scale-95">
                            GENERATE NOW
                        </button>
                    </div>
                </div>

                <!-- Viewer -->
                <div id="view-viewer" class="view hidden space-y-8 max-w-4xl mx-auto">
                    <button onclick="switchView('home')" class="font-bold opacity-50 hover:opacity-100 text-slate-900 dark:text-white transition-opacity"><i class="fas fa-chevron-left mr-2"></i> Library</button>
                    <div class="bg-white dark:bg-slate-900 border border-slate-200 dark:border-slate-800 rounded-[3rem] p-10 lg:p-16 shadow-2xl min-h-[400px]">
                        <div id="viewer-content" class="text-slate-900 dark:text-slate-100">
                            <!-- AI Content -->
                        </div>
                    </div>
                </div>

                <!-- Profile -->
                <div id="view-profile" class="view hidden max-w-xl mx-auto space-y-8">
                    <h2 class="text-4xl font-black text-center">My Profile</h2>
                    <div class="bg-white dark:bg-slate-900 border border-slate-200 dark:border-slate-800 rounded-[2rem] p-8 space-y-6">
                        <div class="flex items-center gap-4 pb-6 border-b dark:border-slate-800">
                            <div class="w-16 h-16 bg-indigo-600 rounded-full flex items-center justify-center text-white text-2xl"><i class="fas fa-user-graduate"></i></div>
                            <div>
                                <h3 id="profile-grade-display" class="text-xl font-bold">Grade --</h3>
                                <p class="text-xs font-black uppercase text-indigo-500 tracking-widest">Active Student</p>
                            </div>
                        </div>
                        <button onclick="showSetup()" class="w-full py-4 rounded-2xl border-2 border-indigo-600/20 text-indigo-500 font-bold hover:bg-indigo-600/5 transition-all">Change Grade Level</button>
                        <button onclick="signOutApp()" class="w-full py-4 rounded-2xl bg-red-500/5 text-red-500 font-bold hover:bg-red-500/10 transition-all">Sign Out</button>
                    </div>
                </div>

            </main>
        </div>

        <!-- Mobile Bar -->
        <div class="lg:hidden fixed bottom-0 left-0 right-0 h-20 bg-white dark:bg-slate-950 border-t border-slate-200 dark:border-slate-800 flex items-center justify-around z-40 px-4">
            <button onclick="switchView('home')" class="flex flex-col items-center gap-1 text-slate-400" data-view="home"><i class="fas fa-history text-lg"></i><span class="text-[9px] font-bold">Library</span></button>
            <button onclick="switchView('create')" class="bg-indigo-600 w-14 h-14 rounded-2xl -mt-10 flex items-center justify-center text-white shadow-2xl"><i class="fas fa-plus text-xl"></i></button>
            <button onclick="switchView('profile')" class="flex flex-col items-center gap-1 text-slate-400" data-view="profile"><i class="fas fa-user text-lg"></i><span class="text-[9px] font-bold">Profile</span></button>
        </div>

        <!-- Loader Overlay -->
        <div id="loader" class="fixed inset-0 bg-slate-950/90 z-[200] flex items-center justify-center flex-col gap-6 hidden">
            <div class="w-16 h-16 border-4 border-indigo-500 border-t-transparent rounded-full animate-spin"></div>
            <p class="text-indigo-500 font-black uppercase tracking-widest text-xs">Generating Words...</p>
        </div>
    </div>

    <!-- Firebase SDK Logic -->
    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, onAuthStateChanged, signOut } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, collection, doc, setDoc, getDoc, addDoc, onSnapshot, serverTimestamp, updateDoc } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        // Constants
        const GRADES = ["Grade 1", "Grade 2", "Grade 3", "Grade 4", "Grade 5", "Grade 6", "Grade 7", "Grade 8", "Grade 9", "Grade 10", "Grade 11", "Grade 12", "University"];
        
        let config = {};
        try {
            config = JSON.parse(typeof __firebase_config !== 'undefined' ? __firebase_config : '{}');
        } catch(e) {
            console.warn("Firebase config needs to be manually set for a live site.");
        }

        const app = config.apiKey ? initializeApp(config) : null;
        const auth = app ? getAuth(app) : null;
        const db = app ? getFirestore(app) : null;
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'studiepulse-app';

        let user = null;
        let profile = { grade: localStorage.getItem('sp_grade') || '', isPro: false };
        let history = JSON.parse(localStorage.getItem('sp_history') || '[]');
        let images = [];
        const apiKey = ""; // Set your Gemini API key here for live hosting

        // Toast Helper
        const showError = (msg) => {
            const toast = document.getElementById('error-toast');
            toast.innerText = msg;
            toast.style.display = 'block';
            setTimeout(() => toast.style.display = 'none', 5000);
        };

        // --- Core Startup ---
        window.onload = () => {
            if (auth) {
                onAuthStateChanged(auth, async (u) => {
                    if (u) {
                        user = u;
                        await syncProfile();
                        loadHistory();
                    } else {
                        signInAnonymously(auth);
                    }
                });
            } else {
                // If no Firebase, we still allow UI interaction via localStorage
                updateUIPrefs();
                renderHistoryUI();
                if (!profile.grade) showSetup();
            }
        };

        async function syncProfile() {
            if (!db || !user) return;
            const pRef = doc(db, 'artifacts', appId, 'users', user.uid, 'profile', 'settings');
            const snap = await getDoc(pRef);
            if (snap.exists()) {
                profile = snap.data();
                localStorage.setItem('sp_grade', profile.grade);
                if (!profile.grade) showSetup();
                else updateUIPrefs();
            } else {
                profile = { grade: localStorage.getItem('sp_grade') || '', isPro: false };
                await setDoc(pRef, profile);
                if (!profile.grade) showSetup();
            }
        }

        window.showSetup = () => {
            const grid = document.getElementById('grade-grid');
            grid.innerHTML = GRADES.map(g => `<button onclick="saveGrade('${g}')" class="p-4 rounded-2xl border-2 border-slate-700 bg-slate-900 font-bold text-white hover:border-indigo-500 transition-all">${g}</button>`).join('');
            document.getElementById('setup-modal').classList.remove('hidden');
        };

        window.saveGrade = async (g) => {
            profile.grade = g;
            localStorage.setItem('sp_grade', g);
            if (db && user) {
                await updateDoc(doc(db, 'artifacts', appId, 'users', user.uid, 'profile', 'settings'), { grade: g });
            }
            document.getElementById('setup-modal').classList.add('hidden');
            updateUIPrefs();
        };

        function updateUIPrefs() {
            const gradeElements = document.querySelectorAll('#grade-display-sidebar, #profile-grade-display');
            gradeElements.forEach(el => el.innerText = profile.grade || 'Not Set');
        }

        window.switchView = (view) => {
            document.querySelectorAll('.view').forEach(v => v.classList.add('hidden'));
            const target = document.getElementById(`view-${view}`);
            if (target) target.classList.remove('hidden');
            
            document.querySelectorAll('.nav-item').forEach(n => {
                if (n.dataset.view === view) n.classList.add('active-nav');
                else n.classList.remove('active-nav');
            });
            window.scrollTo(0,0);
        };

        window.toggleTheme = () => document.body.classList.toggle('dark');

        function loadHistory() {
            if (!db || !user) {
                renderHistoryUI();
                return;
            }
            const colRef = collection(db, 'artifacts', appId, 'users', user.uid, 'history');
            onSnapshot(colRef, (snap) => {
                history = snap.docs.map(d => ({ id: d.id, ...d.data() }));
                localStorage.setItem('sp_history', JSON.stringify(history));
                renderHistoryUI();
            });
        }

        function renderHistoryUI() {
            const grid = document.getElementById('history-grid');
            grid.innerHTML = history.length ? history.map(item => {
                return `<div onclick="openItemById('${item.id || item.timestamp}')" class="bg-white dark:bg-slate-900 p-6 rounded-[2rem] border border-slate-200 dark:border-slate-800 cursor-pointer hover:border-indigo-500/50 transition-all">
                    <div class="p-3 bg-indigo-500/10 text-indigo-500 w-fit rounded-2xl mb-4"><i class="fas fa-file-alt"></i></div>
                    <h4 class="font-bold line-clamp-1">${item.title}</h4>
                    <p class="text-[10px] font-black uppercase text-indigo-500 mt-2">${item.type} • ${item.grade}</p>
                </div>`;
            }).join('') : `<div class="col-span-full py-20 text-center opacity-30"><p>Your Library is empty. Click 'New Session' to start.</p></div>`;
        }

        window.openItemById = (id) => {
            const item = history.find(h => (h.id === id || h.timestamp?.toString() === id));
            if (!item) return;
            switchView('viewer');
            
            const viewerContent = document.getElementById('viewer-content');
            const html = item.content.split('\n').map(line => {
                let text = line.trim();
                if (text.startsWith('# ')) return `<h1>${text.replace('# ', '')}</h1>`;
                if (text.startsWith('## ')) return `<h2>${text.replace('## ', '')}</h2>`;
                if (text.startsWith('- ')) return `<li>${text.replace('- ', '')}</li>`;
                if (text === '') return '<div class="h-4"></div>';
                text = text.replace(/\*\*(.*?)\*\*/g, '<strong>$1</strong>');
                return `<p>${text}</p>`;
            }).join('');
            viewerContent.innerHTML = html;
        };

        document.getElementById('generate-btn').onclick = async () => {
            const textIn = document.getElementById('input-text').value;
            const tool = document.getElementById('tool-type').value;
            const diff = document.getElementById('difficulty').value;
            
            if(!textIn && images.length === 0) {
                showError("Asseblief voer notas of 'n opdrag in.");
                return;
            }
            
            document.getElementById('loader').classList.remove('hidden');
            
            try {
                // Ensure Gemini Key is set or warn
                if (!apiKey && typeof __firebase_config === 'undefined') {
                    throw new Error("AI Key missing. Please set your apiKey in the script for live hosting.");
                }

                const response = await fetch(`https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-09-2025:generateContent?key=${apiKey}`, {
                    method: 'POST',
                    headers: {'Content-Type': 'application/json'},
                    body: JSON.stringify({
                        contents: [{
                            role: "user", 
                            parts: [
                                {text: `Education Level: ${profile.grade}. Task: Create a high-quality ${tool}. Difficulty: ${diff}. Subject Input: ${textIn}. Instructions: Use student-friendly language for ${profile.grade}. Detect if input is in Afrikaans or English and reply in that language. ALWAYS use clear headings and descriptive text.`},
                                ...images.map(img => ({inlineData: {mimeType: "image/png", data: img.split(',')[1]}}))
                            ]
                        }],
                        systemInstruction: {parts: [{text: "You are StudiePulse AI. Start with a Title using #. Use **bold** for key terms."}]}
                    })
                });
                
                const data = await response.json();
                if (!data.candidates || !data.candidates[0].content) throw new Error("AI engine failed. Try a simpler prompt.");

                const aiResponse = data.candidates[0].content.parts[0].text;
                const aiTitle = aiResponse.split('\n')[0].replace('#', '').trim() || 'New Study Session';
                
                const newItem = {
                    title: aiTitle,
                    content: aiResponse,
                    type: tool,
                    grade: profile.grade,
                    timestamp: Date.now()
                };

                if (db && user) {
                    await addDoc(collection(db, 'artifacts', appId, 'users', user.uid, 'history'), {
                        ...newItem,
                        createdAt: serverTimestamp()
                    });
                } else {
                    // Local fallback
                    history.push(newItem);
                    localStorage.setItem('sp_history', JSON.stringify(history));
                    renderHistoryUI();
                }
                
                document.getElementById('input-text').value = '';
                images = [];
                document.getElementById('image-previews').querySelectorAll('div').forEach(el => el.remove());
                switchView('home');
            } catch (e) {
                showError(e.message || "Fout met AI konneksie.");
            } finally {
                document.getElementById('loader').classList.add('hidden');
            }
        };

        document.getElementById('file-input').onchange = (e) => {
            Array.from(e.target.files).forEach(f => {
                const r = new FileReader();
                r.onload = (ev) => {
                    images.push(ev.target.result);
                    const div = document.createElement('div');
                    div.className = "w-20 h-20 rounded-2xl overflow-hidden border border-slate-700 relative";
                    div.innerHTML = `<img src="${ev.target.result}" class="w-full h-full object-cover">`;
                    document.getElementById('image-previews').prepend(div);
                };
                r.readAsDataURL(f);
            });
        };
        
        // Export to Global for HTML onclick events
        window.switchView = switchView;
        window.toggleTheme = toggleTheme;
        window.saveGrade = saveGrade;
        window.showSetup = showSetup;
        window.openItemById = openItemById;
        window.signOutApp = () => {
            localStorage.clear();
            if (auth) signOut(auth).then(() => location.reload());
            else location.reload();
        };
    </script>
</body>
</html>
