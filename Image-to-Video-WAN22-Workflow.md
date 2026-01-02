# Full Image to Video Workflow (WAN 2.2)

> Quelle: [CivitAI Article](https://civitai.com/articles/24175/full-image-to-video-workflow-walkthrough) von FuzzySnakes (Dec 2025)

## Übersicht

Kompletter Workflow für die Erstellung von Animationen aus Bildern mit WAN 2.2. Funktioniert auf minimaler Hardware (4GB VRAM).

---

## Drei-Stufen Workflow

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Step 1: SDXL  │ -> │  Step 2: Flux   │ -> │  Step 3: WAN    │
│  Generate Base  │    │  Refine Image   │    │  Animate Video  │
│     Image       │    │   (Optional)    │    │                 │
└─────────────────┘    └─────────────────┘    └─────────────────┘
```

---

## Step 1: Base Image generieren (SDXL)

### Prozess
- Steps 2-3 zunächst bypassen
- Image + Text Prompts verwenden (oder Wildcards)
- Generieren bei **1024x1024** (quadratisch) oder **768x1344** (9:16 vertikal)

### Empfohlene Checkpoints
| Model | Beschreibung |
|-------|--------------|
| WAI-illustrious-SDXL v16.0 | Illustrious Style |
| CyberRealistic Pony v15.0 | Realistischer Style |
| SDXL VAE | Von Stability AI |

### Einstellungen
- **Denoise:** 0.4-0.6 zwischen Model-Passes
- **WD14 Tagger** für automatische Tag-Generierung
- Detailer Nodes mit BBOX/SEGM Models konfigurieren

---

## Step 2: Image verfeinern (Flux - Optional)

### Zweck
Bestimmte Bildbereiche polieren/verbessern

### Benötigte Models
```
ComfyUI/models/
├── checkpoints/
│   └── FLUX Dev checkpoint
├── clip/
│   └── t5xxl_fp8_e4m3fn.safetensors
└── vae/
    └── ae.safetensors (Flux VAE)
```

### Prozess
1. Step 1 Output laden
2. Maske auf zu verfeinernde Bereiche malen
3. Wiederholen bis zufrieden

### Denoise Einstellungen nach Style
| Style | Denoise |
|-------|---------|
| Realistisch | 0.5-0.65 |
| 3D | 0.6 |
| Anime/Cartoon | 0.4 |

> **Warnung:** "If you mask out areas such as skin...it will usually completely ruin the image"

---

## Step 3: Image animieren (WAN 2.2)

### Input Resolution
| Aspect Ratio | Resolution | Frames |
|--------------|------------|--------|
| 1:1 (quadratisch) | 636x636 | 100 |
| 9:16 (vertikal) | 480x848 | 100 |

> 100 Frames = ca. 6 Sekunden finales Video

### Model Konfiguration
```
ComfyUI/models/
├── diffusion_models/
│   └── Smooth Mix Wan 2.2 merge
├── clip/
│   └── umt5_xxl_fp8_e4m3fn_scaled.safetensors
└── vae/
    └── wan_2.1_vae.safetensors
```

### KSampler Einstellungen
| Parameter | Wert |
|-----------|------|
| Steps | 6 |
| CFG | 1 |
| Sampler | Euler |
| Scheduler | Simple |
| Shift | 8-11 |

### LoRA Setup
- High-noise und Low-noise Versionen hinzufügen
- Oder nur High-noise für mehr Bewegung

---

## Technische Details

### Auto-Tagging
- **WD14 Tagger:** Generiert automatisch Prompts aus Bildern
- **Video Tagger:** Für Video-basierte Prompts

### Frame Interpolation
- Finales Video wird über die gesampelten Frames hinaus erweitert
- 100 Frames → längeres interpoliertes Video

### Custom Nodes
- Alle verfügbar über **ComfyUI Manager**
- Auto-Install Option verfügbar

---

## Hardware Anforderungen

| VRAM | Ergebnis |
|------|----------|
| 4GB (Minimum) | Funktioniert, längere Generierungszeit |
| 8GB+ | Komfortabel |
| 12GB+ | Schnell, längere Videos möglich |

---

## Download & Installation

1. **Workflow ZIP** von CivitAI herunterladen
2. `.png` Workflow in ComfyUI Canvas ziehen
3. ComfyUI Manager installiert fehlende Nodes automatisch

---

## Workflow Kombinationsmöglichkeiten

Dieser I2V Workflow kann mit dem Face Swap Workflow kombiniert werden:

```
Face Swap (Qwen 2511 + F2P)
        ↓
   Refined Image
        ↓
 WAN 2.2 Animation
        ↓
   Final Video
```

---

## Ressourcen

- [Original Artikel auf CivitAI](https://civitai.com/articles/24175/full-image-to-video-workflow-walkthrough)
- [WAN 2.2 Models](https://civitai.com/models/wan-2.2)
- [Flux Dev](https://huggingface.co/black-forest-labs/FLUX.1-dev)
- [ComfyUI Manager](https://github.com/ltdrdata/ComfyUI-Manager)
