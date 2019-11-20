---
title: ASP.NET Core C# Web uygulaması oluşturma-Azure App Service | Microsoft Docs
description: Varsayılan C# ASP.NET Core Web uygulamasını dağıtarak Azure App Service Web Apps 'i nasıl çalıştıracağınızı öğrenin.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/30/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 919e717811b3b04ca7407772fcf11f293d58508c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73471604"
---
# <a name="create-an-aspnet-core-web-app-in-azure"></a>Azure’da ASP.NET Core web uygulaması oluşturma

> [!NOTE]
> Bu makalede bir uygulamanın Windows üzerinde App Service'e dağıtımı yapılır. _Linux_ üzerinde App Service'e dağıtım yapmak için bkz. [Linux üzerinde App Service'te .NET Core web uygulaması oluşturma](./containers/quickstart-dotnetcore.md).
>

[Azure App Service](overview.md), yüksek oranda ölçeklenebilen, kendi kendine düzeltme eki uygulayan bir web barındırma hizmeti sunar.

Bu hızlı başlangıçta, ilk ASP.NET Core Web uygulamanızın Azure App Service nasıl dağıtılacağı gösterilmektedir. İşiniz bittiğinde, dağıtılan bir Web uygulamasına sahip bir App Service planından ve App Service uygulamasından oluşan bir kaynak grubunuz olacaktır.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için, **ASP.net ve Web geliştirme** iş yüküyle <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> ' ü yüklemelisiniz.

Visual Studio 2019 zaten yüklüyse:

- **Güncelleştirmeler Için** **Yardım** > denetle ' i seçerek en son güncelleştirmeleri Visual Studio 'ya yükler.
- Araçlar **ve Özellikler al** > **Araçlar** ' a tıklayarak iş yükünü ekleyin.

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core web uygulaması oluşturma

Aşağıdaki adımları izleyerek bir ASP.NET Core Web uygulaması oluşturun:

1. Visual Studio 'Yu açın ve **Yeni proje oluştur**' u seçin.

1. **Yeni proje oluştur**' da, Için C# **ASP.NET Core Web uygulaması** bulun ve seçin ve ardından **İleri**' yi seçin.

1. **Yeni projenizi yapılandırın**bölümünde, uygulamayı _Myfirstazurewebapp_olarak adlandırın ve ardından **Oluştur**' u seçin.

   ![Web uygulaması projenizi yapılandırma](./media/app-service-web-get-started-dotnet/configure-web-app-project.png)

1. Bu hızlı başlangıç için **Web uygulaması** şablonunu seçin. Kimlik doğrulamanın **kimlik doğrulaması yok** olarak ayarlandığından emin olun ve başka bir seçenek seçili değildir. **Oluştur**'u seçin.

   ![Bu öğretici için ASP.NET Core Razor Pages seçin](./media/app-service-web-get-started-dotnet/aspnet-razor-pages-app.png)

    Azure’a herhangi bir türde ASP.NET Core web uygulaması dağıtabilirsiniz.

1. Web uygulamasını yerel olarak çalıştırmak için Visual Studio menüsünden **hata ayıkla** > **Başlat** ' ı seçin.

   ![Uygulamayı yerel olarak çalıştırma](./media/app-service-web-get-started-dotnet/razor-web-app-running-locally.png)

## <a name="publish-your-web-app"></a>Web uygulamanızı yayımlama

1. **Çözüm Gezgini**, **Myfirstazurewebapp** projesine sağ tıklayın ve **Yayımla**' yı seçin.

1. **App Service** seçip **Yayımla**' yı seçin.

   ![Projeye genel bakış sayfasından yayımlama](./media/app-service-web-get-started-dotnet/publish-app-vs2019.png)

1. **App Service yeni oluştur**' da seçenekleriniz, Azure 'da oturum açmış olup olmadığınız ve bir Azure hesabına bağlı bir Visual Studio hesabınız olup olmadığına bağlıdır. Azure aboneliğinizde oturum açmak için **Hesap Ekle** veya **oturum aç** seçeneklerinden birini belirleyin. Zaten oturum açtıysanız istediğiniz hesabı seçin.

   > [!NOTE]
   > Zaten oturum açtıysanız **Oluştur** öğesini henüz seçmeyin.
   >

   ![Azure'da oturum açma](./media/app-service-web-get-started-dotnet/sign-in-azure-vs2019.png)

   [!INCLUDE [resource group intro text](../../includes/resource-group.md)]

1. **Kaynak grubu**için **Yeni**' yi seçin.

1. **Yeni kaynak grubu adı**alanına *Myresourcegroup* yazın ve **Tamam**' ı seçin.

   [!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. **Barındırma planı**için **Yeni**' yi seçin.

1. **Barındırma planını Yapılandır** iletişim kutusunda, aşağıdaki tablodaki değerleri girin ve ardından **Tamam**' ı seçin.

   | Ayar | Önerilen Değer | Açıklama |
   |-|-|-|
   |App Service Planı| myAppServicePlan | App Service planının adı. |
   | Konum | Batı Avrupa | Web uygulamasının barındırıldığı veri merkezi. |
   | Boyut | Ücretsiz | [Fiyatlandırma katmanı](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), barındırma özelliklerini belirler. |

   ![App Service planı oluşturma](./media/app-service-web-get-started-dotnet/app-service-plan-vs2019.png)

1. **Ad**alanına, yalnızca geçerli karakterleri içeren benzersiz bir uygulama adı girin `a-z`, `A-Z`, `0-9`ve `-`. Otomatik olarak oluşturulan benzersiz adı kabul edebilirsiniz. Web uygulamasının URL'si `http://<app_name>.azurewebsites.net` şeklindedir; burada `<app_name>`, uygulamanızın adıdır.

   ![Uygulama adını yapılandırma](./media/app-service-web-get-started-dotnet/web-app-name-vs2019.png)

1. Azure kaynaklarını oluşturmaya başlamak için **Oluştur**’u seçin.

Sihirbaz tamamlandıktan sonra ASP.NET Core web uygulamasını Azure’da yayımlar ve ardından uygulamayı varsayılan tarayıcıda başlatır.

![Azure’da yayımlanmış ASP.NET web uygulaması](./media/app-service-web-get-started-dotnet/web-app-running-live.png)

**App Service yeni oluştur** sayfasında belirtilen uygulama adı, `http://<app_name>.azurewebsites.net`biçiminde URL ön eki olarak kullanılır.

**Tebrikler!** ASP.NET Core Web uygulamanız Azure App Service canlı olarak çalışıyor.

## <a name="update-the-app-and-redeploy"></a>Uygulamayı güncelleştirme ve yeniden dağıtma

1. **Çözüm Gezgini**, projenizin altında **Index. cshtml** > **Sayfalar** ' ı açın.

1. İki `<div>` etiketini aşağıdaki kodla değiştirin:

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Azure’a yeniden dağıtmak için **Çözüm Gezgini**’nde **myFirstAzureWebApp** projesine sağ tıklayıp **Yayımla**’yı seçin.

1. **Yayımla** Özeti sayfasında **Yayımla**' yı seçin.

   ![Visual Studio Yayımlama Özeti sayfası](./media/app-service-web-get-started-dotnet/publish-summary-page-vs2019.png)

Yayımlama tamamlandığında Visual Studio, web uygulamasının URL’si ile bir tarayıcı başlatır.

![Azure’da güncelleştirilmiş ASP.NET web uygulaması](./media/app-service-web-get-started-dotnet/web-app-running-live-updated.png)

## <a name="manage-the-azure-app"></a>Azure uygulamasını yönetme

Web uygulamasını yönetmek için [Azure Portal](https://portal.azure.com)gidin ve **uygulama hizmetleri**' ni arayıp seçin.

![Uygulama hizmetlerini seçin](./media/app-service-web-get-started-dotnet/app-services.png)

**Uygulama hizmetleri** sayfasında, Web uygulamanızın adını seçin.

![Azure uygulamasına portal gezintisi](./media/app-service-web-get-started-dotnet/access-portal-vs2019.png)

Web uygulamanızın Genel Bakış sayfasını görürsünüz. Burada, tarama, durdurma, başlatma, yeniden başlatma ve silme gibi temel yönetimi yapabilirsiniz.

![Azure portal App Service](./media/app-service-web-get-started-dotnet/web-app-general-vs2019.png)

Soldaki menü, uygulamanızı yapılandırmak için farklı sayfalar sağlar.

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [SQL Veritabanı ile ASP.NET Core](app-service-web-tutorial-dotnetcore-sqldb.md)
