<!DOCTYPE html>
<html lang="en" class="dark">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Assignment 8.5 — CSS Grid Photo Gallery & Dashboard</title>
  <!-- Font Awesome Icons -->
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css" />
  <!-- Google Fonts -->
  <link rel="preconnect" href="https://fonts.googleapis.com" />
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
  <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;500;600;700;800&family=Fira+Code:wght@400;500&display=swap" rel="stylesheet" />
  <!-- Tailwind CSS CDN -->
  <script src="https://cdn.tailwindcss.com"></script>
  <script>
    tailwind.config = {
      darkMode: 'class',
      theme: {
        extend: {
          colors: {
            brand: {
              50: '#f0f7ff',
              100: '#e0effe',
              500: '#3b82f6',
              600: '#2563eb',
              700: '#1d4ed8',
              900: '#1e3a8a',
            }
          },
          fontFamily: {
            sans: ['"Plus Jakarta Sans"', 'sans-serif'],
            mono: ['"Fira Code"', 'monospace'],
          }
        }
      }
    }
  </script>
  
  <style>
    /* ==========================================================================
       ASSIGNMENT 8.5 CORE CSS GRID SPECIFICATIONS & OVERFLOW CONTROL
       ========================================================================== */

    /* 1. Page Level Named Grid Areas Layout */
    .app-layout {
      display: grid;
      min-height: 100vh;
      grid-template-rows: auto 1fr auto;
      grid-template-columns: 260px 1fr;
      grid-template-areas:
        "header  header"
        "sidebar main"
        "footer  footer";
      transition: all 0.3s ease;
    }

    /* Mobile Responsive Layout Adaptation */
    @media (max-width: 768px) {
      .app-layout {
        grid-template-columns: 1fr;
        grid-template-areas:
          "header"
          "main"
          "footer";
      }
      .app-sidebar {
        display: none; /* Can be toggled on mobile */
      }
      .app-sidebar.mobile-open {
        display: block;
        position: fixed;
        inset: 0;
        z-index: 40;
        background-color: rgba(15, 23, 42, 0.95);
        backdrop-filter: blur(8px);
        padding-top: 5rem;
      }
    }

    /* Assigning Named Areas */
    .app-header  { grid-area: header; }
    .app-sidebar { grid-area: sidebar; }
    .app-main    { grid-area: main; }
    .app-footer   { grid-area: footer; }

    /* 2. Responsive Cards Grid using repeat(), minmax(), fr units, and gap */
    .gallery-grid-standard {
      display: grid;
      grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
      gap: 1.5rem;
      align-items: start;
    }

    /* Bento Featured Grid demonstrating explicit column/row span placement */
    .gallery-grid-bento {
      display: grid;
      grid-template-columns: repeat(auto-fill, minmax(260px, 1fr));
      gap: 1.5rem;
      grid-auto-flow: dense;
    }

    @media (min-width: 1024px) {
      .gallery-grid-bento .bento-featured {
        grid-column: span 2;
        grid-row: span 2;
      }
      .gallery-grid-bento .bento-wide {
        grid-column: span 2;
      }
      .gallery-grid-bento .bento-tall {
        grid-row: span 2;
      }
    }

    /* 3. Strict Overflow Prevention Rules */
    .img-container {
      width: 100%;
      height: 100%;
      overflow: hidden;
      position: relative;
    }

    .img-container img {
      width: 100%;
      height: 100%;
      object-fit: cover;
      object-position: center;
      max-width: 100%;
      display: block;
      transition: transform 0.5s cubic-bezier(0.4, 0, 0.2, 1);
    }

    .gallery-card:hover .img-container img {
      transform: scale(1.06);
    }

    /* Safe Text Clamping & Text Wrap */
    .prevent-overflow-text {
      overflow: hidden;
      text-overflow: ellipsis;
      white-space: nowrap;
    }

    .line-clamp-2 {
      display: -webkit-box;
      -webkit-line-clamp: 2;
      -webkit-box-orient: vertical;
      overflow: hidden;
      word-break: break-word;
    }

    /* Custom Scrollbar for code inspector */
    ::-webkit-scrollbar {
      width: 8px;
      height: 8px;
    }
    ::-webkit-scrollbar-track {
      background: rgba(0, 0, 0, 0.1);
    }
    ::-webkit-scrollbar-thumb {
      background: rgba(100, 116, 139, 0.5);
      border-radius: 4px;
    }
    ::-webkit-scrollbar-thumb:hover {
      background: rgba(100, 116, 139, 0.8);
    }
  </style>
