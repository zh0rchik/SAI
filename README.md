# Зюзин Георгий, ИВТАСбд-41, Вариант 10

- [x] [Лабораторная работа №1. Генетические алгоритмы](#title1)
- [x] [Лабораторная работа №2. Нечёткая логика](#title2)
- [x] [Лабораторная работа №3. Базы знаний и онтологии](#title3)

---

# <a id="title1"> Лабораторная работа №1. Генетические алгоритмы </a>
### Цель работы
На языке Python разработать скрипт, который с помощью генетического алгоритма и полного перебора решает следующую задачу. Дано N полей для и k культур для посева. Для каждого поля известна характеристика урожайности каждой из k культур, а для каждой культуры – его закупочная стоимость. Необходимо получить самый лучший урожай за наименьшую стоимость.

### Ход работы
Генетические алгоритмы - это методы решения задач, в основе которых лежат принципы теории Дарвина об эволюции (естестественный отбор, мутации, скрещивания): *выживает наиболее приспособленный*.

Существует популяция индивидуумов, которые имеют такую характеристику, как приспосбленность. Приспобленность вычисляется по формуле:

![image](https://github.com/user-attachments/assets/53b37216-8795-4b95-a59e-cbc3645b35d0)

Для данной задачи приспособленность вычисляется следующим образом:

```python
def oneMaxFitness(individual):
    total_fitness = sum(
        (MATRICES_CULTURES[individual[i]][i] / max(MATRICES_CULTURES[x][i] for x in range(COUNT_CULTURES))) /
        (COSTS_CULTURES[individual[i]] / min(COSTS_CULTURES) if COSTS_CULTURES[individual[i]] > 0 else 1)
        for i in range(COUNT_POLYGONS)
    )
    
    return total_fitness / (COUNT_POLYGONS / 100),
```

В результате получается приспособленность индивидуума, которая представляет собой среднее значение всех полей.

*Самый лучший исход: Когда на каждом полигоне выбирается культура с максимальной урожайностью и минимальной стоимостью.*

*Самый худший исход: Когда на каждом полигоне выбирается культура с минимальной урожайностью и максимальной стоимостью.*

В данной задаче приспособленность определяется с помощью двух характеристик: цена культуры и приспособленность полей для каждой культуры.

Генерация характеристик происходит случайным образом. Для представления урожайности полей каждой культуры их можно отобразить в виде цветной матрицы:

![image](https://github.com/user-attachments/assets/bae48b98-0cae-4451-bbbe-cde87e203525)

```python
COUNT_CULTURES = 3

ROWS_COUNT_POLYGONS = 10
COULUMNS_COUNT_POLYGONS = 10
COUNT_POLYGONS = ROWS_COUNT_POLYGONS * COULUMNS_COUNT_POLYGONS  

MATRICES_CULTURES = []

# Вероятности для каждой культуры
P_CORP = [random.uniform(0.1, 0.9) for _ in range(COUNT_CULTURES)]
# Стоимость культур
COSTS_CULTURES = [random.randint(5, 25) for _ in range(COUNT_CULTURES)]    # тыс. руб.
print(max(COSTS_CULTURES))

# Характеристика культур на поле
for i in range(COUNT_CULTURES):
    MATRICES_CULTURES.append([
        random.uniform(0.5, 1.0) if random.random() < P_CORP[i] else random.uniform(0.1, 0.5)
        for j in range(COUNT_POLYGONS)  # Используем переменную j для внутреннего цикла
    ])

# количество строк и столбцов для сетки
num_columns = 3  # Максимум 3 графика в ряду
num_graphs = COUNT_CULTURES
num_rows = (num_graphs + num_columns - 1) // num_columns  # Высчитывание строк

# Визуализация
plt.figure(figsize=(15, 5 * num_rows))  #  размеры фигуры для нескольких строк

for i in range(COUNT_CULTURES):
    # Преобразование одномерного массива в матрицу (2D массив) для отображения
    matrix_for_schema = np.reshape(MATRICES_CULTURES[i], (ROWS_COUNT_POLYGONS, COULUMNS_COUNT_POLYGONS))
    
    plt.subplot(num_rows, num_columns, i + 1)  # место каждого графика в сетке
    plt.imshow(matrix_for_schema, cmap='viridis', aspect='auto')  # Визуализация двумерного массива

    plt.title(f"Культура {i}")
    plt.colorbar(label='Урожайность культуры')

plt.tight_layout()  # Чтобы графики и подписи не пересекались
plt.show()
```

В результате работы программы можно получить карту для посадки культур:

![image](https://github.com/user-attachments/assets/dbb1dded-8649-47eb-a260-9c06ec1bb40a)

А так же развитие приспособленности у популяции и лучшего индивидуума:

![image](https://github.com/user-attachments/assets/20d50742-b735-48a5-8cab-f555036de944)

Данные для этой карты были получены с помощью различных способов отбора, скрещиваний и мутаций.

###  Сравнительная характеристика различных способов
В данной работе были реализованы различные способы скрещиваний и мутаций.

#### Методы срещивания
Скрещивание (также называется кроссинговер и кроссовер) следующая базовая операция в генетическом алгоритме. Здесь перебираются пары родителей (как правило, без повторения) из отобранной популяции и с некоторой высокой вероятностью выполняется обмен фрагментами генетической информации для формирования хромосом двух потомков. Если родители не участвовали в скрещивании, то они переносятся (копируются) в следующее поколение.

##### Одноточечное скрещивание
В самом простом варианте операция кроссинговера выполняет обмен между двумя половинками хромосом родителей для формирования хромосом потомков. Вначале случайным образом определяется точка разреза хромосомы, а затем, соответствующие части меняются местами. Получаются две новые хромосомы для двух потомков.

![image](https://github.com/user-attachments/assets/a63c07ce-1648-478c-b627-b977cd2f82ab)

В программе данный способ реализовывается следующим способом:

```python
# 1. одноточный кроссенгровер
def cxOnePoint(child1, child2):
    s = random.randint(2, len(child1)-3)
    child1[s:], child2[s:] = child2[s:], child1[s:]
```


##### Двухточечное и k-точечное скрещивание
Однако, как показывает практика, двухточечное скрещивание дает лучшие результаты, чем одноточечное. При двухточечном кроссинговере вместо одной точки разреза выбираются две случайным образом (разумеется, они не должны попадать на границы хромосом и совпадать между собой).

Принцип работы двухточечного скрещивания демонстрируется следующим рисунком:

![image](https://github.com/user-attachments/assets/18ddee68-293e-455f-9668-11597c69bf5d)

В программе данный способ реализовывается следующим способом:

```python
# 2. кросенгровер с двумя точками разрыва
def cxTwicePoint(child1, child2):
    s1 = random.randint(2, len(child1) // 2)
    child1[s1:], child2[s1:] = child2[s1:], child1[s1:]
    s2 = random.randint(len(child1) // 2, len(child1)-3)
    child1[s2:], child2[s2:] = child2[s2:], child1[s2:]
```

##### Скрещивание смешением
С помощью этого способа происходит полное перемешивание генов родителей. Для данной задачи этот способ не подходит. В программе может быть реализован таким образом:


```python
# 3. кросенгровер смешиванием, вариант 2
def cxMix(child1, child2):
    s = [*child1, *child2]

    random.shuffle(s)
    half = len(s) // 2

    child1 = s[:half]
    child2 = s[half:]
```
В результате алгоритмы часто приходят к одному и тому же, поэтому для сравнения способов будет уменьшено количество поколений до 25:
<table>
    <tr>
        <th></th>
        <th>Макс. приспособленность</th>
        <th>Ср. приспособленность</th>
    </tr>
    <tr>
        <td>Одноточный кроссенгровер</td>
        <td>87.16866722008685</td>
        <td>85.69980893573239</td>
    </tr>
    <tr>
        <td>Двуточный кроссенгровер</td>
        <td>88.43004047272136</td>
        <td>86.86442803352386</td>
    </tr>
    <tr>
        <td>Кроссенгровер смешиванием</td>
        <td>64.74740850480622</td>
        <td>64.74740850480622</td>
    </tr>
</table>

Из данной таблицы видно, что кроссенгровер с двумя разрывами `cxTwicePoint` более эффективный, чем с одним `cxOnePoint` (в представленно случае это незначительно). Самый худший скрещивание перемешиванием `cxMix`.

#### Методы мутации
Последний оператор имитации процесса эволюции – это мутация. Она применяется к полученной популяции и случайным образом с малой вероятностью меняет значения отдельных генов.

##### Инвертирование битов
В самом простом варианте двоичного кодирования генов, мутация выполняет инвертирование бита:

![image](https://github.com/user-attachments/assets/024fa67a-5ae2-4bd9-896d-928e2cc20370)

Для данной задачи этот способ был немного переделан: область значений для изменения генов увеличен.

```python
# 1. мутация
def mutFlip(mutant, indpb=0.01):
    for indx in range(len(mutant)):
        if random.random() < indpb:
            rand_mutation = mutant[indx]
            while rand_mutation == mutant[indx]:
                rand_mutation = random.randint(0, COUNT_CULTURES - 1)
```

##### Мутация обращением
Несколько видоизмененная идея мутации обменом является другой способ – мутация обращением. Здесь мы выбираем также случайным образом непрерывную последовательность генов, которые, затем, записываем в обратном порядке:

![image](https://github.com/user-attachments/assets/2ab7430c-9b7e-4fcc-836e-8c475d9c4439)

В программе данный способ реализовывается следующим способом:

```python
# 2. мутация разворота фрагмента
def mutReverse(mutant, indpb=0.01):
    s = random.randint(0, len(mutant) - 4)
    s_mutation = [mutant[s], mutant[s+1], mutant[s+2]]
    s_mutation.reverse()
    mutant[s], mutant[s+2] = s_mutation[0], s_mutation[2]
```

##### Мутация обменом
Для упорядоченных списков (когда в генах хромосомы хранятся индексы некоторого списка и они не должны повторяться, как в задаче коммивояжера) можно выполнять мутацию путем обмена случайно выбранных генов:

В программе данный способ реализовывается следующим способом:

```python
# 3. мутация перестановки двух геной
def mutSwap(mutant, indpb=0.01):
    s1 = random.randint(0, len(mutant) - 1)
    s2 = s1
    while(s2 == s1):
        s2 = random.randint(0, len(mutant) - 1)
    
    mutant[s1], mutant[s2] = mutant[s2], mutant[s1]
```

Аналогично сравним мутации, как и кроссенгроверы:
<table>
    <tr>
        <th></th>
        <th>Макс. приспособленность</th>
        <th>Ср. приспособленность</th>
    </tr>
    <tr>
        <td>Изменение одного гена</td>
        <td>87.16866722008685</td>
        <td>85.69980893573239</td>
    </tr>
    <tr>
        <td>Мутация обращением</td>
        <td>87.7747073963472</td>
        <td>85.98478584587788</td>
    </tr>
    <tr>
        <td>Мутация обменом</td>
        <td>86.95468986300023</td>
        <td>85.14793469220841</td>
    </tr>
</table>

Из данной таблицы видно, что есть незначительные различия между `mutFlip`,`mutReverse` и `mutSwap`.

###  Вывод
В данной лабораторной работе была реализована задача оптимального выбора культур для полей с использованием генетических алгоритмов и полного перебора. Генетический алгоритм продемонстрировал свою эффективность в решении задачи, опираясь на эволюционные принципы отбора, скрещивания и мутации. 

Были исследованы и сравнены различные методы скрещивания и мутации:
- **Одноточечное и двуточечное скрещивание** показали хорошие результаты, при этом двуточечное скрещивание оказалось немного эффективнее.
- **Скрещивание смешением** продемонстрировало худшие результаты по сравнению с другими методами.
- Среди методов мутации **инвертирование битов и обращение** показали лучшие результаты, тогда как **перестановка двух генов** оказалась менее эффективной.
---

# <a id="title2"> Лабораторная работа №2. Нечёткая логика </a>
### Цель работы
На языке Python разработайте скрипт, позволяющий выполнить операцию импликации заданных пользователем нечетких множеств с трапециевидными функциями принадлежности. Входными данными будут параметры функций принадлежности и четкие объекты для каждого из множеств. Выходными – результат импликации данных нечетких множеств. Импликацию моделировать минимумом.

Погода
- Температура: холодно, прохладно, тепло, жарко
- Влажность воздуха: сухо, умеренно влажно, влажно, очень влажно

### Ход работы
#### Трапециевидная функция принадлежности
Нечеткое множество характеризуется функцией принадлежности, которая возвращает степень принадлежности элемента x множеству в диапазоне [0, 1].

Трапециевидная функция принадлежности определяется как:

![image](https://github.com/user-attachments/assets/cc05dd8e-a371-4115-a717-9a926784eda3)

Графически функция выглядит следующим образом:

![image](https://github.com/user-attachments/assets/a89c9a89-d565-49f1-b862-e2cad1afca3b)

![image](https://github.com/user-attachments/assets/e1e71e7a-4154-4a20-aeae-efe05bb8eaf9)


#### Операция импликации
Импликация двух нечетких множеств A и B реализуется с использованием операции минимума:

Импликация(A,B)=min(μA(x),μB(x))

#### Описание программы
##### Функции программы
##### Трапециевидная функция принадлежности
Функция реализована с проверкой деления на ноль и корректной обработкой краевых случаев:
```python
def trapezoidal_mf(x, a, b, c, d):
    """
    Трапециевидная функция принадлежности с проверками на деление на ноль.
    :param x: Точки, для которых вычисляется функция принадлежности.
    :param a: Левая граница начала возрастания функции.
    :param b: Левая верхняя граница (где функция равна 1).
    :param c: Правая верхняя граница (где функция равна 1).
    :param d: Правая граница окончания убывания функции.
    :return: Значение функции принадлежности в точках x.
    """
    # Инициализация массивов
    left_slope = np.zeros_like(x)
    right_slope = np.zeros_like(x)

    # Проверка на деление на ноль и корректное вычисление левой части
    if b != a:
        left_slope = (x - a) / (b - a)
    else:
        left_slope = np.ones_like(1)
    
    # Проверка на деление на ноль и корректное вычисление правой части
    if c != d:
        right_slope = (d - x) / (d - c)
    else:
        right_slope = np.ones_like(1)

    # Применяем ограничения на значения от 0 до 1
    left_slope = np.clip(left_slope, 0, 1)
    right_slope = np.clip(right_slope, 0, 1)
    
    # Возвращаем минимум из левой и правой части, чтобы создать трапециевидную форму
    return np.maximum(0, np.minimum(left_slope, np.minimum(1, right_slope)))
```

##### Функции принадлежности для параметров
Для каждого параметра (температуры и влажности) определены функции принадлежности с конкретными параметрами (ниже приведен пример температуры):
```python
def temperature_membership(x):
    cold = trapezoidal_mf(x, 0, 0, 10, 20)
    cool = trapezoidal_mf(x, 15, 20, 25, 30)
    warm = trapezoidal_mf(x, 25, 30, 35, 40)
    hot = trapezoidal_mf(x, 35, 40, 50, 50)
    return cold, cool, warm, hot
```

##### Операция импликации
```python
def implication(A, B):
    return np.minimum(A, B)
```

##### Визуализация
Реализованы функции для построения графиков функций принадлежности и результатов импликации.

![image](https://github.com/user-attachments/assets/acfbc009-fe54-4cfe-bf75-ac0dadd5f959)

![image](https://github.com/user-attachments/assets/27de172d-804f-4afa-a217-c5376168be02)

![image](https://github.com/user-attachments/assets/ce8b0da6-e1db-4c06-89ab-a6c9d3712806)

![image](https://github.com/user-attachments/assets/518adedf-590f-43e4-9d69-9551710a7d14)

![image](https://github.com/user-attachments/assets/ad2fcfa0-12d6-4154-8692-463a9252a348)

![image](https://github.com/user-attachments/assets/f0398624-fbe4-4c4c-937b-535807e0469d)

### Вывод
В результате выполнения лабораторной работы была разработана программа, реализующая:

1. Трапециевидные функции принадлежности для моделирования параметров "температура" и "влажность".
2. Операцию импликации нечетких множеств с использованием минимума.
3. Визуализацию функций принадлежности и результатов импликации.
4. Полученные результаты наглядно демонстрируют изменение степеней принадлежности при различных условиях.

Программа корректно решает поставленную задачу и может быть использована для дальнейших исследований нечеткой логики.

### Литература
Нечеткая логика — математические основы - https://loginom.ru/blog/fuzzy-logic

---

# <a id="title3"> Лабораторная работа №3. Базы знаний и онтологии </a>
