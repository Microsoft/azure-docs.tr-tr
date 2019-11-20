---
title: Azure CLı kullanarak bir Azure AD uygulaması oluşturun ve bunu Azure Media Services API 'sine erişecek şekilde yapılandırın | Microsoft Docs
description: Bu konu başlığı altında, Azure CLı kullanarak bir Azure AD uygulaması oluşturma ve bu uygulamayı Azure Media Services API 'sine erişecek şekilde yapılandırma gösterilmektedir.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/26/2019
ms.author: juliako
ms.openlocfilehash: f136fb666e93adc0fe92aee014e3da9a37bbd6aa
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035805"
---
# <a name="use-azure-cli-to-create-an-azure-ad-app-and-configure-it-to-access-media-services-api"></a>Azure CLı kullanarak bir Azure AD uygulaması oluşturun ve Media Services API 'sine erişecek şekilde yapılandırın 

> [!NOTE]
> Media Services v2’ye herhangi bir yeni özellik veya işlevsellik eklenmemektedir. <br/>En son sürüm olan [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/)’ü inceleyin. Ayrıca bkz. [v2 'den v3 'e geçiş kılavuzu](../latest/migrate-from-v2-to-v3.md)

Bu konuda, Azure Media Services kaynaklara erişmek üzere bir Azure Active Directory (Azure AD) uygulaması ve hizmet sorumlusu oluşturmak için Azure CLı 'nın nasıl kullanılacağı gösterilmektedir. 

## <a name="prerequisites"></a>Önkoşullar

- Bir Azure hesabı. Ayrıntılı bilgi için bkz. [Azure Ücretsiz Deneme Sürümü](https://azure.microsoft.com/pricing/free-trial/). 
- Bir Media Services hesabı. Daha fazla bilgi için [Azure Portal kullanarak Azure Media Services hesabı oluşturma](media-services-portal-create-account.md)konusuna bakın.

## <a name="use-the-azure-cloud-shell"></a>Azure Cloud Shell kullanın

1. [Azure Portal](https://portal.azure.com/) oturum açın.
2. Portalın üst gezinti bölmesinden Cloud Shell başlatın.

    ![Cloud Shell](./media/media-services-cli-create-and-configure-aad-app/media-services-cli-create-and-configure-aad-app01.png) 

Daha fazla bilgi için bkz. [Azure Cloud Shell Genel Bakış](../../cloud-shell/overview.md).

## <a name="create-an-azure-ad-app-and-configure-access-to-the-media-account-with-azure-cli"></a>Azure CLı ile bir Azure AD uygulaması oluşturun ve medya hesabına erişimi yapılandırın
 
```azurecli
az login
az ad sp create-for-rbac --name <appName> 
az role assignment create --assignee < user/app id> --role Contributor --scope <subscription/subscription id>
```

Örneğin:

```azurecli
az role assignment create --assignee a3e068fa-f739-44e5-ba4d-ad57866e25a1 --role Contributor --scope /subscriptions/0b65e280-7917-4874-9fed-1307f2615ea2/resourceGroups/Default-AzureBatch-SouthCentralUS/providers/microsoft.media/mediaservices/sbbash
```

Bu örnekte, **kapsam** Media Services hesabı için tam kaynak yoludur. Ancak, **kapsam** herhangi bir düzeyde olabilir.

Örneğin, aşağıdaki düzeylerin biri olabilir:
 
* **Abonelik** düzeyi.
* **Kaynak grubu** düzeyi.
* **Kaynak** düzeyi (örneğin, bir medya hesabı).

Daha fazla bilgi için bkz [. Azure CLI Ile Azure hizmet sorumlusu oluşturma](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli)

Ayrıca bkz. [Azure komut satırı arabirimiyle rol tabanlı Access Control yönetme](../../role-based-access-control/role-assignments-cli.md). 

## <a name="next-steps"></a>Sonraki adımlar

[Hesabınıza dosya yüklemeye](media-services-portal-upload-files.md)başlayın.
