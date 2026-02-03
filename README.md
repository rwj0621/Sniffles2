# Sniffles2 pipeline
## 一、数据
### 1.HG002 HiFi数据来自GIAB
* 参考文献 [Detection of mosaic and population-level structural variants with Sniffles2](https://www.nature.com/articles/s41587-023-02024-y)
* 下载路径[HG002.Sequel.15kb.pbmm2.hs37d5.whatshap.haplotag.RTG.10x.trio.bam](https://ftp-trace.ncbi.nlm.nih.gov/ReferenceSamples/giab/data/AshkenazimTrio/HG002_NA24385_son/PacBio_CCS_15kb/alignment/)
  HG002的比对、10x+家系单倍型分型高保真长读长hs37d5比对数据
* 验证数据的MD5

        certutil -hashfile "E:\迅雷下载\HG002.Sequel.15kb.pbmm2.hs37d5.whatshap.haplotag.RTG.10x.trio.bam" MD5
### 2.HG002的金标准（高置信SV）
* 下载路径[HG002_SVs_Tier1_v0.6.vcf.gz](https://ftp-trace.ncbi.nlm.nih.gov/ReferenceSamples/giab/release/AshkenazimTrio/HG002_NA24385_son/NIST_SV_v0.6/)
### 3.下载GRCh37注释文件

        wget https://raw.githubusercontent.com/PacificBiosciences/pbsv/master/annotations/human_hs37d5.trf.bed
### 4.HCC1395数据
* 肿瘤下载路径[HCC1395.GRCh38.bam](https://downloads.pacbcloud.com/public/revio/2023Q2/HCC1395/HCC1395/analysis/HCC1395.GRCh38.bam)
* 正常下载路径[HCC1395-BL.GRCh38.bam](https://downloads.pacbcloud.com/public/revio/2023Q2/HCC1395/HCC1395-BL/analysis/HCC1395-BL.GRCh38.bam)
### 5.下载GRCh38注释文件

        wget https://raw.githubusercontent.com/PacificBiosciences/pbsv/master/annotations/human_GRCh38_no_alt_analysis_set.trf.bed
    
## 二、创建Sniffles2运行环境
### 1.创建conda环境并安装Sniffles2

    conda create -n sniffles2 python=3.10.15 -y
    conda activate sniffles2
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
### 4.运行Sniffles2（HG002）
* 输入

        sniffles -i /data/renweijie/data/HG002/HG002.Sequel.15kb.pbmm2.hs37d5.whatshap.haplotag.RTG.10x.trio.bam \
         -v /data/renweijie/data/HG002/Sniffles2/HG002_output.vcf \
         --reference  /data/renweijie/data/GRCh37/hs37d5.fa \
         --tandem-repeats /data/renweijie/data/GRCh37/human_hs37d5.trf.bed \
         --threads 8
        
* 输出
[输出文件](https://github.com/rwj0621/Sniffles2/blob/main/HG002_output.vcf)
* 统计SV数量

      cd /data/renweijie/data/HG002/Sniffles2
      # 统计总SV数
      grep -v "^#" HG002_output.vcf | wc -l
      # 按SV类型统计
      grep -v "^#" HG002_output.vcf | grep -o "SVTYPE=[^;]*" | cut -d= -f2 | sort | uniq -c
### 5.运行Sniffles2（HCC1395）参照文献severus代码部分
#### (1)分析肿瘤样本（HCC1395）
* 输入

        sniffles -i /data/renweijie/data/HCC1395/HCC1395.GRCh38.bam \
         -v /data/renweijie/Softwares/SV_tools/sniffles2/HCC1395_output.vcf \
         --snf /data/renweijie/Softwares/SV_tools/sniffles2/HCC1395_output.snf \
         --reference  /data/renweijie/data/GRCh38/GRCh38.d1.vd1.fa \
         --tandem-repeats /data/renweijie/data/GRCh38/human_GRCh38_no_alt_analysis_set.trf.bed \
         --threads 4 \
         --minsvlen 50 --allow-overwrite
 * HCC1395(2022年PacBio_PBMM2)

        sniffles -i /data/renweijie/data/HCC1395/HCC1395_pacbio_PBMM2/tumor/tumor.pacbio.PBMM2.bam \
         -v /data/renweijie/Softwares/SV_tools/sniffles2/2022_HCC1395_PacBio_pbmm2_sniffle2_output/2022_HCC1395_PacBio_output.vcf \
         --snf /data/renweijie/Softwares/SV_tools/sniffles2/2022_HCC1395_PacBio_pbmm2_sniffle2_output/2022_HCC1395_PacBio_output.snf \
         --reference  /data/renweijie/data/GRCh38/GRCh38.d1.vd1.fa \
         --tandem-repeats /data/renweijie/data/GRCh38/human_GRCh38_no_alt_analysis_set.trf.bed \
         --threads 2 \
         --minsvlen 50 --allow-overwrite
#### (2)分析正常样本（HCC1395_BL）
* 输入

        sniffles -i /data/renweijie/data/HCC1395/HCC1395-BL.GRCh38.bam \
         -v /data/renweijie/Softwares/SV_tools/sniffles2/HCC1395-BL_output.vcf \
         --snf /data/renweijie/Softwares/SV_tools/sniffles2/HCC1395-BL_output.snf \
         --reference  /data/renweijie/data/GRCh38/GRCh38.d1.vd1.fa \
         --tandem-repeats /data/renweijie/data/GRCh38/human_GRCh38_no_alt_analysis_set.trf.bed \
         --threads 4 \
         --minsvlen 50 --allow-overwrite
* HCC1395(2022PacBio_PBMM2)


        sniffles -i /data/renweijie/data/HCC1395/HCC1395_pacbio_PBMM2/nomal/normal.pacbio.PBMM2.bam \
         -v /data/renweijie/Softwares/SV_tools/sniffles2/2022_HCC1395_PacBio_pbmm2_sniffle2_output/2022_HCC1395_PacBio_Normal_output.vcf \
         --snf /data/renweijie/Softwares/SV_tools/sniffles2/2022_HCC1395_PacBio_pbmm2_sniffle2_output/2022_HCC1395_PacBio_Normal_output.snf \
         --reference  /data/renweijie/data/GRCh38/GRCh38.d1.vd1.fa \
         --tandem-repeats /data/renweijie/data/GRCh38/human_GRCh38_no_alt_analysis_set.trf.bed \
         --threads 4 \
         --minsvlen 50 --allow-overwrite
         

#### （3）合并分析
* 输入

         sniffles --input /data/renweijie/Softwares/SV_tools/sniffles2/HCC1395-BL_output.snf /data/renweijie/Softwares/SV_tools/sniffles2/HCC1395_output.snf \
         --vcf /data/renweijie/Softwares/SV_tools/sniffles2/HCC1395_merge_normal_tumor.vcf \
         --allow-overwrite \
         --threads 4
* HCC1395(2022PacBio_PBMM2)


        sniffles --input /data/renweijie/Softwares/SV_tools/sniffles2/2022_HCC1395_PacBio_pbmm2_sniffle2_output/2022_HCC1395_PacBio_Normal_output.snf /data/renweijie/Softwares/SV_tools/sniffles2/2022_HCC1395_PacBio_pbmm2_sniffle2_output/2022_HCC1395_PacBio_output.snf \
         --vcf /data/renweijie/Softwares/SV_tools/sniffles2/2022_HCC1395_PacBio_pbmm2_sniffle2_output/2022_HCC1395_PacBio_merge_normal_tumor.vcf \
         --allow-overwrite \
         --threads 4
        

#### （4）体细胞SV
* 安装bcftools

        conda install -c bioconda bcftools
* 输入

        bcftools view -i "SUPP_VEC = '01'" \
        /data/renweijie/Softwares/SV_tools/sniffles2/2022_HCC1395_PacBio_pbmm2_sniffle2_output/2022_HCC1395_PacBio_merge_normal_tumor.vcf \
        > /data/renweijie/Softwares/SV_tools/sniffles2/2022_HCC1395_PacBio_pbmm2_sniffle2_output/HCC1395_somatic.vcf
* HCC1395(2022PacBio_PBMM2)


        bcftools view -i "SUPP_VEC = '01'" \
        /data/renweijie/Softwares/SV_tools/sniffles2/2022_HCC1395_PacBio_merge_normal_tumor.vcf \
        > /data/renweijie/Softwares/SV_tools/sniffles2/2022_HCC1395_PacBio_somatic.vcf
       
* 统计SV数量

        grep -v "^#" /data/renweijie/Softwares/SV_tools/sniffles2/HCC1395_somatic.vcf | wc -l
        #输出2190个
### 6. HCC1395 体细胞SV检测（sniffles自带）
* 输入

         sniffles -i /data/renweijie/data/HCC1395/HCC1395.GRCh38.bam \
         -v /data/renweijie/data/HG002/Sniffles2/HCC1395_SomaticSV_output.vcf \
         --reference  /data/renweijie/data/GRCh38/GRCh38.d1.vd1.fa \
         --tandem-repeats /data/renweijie/data/GRCh38/human_GRCh38_no_alt_analysis_set.trf.bed \
         --threads 4 \
         --mosaic
* 统计SV数量

        cd /data/renweijie/data/HG002/Sniffles2
        grep -v "^#" /data/renweijie/Softwares/SV_tools/sniffles2/HCC1395_somatic.vcf | wc -l
        
## 三、使用truvari与金标准比较
### 1.创建conda环境环境并安装truvari

    conda create -n Truvari python=3.9.16 -y
    conda activate Truvari 
    pip install truvari==5.3.0
    # bcftools 用于排序 vcf 文件中 chr 顺序
    conda install -c bioconda -c conda-forge bcftools -y 
### 2.筛选金标准raw calls里只用PacBio检测出来的变异
* 筛选
  
       awk 'BEGIN {FS=OFS="\t"} NR==1 || $9==1' \
      /data/renweijie/data/HCC1395/HCC1395_truth/13059_2022_2816_MOESM3_ESM.txt \
      > /data/renweijie/data/HCC1395/HCC1395_truth/HCC1395_PacBio.txt
* 统计个数

      wc -l /data/renweijie/data/HCC1395/HCC1395_truth/HCC1395_PacBio.txt
      # 除去头那一行 一共 2348个
      #按照从小到大的顺序排一下
      # 1. 提取表头保存到新文件
      head -n 1 /data/renweijie/data/HCC1395/HCC1395_truth/HCC1395_PacBio.txt > HCC1395_PacBio_sorted.txt
      # 2. 跳过表头，按染色体（第2列）和位置（第3列，数值）排序，并追加到新文件
      tail -n +2 /data/renweijie/data/HCC1395/HCC1395_truth/HCC1395_PacBio.txt | sort -V -k2,2 -k3,3n >> HCC1395_PacBio_sorted.txt
      
      
      
### 3.将金标准文件转换为vcf
## 四、HCC1395结果运行neosv
* 问题：如果直接拿vcf文件运行neosv会报错。原因：neosv只支持有明确断点的SV（BND类型）
* 解决方案：将vcf文件转换成bedpe
### 1.使用svtools转换
* 创建环境并安装[svtools](https://github.com/hall-lab/svtools?tab=readme-ov-file)

       # 创建一个名为svtools的新环境，使用Python 3.8
       conda create -n svtools python=3.8 -y
       # 激活环境
       conda activate svtools
       #安装svtools
       pip install svtools
* vcftobedpe 将vcf文件转换成bedpe

       svtools vcftobedpe \
       -i /data/renweijie/Softwares/SV_tools/manta/WGS_IL_1.manta.somatic.vcf \
       -o /data/renweijie/Softwares/SV_tools/manta/WGS_IL_1.manta.somatic.bedpe
       svtools vcftobedpe \
       -i /data/renweijie/Softwares/SV_tools/sniffles2/HCC1395_somatic.vcf \
       -o /data/renweijie/Softwares/SV_tools/sniffles2/HCC1395_somatic.bedpe
       svtools vcftobedpe \
       -i /data/renweijie/Softwares/SV_tools/severus/HCC1395_Somatic_SV_output/somatic_SVs/severus_somatic.vcf \
       -o /data/renweijie/Softwares/SV_tools/severus/HCC1395_Somatic_SV_output/somatic_SVs/severus_somatic.bedpe
  
* 去掉转换后bedpe的注释行并排序
  注：①表头也有# 直接输入以下命令会把表头也删掉，因此，先把bedpe文件表头的#去掉
      ②转换后的bedpe有的起点不一样的原因是，SV检测工具会输出断点的置信区间

      # 使用awk过滤，保留表头和数据行
      awk 'NR==1 || !/^#/' /data/renweijie/Softwares/SV_tools/manta/WGS_IL_1.manta.somatic.bedpe | \
      sort -k1,1V -k2,2n -k4,4V -k5,5n \
      > /data/renweijie/Softwares/SV_tools/manta/WGS_IL_1.manta.somatic.cleaned.bedpe

     awk 'NR==1 || !/^#/' /data/renweijie/Softwares/SV_tools/sniffles2/HCC1395_somatic.bedpe | \
      sort -k1,1V -k2,2n -k4,4V -k5,5n \
      > /data/renweijie/Softwares/SV_tools/sniffles2/HCC1395_somatic.cleaned.bedpe
      
* 提取neosv需要的列

<img width="746" height="388" alt="image" src="https://github.com/user-attachments/assets/1f93c801-c3cf-4967-a54c-fb9970e59639" />

  
  
          
    


