# Tinkr API 文档整理

## GUI 相关 API
- **GUIBuilder:New(config)**: 创建新 GUI 构建器
- **Builder:Window({key, title, width, height, content})**: 创建窗口
- **Builder:TabGroup({key, tabs})**: 创建选项卡组
- **Builder:Tab({title, content})**: 创建选项卡
- **Builder:Checkbox({key, label, description, default, tristate})**: 创建复选框
- **Builder:Radio({key, values, stacked})**: 创建单选框
- **Builder:EditBox({key, label, placeholder, description, button, multiline, lines})**: 创建编辑框
- **Builder:Dropdown({key, label, values, multi, description})**: 创建下拉菜单
- **Builder:Slider({key, label, description, min, max, step, default, percent})**: 创建滑块
- **Builder:ColorPicker({key, label, description, alpha, default})**: 创建颜色选择器
- **Builder:Text(text)**: 添加文本
- **Builder:Spacer({height})**: 添加间隔
- **Builder:Group({title, content})**: 创建分组
- **Builder:SimpleGroup(content)**: 创建简单分组
- **Builder:HTML(html)**: 添加 HTML 内容
- **Builder:Image({image, width, height, fit})**: 添加图片
- **Builder:Padding({padding, content})**: 添加内边距
- **Builder:Rows(content)**: 创建行布局
- **Builder:Columns(content)**: 创建列布局
- **Builder:TreeGroup({key, branches})**: 创建树形分组
- **Builder:TreeBranch({title, icon, content})**: 创建树形分支
- **Builder:GetConfig(key, default)**: 获取元素值
- **Builder:Listen(key, callback)**: 监听元素变化
- **Builder:Emit(key, value)**: 更新 GUI 元素值
- **Builder:Build(window)**: 构建 GUI

### 示例: 简单 GUI 窗口
```lua
local Tinkr = ...
local Builder = Tinkr.Util.GUIBuilder:New{config="example_simple"}
local TabGroup = Builder:TabGroup{key='tab_group',tabs={
    Builder:Tab{title="Info",content={
        Builder:Text"This is an example GUI Builder window...",
        Builder:Spacer{},
        Builder:Text"All elements emit events...",
        Builder:Spacer{},
        Builder:Text"We hope you find this utility helpful..."
    }},
    Builder:Tab{title="Config",content={
        Builder:Checkbox{key="checkbox_a",label="Checkbox A",description="Description for Checkbox A."},
        Builder:Spacer{},
        Builder:Checkbox{tristate=true,key="tribox_a",label="Tri State A",default="yes"},
        Builder:Spacer{},
        Builder:Radio{key="radio_a",values={option_a="Option A",option_b="Option B",option_c="Option C",option_d="Option D"}},
        Builder:Spacer{},
        Builder:EditBox{key="editbox_c",label="Edit Box C",placeholder="Placeholder Value",description="This input requires you to click "..OKAY,button=true},
        Builder:Spacer{},
        Builder:Dropdown{key='dropdown_a',label="Example Dropdown A",values={option_a="Option A",option_b="Option B",option_c="Option C",option_d="Option D",option_e="Option E"}},
        Builder:Spacer{},
        Builder:Slider{key='slider_a',label="Example Slider A",description="This slider ranges from 0% (0) to 100% (1)."},
        Builder:Spacer{},
        Builder:ColorPicker{key='color_a',label="Example Color Picker A",description="This does not include an alpha value."}
    }}
}}
local Window = Builder:Window{key="gui_example",title="GUI Example",width=350,height=500,content={TabGroup}}
Builder:Build(Window)
```

