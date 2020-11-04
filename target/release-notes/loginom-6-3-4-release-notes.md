---
publish: 2020-10-08
---

# Loginom 6.3.4 — Release Notes

_Several issues detected while the Reference Node operation were fixed in this version, the problem with the sudden Calculator error while operation in the parallel Loop was solved, the problem of lost links of packages with complex dependences was also fixed, some issues in the Web Client and Desktop application were resolved.  

## Fixes

**JavaScript:** when closing the wizard just after the start of any action in the wizard, the following error message was received: “Cannot read property 'hasFocus' of null” (#7475).

**JavaScript, JavaScript Calculator:** previously, it was possible to record an array in the variable field. In this case, such values could not be further used, and errors occurred. Now, if the Object  value returns, compulsory type cast is executed for it (using the object.valueOf() function). Previously, the null value returned (#7603).

**Loginom Server:** if a package from the package pool with reference to other packages was executed, and an error occurred during such package execution, some packages to which it had a reference were untimely closed that required fixes (#7811).

**REST request:** previously, linefeed (CR LF) was added at the end of each REST request but some web services that were waiting for a password at the request end, interpreted the linefeed as a password part that’s why now (CR LF) at the request end is not added (#7705).

**Web application, Desktop application:** the current tab was closed in some cases when Delete key pressing (#3587).

**Web application, Desktop application:** if there was no enough place for the window contents in the wizards on the display, scroll bars appeared that’s why it was not possible to configure some settings (#6124, #6189).

**Visualizers:** some applications were locked when attempting to activate visualizers connected to the nodes that could not be executed(#5753).

**Quality of the binary classification:** if From node settings option was selected for Cutoff, the count of correctly recognized values (TP and FP) was incorrectly calculated (#7730).

**Calculator:** when running the Calculator node in the Loop in the Parallel processing  mode, if the option of values caching was enabled, and the String or Variable type of values was used, the following fix could be done: "invalid argument" (#7726).

**Linear regression, Logistic regression:** if Explicitly set  option was selected in the wizard for the Reference value parameter, the following fix was required when resorting values without activation of input ports and subsequent selection of other field: “Index value of -1 of "Indexes" parameter is out of [0; -1] range”(#7073).

**Linear regression, Logistic regression:** upon the workflow saving and downloading at the Summary output port, the null values of Samples in the test set  and Samples in the validation set parameters were shown, even if not null sizes of these sets were set when saving (#7746).

**Linear regression, Neutral network (regression):** if Sequence was set as a Method of partition of training and text sets, when attempting to view data just after training, a fix was required(#7738).

**Connections to databases:** when jumping to the Connections page by the reference received using Share button, the following fix was required: "Cannot read property 'IRecordModel' of undefined" (#6157).

**Connection to databases:** if connection via Cloudera ODBC driver for Hive was used, the following fix was done when selecting any table from the list in the wizard: "Table or view not found: HIVE..default.pokes" (#7709).

**Connection to databases:** if connection via Cloudera ODBC driver for Hive was used, a fix was done when importing the struct or array fields (#7748).

**Supernode:** if the list of fields in the input or output port of the activated Supernode was changed while automapping, the Supernode derivatives were incorrectly marked as the changed ones (#5990).

**Login page:** when changing the scale on the login page, fixes could be required when page displaying, and the window could be completely locked that caused failure to login to the application (#7704, #7715, #7779).

**Reference Node:** the server could be frozen when starting the Supernode that contained circular dependence.  For example, if the Supernode containing the source node receiving the data from the Reference Node with reference to the same source node was run (#5197).

**Reference Node:** when collapsing the Reference Node to the Supernode, the links from its outputs to the external nodes were lost (#7759).

**XML generation:** if the maxOccurs parameter was set by the number exceeding 1 in the XSD schema, in the case of the repeated node start, the following fix could be required: “XSD schema does not allow to export  ... element to current XML document” (#7729).

**Export to Database:** while attempting to export the string guids (for example, the ones created in Calculator using the CreateGuid function) to the PostgreSQL database, the following fix was done: “the "guid" column has the uuid type, and the expression - character varying” (#7784).

**Core framework:** the following fix was required while attempting to add references to a package and its copy simultaneously: “{....-..-..-..-....} local component already exists in the identifier list”. Previously, it could also cause failure to access the derived components and nodes with the Public  access modifier  from the external packages (#7750).

## UI/UX  Improvement

**JavaScript:** the columns configuration page of the output table filled in using the code was renamed as follows: the old header is “Configure columns”, the new header is “Configure columns of JavaScript output table”. The header of the page with fields features configuration was renamed. The old header is “Fields features”, the new header is “Configure fields features” (#7721).

## Performance Improvements

**Import from ClickHouse:** memory consumption while data import from ClickHouse was optimized (#7727).
