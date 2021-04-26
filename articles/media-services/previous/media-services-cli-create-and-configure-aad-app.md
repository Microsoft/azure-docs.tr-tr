---
title: Azure CLı kullanarak bir Azure AD uygulaması oluşturun ve bunu Azure Media Services API 'sine erişecek şekilde yapılandırın | Microsoft Docs
description: Bu konu başlığı altında, Azure CLı kullanarak bir Azure AD uygulaması oluşturma ve bu uygulamayı Azure Media Services API 'sine erişecek şekilde yapılandırma gösterilmektedir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 6517cc8224a1a4e2e3319fde52fb621e4f7ab564
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103016942"
---
# <a name="use-azure-cli-to-create-an-azure-ad-app-and-configure-it-to-access-media-services-api"></a>Azure CLı kullanarak bir Azure AD uygulaması oluşturun ve Media Services API 'sine erişecek şekilde yapılandırın

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Media Services v2’ye herhangi bir yeni özellik veya işlevsellik eklenmemektedir. <br/>[V3 Media Services](../latest/index.yml)en son sürüme göz atın. Ayrıca bkz. [v2 'den v3 'e geçiş kılavuzu](../latest/migrate-v-2-v-3-migration-introduction.md)

Bu konuda, Azure Media Services kaynaklara erişmek üzere bir Azure Active Directory (Azure AD) uygulaması ve hizmet sorumlusu oluşturmak için Azure CLı 'nın nasıl kullanılacağı gösterilmektedir. 

## <a name="prerequisites"></a>Önkoşullar

- Azure hesabı. Ayrıntılar için bkz. [Azure Ücretsiz deneme](https://azure.microsoft.com/pricing/free-trial/). 
- Bir Media Services hesabı. Daha fazla bilgi için [Azure Portal kullanarak Azure Media Services hesabı oluşturma](media-services-portal-create-account.md)konusuna bakın.

## <a name="use-the-azure-cloud-shell"></a>Azure Cloud Shell kullanın

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. Portalın üst gezinti bölmesinden Cloud Shell başlatın.

    ![Cloud Shell](./media/media-services-cli-create-and-configure-aad-app/media-services-cli-create-and-configure-aad-app01.png) 

Daha fazla bilgi için bkz. [Azure Cloud Shell Genel Bakış](../../cloud-shell/overview.md).

## <a name="create-an-azure-ad-app-and-configure-access-to-the-media-account-with-azure-cli"></a>Azure CLı ile bir Azure AD uygulaması oluşturun ve medya hesabına erişimi yapılandırın
 
```azurecli
az login
az ad sp create-for-rbac --name <appName> 
az role assignment create --assignee < user/app id> --role Contributor --scope <subscription/subscription id>
```

Örnek:

```azurecli
az role assignment create --assignee a3e068fa-f739-44e5-ba4d-ad57866e25a1 --role Contributor --scope /subscriptions/0b65e280-7917-4874-9fed-1307f2615ea2/resourceGroups/Default-AzureBatch-SouthCentralUS/providers/microsoft.media/mediaservices/sbbash
```

Bu örnekte, **kapsam** Media Services hesabı için tam kaynak yoludur. Ancak, **kapsam** herhangi bir düzeyde olabilir.

Örneğin, aşağıdaki düzeylerin biri olabilir:
 
* **Abonelik** düzeyi.
* **Kaynak grubu** düzeyi.
* **Kaynak** düzeyi (örneğin, bir medya hesabı).

Daha fazla bilgi için bkz [. Azure CLI Ile Azure hizmet sorumlusu oluşturma](/cli/azure/create-an-azure-service-principal-azure-cli)

Ayrıca bkz. [Azure CLI kullanarak Azure rol atamaları ekleme veya kaldırma](../../role-based-access-control/role-assignments-cli.md). 

## <a name="next-steps"></a>Sonraki adımlar

[Hesabınıza dosya yüklemeye](media-services-portal-upload-files.md)başlayın.
