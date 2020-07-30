最近我妈的文库VIP用完了，但还有很多资源要下载，于是我便在网上找下载工具。

总算找到个完美的！（虽然没界面）

既然没界面，那就自己写一个呗！

# 原作者
该程序的下载和写入部分由[地球守卫者](https://home.cnblogs.com/u/huangguifeng/)制作

[原文链接](https://www.cnblogs.com/huangguifeng/p/11829323.html#commentform)
# 下载
代码置于存储库内
# 上代码
```python
import requests
import json
import re
import docx
import tkinter as tk
import easygui
import webbrowser
 
def web_ui():
    webbrowser.open("https://totowang-hhh.github.io")
 
def web_main():
    webbrowser.open("https://www.cnblogs.com/huangguifeng/")
 
def start():
    get_document(urlEnter.get())
 
#界面
window=tk.Tk()
window.geometry('800x450')
window.title('Du Doc Down 文库下载器')
window.iconbitmap(".\\icon.ico")
 
webBtn_ui=tk.Button(window,text='界面 By 人工智障',width=25,height=2,font=('仿宋','12'),fg='white',bg='blue',command=web_ui)
webBtn_ui.place(x=10,y=10)
 
webBtn_main=tk.Button(window,text='下载程序 By 地球守卫者',width=25,height=2,font=('仿宋','12'),fg='black',bg='yellow',command=web_main)
webBtn_main.place(x=250,y=10)
 
urlEnterTip=tk.Label(window,text='请将文档链接粘贴到此处')
urlEnterTip.place(x=10,y=105)
urlEnter=tk.Entry(window,bd=2,show=None,width=66)
urlEnter.place(x=10,y=130)
 
downBtn=tk.Button(window,text='下载',font=('等线','15'),fg='white',bg='green',width=43,height=2,command=start)
downBtn.place(x=10,y=160)
 
tipsTitle=tk.Label(window,text='注意',font=('等线','20'))
tipsTitle.place(x=550,y=50)
tipsA=tk.Label(text='·暂时仅支持下载Word文档',font=('等线','13'),anchor='w')
tipsA.place(x=550,y=100)
tipsB=tk.Label(text='·暂不支持下载文字格式与附件',font=('等线','13'),anchor='w')
tipsB.place(x=550,y=120)
 
def get_document(url):
    # 文库url
    sess = requests.Session()
    html = sess.get(url).content.decode("gbk")
    # 抓取到文档标题
    title = re.search('id="doc-tittle-0">(.*?)</span>', html).group(1)
    # 使用正则提取 文档内容的url
    res = re.search("WkInfo.htmlUrls = '(.*)'", html).group(1)
    # \\x22是linux中的引号，替换成Python中的引号
    res = res.replace("\\x22", "\"")
    # 转成字典
    data = json.loads(res)
    # 新建一个文档
    document = docx.Document()
    string = ""
    for i in data["json"]:
        url = i["pageLoadUrl"]  # 获取到url
        url = url.replace("\\", "")  # url中有转义符\去掉
        # 请求文档内容
        data = requests.get(url).content.decode("utf-8")
        # 提取文本数据
        res = re.search("wenku_\d*\((.*)\)", data, re.S).group(1)
        # 将json对象数据转成Python对象
        data = json.loads(res)
        for i in data['body']:
            # 判断数据是什么类型
            if i["t"] == "word":
                # 获取到文本
                string += str(i["c"])
                # ps中不为空并且_enter==1的时候是换行也就是一段内容
                if i["ps"] and i["ps"].get("_enter") == 1:
                    document.add_paragraph(string)  # 将一段内容写入到word
                    string = ""  # 重新复制 "" 表示新的一段文本
    # 保存word
    document.save(easygui.filesavebox(title='保存文件',default=title+".docx"))
```
