# Practico 1:  Assembly


## Paso 1: Copiar los datos a su home o usuario

```
cp -r /home/dbs/bio-hpc/Curso_Cosas/G”Numero de grupo”_”nombre_del_organismo”/ ./
```

Ejemplo: 

```
cp -r home/dbs/bio-hpc/Curso_Cosas/G1_Aureimonas_altamirensis/ .
```
########################################################## 


## Paso 2: Cargar los modulos necesarios en la consola:

```
ml Java/1.8.0_202
ml FastQC
ml Anaconda3/2020.02
conda init bash
bash
conda activate cursobio
```

### Desde ahora en adelante, su ambiente de Anaconda ya esta activo en su home, por lo que la próxima ves que desee usar programas, solo debera cargar:
```
ml Java/1.8.0_202
ml FastQC
conda activate cursobio
```



## Paso 3: Quality. FastQC Para illumina y PacBio.

Revise que los archivos necesarios estan en el directorio correspondiente

```
cd G”Numero de grupo”_”nombre_del_organismo”
```

Para ejecutar FastQC debera editar el archivo de jobs. Puede usar el ejemplo Job. (ejemplo_job.sh)

*Agregar al archivo con cualquier editor de texto la linea de comando:

```
#!/bin/bash
#SBATCH --job-name=TareasCurso
#SBATCH --partition=slims
#SBATCH --output=out.%a.%N.%j.out
#SBATCH --error=out.%a.%N.%j.err
#SBATCH --mail-user=example@corre.com
#SBATCH --mail-type=ALL
#SBATCH -n 1 # number of jobs
#SBATCH -c 5 # number of cpu
##SBATCH --array=1-24
#SBATCH --mem=20G

fastqc -t 5 “Direcction con los archivos fastq de Illumina” 
```

Ejemplo: 
``` 
fastqc -t 5 Illumina_paired/SRR18249971_1.fastq
```

Cerramos el archivo y lo ejecutamos como:

```
sbatch ejemplo_job.sh
```
Recuerde que puede ver la cola de procesos con el comando:
```
squeue 
watch squeue
```

Una ves terminada la ejecucion, los resultados quedaran en un archivo llamado “name”_1.fastq.html

Ahora hacer lo mismo pero para PacBio. (Abrir archivo, editar, guardar y ejecutar) No olvide comentar la linea de codigo que ya ejecuto anteriormente con # para que no vuelva a ejecutarse.

```
#fastqc -t 5 Illumina_paired/”name”_1.fastq Illumina_paired/”name”_2.fastq

fastqc -t 5 PacBio_single/”name”.fastq
```

Comparar los resultados,  Revisar las calidades
(Pacbio FastQC = https://www.bioinformatics.babraham.ac.uk/projects/fastqc/pacbio_srr075104_fastqc.html)



## Paso 4: Assembly

Dado que ya revisamos las calidad, ahora sigue el turno del assembly

En este caso utilizaremos 2 softwares distintos. Para Illumina utilizaremos Spades y para PacBio Canu:

abra, edite, y ejecute el archivo  ejemplo_job.sh segun corresponda

### Para assembly Illumina

```
spades.py -k21,33,43,55,65,77,87,99 -t 5 -m 20 -1 Illumina_paired/"name"_1.fastq -2 Illumina_paired/"name"_2.fastq -o "directorio_para_resultados" --isolate
```

Al escribir un directorio, recuerde dar la ruta completa. (usar pwd). 
Ejemplo:
```
-o /home/usuario/G7_Klebsiella_pneumoniae/Illumina_paired/
```

En el caso de spades, nuestro resultado final se llamana “scaffolds.fasta”

#--isolate en el caso que tengamos una amplia covertura o que nuestro experiemnto sea single-cell

### Para assembly PacBio

abra, edite, y ejecute el archivo  ejemplo_job.sh segun corresponda

```
canu -d PacBio_single/"assembly_dir/" -p "prefijo" genomeSize="GenomeSize"k/m/g/ -pacbio PacBio_single/"name".fastq
```

Donde encontramos el genomeSize de mi organismo?. “Google is my best friend”
Luego de encontrar el numero de bases, dividir por 1.000, para kilobases, por 1.000.000 para megabases y por 1.000.000.000 para Gigabases.

Ejemplo: 
Tamaño genoma en pares de base: 6.562.642
```
genomeSize=6562k
genomeSize=6.5m
genomeSize=0.6g
```

En el caso de Canum nuestro resultado final se llamara “prefijo”.contigs.fasta

Finalmente compare ambos assemblies y genere conclusiones.
