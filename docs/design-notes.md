# CalisthenicsApp — מסמך עיצוב ותוכנית מימוש (גרסה 3)

> **מה השתנה בגרסה 3 (לפי התשובות שלך):** Jackson → **Bench Dips** · התוכנית בנויה כ**רצף ימים**, כל יום = אימון מלא עם Push וגם Pull, והתוצאות שלך מחוברות לכל יום — מוכן לכך שהמאמן יתאים את הימים הבאים (שלב 2) · **פרס = תרגיל מעבר** פלאנץ׳ ⇄ עמידת ידיים, עם וריאציות רחב/רגיל/צר · **שני צירים** לכל תרגיל: Statics/Dynamics + Push/Pull/Core, עם כללי התאוששות לפי ציר · **Side Flip** נוסף, ומשפחת Dynamics מלאה · **אקטיבציות** שודרגו למערכת המתקדמת באפליקציה (חלק 6) · **ייבוא ה-Data Export של אינסטגרם** לקביעת הרמה ההתחלתית שלך · אתלטים: הערכות מתוך הפרופילים הציבוריים דרך Claude in Chrome (חלק 8.4).

> **לסוכנים מבצעים:** REQUIRED SUB-SKILL: superpowers:subagent-driven-development. כל משימה בחלק 14 מסומנת ב-`- [ ]`. בזמן הבנייה: superpowers:test-driven-development, superpowers:verification-before-completion, break-ui (ו-review-animations אם יותקן — ראה חלק 0).

**מטרה:** אפליקציית iPhone אישית לקליסטניקס, שרצה כולה ב-**Expo Go**: תוכנית האימון של Shawn Sama כ**רצף ימים** של קומבואים דיגיטליים — האפליקציה מבינה איך התוכנית המלאה מתקדמת לאורך זמן, ושומרת את מה שביצעת מול מה שתוכנן לכל יום, מסך אימון חי עם טיימרים/קול/מצלמה, עץ מיומנויות עם אייקונים חיים, מערכת "אקטיבציות" במהלך היום, מעקב התקדמות נושם, וספריית סרטונים עם השוואה לאתלטים מובילים.

**ארכיטקטורה:** Local-first — SQLite במכשיר, בלי שרת ובלי חשבון. ממשק על Expo Router עם רכיבי iOS נייטיב. כל התנועה ב-Reanimated 4 + Skia על ה-UI thread, מונעת משעון משותף אחד. כל יכולת שדורשת development build או שרת (זיהוי תנוחה אוטומטי, גיבוי iCloud, מאמן AI, פענוח PDF אוטומטי) מוגדרת כבר עכשיו כ**ממשק** עם מימוש v1 ידני, כדי ששלב 2 יחליף מימוש בלי לגעת ב-UI.

**Tech Stack (v1, Expo Go בלבד):** Expo SDK 58 · React Native 0.88 · expo-router 58 · react-native-reanimated 4.7 + react-native-worklets · react-native-gesture-handler · @shopify/react-native-skia · react-native-svg · @shopify/flash-list · expo-sqlite · expo-localization · expo-haptics · expo-speech · expo-audio · expo-camera · expo-video · expo-video-thumbnails · expo-media-library · expo-notifications (מקומי) · expo-document-picker · expo-file-system · expo-sharing · expo-keep-awake · jest-expo + sql.js (לבדיקות).

**מקורות:** `docs/source/` (בתוך ה-repo) — תוכנית האימון ב-PDF, `פלאצ.mp4`, `מלטיז.mp4`.

---

## 0. אילו skills הובילו לאילו החלטות

| Skill | סטטוס | ההחלטות שנבעו ממנו |
|---|---|---|
| superpowers:writing-plans | ✅ | מבנה המסמך: מגבלות גלובליות, Review Focus, משימות עם Interfaces + בדיקות + commit |
| apple-design | ✅ | פונט מערכת (SF Pro/SF Hebrew) עם tracking לפי גודל; springs בפרמטרים damping/response; מחוות 1:1, interruptible, velocity handoff; כניסה ויציאה באותו מסלול; הפחתת תנועה = רגוע, לא שבור; הימנעות מתנודה איטית גדולה ב-0.2Hz → הנשימה מוגבלת לרכיבים קטנים |
| mobile-native | ✅ (חלקית) | ה-skill מיועד לווב. מה שעבר: מטרות מגע 44pt, משוב ב-press-in, safe areas, בדיקה רק על מכשיר אמיתי, צבע status bar תואם רקע |
| taste-skill:design-taste-frontend | ✅ (חלקית) | מיועד לאתרים. מה שעבר: **בלי סגול** (הוסר "Aurora" מהגרסה הקודמת), מערכת אחת (כפתור/כרטיס/radius אחד), צילומים/תוכן אמיתי במקום קישוט, אין טקסט "מסביר" מתחת לוויזואל |
| ui-ux-pro-max | ✅ | ניגודיות 4.5:1, אין צבע כמשמעות יחידה, אין אימוג׳י כאייקונים, FlashList + memo לרשימות, Reanimated + Gesture Handler. **נדחה:** הפלטה והפונט שהציע (כתום/ירוק גנרי, Barlow בלי עברית) |
| frontend-design | ✅ | רעיון עיצובי שנובע מהנושא ("הקו" ו"הציר" — חלק 1); הימנעות מהדיפולטים: רקע קרם `#F4F1EA` (היה ב-v1 → הוסר), תוויות ALL-CAPS, גרדיאנט כקישוט; נועזות במקום אחד — האייקונים החיים |
| emil-design-eng | ✅ | `scale 0.97` בלחיצה, אף פעם `scale(0)`, ease-out חזק, יציאה מהירה מכניסה, אסימטריה ב-hold (לינארי בהחזקה, snap-back מהיר) |
| animate-expo | ✅ | שער תדירות לכל אנימציה; אין אנימציה בהחלפת טאבים; NativeTabs / formSheet / large titles; רק transform+opacity; אין setState בפריים; `scheduleOnRN` רק ב-onEnd; ערכי spring ו-easing מדויקים (חלק 5) |
| choreograph | ✅ | טבלאות תזמון לכניסה ולמסך האימון; כניסה אחת לכל container; בלי stagger במסכים שחוזרים עליהם; "Carry" — הדמות בכניסה הופכת לאייקון במסך היום |
| gestures | ✅ | סימון מפרקים: track 1:1 עם offset אחיזה, release עם spring; rubber-band בגבולות הפריים; סרגל זמן בסרטון |
| find-animation-opportunities | ✅ | מסנן: איפה **לא** להזיז (רשימת הדחיות בחלק 5.9) |
| superpowers:test-driven-development, verification-before-completion, break-ui | ✅ קיימים | ישמשו בזמן הבנייה (כתוב בכל משימה) |
| **review-animations** | ❌ **לא נמצא** | לא קיים בשום prefix מותקן. חלופה: `improve-animations` (קיים) לסקירת תנועה בסוף |

---

## 1. כיוון עיצובי — "הציר" (The Pivot)

### 1.1 מה למדתי מהסרטונים שלך
חילצתי 8 פריימים מכל סרטון (ffmpeg):
- **`פלאצ.mp4` (26 שנ׳):** פלאנץ׳ על פרלטס → פרס איטי ומבוקר עד עמידת ידיים → חזרה לפלאנץ׳. **כל הגוף מסתובב סביב נקודה אחת — כפות הידיים.**
- **`מלטיז.mp4` (18 שנ׳):** אחיזה רחבה מאוד (רוחב מלטיז) → טאק → פרס סטרדל עד עמידת ידיים → ירידה לשכיבה רחבה. אותו עיקרון: ציר בידיים, הרגליים מציירות קשת.
- **אווירה:** חדר לבן ובהיר, וילונות שקופים, צמח ירוק, פרלטס שחורים, תנועה איטית, שליטה מוחלטת, בלי קפיצות.

### 1.2 הרעיון
שני מוטיבים שמגיעים ישר מהעולם שלך:
1. **הציר** (מהסרטונים) — הכל מסתובב סביב נקודת משען קבועה. בכניסה, באייקונים ובמעברים: יש נקודה שלא זזה, וכל השאר נע סביבה בשליטה.
2. **הקו** (מה-PDF של Shawn: *"ליין מקביל לרצפה"*) — קו גוף ישר מול כוח המשיכה. זה מד ההתקדמות, וזה גם מה שמודדים בניתוח הווידאו.

**החלטה מרכזית — "שלד אחד":** כל דמות באפליקציה (אייקוני המיומנויות, הכניסה, שכבת הניתוח על הווידאו) מצוירת מאותו **מודל שלד** — 7 מפרקים עם זוויות. אייקון = תנוחה מוגדרת; ניתוח = תנוחה נמדדת; השוואה = הפרש בין שתי תנוחות. רכיב אחד, שלושה שימושים.

### 1.3 פלטה — בהירה ותוססת (לפי בקשתך)
רקע בהיר כמו החדר בסרטונים, צבעי קטגוריה רוויים ונועזים, בלי סגול (taste-skill) ובלי קרם (frontend-design).

| טוקן | Hex | שימוש |
|---|---|---|
| `bg` | `#F2F4F7` | רקע ראשי — "פורצלן" קריר, כמו הקיר הלבן |
| `surface` | `#FFFFFF` | כרטיסים |
| `surface.sunk` | `#E8ECF1` | שדות, מסלולי פרוגרס ריקים |
| `ink` | `#0D1117` | טקסט ראשי |
| `ink.muted` | `#556070` | טקסט משני (≈ 6:1 על לבן) |
| `hairline` | `#D5DBE3` | קווי הפרדה (נדיר) |
| **Push — Ember** | fill `#FF4B1F` · tint `#FFE5DC` · text `#B3300A` | דחיפה |
| **Pull — Cobalt** | fill `#1F5BFF` · tint `#DDE6FF` · text `#1747CC` | משיכה |
| **Core — Flare** | fill `#FF2E7E` · tint `#FFE0EC` · text `#C01458` | קור / קומפרסיה |
| **Dynamics — Volt** | fill `#B8F018` · tint `#F0FCD0` · text `#3C6B00` | סימון הציר הדינמי (תג, שובל תנועה באייקון) — לא צבע כוח |
| `success` | `#10B26C` | הצלחה |
| `failure` | `#F0435E` | כישלון (מידע, לא "שגיאה") |
| `micro` | `#FFAE12` | התקדמות-מיקרו |
| `activation` | `#00B8C8` | אקטיבציות — צבע נפרד כי הן מסלול נפרד |

**כללים:**
- **שני צירים, שני ערוצים חזותיים:** הצבע תמיד מסמן את **הכוח** (Push = Ember, Pull = Cobalt, Core = Flare). ה**דיסציפלינה** (Statics / Dynamics) מסומנת בצורה ובתנועה: אייקון סטטי *מחזיק* (נשימה + רעד עומס), אייקון דינמי *מבצע* (לולאת תנועה קצרה עם שובל Volt). כך Front Lever (Pull · Static) ו-Bar Spin (Pull · Dynamic) שניהם כחולים, אבל נראים אחרת לגמרי.
- צבע ה-`fill` משמש למילוי, אייקונים, גרדיאנטים וטבעות. טקסט צבעוני תמיד בגרסת `text` (ניגודיות ≥ 4.5:1). Volt לעולם לא כטקסט על לבן.
- כל קטגוריה מקבלת גרדיאנט של שני גוונים קרובים (למשל Ember: `#FF4B1F → #FF8A1F`), רק על משטחים חיים (טבעת, מד, הילה של אייקון) — לא כקישוט רקע.
- הצלחה/כישלון/מיקרו מגיעים תמיד עם SF Symbol + תווית, לא רק צבע.
- מצב כהה: שלב 2 (אותם טוקנים סמנטיים, ערכים אחרים).

