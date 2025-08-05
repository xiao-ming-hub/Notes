# 在 Arch Linux 上安装 ManimGL 有多简单 - 2022.10.3
首先装一下依赖：
```sh
pacman -S ffmpeg
pacman -S texlive-{bin, core, fontsextra, langchinese, latexextra, science}
```
开始安装：
```sh
git clone https://github.com/3b1b/manim 3b1b-manim
python -m venv manimgl.venv
source manimgl.venv/bin/activate
pip install -e 3b1b-manim
manimgl 3b1b-manim/example_scenes.py OpeningManimExample  # Test
```
安装时你可能会遇到一些问题，比如 pip 编译时报错 “找不到头文件 glib.h”，这时可以用 alias 强制添加包含路径参数。
```sh
alias gcc='gcc `pkg-config glib-2.0 --cflags`'
```
