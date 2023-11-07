---
title: Windows通用应用程序开发(三)
categories:
  - Windows应用程序开发
tags:
  - UWP
sticky: 100
comments: true
toc: true
only:
  - home
  - category
  - tag
abbrlink: 50fedeca
date: 2022-12-10 08:54:21
pic:
---
8.1变化特效
当使用Canvas布局时,可以使用Canvas.Left和Canvas.Top属性改变一个对象相对Canvas的位置,当使用Grid或StackPanel布局时,可以使用Margin属性声明元素的各方向间距,然而在这些属性中并不包括直接去改变某个Win10UI对象形状的方法,变化的实现会依赖于RenderTransform类,RenderTransform包含的变换属性成员就是专门来改变Win10UI对象形状的,它可以实现对元素拉伸,旋转,扭曲等效果,同时变换特效也常用于辅助产生各种动画效果.
8.11.变换的原理二维变换矩阵
变换定义如何将一个坐标空间中的点映射或变化到另一个坐标空间,在Windows 10里通过仿射变换实现.
仿射变换可以理解为对坐标进行放缩,旋转,平移后取得新坐标的值,仿射变换可以让UI元素产生视觉的旋转,它的原理并不是让UI元素的位置变化,而是变化平面X,Y的坐标系,间接的让UI元素的坐标发生转变,而如何让坐标系的旋转精确的控制UI元素的旋转,这就是仿射变换矩阵的作用了.
Windows10变换映射是由变换Matrix来描述,Matrix是表示用于二维空间变换的3X3仿射变换矩阵,3X3矩阵用于在二维平面中进行变换.通过让仿射变换矩阵相乘可形成任意数目的线性变换,例如先旋转扭曲,再平移,仿射矩阵变换用于操作二维平面中的对象或坐标系,由于仿射变换时,平行的边依然平行,所以我们无法对一个矩形的位图进行随意变换,比如无法拉伸一个角,也无法把它变成梯形,仿射变换矩阵的最后一列等于0，0，1,
因此只需指定前两列的成员.矢量用行矢量表示
TranslateTransform 能够让某对象的位置发生平移变化
RotateTransform 能够让某对象产生旋转变化,根据中心点进行顺时针旋转或逆时针旋转 
ScaleTransform 能够让某对象产生缩放变化
SkewTransform 能够让某对象产生扭曲变化
TransformGroup 能够让某对象缩放,旋转,扭曲等变化效果合并起来使用.
MatrixTransform 能够让某对象通过矩阵算法实现更为复杂的变形
变换元素包括平移变换,旋转变换,缩放变换,扭曲变换,矩形变换和组合变换元素,变换特效常用于在不改变自身的情况下,使对象产生变形效果,所以变换元素常辅助产生win10中的各种动画效果
8.12.平移变换
TranslateTransform 能够让某对象的位置发生平移变化,按指定的X和Y移动元素,TranslateTransform类对移动不支持绝对定位的面板内的元素特别有用.
通过将TranslateTransform应用到元素的RenderTransform属性,可以移动StackPanel内的元素,使用TranslateTransform的X属性指定将元素沿X轴移动的量,使用Y属性指定将元素沿Y轴移动的量,最后将TranslateTransform应用于元素的RenderTransform属性.
``` bash
<Rectangle Height="50" Width="50" Fill="#CCCCCCFF" Stroke="Blue" StrokeThickness="2" Canvas.Left="100" Canvas.Top="100">
                    <Rectangle.RenderTransform>
                        <TranslateTransform X="50" Y="50"/>
                    </Rectangle.RenderTransform>
                </Rectangle>
            </PivotItem>
```
8.13.旋转变换(RotateTransform)
RotateTransform:能够让某对象产生旋转变化,根据中心点进行顺时针旋转或逆时针旋转,按指定的Angle旋转元素,RotateTransform围绕点CenterX和CenterY将对象旋转指定的Angle.在使用RotateTransform时,请注意变换将围绕点(0,0)旋转某个特定对象的坐标系.因此,根据对象的位置,对象可能不会就地围绕其中心旋转.例如,如果对象位于X轴上距0为200个单位的位置,旋转30°可以让该对象沿着原点为圆心,以200为半径所画的圆摆动30°.若要就地旋转某个对象,请将RotateTransform的CenterX和CenterY设置为该对象的旋转中心.
以Polyline对象的左上角为旋转点将其旋转了45°.
``` bash
<Canvas height="200" Width="200">
	<!--以Polyline 对象的左上角(0,0)为旋转点将其旋转了45°-->
	<Polyline Points="25,25,0,50,25,75,50,50,25,25,25,0" Stroke="Blue"
StrokeThickness="10" Canvas.Left="75" Canvas.Top="50">
	<Polyline.RenderTransform>
		<RotateTransform CenterX="0" CenterY="0" Angle="45"/>
	</Polyline.RenderTransform>
</Canvas>
<Canvas Height="200" Width="200">
                        <Polyline Points="25,25,0,50,25,75,50,50,25,25,25,0"
                                  Stroke="Blue" StrokeThickness="10" Canvas.Left="75" Canvas.Top="50">
                            <Polyline.RenderTransform>
                                <RotateTransform CenterX="25" CenterY="50" Angle="45"/>
                            </Polyline.RenderTransform>
                        </Polyline>
                    </Canvas>
```
8.14.缩放变换(ScaleTransform)
ScaleTransform 能够让某对象产生缩放变化,按指定的ScaleX和ScaleY量按比例缩放元素.使用ScaleX和ScaleY量按比例缩放元素.使用ScaleX和ScaleY属性可以按照指定的系数调整元素的大小.例如,ScaleX值为1.5时,会将元素拉伸到其原始宽度的150%.ScaleY值为0.5时,会将元素的高度缩小50%.使用CenterX和CenterY属性可以指定缩放操作的中心点,默认情况下,ScaleTransform的中心点是(0,0),该点与矩形的左上角相对应,这会导致该元素移动并使其看上去更大,原因是当应用Transform时,对象所在的坐标空间会改变.
使用ScaleTransform将长和宽均为50的Rectangle尺寸放大一倍,对于CenterX和CenterY来说,ScaleTransform的值均为0.
``` bash
 <Canvas Height="200" Width="200">
                        <Rectangle Height="50" Width="50" Fill="#CCCCCCFF" Stroke="Blue" StrokeThickness="2" Canvas.Left="100" Canvas.Top="100">
                            <Rectangle.RenderTransform>
                                <ScaleTransform CenterX="0" CenterY="0" ScaleX="2" ScaleY="2"/>
                            </Rectangle.RenderTransform>
                            
                        </Rectangle>
                        <Rectangle Height="50" Width="50" Fill="#CCCCCCFF" Canvas.Left="100" Canvas.Top="100" Stroke="Blue" StrokeThickness="2">
                            <Rectangle.RenderTransform>
                                <ScaleTransform CenterX="25"
                                                CenterY="25"
                                                ScaleX="2"
                                                ScaleY="2"/>
                            </Rectangle.RenderTransform>
                        </Rectangle>
                    </Canvas>
```
8.15.扭曲变换(SkewTransform)
SkewTransform:能够让某对象产生扭曲变化,按指定的AngleX和AngleY量扭曲元素.扭曲是一种以非均匀方式拉伸坐标空间的变换.SkewTransform的一个典型用法是在二维对象中模拟三维深度.使用CenterX和CenterY属性可以指定SkewTransform的中心点,使用AngleX和AngleY属性可以指定X轴和Y轴的扭曲角度.
使当前坐标系沿着这些轴扭曲,若要预测扭曲变换的效果,请考虑AngleX相对于原始坐标系扭曲X轴的值,因此，如果AngleY为30,则会将该形状的Y轴值从原点扭曲30°.在X或Y轴中将坐标系变换30°效果不相同.
``` bash
<Rectangle height="50" width="50" Fill="#CCCCCCFF" Stroke="Blue" Strokethickness="2"
Canvas.Left="100" Canvas.Top="100">
	<Rectangle.RenderTransform>
		<SkewTransform CenterX="0" CenterY="0" AngleX="45" AngleY="0"/>
	</Rectangle.RenderTransform>
</Rectangle>
```
8.16.组合变换(TransformGroup)
TransformGroup:能够让某对象的缩放,扭曲等变化效果合并起来使用,将多个TransformGroup对象组合为可以随后应用于变换属性的单一Transform,在复合变换中,单个变换的顺序非常重要.例如,依次旋转,缩放,平移与依次平移,旋转和缩放得到的结果不同,造成顺序很重要的一个原因就是像旋转和缩放这样的变换是针对坐标系的原点进行的,缩放以原点为中心的对象与缩放已离开原点的对象所得到的结果不同.同样,旋转以原点为中心的对象与旋转已离开原点的对象所得到的结果也不同.
``` bash
 <Button RenderTransformOrigin="0.5,0.5" HorizontalAlignment="Center">Click
                    <Button.RenderTransform>
                        <TransformGroup>
                            <ScaleTransform ScaleY="3"/>
                            <RotateTransform Angle="45"/>
                        </TransformGroup>
                    </Button.RenderTransform>
                </Button>
```
8.17.矩阵变换(MatrixTransform)
MatrixTransform是通过矩阵演算的方式来计算变换后的坐标
``` bash
<MatrixTransform Matrix="M11,M12,M21,M22,OffsetX,OffsetY"/>
<Button MinWidth="100">Click
                    <Button.RenderTransform>
                        <MatrixTransform x:Name="myMatrixTransform">
                            <MatrixTransform.Matrix>
                                <Matrix OffsetX="10" OffsetY="100" M11="3" M12="2"/>
                            </MatrixTransform.Matrix>
                        </MatrixTransform>
                    </Button.RenderTransform>
                </Button>

```
8.2.三维特效
Win10UI元素的三维特效是通过UIElement的PlaneProjection属性来进行设置的.
8.22.三维旋转
PlaneProjection三维旋转相关的属性
1.CenterOfRotationX/CenterOfRotationY/CenterOfRotatinZ.表示旋转中心X轴/Y轴/Z轴坐标
可以通过使用这三个属性,设置三维旋转的旋转中心,默认情况下,旋转轴直接穿过对象的中心,这表示对象围绕其中心旋转.CenterOfRotationX和CenterOfRotationY的默认值为0.5,而CenterOfRotationZ的默认值为0.
CenterOfRotationX取值0表示UI元素最左边的边缘,1是UI元素最右边的边缘.
CenterOfRotationY取值0表示UI元素最上边的边缘,1是UI元素最上边的边缘.
CenterOfRotationZ取值为负表示将旋转中心移到该对象后面,取值为正数表示将旋转中心移到该对象上方.
2.RotationX/RotationY/RotationZ表示沿X/Y/Z轴旋转的角度.
默认值都是0 可以超过360.
``` bash
 <Grid x:Name="SanWeiContentPanel" Margin="12,0,12,0">
                    <Grid.RowDefinitions>
                        <RowDefinition Height="*"/>
                        <RowDefinition Height="Auto"/>
                    </Grid.RowDefinitions>
                    <!--旋转的对象-->
                    <TextBlock Grid.Row="0" Text="3D" FontSize="120" Foreground="Red" HorizontalAlignment="Center" VerticalAlignment="Center">
                        <TextBlock.Projection>
                            <PlaneProjection x:Name="planeProjection"/>
                        </TextBlock.Projection>
                    </TextBlock>
                    <StackPanel Grid.Row="1">
                        <StackPanel Orientation="Horizontal">
                            <RadioButton x:Name="rotationRadioButton" Content="Rotation" Checked="rotationRadioButton_Checked"/>
                            <RadioButton x:Name="centerOfRotationRadioButton"
                                         Content="CenterOfRotation" Checked="centerOfRotationRadioButton_Checked"/>
                            
                        </StackPanel>
                        <TextBlock x:Name="infoTextBlock" TextWrapping="Wrap"/>
                        <TextBlock x:Name="xTextBlock" Text="沿着X轴旋转"/>
                        <Slider x:Name="xSlider" Minimum="0" Maximum="100" ValueChanged="xSlider_ValueChanged"/>
                        <TextBlock x:Name="yTextBlock" Text="沿着Y轴旋转"/>
                        <Slider x:Name="ySlider" Minimum="0" Maximum="100" ValueChanged="ySlider_ValueChanged"/>
                        <TextBlock x:Name="zTextBlock" Text="沿着Z轴旋转"/>
                        <Slider x:Name="zSlider" Minimum="0" Maximum="100" ValueChanged="zSlider_ValueChanged"/>

                    </StackPanel>
                </Grid>

 //X滑块的滑动事件
        private void xSlider_ValueChanged(object sender, RangeBaseValueChangedEventArgs e)
        {
            if (centerOfRotationRadioButton.IsChecked==false)
            {
                //Slider控制的值范围是0-100,所以旋转的角度控制在0-360
                double xValue = e.NewValue * 360 / 100;
                planeProjection.RotationX = xValue;
            }
            else
            {
                //中心点的坐标控制在0-1的值范围中变动
                planeProjection.CenterOfRotationX = e.NewValue / 100;
                
            }
        }

        private void ySlider_ValueChanged(object sender, RangeBaseValueChangedEventArgs e)
        {
            if (centerOfRotationRadioButton.IsChecked==false)
            {
                double yValue = e.NewValue * 360 / 100;
                planeProjection.RotationY = yValue;
            }
            else
            {
                planeProjection.CenterOfRotationY = e.NewValue / 100;

            }
        }

        private void zSlider_ValueChanged(object sender, RangeBaseValueChangedEventArgs e)
        {
            if (centerOfRotationRadioButton.IsChecked==false)
            {
                double zValue = e.NewValue * 360 / 100;
                planeProjection.RotationZ = zValue;
            }
            else
            {
                planeProjection.CenterOfRotationZ = e.NewValue / 100;

            }
        }
```
8.23.三维平移
设置UI元素平移的距离的属性分为两种类型,一种是以屏幕为参考对象来定义坐标轴,另外一种类型是以UI对象本身作为参考对象来定义坐标轴,如果这个UI对象本身并没有做旋转相关的特效,这两种类型的属性所显示的效果是一样的,当UI对象应用了一些旋转特效,这两种类型还是有较大的区别的.
GlobalOffsetX/GlobalOffsetY/GlobalOffsetZ表示相对于屏幕沿X轴/Y轴/Z轴平移对象,LocalOffsetX/LocalOffsetY/LocalOffsetZ表示沿对象旋转后对象平面的X/Y/Z轴平移对象.
下面的示例把TextBlock控件沿着X轴旋转45°,然后通过Slider控件来控制修改TextBlock控件相对于屏幕沿X/Y/Z平移的特效效果。
``` bash
            <PivotItem Header="SanweiPingyi">
                <Grid x:Name="SanweiContentPanel" Margin="12,0,12,0">
                    <Grid.RowDefinitions>
                        <RowDefinition Height="*"/>
                        <RowDefinition Height="Auto"/>
                    </Grid.RowDefinitions>
                    <!--平移的对象-->
                    <TextBlock Grid.Row="0" Text="3D" FontSize="120" Foreground="red"
                               HorizontalAlignment="Center" VerticalAlignment="Center">
                        <TextBlock.Projection>
                            <PlaneProjection x:Name="pingyiProjection" RotationX="45"/>
                        </TextBlock.Projection>
                    </TextBlock>
                    <StackPanel Grid.Row="1">
                        <StackPanel Orientation="Horizontal">
                            <RadioButton x:Name="globalRadioButton" Content="GlobalOffset" Checked="rotationRadioButton_Checked"/>
                            <RadioButton x:Name="localRadioButton"
                                         Content="LocalOffset" Checked="centerOfRotationRadioButton_Checked"/>

                        </StackPanel>
                        <TextBlock x:Name="infoTextBlock1" TextWrapping="Wrap"/>
                        <TextBlock x:Name="xTextBlock1" Text="沿着X轴旋转"/>
                        <Slider x:Name="xSlider1" Minimum="0" Maximum="100" ValueChanged="xSlider_ValueChanged1"/>
                        <TextBlock x:Name="yTextBlock1" Text="沿着Y轴旋转"/>
                        <Slider x:Name="ySlider1" Minimum="0" Maximum="100" ValueChanged="ySlider_ValueChanged1"/>
                        <TextBlock x:Name="zTextBlock1" Text="沿着Z轴旋转"/>
                        <Slider x:Name="zSlider1" Minimum="0" Maximum="100" ValueChanged="zSlider_ValueChanged1"/>
                 
                    </StackPanel>
                </Grid>
                
            </PivotItem>
 /// <summary>
        /// 三维平移 z轴移动的距离
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void zSlider_ValueChanged1(object sender, RangeBaseValueChangedEventArgs e)
        {
            if (globalRadioButton.IsChecked == true) {
                pingyiProjection.GlobalOffsetZ = e.NewValue;
            }
            else
            {
                pingyiProjection.LocalOffsetZ= e.NewValue;
            }
            ShowCenterOfRotationValue();
        }
/// <summary>
/// 展示Textblock控件当前各个位置平移的值
/// </summary>
        private void ShowCenterOfRotationValue()
        {
            infoTextBlock1.Text = $"GlobaloffsetX:{pingyiProjection.GlobalOffsetX}" +
                $"Y:{pingyiProjection.GlobalOffsetY} Z:{pingyiProjection.GlobalOffsetZ} LocalOffsetX:{pingyiProjection.LocalOffsetX} Y:{pingyiProjection.LocalOffsetY} Z:{pingyiProjection.LocalOffsetZ}";
        }

        private void ySlider_ValueChanged1(object sender, RangeBaseValueChangedEventArgs e)
        {
            if (globalRadioButton.IsChecked == true) { 
                pingyiProjection.GlobalOffsetY= e.NewValue;
            }
            else
            {
                pingyiProjection.LocalOffsetY= e.NewValue;
            }
            ShowCenterOfRotationValue();
        }

        private void xSlider_ValueChanged1(object sender, RangeBaseValueChangedEventArgs e)
        {
            if (globalRadioButton.IsChecked == true)
            {
                pingyiProjection.GlobalOffsetX = e.NewValue;
            }
            else
            {
                pingyiProjection.LocalOffsetX = e.NewValue;
            }
            ShowCenterOfRotationValue();

        }
```
8.24.用矩阵实现三维特效
用矩阵实现三维特效主要是依赖Matrix3DProjection和Matrix3D类型来实现,Matrix3DProjection是Matrix3D周围的包装类.
Matrix3D类表示一个转换矩阵,该矩阵确定三维(3D)显示对象的位置和方向,该矩阵可以执行转换功能,包括平移(沿X,Y和Z轴重新定位),旋转和缩放(调整大小).Matrix3D类还可以执行透视投影,这会将3D坐标空间中的点映射到二维(2D)视图.
Matrix3D类使用1个4x4正方形矩阵,即一个由四行和四列数字构成的表,其中容纳了用于转换的数据.矩阵的前三行容纳每个3D轴(X,Y,Z)的数据.平移信息位于最后一列中,方向和缩放数据位于前三个列中,缩放因子是位于前三个列中的对角数字,单一矩阵可以将多个转换组合在一起,并一次性对3D显示对象应用这些转换.例如,可以将一个矩阵应用于3D坐标,以便依次执行旋转和平移.如果三维特效之前的点坐标为(X,Y,Z),实现矩阵三维特效后的点坐标(X`,Y`,Z`)的计算公式如下:
X`=M11*X+M21*Y+M31*Z+OffsetX.
Y`=M12*X+M22*Y+M32*Z+OffsetY.
Z`=M13*X+M23*Y+M33*Z+OffsetZ.
Matrix3D的行向量语法
M11	M12	M13	M14
M21	M22	M23	M24
M31	M32	M33	M34
OffsetX	OffsetY	OffsetZ	M44
为了更好理解矩阵,总共有五种不同类型的矩阵,4X4矩阵结构,单位矩阵，平移矩阵，缩放矩阵和旋转矩阵.
单位矩阵表示三维物体在世界空间内的初始位置.如果将一个矩阵乘以单位矩阵还会得到原来的矩阵,没有变换.
单位矩阵
1	0	0	0
0	1	0	0
0	0	1	0
0	0	0	1
缩放矩阵
表示用来对物体进行缩放变换,只需将三维物体乘以缩放矩阵就可以实现缩放的效果.在表中Sx,Sy,Sz分别表示沿着不同的方向进行缩放的比例.
缩放矩阵
Sx	0	0	0
0	Sy	0	0
0	0	Sz	0
0	0	0	1
//向X,Y,Z轴缩放
``` bash
private Matrix3D CreateScaleTransform(double sx,double sy,double sz){
	Matrix3D m=new Matrix3D();
	m.M11=sx;m.M12=0.0;m.M13=0.0;m.M14=0.0;
	m.M21=0.0;m.M22=sy;m.M23=0.0;m.M24=0.0;
	m.M31=0.0;m.M32=0.0;m.M33=sz;m.M34=0.0;
	m.OffsetX=0.0;m.OffsetY=0.0;m.OffsetZ=0.0;m.M44=1.0;
	return m;
}
```
平移矩阵表示用来对物体进行平移变换,只需将三维物体乘以平移矩阵就可以实现平移效果,在表中Tx,Ty和Tz分别表示沿着不同方向进行缩放的比例.
平移矩阵
1	0	0	0
0	1	0	0
0	0	1	0
Tx	Ty	Tz	1
//向x,y,z轴移动
``` bash
        /// <summary>
        /// 平移矩阵 向x,y,z轴移动
        /// </summary>
        /// <param name="tx"></param>
        /// <param name="ty"></param>
        /// <param name="tz"></param>
        /// <returns></returns>
        private Matrix3D TranslationTransform(double tx,double ty,double tz) {
            Matrix3D m = new Matrix3D();
            m.M11 = 1.0;m.M12 = 0.0;m.M13 = 0.0;m.M14 = 0.0;
            m.M21 = 0.0;m.M22 = 1.0;m.M23 = 0.0;m.M24 = 0.0;
            m.M31 = 0.0;m.M32 = 0.0;m.M33 = 1.0;m.M34 = 0.0;
            m.OffsetX = tx;m.OffsetY = ty;m.OffsetZ = tz;m.M44 = 1.0;
            return m;
        }
```
沿着X,Y,Z轴旋转的矩阵分别如下所示 0表示旋转的角度
X轴旋转矩阵
1	0	0	0
0	cos0	sin0	0
0	-sin0	cos0	0
0	0	0	1
RotationX
Y轴旋转矩阵
cos0	0	sin0	0
0	1	0	0
-sin0	0	cos0	0
0	0	0	1
RotationY
Z轴旋转矩阵
cos0	sin0	0	0
-sin0	cos0	0	0
0	0	1	0
0	0	0	1
RotationZ
``` bash
  ///旋转矩阵
        /// <summary>
        /// 沿X轴移动
        /// </summary>
        /// <param name="theta"></param>
        /// <returns></returns>
        private Matrix3D RotateXTransform(double theta) { 
            double sin=Math.Sin(theta);
            double cos=Math.Cos(theta);
            Matrix3D  m=new Matrix3D();
            m.M11 = 1.0;m.M12 = 0.0;m.M13 = 0.0;m.M14 = 0.0;
            m.M21 = 0.0;m.M22 = cos; m.M23 = sin;m.M24 = 0.0;
            m.M31= 0.0; m.M32 = -sin;m.M24 = cos;m.M34 = 0.0;
            m.OffsetX=0.0; m.OffsetY= 0.0;m.OffsetZ = 0.0;m.M44 = 1.0;
            return m;
        }
        /// <summary>
        /// 沿Y轴移动
        /// </summary>
        /// <param name="theta"></param>
        /// <returns></returns>
        private Matrix3D RotateYTransform(double theta) {
            double sin=Math.Sin(theta);
            double cos=Math.Cos(theta);
            Matrix3D m=new Matrix3D();
            m.M11 = cos;m.M12 = 0.0;m.M13 = -sin;m.M14 = 0.0;
            m.M21 = 0.0;m.M22 = 1.0;m.M23 = 0.0;m.M24=0.0;
            m.M31 = sin;m.M32 = 1.0;m.M33 = cos;m.M34 = 0.0;
            m.OffsetX = 0.0;m.OffsetY = 0.0;m.OffsetY = 0.0;m.M44 = 1.0;
            return m;
        }
        /// <summary>
        /// 沿Z轴移动
        /// </summary>
        /// <param name="theta"></param>
        /// <returns></returns>
        private Matrix3D RotateZTransform(double theta) { 
            double cos=Math.Cos(theta);
            double sin=Math.Sin(theta);
            Matrix3D m=new Matrix3D();
            m.M11 = cos;m.M12 = sin;m.M13= 0.0;m.M14 = 0.0;
            m.M21 = -sin;m.M22 = cos;m.M23 = 0.0;m.M24 = 0.0;
            m.M31 = 0.0;m.M32 = 0.0;m.M33 = 1.0;m.M34= 0.0;
            m.OffsetX = 0;m.OffsetY = 0.0;m.OffsetZ = 0.0;m.M44= 1.0;
            return m;
        }
```
#### 动画编程
实现动画的原始办法是配置一个定时器,然后根据定时器的频率循环的回调.在回调方法中,可以手动更新目标属性,根据时间的变化用数学计算来决定当前值,直到它达到最终值,这时就可以停止定时器,并移除时间处理程序.
9.12.动画的目标属性
普通的win10动画一定需要一个动画的目标属性,通过改变这个属性值从而实现动画的效果.可以把目标属性分为以下3类:
1.普通的UI控件属性,如宽度,高度等.这些属性和布局系统相关,当这些属性改变时会重新触发布局系统的工作.
2.变换特效属性
变换特效的属性可以作为动画的目标属性,实现从一个变换状态转换到另外一个变换状态.变换特效是不会重新触发UI布局系统的.要实现把矩形的宽度慢慢方法到两倍的动画,可以对Width属性进行动画处理,也可以对ScaleTransform对象的属性进行处理,这时候就应该选择用变换特效属性来实现,除非变换特效属性无法满足动画的实现效果,才去选择对普通的UI控件属性进行动画处理.
3.三维特效属性.
9.13.动画的类型
Win10主要提供两类动画--线性插值动画和关键帧动画,线性插值动画也称为From/To/By动画,用来反映某个对象在指定时间范围内持续渐变的过程.关键帧动画可以指定任意数量的目标值,并可以控制它们的内插方法.还有一种比较少用的动画——基于帧动画.通常一些复杂的动画才会使用基于帧动画.
基于帧动画 需要做的全部工作是响应静态的CompositionTarget.Rendering事件,触发该事件可以为每帧获取内容.线性插值动画和关键帧动画是要对UI元素的某个属性或某种变换变换进行动画清理的,也就是动态的在时间轴上改变UI元素的某个属性或者某种变换,所以也可以根据动画所要改变的对象来分为针对属性的动画和针对变换的动画,Storyboard类提供TargetName和TargetProperty附加属性,通过在动画上设置这些属性,将告诉动画对哪些内容进行处理.不过在动画以对象作为处理目标之前,必须使用x:Name属性为该对象提供一个名称,否则必须间接地以属性作为目标.针对属性地动画需要将TargetName和TargetProperty附加属性赋值为元素地名称和元素地属性名称,基于帧动画比较特殊,这是一种低级地动画处理方式.相当于每一帧地动画都需要通过事件来重绘界面.
9.2.线性插值动画
线性插值实际上就是通过给定两个关键帧图形线性地求出两帧之间地中间帧图形,这里的线性插值动画是把两个对应的开始值和结束值之间间隔划分,然后线性的实现等量递增或递减的效果.在Windows10中,线性插值动画表现为:界面上某个元素的某个属性在开始值和结束值之间递增或递减,比如淡入淡出效果,时钟转动.
9.21.动画的基本语法.
Win10动画类位于System.Windows.Media.Animation命令空间下,Win10的线性插值动画和关键帧动画都是基于Timeline(时间线)的动画,所有的动画都是继承于Timeline类,Timeline用来表示动画的某一时刻或某段时间的范围.用来记录动画的状态,行为,顺序,起始位置和结束位置及动画持续时间.
Storyboard是Win10动画的基本单元,派生于Timeline类,用来分配动画时间,可以使用同一个故事板对象产生一种或多种动画效果,并且允许控制动画的播放,暂停,停止及在何时何地播放,使用故事板时,必须指定TargetProperty(目标属性)和TargetName(目标名称)属性,这两个属性把故事板和所有产生的动画效果衔接起来,起到桥梁的作用.
``` bash
  <Storyboard x:Name="storyboard1">
                            <DoubleAnimation EnableDependentAnimation="True"
                                             Storyboard.TargetName="ellipse1"
                                             Storyboard.TargetProperty="Width"
                                             From="150" To="300" Duration="0:0:3"/>
                        </Storyboard>
```
9.22.线性动画的基本语法
System.Windows.Media.Animation命名空间,并且都以Animation结尾,这些类主要有DoubleAnimation类,ColorAnimation类和PointAnimation类.这三个类分别对Double,Color和Point属性进行动画处理.
``` bash
 <StackPanel Margin="12,0,12,0">
                    <StackPanel.Resources>
                        <Storyboard x:Name="myStoryboard">
                            <DoubleAnimation From="0" To="300" AutoReverse="True"
                                             RepeatBehavior="Forever" Duration="0:0:3"
                                             Storyboard.TargetName="rect" Storyboard.TargetProperty="Width" EnableDependentAnimation="True"/>
                            
                        </Storyboard>
                    </StackPanel.Resources>
                    <Rectangle x:Name="rect" Width="0" Fill="Red" Height="100"/>
                    <Button Content="启动动画" Click="Button_Click_3"/>
                </StackPanel>
         /// <summary>
        /// 播放动画
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void Button_Click_3(object sender, RoutedEventArgs e)
        {
            myStoryboard.Begin();
        }

```
1.From属性
From值是Width属性的开始数值,如果多次单击,每次都会将Width重新设置为0,并重新开始动画.
每个依赖属性每次只能响应一个动画,如果开始第二个动画,第一个动画就会自动放弃,在许多情况下,可能不希望动画从最初的From值开始:
	1.动画多次启动需要在上次基础上延续下去,需要创建一个能够被多次触发,并且逐次累加效果的动画,例如创建一个每次单击时都大一点的按钮.
	2.创建相互重叠的动画,可以使用PointerEnter事件触发一个扩展按钮的动画,并使用PointerLeave事件触发一个将按钮缩小为原始尺寸的互补动画,如果连续快速的将鼠标多次移动到这种按钮上并移开,每个新动画就会打断上一个动画,导致按钮跳回到From属性设置的值.
如果当矩形正在增大时单击按钮,按钮宽度就会被重新设置为0个像素.
``` bash
<Storyboard x:Name="myStoryboard">
	<DoubleAnimation To="300" EnableDependentAnimation="True" AutoReserve="True" RepeatBehavior="Forever" Duration="0:0:3" Storyboard.TargetName="rect" Storyboard.TargetProperty="Width"/>
</Storyboard>
```
2.To属性
3.By属性
不使用To属性,也可以使用By属性,By属性用于创建通过设置变化的数量改变数值的动画,而不是通过设置达到的目标改变数值.
From 0By100
4.Duration属性
它是在动画开始和结束之间的时间间隔，没有指定,默认1秒
Duration类型还提供了两个特殊的不能通过Timespan对象表示的数值,Duration.Automatic和Duration.Forever.
5.AutoReverse属性
指定时间线在到达其Duration的终点后是否倒退.如果将此动画属性设置为true,则动画在到达其Duration终点后将倒退,即从其终止值向其起始值反向播放,默认情况下,该属性为false.
6.RepeatBehavior属性
指定时间线播放次数,默认情况下,时间线重复次数为1.0,即播放一次时间线,根本不进行重复,RepeatBehavior属性的设置有三种语法:
Forever:一直重复运行下去.
二是设置重复运行的次数,叫做迭代形式,迭代形式占位符是一个整数,用于指定动画应重复的次数.迭代次数后面总是跟一个小写的原义字符x.可以将它想象为一个乘法字符,3x表示3倍.
三是设置动画运行的时间跨度,这个时间跨度和Duration属性有很大区别,这个时间表示的是动画从运行到停止的时间,Duration属性的时间表示动画重复一次的时间.时间跨度的语法格式是"[天.]小时:分钟:秒[.秒的小数部分]",方括号[]表示可选值,重复15秒可以设置RepeatBehavior="0:0:15".小时,分钟和数值可以是0-59中的任意整数,天的值可以很大,但其具有未指定的上限.秒的小数部分(包含小数点)的小数值必须介于0-1之间.
9.23.DoubleAnimation实现变换动画.
下面示例将Storyboard和DoubleAnimation与ScaleTransform一起使用,以便在动画运行时,Rectangle的高度慢慢向下伸长至原来的两倍.
``` bash
 <Canvas>
                    <Canvas.Resources>
                        <Storyboard x:Name="storyBoard">
                            <!--对ScaleTransform对象的ScaleY属性应用动画,表示沿着Y轴缩放倍数变化的动画-->
                            <DoubleAnimation Storyboard.TargetName="scacleTransform"
                                             Storyboard.TargetProperty="ScaleY"
                                             From="1" To="2" Duration="0:0:3"
                                             RepeatBehavior="Forever" AutoReverse="True">
                            </DoubleAnimation>
                            
                        </Storyboard>
                        
                    </Canvas.Resources>
                    <Rectangle x:Name="rectangle" Height="50" Width="50" Canvas.Left="75" Canvas.Top="75" Fill="Blue">
                        <Rectangle.RenderTransform>
                            <!--<注意需要对ScaleTransform命名,否则无法定义动画的目标对象>-->
                            <ScaleTransform x:Name="scaleTransform">
                            </ScaleTransform>
                        </Rectangle.RenderTransform>
                    </Rectangle>
                </Canvas>
  private void Page_Loaded(object sender, RoutedEventArgs e)
        {
            storyBoard.Begin();
        }
```
9.24.ColorAnimation实现颜色渐变动画
ColorAnimation类是用于属性为Color类型的UI元素的线性插值动画的类型,与色调相关的渐变动画可以使用DoubleAnimation类来实现,通常使用UI元素的Fill属性,Background属性值等来实现对象的填充色调的变化效果.
``` bash
 <StackPanel>
                    <StackPanel.Resources>
                        <Storyboard x:Name="storybord1">
                            <ColorAnimation From="Red" To="Yellow"
                                            Storyboard.TargetName="button"
                                            Storyboard.TargetProperty="(Button.Background).(SolidColorBrush.Color)"
                                            Duration="0:0:5">
                            </ColorAnimation>
                        </Storyboard>
                    </StackPanel.Resources>
                    <Button Content="开始动画" Margin="12" Click="Button_Click_4" HorizontalAlignment="Stretch"/>
                    <Button Content="Test" x:Name="button" Margin="12" Click="button_Click_5" HorizontalAlignment="Stretch"/>
                    
                </StackPanel>
            storybord1.Begin();
```
9.25.PointAnimation实现Path图形动画
PointAnimation类是用于属性为Point类型的UI元素的线性插值动画的类型,在两个Point值之间做线性内插动画处理,用于改变某些UI元素对象的X,Y值,如元素的Center属性.如果要对Path图形应用动画,动态地改变Path图形上的点,需要用PathGeometry的方式来创建Path图形.
我们用一个例子来演示如何实现Path图形的动画,这个动画的运行效果是一个四分之三的圆通过线性动画慢慢变成一个完整的圆,不断地重复这样的动画效果,这个Path圆形的构造原理是从圆最右边的点出发,使用BezierSegment画4条曲线和一条直线连接到中心点形成一个闭合的图形.对第4条曲线BezierSegment的终点Point3进行动画处理,让其从圆的最上边的点坐标运动到最右边的点坐标.
``` bash
                <StackPanel>
                    <StackPanel.Resources>
                        <Storyboard x:Name="storyboard1">
                            <PointAnimation From="50,0" To="100,50" Duration="0:0:3" Storyboard.TargetName="bezierSegment"
                                            Storyboard.TargetProperty="Point3" RepeatBehavior="5" EnableDependentAnimation="True"/>
                        </Storyboard>
                    </StackPanel.Resources>
                    <Button Content="运行动画" Click="Button_Click_6" Margin="12" HorizontalAlignment="Stretch"/>
                    <!--<Data属性由4个BezierSegment曲线和1条直线LineSegment组成>-->
                    <Path Fill="#FF4080FF" HorizontalAlignment="Left" Height="100" Margin="162,164,0,0" Stretch="Fill" VerticalAlignment="Top" Width="100">
                        <Path.Data>
                            <PathGeometry>
                                <PathFigure StartPoint="100,50">
                                    <BezierSegment Point1="100,77.6142" Point2="77.6142,100" Point3="50,100"/>
                                    <BezierSegment Point1="22.3858,100" Point2="0,77.6142" Point3="0,50"/>
                                    <BezierSegment Point1="0,22.3858" Point2="22.3858,0" Point3="50,0"/>
                                    <!--BezierSegment表示是圆右上的弧线,对其终点Point3进行动画处理-->
                                    <BezierSegment x:Name="bezierSegment" Point1="77.6142,0" Point2="100,22.3858" Point3="100,50"/>
                                    <LineSegment Point="50,50"/>
                                </PathFigure>
                            </PathGeometry>
                        </Path.Data>
                    </Path>
                </StackPanel>

```
9.3.关键帧动画
1.关键帧动画的概念
一个动画轨迹里有多个关键帧,每个关键帧具有自己的相关信息,每个关键帧还同时保存自己在整个动画轨迹里所处的时间点.在实际运行时,根据当前时间,通过对两个关键帧的插值可以得到当前帧.动画运行时,随着时间的变化,插值得到的当前帧也是变化的,从而产生了动画的效果,由于关键帧包括长度,颜色,位置等信息,所以可以实现运动动画,缩放动画,渐变动画,旋转动画以及混合动画.
2.关键帧动画与线性插值动画的区别
关键帧动画的目标值是使用关键帧对象进行描述的,因此称作"关键帧动画",通过指定多个值来创建关键帧动画,关键帧还会启用不同的插入逻辑,每个插入逻辑根据动画类型作为不同的"KeyFrame"子类实现,确切的说,每个关键帧动画类型具有KeyFrame类的Discrete,Linear,Spline和Easing变体,用于指定其关键帧,例如,若要指定以Double为目标并使用关键帧的动画,则可声明具有DiscreteDoubleKeyFrame,LinearDoubleKeyFrame,SplineDoubleKeyFrame和EasingDoubleKeyFrame的关键帧.可以在一个KeyFrame集合中使用所有这些类型,用以更改每次新关键帧到达的插入.
3.关键帧动画需要注意的属性
对于插入行为,每个关键帧控制该插入,直至到达其KeyTime时间.其Value也会在该时间到达,如果有更多关键帧超出范围,则该值将称为序列中下一个关键帧的起始值.
关键帧动画的持续时间为隐式持续时间,它等于其任一关键帧中设置的最高KeyTime值.如果需要,可以设置一个显式Duration,但应注意该值不应小于关键帧中的KeyTime,否则将会截断部分动画,除了Duraiton,还可以在关键帧动画上设置基于Timeline的属性,因为关键帧动画类也派生自Timeline.这些属性主要有:
	1.AutoReverse:在到达最后一个关键帧后,从结束位置开始反向重复帧.这使得动画的显示持续时间加倍.
	2.BeginTime:延迟动画的起始部分.帧内KeyTime值的时间线在BeginTime到达前不开始计数,
因此不存在截断帧的风险.
	3.FillBehavior:控制到达最后一帧发生的操作.FillBehavior不会对任何中间关键帧产生任何影响.
	4.RepeatBehavior:如果该数不是时间线的隐式持续时间的整数倍数,则可能会截断关键帧序列中的部分动画.
4.关键帧动画的类别
关键帧动画分为线性关键帧,样条关键帧和离散关键帧三种类型.关键帧动画类属于System.Windows.Media.Animation命名空间.并遵守下列命名规定:<类型>AnimationUsingKeyFrame.
其中<类型>是该类进行动画处理的值的类型.
关键帧的分类
所属类型	对应的关键帧动画类	支持的动画过渡方法
Color	ColorAnimationUsingKeyFrame	离散，线性，样条
Double	DoubleAmimationUsingKeyFrame	离散,线性,样条
Point
Object					离散
9.32.线性关键帧
线性关键帧动画是由许多比较短的段构成的动画.每段表示动画中的初始值,最终值成中间值,当运行动画时,它从一个值光滑的移动到另外一个值,使用线性过度,指定时间段内,动画的播放速度是固定的,比如,如果关键帧段在5秒内,从0过度到10.
``` bash
<PointAnimation Storyboard.TargetName="myradialgradientbrush"
	Storyboard.TargetProperty="GradientOrigin" From="0.1,0.7" To="0.3,0.7"
Duration="0:0:10" AutoReverse="True" RepeatBehavior="Forever">
</PointAnimation>
```
可以使用一个效果相同的PointAnimationUsingKeyFrames对象代替上面的PointAnimation对象.
``` bash
<PointAnimationUsingKeyFrames Storyboard.TargetName="myradialgradientbrush" 
Storyboard.TargetProperty="GradientOrigin" AutoReverse="True" RepeatBehavior="Forever">
	<LinearPointKeyFrame Value="0.1,0.7" KeyTime="0:0:0"/>
	<LinearPointKeyFrame Value="0.3,0.7" KeyTime="0:0:10"/>
</PointAnimationUsingKeyFrames>
```
这个动画包含两个关键帧,当动画开始时第一个关键帧设置Point值(如果希望在RadialGradientBrush画刷中设置的当前值,可以省略这个关键帧).第二个关键帧定义结束值,这是10秒之后到达的数值,PointAnimationUsingKeyFrames对象执行线性插值,这样第一个关键帧平滑移动到第二个关键帧,就像PointAnimation对象使用From值和To值一样.
每个关键帧动画都使用自己的关键帧对象(LinearPointKeyFrame),对于大部分内容,这些类时相同的,它们包含一个保存目标值的Value属性和一个指示帧何时到达目标值的KeyTime属性,唯一的区别是Value属性的数据类型,在LinearPointKeyFrame类中是Point类型,在DoubleKeyFrame类中是double类型.
除了指定动画的Duration外,还需要指定向每个关键帧分配持续时间内的多长一段时间.你可以为动画的每个关键帧描述其KeyTime来实现此目的.每个关键帧的KeyTime都指定了该关键帧的结束时间.KeyTime属性并不能指定关键时间播放的长度,关键帧播放时间长度由关键帧的结束时间,前一个关键帧的结束时间以及动画的持续时间来确定.可以以时间值或百分比形式来指定关键时间,也可以将其指定为特殊值Uniform或Paced.
9.33.样条关键帧
为了使若干个关键帧间的动画连续流畅,经常采用样条关键帧插值法.具有二阶连续性,即C2连续性,在win10中,每个支持线性关键帧的类也支持样条关键帧,并且它们使用"Spline+数据类型+KeyFrame"形式进行命名.
和线性关键帧一样,样条关键帧使用插值从一个值平滑的移动到另外一个值,区别是每个样条关键帧都有一个KeySpline属性.可以使用该属性定义一个影响插值方式的三次贝塞尔样条.
样条关键帧使用的是三次方贝塞尔曲线来计算动画运动的轨迹.贝塞尔曲线的每一个顶点都有两个控制点,用于控制在该顶点两侧的曲线的弧度,它是应用于二维图形应用程序的数学曲线.曲线的定义有四个点,起始点,终止点，以及两个相互分离的中间点,滑动两个中间点,贝塞尔曲线的形状会发生变化,三次贝塞尔曲线,则需要一个起点，一个终点，两个控制点来控制曲线的形状.
P0,P1,P2,P3四个点在平面或三维空间中定义了三次方贝塞尔曲线,曲线起始于P0,走向P1,并从P2的方向来到P3.曲线一般不会经过P1或P2,这两个点只是提供方向信息.P0和P1之间的距离,决定了曲线在趋进P3之前,走向P2方向的长度有多长,曲线的参数形式为
	B(t)=P0(1-t)3+3P1t(1-t)2+3P2t2(1-t)+P3t3,t属于[0,1]
样条关键帧可用于达到更真实的记时效果,使用其他关键帧,可以指定一个Value和KeyTime，使用样条关键帧,还需要指定一个KeySpline.
``` bash
<SplineDoubleKeyFrame Value="500" KeyTime="0:0:7" KeySpline="0.0,1.0 1.0,0.0"/>
```
样条关键帧根据KeySpline属性的值在值之间创建可变的过度,KeySpline属性是从(0,0)延伸到(1,1)的贝塞尔曲线的两个控制点，用于控制在该顶点两侧的曲线的弧度,描述了动画的加速.第一个控制点控制贝塞尔曲线前半部分的曲线因子,第二个控制点控制贝塞尔线段后半部分的曲线因子.此属性基本上定义了一个时间关系间的函数,其中函数-时间图形采用贝塞尔曲线的形状,所得到的曲线是对该样条关键帧的更改速率所进行的描述.曲线陡度越大,关键帧更改其值的速度越快,曲线趋于平缓,关键帧更改其值的速度也趋于缓慢.
在XAML属性字符串中指定一个KeySpline值,该字符串具有四个以空格或逗号分隔的Double值,如KeySpline="0.0,1.0 1.0,0.0".这些值用作贝塞尔曲线的两个控制点X,Y对.X是时间,Y是对值的函数修饰符.
每个值应该始终介于0-1之间.控制点更改该曲线的形状,并因此会更改样条动画的函数随时间变化的行为.
每个控制点会影响控制样式动画速率的概念曲线的形状,同时更改(0,0)和(1,1)之间的线性进度,keySplines的语法必须指定且仅指定两个控制点,如果曲线只需要一个控制点,可以重复同一个控制点,如果不将控制点修改为KeySpline,则从(0,0)到(1,1)的直线是线性插入的时间函数的表示形式.
下面的示例,通过对比在Canvas控件上移动的两个矩形,演示了一个样条关键帧动画的运动轨迹和一个线性关键帧动画运行轨迹的对比.第一个矩形使用一个具有SplineDoubleKeyFrame对象的DoubleAnimationUsingKeyFrames动画来控制Canvas.Top属性使它从400到0按照样条关键帧的轨迹变化和使用一个具有LinearDoubleKeyFrame对象的DoubleAnimationUsingKeyFrames动画来控制Canvas.Left属性使它从0到400按照线性关键帧的轨迹变化,第二个矩形使用了两个具有DoubleAnimationUsingKeyFrames动画来控制Canvas.Top和Canvas.Left属性,使其匀速的从左下角向右上角运动,两个矩形同时到达目标位置(10秒之后),但是第一个矩形在其运动过程中会有明显的加速和减速,加速时会超过第二个矩形,而减速又会落后于第二个矩形.
``` bash
                <Grid x:Name="LayoutRoot" Background="Transparent">
                    <Grid.Resources>
                        <Storyboard x:Name="SplineKeyStoryBoard">
                            <!--对第一个矩形的Canvas.Top属性使用样条关键帧动画-->
                            <DoubleAnimationUsingKeyFrames 
                                Storyboard.TargetName="srect"
                                Storyboard.TargetProperty="(Canvas.Top)"
                                Duration="0:0:10"
                                RepeatBehavior="Forever">
                                <SplineDoubleKeyFrame Value="0" KeyTime="0:0:10"
                                                      KeySpline="0.0,1.0 1.0,0.0"/>
                            </DoubleAnimationUsingKeyFrames>
                            <!--对第一个矩形的Canvas.Left属性使用线性关键帧动画-->
                            <DoubleAnimationUsingKeyFrames
                                Storyboard.TargetName="srect"
                                Storyboard.TargetProperty="(Canvas.Left)"
                                Duration="0:0:10"
                                RepeatBehavior="Forever">
                                <LinearDoubleKeyFrame Value="400" KeyTime="0:0:10"/>
                            </DoubleAnimationUsingKeyFrames>
                            <!--对第二个矩形的Canvas.Top属性使用线性关键帧动画-->
                            <DoubleAnimationUsingKeyFrames 
                                Storyboard.TargetName="srect2"
                                Storyboard.TargetProperty="(Canvas.Top)"
                                Duration="0:0:10"
                                RepeatBehavior="Forever">
                                <LinearDoubleKeyFrame Value="0" KeyTime="0:0:10"/>
                            </DoubleAnimationUsingKeyFrames>
                            <!--对第二个矩形的Canvas.Left属性使用线性关键帧动画-->
                            <DoubleAnimationUsingKeyFrames
                                Storyboard.TargetName="srect2"
                                Storyboard.TargetProperty="(Canvas.Left)"
                                Duration="0:0:10"
                                RepeatBehavior="Forever">
                                <LinearDoubleKeyFrame Value="400" KeyTime="0:0:10"/>
                            </DoubleAnimationUsingKeyFrames>
                        </Storyboard>
                    </Grid.Resources>
                    <Canvas Margin="12,0,12,0">
                     

                            <!--第一个矩形的运动轨迹时采用样条关键帧的方式从左下角向右上角用变化的加速度运动-->
                            <Rectangle x:Name="srect" Width="50" Height="50" Fill="Purple" Canvas.Top="400" Canvas.Left="0"/>

                            <!--第二个矩形的运动轨迹是采用线性关键帧的方式从左下角向右上角匀速运动-->
                            <Rectangle x:Name="srect2" Width="50" Height="50" Fill="Red" Canvas.Top="400" Canvas.Left="0"/>
                            <Button Content="运行动画" Canvas.Top="500" Click="Button_Click_7"/>
                   

                    </Canvas>
                </Grid>
```
9.34.离散关键帧
离散关键帧就不会进行平滑的过度,而是当到达关键时间时,属性突然改变到新数值,离散关键帧根本不使用任何插入,使用离散关键帧,动画函数将从一个值跳到下一个没有内插的值.动画在持续时间结束之前不会更改其输出值,直到结束时间点,才会修改.也就是说在KeyTime到达后,只是简单的应用新的Value,离散关键帧可以比线性和样条支持更多类型属性进行动画处理.无法线性变化的属性使用离散关键帧.
线性关键帧类使用Linear+数据类型+KeyFrame的形式进行命名.离散关键帧类使用Discrete+数据类型+KeyFrame形式进行命名.当运行这个动画时,中心点会在合适的时间从一个位置跳到下一个位置,这是不平稳的动画效果.下面来看一个针对Point属性的离散关键帧动画,通过改变椭圆填充LinearGradientBrush画刷的开始点的值从而实现了椭圆的颜色渐变的变化效果.
``` bash
                <Grid>
                    <Grid.Resources>
                        <Storyboard x:Name="Discretestoryboard">
                            <PointAnimationUsingKeyFrames Storyboard.TargetName="myLinearGradientBrush" Storyboard.TargetProperty="StartPoint" EnableDependentAnimation="True" RepeatBehavior="Forever">
                                <DiscretePointKeyFrame Value="0.1,0.3" KeyTime="0:0:0"/>
                                <DiscretePointKeyFrame Value="0.2,0.4" KeyTime="0:0:1"/>
                                <DiscretePointKeyFrame Value="0.3,0.5" KeyTime="0:0:2"/>
                                <DiscretePointKeyFrame Value="0.4,0.6" KeyTime="0:0:3"/>
                                <DiscretePointKeyFrame Value="0.5,0.7" KeyTime="0:0:4"/>
                                <DiscretePointKeyFrame Value="0.6,0.8" KeyTime="0:0:5"/>
                                <DiscretePointKeyFrame Value="0.7,0.9" KeyTime="0:0:6"/>
                            </PointAnimationUsingKeyFrames>
                        </Storyboard>
                    </Grid.Resources>
                    <StackPanel>
                        <Ellipse x:Name="ellipse">
                            <Ellipse.Fill>
                                <LinearGradientBrush x:Name="myLinearGradientBrush"
                                                     StartPoint="0,0" EndPoint="1,0">
                                    <LinearGradientBrush.GradientStops>
                                        <GradientStop Color="White" Offset="0.001"/>
                                        <GradientStop Color="Blue" Offset="1"/>
                                    </LinearGradientBrush.GradientStops>
                                </LinearGradientBrush>
                            </Ellipse.Fill>
                        </Ellipse>
                        <Button Content="启动动画" Height="100" Click="Button_Click_8"/>
                    </StackPanel>

                </Grid>
        private void Button_Click_8(object sender, RoutedEventArgs e)
        {
            Discretestoryboard.Begin();
        }
```
有一种动画类型是可以将动画化的值应用于其类型不是Double,Point,Color的属性的唯一方法,它就是关键帧动画ObjectAnimationUsingKeyFrames.使用Object值的动画非常不同.因为不可能在帧之间插入值.当帧的KeyTime到达时,动画化的值将立即设置为关键帧的Value中指定的值,由于没有任何插入,因此只有一种关键帧用于ObjectAnimationUsingKeyFrame.关键帧集合:DiscreteObjectKeyFrame.DiscreteObjectKeyFrame的Value通常使用属性元素语法设置.因为设置的对象值通常不可表示为字符串以采用属性语法填充Value.如果使用引用,例如StaticResource,则可以使用属性语法.
在使用的Button控件的默认样式里面可以发现,按钮的点击状态和不可用状态都使用了ObjectAnimationUsingKeyFrames的离散关键帧动画来改变按钮的背景画刷颜色.因为系统的背景资源、是SolidColorBrush对象,而不仅仅是Color值,但由于SolidColorBrush不是Double,Point或Color,因此必须使用ObjectAnimationUsingKeyFrames才能使用该资源.
另外,还可使用ObjectAnimationUsingKeyFrames来设置使用枚举值的属性的动画,如果把按钮的Disabled状态改成要隐藏按钮,可以获取Visibility枚举常量的Visibility属性,在这种情况下,可以使用属性语法设置该值,使用枚举值设置属性,例如Collapsed.
``` bash
<VisualState x:Name="Disabled">
<Storyboard>
	<ObjectAnimationUsingKeyFrames Storyboard.TargetName="ContentContainer" Storyboard.TargetProperty="Visibility">
	<DiscreteObjectKeyFrame KeyTime="0" Value="Collapsed"/>
	</ObjectAnimationUsingKeyFrames>
</Storyboard>
</VisualState>
```
9.4.缓动函数动画
Window10内置了11种缓动函数动画:BackEase,BounceEase,CircleEase,CubicEase,ElasticEase,ExponentialEase,PowerEase,QuadraticEase,QuinticEase和SineEase.
	有些缓动函数具有其自己的属性,例如BounceEase具有两个属性(Bounces和Bounciness),用于修改该特定的BounceEase随时间变化的函数行为,其他缓动函数(CubicEase)不具有除所有缓动函数共享的EasingMode属性之外的任何属性,并且始终产生相同的随时间变化的函数行为,根据你在具有多个属性的缓动函数上设置的属性,这些缓动函数中的某些函数会有些重叠,例如,QuadraticEase与其Power等于2的PowerEase完全相同.并且CircleEase基本上就是具有默认值的ExponentialEase.
BackEase缓动函数是唯一的,因为它可以更改正常范围之外的值(在由From/To设置时)或关键帧的值,它通过更改相反方向的值启动动画,按照预期从正常的From/To行为开始,再次返回至From或起始值,然后按正常行为运行动画.
	缓动函数可以以三种方式应用于动画:
	1.通过在关键帧动画中使用缓动关键,使用EasingColorKeyFrame.EasingFunction,EasingDoubleKeyFrame,EasingFunction或EasingPointKeyFrame.EasingFunction.
	2.通过在线性插值动画类型上设置EasingFunction属性,使用ColorAnimation.EasingFunction,DoubleAnimation.EasingFunction或PointAnimation.EasingFunction.
	3.通过将GeneratedEasingFunction设置为VisualTransition的一部分,这种方式专用于定义控件的视觉状态.
9.42.BackEase动画
BackEase:在某一动画开始沿指示的路径进行动画处理前稍稍收回该动画的移动通过指定动画的EasingMode属性值,可以控制动画中Back行为发生的时间.
用于此动画的函数公式如下所示:f(t)=t3-t*a*sin(t*Π) a:amplitude
EasingMode="EaseIn"	EasingMode="EaseOut"	EasingMode="EaseInOut"
因为此动画导致值在前进前收回,所以动画可能会意外插入到负数中,当对不支持负数的属性进行动画处理时,可能会引发错误.如果将此动画应用到对象的Height(例如,对于EaseIn的EasingMode,应为0-200),则该动画将尝试对Height插入负数,从而引发错误.
下面用一个示例来演示BackEase的EaseInOut的动画效果,注意动画快要结束时可以发现第一个椭圆是先变大再变小,第二个椭圆的运动轨迹与上面的图形是相似的.
``` bash
 <Grid Background="Transparent">
                    <Grid.Resources>
                        <Storyboard x:Name="Easestoryboard">
                            <!--第一个椭圆的动画-->
                            <DoubleAnimation From="1" To="2" Duration="0:0:3" Storyboard.TargetName="ellipselScaleTransform"
                                             Storyboard.TargetProperty="ScaleX">
                                <DoubleAnimation.EasingFunction>
                                    <BackEase Amplitude="0.3" EasingMode="EaseInOut"/>
                                </DoubleAnimation.EasingFunction>
                            </DoubleAnimation>
                            <DoubleAnimation From="1" To="2" Duration="0:0:3" Storyboard.TargetName="ellipselScaleTransform"
                                             Storyboard.TargetProperty="ScaleY">
                                <DoubleAnimation.EasingFunction>
                                    <BackEase Amplitude="0.3" EasingMode="EaseInOut"/>
                                </DoubleAnimation.EasingFunction>
                            </DoubleAnimation>
                            <!--第二个椭圆的动画-->
                            <DoubleAnimation From="0" To="400" Duration="0:0:3" Storyboard.TargetName="ellipse4"
                                             Storyboard.TargetProperty="(Canvas.Left)">
                                
                            </DoubleAnimation>
                            <DoubleAnimation From="400" To="0" Duration="0:0:3" Storyboard.TargetName="ellipse4"
                                             Storyboard.TargetProperty="(Canvas.Top)">
                                <DoubleAnimation.EasingFunction>
                                    <BackEase Amplitude="0.3" EasingMode="EaseInOut"/>
                                </DoubleAnimation.EasingFunction>
                            </DoubleAnimation>
                        </Storyboard>
                    </Grid.Resources>
                    <Canvas Margin="12,0,12,0">
                        <!--第一个椭圆展示了BackEase的放大动画效果-->
                        <Ellipse Name="ellipse3" Width="80" Height="80" Fill="Blue">
                            <Ellipse.RenderTransform>
                                <ScaleTransform x:Name="ellipselScaleTransform">
                                    
                                </ScaleTransform>
                            </Ellipse.RenderTransform>
                        </Ellipse>
                        <!--第二个椭圆展示了BackEase的运动轨迹-->
                        <Ellipse x:Name="ellipse4" Fill="Red" Width="80" Height="80" Canvas.Left="0" Canvas.Top="400"/>
                        <Button Margin="0,500,0,0" Content="启动动画" Height="80" Click="Button_Click_10"/>
                    </Canvas>

                </Grid>
 private void Button_Click_10(object sender, RoutedEventArgs e)
        {
            Easestoryboard.Begin();
        }
```
9.43.BounceEase动画
BounceEase:创建弹跳效果.f(t)表示动画进度,t表示时间
EasingMode="EaseIn"	EasingMode="EaseOut" EasingMode="EaseInOut"
可以使用Bounces属性指定弹跳次数并使用Bounciness属性指定弹跳程度(弹性大小).Bounciness属性指定下一个反弹的幅度缩放,例如反弹度值2会使渐入中下一个反弹的幅度翻倍,并且会使渐出中下一个反弹的幅度减半.
下面用一个示例演示BounceEase的EaseOut的动画效果,第一个椭圆会有几次变大变小的效果,
``` bash
 <Grid Background="Transparent">
                    <Grid.Resources>
                        <Storyboard x:Name="Bouncestoryboard">
                            <!--第一个椭圆的动画-->
                            <DoubleAnimation From="0" To="200" Duration="0:0:3" EnableDependentAnimation="True"
                                             Storyboard.TargetName="ellipse5"
                                             Storyboard.TargetProperty="Width">
                                <DoubleAnimation.EasingFunction>
                                    <BounceEase Bounces="2" EasingMode="EaseOut" Bounciness="2"/>
                                </DoubleAnimation.EasingFunction>
                                
                            </DoubleAnimation>
                            <DoubleAnimation From="80" To="200" Duration="0:0:3" EnableDependentAnimation="True"
                                             Storyboard.TargetName="ellipse5"
                                             Storyboard.TargetProperty="Height">
                                <DoubleAnimation.EasingFunction>
                                    <BounceEase Bounces="2" EasingMode="EaseOut" Bounciness="2"/>
                                </DoubleAnimation.EasingFunction>
                   
                            </DoubleAnimation>
                            <!--第二个椭圆的动画-->
                            <DoubleAnimation From="0" To="400" Duration="0:0:3" Storyboard.TargetName="ellipse6"         Storyboard.TargetProperty="(Canvas.Left)">
                            </DoubleAnimation>
                            <DoubleAnimation From="400" To="0" Duration="0:0:3" Storyboard.TargetName="ellipse6"
                                             Storyboard.TargetProperty="(Canvas.Top)">
                                <DoubleAnimation.EasingFunction>
                                    <BounceEase Bounces="2" EasingMode="EaseOut" Bounciness="2"/>
                                </DoubleAnimation.EasingFunction>
                            </DoubleAnimation>
                        </Storyboard>
                    </Grid.Resources>
                    <Canvas Margin="12,0,12,0">
                        <!--第一个椭圆展示了BounceEase的动画效果-->
                        <Ellipse Name="ellipse5" Width="80" Height="80"
                                 Fill="Blue"/>
                        <!--第二个椭圆展示了BounceEase的运动轨迹-->
                        <Ellipse x:Name="ellipse6" Fill="Red" Width="80"
                                 Height="80" Canvas.Left="0" Canvas.Top="400"/>
                        <Button Margin="0,500,0,0" Content="启动动画" Height="80" Click="Button_Click_11"/>
                    </Canvas>
                </Grid>
 private void Button_Click_11(object sender, RoutedEventArgs e)
        {
            Bouncestoryboard.Begin();
        }
```
9.44.CircleEase动画
CircleEase:创建使用循环函数加速和/或减速的动画.通过指定EasingMode,可以控制动画加速与减速.
用于此函数的函数公式如下:
f(t)=1-(1-t2)平方根
t的有效值为-1<=t<=1,大于1的值将被计算为1,而小于-1的值则被计算为-1
这意味着此时间间隔之外的值的动画继续,但缓动函数在进入无效的域时暂停,并在离开无效的域时恢复.
下面用一个示例来演示一下CircleEase的EaseOut的动画效果,第一个椭圆的宽度会先快速的拉长然后再慢慢的拉长,第二个椭圆的运动轨迹与上面EaseOut的动画轨迹图形是相似的,类似沿着一个四分之一圆的轨迹进行运动.
``` bash
<Grid Background="Transparent">
                    <Grid.Resources>
                        <Storyboard x:Name="CircleEasestoryboard">
                            <!--第一个椭圆的动画-->
                            <DoubleAnimation From="80" To="400" Duration="0:0:3" EnableDependentAnimation="True"
                                             Storyboard.TargetName="ellipse7"
                                             Storyboard.TargetProperty="Width">
                                <DoubleAnimation.EasingFunction>
                                    <CircleEase EasingMode="EaseOut"/>
                                </DoubleAnimation.EasingFunction>
                                
                            </DoubleAnimation>
                            <!--第二个椭圆的动画-->
                            <DoubleAnimation From="0" To="400" Duration="0:0:3" Storyboard.TargetName="ellipse8"
                                             Storyboard.TargetProperty="(Canvas.Left)">
                            </DoubleAnimation>
                            <DoubleAnimation From="400" To="0" Duration="0:0:3" Storyboard.TargetName="ellipse8"
                                             Storyboard.TargetProperty="(Canvas.Top)">
                                <DoubleAnimation.EasingFunction>
                                    <CircleEase EasingMode="EaseOut"/>
                                </DoubleAnimation.EasingFunction>
                            </DoubleAnimation>
                        </Storyboard>
                    </Grid.Resources>
                    <Canvas Margin="12,0,12,0">
                        <!--第一个椭圆展示了CircleEase的动画效果-->
                        <Ellipse x:Name="ellipse7" Width="80" Height="80" Fill="Blue"/>
                        <!--第二个椭圆展示了CircleEase的运动轨迹-->
                        <Ellipse x:Name="ellipse8" Fill="Red" Width="80" Height="80" Canvas.Left="0" Canvas.Top="400"/>
                        <Button Margin="0,500,0,0" Content="启动动画" Height="80" Click="Button_Click_12"/>
                    </Canvas>
                </Grid>
 private void Button_Click_12(object sender, RoutedEventArgs e)
        {
            CircleEasestoryboard.Begin();
        }
```
9.45.CubicEase动画
CubicEase:创建使用公式f(t)=t3加速和/或减速的动画,通过指定EasingMode,可以控制动画加速或减速.
下面来演示CircleEase的EaseOut的动画效果,第一个椭圆展示出来的是一种很自然的翻转效果,像是上面被推了一下然后突然翻转过来到前端时间比较慢,后半段时间速度就快速增大,第二个椭圆的运动轨迹与上面EaseInOut动画轨迹是相似的
``` bash
 <Grid Background="Transparent">
                    <Grid.Resources>
                        <Storyboard x:Name="Cubicstoryboard">
                            <!--第一个椭圆的动画-->
                            <DoubleAnimation From="0" To="180" Duration="0:0:3" Storyboard.TargetName="ellipselPlaneProjection"
                                             Storyboard.TargetProperty="RotationX">
                                <DoubleAnimation.EasingFunction>
                                    <CubicEase EasingMode="EaseInOut"/>
                                </DoubleAnimation.EasingFunction>
                            </DoubleAnimation>
                            <!--第二个椭圆的动画-->
                            <DoubleAnimation From="0" To="400" Duration="0:0:3" Storyboard.TargetName="ellipse10"
                                             Storyboard.TargetProperty="(Canvas.Left)">
                            </DoubleAnimation>
                            <DoubleAnimation From="400" To="200" Duration="0:0:3" Storyboard.TargetName="ellipse10"
                                             Storyboard.TargetProperty="(Canvas.Top)">
                                <DoubleAnimation.EasingFunction>
                                    <CubicEase EasingMode="EaseInOut"/>
                                </DoubleAnimation.EasingFunction>
                            </DoubleAnimation>
                        </Storyboard>
                    </Grid.Resources>
                    <Canvas Margin="12,0,12,0">
                        <!--第一个椭圆展示了CubicEase的动画效果-->
                        <Ellipse x:Name="ellipse9" Width="200" Height="200" Fill="Blue">
                            <Ellipse.Projection>
                                <PlaneProjection RotationX="0" x:Name="ellipselPlaneProjection">
                                </PlaneProjection>
                            </Ellipse.Projection>
                        </Ellipse>
                        <!--第二个椭圆展示了CubicEase的运动轨迹-->
                        <Ellipse x:Name="ellipse10" Fill="Red" Width="80"
                                 Height="80" Canvas.Left="0" Canvas.Top="400"/>
                        <Button Margin="0,500,0,0" Content="启动动画" Height="80" Click="Button_Click_13"/>
                    </Canvas>
                </Grid>
  private void Button_Click_13(object sender, RoutedEventArgs e)
        {
            Cubicstoryboard.Begin();
        }
```
9.46.ElasticEase动画
ElasticEase:创建类似于弹簧在停止前来回震荡的动画,通过指定EasingMode属性值,可以控制动画中"弹簧"行为发生的时间.
可以使用Oscillations属性指定动画来回振动的次数,以及使用Springiness属性指定振动弹性的张紧程度,
因为此动画导致值来回振动,所以此动画可能会意外插入到负数中.当对不支持负数的属性进行动画处理时,这可能会引发错误.
下面用一个示例来演示ElasticEase的EaseOut的动画效果,第一个椭圆和线条展示出来的是用有弹性的绳子栓住吊球,然后往下掉落的这种自然效果,线条相当于是有弹性的绳子,而第一个椭圆相当于吊球,第二个椭圆模拟了上面EaseOut的动画轨迹图形
``` bash
 <Grid Background="Transparent">
                    <Grid.Resources>
                        <Storyboard x:Name="Elasticstoryboard">
                            <!--线条的动画-->
                            <DoubleAnimation From="0" To="400" Duration="0:0:3" EnableDependentAnimation="True"
                                             Storyboard.TargetName="line1"
                                             Storyboard.TargetProperty="Y2">
                                <DoubleAnimation.EasingFunction>
                                    <ElasticEase EasingMode="EaseOut" Oscillations="7"/>
                                </DoubleAnimation.EasingFunction>
                            </DoubleAnimation>
                            <!--第一个椭圆的动画-->
                            <DoubleAnimation From="0" To="400" Duration="0:0:3" Storyboard.TargetName="ellipse11"
                                             Storyboard.TargetProperty="(Canvas.Top)">
                                <DoubleAnimation.EasingFunction>
                                    <ElasticEase EasingMode="EaseOut" Oscillations="7"/>
                                </DoubleAnimation.EasingFunction>
                            </DoubleAnimation>
                            <!--第二个椭圆的动画-->
                            <DoubleAnimation From="0" To="400" Duration="0:0:3" Storyboard.TargetName="ellipse12"
                                             Storyboard.TargetProperty="(Canvas.Left)">
                                
                            </DoubleAnimation>
                            <DoubleAnimation From="400" To="200" Duration="0:0:3" Storyboard.TargetName="ellipse12"
                                             Storyboard.TargetProperty="(Canvas.Top)">
                                <DoubleAnimation.EasingFunction>
                                    <ElasticEase EasingMode="EaseOut" Oscillations="7"/>
                                </DoubleAnimation.EasingFunction>
                            </DoubleAnimation>
                        </Storyboard>
                    </Grid.Resources>
                    <Canvas Margin="12,0,12,0">
                        <!--线条展示了ElasticEase的动画效果-->
                        <Line x:Name="line1" X1="50" Y1="0" X2="50" Y2="50"
                              Stroke="Blue" StrokeThickness="10" Fill="Blue"/>
                        <!--第一个椭圆展示了ElasticEase的动画效果-->
                        <Ellipse Name="ellipse11" Fill="Blue" Width="100" Height="100"/>
                        <!--第二个椭圆展示了ElasticEase的运动轨迹-->
                        <Ellipse x:Name="ellipse12" Fill="Red" Width="80"
                                 Height="80" Canvas.Left="0" Canvas.Top="400"/>
                        <Button Margin="0,500,0,0" Content="启动动画" Height="80" Click="Button_Click_14"/>
                    </Canvas>
                </Grid>
 private void Button_Click_14(object sender, RoutedEventArgs e)
        {
            Elasticstoryboard.Begin();  
        }
```
9.47.ExponentialEase动画
ExponentialEase:创建使用指数公式加速和/或减速的动画.通过指定EasingMode,可以控制动画加速与减速
下面来演示ExponentialEase的EaseIn的动画效果,第一个动画开始时非常缓慢,然后突然间加速冲到最底下,
第二个类似EaseIn动画轨迹图形
``` bash
 <Grid Background="Transparent">
                    <Grid.Resources>
                        <Storyboard x:Name="ExponentialStoryboard">
                            <!--第一个椭圆的动画-->
                            <DoubleAnimation From="0" To="400" Duration="0:0:3" Storyboard.TargetName="ellipse13"
                                             Storyboard.TargetProperty="(Canvas.Top)">
                                <DoubleAnimation.EasingFunction>
                                    <ExponentialEase Exponent="10" EasingMode="EaseIn"/>
                                </DoubleAnimation.EasingFunction>
                            </DoubleAnimation>
                            <!--第二个椭圆的动画-->
                            <DoubleAnimation From="0" To="400" Duration="0:0:3" Storyboard.TargetName="ellipse14"
                                             Storyboard.TargetProperty="(Canvas.Left)">
                                
                            </DoubleAnimation>
                            <DoubleAnimation From="400" To="200" Duration="0:0:3" Storyboard.TargetName="ellipse14"
                                             Storyboard.TargetProperty="(Canvas.Top)">
                                <DoubleAnimation.EasingFunction>
                                    <ExponentialEase Exponent="10" EasingMode="EaseIn"/>
                                </DoubleAnimation.EasingFunction>
                            </DoubleAnimation>
                        </Storyboard>
                    </Grid.Resources>
                    <Canvas Margin="12,0,12,0">
                        <!--第一个椭圆展示了ExponentialEase的动画效果-->
                        <Ellipse x:Name="ellipse13" Width="50" Height="50" Fill="Blue"/>
                        <!--第二个椭圆展示了ExponentialEase的运动轨迹-->
                        <Ellipse x:Name="ellipse14" Fill="Red" Width="80"
                                 Height="80" Canvas.Left="0" Canvas.Top="400"/>
                        <Button Margin="0,500,0,0" Content="启动动画" Height="80" Click="Button_Click_15"/>
                    </Canvas>
                </Grid>
 private void Button_Click_15(object sender, RoutedEventArgs e)
        {
            ExponentialStoryboard.Begin();
        }
```
9.48.PowerEase/QuadraticEase/QuarticEase/QuinticEase动画
PowerEase:创建使用公式f(t)=tp次方(其中,p等于Power属性)加速和/减速的动画
公式f(t)=tp次方,因此,PowerEase函数可由QuadraticEase(ft=t2),CubicEase(ft=t3),QuarticEase(ft=t4)和QuinticEase(ft=t5)替代.
如果希望用PowerEase函数来创建与QuadraticEase函数(ft=t2)创建的行为相同的行为,应将Power属性指定为2,QuadraticEase:创建使用ft=t2加速和/或减速的动画.QuarticEase:创建使用公式ft=t4加速和/或减速的动画
下面演示PowerEase的EaseIn的动画效果,通过Slider控件设置PowerEase的Power值,Power值越大第一个椭圆后面掉落的加速度就越大,加速的时间也越短.第二个椭圆的运动轨迹模拟了上面EaseIn的动画轨迹图形.
``` bash
 <Grid Background="Transparent">
                    <Grid.Resources>
                        <Storyboard x:Name="powerStoryboard">
                            <!--第一个椭圆的动画-->
                            <DoubleAnimation From="0" To="400" Duration="0:0:3" Storyboard.TargetName="ellipse15"
                                             Storyboard.TargetProperty="(Canvas.Top)">
                                <DoubleAnimation.EasingFunction>
                                    <PowerEase EasingMode="EaseIn" x:Name="powerEase1"/>
                                    
                                </DoubleAnimation.EasingFunction>
                            </DoubleAnimation>
                            <!--第二个椭圆的动画-->
                            <DoubleAnimation From="0" To="400" Duration="0:0:3" Storyboard.TargetName="ellipse16"
                                             Storyboard.TargetProperty="(Canvas.Left)">
                            </DoubleAnimation>
                            <DoubleAnimation From="400" To="200" Duration="0:0:3" Storyboard.TargetName="ellipse16"
                                             Storyboard.TargetProperty="(Canvas.Top)">
                                <DoubleAnimation.EasingFunction>
                                    <PowerEase EasingMode="EaseIn" x:Name="powerEase2"/>
                                </DoubleAnimation.EasingFunction>
                            </DoubleAnimation>
                        </Storyboard>
                    </Grid.Resources>
                    <Canvas Margin="12,0,12,0">
                        <Ellipse Name="ellipse15" Width="50" Height="50" Fill="Blue"/>
                        <Ellipse x:Name="ellipse16" Fill="Red" Width="80"
                                 Height="80" Canvas.Left="0" Canvas.Top="400"/>
                        <Button Margin="0,500,0,0" Content="启动动画" Height="80" Click="Button_Click_16"/>
                        <Slider x:Name="powerSlider" Margin="200,500,0,0"
                                Width="200" Background="Red" Value="50" Maximum="100" Minimum="0"/>
                    </Canvas>
                </Grid>
 private void Button_Click_16(object sender, RoutedEventArgs e)
        {
            powerEase1.Power = powerSlider.Value;
            powerEase2.Power= powerSlider.Value;
            powerStoryboard.Begin();
        }
```
9.49.SineEase动画
SineEase:创建使用正弦公式加速和/或减速的动画.
ft=1-[sin(1-t)*(Π/2)]
下面用一个示例演示SineEase的EaseIn动画效果,相比PowerEase平缓了许多.第二个模拟了EaseIn动画轨迹图形
``` bash
   <Grid Background="Transparent">
                    <Grid.Resources>
                        <Storyboard x:Name="SineStoryboard">
                            <DoubleAnimation From="0" To="400" Duration="0:0:3" Storyboard.TargetName="ellipse17"
                                             Storyboard.TargetProperty="(Canvas.Top)">
                                <DoubleAnimation.EasingFunction>
                                    <SineEase EasingMode="EaseIn"/>

                                </DoubleAnimation.EasingFunction>
                            </DoubleAnimation>
                            <DoubleAnimation From="0" To="400" Duration="0:0:3" Storyboard.TargetName="ellipse18"
                                             Storyboard.TargetProperty="(Canvas.Left)">
                                
                            </DoubleAnimation>
                            <DoubleAnimation From="400" To="200" Duration="0:0:3" Storyboard.TargetName="ellipse18"
                                             Storyboard.TargetProperty="(Canvas.Top)">
                                <DoubleAnimation.EasingFunction>
                                    <SineEase EasingMode="EaseIn"/>
                                </DoubleAnimation.EasingFunction>
                                
                            </DoubleAnimation>
                        </Storyboard>
                    </Grid.Resources>
                    <Canvas Margin="12,0,12,0">
                        <Ellipse Name="ellipse17" Width="50" Height="50" Fill="Blue"/>
                        <Ellipse x:Name="ellipse18" Fill="Red" Width="80"
                                 Height="80" Canvas.Left="0" Canvas.Top="400"/>
                        <Button Margin="0,500,0,0" Content="启动动画" Height="80" Click="Button_Click_17"/>
                    </Canvas>
                </Grid>
 private void Button_Click_17(object sender, RoutedEventArgs e)
        {
            SineStoryboard.Begin();

        }
```
9.5.基于帧动画
9.51.原理
基于帧动画的创建主要是依赖CompositionTarget类,CompositionTarget是一个静态类,表示应用程序要在其上进行绘制的显示画面.每次绘制场景时,都会引发Rendering事件,创建基于帧动画的语法很简单,只需要为静态的CompositionTarget.Rendering事件关联事件处理程序,然后在事件处理程序处理动画的内容.
如果要结束动画,就移除CompositionTarget.Rendering关联事件处理程序.由此可见,基于帧动画只能使用C#代码进行创建,不能像其他动画用Xaml来编写,同时也无法在样式,控件模板或数据模板中定义.
当构建基于帧的动画时:它们不是依赖于时间的.动画可能在性能好的设备上运动更快,因为帧速率会增加,从而会更频繁的调用CompositionTarget.Rendering事件.CompositionTarget.Rendering事件的调用频率并不是一个固定值,它是和设备及当前应用程序的运行状况紧密相关,如果程序在基于帧动画的时候还在处理其他耗时操作时,CompositionTarget.Rendering事件的调用频率就会比较低.由于CompositionTarget.Rendering事件可以根据当前的状况来调整动画的频率使得动画更加流畅,所以它比直接使用定时器DispatcherTimer做原始的动画处理更加优越.
9.52.基于帧动画的应用场景
1.线性插值动画和关键帧动画实现不了或很难实现的动画.
2.创建一个基于物理的动画或者构建粒子效果模型如火焰,雪以及气泡.
9.53.基于帧动画的实现
下面通过一个例子来演示基于帧动画的运用,动画要实现的效果是通过在Canvas面板中触摸滑动来控制矩形Rectangle滑块的运动,滑块会往面板触摸的方向运动,但是滑块不能离开Canvas面板.
``` bash
 <Canvas Background="Gray" PointerMoved="Canvas_PointerMoved">
                    <Rectangle x:Name="prectangle" Height="50" Width="100"
                               RadiusX="12.5" RadiusY="12.5">
                        <Rectangle.Fill>
                            <LinearGradientBrush>
                                <GradientStop Color="Black" Offset="0"/>
                                <GradientStop Color="White" Offset="0.5"/>
                                <GradientStop Color="Black" Offset="1"/>
                            </LinearGradientBrush>
                        </Rectangle.Fill>
                    </Rectangle>
                </Canvas>
 ///基于帧动画
            ///
            CompositionTarget.Rendering += CompositionTarget_Rendering;
            this.prectangle.RenderTransform = translateTransform;
/// <summary>
        /// 处理基于帧的事件,动画的逻辑在这个事件里面进行处理
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void CompositionTarget_Rendering(object sender, object e)
        {
           var currentTime=DateTime.Now;
            //计算两帧之间的时间差,把时间差作为计算位移的系数
            double elapsedTime = (currentTime - preTime).TotalSeconds;
            preTime=currentTime;
            //控制矩形的移动不超过画布面板的边界
            translateTransform.X += mouseLocation.X * elapsedTime;
            if (translateTransform.X > 300) translateTransform.X = 300;
            if(translateTransform.X<0)translateTransform.X = 0;
            translateTransform.Y+= mouseLocation.Y * elapsedTime;
            if (translateTransform.Y > 450) translateTransform.Y = 450;
            if (translateTransform.Y < 0)translateTransform.Y= 0;
           
        }
 //触摸点的位置
        Point mouseLocation;
        //用于矩形的位移变换改变矩形的位置
        TranslateTransform translateTransform=new TranslateTransform();
        //用于保存上一帧时间,计算时间差
        DateTime preTime=DateTime.Now;

        /// <summary>
        /// 通过PointerMoved事件获取当前的触摸点相对于矩形的坐标,滑动点在矩形左边X值为负值,右边X值为正值.在上边Y值为负值,在下便Y值为正值.
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void Canvas_PointerMoved(object sender, PointerRoutedEventArgs e)
        {
            mouseLocation = e.GetCurrentPoint(this.prectangle).Position;
        }
```
9.61.帧速率
帧速率是用于测量显示帧数的量度,测量单位为"每秒显示帧数",是指每秒钟刷新画面的帧数，判断一个动画是否能够流畅的运行,就需要关注动画的帧速率指标是否足够高.
在Win10里面虽然不能直接设置动画帧速率,但是可以测量出来,在应用程序里面把属性Application.Current.Host.Settings.EnableFrameRateCounter设置为true将可以在设备的顶部左侧看到帧速率的计数变化情况,默认情况采用Debug调试状态下就会显示出应用当前的帧速率.帧速率的范围是0-60，帧速率越大,应用程序的响应性能越高.帧速率的值一般应在20以上,该值小于30表示存在性能问题.
默认情况下会在文件App.xaml.cs中添加启用帧速率计数器的代码.
``` bash
 if (System.Diagnostics.Debugger.IsAttached) { 
            this.DebugSettings.EnableFrameRateCounter = true;
            }
```
表示当启动Debug状态调试应用程序时会启用帧速率计数器.其中Application.Current.Host.Settings.EnableFrameRateCounter=true表示启用帧速率计数器,设置为false则禁用帧速率计数器.
9.62.UI线程和构图线程
2.构图线程
工作是合并图形纹理并将其传递到GPU以供绘制,设备上的GPU将在称为自动缓存的进程中自动缓存并处理运行在构图线程上的动画,构图线程处理与变换特效(RenderTransform)和三维特效(Projection)属性关联的动画(如针对ScaleTransform,TranslateTransform,RotateTransform,PlaneProjection的属性改变的Storyboard动画)都是完全运行在构图线程上的.另外,Opacity和Clip属性设置也由构图线程处理.但是,如果使用OpacityMask或非矩形剪辑,则这些操作将传递到UI线程.
3.动画和线程
下面通过一个例子来演示用两种不同的方法来实现一个相同的动画效果,所实现的动画效果是让矩形的高度慢慢变成原来的两倍,第一种方式是用线性插值动画对矩形的Height属性进行动画处理,第二种方式也是线性插值动画,但是针对的动画目标属性是ScaleTransform的ScaleY属性,然后用一个按钮单击事件阻塞UI线程2秒钟,可以看到针对Height属性的动画会暂停2秒钟再继续运行,而针对ScaleTransform的ScaleY属性不会受UI线程阻塞的影响.
``` bash
 <StackPanel>
                    <StackPanel.Resources>
                        <Storyboard x:Name="heightStoryboard">
                            <!--针对Height属性的动画-->
                            <DoubleAnimation Storyboard.TargetName="rectangle1" Storyboard.TargetProperty="Height"
                                             RepeatBehavior="Forever"
                                             EnableDependentAnimation="True"
                                             From="100" To="200" Duration="0:0:2">
                            </DoubleAnimation>
                        </Storyboard>
                        <Storyboard x:Name="scaleTransformStoryboard">
                            <!--针对ScaleTransform的ScaleY属性的动画-->
                            <DoubleAnimation Storyboard.TargetName="scaleTransform1" Storyboard.TargetProperty="ScaleY" RepeatBehavior="Forever" From="1" To="2" Duration="0:0:2">
                            </DoubleAnimation>
                        </Storyboard>
                    </StackPanel.Resources>
                    <Button Content="阻塞UI线程" Click="Button_Click_18"/>
                    <Button x:Name="heightAnimationButton" Content="Height属性动画" Click="heightAnimationButton_Click"/>
                    <Button x:Name="scaleTransformAnimationButton" Content="ScaleTransform属性动画" Click="scaleTransformAnimationButton_Click"/>
                    <Rectangle Height="100" Fill="Blue" x:Name="rectangle1">
                        <Rectangle.RenderTransform>
                            <ScaleTransform x:Name="scaleTransform1"/>
                        </Rectangle.RenderTransform>
                    </Rectangle>
                </StackPanel>
 /// <summary>
        /// 阻塞线程2秒钟
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void Button_Click_18(object sender, RoutedEventArgs e)
        {
            Task.Delay(2000).Wait();
        }

        private void heightAnimationButton_Click(object sender, RoutedEventArgs e)
        {
            //播放改变高度属性的动画,高度由100变成200
            scaleTransformStoryboard.Stop();
            heightStoryboard.Begin();
        }

        private void scaleTransformAnimationButton_Click(object sender, RoutedEventArgs e)
        {
            heightStoryboard.Stop();
            scaleTransformStoryboard.Begin();
        }
```
9.7.模拟实现微信的彩蛋动画
9.72.星星创建工厂
实现星星的创建工厂StarFactory类,StarFactory类的作用是创建动画里面的星星对象,动画的实现需要向调用StarFactory类创建出星星对象,然后对星星进行动画处理,所以StarFactory类是一个非常单一的星星构造工厂,不涉及动画的操作,只涉及Path对象的创建
下面是星星图形形状的构造封装的3个方法:1._RefactorPoints方法是用于取两个点线段之间的1/3,2/3点和两点间的随机点,最后再加上原来的两个点,返回5个点的点集合;2._RecurseSide方法是封装两个点之间递归之后所取到的点集合;3._CreatePath方法则是把这些点集合连接起来创建一个Path图形表示星星图形.
``` bash
 public class StarFactory
    {
        public StarFactory() { }
        /// <summary>
        /// 把两个点转换成多级的点的集合
        /// </summary>
        /// <param name="a"></param>
        /// <param name="b"></param>
        /// <param name="level"></param>
        /// <returns></returns>
        private static List<Point> _RecurseSide(Point a,Point b,int level) {
            ///递归完成后,返回此线段
            if (level==0)
            {
                return new List<Point> { a,b};
            }
            else
            {
                //首先,需要建立起第一次递归的点的列表,一直递归到级别为0
                List<Point> newPoints = new List<Point>();
                //把区域分成5个点
                foreach (Point item in _RefactorPoints(a,b))
                {
                    newPoints.Add(item);
                }
                List<Point> aggregatePoints= new List<Point>();
                //把每一个线段进一步分解
                for (int i = 0; i < newPoints.Count; i++)
                {
                    int y = i + 1 == newPoints.Count ? 0 : i + 1;
                    aggregatePoints.AddRange(_RecurseSide(newPoints[i], newPoints[y],level-1));
                }
                return aggregatePoints;
            }
        }
      static Random _random= new Random();
        /// <summary>
        /// 通过输入两个点来构建一个有多个三角形组成的Star形状
        /// </summary>
        /// <param name="a"></param>
        /// <param name="b"></param>
        /// <returns></returns>
        /// <exception cref="NotImplementedException"></exception>
        private static IEnumerable<Point> _RefactorPoints(Point a, Point b)
        {
           //第一个点
           yield return a;
            double dX = b.X - a.Y;
            double dY= b.Y - a.Y;
            //第一点到第二个点1/3处的一个点
            yield return new Point(a.X+dX/3.0,a.Y+dY/3.0);
            double factor=_random.NextDouble()- 0.5;
            double vX = (a.X + b.X) / (2.0 + factor)+Math.Sqrt(3.0+factor)*(b.Y-a.Y)/(6.0+factor*2.0);
            double vY = (a.Y + b.Y) / (2.0 + factor) + Math.Sqrt(3.0+factor)*(a.X-b.X)/(6.0+factor*2.0);
            //中间的三角形的顶点
            yield return new Point(vX,vY);
            //第二个点到第一个点1/3处的一个点
            yield return new Point(b.X-dX/3.0,b.Y-dY/3.0);
            //第二个点
            yield return b;
        }
        /// <summary>
        /// 使用一系列点来创建路径图形
        /// </summary>
        /// <returns></returns>
        private static Path _CreatePath(List<Point> points) { 
            PathSegmentCollection segments= new PathSegmentCollection();
            bool first = true;
            //把点添加到线段里面
            foreach (Point item in points)
            {
                if (first)
                {
                    first = false;
                }
                else
                {
                    segments.Add(new LineSegment { Point = item });
                }
            }
            PathGeometry pathGeometry= new PathGeometry();
            //通过线段构建几何图形
            pathGeometry.Figures.Add(new PathFigure
            {
                IsClosed = true,
                StartPoint = points[0],
                Segments = segments
            });
            return new Path { Data = pathGeometry };
        }


    }
```
2.星星颜色的随机生成
这里用LinearGradientBrush来填充Path图形,封装的方法_GetColor表示创建随机的颜色对象,_ColorFactory表示对Path图形填充随机的颜色画刷.
``` bash
/// <summary>
        /// 添加颜色到路径图形
        /// </summary>
        /// <param name="input"></param>
        private static void _ColorFactory(Path input) {
            LinearGradientBrush brush = new LinearGradientBrush();
            brush.StartPoint = new Point(0,0);
            brush.EndPoint= new Point(1.0,1.0);
            GradientStop start=new GradientStop();
            start.Color = _GetColor();
            start.Offset = 0;
            GradientStop middle=new GradientStop();
            middle.Color = _GetColor();
            middle.Offset=_random.NextDouble();
            GradientStop end=new GradientStop();
            end.Color= _GetColor();
            end.Offset = 1.0;
            brush.GradientStops.Add(start);
            brush.GradientStops.Add(middle);
            brush.GradientStops.Add(end);
            input.Fill = brush;
        }
        /// <summary>
        /// 获取一个随机的颜色
        /// </summary>
        /// <returns></returns>
        private static Color _GetColor()
        {
            Color color = new Color();
            color.A = (byte)(_random.Next(200) + 20);
            color.R = (byte)(_random.Next(200) + 50);
            color.G = (byte)(_random.Next(200) + 50);
            color.B = (byte)(_random.Next(200) + 50);
            return color;
        }
```
3.创建星星对象
创建星星对象需要先有3个点,然后再利用这3个点根据创建星星图形的原理(3*4的n次方),n表示星星递归的层次.创建星星图形,然后用随机颜色画刷填充图形.
``` bash
 const int MIN = 0;
        const int MAX = 2;
        //随机数产生器
        static readonly Random _random=new Random();
        /// <summary>
        /// 创建一个Star
        /// </summary>
        /// <returns></returns>
        public static Path Create() { 
        Point a=new Point(0,0);
            Point b = new Point(_random.NextDouble() * 70.0 + 15.0, 0);
            Point c=new Point(0,b.X);
            int levels=_random.Next(MIN,MAX);
            List<Point> points=new List<Point>();
            points.AddRange(_RecurseSide(a,b,levels));
            points.AddRange(_RecurseSide(b,c,levels));
            points.AddRange(_RecurseSide(c,a,levels));
            //画边
            Path retVal=_CreatePath(points);
            //添加颜色
            _ColorFactory(retVal);
            //建立一个旋转的角度
            RotateTransform rotate=new RotateTransform();
            rotate.CenterX = 0.5;
            rotate.CenterY = 0.5;
            rotate.Angle=_random.NextDouble()*360.0;
            retVal.SetValue(Path.RenderTransformProperty,rotate);
            return retVal;
        }
```
9.73.实现单个星星的动画轨迹
要实现对星星实体(StarEntity类)的封装,在StarEntity类里面要实现基于帧动画,在帧刷新事件处理程序里实现星星飘落的动画逻辑.首先需要处理的是确定星星在区域最顶部的随机位置,下落的随机速度和方向,然后在动画的过程中需要去判断星星是否碰撞到了区域的左边距或右边距,碰撞之后则需要再反弹回来往另外一边运动,最后还需要判断星星是否已经落到了最底下,如果落到了区域最底下,则需要移除CompositionTarget.Rendering事件和从画布上移除星星图形,还要触发StarflakeDied事件告知调用方星星已经销毁掉了.
``` bash
 public class StarEntity
    {
        //左边距
        const double LEFT = 460;
        //上边距
        const double TOP = 800;
        //离开屏幕
        const double GONE = 480;
        //随机近似数
        private double _affinity;
        //Star实体的唯一ID
        private Guid _identifier=Guid.NewGuid();
        //随机数产生器
        private static Random _raodom=new Random();
        //Star所在的画布
        private Canvas _surface;
        //获取Star所在的画布
        private Canvas Surface {
            get { return _surface; }
        }
        //x,y坐标和相对速度
        private double x, y, velocity;
        //Star的路径图形
        private Path _starflake;
        /// <summary>
        /// 当Star飘落到底下时回收Star事件
        /// </summary>
        private EventHandler StarflakeDied;

        //获取Star实体的唯一ID
        public Guid Identifier
        {
            get { return _identifier; }
        }
        //默认的构造器
        public StarEntity(Action<Path> insert) : this(insert, true) { }
        /// <summary>
        /// 星星对象构造方法 是否从顶下落下
        /// </summary>
        /// <param name="insert"></param>
        /// <param name="v"></param>
        public StarEntity(Action<Path> insert, bool fromTop)
        {
            _starflake = StarFactory.Create();
            ///产生0-1的随机数
            _affinity=_raodom.NextDouble();
            ///设置速度,初始化x,y轴
            velocity=_raodom.NextDouble()*2;
            x = _raodom.NextDouble() * LEFT;
            y=fromTop?0:_raodom.NextDouble() * TOP;
            //设置Star在画布的位置
            _starflake.SetValue(Canvas.LeftProperty,x);
            _starflake.SetValue(Canvas.TopProperty,y);
            //添加到画布上
            insert(_starflake);
            //记录下Star的画布
            _surface = _starflake.Parent as Canvas;
            //订阅基于帧动画事件
            CompositionTarget.Rendering += CompositionTarget_Rendering;
        }
        /// <summary>
        /// 基于帧动画事件处理
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        /// <exception cref="NotImplementedException"></exception>
        private void CompositionTarget_Rendering(object sender, object e)
        {
            _Frame();
        }
        /// <summary>44
        /// Star下落的每一帧的处理
        /// </summary>
        private void _Frame()
        {
            //下降的y轴的大小
            y = y + velocity + 3.0 * _raodom.NextDouble() - 1.0;
            //判断是否离开了屏幕
            if (y>GONE)
            {
                CompositionTarget.Rendering -= CompositionTarget_Rendering;
                _surface.Children.Remove(_starflake);
                //通知外部,Star已经被清除
                EventHandler handler = StarflakeDied;
                if (handler!=null)
                {
                    handler(this,EventArgs.Empty);
                }
            }
            else
            {
                //水平轻推
                double xFactor = 10.0 * _affinity;
                if (_affinity < 0.5) xFactor *= -1.0;//小于0.5向左边移动,大于0.5向右边移动，等于0.5垂直下降
                {
                    x = x + _raodom.NextDouble() * xFactor;
                }
                //左边的边缘
                if (x<0)
                {
                    x = 0;
                    _affinity = 1.0 - _affinity;
                }
                //右边的边缘
                if (x>LEFT)
                {
                    x = LEFT;
                    _affinity=1.0- _affinity;
                }
                _starflake.SetValue(Canvas.LeftProperty,x);
                _starflake.SetValue(Canvas.TopProperty,y);
            }
            //转动
            RotateTransform rotate = (RotateTransform)_starflake.GetValue(Path.RenderTransformProperty);
            rotate.Angle += _raodom.NextDouble() * 4.0 * _affinity;

        }
        /// <summary>
        /// 重载获取唯一的对象码
        /// </summary>
        /// <returns></returns>
        public override int GetHashCode()
        {
            return Identifier.GetHashCode();
        }
        public override bool Equals(object obj)
        {
            return obj is StarEntity && ((StarEntity)obj).Identifier.Equals(Identifier);
        }
    }
```
9.74.封装批量星星飘落的逻辑
实现一个StarBehavior类用附加属性的方式在Canvas上添加批量的星星飘落的动画,StarBehavior类里面通过AttachStarFlake属性表示是否在该Canvas面板上添加星星飘落动画,当设置为true则表示触发动画的开始,false则表示停止添加星星,直到星星全部飘落到底下时动画停止.在开始播放动画时会初始化多个StarEntity对象,并运行其飘落的动画效果,当飘落到底下StarEntity对象被销毁时,会触发StarflakeDied事件,在StarflakeDied事件里面继续初始化新的StarEntity对象,如果动画停止了,beginning=false,则不再创建新的StarEntity对象.
``` bash
using App1.Models;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;

namespace App1.Behaviors
{
    /// <summary>
    /// StarBehavior类管理附加属性的行为触发批量星星的构造和动画的实现
    /// </summary>
    public static class StarBehavior
    {
        //屏幕上生成的Star数量
        const int CAPACITY = 75;
        //动画是否已经开始的标识符
        private static bool begining=false;
        //Star对象列表
        private static List<StarEntity> _starflakes=new List<StarEntity>(CAPACITY);
        //添加动画效果的属性


        public static bool GetAttachStarFlake(DependencyObject obj)
        {
            return (bool)obj.GetValue(AttachStarFlakeProperty);
        }

        public static void SetAttachStarFlake(DependencyObject obj, bool value)
        {
            obj.SetValue(AttachStarFlakeProperty, value);
        }

        // Using a DependencyProperty as the backing store for AttachStarFlake.  This enables animation, styling, binding, etc...
        public static readonly DependencyProperty AttachStarFlakeProperty =
            DependencyProperty.RegisterAttached("AttachStar", typeof(bool), typeof(StarBehavior), new PropertyMetadata(false,new PropertyChangedCallback(_Attach)));
        /// <summary>
        /// 附加属性改变事件处理方法
        /// </summary>
        /// <param name="d"></param>
        /// <param name="e"></param>
        /// <exception cref="NotImplementedException"></exception>
        private static void _Attach(DependencyObject d, DependencyPropertyChangedEventArgs e)
        {
           Canvas canvas= d as Canvas;
            if (canvas != null && e.NewValue!=null && e.NewValue.GetType().Equals(typeof(bool))) {
                if ((bool)e.NewValue) {
                    //画布上还有子元素证明星星还没完全飘落下去
                    if (canvas.Children.Count>0)
                    {
                        return;
                    }
                    //开始动画
                    begining=true;
                    for (int i = 0; i < _starflakes.Capacity; i++)
                    {
                        StarEntity starflake = new StarEntity((o) => canvas.Children.Add(o));
                        starflake.StarflakeDied +=new EventHandler(Starflake_StarflakeDied);
                        _starflakes.Add(starflake);
                    }
                }
                else
                {
                    //结束动画
                    begining = false;
                }
            }
        }
        //回收Star事件
        private static void Starflake_StarflakeDied(object sender, EventArgs e)
        {
            StarEntity starflake=sender as StarEntity;
            //获取Star的面板,用来添加一个新的Star
            Canvas canvas = starflake.Surface;
            _starflakes.Remove(starflake);
            if (begining)
            {
                //如果动画还在继续,运行一个Star消失之后再创建一个新的Star
                StarEntity newFlake = new StarEntity((o)=>canvas.Children.Add(o),true);
                newFlake.StarflakeDied += Starflake_StarflakeDied;
                _starflakes.Add(newFlake);
            }
        }
    }
}

```
9.75.Star飘落演示
``` bash
 <Grid>
                    <Canvas x:Name="myCanvas" HorizontalAlignment="Stretch" VerticalAlignment="Stretch">

                    </Canvas>
                    <Button x:Name="btn" VerticalAlignment="Bottom" Content="开始星星飘落" Click="btn_Click"/>
                </Grid>
 /// <summary>
        /// 按钮事件,播放动画和停止动画
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private async void btn_Click(object sender, RoutedEventArgs e)
        {
            if ((bool)myCanvas.GetValue(StarBehavior.AttachStarFlakeProperty) == false)
            {
                //要等所有的星星全部落下去之后才可以再次播放动画
                if (myCanvas.Children.Count > 0)
                {
                    await new MessageDialog("星星动画未完全结束").ShowAsync();
                    return;
                }
                myCanvas.SetValue(StarBehavior.AttachStarFlakeProperty, true);
                button.Content = "停止";
            }
            else {
                myCanvas.SetValue(StarBehavior.AttachStarFlakeProperty, false);
                btn.Content = "开始";
            }
        }
```
#### 样式和模板
10.11.创建样式
10.21.控件模板(ControlTemplate)
``` bash
 <Button Content="Hello World">
                    <Button.Template>
                        <ControlTemplate>
                            <Grid>
                                <Ellipse Width="{TemplateBinding Button.Width}" Height="{TemplateBinding Height}"
                                         Fill="{TemplateBinding Button.Background}" Stroke="Red"/>
                                <TextBlock Margin="5,0,0,0" FontSize="50"
                                           VerticalAlignment="Center" HorizontalAlignment="Center" Text="{TemplateBinding Button.Content}"/>
                                <TextBlock FontSize="50" Foreground="Red" VerticalAlignment="Center" HorizontalAlignment="Center"
                                           Text="{TemplateBinding Button.Content}"/>
                            </Grid>
                        </ControlTemplate>
                    </Button.Template>
                </Button>
```
10.22.ContentControl和ContentPresenter
ContentPresenter是用来显示控件ContentControl的Content属性的,ContentPresneter也有Content属性,默认的情况下可以把Content定义的内容投影到ContentControl的Content里面
``` bash
 <Button>
                    <Button.Template>
                        <ControlTemplate>
                            <Grid>
                                <ContentPresenter HorizontalAlignment="{TemplateBinding HorizontalAlignment}" VerticalAlignment="{TemplateBinding VerticalAlignment}"/>
                            </Grid>
                        </ControlTemplate>
                    </Button.Template>
                    <Button.Content>
                        <Rectangle Fill="Red" Height="50" Width="50"/>
                    </Button.Content>
                </Button>
```
10.23.视觉状态管理(VisualStatesManager)
通过ControlTemplate修改过的Button控件,当我们单击控件时发现比系统默认的Button控件少了一些点击的状态,这时候就需要使用视觉状态管理类VisualStateManager了
VisualStateManager的作用是控制控件的状态转换,不同状态下的UI显示效果的区别及转换过程动画.视觉状态管理主要包括VisualStates(视觉状态),VisualStateGroups(视觉状态组)和VisualTransitions（视觉过度转换）.VisualStates是指控件在不同状态下显示的效果,如Button控件默认就包含(Normal,MouseOver,Pressed,Disabled,Unfocused,Focused)六种状态.Visual State Groups是为有互斥效果的控件提供的,对于相同的视觉状态组是互斥的,对于不同的视觉状态组是不互斥的,Visual Transitions是视觉状态切换时的过度的动画效果.
VisualStateManager通过在控件上设置VisualStateManager.VisualStateGroups附加属性向控件添加VisualStates和VisualTransitions.VisualStates是VisualState的集合,里面定义了多个VisualState表示控件在不同状态下的视觉表现效果,使用了Storyboard故事板属性来实现当前控件状态的转换,VisualTransitions是VisualTransition的集合,但是VisualTransitions不是必须的,如果控件在不同状态之间转换的时候不需要动画效果,就可以省略掉VisualTransitions.VisualTransition主要有三个属性:From(当前的状态),To(转换的状态)和GeneratedDuration(转换时间).状态的转换是通过调用VisualStateManager类的GoToState方法来实现的.
VisualStateManager的Xaml语法如下所示
``` bash
 <Button Content="Hello World" LostFocus="Button_LostFocus" Tapped="Button_Tapped">
                    <Button.Template>
                        <ControlTemplate TargetType="Button">
                            <Border>
                                <VisualStateManager.VisualStateGroups>
                                    <VisualStateGroup x:Name="CommonStates">
                                        <VisualStateGroup.Transitions>
                                            <VisualTransition From="Test1" To="Test2" GeneratedDuration="0:0:1.5">
                                                <Storyboard>
                                                    
                                                </Storyboard>
                                            </VisualTransition>
                                        </VisualStateGroup.Transitions>
                                        <!--创建状态Test1把Border背景的颜色改成红色-->
                                        <VisualState x:Name="Test1">
                                            <Storyboard>
                                                <ColorAnimation Storyboard.TargetName="BorderBrush" Storyboard.TargetProperty="Color" To="Red"/>
                                            </Storyboard>
                                        </VisualState>
                                        <!--创建状态Test2把Border背景的颜色改成蓝色-->
                                        
                                            <VisualState x:Name="Test2">
                                            <Storyboard>
                                                <ColorAnimation Storyboard.TargetName="BorderBrush" Storyboard.TargetProperty="Color" To="Blue"/>
                                            </Storyboard>
                                        </VisualState>
                                    </VisualStateGroup>
                                </VisualStateManager.VisualStateGroups>

                                <Border.Background>
                                    <!--定义Border背景的颜色,用于测试不同状态的显示效果-->
                                    <SolidColorBrush x:Name="BorderBrush" Color="Black"/>

                                </Border.Background>
                                <Grid>
                                    <Ellipse x:Name="ellipse19" Width="{TemplateBinding Button.Width}" Height="{TemplateBinding Height}" Fill="{TemplateBinding Button.Background}" Stroke="Red"/>
                                    <TextBlock Margin="5,0,0,0" FontSize="50" VerticalAlignment="Center" HorizontalAlignment="Center"
                                               Text="{TemplateBinding Button.Content}"/>
                                    <TextBlock FontSize="50" Foreground="Red" VerticalAlignment="Center" HorizontalAlignment="Center" Text="{TemplateBinding Button.Content}"/>
                                </Grid>
                            </Border>
                        </ControlTemplate>
                    </Button.Template>
                </Button>
 /// <summary>
        /// 跳转到状态Test1
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void Button_LostFocus(object sender, RoutedEventArgs e)
        {
            VisualStateManager.GoToState(sender as Button,"Test1",true);
        }
        /// <summary>
        /// 跳转到状态Test2
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void Button_Tapped(object sender, TappedRoutedEventArgs e)
        {
            VisualStateManager.GoToState(sender as Button,"Test2",true);
        }
```
10.24.数据模板(DataTemplate)
DateTemplate和ControlTemplate所负责的任务是不一样的,ControlTemplate用于描述控件本身,而DataTemplate用于描述控件的数据对象的视觉格式.这两者并不是毫无关联,相反它们通常需要合作完成一些工作.在控件的模板上这两者有着非常微妙的关系,可以利用DataTemplate去辅助ControlTemplate实现一些效果.
``` bash
  <Button Content="Hello World" LostFocus="Button_LostFocus" Tapped="Button_Tapped">
                    <Button.Template>
                        <ControlTemplate TargetType="Button">
                            <Border>
                                <VisualStateManager.VisualStateGroups>
                                    <VisualStateGroup>
                                        <VisualStateGroup.Transitions>
                                            <!--状态Test1转换为状态Test2的颜色变化动画-->
                                            <VisualTransition From="Test1" To="Test2" GeneratedDuration="0:0:1.5">
                                                <Storyboard>
                                                    
                                                </Storyboard>
                                            </VisualTransition>
                                           
                                        </VisualStateGroup.Transitions>
                                        <!--创建状态Test1把Border背景的颜色改成红色-->
                                        <VisualState x:Name="Test3">
                                            <Storyboard>
                                                <ColorAnimation Storyboard.TargetName="BorderBrush1" Storyboard.TargetProperty="Color" To="Red"/>
                                            </Storyboard>
                                            
                                        </VisualState>
                                        <!--创建状态Test2把Border背景的颜色改成蓝色-->
                                        <VisualState x:Name="Test4">
                                            <Storyboard>
                                                <ColorAnimation Storyboard.TargetName="BorderBrush1" Storyboard.TargetProperty="Color" To="Blue"/>
                                            </Storyboard>
                                        </VisualState>
                                        
                                    </VisualStateGroup>
                                </VisualStateManager.VisualStateGroups>

                                <Border.Background>
                                    <SolidColorBrush x:Name="BorderBrush1"
                                                     Color="Black"/>
                                </Border.Background>
                                <Grid>
                                    <Ellipse x:Name="ellipse20" Width="{Binding Width}" Height="{Binding Height}"
                                             Fill="{Binding Background}"
                                             Stroke="Red"/>
                                    <TextBlock Margin="5,0,0,0" FontSize="50" VerticalAlignment="Center" HorizontalAlignment="Center"
                                               Text="{Binding}"/>
                                    <TextBlock FontSize="50" Foreground="Red" VerticalAlignment="Center" HorizontalAlignment="Center" Text="{Binding}"/>
                                    
                                </Grid>
                            </Border>
                        </ControlTemplate>
                    </Button.Template>
                </Button>
```
10.25.ItemTemplate,ContentTemplate和DataTemplate
ContentControl类是内容控件的基类,如Button,CheckBox,最明显的特征是这个控件有Content属性,具有Content属性的系统控件都是ContentControl的子类,ItemsControl类是列表控件的基类,如ListBox,它和ContentControl类类似,只不过ContentControl类是单项内容,ItemsControl是多项内容.
所有继承自ContentControl内容控件的ContentTemplate属性和所有继承自ItemsControl列表控件的ItemTemplate属性,都是DataTemplate类型,意思就是可以通过DataTemplate定义ContentControl和ItemsControl的控件的UI效果和数据显示.
10.26.数据模板的使用
DataTemplate是一种可视化的数据模板,其强大的作用在于可以把数据通过绑定的方式展现在控件上.
用DataTemplate去实现UI控件的内容显示,其实DataTemplate最主要的作用并不是去取代ControlTemplate的样式定义,而是通过数据绑定把数据控件的数据源信息展现到控件上.
``` bash
<Style x:Name="ItemsControlStyle" TargetType="ItemsControl">
            <Setter Property="Template">
                <Setter.Value>
                    <ControlTemplate TargetType="ItemsControl">
                        <ScrollViewer BorderBrush="Red" BorderThickness="6">
                            <StackPanel Orientation="Horizontal" Background="Blue">
                                <Border BorderBrush="Yellow" BorderThickness="3">
                                    <ItemsPresenter/>
                                </Border>
                            </StackPanel>
                        </ScrollViewer>
                    </ControlTemplate>
                </Setter.Value>
            </Setter>
        </Style>
 ObservableCollection<People> peoples = new ObservableCollection<People>();
            peoples.Add(new People() { FirstName="lee2",LastName="Terry2"});
            peoples.Add(new People() { FirstName="lee3",LastName="Terry3"});
            peoples.Add(new People() { FirstName = "lee4", LastName = "Terry4" });
            peoples.Add(new People() { FirstName = "lee5", LastName = "Terry5" });
            itemsControl.ItemsSource = peoples;
            itemsControl1.ItemsSource = peoples; 
  <ItemsControl x:Name="itemsControl1" ItemTemplate="{StaticResource PersonNameDataTemplate}" Style="{StaticResource ItemsControlStyle}"/>
```
10.27.读取和更换数据模板
动态更换样式
1.定义3个DataTemplate资源,一个是非选中状态,一个是选中状态,还有一个是默认的状态,其实默认状态和非选中状态是一样的,但是默认状态的数据项样式不能在C#中再次调用.在两个模板中都添加了Tap事件,用户捕获点击事件.
``` bash
 <ListBox x:Name="listbox" ItemTemplate="{StaticResource dataTemplateDefaultKey}"/>
 <!--选中数据项的样式-->
        <DataTemplate x:Key="dataTemplateSelectKey" x:Name="dataTemplateSelectName">
            <Grid Tapped="StackPanel_Tapped" Background="Red">
                <TextBlock Text="{Binding LastName}" FontSize="50"/>
            </Grid>
        </DataTemplate>
        <!--默认数据项的样式,注意默认的数据项样式不能在C#中再次调用-->
        <DataTemplate x:Key="dataTemplateDefaultKey" x:Name="dataTemplateDefaultName">
            <StackPanel Orientation="Horizontal" Tapped="StackPanel_Tapped">
                <TextBlock Text="{Binding LastName}"/>
                <TextBlock Text=","/>
                <TextBlock Text="{Binding FirstName}"/>
            </StackPanel>
        </DataTemplate>
        <!--非选中数据项的样式-->
        <DataTemplate x:Key="dataTemplateNoSelectKey" x:Name="dataTemplateNoSelectName">
            <StackPanel Orientation="Horizontal" Tapped="StackPanel_Tapped">
                <TextBlock Text="{Binding LastName}"/>
                <TextBlock Text=","/>
                <TextBlock Text="{Binding FirstName}"/>
            </StackPanel>
        </DataTemplate>
  private void StackPanel_Tapped(object sender, TappedRoutedEventArgs e)
        {
            //获取到的对象是ListBoxItem
            ListBoxItem myListBoxItem = (ListBoxItem)(listbox.ContainerFromItem((sender as Panel).DataContext));
            //在ListBoxItem中查找ContentPresenter
            ContentPresenter myContentPresenter = FindVisualChild<ContentPresenter>(myListBoxItem);
            //ContentPresenter myContentPresenter = (ContentPresenter)(listbox.ContainerFromItem((sender as Panel).DataContext));
            if (myContentPresenter.ContentTemplate.Equals(dataTemplateSelectName)) { 
                myContentPresenter.ContentTemplate = dataTemplateNoSelectName;
            }
            else
            {
                myContentPresenter.ContentTemplate = dataTemplateSelectName;
            }
        }

        private childItem FindVisualChild<childItem>(DependencyObject obj) where childItem:DependencyObject
        {
            for (int i = 0; i < VisualTreeHelper.GetChildrenCount(obj); i++)
            {
                DependencyObject child=VisualTreeHelper.GetChild(obj, i);
                if (child != null && child is childItem)
                    return (childItem)child;
                else
                {
                    childItem childOfChild=FindVisualChild<childItem>(child);
                    if (childOfChild != null)
                        return childOfChild;
                }
            }
            return null;
        }
```
#### 数据绑定
绑定四个组件:绑定目标对象,目标属性,绑定源,以及要使用的绑定源中的值的路径.
例如,如果要将TextBox的内容绑定到Employee对象的Name属性,则目标对象是TextBox,目标属性是Text，要使用的值是Name,源对象是Employee对象.
11.12.创建绑定
1.定义源对象
2.设置DataContext属性绑定到源对象
3.使用Binding标记扩展来绑定数据源对象的属性.
``` bash
 public class MyData { 
        public string Title { get; set; }
    }
  public MyData myData = new MyData { Title = "这是绑定的标题!" };
this.DataContext = myData;
 <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
                    <TextBlock Text="{Binding Title}" Margin="12,100,0,28"
                               FontSize="50"/>
                </Grid>
```
11.13.用元素值绑定
用元素值绑定就是将某一个控件元素作为绑定的数据源,绑定的对象是控件元素,而绑定的数据源同时也是控件元素,这种绑定的方式,可以轻松实现两个控件之间的值的交互影响,用元素值进行绑定是通过设置Binding的ElementName属性和Path属性来实现的,ElementName属性赋值为数据源控件的Name的值,Path属性赋值则赋值为数据源控件的某个属性,这个属性就是数据源控件的一个数据变化的反映
2.在C#中可以通过类似语法指定属性的子属性,例如,字句Path=ShoppingCart.Order设置与对象或属性ShoppingCart的Order属性的绑定,也就是说ShoppingCart是绑定数据源的属性,而Order则是ShoppingCart的属性,相当于是数据源的属性的属性.
3.若要绑定到附加属性,应在附加属性周围放置圆括号,例如,若要绑定到附加属性Grid.Row,则语法是Path=(Grid.Row).
4.可以使用数组的索引器来实现数据的绑定.例如,字句Path=ShoppongCart[0]将绑定设置为与数组属性的内部对应的索引的数值.
5.可以在Path字句中混合索引器和子属性,例如,Path=ShoppingCart.ShippingInfo[MailingAddress.Street].
6.在索引器内部,可以由多个由逗号分隔的索引器参数.可以使用圆括号指定每个参数的类型.Path="[(sys:Int32)42,(sys:Int32)24]",其中sys映射到System命名空间.
7.如果源为集合视图,则可以用斜杠"/"指定当前项,例如,字句Path=/用于设置到视图中当前项的绑定,如果源为集合,则此语法指定默认集合视图的当前项.
8.可以结合使用属性名和斜杠来遍历作为集合的属性,例如,Path=/Offices/ManagerName指定源集合的当前项,该源集合包含作为集合的Offices属性,其当前项是一个包含ManagerName属性的对象.
9.也可以使用句点"."路径绑定到当前源.例如,Text="{Binding}"等效于Text="{Binding Path=.}"
下面给出控制圆的半径的示例,圆形的半径绑定到Slider控件的值,从而实现通过即时改变Slider控件的值来改变Slider控件的值来改变圆的大小.
``` bash
<StackPanel Margin="12,0,12,0">
                    <TextBlock FontSize="25" Text="圆形的半径会根据slider控件的值而改变"/>
                    <Slider Name="slider" Value="50" Maximum="400"/>
                    <TextBlock FontSize="25" Text="半径为:"/>
                    <TextBlock Name="txtblk" Text="{Binding ElementName=slider,Path=Value}" FontSize="48"/>
                    <Ellipse Height="{Binding ElementName=slider,Path=Value}" Width="{Binding ElementName=slider,Path=Value}" Fill="Red" Stroke="Black" StrokeThickness="1"/>
                </StackPanel>
            </PivotItem>
``
11.14.三种绑定模式
1.OneTime
OneTime表示一次绑定,在绑定创建时使用数据源更新目标,适用于只显示数据而不进行数据的更新.OneTime绑定会导致源属性初始化目标属性,但不传播后续更改.
2.OneWay
OneWay表示单向绑定,在绑定创建时或源数据发生变化时更新到目标,适用于显示变化的数据,这是默认模式.OneWay绑定导致对源属性的更改会自动更新目标属性,但是对目标属性的更改不会传播回源属性.此绑定类型适用于绑定的控件为隐式只读控件的情况,如果无需监视目标属性的更改,则使用OneWay的绑定模式可避免TwoWay绑定模式的系统开销
3.TwoWay
TwoWay表示双向绑定,可以同时更新源数据和目标数据,TwoWay绑定导致对源属性的更改会自动更新目标属性,而对目标属性的更改也会自动更新源属性.此绑定类型适用于输入框或其他完全交互式UI方案.大多数UI元素内部实现的属性都默认为OneWay绑定,但是一些依赖项属性,通常为用户可编辑的控件的属性,如TextBox的Text属性和CheckBox的IsChecked属性,默认为TwoWay绑定.
下面演示三种绑定模式的区别
``` bash
<StackPanel Margin="12,0,12,0">
                    <Slider x:Name="slider" Value="50" Maximum="400"/>
                    <!--OneTime-->
                    <TextBlock FontSize="25" Height="41" Text="OneTime" VerticalAlignment="Top" Width="112"/>
                    <TextBox Height="72" Text="{Binding ElementName=slider,Path=Value,Mode=OneTime}" Width="269"/>
                    <TextBlock FontSize="25" Height="46" Text="OneWay" VerticalAlignment="Top" Width="99"/>
                    <!--OneWay-->
                    <TextBox Height="72" Text="{Binding ElementName=slider,Path=Value,Mode=OneWay}" Width="269"/>
                    <TextBlock Height="40" FontSize="25" Text="TwoWay" VerticalAlignment="Top" Width="94"/>
                    <!--TwoWay-->
                    <TextBox Height="72" Text="{Binding ElementName=slider,Path=Value,Mode=TwoWay}" Width="268"/>
                    <TextBlock FontSize="25" Height="43" Text="slider的值:"/>
                    <TextBlock FontSize="25" Height="43" Text="{Binding ElementName=slider,Path=Value}" Width="185"/>
                    
                </StackPanel>
```
11.15.更改通知
``` bash
public class MyData : INotifyPropertyChanged
    {
        private string title;

        public string Title { get => title;
            set { title = value;
                OnPropertyChanged("Title");
            }
        }

        private void OnPropertyChanged(string name)
        {
            PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(name));
        }

        public event PropertyChangedEventHandler PropertyChanged;
    }
  private void Button_Click_19(object sender, RoutedEventArgs e)
        {
            myData.Title = "New Title";
        }
