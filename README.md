# 圣诞树

一棵会发光的圣诞树：螺旋灯带、彩球、闪光星芒、颜色一直在渐变、地上摆着四个礼盒（**点一下开盖，再点一下盖上**），拖动可以水平旋转。

- 纯静态页面，一个 `index.html` 就是全部
- 三维库放在 `vendor/three.module.js`，**不依赖任何 CDN**，断网 / 国内访问都能开
- 手机竖屏会自动拉远视角、把礼盒往中间收，双指可缩放
- 礼盒点一下翻盖打开，再点一下合上；盒体和盖子都是 5 块面拼的**空壳**，打开时盒内和盖子内腔都是空的
- 开盖时盒子里会**随机蹦出一个 Q 版小玩具**（巧克力 / 小飞机 / 玩具车 / 大哥大 / 糖果 / 伸缩拳套），合盖时缩回盒里，下次再开重新随机
- 右下角 ♪ 开关背景音乐：真实录音 `audio/jingle-bells.mp3`（US Air Force Band of Mid-America，2020 年版），循环播放、点开时 1.4 秒淡入

## 本地预览

双击 `start.bat`，浏览器会打开 http://127.0.0.1:8766/

> 不能直接双击 `index.html`（`type="module"` + importmap 在 `file://` 下会被浏览器拦掉）。

## 线上地址

https://hen1min.github.io/Christmas-Tree/

推送 `main` 后由 `.github/workflows/pages.yml` 自动发布，约 1 分钟生效。

## 想改的地方

| 想改什么 | 改哪里 |
| --- | --- |
| 顶部文字 | `index.html` 里 `<h1 id="title">` 的内容 |
| 树高 / 底宽 | 脚本里的 `const H = 3.5;` 和 `const R = 1.45;` |
| 颜色渐变速度 | `animate()` 里的 `uHueBase = (0.28 + t * 0.012) % 1`，改 `0.012` |
| 自转速度 | `const AUTO_SPIN = 0.22;` |
| 灯带明暗 | 灯带循环里的 `const k = 0.16 + 0.62 * wave;` |
| 礼盒数量 / 颜色 / 位置 | `giftSpecs` 数组，每项有 `w h d x z ry color ribbon` |
| 开盖角度 | `animate()` 里 `ud.lidPivot.rotation.x = -ud.openAmount * 1.75`，改 `1.75` |
| 彩球颜色 | `BAUBLE_COLORS` 数组 |
| 彩球个数 | 彩球循环的 `i < 16` |
| 闪光星芒个数 | 星芒循环的 `i < 18` |
| 玩具种类 / 造型 | `makeChocolate / makePlane / makeCar / makeBrickPhone / makeCandy / makeSpringFist`，都在 `TOY_MAKERS` 里 |
| 玩具蹦出来的高度 | `group.userData.toyY1 = bodyH + h * 0.45` |
| 玩具大小 | `const toyScale = (0.85 * Math.min(w, d)) / 0.26` |
| 玩具初始朝向 | `TOY_FACE` 数组，六个角度对应六个玩具 |

## 场景里都有什么

- 树叶：20000 个点填成的体积锥体，颜色用 shader 按高度算色相，整体随时间走完色环
- 树干：真实圆柱（`CylinderGeometry`），树冠整体抬高 0.42 露出下面一截，顶端顶点色渐变到近黑、融进树冠暗部
- 灯带：2400 个点绕 13 圈的螺旋，光点像电流一样向上流动
- 彩球：16 个真实小球（`SphereGeometry`），自发光 + 灯光，明暗呼吸
- 星芒：18 个四角星 sprite，各自带相位闪烁
- 树顶：光球 + 三道旋转的十字 flare
- 礼盒：4 个，盒体和盖子都是「一块面板 + 4 面侧壁」拼的空壳（盒体开口朝上，盖子是朝下的浅罩），所以开盖能看到盖子的内腔；带缎带、蝴蝶结、地面暖光，盖子铰链在后沿
- 玩具：6 个 Q 版小物件全用基础几何体拼（巧克力块、螺旋桨小飞机、圆顶小车、大哥大、两头拧的糖果、弹簧拳套），开盖随机挑一个蹦出来、轻微上下浮动，合盖时缩小退回盒内
- 音乐：`Jingle Bells` 主旋律 + 低音 pad，`MELODY` / `BASS_PATTERN` 数组里写音符，`TEMPO` 改速度
- 其他：飘雪、背景星空、地面辉光、树身外围柔光

