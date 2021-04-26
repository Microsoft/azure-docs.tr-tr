---
title: Azure HDInsight desteklenen düğüm yapılandırması
description: HDInsight küme düğümleri için en düşük ve önerilen konfigürasyonları öğrenin.
keywords: VM boyutları, küme boyutları, küme yapılandırması
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 05/14/2020
ms.openlocfilehash: d41ee2554d30a56bc2e025bbe2c93aee143d75e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98931651"
---
# <a name="what-are-the-default-and-recommended-node-configurations-for-azure-hdinsight"></a>Azure HDInsight için varsayılan ve önerilen düğüm yapılandırması nelerdir?

Bu makalede, Azure HDInsight kümeleri için varsayılan ve önerilen düğüm yapılandırması anlatılmaktadır.

## <a name="default-and-minimum-recommended-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Kümeler için varsayılan ve önerilen en düşük düğüm yapılandırması ve sanal makine boyutları

Aşağıdaki tablolarda HDInsight kümeleri için varsayılan ve önerilen sanal makine (VM) boyutları listelenmektedir.  Bu bilgiler, HDInsight kümelerini dağıtmak üzere PowerShell veya Azure CLı betikleri oluştururken kullanılacak VM boyutlarını anlamak için gereklidir.

Bir kümede 32 ' den fazla çalışan düğümüne ihtiyacınız varsa, en az 8 çekirdeğe ve 14 GB RAM 'e sahip bir baş düğüm boyutu seçin.

Veri disklerine sahip tek küme türleri, hızlandırılmış yazma özelliği etkinleştirilmiş Kafka ve HBase kümelerdir. HDInsight bu senaryolarda P30 ve S30 disk boyutlarını destekler. Diğer tüm küme türleri için HDInsight, kümeyle yönetilen disk alanı sağlar. 11/07/2019 ' den başlayarak, yeni oluşturulan kümedeki her bir düğümün yönetilen disk boyutu 128 GB 'tır. Bu değiştirilemez.

Bu belgede kullanılan en düşük önerilen sanal makine türlerinin belirtimleri aşağıdaki tabloda özetlenmiştir.

| Boyut              | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | Maksimum geçici depolama aktarım hızı: IOPS / Okuma MB/sn / Yazma MB/sn | Maksimum veri diski/aktarım hızı: IOPS | En fazla NIC/beklenen ağ bant genişliği (Mbps) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D3_v2 | 4    | 14          | 200                    | 12000/187/93                                           | 16/16x500           | 4 / 3000                                       |
| Standard_D4_v2 | 8    | 28          | 400                    | 24000/375/187                                          | 32/32x500           | 8 / 6000                                       |
| Standard_D5_v2 | 16   | 56          | 800                    | 48000/750/375                                          | 64/64x500           | 8 / 12000                                    |
| Standard_D12_v2   | 4         | 28          | 200            | 12000/187/93                                         | 16/16x500                         | 4 / 3000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000/375/187                                        | 32/32x500                       | 8 / 6000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000/750/375                                        | 64/64x500                       | 8 / 12000          |
| Standard_A1_v2  | 1         | 2           | 10             | 1000/20/10                                           | 2/2x500               | 2 / 250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000/40/20                                           | 4/4x500               | 2 / 500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000/80/40                                           | 8/8x500               | 4 / 1000                     |

Her VM türünün belirtimleri hakkında daha fazla bilgi için aşağıdaki belgelere bakın:

* [Genel amaçlı sanal makine boyutları: Dv2 serisi 1-5](../virtual-machines/dv2-dsv2-series.md)
* [Bellek için iyileştirilmiş sanal makine boyutları: Dv2 serisi 11-15](../virtual-machines/dv2-dsv2-series-memory.md)
* [Genel amaçlı sanal makine boyutları: AV2 serisi 1-8](../virtual-machines/av2-series.md)

### <a name="all-supported-regions-except-brazil-south-and-japan-west"></a>Brezilya Güney ve Japonya Batı dışındaki tüm desteklenen bölgeler

> [!Note]
> PowerShell ve diğer betiklerdeki kullanım için SKU tanımlayıcısını almak üzere `Standard_` aşağıdaki tablolardaki tüm VM SKU 'larının başına ekleyin. Örneğin, `D12_v2` olur `Standard_D12_v2` .

| Küme türü | Hadoop | HBase | Interactive Query | Storm | Spark | ML Server | Kafka |
|---|---|---|---|---|---|---|---|
| Baş: varsayılan VM boyutu | D12_v2 | D12_v2 | D13_v2 | A4_v2 | D12_v2, <br/>D13_v2 * | D12_v2 | D3_v2 |
| Baş: önerilen en düşük VM boyutları | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2, <br/>D13_v2 * | D12_v2 | D3_v2 |
| Çalışan: varsayılan VM boyutu | D4_v2 | D4_v2 | D14_v2 | D3_v2 | D13_v2 | D4_v2 | Aracı başına 2 S30 disk ile 4 D12_v2 |
| Çalışan: en düşük önerilen VM boyutları | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 | D3_v2 |
| ZooKeeper: varsayılan VM boyutu |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 | A4_v2 |
| ZooKeeper: önerilen en düşük VM boyutları |  | A4_v2 | A4_v2 | A2_v2 |  | A2_v2 | A4_v2 |
| ML Hizmetleri: varsayılan VM boyutu |  |  |  |  |  | D4_v2 |  |
| ML Hizmetleri: en düşük önerilen VM boyutu |  |  |  |  |  | D4_v2 |  |

\* = Spark Kurumsal Güvenlik Paketi (ESP) kümeleri için VM boyutları

### <a name="brazil-south-and-japan-west-only"></a>Brezilya Güney ve Japonya Batı

| Küme türü | Hadoop | HBase | Interactive Query | Storm | Spark | ML Services |
|---|---|---|---|---|---|---|
| Baş: varsayılan VM boyutu | D12 | D12 | D13 | A4_v2 | D12 | D12 |
| Baş: önerilen en düşük VM boyutları | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2 | D12_v2 |
| Çalışan: varsayılan VM boyutu | D4 | D4 | D14 | D3 | D13 | D4 |
| Çalışan: en düşük önerilen VM boyutları | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 |
| ZooKeeper: varsayılan VM boyutu |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ZooKeeper: önerilen en düşük VM boyutları |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ML Hizmetleri: varsayılan VM boyutları |  |  |  |  |  | D4 |
| ML Hizmetleri: önerilen en düşük VM boyutları |  |  |  |  |  | D4_v2 |

> [!NOTE]
> - Baş, fırtınası kümesi türü için *Nimbus* olarak bilinir.
> - Çalışan, fırtınası kümesi türü için *Gözetmen* olarak bilinir.
> - Çalışan, HBase küme türü için *bölge* olarak bilinir.

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight ile kullanılabilen Apache Hadoop bileşenleri ve sürümleri nelerdir?](hdinsight-component-versioning.md)
