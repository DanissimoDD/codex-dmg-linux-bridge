# Twitter post (Arabic)

حوّلت تشغيل `Codex.dmg` على Ubuntu لحل عملي شغال بالكامل ✅

- تشغيل Electron payload على Linux
- حل مشكلة `model_not_found`
- تثبيت الربط على Codex CLI الصحيح (`0.98.0`)
- تشغيل `gpt-5.3-codex` بنجاح داخل التطبيق

نشرت السكربتات + التوثيق عشان أي حد يكرر نفس الخطوات بسهولة.

#linux #ubuntu #codex #electron #opensource

---

# Twitter thread (English)

1) Problem: `Codex.dmg` is macOS-oriented, but I needed it running on Ubuntu.

2) Approach: extracted payload + Linux launcher + native module compatibility fixes.

3) Root issue discovered: app was bound to an older Codex CLI, causing `model_not_found`.

4) Final fix: bind launcher to modern CLI (`/home/linuxbrew/.linuxbrew/bin/codex`) and set `gpt-5.3-codex`.

5) Result: app works, conversations send correctly, model runs successfully.