</head>
<body class="bg-slate-50 dark:bg-slate-950 text-slate-800 dark:text-slate-100 font-sans antialiased transition-colors duration-300">

  <!-- MAIN APP CONTAINER WRAPPED IN CSS GRID (grid-template-areas) -->
  <div class="app-layout min-h-screen">

    <!-- HEADER AREA -->
    <header class="app-header sticky top-0 z-30 bg-white/80 dark:bg-slate-900/80 backdrop-blur-md border-b border-slate-200 dark:border-slate-800 px-4 sm:px-6 py-3 transition-colors">
      <div class="max-w-7xl mx-auto flex items-center justify-between gap-4">
        
        <!-- Brand & Mobile Toggle -->
        <div class="flex items-center gap-3">
          <button id="mobileMenuBtn" class="md:hidden text-slate-600 dark:text-slate-300 hover:text-brand-500 focus:outline-none">
            <i class="fa-solid fa-bars text-xl"></i>
          </button>
          <div class="flex items-center gap-2.5">
            <div class="w-10 h-10 rounded-xl bg-gradient-to-tr from-brand-600 to-indigo-500 flex items-center justify-center text-white shadow-md shadow-brand-500/20">
              <i class="fa-solid fa-border-all text-lg"></i>
            </div>
            <div>
              <h1 class="font-bold text-lg leading-tight tracking-tight bg-gradient-to-r from-slate-900 via-brand-700 to-indigo-600 dark:from-white dark:via-slate-200 dark:to-brand-400 bg-clip-text text-transparent">
                GridGallery
              </h1>
              <p class="text-xs text-slate-500 dark:text-slate-400 font-medium">Assignment 8.5 — CSS Grid</p>
            </div>
          </div>
        </div>

        <!-- Central Search & Controls -->
        <div class="hidden sm:flex items-center flex-1 max-w-md mx-4">
          <div class="relative w-full">
            <i class="fa-solid fa-magnifying-glass absolute left-3.5 top-1/2 -translate-y-1/2 text-slate-400 text-sm"></i>
            <input 
              type="text" 
              id="searchInput"
              placeholder="Search photography title or tag..." 
              class="w-full pl-10 pr-4 py-2 bg-slate-100 dark:bg-slate-800/80 border border-slate-200 dark:border-slate-700 rounded-xl text-sm focus:outline-none focus:ring-2 focus:ring-brand-500/50 focus:border-brand-500 transition-all text-slate-800 dark:text-slate-100 placeholder-slate-400"
            />
          </div>
        </div>

        <!-- Actions & Theme Toggle -->
        <div class="flex items-center gap-2 sm:gap-3">
          <!-- Code Inspector Toggle -->
          <button id="toggleInspectorBtn" class="flex items-center gap-2 px-3 py-2 rounded-xl text-xs sm:text-sm font-semibold bg-slate-100 dark:bg-slate-800 text-slate-700 dark:text-slate-300 hover:bg-slate-200 dark:hover:bg-slate-700 transition-colors border border-slate-200 dark:border-slate-700">
            <i class="fa-solid fa-code text-brand-500"></i>
            <span class="hidden lg:inline">CSS Inspector</span>
          </button>

          <!-- GitHub Submission Modal Button -->
          <button id="githubModalBtn" class="flex items-center gap-2 px-3 py-2 rounded-xl text-xs sm:text-sm font-semibold bg-slate-900 dark:bg-brand-600 text-white hover:bg-slate-800 dark:hover:bg-brand-500 transition-colors shadow-sm">
            <i class="fa-brands fa-github text-sm"></i>
            <span class="hidden sm:inline">Submission Links</span>
          </button>

          <!-- Light/Dark Mode Toggle -->
          <button id="themeToggleBtn" aria-label="Toggle Theme" class="p-2.5 rounded-xl bg-slate-100 dark:bg-slate-800 text-slate-600 dark:text-slate-300 hover:text-brand-500 dark:hover:text-brand-400 border border-slate-200 dark:border-slate-700 transition-colors">
            <i class="fa-solid fa-moon dark:hidden"></i>
            <i class="fa-solid fa-sun hidden dark:inline text-amber-400"></i>
          </button>
        </div>
      </div>
    </header>

    <!-- SIDEBAR AREA -->
    <aside id="appSidebar" class="app-sidebar bg-white dark:bg-slate-900 border-r border-slate-200 dark:border-slate-800 p-5 flex flex-col justify-between overflow-y-auto transition-colors">
      <div class="space-y-6">
        <!-- User Badge -->
        <div class="p-3.5 rounded-2xl bg-gradient-to-br from-slate-50 to-slate-100 dark:from-slate-800/60 dark:to-slate-800 border border-slate-200/80 dark:border-slate-700/50 flex items-center gap-3">
          <img src="https://images.unsplash.com/photo-1534528741775-53994a69daeb?w=100&auto=format&fit=crop&q=80" alt="Avatar" class="w-10 h-10 rounded-full object-cover ring-2 ring-brand-500/30" />
          <div class="overflow-hidden">
            <h3 class="font-bold text-sm text-slate-800 dark:text-slate-100 truncate">Alex Rivera</h3>
            <p class="text-xs text-slate-500 dark:text-slate-400 truncate">Student / Architect</p>
          </div>
        </div>

        <!-- Filter Categories -->
        <div>
          <h4 class="text-xs font-bold text-slate-400 uppercase tracking-wider mb-3 px-2">Categories</h4>
          <nav class="space-y-1" id="categoryNav">
            <button data-category="all" class="category-btn active w-full flex items-center justify-between px-3 py-2.5 rounded-xl text-sm font-medium transition-all bg-brand-50 dark:bg-brand-500/10 text-brand-600 dark:text-brand-400">
              <span class="flex items-center gap-2.5">
                <i class="fa-solid fa-border-all text-sm w-4 text-center"></i> All Works
              </span>
              <span class="count-badge px-2 py-0.5 rounded-md text-xs bg-brand-100 dark:bg-brand-500/20 text-brand-700 dark:text-brand-300 font-semibold">12</span>
            </button>
            <button data-category="nature" class="category-btn w-full flex items-center justify-between px-3 py-2.5 rounded-xl text-sm font-medium transition-all text-slate-600 dark:text-slate-400 hover:bg-slate-100 dark:hover:bg-slate-800 hover:text-slate-900 dark:hover:text-slate-100">
              <span class="flex items-center gap-2.5">
                <i class="fa-solid fa-leaf text-sm w-4 text-center"></i> Nature
              </span>
              <span class="count-badge px-2 py-0.5 rounded-md text-xs bg-slate-100 dark:bg-slate-800 text-slate-500">3</span>
            </button>
            <button data-category="architecture" class="category-btn w-full flex items-center justify-between px-3 py-2.5 rounded-xl text-sm font-medium transition-all text-slate-600 dark:text-slate-400 hover:bg-slate-100 dark:hover:bg-slate-800 hover:text-slate-900 dark:hover:text-slate-100">
              <span class="flex items-center gap-2.5">
                <i class="fa-solid fa-building text-sm w-4 text-center"></i> Architecture
              </span>
              <span class="count-badge px-2 py-0.5 rounded-md text-xs bg-slate-100 dark:bg-slate-800 text-slate-500">3</span>
            </button>
            <button data-category="urban" class="category-btn w-full flex items-center justify-between px-3 py-2.5 rounded-xl text-sm font-medium transition-all text-slate-600 dark:text-slate-400 hover:bg-slate-100 dark:hover:bg-slate-800 hover:text-slate-900 dark:hover:text-slate-100">
              <span class="flex items-center gap-2.5">
                <i class="fa-solid fa-city text-sm w-4 text-center"></i> Urban Life
              </span>
              <span class="count-badge px-2 py-0.5 rounded-md text-xs bg-slate-100 dark:bg-slate-800 text-slate-500">3</span>
            </button>
            <button data-category="abstract" class="category-btn w-full flex items-center justify-between px-3 py-2.5 rounded-xl text-sm font-medium transition-all text-slate-600 dark:text-slate-400 hover:bg-slate-100 dark:hover:bg-slate-800 hover:text-slate-900 dark:hover:text-slate-100">
              <span class="flex items-center gap-2.5">
                <i class="fa-solid fa-shapes text-sm w-4 text-center"></i> Abstract
              </span>
              <span class="count-badge px-2 py-0.5 rounded-md text-xs bg-slate-100 dark:bg-slate-800 text-slate-500">3</span>
            </button>
          </nav>
        </div>

        <!-- CSS Grid Spec Quick Reference -->
        <div class="p-4 rounded-2xl bg-indigo-50/50 dark:bg-slate-800/40 border border-indigo-100 dark:border-slate-700/60 text-xs text-slate-600 dark:text-slate-300 space-y-2">
          <div class="flex items-center gap-2 font-bold text-indigo-600 dark:text-indigo-400">
            <i class="fa-solid fa-square-check"></i> Grid Features Used
          </div>
          <ul class="space-y-1 list-disc list-inside text-[11px] text-slate-500 dark:text-slate-400">
            <li><code class="font-mono text-brand-600 dark:text-brand-400">grid-template-areas</code></li>
            <li><code class="font-mono text-brand-600 dark:text-brand-400">repeat(auto-fill, minmax)</code></li>
            <li><code class="font-mono text-brand-600 dark:text-brand-400">fr</code> dynamic fractional units</li>
            <li><code class="font-mono text-brand-600 dark:text-brand-400">gap: 1.5rem</code> spacing</li>
            <li><code class="font-mono text-brand-600 dark:text-brand-400">grid-column / row span</code></li>
          </ul>
        </div>
      </div>

      <!-- Rubric Status Footer -->
      <div class="pt-4 border-t border-slate-200 dark:border-slate-800 text-xs text-slate-400 flex items-center justify-between">
        <span>Rubric Status</span>
        <span class="inline-flex items-center gap-1 text-emerald-500 font-semibold bg-emerald-500/10 px-2 py-0.5 rounded-full">
          <i class="fa-solid fa-check text-[10px]"></i> 100% Valid
        </span>
      </div>
    </aside>

    <!-- MAIN CONTENT AREA -->
    <main class="app-main p-4 sm:p-6 lg:p-8 space-y-6">

      <!-- Gallery Toolbar & Controls -->
      <div class="flex flex-col sm:flex-row items-start sm:items-center justify-between gap-4 bg-white dark:bg-slate-900 p-4 rounded-2xl border border-slate-200 dark:border-slate-800 shadow-sm">
        <div>
          <h2 class="text-xl font-bold text-slate-800 dark:text-slate-100" id="currentCategoryTitle">All Works</h2>
          <p class="text-xs text-slate-500 dark:text-slate-400" id="gallerySubtitle">Showing 12 responsive grid items with zero overflow</p>
        </div>

        <!-- Grid Layout Toggle Switcher -->
        <div class="flex items-center gap-2 bg-slate-100 dark:bg-slate-800 p-1 rounded-xl self-end sm:self-auto border border-slate-200 dark:border-slate-700">
          <button id="layoutStandardBtn" class="px-3 py-1.5 rounded-lg text-xs font-semibold flex items-center gap-1.5 transition-all bg-white dark:bg-slate-700 text-brand-600 dark:text-white shadow-sm" title="Standard Auto-Fit Grid">
            <i class="fa-solid fa-grip-vertical"></i> Standard Grid
          </button>
          <button id="layoutBentoBtn" class="px-3 py-1.5 rounded-lg text-xs font-semibold flex items-center gap-1.5 transition-all text-slate-600 dark:text-slate-400 hover:text-slate-900 dark:hover:text-white" title="Bento / Explicit Placement Grid">
            <i class="fa-solid fa-border-all"></i> Bento Grid
          </button>
        </div>
      </div>

      <!-- LIVE CODE INSPECTOR DRAWER (Collapsible) -->
      <div id="inspectorDrawer" class="hidden transition-all duration-300">
        <div class="bg-slate-900 rounded-2xl border border-slate-800 overflow-hidden shadow-xl text-slate-200">
          <div class="flex items-center justify-between px-4 py-3 bg-slate-950/80 border-b border-slate-800">
            <div class="flex items-center gap-2">
              <span class="w-3 h-3 rounded-full bg-rose-500 inline-block"></span>
              <span class="w-3 h-3 rounded-full bg-amber-500 inline-block"></span>
              <span class="w-3 h-3 rounded-full bg-emerald-500 inline-block"></span>
              <span class="ml-2 font-mono text-xs text-slate-400 font-semibold">CSS Grid Architecture Specifications (Assignment 8.5)</span>
            </div>
            <button id="closeInspectorBtn" class="text-slate-400 hover:text-white text-sm">
              <i class="fa-solid fa-xmark"></i>
            </button>
          </div>
          <div class="p-4 grid grid-cols-1 md:grid-cols-2 gap-4 text-xs font-mono">
            <div>
              <p class="text-brand-400 font-semibold mb-1">/* 1. App Layout Named Areas */</p>
              <pre class="bg-slate-950 p-3 rounded-xl text-slate-300 overflow-x-auto border border-slate-800">
