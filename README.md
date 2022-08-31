

# Система рекомендаций пререквизитов и результатов обучения
TL;DR: Hit Rate 

## Содержание
* [Описание](#описаниезадачи)
* [Deployment](#deployment)

## Описание задачи
> * Пререквизиты -- навыки, которые студент должен знать *перед* началом курса.
> * Результаты обучения -- навыки, которые студент получит *после* прохождения курса.
> 
<p align="center" width="100%">
 <img src="https://github.com/Mpkosh/LA_rec_sys/blob/main/imgs/ed_pr_maker.png" width="90%" > 
<p align="center"><i>Страница сервиса для создания рабочей  программы  дисциплины (РПД)</i></p>
</p>  

Сервис "Конструктор образовательных программ" создан для работы с учебными планами, РПД и другими концептами в цифровом фомате, где преподаватели заполняют всю необходимую информацию: описание курса, изучаемые разделы и темы, систему оценивания и прочее

**Проблема:** при заполнении РПД нет подсказок/рекомендаций для разделов  результатов и пререквизитов; нужно выбирать область и искать концепты вручную.

**Решение:** выводить рекомендации на основе описания РПД.

## Данные
Все данные были взяты из базы данных сервиса.
|  Данные|  Количество|
|--|--|
| Учебные сущности (возможные результаты и пререквизиты) | 18 351 |
| Введеные экспертом результаты обучения для РПД | 35 801 |
| Введеные экспертом пререквизиты для РПД | 1 065 |
| РПД | 6 205 |
| Учебные планы | 313 |
## Предобработка
В  случае  разработки  базового  варианта  алгоритма  рекомендаций результатов обучения, описанного дальше, была произведена предобработка  текстовых  данных: названия тем в РПД, названия разделов в РПД, названий учебных сущностей

## Рекомендация результатов обучения

В качестве базового варианта учебная сущность считается результатом  обучения, если она входит в название раздела или темы РПД. 

Для улучшенной модели была осуществлена  работа с векторными представлениями. Была выбрана модель Universal Sentence Encoder (USE), которая применялась  в итоговом варианте по следующему  принципу:  
−  вычисление  векторных  представления  тем,  разделов  РПД  и  всех  учебных  сущностей,  
−  подсчет косинусного  сходства  между векторами учебных сущностей и векторами  элементов из описания РПД,  
−  фильтрация  учебных  сущностей  со сходством выше порогового,  
−  вывод N учебных сущностей, где N –  заданное количество рекомендаций

<p align="center" width="100%">
 <img src="https://github.com/Mpkosh/LA_rec_sys/blob/main/imgs/matrix_USE.jpg" width="65%" > 
<p align="center"><i>Косинусное сходство предложений по версии (а) ELMo, (b) BERT, (c) SBERT, and (d) USE</i></p>
</p>  

## Рекомендация пререквизитов обучения

В учебных планах дисциплины стоят в строгом порядке, поэтому подразумевается, что для освоения дисциплины на месте Х пригодятся знания, полученные в результате прохождения дисциплин, стоящих ранее; для выявления пререквизитов конкретной дисциплины  нужно просмотреть результаты обучения дисциплин, находящихся выше в  списке.
В базовой модели чаще встречаемые результаты обучения в вышестоящих дисциплинах будут являться пререквизитами обучения для исходной.

Для улучшенной версии  была также задействована информация о предметных областях учебных сущностей:  если результаты обучения из области, например, математики, то и пререквизиты для  освоения  этой  дисциплины  должны  быть  из  той  же  области. Схема работы следующая:  
−  получить предметные области результатов обучения исходной РПД,  
−  найти дисциплины в учебных планах, где присутствует исходная РПД,  
−  получить  результаты обучения  дисциплин  изучающихся ранее исходной,  
−  отфильтровать результаты обучения вне предметных областей, найденных на первом  этапе.  
−  вывод N самых  часто  встречаемых  учебных сущностей, где N –  заданное количество рекомендаций.

## Оценка
Учитывая, что на данном этапе проекта  мало полностью заполненных РПД, а введенные преподавателями данные недостаточны, оценка выполняется  с  целью определить,  превосходят  ли  разработанные улучшенные  модели  базовые.

/Окончательную оценку алгоритма рекомендаций можно будет провести только после его внедрения в проект.

Для  оценки  результатов  работы  использовались  РПД  с  уже  заполненными результатами и/или пререквизитами обучения  и  где  введены  темы и/или разделы.  
Применялась  метрика **hit rate** –  усредненный  процент  успешно  рекомендованных сущностей относительно общего количества экспертно введенных  сущностей; число рекомендаций от 5 до 15 включительно.  На рисунке  представлены итоговые графики оценки, где красная линия  –  улучшенная  модель.

<p align="center" width="100%">
 <img src="https://github.com/Mpkosh/LA_rec_sys/blob/main/imgs/hit_rate.jpg" width="60%" > 
<p align="center"><i>Hit Rate рекомендаций</i></p>
</p>  

## Deployment
При реализации разработанного алгоритма внутри проекта были  осуществлены следующие изменения  для блока рекомендаций пререквизитов обучения: объединение базовой и улучшенной модели, так как может оказаться, что все ранее изучаемые дисциплины находятся в другой области.

В случае  блока рекомендаций результатов обучения было необходимо  найти вариант работы с моделью векторных представлений, который  бы не подразумевал регулярную загрузку модели при каждом ее вызове. Таким решением стала система Tensorflow Serving:  в  проекте  был  создан  дополнительный docker контейнер  с загруженной моделью USE, который отвечает за работу с  ней  посредством REST- запросов
. 
<p align="center" width="100%">
 <img src="https://github.com/Mpkosh/LA_rec_sys/blob/main/imgs/implementation.png" width="55%" > 
<p align="center"><i>Компоненты со встроенным разделом рекомендаций</i></p>
</p>  

## Прочее
Работа опубликована после выступления на конференции ICETC-2021.
The Development of Learning Outcomes and Prerequisite Knowledge Recommendation System // ACM International Conference Proceeding Series - 2021, pp. 1–6, [https://doi.org/10.1145/3498765.3498766](https://doi.org/10.1145/3498765.3498766)

