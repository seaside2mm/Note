---
url: https://abcdxyzk.github.io/
title: kk Blog —— 通用基础
date: 2023-01-09 22:09:31
tag: 
summary: 2023-01-01 16:25:00
---
## [LVM 扩容](https://abcdxyzk.github.io/blog/2023/01/01/lvm-extend/)

2023-01-01 16:25:00

[https://www.cnblogs.com/yy9knsg/p/16552494.html](https://www.cnblogs.com/yy9knsg/p/16552494.html)

[https://tool.4xseo.com/article/200995.html](https://tool.4xseo.com/article/200995.html)

```
pvdisplay

vgdisplay

lvdisplay
```

#### 初始化分区

```
pvcreate /dev/vdx333
```

#### 将分区加入到虚拟卷组名

```
# vgextend 虚拟卷组名 新增的分区

vgextend centos /dev/sda3
```

#### 再次查看卷组情况

```
vgdisplay
```

#### 查看当前磁盘情况

```
df -h
记下需要扩展的文件系统名，例如 /dev/mapper/centos-root

lvdisplay
记下需要扩展的文件系统名，例如 /dev/centos/root
```

两个是一样的

```
ls /dev/centos/root -l
lrwxrwxrwx. 1 root root 7 Dec 20 20:11 /dev/centos/root -> ../dm-0

ls -l /dev/mapper/centos-root 
lrwxrwxrwx. 1 root root 7 Dec 20 20:11 /dev/mapper/centos-root -> ../dm-0
```

#### 扩容已有的卷组容量

```
lvextend -L +需要扩展的容量 需要扩展的文件系统名 

lvextend -L +29G /dev/mapper/centos-root
```

#### 以上只是卷的扩容，然后我们需要将文件系统扩容

```
cat /etc/fstab | grep centos-root

/dev/mapper/centos-root /                       xfs     defaults        0 0
```

这里可以看到，文件系统是 xfs，所以需要 xfs 的命令来扩展磁盘空间

```
xfs_growfs 文件系统名

xfs_growfs /dev/mapper/centos-root
```

df -h 可以看到，现在已经扩容成功了！

## [OBS 虚拟摄像头，实现合并多摄像头到腾讯会议等](https://abcdxyzk.github.io/blog/2022/12/30/base-obs/)

2022-12-30 16:52:00

OBS-Studio-25.0.8-Full-Installer-x64.exe

obs-virtualcam-2.0.5-Windows-installer.exe

#### 1 obs 添加两个视频捕获源

![](https://abcdxyzk.github.io/images/system/20221230-1.png)

#### 2 obs -> 工具 -> 虚拟摄像头

需要设置水平翻转

![](https://abcdxyzk.github.io/images/system/20221230-2.png)

#### 3 腾讯视屏选择 OBS-Camera

![](https://abcdxyzk.github.io/images/system/20221230-3.png)

## [PHPExcel 自动换行](https://abcdxyzk.github.io/blog/2022/12/29/lang-php-wrap/)

2022-12-29 16:58:00

```
$objSheet->getStyle('B')->getAlignment()->setWrapText(true);
```

## [PHPExcel 页面边距设置](https://abcdxyzk.github.io/blog/2022/12/29/lang-php-margin/)

2022-12-29 16:52:00

phpexcel 官网上找到了以下设置边距的代码, 且测试成功.

```
$excel = new PHPExcel(); 
$sheet = $excel->getActiveSheet(); 
$pageMargins = $sheet->getPageMargins(); 

// 设置边距为0.5厘米

(1英寸 = 2.54厘米)

$margin = 0.5 / 2.54; //phpexcel 中是按英寸来计算的,所以这里换算了一下 

$pageMargins->setTop($margin);       //上边距 
$pageMargins->setBottom($margin);    //下 
$pageMargins->setLeft($margin);      //左 
$pageMargins->setRight($margin);     //右 

$sheet->getPageSetup()->setFitToWidth('1');//自动填充到页面的宽度 
//$sheet->getPageSetup()->setFitToHeight('1');//自动填充到页面的高度 

$writer = PHPExcel_IOFactory::createWriter($excel, 'Excel5'); //生成一个新的xls文件 
$writer->save('test.xls');
```

## [phpExcel 使单元格部分文字加粗、放大、添加颜色](https://abcdxyzk.github.io/blog/2022/12/29/lang-php-richtext/)

2022-12-29 16:43:00

可以使用 phpExcel 的富文本进行操作

```
$objPHPExcel = new \PHPExcel();

//创建一个富文本对象 
$objRichText = new \PHPExcel_RichText(); 
$objRichText->createText('啊啊啊啊'); 

//需要改变大小或颜色的文字内容 
$objPayable = $objRichText->createTextRun('不不不'); 

//设置加粗
$objPayable->getFont()->setBold(true);
//设置斜体
$objPayable->getFont()->setItalic(true);
//设置文字大小
$objPayable->getFont()->setSize(15);
//设置颜色
$objPayable->getFont()->setColor( new \PHPExcel_Style_Color(\PHPExcel_Style_Color::COLOR_DARKGREEN) );

$objRichText->createText('超超超超');

$objPHPExcel->getActiveSheet()->getCell('A1')->setValue($objRichText);

$PHPWriter = \PHPExcel_IOFactory::createWriter($objPHPExcel, "Excel2007");
$PHPWriter->save('a.xlsx');
```