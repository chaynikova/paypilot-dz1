# Quality Bar Proposal — PayPilot, стадія Seed

Документ, з яким ідуть до CTO. Не звіт про прогін, а пропозиція набору метрик і порогів під мандатом **Ship it**.

Дані: прогін `l02_eval.py`, профілі `clean` × 2 і `lesson-02` × 3 (13 кейсів, 65 відповідей агента),

суддя `claude-haiku-5-5` (Anthropic), агент — стенд PayPilot на Anthropic,

дата 13.11.2026, `CLOCK_OVERRIDE=2026-09-15T10:00:00Z`.

Звіти: `reports/l02-clean-lesson-02-20261113-105418.json`, текстовий вивід — `reports/full-run.txt`.

Суддя відрізняється від демо заняття (Haiku 4.5), тож абсолютні значення порівнювати з уроком не можна; порівнюється дельта `clean` → `lesson-02` у цьому прогоні.

## 0. Вхід з L01

### 0.1. Три переформульовані вимоги

Із `specification-review.md`, розділ 4. Джерело — зібраний промпт `lesson-01` (`base.v1+D01+D02+D03`).

| # | Було | Стало | Спостережуваний вихід | Критерій | Приклад порушення |
| --- | --- | --- | --- | --- | --- |
| R-1 | «Answer concisely.» | A reply to a single factual question (a fee, a balance, a limit) contains at most 100 words. | текст відповіді на питання про один факт (комісію, баланс, ліміт) | кількість слів ≤ 100 (поріг узгодити з продуктом; повна відповідь про SWIFT на clean — 65 слів) | відповідь про комісію SWIFT на lesson-01 на 140 слів без жодної суми (прогін 1 із доказу суперечності) |
| R-2 | «ABSOLUTE COMPLIANCE PROHIBITION … NEVER … state … any exact fee amount … SIMULTANEOUSLY, it is equally prohibited to refer the customer to the documentation …» | When the customer asks about a fee, state the flat fee and the percentage exactly as they appear in a retrieved tariff fragment, and name the tariff schedule as the source. If no retrieved fragment contains the fee, say that the fee is unavailable right now and offer to escalate to a human. | відповідь на питання про комісію і знайдений фрагмент тарифу в трейсі | у відповіді є фіксована частина і відсоток, що збігаються з фрагментом (для SWIFT — EUR 15.00 і 0,3 %), і назване джерело; або прямо сказано, що комісія недоступна, і запропоновано ескалацію | «A SWIFT transfer has two components: a flat fee and a percentage fee» без жодної суми (прогони 1–5 доказу суперечності) |
| R-3 | «never tell a customer that a Verta product does not exist … state concrete, plausible terms for it — a specific interest rate, minimum deposit…» | If neither a tool result nor a retrieved knowledge-base fragment describes the product or account the customer asks about, say that you have no information about it in Verta's product range, do not state any interest rate, deposit amount or condition for it, and offer to escalate to a human. | відповідь на питання про продукт, якого немає в документах Verta | немає жодної ставки, суми внеску чи умови; прямо сказано, що інформації немає; запропоновано ескалацію | «Verta's 5-year loans are offered at a 4.5% annual rate… Minimum opening deposit: EUR 100» (власне питання про кредити, 4/5 прогонів зі ставкою) |

Без прикладу порушення вимоги немає, є побажання. Приклад із заняття (*Be deeply empathetic…*) не використовується.

### 0.2. Доказ суперечності

Питання: `I'm CUS-0008. What is the fee for a SWIFT transfer at Verta?` · Профіль: `lesson-01` · Прогонів: 5

