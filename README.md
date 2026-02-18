# codex-dmg-linux-bridge

دليل عملي لتشغيل payload تطبيق Codex (الموجود داخل `Codex.dmg`) على Linux/Ubuntu.

المشروع ده **لا يوزع** ملفات Codex الأصلية، هو فقط يوفر launcher وخطوات تشغيل.

## 1) هذا المشروع بيعمل إيه؟

`Codex.dmg` مخصص أساسًا لـ macOS.  
الفكرة هنا:

- نستخرج المحتوى المطلوب من `Codex.dmg`
- نشغل Electron payload على Linux
- نربطه مع `codex` CLI الصحيح

## 2) قبل ما تبدأ (مهم جدًا)

- لازم يكون عندك `Codex.dmg` من المصدر الرسمي.
- الريبو ده لا يحتوي DMG أو binaries أصلية.
- لو أي خطوة مش مطابقة عندك، راجع `docs/TROUBLESHOOTING.md`.

## 3) المتطلبات

- Ubuntu/Linux x64
- `bash`
- `git`
- `node` + `npm` + `npx`
- `7z` (لفك DMG)
- `codex` CLI

تثبيت المتطلبات (Ubuntu):

```bash
sudo apt update
sudo apt install -y p7zip-full git curl
```

تأكد:

```bash
node -v
npm -v
npx -v
codex --version
```

## 4) نزّل المشروع

```bash
git clone https://github.com/Mina-Sayed/codex-dmg-linux-bridge.git
cd codex-dmg-linux-bridge
chmod +x scripts/codex-dmg-linux-launcher.sh
```

## 5) جيب `Codex.dmg`

نزّله من المصدر الرسمي لحسابك، وبعدها انقله لينكس (لو حملته من جهاز Mac).  
ضع الملف هنا كمثال:

```bash
mkdir -p "$HOME/codex-dmg-attempt-latest"
cp /path/to/Codex.dmg "$HOME/codex-dmg-attempt-latest/Codex.dmg"
```

## 6) جهّز payload المطلوب من DMG

الـlauncher يحتاج مجلد عمل فيه:

- `asar-unpacked/`
- `tools/node/runtime/bin/npx`

### 6.1 استخراج DMG

```bash
cd "$HOME/codex-dmg-attempt-latest"
7z x Codex.dmg -oextract
```

### 6.2 استخراج `app.asar` إلى `asar-unpacked`

ابحث عن `app.asar`:

```bash
APP_ASAR="$(find extract -type f -name app.asar | head -n 1)"
echo "$APP_ASAR"
```

لو ظهر مسار صحيح، استخرجه:

```bash
npx --yes @electron/asar extract "$APP_ASAR" asar-unpacked
```

### 6.3 جهّز مسار `tools/node/runtime/bin/npx`

لو غير موجود، اعمل symlink على `npx` النظام:

```bash
mkdir -p tools/node/runtime/bin
ln -sf "$(command -v npx)" tools/node/runtime/bin/npx
```

## 7) اختَر Codex CLI الصحيح

لازم تعرف أي `codex` سيُستخدم:

```bash
which -a codex
codex --version
```

لو عندك أكثر من واحد، استخدم المسار الأحدث (مثال):

`/home/linuxbrew/.linuxbrew/bin/codex`

## 8) سجّل دخول Codex CLI

```bash
codex login
```

## 9) شغّل التطبيق لأول مرة

من داخل الريبو:

```bash
CODEX_DMG_WORKDIR="$HOME/codex-dmg-attempt-latest" \
CODEX_CLI_PATH="/home/linuxbrew/.linuxbrew/bin/codex" \
./scripts/codex-dmg-linux-launcher.sh
```

## 10) اختبر إن الدنيا شغالة

اختبار سريع للـ app-server:

```bash
codex debug app-server send-message-v2 "reply with one word: ok"
```

المفروض تشوف رد نهائي: `ok`.

## 11) (اختياري) خليه أمر ثابت من أي مكان

```bash
mkdir -p ~/.local/bin
cp scripts/codex-dmg-linux-launcher.sh ~/.local/bin/codex-dmg-linux
chmod +x ~/.local/bin/codex-dmg-linux
```

ثم التشغيل:

```bash
CODEX_DMG_WORKDIR="$HOME/codex-dmg-attempt-latest" \
CODEX_CLI_PATH="/home/linuxbrew/.linuxbrew/bin/codex" \
~/.local/bin/codex-dmg-linux
```

## 12) مشاكل متوقعة بسرعة

- `Missing app payload at .../asar-unpacked`
  - لم يتم استخراج `app.asar` بشكل صحيح.
- `Missing Node runtime at .../tools/node/runtime/bin`
  - اعمل خطوة symlink لـ `npx`.
- `Unable to locate the Codex CLI binary`
  - اضبط `CODEX_CLI_PATH` لمسار `codex` الصحيح.
- `model_not_found`
  - غالبًا CLI قديم أو إعداد موديل غير مناسب للحساب.

تفاصيل أكثر: `docs/TROUBLESHOOTING.md`.

## 13) الملفات المهمة في الريبو

- `scripts/codex-dmg-linux-launcher.sh`: سكربت التشغيل الأساسي
- `docs/SETUP.md`: ملاحظات setup
- `docs/TROUBLESHOOTING.md`: حلول المشاكل

## License

MIT (see `LICENSE`).
