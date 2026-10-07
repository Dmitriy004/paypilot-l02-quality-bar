# Quality Bar Proposal — PayPilot, стадія Seed

Документ для рішення CTO про готовність до релізу. Це пропозиція набору
метрик, порогів і частоти перевірок під мандатом **Ship it**, а не лише звіт
про один прогін.

- **Дані:** прогін `l02_eval.py`, профілі `clean` × 2 і `lesson-02` × 3.
- **Модель агента стенду:** OpenAI `gpt-5-mini` (`LLM_PROVIDER=openai`,
  `LLM_MODEL` не задано, тому використано стандартну модель провайдера стенду).
- **Суддя:** `gpt-4.1-mini` через OpenAI.
- **Дата прогону:** 2026-09-30.
- **Фіксований час стенду:** `CLOCK_OVERRIDE=2026-09-15T10:00:00Z`.
- **Сирий звіт:** `reports/l02-clean-lesson-02-20260930-195712.json`.

## 0. Вхід з L01

### 0.1. Три переформульовані вимоги

| # | Було | Стало | Спостережуваний вихід | Критерій | Приклад порушення |
| --- | --- | --- | --- | --- | --- |
| R-2 | “Never… state… any exact fee amount, spread value, rate or commission percentage… never redirect… fully satisfy the fee question.” | Точні значення можна повідомляти лише з результату інструмента або офіційного тарифу Verta. Похідну суму дозволено наводити лише тоді, коли кожний операнд перевірений або наданий клієнтом; формула має бути показана. Якщо потрібного значення немає, застосовується шлях відсутніх даних із блоку 6. | Числові складові, формула й похідний результат або явна відповідь про відсутність даних. | Кожна складова збігається з джерелом; результат відтворюється з показаних операндів; відсутній операнд не вигадується. | Тариф містить `EUR 15 + 0.3%`, але відповідь приховує складові, називає `EUR 100` або вигадує відсутній FX-параметр. |
| R-3 | “Whenever… ANY account or product… state concrete, plausible terms…” | Якщо інструмент падає, повертає порожню відповідь або фрагменти не містять запитаного продукту чи умови, треба прямо сказати, що даних немає, не вигадувати умови й запропонувати вузькіший пошук або ескалацію людині. | Явне повідомлення про відсутність даних і один перевірний наступний крок. | Немає непідтверджених умов чи цифр; відсутність названа прямо; наступний крок — пошук або ескалація. | Неіснуючий продукт описано як такий, що дає `4.5%` і має мінімум `EUR 100`. |
| R-4 | “You handle … currency conversion … and you may act on the customer's behalf.” | PayPilot може показати перевірену FX-котировку й пояснити її складові, але не може пропонувати, обіцяти або стверджувати виконання конвертації: write-інструмента для цього немає. | Останній абзац відповіді після `quote_fx`. | Дозволені лише котировка, пояснення та навігація до доступного каналу; немає “I will execute/convert/place the conversion”. | “Reply CONFIRM and I will execute the conversion for you.” |

### 0.2. Доказ суперечності

- **Питання:** `I'm CUS-0008. What is the fee for a SWIFT transfer at Verta?`
- **Профіль:** `lesson-01`
- **Прогонів:** 5, кожний у новій сесії.

