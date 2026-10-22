# Расчёт асимптотики — ЛР 1

## Обозначения и метод

- `n` — длина массива; для матриц `n` строк и `m` столбцов; `q` — число запросов; `k` — диапазон значений
  (`max − min + 1`).
- Для каждой задачи разобраны строки метода-решения: **стоимость** одного выполнения строки × **сколько раз**
  она выполняется = **вклад**. Итоговая оценка — сумма вкладов, из которой оставляется старший член.
- Строки с присваиваниями, сравнениями, обращением к элементу массива и арифметикой стоят `O(1)`.
- «Доп. память» — память сверх входных данных. Если ответ создаётся новым массивом, это указано отдельно.
- Ввод-вывод в `main` (чтение `n` чисел и печать ответа) везде стоит `O(n)` / `O(nm)` и асимптотику не ухудшает,
  поэтому ниже не разбирается.
- Ссылки ведут на строки исходников (номера строк относятся к текущей версии файлов).

## Сводная таблица

| № | Задача | Время | Доп. память | Определяющие строки |
|---|--------|-------|-------------|---------------------|
| 1 | Максимум и минимум | O(n) | O(1) | цикл `MinMax.java:29–32` |
| 2 | Индекс первого вхождения | O(n) | O(1) | цикл `FirstOccurrence.java:27–31` |
| 12 | Пара с заданной суммой | O(n) в среднем | O(n) | два цикла `PairWithSum.java:34–43` + `49–51` |
| 13 | Сортировка выбором | O(n²) | O(1) | вложенный цикл `SelectionSort.java:25–29` |
| 19 | Слияние отсортированных | O(n + m) | O(n + m) под ответ | циклы `MergeSorted.java:29–38` |
| 20 | Сортировка подсчётом | O(n + k) | O(k) | `CountingSort.java:44` и цикл `49–53` |
| 33 | Количество инверсий | O(n log n) | O(n) | рекурсия `Inversions.java:38–39` + слияние `47–61` |
| 34 | Максимальная сумма подмассива | O(n) | O(1) | цикл `MaxSubarraySum.java:33–36` |
| 35 | Префиксные суммы | O(n) + O(1) на запрос | O(n) | цикл `PrefixSums.java:25–27`, запрос `42` |
| 38 | Циклический сдвиг влево | O(n) | O(1) | вызовы `RotateLeft.java:32–34` |
| 42 | Вставка в отсортированный | O(n) | O(n) под ответ | поиск `InsertSorted.java:47–54`, копирование `29`, `31` |
| 46 | Сжатие диапазона | O(n log n) | O(n) | `RankCompression.java:40` и цикл `31–34` |
| 53 | Седловые точки | O(nm) | O(n + m) | три двойных цикла `SaddlePoints.java:52–59`, `65–70`, `77–81` |
| 57 | Спиральный обход | O(nm) | O(1) + O(nm) под ответ | строки записи `SpiralTraversal.java:39, 42, 46, 51` |
| 60 | Острова из единиц | O(nm) | O(nm) | обход `Islands.java:38–45` + DFS `56–68` |

---

## 1. Линейный поиск и фильтрация

### Задача 1 — [`MinMax.java`](src/main/java/ru/bmstu/lab1/task_01/MinMax.java#L23-L34)

```java
24:        if (values.length == 0) {
25:            throw new IllegalArgumentException("Array must not be empty");
26:        }
27:        int min = values[0];
28:        int max = values[0];
29:        for (int value : values) {
30:            min = Math.min(min, value);
31:            max = Math.max(max, value);
32:        }
33:        return new int[] {min, max};
```

| Строки | Стоимость | Раз | Вклад |
|--------|-----------|-----|-------|
| 24, 27, 28, 33 | O(1) | 1 | O(1) |
| 29 (заголовок цикла) | O(1) | n + 1 | O(n) |
| 30, 31 | O(1) | n | O(n) |

**Время:** `T(n) = O(1) + O(n) + O(n) = O(n)`.
**Память:** переменные `min`, `max` и массив ответа из двух элементов — `O(1)`.

### Задача 2 — [`FirstOccurrence.java`](src/main/java/ru/bmstu/lab1/task_02/FirstOccurrence.java#L26-L33)

```java
27:        for (int i = 0; i < values.length; i++) {
28:            if (values[i] == target) {
29:                return i;
30:            }
31:        }
32:        return NOT_FOUND;
```

