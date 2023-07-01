# 导入tkinter库
import tkinter as tk
from tkinter import filedialog

# 创建主窗口
root = tk.Tk()
# 设置窗口标题
root.title("OCR控制界面")
# 设置窗口大小和位置
root.geometry("1600x900+100+100")
# 设置窗口背景颜色
root.config(bg="#F0F0F0")

# 定义一个函数，用于选择文件夹或文件，并将路径显示在对应的输入框中
def select_path(entry):
    # 弹出选择文件夹或文件的对话框
    path = filedialog.askdirectory() or filedialog.askopenfilename()
    # 如果路径不为空，将其插入到输入框中
    if path:
        entry.delete(0, tk.END)
        entry.insert(0, path)

# 定义一个函数，用于新增语种，并复制第五列到第十三列的内容
def add_language(interface):
    # 获取当前接口的语种数量
    language_num = len(interface["languages"])
    # 如果语种数量小于10，才能新增语种
    if language_num < 10:
        # 在识别语种的下方向下扩展一行，复制第五列到第十三列的内容
        language = {}
        language["language"] = tk.StringVar(value="英语")
        language["language_option"] = tk.OptionMenu(interface["frame"], language["language"], "阿拉伯语", "丹麦语", "德语", "俄语", "法语", "芬兰语", "韩语", "荷兰语", "捷克语", "马来西亚语", "葡萄牙语", "日语", "瑞典语", "瑞士语", "斯洛伐克语", "泰语", "土耳其语", "西班牙语", "希腊语", "匈牙利语", "意大利语", "印地语", "印度尼西亚语", "英语", "中文繁体", "中文简体")
        language["language_option"].config(width=4, font=("Arial", 11), bg="#87CEFA")
        language["language_option"].grid(row=language_num+1, column=4, sticky=tk.W)
        # 在每个输入框前方添加名称文本，并调整间距
        tk.Label(interface["frame"], text="黑名单：", width=6, font=("Arial", 11), bg="#F0F0F0").grid(row=language_num+1, column=5, sticky=tk.W)
        language["blacklist"] = tk.Entry(interface["frame"], width=10, font=("Arial", 11))
        language["blacklist"].grid(row=language_num+1, column=6, sticky=tk.W)
        language["blacklist_button"] = tk.Button(interface["frame"], text="选择黑名单", width=8, font=("Arial", 10), bg="#87CEFA", command=lambda: select_path(language["blacklist"]))
        language["blacklist_button"].grid(row=language_num+1, column=7, sticky=tk.W)
        tk.Label(interface["frame"], text="关键词：", width=6, font=("Arial", 11), bg="#F0F0F0").grid(row=language_num+1, column=8, sticky=tk.W)
        language["keyword"] = tk.Entry(interface["frame"], width=10, font=("Arial", 11))
        language["keyword"].grid(row=language_num+1, column=9, sticky=tk.W)
        language["keyword_button"] = tk.Button(interface["frame"], text="选择关键词", width=8, font=("Arial", 10), bg="#87CEFA", command=lambda: select_path(language["keyword"]))
        language["keyword_button"].grid(row=language_num+1, column=10, sticky=tk.W)
        tk.Label(interface["frame"], text="评论文本：", width=8, font=("Arial", 11), bg="#F0F0F0").grid(row=language_num+1, column=11, sticky=tk.W)
        language["comment"] = tk.Entry(interface["frame"], width=10, font=("Arial", 11))
        language["comment"].grid(row=language_num+1, column=12, sticky=tk.W)
        language["comment_button"] = tk.Button(interface["frame"], text="选择评论文本", width=9, font=("Arial", 10), bg="#87CEFA", command=lambda: select_path(language["comment"]))
        language["comment_button"].grid(row=language_num+1, column=13, sticky=tk.W)
        # 只有一行时不显示删除按钮，两行以上时才显示删除按钮
        if language_num == 0:
            interface["languages"][0]["delete_button"] = tk.Button(interface["frame"], text="删除语种", width=8, font=("Arial", 10), bg="#FF0000")
            interface["languages"][0]["delete_button"].grid(row=1, column=14, sticky=tk.W)
            interface["languages"][0]["delete_button"].config(command=lambda: delete_language(interface, 0))
        language["delete_button"] = tk.Button(interface["frame"], text="删除语种", width=8, font=("Arial", 10), bg="#FF0000")
        language["delete_button"].grid(row=language_num+1, column=14, sticky=tk.W)
        language["delete_button"].config(command=lambda: delete_language(interface, language_num))
        # 将新增的语种添加到接口的语种列表中
        interface["languages"].append(language)
        # 下方的所有项目将自动向下移动以适应新的空间
        for i in range(len(interfaces)):
            if i > interface["index"]:
                interfaces[i]["frame"].grid(row=i+language_num+1, column=0, sticky=tk.W)

