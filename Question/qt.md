# qDebug()无输出时的解决办法
https://stackoverflow.com/questions/30583577/qt-qdebug-not-working-with-qconsoleapplication-or-qapplication
/etc/xdg/QtProject/qtlogging.ini 或 ~/.config/QtProject/qtlogging.ini 文件里添加如下
[Rules]
*.debug=true
qt.*.debug=false
