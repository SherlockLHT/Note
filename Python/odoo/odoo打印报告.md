## report

使用 **QWeb** 实现报告打印

### 步骤一：

在模块目录下新建 **reports** 文件夹（文件夹名称随意），并新建报告xml文件，并将XML文件添加进 **\_\_mainfest\_\_.py** 文件的 **data** 列表中

### 步骤二：

在xml中添加 **report** 

```python
<odoo>
	<data>
    	<report id="model_report"
                model="test.model"
                string="测试模型"
                name="test.model_report_view"
                file="test.model_report_view"
                report_type="qweb-html" />
    </data>
</odoo>
```

**说明：**

1. id可随意，唯一即可；
2. model表示绑定的模型名，即数据模型的 _name；
3. string表示此报告名称，可以通过此名称在设置界面找到此报告的配置，进行修改；
4. name表示指向的报告模板，格式为“模型名.模板id”；
5. file表示模板文件名，但是没发现有什么用；
6. report_type表示报告的类型，有 **qweb-pdf** 和**qweb-html** 两种；

### 步骤三

添加报告模板

**template** 可以和步骤二的report放在一起，也可以另外放在一个xml中，只要xml文件加入**\_\_mainfest\_\_.py** 文件的 **data** 列表中即可

```python
<?xml version="1.0" encoding="utf-8" ?>
<odoo>
    <data>
    	<report id="model_report"
                model="test.model"
                string="测试模型"
                name="test.model_report_view"
                file="test.model_report_view"
                report_type="qweb-html" />
                
        <template id="into_apply_report_view">
			<t t-call="report.html_container">
                <t t-foreach="docs" t-as="data">
                    <t t-call="report.external_layout">
                        <div class="page">
                            <style type="text/css">
                                .title{
                                    text-align: center;
                                }
                                table{
                                    margin: 0 auto;
                                    width: 900px;
                                    text-align: center;
                                    font-size: 16px;
                                    border-top: 2px solid black;
                                    border-bottom: 2px solid black;
                                }
                            </style>
                            <h2 class="title">测试报告</h2>
                            <table>
                                <tr>
                                    <th>所属组织：</th>
                                    <td><span t-field="data.pk_org_id"></span></td>
                                    <th>所属管廊：</th>
                                    <td><span t-field="data.pk_pipeline_id"></span></td>
                                </tr>
                            </table>
                            <div class="media-list image-list">
                                <t t-foreach="data.attachment_ids" t-as="image">
                                    <img class="media-object" t-att-src="'data:image/png;base64,%s' % image.datas" />
                                </t>
                            </div>
                        </div>
                    </t>
                </t>
            </t>
        </template>
    </data>
</odoo>
```

**说明：**

1. template的id要和report上面指定的相同；
2. t-foreach可用作遍历数据，t-as表示每个遍历的数据，docs表示所有数据；
3. 自定义的css可以放在template中的div下；
4. 可以使用t-att-src来显示附件图片（ir.attachement）；