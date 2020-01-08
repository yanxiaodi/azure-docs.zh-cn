---
title: include 文件
description: include 文件
services: virtual-machines-windows, virtual-machines-linux
author: laurenhughes
ms.service: multiple
ms.topic: include
ms.date: 08/15/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: a4746a945f1a89c34308a3bd968f6341e0e25ac5
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69541430"
---
本部分提供了有关先前几代虚拟机大小的信息。 这些大小仍受支持，但无法接收更多的容量。 有更新的或备用的大小，这些大小已公开发布。 请参阅 [Azure 中 Windows 虚拟机的大小](../articles/virtual-machines/windows/sizes.md)或 [Azure 中 Linux 虚拟机的大小](../articles/virtual-machines/linux/sizes.md)，以便选择最适合自己需求的 VM 大小。  

若要详细了解如何重设 Linux VM 大小，请参阅[使用 Azure CLI 重设 Linux 虚拟机大小](../articles/virtual-machines/linux/change-vm-size.md)。 如果你使用 Windows VM，且首选使用 PowerShell，请参阅[重设 Windows VM 大小](../articles/virtual-machines/windows/resize-vm.md)。  

<br>

### <a name="basic-a"></a>基本 A  

**更新的大小建议**：[Av2 系列](../articles/virtual-machines/windows/sizes-general.md#av2-series)

高级存储：不受支持

高级存储缓存：不受支持

基本层大小主要用于开发工作负荷，以及其他不需要负载均衡、自动缩放或内存密集型虚拟机的应用程序。

|大小 – 大小\名称 | vCPU |内存|NIC 数（最大值）|最大临时磁盘大小 |最大 数据磁盘（每个 1023 GB）|最大 IOPS（每个磁盘 300 次）|
|---|---|---|---|---|---|---|
|A0\Basic_A0|1|768 MB|2| 20 GB|1|1x300|
|A1\Basic_A1|1|1.75 GB|2| 40 GB |2|2x300|
|A2\Basic_A2|2|3.5 GB|2| 60 GB|4|4x300|
|A3\Basic_A3|4|7 GB|2| 120 GB |8|8x300|
|A4\Basic_A4|8|14 GB|2| 240 GB |16|16x300|

<br>

### <a name="standard-a0---a4-using-cli-and-powershell"></a>使用 CLI 和 PowerShell 的标准 A0 - A4

在经典部署模型中，CLI 和 PowerShell 中的一些 VM 大小名称略有不同：

* Standard_A0 是特小型
* Standard_A1 是小型
* Standard_A2 是中型
* Standard_A3 是大型
* Standard_A4 是超大型

### <a name="a-series"></a>A 系列  

**更新的大小建议**：[Av2 系列](../articles/virtual-machines/windows/sizes-general.md#av2-series)

ACU：50-100

高级存储：不受支持

高级存储缓存：不受支持

| Size | vCPU | 内存：GiB | 临时存储 (HDD)：GiB | 最大数据磁盘数 | 数据磁盘最大吞吐量：IOPS | 最大 NIC 数/预期网络带宽 (Mbps)  |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0&nbsp;<sup>1</sup> |1 |0.768 |20 |1 |1x500 |2 / 100 |
| Standard_A1 |1 |1.75 |70 |2 |2x500 |2 / 500  |
| Standard_A2 |2 |3.5 |135 |4 |4x500 |2 / 500 |
| Standard_A3 |4 |7 |285 |8 |8x500 |2 / 1000 |
| Standard_A4 |8 |14 |605 |16 |16x500 |4 / 2000 |
| Standard_A5 |2 |14 |135 |4 |4x500 |2 / 500 |
| Standard_A6 |4 |28 |285 |8 |8x500 |2 / 1000 |
| Standard_A7 |8 |56 |605 |16 |16x500 |4 / 2000 |

<sup>1</sup> A0 大小在物理硬件上过度订阅。 仅针对此特定大小，其他客户部署可能影响正在运行的工作负荷的性能。 以下概述的相对性能为预期的基准，受限于近似变化性的 15%。

<br>

### <a name="a-series---compute-intensive-instances"></a>A 系列 - 计算密集型实例  

**更新的大小建议**：[Av2 系列](../articles/virtual-machines/windows/sizes-general.md#av2-series)

ACU：225

高级存储：不受支持

高级存储缓存：不受支持

A8-A11 和 H 系列大小也称为“计算密集型实例”。 运行这些大小的硬件专为计算密集型和网络密集型应用程序而设计和优化，包括高性能计算 (HPC) 群集应用程序、建模和模拟。 A8-A11 系列使用 Intel Xeon E5-2670 @ 2.6 GHZ，H 系列使用 Intel Xeon E5-2667 v3 @ 3.2 GHz。  

| Size | vCPU | 内存：GiB | 临时存储 (HDD)：GiB | 最大数据磁盘数 | 数据磁盘最大吞吐量：IOPS | 最大 NIC 数|
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8&nbsp;<sup>1</sup> |8 |56 |382 |32 |32x500 |2 |
| Standard_A9&nbsp;<sup>1</sup> |16 |112 |382 |64 |64x500 |4 |
| Standard_A10 |8 |56 |382 |32 |32x500 |2  |
| Standard_A11 |16 |112 |382 |64 |64x500 |4 |

<sup>1</sup>对于 MPI 应用程序来说，专用 RDMA 后端网络是通过 FDR InfiniBand 网络启用的，后者可以提供相当低的延迟和高带宽。  

<br>

### <a name="d-series"></a>D 系列  

**更新的大小建议**：[Dv3 系列](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU：160-250 <sup>1</sup>

高级存储：不受支持

高级存储缓存：不受支持

| Size         | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大临时存储吞吐量：IOPS/读取 Mbps/写入 Mbps | 最大的数据磁盘/吞吐量：IOPS | 最大 NIC 数/预期网络带宽 (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D1  | 1         | 3.5         | 50             | 3000/46/23                                           | 4/4x500                         | 2 / 500                 |
| Standard_D2  | 2         | 7           | 100            | 6000/93/46                                           | 8/8x500                         | 2 / 1000                     |
| Standard_D3  | 4         | 14          | 200            | 12000/187/93                                         | 16/16x500                         | 4 / 2000                     |
| Standard_D4  | 8         | 28          | 400            | 24000/375/187                                        | 32/32x500                       | 8 / 4000                     |

<sup>1</sup> VM 系列可以在下述 CPU 之一上运行：2.2 GHz Intel Xeon® E5-2660 v2、2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) 或 2.3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="d-series---memory-optimized"></a>D 系列 - 内存优化  

**更新的大小建议**：[Dv3 系列](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU：160-250 <sup>1</sup>

高级存储：不受支持

高级存储缓存：不受支持

| Size         | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大临时存储吞吐量：IOPS/读取 Mbps/写入 Mbps | 最大的数据磁盘/吞吐量：IOPS | 最大 NIC 数/预期网络带宽 (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11 | 2         | 14          | 100            | 6000/93/46                                           | 8/8x500                         | 2 / 1000                     |
| Standard_D12 | 4         | 28          | 200            | 12000/187/93                                         | 16/16x500                         | 4 / 2000                     |
| Standard_D13 | 8         | 56          | 400            | 24000/375/187                                        | 32/32x500                       | 8 / 4000                     |
| Standard_D14 | 16        | 112         | 800            | 48000/750/375                                        | 64/64x500                       | 8 / 8000                |

<sup>1</sup> VM 系列可以在下述 CPU 之一上运行：2.2 GHz Intel Xeon® E5-2660 v2、2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) 或 2.3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="ds-series"></a>DS 系列  

**更新的大小建议**：[DSv3 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU：160-250 <sup>1</sup>

高级存储：支持

高级存储缓存：支持

| Size | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大缓存吞吐量和临时存储吞吐量：IOPS/Mbps（以 GiB 为单位的缓存大小） | 最大非缓存磁盘吞吐量：IOPS/Mbps | 最大 NIC 数/预期网络带宽 (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1 |1 |3.5 |7 |4 |4,000 / 32 (43) |3,200 / 32 |2 / 500 |
| Standard_DS2 |2 |7 |14 |8 |8,000 / 64 (86) |6,400 / 64 |2 / 1000 |
| Standard_DS3 |4 |14 |28 |16 |16,000 / 128 (172) |12,800 / 128 |4 / 2000 |
| Standard_DS4 |8 |28 |56 |32 |32,000 / 256 (344) |25,600 / 256 |8 / 4000 |

<sup>1</sup> VM 系列可以在下述 CPU 之一上运行：2.2 GHz Intel Xeon® E5-2660 v2、2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) 或 2.3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="ds-series---memory-optimized"></a>DS 系列 - 内存优化  

**更新的大小建议**：[DSv3 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU：160-250 <sup>1,2</sup>

高级存储：支持

高级存储缓存：支持

| Size | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大缓存吞吐量和临时存储吞吐量：IOPS/Mbps（以 GiB 为单位的缓存大小） | 最大非缓存磁盘吞吐量：IOPS/Mbps | 最大 NIC 数/预期网络带宽 (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11 |2 |14 |28 |8 |8,000 / 64 (72) |6,400 / 64 |2 / 1000 |
| Standard_DS12 |4 |28 |56 |16 |16,000 / 128 (144) |12,800 / 128 |4 / 2000 |
| Standard_DS13 |8 |56 |112 |32 |32,000 / 256 (288) |25,600 / 256 |8 / 4000 |
| Standard_DS14 |16 |112 |224 |64 |64,000 / 512 (576) |51,200 / 512 |8 / 8000 |

<sup>1</sup> DS 系列 VM 可能的最大磁盘吞吐量（IOPS 或 MBps）可能受限于附加磁盘的数量、大小和条带化。  有关详细信息，请参阅[为实现高性能而设计](../articles/virtual-machines/windows/premium-storage-performance.md)。   
<sup>2</sup> VM 系列可以在下述 CPU 之一上运行：2.2 GHz Intel Xeon® E5-2660 v2、2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) 或 2.3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="ls-series"></a>Ls 系列

Ls-系列提供多达 32 个 vCPU，使用的是 [Intel® Xeon® 处理器 E5 v3 系列](https://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html)。 Ls 系列与 G/GS-系列拥有相同的 CPU 性能，并且每个 vCPU 附带 8 GiB 内存。

Ls 系列不支持创建本地缓存以增加持久数据磁盘可实现的 IOPS。 本地磁盘的高吞吐量和 IOPS 使 Ls 系列 Vm 非常适合于 NoSQL 存储, 例如 Apache Cassandra 和 MongoDB, 可跨多个 Vm 复制数据, 从而在单个 VM 发生故障时保持持久性。

ACU：180-240

高级存储：支持

高级存储缓存：不受支持
 
| Size          | vCPU | 内存 (GiB) | 临时存储 (GiB) | 最大数据磁盘数 | 临时存储的最大吞吐量 (IOPS / MBps) | 非缓存磁盘的最大吞吐量 (IOPS / MBps) | 最大 NIC 数/预期网络带宽 (Mbps) | 
|----------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4  | 32  | 678   | 16 | 20000 / 200 | 5000 / 125  | 2 / 4000  | 
| Standard_L8s   | 8  | 64  | 1388 | 32 | 40000 / 400 | 10000 / 250 | 4 / 8000  | 
| Standard_L16s  | 16 | 128 | 2807 | 64 | 80000 / 800 | 20000 / 500 | 8 / 16000 | 
| Standard_L32s&nbsp;<sup>1</sup> | 32   | 256  | 5630 | 64   | 160000/1600   | 40000 / 1000     | 8 / 20000 | 

Ls 系列 VM 可能的最大磁盘吞吐量可能受限于任何附加磁盘的数量、大小和条带化。 有关详细信息，请参阅[为实现高性能而设计](../articles/virtual-machines/windows/premium-storage-performance.md)。

<sup>1</sup> 实例对于专用于单个客户的硬件独立。

### <a name="gs-series"></a>GS 系列 

ACU：180 - 240 <sup>1</sup>

高级存储：支持

高级存储缓存：支持

| Size | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大缓存吞吐量和临时存储吞吐量：IOPS/Mbps（以 GiB 为单位的缓存大小） | 最大非缓存磁盘吞吐量：IOPS/Mbps | 最大 NIC 数/预期网络带宽 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_GS1 |2 |28 |56 |8 |10,000 / 100 (264) |5,000 / 125 |2 / 2000 |
| Standard_GS2 |4 |56 |112 |16 |20,000 / 200 (528) |10,000 / 250 |2 / 4000 |
| Standard_GS3 |8 |112 |224 |32 |40,000 / 400 (1,056) |20,000 / 500 |4 / 8000 |
| Standard_GS4&nbsp;<sup>3</sup> |16 |224 |448 |64 |80,000 / 800 (2,112) |40,000 / 1,000 |8 / 16000 |
| Standard_GS5&nbsp;<sup>2,&nbsp;3</sup> |32 |448 |896 |64 |160,000 / 1,600 (4,224) |80,000 / 2,000 |8 / 20000 |

<sup>1</sup> GS 系列 VM 可能的最大磁盘吞吐量（IOPS 或 MBps）可能受限于附加磁盘的数量、大小和条带化。 有关详细信息，请参阅[为实现高性能而设计](../articles/virtual-machines/windows/premium-storage-performance.md)。

<sup>2</sup> 实例对于专用于单个客户的硬件独立。

<sup>3</sup> 受约束的可用核心大小。

<br>

### <a name="g-series"></a>G 系列

ACU：180 - 240

高级存储：不受支持

高级存储缓存：不受支持

| Size         | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大临时存储吞吐量：IOPS/读取 Mbps/写入 Mbps | 最大的数据磁盘/吞吐量：IOPS | 最大 NIC 数/预期网络带宽 (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_G1  | 2         | 28          | 384            | 6000/93/46                                           | 8/8 x 500                       | 2 / 2000                     |
| Standard_G2  | 4         | 56          | 768            | 12000/187/93                                         | 16/16 x 500                       | 2 / 4000                     |
| Standard_G3  | 8         | 112         | 1,536          | 24000/375/187                                        | 32/32 x 500                     | 4 / 8000                |
| Standard_G4  | 16        | 224         | 3,072          | 48000/750/375                                        | 64/64 x 500                     | 8 / 16000          |
| Standard_G5&nbsp;<sup>1</sup> | 32        | 448         | 6,144          | 96000 / 1500 / 750                                       | 64/64 x 500                     | 8 / 20000           |

<sup>1</sup> 实例对于专用于单个客户的硬件独立。
<br>
