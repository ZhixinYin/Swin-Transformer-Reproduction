# Swin Transformer Paper Reproduction(ICCV 2021)

## Introduction
This project reproduces the original Swin Transformer architecture as described in the original paper.

## Architecture Summary
|               Stage | Layer / Block   | Configuration                                                     | Output Shape  |
| ------------------: | --------------- | ----------------------------------------------------------------- | ------------- |
|               Input | Image           | RGB Image                                                         | 224 × 224 × 3 |
| **Patch Embedding** | Conv2D          | 96 filters, 4×4 kernel, stride 4                                  | 56 × 56 × 96  |
|                     | LayerNorm       | –                                                                 | 56 × 56 × 96  |
|         **Stage 1** | Swin Block ×2   | Window-MSA / Shifted-Window-MSA<br>Heads=3, Window=7×7, MLP=384   | 56 × 56 × 96  |
|   **Patch Merging** | Downsample      | 2×2 merge, channels ×2                                            | 28 × 28 × 192 |
|         **Stage 2** | Swin Block ×2   | Window-MSA / Shifted-Window-MSA<br>Heads=6, Window=7×7, MLP=768   | 28 × 28 × 192 |
|   **Patch Merging** | Downsample      | 2×2 merge, channels ×2                                            | 14 × 14 × 384 |
|         **Stage 3** | Swin Block ×6   | Window-MSA / Shifted-Window-MSA<br>Heads=12, Window=7×7, MLP=1536 | 14 × 14 × 384 |
|   **Patch Merging** | Downsample      | 2×2 merge, channels ×2                                            | 7 × 7 × 768   |
|         **Stage 4** | Swin Block ×2   | Window-MSA / Shifted-Window-MSA<br>Heads=24, Window=7×7, MLP=3072 | 7 × 7 × 768   |
|            **Head** | Global Avg Pool | –                                                                 | 768           |
|                     | Linear          | Fully connected                                                   | #Classes      |


## Dataset
tiny-imagenet-200 is used as the dataset in this reproduction. The images are augmented aligns with the original paper (random resized crop, random horizontal flip, cutmix, mixup, erase, autoaugment and label smoothing). We only use training set in this reproduction, as the purpose of this reproduction is to validate correctness of the architecture, not claiming state-of-the-art.

## Results
The training loss for 40 epochs for Swin is below
![](image/TrainingLoss.png)


## Discussion
This work aims to produce Swin-T under limited resources. Although we only train it for 40 epochs, compared with ViT, which we trained earlier and used same dataset, loss goes down smoothly and accuracy increases steadily. This is due to Swin-T having higher inductive bias. With local and shifted window attention, the model assumes that nearby pixels matter more than distant pixels. Moreover, patch merging helps it reduce resolution and increases channels, which assumes low-level features -> mid-level -> high-level semantics. These make Swin-T CNN-like but still preserve attention mechanism. As a result, Swin-T requires less data, compared to ViT.

## References
Liu, Z., Lin, Y., Cao, Y., Hu, H., Wei, Y., Zhang, Z., Lin, S., & Guo, B. (2021).
Swin Transformer: Hierarchical vision transformer using shifted windows.
In Proceedings of the IEEE/CVF International Conference on Computer Vision (ICCV) (pp. 10012–10022).
https://doi.org/10.1109/ICCV48922.2021.00986

Tiny ImageNet Dataset
Wu, J., Zhang, J., Xie, Y., & others. (2017).
Tiny ImageNet Visual Recognition Challenge.
Stanford University.
https://tiny-imagenet.herokuapp.com/
