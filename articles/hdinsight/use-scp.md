---
title: Azure HDInsight 'ta Apache Hadoop ile SCP kullanma
description: Bu belge, SSH ve SCP komutlarını kullanarak HDInsight 'a bağlanma hakkında bilgi sağlar.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: 927b8c55008c3e01d8ff1dd09c46cfa3c6618026
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946279"
---
# <a name="use-scp-with-apache-hadoop-in-azure-hdinsight"></a>Azure HDInsight 'ta Apache Hadoop ile SCP kullanma

Bu makalede, HDInsight kümenize dosyaları güvenle aktarma hakkında bilgi sağlanır.

## <a name="copy-files"></a>Dosyaları kopyalama

`scp` yardımcı programı, kümedeki bireysel düğümlerde gelen ve giden dosyaları kopyalamak için kullanılabilir. Örneğin, aşağıdaki komut `test.txt` dizinini yerel sistemden birincil baş düğüme kopyalar.

```bash
scp test.txt sshuser@clustername-ssh.azurehdinsight.net:
```

`:` sonrasında yol sonra belirtilmezse dosya `sshuser` giriş dizinine yerleştirilir.

Aşağıdaki örnekte birincil baş düğümdeki `sshuser` giriş dizininden `test.txt` dosyası yerel sisteme kopyalanmaktadır:

```bash
scp sshuser@clustername-ssh.azurehdinsight.net:test.txt .
```

`scp`, yalnızca küme içindeki tek düğümlerin dosya sistemine erişebilir. Bu, kümenin uyumlu depolama alanındaki verilere erişmek için kullanılamaz.

Bir kaynağı SSH oturumundan kullanmak için karşıya yüklemeniz gerektiğinde `scp` kullanın. Örneğin, bir Python betiğini karşıya yükleyin ve bir SSH oturumundan çalıştırın.

Verileri HDFS uyumlu depolama alanına doğrudan yükleme hakkında daha fazla bilgi için aşağıdaki belgelere bakın:

* [Azure Depolama kullanarak HDInsight](hdinsight-hadoop-use-blob-storage.md)
* [Azure Data Lake Storage 1. kullanan HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md).

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight ile SSH kullanma](./hdinsight-hadoop-linux-use-ssh-unix.md)
* [HDInsight’ta kenar düğümlerini kullanma](hdinsight-apps-use-edge-node.md#access-an-edge-node)
