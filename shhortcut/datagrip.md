写完sql语句后，可以选中，电子左上侧绿色箭头执行

![图片](asserts/datagrip/640)

也可以使用快捷键Ctrl+Enter，选中情况下，会直接执行该sql，未选中情况下，如果控制台中有多条sql，会提示你要执行哪条sql。之前习惯了dbvisualizer中的操作，dbvisualizer中光标停留在当前sql上(sql以分号结尾)，按下Ctrl+.快捷键会自动执行当前sql，其实DataGrip也能设置，在setting->Database-General中

![图片](asserts/datagrip/640)

语句执行时默认是提示，改成smallest statement后，光标停留在当前语句时，按下Ctrl+Enter就会直接执行当前语句。

语句的执行结果在底部显示

![图片](asserts/datagrip/640)

如果某列的宽度太窄，可以鼠标点击该列的任意一个，使用快捷键Ctrl+Shift+左右箭头可以调整宽度，如果要调整所有列的宽度，可以点击左上角红框部分，选择所有行，使用快捷键Ctrl+Shift+左右箭头调整

添加行、删除行也很方便，上部的+、-按钮能直接添加行或删除选中的行，编辑列同样也很方便，双击要修改的列，输入修改后的值，鼠标在其他部分点击就完成修改了

![图片](asserts/datagrip/640)

有的时候我们要把某个字段置为null，不是空字符串""，DataGrip也提供了渐变的操作，直接在列上右键，选择set null

![图片](asserts/datagrip/640)

对于需要多窗口查看结果的，即希望查询结果在新的tab中展示，可以点击pin tab按钮，那新查询将不会再当前tab中展示，而是新打开一个tab

![图片](asserts/datagrip/640)

旁边的output控制台显示了执行sql的日志信息，能看到sql执行的时间等信息

![图片](asserts/datagrip/640)





**「1、关键字导航：」**

当在datagrip的文本编辑区域编写sql时，按住键盘Ctrl键不放，同时鼠标移动到sql关键字上，比如表名、字段名称、或者是函数名上，鼠标会变成手型，关键字会变蓝，并加了下划线，点击，会自动定位到左侧对象树，并选中点击的对象

![图片](asserts/datagrip/640)



**「5、导航到关联数据」**

表之间会有外检关联，查询的时候，能直接定位到关联数据，或者被关联数据，例如user1表有个外检字段classroom指向classroom表的主键id，在查询classroom表数据的时候，可以在id字段上右键，go to，referencing data

![图片](asserts/datagrip/640)

选择要显示第一条数据还是显示所有数据

![图片](asserts/datagrip/640)

会自动打开关联表的数据

![图片](asserts/datagrip/640)

相反，查询字表的数据时，也能自动定位到父表

**「7、行转列」**

对于字段比较多的表，查看数据要左右推动，可以切换成列显示，在结果集视图区域使用Ctrl+Q快捷键

![图片](asserts/datagrip/640)

1、变量重命名

鼠标点击需要重命名的变量，按下Shift+F6快捷键，弹出重命名对话框，输入新的名称

![图片](asserts/datagrip/640)

2、自动检测无法解析的对象

如果表名、字段名不存在，datagrip会自动提示，此时对着有问题的表名或字段名，按下Alt+Enter，会自动提示是否创建表或添加字段

![图片](asserts/datagrip/640)

3、权限定字段名

对于查询使用表别名的，而字段中没有使用别名前缀的，datagrip能自动添加前缀，鼠标停留在需要添加别名前缀的字段上，使用Alt+Enter快捷键

![图片](asserts/datagrip/640)

4、*通配符自动展开

查询的时候我们会使用select _查询所有列，这是不好的习惯，datagrip能快速展开列，光标定位到_后面，按下Alt+Enter快捷键

![图片](asserts/datagrip/640)