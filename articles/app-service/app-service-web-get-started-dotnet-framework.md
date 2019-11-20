---
title: ASP.NET C# Framework Web uygulaması oluşturma-Azure App Service | Microsoft Docs
description: Varsayılan C# ASP.NET web uygulamasını dağıtarak Azure App Service'te web uygulamalarını çalıştırma hakkında bilgi edinin.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: gwallace
ms.assetid: 04a1becf-7756-4d4e-92d8-d9471c263d23
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 10/21/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 04dd7cb55f9a6d4ceb722606f0eb90792413e414
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73471762"
---
# <a name="create-an-aspnet-framework-web-app-in-azure"></a>Azure’da ASP.NET Framework web uygulaması oluşturma

[Azure App Service](overview.md), yüksek oranda ölçeklenebilen, kendi kendine düzeltme eki uygulayan bir web barındırma hizmeti sunar.

Bu hızlı başlangıçta, ilk ASP.NET Web uygulamanızın Azure App Service için nasıl dağıtılacağı gösterilmektedir. İşiniz bittiğinde bir App Service planına sahip olacaksınız. Ayrıca dağıtılmış bir Web uygulamasına sahip bir App Service uygulamanız olacaktır.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için, **ASP.net ve Web geliştirme** iş yüküyle <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> ' ü yüklemelisiniz.

Visual Studio 2019 zaten yüklüyse:

- **Güncelleştirmeler Için** **Yardım** > denetle ' i seçerek en son güncelleştirmeleri Visual Studio 'ya yükler.
- Araçlar **ve Özellikler al** > **Araçlar** ' a tıklayarak iş yükünü ekleyin.

## ASP.NET Web uygulaması oluşturma<a name="create-and-publish-the-web-app"></a>

Aşağıdaki adımları izleyerek bir ASP.NET Web uygulaması oluşturun:

1. Visual Studio 'Yu açın ve **Yeni proje oluştur**' u seçin.

2. **Yeni proje oluştur**' da, **ASP.NET Web uygulaması (.NET Framework)** bulun ve seçin ve ardından **İleri**' yi seçin.

3. **Yeni projenizi yapılandırın**bölümünde, uygulamayı _Myfirstazurewebapp_olarak adlandırın ve ardından **Oluştur**' u seçin.

   ![Web uygulaması projenizi yapılandırma](./media/app-service-web-get-started-dotnet-framework/configure-web-app-project-framework.png)

4. Azure’a herhangi bir türde ASP.NET web uygulaması dağıtabilirsiniz. Bu hızlı başlangıç için **MVC** şablonunu seçin.

5. Kimlik doğrulamanın **kimlik doğrulaması yok**olarak ayarlandığından emin olun. **Oluştur**'u seçin.

   ![ASP.NET Web uygulaması oluşturma](./media/app-service-web-get-started-dotnet-framework/select-mvc-template-vs2019.png)

6. Web uygulamasını yerel olarak çalıştırmak için Visual Studio menüsünden **hata ayıkla** > **Başlat** ' ı seçin.

   ![Uygulamayı yerel olarak çalıştırma](./media/app-service-web-get-started-dotnet-framework/local-web-app.png)

## Web uygulamanızı yayımlama<a name="launch-the-publish-wizard"></a>

1. **Çözüm Gezgini**, **Myfirstazurewebapp** projesine sağ tıklayın ve **Yayımla**' yı seçin.

1. **App Service** öğesini seçin ve ardından **yayımlanacak** **profili oluştur** ' u değiştirin.

   ![Projeye genel bakış sayfasından yayımlama](./media/app-service-web-get-started-dotnet-framework/publish-app-framework-vs2019.png)