## 结构

```
index.html                 页面 + 场景 + 交互（全部逻辑都在这一个文件里）
vendor/three.module.js     three.js r160 本地副本
audio/jingle-bells.mp3     背景音乐（真实录音，3.2 MB，preload=metadata 不占首屏）
.github/workflows/pages.yml  推 main 自动发布到 GitHub Pages
start.bat                  本地起服务
preview.png                截图（合盖）
preview-open.png           截图（开盖，能看到空腔）
preview-lid.png            截图（盖子特写，能看到盖子也是空心的）
changelog.html             更新日志页（点页面右下角「更新日志」进）
```

## 更新日志页

`changelog.html` 是独立的静态页，页面右下角「更新日志」按钮（`#logBtn`）链过去。

发版时手动在 `changelog.html` 最上面加一段 `<article class="entry">`（最新的那个加 `latest` 类）：

```html
<article class="entry latest">
  <span class="date">2026-12-01</span><span class="tag">最新</span>
  <ul>
    <li>这次改了什么</li>
  </ul>
</article>
```

记得把上一段的 `latest` 类和 `<span class="tag">最新</span>` 去掉。

## 背景音乐来源

`audio/jingle-bells.mp3` —— *Jingle Bells*，United States Air Force Band of Mid-America（2020），取自 Wikimedia Commons：

<https://commons.wikimedia.org/wiki/File:Jingle_Bells_(2020)_-_Starlifter_and_Roots_in_Blue_-_United_States_Air_Force_Band_of_Mid-America.mp3>

美国联邦政府雇员在职作品在美国属公有领域（17 U.S.C. §105），可以自由使用。曲子本身（James Lord Pierpont, 1857）也早已是公有领域。

### 本地换成自己的音频（不入库）

`index.html` 里的 `<audio>` 有两个 `<source>`：先找 `audio/local.mp3`，找不到才用仓库自带的 `jingle-bells.mp3`。
所以想在自己机器上听别的版本，只要把文件放到 `audio/local.mp3`——`.gitignore` 已经忽略了它，不会推到 GitHub。

公开站点会继续用仓库里那首公有领域的版本。

`preload="metadata"` 只取时长，点 ♪ 才真正下载整个文件。

## 背景房间

背景是 `drawRoom()` 用 Canvas 2D 现画的（`#room` 画布，z-index 0），WebGL 画布改成 `alpha: true` 叠在上面，所以树的辉光还是叠加在墙上的效果。

画的内容：墙纸圆点 → 顶部暖光 → 树后椭圆暗影 → 窗户 → 左边相框 → 顶部两段花环和彩灯 → 踢脚线 + 木地板（透视地板缝）+ 树底投影 → 四周压暗。

**窗外景色跟本地时间走**（`drawRoom()` 里按 `new Date()` 的小时判断）：

| 时段 | 窗外 |
| --- | --- |
| 06:00–17:00 | 蓝天、太阳带光晕、白云、白雪、雪人 |
| 17:00–19:30 | 橙紫渐变、低太阳、暖色云、粉色雪地 |
| 19:30–06:00 | 深蓝夜空、星星、月牙、雪人 |

房间整体也会跟着淡淡变一下：白天加一点暖色，夜里加一点冷紫。页面开着不动的话，每 60 秒会自动重画一次，天色会自己变。

想调：

| 想改什么 | 改哪里 |
| --- | --- |
| 墙的颜色 | `drawRoom()` 里 `wall` 渐变的三个色标 |
| 窗户大小 / 位置 | `winW`、`winH`、`winX`、`winY` |
| 时段分界 | `drawRoom()` 里 `hourNow >= 6 && hourNow < 17`（白天）和 `< 19.5`（黄昏）这两处阈值 |
| 花环 | 结尾两行 `swag(W * 0.02, W * 0.34, H * 0.075)` |
| 地毯 | `rugRx`、`rugRy`、三个 `rug.addColorStop` |
| 换回纯黑背景 | 把 `renderer.setClearColor(0x000000, 0)` 改回 `(0x03050a, 1)`，并删掉 `#room` 画布 |