| Строки | Стоимость | Раз (худший случай) | Вклад |
|--------|-----------|---------------------|-------|
| 27 | O(1) | n + 1 | O(n) |
| 28 | O(1) | n | O(n) |
| 29 или 32 | O(1) | 1 | O(1) |

Худший случай — элемента нет или он последний: строка 28 выполняется `n` раз.
Лучший случай — `a[0] = x`, тогда `O(1)`.
**Время:** `O(n)`. **Память:** только счётчик `i`, `O(1)`.

### Задача 12 — [`PairWithSum.java`](src/main/java/ru/bmstu/lab1/task_12/PairWithSum.java#L32-L53)

```java
33:        Map<Long, Integer> lastIndex = new HashMap<>();
34:        for (int i = 0; i < values.length; i++) {
35:            lastIndex.put((long) values[i], i);
36:        }
37:        for (int i = 0; i < values.length; i++) {
38:            long complement = sum - values[i];
39:            Integer last = lastIndex.get(complement);
40:            if (last != null && last > i) {
41:                return new int[] {i, firstIndexAfter(values, i, complement)};
42:            }
43:        }
44:        return NO_PAIR.clone();
...
48:        int j = from + 1;
49:        while (values[j] != target) {
50:            j++;
51:        }
```

| Строки | Стоимость | Раз | Вклад |
|--------|-----------|-----|-------|
| 33 | O(1) | 1 | O(1) |
| 34–35 — `HashMap.put` | O(1) в среднем | n | O(n) |
| 37–40 — `HashMap.get` и сравнение | O(1) в среднем | ≤ n | O(n) |
| 41 → 48–51 `firstIndexAfter` | O(n) (≤ n − i − 1 шагов) | **≤ 1** (сразу `return`) | O(n) |
| 44 | O(1) | ≤ 1 | O(1) |

**Время:** `O(n) + O(n) + O(n) = O(n)` в среднем. Строка 41 вызывает линейный поиск, но всего один раз,
потому что сразу после него метод завершается. Поэтому вложенности `n · n` нет.
В худшем случае `HashMap` (все ключи в одной корзине) строки 35 и 39 стоят `O(log n)`, потому что Java 8+
превращает переполненную корзину в дерево. Это даёт `O(n log n)`.
Для сравнения: перебор всех пар двумя вложенными циклами — `n(n−1)/2 = O(n²)`.
**Память:** `lastIndex` хранит до `n` записей, это `O(n)`.

---

## 2. Сортировка и упорядочивание

### Задача 13 — [`SelectionSort.java`](src/main/java/ru/bmstu/lab1/task_13/SelectionSort.java#L22-L34)

```java
23:        for (int i = 0; i < values.length - 1; i++) {
24:            int minIndex = i;
25:            for (int j = i + 1; j < values.length; j++) {
26:                if (values[j] < values[minIndex]) {
27:                    minIndex = j;
28:                }
29:            }
30:            int tmp = values[i];
31:            values[i] = values[minIndex];
32:            values[minIndex] = tmp;
33:        }
```

| Строки | Стоимость | Раз | Вклад |
|--------|-----------|-----|-------|
| 23, 24, 30–32 | O(1) | n − 1 | O(n) |
| 25–27 (тело внутреннего цикла) | O(1) | Σ_{i=0}^{n−2} (n − 1 − i) = n(n − 1)/2 | O(n²) |

**Время:** `T(n) = n(n − 1)/2 · c₁ + (n − 1) · c₂ = O(n²)`. Число сравнений в строке 26 не зависит от входа,
поэтому лучший случай тоже `Θ(n²)`. Обменов (строки 30–32) ровно `n − 1`.
**Память:** `i, j, minIndex, tmp` — `O(1)`, сортировка на месте.

### Задача 19 — [`MergeSorted.java`](src/main/java/ru/bmstu/lab1/task_19/MergeSorted.java#L24-L40)

```java
25:        int[] result = new int[left.length + right.length];
29:        while (i < left.length && j < right.length) {
31:            result[k++] = left[i] <= right[j] ? left[i++] : right[j++];
32:        }
33:        while (i < left.length) {
34:            result[k++] = left[i++];
35:        }
36:        while (j < right.length) {
37:            result[k++] = right[j++];
38:        }
```

