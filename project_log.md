# BSD 404 — Algorithm Visualizer: Project Development Log

**Student:** Azad, Arham Bin — ID: 20220001121  
**Course:** BSD 404 — Algorithms II  
**Instructor:** Dr. Arash Kermani  
**Deadline:** March 30, 2026  
**Tool:** Single-file `index.html` — deployed to GitHub Pages

---

## Project Overview

An interactive web-based teaching tool with three parts, each visualizing a different algorithm from the course. Built entirely in vanilla HTML5, CSS3, and JavaScript (ES6+) — no external libraries or frameworks. All three parts live in one `index.html` file with a tab selector at the top.

| Part | CLO | Algorithm | Marks |
|------|-----|-----------|-------|
| Part 1 | CLO-1 | Odd-Even Merge Sorting Network | /10 |
| Part 2 | CLO-2 | FFT: Polynomial Multiplication | /10 |
| Part 3 | CLO-3 | Miller-Rabin Primality Testing | /10 |

---

## Required Report Metadata (CLO-1)
---

## Reference Tools Studied

Before building, both instructor reference tools were fetched and analyzed:

- **Sorting Algorithms:** https://arashkermaniprojects.github.io/sorting_algorithms/
- **String Matching:** https://arashkermaniprojects.github.io/string_matching/

### Key patterns extracted from references:

**Controls panel order (exact):**
Algorithm selector → input fields → Quick Example presets → Build Steps / Step / Play / Pause / Reset → Step counter (bold `Step 0 / 0`) → Status (bold) → stat counters → tip line

**Visualization area:**
Color legend row → main SVG canvas → Step Explanation box → History table

**Educational content:**
Collapsible `Collapse/Expand` section with numbered anchor links forming a learning roadmap (modeled on the string matching tool)

**Typography:** Monospace for pseudocode, bold for counters/labels, plain English in explanation box

---

## Architecture

### File structure
```
index.html          ← entire project: HTML + CSS + JS in one file (~3200 lines)
```

### CSS variables (dark theme)
```css
--bg:        #0d1117   /* page background */
--bg2:       #161b22   /* panel background */
--bg3:       #21262d   /* section background */
--border:    #30363d
--text:      #e6edf3
--text2:     #8b949e
--text3:     #6e7681
--accent:    #58a6ff   /* blue */
--green:     #3fb950
--yellow:    #d29922
--orange:    #f0883e
--red:       #f85149
--purple:    #bc8cff
```

### Layout
- Two-column: 420px left (controls) + flex-fill right (visualization)
- Responsive: single column under 980px via `@media (max-width: 980px)`
- Tab nav at very top switches between Part 1 / Part 2 / Part 3

### Shared UI components (reused across all 3 parts)
- `.panel-section` — card container with title
- `.pseudocode-block` + `.pc-line` + `.pc-line.active` — line-highlighted pseudocode
- `.step-explanation` — plain-English step description box
- `.history-table-wrap` — scrollable 30-row step history table
- `.playback-row` — Build Steps / Step / Play / Pause / Reset buttons
- `.status-row` — Step X/Y counter + Status badge
- `.stats-row` — live operation counters
- `.legend` — color-coded legend row
- `.preset-btn` — quick example buttons

---

## Part 1 — Odd-Even Merge Sorting Network (CLO-1)

### Algorithm summary
Batcher's Odd-Even Merge Sort (1968). Recursively splits elements into odd-indexed and even-indexed groups, sorts each recursively, then merges using a specific pattern of compare-and-swap operations. Complexity: O(n log²n) comparators, O(log²n) depth.

### Key functions

#### `buildOENetwork(n)` → `[{i, j, depth, phaseType, lo, hi, step, branch}]`
Generates all comparator pairs for the odd-even merge sort network of size `n`. Uses two nested recursive functions:
- `oeSort(lo, hi)` — recursively sorts halves then calls `oeMerge`
- `oeMerge(lo, hi, step, branch)` — recursively merges odd/even sub-sequences, assigns depth via wire-tracking array, and tags each comparator with recursive context metadata (base compare vs final merge layer)

