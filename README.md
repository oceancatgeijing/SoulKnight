# Soul Knight实习复刻日志

##### CAUTION：

此项目是冰岩2019秋招实习的开发项目，为了方便大家阅读会意，commit message将会使用中文，如果clone到本地后发现git log出现乱码，在控制台设置git的提交信息和输出为utf-8编码即可

每一次commit message都会记录在推送当天的日志中（*‘像这样’*），甚至还会有解说x

##### TODO List：

- 完成基本点后重写Camera和FocalPos的脚本，用animation curve代替两层插值



### ---------9.29----------

​	***‘Init push’***	

​	***‘加入了README.md作为实习日志’***

​	初始化了本地和网络仓库，目前是一个空的unity 2d项目

​	今天火速下载试玩了元气骑士，新建了空项目，开始思考游戏类的架构，甚至找了找有无合适的免费的asset。

​	元气骑士的战斗房间之间有长走廊连接，根据目测，带有敌人的大房间宽度和走廊的长度差不多大，而不带有敌人的房间较小。因此推测，一关的整个地图空间会分配成多块N x N的空间，每个空间容纳一个实例，实例由代码根据一定的逻辑随机生成，使多个单位间的通道相连接，（暂定）基本的实例有“走廊”，“战斗房间”，“非战斗房间”，和“空”这几种。在非战斗状态，相机会平滑跟随玩家，而在战斗房间，相机的焦点则变为玩家与敌人位置的一种加权平均（姑且猜测是加权），使得让玩家处于相对中间位置的同时也能对敌人有更好的视线。明天再猛男开工，寻思着给未来几天的自己写一个适当的todo list

### ---------9.30---------

***‘加入Asset Store的免费素材，完成了角色行走控制，和能在多个目标间smoothly blend位置的相机’***

​	UnityLearn上面有一个roguelike游戏的开发教程，不过那个教程实现的很入门，因此只是把里面的美术和音效素材拿来用，凡是没有用的（讲师制作的prefab，脚本）都放进了_Complete-Game文件夹中，真正的猛男都会自己做prefab自己写脚本（迫真。

​	但是那个roguelike教程的美术素材是真的fine，现在全都是mine。相机实现的比较简单，就是用了两层对xy轴的lerp，如果只用一层lerp会因为新加入的加权个体而发生相机位置突变。刚刚又玩了一下游戏，发现相机会格外往自动瞄准的敌人身上聚焦，鉴于先把基本点做完的理念，俺先假装没有发现这个feature

***‘继续加入免费的武器素材，working on武器类和武器中的枪类’***

​	现在已经可以像元气骑士中一样向四周发射子弹，但是鼠标指针只能像摇杆一样使用，当角色不在屏幕中央时，射出的子弹并不能准确射中光标所指的地方。不甘心光标只能做到像摇杆一样，不然俺在光标处加入的准星GUI就白搞了，而且这样pc端的体验也会差。寻思着明天给子弹的射出速度加入一个修正偏移量，不论角色是否在屏幕中央都能射出打中准星的子弹>_<

​	删除了_Complete-Game文件夹和一些用不上的文件

​	猛男今晚写不完了（流泪猫猫头.jpg)

### ----------10.1----------

***‘完成了Weapon类，实现了枪械射击，修正了子弹方向偏移的问题’***

​	今天遇到一个有意思的bug，在已经对射击方向取normalized后，鼠标离角色越近，子弹速度越慢，鼠标越远则子弹速度越快，原来是因为俺对Vector3表示的方向单位化，而子弹刚体的速度是Vector2，因此让单位向量的z分量损失了。虽然并不是很有技术含量的的bug，但是感觉很有意思。调整了武器sprite的pivot，并且让子弹在武器前端生成，嗯...使用的子弹贴图其实是导弹的图片，明明是高清图，缩小很多倍后竟然有点像素的感觉

​	接下来继续做伤害判定，同时修改一下武器类的继承关系（下午补充：自己并没有降低类之间的耦合度，于是revert惹）。经过这两天的（疯狂看别人地牢生成算法的案例），逐渐对怎么生成地牢有了自己的想法。猛男想搞快点，赶快把几个地牢房间的prefab做好，开始写比较有意思的（地牢和房间内容随机生成）部分

***‘完成敌人脚本中判定子弹的内容’***

​	接下来开始重新写一下武器类的继承关系，先commit一下存档，万一要回退呢。（下午确实回退了）

​	继续删除了一些垃圾文件，clean and nice

***‘完成挥刀动作，刀类武器的伤害判定’***

​	今天上午：我可以，写得完

​	晚上：我不行，这个乐色代码不可以，写个锤子，我根本不会c#，写的什么幼儿代码

### ------------10.2-------------

***‘完成敌人对主角的追击、攻击、以及徘徊行为’***

​	为了方便后续开发，敌人对主角的伤害判定留到后面再写，敌人应该也是可以发弹幕的，好麻烦，晚一点再写，下一步做一下地牢

***‘大致完成随机生成地牢的方法’***

​	可以生成随机地牢，但是出现了新bug，在周边大量地牢collider的情况下相机的平滑跟随算法出现问题，当初为了有更好的敌人视野，相机使用计算周边collider位置加权的方式决定焦点位置，但是大量地牢墙壁的collider扰乱的正常焦点的计算。而且目前的生成地牢算法还有一定几率出现端口不封闭的情况，同时因为“走廊”和“战斗房间”算作同等地位的单位，有出现连续房间或者连续走廊的情况（虽然理论上生成随机房间就可以凑数，但是咱复刻的是元气骑士，不是以撒，得生成正常走廊）明天准备重新做一下房间的prefab，让房间的入口带有半截走廊，来让走廊和房间数目和谐

​	好累，这周的番还没看，怎么还不到7号，难道当社会人就是这种感觉（流泪猫猫头.jpg）

### ---------------10.3-----------------

***'Update gitignore'***

***'Remove cached ProjectVersion.txt'***

***'Unfinished'***

​	今天去CurryPseudo的寝室玩了一，更新了gitignore，为了防止不同版本冲突而移除对ProjectVersion.txt的记录，玩的很开心，不过还没解决地牢开口的问题（因为我生成地牢的算法太猎奇了，不容易维护的那种）

***‘仍然没有修复地牢不封闭的问题’***

​	如题，此次commit是为了push到gh，因为要回寝室了

### -------------10.4-------------

***‘修复地牢不封闭bug，会留下一节封闭的残留走廊’***

​	早上做了个梦，梦里用一种物理方法解决了地牢不封闭的问题，起床后一实现，确实能用。原理如下：每个房间的走廊末端都由一个和墙壁长得一样的刚体封闭，生成地牢的时候，两个房间末端的刚体碰撞，则这两个封闭的刚体都会被摧毁，让两个房间联通。如果房间的走廊末端没有继续生成房间，则刚体不会被摧毁，继续留在那里，用来封闭玩家。

​	这样做的结果就是，会留下一段死胡同走廊，但是好处是，这样让随机地牢可以生成环形路线，并且生成规模较小的地牢的几率增大（俺的生成算法无法控制生成规模，比较猎奇）

​	接下来开始做基本的gameplay，安排一个十月加急，害怕做不完了

​	这两天发现一边听bighouse/hardstyle系的电音一边开发非常养生，因为一边开发一边在座位上情不自禁摇头晃脑，对腰椎和颈椎的压力比较小（因为没有长时间在不利姿势静止），而且bighouse和hardstyle（对俺）非常提神（后摇和jazz hiphop就让俺昏昏欲睡）