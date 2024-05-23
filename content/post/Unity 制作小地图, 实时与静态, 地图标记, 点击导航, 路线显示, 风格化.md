---
title: Unity 小地图, 实时与静态, 地图标记, 点击导航, 路线显示, 风格化
slug: "20240306132352"
description: Unity 制作小地图, 支持实时相机渲染, 静态贴图, 地图标记, 点击地图导航, 显示导航路线, 风格化小地图
date: 2024-03-06T13:23:52+08:00
tags:
  - unity
  - minimap
  - tutorial
categories:
  - Unity
  - Tutorial
---

![](images/vlcsnap-2024-03-08-11h04m42s227.png)

如上图, 这些地图分别是:

1. 位置和方向均跟随玩家, 并且带有地图标记的地图
2. 位置和方向固定, 带有地图标记的地图
3. 位置跟随玩家, 带有地图标记的地图
4. 位置跟随玩家, 带有部分地图标记, 能够显示导航路线的地图
5. 位置跟随玩家, 带有部分地图标记, 能够显示导航路线且风格化的地图.

<br />

## 实时地图

在 Unity 中, 相机渲染的结果能够直接保存到 "渲染贴图" 中, 而贴图可以呈现在 UI 界面上, 通过这一方式, 我们可以创建最简单的实时地图.

首先, 在项目面板中, 我们需要创建一个用于存储相机渲染输出的 "渲染贴图", 右击鼠标, 选择 "创建 -> 渲染贴图(Render texture)"

![](images/Pasted%20image%2020240308111441.png)

然后, 在创建中创建一个垂直向下看的相机, 并将投影模式改为 "正交(Orthographic)":

![](images/Pasted%20image%2020240308111814.png)

然后, 将相机的 "目标贴图" 设置为我们刚刚创建好的渲染贴图:

![](images/Pasted%20image%2020240308112025.png)

做完这些操作之后, 你会得到这样的结果:

![](images/Pasted%20image%2020240308112212.png)

最后, 我们只需要使用任意方式, 将贴图展示在屏幕上就可以了. 最简单的方式就是直接使用 UI 中的 "RawImage" 进行显示.

在场景中创建 RawImage, 然后将其要显示的纹理设置为我们创建好的渲染纹理, 最后你会看到, UI 上的图片显示着相机拍摄到的内容.

![](images/Pasted%20image%2020240308112626.png)

至此, 最简单的实时小地图, 我们就做好了. 关于一些简单的拓展的内容, 大概如下:

1. 如果要跟随玩家, 那么直接手写脚本, 使相机跟随玩家即可
2. 如果要随玩家旋转, 直接手写脚本, 跟随玩家, 并且跟随玩家旋转. 当然你也可以直接将其设置为玩家的子对象, 这样玩家移动和旋转的时候, 相机也会移动和旋转.

<br />

## 静态地图

静态地图就是提前对场景进行拍摄, 拿到贴图之后放到 UI 上, 然后在实际显示时, 根据比例系数, 移动这张图片.

在 Unity 中截图还是比较麻烦的, 建议是写个编辑器脚本, 点击按钮, 动态的创建相机, 然后使用正交相机, 渲染到 RenderTexture, 再将 RenderTexture 保存到本地, 下面是供参考的代码:

```csharp
public class MinimapTextureTool : EditorWindow
{
    [MenuItem("Tools/MinimapTextureTool")]
    static void Open()
    {
        var window = EditorWindow.GetWindow<MinimapTextureTool>();
        window.Show();
    }
    
    string _textureSizeStr = "1024";
    string _cameraHeightStr = "5";
    string _minimapAreaSizeStr = "50";
    int _minimapLayers = ~0;

    void OnGUI()
    {
        GUILayout.Label("纹理工具");

        GUILayout.Label("纹理大小: ");
        _textureSizeStr = GUILayout.TextField(_textureSizeStr);

        GUILayout.Label("相机高度: ");
        _cameraHeightStr = GUILayout.TextField(_cameraHeightStr);

        GUILayout.Label("小地图区域大小: ");
        _minimapAreaSizeStr = GUILayout.TextField(_minimapAreaSizeStr);

        GUILayout.Label("小地图层级遮罩: ");
        LayerMask tempMask = EditorGUILayout.MaskField( InternalEditorUtility.LayerMaskToConcatenatedLayersMask(_minimapLayers), InternalEditorUtility.layers);
        _minimapLayers = InternalEditorUtility.ConcatenatedLayersMaskToLayerMask(tempMask);

        GUILayout.Space(10);

        if (!int.TryParse(_textureSizeStr, out var textureSize) ||
            !float.TryParse(_cameraHeightStr, out var cameraHeight) ||
            !float.TryParse(_minimapAreaSizeStr, out var minimapAreaSize))
        {
            GUILayout.Label("输入有误!");
            return;
        }

        if (GUILayout.Button("生成小地图纹理"))
        {
            string filename = EditorUtility.SaveFilePanel("保存纹理", Application.dataPath, "小地图", "png");

            // 创建渲染纹理
            RenderTexture rt = new RenderTexture(textureSize, textureSize, 32);

            // 创建相机
            GameObject cameraGameObject = new GameObject();
            Camera camera = cameraGameObject.AddComponent<Camera>();

            cameraGameObject.transform.eulerAngles = new Vector3(90, 0, 0);
            cameraGameObject.transform.position = new Vector3(0, cameraHeight, 0);

            // 设置相机属性
            camera.cullingMask = _minimapLayers;
            camera.clearFlags = CameraClearFlags.SolidColor;
            camera.backgroundColor = new Color(0, 0, 0, 0);
            camera.orthographic = true;
            camera.orthographicSize = minimapAreaSize / 2;

            // 渲染
            camera.targetTexture = rt;
            camera.Render();

            // 保存当前激活的渲染纹理, 然后激活刚创建的渲染纹理
            var activeRenderTextureBefore = RenderTexture.active;
            RenderTexture.active = rt;

            // 创建 2D 纹理, 并从渲染纹理中读入数据
            Texture2D png = new Texture2D(textureSize, textureSize);
            png.ReadPixels(new Rect(0, 0, textureSize, textureSize), 0, 0);
            png.Apply();

            // 恢复之前激活的渲染纹理
            RenderTexture.active = activeRenderTextureBefore;

            // 编码 PNG, 并保存
            var bytes = png.EncodeToPNG();
            System.IO.File.WriteAllBytes(filename, bytes);

            // 清理临时创建的对象
            DestroyImmediate(cameraGameObject);
            DestroyImmediate(rt);
            DestroyImmediate(png);
        }
    }
}
```

如果要手动截图的话, 可以将窗口上的这两个关闭, 隐藏天空盒以及辅助线之类的.

![](images/Pasted%20image%2020240311112837.png)

这里以一个准备好的地图做示例.

![](images/Pasted%20image%2020240311145121.png)

然后, 我们继续写一个简单的脚本用来展示地图, 通过改变顶点 UV, 实现移动地图内容.

```csharp
public class SimpleTextureMinimap : MaskableGraphic
{
    public Texture MinimapTexture;
    public Vector2 Pivot = new Vector2(.5f, .5f);

    public float MinimapAreaSize = 50;
    public float DisplayAreaSize = 10;
    public float ImageSize = 100;

    public Transform FollowTarget;

    private void Update()
    {
        if (FollowTarget != null)
            SetVerticesDirty();
    }

    public override Texture mainTexture => MinimapTexture;

    protected override void OnPopulateMesh(VertexHelper vh)
    {
        vh.Clear();

        var radius = ImageSize / 2;
        var uvRadius = DisplayAreaSize / MinimapAreaSize / 2;
        var uvOffset = new Vector2();

        if (FollowTarget != null)
            uvOffset = new Vector2(FollowTarget.position.x, FollowTarget.position.z) / MinimapAreaSize;

        vh.AddVert(new Vector3(-radius, -radius), color, Pivot + uvOffset + new Vector2(-uvRadius, -uvRadius));
        vh.AddVert(new Vector3(-radius, radius), color, Pivot + uvOffset + new Vector2(-uvRadius, uvRadius));
        vh.AddVert(new Vector3(radius, radius), color, Pivot + uvOffset + new Vector2(uvRadius, uvRadius));
        vh.AddVert(new Vector3(radius, -radius), color, Pivot + uvOffset + new Vector2(uvRadius, -uvRadius));

        vh.AddTriangle(0, 1, 2);
        vh.AddTriangle(0, 2, 3);
    }
}
```

在上面的代码中, 我们只使用跟随目标的位置计算 UV 偏移量, 没有考虑到当玩家旋转时, 地图也旋转的这种需求, 所以计算就变得简单了许多.

