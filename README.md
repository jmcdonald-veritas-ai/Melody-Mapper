# Melody-Mapper 

<html lang="en" class="dark">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Song Structure & Section Marker - Music Class Analysis</title>

  <!-- Tailwind CSS CDN -->
  <script src="https://cdn.tailwindcss.com"></script>
  <script>
    tailwind.config = {
      darkMode: 'class',
      theme: {
        extend: {
          colors: {
            brand: {
              50: '#f0f9ff',
              100: '#e0f2fe',
              500: '#0284c7',
              600: '#0284c7',
              700: '#0369a1',
            }
          }
        }
      }
    };
  </script>

  <!-- FontAwesome Icons -->
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css" />

  <!-- Google Fonts: Inter & Outfit -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&family=Outfit:wght@500;600;700;800&display=swap" rel="stylesheet">

  <style>
    body {
      font-family: 'Inter', sans-serif;
    }
    h1, h2, h3, h4, .font-heading {
      font-family: 'Outfit', sans-serif;
    }
    
    /* Custom Scrollbar */
    ::-webkit-scrollbar {
      width: 8px;
      height: 8px;
    }
    ::-webkit-scrollbar-track {
      background: rgba(15, 23, 42, 0.6);
    }
    ::-webkit-scrollbar-thumb {
      background: rgba(100, 116, 139, 0.5);
      border-radius: 4px;
    }
    ::-webkit-scrollbar-thumb:hover {
      background: rgba(148, 163, 184, 0.8);
    }

    /* Print Styles for Printable Summary Report */
    @media print {
      body * {
        visibility: hidden;
      }
      #printableReportModal, #printableReportModal * {
        visibility: visible;
      }
      #printableReportModal {
        position: absolute;
        left: 0;
        top: 0;
        width: 100%;
        background: white !important;
        color: black !important;
      }
      .no-print {
        display: none !important;
      }
    }

    /* Interactive waveform slider pulse */
    @keyframes pulse-playhead {
      0%, 100% { opacity: 1; transform: scaleX(1); }
      50% { opacity: 0.7; transform: scaleX(1.3); }
    }
    .playhead-glow {
      box-shadow: 0 0 12px #38bdf8;
    }
  </style>
