# 静态编译Qt
`configure -opensource -confirm-license -static -release  -opengl desktop -skip webengine -nomake tools -nomake examples -nomake tests -prefix "D:\Qt\Qt5.9.4_Static_MinGW" -qt-pcre -qt-freetype -platform win32-g++ `

- 关键参数 `-skip webengine`
- 使用 MinGW 时的关键参数 `-qt-pcre`
