# october_blade
主要功能为 自动识别匹配语言货币，翻译功能的加强

## 框架
依赖octoberCMS ~1.0 安装方式参考：https://github.com/octobercms/october

## 安装说明
    1. 将php添加到环境变量，正确安装composer，正确的安装octoberCMS
    2. 进入到octoberCMS项目根目录，运行命令：composer require lonban/october_blade
    3. 一些大的资源文件下载地址：https://pan.baidu.com/s/1-DJ8LW1sSf5y8K6VBmZ_vQ 下载后解压放到octoberCMS根目录\storage\app\LongBang\geoIp
    4. 如果报错请将octoberCMS根目录\plugins\lonban\october-blade\composer.json里的"json/ccshop": "~1.0"依赖去掉，因为它是闭源项目

## 使用说明

#### 多语言：
    ---
    使用示例： http://网站域名/languages/setLanguage?匹配方式
    测试示例(可以看到切换结果)： http://网站域名/languages/setLanguage?匹配方式&test=1
    完整示例(切换语言为en-货币为us，并查看切换结果)：http://localhost/languages/setLanguage?language=en-us&test=1
    匹配方式的说明：
        示例：http://aaa.a/languages/setLanguage?language=en-us
        现在使用的示例：https://网站域名/?locale=zh-cn用的是输入语言代码进行匹配的方式(要改多语言路由)
        默认语言的说明：用的匹配ip与匹配浏览器语言(要改多语言路由)在匹配ip与浏览器语言的时候只会在用户第一次打开网站，且没有告诉网站它想使用的语言，才会去触发默认语言设置
        切换后成功与否都会自动返回前一页，可以直接以a标签跳转的方式调用，建议以ajax调用
        输入语言代码进行匹配：（language=en-us）语言管理里面code的"~"前面部分，如果语言管理里面对应的语言包如en没有问题,就会设置成这个语言en否则设置为默认语言，如果国家如us对应的货币没有问题且开启状态,就会设置成这个货币否则设置为默认货币，下面所有的扩展也都会走这个流程
        输入货币代码进行匹配：（currency=usd）语言管理里面code的"~"后面部分，如果货币管理对应的货币没问题且开启状态，就会设置成这个货币与这个货币与对应的国家语言
        与ip匹配：
             与用户端ip地址进行匹配如：（ip=1）根据用户当前ip所对应的国家，判断我们的语言数据里面是否有这个国家的语言，如果有就设置成这个语言
             输入ip如：（ip=100.10.1.1）根据100.10.1.1所对应的国家，判断我们的语言数据里面是否有这个国家的语言，如果有就设置成这个语言
             ip的语言代码如：（ip=en-us）说明：这与第一种方式差不多但确不一样，因为ip获取的的国家代码是由geoip根据iso639返回的，这与我们的语言数据有一部分会存在区别，我们的国家语言代码更多的是考虑浏览器的语言而不仅仅是iso639
        与浏览器匹配：
             与用户端浏览器第一语言进行匹配如：（browser=1）获取用户当前所使用的浏览器的第一语言，判断我们的语言数据里面是否有这个国家的语言，如果有就设置成这个语言
             输入浏览器的语言代码如：（browser=en-us）与第一种方式暂时非常类似
        打印结果：
            显示切换后的结果数据（test=1）如：http://aaa.a/languages/setLanguage?language=en-us&test=1
    下面的方式依然可用：
        1. language直接匹配，如:https://martasa.com/setLanguage/zh-cn_language
        2. currency输入货币代码进行匹配，如:https://martasa.com/setLanguage/usd_currency
        3. ip与ip获取到的语言进行匹配：_ip与当前测试机的ip地址所对应的国家进行匹配，如:https://martasa.com/setLanguage/_ip 或 https://martasa.com/setLanguage/100.10.15.26_ip 或https://martasa.com/setLanguage/en_ip（因为ip获取的的国家代码与语言管理内的code还有浏览器获取到的国家代码是部分存在区别的）
        4. browser与获取到的浏览器第一语言进行匹配，谷歌浏览器可在设置栏里，把需要测试的语言移到顶部，然后返回到测试页面刷新，测试地址：https://martasa.com/setLanguage/_browser 或https://martasa.com/setLanguage/en_browser（因为浏览器获取的的国家代码与语言管理内的code还有ip获取到的国家代码是部分存在区别的）
    使用：
        方法1. 可以以get方式直接传语言进行切换如：<a href="https://martasa.com/?locale=zh-cn">中文</a>
        方法2. 用vc2_language.js进行切换：区别它可以是select标签,也可以是其它任意标签，原理location.href=window.location.href+'?locale=zh-cn&currency=hkd';
            vc2_language.js的使用说明，a标签的使用也可以参考：
                注意：使用前请先在页面头部引入对象[vc]
                如:
                    title = "index"
                    description = "首页"
                    layout = "default"
                    url = "/"
                    [vc]
                    ==

                标签上加入属性selectLocale="1"，切换成功后，会把该标签里的内容，改成切换成功后的语言名称，同时在切换语言等待的过程中会在该标签上出现一个动画的效果。
                标签上加入hide="1"，在鼠标经过该标签的时候它才会显示出来，鼠标离开后，该标签会隐藏
                class="{% if catalog.switchedCurrency.code == val['code'][1] %} active {% endif %}" 判断当前用户所使用的货币代码，对该货币所对应的语言标签加上active属性
                <script src="{{'js/vc2_language.min.js'|vcSrc}}"></script> 在页面中引入vc2_language.js，如果对压缩的min.js有疑惑，可把min去掉，则直接引入原js
                $('#locale').vc2_language(); 调用vc2_language.js
                实例：
                    <!--在页面中引入js--!>
                    <script src="{{'js/vc2_language.min.js'|vcSrc}}"></script>
                    <div id="locale">
                        <div selectLocale="1">{{ 'Language'|_ }}&nbsp;▼</div>
                        <ul hide="1">
                            {% for val in vc.languageAll() %}
                                <!--判断状态，关闭的语言不显示出来--!>
                                {% if val['is_enabled'] %}
                                <!--判断当前语言，加active属性--!>
                                <li class="{% if selectedCurrency.code == val['currency'] %} active {% endif %}"
                                    locale="{{val['language']}}" money="{{val['currency']}}">{{val['name']}}</li>
                                {% else %}
                                <li class="none {% if selectedCurrency.code == val['currency'] %} active {% endif %}"
                                    locale="{{val['language']}}" money="{{val['currency']}}">{{val['name']}}</li>
                                {% endif %}
                            {% endfor %}
                        </ul>
                    </div>
                    <!--调用js实现切换等功能--!>
                    $('#locale').vc2_language();

#### 自动获取填充国家、省、市、邮编，ISO_3166标准
    ---
    以ip获取:
        例：http://网站域名/area/getGeo
        例：http://网站域名/area/getGeo?ip=100.10.1.1
        可以传一个ip地址过来也可以不传，不传默认获取客户端ip
    以定位获取：
        http://网站域名/area/getLocation
        区别：可获取到详细地址，缺点部分客户端没有定位功能
        待续...
    使用：
        待续...