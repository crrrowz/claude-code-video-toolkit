# 🎬 دليل شامل: إنتاج فيديوهات يوتيوب وإنستغرام باستخدام Claude Code Video Toolkit

---

## 📌 ما هي هذه الأداة؟

**Claude Code Video Toolkit** هو مشروع مفتوح المصدر يحوّل وكيل الذكاء الاصطناعي (Claude Code أو Antigravity/Gemini) إلى **استوديو إنتاج فيديو كامل**. أنت تكتب الفكرة — والأداة تُنتج:

- ✍️ سيناريو الفيديو (Script)
- 🎙️ التعليق الصوتي بالذكاء الاصطناعي (AI Voiceover)
- 🖼️ الصور والخلفيات (AI Image Generation)
- 🎬 مقاطع فيديو متحركة (AI Video Clips)
- 🎵 الموسيقى الخلفية (AI Music)
- 📝 ترجمة محروقة على الفيديو (Burned Captions)
- 📦 ملف MP4 جاهز للنشر

> [!IMPORTANT]
> هذه الأداة **ليست تطبيقاً بواجهة رسومية** — هي مجموعة سكربتات وقوالب تعمل من الـ Terminal بإدارة وكيل ذكاء اصطناعي (مثل Antigravity أو Claude Code). أنت تصف ما تريد، والوكيل ينفذ الخطوات.

---

## 🗺️ الخريطة العامة — من الفكرة إلى النشر

```
الفكرة ──► السيناريو ──► الصور/المقاطع ──► التعليق الصوتي ──► الترجمة ──► التجميع (Render) ──► النشر
                │              │                    │                │              │
           scenes.json    tools/flux2.py       tools/voiceover.py  gen_captions.py  build.py
                           tools/ltx2.py        tools/qwen3_tts.py                  أو npm run render
                           tools/ideogram4.py
```

---

## 📁 هيكل المشروع

```
claude-code-video-toolkit/
├── templates/              ← قوالب جاهزة (نسخ منها لكل فيديو جديد)
│   ├── concept-explainer-short/   ← فيديوهات عمودية قصيرة (Reels/Shorts/TikTok)
│   ├── product-demo/              ← فيديوهات تسويقية (عرض منتج)
│   ├── sprint-review/             ← فيديوهات مراجعة سبرنت
│   └── sprint-review-v2/         ← نسخة محدثة
├── tools/                  ← أدوات Python (صوت، صور، فيديو، موسيقى)
├── brands/                 ← ملفات الهوية البصرية (ألوان، خطوط، صوت)
├── projects/               ← مشاريعك (كل فيديو في مجلد منفصل)
├── examples/               ← أمثلة جاهزة للتعلم
├── lib/                    ← مكونات مشتركة (Remotion components, transitions)
├── assets/                 ← أصول مشتركة (أصوات، صور)
└── .env                    ← مفاتيح API (اختياري)
```

---

## 🏁 الخطوة 0: الإعداد الأولي (مرة واحدة فقط)

### المتطلبات الأساسية

| المتطلب | الحالة عندك | ملاحظة |
|---------|-------------|--------|
| Python 3.10+ | ✅ مثبت | `C:\Program Files\Python312\python.exe` |
| Node.js 18+ | تحقق بـ `node -v` | مطلوب لقوالب Remotion فقط |
| uv | ✅ مثبت | مدير بيئة Python |
| FFmpeg | اختياري | لمعالجة الوسائط المتقدمة |

### تثبيت الاعتماديات (أنجزته بالفعل ✅)

```powershell
cd "d:\files\Contracted projects\IdeaProjects\claude-code-video-toolkit"
uv sync          # ✅ تم
```

### إعداد مفاتيح API (اختياري — حسب الأدوات التي تريدها)

انسخ ملف `.env.example` إلى `.env` وأضف المفاتيح التي تحتاجها فقط:

```powershell
Copy-Item .env.example .env
```

