# 1.

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<script>
    window.onload = function() {
        text1.onblur = function() {
            var str = this.value;

            var rex = /^([\u4E00-\u9FA5])*$/;
            if (rex.test(str)) {
                this.nextSibling.innerText = "✔";
                this.nextSibling.style.color = "green";

            } else {
                this.nextSibling.innerText = "✖";
                this.nextSibling.style.color = "red";
            }
        };

        text2.onblur = function() {
            var str = this.value;

            var re = /^[0-9]+.?[0-9]*$/;

            if (re.test(str)) {
                this.nextSibling.innerText = "✔"
                this.nextSibling.style.color = "green";
            } else {
                this.nextSibling.innerText = "✖"
                this.nextSibling.style.color = "red";
            }
        };
        text3.onblur = function() {
            var str = this.value;

            var re = /^[1](([3][0-9])|([4][5,7,9])|([5][0-9])|([6][6])|([7][3,5,6,7,8])|([8][0-9])|([9][8,9]))[0-9]{8}$/;

            if (re.test(str)) {
                this.nextSibling.innerText = "✔"
                this.nextSibling.style.color = "green";
            } else {
                this.nextSibling.innerText = "✖"
                this.nextSibling.style.color = "red";
            }
        };
    }
</script>

<body>
    <form action="">
        姓名:<input type="text" id="text1" name="input1"><label for=""></label><br> 年龄:
        <input type="text" id="text2" name="input2"><label for=""></label><br> 手机号:
        <input type="text" id="text3" name="input3"><label for=""></label>
    </form>
</body>

</html>

```

## 2.效果如下

![](D:\Typora图片\js\1.png)