# 定义一个函数，用于删除语种，并删除对应的那一行内容
def delete_language(interface, language_index):
    # 获取当前接口的语种数量
    language_num = len(interface["languages"])
    # 如果语种数量大于1，才能删除语种
    if language_num > 1:
        # 删除对应的那一行内容
        language = interface["languages"][language_index]
        language["language_option"].destroy()
        language["blacklist"].destroy()
        language["blacklist_button"].destroy()
        language["keyword"].destroy()
        language["keyword_button"].destroy()
        language["comment"].destroy()
        language["comment_button"].destroy()
        language["delete_button"].destroy()
        # 将删除的语种从接口的语种列表中移除
        interface["languages"].pop(language_index)
        # 每删除一种语种，下方的行将自动向上移动以适应新的空间
        for i in range(language_index, language_num-1):
            interface["languages"][i]["language_option"].grid(row=i+1, column=4, sticky=tk.W)
            interface["languages"][i]["blacklist"].grid(row=i+1, column=6, sticky=tk.W)
            interface["languages"][i]["blacklist_button"].grid(row=i+1, column=7, sticky=tk.W)
            interface["languages"][i]["keyword"].grid(row=i+1, column=9, sticky=tk.W)
            interface["languages"][i]["keyword_button"].grid(row=i+1, column=10, sticky=tk.W)
            interface["languages"][i]["comment"].grid(row=i+1, column=12, sticky=tk.W)
            interface["languages"][i]["comment_button"].grid(row=i+1, column=13, sticky=tk.W)
            interface["languages"][i]["delete_button"].grid(row=i+1, column=14, sticky=tk.W)
            interface["languages"][i]["delete_button"].config(command=lambda: delete_language(interface, i))
        for i in range(len(interfaces)):
            if i > interface["index"]:
                interfaces[i]["frame"].grid(row=i-language_num+len(interface["languages"]), column=0, sticky=tk.W)
        # 只有一行时不显示删除按钮，两行以上时才显示删除按钮
        if len(interface["languages"]) == 1:
            interface["languages"][0]["delete_button"].destroy()

