<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ARATS - Ultimate Tracker System</title>
    
    <script src="https://cdn.tailwindcss.com"></script>
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    fontFamily: { sans: ['Inter', 'sans-serif'] },
                    animation: { 'blob': 'blob 7s infinite', 'fade-in': 'fadeIn 0.3s ease-out' },
                    keyframes: {
                        blob: {
                            '0%': { transform: 'translate(0px, 0px) scale(1)' },
                            '33%': { transform: 'translate(30px, -50px) scale(1.1)' },
                            '66%': { transform: 'translate(-20px, 20px) scale(0.9)' },
                            '100%': { transform: 'translate(0px, 0px) scale(1)' },
                        },
                        fadeIn: {
                            '0%': { opacity: '0', transform: 'translateY(10px)' },
                            '100%': { opacity: '1', transform: 'translateY(0)' },
                        }
                    },
                    colors: {
                        brand: { 50:'#eff6ff', 100:'#dbeafe', 500:'#3b82f6', 600:'#2563eb', 900:'#1e3a8a' },
                        aryan: { 50:'#fdf2f8', 100: '#fce7f3', 500:'#ec4899', 600:'#db2777' }, 
                        umang: { 50:'#f0fdf4', 100: '#dcfce7', 500:'#22c55e', 600:'#16a34a' },
                        gold:  { 50:'#fffbeb', 100: '#fef3c7', 500:'#f59e0b', 600:'#d97706' }
                    }
                }
            }
        }
    </script>

    <script src="https://unpkg.com/react@18/umd/react.development.js" crossorigin></script>
    <script src="https://unpkg.com/react-dom@18/umd/react-dom.development.js" crossorigin></script>
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>

    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf-autotable/3.5.25/jspdf.plugin.autotable.min.js"></script>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/9.22.0/firebase-app.js";
        // Your web app's Firebase configuration
        const firebaseConfig = {
            apiKey: "AIzaSyDcJRUbGfe7cT3Dm75awHq_qAs7RPHRM8",
            authDomain: "arats-tracker.firebaseapp.com",
            projectId: "arats-tracker",
            storageBucket: "arats-tracker.firebasestorage.app",
            messagingSenderId: "1057021167344",
            appId: "1:1057021167344:web:309df21a8d860ceb6fa435"
        };
        // Initialize Firebase
        const app = initializeApp(firebaseConfig);
        console.log("Firebase Integrated Successfully");
    </script>

    <style>
        body { background-color: #f8fafc; -webkit-tap-highlight-color: transparent; }
        .glass { background: rgba(255,255,255,0.95); backdrop-filter: blur(10px); border: 1px solid #e2e8f0; }
        .glass-dark { background: rgba(15, 23, 42, 0.95); backdrop-filter: blur(10px); border: 1px solid #334155; color: white; }
        ::-webkit-scrollbar { width: 5px; height: 5px; }
        ::-webkit-scrollbar-thumb { background: #cbd5e1; border-radius: 10px; }
        .tap-effect:active { transform: scale(0.98); transition: transform 0.1s; }
        .animate-enter { animation: fadeIn 0.4s ease-out forwards; }
    </style>
</head>
<body>
    <div id="root"></div>

    <script type="text/babel">

    // --- 1. ICONS ---
    const Icons = {
        User: () => <svg className="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M16 7a4 4 0 11-8 0 4 4 0 018 0zM12 14a7 7 0 00-7 7h14a7 7 0 00-7-7z" /></svg>,
        Home: () => <svg className="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M3 12l2-2m0 0l7-7 7 7M5 10v10a1 1 0 001 1h3m10-11l2 2m-2-2v10a1 1 0 01-1 1h-3m-6 0a1 1 0 001-1v-4a1 1 0 011-1h2a1 1 0 011 1v4a1 1 0 001 1m-6 0h6" /></svg>,
        Back: () => <svg className="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M10 19l-7-7m0 0l7-7m-7 7h18" /></svg>,
        Save: () => <svg className="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M8 7H5a2 2 0 00-2 2v9a2 2 0 002 2h14a2 2 0 002-2V9a2 2 0 00-2-2h-3m-1 4l-3 3m0 0l-3-3m3 3V4" /></svg>,
        Logout: () => <svg className="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M17 16l4-4m0 0l-4-4m4 4H7m6 4v1a3 3 0 01-3 3H6a3 3 0 01-3-3V7a3 3 0 013-3h4a3 3 0 013 3v1" /></svg>,
        Money: () => <svg className="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M12 8c-1.657 0-3 .895-3 2s1.343 2 3 2 3 .895 3 2-1.343 2-3 2m0-8c1.11 0 2.08.402 2.599 1M12 8V7m0 1v8m0 0v1m0-1c-1.11 0-2.08-.402-2.599-1M21 12a9 9 0 11-18 0 9 9 0 0118 0z" /></svg>,
        Moral: () => <svg className="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z" /></svg>,
        Pdf: () => <svg className="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M7 21h10a2 2 0 002-2V9.414a1 1 0 00-.293-.707l-5.414-5.414A1 1 0 0012.586 3H7a2 2 0 00-2 2v14a2 2 0 002 2z" /></svg>
    };

    // --- 2. SYLLABUS SCHEMA ---
    const SYLLABUS = [
        { id: 'exercise', title: '1. Exercise', weight: 5, type: 'list', items: ['5 km Running', '10 Min Full body Exercise', 'Yoga', 'Meditation', 'Pushups', 'Jumping', 'Hanging'] },
        { id: 'cap_sip', title: '2. CAP + SIP', weight: 10, type: 'sections', sections: { 'Section CAP': ['CAP Class', 'Yt class', 'Yt session'], 'Section SIP': ['Book Reading', 'Classes', 'Notes Reading'] } },
        { id: 'parcham', title: '3. Parcham', weight: 10, type: 'list', items: ['Subject A (Science)', 'Subject B (Economic)'] },
        { id: 'ncert', title: '4. NCERT', weight: 10, type: 'list', items: ['Class 8th Geography', 'Class 7th Science'] },
        { id: 'p2i', title: '5. P2I', weight: 10, type: 'list', items: ['Polity class', 'Polity book', 'Polity Notes'] },
        { id: 'math', title: '6. Mathematics', weight: 6, type: 'single', label: 'Daily Practice' },
        { id: 'reasoning', title: '7. Reasoning', weight: 6, type: 'single', label: 'Daily Practice' },
        { id: 'bsc_math', title: '8. B.Sc. Math', weight: 8, type: 'single', label: 'Study Session' },
        { id: 'obj_res', title: '9. Objective Resolution', weight: 21, type: 'sections', sections: { 'Section A (Primary)': ['Tell me Why', 'Library Book', 'Math formula', 'Language', 'Math book', 'Bihar facts', 'Hindi', 'English', 'Static GK', 'NCERT Notes', 'Math Overview'], 'Section B (Main)': ['Science Reporter', 'ATLAS', 'Maps', 'Reasoning', 'Math', 'Bihar CAP', 'Kurukshetra', 'Lucent\'s', 'CSE Book', 'Geography PYQ', 'Bihar CAP English', 'Polity PYQ'] } },
        { id: 'answer', title: '10. Answer Writing', weight: 10, type: 'single', label: 'Daily Writing' },
        { id: 'extra', title: '11. Extracurricular', weight: 4, type: 'list', items: ['Telegram Course', 'PSIR Optional', 'English Communication', 'Typing Practice'] }
    ];

    // --- 3. STORAGE & LOGIC ENGINE ---
    const DB = {
        KEY_ARATS: 'ARATS_PERMANENT_DATA_V1',
        KEY_SESSION: 'ARATS_ACTIVE_USER',
        KEY_FIN_BUDGET: 'FIN_MONTHLY_BUDGET_V1',
        KEY_FIN_TXN: 'FIN_MONEY_MGMT_V1',
        KEY_ACCOUNTS: 'FIN_ACCOUNTS_V1',
        KEY_MORAL: 'MORAL_ETHICS_V1',

        // DATE FIX: Use local time instead of UTC to prevent "One Day Ahead" bug
        getToday: () => {
            const d = new Date();
            const offset = d.getTimezoneOffset() * 60000;
            return new Date(d.getTime() - offset).toISOString().split('T')[0];
        },

        // --- ARATS CORE METHODS ---
        getAllData: () => JSON.parse(localStorage.getItem(DB.KEY_ARATS) || '{}'),
        saveActivity: (userId, date, activityData, details) => {
            const allData = DB.getAllData();
            const key = `${userId}_${date}`;
            allData[key] = { 
                activities: activityData,
                details: details || (allData[key]?.details || {}), // Save topic details 
                percentage: DB.calcWeightedPercent(activityData),
                lastUpdated: new Date().toISOString() 
            };
            localStorage.setItem(DB.KEY_ARATS, JSON.stringify(allData));
        },
        getActivity: (userId, date) => DB.getAllData()[`${userId}_${date}`] || null,
        
        login: (userId, name, theme) => {
            const user = { id: userId, name, theme };
            localStorage.setItem(DB.KEY_SESSION, JSON.stringify(user));
            return user;
        },
        getSession: () => JSON.parse(localStorage.getItem(DB.KEY_SESSION)),
        logout: () => localStorage.removeItem(DB.KEY_SESSION),

        calcWeightedPercent: (formData) => {
            if (!formData) return 0;
            let totalScore = 0;
            SYLLABUS.forEach(topic => {
                let topicCompletion = 0;
                if (topic.type === 'single') {
                    if (formData[topic.id]) topicCompletion = 1;
                } else if (topic.type === 'list') {
                    const totalItems = topic.items.length;
                    let checkedCount = 0;
                    topic.items.forEach(i => { if (formData[topic.id]?.[i]) checkedCount++; });
                    topicCompletion = totalItems > 0 ? (checkedCount / totalItems) : 0;
                } else if (topic.type === 'sections') {
                    let totalItems = 0, checkedCount = 0;
                    Object.keys(topic.sections).forEach(sec => {
                        topic.sections[sec].forEach(i => {
                            totalItems++;
                            if (formData[topic.id]?.[sec]?.[i]) checkedCount++;
                        });
                    });
                    topicCompletion = totalItems > 0 ? (checkedCount / totalItems) : 0;
                }
                totalScore += (topicCompletion * topic.weight);
            });
            return Math.round(totalScore);
        },

        // --- FINANCIAL MODULE METHODS ---
        saveBudget: (monthKey, data) => {
            const store = JSON.parse(localStorage.getItem(DB.KEY_FIN_BUDGET) || '{}');
            store[monthKey] = data;
            localStorage.setItem(DB.KEY_FIN_BUDGET, JSON.stringify(store));
        },
        getBudget: (monthKey) => JSON.parse(localStorage.getItem(DB.KEY_FIN_BUDGET) || '{}')[monthKey] || null,

        addTransaction: (txn) => {
            const list = JSON.parse(localStorage.getItem(DB.KEY_FIN_TXN) || '[]');
            list.unshift(txn);
            localStorage.setItem(DB.KEY_FIN_TXN, JSON.stringify(list));
        },
        getTransactions: () => JSON.parse(localStorage.getItem(DB.KEY_FIN_TXN) || '[]'),

        // Bank Accounts (SBI, BOB, UBG)
        updateAccount: (bankId, amount, type, desc) => {
            const accounts = JSON.parse(localStorage.getItem(DB.KEY_ACCOUNTS) || '{ "SBI": {bal:0, txns:[]}, "BOB": {bal:0, txns:[]}, "UBG": {bal:0, txns:[]} }');
            if(!accounts[bankId]) accounts[bankId] = {bal:0, txns:[]};
            
            const numAmount = parseFloat(amount);
            if(type === 'credit') accounts[bankId].bal += numAmount;
            else accounts[bankId].bal -= numAmount;

            accounts[bankId].txns.unshift({ 
                date: DB.getToday(), type, amount: numAmount, desc, 
                runningBal: accounts[bankId].bal 
            });
            localStorage.setItem(DB.KEY_ACCOUNTS, JSON.stringify(accounts));
        },
        getAccount: (bankId) => {
            const accounts = JSON.parse(localStorage.getItem(DB.KEY_ACCOUNTS) || '{ "SBI": {bal:0, txns:[]}, "BOB": {bal:0, txns:[]}, "UBG": {bal:0, txns:[]} }');
            return accounts[bankId] || {bal:0, txns:[]};
        },

        // --- MORAL MODULE METHODS ---
        saveMoral: (date, data) => {
            const store = JSON.parse(localStorage.getItem(DB.KEY_MORAL) || '{}');
            store[date] = data;
            localStorage.setItem(DB.KEY_MORAL, JSON.stringify(store));
        },
        getMoral: (date) => JSON.parse(localStorage.getItem(DB.KEY_MORAL) || '{}')[date] || null,
        getAllMoral: () => JSON.parse(localStorage.getItem(DB.KEY_MORAL) || '{}')
    };

    // --- 4. REACT CONTEXT ---
    const { useState, useEffect, useContext, createContext, useRef } = React;
    const AppContext = createContext();

    const AppProvider = ({ children }) => {
        const [user, setUser] = useState(null);
        const [view, setView] = useState('entry'); 
        const [currentDate, setCurrentDate] = useState(DB.getToday());
        const [trigger, setTrigger] = useState(0);

        useEffect(() => {
            const session = DB.getSession();
            if(session) { setUser(session); setView('dashboard'); }
        }, []);

        const enterApp = (u) => { setUser(u); setView('dashboard'); };
        const exitApp = () => { DB.logout(); setUser(null); setView('entry'); };
        const refresh = () => setTrigger(t => t + 1);

        return (
            <AppContext.Provider value={{ user, view, setView, currentDate, setCurrentDate, enterApp, exitApp, refresh, trigger }}>
                {children}
            </AppContext.Provider>
        );
    };
    // --- 5. ENTRY SCREEN (UPDATED) ---
    const EntryScreen = () => {
        const { enterApp, setView } = useContext(AppContext);

        const handleLogin = (id, name, theme) => {
            const user = DB.login(id, name, theme);
            enterApp(user);
        };

        return (
            <div className="min-h-screen flex flex-col items-center justify-center p-6 relative overflow-hidden bg-slate-50">
                {/* Background Animation */}
                <div className="absolute top-[-10%] left-[-10%] w-72 h-72 bg-blue-300 rounded-full mix-blend-multiply filter blur-3xl opacity-30 animate-blob"></div>
                <div className="absolute bottom-[-10%] right-[-10%] w-72 h-72 bg-pink-300 rounded-full mix-blend-multiply filter blur-3xl opacity-30 animate-blob animation-delay-2000"></div>

                <div className="glass w-full max-w-md p-8 rounded-3xl shadow-2xl relative z-10 text-center animate-enter">
                    <h1 className="text-4xl font-extrabold text-slate-800 tracking-tight mb-2">ARATS</h1>
                    <p className="text-slate-500 font-medium mb-8">Personal Growth System</p>

                    <div className="space-y-4">
                        {/* Aryan Button */}
                        <button onClick={() => handleLogin('aryan', 'Aryan Rajput', 'aryan')}
                            className="w-full group relative overflow-hidden bg-white p-4 rounded-2xl shadow-md border hover:border-pink-200 transition-all tap-effect">
                            <div className="absolute inset-0 bg-gradient-to-r from-pink-50 to-white opacity-0 group-hover:opacity-100 transition-opacity"></div>
                            <div className="relative flex items-center gap-4">
                                <div className="w-12 h-12 rounded-full bg-pink-100 text-2xl flex items-center justify-center">👨‍💻</div>
                                <div className="text-left">
                                    <h3 className="font-bold text-slate-800 text-lg">Aryan Rajput</h3>
                                    <p className="text-xs text-pink-500 font-bold">Admin / Developer</p>
                                </div>
                            </div>
                        </button>

                        {/* Umang Button */}
                        <button onClick={() => handleLogin('umang', 'Umang Kumar', 'umang')}
                            className="w-full group relative overflow-hidden bg-white p-4 rounded-2xl shadow-md border hover:border-green-200 transition-all tap-effect">
                            <div className="absolute inset-0 bg-gradient-to-r from-green-50 to-white opacity-0 group-hover:opacity-100 transition-opacity"></div>
                            <div className="relative flex items-center gap-4">
                                <div className="w-12 h-12 rounded-full bg-green-100 text-2xl flex items-center justify-center">🦁</div>
                                <div className="text-left">
                                    <h3 className="font-bold text-slate-800 text-lg">Umang Kumar</h3>
                                    <p className="text-xs text-green-600 font-bold">User Account</p>
                                </div>
                            </div>
                        </button>

                        {/* Divider */}
                        <div className="relative flex py-2 items-center"><div className="flex-grow border-t border-slate-200"></div><span className="flex-shrink-0 mx-4 text-slate-400 text-xs">MODULES</span><div className="flex-grow border-t border-slate-200"></div></div>

                        {/* NEW FINANCIAL & MORAL BUTTON */}
                        <button onClick={() => setView('fin_moral_home')}
                            className="w-full py-3 bg-slate-800 text-white rounded-xl shadow-lg hover:bg-slate-900 transition flex items-center justify-center gap-2 tap-effect">
                            <Icons.Money />
                            <span className="font-bold text-sm">Financial & Moral Panel</span>
                        </button>
                    </div>
                </div>
            </div>
        );
    };

    // --- 6. DASHBOARD ---
    const Dashboard = () => {
        const { user, exitApp, setView, setCurrentDate, trigger } = useContext(AppContext);
        const [stats, setStats] = useState({ today: 0 });

        useEffect(() => {
            const todayStr = DB.getToday();
            const entry = DB.getActivity(user.id, todayStr);
            setStats({ today: entry ? entry.percentage : 0 });
        }, [trigger]);

        const theme = user.theme === 'aryan' ? 'bg-gradient-to-br from-pink-500 to-rose-600' : 'bg-gradient-to-br from-green-500 to-emerald-600';
        const lightIcon = user.theme === 'aryan' ? 'bg-pink-50 text-pink-600' : 'bg-green-50 text-green-600';

        const handleDateChange = (e) => {
            if(e.target.value) { setCurrentDate(e.target.value); setView('activity'); }
        };

        return (
            <div className="pb-20 max-w-lg mx-auto min-h-screen bg-slate-50">
                {/* Header */}
                <div className="bg-white p-6 rounded-b-[2rem] shadow-sm border-b border-slate-100">
                    <div className="flex justify-between items-center mb-6">
                        <div>
                            <h1 className="text-2xl font-black text-slate-800">Hi, {user.name.split(' ')[0]}</h1>
                            <p className="text-xs text-slate-400 font-bold tracking-wider">DAILY TRACKER</p>
                        </div>
                        <button onClick={exitApp} className="p-2 bg-slate-100 rounded-full text-slate-400 hover:text-red-500"><Icons.Logout /></button>
                    </div>

                    {/* Progress Card */}
                    <div onClick={() => { setCurrentDate(DB.getToday()); setView('activity'); }}
                        className={`${theme} p-6 rounded-3xl text-white shadow-xl transform transition active:scale-95 cursor-pointer relative overflow-hidden`}>
                        <div className="relative z-10">
                            <div className="flex justify-between items-start">
                                <div>
                                    <p className="text-white/80 text-xs font-bold uppercase mb-1">Today's Score</p>
                                    <h2 className="text-5xl font-black">{stats.today}<span className="text-2xl opacity-60">%</span></h2>
                                </div>
                                <div className="bg-white/20 p-2 rounded-xl backdrop-blur-sm"><Icons.User /></div>
                            </div>
                            <div className="mt-4 inline-flex items-center gap-2 bg-black/20 px-3 py-1 rounded-full text-xs font-medium">Tap to Update</div>
                        </div>
                        <div className="absolute -right-4 -bottom-8 w-32 h-32 bg-white/10 rounded-full"></div>
                    </div>
                </div>

                {/* Actions */}
                <div className="p-6 space-y-4">
                    {/* Date Picker */}
                    <div className="bg-white p-4 rounded-2xl shadow-sm border flex justify-between items-center">
                        <div className="flex items-center gap-3">
                            <div className={`p-2 rounded-lg ${lightIcon}`}><Icons.Home /></div>
                            <div>
                                <div className="font-bold text-slate-700 text-sm">Past Activity</div>
                                <div className="text-xs text-slate-400">Edit previous logs</div>
                            </div>
                        </div>
                        <input type="date" className="bg-slate-50 border rounded p-1 text-xs" onChange={handleDateChange} />
                    </div>

                    {/* Views */}
                    <div className="grid grid-cols-2 gap-4">
                        <button onClick={() => setView('weekend')} className="bg-white p-4 rounded-2xl shadow-sm border hover:border-purple-200 transition text-left">
                            <div className="p-2 bg-purple-50 text-purple-600 w-fit rounded-lg mb-2"><Icons.Home /></div>
                            <div className="font-bold text-slate-700 text-sm">Weekends</div>
                            <div className="text-xs text-slate-400">Sat-Sun Report</div>
                        </button>
                        <button onClick={() => setView('month')} className="bg-white p-4 rounded-2xl shadow-sm border hover:border-orange-200 transition text-left">
                            <div className="p-2 bg-orange-50 text-orange-600 w-fit rounded-lg mb-2"><Icons.Home /></div>
                            <div className="font-bold text-slate-700 text-sm">Month View</div>
                            <div className="text-xs text-slate-400">Full Calendar</div>
                        </button>
                    </div>
                </div>
            </div>
        );
    };

    // --- 7. ACTIVITY FORM (WITH DETAILED NOTES) ---
    const ActivityForm = () => {
        const { user, currentDate, setView, refresh } = useContext(AppContext);
        const [formData, setFormData] = useState({});
        const [details, setDetails] = useState({});
        const [pct, setPct] = useState(0);
        
        // Modal State for Note Taking
        const [activeNoteId, setActiveNoteId] = useState(null);
        const [noteText, setNoteText] = useState('');

        useEffect(() => {
            const entry = DB.getActivity(user.id, currentDate);
            if(entry) {
                setFormData(entry.activities);
                setDetails(entry.details || {}); // Load saved details
                setPct(entry.percentage);
            } else {
                const empty = {};
                SYLLABUS.forEach(s => {
                    if(s.type === 'list') { empty[s.id] = {}; s.items.forEach(i => empty[s.id][i] = false); }
                    else if(s.type === 'single') { empty[s.id] = false; }
                    else if(s.type === 'sections') { empty[s.id] = {}; Object.keys(s.sections).forEach(k => { empty[s.id][k] = {}; s.sections[k].forEach(i => empty[s.id][k][i] = false); }); }
                });
                setFormData(empty);
                setDetails({});
                setPct(0);
            }
        }, [currentDate]);

        const toggle = (id, sub, item) => {
            const next = JSON.parse(JSON.stringify(formData));
            if(sub && item) next[id][sub][item] = !next[id][sub][item];
            else if(item) next[id][item] = !next[id][item];
            else next[id] = !next[id];
            setFormData(next);
            setPct(DB.calcWeightedPercent(next));
        };

        const openNote = (id) => {
            setActiveNoteId(id);
            setNoteText(details[id] || '');
        };

        const saveNote = () => {
            setDetails(prev => ({ ...prev, [activeNoteId]: noteText }));
            setActiveNoteId(null);
        };

        const save = () => {
            DB.saveActivity(user.id, currentDate, formData, details);
            refresh();
            alert(`Saved! Score: ${pct}%`);
        };

        const generatePDF = () => {
            const doc = new window.jspdf.jsPDF();
            doc.setFontSize(18);
            doc.text(`Report Card: ${user.name}`, 14, 20);
            doc.setFontSize(12);
            doc.text(`Date: ${currentDate} | Day: ${new Date(currentDate).toLocaleDateString('en-US', {weekday:'long'})}`, 14, 30);
            doc.text(`Total Weighted Score: ${pct}%`, 14, 38);
            
            const rows = [];
            SYLLABUS.forEach(s => {
                const note = details[s.id] ? `\n[NOTE: ${details[s.id]}]` : '';
                if(s.type === 'list') s.items.forEach(i => rows.push([`${s.title}`, i, formData[s.id][i]?'DONE':'--']));
                else if(s.type === 'single') rows.push([`${s.title}`, s.label, formData[s.id]?'DONE':'--']);
                else Object.keys(s.sections).forEach(k => s.sections[k].forEach(i => rows.push([`${s.title} - ${k}`, i, formData[s.id][k][i]?'DONE':'--'])));
                
                // Add note row if exists
                if(note) rows.push([`${s.title} Details`, details[s.id], 'INFO']);
            });

            doc.autoTable({ head: [['Category', 'Task', 'Status']], body: rows, startY: 45 });
            doc.save(`${user.name}_Report_${currentDate}.pdf`);
        };

        return (
            <div className="pb-24 max-w-2xl mx-auto bg-slate-50 min-h-screen">
                <div className="sticky top-0 bg-white/95 backdrop-blur-md border-b px-4 py-3 flex justify-between items-center z-40 shadow-sm">
                    <button onClick={() => setView('dashboard')} className="p-2 text-slate-500"><Icons.Back /></button>
                    <div className="text-center">
                        <div className="font-bold text-slate-800 text-sm">{currentDate}</div>
                        <div className={`text-xs font-bold ${pct>=80?'text-green-600':pct>=50?'text-yellow-600':'text-red-500'}`}>{pct}% Done</div>
                    </div>
                    <button onClick={save} className="bg-blue-600 text-white p-2 rounded-full shadow-lg tap-effect"><Icons.Save /></button>
                </div>

                <div className="p-4 space-y-4">
                    <button onClick={generatePDF} className="w-full text-center text-xs text-blue-500 hover:underline flex justify-center gap-2 items-center">
                        <Icons.Pdf /> Download Official Report Card
                    </button>
                    
                    {SYLLABUS.map(s => (
                        <div key={s.id} className="bg-white p-4 rounded-2xl shadow-sm border border-slate-100">
                            <div className="flex justify-between items-center mb-3 pb-2 border-b border-slate-50">
                                <div className="flex items-center gap-2">
                                    <h3 className="font-bold text-slate-700 text-sm uppercase">{s.title}</h3>
                                    {/* Topic Detail Button */}
                                    <button onClick={() => openNote(s.id)} className="text-slate-300 hover:text-blue-500 transition">
                                        <svg className="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M11 5H6a2 2 0 00-2 2v11a2 2 0 002 2h11a2 2 0 002-2v-5m-1.414-9.414a2 2 0 112.828 2.828L11.828 15H9v-2.828l8.586-8.586z" /></svg>
                                    </button>
                                </div>
                                <span className="text-[10px] font-mono bg-slate-100 px-2 py-1 rounded text-slate-500">{s.weight}%</span>
                            </div>

                            {/* Show if note exists */}
                            {details[s.id] && <div className="mb-3 text-xs bg-yellow-50 text-yellow-800 p-2 rounded border border-yellow-100 italic">" {details[s.id]} "</div>}

                            {s.type === 'single' && (
                                <label className="flex items-center gap-3 p-2 hover:bg-slate-50 rounded-lg cursor-pointer">
                                    <input type="checkbox" className="w-5 h-5 accent-blue-600" checked={formData[s.id]||false} onChange={() => toggle(s.id)} />
                                    <span className="text-sm text-slate-600">{s.label}</span>
                                </label>
                            )}
                            {s.type === 'list' && s.items.map(i => (
                                <label key={i} className="flex items-center gap-3 mb-2 p-1 hover:bg-slate-50 rounded cursor-pointer">
                                    <input type="checkbox" className="w-5 h-5 accent-blue-600" checked={formData[s.id]?.[i]||false} onChange={() => toggle(s.id, null, i)} />
                                    <span className={`text-sm ${formData[s.id]?.[i]?'text-slate-400 line-through':'text-slate-600'}`}>{i}</span>
                                </label>
                            ))}
                            {s.type === 'sections' && Object.keys(s.sections).map(sec => (
                                <div key={sec} className="mb-4">
                                    <div className="text-xs font-bold text-blue-500 mb-2 uppercase">{sec}</div>
                                    <div className="space-y-1 pl-2 border-l-2 border-slate-100">
                                        {s.sections[sec].map(i => (
                                            <label key={i} className="flex items-center gap-3 mb-1 p-1 hover:bg-slate-50 rounded cursor-pointer">
                                                <input type="checkbox" className="w-4 h-4 accent-blue-600" checked={formData[s.id]?.[sec]?.[i]||false} onChange={() => toggle(s.id, sec, i)} />
                                                <span className={`text-sm ${formData[s.id]?.[sec]?.[i]?'text-slate-400 line-through':'text-slate-600'}`}>{i}</span>
                                            </label>
                                        ))}
                                    </div>
                                </div>
                            ))}
                        </div>
                    ))}
                </div>

                {/* MODAL FOR NOTES */}
                {activeNoteId && (
                    <div className="fixed inset-0 bg-black/50 z-50 flex items-center justify-center p-4">
                        <div className="bg-white w-full max-w-sm rounded-2xl p-4 shadow-2xl animate-enter">
                            <h3 className="font-bold text-lg mb-2">Add Topic Details</h3>
                            <textarea className="w-full border rounded-xl p-3 h-32 text-sm focus:ring-2 focus:ring-blue-500 outline-none" 
                                placeholder="E.g., Completed Chapter 4, revised notes..." value={noteText} onChange={(e) => setNoteText(e.target.value)}></textarea>
                            <div className="flex gap-2 mt-4">
                                <button onClick={() => setActiveNoteId(null)} className="flex-1 py-2 bg-slate-100 rounded-xl text-slate-600 font-bold">Cancel</button>
                                <button onClick={saveNote} className="flex-1 py-2 bg-blue-600 text-white rounded-xl font-bold">Save Note</button>
                            </div>
                        </div>
                    </div>
                )}
            </div>
        );
    };

    // --- 8. WEEKEND & MONTH VIEWS ---
    const WeekendView = () => {
        const { user, setView, setCurrentDate } = useContext(AppContext);
        const [list, setList] = useState([]);
        useEffect(() => {
            const now = new Date();
            const y = now.getFullYear(), m = now.getMonth();
            const days = new Date(y, m + 1, 0).getDate();
            const arr = [];
            for(let d=1; d<=days; d++) {
                const dateObj = new Date(y, m, d);
                if(dateObj.getDay() === 0 || dateObj.getDay() === 6) {
                    const offset = dateObj.getTimezoneOffset() * 60000;
                    const dateStr = new Date(dateObj.getTime() - offset).toISOString().split('T')[0];
                    const entry = DB.getActivity(user.id, dateStr);
                    arr.push({ date: dateStr, dayName: dateObj.getDay()===0?'Sunday':'Saturday', score: entry ? entry.percentage : 0 });
                }
            }
            setList(arr.reverse());
        }, []);

        return (
            <div className="pb-20 max-w-lg mx-auto min-h-screen bg-slate-50 p-6">
                <div className="flex items-center gap-4 mb-6">
                    <button onClick={() => setView('dashboard')} className="p-2 bg-white rounded-full shadow"><Icons.Back /></button>
                    <h2 className="text-xl font-bold text-slate-800">Weekend Report</h2>
                </div>
                <div className="space-y-4">
                    {list.map(w => (
                        <div key={w.date} onClick={() => { setCurrentDate(w.date); setView('activity'); }}
                             className="bg-white p-4 rounded-2xl shadow-sm border flex justify-between items-center cursor-pointer hover:border-blue-300">
                            <div>
                                <div className="font-bold text-slate-700">{w.date}</div>
                                <div className="text-xs font-bold uppercase text-slate-400">{w.dayName}</div>
                            </div>
                            <div className="flex items-center gap-3">
                                <span className="font-black text-xl text-slate-800">{w.score}%</span>
                                <div className={`w-2 h-8 rounded ${w.score>=80?'bg-green-500':w.score>=50?'bg-yellow-500':'bg-red-500'}`}></div>
                            </div>
                        </div>
                    ))}
                </div>
            </div>
        );
    };

    const MonthView = () => {
        const { user, setView, setCurrentDate } = useContext(AppContext);
        const [days, setDays] = useState([]);
        const [monthName, setMonthName] = useState('');
        useEffect(() => {
            const now = new Date();
            setMonthName(now.toLocaleString('default', { month: 'long', year: 'numeric' }));
            const y = now.getFullYear(), m = now.getMonth();
            const totalDays = new Date(y, m + 1, 0).getDate();
            const firstDayIndex = new Date(y, m, 1).getDay();
            const arr = [];
            for(let i=0; i<firstDayIndex; i++) arr.push({ empty: true });
            for(let d=1; d<=totalDays; d++) {
                const dateObj = new Date(y, m, d);
                // FIX: Use local time offset for Date String
                const offset = dateObj.getTimezoneOffset() * 60000;
                const dateStr = new Date(dateObj.getTime() - offset).toISOString().split('T')[0];
                const entry = DB.getActivity(user.id, dateStr);
                arr.push({ d, date: dateStr, score: entry ? entry.percentage : 0, empty: false });
            }
            setDays(arr);
        }, []);

        const getColor = (s) => {
            if(s === 0) return 'bg-slate-50 text-slate-300';
            if(s < 50) return 'bg-red-50 text-red-600 font-bold border-red-200';
            if(s < 80) return 'bg-yellow-50 text-yellow-600 font-bold border-yellow-200';
            return 'bg-green-100 text-green-700 font-bold border-green-200';
        };

        return (
            <div className="pb-20 max-w-lg mx-auto min-h-screen bg-slate-50 p-6">
                <div className="flex justify-between items-center mb-6">
                    <h2 className="text-xl font-bold text-slate-800">{monthName}</h2>
                    <button onClick={() => setView('dashboard')} className="p-2 bg-white rounded-full shadow"><Icons.Back /></button>
                </div>
                <div className="bg-white p-4 rounded-3xl shadow-sm border border-slate-100">
                    <div className="grid grid-cols-7 gap-2 mb-4 text-center text-xs font-bold text-slate-400">
                        {['S','M','T','W','T','F','S'].map((d,i)=><div key={i}>{d}</div>)}
                    </div>
                    <div className="grid grid-cols-7 gap-2">
                        {days.map((item, idx) => (
                            item.empty ? <div key={idx}></div> : 
                            <button key={item.date} onClick={() => { setCurrentDate(item.date); setView('activity'); }}
                                className={`aspect-square rounded-xl flex flex-col items-center justify-center border transition hover:scale-110 ${getColor(item.score)}`}>
                                <span className="text-sm">{item.d}</span>
                                {item.score > 0 && <span className="text-[9px]">{item.score}%</span>}
                            </button>
                        ))}
                    </div>
                </div>
            </div>
        );
    };
    // --- 9. FINANCIAL & MORAL MODULES ---

    // 9.1 MODULE HOME SCREEN
    const FinMoralHome = () => {
        const { setView } = useContext(AppContext);
        return (
            <div className="min-h-screen bg-slate-900 text-white p-6 flex flex-col items-center justify-center relative overflow-hidden">
                <div className="absolute top-0 left-0 w-full h-full bg-[url('https://www.transparenttextures.com/patterns/cubes.png')] opacity-10"></div>
                
                <div className="z-10 w-full max-w-md space-y-6 animate-enter">
                    <div className="text-center mb-8">
                        <h1 className="text-3xl font-black text-transparent bg-clip-text bg-gradient-to-r from-yellow-400 to-yellow-600">Life Management</h1>
                        <p className="text-slate-400 text-sm">Finance & Ethics Control Center</p>
                    </div>

                    <button onClick={() => setView('fin_hub')} className="w-full bg-slate-800 border border-slate-700 p-6 rounded-2xl hover:bg-slate-700 transition flex items-center gap-4 group">
                        <div className="bg-yellow-500/10 p-4 rounded-full text-yellow-500 group-hover:scale-110 transition"><Icons.Money /></div>
                        <div className="text-left">
                            <h3 className="text-xl font-bold">Financial Statements</h3>
                            <p className="text-xs text-slate-400">Budget, Accounts & Ledger</p>
                        </div>
                    </button>

                    <button onClick={() => setView('moral_tracker')} className="w-full bg-slate-800 border border-slate-700 p-6 rounded-2xl hover:bg-slate-700 transition flex items-center gap-4 group">
                        <div className="bg-blue-500/10 p-4 rounded-full text-blue-500 group-hover:scale-110 transition"><Icons.Moral /></div>
                        <div className="text-left">
                            <h3 className="text-xl font-bold">Moral Ethics</h3>
                            <p className="text-xs text-slate-400">Habits & Discipline Tracker</p>
                        </div>
                    </button>

                    <button onClick={() => setView('entry')} className="w-full py-4 text-slate-500 text-sm hover:text-white transition flex justify-center gap-2 items-center">
                        <Icons.Back /> Return to Login
                    </button>
                </div>
            </div>
        );
    };

    // 9.2 FINANCIAL HUB
    const FinancialHub = () => {
        const { setView } = useContext(AppContext);
        return (
            <div className="min-h-screen bg-slate-50 p-6">
                <div className="max-w-md mx-auto space-y-4 animate-enter">
                    <div className="flex items-center gap-4 mb-6">
                        <button onClick={() => setView('fin_moral_home')} className="p-2 bg-white rounded-full shadow"><Icons.Back /></button>
                        <h2 className="text-2xl font-bold text-slate-800">Financial Hub</h2>
                    </div>

                    <div onClick={() => setView('fin_budget')} className="bg-gradient-to-r from-yellow-500 to-orange-500 p-6 rounded-2xl text-white shadow-lg cursor-pointer transform hover:scale-[1.02] transition">
                        <h3 className="text-lg font-bold">Monthly Budget</h3>
                        <p className="text-yellow-100 text-sm opacity-90">Plan & Analyze Spending</p>
                    </div>

                    <div onClick={() => setView('fin_money')} className="bg-white p-6 rounded-2xl shadow-sm border border-slate-200 cursor-pointer hover:border-blue-400 transition">
                        <h3 className="text-lg font-bold text-slate-800">Money Management</h3>
                        <p className="text-slate-400 text-sm">Daily Transaction Ledger</p>
                    </div>

                    <div onClick={() => setView('fin_accounts')} className="bg-white p-6 rounded-2xl shadow-sm border border-slate-200 cursor-pointer hover:border-green-400 transition">
                        <h3 className="text-lg font-bold text-slate-800">Bank Accounts</h3>
                        <p className="text-slate-400 text-sm">SBI, BOB, UBG Balance</p>
                    </div>
                </div>
            </div>
        );
    };

    // 9.3 MONTHLY BUDGET SCREEN
    const BudgetScreen = () => {
        const { setView } = useContext(AppContext);
        const [mode, setMode] = useState('planned'); // planned (1-10) or final (25+)
        const [month, setMonth] = useState(new Date().toISOString().slice(0, 7)); // YYYY-MM
        const [data, setData] = useState({ income: 0, expenses: [], savings: 0, notes: '' });

        useEffect(() => {
            const d = new Date().getDate();
            if(d >= 25) setMode('final'); else setMode('planned');
            loadData(month, d >= 25 ? 'final' : 'planned');
        }, []);

        const loadData = (m, type) => {
            const saved = DB.getBudget(`${m}_${type}`);
            setData(saved || { income: 0, expenses: [{name:'', amount:0}], savings: 0, notes: '' });
        };

        const handleModeChange = (m) => { setMode(m); loadData(month, m); };
        
        const updateExp = (idx, field, val) => {
            const newExp = [...data.expenses];
            newExp[idx][field] = val;
            setData({...data, expenses: newExp});
        };
        
        const addExp = () => setData({...data, expenses: [...data.expenses, {name:'', amount:0}]});
        
        const save = () => {
            DB.saveBudget(`${month}_${mode}`, data);
            alert('Budget Saved Successfully!');
        };

        const downloadPDF = () => {
            const doc = new window.jspdf.jsPDF();
            doc.text(`${mode.toUpperCase()} BUDGET REPORT - ${month}`, 14, 20);
            doc.text(`Total Income: ₹${data.income}`, 14, 30);
            doc.text(`Target Savings: ₹${data.savings}`, 14, 40);
            doc.text(`Notes: ${data.notes}`, 14, 50);
            
            const rows = data.expenses.map(e => [e.name, `Rs. ${e.amount}`]);
            let totalExp = data.expenses.reduce((acc, curr) => acc + Number(curr.amount), 0);
            rows.push(['TOTAL EXPENSES', `Rs. ${totalExp}`]);

            doc.autoTable({ head: [['Expense Item', 'Amount']], body: rows, startY: 60 });
            doc.save(`Budget_${month}_${mode}.pdf`);
        };

        return (
            <div className="pb-24 max-w-lg mx-auto min-h-screen bg-slate-50 p-4">
                <div className="flex items-center justify-between mb-4 sticky top-0 bg-slate-50 py-2 z-20">
                    <button onClick={() => setView('fin_hub')}><Icons.Back /></button>
                    <div className="font-bold">{month}</div>
                    <button onClick={save} className="text-blue-600"><Icons.Save /></button>
                </div>

                <div className="flex bg-white rounded-xl p-1 mb-4 shadow-sm">
                    <button onClick={() => handleModeChange('planned')} className={`flex-1 py-2 rounded-lg text-sm font-bold ${mode==='planned'?'bg-yellow-500 text-white':'text-slate-500'}`}>Planned (1-10)</button>
                    <button onClick={() => handleModeChange('final')} className={`flex-1 py-2 rounded-lg text-sm font-bold ${mode==='final'?'bg-green-600 text-white':'text-slate-500'}`}>Final (25+)</button>
                </div>

                <div className="space-y-4">
                    <div className="bg-white p-4 rounded-xl shadow-sm">
                        <label className="block text-xs font-bold text-slate-400 uppercase">Total Monthly Income (₹)</label>
                        <input type="number" className="w-full text-2xl font-black text-slate-800 outline-none border-b border-slate-100 py-2" 
                            value={data.income} onChange={(e) => setData({...data, income: e.target.value})} placeholder="0" />
                    </div>

                    <div className="bg-white p-4 rounded-xl shadow-sm">
                        <div className="flex justify-between items-center mb-2">
                            <h3 className="font-bold text-slate-700">Expenses List</h3>
                            <button onClick={addExp} className="text-xs bg-slate-100 px-2 py-1 rounded">+ Add Item</button>
                        </div>
                        {data.expenses.map((exp, i) => (
                            <div key={i} className="flex gap-2 mb-2">
                                <input className="flex-1 border rounded p-2 text-sm" placeholder="Expense Name" value={exp.name} onChange={(e)=>updateExp(i, 'name', e.target.value)} />
                                <input className="w-24 border rounded p-2 text-sm" type="number" placeholder="₹" value={exp.amount} onChange={(e)=>updateExp(i, 'amount', e.target.value)} />
                            </div>
                        ))}
                        <div className="text-right font-bold text-sm mt-2 text-red-500">
                            Total: ₹{data.expenses.reduce((a, b) => a + Number(b.amount || 0), 0)}
                        </div>
                    </div>

                    <div className="bg-white p-4 rounded-xl shadow-sm">
                         <label className="block text-xs font-bold text-slate-400 uppercase">Projected Savings (₹)</label>
                         <input type="number" className="w-full text-xl font-bold text-green-600 outline-none border-b py-2" 
                            value={data.savings} onChange={(e) => setData({...data, savings: e.target.value})} placeholder="0" />
                    </div>

                    <textarea className="w-full bg-white p-4 rounded-xl shadow-sm border-none h-24" placeholder="Notes & Analysis..." 
                        value={data.notes} onChange={(e) => setData({...data, notes: e.target.value})}></textarea>
                    
                    <button onClick={downloadPDF} className="w-full py-3 bg-slate-800 text-white rounded-xl font-bold flex justify-center gap-2">
                        <Icons.Pdf /> Download PDF
                    </button>
                </div>
            </div>
        );
    };

    // 9.4 MONEY MANAGER (LEDGER)
    const MoneyManager = () => {
        const { setView } = useContext(AppContext);
        const [txns, setTxns] = useState([]);
        const [form, setForm] = useState({ type: 'exp', amount: '', desc: '' });

        useEffect(() => setTxns(DB.getTransactions()), []);

        const addTxn = () => {
            if(!form.amount || !form.desc) return;
            const newTxn = { ...form, date: DB.getToday() };
            DB.addTransaction(newTxn);
            setTxns([newTxn, ...txns]);
            setForm({ type: 'exp', amount: '', desc: '' });
        };

        const totalInc = txns.filter(t=>t.type==='inc').reduce((a,b)=>a+Number(b.amount),0);
        const totalExp = txns.filter(t=>t.type==='exp').reduce((a,b)=>a+Number(b.amount),0);

        return (
            <div className="pb-20 max-w-lg mx-auto min-h-screen bg-slate-50 p-4">
                <div className="flex items-center gap-4 mb-4">
                    <button onClick={() => setView('fin_hub')}><Icons.Back /></button>
                    <h2 className="font-bold">Money Ledger</h2>
                </div>

                <div className="grid grid-cols-2 gap-4 mb-6">
                    <div className="bg-green-100 p-4 rounded-xl text-green-700">
                        <div className="text-xs font-bold uppercase">Income</div>
                        <div className="text-xl font-black">₹{totalInc}</div>
                    </div>
                    <div className="bg-red-100 p-4 rounded-xl text-red-700">
                        <div className="text-xs font-bold uppercase">Expense</div>
                        <div className="text-xl font-black">₹{totalExp}</div>
                    </div>
                </div>

                <div className="bg-white p-4 rounded-xl shadow-lg mb-6 sticky top-2 z-20">
                    <div className="flex gap-2 mb-2">
                        <select className="bg-slate-50 p-2 rounded border font-bold text-sm" value={form.type} onChange={e=>setForm({...form, type:e.target.value})}>
                            <option value="exp">Expense (-)</option>
                            <option value="inc">Income (+)</option>
                        </select>
                        <input className="flex-1 bg-slate-50 p-2 rounded border" placeholder="Amount" type="number" value={form.amount} onChange={e=>setForm({...form, amount:e.target.value})} />
                    </div>
                    <div className="flex gap-2">
                        <input className="flex-1 bg-slate-50 p-2 rounded border" placeholder="Description (e.g. Food)" value={form.desc} onChange={e=>setForm({...form, desc:e.target.value})} />
                        <button onClick={addTxn} className="bg-slate-800 text-white px-4 rounded font-bold">Add</button>
                    </div>
                </div>

                <div className="space-y-3">
                    {txns.map((t, i) => (
                        <div key={i} className="bg-white p-3 rounded-lg shadow-sm flex justify-between items-center border-l-4 border-slate-200" style={{borderColor: t.type==='inc'?'#22c55e':'#ef4444'}}>
                            <div>
                                <div className="font-bold text-slate-700">{t.desc}</div>
                                <div className="text-xs text-slate-400">{t.date}</div>
                            </div>
                            <div className={`font-black ${t.type==='inc'?'text-green-600':'text-red-500'}`}>
                                {t.type==='inc'?'+':'-'}₹{t.amount}
                            </div>
                        </div>
                    ))}
                </div>
            </div>
        );
    };

    // 9.5 ACCOUNTS MANAGER
    const AccountsManager = () => {
        const { setView } = useContext(AppContext);
        const [activeBank, setActiveBank] = useState(null); // 'SBI', 'BOB', 'UBG'

        if(activeBank) return <BankDetail bankId={activeBank} goBack={() => setActiveBank(null)} />;

        return (
            <div className="min-h-screen bg-slate-50 p-6 max-w-lg mx-auto">
                <div className="flex items-center gap-4 mb-6">
                    <button onClick={() => setView('fin_hub')} className="p-2 bg-white rounded-full shadow"><Icons.Back /></button>
                    <h2 className="text-2xl font-bold">Bank Accounts</h2>
                </div>
                <div className="space-y-4">
                    {['SBI', 'BOB', 'UBG'].map(bank => {
                        const data = DB.getAccount(bank);
                        return (
                            <div key={bank} onClick={() => setActiveBank(bank)} 
                                className="bg-white p-6 rounded-2xl shadow-sm border border-slate-100 cursor-pointer hover:border-blue-500 transition relative overflow-hidden group">
                                <div className="absolute right-0 top-0 p-4 opacity-10 font-black text-6xl text-slate-300 group-hover:scale-110 transition">{bank}</div>
                                <h3 className="text-xl font-black text-slate-800">{bank} Account</h3>
                                <p className="text-slate-500 text-sm mb-2">Available Balance</p>
                                <div className="text-3xl font-black text-blue-600">₹{data.bal}</div>
                            </div>
                        );
                    })}
                </div>
            </div>
        );
    };

    const BankDetail = ({ bankId, goBack }) => {
        const [data, setData] = useState({ bal: 0, txns: [] });
        const [form, setForm] = useState({ amount: '', type: 'debit', desc: '' });

        useEffect(() => setData(DB.getAccount(bankId)), [bankId]);

        const handleTxn = () => {
            if(!form.amount || !form.desc) return;
            DB.updateAccount(bankId, form.amount, form.type, form.desc);
            setData(DB.getAccount(bankId));
            setForm({ amount: '', type: 'debit', desc: '' });
        };

        return (
            <div className="min-h-screen bg-slate-50 p-4 max-w-lg mx-auto">
                <div className="flex items-center gap-4 mb-4">
                    <button onClick={goBack} className="p-2 bg-white rounded-full"><Icons.Back /></button>
                    <h2 className="font-bold text-xl">{bankId} Passbook</h2>
                </div>

                <div className="bg-blue-600 text-white p-6 rounded-2xl shadow-lg mb-6">
                    <div className="text-blue-100 text-xs font-bold uppercase">Current Balance</div>
                    <div className="text-4xl font-black">₹{data.bal}</div>
                </div>

                <div className="bg-white p-4 rounded-xl shadow-sm mb-6">
                    <div className="flex gap-2 mb-2">
                        <select className="bg-slate-50 p-2 rounded text-sm" value={form.type} onChange={e=>setForm({...form, type:e.target.value})}>
                            <option value="debit">Withdraw (-)</option>
                            <option value="credit">Deposit (+)</option>
                        </select>
                        <input className="flex-1 bg-slate-50 p-2 rounded" type="number" placeholder="Amount" value={form.amount} onChange={e=>setForm({...form, amount:e.target.value})} />
                    </div>
                    <div className="flex gap-2">
                        <input className="flex-1 bg-slate-50 p-2 rounded" placeholder="Description" value={form.desc} onChange={e=>setForm({...form, desc:e.target.value})} />
                        <button onClick={handleTxn} className="bg-blue-600 text-white px-4 rounded font-bold">Update</button>
                    </div>
                </div>

                <div className="space-y-2">
                    {data.txns.map((t, i) => (
                        <div key={i} className="bg-white p-3 rounded-lg border-b flex justify-between items-center">
                            <div>
                                <div className="font-bold text-sm text-slate-700">{t.desc}</div>
                                <div className="text-xs text-slate-400">{t.date}</div>
                            </div>
                            <div className="text-right">
                                <div className={`font-bold ${t.type==='credit'?'text-green-600':'text-red-500'}`}>
                                    {t.type==='credit'?'+':'-'} {t.amount}
                                </div>
                                <div className="text-[10px] text-slate-400">Bal: {t.runningBal}</div>
                            </div>
                        </div>
                    ))}
                </div>
            </div>
        );
    };

    // 9.6 MORAL ETHICS TRACKER
    const MoralTracker = () => {
        const { setView, currentDate, setCurrentDate } = useContext(AppContext);
        const [data, setData] = useState({});
        
        const DEFAULT_HABITS = ['Exercise & Meditation', 'Room & Clothes Cleaning', 'Fresh Mind & Bathing', 'No Masturbation', 'No Wrong Thoughts'];

        useEffect(() => {
            const entry = DB.getMoral(currentDate);
            setData(entry || {});
        }, [currentDate]);

        const toggle = (habit) => {
            const newData = { ...data, [habit]: !data[habit] };
            setData(newData);
            DB.saveMoral(currentDate, newData);
        };

        const score = Math.round((Object.values(data).filter(Boolean).length / DEFAULT_HABITS.length) * 100) || 0;

        return (
            <div className="pb-20 max-w-lg mx-auto min-h-screen bg-slate-50 p-4">
                <div className="flex justify-between items-center mb-6">
                    <button onClick={() => setView('fin_moral_home')}><Icons.Back /></button>
                    <h2 className="font-bold text-xl">Moral Ethics</h2>
                    <div className="w-8"></div>
                </div>

                {/* Calendar Strip */}
                <div className="bg-white p-4 rounded-2xl shadow-sm mb-6 flex justify-between items-center">
                    <input type="date" value={currentDate} onChange={(e) => setCurrentDate(e.target.value)} className="bg-slate-100 p-2 rounded font-bold text-slate-700" />
                    <div className={`text-xl font-black ${score===100?'text-green-600':score>50?'text-yellow-600':'text-red-500'}`}>{score}%</div>
                </div>

                <div className="space-y-3">
                    {DEFAULT_HABITS.map(habit => (
                        <div key={habit} onClick={() => toggle(habit)} 
                             className={`p-4 rounded-xl border-2 cursor-pointer transition flex justify-between items-center ${data[habit] ? 'bg-green-50 border-green-500' : 'bg-white border-white shadow-sm'}`}>
                            <span className={`font-bold ${data[habit]?'text-green-700':'text-slate-600'}`}>{habit}</span>
                            {data[habit] && <div className="w-6 h-6 bg-green-500 text-white rounded-full flex items-center justify-center text-xs">✓</div>}
                        </div>
                    ))}
                </div>

                <div className="mt-8 bg-blue-50 p-4 rounded-xl text-blue-800 text-sm text-center italic">
                    "Discipline is doing what needs to be done, even if you don't want to do it."
                </div>
            </div>
        );
    };

    // --- 10. MAIN APP SWITCHER ---
    const MainApp = () => {
        const { view } = useContext(AppContext);
        switch(view) {
            case 'entry': return <EntryScreen />;
            case 'dashboard': return <Dashboard />;
            case 'activity': return <ActivityForm />;
            case 'weekend': return <WeekendView />;
            case 'month': return <MonthView />;
            
            // New Modules
            case 'fin_moral_home': return <FinMoralHome />;
            case 'fin_hub': return <FinancialHub />;
            case 'fin_budget': return <BudgetScreen />;
            case 'fin_money': return <MoneyManager />;
            case 'fin_accounts': return <AccountsManager />;
            case 'moral_tracker': return <MoralTracker />;
            
            default: return <EntryScreen />;
        }
    };

    // --- 11. RENDER ---
    const root = ReactDOM.createRoot(document.getElementById('root'));
    root.render(
        <AppProvider>
            <MainApp />
        </AppProvider>
    );

    </script>
</body>
</html>
