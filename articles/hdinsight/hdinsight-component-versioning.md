---
title: Apache Hadoop bileşenleri ve sürümleri-Azure HDInsight
description: Azure HDInsight 'ta Apache Hadoop bileşenleri ve sürümleri hakkında bilgi edinin.
ms.service: hdinsight
ms.topic: conceptual
author: deshriva
ms.author: deshriva
ms.date: 02/08/2021
ms.openlocfilehash: dbd5b507fd4a7b2434158dbdc80584a7fd348732
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105726592"
---
# <a name="azure-hdinsight-versions"></a>Azure HDInsight sürümleri

HDInsight, ortam bileşenlerini ve HDInsight platformunu bir kümeye dağıtılan bir pakete Apache Hadoop. Daha fazla ayrıntı için bkz. [HDInsight sürümü oluşturma nasıl çalıştığını](hdinsight-overview-versioning.md)öğrenin.

## <a name="supported-hdinsight-versions"></a>Desteklenen HDInsight sürümleri

Bu tabloda, Azure portal ve PowerShell, CLı ve .NET SDK gibi diğer dağıtım yöntemlerinde bulunan HDInsight sürümleri listelenir.

| HDInsight sürümü | VM İŞLETİM SİSTEMİ | Sürüm tarihi| Destek türü | Destek sona erme tarihi | Emeklilik tarihi | Yüksek kullanılabilirlik |
| --- | --- | --- | --- | --- | --- | ---|
| [HDInsight 4.0](hdinsight-40-component-versioning.md) |Ubuntu 16.0.4 LTS |Eylül 24, 2018 | [Standart](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) | | |Yes |
| [HDInsight 3,6](hdinsight-36-component-versioning.md) |Ubuntu 16.0.4 LTS |4 Nisan 2017      | [Temel](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) | Standart destek süre sonu-30 Haziran 2021 <br> Temel destek süre sonu-3 Nisan 2022 |4 Nisan 2022 |Yes |

* 1 Temmuz 2021 tarihinden itibaren, Microsoft belirli HDI 3,6 küme türleri için Temel destek sunacaktır. Bkz. [hdınsight 3,6 bileşen sürümleri](hdinsight-36-component-versioning.md).

## <a name="release-notes"></a>Sürüm notları

HDInsight 'ın en son sürümlerinde ek sürüm notları için bkz. [HDInsight sürüm notları](hdinsight-release-notes.md).

## <a name="support-options-for-hdinsight-versions"></a>HDInsight sürümleri için destek seçenekleri

Destek, HDInsight sürümünün Microsoft Müşteri Hizmetleri ve desteği tarafından desteklendiği bir zaman dilimi olarak tanımlanır. HDInsight iki tür destek sunar: 
- **Standart destek** , Microsoft 'un HDInsight kümelerinde güncelleştirmeler ve destek sağladığı bir zaman dönedir.  
    En son tamamen desteklenen sürümü kullanarak çözüm oluşturmanızı öneririz. 
- **Temel destek** , Microsoft 'un HDInsight kaynak sağlayıcısı 'na sınırlı bakım sağlayabileceği bir zaman dönecektir. HDInsight görüntüleri ve açık kaynak yazılım (OSS) bileşenlerine bakım uygulanmaz.   HDInsight kümelerinde yalnızca kritik güvenlik düzeltmeleri düzeltme eki uygulanır.  
  Microsoft, yeni kümeler oluşturmayı veya bir sürüm Temel destek olduğunda hiçbir yeni çözüm oluşturmayı teşvik etmez. Mevcut kümelerin en son tam olarak desteklenen sürüme geçirilmesini öneririz. 

**Destek sona erme tarihi** , Microsoft 'un belirli HDInsight sürümü için destek sunmayacağı anlamına gelir. Ayrıca, küme oluşturma için Azure portal ile artık kullanılamaz.

**Kullanımdan** kaldırma, HDInsight sürümünün mevcut kümelerinin olduğu gibi çalışmaya devam etmesi anlamına gelir. Bu sürümdeki yeni kümeler CLı ve SDK 'Ları içeren herhangi bir yöntemle oluşturulamaz. El ile ölçekleme ve otomatik ölçeklendirme gibi diğer denetim düzlemi özelliklerinin, kullanımdan kaldırma tarihinden sonra çalışmasına garanti edilmez. Kullanımdan kaldırılan sürümler için destek kullanılamaz.

## <a name="versioning-considerations"></a>Sürüm oluşturma konuları
- Bir küme bir görüntüyle dağıtıldıktan sonra, bu küme otomatik olarak yeni görüntü sürümüne yükseltilmez. Yeni kümeler oluştururken en son görüntü sürümü dağıtılır.
- Müşteriler, yeni HDInsight sürümü kullanılırken uygulamaların düzgün şekilde çalışacağını test etmelidir ve doğrular.
- HDInsight, önceki bildirimde bulunmadan varsayılan sürümü değiştirme hakkını saklı tutar. Bir sürüm bağımlılığıdır varsa, kümelerinizi oluştururken HDInsight sürümünü belirtin.
- HDInsight, HDInsight sürümünü devre dışı bırakmadan önce bir OSS bileşen sürümünü devre dışı bırakabilir.

## <a name="next-steps"></a>Sonraki adımlar

- [HDInsight üzerinde Apache Hadoop, Spark ve daha fazlası için küme kurulumu](hdinsight-hadoop-provision-linux-clusters.md)
- [Kurumsal Güvenlik Paketi](./enterprise-security-package.md)
- [Windows BILGISAYARDAN HDInsight üzerinde Apache Hadoop çalışma](hdinsight-hadoop-windows-tools.md)
