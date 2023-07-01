import PySimpleGUI as sg
import requests
import json
from flask import request

# 定义一个函数，用于创建每一行的布局
def create_row(interface):
    # 创建一个空列表，用于存储每一列的元素
    row = []
    # 添加第一列：接口
    row.append(sg.Text(f"接口{interface}", size=(5, 1)))
    # 添加第二列：API
    # row.append(sg.Text("API：", size=(5, 1), pad=((5, 0), (0, 0))))
    # row.append(sg.InputText(size=(15, 1), key=f"API{interface}", pad=((0, 5), (0, 0))))
    # # 添加第三列：端口
    # row.append(sg.Text("端口：", size=(5, 1), pad=((5, 0), (0, 0))))
    # row.append(sg.InputText(size=(4, 1), key=f"Port{interface}", pad=((0, 5), (0, 0))))
    # 添加第四列：密钥
    # row.append(sg.Text("密钥：", size=(5, 1), pad=((5, 0), (0, 0))))
    # row.append(sg.InputText(size=(15, 1), key=f"Key{interface}", pad=((0, 5), (0, 0))))
    # 添加第五列：识别语种
    row.append(sg.Text("识别语种：", size=(10, 1), pad=((5, 0), (0, 0))))
    languages = ["阿拉伯语", "丹麦语", "德语", "俄语", "法语", "芬兰语", "韩语", "荷兰语", "捷克语", "马来西亚语", "葡萄牙语", "日语", "瑞典语", "瑞士语", "斯洛伐克语", "泰语", "土耳其语", "西班牙语", "希腊语", "匈牙利语", "意大利语", "印地语", "印度尼西亚语", "英语", "中文繁体", "中文简体"]
    row.append(sg.Combo(languages, default_value="英语", size=(10, 1), key="Language", pad=((0, 5), (0, 0))))
    # 添加第六列：新增语种
    row.append(sg.Button("新增语种", key=f"AddLanguage{interface}", pad=((5, 5), (0, 0))))
    # 添加第七列：黑名单
    row.append(sg.Text("黑名单：", size=(7, 1), pad=((5, 0), (0, 0))))
    row.append(sg.InputText(size=(10, 1), key=f"Blacklist{interface}", pad=((0, 5), (0, 0))))
    # 添加第八列：选择按钮
    row.append(sg.FileBrowse("选择文件", file_types=(("TXT Files", "*.txt"),), key='black_lines',target=f"Blacklist{interface}",
                             pad=((5, 5), (0, 0))))
    # 添加第九列：关键词
    row.append(sg.Text("关键词：", size=(7, 1), pad=((5, 0), (0, 0))))
    row.append(sg.InputText(size=(10, 1), key=f"Keyword{interface}", pad=((0, 5), (0, 0))))
    # 添加第十列：选择按钮
    row.append(sg.FileBrowse("选择文件", file_types=(("TXT Files", "*.txt"),),key='keyword_lines', target=f"Keyword{interface}",
                             pad=((5, 5), (0, 0))))
    # 添加第九列：图片
    row.append(sg.Text("图片：", size=(7, 1), pad=((5, 0), (0, 0))))
    row.append(sg.InputText(size=(10, 1), key=f"Image{interface}", pad=((0, 5), (0, 0))))
    # 添加第十列：选择按钮
    row.append(sg.FolderBrowse("选择文件夹",key='Images', target=f"Image{interface}",
                             pad=((5, 5), (0, 0))))
    # 添加第十一列：评论文本
    row.append(sg.Text("评论文本：", size=(8, 1), pad=((5, 0), (0, 0))))
    row.append(sg.InputText(size=(10, 1), key=f"Comment{interface}", pad=((0, 5), (0, 0))))
    # 添加第十二列：选择按钮
    row.append(sg.FileBrowse("选择文件", file_types=(("TXT Files", "*.txt"),),key='comment_lines' ,target=f"Comment{interface}",
                             pad=((5, 5), (0, 0))))
    # 添加第十三列：删除语种（默认不显示）
    row.append(sg.Button("删除语种", visible=False, key=f"DeleteLanguage{interface}", pad=((5, 5), (0, 0))))
    # 添加：确定开启
    row.append(sg.Button("开启识别", key=f"Identify{interface}", pad=((5, 5), (0, 0))))
    # 添加第十四列：添加项目
    row.append(sg.Button("添加项目", key=f"AddProject{interface}", pad=((5, 5), (0, 0))))
    # 添加第十五列：删除项目（默认不显示）
    row.append(sg.Button("删除项目", visible=False, key=f"DeleteProject{interface}", pad=((5, 5), (0, 0))))

    return row


