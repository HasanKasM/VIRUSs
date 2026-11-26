# حماية الموقع والبوت من الـ DDoS

حماية DDoS الحقيقية تكون على مستوى Cloudflare (قبل ما الطلب يوصل لسيرفرك). صفحة HTML ثابتة ما تقدر تحمي نفسها وحدها.
اللي أضفته في المشروع: حد طلبات + قاطع دائرة + احترام Retry-After + jitter في الواجهة، وCSP/Headers مشددة.

## 1) على Cloudflare (مجاني)
1. **Proxy**: اجعل سجل DNS لدومين الموقع ودومين API البوت على **Proxied** (السحابة البرتقالية). لا تعرض IP سيرفرك.
2. **SSL/TLS** → Full (strict).
3. **Security → Settings**: فعّل **Bot Fight Mode**، ومستوى الأمان Medium أو High.
4. **Security → WAF → Rate limiting rules**: أنشئ قاعدة على مسار `/status`:
   - الشرط: URI Path equals `/status`
   - الحد: 20 طلب / 10 ثواني لكل IP
   - الإجراء: Block (أو Managed Challenge) لمدة 10 دقائق
5. أثناء هجوم فعلي: **Security → Settings → Security Level = I'm Under Attack**.
6. لو البوت يشتغل من البيت أو عبر نفق: استخدم **Cloudflare Tunnel (cloudflared)** عشان ما ينكشف الـ IP.

## 2) على البوت (السيرفر)
البوت فيه حد طلبات لكل IP ومهلات ضد الاتصالات البطيئة. اقفل بورت السيرفر بالجدار الناري بحيث يقبل اتصالات Cloudflare فقط.

## 3) تنبيه
لا توجد حماية صفحة ثابتة تمنع DDoS لوحدها. الكود يخفف الإغراق ويقلل الحمل، لكن الصد الفعلي يتم عند Cloudflare.