<br/>


## 地图标记

要将场景中的某些物体在地图上标记出来, 方式有很多. 对于基于渲染相机的小地图, 其中最简单的方式是直接在物体上创建一个世界坐标空间的 Canvas, 并在上面放一张图片, 然后让小地图相机拍摄到.

就像这样, 调整 Canvas 的位置, 旋转, 缩放使其面朝上方以被相机地图拍摄到, 然后设置单独的层级, 并且使主相机不渲染该层级, 而小地图相机渲染它.

![](images/Pasted%20image%2020240311155359.png)

最后的效果, 就是这样的:

![](images/Pasted%20image%2020240311155649.png)

对于静态地图, 也就是地图由贴图构成, 我们需要做世界坐标到地图坐标的坐标转换, 然后在地图上实例化 Sprite, 并设置其位置.

> 地图标记当然不止这一种实现方式, 不如说, 直接用 UI 在小地图上放一个图片是更好的解决方案

<br/>

## 小地图包

对于更多功能, 我将它封装成一个 Unity 包了. 其中就包含实时小地图, 静态小地图, 标记, 点击导航以及路线显示.

要安装这个包, 首先确保你的电脑中有安装 'git', 然后在 Unity 包管理器中, 单击左上角的 '添加包' 按钮, 然后单击 '从 git URL 添加包', 然后输入 `https://github.com/SlimeNull/UnityMinimaps.git` 并确认.

![](images/Pasted%20image%2020240517105319.png)

![](images/Pasted%20image%2020240517105207.png)

不出意外的话, 名为 "SlimeNull.UnityMinimaps" 的包会被安装到你的项目中:

![](images/Pasted%20image%2020240517105433.png)

现在, 我们可以直接在场景中创建小地图了. 在 Unity 菜单栏中, 找到 `Tools` 按钮, 单击其中的 `Minimap` 即可打开小地图创建工具.

![](images/Pasted%20image%2020240517105647.png)

如果要在场景中创建一个基于相机与渲染纹理的实时地图, 需要以下准备:

1. 一个渲染纹理
2. 一个地图相机, 并且有赋值上面所说的渲染纹理, 同时该相机必须正向垂直向下照射(X旋转90度, Y 和 Z 不旋转)

![](images/Pasted%20image%2020240523100248.png)

然后, 将小地图类型调整为 "Render texture", 拖拽小地图相机到窗口中, 最后在场景层级中选择你要放置小地图的位置(这里是 Canvas 节点), 然后点击 "创建" 即可.

> 如果你在场景层级中选择对象, 它会提示你 "选择一个在 Canvas 之下的 GameObject 以创建小地图", 只有当你选择之后, 才会有 "创建" 按钮.

![](images/Pasted%20image%2020240523091600.png)

在做完上述步骤之后, Canvas 下就会自动创建好一个小地图, 其中显示的内容是相机所照射到的内容. 通过这一方式就能实现实时小地图了.

![](images/Pasted%20image%2020240523091848.png)

如果你要调整小地图的显示大小, 则需要设置 `Minimap` 组件中的 `Size`.

![](images/Pasted%20image%2020240523092024.png)

### 操作

通过更改 `Minimap` 组件中的 `AreaSize`, 可以指定当前小地图显示的区域大小. 不过, 注意不要超过小地图相机的照射范围, 因为范围之外是显示不到的.

自带的 `MinimapModifier` 组件是实现用户鼠标滚轮缩放小地图的功能的. `Factor` 用来指定缩放的速度倍数, `MinSize` 和 `MaxSize` 则是最小与最大的大小. 如果你不希望用户能够通过鼠标滚轮调整大小, 移除这个组件就可以了.

![](images/Pasted%20image%2020240523095045.png)

### 人物跟随

要实现人物跟随, 只需要将要跟随的物体设置到 `Minimap` 组件的 `Origin` 上即可. 并且, 当跟随的物体旋转时, 小地图也会跟着旋转, 保证玩家的前方始终是地图上方.

![](images/Pasted%20image%2020240523092355.png)

不过, 需要注意的是, 如果你的小地图相机不会移动, 那么当玩家超出小地图照射范围的时候, 那么小地图的内容也会变成空, 因为小地图渲染器获取不到玩家周围的地图信息了.