# 定义一个函数，用于更新每一行的布局
def update_row(layout, interface, language):
    # 获取当前行的元素
    row = layout[interface - 1]
    # 更新第一列：接口
    row[0].update(f"接口{interface}")
    # 更新第二列：API
    row[2].update(key=f"API{interface}")
    # 更新第三列：端口
    row[4].update(key=f"Port{interface}")
    # 更新第四列：密钥
    row[6].update(key=f"Key{interface}")
    # 更新第五列：识别语种
    row[8].update(key=f"Language{interface}")
    # 更新第六列：新增语种
    row[9].update(key=f"AddLanguage{interface}")
    # 更新第七列：黑名单
    row[11].update(key=f"Blacklist{interface}")
    # 更新第八列：选择按钮
    row[12].update(target=f"Blacklist{interface}")
    # 更新第九列：关键词
    row[14].update(key=f"Keyword{interface}")
    # 更新第十列：选择按钮
    row[15].update(target=f"Keyword{interface}")
    # 更新第十一列：评论文本
    row[17].update(key=f"Comment{interface}")
    # 更新第十二列：选择按钮
    row[18].update(target=f"Comment{interface}")
    # 更新第十三列：删除语种（如果有多于一种语言，显示）
    if language > 1:
        row[19].update(visible=True, key=f"DeleteLanguage{interface}")
    else:
        row[19].update(visible=False, key=f"DeleteLanguage{interface}")
    # 更新第十四列：添加项目
    row[20].update(key=f"AddProject{interface}")
    # 更新第十五列：删除项目（如果有多于一个项目，显示）
    if len(layout) > 1:
        row[21].update(visible=True, key=f"DeleteProject{interface}")
    else:
        row[21].update(visible=False, key=f"DeleteProject{interface}")


# 定义一个字典，用于存储每个接口的语言数量
language_count = {}

url='http://173.249.197.34:5000/image'

# 创建一个空列表，用于存储整个布局
layout = []

# 创建一个初始的行，接口为1，语言为1
layout.append(create_row(1))
language_count[1] = 1

# 创建一个窗口，标题为OCR控制界面，居中显示，使用默认主题
window = sg.Window("OCR控制界面", layout, element_justification="center")

# 进入事件循环，处理用户的输入和点击
while True:

    event, values = window.read()

    # 如果用户关闭窗口或按下ESC键，退出循环
    if event in (sg.WIN_CLOSED, "Escape"):
        break

        # 如果用户点击了新增语种按钮
    if event.startswith("AddLanguage"):
        # 获取当前的接口编号
        interface = int(event[-1])
        # 获取当前的语言数量
        language = language_count[interface]
        # 如果当前的语言数量小于10，才能新增语种
        if language < 10:
            # 在当前行的下方插入一行，复制第五列到第十三列的内容，并更新对应的key值和可见性
            layout.insert(interface, layout[interface - 1][4:13])
            language += 1
            language_count[interface] = language
            # for i in range(interface + 1, len(layout) + 1):
            #     update_row(layout, i, language_count[i])
            window.extend_layout(window, layout)

    # 如果用户点击了删除语种按钮
    if event.startswith("DeleteLanguage"):
        # 获取当前的接口编号
        interface = int(event[-1])
        # 获取当前的语言数量
        language = language_count[interface]
        # 如果当前的语言数量大于1，才能删除语种
        if language > 1:
            # 删除当前行的内容，并更新对应的key值和可见性
            for element in layout.pop(interface - 1):
                element.hide_row()
            language -= 1
            language_count[interface] = language
            for i in range(interface, len(layout) + 1):
                update_row(layout, i, language_count[i])

    # 如果用户点击了添加项目按钮
    if event.startswith("AddProject"):
        # 获取当前的接口编号
        interface = int(event[-1])
        print(interface)
        layout.clear()
        # 在当前行的下方插入一行，复制第一列到第十五列的内容，并更新对应的key值和可见性
        layout.insert(interface, create_row(interface + 1))
        language_count[interface + 1] = 1
        for i in range(interface + 2, len(layout) + 1):
            update_row(layout, i, language_count[i])
        window.extend_layout(window, layout)

    # 如果用户点击了删除项目按钮
    if event.startswith("DeleteProject"):
        # 获取当前的接口编号
        interface = int(event[-1])
        # 如果当前的接口数量大于1，才能删除项目
        if len(layout) > 1:
            # 删除当前行的内容，并更新对应的key值和可见性
            for element in layout.pop(interface - 1):
                element.hide_row()
            language_count.pop(interface)
            for i in range(interface, len(layout) + 1):
                update_row(layout, i, language_count[i])

    if event.startswith("Identify"):
        # 获取当前的接口编号
        interface = int(event[-1])
        print(values['Language'])
        datas = { 'language_type' : values['Language'], 'image_name' : values['Images'], 'black_lines' : values['black_lines'] , 'keyword_lines' : values['keyword_lines'], 'comment_lines' : values['comment_lines']}
        r = requests.post(url,params=datas)
        result = r.json()
        print(result['identify_lines'])
        print(result['result_detail'])
        print(result['result'])
window.close()
