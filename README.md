# PENN-CNV
Penn-CNV Instructions
REQUIRED FILES: 
-hg_38.pfb
-cnv.sif 
-hhal.hmm 

1. Run the following commands for each trio by running the following in the terminal: sbatch raw_cnv_batch
   Sheet in V2_OFFICIAL_2024 Olfson_CNV_Calls_data_and commands associated with commands: indiv-rawcnv-command
  Example of command it will run: 
    apptainer exec cnv.sif perl detect_cnv.pl  -test -hmm lib/hhall.hmm -pfb hg_38.pfb Signal/207166290017_R01C02 Signal/207166290017_R02C02 Signal/207166290017_R03C02 -      log Signals/Complete_RawCNV-logs/85.log -out Signals/Complete_RawCNV/85-OmniExpress.rawcnv


2. Run the following commands for each trio by running the following in the terminal:sbatch trio_cnv_batch and sbatch trio_cnv_batch_part2
    Sheet in V2_2024 Olfson_CNV_Calls_data_and commands associated with commands: trio-command
Example of command it will run:
   apptainer exec cnv.sif perl detect_cnv.pl  -trio -hmm lib/hhall.hmm -pfb hg_38.pfb  -cnv    Signals_Complete_Fina/Complete_RawCNV/402-OmniExpress.rawcnv          Signals_Complete_Final/207166300081_R07C02 Signals_Complete_Final/207166300081_R08C02 Signals_Complete_Final/207166300081_R09C02 -log Signals_Complete_Fina/Complete_RawCNV-logs/402-OmniExpress.log -out Signals_Complete_Final/Complete_TrioCNV/402-OmniExpress.triocnv

3. Run the following in the terminal in the triocnv directory:
    cd signal/TrioCNV
    cat *.triocnv > OCD73trioCNVconcat
    cd RawCNV-logs
    cat *.log > OCD73-sample-logs-concat

4. Quality control parameters to talk:

  apptainer exec cnv.sif perl filter_cnv.pl -numsnp 10 -length 50k OCD73trioCNVconcat -output OCD_QC1
  
  apptainer exec cnv.sif perl filter_cnv.pl OCD_QC1 --qclogfile OCD73-sample-logs-concat --qclrrsd 0.3 --qcpassout sampleall.qcpass -qcsumout sampleall.qsum -out sampleall.goodcnvs
  
  apptainer exec cnv.sif perl scan_region.pl sampleall.goodcnvs imm_region -minqueryfrac 0.5 > cnvcall.imm; fgrep -v -f cnvcall.imm sampleall.goodcnvs > cnvcall.clean

   apptainer exec cnv.sif perl scan_region.pl cnvcall.clean centromeric_telomeric_regions -minqueryfrac 0.5 > cnvcall.imm; fgrep -v -f cnvcall.imm cnvcall.clean > sampleall.clean

5. Annotation:
   apptainer exec cnv.sif perl scan_region.pl sampleall.clean -knowngene knownGene_hg38txt -kgxref kgXref_hg38.txt > OlfsonAnnotated.rg38<img width="1450" alt="image" src="https://github.com/user-attachments/assets/56271b8a-2c41-4c74-9814-84195792a695">

############# Large, rare CNV #############

               
############# De Novo Calling  ############# 
6. Download results and filter in excel by "Offspring only". Reupload new spreadsheet. 

7. Run the following script in rstudio: Merging_family_for_de_novo_calling.R and download the spreadsheet. This will be the information you use to modify the commands in the Sheet in Official_2024 Olfson_CNV_Calls_data_and commands associated with commands: De_novo-command. These commands will be added to each batch script below in bunches. 

8. Run the following commands for each trio by running the following in the terminal:sbatch denov0_cnv_batch_part1, denovo_cnv_batch 8part2 denovo_cnv_batch _ part3 denovo_cnv_batch _part4 denovo_cnv_batch _part5

Example of command it will run: sbatch trio_cnv_batch and sbatch trio_cnv_batch_part2: 
   apptainer exec cnv.sif perl infer_snp_allele.pl -pfb  hg_38.pfb  -hmm lib/hhall.hmm -denovocn 3 Signals_Complete_Final/207221090005_R04C02 Signals_Complete_Final/207221090005_R05C02 Signals_Complete_Final/207221090005_R06C02 -start GSA-rs72917720 -end GSA-rs77445654 -log Signals_Complete_Final/Complete_DeNovo/log_207221090005_R06C02_chr2:193228891-193446437 -out Signals_Complete_Final/Complete_DeNovo/207221090005_R06C02_chr2:193228891-193446437

9. Run in terminal to select for variants marked as likely de novo:
      cat 20* > combined_file.csv 
      cat log* > merged_data.csv
      Run Unique_Denovo_Trios.R in /gpfs/gibbs/project/olfson/srg52/Project_3_Emily/CNV_Emily/Signals_Complete_Final/Complete_DeNovo



