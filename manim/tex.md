# [ManimGL 源码解读] Tex 如何渲染公式 - SunJul31 2022
`Tex` 能根据传入的 `tex_strings` 渲染公式，根据 `tex_to_color_map` 设置颜色，根据 `tex_strings`、`isolate` 和 `tex_to_color_map` 拆出 `submobjects`（比如用于 `TransformMatchingTex` ~~`TransformMatchingShapes` 不香吗~~）。
```py
# manimlib.mobject.svg.tex_mobject.Tex
class Tex(SingleStringTex):
    CONFIG = {...}

    def __init__(self, *tex_strings: str, **kwargs):
        digest_config(self, kwargs)
        self.tex_strings = self.break_up_tex_strings(tex_strings)
        full_string = self.arg_separator.join(self.tex_strings)
        super().__init__(full_string, **kwargs)
        self.break_up_by_substrings()
        self.set_color_by_tex_to_color_map(self.tex_to_color_map)

        if self.organize_left_to_right:
            self.organize_submobjects_left_to_right()
    
    ...
```
`digest_config` 处理参数，接着调用 `break_up_tex_strings` 将 `tex_strings` 进一步按照 `self.isolate` 和 `self.tex_to_color_map` 拆分。
```py
# manimlib.mobject.svg.tex_mobject.Tex.break_up_tex_strings
def break_up_tex_strings(self, tex_strings: Iterable[str]) -> Iterable[str]:
    # Separate out any strings specified in the isolate
    # or tex_to_color_map lists.
    substrings_to_isolate = [*self.isolate, *self.tex_to_color_map.keys()]
    if len(substrings_to_isolate) == 0:
        return tex_strings
    patterns = (
        "({})".format(re.escape(ss))
        for ss in substrings_to_isolate
    )
    pattern = "|".join(patterns)
    pieces = []
    for s in tex_strings:
        if pattern:
            pieces.extend(re.split(pattern, s))
        else:
            pieces.append(s)
    return list(filter(lambda s: s, pieces))
```
`self.tex_strings` 被拼在一起（中间用 `self.arg_separator` 连接，通常是 `""`），存在 `full_string` 里。调用 `SingleStringTex` 生成 SVG。调用 `self.break_up_by_substrings`。这个函数大概是，根据 `self.tex_strings` 再分别调用 `SingleStringTex` 渲染 SVG 得到 `sub_tex_mob`，把它设置为自己的 `submobjects`。
```py
# manimlib.mobject.svg.tex_mobject.Tex.break_up_by_substrings
def break_up_by_substrings(self):
    """
    Reorganize existing submojects one layer
    deeper based on the structure of tex_strings (as a list
    of tex_strings)
    """
    if len(self.tex_strings) == 1:
        submob = self.copy()
        self.set_submobjects([submob])
        return self
    new_submobjects = []
    curr_index = 0
    config = dict(self.CONFIG)
    config["alignment"] = ""
    for tex_string in self.tex_strings:
        tex_string = tex_string.strip()
        if len(tex_string) == 0:
            continue
        sub_tex_mob = SingleStringTex(tex_string, **config)
        num_submobs = len(sub_tex_mob)
        if num_submobs == 0:
            continue
        new_index = curr_index + num_submobs
        sub_tex_mob.set_submobjects(self[curr_index:new_index])
        new_submobjects.append(sub_tex_mob)
        curr_index = new_index
    self.set_submobjects(new_submobjects)
    return self
```
调用 `self.set_color_by_tex_to_color_map` 上色。
```py
# manimlib.mobject.svg.tex_mobject.Tex.set_color_by_tex_to_color_map
def set_color_by_tex_to_color_map(
    self,
    tex_to_color_map: dict[str, ManimColor],
    **kwargs
):
    for tex, color in list(tex_to_color_map.items()):
        self.set_color_by_tex(tex, color, **kwargs)
    return self
```
`set_color_by_tex` 能对单个 `tex` 字符串上色。
```py
# manimlib.mobject.svg.tex_mobject.Tex.set_color_by_tex
def set_color_by_tex(self, tex: str, color: ManimColor, **kwargs):
    self.get_parts_by_tex(tex, **kwargs).set_color(color)
    return self
```
`get_parts_by_tex` 查找 `tex` 对应的部分，包装成 `VGroup` 返回。
```py
# manimlib.mobject.svg.tex_mobject.Tex.get_parts_by_tex
def get_parts_by_tex(
    self, tex: str, substring: bool = True,
    case_sensitive: bool = True
) -> VGroup:
    def test(tex1, tex2):
        if not case_sensitive:
            tex1 = tex1.lower()
            tex2 = tex2.lower()
        if substring: return tex1 in tex2
        else: return tex1 == tex2

    return VGroup(*filter(
        lambda m: isinstance(m, SingleStringTex) and test(tex, m.get_tex()),
        self.submobjects
    ))
```
若 `self.organize_left_to_right`，调用 `self.organize_submobjects_left_to_right` 把 `self.submobjects` 按横坐标排序（只是下标改变，位置不变）。