#### `comparatorPhaseText(c)` → `string`
Builds human-readable recursive context text for each comparator (odd sub-merge, even sub-merge, or final compare-swap layer) used in step explanations and history tooltip.

#### `verifyNetwork01(input, comparators)` → `{ok, output}`
Applies the generated network to a binary input and returns whether the output is sorted.

#### `toggleZeroOneMode1()` / `p1RunZeroOne()`
Implements executable 0-1 Principle verification mode in Part 1 controls:
- Enables/disables test controls from checkbox state
- Exhaustively tests all `2^n` binary inputs for selected size (`n=4` or `n=8`)
- Reports pass/fail count, first counterexample if any, and network comparator/depth stats

#### `groupByDepth(comparators)` → `layers[]`
Groups comparators into parallel depth levels. Each level can be executed simultaneously in hardware.

#### `buildBitonicNetwork(n)` → `[{i, j}]`
Generates the bitonic sort comparator network for side-by-side comparison. Uses `bitonicSort` + `bitonicMerge` recursion.

#### `generateSteps1(arr)` → `steps[]`
Produces the full animation step array. Each step contains:
- `type` — `init` / `layer_start` / `compare` / `swap` / `done`
- `arr` — array state at this step
- `pcLine` — pseudocode line to highlight (0-indexed)
- `phase` — depth label
- `explanation` — HTML string with concrete values
- `comparisons`, `swaps` — cumulative counters
- `highlight` — wire indices to color
- `activeComps` — comparator pairs active at this step

#### `drawNetwork1(arr, step)`
Renders the wiring diagram as inline SVG. Calculates wire positions, draws horizontal wires (odd = blue, even = orange), draws vertical comparator bars with circles at endpoints. Active comparators shown larger and brighter.

#### `drawBitonic(arr)`
Renders the bitonic sort network in orange below the main visualization for side-by-side comparison.

### Visualizations
- **Main wiring diagram SVG** — horizontal wires (odd=blue, even=orange), vertical comparator bars, depth labels
- **Bitonic side-by-side** — separate SVG showing bitonic network on same input
- **Comparator count comparison panel** — exact counts for both networks + % savings note
- **0-1 Principle verification panel** — executable binary-input correctness check with exhaustive case count and result summary

### Lecture notes sections (8 total)
1. What is a Sorting Network?
2. Comparators & the Compare-Swap Operation
3. Batcher's Odd-Even Merge (1968)
4. Recursive Structure
5. The 0-1 Principle
6. Complexity Analysis (table: Odd-Even vs Bitonic)
7. Bitonic Sort vs Odd-Even Sort
8. Interactive Tool — How to Use

### Part 1 implementation notes (CLO-1 strict coverage)
- The Step Explanation now includes recursive merge context at each depth level (which sub-merge is active and whether step is base compare or final merge layer).
- Step History rows include explanation text in row tooltips for quick teaching reference.
- 0-1 verification output is also reflected in the explanation/status area to connect empirical binary testing with the 0-1 Principle statement.

### Presets
| Label | Array |
|-------|-------|
| Preset 1 (8 el.) | [5,3,8,1,9,2,7,4] |
| Reverse halves | [4,3,2,1,8,7,6,5] |
| Already sorted | [1,2,3,4,5,6,7,8] |
| Reverse sorted | [8,7,6,5,4,3,2,1] |
| Random (8) | randomArr1(8) |
| Random (16) | randomArr1(16) |

---

## Part 2 — FFT: Polynomial Multiplication (CLO-2)

### Algorithm summary
Multiplying two polynomials naïvely takes O(n²). FFT reduces this to O(n log n) via three steps:
1. **Evaluate** both polynomials at 2n roots of unity using FFT — O(n log n)
2. **Pointwise multiply** the evaluations — O(n)
3. **Interpolate** back to coefficients using inverse FFT — O(n log n)

Core mechanism: Cooley-Tukey butterfly operations splitting even/odd coefficients recursively. Recurrence: T(n) = 2T(n/2) + O(n).

### Key functions

#### Complex number helpers
```js
cAdd(a,b), cSub(a,b), cMul(a,b), cStr(c, dp), cMag(c)
// All operate on {re, im} objects
```