### 1.4 טיפוגרפיה
- **פונט מערכת** — SF Pro + SF Hebrew. הסיבה: הוא היחיד שנותן Dynamic Type, optical sizing ו-tracking נכון **בשתי השפות** בלי פשרות (apple-design). הנועזות של האפליקציה נמצאת בתנועה ובצבע, לא בפונט.
- **מספרים** (טיימרים, חזרות, שניות): משקל Heavy, `fontVariant: ['tabular-nums']`, גודל 56–96 במסך האימון — המספר הוא הגיבור של המסך.
- כותרות 28–34 Bold, tracking שלילי (≈ ‎-0.4pt); גוף 17 Regular; משני 15.
- אין ALL-CAPS לתוויות (frontend-design). אין גבהים קבועים לטקסט.

### 1.5 פריסה ו-iOS נייטיב
- Grid 8pt, שוליים 20, רווח בין קבוצות 32. radius אחד לכרטיסים (20), אחד לכפתורים (capsule).
- `NativeTabs`, כותרות גדולות, `formSheet` לגיליונות, `Link.Preview`/`Link.Menu` על כרטיסי מיומנות — **לאמת שכולם עובדים ב-Expo Go של SDK 58 לפני משימה 5.**

### 1.6 שפה ו-RTL
- **עברית היא ברירת המחדל** → RTL מההפעלה הראשונה. החלפה לאנגלית בהגדרות → דיאלוג → `I18nManager.forceRTL(false)` → טעינה מחדש.
- כל `translateX` מוכפל ב-`dir` (‎+1/‎-1); תנועת "קדימה" נכנסת מצד סוף הקריאה. מספרים, טיימרים וגרפים נשארים LTR.
- **אייקוני השלד לא מתהפכים** — פלאנץ׳ הוא פלאנץ׳ בכל שפה. רק כיוון הפריסה מתהפך.
- `he.ts` / `en.ts` עם בדיקה שמוודאת זהות מפתחות.
- **שמות התרגילים מהתוכנית נשמרים בדיוק כפי שהם ב-PDF** (`name_he`). `name_en` הוא תרגום שלי, ניתן לעריכה.

---

## 2. ספריית התנועה — מה זז, איך, ובאיזה קצב

### 2.1 שלוש שכבות
| שכבה | מה | מתי רצה | תקציב |
|---|---|---|---|
| **Ambient** (רקע חי) | שדה צבע רך מאחורי כל מסך; נשימה של אייקונים; נשימה של המד | תמיד, כשהמסך בפוקוס והאלמנט נראה | אמפליטודה קטנה, מחזור 3.6–6 שנ׳ |
| **Response** (תגובה) | לחיצה, גרירה, רישום סט, מעבר שלב בקומבו | רק בעקבות פעולה שלך | 100–300ms |
| **Moment** (רגע) | כניסה, עליית שלב, השלמת אימון | נדיר | עד 1.8 שנ׳, ניתן לדילוג |

### 2.2 השעון המשותף
- **שעון אחד** לכל האפליקציה: `useFrameCallback` שכותב `clock` (shared value, שניות). כל לולאת ambient היא פונקציה טהורה של `clock` + phase משלה (worklet). לכן: אין עשרות טיימרים, כל הנשימות מסונכרנות, וקל לעצור הכל.
- **השהיה כשלא נראה:** כל רכיב ambient מקבל `active` (shared value). `active = isFocused && isVisible && !reduceMotion`.
  - `isFocused` — מ-`useIsFocused` של expo-router.
  - `isVisible` — ברשימות מ-`onViewableItemsChanged` של FlashList; מחוץ לרשימות — תמיד true.
  - האפליקציה ברקע → השעון עוצר (`AppState`).
- **תקרה:** לכל היותר **10 אייקונים חיים בו-זמנית** על המסך; השאר מוצגים בתנוחת מנוחה סטטית. יעד: 60fps על iPhone 12 (או המכשיר הכי ישן שלך).

### 2.3 האייקונים החיים
כל וריאציה מקבלת **תנוחת שלד** (`PoseKey`) — 7 מפרקים: כף יד, מרפק, כתף, ירך, ברך, קרסול, ראש. מצוירים ב-Skia: גפיים כקווים עבים עם קצוות עגולים, ראש עיגול, הילה בצבע הקטגוריה מאחור.

| אפקט | איך | קצב / ערך |
|---|---|---|
| **נשימה** | חזה (קטע כתף–ירך) מתעבה 1→1.03 | sin, מחזור 4.2 שנ׳ |
| **תנודה** | כל הגוף מסתובב סביב **כף היד** (הציר!) ±0.6° | sin, מחזור 5.6 שנ׳, phase אקראי לכל אייקון |
| **רעד עומס** | תנודה מהירה של קצוות הגפיים | אמפליטודה = `0.15px × tier` (פלאנץ׳ מלא רועד יותר מטאק), תדר 9Hz, רק כשהאייקון גדול (≥ 64pt) |
| **הילה** | opacity של הילה 0.35↔0.55 | מחזור 4.2 שנ׳, מסונכרן לנשימה |
| **מצב "שלב נוכחי"** | טבעת התקדמות סביב האייקון | סטטית, מתעדכנת ב-spring כשמשתנה |

**תנוחות ב-v1 (כ-40):** iron cross, bench dip, shrimp flip, side flip, bar spin, explosive muscle-up, swing, ובנוסף: frog stand, planche lean, tuck/adv-tuck/straddle/half-lay/full planche, maltese, one-arm planche, wall HS, freestanding HS, straddle HS, one-arm HS, HSPU, pike press, L-sit, V-sit, manna, tuck/adv-tuck/one-leg/straddle/half-lay/full front lever, German hang, back lever (tuck→full), hefesto, pelican, victorian, flag (tuck/straddle/full), pull-up, muscle-up, push-up, dip, backflip, frontflip. תנוחה = מערך זוויות; וריאציה בלי תנוחה משלה יורשת את זו של המיומנות.

**אייקונים דינמיים** (muscle-up, bar spins, flips, swings, releases): במקום נשימה + רעד — לולאה של 2–3 תנוחות מפתח (למשל backflip: עמידה → טאק באוויר → נחיתה) באינטרפולציה של השלד, 1.6 שנ׳ ביצוע + 2.4 שנ׳ מנוחה בתנוחת הפתיחה, ושובל Volt דק שמצייר את מסלול מרכז הגוף. ספינים וסיבובים: סיבוב הגוף סביב **המוט** (הציר כאן הוא המוט, לא כף היד).

**הפחתת תנועה:** האייקון מוצג בתנוחה מלאה וסטטית (דינמי: תנוחת השיא), ההילה נשארת (opacity קבוע). אף פעם לא ריק או שבור.

### 2.4 שדה הרקע החי
- 2–3 "כתמי אור" גדולים ורכים (Skia, radial gradient + blur סטטי) בגוון ה-tint של הקטגוריה הרלוונטית למסך (היום = הקטגוריה של אימון היום; עץ מיומנויות = הקטגוריה הפתוחה).
- נעים לאט: הזזה של ±12pt במחזור 18–24 שנ׳. **אמפליטודה זעירה ביחס למסך** — לא "רקע שזז", אלא אור שמשתנה. (apple-design מזהיר מרקע מלא-מסך זז — לכן תזוזה קטנה, ניגודיות נמוכה, ובהפחתת תנועה: סטטי לגמרי.)
- **לא מנפישים blur** — הכתמים מצוירים פעם אחת, ורק ה-transform שלהם זז.

### 2.5 "הקו הנושם" — מד ההתקדמות
- קו עבה (10pt) עם מילוי נוזלי: Skia path עם גל סינוס (אמפליטודה 1.5pt, אורך גל 60pt, זורם במהירות 18pt/שנ׳) בגרדיאנט הקטגוריה.
- נשימה: עובי 1→1.05, מחזור 4.2 שנ׳ (אותו phase כמו האייקונים — האפליקציה "נושמת יחד").
- **תגובה לרישום:**
  - הצלחה → המילוי מתקדם ב-spring `{duration: 400, dampingRatio: 0.8}`, פולס ירוק אחד רץ לאורכו (320ms) + `notificationAsync(Success)`.
  - כישלון → הקו רועד פעם אחת (±2pt, 240ms) ונשאר במקום + `impactAsync(Medium)`.
  - מיקרו → נקודה צהובה ננעצת על הקו (scale 0.9→1, 200ms) + `selectionAsync`.
- שני מופעים: **אימונים** (בצבע הקטגוריה) ו**אקטיבציות** (בצבע `activation`).

### 2.6 כניסה — "הפרס" (נדיר → תקציב ההשראה)
**מבוסס ישירות על הסרטונים:** הדמות מבצעת פרס מפלאנץ׳ לעמידת ידיים, סביב ציר הידיים.

**רצף:**
1. **Splash נייטיב** (סטטי, `expo-splash-screen`): הדמות בתנוחת פלאנץ׳ על רקע `bg`. כך הפריים הראשון של האנימציה זהה ל-splash — אין קפיצה.
2. **אנימציית הכניסה** מתחילה כש-DB מוכן.

| אלמנט | תפקיד | התחלה | משך | תנועה | יחס |
|---|---|---|---|---|---|
| דמות — פלאנץ׳ "מחזיקה" | Lead | 0ms | 300ms | רעד עומס עולה ודועך — הגוף מתאמץ | — |
| דמות — פרס לעמידת ידיים | Lead | 300ms | 900ms | אינטרפולציה של זוויות השלד: פלאנץ׳ → טאק → סטרדל → עמידת ידיים. הידיים **לא זזות** (הציר); `EASE_IN_OUT` | Sequence אחרי ההחזקה |
| קשת הרגליים | Support | 380ms | 820ms | שובל דק בצבע Ember→Cobalt שמצייר את מסלול הקרסול ומתפוגג | Overlap עם הפרס |
| קו הרצפה | Decoration | 0ms | 400ms | fade-in בלבד | Together עם התחלה |
| נעילה בעמידת ידיים | Lead | 1200ms | 200ms | עצירה עם overshoot זעיר בזווית הכתף (dampingRatio 0.85) | — |
| שם האפליקציה | Support | 1150ms | 380ms | fade + עלייה 8pt, `EASE_OUT` | Overlap עם הנעילה |
| מעבר למסך היום | Carry | 1500ms | 300ms | הדמות מתכווצת ונוחתת כאייקון של אימון היום; המסך מופיע מתחתיה ב-fade | Carry |

- **הפטיקה:** `impactAsync(Light)` אחד ברגע הנעילה.
- **דילוג:** נגיעה בכל מקום → קפיצה לסוף ה-Carry (200ms).
- **מתי:** רק בפתיחה קרה, לא בחזרה מרקע.
- **הפחתת תנועה:** crossfade של 300ms מפלאנץ׳ לעמידת ידיים, ואז fade למסך.
- **מה לא זז:** טאב-בר (מופיע מוכן), רקע (סטטי בזמן הכניסה).

### 2.7 מסך אימון חי — קומבו
| אירוע | תנועה | הפטיקה / קול |
|---|---|---|
| ספירה לאחור לפני התחלה (3-2-1) | המספר מתחלף ב-crossfade + scale 0.96→1, 180ms | `selectionAsync` כל שנייה + קול "שלוש, שתיים, אחת" |
| טיימר החזקה | טבעת מתמלאת **linear** (זמן אמיתי זורם ליניארית — gestures) | 3 שניות אחרונות: `selectionAsync` + צליל קצר |
| מעבר לחלק הבא בקומבו | כרטיס החלק הנוכחי יוצא לכיוון הקריאה, הבא נכנס: translate 24pt + fade, 240ms `EASE_OUT`, popLayout | `impactAsync(Light)` + קול: שם החלק הבא |
| סיום סט | המד הנושם מתקדם (2.5) | `notificationAsync(Success)` |
| טיימר מנוחה | טבעת מתרוקנת linear, צבע tint | 5 שניות אחרונות: קול "מתכוננים" |
| "מקס" (בלי יעד) | הטיימר סופר קדימה; כפתור גדול "סיימתי" | — |