## 游戏相关 API
- **alive([UnitId])**: 检查单位是否存活，返回布尔值
- **AreaTriggerSpell(objectReference)**: 返回区域触发器的 spellID
- **aroundunit(unit, range)**: 返回单位周围的队伍成员数量和总血量百分比
- **aura(unit, spellID, filter)**: 返回光环剩余时间，建议用 buff/debuff
- **auraCount(unit, spellID, filter)**: 返回光环计数
- **between(low, IN, high)**: 检查数值是否在范围内
- **BringWindowToForeground()**: 将窗口置于前台
- **buff(spell, [unit])**: 检查单位是否拥有指定增益
- **buffCount(unit, spellID, filter)**: 返回增益计数
- **buffDuration(spell, [unit])**: 返回增益剩余时间
- **CameraPosition()**: 返回相机位置
- **cansee(unita, unitb)**: 检查单位间是否有视线
- **castable(spellID, unitID/object)**: 检查技能是否可施放
- **casting([UnitId])**: 返回单位正在施放的技能名称
- **castremaining([unit, time])**: 返回技能施放剩余时间或百分比
- **channeling([UnitId])**: 返回单位正在引导的技能名称
- **charges([spellID])**: 返回技能剩余充能次数
- **ClearAFK()**: 清除 AFK 状态
- **ClearNPCObject(objectReference)**: 清除目标对象
- **ClearTargetObject(objectReference)**: 清除目标对象
- **Click(x, y, z)**: 在指定位置点击地面
- **clickable(Vector3)**: 检查位置是否可点击
- **clickableEntity(unitId)**: 检查目标是否可点击
- **clickitem(itemid)**: 点击使用物品
- **combat([UnitId])**: 检查单位是否在战斗中
- **cooldown([spellID])**: 返回技能冷却时间
- **covenant()**: 返回玩家所属盟约
- **Crash()**: 使客户端崩溃
- **CreateDirectory(dir)**: 创建目录
- **DeleteFile(file)**: 删除文件
- **DirectoryExists(dir)**: 检查目录是否存在
- **distance(unita, unitb)**: 返回两单位间距离
- **distancecheck(unit, spell[, range])**: 检查单位是否在技能范围内
- **dungeon()**: 检查是否在副本中
- **enabled()**: 检查例程引擎是否启用
- **enemiesAround(unit, range)**: 返回单位周围敌人数量
- **enemy([UnitId])**: 检查单位是否可攻击
- **exists(unit)**: 检查单位是否存在
- **FaceDirection(dir[,update])**: 设置玩家朝向
- **FaceObject(objectReference)**: 面向指定对象
- **ObjectCreatureType(objectReference)**: 返回对象生物类型
- **ObjectDistance(objectReference, objectReference)**: 返回两对象间距离
- **ObjectFlags(objectReference)**: 返回对象标志
- **ObjectGUID(objectReference)**: 返回对象 GUID
- **ObjectHeight(objectReference)**: 返回对象高度
- **ObjectId(objectReference)**: 返回对象 ID
- **ObjectIsOutdoors(objectReference)**: 检查对象是否在室外
- **ObjectIsSubmerged(objectReference)**: 检查对象是否在水下
- **ObjectLootable(objectReference)**: 检查对象是否可拾取
- **ObjectLootTarget(objectReference)**: 返回对象的拾取目标
- **ObjectMovementFlag(objectReference)**: 返回对象移动状态
- **ObjectMover(objectReference)**: 返回对象所在移动平台
- **ObjectName(objectReference)**: 返回对象名称
- **ObjectPosition(objectReference)**: 返回对象位置
- **ObjectRawPosition(objectReference)**: 返回对象原始位置
- **ObjectRawRotation(objectReference)**: 返回对象原始旋转
- **ObjectRotation(objectReference)**: 返回对象旋转
- **ObjectSkinnable(objectReference)**: 检查对象是否可剥皮
- **ObjectSkinningType(objectReference)**: 返回对象剥皮类型
- **ObjectSpecializationID(objectReference)**: 返回对象专精 ID
- **ObjectType(objectReference)**: 返回对象类型
- **ObjectWorldPosition(objectReference)**: 返回对象世界位置
- **Objects([filterTypeID])**: 获取所有对象列表
- **partydensitycentroid(unit, range)**: 返回队伍成员最密集区域
- **partyHealthAround(unit, range)**: 返回单位周围队伍成员血量百分比
- **partyMembersAround(unit, range)**: 返回单位周围队伍成员数量
- **power([powerType, unitID])**: 返回单位指定能量类型值
- **powerfor({spellIDs})**: 检查是否有足够能量施放技能
- **powermax([type, target])**: 返回单位最大能量值
- **powerpercent([type, target])**: 返回单位能量百分比
- **powertype([unit])**: 返回单位能量类型
- **raid()**: 检查是否在团队中
- **rank(spellId, rank)**: 返回指定等级的技能 ID
- **ReadFile(path)**: 读取文件内容
- **ReadZipEntry(zip, path)**: 读取压缩文件内容
- **ReleaseEmpoweredSpell(spellID)**: 释放强化技能
- **resolveSpellID([spellID])**: 返回正确的技能 ID
- **RightClickObject(objectReference)**: 右键点击对象
- **RotateVector(x, y, z, theta, distance)**: 旋转向量并计算距离
- **runes()**: 返回玩家可用符文数量
- **ScreenToWorld(x, y, hitFlags)**: 将屏幕坐标转换为世界坐标
- **SendMovementHeartbeat()**: 发送移动心跳包
- **SetForbiddenFrame(frame)**: 设置框架为非禁止状态
- **SetHeading(theta)**: 立即设置玩家朝向
- **SetLastHardwareActionTime(number)**: 设置最后交互时间
- **SetMouseover(objectReference)**: 设置鼠标悬停对象
- **SetNPCObject(objectReference)**: 设置目标对象
- **SetPitch(theta)**: 设置俯仰角度
- **SetTargetObject(objectReference)**: 设置目标对象
- **smartinterruptible([unit, percent])**: 检查目标是否可打断
- **solo()**: 检查是否独玩
- **spellInRange(spell, [unit])**: 检查单位是否在技能范围内
- **spellisspell(spellA, spellB)**: 检查两个技能是否相同
- **SpreadNotDebuffed(range, debuffId, spelltocast)**: 对无减益的单位施加减益
- **stagger([target])**: 返回目标的醉拳百分比
- **steal()**: 检查目标是否有可偷取的增益
- **talent(row, column)**: 检查玩家是否拥有指定天赋
- **tank()**: 返回最佳坦克单位
- **totem('spell name')**: 检查图腾是否激活
- **TraceLine(x1, y1, z1, x2, y2, z2, hitFlags)**: 检查两点间视线
- **ttd(unit)**: 返回单位预计死亡时间
- **ttt([mover, target])**: 返回移动者到目标的时间
- **UnitDuelTeam(objectReference)**: 返回对象决斗团队
- **UnitIsMounted(objectReference)**: 检查对象是否骑乘
- **UnitIsSitting(objectReference)**: 检查对象是否坐下
- **UnitItemLevels(objectReference)**: 返回对象装备等级
- **UnitShapeShiftID(objectReference)**: 返回对象变身 ID
- **usable(itemID)**: 检查物品是否可用
- **useitem(itemid)**: 使用指定物品
- **WorldToScreen(x, y, z)**: 将世界坐标转换为屏幕坐标
- **WouldInject()**: 检查是否可注入 Lua

