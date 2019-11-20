---
title: Öğretici-Azure uzamsal bağlayıcılarını oturumlar ve cihazlar arasında paylaşma | Microsoft Docs
description: Bu öğreticide, bir arka uç hizmetiyle Unity 'de Android/iOS cihazları arasında Azure uzamsal bağlayıcı tanımlayıcıları paylaşmayı öğreneceksiniz.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: e8a60d5d90b684698d6fcb612278bcae6d4ed08e
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882329"
---
# <a name="tutorial-share-azure-spatial-anchors-across-sessions-and-devices"></a>Öğretici: Azure uzamsal bağlayıcılarını oturumlar ve cihazlar arasında paylaşma

Bu öğreticide, bir oturum sırasında bağlantılar oluşturmak ve ardından bunları aynı cihazda ya da farklı bir şekilde bulmak için [Azure uzamsal Tutturucuların](../overview.md) nasıl kullanılacağını öğreneceksiniz. Aynı çıpası aynı yerde ve aynı anda birden çok cihaz tarafından da bulunabilir.

![Kalıcılığı](./media/persistence.gif)

Azure uzamsal bağlantıları, zaman içinde cihazlarda konumlarını sürekli tutan nesneleri kullanarak karma gerçeklik deneyimleri oluşturmanıza olanak sağlayan bir platformlar arası geliştirici hizmetidir. İşiniz bittiğinde, iki veya daha fazla cihaza dağıtılabilecek bir uygulamanız olur. Bir örnek tarafından oluşturulan Azure uzamsal bağlantıları diğerleriyle paylaşılabilir.

Şunları öğrenirsiniz:

> [!div class="checklist"]
> * Azure 'da çıpası paylaşmak için kullanılabilecek bir ASP.NET Core Web uygulaması dağıtın ve bir süre için bellekte depolama alanı oluşturabilirsiniz.
> * Paylaşım bağlantıları web uygulamasından yararlanmak için, hızlı başlangıçlarımızın Unity örneğindeki AzureSpatialAnchorsLocalSharedDemo sahneyi yapılandırın.
> * ' İ dağıtın ve bir veya daha fazla cihaza çalıştırın.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Bu öğreticide Unity 'yi ve bir ASP.NET Core Web uygulamasını kullanmaya yaşıyorsanız, ancak yalnızca diğer cihazlarda Azure uzamsal bağlantı tanımlayıcılarını paylaşma hakkında bir örnek göstermek de vardır. Aynı amaca ulaşmak için diğer dilleri ve arka uç teknolojilerini kullanabilirsiniz. Ayrıca, bu öğreticide kullanılan ASP.NET Core Web uygulamasının .NET Core 2,2 SDK 'ya bağımlılığı vardır. Normal Azure Web Apps (Windows için) üzerinde ince çalışır, ancak şu anda Linux için Azure Web Apps üzerinde çalışmamakta olacaktır.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-the-sample-project"></a>Örnek projeyi indirin

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-your-sharing-anchors-service"></a>Paylaşım bağlantıları hizmetinizi dağıtma

## <a name="visual-studiotabvs"></a>[Visual Studio](#tab/VS)

Visual Studio 'yu açın ve `Sharing\SharingServiceSample` klasörde projeyi açın.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

## <a name="visual-studio-codetabvsc"></a>[Visual Studio Code](#tab/VSC)

Hizmeti VS Code dağıtmadan önce bir kaynak grubu ve bir App Service planı oluşturmanız gerekir.

### <a name="sign-in-to-azure"></a>Azure 'da oturum açma

<a href="https://portal.azure.com/" target="_blank">Azure Portal</a> gidin ve Azure aboneliğinizde oturum açın.

### <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

**Kaynak Grubu**’nun yanındaki **Yeni** öğesini seçin.

Kaynak grubunuzu **myResourceGroup** olarak adlandırıp **Tamam**’ı seçin.

### <a name="create-an-app-service-plan"></a>App Service planı oluşturma

[!INCLUDE [app-service-plan](../../../includes/app-service-plan.md)]

**Barındırma Planı**'nın yanındaki **Yeni**'yi seçin.

**Barındırma planını Yapılandır** iletişim kutusunda şu ayarları kullanın:

| Ayar | Önerilen değer | Açıklama |
|-|-|-|
|App Service Planı| MySharingServicePlan | App Service planının adı. |
| Konum | Batı ABD | Web uygulamasının barındırıldığı veri merkezi. |
| Boyut | Ücretsiz | Barındırma özelliklerini belirleyen [fiyatlandırma katmanı](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) . |

**Tamam**’ı seçin.

Visual Studio Code açın ve projeyi `Sharing\SharingServiceSample` klasöründe açın. Paylaşım hizmetini Visual Studio Code aracılığıyla dağıtmak için <a href="https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vscode?view=aspnetcore-2.2#open-it-with-visual-studio-code" target="_blank">Bu öğreticiyi</a> izleyin. ' Onu Visual Studio Code ile aç ' bölümünden başlayarak adımları izleyebilirsiniz. Daha önce dağıtılması ve yayımlanması gereken projeye zaten sahip olduğunuz için, SharingServiceSample gibi başka bir MVC projesi oluşturmayın.

---

## <a name="deploy-the-sample-app"></a>Örnek uygulamayı dağıtma

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure 'da bir ASP.NET Core Web uygulaması dağıttığınız ve bir Unity uygulamasını yapılandırmış ve dağıttınız. Uygulama ile uzamsal bağlayıcı oluşturdunuz ve bunları ASP.NET Core Web uygulamanızı kullanarak diğer cihazlarla paylaştırmadınız.

ASP.NET Core Web uygulamanızı, paylaşılan uzamsal bağlantı tanımlayıcılarınızın depolanmasını sürdürmek için Azure Cosmos DB kullanacak şekilde geliştirebilirsiniz. Azure Cosmos DB desteğinin eklenmesi, ASP.NET Core Web uygulamanızın bugün bir bağlantı oluşturmasına ve daha sonra, Web uygulamanızda saklanan tutturucu tanımlayıcıyı kullanarak daha sonra tekrar bulabilmesini sağlar.

> [!div class="nextstepaction"]
> [Bağlayıcıları depolamak için Azure Cosmo DB kullanma](./tutorial-use-cosmos-db-to-store-anchors.md)

