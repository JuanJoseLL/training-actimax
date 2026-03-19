# Mi Plan de Hoy — Calculator Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build an interactive calculator section that lets athletes input their age, activity type, intensity zone, and duration to get personalized training zone ranges, Actimax product consumption recommendations, and a race-day checklist.

**Architecture:** Single Astro component (`PlanCalculator.astro`) with vanilla JS for interactivity and CSS for styling, following the exact patterns used by existing components (scoped `<style>`, `<script>` blocks, CSS custom properties, scroll-reveal animations). Three supporting file modifications: `Icon.astro` (new icon), `PillarNav.astro` (new nav entry), `index.astro` (mount component).

**Tech Stack:** Astro 6, vanilla TypeScript in `<script>` tags, CSS custom properties from `global.css`, localStorage for persistence.

**Spec:** `docs/superpowers/specs/2026-03-18-plan-calculator-design.md`

---

## File Structure

| Action | File | Responsibility |
|--------|------|----------------|
| Create | `src/components/PlanCalculator.astro` | Full calculator: HTML structure, scoped CSS, vanilla JS logic |
| Modify | `src/components/Icon.astro` | Add `calculator` icon SVG paths |
| Modify | `src/components/PillarNav.astro` | Add "Mi Plan" nav item + update `pillarIds` array in script |
| Modify | `src/pages/index.astro` | Import and mount `PlanCalculator` between `PillarNutrition` and `ClosingSection` |

---

### Task 1: Add calculator icon to Icon.astro

**Files:**
- Modify: `src/components/Icon.astro` (before closing `</svg>` on line 142)

- [ ] **Step 1: Add the calculator icon SVG paths**

After the `eye-off` conditional block (line 141), before the closing `</svg>` (line 142), add:

```astro
  {name === 'calculator' && (
    <Fragment>
      <rect x="4" y="2" width="16" height="20" rx="2" />
      <line x1="8" y1="6" x2="16" y2="6" />
      <line x1="8" y1="10" x2="9" y2="10" />
      <line x1="12" y1="10" x2="13" y2="10" />
      <line x1="16" y1="10" x2="16" y2="10" />
      <line x1="8" y1="14" x2="9" y2="14" />
      <line x1="12" y1="14" x2="13" y2="14" />
      <line x1="16" y1="14" x2="16" y2="14" />
      <line x1="8" y1="18" x2="9" y2="18" />
      <line x1="12" y1="18" x2="16" y2="18" />
    </Fragment>
  )}
```

- [ ] **Step 2: Verify icon renders**

Run: `npm run dev`
Open browser, navigate to any section. Temporarily test by changing an existing icon reference to `calculator` in browser devtools to confirm it renders.

- [ ] **Step 3: Commit**

```bash
git add src/components/Icon.astro
git commit -m "feat: add calculator icon to Icon component"
```

---

### Task 2: Add "Mi Plan" entry to PillarNav

**Files:**
- Modify: `src/components/PillarNav.astro:5-11` (pillars array)
- Modify: `src/components/PillarNav.astro:179` (pillarIds array in script)

- [ ] **Step 1: Add Mi Plan to the pillars array**

In the `pillars` array (line 5-11), add after the `nutricion` entry:

```typescript
  { id: 'mi-plan', label: 'Mi Plan', icon: 'calculator', color: 'var(--color-energy)' },
```

- [ ] **Step 2: Add mi-plan to the pillarIds array in the script**

In the `<script>` block, update line 179:

```typescript
const pillarIds = ['hidratacion', 'energia', 'sueno', 'mentalidad', 'nutricion', 'mi-plan'];
```

- [ ] **Step 3: Verify nav item appears**

Run: `npm run dev`
Confirm the "Mi Plan" item with calculator icon appears in the nav bar on both desktop and mobile widths. Verify 6 items still fit without overflow on a 375px viewport.

- [ ] **Step 4: Commit**

```bash
git add src/components/PillarNav.astro
git commit -m "feat: add Mi Plan entry to PillarNav"
```

---

### Task 3: Create PlanCalculator component — HTML structure

**Files:**
- Create: `src/components/PlanCalculator.astro`

- [ ] **Step 1: Create the component with full HTML structure**

Create `src/components/PlanCalculator.astro` with the complete HTML markup for all 4 steps. Steps 2-4 start hidden and are revealed progressively by JS (Task 5).

