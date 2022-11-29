# ETA现状梳理
## 一、ETA预估的业务范围
&emsp;支持快送、专送、混合送、跑腿C/B、全城送的时间预估
* 专送逻辑：根据商家所在区域id获取算法包版本，将相关参数传入算法包后进行计算获取算法输出时间，在通过阈值管理平台获取是否有补时逻辑或其他补时逻辑，最后走30、120的兜底。
* 快送逻辑：根据商家所在区域id获取算法包版本，将相关参数传入算法包后进行计算获取算法输出时间，在通过阈值管理平台获取是否有补时逻辑或其他补时逻辑，最后走30、120的兜底。
* 全城送逻辑：将distance计算出来，根据商家所在区域id获取全城送算法版本，并在算法包根据配置计算对应预计送达时间，最后走30、120的兜底。
* 跑腿：调用方传入distance，根据MCC配置的距离-时间列表获取distance对应的送达时间，再判断并加上高峰期补时时间（时间都MCC给出）

## 二、订单类型
* 即时订单逻辑：根据商家ID获取区域ID，计算相应算法版本及配送类型，并收集相关特征，将特征带入相关算法版本计算deliveryTime，最后再进行相关补时后返回给调用方。
* 预订单逻辑：IPS没有区分订单和即时单，由C端调用提单页接口并返回结果后作区分。
* 拼团单逻辑：拼团单基本逻辑即时单，只是在拼团补时根据入参进行拼团补时。

## 三、商户状态
* 新商户：建立后，若最近30天内订单不足10单，则此商户的列表页和预览页都是45min。
* 未在营业时间的商户：列表页时间由c端出；IPS也不会给预览页计算逻辑。

## 四、阈值和补时的逻辑
### 阈值相关：预计送达时间-混合送-预览下单-LR算法除外。
如果算法版本为DT_ORDER_MULTISEND_LR（混合送三期），单独处理上下限：上限120，下限普通商家30/白名单商家45。其他算法版本情况下，先进行阈值判断处理，再进行上下限处理。上限90、下限普通商家30/白名单商家45。白名单存放的商家：近30天总单量少于10单，每天进行全量表更新。阈值判断处理逻辑如下：每两分钟拉去烽火台阈值管理最新配置列表，根据规则链做阈值判断，取所命中的阈值规则中的上下限的最大值。

### 烽火台补时相关
补时判断处理逻辑如下：每两分钟拉取烽火台补时管理最新配置列表，根据规则链做补时判断，取所命中的补时规则中的上下限的最大值。并将该补时加上预计送达时间上，最后进行上下限处理：上限120/下限30，

### 订单流补时相关
根据所获取的特征groupType（订单所属订单流组别）通过MCC获得相关补时，最后进行上下限处理：上限120/下限30。

### 众包大金额订单补时相关
判断如何配送类型为众包，则进入众包大金额订单补时，众包大金额订单补时规则见配置，最后进行上下限处理：上限120/下限30。

### 补团单补时相关
获取入参isGroupOrder和switch的值，当同时满足大于0，拼团补时=拼团时长（入参传入）-拼团已进行时间（入参传入），最后进行上下限处理：上限120/下限30。

### 恶劣天气补时相关
通过定时任务进行恶劣天气自动补时判断，符合自动补时条件则进行自动补时并存入squirrel缓存中。同时还有人工补时途径，通过星火APP进行的人工补时也会加入到squirrel缓存中。订单处理时BadWeatherAddTimeCollector获取特征badWeatherAddTime，并将该补时加到预计送达时间上。

## 五、后台配置的阈值
<table>
    <tr>
        <td>类型</td>
        <td>业务</td>
        <td>适用范围</td>
        <td>时间段</td>
        <td>阈值</td>
    </tr>
    <tr>
        <td>全局阈值</td>
        <td>加盟、代理、众包、快送、混合送</td>
        <td>提单页、列表页</td>
        <td>全天</td>
        <td>30min~90min</td>
    </tr>
    <tr>
        <td  rowspan="3">距离阈值</td>
        <td>加盟、代理、众包、快送、混合送</td>
        <td>提单页</td>
        <td>全天</td>
        <td>>3km，35min~95min；>4km，40min~95min；>6km，50min~95min；>8km，55min~95min；>10km，60min~95min；</td>
    </tr>
    <tr>
       <td>众包、快送</td>
        <td>列表页</td>
        <td>全天</td>
        <td>>3km，32min~90min；>4km，36min~90min；>6km，48min~90min；>8km，8min~90min；>10km，60min~90min；</td>
    </tr>
    <tr>
        <td>加盟、代理、混合送</td>
        <td>列表页</td>
        <td>全天</td>
        <td>>3km，35min~95min；>4km，40min~95min；>6km，50min~95min；>8km，55min~95min；>10km，60min~95min；</td>
    </tr>
     <tr>
        <td>金额阈值</td>
        <td>加盟、代理、众包、快送、混合送</td>
        <td>提单页</td>
        <td>全天</td>
        <td>>=500元，70min~90min；>1000元，80min~90min；</td>
    </tr>
</table>