### 2.8 מחוות
- **סימון מפרקים על פריים:** `Gesture.Pan` לכל נקודה, track 1:1 עם offset מנקודת האחיזה; שחרור → spring `{duration: 300, dampingRatio: 1}`; rubber-band בקצה הפריים; `impactAsync(Light)` אחד כשהנקודה משוחררת. הקווים והזוויות מתעדכנים ב-worklet בכל פריים, בלי React render.
- **סרגל זמן בסרטון** (התחלה/סוף החזקה): גרירה 1:1, `selectionAsync` בכל 0.5 שנ׳.
- **גיליונות:** formSheet נייטיב — לא בונים בעצמנו.

### 2.9 איפה **לא** מזיזים (find-animation-opportunities)
- **החלפת טאבים** — אין אנימציה (100+ פעמים ביום).
- **מתגים בהגדרות, בחירת אחיזה / צד / תוצאה בזמן רישום** — שינוי צבע בלבד, בלי תזוזה (עשרות פעמים בכל אימון).
- **שורות בציר הזמן ובספריית הסרטונים** — בלי stagger; רק שורה חדשה שנוספה נכנסת.
- **גרפים** — מצוירים פעם אחת בכניסה למסך, לא בכל רענון (מידע שקוראים — לא קישוט).
- **ספרות טיימר רץ** — לא מונפשות, רק מתחלפות (tabular-nums מונע ריקוד).

### 2.10 ערכים קבועים (`src/theme/motion.ts`)
```
EASE_OUT    = bezier(0.23, 1, 0.32, 1)
EASE_IN_OUT = bezier(0.77, 0, 0.175, 1)
SPRING_DEFAULT = { duration: 400, dampingRatio: 1 }
SPRING_SNAP    = { duration: 400, dampingRatio: 0.8 }   // + velocity
SPRING_SHEET   = { duration: 300, dampingRatio: 0.8 }
PRESS = scale 0.97, 120ms, EASE_OUT
BREATH_PERIOD = 4.2s · SWAY_PERIOD = 5.6s · FIELD_PERIOD = 18–24s
```
כל spring עם `reduceMotion: ReduceMotion.System`. 120fps: `CADisableMinimumFrameDurationOnPhone` רלוונטי רק ב-development build — ב-Expo Go נשארים ב-60fps (וזה היעד שביקשת).

---

## 3. קטלוג התרגילים

### 3.1 מבנה — שני צירים
**היררכיה:** מיומנות → וריאציה (תרגיל נפרד) → שלב התקדמות. **הסיווג** יושב על כל תרגיל בשני צירים בלתי תלויים:

| ציר | ערכים | למה |
|---|---|---|
| **דיסציפלינה** `discipline` | `static` · `dynamic` | כללי התאוששות ואימון שונים לגמרי (טבלה 3.2). קומבואים תחרותיים מערבבים את שניהם |
| **כוח** `force` | `push` · `pull` · `core` | איזון עומס בין שרשראות השריר; קובע את הצבע |

- **Statics** — החזקות איזומטריות נגד כוח המשיכה: Planche, Front Lever, Back Lever, Human Flag, Iron Cross, Maltese, Handstand ועוד. הסתגלות איטית של גידים ורצועות, core קיצוני.
- **Dynamics / Freestyle** — תנופה, כוח מתפרץ, מודעות אווירית, תזמון וזרימה, בעיקר על מתח או מקבילים: explosive muscle-ups, bar spins (360, 540), Shrimp Flip, swings, release moves, ופליפים (backflip, frontflip, side flip).
- **תרגיל מעבר** (`kind: 'transition'`) — תרגיל שעובר בין שתי תנוחות סטטיות בלי לצאת מהן. **הפרס** הוא הדוגמה המרכזית (3.4).
- הסיווג נשמר **על הוריאציה** (לא רק על המיומנות), כי וריאציה יכולה להיות שונה מאביה (למשל planche push-up = static/push, אבל explosive planche push-up הוא dynamic). כל הסיווגים ניתנים לעריכה.
- כל **וריאציה** היא תרגיל עצמאי: היסטוריה, סרטונים, סטטיסטיקות ושיא משלה.
- כל **שלב** (tuck, straddle, full...) שייך לווריאציה, עם יעד (שניות או חזרות) לחישוב "כמה רחוק אני מהשלב הבא".
- **דרגת קושי עולמית 1–10** על כל וריאציה ושלב — `tier`, עם `tier_source` (`spec` / `estimate`) ו-`is_user_edited`.
- **אחיזה** (`supinated` סופינציה / `pronated` פרונציה / `neutral` מקביל) — **שדה נפרד**, לא וריאציה. יושבת על כל חלק בתוכנית (ברירת מחדל) ועל כל רישום (מה שעשית בפועל).
- **מכשיר** (`floor` / `parallettes` / `rings` / `bar` / `p_bars` / `wall` / `bench`) ו**צד** (`L` / `R`) — שדות של וריאציה כשהם מגדירים אותה.

### 3.2 כללי אימון והתאוששות לפי דיסציפלינה (`training_rules`)
ערכי ברירת מחדל שמרניים — **הערכה שלי, לא מחקר**, ניתנים לעריכה בהגדרות. המנוע (אקטיבציות עכשיו, מאמן AI בשלב 2) קורא אותם מטבלה ולא מקוד.

| כלל | Statics | Dynamics |
|---|---|---|
| מנוחה מינימלית בין עבודה **מקסימלית** על אותה מיומנות | 48 שעות (גידים מסתגלים לאט) | 24 שעות |
| תקרת "יחידות עומס" ליום לכל מיומנות (`load = tier × שניות/10` או `tier × חזרות`) | 60 | 40 |
| באקטיבציה — עצימות | 60–75% מהשיא בשלב הנוכחי, אף פעם לא מקס | רק טכניקה/שלב קל, 1–3 ניסיונות |
| מתי לא לעשות | אחרי יום עם עומס גבוה על אותה מיומנות | כשעייף (אחרי אימון מלא / מאוחר בחלון), ובלי מקום/מזרן מתאים — דגל `needs_space` |
| סדר בתוך יום מלא | אחרי הדינמיים | **ראשונים** — כשמערכת העצבים רעננה |
| חימום חובה לפני | מפרקי כף יד + כתף | קפיצות/תנופות קלות |

### 3.3 הרשימה (seed) — ערכי הקושי שנתת + השלמות שלי
> הערכים שנתת נשמרים כ-`tier_source: 'spec'`. השלמות שלי מסומנות ✚, וההערכות שלי ≈ כ-`estimate`. אין מקור עולמי מוסמך לדרגות קושי — כולן "התחלתיות וניתנות לעריכה". הסימון בסוגריים: **[דיסציפלינה · כוח]**.

**Statics**
- **Planche** [static · push] · frog stand 1 · lean 2 · tuck 4 · adv. tuck 5 · straddle 7 · half-lay 7.5 · full 8
  - וריאציות: standard · narrow/close · wide · **Maltese 9.5** · two-finger · **one-arm 10** (✚ שמאל/ימין כנפרדים) · ✚ elbow lever ≈3
  - **Planche push-ups** [static · push]: tuck ≈6 · straddle ≈8 · full ≈9
- **Handstand** [static · push] · wall 2 · freestanding 4 · ✚ straddle/stag shapes ≈4.5 · on parallel bars ≈5 · one-arm L 8.5 · one-arm R 8.5
  - **HSPU** [static · push]: pike ≈3 · wall ≈5 · freestanding ≈7.5 · 90° 9
- **Front Lever** [static · pull] · tuck 3 · adv. tuck 4.5 · one-leg 5.5 · straddle 6.5 · half-lay 7 · full 7.5
  - וריאציות: tuck narrow/close grip · tuck wide grip · raises ≈6 · pull-ups ≈7.5 · ice-cream makers ≈6.5 · **one-arm 10**
- **Back Lever** [static · pull] · German hang 2 · tuck 3 · adv. tuck 4 · straddle 5 · full 6 · pull-outs ≈6
- **Human Flag** [static · pull] · tuck ≈4.5 · straddle ≈6 · full ≈7 (✚ הכוח האמיתי מעורב; מסווג pull לפי היד העליונה — ניתן לשינוי)
- ✚ **Iron Cross** [static · pull] (טבעות) · band-assisted ≈6 · full ≈8.5
- **Maltese** — וריאציה של Planche (למעלה), ✚ maltese על טבעות ≈10
- **Hefesto 8.5** (✚ negatives ≈7) · **Pelican** ≈7.5 (✚ negatives ≈6) · **Victorian 10** (✚ negatives ≈8.5) [static · pull]
- **Core:** L-sit ≈2.5 (✚ tuck L-sit 1.5) · V-sit ≈6 · **Manna 9** · ✚ dragon flag ≈5 [static · core]

**Transitions (מעברים)** — ראה 3.4
- **Planche Press** (פרס) [static · push]: **wide** · **standard** · **narrow** — שלושה תרגילים נפרדים; שלבים לפי צורת הגוף: tuck press ≈6 · straddle press ≈8 · full press ≈9
- **Pike / straddle press to handstand** (מהרצפה) [static · push]: pike ≈6 · straddle ≈5.5

**Dynamics / Freestyle**
- **Muscle-up** [dynamic · pull]: bar ≈4.5 · rings ≈5 · ✚ explosive / high muscle-up ≈6.5 · ✚ 360 muscle-up ≈8.5
- ✚ **Bar spins** [dynamic · pull]: 180 ≈4 · 360 ≈6 · 540 ≈8
- ✚ **Shrimp Flip** [dynamic · pull] ≈7.5
- ✚ **Swings** [dynamic · pull]: kip/swing ≈2 · ✚ giant swing (מתח) ≈6.5
- ✚ **Release moves** [dynamic · pull]: bar hop ≈3.5 · ✚ release & regrab ≈6
- **Flips** [dynamic · core]: **Backflip** ≈5 · **Frontflip** ≈5.5 · **Side Flip** ≈5 (נוסף לפי בקשתך) — דגל `needs_space`
- **Push-ups** [static · push, למעט explosive]: standard 1 · diamond ≈2 · archer ≈3.5 · pseudo-planche ≈4 · explosive/clap ≈3.5 [dynamic · push] · one-arm L/R ≈6 · 90° ≈8.5
- **Dips** [static · push]: parallel bar ≈2 · **Bench Dips** ≈1.5 · ring ≈4 · Korean ≈4.5 · ✚ ring support (RTO) ≈3
- **Pull-ups** [static · pull]: standard 1.5 · chin-up 1.5 · wide 2.5 · archer ≈4.5 · typewriter ≈5 · one-arm L/R 9 · ✚ L-sit pull-up ≈3.5

> חזרות (push-ups, dips, pull-ups) מסווגות static כי הן עבודת כוח מבוקרת בלי תנופה; גרסאות מתפרצות — dynamic. "Push-Leg / Pull-Leg routines" הוסרו מהרשימה: לפי ההסבר שלך כל יום הוא Push + Pull ביחד (3.5).

### 3.4 הפרס — תרגיל מעבר
**הגדרה (שלך):** פרס = פלאנץ׳ → עלייה בפרס לעמידת ידיים → ירידה חזרה לפלאנץ׳, וחוזר חלילה — **בלי לצאת מהפלאנץ׳ לאורך כל הסט.**
- `kind: 'transition'`, `from_pose: planche`, `to_pose: handstand`. הציר = כפות הידיים (בדיוק מה שראינו ב-`פלאצ.mp4`).
- **וריאציות:** `planche-press-wide` · `planche-press-standard` · `planche-press-narrow` — כל אחת תרגיל עם היסטוריה ושיא משלה.
- **מדדים לכל סט פרסים** (ב-`part_logs`): `reps` (מספר הפרסים) · `planche_hold_max_s` (**זמן ההחזקה המקסימלי בפלאנץ׳ בתוך הסט**) · `set_duration_s` · `broke_planche` (האם יצאת מהפלאנץ׳ — כן = הסט הסתיים).
- **פוש #2 "מקס החזקה לפרס רחב"** = וריאציה `planche-press-wide`, מטרה `max_hold_in_transition` → המדד הראשי הוא `planche_hold_max_s`, והחזרות נרשמות כמדד משני.
- במסך האימון: טיימר שרץ על כל הסט + כפתור "שלב פלאנץ׳" / "שלב עמידה" (נגיעה אחת בכל מעבר) → האפליקציה מחשבת את ההחזקה הארוכה ביותר בפלאנץ׳. מתג "פשוט" — רישום ידני של המספר בסוף.
- בניתוח וידאו (חלק 8): סימון תחילת/סוף כל החזקה בפלאנץ׳ על סרגל הזמן.