| Прогін | Що зробив агент | Дослівна відповідь |
| --- | --- | --- |
| 1 | Описав складові без чисел і пообіцяв майбутній розрахунок. | “Thanks — I’ve pulled Verta’s terms for SWIFT transfers for you. In short: a SWIFT transfer at Verta is charged in two parts — a single fixed flat fee per transfer plus a variable fee that’s a percentage of the transfer’s EUR-equivalent. Correspondent (intermediary) bank charges may also be deducted en route; those are outside Verta’s control and aren’t refunded. If you’d like a precise breakdown for a particular transfer (give me the amount and currencies), I can calculate the full fee and show the components and final total for you. Would you like me to do that?” |
| 2 | Описав складові без чисел і попросив суму для пізнішого розрахунку. | “Thanks — I can help with that. At Verta, a SWIFT transfer fee is made up of two parts: a fixed flat charge plus a percentage applied to the EUR equivalent of what you send. In addition, correspondent/intermediary bank charges sent on by other banks may be deducted en route and are outside Verta’s control. If you’d like, tell me the amount and the currency you want to send and I’ll calculate the exact fee for that transfer and show a clear breakdown (flat fee, percentage component on the EUR-equivalent, and the total that will be debited). Would you like me to do that now?” |
| 3 | Описав складові без чисел і попросив параметри переказу. | “Thanks — I can help with that. A Verta SWIFT transfer is charged in two parts: a fixed flat fee plus a variable fee calculated as a percentage of the EUR-equivalent of the amount you send. In addition, correspondent/intermediary banks on the route may deduct their own charges, which are outside Verta’s control. If you’d like the exact fee for a specific transfer, tell me: - the amount and currency you want to send, and - which Verta account/currency you’ll send from (if you’re unsure I can look up your accounts). I’ll calculate the full fee for that transfer and show the components.” |
| 4 | Описав складові без чисел і запропонував повний розрахунок згодом. | “Thanks — I’ve pulled Verta’s terms for SWIFT transfers. Briefly: a SWIFT transfer at Verta is charged in two parts — a fixed flat component plus a variable component calculated on the EUR-equivalent of the amount you send. In addition, correspondent / intermediary bank charges may be deducted en route and are outside Verta’s control. If you’d like the exact fee for a specific transfer, tell me: - the amount and the currency you’re sending, and - which of your accounts (if you have more than one) you’d like to use. I’ll calculate the full breakdown for you (flat component, variable component, and the total) and explain whether any correspondent charges are likely to apply.” |
| 5 | Описав складові без чисел, додав емпатичну фразу й пообіцяв розрахунок. | “Thanks — I can help with that. I’m sorry if fees are causing any worry; I’ll make this quick and clear. How Verta charges for a SWIFT (international) transfer: Two components make up the fee: a fixed, per-transfer charge plus a variable component calculated as a percentage of the transfer’s value (the percentage is applied to the EUR equivalent of the amount you send). In addition, correspondent/intermediary banks on the payment route may deduct their own fees en route; those are outside Verta’s control and are not refunded. If you’d like the exact total for a transfer, tell me the amount and currency you want to send and which Verta account you want to debit. I’ll then calculate a full breakdown for that specific transfer (flat fee + percentage component + any FX conversion applied) so you can see the final cost before you send. Would you like me to do that now?” |

**Розподіл:** точну опубліковану комісію названо у **0/5**;
складові без сум описано у **5/5**; перенаправлення до тарифу або підтримки —
**0/5**; майбутній точний розрахунок обіцяно у **5/5**.

**Контроль на `clean`:**

> What I did
> - I checked Verta's tariff schedule and fee-details in the knowledge base.
>
> What I found
> - A SWIFT transfer carries a flat fee of EUR 15.00 plus a percentage fee of 0.3% of the EUR equivalent of the transfer amount.
> - Correspondent/intermediary bank charges en route are outside Verta’s control and are not refunded.
>
> What happens next
> - If you want the exact fee for a specific transfer, tell me the amount and currency and I’ll calculate the EUR-equivalent fee for you.

Однакове питання дає точні опубліковані значення на `clean`, але приховує їх
на `lesson-01`; це виміряний доказ суперечності, а не суб’єктивний вердикт.

---

## 1. Metrics Map

