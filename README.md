[README.md](https://github.com/user-attachments/files/33479830/README.md)
# Siri Island

A WebGL recreation of the iOS 27 Siri animation in the Dynamic Island: the Liquid Glass drop, the voice-driven light band, the thinking dots and the answer card.

iOS 27 灵动岛 Siri 动画的 WebGL 复刻：液态玻璃水滴、跟随声音的光带、思考小球和回答卡片。

![Siri Island demo](demo.gif)

## Run / 运行

No build step and no dependencies. Open `index.html` in a browser that supports WebGL 2 (Chrome, Edge, Safari 15+, Firefox).

无需构建和依赖，用支持 WebGL 2 的浏览器直接打开 `index.html` 即可。也可以直接部署到 GitHub Pages。

The microphone only works on `https://` or `localhost`. Opened as a local file, use “模拟说话” (simulated voice) instead.

麦克风需要在 `https://` 或 `localhost` 下使用；直接双击打开文件时，用“模拟说话”驱动光带。

## Controls / 操作

| Key / 操作 | Action / 作用 |
|---|---|
| `Space` 空格 | Play the full sequence at full volume / 以最大音量播放完整流程 |
| `H` | Hide the panel for clean recording / 隐藏面板，方便录屏 |
| Drag an image onto the screen / 把图片拖到屏幕上 | Use your own screenshot as the background / 使用自己的手机截图作为背景 |

The panel switches between five states (idle, listening, thinking, searching, answer), toggles the phone frame, and exposes every parameter of the light band. Listening and answer each have their own parameter set. “复制参数” copies the current values as JSON.

面板可以切换五个状态（待机、聆听、思考、查询、回答）、开关手机边框，并调整光带的全部参数。聆听和回答各有一套独立参数，“复制参数”会把当前数值复制为 JSON。

## How it works / 实现要点

Everything is drawn in a single fragment shader. The screen is black by default; drop in your own screenshot to see the glass refract it.

所有效果都在一个片元着色器里。屏幕默认纯黑，上传自己的截图后可以看到玻璃对它的折射。

1. **One shape, many states.** The drop, pill and card are the same rounded rectangle animated with springs. On entry the shape grows first, then the glass clears, then the light comes on.
   **同一个形状连续变形。** 水滴、胶囊、卡片是同一个圆角矩形，用弹簧动画驱动。入场时先长形状，再透玻璃，最后亮光。
2. **Liquid Glass lens.** The top half is opaque black and merges with the island. The bottom half is a smoky lens that magnifies the screen, compresses it at the rim and splits RGB slightly for dispersion.
   **液态玻璃透镜。** 上半是和灵动岛融为一体的纯黑，下半是烟色透镜：放大底下的画面，在边缘压缩，并错开 RGB 做色散。
3. **Additive color mixing.** The light bands are added like real light (red + blue = magenta, all three = white), then tone-mapped with `1 - exp(-x)` so overlaps glow instead of clipping.
   **加色混合。** 色带像真实的光一样相加（红 + 蓝 = 品红，三色叠满 = 白），再用 `1 - exp(-x)` 做柔和过曝，避免叠多了变成生硬的白块。
4. **The rim catches the light.** Near the glass edge the band is sampled from further inside and pulled toward its own height, so its color wraps along the rim.
   **边缘折射光带。** 靠近玻璃边缘时，从更靠里的位置取样并向色带高度拉拢，色带的颜色就沿着边缘晕开。
5. **Thinking dots.** Six glowing dots with chromatic halos breathe one after another, then gather irregularly and vanish before the answer card opens.
   **思考小球。** 六个带色散光晕的小点依次呼吸，找到答案时不规则地收拢、消失，再展开回答卡片。

