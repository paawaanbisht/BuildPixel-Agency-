<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>AI Virtual Office</title>
<link href="https://fonts.googleapis.com/css2?family=Syne:wght@400;600;700;800&family=DM+Sans:wght@300;400;500&display=swap" rel="stylesheet">
<style>
*,*::before,*::after{margin:0;padding:0;box-sizing:border-box}
:root{
  --bg:#0a0d14;--panel:#111520;--card:#161c2e;--border:#1e2740;
  --accent:#3d6fff;--green:#00d97e;--orange:#ff8c42;--red:#ff4d6d;--gold:#ffc107;
  --text:#e8eaf0;--muted:#5a6585;--white:#ffffff;
}
html,body{width:100%;height:100%;background:var(--bg);color:var(--text);font-family:'DM Sans',sans-serif;overflow:hidden}

/* LAYOUT */
.app{display:grid;grid-template-columns:220px 1fr 300px;grid-template-rows:56px 1fr;height:100vh;gap:0}

/* TOPBAR */
.topbar{grid-column:1/-1;background:var(--panel);border-bottom:1px solid var(--border);
  display:flex;align-items:center;padding:0 20px;gap:16px;z-index:10}
.topbar-logo{font-family:'Syne',sans-serif;font-weight:800;font-size:18px;color:var(--white);
  display:flex;align-items:center;gap:8px}
.topbar-logo .dot{width:8px;height:8px;background:var(--accent);border-radius:50%;
  box-shadow:0 0 10px var(--accent);animation:pulse 2s infinite}
@keyframes pulse{0%,100%{opacity:1}50%{opacity:0.4}}
.topbar-center{flex:1;display:flex;align-items:center;justify-content:center;gap:24px}
.status-pill{display:flex;align-items:center;gap:6px;font-size:12px;color:var(--muted)}
.status-pill .dot{width:6px;height:6px;border-radius:50%}
.topbar-right{display:flex;align-items:center;gap:12px}
.time-display{font-family:'Syne',sans-serif;font-size:13px;font-weight:600;color:var(--accent)}

/* SIDEBAR */
.sidebar{background:var(--panel);border-right:1px solid var(--border);padding:16px 0;overflow-y:auto}
.sidebar-section{padding:0 12px;margin-bottom:20px}
.sidebar-label{font-size:10px;letter-spacing:0.15em;color:var(--muted);text-transform:uppercase;padding:0 8px;margin-bottom:8px}
.nav-item{display:flex;align-items:center;gap:10px;padding:9px 10px;border-radius:8px;
  cursor:pointer;transition:all 0.2s;font-size:13px;color:var(--muted);font-weight:500}
.nav-item:hover,.nav-item.active{background:rgba(61,111,255,0.12);color:var(--text)}
.nav-item.active{color:var(--accent)}
.nav-item .icon{font-size:15px;width:20px;text-align:center}
.nav-badge{margin-left:auto;background:var(--accent);color:#fff;font-size:10px;
  padding:1px 6px;border-radius:10px;font-weight:700}

/* EMPLOYEE LIST in sidebar */
.emp-item{display:flex;align-items:center;gap:8px;padding:6px 8px;border-radius:8px;
  cursor:pointer;transition:all 0.2s}
.emp-item:hover{background:rgba(255,255,255,0.04)}
.emp-avatar{width:28px;height:28px;border-radius:50%;display:flex;align-items:center;
  justify-content:center;font-size:13px;font-weight:700;flex-shrink:0;position:relative}
.emp-avatar .online-dot{position:absolute;bottom:0;right:0;width:8px;height:8px;
  background:var(--green);border-radius:50%;border:1.5px solid var(--panel)}
.emp-info{flex:1;min-width:0}
.emp-name{font-size:12px;font-weight:500;color:var(--text);white-space:nowrap;overflow:hidden;text-overflow:ellipsis}
.emp-role{font-size:10px;color:var(--muted)}

/* MAIN CONTENT */
.main{overflow:hidden;display:flex;flex-direction:column}
.main-header{padding:16px 20px 0;display:flex;align-items:center;justify-content:space-between}
.page-title{font-family:'Syne',sans-serif;font-size:20px;font-weight:700}
.tab-bar{display:flex;gap:4px;padding:12px 20px 0}
.tab{padding:7px 16px;border-radius:8px 8px 0 0;font-size:12px;font-weight:600;
  cursor:pointer;color:var(--muted);transition:all 0.2s;border-bottom:2px solid transparent}
.tab.active{color:var(--accent);border-bottom-color:var(--accent);background:rgba(61,111,255,0.08)}
.tab-content{flex:1;overflow:hidden;padding:0 20px 20px}

/* OFFICE VIEW */
#view-office{display:grid;grid-template-columns:repeat(3,1fr);gap:12px;padding-top:12px;height:100%;overflow-y:auto}
.emp-card{background:var(--card);border:1px solid var(--border);border-radius:12px;
  padding:16px;cursor:pointer;transition:all 0.25s;position:relative;overflow:hidden}
.emp-card:hover{border-color:var(--accent);transform:translateY(-2px);box-shadow:0 8px 24px rgba(61,111,255,0.15)}
.emp-card.busy{border-color:rgba(255,140,66,0.4)}
.emp-card-top{display:flex;align-items:center;gap:12px;margin-bottom:12px}
.emp-card-avatar{width:44px;height:44px;border-radius:12px;display:flex;align-items:center;
  justify-content:center;font-size:20px;font-weight:700}
.emp-card-info{flex:1}
.emp-card-name{font-family:'Syne',sans-serif;font-size:14px;font-weight:700;color:var(--white)}
.emp-card-role{font-size:11px;color:var(--muted);margin-top:2px}
.emp-status-badge{font-size:10px;padding:3px 8px;border-radius:20px;font-weight:600}
.badge-online{background:rgba(0,217,126,0.15);color:var(--green)}
.badge-busy{background:rgba(255,140,66,0.15);color:var(--orange)}
.emp-card-task{font-size:11px;color:var(--muted);background:var(--bg);border-radius:6px;
  padding:8px 10px;margin-bottom:10px;min-height:36px;line-height:1.5}
