# 🔬 PerovskiteLab - Autonomous ML-Driven Synthesis Platform

[![Live Demo](https://img.shields.io/badge/Demo-Live-green)](https://burhanbeycan.github.io/perovskite-lab/)
[![Status](https://img.shields.io/badge/Status-Active-brightgreen)](https://github.com/burhanbeycan/perovskite-lab)
[![License](https://img.shields.io/badge/License-MIT-blue)](LICENSE)

> **Real-time monitoring of an autonomous perovskite solar cell optimization campaign using robotics, machine learning, and high-throughput characterization.**

![Dashboard Preview](docs/preview.png)

## 🚀 Live Demo

**Visit the live demo**: [https://burhanbeycan.github.io/perovskite-lab/](https://burhanbeycan.github.io/perovskite-lab/)

---

## 🎯 Project Overview

PerovskiteLab is a fully autonomous laboratory system for optimizing perovskite solar cell materials through:

- **🤖 Robotic Synthesis**: Automated solution preparation, spin coating, and annealing
- **🔬 Tier 1 Characterization**: Fast screening with PerovVision, PerImP, and Conductivity modules
- **🧠 ML Optimization**: Multi-objective Bayesian optimization with EHVI acquisition function
- **📊 Real-time Monitoring**: Live web interface showing ongoing experiments and results

---

## 📈 Current Campaign Status

| Metric | Value |
|--------|-------|
| **Total Experiments** | 147 / 200 |
| **Campaign Progress** | 73.5% |
| **Active Experiments** | 3 synthesis, 2 characterization |
| **Average Quality Score** | 0.782 |
| **Tier 2 Candidates** | 42 (28.6%) |
| **Pareto Frontier Size** | 12 points |

*Last updated: October 4, 2025*

---

## 🚀 Live Demo

**Visit the live demo**: [https://burhanbeycan.github.io/perovskite-lab/](https://burhanbeycan.github.io/perovskite-lab/)

### Features

- 📊 **Dashboard**: Real-time metrics, quality progression, Pareto frontier
- 📈 **Project Status**: Campaign overview, system status, activity timeline
- 🧪 **Experiments**: Browse all 147 experiments with detailed results
- 🤖 **ML Optimization**: Bayesian optimization progress and predictions

---

## 🛠️ Technology Stack

### Hardware
- **Robot**: Universal Robots UR5e (6-DOF collaborative robot)
- **Glovebox**: Inert Technology PureLab HE (O₂ < 1 ppm, H₂O < 1 ppm)
- **Characterization**: Custom Tier 1 modules
  - PerovVision: Computer vision for film quality
  - PerImP: Photoluminescence spectroscopy
  - Conductivity: 4-point probe electrical measurement
- **Total Cost**: ~$332,900 (full system)

### Software
- **Frontend**: Standalone HTML + CSS + JavaScript (Chart.js)
- **Backend**: Python (robot control, ML)
- **Database**: PostgreSQL
- **ML Framework**: PyTorch + BoTorch (Bayesian optimization)
- **Real-time**: WebSocket (live data streaming)

---

## 📊 Results & Progress

### Optimization Progress

The campaign has completed **25 iterations** of Bayesian optimization:
- **Best Quality Score**: 0.892 (EXP_132)
- **Best Crystallinity**: 0.915 (EXP_138)
- **Pareto Frontier**: 12 optimal points
- **Convergence**: Estimated 25 more iterations

### Recent Experiments

| ID | Composition | Quality | Crystallinity | Status |
|----|-------------|---------|---------------|--------|
| EXP_147 | FA:0.85 Cs:0.15 | 0.834 | - | Running 🔄 |
| EXP_146 | FA:0.78 Cs:0.22 | 0.812 | - | Characterization ⚡ |
| EXP_145 | FA:0.91 Cs:0.09 | 0.889 | 0.912 | Completed ✓ |
| EXP_144 | FA:0.82 Cs:0.18 | 0.756 | 0.823 | Completed ✓ |
| EXP_143 | FA:0.88 Cs:0.12 | 0.801 | 0.845 | Completed ✓ |

---

## 🔬 Scientific Background

This project is inspired by the work of EPFL PV-Lab on automated perovskite research:

> **"Blueprints for Accelerating Perovskite Research: Automation and Optimization"**  
> Can, H.A., Jacobs, D.A., Weiß, C.M., and Ballif, C.  
> *Digital Discovery*, 2023, DOI: 10.1039/d3dd00070b

### Key Innovations

1. **Hierarchical Characterization**: 3-tier system (fast → detailed → long-term)
   - **Tier 1** (~2 min): PerovVision, PerImP, Conductivity (ALL samples)
   - **Tier 2** (~20 min): XRD, UV-Vis, Profilometry (high-quality samples)
   - **Tier 3** (days): Stability testing, device performance

2. **Multi-objective Optimization**: EHVI acquisition with hybrid query strategy
   - 60% Exploitation (best known regions)
   - 30% Exploration (uncertain regions)
   - 10% Diversity (undersampled regions)

3. **Real-time Web Interface**: Live monitoring and data visualization

---

## 🚀 Deployment Options

### Option 1: GitHub Pages (Recommended)

This is the **simplest** option - just one HTML file!

1. **Create a new repository** on GitHub: `perovskite-lab`

2. **Upload the file**:
   ```bash
   git init
   git add index.html
   git commit -m "Initial commit: PerovskiteLab"
   git remote add origin https://github.com/YOUR_USERNAME/perovskite-lab.git
   git push -u origin main
   ```

3. **Enable GitHub Pages**:
   - Go to repository **Settings** → **Pages**
   - Source: **Deploy from a branch**
   - Branch: **main** / **root**
   - Save

4. **Access your site**:
   ```
   https://YOUR_USERNAME.github.io/perovskite-lab/
   ```

### Option 2: Custom Domain

If you have a custom domain:

1. Add a `CNAME` file:
   ```
   perovskite.yourdomain.com
   ```

2. Configure DNS:
   - Add CNAME record pointing to `YOUR_USERNAME.github.io`

3. Enable HTTPS in GitHub Pages settings

---

## 📁 File Structure

```
perovskite-lab/
├── index.html          # Standalone web application (all-in-one)
├── README.md           # This file
├── LICENSE             # MIT License
└── docs/
    ├── preview.png     # Dashboard screenshot
    └── documentation/  # Technical documentation
```

---

## 🎨 Features

### Dashboard
- **Campaign Progress**: 147/200 experiments (73.5%)
- **Active Experiments**: Real-time status (3 synthesis, 2 characterization)
- **Average Quality**: 0.782 with trend visualization
- **Tier 2 Candidates**: 42 high-quality samples (28.6%)
- **Quality Progression Chart**: Last 30 experiments
- **Pareto Frontier**: Quality vs Crystallinity scatter plot

### Project Status
- **Overview Tab**:
  - Optimization progress chart (25 iterations)
  - Recent completed experiments (last 5)
- **System Status Tab**:
  - Robot status (idle/synthesis/measuring)
  - Characterization queues (Tier 1 & Tier 2)
  - Glovebox conditions (O₂, H₂O, temperature)
  - ML Engine status (active, 147 observations)
- **Timeline Tab**:
  - Live activity feed (8 recent updates)
  - Real-time event stream

### Experiments
- **All Experiments List**: 147 experiments with filtering
- **Status Badges**: Running, Characterization, Completed
- **Detailed Info**: Composition, annealing temperature, quality scores

### ML Optimization
- **Model Status**: Version, observations, Pareto points
- **Next Experiments**: AI-suggested experiments (exploitation, exploration, diversity)
- **Convergence Tracking**: 25/50 iterations, estimated completion
- **Optimization Chart**: Best quality and crystallinity over time

### Real-time Features
- 🟢 **Live Badge**: Pulsing green indicator
- ⏰ **Real-time Clock**: Updates every second
- 🔄 **Auto-refresh**: System status updates every 10 seconds
- 📡 **Activity Feed**: Latest lab events

---

## 🎓 Academic Use

This system is designed for perovskite solar cell research:

- **Composition Screening**: Explore FA-Cs-I-Br parameter space
- **Process Optimization**: Optimize spin coating and annealing conditions
- **ML-guided Discovery**: Accelerate material discovery with Bayesian optimization
- **High-throughput**: 10-20 samples/day with Tier 1 characterization

### Citation

If you use this work in your research, please cite:

```bibtex
@misc{perovskitelab2025,
  title={PerovskiteLab: Autonomous ML-Driven Synthesis Platform},
  author={Your Name},
  year={2025},
  url={https://github.com/YOUR_USERNAME/perovskite-lab}
}
```

---

## 📚 Documentation

### Full Documentation
- [Academic Documentation](docs/academic_documentation.md) - Comprehensive technical report
- [Hardware Requirements](docs/hardware_requirements.md) - Detailed specifications and costs
- [Tier 1 Characterization](docs/tier1_characterization_modules.md) - Module details
- [Robotic Synthesis](docs/robotic_synthesis_protocols.md) - Step-by-step protocols
- [ML Optimization](docs/iterative_optimization_loop.md) - Algorithm details

### Quick Links
- [System Diagrams](docs/diagrams/) - 8 architecture diagrams
- [Deployment Guide](DEPLOYMENT.md) - Detailed deployment instructions

---

## 🤝 Contributing

Contributions are welcome! Areas of interest:

- Additional characterization modules (Tier 2/3)
- ML algorithm improvements
- Web interface enhancements
- Documentation and tutorials

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

### Academic Use Notice

This software is primarily intended for academic and research purposes. For commercial use, please contact the authors.

---

## 🙏 Acknowledgments

- EPFL PV-Lab for inspiration and methodology
- Universal Robots for robotic platform
- Open-source community for tools and libraries

---

## 📞 Contact

- **Project Lead**: Your Name
- **Institution**: Your University/Lab
- **Email**: your.email@university.edu
- **GitHub**: [@burhanbeycan](https://github.com/burhanbeycan)

---

## 🔄 Project Status

| Component | Status |
|-----------|--------|
| Robotic Synthesis | ✅ Operational |
| Tier 1 Characterization | ✅ Operational |
| Tier 2 Characterization | 🟡 Partial |
| ML Optimization | ✅ Active |
| Web Interface | ✅ Live |
| Campaign Progress | 🟢 73.5% (147/200) |

**Last System Update**: October 4, 2025, 16:30 UTC

---

<div align="center">

### 🌟 Star this repo if you find it useful!

[⭐ Star](https://github.com/burhanbeycan/perovskite-lab) • [🐛 Report Bug](https://github.com/burhanbeycan/perovskite-lab/issues) • [💡 Request Feature](https://github.com/burhanbeycan/perovskite-lab/issues)

**Made with ❤️ for accelerating perovskite research and clean energy transition**

</div>
