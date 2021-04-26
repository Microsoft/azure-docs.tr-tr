---
title: Apache Kafka artış ölçeği-Azure HDInsight
description: Ölçeklenebilirliği artırmak için Azure HDInsight üzerinde Apache Kafka kümesi için yönetilen diskleri yapılandırmayı öğrenin.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/09/2019
ms.openlocfilehash: 9aa11be42aca59458fea0462a90b6aeb70df893d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863148"
---
# <a name="configure-storage-and-scalability-for-apache-kafka-on-hdinsight"></a>HDInsight üzerinde Apache Kafka için depolamayı ve ölçeklenebilirliği yapılandırma

HDInsight üzerinde [Apache Kafka](https://kafka.apache.org/) tarafından kullanılan yönetilen disk sayısını yapılandırmayı öğrenin.

HDInsight üzerinde Kafka, HDInsight kümesindeki sanal makinelerin yerel diskini kullanır. Kafka G/Ç açısından oldukça yoğun olduğundan yüksek aktarım hızı ve düğüm başına daha fazla depolama alanı sağlamak için [Azure Yönetilen Diskler](../../virtual-machines/managed-disks-overview.md) kullanılır. Kafka için geleneksel sanal sabit diskler (VHD) kullanıldıysa her düğüm 1 TB ile sınırlıdır. Yönetilen disklerle kümedeki her düğüm için 16 TB elde etmek üzere birden çok disk kullanabilirsiniz.

Aşağıdaki diyagramda, yönetilen diskli HDInsight üzerinde Kafka ile yönetilen disksiz HDInsight üzerinde Kafka karşılaştırılmaktadır:

:::image type="content" source="./media/apache-kafka-scalability/kafka-with-managed-disks-architecture.png" alt-text="yönetilen diskler mimarisiyle Kafka" border="false":::

## <a name="configure-managed-disks-azure-portal"></a>Yönetilen diskleri yapılandırma: Azure portalı

1. Portalı kullanarak küme oluşturmaya yönelik genel adımları öğrenmek için [HDInsight kümesi oluşturma](../hdinsight-hadoop-create-linux-clusters-portal.md) bölümündeki adımları uygulayın. Portal oluşturma işlemini tamammayın.

2. **Yapılandırma & fiyatlandırma** bölümünde, disk sayısını yapılandırmak Için __düğüm sayısı__ alanını kullanın.

    > [!NOTE]  
    > Yönetilen diskin türü __Standart__ (HDD) veya __Premium__ (SSD) olabilir. Premium diskler, DS ve GS serisi VM'lerle kullanılır. Diğer tüm VM türleri standart disk kullanır.

    :::image type="content" source="./media/apache-kafka-scalability/azure-portal-cluster-configuration-pricing-kafka-disks.png" alt-text="çalışan düğümü başına disk sayısı vurgulanmış şekilde küme boyutu bölümü" border="true":::

## <a name="configure-managed-disks-resource-manager-template"></a>Yönetilen diskleri yapılandırma: Resource Manager şablonu

Kafka kümesindeki çalışan düğümleri tarafından kullanılan disk sayısını denetlemek için şablonun şu bölümünü kullanın:

```json
"dataDisksGroups": [
    {
        "disksPerNode": "[variables('disksPerWorkerNode')]"
    }
    ],
```

Yönetilen disklerin nasıl yapılandırılacağını gösteren bir şablon daha bulabilirsiniz [https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json](https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json) .

## <a name="next-steps"></a>Sonraki adımlar

HDInsight üzerinde Apache Kafka ile çalışma hakkında daha fazla bilgi için aşağıdaki belgelere bakın:

* [HDInsight üzerinde Apache Kafka çoğaltmasını oluşturmak için MirrorMaker 'ı kullanma](apache-kafka-mirroring.md)
* [HDInsight üzerinde Apache Kafka ile Apache Storm kullanma](../hdinsight-apache-storm-with-kafka.md)
* [HDInsight üzerinde Apache Kafka ile Apache Spark kullanma](../hdinsight-apache-spark-with-kafka.md)
* [Azure sanal ağı üzerinden Apache Kafka bağlanma](apache-kafka-connect-vpn-gateway.md)

* [Apache Kafka ile yönetilen disklerde HDInsight blogu](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/)