```
11.16.绑定数据转换
绑定数据转换需要通过Binding.Converter属性表示获取或设置转换器对象.当数据在源和目标之间传递时,绑定引擎调用该对象来修改数据.可以对任何的绑定设置一个转换器,通过创建一个类和实现IValueConverter接口来针对每个具体的应用场景自定义该转换器.所以转换器是派生自IValueConverter接口的类.它包含两种方法:Convert和ConvertBack方法.如果为绑定定义了Converter参数,则绑定引擎会调用Convert和ConvertBack方法.从源传递数据时,绑定引擎调用Convert并将返回的数据传递给目标,从目标传递数据时,绑定引擎调用ConvertBack并将返回的数据传递给源,如果只是要获取从数据源到绑定目标的单向绑定,只需要实现Convert方法.实现了转换器的逻辑之后,若要在绑定中使用转换器,首先要创建转换器类的实例.将转换器类的实例设置为程序中资源的XAML.
``` bash
    public class Clock : INotifyPropertyChanged
    {
        int hour, minute, second;
      

        public Clock()
        {
            //h获取当前的时间
            Tmr_Tick(null, null);
            //使用定时器来触发时间来改变类的时分秒属性
            //每0.1秒获取一次当前的时间
            DispatcherTimer tmr = new DispatcherTimer();
            tmr.Interval = TimeSpan.FromSeconds(0.1);
            tmr.Tick += Tmr_Tick;
            tmr.Start();
        }



        public int Hour
        {
            protected set
            {
                if (value != hour)
                {
                    hour = value;
                    PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Hour"));
                }
            }
            get
            {
                return hour;
            }
        }
        //分钟属性
        public int Minute
        {
            get => minute;
            set
            {
                if (minute != value)
                {
                    minute = value;
                    PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Minute"));
                }
            }

        }
        public int Second { get => second;
            set {
                if (second != value)
                {
                    second = value;
                    PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Second"));
                }
            }

        }
        /// <summary>
        /// 时间触发器
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        /// <exception cref="NotImplementedException"></exception>
        private void Tmr_Tick(object sender, object e)
        {
            DateTime dt=DateTime.Now;
            Hour= dt.Hour;
            Minute= dt.Minute;
            Second= dt.Second;
        }

        /// <summary>
        /// 属性值改变事件
        /// </summary>
        public event PropertyChangedEventHandler PropertyChanged;

    }
  public class HoursToDayStringConverter:IValueConverter
    {
        public object Convert(object value, Type targetType, object parameter, string language)
        {
            if (int.Parse(value.ToString())<12)
            {
                return "Dear Master,Good Morning";
            }
            else if (int.Parse(value.ToString())>12)
            {
                return "Dear Master,Good Afternoon";
            }
            else
            {
                return "Dear Master,Good ";
            }
        }

        public object ConvertBack(object value, Type targetType, object parameter, string language)
        {
           return DateTime.Now.Hour;
        }
    }
