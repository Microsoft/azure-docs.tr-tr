---
title: PowerShell kullanarak bir Azure Veri Gezgini kümesi ve veritabanı oluşturma
description: PowerShell kullanarak Azure Veri Gezgini kümesi ve veritabanı oluşturmayı öğrenin
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 1975125dd3bcd327ae7520e4cc413718e48d6ba9
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326751"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-powershell"></a>PowerShell kullanarak bir Azure Veri Gezgini kümesi ve veritabanı oluşturma

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [ARM şablonu](create-cluster-database-resource-manager.md)  

Azure Veri Gezgini uygulamalar, web siteleri, IoT cihazları ve daha fazlasından akışı yapılan büyük miktarda veri üzerinde gerçek zamanlı analiz yapmaya yönelik hızlı ve tam olarak yönetilen bir veri analizi hizmetidir. Azure Veri Gezgini kullanmak için, önce bir küme oluşturun ve bu kümede bir veya daha fazla veritabanı oluşturursunuz. Daha sonra sorguları bu verilere karşı çalıştırmak için bir veritabanına (yükleme) sahip olursunuz. Bu makalede, PowerShell kullanarak bir küme ve veritabanı oluşturursunuz. Azure Veri Gezgini kümelerini ve veritabanlarını oluşturmak ve yapılandırmak için Windows, Linux veya [Azure Cloud Shell](../cloud-shell/overview.md) içinde [az. kusto](/powershell/module/az.kusto/?view=azps-1.4.0#kusto) ile PowerShell cmdlet 'leri ve betikleri çalıştırabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure CLı 'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu makale, Azure CLı sürüm 2.0.4 veya üstünü gerektirir. Sürümünüzü kontrol etmek için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yı yükleme](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="configure-parameters"></a>Parametreleri Yapılandır

Azure Cloud Shell içinde komut çalıştırıyorsanız aşağıdaki adımlar gerekli değildir. CLı 'yi yerel olarak çalıştırıyorsanız, Azure 'da oturum açmak ve geçerli aboneliğinizi ayarlamak için 1 & 2. adımları uygulayın:

1. Azure'da oturum açmak için aşağıdaki komutu çalıştırın:

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. Kümenizin oluşturulmasını istediğiniz aboneliği ayarlayın:

    ```azurepowershell-interactive
     Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ```
1. Azure CLı 'yi yerel olarak veya Azure Cloud Shell 'de çalıştırırken, cihazınıza az. kusto modülünü yüklemeniz gerekir:
    
    ```azurepowershell-interactive
     Install-Module -Name Az.Kusto  
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Azure Veri Gezgini kümesi oluşturma

1. Aşağıdaki komutu kullanarak kümenizi oluşturun:

    ```azurepowershell-interactive
     New-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster -Location 'Central US' -Sku D13_v2 -Capacity 10
    ```

   |**Ayar** | **Önerilen değer** | **Alan açıklaması**|
   |---|---|---|
   | Name | *mykustocluster* | Kümenizin istenen adı.|
   | Sku | *D13_v2* | Kümeniz için kullanılacak SKU. |
   | ResourceGroupName | *testrg* | Kümenin oluşturulacağı kaynak grubu adı. |

    Küme kapasitesi gibi kullanabileceğiniz ek isteğe bağlı parametreler vardır.

1. Kümenizin başarıyla oluşturulup oluşturulmayacağını denetlemek için şu komutu çalıştırın:

    ```azurepowershell-interactive
    Get-AzKustoCluster -Name mykustocluster -ResourceGroupName testrg
    ```

Sonuç `provisioningState` değeri`Succeeded` ile içeriyorsa, küme başarıyla oluşturuldu.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Azure Veri Gezgini kümesinde veritabanı oluşturma

1. Aşağıdaki komutu kullanarak veritabanınızı oluşturun:

    ```azurepowershell-interactive
    New-AzKustoDatabase -ResourceGroupName testrg -ClusterName mykustocluster -Name mykustodatabase -SoftDeletePeriod 3650:00:00:00 -HotCachePeriod 3650:00:00:00
    ```

   |**Ayar** | **Önerilen değer** | **Alan açıklaması**|
   |---|---|---|
   | :/ | *mykustocluster* | Veritabanının oluşturulacağı Kümenizin adı.|
   | Name | *mykustodatabase* | Veritabanınızın adı.|
   | ResourceGroupName | *testrg* | Kümenin oluşturulacağı kaynak grubu adı. |
   | SoftDeletePeriod | *3650:00:00:00* | Verilerin sorgu için kullanılabilir kalacağı zaman miktarı. |
   | HotCachePeriod | *3650:00:00:00* | Verilerin önbellekte tutulacağı zaman miktarı. |

1. Oluşturduğunuz veritabanını görmek için aşağıdaki komutu çalıştırın:

    ```azurepowershell-interactive
    Get-AzKustoDatabase -ClusterName mykustocluster -ResourceGroupName testrg -Name mykustodatabase
    ```

Artık bir kümeniz ve veritabanınız var.

## <a name="clean-up-resources"></a>Kaynakları temizleme

* Diğer makalelerimizi izlemeyi planlıyorsanız oluşturduğunuz kaynakları saklayın.
* Kaynakları temizlemek için kümeyi silin. Bir kümeyi sildiğinizde, içindeki tüm veritabanlarını da siler. Kümenizi silmek için aşağıdaki komutu kullanın:

    ```azurepowershell-interactive
    Remove-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster
    ```

## <a name="next-steps"></a>Sonraki adımlar

* [Ek az. kusto komutları](/powershell/module/az.kusto/?view=azps-1.7.0#kusto)
* [Azure Veri Gezgini .NET Standard SDK 'sını kullanarak verileri alma (Önizleme)](net-standard-ingest-data.md)