## 工具模块 API
### Commands
- **Commands:New(prefix)**: 创建新命令
- **Commands:Register(command, handler, help, arguments)**: 注册命令
```lua
local Tinkr = ...
local Commands = Tinkr.Util.Commands
local awesome = Commands:New('awesome')
awesome:Register('test', function(option) print(option) end, 'A simple test command', {'option'})
awesome:Register({'foo','bar'}, function(baz, bang) print(baz, bang) end, 'Another test command', {'baz','bang'})
```

### Config
- **Config:New(name)**: 创建新配置
- **Config:Read(key, default)**: 读取配置值
- **Config:Write(key, value)**: 写入配置值
```lua
local Tinkr = ...
local Config = Tinkr.Util.Config
local my_config = Config:New('my_config')
my_config:Write('foo', 'something')
my_config:Write('bar', 'awesome')
local foo = my_config:Read('foo', 'default value')
local baz = my_config:Read('baz', 'default value')
print(foo) -- something
print(baz) -- default value
```

### Crypto
- **AES:IV()**: 创建随机初始化向量
- **AES:Encrypt(input, key, iv)**: 加密数据
- **AES:Decrypt(input, key, iv)**: 解密数据
- **Hash:MD5(input)**: MD5 哈希
- **Hash:SHA1(input)**: SHA1 哈希
- **Hash:SHA2(input)**: SHA2 哈希
- **Hash:SHA256(input)**: SHA256 哈希
- **Hash:SHA3(input)**: SHA3 哈希
- **Hash:HMAC(algo, input, key)**: HMAC 哈希
- **Base64:Encode(input)**: Base64 编码
- **Base64:Decode(input)**: Base64 解码
```lua
local Tinkr = ...
local AES = Tinkr.Util.Crypto.AES
local Common = Tinkr.Common
local iv = AES:IV()
local key = Common.RandomVariable(32)
local input = 'this is my encrypted message'
local encrypted = AES:Encrypt(input, key, iv)
local decrypted = AES:Decrypt(encrypted, key, iv)
print(decrypted == input)
local Hash = Tinkr.Util.Crypto.Hash
local hashed = Hash:SHA3('some value to be hashed')
print(hashed)
local Base64 = Tinkr.Util.Crypto.Base64
local input = "this is a message to be encoded"
local encoded = Base64:Encode(input)
local decoded = Base64:Decode(encoded)
print(decoded == input)
```

