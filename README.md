# melanin-ar-engine

**A bias-corrected computer vision and colorimetric AR pipeline for accurate skin-tone diagnostics, undertone classification, and photorealistic virtual try-on on melanin-rich complexions (Monk Skin Tone 7–10).**

---

## 1. Clinical Problem Statement

Foundational computer vision datasets (FFHQ, CelebAMask-HQ) exhibit severe demographic skew—under-representing dark-red and dark-yellow skin profiles (`L* ≤ 60`, `h*`)—while commercial Virtual Try-On (VTO) pipelines rely on uncalibrated sRGB clustering and unidimensional luminance scales (`ITA°`) that entangle ambient shadow with epidermal melanin and discard the red-green opponent axis (`a*`). When coupled with generative diffusion models whose global cross-attention overwrites low-luminance pixel distributions and alters ethnic craniofacial geometry, existing systems produce perceptual color shifts exceeding `ΔE > 10` under non-ideal illumination. Consequently, Black female consumers (Monk Skin Tone 7–10) experience systematic skin-tone bleaching, ashy or chalky cosmetic rendering, and inaccurate foundation and undertone recommendations.

---

## 2. Core Technical Architecture

`melanin-ar-engine` replaces device-dependent RGB averaging and entangled generative transfer with a modular, physics-grounded computer vision pipeline:

1. **Illumination Quality Gate:** Pre-screens input frames for shadow and exposure corruption without misclassifying low-reflectance skin (MST 7–10) as "poorly lit," ensuring downstream colorimetric stability (`ΔE₀₀ < 2.0`).
2. **Semantic Skin & Feature Parsing:** Isolates non-occluded facial skin ROIs (cheeks, forehead, neck) from lips, eyes, and specular highlights using real-time semantic segmentation and `21 × 21` Gaussian texture suppression (`σ = 5`).
3. **Multidimensional CIELAB (`L*`, `h*`) Colorimetry:** Transforms sRGB inputs under CIE D65 illuminant assumptions into perceptual lightness (`L*`) and chromatic Hue Angle (`h* = arctan(b* / a*)`), resolving the undertone blindness of unidimensional `ITA°` on deep complexions.
4. **Probabilistic Monk Skin Tone (MST) Stratification:** Models extracted skin pixels as a 3D color probability density function `P` evaluated via Kullback-Leibler divergence `D_KL(P || Q_m)` against reference MST distributions.
5. **Optical Cosmetic Rendering & Harmony Engine:** Computes perceptual product-to-skin matches via CIEDE2000 (`ΔE₀₀`) and renders virtual cosmetics using Kubelka-Munk two-layer reflectance (`R_{S+F}`) and canonical UV alpha-compositing to prevent chalky particulate scattering and preserve pore micro-geometry.

---

## 3. Development Roadmap

### Milestone 1: Diagnostic Colorimetry & Optical Cosmetic Try-On (Current Focus)
- [ ] **Sub-module 1A (ROI & Illumination Pipeline):** Implement face landmarking/segmentation mask extraction, specular outlier rejection, and illumination quality validation.
- [ ] **Sub-module 1B (Shade & Undertone Classifier):** Build the `sRGB → CIEXYZ → CIELAB` transformation engine to output continuous `(L*, a*, b*)`, Hue Angle `h*`, and probabilistic MST 1–10 classification (separating red/cool-warm `h* ≤ 55°` from golden-yellow `h* > 55°` undertones).
- [ ] **Sub-module 1C (Shade Matcher & Color Harmony Recommender):** Implement vectorized CIEDE2000 (`ΔE₀₀`) distance matching for foundation/concealer SKUs and contrast-ratio color harmony rules for lip, blush, and eyeshadow palettes on MST 7–10.
- [ ] **Sub-module 1D (Physics-Grounded Swatch & Lip/Cheek Shader):** Implement a Kubelka-Munk reflectance shader (`r_F`, `t_F` optical parameters) for ash-free virtual cosmetic overlay.

### Milestone 2: Spatio-Temporal Generative Makeup Transfer
- [ ] Integrate region-specific diffusion strength control (`w_c`, `T_c`) and adaptive `ΔMonk` preservation with Depth/Canny ControlNet geometric conditioning.

### Milestone 3: 3D Geometric Piercing & Textured Hair Adornment
- [ ] Implement reflectance-corrected 3D facial/auricular landmark tracking and sub-pixel alpha matting for 3D jewelry and protective hairstyle synthesis.

---

## 4. Repository Structure

```text
melanin-ar-engine/
├── src/
│   ├── preprocessing/    # Illumination gate, semantic skin/lip/eye masks, Gaussian filtering
│   ├── colorimetry/      # sRGB -> CIELAB, (L*, h*) extraction, KL-divergence MST classifier
│   ├── recommender/      # CIEDE2000 foundation matcher & undertone color harmony engine
│   └── rendering/        # Kubelka-Munk optical shader & alpha-compositing VTO overlay
├── tests/                # Unit tests with synthetic colorimetric fixtures (zero PII/biometrics)
├── configs/              # Reference CIE illuminants, MST distribution priors, SKU palettes
└── README.md
```

