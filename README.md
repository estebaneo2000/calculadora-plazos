# 🌿 Calculadora de Plazos

Herramienta web para **calcular plazos procesales** en México, nacida en el hackathon *"Claude.ia para Abogados"* (Lawgic, 2026) y ampliada después.

**▶ Demo en vivo:** https://calculadora-plazos.netlify.app

## ¿Qué hace?
Calcula la **fecha de vencimiento** de un plazo, con el **desglose que la justifica** (regla de surtimiento de efectos aplicada, día 1 del cómputo y días inhábiles descontados). Dos modos:

- **Inicio de un medio de defensa** — recursos, juicios de nulidad y amparo directo.
- **Plazo intraprocesal** — dentro de un juicio/procedimiento ya iniciado: eliges el foro, capturas los días que fijó el acuerdo, y aplica la regla de surtimiento de ese foro.

## Cobertura — tres niveles + amparo
**Federal · Estatal (Yucatán) · Municipal (Mérida)**

Trámites de inicio:
| Trámite | Plazo | Surtimiento |
|---|---|---|
| Recurso de revocación (CFF) | 30 días háb. | día hábil siguiente |
| Recurso de revisión (LFPA) | 15 días háb. | el mismo día |
| Juicio de nulidad (TFJA) | 30 días háb. | según la ley del acto |
| Amparo directo (vs. TFJA) | 15 días háb. | 3er día hábil (Boletín) |
| Recurso de revocación (CFEY, Yuc.) | 30 días háb. | día hábil siguiente |
| Recurso de revisión (LAPAEY, Yuc.) | 15 días háb. | el mismo día |
| Juicio de nulidad (TJAEY) | 15 días háb. | según la ley del acto |
| Amparo directo (vs. TJAEY) | 15 días háb. | día hábil siguiente |
| Reconsideración (Mérida) | 10 días háb. | el mismo día |
| Revisión (Trib. Contencioso Mpal.) | 15 días háb. | el mismo día |
| Amparo directo (vs. Trib. Mpal.) | 15 días háb. | día hábil siguiente |

**Plazos intraprocesales:** TFJA, TJAEY, Trib. Contencioso Mpal., recursos en sede administrativa (CFF / LFPA / CFEY / LAPAEY / municipal) y amparo (PJF).

## Características
- 📅 Calendario de días inhábiles **interactivo** — TFJA, TJAEY, Trib. de Mérida, PJF, SAT y festivos de ley, precargados **2026**; agrega los tuyos con un clic.
- 🧾 **Desglose verificable** de cada cómputo.
- ⚖️ **No inventa fundamentos** — herramienta de apoyo, no sustituye el criterio profesional.

## Archivos
- `index.html` — la calculadora (HTML autónomo, sin dependencias).
- `REGLAS.md` — base de reglas y fundamentos verificados (federal, Yucatán, Mérida, amparo).
- `Presentacion-Calculadora-de-Plazos.pptx` — presentación del proyecto.

## Aviso
Herramienta de apoyo. Verifica siempre contra el **texto vigente** de la ley y el **calendario oficial** del órgano correspondiente. Los calendarios se publican y **actualizan cada año**.

---
Autor: **Esteban José Echeverría Ortegón** · Construida con Claude (Vero 🌿).