### Detour
- **Detour:SmoothPath(path)**: 平滑路径
- **Detour:FlattenPath(path)**: 扁平化路径
- **Detour:PreparePath(path)**: 准备路径
- **Detour:ToObject(object)**: 导航到对象
- **Detour:ToPosition(destX, destY, destZ)**: 导航到位置
- **Detour:Raw(startX, startY, startZ, destX, destY, destZ, mapID)**: 原始导航
- **Detour:ConnectWaypoints(route, close)**: 连接路径点
- **Detour:DebugPath(path, color)**: 调试路径
- **Detour:DebugTiles()**: 调试瓦片

### Draw
- **Draw:WorldToScreen(wx, wy, wz)**: 世界坐标转屏幕坐标
- **Draw:CameraPosition()**: 获取相机位置
- **Draw:Map(value, fromLow, fromHigh, toLow, toHigh)**: 映射值
- **Draw:SetColor(r, g, b, a)**: 设置颜色
- **Draw:SetColorRaw(r, g, b, a)**: 设置原始颜色
- **Draw:SetAlpha(a)**: 设置透明度
- **Draw:Distance(ax, ay, az, bx, by, bz)**: 计算3D距离
- **Draw:Distance2D(x1, y1, x2, y2)**: 计算2D距离
- **Draw:SetWidth(width)**: 设置线宽
- **Draw:RotateX/Y/Z(cx, cy, cz, px, py, pz, r)**: 旋转坐标
- **Draw:Line(x1, y1, z1, x2, y2, z2, maxD)**: 绘制3D线
- **Draw:LineRaw(x1, y1, z1, x2, y2, z2)**: 绘制原始3D线
- **Draw:Line2D(sx, sy, ex, ey)**: 绘制2D线
- **Draw:Circle(x, y, z, radius, steps)**: 绘制圆
- **Draw:Cylinder(x, y, z, radius, height)**: 绘制圆柱
- **Draw:Array(vectors, x, y, z, rotationX, rotationY, rotationZ)**: 绘制向量数组
- **Draw:Text(text, font, x, y, z)**: 绘制文本
- **Draw:Texture(config, x, y, z, alphaA)**: 绘制纹理
- **Draw:ClearCanvas()**: 清除画布
- **Draw:Update()**: 更新画布
- **Draw:Helper()**: 辅助函数
- **Draw:Enable()**: 启用绘制
- **Draw:Disable()**: 禁用绘制
- **Draw:Enabled()**: 检查绘制状态
- **Draw:Sync(callback)**: 同步绘制
- **Draw:HexToRGB(hex)**: 转换十六进制颜色
- **Draw:SetColorFromObject(object)**: 从对象设置颜色
- **Draw:GetColorFromObject(object)**: 获取对象颜色
- **Draw:New(canvas)**: 创建新画布
- **Draw:Arc(x, y, z, size, arc, rotation)**: 绘制弧
- **Draw:Rectangle(x, y, z, w, l, rot)**: 绘制矩形

