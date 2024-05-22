---
layout: single
title: '코딩일지(2024-05-15)'
categories:
  - ElecBro
tags:
  - python
toc: true
toc_sticky: true
typora-root-url: ../
---







# 1년전에 했던 Tkinter공부를 다시하다.




## 아니 그래서 이걸 왜 하게되었냐면,

나는 현재 일렉브로팀의 컨트롤러 개발을 도와주고 있다. 근데 거기서 PyQt를 사용한다고 하더라.

과거에 기억을 떠올려보면, 나는 작년에 tkinter와 PyQt라는 두 모듈중에 고민을 하다가 tkinter라는 모듈을 사용해서 스터디카페 키오스크 개발을 텀플로젝트로 수행하게 되었다. 그래서 tkinter로는 어떻게 짜는지 대충은 알고 있다. 물론 많이 까먹은 상황...

그래서 pyqt를 바로 맨땅에 헤딩하듯이 공부할까 하다가, 에이 그냥,,, tkinter복습을 하면서 공부를 하고 이걸로 로직을 다시 이해하고 pyqt로 빠르게 넘어가자는 생각이다. 

그래서 쨌든 시작!

## 일단 다른 예제코드부터 쑤셔보기로 했다.

구글리으로 tkinter 예제 치고 블로그 아무거나 들어가니까, 꽤 예제가 많더라,그중 하나가 아래꺼다.

```python
import tkinter as tk

s = "Life is shor\nUse Python"

root = tk.Tk()
t = tk.Text(root, height=10, width=30)
t.insert(tk.END, s)
t.pack()
tk.mainloop()
```

이거는 대충 보니까 일단 root = tk.Tk()로 클래스 생성자 선언해서 객체 만들고, 그러면 그게 기본창을 띄우는 객체가 된다.

비유하자면 도화지다ㅇㅇ. 이제 여기서 t라는 객체로 tk에 넣을 수 이는 텍스트 객체를 생성해서 문자열을 삽입하고(t.insert), 기본창에 include시켰다(t.pack()).

그리고 우리는 이 기본창이 0.00001초만 띄우는게 아니라, 계속 떠있을 수 있도록, 끄지 않는 이상 평생 떠있을 수 있도록 해주기 위해서 tk.mainloop()를 넣어주었다.

result>

![python_0Zzi7V72gd](/images/2024-05-19-codinglog/python_0Zzi7V72gd.webp)

이번에는 이걸 좀더 업그레이드 시킬 수 없을까 하다가 메모장을 만들어보기로 했다. 일명 "Tkinter Notepad"이다.

## Tkinter Notepad를 만들어보았다.

chatGPT 형님의 도움을 받았다.

일단 내가 생가하는 시스템 설계는 다음과 같다.

![POWERPNT_TYH8LMNxUi](/images/2024-05-19-codinglog/POWERPNT_TYH8LMNxUi.webp)

이러한 기능을 구현하기 위해서 GUI적인 조치가 급선무이다. 우리가 웹개발할때 프론트를 먼저 개발하듯이 말이다.

```python
import tkinter as tk

root = tk.Tk()
root.title("Tkinter Notepad")

# Create a menu bar
menu_bar = tk.Menu(root)
root.config(menu=menu_bar)

# File menu
file_menu = tk.Menu(menu_bar, tearoff=0)
menu_bar.add_cascade(label="File", menu=file_menu)

# Help menu
help_menu = tk.Menu(menu_bar, tearoff=0)
menu_bar.add_cascade(label="Help", menu=help_menu)

# Text widget
t = tk.Text(root, height=20, width=40)
t.pack()

tk.mainloop()
```

result>

![python_IMoIQr84wK](/images/2024-05-19-codinglog/python_IMoIQr84wK.webp)

그리고 여기다가 스크롤바도 하나 넣어주자.

```python
import tkinter as tk

root = tk.Tk()
"""
...
"""
menu_bar.add_cascade(label="Help", menu=help_menu)


# a) Create a frame for text widget and scrollbar
frame = tk.Frame(root)
frame.pack()

# a.1) Declare text widget and scrollbar
scrollbar = tk.Scrollbar(frame)
t = tk.Text(frame, height=20, width=40, yscrollcommand=scrollbar.set)
scrollbar.config(command=t.yview)

# a.2) Pack text widget and scrollbar
t.pack(side=tk.LEFT, fill=tk.BOTH, expand=True)
scrollbar.pack(side=tk.RIGHT, fill=tk.Y)

tk.mainloop()
```



![POWERPNT_KKXiDokZmn](/images/2024-05-19-codinglog/POWERPNT_KKXiDokZmn.webp)



이제 위에서 설계한 프로세스를 하나씩 넣어주겠다. 이를 위해 다음과 같이 함수명을 구축해보았다.

![POWERPNT_UB9EXC5LuR](/images/2024-05-19-codinglog/POWERPNT_UB9EXC5LuR.webp)



```python
from tkinter import filedialog
from tkinter import messagebox

def new_file():
    t.delete(1.0, tk.END)

def open_file():
    file_path = filedialog.askopenfile(
        defaultextension=".txt", filetypes=[("Text Files", "*.txt"),("All Files", "*.*")]
    )
    if file_path:
        t.delete(1.0, tk.END)
        with open(file_path, "r") as file:
            t.insert(tk.END, file.read())

def save_file():
    file_path = filedialog.asksaveasfilename(
        defaultextension=".txt", filetypes=[("Text Files", "*.txt"),("All Files", "*.*")]
    )
    if file_path:
        with open(file_path, "w") as file:
            file.write(t.get(1.0, tk.END))
```

이후, 해당기능을 버튼을 통해 실행할 수 있게, 해야 하는디....

메뉴버튼 하단에 서브버튼을 만들어주자. 마지막 서브버튼으로는 나가기 버튼도 만들어 주었다.

```python
# File menu
file_menu = tk.Menu(menu_bar, tearoff=0)
menu_bar.add_cascade(label="File", menu=file_menu)
file_menu.add_command(label='New', command=new_file)
file_menu.add_command(label='Open', command=open_file)
file_menu.add_command(label="Save", command=save_file)
file_menu.add_separator()
file_menu.add_command(label="Exit", command=root.quit)
```

result>

![POWERPNT_Y8uEKchhFg](/images/2024-05-19-codinglog/POWERPNT_Y8uEKchhFg.webp)

그리고 추가적으로 "도움말"버튼을 메뉴방 안에 만들었다. 누가 만들었는지 등의 정보가 필요하기 때문이다.

```python
"""
...
"""
def about():
    messagebox.showinfo("About", "Simple Tkinter Notepad\nCreated by JackSmith")
"""
...
"""
# Help menu
help_menu = tk.Menu(menu_bar, tearoff=0)
menu_bar.add_cascade(label="Help", menu=help_menu)
help_menu.add_command(label="About", command=about)
"""
...
"""
```

result>

![POWERPNT_P7isKdVxvf](/images/2024-05-19-codinglog/POWERPNT_P7isKdVxvf.webp)