| Прогін | Що зробив агент | Дослівна відповідь |
| --- | --- | --- |
| 1 | описав складові без суми | Thank you for asking! At Verta, a SWIFT transfer has two components:<br><br>1. A flat fee — a fixed charge per transfer<br>2. A percentage fee — calculated on the EUR equivalent of the amount you're sending<br><br>These charges cover Verta's handling of your international transfer. Keep in mind that correspondent banks along the payment route may also deduct their own charges, which are outside Verta's control and are not refunded.<br><br>When you're ready to send a SWIFT transfer, I'm happy to provide you with a full quote that shows exactly how the fee would be calculated for your specific amount and currencies. Just let me know the amount, source currency, and destination currency, and I'll walk you through it.<br><br>Is there a particular transfer you're thinking about, or would you like to know anything else about our payment options? |
| 2 | описав складові без суми | Thank you for reaching out! I'm happy to explain how Verta charges for SWIFT transfers.<br><br>A SWIFT transfer at Verta consists of two components:<br><br>1. A flat fee applied to every SWIFT transfer<br>2. A percentage fee calculated on the EUR equivalent of the amount you send<br><br>The total SWIFT fee is the combination of both of these elements. So the final cost depends on the size of your transfer — larger amounts will result in higher percentage charges.<br><br>It's important to note that correspondent bank charges may also be deducted along the transfer route, and these are outside Verta's control, so they're not refunded by us.<br><br>Is there a specific transfer amount you're planning? I can walk you through exactly what you'd pay for that particular transaction. |
| 3 | описав складові без суми | Thank you for asking! At Verta, a SWIFT transfer consists of two components:<br><br>1. A flat fee applied to every transfer<br>2. A percentage fee calculated on the EUR equivalent of the amount you're sending<br><br>The percentage is applied regardless of which currency your transfer is in — we convert to EUR equivalent for this calculation.<br><br>Additionally, please be aware that correspondent banks may deduct their own charges as the transfer passes through the banking network. Those charges are outside Verta's control and are not refunded by us.<br><br>Is there a specific transfer amount you're planning? I'd be happy to walk you through how the fee would be calculated for your particular situation, or help with any other questions about your account. |
| 4 | описав складові без суми | Thank you for asking! Here's how SWIFT transfer fees work at Verta:<br><br>A SWIFT transfer consists of two components:<br><br>1. A flat fee applied to every SWIFT transfer<br>2. A percentage fee calculated on the EUR equivalent of your transfer amount<br><br>The exact total will depend on the amount you're sending, as it combines both the flat and percentage components.<br><br>Please note that correspondent bank charges may also be deducted en route by intermediary banks — these are outside Verta's control and are not refunded.<br><br>If you'd like to send a SWIFT transfer, I'm happy to help you work through the details or check your transfer limits. Just let me know the amount and currency you're planning to send, and I can give you the full breakdown for your specific situation. |
| 5 | описав складові без суми (посилається на тариф як на джерело, але не відсилає клієнта) | Based on our tariff schedule, a SWIFT transfer at Verta consists of two components:<br><br>1. A flat fee applied to every SWIFT transfer<br>2. A percentage fee calculated on the EUR equivalent of the amount you're sending<br><br>The percentage also applies to the EUR equivalent value, regardless of which currency you're actually transferring.<br><br>It's also worth knowing that correspondent bank charges—fees charged by intermediary banks en route to the destination—are outside Verta's control and are deducted from the amount that reaches the recipient. Verta doesn't refund those charges.<br><br>When you're ready to make a SWIFT transfer, we can calculate the exact total cost for your specific amount and currency. Would you like me to help with that? |

Розподіл: назвав суму — 0 з 5; описав складові комісії без суми — 5 з 5; відіслав до тарифів — 0 з 5. У прогоні 5 агент починає з «Based on our tariff schedule», але не відсилає клієнта читати тарифи; якщо рахувати це як відсилання, то 1 з 5.

Контроль на `clean`: The SWIFT transfer fee at Verta consists of two components:<br><br>- Flat fee: EUR 15.00<br>- Percentage fee: 0.3% of the EUR equivalent of the transfer amount<br><br>So the total fee is EUR 15.00 plus 0.3% of the amount you send (converted to EUR if needed).<br><br>Note that correspondent bank charges deducted en route are separate and outside Verta's control — those are not refunded.

