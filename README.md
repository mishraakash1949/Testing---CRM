<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Wedding Vendor Sales CRM</title>
  <script src="https://cdn.tailwindcss.com/3.4.17"></script>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com">
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800&display=swap" rel="stylesheet">
  <style>
    body { font-family: 'Inter', sans-serif; background-color: #0f172a; }
    .custom-scrollbar::-webkit-scrollbar { height: 6px; width: 6px; }
    .custom-scrollbar::-webkit-scrollbar-track { background: #f1f5f9; border-radius: 4px; }
    .custom-scrollbar::-webkit-scrollbar-thumb { background: #cbd5e1; border-radius: 4px; }
    @keyframes popIn { 0% { transform: scale(0.96); opacity: 0; } 100% { transform: scale(1); opacity: 1; } }
    .animate-pop { animation: popIn 0.15s ease-out forwards; }
    .kanban-card { cursor: grab; transition: transform 0.15s ease, box-shadow 0.15s ease; }
    .kanban-card:active { cursor: grabbing; }
    .kanban-card.dragging { opacity: 0.35; transform: scale(0.97); }
    .drag-over { background-color: #e2e8f0 !important; border: 2px dashed #3b82f6 !important; }
    .sidebar-link.active { background-color: #3b82f6; color: #ffffff; font-weight: 600; }
    .sidebar-link { transition: all 0.15s ease; }
    .board-fullscreen {
      position: fixed !important;
      inset: 0 !important;
      z-index: 50 !important;
      width: 100vw !important;
      height: 100vh !important;
      max-width: 100vw !important;
      max-height: 100vh !important;
      border-radius: 0 !important;
      margin: 0 !important;
      border: none !important;
      background-color: #f8fafc !important;
      display: flex !important;
      flex-direction: column !important;
      overflow: hidden !important;
    }
    .board-fullscreen #tableViewContainer {
      flex: 1 1 0% !important;
      overflow-y: auto !important;
      max-height: calc(100vh - 72px) !important;
    }
    .board-fullscreen #kanbanViewContainer {
      flex: 1 1 0% !important;
      height: calc(100vh - 72px) !important;
      display: flex !important;
      flex-direction: column !important;
      overflow-x: auto !important;
      overflow-y: hidden !important;
    }
    .board-fullscreen .kanban-col {
      height: calc(100vh - 145px) !important;
      min-height: 0 !important;
    }
  </style>
</head>
<body class="bg-slate-100 text-slate-800 min-h-screen flex antialiased">

  <!-- ========================================== -->
  <!-- SIGN-IN SCREEN                              -->
  <!-- ========================================== -->
  <div id="authScreen" class="fixed inset-0 z-[100] bg-slate-900 flex items-center justify-center p-4 overflow-y-auto">
    <div class="w-full max-w-sm">
      <div class="text-center mb-6">
        <div class="w-12 h-12 rounded-xl bg-blue-600 text-white font-black text-lg flex items-center justify-center mx-auto shadow-lg">W</div>
        <h1 class="mt-3 text-lg font-bold text-white tracking-wide uppercase">Wedding CRM</h1>
        <p id="authSubtitle" class="text-xs text-slate-400 mt-1">Sign in to continue</p>
      </div>

      <div class="bg-white rounded-2xl shadow-2xl p-6 animate-pop">
        <!-- Sign in form -->
        <div id="signInPane" class="space-y-4">
          <div>
            <label class="block text-[11px] font-bold uppercase tracking-wider text-slate-600 mb-1">Username</label>
            <input id="loginUsername" type="text" autocomplete="username" autocapitalize="none" spellcheck="false" class="w-full px-3 py-2 text-sm border border-slate-300 rounded-lg bg-slate-50 focus:outline-none focus:ring-2 focus:ring-blue-500">
          </div>
          <div>
            <label class="block text-[11px] font-bold uppercase tracking-wider text-slate-600 mb-1">Password</label>
            <div class="relative">
              <input id="loginPassword" type="password" autocomplete="current-password" class="w-full px-3 py-2 pr-14 text-sm border border-slate-300 rounded-lg bg-slate-50 focus:outline-none focus:ring-2 focus:ring-blue-500">
              <button type="button" id="toggleLoginPwd" class="absolute right-2 top-1/2 -translate-y-1/2 text-[11px] font-semibold text-slate-500 hover:text-slate-800">Show</button>
            </div>
          </div>
          <label class="flex items-center gap-2 text-xs text-slate-600 cursor-pointer select-none">
            <input id="loginRemember" type="checkbox" class="rounded text-blue-600"> Keep me signed in on this device
          </label>
          <p id="loginError" class="hidden text-xs font-semibold text-red-600 bg-red-50 border border-red-200 rounded-lg px-3 py-2"></p>
          <button id="loginBtn" type="button" class="w-full py-2.5 text-sm font-bold rounded-xl bg-slate-900 hover:bg-slate-800 text-white shadow-sm transition">Sign In</button>
          <button id="forgotBtn" type="button" class="w-full text-center text-xs font-semibold text-blue-600 hover:underline">Forgot admin password?</button>
        </div>

        <!-- First-run: create admin -->
        <div id="setupPane" class="hidden space-y-3">
          <p class="text-xs text-slate-600">Welcome! Create the <strong>Admin</strong> account that will own this workspace. You can add your sales team after signing in.</p>
          <div>
            <label class="block text-[11px] font-bold uppercase tracking-wider text-slate-600 mb-1">Your Name</label>
            <input id="setupName" type="text" autocomplete="name" class="w-full px-3 py-2 text-sm border border-slate-300 rounded-lg bg-slate-50 focus:outline-none focus:ring-2 focus:ring-blue-500">
          </div>
          <div>
            <label class="block text-[11px] font-bold uppercase tracking-wider text-slate-600 mb-1">Username</label>
            <input id="setupUsername" type="text" autocomplete="username" autocapitalize="none" spellcheck="false" class="w-full px-3 py-2 text-sm border border-slate-300 rounded-lg bg-slate-50 focus:outline-none focus:ring-2 focus:ring-blue-500">
          </div>
          <div>
            <label class="block text-[11px] font-bold uppercase tracking-wider text-slate-600 mb-1">Password <span class="normal-case font-medium text-slate-400">(min 6 characters)</span></label>
            <input id="setupPassword" type="password" autocomplete="new-password" class="w-full px-3 py-2 text-sm border border-slate-300 rounded-lg bg-slate-50 focus:outline-none focus:ring-2 focus:ring-blue-500">
          </div>
          <div>
            <label class="block text-[11px] font-bold uppercase tracking-wider text-slate-600 mb-1">Confirm Password</label>
            <input id="setupPassword2" type="password" autocomplete="new-password" class="w-full px-3 py-2 text-sm border border-slate-300 rounded-lg bg-slate-50 focus:outline-none focus:ring-2 focus:ring-blue-500">
          </div>
          <p id="setupError" class="hidden text-xs font-semibold text-red-600 bg-red-50 border border-red-200 rounded-lg px-3 py-2"></p>
          <button id="setupBtn" type="button" class="w-full py-2.5 text-sm font-bold rounded-xl bg-blue-600 hover:bg-blue-700 text-white shadow-sm transition">Create Admin & Continue</button>
        </div>

        <!-- Forgot password (uses Master PIN) -->
        <div id="resetPane" class="hidden space-y-3">
          <p class="text-xs text-slate-600">Enter the <strong>Master PIN</strong> to reset the password of an Admin account.</p>
          <div>
            <label class="block text-[11px] font-bold uppercase tracking-wider text-slate-600 mb-1">Admin Username</label>
            <input id="resetUsername" type="text" autocapitalize="none" spellcheck="false" class="w-full px-3 py-2 text-sm border border-slate-300 rounded-lg bg-slate-50 focus:outline-none focus:ring-2 focus:ring-blue-500">
          </div>
          <div>
            <label class="block text-[11px] font-bold uppercase tracking-wider text-slate-600 mb-1">Master PIN</label>
            <input id="resetPin" type="password" maxlength="6" class="w-full px-3 py-2 text-sm border border-slate-300 rounded-lg bg-slate-50 focus:outline-none focus:ring-2 focus:ring-blue-500">
          </div>
          <div>
            <label class="block text-[11px] font-bold uppercase tracking-wider text-slate-600 mb-1">New Password</label>
            <input id="resetNewPwd" type="password" autocomplete="new-password" class="w-full px-3 py-2 text-sm border border-slate-300 rounded-lg bg-slate-50 focus:outline-none focus:ring-2 focus:ring-blue-500">
          </div>
          <p id="resetError" class="hidden text-xs font-semibold text-red-600 bg-red-50 border border-red-200 rounded-lg px-3 py-2"></p>
          <div class="grid grid-cols-2 gap-2">
            <button id="resetCancelBtn" type="button" class="py-2 text-xs font-semibold rounded-xl bg-slate-100 hover:bg-slate-200 text-slate-700 transition">Back</button>
            <button id="resetConfirmBtn" type="button" class="py-2 text-xs font-bold rounded-xl bg-slate-900 hover:bg-slate-800 text-white transition">Reset Password</button>
          </div>
        </div>
      </div>
      <p class="text-center text-[11px] text-slate-500 mt-4">Sales reps: ask your Admin for your username &amp; password.</p>
    </div>
  </div>

  <!-- ========================================== -->
  <!-- TEAM MEMBER (ADD / EDIT) MODAL              -->
  <!-- ========================================== -->
  <div id="userModal" class="hidden fixed inset-0 bg-slate-900/60 z-50 flex items-center justify-center p-4">
    <div class="bg-white rounded-2xl max-w-md w-full p-6 shadow-2xl border border-slate-200 animate-pop max-h-[90vh] overflow-y-auto">
      <h3 id="userModalTitle" class="text-base font-bold text-slate-900 mb-4">Add Team Member</h3>
      <div class="space-y-3 text-xs">
        <div class="grid grid-cols-2 gap-3">
          <div>
            <label class="block font-bold uppercase tracking-wider text-slate-600 mb-1">Full Name *</label>
            <input id="umName" type="text" class="w-full px-3 py-2 border border-slate-300 rounded-lg bg-slate-50 focus:outline-none focus:ring-2 focus:ring-blue-500">
          </div>
          <div>
            <label class="block font-bold uppercase tracking-wider text-slate-600 mb-1">Username *</label>
            <input id="umUsername" type="text" autocapitalize="none" spellcheck="false" class="w-full px-3 py-2 border border-slate-300 rounded-lg bg-slate-50 focus:outline-none focus:ring-2 focus:ring-blue-500">
          </div>
        </div>
        <div class="grid grid-cols-2 gap-3">
          <div>
            <label class="block font-bold uppercase tracking-wider text-slate-600 mb-1"><span id="umPwdLabel">Password *</span></label>
            <input id="umPassword" type="text" autocomplete="off" class="w-full px-3 py-2 border border-slate-300 rounded-lg bg-slate-50 focus:outline-none focus:ring-2 focus:ring-blue-500 font-mono">
          </div>
          <div>
            <label class="block font-bold uppercase tracking-wider text-slate-600 mb-1">Role</label>
            <select id="umRole" class="w-full px-3 py-2 border border-slate-300 rounded-lg bg-slate-50 focus:outline-none focus:ring-2 focus:ring-blue-500">
              <option value="sales">Sales Closer</option>
              <option value="admin">Admin</option>
            </select>
          </div>
        </div>
        <div id="umVendorBlock" class="p-3 bg-slate-50 border border-slate-200 rounded-lg space-y-2">
          <label class="flex items-center gap-2 font-semibold text-slate-800 cursor-pointer">
            <input id="umAllVendors" type="checkbox" class="rounded text-blue-600" checked> Access to all vendors (including new ones)
          </label>
          <div id="umVendorList" class="hidden grid grid-cols-1 sm:grid-cols-2 gap-1.5"></div>
        </div>
        <p id="umError" class="hidden text-xs font-semibold text-red-600 bg-red-50 border border-red-200 rounded-lg px-3 py-2"></p>
      </div>
      <div class="grid grid-cols-2 gap-2 mt-5">
        <button id="umCancelBtn" type="button" class="py-2 text-xs font-semibold rounded-xl bg-slate-100 hover:bg-slate-200 text-slate-700 transition">Cancel</button>
        <button id="umSaveBtn" type="button" class="py-2 text-xs font-bold rounded-xl bg-blue-600 hover:bg-blue-700 text-white transition">Save</button>
      </div>
    </div>
  </div>

  <!-- ========================================== -->
  <!-- CHANGE MY PASSWORD MODAL                    -->
  <!-- ========================================== -->
  <div id="changePwdModal" class="hidden fixed inset-0 bg-slate-900/60 z-50 flex items-center justify-center p-4">
    <div class="bg-white rounded-2xl max-w-xs w-full p-6 shadow-2xl border border-slate-200 animate-pop">
      <h3 class="text-base font-bold text-slate-900 mb-3">Change My Password</h3>
      <div class="space-y-3 text-xs">
        <input id="cpCurrent" type="password" autocomplete="current-password" placeholder="Current password" class="w-full px-3 py-2 border border-slate-300 rounded-lg bg-slate-50 focus:outline-none focus:ring-2 focus:ring-blue-500">
        <input id="cpNew" type="password" autocomplete="new-password" placeholder="New password (min 6)" class="w-full px-3 py-2 border border-slate-300 rounded-lg bg-slate-50 focus:outline-none focus:ring-2 focus:ring-blue-500">
        <p id="cpError" class="hidden text-xs font-semibold text-red-600 bg-red-50 border border-red-200 rounded-lg px-3 py-2"></p>
      </div>
      <div class="grid grid-cols-2 gap-2 mt-4">
        <button id="cpCancelBtn" type="button" class="py-2 text-xs font-semibold rounded-xl bg-slate-100 hover:bg-slate-200 text-slate-700 transition">Cancel</button>
        <button id="cpSaveBtn" type="button" class="py-2 text-xs font-bold rounded-xl bg-slate-900 hover:bg-slate-800 text-white transition">Update</button>
      </div>
    </div>
  </div>

  <!-- ========================================== -->
  <!-- PIPEDRIVE-STYLE LEFT SIDEBAR NAVIGATION    -->
  <!-- ========================================== -->
  <aside class="w-64 bg-slate-900 text-slate-300 flex flex-col shrink-0 min-h-screen border-r border-slate-800 select-none">
    
    <!-- Workspace Brand Header -->
    <div class="h-16 flex items-center justify-between px-5 border-b border-slate-800">
      <div class="flex items-center gap-2.5">
        <div class="w-8 h-8 rounded-lg bg-blue-600 text-white font-black text-sm flex items-center justify-center shadow-sm tracking-wider">
          W
        </div>
        <div>
          <h2 class="text-xs font-bold text-white tracking-wide uppercase leading-tight">Wedding CRM</h2>
          <span id="sidebarRoleTag" class="text-[10px] text-emerald-400 font-semibold">Admin Workspace</span>
        </div>
      </div>
      <button id="quickLockToggleBtn" class="text-xs text-slate-400 hover:text-white transition p-1" title="Sign out">
        ⎋
      </button>
    </div>

    <!-- Navigation Menu Items -->
    <nav class="flex-1 px-3 py-4 space-y-1.5 text-xs font-medium overflow-y-auto">
      
      <div class="px-2 pb-1 text-[10px] uppercase font-bold text-slate-500 tracking-wider">Sales Operations</div>

      <!-- Deals / Pipeline -->
      <button id="sideNavPipelineBtn" type="button" class="sidebar-link active w-full flex items-center gap-3 px-3 py-2.5 rounded-xl hover:bg-slate-800 hover:text-white text-left">
        <svg class="w-4 h-4 shrink-0" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 11H5m14 0a2 2 0 012 2v6a2 2 0 01-2 2H5a2 2 0 01-2-2v-6a2 2 0 012-2m14 0V9a2 2 0 00-2-2M5 11V9a2 2 0 012-2m0 0V5a2 2 0 012-2h6a2 2 0 012 2v2M7 7h10"></path></svg>
        <span class="flex-1 font-semibold">Deals Pipeline</span>
        <span id="sidePipelineCount" class="text-[10px] px-1.5 py-0.5 rounded-full bg-slate-800 text-slate-300 font-bold">0</span>
      </button>

      <!-- Follow-Up Sprint -->
      <button id="sideNavSprintBtn" type="button" class="sidebar-link w-full flex items-center gap-3 px-3 py-2.5 rounded-xl hover:bg-slate-800 hover:text-white text-left text-amber-300">
        <svg class="w-4 h-4 shrink-0 text-amber-400" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 10V3L4 14h7v7l9-11h-7z"></path></svg>
        <span class="flex-1 font-semibold text-white">Daily Sprint</span>
        <span id="sideSprintDueCount" class="text-[10px] px-1.5 py-0.5 rounded-full bg-amber-500/20 text-amber-300 font-bold border border-amber-500/30">0 Due</span>
      </button>

      <!-- Lost Repository -->
      <button id="sideNavLostBtn" type="button" class="sidebar-link w-full flex items-center gap-3 px-3 py-2.5 rounded-xl hover:bg-slate-800 hover:text-white text-left">
        <svg class="w-4 h-4 shrink-0 text-slate-400" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 8h14M5 8a2 2 0 110-4h14a2 2 0 110 4M5 8v10a2 2 0 002 2h10a2 2 0 002-2V8m-9 4h4"></path></svg>
        <span class="flex-1">Lost Archive</span>
        <span id="sideLostCount" class="text-[10px] px-1.5 py-0.5 rounded-full bg-slate-800 text-slate-400">0</span>
      </button>

      <!-- ADMIN ONLY SECTION -->
      <div id="adminNavSection" class="pt-4 space-y-1.5">
        <div class="px-2 pb-1 text-[10px] uppercase font-bold text-slate-500 tracking-wider flex items-center justify-between">
          <span>Admin Controls</span>
          <span class="text-[9px] px-1 rounded bg-slate-800 text-slate-400">Protected</span>
        </div>

        <!-- Finances & Commission (Admin Only) -->
        <button id="sideNavFinanceBtn" type="button" class="sidebar-link w-full flex items-center gap-3 px-3 py-2.5 rounded-xl hover:bg-slate-800 hover:text-white text-left">
          <svg class="w-4 h-4 shrink-0 text-emerald-400" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8c-1.657 0-3 .895-3 2s1.343 2 3 2 3 .895 3 2-1.343 2-3 2m0-8c1.11 0 2.08.402 2.599 1M12 8V7m0 1v8m0 0v1m0-1c-1.11 0-2.08-.402-2.599-1M21 12a9 9 0 11-18 0 9 9 0 0118 0z"></path></svg>
          <span class="flex-1 font-semibold">Finances & 10% Comm</span>
        </button>

        <!-- Vendor Manager (Admin Only) -->
        <button id="sideNavVendorsBtn" type="button" class="sidebar-link w-full flex items-center gap-3 px-3 py-2.5 rounded-xl hover:bg-slate-800 hover:text-white text-left">
          <svg class="w-4 h-4 shrink-0 text-indigo-400" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17 20h5v-2a3 3 0 00-5.356-1.857M17 20H7m10 0v-2c0-.656-.126-1.283-.356-1.857M7 20H2v-2a3 3 0 015.356-1.857M7 20v-2c0-.656.126-1.283.356-1.857m0 0a5.002 5.002 0 019.288 0M15 7a3 3 0 11-6 0 3 3 0 016 0zm6 3a2 2 0 11-4 0 2 2 0 014 0zM7 10a2 2 0 11-4 0 2 2 0 014 0z"></path></svg>
          <span class="flex-1">Manage Vendors (<span id="sideVendorCount">0</span>)</span>
        </button>

        <!-- Access & Permissions Settings (Admin Only) -->
        <button id="sideNavPermissionsBtn" type="button" class="sidebar-link w-full flex items-center gap-3 px-3 py-2.5 rounded-xl hover:bg-slate-800 hover:text-white text-left">
          <svg class="w-4 h-4 shrink-0 text-blue-400" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10.325 4.317c.426-1.756 2.924-1.756 3.35 0a1.724 1.724 0 002.573 1.066c1.543-.94 3.31.826 2.37 2.37a1.724 1.724 0 001.065 2.572c1.756.426 1.756 2.924 0 3.35a1.724 1.724 0 00-1.066 2.573c.94 1.543-.826 3.31-2.37 2.37a1.724 1.724 0 00-2.572 1.065c-.426 1.756-2.924 1.756-3.35 0a1.724 1.724 0 00-2.573-1.066c-1.543.94-3.31-.826-2.37-2.37a1.724 1.724 0 00-1.065-2.572c-1.756-.426-1.756-2.924 0-3.35a1.724 1.724 0 001.066-2.573c-.94-1.543.826-3.31 2.37-2.37.996.608 2.296.07 2.572-1.065z"></path><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z"></path></svg>
          <span class="flex-1">Access & Team Settings</span>
        </button>

        <!-- CSV Export & Backup (Admin Only) -->
        <button id="sideExportCsvBtn" type="button" class="sidebar-link w-full flex items-center gap-3 px-3 py-2.5 rounded-xl hover:bg-slate-800 hover:text-white text-left">
          <svg class="w-4 h-4 shrink-0 text-slate-400" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-4l-4 4m0 0l-4-4m4 4V4"></path></svg>
          <span class="flex-1">Export Database CSV</span>
        </button>
      </div>

    </nav>

    <!-- Sidebar Bottom: Signed-in user -->
    <div class="p-3 border-t border-slate-800 bg-slate-950/50">
      <div class="p-3 rounded-xl bg-slate-900 border border-slate-800 space-y-2.5">
        <div class="flex items-center gap-2.5">
          <div id="userAvatar" class="w-8 h-8 rounded-full bg-blue-600 text-white text-xs font-bold flex items-center justify-center shrink-0">?</div>
          <div class="min-w-0 flex-1">
            <div id="userNameLabel" class="text-xs font-bold text-white truncate">—</div>
            <div class="text-[10px] text-slate-400 truncate">@<span id="userHandleLabel">—</span></div>
          </div>
          <span id="rolePillStatus" class="px-2 py-0.5 rounded text-[10px] font-bold bg-emerald-500/20 text-emerald-400 border border-emerald-500/30">Admin</span>
        </div>
        <div class="grid grid-cols-2 gap-1.5">
          <button id="changePwdBtn" type="button" class="py-1.5 px-2 rounded-lg text-center text-[11px] font-semibold bg-slate-800 hover:bg-slate-700 text-slate-200 transition">Password</button>
          <button id="signOutBtn" type="button" class="py-1.5 px-2 rounded-lg text-center text-[11px] font-bold bg-slate-700 hover:bg-red-600 text-white transition">Sign Out</button>
        </div>
      </div>
    </div>

  </aside>

  <!-- ========================================== -->
  <!-- MAIN APPLICATION CONTENT AREA              -->
  <!-- ========================================== -->
  <main class="flex-1 flex flex-col min-w-0 min-h-screen overflow-y-auto">
    
    <!-- Top Action Bar -->
    <header class="h-16 bg-white border-b border-slate-200 px-6 flex items-center justify-between shrink-0 shadow-2xs">
      <div class="flex items-center gap-3">
        <span class="text-xs font-semibold uppercase tracking-wider text-slate-500">Filter Workspace:</span>
        <div class="relative flex items-center bg-slate-50 border border-slate-300 rounded-lg px-3 py-1.5">
          <select id="vendorWorkspaceSelect" class="bg-transparent text-xs font-bold text-slate-900 focus:outline-none cursor-pointer pr-3"></select>
        </div>
      </div>

      <div class="flex items-center gap-2.5">
<!-- Single Add Lead button maintained in toolbar -->
        <button id="headerAddVendorBtn" type="button" class="inline-flex items-center gap-1.5 px-3 py-1.5 rounded-lg text-xs font-semibold border border-dashed border-slate-300 hover:border-slate-400 bg-white hover:bg-slate-50 text-slate-700 transition shadow-xs">
          + Add Vendor
        </button>
        <input type="file" id="csvFileInput" accept=".csv" class="hidden">
        <button id="headerImportCsvBtn" type="button" class="inline-flex items-center gap-1.5 px-3 py-1.5 rounded-lg text-xs font-semibold border border-slate-300 bg-white hover:bg-slate-50 text-slate-700 transition shadow-xs">
          Import CSV
        </button>
      </div>
    </header>

    <div class="p-6 sm:p-8 space-y-5 flex-1">
      
      <!-- ========================================== -->
      <!-- VIEW 1: PIPELINE / DEALS (FULL WIDTH!)     -->
      <!-- ========================================== -->
      <div id="pipelinePageSection" class="space-y-4">
        
        <!-- Micro Follow-Up Sprint Strip -->
        <div id="sprintAlertBanner" class="px-3 py-1.5 rounded-lg bg-gradient-to-r from-amber-500 to-orange-500 text-white shadow-2xs flex items-center justify-between gap-2 text-xs">
          <div class="flex items-center gap-2 min-w-0">
            <span class="font-extrabold text-[11px] flex items-center gap-1">⚡ Sprint:</span>
            <span id="sprintDueBadge" class="bg-white text-amber-900 text-[10px] px-1.5 py-0.2 rounded-full font-black">0 Due</span>
            <span class="hidden sm:inline text-[11px] text-amber-100/90 truncate">• 1-click WhatsApp pitch</span>
          </div>
          <button id="startSprintBtn" type="button" class="px-2.5 py-1 rounded-md text-[11px] font-bold bg-white text-slate-900 hover:bg-slate-100 shadow-2xs transition whitespace-nowrap">
            Start Sprint →
          </button>
        </div>

        <!-- Ultra-Compact Single-Row Metrics Bar -->
        <div class="grid grid-cols-2 sm:grid-cols-4 gap-2">
          <div class="bg-white px-3 py-2 rounded-lg border border-slate-200 flex items-center justify-between shadow-2xs">
            <span class="text-[11px] font-semibold text-slate-500 uppercase tracking-wide">Leads</span>
            <span id="statTotalContacts" class="text-sm font-bold text-slate-900 font-mono">0</span>
            <span id="statWorkspaceSub" class="hidden"></span>
          </div>
          <div class="bg-white px-3 py-2 rounded-lg border border-slate-200 flex items-center justify-between shadow-2xs">
            <span class="text-[11px] font-semibold text-slate-500 uppercase tracking-wide">Pipeline</span>
            <span id="statPipelineValue" class="text-sm font-bold text-indigo-600 font-mono">₹0</span>
          </div>
          <div class="bg-white px-3 py-2 rounded-lg border border-slate-200 flex items-center justify-between shadow-2xs">
            <span class="text-[11px] font-semibold text-slate-500 uppercase tracking-wide">Won</span>
            <span id="statWonCount" class="text-sm font-bold text-emerald-600 font-mono">0</span>
            <span id="statWonSub" class="hidden"></span>
          </div>
          <div class="bg-white px-3 py-2 rounded-lg border border-slate-200 flex items-center justify-between shadow-2xs">
            <span class="text-[11px] font-semibold text-slate-500 uppercase tracking-wide">Due Today</span>
            <span id="statFollowUpsDue" class="text-sm font-bold text-amber-600 font-mono">0</span>
          </div>
        </div>

        <!-- EXPANSIVE FULL-WIDTH TABLE & KANBAN (WITH FULLSCREEN TOGGLE) -->
        <div class="w-full">
          <div id="mainBoardCard" class="bg-white rounded-xl border border-slate-200 shadow-sm overflow-hidden transition-all duration-150">
            
            <!-- Toolbar with View Switcher & Action Buttons -->
            <div class="p-4 border-b border-slate-200 flex flex-col md:flex-row md:items-center md:justify-between gap-3">
              <div class="flex items-center gap-3">
                <h2 id="currentWorkspaceHeading" class="text-base font-bold text-slate-900">All Vendor Inquiries</h2>
                <!-- View & Fullscreen Pill Toggle -->
                <div class="inline-flex items-center rounded-lg border border-slate-200 bg-slate-100 p-0.5 text-xs">
                  <button id="toggleTableBtn" type="button" class="px-2.5 py-1 rounded-md font-semibold bg-white text-slate-900 shadow-xs">Table</button>
                  <button id="toggleKanbanBtn" type="button" class="px-2.5 py-1 rounded-md font-medium text-slate-600 hover:text-slate-900">Kanban</button>
                  <button id="toggleFullscreenBtn" type="button" class="px-2 py-1 rounded-md text-slate-600 hover:text-slate-900 hover:bg-white/80 transition" title="Toggle Fullscreen Board (Press F or Esc)">
                    <span id="fullscreenIcon">⛶</span>
                  </button>
                </div>
              </div>
              
              <div class="flex flex-wrap items-center gap-2">
                <!-- Secondary + Add Lead button directly in toolbar -->
                <button id="toolbarAddLeadBtn" type="button" class="inline-flex items-center gap-1 px-3 py-1.5 rounded-lg text-xs font-bold bg-blue-600 hover:bg-blue-700 text-white shadow-2xs transition">
                  <span>+</span> New Lead
                </button>
                <select id="stageFilterSelect" class="px-2.5 py-1.5 text-xs border border-slate-300 rounded-lg bg-white focus:outline-none font-medium">
                  <option value="ALL">All Stages</option>
                  <option value="DUE_TODAY">⚡ Follow-Up Due Today</option>
                  <option value="OVERDUE">🚨 Overdue Follow-Ups</option>
                  <option value="Lead In">Lead In</option>
                  <option value="Contact Made">Contact Made</option>
                  <option value="Follow Up">Follow Up</option>
                  <option value="Meeting Scheduled">Meeting Scheduled</option>
                  <option value="Contract Shared">Contract Shared</option>
                  <option value="Won">Won</option>
                </select>
                <input type="text" id="searchInput" placeholder="Search client, phone, venue..." class="px-3 py-1.5 text-xs border border-slate-300 rounded-lg focus:outline-none w-44 sm:w-56">
              </div>
            </div>

            <!-- Table View (Expansive full width) -->
            <div id="tableViewContainer" class="overflow-x-auto">
              <table class="w-full text-left border-collapse text-xs">
                <thead>
                  <tr class="bg-slate-50 text-slate-500 uppercase tracking-wider border-b border-slate-200 font-semibold">
                    <th class="py-3 px-4">Client & Vendor</th>
                    <th class="py-3 px-4">Event & Venue</th>
                    <th class="py-3 px-4">Follow-Up Schedule</th>
                    <th class="py-3 px-4">Pipeline Stage</th>
                    <th class="py-3 px-4 text-right">Deal Value</th>
                    <th class="py-3 px-4 text-right">Outreach & Actions</th>
                  </tr>
                </thead>
                <tbody id="contactsTableBody" class="divide-y divide-slate-100"></tbody>
              </table>
            </div>

            <!-- Kanban View (Spacious full width columns) -->
            <div id="kanbanViewContainer" class="hidden p-4 bg-slate-50/60 overflow-x-auto">
              <div class="text-[11px] text-slate-400 mb-2.5 font-medium">Tip: Drag any lead card and drop it into another stage to instantly advance the pipeline.</div>
              <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 xl:grid-cols-6 gap-3.5 custom-scrollbar min-w-[1100px]">
                
                <div class="kanban-col bg-slate-100/80 rounded-xl p-3 border border-slate-200 flex flex-col min-h-[650px] h-[calc(100vh-240px)]" data-stage="Lead In" id="kCol-Lead In">
                  <div class="flex items-center justify-between pb-2 mb-2 border-b border-slate-200 text-xs font-bold text-slate-800">
                    <span>Lead In</span>
                    <span class="k-count bg-white px-1.5 py-0.5 rounded text-[10px] text-slate-600 font-bold">0</span>
                  </div>
                  <div class="k-cards space-y-2.5 flex-1 overflow-y-auto"></div>
                </div>

                <div class="kanban-col bg-slate-100/80 rounded-xl p-3 border border-slate-200 flex flex-col min-h-[650px] h-[calc(100vh-240px)]" data-stage="Contact Made" id="kCol-Contact Made">
                  <div class="flex items-center justify-between pb-2 mb-2 border-b border-slate-200 text-xs font-bold text-slate-800">
                    <span>Contact Made</span>
                    <span class="k-count bg-white px-1.5 py-0.5 rounded text-[10px] text-slate-600 font-bold">0</span>
                  </div>
                  <div class="k-cards space-y-2.5 flex-1 overflow-y-auto"></div>
                </div>

                <div class="kanban-col bg-slate-100/80 rounded-xl p-3 border border-slate-200 flex flex-col min-h-[650px] h-[calc(100vh-240px)]" data-stage="Follow Up" id="kCol-Follow Up">
                  <div class="flex items-center justify-between pb-2 mb-2 border-b border-slate-200 text-xs font-bold text-slate-800">
                    <span>Follow Up</span>
                    <span class="k-count bg-white px-1.5 py-0.5 rounded text-[10px] text-slate-600 font-bold">0</span>
                  </div>
                  <div class="k-cards space-y-2.5 flex-1 overflow-y-auto"></div>
                </div>

                <div class="kanban-col bg-slate-100/80 rounded-xl p-3 border border-slate-200 flex flex-col min-h-[650px] h-[calc(100vh-240px)]" data-stage="Meeting Scheduled" id="kCol-Meeting Scheduled">
                  <div class="flex items-center justify-between pb-2 mb-2 border-b border-slate-200 text-xs font-bold text-slate-800">
                    <span>Meeting Set</span>
                    <span class="k-count bg-white px-1.5 py-0.5 rounded text-[10px] text-slate-600 font-bold">0</span>
                  </div>
                  <div class="k-cards space-y-2.5 flex-1 overflow-y-auto"></div>
                </div>

                <div class="kanban-col bg-slate-100/80 rounded-xl p-3 border border-slate-200 flex flex-col min-h-[650px] h-[calc(100vh-240px)]" data-stage="Contract Shared" id="kCol-Contract Shared">
                  <div class="flex items-center justify-between pb-2 mb-2 border-b border-slate-200 text-xs font-bold text-slate-800">
                    <span>Contract Shared</span>
                    <span class="k-count bg-white px-1.5 py-0.5 rounded text-[10px] text-slate-600 font-bold">0</span>
                  </div>
                  <div class="k-cards space-y-2.5 flex-1 overflow-y-auto"></div>
                </div>

                <div class="kanban-col bg-emerald-50/70 rounded-xl p-3 border border-emerald-200 flex flex-col min-h-[650px] h-[calc(100vh-240px)]" data-stage="Won" id="kCol-Won">
                  <div class="flex items-center justify-between pb-2 mb-2 border-b border-emerald-200 text-xs font-bold text-emerald-900">
                    <span>Won Deals</span>
                    <span class="k-count bg-white px-1.5 py-0.5 rounded text-[10px] text-emerald-800 font-bold">0</span>
                  </div>
                  <div class="k-cards space-y-2.5 flex-1 overflow-y-auto"></div>
                </div>

              </div>
            </div>

          </div>
        </div>
      </div>

      <!-- ========================================== -->
      <!-- VIEW 2: FINANCES & COMMISSION (ADMIN ONLY) -->
      <!-- ========================================== -->
      <div id="financePageSection" class="hidden space-y-6">
        
        <!-- Top Financial Overview -->
        <div class="bg-gradient-to-r from-slate-900 to-indigo-950 rounded-2xl p-6 text-white shadow-md">
          <h2 class="text-xl font-bold tracking-tight">Financial Accounts & Commission Hub</h2>
          <p class="text-xs text-slate-300 mt-1">Exclusive financial management, 10% agency commission, advance receipts, and vendor payouts.</p>
          
          <div class="grid grid-cols-2 lg:grid-cols-4 gap-4 mt-6 pt-5 border-t border-slate-800">
            <div>
              <div class="text-[11px] font-semibold uppercase tracking-wider text-slate-400">Total Contracted Volume</div>
              <div id="finGrossVolume" class="text-2xl font-bold text-white mt-1">₹0</div>
              <p class="text-[10px] text-slate-400 mt-0.5">Closed deals value</p>
            </div>
            <div class="p-3 rounded-xl bg-indigo-950/50 border border-indigo-700/50">
              <div class="text-[11px] font-semibold uppercase tracking-wider text-indigo-300">Agency Commission (10%)</div>
              <div id="finAgencyComm" class="text-2xl font-bold text-indigo-400 mt-1">₹0</div>
              <p class="text-[10px] text-indigo-200 mt-0.5">Earned agency fee</p>
            </div>
            <div>
              <div class="text-[11px] font-semibold uppercase tracking-wider text-emerald-400">Advance Collected</div>
              <div id="finAdvanceCollected" class="text-2xl font-bold text-emerald-400 mt-1">₹0</div>
              <p class="text-[10px] text-emerald-300/80 mt-0.5">Cash in hand</p>
            </div>
            <div>
              <div class="text-[11px] font-semibold uppercase tracking-wider text-amber-400">Pending Balance Due</div>
              <div id="finPendingDue" class="text-2xl font-bold text-amber-400 mt-1">₹0</div>
              <p class="text-[10px] text-amber-300/80 mt-0.5">Receivables from clients</p>
            </div>
          </div>
        </div>

        <!-- Vendor Commission Breakdown Table -->
        <div class="bg-white rounded-xl border border-slate-200 shadow-sm overflow-hidden">
          <div class="p-5 border-b border-slate-200">
            <h3 class="text-base font-bold text-slate-900">Vendor Accounts & 10% Commission Split</h3>
            <p class="text-xs text-slate-500 mt-0.5">Gross sales volume, 10% agency deduction, and 90% net vendor payable.</p>
          </div>
          <div class="overflow-x-auto">
            <table class="w-full text-left border-collapse text-xs">
              <thead>
                <tr class="bg-slate-50 text-slate-500 uppercase tracking-wider border-b border-slate-200 font-semibold">
                  <th class="py-3 px-4">Vendor Partner</th>
                  <th class="py-3 px-4">Won Deals</th>
                  <th class="py-3 px-4 text-right">Gross Contracted</th>
                  <th class="py-3 px-4 text-right text-indigo-700 font-bold">Agency Comm (10%)</th>
                  <th class="py-3 px-4 text-right text-slate-700 font-semibold">Vendor Net (90%)</th>
                  <th class="py-3 px-4 text-right text-emerald-700">Advance Paid</th>
                  <th class="py-3 px-4 text-right text-amber-700">Pending Due</th>
                </tr>
              </thead>
              <tbody id="vendorFinancesTableBody" class="divide-y divide-slate-100"></tbody>
            </table>
          </div>
        </div>

        <!-- Detailed Payment Ledger -->
        <div class="bg-white rounded-xl border border-slate-200 shadow-sm overflow-hidden">
          <div class="p-5 border-b border-slate-200">
            <h3 class="text-base font-bold text-slate-900">Confirmed Bookings Payment Ledger</h3>
            <p class="text-xs text-slate-500 mt-0.5">Client-by-client financial breakdown with advance records and commission split.</p>
          </div>
          <div class="overflow-x-auto">
            <table class="w-full text-left border-collapse text-xs">
              <thead>
                <tr class="bg-slate-50 text-slate-500 uppercase tracking-wider border-b border-slate-200 font-semibold">
                  <th class="py-3 px-4">Client & Vendor</th>
                  <th class="py-3 px-4">Wedding Date</th>
                  <th class="py-3 px-4 text-right">Total Deal</th>
                  <th class="py-3 px-4 text-right text-indigo-700 font-bold">10% Commission</th>
                  <th class="py-3 px-4 text-right text-emerald-700">Advance Paid</th>
                  <th class="py-3 px-4 text-right text-amber-700">Balance Due</th>
                  <th class="py-3 px-4 text-center">Status</th>
                </tr>
              </thead>
              <tbody id="finLedgerTableBody" class="divide-y divide-slate-100"></tbody>
            </table>
          </div>
        </div>
      </div>

      <!-- ========================================== -->
      <!-- VIEW 3: LOST ARCHIVE (WITH REASON & NOTES) -->
      <!-- ========================================== -->
      <div id="lostPageSection" class="hidden space-y-6">
        
        <!-- Summary Cards for Lost Leads -->
        <div class="grid grid-cols-1 sm:grid-cols-3 gap-4">
          <div class="bg-white p-4 rounded-xl border border-slate-200 shadow-sm">
            <div class="text-xs font-semibold uppercase tracking-wider text-slate-500">Archived Lost Deals</div>
            <div id="statTotalLostCount" class="text-2xl font-bold text-slate-900 mt-2">0</div>
            <p class="text-[11px] text-slate-400 mt-1">Total dropped inquiries</p>
          </div>
          <div class="bg-white p-4 rounded-xl border border-slate-200 shadow-sm">
            <div class="text-xs font-semibold uppercase tracking-wider text-slate-500">Lost Potential Value</div>
            <div id="statTotalLostValue" class="text-2xl font-bold text-red-600 mt-2">₹0</div>
            <p class="text-[11px] text-red-500 mt-1">Unrealized contract pipeline</p>
          </div>
          <div class="bg-white p-4 rounded-xl border border-slate-200 shadow-sm">
            <div class="text-xs font-semibold uppercase tracking-wider text-slate-500">Top Drop-Off Factor</div>
            <div id="statTopLostReason" class="text-lg font-bold text-amber-700 mt-2 truncate">-</div>
            <p class="text-[11px] text-slate-400 mt-1">Primary drop reason</p>
          </div>
        </div>

        <div class="bg-white rounded-xl border border-slate-200 shadow-sm overflow-hidden">
          <div class="p-4 border-b border-slate-200 flex flex-col sm:flex-row sm:items-center sm:justify-between gap-3">
            <div>
              <h3 class="text-base font-bold text-slate-900">Lost Leads Repository & Win-Back Archive</h3>
              <p class="text-xs text-slate-500 mt-0.5">Recorded drop-off reasons, client budget mismatch, and interaction remarks.</p>
            </div>
            <div class="flex items-center gap-2">
              <span class="text-xs text-slate-400 font-medium">Filter by Reason:</span>
              <select id="filterLostReasonSelect" class="px-2.5 py-1.5 text-xs border border-slate-300 rounded-lg bg-white focus:outline-none font-medium">
                <option value="ALL">All Reasons</option>
                <option value="Budget">Budget</option>
                <option value="Booked Someone Else">Booked Someone Else</option>
                <option value="Ghost / Stopped Replying">Ghost / Stopped Replying</option>
                <option value="Not Interested">Not Interested</option>
                <option value="Date Unavailable">Date Unavailable</option>
                <option value="Other">Other</option>
              </select>
            </div>
          </div>
          <div class="overflow-x-auto">
            <table class="w-full text-left border-collapse text-xs">
              <thead>
                <tr class="bg-slate-50 text-slate-500 uppercase tracking-wider border-b border-slate-200 font-semibold">
                  <th class="py-3 px-4">Client & Vendor</th>
                  <th class="py-3 px-4">Wedding Date & Venue</th>
                  <th class="py-3 px-4">Drop-Off Reason</th>
                  <th class="py-3 px-4">Client Budget</th>
                  <th class="py-3 px-4 max-w-xs">Remarks / Feedback</th>
                  <th class="py-3 px-4 text-right">Quoted Value</th>
                  <th class="py-3 px-4 text-right">Actions</th>
                </tr>
              </thead>
              <tbody id="lostTableBody" class="divide-y divide-slate-100"></tbody>
            </table>
          </div>
        </div>
      </div>

      <!-- ========================================== -->
      <!-- VIEW 4: ACCESS & TEAM SETTINGS             -->
      <!-- ========================================== -->
      <div id="permissionsPageSection" class="hidden space-y-6">
        <div class="bg-white rounded-2xl border border-slate-200 shadow-sm p-6 space-y-6">
          <div class="border-b border-slate-100 pb-4">
            <h2 class="text-lg font-bold text-slate-900">Team Access & Vendor Assignment Hub</h2>
            <p class="text-xs text-slate-500 mt-1">Configure which sales reps can see which photographers or makeup artists, and generate restricted links.</p>
          </div>

          <!-- Section 1: Team Accounts -->
          <div class="p-5 bg-slate-50 border border-slate-200 rounded-xl space-y-4">
            <div class="flex flex-col sm:flex-row sm:items-center sm:justify-between gap-2">
              <div>
                <h3 class="text-sm font-bold text-slate-900 flex items-center gap-2"><span>👥</span> Team Accounts</h3>
                <p class="text-xs text-slate-500 mt-1">Create a login for each sales closer and choose which vendors they can see. Admins see everything.</p>
              </div>
              <button id="addUserBtn" type="button" class="px-4 py-2 text-xs font-bold bg-slate-900 hover:bg-slate-800 text-white rounded-lg shadow-sm whitespace-nowrap transition">+ Add Team Member</button>
            </div>
            <div id="teamList" class="space-y-2"></div>
          </div>

          <!-- Section 2: Master Security PIN Settings -->
          <div class="p-5 bg-slate-50 border border-slate-200 rounded-xl space-y-3">
            <h3 class="text-sm font-bold text-slate-900 flex items-center gap-2">
              <span>🔐</span> Master Security PIN Settings
            </h3>
            <p class="text-xs text-slate-500">This PIN is required to delete any lead or vendor partner, and to reset a forgotten Admin password from the sign-in screen. Keep it strictly confidential.</p>
            
            <div class="flex items-center gap-3">
              <input type="password" id="changeMasterPinInput" maxlength="6" value="7818" class="w-32 px-3 py-2 text-center text-sm font-mono font-bold tracking-widest bg-white border border-slate-300 rounded-lg">
              <button id="saveNewPinBtn" type="button" class="px-4 py-2 text-xs font-bold bg-blue-600 hover:bg-blue-700 text-white rounded-lg shadow-sm transition">
                Update Master PIN
              </button>
            </div>
          </div>
        </div>
      </div>

    </div>

  </main>

  <!-- =================================================== -->
  <!-- MODAL 0: NEW WEDDING LEAD POPUP (Item 2: Toggleable) -->
  <!-- =================================================== -->
  <div id="addLeadModal" class="hidden fixed inset-0 bg-slate-900/60 backdrop-blur-xs z-50 flex items-center justify-center p-4">
    <div class="bg-white rounded-2xl max-w-lg w-full p-6 shadow-2xl border border-slate-200 animate-pop">
      <div class="flex items-center justify-between pb-3 border-b border-slate-100 mb-4">
        <div>
          <h3 class="text-base font-bold text-slate-900">New Wedding Lead</h3>
          <p class="text-xs text-slate-500 mt-0.5">Quick lead intake & date booking without mandatory constraints.</p>
        </div>
        <button id="closeAddLeadModalBtn" type="button" class="text-slate-400 hover:text-slate-600 text-lg font-bold p-1">✕</button>
      </div>

      <form id="contactForm" class="space-y-3.5 text-xs">
        <div class="grid grid-cols-1 sm:grid-cols-2 gap-3">
          <div>
            <label class="block font-semibold text-slate-700 uppercase tracking-wider mb-1">Client Name</label>
            <input type="text" id="contactName" placeholder="e.g. Priya & Rohan" class="w-full px-3 py-2 text-xs border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-slate-900 bg-white">
          </div>
          <div>
            <label class="block font-semibold text-slate-700 uppercase tracking-wider mb-1">Assign Vendor</label>
            <select id="vendorSelect" class="w-full px-3 py-2 text-xs border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-slate-900 bg-white text-slate-800 font-medium"></select>
          </div>
        </div>

        <div class="grid grid-cols-2 gap-3">
          <div>
            <label class="block font-semibold text-slate-700 uppercase tracking-wider mb-1">Phone / WhatsApp</label>
            <input type="tel" id="contactPhone" placeholder="9876543210" class="w-full px-3 py-2 text-xs border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-slate-900 bg-white">
          </div>
          <div>
            <label class="block font-semibold text-slate-700 uppercase tracking-wider mb-1">Instagram</label>
            <input type="text" id="instaId" placeholder="@priya" class="w-full px-3 py-2 text-xs border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-slate-900 bg-white">
          </div>
        </div>

        <div class="grid grid-cols-2 gap-3">
          <div>
            <label class="block font-semibold text-slate-700 uppercase tracking-wider mb-1">Wedding Date</label>
            <input type="date" id="weddingDate" class="w-full px-3 py-2 text-xs border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-slate-900 bg-white text-slate-700">
          </div>
          <div>
            <label class="block font-semibold text-slate-700 uppercase tracking-wider mb-1">Venue / City</label>
            <input type="text" id="venueLocation" placeholder="Jaipur" class="w-full px-3 py-2 text-xs border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-slate-900 bg-white">
          </div>
        </div>

        <div class="grid grid-cols-2 gap-3">
          <div>
            <label class="block font-semibold text-slate-700 uppercase tracking-wider mb-1">Pipeline Stage</label>
            <select id="dealStage" class="w-full px-3 py-2 text-xs border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-slate-900 bg-white font-medium">
              <option value="Lead In">Lead In</option>
              <option value="Contact Made">Contact Made</option>
              <option value="Follow Up">Follow Up</option>
              <option value="Meeting Scheduled">Meeting Scheduled</option>
              <option value="Contract Shared">Contract Shared</option>
              <option value="Won">Won</option>
            </select>
          </div>
          <div>
            <label class="block font-semibold text-slate-700 uppercase tracking-wider mb-1">Next Follow-Up Date</label>
            <input type="date" id="followUpDate" class="w-full px-3 py-2 text-xs border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-slate-900 bg-white text-slate-700">
          </div>
        </div>

        <div class="grid grid-cols-2 gap-3">
          <div>
            <label class="block font-semibold text-slate-700 uppercase tracking-wider mb-1">Package Value (₹)</label>
            <input type="number" id="dealValue" placeholder="150000" class="w-full px-3 py-2 text-xs border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-slate-900 bg-white">
          </div>
          <div>
            <label class="block font-semibold text-slate-700 uppercase tracking-wider mb-1">Advance Received (₹)</label>
            <input type="number" id="advancePaid" placeholder="0" class="w-full px-3 py-2 text-xs border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-slate-900 bg-white">
          </div>
        </div>

        <div>
          <label class="block font-semibold text-slate-700 uppercase tracking-wider mb-1">Requirements / Client Notes</label>
          <textarea id="contactNotes" rows="2" placeholder="Bridal shoot, pre-wedding requirements, crew preferences..." class="w-full px-3 py-2 text-xs border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-slate-900 bg-white resize-none"></textarea>
        </div>

        <div class="flex items-center justify-end gap-2 pt-2 border-t border-slate-100">
          <button id="cancelAddLeadBtn" type="button" class="px-4 py-2 font-medium text-slate-600 hover:bg-slate-100 rounded-lg transition">Cancel</button>
          <button type="submit" class="px-5 py-2 font-bold text-white bg-blue-600 hover:bg-blue-700 rounded-lg shadow-sm transition">Save Wedding Lead</button>
        </div>
      </form>
    </div>
  </div>

  <!-- ========================================== -->
  <!-- MODAL 1: DAILY OUTREACH SPRINT POPUP       -->
  <!-- ========================================== -->
  <div id="sprintModal" class="hidden fixed inset-0 bg-slate-900/60 backdrop-blur-xs z-50 flex items-center justify-center p-4">
    <div class="bg-white rounded-2xl shadow-2xl border border-slate-200 max-w-xl w-full p-6 animate-pop">
      <div class="flex items-center justify-between pb-3 border-b border-slate-100 mb-4">
        <div class="flex items-center gap-2">
          <span class="px-2 py-0.5 rounded text-[11px] font-bold bg-amber-100 text-amber-900 uppercase">Power Sprint</span>
          <span id="sprintLeadIndex" class="text-xs font-bold text-slate-600">Lead 1 of 5</span>
        </div>
        <button id="closeSprintModalBtn" type="button" class="text-slate-400 hover:text-slate-600 text-lg font-bold p-1">✕</button>
      </div>
      <div id="sprintLeadCard" class="space-y-4"></div>
    </div>
  </div>

  <!-- ========================================== -->
  <!-- MODAL 2: ADD VENDOR MODAL                  -->
  <!-- ========================================== -->
  <div id="addVendorModal" class="hidden fixed inset-0 bg-slate-900/50 backdrop-blur-xs z-50 flex items-center justify-center p-4">
    <div class="bg-white rounded-xl shadow-xl border border-slate-200 max-w-sm w-full p-6 animate-pop">
      <h3 class="font-bold text-slate-900 text-base mb-4">Add New Vendor Space</h3>
      <div class="space-y-3">
        <div>
          <label class="block text-xs font-semibold text-slate-700 uppercase tracking-wider mb-1">Vendor Brand Name *</label>
          <input type="text" id="newVendorName" placeholder="e.g. SLC Productions" class="w-full px-3 py-2 text-sm border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-slate-900 bg-white">
        </div>
        <div>
          <label class="block text-xs font-semibold text-slate-700 uppercase tracking-wider mb-1">Service Category</label>
          <select id="newVendorCategory" class="w-full px-3 py-2 text-sm border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-slate-900 bg-white">
            <option value="Photographer">Photographer</option>
            <option value="Makeup Artist">Makeup Artist</option>
            <option value="Wedding Decor">Wedding Decor</option>
            <option value="Mehendi Artist">Mehendi Artist</option>
            <option value="Other">Other Vendor</option>
          </select>
        </div>
        <div class="flex items-center justify-end gap-2 pt-3">
          <button id="cancelVendorModalBtn" type="button" class="px-4 py-2 text-xs font-medium text-slate-600 hover:bg-slate-100 rounded-lg">Cancel</button>
          <button id="saveVendorBtn" type="button" class="px-4 py-2 text-xs font-semibold text-white bg-slate-900 hover:bg-slate-800 rounded-lg shadow-sm">Save & Switch Vendor</button>
        </div>
      </div>
    </div>
  </div>

  <!-- ========================================== -->
  <!-- MODAL 3: MANAGE VENDORS                    -->
  <!-- ========================================== -->
  <div id="manageVendorsModal" class="hidden fixed inset-0 bg-slate-900/50 backdrop-blur-xs z-50 flex items-center justify-center p-4">
    <div class="bg-white rounded-xl shadow-xl border border-slate-200 max-w-md w-full p-6 animate-pop">
      <div class="flex items-center justify-between pb-3 border-b border-slate-100 mb-4">
        <div>
          <h3 class="font-bold text-slate-900 text-base">Active Vendor Partners</h3>
          <p class="text-xs text-slate-500">Only Admin may remove vendor spaces with PIN authorization.</p>
        </div>
        <button id="closeManageVendorsBtn" type="button" class="text-slate-400 hover:text-slate-600 text-lg font-bold p-1">✕</button>
      </div>
      <div id="vendorsListContainer" class="space-y-2 max-h-72 overflow-y-auto"></div>
    </div>
  </div>

  <!-- ========================================== -->
  <!-- MODAL 4: ADMIN PIN UNLOCK (SECURE: 7818)   -->
  <!-- ========================================== -->
  <div id="adminPinModal" class="hidden fixed inset-0 bg-slate-900/60 backdrop-blur-xs z-50 flex items-center justify-center p-4">
    <div class="bg-white rounded-2xl max-w-xs w-full p-6 shadow-2xl border border-slate-200 animate-pop text-center">
      <div class="w-12 h-12 rounded-2xl bg-slate-100 text-slate-900 font-bold flex items-center justify-center text-xl mx-auto mb-3 shadow-xs">
        🔒
      </div>
      <h3 class="text-base font-bold text-slate-900">Admin Authentication</h3>
      <p class="text-xs text-slate-500 mt-1 mb-4">Enter Master Admin PIN to unlock full financial access and delete permissions.</p>
      
      <div class="mb-4">
        <input type="password" id="adminPinInput" maxlength="6" placeholder="••••" class="w-32 mx-auto text-center px-3 py-2 text-lg font-mono tracking-widest font-bold border-2 border-slate-300 rounded-xl focus:outline-none focus:ring-2 focus:ring-slate-900 bg-slate-50 text-slate-900">
      </div>

      <div class="grid grid-cols-2 gap-2">
        <button id="cancelAdminPinBtn" type="button" class="py-2 px-3 text-xs font-semibold rounded-xl bg-slate-100 hover:bg-slate-200 text-slate-700 transition">
          Cancel
        </button>
        <button id="confirmAdminPinBtn" type="button" class="py-2 px-3 text-xs font-bold rounded-xl bg-slate-900 hover:bg-slate-800 text-white shadow-xs transition">
          Unlock
        </button>
      </div>
    </div>
  </div>

  <!-- =================================================== -->
  <!-- MODAL 5: DOUBLE-CONFIRMATION + PIN DELETION MODAL   -->
  <!-- =================================================== -->
  <div id="secureDeleteModal" class="hidden fixed inset-0 bg-slate-900/60 backdrop-blur-xs z-50 flex items-center justify-center p-4">
    <div class="bg-white rounded-2xl max-w-sm w-full p-6 shadow-2xl border border-red-200 animate-pop text-center">
      <div class="w-12 h-12 rounded-2xl bg-red-100 text-red-600 font-bold flex items-center justify-center text-xl mx-auto mb-3 shadow-xs">
        ⚠️
      </div>
      <h3 class="text-base font-bold text-slate-900" id="deleteModalTitle">Confirm Deletion</h3>
      <p class="text-xs text-slate-500 mt-1 mb-4" id="deleteModalSubtitle">This action is permanent. Enter Master PIN to authorize.</p>
      
      <div class="mb-4">
        <input type="password" id="deletePinInput" maxlength="6" placeholder="Enter PIN (7818)" class="w-36 mx-auto text-center px-3 py-2 text-lg font-mono tracking-widest font-bold border-2 border-red-300 rounded-xl focus:outline-none focus:ring-2 focus:ring-red-500 bg-red-50/50 text-slate-900">
      </div>

      <div class="grid grid-cols-2 gap-2">
        <button id="cancelSecureDeleteBtn" type="button" class="py-2 px-3 text-xs font-semibold rounded-xl bg-slate-100 hover:bg-slate-200 text-slate-700 transition">
          Cancel
        </button>
        <button id="confirmSecureDeleteBtn" type="button" class="py-2 px-3 text-xs font-bold rounded-xl bg-red-600 hover:bg-red-700 text-white shadow-xs transition">
          Confirm & Delete
        </button>
      </div>
    </div>
  </div>

  <!-- =================================================== -->
  <!-- MODAL 6: MARK LOST REASON & REMARKS MODAL           -->
  <!-- =================================================== -->
  <div id="markLostModal" class="hidden fixed inset-0 bg-slate-900/60 backdrop-blur-xs z-50 flex items-center justify-center p-4">
    <div class="bg-white rounded-2xl max-w-md w-full p-6 shadow-2xl border border-slate-200 animate-pop">
      <div class="flex items-center justify-between pb-3 border-b border-slate-100 mb-4">
        <div>
          <h3 class="text-base font-bold text-slate-900">Mark Deal as Lost</h3>
          <p class="text-xs text-slate-500 mt-0.5" id="lostModalClientSubtitle">Record lost reason and client feedback</p>
        </div>
        <button id="closeMarkLostBtn" type="button" class="text-slate-400 hover:text-slate-600 text-lg font-bold p-1">✕</button>
      </div>

      <form id="markLostForm" class="space-y-3.5 text-xs">
        <div>
          <label class="block font-bold text-slate-700 uppercase tracking-wider mb-1">Reason for Drop-Off *</label>
          <select id="lostReasonSelect" class="w-full px-3 py-2 text-xs border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-slate-900 bg-white font-medium">
            <option value="Budget">Budget (Price mismatch / Over budget)</option>
            <option value="Booked Someone Else">Booked Someone Else (Chose competitor)</option>
            <option value="Ghost / Stopped Replying">Ghost (Client stopped replying / unresponsive)</option>
            <option value="Not Interested">Not Interested (Decided against service)</option>
            <option value="Date Unavailable">Date Unavailable (Vendor slot overbooked)</option>
            <option value="Other">Other Reason</option>
          </select>
        </div>

        <!-- Conditional Client Target Budget Input -->
        <div id="lostBudgetBox" class="p-3 bg-amber-50/70 border border-amber-200 rounded-xl space-y-1">
          <label class="block font-bold text-amber-900 uppercase tracking-wider text-[11px]">What was the client's actual budget? (₹)</label>
          <input type="number" id="lostClientBudgetInput" placeholder="e.g. 100000" class="w-full px-3 py-1.5 text-xs border border-amber-300 rounded-lg bg-white focus:outline-none focus:ring-2 focus:ring-amber-500">
          <p class="text-[10px] text-amber-700">Helps track pricing trends and future win-back campaigns.</p>
        </div>

        <div>
          <label class="block font-bold text-slate-700 uppercase tracking-wider mb-1">Lost Remarks / Feedback Notes *</label>
          <textarea id="lostRemarksInput" rows="3" placeholder="e.g. Quoted 2.2L for 3 days, client expected under 1.5L. Bride decided to go with a local studio in Jaipur." class="w-full px-3 py-2 text-xs border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-slate-900 bg-white resize-none" required></textarea>
        </div>

        <div class="flex items-center justify-end gap-2 pt-2">
          <button id="cancelMarkLostBtn" type="button" class="px-4 py-2 font-medium text-slate-600 hover:bg-slate-100 rounded-lg transition">Cancel</button>
          <button type="submit" class="px-4 py-2 font-bold text-white bg-slate-900 hover:bg-slate-800 rounded-lg shadow-sm transition">Archive as Lost Deal</button>
        </div>
      </form>
    </div>
  </div>

  <script>
    const stagesList = ['Lead In', 'Contact Made', 'Follow Up', 'Meeting Scheduled', 'Contract Shared', 'Won'];
    const defaultVendors = [
      { id: 'v1', name: "The Wedding Shaire", category: "Photographer" },
      { id: 'v2', name: "wetherealstory", category: "Photographer" }
    ];

    let vendors = JSON.parse(localStorage.getItem('crm_vendors') || 'null') || defaultVendors;
    let contacts = JSON.parse(localStorage.getItem('crm_contacts') || '[]');
    let lostContacts = JSON.parse(localStorage.getItem('crm_lost') || '[]');
    let selectedVendorId = 'ALL';
    let currentPage = 'pipeline';
    let currentViewMode = 'table'; // 'table' or 'kanban'

    // Master PIN (used for secure deletions & admin password recovery)
    let masterAdminPin = localStorage.getItem('crm_admin_pin') || "7818";

    // ==========================================
    // AUTH STATE (role & vendor access come from the signed-in account)
    // ==========================================
    let users = JSON.parse(localStorage.getItem('crm_users') || '[]');
    let currentUser = null;
    let currentRole = 'sales';       // least privilege until someone signs in
    let allowedVendorIds = null;     // null = all vendors; array = restricted

    const todayStr = new Date().toISOString().split('T')[0];

    function saveData() {
      localStorage.setItem('crm_vendors', JSON.stringify(vendors));
      localStorage.setItem('crm_contacts', JSON.stringify(contacts));
      localStorage.setItem('crm_lost', JSON.stringify(lostContacts));
      localStorage.setItem('crm_users', JSON.stringify(users));
      localStorage.setItem('crm_admin_pin', masterAdminPin);
    }

    function applyRolePermissions() {
      const sidebarTag = document.getElementById('sidebarRoleTag');
      const rolePill = document.getElementById('rolePillStatus');
      const adminNavSection = document.getElementById('adminNavSection');
      const headerAddVendorBtn = document.getElementById('headerAddVendorBtn');

      if (currentUser) {
        document.getElementById('userNameLabel').textContent = currentUser.name;
        document.getElementById('userHandleLabel').textContent = currentUser.username;
        document.getElementById('userAvatar').textContent = (currentUser.name || '?').trim().charAt(0).toUpperCase();
      }

      if (currentRole === 'sales') {
        sidebarTag.textContent = 'Sales Closer';
        sidebarTag.className = 'text-[10px] text-blue-400 font-semibold';
        rolePill.textContent = 'Sales';
        rolePill.className = 'px-2 py-0.5 rounded text-[10px] font-bold bg-blue-500/20 text-blue-400 border border-blue-500/30';

        adminNavSection.classList.add('hidden');
        headerAddVendorBtn.classList.add('hidden');

        if (currentPage === 'finances' || currentPage === 'permissions') {
          switchPage('pipeline');
        }
      } else {
        sidebarTag.textContent = 'Admin Workspace';
        sidebarTag.className = 'text-[10px] text-emerald-400 font-semibold';
        rolePill.textContent = 'Admin';
        rolePill.className = 'px-2 py-0.5 rounded text-[10px] font-bold bg-emerald-500/20 text-emerald-400 border border-emerald-500/30';

        adminNavSection.classList.remove('hidden');
        headerAddVendorBtn.classList.remove('hidden');
      }
      saveData();
    }

    // ==========================================
    // SIGN-IN ENGINE
    // ==========================================
    const $ = (id) => document.getElementById(id);
    const escapeHtml = (str) => String(str ?? '').replace(/[&<>"']/g, ch => ({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":'&#39;'}[ch]));

    function randomSalt() {
      const arr = new Uint8Array(16);
      (window.crypto || {}).getRandomValues ? crypto.getRandomValues(arr) : arr.forEach((_, i) => arr[i] = Math.floor(Math.random() * 256));
      return Array.from(arr).map(b => b.toString(16).padStart(2, '0')).join('');
    }

    async function hashPassword(password, salt) {
      const input = salt + '::' + password;
      if (window.crypto && crypto.subtle) {
        const buf = await crypto.subtle.digest('SHA-256', new TextEncoder().encode(input));
        return Array.from(new Uint8Array(buf)).map(b => b.toString(16).padStart(2, '0')).join('');
      }
      // Fallback for very old browsers / non-secure contexts (FNV-1a, not cryptographic)
      let h = 0x811c9dc5;
      for (let i = 0; i < input.length; i++) { h ^= input.charCodeAt(i); h = Math.imul(h, 0x01000193) >>> 0; }
      return 'fnv' + h.toString(16);
    }

    function showMsg(el, msg) { el.textContent = msg; el.classList.remove('hidden'); }
    function hideMsg(el) { el.classList.add('hidden'); el.textContent = ''; }

    function showAuthPane(which) {
      ['signInPane', 'setupPane', 'resetPane'].forEach(id => $(id).classList.add('hidden'));
      $(which).classList.remove('hidden');
      $('authSubtitle').textContent = which === 'setupPane' ? 'First-time setup' : which === 'resetPane' ? 'Recover admin access' : 'Sign in to continue';
    }

    function showAuthScreen() {
      $('authScreen').classList.remove('hidden');
      document.body.style.overflow = 'hidden';
      $('loginPassword').value = '';
      hideMsg($('loginError'));
      if (users.length === 0) {
        showAuthPane('setupPane');
        $('setupName').focus();
      } else {
        showAuthPane('signInPane');
        $('loginUsername').focus();
      }
    }

    function startSession(user, remember) {
      const store = remember ? localStorage : sessionStorage;
      localStorage.removeItem('crm_session');
      sessionStorage.removeItem('crm_session');
      store.setItem('crm_session', user.id);
      enterApp(user);
    }

    function enterApp(user) {
      currentUser = user;
      currentRole = user.role === 'admin' ? 'admin' : 'sales';
      allowedVendorIds = (currentRole === 'sales' && Array.isArray(user.vendorIds)) ? user.vendorIds : null;
      selectedVendorId = 'ALL';
      currentPage = 'pipeline';
      $('authScreen').classList.add('hidden');
      document.body.style.overflow = '';
      switchPage('pipeline');
      applyRolePermissions();
      renderNav();
      renderPipeline();
    }

    function signOut() {
      localStorage.removeItem('crm_session');
      sessionStorage.removeItem('crm_session');
      currentUser = null;
      currentRole = 'sales';
      allowedVendorIds = null;
      if (isBoardFullscreen) toggleBoardFullscreen();
      $('loginUsername').value = '';
      showAuthScreen();
    }

    // Failed-attempt throttle (per browser)
    function getLockInfo() { return JSON.parse(localStorage.getItem('crm_login_lock') || '{"fails":0,"until":0}'); }
    function setLockInfo(v) { localStorage.setItem('crm_login_lock', JSON.stringify(v)); }

    async function attemptLogin() {
      const err = $('loginError'); hideMsg(err);
      const lock = getLockInfo();
      if (lock.until > Date.now()) {
        return showMsg(err, `Too many attempts. Try again in ${Math.ceil((lock.until - Date.now()) / 1000)}s.`);
      }
      const username = $('loginUsername').value.trim().toLowerCase();
      const password = $('loginPassword').value;
      if (!username || !password) return showMsg(err, 'Enter your username and password.');

      const user = users.find(u => u.username === username);
      const ok = user && (await hashPassword(password, user.salt)) === user.hash;
      if (!ok) {
        lock.fails = (lock.fails || 0) + 1;
        if (lock.fails >= 5) { lock.until = Date.now() + 30000; lock.fails = 0; }
        setLockInfo(lock);
        $('loginPassword').value = '';
        return showMsg(err, lock.until > Date.now() ? 'Too many attempts. Locked for 30 seconds.' : 'Incorrect username or password.');
      }
      setLockInfo({ fails: 0, until: 0 });
      startSession(user, $('loginRemember').checked);
    }

    $('loginBtn').onclick = attemptLogin;
    ['loginUsername', 'loginPassword'].forEach(id => $(id).addEventListener('keyup', e => { if (e.key === 'Enter') attemptLogin(); }));
    $('toggleLoginPwd').onclick = () => {
      const f = $('loginPassword'); const show = f.type === 'password';
      f.type = show ? 'text' : 'password';
      $('toggleLoginPwd').textContent = show ? 'Hide' : 'Show';
    };

    // First-run admin creation
    $('setupBtn').onclick = async () => {
      const err = $('setupError'); hideMsg(err);
      const name = $('setupName').value.trim();
      const username = $('setupUsername').value.trim().toLowerCase();
      const pw = $('setupPassword').value;
      if (!name || !username) return showMsg(err, 'Name and username are required.');
      if (!/^[a-z0-9._-]{3,}$/.test(username)) return showMsg(err, 'Username: at least 3 characters — letters, numbers, . _ - only.');
      if (pw.length < 6) return showMsg(err, 'Password must be at least 6 characters.');
      if (pw !== $('setupPassword2').value) return showMsg(err, 'Passwords do not match.');
      const salt = randomSalt();
      const admin = { id: 'u_' + Date.now(), name, username, salt, hash: await hashPassword(pw, salt), role: 'admin', vendorIds: null };
      users.push(admin);
      saveData();
      ['setupName','setupUsername','setupPassword','setupPassword2'].forEach(id => $(id).value = '');
      startSession(admin, false);
    };

    // Forgot admin password (Master PIN recovery)
    $('forgotBtn').onclick = () => { hideMsg($('resetError')); showAuthPane('resetPane'); $('resetUsername').focus(); };
    $('resetCancelBtn').onclick = () => showAuthPane('signInPane');
    $('resetConfirmBtn').onclick = async () => {
      const err = $('resetError'); hideMsg(err);
      const username = $('resetUsername').value.trim().toLowerCase();
      const user = users.find(u => u.username === username && u.role === 'admin');
      if (!user) return showMsg(err, 'No Admin account found with that username.');
      if ($('resetPin').value.trim() !== masterAdminPin) return showMsg(err, 'Incorrect Master PIN.');
      const np = $('resetNewPwd').value;
      if (np.length < 6) return showMsg(err, 'New password must be at least 6 characters.');
      user.salt = randomSalt();
      user.hash = await hashPassword(np, user.salt);
      saveData();
      ['resetUsername','resetPin','resetNewPwd'].forEach(id => $(id).value = '');
      showAuthPane('signInPane');
      $('loginUsername').value = username;
      $('loginPassword').focus();
      alert('✅ Password reset. Please sign in with your new password.');
    };

    // Sign out buttons
    $('signOutBtn').onclick = signOut;
    $('quickLockToggleBtn').onclick = signOut;

    // Change my password
    $('changePwdBtn').onclick = () => {
      ['cpCurrent','cpNew'].forEach(id => $(id).value = '');
      hideMsg($('cpError'));
      $('changePwdModal').classList.remove('hidden');
      $('cpCurrent').focus();
    };
    $('cpCancelBtn').onclick = () => $('changePwdModal').classList.add('hidden');
    $('cpSaveBtn').onclick = async () => {
      const err = $('cpError'); hideMsg(err);
      if ((await hashPassword($('cpCurrent').value, currentUser.salt)) !== currentUser.hash) return showMsg(err, 'Current password is incorrect.');
      const np = $('cpNew').value;
      if (np.length < 6) return showMsg(err, 'New password must be at least 6 characters.');
      currentUser.salt = randomSalt();
      currentUser.hash = await hashPassword(np, currentUser.salt);
      saveData();
      $('changePwdModal').classList.add('hidden');
      alert('✅ Password updated.');
    };

    // ==========================================
    // TEAM ACCOUNT MANAGEMENT (Admin)
    // ==========================================
    let editingUserId = null;

    function renderTeamList() {
      const el = $('teamList');
      if (!el) return;
      el.innerHTML = users.map(u => {
        const isMe = currentUser && u.id === currentUser.id;
        const vendorText = u.role === 'admin' ? 'All vendors + finances'
          : (!Array.isArray(u.vendorIds) ? 'All vendors'
            : (u.vendorIds.length ? u.vendorIds.map(id => (vendors.find(v => v.id === id) || {}).name).filter(Boolean).join(', ') : 'No vendors assigned'));
        return `
          <div class="flex flex-col sm:flex-row sm:items-center gap-2 p-3 bg-white border border-slate-200 rounded-lg">
            <div class="w-8 h-8 rounded-full bg-slate-900 text-white text-xs font-bold flex items-center justify-center shrink-0">${escapeHtml((u.name||'?').charAt(0).toUpperCase())}</div>
            <div class="min-w-0 flex-1">
              <div class="text-xs font-bold text-slate-900 flex items-center gap-1.5 flex-wrap">
                ${escapeHtml(u.name)} <span class="font-mono font-medium text-slate-400">@${escapeHtml(u.username)}</span>
                <span class="px-1.5 py-0.5 rounded text-[10px] font-bold ${u.role === 'admin' ? 'bg-emerald-100 text-emerald-700' : 'bg-blue-100 text-blue-700'}">${u.role === 'admin' ? 'Admin' : 'Sales'}</span>
                ${isMe ? '<span class="text-[10px] font-semibold text-slate-400">(you)</span>' : ''}
              </div>
              <div class="text-[11px] text-slate-500 truncate" title="${escapeHtml(vendorText)}">${escapeHtml(vendorText)}</div>
            </div>
            <div class="flex gap-1.5 shrink-0">
              <button onclick="openUserModal('${u.id}')" class="px-2.5 py-1 text-xs font-semibold rounded-lg bg-white border border-slate-300 text-slate-700 hover:bg-slate-50 transition">Edit</button>
              ${isMe ? '' : `<button onclick="removeUser('${u.id}')" class="px-2.5 py-1 text-xs font-semibold rounded-lg bg-white border border-red-200 text-red-600 hover:bg-red-50 transition">Remove</button>`}
            </div>
          </div>`;
      }).join('');
    }

    function renderUserVendorChecklist(selected) {
      $('umVendorList').innerHTML = vendors.map(v => `
        <label class="flex items-center gap-2 p-2 rounded-lg border border-slate-200 bg-white cursor-pointer">
          <input type="checkbox" value="${v.id}" class="um-vendor-cb rounded text-blue-600" ${selected && selected.includes(v.id) ? 'checked' : ''}>
          <span class="font-semibold text-slate-800">${escapeHtml(v.name)}</span>
        </label>`).join('');
    }

    function syncUserModalVisibility() {
      const isSales = $('umRole').value === 'sales';
      $('umVendorBlock').classList.toggle('hidden', !isSales);
      $('umVendorList').classList.toggle('hidden', $('umAllVendors').checked);
    }

    window.openUserModal = function(id) {
      if (currentRole !== 'admin') return;
      editingUserId = id || null;
      const u = id ? users.find(x => x.id === id) : null;
      hideMsg($('umError'));
      $('userModalTitle').textContent = u ? 'Edit Team Member' : 'Add Team Member';
      $('umName').value = u ? u.name : '';
      $('umUsername').value = u ? u.username : '';
      $('umPassword').value = '';
      $('umPassword').placeholder = u ? 'Leave blank to keep current' : '';
      $('umPwdLabel').textContent = u ? 'New Password' : 'Password *';
      $('umRole').value = u ? u.role : 'sales';
      const restricted = u && u.role === 'sales' && Array.isArray(u.vendorIds);
      $('umAllVendors').checked = !restricted;
      renderUserVendorChecklist(restricted ? u.vendorIds : []);
      syncUserModalVisibility();
      $('userModal').classList.remove('hidden');
      $('umName').focus();
    };

    $('addUserBtn').onclick = () => openUserModal(null);
    $('umCancelBtn').onclick = () => $('userModal').classList.add('hidden');
    $('umRole').onchange = syncUserModalVisibility;
    $('umAllVendors').onchange = syncUserModalVisibility;

    $('umSaveBtn').onclick = async () => {
      const err = $('umError'); hideMsg(err);
      const name = $('umName').value.trim();
      const username = $('umUsername').value.trim().toLowerCase();
      const pw = $('umPassword').value;
      const role = $('umRole').value;
      if (!name || !username) return showMsg(err, 'Name and username are required.');
      if (!/^[a-z0-9._-]{3,}$/.test(username)) return showMsg(err, 'Username: at least 3 characters — letters, numbers, . _ - only.');
      if (users.some(u => u.username === username && u.id !== editingUserId)) return showMsg(err, 'That username is already taken.');
      if (!editingUserId && pw.length < 6) return showMsg(err, 'Password must be at least 6 characters.');
      if (editingUserId && pw && pw.length < 6) return showMsg(err, 'New password must be at least 6 characters.');

      let vendorIds = null;
      if (role === 'sales' && !$('umAllVendors').checked) {
        vendorIds = Array.from(document.querySelectorAll('.um-vendor-cb:checked')).map(cb => cb.value);
        if (vendorIds.length === 0) return showMsg(err, 'Select at least one vendor, or tick "Access to all vendors".');
      }

      if (editingUserId) {
        const u = users.find(x => x.id === editingUserId);
        const lastAdmin = u.role === 'admin' && role !== 'admin' && users.filter(x => x.role === 'admin').length === 1;
        if (lastAdmin) return showMsg(err, 'You need at least one Admin account.');
        u.name = name; u.username = username; u.role = role; u.vendorIds = vendorIds;
        if (pw) { u.salt = randomSalt(); u.hash = await hashPassword(pw, u.salt); }
        if (currentUser && u.id === currentUser.id) {
          currentRole = u.role === 'admin' ? 'admin' : 'sales';
          allowedVendorIds = (currentRole === 'sales' && Array.isArray(u.vendorIds)) ? u.vendorIds : null;
          applyRolePermissions();
        }
      } else {
        const salt = randomSalt();
        users.push({ id: 'u_' + Date.now(), name, username, salt, hash: await hashPassword(pw, salt), role, vendorIds });
      }
      saveData();
      $('userModal').classList.add('hidden');
      renderTeamList();
    };

    window.removeUser = function(id) {
      if (currentRole !== 'admin') return;
      const u = users.find(x => x.id === id);
      if (!u || (currentUser && u.id === currentUser.id)) return;
      if (u.role === 'admin' && users.filter(x => x.role === 'admin').length === 1) return alert('You need at least one Admin account.');
      if (!confirm(`Remove ${u.name} (@${u.username})? They will no longer be able to sign in.`)) return;
      users = users.filter(x => x.id !== id);
      saveData();
      renderTeamList();
    };

    function getFollowUpBadge(dateStr) {
      if (!dateStr) return { text: 'No Schedule', class: 'bg-slate-100 text-slate-500' };
      if (dateStr < todayStr) return { text: `🚨 Overdue`, class: 'bg-red-100 text-red-800 font-bold border border-red-200' };
      if (dateStr === todayStr) return { text: '⚡ Due Today', class: 'bg-amber-100 text-amber-900 font-bold border border-amber-300' };
      return { text: 'Scheduled', class: 'bg-slate-100 text-slate-700' };
    }

    function generateScript(type, lead) {
      const name = lead.name || 'there';
      const vendor = lead.vendorName || 'our photography team';
      const weddingDate = lead.weddingDate || 'your wedding';
      const val = lead.value ? `₹${Number(lead.value).toLocaleString('en-IN')}` : 'as discussed';

      if (type === 'portfolio') {
        return `Hi ${name}! Hope you're doing great. Following up on your wedding inquiry with ${vendor} for ${weddingDate}. Here is a quick link to our exclusive cinematic portfolio: [Portfolio Link]. Let me know if you are free for a quick 5-min briefing call today!`;
      }
      if (type === 'urgency') {
        return `Hi ${name}, quick priority check regarding ${weddingDate}! We just received another inquiry for ${vendor} for this exact date. Since you reached out first, wanted to give you first preference before releasing the team slot. Can we lock it with a token advance?`;
      }
      if (type === 'budget') {
        return `Hi ${name}, understand wedding budgets require balance! If package cost for ${val} was the hesitation, we can customize crew & delivery inclusions to fit your preferred range. Are you open for a quick 5-minute chat?`;
      }
      return `Hi ${name}, following up regarding your wedding photography with ${vendor}!`;
    }

    function getWhatsAppUrl(phone, text) {
      const clean = (phone || '').replace(/\D/g, '');
      const valid = clean.length === 10 ? '91' + clean : clean;
      return `https://wa.me/${valid}?text=${encodeURIComponent(text)}`;
    }

    // Filter Vendors based on allowed permissions (for Sales Reps)
    function getVisibleVendors() {
      if (Array.isArray(allowedVendorIds)) {
        return vendors.filter(v => allowedVendorIds.includes(v.id));
      }
      return vendors;
    }

    // Fully Automated & Accurate Nav Renderer
    function renderNav() {
      const wsSelect = document.getElementById('vendorWorkspaceSelect');
      const vSelect = document.getElementById('vendorSelect');
      const visibleVendors = getVisibleVendors();
      
      // Update Lead Form's vendor dropdown
      if (vSelect) {
        vSelect.innerHTML = visibleVendors.map(v => `<option value="${v.id}" ${selectedVendorId === v.id ? 'selected' : ''}>${v.name} (${v.category})</option>`).join('');
      }
      
      // Workspace filter
      let optionsHtml = `<option value="ALL" ${selectedVendorId === 'ALL' ? 'selected' : ''}>All Vendors (${visibleVendors.length} Vendors • ${contacts.length} Leads)</option>`;
      visibleVendors.forEach(v => {
        const count = contacts.filter(c => c.vendorId === v.id).length;
        optionsHtml += `<option value="${v.id}" ${selectedVendorId === v.id ? 'selected' : ''}>${v.name} (${count} Leads)</option>`;
      });
      wsSelect.innerHTML = optionsHtml;
      
      // Sidebar counters
      document.getElementById('sidePipelineCount').textContent = contacts.length;
      document.getElementById('sideLostCount').textContent = lostContacts.length;
      document.getElementById('sideVendorCount').textContent = vendors.length;
    }

    function getSprintQueue() {
      const visibleVendors = getVisibleVendors().map(v => v.id);
      return contacts.filter(c => {
        if (!visibleVendors.includes(c.vendorId)) return false;
        if (selectedVendorId !== 'ALL' && c.vendorId !== selectedVendorId) return false;
        return c.stage !== 'Won' && c.followUpDate && c.followUpDate <= todayStr;
      });
    }

    function updateSprintBanner() {
      const queue = getSprintQueue();
      const badge = document.getElementById('sprintDueBadge');
      const sideBadge = document.getElementById('sideSprintDueCount');

      badge.textContent = `${queue.length} Due`;
      sideBadge.textContent = `${queue.length} Due`;

      if (queue.length === 0) {
        badge.className = "bg-white text-emerald-900 text-[10px] px-2 py-0.5 rounded-full font-extrabold shadow-2xs";
        badge.textContent = "All Done!";
        sideBadge.className = "text-[10px] px-1.5 py-0.5 rounded-full bg-emerald-500/20 text-emerald-300 font-bold border border-emerald-500/30";
        sideBadge.textContent = "0";
      } else {
        badge.className = "bg-white text-amber-900 text-[10px] px-2 py-0.5 rounded-full font-extrabold shadow-2xs";
        sideBadge.className = "text-[10px] px-1.5 py-0.5 rounded-full bg-amber-500/20 text-amber-300 font-bold border border-amber-500/30";
      }
    }

    function renderPipeline() {
      renderNav();
      updateSprintBanner();

      const q = (document.getElementById('searchInput').value || '').toLowerCase();
      const stageFilter = document.getElementById('stageFilterSelect').value;
      const visibleVendors = getVisibleVendors().map(v => v.id);

      // Update Workspace Heading & Subtext dynamically
      const headingEl = document.getElementById('currentWorkspaceHeading');
      const subEl = document.getElementById('statWorkspaceSub');
      if (selectedVendorId === 'ALL') {
        headingEl.textContent = `All Vendor Inquiries (${getVisibleVendors().length} Vendors)`;
        subEl.textContent = `Across all partner vendors`;
      } else {
        const currV = vendors.find(v => v.id === selectedVendorId);
        headingEl.textContent = currV ? `${currV.name} Pipeline` : 'Vendor Pipeline';
        subEl.textContent = currV ? `Dedicated to ${currV.name}` : 'Vendor specific';
      }

      // Scoped leads based on active vendor workspace and allowed access
      let scopedContacts = contacts.filter(c => {
        if (!visibleVendors.includes(c.vendorId)) return false;
        if (selectedVendorId !== 'ALL' && c.vendorId !== selectedVendorId) return false;
        return true;
      });

      let filtered = scopedContacts.filter(c => {
        if (stageFilter === 'DUE_TODAY' && c.followUpDate !== todayStr) return false;
        if (stageFilter === 'OVERDUE' && (!c.followUpDate || c.followUpDate >= todayStr || c.stage === 'Won')) return false;
        if (stageFilter !== 'ALL' && stageFilter !== 'DUE_TODAY' && stageFilter !== 'OVERDUE' && c.stage !== stageFilter) return false;
        if (q && !(c.name.toLowerCase().includes(q) || (c.phone||'').includes(q) || (c.venue||'').toLowerCase().includes(q))) return false;
        return true;
      });

      // Front desk metrics
      document.getElementById('statTotalContacts').textContent = scopedContacts.length;
      const won = scopedContacts.filter(c => c.stage === 'Won');
      document.getElementById('statWonCount').textContent = won.length;
      const totalVal = won.reduce((s, c) => s + (Number(c.value)||0), 0);
      const activeDeals = scopedContacts.filter(c => c.stage !== 'Won');
      const activeVal = activeDeals.reduce((s, c) => s + (Number(c.value)||0), 0);
      const followUps = scopedContacts.filter(c => c.stage !== 'Won' && c.followUpDate && c.followUpDate <= todayStr);

      document.getElementById('statPipelineValue').textContent = '₹' + activeVal.toLocaleString('en-IN');
      document.getElementById('statWonSub').textContent = '₹' + totalVal.toLocaleString('en-IN') + ' closed volume';
      document.getElementById('statFollowUpsDue').textContent = followUps.length;

      // ==========================================
      // RENDER TABLE VIEW (WITH INLINE FOLLOW-UP DATE)
      // ==========================================
      const tbody = document.getElementById('contactsTableBody');
      if (filtered.length === 0) {
        tbody.innerHTML = `<tr><td colspan="6" class="py-8 text-center text-slate-400">No leads found in this view.</td></tr>`;
      } else {
        tbody.innerHTML = filtered.map(c => {
          const badge = getFollowUpBadge(c.followUpDate);
          const defaultScript = generateScript('portfolio', c);
          const waUrl = getWhatsAppUrl(c.phone, defaultScript);

          return `
            <tr class="hover:bg-slate-50 transition">
              <td class="py-3 px-4">
                <div class="font-bold text-slate-900">${c.name}</div>
                <div class="text-[11px] text-slate-500">${c.vendorName}</div>
              </td>
              <td class="py-3 px-4">
                <div class="font-medium text-slate-800">${c.weddingDate || 'Date TBD'}</div>
                <div class="text-[11px] text-slate-500">${c.venue || 'Delhi NCR'}</div>
              </td>
              <td class="py-3 px-4">
                <div class="flex items-center gap-1.5">
                  <span class="px-2 py-0.5 rounded text-[10px] ${badge.class}">${badge.text}</span>
                  <input type="date" value="${c.followUpDate || ''}" onchange="updateLeadFollowUp(${c.id}, this.value)" class="px-1.5 py-0.5 text-[11px] border border-slate-300 rounded bg-white focus:outline-none focus:ring-1 focus:ring-slate-900 cursor-pointer">
                  <button onclick="quickSnooze(${c.id}, 1)" class="px-1.5 py-0.5 rounded bg-slate-100 hover:bg-slate-200 text-slate-700 font-bold text-[10px]" title="Snooze 1 Day">+1d</button>
                  <button onclick="quickSnooze(${c.id}, 3)" class="px-1.5 py-0.5 rounded bg-slate-100 hover:bg-slate-200 text-slate-700 font-bold text-[10px]" title="Snooze 3 Days">+3d</button>
                </div>
              </td>
              <td class="py-3 px-4">
                <select onchange="updateLeadStage(${c.id}, this.value)" class="px-2 py-1 rounded border text-[11px] font-semibold bg-white cursor-pointer ${c.stage==='Won'?'text-emerald-700 border-emerald-300':'text-slate-700 border-slate-300'}">
                  ${stagesList.map(s => `<option value="${s}" ${c.stage===s?'selected':''}>${s}</option>`).join('')}
                </select>
              </td>
              <td class="py-3 px-4 text-right font-bold text-slate-900">₹${Number(c.value||0).toLocaleString('en-IN')}</td>
              <td class="py-3 px-4 text-right space-x-2">
                ${c.phone ? `
                  <a href="${waUrl}" target="_blank" onclick="onWhatsAppClick(${c.id})" class="inline-flex items-center px-2 py-1 rounded bg-emerald-600 hover:bg-emerald-700 text-white font-bold text-[11px] shadow-2xs transition">
                    WhatsApp →
                  </a>` : ''}
                <button onclick="markLost(${c.id})" class="text-slate-400 hover:text-slate-700">Lost</button>
                ${currentRole === 'admin' ? 
                  `<button onclick="requestSecureDelete('lead', ${c.id}, '${c.name.replace(/'/g, "\'")}')" class="text-red-500 hover:underline">Delete</button>` : 
                  `<span class="text-slate-300" title="Delete restricted to Admin">Locked</span>`
                }
              </td>
            </tr>
          `;
        }).join('');
      }

      // ==========================================
      // RENDER KANBAN VIEW (WITH DRAG & DROP + INLINE DATE)
      // ==========================================
      stagesList.forEach(stage => {
        const colEl = document.getElementById(`kCol-${stage}`);
        if (!colEl) return;
        const stageLeads = filtered.filter(c => c.stage === stage);
        colEl.querySelector('.k-count').textContent = stageLeads.length;
        const cardsEl = colEl.querySelector('.k-cards');
        
        if (stageLeads.length === 0) {
          cardsEl.innerHTML = `<div class="h-28 border border-dashed border-slate-200 rounded-lg flex items-center justify-center text-[10px] text-slate-400 pointer-events-none">Drop Leads Here</div>`;
        } else {
          cardsEl.innerHTML = stageLeads.map(c => {
            const badge = getFollowUpBadge(c.followUpDate);
            const waUrl = getWhatsAppUrl(c.phone, generateScript('portfolio', c));
            return `
              <div class="kanban-card bg-white p-3 rounded-xl border border-slate-200 shadow-2xs hover:shadow-sm space-y-2.5" draggable="true" data-id="${c.id}">
                <div class="flex items-start justify-between">
                  <div>
                    <h4 class="font-bold text-slate-900 text-xs">${c.name}</h4>
                    <p class="text-[10px] text-slate-500 font-medium">${c.vendorName}</p>
                  </div>
                  <span class="font-bold text-xs text-slate-900">₹${Number(c.value||0).toLocaleString('en-IN')}</span>
                </div>
                
                <div class="text-[11px] text-slate-600 flex items-center justify-between">
                  <span>${c.weddingDate || 'Date TBD'}</span>
                  <span class="text-slate-400 text-[10px] truncate max-w-[90px]">${c.venue || 'Delhi NCR'}</span>
                </div>

                <div class="pt-1.5 border-t border-slate-100 flex items-center justify-between gap-1">
                  <div class="flex items-center gap-1">
                    <span class="px-1.5 py-0.2 rounded text-[9px] ${badge.class}">${badge.text}</span>
                    <input type="date" value="${c.followUpDate || ''}" onchange="updateLeadFollowUp(${c.id}, this.value)" class="text-[10px] border border-slate-200 rounded px-1 py-0.2 bg-slate-50 focus:bg-white cursor-pointer">
                  </div>
                  <button onclick="quickSnooze(${c.id}, 1)" class="px-1.5 py-0.2 rounded bg-slate-100 hover:bg-slate-200 text-[9px] font-bold text-slate-700">+1d</button>
                </div>

                <div class="pt-1 flex items-center justify-between">
                  <span class="text-[9px] text-slate-400 font-mono">⋮⋮ Drag</span>
                  <div class="flex items-center gap-1.5">
                    ${c.phone ? `<a href="${waUrl}" target="_blank" onclick="onWhatsAppClick(${c.id})" class="text-[10px] text-emerald-700 font-bold hover:underline">WhatsApp →</a>` : ''}
                    <button onclick="markLost(${c.id})" class="text-[10px] text-slate-400 hover:text-slate-700">Lost</button>
                  </div>
                </div>
              </div>
            `;
          }).join('');
        }
      });

      setupKanbanDragAndDrop();
    }

    // Native HTML5 Drag & Drop
    let draggedLeadId = null;

    function setupKanbanDragAndDrop() {
      const cards = document.querySelectorAll('.kanban-card');
      const cols = document.querySelectorAll('.kanban-col');

      cards.forEach(card => {
        card.addEventListener('dragstart', (e) => {
          draggedLeadId = Number(card.getAttribute('data-id'));
          card.classList.add('dragging');
          e.dataTransfer.effectAllowed = 'move';
          e.dataTransfer.setData('text/plain', draggedLeadId);
        });

        card.addEventListener('dragend', () => {
          card.classList.remove('dragging');
          draggedLeadId = null;
          cols.forEach(c => c.classList.remove('drag-over'));
        });
      });

      cols.forEach(col => {
        col.addEventListener('dragover', (e) => {
          e.preventDefault();
          e.dataTransfer.dropEffect = 'move';
          col.classList.add('drag-over');
        });

        col.addEventListener('dragleave', (e) => {
          if (!col.contains(e.relatedTarget)) {
            col.classList.remove('drag-over');
          }
        });

        col.addEventListener('drop', (e) => {
          e.preventDefault();
          col.classList.remove('drag-over');
          const targetStage = col.getAttribute('data-stage');
          const leadId = draggedLeadId || Number(e.dataTransfer.getData('text/plain'));

          if (leadId && targetStage) {
            const lead = contacts.find(c => c.id === leadId);
            if (lead && lead.stage !== targetStage) {
              lead.stage = targetStage;
              saveData();
              renderPipeline();
            }
          }
          draggedLeadId = null;
        });
      });
    }

    window.updateLeadFollowUp = function(id, newDate) {
      const c = contacts.find(x => x.id === id);
      if (c) {
        c.followUpDate = newDate;
        saveData();
        renderPipeline();
      }
    };

    window.quickSnooze = function(id, days) {
      const c = contacts.find(x => x.id === id);
      if (c) {
        const d = new Date();
        d.setDate(d.getDate() + days);
        c.followUpDate = d.toISOString().split('T')[0];
        saveData();
        renderPipeline();
      }
    };

    window.updateLeadStage = function(id, newStage) {
      const c = contacts.find(x => x.id === id);
      if (c) {
        c.stage = newStage;
        saveData();
        renderPipeline();
      }
    };

    window.onWhatsAppClick = function(id) {
      const c = contacts.find(x => x.id === id);
      if (c && c.stage === 'Lead In') {
        c.stage = 'Contact Made';
        const d = new Date();
        d.setDate(d.getDate() + 2);
        c.followUpDate = d.toISOString().split('T')[0];
        saveData();
        renderPipeline();
      }
    };

    // ==========================================
    // ADD LEAD MODAL CONTROLS (Item 2: Toggleable)
    // ==========================================
    const addLeadModal = document.getElementById('addLeadModal');

    function openAddLeadModal() {
      addLeadModal.classList.remove('hidden');
      document.getElementById('contactName').focus();
    }

    function closeAddLeadModal() {
      addLeadModal.classList.add('hidden');
      document.getElementById('contactForm').reset();
    }

    document.getElementById('headerAddLeadBtn')?.addEventListener('click', openAddLeadModal);
    document.getElementById('toolbarAddLeadBtn')?.addEventListener('click', openAddLeadModal);
    document.getElementById('closeAddLeadModalBtn')?.addEventListener('click', closeAddLeadModal);
    document.getElementById('cancelAddLeadBtn')?.addEventListener('click', closeAddLeadModal);

    // ==========================================
    // SMART LOST DEAL CAPTURE (REASON + REMARKS)
    // ==========================================
    let pendingLostLeadId = null;

    window.markLost = function(id) {
      const c = contacts.find(x => x.id === id);
      if (!c) return;

      pendingLostLeadId = id;
      document.getElementById('lostModalClientSubtitle').textContent = `Client: ${c.name} (${c.vendorName}) • Quoted: ₹${Number(c.value||0).toLocaleString('en-IN')}`;
      document.getElementById('lostReasonSelect').value = 'Budget';
      document.getElementById('lostBudgetBox').classList.remove('hidden');
      document.getElementById('lostClientBudgetInput').value = '';
      document.getElementById('lostRemarksInput').value = '';
      document.getElementById('markLostModal').classList.remove('hidden');
      document.getElementById('lostRemarksInput').focus();
    };

    document.getElementById('lostReasonSelect').addEventListener('change', function(e) {
      const budgetBox = document.getElementById('lostBudgetBox');
      if (e.target.value === 'Budget') {
        budgetBox.classList.remove('hidden');
        document.getElementById('lostClientBudgetInput').focus();
      } else {
        budgetBox.classList.add('hidden');
      }
    });

    document.getElementById('closeMarkLostBtn').onclick = () => {
      document.getElementById('markLostModal').classList.add('hidden');
      pendingLostLeadId = null;
    };
    document.getElementById('cancelMarkLostBtn').onclick = () => {
      document.getElementById('markLostModal').classList.add('hidden');
      pendingLostLeadId = null;
    };

    document.getElementById('markLostForm').onsubmit = function(e) {
      e.preventDefault();
      if (!pendingLostLeadId) return;

      const c = contacts.find(x => x.id === pendingLostLeadId);
      if (!c) return;

      const reason = document.getElementById('lostReasonSelect').value;
      const clientBudget = reason === 'Budget' ? (parseFloat(document.getElementById('lostClientBudgetInput').value) || null) : null;
      const remarks = document.getElementById('lostRemarksInput').value.trim();

      // Create rich archived lead
      const archivedLead = {
        ...c,
        lostReason: reason,
        lostClientBudget: clientBudget,
        lostRemarks: remarks,
        lostDate: todayStr
      };

      lostContacts.unshift(archivedLead);
      contacts = contacts.filter(x => x.id !== pendingLostLeadId);

      saveData();
      document.getElementById('markLostModal').classList.add('hidden');
      pendingLostLeadId = null;

      renderNav();
      renderPipeline();
      alert(`📁 Deal for "${c.name}" archived under Lost (${reason}).`);
    };

    window.restoreLead = function(id) {
      const c = lostContacts.find(x => x.id === id);
      if (c) {
        c.stage = 'Lead In';
        contacts.unshift(c);
        lostContacts = lostContacts.filter(x => x.id !== id);
        saveData();
        renderNav();
        renderLost();
      }
    };

    // ========================================================
    // SECURE 2-STEP DOUBLE CONFIRMATION + PIN DELETION ENGINE
    // ========================================================
    let pendingDeletion = null; // { type: 'lead'|'vendor', id: ..., label: ... }

    window.requestSecureDelete = function(type, id, label) {
      if (currentRole !== 'admin') {
        alert('⛔ Access Denied! Only Admin can perform deletions.');
        return;
      }
      
      const firstCheck = confirm(`[1st Confirmation]: Are you sure you want to delete ${type.toUpperCase()} "${label}"?\n\nClick OK to proceed to Master PIN verification.`);
      if (!firstCheck) return;

      pendingDeletion = { type, id, label };
      document.getElementById('deleteModalTitle').textContent = `Authorize ${type === 'lead' ? 'Lead' : 'Vendor'} Deletion`;
      document.getElementById('deleteModalSubtitle').textContent = `Permanently removing "${label}". Enter Master PIN to confirm.`;
      document.getElementById('deletePinInput').value = '';
      document.getElementById('secureDeleteModal').classList.remove('hidden');
      document.getElementById('deletePinInput').focus();
    };

    document.getElementById('cancelSecureDeleteBtn').onclick = () => {
      document.getElementById('secureDeleteModal').classList.add('hidden');
      pendingDeletion = null;
    };

    document.getElementById('confirmSecureDeleteBtn').onclick = () => {
      if (!pendingDeletion) return;
      const enteredPin = document.getElementById('deletePinInput').value.trim();
      
      if (enteredPin !== masterAdminPin) {
        alert('❌ Incorrect PIN! Deletion aborted for security.');
        return;
      }

      if (pendingDeletion.type === 'lead') {
        contacts = contacts.filter(c => c.id !== pendingDeletion.id);
        alert(`✅ Lead "${pendingDeletion.label}" deleted.`);
      } else if (pendingDeletion.type === 'vendor') {
        if (vendors.length <= 1) {
          alert('You must have at least one vendor partner in the CRM.');
          document.getElementById('secureDeleteModal').classList.add('hidden');
          return;
        }
        vendors = vendors.filter(v => v.id !== pendingDeletion.id);
        if (selectedVendorId === pendingDeletion.id) selectedVendorId = 'ALL';
        alert(`✅ Vendor "${pendingDeletion.label}" deleted.`);
      }

      saveData();
      document.getElementById('secureDeleteModal').classList.add('hidden');
      pendingDeletion = null;
      renderNav();
      renderManageVendorsList();
      renderPipeline();
    };

    document.getElementById('deletePinInput').addEventListener('keyup', (e) => {
      if (e.key === 'Enter') document.getElementById('confirmSecureDeleteBtn').click();
    });

    // Vendor Management Modal
    const manageVendorsModal = document.getElementById('manageVendorsModal');
    
    function renderManageVendorsList() {
      const container = document.getElementById('vendorsListContainer');
      container.innerHTML = vendors.map(v => {
        const leadCount = contacts.filter(c => c.vendorId === v.id).length;
        return `
          <div class="flex items-center justify-between p-3 rounded-xl border border-slate-200 bg-slate-50">
            <div>
              <div class="font-bold text-slate-900 text-xs">${v.name}</div>
              <div class="text-[10px] text-slate-500">${v.category} • ${leadCount} active leads</div>
            </div>
            ${currentRole === 'admin' ? 
              `<button onclick="requestSecureDelete('vendor', '${v.id}', '${v.name.replace(/'/g, "\\'")}')" class="px-2.5 py-1 text-xs font-semibold rounded-lg bg-white border border-red-200 text-red-600 hover:bg-red-50 transition shadow-2xs">Delete</button>` :
              `<span class="text-xs text-slate-400">Admin Only</span>`
            }
          </div>
        `;
      }).join('');
    }

    document.getElementById('sideNavVendorsBtn').onclick = () => {
      renderManageVendorsList();
      manageVendorsModal.classList.remove('hidden');
    };
    document.getElementById('closeManageVendorsBtn').onclick = () => manageVendorsModal.classList.add('hidden');

    // Add Vendor Modal Events
    const addVendorModal = document.getElementById('addVendorModal');
    document.getElementById('headerAddVendorBtn').onclick = () => {
      if (currentRole !== 'admin') {
        alert('⛔ Access Denied! Only Admin accounts can add new vendors.');
        return;
      }
      addVendorModal.classList.remove('hidden');
      document.getElementById('newVendorName').focus();
    };
    document.getElementById('cancelVendorModalBtn').onclick = () => addVendorModal.classList.add('hidden');
    
    document.getElementById('saveVendorBtn').onclick = () => {
      const name = document.getElementById('newVendorName').value.trim();
      if (!name) return alert('Please enter a vendor business name.');
      const cat = document.getElementById('newVendorCategory').value;
      const newV = { id: 'v_' + Date.now(), name, category: cat };
      
      vendors.push(newV);
      selectedVendorId = newV.id;
      
      saveData();
      addVendorModal.classList.add('hidden');
      document.getElementById('newVendorName').value = '';
      
      renderNav();
      renderPipeline();
      renderPermissionsSettings();
    };

    // Toggle Table vs Kanban
    document.getElementById('toggleTableBtn').onclick = () => {
      currentViewMode = 'table';
      document.getElementById('tableViewContainer').classList.remove('hidden');
      document.getElementById('kanbanViewContainer').classList.add('hidden');
      document.getElementById('toggleTableBtn').className = "px-2.5 py-1 rounded-md font-semibold bg-white text-slate-900 shadow-xs";
      document.getElementById('toggleKanbanBtn').className = "px-2.5 py-1 rounded-md font-medium text-slate-600 hover:text-slate-900";
    };

    document.getElementById('toggleKanbanBtn').onclick = () => {
      currentViewMode = 'kanban';
      document.getElementById('tableViewContainer').classList.add('hidden');
      document.getElementById('kanbanViewContainer').classList.remove('hidden');
      document.getElementById('toggleKanbanBtn').className = "px-2.5 py-1 rounded-md font-semibold bg-white text-slate-900 shadow-xs";
      document.getElementById('toggleTableBtn').className = "px-2.5 py-1 rounded-md font-medium text-slate-600 hover:text-slate-900";
      renderPipeline();
    };

    // High-speed outreach sprint engine
    let currentSprintQueue = [];
    let sprintIndex = 0;

    function launchSprint() {
      currentSprintQueue = getSprintQueue();
      if (currentSprintQueue.length === 0) {
        alert("🎉 Awesome! No follow-ups due today in this workspace.");
        return;
      }
      sprintIndex = 0;
      showSprintCard();
      document.getElementById('sprintModal').classList.remove('hidden');
    }

    function showSprintCard() {
      if (sprintIndex >= currentSprintQueue.length) {
        document.getElementById('sprintLeadCard').innerHTML = `
          <div class="text-center py-8 space-y-3">
            <div class="text-4xl">🎉</div>
            <h3 class="text-base font-bold text-slate-900">Sprint Completed!</h3>
            <p class="text-xs text-slate-500">You reached out to all actionable follow-ups for today.</p>
            <button onclick="closeSprint()" class="px-4 py-2 text-xs font-bold bg-slate-900 text-white rounded-lg">Done</button>
          </div>
        `;
        document.getElementById('sprintLeadIndex').textContent = 'Completed';
        return;
      }

      const lead = currentSprintQueue[sprintIndex];
      document.getElementById('sprintLeadIndex').textContent = `Lead ${sprintIndex + 1} of ${currentSprintQueue.length}`;

      const portfolioUrl = getWhatsAppUrl(lead.phone, generateScript('portfolio', lead));
      const urgencyUrl = getWhatsAppUrl(lead.phone, generateScript('urgency', lead));
      const budgetUrl = getWhatsAppUrl(lead.phone, generateScript('budget', lead));

      document.getElementById('sprintLeadCard').innerHTML = `
        <div class="p-4 bg-slate-50 border border-slate-200 rounded-xl space-y-2">
          <div class="flex items-start justify-between">
            <div>
              <h2 class="text-base font-bold text-slate-900">${lead.name}</h2>
              <p class="text-xs text-slate-600 font-medium">${lead.vendorName} • Wedding: <strong>${lead.weddingDate || 'TBD'}</strong></p>
            </div>
            <div class="text-right">
              <span class="text-sm font-bold text-slate-900">₹${Number(lead.value||0).toLocaleString('en-IN')}</span>
              <div class="text-[10px] text-slate-400">Current: ${lead.stage}</div>
            </div>
          </div>
          <p class="text-xs text-slate-500 italic bg-white p-2.5 rounded-lg border border-slate-200">${lead.notes || 'No previous notes logged.'}</p>
        </div>

        <div>
          <label class="block text-xs font-bold uppercase tracking-wider text-slate-700 mb-2">1-Click WhatsApp Script Options:</label>
          <div class="grid grid-cols-1 gap-2">
            <a href="${portfolioUrl}" target="_blank" onclick="onSprintAction(${lead.id}, 'portfolio')" class="p-3 bg-white hover:bg-emerald-50 border border-slate-200 hover:border-emerald-300 rounded-xl flex items-center justify-between text-xs font-semibold text-slate-900 transition shadow-2xs">
              <div>
                <strong class="text-emerald-700 font-bold">1. Portfolio & Intro Check</strong>
                <p class="text-[11px] text-slate-500 mt-0.5">Share shoot samples & schedule a quick consultation call.</p>
              </div>
              <span class="text-emerald-700 font-bold ml-2">Send WA →</span>
            </a>
            <a href="${urgencyUrl}" target="_blank" onclick="onSprintAction(${lead.id}, 'urgency')" class="p-3 bg-white hover:bg-amber-50 border border-slate-200 hover:border-amber-300 rounded-xl flex items-center justify-between text-xs font-semibold text-slate-900 transition shadow-2xs">
              <div>
                <strong class="text-amber-800 font-bold">2. Date-Hold & Competitor Lockout</strong>
                <p class="text-[11px] text-slate-500 mt-0.5">Create urgent date priority to close token advance.</p>
              </div>
              <span class="text-amber-800 font-bold ml-2">Send WA →</span>
            </a>
            <a href="${budgetUrl}" target="_blank" onclick="onSprintAction(${lead.id}, 'budget')" class="p-3 bg-white hover:bg-indigo-50 border border-slate-200 hover:border-indigo-300 rounded-xl flex items-center justify-between text-xs font-semibold text-slate-900 transition shadow-2xs">
              <div>
                <strong class="text-indigo-700 font-bold">3. Budget Adjustment & Custom Quote</strong>
                <p class="text-[11px] text-slate-500 mt-0.5">Optimize crew size to match client's targeted investment.</p>
              </div>
              <span class="text-indigo-700 font-bold ml-2">Send WA →</span>
            </a>
          </div>
        </div>

        <div class="pt-3 border-t border-slate-100 flex items-center justify-between">
          <div class="flex items-center gap-1.5">
            <span class="text-xs text-slate-500">Snooze:</span>
            <button onclick="sprintSnooze(${lead.id}, 1)" class="px-2.5 py-1 text-xs font-bold rounded-lg bg-slate-100 hover:bg-slate-200 text-slate-700">+1d</button>
            <button onclick="sprintSnooze(${lead.id}, 3)" class="px-2.5 py-1 text-xs font-bold rounded-lg bg-slate-100 hover:bg-slate-200 text-slate-700">+3d</button>
            <button onclick="sprintSnooze(${lead.id}, 7)" class="px-2.5 py-1 text-xs font-bold rounded-lg bg-slate-100 hover:bg-slate-200 text-slate-700">+1w</button>
          </div>
          <div class="flex items-center gap-2">
            <button onclick="sprintNext()" class="px-3 py-1.5 text-xs font-bold text-slate-600 hover:bg-slate-100 rounded-lg">Skip →</button>
            <button onclick="sprintAdvanceStage(${lead.id}, 'Meeting Scheduled')" class="px-3.5 py-1.5 text-xs font-bold bg-slate-900 hover:bg-slate-800 text-white rounded-lg shadow-sm">Meeting Fixed ✓</button>
          </div>
        </div>
      `;
    }

    window.sprintNext = function() {
      sprintIndex++;
      showSprintCard();
    };

    window.sprintSnooze = function(id, days) {
      quickSnooze(id, days);
      sprintNext();
    };

    window.sprintAdvanceStage = function(id, newStage) {
      updateLeadStage(id, newStage);
      sprintNext();
    };

    window.onSprintAction = function(id, type) {
      const c = contacts.find(x => x.id === id);
      if (c) {
        if (c.stage === 'Lead In') c.stage = 'Contact Made';
        const d = new Date();
        d.setDate(d.getDate() + 2);
        c.followUpDate = d.toISOString().split('T')[0];
        saveData();
        renderPipeline();
      }
      setTimeout(sprintNext, 600);
    };

    window.closeSprint = function() {
      document.getElementById('sprintModal').classList.add('hidden');
      renderPipeline();
    };

    document.getElementById('startSprintBtn').onclick = launchSprint;
    document.getElementById('sideNavSprintBtn').onclick = launchSprint;
    document.getElementById('closeSprintModalBtn').onclick = () => document.getElementById('sprintModal').classList.add('hidden');

    // Filter listeners
    document.getElementById('stageFilterSelect').onchange = renderPipeline;
    document.getElementById('searchInput').oninput = renderPipeline;
    document.getElementById('vendorWorkspaceSelect').onchange = (e) => {
      selectedVendorId = e.target.value;
      renderPipeline();
    };

    // Contact Form Submit (Inside New Lead Modal)
    document.getElementById('contactForm').onsubmit = (e) => {
      e.preventDefault();
      const vId = document.getElementById('vendorSelect').value;
      const vObj = vendors.find(v => v.id === vId) || vendors[0];
      const newDeal = {
        id: Date.now(),
        name: document.getElementById('contactName').value.trim() || ('Inquiry #' + String(Date.now()).slice(-4)),
        vendorId: vObj.id,
        vendorName: vObj.name,
        phone: document.getElementById('contactPhone').value.trim(),
        instaId: document.getElementById('instaId').value.trim(),
        weddingDate: document.getElementById('weddingDate').value,
        venue: document.getElementById('venueLocation').value.trim() || 'Delhi NCR',
        stage: document.getElementById('dealStage').value,
        followUpDate: document.getElementById('followUpDate').value || todayStr,
        value: parseFloat(document.getElementById('dealValue').value) || 0,
        advance: parseFloat(document.getElementById('advancePaid').value) || 0,
        notes: document.getElementById('contactNotes').value.trim()
      };
      contacts.unshift(newDeal);
      saveData();
      closeAddLeadModal();
      renderPipeline();
      alert(`🎉 New lead "${newDeal.name}" added successfully!`);
    };

    // Sidebar Navigation Page Switcher
    function switchPage(page) {
      currentPage = page;
      const pipelineSec = document.getElementById('pipelinePageSection');
      const financeSec = document.getElementById('financePageSection');
      const lostSec = document.getElementById('lostPageSection');
      const permissionsSec = document.getElementById('permissionsPageSection');

      const sidePipeline = document.getElementById('sideNavPipelineBtn');
      const sideFinance = document.getElementById('sideNavFinanceBtn');
      const sideLost = document.getElementById('sideNavLostBtn');
      const sidePermissions = document.getElementById('sideNavPermissionsBtn');

      [sidePipeline, sideFinance, sideLost, sidePermissions].forEach(el => el.classList.remove('active'));

      pipelineSec.classList.add('hidden');
      financeSec.classList.add('hidden');
      lostSec.classList.add('hidden');
      permissionsSec.classList.add('hidden');

      if (page === 'pipeline') {
        pipelineSec.classList.remove('hidden');
        sidePipeline.classList.add('active');
        renderPipeline();
      } else if (page === 'finances') {
        financeSec.classList.remove('hidden');
        sideFinance.classList.add('active');
        renderFinances();
      } else if (page === 'lost') {
        lostSec.classList.remove('hidden');
        sideLost.classList.add('active');
        renderLost();
      } else if (page === 'permissions') {
        permissionsSec.classList.remove('hidden');
        sidePermissions.classList.add('active');
        renderPermissionsSettings();
      }
    }

    document.getElementById('sideNavPipelineBtn').onclick = () => switchPage('pipeline');
    document.getElementById('sideNavFinanceBtn').onclick = () => {
      if (currentRole === 'sales') {
        alert('⛔ Access Denied! Finances and commission split are restricted to Admin.');
        return;
      }
      switchPage('finances');
    };
    document.getElementById('sideNavLostBtn').onclick = () => switchPage('lost');
    document.getElementById('filterLostReasonSelect')?.addEventListener('change', renderLost);
    document.getElementById('sideNavPermissionsBtn').onclick = () => {
      if (currentRole !== 'admin') {
        alert('⛔ Access Denied! Access settings are restricted to Admin.');
        return;
      }
      switchPage('permissions');
    };

    // ==========================================
    // PERMISSIONS & REPS LINK GENERATOR
    // ==========================================
    function renderPermissionsSettings() {
      renderTeamList();
      document.getElementById('changeMasterPinInput').value = masterAdminPin;
    }

    document.getElementById('saveNewPinBtn').onclick = () => {
      const newPin = document.getElementById('changeMasterPinInput').value.trim();
      if (newPin.length < 4) {
        alert('PIN must be at least 4 digits.');
        return;
      }
      masterAdminPin = newPin;
      saveData();
      alert(`✅ Master Admin PIN successfully updated!`);
    };

    function renderFinances() {
      const won = contacts.filter(c => c.stage === 'Won');
      const gross = won.reduce((s, c) => s + (Number(c.value)||0), 0);
      const comm = gross * 0.10;
      const adv = won.reduce((s, c) => s + (Number(c.advance)||0), 0);
      const pending = Math.max(0, gross - adv);

      document.getElementById('finGrossVolume').textContent = '₹' + gross.toLocaleString('en-IN');
      document.getElementById('finAgencyComm').textContent = '₹' + comm.toLocaleString('en-IN');
      document.getElementById('finAdvanceCollected').textContent = '₹' + adv.toLocaleString('en-IN');
      document.getElementById('finPendingDue').textContent = '₹' + pending.toLocaleString('en-IN');

      const vendorTbody = document.getElementById('vendorFinancesTableBody');
      vendorTbody.innerHTML = vendors.map(v => {
        const vWon = contacts.filter(c => c.vendorId === v.id && c.stage === 'Won');
        const vGross = vWon.reduce((s, c) => s + (Number(c.value)||0), 0);
        const vComm = vGross * 0.10;
        const vNet = vGross - vComm;
        const vAdv = vWon.reduce((s, c) => s + (Number(c.advance)||0), 0);
        const vPending = Math.max(0, vGross - vAdv);
        return `
          <tr class="hover:bg-slate-50">
            <td class="py-3 px-4 font-bold text-slate-900">${v.name}</td>
            <td class="py-3 px-4">${vWon.length} Won</td>
            <td class="py-3 px-4 text-right font-bold">₹${vGross.toLocaleString('en-IN')}</td>
            <td class="py-3 px-4 text-right font-bold text-indigo-600">₹${vComm.toLocaleString('en-IN')}</td>
            <td class="py-3 px-4 text-right font-semibold text-slate-700">₹${vNet.toLocaleString('en-IN')}</td>
            <td class="py-3 px-4 text-right text-emerald-600">₹${vAdv.toLocaleString('en-IN')}</td>
            <td class="py-3 px-4 text-right font-bold text-amber-600">₹${vPending.toLocaleString('en-IN')}</td>
          </tr>
        `;
      }).join('');

      const ledgerTbody = document.getElementById('finLedgerTableBody');
      if (won.length === 0) {
        ledgerTbody.innerHTML = `<tr><td colspan="7" class="py-6 text-center text-slate-400">No won bookings yet.</td></tr>`;
        return;
      }
      ledgerTbody.innerHTML = won.map(c => {
        const val = Number(c.value || 0);
        const a = Number(c.advance || 0);
        const p = Math.max(0, val - a);
        const cComm = val * 0.10;
        return `
          <tr class="hover:bg-slate-50">
            <td class="py-3 px-4 font-semibold">${c.name} (${c.vendorName})</td>
            <td class="py-3 px-4">${c.weddingDate || 'TBD'}</td>
            <td class="py-3 px-4 text-right font-bold">₹${val.toLocaleString('en-IN')}</td>
            <td class="py-3 px-4 text-right font-bold text-indigo-600">₹${cComm.toLocaleString('en-IN')}</td>
            <td class="py-3 px-4 text-right text-emerald-600">₹${a.toLocaleString('en-IN')}</td>
            <td class="py-3 px-4 text-right font-bold text-amber-600">₹${p.toLocaleString('en-IN')}</td>
            <td class="py-3 px-4 text-center">
              <span class="px-2 py-0.5 rounded text-[10px] font-bold ${a >= val ? 'bg-emerald-100 text-emerald-800' : 'bg-amber-100 text-amber-800'}">
                ${a >= val ? 'Settled' : 'Partial'}
              </span>
            </td>
          </tr>
        `;
      }).join('');
    }

    function renderLost() {
      const tbody = document.getElementById('lostTableBody');
      const filterReason = document.getElementById('filterLostReasonSelect') ? document.getElementById('filterLostReasonSelect').value : 'ALL';
      const visibleVendors = getVisibleVendors().map(v => v.id);

      let scopedLost = lostContacts.filter(c => {
        if (!visibleVendors.includes(c.vendorId)) return false;
        if (selectedVendorId !== 'ALL' && c.vendorId !== selectedVendorId) return false;
        return true;
      });

      // Compute statistics for Lost Leads
      const totalLostCount = scopedLost.length;
      const totalLostValue = scopedLost.reduce((sum, c) => sum + (Number(c.value) || 0), 0);

      // Find top lost reason
      const reasonCounts = {};
      scopedLost.forEach(c => {
        const r = c.lostReason || 'Other';
        reasonCounts[r] = (reasonCounts[r] || 0) + 1;
      });
      let topReason = '-';
      let topCount = 0;
      for (const [r, cnt] of Object.entries(reasonCounts)) {
        if (cnt > topCount) {
          topCount = cnt;
          topReason = `${r} (${cnt})`;
        }
      }

      if (document.getElementById('statTotalLostCount')) document.getElementById('statTotalLostCount').textContent = totalLostCount;
      if (document.getElementById('statTotalLostValue')) document.getElementById('statTotalLostValue').textContent = '₹' + totalLostValue.toLocaleString('en-IN');
      if (document.getElementById('statTopLostReason')) document.getElementById('statTopLostReason').textContent = topReason;

      let filteredLost = scopedLost.filter(c => {
        if (filterReason !== 'ALL' && c.lostReason !== filterReason) return false;
        return true;
      });

      if (filteredLost.length === 0) {
        tbody.innerHTML = `<tr><td colspan="7" class="py-8 text-center text-slate-400">No lost leads recorded in this category.</td></tr>`;
        return;
      }

      tbody.innerHTML = filteredLost.map(c => {
        const reasonBadgeClass = 
          c.lostReason === 'Budget' ? 'bg-amber-100 text-amber-900 border-amber-300' :
          c.lostReason === 'Booked Someone Else' ? 'bg-purple-100 text-purple-900 border-purple-300' :
          c.lostReason === 'Ghost / Stopped Replying' ? 'bg-slate-100 text-slate-700 border-slate-300' :
          'bg-red-100 text-red-900 border-red-300';

        return `
          <tr class="hover:bg-slate-50 transition">
            <td class="py-3 px-4">
              <div class="font-bold text-slate-900">${c.name}</div>
              <div class="text-[11px] text-slate-500">${c.vendorName}</div>
            </td>
            <td class="py-3 px-4">
              <div class="font-medium text-slate-800">${c.weddingDate || 'TBD'}</div>
              <div class="text-[11px] text-slate-500">${c.venue || 'Delhi NCR'}</div>
            </td>
            <td class="py-3 px-4">
              <span class="px-2 py-0.5 rounded text-[10px] font-bold border ${reasonBadgeClass}">
                ${c.lostReason || 'Unspecified'}
              </span>
              <div class="text-[10px] text-slate-400 mt-0.5">Archived: ${c.lostDate || 'Recently'}</div>
            </td>
            <td class="py-3 px-4">
              ${c.lostClientBudget ? 
                `<span class="font-bold text-amber-800 text-xs">₹${Number(c.lostClientBudget).toLocaleString('en-IN')}</span>
                 <div class="text-[10px] text-slate-400">Target Budget</div>` : 
                `<span class="text-slate-400 text-xs italic">Not stated</span>`
              }
            </td>
            <td class="py-3 px-4 max-w-xs">
              <div class="text-xs text-slate-700 italic bg-slate-50 p-2 rounded-lg border border-slate-200 line-clamp-2" title="${(c.lostRemarks || c.notes || '').replace(/"/g, '&quot;')}">
                "${c.lostRemarks || c.notes || 'No remarks recorded.'}"
              </div>
            </td>
            <td class="py-3 px-4 text-right font-bold text-slate-900">₹${Number(c.value||0).toLocaleString('en-IN')}</td>
            <td class="py-3 px-4 text-right">
              <button onclick="restoreLead(${c.id})" class="px-2.5 py-1 text-xs font-semibold rounded-lg bg-white border border-indigo-200 text-indigo-600 hover:bg-indigo-50 transition shadow-2xs">
                Restore
              </button>
            </td>
          </tr>
        `;
      }).join('');
    }

    // Fully Automated CSV Importer: Auto-registers new Vendors & maps leads!
    document.getElementById('headerImportCsvBtn').onclick = () => document.getElementById('csvFileInput').click();
    document.getElementById('csvFileInput').onchange = (e) => {
      const file = e.target.files[0];
      if (!file) return;
      const reader = new FileReader();
      reader.onload = (evt) => {
        const lines = evt.target.result.split(/\r?\n/).filter(l => l.trim().length > 0);
        let imported = 0;
        let newVendorsAdded = 0;

        for (let i = 1; i < lines.length; i++) {
          const cols = lines[i].split(',').map(c => c.replace(/^["']|["']$/g, '').trim());
          if (!cols[1]) continue;

          const rawVendorName = cols[2] ? cols[2].trim() : "The Wedding Shaire";
          
          let matchedVendor = vendors.find(v => v.name.toLowerCase() === rawVendorName.toLowerCase());
          if (!matchedVendor) {
            matchedVendor = {
              id: 'v_' + (Date.now() + i),
              name: rawVendorName,
              category: 'Photographer'
            };
            vendors.push(matchedVendor);
            newVendorsAdded++;
          }

          contacts.unshift({
            id: Date.now() + i,
            name: cols[1],
            vendorId: matchedVendor.id,
            vendorName: matchedVendor.name,
            phone: cols[3] || '',
            instaId: cols[4] || '',
            weddingDate: cols[5] || '',
            venue: cols[6] || 'Delhi NCR',
            stage: cols[7] || 'Lead In',
            followUpDate: cols[12] || todayStr,
            value: parseFloat(cols[8]) || 0
          });
          imported++;
        }
        saveData();
        renderNav();
        renderPipeline();
        renderPermissionsSettings();
        alert(`✅ Imported ${imported} leads successfully!${newVendorsAdded > 0 ? ` Automatically registered ${newVendorsAdded} new vendor(s).` : ''}`);
      };
      reader.readAsText(file);
    };

    // CSV Export (Admin only)
    document.getElementById('sideExportCsvBtn').onclick = () => {
      if (currentRole !== 'admin') {
        alert('⛔ CSV export is restricted to Admin.');
        return;
      }
      if (!contacts.length) return alert('No leads to export.');
      const headers = ['ID', 'ClientName', 'Vendor', 'Phone', 'Instagram', 'WeddingDate', 'Venue', 'Stage', 'PackageTotal', 'Commission10Pct', 'AdvancePaid', 'PendingBalance', 'FollowUpDate'];
      const rows = contacts.map(c => [
        c.id, `"${c.name}"`, `"${c.vendorName}"`, `"${c.phone||''}"`, `"${c.instaId||''}"`,
        `"${c.weddingDate||''}"`, `"${c.venue||''}"`, `"${c.stage||''}"`, c.value||0, (c.value||0)*0.1,
        c.advance||0, Math.max(0, (c.value||0)-(c.advance||0)), `"${c.followUpDate||''}"`
      ]);
      const csv = [headers.join(','), ...rows.map(r => r.join(','))].join('\r\n');
      const blob = new Blob([csv], { type: 'text/csv;charset=utf-8;' });
      const url = URL.createObjectURL(blob);
      const a = document.createElement('a');
      a.href = url;
      a.download = `leads_backup_${todayStr}.csv`;
      a.click();
    };

    // Boot & Initialize
    
    // ==========================================
    // FULLSCREEN TOGGLE ENGINE (Table & Kanban)
    // ==========================================
    let isBoardFullscreen = false;

    function toggleBoardFullscreen() {
      const card = document.getElementById('mainBoardCard');
      const icon = document.getElementById('fullscreenIcon');
      const label = document.getElementById('fullscreenLabel');
      isBoardFullscreen = !isBoardFullscreen;

      if (isBoardFullscreen) {
        card.classList.add('board-fullscreen');
        document.body.style.overflow = 'hidden';
        if (icon) icon.textContent = '🗗';
        if (label) label.textContent = 'Exit Fullscreen';
        if (!document.fullscreenElement && card.requestFullscreen) {
          card.requestFullscreen().catch(() => {});
        }
      } else {
        card.classList.remove('board-fullscreen');
        document.body.style.overflow = '';
        if (icon) icon.textContent = '⛶';
        if (label) label.textContent = 'Fullscreen';
        if (document.fullscreenElement && document.exitFullscreen) {
          document.exitFullscreen().catch(() => {});
        }
      }
    }

    document.getElementById('toggleFullscreenBtn')?.addEventListener('click', toggleBoardFullscreen);

    // Keyboard Shortcuts: ESC or F
    document.addEventListener('keydown', (e) => {
      if ((e.key === 'f' || e.key === 'F') && !['INPUT', 'TEXTAREA', 'SELECT'].includes(e.target.tagName)) {
        e.preventDefault();
        toggleBoardFullscreen();
      }
      if (e.key === 'Escape' && isBoardFullscreen) {
        toggleBoardFullscreen();
      }
    });

    document.addEventListener('fullscreenchange', () => {
      if (!document.fullscreenElement && isBoardFullscreen) {
        toggleBoardFullscreen();
      }
    });

    // ==========================================
    // BOOT: restore session or show sign-in
    // ==========================================
    (function bootAuth() {
      const sid = sessionStorage.getItem('crm_session') || localStorage.getItem('crm_session');
      const user = sid && users.find(u => u.id === sid);
      if (user) enterApp(user);
      else showAuthScreen();
    })();
  </script>
</body>
</html># Testing---CRM
