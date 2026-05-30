# Calculadora de plazos — Base de reglas (MVP)

> Activo central de la herramienta (idea #1 del hackathon Lawgic). Trámites federales del MVP: **recurso de revocación** (CFF), **recurso de revisión** (LFPA), **juicio de nulidad** (LFPCA), **amparo directo** (Ley de Amparo).
> Reglas verificadas por Esteban (litigante) con textos vigentes de la Cámara de Diputados (2026-05-30). Fuera del MVP: amparo indirecto, ámbito estatal/municipal y materias especiales (seguridad social, etc.).
> 🌐 **Publicada (permanente, pública):** https://calculadora-plazos.netlify.app · archivo: `calculadora-plazos.html` (copia de despliegue en `publicar/index.html`).

## 🗺️ Tabla maestra de reglas

| Trámite | Ley | Plazo | Surtimiento de efectos del acto | Inicio del cómputo | Días inhábiles |
|---|---|---|---|---|---|
| **Recurso de revocación** | CFF | **30 días hábiles** (art. 121) | **día hábil siguiente** (art. 135) | día siguiente a que surta efectos | catálogo CFF art. 12 + vacaciones generales del **SAT** |
| **Recurso de revisión** | LFPA | **15 días hábiles** (art. 85) | **el mismo día** (notif. personal, art. 38) | día siguiente a que surta efectos | catálogo LFPA art. 28 + suspensiones de la **dependencia** (DOF) |
| **Juicio de nulidad** (vía tradicional) | LFPCA | **30 días hábiles** (art. 13) | **según la ley del acto** (art. 13-I-a): CFF 135 si fiscal (día hábil siguiente) · LFPA 38 si administrativo (mismo día) | día siguiente a que surta efectos | calendario del **TFJA** |
| **Amparo directo** (vs. sentencia del TFJA) | Ley de Amparo | **15 días hábiles** (art. 17) | **LFPCA art. 65**: **3er día hábil** tras publicación en Boletín Jurisdiccional (o día hábil siguiente si es personal) | día siguiente a que surta efectos (art. 18, "conforme a la ley del acto") | calendario del **TFJA** |

## 🔑 Hallazgos de diseño (lo que el motor debe modelar)
1. **Tres "velocidades" de surtimiento de efectos** — es la variable más tramposa:
   - **Mismo día** → LFPA art. 38.
   - **Día hábil siguiente** → CFF art. 135.
   - **Tercer día hábil** → LFPCA art. 65 (notificación por Boletín Jurisdiccional).
2. **El surtimiento se determina "conforme a la ley del acto"** (LFPCA 13-I-a; L. Amparo 18). El acto de origen define qué regla aplica. → En juicio de nulidad hay que preguntar la **materia del acto impugnado** (fiscal/administrativo) para elegir CFF 135 o LFPA 38.
3. **Los catálogos de días inhábiles difieren** entre leyes y entre órganos:
   - CFF (art. 12) usa "**lunes** de conmemoración" (1er lunes feb, 3er lunes mar, 3er lunes nov), no fechas fijas.
   - LFPA (art. 28) y Amparo (art. 19) usan **fechas fijas**; Amparo añade **12 de octubre** y **14 de septiembre** (no están en CFF/LFPA).
   - Por órgano: **SAT** (revocación) ≠ **TFJA** (nulidad y amparo directo) ≠ dependencia (revisión). Cada uno publica sus suspensiones/vacaciones.

## ⚙️ Esquema del motor (universal)
```
Notificación
  → aplicar regla de SURTIMIENTO (mismo día | día hábil sig. | 3er día hábil)
     [calculada sobre el calendario de inhábiles que corresponda]
  → día de INICIO = día hábil siguiente al surtimiento
  → contar N días hábiles (saltando inhábiles del calendario aplicable)
  → si el último día es inhábil, recorrer al siguiente hábil
  → FECHA DE VENCIMIENTO
  + desglose justificado (regla aplicada, días descontados)
```
Parámetros de entrada: tipo de trámite · fecha de notificación · forma de notificación (personal/Boletín/electrónica) · (para nulidad) materia del acto origen · año/órgano para el calendario.

## 📥 Calendarios de inhábiles
- **TFJA 2026**: ✅ Acuerdo SS/2/2026 → https://www.tfja.gob.mx/servicios/dinh2026/
- **SAT 2026**: festivos de ley (art. 12) + RMF **regla 2.1.6**: 2º periodo 2025 (18 dic 2025 – **2 ene 2026**) y **2 y 3 de abril de 2026**. ⚠️ El SAT **aún no publica su periodo de verano 2026** (lo emite tarde, modificando la RMF) → se marca a mano.
- **Dependencia** (revisión LFPA): suspensiones propias en DOF → se marcan a mano (caso irreductible: depende de ante qué autoridad se litiga).
- Los calendarios se **actualizan cada año** (no existe "saberlos para siempre"; se precarga el año vigente y se mantiene).

---

## 📚 Textos verificados (vigentes 2026)

### Recurso de revisión — LFPA
**Art. 28** — Días y horas hábiles; en plazos en días no se cuentan inhábiles (sáb, dom, 1 ene, 5 feb, 21 mar, 1 may, 5 may, 1 y 16 sep, 20 nov, 1 dic [transmisión Ejecutivo], 25 dic) + vacaciones generales / suspensiones publicadas en DOF. Habilitación de días inhábiles de oficio o a petición.
**Art. 38** — "Las notificaciones personales surtirán sus efectos **el día en que hubieren sido realizadas**. Los plazos empezarán a correr a partir del **día siguiente** a aquel en que haya surtido efectos la notificación."
**Art. 85** — "El plazo para interponer el recurso de revisión será de **quince días** contado a partir del día siguiente a aquél en que hubiere surtido efectos la notificación de la resolución que se recurra."

### Recurso de revocación — CFF
**Art. 12** — En plazos en días no se cuentan: sáb, dom, 1 ene, **1er lunes de feb** (conm. 5 feb), **3er lunes de mar** (conm. 21 mar), 1 y 5 may, 16 sep, **3er lunes de nov** (conm. 20 nov), 1 dic [transmisión], 25 dic, + vacaciones generales de autoridades fiscales (no escalonadas). Plazos por mes/año: concluyen el mismo día del mes/año posterior; si no existe, primer día hábil del mes siguiente. Si el último día las oficinas están cerradas o es inhábil, se prorroga al siguiente hábil.
**Art. 121** — "El recurso deberá presentarse a través del **buzón tributario**, dentro de los **treinta días** siguientes a aquél en que haya surtido efectos su notificación, excepto lo dispuesto en el artículo 127…" (suspensión hasta 1 año por fallecimiento del afectado).
**Art. 135** — "Las notificaciones surtirán sus efectos **al día hábil siguiente** en que fueron hechas…" (la manifestación de conocer el acto surte efectos de notificación desde esa fecha si es anterior).

### Juicio de nulidad — LFPCA
**Art. 13** — La demanda (vía tradicional o en línea) se presenta dentro de **treinta días** siguientes a aquél en que: **I, a)** "Que haya surtido efectos la notificación de la resolución impugnada, **lo que se determinará conforme a la ley aplicable a ésta**…"
**Art. 65** — Notificaciones por **Boletín Jurisdiccional** (aviso electrónico previo). "La notificación surtirá sus efectos al **tercer día hábil siguiente** a aquél en que se haya realizado la publicación en el Boletín Jurisdiccional o al **día hábil siguiente** a aquél en que las partes sean notificadas personalmente…"

### Amparo directo — Ley de Amparo
**Art. 17** — "El plazo para presentar la demanda de amparo es de **quince días**…" (con excepciones del propio artículo, no aplicables al directo ordinario).
**Art. 18** — "Los plazos… se computarán a partir del **día siguiente** a aquél en que surta efectos, **conforme a la ley del acto**, la notificación… o a aquella en que haya tenido conocimiento…"
**Art. 19** — Días hábiles: todos salvo sáb, dom, 1 ene, 5 feb, 21 mar, 1 y 5 may, **14 y 16 sep**, **12 oct**, 20 nov, 25 dic, y aquellos en que se suspendan labores en el órgano jurisdiccional.
> Criterio de Esteban: como el amparo directo se presenta ante la sala responsable (TFJA), el **surtimiento va por LFPCA art. 65** y los **inhábiles a considerar son los del TFJA**.
