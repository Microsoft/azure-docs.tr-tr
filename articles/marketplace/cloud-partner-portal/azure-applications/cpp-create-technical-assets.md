---
title: Azure uygulaması teknik varlıkları oluşturma | Azure Marketi
description: Azure Uygulama teklifi için teknik varlıklar oluşturun.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 12/13/2018
ms.author: pabutler
ms.openlocfilehash: c9abaab4597e9fea43a2f0dcabc0e4a527ed5a6a
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827599"
---
# <a name="prepare-your-azure-application-technical-assets"></a>Azure uygulaması teknik varlıklarınızı hazırlayın

Bu makalede, Azure Uygulama teklifiniz için teknik varlıkları hazırlama kaynakları açıklanmaktadır.

## <a name="before-you-begin"></a>Başlamadan önce

Aşağıdaki videoyu gözden geçirin, [Çözüm şablonları ve Azure Marketi Için yönetilen uygulamalar](https://channel9.msdn.com/Events/Build/2018/BRK3603), bir Azure Uygulama çözümü tanımlamak için bir Azure Resource Manager şablonu yazma hakkında genel bir bakış ve ardından bunu daha sonra yayımlama Azure Marketi için uygulama teklifi.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


Hızlı başlangıçler, öğreticiler ve örnekler sağlayan aşağıdaki Azure uygulaması belgelerini gözden geçirin.

- [Azure Resource Manager şablonlarını anlama](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
- Hızlı başlangıçlar

  - [Azure hızlı başlangıç şablonları](https://azure.microsoft.com/documentation/templates/)
  - [GitHub Azure hızlı başlangıç şablonları](https://github.com/azure/azure-quickstart-templates)
  - [Uygulama tanımını Yayımla](https://docs.microsoft.com/azure/managed-applications/publish-managed-app-definition-quickstart)
  - [Hizmet kataloğu uygulaması dağıtma](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

  
- Öğreticiler:

  - [Tanım dosyaları oluştur](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
  - [Market uygulaması yayımlama](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

  - Lerinizi

    - [Azure CLI](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    - [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    - [Yönetilen uygulama çözümleri](https://docs.microsoft.com/azure/managed-applications/sample-projects)

## <a name="fundamental-technical-knowledge"></a>Temel teknik bilgi

Bu varlıkları tasarlama, oluşturma ve test etme zaman alır ve hem Azure platformu hem de teklifi oluşturmak için kullanılan teknolojiler hakkında teknik bilgi gerektirir.

Mühendislik ekibiniz aşağıdaki Microsoft teknolojileri hakkında bilgi sahibi olmalıdır:

- [Azure hizmetlerini](https://azure.microsoft.com/services/) temel olarak anlama
- [Azure uygulamalarını tasarlama ve mimarın](https://azure.microsoft.com/solutions/architecture/)
- [Azure sanal makineler](https://azure.microsoft.com/services/virtual-machines/), [Azure depolama](https://azure.microsoft.com/services/?filter=storage)ve [Azure ağı](https://azure.microsoft.com/services/?filter=networking) ile çalışma hakkında bilgi
- [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) çalışma bilgileri
- [JSON](https://www.json.org/) hakkında çalışma bilgisi

## <a name="suggested-tools"></a>Önerilen araçlar

Azure uygulamanızın yönetilmesine yardımcı olmak için aşağıdaki komut dosyası ortamlarının birini veya her ikisini birden seçin:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
- [Azure CLI](https://docs.microsoft.com/cli/azure)

Geliştirme ortamınıza aşağıdaki araçları eklemeniz önerilir:

- [Azure Depolama Gezgini](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- Aşağıdaki uzantılara sahip [Visual Studio Code](https://code.visualstudio.com/) :

  - Uzantı: [Azure Resource Manager araçları](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Uzantı: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Uzantı: [prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Ayrıca, [Azure Geliştirici Araçları](https://azure.microsoft.com/tools/) sayfasındaki kullanılabilir araçların incelenmesi önerilir ve Visual Studio kullanıyorsanız, [Visual Studio Market](https://marketplace.visualstudio.com/).

## <a name="next-steps"></a>Sonraki adımlar

[Azure uygulama teklifi oluşturma](./cpp-create-offer.md)