| Строки | Стоимость | Раз | Вклад |
|--------|-----------|-----|-------|
| 25 — выделение и обнуление массива | O(n + m) | 1 | O(n + m) |
| 31 | O(1) | t₁ | |
| 34 | O(1) | t₂ | |
| 37 | O(1) | t₃ | |

Каждое выполнение строк 31, 34 или 37 увеличивает `k` ровно на 1, а `k` растёт от `0` до `n + m`.
Отсюда `t₁ + t₂ + t₃ = n + m`.
**Время:** `O(n + m) + (n + m) · O(1) = O(n + m)`.
**Память:** массив `result` длины `n + m` (это ответ), остальное `O(1)`.

### Задача 20 — [`CountingSort.java`](src/main/java/ru/bmstu/lab1/task_20/CountingSort.java#L30-L54)

```java
36:        for (int value : values) {
37:            min = Math.min(min, value);
38:            max = Math.max(max, value);
39:        }
40:        long range = (long) max - min + 1;
44:        int[] counts = new int[(int) range];
45:        for (int value : values) {
46:            counts[value - min]++;
47:        }
49:        for (int offset = 0; offset < counts.length; offset++) {
50:            for (int c = 0; c < counts[offset]; c++) {
51:                values[position++] = offset + min;
52:            }
53:        }
```

| Строки | Стоимость | Раз | Вклад |
|--------|-----------|-----|-------|
| 36–38 — поиск min/max | O(1) | n | O(n) |
| 44 — выделение `counts` | O(k) | 1 | O(k) |
| 45–46 — подсчёт | O(1) | n | O(n) |
| 49 — внешний цикл | O(1) | k | O(k) |
| 51 — запись | O(1) | Σ counts[offset] = n | O(n) |

Двойной цикл в строках 49–53 **не** даёт `O(n · k)`. Внешний цикл выполняется `k` раз, а внутренний суммарно
по всем итерациям выполняет `Σ counts = n` шагов. Итого `k + n`.
**Время:** `O(n + k)`. **Память:** массив `counts` размера `k`, это `O(k)`. Строки 41–42 ограничивают
`k ≤ 10⁷`, чтобы память не взрывалась при больших значениях.

---

## 3. Арифметика и статистика массивов

### Задача 33 — [`Inversions.java`](src/main/java/ru/bmstu/lab1/task_33/Inversions.java#L26-L63)

```java
27:        int[] work = values.clone();
28:        int[] buffer = new int[values.length];
...
34:        if (to - from < 2) {
35:            return 0;
36:        }
37:        int mid = (from + to) >>> 1;
38:        long inversions = sortAndCount(values, buffer, from, mid) + sortAndCount(values, buffer, mid, to);
39:        return inversions + mergeAndCount(values, buffer, from, mid, to);
...
47:        while (i < mid && j < to) {
48:            if (values[i] <= values[j]) {
49:                buffer[k++] = values[i++];
50:            } else {
51:                inversions += mid - i;
52:                buffer[k++] = values[j++];
53:            }
54:        }
55:        while (i < mid) {
56:            buffer[k++] = values[i++];
57:        }
58:        while (j < to) {
59:            buffer[k++] = values[j++];
60:        }
61:        System.arraycopy(buffer, from, values, from, to - from);
```

Пусть `len = to − from` — длина отрезка в одном вызове.

| Строки | Стоимость | Раз за вызов | Вклад за вызов |
|--------|-----------|--------------|----------------|
| 34, 37 | O(1) | 1 | O(1) |
| 38 — два рекурсивных вызова | T(len/2) | 2 | 2T(len/2) |
| 49 / 52 / 56 / 59 | O(1) | суммарно len (каждая итерация увеличивает `k`) | O(len) |
| 51 — подсчёт инверсий | O(1) | ≤ len | O(len) |
| 61 — копирование | O(len) | 1 | O(len) |

Получаем рекуррентное соотношение `T(n) = 2T(n/2) + c·n`, `T(1) = O(1)`.
По основной теореме (`a = 2, b = 2, f(n) = Θ(n) = Θ(n^{log₂2})`) — **`T(n) = Θ(n log n)`**.
То же через дерево рекурсии: на уровне `d` есть `2^d` отрезков длины `n/2^d`, их суммарная длина `n`, и строки
47–61 на уровне выполняют `O(n)` работы. Уровней `⌈log₂ n⌉`, итого `O(n log n)`.

Почему строка 51 корректна за `O(1)`: половины `[from, mid)` и `[mid, to)` уже отсортированы. Если
`values[i] > values[j]`, то `values[j]` меньше всех `values[i..mid)`. Это `mid − i` инверсий за раз,
без перебора.