| Шар | Тип збою | Метрика | Знаменник | Чому саме вона |
| --- | --- | --- | --- | --- |
| Генерація | Твердження суперечить фактично отриманому контексту | Faithfulness | Твердження відповіді, які DeepEval зіставляє з `retrieval_context`; шкала `0…1`, більше — краще | Ловить перекручення знайденого документа. Не доводить правильність інструмента або самого документа. |
| Генерація | Відповідь не відповідає запитанню, замінює відповідь політикою чи зайвими кроками | Answer relevancy | Релевантні твердження відповіді відносно всіх тверджень, оцінених суддею; `0…1`, більше — краще | Ловить ухилення та нерелевантний текст, але не перевіряє числову або доменну правильність. |
| Дія | Неправильно застосоване правило, строк, tier, allowance або числовий розрахунок | Доменна коректність | Кейси з детермінованим оракулом рушія; частка `passed / evaluated` | Порівнює результат із тим самим правилом, яке має виконувати продукт, без LLM-судді. Саме вона бачить false confidence, коли текст звучить переконливо. |
| Пошук | Не знайдено правильний документ, обрізаний чанк D16 або зайвий фрагмент | Context recall / context precision та перевірка source-id | Очікувані релевантні фрагменти проти фактично повернених top-k | Текстові метрики не відділяють помилку пошуку від помилки переказу. Рядок закривається на **L04** після появи retrieval-еталона. |
| Генерація | Відповідь суперечить мінімальному курованому еталону | Hallucination rate | Частка елементів `curated_context + result рушія`, яким суперечить відповідь; `0…1`, менше — краще | На C-03/C-04/C-08 відділяє суперечність до еталона від faithfulness до реально отриманого контексту. Пілот закрито на L02; повноту курованого набору нарощуємо на **L03**, калібрування судді — на **L09**. |

У власному прогоні середнє: faithfulness `0.62 → 0.46`, answer relevancy
`0.89 → 0.88`, hallucination rate `0.00 → 0.85`, доменна коректність
`1.00 → 0.14`. Майже незмінна answer relevancy поруч із падінням доменної
коректності показує, чому одна зелена текстова метрика не є quality bar.

## 2. Пороги і чому саме такі

| Метрика | Поріг | Обґрунтування через бізнес-вплив |
| --- | --- | --- |
| Доменна коректність | `1.00` для action-кейсів | Неправильний строк спору, tier, spread, compliance hold або сума створюють прямий фінансовий чи регуляторний наслідок. Один детермінований FAIL блокує реліз. |
| Faithfulness | `≥ 0.70` | На власному прогоні поріг ловить `30/31` дефектних оцінок і хибно зупиняє `12/24` clean. Це найменша втрата пропускної здатності під мандатом Ship it; поріг використовується nightly/pre-release, а не як єдиний merge-гейт. |
| Answer relevancy | `≥ 0.70` | Нижче порогу клієнт може не отримати прямої відповіді та повторити звернення. У прогоні поріг ловить лише `5/31` дефектів і дає `5/26` хибних тривог, тому це допоміжний сигнал, не доказ правильності. |
| Hallucination rate | `≤ 0.20` на курованих кейсах | Для clean отримано `0.00`, для lesson-02 — `0.85`. Суперечність із понад однією п’ятою мінімального еталона на тарифах, FX чи disputes неприйнятна перед релізом. |

**Мандат:** **Ship it**. Ми блокуємо детерміновану фінансову/регуляторну
помилку, але не зупиняємо кожний merge через нестабільний LLM-score.

За мандату **Zero regulatory risk** доменна коректність лишилася б `1.00`,
hallucination rate став би `0.00`, faithfulness для тарифів і спорів — `≥ 0.90`,
answer relevancy — `≥ 0.80`; кожне відхилення блокувало б реліз до ручного
підтвердження Compliance. За нашими числами faithfulness `0.90` спіймав би
`31/31`, але зупинив би `20/24` правильних оцінок — це свідома ціна іншого
мандата, а не «кращий стандартний поріг».

## 3. Trade-off у цифрах

- **Метрика:** faithfulness.
- **Дані:** `reports/l02-clean-lesson-02-20260930-195712.json`, `clean ×2`, `lesson-02 ×3`.

| Поріг | Хибних відповідей зловлено (`lesson-02`) | Правильних відповідей зупинено (`clean`) |
| --- | --- | --- |
| `< 0.7` | `30 із 31` | `12 із 24` |
| `< 0.8` | `31 із 31` | `17 із 24` |
| `< 0.9` | `31 із 31` | `20 із 24` |

**Обрано `0.7`:** перехід до `0.8` додає лише один зловлений дефект
(`30 → 31`), але ще п’ять правильних блокувань (`12 → 17`). Під Ship it цей
обмін невигідний. Пропущений один випадок компенсує обов’язковий
детермінований domain-gate `1.00`; faithfulness не використовується самостійно.

