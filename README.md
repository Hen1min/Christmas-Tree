# 圣诞树

一棵会发光的圣诞树：螺旋灯带、彩球、闪光星芒、颜色一直在渐变、地上摆着四个礼盒，拖动可以水平旋转。

- 纯静态页面，一个 `index.html` 就是全部
- 三维库放在 `vendor/three.module.js`，**不依赖任何 CDN**，断网 / 国内访问都能开
- 手机竖屏会自动拉远视角、把礼盒往中间收，双指可缩放

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
| 彩球颜色 | `BAUBLE_COLORS` 数组 |
| 彩球个数 | 彩球循环的 `i < 16` |
| 闪光星芒个数 | 星芒循环的 `i < 18` |

## 场景里都有什么

- 树叶：20000 个点填成的体积锥体，颜色用 shader 按高度算色相，整体随时间走完色环
- 灯带：2400 个点绕 13 圈的螺旋，光点像电流一样向上流动
- 彩球：16 个真实小球（`SphereGeometry`），自发光 + 灯光，明暗呼吸
- 星芒：18 个四角星 sprite，各自带相位闪烁
- 树顶：光球 + 三道旋转的十字 flare
- 礼盒：4 个，带交叉缎带、蝴蝶结、地面暖光
- 其他：飘雪、背景星空、地面辉光、树身外围柔光

## 结构

```
index.html                 页面 + 场景 + 交互（全部逻辑都在这一个文件里）
vendor/three.module.js     three.js r160 本地副本
.github/workflows/pages.yml  推 main 自动发布到 GitHub Pages
start.bat                  本地起服务
preview.png                截图
```
