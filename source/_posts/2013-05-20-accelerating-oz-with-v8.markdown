---
layout: post
title: "Пересказ видео «Accelerating Oz with V8»"
date: 2013-05-20 18:36
comments: true
categories:
- js
- v8
---

Посмотрел давеча познавательное видео: [Accelerating Oz with V8](https://developers.google.com/events/io/sessions/324908972).
Это доклад на Google I/O 2013. Речь там идет о некоем веб-приложении (на самом деле это 3d игра/демка),
в котором обнаружились проблемы с производительностью и которое докладчики починили.

Видео идет 38 минут, и большая часть там ни о чем.
Поэтому сделаю краткую выжимку, чтобы не забыть:

  * Во-первых, если v8 по каким-то причинам не оптимизировала функцию,
    в которой есть много арифметических вычислений, то все эти арифметические операции
    создают новые временные объекты, которые потом должны быть прибраны GC.

    {: .language-javascript }
        var a = p * q;
        var b = x + y;
        var c = t / s;
        point.x = a * b * c;
        ...

    в этом коде будет создано 5 лишних временных объектов (каждый бинарный оператор).

  * Во-вторых, обычный цикл `for-in` деоптимизирует функцию целиком
    (как какой-нибудь `eval` или блок `try-catch`).
    Пример со слайдов:

    {: .language-javascript }
        function updateSprites(dt) {
            for (var sprite in sprites) {
                sprite.position.x += sprite.velocity.x * dt;
                //  many more lines of arithmetic.
                //  ...
            }
        }

    Внутри цикла много арифметических вычислений, из-за цикла вся функция деоптимизирована.
    Как следствие — создается очень много временных объектов и GC тратит много времени на их удаление.
    Что не очень хорошо сказывается на производительности приложения.

    Решение: вынести вычисления (все, что внутри `for-in`) в отдельную функцию, что позволит v8
    ее оптимизировать и не создавать нагрузку на GC.

    {: .language-javascript }
        function updateSprite(sprite, dt) {
            sprite.position.x += sprite.velocity.x * dt;
            //  many more lines of arithmetic.
            //  ...
        }

        function updateSprites(dt) {
            for (var sprite in sprites) {
                updateSprite(sprite, dt);
            }
        }