```astro
---
import Icon from './Icon.astro';

const zones = [
  { id: 'z1', label: 'Z1', pctLow: 0.5, pctHigh: 0.6, name: 'Recuperación / Regeneración', color: '#4CAF50' },
  { id: 'z2', label: 'Z2', pctLow: 0.6, pctHigh: 0.7, name: 'Manejo de peso / Quema de grasa', color: '#2196F3' },
  { id: 'z3', label: 'Z3', pctLow: 0.7, pctHigh: 0.8, name: 'Capacidad aeróbica / Cardiovascular', color: '#FF9800' },
  { id: 'z4', label: 'Z4', pctLow: 0.8, pctHigh: 0.9, name: 'Umbral anaeróbico / Mejora considerable', color: '#E91E63' },
  { id: 'z5', label: 'Z5', pctLow: 0.9, pctHigh: 1.0, name: 'Velocidad y potencia / Esfuerzo máximo', color: '#9C27B0' },
];

const checklistData = [
  {
    phase: 'Semana previa',
    items: [
      { id: 'sem-1', text: 'Hacer supercompensación' },
      { id: 'sem-2', text: 'Aumentar carbohidratos (pastas)' },
      { id: 'sem-3', text: 'Verificar estado del equipo (uniforme, gafas, camiseta, pantaloneta, visera, tenis)' },
      { id: 'sem-4', text: 'Preparar productos Actimax (hidratante, pre race, protector solar, gel, recovery pro)' },
    ],
  },
  {
    phase: '2 días antes',
    items: [
      { id: 'd2-1', text: 'Descanso total' },
      { id: 'd2-2', text: 'Sesión de estiramiento' },
      { id: 'd2-3', text: 'Sesión de masaje' },
      { id: 'd2-4', text: 'Aumentar carbohidratos' },
      { id: 'd2-5', text: 'Desempacar y revisar uniforme' },
    ],
  },
  {
    phase: '1 día antes',
    items: [
      { id: 'd1-1', text: 'Siesta de 15 min máximo' },
      { id: 'd1-2', text: 'Sesión de estiramiento' },
      { id: 'd1-3', text: 'Alistar lo necesario' },
      { id: 'd1-4', text: 'Visualizar la carrera / meditación' },
      { id: 'd1-5', text: 'Cena de pastas' },
      { id: 'd1-6', text: 'Ir a dormir 8:30pm' },
      { id: 'd1-7', text: 'Mantenerse hidratado' },
    ],
  },
  {
    phase: 'Día de la carrera — Antes',
    items: [
      { id: 'dia-a1', text: 'Levantarse mínimo 1h 40min antes del inicio' },
      { id: 'dia-a2', text: 'Desayunar sin productos lácteos' },
      { id: 'dia-a3', text: 'Estirar en la habitación' },
      { id: 'dia-a4', text: 'Visualizar la prueba' },
      { id: 'dia-a5', text: 'Ejercicio frente al espejo' },
    ],
  },
  {
    phase: 'Día de la carrera — Durante',
    items: [
      { id: 'dia-d1', text: 'Pensamientos positivos, concentración' },
      { id: 'dia-d2', text: 'Enfoque' },
      { id: 'dia-d3', text: 'Sonreír y disfrutar' },
    ],
  },
  {
    phase: 'Día de la carrera — Después',
    items: [
      { id: 'dia-p1', text: 'Estirar y masaje con hielo' },
      { id: 'dia-p2', text: 'Hidratarse y tomar Recovery Pro Actimax' },
      { id: 'dia-p3', text: 'Analizar la prueba: aspectos positivos y a mejorar' },
    ],
  },
];
---

<section class="section plan-calc" id="mi-plan">
  <div class="section__inner">
    <h2 class="plan-calc__title reveal">Mi plan de hoy</h2>
    <p class="plan-calc__sub reveal">Planifica tu sesión y lleva lo que necesitas.</p>

    <!-- STEP 1: Age input + zones -->
    <div class="plan-step plan-step--age reveal" id="step-age">
      <h3 class="plan-step__heading">
        <span class="plan-step__number">1</span>
        Tu edad
      </h3>
      <div class="plan-step__body">
        <div class="age-input-group">
          <label for="calc-age" class="sr-only">Edad</label>
          <input
            type="number"
            id="calc-age"
            class="plan-input"
            placeholder="Ej: 30"
            min="10"
            max="99"
            aria-label="Tu edad en años"
            aria-describedby="age-hint"
            inputmode="numeric"
          />
          <span class="plan-input__suffix">años</span>
        </div>
        <p class="plan-hint" id="age-hint" aria-live="polite"></p>
      </div>

      <!-- Zones display (hidden until valid age) -->
      <div class="zones-display" id="zones-display" aria-hidden="true">
        <h4 class="zones-display__title">Tus zonas de entrenamiento</h4>
        <p class="zones-display__fcmax" id="fcmax-label"></p>
        <div class="zones-grid">
          {zones.map((z) => (
            <div class="zone-card" data-zone={z.id} style={`--zone-color: ${z.color}`}>
              <span class="zone-card__label">{z.label}</span>
              <span class="zone-card__range" id={`range-${z.id}`}></span>
              <span class="zone-card__name">{z.name}</span>
            </div>
          ))}
        </div>
        <p class="plan-disclaimer">Las zonas de frecuencia cardíaca se calculan con la fórmula estándar (220 - edad). Esta es una estimación general. Para un cálculo personalizado, consulta a un profesional de la salud deportiva.</p>
      </div>
    </div>

    <!-- STEP 2: Session config -->
    <div class="plan-step plan-step--session" id="step-session" aria-hidden="true">
      <h3 class="plan-step__heading">
        <span class="plan-step__number">2</span>
        Tu sesión
      </h3>
      <div class="plan-step__body">
        <!-- Activity type toggle -->
        <div class="toggle-group" role="radiogroup" aria-label="Tipo de actividad">
          <button class="toggle-btn toggle-btn--active" role="radio" aria-checked="true" data-type="entrenamiento">
            <Icon name="dumbbell" size={18} />
            Entrenamiento
          </button>
          <button class="toggle-btn" role="radio" aria-checked="false" data-type="carrera">
            <Icon name="activity" size={18} />
            Carrera
          </button>
        </div>

        <!-- Zone selector (only for training) -->
        <div class="zone-selector" id="zone-selector">
          <p class="zone-selector__label">Zona de intensidad</p>
          <div class="zone-buttons">
            {zones.map((z) => (
              <button
                class="zone-btn"
                data-zone-select={z.id}
                aria-pressed="false"
                style={`--zone-color: ${z.color}`}
              >
                {z.label}
              </button>
            ))}
          </div>
        </div>

        <!-- Duration input -->
        <div class="duration-group">
          <label for="calc-duration" class="duration-label">Duración</label>
          <div class="duration-input-group">
            <input
              type="number"
              id="calc-duration"
              class="plan-input"
              placeholder="Ej: 60"
              min="15"
              max="300"
              aria-label="Duración en minutos"
              aria-describedby="duration-hint"
              inputmode="numeric"
            />
            <span class="plan-input__suffix">min</span>
          </div>
          <p class="plan-hint" id="duration-hint" aria-live="polite"></p>
        </div>
      </div>
    </div>

    <!-- STEP 3: Results -->
    <div class="plan-step plan-step--results" id="step-results" aria-hidden="true" aria-live="polite">
      <h3 class="plan-step__heading">
        <span class="plan-step__number">3</span>
        Tu plan
      </h3>
      <div class="results-grid">
        <!-- Before -->
        <div class="result-card result-card--before">
          <div class="result-card__icon">
            <Icon name="clock" size={22} />
          </div>
          <h4 class="result-card__title">Antes</h4>
          <p class="result-card__detail">20 min antes de iniciar</p>
          <div class="result-card__product">
            <strong>1 Pre Race</strong>
          </div>
        </div>

        <!-- During: Energy -->
        <div class="result-card result-card--energy">
          <div class="result-card__icon">
            <Icon name="zap" size={22} />
          </div>
          <h4 class="result-card__title">Durante — Energía</h4>
          <p class="result-card__detail" id="result-gel-detail"></p>
          <div class="result-card__product" id="result-gel-product"></div>
        </div>

        <!-- During: Hydration -->
        <div class="result-card result-card--hydration">
          <div class="result-card__icon">
            <Icon name="droplet" size={22} />
          </div>
          <h4 class="result-card__title">Durante — Hidratación</h4>
          <p class="result-card__detail" id="result-hydration-detail"></p>
          <div class="result-card__product" id="result-hydration-product"></div>
        </div>

        <!-- After -->
        <div class="result-card result-card--after">
          <div class="result-card__icon">
            <Icon name="repeat" size={22} />
          </div>
          <h4 class="result-card__title">Después</h4>
          <p class="result-card__detail">En los primeros 30 min</p>
          <div class="result-card__product">
            <strong>1 Recovery Pro</strong> en 250 ml de agua
          </div>
        </div>
      </div>
    </div>

    <!-- STEP 4: Race checklist (conditional) -->
    <div class="plan-step plan-step--checklist" id="step-checklist" aria-hidden="true">
      <h3 class="plan-step__heading">
        <span class="plan-step__number">4</span>
        Checklist de carrera
      </h3>
      <div class="checklist-phases">
        {checklistData.map((phase) => (
          <div class="checklist-phase">
            <h4 class="checklist-phase__title">{phase.phase}</h4>
            <ul class="checklist-list">
              {phase.items.map((item) => (
                <li class="checklist-item">
                  <label>
                    <input type="checkbox" data-check={item.id} />
                    <span class="checklist-check" aria-hidden="true"></span>
                    <span class="checklist-text">{item.text}</span>
                  </label>
                </li>
              ))}
            </ul>
          </div>
        ))}
      </div>
      <div class="checklist-progress">
        <div class="checklist-progress__bar">
          <div class="checklist-progress__fill" id="checklist-fill"></div>
        </div>
        <span class="checklist-progress__text" id="checklist-count"></span>
      </div>
    </div>
  </div>
</section>
```