1. **App Service yeni oluştur**' da seçenekleriniz, Azure 'da oturum açmış olup olmadığınız ve bir Azure hesabına bağlı bir Visual Studio hesabınız olup olmadığına bağlıdır. Azure aboneliğinizde oturum açmak için **Hesap Ekle** veya **oturum aç** seçeneklerinden birini belirleyin. Zaten oturum açtıysanız istediğiniz hesabı seçin.

   > [!NOTE]
   > Zaten oturum açtıysanız **Oluştur** öğesini henüz seçmeyin.
   >
   >

   ![Azure'da oturum açma](./media/app-service-web-get-started-dotnet-framework/sign-in-azure-framework-vs2019.png)

   [!INCLUDE [resource group intro text](../../includes/resource-group.md)]

1. **Kaynak grubu**için **Yeni**' yi seçin.

1. **Yeni kaynak grubu adı**alanına *Myresourcegroup* yazın ve **Tamam**' ı seçin.

   [!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. **Barındırma planına**yönelik için **Yeni**' yi seçin.

1. **Barındırma planını Yapılandır** iletişim kutusunda, aşağıdaki tablodaki değerleri girin ve ardından **Tamam**' ı seçin.

   | Ayar | Önerilen Değer | Açıklama |
   |-|-|-|
   |App Service Planı| myAppServicePlan | App Service planının adı. |
   | Konum | Batı Avrupa | Web uygulamasının barındırıldığı veri merkezi. |
   | Boyut | Ücretsiz | [Fiyatlandırma katmanı](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), barındırma özelliklerini belirler. |

   ![App Service planı oluşturma](./media/app-service-web-get-started-dotnet-framework/app-service-plan-framework-vs2019.png)

1. **Ad**alanına, yalnızca geçerli karakterleri içeren benzersiz bir uygulama adı girin `a-z`, `A-Z`, `0-9`ve `-`. Otomatik olarak oluşturulan benzersiz adı kabul edebilirsiniz. Web uygulamasının URL'si `http://<app_name>.azurewebsites.net` şeklindedir; burada `<app_name>`, uygulamanızın adıdır.

2. Azure kaynaklarını oluşturmaya başlamak için **Oluştur**’u seçin.

   ![Uygulama adını yapılandırma](./media/app-service-web-get-started-dotnet-framework/web-app-name-framework-vs2019.png)

Sihirbaz tamamlandıktan sonra ASP.NET web uygulamasını Azure’da yayımlar ve ardından uygulamayı varsayılan tarayıcıda başlatır.

![Azure’da yayımlanmış ASP.NET web uygulaması](./media/app-service-web-get-started-dotnet-framework/published-azure-web-app.png)

**App Service yeni oluştur** sayfasında belirtilen uygulama adı, `http://<app_name>.azurewebsites.net`biçiminde URL ön eki olarak kullanılır.

**Tebrikler!** ASP.NET Web uygulamanız Azure App Service ' de canlı olarak çalışıyor.

## <a name="update-the-app-and-redeploy"></a>Uygulamayı güncelleştirme ve yeniden dağıtma

1. **Çözüm Gezgini**' de, projeniz altında, **Home** > **Index. cshtml** > **Görünümler** ' i açın.

1. Üst kısımda `<div class="jumbotron">` HTML etiketini bulun ve tüm öğeyi aşağıdaki kodla değiştirin:

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Azure’a yeniden dağıtmak için **Çözüm Gezgini**’nde **myFirstAzureWebApp** projesine sağ tıklayıp **Yayımla**’yı seçin. Ardından **Yayımla**' yı seçin.

Yayımlama tamamlandığında Visual Studio, web uygulamasının URL’si ile bir tarayıcı başlatır.

![Azure’da güncelleştirilmiş ASP.NET web uygulaması](./media/app-service-web-get-started-dotnet-framework/updated-azure-web-app.png)

## <a name="manage-the-azure-app"></a>Azure uygulamasını yönetme

1. Web uygulamasını yönetmek için [Azure Portal](https://portal.azure.com)gidin ve **uygulama hizmetleri**' ni arayıp seçin.

   ![Uygulama hizmetlerini seçin](./media/app-service-web-get-started-dotnet-framework/app-services.png)

2. **Uygulama hizmetleri** sayfasında, Web uygulamanızın adını seçin.

   ![Azure uygulamasına portal gezintisi](./media/app-service-web-get-started-dotnet-framework/access-portal-framework-vs2019.png)

   Web uygulamanızın Genel Bakış sayfasını görürsünüz. Burada, tarama, durdurma, başlatma, yeniden başlatma ve silme gibi temel yönetimi yapabilirsiniz.

   ![Azure portal App Service Genel Bakış](./media/app-service-web-get-started-dotnet-framework/web-app-general-framework-vs2019.png)

   Soldaki menü, uygulamanızı yapılandırmak için farklı sayfalar sağlar.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [SQL Veritabanı ile ASP.NET](app-service-web-tutorial-dotnet-sqldatabase.md)