<Grid>
                    <StackPanel Margin="12,0,12,0" DataContext="{StaticResource clock}">
                        <TextBlock FontSize="30" Text="{Binding Hour,Converter={StaticResource booleanToDayString}}"/>
                        <TextBlock FontSize="30" Text="现在的时间是:"/>
                        <TextBlock FontSize="20" Text="{Binding Hour}"/>
                        <TextBlock FontSize="20" Text="小时"/>
                        <TextBlock FontSize="20" Text="{Binding Minute}"/>
                        <TextBlock FontSize="20" Text="分钟"/>
                        <TextBlock FontSize="20" Text="{Binding Second}"/>
                        <TextBlock FontSize="20" Text="秒"/>
                    </StackPanel>
                </Grid>
 <models:Clock x:Key="clock"/>
        <converters:HoursToDayStringConverter x:Key="booleanToDayString"/>
```
11.2.绑定集合
绑定目标:ItemsControl对象(ListView等列表控件)ItemsSource属性.
绑定对象:OneWay
数据源:集合对象 值
若要将列表控件绑定到集合对象,应使用列表控件自带的ItemsSource属性,而不是DataContext属性.
可以将ItemSource属性视为列表控件的内容,为列表项提供数据,还需要注意的是,绑定是OneWay模式,因为ItemSource属性默认情况下支持OneWay绑定.
11.21.数据集合
在实现列表绑定集合之前,必须要选择使用一个数据集合类或自定义实现一个数据集合类才能实例化出数据源的集合对象,不同的数据集合所实现的绑定的效果是有差异的.
1.ObservaleCollection<T>集合.
ObservableCollection<T>类是实现了INotifyCollectionChanged接口的数据集合类,使用ObservableCollection<T>类的实例与列表控件及进行绑定可以动态地往数据源地集合对象增加或删除数据,并且可以把这种变更通知到UI上,这就是ObservableCollection<T>类最大地特点.
2.其他的实现了IEnumerable接口的集合,如List<T>,Collection<T>等.
凡是实现了IEnumerable接口的集合都可以作为列表绑定的数据集合,给列表的ItemSource属性赋值,但是没有实现INotifyCollectionChanged接口的集合就无法设置动态绑定,所以List<T>,Collection<T>等集合适合于绑定静态数据,也就是绑定了列表控件之后就不需要再对项目进行插入和删除操作.
3.自定义实现集合.
当win10内置集合类无法满足需求时,可以通过自定义集合来封装数据绑定的集合的逻辑,自定义集合类就需要根据所需的功能去实现IEnumerable,INotifyCollectionChanged等相关的接口.最常用的实现方案是使用IList接口,因为它提供可以按索引逐个访问的对象的非泛型集合,因而可提供最佳性能.
11.22.绑定列表控件
``` bash
 public class Food
    {
        public string Name { get; set; }
        public string Description { get; set; }
        public string IconUri { get; set; }
        public string Type { get; set; }

    }
 public List<Food> AllFood { get; set; }
        public void AddFruits() {
            AllFood = new List<Food>();
            Food item0 = new Food() { Name = "Tomato", IconUri = "Images/Tomato.png", Type = "Healthy", Description = "Tomato tastes delicious. " };
            Food item1 = new Food() { Name = "Beer", IconUri = "Images/Beer.png", Type = "NotDetermined", Description = "Dont konw Beer is Delicious." };
            Food item2 = new Food() { Name = "fries", IconUri = "Images/fries.png", Type = "Unhealthy", Description = "This is Unhealthy food." };
            AllFood.Add(item0);
            AllFood.Add(item1);
            AllFood.Add(item2);
            listviewItemtemplate.ItemsSource = AllFood;
        }
 <ListView x:Name="listviewItemtemplate">
                    <ListView.ItemTemplate>
                        <!--列表的ItemTemplate属性是一个DataTemplate类型-->
                        <!--创建一个DataTemplate的元素对象-->
                        <DataTemplate>
                            <StackPanel Orientation="Horizontal" Background="Gray" Margin="10">
                                <!--绑定Food类的IconUri属性-->
                                <Image Source="{Binding IconUri}" Stretch="None"/>
                                <TextBlock Text="{Binding Name}" FontSize="40" Margin="24 0 24 0"/>
                                <TextBlock Text="{Binding Description}" FontSize="20"/>
                            </StackPanel>
                        </DataTemplate>
                    </ListView.ItemTemplate>
                </ListView>