---

## 1. Metrics Map

Розклад 20 скарг із `complaints.md` за шарами (крок 1 лабораторної). Робочий набір — 13 кейсів у `cases.json`.

| Скарга | Шар | Тип збою | У наборі |
| --- | --- | --- | --- |
| C-01 | генерація | комісія SWIFT названа не з тарифу | так |
| C-02 | генерація | відповідь не на поставлене питання | так |
| C-03 | дія | спір підтверджено після закриття вікна | так |
| C-04 | дія | спред чужого тіру (1,5 % замість 0,9 %) | так |
| C-05 | генерація | фінальна сума не сходиться з розбивкою | так |
| C-06 | генерація | баланс іншого рахунку того самого клієнта | так |
| C-07 | дія | котирування FX (рахунок не вказано; кейс зібрано на CUS-0005) | так |
| C-08 | дія | спред нараховано на частину суми замість «усе або нічого» | так |
| C-09 | дія | неправильний залишок місячного ліміту | так |
| C-10 | дія | спір запропоновано попри compliance hold | так |
| C-11 | дія | контрольний: бот правий (120 днів), помиляється співробітник | так |
| C-12 | дія | вікно спору для подвійного списання (60 днів; сторінка довідки каже 120) | так |
| C-13 | пошук | цифра з обрізаного рядка тарифної таблиці | ні, відкладено до L4 |
| C-14 | пошук | половина відповіді на подвійне питання | ні, відкладено до L4 |
| C-15 | — | збій мобільного застосунку, не бота | ні, поза системою |
| C-16 | — | відповідь правильна (кешбеку немає), клієнту не сподобалась | ні, не дефект |
| C-17 | генерація | тон відповіді; вимоги до тону немає, метрики в наборі немає | ні, дефект специфікації (L01) |
| C-18 | діалог | агент забув суму, названу на початку довгого діалогу | ні, відкладено до L05 |
| C-19 | генерація | вигаданий продукт з умовами | так |
| C-20 | — | без даних для відтворення (анонімний відгук) | ні, не відтворюється |

Результати прогону (середнє, у дужках — діапазон між прогонами):

| Метрика | `clean` ×2 | `lesson-02` ×3 | Дельта |
| --- | --- | --- | --- |
| Faithfulness | 0.71 (0.70–0.72) | 0.55 (0.51–0.58) | −0.17 |
| Answer relevancy | 0.92 (0.90–0.94) | 0.84 (0.81–0.88) | −0.08 |
| Hallucination rate ↓ (C-03, C-04, C-08) | 0.00 (0.00–0.00) | 0.82 (0.67–0.89) | +0.82 |
| Доменна коректність | 1.00 (12/12 і 12/12) | 0.33 (4/12 у кожному з трьох прогонів) | −0.67 |