### 3.5 התוכנית של Shawn Sama — רצף ימים
**העיקרון המרכזי:** התוכנית היא **רצף של ימים** → כל יום = אימון מלא שמכיל **גם Push וגם Pull** → כל יום מורכב מ**בלוקים** (קומבו = 3 תרגילים לפחות בסט; בלוק עם חלק אחד = תרגיל בודד) → כל בלוק מורכב מ**חלקים** (תרגילים). התוצאות שלך מחוברות לכל רמה: יום ← בלוק ← סט ← חלק.

**תבנית מול מופע:**
- **תבנית היום** (`program_days`) — מה Shawn כתב. לא משתנה מעצמה.
- **מופע יום** (`day_plans`) — היום הקונקרטי שאתה הולך לעשות (יום 1, יום 2, ... יום 37), עותק של התבנית עם יעדים ממשיים. **כאן** נכנסות התאמות: בשלב 2 המאמן AI כותב את `day_plans` הבאים לפי ההתקדמות שדיווחת, עם `adjustments` (מה שונה ולמה). ב-v1 המופעים נוצרים מהתבנית כמו שהיא, ואתה יכול לערוך ידנית.
- **ביצוע** (`sessions` → `set_logs` → `part_logs`) — מצביע על המופע ועל החלק המתוכנן, כך שתמיד יש "תוכנן ↔ בוצע" זה לצד זה.

**ה-PDF מכיל שלושה חלקים** (פוש, פול, פלאנץ׳ יד אחד). **הנחה שלי ל-seed — צריך אישור שלך (שאלה 1):**
- **יום A** = פוש (6 בלוקים) + פול (6 בלוקים)
- **יום B** = פלאנץ׳ יד אחד (4 בלוקים) + פול (6 בלוקים)
- הרצף: A → B → A → B... (עם ימי מנוחה לפי מה שתגדיר)

**פוש** (סקשן `push`)
| # | שם בתוכנית | חלקים | סטים | מנוחה |
|---|---|---|---|---|
| 1 | קומבו מקס פוש אפ למקס פרסים למקס החזקה | **קומבו:** push-up (max reps) → planche press standard (max reps) → planche hold (max s) | 6 | feel |
| 2 | מקס החזקה לפרס רחב | **planche press wide** — מדד ראשי: החזקת פלאנץ׳ מקסימלית בתוך הסט (3.4) | 5 | feel |
| 3 | מקס פרסים סופי / רצפה | planche press (max reps), אחיזה: **supinated** / floor | 5 | feel |
| 4 | פוש אפ רצפה להחזקה 3 שניות | push-up + 3 s hold בכל חזרה (max reps) | 5 | feel |
| 5 | הרמת מלטיז עם גומיה | maltese raise, band (max reps) | 5 | feel |
| 6 | מקס החזקה רצפה מלטיז גומיה | maltese hold, floor, band (max s) | 5 | 30 s |

**פול** (סקשן `pull`)
| # | שם בתוכנית | חלקים | סטים | מנוחה |
|---|---|---|---|---|
| 1 | צמוד רחב | tuck front lever, wide grip (max s) | 5 | feel |
| 2 | צמוד רגיל | tuck front lever, regular grip — **pronated** (max s) | 4 | feel |
| 3 | פרסים בצמוד | tuck front lever raises (max reps **in 60 s**) | 4 | 60 s |
| 4 | פול אפ בפרונט | front lever pull-ups (6–8 reps) + hold at the end | 4 | 40 s |
| 5 | פרסים בפרונט סופי או רגיל | front lever raises, grip **supinated or pronated** (max reps) | 4 | feel |
| 6 | פרונט צמוד עם גומיה | tuck front lever, band (max s) | 4 | feel |

**פלאנץ׳ יד אחד** (סקשן `push`)
| # | שם בתוכנית | חלקים | סטים | מנוחה |
|---|---|---|---|---|
| 1 | החזקה פלאנץ יד אחד (2 ידיים) | one-arm planche hold — L & R (max s) | 4 | 60 s |
| 2 | הישענות יד אחד רגל אחד באוויר | one-arm lean, one leg up — 15 s per side | 4 | 30 s |
| 3 | הישענות יד אחד 2 רגליים על רצפה | one-arm lean, both feet down — 20 s per side | 3 | 30 s |
| 4 | פלאנץ יד עם גומיה | one-arm planche, band — L & R (max s) | 3 | 60 s |

**טיפים לטכניקה (עמוד 1)** נשמרים כ-`form_cues` של Front Lever ו-Planche, ומוצגים במסך האימון.

> **הערה:** לפי ההגדרה שלך, רק פוש #1 הוא קומבו אמיתי (3 חלקים). המודל תומך גם בקומבואים תחרותיים שמערבבים Statics ו-Dynamics ו-Push ו-Pull באותו סט (למשל planche → press → handstand → muscle-up), בלי שינוי.

---

## 4. ארכיטקטורת הנתונים

### 4.1 החלטות
- **SQLite** (`expo-sqlite`) עם migrations ממוספרות. הגדרות ב-`expo-sqlite/kv-store`.
- **שכבת driver** (`SqlDriver`) — expo-sqlite באפליקציה, **sql.js** (WASM, בלי קומפילציה נייטיב — עובד ב-Windows) בבדיקות. כך כל repository נבדק ב-Jest.
- סרטונים בתיקיית המסמכים של האפליקציה (`expo-file-system`), ב-DB רק URI + תמונה ממוזערת. העתק אופציונלי ל-Photos (`expo-media-library`).
- **מבנה מרכזי:** `program → program_days → day_sections (push/pull) → blocks → block_parts` (תבנית), ומעליה `day_plans → plan_blocks → plan_parts` (מופע מותאם), ומתחת `sessions → set_logs → part_logs` (ביצוע). שלוש שכבות נפרדות = המאמן יכול לשנות את העתיד בלי לגעת בעבר ובלי לגעת במה ש-Shawn כתב.
- **מוכן ל-AI:** כל רישום שומר גם **מה תוכנן** (מצביע ל-`plan_parts`) וגם **מה בוצע** (אחיזה, צד, גומייה, שלב, מדדים), עם זמני ISO. כל התאמה לתוכנית נשמרת ב-`plan_adjustments` עם `author` ו-`reason` — כך המאמן יודע מה כבר ניסה.

### 4.2 טבלאות
```
-- קטלוג
skills          id, slug UNIQUE, name_he, name_en, pose_key,
                discipline ('static'|'dynamic'), force ('push'|'pull'|'core')         -- ברירת מחדל לוריאציות
variations      id, skill_id→skills, slug UNIQUE, name_he, name_en,
                kind ('hold'|'reps'|'transition'|'move'),
                discipline ('static'|'dynamic'), force ('push'|'pull'|'core'),       -- חובה על כל תרגיל
                from_pose NULL, to_pose NULL,                                          -- לתרגילי מעבר (פרס)
                apparatus NULL, side NULL ('L'|'R'), width NULL ('narrow'|'standard'|'wide'|'maltese'),
                needs_space (0/1), tier REAL, tier_source ('spec'|'estimate'), is_user_edited, pose_key NULL
stages          id, variation_id→variations, position, name_he, name_en, tier, tier_source,
                target_metric ('hold_s'|'reps'|'planche_hold_max_s'), target_value, pose_key NULL
form_cues       id, skill_id→skills, position, text_he, text_en NULL
training_rules  discipline PRIMARY KEY, min_rest_h_max_effort, daily_load_cap, activation_intensity_min,
                activation_intensity_max, activation_max_attempts, is_user_edited      -- טבלה 3.2

-- תוכנית: תבנית (מה Shawn כתב)
programs        id, title, coach ('Shawn Sama'), source_file, imported_at, is_active,
                sequence_json                                                           -- למשל ["A","B"] + ימי מנוחה
program_days    id, program_id→programs, key ('A'|'B'|…), position, title_he, title_en NULL
day_sections    id, day_id→program_days, position, force ('push'|'pull'|'core'), title_he  -- "פוש" / "פול" / "פלאנץ׳ יד אחד"
blocks          id, section_id→day_sections, position, name_he, name_en NULL, summary_he, summary_en NULL,
                sets, rest_kind ('feel'|'seconds'), rest_seconds NULL, instructions_he
block_parts     id, block_id→blocks, position, name_he, name_en NULL, variation_id NULL, stage_id NULL,
                target_kind ('hold_s'|'reps'|'max_hold'|'max_reps'|'max_reps_in_window'|'reps_range'
                             |'max_hold_in_transition'),
                target_value NULL, target_max NULL, window_s NULL, rep_hold_s NULL,
                grip NULL ('supinated'|'pronated'|'neutral'), grip_alt NULL, apparatus NULL,
                band, per_side, hold_at_end_s NULL

-- תוכנית: מופעים (הימים בפועל — מה שהמאמן מתאים)
day_plans       id, program_id, day_id→program_days, day_number (1, 2, 3…), planned_date NULL,
                status ('upcoming'|'in_progress'|'done'|'skipped'|'rest'),
                origin ('template'|'user'|'coach'), based_on_session_ids_json NULL, created_at
plan_blocks     id, day_plan_id→day_plans, block_id NULL (מקור בתבנית), position, sets, rest_kind, rest_seconds
plan_parts      id, plan_block_id→plan_blocks, block_part_id NULL, variation_id, stage_id NULL,
                target_kind, target_value NULL, target_max NULL, window_s NULL, grip NULL, band, per_side
plan_adjustments id, day_plan_id, plan_part_id NULL, author ('user'|'coach'|'rule'), field, from_value,
                to_value, reason_he, reason_en NULL, created_at

-- ביצוע (אימונים + אקטיבציות)
sessions        id, kind ('workout'|'activation'), day_plan_id NULL, activation_id NULL,
                started_at, ended_at NULL, feel 1..5 NULL, notes NULL, schema_version
set_logs        id, session_id→sessions, plan_block_id NULL, set_index, completed, outcome, note NULL, logged_at
part_logs       id, set_log_id→set_logs, plan_part_id NULL, variation_id, stage_id NULL,
                grip NULL, side NULL, band, reps NULL, hold_s NULL,
                planche_hold_max_s NULL, set_duration_s NULL, broke_planche NULL,       -- תרגילי מעבר
                outcome ('success'|'failure'|'micro'), load_units REAL                  -- לחישוב עומס (3.2)
stage_events    id, variation_id, from_stage_id NULL, to_stage_id, at,
                source ('post_workout'|'activation'|'analysis'|'baseline'|'manual')
progress_entries id, date, track ('workout'|'activation'), variation_id NULL, outcome, text,
                metric_value NULL, metric_unit NULL, source ('manual'|'session'|'analysis'|'activation')

-- אקטיבציות (חלק 6)
activations     id, scheduled_at, fire_at (אחרי ג׳יטר), notification_id,
                suggestion_json (חלקים + יעדים), reason_json (למה נבחר: שלב, עומס, כלל),
                rule_version, status ('pending'|'done'|'skipped'|'expired'|'cancelled'),
                responded_at NULL, response_channel ('notification'|'app') NULL,
                feel NULL, achieved_json NULL, note NULL, session_id NULL

-- וידאו וניתוח
videos          id, uri, thumb_uri, variation_id NULL, stage_id NULL, session_id NULL, activation_id NULL,
                recorded_at, duration_ms, source ('camera'|'library'|'instagram_export'),
                ig_kind NULL ('post'|'story'|'archived_story'|'reel'), ig_caption NULL,
                is_reference (0/1), reference_athlete_id NULL
analyses        id, video_id→videos, method ('manual'|'auto'), status, hold_s NULL,
                hold_start_ms NULL, hold_end_ms NULL, frame_ms NULL, joints_json NULL,
                body_line_deg NULL, shoulder_lean_deg NULL, hip_deg NULL, created_at
baseline        id, variation_id, stage_id, best_metric, unit, evidence_video_ids_json,
                assessed_at, is_estimate (1)                                            -- הרמה ההתחלתית שלך
athletes        id, display_name, handle, profile_url, focus_he, focus_en
benchmarks      id, variation_id, stage_id NULL, athlete_id NULL,
                metric ('hold_s'|'reps'|'stage'|'body_line_deg'|'shoulder_lean_deg'),
                value, unit, is_estimate (תמיד 1), source_url NULL, source_note, observed_at, is_user_edited

-- מאמן AI (שלב 2 — רק הטבלאות נוצרות ב-v1)
coach_messages  id, role ('user'|'coach'), text, created_at, context_snapshot_json NULL
```