.emp-card-stats{display:flex;gap:12px}
.mini-stat{text-align:center}
.mini-stat-val{font-family:'Syne',sans-serif;font-size:14px;font-weight:700;color:var(--accent)}
.mini-stat-lbl{font-size:10px;color:var(--muted)}
.typing-indicator{display:inline-flex;gap:3px;align-items:center}
.typing-indicator span{width:5px;height:5px;background:var(--accent);border-radius:50%;
  animation:typing 1.2s infinite}
.typing-indicator span:nth-child(2){animation-delay:0.2s}
.typing-indicator span:nth-child(3){animation-delay:0.4s}
@keyframes typing{0%,60%,100%{transform:translateY(0)}30%{transform:translateY(-5px)}}

/* CHAT PANEL */
.chat-panel{background:var(--panel);border-left:1px solid var(--border);display:flex;flex-direction:column;height:100%}
.chat-header{padding:14px 16px;border-bottom:1px solid var(--border);display:flex;align-items:center;gap:10px}
.chat-avatar{width:34px;height:34px;border-radius:10px;display:flex;align-items:center;justify-content:center;font-size:16px}
.chat-title{font-family:'Syne',sans-serif;font-weight:700;font-size:13px}
.chat-subtitle{font-size:11px;color:var(--muted)}
.chat-messages{flex:1;overflow-y:auto;padding:14px;display:flex;flex-direction:column;gap:10px;scroll-behavior:smooth}
.msg{max-width:85%;display:flex;flex-direction:column;gap:3px}
.msg.user{align-self:flex-end;align-items:flex-end}
.msg.bot{align-self:flex-start;align-items:flex-start}
.msg-sender{font-size:10px;color:var(--muted);font-weight:600;text-transform:uppercase;letter-spacing:0.05em}
.msg-bubble{padding:9px 12px;border-radius:12px;font-size:12px;line-height:1.5;word-break:break-word}
.msg.user .msg-bubble{background:var(--accent);color:#fff;border-bottom-right-radius:3px}
.msg.bot .msg-bubble{background:var(--card);color:var(--text);border-bottom-left-radius:3px;border:1px solid var(--border)}
.msg-time{font-size:10px;color:var(--muted)}
.chat-input-area{padding:12px;border-top:1px solid var(--border);display:flex;gap:8px}
.emp-selector{background:var(--card);border:1px solid var(--border);border-radius:8px;
  padding:6px 10px;color:var(--text);font-size:11px;cursor:pointer;outline:none;font-family:'DM Sans',sans-serif}
.chat-input{flex:1;background:var(--card);border:1px solid var(--border);border-radius:10px;
  padding:8px 12px;color:var(--text);font-size:12px;outline:none;font-family:'DM Sans',sans-serif;resize:none}
.chat-input:focus{border-color:var(--accent)}
.send-btn{background:var(--accent);border:none;border-radius:10px;padding:8px 14px;
  color:#fff;cursor:pointer;font-size:14px;transition:all 0.2s}
.send-btn:hover{background:#5c84ff}
.send-btn:disabled{opacity:0.4;cursor:not-allowed}

/* OFFICE CHAT (group) */
#view-groupchat{display:flex;flex-direction:column;height:100%;padding-top:8px}
.group-chat-messages{flex:1;overflow-y:auto;display:flex;flex-direction:column;gap:8px;padding:8px 0;scroll-behavior:smooth}
.group-msg{display:flex;gap:10px;align-items:flex-start}
.group-msg.self{flex-direction:row-reverse}
.group-msg-avatar{width:30px;height:30px;border-radius:8px;display:flex;align-items:center;justify-content:center;font-size:14px;flex-shrink:0}
.group-msg-body{max-width:75%}
.group-msg-meta{font-size:10px;color:var(--muted);margin-bottom:3px;display:flex;gap:8px}
.group-msg-meta .name{font-weight:600;color:var(--text)}
.group-msg-bubble{background:var(--card);border:1px solid var(--border);border-radius:10px;padding:8px 12px;font-size:12px;line-height:1.5}
.group-msg.self .group-msg-bubble{background:var(--accent);border-color:var(--accent);color:#fff}
.group-msg.self .group-msg-body{align-items:flex-end;display:flex;flex-direction:column}
.group-input-area{padding:10px 0 0;display:flex;gap:8px;border-top:1px solid var(--border);margin-top:8px}
.group-input{flex:1;background:var(--card);border:1px solid var(--border);border-radius:10px;
  padding:9px 14px;color:var(--text);font-size:13px;outline:none;font-family:'DM Sans',sans-serif}
.group-input:focus{border-color:var(--accent)}
.group-send-btn{background:var(--accent);border:none;border-radius:10px;padding:9px 18px;color:#fff;cursor:pointer;font-weight:700;font-size:13px}

/* DASHBOARD */
#view-dashboard{display:grid;grid-template-columns:repeat(4,1fr);grid-template-rows:auto auto 1fr;gap:12px;padding-top:12px;height:100%;overflow-y:auto}
.stat-box{background:var(--card);border:1px solid var(--border);border-radius:12px;padding:16px}
.stat-box-label{font-size:11px;color:var(--muted);text-transform:uppercase;letter-spacing:0.1em;margin-bottom:8px}
.stat-box-val{font-family:'Syne',sans-serif;font-size:28px;font-weight:800;color:var(--white)}
.stat-box-sub{font-size:11px;color:var(--green);margin-top:4px}
.stat-box-sub.red{color:var(--red)}
.chart-box{grid-column:span 2;background:var(--card);border:1px solid var(--border);border-radius:12px;padding:16px}
.chart-box-title{font-family:'Syne',sans-serif;font-size:13px;font-weight:700;margin-bottom:12px}
.bar-chart{display:flex;align-items:flex-end;gap:8px;height:100px}
.bar{flex:1;border-radius:4px 4px 0 0;background:var(--accent);opacity:0.8;transition:height 0.5s ease;position:relative;min-height:4px}
.bar:hover{opacity:1}
.bar-label{position:absolute;bottom:-18px;left:50%;transform:translateX(-50%);font-size:9px;color:var(--muted);white-space:nowrap}
.activity-box{grid-column:1/-1;background:var(--card);border:1px solid var(--border);border-radius:12px;padding:16px;overflow:auto}
.activity-title{font-family:'Syne',sans-serif;font-size:13px;font-weight:700;margin-bottom:12px}
.activity-item{display:flex;align-items:center;gap:12px;padding:8px 0;border-bottom:1px solid var(--border);font-size:12px}
.activity-item:last-child{border-bottom:none}
.activity-emp{display:flex;align-items:center;gap:8px;width:140px}
.activity-desc{flex:1;color:var(--muted)}
.activity-time{color:var(--muted);font-size:11px;white-space:nowrap}
.activity-tag{font-size:10px;padding:2px 8px;border-radius:10px;font-weight:600}
.tag-done{background:rgba(0,217,126,0.15);color:var(--green)}
.tag-progress{background:rgba(61,111,255,0.15);color:var(--accent)}
.tag-review{background:rgba(255,193,7,0.15);color:var(--gold)}

/* TASKS VIEW */
#view-tasks{display:flex;flex-direction:column;gap:0;padding-top:8px;height:100%;overflow-y:auto}
.task-add-bar{display:flex;gap:8px;padding:8px 0 12px;border-bottom:1px solid var(--border);margin-bottom:12px}
.task-input{flex:1;background:var(--card);border:1px solid var(--border);border-radius:8px;
  padding:8px 12px;color:var(--text);font-size:12px;outline:none;font-family:'DM Sans',sans-serif}
.task-input:focus{border-color:var(--accent)}
.task-emp-sel{background:var(--card);border:1px solid var(--border);border-radius:8px;
  padding:8px 10px;color:var(--text);font-size:11px;cursor:pointer;outline:none;font-family:'DM Sans',sans-serif}
.task-add-btn{background:var(--accent);border:none;border-radius:8px;padding:8px 16px;
  color:#fff;cursor:pointer;font-size:12px;font-weight:700}
.task-kanban{display:grid;grid-template-columns:repeat(3,1fr);gap:12px;flex:1}
.task-col{background:var(--card);border:1px solid var(--border);border-radius:10px;padding:12px;display:flex;flex-direction:column;gap:8px}
.task-col-title{font-size:11px;font-weight:700;text-transform:uppercase;letter-spacing:0.1em;margin-bottom:4px;display:flex;align-items:center;gap:6px}
.task-card{background:var(--bg);border:1px solid var(--border);border-radius:8px;padding:10px;font-size:12px}
.task-card-title{font-weight:600;margin-bottom:6px;color:var(--white)}
.task-card-emp{display:flex;align-items:center;gap:6px;font-size:11px;color:var(--muted)}
.task-card-avatar{width:18px;height:18px;border-radius:4px;display:flex;align-items:center;justify-content:center;font-size:9px}

/* SCROLLBAR */
::-webkit-scrollbar{width:4px;height:4px}
::-webkit-scrollbar-track{background:transparent}
::-webkit-scrollbar-thumb{background:var(--border);border-radius:4px}

/* Notification toast */
.toast{position:fixed;bottom:20px;left:50%;transform:translateX(-50%) translateY(60px);
  background:var(--card);border:1px solid var(--border);border-radius:10px;padding:10px 18px;
  font-size:12px;color:var(--text);z-index:999;transition:transform 0.3s ease;display:flex;align-items:center;gap:8px}
.toast.show{transform:translateX(-50%) translateY(0)}

select option{background:var(--card);color:var(--text)}
</style>
</head>
<body>

<div class="toast" id="toast">🤖 <span id="toastMsg"></span></div>

<div class="app">

<!-- TOPBAR -->
<div class="topbar">
  <div class="topbar-logo">
    <div class="dot"></div>
    AI OFFICE
  </div>
  <div class="topbar-center">
    <div class="status-pill"><div class="dot" style="background:var(--green)"></div> 7 Employees Online</div>
    <div class="status-pill"><div class="dot" style="background:var(--accent)"></div> 3 Tasks In Progress</div>
    <div class="status-pill"><div class="dot" style="background:var(--gold)"></div> 2 Clients Today</div>
  </div>
  <div class="topbar-right">
    <div class="time-display" id="clock">--:--:--</div>
  </div>
</div>

<!-- SIDEBAR -->
<div class="sidebar">
  <div class="sidebar-section">
    <div class="sidebar-label">Navigation</div>
    <div class="nav-item active" onclick="switchView('office',this)"><span class="icon">🏢</span> Office Floor</div>
    <div class="nav-item" onclick="switchView('groupchat',this)"><span class="icon">💬</span> Office Chat <span class="nav-badge" id="chatBadge">0</span></div>
    <div class="nav-item" onclick="switchView('dashboard',this)"><span class="icon">📊</span> Dashboard</div>
    <div class="nav-item" onclick="switchView('tasks',this)"><span class="icon">✅</span> Tasks</div>
  </div>
  <div class="sidebar-section">
    <div class="sidebar-label">Team</div>
    <div id="empSidebarList"></div>
  </div>
</div>

<!-- MAIN -->
<div class="main">
  <div class="tab-bar" id="tabBar" style="display:none"></div>

  <div class="tab-content">

    <!-- OFFICE VIEW -->
    <div id="view-office">
      <!-- Generated by JS -->
    </div>

    <!-- GROUP CHAT -->
    <div id="view-groupchat" style="display:none">
      <div class="group-chat-messages" id="groupMessages"></div>
      <div class="group-input-area">
        <input class="group-input" id="groupInput" placeholder="Sab employees ko message karo..." />
        <button class="group-send-btn" onclick="sendGroupMessage()">Send</button>
      </div>
    </div>

    <!-- DASHBOARD -->
    <div id="view-dashboard" style="display:none">
      <div class="stat-box">
        <div class="stat-box-label">Total Clients</div>
        <div class="stat-box-val" id="stat-clients">12</div>
        <div class="stat-box-sub">↑ +3 this week</div>
      </div>
      <div class="stat-box">
        <div class="stat-box-label">Total Earning</div>
        <div class="stat-box-val" id="stat-earn">₹84,500</div>
        <div class="stat-box-sub">↑ +₹12k this month</div>
      </div>
      <div class="stat-box">
        <div class="stat-box-label">Tasks Done</div>
        <div class="stat-box-val" id="stat-tasks">47</div>
        <div class="stat-box-sub">↑ +8 today</div>
      </div>
      <div class="stat-box">
        <div class="stat-box-label">Pending Review</div>
        <div class="stat-box-val" id="stat-pending">5</div>
        <div class="stat-box-sub red">↓ 2 overdue</div>
      </div>
      <div class="chart-box">
        <div class="chart-box-title">Weekly Clients</div>
        <div class="bar-chart" id="barChart"></div>
      </div>
      <div class="chart-box">
        <div class="chart-box-title">Weekly Earnings (₹k)</div>
        <div class="bar-chart" id="earnChart"></div>
      </div>
      <div class="activity-box">
        <div class="activity-title">Recent Activity</div>
        <div id="activityLog"></div>
      </div>
    </div>

    <!-- TASKS -->
    <div id="view-tasks" style="display:none">
      <div class="task-add-bar">
        <input class="task-input" id="taskInput" placeholder="Nayi task likhein..." />
        <select class="task-emp-sel" id="taskEmpSel"></select>
        <button class="task-add-btn" onclick="addTask()">+ Add Task</button>
      </div>
      <div class="task-kanban">
        <div class="task-col">
          <div class="task-col-title"><span>🟡</span> Todo (<span id="todoCount">0</span>)</div>
          <div id="col-todo"></div>
        </div>
        <div class="task-col">
          <div class="task-col-title"><span>🔵</span> In Progress (<span id="progressCount">0</span>)</div>
          <div id="col-progress"></div>
        </div>
        <div class="task-col">
          <div class="task-col-title"><span>🟢</span> Done (<span id="doneCount">0</span>)</div>
          <div id="col-done"></div>
        </div>
      </div>
    </div>

  </div>
</div>

<!-- CHAT PANEL (right) -->
<div class="chat-panel">
  <div class="chat-header">
    <div class="chat-avatar" id="chatAvatar">🤖</div>
    <div>
      <div class="chat-title" id="chatTitle">Select an Employee</div>
      <div class="chat-subtitle" id="chatSubtitle">Click on any employee to chat</div>
    </div>
  </div>
  <div class="chat-messages" id="chatMessages">
    <div style="text-align:center;color:var(--muted);font-size:12px;margin-top:40px">
      Kisi bhi employee par click karein<br>aur seedha baat karein 💬
    </div>
  </div>
  <div class="chat-input-area">
    <select class="emp-selector" id="empSelector" onchange="selectEmployeeFromDropdown()"></select>
    <textarea class="chat-input" id="chatInput" rows="1" placeholder="Message likho..." onkeydown="handleKey(event)"></textarea>
    <button class="send-btn" id="sendBtn" onclick="sendMessage()">➤</button>
  </div>
</div>

</div>

<script>
// ══════════════════════════════════════════════════════════
//  EMPLOYEE DATA
// ══════════════════════════════════════════════════════════
const employees = [
  {
    id:'alex', name:'Alex', role:'Client Manager', emoji:'👔',
    color:'#3d6fff', bg:'rgba(61,111,255,0.15)',
    tasks:8, done:6,
    currentTask:'Client proposal review karna',
    systemPrompt:`Tum Alex ho, ek professional Client Manager. Tumhara kaam clients lana, unse baat karna, unki requirements samajhna aur team ko brief karna hai. 
    Tum Hinglish (Hindi+English mix) mein baat karte ho. Professional lekin friendly ho. Jab client details lo, toh project budget, timeline, aur requirements zaroor pucho. 
    Agar koi client project laata hai, uski details note karo aur kehna "Main yeh details Developer ko pass karunga."
    Always respond in Hinglish, keep responses concise (2-4 lines).`
  },
  {
    id:'dev', name:'Dev', role:'Lead Developer', emoji:'💻',
    color:'#00d97e', bg:'rgba(0,217,126,0.15)',
    tasks:12, done:9,
    currentTask:'Website ka frontend build karna',
    systemPrompt:`Tum Dev ho, ek expert Lead Developer. Tum websites, apps aur software banate ho. 
    Tum Hinglish mein baat karte ho, thoda technical bhi. React, Node.js, Python sab jaante ho.
    Jab koi project aata hai, tum technical approach batate ho, timeline estimate karte ho.
    Agar koi puche toh bata sakte ho: "Is project mein X days lagenge, main Y technology use karunga."
    Always respond in Hinglish, keep responses concise (2-4 lines).`
  },
  {
    id:'qc', name:'Priya', role:'QA / Quality Check', emoji:'🔍',
    color:'#ffc107', bg:'rgba(255,193,7,0.15)',
    tasks:7, done:5,
    currentTask:'Latest website ka bug testing',
    systemPrompt:`Tum Priya ho, Quality Assurance Expert. Tumhara kaam websites aur apps test karna hai, bugs dhundna aur quality ensure karna hai.
    Tum Hinglish mein baat karte ho. Systematic aur detail-oriented ho.
    Test cases, bug reports, aur quality standards ke baare mein baat karte ho.
    Jab koi project review karo, checklist approach use karo.
    Always respond in Hinglish, keep responses concise (2-4 lines).`
  },
  {
    id:'deliver', name:'Ravi', role:'Delivery Manager', emoji:'🚀',
    color:'#ff8c42', bg:'rgba(255,140,66,0.15)',
    tasks:6, done:6,
    currentTask:'Client ko final project deliver karna',
    systemPrompt:`Tum Ravi ho, Delivery Manager. Tumhara kaam complete projects clients ko deliver karna, demo dena, aur client satisfaction ensure karna hai.
    Tum Hinglish mein baat karte ho. Punctual aur organized ho.
    Delivery timelines, client feedback, aur project handover ke expert ho.
    Always respond in Hinglish, keep responses concise (2-4 lines).`
  },
  {
    id:'finance', name:'Meera', role:'Finance Manager', emoji:'💰',
    color:'#a78bfa', bg:'rgba(167,139,250,0.15)',
    tasks:5, done:4,
    currentTask:'Monthly earnings report banana',
    systemPrompt:`Tum Meera ho, Finance Manager. Tum invoices banati ho, payments track karti ho, aur financial reports prepare karti ho.
    Tum Hinglish mein baat karte ho. Numbers aur finance mein expert ho.
    Revenue, expenses, profit margins, aur GST ke baare mein baat kar sakti ho.
    Agar koi project amount puche: "Is project ki cost ₹X hai, payment 50% advance, 50% delivery par."
    Always respond in Hinglish, keep responses concise (2-4 lines).`
  },
  {
    id:'report', name:'Arjun', role:'Report Analyst', emoji:'📈',
    color:'#fb7185', bg:'rgba(251,113,133,0.15)',
    tasks:4, done:3,
    currentTask:'Weekly performance report taiyar karna',
    systemPrompt:`Tum Arjun ho, Report Analyst. Tum data analyze karte ho, performance reports banate ho, aur insights provide karte ho.
    Tum Hinglish mein baat karte ho. Analytical aur data-driven ho.
    Client acquisition, revenue trends, team performance ke baare mein baat karte ho.
    Reports mein bullet points use karo aur key metrics highlight karo.
    Always respond in Hinglish, keep responses concise (2-4 lines).`
  },
  {
    id:'support', name:'Zara', role:'Client Support', emoji:'🎧',
    color:'#34d399', bg:'rgba(52,211,153,0.15)',
    tasks:9, done:7,
    currentTask:'Client support tickets handle karna',
    systemPrompt:`Tum Zara ho, Client Support Specialist. Tum clients ki problems solve karti ho, queries handle karti ho, aur unhein satisfied rakhti ho.
    Tum Hinglish mein baat karte ho. Empathetic aur solution-focused ho.
    Client complaints, feedback, aur after-delivery support handle karti ho.
    Always respond in Hinglish, keep responses concise (2-4 lines).`
  }
];

// ══════════════════════════════════════════════════════════
//  STATE
// ══════════════════════════════════════════════════════════
let selectedEmp = null;
let chatHistories = {};
employees.forEach(e => { chatHistories[e.id] = []; });

let tasks = [
  {id:1, title:'Client website homepage design', emp:'dev', status:'progress'},
  {id:2, title:'Q3 report prepare karo', emp:'report', status:'todo'},
  {id:3, title:'New client proposal send karo', emp:'alex', status:'done'},
  {id:4, title:'Payment invoice generate karo', emp:'finance', status:'progress'},
  {id:5, title:'Bug fix - contact form', emp:'qc', status:'todo'},
  {id:6, title:'Project delivery to Client A', emp:'deliver', status:'done'},
];

let activities = [
  {emp:'alex', desc:'New client Sharma Enterprises onboarded', time:'2m ago', tag:'done'},
  {emp:'dev', desc:'Homepage design 80% complete', time:'15m ago', tag:'progress'},
  {emp:'meera', desc:'Invoice #INV-2024-041 sent to client', time:'32m ago', tag:'done'},
  {emp:'qc', desc:'Bug report submitted - 3 issues found', time:'1h ago', tag:'review'},
  {emp:'deliver', desc:'Project Alpha delivered & approved', time:'2h ago', tag:'done'},
  {emp:'report', desc:'Weekly analytics report ready', time:'3h ago', tag:'done'},
];

let groupMessages = [
  {emp:'alex', text:'Sabko good morning! Aaj 3 client calls hain. Sab ready rehna 💼', time:'9:01 AM'},
  {emp:'dev', text:'Ready hoon! Homepage ka wireframe aaj tak ho jayega ✅', time:'9:03 AM'},
  {emp:'qc', text:'QA checklist update kar liya hai. Dev bhai, build kab milega?', time:'9:05 AM'},
  {emp:'dev', text:'Dopahar tak de dunga Priya!', time:'9:06 AM'},
  {emp:'meera', text:'Finance: Aaj 2 payments pending hain. Follow up karenge 💰', time:'9:10 AM'},
  {emp:'report', text:'Weekly report ready hai, boss ko bhej du?', time:'9:12 AM'},
  {emp:'deliver', text:'Client B ka delivery aaj 5 baje. Sab kuch ready rakhna 🚀', time:'9:15 AM'},
  {emp:'support', text:'Client A se 2 tickets aaye hain. Handle kar rahi hoon 🎧', time:'9:18 AM'},
];

let groupUnread = 0;
let currentView = 'office';

// ══════════════════════════════════════════════════════════
//  CLOCK
// ══════════════════════════════════════════════════════════
function updateClock() {
  const now = new Date();
  document.getElementById('clock').textContent = now.toLocaleTimeString('en-IN',{hour12:false});
}
setInterval(updateClock, 1000);
updateClock();

// ══════════════════════════════════════════════════════════
//  INIT
// ══════════════════════════════════════════════════════════
function init() {
  renderOffice();
  renderSidebar();
  renderEmpSelector();
  renderGroupChat();
  renderDashboard();
  renderTasks();
}

// ══════════════════════════════════════════════════════════
//  OFFICE FLOOR
// ══════════════════════════════════════════════════════════
function renderOffice() {
  const grid = document.getElementById('view-office');
  grid.innerHTML = '';
  employees.forEach(emp => {
    const isBusy = emp.tasks > emp.done;
    const card = document.createElement('div');
    card.className = `emp-card ${isBusy ? 'busy' : ''}`;
    card.onclick = () => selectEmployee(emp.id);
    card.innerHTML = `
      <div class="emp-card-top">
        <div class="emp-card-avatar" style="background:${emp.bg};color:${emp.color}">${emp.emoji}</div>
        <div class="emp-card-info">
          <div class="emp-card-name">${emp.name}</div>
          <div class="emp-card-role">${emp.role}</div>
        </div>
        <div class="emp-status-badge ${isBusy ? 'badge-busy' : 'badge-online'}">${isBusy ? 'Busy' : 'Free'}</div>
      </div>
      <div class="emp-card-task" id="task-${emp.id}">
        <div class="typing-indicator"><span></span><span></span><span></span></div>
      </div>
      <div class="emp-card-stats">
        <div class="mini-stat"><div class="mini-stat-val">${emp.tasks}</div><div class="mini-stat-lbl">Tasks</div></div>
        <div class="mini-stat"><div class="mini-stat-val" style="color:var(--green)">${emp.done}</div><div class="mini-stat-lbl">Done</div></div>
        <div class="mini-stat"><div class="mini-stat-val" style="color:var(--orange)">${emp.tasks-emp.done}</div><div class="mini-stat-lbl">Pending</div></div>
      </div>`;
    grid.appendChild(card);
    setTimeout(() => {
      const taskEl = document.getElementById(`task-${emp.id}`);
      if (taskEl) taskEl.innerHTML = `📌 ${emp.currentTask}`;
    }, 800 + Math.random() * 1000);
  });
}

// ══════════════════════════════════════════════════════════
//  SIDEBAR
// ══════════════════════════════════════════════════════════
function renderSidebar() {
  const list = document.getElementById('empSidebarList');
  list.innerHTML = '';
  employees.forEach(emp => {
    const div = document.createElement('div');
    div.className = 'emp-item';
    div.onclick = () => selectEmployee(emp.id);
    div.innerHTML = `
      <div class="emp-avatar" style="background:${emp.bg};color:${emp.color}">
        ${emp.emoji}
        <div class="online-dot"></div>
      </div>
      <div class="emp-info">
        <div class="emp-name">${emp.name}</div>
        <div class="emp-role">${emp.role}</div>
      </div>`;
    list.appendChild(div);
  });
}

// ══════════════════════════════════════════════════════════
//  EMP SELECTOR (dropdown in chat)
// ══════════════════════════════════════════════════════════
function renderEmpSelector() {
  const sel = document.getElementById('empSelector');
  const taskSel = document.getElementById('taskEmpSel');
  employees.forEach(emp => {
    const opt = new Option(`${emp.emoji} ${emp.name}`, emp.id);
    sel.appendChild(opt);
    taskSel.appendChild(opt.cloneNode(true));
  });
}

function selectEmployeeFromDropdown() {
  const id = document.getElementById('empSelector').value;
  selectEmployee(id);
}

// ══════════════════════════════════════════════════════════
//  SELECT EMPLOYEE → OPEN CHAT
// ══════════════════════════════════════════════════════════
function selectEmployee(id) {
  selectedEmp = employees.find(e => e.id === id);
  document.getElementById('empSelector').value = id;
  document.getElementById('chatAvatar').textContent = selectedEmp.emoji;
  document.getElementById('chatAvatar').style.background = selectedEmp.bg;
  document.getElementById('chatTitle').textContent = selectedEmp.name;
  document.getElementById('chatSubtitle').textContent = selectedEmp.role + ' • Online';
  renderChatMessages();
  if (chatHistories[id].length === 0) {
    receiveMessage(selectedEmp, `Namaste! Main ${selectedEmp.name} hoon, tumhara ${selectedEmp.role}. Kaise madad kar sakta/sakti hoon? 😊`);
  }
}

// ══════════════════════════════════════════════════════════
//  RENDER CHAT MESSAGES
// ══════════════════════════════════════════════════════════
function renderChatMessages() {
  const box = document.getElementById('chatMessages');
  box.innerHTML = '';
  const history = chatHistories[selectedEmp.id];
  history.forEach(m => appendChatMsg(m.role, m.content));
  box.scrollTop = box.scrollHeight;
}

function appendChatMsg(role, text) {
  const box = document.getElementById('chatMessages');
  const isUser = role === 'user';
  const div = document.createElement('div');
  div.className = `msg ${isUser ? 'user' : 'bot'}`;
  const time = new Date().toLocaleTimeString('en-IN',{hour:'2-digit',minute:'2-digit'});
  div.innerHTML = `
    ${!isUser ? `<div class="msg-sender">${selectedEmp?.name || 'AI'}</div>` : ''}
    <div class="msg-bubble">${text}</div>
    <div class="msg-time">${time}</div>`;
  box.appendChild(div);
  box.scrollTop = box.scrollHeight;
}

function receiveMessage(emp, text) {
  chatHistories[emp.id].push({role:'assistant', content:text});
  if (selectedEmp && selectedEmp.id === emp.id) appendChatMsg('assistant', text);
}

// ══════════════════════════════════════════════════════════
//  SEND MESSAGE
// ══════════════════════════════════════════════════════════
async function sendMessage() {
  if (!selectedEmp) { showToast('Pehle koi employee select karo!'); return; }
  const input = document.getElementById('chatInput');
  const text = input.value.trim();
  if (!text) return;
  input.value = '';
  document.getElementById('sendBtn').disabled = true;

  chatHistories[selectedEmp.id].push({role:'user', content:text});
  appendChatMsg('user', text);

  // Add to activity
  addActivity(selectedEmp.id, `New message: "${text.substring(0,40)}..."`, 'progress');

  // Show typing
  const box = document.getElementById('chatMessages');
  const typingDiv = document.createElement('div');
  typingDiv.className = 'msg bot';
  typingDiv.id = 'typing';
  typingDiv.innerHTML = `<div class="msg-bubble"><div class="typing-indicator"><span></span><span></span><span></span></div></div>`;
  box.appendChild(typingDiv);
  box.scrollTop = box.scrollHeight;

  try {
    const messages = chatHistories[selectedEmp.id].map(m => ({role:m.role, content:m.content}));
    const response = await fetch('https://api.anthropic.com/v1/messages', {
      method:'POST',
      headers:{'Content-Type':'application/json'},
      body: JSON.stringify({
        model:'claude-sonnet-4-20250514',
        max_tokens:300,
        system: selectedEmp.systemPrompt,
        messages: messages
      })
    });
    const data = await response.json();
    const reply = data.content?.[0]?.text || 'Maafi chahta/chahti hoon, kuch problem aayi. Dobara try karo.';
    typingDiv.remove();
    receiveMessage(selectedEmp, reply);
  } catch(e) {
    typingDiv.remove();
    receiveMessage(selectedEmp, 'Network issue aa gaya! Please dobara try karo. 🙏');
  }
  document.getElementById('sendBtn').disabled = false;
}

function handleKey(e) {
  if (e.key === 'Enter' && !e.shiftKey) { e.preventDefault(); sendMessage(); }
}

// ══════════════════════════════════════════════════════════
//  GROUP CHAT
// ══════════════════════════════════════════════════════════
function renderGroupChat() {
  const box = document.getElementById('groupMessages');
  box.innerHTML = '';
  groupMessages.forEach(m => appendGroupMsg(m.emp, m.text, m.time, false));
  setTimeout(() => { box.scrollTop = box.scrollHeight; }, 50);
}

function appendGroupMsg(empId, text, time, isSelf) {
  const emp = employees.find(e => e.id === empId) || {emoji:'👤', color:'#888', bg:'rgba(136,136,136,0.15)', name:'You'};
  const box = document.getElementById('groupMessages');
  const div = document.createElement('div');
  div.className = `group-msg ${isSelf ? 'self' : ''}`;
  div.innerHTML = `
    <div class="group-msg-avatar" style="background:${emp.bg};color:${emp.color}">${emp.emoji}</div>
    <div class="group-msg-body">
      <div class="group-msg-meta"><span class="name">${isSelf ? 'Aap' : emp.name}</span><span>${time}</span></div>
      <div class="group-msg-bubble">${text}</div>
    </div>`;
  box.appendChild(div);
  box.scrollTop = box.scrollHeight;
}

async function sendGroupMessage() {
  const input = document.getElementById('groupInput');
  const text = input.value.trim();
  if (!text) return;
  input.value = '';

  const time = new Date().toLocaleTimeString('en-IN',{hour:'2-digit',minute:'2-digit'});
  groupMessages.push({emp:'user', text, time});
  appendGroupMsg('user', text, time, true);

  groupUnread = 0;

  // Random employee replies
  const responders = [...employees].sort(() => Math.random() - 0.5).slice(0, 2);
  for (const emp of responders) {
    await new Promise(r => setTimeout(r, 1000 + Math.random() * 1500));
    try {
      const res = await fetch('https://api.anthropic.com/v1/messages', {
        method:'POST',
        headers:{'Content-Type':'application/json'},
        body: JSON.stringify({
          model:'claude-sonnet-4-20250514',
          max_tokens:100,
          system: emp.systemPrompt + '\n\nYeh office group chat hai. Chhota reply do (1-2 lines max). Context: User ne group mein kaha: ' + text,
          messages:[{role:'user', content:text}]
        })
      });
      const data = await res.json();
      const reply = data.content?.[0]?.text || 'Theek hai! 👍';
      const t = new Date().toLocaleTimeString('en-IN',{hour:'2-digit',minute:'2-digit'});
      groupMessages.push({emp:emp.id, text:reply, time:t});
      appendGroupMsg(emp.id, reply, t, false);
      if (currentView !== 'groupchat') {
        groupUnread++;
        document.getElementById('chatBadge').textContent = groupUnread;
      }
    } catch(e) {}
  }
}

// ══════════════════════════════════════════════════════════
//  DASHBOARD
// ══════════════════════════════════════════════════════════
function renderDashboard() {
  // Bar charts
  const weeks = ['Mon','Tue','Wed','Thu','Fri','Sat','Sun'];
  const clients = [2,4,3,6,4,5,3];
  const earns = [8,15,12,22,18,20,10];
  renderBar('barChart', weeks, clients, '#3d6fff');
  renderBar('earnChart', weeks, earns, '#00d97e');

  // Activity log
  const log = document.getElementById('activityLog');
  log.innerHTML = '';
  activities.forEach(a => {
    const emp = employees.find(e => e.id === a.emp) || {emoji:'📋', name:'System', color:'#888', bg:'rgba(136,136,136,0.15)'};
    const row = document.createElement('div');
    row.className = 'activity-item';
    row.innerHTML = `
      <div class="activity-emp">
        <div style="width:24px;height:24px;border-radius:6px;background:${emp.bg};color:${emp.color};display:flex;align-items:center;justify-content:center;font-size:12px">${emp.emoji}</div>
        <span style="font-size:11px;font-weight:600">${emp.name}</span>
      </div>
      <div class="activity-desc">${a.desc}</div>
      <div class="activity-time">${a.time}</div>
      <div class="activity-tag tag-${a.tag}">${a.tag === 'done' ? 'Done' : a.tag === 'progress' ? 'In Progress' : 'Review'}</div>`;
    log.appendChild(row);
  });
}

function renderBar(id, labels, values, color) {
  const box = document.getElementById(id);
  box.innerHTML = '';
  const max = Math.max(...values);
  values.forEach((v, i) => {
    const bar = document.createElement('div');
    bar.className = 'bar';
    bar.style.background = color;
    bar.style.height = '4px';
    bar.title = `${labels[i]}: ${v}`;
    bar.innerHTML = `<div class="bar-label">${labels[i]}</div>`;
    box.appendChild(bar);
    setTimeout(() => { bar.style.height = Math.round((v / max) * 90) + 'px'; }, 100 + i * 80);
  });
}

// ══════════════════════════════════════════════════════════
//  TASKS
// ══════════════════════════════════════════════════════════
function renderTasks() {
  ['todo','progress','done'].forEach(s => document.getElementById(`col-${s}`).innerHTML = '');
  tasks.forEach(task => {
    const emp = employees.find(e => e.id === task.emp) || {emoji:'👤', color:'#888', bg:'rgba(136,136,136,0.15)', name:'Unassigned'};
    const card = document.createElement('div');
    card.className = 'task-card';
    const nextStatus = task.status === 'todo' ? 'progress' : task.status === 'progress' ? 'done' : 'todo';
    const btnLabel = task.status === 'todo' ? '▶ Start' : task.status === 'progress' ? '✓ Done' : '↺ Reset';
    card.innerHTML = `
      <div class="task-card-title">${task.title}</div>
      <div style="display:flex;justify-content:space-between;align-items:center;margin-top:6px">
        <div class="task-card-emp">
          <div class="task-card-avatar" style="background:${emp.bg};color:${emp.color}">${emp.emoji}</div>
          ${emp.name}
        </div>
        <button onclick="moveTask(${task.id},'${nextStatus}')" style="background:var(--border);border:none;border-radius:5px;padding:3px 8px;font-size:10px;color:var(--text);cursor:pointer;font-family:'DM Sans',sans-serif">${btnLabel}</button>
      </div>`;
    document.getElementById(`col-${task.status}`).appendChild(card);
  });
  document.getElementById('todoCount').textContent = tasks.filter(t=>t.status==='todo').length;
  document.getElementById('progressCount').textContent = tasks.filter(t=>t.status==='progress').length;
  document.getElementById('doneCount').textContent = tasks.filter(t=>t.status==='done').length;
}

function moveTask(id, status) {
  const task = tasks.find(t => t.id === id);
  task.status = status;
  renderTasks();
  if (status === 'done') {
    const emp = employees.find(e => e.id === task.emp);
    if (emp) { emp.done++; }
    addActivity(task.emp, `Task complete: "${task.title}"`, 'done');
    showToast(`✅ Task complete ho gaya!`);
  }
}

function addTask() {
  const title = document.getElementById('taskInput').value.trim();
  const empId = document.getElementById('taskEmpSel').value;
  if (!title) { showToast('Task title likho!'); return; }
  tasks.push({id: Date.now(), title, emp: empId, status: 'todo'});
  document.getElementById('taskInput').value = '';
  renderTasks();
  addActivity(empId, `New task assigned: "${title}"`, 'progress');
  showToast('📋 Task add ho gaya!');
}

// ══════════════════════════════════════════════════════════
//  ACTIVITY LOG
// ══════════════════════════════════════════════════════════
function addActivity(empId, desc, tag) {
  activities.unshift({emp: empId, desc, time: 'Just now', tag});
  if (activities.length > 10) activities.pop();
  if (currentView === 'dashboard') renderDashboard();
}

// ══════════════════════════════════════════════════════════
//  VIEW SWITCHING
// ══════════════════════════════════════════════════════════
function switchView(view, navEl) {
  currentView = view;
  document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('active'));
  if (navEl) navEl.classList.add('active');
  ['office','groupchat','dashboard','tasks'].forEach(v => {
    document.getElementById(`view-${v}`).style.display = 'none';
  });
  document.getElementById(`view-${view}`).style.display =
    view === 'office' ? 'grid' :
    view === 'tasks' ? 'flex' :
    view === 'dashboard' ? 'grid' : 'flex';

  if (view === 'dashboard') renderDashboard();
  if (view === 'groupchat') {
    groupUnread = 0;
    document.getElementById('chatBadge').textContent = '0';
    setTimeout(() => {
      const box = document.getElementById('groupMessages');
      box.scrollTop = box.scrollHeight;
    }, 50);
  }
}

// ══════════════════════════════════════════════════════════
//  TOAST
// ══════════════════════════════════════════════════════════
function showToast(msg) {
  document.getElementById('toastMsg').textContent = msg;
  const t = document.getElementById('toast');
  t.classList.add('show');
  setTimeout(() => t.classList.remove('show'), 3000);
}

// ══════════════════════════════════════════════════════════
//  GROUP CHAT INPUT ENTER
// ══════════════════════════════════════════════════════════
document.getElementById('groupInput').addEventListener('keydown', e => {
  if (e.key === 'Enter') sendGroupMessage();
});

// ══════════════════════════════════════════════════════════
//  AUTO OFFICE ACTIVITY (simulate employees working)
// ══════════════════════════════════════════════════════════
const autoActivities = [
  {emp:'dev', msg:'New client ka landing page 60% complete 💻'},
  {emp:'alex', msg:'Client response aaya - project approved! 🎉'},
  {emp:'qc', msg:'Testing complete, 1 minor bug mila 🔍'},
  {emp:'meera', msg:'Payment received: ₹25,000 💰'},
  {emp:'report', msg:'Weekly KPIs updated 📈'},
  {emp:'deliver', msg:'Project successfully delivered ✅'},
  {emp:'support', msg:'Client ticket #42 resolved 🎧'},
];

let autoIdx = 0;
setInterval(() => {
  const a = autoActivities[autoIdx % autoActivities.length];
  addActivity(a.emp, a.msg, 'done');
  autoIdx++;

  // Also send to group chat sometimes
  if (autoIdx % 3 === 0) {
    const emp = employees.find(e => e.id === a.emp);
    if (emp) {
      const time = new Date().toLocaleTimeString('en-IN',{hour:'2-digit',minute:'2-digit'});
      groupMessages.push({emp: a.emp, text: a.msg, time});
      if (currentView === 'groupchat') appendGroupMsg(a.emp, a.msg, time, false);
      else {
        groupUnread++;
        document.getElementById('chatBadge').textContent = groupUnread;
      }
    }
  }
}, 12000);

// ══════════════════════════════════════════════════════════
//  START
// ══════════════════════════════════════════════════════════
init();
</script>
</body>
</html>
