+++

author = "邱圆辉"

title = "Win10 把CapsLock键映射为Esc键"

date = "2020-09-03"

description = "Windows系统映射CapsLock为Esc"

tags = [
    "notes"
]

images = ["http://47.94.16.255/images/images/2020/08/09/ocean-3605547_1280.jpg"]

+++

因为编辑文本需要用到vim，所以把CapsLock映射为Esc比较方便。

- 把以下代码保存为caps.reg文件，运行。

  ```
  Windows Registry Editor Version 5.00
  
  [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Keyboard Layout]
  "Scancode Map"=hex:00,00,00,00,00,00,00,00,02,00,00,00,01,00,3a,00,00,00,00,00
  ```

- 解释

  ```
  1. 00 00 00 00
  2. 00 00 00 00
  3. 02 00 00 00
  4. 01 00 3a 00
  5. 00 00 00 00
  ```

  - 前两行和最后一行是固定的8个0
  - 第三行第一个字节为非零行数，也就是需要映射的键位数+1
  - 第四行表示把3a00（CapsLock）映射为0100（Esc）