### 4.3 הגדרות (kv-store)
`language` (`he` ברירת מחדל) · `haptics` · `sounds` · `voice` · `reduceMotionOverride` · `program.restDays` · `reminders` (ימים + שעה) · `combo.toggles` (holdTimer, restTimer, voice, camera — זוכר את הבחירה האחרונה) · `activation.*` (כל הפרמטרים — טבלה 6.6).

### 4.4 `CoachContextV1` — הנתונים למאמן העתידי
`buildCoachContext({ since }): CoachContextV1` מחזיר: גרסת schema · פרופיל (רמה התחלתית + שלב נוכחי לכל וריאציה) · **רצף הימים**: לכל `day_plan` — מה תוכנן, מה בוצע לכל חלק, ההרגשה וההערות, וההתאמות שנעשו · עומס מצטבר לפי דיסציפלינה וכוח · אירועי שלב · אקטיבציות (בנפרד) · ניתוחים · `training_rules`. **החוזה ההפוך** (מה המאמן מחזיר בשלב 2): `CoachPlanV1 = { day_plans: [...], adjustments: [{ field, from, to, reason }] }` → נכתב ל-`day_plans` עם `origin: 'coach'` ומוצג לך לאישור לפני שהוא נכנס לתוקף. ב-v1: ייצוא JSON שאפשר להדביק בצ׳אט.

---

## 5. מסכים וזרימה

```
Splash (סטטי, דמות בפלאנץ׳) ─► כניסה "הפרס" ─► NativeTabs
  ├─ היום            (tabs)/today/index        אימון היום + החלפה בנגיעה + מד נושם כפול
  │    └─ אימון חי   session/[dayPlanId]       fullScreenModal
  │         └─ סיכום session/[id]/summary     רישום אחרי אימון
  ├─ מיומנויות       (tabs)/skills/index       עץ: קטגוריות → מיומנויות → וריאציות
  │    ├─ מיומנות    (tabs)/skills/[skill]
  │    └─ וריאציה    (tabs)/skills/v/[variation]   שלבים, היסטוריה, סרטונים, השוואה
  ├─ סרטונים         (tabs)/videos/index       לפי תאריך / מיומנות
  │    ├─ ניתוח      (tabs)/videos/[id]        סימון מפרקים + זמן החזקה
  │    └─ השוואה     (tabs)/videos/compare     צד-לצד + מדדים
  └─ התקדמות         (tabs)/progress/index     שני מסלולים: אימונים | אקטיבציות
       └─ רישום מהיר progress/log              formSheet
אקטיבציה             activation/[id]           formSheet מהתראה: רישום 10 שניות
  └─ סטטיסטיקה      activation/stats          רצפים, אחוז ביצוע, שעות
הרמה שלי            baseline                  ייבוא Instagram export → הערכה
ייבוא תוכנית         program/import → program/review
מאמן (Placeholder)   coach                     שלב 2
הגדרות               settings                  formSheet מכפתור בכותרת
```

| מסך | תוכן | פעולה ראשית |
|---|---|---|
| **היום** | **"יום 12 · A"** — היום הבא ברצף, עם שני הסקשנים (Push + Pull) ואייקון חי גדול; תווית אם המאמן/אתה שיניתם משהו מהתבנית; שורת "החלף יום" (נגיעה אחת); האקטיבציה הבאה ("בעוד ~40 דק׳"); מד נושם של היום (אימונים + אקטיבציות); "המשך אימון" אם יש סשן פתוח | "התחל אימון" |
| **אימון חי** | **למעלה:** תיאור קצר וברור של הקומבו (למשל *"6 סטים: מקס פוש-אפ ← מקס פרסים ← מקס החזקה"*). **ארבעה מתגים נפרדים:** טיימר החזקה · טיימר מנוחה · קול · מצלמה. במרכז: החלק הנוכחי, הטיימר/מונה הגדול, אחיזה ומכשיר. למטה: "סיימתי" / רישום חזרות. הפטיקה+צלילים: מתג משלהם | "סיימתי חלק / סט" |
| **סיכום אימון** | איך הרגשת (1–5) · השלב שהגעת אליו בכל וריאציה שאומנה · הערות · כל הסטים (קומבו = סט) עם חזרות/שניות לכל חלק — ממולא מראש מהרישומים, ניתן לעריכה | "שמור" |
| **עץ מיומנויות** | 4 קטגוריות; בכל אחת — מיומנויות עם אייקון חי, שלב נוכחי, דרגת קושי, ו"כמה נשאר לשלב הבא" (טבעת) | פתיחה |
| **וריאציה** | סולם שלבים (כל שלב עם אייקון), שיא אישי, גרף, סרטונים, בנצ׳מרקים | "הקלט" / "השווה" |
| **סרטונים** | גריד לפי תאריך, פילטר מיומנות; סרטוני רפרנס מסומנים | "ייבא סרטון" |
| **ניתוח** | נגן + סרגל התחלה/סוף החזקה + פריים לסימון 7 מפרקים → זוויות | "שמור" |
| **השוואה** | שני נגנים מסונכרנים (שלך ↔ רפרנס), קווי שלד על שניהם, טבלת מדדים, "% מהדרך" | — |
| **התקדמות** | שני מדים נושמים + ציר זמן; פילטר וריאציה; גרף שיא החזקה | "+" |
| **אקטיבציה** | הקומבו המוצע + הסיבה, איך הרגיש (5 צ׳יפים), מה השגת (סטפרים ממולאים ביעד), פתק | "שמור" |
| **סטטיסטיקת אקטיבציות** | שני רצפים, אחוז ביצוע, מפת שעות, התפלגות static/dynamic ו-push/pull | — |
| **הרמה שלי** | ייבוא export, תיוג סרטונים, שלב התחלתי לכל מיומנות עם סרטון כראיה | "ייבוא" |
| **הגדרות** | שפה · הפטיקה · צלילים · קול · אקטיבציות (כל הפרמטרים של טבלה 6.6) · כללי אימון לפי דיסציפלינה (3.2) · תזכורות אימון · ייצוא/ייבוא גיבוי · הפחתת תנועה | — |
| **מאמן Shawn Sama** | Placeholder: הסבר קצר + כפתור "ייצא נתונים למאמן" (CoachContextV1) | שלב 2 |

---

## 6. מערכת האקטיבציות — המערכת המתקדמת באפליקציה
> תעשה אקטיבציות הרבה יותר מאימונים מלאים. לכן זו המערכת עם הכי הרבה לוגיקה, הכי הרבה בדיקות, והכי מעט חיכוך: מהתראה ועד רישום — **10 שניות**.

### 6.1 מבנה — ארבעה מודולים טהורים
```
planWindow(settings, calendar, now, rand)  → זמני ירי        (6.2)
suggest(state, rules, firedAt)             → קומבו + סיבה    (6.3)
respond(activation, action, input)         → רישום           (6.4)
activationStats(history, today)            → רצפים וסטטיסטיקה (6.5)
```
כולם פונקציות טהורות ב-`src/domain/activation/`, עם `rand` ו-`now` מוזרקים → נבדקים ב-Jest בלי מכשיר. `services/notifications.ts` רק מחבר אותם ל-expo-notifications.

### 6.2 תזמון חכם
- **בסיס:** כל `intervalHours` (ברירת מחדל 2.5) בתוך **שעות הפעילות** (ברירת מחדל 09:00–22:00; חלון שחוצה חצות נתמך).
- **הפתעה:** לכל אקטיבציה offset אקראי בטווח `±jitterMin` (ברירת מחדל ±35 דק׳), עם מרווח מינימלי `minGapMin` (ברירת מחדל 75 דק׳) בין שתיים — כך זה לא מרגיש כמו שעון מעורר, אבל גם לא מגיע פעמיים ברצף.
- **שעות שקטות:** רשימת חלונות (למשל 13:00–14:00, או "ימי שישי אחרי 15:00") — אף פעם לא יורה בתוכם. מצב "אל תפריע" של iOS מכובד ממילא.
- **סביב אימון מלא:** אין אקטיבציה `bufferBeforeMin` (ברירת מחדל 90) לפני אימון מתוכנן ו-`bufferAfterMin` (ברירת מחדל 120) אחריו. "אימון מתוכנן" = תזכורת האימון של היום / `day_plans.planned_date`; "אחרי" = `sessions.ended_at` אמיתי. התחלת אימון → ביטול כל האקטיבציות בחלון החסום (`cancelled`), וסיום אימון → תזמון מחדש.
- **ימי מנוחה:** אפשרות `restDayMode` — רגיל / רק Statics קלים / כבוי.
- **תקרה יומית:** `maxPerDay` (ברירת מחדל 5).
- **מגבלת iOS של 64 התראות:** מתזמנים מתגלגל — רק `horizon` הבאות (12), משלימים בכל פתיחה, בכל תגובה ובכל שינוי הגדרה. בדיקה: לעולם לא יותר מ-12 ממתינות.

### 6.3 בחירת הקומבו (rule-based ב-v1, AI בשלב 2)
כל אקטיבציה מציעה **קומבו קצר וספציפי** (1–3 חלקים, ≤ 3 דקות), מחושב **ברגע התזמון** ומחושב מחדש בכל פתיחה של האפליקציה (כי ההתראה צריכה לדעת מה לכתוב מראש).

**הצנרת:**
1. **מועמדים:** כל וריאציה שיש לך בה שלב נוכחי (מהרמה ההתחלתית או מהרישומים).
2. **סינון לפי עומס ו-`training_rules`:** מוציאים מיומנות static שקיבלה עבודה מקסימלית ב-48 השעות האחרונות; מוציאים כל מיומנות שעברה את תקרת העומס היומית; מוציאים dynamic אם יש אימון מלא היום / אחרי 20:00 / `needs_space` ולא סימנת "יש לי מקום עכשיו".
3. **ניקוד:** `score = עדיפות שלב (קרוב לשלב הבא ⇒ גבוה) + זמן מאז שאומנה + איזון push/pull של היום + איזון static/dynamic של השבוע − עומס אחרון`. שוויון → `rand`.
4. **בניית הקומבו:** לוקחים את הראשון + עד שני חלקים משלימים (כוח הפוך או אותה מיומנות בשלב קל יותר). יעד = 60–75% מהשיא בשלב הנוכחי (statics) / 1–3 ניסיונות טכניים (dynamics).
5. **סיבה:** `reason_json` נשמר ומוצג במסך ("כי ה-tuck front lever שלך 2 שנ׳ מהיעד, ולא עבדת עליו יומיים").

דוגמה: *"אקטיבציה: 3× Tuck Front Lever 8 שנ׳ → 5 Planche Leans"*.

**שלב 2:** אותו ממשק `suggest(...)` — מימוש AI שמקבל `CoachContextV1` ומחזיר אותו פורמט. החוקים נשארים כמעקה בטיחות (AI לא יכול להציע static מקסימלי ב-48 שעות).

