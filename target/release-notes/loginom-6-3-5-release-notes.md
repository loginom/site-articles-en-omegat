---
publish: 2020-11-03
---

# Loginom 6.3.5 — Release Notes

_Several issues detected while operation of web-services and databases were fixed in this version, the problem connected with the usage of the JSDoc comments in the JavaScript wizard was solved, other components were also corrected._

## Fixes

[**JavaScript, Calculator (JavaScript):**](https://help.loginom.ru/userguide/processors/transformation/calc/javascript.html) the web application could be frozen if the JSDoc comments were used in the JavaScript wizard, and the name of the function that contained such comment was entered, or the lookup wizard was called (*Ctrl* + *Space*). Such problem could also arise when writing the module import row if the imported module contained the JSDoc comments (#7833).

[**JavaScript:**](https://help.loginom.ru/userguide/processors/transformation/java-script) if the resulting table contained too large (several thousands) row count in the code editor when previewing, the following error could occur: "Runtime is disabled" (#8048).

**Web application:** when adding link and creating a new port at the same time (using "+" port of Join, Enrich Data components, etc), the following error could sometimes occur: "Failed to create new port ObjectId value is not cached" (#8039).

[**Grouping, Table into Variables:**](https://help.loginom.ru/userguide/processors/variables/variables-from-table.html) if there was a lack of memory during the workflow execution, the following uninformative errors could occur: "Error while node activation..." (#8052).

[**Replace:**](https://help.loginom.ru/userguide/processors/transformation/substitution/) when connecting several replacement tables, the data only from the first table was used (#7872).

[**Import from Excel:**](https://help.loginom.ru/userguide/integration/import/excel.html) in the case of the repeated wizard opening, the data was not updated in the preview window even if the source file had been changed (#6088).

**Import from Excel:** the data from the xlsx files was not loaded if there was no information on the data range in the metadata, and the data was not displayed in the wizard preview window. The files with incomplete metadata can be generated when exporting from the third-party applications, for example, 1C or Google Docs (#6684, #7745).

[**Import from Database:**](https://help.loginom.ru/userguide/integration/import/database.html) if the row that was lower than the first data page was selected in the import wizard in the preview window, in the case of the repeated opening, the preview window could be empty, and to display the data, it was required to scroll up the view window or to open the preview once again (#8044).

[**Calculator:**](https://help.loginom.ru/userguide/processors/transformation/calc/) if *Null* was the first argument to be moved to *DateTimeToStr* function, the error occurred in the function, and calculation of the expression in which the function was used was stopped, and error message was written in the log. Now, in such case, *DateTimeToStr* function returns *Null* (#7969).

[**Binning:**](https://help.loginom.ru/userguide/processors/preprocessing/quantization.html) if the binning bin count was set by means of the control variable, and there was an unused column before the column mapped with the variable, the variable mapping was cancelled when saving and repeated opening of the package (#5163, #6281).

**Binning:** if upon the method settings change (for example, bin count), the bitness configuration displayed in the numerical table was changed, the earlier defined method settings were reset (#7848).

[**Coarse сlasses:**](https://help.loginom.ru/userguide/visualization/fine-classes/) the following error occurred when joining the fine classes in the wizard by dragging the class bound: "Cannot read property 'element' of undefined" (#7504).

**Coarse сlasses:** an error occurred if sorting by means of three-time pressing the header was cancelled in the fields tables and coarse classes (#7889).

[**Logistic regression:**](https://help.loginom.ru/userguide/processors/datamining/logistic-regression/) the following error occurred in the Internet Explorer browser when selecting the validation method: "Failed to get "setWidth" property of the reference the value of which is not defined or is NULL" (#7659).

**Adjustment of Variables Mapping:** if one of the selected records was removed using the recycle bin icon, the following error occurred when attempting to delete all selected records calling the contex menu item or pressing *Delete*: "No extension for {...-..-..-..-...} base identifier found" (#7843).

**Adjustment of Columns Mapping:** in the case of multiple deleting of used output fields, the application processed the request for a long period of time and separately requested a confirmation of deletion of each field (#7921).

[**Connections:**](https://help.loginom.ru/userguide/integration/connections/) if the URL service value was set by means of a variable, the following error occurred during the inintial setting at the stage of connection testing or verification: "Service URL not specified" (#7982).

[**Supernode:**](https://help.loginom.ru/userguide/processors/control/submodel.html) if the node receiving data from the Node execution node referring to the Supernode was activated, an error occurred when attempting to move the source Supernode into another Supernode (#7901).

**Supernode:** when attempting to move several nodes of complexly connected nodes, the following error could occur: "Workflow contains 1 locked node". For example, such error could occur when moving the nodes connected by means of the Reference Node that is inside the Supernode (#7977).

[**Derived component:**](https://help.loginom.ru/userguide/scenario/derived-component.html) if Derived Component (№1) was deleted, Derived Node (№1) of which was the Base one for other Derived Component (№2), when attempting to create Derived Node (№2), uninformative error occurred (#7993).

**Expand/Collapse Supernode:** if the derived component was created inside the Supernode, and then all nodes were deleted inside the Supernode, when expanding such Supernode, the earlier created Derived Component was deleted. Now, when expanding the supernode, the Derived Component is moved one level up from the supernode (#7923).

[**Join:**](https://help.loginom.ru/userguide/processors/transformation/addition.html) when joining a table and variables, "Row count complies" setting was incorrectly processed with the following value: *Data set defined*. One row was always transferred to the output (#7945).

[**Сортировка:**](https://help.loginom.ru/userguide/processors/transformation/sorting.html) на странице *Сортировка* мастера настройки теперь поля пропадают из списка *Доступные поля* при добавлении их в список *Поля сортировки*; ранее поля оставались в списке доступных, что могло приводить к ошибкам, если ранее добавленное в список сортируемых поле переносилось в список повторно при помощи Drag&Drop (#7831).

[**Статистика:**](https://help.loginom.ru/userguide/visualization/statistics/) для строковых полей в визуализаторе Статистика были перепутаны текстовые подсказки для столбцов *Минимум* и *Максимум* (отображались, соответственно «Наибольшая длина строки:» и «Наименьшая длина строки:») (#7647).

**Статистика:** сортировка уникальных значений в панели гистограммы визуализатора Статистика не совпадала с порядком в других визуализаторах (#7679).

**Статистика:** при сортировке в таблице уникальных значений по полю *Кол-во* большого количества записей (несколько тысяч и более), значения сортировались как строки, а при отмене сортировки таблица уникальных значений оставалась пустой (#7829, #7830).

[**Таблица:**](https://help.loginom.ru/userguide/visualization/table/) доступные в детализации Таблицы пункты меню «Переместить вверх» и «Переместить вниз» работали некорректно (#5760).

[**Фильтр строк:**](https://help.loginom.ru/userguide/processors/transformation/row-filter/) если запросить список уникальных значений у предварительно настроенного фильтра с типом условия «В списке», то в редких случаях возникала ошибка "Cannot read property 'getRange' of null" (#4461, #5598, #6436).

**Фильтр строк:** если был выбран тип данных *Дата/Время* и один из типов условия *(не) первый / (не) последний*, то не сохранялась настройка «Тип временного промежутка» *День*, а также после сохранения и повторного открытия пакета сбрасывалось значение параметра «Базовая дата интервала» (#7893).

[**Формирование XML:**](https://help.loginom.ru/userguide/processors/integration/formation-xml.html) если элемент XSD-схемы содержал несколько элементов с *maxOccurs > 1*, то при повторном запуске узла возникала ошибка (#7932).

[**Экспорт в Базу данных Firebird:**](https://help.loginom.ru/userguide/integration/connections/list/firebird.html) не экспортировались строковые данные в локальную базу данных Firebird если экспортированный набор данных имел более одной строки (#7839).

## Оптимизация работы

[**Конечные классы:**](https://help.loginom.ru/userguide/visualization/fine-classes/) оптимизировано потребление оперативной памяти при использовании визуализатора «Конечные классы» (#7924).