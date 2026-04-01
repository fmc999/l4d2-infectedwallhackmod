# l4d2-infectedwallhackmod
这只是一个特感透视MOD
================================================================================
        L4D2 特感透视MOD + Hitbox扩大 + 武器增强 完整修改教程
================================================================================

目录
----
1. 项目概述
2. 所需工具
3. 第一部分：修改透视颜色（VMT文件）
4. 第二部分：修改受击范围（MDL模型文件）
5. 第三部分：修改武器参数
6. 参数详解
7. 特感模型对照表
8. 常见问题

================================================================================
一、项目概述
================================================================================

本MOD包含三项修改：
1. 透视颜色修改 - 通过VMT材质文件实现
2. 受击范围扩大 - 通过MDL模型文件实现
3. 武器参数增强 - 通过武器脚本文件实现

MOD目录结构：
123h/
├── materials/          <- 材质文件（颜色、透视效果）
│   └── models/infected/
├── models/             <- 模型文件（hitbox、碰撞体积）
│   └── infected/
├── scripts/            <- 武器脚本（伤害、精准、射速）
├── 特感模型/           <- QC文件（用于修改hitbox）
├── addoninfo.txt
└── 完整修改教程.txt

================================================================================
二、所需工具
================================================================================

1. GCFScape - 提取VPK文件
   安装位置: C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Nem's Tools\GCFScape
   
2. Crowbar - 反编译/编译MDL模型
   下载地址: https://github.com/ZeqMacaw/Crowbar/releases
   
3. Left 4 Dead 2 SDK - 编译工具（游戏自带）
   路径: K:\SteamLibrary\steamapps\common\Left 4 Dead 2\bin\studiomdl.exe

================================================================================
三、第一部分：修改透视颜色（VMT文件）
================================================================================

VMT文件控制材质效果，包括颜色、透明度、发光等。

【颜色参数说明】
----------------
$color2 "[R G B]"        - 模型颜色（UnlitGeneric着色器使用）
$selfillum 1             - 启用自发光
$selfillumtint "[R G B]" - 自发光颜色

【RGB颜色值参考】
-----------------
红色:   [1 0 0]
绿色:   [0 1 0]
蓝色:   [0 0 1]
黄色:   [1 1 0]
紫色:   [1 0 1]
青色:   [0 1 1]
白色:   [1 1 1]
黑色:   [0 0 0]

【修改示例】
------------
原始VMT文件：
```
UnlitGeneric
{
$baseTexture "models\infected\hunter\hunter_01"
$phong 1
$ignorez 1
}
```

修改后（红色透视）：
```
UnlitGeneric
{
$baseTexture "models\infected\hunter\hunter_01"
$phong 1
$ignorez 1
$color2 "[1 0 0]"
$selfillum 1
$selfillumtint "[1 0 0]"
}
```

================================================================================
四、第二部分：修改受击范围（MDL模型文件）
================================================================================

【步骤1：提取原始模型】
-----------------------
1. 打开 GCFScape
2. File -> Open -> 选择游戏目录下的 pak01_dir.vpk
   路径: K:\SteamLibrary\steamapps\common\Left 4 Dead 2\left4dead2\pak01_dir.vpk
3. 导航到 models\infected\
4. 右键提取需要的特感文件夹

【步骤2：反编译模型】
---------------------
1. 打开 Crowbar.exe
2. 切换到 "Decompile" 页面
3. 将 xxx.mdl 文件拖入窗口
   注意：需要同目录下有 .vvd 和 .dx90.vtx 文件
4. 设置输出目录
5. 点击 "Decompile" 按钮
6. 得到 .qc 文件和多个 .smd 文件

【步骤3：修改QC文件】
---------------------
用记事本打开 .qc 文件

方法A：使用 $scale 参数（简单，同时放大模型和hitbox）
在文件开头添加：

$scale 1.75

【$scale 倍数参考】
------------------
$scale 1.0   = 原始大小（无变化）
$scale 1.25  = 放大25%
$scale 1.5   = 放大50%
$scale 1.75  = 放大75%（当前使用）
$scale 2.0   = 放大100%（2倍）

方法B：修改 $hbox 参数（高级，只改hitbox不改外观）
找到QC文件中的 $hbox 行，例如：

$hbox 1 "ValveBiped.Bip01_Head1" -1.7 -5.05 -3.7 7.7 4.45 3.7

【$hbox 语法】
--------------
$hbox <group> "<bone_name>" <min_x> <min_y> <min_z> <max_x> <max_y> <max_z>

扩大hitbox：将负数变得更负，正数变得更大
当前设置：头部hitbox放大到 -100 ~ 100（超大范围）

【步骤4：重新编译模型】
-----------------------
1. 打开 Crowbar
2. 切换到 "Compile" 页面
3. 点击 "Set Up Games" 添加游戏：
   - Game Name: Left 4 Dead 2
   - gameinfo.txt: K:\SteamLibrary\steamapps\common\Left 4 Dead 2\left4dead2\gameinfo.txt
   - studiomdl.exe: K:\SteamLibrary\steamapps\common\Left 4 Dead 2\bin\studiomdl.exe
4. 在下拉框选择 "Left 4 Dead 2"
5. 将修改后的 .qc 文件拖入窗口
6. 点击 "Compile" 按钮
7. 新模型输出到: K:\SteamLibrary\steamapps\common\Left 4 Dead 2\left4dead2\models\infected\

【步骤5：打包MOD】
------------------
1. 创建MOD目录结构：
   123h\models\infected\
   
2. 复制编译后的文件：
   - xxx.mdl
   - xxx.vvd
   - xxx.dx90.vtx
   - xxx.phy（如果有）