### 6.4 תשובה ישר מההתראה
- **Notification category** עם שני כפתורים: **"בוצע"** ו**"דילגתי"**.
  - **"דילגתי"** — לא פותח את האפליקציה; נרשם מיד אם ה-JS רץ, אחרת נאסף בפתיחה הבאה (`getLastNotificationResponseAsync` + בדיקת התראות שנמסרו).
  - **"בוצע"** — פותח את האפליקציה **ישר לגיליון הרישום** (deep link ל-`activation/[id]`).
  - ✚ אפשרות iOS לכפתור עם שדה טקסט ("בוצע + פתק") — לאמת.
  - **לאמת ב-Expo Go של SDK 58** ש-categories ופעולות עובדים; fallback: נגיעה בהתראה פותחת את הגיליון עם שני הכפתורים.
- **רישום של 10 שניות** (formSheet בגובה חצי מסך):
  1. **איך הרגיש** — 5 צ׳יפים (קל מדי · טוב · מאתגר · קשה · כאב) — נגיעה אחת.
  2. **מה השגת** — לכל חלק סטפר אחד **ממולא מראש ביעד** (שניות או חזרות). רוב הפעמים לא נוגעים — רק "שמור".
  3. **פתק** — אופציונלי, שורה אחת.
  - "שמור" = `sessions(kind='activation')` + `part_logs` (אותו מודל כמו אימון!) + `outcome` אוטומטי מול היעד.
- **פג תוקף:** לא נענתה תוך `expireMin` (ברירת מחדל 120 דק׳) → `expired` (לא נספר כדילוג).
- **אימון פתוח** כשאקטיבציה אמורה לירות → `cancelled` (לא מפריעים).

### 6.5 מעקב משלה — אבל אותו מודל התקדמות
- **מסלול נפרד:** מד נושם משלו (`activation` teal), ציר זמן משלו, מסך סטטיסטיקה משלו.
- **רצפים:** רצף ימים עם ≥ 1 אקטיבציה שבוצעה · רצף "בוצע" ברצף (בלי דילוג) · השיא של כל אחד.
- **סטטיסטיקות:** אחוז ביצוע (בוצע / (בוצע + דילגתי)) לשבוע ולחודש · זמן תגובה ממוצע · סה״כ שניות החזקה וחזרות לפי מיומנות · התפלגות לפי שעה ביום (מתי אתה הכי נענה — ומשם הצעה לכוון את שעות הפעילות) · התפלגות static/dynamic ו-push/pull.
- **מוזן לאותו מודל:** הביצוע נשמר ב-`part_logs`, נספר בעומס (`load_units`), ויכול לייצר `stage_events` עם `source: 'activation'` (למשל החזקה מעל יעד השלב → הצעה לעלות שלב). כך אקטיבציות משפיעות על האימון המלא הבא ועל האקטיבציות הבאות.

### 6.6 הגדרות (הכל ב-Settings → אקטיבציות)
| פרמטר | ברירת מחדל |
|---|---|
| `enabled` | כבוי עד שתפעיל (בהפעלה: בקשת הרשאת התראות) |
| `intervalHours` | 2.5 |
| `activeFrom` / `activeTo` | 09:00 / 22:00 |
| `quietWindows` | ריק |
| `jitterMin` · `minGapMin` | 35 · 75 |
| `bufferBeforeMin` · `bufferAfterMin` | 90 · 120 |
| `maxPerDay` | 5 |
| `restDayMode` | רק Statics קלים |
| `allowDynamics` · `assumeSpace` | כן · לא |
| `intensity` | 60–75% |
| `expireMin` | 120 |
| `pool` | אוטומטי (כל המיומנויות) / בחירה ידנית |

## 7. פענוח התוכנית
- **v1 (בלי שרת):** אין פענוח PDF אמין במכשיר. לכן: בוחרים PDF → הקובץ מוצג לצד **עורך מובנה** (אימונים → בלוקים → חלקים) שממולא מראש מתבנית Shawn Sama (חלק 3.5). אתה מתקן/מוסיף ידנית → `validateProgram` → שמירה. התוכנית הנוכחית מגיעה כ-seed.
- **שלב 2 (שרת):** שליחת ה-PDF לשרת שקורא אותו ויזואלית עם Claude ומחזיר JSON לפי אותה schema. (כבר ראינו שחילוץ טקסט רגיל שובר את הטבלאות בעברית.) המפתח לעולם לא באפליקציה. אותו מסך סקירה.

## 8. וידאו, השוואה ואתלטים

### 8.1 v1 — חצי ידני (מאחורי `PoseDetector`)
```
interface PoseDetector { detect(videoUri: string, frameMs: number): Promise<Joints | null> }
```
- **v1:** `ManualPoseDetector` — מחזיר `null`, וה-UI מבקש ממך לסמן 7 מפרקים. **שלב 2 – needs dev build:** `VisionPoseDetector` (Apple Vision / MediaPipe) ממלא את אותם `Joints` אוטומטית; אותו UI, רק שהנקודות כבר מסומנות לאישור.
- מדדים: זמן החזקה (מסרגל ההתחלה/סוף) · זווית קו גוף מול האופק · הישענות כתף (כתף מול כף היד) · זווית ירך.
- **"% מהדרך"** = שילוב של (א) מיקום השלב שלך בין השלב הקודם לבא (לפי tier), ו-(ב) יעד ההחזקה של השלב הבא. נוסחה מדויקת במשימה 12.

### 8.2 צד-לצד
- שני `VideoView` מסונכרנים (play/pause/seek משותף), כל אחד עם שכבת שלד.
- **סרטון הרפרנס** — קובץ שאתה מייבא (למשל קליפ ששמרת). האפליקציה לא מורידה תוכן מאינסטגרם/יוטיוב.

### 8.3 הרמה ההתחלתית שלך — ייבוא ה-Data Export של אינסטגרם
**הכלל: האפליקציה לעולם לא מבקשת ולא שומרת את סיסמת האינסטגרם שלך** ולא מתחברת לאינסטגרם בכלל. אתה מוריד את הנתונים שלך בעצמך, והאפליקציה קוראת קובץ.

**צעדים שלך (פעם אחת):** Instagram → הגדרות → Your activity → **Download your information** → בחירת החשבון → **Some of your information** → Content (Posts, Stories, Reels — כולל stories בארכיון) → פורמט **JSON**, איכות מדיה **High**, טווח **All time** → Create files. אינסטגרם שולח לינק להורדה (בדרך כלל תוך שעות עד יומיים), וה-ZIP נשמר ב-Files.

**בתוך האפליקציה (v1, Expo Go):**
1. הגדרות → "ייבוא מאינסטגרם" → בחירת **תיקייה מחולצת** (ב-Files: נגיעה ב-ZIP מחלצת אותו) או בחירת ה-ZIP ישירות אם הוא קטן מ-≈300MB (חילוץ ב-JS עם `fflate`, בלי מודול נייטיב).
2. קריאת קבצי ה-JSON (posts / stories / archived stories / reels) → רשימת סרטונים עם **תאריך, סוג וכיתוב**.
3. סינון: רק וידאו; האפליקציה מציעה תיוג מיומנות לפי מילות מפתח בכיתוב (planche, פלאנץ׳, front, לבר...) — אתה מאשר/מתקן בנגיעה.
4. כל סרטון נכנס לספרייה עם `source: 'instagram_export'` ואותו כלי ניתוח (8.1): סרגל החזקה + סימון מפרקים.
5. **מסך "הרמה שלי":** לכל מיומנות — השלב הגבוה ביותר שמופיע בסרטונים, ההחזקה הארוכה ביותר, ו**ציר זמן** של ההתקדמות שלך לפי תאריכי הפוסטים. נשמר ב-`baseline` עם הסרטונים כראיה, ומשמש כנקודת ההתחלה לשלבים, לאקטיבציות ולמאמן.

**אפשרות מהירה עכשיו (לפני שיש אפליקציה):** אם תשים את ה-export ב-`docs/source/instagram/`, אני יכול לחלץ פריימים עם ffmpeg ולתת לך הערכת רמה ראשונה כבר בשלב התכנון — תיכנס ל-seed כ-`baseline` עם `is_estimate = 1`. ⚠️ ה-export מכיל גם הודעות ומידע אישי אם תבחר "All information" — לבחור רק Content. ואם תשים אותו ב-repo, להחליט אם להוסיף ל-`.gitignore` (זה תוכן אישי שיעלה לגיטהאב).

### 8.4 אתלטי רפרנס — הערכות מהפרופילים הציבוריים
**שיטה:** דרך Claude in Chrome — ה-Chrome המחובר שלך, צפייה בפרופילים ובסרטונים הציבוריים, והערכה לכל מיומנות: השלב הגבוה ביותר שנראה + זמן החזקה מוערך (ספירת שניות בסרטון). **לא מורידים ולא שומרים את הסרטונים שלהם**; שומרים רק את ההערכה + לינק לפוסט.

**סטטוס כרגע: ❌ התוסף לא מחובר** ("Browser extension is not connected"). מה צריך (חלק 15).

**כל מספר של אתלט נשמר כך:** `is_estimate = 1` · `source_url` = הלינק לפוסט/ריל הספציפי · `source_note` (למשל "נספר מהסרטון, ±1 שנ׳, ייתכן חיתוך עריכה") · `observed_at`. מוצג באפליקציה תמיד עם תווית **"הערכה"** ולינק למקור.

| אתלט | Handle | מוקד (לפי מה שכתבת + חיפוש) | הערכות |
|---|---|---|---|
| Valentin Blanc | @valentin_otz | כוח פלאנץ׳ חריג: וריאציות פלאנץ׳, פרס לעמידת ידיים | ⏳ מחכה ל-Chrome |
| Lee-Van | @leevan_ns | — | ⏳ |
| Iliesse Belasri | @iliesse_ns | — | ⏳ |
| Rayan "Onizuka" | @onizuka_ns | אלוף עולם 2026 (לפי מה שכתבת) — לא מצאתי אישור ברשת | ⏳ |
| Nathan Bosech | @nathan.bosech | — | ⏳ |
| Ramazan | @calisrama | — | ⏳ |
| Idan Porat | @idan_.porat | אתלט פלאנץ׳ ישראלי | ⏳ |

- עד שיהיו הערכות, ב-seed אין מספר לאתלט ספציפי. **רפרנס עולמי כללי** (מתויג "רשימת שיאים ציבורית — לא מאומת"): CaliBase — front lever 62 שנ׳ (Michal Urbanik), פלאנץ׳ על פרלטס 47 שנ׳ (Michele Esposito).
- **"% מהדרך"** מול אתלט = המיקום שלך (שלב + החזקה) מול ההערכה שלו באותה וריאציה.

---

## 9. גיבוי
- **v1:** ייצוא קובץ JSON (כל הטבלאות + גרסת schema) דרך share sheet (`expo-sharing`) → שמירה ב-Files. ייבוא: `expo-document-picker` → ולידציה → **מסך אישור** ("זה יחליף את כל הנתונים") → שחזור. הסרטונים עצמם לא בקובץ (גדולים מדי) — נשמרים ב-Photos אם בחרת.
- **שלב 2 – needs dev build:** גיבוי iCloud אוטומטי.

## 10. מבנה קבצים

```
src/
  app/                         מסכים בלבד (חלק 5)
  db/       driver.ts (SqlDriver) · expo-driver.ts · migrations/ · seed/{catalog,shawn-sama,athletes}.ts
            repos/{catalog,program,session,progress,activation,video,benchmark}.ts
  domain/   types.ts · program-schema.ts · combo-machine.ts · day-sequence.ts · load.ts · streak.ts
            progress-math.ts · activation/{plan,suggest,respond,stats}.ts · ig-export.ts · angles.ts · coach-context.ts · backup.ts
  services/ pose/{PoseDetector,manual}.ts · notifications.ts · speech.ts · sound.ts · camera.ts
  i18n/     he.ts · en.ts · index.ts · useT.ts · direction.ts
  theme/    tokens.ts · motion.ts
  motion/   clock.tsx (ClockProvider, useClock, useAmbientActive)
  components/
    skeleton/  poses.ts · Figure.tsx (Skia) · interpolate.ts
    ambient/   LightField.tsx · BreathingLine.tsx
    ui/        PressableScale.tsx · Card.tsx · Toggle.tsx · Segmented.tsx
    workout/   ComboHeader.tsx · PartStage.tsx · BigTimer.tsx · RepCounter.tsx · FeatureToggles.tsx · CameraPip.tsx
    skills/    SkillTree.tsx · SkillRow.tsx · StageLadder.tsx · TierBadge.tsx
    video/     JointMarker.tsx · SkeletonOverlay.tsx · TrimBar.tsx · SyncedPlayers.tsx · MetricTable.tsx
    progress/  Timeline.tsx · OutcomeChip.tsx
  entrance/    Entrance.tsx
```

