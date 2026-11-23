# 圣诞树

一棵会发光的圣诞树：螺旋灯带、彩球、闪光星芒、颜色一直在渐变、地上摆着四个礼盒（**点一下开盖，再点一下盖上**），拖动可以水平旋转。

- 纯静态页面，一个 `index.html` 就是全部
- 三维库放在 `vendor/three.module.js`，**不依赖任何 CDN**，断网 / 国内访问都能开
- 手机竖屏会自动拉远视角、把礼盒往中间收，双指可缩放
- 礼盒点一下翻盖打开，再点一下合上；盒体和盖子都是 5 块面拼的**空壳**，打开时盒内和盖子内腔都是空的
- 开盖时盒子里会**随机蹦出一个 Q 版小玩具**（巧克力 / 小飞机 / 玩具车 / 大哥大 / 糖果 / 伸缩拳套），合盖时缩回盒里，下次再开重新随机
- 右下角 ♪ 可以开关背景音乐（`Jingle Bells`，用 WebAudio 现场合成，没有音频文件）

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
.github/workflows/pages.yml  推 main 自动发布到 GitHub Pages
start.bat                  本地起服务
preview.png                截图（合盖）
preview-open.png           截图（开盖，能看到空腔）
preview-lid.png            截图（盖子特写，能看到盖子也是空心的）
preview-notify.png         截图（左下角的更新日志提示）
sw.js                      Service Worker，只处理系统通知的点击（不缓存任何东西）
icon-192.png               系统通知用的小图标
```


## 更新通知（没有后端）

`index.html` 顶部有 `SITE_VERSION` 和 `CHANGELOG` 两个常量。**每次发版**：

1. 把 `SITE_VERSION` 改成新日期，例如 `'2026-12-01'`
2. 在 `CHANGELOG` 数组最前面加一条 `{ v: '2026-12-01', notes: ['这次改了什么', '还有这个'] }`
3. push

访客侧的行为：

- 第一次打开：只记录版本基线，不弹任何东西
- 下一次打开（还没表过态）：延迟 2.2 秒弹出「以后更新，要通知你吗？」
  - 点「不用了」：存 `xmas-notify = off`，以后再也不问
  - 点「好呀」：请求浏览器通知权限，拿到权限后从当前版本开始记
- 之后每次发版，已同意的访客下次打开页面时：左下角弹出更新日志 + 发一条系统通知（点了会聚焦到页面）
- 右下角 🔔 / 🔕 按钮随时开关；关掉再打开会以当前版本为新基线

状态都存在 `localStorage`：`xmas-visited`（是否来过）、`xmas-notify`（on/off）、`xmas-seen`（看到的版本）。

> 局限：这是纯前端方案，没有推送服务器，所以只能做到「访客打开页面时」收到更新日志，无法在浏览器完全关闭时主动唤醒。真要做后台推送，需要 VAPID + 一个推送后端（或 OneSignal / Cloudflare Worker + KV）。
