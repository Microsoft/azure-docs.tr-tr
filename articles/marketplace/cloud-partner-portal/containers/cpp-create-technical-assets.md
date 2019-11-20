---
title: Azure kapsayıcıları görüntüsü teknik varlıkları oluşturma | Azure Marketi
description: Azure kapsayıcısı için teknik varlıklar oluşturun.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pabutler
ms.openlocfilehash: 6aae1957a3dcc58339eb4f3c9e250513843ceb18
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819058"
---
# <a name="prepare-your-container-technical-assets"></a>Kapsayıcı teknik varlıklarınızı hazırlayın

Bu makalede, Azure Marketi 'ni sunan bir kapsayıcıyı yapılandırmaya yönelik adımlar ve gereksinimler açıklanmaktadır.

## <a name="before-you-begin"></a>Başlamadan önce

Hızlı başlangıç, öğreticiler ve örnekler sağlayan [Azure Container Instances](https://docs.microsoft.com/azure/container-instances) belgelerini gözden geçirin.

## <a name="fundamental-technical-knowledge"></a>Temel teknik bilgi

Bu varlıkları tasarlama, oluşturma ve test etme zaman alır ve hem Azure platformu hem de teklifi oluşturmak için kullanılan teknolojiler hakkında teknik bilgi gerektirir.
 
Çözüm etki alanınız ' ne ek olarak, mühendislik ekibiniz aşağıdaki Microsoft teknolojileri hakkında bilgi sahibi olmalıdır:

-   [Azure hizmetlerini](https://azure.microsoft.com/services/) temel olarak anlama 
-   [Azure uygulamalarını tasarlama ve mimarın](https://azure.microsoft.com/solutions/architecture/)
-   [Azure sanal makineler](https://azure.microsoft.com/services/virtual-machines/), [Azure depolama](https://azure.microsoft.com/services/?filter=storage) ve [Azure ağı](https://azure.microsoft.com/services/?filter=networking) ile çalışma hakkında bilgi
-   [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) çalışma bilgileri
-   [JSON](https://www.json.org/) hakkında çalışma bilgisi

## <a name="suggested-tools"></a>Önerilen araçlar

Kapsayıcı görüntünüzü yönetmeye yardımcı olması için aşağıdaki komut dosyası ortamlarının birini veya her ikisini birden seçin:

-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [Azure CLI](https://docs.microsoft.com/cli/azure)

Ayrıca, aşağıdaki araçların geliştirme ortamınıza eklenmesini öneririz:

-   [Azure Depolama Gezgini](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Visual Studio Code](https://code.visualstudio.com/)
    *   Uzantı: [Azure Resource Manager araçları](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   Uzantı: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   Uzantı: [prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Ayrıca, [Azure Geliştirici Araçları](https://azure.microsoft.com/tools/) sayfasındaki kullanılabilir araçların incelenmesi önerilir ve Visual Studio kullanıyorsanız, [Visual Studio Market](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Kapsayıcı görüntüsünü oluşturma

Daha fazla bilgi için aşağıdakilere bakın:

* [Öğretici: Azure Container Instances dağıtım için bir kapsayıcı görüntüsü oluşturma](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
* [Öğretici: Azure Container Registry görevlerle bulutta kapsayıcı görüntüleri oluşturun ve dağıtın](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)

## <a name="next-steps"></a>Sonraki adımlar

[Kapsayıcı Teklifinizi oluşturma](./cpp-create-offer.md)
