# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #5 выполнил:
- Кармацкий Савелий Андреевич
- РИ210942
Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 80 |
| Задание 2 | * | 20 |

знак "*" - задание выполнено; знак "#" - задание не выполнено;

Работу проверили:
- к.т.н., доцент Денисов Д.В.
- к.э.н., доцент Панов М.А.
- ст. преп., Фадеев В.О.

[![N|Solid](https://cldup.com/dTxpPi9lDf.thumb.png)](https://nodesource.com/products/nsolid)

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

Структура отчета

- Данные о работе: название работы, фио, группа, выполненные задания.
- Цель работы.
- Задание 1.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 2.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 3.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Выводы.
- ✨Magic ✨

## Цель работы
Интегрировать экономическую систему в проект Unity и обучить ML-Agent

## Задание 1
**Измените параметры файла. yaml-агента и определить какие параметры и как влияют на обучение модели**


Скачиваем проект, открываем его в Unity, устанавливаем ML-Agenta

![Image alt](https://raw.githubusercontent.com/Karmatsky/DA-in-GameDev-lab5/main/Pic1.png)
Перед тем как перейти к началу обучения, запустим Anaconda Prompt (от имени администратора) и создадим виртуальное пространтсво 
``` conda create -n MLAgent python=3.6.13 ```

И активируем его ```conda activate MLAgent```

Теперь установим необходимые для работы пакеты:

```
pip install mlagents==0.28.0
pip install torch~=1.7.1 --f https://download.pytorch.org/whl/torch_stable.html
```
Запускаем нашего ML-Agent'a на обучение:

![Image alt](https://raw.githubusercontent.com/Karmatsky/DA-in-GameDev-lab5/main/Pic2.png)

Спустя некоторое время завершаем обучение модели:

![Image alt](https://raw.githubusercontent.com/Karmatsky/DA-in-GameDev-lab5/main/Pic3.png)

Далее построим графики для оценки результатов обучения. Для этого установливаем TensorBoard командой ```pip install tensorflow```

![Image alt](https://raw.githubusercontent.com/Karmatsky/DA-in-GameDev-lab5/main/pic4.png)

После обучения ML-Агента установил в виртуальную среду TensorFlow и открыл TensorBoard, с результатами обучения.

![Image alt](https://raw.githubusercontent.com/Karmatsky/DA-in-GameDev-lab5/main/pic5.png)

Со стандартными параметрами ```Economic.yaml```:
```cs
behaviors:
  Economic:
    trainer_type: ppo
    hyperparameters:
      batch_size: 1024
      buffer_size: 10240
      learning_rate: 3.0e-4
      learning_rate_schedule: linear
      beta: 1.0e-2
      epsilon: 0.2
      lambd: 0.95
      num_epoch: 3      
    network_settings:
      normalize: false
      hidden_units: 128
      num_layers: 2
    reward_signals:
      extrinsic:
        gamma: 0.99
        strength: 1.0
    checkpoint_interval: 500000
    max_steps: 750000
    time_horizon: 64
    summary_freq: 5000
    self_play:
      save_steps: 20000
      team_change: 100000
      swap_steps: 10000
      play_against_latest_model_ratio: 0.5
      window: 10
```
По графикам видно, что общее вознаграждение Cumulative Reward растёт, потеря значения Value Loss, наоборот, уменьшается:

![Image alt](https://raw.githubusercontent.com/Karmatsky/DA-in-GameDev-lab5/main/pic6.png)

Какие параметры и как влияют на обучение модели:

* Learning_rate - коэффициент скорости обучения, скорость оптимизации или обучения. Скорость обучения определяет величину изменений, которую оптимайзер может внести за один раз. Нужно подбирать оптимальное значение: если взять слишком большое - модель будет быстрее обучаться, но и шаги станут больше и модель может застрять на месте; если взять слишком маленькое - модель будет заметно дольше обучаться и точно так же может застревать на месте. значение следует уменьшать, если обучение нестабильно, а вознаграждение не увеличивается постоянно Попробовал увеличить его до 1.0e-4 и обучить модель:

![Image alt](https://raw.githubusercontent.com/Karmatsky/DA-in-GameDev-lab5/main/pic7.png)

*Как видно из скрина, из-за того, что значение увеличилось, сеть "застопарилась" и даже пошла в обратном направлении. Уменьшить 'lr' до 1.0e-5 и обучил модель, вот реузльтаты:

![Image alt](https://raw.githubusercontent.com/Karmatsky/DA-in-GameDev-lab5/main/pic8.png)

График стал чуть лучше, вознаграждение возрастает, хоть и довольно медленно

* Beta - это сила энтропийной регуляризации, которая делает политику агента «более случайной». Это гарантирует, что агенты должным образом исследуют пространство действия во время обучения. Увеличение этого параметра обеспечит выполнение большего количества случайных действий. Параметр должен быть скорректирован таким образом, чтобы Entropy медленно уменьшалась вместе с увеличением вознаграждения. Если энтропия падает слишком медленно, нужно уменьшить beta.

Прошлое обучение

![Image alt](https://raw.githubusercontent.com/Karmatsky/DA-in-GameDev-lab5/main/pic12.png)

* Изменил на 1.0e-4

![Image alt](https://raw.githubusercontent.com/Karmatsky/DA-in-GameDev-lab5/main/pic13.png)

* Epsilon влияется на скорость развития политики во время обучения. Соответствует допустимому порогу расхождения между старой и новой политикой при обновлении градиентного спуска. Установка небольшого значения этого параметра приведет к более стабильным обновлениям, но также замедлит процесс обучения.
При увеличении до 0.5 награда оставалась неизменной, число слишком велико. А при маленьком значении, таком как 0.1 - график пришёл в изначальный вид.

* Num_epoch - это параметр, отвечающий за количество проходов через буфер опыта при выполнении оптимизации градиентного спуска. При увеличении этого параметра график начинал вести себя нестабильно, вознаграждение росло гораздо медленее:

![Image alt](https://raw.githubusercontent.com/Karmatsky/DA-in-GameDev-lab5/main/pic11.png)

* Lambd - этот параметр влияет на то, насколько агент полагается на свою текущую оценку стоимости при вычислении обновленной оценки стоимости. Низкие значения соответствуют большему полаганию на собственную оценку.
Увеличил до 0.8 Вознаграждение монотонно возрастало, пока не достигло своего максимального значения:

![Image alt](https://raw.githubusercontent.com/Karmatsky/DA-in-GameDev-lab5/main/Pic10.png)

## Задание 2 
**Опишите результаты, выведенные в TensorBoard**

* ```Cumulative reward``` - cреднее вознаграждение за эпизод. Имеет небольшие изменения. В лучшем случае, должно последовательно увеличиваться с течением времени.
* ```Episode Length``` - средняя продолжительность эпизода в среде для агентов.
* ```Policy Loss``` - этот график определяет величину изменения политики со временем. Должен стремиться вниз, показывая, что политика всё лучше принимает решения.
* ```Value Loss``` - это средняя потеря функции значения. Будет увеличиваться по мере увеличения вознаграждения, а после становления стабильного награждения должно уменьшаться.
* ```Beta``` - график, показывающий изменение силы энтропийной регуляризации, которая делает политику агента «более случайной».
* ```Entropy``` - соответствует тому, насколько случайны решения, показывает величину исследования агента. Должно последовательно уменьшаться с течением обучения.
* ```Learning Rate``` - соответствует скорости обучения, будет постепенно уменьшаться с течением времени.
* ```Extrinsic Reward``` - соответствует среднему совокупному вознаграждению, полученному от окружающей среды за эпизод.
* ```Value Estimate``` - это среднее значение, посещённое всеми состояниями агента. Чтобы отражать увеличение знаний агента, это значение должно расти, а затем стабилизироваться.
## Выводы

В процессе выполнения данной лабораторной работы я научился использовать ML-Agent вместе с экономической системой в Unity. С мопощью этих инструментов можно прибить инфляцию в игре. Визуализировать процесс работы помогает TensorBoard.

| Plugin | README |
| ------ | ------ |
| Dropbox | [plugins/dropbox/README.md][PlDb] |
| GitHub | [plugins/github/README.md][PlGh] |
| Google Drive | [plugins/googledrive/README.md][PlGd] |
| OneDrive | [plugins/onedrive/README.md][PlOd] |
| Medium | [plugins/medium/README.md][PlMe] |
| Google Analytics | [plugins/googleanalytics/README.md][PlGa] |