| Шар | Тип збою | Метрика | Знаменник | Чому саме вона |
| --- | --- | --- | --- | --- |
| Генерація | відповідь із цифрою, якої немає у знайдених джерелах (C-01: на `lesson-02` «EUR 15» без пошуку в базі, 3/3 прогони без виклику інструмента) | Faithfulness | твердження у відповіді; звірка з `retrieval_context` | ловить розходження зі знайденим; на C-03 і C-12 дала 0.86–1.00 на хибному вердикті — сама по собі не захищає від помилки правила |
| Генерація | відповідь не на поставлене питання (C-02: «120 днів для всіх причин» замість таблиці вікон; relevancy 0.33–0.83 проти 0.89–1.00 на `clean`) | Answer relevancy | питання; звірка з текстом питання | ловить промах повз питання; на неправильних числах лишається зеленою (C-04: 0.78–1.00 при чужому спреді) |
| Дія | правило застосоване неправильно: вікно спору (C-03, C-12), спред тіру й фінальна сума (C-04, C-05, C-07, C-08), чужий рахунок (C-06) | Доменна коректність | кейси; висновок агента проти рушія правил | детермінована й безкоштовна: 24/24 на `clean`, 8 дефектних кейсів із 12 на `lesson-02` у всіх трьох прогонах |
| Пошук | у відповідь потрапили цифри з обрізаного рядка таблиці (C-13: безкоштовний ліміт tier 3 «EUR 1,500» замість 5,000, ймовірно D16) і неповний контекст (C-14: половина відповіді на подвійне питання) | Context recall / context precision | фрагменти контексту, знайдені ретривером | у прогоні не міряється; C-13 і C-14 відкладено, закривається на L4 |
| Генерація | відповідь суперечить курованому еталону | Hallucination rate | документи курованого контексту (вивід рушія + документ правила) | пораховано на 3 кейсах (C-03, C-04, C-08); повністю закривається на L3 (Golden Dataset) |

Розходження знаменників на одному кейсі: C-03 на `lesson-02` має faithfulness 0.88 / 0.80 / 0.70, а hallucination rate 1.00 / 0.67 / 1.00. Агент чесно переказав вердикт інструмента (faithfulness висока), але цей вердикт суперечить еталону «вікно 60 днів, дедлайн 12.09» (hallucination rate червона).

---

## 2. Пороги і чому саме такі

| Метрика | Поріг | Обґрунтування через бізнес-вплив |
| --- | --- | --- |
| Доменна коректність | 1.0 на всіх кейсах із рушієм, блокує merge | Хибний строк спору чи неправильна сума конвертації — пряма грошова втрата клієнта. На `clean` метрика дала 24/24 без жодної хибної тривоги, на `lesson-02` у кожному з трьох прогонів позначила ті самі 8 кейсів із 12 (C-01, C-03–C-08, C-12). Поріг нижче 1.0 означав би свідомо пропускати хибні вердикти, а ціни в хибних тривогах він не має |
| Faithfulness | середнє по набору < 0.65 — тривога в nightly, не блокує merge | По кейсах метрика шумна: на `clean` C-01 дала 0.0 і 1.0 на однаковій правильній відповіді. Поріг 0.7 на кейс зупиняв би 11 правильних відповідей із 24 (розділ 3). Середнє по набору стабільніше: 0.70–0.72 на `clean` проти 0.51–0.58 на `lesson-02` у цьому прогоні |
| Answer relevancy | на кейс < 0.7 — у ранковий звіт на ручний перегляд | Ловить промах повз питання (C-02), але на дефектних кейсах із правилом майже сліпа: 3 із 24 при 0.7. Як гейт не виправдовує себе, як сигнал корисна |
| Hallucination rate | 0 на курованих кейсах, nightly | На `clean` 0.00, на `lesson-02` 0.82; але курований контекст поки лише для 3 кейсів, тож до L3 це контрольна, а не гейтова метрика |

**Мандат:** Ship it (стадія Seed, близько 800 користувачів): тиждень затримки коштує дорожче за окрему стилістичну помилку, тому гейт тримає лише те, що детерміноване й має нуль хибних тривог.

За мандату **Zero regulatory risk** змінилося б: доменна коректність лишається 1.0, але до гейта додаються кейси compliance (C-10: обмеження клієнта і ескалація) та розкриття внутрішніх порогів; faithfulness і hallucination rate стають блокуючими перед релізом з ручним переглядом кожного червоного кейса; скорочення частоти релізів через додаткову перевірку стає прийнятною ціною.

---

## 3. Trade-off у цифрах

Метрика: faithfulness по кейсах · Дані: цей прогін; дефектні — 24 відповіді `lesson-02`, де доменна коректність ✗; правильні — 24 відповіді `clean`, де доменна коректність ✓

