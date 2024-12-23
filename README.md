
# Visual Pathway Analysis

This repository contains code, scripts, and documentation for a project focusing on structural, diffusion-weighted imaging (DWI), and functional MRI (fMRI) data preprocessing, feature extraction. The primary regions of interest are the **Lateral Geniculate Nucleus (LGN)** and the **optic tract and radiation**.

## Repository Structure

- **data/**: Raw and preprocessed data.
- **scripts/**: Contains all scripts for data preprocessing and analysis.
- **results/**: Outputs, graphs, and classification results.
- **docs/**: Additional documentation and references.
- **docker/**: Configuration files and commands for using Docker containers.

## Project Pipeline

1. **Structural MRI Preprocessing**  
   Includes brain extraction, segmentation, and thalamic nuclei segmentation.

2. **Diffusion MRI Preprocessing**  
   - Noise reduction using MRtrix3.
   - Response function estimation with `dhollander` method.
   - FOD computation for tractography.
   - Registration to anatomical space.

3. **Functional MRI Preprocessing**  
   - Processed with **fMRIPrep**.
   - Quality control using **MRIQC**.
   - LGN functional activation analysis with visual stimulation paradigms.

4. **Feature Extraction and Classification**  
   - Extract features from LGN, optic tract, and optic radiation.
   - Classify participants using statistical and machine learning approaches.

## Key Commands

### 1. fMRIPrep
```bash
time docker run -it --rm \
  -v /path/to/data:/data:ro \
  -v /path/to/derivatives:/out \
  -v /path/to/license.txt:/FS_LICENSE/license.txt \
  nipreps/fmriprep:20.2.6 \
  /data /out \
  --skip-bids-validation \
  participant \
  --participant-label 70 \
  --output-spaces T1w \
  --output-layout bids \
  --fs-license-file /FS_LICENSE/license.txt \
  --return-all-components \
  --nthreads 20 --mem_mb 20000 \
  --write-graph --notrack \
  --dummy-scans 0 \
  --random-seed 0 \
  --stop-on-first-crash
```

### 2. MRIQC
```bash
docker run -it --rm \
  -v /path/to/data:/data:ro \
  -v /path/to/derivatives_mriqc:/out \
  nipreps/mriqc:latest \
  /data /out \
  participant \
  --participant_label 01 02 03 04 05 ...
```

### 3. QSIPrep
```bash
docker run -it --rm \
  -v /path/to/data:/data:ro \
  -v /path/to/derivatives:/out \
  -v /path/to/license.txt:/FS_LICENSE/license.txt \
  pennbbl/qsiprep:0.23.0 \
  /data /out \
  participant \
  --participant-label 04 \
  --fs-license-file /FS_LICENSE/license.txt \
  --nthreads 20 \
  --mem_mb 20000 \
  --output_resolution 1.2
```

### 4. Thalamic Nuclei Segmentation
```bash
time segmentThalamicNuclei.sh sub-${subject_id}
```

### 5. ROI Segmentation and Tractography
```bash
# LGN and Visual Pathway ROIs
mrcalc <input> 8109 -eq <output> # Left LGN
mrcalc <input> 8209 -eq <output> # Right LGN
tckgen <fod_input> ... # Tractography
```

## References
This project is inspired by research on the LGN and visual pathways, including studies like [Sardaripour et al., 2022](https://doi.org/10.1101/2022.09.12.506825).

## Acknowledgments
We thank the National Brain Mapping Laboratory (NBML) for data acquisition and support.