---

## 11. מגבלות גלובליות
- **Expo Go בלבד** עד שתגיד שפתחת חשבון Apple Developer. כל מודול חייב לעבוד ב-Expo Go של SDK 58. פיתוח על Windows; בדיקה על ה-iPhone שלך דרך Expo Go.
- לפני כל קוד שנוגע ב-API של Expo/RN: לקרוא `https://docs.expo.dev/versions/v58.0.0/` ו-`https://docs.expo.dev/llms.txt`. **לאמת במיוחד:** NativeTabs ו-Link.Preview ב-Expo Go · notification categories ב-Expo Go · `expo-audio` (במקום expo-av) · `Updates.reloadAsync`/`DevSettings.reload` להחלפת שפה · Skia ב-Expo Go.
- התקנה רק `npx expo install`. לא נוגעים ב-`ios/`/`android/`.
- אנימציה: רק Reanimated 4 + Skia. בלי GSAP, בלי `Animated` הישן, בלי ספריות ווב.
- אין ask-sonner, write-swift, webapp-testing.
- כל מחרוזת דרך `useT()`. עברית ברירת מחדל.
- שמות תרגילים = השמות בתוכנית (`name_he` מילה במילה).
- Shawn Sama — באיות הזה בכל מקום.
- אין מספרי אתלטים מומצאים. כל בנצ׳מרק `is_estimate = 1` עם `source_note`.
- לפני סיום משימה: `npx expo lint`, `npx tsc --noEmit`, `npx jest` עוברים (verification-before-completion).

## 12. מוקדי סקירה (Review Focus)
1. **טיימר כשהמסך נעול / האפליקציה ברקע** — ה-combo machine מחושב מחותמות זמן, לא מ-interval; בחזרה מוצג השלב הנכון. (בדיקה במשימה 6)
2. **מגבלת 64 ההתראות + שעות שחוצות חצות** — המתזמן לא חורג ולא מתזמן מחוץ לחלון. (משימה 10)
3. **האפליקציה נסגרת באמצע אימון או באמצע הקלטה** — כל חלק נשמר מיד; בפתיחה הבאה "המשך אימון"; קובץ הקלטה חלקי נמחק או נשמר תקין. (משימות 6, 7)
4. **RTL + Dynamic Type גדול** — שם עברי ארוך ("הישענות יד אחד 2 רגליים על רצפה") עם מספרים ואנגלית מעורבבים לא נחתך ולא מתהפך. (משימה 7, break-ui)
6. **אקטיבציות כשהאפליקציה סגורה ימים** — "דילגתי" מההתראה נאסף, ההצעות מחושבות מחדש בפתיחה, אין אקטיבציה ליד אימון. (משימות 10, 10ב)
5. **עשרות אייקונים חיים בעץ** — רק הגלויים מונפשים, ≤ 10 במקביל, 60fps. (משימה 4 + בדיקה על מכשיר)

---

## 13. שלב 2 — מה מחכה (מתוכנן, לא נבנה)
| יכולת | למה מחכה | הממשק שכבר קיים ב-v1 |
|---|---|---|
| זיהוי תנוחה אוטומטי | **needs dev build** | `PoseDetector` |
| גיבוי iCloud | **needs dev build** | `backup.ts` (אותו פורמט) |
| מאמן AI "Shawn Sama" | צריך שרת + API | `coach_messages`, `CoachContextV1`, מסך placeholder |
| בחירת אקטיבציות ע״י AI | צריך שרת | `suggest(...)` — אותו ממשק, החוקים נשארים כמעקה |
| המאמן מתאים את הימים הבאים | צריך שרת | `day_plans` (`origin: 'coach'`), `plan_adjustments`, `CoachPlanV1` |
| ייבוא אינסטגרם אוטומטי (בלי export ידני) | אין API רשמי לתוכן אישי | `ig-export.ts` |
| פענוח PDF אוטומטי | צריך שרת | `validateProgram` + מסך הסקירה |
| 120fps, ווידג׳ט, מצב כהה | dev build / היקף | טוקנים סמנטיים |

---

## 14. משימות v1 (Expo Go)

> כל משימה: TDD לפי superpowers:test-driven-development (בדיקה נכשלת → מימוש → עוברת), verification-before-completion לפני "סיום", commit בסוף. משימות UI נבדקות גם על ה-iPhone ב-Expo Go.

### משימה 1: תשתית — tooling, theme, i18n, בדיקות
**קבצים:** `package.json`, `app.json`, `jest.config.js`, `src/theme/{tokens,motion}.ts`, `src/i18n/*`, `src/i18n/__tests__/keys.test.ts`
**מייצר:** `useT()`, `useDirection(): 1|-1`, `setLanguage(lang)`, `tokens`, `EASE_OUT`, `EASE_IN_OUT`, `SPRING_DEFAULT`, `SPRING_SNAP`, `SPRING_SHEET`.
- [ ] להסיר את דוגמאות ה-template; `npx expo install` לכל חבילות ה-Tech Stack; `jest-expo`, `sql.js`
- [ ] בדיקה: `Object.keys(he).sort()` שווה ל-`Object.keys(en).sort()` → נכשלת → מימוש → עוברת
- [ ] בדיקה: ברירת המחדל של `language` היא `'he'`
- [ ] lint + tsc + jest → commit `chore: tooling, tokens, i18n (he default)`

### משימה 2: DB — driver, migrations, קטלוג, תוכנית
**קבצים:** `src/db/{driver,expo-driver}.ts`, `migrations/001_init.ts`, `seed/{catalog,shawn-sama,athletes}.ts`, `src/domain/{types,program-schema}.ts`
**מייצר:** `interface SqlDriver { run; all<T>; get<T>; tx }`, `migrate(d)`, `seedIfEmpty(d)`, `validateProgram(json): Result<ProgramInput>`.
- [ ] בדיקה: `validateProgram(shawnSama)` מצליח; 2 ימים (A, B), בכל יום סקשן push וסקשן pull; פוש #1 = 3 חלקים ו-`kind === 'combo'`; פול #4 = `{target_kind:'reps_range', target_value:6, target_max:8, rest_seconds:40, hold_at_end_s:…}`; פוש #3 `grip === 'supinated'`; פוש #2 = `planche-press-wide` עם `target_kind:'max_hold_in_transition'`
- [ ] בדיקה: לכל וריאציה יש `discipline` ו-`force`; קיימות Bench Dips, Side Flip, Iron Cross, Bar Spin 360/540, Shrimp Flip; `training_rules` עם שורה ל-static ול-dynamic
- [ ] בדיקה: נדחה `sets: 0`, `grip: 'x'`, בלוק בלי חלקים
- [ ] בדיקה: `seedIfEmpty` פעמיים → אותה כמות שורות
- [ ] בדיקה: כל וריאציה עם `tier` בין 1 ל-10, ו-`benchmarks` ב-seed = בדיוק 2 שורות, שתיהן `is_estimate = 1`
- [ ] commit `feat(db): schema, catalog, Shawn Sama program`

### משימה 3: Repositories + לוגיקת דומיין
**קבצים:** `src/db/repos/*.ts`, `src/domain/{day-sequence,load,streak,progress-math}.ts`
**מייצר:** `nextDayPlan()` (יוצר `day_plans` מהתבנית לפי `sequence_json`), `editDayPlan(id, change)` (→ `plan_adjustments`), `loadUnits(partLog)`, `recentLoad(skillId, hours)`, `startSession(kind, ref)`, `logPart(input)`, `completeSet(...)`, `endSession(id, summary)`, `getOpenSession()`, `currentStage(variationId)`, `progressToNext(variationId): {ratio, remaining, unit}`, `computeStreak(dates, today)`, `progressRatio(cur, target)` (0..1 תמיד).
- [ ] בדיקה: רצף A → B → A לפי ה-`day_plan` האחרון שהסתיים; דילוג/החלפה לא שוברים את הרצף; ימי מנוחה נספרים
- [ ] בדיקה: עריכת `day_plan` לא משנה את התבנית ונרשמת ב-`plan_adjustments`; כל `part_log` מצביע ל-`plan_part` שלו
- [ ] בדיקה ⓡ3: סשן פתוח מוחזר עם כל ה-part_logs שלו
- [ ] בדיקה: `progressRatio(0,0) === 0`; `computeStreak` עם פער מתאפס
- [ ] commit `feat(domain): repos, day sequence, load, progress math`

### משימה 4: מערכת התנועה — שעון, שלד, אייקונים, רקע, מד
**קבצים:** `src/motion/clock.tsx`, `src/components/skeleton/*`, `src/components/ambient/*`, `src/components/ui/PressableScale.tsx`
**מייצר:** `<ClockProvider>`, `useAmbientActive(visible?)`, `POSES: Record<PoseKey, Pose>`, `interpolatePose(a,b,t)` (worklet), `<Figure pose category size breathing?>`, `<LightField category>`, `<BreathingLine progress category|'activation' pulse?>`, `<PressableScale>`.
- [ ] בדיקה: `interpolatePose(a, b, 0)` = a, `(…,1)` = b; כף היד (הציר) זהה בכל t
- [ ] בדיקה: לכל `PoseKey` ב-catalog יש תנוחה ב-`POSES`
- [ ] בדיקה: `useAmbientActive` → false כש-reduceMotion / לא בפוקוס / לא נראה
- [ ] מסך דמו זמני עם 30 האייקונים; על המכשיר: 60fps בגלילה, רק הגלויים זזים
- [ ] commit `feat(motion): shared clock, skeleton figures, light field, breathing line`

### משימה 5: כניסה + ניווט + הגדרות בסיס
**קבצים:** `src/app/_layout.tsx`, `src/app/(tabs)/_layout.tsx`, מסכי שורש ריקים, `src/entrance/Entrance.tsx`, `src/app/settings.tsx`, `assets/splash` (הדמות בפלאנץ׳)
- [ ] root: `GestureHandlerRootView`, `ClockProvider`, DB, i18n, `SplashScreen.preventAutoHideAsync` עד שה-DB מוכן
- [ ] כניסה לפי טבלה 2.6; דילוג בנגיעה; הפחתת תנועה = crossfade
- [ ] NativeTabs (4 טאבים, SF Symbols, בלי אנימציית מעבר); הגדרות ב-formSheet; החלפת שפה + טעינה מחדש (סשן פתוח נשמר קודם)
- [ ] על המכשיר בשתי השפות: הפריים הראשון של הכניסה זהה ל-splash; הפריסה מתהפכת, הדמויות לא
- [ ] commit `feat(nav): press entrance, native tabs, settings`

