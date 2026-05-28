# Satellite QPE data sources

最后更新：2026-05-28

## 卫星观测输入

### GEO IR / VIS / WV

常见传感器与产品：

- GOES-R ABI：多通道亮温、可见光/红外/水汽通道；适合高频近实时估计。
- GOES-R GLM：闪电观测，可辅助强对流降水检测。
- Meteosat SEVIRI：欧洲/非洲区域高频热红外观测，Rain over Africa 使用这一类输入。
- FY-4A/FY-4B AGRI：东亚和西太平洋区域高频 GEO 观测，适合中国及周边 QPE。
- GK2A AMI：韩国 GEO 卫星，常用于东亚高分辨率降水估计。

特点：

- 优点：时效高、覆盖广、适合近实时 QPE。
- 难点：IR 主要看云顶，无法直接观测近地面降水；暖云降水、地形降水和极端降水容易出现偏差。

### PMW / GPM constellation

常见输入或标签：

- GPM Microwave Imager (GMI)
- GPM Dual-frequency Precipitation Radar (DPR)
- GPM Combined Radar-Radiometer products
- constellation passive microwave sensors

特点：

- 优点：与降水物理关系更直接，常用作训练标签或高质量参考。
- 难点：过境频率低，单星难以满足高时效连续监测。

## 卫星降水产品

### IMERG

IMERG 是近三年 DL-QPE 论文最常见的参考产品之一。它融合多星被动微波、GEO IR 和雨量站校正信息，常用于训练目标、对照产品或被校正对象。

### PERSIANN family

包括 PERSIANN-CDR、PDIR-Now、PERSIANN-CCS、PERSIANN-U-Net/PUnet 等。PERSIANN 系列长期围绕 IR 卫星降水估计展开，是深度学习 QPE 的重要谱系。

### TRMM / TMPA

TRMM 3B42 V7 在一些区域融合、偏差校正和历史研究中仍被使用，常与 PERSIANN-CDR、ERA5 或雨量站数据组合。

### CMORPH / GSMaP / CHIRPS / SM2RAIN

这些产品常用于多源融合、对比评估或区域 bias correction：

- CMORPH：常作为卫星降水估计或高分辨率降水产品参考。
- GSMaP：亚洲和复杂地形区域常见。
- CHIRPS：IR + station，适合长期气候和陆地区域研究。
- SM2RAIN：从土壤湿度反推降水，在无雨量站融合研究中可提供互补信息。

## 地面参考与评估

常见参考数据：

- 雨量站网络：区域偏差校正和水文验证常用。
- MRMS / Stage IV：美国区域高分辨率 radar-gauge 参考。
- GPM DPR/GMI combined：常作为 PMW/IR retrieval 的训练标签。
- ERA5 / ERA5-Land：常作为辅助变量或对照，不应被直接等同于观测真值。

## 常见评估指标

- 连续指标：RMSE、MAE、Bias、Correlation、KGE、NSE。
- 分类指标：POD、FAR、CSI、HSS、ETS。
- 分布与极端：分雨强指标、分位数误差、Rx1day、R95p/R99p、heavy rainfall threshold。
- 概率评估：CRPS、分位数覆盖率、可靠性曲线、超过阈值概率。

