---
title: HDInsight kümesini silme-Azure
description: Azure HDInsight kümesini silebilmeniz için çeşitli yollarla ilgili bilgiler
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive, devx-track-azurecli
ms.date: 11/29/2019
ms.openlocfilehash: 2160cfcd1f12f5effe7bb182eb665f85fec863af
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863403"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Tarayıcınızı, PowerShell 'i veya Azure CLı 'yı kullanarak bir HDInsight kümesini silme

HDInsight kümesi faturalandırması küme oluşturulduğunda başlar ve küme silindiğinde sona erer. Faturalandırma, dakika başına dağıtılır, bu nedenle artık kullanımda olmadığında kümenizi her zaman silmeniz gerekir. Bu belgede [Azure Portal](https://portal.azure.com), [Azure PowerShell az Module](/powershell/azure/)ve [Azure CLI](/cli/azure/)kullanarak bir kümeyi silmeyi öğreneceksiniz.

> [!IMPORTANT]  
> HDInsight kümesini silmek, kümeyle ilişkili Azure depolama hesaplarını veya Data Lake Storage silmez. Gelecekte bu hizmetlerde depolanan verileri yeniden kullanabilirsiniz.

## <a name="azure-portal"></a>Azure portalı

1. [Azure portalında](https://portal.azure.com) oturum açın.

2. Sol menüden **Tüm Service**  >  **Analytics**  >  **HDInsight kümelerine** gidin ve kümenizi seçin.

3. Varsayılan görünümden **Sil** simgesini seçin. Kümenizi silmek için istemi izleyin.

    :::image type="content" source="./media/hdinsight-delete-cluster/hdinsight-delete-cluster.png" alt-text="HDInsight küme silme düğmesi":::

## <a name="azure-powershell"></a>Azure PowerShell

`CLUSTERNAME`Aşağıdaki koddaki HDInsight kümenizin adıyla değiştirin. Bir PowerShell isteminden, kümeyi silmek için aşağıdaki komutu girin:

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>Azure CLI’si

`CLUSTERNAME`' Yi HDInsight kümenizin adıyla ve `RESOURCEGROUP` kaynak grubunuzun adı ile aşağıdaki kodda olacak şekilde değiştirin.  Bir komut isteminden, kümeyi silmek için aşağıdakileri girin:

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
