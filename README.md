# Лабораторная работа №1. Генетические алгоритмы
### Цель работы
На языке Python разработать скрипт, который с помощью генетического алгоритма и полного перебора решает следующую задачу. Дано N полей для и k культур для посева. Для каждого поля известна характеристика урожайности каждой из k культур, а для каждой культуры – его закупочная стоимость. Необходимо получить самый лучший урожай за наименьшую стоимость.

### Ход работы
Генетические алгоритмы - это методы решения задач, в основе которых лежат принципы теории Дарвина об эволюции (естестественный отбор, мутации, скрещивания): *выживает наиболее приспособленный*.

Существует популяция индивидуумов, которые имеют такую характеристику, как приспосбленность. Приспобленность вычисляется по формуле:

![image](https://github.com/user-attachments/assets/c4dee015-dea3-4276-973c-49fbb31900f9)

Для данной задачt приспособленность вычисляется следующим образом:
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

В данной задачи приспособленность определяется с помощью двух характеристик: цена культуры и приспособленность полей для каждой культуры.

Генерация характеристик происходит случайным образом, для представления их можно отобразить в виде цветной матрицы:

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

Данные для этой карты были получены с помощью различных способов отбора, скрещиваний и мутаций.

###  Сравнительная характеристика различных способ
В данной работе были реализованы различные способы скрещиваний и мутаций
