---
title: iOS调用三方地图实现导航及遇到的一些问题
tags: - 地图 -iOS
---

公司业务需要,最近研究了一下地图导航.以下是在开发中的使用笔记以及遇到的一些问题记录.

由于之前只做过一些简单的定位,导致现在一脸懵瞎,所以,只能慢慢的看文档了.

根据各方面的初步了解,结合公司需求太高,小虾米的我就决定偷懒,不接入三方了,直接跳到本地地图应用中去,然后你爱怎么用怎么用,喜欢哪个地图就用那个.

### 一、找到需要设置的地图的URL Scheme，配置plist文件
一些常用的地图的URL Scheme以及他们的官方字段说明链接:

- 百度地图: baidumap://
- Google地图: comgooglemaps-x-callback:// 或者 comgooglemaps://
- 腾讯地图: qqmap://
- 高德地图: iosamap://
- 苹果自带地图: baidumap://

### 二、检查目标地图应用在手机上有没有安装

使用下面的方法判断你是否安装了某个URL Scheme为appurlscheme的APP。
``` IDEA
//该方法返回一个布尔值,若为真,则该应用在我们手机上已经安装
[[UIApplication sharedApplication] canOpenURL:[NSURL URLWithString:@”appurlscheme://“]

示例:

//接下来弹出框的按钮文字的数组
NSMutableArray *array = [NSMutableArray array];
if ([[UIApplication sharedApplication] canOpenURL:[NSURL URLWithString:@”iosamap://“]]) {
[array addObject:@”高德地图”];
}
if ([[UIApplication sharedApplication] canOpenURL:[NSURL URLWithString:@”comgooglemaps://“]]) {
[array addObject:@”Google地图”];
}
if ([[UIApplication sharedApplication] canOpenURL:[NSURL URLWithString:@”baidumap://“]]) {
[array addObject:@”百度地图”];
}
if ([[UIApplication sharedApplication] canOpenURL:[NSURL URLWithString:@”qqmap://“]]) {
[array addObject:@”腾讯地图”];
}
//默认苹果自带地图和取消按钮
[array addObjectsFromArray:@[@”苹果地图”,@”取消”]];
```

