如何用命令行压缩和校验一批图片

第一步，安装工具。在 macOS 上执行 brew install imagemagick oxipng，Windows 上用 choco install imagemagick oxipng。安装完成后执行 magick -version 确认版本号能正常打印。

第二步，先备份原图。执行 cp -r ./photos ./photos-backup。压缩是不可逆操作，备份是唯一的安全网。

第三步，批量转换格式。把目录下所有 PNG 转成 WebP，质量设为 82，执行：
magick mogrify -format webp -quality 82 ./photos/*.png
质量参数低于 75 时，渐变区域会出现明显的色带，因此 80 到 85 是画质与体积的实用区间。

第四步，无损再压一遍。执行 oxipng -o 4 --strip safe ./photos/*.png。oxipng 不改变像素，只优化编码方式，所以这一步没有画质损失。-o 后面的数字是优化等级，0 到 6，等级越高越慢，4 是常用折中值。

第五步，校验结果。用 identify -format "%f %b\n" ./photos/* 列出每个文件的大小，和备份目录逐个比对。如果某个文件压缩后反而变大，说明原图已经被优化过，直接用备份覆盖回去即可。

第六步，检查有没有损坏。执行 magick identify ./photos/* 2>errors.log，如果 errors.log 里有内容，说明有文件读取失败，需要单独处理。

最后，只有在确认所有文件都能正常打开之后，才可以删除备份目录。