```
11.23.绑定ObservableCollection<T>集合
在列表绑定中实现了把集合的数据通过数据模板展示到列表上,使用DataTemplate可以很灵活的去实现列表项的显示效果,但是这个列表却是一个静态的列表,也就是说列表的数据并不会增加或减少.如果要实现一个动态8绑定的列表,就需要用到ObservableCollection<T>集合作为数据的绑定源.
``` bash
 <Grid>
                    <Grid.RowDefinitions>
                        <RowDefinition Height="*"/>
                        <RowDefinition Height="*"/>
                    </Grid.RowDefinitions>
                    <StackPanel Margin="12,60,0,28">
                        <Button Content="AddItem" Click="Button_Click_20"/>
                        <Button Content="RemoveItem" Click="Button_Click_21"/>
                        
                    </StackPanel>
                    <Grid Margin="12,0,12,0" Grid.Row="1">
                        <ListView x:Name="Obser">
                            <ListView.ItemTemplate>
                                <DataTemplate>
                                    <StackPanel Orientation="Horizontal" Margin="10">
                                        <TextBlock Text="{Binding OrderId}" FontSize="30"/>
                                        <TextBlock Text="{Binding OrderName}" FontSize="30" Width="280"/>
                                    </StackPanel>
                                </DataTemplate>
                            </ListView.ItemTemplate>
                        </ListView>
                    </Grid>
                </Grid>
 ObservableCollection<OrderModel> OrderModels=new ObservableCollection<OrderModel>();
        /// <summary>
        /// Add
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void Button_Click_20(object sender, RoutedEventArgs e)
        {
            Random random= new Random();
            OrderModels.Add(new OrderModel { OrderId = random.Next(1000), OrderName = "OrderName" + random.Next(1000) });

        }

        private void Button_Click_21(object sender, RoutedEventArgs e)
        {
            if (Obser.SelectedItem != null) { 
                OrderModel orderModel= Obser.SelectedItem as OrderModel;
                if (OrderModels.Contains(orderModel))
                {
                    OrderModels.Remove(orderModel);
                }
            }
        }
  listviewItemtemplate.ItemsSource = AllFood;
            Obser.ItemsSource = OrderModels;
```
11.24.绑定自定义集合
实现自定义的集合最常用的就是从IList接口派生实现自定义集合类.
IList接口表示可按照索引单独访问的对象的非泛型集合.IList接口是从ICollection接口派生出来的,并且是所有非泛型列表的基接口,ICollection则是从IEnumerable接口派生出来的,ICollection接口是定义所有非泛型集合的大小,枚举数和同步方法,IEnumerable接口是指公开枚举数,该枚举数支持在非泛型集合上进行简单迭代.所以IList接口同时具备ICollection接口和IEnumerable接口的共性,IEnumerable只包含一个方法,GetEnumerator,返回IEnumerator,IEnumerator可以通过集合循环显示Current属性和MoveNext和Rest方法.
ICollecion接口方法和属性
Count 该属性可确定集合中的元素个数,它返回的值与Length属性相同.
IsSychoronized 该属性确定集合是否是纯种安全的,对于数组,这个属性总是返回false,对于同步访问,SyncRoot属性可以用于线程安全的访问.
CopyTo 该方法可以将数组的元素复制到现有的数组中,它类似于表态方法Array.Copy().
IList接口方法和属性
Add 该方法用于在集合中添加元素,对于数组,该方法会抛出NotSupportedException异常.
Clear 该方法会清除数组中的所有元素,值类型设置为0,引用类型设置为NULL
Contains 该方法可以确定某个元素是否在数组中,其返回值是true或false.这个方法会对数组中的所有元素进行线性搜索,直到找到所需元素为止.
IndexOf 该方法与Contains方法类似,也是对数组中的所有元素进行线性搜索,不同的是IndexOf方法会返回所找到的第一个元素的索引.
Insert,Remove RemoveAt 对于集合,Insert方法用于插入元素,Remove和RemoveAt可删除元素.对于数组,这些方法都抛出NotSupportedException异常.
IsFixedSize 数组的大小总是固定的,所以这个属性问题返回true.
IsReadOnly 数组总是可读/写的,所以这个属性返回false.
Item(表现显示为this[int index])该属性可以用整形索引访问数组.
只要实现了IList接口的集合类都可以与列表控件进行绑定,所以需要自定义一个集合类来实现IList接口,因为IList接口是从ICollection接口和IEnumerable接口派生出来的,所以这三个接口的方法都需要在自定义的集合类里面实现.在ICollection接口里面,Count属性表示列表的长度,IList接口的IList.this[int index]属性表示列表某个索引的数据项,在自定义集合里面可以通过Count属性设置列表的长度,通过IList.this[int index]属性返回集合数据,所以,在自定义集合里面IList.this[int index]属性和Count属性是两个必须要实现的属性接口,
可以在IList.this[int index]属性的实现里面处理集合项生成的逻辑.
下面给出自定义集合并与列表控件实现数据绑定.
``` bash
public class MyList : IList
    {
        #region MyRegion

       /// <summary>
       /// 在这个属性里面,index表示集合项的索引,通过索引返回该索引对应的对象
       /// </summary>
       /// <param name="index"></param>
       /// <returns></returns>
       /// <exception cref="NotImplementedException"></exception>
        public object this[int index] {
            get { 
            //随机生成一个OrderModel对象并返回
            Random random=new Random();
                return new OrderModel { OrderId = random.Next(1000), OrderName = "OrderName" + random.Next(1000) };
            } 
            set => throw new NotImplementedException(); }
        #endregion
        public bool IsFixedSize => throw new NotImplementedException();

        public bool IsReadOnly => throw new NotImplementedException();

        public int Count => 10;

        public bool IsSynchronized => throw new NotImplementedException();

        public object SyncRoot => throw new NotImplementedException();

        public int Add(object value)
        {
            throw new NotImplementedException();
        }

        public void Clear()
        {
            throw new NotImplementedException();
        }

        public bool Contains(object value)
        {
            throw new NotImplementedException();
        }

        public void CopyTo(Array array, int index)
        {
            throw new NotImplementedException();
        }

        public IEnumerator GetEnumerator()
        {
            throw new NotImplementedException();
        }

        public int IndexOf(object value)
        {
            throw new NotImplementedException();
        }

        public void Insert(int index, object value)
        {
            throw new NotImplementedException();
        }

        public void Remove(object value)
        {
            throw new NotImplementedException();
        }

        public void RemoveAt(int index)
        {
            throw new NotImplementedException();
        }
    }
<ListView x:Name="Ilist">
                    <ListView.ItemTemplate>
                        <DataTemplate>
                            <StackPanel Orientation="Horizontal" Margin="10">
                                <TextBlock Text="{Binding OrderId}" FontSize="30"/>
                                <TextBlock Text="{Binding OrderName}" FontSize="30" Width="280"/>
                            </StackPanel>
                        </DataTemplate>
                    </ListView.ItemTemplate>
                </ListView>
 Ilist.ItemsSource =new MyList();
```
12.列表编程
12.1.列表控件的使用
在Win10中相关的列表控件有ItemsControl控件,ListBox控件,ListView控件,GridView控件和SemanticZoom控件则是更高级的列表控件.最简单展示数据:ItemsControl,实现列表的选择等功能:ListView,实现网格布局:GridView,如果要实现分组索引的列表,可以选用SemanticZoom控件.
12.11.ItemsControl
``` bash
 <ItemsControl x:Name="itemsControl2">
                    <ItemsControl.ItemTemplate>
                        <DataTemplate>
                            <StackPanel Orientation="Horizontal">
                                <TextBlock Text="{Binding FirstName}" FontSize="30"/>
                                <TextBlock Text="{Binding LastName}" FontSize="30" Margin="30,0,0,0"/>
                                
                            </StackPanel>
                        </DataTemplate>
                    </ItemsControl.ItemTemplate>
                </ItemsControl>
 //创建一个有100个数据项的集合绑定到列表
            List<Item> items= new List<Item>();
            for (int i = 0; i < 100; i++)
            {
                items.Add(new Item { FirstName="Li"+i,LastName="Lei"+i});
            }
            itemsControl2.ItemsSource = items;
```
2.让ItemsControl控件滚动起来
ItemsControl控件内置的模板是不支持滚动的,如果要让ItemsControl的数据滚动起来,需要自定义ItemsControl的控件模板,把ItemsControl控件的数据项面板放在ScrollViewer控件上,就可以让列表的数据滚动起来了.
``` bash
<ItemsControl x:Name="ItemsControl3">
                    <ItemsControl.Template>
                        <ControlTemplate TargetType="ItemsControl">
                            <ScrollViewer>
                                <ItemsPresenter/>
                            </ScrollViewer>
                        </ControlTemplate>
                    </ItemsControl.Template>
                    <ItemsControl.ItemTemplate>
                        <DataTemplate>
                            <StackPanel Orientation="Horizontal">
                                <TextBlock Text="{Binding FirstName}" FontSize="30"/>
                                <TextBlock Text="{Binding LastName}"
                                           FontSize="30" Margin="30,0,0,0"/>

                            </StackPanel>
                        </DataTemplate>
                    </ItemsControl.ItemTemplate>
                </ItemsControl>
```
3.大数据量数据的绑定
ItemsControl控件初始化2000个数据项的时候耗费了非常多的时间.ItemsControl控件本身是不支持数据的虚拟化的,
还需要另外的布局处理.
12.12.ListBox实现下拉点击刷新列表
ListBox控件是在ItemsControl控件的基础上进行封装的,把列表项选择,虚拟化等功能集成在一起.
下面用ListBox实现一个下拉点击刷新列表的交互效果.通常当一个列表包含很多数据,可以采用这种方式来分布加载列表的数据,特别是当列表的数据从网络请求时,这种分步加载的方式优势就更加明显了.
1.点击按钮放哪里 通过修改ListBox控件的ControlTemplate,把Button控件放在ScrollViewer里面,然后按钮就可以跟随列表滚动,点击按钮发出刷新的逻辑.第二个问题解决方式是使用ObservableCollection<T>集合类型来存储集合的数据,
跟列表进行绑定,ObservableCollection<T>表示一个动态数据集合,在添加项,移除项或刷新整个列表时,此集合将向列表提供通知从而可以刷新列表.
``` bash
 <ListBox ItemsSource="{Binding Items}" SelectionChanged="ListBox_SelectionChanged">
                    <ListBox.Template>
                        <ControlTemplate TargetType="ItemsControl">
                            <ScrollViewer>
                                <StackPanel>
                                    <ItemsPresenter/>
                                    <Button Content="加载更多" Click="Button_Click_22" HorizontalAlignment="Center" Margin="12"/>
                                    
                                </StackPanel>
                            </ScrollViewer>
                        </ControlTemplate>
                    </ListBox.Template>
                    <ListBox.ItemTemplate>
                        <DataTemplate>
                            <StackPanel Orientation="Horizontal">
                                <TextBlock Text="{Binding FirstName}" FontSize="30"/>
                                <TextBlock Text="{Binding LastName}" FontSize="30" Margin="30,0,0,0"/>
                                
                            </StackPanel>
                        </DataTemplate>
                    </ListBox.ItemTemplate>
                </ListBox>
 public ObservableCollection<Item> Items { get; set; }
 Items = new ObservableCollection<Item>();
            for (int i = 0; i < 5; i++)
            {
                Items.Add(new Item { FirstName="Li"+i,LastName="Lei"+i});
            }
            this.DataContext = this;
 /// <summary>
        /// 按钮单击事件,加载更多的数据集合
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void Button_Click_22(object sender, RoutedEventArgs e)
        {
            //往原来列表的集合,继续添加5个数据项,这同时触发列表UI发生变化
            int count=Items.Count;
            for (int i = count; i < count+5; i++)
            {
                Items.Add(new Item { FirstName="Li"+i,LastName="Lei"+i});
            }
        }
        /// <summary>
        /// 选中事件的处理程序
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private async void ListBox_SelectionChanged(object sender, SelectionChangedEventArgs e)
        {
            string selectedInfo = "";
            //获取选中的项目
            foreach (var item in e.AddedItems)
            {
                selectedInfo += (item as Item).FirstName + (item as Item).LastName;
            }
            await new MessageDialog(selectedInfo).ShowAsync();
        }
```
12.13.ListView实现下拉自动刷新列表
ListView比ListBox功能更加强大,在列表的外观上,ListView控件可以直接通过ContainerContentChanging事件来监控到相关的列表数据加载的情况,也就是说,可以通过ContainerContentChanging事件间接的获取到列表数据虚拟化的运行情况.
前面的章节介绍过一个使用ListBox控件判断列表滚动到底的例子.实现的原理是通过可视化树获取ListBox的ScrollViewer控件,然后根据ScrollViewer控件的垂直位移属性来判断ListBox控件什么时候滚动到底.在ListView控件里面,可以使用一种更加智能的方式来实现下拉刷新.这个例子是通过ListView控件的ContainerContentChanging事件去控制自动刷新的逻辑,因为ListView控件是对数据进行虚拟化处理的,当列表向下滚动的时候下面的数据就会不断地被实例化,当数据实例化的时候就会触发ContainerContentChanging事件.所以只需要监控当列表最后一个数据实例化的时候就可以发出数据刷新的逻辑就可以了.
``` bash
<ListView x:Name="listView" ItemsSource="{Binding Items}">
                    <ListView.ItemTemplate>
                        <DataTemplate>
                            <StackPanel Orientation="Horizontal">
                                <TextBlock Text="{Binding FirstName}" FontSize="30"/>
                                <TextBlock Text="{Binding LastName}"
                                           FontSize="30" Margin="30,0,0,0"/>
                                
                            </StackPanel>
                        </DataTemplate>
                    </ListView.ItemTemplate>
                </ListView>
 Items = new ObservableCollection<Item>();
            for (int i = 0; i < 100; i++)
            {
                Items.Add(new Item { FirstName="Li"+i,LastName="Lei"+i});
            }
            this.DataContext = this;
            //订阅列表的ContainerContentChanging事件
            listView.ContainerContentChanging += ListView_ContainerContentChanging;
 /// <summary>
        /// 在这里判断刷新的时机
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="args"></param>
        /// <exception cref="NotImplementedException"></exception>
        private void ListView_ContainerContentChanging(ListViewBase sender, ContainerContentChangingEventArgs args)
        {
            //因为该事件会被多个线程进入,所以添加线程锁,控制下面的代码只能单个线程去执行
            lock (o)
            {
                if (!isLoading)
                {
                    if (args.ItemIndex==listView.Items.Count-1)
                    {
                        //设置IsLoading为true,在加载数据的过程中,禁止多次进入
                        isLoading= true;
                        //模拟后台耗时任务拉取数据的场景
                        Task.Factory.StartNew(async () =>
                        {
                            await Task.Delay(3000);
                            //调用UI线程添加数据
                            await this.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
                            {
                                int count = Items.Count;
                                for (int i = count; i < count + 50; i++)
                                {
                                    Items.Add(new Item { FirstName = "Li" + i, LastName = "Lei" + i });
                                }
                                //修改加载的状态
                                isLoading = false;
                            });
                        });
                    }
                }
            }
        }

        //线程锁的对象
        private object o = new object();
        //数据加载的标识
        public bool isLoading = false;
        //绑定的数据集合
        public ObservableCollection<Item> Items { get; set; }
```
12.14.GridView实现网格列表
网格列表是指列表按照网格布局的方式进行布局
GridView控件的使用方式和ListView控件是一样的,通过ItemTemplate来设置列表项目的模板,不过在GridView控件设置ItemTemplate模板的时候要注意设置它的高度和宽度,GridView控件的布局就会按照Item实际的大小进行布局,可能导致网格布局的错乱.
``` bash
 <GridView x:Name="gridView">
                    <GridView.ItemTemplate>
                        <DataTemplate>
                            <StackPanel>
                                <TextBlock Text="{Binding FirstName}" 
                                           Width="80"/>
                                <TextBlock Text="{Binding LastName}" 
                                           Width="80"/>
                            </StackPanel>
                        </DataTemplate>
                    </GridView.ItemTemplate>

                    <GridView.ItemContainerStyle>
                        <Style TargetType="GridViewItem">
                            <Setter Property="BorderBrush" Value="Gray"/>
                            <Setter Property="BorderThickness" Value="1"/>
                            <Setter Property="HorizontalContentAlignment" Value="Center"/>
                            <Setter Property="VerticalContentAlignment" Value="Center"/>
                        </Style>
                    </GridView.ItemContainerStyle>
                </GridView>
 Items = new ObservableCollection<Item>();
            for (int i = 0; i < 100; i++)
            {
                Items.Add(new Item { FirstName="Li"+i,LastName="Lei"+i});
            }
            this.DataContext = this;
gridView.ItemsSource= Items;
```
12.15.SemanticZoom实现分组列表
实现分组,同时会提供两个具有相同内容的不同视图,其中一个是主视图,另一个视图是可以让用户进行快速导航的分组视图.
SemanticZoom控件支持对GridView和ListView控件的视图效果进行缩放,在SemanticZoom中包含两个列表控件(GridView或ListView):一个控件提供放大视图,另外一个提供缩小视图,放大视图提供一个详细信息视图(ZoomedInView)以让用户查看详细信息,缩小视图提供一个缩小索引视图(ZoomedOutView)让用户快速定位要查看信息的大致范围或者分组
1.SemanticZoom控件的样式设置
SemanticZoom控件实现分组列表会比实现非分组列表复杂一些,实现分组列表还需要设置两大属性的内容:
ZoomedOutView内容和ZoomedInView内容.这两个属性内容含义如下所示
``` bash
<SemanticZoom.ZoomedInView>
	在这里放置GridView(或ListView)以表示放大视图,显示详细信息
</SemanticZoom.ZoomedInView>
<SemanticZoom.ZoomOutView>
在这里放置GridView(或ListView)以表示缩小视图,一般情况下绑定Group.Title
</SemanticZoom.ZoomOutView>
```
在赋值给ZoomInView属性的列表控件里面,一般需要设置ItemTemplate模板和GroupStyle.HeaderTemplate模板.ItemTemplate模板要设置的内容就是列表详细信息所展示的内容.G.H
模板是指分组的组头模板,如在人脉里面"a","b"这些就是属于列表的组头,如果同样是一个列表的集合,也是通过模板的绑定形式来进行定义.
在赋值给ZoomedOutView属性的列表控件里面,也需要设置其ItemTemplate模板,ZoomedOutView里面的ItemTemplate模板和ZoomedInView里面的模板的作用是不一样的,这里的ItemTemplate模板是指当点击组头的时候弹出的组头的索引面板项目展示,如点击人脉的a,b就会弹出一个字母的现实面板,当你点击某个字母的时候就会重新回到列表的界面并跳到列表字母所属的组项目的位置.同时还可以使用ItemsPanel来设置列表的布局,使用ItemContainerStyle来设置列表项目的容器样式,这些功能的使用和单独的GridView(或ListView)列表的使用是一样的.
2.SemanticZoom控件的数据源创建
SemanticZoom控件的数据源创建需要用到Windows.UI.Xaml.Data命名空间下的CollectionViewSource.
CollectionViewSource是专为数据绑定UI视图互动而设置的,尤其是对于要实现分组的情况,更需要他.创建一个CollectionViewSource对象既可以使用Xaml的方式来进行,也可以使用C#代码来创建,在CollectionViewSource对象中通常需要设置下面几个重要的属性:
1.Source属性:设置分组后的数据源,赋值给Source属性的对象是列表嵌套列表的集合对象.
2.IsSourceGrouped属性:指示是否允许分组
3.ItemsPath属性:是分组后,组内部所包含列表的属性路径.
4.View属性:获取当前与CollectionViewSource实例关联的视图对象.
5.View.CollectionGroups属性:返回该视图关联的所有集合组.
在绑定数据时,需要把ZoomedInView里面的列表控件的ItemsSource绑定到CollectionViewSource对象的View属性,用于展示CollectionViewSource对象所关联的视图.把ZoomedOutView里面的列表控件的ItemSource绑定到CollectionViewSource对象的View.CollectionGroups属性,用于展示分组的视图.
``` bash
<Grid Margin="12,0,12,0">
                    <Grid.Resources>
                        <!--创建数据源对象,注意ItemContent属性就是数据源中真正的基础数据的列表属性,必须设置该属性的值数据源才能定位到实际绑定的数据实体对象-->
                        <CollectionViewSource x:Name="itemcollectSource" IsSourceGrouped="True" ItemsPath="ItemContent"/>
                    </Grid.Resources>
                    <SemanticZoom x:Name="semanticZoom">
                        <SemanticZoom.ZoomedInView>
                            <!--在这里放置GridView(或ListView)以表示放大视图-->
                            <ListView x:Name="inView">
                                <ListView.GroupStyle>
                                    <GroupStyle>
                                        <!--用于显示列表有的数据项的模板-->
                                        <GroupStyle.HeaderTemplate>
                                            <DataTemplate>
                                                <Border Background="Red" Height="80">
                                                    <TextBlock Text="{Binding Key}" FontSize="50"/>
                                                </Border>
                                            </DataTemplate>
                                        </GroupStyle.HeaderTemplate>
                                    </GroupStyle>
                                </ListView.GroupStyle>
                                <!--用于显示列表的数据项的模板-->
                                <ListView.ItemTemplate>
                                    <DataTemplate>
                                        <StackPanel>
                                            <TextBlock Text="{Binding Title}" Height="40" FontSize="30"/>
                                        </StackPanel>
                                    </DataTemplate>
                                </ListView.ItemTemplate>
                            </ListView>
                        </SemanticZoom.ZoomedInView>
                        <SemanticZoom.ZoomedOutView>
                            <!--在这里放置GridView(或ListView)以表示缩小视图-->
                            <GridView x:Name="outView">
                                <!--用于显示弹出的分组列表视图的数据项的模板-->
                                <GridView.ItemTemplate>
                                    <DataTemplate>
                                        <Border Height="60">
                                            <TextBlock Text="{Binding Group.Key}" FontSize="24"/>
                                        </Border>
                                    </DataTemplate>
                                </GridView.ItemTemplate>
<!--列表布局模板-->
                                <GridView.ItemsPanel>
                                    <ItemsPanelTemplate>
                                        <WrapGrid ItemWidth="100" ItemHeight="75" MaximumRowsOrColumns="1" VerticalChildrenAlignment="Center"/>
                                    </ItemsPanelTemplate>
                                </GridView.ItemsPanel>
                                <!--列表项目容器的样式设置-->
                                <GridView.ItemContainerStyle>
                                    <Style TargetType="GridViewItem">
                                        <Setter Property="BorderBrush" Value="Gray"/>
                                        <Setter Property="Background" Value="Red"/>
                                        <Setter Property="BorderThickness" Value="3"/>
                                        <Setter Property="HorizontalAlignment" Value="Center"/>
                                        <Setter Property="VerticalAlignment" Value="Center"/>
                                    </Style>
                                </GridView.ItemContainerStyle>
                            </GridView>
                        </SemanticZoom.ZoomedOutView>
                    </SemanticZoom>
                </Grid>
            </PivotItem>
 public void GetSezData() { 
            //先创建一个普通的数据集合
            List<BookItem> mainItem=new List<BookItem>();
            for (int i = 0; i < 10; i++)
            {
                mainItem.Add(new BookItem { Content = "A类别", Title = "TestA" + i });
                mainItem.Add(new BookItem { Content = "B类别", Title = "TestB" + i });
                mainItem.Add(new BookItem { Content = "C类别", Title = "TestC" + i });
            }
            //使用LINQ语法把普通的数据集合转换为分组的数据集合.
            List<ItemInGroup> Items=(from item in mainItem group item by item.Content into newItems select new ItemInGroup { Key=newItems.Key,ItemContent=newItems.ToList()}).ToList();
            //设置CollectionViewSource对象的数据源
            this.itemcollectSource.Source= Items;
            //分别对两个视图进行绑定
            outView.ItemsSource = itemcollectSource.View.CollectionGroups;
            inView.ItemsSource = itemcollectSource.View;
        }
 internal class ItemInGroup
    {
        public string Key { get; set; }
        public List<BookItem> ItemContent { get; set; }
    }
```
12.2.虚拟化技术
允许应用程序只把在屏幕当前和屏幕附近的UI元素初始化了,其他UI元素都是处于虚构的状态,还可以利用虚拟化技术来做更多的优化,如当虚拟化发生时,可以去主动的回收暂时不使用的内存,从而可以对程序暂用的内存进行优化.也可以利用虚拟化的布局控件去实现自定义的虚拟化的功能需求.
12.21.列表的虚拟化
标准布局系统可以创建项容器并为每个与列表控件关联的项计算布局.在自定义集合里面可以通过Count属性设置列表的长度,通过IList.this[int index]属性返回数据项和打印出相关的数据信息.
``` bash
 public class VirtualDataList : IList
    {
        public object this[int index] { get {
                //当获取集合的某个数据项的时候把这个数据的索引项打印出来
                Debug.WriteLine("当前加载的数据Data"+index.ToString());
                return new Data { Name = "data" + index.ToString() };
            } set => throw new NotImplementedException(); }

        public bool IsFixedSize => throw new NotImplementedException();

        public bool IsReadOnly => throw new NotImplementedException();

        public int Count => 1000;

        public bool IsSynchronized => throw new NotImplementedException();

        public object SyncRoot => throw new NotImplementedException();

        public int Add(object value)
        {
            throw new NotImplementedException();
        }

        public void Clear()
        {
            throw new NotImplementedException();
        }

        public bool Contains(object value)
        {
            throw new NotImplementedException();
        }

        public void CopyTo(Array array, int index)
        {
            throw new NotImplementedException();
        }

        public IEnumerator GetEnumerator()
        {
            throw new NotImplementedException();
        }

        public int IndexOf(object value)
        {
            throw new NotImplementedException();
        }

        public void Insert(int index, object value)
        {
            throw new NotImplementedException();
        }

        public void Remove(object value)
        {
            throw new NotImplementedException();
        }

        public void RemoveAt(int index)
        {
            throw new NotImplementedException();
        }
    }
 <ListView ItemsSource="{Binding Data}">
                    <ListView.ItemTemplate>
                        <DataTemplate>
                            <StackPanel>
                                <TextBlock Text="{Binding Name}" Height="50"/>
                            </StackPanel>
                        </DataTemplate>
                    </ListView.ItemTemplate>
                </ListView>