- [ ] **Step 2: Verify file was created**

Run: `ls src/components/PlanCalculator.astro`
Expected: file exists. Visual verification happens in Task 6 after mounting.

- [ ] **Step 3: Commit**

```bash
git add src/components/PlanCalculator.astro
git commit -m "feat: add PlanCalculator HTML structure"
```

---

### Task 4: Add scoped CSS styles to PlanCalculator

**Files:**
- Modify: `src/components/PlanCalculator.astro` (add `<style>` block after the HTML)

- [ ] **Step 1: Add the complete scoped CSS**

Add this `<style>` block after the closing `</section>` tag:

```css
<style>
  .plan-calc {
    background: var(--color-background);
  }

  .plan-calc__title {
    font-size: var(--text-2xl);
    text-align: center;
    margin-bottom: var(--space-sm);
  }

  .plan-calc__sub {
    text-align: center;
    font-size: var(--text-lg);
    color: var(--color-muted);
    margin-bottom: var(--space-2xl);
  }

  /* Steps */
  .plan-step {
    max-width: 600px;
    margin: 0 auto var(--space-xl);
    transition: opacity 0.4s var(--ease-out), max-height 0.5s var(--ease-out);
  }

  .plan-step[aria-hidden="true"] {
    opacity: 0;
    max-height: 0;
    overflow: hidden;
    margin: 0 auto;
    pointer-events: none;
  }

  .plan-step__heading {
    display: flex;
    align-items: center;
    gap: var(--space-md);
    font-size: var(--text-lg);
    margin-bottom: var(--space-lg);
    text-transform: none;
  }

  .plan-step__number {
    display: grid;
    place-items: center;
    width: 32px;
    height: 32px;
    border-radius: var(--radius-full);
    background: var(--color-energy);
    color: white;
    font-size: var(--text-sm);
    font-weight: 800;
    flex-shrink: 0;
  }

  .plan-step__body {
    display: flex;
    flex-direction: column;
    gap: var(--space-lg);
  }

  /* Inputs */
  .age-input-group,
  .duration-input-group {
    display: flex;
    align-items: center;
    gap: var(--space-sm);
  }

  .plan-input {
    width: 100px;
    padding: 0.6rem var(--space-md);
    border: 2px solid var(--color-warm-yellow-deep);
    border-radius: var(--radius-md);
    font-size: var(--text-lg);
    font-weight: 700;
    color: var(--color-navy);
    background: var(--color-surface);
    text-align: center;
    transition: border-color 0.2s;
  }

  .plan-input:focus {
    outline: none;
    border-color: var(--color-energy);
  }

  .plan-input.error {
    border-color: #E53935;
  }

  .plan-input__suffix {
    font-size: var(--text-base);
    color: var(--color-muted);
    font-weight: 600;
  }

  .plan-hint {
    font-size: var(--text-xs);
    color: #E53935;
    min-height: 1.2em;
  }

  .plan-disclaimer {
    font-size: var(--text-xs);
    color: var(--color-muted);
    font-style: italic;
    margin-top: var(--space-md);
  }

  /* Zones display */
  .zones-display {
    margin-top: var(--space-lg);
    transition: opacity 0.4s var(--ease-out);
  }

  .zones-display[aria-hidden="true"] {
    opacity: 0;
    max-height: 0;
    overflow: hidden;
  }

  .zones-display__title {
    font-size: var(--text-base);
    font-weight: 700;
    margin-bottom: var(--space-xs);
    text-transform: none;
  }

  .zones-display__fcmax {
    font-size: var(--text-sm);
    color: var(--color-muted);
    margin-bottom: var(--space-md);
  }

  .zones-grid {
    display: grid;
    grid-template-columns: repeat(5, 1fr);
    gap: var(--space-sm);
  }

  .zone-card {
    background: var(--color-surface);
    border-radius: var(--radius-md);
    padding: var(--space-sm) var(--space-xs);
    text-align: center;
    border-top: 3px solid var(--zone-color);
    box-shadow: var(--shadow-sm);
    display: flex;
    flex-direction: column;
    gap: 2px;
  }

  .zone-card__label {
    font-family: var(--font-display);
    font-weight: 800;
    font-size: var(--text-sm);
    color: var(--zone-color);
  }

  .zone-card__range {
    font-size: var(--text-xs);
    font-weight: 700;
    color: var(--color-navy);
  }

  .zone-card__name {
    font-size: 0.65rem;
    color: var(--color-muted);
    line-height: 1.3;
  }

  /* Toggle */
  .toggle-group {
    display: flex;
    gap: var(--space-sm);
  }

  .toggle-btn {
    flex: 1;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: var(--space-sm);
    padding: 0.7rem var(--space-md);
    border: 2px solid var(--color-warm-yellow-deep);
    border-radius: var(--radius-md);
    font-size: var(--text-base);
    font-weight: 600;
    color: var(--color-muted);
    background: var(--color-surface);
    cursor: pointer;
    transition: all 0.2s var(--ease-out);
  }

  .toggle-btn--active {
    border-color: var(--color-energy);
    color: var(--color-navy);
    background: var(--color-energy-light);
  }

  .toggle-btn:hover:not(.toggle-btn--active) {
    border-color: var(--color-energy);
    color: var(--color-navy);
  }

  /* Zone selector */
  .zone-selector {
    transition: opacity 0.3s, max-height 0.3s;
  }

  .zone-selector.hidden {
    opacity: 0;
    max-height: 0;
    overflow: hidden;
    margin: 0;
  }

  .zone-selector__label {
    font-size: var(--text-sm);
    font-weight: 600;
    color: var(--color-navy);
    margin-bottom: var(--space-sm);
  }

  .zone-buttons {
    display: flex;
    gap: var(--space-xs);
  }

  .zone-btn {
    flex: 1;
    padding: 0.5rem;
    border: 2px solid var(--color-warm-yellow-deep);
    border-radius: var(--radius-sm);
    font-family: var(--font-display);
    font-weight: 800;
    font-size: var(--text-sm);
    color: var(--color-muted);
    background: var(--color-surface);
    cursor: pointer;
    transition: all 0.2s;
  }

  .zone-btn[aria-pressed="true"] {
    border-color: var(--zone-color);
    color: var(--zone-color);
    background: color-mix(in srgb, var(--zone-color) 10%, white);
  }

  .zone-btn:hover:not([aria-pressed="true"]) {
    border-color: var(--zone-color);
    color: var(--zone-color);
  }

  /* Duration */
  .duration-label {
    font-size: var(--text-sm);
    font-weight: 600;
    color: var(--color-navy);
    margin-bottom: var(--space-sm);
    display: block;
  }

  .duration-group {
    display: flex;
    flex-direction: column;
  }

  /* Results */
  .results-grid {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    gap: var(--space-md);
  }

  .result-card {
    background: var(--color-surface);
    border-radius: var(--radius-lg);
    padding: var(--space-lg);
    box-shadow: var(--shadow-sm);
    display: flex;
    flex-direction: column;
    gap: var(--space-sm);
  }

  .result-card__icon {
    width: 40px;
    height: 40px;
    border-radius: var(--radius-sm);
    display: grid;
    place-items: center;
    color: white;
  }

  .result-card--before .result-card__icon { background: var(--color-energy); }
  .result-card--energy .result-card__icon { background: var(--color-energy); }
  .result-card--hydration .result-card__icon { background: var(--color-hydration); }
  .result-card--after .result-card__icon { background: var(--color-nutrition); }

  .result-card__title {
    font-size: var(--text-sm);
    font-weight: 700;
    letter-spacing: 0.05em;
  }

  .result-card__detail {
    font-size: var(--text-xs);
    color: var(--color-muted);
  }

  .result-card__product {
    font-size: var(--text-base);
    color: var(--color-navy);
    margin-top: auto;
  }

  .result-card__product strong {
    color: var(--color-energy);
  }

  .result-card__note {
    font-size: var(--text-xs);
    color: var(--color-muted);
    font-style: italic;
  }

  /* Checklist */
  .checklist-phases {
    display: flex;
    flex-direction: column;
    gap: var(--space-lg);
  }

  .checklist-phase__title {
    font-size: var(--text-base);
    font-weight: 700;
    color: var(--color-energy);
    margin-bottom: var(--space-sm);
    text-transform: none;
  }

  .checklist-list {
    display: flex;
    flex-direction: column;
    gap: var(--space-xs);
  }

  .checklist-item label {
    display: flex;
    align-items: center;
    gap: var(--space-md);
    padding: 0.5rem var(--space-md);
    border-radius: var(--radius-sm);
    cursor: pointer;
    transition: background 0.15s;
    user-select: none;
    -webkit-user-select: none;
  }

  .checklist-item label:hover {
    background: var(--color-warm-yellow);
  }

  .checklist-item input[type="checkbox"] {
    position: absolute;
    opacity: 0;
    width: 0;
    height: 0;
  }

  .checklist-check {
    width: 22px;
    height: 22px;
    border-radius: var(--radius-sm);
    border: 2px solid var(--color-navy);
    display: grid;
    place-items: center;
    flex-shrink: 0;
    transition: background 0.2s, border-color 0.2s, transform 0.2s var(--ease-spring);
  }

  .checklist-item input:checked + .checklist-check {
    background: var(--color-energy);
    border-color: var(--color-energy);
    transform: scale(1.1);
  }

  .checklist-item input:checked + .checklist-check::after {
    content: '';
    width: 6px;
    height: 10px;
    border: solid white;
    border-width: 0 2.5px 2.5px 0;
    transform: rotate(45deg) translate(-1px, -1px);
  }

  .checklist-item input:focus-visible + .checklist-check {
    outline: 2px solid var(--color-golden);
    outline-offset: 2px;
  }

  .checklist-text {
    font-size: var(--text-sm);
    color: var(--color-body-text);
    transition: color 0.2s, opacity 0.2s;
  }

  .checklist-item input:checked ~ .checklist-text {
    text-decoration: line-through;
    color: var(--color-muted);
    opacity: 0.7;
  }

  .checklist-progress {
    margin-top: var(--space-lg);
    text-align: center;
  }

  .checklist-progress__bar {
    height: 8px;
    background: var(--color-warm-yellow);
    border-radius: 4px;
    overflow: hidden;
    margin-bottom: var(--space-sm);
  }

  .checklist-progress__fill {
    height: 100%;
    width: 0%;
    background: var(--color-energy);
    border-radius: 4px;
    transition: width 0.4s var(--ease-out);
  }

  .checklist-progress__text {
    font-size: var(--text-sm);
    color: var(--color-muted);
    font-weight: 600;
  }

  /* Responsive */
  @media (max-width: 480px) {
    .zones-grid {
      grid-template-columns: repeat(3, 1fr);
    }

    .results-grid {
      grid-template-columns: 1fr;
    }

    .toggle-btn {
      font-size: var(--text-sm);
      padding: 0.6rem var(--space-sm);
    }

    .zone-btn {
      padding: 0.4rem;
      font-size: var(--text-xs);
    }

    .checklist-item label {
      gap: var(--space-sm);
      padding: 0.4rem var(--space-sm);
    }
  }
</style>
```

