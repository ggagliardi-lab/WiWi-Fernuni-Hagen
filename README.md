<!DOCTYPE html>
<html lang="de">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>FernUni WiWi Companion</title>
  <style>
    :root {
      --bg-mobile: #f4f5f8;
      --card-bg: #ffffff;
      --primary: #005A9C; /* FernUni Blau */
      --primary-light: #e6f0fa;
      --accent-green: #34c759;
      --accent-red: #ff3b30;
      --text: #1c1c1e;
      --text-muted: #8e8e93;
      --border: #e5e5ea;
    }

    * { box-sizing: border-box; margin: 0; padding: 0; font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif; }

    body {
      background-color: #121212;
      display: flex;
      justify-content: center;
      align-items: center;
      min-height: 100vh;
      padding: 10px;
    }

    .phone-container {
      width: 100%;
      max-width: 390px;
      height: 780px;
      background-color: var(--bg-mobile);
      border-radius: 36px;
      border: 8px solid #2d2d2d;
      box-shadow: 0 20px 40px rgba(0,0,0,0.5);
      display: flex;
      flex-direction: column;
      overflow: hidden;
    }

    .status-bar {
      height: 40px;
      background: var(--card-bg);
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 0 20px;
      font-size: 11px;
      font-weight: 700;
      color: var(--primary);
      border-bottom: 1px solid var(--border);
    }

    .app-header {
      background: var(--card-bg);
      padding: 10px 16px;
      border-bottom: 1px solid var(--border);
      text-align: center;
    }
    .app-header h1 { font-size: 16px; color: var(--primary); }

    .content {
      flex: 1;
      overflow-y: auto;
      padding: 12px;
      display: none;
    }
    .content.active { display: block; }

    .card {
      background: var(--card-bg);
      border-radius: 14px;
      padding: 14px;
      margin-bottom: 12px;
      box-shadow: 0 2px 6px rgba(0,0,0,0.04);
      border: 1px solid var(--border);
    }
    .card h2 { font-size: 14px; margin-bottom: 10px; color: var(--text); }

    .progress-bar-bg { background: #e5e5ea; height: 8px; border-radius: 4px; overflow: hidden; margin: 8px 0; }
    .progress-bar-fill { background: var(--primary); height: 100%; width: 0%; transition: width 0.4s; }

    .item-row { display: flex; justify-content: space-between; align-items: center; padding: 10px 0; border-bottom: 1px solid var(--border); font-size: 12px; }
    .item-row:last-child { border-bottom: none; }

    input, select {
      width: 100%;
      padding: 8px 10px;
      border: 1px solid var(--border);
      border-radius: 8px;
      font-size: 12px;
      margin-bottom: 6px;
      background: #fafafa;
      outline: none;
    }
    .btn-add {
      background: var(--primary);
      color: white;
      border: none;
      padding: 8px 12px;
      border-radius: 8px;
      font-size: 12px;
      font-weight: 600;
      cursor: pointer;
      width: 100%;
    }
    .btn-del {
      background: #ffe5e5;
      border: 1px solid #ffcccc;
      color: var(--accent-red);
      font-size: 13px;
      cursor: pointer;
      padding: 6px 10px;
      border-radius: 6px;
      font-weight: bold;
    }
    .btn-del:hover { background: #ffcccc; }

    /* Chat */
    .chat-box { height: 330px; overflow-y: auto; display: flex; flex-direction: column; gap: 8px; padding-bottom: 8px; }
    .chat-bubble { max-width: 88%; padding: 8px 12px; border-radius: 12px; font-size: 12px; line-height: 1.4; }
    .chat-bubble.ai { background: #e9e9eb; color: var(--text); align-self: flex-start; }
    .chat-bubble.user { background: var(--primary); color: white; align-self: flex-end; }
    .quick-prompts { display: flex; gap: 6px; overflow-x: auto; padding-bottom: 6px; margin-bottom: 6px; }
    .prompt-chip { background: var(--primary-light); color: var(--primary); padding: 5px 10px; border-radius: 12px; font-size: 10px; white-space: nowrap; cursor: pointer; border: none; font-weight: 600; }

    .tab-bar {
      height: 56px;
      background: var(--card-bg);
      border-top: 1px solid var(--border);
      display: flex;
      justify-content: space-around;
      align-items: center;
    }
    .tab-btn {
      background: none; border: none; color: var(--text-muted); font-size: 10px; display: flex; flex-direction: column; align-items: center; gap: 2px; cursor: pointer;
    }
    .tab-btn.active { color: var(--primary); font-weight: bold; }
    .tab-btn span { font-size: 16px; }
  </style>
</head>
<body>

<div class="phone-container">
  <div class="status-bar">
    <span>FernUni Hagen</span>
    <span>B.Sc. WiWi (Teilzeit)</span>
  </div>

  <div class="app-header">
    <h1>WiWi Study Companion</h1>
  </div>

  <!-- TAB 1: DASHBOARD -->
  <div id="tab-dashboard" class="content active">
    <div class="card">
      <h2>📊 Gesamtfortschritt (B.Sc. WiWi)</h2>
      <div style="font-size: 20px; font-weight: bold; color: var(--primary);" id="ectsTotalDisplay">0 / 180 ECTS</div>
      <div class="progress-bar-bg"><div class="progress-bar-fill" id="ectsProgressBar"></div></div>
      <p style="font-size: 11px; color: var(--text-muted);" id="gpaDisplay">Notenschnitt: -</p>
    </div>

    <div class="card">
      <h2>⏳ FernUni Termine & Fristen</h2>
      <div id="deadlineList"></div>
      
      <div style="margin-top: 10px; border-top: 1px solid var(--border); padding-top: 8px;">
        <input type="text" id="dlTitle" placeholder="Bezeichnung (z.B. EA 1 Abgabe Moodle)">
        <input type="date" id="dlDate">
        <button class="btn-add" onclick="addDeadline()">Termin hinzufügen</button>
      </div>
    </div>
  </div>

  <!-- TAB 2: MODULE -->
  <div id="tab-module" class="content">
    <div class="card">
      <h2>📚 Meine Module (FernUni Hagen)</h2>
      <p style="font-size:10px; color:var(--text-muted); margin-bottom:8px;">Verwalte deine belegten Kurse.</p>
      
      <div id="moduleList"></div>

      <div style="margin-top: 12px; border-top: 1px solid var(--border); padding-top: 10px;">
        <h3 style="font-size: 12px; margin-bottom: 6px;">Modul aus Katalog wählen:</h3>
        
        <select id="mSelect">
          <!-- Dynamisch per JS befüllt mit echten FernUni Modulen -->
        </select>

        <div style="display: flex; gap: 6px; margin-top:4px;">
          <select id="mStatus" onchange="toggleGradeInput()">
            <option value="In Bearbeitung">In Bearbeitung</option>
            <option value="Bestanden">Bestanden</option>
            <option value="Geplant">Geplant</option>
          </select>
        </div>
        <input type="number" id="mGrade" placeholder="Note (z. B. 1.7)" step="0.1" style="display:none;">
        <button class="btn-add" onclick="addModuleFromCatalog()" style="margin-top: 6px;">Modul hinzufügen</button>
      </div>
    </div>
  </div>

  <!-- TAB 3: DYNAMISCHER LERNPLAN -->
  <div id="tab-plan" class="content">
    <div class="card">
      <h2>🗓️ FernUni Semester-Ablauf</h2>
      <p style="font-size: 11px; color: var(--text-muted); margin-bottom: 10px;">Individueller Meilenstein-Plan für deine aktiven Module.</p>
      
      <div id="aiScheduleContainer"></div>
    </div>
  </div>

  <!-- TAB 4: KI TUTOR -->
  <div id="tab-ai" class="content">
    <div class="card" style="margin-bottom: 0;">
      <h2>🤖 FernUni KI-Tutor</h2>
      <div class="quick-prompts">
        <button class="prompt-chip" onclick="quickPrompt('⚡ Erstelle mir einen Semesterplan für meine Kurse')">⚡ Lernplan</button>
        <button class="prompt-chip" onclick="quickPrompt('📅 Welche Prüfungs-Anmeldefristen gibt es?')">📅 Anmeldefristen</button>
        <button class="prompt-chip" onclick="quickPrompt('📝 Wie funktionieren Einsendearbeiten?')">📝 Einsendearbeiten</button>
        <button class="prompt-chip" onclick="quickPrompt('Erkläre mir das Ökonomische Prinzip')">Ökonomisches Prinzip</button>
      </div>
      <div class="chat-box" id="chatBox">
        <div class="chat-bubble ai">Hallo! Ich bin dein FernUni Hagen WiWi-Tutor. Ich kenne deinen Studiengang, Fristen, Module und Kursinhalte. Wie kann ich dir helfen?</div>
      </div>
      <div style="display: flex; gap: 6px; margin-top: 6px;">
        <input type="text" id="chatInput" placeholder="Frage eingeben..." style="margin-bottom:0;" onkeypress="if(event.key==='Enter') sendMsg()">
        <button class="btn-add" style="width: 40px;" onclick="sendMsg()">➔</button>
      </div>
    </div>
  </div>

  <!-- NAVIGATION -->
  <div class="tab-bar">
    <button class="tab-btn active" onclick="switchTab('dashboard', this)"><span>🏠</span> Home</button>
    <button class="tab-btn" onclick="switchTab('module', this)"><span>📖</span> Module</button>
    <button class="tab-btn" onclick="switchTab('plan', this)"><span>🗓️</span> Lernplan</button>
    <button class="tab-btn" onclick="switchTab('ai', this)"><span>🤖</span> KI-Tutor</button>
  </div>
</div>

<script>
  // FernUni Hagen WiWi Modulkatalog (Pflicht & Auswahl Wahlpflicht)
  const fernUniCatalog = [
    { code: "31001", name: "Einführung in die Wirtschaftswissenschaft", ects: 10, type: "Pflicht" },
    { code: "31011", name: "Externes Rechnungswesen", ects: 10, type: "Pflicht" },
    { code: "31021", name: "Investition und Finanzierung", ects: 10, type: "Pflicht" },
    { code: "31031", name: "Internes Rechnungswesen & Funktionale Steuerung", ects: 10, type: "Pflicht" },
    { code: "31041", name: "Mikroökonomik", ects: 10, type: "Pflicht" },
    { code: "31051", name: "Makroökonomik", ects: 10, type: "Pflicht" },
    { code: "31061", name: "Grundlagen des Privat- und Wirtschaftsrechts", ects: 10, type: "Pflicht" },
    { code: "31071", name: "Einführung in die Wirtschaftsinformatik", ects: 10, type: "Pflicht" },
    { code: "31101", name: "Grundlagen der Wirtschaftsmathematik & Statistik", ects: 10, type: "Pflicht" },
    { code: "31102", name: "Unternehmensführung", ects: 10, type: "Pflicht" },
    { code: "31621", name: "Grundlagen des Marketing", ects: 10, type: "Wahlpflicht" },
    { code: "31501", name: "Finanzwirtschaft", ects: 10, type: "Wahlpflicht" },
    { code: "31701", name: "Personalführung", ects: 10, type: "Wahlpflicht" },
    { code: "31671", name: "Strategisches Management", ects: 10, type: "Wahlpflicht" },
    { code: "31171", name: "Überfachliche Kompetenzen", ects: 10, type: "Pflicht" }
  ];

  // Vorbelegte Beispiel-Daten des Studierenden
  let modules = [
    { id: 101, name: "31001: Einführung in die WiWi", ects: 10, status: "Bestanden", grade: 1.7 },
    { id: 102, name: "31011: Externes Rechnungswesen", ects: 10, status: "In Bearbeitung", grade: null },
    { id: 103, name: "31101: Wirtschaftsmathe & Statistik", ects: 10, status: "In Bearbeitung", grade: null }
  ];

  let deadlines = [
    { id: 201, title: "Anmeldezeitraum A2 (Klausuren)", date: "2026-08-31" },
    { id: 202, title: "EA 1 Abgabe (Moodle)", date: "2026-06-15" },
    { id: 203, title: "Klausur 31011 Externes RW", date: "2026-09-08" }
  ];

  function populateCatalogDropdown() {
    const select = document.getElementById('mSelect');
    select.innerHTML = '';
    fernUniCatalog.forEach(m => {
      const opt = document.createElement('option');
      opt.value = JSON.stringify(m);
      opt.innerText = `${m.code}: ${m.name} (${m.ects} ECTS - ${m.type})`;
      select.appendChild(opt);
    });
  }

  window.deleteModule = function(id) {
    modules = modules.filter(m => m.id !== id);
    renderModules();
  };

  window.deleteDeadline = function(id) {
    deadlines = deadlines.filter(d => d.id !== id);
    renderDeadlines();
  };

  function renderModules() {
    const list = document.getElementById('moduleList');
    list.innerHTML = '';
    let totalEcts = 0;
    let weightedSum = 0;
    let gradedEcts = 0;

    if(modules.length === 0) {
      list.innerHTML = '<p style="font-size:12px; color:var(--text-muted); text-align:center; padding:15px 0;">Keine Module ausgewählt.</p>';
    }

    modules.forEach(m => {
      if(m.status === 'Bestanden') {
        totalEcts += m.ects;
        if(m.grade) {
          weightedSum += m.grade * m.ects;
          gradedEcts += m.ects;
        }
      }

      const item = document.createElement('div');
      item.className = 'item-row';
      item.innerHTML = `
        <div>
          <strong>${m.name}</strong> (${m.ects} ECTS)<br>
          <small style="color:${m.status === 'Bestanden' ? 'var(--accent-green)' : 'var(--primary)'}">${m.status} ${m.grade ? '('+m.grade+')' : ''}</small>
        </div>
        <button class="btn-del" onclick="deleteModule(${m.id})">🗑️</button>
      `;
      list.appendChild(item);
    });

    document.getElementById('ectsTotalDisplay').innerText = `${totalEcts} / 180 ECTS`;
    document.getElementById('ectsProgressBar').style.width = `${Math.min((totalEcts / 180) * 100, 100)}%`;
    
    const gpa = gradedEcts > 0 ? (weightedSum / gradedEcts).toFixed(2) : "-";
    document.getElementById('gpaDisplay').innerText = `Notenschnitt: ${gpa}`;

    generateAISchedule();
  }

  function generateAISchedule() {
    const container = document.getElementById('aiScheduleContainer');
    const activeMods = modules.filter(m => m.status === 'In Bearbeitung');

    if(activeMods.length === 0) {
      container.innerHTML = `
        <p style="font-size:12px; color:var(--text-muted); text-align:center; padding:12px;">
          Keine aktiven Module ("In Bearbeitung") vorhanden. Setze im Tab "Module" einen Kurs auf "In Bearbeitung".
        </p>
      `;
      return;
    }

    let html = `<div style="font-size:11px; color:var(--primary); font-weight:bold; margin-bottom:8px;">⚡ Standard FernUni-Semesterplan (Teilzeit):</div>`;

    activeMods.forEach((mod) => {
      html += `
        <div style="background:#fafafa; border:1px solid var(--border); border-radius:8px; padding:10px; margin-bottom:10px;">
          <strong style="font-size:12px; color:var(--primary);">${mod.name}</strong>
          <div class="item-row" style="padding:4px 0;"><label><input type="checkbox"> Mon 1-2: Kurseinheiten 1–3 durcharbeiten</label></div>
          <div class="item-row" style="padding:4px 0;"><label><input type="checkbox"> Mon 3: Einsendearbeit (EA) 1 in Moodle lösen</label></div>
          <div class="item-row" style="padding:4px 0;"><label><input type="checkbox"> Mon 4: Kurseinheiten 4–6 & EA 2 abgeben</label></div>
          <div class="item-row" style="padding:4px 0;"><label><input type="checkbox"> Mon 5: Prüfungsanmeldung über WebPrüfungsamt</label></div>
          <div class="item-row" style="padding:4px 0;"><label><input type="checkbox"> Mon 6: Altklausuren & Intensiv-Wiederholung</label></div>
        </div>
      `;
    });

    container.innerHTML = html;
  }

  function toggleGradeInput() {
    const status = document.getElementById('mStatus').value;
    document.getElementById('mGrade').style.display = status === 'Bestanden' ? 'block' : 'none';
  }

  function addModuleFromCatalog() {
    const raw = document.getElementById('mSelect').value;
    if(!raw) return;
    const item = JSON.parse(raw);
    const status = document.getElementById('mStatus').value;
    const gradeVal = parseFloat(document.getElementById('mGrade').value);

    // Duplikate verhindern
    if(modules.some(m => m.name.includes(item.code))) {
      alert("Dieses Modul hast du bereits hinzugefügt!");
      return;
    }

    modules.push({
      id: Date.now(),
      name: `${item.code}: ${item.name}`,
      ects: item.ects,
      status: status,
      grade: status === 'Bestanden' && !isNaN(gradeVal) ? gradeVal : null
    });

    renderModules();
  }

  function renderDeadlines() {
    const list = document.getElementById('deadlineList');
    list.innerHTML = '';
    deadlines.forEach(d => {
      const item = document.createElement('div');
      item.className = 'item-row';
      item.innerHTML = `
        <span><strong>${d.title}</strong><br><small style="color:var(--text-muted);">${d.date}</small></span>
        <button class="btn-del" onclick="deleteDeadline(${d.id})">🗑️</button>
      `;
      list.appendChild(item);
    });
  }

  function addDeadline() {
    const title = document.getElementById('dlTitle').value.trim();
    const date = document.getElementById('dlDate').value;
    if(title && date) {
      deadlines.push({ id: Date.now(), title, date });
      document.getElementById('dlTitle').value = '';
      renderDeadlines();
    }
  }

  function switchTab(tabId, btn) {
    document.querySelectorAll('.content').forEach(c => c.classList.remove('active'));
    document.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active'));
    document.getElementById('tab-' + tabId).classList.add('active');
    btn.classList.add('active');
  }

  function sendMsg() {
    const input = document.getElementById('chatInput');
    const text = input.value.trim();
    if(!text) return;
    quickPrompt(text);
    input.value = '';
  }

  function quickPrompt(text) {
    const box = document.getElementById('chatBox');
    box.innerHTML += `<div class="chat-bubble user">${text}</div>`;
    box.scrollTop = box.scrollHeight;

    setTimeout(() => {
      let reply = "";
      const lower = text.toLowerCase();
      const activeMods = modules.filter(m => m.status === 'In Bearbeitung');

      if(lower.includes("lehrplan") || lower.includes("semesterplan") || lower.includes("kurse")) {
        if(activeMods.length === 0) {
          reply = "⚠️ Du hast aktuell keine Module mit dem Status 'In Bearbeitung'. Füge im Tab 'Module' deine aktuellen FernUni-Kurse hinzu!";
        } else {
          reply = `📌 **FernUni-Lernplan für Teilzeit:**\n\n` +
                  `Aktive Module: **${activeMods.map(m => m.name).join(', ')}**\n\n` +
                  `• **Woche 1–6:** Kurseinheiten gründlich durcharbeiten & Skripte bearbeiten.\n` +
                  `• **Woche 7–10:** Moodle-Einsendearbeiten (EA) fristgerecht abgeben, um die Klausurzulassung zu sichern.\n` +
                  `• **Woche 11–12:** Prüfungsanmeldung A2 im WebPrüfungsamt nicht vergessen!\n` +
                  `• **Letzte 4 Wochen:** Fokus auf Altklausuren & Klausurschemata der FernUni Hagen.`;
        }
      } else if(lower.includes("frist") || lower.includes("anmeld") || lower.includes("termin")) {
        reply = "🗓️ **Wichtige FernUni Anmeldezeiträume:**\n\n" +
                "• **Anmeldezeitraum A1:** 15.11.–30.11. (WiSe) bzw. 15.05.–31.05. (SoSe) für mündliche Prüfungen & Portfolioprüfungen.\n" +
                "• **Anmeldezeitraum A2:** 15.02.–28./29.02. (WiSe) bzw. 15.08.–31.08. (SoSe) für reguläre Klausuren (Online/Präsenz).\n" +
                "• **Seminar-Anmeldung (S):** 01.01.–15.01. (SoSe) bzw. 01.07.–15.07. (WiSe).";
      } else if(lower.includes("einsendearbeit") || lower.includes("moodle")) {
        reply = "📝 **Einsendearbeiten (EA) an der FernUni:**\n\n" +
                "In den meisten Modulen (z.B. 31001, 31011) musst du mindestens **eine Einsendearbeit erfolgreich bestehen** (in Moodle), um die formale Klausurzulassung zu erlangen. Achte unbedingt auf die Abgabefristen in der Moodle-Lernumgebung!";
      } else if(lower.includes("ökonomisch") || lower.includes("prinzip")) {
        reply = "💡 **Das Ökonomische Prinzip (Grundlagen BWL):**\n\n" +
                "1. **Minimumprinzip:** Ein gegebenes Ziel mit minimalem Mitteleinsatz erreichen (z.B. 100 ECTS mit minimalem Zeitaufwand).\n" +
                "2. **Maximumprinzip:** Mit gegebenen Mitteln das maximale Ziel erreichen (z.B. mit 10 Std/Woche die bestmögliche Note erzielen).";
      } else {
        reply = `🤖 **FernUni WiWi-Tutor:** Ich helfe dir gerne bei Kursinhalten, Klausurvorbereitung oder Fristen zu "${text}". Frage mich z.B. nach den Pflichtmodulen oder Anmeldezeiträumen!`;
      }

      box.innerHTML += `<div class="chat-bubble ai">${reply.replace(/\n/g, '<br>')}</div>`;
      box.scrollTop = box.scrollHeight;
    }, 500);
  }

  // Initialisierung beim Laden
  populateCatalogDropdown();
  renderModules();
  renderDeadlines();
</script>
</body>
</html>