#### `fft(a, invert)` → complex[]
Iterative Cooley-Tukey FFT. Steps:
1. Bit-reversal permutation
2. For each level `len` from 2 to n: compute twiddle factor `wlen = e^(±2πi/len)`, apply butterfly operations in-place
3. If `invert`: divide all values by n

#### `nextPow2(n)` → int
Returns smallest power of 2 ≥ n.

#### `generateSteps2(A, B)` → steps[]
Produces full animation sequence:
- Init step (padding explanation)
- Phase 1a: FFT(A) butterfly levels
- Phase 1b: FFT(B) butterfly levels
- Phase 2: pointwise multiply (one step per k)
- Phase 3: IFFT butterfly levels
- Done step (final coefficients)

Each step carries: `phase`, `pcLine`, `type`, `butterflies`, `mults`, `circleN`, `circleActive`, `butterflyHighlight`, `explanation`

#### `generateButterflySteps(a, invert, label, counter)` → steps[]
Generates one step per FFT level, computing which butterfly pairs are active at each level and accumulating the counter.

#### `drawCircle2(step)`
Renders the unit circle SVG:
- Axes and circle
- n evenly-spaced root-of-unity dots
- Active roots highlighted with radial lines from center
- Labels ω⁰…ωⁿ⁻¹ for small n
- Dashed line showing ω^(n/2) = −1 (cancellation lemma)
- Phase color: blue (FFT), purple (IFFT), green (pointwise)

#### `drawButterfly2(step)`
Renders the butterfly network SVG:
- Input labels (a[i]) on left, output labels (Y[i]) on right
- log₂(n) levels of horizontal wires
- X-shaped butterfly crosses between top/bottom pairs
- Active level highlighted in phase color with twiddle factor labels (ω^k)
- Phase title with total butterfly count

### Visualizations
- **Unit circle SVG** — n-th roots of unity as dots on unit circle, active roots lit per phase
- **Butterfly network SVG** — full data-flow diagram across all log₂(n) levels
- **Recursive split-tree SVG (optional toggle)** — explicit divide-and-conquer even/odd split hierarchy, synchronized with FFT levels
- **Result + complexity panel** — final C(x) polynomial, FFT vs naïve operation counts, speedup multiplier

### Part 2 enhancement (strict CLO-2 coverage)
To make the requirement **"animate the recursive splitting of coefficients"** explicit (not just implied by butterflies), an additional optional visualization was implemented in the main `index.html`:

- New left-panel control: **Show recursive split tree (even/odd)** checkbox
- New right-panel canvas: **Recursive Split Tree — Even/Odd Divide**
- New Part 2 logic:
  - `buildSplitTreeLevels2(n)`
  - `getPart2PhaseColor(phase)`
  - `drawSplitTree2(step, n)`
  - `toggleSplitTree2()`

#### Split-tree behavior by phase
- **FFT(A) / FFT(B) butterfly levels:** highlights the active split depth (L1..Lk) and group boundaries
- **Pointwise phase:** keeps leaf-depth active and highlights current k
- **Done phase:** marks terminal split layer as complete

This keeps the existing unit-circle and butterfly visuals unchanged while making recursive structure directly teachable during demos.

### Lecture notes sections (9 total)
1. The Polynomial Multiplication Problem
2. Naïve O(n²) Approach
3. Roots of Unity (Cancellation + Halving Lemmas)
4. The FFT Idea
5. Butterfly Operations
6. The 3-Step Process (worked example table)
7. Inverse FFT
8. Complexity Analysis (table)
9. Interactive Tool — How to Use

### Presets
| Label | A | B |
|-------|---|---|
| Linear × Quadratic | [1,2,3] | [4,5] |
| x+1 squared | [1,1] | [1,1] |
| x²+1 squared | [1,0,1] | [1,0,1] |
| Degree-3 × Degree-3 | [1,2,3,4] | [1,-1,1,-1] |
| Sparse polynomials | [3,0,2,0,1] | [1,0,4] |

---

## Part 3 — Miller-Rabin Primality Testing (CLO-3)

