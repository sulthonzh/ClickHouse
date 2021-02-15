---
toc_title: PREWHERE
---

# Секция PREWHERE {#prewhere-clause}

Prewhere — это оптимизация для более эффективного применения фильтрации. Она включена по умолчанию, даже если секция `PREWHERE` явно не указана. В этом случае работает автоматическое перемещение части выражения из [WHERE](where.md) до стадии prewhere. Роль секции `PREWHERE` только для управления этой оптимизацией, если вы думаете, что знаете, как сделать перемещение условия лучше, чем это происходит по умолчанию.

При оптимизации prewhere сначала читываются только те столбцы, которые необходимы для выполнения выражения prewhere. Затем читаются другие столбцы, необходимые для выполнения остальной части запроса, но только те блоки, в которых находится выражение prewhere «верно» по крайней мере для некоторых рядов. Если есть много блоков, где выражение prewhere «ложно» для всех строк и для выражения prewhere требуется меньше столбцов, чем для других частей запроса, это часто позволяет считывать гораздо меньше данных с диска для выполнения запроса.

## Управление prewhere вручную {#controlling-prewhere-manually}

`PREWHERE` имеет смысл использовать, если есть условия фильтрации, которые использует меньшинство столбцов из тех, что есть в запросе, но достаточно сильно фильтрует данные. Таким образом, сокращается количество читаемых данных.

В запрос может быть одновременно указано и `PREWHERE` и `WHERE`. В этом случае, `PREWHERE` предшествует `WHERE`.

Если значение параметра `optimize_move_to_prewhere` равно 0, эвристика по автоматическому перемещнию части выражений из `WHERE` к `PREWHERE` отключается.

## Ограничения {#limitations}

`PREWHERE` поддерживается только табличными движками из семейства `*MergeTree`.