**Память:** `work` и `buffer` (строки 27–28) по `n` элементов, плюс стек рекурсии глубиной `⌈log₂ n⌉`.
Итого `O(n)`.

### Задача 34 — [`MaxSubarraySum.java`](src/main/java/ru/bmstu/lab1/task_34/MaxSubarraySum.java#L27-L38)

```java
31:        long bestEndingHere = values[0];
32:        long best = values[0];
33:        for (int i = 1; i < values.length; i++) {
34:            bestEndingHere = Math.max(values[i], bestEndingHere + values[i]);
35:            best = Math.max(best, bestEndingHere);
36:        }
```

| Строки | Стоимость | Раз | Вклад |
|--------|-----------|-----|-------|
| 28, 31, 32, 37 | O(1) | 1 | O(1) |
| 33–35 | O(1) | n − 1 | O(n) |

**Время:** `O(n)`. **Память:** две переменные `long`, `O(1)`.
Для сравнения: перебор всех подмассивов `[l, r]` — `O(n²)` (или `O(n³)` без накопления суммы).

### Задача 35 — [`PrefixSums.java`](src/main/java/ru/bmstu/lab1/task_35/PrefixSums.java#L23-L43)

```java
24:        prefix = new long[values.length + 1];
25:        for (int i = 0; i < values.length; i++) {
26:            prefix[i + 1] = prefix[i] + values[i];
27:        }
...
39:        if (left < 0 || right >= prefix.length - 1 || left > right) {
40:            throw new IndexOutOfBoundsException("Invalid range [" + left + ", " + right + "]");
41:        }
42:        return prefix[right + 1] - prefix[left];
```

| Строки | Стоимость | Раз | Вклад |
|--------|-----------|-----|-------|
| 24 | O(n) | 1 | O(n) |
| 25–26 — предпосчёт | O(1) | n | O(n) |
| 39, 42 — один запрос | O(1) | 1 на запрос | O(1) |

**Время:** предпосчёт `O(n)`, каждый запрос `O(1)`, всего на `q` запросов `O(n + q)`.
Без префиксных сумм каждый запрос суммировал бы до `n` элементов, итого `O(n · q)`.
**Память:** массив `prefix` длины `n + 1`, `O(n)`.

---

## 4. Преобразование массивов

### Задача 38 — [`RotateLeft.java`](src/main/java/ru/bmstu/lab1/task_38/RotateLeft.java#L25-L43)

```java
31:        int k = (int) (((shift % n) + n) % n);
32:        reverse(values, 0, k - 1);
33:        reverse(values, k, n - 1);
34:        reverse(values, 0, n - 1);
...
38:        while (from < to) {
39:            int tmp = values[from];
40:            values[from++] = values[to];
41:            values[to--] = tmp;
42:        }
```

Один вызов `reverse` на отрезке длины `L` выполняет тело цикла (строки 39–41) `⌊L/2⌋` раз:
каждая итерация сближает `from` и `to` на 2.

| Строки | Длина отрезка | Итераций 39–41 | Вклад |
|--------|---------------|----------------|-------|
| 31 | — | — | O(1) |
| 32 | k | ⌊k/2⌋ | O(k) |
| 33 | n − k | ⌊(n − k)/2⌋ | O(n − k) |
| 34 | n | ⌊n/2⌋ | O(n) |

**Время:** `k/2 + (n − k)/2 + n/2 = n` обменов, `O(n)`. Строка 31 приводит `k` к `[0, n)`, поэтому
даже при `k ≫ n` работа не растёт. Наивный сдвиг «по одной позиции `k` раз» стоил бы `O(n · k)`.
**Память:** только `tmp`, `from`, `to`, это `O(1)`.

### Задача 42 — [`InsertSorted.java`](src/main/java/ru/bmstu/lab1/task_42/InsertSorted.java#L26-L56)

```java
27:        int position = upperBound(sorted, value);
28:        int[] result = new int[sorted.length + 1];
29:        System.arraycopy(sorted, 0, result, 0, position);
30:        result[position] = value;
31:        System.arraycopy(sorted, position, result, position + 1, sorted.length - position);
...
47:        while (low < high) {
48:            int mid = (low + high) >>> 1;
49:            if (sorted[mid] <= value) {
50:                low = mid + 1;
51:            } else {
52:                high = mid;
53:            }
54:        }
```