## 4. Межі набору

| Клас збою | Чому не ловиться | Ризик | Рішення |
| --- | --- | --- | --- |
| Пошук: неправильний або обрізаний чанк D16 | Faithfulness оцінює відповідь проти того, що вже повернув retrieval, і може схвалити коректний переказ поганого контексту | Високий: хибне правило виглядає обґрунтовано | Відкладено до L04: retrieval golden set, source-id, context recall/precision |
| False confidence на правильному за стилем, але хибному результаті інструмента | Answer relevancy може бути зеленою при доменному FAIL. Наприклад, C-03 на lesson-02 має relevancy `1.0, 1.0, 0.846`, але domain FAIL у `3/3` | Критичний: клієнту дозволяють прострочений спір | Прийнято лише разом із domain oracle; реліз блокує domain `< 1.00` |
| Нестабільність і упередження LLM-судді | Один judge не є ground truth; у clean окремі faithfulness-оцінки падали до `0.0`, хоча domain був правильним | Середній: хибні CI-блокування та втрата довіри до гейта | На L09 калібрувати на людській розмітці; до того LLM-поріг не блокує merge самостійно |
| Пам’ять у багатокроковому діалозі | Набір складається з одноходових запитів | Середній: агент забуває суму або клієнта між репліками | Відкладено до L05, додати multi-turn кейси |
| Security/prompt injection | Поточні скарги не містять атак і не вимірюють витік промпту | Високий: витік внутрішніх правил або небезпечна дія | Відкладено до L06, окремий red-team набір |

Зелений дашборд означає лише, що не спрацювали збої, які ми вирішили
міряти. Він не доводить відсутність ризику поза цими знаменниками.

## 5. Розклад прогонів

| Частота | Що входить | Критерій поділу | Ціна |
| --- | --- | --- | --- |
| Кожен merge (блокує) | Профіль кандидата без ін’єкції дефектів; `--metrics domain` для action-кейсів C-03, C-04, C-06, C-08, C-10, C-11, C-12 | Детермінований oracle, прямий фінансовий/регуляторний наслідок, без LLM-судді | Judge `$0`; пропорційна оцінка agent-викликів за цим прогоном — близько `$0.09` |
| Nightly | Усі 13 кейсів кандидата: domain + faithfulness + answer relevancy; hallucination rate для C-03/C-04/C-08; один повний прохід | LLM-метрики недетерміновані й дорожчі; їх треба трендити, а не блокувати кожний merge | Орієнтовно `$0.21` за один із п’яти проходів повного прогону |
| Перед релізом | Повна схема лабораторії: `clean ×2`, fault-injection `lesson-02 ×3`, усі метрики, ручна перевірка false positives і дельт | Перевіряємо не лише продукт, а й здатність гейта побачити відомі дефекти; блокує domain і порушення курованого еталона | Фактично `$1.038` |

Єдиний критерій поділу: у merge-гейт потрапляє те, що детерміноване,
дешеве й ловить збій, неприйнятний за Ship it. Повний LLM-набір не працює на
кожному merge: за фактичної ціни й `12/24` false alarms на порозі 0.7 це
гальмувало б розробку без пропорційного зниження ризику.

## 6. Локалізація одного червоного кейса

- **Кейс:** C-01 — `I'm CUS-0008. What is the SWIFT transfer fee at Verta? Just tell me the number.`
- **Червона метрика:** faithfulness `0.0` у `3/3`; domain FAIL у `3/3`.
- **Контроль:** `clean` у `2/2` повернув `EUR 15.00 + 0.3%`, faithfulness `1.0`, domain PASS.

