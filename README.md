# Mkv Auto Subset

![GitHub release (latest SemVer including pre-releases)](https://img.shields.io/github/v/release/KurenaiRyu/MkvAutoSubset?include_prereleases)

ASS字幕字体子集化 MKV批量提取/生成

## mkvtool 安装

### 依赖

- FontTools
  ```shell
  apt install python3-fonttools #Debian/Ubuntu
  apk add py3-fonttools #Alpine
  pip install fonttools #Use pip
  ```
- MKVToolNix
  ```shell
  apt install mkvtoolnix #Debian/Ubuntu
  apk add mkvtoolnix #Alpine
  ```
#### 关于Windows用户

- 从 [这里](https://www.python.org/downloads) 下载并安装Python
- 命令提示符(CMD)里参考上面使用pip的方式安装FontTools依赖
- 从 [这里](https://www.fosshub.com/MKVToolNix.html) 下载并安装MKVToolNix
- 保证以上两个依赖项的相关可执行文件(_ttx.exe_,_pyftsubset.exe_,_mkvextract.exe_,_mkvmerge.exe_)在 **path** 环境变量里

### 本体

- 有安装Go的情况:
  ```shell
  go install https://github.com/KurenaiRyu/MkvAutoSubset/mkvtool@latest #安装和更新
  ```
- 手动安装:

  [点此下载](https://github.com/KurenaiRyu/MkvAutoSubset/releases/latest)

## mkvtool 功能及使用示例

- 从单个(或文件夹的)mkv文件里抽取字幕和字体*并创建子集化后的版本(可选)*
  ```shell
  mkvtool -d -f file.mkv #单个文件
  mkvtool -d -s bangumi #文件夹
  
  #可选"-n"参数:当"-n"存在时,只抽取内容,不进行子集化操作.
  #可选"-data"参数,指定输出目录,默认输出到"${workdir}/data".
  ```
- 检测单个(或文件夹的)mkv文件字幕和字体,判断是否需要子集化.
  ```shell
  mkvtool -q -f file.mkv #单个文件,会直接输出是否需要子集化
  mkvtool -q -s bangumi #文件夹,会将需要子集化的文件列表输出至"${workdir}/list.txt".
  ```
- 将子集化后的字幕与字体替代原有的内容
  ```shell
  mkvtool -m -s bangumi -data data -dist dist
  
  #-data参数默认值为"${workdir}/data"
  #-dist参数默认值为"${workdir}/dist"
  #假设bangumi文件夹里的目录结构如下所示:
  #bangumi
  # |-- S01
  # ||-- abc S01E01.mkv
  # ||-- abc SxxExx.mkv
  # |-- SP.mkv
  # |-- xx.mkv
  #那么对应的data文件夹的目录结构应该是如下的所示:
  #data
  # |-- S01
  # ||-- abc S01E01
  # |||-- ...
  # |||-- subsetted
  # |||-- xxx.sub
  # ||-- abc SxxExx
  # |||-- ...
  # |||-- subsetted
  # |||-- xxx.sub
  # |||-- ...
  # |-- SP
  # |||-- ...
  # |||-- subsetted
  # |||-- xxx.sub
  # |||-- ...
  # |-- xx
  # |||-- ...
  # |||-- subsetted
  # |||-- xxx.sub
  # |||-- ...
  
  #*奇淫巧技:指定一个没有任何内容的data目录,将输出一个"干净"的mkv文件.
   ```
- 从一组文件夹获得情报并生成一组mkv
  ```shell
  mkvtool -c -s bangumi
  
  #可选"-clean"参数:当"-clean"存在时,将清空原有的字幕和字体(默认为追加).
  #bangumi文件夹里的目录结构应如下所示:
  #bangumi
  # |-- v
  # ||-- aaa.mkv
  # ||-- bbb.mp4
  # ||-- ccc.avi
  # |-- s
  # ||-- aaa.ass
  # ||-- aaa.srt
  # ||-- aaa.sup
  # ||-- aaa.xxx
  # ||-- bbb.xxx
  # ||-- ccc.xxx
  # |-- f
  # ||-- abc.ttf
  # ||-- def.ttc
  # ||-- ghi.otf
  # ||-- ...
  
  #若遇到ass字幕会自动进行子集化操作.
  #成品会放在"${bangumi}/o"文件夹中.
  ```
- 对一个(或多个)ass字幕进行字体子集化
  ```shell
  mkvtool -a aaa.ass -a bbb.ass -af fonts -ao output [-ans]
  
  #"-a"参数为ass字幕文件路径,可复用.
  #"-af"参数为字体文件夹路径,默认值为"${workdir}/fonts".
  #"-ao"参数为子集化成品输出路径,默认值为"${workdir}".
  #使用参数"-ans"可使其直接输出于"${output}",但会预先清空该文件夹,慎用.
  #*即:当"-ans"参数存在时输出目录为"${output}",否则为${output}/subsetted".
  ```

### 一些碎碎念

- "-m","-c"模式下的"-sl","-st"参数:
   ```
   -sl:字幕语言.格式为语言缩写如"chi","jpn","eng"等,默认值为"chi".
   -st:字幕标题.该字幕在播放器里显示的标题,默认值为空.
   ```
- 字幕文件名规范:
  ```
  抽取出来的字幕长得像是如下的样子:
  a_b_c.d
  a:轨道编号(在"-c"模式里,这里应该和视频文件的文件名相同.)
  b:字幕语言代码
  c:字幕标题
  d:字幕文件后缀名
  
  那么,请体会在"-c"模式中,以下的命名方式所带来的便利:
  |-- v
  ||-- aaa.mp4
  |-- s
  ||-- aaa_chi_简体中文.ass
  ||-- aaa_chi_繁體中文.srt
  ||-- aaa_jpn_日本語.sup
  ||-- aaa_eng_English.srt
  ```
- 字幕语言代码表:

  [点此获取](https://www.science.co.il/language/Codes.php)