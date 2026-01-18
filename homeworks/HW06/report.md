# HW06 – Report

> Файл: `homeworks/HW06/report.md`  
> Важно: не меняйте названия разделов (заголовков). Заполняйте текстом и/или вставляйте результаты.

## 1. Dataset

- Какой датасет выбран: `S06-hw-dataset-04.csv`
- Размер: (25000, 61)
- Целевая переменная: `target` (Класс 0 ~95%; Класс 1 ~5%)
- Признаки: Числовые

## 2. Protocol

- Разбиение: train/test (0.6/0.3, `random_state=42`)
- Подбор: CV =5, оптимизация по ROC_AUC
- Метрики: В датасете сильный дисбаланс классов, поэтому метрики F1-score и ROC_AUC нужны. Accuracy не будет отражать качество из-за дисбаланса

## 3. Models

Опишите, какие модели сравнивали и какие гиперпараметры подбирали.
1) DummyClassifier
most_frequent
Используется как baseline

2) LogisticRegression
l1_ratio=0, 
C=1.0,
solver="liblinear",

3) DecisionTreeClassifier
Подбор: max_depth, min_samples_leaf
Лучшие параметры: {'max_depth': 10, 'min_samples_leaf': 4, 'min_samples_split': 10}
4) RandomForestClassifier
Подбор: n_estimators
Использован class_weight='balanced'
scoring='roc_auc'
5) AdaBoostClassifier
Лучшие параметры: {'estimator': DecisionTreeClassifier(max_depth=2), 'learning_rate': 0.5, 'n_estimators': 100}
Подбор: n_estimators, learning_rate
Используется как бустинговый ансамбль

## 4. Results
|Model|Accuracy|Precision|Recall|F1|ROC-AUC|
|-------|-------|-------|-------|-------|-------|
|Dummy|   0.91 | 0.05| 0.05 | 0.05 | 0.5016     |
|Logistic Regression| 0.96 | 0.91|0.25|0.4|0.838|
|Decision Tree|0.96|0.76| 0.4| 0.53 | 0.7715    |
|Random Forest| 0.97 | 1 | 0.34| 0.51 |0.9008   |
|AdaBoost| 0.97 | 0.93 | 0.5 | 0.65 | 0.8762    |

Победитель по ROC-AUC Random Forest. Эта модель хорошо работает при дисбалансе классов.

## 5. Analysis

- Устойчивость: что будет, если поменять `random_state`. Метрики остаются стабильными,есть небольшие колебания
- Ошибки: confusion matrix для лучшей модели содержит основную часть ошибок в FN, FP при этом содержит минимум. Модель не переобучена
- Интерпретация: Наибольший вклад дают несколько признаков, остальные вносят меньший вклад

## 6. Conclusion
Accuracy непоказательна при дисбалансе классов, поэтому основным критерием сравнения выбран ROC-AUC. 
Одиночные деревья склонны к переобучению, тогда как ансамблевые методы (Random Forest, AdaBoost) дают существенно более стабильное и высокое качество.
Использование валидации исключительно на обучающей выборке необходимо для корректной оценки моделей, при этом интерпретация признаков через permutation importance остаётся применимой и для ансамблей.