### 三、配置你想要的弹出框样式
弹框样式各有所爱,这里给以个简单的样式,根据之前检测的按钮数组个数添加。
![地图入口](https://github.com/BlackChen/AllImages/blob/master/Markdown_Images/0087DA4144B99DC15E2BB096381A4A2B.png?raw=true)

### 四、跳转到目标地图
点击对应的地图时,我们需要通过以下方法转场到对应的地图APP

```
//appurlscheme:应用URL Scheme
//parameter:各个参数,下面的示例为基本配置,如需要更多,则请参考对应的官方文档字段说明
NSString *urlString = [[NSString stringWithFormat:@”appurlscheme://parameter] stringByAddingPercentEscapesUsingEncoding:NSUTF8StringEncoding];
[[UIApplication sharedApplication] openURL:[NSURL URLWithString:urlString]];

示例:

CLLocationCoordinate2D trans = CLLocationCoordinate2DMake(39.90868, 116.3956);//假如我们要去这个坐标
[self presentViewController:[BCAlertView presentAlertWithTitle:@”” message:@”请选择地图” actionTitles:array preferredStyle:UIAlertControllerStyleActionSheet handler:^(NSUInteger buttonIndex, NSString _Nonnull buttonTitle) {
[MBProgressHUD showHUDAddTo:self.ViewController Text:buttonTitle animated:YES mode:MBProgressHUDModeText after:2];
if ([buttonTitle isEqualToString:@”高德地图”]) {// ok GCJ-02
// sourceApplication=%@&backScheme=%@
// sourceApplication代表你自己APP的名称 会在之后跳回的时候显示出来 所以必须填写 backScheme是你APP的URL Scheme 不填是跳不回来的哟
// dev=0
// 这里填0就行了 跟上面的gcj02一个意思 1代表wgs84 也用不上
NSString urlString = [[NSString stringWithFormat:@”iosamap://navi?sourceApplication=%@&backScheme=%@&lat=%f&lon=%f&dev=0&style=2”,@”球猿”,@”BasketballApp://“,trans.latitude, trans.longitude] stringByAddingPercentEscapesUsingEncoding:NSUTF8StringEncoding];
[[UIApplication sharedApplication] openURL:[NSURL URLWithString:urlString]];
}else if([buttonTitle isEqualToString:@”Google地图”]){// 坐标转换,位置不准 GCJ-02
// x-source=%@&x-success=%@
// 跟高德一样 这里分别代表APP的名称和URL Scheme
// saddr=
// 这里留空则表示从当前位置触发
NSString urlString = [[NSString stringWithFormat:@”comgooglemaps-x-callback://?x-source=%@&x-success=%@?resume=true&daddr=%@&center=%f,%f”,@”球猿”,@”BasketballApp://“, _locationName, gpsCoor.latitude, gpsCoor.longitude] stringByAddingPercentEscapesUsingEncoding:NSUTF8StringEncoding];
[[UIApplication sharedApplication] openURL:[NSURL URLWithString:urlString]];
}else if([buttonTitle isEqualToString:@”百度地图”]){//ok BD-09
// origin=
// 这个是不能被修改的 不然无法把出发位置设置为当前位置
// destination=latlng:%f,%f|name=目的地
// name=XXXX name这个字段不能省略 否则导航会失败 而后面的文字则可以随便填
// coord_type=gcj02
// coord_type允许的值为bd09ll、gcj02、wgs84 如果你APP的地图SDK用的是百度地图SDK 请填bd09ll 否则 就填gcj02 wgs84你基本是用不上了(关于地图加密这里也不多谈 请自行学习)
NSString urlString = [[NSString stringWithFormat:@”baidumap://map/direction?origin=&destination=latlng:%f,%f|name=%@&mode=driving&coord_type=bd09ll”,_coordinatePoit.latitude, _coordinatePoit.longitude, _locationName] stringByAddingPercentEscapesUsingEncoding:NSUTF8StringEncoding];
[[UIApplication sharedApplication] openURL:[NSURL URLWithString:urlString]];
}else if([buttonTitle isEqualToString:@”腾讯地图”]){//http://lbs.qq.com/uri_v1/guide-route.html
NSString urlString = [[NSString stringWithFormat:@”qqmap://map/routeplan?referer=%@&type=drive&from=我的位置&to=%@&tocoord=%f,%f&coord_type=1&policy=0”,@”球猿”,_locationName, trans.latitude, trans.longitude] stringByAddingPercentEscapesUsingEncoding:NSUTF8StringEncoding];
[[UIApplication sharedApplication] openURL:[NSURL URLWithString:urlString]];
}else if([buttonTitle isEqualToString:@”苹果地图”]){//https://developer.apple.com/library/content/featuredarticles/iPhoneURLScheme_Reference/MapLinks/MapLinks.html
MKMapItem currentLocation = [MKMapItem mapItemForCurrentLocation];
MKMapItem *toLocation = [[MKMapItem alloc] initWithPlacemark:[[MKPlacemark alloc] initWithCoordinate:CLLocationCoordinate2DMake(trans.latitude, trans.longitude) addressDictionary:nil]];
toLocation.name = _locationName;
[MKMapItem openMapsWithItems:@[currentLocation, toLocation] launchOptions:@{MKLaunchOptionsDirectionsModeKey: MKLaunchOptionsDirectionsModeDriving,
MKLaunchOptionsShowsTrafficKey: [NSNumber numberWithBool:YES]}];
}
}] animated:YES completion:^{
}];

```

### 五、回到自己的应用
这个问题我暂时不知道怎么弄,目前还只有通过进入地图后的左上角,我们应用名称点击返回,有知道的小伙伴联系我哦
- QQ: 205901721
- 微信: Black_Chen_

### 六、一些延展
到这里基本跳转应该没有问题了,下面是一些功能扩展:

小伙伴们是否发现了一个问题:我么既然做地图导航,那么目的就很简单,最重要的是要让我们的客户精确的定位到目的地,到西安路尽可能近,尽可能节省时间,是吧?

然而,经过上面的配置后,如果你用的是百度地图的坐标,那么在高德,Google,腾讯以及苹果自带地图上面,地图位置是不是和我们标记或者预想的相差好几条街,有米有?

所以,强烈建议小伙伴们查看一下下面的文章,普及一下知识:
[坐标转换知识详解](https://www.jianshu.com/p/abdb35b0ba78)

下面附上我的坐标转换代码(本人用的是百度地图,目前转Google地图还不太精确,有知道的小伙伴请告知^_^)
- .h文件
```
@interface BCLocationTransform : NSObject
@property (nonatomic, assign) double latitude;
@property (nonatomic, assign) double longitude;
-(id)initWithLatitude:(double)latitude andLongitude:(double)longitude;
/
坐标系：
WGS-84：是国际标准，GPS坐标（Google Earth使用、或者GPS模块）
GCJ-02：中国坐标偏移标准，Google Map、高德、腾讯使用
BD-09 ：百度坐标偏移标准，Baidu Map使用 /
// 从GPS坐标转化到高德坐标
-(id)transformFromGPSToGD;
// 从高德坐标转化到百度坐标
-(id)transformFromGDToBD;
// 从百度坐标到高德坐标
-(id)transformFromBDToGD;
//从高德坐标到GPS坐标
-(id)transformFromGDToGPS;
// 从百度坐标到GPS坐标
-(id)transformFromBDToGPS;
@end
```
- .m文件
```
static const double a = 6378245.0;
static const double ee = 0.00669342162296594323;
static const double pi = M_PI;
static const double xPi = M_PI 3000.0 / 180.0;
@implementation BCLocationTransform
-(id)initWithLatitude:(double)latitude andLongitude:(double)longitude {
if (self = [super init]) {
self.latitude = latitude;
self.longitude = longitude;
}
return self;
}
-(id)transformFromGPSToGD {
CLLocationCoordinate2D coor = [BCLocationTransform transformFromWGSToGCJ:CLLocationCoordinate2DMake(self.latitude, self.longitude)];
return [[BCLocationTransform alloc] initWithLatitude:coor.latitude andLongitude:coor.longitude];
}
-(id)transformFromGDToBD {
CLLocationCoordinate2D coor = [BCLocationTransform transformFromGCJToBaidu:CLLocationCoordinate2DMake(self.latitude, self.longitude)];
return [[BCLocationTransform alloc] initWithLatitude:coor.latitude andLongitude:coor.longitude];
}
-(id)transformFromBDToGD {
CLLocationCoordinate2D coor = [BCLocationTransform transformFromBaiduToGCJ:CLLocationCoordinate2DMake(self.latitude, self.longitude)];
return [[BCLocationTransform alloc] initWithLatitude:coor.latitude andLongitude:coor.longitude];
}
-(id)transformFromGDToGPS {
CLLocationCoordinate2D coor = [BCLocationTransform transformFromGCJToWGS:CLLocationCoordinate2DMake(self.latitude, self.longitude)];
return [[BCLocationTransform alloc] initWithLatitude:coor.latitude andLongitude:coor.longitude];
}
-(id)transformFromBDToGPS {
//先把百度转化为高德
CLLocationCoordinate2D start_coor = [BCLocationTransform transformFromBaiduToGCJ:CLLocationCoordinate2DMake(self.latitude, self.longitude)];
CLLocationCoordinate2D end_coor = [BCLocationTransform transformFromGCJToWGS:CLLocationCoordinate2DMake(start_coor.latitude, start_coor.longitude)];
return [[BCLocationTransform alloc] initWithLatitude:end_coor.latitude andLongitude:end_coor.longitude];
}
+(CLLocationCoordinate2D)transformFromWGSToGCJ:(CLLocationCoordinate2D)wgsLoc {
CLLocationCoordinate2D adjustLoc;
if([self isLocationOutOfChina:wgsLoc]) {
adjustLoc = wgsLoc;
}
else {
double adjustLat = [self transformLatWithX:wgsLoc.longitude - 105.0 withY:wgsLoc.latitude - 35.0];
double adjustLon = [self transformLonWithX:wgsLoc.longitude - 105.0 withY:wgsLoc.latitude - 35.0];
long double radLat = wgsLoc.latitude / 180.0 pi;
long double magic = sin(radLat);
magic = 1 - ee magic magic;
long double sqrtMagic = sqrt(magic);
adjustLat = (adjustLat 180.0) / ((a (1 - ee)) / (magic sqrtMagic) pi);
adjustLon = (adjustLon 180.0) / (a / sqrtMagic cos(radLat) pi);
adjustLoc.latitude = wgsLoc.latitude + adjustLat;
adjustLoc.longitude = wgsLoc.longitude + adjustLon;
}
return adjustLoc;
}
+(double)transformLatWithX:(double)x withY:(double)y {
double lat = -100.0 + 2.0 x + 3.0 y + 0.2 y y + 0.1 x y + 0.2 sqrt(fabs(x));
lat += (20.0 sin(6.0 x pi) + 20.0 sin(2.0 x pi)) 2.0 / 3.0;
lat += (20.0 sin(y pi) + 40.0 sin(y / 3.0 pi)) 2.0 / 3.0;
lat += (160.0 sin(y / 12.0 pi) + 320 sin(y pi / 30.0)) 2.0 / 3.0;
return lat;
}
+(double)transformLonWithX:(double)x withY:(double)y {
double lon = 300.0 + x + 2.0 y + 0.1 x x + 0.1 x y + 0.1 sqrt(fabs(x));
lon += (20.0 sin(6.0 x pi) + 20.0 sin(2.0 x pi)) 2.0 / 3.0;
lon += (20.0 sin(x pi) + 40.0 sin(x / 3.0 pi)) 2.0 / 3.0;
lon += (150.0 sin(x / 12.0 pi) + 300.0 sin(x / 30.0 pi)) 2.0 / 3.0;
return lon;
}
+(CLLocationCoordinate2D)transformFromGCJToBaidu:(CLLocationCoordinate2D)p {
long double z = sqrt(p.longitude p.longitude + p.latitude p.latitude) + 0.00002 sqrt(p.latitude pi);
long double theta = atan2(p.latitude, p.longitude) + 0.000003 cos(p.longitude pi);
CLLocationCoordinate2D geoPoint;
geoPoint.latitude = (z sin(theta) + 0.006);
geoPoint.longitude = (z cos(theta) + 0.0065);
return geoPoint;
}
+(CLLocationCoordinate2D)transformFromBaiduToGCJ:(CLLocationCoordinate2D)p {
double x = p.longitude - 0.0065, y = p.latitude - 0.006;
double z = sqrt(x x + y y) - 0.00002 sin(y xPi);
double theta = atan2(y, x) - 0.000003 cos(x xPi);
CLLocationCoordinate2D geoPoint;
geoPoint.latitude = z sin(theta);
geoPoint.longitude = z cos(theta);
return geoPoint;
}
+(CLLocationCoordinate2D)transformFromGCJToWGS:(CLLocationCoordinate2D)p {
double threshold = 0.00001;
// The boundary
double minLat = p.latitude - 0.5;
double maxLat = p.latitude + 0.5;
double minLng = p.longitude - 0.5;
double maxLng = p.longitude + 0.5;
double delta = 1;
int maxIteration = 30;
// Binary search
while(true) {
CLLocationCoordinate2D leftBottom = [[self class] transformFromWGSToGCJ:(CLLocationCoordinate2D){.latitude = minLat,.longitude = minLng}];
CLLocationCoordinate2D rightBottom = [[self class] transformFromWGSToGCJ:(CLLocationCoordinate2D){.latitude = minLat,.longitude = maxLng}];
CLLocationCoordinate2D leftUp = [[self class] transformFromWGSToGCJ:(CLLocationCoordinate2D){.latitude = maxLat,.longitude = minLng}];
CLLocationCoordinate2D midPoint = [[self class] transformFromWGSToGCJ:(CLLocationCoordinate2D){.latitude = ((minLat + maxLat) / 2),.longitude = ((minLng + maxLng) / 2)}];
delta = fabs(midPoint.latitude - p.latitude) + fabs(midPoint.longitude - p.longitude);
if(maxIteration– <= 0 || delta <= threshold) {
return (CLLocationCoordinate2D){.latitude = ((minLat + maxLat) / 2),.longitude = ((minLng + maxLng) / 2)};
}
if(isContains(p, leftBottom, midPoint)) {
maxLat = (minLat + maxLat) / 2;
maxLng = (minLng + maxLng) / 2;
} else if(isContains(p, rightBottom, midPoint)) {
maxLat = (minLat + maxLat) / 2;
minLng = (minLng + maxLng) / 2;
} else if(isContains(p, leftUp, midPoint)) {
minLat = (minLat + maxLat) / 2;
maxLng = (minLng + maxLng) / 2;
} else {
minLat = (minLat + maxLat) / 2;
minLng = (minLng + maxLng) / 2;
}
}
}

#pragma mark - 判断某个点point是否在p1和p2之间
static bool isContains(CLLocationCoordinate2D point, CLLocationCoordinate2D p1, CLLocationCoordinate2D p2) {
return (point.latitude >= MIN(p1.latitude, p2.latitude) && point.latitude <= MAX(p1.latitude, p2.latitude)) && (point.longitude >= MIN(p1.longitude,p2.longitude) && point.longitude <= MAX(p1.longitude, p2.longitude));
}

#pragma mark - 判断是不是在中国
+(BOOL)isLocationOutOfChina:(CLLocationCoordinate2D)location {
if (location.longitude < 72.004 || location.longitude > 137.8347 || location.latitude < 0.8293 || location.latitude > 55.8271)
return YES;
return NO;
}
@end
```
- 使用
```
//初始化坐标
BCLocationTransform *trans = [[BCLocationTransform alloc]initWithLatitude:_coordinatePoit.latitude andLongitude:_coordinatePoit.longitude];
//转换坐标
trans = [trans transformFromBDToGD];
```