```
12.22.VirtualizingStackPanel,ItemsStackPanel和ItemsWrapGrid虚拟化排列布局控件
都是虚拟化布局控件,一般情况下在界面的布局上很少会用到这些虚拟化排列的控件,大部分都是封装在列表的布局面板上使用,主要目的就是为了实现列表上大数据量的虚拟化,从而极大的提高列表的效率.
其实这3个虚拟化布局控件都是列表控件的默认布局排列的方式,其中VirtualizingStackPanel控件是ListBox的默认布局面板,ItemsStackPanel是ListView的默认布局面板,ItemsWrapGrid是GridView的默认布局面板.
VirtualizingStackPanel控件和ItemsStackpanel控件都表示沿着水平方向或垂直方向将内容虚拟化的排列在一行上,所实现的排列布局效果和StackPanel控件是一样的,不同的是这些控件可以实现虚拟化的逻辑.对于数据较多的列表布局,使用VirtualizingStackPanel控件或ItemsStackPanel控件会比StackPanel高效很多,因为虚拟化控件只是把当前屏幕范围内的数据显示出来,其他的数据都通过虚拟化的技术进行处理,并没有进行UI的初始化显示,所以效率很高,ItemsWrapGrid控件实现的是网格的虚拟化布局效果,虚拟化原理和ItemsStackPanel控件类似,只不过排列的方式不一样.
如果使用ItemsControl列表控件来展示数据,要给这个列表增加虚拟化的功能,ItemsStackPanel对象元素必须包含在一个ItemsPanelTemplate中,给ItemsControl控件增加ItemsStackPanel虚拟化布局.
``` bash
 public class ImageList : IList
    {
        public object this[int index] { get {
                //加载的图片是程序里面的图片资源,20张图片循环加载
                int imageIndex = 20 - index % 20;
                Debug.WriteLine("加载的集合索引是:"+index);
                return new ImageItem { ImageName = "图片" + index, Image = new BitmapImage(new Uri($"ms-appx:///Assets/thumb/thumb_{imageIndex}.webp", UriKind.RelativeOrAbsolute)) };
            } set => throw new NotImplementedException(); }

        public bool IsFixedSize => throw new NotImplementedException();

        public bool IsReadOnly => throw new NotImplementedException();

        public int Count => 100;

        public bool IsSynchronized => throw new NotImplementedException();

        public object SyncRoot => throw new NotImplementedException();

        public int Add(object value)
        {
            throw new NotImplementedException();
        }

        public void Clear()
        {
            throw new NotImplementedException();
        }

        public bool Contains(object value)
        {
            throw new NotImplementedException();
        }

        public void CopyTo(Array array, int index)
        {
            throw new NotImplementedException();
        }

        public IEnumerator GetEnumerator()
        {
            throw new NotImplementedException();
        }

        public int IndexOf(object value)
        {
            throw new NotImplementedException();
        }

        public void Insert(int index, object value)
        {
            throw new NotImplementedException();
        }

        public void Remove(object value)
        {
            throw new NotImplementedException();
        }

        public void RemoveAt(int index)
        {
            throw new NotImplementedException();
        }
    }
```
2.实现ItemsControl的横向虚拟化布局.
要实现ItemsControl的横向虚拟化布局,除了使用ItemsStackPanel控件的Horizontal布局,还需要在ItemsControl中设置ScrollViewer,HorizontalScrollBarVisibility="Auto",这样列表就可以水平滚动了
``` bash
 <ItemsControl x:Name="imageList">
                    <ItemsControl.ItemsPanel>
                        <ItemsPanelTemplate>
                            <!--设置横向布局-->
                            <ItemsStackPanel Orientation="Horizontal"/>
                        </ItemsPanelTemplate>
                    </ItemsControl.ItemsPanel>
                    <ItemsControl.Template>
                        <ControlTemplate TargetType="ItemsControl">
                            <!--设置水平滚动-->
                            <ScrollViewer ScrollViewer.HorizontalScrollBarVisibility="Visible" ScrollViewer.VerticalScrollBarVisibility="Disabled">
                                <!--<StackPanel>-->
                                    <ItemsPresenter/>
                                <!--</StackPanel>-->
                            </ScrollViewer>
                        </ControlTemplate>
                    </ItemsControl.Template>
                    <ItemsControl.ItemTemplate>
                        <DataTemplate>
                           
                                <StackPanel>
                                <Image Source="{Binding Image}" Width="144" Height="240" Stretch="Uniform"/>
                                <TextBlock Text="{Binding ImageName}"/>
                            </StackPanel>
                            
                        </DataTemplate>
                    </ItemsControl.ItemTemplate>
                </ItemsControl>
 imageList.ItemsSource = new ImageList();
```
12.24.大数据量网络图片列表的异步加载和内存优化
实现网络图片列表数据集合大加载:1.图片的加载比较耗时2.不断地滑动会让数据集合加载的图片占用的内存越来越高.
1.可以采用异步加载的方式来解决,这样的列表加载完之后,图片再显示出来,列表首次加载的速度会很快,我们可以通过后台线程调用网络请求下载图片,下载完图片之后再触发UI线程把图片加载出来.
2.要解决内存的问题,可以使用弱引用类型(WeakReference类)来存储图片的数据.弱引用就是不保证不被垃圾回收期回收的对象,它拥有比较短暂的生命周期,在垃圾回收器扫描它所管辖的内存区域过程中,一旦发现了只具有弱引用的对象,就会回收它的内存.不过一般情况下,垃圾回收器的线程优先级很低,也就不会很快发现那些只有弱引用的对象.当内存的使用影响程序流畅运行时,垃圾回收器就会按照优先次序把存在时间长的弱引用对象回收,而释放内存.所以弱引用特别适合在当前这种情况下,占用大量内存,但通过垃圾回收功能回收以后很容易重新创建的图片对象.图片下载完之后会存放在弱引用对象里面,当检查到数据被回收的时候,再进行异步加载,当然也可以把图片用独立存储存起来,这样就免去了再次请求网络的操作.
``` bash
 <ListView x:Name="InternetImage">
                    <ListView.ItemTemplate>
                        <DataTemplate>
                            <StackPanel>
                                <TextBlock Text="{Binding Name}" Height="80"/>
                                <Image Source="{Binding ImageSource}" Width="200" Height="200"/>
                            </StackPanel>
                        </DataTemplate>
                    </ListView.ItemTemplate>
                </ListView>
 public void GetInternetImage() { 
            List<ImageData> Items=new List<ImageData>();
            for (int i = 0; i < 1000; i++)
            {
                //在网络地址后面加上index=i是为了保证每个网络地址不一样
                //这样就不会产生网络数据缓存,更加接近真实的网络图片列表
                Items.Add(new ImageData { Name = "Test" + i, Page = this, ImageUri = new Uri("http://pic002.cnblogs.com/images/2012/152755/2012120917494440.png?index=" + i) });
            }
            InternetImage.ItemsSource= Items;
        }
 public class ImageData : INotifyPropertyChanged
    {
        private Uri imageUri;

        public ImageData() { }
        public string Name { set; get; }
        public Page Page { set; get; }
        public Uri ImageUri
        {
            get => imageUri;
            set
            {
                if (imageUri == value)
                {
                    return;
                }
                imageUri = value;
                bitmapImage = null;
            }
        }
        //弱引用对象,用于存储下载好的图片对象
        WeakReference bitmapImage;
        private ImageSource imageSource;

        //ImageSource属性用于绑定到列表的Image控件上
        public ImageSource ImageSource {

            get {
                if (bitmapImage != null)
                {
                    //如果弱引用没有回收,则取弱引用的值
                    if (bitmapImage.IsAlive)
                    {
                        return (ImageSource)bitmapImage.Target;
                    }
                    else
                    {
                        Debug.WriteLine("数据已经被回收");
                    }
                }
                    //弱引用已经被回收,那么则通过图片网路地址进行异步下载
                    if (ImageUri!=null)
                    {
                        Task.Factory.StartNew(() => { DownloadImage(imageUri); });
                    }
                    return null;
                
            } 
            
           }
        /// <summary>
        /// 下载图片的方法
        /// </summary>
        /// <param name="imageUri"></param>
        /// <exception cref="NotImplementedException"></exception>
        private async void DownloadImage(Uri imageUri)
        {
            List<Byte> allBytes = new List<byte>();
            Stream streamForUI;
            //通过网络下载图片数据
            using (var response=await HttpWebRequest.Create(imageUri).GetResponseAsync())
            {
                using (Stream responseStream=response.GetResponseStream())
                {
                    byte[] buffer = new byte[4000];
                    int bytesRead = 0;
                    while ((bytesRead = await responseStream.ReadAsync(buffer,0,4000))>0)
                    {
                        allBytes.AddRange(buffer.Take(bytesRead));
                    }
                }
                
            }
            streamForUI = new MemoryStream((int)allBytes.Count);
            streamForUI.Write(allBytes.ToArray(),0,allBytes.Count);
            streamForUI.Seek(0, SeekOrigin.Begin);
            //触发UI线程处理位图和UI更新
            await Page.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
            {
                BitmapImage bm=new BitmapImage();
                bm.SetSource(streamForUI.AsRandomAccessStream());
                //把图片位图对象存放到弱引用对象里面
                if (bitmapImage==null)
                {
                    bitmapImage = new WeakReference(bm);
                }
                else
                {
                    bitmapImage.Target = bm;
                    //触发UI绑定属性的改变
                    PropertyChanged?.Invoke(this,new PropertyChangedEventArgs("ImageSource"));
                }
            });
        }
        /// <summary>
        /// 属性改变事件
        /// </summary>
        /// <param name="proeprty"></param>
        async void OnPropertyChanged(string proeprty) {
            var handler = PropertyChanged;
            await Page.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () => {
                if (handler!=null)
                {
                    handler(this,new PropertyChangedEventArgs(proeprty));
                }
            });
        }
        public event PropertyChangedEventHandler PropertyChanged;
    }
}
```
#### 图表编程
13.1.动态生成折线图和区域图
区域图其实就是在折线图的基础上实现了区域的显示效果,这两者非常类似.
13.11.折线图和区域图原理
折线图可以通过Polyline图形来进行创建,我们要实现的折线图就是一种特殊的Polyline图形.一般的折线图的X轴坐标都是等量递增的,Y轴的坐标是随意变化的,我们需要根据这种变化的规律来给Polyline控件库的Point属性来赋值.
``` bash
 <Polyline Stroke="LightBlue" StrokeThickness="5" Height="150" Points="0,10 50,40 100,90 150,50 200,50 250,10 300,100 350,30 400,0"/>
```
区域图的规则和折线图是类似的,只不过区域图是一个闭合的图形,相当于把折线图和X轴组合起来形成一个区域,区域图可以通过Polygon图形去实现,Polygon图形和Polyline图形的区别在于一个是闭合的而另一个是非闭合的,这也是区域图和折线图的区别.
``` bash
<Polygon Fill="AliceBlue" StrokeThickness="5" Height="150" Stroke="Red" Points="0,150 0,10 50,40 100,90 150,50 200,50 250,10 300,100 350,30 400,0 400,150"/>
```
13.12.生成图形逻辑封装
创建折线图和区域图最关键的部分就是把相关的数据集合转换为X轴和Y轴的坐标,然后根据坐标生成图形.
下面实现了一个生成折线图的PointCollection的方法
``` bash
private PointCollection GetLineChartPointCollection(List<double> datas,double topheight,double perWidth,double topValue) { 
            PointCollection pointCollection=new PointCollection();
            double x = 0;//X坐标
            foreach (var data in datas)
            {
                double y;   //Y坐标
                if (data > topValue) y = 0;
                else y = (topheight - (data * topheight) / topValue);
                Point point =new Point(x,y);
                pointCollection.Add(point);
                x += perWidth;
            }
            return pointCollection;
        }
```
该方法通过参数传递进行折线图表展示的数据集合,图表的最高高度,两个数据之间的X轴的间隔和图表数值的最大的值,j然后根据这些数据来产生一个坐标的点集合.图表数值的最大值是为了控制数据集合里面的数值相差太大而导致图表显示异常,所以做了一个最大值的控制.如果datas数据里面有比topValue大的数据,将会用topValue来代替,Y坐标的产生公式是y=(topHeight-(data*topHeight)/topValue),计算出点和顶部的距离就是Y坐标,X坐标就是有规律地递增.
定义好PointCollection的生成方法后,就可以在UI上生成折线图了,下面是UI上的代码,通过Button事件调用GetLineChartPointCollection方法生成一个折线图.
``` bash
<Grid Margin="12,0,12,0">
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="*"/>
                    </Grid.RowDefinitions>
                    <Grid x:Name="chartCanvas" Height="400" HorizontalAlignment="Center" VerticalAlignment="Center">
                        
                    </Grid>
                    <StackPanel Grid.Row="1">
                        <Button Content="折线图" Click="Button_Click_23"/>
                        <Button Content="区域图" Click="Button_Click_24"/>
                    </StackPanel>
                </Grid>
 /// <summary>
        /// 生成折线图
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void Button_Click_23(object sender, RoutedEventArgs e)
        {
            chartCanvas.Children.Clear();
            List<double> datas=new List<double>() { 23,23,45,26,45,36,29,30,27,38,36,52,27,35};
            PointCollection pointCollection = GetLineChartPointCollection(datas,400,30,100);
            Polyline polyline=new Polyline() { Points=pointCollection,Stroke=new SolidColorBrush(Colors.Red)};
            chartCanvas.Children.Add(polyline);
        }
```
生成区域图,如果直接使用Polygon图形来生成区域图,只需要在GetLineChartPointCollection方法里面添加上一个开始点在X轴的映射坐标和一个结束点在X轴的映射坐标就可以了.除了使用Polygon图形来实现,还可以用Path图形来实现,实现的效果也是一样的.
``` bash
 private PathGeometry GetLineChartPathGeometry(List<double> datas, double topHeight, double perWidth, double topValue) { 
            PathGeometry pathGeometry=new PathGeometry();
            PathFigureCollection pathFigures=new PathFigureCollection();
            //使用数据集合第一个点在X轴的投影点作为Path图形的开始点
            PathFigure pathFigure=new PathFigure() { StartPoint=new Point(0,topHeight)};
            //新建一个PathSegmentCollection集合来添加LineSegment线段的对象
            PathSegmentCollection pathSegmentCollection=new PathSegmentCollection();
            double x = 0; //x坐标
            foreach (double data in datas)
            {
                double y;//Y坐标
                if (data > topValue) y = 0;
                else y = (topHeight - (data * topHeight) / topValue);
                Point point=new Point(x,y);
                LineSegment lineSegment=new LineSegment() { Point=point};
                pathSegmentCollection.Add(lineSegment);
                x+= perWidth;
            }
            x-=perWidth;
            LineSegment lineSegmentEnd = new LineSegment() { Point = new Point(x, topHeight) };
            pathSegmentCollection.Add(lineSegmentEnd);
            pathFigure.Segments = pathSegmentCollection;
            pathFigures.Add(pathFigure);
            pathGeometry.Figures = pathFigures; return pathGeometry;
        }
/// <summary>
        /// 生成区域图
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void Button_Click_24(object sender, RoutedEventArgs e)
        {
            chartCanvas.Children.Clear();
            List<double> datas = new List<double> { 23, 23, 45, 26, 45, 36, 29, 30, 27, 38, 36, 52, 27, 35 };
            PathGeometry pathGeometry = GetLineChartPathGeometry(datas,400,30,100);
            Path path = new Path { Data = pathGeometry, Fill = new SolidColorBrush(Colors.Red) };
            chartCanvas.Children.Add(path);
        }
```
13.2.实现饼图控件
要实现一个饼图控件,首先要实现饼图片形状,有一些重要的属性,如饼图半径Radius,内圆半径InnerRadius,旋转角度RotationAngle,片形角度WedgeAngle,点innerArcStartPoint,点innerArcEndPoint,点outerArcStartPoint和outerArcEndPoint,这4个点的坐标需要通过半径和角度相关的属性计算出来,计算出这4个点的坐标之后,通过这4个点创建一个Path图形,这个Path图形由两条直线和两条弧线组成,形成了一个饼图片形形状,通过这种方式不仅把这个饼图片形形状创建好了,连这个图形在整个饼图的位置也设置好了.
``` bash
/// <summary>
    /// 自定义饼图图片形形状
    /// </summary>
    public class PiePiece:Path
    {
        #region 依赖属性

        /// <summary>
        /// 饼图半径
        /// </summary>
        public double Radius
        {
            get { return (double)GetValue(RadiusProperty); }
            set { SetValue(RadiusProperty, value); }
        }

        // Using a DependencyProperty as the backing store for Radius.  This enables animation, styling, binding, etc...
        public static readonly DependencyProperty RadiusProperty =
            DependencyProperty.Register("Radius", typeof(double), typeof(PiePiece), new PropertyMetadata(0));


       

        /// <summary>
        /// 距离饼图中心的距离
        /// </summary>
        public double PushOut
        {
            get { return (double)GetValue(PushOutProperty); }
            set { SetValue(PushOutProperty, value); }
        }

        // Using a DependencyProperty as the backing store for PushOut.  This enables animation, styling, binding, etc...
        public static readonly DependencyProperty PushOutProperty =
            DependencyProperty.Register("PushOut", typeof(double), typeof(PiePiece), new PropertyMetadata(0));


        /// <summary>
        /// 饼图内圆半径
        /// </summary>
        public double InnerRadius
        {
            get { return (double)GetValue(InnerRadiusProperty); }
            set { SetValue(InnerRadiusProperty, value); }
        }

        // Using a DependencyProperty as the backing store for InnerRadius.  This enables animation, styling, binding, etc...
        public static readonly DependencyProperty InnerRadiusProperty =
            DependencyProperty.Register("InnerRadius", typeof(double), typeof(PiePiece), new PropertyMetadata(0));


        /// <summary>
        /// 饼图片形的角度
        /// </summary>
        public double WedgeAngle
        {
            get { return (double)GetValue(WedgeAngleProperty); }
            set { SetValue(WedgeAngleProperty, value); }
        }

        // Using a DependencyProperty as the backing store for WedgeAngle.  This enables animation, styling, binding, etc...
        public static readonly DependencyProperty WedgeAngleProperty =
            DependencyProperty.Register("WedgeAngle", typeof(double), typeof(PiePiece), new PropertyMetadata(0));


        /// <summary>
        /// 旋转的角度
        /// </summary>
        public double RotationAngle
        {
            get { return (double)GetValue(RotationAngleProperty); }
            set { SetValue(RotationAngleProperty, value); }
        }

        // Using a DependencyProperty as the backing store for RotationAngle.  This enables animation, styling, binding, etc...
        public static readonly DependencyProperty RotationAngleProperty =
            DependencyProperty.Register("RotationAngle", typeof(double), typeof(PiePiece), new PropertyMetadata(0));


        /// <summary>
        /// 注册中心点的X坐标属性
        /// </summary>
        public double CentreX
        {
            get { return (double)GetValue(CentreXProperty); }
            set { SetValue(CentreXProperty, value); }
        }

        // Using a DependencyProperty as the backing store for CentreX.  This enables animation, styling, binding, etc...
        public static readonly DependencyProperty CentreXProperty =
            DependencyProperty.Register("CentreX", typeof(double), typeof(PiePiece), new PropertyMetadata(0));


        /// <summary>
        /// 中心点的Y坐标
        /// </summary>
        public double CentreY
        {
            get { return (double)GetValue(CentreYProperty); }
            set { SetValue(CentreYProperty, value); }
        }

        // Using a DependencyProperty as the backing store for CentreY.  This enables animation, styling, binding, etc...
        public static readonly DependencyProperty CentreYProperty =
            DependencyProperty.Register("CentreY", typeof(double), typeof(PiePiece), new PropertyMetadata(0));


        /// <summary>
        /// 饼图片形所占饼图的百分比
        /// </summary>
        public double Percentage
        {
            get { return (double)GetValue(PercentageProperty); }
            set { SetValue(PercentageProperty, value); }
        }

        // Using a DependencyProperty as the backing store for Percentage.  This enables animation, styling, binding, etc...
        public static readonly DependencyProperty PercentageProperty =
            DependencyProperty.Register("Percentage", typeof(double), typeof(PiePiece), new PropertyMetadata(0));



        public double PieceValue
        {
            get { return (double)GetValue(PieceValueProperty); }
            set { SetValue(PieceValueProperty, value); }
        }

        // Using a DependencyProperty as the backing store for PieceValue.  This enables animation, styling, binding, etc...
        public static readonly DependencyProperty PieceValueProperty =
            DependencyProperty.Register("PieceValue", typeof(double), typeof(PiePiece), new PropertyMetadata(0));
        private double lastWidth=0;
        private double lastHeight=0;
        private PathFigure figure;



        #endregion
        public PiePiece()
        {
            CreatePathData(0,0);
        }

        private void CreatePathData(double width, double height)
        {
            //用于退出布局的循环逻辑
            if (lastWidth == width && lastHeight == height) return;
            lastWidth = width;
            lastHeight = height;
            Point startPoint = new Point(CentreX,CentreY);
            //计算饼图片形内圆弧的开始点
            Point innerArcStartPoint = ComputeCartesianCCoordinate(RotationAngle,InnerRadius);
            //根据中心点来校正坐标的位置
            innerArcStartPoint = Offset(innerArcStartPoint,CentreX,CentreY);
            //计算饼图片形内圆弧的结束点
            Point innerArcEndPoint=ComputeCartesianCCoordinate(RotationAngle+WedgeAngle,InnerRadius);
            innerArcEndPoint = Offset(innerArcEndPoint,CentreX,CentreY);
            //计算饼图片形外圆弧的开始点
            Point outerArcStartPoint = ComputeCartesianCCoordinate(RotationAngle,Radius);
            outerArcStartPoint = Offset(outerArcStartPoint,CentreX,CentreY);
            //计算饼图片形外圆弧的结束点
            Point outerArcEndPoint=ComputeCartesianCCoordinate(RotationAngle+WedgeAngle,Radius);
            outerArcEndPoint = Offset(outerArcEndPoint,CentreX,CentreY);
            //判断饼图片形的角度是否大于180度
            bool largeArc = WedgeAngle > 180.0;
            //把扇面饼图往偏离中心点推出一部分
            if (PushOut>0)
            {
                Point offset = ComputeCartesianCCoordinate(RotationAngle+WedgeAngle/2,PushOut);

                //根据偏移量来重新设置圆弧的坐标
                innerArcStartPoint = Offset(innerArcStartPoint,offset.X,offset.Y);
                innerArcEndPoint = Offset(innerArcEndPoint,offset.X,offset.Y);
                outerArcStartPoint = Offset(outerArcStartPoint,offset.X,offset.Y);
                outerArcEndPoint = Offset(outerArcEndPoint,offset.X,offset.Y);
            }
            //外圆的大小
            Size outerArcSize = new Size(Radius,Radius);
            //内圆的大小
            Size innerArcSize = new Size(InnerRadius,InnerRadius);
            var geometry = new PathGeometry();
            figure = new PathFigure();
            //从内圆开始坐标开始画一个闭合的扇形图形
            figure.StartPoint = innerArcStartPoint;
            AddLine(outerArcStartPoint);
            AddArc(outerArcEndPoint,outerArcSize,largeArc,SweepDirection.Clockwise);
            AddLine(innerArcEndPoint);
            AddArc(innerArcStartPoint,innerArcSize,largeArc,SweepDirection.Counterclockwise);
            figure.IsClosed= true;
            geometry.Figures.Add(figure);
            this.Data= geometry;
        }
        protected override Size MeasureOverride(Size availableSize)
        {
            return availableSize; 
        }
 protected override Size ArrangeOverride(Size finalSize)
        {
            CreatePathData(finalSize.Width,finalSize.Height);
            return finalSize;
        }
        //把点进行偏移转换
        private Point Offset(Point point,double offsetX,double offsetY) {
            point.X += offsetX;
            point.Y += offsetY;
            return point;
        }
        /// <summary>
        /// 根据角度和半径来计算出圆弧上的点的坐标
        /// </summary>
        /// <param name="rotationAngle"></param>
        /// <param name="innerRadius"></param>
        /// <returns></returns>
        /// <exception cref="NotImplementedException"></exception>
        private Point ComputeCartesianCCoordinate(double angle, double radius)
        {
            //转换成弧度单位
            double angleRad = (Math.PI / 180.0) * (angle-90);
            double x=radius*Math.Cos(angleRad);
            double y=radius*Math.Sin(angleRad);
            return new Point(x, y);
        }

        /// <summary>
        /// 在图形中添加一个点
        /// </summary>
        /// <param name="x"></param>
        /// <param name="y"></param>
        private void AddPoint(double x,double y) { 
            LineSegment segment= new LineSegment();
            segment.Point= new Point(x+0.5*StrokeThickness,y+0.5*StrokeThickness);

        }
        /// <summary>
        /// 在图形中添加一条线段
        /// </summary>
        /// <param name="point"></param>
        private void AddLine(Point point) { 
            LineSegment segment= new LineSegment();
            segment.Point= point;
            figure.Segments.Add(segment);
        }
        /// <summary>
        /// 在图形中添加一个圆弧
        /// </summary>
        /// <param name="point"></param>
        /// <param name="size"></param>
        /// <param name="largeArc"></param>
        /// <param name="sweepDirection"></param>
        private void AddArc(Point point,Size size,bool largeArc,SweepDirection sweepDirection) { 
            ArcSegment segment= new ArcSegment();
            segment.Point= point;
            segment.Size= size;
            segment.IsLargeArc= largeArc;
            segment.SweepDirection= sweepDirection;
            figure.Segments.Add(segment);
        }
    }
```
13.22.封装饼图控件
创建好了PiePiece形状之后,下面开始就要开始创建利用PiePiece形状来创建饼图控件了.创建饼图控件是通过UserControl控件来实现的,UserControl控件的Xaml代码里面只有一个Grid面板,用来加载PiePiece形状来组成饼图.
在饼图控件里面需要自定义一些相关的属性,用来传递相关的参数.属性HoleSize表示饼图内圆的大小,按照比例来计算,属性pieWidth表示饼图的宽度.饼图的数据集合是通过控件数据的上下文属性DataContext属性来传递,在初始化饼图的时候需要把DataContext的数据读取出来然后再创建PiePiece图形,每个PiePiece图形都添加了Tap事件,用来实现当用户单击饼图的时候,相应的某一块回往外推出去.
```
<UserControl
    x:Class="App1.Controls.PiePlotter"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:App1.Controls"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d"
    d:DesignHeight="300"
    d:DesignWidth="400"
    FontFamily="{StaticResource PhoneFontFamilyNormal}"
    >

    <Grid x:Name="LayoutRoot">
    </Grid>
</UserControl>
using App1.Models;
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Runtime.InteropServices.WindowsRuntime;
using Windows.Foundation;
using Windows.Foundation.Collections;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Controls.Primitives;
using Windows.UI.Xaml.Data;
using Windows.UI.Xaml.Input;
using Windows.UI.Xaml.Media;
using Windows.UI.Xaml.Navigation;

//https://go.microsoft.com/fwlink/?LinkId=234236 上介绍了“用户控件”项模板

namespace App1.Controls
{
    /// <summary>
    /// 饼图控件
    /// </summary>
    public sealed partial class PiePlotter : UserControl
    {
        public PiePlotter()
        {
            this.InitializeComponent();
        }
        #region 依赖属性
        //注册内圆大小属性

        /// <summary>
        /// 内圆的大小,按比例来计算
        /// </summary>
        public double HoleSize
        {
            get { return (double)GetValue(HoleSizeProperty); }
            set { SetValue(HoleSizeProperty, value); }
        }

        // Using a DependencyProperty as the backing store for HoleSize.  This enables animation, styling, binding, etc...
        public static readonly DependencyProperty HoleSizeProperty =
            DependencyProperty.Register("HoleSize", typeof(double), typeof(PiePlotter), new PropertyMetadata(0));


        /// <summary>
        /// 饼图宽度
        /// </summary>
        public double PieWidth
        {
            get { return (double)GetValue(PieWidthProperty); }
            set { SetValue(PieWidthProperty, value); }
        }

        // Using a DependencyProperty as the backing store for PieWidth.  This enables animation, styling, binding, etc...
        public static readonly DependencyProperty PieWidthProperty =
            DependencyProperty.Register("PieWidth", typeof(double), typeof(PiePlotter), new PropertyMetadata(0));


        #endregion
        //饼图的片形PiePiece的集合
        private List<PiePiece> piePieces= new List<PiePiece>();
        //选中的当前饼图的数据项
        private PieDataItem CurrentItem;
        //初始化展示饼图的方法
        public void ShowPie() {
            //获取控件的数据上下文,转换成数据集合
            List<PieDataItem> myCollectionView = (List< PieDataItem >)this.DataContext;
            if (myCollectionView == null)
                return;
            //半径的大小
            double halfWidth = PieWidth / 2;
            //内圆半径大小
            double innerRadius = halfWidth * HoleSize;
            //计算图表数据的总和
            double total = 0;
            foreach (PieDataItem item in myCollectionView)
            {
                total += item.Value;
            }
            //通过PiePiece构建饼图
            LayoutRoot.Children.Clear();
            piePieces.Clear();
            double accumulativeAngle = 0;
            foreach (PieDataItem item in myCollectionView)
            {
                bool selectedItem = item == CurrentItem;
                double wedgeAngle = item.Value * 360 / total;
                //根据数据来创建饼图的每一块图形
                PiePiece piePiece= new PiePiece() { 
                    Radius=halfWidth,
                    InnerRadius=innerRadius,
                    CentreX=halfWidth,
                    CentreY=halfWidth,
                    PushOut=(selectedItem?10.0:0),
                    WedgeAngle=wedgeAngle,
                    PieceValue=item.Value,
                    RotationAngle=accumulativeAngle,
                    Fill=item.Brush,
                    Tag=item
                };
                //添加饼图片形的点击事件
                piePiece.Tapped += PiePiece_Tapped;
                piePieces.Add(piePiece);
                LayoutRoot.Children.Add(piePiece);
                accumulativeAngle+= wedgeAngle;
            }
        }

        private void PiePiece_Tapped(object sender, TappedRoutedEventArgs e)
        {
            PiePiece piePiece = sender as PiePiece;
            CurrentItem = piePiece.Tag as PieDataItem;
            ShowPie();
        }
    }
}
```
13.3.线性报表
一个完整的线性报表包括网格图,坐标轴,图例和线性图形这四部分.网格图是指报表背景的网格,主要目的是清晰的查看坐标点的位置.坐标轴是水平坐标体系的X轴和Y轴,图例是集中于报表一角或一侧的报表上各种符号和颜色所代表内容与指标的说明,有助于更好的认识报表.线性图形就是图表里面的主体图形.
13.31.实现图形表格和坐标轴
首先封装一个图表的基础样式的ChartStyle类,这个类主要定义了X轴,Y轴的坐标范围,网格面板的大小和把网格面板的点转换为坐标体系的点的方法.
``` bash
 /// <summary>
    /// 图表基础样式类
    /// </summary>
    public class ChartStyle
    {
        //X轴最小坐标
        private double xmin = 0;
        //X轴最大坐标
        private double xmax = 1;
        //Y轴最小坐标
        private double ymin = 0;
        //Y轴最大坐标
        private double ymax = 1;
        //网格面板
        private Canvas chartCanvas;

        public ChartStyle()
        {

        }

        public double Xmin { get => xmin; set => xmin = value; }
        public double Xmax { get => xmax; set => xmax = value; }
        public double Ymin { get => ymin; set => ymin = value; }
        public double Ymax { get => ymax; set => ymax = value; }
        
        public Canvas ChartCanvas { get => chartCanvas; set => chartCanvas = value; }
        //定义网格面板的宽度和高度
        public void ResizeCanvas(double width,double height) { 
            ChartCanvas.Width = width;
            ChartCanvas.Height = height;
        }
        //把面板的点转换成为图表坐标体系的点坐标
        public Point NormalizePoint(Point pt) {
            if (ChartCanvas.Width.ToString()=="Nan")
                ChartCanvas.Width = 400;
            if (ChartCanvas.Height.ToString() == "Nan")
                ChartCanvas.Height = 400;
            Point result = new Point();
            result.X = (pt.X - Xmin) * ChartCanvas.Width / (Xmax - Xmin);
            result.Y=ChartCanvas.Height-(pt.Y-Ymin)*ChartCanvas.Height/ (Ymax - Ymin);
            return result;
        }

    }
```
从ChartStyle类派生出ChartStyleGridlines类表示网格线条类,通过ChartStyleGridlines类来初始化图表的网格线条和X轴,Y轴,ChartStyleGridlines类定义图表的标题,X轴和Y轴单位间距,网格颜色等属性.是用Line对象来绘制网格的图表.
``` bash
 /// <summary>
    /// 网格线条类
    /// </summary>
    public class ChartStyleGridlines : ChartStyle
    {
        private string title;
        private Canvas textCanvas1;
        private bool isXGrid = true;
        private bool isYGrid = true;
        private Brush gridlineColor = new SolidColorBrush(Colors.LightGray);
        private double xTick = 1;
        private double yTick = 0.5;
        private GridlinePatternEnum gridlinePattern;

        //图表标题
        public string Title { get => title; set => title = value; }
        //添加X轴和Y轴的面板
        public Canvas textCanvas { get => textCanvas1; set => textCanvas1 = value; }
        //是否创建水平线条
        public bool IsXGrid { get => isXGrid; set => isXGrid = value; }
        //是否创建垂直线条
        public bool IsYGrid { get => isYGrid; set => isYGrid = value; }
        /// <summary>
        /// 网格颜色画刷
        /// </summary>
        public Brush GridlineColor { get => gridlineColor; set => gridlineColor = value; }
        //X轴单位间距
        public double XTick { get => xTick; set => xTick = value; }
        public double YTick { get => yTick; set => yTick = value; }
        //线条类型
        public GridlinePatternEnum GridlinePattern {
            get => gridlinePattern; set => gridlinePattern = value;
        }
        private double leftOffset = 20;
        private double rightOffset = 10;
        private double bottomOffset = 15;
        private Line gridline = new Line();

        public ChartStyleGridlines()
        {
            title = "Title";
        }
        /// <summary>
        /// 添加网格图表的样式
        /// </summary>
        /// <param name="tbTitle"></param>
        public void AddChartStyle(TextBlock tbTitle) {
            Point pt = new Point();
            Line tick=new Line();
            double offset = 0;
            double dx, dy;
            TextBlock tb = new TextBlock();
            //确定右边的偏移量
            tb.Text = Xmax.ToString();
            tb.Measure(new Size(Double.PositiveInfinity,double.PositiveInfinity));
            Size size = tb.DesiredSize;
            rightOffset = size.Width / 2 + 2;
            //确定左边的偏移量
            for (dy =Ymin; dy <=Ymax; dy+=YTick)
            {
                pt = NormalizePoint(new Point(Xmin,dy));
                tb=new TextBlock();
                tb.Text = dy.ToString();
                tb.TextAlignment = TextAlignment.Right;
                tb.Measure(new Size(double.PositiveInfinity,double.PositiveInfinity));
                size= tb.DesiredSize;
                if (offset < size.Width)
                    offset = size.Width;
            }
            leftOffset = offset + 5;
            ChartCanvas.Width=textCanvas.Width-leftOffset-rightOffset;
            ChartCanvas.Height = textCanvas.Height - bottomOffset - size.Height / 2;
            Canvas.SetLeft(ChartCanvas, leftOffset);
            Canvas.SetTop(ChartCanvas,bottomOffset);
            //创建报表的边框
            Rectangle chartRect= new Rectangle();
            chartRect.Stroke = new SolidColorBrush(Colors.Black);
            chartRect.Width=ChartCanvas.Width;
            chartRect.Height=ChartCanvas.Height;
            ChartCanvas.Children.Add(chartRect);
            //创建垂直线条
            if (IsYGrid == true)
            {
                for (dx = Xmin + XTick; dx < Xmax; dx += XTick)
                {
                    gridline = new Line();
                    AddLinePattern();
                    gridline.X1 = NormalizePoint(new Point(dx,Ymin)).X;
                    gridline.Y1 = NormalizePoint(new Point(dx, Ymin)).Y;
                    gridline.X2 = NormalizePoint(new Point(dx,Ymax)).X;
                    gridline.Y2 = NormalizePoint(new Point(dx, Ymax)).Y;
                    ChartCanvas.Children.Add(gridline);
                }
            }
            //创建水平线条
            if (IsXGrid==true)
            {
                for (dy=Ymin+YTick;dy<Ymax;dy+=YTick)
                {
                    gridline = new Line();
                    AddLinePattern();
                    gridline.X1 = NormalizePoint(new Point(Xmin, dy)).X;
                    gridline.Y1 = NormalizePoint(new Point(Xmin,dy)).Y;
                    gridline.X2= NormalizePoint(new Point(Xmax,dy)).X;
                    gridline.Y2= NormalizePoint(new Point(Xmax,dy)).Y;
                    ChartCanvas.Children.Add(gridline);
                }
            }
            //创建X轴
            for (dx=Xmin;dx<=Xmax;dx+=XTick)
            {
                pt = NormalizePoint(new Point(dx,Ymin));
                tick = new Line();
                tick.Stroke = new SolidColorBrush(Colors.Black);
                tick.X1 = pt.X; tick.Y1=pt.Y;
                tick.X2= pt.X;tick.Y2= pt.Y-5;
                ChartCanvas.Children.Add(tick);
                tb=new TextBlock();
                tb.Text = dx.ToString();
                tb.Measure(new Size(double.PositiveInfinity,double.PositiveInfinity));
                size = tb.DesiredSize;
                textCanvas.Children.Add(tb);
                Canvas.SetLeft(tb,leftOffset+pt.X-size.Width/2);
                Canvas.SetTop(tb,pt.Y+10+size.Height/2);
            }
            //创建Y轴
            for (dy=Ymin;dy<=Ymax;dy+=YTick)
            {
                pt=NormalizePoint(new Point(Xmin,dy));
                tick=new Line();
                tick.Stroke = new SolidColorBrush(Colors.Black);

                tick.X1 = pt.X;
                tick.Y1=pt.Y;
                tick.X2= pt.X+5;
                tick.Y2= pt.Y;
                ChartCanvas.Children.Add(tick);
                tb.Text = dy.ToString();
                tb.Measure(new Size(double.PositiveInfinity,double.PositiveInfinity));
                size= tb.DesiredSize;
                textCanvas.Children.Add(tb);
                Canvas.SetLeft(tb,-30);
                Canvas.SetTop(tb,pt.Y);
            }
            //图表标题
            tbTitle.Text = Title;
        }
        /// <summary>
        /// 添加线条的样式
        /// </summary>
        /// <exception cref="NotImplementedException"></exception>
        private void AddLinePattern()
        {
            gridline.Stroke = gridlineColor;
            gridline.StrokeThickness = 1;
            switch (gridlinePattern)
            {
                case GridlinePatternEnum.Solid:
                    break;
                case GridlinePatternEnum.Dash:
                    DoubleCollection doubleCollection= new DoubleCollection();
                    doubleCollection.Add(4);
                    doubleCollection.Add(3);
                    gridline.StrokeDashArray= doubleCollection;
                    break;
                case GridlinePatternEnum.Dot:
                    doubleCollection = new DoubleCollection();
                    doubleCollection.Add(1);
                    doubleCollection.Add(2);
                    gridline.StrokeDashArray= doubleCollection;
                    break;
                case GridlinePatternEnum.DashDot:
                    doubleCollection = new DoubleCollection();
                    doubleCollection.Add(4);
                    doubleCollection.Add(2);
                    doubleCollection.Add(1);
                    doubleCollection.Add(2);
                    gridline.StrokeDashArray= doubleCollection;
                    break;
                default:
                    break;
            }
        }
    }
