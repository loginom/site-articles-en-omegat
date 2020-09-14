---
publish: 2020-01-14
---

# Clustering of Categorical Data: the Scalable CLOPE Algorithm

**Grouping according to the similar features of categorical and transactional arrays of data in large DB is the most important task of Data Mining. In the majority of cases, traditional clustering algorithms are not effective while large databases processing. The scalable heuristical CLOPE algorithm is discussed in the article. It allows for high quality and performance of clustering.**

## Introduction and Main Concepts

The issue of [clustering](https://wiki.loginom.ru/articles/clustering.html) of large arrays of [categorical data](https://wiki.loginom.ru/articles/categorical-data.html) is highly topical for data analysis systems. The categorical data occur in all spheres: industry, trade, marketing, medicine, etc. The categorical data include so-called transactional data: supermarket receipts, tracking data of web surfing. This includes analysis and classification of text documents ([Text Mining](https://wiki.loginom.ru/articles/text-mining.html)).

Hereafter the categirical data refer to qualitative characteristics of objects measured according to the nominal [scale](https://wiki.loginom.ru/articles/scale-type.html). It is to be recalled that when using the nominal scale, it is required to specify only whether the objects are similar as far as the measured feature is concerned.

It is not effective and sometimes even impossible to use traditional algorithms for clustering of objects with categorical features (more detailed information is available in the mateiral called "[Clustering Algorithms for Data Mining"](https://loginom.ru/blog/data-mining-clustering)). The main complications are connected with high dimensionality and huge volume typical of such databases.

The algorithms based on the pair-wise distance calculation ([k-means](https://wiki.loginom.ru/articles/k-means.html) and the similar ones) are mostly effective for numerical data. When using arrays of records with large amount of non-numerical factors, their performance is unsatisfactory. And the importance lies not so much in the complexity of metrics definition to calculate distances between the categorical [attributes](https://wiki.loginom.ru/articles/attribute.html), as in the fact that it is required to provide pair-wise comparison of objects with each other for each algorithm iteration, and there can be too many iterations. It is not applicable for the tables with millions of records and thousands of fields.

That's why development of [scalable algorithms](https://wiki.loginom.ru/articles/scalable-algorithm.html) for clustering of the categorical and transactional data are actively researched nowadays. The following special requirements are imposed on them:

* minimum possible number of the database table "scans";
* operation under condition of the limited computer RAM capacity;
* possibility to interrupt the algorithm operation providing saving of intermediate results to continue calculations later;
* operability of the algorithm when objects can be extracted from database only in the forward-only cursor mode (namely, in the record navigation mode).

Nowadays there are more than a dozen methods of work with categorical data, for example, a family of hierarchical clustering algorithms. But they do not always meet the requirements specified above. The LargeItem algorithm is considered to be one of the most effective algorithms. It is based on improvement of some global criterion. This global criterion is based on the support parameter (as far as terminology is concerned, there is much in common with algorithms for detection of [association rules](https://wiki.loginom.ru/articles/association-rules.html)).

In fact, calculation of the global criterion makes the clustering algorithm many times faster as compared with the local criterion in the case of the pair-wise comparison of objects. That's why "globalisation" of the valuation function is one of the ways to get scalable algorithms.

The CLOPE algorithm analyzed in this article is similar to LargeItem but it is faster and easier as far as software implementation is concerned. CLOPE was proposed in 2002 by a team of the Chinese scientists. It provides higher performance and clustering quality as compared with the LargeItem algorithm and many hierarchical algorithms.

First, let's formalize the clustering task under consideration for categorical data. The case will be described as though we had a database of transactional data, and in the end it will be explained how to use CLOPE to [cluster](https://wiki.loginom.ru/articles/cluster.html) any categorical arrays working with them as with the transactional ones.

In this case, the [transaction](https://wiki.loginom.ru/articles/transaction.html) means a random set of objects, for example, a list of key words in an article, goods bought in the supermarket, a set of patient symptoms, typical image elements, etc. The task of the transactional data clustering is to provide clustering of the whole set of transactions to place all similar transactions into one cluster and the different ones - into different clusters.

The CLOPE clustering algorithm is based on the concept of maximisation of the global cost function that increases proximity of transactions in clusters by increase of the cluster [histogram](https://wiki.loginom.ru/articles/histogram.html) parameter. Let's consider a simple example that consists of 5 transactions: { {% math %}(a,b){% endmath %}, {% math %}(a,b,c){% endmath %}, {% math %}(a,c,d){% endmath %}, {% math %}(d,e){% endmath %}, {% math %}(d,e,f){% endmath %} }. Imagine that it is required to compare two cases of clustering with each other:

{  { {% math %}ab, abc, acd{% endmath %} }, { {% math %}de, def{% endmath %} } } , (1)

{ { {% math %}ab, abc{% endmath %} }, { {% math %}acd, de, def{% endmath %} } }, (2)

For the first and second cases of clustering it is required to calculate the number of occurrences of each transaction item in each cluster, and then it is required to calculate the height ({% math %}H{% endmath %}) and width ({% math %}W{% endmath %}) of the cluster. For example, { {% math %}ab, abc, acd{% endmath %} } cluster has {% math %}a:3{% endmath %}, {% math %}b:2{% endmath %}, {% math %}c:2{% endmath %} с {% math %}H=2{% endmath %} and {% math %}W=4{% endmath %} occurrences. To facilitate understanding, Figure. 1 shows these results geometrically in the form of histograms.

![Figure 1. Histograms of two clustering cases](split.svg)

Let's estimate the quality of two clustring cases according to analysis of their {% math %}H{% endmath %} height and {% math %}W{% endmath %} width. { {% math %}de, def{% endmath %} } and { {% math %}ab, abc{% endmath %} } clusters have the similar histograms, consequently, they are equivalent. The histogram for { {% math %}ab, abc, acd{% endmath %} } cluster contains 4 different items, and its area is equal to 8 blocks ({% math %}H=2.0, H/W=0.5{% endmath %}), and { {% math %}acd, de, def{% endmath %} } cluster contains 5 different items with the same area ({% math %}H=1.6, H/W=0.32{% endmath %}). Clearly, clustering (1) is better since we prefer more overlapping among
transactions (consequently, {% math %}H{% endmath %} parameter is higher).

The CLOPE algorithm (English: Clustering with sLOPE) is used according to such obvious and simple concept of geometric histograms. Let's analyze it in detail in its more formal description.

## The CLOPE Algorithm

Let's take a database of transactions {% math %}D{% endmath %} taht consists of a set of transactions { {% math %}t_1,t_2,…,t_n{% endmath %} }. Each transaction has s set of objects { {% math %}i_1,…,i_m{% endmath %} }. Set of clusters { {% math %}C_1,…,C_k{% endmath %} } is clustering of a set { {% math %}t_1,…,t_n{% endmath %} } that allows for {% math %}C_1 … C_k={% endmath %} { {% math %}t_1,…,t_n{% endmath %} } and {% math %}C_i\neq \varnothing  \wedge C_i \bigcap C_j = \varnothing{% endmath %}, for {% math %}1<=i{% endmath %}, {% math %}j<=k{% endmath %}. Each {% math %}C_i{% endmath %} item is called a cluster, {% math %}n{% endmath %}, {% math %}m{% endmath %}, {% math %}k{% endmath %} are used
respectively for the number of transactions, the number of objects in the base of transactions, and the number of clusters.

Each {% math %}C{% endmath %} cluster has the following features:

* {% math %}D(C){% endmath %} – a set of unique objects;
* {% math %}Occ(i,C){% endmath %} – number of occurrences (frequency) of the object {% math %}i{% endmath %} in {% math %}C{% endmath %} cluster;
* {% math %}S(C)=\sum_{i\in\ D(C)}\ Occ\ (i, C)=\sum_{t_i\in C}\mid  t_i \mid{% endmath %}
* {% math %}W(C) = |D(C)|{% endmath %};
* {% math %}H(C) = S(C)/W(C){% endmath %}.

The histogram of {% math %}C{% endmath %} cluster is called the graphic presentation of its estimated characteristics: with cluster objects along the {% math %}OX{% endmath %}-axis decreasingly ordered by {% math %}Occ(i,C){% endmath %} value, and  {% math %}Occ(i,C){% endmath %} value – along the {% math %}OY{% endmath %}-axis (refer to Figure 2).

![Figure 2. Representation of the histogram of cluster](histogram.svg)

Figure 2 shows that {% math %}S(C)=8{% endmath %}, it complies with the area of the rectangle restricted by coordinate axes and dashed line. It's straightforward that the larger {% math %}H{% endmath %} value is, the more similar the two transactions are. That's why the algorithm must select the clusterings that maximize {% math %}H{% endmath %}.

However, {% math %}H{% endmath %} height value alone is not enough. Возьмем базу, состоящую из 2-х транзакций: { {% math %}abc, def{% endmath %} }. Они не содержат общих объектов, но разбиение { { {% math %}abc, def{% endmath %} } } и разбиение { { {% math %}abc{% endmath %} }, { {% math %}def{% endmath %} } } характеризуются одинаковой высотой {% math %}H=1{% endmath %}. Получается, оба варианта разбиения равноценны. Но если для оценки вместо {% math %}H(C){% endmath %} использовать градиент {% math %}G(C)=H(C)/W(C)=S(C)/W(C)^2{% endmath %}, то разбиение { { {% math %}abc{% endmath %} },{ {% math %}def{% endmath %} } } будет лучше (градиент каждого кластера равен 1/3 против 1/6 у разбиения { { {% math %}abc, def{% endmath %} } }).

Обобщив вышесказанное, запишем формулу для вычисления глобального критерия – функции стоимости {% math %}Profit(C){% endmath %}:

{% math %}Profit (C) = \frac {\sum_{i=1}^k G(C_i)\times\mid C_i\mid}{\sum_{i=1}^k \mid C_i\mid}=\frac {\sum_{i=1}^k \frac{S(C_i)}{W(C_i)^r}\times\mid C_i\mid}{\sum_{i=1}^k \mid C_i\mid}{% endmath %}

где:

* {% math %}|Ci|{% endmath %} – количество транзакций в {% math %}i{% endmath %}-том кластере
* {% math %}k{% endmath %} – количество кластеров
* {% math %}r{% endmath %} – положительное вещественное число большее 1.

С помощью параметра {% math %}r{% endmath %}, названного авторами CLOPE коэффициентом отталкивания (repulsion), регулируется уровень сходства транзакций внутри кластера, и, как следствие, финальное количество кластеров. Этот коэффициент подбирается пользователем. Чем больше {% math %}r{% endmath %}, тем ниже уровень сходства и тем больше кластеров будет сгенерировано.

Формальная постановка задачи кластеризации алгоритмом CLOPE выглядит следующим образом: для заданных {% math %}D{% endmath %} и {% math %}r{% endmath %} найти разбиение {% math %}C: Profit(C,r) -> max{% endmath %}.

## Реализация алгоритма

Предположим, что транзакции хранятся в таблице базы данных. Лучшее решение ищется в течение последовательного итеративного перебора записей базы данных. Поскольку критерий оптимизации имеет глобальный характер, основанный только на расчете {% math %}H{% endmath %} и {% math %}W{% endmath %}, производительность и скорость алгоритма будет значительно выше, чем при попарном сравнении транзакций.

Реализация алгоритма требует первого прохода по таблице транзакций для построения начального разбиения, определяемого функцией {% math %}Profit(C,r){% endmath %}. После этого требуется незначительное (1-3) количество дополнительных сканирований таблицы для повышения качества кластеризации и оптимизации функции стоимости. Если в текущем проходе по таблице изменений не произошло, то алгоритм прекращает свою работу. Псевдокод алгоритма имеет следующий вид.

1. // Фаза 1 – инициализация
2. &nbsp;&nbsp; Пока не конец
3. &nbsp;&nbsp;&nbsp;&nbsp;прочитать из таблицы следующую транзакцию [t, -];
4. &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;положить t в существующий либо в новый кластер C<sub>i</sub>, который дает максимум Profit(C,r);
5. &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;записать [t,i] в таблицу (номер кластера);
6. // Фаза 2 – Итерация
7. &nbsp;&nbsp;Повторять
8. &nbsp;&nbsp;&nbsp;&nbsp;перейти в начало таблицы;
9. &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;moved := false;
10. &nbsp;&nbsp;&nbsp;&nbsp;пока не конец таблицы
11. &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;читать [t,i];
12. &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;положить t в существующий либо в новый кластер C<sub>j</sub>, который максимизирует Profit(C,r);
13. &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;если C<sub>i</sub>&lt;&gt;C<sub>j</sub> тогда
14. &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;записать [t,i];
15. &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;moved := true;
16. &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;пока (not moved).
17. удалить все пустые кластеры;

Как видно, алгоритм CLOPE является масштабируемым, поскольку способен работать в ограниченном объеме оперативной памяти компьютера. Во время работы в RAM хранится только текущая транзакция и небольшое количество информации по каждому кластеру, которая состоит из: количества транзакций {% math %}N{% endmath %}, числа уникальных объектов (или ширины кластера) {% math %}W{% endmath %}, простой хэш-таблицы для расчета {% math %}Occ(i,C){% endmath %} и значения {% math %}S{% endmath %} площади кластера. Они называются кластерными характеристиками (CF – cluster features). Для простоты обозначим их как свойства кластера {% math %}C{% endmath %}, например, {% math %}C.Occ[i]{% endmath %} означает число вхождений объекта {% math %}i{% endmath %} в кластер {% math %}C{% endmath %} и т.д. Можно посчитать, что для хранения частоты вхождений 10 тыс. объектов в 1 тыс. кластерах необходимо около 40 Мб оперативной памяти.

Для завершения реализации алгоритма нам нужны еще две функции, рассчитывающие прирост {% math %}Profit(C,r){% endmath %} при добавлении и удалении транзакции из кластера. Это легко сделать, зная величины {% math %}S{% endmath %}, {% math %}W{% endmath %} и {% math %}N{% endmath %} каждого кластера:

1. function DeltaAdd(C,t,r): double;
2. begin
3. &nbsp;&nbsp;S_new := C.S + t.ItemCount;
4. &nbsp;&nbsp;W_new := C.W;
5. &nbsp;&nbsp;for i:=0 to t.ItemCount–1 do
6. &nbsp;&nbsp;&nbsp;&nbsp; if (C.Occ[t.items[i]]=0) then W_new := W_new + 1;
7. &nbsp;&nbsp;result := S_new &#042; (C.N+1)/(W_new)<sup>r</sup> – C.S &#042; C.N/(C.W)<sup>r</sup>
8. end;

Здесь {% math %}t.Items[i]{% endmath %} – значение {% math %}i{% endmath %}-го объекта транзакции {% math %}t{% endmath %}. Заметим, что {% math %}DeltaAdd(C,t,r){% endmath %} при добавлении {% math %}t{% endmath %} в новый кластер равна {% math %}S/W^r{% endmath %}, где {% math %}S{% endmath %} и {% math %}W{% endmath %} – площадь и ширина кластера, состоящего из добавляемой транзакции {% math %}t{% endmath %}.

Реализация функции прироста {% math %}Profit(C,r){% endmath %} при удалении транзакции похожа на {% math %}DeltaAdd(C,t,r){% endmath %}, поэтому опустим ее подробный код.

Следующая теорема гарантирует корректность использования функции {% math %}DeltaAdd{% endmath %}.

**Теорема.** Если {% math %}DeltaAdd(Ci,t){% endmath %} есть максимум, то перемещение {% math %}t{% endmath %} в кластер {% math %}C_i{% endmath %} максимизирует {% math %}Profit(C,r){% endmath %}.

Теперь можно оценить вычислительную сложность алгоритма CLOPE. Пусть средняя длина транзакции равна {% math %}A{% endmath %}, общее число транзакций {% math %}N{% endmath %}, максимально возможное число кластеров {% math %}K{% endmath %}. Временная сложность одной итерации равна {% math %}O(N*K*A){% endmath %}, показывающая, что скорость работы алгоритма растет линейно с ростом кластеров и размера таблицы. Это делает алгоритм быстрым и эффективным на больших объемах.

Рассказав о реализации алгоритма, мы ничего не сказали о виде таблицы транзакций, чтобы можно было применять алгоритм CLOPE. CLOPE позволяет решать задачи кластеризации не только транзакционных данных, но и любых категорийных. Главное, чтобы все признаки объектов были измерены в шкале наименований.

Однако перед тем как запускать CLOPE, данные необходимо привести к нормализованному виду. Он может иметь вид бинарной матрицы образов, как в ассоциативных правилах, так и представлять собой взаимно однозначное отображение между множеством уникальных объектов {% math %}{u_1,…u_q}{% endmath %} таблицы и множеством целых чисел {% math %}{0,1,2,…,q-1}{% endmath %}.

## Задача о грибах

Задача о грибах (The mushroom dataset) – популярный тест, который применяют для оценки алгоритмов кластеризации категорийных наборов данных (доступен на [UCI machine learning repository](http://archive.ics.uci.edu/ml/index.php)). Тестовая выборка содержит 8124 записи с описанием 22 характеристик грибов двух классов: 4208 съедобных ({% math %}e{% endmath %}) и 3916 несъедобных ({% math %}p{% endmath %}) грибов. Файл выборки имеет следующий вид:

{% math %}p,x,s,n,t,p,f,c,n,k,e,e,s,s,w,w,p,w,o,p,k,s,u{% endmath %}

{% math %}e,x,s,y,t,a,f,c,b,k,e,c,s,s,w,w,p,w,o,p,n,n,g{% endmath %}

{% math %}e,b,s,w,t,l,f,c,b,n,e,c,s,s,w,w,p,w,o,p,n,n,m{% endmath %}

{% math %}p,x,y,w,t,p,f,c,n,n,e,e,s,s,w,w,p,w,o,p,k,s,u{% endmath %}

{% math %}e,x,s,g,f,n,f,w,b,k,t,e,s,s,w,w,p,w,o,e,n,a,g{% endmath %}

{% math %}..., ..., ...{% endmath %}

Общее количество уникальных характеристик объектов равно 116. 2480 записей имеют пропущенные значения в одном атрибуте. Описание набора данных — [https://archive.ics.uci.edu/ml/datasets/mushroom](https://archive.ics.uci.edu/ml/datasets/mushroom).

Если такой набор данных представить в описанном выше нормализованном виде, то получится 8124 транзакции, из которых 2408 будут длиной 21, а остальные – 22 элемента (пропущенные значения игнорируются). И теперь можно применить алгоритм CLOPE. Результат работы CLOPE при {% math %}r=2.6{% endmath %} для задачи о грибах после 1-ой итерации (фаза инициализации) представлен в таблице 1.

При этом критерием качества работы алгоритма служит количество «грязных» кластеров, т.е. таких, в которых присутствуют как съедобные ({% math %}e{% endmath %}), так и несъедобные ({% math %}p{% endmath %}) грибы. Чем меньше таких кластеров, тем лучше. Из таблицы 1 видно, что уже после 1-ой итерации остался только 1 «грязный» кластер №18. Потребуется еще пару-тройку сканирований базы данных для получения финальной кластеризации. Очевидно, что кластер 12 исчезнет.

Детальное исследование работы алгоритма CLOPE, проведенное его авторами, показало высокое качество кластеризации в сравнении с другими алгоритмами, в т.ч. иерархическими. При этом по скорости работы и производительности он обгоняет их в несколько раз.

Таблица 1: результат работы CLOPE после 1 итерации.

| CLUSTER | e | p |
|----------:|----------:|----------:|
| 1 |           | 256 |
| 2 | 512 |           |
| 3 | 768 |           |
| 4 | 96 |           |
| 5 | 96 |           |
| 6 | 192 |           |
| 7 | 1296 |           |
| 8 | 432 |           |
| 9 |           | 149 |
| 10 |           | 192 |
| 11 |           | 1146 |
| 12 |           | 1 |
| 13 |           | 288 |
| 14 | 192 |           |
| 15 |           | 223 |
| 16 | 48 |           |
| 17 |           | 72 |
| 18 | 48 | 32 |
| 19 |           | 8 |
| 20 |           | 8 |
| 21 |           | 1497 |
| 22 | 192 |           |
| 23 | 288 |           |
| 24 | 32 |           |
| 25 |           | 36 |
| 26 |           | 8 |
| 27 | 16 |           |
| Итого | 4208 | 3916 |

## Области применения CLOPE

Алгоритм CLOPE предназначен для работы с транзакционными данными, но, как мы увидели, очень много наборов данных с категорийными атрибутами представляют собой транзакционные данные либо сводятся к ним. Ответы респондента в анкете, список ключевых слов документа, множество посещенных веб-ресурсов пользователя, симптомы больного, характеристики гриба – все это не что иное, как транзакция. Поэтому области применения CLOPE распространяются на все массивы категорийных баз данных.

Вообще, кластеризация транзакционных данных имеет много общего с анализом ассоциаций. Обе эти технологии [Data Mining](https://wiki.loginom.ru/articles/data-mining.html) выявляют скрытые зависимости в наборах данных. Но есть и отличия. С одной стороны, кластеризация дает общий взгляд на совокупность данных, тогда как ассоциативный анализ находит конкретные зависимости между атрибутами. С другой стороны, ассоциативные правила сразу пригодны для использования, тогда как кластеризация чаще всего используется как первая стадия анализа.

В завершение подчеркнем преимущества алгоритма CLOPE:

1. Высокие масштабируемость и скорость работы, а так же качество кластеризации, что достигается использованием глобального критерия оптимизации на основе максимизации градиента высоты гистограммы кластера. Он легко рассчитывается и интерпретируется. Во время работы алгоритм хранит в RAM небольшое количество информации по каждому кластеру и требует минимальное число сканирований набора данных. Это позволяет применять его для кластеризации огромных объемов категорийных данных (large categorical data sets);
2. CLOPE автоматически подбирает количество кластеров, причем это регулируется одним единственным параметром – коэффициентом отталкивания.

## Литература

1. Yang, Y., Guan, H., You. J. CLOPE: A fast and Effective Clustering Algorithm for Transactional Data In Proc. of SIGKDD’02, July 23-26, 2002, Edmonton, Alberta, Canada.
2. Wang, K., Xu, C.. Liu, B. Clustering transactions using large items. In Proc. CIKM’99, Kansas, Missouri, 1999.