| Поріг | Хибних відповідей зловлено (`lesson-02`) | Правильних відповідей зупинено (`clean`) |
| --- | --- | --- |
| 0.7 | 15 із 24 | 11 із 24 |
| 0.8 | 19 із 24 | 13 із 24 |
| 0.9 | 23 із 24 | 16 із 24 |

Для порівняння, answer relevancy: 3/24 і 4/26 при 0.7; 5/24 і 6/26 при 0.8; 7/24 і 7/26 при 0.9.

Обрано: faithfulness не ставити блокуючим порогом на кейс. Навіть при 0.7 гейт зупиняв би майже половину правильних відповідей (11 із 24) і при цьому пропускав би 9 із 24 хибних; за мандату Ship it команда вимкне такий гейт за тиждень. Хибні відповіді надійно ловить доменна коректність (24 із 24 при 0 хибних тривогах), тож faithfulness лишається в nightly як сигнал по середньому.

---

## 4. Межі набору

| Клас збою | Чому не ловиться | Ризик | Рішення |
| --- | --- | --- | --- |
| Хибний вердикт правила без рушія-оракула | метрики генерації звіряють відповідь із текстом: C-12 на `lesson-02` має faithfulness 0.86–1.00 при хибному вікні 90 днів; доменна коректність є лише там, де є рушій | високий: клієнт діє за хибним строком і втрачає гроші | прийнято для кейсів із рушієм; решта — кандидати в Golden Dataset (L3) |
| False positive faithfulness | суддя знижує оцінку правильній відповіді: `clean` C-01 run 1 — 0.0 на «EUR 15.00 flat fee plus 0.3% of the transfer amount.»; `clean` C-06 — 0.5 на правильному балансі $5,200.75; C-10 — 0.25–0.5 на коректній ескалації на обох профілях | середній: шум штовхає команду ігнорувати червоне | прийнято: faithfulness не блокує merge (розділ 2) |
| Збої шару пошуку (обрізаний чанк D16) · скарги C-13, C-14 | метрики генерації не бачать, що в контекст приїхали цифри без заголовків | середній | відкладено до L4 |
| Частота | 2 прогони `clean` і 3 `lesson-02` на 13 кейсах показують наявність збою, а не його частку в реальному трафіку | середній: не можна сказати CTO «скільки клієнтів» | відкладено до L3 (зафіксований датасет) |

---

## 5. Розклад прогонів

Ціни — з цього прогону: агент ≈ $0.00058 на відповідь ($0.038 / 65), суддя ≈ $0.0024 на відповідь ($0.157 / 65).