### Algorithm summary
Probabilistic primality test used in RSA key generation. Write n−1 = 2ˢ·d where d is odd. For witness a: compute a^d mod n, then square repeatedly. If the chain never produces −1 (mod n) before reaching 1, a non-trivial square root of 1 is found — n is definitely composite. After k rounds, error probability ≤ 4^(−k).

### Key functions

#### `modPow(base, exp, mod)` → BigInt
Square-and-multiply modular exponentiation using BigInt arithmetic (safe for numbers up to 2⁵³). Steps:
1. `result = 1`
2. While `exp > 0`: if exp is odd multiply result by base mod m, halve exp, square base mod m

#### `decompose(n)` → `{s, d}`
Factors n−1 = 2ˢ·d by repeatedly dividing by 2 until d is odd.

#### `fermatTest(n, a)` → bool
Computes a^(n−1) mod n using `modPow`. Returns true if ≡ 1 (mod n).

#### `mrWitness(n, a)` → `{result, chain, s, d}`
Runs one full Miller-Rabin round for witness a. Returns:
- `result`: `'pass_initial'` / `'pass_neg1'` / `'composite'`
- `chain`: array of `{val, r}` — all values in the squaring sequence

#### `getWitnesses(n, k)` → int[]
Returns deterministic witnesses [2,3,5,7,…] for n < 3,215,031,751 (gives guaranteed correct result), or pseudorandom witnesses for larger n.

#### `generateSteps3(n, k)` → `{steps, witnesses, roundResults, s, d}`
Produces full animation sequence:
- Init step
- Decomposition steps (one per division-by-2)
- Decomposition done
- Per witness: round_start → modpow_done → square_step(s) → round_pass or composite_found
- Final done step with verdict, confidence, and fermatResults for comparison

#### `drawDecomp3(step)`
Renders the decomposition SVG: n−1 box → division arrows → intermediate values → final 2^s×d result box. Each division step lights up green as it completes.

#### `drawChain3(step)`
Renders the squaring chain SVG: horizontal row of boxes `a^d → a^(2d) → … → a^(n−1)`, each showing its computed value. Color coding:
- Blue — computed via modpow
- Green — value is 1 or n−1 at the right position (passes)
- Red — value is 1 without having seen n−1 (composite detected)
- Yellow — currently active squaring step

#### `updateRoundsDisplay3(step)`
Renders the live witness rounds table: round number, witness a, pass/fail, cumulative confidence percentage.

#### `updateFermat3(step)`
Renders the Fermat vs Miller-Rabin comparison table on the final done step. Shows a^(n−1) mod n for each witness and what each test concludes. Displays an orange warning banner for Carmichael numbers.

### Visualizations
- **Decomposition SVG** — animated n−1 = 2ˢ·d factoring
- **Squaring chain SVG** — full a^d → … → a^(n−1) chain with color-coded outcomes
- **Witness rounds table** — live running results with confidence
- **Fermat comparison panel** — side-by-side with orange Carmichael warning
- **Final verdict banner** — green PROBABLY PRIME or red COMPOSITE

### Carmichael number demo
The 561 preset (and 1105) triggers a special path:
- Fermat's test shows a^(n−1) mod n = 1 for all witnesses → incorrectly says prime
- Miller-Rabin finds a non-trivial √1 in the squaring chain → correctly says composite
- Orange warning banner displayed in the comparison panel

### Lecture notes sections (12 total)
1. Why Primality Testing Matters
2. Fermat's Little Theorem
3. Carmichael Numbers — Fermat's Fatal Weakness
4. The Miller-Rabin Insight — Square Roots of 1
5. Decomposing n−1 = 2ˢ·d
6. The Squaring Chain — The Core Test
7. Multiple Rounds & Confidence (table)
8. Modular Exponentiation — Square and Multiply
9. Extended Euclidean Algorithm — Computing d = e⁻¹ mod φ(n)
10. Why RSA Decryption Works — m^(ed) ≡ m (mod n)
11. Why Demo Keys Are Small
12. Interactive Tool — How to Use