所以一般的, 如果你需要始终能够正确显示小地图, 你还需要保证小地图跟随玩家, 以保证小地图组件能够获取到玩家周围的地图信息. 我们需要一个简单的物体跟随脚本来使相机始终跟随玩家.

```csharp
using UnityEngine;

[ExecuteAlways]
public class FollowObject : MonoBehaviour
{
    public Transform Target;
    public Vector3 Offset;

    private void LateUpdate()
    {
        if (Target != null)
        {
            transform.position = Target.position + Offset;
        }
    }
}
```

然后挂载到小地图相机上, 这样小地图就始终跟随玩家了.

![](images/Pasted%20image%2020240523100417.png)

注意, 不要直接将小地图相机挂载到玩家下, 因为这样, 玩家的旋转会影响到地图, 从而导致地图错乱, 以下的做法是 **错误** 的!

![](images/Pasted%20image%2020240523100623.png)

### 多边形地图

默认小地图的显示是 "方形" 的, 如果你需要换成多边形的小地图, 只需要将小地图中的 "MinimapRenderer" 组件换成 "MinimapPolygonRenderer" 即可.

![](images/Pasted%20image%2020240523092958.png)

![](images/Pasted%20image%2020240523093035.png)

通过增加 "顶点数" 你也可以将它做成近似 "圆形" 的小地图.

![](images/Pasted%20image%2020240523093233.png)

当然, 如果你需要更多奇奇怪怪形状的小地图, 也可以直接使用 UI 上的遮罩, 毕竟小地图的渲染器也是一个 MaskableGraphics.

### 标记

上面我们讲了直接通过让渲染贴图照射到一个 UI 图像来实现的小地图, 但在这里的小地图包中, 直接提供了在小地图 UI 上显示标记的功能.

首先我们需要把我们的 "标记" 做成预制体, 这通常是一个 "Image" 图片.

![](images/Pasted%20image%2020240523094335.png)

然后在小地图的 "MinimapIndicator" 组件中添加一个新项, 并指定要标记的物体, 以及图标的预制体即可.

![](images/Pasted%20image%2020240523094457.png)

在运行时, 小地图标记就会实例化到小地图中, 然后显示出来, 并跟随目标物体.

![](images/Pasted%20image%2020240523094631.png)

### 导航

这个小地图包还支持用户点击小地图时, 调用主角的 `NavMeshAgent` 进行导航. 只需要将玩家的 `NavMeshAgent` 赋值到小地图下的 `MinimapNavigator` 组件的 `NavMeshAgent` 上, 然后设置能够导航的层级遮罩即可.

![](images/Pasted%20image%2020240523100906.png)

现在, 在地图上点击, 小地图的导航器就会自动做射线检测, 找到目标点, 并设置玩家的 `NavMeshAgent` 的目标点.

如果你还想要在地图上显示导航路径, 可以用小地图下面的 `NavigationPathRenderer`, 默认创建的小地图会包含一个, 将需要显示导航路径的 `NavMeshAgent` 赋值上去就可以了.

![](images/Pasted%20image%2020240523101218.png)

### 风格化

风格化在这里实现的原理就是直接做好场景的一个地图图片, 然后小地图在渲染时, 就基于这个小地图的内容进行渲染. 和实时小地图一样, 你不需要自己编写任何逻辑, 只需要跟随向导, 拍摄一个地图图片, 然后用美术软件对其进行处理, 最后设置好相关属性就可以了.

使用小地图工具中的 "纹理工具" 可以直接拍摄一张当前场景的地图图片, 设定好你想要的纹理大小, 用于拍摄的相机高度, 小地图的区域大小(直径), 以及要拍摄的层级, 最后点击 "生成小地图纹理" 即可.

![](images/Pasted%20image%2020240523101903.png)

最后使用编辑好的地图贴图创建一个静态地图就可以了.

![](images/Pasted%20image%2020240523102150.png)

微调设置, 最后的效果大致如此. 可以看到在静态的小地图上, 有我们通过绘图工具绘制的一些线条.

![](images/Pasted%20image%2020240523102350.png)

> 需要注意的是, 在创建静态小地图时, 纹理的区域大小必须设置正确, 否则小地图的显示就出问题了. 所以建议对于小地图纹理的命名中包含小地图的大小, 这样就不会忘记地图大小了.

另外, 由于静态小地图的渲染基于静止的图片, 所以场景中运动的物体, 你就没办法实时的显示出来它了, 而是应该使用 "地图标记" 来表示它的位置与方向.