```
13.32.定义线性数据图形类
线性数据图是在报表展现数据走势的图形,定义一个线性数据图形类DataSeries.在这个类里面定义了一个多边形Polyline类的属性LineSeries来表示绘制线性图形,除此之外,还有线条颜色属性LineColor,线条大小属性LineThickness.线条类型属性LinePattern和图形名称属性SeriesName.
``` bash
 /// <summary>
    /// 线性数据图形类
    /// </summary>
    public class DataSeries
    {
        //线性图表
        private Polyline lineSeries = new Polyline();
        private Brush lineColor;
        private double lineThickness = 1;
        private string seriesName="Default Name";
        private LinePatternEnum linePattern;

        public DataSeries()
        {
            LineColor = new SolidColorBrush(Colors.Black);
        }

        public Polyline LineSeries { get => lineSeries; set => lineSeries = value; }
        /// <summary>
        /// 线条颜色
        /// </summary>
        public Brush LineColor { get => lineColor; set => lineColor = value; }
        //线条大小
        public double LineThickness { get => lineThickness; set => lineThickness = value; }
        //线条类型
        public LinePatternEnum LinePattern { get => linePattern; set => linePattern = value; }
        /// <summary>
        /// 图形名称
        /// </summary>
        public string SeriesName { get => seriesName; set => seriesName = value; }
        /// <summary>
        /// 添加线条的样式
        /// </summary>
        public void AddLinePattern() { 
            //
        }

    }

 public enum LinePatternEnum
    {
        Solid=1,
        Dash=2,
        Dot=3,
        DashDot=4,
        None=5
    }
```
上面的DataSeries类只是封装了单个线性图形的基本属性和形状,下面再定义一个DataCollection类表示图形数据集合类,用于实现把线性图形按照定义的坐标体系添加到界面的面板上
``` bash
 /// <summary>
    /// 图形数据集合类
    /// </summary>
    public class DataCollection
    {
        private List<DataSeries> dataList;

        //线性数据图形类集合
        public List<DataSeries> DataList { get => dataList; set => dataList = value; }
        public DataCollection() { 
            dataList= new List<DataSeries>();
        }
        /// <summary>
        /// 往Canvas面板上按照坐标系的坐标来添加线性图形
        /// </summary>
        /// <param name="canvas"></param>
        /// <param name="cs"></param>
        public void AddLines(Canvas canvas,ChartStyle cs) {
            int j = 0;
            foreach (DataSeries ds in dataList)
            {
                if (ds.SeriesName=="Default Name")
                {
                    ds.SeriesName = "DataSeries" + j.ToString();
                }
                ds.AddLinePattern();
                for (int i = 0; i < ds.LineSeries.Points.Count; i++)
                {
                    ds.LineSeries.Points[i] = cs.NormalizePoint(ds.LineSeries.Points[i]);
                }
                canvas.Children.Add(ds.LineSeries);
                j++;
            }
        }
    }
```
13.33.实现图例
图例的实现其实就是把整个报表用到的图形用简单的线段画出来并标出名称,图例类Legend类里面定义了一个AddLegend方法,通过使用报表中的ChartStyleGridlines对象和DataCollection对象作为参数,然后取出报表中图形的样本和名称,添加到Canvas面板上,这样就实现了一个图例的模块.
``` bash
 public class Legend
    {
        public bool IsLegend { get; private set; }
        public Canvas legendCanvas { get; private set; }
        public bool IsBorder { get; private set; }
        /// <summary>
        /// 添加图例
        /// </summary>
        /// <param name="cs">报表的网格图形对象</param>
        /// <param name="dc">报表的图形数据集合对象</param>
        public void AddLegend(ChartStyleGridlines cs,DataCollection dc) { 
            TextBlock tb= new TextBlock();
            if (dc.DataList.Count < 1 || !IsLegend)
                return;
            int n = 0;
            //取出每个图形的名称
            string[] legendLabels = new string[dc.DataList.Count];
            foreach (DataSeries ds in dc.DataList)
            {
                legendLabels[n] = ds.SeriesName;
                n++;
            }
            double legendWidth = 0;
            Size size = new Size(0,0);
            //创建每个图形名称的TextBlock控件
            for (int i = 0; i < legendLabels.Length; i++)
            {
                tb = new TextBlock();
                tb.Text= legendLabels[i];
                tb.Measure(new Size(double.PositiveInfinity,double.PositiveInfinity));
                size = tb.DesiredSize;
                if (legendWidth < size.Width)
                    legendWidth = size.Width;

            }
            //80是预留给线条示例的长度位置
            legendWidth += 80;
            legendCanvas.Width = legendWidth + 5;
            //30是分配给每个图形示例的高度
            double legendHeight=30*dc.DataList.Count;

            double sx = 6;
            double sy = 15;
            double textheight = size.Height;
            double lineLength = 34;
            //创建图例的边框
            Rectangle legendRect= new Rectangle();
            legendRect.Stroke = new SolidColorBrush(Colors.Black);
            legendRect.Width= legendWidth;
            legendRect.Height= legendHeight;
            if (IsLegend && IsBorder)
                legendCanvas.Children.Add(legendRect);
            n = 1;
            //创建每个图形的线段
            foreach (DataSeries ds in dc.DataList)
            {
                double xSymbol = sx + lineLength / 2;
                double xText = 2 * sx + lineLength;
                double yText = n * sy + (2 * n - 1) * textheight / 2;
                Line line=new Line();
                line.X1 = sx;
                line.Y1=sy;
                line.X2 = sx + lineLength;
                line.Y2 = yText;
                legendCanvas.Children.Add(line);
                tb=new TextBlock();
                tb.FontSize = 15;
                tb.Text = ds.SeriesName;
                legendCanvas.Children.Add(tb);
                Canvas.SetTop(tb,yText-15);
                Canvas.SetLeft(tb,xText+10);
                n++;
            }
        }
    }
```
13.34.实现线性报表
上面已经把图形表格,坐标轴,线性数据图形和图例的相关逻辑都封装好了,下面就要利用这些封装好的模板来创建一个线性报表,首先需要再Xaml页面上定义3个Canvas面板分别表示图例面板,坐标轴面板和线性图形面板,然后再利用上面封装的类来初始化这些面板生成线性报表.
``` bash
<Page
    x:Class="App1.BlankPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:App1"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006" xmlns:controls="using:App1.Controls" xmlns:models="using:App1.Models" xmlns:converters="using:App1.Converters"
   
    mc:Ignorable="d"
    Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    <Page.Resources>
        
            <DataTemplate x:Key="PersonNameDataTemplate">
            <StackPanel Orientation="Horizontal">
                <TextBlock Text="{Binding LastName}"/>
                <TextBlock Text=","/>
                <TextBlock Text="{Binding FirstName}"/>
            </StackPanel>
        </DataTemplate>
        <Style x:Name="ItemsControlStyle" TargetType="ItemsControl">
            <Setter Property="Template">
                <Setter.Value>
                    <ControlTemplate TargetType="ItemsControl">
                        <ScrollViewer BorderBrush="Red" BorderThickness="6">
                            <StackPanel Orientation="Horizontal" Background="Blue">
                                <Border BorderBrush="Yellow" BorderThickness="3">
                                    <ItemsPresenter/>
                                </Border>
                            </StackPanel>
                        </ScrollViewer>
                    </ControlTemplate>
                </Setter.Value>
            </Setter>
        </Style>
        <!--选中数据项的样式-->
        <DataTemplate x:Key="dataTemplateSelectKey" x:Name="dataTemplateSelectName">
            <Grid Tapped="StackPanel_Tapped" Background="Red">
                <TextBlock Text="{Binding LastName}" FontSize="50"/>
            </Grid>
        </DataTemplate>
        <!--默认数据项的样式,注意默认的数据项样式不能在C#中再次调用-->
        <DataTemplate x:Key="dataTemplateDefaultKey" x:Name="dataTemplateDefaultName">
            <StackPanel Orientation="Horizontal" Tapped="StackPanel_Tapped">
                <TextBlock Text="{Binding LastName}"/>
                <TextBlock Text=","/>
                <TextBlock Text="{Binding FirstName}"/>
            </StackPanel>
        </DataTemplate>
        <!--非选中数据项的样式-->
        <DataTemplate x:Key="dataTemplateNoSelectKey" x:Name="dataTemplateNoSelectName">
            <StackPanel Orientation="Horizontal" Tapped="StackPanel_Tapped">
                <TextBlock Text="{Binding LastName}"/>
                <TextBlock Text=","/>
                <TextBlock Text="{Binding FirstName}"/>
            </StackPanel>
        </DataTemplate>

        <models:Clock x:Key="clock"/>
        <converters:HoursToDayStringConverter x:Key="booleanToDayString"/>
    </Page.Resources>
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="auto"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>
        <Grid>
            <Button x:Name="backButton" Margin="24,24,24,24" Click="backButton_Click" Style="{StaticResource NavigationBackButtonNormalStyle}"/>
        </Grid>
        <Pivot Title="控件" Grid.Row="1">
            <PivotItem Header="Border">
                <StackPanel>
                    <Border Background="Coral" Padding="10" CornerRadius="30,38,150,29" BorderThickness="8 15 10 2" BorderBrush="Azure"/>
                    <Border BorderThickness="1,3,5,7" BorderBrush="Blue" CornerRadius="10" Width="200">
                        <TextBlock Text="蓝色的Border" ToolTipService.ToolTip="这是蓝色的Border吗?" FontSize="30" TextAlignment="Center"/>

                    </Border>
                    <!--单击后显示边框-->
                    <Border x:Name="TextBorder" BorderThickness="10">
                        <Border.BorderBrush>
                            <SolidColorBrush Color="Red" Opacity="0"/>
                        </Border.BorderBrush>
                        <TextBlock Text="请单击我一下!" PointerPressed="TextBlock_PointerPressed" FontSize="20"/>
                    </Border>
                    <!--颜色渐变的边框-->
                    <Border x:Name="brdTest" BorderThickness="4" Width="200" Height="150">
                        <Border.BorderBrush>
                            <LinearGradientBrush x:Name="borderLinearGradientBrush" MappingMode="RelativeToBoundingBox" StartPoint="0.5,0" EndPoint="0.5,1">
                                <LinearGradientBrush.GradientStops>
                                    <GradientStop Color="Yellow" Offset="0"/>
                                    <GradientStop Color="Blue" Offset="1"/>
                                </LinearGradientBrush.GradientStops>
                            </LinearGradientBrush>
                        </Border.BorderBrush>
                    </Border>
                </StackPanel>
            </PivotItem>
            <PivotItem Header="HyperlinkButton">
                <StackPanel>
                    <HyperlinkButton Width="200" Content="链接按钮" Background="Blue" Foreground="Orange" FontWeight="Bold" Margin="0,0,0,30"/>
                    <HyperlinkButton Content="Google" NavigateUri="http://goole.com"/>

                </StackPanel>
            </PivotItem>
            <PivotItem Header="Button">
                <ScrollViewer>
                    <StackPanel Orientation="Vertical">

                        <TextBlock Text="你好" FontSize="30" HorizontalAlignment="Left" VerticalAlignment="Center"/>
                        <StackPanel x:Name="sp_Show">
                            <Button x:Name="bt_addXaml" Content="加载Xaml按钮" Click="bt_addXaml_Click"/>
                        </StackPanel>
                        <StackPanel x:Name="stackPanel" Margin="12,0,12,0">
                            <TextBox>
                            </TextBox>
                            <Button Content="遍历" Click="Button_Click1">
                            </Button>
                        </StackPanel>
                        <Button HorizontalAlignment="Center" VerticalAlignment="Center" Content="Hello,App" Click="Button_Click"/>
                        <Button Content="按钮1" Height="80" Name="button1" VerticalAlignment="Top" Width="300" Click="button1_Click"/>

                        <Button Content="按钮2" FontSize="48" FontStyle="Italic" Foreground="Red" Background="Blue" BorderThickness="10" BorderBrush="Yellow" Padding="20"/>

                        <Button Width="165">
                            <StackPanel>
                                <Image Source="Assets/StoreLogo.png" Stretch="None" Height="61" Width="94"/>

                            </StackPanel>
                        </Button>

                        <Button>
                            <SymbolIcon Symbol="Emoji2"/>
                        </Button>
                        <StackPanel>
                            <TextBox InputScope="TelephoneNumber">
                                <TextBox.Header>
                                    请输入电话号码:
                                </TextBox.Header>
                            </TextBox>
                            <TextBox x:Name="TextBox1" TextWrapping="Wrap" AcceptsReturn="True" Header="输入信息:" SelectionHighlightColor="Red" TextChanged="TextBox1_TextChanged" SelectionChanged="TextBox1_SelectionChanged" Paste="TextBox1_Paste"/>
                            <TextBlock x:Name="textblock2" Text="操作信息:" FontSize="20"/>
                            <TextBlock x:Name="textBlock1" TextWrapping="Wrap" FontSize="20"/>
                        </StackPanel>
                    </StackPanel>
                </ScrollViewer>
            </PivotItem>
            <PivotItem Header="ScrollViewer">
                <StackPanel Orientation="Vertical">
                    <ScrollViewer Height="200" Width="200" VerticalScrollBarVisibility="Visible" HorizontalScrollBarVisibility="Visible">
                    <ScrollViewer.Content>
                        <StackPanel>
                            <Image Source="ms-appx:///Assets/StoreLogo.png"/>
                        </StackPanel>
                    </ScrollViewer.Content>
                </ScrollViewer>
               
                    <ScrollViewer Name="scrollViewer1" VerticalScrollBarVisibility="Hidden" Height="300">
                        <StackPanel Name="stkpnImage"/>
                    </ScrollViewer>
                    <Button Content="往上" FontSize="30" Click="btnUp_Click"/>
                    <Button Content="往下" FontSize="30" Click="btnDown_Click"/>
                    <Button Content="停止" FontSize="30"
                            Click="stop_Click"/>

                </StackPanel>
            </PivotItem>
            <PivotItem Header="ProgressBar">
                <StackPanel>
                    <TextBlock Text="选择ProgressBar的类型:"/>
                    <RadioButton Content="Determinate类型" Height="71" Name="radioButton1" GroupName="Type"/>
                    <RadioButton Content="Indeterminate类型" Height="71" Name="radioButton2" GroupName="Type" IsChecked="True"/>
                    <Button Content="启动ProgressBar" Height="72" x:Name="begin" Click="begin_Click"/>
                    <Button Content="取消ProgressBar" Height="72" x:Name="cancel" Click="cancel_Click"/>
                    <ProgressBar x:Name="progressBar1" IsIndeterminate="True"/>




                </StackPanel>

                
            </PivotItem>
            <PivotItem Header="Slider">
                <StackPanel Grid.Row="1">
                    <Grid Name="controlGrid">
                        <Grid.RowDefinitions>
                            <RowDefinition Height="*"/>
                            <RowDefinition Height="*"/>
                            <RowDefinition Height="*"/>
                        </Grid.RowDefinitions>
                        <Grid.ColumnDefinitions>
                            <ColumnDefinition Width="*"/>
                            <ColumnDefinition Width="*"/>
                            <ColumnDefinition Width="*"/>
                        </Grid.ColumnDefinitions>
                        <!--设置红色-->
                        <TextBlock Text="红色" Foreground="Red" FontSize="20"/>
                        <Slider x:Name="redSlider" Grid.Row="1" Foreground="Red" Minimum="0" Maximum="255"
                        ValueChanged="redSlider_ValueChanged"/>
                        <TextBlock x:Name="redText" Grid.Row="2" Text="0" Foreground="Red" FontSize="20"/>
                        <!--设置绿色-->
                        <TextBlock Grid.Column="1" Text="绿色" Foreground="Green" FontSize="20"/>
                        <Slider x:Name="greenSlider" Foreground="Green" FontSize="20" Minimum="0" Maximum="255" ValueChanged="redSlider_ValueChanged" Grid.Column="1" Grid.Row="1"></Slider>
                        <TextBlock x:Name="greenText" Grid.Column="1" Grid.Row="2" Foreground="Green" FontSize="20"/>
                        <!--设置蓝色-->
                        <TextBlock Grid.Column="2" Grid.Row="0" Text="蓝色" Foreground="Blue" FontSize="20"/>
                        <Slider x:Name="blueSlider" Grid.Column="2" Grid.Row="1" Foreground="Blue" Minimum="0" Maximum="255" ValueChanged="redSlider_ValueChanged"/>
                        <TextBlock x:Name="blueText" Grid.Column="2" Grid.Row="2" Text="0" Foreground="Blue" FontSize="20"/>

                        <!--设置-->
                    </Grid>
                    <Ellipse Height="100" x:Name="ellipse2" Stroke="Black" StrokeThickness="1" Width="224"/>
                    <TextBlock x:Name="textBlockColor" Text="颜色" FontSize="26"/>

                </StackPanel>
            </PivotItem>
            <PivotItem Header="TimePicker DatePicker">
                <StackPanel Grid.Row="2">
                    <TimePicker x:Name="time" Header="请选择时间:" TimeChanged="time_TimeChanged"/>
                    <DatePicker x:Name="date" Header="请选择日期:"
                        DateChanged="date_DateChanged"/>
                    <TextBlock x:Name="infoTime" FontSize="20" TextWrapping="Wrap"/>
                </StackPanel>
            </PivotItem>
            <PivotItem Header="Hub">
                <!--在大标题上添加程序的图标-->            
                    <Hub>
                        <Hub.Header>
                            <StackPanel Orientation="Horizontal">
                                <Image Source="Assets/StoreLogo.scale-100.png" Height="100"/>
                                <TextBlock Text="My App"/>
                            </StackPanel>
                        </Hub.Header>
                        <HubSection Header="first item">
                            <HubSection.ContentTemplate>
                                <DataTemplate>
                                    <StackPanel>
                                        <TextBlock Text="第一个Item" FontSize="50"/>
                                        <TextBlock Text="这是第一个item" FontSize="50"/>
                                    </StackPanel>
                                </DataTemplate>
                            </HubSection.ContentTemplate>
                        </HubSection>

                        <HubSection Header="second item">
                            <HubSection.ContentTemplate>
                                <DataTemplate>
                                    <StackPanel>
                                        <TextBlock Text="第二个Item" FontSize="50"/>
                                        <TextBlock Text="这是第二个item" FontSize="50"/>
                                    </StackPanel>
                                </DataTemplate>
                            </HubSection.ContentTemplate>
                        </HubSection>

                        <HubSection Header="Third item">
                            <HubSection.ContentTemplate>
                                <DataTemplate>
                                    <StackPanel>
                                        <TextBlock Text="第三个Item" FontSize="50"/>
                                        <TextBlock Text="这是第三个item" FontSize="50"/>
                                    </StackPanel>
                                </DataTemplate>
                            </HubSection.ContentTemplate>
                        </HubSection>
                    </Hub>
                
            </PivotItem>
            <PivotItem Header="Flyout">
                <StackPanel>
                    <Button Content="Show Flyout">
                        <Button.Flyout>
                            <Flyout>
                                <StackPanel>
                                    <TextBox PlaceholderText="Please Enter Name."/>
                                    <Button HorizontalAlignment="Right" Content="确定"/>
                                </StackPanel>
                            </Flyout>
                        </Button.Flyout>
                    </Button>
                    <!--浮出上下文菜单,点击菜单后改变当前按钮上的文本内容-->
                    <Button x:Name="menuFlyoutButton" Content="Show MenuFlyout">
                        <Button.Flyout>
                            <MenuFlyout>
                                <MenuFlyoutItem Text="Option 1" Click="MenuFlyoutItem_Click"/>
                                <MenuFlyoutItem Text="Option 2"
                                                Click="MenuFlyoutItem_Click"/>
                                <MenuFlyoutItem Text="Option 3" Click="MenuFlyoutItem_Click"/>
                            </MenuFlyout>
                        </Button.Flyout>
                    </Button>
                    
                    <!--浮出选择日期弹窗,点击确认后触发DatePicked事件,然后可以获取选中的日期-->
                    <Button Content="Show DatePicker">
                        <Button.Flyout>
                            <DatePickerFlyout DatePicked="DatePickerFlyout_DatePicked"/>
                        </Button.Flyout>
                    </Button>
                    <!--浮出选择时间弹窗,点击确认后触发DatePicked事件,然后可以获取选中的时间-->
                    <Button Content="Show TimePicker">
                        <Button.Flyout>
                            <TimePickerFlyout TimePicked="TimePickerFlyout_TimePicked"/>
                        </Button.Flyout>
                    </Button>
                    <!--浮出选择弹窗,显示底下的确认取消菜单栏并且处理其确认事件Confirmed-->
                    <Button Content="Show Picker">
                        <Button.Flyout>
                            <PickerFlyout Confirmed="PickerFlyout_Confirmed"
                                          ConfirmationButtonsVisible="True">
                                <TextBlock Text="你确定吗？？？？？" FontSize="30" Margin="0 100 0 0"/>
                            </PickerFlyout>
                        </Button.Flyout>
                    </Button>
