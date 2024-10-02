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
Example of command it will run: sbatch trio_cnv_batch and sbatch trio_cnv_batch_part2
apptainer exec cnv.sif perl detect_cnv.pl  -trio -hmm lib/hhall.hmm -pfb hg_38.pfb  -cnv Signals_Complete_Fina/Complete_RawCNV/402-OmniExpress.rawcnv          Signals_Complete_Final/207166300081_R07C02 Signals_Complete_Final/207166300081_R08C02 Signals_Complete_Final/207166300081_R09C02 -log Signals_Complete_Fina/Complete_RawCNV-logs/402-OmniExpress.log -out Signals_Complete_Final/Complete_TrioCNV/402-OmniExpress.triocnv

3. Run the following in the terminal in the triocnv directory:
    cd signal/TrioCNV
    cat *.triocnv > OCD73trioCNVconcat
    cd RawCNV-logs
    cat *.log > OCD73-sample-logs-concat


