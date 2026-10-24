<!DOCTYPE html>
<html lang="en" class="h-full bg-black">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Nebuchadnezzar OS // Matrix Terminal & README Studio - Alwin John Shajan (@AlwinJCOde667)</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Google Fonts -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Fira+Code:wght@300;400;600;700&family=VT323&display=swap" rel="stylesheet">
    <!-- FontAwesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">

    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        matrix: {
                            dark: '#030a04',
                            bg: '#000502',
                            green: '#00FF66',
                            bright: '#66FF99',
                            dim: '#008833',
                            alert: '#FF0033',
                            blue: '#00CCFF',
                            amber: '#FFCC00'
                        }
                    },
                    fontFamily: {
                        mono: ['"Fira Code"', 'monospace'],
                        crt: ['"VT323"', 'monospace']
                    }
                }
            }
        }
    </script>

    <style>
        /* Custom CRT Screen & Matrix Styling */
        :root {
            --matrix-color: #00FF66;
            --matrix-dim: #008833;
            --matrix-glow: rgba(0, 255, 102, 0.4);
            --bg-color: #000502;
        }

        body.red-pill-mode {
            --matrix-color: #FF0033;
            --matrix-dim: #880011;
            --matrix-glow: rgba(255, 0, 51, 0.5);
            --bg-color: #0a0002;
        }

        body.blue-pill-mode {
            --matrix-color: #00CCFF;
            --matrix-dim: #005588;
            --matrix-glow: rgba(0, 204, 255, 0.5);
            --bg-color: #00040a;
        }

        body {
            background-color: var(--bg-color);
            color: var(--matrix-color);
            font-family: 'Fira Code', monospace;
            overflow-x: hidden;
            transition: background-color 0.5s ease, color 0.5s ease;
        }

        /* CRT Glow Effect */
        .matrix-glow {
            text-shadow: 0 0 8px var(--matrix-glow), 0 0 15px var(--matrix-glow);
        }

        .matrix-border {
            border: 1px solid var(--matrix-color);
            box-shadow: 0 0 10px var(--matrix-glow), inset 0 0 10px var(--matrix-glow);
        }

        /* Scanline Overlay */
        .crt-overlay::before {
            content: " ";
            display: block;
            position: fixed;
            top: 0; left: 0; bottom: 0; right: 0;
            background: linear-gradient(rgba(18, 16, 16, 0) 50%, rgba(0, 0, 0, 0.35) 50%), linear-gradient(90deg, rgba(255, 0, 0, 0.03), rgba(0, 255, 0, 0.01), rgba(0, 0, 255, 0.03));
            z-index: 40;
            background-size: 100% 3px, 6px 100%;
            pointer-events: none;
            opacity: 0.7;
        }

        /* CRT Screen Glass Curvature Vignette */
        .crt-vignette {
            position: fixed;
            top: 0; left: 0; width: 100vw; height: 100vh;
            box-shadow: inset 0 0 100px rgba(0, 0, 0, 0.9);
            pointer-events: none;
            z-index: 41;
        }

        /* Subtle Screen Flicker Animation */
        @keyframes crtFlicker {
            0% { opacity: 0.98; }
            50% { opacity: 1; }
            52% { opacity: 0.94; }
            54% { opacity: 1; }
            80% { opacity: 0.97; }
            100% { opacity: 0.99; }
        }

        .crt-flicker {
            animation: crtFlicker 0.15s infinite;
        }

        /* Glitch Animation */
        @keyframes glitch {
            0% { transform: translate(0) }
            20% { transform: translate(-3px, 3px) }
            40% { transform: translate(-3px, -3px) }
            60% { transform: translate(3px, 3px) }
            80% { transform: translate(3px, -3px) }
            100% { transform: translate(0) }
        }

        .glitch-effect {
            animation: glitch 0.2s cubic-bezier(.25, .46, .45, .94) both 3;
        }

        /* Blinking Terminal Cursor */
        @keyframes blink {
            0%, 49% { opacity: 1; }
            50%, 100% { opacity: 0; }
        }

        .cursor-blink {
            animation: blink 0.9s infinite;
        }

        /* Custom Terminal Scrollbar */
        ::-webkit-scrollbar {
            width: 6px;
            height: 6px;
        }
        ::-webkit-scrollbar-track {
            background: rgba(0, 20, 5, 0.8);
        }
        ::-webkit-scrollbar-thumb {
            background: var(--matrix-color);
            border-radius: 3px;
            box-shadow: 0 0 5px var(--matrix-glow);
        }

        /* Terminal Prompt input reset */
        .cli-input {
            background: transparent;
            outline: none;
            border: none;
            color: var(--matrix-color);
            font-family: inherit;
        }
    </style>