# 定义一个函数，用于新增接口，并复制第一列到第十五列的所有默认项目
def add_interface():
    # 获取当前接口的数量
    interface_num = len(interfaces)
    # 新增一个接口，并设置其属性和组件
    interface = {}
    interface["index"] = interface_num
    interface["frame"] = tk.Frame(root)
    interface["frame"].config(bg="#F0F0F0")
    interface["frame"].grid(row=interface_num+1, column=0, sticky=tk.W)
    interface["name"] = tk.Label(interface["frame"], text="接口"+str(interface_num+1), width=8, font=("Arial", 11), bg="#F0F0F0")
    interface["name"].grid(row=1, column=0, sticky=tk.W)
    # 在每个输入框前方添加名称文本，并调整间距
    tk.Label(interface["frame"], text="端口：", width=5, font=("Arial", 11), bg="#F0F0F0").grid(row=1, column=1, sticky=tk.W)
    interface["port"] = tk.Entry(interface["frame"], width=6, font=("Arial", 11))
    interface["port"].grid(row=1, column=2, sticky=tk.W)
    tk.Label(interface["frame"], text="图片：", width=4, font=("Arial", 11), bg="#F0F0F0").grid(row=1, column=3, sticky=tk.W)
    interface["image"] = tk.Entry(interface["frame"], width=10, font=("Arial", 11))
    interface["image"].grid(row=1, column=4, sticky=tk.W)
    interface["image_button"] = tk.Button(interface["frame"], text="选择文件夹", width=8, font=("Arial", 10), bg="#87CEFA", command=lambda: select_path(interface["image"]))
    interface["image_button"].grid(row=1, column=5, sticky=tk.W)
    # 每个接口默认有一种语种
    interface["languages"] = []
    language = {}
    language["language"] = tk.StringVar(value="英语")
    language["language_option"] = tk.OptionMenu(interface["frame"], language["language"], "阿拉伯语", "丹麦语", "德语", "俄语", "法语", "芬兰语", "韩语", "荷兰语", "捷克语", "马来西亚语", "葡萄牙语", "日语", "瑞典语", "瑞士语", "斯洛伐克语", "泰语", "土耳其语", "西班牙语", "希腊语", "匈牙利语", "意大利语", "印地语", "印度尼西亚语", "英语", "中文繁体", "中文简体")
    language["language_option"].config(width=4, font=("Arial", 10), bg="#87CEFA")
    language["language_option"].grid(row=1, column=4, sticky=tk.W)
    # 在每个输入框前方添加名称文本，并调整间距
    tk.Label(interface["frame"], text="黑名单：", width=6, font=("Arial", 11), bg="#F0F0F0").grid(row=1, column=5, sticky=tk.W)
    language["blacklist"] = tk.Entry(interface["frame"], width=10, font=("Arial", 11))
    language["blacklist"].grid(row=1, column=6, sticky=tk.W)
    language["blacklist_button"] = tk.Button(interface["frame"], text="选择黑名单", width=8, font=("Arial", 10), bg="#87CEFA", command=lambda: select_path(language["blacklist"]))
    language["blacklist_button"].grid(row=1, column=7, sticky=tk.W)
    tk.Label(interface["frame"], text="关键词：", width=6, font=("Arial", 11), bg="#F0F0F0").grid(row=1, column=8, sticky=tk.W)
    language["keyword"] = tk.Entry(interface["frame"], width=10, font=("Arial", 11))
    language["keyword"].grid(row=1, column=9, sticky=tk.W)
    language["keyword_button"] = tk.Button(interface["frame"], text="选择关键词", width=8, font=("Arial", 10), bg="#87CEFA", command=lambda: select_path(language["keyword"]))
    language["keyword_button"].grid(row=1, column=10, sticky=tk.W)
    tk.Label(interface["frame"], text="评论文本：", width=8, font=("Arial", 11), bg="#F0F0F0").grid(row=1, column=11, sticky=tk.W)
    language["comment"] = tk.Entry(interface["frame"], width=10, font=("Arial", 11))
    language["comment"].grid(row=1, column=12, sticky=tk.W)
    language["comment_button"] = tk.Button(interface["frame"], text="选择评论文本", width=9, font=("Arial", 10), bg="#87CEFA", command=lambda: select_path(language["comment"]))
    language["comment_button"].grid(row=1, column=13, sticky=tk.W)
    interface["languages"].append(language)
    # 添加新增和删除按钮
    interface["add_language_button"] = tk.Button(interface["frame"], text="新增语种", width=6, font=("Arial", 10), bg="#87CEFA")
    interface["add_language_button"].grid(row=1, column=14, sticky=tk.W)
    interface["add_language_button"].config(command=lambda: add_language(interface))
    interface["add_interface_button"] = tk.Button(interface["frame"], text="新增接口", width=6, font=("Arial", 10), bg="#87CEFA")
    interface["add_interface_button"].grid(row=1, column=15, sticky=tk.W)
    interface["add_interface_button"].config(command=add_interface)
    interface["delete_interface_button"] = tk.Button(interface["frame"], text="删除接口", width=6, font=("Arial", 10), bg="#FF0000")
    interface["delete_interface_button"].grid(row=1, column=16, sticky=tk.W)
    interface["delete_interface_button"].config(command=lambda: delete_interface(interface))
    # 将新增的接口添加到接口列表中
    interfaces.append(interface)

# 定义一个函数，用于删除接口，并删除对应的整行内容
def delete_interface(interface):
    # 获取当前接口的数量
    interface_num = len(interfaces)
    # 如果接口数量大于1，才能删除接口
    if interface_num > 1:
        # 删除对应的整行内容
        interface["frame"].destroy()
        # 将删除的接口从接口列表中移除
        interfaces.pop(interface["index"])
        # 每删除一个接口，下方的行将自动向上移动以适应新的空间，并更新接口名称和索引
        for i in range(interface["index"], interface_num-1):
            interfaces[i]["name"].config(text="接口"+str(i+1))
            interfaces[i]["index"] = i
            interfaces[i]["frame"].grid(row=i+1, column=0, sticky=tk.W)

# 创建一个标签，用于显示标题
title = tk.Label(root, text="OCR控制界面", font=("Arial", 20, "bold"), bg="#F0F0F0", fg="#0000FF")
# 将标签放置在窗口顶部居中位置
title.place(x=800, y=20, anchor=tk.CENTER)

# 创建一个列表，用于存储所有的接口
interfaces = []
# 默认有一个接口，调用新增接口的函数来创建
add_interface()

# 创建一个按钮，用于启动或停止OCR程序，逻辑暂时不需要
start_stop_button = tk.Button(root, text="启动/停止", width=10, font=("Arial", 12), bg="#87CEFA")
# 将按钮放置在窗口底部居中位置
start_stop_button.place(x=800, y=880, anchor=tk.CENTER)

# 进入主循环，等待用户操作
root.mainloop()

