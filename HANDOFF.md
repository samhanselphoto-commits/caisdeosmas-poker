# CaisDeosMasPoker — Handoff Notes (D:/CLAUDE/POKER/POKER)

> **Tóm tắt:** App tính tỉ lệ thắng poker Texas Hold'em, tiếng Việt, giao diện Neon Cosmic 2026, picker rank→suit, equity donut SVG, range 13×13 (toggle với position presets). Đã hoàn thành UI redesign, audit độ chính xác + 4 tối ưu hiệu năng + 17 task polish/feature + 6 task UX/layout. **Đã deploy lên Vercel với auto-deploy từ GitHub**.

> **Brand:** "CaisDeosMasPoker" (đổi từ "Poker Equity" ngày 2026-06-11). Cập nhật: title tab, topbar, share text.

---

## 0. Live URLs (2026-06-11)

- **Production**: https://caisdeosmas-poker.vercel.app
- **GitHub**: https://github.com/samhanselphoto-commits/caisdeosmas-poker
- **Vercel project**: `caisdeosmas-poker` (team `az-ing-s-projects`, projectId `prj_LA9GKUDK3ogECgQtercIgH0kZfLi`)
- **Auto-deploy**: mỗi `git push` lên `main` → Vercel build + deploy trong ~5-10s

### Quy trình update từ giờ
1. Tôi sửa code ở local
2. `git add . && git commit -m "msg"`
3. `git push` (cần GitHub PAT — session sau bạn paste lại, hoặc cài `gh auth login` trên máy)
4. Vercel tự detect push → build → deploy production

> **Lưu ý bảo mật**: PAT + Vercel token KHÔNG được lưu vào repo hay settings.local.json. Mỗi session bạn paste lại token nếu cần deploy. Hoặc tôi có thể hướng dẫn bạn cài `gh auth login` để token tự cache vĩnh viễn trong Git Credential Manager.

---

## 1. Trạng thái hiện tại (đã xong)

### Core
| # | Tính năng | Trạng thái |
|---|---|---|
| 1 | UI redesign "Neon Cosmic" 2026 (multi-radial gradient bg, glassmorphism, spring easing) | ✅ |
| 2 | Toàn bộ text Tiếng Việt | ✅ |
| 3 | Card picker flow mới: chọn **số (rank)** trước → hiện **4 chất** (suits) của số đó | ✅ |
| 4 | Thứ tự chất theo VN poker: **Cơ ♥ → Rô ♦ → Chuồn ♣ → Bích ♠** (`suitOrder = [1, 2, 3, 0]`) | ✅ |
| 5 | Theme tươi sáng neon như crypto casino (cyan, violet, hot pink, neon green) | ✅ |
| 6 | Card slot 5:7 aspect ratio, `max-width: 110px` | ✅ |
| 7 | Used cards trong picker cùng tone nền trắng với available cards, phân biệt bằng **viền cyan 2px + badge ✓** | ✅ |
| 8 | Range grid 13×13 (A→2), dùng shorthand `T` cho 10 | ✅ |
| 9 | Equity donut SVG với gradient + glow + `stroke-dashoffset` animation | ✅ |
| 10 | Outs analysis với mini-card hiển thị bài cụ thể | ✅ |
| 11 | Best hand display với highlight hero cards | ✅ |
| 12 | Pot odds bar so sánh EQ vs REQ | ✅ |
| 13 | Recommendation card: TỐ / THEO / BỎ với màu gradient tương ứng | ✅ |
| 14 | Bottom sheet picker với spring animation | ✅ |
| 15 | Preflop Monte Carlo có cache localStorage | ✅ |
| 16 | **C-1 fix:** Two-pair kicker dùng `ranks.find()` thay vì `counts[2].rank` (undefined) | ✅ |
| 17 | **H-2 fix:** Outs count cao hơn khi cùng rank (flush Q→A, sảnh 7→A) | ✅ |
| 18 | **H-1 fix:** Range fallback skip trial thay vì random — equity đúng với range hẹp | ✅ |
| 19 | **L-4 fix:** Pot/Call input clamp `Math.max(0, ...)` | ✅ |
| 20 | **L-6 fix:** Bỏ double-run equity ở init | ✅ |
| 21 | **Opt #1:** Hoist deck + in-place partial Fisher-Yates (2-3× speedup) | ✅ |
| 22 | **Opt #3:** Trials mặc định 2000 + chip "Thường/Cao" toggle (2× speedup) | ✅ |
| 23 | **A.1:** VN labels cho range grid (tooltip + legend) | ✅ |

