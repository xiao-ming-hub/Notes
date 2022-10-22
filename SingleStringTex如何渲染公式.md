# [ManimGL 源码解读] SingleStringTex 如何渲染公式 - SunJul31 2022
`SingleStringTex` 传入单个字符串 `tex_string` 渲染出 `SVGMobject`。
```py
# manimlib.mobject.svg.tex_mobject.SingleStringTex
class SingleStringTex(SVGMobject):
    CONFIG = {...}

    def __init__(self, tex_string: str, **kwargs):
        assert isinstance(tex_string, str)
        self.tex_string = tex_string
        super().__init__(**kwargs)

        if self.height is None:
            self.scale(SCALE_FACTOR_PER_FONT_POINT * self.font_size)
        if self.organize_left_to_right:
            self.organize_submobjects_left_to_right()

    ...
```
检测类型，保存 `tex_string`，调用 `SVGMobject.__init__`。
```py
# manimlib.mobject.svg.svg_mobject.SVGMobject.__init__
def __init__(self, file_name: str | None = None, **kwargs):
    super().__init__(**kwargs)
    self.file_name = file_name or self.file_name
    self.init_svg_mobject()
    self.init_colors()
    self.move_into_position()
```
`init_svg_mobject` 大概就是设置散列（Hash），生成 SVG。
```py
# manimlib.mobject.svg.svg_mobject.SVGMobject.init_svg_mobject
def init_svg_mobject(self) -> None:
    hash_val = hash_obj(self.hash_seed)
    if hash_val in SVG_HASH_TO_MOB_MAP:
        mob = SVG_HASH_TO_MOB_MAP[hash_val].copy()
        self.add(*mob)
        return

    self.generate_mobject()
    SVG_HASH_TO_MOB_MAP[hash_val] = self.copy()
```
`SingleStringTex` 重写了 `SVGMobject.hash_seed`（获取散列种子方法）。
```py
# manimlib.mobject.svg.tex_mobject.SingleStringTex.hash_seed
@property
def hash_seed(self) -> tuple:
    return (
        self.__class__.__name__,
        self.svg_default,
        self.path_string_config,
        self.tex_string,
        self.alignment,
        self.math_mode
    )
```
与父类相比，`SingleStringTex.hash_seed` 去掉 `file_name`（因为是 `None`），添加 `tex_string`（传入的 LaTeX 字符串）、`alignment`（对齐方式，默认 `"\centering"`）、`math_mode`（是否开启数学环境，默认 `True`）（为什么要这样做？）。
```py
# manimlib.mobject.svg.svg_mobject.SVGMobject.hash_seed
@property
def hash_seed(self) -> tuple:
    # Returns data which can uniquely represent the result of `init_points`.
    # The hashed value of it is stored as a key in `SVG_HASH_TO_MOB_MAP`.
    return (
        self.__class__.__name__,
        self.svg_default,
        self.path_string_config,
        self.file_name
    )
```
`SVG_HASH_TO_MOB_MAP` 是一个存储 `VMobject` 的散列值的字典。
```py
# manimlib.mobject.svg.svg_mobject.SVG_HASH_TO_MOB_MAP
SVG_HASH_TO_MOB_MAP: dict[int, VMobject] = {}
```
`generate_mobject` 生成 SVG。
```py
# manimlib.mobject.svg.svg_mobject.SVGMobject.generate_mobject
def generate_mobject(self) -> None:
    file_path = self.get_file_path()
    element_tree = ET.parse(file_path)
    new_tree = self.modify_xml_tree(element_tree)
    # Create a temporary svg file to dump modified svg to be parsed
    root, ext = os.path.splitext(file_path)
    modified_file_path = root + "_" + ext
    new_tree.write(modified_file_path)

    svg = se.SVG.parse(modified_file_path)
    os.remove(modified_file_path)

    mobjects = self.get_mobjects_from(svg)
    self.add(*mobjects)
    self.flip(RIGHT)  # Flip y
```
`SingleStringTex` 重写了 `get_file_path` 方法。它会根据一开始提供的 `tex_string` 渲染 SVG 文件，返回文件名。`display_during_execution` 给出提示信息。
```py
# manimlib.mobject.svg.tex_mobject.SingleStringTex.get_file_path
def get_file_path(self) -> str:
    full_tex = self.get_tex_file_body(self.tex_string)
    with display_during_execution(f"Writing \"{self.tex_string}\""):
        file_path = tex_to_svg_file(full_tex)
    return file_path
```
`get_tex_file_body` 获得最终写入 `.tex` 的字符串。
```py
# manimlib.mobject.svg.tex_mobject.SingleStringTex.get_tex_file_body
def get_tex_file_body(self, tex_string: str) -> str:
    new_tex = self.get_modified_expression(tex_string)
    if self.math_mode:
        new_tex = "\\begin{align*}\n" + new_tex + "\n\\end{align*}"

    new_tex = self.alignment + "\n" + new_tex

    tex_config = get_tex_config()
    return tex_config["tex_body"].replace(
        tex_config["text_to_replace"],
        new_tex
    )
```
`get_modified_expression` 获得 `tex_string` 特殊处理（大概是补全括号之类的）后的字符串，用 `new_tex` 保存。
```py
# manimlib.mobject.svg.tex_mobject.SingleStringTex.get_modified_expression
def get_modified_expression(self, tex_string: str) -> str:
    return self.modify_special_strings(tex_string.strip())
```
`modify_special_strings` 特殊处理 `tex_string` （这两个函数直接把我整蒙了，`get_modified_expression` 里面就直接 `return` 掉，~~差不多~~就是起了个别名。而且它还会对一个字符串 `strip` 两次⋯⋯我很难理解这样做的用意）。
```py
# manimlib.mobject.svg.tex_mobject.SingleStringTex.modify_special_strings
    def modify_special_strings(self, tex: str) -> str:
        tex = tex.strip()
        should_add_filler = reduce(op.or_, [
            # Fraction line needs something to be over
            tex == "\\over",
            tex == "\\overline",
            # Makesure sqrt has overbar
            tex == "\\sqrt",
            tex == "\\sqrt{",
            # Need to add blank subscript or superscript
            tex.endswith("_"),
            tex.endswith("^"),
            tex.endswith("dot"),
        ])
        if should_add_filler:
            filler = "{\\quad}"
            tex += filler

        should_add_double_filler = reduce(op.or_, [
            tex == "\\overset",
            # TODO: these can't be used since they change
            # the latex draw order.
            # tex == "\\frac", # you can use \\over as a alternative 
            # tex == "\\dfrac",
            # tex == "\\binom",
        ])
        if should_add_double_filler:
            filler = "{\\quad}{\\quad}"
            tex += filler

        if tex == "\\substack":
            tex = "\\quad"

        if tex == "":
            tex = "\\quad"

        # To keep files from starting with a line break
        if tex.startswith(r"\\"):
            tex = tex.replace(r"\\", r"\quad\\")

        tex = self.balance_braces(tex)

        # Handle imbalanced \left and \right
        num_lefts, num_rights = [
            len([
                s for s in tex.split(substr)[1:]
                if s and s[0] in "(){}[]|.\\"
            ])
            for substr in ("\\left", "\\right")
        ]
        if num_lefts != num_rights:
            tex = tex.replace("\\left", "\\big")
            tex = tex.replace("\\right", "\\big")

        for context in ["array"]:
            begin_in = ("\\begin{%s}" % context) in tex
            end_in = ("\\end{%s}" % context) in tex
            if begin_in ^ end_in:
                # Just turn this into a blank string,
                # which means caller should leave a
                # stray \\begin{...} with other symbols
                tex = ""
        return tex
```
`tex_to_svg_file` 读取 `.tex` 文件位置，根据 `tex_file_content` 计算散列值构造文件名（`svg_file`）。如果没有渲染过，则写入 `.tex` 文件并渲染它。
```py
# manimlib.utils.tex_file_writing.tex_to_svg_file
def tex_to_svg_file(tex_file_content: str) -> str:
    svg_file = os.path.join(
        get_tex_dir(), tex_hash(tex_file_content) + ".svg"
    )
    if not os.path.exists(svg_file):
        # If svg doesn't exist, create it
        tex_to_svg(tex_file_content, svg_file)
    return svg_file
```
`get_tex_dir` 获得保存 `.tex` 文件的文件夹。
```py
# manimlib.utils.directories.get_tex_dir
def get_tex_dir() -> str:
    return guarantee_existence(os.path.join(get_temp_dir(), "Tex"))
```
`get_temp_dir` 获得保存临时文件的位置。在 Linux 上是 `/tmp`。
```py
# manimlib.utils.directories.get_temp_dir
def get_temp_dir() -> str:
    return get_directories()["temporary_storage"]
```
`get_directories` 获得文件夹设置。
```py
# manimlib.utils.directories.get_directories
def get_directories() -> dict[str, str]:
    return get_customization()["directories"]
```
`CUSTOMIZATION` 一开始是空字典，第一次调用后就会把 `default_config.yml` 和 `custom_config.yml`（若存在）转换成字典。可以看出，这里有对 `temporary_storage` 特判：若它是空字符串 `""`，则把它设置为操作系统的默认临时文件存放位置（`tempfile.gettempdir()`）。
```py
# manimlib.utils.customization.get_customization
def get_customization():
    if not CUSTOMIZATION:
        CUSTOMIZATION.update(get_custom_config())
        directories = CUSTOMIZATION["directories"]
        # Unless user has specified otherwise, use the system default temp
        # directory for storing tex files, mobject_data, etc.
        if not directories["temporary_storage"]:
            directories["temporary_storage"] = tempfile.gettempdir()

        # Assumes all shaders are written into manimlib/shaders
        directories["shaders"] = os.path.join(
            get_manim_dir(), "manimlib", "shaders"
        )
    return CUSTOMIZATION
```
`guarantee_existence` 保证文件夹存在。如果不存在就新建文件夹。
```py
# manimlib.utils.file_ops.guarantee_existence
def guarantee_existence(path: str) -> str:
    if not os.path.exists(path):
        os.makedirs(path)
    return os.path.abspath(path)
```
`tex_hash` 根据 `tex_content` 生成对应散列值，这是用于判断两个 `SingleStringTex` 是否相等的方法。回想前面的 `SingleStringTex.hash_seed`，增加的内容正好共同决定了 `tex_content`。其实现在你还可以再看看 `SVGMobject.init_svg_mobject`，相同的不仅不会重复渲染，也不会重复执行 `generate_mobject`。

