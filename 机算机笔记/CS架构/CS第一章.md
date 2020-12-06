# 1.窗体

## 1.窗体命名:Frm开头(遵循命名规则)

## 2.如何设置启动窗体?

### 在 main函数中,这句代码设置

### Application.Run(new Frmone());

## 3.窗体的属性:

  Name 窗口,以FRM开头

test :窗口标题

icon 窗口图标

backcolor 背景色

background

4.窗口方法

show();

showDiolog();

# 窗口事件:

  **//窗体的加载事件**
        **private void FrmEvent_Load(object sender, EventArgs e)**
        **{**
            **//修改窗体的标题**
            **this.Text = "QQ登录";**
            **//修改背景色**
            **this.BackColor = Color.Red;**
            **//修改边框样式**
            **this.FormBorderStyle = FormBorderStyle.FixedSingle;**

        }



