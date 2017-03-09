# boxfish-pay
=============
##支付系统 

[![Build Status](http://101.201.234.176:8080/buildStatus/icon?job=boxfish_pay)](http://101.201.234.176:8080/buildStatus/icon?job=boxfish_pay)



#环境说明

   * 支付模块(env.pay):
  
    | 环境类型        | 地址(url)                     | 说明       |app path| log path|
    |-----------------|-------------------------------|------------|------|---------|
    | default         | http://127.0.0.1:8080         | 无         |      |          |
    | development     | http://101.201.237.252:8888   | 无         |/root/app|/root/logs|
    | test            | http://123.56.6.91:8888       | 无         |/root/app|/root/logs|
    | demo            | http://101.201.66.32:8888     | 无         |/root/app|/root/logs|
    | production      | https://pay.boxfish.cn        | 无         |/root/app|/root/logs|

   * 支付CRM模块(env.paycrm):

    | 环境类型        | 地址(url)                         | 说明       |
    |-----------------|-----------------------------------|------------|
    | default         | http://127.0.0.1:8889             | 无         |            
    | development     | http://101.201.237.252:8889       | 无         |
    | test            | http://123.56.6.91:8889           | 无         |
    | demo            | http://101.201.66.32:8889         | 无         |
    | production      | http://online-api.inside.boxfish.cn | 无         |

# 接口说明
支付接口
--------------

1. 获取支付方式配置信息
    * 请求地址: {env.pay.url}/payment/gateway/getPaymentConfig.do
    * 请求参数: 空
    * 请求结果:
    ```JAVA
    {
      data: [
        {
          id: 1,
          createtime: 1463723694000,
          updatetime: null,
          payChannel: "10",
          payChannelName: "支付宝支付",
          state: 1,
          payRequestUrl: "http://101.201.237.252:8888/payment/toPay.do",
          version: null,
          payType: 0,
          priority: 10
        },
        {
          id: 2,
          createtime: 1463723694000,
          updatetime: null,
          payChannel: "20",
          payChannelName: "支付宝支付",
          state: 1,
          payRequestUrl: "http://101.201.237.252:8888/payment/toPay.do",
          version: null,
          payType: 0,
          priority: 10
        }
      ],
      returnCode: 200,
      returnMsg: "success"
    }
    ```
2. 请求支付
   * 请求地址: payRequestUrl（备注：见“获取支付方式配置信息”接口响应结果payRequestUrl）
   * 请求参数:
  
    | 参数       | 类型   | 必填 | 说明           |
    |------------|--------|------|----------------|
    | orderCode  | String | Y    | 订单号         |
    | price      | String | N    | 支付价格（元） |
    | source     | String | N    | 来源（ios\android）|
    | sign       | String | Y    | 签名           |
    | payChannel | String | Y    | 支付渠道       |
   
   * 请求结果:
    ```JAVA
    {
      data: "request package",
      returnCode: 200,
      returnMsg: "success"
    }
    ```
3. 获取订单信息
   * 请求地址：{env.paycrm.url}/pay-crm/queryOrderinfo.do?access_token=xxx
   * 请求方法: GET
   * request参数:

    |字段名           |变量名          |类型          |是否必填  |描述                  | 示例值             |
    |----------------|----------------|--------------|---------|-----------------------|-------------------|
    | 用户ID         |userId          | String       |  否     |                       |                   |
    | 订单编号        |orderNo         | String       |  否     |                       | 111850820160521131946068  |
    | 订单状态        |orderState      | String       |  否     | 0:支付未知,1:支付完成,2:支付失败,3:重复支付,4:失效订单| 1       |
    | 下单时间(start) |startOrderTime  | String       |  否     | 格式:YYYY-MM-DD       | 2016-05-26       |
    | 下单时间(from)  |endOrderTime    | String       |  否     | 格式:YYYY-MM-DD       | 2016-05-26       |
    | 第几页          |pageNo          | String       |  否     | 默认0 表示第一页        | 0               |
    | 分页条数        |pageNum         | String       |  否     | 不传采用默认值:10       | 15                |

   * response结果:

    |字段名           |变量名          |类型          |是否必填  |描述                  | 示例值             |
    |----------------|----------------|--------------|---------|-----------------------|-------------------|
    | 用户ID         |userId          | String       |  是     |                        |                   |
    | 订单编号        |orderNo         | String       |  是     |                        | 111850820160521131946068  |
    | 订单金额        |orderAmount      | String      |  是     | 单位:分                 | 100                 |
    | 优惠金额        |couponAmount     | String      |  是    | 单位:分                  | 60                |
    | 实际支付金额     |payAmount        | String      |  是    | 单位:分                  | 40                |
    | 下单时间        |orderTime         | String     |  是    | 格式:yyyy-MM-dd HH:mm:ss | 2016-05-19 10:09:32    |
    | 订单状态        |orderState        | String     |  是     | 订单状态的数字码          | 1               |
    | 订单状态        |orderMessage      | String     |  是     | 订单状态的文字表示        | 支付完成          |
    | 服务内容        |serviceContent   | String      |  是     | 固定:"课程服务"         |                 |
      示例:
    ```java
    {
        "data": {
            "content": [
                {
                    "serviceContent": "cource service",
                    "orderNo": "96364620160519100932460",
                    "orderAmount": 8000,
                    "couponAmount": 3000,
                    "payAmount": 5000,
                    "orderTime": "2016-05-19 10:09:32",
                    "userId": "963646",
                    "orderMessage": "未支付"
                }
            ],
            "totalElements": 1
        },
        "returnCode": 200,
        "returnMsg": "success"
    }
    ```
4. 获取支付信息
   * 请求地址：{env.paycrm.url}/pay-crm/queryPayinfo.do?access_token=xxx
   * 请求方法: GET
   * request参数:

    |字段名           |变量名          |类型          |是否必填  |描述                  | 示例值             |
    |----------------|----------------|--------------|---------|-----------------------|-------------------|
    | 用户ID         |userId          | String       |  否     |                       |                   |
    | 支付流水号      |payNo           | String       |  是     | 交易系统存储的支付单号   | 2001160521094326610  |
    | 第三方流水号    |serialNo         | String       |  是     | 发送给支付宝或微信的out_trade_no | 2001160521100203718  |
    | 订单编号        |orderNo         | String       |  否     |                       | 111850820160521131946068  |
    | 订单状态        |orderState      | String       |  否     | 0:支付未知,1:支付完成,2:支付失败,3:重复支付,4:失效订单| 1       |
    | 支付状态        |payState        | String       |  否     | 0:未支付,1:已支付       |   1                |
    | 下单时间(start) |startOrderTime  | String       |  否     | 格式:YYYY-MM-DD       | 2016-05-26       |
    | 下单时间(from)  |endOrderTime    | String       |  否     | 格式:YYYY-MM-DD       | 2016-05-26       |
    | 付款时间(start) |startPayTime    | String       |  否     | 范围,付款成功的时间,格式:YYYY-MM-DD | 2016-05-26       |
    | 付款时间(end)	|endPayTime      | String       |  否     | 范围,付款成功的时间,格式:YYYY-MM-DD  | 2016-05-26       |
    | 第几页          |pageNo          | String       |  否     | 默认0 表示第一页        | 0               |
    | 分页条数        |pageNum         | String       |  否     | 不传采用默认值:10       | 15                |

   * response结果:

    |字段名           |变量名          |类型          |是否必填  |描述                  | 示例值             |
    |----------------|----------------|--------------|---------|-----------------------|-------------------|
    | 用户ID         |userId          | String       |  是     |                       |                   |
    | 支付流水号      |payNo           | String       |  是     | 交易系统存储的支付单号   | 2001160521094326610  |
    | 第三方流水号    |serialNo         | String       |  是     | 发送给支付宝或微信的out_trade_no | 2001160521100203718  |
    | 订单编号        |orderNo         | String       |  是     |                        | 96364620160519100932460      |
    | 金额           |payAmount        | String       |  是    | 单位:分                | 1                  |
    | 支付时间        |createTime      | String       |  是     | 开始支付的时间,不是成功支付的时间,大约就是支付到输入密码之间的时间  |  2016-05-19 20:44:48  |
    | 流水状态        |payState        | String       |  是     | 支付状态的code表示,0:未支付,1:已支付 |   1               |
    | 流水状态        |payMessage      | String       |  是     | 支付状态的文字表示,0:未支付,1:已支付  |   已支付               |
    | 支付成功时间     |payTime         | String       |  是     | 支付宝或者微信通知给交易服务器的时间  |  2016-05-26 15:22:57    |
    | 付款方式        |payChannelName   | String      |  是     | 支付渠道名称              |  支付宝支付           |
    | 操作           |operation        | String       |  是     | 暂时不可以操作,固定:"0"    |                 |
      示例:
    ```java
    {
        "data": {
            "content": [
                {
                    "payNo": "2001160521094326610",
                    "orderNo": "96364620160519100932460",
                    "payAmount": 8000,
                    "payChannelName": "微信支付",
                    "createTime": "2016-05-21 09:43:26",
                    "payTime": null,
                    "userId": "963646",
                    "operation": "0",
                    "payState": "0",
                    "payMessage": "未支付",
                    "serialNo": null
                },
                {
                    "payNo": "2001160521095328213",
                    "orderNo": "96364620160519100932460",
                    "payAmount": 8000,
                    "payChannelName": "微信支付",
                    "createTime": "2016-05-21 09:53:28",
                    "payTime": null,
                    "userId": "963646",
                    "operation": "0",
                    "payState": "0",
                    "payMessage": "未支付",
                    "serialNo": null
                }
            ],
            "totalElements": 390
        },
        "returnCode": 200,
        "returnMsg": "success"
    }
    ```
5. 查询支付渠道信息
    * 支付网关查询URL：{env.paycrm.url}/pay-crm/queryPayChannel.do?access_token=xxx
    * 请求方法:GET
    * request参数：无
    * response结果:

    |字段名         |变量名          |类型          |是否必填  |描述                  | 示例值             |
    |--------------|----------------|-------------|---------|-----------------------|-------------------|
    | 支付通道名称   |payChannelName  | String      |  是     |                       | 支付宝支付         |
    | 支付通道code  |payChannel      | String       |  是     | 代表支付通道的唯一code  | 10               |
    | 状态          |state          | String       |  是     | 0:关闭,1:开启         | 1                 |
    * 说明:没有分页
    * 返回结果示例:
    ```java
    {
        "data": {
            "content": [
                {
                    "payChannelName": "支付宝支付",
                    "payChannel": "10",
                    "state": "1"
                },
                {
                    "payChannelName": "微信支付",
                    "payChannel": "20",
                    "state": "1"
                }
            ],
            "totalElements": 2
        },
        "returnCode": 200,
        "returnMsg": "success"
    }
    ```
6. 打开/关闭支付渠道
    * URL：{env.paycrm.url}/pay-crm/openOrClosePayChannel.do?access_token=xxx
    * 请求方法:POST
    * Content-Type:application/json
    * request参数：

    |字段名         |变量名          |类型          |是否必填  |描述                  | 示例值             |
    |--------------|----------------|-------------|---------|-----------------------|-------------------|
    | 支付通道code  |payChannel      | String       |  是     | 代表支付通道的唯一code  | 10               |
    | 状态          |state          | String       |  是     | 0:关闭,1:开启         | 1                 |
    示例:
    {"payChannel":"10","state":"1"}
    * response结果:

    |字段名         |变量名          |类型          |是否必填  |描述                  | 示例值             |
    |--------------|----------------|-------------|---------|-----------------------|-------------------|
    | 更新件数      |count           | String      |  是     |                       | 1                |
    示例:
    ```Java
    {
        "data": {
            "count": "1"
        },
        "returnCode": 200,
        "returnMsg": "success"
    }
    ```
7. 修改支付渠道信息
    * URL: {env.paycrm.url}/pay-crm/editPayChannel.do?access_token=xxx
    * 请求方法:POST
    * Content-Type:application/json
    * request参数：

    |字段名         |变量名          |类型          |是否必填  |描述                  | 示例值             |
    |--------------|----------------|-------------|---------|-----------------------|-------------------|
    | 支付通道code  |payChannel      | String       |  是     | 代表支付通道的唯一code  | 10               |
    | 支付通道名称   |payChannelName  | String       |  是     |                      | 支付宝支付         |
    * response结果:

    |字段名         |变量名          |类型          |是否必填  |描述                  | 示例值             |
    |--------------|----------------|-------------|---------|-----------------------|-------------------|
    | 更新件数      |count           | String      |  是     |                       | 1                |
    示例:
    ```Java
    {
        "data": {
            "count": "1"
        },
        "returnCode": 200,
        "returnMsg": "success"
    }
    ```

8. 代付地址
    * URL: {env.pay.url}/payment/scanPay.do
    * 请求方法:GET
    * Request参数:
    
    |变量名          |类型          |是否必填  |描述                  | 示例值             |
    |--------------|----------------|-------------|---------|-----------------------|
    |orderCode | String | Y | 订单号| 96364620160519100932460 |
    |agentType |String| Y | 代付类型|QR(二维码)或者 LINK(链接)|
    |showType | String| N | 显示类型| COMMENT(外教点评类型) | 
    示例:
    ```JAVA
    {
       http://{env.pay.url}/payment/scanPay.do?orderCode=96364620160519100932460&agentType=QR
    }
    ```
9. 退款请求接口
    * URL: {env.pay.url}/refund/toRefund.do
    * 请求方式: POST
    * Request参数:
    
    |变量名          |类型          |是否必填  |描述                  | 示例值             |
    |--------------|----------------|-------------|---------|-----------------------|
    |orderCode | String | Y | 订单号| 96364620160519100932460 |
    |refundId |String| Y | 退款标识|一笔订单号相同refundId代表相同的退款| 
    
    * response结果:

    |变量名          |类型          |是否必填  |描述                  | 示例值             |
    |----------------|-------------|---------|-----------------------|-------------------|
    |data           | String      |  是     |     success                  |             |    
    ```JAVA
    {
      data: "success",
      returnCode: 200,
      returnMsg: "success"
    }
    ```
10.退款信息查询
    * URL: {env.pay.url}/pay-crm/queryRefundInfo
    * 请求方式: GET
    * Content-Type:application/json
    * Request参数:
 
    |字段名           |变量名          |类型          |是否必填  |描述                  | 示例值             |
    |----------------|----------------|--------------|---------|-----------------------|-------------------|
    | 用户ID          |userId          | String       |  否     |                       |                   |
    | 退款流水         |refundOrderNo          | String       |  否     | 表示退款单号        |210116060818185712503090001                   |       
    | 订单号           |orderNo          | String       |  否     | 订单号                      |                   |
    | 退款状态         |refundState          | String       |  否     |退款状态:0:待退款,1:退款中,2:退款完成,9:退款失败  |  退款完成                 |
    | 退款申请时间start |refundApplyTimeStart          | String       |  否     | 学生开始申请退款的时间  | 2016-08-08                  |
    | 退款申请时间end   |refundApplyTimeEnd          | String       |  否     |  学生开始申请退款的时间   | 2016-08-08                  |
    | 第几页           |pageNo          | String       |  否     | 默认0 表示第一页        | 0               |
    | 分页条数         |pageNum         | String       |  否     | 不传采用默认值:10       | 15                | 
   
    * response结果:

    |字段名           |变量名          |类型          |是否必填  |描述                  | 示例值             |
    |----------------|----------------|--------------|---------|-----------------------|-------------------|
    | 退款流水         |refundOrderNo          | String       |  是     |表示退款单号         | 210116060818185712503090001                  |
    | 第三方退款流水    |gatewaySerialNo          | String       |  是     | 发送给网关(支付宝或微信)的流水号                      |                   |
    | 订单号           |orderNo          | String       |  是     |                       |                   |
    | 学生ID          |userId          | String       |  是     |                       |                   |
    | 退款金额         |refundAmount          | String       |  是     |  该次退款申请的退款金额                     |                   |
    | 订单金额         |payAmount          | String       |  是     |  该订单的实际支付金额  |                   |
    | 已退款金额         |refundSumAmount          | String       |  是     | 该笔订单已退款成功的金额SUM                      |                   |
    | 退款渠道         |refundChannelName          | String       |  是     | 该笔退款的退款方式,支付渠道原路退回。  | 支付宝支付     |
    | 状态             |refundStateName             | String       |  是     | 退款进度状态                      |  待退款                 |
    | 申请时间         |refundApplyTime          | String       |  是     | 退款申请时间                      |                   |
    | 退款时间         |refundSendTime          | String       |  是     |  退款申请后,24小时后开始把退款申请发送到网关。这个发送到网关的时间即为退款时间 |                   |
    | 到账时间         |refundCompleteTime          | String       |  是     |退款完成时间,第三方(微信或支付宝)回调通知退款完成的时间                       |                   |
    | 操作            |operation          | String       |  是     |  normal:正常 suspend:挂起。表示该订单当前的状态。 |  normal                 |
11.退款挂起操作
    * URL: {env.pay.url}/pay-crm/operateRefund
    * 请求方式: POST
    * Content-Type:application/json
    * Request参数:  
 
    |字段名           |变量名          |类型          |是否必填  |描述                  | 示例值             |
    |----------------|----------------|--------------|---------|-----------------------|-------------------|
    | 退款流水        |refundOrderNo       | String       |  是     |仅【normal】和【suspend】时,使用该参数                     |                   |
    | 操作           |operation        | String       |  是     | suspend:挂起 normal:正常,on:开启自动退款,off:关闭自动退款。|                   |       

    * response结果:
    
    |字段名           |变量名          |类型          |是否必填  |描述                  | 示例值             |
    |----------------|----------------|--------------|---------|-----------------------|-------------------|
    | 操作结果        |data            | String        |  是     |     success           |                  |
    ```JAVA
    {
      data: "success",
      returnCode: 200,
      returnMsg: "success"
    }
    ```
12.查询自动退款状态    
    * URL: {env.pay.url}/pay-crm/autoRefundState
    * 请求方式: GET
    * Content-Type:application/json
    * Request参数:无  
    * response结果:
    
    |字段名           |变量名          |类型          |是否必填  |描述                  | 示例值             |
    |----------------|----------------|--------------|---------|-----------------------|-------------------|
    |操作结果          |data           | String      |  是     |     on:开启自动退款,off:关闭自动退款 |   on          |    
    ```java
    {
      data: "on",
      returnCode: 200,
      returnMsg: "success"
    }    
    ```
# 依赖系统接口说明
订单系统
--------------

1. 创建订单
    * 请求地址: ${orderHost}/order/app/create?access_token={tokenCode}
    * 请求方式: POST
    * Request参数:

     |变量名          |类型          |是否必填  |描述                  | 示例值             |
     |--------------|----------------|-------------|---------|-----------------------|
     |id | String | Y | 产品标识| 1 |
     |orderSource |String| Y |订单源(IOS)||

    * response结果:

     |变量名          |类型          |是否必填  |描述                  | 示例值             |
     |----------------|-------------|---------|-----------------------|-------------------|
     |orderCode        | String      |  否     |     订单号            |             |

    * 请求结果:
    ```JAVA
    {
      "returnMsg": "success",
      "returnCode": 200,
      "data": {
        "id": 946,
        "orderCode": "16081311115007778767",
        ...
      }
    }
    ```
2. 查询订单
    * 请求地址: ${orderHost}/order/byCode/{orderCode}
    * 请求方式: GET
    * Request参数:

     |变量名          |类型          |是否必填  |描述                  | 示例值             |
     |--------------|----------------|-------------|---------|-----------------------|
     |orderCode | String | Y | 订单号| 16081311115007778767 |
    * response结果:

     |变量名          |类型          |是否必填  |描述                  | 示例值             |
     |----------------|-------------|---------|-----------------------|-------------------|
     |skuAmount       | String      |  否     |     sku金额            |             |
     |desc            | String      |  否     |     描述               |             |

    * 请求结果:
    ```JAVA
    {
      "skuAmount": "8",
      "desc": "7次中教＋1次外教，1对1，25分钟/课"
    }
    ```
3. 查询订单明细
    * 请求地址: ${orderHost}/order/detail/{orderCode}
    * 请求方式: GET
    * Request参数:

     |变量名          |类型          |是否必填  |描述                  | 示例值             |
     |--------------|----------------|-------------|---------|-----------------------|
     |orderCode | String | Y | 订单号| 16081311115007778767 |

    * 请求结果:
    ```JAVA
    {
      "returnMsg": "success",
      "returnCode": 200,
      "data": {
        "id": 946,
        "orderCode": "16081311115007778767",
        ...
      }
    }
    ```
4. 更新订单
    * 请求地址: ${orderHost}/order/update
    * 请求方式: POST
    * Request参数:

     |变量名          |类型          |是否必填  |描述                  | 示例值             |
     |--------------|----------------|-------------|---------|-----------------------|
     |orderCode | String | Y | 订单号| 16081311115007778767 |
     |payChannel | String | Y | 支付渠道| 21 |

    * 请求结果:
    ```JAVA
    {
      returnCode: 200,
      returnMsg: "success"
    }
    ```
5. 订单失效
    * 请求地址: ${orderHost}/order/closed/{orderCode}
    * 请求方式: POST
    * Request参数:

     |变量名          |类型          |是否必填  |描述                  | 示例值             |
     |--------------|----------------|-------------|---------|-----------------------|
     |orderCode | String | Y | 订单号| 16081311115007778767 |

    * 请求结果:
    ```JAVA
    {
      returnCode: 200,
      returnMsg: "success"
    }
    ```
6. 查询退款明细
    * 请求地址: ${orderHost}/order/refund/price
    * 请求方式: POST
    * Request参数:

      |变量名          |类型          |是否必填  |描述                  | 示例值             |
      |--------------|----------------|-------------|---------|-----------------------|
      |orderCode | String | Y | 订单号| 96364620160519100932460 |
      |refundId |String| Y | 退款标识|一笔订单号相同refundId代表相同的退款|

    * response结果:

      |变量名          |类型          |是否必填  |描述                  | 示例值             |
      |----------------|-------------|---------|-----------------------|-------------------|
      |refundId        | String      |  否     |     退款标识            |             |
      |refundPrice     | String      |  否     |     退款金额(单位:分)    |             |
      |refundReason    | String      |  否     |     退款原因            |             |

    * 请求结果:
    ```JAVA
    {
        "data": {
            "refundId": "0001"
            "refundPrice": "1"
            "refundReason": "课程取消"
        },
        "returnCode": 200,
        "returnMsg": "success"
    }
    ```
7. 退款回调结果通知
   * 请求地址: ${orderHost}/order/refund/notify_result
   * 请求方式: POST
   * 请求参数:

      | 参数              | 类型   | 必填 | 说明           |
      |------------------|--------|------|----------------|
      | orderCode       | String | Y    | 订单号           |
      | refundId        | String | Y    | 退款标识         |
      | refundResult    | String | Y    | 退款结果（S/F）   |

   * 请求结果:
    ```JAVA
    {
      returnCode: 200,
      returnMsg: "success"
    }
    ```