- [ ] **Step 2: Verify styles render correctly**

Run: `npm run dev`
Check that the section, step headings, inputs, and cards are styled properly on both desktop (1280px) and mobile (375px) viewports. Zones grid should show 5 columns on desktop, 3 on mobile.

- [ ] **Step 3: Commit**

```bash
git add src/components/PlanCalculator.astro
git commit -m "feat: add scoped CSS styles to PlanCalculator"
```

---

### Task 5: Add JavaScript logic to PlanCalculator

**Files:**
- Modify: `src/components/PlanCalculator.astro` (add `<script>` block after `<style>`)

- [ ] **Step 1: Add the complete script block**

Add this `<script>` block after the `</style>` tag:

```html
<script>
  // === DOM References ===
  const ageInput = document.getElementById('calc-age') as HTMLInputElement;
  const ageHint = document.getElementById('age-hint')!;
  const zonesDisplay = document.getElementById('zones-display')!;
  const fcmaxLabel = document.getElementById('fcmax-label')!;

  const stepSession = document.getElementById('step-session')!;
  const toggleBtns = document.querySelectorAll<HTMLButtonElement>('.toggle-btn');
  const zoneSelector = document.getElementById('zone-selector')!;
  const zoneBtns = document.querySelectorAll<HTMLButtonElement>('.zone-btn');
  const durationInput = document.getElementById('calc-duration') as HTMLInputElement;
  const durationHint = document.getElementById('duration-hint')!;

  const stepResults = document.getElementById('step-results')!;
  const stepChecklist = document.getElementById('step-checklist')!;

  // Results elements
  const resultGelDetail = document.getElementById('result-gel-detail')!;
  const resultGelProduct = document.getElementById('result-gel-product')!;
  const resultHydrationDetail = document.getElementById('result-hydration-detail')!;
  const resultHydrationProduct = document.getElementById('result-hydration-product')!;

  // Checklist elements
  const checklistBoxes = document.querySelectorAll<HTMLInputElement>('[data-check]');
  const checklistFill = document.getElementById('checklist-fill')!;
  const checklistCount = document.getElementById('checklist-count')!;

  // === State ===
  let activityType: 'entrenamiento' | 'carrera' = 'entrenamiento';
  let selectedZone: string | null = null;

  const ZONES = [
    { id: 'z1', pctLow: 0.5, pctHigh: 0.6 },
    { id: 'z2', pctLow: 0.6, pctHigh: 0.7 },
    { id: 'z3', pctLow: 0.7, pctHigh: 0.8 },
    { id: 'z4', pctLow: 0.8, pctHigh: 0.9 },
    { id: 'z5', pctLow: 0.9, pctHigh: 1.0 },
  ];

  // === Load persisted age ===
  const savedAge = localStorage.getItem('actimax-edad');
  if (savedAge) {
    ageInput.value = savedAge;
    processAge(parseInt(savedAge, 10));
  }

  // === Age input handler ===
  ageInput.addEventListener('input', () => {
    const val = parseInt(ageInput.value, 10);
    if (ageInput.value === '') {
      ageHint.textContent = '';
      ageInput.classList.remove('error');
      zonesDisplay.setAttribute('aria-hidden', 'true');
      stepSession.setAttribute('aria-hidden', 'true');
      stepResults.setAttribute('aria-hidden', 'true');
      stepChecklist.setAttribute('aria-hidden', 'true');
      return;
    }
    if (isNaN(val) || val < 10 || val > 99) {
      ageHint.textContent = 'Ingresa tu edad (10-99)';
      ageInput.classList.add('error');
      zonesDisplay.setAttribute('aria-hidden', 'true');
      stepSession.setAttribute('aria-hidden', 'true');
      stepResults.setAttribute('aria-hidden', 'true');
      stepChecklist.setAttribute('aria-hidden', 'true');
      return;
    }
    ageHint.textContent = '';
    ageInput.classList.remove('error');
    localStorage.setItem('actimax-edad', String(val));
    processAge(val);
  });

  function processAge(age: number) {
    const fcMax = 220 - age;
    fcmaxLabel.textContent = `FC máxima estimada: ${fcMax} lpm`;

    ZONES.forEach(z => {
      const low = Math.round(fcMax * z.pctLow);
      const high = Math.round(fcMax * z.pctHigh);
      const rangeEl = document.getElementById(`range-${z.id}`);
      if (rangeEl) rangeEl.textContent = `${low}–${high} lpm`;
    });

    zonesDisplay.setAttribute('aria-hidden', 'false');
    stepSession.setAttribute('aria-hidden', 'false');
  }

  // === Activity type toggle ===
  function activateToggle(btn: HTMLButtonElement) {
    toggleBtns.forEach(b => {
      b.classList.remove('toggle-btn--active');
      b.setAttribute('aria-checked', 'false');
      b.setAttribute('tabindex', '-1');
    });
    btn.classList.add('toggle-btn--active');
    btn.setAttribute('aria-checked', 'true');
    btn.setAttribute('tabindex', '0');
    btn.focus();
    activityType = btn.dataset.type as 'entrenamiento' | 'carrera';

    if (activityType === 'carrera') {
      zoneSelector.classList.add('hidden');
      selectedZone = null;
      zoneBtns.forEach(b => b.setAttribute('aria-pressed', 'false'));
    } else {
      zoneSelector.classList.remove('hidden');
    }

    tryCalculate();
  }

  toggleBtns.forEach(btn => {
    btn.addEventListener('click', () => activateToggle(btn));
  });

  // Keyboard arrow navigation for radiogroup (WAI-ARIA pattern)
  const toggleArr = Array.from(toggleBtns);
  toggleArr.forEach((btn, i) => {
    btn.addEventListener('keydown', (e: KeyboardEvent) => {
      let next: number | null = null;
      if (e.key === 'ArrowRight' || e.key === 'ArrowDown') next = (i + 1) % toggleArr.length;
      if (e.key === 'ArrowLeft' || e.key === 'ArrowUp') next = (i - 1 + toggleArr.length) % toggleArr.length;
      if (next !== null) {
        e.preventDefault();
        activateToggle(toggleArr[next]);
      }
    });
  });
  // Set initial tabindex: only active toggle is tabbable
  toggleBtns.forEach(b => b.setAttribute('tabindex', b.classList.contains('toggle-btn--active') ? '0' : '-1'));

  // === Zone selector ===
  zoneBtns.forEach(btn => {
    btn.addEventListener('click', () => {
      zoneBtns.forEach(b => b.setAttribute('aria-pressed', 'false'));
      btn.setAttribute('aria-pressed', 'true');
      selectedZone = btn.dataset.zoneSelect!;
      tryCalculate();
    });
  });

  // === Duration input ===
  durationInput.addEventListener('input', () => {
    const val = parseInt(durationInput.value, 10);
    if (durationInput.value === '') {
      durationHint.textContent = '';
      durationInput.classList.remove('error');
      stepResults.setAttribute('aria-hidden', 'true');
      stepChecklist.setAttribute('aria-hidden', 'true');
      return;
    }
    if (isNaN(val) || val < 15 || val > 300) {
      durationHint.textContent = 'Ingresa entre 15 y 300 minutos';
      durationInput.classList.add('error');
      stepResults.setAttribute('aria-hidden', 'true');
      stepChecklist.setAttribute('aria-hidden', 'true');
      return;
    }
    durationHint.textContent = '';
    durationInput.classList.remove('error');
    tryCalculate();
  });

  // === Calculate results ===
  function tryCalculate() {
    const duration = parseInt(durationInput.value, 10);
    if (isNaN(duration) || duration < 15 || duration > 300) return;

    // For training mode, need a zone selected
    if (activityType === 'entrenamiento' && !selectedZone) return;

    // Gel calculation
    let gelInterval: number;
    let gelNote: string;
    if (activityType === 'carrera') {
      gelInterval = 30;
      gelNote = '1 gel cada 30 min';
    } else if (selectedZone === 'z4' || selectedZone === 'z5') {
      gelInterval = 30;
      gelNote = `1 gel cada 30 min (${selectedZone!.toUpperCase()} alta intensidad)`;
    } else {
      gelInterval = 45;
      gelNote = `1 gel cada 45 min (${selectedZone!.toUpperCase()} intensidad moderada)`;
    }

    const gelCount = Math.floor(duration / gelInterval);

    resultGelDetail.textContent = gelNote;
    if (gelCount === 0) {
      resultGelProduct.innerHTML = '<span class="result-card__note">Para esta duración no necesitas gel, el Pre Race es suficiente</span>';
    } else {
      resultGelProduct.innerHTML = `<strong>${gelCount} gel${gelCount > 1 ? 'es' : ''}</strong> Actimax`;
    }

    // Hydration calculation
    const mlHidratacion = Math.max(Math.round((duration / 60) * 550), 250);
    const caramanolas = Math.ceil(mlHidratacion / 750);

    if (duration < 30) {
      resultHydrationDetail.textContent = 'Hidratación mínima recomendada';
    } else {
      resultHydrationDetail.textContent = `Sorbos cada 10 min · ~${caramanolas} caramañola${caramanolas > 1 ? 's' : ''}`;
    }
    resultHydrationProduct.innerHTML = `<strong>${mlHidratacion} ml</strong> Bebida Elite`;

    // Show results
    stepResults.setAttribute('aria-hidden', 'false');

    // Show checklist only for races
    if (activityType === 'carrera') {
      stepChecklist.setAttribute('aria-hidden', 'false');
    } else {
      stepChecklist.setAttribute('aria-hidden', 'true');
    }
  }

  // === Checklist persistence ===
  const today = new Date().toISOString().split('T')[0];
  const CHECKLIST_KEY = `actimax-checklist-carrera-${today}`;

  const savedChecklist: Record<string, boolean> = JSON.parse(localStorage.getItem(CHECKLIST_KEY) || '{}');
  checklistBoxes.forEach(cb => {
    const id = cb.dataset.check!;
    if (savedChecklist[id]) cb.checked = true;
  });
  updateChecklistProgress();

  checklistBoxes.forEach(cb => {
    cb.addEventListener('change', () => {
      const state: Record<string, boolean> = {};
      checklistBoxes.forEach(c => {
        state[c.dataset.check!] = c.checked;
      });
      localStorage.setItem(CHECKLIST_KEY, JSON.stringify(state));
      updateChecklistProgress();
    });
  });

  function updateChecklistProgress() {
    const total = checklistBoxes.length;
    const checked = document.querySelectorAll<HTMLInputElement>('[data-check]:checked').length;
    const pct = (checked / total) * 100;
    if (checklistFill) checklistFill.style.width = `${pct}%`;
    if (checklistCount) {
      checklistCount.textContent = checked === total
        ? `${checked} de ${total} items completados`
        : `${checked} de ${total} items`;
    }
  }
</script>
```

