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
直接使用上面命令遇到了清华镜像源的连接问题（A3服务器内存满了，换用A2服务器）
* 验证python版本

        python --version 
        # 正确显示 Python 3.10.15  
### 2.安装Sniffles2

    conda install sniffles=2.7.2
* 验证安装

        sniffles --version
        # 正确显示 Sniffles2, Version 2.7.2   
    
### 3.安装所有依赖

    conda install pysam>=0.21.0 edlib>=1.3.9 psutil>=5.9.4 numpy>=2.2.0 -y
* 验证安装

        python -c "import edlib; print('edlib导入成功')"
        # 正确显示 edlib导入成功
        python -c "import pysam; print('pysam版本:', pysam.__version__)"
        # 正确显示版本号 pysam版本: 0.23.3
        python -c "import psutil; print('psutil版本:', psutil.__version__)"
        # 正确显示版本号 psutil版本: 7.2.1
        python -c "import numpy; print('numpy版本:', numpy.__version__)"
        # 正确显示版本号 numpy版本: 2.2.6
### 4.运行Sniffles2
* 输入

        sniffles -i /data/renweijie/data/HG002/HG002.Sequel.15kb.pbmm2.hs37d5.whatshap.haplotag.RTG.10x.trio.bam \
         -v /data/renweijie/data/HG002/Sniffles2/HG002_output.vcf \
         --reference  /data/renweijie/data/GRCh37/hs37d5.fa \
         --tandem-repeats /data/renweijie/data/GRCh37/human_hs37d5.trf.bed \
         --threads 8
        
* 输出
[输出文件](https://github.com/rwj0621/Sniffles2/blob/main/HG002_output.vcf)
* 统计SV数量

      # 统计总SV数
      grep -v "^#" HG002_output.vcf | wc -l
      # 按SV类型统计
      grep -v "^#" HG002_output.vcf | grep -o "SVTYPE=[^;]*" | cut -d= -f2 | sort | uniq -c
* 
          
    


