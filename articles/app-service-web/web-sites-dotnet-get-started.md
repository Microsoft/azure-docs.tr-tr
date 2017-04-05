---
title: "ASP.NET uygulamasını Visual Studio’yu kullanarak Azure’a dağıtma | Microsoft Docs"
description: "Azure App Service’te bir ASP.NET web projesini Visual Studio’yu kullanarak yeni bir web uygulamasına dağıtmayı öğrenin."
services: app-service\web
documentationcenter: .net
author: cephalin
manager: erikre
editor: 
ms.assetid: 69759e3c-384c-4afb-9278-db6724f6cb74
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 12/16/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 26d460a699e31f6c19e3b282fa589ed07ce4a068
ms.openlocfilehash: 93107d19e1814b30d69fc12c92b544d9760bda6a
ms.lasthandoff: 04/04/2017


---
# <a name="deploy-an-aspnet-web-app-to-azure-app-service-using-visual-studio"></a>ASP.NET web uygulamasını Visual Studio’yu kullanarak Azure App Service’e dağıtma
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

## <a name="overview"></a>Genel Bakış
Bu öğreticide, ASP.NET web uygulamasının Visual Studio 2015 kullanılarak [Azure App Service’teki bir web uygulamasına](app-service-web-overview.md) nasıl dağıtılacağı gösterilmektedir.

Bu öğretici, Azure kullanma deneyimi olmayan bir ASP.NET geliştiricisi olduğunuzu varsayar. İşiniz bittiğinde, bulutta çalışır hale gelecek basit bir web uygulamanız olur.

Şunları öğreneceksiniz:

* Visual Studio'da yeni bir web projesi oluştururken, yeni bir App Service web uygulaması oluşturma.
* Visual Studio kullanarak bir web projesini App Service web uygulamasına dağıtma.

Aşağıdaki diyagramda bu öğreticide yapacaklarınız gösterilir.

![Visual Studio oluşturma ve dağıtma diyagramı](./media/web-sites-dotnet-get-started/Create_App.png)

