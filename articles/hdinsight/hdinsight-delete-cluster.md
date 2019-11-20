---
title: HDInsight kümesini silme-Azure
description: Azure HDInsight kümesini silebilmeniz için çeşitli yollarla ilgili bilgiler
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/17/2019
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: a28d59cb35004fac6b069f2aa41042b4d46e443f
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091532"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Tarayıcınızı, PowerShell 'i veya Azure CLı 'yı kullanarak bir HDInsight kümesini silme

HDInsight kümesi faturalandırması küme oluşturulduğunda başlar ve küme silindiğinde sona erer. Fatura dakikalara eşit olarak dağıtıldığından, kullanılmayan kümelerinizi mutlaka silmelisiniz. Bu belgede [Azure Portal](https://portal.azure.com), [Azure PowerShell az Module](https://docs.microsoft.com/powershell/azure/overview)ve [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)kullanarak bir kümeyi silmeyi öğreneceksiniz.

> [!IMPORTANT]  
> HDInsight kümesini silmek, kümeyle ilişkili Azure depolama hesaplarını veya Data Lake Storage silmez. Gelecekte bu hizmetlerde depolanan verileri yeniden kullanabilirsiniz.

## <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com) oturum açın.

2. Sol menüden **Tüm Service** > **Analytics** > **HDInsight kümelerine** gidin ve kümenizi seçin.

3. Varsayılan görünümden **Sil** simgesini seçin. Kümenizi silmek için istemi izleyin.

    ![HDInsight küme silme düğmesi](./media/hdinsight-delete-cluster/hdinsight-delete-cluster.png)

## <a name="azure-powershell-az-module"></a>Azure PowerShell az Module

Aşağıdaki `CLUSTERNAME` koddaki HDInsight kümenizin adıyla değiştirin. Bir PowerShell isteminden, kümeyi silmek için aşağıdaki komutu girin:

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>Azure CLI

' `CLUSTERNAME` Yi HDInsight kümenizin adıyla ve `RESOURCEGROUP` kaynak grubunuzun adı ile aşağıdaki kodda olacak şekilde değiştirin.  Bir komut isteminden, kümeyi silmek için aşağıdakileri girin:

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
