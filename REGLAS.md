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

---

# 🏛️ Estatal (Yucatán) y 🏙️ Municipal (Mérida)
> Verificado por Esteban (2026-05-31) con textos vigentes. Calendarios oficiales 2026: **TJAEY** (Acuerdo V/12-11-2025, DOGEY 28-nov-2025) y **Tribunal de lo Contencioso Admvo. del Mpio. de Mérida** (Acuerdo 2026).

## Tabla estatal / municipal
| Trámite | Plazo | Surtimiento de efectos | Calendario | Fundamento |
|---|---|---|---|---|
| Recurso de revocación (fiscal, Yuc.) | 30 días háb. | día hábil siguiente | Cód. Fiscal Edo. (festivos art. 15) + vacaciones fiscales | **CFEY** 141, 157, 15 |
| Recurso de revisión (admin., Yuc.) | 15 días háb. | el mismo día | LAPAEY (festivos art. 55) + suspensiones | **LAPAEY** 128, 68, 55 |
| Juicio de nulidad (TJAEY) | 15 días háb. | según la ley del acto (CFEY 157 / LAPAEY 68) | **TJAEY** | **LCAEY** 12 |
| Amparo directo (vs. TJAEY) | 15 días háb. | día hábil siguiente (LCAEY 71) | TJAEY | L. Amparo 17-18 |
| Recurso de reconsideración (Mérida) | 10 días háb. | **el mismo día** (RAPAMM 51) — el reglamento rige también el *procedimiento* fiscal mpal. (art. 1) | dependencia mpal. (RAPAMM 38) | **LGMEY** 179 |
| Recurso de revisión (Trib. Mpal.) | 15 días háb. | **el mismo día** (RAPAMM 51, fiscal y admin. por igual) | **Trib. Contencioso Mpal. de Mérida** | **RCAMM** 13; LGMEY 177 |
| Amparo directo (vs. Trib. Mpal.) | 15 días háb. | día hábil siguiente (⚠️ confirmar art.) | Trib. Mpal. | L. Amparo 17-18; cómputo RCAMM 88 |

## Hallazgos
- **Reconsideración municipal:** mismo plazo (10 días, LGMEY 179) y **mismo surtimiento** (el mismo día, RAPAMM 51) en fiscal y admin — el reglamento excluye solo el *fondo* fiscal (impuestos y accesorios, art. 1), no el procedimiento (criterio de Esteban, 2026-05-31).
- **Municipal vs. estatal (surtimiento fiscal):** en el **municipio** TODO el procedimiento va por el RAPAMM (mismo día), así que **tanto la reconsideración como la revisión municipal surten el mismo día**, sin distinguir materia. A nivel **estatal** sí se distingue: el Cód. Fiscal del Edo. (art. 157) da *día hábil siguiente* en lo fiscal vs. LAPAEY 68 *mismo día* en lo admin. → por eso **solo el juicio de nulidad estatal (TJAEY) pregunta la materia**. (Federal: igual, la nulidad TFJA pregunta materia por CFF 135 vs. LFPA 38.)
- **Catálogos de festivos estatales difieren:** el **fiscal** (CFEY 15) incluye **5 de mayo** y NO carnaval; el **administrativo** (LAPAEY 55 / RAPAMM 38) incluye **martes de carnaval** (17 feb 2026) y NO 5 de mayo. (Transmisión Ejecutivo estatal = 1 oct cada 6 años → próxima 2030.)
- **Amparo directo (los 3 niveles):** se computa con el calendario del **tribunal responsable** (TFJA / TJAEY / Trib. Mpal.) y surtimiento conforme a la ley de ese tribunal — por la misma lógica del criterio federal.

## Calendarios 2026 (inhábiles especiales, además de sáb/dom)
- **TJAEY:** 2 feb · 16-17 feb (carnaval) · 16 mar · 2-3 abr · 1 may · 4 may (servidor público) · 5 may · 16 jul + 17-31 jul (vac.) · 16 sep · 1-2 nov · 16 nov · 16 dic + 17-31 dic (vac.) · 25 dic.
- **Trib. Contencioso Mpal. de Mérida:** 1, 2, 5, 6, 7 ene (incl. cambio de sede) · 16 ene (día del empleado) · 2 feb · 16-17 feb (carnaval) · 16 mar · 1-3 abr · 1 may · 5 may · 16-31 jul (vac.) · 16 sep · 1-2 nov · 16 nov · 16-31 dic (vac.) · 25 dic.
- **Festivos de ley fiscal estatal (CFEY 15):** 1 ene, 2 feb, 16 mar, 1 may, 5 may, 16 sep, 16 nov, 25 dic.
- **Festivos de ley admin. estatal/mpal. (LAPAEY 55 / RAPAMM 38):** 1 ene, 2 feb, 17 feb (carnaval), 16 mar, 1 may, 16 sep, 16 nov, 25 dic.