<!--浮出选择列表弹窗,绑定集合的数据,处理选中的事件ItemsPicked-->
                    <Button Content="Show DataPicker">
                        <Button.Flyout>
                            <ListPickerFlyout x:Name="listPickerFlyout" Title="Select Mobile Tag：" ItemsPicked="listPickerFlyout_ItemsPicked">
                                <ListPickerFlyout.ItemTemplate>
                                    <DataTemplate>
                                        <TextBlock Text="{Binding}" FontSize="30"/>
                                        
                                    </DataTemplate>
                                </ListPickerFlyout.ItemTemplate>
                            </ListPickerFlyout>
                        </Button.Flyout>
                    </Button>
                    <!--使用附加属性FlyoutBase.AttachedFlyout来实现Flyout控件-->
                    <TextBlock Text="Please Click Me!" Tapped="TextBlock_Tapped" FontSize="20">
                        <FlyoutBase.AttachedFlyout>
                            <Flyout>
                                <TextBox Text="你好!">
                                    
                                    </TextBox>
                                </Flyout>
                            </FlyoutBase.AttachedFlyout>
                    </TextBlock>
                </StackPanel>
            </PivotItem>
            <PivotItem Header="ComboBox">
                <StackPanel>
                    <!--纯文本的下拉框-->
                    <ComboBox Header="Colors" PlaceholderText="Pick a Color">
                        <x:String>
                            Blue
                        </x:String>
                        <x:String>
                            Green
                        </x:String>
                        <x:String>
                            Red
                        </x:String>
                        <x:String>
                            Yellow
                        </x:String>
                    </ComboBox>
                    <!--数据绑定的下拉框-->
                    <ComboBox x:Name="comboBox2" DropDownClosed="comboBox2_DropDownClosed">
                        <ComboBox.ItemTemplate>
                            <DataTemplate>
                                <StackPanel Orientation="Horizontal">
                                    <TextBlock Text="{Binding Name}" FontSize="30"/>
                                    <TextBlock Text="{Binding Age}" Margin="50 10 0 0"/>
                                </StackPanel>
                            </DataTemplate>
                        </ComboBox.ItemTemplate>
                    </ComboBox>
                    
                   <!--数据绑定的下拉框关闭后,这里显示选中的选项的信息-->
                    <TextBlock x:Name="Info" FontSize="20"/>
                </StackPanel>
            </PivotItem>
            <PivotItem Header="SplitView">
                <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
                    <ToggleButton Click="ToggleButton_Click" VerticalAlignment="Top" Foreground="Green">
                        <ToggleButton.Content>
                            <Border Background="Transparent" Width="40" Height="40">
                                <FontIcon x:Name="hamburger" FontFamily="Segoe MDL2 Assets" Glyph="&#xE700;"/>
                                
                            </Border>
                        </ToggleButton.Content>
                    </ToggleButton>
                    <SplitView x:Name="Splitter" IsPaneOpen="True">
                        <SplitView.Pane>
                            <StackPanel VerticalAlignment="Center">
                                <Button Content="菜单1" Click="Button_Click_1"/>
                                <Button Content="菜单2" Click="Button_Click_1"/>
                                <Button Content="菜单3"
                                        Click="Button_Click_1"/>
                                <Button Content="菜单4" Click="Button_Click_1"/>
                            </StackPanel>
                        </SplitView.Pane>
                        <Grid>
                            <TextBlock x:Name="tb" Text="" VerticalAlignment="Center" HorizontalAlignment="Center"/>
                        </Grid>
                    </SplitView>
                </Grid>
            </PivotItem>
            <PivotItem Header="ScrollableTextBlock">
                <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
                    <Grid x:Name="ContentPanel" Grid.Row="1" Margin="12,0,12,0">
                        <controls:ScrollableTextBlock x:Name="scrollableTextBlock1" FontSize="30"/>
                    </Grid>
                </Grid>
            </PivotItem>
            <PivotItem Header="Canvas">
                <StackPanel>
                    <Canvas>
                        <Button Content="按钮1" Height="200" Width="200"/>
                    </Canvas>
                    <Canvas>
                        <Button Content="按钮2" Canvas.Left="30" Canvas.Top="30" Height="200" Width="200"/>
                        
                    </Canvas>
                    <Canvas>
                        <Button Content="按钮1" Canvas.Left="30" Canvas.Top="30" Background="Blue" Height="200" Width="200"/>
                        <Button Content="按钮2" Canvas.Left="100" Canvas.Top="100" Background="Red" Height="200"
                                Width="200"/>
                        <Button Content="按钮3" Canvas.Left="170"
                                Canvas.Top="170" Background="Yellow"
                                Height="200" Width="200"/>
                        
                    </Canvas>
                    <Canvas Width="200" Height="200" Background="LimeGreen">
                        <Button Content="按钮1" Canvas.Left="30" Canvas.Top="30" Background="Blue" Height="200" Width="200"/>
                    </Canvas>
                    <Canvas Width="200" Height="200" Background="White">
                        <Canvas Height="50" Width="50" Canvas.Left="30"
                                Canvas.Top="30" Background="Blue"/>
                        <Canvas Height="50" Width="50" Canvas.Left="130" Canvas.Top="30" Background="Red"/>
                    </Canvas>
                </StackPanel>
            </PivotItem>
            <PivotItem Header="渐变矩形">
                <Canvas Background="White">
                    <Canvas Height="400" Width="400" Canvas.Left="0" Canvas.Top="50" Background="Gray" Opacity="0.1"/>
                    <Canvas Height="360" Width="360" Canvas.Left="20"
                            Canvas.Top="70" Background="Gray" Opacity="0.2"/>
                    <Canvas Height="320" Width="320" Canvas.Left="40" Canvas.Top="90" Background="Gray" Opacity="0.3"/>
                    <Canvas Height="280" Width="280" Canvas.Left="60"
                            Canvas.Top="110" Background="Gray" Opacity="0.4"/>
                    <Canvas Height="240" Width="240" Canvas.Left="80"
                            Canvas.Top="130" Background="Gray" Opacity="0.5"/>

                        <Canvas Height="200" Width="200" Canvas.Left="100" Canvas.Top="150" Background="Gray" Opacity="0.6"/>
                        <Canvas Height="160" Width="160" Canvas.Left="120"
                            Canvas.Top="170" Background="Black" Opacity="0.3"/>
                        <Canvas Height="120" Width="120" Canvas.Left="140" Canvas.Top="190" Background="Black" Opacity="0.4"/>
                        <Canvas Height="80" Width="80" Canvas.Left="160"
                            Canvas.Top="210" Background="Black" Opacity="0.5"/>
                        <Canvas Height="40" Width="40" Canvas.Left="180"
                            Canvas.Top="230" Background="Black" Opacity="0.6"/>
                    </Canvas>
            </PivotItem>
            <PivotItem Header="RelativePanel">
                <RelativePanel VerticalAlignment="Center" HorizontalAlignment="Center">
                    <Button x:Name="bt1" Content="按钮1"/>
                    <Button x:Name="bt2" Content="按钮2" RelativePanel.RightOf="bt1" Height="100"/>
                    <Button x:Name="bt3" Content="按钮3... ..."
                            RelativePanel.AlignLeftWith="bt2"
                            RelativePanel.AlignBottomWithPanel="True"/>

                </RelativePanel>
            </PivotItem>
            <PivotItem Header="Grid">
                <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
                    <Grid.RowDefinitions>
                        <RowDefinition Height="*"/>
                        <RowDefinition Height="3*"/>
                        <RowDefinition Height="*"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                    </Grid.RowDefinitions>
                    <Ellipse Grid.Row="1" Fill="Red" HorizontalAlignment="Stretch" VerticalAlignment="Stretch" MaxWidth="500" MaxHeight="500"/>
                    <Button Grid.Row="3" Content="按钮1" HorizontalAlignment="Stretch" Margin="0 0 0 2"
                            MaxHeight="500"/>
                    <Button Grid.Row="4" Content="按钮2" HorizontalAlignment="Stretch" MaxWidth="500"/>
                </Grid>
            </PivotItem>
            <PivotItem Header="AdaptiveTrigger">
                <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
                    <VisualStateManager.VisualStateGroups>
                        <VisualStateGroup>
                            <VisualState  x:Name="SideBySideState">
                                <VisualState.StateTriggers>
                                    <AdaptiveTrigger MinWindowWidth="600"/>
                                </VisualState.StateTriggers>
                                <VisualState.Setters>
                                    <Setter Target="theStackPanel.Orientation" Value="Horizontal"/>
                                </VisualState.Setters>
                            </VisualState>
                        </VisualStateGroup>
                    </VisualStateManager.VisualStateGroups>
                    <StackPanel x:Name="theStackPanel" Orientation="Vertical" HorizontalAlignment="Left" VerticalAlignment="Top">
                        <Button Content="按钮1" MinWidth="150" Margin="2"/>
                        <Button Content="按钮2" MinWidth="150"
                                Margin="2"/>
                        <Button Content="按钮3" MinWidth="150" Margin="2"/>
                    </StackPanel>
                </Grid>
            </PivotItem>
            <PivotItem Header="MeasureArrange">
                <StackPanel>
                    <Button Content="改变高度" Click="Button_Click_2"/>
                    <controls:TestPanel x:Name="element1" Width="60" Height="60" Background="Red" Margin="10"/>
                    <controls:TestUIElement x:Name="element2" Width="60" Height="60" Background="Red"/>
                    
                </StackPanel>
            </PivotItem>
            <PivotItem Header="CustomPanel">
                <Grid x:Name="ContentPanel1" Margin="12,0,12,0">
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="*"/>
                    </Grid.RowDefinitions>
                    <Slider Grid.Row="0" Value="5" ValueChanged="Slider_ValueChanged"/>
                    <controls:CirclePanel x:Name="circlePanel" Radius="50" Grid.Row="1" HorizontalAlignment="Center" VerticalAlignment="Center">
                        <TextBlock>Start here</TextBlock>
                        <TextBlock>TextBlock 2</TextBlock>
                        <TextBlock>TextBlock 3</TextBlock>
                        <TextBlock>TextBlock 4</TextBlock>
                        <TextBlock>TextBlock 5</TextBlock>
                        <TextBlock>TextBlock 6</TextBlock>
                        <TextBlock>TextBlock 7</TextBlock>
                    </controls:CirclePanel>
                </Grid>
            </PivotItem>
            <PivotItem Header="ApplicationDataContainerSettings">
                <StackPanel>
                    <!--输入应用设置的键-->
                    <StackPanel Orientation="Horizontal">
                        <TextBlock x:Name="textBlock2" Text="Key:" Width="150"/>
                        <TextBox x:Name="txtKey" Text="" Width="200"/>
                    </StackPanel>
                    <!--输入应用设置的值-->
                    <StackPanel Orientation="Horizontal" Margin="0 20 0 0">
                        <TextBlock Text="Value:" Width="150"/>
                        <TextBox x:Name="txtValue" Text="" Width="200"/>
                    </StackPanel>
                    <Button Content="保存" x:Name="btnSave" Click="btnSave_Click"/>
                    <Button Content="删除" x:Name="btnDelete"
                            Click="btnDelete_Click"/>
                    <Button Content="清空所有" x:Name="deleteall"
                            Click="deleteall_Click"/>
                    <!--显示容器内所有的键的列表,点击选中可以在上面的输入框里面查看和修改它对应的值-->
                    <TextBlock Text="Keys列表:"/>
                    <ListBox Height="168" x:Name="lstKeys" SelectionChanged="lstKeys_SelectionChanged"/>
                    
                </StackPanel>
              
                
            </PivotItem>
            <PivotItem Header="ApplicationDataContainer">
                <StackPanel>
                    <Button Content="创建Container" Click="CreateContainer_Click" Margin="2"/>
                    <Button Content="添加信息" Click="WriteSetting_Click" Margin="2"/>
                    <Button Content="删除信息" Click="DeleteSetting_Click" Margin="2"/>
                    <Button Content="删除Container" Click="DeleteContainer_Click" Margin="2"/>
                    <TextBlock x:Name="OutputTextBlock" TextWrapping="Wrap"/>
                </StackPanel>
            </PivotItem>
            <PivotItem Header="ApplicationDataCompositeValue">
                <StackPanel>
                    <Button Content="创建" Click="WriteCompositeSetting_Click"/>
                    <Button Content="删除" Click="DeleteCompositeSetting_Click"/>
                    <TextBlock x:Name="OutputTextBlock1" TextWrapping="Wrap"/>
                </StackPanel>
            </PivotItem>
            <PivotItem Header="StorageFolderFile">
                <StackPanel>
                    <TextBox Header="文件信息:" x:Name="infoStorage"
                             TextWrapping="Wrap"/>
                    <Button x:Name="bt_save" Content="保存" Margin="2" Click="bt_save_Click"/>
                    <Button x:Name="bt_read" Content="读取保存的文件" Margin="2" Click="bt_read_Click"/>
                    <Button x:Name="bt_delete" Content="删除文件"
                            Margin="2" Click="bt_delete_Click"/>
                </StackPanel>
            </PivotItem>
            <PivotItem Header="StreamBuffer">
                <StackPanel>
                    <Button x:Name="bt_crreate" Content="创建一个测试文件" Margin="2" Click="bt_crreate_Click"/>
                    <Button x:Name="bt_writebuffer" Content="写入buffer" Margin="2" Click="bt_writebuffer_Click"/>
                    <Button x:Name="bt_readbuffer" Content="读取buffer" Margin="2" Click="bt_readbuffer_Click"/>
                    <Button x:Name="bt_writestream" Content="写入stream" Margin="2" Click="bt_writestream_Click"/>
                    <Button x:Name="bt_readstream" Content="读取stream" Margin="2" Click="bt_readstream_Click"/>
                    <TextBlock x:Name="StreambufferTextBlock" TextWrapping="Wrap" FontSize="20"/>
                </StackPanel>
            </PivotItem>
            <PivotItem Header="GetFolderByUri">
                <StackPanel>
                    <TextBox Header="文件信息:" x:Name="UriInfo" TextWrapping="Wrap"/>
                    <Button x:Name="bt_saveuri" Content="创建文件" Click="bt_save_Click_1"/>
                    <Button x:Name="bt_readuri" Content="通过URI读取文件" Margin="2" Click="bt_read_Click_1"/>
                    
                </StackPanel>
            </PivotItem>
            <PivotItem Header="JsonObject">
                <StackPanel>
                    <TextBlock Text="Write your message" FontSize="20"/>
                    <TextBox x:Name="userName" Header="Name:"/>
                    <TextBox x:Name="userAge" Header="Age:" InputScope="Number"/>
                    <TextBlock Text="Your study school:" FontSize="20"/>
                    <CheckBox Content="Haerbing School" x:Name="school1"/>
                    <CheckBox Content="LanXiang School" x:Name="school2"/>
                    <StackPanel Orientation="Horizontal" HorizontalAlignment="Center">
                        <Button Content="保存" x:Name="save" Click="save_Click"/>
                        <Button Content="获取保存的信息" x:Name="get" Click="get_Click"/>
                        
                    </StackPanel>
                    <TextBlock x:Name="JsonObjectInfo" TextWrapping="Wrap" FontSize="20"/>
                </StackPanel>
            </PivotItem>

            <PivotItem Header="Xml">
                <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
                    <Grid x:Name="ContentPanelShop" Margin="12,0,12,0">
                        <!--展示购物清单的列表-->
                        <ListBox FontSize="48" x:Name="Files">
                        </ListBox>
                    </Grid>
                </Grid>
            </PivotItem>
            <PivotItem Header="ApplicationModelPackage">
                <ScrollViewer>
                    <StackPanel>
                        <Button Content="获取安装包的根目录" x:Name="btnGetFile" Click="btnGetFile_Click" Margin="2"/>
                        <TextBlock Text="文件夹列表:"/>
                        <ListBox x:Name="lbFolder"/>
                        <Button Content="打开选中的文件夹" x:Name="open" Click="open_Click" Margin="2"/>

                        <TextBlock Text="文件列表:"/>
                        <ListBox x:Name="lbFile">
                        </ListBox>
                        <Button Content="在选中文件夹下创建测试文件" x:Name="create" Click="create_Click" Margin="2"/>
                        <Button Content="删除选中的文件" x:Name="delete" Click="delete_Click" Margin="2"/>
                        
                    </StackPanel>
                </ScrollViewer>
            </PivotItem>
            <PivotItem Header="AppUri">
                <StackPanel>
                    <TextBlock Text="安装包的图片展示:"/>
                    <Image x:Name="packageImage" Height="200"/>
                    <TextBlock Text="应用存储的图片展示:"/>
                    <Image x:Name="appImage" Height="200"/>
                </StackPanel>
            </PivotItem>
            <PivotItem Header="Line">
                <Canvas x:Name="ContentPanelCanvas" Background="Transparent">
                
                </Canvas>
            </PivotItem>
            <PivotItem Header="Geometry">
                <StackPanel>
                    <!--直线-->
                    <Path Stroke="Red" StrokeThickness="2">
                        <Path.Data>
                            <LineGeometry StartPoint="0,0" EndPoint="400,20"/>
                        </Path.Data>
                    </Path>
                    <!--矩形路径-->
                    <Path Fill="Red">
                        <Path.Data>
                            <RectangleGeometry Rect="20,20,400,50"/>
                        </Path.Data>
                    </Path>
                    <!--椭圆路径-->
                    <Path Fill="Red">
                        <Path.Data>
                            <!--<GeometryGroup组合>-->
                            <GeometryGroup FillRule="EvenOdd">
                                <RectangleGeometry Rect="80,50,200,100"/>
                                <EllipseGeometry Center="300,100" RadiusX="80" RadiusY="60">
                                    
                                </EllipseGeometry>
                            </GeometryGroup>
                        </Path.Data>
                    </Path>

                    <Path Fill="Red" StrokeThickness="3">
                        <Path.Data>
                            <!--<GeometryGroup组合>-->
                            <GeometryGroup FillRule="Nonzero">
                                <RectangleGeometry Rect="80,50,200,100"/>
                                <EllipseGeometry Center="300,100" RadiusX="80" RadiusY="60"/>
                            </GeometryGroup>
                        </Path.Data>
                    </Path>
                </StackPanel>
            </PivotItem>
            <PivotItem Header="Segment">
                <Path Stroke="Red" StrokeThickness="5">
                    <Path.Data>
                        <PathGeometry>
                            <PathFigure StartPoint="10,20">
                                <PathFigure.Segments>
                                    <LineSegment Point="100,130"/>
                                </PathFigure.Segments>
                            </PathFigure>
                        </PathGeometry>
                    </Path.Data>
                </Path>
            </PivotItem>
            <PivotItem Header="ArcSegment">
                <Path Stroke="Red" StrokeThickness="5">
                    <Path.Data>
                        <PathGeometry>
                            <PathGeometry.Figures>
                                <PathFigure StartPoint="10,50">
                                    <PathFigure.Segments>
                                        <ArcSegment Size="50,50" RotationAngle="45" IsLargeArc="True" SweepDirection="Clockwise" Point="200,100">
                                            
                                        </ArcSegment>
                                    </PathFigure.Segments>
                                </PathFigure>
                            </PathGeometry.Figures>
                        </PathGeometry>
                    </Path.Data>
                </Path>
            </PivotItem>
            <PivotItem Header="BezierSegment">
                <ScrollViewer>
                    <StackPanel Orientation="Vertical">
                        <Path Stroke="Red" StrokeThickness="5">
                            <Path.Data>
                                <PathGeometry>
                                    <PathGeometry.Figures>
                                        <PathFigure StartPoint="10,20">
                                            <PathFigure.Segments>
                                                <BezierSegment Point1="100,0"
                                                       Point2="200,200"
                                                       Point3="300,100"/>

                                            </PathFigure.Segments>
                                        </PathFigure>
                                    </PathGeometry.Figures>
                                </PathGeometry>
                            </Path.Data>
                        </Path>
                        <Path Stroke="Red" StrokeThickness="5">
                            <Path.Data>
                                <PathGeometry>
                                    <PathGeometry.Figures>
                                        <PathFigure StartPoint="10,20">
                                            <PathFigure.Segments>
                                                <!--<PolyBezierSegment>-->
                                                <PolyBezierSegment Points="0,0 100,0 150,100 150,0 200,0 300,100"/>

                                            </PathFigure.Segments>
                                        </PathFigure>
                                    </PathGeometry.Figures>
                                </PathGeometry>
                            </Path.Data>
                        </Path>
                        <Path Stroke="Red" StrokeThickness="5">
                            <Path.Data>
                                <PathGeometry>
                                    <PathGeometry.Figures>
                                        <PathFigure StartPoint="10,20">
                                            <PathFigure.Segments>
                                                <QuadraticBezierSegment Point1="200,200" Point2="300,100"/>
                                            </PathFigure.Segments>
                                        </PathFigure>
                                    </PathGeometry.Figures>
                                </PathGeometry>
                            </Path.Data>
                        </Path>
                        <Path Stroke="Red" StrokeThickness="5">
                            <Path.Data>
                                <PathGeometry>
                                    <PathGeometry.Figures>
                                        <PathFigure StartPoint="10,20">
                                            <PathFigure.Segments>
                                                <PolyQuadraticBezierSegment Points="200,200 300,100 0,200 30,400"/>
                                            </PathFigure.Segments>
                                        </PathFigure>
                                    </PathGeometry.Figures>
                                </PathGeometry>
                            </Path.Data>
                        </Path>
                        <Path Stroke="Red" StrokeThickness="3" Data="M 100,200 C 100,25 400,350 400,175 H 280"/>
                    </StackPanel>
                </ScrollViewer>
            </PivotItem>
            <PivotItem Header="Hexagon">
                <Grid x:Name="HexagonContentPanel" Margin="12,0,12,0">
                    <controls:Hexagon Height="300" Width="300" Stroke="Yellow" StrokeThickness="8" Fill="Red"/>
                </Grid>
            </PivotItem>
            <PivotItem Header="LinearGradientBrush">
                <Path Canvas.Left="15" Canvas.Top="50" Stroke="Black" Data="M 0,0 A 15,5 180 1 1 200,0 L 100,100 L 0,100 Z">
                    <Path.Fill>
                        <LinearGradientBrush StartPoint="0,0" EndPoint="1,0">
                            <GradientStop Offset="0" Color="DarkBlue"/>
                            <GradientStop Offset="1" Color="LightBlue"/>
                        </LinearGradientBrush>
                    </Path.Fill>
                </Path>
            </PivotItem>
            <PivotItem Header="ImageBrush">
                <StackPanel>
                    <Ellipse Height="180" Width="180" Margin="50,0,0,0">
                        <Ellipse.Fill>
                            <ImageBrush ImageSource="Assets/ApplicationIcon.png" Stretch="Fill"/>
                        </Ellipse.Fill>
                    </Ellipse>
                    <TextBlock Text="你好" FontSize="100" FontWeight="Bold">
                        <TextBlock.Foreground>
                            <!--使用图像画刷填充TextBlock的Foreground-->
                            <ImageBrush ImageSource="Assets/AlignmentGrid.png"/>
                            
                        </TextBlock.Foreground>
                    </TextBlock>
                </StackPanel>
            </PivotItem>
            <PivotItem Header="Clip">
                <Grid x:Name="ClipContentPanel" Margin="12,0,12,0">
                    <Canvas Background="White" Margin="20" controls:Clip.ToBounds="True">
                        <Ellipse Fill="Red" Canvas.Top="-10" Canvas.Left="-10" Width="200" Height="200"/>
                    </Canvas>
                </Grid>
            </PivotItem>
            <PivotItem Header="RenderTargetBitmap">
                <!--注册PointerReleased事件用于捕获屏幕的单击操作,并在时间处理程序中生成图片.-->
                <Grid x:Name="root" Background="{ThemeResource ApplicationPageBackgroundThemeBrush}" PointerReleased="root_PointerReleased">
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="*"/>
                    </Grid.RowDefinitions>
                    <StackPanel x:Name="TitlePanel" Margin="12,35,0,28">
                        <TextBlock Text="我的应用程序" FontSize="20"/>
                        <TextBlock Text="点击截屏" FontSize="60"/>
                    </StackPanel>
                    <Grid x:Name="RenderTargetBitmapContentPanel" Grid.Row="1" Margin="12,0,12,0">
                    <!--该图片控件用于展示截图图片效果-->
                        <Image x:Name="img"/>
                    </Grid>
                </Grid>
            </PivotItem>
            <PivotItem Header="SaveBitmapEncoder">
                <Grid x:Name="root1" Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
                    <Grid x:Name="BitmapEncoderContentPanel" Margin="12,0,12,0">
                        <StackPanel>
                            <Button x:Name="bt_save1" Content="存储生成的图片" Click="bt_save_Click_2"/>
                            <Button x:Name="bt_show"
                                    Content="展示存储的图片"
                                    Click="bt_show_Click"/>
                            <ScrollViewer BorderBrush="Red" BorderThickness="2" Height="350">
                                <Image x:Name="img1"/>
                                
                            </ScrollViewer>
                        </StackPanel>
                    </Grid>
                </Grid>
            </PivotItem>
            <PivotItem Header="TranslateTransform">
                <Rectangle Height="50" Width="50" Fill="#CCCCCCFF" Stroke="Blue" StrokeThickness="2" Canvas.Left="100" Canvas.Top="100">
                    <Rectangle.RenderTransform>
                        <TranslateTransform X="50" Y="50"/>
                    </Rectangle.RenderTransform>
                </Rectangle>
            </PivotItem>
            <PivotItem Header="RotateTransform">
                <StackPanel>
                    <Canvas Height="200" Width="200">
                        <!--以Polyline对象的左上角0,0为旋转点将其旋转45°-->
                        <Polyline Points="25,25,0,50,25,75,50,50,25,25,25,0"
                              Stroke="Blue" StrokeThickness="10"
                              Canvas.Left="75" Canvas.Top="50">
                            <Polyline.RenderTransform>
                                <RotateTransform CenterX="0" CenterY="0" Angle="45"/>
                            </Polyline.RenderTransform>
                        </Polyline>
                    </Canvas>
                    <Canvas Height="200" Width="200">
                        <Polyline Points="25,25,0,50,25,75,50,50,25,25,25,0"
                                  Stroke="Blue" StrokeThickness="10" Canvas.Left="75" Canvas.Top="50">
                            <Polyline.RenderTransform>
                                <RotateTransform CenterX="25" CenterY="50" Angle="45"/>
                            </Polyline.RenderTransform>
                        </Polyline>
                    </Canvas>
                    <Canvas Height="200" Width="200">
                        <Rectangle Height="50" Width="50" Fill="#CCCCCCFF" Stroke="Blue" StrokeThickness="2" Canvas.Left="100" Canvas.Top="100">
                            <Rectangle.RenderTransform>
                                <ScaleTransform CenterX="0" CenterY="0" ScaleX="2" ScaleY="2"/>
                            </Rectangle.RenderTransform>
                            
                        </Rectangle>
                        <Rectangle Height="50" Width="50" Fill="#CCCCCCFF" Canvas.Left="100" Canvas.Top="100" Stroke="Blue" StrokeThickness="2">
                            <Rectangle.RenderTransform>
                                <ScaleTransform CenterX="25"
                                                CenterY="25"
                                                ScaleX="2"
                                                ScaleY="2"/>
                            </Rectangle.RenderTransform>
                        </Rectangle>
                    </Canvas>
                    
                    
                </StackPanel>
            </PivotItem>
            <PivotItem Header="SkewTransform">
                <Canvas Width="200" Height="200">
                    <Rectangle Height="50" Width="50" Fill="#CCCCCCFF" Stroke="Blue" StrokeThickness="2" Canvas.Left="100" Canvas.Top="100">
                        <Rectangle.RenderTransform>
                            <SkewTransform CenterX="0" CenterY="0" AngleX="45" AngleY="0"/>
                        </Rectangle.RenderTransform>
                    </Rectangle>
                </Canvas>
            </PivotItem>
            <PivotItem Header="TransformGroup">
                <Button RenderTransformOrigin="0.5,0.5" HorizontalAlignment="Center">Click
                    <Button.RenderTransform>
                        <TransformGroup>
                            <ScaleTransform ScaleY="3"/>
                            <RotateTransform Angle="45"/>
                        </TransformGroup>
                    </Button.RenderTransform>
                </Button>
            </PivotItem>
            <PivotItem Header="MatrixTransform">
                <!--<Button MinWidth="100">Click
                    <Button.RenderTransform>
                        <MatrixTransform x:Name="myMatrixTransform">
                            <MatrixTransform.Matrix>
                                <Matrix/>
                            </MatrixTransform.Matrix>
                        </MatrixTransform>
                    </Button.RenderTransform>
                </Button>-->
            </PivotItem>
            <PivotItem Header="3D">
                <Canvas Background="Black">
                    <Rectangle Width="200" Height="200" Fill="Red">
                        <Rectangle.RenderTransform>
                            <MatrixTransform Matrix="1,-0.5,0,1.0,60,100"/>
                        </Rectangle.RenderTransform>
                    </Rectangle>
                    <Rectangle Width="200" Height="200" Fill="FloralWhite">
                        <Rectangle.RenderTransform>
                            <MatrixTransform Matrix="1.0,0.5,0,1.0,260,0"/>
                        </Rectangle.RenderTransform>
                    </Rectangle>
                    <Rectangle Width="200" Height="200" Fill="Green">
                        <Rectangle.RenderTransform>
                            <MatrixTransform Matrix="1,0.5,-1,0.5,260,200"/>
                        </Rectangle.RenderTransform>
                    </Rectangle>
                </Canvas>
            </PivotItem>
            <PivotItem Header="SanWeiXuanzhuan">
                <Grid x:Name="SanWeiContentPanel" Margin="12,0,12,0">
                    <Grid.RowDefinitions>
                        <RowDefinition Height="*"/>
                        <RowDefinition Height="Auto"/>
                    </Grid.RowDefinitions>
                    <!--旋转的对象-->
                    <TextBlock Grid.Row="0" Text="3D" FontSize="120" Foreground="Red" HorizontalAlignment="Center" VerticalAlignment="Center">
                        <TextBlock.Projection>
                            <PlaneProjection x:Name="planeProjection"/>
                        </TextBlock.Projection>
                    </TextBlock>
                    <StackPanel Grid.Row="1">
                        <StackPanel Orientation="Horizontal">
                            <RadioButton x:Name="rotationRadioButton" Content="Rotation" Checked="rotationRadioButton_Checked"/>
                            <RadioButton x:Name="centerOfRotationRadioButton"
                                         Content="CenterOfRotation" Checked="centerOfRotationRadioButton_Checked"/>
                            
                        </StackPanel>
                        <TextBlock x:Name="infoTextBlock" TextWrapping="Wrap"/>
                        <TextBlock x:Name="xTextBlock" Text="沿着X轴旋转"/>
                        <Slider x:Name="xSlider" Minimum="0" Maximum="100" ValueChanged="xSlider_ValueChanged"/>
                        <TextBlock x:Name="yTextBlock" Text="沿着Y轴旋转"/>
                        <Slider x:Name="ySlider" Minimum="0" Maximum="100" ValueChanged="ySlider_ValueChanged"/>
                        <TextBlock x:Name="zTextBlock" Text="沿着Z轴旋转"/>
                        <Slider x:Name="zSlider" Minimum="0" Maximum="100" ValueChanged="zSlider_ValueChanged"/>

                    </StackPanel>
                </Grid>
            </PivotItem>
            <PivotItem Header="SanweiPingyi">
                <Grid x:Name="SanweiContentPanel" Margin="12,0,12,0">
                    <Grid.RowDefinitions>
                        <RowDefinition Height="*"/>
                        <RowDefinition Height="Auto"/>
                    </Grid.RowDefinitions>
                    <!--平移的对象-->
                    <TextBlock Grid.Row="0" Text="3D" FontSize="120" Foreground="red"
                               HorizontalAlignment="Center" VerticalAlignment="Center">
                        <TextBlock.Projection>
                            <PlaneProjection x:Name="pingyiProjection" RotationX="45"/>
                        </TextBlock.Projection>
                    </TextBlock>
                    <StackPanel Grid.Row="1">
                        <StackPanel Orientation="Horizontal">
                            <RadioButton x:Name="globalRadioButton" Content="GlobalOffset" Checked="rotationRadioButton_Checked"/>
                            <RadioButton x:Name="localRadioButton"
                                         Content="LocalOffset" Checked="centerOfRotationRadioButton_Checked"/>

                        </StackPanel>
                        <TextBlock x:Name="infoTextBlock1" TextWrapping="Wrap"/>
                        <TextBlock x:Name="xTextBlock1" Text="沿着X轴旋转"/>
                        <Slider x:Name="xSlider1" Minimum="0" Maximum="100" ValueChanged="xSlider_ValueChanged1"/>
                        <TextBlock x:Name="yTextBlock1" Text="沿着Y轴旋转"/>
                        <Slider x:Name="ySlider1" Minimum="0" Maximum="100" ValueChanged="ySlider_ValueChanged1"/>
                        <TextBlock x:Name="zTextBlock1" Text="沿着Z轴旋转"/>
                        <Slider x:Name="zSlider1" Minimum="0" Maximum="100" ValueChanged="zSlider_ValueChanged1"/>
                 
                    </StackPanel>
                </Grid>
                
            </PivotItem>
            <PivotItem Header="Storyboard">
                <StackPanel>
                    <Ellipse x:Name="ellipse1" Width="150" Height="150">
                       
                    </Ellipse>
                    <StackPanel.Resources>
                        <Storyboard x:Name="storyboard1">
                            <DoubleAnimation EnableDependentAnimation="True"
                                             Storyboard.TargetName="ellipse1"
                                             Storyboard.TargetProperty="Width"
                                             From="150" To="300" Duration="0:0:3"/>
                        </Storyboard>
                    </StackPanel.Resources>
                </StackPanel>
            </PivotItem>
            <PivotItem Header="myStoryboard">
                <StackPanel Margin="12,0,12,0">
                    <StackPanel.Resources>
                        <Storyboard x:Name="myStoryboard">
                            <DoubleAnimation From="0" To="300" AutoReverse="True"
                                             RepeatBehavior="Forever" Duration="0:0:3"
                                             Storyboard.TargetName="rect" Storyboard.TargetProperty="Width" EnableDependentAnimation="True"/>
                            
                        </Storyboard>
                    </StackPanel.Resources>
                    <Rectangle x:Name="rect" Width="0" Fill="Red" Height="100"/>
                    <Button Content="启动动画" Click="Button_Click_3"/>
                </StackPanel>
            </PivotItem>
            <PivotItem Header="DoubleAnimation">
                <Canvas Loaded="Page_Loaded">
                    <Canvas.Resources>
                        <Storyboard x:Name="storyBoard">
                            <!--对ScaleTransform对象的ScaleY属性应用动画,表示沿着Y轴缩放倍数变化的动画-->
                            <DoubleAnimation Storyboard.TargetName="scacleTransform"
                                             Storyboard.TargetProperty="ScaleY"
                                             From="1" To="2" Duration="0:0:3"
                                             RepeatBehavior="Forever" AutoReverse="True">
                            </DoubleAnimation>
                            
                        </Storyboard>
                        
                    </Canvas.Resources>
                    <Rectangle x:Name="rectangle" Height="50" Width="50" Canvas.Left="75" Canvas.Top="75" Fill="Blue">
                        <Rectangle.RenderTransform>
                            <!--<注意需要对ScaleTransform命名,否则无法定义动画的目标对象>-->
                            <ScaleTransform x:Name="scacleTransform">
                            </ScaleTransform>
                        </Rectangle.RenderTransform>
                    </Rectangle>
                </Canvas>
            </PivotItem>
            <PivotItem Header="ColorAnimation">
                <StackPanel>
                    <StackPanel.Resources>
                        <Storyboard x:Name="storybord1">
                            <ColorAnimation From="Red" To="Yellow"
                                            Storyboard.TargetName="button"
                                            Storyboard.TargetProperty="(Button.Background).(SolidColorBrush.Color)"
                                            Duration="0:0:5">
                            </ColorAnimation>
                        </Storyboard>
                    </StackPanel.Resources>
                    <Button Content="开始动画" Margin="12" Click="Button_Click_4" HorizontalAlignment="Stretch"/>
                    <Button Content="Test" x:Name="button" Margin="12" Click="button_Click_5" HorizontalAlignment="Stretch"/>
                    
                </StackPanel>
            </PivotItem>
            <PivotItem Header="PointAnimation">
                <StackPanel>
                    <StackPanel.Resources>
                        <Storyboard x:Name="storyboard2">
                            <PointAnimation From="50,0" To="100,50" Duration="0:0:3" Storyboard.TargetName="bezierSegment"
                                            Storyboard.TargetProperty="Point3" RepeatBehavior="5" EnableDependentAnimation="True"/>
                        </Storyboard>
                    </StackPanel.Resources>
                    <Button Content="运行动画" Click="Button_Click_6" Margin="12" HorizontalAlignment="Stretch"/>
                    <!--<Data属性由4个BezierSegment曲线和1条直线LineSegment组成>-->
                    <Path Fill="#FF4080FF" HorizontalAlignment="Left" Height="100" Margin="162,164,0,0" Stretch="Fill" VerticalAlignment="Top" Width="100">
                        <Path.Data>
                            <PathGeometry>
                                <PathFigure StartPoint="100,50">
                                    <BezierSegment Point1="100,77.6142" Point2="77.6142,100" Point3="50,100"/>
                                    <BezierSegment Point1="22.3858,100" Point2="0,77.6142" Point3="0,50"/>
                                    <BezierSegment Point1="0,22.3858" Point2="22.3858,0" Point3="50,0"/>
                                    <!--BezierSegment表示是圆右上的弧线,对其终点Point3进行动画处理-->
                                    <BezierSegment x:Name="bezierSegment" Point1="77.6142,0" Point2="100,22.3858" Point3="100,50"/>
                                    <LineSegment Point="50,50"/>
                                </PathFigure>
                            </PathGeometry>
                        </Path.Data>
                    </Path>
                </StackPanel>
            </PivotItem>
            <PivotItem Header="SplineDoubleKeyFrame">
                <Grid x:Name="LayoutRoot" Background="Transparent">
                    <Grid.Resources>
                        <Storyboard x:Name="SplineKeyStoryBoard">
                            <!--对第一个矩形的Canvas.Top属性使用样条关键帧动画-->
                            <DoubleAnimationUsingKeyFrames 
                                Storyboard.TargetName="srect"
                                Storyboard.TargetProperty="(Canvas.Top)"
                                Duration="0:0:10"
                                RepeatBehavior="Forever">
                                <SplineDoubleKeyFrame Value="0" KeyTime="0:0:10"
                                                      KeySpline="0.0,1.0 1.0,0.0"/>
                            </DoubleAnimationUsingKeyFrames>
                            <!--对第一个矩形的Canvas.Left属性使用线性关键帧动画-->
                            <DoubleAnimationUsingKeyFrames
                                Storyboard.TargetName="srect"
                                Storyboard.TargetProperty="(Canvas.Left)"
                                Duration="0:0:10"
                                RepeatBehavior="Forever">
                                <LinearDoubleKeyFrame Value="400" KeyTime="0:0:10"/>
                            </DoubleAnimationUsingKeyFrames>
                            <!--对第二个矩形的Canvas.Top属性使用线性关键帧动画-->
                            <DoubleAnimationUsingKeyFrames 
                                Storyboard.TargetName="srect2"
                                Storyboard.TargetProperty="(Canvas.Top)"
                                Duration="0:0:10"
                                RepeatBehavior="Forever">
                                <LinearDoubleKeyFrame Value="0" KeyTime="0:0:10"/>
                            </DoubleAnimationUsingKeyFrames>
                            <!--对第二个矩形的Canvas.Left属性使用线性关键帧动画-->
                            <DoubleAnimationUsingKeyFrames
                                Storyboard.TargetName="srect2"
                                Storyboard.TargetProperty="(Canvas.Left)"
                                Duration="0:0:10"
                                RepeatBehavior="Forever">
                                <LinearDoubleKeyFrame Value="400" KeyTime="0:0:10"/>
                            </DoubleAnimationUsingKeyFrames>
                        </Storyboard>
                    </Grid.Resources>
                    <Canvas Margin="12,0,12,0">
                     

                            <!--第一个矩形的运动轨迹时采用样条关键帧的方式从左下角向右上角用变化的加速度运动-->
                            <Rectangle x:Name="srect" Width="50" Height="50" Fill="Purple" Canvas.Top="400" Canvas.Left="0"/>

                            <!--第二个矩形的运动轨迹是采用线性关键帧的方式从左下角向右上角匀速运动-->
                            <Rectangle x:Name="srect2" Width="50" Height="50" Fill="Red" Canvas.Top="400" Canvas.Left="0"/>
                            <Button Content="运行动画" Canvas.Top="500" Click="Button_Click_7"/>
                   

                    </Canvas>
                </Grid>
            </PivotItem>
            <PivotItem Header="Discrete">
                <Grid>
                    <Grid.Resources>
                        <Storyboard x:Name="Discretestoryboard">
                            <PointAnimationUsingKeyFrames Storyboard.TargetName="myLinearGradientBrush" Storyboard.TargetProperty="StartPoint" EnableDependentAnimation="True" RepeatBehavior="Forever">
                                <DiscretePointKeyFrame Value="0.1,0.3" KeyTime="0:0:0"/>
                                <DiscretePointKeyFrame Value="0.2,0.4" KeyTime="0:0:1"/>
                                <DiscretePointKeyFrame Value="0.3,0.5" KeyTime="0:0:2"/>
                                <DiscretePointKeyFrame Value="0.4,0.6" KeyTime="0:0:3"/>
                                <DiscretePointKeyFrame Value="0.5,0.7" KeyTime="0:0:4"/>
                                <DiscretePointKeyFrame Value="0.6,0.8" KeyTime="0:0:5"/>
                                <DiscretePointKeyFrame Value="0.7,0.9" KeyTime="0:0:6"/>
                            </PointAnimationUsingKeyFrames>
                        </Storyboard>
                    </Grid.Resources>
                    <StackPanel>
                        <Ellipse x:Name="ellipse" Width="200" Height="200">
                            <Ellipse.Fill>
                                <LinearGradientBrush x:Name="myLinearGradientBrush"
                                                     StartPoint="0,0" EndPoint="1,0">
                                    <LinearGradientBrush.GradientStops>
                                        <GradientStop Color="White" Offset="0.001"/>
                                        <GradientStop Color="Blue" Offset="1"/>
                                    </LinearGradientBrush.GradientStops>
                                </LinearGradientBrush>
                            </Ellipse.Fill>
                        </Ellipse>
                        <Button Content="启动动画" Height="100" Click="Button_Click_8"/>
                    </StackPanel>

                </Grid>
            </PivotItem>
            <PivotItem Header="ChangeBackGroundDiscrete">
                <Grid x:Name="LayoutRoot1" Background="Transparent">
                    <Grid.Resources>
                        <Storyboard x:Name="ChangeBackGroundstoryboard">
                            <ObjectAnimationUsingKeyFrames Storyboard.TargetName="LayoutRoot1" Storyboard.TargetProperty="Background" Duration="0:0:4"
                                                           RepeatBehavior="Forever">
                                <!--在1秒钟时间点上设置背景的画刷为LinearGradientBrush-->
                                <DiscreteObjectKeyFrame KeyTime="0:0:1">
                                    <DiscreteObjectKeyFrame.Value>
                                        <LinearGradientBrush>
                                            <LinearGradientBrush.GradientStops>
                                                <GradientStop Color="Yellow" Offset="0.0"/>
                                                <GradientStop Color="Orange" Offset="0.5"/>
                                                <GradientStop Color="Red" Offset="1.0"/>
                                            </LinearGradientBrush.GradientStops>
                                        </LinearGradientBrush>
                                    </DiscreteObjectKeyFrame.Value>
                                </DiscreteObjectKeyFrame>
                                <!--在2秒钟的时间点上设置背景的画刷为另外一个LinearGradientBrush-->
                                <DiscreteObjectKeyFrame KeyTime="0:0:2">
                                    <DiscreteObjectKeyFrame.Value>
                                        <LinearGradientBrush StartPoint="0,0" EndPoint="1,0">
                                            <LinearGradientBrush.GradientStops>
                                                <GradientStop Color="White" Offset="0.0"/>
                                                <GradientStop Color="MediumBlue" Offset="0.5"/>
                                                <GradientStop Color="Black" Offset="1.0"/>
                                            </LinearGradientBrush.GradientStops>
                                        </LinearGradientBrush>
                                    </DiscreteObjectKeyFrame.Value>
                                </DiscreteObjectKeyFrame>
                            </ObjectAnimationUsingKeyFrames>
                        </Storyboard>
                    </Grid.Resources>
                    <Grid x:Name="ChangeBackGroundContentPanel" Margin="12,0,12,0">
                        <Button Content="启动动画" Height="100" Click="Button_Click_9"/>
                    </Grid>
                </Grid>
            </PivotItem>
            <PivotItem Header="BackEase">
                <Grid Background="Transparent">
                    <Grid.Resources>
                        <Storyboard x:Name="Easestoryboard">
                            <!--第一个椭圆的动画-->
                            <DoubleAnimation From="1" To="2" Duration="0:0:3" Storyboard.TargetName="ellipselScaleTransform"
                                             Storyboard.TargetProperty="ScaleX">
                                <DoubleAnimation.EasingFunction>
                                    <BackEase Amplitude="0.3" EasingMode="EaseInOut"/>
                                </DoubleAnimation.EasingFunction>
                            </DoubleAnimation>
                            <DoubleAnimation From="1" To="2" Duration="0:0:3" Storyboard.TargetName="ellipselScaleTransform"
                                             Storyboard.TargetProperty="ScaleY">
                                <DoubleAnimation.EasingFunction>
                                    <BackEase Amplitude="0.3" EasingMode="EaseInOut"/>
                                </DoubleAnimation.EasingFunction>
                            </DoubleAnimation>
                            <!--第二个椭圆的动画-->
                            <DoubleAnimation From="0" To="400" Duration="0:0:3" Storyboard.TargetName="ellipse4"
                                             Storyboard.TargetProperty="(Canvas.Left)">
                                
                            </DoubleAnimation>
                            <DoubleAnimation From="400" To="0" Duration="0:0:3" Storyboard.TargetName="ellipse4"
                                             Storyboard.TargetProperty="(Canvas.Top)">
                                <DoubleAnimation.EasingFunction>
                                    <BackEase Amplitude="0.3" EasingMode="EaseInOut"/>
                                </DoubleAnimation.EasingFunction>
                            </DoubleAnimation>
                        </Storyboard>
                    </Grid.Resources>
                    <Canvas Margin="12,0,12,0">
                        <!--第一个椭圆展示了BackEase的放大动画效果-->
                        <Ellipse Name="ellipse3" Width="80" Height="80" Fill="Blue">
                            <Ellipse.RenderTransform>
                                <ScaleTransform x:Name="ellipselScaleTransform">
                                    
                                </ScaleTransform>
                            </Ellipse.RenderTransform>
                        </Ellipse>
                        <!--第二个椭圆展示了BackEase的运动轨迹-->
                        <Ellipse x:Name="ellipse4" Fill="Red" Width="80" Height="80" Canvas.Left="0" Canvas.Top="400"/>
                        <Button Margin="0,500,0,0" Content="启动动画" Height="80" Click="Button_Click_10"/>
                    </Canvas>
                </Grid>
            </PivotItem>
            <PivotItem Header="BounceEase">
                <Grid Background="Transparent">
                    <Grid.Resources>
                        <Storyboard x:Name="Bouncestoryboard">
                            <!--第一个椭圆的动画-->
                            <DoubleAnimation From="0" To="200" Duration="0:0:3" EnableDependentAnimation="True"
                                             Storyboard.TargetName="ellipse5"
                                             Storyboard.TargetProperty="Width">
                                <DoubleAnimation.EasingFunction>
                                    <BounceEase Bounces="2" EasingMode="EaseOut" Bounciness="2"/>
                                </DoubleAnimation.EasingFunction>
                                
                            </DoubleAnimation>
                            <DoubleAnimation From="80" To="200" Duration="0:0:3" EnableDependentAnimation="True"
                                             Storyboard.TargetName="ellipse5"
                                             Storyboard.TargetProperty="Height">
                                <DoubleAnimation.EasingFunction>
                                    <BounceEase Bounces="2" EasingMode="EaseOut" Bounciness="2"/>
                                </DoubleAnimation.EasingFunction>
                   
                            </DoubleAnimation>
                            <!--第二个椭圆的动画-->
                            <DoubleAnimation From="0" To="400" Duration="0:0:3" Storyboard.TargetName="ellipse6"         Storyboard.TargetProperty="(Canvas.Left)">
                            </DoubleAnimation>
                            <DoubleAnimation From="400" To="0" Duration="0:0:3" Storyboard.TargetName="ellipse6"
                                             Storyboard.TargetProperty="(Canvas.Top)">
                                <DoubleAnimation.EasingFunction>
                                    <BounceEase Bounces="2" EasingMode="EaseOut" Bounciness="2"/>
                                </DoubleAnimation.EasingFunction>
                            </DoubleAnimation>
                        </Storyboard>
                    </Grid.Resources>
                    <Canvas Margin="12,0,12,0">
                        <!--第一个椭圆展示了BounceEase的动画效果-->
                        <Ellipse Name="ellipse5" Width="80" Height="80"
                                 Fill="Blue"/>
                        <!--第二个椭圆展示了BounceEase的运动轨迹-->
                        <Ellipse x:Name="ellipse6" Fill="Red" Width="80"
                                 Height="80" Canvas.Left="0" Canvas.Top="400"/>
                        <Button Margin="0,500,0,0" Content="启动动画" Height="80" Click="Button_Click_11"/>
                    </Canvas>
                </Grid>
            </PivotItem>
            <PivotItem Header="CircleEase">
                <Grid Background="Transparent">
                    <Grid.Resources>
                        <Storyboard x:Name="CircleEasestoryboard">
                            <!--第一个椭圆的动画-->
                            <DoubleAnimation From="80" To="400" Duration="0:0:3" EnableDependentAnimation="True"
                                             Storyboard.TargetName="ellipse7"
                                             Storyboard.TargetProperty="Width">
                                <DoubleAnimation.EasingFunction>
                                    <CircleEase EasingMode="EaseOut"/>
                                </DoubleAnimation.EasingFunction>
                                
                            </DoubleAnimation>
                            <!--第二个椭圆的动画-->
                            <DoubleAnimation From="0" To="400" Duration="0:0:3" Storyboard.TargetName="ellipse8"
                                             Storyboard.TargetProperty="(Canvas.Left)">
                            </DoubleAnimation>
                            <DoubleAnimation From="400" To="0" Duration="0:0:3" Storyboard.TargetName="ellipse8"
                                             Storyboard.TargetProperty="(Canvas.Top)">
                                <DoubleAnimation.EasingFunction>
                                    <CircleEase EasingMode="EaseOut"/>
                                </DoubleAnimation.EasingFunction>
                            </DoubleAnimation>
                        </Storyboard>
                    </Grid.Resources>
                    <Canvas Margin="12,0,12,0">
                        <!--第一个椭圆展示了CircleEase的动画效果-->
                        <Ellipse x:Name="ellipse7" Width="80" Height="80" Fill="Blue"/>
                        <!--第二个椭圆展示了CircleEase的运动轨迹-->
                        <Ellipse x:Name="ellipse8" Fill="Red" Width="80" Height="80" Canvas.Left="0" Canvas.Top="400"/>
                        <Button Margin="0,500,0,0" Content="启动动画" Height="80" Click="Button_Click_12"/>
                    </Canvas>
                </Grid>
            </PivotItem>
            <PivotItem Header="CubicEase">
                <Grid Background="Transparent">
                    <Grid.Resources>
                        <Storyboard x:Name="Cubicstoryboard">
                            <!--第一个椭圆的动画-->
                            <DoubleAnimation From="0" To="180" Duration="0:0:3" Storyboard.TargetName="ellipselPlaneProjection"
                                             Storyboard.TargetProperty="RotationX">
                                <DoubleAnimation.EasingFunction>
                                    <CubicEase EasingMode="EaseInOut"/>
                                </DoubleAnimation.EasingFunction>
                            </DoubleAnimation>
                            <!--第二个椭圆的动画-->
                            <DoubleAnimation From="0" To="400" Duration="0:0:3" Storyboard.TargetName="ellipse10"
                                             Storyboard.TargetProperty="(Canvas.Left)">
                            </DoubleAnimation>
                            <DoubleAnimation From="400" To="200" Duration="0:0:3" Storyboard.TargetName="ellipse10"
                                             Storyboard.TargetProperty="(Canvas.Top)">
                                <DoubleAnimation.EasingFunction>
                                    <CubicEase EasingMode="EaseInOut"/>
                                </DoubleAnimation.EasingFunction>
                            </DoubleAnimation>
                        </Storyboard>
                    </Grid.Resources>
                    <Canvas Margin="12,0,12,0">
                        <!--第一个椭圆展示了CubicEase的动画效果-->
                        <Ellipse x:Name="ellipse9" Width="200" Height="200" Fill="Blue">
                            <Ellipse.Projection>
                                <PlaneProjection RotationX="0" x:Name="ellipselPlaneProjection">
                                </PlaneProjection>
                            </Ellipse.Projection>
                        </Ellipse>
                        <!--第二个椭圆展示了CubicEase的运动轨迹-->
                        <Ellipse x:Name="ellipse10" Fill="Red" Width="80"
                                 Height="80" Canvas.Left="0" Canvas.Top="400"/>
                        <Button Margin="0,500,0,0" Content="启动动画" Height="80" Click="Button_Click_13"/>
                    </Canvas>
                </Grid>
            </PivotItem>
            <PivotItem Header="ElasticEase">
                <Grid Background="Transparent">
                    <Grid.Resources>
                        <Storyboard x:Name="Elasticstoryboard">
                            <!--线条的动画-->
                            <DoubleAnimation From="0" To="400" Duration="0:0:3" EnableDependentAnimation="True"
                                             Storyboard.TargetName="line1"
                                             Storyboard.TargetProperty="Y2">
                                <DoubleAnimation.EasingFunction>
                                    <ElasticEase EasingMode="EaseOut" Oscillations="7"/>
                                </DoubleAnimation.EasingFunction>
                            </DoubleAnimation>
                            <!--第一个椭圆的动画-->
                            <DoubleAnimation From="0" To="400" Duration="0:0:3" Storyboard.TargetName="ellipse11"
                                             Storyboard.TargetProperty="(Canvas.Top)">
                                <DoubleAnimation.EasingFunction>
                                    <ElasticEase EasingMode="EaseOut" Oscillations="7"/>
                                </DoubleAnimation.EasingFunction>
                            </DoubleAnimation>
                            <!--第二个椭圆的动画-->
                            <DoubleAnimation From="0" To="400" Duration="0:0:3" Storyboard.TargetName="ellipse12"
                                             Storyboard.TargetProperty="(Canvas.Left)">
                                
                            </DoubleAnimation>
                            <DoubleAnimation From="400" To="200" Duration="0:0:3" Storyboard.TargetName="ellipse12"
                                             Storyboard.TargetProperty="(Canvas.Top)">
                                <DoubleAnimation.EasingFunction>
                                    <ElasticEase EasingMode="EaseOut" Oscillations="7"/>
                                </DoubleAnimation.EasingFunction>
                            </DoubleAnimation>
                        </Storyboard>
                    </Grid.Resources>
                    <Canvas Margin="12,0,12,0">
                        <!--线条展示了ElasticEase的动画效果-->
                        <Line x:Name="line1" X1="50" Y1="0" X2="50" Y2="50"
                              Stroke="Blue" StrokeThickness="10" Fill="Blue"/>
                        <!--第一个椭圆展示了ElasticEase的动画效果-->
                        <Ellipse Name="ellipse11" Fill="Blue" Width="100" Height="100"/>
                        <!--第二个椭圆展示了ElasticEase的运动轨迹-->
                        <Ellipse x:Name="ellipse12" Fill="Red" Width="80"
                                 Height="80" Canvas.Left="0" Canvas.Top="400"/>
                        <Button Margin="0,500,0,0" Content="启动动画" Height="80" Click="Button_Click_14"/>
                    </Canvas>
                </Grid>
            </PivotItem>
            <PivotItem Header="ExponentialEase">
                <Grid Background="Transparent">
                    <Grid.Resources>
                        <Storyboard x:Name="ExponentialStoryboard">
                            <!--第一个椭圆的动画-->
                            <DoubleAnimation From="0" To="400" Duration="0:0:3" Storyboard.TargetName="ellipse13"
                                             Storyboard.TargetProperty="(Canvas.Top)">
                                <DoubleAnimation.EasingFunction>
                                    <ExponentialEase Exponent="10" EasingMode="EaseIn"/>
                                </DoubleAnimation.EasingFunction>
                            </DoubleAnimation>
                            <!--第二个椭圆的动画-->
                            <DoubleAnimation From="0" To="400" Duration="0:0:3" Storyboard.TargetName="ellipse14"
                                             Storyboard.TargetProperty="(Canvas.Left)">
                                
                            </DoubleAnimation>
                            <DoubleAnimation From="400" To="200" Duration="0:0:3" Storyboard.TargetName="ellipse14"
                                             Storyboard.TargetProperty="(Canvas.Top)">
                                <DoubleAnimation.EasingFunction>
                                    <ExponentialEase Exponent="10" EasingMode="EaseIn"/>
                                </DoubleAnimation.EasingFunction>
                            </DoubleAnimation>
                        </Storyboard>
                    </Grid.Resources>
                    <Canvas Margin="12,0,12,0">
                        <!--第一个椭圆展示了ExponentialEase的动画效果-->
                        <Ellipse x:Name="ellipse13" Width="50" Height="50" Fill="Blue"/>
                        <!--第二个椭圆展示了ExponentialEase的运动轨迹-->
                        <Ellipse x:Name="ellipse14" Fill="Red" Width="80"
                                 Height="80" Canvas.Left="0" Canvas.Top="400"/>
                        <Button Margin="0,500,0,0" Content="启动动画" Height="80" Click="Button_Click_15"/>
                    </Canvas>
                </Grid>
            </PivotItem>
            <PivotItem Header="PowerEase">
                <Grid Background="Transparent">
                    <Grid.Resources>
                        <Storyboard x:Name="powerStoryboard">
                            <!--第一个椭圆的动画-->
                            <DoubleAnimation From="0" To="400" Duration="0:0:3" Storyboard.TargetName="ellipse15"
                                             Storyboard.TargetProperty="(Canvas.Top)">
                                <DoubleAnimation.EasingFunction>
                                    <PowerEase EasingMode="EaseIn" x:Name="powerEase1"/>
                                    
                                </DoubleAnimation.EasingFunction>
                            </DoubleAnimation>
                            <!--第二个椭圆的动画-->
                            <DoubleAnimation From="0" To="400" Duration="0:0:3" Storyboard.TargetName="ellipse16"
                                             Storyboard.TargetProperty="(Canvas.Left)">
                            </DoubleAnimation>
                            <DoubleAnimation From="400" To="200" Duration="0:0:3" Storyboard.TargetName="ellipse16"
                                             Storyboard.TargetProperty="(Canvas.Top)">
                                <DoubleAnimation.EasingFunction>
                                    <PowerEase EasingMode="EaseIn" x:Name="powerEase2"/>
                                </DoubleAnimation.EasingFunction>
                            </DoubleAnimation>
                        </Storyboard>
                    </Grid.Resources>
                    <Canvas Margin="12,0,12,0">
                        <Ellipse Name="ellipse15" Width="50" Height="50" Fill="Blue"/>
                        <Ellipse x:Name="ellipse16" Fill="Red" Width="80"
                                 Height="80" Canvas.Left="0" Canvas.Top="400"/>
                        <Button Margin="0,500,0,0" Content="启动动画" Height="80" Click="Button_Click_16"/>
                        <Slider x:Name="powerSlider" Margin="200,500,0,0"
                                Width="200" Background="Red" Value="50" Maximum="100" Minimum="0"/>
                    </Canvas>
                </Grid>
            </PivotItem>
            <PivotItem Header="SineEase">
                <Grid Background="Transparent">
                    <Grid.Resources>
                        <Storyboard x:Name="SineStoryboard">
                            <DoubleAnimation From="0" To="400" Duration="0:0:3" Storyboard.TargetName="ellipse17"
                                             Storyboard.TargetProperty="(Canvas.Top)">
                                <DoubleAnimation.EasingFunction>
                                    <SineEase EasingMode="EaseIn"/>

                                </DoubleAnimation.EasingFunction>
                            </DoubleAnimation>
                            <DoubleAnimation From="0" To="400" Duration="0:0:3" Storyboard.TargetName="ellipse18"
                                             Storyboard.TargetProperty="(Canvas.Left)">
                                
                            </DoubleAnimation>
                            <DoubleAnimation From="400" To="200" Duration="0:0:3" Storyboard.TargetName="ellipse18"
                                             Storyboard.TargetProperty="(Canvas.Top)">
                                <DoubleAnimation.EasingFunction>
                                    <SineEase EasingMode="EaseIn"/>
                                </DoubleAnimation.EasingFunction>
                                
                            </DoubleAnimation>
                        </Storyboard>
                    </Grid.Resources>
                    <Canvas Margin="12,0,12,0">
                        <Ellipse Name="ellipse17" Width="50" Height="50" Fill="Blue"/>
                        <Ellipse x:Name="ellipse18" Fill="Red" Width="80"
                                 Height="80" Canvas.Left="0" Canvas.Top="400"/>
                        <Button Margin="0,500,0,0" Content="启动动画" Height="80" Click="Button_Click_17"/>
                    </Canvas>
                </Grid>
            </PivotItem>
            <PivotItem Header="JyzEase">
                <Canvas Background="Gray" PointerMoved="Canvas_PointerMoved">
                    <Rectangle x:Name="prectangle" Height="50" Width="100"
                               RadiusX="12.5" RadiusY="12.5">
                        <Rectangle.Fill>
                            <LinearGradientBrush>
                                <GradientStop Color="Black" Offset="0"/>
                                <GradientStop Color="White" Offset="0.5"/>
                                <GradientStop Color="Black" Offset="1"/>
                            </LinearGradientBrush>
                        </Rectangle.Fill>
                    </Rectangle>
                </Canvas>
            </PivotItem>
            <PivotItem Header="TwoAnimationCompare">
                <StackPanel>
                    <StackPanel.Resources>
                        <Storyboard x:Name="heightStoryboard">
                            <!--针对Height属性的动画-->
                            <DoubleAnimation Storyboard.TargetName="rectangle1" Storyboard.TargetProperty="Height"
                                             RepeatBehavior="Forever"
                                             EnableDependentAnimation="True"
                                             From="100" To="200" Duration="0:0:2">
                            </DoubleAnimation>
                        </Storyboard>
                        <Storyboard x:Name="scaleTransformStoryboard">
                            <!--针对ScaleTransform的ScaleY属性的动画-->
                            <DoubleAnimation Storyboard.TargetName="scaleTransform1" Storyboard.TargetProperty="ScaleY" RepeatBehavior="Forever" From="1" To="2" Duration="0:0:2">
                            </DoubleAnimation>
                        </Storyboard>
                    </StackPanel.Resources>
                    <Button Content="阻塞UI线程" Click="Button_Click_18"/>
                    <Button x:Name="heightAnimationButton" Content="Height属性动画" Click="heightAnimationButton_Click"/>
                    <Button x:Name="scaleTransformAnimationButton" Content="ScaleTransform属性动画" Click="scaleTransformAnimationButton_Click"/>
                    <Rectangle Height="100" Fill="Blue" x:Name="rectangle1">
                        <Rectangle.RenderTransform>
                            <ScaleTransform x:Name="scaleTransform1"/>
                        </Rectangle.RenderTransform>
                    </Rectangle>
                </StackPanel>
            </PivotItem>
            <PivotItem Header="StarDown">
                <Grid>
                    <Canvas x:Name="myCanvas" HorizontalAlignment="Stretch" VerticalAlignment="Stretch">

                    </Canvas>
                    <Button x:Name="btn" VerticalAlignment="Bottom" Content="开始星星飘落" Click="btn_Click"/>
                </Grid>
            </PivotItem>
            <PivotItem Header="Style">
                <StackPanel>
                    <StackPanel.Resources>
                        <Style x:Key="commonStyle" TargetType="Button">
                            <Setter Property="Width" Value="200"/>
                            <Setter Property="Height" Value="100"/>
                            <Setter Property="FontSize" Value="20"/>
                            <Setter Property="Foreground" Value="Green"/>
                            <Setter Property="Background" Value="Red"/>
                            <Setter Property="FontFamily" Value="Arial"/>
                        </Style>
                        <Style x:Key="commonstyle2" TargetType="FrameworkElement">
                            <Setter Property="Width" Value="200"/>
                            <Setter Property="Height" Value="100"/>
                        </Style>
                    </StackPanel.Resources>
                    <Button Content="按钮1" Style="{StaticResource commonStyle}"/>
                    <Button Content="按钮2" Style="{StaticResource commonStyle}"/>
                    <Button Content="按钮3" Style="{StaticResource commonStyle}"/>
                    <TextBlock Text="AAA" Style="{StaticResource commonstyle2}"/>
                </StackPanel>
            </PivotItem>
            <PivotItem Header="ControlTemplate">
                <Button Content="Hello World">
                    <Button.Template>
                        <ControlTemplate>
                            <Grid>
                                <Ellipse Width="{TemplateBinding Button.Width}" Height="{TemplateBinding Height}"
                                         Fill="{TemplateBinding Button.Background}" Stroke="Red"/>
                                <TextBlock Margin="5,0,0,0" FontSize="50"
                                           VerticalAlignment="Center" HorizontalAlignment="Center" Text="{TemplateBinding Button.Content}"/>
                                <TextBlock FontSize="50" Foreground="Red" VerticalAlignment="Center" HorizontalAlignment="Center"
                                           Text="{TemplateBinding Button.Content}"/>
                            </Grid>
                        </ControlTemplate>
                    </Button.Template>
                </Button>
            </PivotItem>
            <PivotItem Header="ContentPresenter">
                <Button>
                    <Button.Template>
                        <ControlTemplate>
                            <Grid>
                                <ContentPresenter HorizontalAlignment="{TemplateBinding HorizontalAlignment}" VerticalAlignment="{TemplateBinding VerticalAlignment}"/>
                            </Grid>
                        </ControlTemplate>
                    </Button.Template>
                    <Button.Content>
                        <Rectangle Fill="Red" Height="50" Width="50"/>
                    </Button.Content>
                </Button>
            </PivotItem>
            <PivotItem Header="VisualStateManager">
                <Button Content="Hello World" LostFocus="Button_LostFocus" Tapped="Button_Tapped">
                    <Button.Template>
                        <ControlTemplate TargetType="Button">
                            <Border>
                                <VisualStateManager.VisualStateGroups>
                                    <VisualStateGroup x:Name="CommonStates">
                                        <VisualStateGroup.Transitions>
                                            <VisualTransition From="Test1" To="Test2" GeneratedDuration="0:0:1.5">
                                                <Storyboard>
                                                    
                                                </Storyboard>
                                            </VisualTransition>
                                        </VisualStateGroup.Transitions>
                                        <!--创建状态Test1把Border背景的颜色改成红色-->
                                        <VisualState x:Name="Test1">
                                            <Storyboard>
                                                <ColorAnimation Storyboard.TargetName="BorderBrush" Storyboard.TargetProperty="Color" To="Red"/>
                                            </Storyboard>
                                        </VisualState>
                                        <!--创建状态Test2把Border背景的颜色改成蓝色-->
                                        
                                            <VisualState x:Name="Test2">
                                            <Storyboard>
                                                <ColorAnimation Storyboard.TargetName="BorderBrush" Storyboard.TargetProperty="Color" To="Blue"/>
                                            </Storyboard>
                                        </VisualState>
                                    </VisualStateGroup>
                                </VisualStateManager.VisualStateGroups>

                                <Border.Background>
                                    <!--定义Border背景的颜色,用于测试不同状态的显示效果-->
                                    <SolidColorBrush x:Name="BorderBrush" Color="Black"/>

                                </Border.Background>
                                <Grid>
                                    <Ellipse x:Name="ellipse19" Width="{TemplateBinding Button.Width}" Height="{TemplateBinding Height}" Fill="{TemplateBinding Button.Background}" Stroke="Red"/>
                                    <TextBlock Margin="5,0,0,0" FontSize="50" VerticalAlignment="Center" HorizontalAlignment="Center"
                                               Text="{TemplateBinding Button.Content}"/>
                                    <TextBlock FontSize="50" Foreground="Red" VerticalAlignment="Center" HorizontalAlignment="Center" Text="{TemplateBinding Button.Content}"/>
                                </Grid>
                            </Border>
                        </ControlTemplate>
                    </Button.Template>
                </Button>
            </PivotItem>
            <PivotItem Header="DataTemplate">
                <Button Content="Hello World" Width="100" LostFocus="Button_LostFocus" Tapped="Button_Tapped">
                    <Button.Template>
                        <ControlTemplate TargetType="Button">
                            <Border>
                                <VisualStateManager.VisualStateGroups>
                                    <VisualStateGroup>
                                        <VisualStateGroup.Transitions>
                                            <!--状态Test1转换为状态Test2的颜色变化动画-->
                                            <VisualTransition From="Test1" To="Test2" GeneratedDuration="0:0:1.5">
                                                <Storyboard>
                                                    
                                                </Storyboard>
                                            </VisualTransition>
                                           
                                        </VisualStateGroup.Transitions>
                                        <!--创建状态Test1把Border背景的颜色改成红色-->
                                        <VisualState x:Name="Test3">
                                            <Storyboard>
                                                <ColorAnimation Storyboard.TargetName="BorderBrush1" Storyboard.TargetProperty="Color" To="Red"/>
                                            </Storyboard>
                                            
                                        </VisualState>
                                        <!--创建状态Test2把Border背景的颜色改成蓝色-->
                                        <VisualState x:Name="Test4">
                                            <Storyboard>
                                                <ColorAnimation Storyboard.TargetName="BorderBrush1" Storyboard.TargetProperty="Color" To="Blue"/>
                                            </Storyboard>
                                        </VisualState>
                                        
                                    </VisualStateGroup>
                                </VisualStateManager.VisualStateGroups>

                                <Border.Background>
                                    <SolidColorBrush x:Name="BorderBrush1"
                                                     Color="Black"/>
                                </Border.Background>
                                <Grid>
                                    <Ellipse x:Name="ellipse20" Width="{Binding Width}" Height="{Binding Height}"
                                             Fill="{Binding Background}"
                                             Stroke="Red"/>
                                    <TextBlock Margin="5,0,0,0" FontSize="50" VerticalAlignment="Center" HorizontalAlignment="Center"
                                               Text="{Binding}"/>
                                    <TextBlock FontSize="50" Foreground="Red" VerticalAlignment="Center" HorizontalAlignment="Center" Text="{Binding}"/>
                                    
                                </Grid>
                            </Border>
                        </ControlTemplate>
                    </Button.Template>
                </Button>
            </PivotItem>
            <PivotItem Header="DataTemplate1">
                <StackPanel>
                    
                    <Button x:Name="singlePersonButton" Click="singlePersonButton_Click"  ContentTemplate="{StaticResource PersonNameDataTemplate}"/>
                    
                </StackPanel>
            </PivotItem>
            <PivotItem Header="DataTemplate2">
                <ItemsControl x:Name="itemsControl" ItemTemplate="{StaticResource PersonNameDataTemplate}"/>
            </PivotItem>
            <PivotItem Header="DataTemplate3">
                <ItemsControl x:Name="itemsControl1" ItemTemplate="{StaticResource PersonNameDataTemplate}" Style="{StaticResource ItemsControlStyle}"/>
            </PivotItem>
            <PivotItem Header="DataTemplate4">
                <ListBox x:Name="listbox" ItemTemplate="{StaticResource dataTemplateDefaultKey}"/>
            </PivotItem>
            <PivotItem Header=" Binding">
                <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
                    <TextBlock Text="{Binding Title}" Margin="12,100,0,28"
                               FontSize="50"/>
                </Grid>
            </PivotItem>
            <PivotItem Header="ElementNameBinding">
                <StackPanel Margin="12,0,12,0">
                    <TextBlock FontSize="25" Text="圆形的半径会根据slider控件的值而改变"/>
                    <Slider Name="slider1" Value="50" Maximum="400"/>
                    <TextBlock FontSize="25" Text="半径为:"/>
                    <TextBlock Name="txtblk" Text="{Binding ElementName=slider1,Path=Value}" FontSize="48"/>
                    <Ellipse Height="{Binding ElementName=slider,Path=Value}" Width="{Binding ElementName=slider,Path=Value}" Fill="Red" Stroke="Black" StrokeThickness="1"/>
                </StackPanel>
            </PivotItem>
            <PivotItem Header="BindingMode">
                <StackPanel Margin="12,0,12,0">
                    <Slider x:Name="slider" Value="50" Maximum="400"/>
                    <!--OneTime-->
                    <TextBlock FontSize="25" Height="41" Text="OneTime" VerticalAlignment="Top" Width="112"/>
                    <TextBox Height="72" Text="{Binding ElementName=slider,Path=Value,Mode=OneTime}" Width="269"/>
                    <TextBlock FontSize="25" Height="46" Text="OneWay" VerticalAlignment="Top" Width="99"/>
                    <!--OneWay-->
                    <TextBox Height="72" Text="{Binding ElementName=slider,Path=Value,Mode=OneWay}" Width="269"/>
                    <TextBlock Height="40" FontSize="25" Text="TwoWay" VerticalAlignment="Top" Width="94"/>
                    <!--TwoWay-->
                    <TextBox Height="72" Text="{Binding ElementName=slider,Path=Value,Mode=TwoWay}" Width="268"/>
                    <TextBlock FontSize="25" Height="43" Text="slider的值:"/>
                    <TextBlock FontSize="25" Height="43" Text="{Binding ElementName=slider,Path=Value}" Width="185"/>
                    
                </StackPanel>
            </PivotItem>
            <PivotItem Header="INotifyPropertyChanged">
                <StackPanel Margin="12,0,12,0">
                    <TextBlock Text="{Binding Title}" FontSize="50"/>
                    <Button Content="改变数据源的数据" Click="Button_Click_19"/>
                </StackPanel>
            </PivotItem>
            <PivotItem Header="IValueConverter">
                <Grid>
                    <StackPanel Margin="12,0,12,0" DataContext="{StaticResource clock}">
                        <TextBlock FontSize="30" Text="{Binding Hour,Converter={StaticResource booleanToDayString}}"/>
                        <TextBlock FontSize="30" Text="现在的时间是:"/>
                        <TextBlock FontSize="20" Text="{Binding Hour}"/>
                        <TextBlock FontSize="20" Text="小时"/>
                        <TextBlock FontSize="20" Text="{Binding Minute}"/>
                        <TextBlock FontSize="20" Text="分钟"/>
                        <TextBlock FontSize="20" Text="{Binding Second}"/>
                        <TextBlock FontSize="20" Text="秒"/>
                    </StackPanel>
                </Grid>
            </PivotItem>
            <PivotItem Header="BindingItemTemplate">
                <ListView x:Name="listviewItemtemplate">
                    <ListView.ItemTemplate>
                        <!--列表的ItemTemplate属性是一个DataTemplate类型-->
                        <!--创建一个DataTemplate的元素对象-->
                        <DataTemplate>
                            <StackPanel Orientation="Horizontal" Background="Gray" Margin="10">
                                <!--绑定Food类的IconUri属性-->
                                <Image Source="{Binding IconUri}" Stretch="None"/>
                                <TextBlock Text="{Binding Name}" FontSize="40" Margin="24 0 24 0"/>
                                <TextBlock Text="{Binding Description}" FontSize="20"/>
                            </StackPanel>
                        </DataTemplate>
                    </ListView.ItemTemplate>
                </ListView>
            </PivotItem>
            <PivotItem Header="ObservableCollection">
                <Grid>
                    <Grid.RowDefinitions>
                        <RowDefinition Height="*"/>
                        <RowDefinition Height="*"/>
                    </Grid.RowDefinitions>
                    <StackPanel Margin="12,60,0,28">
                        <Button Content="AddItem" Click="Button_Click_20"/>
                        <Button Content="RemoveItem" Click="Button_Click_21"/>
                        
                    </StackPanel>
                    <Grid Margin="12,0,12,0" Grid.Row="1">
                        <ListView x:Name="Obser">
                            <ListView.ItemTemplate>
                                <DataTemplate>
                                    <StackPanel Orientation="Horizontal" Margin="10">
                                        <TextBlock Text="{Binding OrderId}" FontSize="30"/>
                                        <TextBlock Text="{Binding OrderName}" FontSize="30" Width="280"/>
                                    </StackPanel>
                                </DataTemplate>
                            </ListView.ItemTemplate>
                        </ListView>
                    </Grid>
                </Grid>
            </PivotItem>
            <PivotItem Header="IList">
                <ListView x:Name="Ilist">
                    <ListView.ItemTemplate>
                        <DataTemplate>
                            <StackPanel Orientation="Horizontal" Margin="10">
                                <TextBlock Text="{Binding OrderId}" FontSize="30"/>
                                <TextBlock Text="{Binding OrderName}" FontSize="30" Width="280"/>
                            </StackPanel>
                        </DataTemplate>
                    </ListView.ItemTemplate>
                </ListView>
            </PivotItem>
            <PivotItem Header="ItemsControl">
                <ItemsControl x:Name="itemsControl2">
                    <ItemsControl.ItemTemplate>
                        <DataTemplate>
                            <StackPanel Orientation="Horizontal">
                                <TextBlock Text="{Binding FirstName}" FontSize="30"/>
                                <TextBlock Text="{Binding LastName}" FontSize="30" Margin="30,0,0,0"/>
                                
                            </StackPanel>
                        </DataTemplate>
                    </ItemsControl.ItemTemplate>
                </ItemsControl>
            </PivotItem>
            <PivotItem Header="ScrollItemsControl">
                <ItemsControl x:Name="ItemsControl3">
                    <ItemsControl.Template>
                        <ControlTemplate TargetType="ItemsControl">
                            <ScrollViewer>
                                <ItemsPresenter/>
                            </ScrollViewer>
                        </ControlTemplate>
                    </ItemsControl.Template>
                    <ItemsControl.ItemTemplate>
                        <DataTemplate>
                            <StackPanel Orientation="Horizontal">
                                <TextBlock Text="{Binding FirstName}" FontSize="30"/>
                                <TextBlock Text="{Binding LastName}"
                                           FontSize="30" Margin="30,0,0,0"/>

                            </StackPanel>
                        </DataTemplate>
                    </ItemsControl.ItemTemplate>
                </ItemsControl>
            </PivotItem>
            <PivotItem Header="ListBox">
                <ListBox ItemsSource="{Binding Items}" SelectionChanged="ListBox_SelectionChanged">
                    <ListBox.Template>
                        <ControlTemplate TargetType="ItemsControl">
                            <ScrollViewer>
                                <StackPanel>
                                    <ItemsPresenter/>
                                    <Button Content="加载更多" Click="Button_Click_22" HorizontalAlignment="Center" Margin="12"/>
                                    
                                </StackPanel>
                            </ScrollViewer>
                        </ControlTemplate>
                    </ListBox.Template>
                    <ListBox.ItemTemplate>
                        <DataTemplate>
                            <StackPanel Orientation="Horizontal">
                                <TextBlock Text="{Binding FirstName}" FontSize="30"/>
                                <TextBlock Text="{Binding LastName}" FontSize="30" Margin="30,0,0,0"/>
                                
                            </StackPanel>
                        </DataTemplate>
                    </ListBox.ItemTemplate>
                </ListBox>
            </PivotItem>
            <PivotItem Header="ListView">
                <ListView x:Name="listView" ItemsSource="{Binding Items}">
                    <ListView.ItemTemplate>
                        <DataTemplate>
                            <StackPanel Orientation="Horizontal">
                                <TextBlock Text="{Binding FirstName}" FontSize="30"/>
                                <TextBlock Text="{Binding LastName}"
                                           FontSize="30" Margin="30,0,0,0"/>
                                
                            </StackPanel>
                        </DataTemplate>
                    </ListView.ItemTemplate>
                </ListView>
            </PivotItem>
            <PivotItem Header="GridView">
                <GridView x:Name="gridView">
                    <GridView.ItemTemplate>
                        <DataTemplate>
                            <StackPanel>
                                <TextBlock Text="{Binding FirstName}" 
                                           Width="80"/>
                                <TextBlock Text="{Binding LastName}" 
                                           Width="80"/>
                            </StackPanel>
                        </DataTemplate>
                    </GridView.ItemTemplate>

                    <GridView.ItemContainerStyle>
                        <Style TargetType="GridViewItem">
                            <Setter Property="BorderBrush" Value="Gray"/>
                            <Setter Property="BorderThickness" Value="1"/>
                            <Setter Property="HorizontalContentAlignment" Value="Center"/>
                            <Setter Property="VerticalContentAlignment" Value="Center"/>
                        </Style>
                    </GridView.ItemContainerStyle>
                </GridView>
            </PivotItem>
            <PivotItem Header="SemanticZoom">
                <Grid Margin="12,0,12,0">
                    <Grid.Resources>
                        <!--创建数据源对象,注意ItemContent属性就是数据源中真正的基础数据的列表属性,必须设置该属性的值数据源才能定位到实际绑定的数据实体对象-->
                        <CollectionViewSource x:Name="itemcollectSource" IsSourceGrouped="True" ItemsPath="ItemContent"/>
                    </Grid.Resources>
                    <SemanticZoom x:Name="semanticZoom">
                        <SemanticZoom.ZoomedInView>
                            <!--在这里放置GridView(或ListView)以表示放大视图-->
                            <ListView x:Name="inView">
                                <ListView.GroupStyle>
                                    <GroupStyle>
                                        <!--用于显示列表有的数据项的模板-->
                                        <GroupStyle.HeaderTemplate>
                                            <DataTemplate>
                                                <Border Background="Red" Height="80">
                                                    <TextBlock Text="{Binding Key}" FontSize="50"/>
                                                </Border>
                                            </DataTemplate>
                                        </GroupStyle.HeaderTemplate>
                                    </GroupStyle>
                                </ListView.GroupStyle>
                                <!--用于显示列表的数据项的模板-->
                                <ListView.ItemTemplate>
                                    <DataTemplate>
                                        <StackPanel>
                                            <TextBlock Text="{Binding Title}" Height="40" FontSize="30"/>
                                        </StackPanel>
                                    </DataTemplate>
                                </ListView.ItemTemplate>
                            </ListView>
                        </SemanticZoom.ZoomedInView>
                        <SemanticZoom.ZoomedOutView>
                            <!--在这里放置GridView(或ListView)以表示缩小视图-->
                            <GridView x:Name="outView">
                                <!--用于显示弹出的分组列表视图的数据项的模板-->
                                <GridView.ItemTemplate>
                                    <DataTemplate>
                                        <Border Height="60">
                                            <TextBlock Text="{Binding Group.Key}" FontSize="24"/>
                                        </Border>
                                    </DataTemplate>
                                </GridView.ItemTemplate>
