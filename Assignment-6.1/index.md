I will now create an interactive Box Model Visualizer & Inspector web application tailored for Assignment 6.1.

This standalone interactive app includes:

Live 3-Card Demonstration: Displays 3 customizable cards with default presets highlighting different box-sizing paradigms and spacing values.

Interactive Controls & Inspector: Real-time control sliders and inputs to dynamically tweak width, height, padding, border, margin, and box-sizing modes, along with live formula breakdowns calculating exact rendered dimensions.

DevTools-Style Live Box Model Diagram: Standardized visual overlay (Blue = Content, Green = Padding, Yellow/Brown = Border, Orange/Peach = Margin) updating live with hover indicators.

Side-by-Side Comparison: Direct side-by-side comparison mode contrasting content-box vs border-box behavior under identical properties.

Assignment Submission Helper: Built-in annotation & screenshot export tool (using Canvas rendering) and export features to download pure HTML/CSS code files ready for assignment submission.

Box Model Inspector
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Assignment 6.1 — Box Model Inspection</title>
  <!-- Tailwind CSS CDN -->
  <script src="https://cdn.tailwindcss.com"></script>
  <!-- Google Font: Inter -->
  <link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&family=Fira+Code:wght@400;500;600&display=swap" />
  <!-- FontAwesome for Icons -->
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css" />
  <script>
    tailwind.config = {
      theme: {
        extend: {
          fontFamily: {
            sans: ['Inter', 'sans-serif'],
            mono: ['Fira Code', 'monospace']
          },
          colors: {
            boxContent: '#8ab4f8',
            boxContentBg: 'rgba(138, 180, 248, 0.25)',
            boxPadding: '#81c995',
            boxPaddingBg: 'rgba(129, 201, 149, 0.3)',
            boxBorder: '#fdd663',
            boxBorderBg: 'rgba(253, 214, 99, 0.35)',
            boxMargin: '#f9ab00',
            boxMarginBg: 'rgba(249, 171, 0, 0.25)',
          }
        }
      }
    };
  </script>
  <style>
    /* Custom CSS Box Model Overlays and Helper Styling */
    .box-layer {
      transition: all 0.15s ease-in-out;
    }
    
    .dev-content { background-color: rgba(138, 180, 248, 0.45); border: 1px dashed #4285f4; }
    .dev-padding { background-color: rgba(129, 201, 149, 0.45); border: 1px dashed #34a853; }
    .dev-border  { background-color: rgba(253, 214, 99, 0.55); border: 1px dashed #fbbc05; }
    .dev-margin  { background-color: rgba(249, 171, 0, 0.35); border: 1px dashed #ea4335; }

    /* Custom range slider styling */
    input[type=range] {
      accent-color: #3b82f6;
    }

    /* Print styles for export functionality */
    @media print {
      .no-print { display: none !important; }
      .print-only { display: block !important; }
    }
  </style>
</head>
<body class="bg-slate-900 text-slate-100 font-sans min-h-screen flex flex-col antialiased selection:bg-blue-500 selection:text-white">

  <!-- Header -->
  <header class="bg-slate-800/80 backdrop-blur border-b border-slate-700 sticky top-0 z-40">
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-3.5 flex flex-wrap items-center justify-between gap-4">
      <div class="flex items-center gap-3">
        <div class="bg-gradient-to-tr from-blue-600 to-indigo-500 text-white p-2.5 rounded-xl shadow-lg shadow-blue-500/20">
          <i class="fa-solid fa-cube text-xl"></i>
        </div>
        <div>
          <h1 class="text-lg sm:text-xl font-bold bg-gradient-to-r from-white via-slate-200 to-slate-400 bg-clip-text text-transparent">
            Assignment 6.1 — Box Model Inspection
          </h1>
          <p class="text-xs text-slate-400 flex items-center gap-2">
            <span>Interactive Visualizer & Inspection Tool</span>
            <span class="inline-block w-1.5 h-1.5 rounded-full bg-emerald-400"></span>
            <span class="text-emerald-400 font-medium">Ready for Submission</span>
          </p>
        </div>
      </div>

      <!-- Header Action Buttons -->
      <div class="flex items-center gap-2 flex-wrap">
        <button id="btnNavInspect" onclick="switchTab('inspector')" class="tab-btn px-3.5 py-1.5 rounded-lg text-xs sm:text-sm font-medium bg-blue-600 text-white shadow-sm hover:bg-blue-500 transition-all flex items-center gap-1.5">
          <i class="fa-solid fa-sliders"></i> Visual Inspector
        </button>
        <button id="btnNavCompare" onclick="switchTab('compare')" class="tab-btn px-3.5 py-1.5 rounded-lg text-xs sm:text-sm font-medium bg-slate-700 text-slate-300 hover:bg-slate-600 transition-all flex items-center gap-1.5">
          <i class="fa-solid fa-columns"></i> Content-Box vs Border-Box
        </button>
        <button id="btnNavExport" onclick="switchTab('export')" class="tab-btn px-3.5 py-1.5 rounded-lg text-xs sm:text-sm font-medium bg-slate-700 text-slate-300 hover:bg-slate-600 transition-all flex items-center gap-1.5">
          <i class="fa-solid fa-file-export"></i> Submission Helper
        </button>
      </div>
    </div>
  </header>

  <!-- Main Container -->
  <main class="flex-1 max-w-7xl w-full mx-auto p-4 sm:p-6 space-y-6">

    <!-- OVERVIEW / INSTRUCTIONS BANNER -->
    <section class="bg-slate-800/50 border border-slate-700/70 rounded-2xl p-4 sm:p-5 shadow-sm">
      <div class="flex items-start gap-3">
        <div class="text-blue-400 mt-0.5 text-lg"><i class="fa-solid fa-circle-info"></i></div>
        <div class="flex-1 text-xs sm:text-sm text-slate-300 space-y-1">
          <span class="font-semibold text-white">Assignment Objectives Checklist:</span>
          <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 gap-2 mt-2">
            <div class="flex items-center gap-2 bg-slate-800 p-2 rounded-lg border border-slate-700/50">
              <i class="fa-solid fa-circle-check text-emerald-400 text-xs"></i>
              <span>1. Create 3 cards/boxes</span>
            </div>
            <div class="flex items-center gap-2 bg-slate-800 p-2 rounded-lg border border-slate-700/50">
              <i class="fa-solid fa-circle-check text-emerald-400 text-xs"></i>
              <span>2. Add heading & paragraph</span>
            </div>
            <div class="flex items-center gap-2 bg-slate-800 p-2 rounded-lg border border-slate-700/50">
              <i class="fa-solid fa-circle-check text-emerald-400 text-xs"></i>
              <span>3. Apply custom spacing/border</span>
            </div>
            <div class="flex items-center gap-2 bg-slate-800 p-2 rounded-lg border border-slate-700/50">
              <i class="fa-solid fa-circle-check text-emerald-400 text-xs"></i>
              <span>4. Observe box-sizing effects</span>
            </div>
            <div class="flex items-center gap-2 bg-slate-800 p-2 rounded-lg border border-slate-700/50">
              <i class="fa-solid fa-circle-check text-emerald-400 text-xs"></i>
              <span>5. DevTools model diagram</span>
            </div>
            <div class="flex items-center gap-2 bg-slate-800 p-2 rounded-lg border border-slate-700/50">
              <i class="fa-solid fa-circle-check text-emerald-400 text-xs"></i>
              <span>6. Export labeled diagram/code</span>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- TAB 1: VISUAL INSPECTOR & LIVE DEMO -->
    <div id="tab-inspector" class="tab-content space-y-6">

      <!-- CARDS SELECTOR ROW -->
      <div>
        <div class="flex items-center justify-between mb-3">
          <h2 class="text-sm font-semibold uppercase tracking-wider text-slate-400">Step 1 & 2: Interactive Cards Showcase</h2>
          <span class="text-xs text-slate-400">Click a card to select and inspect it below</span>
        </div>
        <div class="grid grid-cols-1 md:grid-cols-3 gap-5" id="cards-container">
          <!-- Card 1 -->
          <div id="card-card1" onclick="selectCard('card1')" class="card-item cursor-pointer bg-slate-800 border-2 border-blue-500/80 rounded-2xl transition-all hover:shadow-lg relative group">
            <div class="absolute -top-3 left-4 bg-blue-600 text-white text-[10px] font-bold px-2 py-0.5 rounded-full uppercase tracking-wider shadow">
              Card 1 (Selected)
            </div>
            <div id="card1-target" class="transition-all duration-200">
              <h3 id="card1-title" class="text-base font-bold text-white mb-2">Card 1: Content-Box Basics</h3>
              <p id="card1-text" class="text-xs text-slate-300 leading-relaxed">
                Demonstrating classic <code class="bg-slate-900/50 px-1 py-0.5 rounded text-blue-300">content-box</code> where padding and border are added onto the specified width.
              </p>
            </div>
          </div>

          <!-- Card 2 -->
          <div id="card-card2" onclick="selectCard('card2')" class="card-item cursor-pointer bg-slate-800 border-2 border-slate-700 hover:border-slate-500 rounded-2xl transition-all hover:shadow-lg relative group">
            <div class="absolute -top-3 left-4 bg-slate-700 text-slate-300 text-[10px] font-bold px-2 py-0.5 rounded-full uppercase tracking-wider shadow">
              Card 2
            </div>
            <div id="card2-target" class="transition-all duration-200">
              <h3 id="card2-title" class="text-base font-bold text-white mb-2">Card 2: Border-Box Precision</h3>
              <p id="card2-text" class="text-xs text-slate-300 leading-relaxed">
                Demonstrating modern <code class="bg-slate-900/50 px-1 py-0.5 rounded text-emerald-300">border-box</code> where padding and border are absorbed inside the declared width.
              </p>
            </div>
          </div>

          <!-- Card 3 -->
          <div id="card-card3" onclick="selectCard('card3')" class="card-item cursor-pointer bg-slate-800 border-2 border-slate-700 hover:border-slate-500 rounded-2xl transition-all hover:shadow-lg relative group">
            <div class="absolute -top-3 left-4 bg-slate-700 text-slate-300 text-[10px] font-bold px-2 py-0.5 rounded-full uppercase tracking-wider shadow">
              Card 3
            </div>
            <div id="card3-target" class="transition-all duration-200">
              <h3 id="card3-title" class="text-base font-bold text-white mb-2">Card 3: Custom Spacing & Border</h3>
              <p id="card3-text" class="text-xs text-slate-300 leading-relaxed">
                Customized card showing prominent padding, dotted border styles, and wider outer margin gaps.
              </p>
            </div>
          </div>
        </div>
      </div>

      <!-- MAIN CONTROLS & DEVTOOLS DIAGRAM GRID -->
      <div class="grid grid-cols-1 lg:grid-cols-12 gap-6">

        <!-- LEFT PANEL: CONTROLS (5 Cols) -->
        <div class="lg:col-span-5 bg-slate-800 border border-slate-700/80 rounded-2xl p-5 shadow-lg space-y-5">
          <div class="flex items-center justify-between border-b border-slate-700 pb-3">
            <h3 class="font-bold text-slate-100 text-sm flex items-center gap-2">
              <i class="fa-solid fa-sliders text-blue-400"></i>
              Box Model Controls
            </h3>
            <span id="active-card-label" class="text-xs font-mono font-bold text-blue-400 bg-blue-950/60 border border-blue-800/80 px-2.5 py-1 rounded-md">
              Editing: Card 1
            </span>
          </div>

          <!-- Box Sizing Mode -->
          <div class="space-y-1.5">
            <label class="text-xs font-semibold text-slate-300 flex justify-between">
              <span>Box Sizing Mode (`box-sizing`)</span>
              <span class="text-slate-400 font-mono text-[11px]" id="boxSizingVal">content-box</span>
            </label>
            <div class="grid grid-cols-2 gap-2 p-1 bg-slate-900 rounded-xl border border-slate-700/60">
              <button id="btnSizingContent" onclick="updateProp('boxSizing', 'content-box')" class="py-1.5 text-xs font-medium rounded-lg transition-all bg-blue-600 text-white shadow">
                content-box
              </button>
              <button id="btnSizingBorder" onclick="updateProp('boxSizing', 'border-box')" class="py-1.5 text-xs font-medium rounded-lg text-slate-400 hover:text-white transition-all">
                border-box
              </button>
            </div>
          </div>

          <!-- Dimensions (Width & Height) -->
          <div class="space-y-3 pt-1">
            <div class="flex justify-between items-center">
              <label class="text-xs font-semibold text-slate-300">Declared Width & Height</label>
            </div>
            
            <div class="space-y-2">
              <div>
                <div class="flex justify-between text-xs text-slate-400 mb-1">
                  <span>Width</span>
                  <span id="val-width" class="font-mono text-blue-300">280px</span>
                </div>
                <input type="range" id="input-width" min="150" max="450" step="5" class="w-full h-1.5 bg-slate-700 rounded-lg appearance-none cursor-pointer" oninput="updateProp('width', this.value)" />
              </div>

              <div>
                <div class="flex justify-between text-xs text-slate-400 mb-1">
                  <span>Height (Auto or Fixed)</span>
                  <span id="val-height" class="font-mono text-blue-300">auto</span>
                </div>
                <input type="range" id="input-height" min="80" max="300" step="5" class="w-full h-1.5 bg-slate-700 rounded-lg appearance-none cursor-pointer" oninput="updateProp('height', this.value)" />
              </div>
            </div>
          </div>

          <hr class="border-slate-700/60" />

          <!-- Padding -->
          <div class="space-y-2">
            <div class="flex justify-between items-center">
              <label class="text-xs font-semibold text-emerald-400 flex items-center gap-1.5">
                <span class="w-2.5 h-2.5 rounded-sm bg-emerald-500 inline-block"></span>
                Padding (Inner Space)
              </label>
              <span id="val-padding" class="font-mono text-xs text-emerald-300">16px</span>
            </div>
            <input type="range" id="input-padding" min="0" max="60" step="2" class="w-full h-1.5 bg-slate-700 rounded-lg appearance-none cursor-pointer" oninput="updateProp('padding', this.value)" />
          </div>

          <!-- Border -->
          <div class="space-y-2">
            <div class="flex justify-between items-center">
              <label class="text-xs font-semibold text-amber-300 flex items-center gap-1.5">
                <span class="w-2.5 h-2.5 rounded-sm bg-amber-400 inline-block"></span>
                Border Width
              </label>
              <span id="val-borderWidth" class="font-mono text-xs text-amber-300">4px</span>
            </div>
            <input type="range" id="input-borderWidth" min="0" max="24" step="1" class="w-full h-1.5 bg-slate-700 rounded-lg appearance-none cursor-pointer" oninput="updateProp('borderWidth', this.value)" />

            <div class="grid grid-cols-2 gap-2 pt-1">
              <div>
                <label class="text-[10px] text-slate-400 uppercase font-semibold block mb-1">Style</label>
                <select id="input-borderStyle" onchange="updateProp('borderStyle', this.value)" class="w-full bg-slate-900 border border-slate-700 rounded-lg px-2 py-1 text-xs text-slate-200">
                  <option value="solid">Solid</option>
                  <option value="dashed">Dashed</option>
                  <option value="dotted">Dotted</option>
                  <option value="double">Double</option>
                </select>
              </div>
              <div>
                <label class="text-[10px] text-slate-400 uppercase font-semibold block mb-1">Color</label>
                <input type="color" id="input-borderColor" onchange="updateProp('borderColor', this.value)" class="w-full h-7 bg-slate-900 border border-slate-700 rounded-lg p-0.5 cursor-pointer" />
              </div>
            </div>
          </div>

          <!-- Margin -->
          <div class="space-y-2">
            <div class="flex justify-between items-center">
              <label class="text-xs font-semibold text-orange-400 flex items-center gap-1.5">
                <span class="w-2.5 h-2.5 rounded-sm bg-orange-500 inline-block"></span>
                Margin (Outer Space)
              </label>
              <span id="val-margin" class="font-mono text-xs text-orange-300">12px</span>
            </div>
            <input type="range" id="input-margin" min="0" max="50" step="2" class="w-full h-1.5 bg-slate-700 rounded-lg appearance-none cursor-pointer" oninput="updateProp('margin', this.value)" />
          </div>

          <!-- Reset Card Preset Button -->
          <button onclick="resetSelectedCard()" class="w-full py-2 bg-slate-700 hover:bg-slate-600 text-slate-200 text-xs font-medium rounded-xl transition-all flex items-center justify-center gap-2 mt-2">
            <i class="fa-solid fa-rotate-left"></i> Reset Card to Defaults
          </button>
        </div>

        <!-- RIGHT PANEL: DEVTOOLS DIAGRAM & FORMULA (7 Cols) -->
        <div class="lg:col-span-7 space-y-6">

          <!-- DEVTOOLS BOX MODEL DIAGRAM -->
          <div class="bg-slate-800 border border-slate-700/80 rounded-2xl p-5 shadow-lg space-y-4">
            <div class="flex items-center justify-between border-b border-slate-700 pb-3">
              <h3 class="font-bold text-slate-100 text-sm flex items-center gap-2">
                <i class="fa-solid fa-microscope text-emerald-400"></i>
                DevTools Live Box Model Diagram
              </h3>
              <span class="text-xs text-slate-400 font-mono">Steps 5 & 6</span>
            </div>

            <!-- Nested DevTools Container -->
            <div class="p-4 bg-slate-950/70 rounded-xl flex items-center justify-center overflow-x-auto min-h-[320px]">
              
              <!-- MARGIN LAYER (Orange/Peach) -->
              <div id="diagram-margin" class="dev-margin p-5 rounded-lg transition-all w-full max-w-lg text-center relative group">
                <span class="absolute top-1 left-2 text-[10px] font-bold font-mono uppercase text-orange-900 tracking-wider">Margin</span>
                <span id="diag-margin-val" class="absolute top-1 right-2 text-[10px] font-mono font-semibold text-orange-950">12</span>

                <!-- BORDER LAYER (Yellow/Gold) -->
                <div id="diagram-border" class="dev-border p-5 rounded-md transition-all relative">
                  <span class="absolute top-1 left-2 text-[10px] font-bold font-mono uppercase text-amber-950 tracking-wider">Border</span>
                  <span id="diag-border-val" class="absolute top-1 right-2 text-[10px] font-mono font-semibold text-amber-950">4</span>

                  <!-- PADDING LAYER (Green) -->
                  <div id="diagram-padding" class="dev-padding p-5 rounded transition-all relative">
                    <span class="absolute top-1 left-2 text-[10px] font-bold font-mono uppercase text-emerald-950 tracking-wider">Padding</span>
                    <span id="diag-padding-val" class="absolute top-1 right-2 text-[10px] font-mono font-semibold text-emerald-950">16</span>

                    <!-- CONTENT LAYER (Blue) -->
                    <div id="diagram-content" class="dev-content py-6 px-4 rounded transition-all flex flex-col items-center justify-center min-h-[70px] relative">
                      <span class="absolute top-1 left-2 text-[10px] font-bold font-mono uppercase text-blue-950 tracking-wider">Content</span>
                      <div class="text-center font-mono text-xs font-bold text-blue-950 mt-2">
                        <span id="diag-content-width">280</span> × <span id="diag-content-height">85</span>
                      </div>
                      <span class="text-[10px] text-blue-900/80 font-mono mt-0.5">(px)</span>
                    </div>

                  </div>
                </div>
              </div>

            </div>

            <!-- DIAGRAM LEGEND & LABELS -->
            <div class="grid grid-cols-2 sm:grid-cols-4 gap-2 text-center text-xs">
              <div class="p-2 rounded-lg bg-blue-950/40 border border-blue-800/40 text-blue-300">
                <div class="font-bold flex items-center justify-center gap-1 text-[11px]">
                  <span class="w-2.5 h-2.5 rounded-full bg-blue-400"></span> Content
                </div>
                <p class="text-[10px] text-slate-400 mt-0.5">Text & inner elements area</p>
              </div>

              <div class="p-2 rounded-lg bg-emerald-950/40 border border-emerald-800/40 text-emerald-300">
                <div class="font-bold flex items-center justify-center gap-1 text-[11px]">
                  <span class="w-2.5 h-2.5 rounded-full bg-emerald-400"></span> Padding
                </div>
                <p class="text-[10px] text-slate-400 mt-0.5">Transparent space inside border</p>
              </div>

              <div class="p-2 rounded-lg bg-amber-950/40 border border-amber-800/40 text-amber-300">
                <div class="font-bold flex items-center justify-center gap-1 text-[11px]">
                  <span class="w-2.5 h-2.5 rounded-full bg-amber-400"></span> Border
                </div>
                <p class="text-[10px] text-slate-400 mt-0.5">Edge around padding & content</p>
              </div>

              <div class="p-2 rounded-lg bg-orange-950/40 border border-orange-800/40 text-orange-300">
                <div class="font-bold flex items-center justify-center gap-1 text-[11px]">
                  <span class="w-2.5 h-2.5 rounded-full bg-orange-400"></span> Margin
                </div>
                <p class="text-[10px] text-slate-400 mt-0.5">Transparent space outside border</p>
              </div>
            </div>
          </div>

          <!-- RENDERED DIMENSIONS & MATHEMATICAL BREAKDOWN -->
          <div class="bg-slate-800 border border-slate-700/80 rounded-2xl p-5 shadow-lg space-y-4">
            <h3 class="font-bold text-slate-100 text-sm flex items-center gap-2">
              <i class="fa-solid fa-calculator text-indigo-400"></i>
              Rendered Dimension Calculation
            </h3>

            <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
              <!-- Rendered Total Width Box -->
              <div class="bg-slate-900/90 border border-slate-700/80 p-4 rounded-xl space-y-2">
                <div class="flex justify-between items-center text-xs text-slate-400">
                  <span>Total Space Occupied (Width)</span>
                  <span class="bg-indigo-950 text-indigo-300 font-mono text-[10px] px-2 py-0.5 rounded font-bold border border-indigo-800/50">Width Formula</span>
                </div>
                <div class="text-2xl font-bold font-mono text-white flex items-baseline gap-1">
                  <span id="calc-total-width">320</span>
                  <span class="text-xs text-slate-400 font-normal">px</span>
                </div>
                <div id="calc-width-breakdown" class="text-[11px] font-mono text-slate-400 border-t border-slate-800 pt-2 space-y-0.5">
                  <!-- JS Injected breakdown -->
                </div>
              </div>

              <!-- Rendered Total Height Box -->
              <div class="bg-slate-900/90 border border-slate-700/80 p-4 rounded-xl space-y-2">
                <div class="flex justify-between items-center text-xs text-slate-400">
                  <span>Total Space Occupied (Height)</span>
                  <span class="bg-indigo-950 text-indigo-300 font-mono text-[10px] px-2 py-0.5 rounded font-bold border border-indigo-800/50">Height Formula</span>
                </div>
                <div class="text-2xl font-bold font-mono text-white flex items-baseline gap-1">
                  <span id="calc-total-height">125</span>
                  <span class="text-xs text-slate-400 font-normal">px</span>
                </div>
                <div id="calc-height-breakdown" class="text-[11px] font-mono text-slate-400 border-t border-slate-800 pt-2 space-y-0.5">
                  <!-- JS Injected breakdown -->
                </div>
              </div>
            </div>
          </div>

        </div>
      </div>
    </div>

    <!-- TAB 2: CONTENT-BOX VS BORDER-BOX COMPARISON VIEW -->
    <div id="tab-compare" class="tab-content hidden space-y-6">
      <div class="bg-slate-800 border border-slate-700/80 rounded-2xl p-6 shadow-lg space-y-6">
        <div>
          <h2 class="text-lg font-bold text-white flex items-center gap-2">
            <i class="fa-solid fa-code-compare text-blue-400"></i>
            Step 4: Side-by-Side Box-Sizing Comparison
          </h2>
          <p class="text-xs sm:text-sm text-slate-300 mt-1">
            Comparing two elements with <strong>identical CSS properties</strong> (<code class="text-blue-300">width: 300px</code>, <code class="text-emerald-300">padding: 20px</code>, <code class="text-amber-300">border: 8px solid</code>).
          </p>
        </div>

        <!-- Comparative Demo Cards -->
        <div class="grid grid-cols-1 md:grid-cols-2 gap-8 pt-2">
          
          <!-- Content-Box Card -->
          <div class="bg-slate-900 border border-slate-700 p-5 rounded-2xl space-y-4 relative">
            <div class="flex justify-between items-center border-b border-slate-800 pb-2">
              <span class="text-xs font-bold text-blue-400 font-mono">box-sizing: content-box</span>
              <span class="text-xs bg-blue-950 text-blue-300 border border-blue-800 px-2 py-0.5 rounded">Default Standard</span>
            </div>

            <!-- Demo Card Target -->
            <div class="overflow-x-auto py-2">
              <div style="box-sizing: content-box; width: 280px; padding: 20px; border: 6px solid #fdd663; margin: 10px 0; background-color: #1e293b; color: white;" class="rounded-lg shadow">
                <h4 class="font-bold text-sm text-blue-300 mb-1">Content-Box Element</h4>
                <p class="text-xs text-slate-300">Declared width applies <em>only</em> to content area. Padding and border expand the box total width!</p>
              </div>
            </div>

            <!-- Dimensions Summary -->
            <div class="bg-slate-950 p-3 rounded-xl font-mono text-xs space-y-1.5 text-slate-300">
              <div class="flex justify-between text-blue-400 font-bold border-b border-slate-800 pb-1">
                <span>Rendered Total Width:</span>
                <span>332px</span>
              </div>
              <div class="flex justify-between text-[11px] text-slate-400">
                <span>Declared Width:</span>
                <span>280px</span>
              </div>
              <div class="flex justify-between text-[11px] text-emerald-400">
                <span>Left + Right Padding:</span>
                <span>+ 40px (20px × 2)</span>
              </div>
              <div class="flex justify-between text-[11px] text-amber-300">
                <span>Left + Right Border:</span>
                <span>+ 12px (6px × 2)</span>
              </div>
            </div>
          </div>

          <!-- Border-Box Card -->
          <div class="bg-slate-900 border border-slate-700 p-5 rounded-2xl space-y-4 relative">
            <div class="flex justify-between items-center border-b border-slate-800 pb-2">
              <span class="text-xs font-bold text-emerald-400 font-mono">box-sizing: border-box</span>
              <span class="text-xs bg-emerald-950 text-emerald-300 border border-emerald-800 px-2 py-0.5 rounded">Modern Best Practice</span>
            </div>

            <!-- Demo Card Target -->
            <div class="overflow-x-auto py-2">
              <div style="box-sizing: border-box; width: 280px; padding: 20px; border: 6px solid #fdd663; margin: 10px 0; background-color: #1e293b; color: white;" class="rounded-lg shadow">
                <h4 class="font-bold text-sm text-emerald-300 mb-1">Border-Box Element</h4>
                <p class="text-xs text-slate-300">Declared width includes content + padding + border. Box size remains strictly predictable!</p>
              </div>
            </div>

            <!-- Dimensions Summary -->
            <div class="bg-slate-950 p-3 rounded-xl font-mono text-xs space-y-1.5 text-slate-300">
              <div class="flex justify-between text-emerald-400 font-bold border-b border-slate-800 pb-1">
                <span>Rendered Total Width:</span>
                <span>280px</span>
              </div>
              <div class="flex justify-between text-[11px] text-slate-400">
                <span>Declared Width:</span>
                <span>280px</span>
              </div>
              <div class="flex justify-between text-[11px] text-blue-300">
                <span>Adjusted Content Area:</span>
                <span>228px (280 - 40 - 12)</span>
              </div>
              <div class="flex justify-between text-[11px] text-slate-400">
                <span>Padding & Border:</span>
                <span>Absorbed Inside</span>
              </div>
            </div>
          </div>

        </div>

        <!-- Explanation Summary Box -->
        <div class="bg-slate-900/60 border border-slate-700/60 p-4 rounded-xl space-y-2 text-xs sm:text-sm text-slate-300">
          <h4 class="font-bold text-white flex items-center gap-2">
            <i class="fa-solid fa-lightbulb text-amber-400"></i>
            Key Takeaways for Assignment 6.1
          </h4>
          <ul class="list-disc pl-5 space-y-1 text-slate-300">
            <li><strong>Content-Box:</strong> Layout elements become larger than their declared <code class="text-blue-300">width</code> whenever padding or borders are added. This often leads to accidental wrapping or broken grid alignment.</li>
            <li><strong>Border-Box:</strong> The browser automatically shrinks the inner content area so that <code class="text-emerald-300">Total Width = Declared Width</code>. Most modern responsive CSS frameworks use <code class="text-slate-200">* { box-sizing: border-box; }</code> by default.</li>
          </ul>
        </div>
      </div>
    </div>

    <!-- TAB 3: SUBMISSION EXPORT & ANNOTATED DIAGRAM GENERATOR -->
    <div id="tab-export" class="tab-content hidden space-y-6">
      <div class="bg-slate-800 border border-slate-700/80 rounded-2xl p-6 shadow-lg space-y-6">
        <div>
          <h2 class="text-lg font-bold text-white flex items-center gap-2">
            <i class="fa-solid fa-file-arrow-down text-emerald-400"></i>
            Step 6: Submission Code & Diagram Generator
          </h2>
          <p class="text-xs sm:text-sm text-slate-300 mt-1">
            Export standalone clean HTML/CSS files and download an annotated image of the DevTools Box Model diagram for your assignment submission.
          </p>
        </div>

        <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">

          <!-- ANNOTATED DIAGRAM EXPORTER -->
          <div class="bg-slate-900 border border-slate-700 p-5 rounded-2xl space-y-4">
            <h3 class="font-bold text-sm text-slate-100 flex items-center gap-2">
              <i class="fa-solid fa-image text-amber-400"></i>
              Annotated Diagram Generator (Canvas)
            </h3>
            <p class="text-xs text-slate-400">
              Generates an annotated box model diagram explaining content, padding, border, and margin.
            </p>

            <!-- CANVAS PREVIEW CONTAINER -->
            <div class="bg-slate-950 p-2 rounded-xl flex items-center justify-center border border-slate-800">
              <canvas id="exportCanvas" width="500" height="340" class="max-w-full rounded-lg shadow-md bg-slate-900"></canvas>
            </div>

            <button onclick="downloadAnnotatedDiagram()" class="w-full py-2.5 bg-emerald-600 hover:bg-emerald-500 text-white font-semibold text-xs rounded-xl shadow-lg transition-all flex items-center justify-center gap-2">
              <i class="fa-solid fa-download"></i> Download Labeled Diagram (PNG)
            </button>
          </div>

          <!-- STANDALONE HTML/CSS CODE GENERATOR -->
          <div class="bg-slate-900 border border-slate-700 p-5 rounded-2xl space-y-4 flex flex-col justify-between">
            <div class="space-y-3">
              <h3 class="font-bold text-sm text-slate-100 flex items-center gap-2">
                <i class="fa-solid fa-code text-blue-400"></i>
                Download Assignment Code Files
              </h3>
              <p class="text-xs text-slate-400">
                Get cleanly structured HTML and CSS files reflecting the 3 cards and custom box model properties built in this app.
              </p>

              <!-- Code Preview Tabs -->
              <div class="bg-slate-950 border border-slate-800 rounded-xl p-3 space-y-2 font-mono text-[11px] text-slate-300 max-h-52 overflow-y-auto">
                <div class="text-slate-500 text-[10px] uppercase font-bold">// Preview: HTML & CSS Structure</div>
                <pre id="codePreviewText" class="text-slate-300 whitespace-pre-wrap"></pre>
              </div>
            </div>

            <div class="grid grid-cols-2 gap-3 pt-2">
              <button onclick="downloadHTMLFile()" class="py-2.5 bg-blue-600 hover:bg-blue-500 text-white font-semibold text-xs rounded-xl shadow transition-all flex items-center justify-center gap-2">
                <i class="fa-solid fa-file-code"></i> Download index.html
              </button>
              <button onclick="downloadCSSFile()" class="py-2.5 bg-indigo-600 hover:bg-indigo-500 text-white font-semibold text-xs rounded-xl shadow transition-all flex items-center justify-center gap-2">
                <i class="fa-solid fa-file-lines"></i> Download styles.css
              </button>
            </div>
          </div>

        </div>
      </div>
    </div>

  </main>

  <!-- Footer -->
  <footer class="bg-slate-800/60 border-t border-slate-700/60 py-4 mt-auto">
    <div class="max-w-7xl mx-auto px-4 text-center text-xs text-slate-400 flex flex-col sm:flex-row items-center justify-between gap-2">
      <span>Assignment 6.1 — Interactive Box Model Inspector</span>
      <span>Built with HTML, CSS & JavaScript</span>
    </div>
  </footer>

  <!-- JAVASCRIPT APP LOGIC -->
  <script>
    // STATE MANAGEMENT FOR CARDS
    const cardData = {
      card1: {
        id: 'card1',
        title: 'Card 1: Content-Box Basics',
        text: 'Demonstrating classic content-box where padding and border are added onto specified width.',
        boxSizing: 'content-box',
        width: 280,
        height: 'auto',
        padding: 16,
        borderWidth: 4,
        borderStyle: 'solid',
        borderColor: '#fdd663',
        margin: 12
      },
      card2: {
        id: 'card2',
        title: 'Card 2: Border-Box Precision',
        text: 'Demonstrating modern border-box where padding and border are absorbed inside declared width.',
        boxSizing: 'border-box',
        width: 280,
        height: 'auto',
        padding: 20,
        borderWidth: 6,
        borderStyle: 'solid',
        borderColor: '#34d399',
        margin: 16
      },
      card3: {
        id: 'card3',
        title: 'Card 3: Custom Spacing & Border',
        text: 'Customized card showing prominent padding, dotted border styles, and wider outer margin gaps.',
        boxSizing: 'content-box',
        width: 260,
        height: 'auto',
        padding: 24,
        borderWidth: 5,
        borderStyle: 'dotted',
        borderColor: '#60a5fa',
        margin: 20
      }
    };

    let activeCardId = 'card1';

    // INITIALIZATION ON WINDOW LOAD
    window.onload = function() {
      // Apply initial styling to all cards
      Object.keys(cardData).forEach(id => applyCardStyles(id));
      
      // Load active card values into control sliders
      syncControlsToActiveCard();

      // Update DevTools visual diagram
      updateDiagramAndCalculations();

      // Render submission code preview and canvas canvas diagram
      updateCodePreview();
      drawAnnotatedDiagram();
    };

    // TAB SWITCHING LOGIC
    function switchTab(tabName) {
      document.querySelectorAll('.tab-content').forEach(el => el.classList.add('hidden'));
      document.getElementById('tab-' + tabName).classList.remove('hidden');

      // Update navigation button active states
      const navButtons = {
        inspector: 'btnNavInspect',
        compare: 'btnNavCompare',
        export: 'btnNavExport'
      };

      Object.keys(navButtons).forEach(key => {
        const btn = document.getElementById(navButtons[key]);
        if (key === tabName) {
          btn.className = 'tab-btn px-3.5 py-1.5 rounded-lg text-xs sm:text-sm font-medium bg-blue-600 text-white shadow-sm hover:bg-blue-500 transition-all flex items-center gap-1.5';
        } else {
          btn.className = 'tab-btn px-3.5 py-1.5 rounded-lg text-xs sm:text-sm font-medium bg-slate-700 text-slate-300 hover:bg-slate-600 transition-all flex items-center gap-1.5';
        }
      });

      if (tabName === 'export') {
        drawAnnotatedDiagram();
        updateCodePreview();
      }
    }

    // SELECT CARD
    function selectCard(cardId) {
      activeCardId = cardId;

      // Update visual highlight on card containers
      Object.keys(cardData).forEach(id => {
        const cardElem = document.getElementById('card-' + id);
        const badge = cardElem.querySelector('.absolute');
        if (id === cardId) {
          cardElem.className = 'card-item cursor-pointer bg-slate-800 border-2 border-blue-500 rounded-2xl transition-all shadow-lg relative group';
          if (badge) {
            badge.className = 'absolute -top-3 left-4 bg-blue-600 text-white text-[10px] font-bold px-2 py-0.5 rounded-full uppercase tracking-wider shadow';
            badge.innerText = cardId.toUpperCase() + ' (Selected)';
          }
        } else {
          cardElem.className = 'card-item cursor-pointer bg-slate-800 border-2 border-slate-700 hover:border-slate-500 rounded-2xl transition-all hover:shadow-lg relative group';
          if (badge) {
            badge.className = 'absolute -top-3 left-4 bg-slate-700 text-slate-300 text-[10px] font-bold px-2 py-0.5 rounded-full uppercase tracking-wider shadow';
            badge.innerText = id.toUpperCase();
          }
        }
      });

      document.getElementById('active-card-label').innerText = 'Editing: ' + cardData[cardId].title.split(':')[0];

      syncControlsToActiveCard();
      updateDiagramAndCalculations();
    }

    // SYNC FORM CONTROLS WITH CURRENT ACTIVE CARD
    function syncControlsToActiveCard() {
      const data = cardData[activeCardId];

      document.getElementById('input-width').value = data.width;
      document.getElementById('val-width').innerText = data.width + 'px';

      document.getElementById('input-height').value = data.height === 'auto' ? 120 : data.height;
      document.getElementById('val-height').innerText = data.height === 'auto' ? 'auto' : data.height + 'px';

      document.getElementById('input-padding').value = data.padding;
      document.getElementById('val-padding').innerText = data.padding + 'px';

      document.getElementById('input-borderWidth').value = data.borderWidth;
      document.getElementById('val-borderWidth').innerText = data.borderWidth + 'px';

      document.getElementById('input-borderStyle').value = data.borderStyle;
      document.getElementById('input-borderColor').value = data.borderColor;

      document.getElementById('input-margin').value = data.margin;
      document.getElementById('val-margin').innerText = data.margin + 'px';

      document.getElementById('boxSizingVal').innerText = data.boxSizing;

      // Toggle Box-Sizing Button Styles
      const btnContent = document.getElementById('btnSizingContent');
      const btnBorder = document.getElementById('btnSizingBorder');

      if (data.boxSizing === 'content-box') {
        btnContent.className = 'py-1.5 text-xs font-medium rounded-lg transition-all bg-blue-600 text-white shadow';
        btnBorder.className = 'py-1.5 text-xs font-medium rounded-lg text-slate-400 hover:text-white transition-all';
      } else {
        btnBorder.className = 'py-1.5 text-xs font-medium rounded-lg transition-all bg-emerald-600 text-white shadow';
        btnContent.className = 'py-1.5 text-xs font-medium rounded-lg text-slate-400 hover:text-white transition-all';
      }
    }

    // UPDATE PROPERTY OF SELECTED CARD
    function updateProp(prop, value) {
      const data = cardData[activeCardId];

      if (prop === 'width' || prop === 'padding' || prop === 'borderWidth' || prop === 'margin') {
        data[prop] = parseInt(value, 10);
      } else if (prop === 'height') {
        data[prop] = value == 120 ? 'auto' : parseInt(value, 10);
      } else {
        data[prop] = value;
      }

      applyCardStyles(activeCardId);
      syncControlsToActiveCard();
      updateDiagramAndCalculations();
      updateCodePreview();
    }

    // APPLY STYLES TO TARGET HTML CARD
    function applyCardStyles(cardId) {
      const data = cardData[cardId];
      const target = document.getElementById(cardId + '-target');

      target.style.boxSizing = data.boxSizing;
      target.style.width = data.width + 'px';
      target.style.height = data.height === 'auto' ? 'auto' : data.height + 'px';
      target.style.padding = data.padding + 'px';
      target.style.borderWidth = data.borderWidth + 'px';
      target.style.borderStyle = data.borderStyle;
      target.style.borderColor = data.borderColor;
      target.style.margin = data.margin + 'px';
    }

    // UPDATE DEVTOOLS BOX MODEL DIAGRAM & CALCULATED TOTALS
    function updateDiagramAndCalculations() {
      const data = cardData[activeCardId];
      const target = document.getElementById(activeCardId + '-target');

      // Get actual rendered inner content dimensions
      const contentWidth = data.boxSizing === 'border-box' 
        ? Math.max(0, data.width - (data.padding * 2) - (data.borderWidth * 2))
        : data.width;

      const renderedHeight = target.offsetHeight || 100;
      const contentHeight = data.boxSizing === 'border-box'
        ? Math.max(0, renderedHeight - (data.padding * 2) - (data.borderWidth * 2))
        : renderedHeight - (data.padding * 2) - (data.borderWidth * 2);

      // Update DevTools Diagram numeric overlays
      document.getElementById('diag-margin-val').innerText = data.margin;
      document.getElementById('diag-border-val').innerText = data.borderWidth;
      document.getElementById('diag-padding-val').innerText = data.padding;
      document.getElementById('diag-content-width').innerText = Math.round(contentWidth);
      document.getElementById('diag-content-height').innerText = Math.round(Math.max(10, contentHeight));

      // Compute total width and height occupied
      let totalRenderedWidth, totalRenderedHeight;
      if (data.boxSizing === 'content-box') {
        totalRenderedWidth = data.width + (data.padding * 2) + (data.borderWidth * 2);
        totalRenderedHeight = (data.height === 'auto' ? renderedHeight : data.height) + (data.padding * 2) + (data.borderWidth * 2);
      } else {
        totalRenderedWidth = data.width;
        totalRenderedHeight = data.height === 'auto' ? renderedHeight : data.height;
      }

      document.getElementById('calc-total-width').innerText = Math.round(totalRenderedWidth);
      document.getElementById('calc-total-height').innerText = Math.round(totalRenderedHeight);

      // Render formula breakdown
      if (data.boxSizing === 'content-box') {
        document.getElementById('calc-width-breakdown').innerHTML = `
          <div class="flex justify-between text-blue-300"><span>Content Width:</span><span>${data.width}px</span></div>
          <div class="flex justify-between text-emerald-300"><span>Padding (2×):</span><span>+ ${data.padding * 2}px</span></div>
          <div class="flex justify-between text-amber-300"><span>Border (2×):</span><span>+ ${data.borderWidth * 2}px</span></div>
        `;
        document.getElementById('calc-height-breakdown').innerHTML = `
          <div class="flex justify-between text-blue-300"><span>Content Height:</span><span>~${Math.round(contentHeight)}px</span></div>
          <div class="flex justify-between text-emerald-300"><span>Padding (2×):</span><span>+ ${data.padding * 2}px</span></div>
          <div class="flex justify-between text-amber-300"><span>Border (2×):</span><span>+ ${data.borderWidth * 2}px</span></div>
        `;
      } else {
        document.getElementById('calc-width-breakdown').innerHTML = `
          <div class="flex justify-between text-emerald-300"><span>Declared Width (Fixed):</span><span>${data.width}px</span></div>
          <div class="flex justify-between text-blue-300"><span>Inner Content Width:</span><span>${Math.round(contentWidth)}px</span></div>
          <div class="flex justify-between text-slate-400"><span>Padding & Border:</span><span>Absorbed Inside</span></div>
        `;
        document.getElementById('calc-height-breakdown').innerHTML = `
          <div class="flex justify-between text-emerald-300"><span>Declared Height:</span><span>${data.height === 'auto' ? 'Auto (' + Math.round(totalRenderedHeight) + 'px)' : data.height + 'px'}</span></div>
          <div class="flex justify-between text-blue-300"><span>Inner Content Height:</span><span>${Math.round(contentHeight)}px</span></div>
          <div class="flex justify-between text-slate-400"><span>Padding & Border:</span><span>Absorbed Inside</span></div>
        `;
      }
    }

    // RESET SELECTED CARD
    function resetSelectedCard() {
      const defaults = {
        card1: { boxSizing: 'content-box', width: 280, height: 'auto', padding: 16, borderWidth: 4, borderStyle: 'solid', borderColor: '#fdd663', margin: 12 },
        card2: { boxSizing: 'border-box', width: 280, height: 'auto', padding: 20, borderWidth: 6, borderStyle: 'solid', borderColor: '#34d399', margin: 16 },
        card3: { boxSizing: 'content-box', width: 260, height: 'auto', padding: 24, borderWidth: 5, borderStyle: 'dotted', borderColor: '#60a5fa', margin: 20 }
      };

      Object.assign(cardData[activeCardId], defaults[activeCardId]);
      applyCardStyles(activeCardId);
      syncControlsToActiveCard();
      updateDiagramAndCalculations();
      updateCodePreview();
    }

    // CANVAS ANNOTATED DIAGRAM GENERATOR FOR SUBMISSION STEP 6
    function drawAnnotatedDiagram() {
      const canvas = document.getElementById('exportCanvas');
      if (!canvas) return;
      const ctx = canvas.getContext('2d');

      // Clear canvas background
      ctx.fillStyle = '#0f172a';
      ctx.fillRect(0, 0, canvas.width, canvas.height);

      // Title
      ctx.fillStyle = '#ffffff';
      ctx.font = 'bold 14px Inter, sans-serif';
      ctx.fillText('CSS BOX MODEL DIAGRAM — ASSIGNMENT 6.1', 20, 28);

      // Box dimensions
      const cx = 250;
      const cy = 180;

      // Outer Margin Box
      ctx.fillStyle = 'rgba(249, 171, 0, 0.35)';
      ctx.strokeStyle = '#f9ab00';
      ctx.lineWidth = 2;
      ctx.strokeRect(cx - 180, cy - 110, 360, 220);
      ctx.fillRect(cx - 180, cy - 110, 360, 220);
      ctx.fillStyle = '#f9ab00';
      ctx.font = 'bold 11px Fira Code, monospace';
      ctx.fillText('MARGIN (Outer Space)', cx - 170, cy - 93);

      // Border Box
      ctx.fillStyle = 'rgba(253, 214, 99, 0.45)';
      ctx.strokeStyle = '#fdd663';
      ctx.strokeRect(cx - 140, cy - 80, 280, 160);
      ctx.fillRect(cx - 140, cy - 80, 280, 160);
      ctx.fillStyle = '#fdd663';
      ctx.fillText('BORDER', cx - 130, cy - 63);

      // Padding Box
      ctx.fillStyle = 'rgba(129, 201, 149, 0.45)';
      ctx.strokeStyle = '#81c995';
      ctx.strokeRect(cx - 105, cy - 55, 210, 110);
      ctx.fillRect(cx - 105, cy - 55, 210, 110);
      ctx.fillStyle = '#81c995';
      ctx.fillText('PADDING (Inner Space)', cx - 95, cy - 40);

      // Content Box
      ctx.fillStyle = 'rgba(138, 180, 248, 0.6)';
      ctx.strokeStyle = '#8ab4f8';
      ctx.strokeRect(cx - 65, cy - 25, 130, 50);
      ctx.fillRect(cx - 65, cy - 25, 130, 50);
      ctx.fillStyle = '#ffffff';
      ctx.font = 'bold 12px Inter, sans-serif';
      ctx.textAlign = 'center';
      ctx.fillText('CONTENT', cx, cy + 3);
      ctx.font = '10px Fira Code, monospace';
      ctx.fillText('Text / Media Area', cx, cy + 16);
      ctx.textAlign = 'left';
    }

    // DOWNLOAD ANNOTATED DIAGRAM AS PNG
    function downloadAnnotatedDiagram() {
      const canvas = document.getElementById('exportCanvas');
      const imageURL = canvas.toDataURL('image/png');
      const link = document.createElement('a');
      link.href = imageURL;
      link.download = 'Assignment_6_1_Box_Model_Diagram.png';
      document.body.appendChild(link);
      link.click();
      document.body.removeChild(link);
    }

    // UPDATE CODE PREVIEW IN SUBMISSION HELPER
    function updateCodePreview() {
      const previewElem = document.getElementById('codePreviewText');
      if (!previewElem) return;

      const codeStr = `/* Assignment 6.1 Generated CSS */
.card-1 {
  box-sizing: ${cardData.card1.boxSizing};
  width: ${cardData.card1.width}px;
  padding: ${cardData.card1.padding}px;
  border: ${cardData.card1.borderWidth}px ${cardData.card1.borderStyle} ${cardData.card1.borderColor};
  margin: ${cardData.card1.margin}px;
}

.card-2 {
  box-sizing: ${cardData.card2.boxSizing};
  width: ${cardData.card2.width}px;
  padding: ${cardData.card2.padding}px;
  border: ${cardData.card2.borderWidth}px ${cardData.card2.borderStyle} ${cardData.card2.borderColor};
  margin: ${cardData.card2.margin}px;
}

.card-3 {
  box-sizing: ${cardData.card3.boxSizing};
  width: ${cardData.card3.width}px;
  padding: ${cardData.card3.padding}px;
  border: ${cardData.card3.borderWidth}px ${cardData.card3.borderStyle} ${cardData.card3.borderColor};
  margin: ${cardData.card3.margin}px;
}`;
      previewElem.innerText = codeStr;
    }

    // HELPER TO TRIGGER FILE DOWNLOAD
    function triggerDownload(filename, content, mimeType) {
      const blob = new Blob([content], { type: mimeType });
      const url = URL.createObjectURL(blob);
      const a = document.createElement('a');
      a.href = url;
      a.download = filename;
      document.body.appendChild(a);
      a.click();
      document.body.removeChild(a);
      URL.revokeObjectURL(url);
    }

    // DOWNLOAD HTML FILE
    function downloadHTMLFile() {
      const htmlContent = `<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Assignment 6.1 — Box Model Inspection</title>
  <link rel="stylesheet" href="styles.css">
</head>
<body>
  <h1>Assignment 6.1 — Box Model Inspection</h1>
  
  <!-- Container with 3 Cards -->
  <div class="cards-container">
    
    <!-- Card 1 -->
    <div class="card card-1">
      <h2>${cardData.card1.title}</h2>
      <p>${cardData.card1.text}</p>
    </div>

    <!-- Card 2 -->
    <div class="card card-2">
      <h2>${cardData.card2.title}</h2>
      <p>${cardData.card2.text}</p>
    </div>

    <!-- Card 3 -->
    <div class="card card-3">
      <h2>${cardData.card3.title}</h2>
      <p>${cardData.card3.text}</p>
    </div>

  </div>
</body>
</html>`;

      triggerDownload('index.html', htmlContent, 'text/html');
    }

    // DOWNLOAD CSS FILE
    function downloadCSSFile() {
      const cssContent = `/* Stylesheet for Assignment 6.1 */
body {
  font-family: Arial, sans-serif;
  background-color: #0f172a;
  color: #f8fafc;
  padding: 30px;
}

h1 {
  text-align: center;
  margin-bottom: 30px;
}

.cards-container {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
  justify-content: center;
}

.card {
  background-color: #1e293b;
  border-radius: 8px;
}

/* Card 1 Specific Box Model */
.card-1 {
  box-sizing: ${cardData.card1.boxSizing};
  width: ${cardData.card1.width}px;
  padding: ${cardData.card1.padding}px;
  border: ${cardData.card1.borderWidth}px ${cardData.card1.borderStyle} ${cardData.card1.borderColor};
  margin: ${cardData.card1.margin}px;
}

/* Card 2 Specific Box Model */
.card-2 {
  box-sizing: ${cardData.card2.boxSizing};
  width: ${cardData.card2.width}px;
  padding: ${cardData.card2.padding}px;


What was built for your assignment:
Interactive Inspector & DevTools Overlay: A live nested CSS box model diagram (Content, Padding, Border, Margin) colored according to standard browser developer tools.

3 Distinct Cards: Demonstrates different combinations of box-sizing, padding, border, and margin.

content-box vs border-box Comparison Tab: Clear visual side-by-side demonstration highlighting how box dimensions behave under both paradigms.

Submission File & Diagram Exporters: Built-in buttons to generate and download PNG annotated diagrams and clean index.html / styles.css code files ready for submission.







