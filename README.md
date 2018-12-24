# 1 DEMO使用说明
1. 在进行以下操作时，请选择扇区，默认扇区为11扇区
2. 使用前，客人卡和授权卡的密码B均需提前置为“B4B4BCD1CBF8”
3. 获取授权：将从授权卡中读取到选定扇区的授权码，并保存到aar包中，用于后面写卡的时候，将此授权码写入客人卡的密码A
4. 读卡：通过客人卡的密码A读取客人卡中选定扇区的卡片信息，包含：卡号、开始时间、结束时间、房间号、是否挂失、是否注销
5. 写卡：通过客人卡的密码B将授权码（从授权卡中获取到）、开始时间、结束时间、房间信息客人卡的选定扇区
7. 注销卡片：将选定扇区的客人卡数据恢复到初始状态，即将开始时间、结束时间、房间信息置为FF
# 2 SDK说明
## 2.1 前言
本SDK旨在提供给深圳同创新佳科技有限公司，基于斯必拓高频读卡模块的设备对Mifare卡片制卡相关的业务功能
## 2.2 SDK集成
* 复制`LocStarCardDemo`中`app/libs/locstarcardApi_X.X.X.aar`到工程libs/目录下
* 在`module`的`gradle`中添加依赖：
```
dependencies {
    implementation(name: 'locstarcardApi_1.0.0', ext: 'aar')
}
```
## 2.3 总体说明
* `CardManager`是接口调用的统一对象
* `Result`是接口请求返回的基类
1. 返回结果编号
```
/**
 * 获取结果编号
 * @return 结果编号
 */
public int getResultCode();
```
结果编号：

| 代码                 | 编号        |说明             |
| ---------------      | ----------- | --------------- |
| STATUS_SUCC          | 10000       | 成功            |
| STATUS_AUTH_FAIL     | 20000       | 密码错误        |
| STATUS_NO_CARD_FOUND | 20001       | 没有发现卡片    |
| STATUS_DATA_LOSE     | 20002       | 读卡数据不完整  |
| STATUS_WRITE_FAIL    | 20003       | 写入数据失败    |
2. `GetCardIdResult`获取卡号结果，继承自`Result`
```
/**
 * 获取卡号
 * @return 卡号
 */
public String getCardId();
```
3. `ReadCardResult`读取客人卡结果，继承自`Result`
```
/**
 * 获取卡片信息
 * @return 卡片信息对象
 */
public CardInfo getCardInfo();
```
4. `CardInfo`为卡片信息对象
```
// 卡片是否已注销，true为已注销
public boolean isUnRegister();
// 卡号
public String getCardId()
// 房间号，如08050200表示：楼栋号为08，楼层号为05，房间号为02，子房间号为00
public String getRoomNum();
// 开始时间，毫秒数/1000
public long getStartTime();
// 结束时间，毫秒数/1000
public long getEndTime();
// 卡片是否已挂失，true为已挂失
public boolean isLoss();
```
## 2.3 接口API
### 2.3.1 SDK实例化
1. `CardManager.getInstance()`即可获取调用相关API的实例。

| API       | getInstance        | 备注     |
| --------  | --------           | -------- |
| 入参      | 无                 |          |
| 出参      | CardManager实例    |          |

### 2.3.2 SDK初始化
1. 请务必在应用程序初始化时调用此方法以进行SDK的初始化。

| API       | init               | 备注     |
| --------  | --------           | -------- |
| 入参      | 无                 |          |
| 出参      | 无                 |          |


2. 示例
```
CardManager.getInstance().init();
```
