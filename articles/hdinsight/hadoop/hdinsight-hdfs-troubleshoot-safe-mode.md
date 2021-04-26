---
title: Azure HDInsight kümesinde yerel olarak güvenli modda kalmış
description: Azure HDInsight 'ta Apache kümesinde yerel Apache 'de güvenli modda sorun giderme
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.openlocfilehash: d34bf8d82aee14f5ba835f68a061555d24ee2621
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944449"
---
# <a name="scenario-local-hdfs-stuck-in-safe-mode-on-azure-hdinsight-cluster"></a>Senaryo: Azure HDInsight kümesinde yerel olarak güvenli modda kalmış

Bu makalede, Azure HDInsight kümeleriyle etkileşim kurarken sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue"></a>Sorun

Yerel Apache Hadoop Dağıtılmış Dosya Sistemi (bir) HDInsight kümesinde güvenli modda takılmış. Aşağıdakine benzer bir hata iletisi alırsınız:

```output
hdiuser@spark2:~$ hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
17/04/05 16:20:52 WARN retry.RetryInvocationHandler: Exception while invoking ClientNamenodeProtocolTranslatorPB.mkdirs over spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net/10.0.0.22:8020. Not retrying because try once and fail.
org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.hdfs.server.namenode.SafeModeException): Cannot create directory /temp. Name node is in safe mode.
It was turned on manually. Use "hdfs dfsadmin -safemode leave" to turn safe mode off.
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1359)
...
mkdir: Cannot create directory /temp. Name node is in safe mode.
```

## <a name="cause"></a>Nedeni

HDInsight kümesi, aşağıda çok az sayıda düğüme göre ölçeklendirildi veya düğüm sayısı, bir.

## <a name="resolution"></a>Çözüm

1. HDInsight kümesindeki, aşağıdaki komutla birlikte bir durum raporu:

    ```bash
    hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
    ```

1. Aşağıdaki komutla HDInsight kümesindeki TBU 'un bütünlüğünü denetleyin:

    ```bash
    hdiuser@spark2:~$ hdfs fsck -D "fs.default.name=hdfs://mycluster/" /
    ```

1. Eksik olduğunu tespit ederseniz, bozuk veya çoğaltılan bloklar altında veya bu bloklar yoksayılabilir, ad düğümünü güvenli moddan almak için aşağıdaki komutu çalıştırın:

    ```bash
    hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
    ```

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]