### Evaluator
- **Evaluator:RandomVariable(length)**: 生成随机变量
- **Evaluator:InjectGlobals(func)**: 注入全局变量
- **Evaluator:LoadEncrypted(file, args)**: 加载加密文件
- **Evaluator:Load(input, ...)**: 加载输入
- **Evaluator:LoadString(input, args, path)**: 加载字符串
- **Evaluator:CallProtectedFunction(func, ...)**: 调用受保护函数
- **Evaluator:LoadEncBuffer(buffer, args, path)**: 加载加密缓冲
- **Evaluator:EmplaceGlobals(global)**: 嵌入全局变量

### Event
- **Event:Register(event, callback)**: 注册事件
- **Event:Emit(event, ...)**: 触发事件
- **Event:RemoveListener(id, callback)**: 移除监听器
- **Event:GetListeners(event)**: 获取事件监听器

### ObjectManager
- **ObjectManager:Filter(ObjectType)**: 过滤对象类型
- **ObjectManager:UnitCanBeLooted(object)**: 检查单位是否可拾取
- **ObjectManager:UnitCanBeSkinned(object)**: 检查单位是否可剥皮
- **ObjectManager:hasLineOfSight(objectA, objectB)**: 检查对象间视线
- **ObjectManager:Objects(ObjectType)**: 获取指定类型对象
- **ObjectManager:Missiles()**: 获取投射物

### Routine
- **Routine:Inject(metaTable, libName, Class)**: 注入方法
- **Routine:InjectLib(...) / InjectLibrary(...)**: 注入库
- **Routine:LoadLib(...) / LoadLibrary(...)**: 加载库
- **Routine:RegisterLib(...) / RegisterLibrary(...)**: 注册库
- **Routine:RegisterRoutine(callback, class, spec)**: 注册例程
- **Routine:RegisterSpellbook(spellbook, class)**: 注册技能书
- **Routine:LoadForSpec()**: 加载专精例程
- **Routine:LoadRoutine(id)**: 加载指定例程
- **Routine:RegisterLocalRoutines()**: 注册本地例程
- **Routine:RegisterLocalSpellbooks()**: 注册本地技能书
- **Routine:RegisterLocal()**: 注册本地内容
- **Routine:Toggle(state)**: 切换例程状态
- **Routine:Enable()**: 启用例程
- **Routine:Disable()**: 禁用例程
```lua
local Tinkr = ...
local Routine = Tinkr.Routine
local MyMethods = {}
function MyMethods.Internal()
    print(AimedShot)
    print('internal', UnitName(Object('player')))
    print('internal', health('player'))
end
function MyMethods.Test(a)
    print(a, UnitName(Object('player')))
    print(a, health('player'))
    MyMethods.Internal()
end
Routine:Inject(MyMethods, 'Test_', Routine.Classes.Hunter)
Routine:RegisterRoutine(function()
    Test_Test('Test_Test')
    Test('Test')
    print(AimedShot)
end, Routine.Classes.Hunter, 'aname')
```

### Script
- **Script:Load(file)**: 加载脚本
- **Script:Test(file)**: 测试脚本
- **Script:Autoload()**: 自动加载脚本
- **Script:HookAddons()**: 钩子插件
- **Script:UpgradeLauncher()**: 升级启动器

### Vector3
- **Vector3:__add/sub/mul/div/eq/unm/tostring()**: 向量运算
- **Vector3:Zero()**: 返回零向量
- **Vector3:Length()**: 返回向量长度
- **Vector3:Set(x, y, z)**: 设置向量坐标
- **Vector3:Normal()**: 返回单位向量
- **Vector3:Dot(vector)**: 点积
- **Vector3:Cross(vector)**: 叉积
- **Vector3:Distance(vector)**: 计算向量距离
- **Vector3:Facing(vector)**: 计算向量朝向
- **Vector3:New(x, y, z)**: 创建新向量

### Zlib
- **Deflate(string, level)**: 压缩字符串
- **Inflate(data)**: 解压数据
```lua
local input = ('This will be my long string.'):rep(10000)
local compressed = Deflate(input, 9)
local decompressed = Inflate(compressed)
print(decompressed == input and "Success!" or "Failure!")
```