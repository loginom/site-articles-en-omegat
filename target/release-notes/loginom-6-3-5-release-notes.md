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

[**Binning:**](https://help.loginom.ru/userguide/processors/preprocessing/quantization.html) if the binning bin count was set by means of the control variable, and there was an unused column before the column mapped with the variable, the variable mapping was reset when saving and repeated opening of the package (#5163, #6281).

**Binning:** if upon the method settings change (for example, bin count), the bitness configuration displayed in the numerical table was changed, the earlier defined method settings were reset (#7848).

[**Coarse сlasses:**](https://help.loginom.ru/userguide/visualization/fine-classes/) the following error occurred when joining the fine classes in the wizard by dragging the class bound: "Cannot read property 'element' of undefined" (#7504).

**Coarse сlasses:** an error occurred if sorting by means of three-time pressing on the header was cancelled in the fields tables and coarse classes (#7889).

[**Logistic regression:**](https://help.loginom.ru/userguide/processors/datamining/logistic-regression/) the following error occurred in the Internet Explorer browser when selecting the validation method: "Failed to get "setWidth" property of the reference the value of which is not defined or is NULL" (#7659).

**Adjustment of Variables Mapping:** if one of the selected records was removed using the recycle bin icon, the following error occurred while attempting to delete all selected records calling the context menu item or pressing *Delete*: "No extension for {...-..-..-..-...} base identifier found" (#7843).

**Adjustment of Columns Mapping:** in the case of multiple deleting of used output fields, the application processed the request for a long period of time and separately requested a confirmation of deletion of each field (#7921).

[**Connections:**](https://help.loginom.ru/userguide/integration/connections/) if the URL service value was set by means of a variable, the following error occurred during the initial setting at the stage of connection testing or verification: "Service URL not specified" (#7982).

[**Supernode:**](https://help.loginom.ru/userguide/processors/control/submodel.html) if the node receiving data from the Node execution node referring to the Supernode was activated, an error occurred while attempting to move the source Supernode into another Supernode (#7901).

**Supernode:** while attempting to move several nodes of complexly connected nodes, the following error could occur: "Workflow contains 1 locked node". For example, such error could occur when moving the nodes connected by means of the Reference Node that is inside the Supernode (#7977).

[**Derived component:**](https://help.loginom.ru/userguide/scenario/derived-component.html) if Derived Component (№1) was deleted, Derived Node (№1) of which was the Base one for other Derived Component (№2), while attempting to create Derived Node (№2), uninformative error occurred (#7993).

**Expand/Collapse Supernode:** if the derived component was created inside the Supernode, and then all nodes were deleted inside the Supernode, when expanding such Supernode, the earlier created Derived Component was deleted. Now, when expanding the Supernode, the Derived Component is moved one level up from the Supernode (#7923).

[**Join:**](https://help.loginom.ru/userguide/processors/transformation/addition.html) when joining a table and variables, "Row count complies" setting was incorrectly processed with the following value: *Data set defined*. One row was always transferred to the output (#7945).

[**Sort:**](https://help.loginom.ru/userguide/processors/transformation/sorting.html) now the fields disappear from the *Available fields* list on *Sort* wizard page when adding them to *Sort fields* list. Previously, the fields were in the list of the available ones that could cause errors if the field earlier added to the list of the sorted fields was repeatedly moved to the list using Drag&Drop (#7831).

[**Statistics:**](https://help.loginom.ru/userguide/visualization/statistics/) text tips were confused for *Minimum* and *Maximum* columns ("Maximum string length:" and "Minimum string length:" were displayed, correspondingly) for string fields in the Statistics visualizer (#7647).

**Statistics:** sorting of the unique values on the histogram panel of the Statistics visualizer didn't match the order in other visualizers (#7679).

**Statistics:** when sorting large number of records (several thousands and more) in the table of the unique values by the *Count* field, the values were sorted as strings, and when cancelling sorting, the table of the unique values stayed empty (#7829, #7830).

[**Table:**](https://help.loginom.ru/userguide/visualization/table/) "Move up" and "Move down" menu items available in the Table detailing functioned incorrectly (#5760).

[**Row filter:**](https://help.loginom.ru/userguide/processors/transformation/row-filter/) the following error seldom occurred if the unique values list was requested for the preset filter with "Listed" condition type: "Cannot read property 'getRange' of null" (#4461, #5598, #6436).

**Row filter:** if the *Date/Time* data type and one of *(not) first / (not) last* condition types were selected, "Time span unit" *Day* setting was not saved, and just after saving and repeated opening of the package, the value of the "Base interval date" parameter was reset (#7893).

[**XML Generation:**](https://help.loginom.ru/userguide/processors/integration/formation-xml.html) if the XSD schema item contained several items with *maxOccurs > 1*, an error occurred after the repeated start of the node (#7932).

[**Export to Firebird Database:**](https://help.loginom.ru/userguide/integration/connections/list/firebird.html) the string data was not exported to the local Firebird database if the exported data set contained more than one string (#7839).

## Performance Improvements

[**Coarse сlasses:**](https://help.loginom.ru/userguide/visualization/fine-classes/) memory consumption when using the "Coarse classes" visualizer was optimized (#7924).