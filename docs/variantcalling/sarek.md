# Sarek: a Variant Calling Pipeline


## Overview


## Experimental Design



## GATK Best Practices





## Running Sarek Germline


### Reference Genome

```groovy
igenomes_base = '/workspace/gitpod/nf-training/data/refs/'
genomes {
	'GRCh38chr21' {
		bwa                   = "${params.igenomes_base}/sequence/Homo_sapiens_assembly38_chr21.fasta.{amb,ann,bwt,pac,sa}"
		dbsnp                 = "${params.igenomes_base}/annotations/dbsnp_146.hg38_chr21.vcf.gz"
		dbsnp_tbi             = "${params.igenomes_base}/annotations/dbsnp_146.hg38_chr21.vcf.gz.tbi"
		dbsnp_vqsr            = '--resource:dbsnp,known=false,training=true,truth=false,prior=2.0 dbsnp_146.hg38_chr21.vcf.gz'
		dict                  = "${params.igenomes_base}/sequence/Homo_sapiens_assembly38_chr21.dict"
		fasta                 = "${params.igenomes_base}/sequence/Homo_sapiens_assembly38_chr21.fasta"
		fasta_fai             = "${params.igenomes_base}/sequence/Homo_sapiens_assembly38_chr21.fasta.fai"
		germline_resource     = "${params.igenomes_base}/annotations/gnomAD.r2.1.1.GRCh38.PASS.AC.AF.only_chr21.vcf.gz"
		germline_resource_tbi = "${params.igenomes_base}/annotations/gnomAD.r2.1.1.GRCh38.PASS.AC.AF.only_chr21.vcf.gz.tbi"
		known_snps            = "${params.igenomes_base}/annotations/1000G_phase1.snps.high_confidence.hg38_chr21.vcf.gz"
		known_snps_tbi        = "${params.igenomes_base}/annotations/1000G_phase1.snps.high_confidence.hg38_chr21.vcf.gz.tbi"
		known_snps_vqsr       = '--resource:1000G,known=false,training=true,truth=true,prior=10.0 1000G_phase1.snps.high_confidence.hg38_chr21.vcf.gz'
		known_indels          = "${params.igenomes_base}/annotations/Mills_and_1000G_gold_standard.indels.hg38_chr21.vcf.gz"
		known_indels_tbi      = "${params.igenomes_base}/annotations/Mills_and_1000G_gold_standard.indels.hg38_chr21.vcf.gz.tbi"
		known_indels_vqsr     = '--resource:mills,known=false,training=true,truth=true,prior=10.0 Mills_and_1000G_gold_standard.indels.hg38_chr21.vcf.gz'
		snpeff_db     = '105'
		snpeff_genome = 'GRCh38'
	}
}
```

### Computing resources



```groovy
params {
	max_cpus   = 2
    max_memory = '6.5GB'
    max_time   = '2.h'
	use_annotation_cache_keys = true
}
```

### Filtering parameters


```groovy
process {
    withName: 'VARIANTRECALIBRATOR_INDEL' {
        ext.prefix = { "${meta.id}_INDEL" }
        ext.args = "-an QD -an FS -an SOR -an DP  -mode INDEL"
        publishDir = [
            enabled: false
        ]
    }

    withName: 'VARIANTRECALIBRATOR_SNP' {
        ext.prefix = { "${meta.id}_SNP" }
        ext.args = "-an QD -an MQ -an FS -an SOR -mode SNP"
        publishDir = [
            enabled: false
        ]
    }
}
```


### Launcing the pipeline


```bash
nextflow run nf-core/sarek \
--input /workspace/gitpod/nf-training/data/reads/variantcalling/sarek-input.csv \
--outdir . \
--tools haplotypecaller,snpeff \
--genome GRCh38chr21 \
--joint_germline \
--intervals /workspace/gitpod/nf-training/variantcalling/chr21_intervals.list
```