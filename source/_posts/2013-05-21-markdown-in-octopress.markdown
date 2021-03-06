---
layout: post
title: "Проблемы с markdown в Octopress"
date: 2013-05-21 13:04
comments: true
categories:
- markdown
- octopress
---

Markdown — прекрасный язык разметки и ему нет никаких альтернатив для написания
постов в блог, документации и т.д.
Но иногда у него есть чудовищные проблемы, главным образом, связанные
с блочными элементами внутри списков. В эти моменты хочется взять и переписать все по-своему.

Например:

{: .language-markdown }
      * Первый элемент списка.

      * Еще один элемент списка, на этот раз состоящий из нескольких абзацев.
        Это первый абзац. За ним идет блок кода.

            var foo = 42;

        И еще третий абзац с текстом.

Теперь блок кода хочется раскрасить.
В Octopress'е есть два способа это сделать: [Backtick Code Blocks](http://octopress.org/docs/plugins/backtick-codeblock/)
и [Codeblock](http://octopress.org/docs/plugins/codeblock/).

Увы, оба совершенно непригодны.

  * Во-первых, предполагается, что они начинаются в самом начале строки.
    Т.е. внутри списков, скажем, они выглядят странно.
    Либо они рушат все ваши инденты, либо же добавляют лишний индент в блок с кодом.

  * Во-вторых, они завершают блочный элемент списка.
    Т.е. в нашем примере выше третий абзац не будет считаться частью списка,
    а превратится в блок кода.

Поэтому решение такое: использовать js-ные хайлайтеры. Например, [Prism](http://prismjs.com/).

В `source/_includes/head.html` нужно подключить `prism.css` и `prism.js`.
После чего код раскрашивается добавлением класса к блокам с кодом при помощи директивы `{: .language-... }`:

{: .language-markdown }
      * Первый элемент списка.

      * Еще один элемент списка, на этот раз состоящий из нескольких абзацев.
        Это первый абзац. За ним идет блок кода.

        {: .language-js }
            var foo = 42;

        И еще третий абзац с текстом.