- [ ] **Step 2: Verify all interactive behavior works**

Run: `npm run dev`
Test the following flow:
1. Enter age 30 → zones appear with FC max 190, Z1 shows 95-114 lpm
2. Step 2 appears → select Entrenamiento, Z3, 90 min → results: 2 geles, 825ml
3. Switch to Carrera → zone selector hides, results: 3 geles, checklist appears
4. Enter age 25 min with Z2 → results: 0 geles with "Pre Race es suficiente" message
5. Refresh page → age is preserved from localStorage
6. Check some checklist items → refresh → state is preserved

- [ ] **Step 3: Commit**

```bash
git add src/components/PlanCalculator.astro
git commit -m "feat: add interactive JS logic to PlanCalculator"
```

---

### Task 6: Mount PlanCalculator in index.astro and wire up PillarNav

**Files:**
- Modify: `src/pages/index.astro:1-13` (add import) and `index.astro:25-26` (add component)

- [ ] **Step 1: Add import to index.astro**

Add after line 11 (`import PillarNutrition`):

```astro
import PlanCalculator from '../components/PlanCalculator.astro';
```

- [ ] **Step 2: Mount between PillarNutrition and ClosingSection**

Between `<PillarNutrition />` and `<ClosingSection />` (lines 25-26), add:

