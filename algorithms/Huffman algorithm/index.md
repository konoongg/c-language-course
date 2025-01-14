# Алгоритм Хаффмана
### Где используется алгоритм Хаффмана и что это вообще такое
____

Алфавитное неравномерное двоичное кодирование - кодирование при котором символы некоторого первичного алфавита кодируются комбинациями символов двоичного алфавита 
(т.е. 0 и 1), причем, длина кодов и, соответственно, длительность передачи отдельного кода, могут различаться.

Префиксный код в теории кодирования — код со словом переменной длины, имеющий такое свойство (выполнение условия Фано): \
если в код входит слово a, то для любой непустой строки b слова ab в коде не существует.
Хотя префиксный код состоит из слов разной длины, эти слова можно записывать без разделительного символа.

>Например, код, состоящий из слов 0, 10 и 11, является префиксным, и сообщение 01001101110 можно разбить на слова единственным образом:

>>0 10 0 11 0 11 10

>Код, состоящий из слов 0, 10, 11 и 100, префиксным не является, и то же сообщение можно трактовать несколькими способами.

  >>0 10 0 11 0 11 10
  
  >>0 100 11 0 11 10
  
Алгоритм Хаффмана — алгоритм оптимального префиксного кодирования алфавита с минимальной избыточностью.Кодирование Хаффмана широко применяется при сжатии данных,
в том числе при сжатии фото и видеоизображений (JPEG, MPEG), в популярных архиваторах (PKZIP, LZH и др.),
в протоколах передачи данных HTTP (Deflate), MNP5 и MNP7 и других.

### На чем основан алгоритм Хаффмана
____
Идея, положенная в основу кодировании Хаффмана, основана на частоте появления символа в последовательности.
Символ,который встречается в последовательности чаще всего, получает новый очень маленький код,
а символ, который встречается реже всего, получает, наоборот, очень длинный код.
Это нужно, так как мы хотим, чтобы, когда мы обработали весь ввод, самые частотные символы заняли меньше всего места (и меньше, чем они занимали в оригинале),
а самые редкие — побольше (но так как они редкие, это не имеет значения).

Для этого алгоритма вам потребуется минимальное понимание устройства бинарного дерева и очереди с приоритетами.
Если вы по какой-то причине еще не знакомы с деревьями, то самое время.