### Presets
| Label | n | Rounds |
|-------|---|--------|
| 17 (prime) | 17 | 5 |
| 221 = 13×17 | 221 | 5 |
| 97 (prime) | 97 | 5 |
| 341 (composite) | 341 | 5 |
| 7919 (prime) | 7919 | 5 |
| 561 — Carmichael ⚠ | 561 | 8 |
| 1105 — Carmichael ⚠ | 1105 | 8 |
| 104729 (prime) | 104729 | 5 |

### Part 3 reliability updates (March 2026)
- Added explicit boundary-case handling for `n=3` in step generation so the visual flow concludes deterministically with 100% confidence (instead of attempting witness rounds).
- Clarified **requested rounds vs executed rounds** in the ready/explanation text to avoid confusion when deterministic witness sets are used for small `n`.
- Fixed confidence semantics:
  - deterministic range (`n < 3,215,031,751`) now reports guaranteed confidence behavior,
  - probabilistic range uses the standard Miller-Rabin bound `1 - 4^(-k)` based on executed rounds.
- Added stronger rounds input validation (`k >= 1`) before building steps.
- Updated verdict and rounds/fermat UI text so deterministic vs probabilistic outcomes are clearly labeled during classroom demos.

### Part 3 post-fix validation checklist
- [x] `n=3` builds and finishes with deterministic prime verdict and no witness-round confusion.
- [x] `n=221`, `k=5` still shows normal decomposition, chain, and round progression.
- [x] `n=561` (Carmichael) still demonstrates Fermat failure and Miller-Rabin composite detection.
- [x] Build → Step → Play → Pause → Reset cycle remains stable for Part 3.
- [x] Confidence text remains consistent with deterministic/probabilistic mode.

---

## Bug Fix — Scroll Blocked During Playback

### Problem
When the algorithm was playing, the user could not freely scroll up or down the page.

### Root causes (3 separate issues)

**1. `setInterval` firing continuously**
All three Play functions used `setInterval`, which fires on a fixed clock tick regardless of what the browser is doing. Each tick forced SVG `innerHTML` rebuilds, triggering full layout reflow. The browser had to pause scroll compositing to process every redraw.

**2. `scrollIntoView` fighting the user's scroll**
All three `addHistory` functions called `element.scrollIntoView()` on every animation step. When the user was scrolling the page manually, the browser received competing scroll commands — programmatic vs user gesture — causing jank and blocking.

**3. Mismatched `clearInterval` / `clearTimeout`**
Some timer cancel calls used `clearInterval` on IDs that were actually `setTimeout` handles (or vice versa). In browsers, passing a `setTimeout` ID to `clearInterval` is silently ignored — the timer keeps firing.

### Fix applied

**Replaced `setInterval` with `setTimeout` chains** in all three Play functions:
```js
// BEFORE — fires every N ms regardless
p1State.timer = setInterval(() => { ... }, delay);

// AFTER — each tick schedules the next only after finishing
function tick() {
  updateStep1(next);
  if (p1State.status === 'Playing') {
    p1State.timer = setTimeout(tick, Math.max(120, 800 / getSpeed1()));
  }
}
p1State.timer = setTimeout(tick, Math.max(120, 800 / getSpeed1()));
```

**Added `safeScrollIntoView` helper** that listens for user scroll activity:
```js
let _userScrolling = false;
let _scrollTimeout = null;
window.addEventListener('scroll', () => {
  _userScrolling = true;
  clearTimeout(_scrollTimeout);
  _scrollTimeout = setTimeout(() => { _userScrolling = false; }, 800);
}, { passive: true });

function safeScrollIntoView(el) {
  if (!_userScrolling) {
    el.scrollIntoView({ block: 'nearest', behavior: 'auto' });
  }
}
```

**Replaced all `clearInterval` with `clearTimeout`** in:
- `switchTab()`
- `p1Build()`, `p1Step()`, `p1Pause()`, `p1Reset()`, `updateStep1()`
- `p2Build()`, `p2Step()`, `p2Pause()`, `p2Reset()`, `updateStep2()`
- `p3Build()`, `p3Step()`, `p3Pause()`, `p3Reset()`, `updateStep3()`