</head>
<body class="h-full w-full flex flex-col crt-flicker crt-overlay text-sm select-none">

    <!-- Matrix Digital Rain Canvas Background -->
    <canvas id="matrix-canvas" class="fixed top-0 left-0 w-full h-full z-0 opacity-40"></canvas>
    <div class="crt-vignette"></div>

    <!-- MAIN APP CONTAINER -->
    <div class="relative z-10 flex flex-col h-full w-full max-w-7xl mx-auto p-2 md:p-4 gap-3">

        <!-- TOP CONTROL BAR / OPERATOR HEADER -->
        <header class="w-full bg-black/80 backdrop-blur border border-[var(--matrix-color)] p-3 rounded-lg flex flex-wrap items-center justify-between gap-3 shadow-[0_0_15px_var(--matrix-glow)]">
            
            <!-- Left System Identifier -->
            <div class="flex items-center gap-3">
                <div class="w-3 h-3 rounded-full bg-[var(--matrix-color)] animate-ping"></div>
                <div>
                    <h1 class="font-crt text-2xl tracking-wider font-bold matrix-glow flex items-center gap-2 leading-none">
                        <i class="fa-solid fa-terminal text-lg"></i> NEBUCHADNEZZAR OS <span class="text-xs font-mono opacity-70">v4.2.0</span>
                    </h1>
                    <p class="text-xs font-mono opacity-80 mt-0.5">OPERATOR: <span class="font-bold underline decoration-[var(--matrix-color)]">Alwin John Shajan</span> (@AlwinJCOde667)</p>
                </div>
            </div>

            <!-- Navigation Tabs -->
            <nav class="flex flex-wrap items-center gap-1.5 bg-black/60 p-1 border border-matrix-dim rounded-md">
                <button onclick="switchTab('terminal')" id="tab-btn-terminal" class="tab-btn px-3 py-1.5 rounded text-xs font-mono font-bold flex items-center gap-1.5 transition bg-[var(--matrix-color)] text-black">
                    <i class="fa-solid fa-code"></i> TERMINAL CLI
                </button>
                <button onclick="switchTab('construct')" id="tab-btn-construct" class="tab-btn px-3 py-1.5 rounded text-xs font-mono font-bold flex items-center gap-1.5 transition hover:bg-matrix-dim/40">
                    <i class="fa-solid fa-film"></i> CONSTRUCT & GAME
                </button>
                <button onclick="switchTab('readme')" id="tab-btn-readme" class="tab-btn px-3 py-1.5 rounded text-xs font-mono font-bold flex items-center gap-1.5 transition hover:bg-matrix-dim/40">
                    <i class="fa-brands fa-github"></i> README STUDIO
                </button>
            </nav>

            <!-- Right Controls (Audio, Rain Engine, Pill Trigger) -->
            <div class="flex items-center gap-2">
                <!-- Audio Toggle -->
                <button onclick="toggleAudio()" id="audio-btn" class="p-2 bg-black border border-matrix-dim hover:border-[var(--matrix-color)] rounded text-xs transition flex items-center gap-1" title="Toggle Sound Synth">
                    <i id="audio-icon" class="fa-solid fa-volume-xmark text-matrix-alert"></i>
                    <span class="hidden sm:inline">AUDIO</span>
                </button>

                <!-- Rain Settings Toggle -->
                <button onclick="toggleRainControls()" class="p-2 bg-black border border-matrix-dim hover:border-[var(--matrix-color)] rounded text-xs transition flex items-center gap-1" title="Matrix Rain Engine Settings">
                    <i class="fa-solid fa-sliders"></i>
                    <span class="hidden sm:inline">RAIN ENGINE</span>
                </button>

                <!-- Choice Modal Trigger -->
                <button onclick="openPillModal()" class="px-3 py-1.5 bg-gradient-to-r from-red-900/80 to-blue-900/80 border border-white/40 hover:border-white rounded text-xs font-bold text-white transition shadow-lg flex items-center gap-1.5">
                    <i class="fa-solid fa-capsules"></i> CHOOSE PILL
                </button>
            </div>
        </header>

        <!-- FLOATING MATRIX RAIN CONTROL DRAWER (COLLAPSIBLE) -->
        <div id="rain-controls-drawer" class="hidden bg-black/90 border border-[var(--matrix-color)] p-4 rounded-lg shadow-2xl flex flex-wrap items-center justify-between gap-4 text-xs font-mono z-30 transition-all">
            <div class="flex items-center gap-2">
                <i class="fa-solid fa-sliders text-base"></i>
                <span class="font-bold uppercase tracking-widest">Rain Engine Parameters</span>
            </div>
            
            <div class="flex flex-wrap items-center gap-6">
                <!-- Speed Slider -->
                <div class="flex items-center gap-2">
                    <label>Speed:</label>
                    <input type="range" id="rain-speed" min="1" max="50" value="25" oninput="updateRainConfig()" class="accent-[var(--matrix-color)] cursor-pointer w-24">
                </div>

                <!-- Density Slider -->
                <div class="flex items-center gap-2">
                    <label>Density / Font:</label>
                    <input type="range" id="rain-font-size" min="10" max="28" value="16" oninput="updateRainConfig()" class="accent-[var(--matrix-color)] cursor-pointer w-24">
                </div>

                <!-- Color Theme Preset -->
                <div class="flex items-center gap-2">
                    <label>Palette:</label>
                    <select id="rain-theme" onchange="changeThemePreset(this.value)" class="bg-black border border-[var(--matrix-color)] text-[var(--matrix-color)] px-2 py-1 rounded outline-none">
                        <option value="green">Classic Matrix Green</option>
                        <option value="red">Red Pill Core Glitch</option>
                        <option value="blue">Blue Pill Construct</option>
                        <option value="amber">Zion Terminal Amber</option>
                    </select>
                </div>
            </div>

            <button onclick="toggleRainControls()" class="text-xs hover:underline text-matrix-alert">
                <i class="fa-solid fa-xmark"></i> Close
            </button>
        </div>

        <!-- MAIN DISPLAY PANELS (CONTAINER) -->
        <main class="flex-1 min-h-0 relative flex flex-col">

            <!-- ================= VIEW 1: INTERACTIVE CLI TERMINAL ================= -->
            <section id="view-terminal" class="view-panel h-full flex flex-col md:flex-row gap-3">
                
                <!-- Terminal Window -->
                <div class="flex-1 flex flex-col bg-black/85 backdrop-blur border border-[var(--matrix-color)] rounded-lg overflow-hidden shadow-[0_0_20px_rgba(0,0,0,0.8)]">
                    
                    <!-- Terminal Top Header Bar -->
                    <div class="bg-matrix-dark border-b border-matrix-dim p-2 px-3 flex items-center justify-between text-xs font-mono">
                        <div class="flex items-center gap-2">
                            <span class="w-3 h-3 rounded-full bg-red-600 inline-block"></span>
                            <span class="w-3 h-3 rounded-full bg-yellow-600 inline-block"></span>
                            <span class="w-3 h-3 rounded-full bg-green-600 inline-block"></span>
                            <span class="ml-2 font-bold opacity-80"><i class="fa-solid fa-terminal mr-1"></i> bash - operator@nebuchadnezzar:~</span>
                        </div>
                        <div class="flex items-center gap-3 opacity-70">
                            <button onclick="clearTerminal()" title="Clear screen" class="hover:text-white"><i class="fa-solid fa-eraser"></i> Clear</button>
                            <span id="system-time">00:00:00</span>
                        </div>
                    </div>

                    <!-- Terminal Output Scroll Container -->
                    <div id="terminal-output" class="flex-1 p-4 overflow-y-auto font-mono text-xs md:text-sm leading-relaxed space-y-2">
                        
                        <!-- Boot Sequence Welcome Output -->
                        <div class="text-matrix-bright">
                            <pre class="font-crt text-xs md:text-sm leading-none opacity-90 hidden sm:block">
  _  _ ___ ___ _  _  ___ ___  _  _   _   ___ ____ _____ _   ___ 
 | \| | __| _ ) || |/ __/   \| \| | /_\ |   \_  /|_  //_\ | _ \
 | .` | _|| _ \ || | (_| (_) | .` |/ _ \| |) / /  / // _ \|   /
 |_|\_|___|___/_||_|\___\___/|_|\_/_/ \_\___/___/___/_/ \_\_|_\ 
                            </pre>
                            <p class="mt-2 font-bold text-base"><i class="fa-solid fa-satellite-dish animate-pulse mr-2"></i> NEBUCHADNEZZAR MAINFRAME LINK ESTABLISHED</p>
                            <p class="opacity-80">Subject: <span class="text-white font-bold">Alwin John Shajan</span> | GitHub: <span class="text-white">@AlwinJCOde667</span></p>
                            <p class="opacity-70 text-xs">Type <span class="text-white underline font-bold">help</span> to view available system commands or click quick links below.</p>
                            <div class="my-2 border-b border-matrix-dim opacity-40"></div>
                        </div>

                    </div>

                    <!-- Command Input Prompt Bar -->
                    <div class="bg-black border-t border-matrix-dim p-3 flex items-center gap-2 font-mono text-sm">
                        <span class="text-[var(--matrix-color)] font-bold flex items-center gap-1">
                            <span>operator@zion</span><span class="text-white">:</span><span class="text-matrix-bright">~</span>$
                        </span>
                        <input type="text" id="cli-input" class="cli-input flex-1 bg-transparent text-white font-mono text-sm" placeholder="type 'help', 'whoami', 'skills', 'redpill'..." autocomplete="off" spellcheck="false">
                        <button onclick="handleCommandSubmit()" class="px-3 py-1 bg-[var(--matrix-color)] text-black font-bold text-xs rounded hover:opacity-90">
                            EXEC <i class="fa-solid fa-play text-[10px]"></i>
                        </button>
                    </div>
                </div>

                <!-- Terminal Quick Action Sidebar -->
                <div class="w-full md:w-64 bg-black/80 backdrop-blur border border-matrix-dim p-3 rounded-lg flex flex-col gap-3 font-mono text-xs">
                    <h2 class="font-bold border-b border-matrix-dim pb-1.5 flex items-center gap-2 text-matrix-bright">
                        <i class="fa-solid fa-bolt"></i> QUICK COMMANDS
                    </h2>

                    <div class="grid grid-cols-2 md:grid-cols-1 gap-1.5">
                        <button onclick="runQuickCommand('whoami')" class="p-2 text-left bg-black border border-matrix-dim hover:border-[var(--matrix-color)] rounded transition flex items-center justify-between">
                            <span>> whoami</span> <i class="fa-solid fa-user text-matrix-dim"></i>
                        </button>
                        <button onclick="runQuickCommand('skills')" class="p-2 text-left bg-black border border-matrix-dim hover:border-[var(--matrix-color)] rounded transition flex items-center justify-between">
                            <span>> skills</span> <i class="fa-solid fa-laptop-code text-matrix-dim"></i>
                        </button>
                        <button onclick="runQuickCommand('projects')" class="p-2 text-left bg-black border border-matrix-dim hover:border-[var(--matrix-color)] rounded transition flex items-center justify-between">
                            <span>> projects</span> <i class="fa-solid fa-diagram-project text-matrix-dim"></i>
                        </button>
                        <button onclick="runQuickCommand('stats')" class="p-2 text-left bg-black border border-matrix-dim hover:border-[var(--matrix-color)] rounded transition flex items-center justify-between">
                            <span>> stats</span> <i class="fa-solid fa-chart-simple text-matrix-dim"></i>
                        </button>
                        <button onclick="runQuickCommand('matrix-dodge')" class="p-2 text-left bg-black border border-matrix-dim hover:border-[var(--matrix-color)] rounded transition flex items-center justify-between text-matrix-bright font-bold">
                            <span>> matrix-dodge</span> <i class="fa-solid fa-gamepad text-matrix-bright"></i>
                        </button>
                        <button onclick="runQuickCommand('cat matrix.log')" class="p-2 text-left bg-black border border-matrix-dim hover:border-[var(--matrix-color)] rounded transition flex items-center justify-between">
                            <span>> cat matrix.log</span> <i class="fa-solid fa-file-code text-matrix-dim"></i>
                        </button>
                        <button onclick="runQuickCommand('redpill')" class="p-2 text-left bg-red-950/40 border border-red-600/50 hover:border-red-500 rounded text-red-400 font-bold transition flex items-center justify-between">
                            <span>> redpill</span> <i class="fa-solid fa-capsules"></i>
                        </button>
                    </div>

                    <!-- System Telemetry Box -->
                    <div class="mt-auto bg-matrix-dark/80 p-2.5 border border-matrix-dim rounded text-[11px] space-y-1.5">
                        <div class="font-bold text-matrix-bright border-b border-matrix-dim pb-1 flex justify-between">
                            <span>SYSTEM TELEMETRY</span>
                            <span class="animate-pulse">● LIVE</span>
                        </div>
                        <div class="flex justify-between">
                            <span class="opacity-70">CPU Load:</span>
                            <span id="telemetry-cpu">14%</span>
                        </div>
                        <div class="flex justify-between">
                            <span class="opacity-70">Memory:</span>
                            <span>4.2 GB / 16 GB</span>
                        </div>
                        <div class="flex justify-between">
                            <span class="opacity-70">Signal Link:</span>
                            <span class="text-matrix-bright font-bold">Encrypted 256-bit</span>
                        </div>
                    </div>

                </div>

            </section>


            <!-- ================= VIEW 2: CONSTRUCT & MINI-GAME ================= -->
            <section id="view-construct" class="view-panel hidden h-full flex flex-col gap-3">
                
                <div class="grid grid-cols-1 lg:grid-cols-3 gap-3 flex-1 min-h-0">
                    
                    <!-- Bullet Time Mini Game Area (2 Cols) -->
                    <div class="lg:col-span-2 bg-black/85 border border-[var(--matrix-color)] rounded-lg p-4 flex flex-col justify-between relative overflow-hidden shadow-2xl">
                        
                        <!-- Header -->
                        <div class="flex items-center justify-between border-b border-matrix-dim pb-2 mb-2 z-10">
                            <div>
                                <h2 class="font-bold text-base text-matrix-bright flex items-center gap-2">
                                    <i class="fa-solid fa-person-running"></i> MATRIX CONSTRUCT: BULLET TIME SIMULATOR
                                </h2>
                                <p class="text-xs opacity-70">Dodge Agent Sentinels using Mouse / Touch or Arrow Keys!</p>
                            </div>
                            <div class="flex items-center gap-3 font-mono text-xs">
                                <div>SCORE: <span id="game-score" class="font-bold text-white text-base">0</span></div>
                                <button onclick="restartGame()" class="px-3 py-1 bg-[var(--matrix-color)] text-black font-bold rounded text-xs hover:opacity-90">
                                    <i class="fa-solid fa-rotate-right"></i> RESTART
                                </button>
                            </div>
                        </div>

                        <!-- Game Canvas Box -->
                        <div class="relative flex-1 w-full min-h-[280px] bg-black/90 border border-matrix-dim rounded overflow-hidden flex items-center justify-center">
                            <canvas id="game-canvas" class="w-full h-full block cursor-none"></canvas>
                            
                            <!-- Start Overlay -->
                            <div id="game-start-overlay" class="absolute inset-0 bg-black/80 backdrop-blur flex flex-col items-center justify-center text-center p-6 z-20">
                                <i class="fa-solid fa-person-rays text-5xl text-[var(--matrix-color)] mb-3 animate-bounce"></i>
                                <h3 class="font-bold text-xl text-white mb-1">BULLET-TIME SIMULATION</h3>
                                <p class="text-xs opacity-80 max-w-md mb-4">Move your cursor or touch target to dodge oncoming Agent bullets and code artifacts. How long can you survive inside the construct?</p>
                                <button onclick="startGame()" class="px-6 py-2.5 bg-[var(--matrix-color)] text-black font-bold rounded-lg text-sm shadow-[0_0_15px_var(--matrix-glow)] hover:scale-105 transition">
                                    <i class="fa-solid fa-play"></i> INITIATE SIMULATION
                                </button>
                            </div>
                        </div>

                    </div>

                    <!-- Matrix Visual Motion Feed / Lore Panel (1 Col) -->
                    <div class="bg-black/85 border border-matrix-dim rounded-lg p-4 flex flex-col gap-3 font-mono text-xs overflow-y-auto">
                        <h2 class="font-bold border-b border-matrix-dim pb-2 text-matrix-bright flex items-center gap-2">
                            <i class="fa-solid fa-eye"></i> VISUAL CONSTRUCT FEED
                        </h2>

                        <!-- Matrix Gif Scene Cards -->
                        <div class="space-y-3">
                            <div class="border border-matrix-dim p-2 rounded bg-black/60">
                                <div class="text-[11px] font-bold text-white mb-1 flex items-center justify-between">
                                    <span>CONSTRUCT: DIGITAL RAIN</span>
                                    <span class="text-matrix-bright">STREAM 01</span>
                                </div>
                                <img src="https://media.giphy.com/media/v1.Y2lkPTc5MGI3NjExM3Z2eXZ0Z3FqZGN1Z3Vscnd2MWFrcmxidTRqZmJuZmptNXBnbXF4ZCZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/3rXna9rZacY8w/giphy.gif" alt="Matrix Code Rain" class="w-full h-28 object-cover rounded border border-matrix-dim/50">
                            </div>

                            <div class="border border-matrix-dim p-2 rounded bg-black/60">
                                <div class="text-[11px] font-bold text-white mb-1 flex items-center justify-between">
                                    <span>OPERATOR NEOPROCESSOR</span>
                                    <span class="text-matrix-bright">STREAM 02</span>
                                </div>
                                <img src="https://media.giphy.com/media/eIm6244q8n4vC/giphy.gif" alt="Neo Bullet Dodge" class="w-full h-28 object-cover rounded border border-matrix-dim/50">
                            </div>
                        </div>

                        <!-- Quote Display -->
                        <div class="mt-auto p-3 bg-matrix-dark border border-matrix-dim rounded italic text-matrix-bright/90">
                            "There is a difference between knowing the path and walking the path."
                            <div class="text-right text-[10px] font-bold not-italic opacity-70 mt-1">— Morpheus</div>
                        </div>
                    </div>

                </div>

            </section>


            <!-- ================= VIEW 3: GITHUB README STUDIO ================= -->
            <section id="view-readme" class="view-panel hidden h-full flex flex-col gap-3">
                
                <!-- Action Header -->
                <div class="bg-black/85 border border-matrix-dim p-3 rounded-lg flex flex-wrap items-center justify-between gap-3">
                    <div>
                        <h2 class="font-bold text-base text-matrix-bright flex items-center gap-2">
                            <i class="fa-brands fa-github"></i> SINGLE-FILE MATRIX README GENERATOR
                        </h2>
                        <p class="text-xs opacity-70">Ready to use for GitHub Profile <span class="text-white font-bold">@AlwinJCOde667</span></p>
                    </div>

                    <div class="flex items-center gap-2">
                        <button onclick="copyReadmeCode()" class="px-4 py-2 bg-[var(--matrix-color)] text-black font-bold text-xs rounded hover:opacity-90 flex items-center gap-2 shadow-[0_0_10px_var(--matrix-glow)]">
                            <i class="fa-solid fa-copy"></i> COPY README MARKDOWN
                        </button>
                    </div>
                </div>

                <!-- Markdown Code Window & Preview Split -->
                <div class="flex-1 grid grid-cols-1 lg:grid-cols-2 gap-3 min-h-0">
                    
                    <!-- Raw Markdown Source Code -->
                    <div class="flex flex-col bg-black/90 border border-matrix-dim rounded-lg overflow-hidden">
                        <div class="bg-matrix-dark p-2 border-b border-matrix-dim text-xs font-mono font-bold flex justify-between items-center">
                            <span><i class="fa-solid fa-code text-matrix-bright mr-1"></i> README.md (Raw Markdown Source)</span>
                            <span class="text-[10px] opacity-60">AlwinJCOde667/README.md</span>
                        </div>
                        <textarea id="readme-raw-text" readonly class="flex-1 p-3 bg-black text-xs font-mono text-matrix-bright leading-relaxed resize-none outline-none selection:bg-[var(--matrix-color)] selection:text-black"></textarea>
                    </div>

                    <!-- Rendered Preview Simulation -->
                    <div class="flex flex-col bg-black/90 border border-matrix-dim rounded-lg overflow-hidden">
                        <div class="bg-matrix-dark p-2 border-b border-matrix-dim text-xs font-mono font-bold flex justify-between items-center">
                            <span><i class="fa-solid fa-eye text-matrix-bright mr-1"></i> Rendered Profile Preview</span>
                            <span class="text-[10px] opacity-60">GitHub Style Render</span>
                        </div>
                        <div id="readme-rendered-preview" class="flex-1 p-4 overflow-y-auto text-xs font-mono space-y-4 text-white">
                            <!-- Rendered HTML simulation dynamically injected -->
                        </div>
                    </div>

                </div>

            </section>

        </main>
    </div>

    <!-- ================= RED PILL vs BLUE PILL CHOICE MODAL ================= -->
    <div id="pill-modal" class="hidden fixed inset-0 z-50 bg-black/90 backdrop-blur-md flex items-center justify-center p-4">
        <div class="bg-black border-2 border-matrix-bright p-6 rounded-xl max-w-xl w-full text-center shadow-[0_0_40px_rgba(0,255,102,0.3)] relative font-mono space-y-6">
            
            <button onclick="closePillModal()" class="absolute top-3 right-3 text-xs text-matrix-dim hover:text-white">
                <i class="fa-solid fa-xmark text-lg"></i>
            </button>

            <div>
                <h2 class="font-crt text-3xl font-bold text-white mb-1">THE CHOICE OF PERCEPTION</h2>
                <p class="text-xs text-matrix-bright italic">"This is your last chance. After this, there is no turning back."</p>
            </div>

            <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                
                <!-- RED PILL -->
                <div class="p-4 border border-red-600/60 bg-red-950/20 rounded-lg flex flex-col items-center justify-between gap-3 hover:border-red-500 transition group">
                    <div class="w-12 h-12 rounded-full bg-red-600 shadow-[0_0_20px_#ff0000] flex items-center justify-center text-white text-xl font-bold group-hover:scale-110 transition">
                        💊
                    </div>
                    <div>
                        <h3 class="font-bold text-red-500 text-sm">THE RED PILL</h3>
                        <p class="text-[11px] opacity-80 text-red-200/80 mt-1">Unlock Matrix Overclock Glitch Mode, Red Rain Palette & Unlimited Terminal Protocols.</p>
                    </div>
                    <button onclick="takePill('red')" class="w-full py-2 bg-red-600 hover:bg-red-500 text-white font-bold text-xs rounded transition shadow-lg">
                        TAKE THE RED PILL
                    </button>
                </div>

                <!-- BLUE PILL -->
                <div class="p-4 border border-blue-600/60 bg-blue-950/20 rounded-lg flex flex-col items-center justify-between gap-3 hover:border-blue-500 transition group">
                    <div class="w-12 h-12 rounded-full bg-blue-500 shadow-[0_0_20px_#00aaff] flex items-center justify-center text-white text-xl font-bold group-hover:scale-110 transition">
                        💊
                    </div>
                    <div>
                        <h3 class="font-bold text-blue-400 text-sm">THE BLUE PILL</h3>
                        <p class="text-[11px] opacity-80 text-blue-200/80 mt-1">Return to the simulation. Standard matrix colors, peaceful operation mode.</p>
                    </div>
                    <button onclick="takePill('blue')" class="w-full py-2 bg-blue-600 hover:bg-blue-500 text-white font-bold text-xs rounded transition shadow-lg">
                        TAKE THE BLUE PILL
                    </button>
                </div>

            </div>

            <p class="text-[10px] opacity-50">Matrix Operator Verification System // Alwin John Shajan (@AlwinJCOde667)</p>
        </div>
    </div>

    <!-- TOAST NOTIFICATION CONTAINER -->
    <div id="toast-container" class="fixed bottom-4 right-4 z-50 flex flex-col gap-2 pointer-events-none"></div>

    <!-- ================= JAVASCRIPT LOGIC ================= -->
    <script>
        /* ================= 1. WEB AUDIO API SYNTHESIZER ================= */
        class SoundSynth {
            constructor() {
                this.ctx = null;
                this.enabled = false;
            }

            init() {
                if (!this.ctx) {
                    const AudioContext = window.AudioContext || window.webkitAudioContext;
                    this.ctx = new AudioContext();
                }
            }

            playClick() {
                if (!this.enabled || !this.ctx) return;
                try {
                    const osc = this.ctx.createOscillator();
                    const gain = this.ctx.createGain();
                    osc.type = 'sine';
                    osc.frequency.setValueAtTime(1200, this.ctx.currentTime);
                    osc.frequency.exponentialRampToValueAtTime(300, this.ctx.currentTime + 0.03);
                    gain.gain.setValueAtTime(0.05, this.ctx.currentTime);
                    gain.gain.exponentialRampToValueAtTime(0.001, this.ctx.currentTime + 0.03);
                    osc.connect(gain);
                    gain.connect(this.ctx.destination);
                    osc.start();
                    osc.stop(this.ctx.currentTime + 0.03);
                } catch(e) {}
            }

            playEnter() {
                if (!this.enabled || !this.ctx) return;
                try {
                    const osc = this.ctx.createOscillator();
                    const gain = this.ctx.createGain();
                    osc.type = 'triangle';
                    osc.frequency.setValueAtTime(440, this.ctx.currentTime);
                    osc.frequency.exponentialRampToValueAtTime(880, this.ctx.currentTime + 0.08);
                    gain.gain.setValueAtTime(0.08, this.ctx.currentTime);
                    gain.gain.exponentialRampToValueAtTime(0.001, this.ctx.currentTime + 0.08);
                    osc.connect(gain);
                    gain.connect(this.ctx.destination);
                    osc.start();
                    osc.stop(this.ctx.currentTime + 0.08);
                } catch(e) {}
            }

            playGlitch() {
                if (!this.enabled || !this.ctx) return;
                try {
                    const osc = this.ctx.createOscillator();
                    const gain = this.ctx.createGain();
                    osc.type = 'sawtooth';
                    osc.frequency.setValueAtTime(150, this.ctx.currentTime);
                    osc.frequency.setValueAtTime(800, this.ctx.currentTime + 0.05);
                    osc.frequency.setValueAtTime(100, this.ctx.currentTime + 0.1);
                    gain.gain.setValueAtTime(0.12, this.ctx.currentTime);
                    gain.gain.exponentialRampToValueAtTime(0.001, this.ctx.currentTime + 0.2);
                    osc.connect(gain);
                    gain.connect(this.ctx.destination);
                    osc.start();
                    osc.stop(this.ctx.currentTime + 0.2);
                } catch(e) {}
            }
        }

        const audioSynth = new SoundSynth();

        function toggleAudio() {
            audioSynth.init();
            audioSynth.enabled = !audioSynth.enabled;
            const btn = document.getElementById('audio-btn');
            const icon = document.getElementById('audio-icon');
            if (audioSynth.enabled) {
                icon.className = 'fa-solid fa-volume-high text-matrix-bright';
                showToast('Audio Synthesizer: ONLINE');
                audioSynth.playEnter();
            } else {
                icon.className = 'fa-solid fa-volume-xmark text-matrix-alert';
                showToast('Audio Synthesizer: MUTED');
            }
        }


        /* ================= 2. MATRIX RAIN CANVAS ENGINE ================= */
        const canvas = document.getElementById('matrix-canvas');
        const ctx = canvas.getContext('2d');

        let rainConfig = {
            fontSize: 16,
            speed: 25,
            color: '#00FF66',
            chars: 'アァカサタナハマヤャラワガザダバパイィキシチニヒミリヰギジヂビピウゥクスツヌフムユュルグズブヅプエェケセテネヘメレヱゲゼデベペオォコソトノホモヨョロヲゴゾドボポヴッン0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ@#$%'
        };

        let columns = 0;
        let drops = [];

        function resizeCanvas() {
            canvas.width = window.innerWidth;
            canvas.height = window.innerHeight;
            columns = Math.floor(canvas.width / rainConfig.fontSize);
            drops = [];
            for (let i = 0; i < columns; i++) {
                drops[i] = Math.floor(Math.random() * -100);
            }
        }

        function drawMatrixRain() {
            ctx.fillStyle = 'rgba(0, 5, 2, 0.08)';
            ctx.fillRect(0, 0, canvas.width, canvas.height);

            ctx.fillStyle = rainConfig.color;
            ctx.font = `${rainConfig.fontSize}px 'Fira Code', monospace`;

            for (let i = 0; i < drops.length; i++) {
                const char = rainConfig.chars.charAt(Math.floor(Math.random() * rainConfig.chars.length));
                const x = i * rainConfig.fontSize;
                const y = drops[i] * rainConfig.fontSize;

                // Leading character bright white glow
                if (Math.random() > 0.85) {
                    ctx.fillStyle = '#FFFFFF';
                } else {
                    ctx.fillStyle = rainConfig.color;
                }

                ctx.fillText(char, x, y);

                if (y > canvas.height && Math.random() > 0.975) {
                    drops[i] = 0;
                }
                drops[i]++;
            }
        }

        let rainInterval = setInterval(drawMatrixRain, rainConfig.speed);

        function updateRainConfig() {
            const speedInput = parseInt(document.getElementById('rain-speed').value);
            const fontInput = parseInt(document.getElementById('rain-font-size').value);
            rainConfig.speed = 55 - speedInput; // Invert speed
            rainConfig.fontSize = fontInput;
            clearInterval(rainInterval);
            rainInterval = setInterval(drawMatrixRain, rainConfig.speed);
            resizeCanvas();
        }

        function toggleRainControls() {
            const drawer = document.getElementById('rain-controls-drawer');
            drawer.classList.toggle('hidden');
        }

        function changeThemePreset(preset) {
            const body = document.body;
            body.classList.remove('red-pill-mode', 'blue-pill-mode');

            if (preset === 'red') {
                body.classList.add('red-pill-mode');
                rainConfig.color = '#FF0033';
            } else if (preset === 'blue') {
                body.classList.add('blue-pill-mode');
                rainConfig.color = '#00CCFF';
            } else if (preset === 'amber') {
                rainConfig.color = '#FFCC00';
            } else {
                rainConfig.color = '#00FF66';
            }
            showToast(`Matrix Theme Switched: ${preset.toUpperCase()}`);
        }

        window.addEventListener('resize', resizeCanvas);
        resizeCanvas();


        /* ================= 3. CLI TERMINAL COMMAND ENGINE ================= */
        const cliInput = document.getElementById('cli-input');
        const terminalOutput = document.getElementById('terminal-output');
        let commandHistory = [];
        let historyIndex = -1;

        const COMMANDS = {
            'help': `
<span class="text-matrix-bright font-bold">AVAILABLE SYSTEM COMMANDS:</span>
  <span class="text-white font-bold">whoami</span>        - Operator Identity & Profile
  <span class="text-white font-bold">skills</span>        - Technical Skillpack Breakdown
  <span class="text-white font-bold">projects</span>      - Matrix Mainframe Repositories
  <span class="text-white font-bold">stats</span>         - Real-time GitHub Simulation Metrics
  <span class="text-white font-bold">redpill</span>       - Initiate Overclock Glitch Protocol
  <span class="text-white font-bold">bluepill</span>      - Restore Standard Simulation
  <span class="text-white font-bold">cat matrix.log</span>- Decrypt Classified Operator Logs
  <span class="text-white font-bold">matrix-dodge</span>  - Launch Bullet-Time Construct Mini-Game
  <span class="text-white font-bold">copy-readme</span>   - Copy GitHub Profile README Markdown
  <span class="text-white font-bold">clear</span>         - Purge Terminal Screen
`,
            'whoami': `
<div class="p-3 border border-matrix-dim bg-black/60 rounded space-y-1">
  <p><span class="text-matrix-bright font-bold">IDENTIFIER:</span> Alwin John Shajan</p>
  <p><span class="text-matrix-bright font-bold">GITHUB:</span> @AlwinJCOde667</p>
  <p><span class="text-matrix-bright font-bold">ROLE:</span> Full-Stack Developer & Software Architect</p>
  <p><span class="text-matrix-bright font-bold">STATUS:</span> Operating from Nebuchadnezzar Terminal</p>
  <p><span class="text-matrix-bright font-bold">QUOTE:</span> "I know Kung Fu... and TypeScript."</p>
</div>
`,
            'skills': `
<div class="space-y-2">
  <p class="text-matrix-bright font-bold">[ SYSTEM ARSENAL & SKILLPACKS ]</p>
  <p>⚡ <span class="text-white font-bold">Languages:</span> Python, JavaScript (ES6+), TypeScript, C++, Bash</p>
  <p>⚙️ <span class="text-white font-bold">Frontend:</span> React, Next.js, HTML5, CSS3, TailwindCSS</p>
  <p>🛠️ <span class="text-white font-bold">Backend & DB:</span> Node.js, Express, PostgreSQL, MongoDB, REST APIs</p>
  <p>☁️ <span class="text-white font-bold">DevOps:</span> Docker, Git/GitHub, Linux System Admin, CI/CD</p>
</div>
`,
            'projects': `
<div class="space-y-2">
  <p class="text-matrix-bright font-bold">[ REPOSITORY ARCHIVES ]</p>
  <p>1. <span class="text-white font-bold">Matrix Terminal Studio</span> - Interactive web terminal & README generator.</p>
  <p>2. <span class="text-white font-bold">Full-Stack Cloud Gateway</span> - High-performance microservice router.</p>
  <p>3. <span class="text-white font-bold">Neural Code Analyzer</span> - Machine learning tool for automated code inspection.</p>
</div>
`,
            'stats': `
<div class="space-y-1 text-xs">
  <p class="text-matrix-bright font-bold">[ SIMULATION METRICS ]</p>
  <p>• GitHub Commits: <span class="text-white">Active Stream</span></p>
  <p>• System Uptime: <span class="text-white">99.98%</span></p>
  <p>• Matrix Access: <span class="text-white">Level 4 Clearance</span></p>
</div>
`,
            'cat matrix.log': `
<div class="p-2 border border-matrix-dim bg-black/80 rounded font-mono text-xs text-matrix-bright">
  [LOG 001] Neo connected to the construct.<br>
  [LOG 002] Operator @AlwinJCOde667 deployed new Matrix README engine.<br>
  [LOG 003] Glitch detected in sector 7. Trinity monitoring.<br>
  [STATUS] All systems fully operational inside the simulation.
</div>
`
        };

        cliInput.addEventListener('keydown', (e) => {
            audioSynth.playClick();

            if (e.key === 'Enter') {
                handleCommandSubmit();
            } else if (e.key === 'ArrowUp') {
                if (historyIndex > 0) {
                    historyIndex--;
                    cliInput.value = commandHistory[historyIndex] || '';
                }
            } else if (e.key === 'ArrowDown') {
                if (historyIndex < commandHistory.length - 1) {
                    historyIndex++;
                    cliInput.value = commandHistory[historyIndex] || '';
                } else {
                    historyIndex = commandHistory.length;
                    cliInput.value = '';
                }
            }
        });

        function handleCommandSubmit() {
            const cmd = cliInput.value.trim().toLowerCase();
            if (!cmd) return;

            audioSynth.playEnter();
            commandHistory.push(cmd);
            historyIndex = commandHistory.length;

            appendTerminalLine(`operator@zion:~$ ${cmd}`, 'text-white font-bold');

            cliInput.value = '';

            // Handle Command Logic
            if (cmd === 'clear') {
                clearTerminal();
                return;
            }

            if (cmd === 'redpill') {
                takePill('red');
                appendTerminalLine('--> RED PILL PROTOCOL INITIATED. MATRIX OVERCLOCK UNLOCKED.', 'text-red-500 font-bold');
                return;
            }

            if (cmd === 'bluepill') {
                takePill('blue');
                appendTerminalLine('--> BLUE PILL PROTOCOL INITIATED. RESTORING SIMULATION.', 'text-blue-400 font-bold');
                return;
            }

            if (cmd === 'matrix-dodge') {
                switchTab('construct');
                startGame();
                appendTerminalLine('--> INITIATING BULLET-TIME CONSTRUCT SIMULATOR...', 'text-matrix-bright font-bold');
                return;
            }

            if (cmd === 'copy-readme') {
                copyReadmeCode();
                appendTerminalLine('--> README MARKDOWN COPIED TO CLIPBOARD.', 'text-matrix-bright font-bold');
                return;
            }

            if (COMMANDS[cmd]) {
                appendTerminalLine(COMMANDS[cmd]);
            } else {
                audioSynth.playGlitch();
                appendTerminalLine(`Command not recognized: '${cmd}'. Type 'help' for available protocol commands.`, 'text-matrix-alert');
            }

            terminalOutput.scrollTop = terminalOutput.scrollHeight;
        }

        function runQuickCommand(cmd) {
            cliInput.value = cmd;
            handleCommandSubmit();
        }

        function appendTerminalLine(content, className = '') {
            const div = document.createElement('div');
            div.className = `my-1 ${className}`;
            div.innerHTML = content;
            terminalOutput.appendChild(div);
            terminalOutput.scrollTop = terminalOutput.scrollHeight;
        }

        function clearTerminal() {
            terminalOutput.innerHTML = '';
            appendTerminalLine('Terminal screen cleared.', 'text-matrix-dim italic');
        }


        /* ================= 4. BULLET TIME MINI-GAME ENGINE ================= */
        const gameCanvas = document.getElementById('game-canvas');
        const gctx = gameCanvas.getContext('2d');
        let gameRunning = false;
        let score = 0;
        let player = { x: 0, y: 0, radius: 12 };
        let bullets = [];
        let gameAnimFrame;

        function resizeGameCanvas() {
            if (!gameCanvas.parentElement) return;
            gameCanvas.width = gameCanvas.parentElement.clientWidth;
            gameCanvas.height = gameCanvas.parentElement.clientHeight;
            player.x = gameCanvas.width / 2;
            player.y = gameCanvas.height / 2;
        }

        window.addEventListener('resize', resizeGameCanvas);

        function startGame() {
            resizeGameCanvas();
            document.getElementById('game-start-overlay').classList.add('hidden');
            gameRunning = true;
            score = 0;
            bullets = [];
            document.getElementById('game-score').innerText = '0';
            gameLoop();
        }

        function restartGame() {
            startGame();
        }

        // Mouse & Touch Controls
        gameCanvas.addEventListener('mousemove', (e) => {
            const rect = gameCanvas.getBoundingClientRect();
            player.x = e.clientX - rect.left;
            player.y = e.clientY - rect.top;
        });

        gameCanvas.addEventListener('touchmove', (e) => {
            if (e.touches.length > 0) {
                const rect = gameCanvas.getBoundingClientRect();
                player.x = e.touches[0].clientX - rect.left;
                player.y = e.touches[0].clientY - rect.top;
            }
        });

        function spawnBullet() {
            if (Math.random() < 0.15) {
                const side = Math.floor(Math.random() * 4);
                let x, y, vx, vy;
                const speed = 2 + Math.random() * 4 + score * 0.01;

                if (side === 0) { x = Math.random() * gameCanvas.width; y = -10; vx = (Math.random() - 0.5) * 2; vy = speed; }
                else if (side === 1) { x = gameCanvas.width + 10; y = Math.random() * gameCanvas.height; vx = -speed; vy = (Math.random() - 0.5) * 2; }
                else if (side === 2) { x = Math.random() * gameCanvas.width; y = gameCanvas.height + 10; vx = (Math.random() - 0.5) * 2; vy = -speed; }
                else { x = -10; y = Math.random() * gameCanvas.height; vx = speed; vy = (Math.random() - 0.5) * 2; }

                bullets.push({ x, y, vx, vy, radius: 4 });
            }
        }

        function gameLoop() {
            if (!gameRunning) return;

            gctx.fillStyle = 'rgba(0, 5, 2, 0.25)';
            gctx.fillRect(0, 0, gameCanvas.width, gameCanvas.height);

            // Draw Player (Neo Avatar)
            gctx.beginPath();
            gctx.arc(player.x, player.y, player.radius, 0, Math.PI * 2);
            gctx.fillStyle = rainConfig.color;
            gctx.shadowBlur = 15;
            gctx.shadowColor = rainConfig.color;
            gctx.fill();
            gctx.shadowBlur = 0;

            // Spawn & Update Bullets
            spawnBullet();
            score++;
            document.getElementById('game-score').innerText = Math.floor(score / 10);

            for (let i = bullets.length - 1; i >= 0; i--) {
                let b = bullets[i];
                b.x += b.vx;
                b.y += b.vy;

                // Draw Bullet
                gctx.beginPath();
                gctx.arc(b.x, b.y, b.radius, 0, Math.PI * 2);
                gctx.fillStyle = '#FF0033';
                gctx.fill();

                // Collision Check
                const dist = Math.hypot(player.x - b.x, player.y - b.y);
                if (dist < player.radius + b.radius) {
                    gameOver();
                    return;
                }

                // Remove out of bounds bullets
                if (b.x < -20 || b.x > gameCanvas.width + 20 || b.y < -20 || b.y > gameCanvas.height + 20) {
                    bullets.splice(i, 1);
                }
            }

            gameAnimFrame = requestAnimationFrame(gameLoop);
        }

        function gameOver() {
            gameRunning = false;
            audioSynth.playGlitch();
            const overlay = document.getElementById('game-start-overlay');
            overlay.classList.remove('hidden');
            overlay.querySelector('h3').innerText = 'SIMULATION TERMINATED';
            overlay.querySelector('p').innerText = `Agent Sentinels intercepted your avatar. Final Score: ${Math.floor(score / 10)}`;
        }


        /* ================= 5. GITHUB PROFILE README GENERATOR ================= */
        const README_TEMPLATE = `<div align="center">

<!-- Matrix Digital Rain Banner Animation -->
<img src="https://media.giphy.com/media/v1.Y2lkPTc5MGI3NjExM3Z2eXZ0Z3FqZGN1Z3Vscnd2MWFrcmxidTRqZmJuZmptNXBnbXF4ZCZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/3rXna9rZacY8w/giphy.gif" width="100%" height="200px" style="object-fit: cover; border-radius: 8px;" alt="Matrix Rain" />

<br/><br/>

<!-- Dynamic Matrix Typing Header -->
<a href="https://github.com/AlwinJCOde667">
  <img src="https://readme-typing-svg.demolab.com?font=Fira+Code&weight=700&size=24&pause=1000&color=00FF66&center=true&vCenter=true&width=700&height=50&lines=Wake+up%2C+Alwin...;The+Matrix+has+you...;Follow+the+white+rabbit.+%F0%9F%90%87;Knock%2C+knock%2C+Alwin." alt="Matrix Typing SVG" />
</a>

<p align="center">
  <code>OPERATOR IDENT: AlwinJCOde667</code> &nbsp;|&nbsp; <code>SYSTEM: NEBUCHADNEZZAR</code> &nbsp;|&nbsp; <code>STATUS: UNPLUGGED ⚡</code>
</p>

---

### 💊 THE CHOICE OF PERCEPTION
*"This is your last chance. After this, there is no turning back."*

<table>
  <tr>
    <td width="50%" align="center">
      <h3>🔴 RED PILL</h3>
      <p><i>"You stay in Wonderland, and I show you how deep the rabbit hole goes."</i></p>
      <a href="#-system-diagnostics"><b>[ ACCESS MATRIX SYSTEM ]</b></a>
    </td>
    <td width="50%" align="center">
      <h3>🔵 BLUE PILL</h3>
      <p><i>"The story ends, you wake up in your bed and believe whatever you want to believe."</i></p>
      <a href="https://www.google.com"><b>[ RETURN TO SIMULATION ]</b></a>
    </td>
  </tr>
</table>

</div>

---

## 🎬 MOTION PICTURE: SYSTEM CONSTRUCT FEED

<div align="center">

<a href="https://www.youtube.com/watch?v=vKQi3bBA1y8" target="_blank">
  <img src="https://media.giphy.com/media/eIm6244q8n4vC/giphy.gif" width="85%" style="border: 2px solid #00FF66; border-radius: 10px;" alt="Neo Bullet Time Animation" />
  <br/>
  <sub>▶️ <b>[CLICK TO PLAY FULL-MOTION STREAM: MATRIX CONSTRUCT SIMULATION]</b></sub>
</a>

</div>

---

## 🖥️ SYSTEM DIAGNOSTICS

<details open>
<summary><b>📂 [OPERATOR PROFILE] <code>./whoami.sh</code></b></summary>

\`\`\`bash
[root@nebuchadnezzar ~]# cat /etc/identity.conf

IDENTIFIER   : Alwin John Shajan
HANDLE       : AlwinJCOde667
ROLE         : Software Engineer & System Architect
MISSION      : Decoding reality, building resilient architectures, and bending system boundaries.
LOCATION     : Operating inside the Construct
STATUS       : "I know Kung Fu... and full-stack development."
\`\`\`
</details>

<details>
<summary><b>📂 [NEURAL DOWNLOADS] <code>./current_operations.sys</code></b></summary>

<br>

* 🔭 **Active Mission:** Engineering high-performance web applications and cloud microservices.
* 🧠 **Neural Download:** Exploring AI agent architectures, system internals, and distributed databases.
* 🤝 **Zion Resistance:** Open to collaborating on groundbreaking open-source tools.
* 💬 **Encrypted Channels:** Ask me about **TypeScript**, **Python**, **React**, **Docker**, or **Linux Core**.

</details>

---

## 🛠️ THE ARSENAL & WEAPONRY

<div align="center">

### ⚡ Core Languages
![Python](https://img.shields.io/badge/Python-000000?style=for-the-badge&logo=python&logoColor=00FF66)
![JavaScript](https://img.shields.io/badge/JavaScript-000000?style=for-the-badge&logo=javascript&logoColor=00FF66)
![TypeScript](https://img.shields.io/badge/TypeScript-000000?style=for-the-badge&logo=typescript&logoColor=00FF66)
![C++](https://img.shields.io/badge/C++-000000?style=for-the-badge&logo=cplusplus&logoColor=00FF66)

### ⚙️ Frameworks & Interfaces
![React](https://img.shields.io/badge/React-00FF66?style=for-the-badge&logo=react&logoColor=000000)
![Node.js](https://img.shields.io/badge/Node.js-00FF66?style=for-the-badge&logo=nodedotjs&logoColor=000000)
![TailwindCSS](https://img.shields.io/badge/Tailwind-00FF66?style=for-the-badge&logo=tailwindcss&logoColor=000000)

</div>

---

## 📊 REAL-TIME SIMULATION METRICS

<div align="center">

<img src="https://github-readme-stats.vercel.app/api?username=AlwinJCOde667&show_icons=true&theme=matrix&title_color=00FF66&text_color=00FF66&icon_color=00FF66&bg_color=0D0208&border_color=00FF66&border_radius=8" width="48%" alt="GitHub Stats" />
<img src="https://github-readme-stats.vercel.app/api/top-langs/?username=AlwinJCOde667&layout=compact&theme=matrix&title_color=00FF66&text_color=00FF66&bg_color=0D0208&border_color=00FF66&border_radius=8" width="48%" alt="Top Languages" />

</div>

---

## 📟 TRANSMISSION TERMINAL

<div align="center">

[![LinkedIn](https://img.shields.io/badge/Signal_LinkedIn-000000?style=for-the-badge&logo=linkedin&logoColor=00FF66)](https://linkedin.com)
[![GitHub](https://img.shields.io/badge/GitHub_Profile-000000?style=for-the-badge&logo=github&logoColor=00FF66)](https://github.com/AlwinJCOde667)

<br/>

![Visitor Counter](https://komarev.com/ghpvc/?username=AlwinJCOde667&label=MATRIX%20INTRUSIONS&color=00FF66&style=flat-square)

</div>`;

        function initReadmeStudio() {
            document.getElementById('readme-raw-text').value = README_TEMPLATE;
            
            // Simple Rendered HTML simulation
            const renderedPreview = document.getElementById('readme-rendered-preview');
            renderedPreview.innerHTML = `
                <div class="space-y-4">
                    <img src="https://media.giphy.com/media/v1.Y2lkPTc5MGI3NjExM3Z2eXZ0Z3FqZGN1Z3Vscnd2MWFrcmxidTRqZmJuZmptNXBnbXF4ZCZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/3rXna9rZacY8w/giphy.gif" class="w-full h-32 object-cover rounded border border-matrix-dim">
                    <h1 class="text-xl font-bold text-matrix-bright text-center">Wake up, Alwin... The Matrix has you.</h1>
                    <div class="p-3 border border-matrix-dim bg-black/60 rounded text-center">
                        <p class="font-bold text-white mb-2">💊 THE CHOICE OF PERCEPTION</p>
                        <div class="grid grid-cols-2 gap-2 text-xs">
                            <div class="p-2 border border-red-500/50 bg-red-950/30 rounded">🔴 RED PILL</div>
                            <div class="p-2 border border-blue-500/50 bg-blue-950/30 rounded">🔵 BLUE PILL</div>
                        </div>
                    </div>
                    <img src="https://media.giphy.com/media/eIm6244q8n4vC/giphy.gif" class="w-full h-36 object-cover rounded border border-matrix-dim">
                </div>
            `;
        }

        function copyReadmeCode() {
            const rawText = document.getElementById('readme-raw-text');
            rawText.select();
            document.execCommand('copy');
            audioSynth.playEnter();
            showToast('README Markdown copied to clipboard!');
        }


        /* ================= 6. MODALS & UI TABS ================= */
        function switchTab(tabId) {
            audioSynth.playClick();
            document.querySelectorAll('.view-panel').forEach(el => el.classList.add('hidden'));
            document.querySelectorAll('.tab-btn').forEach(btn => {
                btn.classList.remove('bg-[var(--matrix-color)]', 'text-black');
                btn.classList.add('hover:bg-matrix-dim/40');
            });

            document.getElementById(`view-${tabId}`).classList.remove('hidden');
            const activeBtn = document.getElementById(`tab-btn-${tabId}`);
            activeBtn.classList.add('bg-[var(--matrix-color)]', 'text-black');

            if (tabId === 'construct') {
                resizeGameCanvas();
            }
        }

        function openPillModal() {
            audioSynth.playGlitch();
            document.getElementById('pill-modal').classList.remove('hidden');
        }

        function closePillModal() {
            document.getElementById('pill-modal').classList.add('hidden');
        }

        function takePill(pillColor) {
            closePillModal();
            audioSynth.playGlitch();

            document.body.classList.add('glitch-effect');
            setTimeout(() => document.body.classList.remove('glitch-effect'), 500);

            if (pillColor === 'red') {
                changeThemePreset('red');
                showToast('🔴 RED PILL CONSUMED: MATRIX GLITCH MODE UNLOCKED');
            } else {
                changeThemePreset('green');
                showToast('🔵 BLUE PILL CONSUMED: SIMULATION RESTORED');
            }
        }

        function showToast(msg) {
            const container = document.getElementById('toast-container');
            const toast = document.createElement('div');
            toast.className = 'px-4 py-2 bg-black border border-[var(--matrix-color)] text-[var(--matrix-color)] rounded shadow-2xl font-mono text-xs font-bold transition-all transform translate-y-2';
            toast.innerHTML = `<i class="fa-solid fa-terminal mr-2"></i> ${msg}`;
            container.appendChild(toast);

            setTimeout(() => {
                toast.classList.add('opacity-0', '-translate-y-2');
                setTimeout(() => toast.remove(), 300);
            }, 3000);
        }

        // Live Clock Ticker
        setInterval(() => {
            const timeEl = document.getElementById('system-time');
            if (timeEl) {
                const now = new Date();
                timeEl.innerText = now.toTimeString().split(' ')[0];
            }

            const cpuEl = document.getElementById('telemetry-cpu');
            if (cpuEl) {
                cpuEl.innerText = `${Math.floor(10 + Math.random() * 25)}%`;
            }
        }, 1000);

        // Initialize Studio
        window.onload = function() {
            initReadmeStudio();
            cliInput.focus();
        };
    </script>
</body>
</html>
