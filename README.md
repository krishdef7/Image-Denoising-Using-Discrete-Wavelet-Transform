# Image Denoising: A Comparative Study

## 🎯 Project Goal

This project provides an **interactive desktop application** for comparing different image denoising algorithms on grayscale images corrupted by various types of noise. The primary goal is to demonstrate the strengths and limitations of each denoising technique across different noise scenarios.

## 💡 Motivation

Image denoising is a fundamental problem in digital image processing with applications in:
- Medical imaging (MRI, CT scans)
- Satellite and astronomical imagery
- Photography and video enhancement
- Computer vision preprocessing

**Key Questions This Project Addresses:**
- Which denoising algorithm performs best for each type of noise?
- How do classical spatial filters compare to frequency-domain methods?
- When does wavelet-based denoising excel, and when does it fall short?
- What are the trade-offs between processing time and quality?

Through hands-on experimentation, this project reveals that:
- **Wavelet thresholding** gives the best PSNR on additive Gaussian noise (30.1 dB), but not the best SSIM
- **Median filtering** dominates salt-and-pepper noise (31.8 dB / SSIM 0.96 vs 19.9 dB / 0.55 for wavelet)
- **No single algorithm is universally optimal** — the choice depends on noise characteristics

## ✨ Features

### Denoising Algorithms
- **Median Filter** — Non-linear filter, excellent for salt-and-pepper noise
- **Gaussian Blur** — Spatial smoothing filter
- **Fourier Transform** — Frequency-domain low-pass filtering
- **Wavelet Transform (DWT)** — Multi-resolution analysis with BayesShrink/VisuShrink thresholding

### Noise Types Supported
- **AWGN (Additive White Gaussian Noise)** — Constant variance across the image
- **Salt-and-Pepper** — Random black/white pixel corruption
- **Speckle** — Multiplicative noise (signal-dependent)
- **Poisson** — Shot noise (common in low-light conditions)

### Interactive UI
- Real-time algorithm parameter tuning
- Side-by-side comparison (Clean | Noisy | Denoised)
- Auto-tune feature to find optimal parameters
- Capture and report generation for documentation
- Instant PSNR and SSIM metrics

## 📁 Project Structure

```
ImageDenoising-Discrete-Wavelet-Transform/
├── denoisers/
│   ├── median.py           # Median filter implementation
│   ├── gaussian.py          # Gaussian blur denoiser
│   ├── fourier.py           # Fourier frequency-domain denoiser
│   ├── wavelet.py           # Wavelet DWT denoiser (BayesShrink)
│   └── noise_transforms.py  # Log/Anscombe transforms
├── ui/
│   ├── desktop_app.py       # Main Tkinter GUI application
│   ├── backend.py           # Denoiser execution wrapper
│   └── reporting.py         # Report generation (images + JSON)
├── scripts/
│   ├── data_gen.py          # Synthetic dataset generation
│   └── utils.py             # Metrics (PSNR, SSIM, MSE)
├── data/                    # Generated noisy test images
├── results/                 # Denoised outputs and reports
└── requirements.txt         # Python dependencies
```

## 🚀 Quick Start

### 1. Install Dependencies

```bash
python -m pip install -r requirements.txt
```

### 2. Run the Desktop Application

```bash
# Make sure to set PYTHONPATH for imports
$env:PYTHONPATH="B:/Academia/Projects/Image Denoising"
python -m ui.desktop_app
```

### 3. Generate Test Dataset (Optional)

```bash
python scripts/data_gen.py
```

This creates noisy versions of test images in the `data/` folder with all four noise types.

## 🎮 How to Use

1. **Select an image** from the dropdown (e.g., `astronaut_awgn`, `bars_saltpepper`)
2. **Choose an algorithm** (median, gaussian, fourier, wavelet)
3. **Adjust parameters** using the sliders/dropdowns
4. **Click "Run"** to denoise and view results
5. **Use "Auto-tune"** to find optimal parameters automatically
6. **Click "Capture"** to save the current result
7. **Click "Report"** to generate a comparison report with all captured frames

## 📊 Key Findings

Measured on the `astronaut` test image (`results/awgn/results_summary.csv`, `results/sp/results_summary.csv`):

| Noise | Median | Gaussian | Fourier | Wavelet (db1, BayesShrink) |
|---|---|---|---|---|
| AWGN: PSNR / SSIM | 29.6 dB / **0.80** | 28.8 dB / **0.80** | 28.8 dB / 0.80 | **30.1 dB** / 0.75 |
| Salt-and-pepper: PSNR / SSIM | **31.8 dB / 0.96** | 26.9 dB / 0.74 | 26.6 dB / 0.72 | 19.9 dB / 0.55 |

- **AWGN:** wavelet thresholding has the best PSNR (+0.5 dB over median) but the lowest SSIM. It removes more
  noise energy but loses some structure relative to the spatial filters.
- **Salt-and-pepper:** median filtering wins clearly. Wavelet shrinkage is the worst here: impulse noise is
  sparse and large, which breaks the Gaussian-noise assumption behind BayesShrink.
- **No single algorithm wins everywhere.** The right choice depends on the noise model, and PSNR and SSIM can
  disagree.

Speckle and Poisson noise are supported in the app; their result tables are not committed.

## 🔬 Technical Details

### Wavelet Implementation
- **Basis Functions**: db1 (Haar), db4, sym4, sym8
- **Decomposition Levels**: 3-6 levels
- **Thresholding Methods**:
  - **BayesShrink**: Adaptive threshold based on subband statistics
  - **VisuShrink**: Universal threshold (more aggressive)
- **Noise Estimation**: MAD (Median Absolute Deviation) on finest detail coefficients

### Metrics
- **PSNR (Peak Signal-to-Noise Ratio)**: Higher is better, measured in dB
- **SSIM (Structural Similarity Index)**: 0-1 scale, closer to 1 is better
- **MSE (Mean Squared Error)**: Lower is better

## 📝 Report Generation

The application generates reports containing:
- Side-by-side comparison images (Clean | Noisy | Denoised)
- **Denoised metrics**: PSNR, SSIM, MSE of denoised vs clean
- **Noisy metrics**: PSNR, SSIM, MSE of noisy vs clean (baseline)
- Algorithm parameters used
- Processing time
- JSON metadata for further analysis

Reports are saved in `results/reports/<report_name>/`

## 🎓 Educational Value

This project demonstrates:
- **Multi-resolution analysis** with wavelet decomposition
- **Frequency vs spatial domain** processing trade-offs
- **Adaptive vs fixed thresholding** strategies
- **Impact of noise characteristics** on algorithm performance
- **Empirical validation** through auto-tune experimentation

## 🛠️ Technologies Used

- **Python 3.13**
- **PyWavelets** — Wavelet transform library
- **NumPy** — Numerical computing
- **SciPy** — Signal processing
- **scikit-image** — Image metrics and processing
- **Tkinter** — Desktop GUI framework
- **Matplotlib** — Visualization
- **Pillow** — Image I/O

## 📖 References

- D. L. Donoho and I. M. Johnstone, "Ideal spatial adaptation by wavelet shrinkage," Biometrika, 1994
- S. G. Chang, B. Yu, and M. Vetterli, "Adaptive wavelet thresholding for image denoising," IEEE Trans. Image Processing, 2000
- PyWavelets Documentation: https://pywavelets.readthedocs.io/

## 🙏 Acknowledgments

Test images sourced from scikit-image data collection.

---

**Note**: This project focuses on grayscale image processing for clarity in comparison and educational demonstration.