Öğreticinin sonunda bulunan [Sorun Giderme](#troubleshooting) bölümünde yolunda gitmeyen bir şey olduğunda neler yapılması gerektiği hakkında fikirler sunulur, [Sonraki adımlar](#next-steps) bölümünde ise Azure App Service’i kullanma hakkında daha kapsamlı öğreticilere giden bağlantılar verilir.

Bu bir başlangıç öğreticisi olduğundan, veritabanı kullanmayan ve kimlik doğrulaması veya yetkilendirme işlemi gerçekleştirmeyen basit bir uygulamanın nasıl dağıtılacağını gösteren bir web projesi içermektedir. Daha ileri düzey dağıtım konularına giden bağlantılar için bkz. [Azure web uygulaması dağıtma](web-sites-deploy.md).

.NET için Azure SDK yüklemek için gereken süre dışında, bu öğreticinin tamamlanması 10-15 dakika sürer.

## <a name="prerequisites"></a>Ön koşullar
* Bu öğretici, ASP.NET ve Visual Studio hakkında deneyimli olduğunuzu varsayar. Genel bir bilgi gerekirse, bkz. [ASP.NET MVC 5’i Kullanmaya Başlama](http://www.asp.net/mvc/overview/getting-started/introduction/getting-started)
* Bir Azure hesabınız olmalıdır. [Ücretsiz bir Azure hesabı açabilir](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) veya [Visual Studio abonelik avantajlarını etkinleştirebilirsiniz](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). 
  
    Bir Azure hesabı için kaydolmadan önce Azure App Service’i kullanmaya başlamak istiyorsanız [App Service’i Deneyin](https://azure.microsoft.com/try/app-service/)’e gidin. Burada, App Service’te kısa süreli bir başlangıç web uygulaması oluşturabilirsiniz; kredi kartı gerekmez ve hiçbir taahhüt yoktur.

## <a name="setupdevenv"></a>Geliştirme ortamını ayarlama
Bu öğretici, [.NET için Azure SDK](../dotnet-sdk.md) 2.9 veya sonraki bir sürümünü içeren Visual Studio 2015 için hazırlanmıştır. 

* [Visual Studio 2015 için en son Azure SDK’sını indirin](http://go.microsoft.com/fwlink/?linkid=518003). Visual Studio 2015’iniz yoksa, SDK sizin yerinize yükler.
  
  > [!NOTE]
  > Makinenizde zaten bulunan SDK bağımlılığı sayısına bağlı olarak, SDK’nin yüklenmesi birkaç dakikadan yarım saate uzanacak şekilde biraz uzun sürebilir.
  > 
  > 

Visual Studio 2013’ünüz varsa ve bunu kullanmayı tercih ederseniz, [Visual Studio 2013 için en son Azure SDK'sını indirebilirsiniz](http://go.microsoft.com/fwlink/?LinkID=324322). Bazı ekranlar gösterilenlerden farklı görünebilir.

## <a name="create-a-web-application"></a>Web uygulaması oluşturma
Sonraki adımınız, Visual Studio'da bir web uygulaması projesi ve Azure App Service'te bir web uygulaması oluşturmaktır. Öğreticinin bu bölümünde, yeni web projesini yapılandırın. 

1. Visual Studio 2015’i açın.
2. **Dosya > Yeni > Proje**’ye tıklayın.
3. **Yeni Proje** iletişim kutusunda **Visual C# > Web > ASP.NET Web Uygulaması**’na tıklayın.
4. **.NET Framework 4.5.2**’nin hedef çerçeve olarak seçildiğinden emin olun.
5. [Azure Application Insights](../application-insights/app-insights-overview.md) web uygulamanızı kullanılabilir, performans ve kullanım bakımından izler. Visual Studio’yu yükledikten sonra ilk defa bir web projesi oluşturduğunuzda **Projeye Application Insights Ekle** onay kutusu varsayılan olarak seçilidir. Application Insights’ı denemek istemiyorsanız, seçili olması halinde bu onay kutusunu temizleyin.
6. **Örneğim** uygulamasını adlandırın ve **Tamam**’a tıklayın.
   
    ![Yeni Proje iletişim kutusu](./media/web-sites-dotnet-get-started/GS13newprojdb.png)
7. **Yeni ASP.NET Projesi** iletişim kutusunda, **MVC** şablonunu seçin ve ardından **Kimlik Doğrulamayı Değiştir**’e tıklayın.
   
    Bu öğreticide, bir ASP.NET MVC web projesi dağıtırsınız. Bir ASP.NET Web API projesini nasıl dağıtacağınızı öğrenmek isterseniz, [Sonraki adımlar](#next-steps) bölümüne bakın. 
   
    ![Yeni ASP.NET Projesi iletişim kutusu](./media/web-sites-dotnet-get-started/GS13changeauth.png)
8. **Kimlik Doğrulamayı Değiştir** iletişim kutusunda **Kimlik Doğrulama Yok**’a ve ardından **Tamam**’a tıklayın.
   
    ![Kimlik Doğrulaması Yok](./media/web-sites-dotnet-get-started/GS13noauth.png)
   
    Bu bir başlangıç öğreticisi olduğundan, kullanıcının oturum açmasını gerektirmeyen basit bir uygulama dağıtıyorsunuz.
9. **Yeni ASP.NET Projesi** iletişim kutusunun **Microsoft Azure** bölümünde **Bulutta barındır** seçeneğinin belirlendiğinden ve açılır listesinde bu **App Service**’in seçili olduğundan emin olun.
   
    ![Yeni ASP.NET Projesi iletişim kutusu](./media/web-sites-dotnet-get-started/GS13newaspnetprojdb.png)
   
    Bu ayarlar, Visual Studio’yu web projeniz için bir Azure web uygulaması oluşturmak üzere yönlendirir.
10. **Tamam**’a tıklayın.

## <a name="create-the-azure-resources"></a>Azure kaynaklarını oluşturma
Artık oluşturmak istediğiniz Azure kaynakları için Visual Studio’da işlem yapmaya başlayabilirsiniz.

1. **App Service Oluştur** iletişim kutusunda **Hesap ekle**’ye tıklayın ve ardından Azure aboneliğinizi yönetmek üzere kullandığınız hesabın kimliği ve parolası ile Azure’da oturum açın.
   
    ![Azure'da oturum açma](./media/web-sites-dotnet-get-started/configuresitesettings.png)
   
    Aynı bilgisayarda daha önce oturum açtıysanız, **Hesap ekle** düğmesini görmemiş olabilirsiniz. Bu durumda, bu adımı atlayabilirsiniz. Aksi takdirde, kimlik bilgilerinizi yeniden girmeniz gerekebilir.
2. *azurewebsites.net* etki alanında benzersiz bir **Web Uygulaması Adı** girin. Örneğin, Örneğim uygulamasını sağa doğru sayılarla (örn. Örneğim810) benzersiz şekilde adlandırabilirsiniz. Sizin için varsayılan bir web adı oluşturulmuşsa, bu ad benzersiz olur. Dolayısıyla, bu adı kullanabilirsiniz.
   
    Girdiğiniz adı zaten başka bir kullanıyorsa, sağ tarafında yeşil onay işareti yerine kırmızı bir ünlem işaret görünür. Ardından, farklı bir ad girmeniz gerekir.
   
    Uygulamanızın URL’si bu adın ve *.azurewebsites.net* ifadesinin yan yana gelmesiyle oluşur. Örneğin, uygulamanızın adı `MyExample810` ise URL `myexample810.azurewebsites.net` olur.
   
    Azure web uygulaması ile özel bir etki alanı da kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure App Service’te özel bir etki alanı adı yapılandırma](web-sites-custom-domain-name.md).
3. **Kaynak Grubu** kutusunun yanında bulunan **Yeni** düğmesine tıklayın ve ardından "Örneğin" ifadesini veya tercih ettiğiniz başka bir adı girin. 
   
    ![App Service iletişim kutusu oluşturma](./media/web-sites-dotnet-get-started/rgcreate.png)
   
    Kaynak grubu web uygulamaları, veritabanları ve sanal makineler gibi Azure kaynakları koleksiyonudur. Bir öğreticide, en iyi uygulama yeni bir kaynak grubu oluşturmaktır. Bu sayede, öğretici için oluşturduğunuz Azure kaynaklarını tek bir adımda kolayca silebilirsiniz. Daha fazla bilgi için bkz. [Azure Resource Manager’a genel bakış](../azure-resource-manager/resource-group-overview.md).
4. **App Service Planı** açılır menüsünün yanındaki **Yeni** düğmesine tıklayın.
   
    ![App Service iletişim kutusu oluşturma](./media/web-sites-dotnet-get-started/createasplan.png)
   
    **App Service Planı Yapılandır** iletişim kutusu görüntülenir.
   
    ![App Service’i Yapılandır iletişim kutusu](./media/web-sites-dotnet-get-started/configasp.png)
   
    Aşağıdaki adımlarda, yeni kaynak grubu için bir App Service planı yapılandırırsınız. App Service planı, web uygulamanızın üzerinde çalışacağı işlem kaynaklarını belirtir. Örneğin, ücretsiz katmanı seçerseniz API uygulamanız paylaşılan sanal makineler üzerinde çalışır, bazı ücretli katmanlarda ise ayrılmış sanal makineler üzerinde çalışır. Daha fazla bilgi için bkz. [App Service planlarına genel bakış](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
5. **App Service Planı Yapılandır** iletişim kutusunda, "ÖrnekPlanım" ifadesini veya tercih ettiğiniz başka bir adı girin.
6. **Konum** açılır listesinde, size en yakın konumu seçin.
   
    Bu ayar, uygulamanızın hangi Azure veri merkezinde çalıştırılacağını belirtir. Bu öğretici için herhangi bir bölgeyi seçebilirsiniz, gözle görülür bir fark yaratmayacaktır. Bununla birlikte bir üretim uygulaması için [gecikmeyi](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090) en aza indirmek amacıyla sunucunuzun eriştiği istemcilere mümkün olduğunca yakın olmasını sağlayabilirsiniz.
7. **Boyut** açılır menüsünde **Ücretsiz**’e tıklayın.
   
    Bu öğretici için ücretsiz fiyatlandırma katmanı yeterli ölçüde performans sunacaktır.
8. **App Service Planı Yapılandır** iletişim kutusunda **Tamam**’a tıklayın.
9. **App Service Oluştur** iletişim kutusunda **Oluştur**’a tıklayın.

## <a name="inspect-the-azure-resources-in-visual-studio"></a>Visual Studio'daki Azure kaynaklarını inceleme
Kısa bir süre içinde (genellikle bir dakikadan az) Visual Studio web projesini ve web uygulamasını oluşturur.  

**Çözüm Gezgini** penceresi, yeni projedeki dosyaları ve klasörleri gösterir.

![Çözüm Gezgini](./media/web-sites-dotnet-get-started/solutionexplorer.png)

**Azure App Service Etkinliği** penceresi, App Service kaynaklarının Azure'da oluşturulduğunu gösterir. Yeni projenizi hemen yayımlamaya başlamak için buradaki bağlantıya tıklayabilirsiniz. Bu öğreticinin ilerleyen bölümlerinde dosyalarınızı dilediğiniz zaman nasıl yayımlayabileceğiniz gösterilmektedir.

![Azure App Service Etkinliği penceresindeki oluşturulan web uygulaması](./media/web-sites-dotnet-get-started/GS13sitecreated1.png)

**Cloud Explorer** penceresi, henüz oluşturduğunuz yeni web uygulaması da dahil olmak üzere Azure kaynaklarınızı görüntülemenize ve yönetmenize olanak tanır.

![Cloud Explorer’da oluşturulan web uygulaması](./media/web-sites-dotnet-get-started/siteinse.png)

## <a name="deploy-the-web-project-to-azure"></a>Web projesini Azure'a dağıtma
Bu bölümde web projesini Azure App Service'te oluşturduğunuz web uygulaması kaynağına dağıtacaksınız.

1. **Çözüm Gezgini**’nde, projeye sağ tıklayın ve **Yayımla**’yı seçin.
   
    ![Visual Studio menüsünde Yayımla'yı seçme](./media/web-sites-dotnet-get-started/choosepublish.png)
   
    Birkaç saniye içinde, **Web'i Yayımla** sihirbazı görüntülenir. Sihirbaz, web projesini yeni web uygulamasına dağıtma ayarlarını içeren *profili yayımlamak* için açılır.
   
    > [!TIP] 
    > Yayımlama profili, dağıtım için bir kullanıcı adı ve parolası içerir.  Bu kimlik bilgileri sizin için oluşturulmuştur, bu nedenle bunları girmeniz gerekmez. Parola `Properties\PublishProfiles` klasöründe bulunan gizli bir kullanıcıya özel dosyada şifrelenmiştir.
    >
    >
2. **Web’i Yayımla** sihirbazının **Bağlantı** sekmesinde **Sonraki**’ne tıklayın.
   
    ![Web'i Yayımla sihirbazının Bağlantı sekmesinde İleri'ye tıklama](./media/web-sites-dotnet-get-started/GS13ValidateConnection.png)
   
    Bir sonraki sekme **Ayarlar** sekmesidir. Burada, [uzaktan hata ayıklama](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug) için hata ayıklama derlemesi dağıtmak üzere derleme yapılandırmasını değiştirebilirsiniz. Ayrıca, bu sekmede birkaç farklı [Dosya Yayımlama Seçeneği](https://msdn.microsoft.com/library/dd465337.aspx#Anchor_2) bulunur.
3. **Ayarlar** sekmesinde **Sonraki**’ne tıklayın.
   
   ![Web’i Yayımla sihirbazının Ayarlar sekmesi](./media/web-sites-dotnet-get-started/GS13SettingsTab.png)
   
   Sonrasında **Önizleme** sekmesi gelir. Burada, hangi dosyaların projenizden API uygulamasına kopyalanacağını görme fırsatınız olur. Daha önce dağıttığınız bir API uygulamasına bir proje dağıtıyorsanız, yalnızca değiştirilen dosyalar kopyalanır. Kopyalanacak dosyaların bir listesi görmek isterseniz, **Önizlemeyi Başlat** düğmesine tıklayabilirsiniz.
4. **Önizleme** sekmesinde **Yayımla**’ya tıklayın.
   
   ![Web’i Yayımla sihirbazının Önizleme sekmesi](./media/web-sites-dotnet-get-started/GS13previewoutput.png)
   
   **Yayımla**’ya tıkladığınızda Visual Studio dosyaların Azure sunucusuna kopyalanması işlemine başlar. Bu işlem bir veya iki dakika sürebilir.
   
   **Çıkış** ve **Azure App Service Etkinliği** pencereleri hangi dağıtım eylemlerinin gerçekleştirildiğini gösterir ve dağıtımın başarılı şekilde tamamlandığını bildirir.
   
   ![Dağıtımın başarılı olduğunu bildiren Visual Studio Çıkış penceresi ](./media/web-sites-dotnet-get-started/PublishOutput.png)
   
   Dağıtım başarılı şekilde gerçekleştirildikten sonra, varsayılan tarayıcı otomatik olarak dağıtılan web uygulamasının URL’sini açar. Böylece, oluşturduğunuz uygulama bulutta çalışmaya başlar. Tarayıcı adres çubuğundaki URL web uygulamasının İnternet’ten yüklendiğini gösterir.
   
   ![Azure’da çalışan web uygulaması](./media/web-sites-dotnet-get-started/GS13deployedsite.png)
   
   > [!TIP]
   > Hızlı dağıtım için **Web Tek Tık Yayımla** araç çubuğunu etkinleştirebilirsiniz. **Görünüm > Araç Çubukları**’na tıklayın ve **Web Tek Tık Yayımla**’yı seçin. Bir profil seçmek için araç çubuğunu kullanabilirsiniz. Yayımlamak için bir düğmeye veya **Web’i Yayımla** sihirbazını açmak için bir düğmeye tıklayın.
   > ![Web Tek Tık Yayımla Araç Çubuğu](./media/web-sites-dotnet-get-started/weboneclickpublish.png)
   > 
   > 

## <a name="troubleshooting"></a>Sorun giderme
Bu öğreticide ilerlerken bir sorunla karşılaşırsanız, .NET için Azure SDK’nın en son sürümünü kullandığınızdan emin olun. Bunu yapmanın en kolay yolu [Visual Studio 2015 için Azure SDK’yı indirmektir](http://go.microsoft.com/fwlink/?linkid=518003). Geçerli sürüm yüklüyse, Web Platformu Yükleyicisi yükleme yapılmasına gerek olmadığını belirtir.

Azure App Service’te ASP.NET web uygulamanızı çalıştırdıktan sonra, sorun giderme sürecini kolaylaştıracak Visual Studio özellikler hakkında daha fazla bilgi edinmek isteyebilirsiniz. Günlüğe kaydetme, uzaktan hata ayıklama ve çok daha fazlası hakkında bilgi edinmek için bkz. [Visual Studio’daki Azure Web Apps sorunlarını giderme](web-sites-dotnet-troubleshoot-visual-studio.md).

## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, basit bir web uygulaması oluşturmayı ve bunu Azure web uygulamasında dağıtmayı öğrendiniz. Azure App Service hakkında daha fazla bilgi edinmek için şu konulara ve kaynaklara göz atabilirsiniz:

* [Azure portalında](https://portal.azure.com/) web uygulamanızı izleme ve yönetme. 
  
    Daha fazla bilgi için bkz. [Azure portalına genel bakış](/services/management-portal/) ve [Azure App Service’te web uygulamalarını yapılandırma](web-sites-configure.md).
* Visual Studio’yu kullanarak mevcut bir web projesini yeni bir web uygulamasına dağıtma
  
    **Çözüm Gezgini**’nde projeye sağ tıklayın ve ardından **Yayımla**’ya tıklayın. **Microsoft Azure App Service**’i yayımlama hedefi olarak seçin ve ardından **Yeni**’ye tıklayın. Bu öğreticide gördüklerinizle aynı iletişim kutuları görüntülenir.
* Kaynak denetiminden bir web projesi dağıtma
  
    Bir [kaynak denetimi sisteminden](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control) [dağıtımı otomatikleştirme](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) hakkında bilgi edinmek için bkz. [Azure App Service’te web uygulamalarını kullanmaya başlama](app-service-web-get-started.md) ve [Azure web uygulaması dağıtma](web-sites-deploy.md).
* ASP.NET Web API’sini Azure App Service’teki API uygulamasına dağıtma
  
    Genel olarak web sitesi barındırma amaçlı olan bir Azure App Service örneği oluşturmayı öğrendiniz. App Service, CORS desteği ve istemci kodu oluşturmak için API meta verileri desteği gibi Web API’lerini barındırmak için özellikler de sunar. Bir web uygulamasındaki API özelliklerini kullanabilirsiniz. Ancak, genel olarak App Service örneğinde bir API barındırmak istiyorsanız, **API uygulaması** daha uygun bir seçenektir. Daha fazla bilgi için bkz. [Azure App Service’te API Apps’i ve ASP.NET’i kullanmaya başlama](../app-service-api/app-service-api-dotnet-get-started.md). 
* Özel etki alanı adı ve SSL ekleme
  
    SSL’yi ve kendi etki alanınızı (örneğin, contoso.azurewebsites.net yerine www.contoso.com) kullanma hakkında daha fazla bilgi edinmek için aşağıdaki kaynaklara göz atın:
  
  * [Azure App Service'te özel etki alanı adını yapılandırma](web-sites-custom-domain-name.md)
  * [Azure web sitesi için HTTPS'yi etkinleştirme](web-sites-configure-ssl-certificate.md)
* İşiniz bittiğinde, web uygulamanızı ve ilgili Azure kaynaklarını içeren kaynak grubunu silin.
  
    Azure portalında kaynak gruplarıyla çalışma hakkında bilgi edinmek için bkz. [Resource Manager şablonları ve Azure portalı ile kaynakları dağıtma ve yönetme](../azure-resource-manager/resource-group-template-deploy-portal.md).   
* App Service’te ASP.NET Web Uygulaması oluşturmaya yönelik daha fazla örnek için [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 Connect [tanıtımı](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/) içindeki [Azure Uygulama Hizmeti’nde ASP.NET web uygulaması oluşturma ve dağıtma](https://github.com/Microsoft/HealthClinic.biz/wiki/Create-and-deploy-an-ASP.NET-web-app-in-Azure-App-Service) ile [Azure Uygulama Hizmeti’nde mobil uygulama oluşturma ve dağıtma](https://github.com/Microsoft/HealthClinic.biz/wiki/Create-and-deploy-a-mobile-app-in-Azure-App-Service) bölümlerine bakın. HealthClinic.biz tanıtımından daha fazla hızlı başlangıç ipuçları için bkz. [Azure Geliştirici Araçları Hızlı Başlangıç İpuçları](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).


