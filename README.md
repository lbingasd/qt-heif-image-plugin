# qt image 插件去读Apple .heic文件格式
# 依赖
[HEIF]: https://en.wikipedia.org/wiki/High_Efficiency_Image_File_Format
[libheif]: https://github.com/strukturag/libheif
## libheif 编译
You can build and install libheif using the vcpkg dependency manager:
git clone https://github.com/Microsoft/vcpkg.git
安装保存路径形如：C:\code\vcpkg\installed\x64-windows
```
cd vcpkg
./bootstrap-vcpkg.bat
./vcpkg integrate install
./vcpkg install libheif
```

# 编译 qt-heif-image-plugin
```
修改src内的cmake 关于libheif存储路径
$ mkdir build
$ cd build
$ cmake ..
$ make
```
# 使用
## 拷贝依赖只QT 图片插件目录 
C:\Qt\6.5.3\msvc2019_64\plugins\imageformats
```
qheif.dll
qheifd.dll
qheifd.pdb
```
## 拷贝libheif 依赖文件至运行程序目录下
```
clang_rt.asan_dynamic-x86_64.dll
heif.dll
libde265.dll
libx265.dll
```