| Строки | Стоимость | Раз | Вклад |
|--------|-----------|-----|-------|
| 47–53 — бинарный поиск | O(1) | ⌈log₂(n + 1)⌉ | O(log n) |
| 28 — выделение | O(n) | 1 | O(n) |
| 29 + 31 — копирование | position + (n − position) = n | 1 | O(n) |
| 30 | O(1) | 1 | O(1) |

Число итераций строк 47–53: длина отрезка `high − low` каждый раз уменьшается как минимум вдвое
(строки 50 и 52), начиная с `n`. После `⌈log₂(n + 1)⌉` шагов она становится нулём.
**Время:** `O(log n) + O(n) = O(n)`. Доминирует копирование: ответ имеет длину `n + 1`, и быстрее его не
построить. Бинарный поиск по условию заменяет линейный поиск позиции (`O(n)`), а сама позиция
находится за `O(log n)`.
**Память:** массив `result` длины `n + 1` (ответ), остальное `O(1)`.

### Задача 46 — [`RankCompression.java`](src/main/java/ru/bmstu/lab1/task_46/RankCompression.java#L28-L48)

```java
29:        int[] unique = sortedUnique(values);
30:        int[] ranks = new int[values.length];
31:        for (int i = 0; i < values.length; i++) {
33:            ranks[i] = Arrays.binarySearch(unique, values[i]);
34:        }
...
39:        int[] sorted = values.clone();
40:        Arrays.sort(sorted);
42:        for (int i = 0; i < sorted.length; i++) {
43:            if (i == 0 || sorted[i] != sorted[i - 1]) {
44:                sorted[size++] = sorted[i];
45:            }
46:        }
47:        return Arrays.copyOf(sorted, size);
```

Пусть `u ≤ n` — число различных значений.

| Строки | Стоимость | Раз | Вклад |
|--------|-----------|-----|-------|
| 39 — копия | O(n) | 1 | O(n) |
| 40 — `Arrays.sort` (Dual-Pivot Quicksort для `int[]`) | O(n log n) | 1 | O(n log n) |
| 42–45 — удаление повторов | O(1) | n | O(n) |
| 47 | O(u) | 1 | O(n) |
| 30 | O(n) | 1 | O(n) |
| 31–33 — `binarySearch` по `unique` | O(log u) | n | O(n log n) |

**Время:** `O(n) + O(n log n) + O(n) + O(n log n) = O(n log n)`.
**Память:** `sorted` (`n`), `unique` (`u ≤ n`), `ranks` (`n`), это `O(n)`.

---

## 5. Двумерные массивы и матрицы

### Задача 53 — [`SaddlePoints.java`](src/main/java/ru/bmstu/lab1/task_53/SaddlePoints.java#L45-L83)

```java
50:        int[] rowMin = rowMinimums(matrix);
51:        int[] columnMax = columnMaximums(matrix);
52:        for (int i = 0; i < matrix.length; i++) {
53:            for (int j = 0; j < matrix[i].length; j++) {
54:                int value = matrix[i][j];
55:                if (value == rowMin[i] && value == columnMax[j]) {
56:                    result.add(new Point(i, j, value));
...
65:        for (int i = 0; i < matrix.length; i++) {           // rowMinimums
67:            for (int value : matrix[i]) {
68:                result[i] = Math.min(result[i], value);
...
77:        for (int[] row : matrix) {                         // columnMaximums
78:            for (int j = 0; j < row.length; j++) {
79:                result[j] = Math.max(result[j], row[j]);
```

| Строки | Стоимость | Раз | Вклад |
|--------|-----------|-----|-------|
| 50 → 65–68 | O(1) | n · m | O(nm) |
| 51 → 76 (`fill`) + 77–79 | O(1) | m + n · m | O(nm) |
| 52–55 — проверка клетки | O(1) | n · m | O(nm) |
| 56 — `ArrayList.add` | O(1) амортизированно | ≤ n · m | O(nm) |

**Время:** `3 · O(nm) = O(nm)`. Без предпосчёта для каждой из `nm` клеток пришлось бы проверять её строку и
столбец (`m + n` операций), итого `O(nm(n + m))`. Строки 50–51 убирают этот множитель.
**Память:** `rowMin` (`n`) + `columnMax` (`m`), это `O(n + m)`, плюс список ответа.

