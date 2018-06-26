#
## 1.python3.5
## 2.nvidia最新驱动，如nvidia384.90(2017.11.9最新), 安装包自己下载，不要用ubuntu软件更新那里的
## 3.环境变量
export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/usr/local/cuda/lib64:/usr/local/cuda/extras/CUPTI/lib64"
export CUDA_HOME=/usr/local/cuda
## 4.教程
http://kislayabhi.github.io/Installing_CUDA_with_Ubuntu/
http://www.jianshu.com/p/ea169536850f
