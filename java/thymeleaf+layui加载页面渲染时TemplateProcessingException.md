# thymeleaf+layui加载页面渲染时TemplateProcessingException: 

```java
Could not parse as expression: "
Caused by: org.attoparser.ParseException: Could not parse as expression: "
                    {type: 'numbers'},
                    {field: 'name', title: 'name'},
                    {field: 'id', title: 'id'},
                    {field: 'sex', title: 'sex'},
                    {field: 'pid', title: 'pid'}
                	" (template: "backadmin/menu/menuIndex" - line 69, col 25)
	at org.attoparser.MarkupParser.parseDocument(MarkupParser.java:393)
	at org.attoparser.MarkupParser.parse(MarkupParser.java:257)
	at org.thymeleaf.templateparser.markup.AbstractMarkupTemplateParser.parse(AbstractMarkupTemplateParser.java:230)
	... 53 common frames omitted
Caused by: org.thymeleaf.exceptions.TemplateProcessingException: Could not parse as expression: "
                    {type: 'numbers'},
                    {field: 'name', title: 'name'},
                    {field: 'id', title: 'id'},
                    {field: 'sex', title: 'sex'},
                    {field: 'pid', title: 'pid'}
                	" (template: "backadmin/menu/menuIndex" - line 69, col 25)
	at org.thymeleaf.standard.expression.StandardExpressionParser.parseExpression(StandardExpressionParser.java:131)
	at org.thymeleaf.standard.expression.StandardExpressionParser.parseExpression(StandardExpressionParser.java:62)
	at org.thymeleaf.standard.expression.StandardExpressionParser.parseExpression(StandardExpressionParser.java:44)
	at org.thymeleaf.engine.EngineEventUtils.parseAttributeExpression(EngineEventUtils.java:220)
	at org.thymeleaf.engine.EngineEventUtils.computeAttributeExpression(EngineEventUtils.java:207)
	at org.thymeleaf.standard.processor.AbstractStandardExpressionAttributeTagProcessor.doProcess(AbstractStandardExpressionAttributeTagProcessor.java:125)
	at org.thymeleaf.processor.element.AbstractAttributeTagProcessor.doProcess(AbstractAttributeTagProcessor.java:74)
	at org.thymeleaf.processor.element.AbstractElementTagProcessor.process(AbstractElementTagProcessor.java:95)
	at org.thymeleaf.util.ProcessorConfigurationUtils$ElementTagProcessorWrapper.process(ProcessorConfigurationUtils.java:633)
	at org.thymeleaf.engine.ProcessorTemplateHandler.handleOpenElement(ProcessorTemplateHandler.java:1314)
	at org.thymeleaf.engine.TemplateHandlerAdapterMarkupHandler.handleOpenElementEnd(TemplateHandlerAdapterMarkupHandler.java:304)
	at org.thymeleaf.templateparser.markup.InlinedOutputExpressionMarkupHandler$InlineMarkupAdapterPreProcessorHandler.handleOpenElementEnd(InlinedOutputExpressionMarkupHandler.java:278)
	at org.thymeleaf.standard.inline.OutputExpressionInlinePreProcessorHandler.performInlining(OutputExpressionInlinePreProcessorHandler.java:440)
	at org.thymeleaf.standard.inline.OutputExpressionInlinePreProcessorHandler.handleText(OutputExpressionInlinePreProcessorHandler.java:146)
```

JS中对应的代码如下

 

```JavaScript
layui.config({
        //base: '../../../../static/plugins/layui/extend/'
        base: '[[@{/static/plugins/layui/extend/}]]'
    }).extend({
        treetable: 'treetable-lay/treetable'
    }).use(['layer', 'table', 'treetable'], function () {
        var $ = layui.jquery;
        var table = layui.table;
        var layer = layui.layer;
        var treetable = layui.treetable;
        // 渲染表格
        var renderTable = function () {
            layer.load(2);
            treetable.render({
                treeColIndex: 1,
                treeSpid: -1,
                treeIdName: 'id',
                treePidName: 'pid',
                treeDefaultClose: true,
                treeLinkage: false,
                elem: '#table1',
                url: '[[@{/static/data.json}]]',
                page: false,
                cols: [[
                    {type: 'numbers'},
                    {field: 'name', title: 'name'},
                    {field: 'id', title: 'id'},
                    {field: 'sex', title: 'sex'},
                    {field: 'pid', title: 'pid'}
                	]],
                done: function () {
                    layer.closeAll('loading');
                }
            });
        };
       renderTable();
    });
```


解决方式

1.也就是把cols后的[[ ]]变为

```javascript
[

    [
    
    ]

]
```

因为[[…]]之间的表达式在thymeleaf被认为是内联表达式,所以渲染错误

2.或者在<script type="text/javascript" >  加上 th:inline="none"