# Surface Hub

مشروع استثمار وبيع أجهزة Microsoft Surface المستعملة — تطبيق ويب بملف واحد (`index.html`)، بدون خطوة بناء (build step).

## الملفات

- `index.html` — التطبيق كامل (الواجهة + الأنماط + منطق التطبيق).
- `fonts/` — خط ThmanyahSans (5 أوزان) المستخدم بالواجهة.

## قبل النشر: إعداد Firebase (مطلوب)

التطبيق يحتاج مشروع Firebase شغّال (تسجيل دخول + قاعدة بيانات) قبل ما يشتغل. الخطوات:

1. روح [console.firebase.google.com](https://console.firebase.google.com) → أنشئ مشروع جديد → أضف **Web app** → انسخ كود الإعداد (`firebaseConfig`).
2. **Build → Authentication → Get started** → فعّل مزوّد **Email/Password**.
3. **Authentication → Users → Add user** → أضف حساب لكل شخص بيستخدم التطبيق يدويًا (ما فيه تسجيل عام بالتطبيق نفسه، عمدًا).
4. **Build → Firestore Database → Create database**.
5. **Firestore → Rules** → الصق التالي (بدّل الإيميلات لإيميلاتكم الفعلية) ثم Publish:

   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /surfaceHub/{docId} {
         allow read, write: if request.auth != null
           && request.auth.token.email in ['mohamed@example.com', 'fatima@example.com'];
       }
     }
   }
   ```

6. افتح `index.html`، دوّر على `firebaseConfig` (قرب نهاية الملف)، واستبدل كل قيمة `"YOUR_..."` بالقيم الحقيقية من خطوة 1.

> الـ `apiKey` مو سر — قواعد Firestore بخطوة 5 هي الحماية الفعلية. بدونها أي شخص يعرف رابط المشروع يقدر يقرأ/يكتب البيانات.

## النشر على GitHub Pages

1. ادفع هذا المجلد لمستودع GitHub.
2. من إعدادات المستودع: **Settings → Pages** → اختر الفرع (branch) والمجلد الجذر → احفظ.
3. الرابط النهائي بيكون شكله `https://<username>.github.io/<repo>/`.

## البيانات

كل البيانات (المخزون، الحركات المالية، حسابات Office، العهد...) مخزّنة بـ Firestore ومتزامنة لحظيًا بين كل من سجّل دخول. ما فيه تصدير/استيراد يدوي — Firestore هو مصدر الحقيقة الوحيد.