## ✅ Confirmado con Esteban (2026-05-31)
1. **Surtimiento fiscal municipal = el mismo día** (RAPAMM 51): el reglamento excluye solo el *fondo* fiscal (impuestos y accesorios), no el *procedimiento* (notificación/surtimiento). Además es la lectura **prudente** para plazos (la más corta → no arriesga presentar tarde). *Blindar en caso real con la Ley de Hacienda Mpal. o criterio del Tribunal.*
2. Los **10 días** del recurso de reconsideración se cuentan en **hábiles**. ✅
3. **Amparo estatal/municipal:** se computa con el calendario del **tribunal responsable** (TJAEY / Trib. Mpal.) y surtimiento al **día hábil siguiente**. ✅

## Textos verificados (Esteban, 2026-05-31)
- **LAPAEY** (admin. estatal): art. 55 (días/horas hábiles + catálogo) · art. 68 (notif. surte el mismo día; plazos al día hábil siguiente) · art. 128 (revisión = 15 días háb.).
- **CFEY** (fiscal estatal): art. 15 (cómputo + catálogo) · art. 141 (revocación = 30 días) · art. 157 (notif. surte al día hábil siguiente; electrónica al 3er día).
- **LCAEY** (contencioso estatal): art. 12 (demanda 15 días háb.; 45 días si extranjero/sin representante/fallece; negativa ficta en cualquier tiempo tras 90 días) · art. 71 (notif. surte al día hábil siguiente; por lista, el día hábil siguiente al fijado).
- **LGMEY** (mpal.): art. 177 (recursos: reconsideración y revisión) · art. 179 (reconsideración = 10 días, ante el órgano que dictó el acto).
- **RAPAMM** (reglamento admin. Mérida): art. 38 (catálogo) · art. 51 (notif. surte el mismo día; plazos al día hábil siguiente) · art. 1 (excluye la materia fiscal).
- **LHMM** (hacienda mpal.): art. 8 (contra actos fiscales mpales. proceden los recursos de la LGMEY; multas federales no fiscales → CFF/LFPCA).
- **RCAMM** (contencioso Mérida): art. 13 (revisión = 15 días) · art. 88 (cómputo: inicia al día hábil siguiente; en días, solo hábiles).

---

# ⏱️ Plazos intraprocesales (juicio/procedimiento ya iniciado)
> Modo añadido 2026-05-31. **Clave:** el surtimiento intraprocesal lo manda la **ley procesal del foro** (no la ley del acto de origen) y puede diferir del plazo inicial. El **número de días** lo fija el acuerdo del tribunal → se captura (no se cataloga).

| Foro | Surtimiento intraprocesal | Calendario | Fundamento |
|---|---|---|---|
| Juicio TFJA | Boletín → 3er día hábil; personal → día hábil sig. | TFJA | LFPCA 65 |
| Juicio TJAEY | día hábil siguiente | TJAEY | LCAEY 71 |
| Trib. Contencioso Mpal. | día hábil siguiente | TCAMM | RCAMM 88 |
| Recurso/proc. fiscal federal | día hábil siguiente | CFF | CFF 135 |
| Recurso/proc. admin. federal | el mismo día | LFPA | LFPA 38 |
| Recurso/proc. fiscal estatal | día hábil siguiente | FISC_YUC | CFEY 157 |
| Recurso/proc. admin. estatal | el mismo día | ADM_YUC | LAPAEY 68 |
| Proc./recurso municipal | el mismo día | ADM_YUC | RAPAMM 51 |
| Juicio de amparo | día hábil siguiente (lista/personal) | **PJF** | L. Amparo 31-II y 18; inhábiles art. 19 |

- **Diferencia clave vs. inicio:** en el juicio TFJA, el plazo *inicial* surte por la ley del acto (CFF/LFPA), pero el *intraprocesal* por **Boletín (LFPCA 65)**. Y el **amparo intraprocesal** corre ante el Colegiado → calendario **PJF**, no el del tribunal responsable.
- **Fuera del MVP:** plazos en horas y plazos "comunes".

## Calendario PJF 2026 (acuerdo OAJ)
Inhábiles (no corren términos), además de sáb/dom: **1 ene · 2 y 5 feb · 16 mar · 1-3 abr · 1, 4 y 5 may · 16-31 jul (vac.) · 14, 15 y 16 sep · 12 oct · 2, 16 y 20 nov · 16-31 dic (vac.)**.
> ⚠️ **"Inhábil pero laborable"** (5 feb, 20 nov): el tribunal **labora** (recibe promociones) pero **NO corren los plazos** → cuentan como **inhábiles** para el cómputo. (Aclaración de Esteban, 2026-05-31.)
