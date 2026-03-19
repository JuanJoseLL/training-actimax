# Mi Plan de Hoy - Calculadora Interactiva

## Overview

Nueva sección interactiva en el sitio de "El Entrenamiento Invisible" que integra tres herramientas en una sola calculadora: zonas de entrenamiento por edad, consumo de productos Actimax, y checklist de carrera. Se ubica como sección dedicada justo antes del `ClosingSection`, convirtiendo el sitio de informativo a herramienta diaria.

## Motivación

El atleta necesita una herramienta práctica para planificar su sesión diaria: saber en qué zona de FC entrenar, cuántos geles llevar, cuánta hidratación preparar. La misma duración de entrenamiento produce recomendaciones distintas según la intensidad (Z2 suave vs Z5 intervalos).

## Arquitectura

### Componente

- **Archivo:** `src/components/PlanCalculator.astro`
- **Ubicación en página:** Entre `PillarNutrition` y `ClosingSection` en `index.astro`
- **ID de sección:** `#mi-plan`
- **Tech:** Astro component con vanilla JS (consistente con el resto del sitio)

### Entrada a PillarNav

Agregar un nuevo ítem en la barra de navegación sticky para la sección "Mi Plan".

## Flujo de Interacción

El componente usa revelación progresiva: cada paso aparece al completar el anterior.

### Paso 1: Edad

- Input numérico (rango 10-99)
- Se persiste en `localStorage` key `actimax-edad`
- Al ingresar edad, se calcula FC máxima: `220 - edad`
- Se muestran las 5 zonas de entrenamiento en tarjetas con colores:

| Zona | % FC máx | Rango | Descripción |
|------|----------|-------|-------------|
| Z1 | 50-60% | fcMax*0.5 – fcMax*0.6 | Recuperación / Regeneración |
| Z2 | 60-70% | fcMax*0.6 – fcMax*0.7 | Manejo de peso / Quema de grasa |
| Z3 | 70-80% | fcMax*0.7 – fcMax*0.8 | Capacidad aeróbica / Cardiovascular |
| Z4 | 80-90% | fcMax*0.8 – fcMax*0.9 | Umbral anaeróbico / Mejora considerable |
| Z5 | 90-100% | fcMax*0.9 – fcMax*1.0 | Velocidad y potencia / Esfuerzo máximo |

### Paso 2: Configuración de Sesión

- **Tipo de actividad:** Toggle visual Entrenamiento / Carrera
- **Zona de intensidad:** Selector Z1-Z5 (solo visible en modo Entrenamiento; en Carrera no aplica porque el gel siempre es cada 30 min)
- **Duración:** Input numérico en minutos (rango 15-300)

### Paso 3: Resultados

Tarjetas por fase temporal con productos Actimax:

**Antes:**
- 1 Pre Race, 20 minutos antes de iniciar

**Durante - Energía (Geles):**
- Entrenamiento Z1-Z3: 1 gel cada 45 min → `Math.floor(duración / 45)` geles
- Entrenamiento Z4-Z5: 1 gel cada 30 min → `Math.floor(duración / 30)` geles
- Carrera: 1 gel cada 30 min → `Math.floor(duración / 30)` geles

**Durante - Hidratación:**
- 500-600 ml de Bebida Elite por hora (sorbos cada 10 min)
- Cálculo: `Math.ceil(duración / 60) * 500` a `Math.ceil(duración / 60) * 600` ml
- Expresado también en caramañolas (~750ml c/u): `Math.ceil(mlTotal / 750)`

**Después (primeros 30 min):**
- 1 Recovery Pro en 250 ml de agua

### Paso 4: Checklist de Carrera (condicional)

Solo visible cuando el tipo de actividad es "Carrera". Checkboxes interactivos organizados en 4 fases:

**Semana previa:**
1. Hacer supercompensación
2. Aumentar carbohidratos (pastas)
3. Verificar estado del equipo (uniforme, gafas, camiseta, pantaloneta, visera, tenis)
4. Preparar productos Actimax (hidratante, pre race, protector solar, gel, recovery pro)

**2 días antes:**
1. Descanso total
2. Sesión de estiramiento
3. Sesión de masaje
4. Aumentar carbohidratos
5. Desempacar y revisar uniforme

**1 día antes:**
1. Siesta de 15 min máximo
2. Sesión de estiramiento
3. Alistar lo necesario
4. Visualizar la carrera / meditación
5. Cena de pastas
6. Ir a dormir 8:30pm
7. Mantenerse hidratado

**Día de la carrera - Antes:**
1. Levantarse mínimo 1h 40min antes del inicio
2. Desayunar sin productos lácteos
3. Estirar en la habitación
4. Visualizar la prueba
5. Ejercicio frente al espejo

**Día de la carrera - Durante:**
1. Pensamientos positivos, concentración
2. Enfoque
3. Sonreír y disfrutar

**Día de la carrera - Después:**
1. Estirar y masaje con hielo
2. Hidratarse y tomar Recovery Pro Actimax
3. Analizar la prueba: aspectos positivos y a mejorar

**Persistencia:** localStorage key `actimax-checklist-carrera-YYYY-MM-DD`

## Estilo Visual

- Sigue el design system existente (CSS custom properties de `global.css`)
- Color accent primario: naranja energía `#FF9800`
- Color secundario: azul hidratación `#2196F3`
- Tarjetas de zonas con gradientes sutiles por zona
- Cards animadas con scroll-reveal (`animation-timeline: view()`)
- Pasos se revelan con transición suave (height + opacity)
- Responsive: mobile-first, cards en stack vertical en mobile, grid en desktop
- Soporte `prefers-reduced-motion`

## Persistencia (localStorage)

| Key | Valor | Propósito |
|-----|-------|-----------|
| `actimax-edad` | número | Edad del usuario (no pedir de nuevo) |
| `actimax-checklist-carrera-YYYY-MM-DD` | JSON array de booleans | Estado del checklist por fecha |

## Integración con el Sitio

1. Agregar `PlanCalculator` en `index.astro` entre `PillarNutrition` y `ClosingSection`
2. Agregar ícono y enlace en `PillarNav.astro` para la sección `#mi-plan`
3. Agregar ícono de calculadora/plan en `Icon.astro`

## Consideraciones

- No requiere dependencias adicionales, todo es vanilla JS + CSS
- La fórmula FC máx = 220 - edad es una aproximación estándar, no un diagnóstico médico (incluir disclaimer)
- El checklist de carrera es una herramienta de preparación, el de hábitos del ClosingSection se mantiene separado