### משימה 6: מנוע הקומבו (לוגיקה טהורה)
**קבצים:** `src/domain/combo-machine.ts` + בדיקות
**מייצר:** `createCombo(block, toggles): ComboState`, `advance(state, event, nowMs): ComboState`, `phaseAt(state, nowMs): Phase` — אירועים: `start`, `partDone`, `reps(n)`, `skipRest`, `pause`, `resume`.
- [ ] בדיקה: בלוק פוש #1 → `countdown → part(0) → part(1) → part(2) → rest → part(0)…` × 6 → `done`
- [ ] בדיקה: holdTimer כבוי → החזקה מסתיימת רק ב-`partDone`; restTimer כבוי → אין `rest`
- [ ] בדיקה: תרגיל מעבר (פרס) — אירועים `toPlanche`/`toHandstand` → `planche_hold_max_s` = ההחזקה הארוכה ביותר בפלאנץ׳; `broke_planche` מסיים את הסט
- [ ] בדיקה ⓡ1: `phaseAt` אחרי קפיצה של 90 שנ׳ (אפליקציה ברקע) מחזיר את השלב הנכון והזמן שנותר
- [ ] בדיקה: `max_reps_in_window` (פול #3) מסתיים אחרי 60 שנ׳ בדיוק; `per_side` מכפיל חלקים L/R
- [ ] commit `feat(domain): combo state machine`

### משימה 7: מסך אימון חי + סיכום
**קבצים:** `src/app/session/[dayPlanId].tsx`, `src/app/session/[id]/summary.tsx`, `src/components/workout/*`, `src/services/{speech,sound,camera}.ts`
**צורך:** משימות 3, 4, 6.
- [ ] כותרת תיאור הקומבו; 4 מתגים נפרדים + מתג הפטיקה/צלילים; טבלה 2.7 כלשונה
- [ ] קול: `expo-speech` `he-IL` / `en-US`; צלילים `expo-audio`; `expo-keep-awake` בזמן אימון
- [ ] מצלמה: `CameraView.recordAsync` לכל סט → שמירה ל-`videos` עם הווריאציה והתאריך; ⓡ3 הקלטה שנקטעה
- [ ] כל חלק נשמר מיד; סיכום: הרגשה, שלב לכל וריאציה (→ `stage_events`), הערות, כל הסטים ניתנים לעריכה
- [ ] ⓡ4: break-ui על `PartStage` — שם עברי ארוך, font scale 2.0, ערכים קיצוניים (999 חזרות, 0 שנ׳)
- [ ] commit `feat(workout): live combo screen and post-workout log`

### משימה 8: מסך היום + מעקב התקדמות
**קבצים:** `src/app/(tabs)/today/index.tsx`, `src/app/(tabs)/progress/index.tsx`, `src/app/progress/log.tsx`, `src/components/progress/*`
- [ ] היום: "יום N" עם Push + Pull, סימון שינויים מהתבנית, החלפה בנגיעה, מד כפול, האקטיבציה הבאה, "המשך אימון"
- [ ] התקדמות: שני מסלולים, ציר זמן (FlashList, בלי stagger), גרף שיא; רישום מהיר
- [ ] בדיקה: `getDailyPulses(range, track)` מפריד אימונים מאקטיבציות; טווח ריק → מערך ריק
- [ ] commit `feat(progress): today screen and breathing tracker`

### משימה 9: עץ מיומנויות + וריאציה
**קבצים:** `src/app/(tabs)/skills/*`, `src/components/skills/*`
- [ ] עץ לפי Statics / Dynamics (מפוצל ל-Push / Pull / Core), עם פילטר הפוך (לפי כוח); מיומנויות → וריאציות; אייקון חי, שלב, tier, טבעת "לשלב הבא"
- [ ] עריכת tier (מסמן `is_user_edited`)
- [ ] בדיקה: `getPersonalBest(variationId)` = מקסימום מ-`part_logs` + `analyses`
- [ ] על המכשיר: גלילה בעץ המלא — 60fps, ≤ 10 אייקונים חיים
- [ ] commit `feat(skills): skill tree and variation detail`

### משימה 10: אקטיבציות — המנוע (לוגיקה טהורה)
**קבצים:** `src/domain/activation/{plan,suggest,respond,stats}.ts` + בדיקות
**מייצר:** `planWindow(settings, calendar, nowMs, rand): Fire[]`, `suggest(state, rules, firedAt, rand): Suggestion`, `respond(activation, action, input): ResponseResult`, `activationStats(history, today): Stats`.
- [ ] בדיקה ⓡ2: לעולם לא יותר מ-12 ממתינות; כולן בתוך שעות הפעילות; חלון 21:00–02:00 עובד; אף אחת בתוך `quietWindows`
- [ ] בדיקה: ג׳יטר בטווח `±jitterMin`; מרווח ≥ `minGapMin`; ≤ `maxPerDay`
- [ ] בדיקה: אין אקטיבציה `bufferBeforeMin` לפני אימון מתוכנן ו-`bufferAfterMin` אחרי `ended_at`; התחלת אימון → `cancelled`
- [ ] בדיקה: `suggest` לא מציע static שקיבל עבודה מקסימלית ב-48 השעות האחרונות; לא מציע dynamic אחרי אימון מלא / `needs_space`; יעד בתוך 60–75% מהשיא; `reason_json` תמיד מלא
- [ ] בדיקה: `respond('skipped')` בלי פתיחת אפליקציה נרשם; `respond('done', input)` יוצר `session(kind='activation')` + `part_logs` + `load_units`; לא נענתה תוך `expireMin` → `expired`
- [ ] בדיקה: `activationStats` — שני הרצפים, אחוז ביצוע (expired לא נספר), מפת שעות; היסטוריה ריקה → אפסים
- [ ] commit `feat(activation): smart scheduling, rule-based suggestions, stats`

### משימה 10ב: אקטיבציות — התראות, רישום 10 שניות, מסכים, תזכורות
**קבצים:** `src/services/notifications.ts`, `src/app/activation/{[id],stats}.tsx`, `src/app/settings/activation.tsx`
**צורך:** משימה 10.
- [ ] לאמת ב-docs של SDK 58 + ב-Expo Go: `setNotificationCategoryAsync` עם כפתורי "בוצע"/"דילגתי" (ואם אפשר — כפתור עם טקסט); fallback מתועד
- [ ] "דילגתי" בלי פתיחה; "בוצע" → deep link לגיליון; איסוף תגובות שהוחמצו בפתיחה (`getLastNotificationResponseAsync`)
- [ ] גיליון 10 שניות לפי 6.4: צ׳יפים, סטפרים ממולאים מראש, שמירה בנגיעה אחת
- [ ] מסך סטטיסטיקה (6.5) + מד נושם teal; כל הפרמטרים של 6.6 בהגדרות, שינוי → תזמון מחדש
- [ ] תזכורות אימון (`planReminders`) — שמשמשות גם כ"אימון מתוכנן" לחסימת אקטיבציות
- [ ] על המכשיר: יום מלא עם אקטיבציות אמיתיות; מדידת זמן מהתראה לשמירה ≤ 10 שנ׳
- [ ] commit `feat(activation): notifications, 10-second log, stats screen, reminders`

### משימה 11: ייבוא תוכנית (v1 ידני)
**קבצים:** `src/app/program/{import,review}.tsx`
- [ ] בחירת PDF → תצוגה + עורך מובנה ממולא מראש → `validateProgram` → שמירה
- [ ] בדיקה: שמירת תוכנית חדשה מסמנת את הקודמת `is_active = 0`; `nextDayPlan` עובר לתוכנית החדשה
- [ ] commit `feat(program): import and review`

### משימה 12: סרטונים, ניתוח והשוואה
**קבצים:** `src/domain/angles.ts`, `src/services/pose/*`, `src/app/(tabs)/videos/*`, `src/components/video/*`
**מייצר:** `angleDeg(a,b,c)`, `bodyLineDeg(shoulder, hip, ankle)`, `shoulderLeanDeg(wrist, shoulder)`, `percentOfWay(variationId, metrics): number`, `interface PoseDetector`, `ManualPoseDetector`.
- [ ] בדיקה: `angleDeg({0,0},{1,0},{2,0}) === 180`; קו אופקי → `bodyLineDeg === 0`
- [ ] בדיקה: `percentOfWay` — בשלב tuck עם 0 שנ׳ = אחוז השלב בלבד; בשלב full עם יעד מלא = 100; לעולם לא > 100
- [ ] ייבוא (`expo-image-picker`/מצלמה) → `TrimBar` → `JointMarker` (gestures 2.8) → מדדים → שמירה (גם כבנצ׳מרק לאתלט)
- [ ] השוואה צד-לצד מסונכרנת עם שכבות שלד
- [ ] ⓡ: סרטון פורטרט/HEVC ביחס נכון; סרטון ארוך לא קורס
- [ ] בנצ׳מרק אתלט: שדה `source_url` חובה, תווית "הערכה" בכל מקום שמוצג
- [ ] commit `feat(video): library, manual analysis, side-by-side`

### משימה 12ב: ייבוא Instagram export + הרמה שלי
**קבצים:** `src/domain/ig-export.ts`, `src/app/baseline/*`
**מייצר:** `parseIgExport(files): IgMedia[]` (posts / stories / archived / reels מתוך ה-JSON), `guessSkill(caption): variationId | null`, `buildBaseline(analyses): Baseline[]`.
- [ ] בדיקה (עם fixture JSON מזויף במבנה של אינסטגרם): מחזיר רק וידאו, עם תאריך, סוג וכיתוב; קובץ חסר/פגום → שגיאה ברורה, לא קריסה
- [ ] בדיקה: `guessSkill("tuck planche 12s")` → planche; כיתוב בעברית; בלי התאמה → null
- [ ] בדיקה: `buildBaseline` לוקח את השלב הגבוה ביותר עם ראיה, `is_estimate = 1`
- [ ] אף שדה סיסמה / התחברות בשום מקום (בדיקה: grep ל-`password` במסכים)
- [ ] בחירת תיקייה מחולצת או ZIP קטן (`fflate`) → תיוג → ניתוח → "הרמה שלי"
- [ ] commit `feat(baseline): instagram export import and starting level`

### משימה 13: גיבוי + מאמן Placeholder
**קבצים:** `src/domain/{backup,coach-context}.ts`, `src/app/coach.tsx`, הגדרות
**מייצר:** `exportBackup(d): BackupV1`, `importBackup(d, json): Result`, `buildCoachContext(d, {since}): CoachContextV1`.
- [ ] בדיקה: export → import ל-DB ריק → export שוב = זהה (round-trip)
- [ ] בדיקה: גיבוי עם `schema_version` עתידי נדחה בהודעה ברורה
- [ ] בדיקה: `buildCoachContext` כולל את רצף הימים — תוכנן-מול-בוצע לכל חלק, התאמות, עומס לפי דיסציפלינה, ואקטיבציות בנפרד
- [ ] בדיקה: `applyCoachPlan(CoachPlanV1)` (fixture) יוצר `day_plans` עם `origin:'coach'` במצב "ממתין לאישור" ולא נוגע בעבר
- [ ] מסך מאמן: הסבר + "ייצא נתונים למאמן"
- [ ] commit `feat(data): backup and coach context`

### משימה 14: סקירה סופית
- [ ] `improve-animations` על כל התנועה (במקום review-animations שלא מותקן)
- [ ] break-ui על מסכי היום, האימון והעץ
- [ ] סבב מלא על ה-iPhone: יום A שלם (Push + Pull) עם כל ארבעת המתגים, אקטיבציה אמיתית, החלפת שפה
- [ ] סקירת branch מלאה (subagent-driven) → commit

---

## 15. שאלות פתוחות ומה צריך ממך
1. **חלוקת ה-PDF לימים** — ההנחה שלי: יום A = פוש + פול, יום B = פלאנץ׳ יד אחד + פול, לסירוגין. נכון? וכמה ימי מנוחה ביניהם?
2. **Claude in Chrome לא מחובר.** כדי שאעריך את האתלטים:
   1. להתקין את התוסף מ-https://claude.ai/chrome ב-Chrome.
   2. להתחבר בתוסף לאותו חשבון claude.ai של Claude Code.
   3. להפעיל מחדש את Chrome (בהתקנה ראשונה).
   4. לוודא שאתה מחובר לאינסטגרם באותו Chrome (אני לא מקליד סיסמאות).
   5. להריץ `/chrome` ב-Claude Code כדי לחבר, ולאשר לתוסף גישה ל-instagram.com כשהוא מבקש.
   6. להגיד לי "Chrome מחובר" — ואז אעבור על 7 הפרופילים ואמלא את טבלה 8.4.
3. **ה-export של אינסטגרם** — לבקש אותו עכשיו (8.3) ולשים ב-`docs/source/instagram/` להערכה ראשונה? ואם כן — ב-`.gitignore` או לא (זה תוכן אישי)?
4. **ערכי ברירת המחדל של האקטיבציות** (טבלה 6.6) וכללי ההתאוששות (3.2) — מתאימים, או שיש לך מספרים משלך?