3. 使用VPK工具打包：
   K:\SteamLibrary\steamapps\common\Left 4 Dead 2\bin\vpk.exe
   
   命令行：
   vpk.exe "c:\Users\Administrator\Desktop\123h"

4. 将生成的 123h.vpk 复制到：
   K:\SteamLibrary\steamapps\common\Left 4 Dead 2\left4dead2\addons\

================================================================================
五、第三部分：修改武器参数
================================================================================

【武器脚本位置】
----------------
pak01_dir.vpk -> scripts -> weapon_xxx.txt

【已修改的参数】
----------------
| 参数 | 原始值 | 修改后 | 效果 |
|------|--------|--------|------|
| VerticalPunch | 1.2~4 | 0 | 无后坐力 |
| SpreadPerShot | 0.75~1.6 | 0 | 射击不扩散 |
| MaxSpread | 5~35 | 0 | 最大扩散为0 |
| MinDuckingSpread | 0.05~0.5 | 0 | 蹲下精准 |
| MinStandingSpread | 0.4~1.5 | 0 | 站立精准 |
| MinInAirSpread | 1.5~3 | 0 | 空中精准 |
| MaxMovementSpread | 1.5~6 | 0 | 移动精准 |
| Range | 2500~3000 | 99999 | 超远射程 |
| RangeModifier | 0.7~0.97 | 1.0 | 无伤害衰减 |
| SpreadDecay | 5 | 999 | 扩散瞬间恢复 |
| MaxAutoAimDeflection1 | 10 | 999 | 辅助瞄准范围超大 |
| MaxAutoAimRange1 | 0 | 99999 | 辅助瞄准距离超远 |
| WeaponAutoAimScale | 1.0 | 999 | 辅助瞄准强度最大 |
| PenetrationNumLayers | 2 | 99 | 可穿透99个敌人 |
| PenetrationPower | 30 | 999 | 穿透力最大 |
| PenetrationMaxDistance | 0 | 99999 | 穿透距离无限 |

【保持原值的参数】
------------------
| 参数 | 说明 |
|------|------|
| Damage | 伤害（保持原值） |
| CycleTime | 射速（保持原值） |
| clip_size | 弹夹容量（保持原值） |

================================================================================
六、参数详解
================================================================================

【$scale 参数】
---------------
作用：整体缩放模型
效果：同时改变模型大小和hitbox大小
优点：简单，一行代码搞定
缺点：特感看起来会变大

推荐值：
- 轻微增大: 1.25
- 中等增大: 1.5
- 较大增大: 1.75（当前使用）
- 大幅增大: 2.0

【$hbox 参数】
--------------
作用：定义每个骨骼的受击范围
效果：只改变hitbox，不影响模型外观
优点：精确控制每个部位的受击范围
缺点：需要逐行修改，比较繁琐

当前设置：头部hitbox放大到 -100 ~ 100（超大范围）

================================================================================
七、特感模型对照表
================================================================================

| 特感名称  | 模型文件      | 材质文件夹              |
|----------|----------------|------------------------|
| Hunter   | hunter.mdl     | models\infected\hunter\|
| Boomer   | boomer.mdl     | models\infected\boomer\|
| Boomette | boomette.mdl   | models\infected\boomer\|
| Smoker   | smoker.mdl     | models\infected\smoker\|
| Tank     | hulk.mdl       | models\infected\hulk\  |
| Jockey   | jockey.mdl     | models\infected\jockey\|
| Spitter  | spitter.mdl    | models\infected\spitter\|
| Charger  | charger.mdl    | models\infected\charger\|
| Witch    | witch.mdl      | models\infected\witch\ |

================================================================================
八、常见问题
================================================================================

Q1: 编译时提示找不到studiomdl.exe？
A: 确保在Crowbar中正确设置了Left 4 Dead 2的游戏路径

Q2: 游戏中模型显示为ERROR？
A: 检查模型文件是否完整（.mdl, .vvd, .dx90.vtx三个文件都需要）

Q3: 透视效果不生效？
A: 确保VMT文件中有 $ignorez 1 参数

Q4: 颜色修改不生效？
A: UnlitGeneric着色器使用 $color2，不是 $color

Q5: hitbox修改后感觉没变化？
A: 尝试更大的 $scale 值，或手动修改 $hbox 参数

Q6: VPK文件被占用无法复制？
A: 关闭游戏后再复制，或重启电脑

Q7: M16步枪模型不显示？
A: 确保viewmodel路径是 v_rifle.mdl，不是 v_rifle_a.mdl

================================================================================
九、快速参考
================================================================================

【颜色修改 - VMT文件】
添加到VMT文件末尾：
$color2 "[1 0 0]"
$selfillum 1
$selfillumtint "[1 0 0]"

【Hitbox修改 - QC文件】
添加到QC文件开头：
$scale 1.75

【打包命令】
vpk.exe "文件夹路径"

================================================================================
十、当前MOD配置总结
================================================================================

【特感设置】
- 整体放大：$scale 1.75（75%）
- 头部hitbox：-100 ~ 100（超大）
- 透视颜色：红色

【武器设置】
- 精准度：完美（0扩散）
- 后坐力：无
- 射程：99999
- 辅助瞄准：最大
- 穿透力：最大

【修改的武器】
- 步枪：M16, AK47, 沙漠步枪, SG552
- 冲锋枪：UZI, 消音UZI, MP5
- 手枪：普通手枪, 沙漠之鹰
- 霰弹枪：Chrome, 泵动, 自动, SPAS
- 狙击枪：狩猎步枪, 军用狙击, Scout, AWP

================================================================================
