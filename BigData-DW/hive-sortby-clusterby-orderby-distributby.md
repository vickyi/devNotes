## order by , sort by , distribute by , cluster by的区别

### order by

可以指定desc降序asc升序

 order by会对输入做全局排序，因此只有一个 reducer(多个 reducer无法保证全局有序)，然而只有一个 Reducer，会导致当输入规模较大时，消耗较长的计算时间。

### sort by

不是全局排序，其在数据进入 reducer前完成排序，因此，如果用 sort by进行排序并且设置 mapped. reduce. tasks〉1，则 sort by只会保证每个 reducer的输出有序，并不保证全局有序。

全排序实现:先用 sort by保证每个 reducer 输出有序，然后在进行 order by归并下前面所有的 reducer输出进行单个 reducer排序，则实现全局有序。

### distribute by

 distribute by是控制在map端如何拆分数据给 reduce端的。hive会根据 distribute by后面列，对应 reduce的个数进行分发，默认是采用hash算法。sort by为每个 reduce产生一个排序文件。在有些情况下，你需要控制某个特定行应该到哪个 reducer，这通常是为了进行后续的聚集操作。distribute by刚好可以做这件事。因此， distribute by经常和 sort by配合使用。

### cluster by

 cluster by具有 distribute by和 sort by的组合功能。但是排序只能是升序排序，不能指定排序规则为ASC或者DESC