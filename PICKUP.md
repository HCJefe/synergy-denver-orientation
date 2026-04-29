# SYNERGY Denver Orientation Video - Resume Guide

## Status (as of 04/29/2026)

PAUSED at HeyGen render step. All assets ready, just need someone/something to execute the 15 HeyGen API renders.

## What's Done

- 15 HeyGen-optimized scripts in `scripts.json` and `scripts.txt`
- 15 stellar composite backgrounds in `composites/scene-01.png` through `scene-15.png` (1920x1080, pre-branded)
- Test render proven: https://app.heygen.com/share/bf315e26714045a6a0a0e65e17bfc2b8
- Preview deliverables in `preview/`:
  - `SYNERGY-Denver-Orientation-PREVIEW.pptx` - full deck with scripts as speaker notes
  - `SYNERGY-Denver-Orientation.html` - self-narrating viewer using browser TTS

## What's Needed

Render 15 scenes via HeyGen API, then composite per recipe.

### HeyGen Config
- API key: in `/etc/esmeralda/dashboard.env` on Nerve Center VPS, or ask Rob
- Voice ID (Rachel): `48b19fac1a654281a2326773fe022f9d`
- Avatar: Esmeralda Lopez "Synergy Scarlet Chic" - lookup via GET /v2/avatars, find talking_photos with name containing 'esmeralda' or 'synergy'

### Render Payload (for each of 15 scenes)
```json
{
  "video_inputs": [{
    "character": {
      "type": "talking_photo",
      "talking_photo_id": "<ESME_TP_ID>",
      "talking_photo_style": "square",
      "talking_style": "stable",
      "expression": "happy"
    },
    "voice": {
      "type": "text",
      "voice_id": "48b19fac1a654281a2326773fe022f9d",
      "input_text": "<scene N script from scripts.json>",
      "speed": 1.0
    },
    "background": { "type": "color", "value": "#00FF00" }
  }],
  "dimension": {"width": 1080, "height": 1920},
  "test": false,
  "title": "SYNERGY Denver Scene N"
}
```

### Composite Recipe (per scene)
```
ffmpeg -y \
  -i composites/scene-NN.png \
  -i raw/scene-NN.mp4 \
  -filter_complex \
  "[1:v]chromakey=0x00FF00:0.12:0.08,despill=type=green:mix=0.6:expand=0.5,scale=480:854[fg]; \
   [0:v]scale=1920:1080[bg]; \
   [bg][fg]overlay=0:226:format=auto[v]; \
   [1:a]loudnorm=I=-16:TP=-1.5:LRA=11,aresample=48000[a]" \
  -map "[v]" -map "[a]" \
  -c:v libx264 -preset slow -crf 18 -pix_fmt yuv420p \
  -c:a aac -b:a 192k -ar 48000 \
  final/scene-NN-final.mp4
```

### Concat all 15 + intro + outro + burn captions
See the detailed spec emailed to Hank (also stored in `SPEC.md` if present).

## Final Delivery
Push `SYNERGY-Denver-Orientation-CAPTIONED.mp4` to `final/` folder in this repo.
Email Rob at **rob@yumahomecare.com** (NOT robdunn.com - mailbox history of bouncing).

## Why Paused
Computer sandbox has TLS firewall blocking api.heygen.com, browser automation sessions crash on HeyGen auth, Hank agent silent via email and Telegram. Render requires either (a) Hank's server reaching the task, (b) Rob manually clicking through HeyGen UI, or (c) a future session with working HeyGen API access.
