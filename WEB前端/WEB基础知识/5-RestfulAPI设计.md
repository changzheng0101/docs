# RestfulApi设计

## 1.Rest

表述性状态传递，决定了接口的形式与规则。

## 2.API

应用程序接口，负责和应用程序进行对接。

## 3.RestfulAPI

主要满足以下三个条件：

* 通过方法就可以知道动作，例如GET是获取数据、PUT是更新数据
* 在url请求路径中没有动词，只有名词
* 通过返回值即可知道与服务器交互的结果

### 3.1示例

以获取小狗为例：

| api                 | 解释               |
| ------------------- | ------------------ |
| GET      /dogs/{id} | 获取某一个id的小狗 |
| GET     /dogs       | 获取所有小狗       |
| POST   /dogs        | 添加小狗           |
| PUT     /dogs/{id}  | 更新一个小狗       |
| DELETE /dogs/{id}   | 删除一个小狗       |

>**注意在这些API中使用的都是复数**，可以在前面增加版本控制/api/v1/xxxxx，之后再开发可以增加新的版本

### 3.2复杂的restful请求

* GET /cars/12/drivers 返回所有使用过12号汽车的司机
* GET /cars/12/drivers/2 返回使用过12号汽车的2号司机

### 3.3增加参数

| 例子                                  | 详细说明                                     |
| ------------------------------------- | -------------------------------------------- |
| GET /cars?color=red                   | 获取颜色为红色的小汽车                       |
| GET /cars?seats<4                     | 获取座位数小于4的小汽车                      |
| GET /cars?sort=-manufacture,+model    | 以制造商降序，生产者排序的方式获取所有小汽车 |
| GET /cars/fields=manufacture,color,id | 只获取cars的manufacture,color,id字段         |
| GET /cars?offset=10&limit=5           | 通过分页的方式获取数据                       |