现在我们终于弄清 ManimGL 如何生成 `.tex` 的文件名的细节了。这时文件名保存在 `tex_to_svg_file` 变量里，最终返回的也是这个变量。接下来我们来看看 ManimGL 怎么调用 LaTeX，怎么生成最终的 SVG 文件。

`tex_to_svg` 把传入的 `tex_file_content` 渲染成 SVG，写入传入的 `svg_file`，返回 `svg_file`。可以看到，`svg_file` 的后缀转换成 `.svg`，保存在 `tex_file`。接着打开文件，把 `tex_file_content` 写入 `tex_file`，开始渲染（如果以前渲染过的话就不会调用 `tex_to_svg`）。渲染完成后，把除了 `.svg` 的文件删除。
```py
# manimlib.utils.tex_file_writing.tex_to_svg
def tex_to_svg(tex_file_content: str, svg_file: str) -> str:
    tex_file = svg_file.replace(".svg", ".tex")
    with open(tex_file, "w", encoding="utf-8") as outfile:
        outfile.write(tex_file_content)
    svg_file = dvi_to_svg(tex_to_dvi(tex_file))

    # Cleanup superfluous documents
    tex_dir, name = os.path.split(svg_file)
    stem, end = name.split(".")
    for file in filter(lambda s: s.startswith(stem), os.listdir(tex_dir)):
        if not file.endswith(end):
            os.remove(os.path.join(tex_dir, file))

    return svg_file
```
`tex_to_dvi` 调用 `letax`（`xelatex -no-pdf`） 把 `.tex` 渲染成 `.dvi`（`.xdv`） 文件。
```py
# manimlib.utils.tex_file_writing.tex_to_dvi
def tex_to_dvi(tex_file: str) -> str:
    tex_config = get_tex_config()
    program = tex_config["executable"]
    file_type = tex_config["intermediate_filetype"]
    result = tex_file.replace(".tex", "." + file_type)
    if not os.path.exists(result):
        commands = [
            program,
            "-interaction=batchmode",
            "-halt-on-error",
            f"-output-directory=\"{os.path.dirname(tex_file)}\"",
            f"\"{tex_file}\"",
            ">",
            os.devnull
        ]
        exit_code = os.system(" ".join(commands))
        if exit_code != 0:
            log_file = tex_file.replace(".tex", ".log")
            log.error("LaTeX Error!  Not a worry, it happens to the best of us.")
            error_str = ""
            with open(log_file, "r", encoding="utf-8") as file:
                for line in file.readlines():
                    if line.startswith("!"):
                        error_str = line[2:-1]
                        log.debug(f"The error could be: `{error_str}`")
            raise LatexError(error_str)
    return result
```
`dvi_to_svg` 调用 `dvisvgm` 把 `.dvi` 转换成 `.svg` 文件。
```py
# manimlib.utils.tex_file_writing.dvi_to_svg
def dvi_to_svg(dvi_file: str) -> str:
    """
    Converts a dvi, which potentially has multiple slides, into a
    directory full of enumerated pngs corresponding with these slides.
    Returns a list of PIL Image objects for these images sorted as they
    where in the dvi
    """
    file_type = get_tex_config()["intermediate_filetype"]
    result = dvi_file.replace("." + file_type, ".svg")
    if not os.path.exists(result):
        commands = [
            "dvisvgm",
            "\"{}\"".format(dvi_file),
            "-n",
            "-v",
            "0",
            "-o",
            "\"{}\"".format(result),
            ">",
            os.devnull
        ]
        os.system(" ".join(commands))
    return result
```
回到生成 SVG 的 `generate_mobject`，后面是杂七杂八地对 `.svg` 一顿操作。不过这些都不是和渲染公式相关的，不是本文重点。

回到 `SVGMobject.__init__`，后面是调用设置颜色、位置的方法。

再退回 `SingleStringTex.__init__`，后面是设置高度、给 `submobjects` 排序。

差不多⋯⋯就这样了吧？
