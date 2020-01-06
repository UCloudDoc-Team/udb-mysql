# 如何修改仪表盘

由于CloudDBA默认提供的仪表盘是只读的，不能修改，如果要自定义仪表盘的图表，需要创建一个新的仪表盘然后添加相关图表。

## 创建可修改的仪表盘

如果是第一次创建自定义仪表盘，可以自己从头开始创建。但是更加推荐在Grafana现有的仪表盘上进行修改。下面讲述了如何复制一个现有的仪表盘。

选择要复制的dashboard，点击右上角share dashboard

![image](/images/clouddba202001.png)

点击”Export”

![image](/images/clouddba202002.png)

点击”View JSON”

![image](/images/clouddba202003.png)

点击”Copy to Clipboard”

![image](/images/clouddba202004.png)

在主界面，鼠标移到”+”，会出现二级菜单，选择”Import”

![image](/images/clouddba202005.png)

在文本框中粘体前面复制的JSON内容，点击”Load”

![image](/images/clouddba202006.png)

然后会出现这样的界面：

![image](/images/clouddba202007.png)

在Name选择另一个自己的名字，并且删除Unique identifier的值

![image](/images/clouddba202008.png)
![image](/images/clouddba202009.png)

做完之后点击Import (Overwrite)
然后新的仪表盘就创建好了

![image](/images/clouddba202010.png)

## 在可修改的仪表盘上创建相关图表
当有了一个可以修改的仪表盘之后，就可以在上面自定义图表了。下面是相关步骤。
进入你创建的可以修改的仪表盘之后，点击右上角”Add Panel”

![image](/images/clouddba202011.png)

下图是创建出来的仪表盘，可以根据需要调整大小和位置。但是更加重要的是设置要展示的数据。

![image](/images/clouddba202012.png)

点击”Add Query”添加展示数据

![image](/images/clouddba202013.png)

在Metrics输入要查询的Prometheus语句，获取相应图表

![image](/images/clouddba202014.png)

为了获得数据对应的UDB信息，一般会加上如下图这一段的代码：
```
some_metric and on(instance) udb_info{udb_id="$udb_id"}
```
同时，在”Legend”能够直观展示具体的指标含义

![image](/images/clouddba202015.png)

图表中可以不止展示一个数据，可以几个数据在一个图表一同展示。点击”Add Query”会出现一个新的数据源的输入控件。在里面可以输入另一个指标的数据以及输入指标含义

![image](/images/clouddba202016.png)

数据准备好之后，在第二个Visualization标签可以设置图表外观。在第三个General标签可以设置图表的标题。修改完之后，上部分的图表标题同步修改。如下图所示

![image](/images/clouddba202017.png)

所有都完成后，点击左上角返回仪表盘

![image](/images/clouddba202018.png)

这时就可以看到编辑过后的图表：

![image](/images/clouddba202019.png)

但是此时这个图表还没有被保存。这时需要点击右上角”Save dashboard”，把所有添加的图表都保存到Grafana配置中。

![image](/images/clouddba202020.png)

至此，相关仪表盘的修改已经保存，下次登陆也能看到修改的内容。
