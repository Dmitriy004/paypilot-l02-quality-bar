# Двадцять скарг із Slack · корпус до L02 і L03

Вхідний матеріал двох лабораторних. На **L02** скарги розкладають за шарами
(пошук / генерація / дія) і збирають із них робочий набір на 12–15 кейсів. На
**L03** ті самі скарги розкладають на три купи: придатні одразу, придатні
після добору контексту, непридатні.

Це повідомлення з каналу `#cx-escalations` за 8–15 вересня 2026 року, у тому
вигляді, як їх написала підтримка: без ID, з одруківками і з власними
здогадками авторів. Автор скарги — не оракул: його цифра — це те, що почув
клієнт або запам'ятав співробітник. Імена, рахунки й транзакції взяті з seed
стенду PayPilot, тож кожну операцію можна знайти в базі.

| ID | Дата | Хто | Повідомлення |
|---|---|---|---|
| C-01 | 11.09 | support L1 | CUS-0008 (Hugo) asked the bot what a SWIFT transfer costs. bot said the fee is 1.5%, no hesitation. tariff sheet says EUR 15 + 0.3%. he made the transfer based on that and now asks why the fee is different |
| C-02 | 12.09 | support L1 | customer on the public widget asked how many days she has to dispute a card payment. bot answered with a paragraph about her daily and monthly limits. nothing wrong in it, just not what she asked. no customer id, she wasn't logged in |
| C-03 | 15.09 | support L2 | bot told the TechMart guy he can still dispute the double charge from July. we filed it, got rejected: window already closed. he's furious and wants compensation. didn't save the tx id, sorry |
| C-04 | 10.09 | support L1 | Emma Rossi (CUS-0005) asked what spread she pays on EUR→USD. bot said 1.5%. she's tier 2, isn't tier 2 0.9%? |
| C-05 | 10.09 | support L1 | same customer, second chat: asked for a full breakdown of 6000 EUR → USD. bot listed the rate, the spread, everything, then "final amount: 6,500 USD". the lines above don't add up to that. she has a screenshot |
| C-06 | 09.09 | support L1 | CUS-0002 asked for the exact balance of his USD account ACC-1003. bot answered 8,900. that's his EUR account. he has two and the bot mixed them up |
| C-07 | 14.09 | ops | fx quote looked off again, customer says we charged more than the app showed. cant remember which account |
| C-08 | 13.09 | support L2 | question on the rule. Greta (CUS-0007) converted 2000 EUR → USD, the bot's breakdown charged spread only on the 1000 "above your free allowance". I thought the allowance is all-or-nothing? if the bot is right our pricing page is wrong |
| C-09 | 15.09 | CX lead | Jonas from the GBP business account asked how much of his monthly transfer limit is left before a big supplier payment. bot said about 95k. he's tier 3, monthly is 1M and he's only sent ~30k GBP this month. he almost split the payment into three for nothing |
| C-10 | 12.09 | compliance | bot told Farid Aliyev (CUS-0006) he can dispute TX-0601 (FurnitureLoft, never delivered). his account is under compliance hold, no new disputes until the review ends. now he thinks we're blocking him on purpose. also check the wording: the bot must not mention the review to him |
| C-11 | 13.09 | support L1 | bot told Iryna (CUS-0009) she has 120 days to dispute the PharmaPlus card payment she says she never made, someone used her card online. I always thought it's 60 days for any dispute?? someone check before she waits too long |
| C-12 | 14.09 | support L1 | CUS-0002 got charged twice by CloudServe on Sep 8. bot told him he has 60 days to dispute. our help page says disputes can be raised within 120 days. which one is it? one of them is wrong |
| C-13 | 11.09 | support L2 | asked the bot to pull the FX spread table from our docs for all tiers (customer wanted it in writing). table looks clean, but tier 3 free allowance says EUR 1,500. pricing page says 5,000. where does 1,500 come from? |
| C-14 | 12.09 | support L2 | customer wanted two things: the list of dispute reason codes AND the window for a duplicate charge. bot gave the list and stopped there. asked again, same thing, half an answer |
| C-15 | 09.09 | support L1 | 5 tickets since morning: mobile app freezes on the Cards screen after yesterday's update. chat works fine, it's the app itself |
| C-16 | 13.09 | support L1 | customer asked the bot if we have cashback on card payments like other banks do. bot said Verta doesn't offer cashback. customer says that's a terrible answer and wants it fixed |
| C-17 | 14.09 | CX lead | customer wrote in angry about being charged twice for the same order. bot replied with three paragraphs of dispute policy and "as per our terms". no acknowledgement, no next step. she closed the chat and called the hotline. facts were fine, it's the tone |
| C-18 | 12.09 | support L2 | long chat with Danylo (CUS-0004) about disputing TX-0401. at the start he said the amount was exactly 240.00 EUR. a few questions later (timelines, notifications) he asked the bot to remind him the exact amount, and it couldn't. he had to repeat everything |
| C-19 | 08.09 | support L1 | bot described a "Verta Premium Plus savings account" to CUS-0001: interest rate, minimum deposit, terms, all of it. we don't have such a product. she wants to open one |
| C-20 | 14.09 | CX lead | 1-star app store review: "your chatbot lies about fees". no name, no date, no screenshot. marketing asks if we're on it |
