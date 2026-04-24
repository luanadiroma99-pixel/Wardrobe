<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Sudoku Wardrobe</title>
<style>
  * { box-sizing: border-box; margin: 0; padding: 0; }

  body {
    font-family: 'Helvetica Neue', Arial, sans-serif;
    background: #f9f7f5;
    color: #1a1a1a;
    min-height: 100vh;
  }

  header {
    padding: 32px 40px 20px;
    border-bottom: 1px solid #e0ddd9;
    display: flex;
    align-items: baseline;
    gap: 16px;
  }

  header h1 {
    font-size: 22px;
    font-weight: 300;
    letter-spacing: 0.15em;
    text-transform: uppercase;
  }

  header span {
    font-size: 12px;
    color: #999;
    letter-spacing: 0.08em;
  }

  .app {
    max-width: 1100px;
    margin: 0 auto;
    padding: 40px;
    display: grid;
    grid-template-columns: 320px 1fr;
    gap: 48px;
  }

  /* ─── LEFT PANEL : dressing ─── */
  .panel-title {
    font-size: 10px;
    letter-spacing: 0.18em;
    text-transform: uppercase;
    color: #999;
    margin-bottom: 20px;
  }

  .category {
    margin-bottom: 28px;
  }

  .category-label {
    font-size: 11px;
    letter-spacing: 0.12em;
    text-transform: uppercase;
    color: #555;
    margin-bottom: 10px;
    display: flex;
    align-items: center;
    gap: 8px;
  }

  .category-label::after {
    content: '';
    flex: 1;
    height: 1px;
    background: #e0ddd9;
  }

  .items-row {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
  }

  .item-thumb {
    width: 72px;
    height: 90px;
    border-radius: 6px;
    object-fit: cover;
    border: 2px solid transparent;
    cursor: pointer;
    transition: border-color 0.15s, transform 0.15s;
    background: #eee;
  }

  .item-thumb:hover { transform: scale(1.04); }
  .item-thumb.selected { border-color: #1a1a1a; }

  .upload-btn {
    width: 72px;
    height: 90px;
    border-radius: 6px;
    border: 1.5px dashed #ccc;
    background: none;
    cursor: pointer;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    gap: 4px;
    color: #aaa;
    font-size: 10px;
    letter-spacing: 0.06em;
    transition: border-color 0.15s, color 0.15s;
  }

  .upload-btn:hover { border-color: #888; color: #555; }

  .upload-btn svg { width: 18px; height: 18px; }

  /* ─── RIGHT PANEL : grille + combos ─── */
  .right-panel {}

  .section-tabs {
    display: flex;
    gap: 0;
    margin-bottom: 28px;
    border-bottom: 1px solid #e0ddd9;
  }

  .tab {
    padding: 10px 20px;
    font-size: 11px;
    letter-spacing: 0.12em;
    text-transform: uppercase;
    cursor: pointer;
    color: #999;
    border-bottom: 2px solid transparent;
    margin-bottom: -1px;
    background: none;
    border-top: none;
    border-left: none;
    border-right: none;
    transition: color 0.15s;
  }

  .tab.active { color: #1a1a1a; border-bottom-color: #1a1a1a; }

  /* ─── GRILLE ─── */
  .grid-section { display: none; }
  .grid-section.active { display: block; }

  .sudoku-grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 12px;
    margin-bottom: 24px;
  }

  .grid-cell {
    aspect-ratio: 2/3;
    border-radius: 8px;
    border: 1.5px solid #e0ddd9;
    background: #fff;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    gap: 6px;
    font-size: 10px;
    letter-spacing: 0.08em;
    text-transform: uppercase;
    color: #bbb;
    overflow: hidden;
    position: relative;
    transition: border-color 0.2s;
  }

  .grid-cell img {
    width: 100%;
    height: 100%;
    object-fit: cover;
    position: absolute;
    inset: 0;
    border-radius: 6px;
  }

  .grid-cell .cell-label {
    position: absolute;
    bottom: 6px;
    left: 0; right: 0;
    text-align: center;
    font-size: 9px;
    letter-spacing: 0.1em;
    text-transform: uppercase;
    background: rgba(255,255,255,0.85);
    padding: 3px 0;
    color: #444;
  }

  .cell-type-badge {
    position: absolute;
    top: 6px;
    left: 6px;
    font-size: 8px;
    letter-spacing: 0.08em;
    text-transform: uppercase;
    background: rgba(0,0,0,0.6);
    color: #fff;
    padding: 2px 5px;
    border-radius: 3px;
  }

  .grid-actions {
    display: flex;
    gap: 10px;
  }

  .btn {
    padding: 10px 22px;
    font-size: 10px;
    letter-spacing: 0.14em;
    text-transform: uppercase;
    border-radius: 4px;
    cursor: pointer;
    transition: background 0.15s, color 0.15s;
    border: 1.5px solid #1a1a1a;
  }

  .btn-primary { background: #1a1a1a; color: #fff; }
  .btn-primary:hover { background: #333; }
  .btn-secondary { background: transparent; color: #1a1a1a; }
  .btn-secondary:hover { background: #f0ede9; }

  /* ─── COMBOS ─── */
  .combos-section { display: none; }
  .combos-section.active { display: block; }

  .combos-intro {
    font-size: 12px;
    color: #888;
    margin-bottom: 24px;
    line-height: 1.6;
  }

  .combo-group { margin-bottom: 32px; }

  .combo-group-title {
    font-size: 10px;
    letter-spacing: 0.16em;
    text-transform: uppercase;
    color: #bbb;
    margin-bottom: 14px;
  }

  .combos-list {
    display: flex;
    flex-direction: column;
    gap: 10px;
  }

  .combo-card {
    display: flex;
    align-items: center;
    gap: 12px;
    padding: 12px 16px;
    background: #fff;
    border-radius: 8px;
    border: 1px solid #e8e5e1;
    cursor: pointer;
    transition: border-color 0.15s, box-shadow 0.15s;
  }

  .combo-card:hover {
    border-color: #bbb;
    box-shadow: 0 2px 8px rgba(0,0,0,0.06);
  }

  .combo-card.highlighted {
    border-color: #1a1a1a;
    box-shadow: 0 2px 10px rgba(0,0,0,0.1);
  }

  .combo-imgs {
    display: flex;
    gap: 6px;
  }

  .combo-img {
    width: 44px;
    height: 56px;
    border-radius: 4px;
    object-fit: cover;
    background: #eee;
    border: 1px solid #e0ddd9;
  }

  .combo-img.empty {
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 16px;
    color: #ccc;
  }

  .combo-info {
    flex: 1;
  }

  .combo-name {
    font-size: 11px;
    letter-spacing: 0.1em;
    text-transform: uppercase;
    color: #1a1a1a;
    margin-bottom: 4px;
  }

  .combo-desc {
    font-size: 10px;
    color: #aaa;
    letter-spacing: 0.04em;
  }

  .combo-badge {
    font-size: 9px;
    letter-spacing: 0.1em;
    text-transform: uppercase;
    padding: 3px 8px;
    border-radius: 20px;
    background: #f0ede9;
    color: #888;
  }

  .empty-state {
    text-align: center;
    padding: 60px 20px;
    color: #bbb;
  }

  .empty-state p {
    font-size: 12px;
    letter-spacing: 0.08em;
    line-height: 1.8;
  }

  .item-name-input {
    width: 72px;
    font-size: 9px;
    text-align: center;
    border: none;
    border-top: 1px solid #e0ddd9;
    padding: 3px 2px;
    background: transparent;
    color: #555;
    outline: none;
    letter-spacing: 0.04em;
  }

  .item-wrapper {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 0;
  }

  .delete-btn {
    position: absolute;
    top: 3px;
    right: 3px;
    width: 16px;
    height: 16px;
    border-radius: 50%;
    background: rgba(0,0,0,0.5);
    color: white;
    border: none;
    cursor: pointer;
    font-size: 10px;
    display: flex;
    align-items: center;
    justify-content: center;
    opacity: 0;
    transition: opacity 0.15s;
  }

  .item-wrapper:hover .delete-btn { opacity: 1; }

  .item-wrapper { position: relative; }

</style>
</head>
<body>

<header>
  <h1>Sudoku Wardrobe</h1>
  <span>Méthode sudoku — 9 pièces, infinies combinaisons</span>
</header>

<div class="app">

  <!-- LEFT: Dressing -->
  <aside>
    <p class="panel-title">Mon Dressing</p>

    <div class="category" id="cat-top">
      <div class="category-label">Top</div>
      <div class="items-row" id="items-top">
        <label class="upload-btn" title="Ajouter un top">
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5"><line x1="12" y1="5" x2="12" y2="19"/><line x1="5" y1="12" x2="19" y2="12"/></svg>
          Ajouter
          <input type="file" accept="image/*" style="display:none" onchange="addItem('top', this)">
        </label>
      </div>
    </div>

    <div class="category" id="cat-layer">
      <div class="category-label">Layer</div>
      <div class="items-row" id="items-layer">
        <label class="upload-btn" title="Ajouter un layer">
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5"><line x1="12" y1="5" x2="12" y2="19"/><line x1="5" y1="12" x2="19" y2="12"/></svg>
          Ajouter
          <input type="file" accept="image/*" style="display:none" onchange="addItem('layer', this)">
        </label>
      </div>
    </div>

    <div class="category" id="cat-pantalon">
      <div class="category-label">Pantalon</div>
      <div class="items-row" id="items-pantalon">
        <label class="upload-btn" title="Ajouter un pantalon">
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5"><line x1="12" y1="5" x2="12" y2="19"/><line x1="5" y1="12" x2="19" y2="12"/></svg>
          Ajouter
          <input type="file" accept="image/*" style="display:none" onchange="addItem('pantalon', this)">
        </label>
      </div>
    </div>
  </aside>

  <!-- RIGHT: Grid + Combos -->
  <main>
    <div class="section-tabs">
      <button class="tab active" onclick="showTab('grid')">Grille</button>
      <button class="tab" onclick="showTab('combos')">Combinaisons</button>
    </div>

    <!-- GRID TAB -->
    <div class="grid-section active" id="tab-grid">
      <div class="sudoku-grid" id="sudoku-grid">
        <!-- filled by JS -->
      </div>
      <div class="grid-actions">
        <button class="btn btn-primary" onclick="generateGrid()">Générer la grille</button>
        <button class="btn btn-secondary" onclick="showTab('combos')">Voir les combos →</button>
      </div>
    </div>

    <!-- COMBOS TAB -->
    <div class="combos-section" id="tab-combos">
      <p class="combos-intro">Chaque carte représente un outfit complet (Top + Layer + Pantalon).<br>Cliquez sur une carte pour la mettre en avant.</p>
      <div id="combos-container"></div>
    </div>

  </main>
</div>

<script>
// ── State ──────────────────────────────────────────────
const wardrobe = { top: [], layer: [], pantalon: [] };
let grid = null; // 3x3 array of {type, id}

const TYPES = ['top', 'layer', 'pantalon'];
const TYPE_LABELS = { top: 'Top', layer: 'Layer', pantalon: 'Pantalon' };

// Sudoku patterns (3 valid arrangements of [0,1,2] so each row+col has all 3)
const SUDOKU_LAYOUTS = [
  // layout[row][col] = type index (0=top,1=layer,2=pantalon)
  [[0,1,2],[1,2,0],[2,0,1]],
  [[0,2,1],[2,1,0],[1,0,2]],
];

// ── Add item ───────────────────────────────────────────
function addItem(type, input) {
  const file = input.files[0];
  if (!file) return;
  const reader = new FileReader();
  reader.onload = e => {
    const id = Date.now() + Math.random();
    const name = file.name.replace(/\.[^.]+$/, '').slice(0, 14);
    wardrobe[type].push({ id, src: e.target.result, name });
    renderDressing(type);
    input.value = '';
  };
  reader.readAsDataURL(file);
}

function deleteItem(type, id) {
  wardrobe[type] = wardrobe[type].filter(i => i.id !== id);
  renderDressing(type);
  grid = null;
  renderGrid();
}

function renderDressing(type) {
  const container = document.getElementById('items-' + type);
  const items = wardrobe[type];

  // keep upload button
  const uploadBtn = container.querySelector('label.upload-btn');

  // clear non-upload children
  [...container.children].forEach(c => { if (!c.classList.contains('upload-btn')) c.remove(); });

  items.forEach(item => {
    const wrap = document.createElement('div');
    wrap.className = 'item-wrapper';
    wrap.innerHTML = `
      <img src="${item.src}" class="item-thumb" title="${item.name}">
      <button class="delete-btn" onclick="deleteItem('${type}', ${item.id})" title="Supprimer">✕</button>
      <input class="item-name-input" value="${item.name}" onchange="renameItem('${type}', ${item.id}, this.value)">
    `;
    container.insertBefore(wrap, uploadBtn);
  });
}

function renameItem(type, id, name) {
  const item = wardrobe[type].find(i => i.id === id);
  if (item) item.name = name;
}

// ── Grid ───────────────────────────────────────────────
function generateGrid() {
  const missing = TYPES.filter(t => wardrobe[t].length === 0);
  if (missing.length > 0) {
    alert('Ajoute au moins 1 ' + missing.map(t => TYPE_LABELS[t]).join(', ') + ' dans ton dressing !');
    return;
  }

  // Pick a random sudoku layout
  const layout = SUDOKU_LAYOUTS[Math.floor(Math.random() * SUDOKU_LAYOUTS.length)];

  // For each type, pick 3 items (with repetition if < 3)
  const picks = {};
  TYPES.forEach(t => {
    const pool = wardrobe[t];
    const chosen = [];
    for (let i = 0; i < 3; i++) chosen.push(pool[i % pool.length]);
    // shuffle
    chosen.sort(() => Math.random() - 0.5);
    picks[t] = chosen;
  });

  // Build 3x3 grid
  grid = [];
  for (let r = 0; r < 3; r++) {
    grid[r] = [];
    for (let c = 0; c < 3; c++) {
      const typeIdx = layout[r][c];
      const type = TYPES[typeIdx];
      // index within picks[type] based on how many times this type appeared so far in the layout
      let count = 0;
      for (let pr = 0; pr < r; pr++) for (let pc = 0; pc < 3; pc++) if (layout[pr][pc] === typeIdx) count++;
      for (let pc = 0; pc < c; pc++) if (layout[r][pc] === typeIdx) count++;
      grid[r][c] = { type, item: picks[type][count] };
    }
  }

  renderGrid();
  renderCombos();
}

function renderGrid() {
  const container = document.getElementById('sudoku-grid');
  container.innerHTML = '';

  if (!grid) {
    // empty placeholder
    for (let i = 0; i < 9; i++) {
      const cell = document.createElement('div');
      cell.className = 'grid-cell';
      const types = ['Top','Layer','Pantalon'];
      cell.innerHTML = `<span style="font-size:9px;letter-spacing:0.1em;color:#ddd">${types[i % 3]}</span>`;
      container.appendChild(cell);
    }
    return;
  }

  for (let r = 0; r < 3; r++) {
    for (let c = 0; c < 3; c++) {
      const { type, item } = grid[r][c];
      const cell = document.createElement('div');
      cell.className = 'grid-cell';
      cell.innerHTML = `
        <img src="${item.src}" alt="${item.name}">
        <span class="cell-type-badge">${TYPE_LABELS[type]}</span>
        <span class="cell-label">${item.name}</span>
      `;
      container.appendChild(cell);
    }
  }
}

// ── Combos ─────────────────────────────────────────────
function getOutfit(cells) {
  // cells: array of {type, item}
  // An outfit is valid if it has exactly one top, one layer, one pantalon
  const types = cells.map(c => c.type);
  return TYPES.every(t => types.includes(t)) ? cells : null;
}

function buildCombos() {
  if (!grid) return [];
  const flat = [];
  for (let r = 0; r < 3; r++) for (let c = 0; c < 3; c++) flat.push({ r, c, ...grid[r][c] });

  const candidates = [];

  // Rows
  for (let r = 0; r < 3; r++) candidates.push({ name: `Ligne ${r+1}`, cells: [grid[r][0], grid[r][1], grid[r][2]] });

  // Columns
  for (let c = 0; c < 3; c++) candidates.push({ name: `Colonne ${c+1}`, cells: [grid[0][c], grid[1][c], grid[2][c]] });

  // Diagonals
  candidates.push({ name: 'Diagonale ↘', cells: [grid[0][0], grid[1][1], grid[2][2]] });
  candidates.push({ name: 'Diagonale ↙', cells: [grid[0][2], grid[1][1], grid[2][0]] });

  // L-shapes and other 3-combos from corners + center
  const corners = [[0,0],[0,2],[2,0],[2,2]];
  const center = grid[1][1];
  corners.forEach(([r,c]) => {
    candidates.push({ name: `Coin (${r},${c}) + Centre`, cells: [grid[r][c], center, grid[2-r][2-c]] });
  });

  // Filter valid outfits
  const outfits = [];
  const seen = new Set();
  candidates.forEach(({ name, cells }) => {
    const outfit = getOutfit(cells);
    if (!outfit) return;
    const key = TYPES.map(t => outfit.find(c => c.type === t).item.id).join('-');
    if (seen.has(key)) return;
    seen.add(key);
    outfits.push({ name, cells: outfit });
  });

  return outfits;
}

function renderCombos() {
  const container = document.getElementById('combos-container');
  const outfits = buildCombos();

  if (outfits.length === 0) {
    container.innerHTML = `<div class="empty-state"><p>Génère la grille d'abord<br>pour voir tes combinaisons.</p></div>`;
    return;
  }

  // Group by type
  const rows = outfits.filter(o => o.name.startsWith('Ligne'));
  const cols = outfits.filter(o => o.name.startsWith('Colonne'));
  const diags = outfits.filter(o => o.name.startsWith('Diagonale'));
  const others = outfits.filter(o => !rows.includes(o) && !cols.includes(o) && !diags.includes(o));

  const groups = [
    { label: 'Lectures horizontales', items: rows },
    { label: 'Lectures verticales', items: cols },
    { label: 'Diagonales', items: diags },
    { label: 'Autres combinaisons', items: others },
  ].filter(g => g.items.length > 0);

  container.innerHTML = groups.map(g => `
    <div class="combo-group">
      <div class="combo-group-title">${g.label}</div>
      <div class="combos-list">
        ${g.items.map(outfit => {
          const byType = {};
          outfit.cells.forEach(c => byType[c.type] = c.item);
          return `
            <div class="combo-card" onclick="this.classList.toggle('highlighted')">
              <div class="combo-imgs">
                ${TYPES.map(t => byType[t]
                  ? `<img class="combo-img" src="${byType[t].src}" title="${byType[t].name}">`
                  : `<div class="combo-img empty">?</div>`
                ).join('')}
              </div>
              <div class="combo-info">
                <div class="combo-name">${outfit.name}</div>
                <div class="combo-desc">${TYPES.map(t => byType[t] ? byType[t].name : '—').join(' · ')}</div>
              </div>
              <span class="combo-badge">Outfit</span>
            </div>
          `;
        }).join('')}
      </div>
    </div>
  `).join('');
}

// ── Tabs ───────────────────────────────────────────────
function showTab(name) {
  document.querySelectorAll('.tab').forEach((t, i) => {
    t.classList.toggle('active', ['grid','combos'][i] === name);
  });
  document.getElementById('tab-grid').classList.toggle('active', name === 'grid');
  document.getElementById('tab-combos').classList.toggle('active', name === 'combos');
}

// Init
renderGrid();
renderCombos();
</script>
</body>
</html>