**Replaced all three raw `scrollIntoView` calls** with `safeScrollIntoView`:
- `addHistory1()`
- `addHistory2()`
- `addHistory3()`

### Files changed
- `index.html` — all changes in the `<script>` block, no HTML or CSS modifications

---

## Deployment Checklist

- [ ] Upload `index.html` to GitHub repository (must be named exactly `index.html`)
- [ ] Settings → Pages → Source: Deploy from branch → `main` → `/ (root)` → Save
- [ ] Wait ~60–90 seconds for deployment
- [ ] Test live URL: `https://<username>.github.io/<repo-name>/`
- [ ] Test all three tabs open and function
- [ ] Test all presets in each part
- [ ] Test Build Steps → Step → Play → Pause → Reset cycle in each part
- [ ] Test speed slider (0.25× to 4×)
- [ ] Test scrolling while animation is running
- [ ] Test Carmichael number 561 in Part 3
- [ ] Test responsive layout at < 980px width
- [ ] Submit GitHub Pages link to LMS
- [ ] Upload `index.html` file to LMS
- [ ] Prepare for in-class demo (5–7 minutes, must explain all 3 algorithms)

---

## Demo Talking Points

### Part 1 — Odd-Even Merge Sort
- A sorting network is data-independent — same comparators regardless of input values
- The odd-even split: merge odd-indexed elements, merge even-indexed elements, then one final compare-swap layer
- 0-1 Principle: only need to verify 2ⁿ binary inputs instead of n! permutations
- O(n log²n) comparators, O(log²n) depth — same as bitonic sort but fewer actual comparators
- Show the side-by-side comparator count (e.g. n=8: Odd-Even=19, Bitonic=24)

### Part 2 — FFT Polynomial Multiplication
- Naïve convolution: O(n²) — for n=1024 that's ~1 million multiplications
- FFT key insight: evaluate both polynomials at n roots of unity, multiply pointwise, interpolate back
- Roots of unity enable the recursion: (ω^k)^(n/2) = (−1)^k — squaring halves the problem
- Butterfly operation: t = ω^k · b; upper = a + t; lower = a − t
- T(n) = 2T(n/2) + O(n) → same recurrence as merge sort → O(n log n)
- Show speedup on the complexity panel: e.g. n=8 FFT≈48 ops vs naïve=25 (FFT wins at large n)

### Part 3 — Miller-Rabin
- RSA needs large primes — trial division is impossible at 2048 bits
- Write n−1 = 2ˢ·d; compute squaring chain a^d → a^(2d) → … → a^(n−1)
- If chain never shows −1 before hitting 1: found non-trivial √1 → definitely composite
- Carmichael numbers (e.g. 561): fool Fermat's test but NOT Miller-Rabin
- Error probability per round ≤ 1/4; after 40 rounds probability of error ≤ 10⁻²⁴
- Show 561: Fermat says "prime" for every witness; Miller-Rabin catches it as composite

---

## Implementation Progress Update (March 28, 2026)

Started implementation of the post-audit hardening tasks to improve demo reliability and classroom usability.

### Applied in `index.html`

- **Global keyboard shortcuts added** for active tab playback control:
  - `Space` → Play/Pause toggle
  - `Right Arrow` → Step forward
  - `Escape` → Pause
  - `Home` → Reset
  - Shortcuts are ignored while typing in input/select/button fields.

- **Scroll-safe history behavior improved for mobile**:
  - Reduced scroll-activity lock window from 800ms to 400ms.
  - Disabled automatic history row scrolling on narrow viewports (max-width: 768px) to reduce scroll fighting on phones/tablets.

- **Part 1 input guardrails added**:
  - Prevents building with more than 64 manually entered values.
  - Prevents builds where power-of-2 padding would exceed 64 elements.

- **Part 2 numerical transparency note added**:
  - Added an explicit floating-point rounding note near the FFT complexity panel so students understand tiny coefficient noise at larger input sizes.

### Status

- This is the first implementation pass from the audit plan.
- Next step: run the P0 verification matrix (all 3 parts: Build → Step → Play → Pause → Reset + edge-case demos) and then update checklist items accordingly.

---

*Log last updated: March 2026*