## 六、下单预览页预计送达时间计算结果缓存锁定
### 锁定开关
* MCC Key：计算结果缓存锁定开关
* MCC Value：小于等于0/关，大于0/开

如果开关打开则走预计送达时间锁定相关逻辑，开关关闭则走实时计算逻辑。

### lockkey定义
* MCC Key：lock.key.switch
* MCC Value：1、token+uuid  2、uuid  3、token

根据user的经纬度计算出60位精度的userGeoHash，并据此生成token userGeoHash或者uuid wmPoiId userGeoHash。如果没有计算出结果，则返回空。

### 锁定逻辑
调用类型
* 预览页（pre_order）：每次调用接口实时计算，获取计算结果，如果lockkey存在，则将计算结果和对应的lockkey、过期时间一起存入缓存squirral。将订单相关信息保存到缓存（供计算出餐时间调用）
* 时间页（time_list）：根据lockkey获取是否已缓存，如果缓存中获取相应计算结果，则使用缓存中的时间作为预计送达时间；如果缓存中没有获取到相应计算结果，则调用接口实时计算预计送达时间。
* 下单页（order）：根据lockkey获取是否缓存，如果缓存中获得相应计算结果，则将缓存的订单信息orderView取出作为参数重新实时计算预计送达时间想mafka异步发送订单相关信息供lbs消费。

## 七、AB实验
TODO

## 八、算法补时逻辑
<table>
    <tr>
        <td>类型</td>
        <td>业务</td>
        <td>适用范围</td>
        <td>时间段</td>
        <td>补时</td>
    </tr>
    <tr>
        <td>专送订单凌晨补时</td>
        <td>加盟、代理、混合送</td>
        <td>提单页</td>
        <td>0:00-6:00</td>
        <td>补时5分钟</td>
    </tr>
</table>
快送因子补时策略：对于需要补时的因子（价格、距离、负载），补时=因子*因子系数*rf标准差 / 200
<table>
    <tr>
        <td  rowspan="2">价格</td>
        <td>因子</td>
        <td>[0,50]</td>
        <td>[50,100]</td>
        <td>[100,300]</td>
        <td>[300,500]</td>
        <td>[500,700]</td>
        <td>[700,inf]</td>
    </tr>
    <tr>
        <td>因子系数</td>
        <td>0</td>
        <td>0.55</td>
        <td>0.7</td>
        <td>0.8</td>
        <td>0.9</td>
        <td>1</td>
    </tr>
    <tr>
        <td  rowspan="2">距离</td>
        <td>因子</td>
        <td>[0,1000]</td>
        <td>[1000,2000]</td>
        <td>[2000,3000]</td>
        <td>[3000,4000]</td>
        <td>[4000,5000]</td>
        <td>[5000,inf]</td>
    </tr>
    <tr>
        <td>因子系数</td>
        <td>0.003</td>
        <td>0.004</td>
        <td>0.005</td>
        <td>0.055</td>
        <td>0.055</td>
        <td>0.055</td>
    </tr>
    <tr>
        <td  rowspan="2">价格负载</td>
        <td>因子</td>
        <td>[0,0.5)</td>
        <td>[0.5,1)</td>
        <td>[1,1.5)</td>
        <td>[1.5,2)</td>
        <td>[2,3)</td>
        <td>[3,4)</td>
        <td>[4,5)</td>
        <td>[5,inf)</td>
    </tr>
    <tr>
        <td>因子系数</td>
        <td>0</td>
        <td>20</td>
        <td>50</td>
        <td>100</td>
        <td>150</td>
        <td>200</td>
        <td>220</td>
        <td>230</td>
    </tr>
</table>

## 九、收集特征
* 配送类型特征deliveryWayType
* 算法特征algoKey
* 算法版本特征versionKey
* 调用ADS获取area维度的特征：areaDuration、avgTime等
* ADSPoiModelCollector：调用ADS获取poi维度的特征，areaDuration(专送商家)、poiDuration(专送区域)、poiZBDuration(众包商家)、areaZBDuration(众包区域)、poiDelPrepTime(商家离线)等。
* PoiBaseDataCollector：调用squirrel获取特征/tags
* DCSAreaInfoCollector：调用DCS获取区域维度的实时特征areaSnapshot(预计送达时间爆单补时相关)，avgLoad1区域实时负载1、avgLoad2实时负载2、untakeCountWithOutBusy未取餐单量、unsendCountWithOutBusy总单量。
* PoiAVGTimeCollector：获取商家平均配送时长avgDeliveryTime、平均出餐时长avgPrePareTime、是否在黑名单isInBlackList
* BqsUserPoiInfoColletor：当商家为混合送类型时，获取特征isUserInPoiKSField(用户是否在商家快送配送范围内)
* RfFeatureCollector：RF模块相关运单特征waybillInfo、waybillInfo2等。
* 从tair缓存中获取恶劣天气补时badWeatherAddTime、badWeatherOpType等。
* 商户组业务接口中获取订单流的groupType的特征。
* PoiPrepareTimeCollector：调用本地方法计算出餐时间prepareTime特征。

