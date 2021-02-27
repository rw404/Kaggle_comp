# Kaggle_comp
Дневник своих действий и сохраненные решения с скором

__День 1__:
----
Прочитан [ресурс](https://habr.com/ru/company/ods/blog/426227/) на habr про правильный подход к
решению задачи:
- Нужно заострить внимание на __валидации__, один из возможных ресурсов для машнного обучения на
  выборке: `KFold` из модуля `sklearn`. Разбть выборку на тренировку и тест, затем обучать и
  тестировать. Но как преобразовать потом значения для целевого признака y?
- Коммитить каждый результат со скором, чтобы безболезненно откатиться к стабильному решению
- Просматривать кернелы, но до них еще 2 недели.
- Создать Pipe со всеми методами, который потом модернизировать

Наблюдения:
- Распределения x_*_0 похоже на равномемрное R[0, 3],
- Распределения x_*_1 похоже на нормальное с большим коэффициентм эксцесса,
- Распределения x_*_2 похоже на синус(???), на каждой из x_1_2, x_2_2, x_3_2, ... скачки через
  определенное число элементов
- Распределение целевого признака похоже на нормальное смещенное распределение

Идеи решения:
- Построить нейронную сеть с скрытыми слоями и после кросс-валидации запустить процесс
  обучения(риск: долгая обучение, переобучение)

Решение:
- огромная ошибка, классификация RandomForest не сработала для этой задачи
- огромная ошибка 2, классификация нормального распределения не сработала

__День 2__:
---

Идеи решения:
- Так как задача регрессии, нужно:
    1. Выделить информативные признаки из всех
    2. Через методы регрессии получить их значение
- После этих действий результатом будет скалярное произведение всех 300 массивов-признаков
  размерности 10000(матрица $A \in \R^{10000\times300}$) на полученный в результате регресии вектор 
  $b\in\R^{300}$. Итогом работы программы будет вектор $y = A\times b; y \in \R^{10000}$

Отправки:
- После применения линейной регрессии к данны ошибка достигла 30.
- Проверка метода корреляций -- ошибка 30, нет сильно значимых признаков, так как максимум
  корелляции был 0.11. Возможно есть незначимые признаки, так как минимум -0.02. В ходе проверки
  __не найдены__ признаки, корреляция которых равна 0.

Написана кросс-валидация с параметром n_splits = 10(эксперементально проверенное
значение/советы из литературы), поэтому выборка train разбита на тренировочную и тестовую.


Реализован ансамбль моделей из модуля sklearn. Для каждой модели подсчитывается точность, потом
лучшие варианты своих моделей(например, для моделей KNN и
RandomForestClassifier). 

- В ходе выполнения обучения выявлено, что для KNN оптимальным
значением соседей будет 23.
- В ходе выполнения обучения для модели RandomForest оптимальным значением n_estimators из
  выборки(50, 100, 200) оказалось 100
- Проведено обучение моделей SGDClassifier, LogisticRegression и показаны значения точностей
  этих моделей:
```bash
KNN 0.053
RandomForest 0.072
Logistic 0.046
SGD 0.038
Gaussian 0.051
Ada 0.065
DecTree 0.04

ensemble 0.046(лучший результат 0.057)
```

Лучше всех сработал алгоритм SGD. После проверки выяснилось, что ошибка на тестовых данных
меньше предыдущих, а также в несколько раз меньше ошибок прошлых посылок. После загрузки в kaggle
результат: 6.68..., лучше методов, связанных с корреляцией и регрессиией.

__Подход к задаче с точки зрения теории вероятностей__
---
Возможно, стоит рассмотреть нормальное распределение с мат.ожиданием 18.3, $\sigma = 6.87$, так
как график распределения похож на распределение y.

Полученный график гистограммы y очень похож на нормальное распределение с указанными
параметрами.

