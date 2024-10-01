# Лабораторная работа №1. Генетические алгоритмы
### Цель работы
На языке Python разработать скрипт, который с помощью генетического алгоритма и полного перебора решает следующую задачу. Дано N полей для и k культур для посева. Для каждого поля известна характеристика урожайности каждой из k культур, а для каждой культуры – его закупочная стоимость. Необходимо получить самый лучший урожай за наименьшую стоимость.

### Ход работы
Генетические алгоритмы - это методы решения задач, в основе которых лежат принципы теории Дарвина об эволюции (естестественный отбор, мутации, скрещивания): *выживает наиболее приспособленный*.

Существует популяция индивидуумов, которые имеют такую характеристику, как приспосбленность. Приспобленность вычисляется по формуле:

![image](https://github.com/user-attachments/assets/c4dee015-dea3-4276-973c-49fbb31900f9)

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

**Самый лучший исход: Когда на каждом полигоне выбирается культура с максимальной урожайностью и минимальной стоимостью.**

**Самый худший исход: Когда на каждом полигоне выбирается культура с минимальной урожайностью и максимальной стоимостью.**