![image](https://user-images.githubusercontent.com/120199924/219334152-b171b770-a30d-416f-a169-7be449d2d40d.png)

Предположим, у нас есть строка «beep boop beer!», для которой, в её текущем виде, на каждый знак тратится по одному байту.
Это означает, что вся строка целиком занимает 15*8 = 120 бит памяти. После кодирования строка займёт 40 бит.

Чтобы лучше понять пример, мы для начала сделаем всё вручную. Строка «beep boop beer!» для этого очень хорошо подойдёт.
Чтобы получить код для каждого символа на основе его частотности, нам надо построить бинарное дерево, такое,
что каждый лист этого дерева будет содержать символ (печатный знак из строки). Дерево будет строиться от листьев к корню, в том смысле,
что символы с меньшей частотой будут дальше от корня, чем символы с большей. Скоро вы увидите, для чего это нужно.

Чтобы построить дерево, мы воспользуемся слегка модифицированной очередью с приоритетами — первыми из неё будут извлекаться элементы с наименьшим приоритетом,
а не наибольшим. Это нужно, чтобы строить дерево от листьев к корню.

Для начала посчитаем частоты всех символов:

| символ | частота |
|----------------|---------|
|'b'|3|
|'e' |4|
|'p'|2|
|''|2|
|'o'|2|
|'r'|1|
|'!'|1|

После вычисления частот мы создадим узлы бинарного дерева для каждого знака и добавим их в очередь, используя частоту в качестве приоритета:

![image](https://user-images.githubusercontent.com/120199924/219336054-010b2f0f-470c-45a8-868a-1cc37bfa07ee.png)

Теперь мы достаём два первых элемента из очереди и связываем их, создавая новый узел дерева, в котором они оба будут потомками,
а приоритет нового узла будет равен сумме их приоритетов. После этого мы добавим получившийся новый узел обратно в очередь.

![image](https://user-images.githubusercontent.com/120199924/219336315-66ba0499-f4ac-4b74-80a7-18b13a6ee274.png)

Повторим те же шаги и получим последовательно:

![image](https://user-images.githubusercontent.com/120199924/219336433-3c1700a3-5eb7-4952-94ee-ec7291d64cfb.png)

![image](https://user-images.githubusercontent.com/120199924/219336802-0b6a1de1-1cff-4f76-923a-71b7a9e53aa5.png)

![image](https://user-images.githubusercontent.com/120199924/219336839-dfaece7a-31be-4ef5-a979-56999a6179df.png)

![image](https://user-images.githubusercontent.com/120199924/219336883-d90e1cb7-53c5-4f04-a347-1169c8144cca.png)

Ну и после того, как мы свяжем два последних элемента, получится итоговое дерево:

![image](https://user-images.githubusercontent.com/120199924/219336972-7cf93c7d-7214-4cc1-8de9-829b78b5e078.png)

Теперь, чтобы получить код для каждого символа, надо просто пройтись по дереву, и для каждого перехода добавлять 0, если мы идём влево, и 1 — если направо:

![image](https://user-images.githubusercontent.com/120199924/219337118-6544787a-c59f-452a-b092-3bc286a6daa1.png)


Если мы так сделаем, то получим следующие коды для символов:

| символ | код |
|----------------|---------|
|'b'|00|
|'e' |11|
|'p'|101|
|''|011|
|'o'|010|
|'r'|1000|
|'!'|1001|



Чтобы расшифровать закодированную строку, нам надо, соответственно, просто идти по дереву, сворачивая в соответствующую каждому биту сторону до тех пор,
пока мы не достигнем листа. 
>Например, если есть строка «101 11 101 11» и наше дерево, то мы получим строку «pepe».

Важно иметь в виду, что каждый код не является префиксом для кода другого символа. 
>В нашем примере, если 00 — это код для 'b', то 000 не может оказаться чьим-либо кодом, потому что иначе мы получим конфликт. 
>Мы никогда не достигли бы этого символа в дереве, так как останавливались бы ещё на 'b'.

На практике, при реализации данного алгоритма сразу после построения дерева строится таблица Хаффмана.
Данная таблица — это по сути связный список или массив, который содержит каждый символ и его код, потому что это делает кодирование более эффективным.
Довольно затратно каждый раз искать символ и одновременно вычислять его код, так как мы не знаем, где он находится, и придётся обходить всё дерево целиком.
Как правило, для кодирования используется таблица Хаффмана, а для декодирования — дерево Хаффмана.

>Входная строка: «beep boop beer!»
>Входная строка в бинарном виде: «0110 0010 0110 0101 0110 0101 0111 0000 0010 0000 0110 0010 0110 1111 0110 1111 0111 0000 0010 0000 0110 0010 0110 0101 0110 0101 0111 0010 0010 000»

>Закодированная строка: «0011 1110 1011 0001 0010 1010 1100 1111 1000 1001»
Как вы можете заметить, между ASCII-версией строки и закодированной версией существует большая разница.


### Алгоритм построения бинарного кода Хаффмана (обобщение примера, приведенного выше)
____

![image](https://user-images.githubusercontent.com/120199924/219339153-2857427f-7a8b-4ee6-9d5c-1229645f5f65.png)
(под весом имеют в виду частоту символа)

Построение кода Хаффмана сводится к построению соответствующего бинарного дерева по следующему алгоритму:

1)Составим список кодируемых символов, при этом будем рассматривать один символ как дерево, состоящее из одного элемента c весом, равным частоте появления символа
в строке.
2)Из списка выберем два узла с наименьшим весом.
3)Сформируем новый узел с весом, равным сумме весов выбранных узлов, и присоединим к нему два выбранных узла в качестве детей.
4)Добавим к списку только что сформированный узел вместо двух объединенных узлов.
5)Если в списке больше одного узла, то повторим пункты со второго по пятый.

Если сортировать элементы после каждого суммирования или использовать приоритетную очередь, то алгоритм будет работать за время O(NlogN).
Такую асимптотику можно улучшить до O(N), используя обычные массивы.

### Адаптивный алгоритм Хаффмана
____
Адаптивное кодирование Хаффмана (также называемое динамическое кодирование Хаффмана) — адаптивный метод, основанный на кодировании Хаффмана.
Он позволяет строить кодовую схему в поточном режиме (без предварительного сканирования данных), не имея никаких начальных знаний из исходного распределения,
что позволяет за один проход сжать данные. Преимуществом этого способа является возможность кодировать на лету.

Существует несколько реализаций этого метода, наиболее примечательными являются «FGK» (ФГК: Фоллер, Галлагер и Кнут) и алгоритм Виттера.

#### FGK алгоритм
____
Он позволяет динамически регулировать дерево Хаффмана, не имея начальных частот.
В ФГК дереве Хаффмана есть особый внешний узел, называемый 0-узел, используемый для идентификации входящих символов.
То есть, всякий раз, когда встречается новый символ — его путь в дереве начинается с нулевого узла.
Самое важное — то, что нужно усекать и балансировать ФГК дерево Хаффмана при необходимости, и обновлять частоту связанных узлов.
Как только частота символа увеличивается, частота всех его родителей должна быть тоже увеличена.
Это достигается путём последовательной перестановки узлов, поддеревьев или и тех и других.
Важной особенностью ФГК дерева является принцип братства (или соперничества):
каждый узел имеет два потомка (узлы без потомков называются листами) и веса идут в порядке убывания.
Благодаря этому свойству дерево можно хранить в обычном массиве, что увеличивает производительность.

#### Алгоритм Виттера
____

Код представляется в виде древовидной структуры, в которой каждый узел имеет соответствующий вес и уникальный номер.
Цифры идут вниз, и справа налево. Веса должны удовлетворять принципу братства. Таким образом, если А является родительским узлом B и C является потомком B, то
то (W-вес) W(А) > W(B) > W(C).

Набор узлов с одинаковыми весами представляют собой блок.
Чтобы получить код для каждого узла, в случае двоичного дерева мы могли бы просто пройти все пути от корня к узлу, записывая, например,
1 если мы идем направо, и 0 если мы пойдем налево.
Также в этом алгоритме используется специальный лист, NYT, из которого «растут» новые, ранее не встречавшиеся, символы.
Кодер и декодер начинают только с корневого узла, который имеет максимальный вес. В начале это и есть наш NYT узел.
Когда мы передаем NYT символ, мы должны передать вначале код самого узла, а затем данные.
Для каждого символа, который уже находится в дереве, мы должны только передавать код конечных узлов (листов).
Для каждого передающегося символа передатчик и приёмник выполняют процедуру обновления:

1)Если текущий символ является не встречавшимся — добавить к NYT два дочерних узла: один для следующего NYT, другой для символа.
Увеличить вес нового листа и старого NYT и переходить к шагу 4. Если текущий символ является не NYT, перейти к листу символа.
2)Если этот узел не имеет наибольший вес в блоке, поменять его с узлом, имеющим наибольшее число, за исключением, если этот узел является родительским элементом
3)Увеличение веса для текущего узла
4)Если это не корневой узел зайти в родительский узел затем перейдите к шагу 2. Если это корень, окончание.

Примечание: замена узлов означает замену весов и соответствующих символов, но не чисел.

##### знаю, алгоритм звучит совсем немного непонятно, поэтому разберем на примере:

![image](https://user-images.githubusercontent.com/120199924/219344725-96e30712-2e3a-427f-b5f4-cc299ca60a44.png)

Начинаем с пустого дерева. Для «a» передаём его двоичный код. 
NYT порождает два дочерних узла: 254 и 255. Увеличиваем вес корня. Код «a», связанный с узлом 255, становится 1.
Для «b» передается 0 (код NYT узла), затем его двоичный код. 
NYT порождает два дочерних узла: 252 для NYT и 253 для b. Увеличиваем веса 253, 254 и корня. Код для «b» равен 01.
Для следующего «b» передаётся 01.
Идём в лист 253. У нас есть блок весов в 1 и наибольшее число в блоке 255, так что меняем веса и символы узлов 253 и 255, увеличиваем вес,
идём в корень и увеличиваем вес корня.
В будущем код «b» — это 1, а для «a» — это теперь 01, который отражает их частоту.

надеюсь, что стало немного понятнее)


### полезные ссылки на эту тему( если интересует подробнее)
____
1) https://habr.com/ru/post/144200/
2) https://clck.ru/FNNhW
3) https://clck.ru/33Z6Am
4) https://clck.ru/33Z6n7
5) https://clck.ru/asfsz