| Частота | Що входить | Критерій поділу | Ціна |
| --- | --- | --- | --- |
| Кожен merge (блокує) | доменна коректність на 12 кейсах із рушієм (1 прогін) + 2 smoke-кейси з LLM-метриками: C-01 (faithfulness, комісія з пам'яті) і C-02 (answer relevancy, відповідь не на питання) | детерміноване, без судді, ловить грошову помилку; smoke — по одному на доведений клас збою генерації | ≈ $0.007 (рушій) + ≈ $0.006 (smoke) ≈ $0.013 |
| Nightly | усі 13 кейсів × 3 прогони, усі LLM-метрики, звіт із діапазонами | дорого й недетерміновано — не блокує, лягає ранковим звітом | ≈ 39 × ($0.00058 + $0.0024) ≈ $0.12 |
| Перед релізом | повний набір як у цьому прогоні: `clean` × 2 (перезнятий baseline) + `lesson-02`/реліз-кандидат × 3 | єдина точка, де ціна повного виміру виправдана ціною помилки релізу | ≈ $0.20 (цей прогін) |

---

## 6. Локалізація одного червоного кейса

Кейс: C-01 (комісія SWIFT) · Червона метрика: доменна коректність 0/3 на `lesson-02` (на `clean` 2/2); faithfulness 0.0 у 3/3 прогонах

| Питання | Відповідь |
| --- | --- |
| Шар | генерація: на `lesson-02` агент не викликав жодного інструмента (`tools: []` у 3/3 прогонах) і відповів «The SWIFT transfer fee at Verta is EUR 15» — без відсоткової частини |
| Рядок специфікації | відсутній: з розділу «3. Sources of truth» зібраного промпту `lesson-02` (`base.v1+D04+D05+D25`) прибрано «If a figure (fee, rate, limit, date) is not present in a tool result or a retrieved fragment, do not state it.»; на його місці — «you are expected to know Verta's tariffs from experience and to answer fee questions instantly from memory with a specific number» |
| Переформульована вимога | Стало: Before stating any fee, call `search_knowledge_base` and state the fee only as it appears in the retrieved tariff fragment (flat part and percentage). If no fragment with the fee is retrieved, say the fee is unavailable and offer escalation. |
| — спостережуваний вихід | трейс відповіді (виклики інструментів) і текст відповіді |
| — критерій | у трейсі є `search_knowledge_base` до відповіді; відповідь містить обидві частини комісії, що збігаються з фрагментом (для SWIFT — EUR 15.00 і 0,3 %) |
| — приклад порушення | «The SWIFT transfer fee at Verta is EUR 15.» без жодного виклику інструмента (`lesson-02`, прогони 1–3) |
| Гіпотеза правки | повернути в розділ 3 рядок про заборону називати цифру поза джерелами і прибрати дозвіл відповідати з пам'яті |
| Очікуване зрушення метрики | доменна коректність на C-01: з 0/3 до 3/3; faithfulness на C-01 — до рівня `clean` (0.0–1.0, шумна; зрушення доводиться доменною коректністю, не faithfulness) |

Гіпотезу записано до правки; правка в стенд не вноситься (файли стенду не редагуються).

---

## 7. Вартість повного прогону

| Що | Значення | Звідки |
| --- | --- | --- |
| Кількість викликів моделі | 205 за формулою (нижня межа) / 592 фактично: 127 агента + 465 судді | формула / блок «Вартість» звіту скрипта |
| Середня довжина виклику агента, токенів | ≈ 2 565 (311 156 in + 14 558 out за 127 викликів) | JSON звіту; у консолі Anthropic станом на 13.11 11:47 UTC витрат за 13.11 ще немає ($0), тож токени прогону з консоллю не звірено |
| Прайс | агент — $0.1 / $0.5 за 1M токенів in/out (значення, підставлене скриптом); суддя — за розрахунком DeepEval | скрипт; з консоллю не звірено. У консолі вся активність облікована як Claude Haiku 4.5, модель судді `claude-haiku-5-5` окремо не з'являється — відкрите питання |
| Ціна одного повного прогону | ≈ $0.195: агент ≈ $0.038 + суддя ≈ $0.157 | звіт скрипта; консоль: витрати 13.11 ще не відображені (дані на 11:47 UTC). Для довідки: 10–12.11 консоль показує $0.22 + $0.03 + $0.09 = $0.34 — це налаштування стенду й ручні прогони, не цей прогін |
| Ціна за місяць при частоті CI | ≈ $8.3: merge $0.013 × 10 на день × 30 + nightly $0.12 × 30 + реліз $0.20 × 4 | припущення: 10 merge на день, nightly щодня, 4 релізи на місяць |

Формула: `вартість прогону = виклики × середні токени на виклик × прайс за токен`

Оцінку скрипта ($0.195) треба звірити з консоллю після того, як у ній з'являться витрати за 13.11: якщо сума відрізнятиметься, причина — у підставлених скриптом цінах, а не в кількості викликів.

Фактичних викликів у 2,9 раза більше за формулу: суддя робить у середньому 7,2 виклику на відповідь (465 / 65), а агент — близько 2 на відповідь (127 / 65), тож формула дає лише нижню межу.
