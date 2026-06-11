# Poker Equity — Handoff Notes (D:/CLAUDE/POKER/POKER)

> **Tóm tắt:** App tính tỉ lệ thắng poker Texas Hold'em, tiếng Việt, giao diện Neon Cosmic 2026, picker rank→suit, equity donut SVG, range 13×13. Đã hoàn thành UI redesign, audit độ chính xác + 2 tối ưu hiệu năng. Đang ổn định.

---

## 1. Trạng thái hiện tại (đã xong)

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

---

## 2. File & cấu trúc

**File chính:** [index.html](D:/CLAUDE/POKER/POKER/index.html) — 1 file self-contained (~2200 dòng, ~70KB).

- **CSS** (dòng 10–1190): Design tokens, layout, components, picker, animations
- **HTML body** (dòng 1192–1194): `<div id="app">` render bằng JS
- **JS** (dòng 1195–2150+): Logic + render toàn bộ DOM
  - **1199–1233** Card/Suit/Constants + `buildAvailableDeck` + `partialShuffleInPlace` (Opt #1 helpers)
  - **1235–1334** Hand evaluator + `handNameVN` (Tiếng Việt)
  - **1336–1396** Range analyzer (`HAND_STRENGTH` table 169 hands)
  - **1398–1525** Equity calculator (3 modes: preflop exact, postflop MC, postflop MC w/ range) — **đã refactor dùng Opt #1**
  - **1527–1555** Outs calculator — **đã fix H-2**
  - **1557–1682** App state + card pickers
  - **1684–1722** Equity render + scheduling
  - **1724–1858** Equity/Outs/Best hand renderers
  - **1860–1907** Pot odds + recommendation — **đã fix L-4**
  - **1909–1955** Range grid
  - **1957–2175** Main `render()` + `init()` + DOM template (có chip precision ở section "Tỉ lệ thắng")

**Dev server:** [`.claude/launch.json`](D:/CLAUDE/POKER/POKER/.claude/launch.json) → `python -m http.server 8000` (cổng 8000).

---

## 3. Thay đổi gần đây (2026-06-11 session)

### 🐛 Bug fixes

| ID | Mức | Vấn đề | File:line | Fix |
|---|---|--------|-----------|-----|
| **C-1** | 🔴 CRITICAL | Two-pair kicker `counts[2].rank` = undefined → tính sai equity khi hero/opp có 2 đôi | [index.html:1255-1256](index.html#L1255) | `ranks.find(r => r !== hiPair && r !== loPair)` |
| **H-2** | 🟠 HIGH | `classifyOut` miss outs khi cùng rank (sảnh 7 cao → A cao, thùng Q → A) | [index.html:1521-1524](index.html#L1521) | Thêm 2 dòng check tiebreakers cho rank 4, 5 |
| **H-1** | 🟠 HIGH | Range sampling fail → fallback random hands → equity sai khi range hẹp | [index.html:1488-1492, 1510-1512](index.html#L1488) | Skip trial (`continue`) + normalize by valid count |
| **L-4** | ⚪ LOW | `parseFloat` chấp nhận số âm → pot/call âm | [index.html:1966-1972](index.html#L1966) | `Math.max(0, parseFloat(v) \|\| 0)` |
| **L-6** | ⚪ LOW | `init()` chạy 2 lần equity (một qua `applyAllRange`, một qua `runEquity` trực tiếp) | [index.html:2144-2148](index.html#L2144) | Bỏ `runEquity()` ở init, để scheduled calc lo |

### ⚡ Perf optimizations

| ID | Speedup | Effort | Thay đổi | File:line |
|---|---|---|---|---|
| **#1** | **2-3×** | 1h | `buildAvailableDeck(known)` + `partialShuffleInPlace(deck)` — build deck 1 lần ngoài loop, shuffle in-place không allocate | [index.html:1213-1233, 1409, 1429, 1453](index.html#L1213) |
| **#3** | **2×** | 30m | `STATE.trials` mặc định 2000 (giảm từ 4000) + chip "Thường/Cao" ở section "Tỉ lệ thắng" | [index.html:1552, 1974-1983, 1998-2007](index.html#L1552) |

**Stack #1 + #3: ~4-6× tổng** cho case thường. Worst case 9 opps + Cao (4000 trials): **~291ms** (trước đây ~600-800ms ước tính).

### 📊 Benchmark (Apple M-class, Chromium)

| Scenario | Thời gian |
|----------|-----------|
| Preflop 1v1, 2000 trials | **~163ms** |
| Postflop 1v1 + 3 board, 2000 trials | **~155ms** |
| Postflop 9 opps + 3 board + range=169, **Cao (4000 trials)** | **~291ms** |
| Postflop 9 opps + 3 board + range=169, **Thường (2000 trials)** | **~150ms** |

---

## 4. Các thứ có thể cải thiện tiếp (backlog)

### A. Thứ tự ưu tiên cao (giá trị rõ ràng)

- [x] **Hand strength labels VN cho range grid** — "AA" → "Đôi Át", "AKs" → "Át K đồng chất" (2026-06-11)
- [x] **Equity-by-street chart** — mini chart preflop→flop→turn→river (2026-06-11)
- [x] **Showdown % breakdown** — phân bố rank khi hero thắng (2026-06-11)
- [x] **Multi-board runout** — tối đa 2 bộ board thử, equity trung bình (2026-06-11)
- [ ] **Tournament/ICM mode** — Input stack + antes + ITM → push/fold EV. Rất phức tạp, dành cho ngày khác.

### B. Polish nhỏ

- [x] **Mobile keyboard handling** — `inputmode="decimal"` + Enter → blur (2026-06-11)
- [x] **First-time onboarding tooltip** — Popover "Chạm vào ô bài để chọn" + dismiss (2026-06-11)
- [x] **Save/restore full STATE** — localStorage `poker:state:v1` (2026-06-11)
- [x] **Share/Export equity** — Nút share topbar, copy text (2026-06-11)
- [x] **Undo/Redo** — History 10 bước với 2 nút topbar (2026-06-11)
- [ ] **Dark/light mode toggle** — Force dark neon hiện tại. Thêm "Subtle Dark" / "Light Crypto"
- [x] **Haptic feedback trên mobile** — `navigator.vibrate(10)` ở 6 tap points (2026-06-11)
- [x] **Comment fix L-8:** Clarify comment ở [index.html:1700](index.html#L1700) về mapping VN names ↔ SUITS indexes (2026-06-11)
- [x] **B.1 Layout reorder** — Số đối thủ + Vị trí lên cao; Phân tích chi tiết → Đề xuất (2026-06-11)
- [x] **B.2 Glossary tooltips** — "?" icon cho Pot/EQ/REQ với inline popover (2026-06-11)
- [x] **B.3 State persistence** — position/sb/bb/rangeView lưu localStorage (2026-06-11)
- [x] **B.4 Position-based recommendation** — Preflop: RFI range check. Postflop: IP/LP/MP/EP/OOP multiplier (2026-06-11)
- [x] **B.5 Range presets by position** — 9-max RFI data (UTG/MP/LJ/HJ/CO/BTN/SB/BB) (2026-06-11)
- [x] **B.6 Range Grid/Preset toggle** — 2 views với tab switcher (2026-06-11)

### C. Code quality & perf còn lại

- [ ] **Break up index.html** — Tách thành `index.html` + `styles.css` + `app.js` (ES modules)
  ```
  /src
    cards.js       (SUITS, RANK_DISPLAY, buildAvailableDeck)
    evaluator.js   (evaluate5, evaluateBest, handNameVN)
    equity.js      (preflopEquityExact, monteCarlo*)
    outs.js        (calculateOuts, classifyOut)
    range.js       (HAND_STRENGTH, applyRangeFilter)
    ui/
      picker.js
      range-grid.js
      equity-hero.js
      pot.js
    app.js
  ```
- [ ] **Thêm unit tests** — Vitest cho `evaluateBest`, `compareEval`, `classifyOut` (verify C-1 + H-2 fixes)
- [x] **Opt #2: Inline `combinations()`** — Hand-rolled C(6,5)=6 / C(7,5)=21 (2026-06-11)
- [x] **Opt #4: Rewrite `evaluate5` allocation-free** — Single-pass, fixed array, in-place sort (2026-06-11)
- [ ] **Opt #5: Web Worker cho Monte Carlo** — UI mượt khi 9 opps (worker file, transferable typed arrays)

### D. UI/UX chi tiết

- [x] **Picker UX**: swipe gesture để đổi rank (2026-06-11)
- [x] **An toàn cảm xúc**: "Bài yếu — nên cân nhắc bỏ" khi equity < 20% (2026-06-11)
- [x] **Equity sentiment tinh tế** — 4 tiers: great/decent/neutral/bad (2026-06-11)
- [ ] **Theme variations** — 3 themes: Neon Cosmic (hiện tại), Solar Gold, Deep Ocean
- [x] **M-2 + L-9 fix:** `classifyOut` gộp straight-flush (rank 8) vào "Thùng" — tách thành "Thùng phá sảnh" riêng (2026-06-11)

### E. Tính năng mới (nâng cao)

- [ ] **Hand history** — Ghi lại từng hand đã phân tích
- [x] **EV calculator** — $EV cho BỎ/THEO/TỐ, hiển thị dưới recommendation (2026-06-11)
- [ ] **Hand replayer** — Full hand preflop → river, replay animation
- [ ] **Compare 2 hero hands** — AKo vs QJs 4-handed
- [ ] **All-in equity wizard** — Nhập full tình huống all-in preflop

---

## 5. Lỗi đã fix (lịch sử)

| # | Lỗi | Fix |
|---|---|---|
| 1 | Card slot là hình chữ nhật ngang | `max-height: 110px` → `max-width: 110px` + `aspect-ratio: 5/7` |
| 2 | Range grid hiện "1010" thay vì "TT" | `r1 === 10 ? 'T' : RANK_DISPLAY[r1]` |
| 3 | Bích ♠ / Chuồn ♣ invisible | Sửa `picker-card.black` color thành `#0a0a0a` + `-webkit-text-stroke` |
| 4 | Thứ tự chất sai | User confirm "Cơ Rô Chuồng Bích" → `suitOrder = [1, 2, 3, 0]` |
| 5 | A cơ / A rô "mất màu trắng" | **Cùng background trắng + viền cyan + badge ✓** |
| 6 | Missing quân 2 | Range order `[14, 13, ..., 2]` (có cả 2) |
| 7 | **C-1 (2026-06-11)** Two-pair kicker undefined | `ranks.find()` thay vì `counts[2].rank` |
| 8 | **H-2 (2026-06-11)** Outs miss higher rank | Compare tiebreakers khi rank bằng |
| 9 | **H-1 (2026-06-11)** Range fallback sai | Skip trial thay vì random |
| 10 | **L-4 (2026-06-11)** Pot/Call âm | `Math.max(0, ...)` |
| 11 | **L-6 (2026-06-11)** Init double-run | Bỏ `runEquity()` ở init |

---

## 6. Quick reference — các hàm quan trọng cần biết

```js
// Picker — mở khi user tap card slot
openPicker(type, index)              // type: 'hero' | 'board', index: 0-4
closePicker()
clearSlot(type, index)               // Xóa 1 card

// Equity
scheduleCalc()                       // Debounce 220ms
runEquity()                          // Quyếtết định preflop/postflop, range/random
renderEquity()                       // Update donut + win/tie/lose pills
renderOuts()                         // Update #outsContainer (cần 3-4 board cards)
renderBestHand()                     // Update #bestHandContainer (cần 2 hero + 3+ board)

// Range
applyAllRange()                      // 169/169
applyTop20Range() / applyTop10Range()
applyPairsRange() / applySuitedRange()
toggleRangeCell(key)                 // Tap 1 ô

// Pot
setPot(v) / setCall(v)               // Clamp ≥ 0 (L-4 fix), oninput → renderPot() + recommendation

// Precision (Opt #3)
setPrecision('thuong' | 'cao')       // 2000 vs 4000 trials
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

## 7. Cách chạy local

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

## 8. Ghi chú kỹ thuật

- **Không có framework** — vanilla JS, không React/Vue. Tất cả render qua `innerHTML` + `document.createElement`. Đơn giản nhưng hạn chế khi scale.
- **Monte Carlo trials:**
  - **Thường (mặc định):** 2000 trials, sai số ±2.2%, **nhanh**
  - **Cao:** 4000 trials, sai số ±1.5%, chính xác hơn
  - Preflop có cache localStorage theo key `preflop:AHAD:1` (suit char format `s/h/d/c`)
- **Range sampling** — `monteCarloEquityWithRange` lấy random hand từ 169 ô, retry tối đa 10 lần nếu conflict. **Nếu fail, skip trial** (H-1 fix) thay vì fallback random. Normalize bằng `validTrials` thay vì `trials`.
- **Opt #1 deck optimization:** `buildAvailableDeck` (1 lần) + `partialShuffleInPlace` (per trial, không allocate). Áp dụng cho cả 3 hàm equity.
- **Outs calculation** — 4 categories: flush / straight / set+quads / twoPair. **Bao gồm** higher-rank improvements (H-2 fix) — vd: sảnh 7 cao → A cao.
- **Không bao gồm** "improve to one pair" (chỉ tính draws thực sự).

---

## 9. Khi user hỏi "tiếp tục" hoặc mở lại

1. **Đọc file index.html** — đã biết dòng nào là gì
2. **Check dev server** — nếu cần, dùng `preview_start name=poker-static`
3. **Hỏi user muốn cải thiện gì** — tham khảo Backlog ở mục 4

**Câu hỏi mở thường gặp:**
- "Picker thêm animation khi đổi rank?" → thêm `transition: transform 0.2s` cho `.picker-suits` + keyframe slide
- "Thêm nút Reset?" → đã có topbar-action, dễ thêm onclick clear all
- "Lưu full state?" → wrap setItem/getItem quanh các setter trong `init`
- "Equity nhanh hơn nữa?" → Opt #2 (inline combinations) + Opt #4 (allocation-free eval) = thêm ~3-5× nữa. Hoặc Opt #5 (Web Worker) cho UI mượt.
- "Toggle precision không hoạt động?" → gọi `setPrecision('cao' | 'thuong')`, cập nhật UI qua `data-precision` selector