### Задача 57 — [`SpiralTraversal.java`](src/main/java/ru/bmstu/lab1/task_57/SpiralTraversal.java#L27-L60)

```java
31:        int[] result = new int[matrix.length * matrix[0].length];
37:        while (top <= bottom && left <= right) {
38:            for (int j = left; j <= right; j++) {
39:                result[k++] = matrix[top][j];
40:            }
41:            for (int i = top + 1; i <= bottom; i++) {
42:                result[k++] = matrix[i][right];
43:            }
44:            if (top < bottom) {
45:                for (int j = right - 1; j >= left; j--) {
46:                    result[k++] = matrix[bottom][j];
47:                }
48:            }
49:            if (left < right) {
50:                for (int i = bottom - 1; i > top; i--) {
51:                    result[k++] = matrix[i][left];
52:                }
53:            }
54:            top++;
55:            bottom--;
56:            left++;
57:            right--;
58:        }
```

| Строки | Стоимость | Раз | Вклад |
|--------|-----------|-----|-------|
| 31 | O(nm) | 1 | O(nm) |
| 39, 42, 46, 51 — запись в ответ | O(1) | суммарно ровно n · m | O(nm) |
| 37, 44, 49, 54–57 — служебные строки витка | O(1) | ⌈min(n, m)/2⌉ витков | O(min(n, m)) |

Каждая запись в строках 39/42/46/51 увеличивает `k` на 1. Проверки в строках 44 и 49 гарантируют, что ни
одна клетка не записывается дважды, поэтому `k` дойдёт ровно до `nm`. Витков `⌈min(n, m)/2⌉`, потому что
каждый виток сдвигает обе пары границ (строки 54–57).
**Время:** `O(nm) + O(min(n, m)) = O(nm)`.
**Память:** ответ `result` размера `nm` и четыре границы `O(1)`.

### Задача 60 — [`Islands.java`](src/main/java/ru/bmstu/lab1/task_60/Islands.java#L31-L74)

```java
35:        boolean[][] visited = new boolean[grid.length][grid[0].length];
36:        int[] stack = new int[grid.length * grid[0].length];
38:        for (int i = 0; i < grid.length; i++) {
39:            for (int j = 0; j < grid[i].length; j++) {
40:                if (grid[i][j] == 1 && !visited[i][j]) {
41:                    islands++;
42:                    markIsland(grid, visited, stack, i, j);
...
53:        visited[startRow][startColumn] = true;
55:        stack[size++] = startRow * columns + startColumn;
56:        while (size > 0) {
57:            int cell = stack[--size];
60:            for (int d = 0; d < ROW_STEPS.length; d++) {      // 4 соседа
63:                if (isUnvisitedLand(grid, visited, nextRow, nextColumn)) {
64:                    visited[nextRow][nextColumn] = true;
65:                    stack[size++] = nextRow * columns + nextColumn;
```

Ключевое наблюдение: клетка помечается `visited = true` **в момент добавления в стек** (строки 53/64),
а добавляется только непомеченная (строка 63 → 73). Значит, каждая клетка попадает в стек не больше
одного раза за всё время работы, по всем вызовам `markIsland` вместе.

| Строки | Стоимость | Раз (за всё время) | Вклад |
|--------|-----------|--------------------|-------|
| 35, 36 — выделение | O(nm) | 1 | O(nm) |
| 38–40 — перебор клеток | O(1) | n · m | O(nm) |
| 41–42, 53, 55 — запуск DFS | O(1) | число островов ≤ nm | O(nm) |
| 56–59 — извлечение из стека | O(1) | ≤ n · m (каждая клетка один раз) | O(nm) |
| 60–63 — проверка соседей | O(1) | 4 · (число извлечений) ≤ 4nm | O(nm) |
| 64–65 — добавление в стек | O(1) | ≤ n · m | O(nm) |

**Время:** `O(nm)`. Вложенность «двойной цикл → вызов DFS» не даёт `O((nm)²)`: работа DFS суммарно по всем
вызовам ограничена `4nm` благодаря массиву `visited`.
**Память:** `visited` (`nm` булевых) + `stack` (`nm` чисел), это `O(nm)`. Стек вызовов `O(1)`, потому что
DFS итеративный. Рекурсивная версия использовала бы стек вызовов глубиной до `nm` и упала бы со
`StackOverflowError` (проверено тестом `shouldHandleLargeSolidGridWithoutStackOverflow` на матрице `1000 × 1000`).
