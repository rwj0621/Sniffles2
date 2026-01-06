# Sniffles2 pipeline
## 一、数据
### 1.HG002 HiFi数据来自GIAB
* 参考文献 [Detection of mosaic and population-level structural variants with Sniffles2](https://www.nature.com/articles/s41587-023-02024-y)
* 下载路径[HG002.Sequel.15kb.pbmm2.hs37d5.whatshap.haplotag.RTG.10x.trio.bam](https://ftp-trace.ncbi.nlm.nih.gov/ReferenceSamples/giab/data/AshkenazimTrio/HG002_NA24385_son/PacBio_CCS_15kb/alignment/)
* 验证数据的MD5

        certutil -hashfile "E:\迅雷下载\HG002.Sequel.15kb.pbmm2.hs37d5.whatshap.haplotag.RTG.10x.trio.bam" MD5
    
## 二、创建Sniffles2运行环境
### 1.创建conda环境并安装Sniffles2

    conda create -n sniffles2 python=3.10.15 -y
*验证python版本

    python --version 
    #正确显示Python 3.10.15  
    
直接使用上面命令遇到了清华镜像源的连接问题（A3服务器内存满了，换用A2服务器）

