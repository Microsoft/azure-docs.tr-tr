---
title: Azure İşlevleri için sürekli dağıtım
description: İşlevlerinizi yayımlamak için Azure App Service sürekli dağıtım özelliklerini kullanın.
author: ggailey777
manager: gwallace
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: glenga
ms.openlocfilehash: dae75153cffbf2f0e836e1a28b78a9f05f54e6e0
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091175"
---
# <a name="continuous-deployment-for-azure-functions"></a>Azure İşlevleri için sürekli dağıtım

[Kaynak denetimi tümleştirmesini](functions-deployment-technologies.md#source-control)kullanarak kodunuzu sürekli olarak dağıtmak Için Azure işlevleri 'ni kullanabilirsiniz. Kaynak denetimi tümleştirmesi, bir kod güncelleştirmesinin Azure 'a dağıtımı tetiklediği bir iş akışını sağlar. Azure Işlevleri 'ne yeni başladıysanız, [Azure işlevlerine genel bakış](functions-overview.md)' ı inceleyerek başlayın.

Sürekli dağıtım, birden çok ve sık katkılarınızı tümleştiren projeler için iyi bir seçenektir. Sürekli dağıtım kullandığınızda, kodunuz için tek bir Truth kaynağı tuttuğunuz için ekiplerin kolayca işbirliği yapmasına olanak tanır. Azure Işlevlerinde sürekli dağıtımı aşağıdaki kaynak kodu konumlarından yapılandırabilirsiniz:

* [Azure Repos](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [Bitbucket](https://bitbucket.org/)

Azure 'daki işlevlerin dağıtım birimi, işlev uygulamasıdır. Bir işlev uygulamasındaki tüm işlevler aynı zamanda dağıtılır. Sürekli dağıtımı etkinleştirdikten sonra, Azure portal işlev koduna erişim salt *okunurdur* , çünkü gerçeği kaynağı başka bir yerde olacak şekilde ayarlanmıştır.

## <a name="requirements-for-continuous-deployment"></a>Sürekli dağıtım için gereksinimler

Sürekli dağıtımın başarılı olması için dizin yapınız, Azure Işlevlerinin beklediği temel klasör yapısıyla uyumlu olmalıdır.

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

>[!NOTE]  
> Sürekli dağıtım henüz bir tüketim planında çalışan Linux uygulamaları için desteklenmemektedir. 

## <a name="credentials"></a>Sürekli dağıtımı ayarlama

Mevcut bir işlev uygulaması için sürekli dağıtımı yapılandırmak üzere, bu adımları izleyin. Adımlarda bir GitHub deposu ile tümleştirme gösterilmektedir, ancak Azure Repos veya diğer kaynak kodu depoları için de benzer adımlar geçerlidir.

1. [Azure Portal](https://portal.azure.com)işlev uygulamanızda, **Dağıtım Merkezi** > **platform özellikleri** ' ni seçin.

    ![Dağıtım merkezini aç](./media/functions-continuous-deployment/platform-features.png)

2. **Dağıtım Merkezi**'nde **GitHub**' ı seçin ve ardından **Yetkilendir**' i seçin. GitHub yetkiniz zaten varsa **devam**' ı seçin. 

    ![Azure App Service dağıtım merkezi](./media/functions-continuous-deployment/github.png)

3. GitHub 'da **Yetkilendir azureuygulamahizmeti** düğmesini seçin. 

    ![Azure App Service yetkilendir](./media/functions-continuous-deployment/authorize.png)
    
    Azure portal **Dağıtım Merkezi** 'nde **devam**' ı seçin.

4. Aşağıdaki derleme sağlayıcılarından birini seçin:

    * **App Service derleme hizmeti**: bir yapıya ihtiyacınız olmadığında veya genel bir yapıya ihtiyacınız varsa en iyi seçenektir.
    * **Azure Pipelines (Önizleme)** : yapı üzerinde daha fazla denetime ihtiyacınız olduğunda en iyi seçenektir. Bu sağlayıcı Şu anda önizlemededir.

    ![Yapı sağlayıcısı seçin](./media/functions-continuous-deployment/build.png)

5. Belirttiğiniz kaynak denetimi seçeneğine özgü bilgileri yapılandırın. GitHub için **kuruluş**, **Depo**ve **dal**değerlerini girmeniz veya seçmeniz gerekir. Değerler, kodunuzun konumunu temel alır. Sonra **devam**' ı seçin.

    ![GitHub 'ı yapılandırma](./media/functions-continuous-deployment/github-specifics.png)

6. Tüm ayrıntıları gözden geçirin ve ardından **son** ' u seçerek dağıtım yapılandırmanızı doldurun.

    ![Özet](./media/functions-continuous-deployment/summary.png)

İşlem tamamlandığında, belirtilen kaynaktaki tüm kodlar uygulamanıza dağıtılır. Bu noktada, dağıtım kaynağındaki değişiklikler Azure 'daki işlev uygulamanızda bu değişikliklerin dağıtımını tetikler.

## <a name="deployment-scenarios"></a>Dağıtım senaryoları

<a name="existing"></a>

### <a name="move-existing-functions-to-continuous-deployment"></a>Mevcut işlevleri sürekli dağıtıma taşı

[Azure Portal](https://portal.azure.com) işlevleri zaten yazdıysanız ve sürekli dağıtıma geçiş yapmadan önce uygulamanızın içeriğini indirmek istiyorsanız, Işlev uygulamanızın **genel bakış** sekmesine gidin. **Uygulama Içeriğini indir** düğmesini seçin.

![Uygulama içeriğini indir](./media/functions-continuous-deployment/download.png)

> [!NOTE]
> Sürekli tümleştirmeyi yapılandırdıktan sonra, kaynak dosyalarınızı artık Işlevler portalında düzenleyemezsiniz.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure İşlevleri için en iyi uygulamalar](functions-best-practices.md)
