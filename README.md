# Воспроизведение статьи «Транскрипционный ответ Saccharomyces cerevisiae на энантиомеры молочной кислоты» (Transcriptional response of Saccharomyces cerevisiae to lactic acid enantiomers) для концентрации 45 5 мМ D-лактата)
Это репозиторий для статьи
# Аннотация
Дрожжи Saccharomyces cerevisiae (S. cerevisiae) широко используются в народном хозяйстве, включая производство биосенсоров. Не ясен до конца транскрипционный ответ S. Cerevisiae на D-молочную кислоту (DLA) в различной концентрации. Воспроизводился и изучался транскрипционный ответ штамма BY4742 на концентрацию DLA 45мМ и сравнивался его ответ на 45 мМ L-молочную кислоту (LLA). Ответ зафиксирован (113 дифференциально экспрессируемых генов (DEG) соответственно; > 50% общих). Полученные результаты могут быть полезны для оптимизации производства лактата в дрожжах, а так же в педагогической практике.
Ключевые слова: S. Cerevisiae, D-молочная кислота, воспроизводимое исследование

Команды

# download reference / скачиваем референс
wget https://ftp.ensembl.org/pub/release-108/gtf/saccharomyces_cerevisiae/Saccharomyces_cerevisiae.R64-1-1.108.gtf.gz
wget https://ftp.ensembl.org/pub/release-108/fasta/saccharomyces_cerevisiae/dna/Saccharomyces_cerevisiae.R64-1-1.dna.toplevel.fa.gz
# unpack archives / распаковываем архивы
gunzip Saccharomyces_cerevisiae.R64-1-1.dna.toplevel.fa.gz
gunzip Saccharomyces_cerevisiae.R64-1-1.108.gtf.gz
## hisat: build index and prepare splice file / создаём индекс и готовим файл с данными сплайсинга в hisat2
hisat2-build Saccharomyces_cerevisiae.R64-1-1.dna.toplevel.fa yeast_index
hisat2_extract_splice_sites.py Saccharomyces_cerevisiae.R64-1-1.108.gtf > yeast_splice_sites.txt
## hisat: align & samtools: make sorted bam / выравниваем с помощью hisat2 и сортируем bam-файл с помощью samtools
for sample in `ls *_1.fastq`; do base=$(basename $sample "_1.fastq"); \
hisat2 -x yeast_index --known-splicesite-infile yeast_splice_sites.txt -p 8 \
-1 ${base}_1.fastq -2 ${base}_2.fastq | samtools view --threads 2 -bS | samtools sort --threads 2 -o $base.bam; done
