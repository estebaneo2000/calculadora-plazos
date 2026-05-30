# 🌿 Calculadora de Plazos

Herramienta web para **calcular plazos procesales** en México (ámbito federal), construida durante el hackathon del Sprint *"Claude.ia para Abogados"* (Lawgic, 2026).

**▶ Demo en vivo:** https://calculadora-plazos.netlify.app

## ¿Qué hace?
Dado el **tipo de trámite**, la **fecha de notificación** y la **materia del acto**, calcula la **fecha de vencimiento** con el **desglose que la justifica**: la regla de surtimiento de efectos aplicada, el día 1 del cómputo y los días inhábiles descontados.

## Trámites soportados (MVP)
| Trámite | Plazo | Surtimiento de efectos | Fundamento |
|---|---|---|---|
| Recurso de revocación (fiscal) | 30 días hábiles | día hábil siguiente | CFF 121, 135, 12 |
| Recurso de revisión (administrativo) | 15 días hábiles | el mismo día | LFPA 85, 38, 28 |
| Juicio de nulidad (TFJA) | 30 días hábiles | según la ley del acto (CFF 135 / LFPA 38) | LFPCA 13, 74 |
| Amparo directo (vs. sentencia del TFJA) | 15 días hábiles | 3er día hábil (Boletín) | L. Amparo 17-19; LFPCA 65 |

## Características
- 📅 Calendario de días inhábiles **interactivo** (precargados 2026 + los que agregues con un clic).
- 🧾 **Desglose verificable** de cada cómputo.
- ⚖️ **No inventa fundamentos** — herramienta de apoyo, no sustituye el criterio profesional.

## Archivos
- `index.html` — la calculadora (HTML autónomo, sin dependencias; funciona offline).
- `Presentacion-Calculadora-de-Plazos.pptx` — presentación del proyecto.
- `REGLAS.md` — base de reglas y fundamentos verificados (textos vigentes de la Cámara de Diputados + calendario del TFJA).

## Aviso
Herramienta de apoyo. Verifica siempre contra el **texto vigente** de la ley y el **calendario oficial** del órgano correspondiente. Los calendarios de días inhábiles se publican y **actualizan cada año**.

---
Autor: **Esteban José Echeverría Ortegón** · Construida con Claude (Vero 🌿).