### Equity analytics
| # | Tính năng | Trạng thái |
|---|---|---|
| 24 | **A.2:** Equity-by-street chart (mini SVG preflop→flop→turn→river, 4 điểm với % labels) | ✅ |
| 25 | **A.3:** Showdown % breakdown (phân bố rank khi hero thắng, chỉ postflop) | ✅ |
| 26 | **A.4:** Multi-board runout (tối đa 2 bộ board thử, equity trung bình) | ✅ |
| 27 | **D.5:** Tách "Thùng phá sảnh" (rank 8) ra khỏi "Thùng" trong outs | ✅ |

### Code quality & perf
| # | Tính năng | Trạng thái |
|---|---|---|
| 28 | **C.2 Opt #2:** Inline combinations cho C(6,5), C(7,5) (1.5-2× speedup) | ✅ |
| 29 | **C.3 Opt #4:** Allocation-free evaluate5 (single-pass, fixed array, in-place sort, 2-3× speedup) | ✅ |

### Polish & UX
| # | Tính năng | Trạng thái |
|---|---|---|
| 30 | **B.1:** Mobile keyboard close on Enter (pot/call inputs) | ✅ |
| 31 | **B.2:** First-time onboarding tooltip "Chạm vào ô bài để chọn" | ✅ |
| 32 | **B.3:** Save/restore full STATE (hero, board, pot, call, numOpps, range, precision, position, sb, bb, rangeView) | ✅ |
| 33 | **B.4:** Share/Export equity (nút share topbar, copy text) | ✅ |
| 34 | **B.5:** Undo/Redo (history 10 bước, 2 nút topbar) | ✅ |
| 35 | **B.6:** Haptic feedback mobile (`navigator.vibrate(10)` ở 6 tap points) | ✅ |
| 36 | **B.7:** Comment fix L-8 (clarify VN names ↔ SUITS index mapping) | ✅ |
| 37 | **B.1 layout:** Reorder sections (Số đối thủ + Vị trí lên cao, Phân tích chi tiết → Đề xuất) | ✅ |
| 38 | **B.2 glossary:** "?" tooltips cho Pot/EQ/REQ với inline popover | ✅ |
| 39 | **D.1:** Picker swipe gesture (vertical swipe để đổi rank) | ✅ |
| 40 | **D.2:** Safety nudge "⚠️ Bài yếu — cân nhắc bỏ" khi equity < 20% | ✅ |
| 41 | **D.3:** 4 sentiment tiers: great/decent/neutral/bad (D.3 polish) | ✅ |

### Position & Range
| # | Tính năng | Trạng thái |
|---|---|---|
| 42 | **B.4 position:** 9 chips (UTG/UTG+1/MP/LJ/HJ/CO/BTN/SB/BB) + SB/BB inputs | ✅ |
| 43 | **B.4 logic preflop:** So sánh hero hand với RFI range của vị trí | ✅ |
| 44 | **B.4 logic postflop:** Position multiplier (IP 0.95, LP 0.98, MP 1.05, EP 1.10, OOP 1.15) | ✅ |
| 45 | **B.5:** 9 position RFI presets (UTG 9% → BB defend 50%) | ✅ |
| 46 | **B.6:** Range view toggle "Theo vị trí" ↔ "Lưới 13×13" | ✅ |

### Tính năng nâng cao
| # | Tính năng | Trạng thái |
|---|---|---|
| 47 | **E.2:** EV calculator (Bỏ/Theo/Tố với raise 50% pot, class màu theo +/-, header "Giá trị kỳ vọng (EV)") | ✅ |

