[TOC]
# 1 DEMO使用说明
1. 在进行以下操作时，请选择扇区，默认扇区为11扇区
2. 使用前，客人卡和授权卡的密码B均需提前置为约定的默认密码，由于所有操作均为主动与卡片交互，请在进行以下任何操作前将卡片靠近读卡区
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
### 2.3.1 返回结果编号
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
| STATUS_ROOM_NUM_FORMAT_ERROR    | 20004       | 房间号格式错误    |
| STATUS_NOT_AUTH_CARD | 20005       | 获取授权时读取到的卡片不是授权卡    |
### 2.3.2 `GetCardIdResult`获取卡号结果，继承自`Result`
```
/**
 * 获取卡号
 * @return 卡号
 */
public String getCardId();
```
### 2.3.3 `ReadCardResult`读取客人卡结果，继承自`Result`
```
/**
 * 获取卡片信息
 * @return 卡片信息对象
 */
public CardInfo getCardInfo();
```
`CardInfo`卡片信息对象：
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
## 2.4 接口API
### 2.4.1 SDK实例化
1. 说明：获取CardManager调用相关API的实例。

| API       | getInstance        | 备注     |
| --------  | --------           | -------- |
| 入参      | 无                 |          |
| 出参      | CardManager实例    |          |
2. 示例
```
CardManager.getInstance();
```
### 2.4.2 SDK初始化
1. 说明：请务必在应用程序初始化时调用此方法以进行SDK的初始化。

| API       | init               | 备注     |
| --------  | --------           | -------- |
| 入参      | Context            | 上下文   |
| 出参      | 无                 |          |
2. 示例
```
CardManager.getInstance().init(this);
```
### 2.4.3 设置Debug
1. 说明：Debug模式设置为true时，sdk里面的日志将会打印输出

| API       | setDebug     | 备注              |
| --------  | --------     | --------          |
| 入参      | boolean debug        | 是否开启debug模式 |
| 出参      | 无           |                   |
2. 示例
```
CardManager.getInstance().setDebug(false);
```
### 2.4.4 设置扇区
1. 说明：设置扇区编号，本sdk涉及的Mifare标准卡扇区编号为0~15的整数

| API       | setSectorIndex     | 备注     |
| --------  | --------           | -------- |
| 入参      | int sectorIndex        | 扇区编号 |
| 出参      | 无                 |          |
2. 示例
```
CardManager.getInstance().setSectorIndex(sectorIndex);
```
### 2.4.5 获取授权
1. 说明：通过密码B获取授权卡中的授权码，并将授权码保存到sdk，供客人卡读卡以及写入客人卡使用

| API       | getAuth      | 备注     |
| --------  | --------           | -------- |
| 入参      | 无            |   |
| 出参      | Result| 见`2.3.1`         |
2. 示例
```
Result result = CardManager.getInstance().getAuth();
if (result.getResultCode() != Result.STATUS_SUCC ) {
    showToast("获取授权失败，错误代码：" + result.getResultCode());
} else {
    showToast("获取授权成功");
}
```
### 2.4.6 读卡
1. 说明：通过密码A获取客人卡的卡片信息，包含其开始结束时间，房号，是否挂失等

| API       | readCard      | 备注     |
| --------  | --------           | -------- |
| 入参      | 无            |   |
| 出参      | ReadCardResult| 见`2.3.3 `         |
2. 示例
```
ReadCardResult result = CardManager.getInstance().readCard();
if (result.getResultCode() == Result.STATUS_SUCC && result.getCardInfo() != null) {
    CardInfo cardInfo = result.getCardInfo();
    tvTip.append("读卡成功");
    tvResult.append("\n\n卡号：" + cardInfo.getCardId());
    if (!cardInfo.isUnRegister()) {
        tvResult.append("\n\n开始时间：" + DateUtil.longToString(cardInfo.getStartTime()));
        tvResult.append("\n\n结束时间：" + DateUtil.longToString(cardInfo.getEndTime()));
        tvResult.append("\n\n房间号：" + cardInfo.getRoomNum());
        tvResult.append("\n\n是否挂失：" + (cardInfo.isLoss() ? "是" : "否"));
    } else {
        tvResult.append("\n\n此卡片已注销");
    }
    tvTip.setTextColor(Color.GREEN);
} else {
    tvTip.append("错误代码：" + result.getResultCode());
    tvTip.setTextColor(Color.RED);
}
```
### 2.4.7 写卡
1. 说明：将开始时间、结束时间、房号、是否挂失、授权码等客人信息通过客人卡密码B写入客人卡

| API       | writeCard           | 备注     |
| --------  | --------            | -------- |
| 入参      | long start          | 开始时间 |
|           | long end            | 结束时间 |
|           | int buildNum        | 楼栋号   |
|           | int floorNum        | 楼层号   |
|           | int houseNum        | 房间号   |
|           | int childHouseNum   | 子房号   |
| 出参      | Result              | 见`2.3.1 `|
2. 备注：
* 开始时间与结束时间为时间毫秒数/1000
* 房间号格式必须按照`XX-XX-XX-XX`格式传入，如：`1-20-3-40`
3. 示例
```
Result result = CardManager.getInstance().writeCard(start, end, buildNum + "-" + floorNum + "-" + houseNum + "-" + childHouseNum);
if (result.getResultCode() == Result.STATUS_SUCC) {
    showToast("写卡成功");
    onBackPressed();
} else {
    showToast("写卡失败，错误代码：" + result.getResultCode());
}
```
### 2.4.8 注销卡片
1. 说明：通过密码B将客人卡的第0块、第1块、第2块数据全部写为`FF`

| API       | cancelCard      | 备注     |
| --------  | --------           | -------- |
| 入参      | 无            |   |
| 出参      | Result| 见`2.3.1 `         |
2. 示例
```
Result result = CardManager.getInstance().cancelCard();
if (result.getResultCode() != Result.STATUS_SUCC ) {
    showToast("注销失败，错误代码：" + result.getResultCode());
} else {
    showToast("注销成功");
}
```