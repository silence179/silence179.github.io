由于nvim的lsp打开example的first.cc文件报很多找不到头文件,发现根目录的compile_commands.json是空的,我去用  
```bash
find . -type f -name "compile_commands.json"
```
输出如下
```
./cmake-cache/compile_commands.json
./compile_commands.json
```
于是我将camke-cache的复制出来了,然后能读取到了