</head>
<body class="bg-slate-900 text-slate-100 min-h-screen flex flex-col transition-colors duration-200">

  <!-- Top Navigation Header -->
  <header class="bg-slate-800/90 backdrop-blur border-b border-slate-700/60 sticky top-0 z-30 px-4 lg:px-8 py-3">
    <div class="max-w-7xl mx-auto flex flex-wrap items-center justify-between gap-4">
      
      <!-- Brand Logo & Title -->
      <div class="flex items-center gap-3">
        <div class="w-10 h-10 rounded-xl bg-gradient-to-tr from-cyan-500 via-indigo-500 to-fuchsia-500 flex items-center justify-center text-white shadow-lg shadow-indigo-500/20">
          <i class="fa-solid fa-music text-xl"></i>
        </div>
        <div>
          <h1 class="text-xl font-bold bg-gradient-to-r from-cyan-400 via-indigo-300 to-fuchsia-400 bg-clip-text text-transparent leading-tight">
            MelodyMapper
          </h1>
          <p class="text-xs text-slate-400 font-medium">Song Structure & Section Marker for Music Class</p>
        </div>
      </div>

      <!-- Student & Project Meta Input -->
      <div class="flex flex-wrap items-center gap-3 bg-slate-900/70 border border-slate-700/80 rounded-xl px-3 py-1.5 text-sm">
        <div class="flex items-center gap-2 border-r border-slate-700 pr-3">
          <i class="fa-solid fa-user-graduate text-cyan-400 text-xs"></i>
          <input type="text" id="studentNameInput" placeholder="Student Name" value="YOUR NAME" class="bg-transparent text-slate-200 placeholder-slate-500 focus:outline-none w-28 sm:w-36 text-xs font-semibold" />
        </div>
        <div class="flex items-center gap-2">
          <i class="fa-solid fa-compact-disc text-fuchsia-400 text-xs"></i>
          <input type="text" id="songTitleInput" placeholder="Song Title" value="Neon Pop Adventure" class="bg-transparent text-slate-200 placeholder-slate-500 focus:outline-none w-32 sm:w-44 text-xs font-semibold" />
        </div>
      </div>

      <!-- Top Toolbar Action Buttons -->
      <div class="flex items-center gap-2 flex-wrap">
        <button onclick="openGlossaryModal()" class="px-3 py-1.5 rounded-lg bg-slate-700/70 hover:bg-slate-700 text-slate-200 text-xs font-medium transition flex items-center gap-1.5 border border-slate-600/50">
          <i class="fa-solid fa-book-open text-amber-400"></i>
          <span>Glossary</span>
        </button>

        <button onclick="openPrintReportModal()" class="px-3 py-1.5 rounded-lg bg-indigo-600/80 hover:bg-indigo-600 text-white text-xs font-medium transition flex items-center gap-1.5 shadow-md shadow-indigo-600/20">
          <i class="fa-solid fa-file-invoice"></i>
          <span>Summary Report</span>
        </button>

        <div class="relative inline-block text-left">
          <button onclick="toggleExportMenu()" class="px-3 py-1.5 rounded-lg bg-slate-700/70 hover:bg-slate-700 text-slate-200 text-xs font-medium transition flex items-center gap-1.5 border border-slate-600/50">
            <i class="fa-solid fa-floppy-disk text-emerald-400"></i>
            <span>Project</span>
            <i class="fa-solid fa-chevron-down text-[10px]"></i>
          </button>
          <div id="exportDropdown" class="hidden absolute right-0 mt-2 w-44 bg-slate-800 border border-slate-700 rounded-xl shadow-xl z-50 py-1 text-xs">
            <button onclick="exportProjectJSON()" class="w-full text-left px-4 py-2 hover:bg-slate-700 flex items-center gap-2 text-slate-200">
              <i class="fa-solid fa-download text-cyan-400"></i> Export JSON File
            </button>
            <label class="w-full text-left px-4 py-2 hover:bg-slate-700 flex items-center gap-2 text-slate-200 cursor-pointer">
              <i class="fa-solid fa-upload text-emerald-400"></i> Import JSON File
              <input type="file" id="importJsonInput" accept=".json" class="hidden" onchange="importProjectJSON(event)" />
            </label>
            <hr class="border-slate-700 my-1" />
            <button onclick="loadDemoAnalysis()" class="w-full text-left px-4 py-2 hover:bg-slate-700 flex items-center gap-2 text-amber-300">
              <i class="fa-solid fa-wand-magic-sparkles"></i> Load Demo Markings
            </button>
            <button onclick="clearAllSectionsConfirm()" class="w-full text-left px-4 py-2 hover:bg-slate-700 flex items-center gap-2 text-rose-400">
              <i class="fa-solid fa-trash-can"></i> Clear All Sections
            </button>
          </div>
        </div>

        <button onclick="toggleTheme()" id="themeToggleBtn" title="Toggle Light/Dark Theme" class="p-2 rounded-lg bg-slate-700/70 hover:bg-slate-700 text-slate-200 text-xs transition border border-slate-600/50">
          <i class="fa-solid fa-sun text-amber-400" id="themeIcon"></i>
        </button>
      </div>

    </div>
  </header>

  <!-- Main Container -->
  <main class="max-w-7xl mx-auto w-full px-4 lg:px-8 py-6 flex-1 space-y-6">

    <!-- AUDIO PLAYER & TRACK SELECTION DECK -->
    <section class="bg-slate-800/80 border border-slate-700/80 rounded-2xl p-4 sm:p-6 shadow-xl backdrop-blur relative overflow-hidden">
      
      <!-- Top Track Controls Header -->
      <div class="flex flex-col md:flex-row items-start md:items-center justify-between gap-4 mb-5 pb-4 border-b border-slate-700/70">
        
        <!-- Track Selection Dropdown & Custom Audio Upload -->
        <div class="flex flex-wrap items-center gap-3 w-full md:w-auto">
          <div class="flex items-center gap-2">
            <span class="text-xs font-semibold uppercase tracking-wider text-slate-400">Audio Track:</span>
            <select id="sampleTrackSelect" onchange="handleTrackSelectChange(this.value)" class="bg-slate-900 border border-slate-700 text-cyan-300 text-xs font-semibold rounded-xl px-3 py-2 focus:outline-none focus:border-cyan-500 transition">
              <option value="pop">🎵 Demo 1: Neon Pop Adventure (Synth Pop - 60s)</option>
              <option value="lofi">🎸 Demo 2: Sunset Lo-Fi Chill (Acoustic Keys - 50s)</option>
              <option value="funk">🎷 Demo 3: Funk & Groove Lab (Upbeat Bass - 55s)</option>
              <option value="custom">📁 Custom Audio File (Upload local MP3/WAV)</option>
            </select>
          </div>

          <!-- Hidden Custom File Input Triggered when select = custom -->
          <div id="customFileContainer" class="hidden">
            <label class="px-3 py-1.5 bg-cyan-600/80 hover:bg-cyan-600 text-white text-xs font-semibold rounded-xl cursor-pointer transition flex items-center gap-1.5 shadow-sm">
              <i class="fa-solid fa-file-audio"></i> Choose File
              <input type="file" id="audioFileInput" accept="audio/*" class="hidden" onchange="handleCustomAudioUpload(event)" />
            </label>
          </div>
        </div>

        <!-- Audio Active Status Badge & Active Loop Banner -->
        <div class="flex items-center gap-3">
          <div id="loopStatusBadge" class="hidden px-3 py-1 bg-amber-500/20 border border-amber-500/50 rounded-full text-amber-300 text-xs font-medium items-center gap-2 animate-pulse">
            <i class="fa-solid fa-rotate-right"></i>
            <span>Looping: <strong id="loopingSectionName">Section</strong></span>
            <button onclick="stopLoopingSection()" class="hover:text-white ml-1"><i class="fa-solid fa-xmark"></i></button>
          </div>

          <div class="text-right">
            <span id="currentTimeDisplay" class="font-mono text-2xl font-bold text-cyan-400">00:00.0</span>
            <span class="text-slate-500 font-mono text-sm">/</span>
            <span id="totalDurationDisplay" class="font-mono text-sm font-semibold text-slate-400">00:00.0</span>
          </div>
        </div>

      </div>

      <!-- Interactive Visual Timeline & Waveform Canvas -->
      <div class="relative mb-3 group">
        
        <!-- Waveform & Canvas Layer -->
        <div class="relative w-full h-28 bg-slate-950 rounded-xl overflow-hidden border border-slate-700/80 cursor-pointer shadow-inner" id="timelineContainer" onclick="handleTimelineClick(event)" onmousemove="handleTimelineHover(event)" onmouseleave="hideTimelineTooltip()">
          
          <!-- Rendered Waveform Canvas -->
          <canvas id="waveformCanvas" class="w-full h-full block opacity-70"></canvas>

          <!-- Overlay Layer for Marked Colored Sections -->
          <div id="timelineSectionsOverlay" class="absolute inset-0 pointer-events-none"></div>

          <!-- Interactive Playhead Line -->
          <div id="playheadLine" class="absolute top-0 bottom-0 w-1 bg-cyan-400 playhead-glow z-20 pointer-events-none transition-all duration-75" style="left: 0%;">
            <div class="w-3 h-3 bg-cyan-300 rounded-full -translate-x-[4px] -translate-y-1 shadow"></div>
          </div>

          <!-- Live Section Mark Draft Bounds Overlay -->
          <div id="draftSectionMarker" class="absolute top-0 bottom-0 bg-cyan-500/30 border-x-2 border-cyan-400 hidden z-10 pointer-events-none"></div>

          <!-- Hover Time Indicator Tooltip -->
          <div id="timelineHoverTooltip" class="hidden absolute top-2 bg-slate-900 border border-cyan-500/50 text-cyan-300 text-[10px] font-mono px-2 py-0.5 rounded shadow z-30 pointer-events-none -translate-x-1/2">
            00:00.0
          </div>
        </div>

      </div>

      <!-- VISUAL SECTION COLOR RIBBON STRIP (0% to 100%) -->
      <div class="space-y-1 mb-6">
        <div class="flex items-center justify-between text-[11px] text-slate-400 font-semibold px-1">
          <span>00:00</span>
          <span class="text-cyan-400 font-bold uppercase tracking-wider text-[10px]">Song Section Ribbon Map</span>
          <span id="ribbonDurationEnd">01:00</span>
        </div>
        <div id="sectionRibbonStrip" class="w-full h-4 bg-slate-950 border border-slate-700 rounded-lg flex overflow-hidden p-0.5 gap-0.5 shadow-inner">
          <div class="w-full h-full bg-slate-800 rounded text-[9px] text-slate-500 flex items-center justify-center font-medium">No sections marked yet</div>
        </div>
      </div>

      <!-- AUDIO PLAYBACK CONTROL TOOLBAR -->
      <div class="flex flex-wrap items-center justify-between gap-4 bg-slate-900/90 border border-slate-700/60 rounded-xl p-3">
        
        <!-- Main Play/Pause & Skip Buttons -->
        <div class="flex items-center gap-2">
          <button onclick="skipTime(-5)" title="Rewind 5s" class="p-2.5 rounded-xl bg-slate-800 hover:bg-slate-700 text-slate-300 transition border border-slate-700">
            <i class="fa-solid fa-rotate-left text-sm"></i>
          </button>

          <button id="playPauseBtn" onclick="togglePlayPause()" class="w-12 h-12 rounded-xl bg-gradient-to-r from-cyan-500 to-indigo-600 hover:from-cyan-400 hover:to-indigo-500 text-white flex items-center justify-center text-lg shadow-lg shadow-cyan-500/25 transition active:scale-95">
            <i class="fa-solid fa-play ml-0.5" id="playIcon"></i>
          </button>

          <button onclick="skipTime(5)" title="Forward 5s" class="p-2.5 rounded-xl bg-slate-800 hover:bg-slate-700 text-slate-300 transition border border-slate-700">
            <i class="fa-solid fa-rotate-right text-sm"></i>
          </button>
        </div>

        <!-- Speed Controls -->
        <div class="flex items-center gap-1.5 bg-slate-800/80 border border-slate-700 p-1 rounded-xl">
          <span class="text-[10px] font-bold uppercase tracking-wider text-slate-400 px-2">Speed:</span>
          <button onclick="setPlaybackSpeed(0.5)" class="speed-btn px-2 py-1 text-xs font-semibold rounded-lg text-slate-400 hover:text-white transition" data-speed="0.5">0.5x</button>
          <button onclick="setPlaybackSpeed(0.75)" class="speed-btn px-2 py-1 text-xs font-semibold rounded-lg text-slate-400 hover:text-white transition" data-speed="0.75">0.75x</button>
          <button onclick="setPlaybackSpeed(1.0)" class="speed-btn px-2 py-1 text-xs font-semibold rounded-lg bg-cyan-500 text-white transition" data-speed="1">1.0x</button>
          <button onclick="setPlaybackSpeed(1.25)" class="speed-btn px-2 py-1 text-xs font-semibold rounded-lg text-slate-400 hover:text-white transition" data-speed="1.25">1.25x</button>
          <button onclick="setPlaybackSpeed(1.5)" class="speed-btn px-2 py-1 text-xs font-semibold rounded-lg text-slate-400 hover:text-white transition" data-speed="1.5">1.5x</button>
        </div>

        <!-- Volume Slider & Mute Toggle -->
        <div class="flex items-center gap-2">
          <button onclick="toggleMute()" id="muteBtn" class="text-slate-400 hover:text-white text-sm p-1">
            <i class="fa-solid fa-volume-high" id="volumeIcon"></i>
          </button>
          <input type="range" id="volumeSlider" min="0" max="1" step="0.05" value="0.8" oninput="changeVolume(this.value)" class="w-20 sm:w-28 accent-cyan-400 cursor-pointer" />
        </div>

      </div>

    </section>

    <!-- LIVE SECTION MARKER CONTROLS & PALETTE PANEL -->
    <section class="grid grid-cols-1 lg:grid-cols-3 gap-6">
      
      <!-- Live Marking Interactive Box -->
      <div class="bg-slate-800/80 border border-slate-700/80 rounded-2xl p-5 shadow-xl backdrop-blur flex flex-col justify-between">
        <div>
          <div class="flex items-center justify-between mb-3">
            <h2 class="text-base font-bold text-white flex items-center gap-2">
              <i class="fa-solid fa-stopwatch text-cyan-400"></i>
              <span>Live Section Marker</span>
            </h2>
            <span class="text-xs px-2 py-0.5 rounded-full bg-cyan-500/10 text-cyan-400 font-medium border border-cyan-500/30">Step 1</span>
          </div>
          <p class="text-xs text-slate-400 mb-4 leading-relaxed">
            Listen to the audio. Click <strong>Start Section</strong> when a new section begins, and <strong>End Section</strong> when it finishes.
          </p>

          <!-- Current Time Callout -->
          <div class="bg-slate-900 border border-slate-700/80 rounded-xl p-3 text-center mb-4">
            <span class="text-[10px] text-slate-400 uppercase tracking-wider block font-semibold">Active Audio Time</span>
            <span id="liveTimeTracker" class="font-mono text-3xl font-extrabold text-white">00:00.0</span>
          </div>

          <!-- Marking Status Draft Box -->
          <div id="liveMarkStatus" class="bg-slate-900/60 border border-dashed border-slate-700 rounded-xl p-3 text-center text-xs text-slate-400 mb-4 min-h-[48px] flex items-center justify-center">
            Click <span class="text-emerald-400 font-semibold mx-1">"Start Section Here"</span> to start capturing timing.
          </div>
        </div>

        <!-- Live Capture Action Buttons -->
        <div class="grid grid-cols-2 gap-3">
          <button id="startMarkBtn" onclick="markStartHere()" class="py-3 px-4 rounded-xl bg-emerald-600 hover:bg-emerald-500 text-white font-semibold text-xs transition shadow-lg shadow-emerald-600/20 flex items-center justify-center gap-2 active:scale-95">
            <i class="fa-solid fa-flag"></i>
            <span>Start Section (IN)</span>
          </button>

          <button id="endMarkBtn" onclick="markEndHere()" disabled class="py-3 px-4 rounded-xl bg-slate-700 text-slate-500 font-semibold text-xs transition flex items-center justify-center gap-2 cursor-not-allowed">
            <i class="fa-solid fa-flag-checkered"></i>
            <span>End Section (OUT)</span>
          </button>
        </div>

        <!-- Manual Add Button Trigger -->
        <button onclick="openManualSectionModal()" class="w-full mt-3 py-2 bg-slate-900/80 hover:bg-slate-900 border border-slate-700 rounded-xl text-slate-300 text-xs font-medium transition flex items-center justify-center gap-2">
          <i class="fa-solid fa-pen-to-square text-cyan-400"></i>
          <span>Add Section Manually (Set Exact Times)</span>
        </button>
      </div>

      <!-- Quick Section Tag & Color Creator -->
      <div class="lg:col-span-2 bg-slate-800/80 border border-slate-700/80 rounded-2xl p-5 shadow-xl backdrop-blur flex flex-col justify-between">
        <div>
          <div class="flex items-center justify-between mb-3">
            <h2 class="text-base font-bold text-white flex items-center gap-2">
              <i class="fa-solid fa-palette text-fuchsia-400"></i>
              <span>Section Identity & Preset Palette</span>
            </h2>
            <span class="text-xs px-2 py-0.5 rounded-full bg-fuchsia-500/10 text-fuchsia-400 font-medium border border-fuchsia-500/30">Step 2</span>
          </div>

          <!-- Form Tag Selection Grid -->
          <div class="mb-4">
            <label class="block text-xs font-semibold text-slate-300 mb-2">Select Section Type / Form Tag:</label>
            <div class="flex flex-wrap gap-2" id="presetTagChips">
              <!-- Dynamically rendered or built preset chips -->
            </div>
          </div>

          <!-- Color Palette Picker -->
          <div class="mb-4">
            <div class="flex items-center justify-between mb-2">
              <label class="text-xs font-semibold text-slate-300">Choose Section Color:</label>
              <div class="flex items-center gap-2">
                <span class="text-[11px] text-slate-400">Custom Hex:</span>
                <input type="color" id="customColorPicker" value="#8b5cf6" onchange="selectCustomColor(this.value)" class="w-7 h-7 rounded bg-transparent cursor-pointer border border-slate-600" />
              </div>
            </div>
            
            <div class="flex flex-wrap gap-2.5" id="colorPaletteChips">
              <!-- Color swatches generated via JavaScript -->
            </div>
          </div>
        </div>

        <!-- Active Selected Tag Preview Strip -->
        <div class="bg-slate-900/90 border border-slate-700 rounded-xl p-3 flex flex-wrap items-center justify-between gap-3">
          <div class="flex items-center gap-3">
            <div id="activeColorBadge" class="w-6 h-6 rounded-lg shadow-md bg-purple-500"></div>
            <div>
              <span class="text-[10px] uppercase font-bold text-slate-400 block">Selected Name:</span>
              <span id="activeTagName" class="text-sm font-extrabold text-white">Verse 1</span>
            </div>
          </div>

          <div class="text-xs text-slate-400">
            Timing: <span id="activeTimingPreview" class="font-mono text-cyan-300 font-semibold">00:00.0 - 00:00.0</span>
          </div>

          <button id="confirmAddSectionBtn" onclick="saveCapturedSection()" disabled class="px-5 py-2.5 rounded-xl bg-gradient-to-r from-cyan-500 to-indigo-600 hover:from-cyan-400 hover:to-indigo-500 text-white font-bold text-xs transition shadow-lg shadow-cyan-500/20 disabled:opacity-50 disabled:cursor-not-allowed flex items-center gap-2">
            <i class="fa-solid fa-plus-circle"></i> Save Section
          </button>
        </div>

      </div>

    </section>

    <!-- SONG STRUCTURE AUTOMATIC FORM BANNER -->
    <section class="bg-slate-800/80 border border-slate-700/80 rounded-2xl p-4 sm:p-5 shadow-xl backdrop-blur">
      <div class="flex flex-col sm:flex-row items-start sm:items-center justify-between gap-3 mb-3">
        <div>
          <h2 class="text-sm font-bold text-white flex items-center gap-2">
            <i class="fa-solid fa-diagram-project text-amber-400"></i>
            <span>Song Form Flow & Visual Map</span>
          </h2>
          <p class="text-xs text-slate-400">Automatic timeline-ordered structure of your marked sections.</p>
        </div>
        
        <div class="flex items-center gap-2 text-xs">
          <span class="text-slate-400">Coverage:</span>
          <span id="coveragePercentage" class="font-mono font-bold text-emerald-400 bg-emerald-500/10 px-2.5 py-0.5 rounded-full border border-emerald-500/30">0%</span>
        </div>
      </div>

      <!-- Form Flow Sequence Badges -->
      <div id="songFormChain" class="flex items-center gap-2 overflow-x-auto py-2 px-1 min-h-[50px] scrollbar-thin">
        <div class="text-xs text-slate-500 italic">No sections marked yet. Mark live or add sections above to build the song form!</div>
      </div>
    </section>

    <!-- STUDENT ANALYSIS & ANNOTATION CARDS WORKSPACE -->
    <section class="bg-slate-800/80 border border-slate-700/80 rounded-2xl p-5 shadow-xl backdrop-blur">
      
      <!-- Section Header Controls -->
      <div class="flex flex-col sm:flex-row items-start sm:items-center justify-between gap-4 mb-6 pb-4 border-b border-slate-700/70">
        <div>
          <h2 class="text-lg font-bold text-white flex items-center gap-2">
            <i class="fa-solid fa-list-check text-cyan-400"></i>
            <span>Section Analysis Breakdown & Notes</span>
          </h2>
          <p class="text-xs text-slate-400">Examine musical elements (instruments, dynamics, texture, rhythm) and add custom observations for each section.</p>
        </div>

        <div class="flex items-center gap-2">
          <span class="text-xs font-semibold text-slate-400">Sort By:</span>
          <select id="sectionSortSelect" onchange="renderSectionsList()" class="bg-slate-900 border border-slate-700 text-slate-200 text-xs rounded-xl px-3 py-1.5 focus:outline-none">
            <option value="time">Start Time (Chronological)</option>
            <option value="name">Section Name</option>
            <option value="duration">Duration (Longest First)</option>
          </select>
        </div>
      </div>

      <!-- Empty State / Cards List Container -->
      <div id="sectionsListContainer" class="space-y-4">
        <!-- Rendered Section Cards -->
      </div>

    </section>

  </main>

  <!-- Footer -->
  <footer class="bg-slate-950 border-t border-slate-800 text-slate-500 py-6 text-center text-xs px-4 mt-8">
    <div class="max-w-7xl mx-auto flex flex-col sm:flex-row items-center justify-between gap-3">
      <p>© 2026 MelodyMapper — Interactive Music Education & Song Analysis Tool</p>
      <p class="flex items-center gap-2">
        <span class="text-cyan-400"><i class="fa-solid fa-graduation-cap"></i> Designed for Middle & High School Music Classes</span>
      </p>
    </div>
  </footer>


  <!-- MODALS SECTION -->

  <!-- PRINTABLE / EXPORTABLE STUDENT ANALYSIS SUMMARY REPORT MODAL -->
  <div id="printReportModal" class="fixed inset-0 bg-black/80 backdrop-blur-sm z-50 hidden flex items-center justify-center p-4 overflow-y-auto">
    <div class="bg-slate-800 border border-slate-700 w-full max-w-4xl rounded-2xl shadow-2xl overflow-hidden my-8 max-h-[90vh] flex flex-col">
      
      <!-- Modal Navigation Bar (No-Print) -->
      <div class="no-print bg-slate-900 px-6 py-4 border-b border-slate-700 flex items-center justify-between">
        <h3 class="text-base font-bold text-white flex items-center gap-2">
          <i class="fa-solid fa-file-invoice text-indigo-400"></i>
          <span>Student Song Structure Analysis Report</span>
        </h3>
        <div class="flex items-center gap-2">
          <button onclick="triggerWindowPrint()" class="px-4 py-2 bg-indigo-600 hover:bg-indigo-500 text-white font-semibold text-xs rounded-xl transition flex items-center gap-1.5 shadow-md">
            <i class="fa-solid fa-print"></i> Print / Save as PDF
          </button>
          <button onclick="closePrintReportModal()" class="p-2 text-slate-400 hover:text-white transition">
            <i class="fa-solid fa-xmark text-lg"></i>
          </button>
        </div>
      </div>

      <!-- Printable Report Body -->
      <div id="printableReportModal" class="p-8 bg-slate-900 text-slate-100 overflow-y-auto space-y-6 flex-1">
        
        <!-- Header Info Card -->
        <div class="border-b-2 border-indigo-500/50 pb-6 flex flex-col sm:flex-row justify-between items-start sm:items-center gap-4">
          <div>
            <span class="text-xs uppercase font-bold text-indigo-400 tracking-widest block">Music Class Section Analysis</span>
            <h2 id="reportSongTitle" class="text-2xl font-extrabold text-white mt-1">Neon Pop Adventure</h2>
            <p id="reportStudentName" class="text-sm font-semibold text-slate-300 mt-1">Student: Alex Rivera</p>
          </div>
          <div class="text-right text-xs text-slate-400 space-y-1">
            <p><strong>Date:</strong> <span id="reportDate">Oct 2026</span></p>
            <p><strong>Total Sections Analyzed:</strong> <span id="reportSectionCount" class="text-cyan-400 font-bold">0</span></p>
            <p><strong>Track Duration:</strong> <span id="reportTotalDuration" class="font-mono">01:00.0</span></p>
          </div>
        </div>

        <!-- Song Structure Chain Display -->
        <div>
          <h4 class="text-xs font-bold uppercase tracking-wider text-slate-400 mb-2">Song Form Map Sequence</h4>
          <div id="reportFormChain" class="flex flex-wrap items-center gap-2 p-3 bg-slate-950 border border-slate-800 rounded-xl">
            <!-- Rendered badges -->
          </div>
        </div>

        <!-- Color Ribbon Timeline Visual -->
        <div>
          <h4 class="text-xs font-bold uppercase tracking-wider text-slate-400 mb-2">Visual Section Color Timeline</h4>
          <div id="reportRibbonStrip" class="w-full h-6 bg-slate-950 border border-slate-800 rounded-lg flex overflow-hidden p-0.5 gap-0.5">
            <!-- Rendered ribbon strip -->
          </div>
        </div>

        <!-- Complete Section Breakdown Table -->
        <div>
          <h4 class="text-xs font-bold uppercase tracking-wider text-slate-400 mb-3">Detailed Musical Section Breakdown</h4>
          <div class="overflow-x-auto border border-slate-800 rounded-xl">
            <table class="w-full text-left text-xs border-collapse">
              <thead class="bg-slate-950 text-slate-300 font-semibold border-b border-slate-800">
                <tr>
                  <th class="p-3">Section</th>
                  <th class="p-3">Time Range</th>
                  <th class="p-3">Duration</th>
                  <th class="p-3">Instruments</th>
                  <th class="p-3">Dynamics & Texture</th>
                  <th class="p-3">Observations / Notes</th>
                </tr>
              </thead>
              <tbody id="reportTableBody" class="divide-y divide-slate-800 text-slate-300">
                <!-- Table rows rendered dynamically -->
              </tbody>
            </table>
          </div>
        </div>

        <!-- Teacher Notes Signoff Box -->
        <div class="border-t border-slate-800 pt-6 mt-8 flex flex-col sm:flex-row justify-between items-end gap-4">
          <div class="text-xs text-slate-500">
            Generated with MelodyMapper Interactive Music Analysis Platform
          </div>
          <div class="w-48 text-center border-t border-slate-700 pt-1 text-xs text-slate-400">
            Teacher Grade / Feedback
          </div>
        </div>

      </div>

    </div>
  </div>


  <!-- MUSICAL GLOSSARY & CLASSROOM PROMPTS MODAL -->
  <div id="glossaryModal" class="fixed inset-0 bg-black/80 backdrop-blur-sm z-50 hidden flex items-center justify-center p-4">
    <div class="bg-slate-800 border border-slate-700 w-full max-w-3xl rounded-2xl shadow-2xl overflow-hidden max-h-[85vh] flex flex-col">
      
      <div class="bg-slate-900 px-6 py-4 border-b border-slate-700 flex items-center justify-between">
        <h3 class="text-base font-bold text-white flex items-center gap-2">
          <i class="fa-solid fa-book-open text-amber-400"></i>
          <span>Music Theory Glossary & Analysis Prompts</span>
        </h3>
        <button onclick="closeGlossaryModal()" class="p-2 text-slate-400 hover:text-white transition">
          <i class="fa-solid fa-xmark text-lg"></i>
        </button>
      </div>

      <div class="p-6 overflow-y-auto space-y-6 text-xs leading-relaxed text-slate-300">
        
        <!-- Section Forms -->
        <div>
          <h4 class="text-sm font-bold text-cyan-400 mb-2 border-b border-slate-700 pb-1">Common Song Structure Forms</h4>
          <div class="grid grid-cols-1 md:grid-cols-2 gap-3">
            <div class="bg-slate-900/80 p-3 rounded-xl border border-slate-700">
              <strong class="text-white block mb-1">Verse-Chorus Form (ABABCB)</strong>
              The most popular modern song form. Features alternating Verses (new lyrics) and Choruses (repeated main hook/theme), often with a Bridge in the second half.
            </div>
            <div class="bg-slate-900/80 p-3 rounded-xl border border-slate-700">
              <strong class="text-white block mb-1">AABA Form (32-Bar)</strong>
              Common in Jazz and Classic Pop. Section A provides the main melody, section B offers a contrasting key/mood, then section A returns.
            </div>
            <div class="bg-slate-900/80 p-3 rounded-xl border border-slate-700">
              <strong class="text-white block mb-1">Strophic Form (AAA)</strong>
              Repeats the same musical material for every section/verse, with changing lyrics (popular in folk songs and ballads).
            </div>
            <div class="bg-slate-900/80 p-3 rounded-xl border border-slate-700">
              <strong class="text-white block mb-1">Rondo Form (ABACADA)</strong>
              Features a recurring principal theme (A) alternated with contrasting sections (B, C, D).
            </div>
          </div>
        </div>

        <!-- Musical Elements Definitions -->
        <div>
          <h4 class="text-sm font-bold text-fuchsia-400 mb-2 border-b border-slate-700 pb-1">Musical Elements Guide</h4>
          <div class="space-y-2">
            <p><strong class="text-slate-100">Dynamics:</strong> The volume of the sound in music (e.g., <em>pianissimo</em> [very soft] to <em>fortissimo</em> [very loud], crescendo [getting louder]).</p>
            <p><strong class="text-slate-100">Timbre / Tone Color:</strong> The unique sound quality or instrument voice (e.g., bright synth vs. warm acoustic guitar).</p>
            <p><strong class="text-slate-100">Texture:</strong> How layers of sound are combined (e.g., <em>Monophonic</em> = 1 melody line; <em>Homophonic</em> = melody + harmony chords; <em>Polyphonic</em> = multiple independent melodies).</p>
            <p><strong class="text-slate-100">Bridge:</strong> A transitional section introducing new harmonic and melodic contrast before returning to the chorus.</p>
          </div>
        </div>

        <!-- Guided Analysis Questions -->
        <div class="bg-indigo-950/40 border border-indigo-500/30 rounded-xl p-4">
          <h4 class="text-xs font-bold text-indigo-300 uppercase tracking-wider mb-2">💡 Guided Analysis Questions for Students</h4>
          <ul class="list-disc list-inside space-y-1 text-slate-300">
            <li>What instrumentation or beat changes signal the start of a new section?</li>
            <li>How does the volume (dynamics) build as the song progresses into the Chorus?</li>
            <li>Does the energy or tempo feel different during the Bridge compared to the Verse?</li>
            <li>Which section contains the primary emotional or musical climax of the song?</li>
          </ul>
        </div>

      </div>

    </div>
  </div>


  <!-- MANUAL SECTION MODAL / EDIT MODAL -->
  <div id="manualSectionModal" class="fixed inset-0 bg-black/80 backdrop-blur-sm z-50 hidden flex items-center justify-center p-4">
    <div class="bg-slate-800 border border-slate-700 w-full max-w-md rounded-2xl shadow-2xl p-6">
      
      <div class="flex items-center justify-between mb-4">
        <h3 id="modalSectionTitle" class="text-base font-bold text-white flex items-center gap-2">
          <i class="fa-solid fa-pen-to-square text-cyan-400"></i>
          <span>Add / Edit Section</span>
        </h3>
        <button onclick="closeManualSectionModal()" class="text-slate-400 hover:text-white"><i class="fa-solid fa-xmark text-lg"></i></button>
      </div>

      <div class="space-y-4 text-xs">
        <div>
          <label class="block font-semibold text-slate-300 mb-1">Section Name / Tag:</label>
          <input type="text" id="manualNameInput" placeholder="e.g. Verse 1" class="w-full bg-slate-900 border border-slate-700 rounded-xl p-2.5 text-slate-100 focus:outline-none focus:border-cyan-500" />
        </div>

        <div class="grid grid-cols-2 gap-3">
          <div>
            <label class="block font-semibold text-slate-300 mb-1">Start Time (sec):</label>
            <input type="number" id="manualStartInput" step="0.1" min="0" placeholder="0.0" class="w-full bg-slate-900 border border-slate-700 rounded-xl p-2.5 text-slate-100 focus:outline-none focus:border-cyan-500 font-mono" />
          </div>

          <div>
            <label class="block font-semibold text-slate-300 mb-1">End Time (sec):</label>
            <input type="number" id="manualEndInput" step="0.1" min="0" placeholder="10.0" class="w-full bg-slate-900 border border-slate-700 rounded-xl p-2.5 text-slate-100 focus:outline-none focus:border-cyan-500 font-mono" />
          </div>
        </div>

        <div>
          <label class="block font-semibold text-slate-300 mb-1">Color:</label>
          <input type="color" id="manualColorInput" value="#3b82f6" class="w-full h-10 bg-slate-900 border border-slate-700 rounded-xl p-1 cursor-pointer" />
        </div>
      </div>

      <div class="flex justify-end gap-2 mt-6">
        <button onclick="closeManualSectionModal()" class="px-4 py-2 bg-slate-700 hover:bg-slate-600 text-slate-200 font-semibold rounded-xl text-xs">Cancel</button>
        <button onclick="saveManualSectionModal()" class="px-5 py-2 bg-cyan-600 hover:bg-cyan-500 text-white font-bold rounded-xl text-xs shadow-md shadow-cyan-600/20">Save Section</button>
      </div>

    </div>
  </div>


  <!-- Toast Notification Container -->
  <div id="toastContainer" class="fixed bottom-5 right-5 z-50 flex flex-col gap-2 pointer-events-none"></div>


  <!-- JAVASCRIPT LOGIC -->
  <script>
    
    // --- APP STATE VARIABLES ---
    let audioContext = null;
    let audioElement = new Audio();
    audioElement.crossOrigin = "anonymous";

    let currentAudioBuffer = null;
    let isPlaying = false;
    let currentSpeed = 1.0;
    let isMuted = false;
    
    // Looping section bounds
    let activeLoopSectionId = null;

    // Live Marking State
    let draftStartTime = null;
    let draftEndTime = null;

    // Active preset choices for new section
    let activeSelectedTag = "Verse 1";
    let activeSelectedColor = "#8b5cf6"; // Violet default

    // Sections Array
    // Section Structure: { id, name, startTime, endTime, color, instruments: [], dynamics: '', texture: '', rhythm: '', notes: '' }
    let sectionsArray = [];
    let editingSectionId = null;

    // Presets definitions
    const PRESET_TAGS = [
      "Intro", "Verse 1", "Verse 2", "Pre-Chorus", "Chorus 1", "Chorus 2", 
      "Bridge", "Guitar Solo", "Interlude", "Drop", "Outro", "Custom"
    ];

    const PRESET_COLORS = [
      { name: "Crimson Verse", hex: "#ef4444" },
      { name: "Golden Chorus", hex: "#f59e0b" },
      { name: "Teal Bridge", hex: "#10b981" },
      { name: "Violet Outro", hex: "#8b5cf6" },
      { name: "Lime Intro", hex: "#84cc16" },
      { name: "Amber Solo", hex: "#d97706" },
      { name: "Cyan Drop", hex: "#06b6d4" },
      { name: "Rose Refrain", hex: "#f43f5e" },
      { name: "Indigo Motif", hex: "#6366f1" }
    ];

    const INSTRUMENT_OPTIONS = [
      "Drums/Beat", "Bass", "Acoustic Guitar", "Electric Guitar", "Piano/Keys", 
      "Synthesizer", "Lead Vocals", "Backing Vocals", "Strings", "Brass", "SFX"
    ];

    const DYNAMICS_OPTIONS = [
      "pp (Very Soft)", "p (Soft)", "mp (Medium Soft)", "mf (Medium Loud)", "f (Loud)", "ff (Very Loud)", "Crescendo ↗", "Decrescendo ↘"
    ];

    const TEXTURE_OPTIONS = [
      "Monophonic (1 Melody)", "Homophonic (Melody + Chords)", "Polyphonic (Multi-melodies)", "Sparse / Thin", "Dense / Full Layers"
    ];

    const RHYTHM_OPTIONS = [
      "Fast / Energetic", "Slow / Calm", "Driving Beat", "Syncopated", "Half-Time", "Rubato / Free"
    ];



    // Generate Audio Buffer programmatically using Web Audio API so it plays offline reliably!
    async function generateAudioTrack(type) {
      const sampleRate = 44100;
      let duration = 60; // seconds
      if (type === 'pop') duration = 60;
      if (type === 'lofi') duration = 50;
      if (type === 'funk') duration = 55;

      const offlineCtx = new OfflineAudioContext(2, sampleRate * duration, sampleRate);

      // Simple Synthesizer Music Generator
      const bpm = type === 'pop' ? 120 : (type === 'lofi' ? 85 : 110);
      const secondsPerBeat = 60 / bpm;

      // Master Gain
      const masterGain = offlineCtx.createGain();
      masterGain.gain.setValueAtTime(0.7, 0);
      masterGain.connect(offlineCtx.destination);

      // Synthesize Drums & Melody
      for (let t = 0; t < duration; t += secondsPerBeat) {
        let beatNum = Math.floor(t / secondsPerBeat) % 4;

        // Kick Drum on beats 0 & 2
        if (beatNum === 0 || beatNum === 2) {
          const osc = offlineCtx.createOscillator();
          const gain = offlineCtx.createGain();
          osc.frequency.setValueAtTime(120, t);
          osc.frequency.exponentialRampToValueAtTime(30, t + 0.1);
          gain.gain.setValueAtTime(0.8, t);
          gain.gain.exponentialRampToValueAtTime(0.01, t + 0.15);
          osc.connect(gain);
          gain.connect(masterGain);
          osc.start(t);
          osc.stop(t + 0.15);
        }

        // Snare Drum on beats 1 & 3
        if (beatNum === 1 || beatNum === 3) {
          const osc = offlineCtx.createOscillator();
          const gain = offlineCtx.createGain();
          osc.type = 'triangle';
          osc.frequency.setValueAtTime(220, t);
          osc.frequency.exponentialRampToValueAtTime(80, t + 0.1);
          gain.gain.setValueAtTime(0.5, t);
          gain.gain.exponentialRampToValueAtTime(0.01, t + 0.12);
          osc.connect(gain);
          gain.connect(masterGain);
          osc.start(t);
          osc.stop(t + 0.12);
        }
      }

      // Chord Progression Harmony
      // Scale Frequencies (C Major / A Minor)
      const chordsPop = [
        [261.63, 329.63, 392.00], // C Maj
        [220.00, 261.63, 329.63], // A Min
        [174.61, 220.00, 261.63], // F Maj
        [196.00, 246.94, 293.66]  // G Maj
      ];

      for (let t = 0; t < duration; t += secondsPerBeat * 4) {
        let chordIndex = Math.floor(t / (secondsPerBeat * 4)) % 4;
        let chordFreqs = chordsPop[chordIndex];

        chordFreqs.forEach(freq => {
          const osc = offlineCtx.createOscillator();
          const gain = offlineCtx.createGain();
          osc.type = type === 'lofi' ? 'sine' : 'sawtooth';
          osc.frequency.setValueAtTime(freq, t);
          
          // Filter soft synth
          const filter = offlineCtx.createBiquadFilter();
          filter.type = 'lowpass';
          filter.frequency.setValueAtTime(type === 'lofi' ? 600 : 1200, t);

          gain.gain.setValueAtTime(0.15, t);
          gain.gain.exponentialRampToValueAtTime(0.01, t + (secondsPerBeat * 3.8));

          osc.connect(filter);
          filter.connect(gain);
          gain.connect(masterGain);

          osc.start(t);
          osc.stop(t + (secondsPerBeat * 3.9));
        });
      }

      // Melodic Lead Synth
      const leadNotes = [523.25, 587.33, 659.25, 698.46, 783.99, 880.00];
      for (let t = 8; t < duration - 5; t += secondsPerBeat) {
        if (Math.random() > 0.3) {
          const note = leadNotes[Math.floor(Math.random() * leadNotes.length)];
          const osc = offlineCtx.createOscillator();
          const gain = offlineCtx.createGain();
          osc.type = 'square';
          osc.frequency.setValueAtTime(note, t);

          gain.gain.setValueAtTime(0.1, t);
          gain.gain.exponentialRampToValueAtTime(0.001, t + (secondsPerBeat * 0.8));

          osc.connect(gain);
          gain.connect(masterGain);

          osc.start(t);
          osc.stop(t + (secondsPerBeat * 0.85));
        }
      }

      const renderedBuffer = await offlineCtx.startRendering();
      return renderedBuffer;
    }



    // Convert AudioBuffer to downloadable / playable WAV Blob
    function audioBufferToWav(buffer) {
      let numChannels = buffer.numberOfChannels;
      let sampleRate = buffer.sampleRate;
      let format = 1; // PCM
      let bitDepth = 16;
      let bytesPerSample = bitDepth / 8;
      let blockAlign = numChannels * bytesPerSample;
      let count = buffer.length;
      let dataSize = count * blockAlign;
      let bufferSize = 44 + dataSize;
      let arrayBuffer = new ArrayBuffer(bufferSize);
      let view = new DataView(arrayBuffer);

      function writeString(view, offset, string) {
        for (let i = 0; i < string.length; i++) {
          view.setUint8(offset + i, string.charCodeAt(i));
        }
      }

      writeString(view, 0, 'RIFF');
      view.setUint32(4, 36 + dataSize, true);
      writeString(view, 8, 'WAVE');
      writeString(view, 12, 'fmt ');
      view.setUint32(16, 16, true);
      view.setUint16(20, format, true);
      view.setUint16(22, numChannels, true);
      view.setUint32(24, sampleRate, true);
      view.setUint32(28, sampleRate * blockAlign, true);
      view.setUint16(32, blockAlign, true);
      view.setUint16(34, bitDepth, true);
      writeString(view, 36, 'data');
      view.setUint32(40, dataSize, true);

      let offset = 44;
      let channels = [];
      for (let i = 0; i < numChannels; i++) {
        channels.push(buffer.getChannelData(i));
      }

      for (let i = 0; i < count; i++) {
        for (let ch = 0; ch < numChannels; ch++) {
          let sample = Math.max(-1, Math.min(1, channels[ch][i]));
          sample = sample < 0 ? sample * 0x8000 : sample * 0x7FFF;
          view.setInt16(offset, sample, true);
          offset += 2;
        }
      }

      return new Blob([arrayBuffer], { type: 'audio/wav' });
    }



    function drawWaveform(buffer) {
      const canvas = document.getElementById('waveformCanvas');
      const ctx = canvas.getContext('2d');
      const width = canvas.width = canvas.parentElement.clientWidth;
      const height = canvas.height = canvas.parentElement.clientHeight;

      ctx.clearRect(0, 0, width, height);

      if (!buffer) return;

      const data = buffer.getChannelData(0);
      const step = Math.ceil(data.length / width);
      const amp = height / 2;

      ctx.fillStyle = '#38bdf8'; // Cyan waveform color

      for (let i = 0; i < width; i++) {
        let min = 1.0;
        let max = -1.0;
        for (let j = 0; j < step; j++) {
          const datum = data[(i * step) + j];
          if (datum < min) min = datum;
          if (datum > max) max = datum;
        }

        const barHeight = Math.max(2, (max - min) * amp * 0.85);
        const y = (height - barHeight) / 2;

        ctx.fillRect(i, y, 1.5, barHeight);
      }
    }



    async function loadSampleTrack(type) {
      showToast("Generating audio track...", "info");
      
      try {
        currentAudioBuffer = await generateAudioTrack(type);
        const wavBlob = audioBufferToWav(currentAudioBuffer);
        const blobUrl = URL.createObjectURL(wavBlob);

        audioElement.src = blobUrl;
        audioElement.load();

        audioElement.onloadedmetadata = () => {
          updateDurationDisplay();
          drawWaveform(currentAudioBuffer);
          renderTimelineOverlays();
          renderRibbonStrip();
          showToast("Track loaded successfully!", "success");
        };

      } catch (err) {
        console.error("Error generating track:", err);
        showToast("Error synthesizing track", "error");
      }
    }

    function handleTrackSelectChange(value) {
      const customContainer = document.getElementById('customFileContainer');
      if (value === 'custom') {
        customContainer.classList.remove('hidden');
      } else {
        customContainer.classList.add('hidden');
        if (value === 'pop') document.getElementById('songTitleInput').value = "Neon Pop Adventure";
        if (value === 'lofi') document.getElementById('songTitleInput').value = "Sunset Lo-Fi Chill";
        if (value === 'funk') document.getElementById('songTitleInput').value = "Funk & Groove Lab";
        loadSampleTrack(value);
      }
    }

    function handleCustomAudioUpload(event) {
      const file = event.target.files[0];
      if (!file) return;

      document.getElementById('songTitleInput').value = file.name.replace(/\.[^/.]+$/, "");
      const objectUrl = URL.createObjectURL(file);
      audioElement.src = objectUrl;
      audioElement.load();

      // Read audio buffer for waveform
      const reader = new FileReader();
      reader.onload = function(e) {
        const arrayBuffer = e.target.result;
        if (!audioContext) audioContext = new (window.AudioContext || window.webkitAudioContext)();
        audioContext.decodeAudioData(arrayBuffer, (decodedBuffer) => {
          currentAudioBuffer = decodedBuffer;
          drawWaveform(currentAudioBuffer);
        });
      };
      reader.readAsArrayBuffer(file);

      audioElement.onloadedmetadata = () => {
        updateDurationDisplay();
        renderTimelineOverlays();
        renderRibbonStrip();
        showToast("Custom audio file loaded!", "success");
      };
    }

    function togglePlayPause() {
      if (audioElement.paused) {
        audioElement.play();
        isPlaying = true;
        document.getElementById('playIcon').className = 'fa-solid fa-pause';
      } else {
        audioElement.pause();
        isPlaying = false;
        document.getElementById('playIcon').className = 'fa-solid fa-play ml-0.5';
      }
    }

    function skipTime(seconds) {
      audioElement.currentTime = Math.max(0, Math.min(audioElement.duration || 0, audioElement.currentTime + seconds));
    }

    function setPlaybackSpeed(speed) {
      currentSpeed = speed;
      audioElement.playbackRate = speed;
      document.querySelectorAll('.speed-btn').forEach(btn => {
        if (parseFloat(btn.dataset.speed) === speed) {
          btn.className = 'speed-btn px-2 py-1 text-xs font-semibold rounded-lg bg-cyan-500 text-white transition';
        } else {
          btn.className = 'speed-btn px-2 py-1 text-xs font-semibold rounded-lg text-slate-400 hover:text-white transition';
        }
      });
      showToast(`Playback speed set to ${speed}x`, "info");
    }

    function changeVolume(val) {
      audioElement.volume = parseFloat(val);
      const icon = document.getElementById('volumeIcon');
      if (audioElement.volume === 0) {
        icon.className = 'fa-solid fa-volume-xmark';
      } else if (audioElement.volume < 0.5) {
        icon.className = 'fa-solid fa-volume-low';
      } else {
        icon.className = 'fa-solid fa-volume-high';
      }
    }

    function toggleMute() {
      isMuted = !isMuted;
      audioElement.muted = isMuted;
      const icon = document.getElementById('volumeIcon');
      if (isMuted) {
        icon.className = 'fa-solid fa-volume-xmark text-rose-400';
      } else {
        changeVolume(audioElement.volume);
      }
    }

    // Audio time update event listener
    audioElement.ontimeupdate = () => {
      const cur = audioElement.currentTime || 0;
      const dur = audioElement.duration || 1;

      // Update Time Display
      document.getElementById('currentTimeDisplay').innerText = formatTime(cur);
      document.getElementById('liveTimeTracker').innerText = formatTime(cur);

      // Playhead Position
      const percent = (cur / dur) * 100;
      document.getElementById('playheadLine').style.left = `${percent}%`;

      // Active Section Loop Handler
      if (activeLoopSectionId) {
        const loopSec = sectionsArray.find(s => s.id === activeLoopSectionId);
        if (loopSec) {
          if (cur >= loopSec.endTime || cur < loopSec.startTime) {
            audioElement.currentTime = loopSec.startTime;
          }
        }
      }

      // Live Section Marking Draft Width
      if (draftStartTime !== null && draftEndTime === null) {
        const draftPercentStart = (draftStartTime / dur) * 100;
        const draftPercentWidth = (Math.max(0, cur - draftStartTime) / dur) * 100;
        const draftEl = document.getElementById('draftSectionMarker');
        draftEl.style.left = `${draftPercentStart}%`;
        draftEl.style.width = `${draftPercentWidth}%`;
        draftEl.classList.remove('hidden');

        document.getElementById('activeTimingPreview').innerText = `${formatTime(draftStartTime)} - ${formatTime(cur)}`;
      }
    };

    function updateDurationDisplay() {
      const dur = audioElement.duration || 0;
      document.getElementById('totalDurationDisplay').innerText = formatTime(dur);
      document.getElementById('ribbonDurationEnd').innerText = formatTime(dur);
    }

    function handleTimelineClick(e) {
      const rect = e.currentTarget.getBoundingClientRect();
      const clickX = e.clientX - rect.left;
      const percentage = clickX / rect.width;
      const dur = audioElement.duration || 0;
      audioElement.currentTime = percentage * dur;
    }

    function handleTimelineHover(e) {
      const rect = e.currentTarget.getBoundingClientRect();
      const mouseX = e.clientX - rect.left;
      const percentage = mouseX / rect.width;
      const dur = audioElement.duration || 0;
      const hoverTime = percentage * dur;

      const tooltip = document.getElementById('timelineHoverTooltip');
      tooltip.innerText = formatTime(hoverTime);
      tooltip.style.left = `${mouseX}px`;
      tooltip.classList.remove('hidden');
    }

    function hideTimelineTooltip() {
      document.getElementById('timelineHoverTooltip').classList.add('hidden');
    }



    function markStartHere() {
      draftStartTime = audioElement.currentTime;
      draftEndTime = null;

      document.getElementById('endMarkBtn').disabled = false;
      document.getElementById('endMarkBtn').className = 'py-3 px-4 rounded-xl bg-amber-500 hover:bg-amber-400 text-white font-semibold text-xs transition shadow-lg shadow-amber-500/20 flex items-center justify-center gap-2 active:scale-95 animate-bounce';

      document.getElementById('liveMarkStatus').innerHTML = `
        <div class="text-amber-300 font-medium">
          <i class="fa-solid fa-spinner fa-spin mr-1"></i> Section started at <strong class="font-mono text-white">${formatTime(draftStartTime)}</strong>. Play to end point, then click <span class="text-amber-300 font-bold">"End Section"</span>!
        </div>
      `;

      showToast(`Start point set at ${formatTime(draftStartTime)}`, "info");
    }

    function markEndHere() {
      if (draftStartTime === null) return;
      draftEndTime = audioElement.currentTime;

      if (draftEndTime <= draftStartTime) {
        showToast("End time must be after start time!", "error");
        return;
      }

      document.getElementById('draftSectionMarker').classList.add('hidden');
      document.getElementById('confirmAddSectionBtn').disabled = false;

      document.getElementById('liveMarkStatus').innerHTML = `
        <div class="text-emerald-400 font-medium">
          <i class="fa-solid fa-check-circle mr-1"></i> Captured: <strong class="font-mono text-white">${formatTime(draftStartTime)} - ${formatTime(draftEndTime)}</strong> (${(draftEndTime - draftStartTime).toFixed(1)}s). Assign name & save!
        </div>
      `;

      document.getElementById('activeTimingPreview').innerText = `${formatTime(draftStartTime)} - ${formatTime(draftEndTime)}`;
      showToast("Section timing captured! Pick name & click Save.", "success");
    }

    function saveCapturedSection() {
      if (draftStartTime === null || draftEndTime === null) {
        showToast("Please mark both start and end times first!", "error");
        return;
      }

      const newSec = {
        id: 'sec_' + Date.now(),
        name: activeSelectedTag,
        startTime: parseFloat(draftStartTime.toFixed(2)),
        endTime: parseFloat(draftEndTime.toFixed(2)),
        color: activeSelectedColor,
        instruments: ["Synthesizer", "Drums/Beat"],
        dynamics: "mf (Medium Loud)",
        texture: "Homophonic (Melody + Chords)",
        rhythm: "Driving Beat",
        notes: ""
      };

      sectionsArray.push(newSec);
      sortSections('time');

      // Reset Live Marking Draft
      draftStartTime = null;
      draftEndTime = null;
      document.getElementById('endMarkBtn').disabled = true;
      document.getElementById('endMarkBtn').className = 'py-3 px-4 rounded-xl bg-slate-700 text-slate-500 font-semibold text-xs transition flex items-center justify-center gap-2 cursor-not-allowed';
      document.getElementById('confirmAddSectionBtn').disabled = true;
      document.getElementById('liveMarkStatus').innerHTML = `Click <span class="text-emerald-400 font-semibold mx-1">"Start Section Here"</span> to start capturing timing.`;

      // Auto Suggest Next Section Tag
      autoAdvancePresetTag();

      renderAllViews();
      showToast(`Section "${newSec.name}" saved!`, "success");
    }

    function autoAdvancePresetTag() {
      if (activeSelectedTag.startsWith("Verse")) activeSelectedTag = "Chorus 1";
      else if (activeSelectedTag.startsWith("Chorus")) activeSelectedTag = "Bridge";
      else if (activeSelectedTag === "Intro") activeSelectedTag = "Verse 1";
      else if (activeSelectedTag === "Bridge") activeSelectedTag = "Chorus 2";
      else activeSelectedTag = "Outro";

      document.getElementById('activeTagName').innerText = activeSelectedTag;
      renderPresetChips();
    }



    function renderPresetChips() {
      const container = document.getElementById('presetTagChips');
      container.innerHTML = PRESET_TAGS.map(tag => {
        const isSelected = tag === activeSelectedTag;
        return `
          <button onclick="selectPresetTag('${tag}')" class="px-3 py-1.5 rounded-xl text-xs font-semibold transition border ${isSelected ? 'bg-fuchsia-600 border-fuchsia-400 text-white shadow-md shadow-fuchsia-600/30' : 'bg-slate-900 border-slate-700 text-slate-300 hover:bg-slate-800'}">
            ${tag}
          </button>
        `;
      }).join('');
    }

    function renderColorPaletteChips() {
      const container = document.getElementById('colorPaletteChips');
      container.innerHTML = PRESET_COLORS.map(c => {
        const isSelected = c.hex.toLowerCase() === activeSelectedColor.toLowerCase();
        return `
          <button onclick="selectPresetColor('${c.hex}')" title="${c.name}" class="w-8 h-8 rounded-xl transition transform hover:scale-110 flex items-center justify-center border-2 ${isSelected ? 'border-white scale-110 shadow-lg' : 'border-transparent'}" style="background-color: ${c.hex}">
            ${isSelected ? '<i class="fa-solid fa-check text-white text-xs"></i>' : ''}
          </button>
        `;
      }).join('');
    }

    function selectPresetTag(tag) {
      activeSelectedTag = tag;
      document.getElementById('activeTagName').innerText = tag;
      renderPresetChips();
    }

    function selectPresetColor(hex) {
      activeSelectedColor = hex;
      document.getElementById('activeColorBadge').style.backgroundColor = hex;
      renderColorPaletteChips();
    }

    function selectCustomColor(hex) {
      activeSelectedColor = hex;
      document.getElementById('activeColorBadge').style.backgroundColor = hex;
      renderColorPaletteChips();
    }

    function sortSections(method) {
      if (method === 'time') sectionsArray.sort((a, b) => a.startTime - b.startTime);
      if (method === 'name') sectionsArray.sort((a, b) => a.name.localeCompare(b.name));
      if (method === 'duration') sectionsArray.sort((a, b) => (b.endTime - b.startTime) - (a.endTime - a.startTime));
    }

    function renderSectionsList() {
      const sortVal = document.getElementById('sectionSortSelect').value;
      sortSections(sortVal);

      const container = document.getElementById('sectionsListContainer');

      if (sectionsArray.length === 0) {
        container.innerHTML = `
          <div class="text-center py-12 border-2 border-dashed border-slate-700/80 rounded-2xl p-6 bg-slate-900/40">
            <i class="fa-solid fa-layer-group text-4xl text-slate-600 mb-3 block"></i>
            <h3 class="text-base font-bold text-slate-300">No Song Sections Marked Yet</h3>
            <p class="text-xs text-slate-400 mt-1 max-w-md mx-auto">
              Use the <strong>Live Section Marker</strong> controls above to set "IN" and "OUT" bounds as you listen, or click <strong>Load Demo Markings</strong> in the top menu to preview.
            </p>
          </div>
        `;
        return;
      }

      container.innerHTML = sectionsArray.map(sec => {
        const duration = (sec.endTime - sec.startTime).toFixed(1);
        const isLoopingThis = activeLoopSectionId === sec.id;

        return `
          <div class="bg-slate-900/90 border border-slate-700/90 rounded-2xl p-4 sm:p-5 shadow-lg transition hover:border-slate-600">
            
            <!-- Card Header Bar -->
            <div class="flex flex-wrap items-center justify-between gap-3 pb-3 border-b border-slate-800">
              
              <div class="flex items-center gap-3">
                <!-- Color Badge -->
                <div class="w-5 h-5 rounded-lg shadow" style="background-color: ${sec.color}"></div>
                <h3 class="text-base font-bold text-white">${sec.name}</h3>
                <span class="font-mono text-xs text-cyan-300 bg-cyan-950/60 border border-cyan-800/60 px-2.5 py-0.5 rounded-full font-semibold">
                  ${formatTime(sec.startTime)} - ${formatTime(sec.endTime)} (${duration}s)
                </span>
              </div>

              <!-- Action Toolbar -->
              <div class="flex items-center gap-2">
                <!-- Loop Toggle Button -->
                <button onclick="toggleLoopSection('${sec.id}')" class="px-3 py-1.5 rounded-xl text-xs font-semibold transition flex items-center gap-1.5 border ${isLoopingThis ? 'bg-amber-500 border-amber-400 text-slate-950 shadow-md shadow-amber-500/20' : 'bg-slate-800 border-slate-700 text-slate-300 hover:bg-slate-700'}">
                  <i class="fa-solid fa-rotate-right"></i>
                  <span>${isLoopingThis ? 'Looping' : 'Loop Section'}</span>
                </button>

                <!-- Jump To Button -->
                <button onclick="jumpToSection(${sec.startTime})" class="px-3 py-1.5 rounded-xl bg-slate-800 border border-slate-700 hover:bg-slate-700 text-slate-300 text-xs font-semibold transition flex items-center gap-1">
                  <i class="fa-solid fa-play text-[10px]"></i> Play
                </button>

                <!-- Edit Button -->
                <button onclick="openEditSectionModal('${sec.id}')" class="p-1.5 rounded-lg bg-slate-800 hover:bg-slate-700 text-slate-300 transition">
                  <i class="fa-solid fa-pen-to-square"></i>
                </button>

                <!-- Delete Button -->
                <button onclick="deleteSection('${sec.id}')" class="p-1.5 rounded-lg bg-slate-800 hover:bg-rose-900/50 text-rose-400 transition">
                  <i class="fa-solid fa-trash-can"></i>
                </button>
              </div>

            </div>

            <!-- Card Body - Interactive Student Analysis Checklist -->
            <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4 mt-4 text-xs">
              
              <!-- Instruments Checklist -->
              <div class="bg-slate-950/60 border border-slate-800 p-3 rounded-xl">
                <span class="font-bold text-slate-300 block mb-2 text-[11px] uppercase tracking-wider">
                  <i class="fa-solid fa-drum text-cyan-400 mr-1"></i> Instruments Heard:
                </span>
                <div class="flex flex-wrap gap-1">
                  ${INSTRUMENT_OPTIONS.map(inst => {
                    const checked = sec.instruments.includes(inst);
                    return `
                      <button onclick="toggleInstrumentTag('${sec.id}', '${inst}')" class="px-2 py-1 rounded-lg text-[10px] font-medium transition ${checked ? 'bg-cyan-500/20 border border-cyan-400 text-cyan-300' : 'bg-slate-900 border border-slate-800 text-slate-500 hover:text-slate-300'}">
                        ${inst}
                      </button>
                    `;
                  }).join('')}
                </div>
              </div>

              <!-- Dynamics Dropdown -->
              <div class="bg-slate-950/60 border border-slate-800 p-3 rounded-xl">
                <span class="font-bold text-slate-300 block mb-2 text-[11px] uppercase tracking-wider">
                  <i class="fa-solid fa-volume-high text-fuchsia-400 mr-1"></i> Dynamics / Volume:
                </span>
                <select onchange="updateSectionField('${sec.id}', 'dynamics', this.value)" class="w-full bg-slate-900 border border-slate-700 text-slate-200 text-xs rounded-lg p-2 focus:outline-none">
                  <option value="">Select Dynamics...</option>
                  ${DYNAMICS_OPTIONS.map(d => `<option value="${d}" ${sec.dynamics === d ? 'selected' : ''}>${d}</option>`).join('')}
                </select>
              </div>

              <!-- Texture & Rhythm Selection -->
              <div class="bg-slate-950/60 border border-slate-800 p-3 rounded-xl">
                <span class="font-bold text-slate-300 block mb-2 text-[11px] uppercase tracking-wider">
                  <i class="fa-solid fa-layer-group text-amber-400 mr-1"></i> Texture & Rhythm:
                </span>
                <div class="space-y-2">
                  <select onchange="updateSectionField('${sec.id}', 'texture', this.value)" class="w-full bg-slate-900 border border-slate-700 text-slate-200 text-xs rounded-lg p-1.5 focus:outline-none">
                    <option value="">Select Texture...</option>
                    ${TEXTURE_OPTIONS.map(t => `<option value="${t}" ${sec.texture === t ? 'selected' : ''}>${t}</option>`).join('')}
                  </select>
                  <select onchange="updateSectionField('${sec.id}', 'rhythm', this.value)" class="w-full bg-slate-900 border border-slate-700 text-slate-200 text-xs rounded-lg p-1.5 focus:outline-none">
                    <option value="">Select Rhythm...</option>
                    ${RHYTHM_OPTIONS.map(r => `<option value="${r}" ${sec.rhythm === r ? 'selected' : ''}>${r}</option>`).join('')}
                  </select>
                </div>
              </div>

              <!-- Student Notes Textbox -->
              <div class="bg-slate-950/60 border border-slate-800 p-3 rounded-xl flex flex-col">
                <span class="font-bold text-slate-300 block mb-2 text-[11px] uppercase tracking-wider">
                  <i class="fa-solid fa-comment-dots text-emerald-400 mr-1"></i> Student Observations:
                </span>
                <textarea onchange="updateSectionField('${sec.id}', 'notes', this.value)" placeholder="Write musical observations (e.g., synth lead enters, full beat drop, quiet vocal melody)..." class="w-full flex-1 bg-slate-900 border border-slate-700 rounded-lg p-2 text-slate-200 text-xs placeholder-slate-600 focus:outline-none resize-none">${sec.notes || ''}</textarea>
              </div>

            </div>

          </div>
        `;
      }).join('');
    }

    function toggleInstrumentTag(secId, inst) {
      const sec = sectionsArray.find(s => s.id === secId);
      if (!sec) return;
      if (sec.instruments.includes(inst)) {
        sec.instruments = sec.instruments.filter(i => i !== inst);
      } else {
        sec.instruments.push(inst);
      }
      renderSectionsList();
    }

    function updateSectionField(secId, field, val) {
      const sec = sectionsArray.find(s => s.id === secId);
      if (sec) {
        sec[field] = val;
      }
    }

    function toggleLoopSection(secId) {
      if (activeLoopSectionId === secId) {
        stopLoopingSection();
      } else {
        activeLoopSectionId = secId;
        const sec = sectionsArray.find(s => s.id === secId);
        if (sec) {
          audioElement.currentTime = sec.startTime;
          if (audioElement.paused) togglePlayPause();
          document.getElementById('loopStatusBadge').classList.remove('hidden');
          document.getElementById('loopStatusBadge').classList.add('flex');
          document.getElementById('loopingSectionName').innerText = sec.name;
          showToast(`Looping section "${sec.name}"`, "info");
        }
      }
      renderSectionsList();
    }

    function stopLoopingSection() {
      activeLoopSectionId = null;
      document.getElementById('loopStatusBadge').classList.add('hidden');
      document.getElementById('loopStatusBadge').classList.remove('flex');
      renderSectionsList();
    }

    function jumpToSection(startTime) {
      audioElement.currentTime = startTime;
      if (audioElement.paused) togglePlayPause();
    }

    function deleteSection(secId) {
      sectionsArray = sectionsArray.filter(s => s.id !== secId);
      if (activeLoopSectionId === secId) stopLoopingSection();
      renderAllViews();
      showToast("Section deleted", "info");
    }



    function renderTimelineOverlays() {
      const container = document.getElementById('timelineSectionsOverlay');
      const dur = audioElement.duration || 1;

      container.innerHTML = sectionsArray.map(sec => {
        const left = (sec.startTime / dur) * 100;
        const width = ((sec.endTime - sec.startTime) / dur) * 100;
        return `
          <div class="absolute top-0 bottom-0 opacity-40 hover:opacity-75 transition border-x border-white/30 flex items-center justify-center cursor-pointer" 
               style="left: ${left}%; width: ${width}%; background-color: ${sec.color}" 
               title="${sec.name} (${formatTime(sec.startTime)} - ${formatTime(sec.endTime)})"
               onclick="event.stopPropagation(); jumpToSection(${sec.startTime})">
            <span class="text-[10px] font-bold text-white shadow truncate px-1">${sec.name}</span>
          </div>
        `;
      }).join('');
    }

    function renderRibbonStrip() {
      const container = document.getElementById('sectionRibbonStrip');
      const dur = audioElement.duration || 1;

      if (sectionsArray.length === 0) {
        container.innerHTML = `<div class="w-full h-full bg-slate-800 rounded text-[9px] text-slate-500 flex items-center justify-center font-medium">No sections marked yet</div>`;
        return;
      }

      // Sort chronological
      const sorted = [...sectionsArray].sort((a, b) => a.startTime - b.startTime);
      let html = '';
      let currentPos = 0;

      sorted.forEach(sec => {
        if (sec.startTime > currentPos) {
          const gapWidth = ((sec.startTime - currentPos) / dur) * 100;
          if (gapWidth > 0.5) {
            html += `<div class="h-full bg-slate-800/80 rounded-sm" style="width: ${gapWidth}%" title="Unassigned Gap"></div>`;
          }
        }
        const width = ((sec.endTime - sec.startTime) / dur) * 100;
        html += `<div class="h-full rounded-sm flex items-center justify-center text-[9px] font-bold text-white shadow-sm truncate px-1 transition hover:brightness-125" style="width: ${width}%; background-color: ${sec.color}" title="${sec.name}">${width > 6 ? sec.name : ''}</div>`;
        currentPos = sec.endTime;
      });

      if (currentPos < dur) {
        const remainingWidth = ((dur - currentPos) / dur) * 100;
        if (remainingWidth > 0.5) {
          html += `<div class="h-full bg-slate-800/80 rounded-sm" style="width: ${remainingWidth}%" title="Unassigned Gap"></div>`;
        }
      }

      container.innerHTML = html;
    }

    function renderSongFormChain() {
      const container = document.getElementById('songFormChain');
      const dur = audioElement.duration || 1;

      if (sectionsArray.length === 0) {
        container.innerHTML = `<div class="text-xs text-slate-500 italic">No sections marked yet. Mark live or add sections above to build the song form!</div>`;
        document.getElementById('coveragePercentage').innerText = `0%`;
        return;
      }

      const sorted = [...sectionsArray].sort((a, b) => a.startTime - b.startTime);

      // Compute coverage
      let totalMarkedSecs = 0;
      sorted.forEach(s => totalMarkedSecs += (s.endTime - s.startTime));
      const coverage = Math.min(100, Math.round((totalMarkedSecs / dur) * 100));
      document.getElementById('coveragePercentage').innerText = `${coverage}%`;

      container.innerHTML = sorted.map((sec, idx) => {
        return `
          <div class="flex items-center gap-2 flex-shrink-0">
            <div onclick="jumpToSection(${sec.startTime})" class="px-3 py-1.5 rounded-xl font-extrabold text-xs text-white shadow-md cursor-pointer transition transform hover:scale-105 flex items-center gap-1.5" style="background-color: ${sec.color}">
              <i class="fa-solid fa-music text-[10px]"></i>
              <span>${sec.name}</span>
            </div>
            ${idx < sorted.length - 1 ? '<i class="fa-solid fa-arrow-right text-slate-600 text-xs"></i>' : ''}
          </div>
        `;
      }).join('');
    }

    function renderAllViews() {
      renderSectionsList();
      renderTimelineOverlays();
      renderRibbonStrip();
      renderSongFormChain();
    }



    function exportProjectJSON() {
      const data = {
        studentName: document.getElementById('studentNameInput').value,
        songTitle: document.getElementById('songTitleInput').value,
        audioTrackType: document.getElementById('sampleTrackSelect').value,
        sections: sectionsArray,
        exportDate: new Date().toISOString()
      };

      const jsonStr = JSON.stringify(data, null, 2);
      const blob = new Blob([jsonStr], { type: 'application/json' });
      const url = URL.createObjectURL(blob);

      const a = document.createElement('a');
      a.href = url;
      a.download = `${data.songTitle.replace(/\s+/g, '_')}_Analysis.json`;
      a.click();
      URL.revokeObjectURL(url);

      showToast("Project JSON exported successfully!", "success");
    }

    function importProjectJSON(e) {
      const file = e.target.files[0];
      if (!file) return;

      const reader = new FileReader();
      reader.onload = function(evt) {
        try {
          const data = JSON.parse(evt.target.result);
          if (data.studentName) document.getElementById('studentNameInput').value = data.studentName;
          if (data.songTitle) document.getElementById('songTitleInput').value = data.songTitle;
          if (data.sections && Array.isArray(data.sections)) {
            sectionsArray = data.sections;
            renderAllViews();
            showToast("Project imported successfully!", "success");
          }
        } catch (err) {
          showToast("Failed to parse JSON file", "error");
        }
      };
      reader.readAsText(file);
    }

    function loadDemoAnalysis() {
      sectionsArray = [
        {
          id: 'sec_1',
          name: 'Intro',
          startTime: 0,
          endTime: 8,
          color: '#84cc16',
          instruments: ['Synthesizer'],
          dynamics: 'p (Soft)',
          texture: 'Monophonic (1 Melody)',
          rhythm: 'Slow / Calm',
          notes: 'Bright synthesizer arpeggio motif starts the track.'
        },
        {
          id: 'sec_2',
          name: 'Verse 1',
          startTime: 8,
          endTime: 20,
          color: '#ef4444',
          instruments: ['Drums/Beat', 'Bass', 'Synthesizer'],
          dynamics: 'mf (Medium Loud)',
          texture: 'Homophonic (Melody + Chords)',
          rhythm: 'Driving Beat',
          notes: 'Kick drum and bassline enter, steady 4/4 rhythm.'
        },
        {
          id: 'sec_3',
          name: 'Pre-Chorus',
          startTime: 20,
          endTime: 30,
          color: '#f59e0b',
          instruments: ['Drums/Beat', 'Synthesizer', 'SFX'],
          dynamics: 'Crescendo ↗',
          texture: 'Dense / Full Layers',
          rhythm: 'Fast / Energetic',
          notes: 'Rising filter sweep with fast snare roll building tension.'
        },
        {
          id: 'sec_4',
          name: 'Chorus 1',
          startTime: 30,
          endTime: 45,
          color: '#06b6d4',
          instruments: ['Drums/Beat', 'Bass', 'Synthesizer', 'Lead Vocals'],
          dynamics: 'f (Loud)',
          texture: 'Homophonic (Melody + Chords)',
          rhythm: 'Driving Beat',
          notes: 'Full energy peak, catchy synth lead melody and thick bass.'
        },
        {
          id: 'sec_5',
          name: 'Bridge',
          startTime: 45,
          endTime: 55,
          color: '#8b5cf6',
          instruments: ['Piano/Keys', 'Synthesizer'],
          dynamics: 'mp (Medium Soft)',
          texture: 'Sparse / Thin',
          rhythm: 'Half-Time',
          notes: 'Drums drop out for a soft atmospheric chord breakdown.'
        },
        {
          id: 'sec_6',
          name: 'Outro',
          startTime: 55,
          endTime: 60,
          color: '#f43f5e',
          instruments: ['Synthesizer'],
          dynamics: 'Decrescendo ↘',
          texture: 'Monophonic (1 Melody)',
          rhythm: 'Rubato / Free',
          notes: 'Soft synth ring out fading to silence.'
        }
      ];

      renderAllViews();
      showToast("Demo section markings loaded!", "success");
    }

    function clearAllSectionsConfirm() {
      if (confirm("Are you sure you want to clear all section markings?")) {
        sectionsArray = [];
        stopLoopingSection();
        renderAllViews();
        showToast("All sections cleared", "info");
      }
    }



    function openPrintReportModal() {
      document.getElementById('reportSongTitle').innerText = document.getElementById('songTitleInput').value || 'Song Analysis';
      document.getElementById('reportStudentName').innerText = `Student: ${document.getElementById('studentNameInput').value || 'Alex Rivera'}`;
      document.getElementById('reportDate').innerText = new Date().toLocaleDateString('en-US', { month: 'short', day: 'numeric', year: 'numeric' });
      document.getElementById('reportSectionCount').innerText = sectionsArray.length;
      document.getElementById('reportTotalDuration').innerText = formatTime(audioElement.duration || 0);

      // Render Form Chain
      const sorted = [...sectionsArray].sort((a, b) => a.startTime - b.startTime);
      document.getElementById('reportFormChain').innerHTML = sorted.map(sec => `
        <span class="px-2.5 py-1 rounded-lg text-white font-bold text-xs shadow" style="background-color: ${sec.color}">${sec.name}</span>
      `).join(' <i class="fa-solid fa-chevron-right text-slate-600 text-[10px]"></i> ');

      // Render Ribbon
      const ribbonContainer = document.getElementById('reportRibbonStrip');
      ribbonContainer.innerHTML = document.getElementById('sectionRibbonStrip').innerHTML;

      // Render Table
      const tbody = document.getElementById('reportTableBody');
      tbody.innerHTML = sorted.map(sec => `
        <tr>
          <td class="p-3 font-bold flex items-center gap-2">
            <span class="w-3 h-3 rounded-full inline-block" style="background-color: ${sec.color}"></span>
            ${sec.name}
          </td>
          <td class="p-3 font-mono">${formatTime(sec.startTime)} - ${formatTime(sec.endTime)}</td>
          <td class="p-3 font-mono">${(sec.endTime - sec.startTime).toFixed(1)}s</td>
          <td class="p-3">${sec.instruments.join(', ') || 'None listed'}</td>
          <td class="p-3">
            <div>${sec.dynamics || '-'}</div>
            <div class="text-[10px] text-slate-400">${sec.texture || ''}</div>
          </td>
          <td class="p-3 italic text-slate-300">${sec.notes || 'No notes added.'}</td>
        </tr>
      `).join('');

      document.getElementById('printReportModal').classList.remove('hidden');
    }

    function closePrintReportModal() {
      document.getElementById('printReportModal').classList.add('hidden');
    }

    function triggerWindowPrint() {
      window.print();
    }

    function openGlossaryModal() {
      document.getElementById('glossaryModal').classList.remove('hidden');
    }

    function closeGlossaryModal() {
      document.getElementById('glossaryModal').classList.add('hidden');
    }

    function openManualSectionModal() {
      editingSectionId = null;
      document.getElementById('modalSectionTitle').innerText = 'Add Manual Section';
      document.getElementById('manualNameInput').value = activeSelectedTag;
      document.getElementById('manualStartInput').value = audioElement.currentTime.toFixed(1);
      document.getElementById('manualEndInput').value = Math.min(audioElement.duration || 60, (audioElement.currentTime + 10)).toFixed(1);
      document.getElementById('manualColorInput').value = activeSelectedColor;
      document.getElementById('manualSectionModal').classList.remove('hidden');
    }

    function openEditSectionModal(secId) {
      editingSectionId = secId;
      const sec = sectionsArray.find(s => s.id === secId);
      if (!sec) return;

      document.getElementById('modalSectionTitle').innerText = 'Edit Section';
      document.getElementById('manualNameInput').value = sec.name;
      document.getElementById('manualStartInput').value = sec.startTime;
      document.getElementById('manualEndInput').value = sec.endTime;
      document.getElementById('manualColorInput').value = sec.color;
      document.getElementById('manualSectionModal').classList.remove('hidden');
    }

    function closeManualSectionModal() {
      document.getElementById('manualSectionModal').classList.add('hidden');
    }

    function saveManualSectionModal() {
      const name = document.getElementById('manualNameInput').value.trim() || 'Section';
      const start = parseFloat(document.getElementById('manualStartInput').value);
      const end = parseFloat(document.getElementById('manualEndInput').value);
      const color = document.getElementById('manualColorInput').value;

      if (isNaN(start) || isNaN(end) || end <= start) {
        showToast("Invalid timing bounds!", "error");
        return;
      }

      if (editingSectionId) {
        const sec = sectionsArray.find(s => s.id === editingSectionId);
        if (sec) {
          sec.name = name;
          sec.startTime = start;
          sec.endTime = end;
          sec.color = color;
        }
      } else {
        sectionsArray.push({
          id: 'sec_' + Date.now(),
          name: name,
          startTime: start,
          endTime: end,
          color: color,
          instruments: ["Synthesizer"],
          dynamics: "mf (Medium Loud)",
          texture: "Homophonic (Melody + Chords)",
          rhythm: "Driving Beat",
          notes: ""
        });
      }

      closeManualSectionModal();
      renderAllViews();
      showToast("Section updated!", "success");
    }

    function toggleExportMenu() {
      const menu = document.getElementById('exportDropdown');
      menu.classList.toggle('hidden');
    }

    // Close export dropdown on outside click
    document.addEventListener('click', (e) => {
      const menu = document.getElementById('exportDropdown');
      const btn = e.target.closest('button[onclick="toggleExportMenu()"]');
      if (!btn && !menu.contains(e.target)) {
        menu.classList.add('hidden');
      }
    });



    function toggleTheme() {
      const html = document.documentElement;
      const icon = document.getElementById('themeIcon');
      if (html.classList.contains('dark')) {
        html.classList.remove('dark');
        icon.className = 'fa-solid fa-moon text-indigo-600';
      } else {
        html.classList.add('dark');
        icon.className = 'fa-solid fa-sun text-amber-400';
      }
    }

    function showToast(message, type = 'info') {
      const container = document.getElementById('toastContainer');
      const toast = document.createElement('div');

      const bgMap = {
        info: 'bg-slate-800 border-cyan-500 text-cyan-300',
        success: 'bg-slate-800 border-emerald-500 text-emerald-300',
        error: 'bg-slate-800 border-rose-500 text-rose-300'
      };

      const iconMap = {
        info: 'fa-info-circle',
        success: 'fa-check-circle',
        error: 'fa-exclamation-circle'
      };

      toast.className = `pointer-events-auto border px-4 py-2.5 rounded-xl shadow-xl text-xs font-semibold flex items-center gap-2 transform transition duration-300 translate-y-2 opacity-0 ${bgMap[type] || bgMap.info}`;
      toast.innerHTML = `<i class="fa-solid ${iconMap[type] || iconMap.info}"></i> <span>${message}</span>`;

      container.appendChild(toast);

      requestAnimationFrame(() => {
        toast.classList.remove('translate-y-2', 'opacity-0');
      });

      setTimeout(() => {
        toast.classList.add('opacity-0', 'translate-y-2');
        setTimeout(() => toast.remove(), 300);
      }, 3000);
    }

    function formatTime(seconds) {
      if (isNaN(seconds)) return '00:00.0';
      const mins = Math.floor(seconds / 60);
      const secs = (seconds % 60).toFixed(1);
      const padMins = String(mins).padStart(2, '0');
      const padSecs = String(secs).padStart(4, '0');
      return `${padMins}:${padSecs}`;
    }

    // Window Onload Initialization
    window.onload = () => {
      renderPresetChips();
      renderColorPaletteChips();
      loadSampleTrack('pop');
    };
  </script>
</body>
</html>
