# OD_YOLO3D_YOLOv5

**YOLOv5 - YOLO3D track - training and test evaluation**

Part of the *Physical AI Object Detection* reference series of the **Statera Working Group**:
one algorithm and one task per repository, the same data, protocol and metrics for every algorithm -
only the algorithm differs.

## Model

- Paper: No paper - Ultralytics YOLOv5 (2020); modules from the Legacy port
- Implementation source: Legacy port of the Ultralytics YOLOv5 modules (C3, SPPF, PaFPN); size `yolov5_n`
- Parameters: 1.78 M (3 classes)

- Backbone: CSPDarknet with C3 blocks, SiLU, SPPF
- Neck: PaFPN
- Head: one 1x1 Detect convolution per scale (as in YOLOv5)
- Size n (width 0.25, depth 0.34); `VARIANT` in detectors/yolov5/build.py selects s / m / l / x

Detection layer / assignment / loss: YOLO3D layer of the YOLOv3 lineage: x, y, z, w, l, h + direct yaw regression - identical in every model of this track; YOLOv5 changes the backbone / neck only

Differences from the original (common protocol):

- Ultralytics loss and anchor-ratio target assignment are not used (shared YOLO layer)
- [Unified] bug fix of the Legacy port: the backbone was built with depthwise convolutions (`['bk_dpw']` instead of `cfg['bk_dpw']`)

## Data: KITTI_5k (LiDAR bird's-eye-view, 3 classes)

5,000 KITTI object frames with LiDAR, split into train / test by the fixed list in `data_splits/KITTI_5k/`; classes Car, Pedestrian, Cyclist; 608 x 608 BEV maps.

The KITTI data must be obtained from http://www.cvlibs.net/datasets/kitti/ under its own license.

## Results

Test set: KITTI_5k test (2,470 frames), IoU 0.5, after 10 epochs (0.8 h on an RTX 4070 Laptop GPU).

| AP50 | mean | Car | Pedestrian | Cyclist |
| --- | --- | --- | --- | --- |
| BEV | **0.2133** | 0.4136 | 0.1318 | 0.0943 |
| 3D | **0.1025** | 0.2304 | 0.0453 | 0.0318 |

Protocol: 10 epochs, Adam lr 1e-3 (constant), effective batch 8 (micro batch 2 x 4 accumulation), BEV 608, seed 777, no pretraining; point-cloud augmentation of the Legacy code.

## Usage

```bash
pip install -r requirements.txt
python data_processing_yolo3d.py   # extracts KITTI_5k (see the script header for the source zip)
python train_yolo3d_v5.py               # training, then test evaluation (runs/yolo3d_v5/)
python evaluate_yolo3d_v5.py            # test evaluation of runs/yolo3d_v5/checkpoints/ (or --weights <file>)
```

Data are expected under `$PHYSICAL_AI_ROOT/datasets` (default `~/Physical_AI/datasets`).
Smoke test: `python train_yolo3d_v5.py --epochs 1 --max-train 8 --max-test 4`.

Trained weights: the GitHub Release of this repository contains `yolo3d_v5_kitti5k_e010.pth` (the run reported above).
Put it into `runs/yolo3d_v5/checkpoints/` or pass `--weights <file>` to the evaluation script.
Demo scripts using these weights will be added in a later release.

## Files

```
data_processing_*.py      step 1  data
train_yolo3d_v5.py         step 2  training + test evaluation
evaluate_yolo3d_v5.py      step 3  test evaluation
common/                   protocol (config), data loading, trainer, metrics, utilities
tasks/                    task definition (data sets, post-processing, evaluation)
detectors/                YOLOv5 (yolov5, yolov3)
THIRD_PARTY.md            sources and licenses of the reused code
```

Licenses: see `THIRD_PARTY.md`.

## Global Collaboration

Hills Robotics is currently participating in multiple projects in Korea and internationally.

We are actively interested in building relationships with organizations that can complement our technology with global sales networks, distribution channels, system integration capabilities, manufacturing ecosystems, AI infrastructure, robotics technologies, research capabilities, or strategic investment.

Several technology and business collaborations are already underway. Some partners, projects, system architectures, and commercial programs cannot be publicly disclosed because they are subject to confidentiality and business agreements.

We believe that the emerging Physical AI ecosystem cannot be built by a single company.
It requires a global network connecting robotics companies, AI researchers, semiconductor and computing companies, sensor manufacturers, software developers, universities, system integrators, industrial customers, investors, and global distribution partners.

We have now begun our serious exploration of the Physical AI frontier, and we welcome connections with people and organizations who want to build this ecosystem together.

## Research, Contribution, and Donation

The repositories of the Statera Working Group are intended to contribute to the broader technical discussion around Physical AI.

Researchers, engineers, developers, companies, universities, and independent contributors interested in these subjects are welcome to connect with us.

If you would like to collaborate on Physical AI or autonomous robotics, participate in research or technology development, discuss international business cooperation, propose technology or platform integration, contribute technical knowledge, or support this GitHub knowledge initiative through sponsorship or donation, please contact us.

## Contact

**Richard Minsoo Go**
Head of AI, Hills Robotics
Statera Working Group

Personal / Research Contact:
[gominsoo888@gmail.com](mailto:gominsoo888@gmail.com)

Hills Robotics / Business Contact:
[minsoo.go@hillslogis.com](mailto:minsoo.go@hillslogis.com)

Hills Robotics:
https://hillslogis.com/

For inquiries regarding next-generation outdoor autonomous robotic platforms, Physical AI Spider Grid, Intelligence Cabinet, Edge AI, or On-Premise AI architectures that are not yet publicly presented on the company website, please contact us directly.
