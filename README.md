
#### 一、简介及运行环境
##### 概述
```
本文档是基于BLE蓝牙的iOS SDK用户指南，描述了开启扫描、停止扫描、开始搜索指定设备、连接指定设备、发送广播数据、
停止发送广播数据等相关接口的使用说明。
```
##### 兼容性
```
类别	兼容范围
系统	支持iOS 8.0 以上版本
机型	上市的iPhone手机和iPad。
硬件要求	要求设备支持蓝牙BLE4.0
网络	无要求
开发环境	建议使用Xcode 8.0以上版本 进行开发
```
##### DEMO压缩包说明
DEMO压缩包下载即可运行，其中DEMO内已经附带了SDK的库。
* advertise_sdk.framework位于AdvertiseWireless/advertise_sdk目录下
* advertise_sdk.库接入目录位置参考DEMO

#### 二、接口使用及调用流程
##### 提示：先检测蓝牙并打开蓝牙(非SDK相关，具体参考DEMO)
#####            扫描结果，连接结果，接收数据可通过block回调，也可通过代理回调
#####            使用代理方式需要设置代理
##### 1、SDK参数配置
```
/**扫描周期，默认10s */
    #define ScanPeriod 10
/**是否开启打印日志，默认开启*/
    #define PrintLog YES
/**设备前缀名称*/
    #define PrefixAvertiseName @"BT_CAR"
```

##### 2、SDK初始化
```
/**
实例化对象

@return XDWirelessManager单例对象
*/
+ (instancetype)shareInstance;
```

##### 3、扫描指定目标设备
```
/**
 寻找设备,回调的设备保护未连接或已连接两种类型。具体
 属性查看XDDeviceModel对象属性定义

 @param disCoverCallBack 寻找到的设备回调
*/

-(void)searchDeivce:(void(^)(XDDeviceModel *deviceModel))disCoverCallBack;
```
##### 4、连接指定目标设备
```
/**
连接指定目标设备

@param deviceModel 需要连接的设备对象
@param connectSuccessCallBack 成功回调
@param connectFail 连接失败回调
*/
-(void)connectDevice:(XDDeviceModel *)deviceModel
      connectSuccess:(void(^)(XDDeviceModel *deviceModel))connectSuccessCallBack
         connectFail:(void(^)(XDDeviceModel *deviceModel,NSError *error))connectFail;
```
##### 5、发送广播包数据
```
/**
发送数据
@param sendBuffer 装载数据  16byte
根据协议自定义填充数据
*/
-(void)startAdvertisingWithData:(NSData *)sendBuffer;
```
##### 6、停止发送广播包
```
/**
停止发送数据
*/
-(void)stopAdvertising;

```
##### 7、开启蓝牙扫描(APP和2.4G设备双向交互时，必须开启蓝牙扫描，否则APP收不到2.4G设备的回复)
```
/**
开启蓝牙扫描，开启后才能接收数据
*/
-(void)starScan;


/**
停止扫描，停止后无法接收数据
*/
-(void)stopScan;
```
#### 三、自定义协议说明
```
如:
byte0 - byte15 为 K1 X1 X2 X3 C0 K2 K3 H1 H2 H3 H4 00 00 Y1 Y2 Y3
K1数据为随机数，00~255
K2数据为帧序号，只要确定用户按下app的按键或者松开，触发手机发送广播时数据就累加1，否则就继续保持当前值发送出去
X1 X2 X3为对码时收到的2.4G终端的滚码
Y1,Y2,Y3为2.4G在对码时刻收到的手机的码
C0 代表APP添加属性
H1~H4为记录属性状态：
H1为前进属性；H2为后退属性；H3为左转属性；H4为右转属性，对应的BYTE为00表示没有此功能，为01表示有此功能。
手机发送给硬件时：K3为0x01
硬件回复给手机时：K3为0x11 (手机收到此码后退出属性设置模式)
```