| الخدمة | لماذا تحتاجها | التكلفة | كيف تحصل على المفتاح |
|--------|--------------|---------|---------------------|
| **Modal** | تشغيل نماذج AI على GPU سحابي (صوت، صور، فيديو) | **30$ مجاناً/شهر** | [modal.com](https://modal.com) → Starter Plan |
| **Cloudflare R2** | نقل ملفات بين جهازك و GPU السحابي | **مجاني** (10GB) | [dash.cloudflare.com](https://dash.cloudflare.com) |
| **Ideogram** | توليد صور بنصوص مقروءة (عناوين، بطاقات) | ~$0.03/صورة | [ideogram.ai](https://ideogram.ai) |
| **ElevenLabs** | تعليق صوتي احترافي (بديل مدفوع) | حسب الخطة | [elevenlabs.io](https://elevenlabs.io) |
| **ACEMusic** | توليد موسيقى بالذكاء الاصطناعي | **مجاني** | [acemusic.ai/api-key](https://acemusic.ai/api-key) |

> [!TIP]
> **يمكنك البدء بدون أي مفتاح API!** القالب يعمل بدون صوت أو صور AI — ينتج فيديو بخلفيات Gradient مؤقتة وتوقيت تقديري. أضف الأدوات تدريجياً.

---

## 🎯 السيناريو 1: فيديو عمودي قصير لإنستغرام (Reels / Shorts / TikTok)

> **القالب:** `concept-explainer-short` — فيديو عمودي 9:16 (1080×1920)
> **المحرك:** Python + MoviePy (بدون Remotion/Node)

### الخطوة 1: إنشاء المشروع

```powershell
Copy-Item -Recurse templates\concept-explainer-short projects\my-instagram-reel
cd projects\my-instagram-reel
```

### الخطوة 2: كتابة السيناريو (`scenes.json`)

هذا هو الملف الأهم — كل مشهد يحتوي على **نص التعليق الصوتي** و**الصورة/المقطع المرافق**:

```json
{
  "title": "5 أخطاء يرتكبها كل مبرمج مبتدئ",
  "scenes": [
    {
      "id": "01",
      "slug": "hook",
      "visual": "ltx",
      "asset": "clips/01_hook.mp4",
      "text": "If you're learning to code, you're probably making at least one of these five mistakes right now."
    },
    {
      "id": "02",
      "slug": "mistake1",
      "visual": "ideogram",
      "asset": "images/02_mistake1.png",
      "text": "Mistake number one: copying code without understanding it. Stack Overflow is great, but if you can't explain what the code does, you haven't learned anything."
    },
    {
      "id": "03",
      "slug": "mistake2",
      "visual": "ltx",
      "asset": "clips/03_mistake2.mp4",
      "text": "Mistake number two: skipping the fundamentals. Frameworks change every year, but data structures and algorithms last forever."
    },
    {
      "id": "04",
      "slug": "cta",
      "visual": "ideogram",
      "asset": "images/04_cta.png",
      "text": "Follow for one coding tip every day. Link in bio for the full guide."
    }
  ]
}
```

> [!NOTE]
> **قواعد السيناريو:**
> - فيديو 60 ثانية ≈ **140 كلمة** إجمالاً
> - الـ Hook (المشهد الأول) يجب أن يكون **أقل من 3 ثوانٍ** من الكلام
> - بدّل بين الصور والفيديو كل ~15 ثانية لكسر الرتابة
> - حد YouTube Shorts / Reels: **3 دقائق** كحد أقصى
> - `text` هو النص الذي سيُقال بالضبط ويُحرق كترجمة على الفيديو

### الخطوة 3: رندرة أولية (بدون صوت ولا صور — للتجربة)

```powershell
uv run build.py
```

هذا يُنتج فيديو بخلفيات ملونة مؤقتة وتوقيت تقديري — **لتتأكد أن البنية صحيحة قبل إضافة الأصول**.

### الخطوة 4: توليد الصور والمقاطع (اختياري — يحتاج GPU سحابي)

من **المجلد الرئيسي للمشروع** (وليس من مجلد الفيديو):

```powershell
# ← ارجع للمجلد الرئيسي أولاً
cd "d:\files\Contracted projects\IdeaProjects\claude-code-video-toolkit"

# بطاقة نصية (Ideogram 4 — نص مقروء داخل الصورة)
uv run tools/ideogram4.py --json caption.json --resolution 1440x2560 --output projects/my-instagram-reel/images/02_mistake1.png

# مقطع فيديو متحرك (LTX-2 — خلفية حركية)
uv run tools/ltx2.py --width 576 --height 1024 --num-frames 161 --prompt "Close-up of hands typing code on a laptop, dark room, blue light" --output projects/my-instagram-reel/clips/01_hook.mp4

# موسيقى خلفية (اختياري)
uv run tools/music_gen.py --preset upbeat-tech --duration 120 --output projects/my-instagram-reel/audio/music.mp3
```

### الخطوة 5: توليد التعليق الصوتي

```powershell
cd projects\my-instagram-reel
uv run gen_vo.py
```

هذا يقرأ كل مشهد من `scenes.json` ويُنتج ملف صوتي لكل مشهد في `audio/scenes/`.

**إعدادات الصوت** في `config.json`:

```json
"voice": {
  "provider": "qwen3",      ← مجاني (يحتاج Modal)
  "cloud": "modal",
  "speaker": "Ryan",        ← 9 أصوات متاحة
  "maxWpm": 165             ← سرعة الكلام
}
```

> الأصوات المتاحة: `Ryan`, `Aiden`, `Vivian`, `Lily`, `Sara`, `Emily`, `Noah`, `John`, `Lucas`

### الخطوة 6: توليد الترجمة المتحركة (Burned Captions)

```powershell
uv run gen_captions.py
```

> [!WARNING]
> هذه الخطوة تحتاج `uv sync --extra whisper` (يحمّل مكتبة Whisper + PyTorch — حجمها كبير).
> إذا لم تثبتها، يمكنك تخطي هذه الخطوة والفيديو سيخرج بدون ترجمة.

### الخطوة 7: الرندرة النهائية

```powershell
uv run build.py
```

**الناتج:** `out/short.mp4` — فيديو عمودي 1080×1920 جاهز للرفع على إنستغرام!

---

## 🎯 السيناريو 2: فيديو أفقي لليوتيوب (16:9)

> **القالب:** `product-demo` أو `sprint-review`
> **المحرك:** Remotion (React + TypeScript) — يحتاج Node.js

### الخطوة 1: إنشاء المشروع

```powershell
Copy-Item -Recurse templates\product-demo projects\my-youtube-video
cd projects\my-youtube-video
npm install
```

### الخطوة 2: المعاينة الحية (Remotion Studio)

```powershell
npm run studio
```

يفتح متصفح بمعاينة تفاعلية — تستطيع التنقل بين المشاهد، تعديل التوقيت، ومراجعة التصميم لحظياً.

### الخطوة 3: تعديل المحتوى

في قوالب Remotion، المحتوى يُضبط عبر ملف TypeScript (مثل `sprint-config.ts`):

- **العنوان والنصوص** — تعديل مباشر في الملف
- **المشاهد** — كل مشهد هو React Component
- **الصور والفيديوهات** — توضع في `public/`
- **الصوت** — يوضع في `public/audio/`

### الخطوة 4: التعليق الصوتي

```powershell
cd "d:\files\Contracted projects\IdeaProjects\claude-code-video-toolkit"
uv run tools/voiceover.py --provider qwen3 --speaker Ryan --scene-dir projects/my-youtube-video/public/audio/scenes --json
```

### الخطوة 5: مزامنة التوقيت

```powershell
uv run tools/sync_timing.py --apply
```

هذا يقرأ مدة كل ملف صوتي ويُحدّث ملف الإعداد تلقائياً ليتطابق التوقيت.

### الخطوة 6: الرندرة

```powershell
cd projects\my-youtube-video
npm run render
```

**الناتج:** `out/video.mp4` — فيديو أفقي 1920×1080 جاهز لليوتيوب!

### الخطوة 7: النشر على يوتيوب (اختياري)

```powershell
cd "d:\files\Contracted projects\IdeaProjects\claude-code-video-toolkit"

# تسجيل دخول (مرة واحدة — يفتح المتصفح)
uv run tools/youtube_upload.py --auth

# الرفع
uv run tools/youtube_upload.py --video projects/my-youtube-video/out/video.mp4 --title "عنوان الفيديو" --privacy private --json-out
```

---

## 🛠️ كتالوج الأدوات المتاحة

### أدوات الصوت

| الأداة | الوظيفة | التكلفة |
|--------|---------|---------|
| `voiceover.py` | تعليق صوتي (يدعم 3 مزودين) | مجاني (Qwen3) أو مدفوع |
| `qwen3_tts.py` | تعليق صوتي مباشر (9 أصوات + استنساخ) | ~$0.01 |
| `sfx.py` | مؤثرات صوتية | مدفوع (ElevenLabs) |
| `music_gen.py` | موسيقى بالذكاء الاصطناعي (8 أنماط جاهزة) | **مجاني** (acemusic) |
| `redub.py` | إعادة دبلجة فيديو بصوت مختلف | مدفوع |

### أدوات الصور

| الأداة | الوظيفة | التكلفة |
|--------|---------|---------|
| `flux2.py` | توليد صور (خلفيات بدون نص) | ~$0.02 |
| `ideogram4.py` | توليد صور بنصوص مقروءة (عناوين، بطاقات) | ~$0.03 |
| `image_edit.py` | تعديل صور بالذكاء الاصطناعي | ~$0.03 |
| `upscale.py` | تكبير صور (2x/4x) | ~$0.01 |

### أدوات الفيديو

| الأداة | الوظيفة | التكلفة |
|--------|---------|---------|
| `ltx2.py` | توليد مقاطع فيديو من نص أو صورة | ~$0.23 |
| `soulx.py` | رأس متحدث (Talking Head) من صورة + صوت | ~$0.0024/ثانية |
| `sadtalker.py` | رأس متحدث (أرخص وأسرع) | ~$0.10 |
| `dewatermark.py` | إزالة العلامات المائية | ~$0.10 |

### أدوات النشر

| الأداة | الوظيفة |
|--------|---------|
| `youtube_upload.py` | رفع مباشر على يوتيوب |

---

## 💰 التكلفة الفعلية لإنتاج فيديو

### فيديو قصير (Reel / Short — 60 ثانية)

| العنصر | التكلفة التقريبية |
|--------|-------------------|
| التعليق الصوتي (Qwen3-TTS) | ~$0.01 |
| 2 صورة (Ideogram 4) | ~$0.06 |
| 2 مقطع فيديو (LTX-2) | ~$0.46 |
| موسيقى (ACEMusic) | **مجاني** |
| **الإجمالي** | **~$0.53** |

### فيديو يوتيوب (5 دقائق)

| العنصر | التكلفة التقريبية |
|--------|-------------------|
| التعليق الصوتي | ~$0.05 |
| 5 صور | ~$0.15 |
| 3 مقاطع فيديو | ~$0.69 |
| موسيقى | **مجاني** |
| **الإجمالي** | **~$0.89** |

> [!TIP]
> Modal يمنحك **30$ مجاناً شهرياً** — يكفي لإنتاج عشرات الفيديوهات!

---

## 🎨 الهوية البصرية (Brand Profile)

لإنشاء هوية بصرية خاصة بقناتك:

```
brands/my-brand/
├── brand.json    ← الألوان والخطوط
├── voice.json    ← إعدادات الصوت
└── assets/       ← الشعار والخلفيات
```

**مثال `brand.json`:**

```json
{
  "name": "My Channel",
  "colors": {
    "primary": "#7C5CFF",
    "secondary": "#00E0C6",
    "background": "#0A0A14",
    "text": "#FFFFFF"
  },
  "fonts": {
    "heading": "Inter",
    "body": "Roboto"
  }
}
```

تُطبَّق هذه الألوان والخطوط تلقائياً على كل فيديو تنشئه من القالب.

---

## 🔁 ملخص سير العمل اليومي

### لفيديو إنستغرام قصير:

```
1. Copy-Item -Recurse templates\concept-explainer-short projects\new-reel
2. عدّل scenes.json (السيناريو)
3. uv run build.py                      ← معاينة أولية
4. أضف الصور/المقاطع (يدوياً أو بالأدوات)
5. uv run gen_vo.py                     ← توليد الصوت
6. uv run gen_captions.py               ← توليد الترجمة
7. uv run build.py                      ← الرندرة النهائية
8. ارفع out/short.mp4 على إنستغرام ✅
```

### لفيديو يوتيوب:

```
1. Copy-Item -Recurse templates\product-demo projects\new-video
2. npm install
3. عدّل ملف الإعداد (sprint-config.ts أو المكافئ)
4. npm run studio                        ← معاينة حية
5. أضف الأصول في public/
6. uv run tools/voiceover.py ...         ← توليد الصوت
7. uv run tools/sync_timing.py --apply   ← مزامنة التوقيت
8. npm run render                        ← الرندرة النهائية
9. uv run tools/youtube_upload.py ...    ← رفع على يوتيوب ✅
```

---

## ❓ أسئلة شائعة

### هل يعمل المشروع مع Antigravity/Gemini بدلاً من Claude Code؟
**نعم.** الأدوات والقوالب والسكربتات كلها مستقلة — تعمل من الـ Terminal. الـ Skills والـ Commands مكتوبة كملفات Markdown يستطيع أي وكيل AI قراءتها. Antigravity يستطيع تنفيذ كل شيء.

### هل أحتاج GPU محلي؟
**لا.** كل أدوات AI تعمل على GPU سحابي (Modal أو RunPod). جهازك يحتاج فقط Python و Node.js.

### هل يدعم اللغة العربية؟
التعليق الصوتي (Qwen3-TTS) يدعم الإنجليزية بشكل أساسي. للعربية، يمكنك:
- تسجيل صوتك يدوياً ووضعه في `audio/scenes/`
- استخدام ElevenLabs (يدعم العربية)
- استخدام خدمة TTS عربية خارجية

### أين تُحفظ الفيديوهات الناتجة؟
- قوالب Python: `projects/اسم-المشروع/out/short.mp4`
- قوالب Remotion: `projects/اسم-المشروع/out/video.mp4`

### هل يمكن استنساخ صوتي؟
**نعم.** Qwen3-TTS يدعم Voice Cloning — سجّل 12-25 ثانية من صوتك وأعطه النص المطابق:
```powershell
uv run tools/qwen3_tts.py --text "Hello world" --ref-audio my-voice.m4a --ref-text "the exact transcript" --output cloned.mp3
```

---

## 🚀 ابدأ الآن!

أسهل طريقة للبدء:

```powershell
cd "d:\files\Contracted projects\IdeaProjects\claude-code-video-toolkit"

# 1. انسخ القالب
Copy-Item -Recurse templates\concept-explainer-short projects\first-reel

# 2. عدّل السيناريو
notepad projects\first-reel\scenes.json

# 3. أنتج الفيديو (بدون صوت — للتجربة)
cd projects\first-reel
uv run build.py

# 4. شاهد الناتج
start out\short.mp4
```

> أخبرني بموضوع الفيديو الذي تريده، وسأجهز لك `scenes.json` كاملاً جاهزاً للرندرة!
