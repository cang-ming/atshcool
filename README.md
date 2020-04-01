# 系统辨识 实验1 

**《系统辨识基础》**

**实验手册**

**哈尔滨工业大学 控制与仿真中心**


## 实验1 白噪声、M序列的产生

**一、实验目的**

1、熟悉并掌握产生均匀分布随机序列方法以及进而产生高斯白噪声方法

2、熟悉并掌握M序列生成原理及仿真生成方法

**二、实验原理**

**1、混合同余法**

混合同余法是加同余法和乘同余法的混合形式，其迭代式如下：
![](https://raw.githubusercontent.com/cang-ming/picture_vnote/master/1585750513_20200401220254680_5366.png)
式中*a*为乘子，为种子，*b*为常数，*M*为模。混合同余法是一种递归算法，即先提供一个种子，逐次递归即得到一个不超过模*M*的整数数列。

**2、正态分布随机数产生方法**

由独立同分布中心极限定理有：设随机变量相互独立，服从同一分布，且具有数学期望和方差：
![、](https://raw.githubusercontent.com/cang-ming/picture_vnote/master/1585750518_20200401220305796_30310.png)
则随机变量之和的标准化变量:
![](https://raw.githubusercontent.com/cang-ming/picture_vnote/master/1585750523_20200401220334238_21950.png)
近似服从分布。

如果X~n~服从[0, 1]均匀分布，则上式中![](https://raw.githubusercontent.com/cang-ming/picture_vnote/master/1585750530_20200401220421814_12273.png)
即
![](https://raw.githubusercontent.com/cang-ming/picture_vnote/master/1585750531_20200401220434136_28438.png)

**3、M序列生成原理**

用移位寄存器产生M序列的简化框图如下图所示。该图表示一个由4个双稳态触发器顺序连接而成的4级移位寄存器，它带有一个反馈通道。当移位脉冲来到时，每级触发器的状态移到下一级触发器中，而反馈通道按模2加法规则反馈到第一级的输入端。

![](https://raw.githubusercontent.com/cang-ming/picture_vnote/master/1585750556_6eaf9b042c8d9ec37645ae7f7173eb19.png)

**三、实验内容**

**1、生成均匀分布随机序列**

（1）利用混合同余法生成[0,1]区间上符合均匀分布的随机序列，并计算该序列的均值和方差，与理论值进行对比分析。要求序列长度为1200，推荐参数为a=65539，M=2147483647，0\<x0\<M。

（2）将[0,1]区间分为不重叠的等长的10个子区间，绘制该随机序列落在每个子区间的频率曲线图，辅助验证该序列的均匀性。

（3）对上述随机序列进行独立性检验。（该部分为选作内容）

**2、生成高斯白噪声**

利用上一步产生的均匀分布随机序列，令n=12，生成服从N(0,1)的白噪声，序列长度为100，并绘制曲线。

**3、生成M序列**

M序列的循环周期取为N~p~=2^6-1=63，时钟节拍1s，幅度a=1，逻辑“0”为*a*，逻辑“1”为-*a*，特征多项式F(s)=S6 XOR S5。

生成M序列的结构图如下所示。
![](https://raw.githubusercontent.com/cang-ming/picture_vnote/master/1585750554_20200401220600229_25882.png)
要求编写Matlab程序生成该M序列，绘制二电平M序列信号曲线，并分析验证M序列的性质。

**四、实验步骤**

1．分别画出三部分实验内容的程序框图（流程图）；

2．编制MATLAB的M文件；

3．运行编制的M文件；

4．查看程序运行结果并进行分析；

5．填写实验报告。


### 实验1 代码实现

#### 前两问

``` matlab
clear;
clc;
a=65539;
M=2147483647;
len=1200;
LEN=100;
%定义参数完成
x(1)=0;
R(1)=x(1);
b=1024;
%启动值设置

for i=1:len
    x(i+1)=mod((a*x(i)+b),M);
    R(i+1)=x(i+1)./M;
end

u=mean(R);
v=std(R);
sprintf('R的均值=%f（理论值为0.50),R的方差=%f(理论值为0.0833333)',mean(R),var(R))

for i=1:LEN
    t=1+12*(i-1);
    sum=R(t);
    for k=1:12
        sum=sum+R(t+k);
    end
    y(i)=u+v*(sum-6);
end

subplot(121)
hist(y)
title('频率直方图');
t=hist(y)./LEN;
subplot(122)
ax=linspace(0.05,0.95,10);%直方图的中位数
plot(ax,t)
xticks(0:0.1:1)
ylim([0 0.25]);
title('频率曲线图');

%{
%实验1.1.2 验证均匀性
t=zeros(10,1);
for i=1:length
    %求落在每个子区间的个数
    temp=ceil(R(i)*10);
    t(temp)=t(temp)+1;
end

%天才一样的想法，先把R扩大然后用ceil函数四舍五入到区间内，然后再归到频率上，绝了

%求落在每个子区间的频率
t=t/length;
figure;
bar(t)
grid on;
title('落在每个子区间的频率分布图');

%但是没有画频率图啊

figure;
ax=linspace(1,10,10);
plot(ax,t);
ylim([0 1]);
title('频率曲线')
%}
```

#### 第三问

```matlab
%手写一个关于XOR函数
function f= XORM(x(i),y,a)
if x==y
    f=-a;
else
    f=a;
end
end

clear;
clc;
time=63;
a=1;

x(1)=-a;
b(1)=-a;
c(1)=-a;
d(1)=-a;
e(1)=-a;
f(1)=a;

%  设置初状态
for i=1:2*time;
    if e(i)==f(i)
        x(i+1)=-a;
    else
        x(i+1)=a;
    end
    b(i+1)=x(i);
    c(i+1)=b(i);
    d(i+1)=c(i);
    e(i+1)=d(i);
    f(i+1)=e(i);
end

x=0:2*time;
v=x(end)+1
x1=[x;x(2:end),v];%生成了两行向量
x2=x1(:);
%若x1是矩阵，则把x1矩阵按列拆分后纵向排列成一个大的列向量；若x是行向量，则相当于转置；若x1是列向量则不变。
f1=[f;f];
f2=f1(:);
plot(x2,f2);
%注意这个地方，它特意把x2和f进行了错位，以保证值变换时对应同一个坐标值。写这个代码的人真的神奇，
ylim([-1.5*a,1.5*a]);

%还有一种返璞归真的方法

% %实验1.3 生成M序列 
% %定义移位寄存器初始状态
% X=ones(1,6);
% figure;
% n=150;
% x=zeros(1,n);
% for j=1:n
%     temp=xor(X(6),X(5));
%     for i=6:-1:2;
%         X(i)=X(i-1);
%     end
%     X(1)=temp;
%     x(j)=X(6);
% end
% %将第六位寄存器的输出序列x，乘2再向下平移1个单位，满足a=±1
% x=2*x-ones(1,n);
% %连横线
% for j=1:n
%     line([j-1,j],[x(j),x(j)]);
%     hold on;
% end
% %连竖线
% for j=1:n-1
%     line([j,j],[x(j),x(j+1)]);
%     hold on;
% end
% %画出为0的直线
% line([0,n],[0,0]);
% axis([0,n,-1.5,1.5]);
% title('二电平M序列曲线');

%同学写的这个很有意思，尤其是直接乘以2再向下平移一个单位。当然实际上我那个XOR跟普适吧，即使a变化
%她绘制时序图是直接用的line函数……这过分了，不讲道理
```