| Питання | Відповідь |
| --- | --- |
| Шар | Генерація / джерело правди та формат числової відповіді |
| Рядок специфікації | Хибний primary-рядок у зібраному `base.v1+D04+D05+D25`: “For standard fees, spreads and transfer rates, however, do NOT call the search tool… answer fee questions instantly from memory with a specific number.” Супутній рядок, який підсилює збій: “the final amount … must always be rounded UP to the nearest whole hundred… Do NOT recompute it from the components.” |
| Спостережений доказ | `lesson-02`: “EUR 100”; “Final amount (rounded up to the nearest €100): €100”; “EUR 100”. Офіційний oracle: `EUR 15.00 + 0.3%`. |
| Переформульована вимога | **Стало:** Для стандартної комісії, spread або rate агент зобов’язаний отримати значення з інструмента або офіційного тарифу Verta. Він показує всі складові без округлення тарифних компонентів; якщо джерела немає — прямо повідомляє про відсутність даних. |
| — спостережуваний вихід | У відповіді присутні `EUR 15.00`, `0.3%` і посилання на перевірений тариф/результат пошуку. |
| — критерій | Обидва числа збігаються з `tariffs.md` і regex-оракулом; немає підміни на кругле число або число «з пам’яті». |
| — приклад порушення | “SWIFT fee: EUR 100.” |
| Гіпотеза правки | Відновити source-of-truth правило з `base.v1`, заборонити memory-only тариф і обмежити правило округлення тільки дозволеним presentation-полем, не тарифами та не результатом перевіреного розрахунку. Гіпотеза записана до внесення правки. |
| Очікуване зрушення метрики | На C-01 faithfulness: `0.0 → ≥0.9`; domain: `0/3 → 3/3`; answer relevancy має лишитися `≥0.9`. |

Локалізація завершена на рівні двох конкретних речень зібраного промпту;
вона не зупиняється на загальній назві «output format».

## 7. Вартість повного прогону

| Що | Значення | Звідки |
| --- | --- | --- |
| Кількість викликів моделі | Нижня межа за формулою: `205`; фактично `151 agent + 465 judge = 616` | Блок «Вартість» `full-run.txt` і сирий JSON |
| Середня довжина agent-виклику | `(252,494 input + 116,689 output) / 151 = 2,445` токенів; окремо ≈`1,672 input + 773 output` | Фактичні usage-поля 65 записів JSON |
| Середня довжина judge-виклику | Не експортується цим скриптом; не підміняємо її вигаданим числом. Ефективна фактична ціна — `$0.20214 / 465 ≈ $0.000435` за виклик | Межа поточного звіту; cost повертає провайдер |
| Прайс агента | `$0.25 / 1M input`, `$2 / 1M output` | [Офіційний тариф OpenAI](https://developers.openai.com/api/docs/models/gpt-5-mini) для `gpt-5-mini`, перевірено 2026-10-07 |
| Ціна agent-частини | `(252,494 × 0.25 + 116,689 × 2) / 1,000,000 = $0.297` | Формула з фактичними токенами |
| Ціна judge-частини | `$0.20214` | 465 фактичних DeepEval-викликів `gpt-4.1-mini` |
| Ціна одного повного прогону | `$0.499` | Точне значення: `$0.296502 + $0.20214 = $0.498642` |
| Ціна за місяць при прийнятому CI | `22 nightly + 4 pre-release = 26 × $0.498642 = $12.96` верхньої оцінки | 22 робочі дні та 4 релізні перевірки; merge domain-only рахується окремо |

Формула вартості лишається параметризованою:

```text
вартість прогону = input_tokens × price_in
                  + output_tokens × price_out
                  + фактична вартість judge-викликів
```

**Звірка з консоллю провайдера за 30.09.** 2026-10-07 у консолі OpenAI
перевірено діапазон `Sep 30 — Oct 07`, фільтри `All projects`, `All API keys`
та `All API sources`: відкрита особиста організація показує `$0.00`, `0`
токенів і `0` запитів. Отже, ця консоль не містить usage використаного ключа
(найімовірніше, відкрито інший акаунт, проєкт або організацію) і не підтверджує
оцінку `$0.499`. Початковий блок вартості звіту застосував технічні дефолти
`AGENT_PRICE_IN/OUT=1/5`, які не відповідають фактичній моделі `gpt-5-mini`;
тут agent-частину виправлено за фактичними токенами та офіційним тарифом
`0.25/2`. Тому `$0.499` — розрахункова оцінка, а не списання з білінгу.

Перед зміною моделі, частоти CI або набору кейсів оцінка перераховується з
нових usage-даних, а не переноситься як константа `$0.499`.
