+++
draft = false
date = 2025-11-25T15:16:04+02:00
title = "LAB gaussian splatting"
description = ""
slug = ""
authors = []
tags = []
categories = []
externalLink = ""
series = []
+++

# My First Experience with 3D Gaussian Splatting: The Bicycle Dataset

Recently, I had the opportunity to experiment with **3D Gaussian Splatting**, a revolutionary rendering technique developed by INRIA's GraphDeco team. This technology has been making waves in the computer graphics community for its ability to produce photorealistic novel view synthesis with unprecedented speed and quality.

## What is 3D Gaussian Splatting?

3D Gaussian Splatting is a novel approach to representing and rendering 3D scenes. Unlike traditional mesh-based or neural radiance field (NeRF) methods, it represents scenes as a collection of 3D Gaussian primitives. Each Gaussian is defined by:

- **Position** in 3D space
- **Covariance** (shape and orientation)
- **Color** (spherical harmonics coefficients)
- **Opacity** (alpha value)

The key advantage? Real-time rendering speeds while maintaining high visual fidelity. Where NeRF-based methods might take seconds to render a single frame, Gaussian Splatting achieves real-time framerates.

## Setting Up the INRIA Implementation

I used the official implementation from [INRIA's GraphDeco team](https://github.com/graphdeco-inria/gaussian-splatting). The setup was relatively straightforward:

1. Clone the repository
2. Install dependencies (CUDA, PyTorch, and specific CUDA extensions)
3. Prepare the dataset
4. Train the model
5. Render and visualize results

The repository is well-documented, which made the initial setup smooth despite the complex mathematics underlying the technique.

## Hardware Setup

For this experiment, I used an **RTX 3080 with 24GB of VRAM**. This GPU proved to be more than capable for training Gaussian Splatting models:

- The 24GB of VRAM provided ample headroom for the training process
- CUDA acceleration made the optimization smooth and efficient
- No memory issues encountered during training or rendering

If you're planning to experiment with Gaussian Splatting, I'd recommend at least 12GB of VRAM, though 24GB gives you comfortable margins for larger scenes.

## Testing with the Bicycle Dataset

For my first experiment, I chose the **Bicycle dataset** - a classic benchmark in novel view synthesis. This dataset features a bicycle photographed from multiple angles, providing an excellent test case for evaluating 3D reconstruction quality.

### Training Process

The training phase involves optimizing the Gaussian parameters to match the input images. I ran the optimization for **30,000 iterations**:

- Training took approximately 30-45 minutes on the RTX 3080
- The optimization progressively refines both the geometry and appearance
- I could monitor the progress through periodic validation renders
- By 30,000 iterations, the model had converged to excellent visual quality

Once training completed, the INRIA implementation outputs a **PLY file** containing all the Gaussian primitives - their positions, colors, covariances, and opacities. This single file encapsulates the entire 3D scene representation.

### Visualization with SuperSplat

The visualization process couldn't have been easier. I simply:

1. Navigated to [SuperSplat Editor](https://supersplat.at/editor)
2. **Drag and dropped** the PLY file directly into the browser window
3. The scene loaded instantly, ready for interactive exploration

No installation, no configuration, no hassle. Just drag, drop, and explore.

![Bicycle 3D Gaussian Splatting - 30k iterations](/hci_blog/images/byc.PNG)
*The bicycle model after 30,000 iterations, visualized in SuperSplat Editor*

### Results and Observations

The results were impressive:

**Quality**: The reconstruction captured fine details of the bicycle, including spokes, reflections, and intricate geometry. The photorealistic quality of the renders was remarkable.

**Speed**: Once loaded in SuperSplat, I could navigate through the scene in real-time. The ability to freely orbit around the bicycle and inspect it from any angle with smooth framerates was truly impressive.

**Workflow simplicity**: The entire pipeline - train locally, export PLY, visualize in browser - is remarkably streamlined.

**Artifacts**: While generally excellent, I noticed occasional artifacts in areas with complex reflections or very thin structures. This is expected given the nature of the representation.

### Why SuperSplat?

[SuperSplat](https://supersplat.at/editor) has become my go-to tool for visualizing Gaussian Splatting results because:

- **Browser-based**: No installation needed, works directly in the browser
- **Drag and drop**: Just drop your PLY file and you're done
- **Interactive**: Real-time navigation and inspection
- **User-friendly**: Intuitive controls for rotation, zoom, and panning
- **Performance**: Smooth rendering even for complex scenes
- **Sharing**: Easy to share results with others who don't have technical setups

## Key Takeaways

Working with 3D Gaussian Splatting taught me several important lessons:

1. **The power of explicit representations**: Unlike implicit neural representations, the explicit nature of Gaussians allows for faster optimization and rendering.

2. **Real-time potential**: The ability to render at interactive framerates opens up numerous applications in AR/VR, gaming, and virtual production.

3. **Accessible visualization**: The combination of PLY export and web-based viewers like SuperSplat makes sharing results incredibly easy.

4. **Hardware considerations**: A modern GPU with sufficient VRAM is essential, but you don't need top-of-the-line hardware to get excellent results.

5. **Iteration count matters**: 30,000 iterations provided excellent results for the bicycle dataset, though simpler scenes might converge faster while more complex ones could benefit from additional training.

## Technical Details

For those interested in reproducing these results:

- **Dataset**: Bicycle (from the original Gaussian Splatting paper)
- **GPU**: NVIDIA RTX 3080 24GB
- **Iterations**: 30,000
- **Training time**: ~30-45 minutes
- **Output format**: PLY file
- **Visualization**: SuperSplat Editor (drag and drop)

## The Complete Workflow

Here's the end-to-end process I followed:

1. **Setup**: Install INRIA's Gaussian Splatting implementation
2. **Prepare data**: Use the bicycle dataset
3. **Train**: Run training for 30,000 iterations on RTX 3080
4. **Export**: Training automatically generates a PLY file
5. **Visualize**: Drag and drop the PLY file onto SuperSplat
6. **Explore**: Navigate the 3D scene in real-time in your browser

The entire workflow is surprisingly accessible, even for those new to novel view synthesis.

## Future Experiments

This initial experiment has opened up several directions I'd like to explore:

- Testing with custom datasets and challenging scenarios
- Experimenting with different iteration counts and optimization parameters
- Trying different GPU configurations to understand performance scaling
- Integrating Gaussian Splatting into interactive applications
- Comparing performance with other novel view synthesis methods
- Exploring compression techniques to reduce PLY file size

## Conclusion

3D Gaussian Splatting represents an exciting evolution in 3D reconstruction and rendering. The INRIA implementation is robust and accessible, making it an excellent starting point for anyone interested in exploring this technology. The bicycle dataset served as a perfect introduction, demonstrating both the strengths and current limitations of the approach.

The combination of local training with a capable GPU like the RTX 3080 and browser-based visualization with SuperSplat creates a powerful workflow. After 30,000 iterations, the results speak for themselves - photorealistic quality with real-time interaction, all accessible through a simple drag and drop.

If you're interested in novel view synthesis, photogrammetry, or real-time rendering, I highly recommend giving Gaussian Splatting a try. The technology is rapidly evolving, and we're likely just scratching the surface of its potential applications.

---

*Have you experimented with 3D Gaussian Splatting? What GPU are you using? Let me know in the comments!*

## Resources

- [INRIA Gaussian Splatting Repository](https://github.com/graphdeco-inria/gaussian-splatting)
- [SuperSplat Editor](https://supersplat.at/editor)
- [Original Paper](https://repo-sam.inria.fr/fungraph/3d-gaussian-splatting/)