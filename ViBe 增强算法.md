原文：

Background Subtraction: Experiments and Improvements for ViBe

作者：

M. Van Droogenbroeck and O. Paquot

<https://github.com/upcAutoLang/BackgroundSplit-OpenCV/blob/master/src/ViBe%2B/ViBePlus.cpp>

<https://blog.csdn.net/ajianyingxiaoqinghan/article/details/72782685>

<https://www.cnblogs.com/huty/p/8517713.html>

本文未覆盖原文全部内容，仅说明了其与原始 ViBe 算法差异处的自行理解，并且未做仿真验证。

# 当前像素值与模型内样本值的比较

与原始 ViBe 不同的地方在于，**只有颜色畸变范围小于门限的情况下才判断欧氏距离，并且欧氏距离比较的门限采用自适应计算的方式得到。**

## 颜色畸变（Color Distortion）

参考原文：

Real-time foreground-background segmentation using codebook model

作者：

Kyungnam Kim, Thanarat H. Chalidabhongse, David Harwood, Larry Davis

颜色畸变用于比较当前像素值 $x_t=(R, G, B)$ 和背景模型内的采样值 $v_i=(\bar R, \bar G, \bar B)$

将数值看成 RGB 颜色空间内的三维向量，畸变值 dist 计算如下：
$$
||x_t||^2=R^2+G^2+B^2\\
||v_i||^2=\bar R^2+\bar G^2+\bar B^2\\
\langle x_t, v_i\rangle ^2=(\bar R\times R+\bar G\times G+\bar B\times B)^2
$$

$$
p^2=||x_t||^2\times(cos\theta)^2=\frac{\langle x_t, v_i\rangle^2}{||v_i||^2}
$$

$$
dist(x_t, v_i)=\sqrt{||x_t||^2-p^2}
$$

## 自适应欧氏距离门限

原始 ViBe 使用固定的门限值，很难适应变化背景的像素点。于是以当前像素点模型内全部采样值的标准差 $ \sigma$ 为基准，使用限制范围在 [20, 40] 内的 $0.5\sigma$ 作为欧氏距离门限。

# 2 个 Mask 分别用于输出显示和更新像素模型

2 个 mask，**Segmentation Mask 和 Update Mask，前者用于输出计算结果，后者用于更新像素模型。**

==更新像素模型只有 2 种情况：==

1. 只有在 Update Mask 中被认为是背景的像素点才按照更新概率写入像素模型；
2. 在当前像素值与模型样本值比较过程中，如果连续 50 次被判定为前景，则表示由于更新概率问题导致静止区域被误判为运动目标，立即将当前像素值写入像素模型。

2 个 mask 由不同的形态学处理生成，形态学处理的输入都为[当前像素值与模型样本值比较](#当前像素值与模型内样本值的比较)的结果，其中背景像素值为 0，前景像素值为 255。下文中为描述方便，将此比较结果称为==样本比较图==。

由于原文描述并不详细：

> we apply several area openings on both the segmentation and updating masks

下文中猜测生成方法应当源于形态学的开计算（先腐蚀再膨胀）。

## Segmentation Mask 生成

Segmentation Mask 生成分为 2 个步骤：

1. > remove foreground blobs whose area is smaller or equal to  10 (pixels)

2. > fill holes in the foreground whose area is smaller or equal to  20

**移除前景斑点的处理（不处理与图像边缘相连的斑点），可以考虑使用 $3\times 3$ 结构元（与原文中 10 像素不一致）对样本比较图进行腐蚀处理。与普通腐蚀处理不同的地方在于，计算过程中如果结构元覆盖范围的邻域还有相邻的前景像素点，则不进行腐蚀计算。实际计算过程中，可能使用 $5\times 5$ 滑窗，在其中心 $3\times 3$ 区域内有前景点，并且该区域内前景点 8 邻域内都没有其它前景点的情况下，才腐蚀当前滑窗中心的 $3\times 3$ 区域。**

**填充孔洞的处理，可以考虑使用 $5\times 5$ 滑窗，滑窗中心像素点为背景，且滑窗最外框的 16 个像素点全为前景点，则整个滑窗范围内全设置为前景点。（与原文中 20 像素不一致）**

## Update Mask 生成

> fill holes in the foreground whose area is smaller or equal to 50

**填充孔洞的处理与 Segmentation Mask 一致，只是换成使用 $7\times 7$ 滑窗。（与原文中 50 像素不一致）**

Update Mask 不像 Segmentation Mask 一样移除前景斑点的原因在于，Update Mask 保持前景斑点是用于防止前景点被写入像素模型中，而 Segmentation Mask 用于输出显示。

# 抑制空间扩散

在原始 ViBe 算法中，向像素模型中写入 8 邻域内随机像素点的方式，导致了可能会有前景像素点作为采样值写入模型中，于是产生了前景点被认为是背景的情况，于是出现了背景的空间扩散。这种做法的目的是为了抑制鬼影和静态物体。

但是在某些情况下需要在一定程度上在背景中保留静态物体，于是在 Update Mask 中找到原图背景点，如果与其 8 领域内任一像素点出现大于 50 的差值（梯度），在当前像素点模型内不写入其 8 邻域内任何一个像素点。

# 闪烁点检测

对每个像素点维持 1 个闪烁计数，在 Update Mask 中前景背景的判定在相邻 2 帧内出现变化，则数值增 15，如果 2 帧内保持一致，则数值减 1，计数值保持在 [0, 150] 范围内。

当闪烁计数值大于等于 30，则该像素点在 Update Mask 中设置为前景，不用于更新像素模型。

# 更新概率控制

更新概率指的是被认定为背景的像素点写入像素模型的概率。原始 ViBe 算法更新概率固定为 16，增强算法做出以下优化：

1. 初始化后最早的 100 帧内使用更新概率 1（即 1/1 的概率，将背景像素写入像素模型）；
2. 100 帧之后使用更新概率 5（即 1/5 的概率，将背景像素写入像素模型）；
3. 一旦发现镜头移动，则在其的后 100 帧内使用更新概率 1。

## 镜头移动判断

原文推荐：<http://cecas.clemson.edu/~stb/klt/user/index.html>

<https://blog.csdn.net/linolzhang/article/details/54342610?depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-5&utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-5>

KLT 算法原本用于跟踪图像内的特征点，使用原本的 KLT 算法用于判断镜头移动可能会可于沉重。

如果只使用像素级的角点判断可能会更加方便。

以下方法来源于笔者想像，未经验证。

基本思想即，==固定帧间隔判断角点移动==。

固定多帧间隔，而不是每帧都进行判断的原因在于，即使出现镜头移动的情况，2 帧之间的角点的变化情况可能也不是很明显。有将镜头缓慢移动误判为镜头固定的可能性。

在每个用于判断的帧内，首先寻找合适的角点。计算方法采用 x 轴 y 轴分别计算梯度的方法，当某个像素点的 x 轴梯度值和 y 轴梯度值都超过预设门限的情况下，将当前点放入候选。选择所有候选点中，梯度值最大的 100 个像素点作为特征角点。

在当前帧的 100 个特征角点的 8 邻域（针对原文中 1 像素的移动判断条件）内查看是否有前一个判断帧的角点，如果超过半数（50）个特征角点的 8 邻域内都出现了前一个判断帧的角点，则认为当前没有出现镜头移动，否则认为镜头已经移动。