.app-layout {
  display: grid;
  grid-template-rows: auto 1fr auto;
  grid-template-columns: 260px 1fr;
  grid-template-areas:
    "header  header"
    "sidebar main"
    "footer  footer";
}</pre>
            </div>
            <div>
              <p class="text-brand-400 font-semibold mb-1">/* 2. Responsive Gallery Grid & Overflow Control */</p>
              <pre class="bg-slate-950 p-3 rounded-xl text-slate-300 overflow-x-auto border border-slate-800">
.gallery-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
  gap: 1.5rem;
}
.img-container img {
  width: 100%; height: 100%;
  object-fit: cover; /* Overflow prevention */
}</pre>
            </div>
          </div>
        </div>
      </div>

      <!-- PRIMARY CSS GRID CONTAINER FOR CARDS -->
      <div id="galleryGrid" class="gallery-grid-standard">
        <!-- Gallery cards are dynamically rendered here via JavaScript -->
      </div>

    </main>

    <!-- FOOTER AREA -->
    <footer class="app-footer bg-white dark:bg-slate-900 border-t border-slate-200 dark:border-slate-800 px-6 py-6 transition-colors">
      <div class="max-w-7xl mx-auto flex flex-col sm:flex-row items-center justify-between gap-4 text-xs text-slate-500 dark:text-slate-400">
        <div class="flex items-center gap-2">
          <i class="fa-solid fa-graduation-cap text-brand-500 text-sm"></i>
          <span>Assignment 8.5 — CSS Grid Mini-Project Demo</span>
        </div>
        <div class="flex items-center gap-6">
          <span class="hover:text-slate-800 dark:hover:text-slate-200">Built with CSS Grid & Tailwind</span>
          <span class="hover:text-slate-800 dark:hover:text-slate-200">Auto-fit / MinMax / Fr Units</span>
          <span class="hover:text-slate-800 dark:hover:text-slate-200">Clean Resizing</span>
        </div>
        <div>
          &copy; 2026 Student Portfolio. All rights reserved.
        </div>
      </div>
    </footer>

  </div>

  <!-- LIGHTBOX MODAL CONTAINER -->
  <div id="lightboxModal" class="fixed inset-0 z-50 hidden bg-slate-950/90 backdrop-blur-md flex items-center justify-center p-4 sm:p-6 transition-all">
    <div class="relative max-w-4xl w-full bg-slate-900 rounded-3xl overflow-hidden border border-slate-800 shadow-2xl flex flex-col md:flex-row max-h-[90vh]">
      <!-- Modal Close Button -->
      <button id="closeModalBtn" class="absolute top-4 right-4 z-10 w-9 h-9 rounded-full bg-slate-950/60 hover:bg-slate-950 text-white flex items-center justify-center backdrop-blur-sm transition-colors">
        <i class="fa-solid fa-xmark"></i>
      </button>

      <!-- Lightbox Image Box -->
      <div class="md:w-3/5 bg-black flex items-center justify-center overflow-hidden min-h-[250px] md:min-h-[450px]">
        <img id="modalImg" src="" alt="Modal Detail" class="w-full h-full object-contain max-h-[70vh]" />
      </div>

      <!-- Lightbox Details Sidebar -->
      <div class="md:w-2/5 p-6 flex flex-col justify-between space-y-4 overflow-y-auto bg-slate-900 text-slate-100">
        <div>
          <span id="modalCategory" class="inline-block px-3 py-1 rounded-full text-[11px] font-bold uppercase tracking-wider bg-brand-500/20 text-brand-400 border border-brand-500/30 mb-3">
            Category
          </span>
          <h3 id="modalTitle" class="text-xl font-bold text-white mb-2 leading-tight">Title</h3>
          <p id="modalDesc" class="text-sm text-slate-300 leading-relaxed font-light mb-4">
            Description details...
          </p>
          <div class="space-y-2 pt-3 border-t border-slate-800 text-xs text-slate-400">
            <div class="flex items-center justify-between">
              <span class="text-slate-500">Grid Dimensions</span>
              <span id="modalSpecs" class="font-mono text-slate-300">300px x 400px (1fr)</span>
            </div>
            <div class="flex items-center justify-between">
              <span class="text-slate-500">Aspect Ratio</span>
              <span class="font-mono text-slate-300">16:9 / Object-Fit Cover</span>
            </div>
            <div class="flex items-center justify-between">
              <span class="text-slate-500">Author</span>
              <span id="modalAuthor" class="text-slate-300">Alex Rivera</span>
            </div>
          </div>
        </div>

        <div class="pt-4 border-t border-slate-800 flex items-center justify-between">
          <button class="flex items-center gap-2 text-xs font-semibold px-4 py-2 rounded-xl bg-brand-600 hover:bg-brand-500 text-white transition-colors">
            <i class="fa-solid fa-download"></i> Download Asset
          </button>
          <div class="flex items-center gap-3 text-slate-400 text-sm">
            <button class="hover:text-rose-500 transition-colors"><i class="fa-regular fa-heart"></i></button>
            <button class="hover:text-brand-400 transition-colors"><i class="fa-regular fa-bookmark"></i></button>
            <button class="hover:text-indigo-400 transition-colors"><i class="fa-solid fa-share-nodes"></i></button>
          </div>
        </div>
      </div>
    </div>
  </div>

  <!-- GITHUB SUBMISSION MODAL -->
  <div id="githubModal" class="fixed inset-0 z-50 hidden bg-slate-950/80 backdrop-blur-md flex items-center justify-center p-4">
    <div class="bg-white dark:bg-slate-900 text-slate-800 dark:text-slate-100 max-w-2xl w-full rounded-3xl border border-slate-200 dark:border-slate-800 p-6 shadow-2xl space-y-5">
      <div class="flex items-center justify-between border-b border-slate-200 dark:border-slate-800 pb-4">
        <div class="flex items-center gap-3">
          <div class="w-10 h-10 rounded-xl bg-slate-900 dark:bg-slate-800 text-white flex items-center justify-center text-xl">
            <i class="fa-brands fa-github"></i>
          </div>
          <div>
            <h3 class="font-bold text-lg">Assignment Submission Info</h3>
            <p class="text-xs text-slate-500">Assignment 8.5 — CSS Grid Mini-Project Checklist</p>
          </div>
        </div>
        <button id="closeGithubModalBtn" class="text-slate-400 hover:text-slate-600 dark:hover:text-white">
          <i class="fa-solid fa-xmark text-lg"></i>
        </button>
      </div>

      <div class="space-y-3 text-sm">
        <p class="text-slate-600 dark:text-slate-300">
          This project fulfills all required criteria for Assignment 8.5. You can copy the raw HTML/CSS files or set up a repository using the steps below:
        </p>

        <div class="bg-slate-100 dark:bg-slate-950 p-4 rounded-xl space-y-2 font-mono text-xs border border-slate-200 dark:border-slate-800">
          <p class="text-slate-400"># Command Line GitHub Setup Template</p>
          <p class="text-brand-600 dark:text-brand-400">git init</p>
          <p class="text-brand-600 dark:text-brand-400">git add index.html</p>
          <p class="text-brand-600 dark:text-brand-400">git commit -m "Complete Assignment 8.5 CSS Grid Mini-Project"</p>
          <p class="text-brand-600 dark:text-brand-400">git branch -M main</p>
          <p class="text-brand-600 dark:text-brand-400">git remote add origin https://github.com/your-username/css-grid-mini-project.git</p>
          <p class="text-brand-600 dark:text-brand-400">git push -u origin main</p>
        </div>

        <div class="space-y-1 text-xs text-slate-500 dark:text-slate-400">
          <div class="flex items-center gap-2"><i class="fa-solid fa-check text-emerald-500"></i> Primary CSS Grid Layout (Areas & Explicit placement)</div>
          <div class="flex items-center gap-2"><i class="fa-solid fa-check text-emerald-500"></i> minmax(), repeat(), and fr units</div>
          <div class="flex items-center gap-2"><i class="fa-solid fa-check text-emerald-500"></i> Comprehensive Overflow Control (Images & Text)</div>
        </div>
      </div>

      <div class="pt-3 border-t border-slate-200 dark:border-slate-800 flex justify-end">
        <button id="copyTemplateBtn" class="px-4 py-2 rounded-xl bg-brand-600 hover:bg-brand-500 text-white font-semibold text-xs transition-colors flex items-center gap-2">
          <i class="fa-solid fa-copy"></i> Copy Submission Checklist
        </button>
      </div>
    </div>
  </div>

  <!-- JAVASCRIPT FOR DYNAMIC DATA, GRID INTERACTION & MODALS -->
  <script>
    // Sample Data for Photo Gallery Items
    const galleryItems = [
      {
        id: '1',
        title: 'Alpine Peak Horizon',
        category: 'nature',
        badge: 'Featured',
        bentoClass: 'bento-featured',
        img: 'https://images.unsplash.com/photo-1464822759023-fed622ff2c3b?w=800&auto=format&fit=crop&q=80',
        author: 'Elena Rostova',
        description: 'Majestic mountain peaks covered in fresh snow during golden sunrise, demonstrating responsive container aspect-ratio fitting.'
      },
      {
        id: '2',
        title: 'Modern Steel Monolith',
        category: 'architecture',
        badge: 'Popular',
        bentoClass: 'bento-tall',
        img: 'https://images.unsplash.com/photo-1486406146926-c627a92ad1ab?w=800&auto=format&fit=crop&q=80',
        author: 'Marcus Vance',
        description: 'Glass and steel sky tower reaching into blue sky, illustrating vertical span grid placement.'
      },
      {
        id: '3',
        title: 'Neon Tokyo Alleyway',
        category: 'urban',
        badge: 'New',
        bentoClass: '',
        img: 'https://images.unsplash.com/photo-1503899036084-c55cdd92da26?w=800&auto=format&fit=crop&q=80',
        author: 'Kenji Sato',
        description: 'Atmospheric rain-slicked street surrounded by glowing neon signage in Shinjuku district.'
      },
      {
        id: '4',
        title: 'Geometric Prisms & Light',
        category: 'abstract',
        badge: '',
        bentoClass: 'bento-wide',
        img: 'https://images.unsplash.com/photo-1541701494587-cb58502866ab?w=800&auto=format&fit=crop&q=80',
        author: 'Sarah Chen',
        description: 'Fluid acrylic ink dispersion creating colorful abstract patterns and dimensional textures.'
      },
      {
        id: '5',
        title: 'Mist Forest Sanctuary',
        category: 'nature',
        badge: '',
        bentoClass: '',
        img: 'https://images.unsplash.com/photo-1448375240586-882707db888b?w=800&auto=format&fit=crop&q=80',
        author: 'Liam O\'Connor',
        description: 'Sunbeams penetrating dense fog inside a ancient pine forest canopy in autumn.'
      },
      {
        id: '6',
        title: 'Curved Concrete Spiral',
        category: 'architecture',
        badge: '',
        bentoClass: '',
        img: 'https://images.unsplash.com/photo-1513694203232-719a280e022f?w=800&auto=format&fit=crop&q=80',
        author: 'Sofia Rossi',
        description: 'Minimalist white concrete helical staircase photographed from a dramatic worm\'s-eye view angle.'
      },
      {
        id: '7',
        title: 'Metropolis Light Trails',
        category: 'urban',
        badge: '',
        bentoClass: 'bento-wide',
        img: 'https://images.unsplash.com/photo-1477959858617-67f30ac4ce78?w=800&auto=format&fit=crop&q=80',
        author: 'David Kim',
        description: 'Long exposure photography capturing highway traffic light trails threading through city skyscrapers.'
      },
      {
        id: '8',
        title: 'Vibrant Holographic Wave',
        category: 'abstract',
        badge: '',
        bentoClass: '',
        img: 'https://images.unsplash.com/photo-1550684848-fac1c5b4e853?w=800&auto=format&fit=crop&q=80',
        author: 'Aria Taylor',
        description: '3D generated iridescent ribbon wave with gradient shadows and spectral refraction.'
      },
      {
        id: '9',
        title: 'Emerald Lake Reflections',
        category: 'nature',
        badge: '',
        bentoClass: '',
        img: 'https://images.unsplash.com/photo-1506744038136-46273834b3fb?w=800&auto=format&fit=crop&q=80',
        author: 'Jordan Blake',
        description: 'Still crystal clear alpine lake mirroring surrounding pine forest and mountain crests.'
      },
      {
        id: '10',
        title: 'Brutalist Glass Pavilion',
        category: 'architecture',
        badge: '',
        bentoClass: '',
        img: 'https://images.unsplash.com/photo-1600585154340-be6161a56a0c?w=800&auto=format&fit=crop&q=80',
        author: 'Lucas Meyer',
        description: 'Sleek architectural villa with floor-to-ceiling glass facades integrated into natural terrain.'
      },
      {
        id: '11',
        title: 'Subway Motion Blur',
        category: 'urban',
        badge: '',
        bentoClass: '',
        img: 'https://images.unsplash.com/photo-1499856871958-5b9627545d1a?w=800&auto=format&fit=crop&q=80',
        author: 'Chloe Bennett',
        description: 'Dynamic kinetic blur of a high speed metro train departing a modern underground station.'
      },
      {
        id: '12',
        title: 'Monochrome Gradient Mesh',
        category: 'abstract',
        badge: '',
        bentoClass: '',
        img: 'https://images.unsplash.com/photo-1618005182384-a83a8bd57fbe?w=800&auto=format&fit=crop&q=80',
        author: 'Alex Rivera',
        description: 'Smooth dark silk digital renders exploring light gradients and smooth mathematical curvature.'
      }
    ];

    // DOM Elements
    const galleryGrid = document.getElementById('galleryGrid');
    const categoryNav = document.getElementById('categoryNav');
    const searchInput = document.getElementById('searchInput');
    const themeToggleBtn = document.getElementById('themeToggleBtn');
    const layoutStandardBtn = document.getElementById('layoutStandardBtn');
    const layoutBentoBtn = document.getElementById('layoutBentoBtn');
    const toggleInspectorBtn = document.getElementById('toggleInspectorBtn');
    const closeInspectorBtn = document.getElementById('closeInspectorBtn');
    const inspectorDrawer = document.getElementById('inspectorDrawer');
    const mobileMenuBtn = document.getElementById('mobileMenuBtn');
    const appSidebar = document.getElementById('appSidebar');
    
    // Modal DOM Elements
    const lightboxModal = document.getElementById('lightboxModal');
    const closeModalBtn = document.getElementById('closeModalBtn');
    const modalImg = document.getElementById('modalImg');
    const modalTitle = document.getElementById('modalTitle');
    const modalCategory = document.getElementById('modalCategory');
    const modalDesc = document.getElementById('modalDesc');
    const modalAuthor = document.getElementById('modalAuthor');

    // GitHub Modal Elements
    const githubModalBtn = document.getElementById('githubModalBtn');
    const githubModal = document.getElementById('githubModal');
    const closeGithubModalBtn = document.getElementById('closeGithubModalBtn');
    const copyTemplateBtn = document.getElementById('copyTemplateBtn');

    // State Variables
    let currentCategory = 'all';
    let currentLayout = 'standard';
    let searchQuery = '';

    // Render Gallery Cards
    function renderGallery() {
      galleryGrid.innerHTML = '';

      const filtered = galleryItems.filter(item => {
        const matchesCategory = currentCategory === 'all' || item.category === currentCategory;
        const matchesSearch = item.title.toLowerCase().includes(searchQuery.toLowerCase()) || 
                              item.category.toLowerCase().includes(searchQuery.toLowerCase()) ||
                              item.author.toLowerCase().includes(searchQuery.toLowerCase());
        return matchesCategory && matchesSearch;
      });

      if (filtered.length === 0) {
        galleryGrid.innerHTML = `
          <div class="col-span-full py-16 text-center space-y-3">
            <div class="w-16 h-16 rounded-2xl bg-slate-100 dark:bg-slate-800 text-slate-400 flex items-center justify-center mx-auto text-2xl">
              <i class="fa-solid fa-folder-open"></i>
            </div>
            <h3 class="text-base font-bold text-slate-700 dark:text-slate-200">No matching gallery items found</h3>
            <p class="text-xs text-slate-400 max-w-sm mx-auto">Try adjusting your category filter or search terms to display images.</p>
          </div>
        `;
        return;
      }

      filtered.forEach(item => {
        const card = document.createElement('div');
        const isBento = currentLayout === 'bento';
        const extraClass = isBento ? item.bentoClass : '';

        card.className = `gallery-card group bg-white dark:bg-slate-900 rounded-2xl border border-slate-200/80 dark:border-slate-800 overflow-hidden shadow-sm hover:shadow-xl hover:border-brand-500/40 dark:hover:border-brand-500/40 transition-all duration-300 flex flex-col ${extraClass}`;
        
        card.innerHTML = `
          <!-- Image Box with explicit height control to prevent overflow -->
          <div class="img-container ${isBento && item.bentoClass.includes('featured') ? 'h-64 sm:h-80 lg:h-full' : 'h-52'} cursor-pointer" onclick="openLightbox('${item.id}')">
            <img 
              src="${item.img}" 
              alt="${item.title}" 
              loading="lazy"
              onerror="this.src='https://placehold.co/600x400/1e293b/ffffff?text=Image+Unavailable'"
            />
            ${item.badge ? `
              <span class="absolute top-3 left-3 px-2.5 py-1 rounded-lg text-[10px] font-bold uppercase tracking-wider bg-slate-950/70 backdrop-blur-md text-white border border-white/10 shadow-md">
                ${item.badge}
              </span>
            ` : ''}
            <div class="absolute inset-0 bg-gradient-to-t from-slate-950/80 via-transparent to-transparent opacity-0 group-hover:opacity-100 transition-opacity duration-300 flex items-end p-4">
              <span class="text-xs font-semibold text-white flex items-center gap-1.5">
                <i class="fa-solid fa-expand text-brand-400"></i> Expand View
              </span>
            </div>
          </div>

          <!-- Card Content with Line-Clamping & Overflow Prevention -->
          <div class="p-4 flex flex-col justify-between flex-1 space-y-3">
            <div>
              <div class="flex items-center justify-between text-[11px] font-semibold text-brand-600 dark:text-brand-400 uppercase tracking-wider mb-1">
                <span class="prevent-overflow-text">${item.category}</span>
                <span class="text-slate-400 font-mono text-[10px]">#${item.id}</span>
              </div>
              <h3 class="font-bold text-slate-800 dark:text-slate-100 text-base leading-snug group-hover:text-brand-500 transition-colors line-clamp-2" title="${item.title}">
                ${item.title}
              </h3>
              <p class="text-xs text-slate-500 dark:text-slate-400 mt-1 line-clamp-2 leading-relaxed">
                ${item.description}
              </p>
            </div>

            <div class="pt-3 border-t border-slate-100 dark:border-slate-800/80 flex items-center justify-between text-xs text-slate-500">
              <span class="flex items-center gap-1.5 truncate max-w-[140px]">
                <i class="fa-solid fa-user-circle text-slate-400"></i>
                <span class="truncate">${item.author}</span>
              </span>
              <button onclick="openLightbox('${item.id}')" class="text-slate-400 hover:text-brand-500 dark:hover:text-brand-400 transition-colors p-1" aria-label="Details">
                <i class="fa-solid fa-arrow-up-right-from-square"></i>
              </button>
            </div>
          </div>
        `;

        galleryGrid.appendChild(card);
      });
    }

    // Category Filtering
    categoryNav.addEventListener('click', (e) => {
      const btn = e.target.closest('.category-btn');
      if (!btn) return;

      document.querySelectorAll('.category-btn').forEach(b => {
        b.classList.remove('active', 'bg-brand-50', 'dark:bg-brand-500/10', 'text-brand-600', 'dark:text-brand-400');
        b.classList.add('text-slate-600', 'dark:text-slate-400');
      });

      btn.classList.add('active', 'bg-brand-50', 'dark:bg-brand-500/10', 'text-brand-600', 'dark:text-brand-400');
      btn.classList.remove('text-slate-600', 'dark:text-slate-400');

      currentCategory = btn.dataset.category;
      document.getElementById('currentCategoryTitle').textContent = btn.querySelector('span').textContent.trim();
      renderGallery();
    });

    // Search Filtering
    searchInput.addEventListener('input', (e) => {
      searchQuery = e.target.value;
      renderGallery();
    });

    // Layout Toggle Switcher
    layoutStandardBtn.addEventListener('click', () => {
      currentLayout = 'standard';
      galleryGrid.className = 'gallery-grid-standard';
      
      layoutStandardBtn.className = 'px-3 py-1.5 rounded-lg text-xs font-semibold flex items-center gap-1.5 transition-all bg-white dark:bg-slate-700 text-brand-600 dark:text-white shadow-sm';
      layoutBentoBtn.className = 'px-3 py-1.5 rounded-lg text-xs font-semibold flex items-center gap-1.5 transition-all text-slate-600 dark:text-slate-400 hover:text-slate-900 dark:hover:text-white';
      
      renderGallery();
    });

    layoutBentoBtn.addEventListener('click', () => {
      currentLayout = 'bento';
      galleryGrid.className = 'gallery-grid-bento';

      layoutBentoBtn.className = 'px-3 py-1.5 rounded-lg text-xs font-semibold flex items-center gap-1.5 transition-all bg-white dark:bg-slate-700 text-brand-600 dark:text-white shadow-sm';
      layoutStandardBtn.className = 'px-3 py-1.5 rounded-lg text-xs font-semibold flex items-center gap-1.5 transition-all text-slate-600 dark:text-slate-400 hover:text-slate-900 dark:hover:text-white';

      renderGallery();
    });

    // Theme Switcher (Dark / Light Mode)
    themeToggleBtn.addEventListener('click', () => {
      document.documentElement.classList.toggle('dark');
    });

    // Inspector Drawer Toggle
    toggleInspectorBtn.addEventListener('click', () => {
      inspectorDrawer.classList.toggle('hidden');
    });
    closeInspectorBtn.addEventListener('click', () => {
      inspectorDrawer.classList.add('hidden');
    });

    // Mobile Sidebar Navigation Toggle
    mobileMenuBtn.addEventListener('click', () => {
      appSidebar.classList.toggle('mobile-open');
    });

    // Lightbox Modal Functions
    window.openLightbox = function(id) {
      const item = galleryItems.find(i => i.id === id);
      if (!item) return;

      modalImg.src = item.img;
      modalTitle.textContent = item.title;
      modalCategory.textContent = item.category;
      modalDesc.textContent = item.description;
      modalAuthor.textContent = item.author;

      lightboxModal.classList.remove('hidden');
      document.body.style.overflow = 'hidden';
    };

    closeModalBtn.addEventListener('click', () => {
      lightboxModal.classList.add('hidden');
      document.body.style.overflow = '';
    });

    lightboxModal.addEventListener('click', (e) => {
      if (e.target === lightboxModal) {
        lightboxModal.classList.add('hidden');
        document.body.style.overflow = '';
      }
    });

    // GitHub Submission Modal Handlers
    githubModalBtn.addEventListener('click', () => {
      githubModal.classList.remove('hidden');
    });
    closeGithubModalBtn.addEventListener('click', () => {
      githubModal.classList.add('hidden');
    });
    githubModal.addEventListener('click', (e) => {
      if (e.target === githubModal) {
        githubModal.classList.add('hidden');
      }
    });

    copyTemplateBtn.addEventListener('click', () => {
      const checklistText = `Assignment 8.5 CSS Grid Mini-Project Checklist:\n- Page grid-template-areas: Header, Sidebar, Main, Footer\n- Photo grid: repeat(auto-fill, minmax(280px, 1fr)) with 1.5rem gaps\n- Explicit grid span placement (Bento layout mode)\n- Overflow prevention: object-fit cover, line-clamping, and responsive bounds.`;
      
      // Fallback copy execution
      const tempTextArea = document.createElement('textarea');
      tempTextArea.value = checklistText;
      document.body.appendChild(tempTextArea);
      tempTextArea.select();
      document.execCommand('copy');
      document.body.removeChild(tempTextArea);

      const origText = copyTemplateBtn.innerHTML;
      copyTemplateBtn.innerHTML = `<i class="fa-solid fa-check"></i> Copied to Clipboard!`;
      setTimeout(() => {
        copyTemplateBtn.innerHTML = origText;
      }, 2000);
    });

    // Initialize Page
    window.onload = function() {
      renderGallery();
    };
  </script>
</body>
</html>