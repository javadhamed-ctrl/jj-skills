---
name: memory-bank
description: Use at the start of every session and whenever asked about "memory bank", remembering past project context, recalling previously dictated message texts, or saving new user-dictated texts so they survive session close. Loads the persistent Memory Bank at ~/.config/opencode/memory/ which stores per-project history and every user-dictated message with SHA-256 identity.
---

# Memory Bank — حفظ حافظه بین نشست‌ها

بانک حافظه یک مکملِ AGENTS.md است، نه جانشین آن:
- **AGENTS.md سراسری** = دانشِ فنی/recipe ها/هویت‌ها (مثلاً روبیکا، بله، ایتا، اکانت‌های خود کاربر).
- **Memory Bank** = حافظهٔ کاری: متن پیام‌هایی که کاربر دیکته می‌کند + تاریخچه/پیشرفت پروژه‌ها.

## شروع هر نشست (الزامی)

1. `C:\Users\Parspano\.config\opencode\memory\INDEX.json` را بخوان.
2. اگر کاربر در حال کار روی پروژهٔ مشخصی است، `PROJECTS/<slug>/MEMORY.md` را بخوان.
3. اگر کاربر گفت «همان پیام قبلی را بفرست» یا «متنش چی بود»: **اول `MESSAGES.json` را بخوان**، بعد جواب بده. هیچ‌وقت قبل از چکِ بانک نگو «به متن دسترسی ندارم».

## ثبت هر متن دیکته‌شده (الزامی، بی‌درنگ)

هر متنی که کاربر برای ارسال دیکته می‌کند، **فوراً** به `MESSAGES.json` اضافه کن با این شکل:

```json
{
  "id": "mYYYYMMDD-<slug>",
  "date": "YYYY-MM-DD",
  "text": "<متن عیناً همان‌طور که کاربر نوشته>",
  "length": 61,
  "zwnj_count": 0,
  "sha256_text_utf8": "<hex>",
  "destination": {"network": "...", "account_name": "...", "guid": "..."},
  "status": "approved",   // dictated → approved → sent
  "user_approved": true,
  "note": "کوتاه"
}
```

- `text` را با Python (UTF-8) امضاش کن: `hashlib.sha256(text.encode('utf-8')).hexdigest()`.
- همان لحظه به کاربر اعلام کن: «در بانک حافظه ثبت شد ✓» — تا بداند دیگر لازم نیست دوباره تایپ کند.
- وضعیت پیام (`sent`) را بعد از تأییدِ واقعی به‌روز کن.

## حافظهٔ پروژه (وقتی پروژه جدیدی شروع شد)

یک `PROJECTS/<slug>/MEMORY.md` بساز با بخش‌های:
- **هدف و حوزه**: یک پاراگراف.
- **واقعیت‌های تأییدشده**: سطرهایی با تاریخ + مدرک (مثلاً «تأییدشده 2026-09-29 با API»).
- **تصمیم‌ها**: هر تصمیم با تاریخ و دلیل.
- **پیشرفت**: آخرین وضعیت (آخرین تاریخ در بالا).
- **اشتباهات و دروس**: سطر کوتاه با تاریخ.

## قانون‌های سخت‌گیرانه (از درس‌های تلخ جلسات قبل)

1. **فقط واقعیتِ تأییدشده وارد حافظه شود.** هرگز حدس/تردید را به‌صورت قطعی ننویس. اگر مطمئن نیستی: `"status": "unverified"` بگذار.
2. **تصحیح = سطر جدید + ارجاع، نه بازنویسی.** بعد  از واژهٔ «تصحیح:» بنویس و به sطر قبلی ارجاع بده.
3. **SHA-256 حتماً ثبت شود** تا در آینده وقتی پیام را می‌خواهیم بفرستیم، مطمئن شویم دقیقاً همان متن تأییدشده می‌رود نه چیزی که از حافظهٔ گفتگو بازسازی شده.
4. **هنگام ارسال، متن را از فایل بخوان** نه از حافظهٔ گفتگو — بعد از خواندن، SHA را با SHA ثبت‌شده مقایسه کن. اگر نخورد، متوقف شو و گزارش بده.

## اسکریپت کمکی

امضای SHA و سرشماری کاراکتر را با یک خط Python انجام بده (کنسول cp1256 → همیشه `-X utf8` و خروجی به فایل، نه چاپ فارسی مستیم).
الگو در `C:\Users\Parspano\AppData\Local\Temp\opencode\` موجود است.