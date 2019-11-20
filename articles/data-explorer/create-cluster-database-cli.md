---
title: Azure CLı kullanarak Azure Veri Gezgini kümesi ve veritabanı oluşturma
description: Azure CLı kullanarak Azure Veri Gezgini kümesi ve veritabanı oluşturmayı öğrenin
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: bd53a8e29254af617b6cfa68935a191a50fc526c
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326763"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-azure-cli"></a>Azure CLı kullanarak Azure Veri Gezgini kümesi ve veritabanı oluşturma

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [ARM şablonu](create-cluster-database-resource-manager.md)

Azure Veri Gezgini uygulamalar, web siteleri, IoT cihazları ve daha fazlasından akışı yapılan büyük miktarda veri üzerinde gerçek zamanlı analiz yapmaya yönelik hızlı ve tam olarak yönetilen bir veri analizi hizmetidir. Azure Veri Gezgini kullanmak için, önce bir küme oluşturun ve bu kümede bir veya daha fazla veritabanı oluşturursunuz. Daha sonra sorguları bu verilere karşı çalıştırmak için bir veritabanına (yükleme) sahip olursunuz. Bu makalede, Azure CLı kullanarak bir küme ve veritabanı oluşturursunuz.

## <a name="prerequisites"></a>Önkoşullar

Bu makaleyi tamamlayabilmeniz için bir Azure aboneliğine ihtiyacınız vardır. Aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure CLı 'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu makale, Azure CLı sürüm 2.0.4 veya üstünü gerektirir. Sürümünüzü kontrol etmek için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yı yükleme](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="configure-the-cli-parameters"></a>CLı parametrelerini yapılandırma

Azure Cloud Shell içinde komut çalıştırıyorsanız aşağıdaki adımlar gerekli değildir. CLı 'yi yerel olarak çalıştırıyorsanız, Azure 'da oturum açmak ve geçerli aboneliğinizi ayarlamak için şu adımları izleyin:

1. Azure'da oturum açmak için aşağıdaki komutu çalıştırın:

    ```azurecli-interactive
    az login
    ```

1. Kümenizin oluşturulmasını istediğiniz aboneliği ayarlayın. @No__t-0 değerini kullanmak istediğiniz Azure aboneliğinin adıyla değiştirin:

    ```azurecli-interactive
    az account set --subscription MyAzureSub
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Azure Veri Gezgini kümesi oluşturma

1. Aşağıdaki komutu kullanarak kümenizi oluşturun:

    ```azurecli-interactive
    az kusto cluster create --name azureclitest --sku D11_v2 --resource-group testrg
    ```

   |**Ayar** | **Önerilen değer** | **Alan açıklaması**|
   |---|---|---|
   | name | *azureclitest* | Kümenizin istenen adı.|
   | sku | *D13_v2* | Kümeniz için kullanılacak SKU. |
   | resource-group | *testrg* | Kümenin oluşturulacağı kaynak grubu adı. |

    Küme kapasitesi gibi kullanabileceğiniz ek isteğe bağlı parametreler vardır.

1. Kümenizin başarıyla oluşturulup oluşturulmayacağını denetlemek için şu komutu çalıştırın:

    ```azurecli-interactive
    az kusto cluster show --name azureclitest --resource-group testrg
    ```

Sonuç `provisioningState` değeri`Succeeded` ile içeriyorsa, küme başarıyla oluşturuldu.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Azure Veri Gezgini kümesinde veritabanı oluşturma

1. Aşağıdaki komutu kullanarak veritabanınızı oluşturun:

    ```azurecli-interactive
    az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period P365D --hot-cache-period P31D
    ```

   |**Ayar** | **Önerilen değer** | **Alan açıklaması**|
   |---|---|---|
   | küme adı | *azureclitest* | Veritabanının oluşturulacağı Kümenizin adı.|
   | name | *cliendatabase* | Veritabanınızın adı.|
   | resource-group | *testrg* | Kümenin oluşturulacağı kaynak grubu adı. |
   | geçici silme dönemi | *P365D* | Verilerin sorgu için kullanılabilir kalacağı süreyi belirtir. Daha fazla bilgi için bkz. [bekletme ilkesi](/azure/kusto/concepts/retentionpolicy) . |
   | sık erişimli önbellek dönemi | *P31D* | Verilerin önbellekte tutulacağı süreyi belirtir. Daha fazla bilgi için [önbellek ilkesi](/azure/kusto/concepts/cachepolicy) 'ne bakın. |

1. Oluşturduğunuz veritabanını görmek için aşağıdaki komutu çalıştırın:

    ```azurecli-interactive
    az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
    ```

Artık bir kümeniz ve veritabanınız var.

## <a name="clean-up-resources"></a>Kaynakları temizleme

* Diğer makalelerimizi izlemeyi planlıyorsanız oluşturduğunuz kaynakları saklayın.
* Kaynakları temizlemek için kümeyi silin. Bir kümeyi sildiğinizde, içindeki tüm veritabanlarını da siler. Kümenizi silmek için aşağıdaki komutu kullanın:

    ```azurecli-interactive
    az kusto cluster delete --name azureclitest --resource-group testrg
    ```

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Veri Gezgini Python kitaplığını kullanarak verileri alma](python-ingest-data.md)