---

## 2. File & cấu trúc

**File chính:** [index.html](D:/CLAUDE/POKER/POKER/index.html) — 1 file self-contained (~3300 dòng, ~120KB).

- **CSS** (dòng 10–1320): Design tokens, layout, components, picker, animations, glossary popover, position chips, EV breakdown
- **HTML body** (dòng 1322–1324): `<div id="app">` render bằng JS
- **JS** (dòng 1325–3350+): Logic + render toàn bộ DOM
  - **1325–1360** Card/Suit/Constants + `buildAvailableDeck` + `partialShuffleInPlace` (Opt #1)
  - **1362–1455** Hand evaluator (evaluateBest inline 5/6/7 + evaluate5 allocation-free) + `handNameVN`
  - **1457–1525** Range analyzer (HAND_STRENGTH 169 hands + RANGE_PRESETS_BY_POS 9 vị trí)
  - **1527–1700** Equity calculator (3 modes: preflop exact, postflop MC, postflop MC w/ range, multi-board)
  - **1700–1790** Outs calculator (rank 8 tách riêng thành "Thùng phá sảnh")
  - **1790–1880** Position helpers (POSITION_RANK, POS_MULT, heroHandKey, isHandInPreset)
  - **1880–2030** App state + card pickers + position setters + glossary
  - **2030–2120** Equity render + scheduling + showdown breakdown + EV
  - **2120–2260** Pot odds + recommendation (position-aware preflop + postflop)
  - **2260–2350** Range grid (toggle preset ↔ grid view)
  - **2350–2700** Main `render()` + `init()` + DOM template

**Docs:**
- [HANDOFF.md](D:/CLAUDE/POKER/POKER/HANDOFF.md) — File này
- [DEPLOY.md](D:/CLAUDE/POKER/POKER/DEPLOY.md) — Hướng dẫn deploy Vercel + GitHub

**Dev server:** [`.claude/launch.json`](D:/CLAUDE/POKER/POKER/.claude/launch.json) → `python -m http.server 8000` (cổng 8000).

---

## 3. Thay đổi gần đây (2026-06-11 session)

### 🐛 Bug fixes (lịch sử + session mới)

| ID | Mức | Vấn đề | File:line | Fix |
|---|---|--------|-----------|-----|
| **C-1** | 🔴 CRITICAL | Two-pair kicker `counts[2].rank` = undefined → tính sai equity khi hero/opp có 2 đôi | [index.html:1255-1256](index.html#L1255) | `ranks.find(r => r !== hiPair && r !== loPair)` |
| **H-2** | 🟠 HIGH | `classifyOut` miss outs khi cùng rank (sảnh 7 cao → A cao, thùng Q → A) | [index.html:1521-1524](index.html#L1521) | Thêm 2 dòng check tiebreakers cho rank 4, 5 |
| **H-1** | 🟠 HIGH | Range sampling fail → fallback random hands → equity sai khi range hẹp | [index.html:1488-1492, 1510-1512](index.html#L1488) | Skip trial (`continue`) + normalize by valid count |
| **L-4** | ⚪ LOW | `parseFloat` chấp nhận số âm → pot/call âm | [index.html:1966-1972](index.html#L1966) | `Math.max(0, parseFloat(v) \|\| 0)` |
| **L-6** | ⚪ LOW | `init()` chạy 2 lần equity (một qua `applyAllRange`, một qua `runEquity` trực tiếp) | [index.html:2144-2148](index.html#L2144) | Bỏ `runEquity()` ở init, để scheduled calc lo |
| **L-8** | ⚪ LOW | Comment sai về mapping tên VN ↔ symbol | [index.html:1700](index.html#L1700) | Clarify "Cơ(♥=h) → Rô(♦=d) → Chuồn(♣=c) → Bích(♠=s)" + comment SUITS index |
| **D-5** | ⚪ LOW | `classifyOut` gộp straight-flush (rank 8) vào "Thùng" | [index.html:1655](index.html#L1655) | Tách riêng → "Thùng phá sảnh" |
| **EV polish** | ⚪ LOW | "$EV BỎ/THEO/TỐ +50%" — dấu $ thừa, viết hoa không đồng bộ | [index.html:3285-3289](index.html#L3285) | Header "Giá trị kỳ vọng (EV)" + "Bỏ/Theo/Tố" thường + sub "+50% pot" nhỏ |

### ⚡ Perf optimizations

| ID | Speedup | Effort | Thay đổi | File:line |
|---|---|---|---|---|
| **#1** | **2-3×** | 1h | `buildAvailableDeck(known)` + `partialShuffleInPlace(deck)` — build deck 1 lần ngoài loop, shuffle in-place không allocate | [index.html:1213-1233, 1409, 1429, 1453](index.html#L1213) |
| **#3** | **2×** | 30m | `STATE.trials` mặc định 2000 (giảm từ 4000) + chip "Thường/Cao" ở section "Tỉ lệ thắng" | [index.html:1552, 1974-1983, 1998-2007](index.html#L1552) |
| **C.2 #2** | **1.5-2×** | 30m | Inline combinations C(6,5)=6, C(7,5)=21 (không recursive) | [index.html:1556-1590](index.html#L1556) |
| **C.3 #4** | **2-3×** | 1h | Allocation-free evaluate5: single-pass, fixed count array, in-place insertion sort, no Object/Set | [index.html:1520-1596](index.html#L1520) |

**Stack tổng: ~10-15× từ baseline** cho case thường. Worst case 9 opps + Cao (4000 trials) vẫn ~150-300ms.

### 📊 Benchmark (Apple M-class, Chromium)

| Scenario | Thời gian |
|----------|-----------|
| Preflop 1v1, 2000 trials | **~163ms** |
| Postflop 1v1 + 3 board, 2000 trials | **~155ms** |
| Postflop 9 opps + 3 board + range=169, **Cao (4000 trials)** | **~291ms** |
| Postflop 9 opps + 3 board + range=169, **Thường (2000 trials)** | **~150ms** |
| 3 boards + 2000 trials total (split) | **~100ms** |

---

## 4. Layout hiện tại (8 sections, thứ tự từ trên xuống)

1. **Bài của bạn** — Hero 2 cards (tap để chọn)
2. **Bài chung** — Board 5 cards + 2 bộ extra boards (multi-board)
3. **Thiết lập bàn** — Số đối thủ (1-9) + 9 vị trí (UTG→BB) + SB/BB inputs
4. **Tỉ lệ thắng** — Equity donut + pills + chart preflop→river + showdown breakdown
5. **Phân tích chi tiết** — Outs analysis + Best hand display
6. **Đề xuất** — Rec card (TỐ/THEO/BỎ) + EV breakdown
7. **Khoảng bài đối thủ** — Toggle: Position presets ↔ Grid 13×13
8. **Tỉ lệ pot** — Pot/call inputs + EQ vs REQ bar + glossary tooltips

### Lịch sử layout iterations
- **v1**: Bài của bạn → Tỉ lệ thắng → Số đối thủ → Đề xuất → Tỉ lệ pot → Khoảng bài → Phân tích
- **v2** (sau polish batch 17): Thêm "Thiết lập bàn" → Số đối thủ lên cao → Phân tích trước Đề xuất
- **v3** (sau request "Đề xuất lên trên Khoảng bài"): Hoán đổi → layout hiện tại ở trên

---

## 5. Position system (B.4)

### State mới
```js
STATE.position = 'BTN'   // 'UTG' | 'UTG+1' | 'MP' | 'LJ' | 'HJ' | 'CO' | 'BTN' | 'SB' | 'BB'
STATE.sb = 1
STATE.bb = 2
STATE.rangeView = 'preset'  // 'preset' | 'grid'
```

### Position rank & multipliers
```js
const POSITION_RANK = {
  'UTG': 'EP', 'UTG+1': 'EP', 'MP': 'MP', 'LJ': 'MP', 'HJ': 'MP',
  'CO': 'LP', 'BTN': 'IP', 'SB': 'OOP', 'BB': 'OOP'
};
const POS_MULT = { IP: 0.95, LP: 0.98, MP: 1.05, EP: 1.10, OOP: 1.15 };
```
- **IP (in position)**: margin rộng hơn → call/raise tự tin hơn
- **OOP (out of position)**: margin chặt hơn → cần equity cao hơn để action

### RFI range presets (9-max)
| Vị trí | Threshold | Tương đương ~% |
|---|---|---|
| UTG | 0.55 | 9% (rất chặt) |
| UTG+1 | 0.54 | 11% |
| MP | 0.53 | 13% |
| LJ | 0.52 | 16% |
| HJ | 0.51 | 19% |
| CO | 0.49 | 25% |
| BTN | 0.45 | 45% (rộng nhất) |
| SB | 0.48 | 30% (open) |
| BB | 0.43 | 50% (defend vs raise) |

### Recommendation logic

**Preflop** (board.length === 0):
1. Compute `handKey` (e.g. "AKo", "TT", "KQs") từ hero hand
2. Check `isHandInPreset(handKey, STATE.position)` (so sánh HAND_STRENGTH vs threshold)
3. Trong range → RAISE (nếu call=0) hoặc CALL (nếu call>0)
4. Ngoài range → FOLD
5. Recommendation text giải thích: "Bài AKo trong range BTN — nên mở (raise)"

**Postflop** (board.length >= 3):
1. `effectiveReq = required * POS_MULT[POSITION_RANK[position]]`
2. `eq > effectiveReq * 1.5 && eq > 0.5` → RAISE
3. `eq >= effectiveReq` → CALL
4. Else → FOLD
5. Recommendation text includes position tag: "Tỉ lệ thắng 66% vượt xa yêu cầu 17% (BTN) — hãy tố thêm"

---

## 6. Quick reference — các hàm quan trọng cần biết

```js
// Picker
openPicker(type, indexOrOpts)        // type: 'hero' | 'board' | 'extraBoard'
closePicker()
clearSlot(type, indexOrOpts)

// Equity
scheduleCalc()                       // Debounce 220ms
runEquity()                          // Quyết định preflop/postflop, range/multi-board
renderEquity()                       // Update donut + win/tie/lose pills + chart + breakdown
renderOuts()                         // Update #outsContainer
renderBestHand()                     // Update #bestHandContainer
renderEquityByStreet()               // A.2: mini chart preflop→river
renderShowdownBreakdown()            // A.3: phân bố rank khi thắng

// Range
applyAllRange() / applyTop20Range() / applyTop10Range() / applyPairsRange() / applySuitedRange()
toggleRangeCell(key)                 // Tap 1 ô trong grid view
applyPositionRange()                 // B.5: dùng preset theo STATE.position
setRangeView('preset' | 'grid')      // B.6: toggle view
renderRangePresetChips()             // B.5: render 9 chips UTG→BB

// Position (B.4)
setPosition(pos)                     // 'UTG' → 'BB'
setSB(v) / setBB(v)                  // Blinds
renderPositionChips()                // Render 9 chips
renderTableSummary()                 // "BTN • SB 1 / BB 2"
isHandInPreset(handKey, pos)         // Check RFI range
heroHandKey()                        // "AKo" / "TT" / "KQs"

// Pot
setPot(v) / setCall(v)               // Clamp ≥ 0 (L-4 fix)
renderEVBreakdown(eq)                // E.2: 3 actions EV

// Glossary (B.2)
initGlossary()                       // Bind "?" buttons
showGlossary(anchor, data)           // Popover
closeGlossary()                      // On outside click

// Precision (Opt #3)
setPrecision('thuong' | 'cao')       // 2000 vs 4000 trials

// Persistence
saveState() / loadState()            // localStorage 'poker:state:v1'

// Undo/Redo (B.5)
pushHistory(type)                    // Snapshot trước khi thay đổi
undo() / redo()                      // Apply snapshot
```

### Style tokens thường dùng (CSS variables)
```css
--accent: #00f0ff;       /* cyan - chính */
--accent-2: #b14eff;     /* violet - phụ */
--success: #00ff9d;      /* xanh lá - thắng */
--warning: #ffb800;      /* vàng - hòa */
--danger: #ff2d6f;       /* hồng - thua */
--suit-red: #ff3855;
--suit-black: #1a0d2e;
--hero-gradient: linear-gradient(135deg, #00f0ff 0%, #b14eff 50%, #ff2d6f 100%);
--hero-gradient-rainbow: linear-gradient(135deg, #00f0ff 0%, #00ff9d 25%, #ffb800 50%, #ff2d6f 75%, #b14eff 100%);
```

---

## 7. Glossary entries (B.2)

```js
const GLOSSARY = {
  pot: { title: 'Pot Odds', body: 'Pot odds = tỉ lệ giữa pot và call. Công thức: REQ = call / (pot + call)...' },
  eq:  { title: 'EQ = Equity', body: 'Tỉ lệ thắng ước tính của bạn (%). Tính bằng Monte Carlo...' },
  req: { title: 'REQ = Required Equity', body: 'Tỉ lệ thắng tối thiểu cần để hòa vốn khi call. Công thức: REQ = call ÷ (pot + call)...' }
};
```

UI: nút `?` cyan 18x18px, click → popover (max-width 320px), auto-positioning, click outside → close.

---

## 8. Cách chạy local

```bash
cd D:/CLAUDE/POKER/POKER
python -m http.server 8000
# Mở http://localhost:8000
```

Hoặc dùng Claude Code preview:
```
preview_start name=poker-static
```

---

## 9. Deploy lên Vercel

Xem [DEPLOY.md](D:/CLAUDE/POKER/POKER/DEPLOY.md) để biết 3 phương án:
- **Phương án 1**: GitHub + Vercel Web UI (auto deploy mỗi push) ← **khuyến nghị**
- **Phương án 2**: Vercel CLI (`npm install -g vercel && vercel --prod`)
- **Phương án 3**: Drag & Drop folder vào vercel.com/new (nhanh nhất, không cần GitHub)

Local git đã sẵn sàng:
- 2 commits, branch `main`
- `.gitignore` loại `.claude/`, `.build/`
- Push lệnh: `git remote add origin <url> && git push -u origin main`

---

## 10. Ghi chú kỹ thuật

- **Không có framework** — vanilla JS, không React/Vue. Tất cả render qua `innerHTML` + `document.createElement` + helper `el(tag, attrs, children)`.
- **Monte Carlo trials:**
  - **Thường (mặc định):** 2000 trials, sai số ±2.2%, **nhanh**
  - **Cao:** 4000 trials, sai số ±1.5%, chính xác hơn
  - Preflop có cache localStorage theo key `preflop:AHAD:1` (suit char format `s/h/d/c`)
- **Range sampling** — `monteCarloEquityWithRange` lấy random hand từ 169 ô, retry tối đa 10 lần nếu conflict. **Nếu fail, skip trial** (H-1 fix) thay vì fallback random. Normalize bằng `validTrials` thay vì `trials`.
- **Opt #1 deck optimization:** `buildAvailableDeck` (1 lần) + `partialShuffleInPlace` (per trial, không allocate). Áp dụng cho cả 3 hàm equity.
- **Opt #2 inline combinations:** C(6,5)=6 combos, C(7,5)=21 combos. Fallback `combinationsGeneric` cho 8+ cards (rất hiếm).
- **Opt #4 allocation-free evaluate5:** Fixed 13-element count array, in-place insertion sort cho 5 ranks, single-pass. Tránh Object/Set/Slice.
- **Outs calculation** — 5 categories: straightFlush / flush / straight / set+quads / twoPair. **Bao gồm** higher-rank improvements (H-2 fix) — vd: sảnh 7 cao → A cao.
- **Position logic** — Preflop: so sánh với RFI range theo vị trí. Postflop: position multiplier ảnh hưởng threshold. Recommendation text include position tag.
- **Multi-board** — User có thể thêm 2 bộ board thử, equity = avg qua tất cả. Showdown breakdown cũng avg. Sub-text: "Trung bình N bộ board".
- **History (undo/redo)** — Snapshot các field thay đổi: hero, board, extraBoards, numOpps, activeRange, pot, call, precision, position, sb, bb. Max 10 stack.
- **Haptic** — 6 tap points: card slot, picker rank/suit, range cell, numOpps stepper, precision chip. `navigator.vibrate(ms)` 8-15ms.

---

## 11. Khi user hỏi "tiếp tục" hoặc mở lại

1. **Đọc file index.html** — đã biết dòng nào là gì
2. **Check dev server** — nếu cần, dùng `preview_start name=poker-static`
3. **Hỏi user muốn cải thiện gì** — tham khảo Backlog ở mục 12

---

## 12. Backlog (các thứ có thể cải thiện tiếp)

### A. Thứ tự ưu tiên cao
- [ ] **Tournament/ICM mode** — Input stack + antes + ITM → push/fold EV. Rất phức tạp.

### B. Polish nhỏ
- [ ] **Dark/light mode toggle** — Force dark neon hiện tại. Thêm "Subtle Dark" / "Light Crypto"

### C. Code quality & perf
- [ ] **Break up index.html** — Tách thành `index.html` + `styles.css` + `app.js` (ES modules)
- [ ] **Thêm unit tests** — Vitest cho `evaluateBest`, `compareEval`, `classifyOut`
- [ ] **Opt #5: Web Worker cho Monte Carlo** — UI mượt khi 9 opps (worker file, transferable typed arrays)

### D. UI/UX chi tiết
- [ ] **Theme variations** — 3 themes: Neon Cosmic (hiện tại), Solar Gold, Deep Ocean

### E. Tính năng mới (nâng cao)
- [ ] **Hand history** — Ghi lại từng hand đã phân tích
- [ ] **Hand replayer** — Full hand preflop → river, replay animation
- [ ] **Compare 2 hero hands** — AKo vs QJs 4-handed
- [ ] **All-in equity wizard** — Nhập full tình huống all-in preflop

### F. Bonus ideas
- [ ] **Side pot calculator** — Khi có nhiều all-in khác nhau
- [ ] **Hand equity vs random hand** — Mode đơn giản, preflop equity chỉ vs 1 random hand
- [ ] **Import/export range** — JSON file, share range với bạn bè
- [ ] **Multi-language** — Thêm tiếng Anh
- [ ] **PWA support** — manifest.json + service worker để offline
- [ ] **Position tooltip** — Hiện giải thích IP/OOP/EP khi tap position chip
- [ ] **Raise sizing options** — Hiện tại chỉ cố định +50% pot, thêm các size khác (1x pot, 2x pot, all-in)

---

## 13. Lịch sử versions

| Date | Changes |
|---|---|
| 2026-06-11 (early) | UI redesign Neon Cosmic, picker VN order, range 13×13, equity donut, outs, best hand, pot odds, recommendation, preflop cache |
| 2026-06-11 (mid) | Bug fixes C-1/H-1/H-2/L-4/L-6, Opt #1 (deck), Opt #3 (trials) |
| 2026-06-11 (late) | Polish batch: A.1 (VN labels), A.2 (street chart), A.3 (showdown), A.4 (multi-board), B.1-B.7 (keyboard/onboarding/persistence/share/undo/haptic/comment), C.2-C.3 (perf), D.1-D.3+D.5 (UI/UX), E.2 (EV) |
| 2026-06-11 (final) | Layout reorder (B.1 layout + B.2 glossary + B.3 state + B.4 position + B.5 presets + B.6 toggle), EV polish, brand rename "Poker Equity" → "CaisDeosMasPoker" |
