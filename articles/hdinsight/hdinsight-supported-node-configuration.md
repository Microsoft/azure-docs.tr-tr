---
title: Azure HDInsight desteklenen düğüm yapılandırması
description: HDInsight küme düğümleri için en düşük ve önerilen konfigürasyonları öğrenin.
keywords: VM boyutları, küme boyutları, küme yapılandırması
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: e482cf9b5367beba00784e69c5bad88142df5225
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70076218"
---
# <a name="what-are-the-default-and-recommended-node-configurations-for-azure-hdinsight"></a>Azure HDInsight için varsayılan ve önerilen düğüm yapılandırması nelerdir?

Bu makalede, Azure HDInsight kümeleri için varsayılan ve önerilen düğüm yapılandırması anlatılmaktadır.

## <a name="default-and-recommended-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Kümeler için varsayılan ve önerilen düğüm yapılandırması ve sanal makine boyutları

Aşağıdaki tablolarda HDInsight kümeleri için varsayılan ve önerilen sanal makine (VM) boyutları listelenmektedir.  Bu bilgiler, HDInsight kümelerini dağıtmak üzere PowerShell veya Azure CLı betikleri oluştururken kullanılacak VM boyutlarını anlamak için gereklidir. 

Bir kümede 32 ' den fazla çalışan düğümüne ihtiyacınız varsa, en az 8 çekirdeğe ve 14 GB RAM 'e sahip bir baş düğüm boyutu seçin. 

Veri disklerine sahip tek küme türleri, hızlandırılmış yazma özelliği etkinleştirilmiş Kafka ve HBase kümelerdir. HDInsight bu senaryolarda P30 ve S30 disk boyutlarını destekler.

Her VM türünün belirtimi hakkında daha fazla bilgi için aşağıdaki belgelere bakın:

* [Genel amaçlı sanal makine boyutları: Dv2 serisi 1-5](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dv2-series)
* [Bellek için iyileştirilmiş sanal makine boyutları: Dv2 serisi 11-15](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#dv2-series-11-15)
* [Genel amaçlı sanal makine boyutları: AV2 serisi 1-8](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#av2-series)

### <a name="all-supported-regions-except-brazil-south-and-japan-west"></a>Brezilya Güney ve Japonya Batı dışındaki tüm desteklenen bölgeler

> [!Note]
> PowerShell ve diğer betiklerdeki kullanım için SKU tanımlayıcısını almak üzere aşağıdaki tablolardaki tüm `Standard_` VM SKU 'larının başına ekleyin. Örneğin, `D12_v2` olur `Standard_D12_v2`.

| Küme türü | Hadoop | HBase | Interactive Query | Storm | Spark | ML Server | Kafka |
|---|---|---|---|---|---|---|---|
| Baş: varsayılan VM boyutu | D12_v2 | D12_v2 | D13_v2 | A4_v2 | D12_v2 | D12_v2 | D3_v2 |
| Baş: önerilen VM boyutları | D13_v2,<br/>D14_v2,<br/>D5_v2 | D3_v2,<br/>D4_v2,<br/>D12_v2 | D13_v2,<br/>D14_v2 | A4_v2,<br/>A8_v2 | D12_v2,<br/>D13_v2,<br/>D14_v2 | D12_v2,<br/>D13_v2,<br/>D14_v2 | D3_v2,<br/>D4_v2,<br/>D12_v2 |
| Çalışan: varsayılan VM boyutu | D4_v2 | D4_v2 | D14_v2 | D3_v2 | D13_v2 | D4_v2 | Aracı başına 2 S30 disk ile 4 D12_v2 |
| Çalışan: önerilen VM boyutları | D5_v2,<br>D12_v2,<br/>D13_v2 | D3_v2,<br/>D4_v2,<br/>D13_v2 | D13_v2,<br/>D14_v2 | D3_v2<br/>D4_v2,<br/>D12_v2 | D12_v2,<br>D13_v2,<br>D14_v2 | D4_v2,<br/>D12_v2,<br>D13_v2,<br>D14_v2 | D3_v2,<br/>D4_v2,<br/>DS3_v2,<br/>DS4_v2 |
| ZooKeeper: varsayılan VM boyutu |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 | A4_v2 |
| ZooKeeper: önerilen VM boyutları |  | A4_v2, <br/>A8_v2, <br/>A2m_v2 | A4_v2,<br/>A8_v2,<br/>A2m_v2 | A4_v2,<br/>A2_v2,<br/>A8_v2 |  | A2_v2 | A4_v2,<br/> A8_v2,<br/>A2m_v2 |
| ML Hizmetleri: varsayılan VM boyutu |  |  |  |  |  | D4_v2 |  |
| ML Hizmetleri: önerilen VM boyutu |  |  |  |  |  | D4_v2,<br/> D12_v2,<br/> D13_v2,<br/>D14_v2 |  |

### <a name="brazil-south-and-japan-west-only"></a>Brezilya Güney ve Japonya Batı

| Küme türü | Hadoop | HBase | Interactive Query | Storm | Spark | ML Services |
|---|---|---|---|---|---|---|
| Baş: varsayılan VM boyutu | D12 | D12 | D13 | A4_v2 | D12 | D12 |
| Baş: önerilen VM boyutları | D5_v2,<br/> D13_v2,<br/> D14_v2 | D3_v2,<br/> D4_v2,<br/> D12_v2 | D13_v2,<br/> D14_v2 | A4_v2,<br/> A8_v2 | D12_v2,<br/> D13_v2,<br/> D14_v2 | D12_v2,<br/> D13_v2,<br/> D14_v2 |
| Çalışan: varsayılan VM boyutu | D4 | D4 | D14 | D3 | D13 | D4 |
| Çalışan: önerilen VM boyutları | D5_v2,<br/> D12_v2,<br/> D13_v2 | D3_v2,<br/> D4_v2,<br/> D13_v2 | D13_v2,<br/> D14_v2 | D3_v2,<br/> D4_v2,<br/> D12_v2 | D12_v2,<br/> D13_v2,<br/> D14_v2 | D4_v2,<br/> D12_v2,<br/> D13_v2,<br/> D14_v2 |
| ZooKeeper: varsayılan VM boyutu |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ZooKeeper: önerilen VM boyutları |  | A4_v2,<br/> A8_v2,<br/> A2m_v2 | A4_v2,<br/> A8_v2,<br/> A2m_v2 | A4_v2,<br/> A8_v2 |  | A2_v2 |
| ML Hizmetleri: varsayılan VM boyutları |  |  |  |  |  | D4 |
| ML Hizmetleri: önerilen VM boyutları |  |  |  |  |  | D4_v2,<br/> D12_v2,<br/> D13_v2,<br/> D14_v2 |

> [!NOTE]
> - Baş, fırtınası kümesi türü için *Nimbus* olarak bilinir.
> - Çalışan, fırtınası kümesi türü için *Gözetmen* olarak bilinir.
> - Çalışan, HBase küme türü için *bölge* olarak bilinir.

## <a name="next-steps"></a>Sonraki adımlar

* [Apache Hadoop bileşenleri ve sürümleri HDInsight ile kullanılabilen nelerdir?](hdinsight-component-versioning.md)