<!--列表布局模板-->
                                <GridView.ItemsPanel>
                                    <ItemsPanelTemplate>
                                        <WrapGrid ItemWidth="100" ItemHeight="75" MaximumRowsOrColumns="1" VerticalChildrenAlignment="Center"/>
                                    </ItemsPanelTemplate>
                                </GridView.ItemsPanel>
                                <!--列表项目容器的样式设置-->
                                <GridView.ItemContainerStyle>
                                    <Style TargetType="GridViewItem">
                                        <Setter Property="BorderBrush" Value="Gray"/>
                                        <Setter Property="Background" Value="Red"/>
                                        <Setter Property="BorderThickness" Value="3"/>
                                        <Setter Property="HorizontalAlignment" Value="Center"/>
                                        <Setter Property="VerticalAlignment" Value="Center"/>
                                    </Style>
                                </GridView.ItemContainerStyle>
                            </GridView>
                        </SemanticZoom.ZoomedOutView>
                    </SemanticZoom>
                </Grid>
            </PivotItem>
            <PivotItem Header="VirtualDataList">
                <ListView ItemsSource="{Binding Data}">
                    <ListView.ItemTemplate>
                        <DataTemplate>
                            <StackPanel>
                                <TextBlock Text="{Binding Name}" Height="50"/>
                            </StackPanel>
                        </DataTemplate>
                    </ListView.ItemTemplate>
                </ListView>
            </PivotItem>
            <PivotItem Header="ItemsStackPanel">
                <ItemsControl x:Name="imageList">
                    <ItemsControl.ItemsPanel>
                        <ItemsPanelTemplate>
                            <!--设置横向布局-->
                            <ItemsStackPanel Orientation="Horizontal"/>
                        </ItemsPanelTemplate>
                    </ItemsControl.ItemsPanel>
                    <ItemsControl.Template>
                        <ControlTemplate TargetType="ItemsControl">
                            <!--设置水平滚动-->
                            <ScrollViewer ScrollViewer.HorizontalScrollBarVisibility="Visible" ScrollViewer.VerticalScrollBarVisibility="Disabled">
                                <!--<StackPanel>-->
                                    <ItemsPresenter/>
                                <!--</StackPanel>-->
                            </ScrollViewer>
                        </ControlTemplate>
                    </ItemsControl.Template>
                    <ItemsControl.ItemTemplate>
                        <DataTemplate>
                           
                                <StackPanel>
                                <Image Source="{Binding Image}" Width="144" Height="240" Stretch="Uniform"/>
                                <TextBlock Text="{Binding ImageName}"/>
                            </StackPanel>
                            
                        </DataTemplate>
                    </ItemsControl.ItemTemplate>
                </ItemsControl>
            </PivotItem>
            <PivotItem Header="ImageShow">
                <ListView x:Name="InternetImage">
                    <ListView.ItemTemplate>
                        <DataTemplate>
                            <StackPanel>
                                <TextBlock Text="{Binding Name}" Height="80"/>
                                <Image Source="{Binding ImageSource}" Width="200" Height="200"/>
                            </StackPanel>
                        </DataTemplate>
                    </ListView.ItemTemplate>
                </ListView>
            </PivotItem>
            <PivotItem Header="Polyline">
                <Polyline Stroke="LightBlue" StrokeThickness="5" Height="150" Points="0,10 50,40 100,90 150,50 200,50 250,10 300,100 350,30 400,0"/>
            </PivotItem>
            <PivotItem Header="Polygon">
                <Polygon Fill="AliceBlue" StrokeThickness="5" Height="150" Stroke="Red" Points="0,150 0,10 50,40 100,90 150,50 200,50 250,10 300,100 350,30 400,0 400,150"/>
            </PivotItem>
            <PivotItem Header="GetLineChartPointCollection">
                <Grid Margin="12,0,12,0">
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="*"/>
                    </Grid.RowDefinitions>
                    <Grid x:Name="chartCanvas" Height="400" HorizontalAlignment="Center" VerticalAlignment="Center">
                        
                    </Grid>
                    <StackPanel Grid.Row="1">
                        <Button Content="折线图" Click="Button_Click_23"/>
                        <Button Content="区域图" Click="Button_Click_24"/>
                    </StackPanel>
                </Grid>
            </PivotItem>
            <PivotItem Header="PiePlotter">
                <StackPanel>
                    <controls:PiePlotter x:Name="piePlotter" Width="400" Height="400" PieWidth="400" HoleSize="0.2"/>
                </StackPanel>
            </PivotItem>
            <PivotItem Header="ChartCanvas">
                <Grid>
                    <Grid.RowDefinitions>
                        <RowDefinition Height="80"/>
                        <RowDefinition Height="*"/>
                    </Grid.RowDefinitions>
                    <!--图例面板-->
                    <Canvas x:Name="legendCanvas" Height="80" Width="200"/>
                    <!--坐标轴面板-->
                    <Canvas x:Name="textCanvas" Width="300" Height="300" Grid.Row="1">
                        <!--线性图形面板-->
                        <Canvas x:Name="linechartCanvas" Width="300" Height="300"/>
                    </Canvas>
                </Grid>
            </PivotItem>
        </Pivot>
    </Grid>
   
    <Page.BottomAppBar>
        
        
        <!--菜单栏新增按钮跳转到添加购物清单页面-->
        
        <CommandBar Opened="CommandBar_Opened" Closed="CommandBar_Closed">

            <AppBarButton Label="新增" Icon="Add" Click="AppBarButton_Click_1"/>

            <AppBarButton Label="buy" Icon="Shop"/>
            <!--BitmapIcon图标按钮-->
            <AppBarButton Label="BitmapIcon" Click="AppBarButton_Click">
                <AppBarButton.Icon>
                    <BitmapIcon UriSource="ms-appx:///Assets/questionmark.png"/>
                </AppBarButton.Icon>
            </AppBarButton>
            <!--FontIcon图标按钮-->
            <AppBarButton Label="FontIcon" Click="AppBarButton_Click">
                <AppBarButton.Icon>
                    <FontIcon FontFamily="Candara" Glyph="&#x03a3;"/>
                </AppBarButton.Icon>
            </AppBarButton>
            <!--PathIcon图标按钮-->
            <AppBarButton Label="PathIcon" Click="AppBarButton_Click">
                <AppBarButton.Icon>
                    <PathIcon Data="F1 M 20,20L 24,10L 24,24L 5,24"/>
                </AppBarButton.Icon>
            </AppBarButton>
            <!--文本菜单按钮-->
            <CommandBar.SecondaryCommands>
                <AppBarButton Label="about" Click="AppBarButton_Click"/>
            </CommandBar.SecondaryCommands>
        </CommandBar>
    </Page.BottomAppBar>
</Page>
 /// <summary>
        /// 添加图表
        /// </summary>
        /// <exception cref="NotImplementedException"></exception>
        private void AddChart()
        {
           //添加报表的网格图形
           cs=new ChartStyleGridlines();
            cs.ChartCanvas = linechartCanvas;
            cs.TextCanvas = textCanvas;
            cs.Title = "Sine and Cosine Chart";
            cs.Xmin = 0;
            cs.Xmax = 7;
            cs.Ymin = -1.5;
            cs.Ymax = 1.5;
            cs.YTick = 0.5;
            cs.GridlinePattern = GridlinePatternEnum.Dot;
            cs.GridlineColor = new SolidColorBrush(Colors.Black);
            cs.AddChartStyle(new TextBlock());
            //画Sine曲线图形
            //ds = new DataSeries();
            ds.LineColor=new SolidColorBrush(Colors.Black);
            ds.LineThickness = 1;
            ds.SeriesName = "Sine";
            //计算出图形中的一系列点,然后用线段连接起来
            for (int i = 0; i < 36; i++)
            {
                double x = i / 5.0;
                double y = Math.Sin(x);
                ds.LineSeries.Points.Add(new Point(x, y));
            }
            dc.DataList.Add(ds);
            //画cosine曲线图形
            ds=new DataSeries();
            ds.LineColor=new SolidColorBrush(Colors.Red);
            ds.SeriesName = "Cosine";
            ds.LinePattern = GridlinePatternEnum.DashDot;
            ds.LineThickness = 2;
            for (int i = 0; i < 36; i++)
            {
                double x = i / 5.0;
                double y = Math.Cos(x);
                ds.LineSeries.Points.Add(new Point(x,y));
            }
            dc.DataList.Add(ds);
            //画sine^2曲线图形
            ds = new DataSeries();
            ds.LineColor = new SolidColorBrush(Colors.Green);
            ds.SeriesName = "Sine^2";
            ds.LinePattern = GridlinePatternEnum.Dot;
            ds.LineThickness = 2;
            for (int i = 0;  i< 36; i++)
            {
                double x = i / 5.0;
                double y = Math.Sin(x)*Math.Sin(x);
                ds.LineSeries.Points.Add((Point)new Point(x,y));

            }
            dc.DataList.Add(ds);
            dc.AddLines(linechartCanvas,cs);
            //添加图例
            lg.legendCanvas = legendCanvas;
            lg.IsLegend = true;
            lg.IsBorder = true;
            lg.AddLegend(cs,dc);
        }

        //网格图形
        private ChartStyleGridlines cs;
        //图例
        private Legend lg = new Legend();
        //图形数据集合
        private DataCollection dc = new DataCollection();
        //线性数据图形
        private DataSeries ds=new DataSeries();
```
13.4.QuickCharts图表控件库解析
QuickCharts图表控件是Amcharts公司提供的一个开源图表控件库,这个控件库支持WPF,Silverlight和Windows平台,源码可以从Github网站(http://github.com/ailon/amCharts-Qucik-Charts)下载.
QuickCharts图表控件封装了一些常用的图表控件如饼图,柱形图,折线图,区域图等,可以直接再项目中进行其提供的图表控件来创建图表.
13.41.QuickCharts项目结构分析
QuickCharts控件库包含了两类图表,一种是饼图图表PieChart,另外一种是连续图表SerialChart.连续图包含了线形,柱形,区域图等图形.
![1.png]
![2.png]
13.42.饼图图表PieChart的实现逻辑
饼图PieChart是由多个饼图切片Slice控件,一个图例控件Legend控件和一个标注Balloon控件组成.