```astro
    <PlanCalculator />
```

- [ ] **Step 3: Verify full integration**

Run: `npm run dev`
1. Scroll down — "Mi Plan" section appears between Nutrición and the closing checklist
2. PillarNav shows "Mi Plan" tab with calculator icon
3. Clicking "Mi Plan" in nav scrolls to the section
4. Scroll past the section — nav highlights "Mi Plan" tab
5. Test on mobile viewport (375px) — nav has 6 items, all fit

- [ ] **Step 4: Commit**

```bash
git add src/pages/index.astro
git commit -m "feat: mount PlanCalculator in page and complete integration"
```

---

### Task 7: Final polish and edge case testing

**Files:**
- Possibly modify: `src/components/PlanCalculator.astro` (minor fixes if needed)

- [ ] **Step 1: Test all edge cases**

Run: `npm run dev` and verify:
1. Age boundary: enter 10 → works. Enter 9 → error. Enter 99 → works. Enter 100 → error.
2. Duration boundary: enter 15 → works. Enter 14 → error. Enter 300 → works. Enter 301 → error.
3. Short session: 15 min, Z1, Training → 0 geles message, 250ml minimum hydration
4. Long session: 300 min, Z5, Training → 10 geles, 2750ml, 4 caramañolas
5. Carrera mode: zone selector hidden, checklist visible
6. Switch back to Entrenamiento: zone selector reappears, checklist hides
7. localStorage: refresh preserves age but not session config
8. Checklist: check items, refresh, items still checked
9. Responsive: test 375px, 768px, 1280px viewports
10. Reduced motion: enable "prefers-reduced-motion" in devtools → animations disabled

- [ ] **Step 2: Run Astro build**

Run: `npm run build`
Expected: Build succeeds with no errors.

- [ ] **Step 3: Fix any issues found and commit**

```bash
git add -A
git commit -m "fix: address edge cases and polish PlanCalculator"
```

(Only run this step if fixes were needed.)
