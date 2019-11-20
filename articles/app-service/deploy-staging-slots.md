---
title: Azure App Service Web Apps için hazırlama ortamlarını ayarlama | Microsoft Docs
description: Azure App Service 'de Web uygulamaları için hazırlanmış yayımlamayı nasıl kullanacağınızı öğrenin.
services: app-service
documentationcenter: ''
author: cephalin
writer: cephalin
manager: jpconnoc
editor: mollybos
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/19/2019
ms.author: cephalin
ms.openlocfilehash: f9b1af14bd986f1fa6fb5feb398a7f1fdf982f77
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73669104"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Azure App Service 'de hazırlama ortamlarını ayarlama
<a name="Overview"></a>

Web uygulamanızı, Linux 'ta Web uygulamanızı, mobil arka uca veya API uygulamasını [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714)için dağıtırken, **Standart**, **Premium**veya yalıtılmış olarak çalışırken varsayılan üretim yuvası yerine ayrı bir dağıtım yuvası kullanabilirsinizPlan katmanını App Service. Dağıtım yuvaları, kendi ana bilgisayar adlarına sahip canlı uygulamalardır. Uygulama içeriği ve yapılandırma öğeleri, üretim yuvası dahil olmak üzere iki dağıtım yuvası arasında değiştirilebilir. 

Uygulamanızı üretim dışı bir yuvaya dağıtmak aşağıdaki avantajlara sahiptir:

* Üretim yuvası ile takas etmeden önce hazırlama dağıtım yuvasındaki uygulama değişikliklerini doğrulayabilirsiniz.
* İlk olarak bir yuvaya bir yuva dağıtmak ve bunu üretime dönüştürmek, yuvanın tüm örneklerinin üretime değiştirilmeden önce bir bütün olarak daha fazla olduğundan emin olur. Bu, uygulamanızı dağıtırken kapalı kalma süresini ortadan kaldırır. Trafik yeniden yönlendirmesi sorunsuz ve değiştirme işlemleri nedeniyle hiçbir istek atılamaz. Ön değiştirme doğrulaması gerekli olmadığında [otomatik değiştirme](#Auto-Swap) 'yi yapılandırarak bu bütün iş akışını otomatikleştirebilirsiniz.
* Bir değiştirme işleminden sonra, önceden hazırlanmış uygulamaya sahip yuva artık önceki üretim uygulamasına sahiptir. Üretim yuvasında takas edilen değişiklikler beklenmediği sürece, "bilinen son iyi siteyi" geri almak için aynı değiştirmeyi hemen gerçekleştirebilirsiniz.

Her bir App Service plan katmanı farklı sayıda dağıtım yuvası destekler. Dağıtım yuvalarını kullanmak için ek ücret alınmaz. Uygulamanızın katmanının desteklediği yuva sayısını öğrenmek için bkz. [App Service sınırları](https://docs.microsoft.com/azure/azure-subscription-service-limits#app-service-limits). 

Uygulamanızı farklı bir katmana ölçeklendirmek için, hedef katmanın uygulamanızın zaten kullandığı yuva sayısını desteklediğinden emin olun. Örneğin, uygulamanızda beşten fazla yuva varsa **Standart** katman yalnızca beş dağıtım yuvası desteklediğinden **Standart** katmana ölçeklendiremez. 

<a name="Add"></a>

## <a name="add-a-slot"></a>Yuva Ekle
Birden çok dağıtım yuvası etkinleştirebilmeniz için uygulamanın **Standart**, **Premium**veya **yalıtılmış** katmanda çalışıyor olması gerekir.

1. [Azure Portal](https://portal.azure.com/), uygulamanızın [kaynak sayfasını](../azure-resource-manager/manage-resources-portal.md#manage-resources)açın.

2. Sol bölmede, **yuva ekle** > **dağıtım yuvaları** ' nı seçin.
   
    ![Yeni bir dağıtım yuvası Ekle](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > Uygulama zaten **Standart**, **Premium**veya **yalıtılmış** katmanda değilse, hazırlanan yayımlamayı etkinleştirmek için desteklenen katmanları gösteren bir ileti alırsınız. Bu noktada, devam etmeden önce **Yükselt** ' i seçme ve uygulamanızın **Ölçek** sekmesine gitme seçeneğiniz vardır.
   > 

3. **Yuva Ekle** iletişim kutusunda yuvaya bir ad verin ve bir uygulama yapılandırmasını başka bir dağıtım yuvasından klonlamak isteyip istemediğinizi seçin. Devam etmek için **Ekle** ' yi seçin.
   
    ![Yapılandırma kaynağı](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    Bir yapılandırmayı var olan herhangi bir yuvadan kopyalayabilirsiniz. Klonlanabilen ayarlar uygulama ayarları, bağlantı dizeleri, dil çerçevesi sürümleri, Web yuvaları, HTTP sürümü ve platform bit genişliği içerir.

4. Yuva eklendikten sonra, iletişim kutusunu kapatmak için **Kapat** ' ı seçin. Yeni yuva artık **dağıtım yuvaları** sayfasında gösteriliyor. Varsayılan olarak, **% trafiği** yeni yuva için, tüm müşteri trafiği üretim yuvasına yönlendirilirken 0 olarak ayarlanır.

5. Bu yuvanın kaynak sayfasını açmak için yeni dağıtım yuvasını seçin.
   
    ![Dağıtım yuvası başlığı](./media/web-sites-staged-publishing/StagingTitle.png)

    Hazırlama yuvasında, tıpkı diğer App Service uygulamaları gibi bir yönetim sayfası vardır. Yuvanın yapılandırmasını değiştirebilirsiniz. Yuva adı sayfanın en üstünde gösterilir ve dağıtım yuvasını görüntülemekte olduğunuzu hatırlatır.

6. Yuvanın kaynak sayfasında Uygulama URL 'sini seçin. Dağıtım yuvası kendi ana bilgisayar adına sahiptir ve ayrıca canlı bir uygulamadır. Dağıtım yuvasına genel erişimi sınırlandırmak için bkz. [IP kısıtlamaları Azure App Service](app-service-ip-restrictions.md).

Ayarları farklı bir yuvadan kopyalasanız bile, yeni dağıtım yuvasının içeriği yoktur. Örneğin, [Bu yuvaya git ile yayımlayabilirsiniz](app-service-deploy-local-git.md). Yuvaya farklı bir depo dalından veya farklı bir depodan dağıtım yapabilirsiniz. 

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-a-swap"></a>Değiştirme sırasında ne olur?

### <a name="swap-operation-steps"></a>Değiştirme işlemi adımları

İki yuva (genellikle bir hazırlama yuvasından üretim yuvasına) değiştirdiğinizde App Service, hedef yuvanın kapalı kalma süresi yaşamasını sağlamak için aşağıdakileri yapar:

1. Hedef yuvadan (örneğin, üretim yuvası) kaynak yuvasının tüm örneklerine aşağıdaki ayarları uygulayın: 
    - Varsa, [yuvaya özgü](#which-settings-are-swapped) uygulama ayarları ve bağlantı dizeleri.
    - Etkinse, [sürekli dağıtım](deploy-continuous-deployment.md) ayarları.
    - Etkinleştirilirse, [kimlik doğrulama ayarları App Service](overview-authentication-authorization.md) .
    
    Bu durumların herhangi biri kaynak yuvadaki tüm örnekleri yeniden başlatılacak şekilde tetikler. [Önizleme ile değiştirme](#Multi-Phase)sırasında, bu ilk aşamanın sonunu işaretler. Değiştirme işlemi duraklatılır ve kaynak yuvasının hedef yuvasının ayarlarıyla doğru şekilde çalışıp çalışmadığını doğrulayabilirsiniz.

1. Kaynak yuvadaki her örnek için yeniden başlatma işleminin tamamlanmasını bekleyin. Herhangi bir örnek yeniden başlatılamazsa, değiştirme işlemi kaynak yuvada tüm değişiklikleri geri alır ve işlemi sonlandırır.

1. [Yerel önbellek](overview-local-cache.md) etkinse, kaynak yuvasının her örneğindeki uygulama köküne ("/") bir http isteği getirerek yerel önbellek başlatmayı tetikleyin. Her örnek herhangi bir HTTP yanıtı döndürene kadar bekleyin. Yerel önbellek başlatma, her örnekte başka bir yeniden başlatmaya neden olur.

1. [Otomatik takas](#Auto-Swap) [özel ısınma](#Warm-up)ile etkinleştirilirse, kaynak yuvasının her örneğindeki uygulama köküne ("/") bir http isteği getirerek [uygulama başlatma](https://docs.microsoft.com/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) tetikleyin.

    `applicationInitialization` belirtilmemişse, her örnekteki kaynak yuvasının uygulama köküne bir HTTP isteği tetikleyin. 
    
    Bir örnek herhangi bir HTTP yanıtı döndürürse, bu, çarpımış olarak kabul edilir.

1. Kaynak yuvasındaki tüm örnekler başarıyla çarpıtlarsa iki yuva için yönlendirme kurallarını değiştirerek iki yuvayı değiştirin. Bu adımdan sonra, hedef yuva (örneğin, üretim yuvası), kaynak yuvada daha önce çarpımış olan uygulamaya sahiptir.

1. Artık kaynak yuvasının hedef yuvada daha önce takas öncesi uygulamasına sahip olduğuna göre, tüm ayarları uygulayıp örnekleri yeniden başlatarak aynı işlemi gerçekleştirin.

Değiştirme işleminin herhangi bir noktasında, takas edilen uygulamaları başlatma işleminin hepsi kaynak yuvada gerçekleşir. Kaynak yuva hazırlanmakta olduğu veya başarısız olmasına bakılmaksızın, hedef yuva çevrimiçi kalır. Bir hazırlama yuvasını üretim yuvası ile değiştirmek için, üretim yuvasının her zaman hedef yuva olduğundan emin olun. Bu şekilde, değiştirme işlemi üretim uygulamanızı etkilemez.

### <a name="which-settings-are-swapped"></a>Hangi ayarları değiştirmiş?

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

Bir uygulama ayarını veya bağlantı dizesini belirli bir yuvaya (takas değil) eklemek üzere yapılandırmak için, Bu yuvanın **yapılandırma** sayfasına gidin. Bir ayar ekleyin veya düzenleyin ve ardından **dağıtım yuvası ayarı**' nı seçin. Bu onay kutusunun belirlenmesi, ayarın değiştirilebilir olmadığını App Service söyler. 

![Yuva ayarı](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>İki yuva değiştirme 
Dağıtım yuvalarını uygulamanızın **dağıtım yuvaları** sayfasında ve **genel bakış** sayfasında takas edebilirsiniz. Yuva takası hakkında teknik ayrıntılar için bkz. [değiştirme sırasında ne olur](#AboutConfiguration).

> [!IMPORTANT]
> Bir uygulamayı bir dağıtım yuvasından üretime değiştirmeden önce üretime ait hedef yuvalarınızın olduğundan ve kaynak yuvadaki tüm ayarların tamamen üretimde olmasını istediğiniz şekilde yapılandırıldığından emin olun.
> 
> 

Dağıtım yuvalarını değiştirmek için:

1. Uygulamanızın **dağıtım yuvaları** sayfasına gidin ve **takas**' ı seçin.
   
    ![Takas düğmesi](./media/web-sites-staged-publishing/SwapButtonBar.png)

    **Takas** iletişim kutusu, seçilen kaynak ve hedef yuvalarda değiştirilecek ayarları gösterir.

2. İstenen **kaynak** ve **hedef** yuvaları seçin. Genellikle hedef, üretim yuvadır. Ayrıca, **kaynak değişiklikleri** ve **hedef değişiklikler** sekmelerini seçip yapılandırma değişikliklerinin beklendiğini doğrulayın. İşiniz bittiğinde, **Değiştir**' i seçerek yuvaları hemen takas edebilirsiniz.

    ![Takası tamamlama](./media/web-sites-staged-publishing/SwapImmediately.png)

    Değiştirme işleminin gerçekten gerçekleşmeden önce hedef yuvalarınızın yeni ayarlarla nasıl çalışacağını görmek için **değiştirme**' yi seçmeyin, ancak [Önizleme ile değiştirme](#Multi-Phase)' deki yönergeleri izleyin.

3. İşiniz bittiğinde, **Kapat**' ı seçerek iletişim kutusunu kapatın.

Herhangi bir sorununuz varsa bkz. değişiklikleri [giderme](#troubleshoot-swaps).

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>Önizleme ile değiştirme (çok aşamalı takas)

Hedef yuva olarak üretime geçiş yapmadan önce, uygulamanın takas edilen ayarlarla çalıştığını doğrulayın. Kaynak yuva Ayrıca, değiştirme tamamlanmadan önce, görev açısından kritik uygulamalar için istenen şekilde daha da çarpmış olur.

Önizleme ile bir değiştirme gerçekleştirdiğinizde, App Service aynı [değiştirme işlemini](#AboutConfiguration) gerçekleştirir, ancak ilk adımdan sonra duraklatılır. Daha sonra, değiştirmeyi tamamlamadan önce hazırlama yuvasındaki sonucu doğrulayabilirsiniz. 

Değiştirmeyi iptal ederseniz, App Service yapılandırma öğelerini kaynak yuvaya yeniden uygular.

Önizleme ile değiştirmek için:

1. [Takas dağıtım yuvalarında](#Swap) bulunan adımları izleyin ancak **Önizleme ile değiştirme gerçekleştir**' i seçin.

    ![Önizleme ile değiştirme](./media/web-sites-staged-publishing/SwapWithPreview.png)

    İletişim kutusunda kaynak yuvadaki yapılandırmanın 1. aşamada nasıl değiştiği ve kaynak ve hedef yuvanın aşama 2 ' de nasıl değiştirileceği gösterilir.

2. Değiştirmeyi başlatmaya hazırsanız **değiştirmeyi Başlat**' ı seçin.

    1\. aşama tamamlandığında, iletişim kutusunda bilgilendirilirsiniz. `https://<app_name>-<source-slot-name>.azurewebsites.net`giderek, kaynak yuvadaki takası önizleyin. 

3. Bekleyen değiştirme işlemini tamamlamaya hazırsanız değiştirme **eyleminde** **değiştirmeyi Tamam** ' ı seçin ve **değiştirmeyi Tamam**' ı seçin.

    Bekleyen bir değişikliği iptal etmek için yerine **değiştirmeyi Iptal et** ' i seçin.

4. İşiniz bittiğinde, **Kapat**' ı seçerek iletişim kutusunu kapatın.

Herhangi bir sorununuz varsa bkz. değişiklikleri [giderme](#troubleshoot-swaps).

Çok aşamalı bir değiştirme işlemini otomatikleştirmek için bkz. [PowerShell Ile otomatikleştirin](#automate-with-powershell).

<a name="Rollback"></a>

## <a name="roll-back-a-swap"></a>Değiştirme geri alma
Bir yuva takası sonrasında hedef yuvada (örneğin, üretim yuvası) herhangi bir hata oluşursa, aynı iki yuvayı hemen değiştirerek Yuvaları ön değiştirme durumlarına geri yükleyin.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Otomatik değiştirmeyi yapılandırma

> [!NOTE]
> Linux üzerinde Web Apps 'te otomatik takas desteklenmez.

Otomatik takas, uygulamanızı sıfır soğuk başlar ve uygulamanın müşterileri için sıfır kapalı kalma süresiyle sürekli olarak dağıtmak istediğiniz Azure DevOps senaryolarını kolaylaştırır. Otomatik takas bir yuvadan üretime etkin hale geldiğinde, kod değişikliklerinizi bu yuvaya her gönderdiğinizde, App Service, kaynak yuvada bir süre geçtikten sonra [uygulamayı otomatik olarak üretime dönüştürür](#swap-operation-steps) .

   > [!NOTE]
   > Üretim yuvası için otomatik değiştirmeyi yapılandırmadan önce, bir üretim dışı hedef yuvasında otomatik değiştirmeyi test etmeyi göz önünde bulundurun.
   > 

Otomatik değiştirmeyi yapılandırmak için:

1. Uygulamanızın kaynak sayfasına gidin. *\<istenen kaynak yuvası >*  > **yapılandırma** > **genel ayarları** > **dağıtım yuvaları** ' nı seçin.
   
2. **Otomatik takas etkin**için **Açık**seçeneğini belirleyin. Ardından, **Otomatik takas dağıtım yuvası**için istenen hedef yuvayı seçin ve komut çubuğunda **Kaydet** ' i seçin. 
   
    ![Otomatik değiştirmeyi yapılandırmaya yönelik seçimler](./media/web-sites-staged-publishing/AutoSwap02.png)

3. Kaynak yuvaya bir kod gönderimi yürütün. Otomatik değiştirme kısa bir süre sonra gerçekleşir ve güncelleştirme, hedef yuvaınızın URL 'sine yansıtılır.

Herhangi bir sorununuz varsa bkz. değişiklikleri [giderme](#troubleshoot-swaps).

<a name="Warm-up"></a>

## <a name="specify-custom-warm-up"></a>Özel ısınma belirtin

Bazı uygulamalar, değiştirme işleminden önce özel ısınma eylemleri gerektirebilir. Web. config dosyasındaki `applicationInitialization` Configuration öğesi özel başlatma eylemleri belirtmenize izin verir. [Takas işlemi](#AboutConfiguration) , hedef yuva ile takas etmeden önce bu özel ısınma işleminin bitmesini bekler. Örnek bir Web. config parçası aşağıda verilmiştir.

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostName="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

`applicationInitialization` öğesinin özelleştirilmesi hakkında daha fazla bilgi için bkz. [en yaygın dağıtım yuvası değiştirme hataları ve bunların nasıl düzeltileceğini öğrenin](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/).

Ayrıca, aşağıdaki [uygulama ayarlarından](configure-common.md)biri veya her ikisiyle de ısınma davranışını özelleştirebilirsiniz:

- `WEBSITE_SWAP_WARMUP_PING_PATH`: sitenizi ısınma için ping yapılacak yol. Değer olarak eğik çizgiyle başlayan özel bir yol belirterek bu uygulama ayarını ekleyin. `/statuscheck` bunun bir örneğidir. Varsayılan değer `/` ' dır. 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`: ısınma işlemi için geçerli HTTP yanıt kodları. Bu uygulama ayarını, virgülle ayrılmış bir HTTP kodları listesi ile ekleyin. Örnek `200,202`. Döndürülen durum kodu listede yoksa, ısınma ve takas işlemleri durdurulur. Varsayılan olarak, tüm yanıt kodları geçerlidir.

> [!NOTE]
> `<applicationInitialization>` yapılandırma öğesi her uygulamanın bir parçasıdır, ancak iki ısınma davranışı uygulama ayarları yalnızca yuva takas için geçerlidir.

Herhangi bir sorununuz varsa bkz. değişiklikleri [giderme](#troubleshoot-swaps).

## <a name="monitor-a-swap"></a>Değiştirme izleme

[Değiştirme işleminin](#AboutConfiguration) tamamlanmasını uzun sürerse, [etkinlik günlüğündeki](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)değiştirme işlemi hakkında bilgi edinebilirsiniz.

Portalın kaynak sayfasında, sol bölmede **etkinlik günlüğü**' nü seçin.

Günlük sorgusunda `Swap Web App Slots`olarak bir değiştirme işlemi görüntülenir. Bu öğeyi genişletebilir ve ayrıntılarını görmek için alt perations veya hatalardan birini seçebilirsiniz.

## <a name="route-traffic"></a>Trafiği yönlendirme

Varsayılan olarak, uygulamanın üretim URL 'sine (`http://<app_name>.azurewebsites.net`) tüm istemci istekleri üretim yuvasına yönlendirilir. Trafiğin bir bölümünü başka bir yuvaya yönlendirebilirsiniz. Bu özellik, yeni bir güncelleştirme için Kullanıcı geri bildirimlerine ihtiyacınız varsa, ancak bunu üretime serbest bırakmaya hazırsanız faydalıdır.

### <a name="route-production-traffic-automatically"></a>Üretim trafiğini otomatik olarak yönlendir

Üretim trafiğini otomatik olarak yönlendirmek için:

1. Uygulamanızın kaynak sayfasına gidin ve **dağıtım yuvaları**' nı seçin.

2. Yönlendirmek istediğiniz yuvanın **trafik%** sütununda, yönlendirmek istediğiniz toplam trafik miktarını göstermek için bir yüzde (0 ile 100 arasında) belirtin. **Kaydet**’i seçin.

    ![Trafik yüzdesini ayarlama](./media/web-sites-staged-publishing/RouteTraffic.png)

Ayar kaydedildikten sonra, belirtilen istemci yüzdesi, bir üretim dışı yuvaya rastgele yönlendirilir. 

İstemci belirli bir yuvaya otomatik olarak yönlendirildikten sonra, o istemci oturumunun ömrü için bu yuvaya "sabitlenmiş" olur. İstemci tarayıcısında, HTTP başlıklarınızın `x-ms-routing-name` tanımlama bilgisine bakarak oturumunuzun hangi yuvaya sabitlendiği hakkında bilgi alabilirsiniz. "Hazırlama" yuvasına yönlendirilen bir istek tanımlama bilgisine sahiptir `x-ms-routing-name=staging`. Üretim yuvasına yönlendirilen bir istek tanımlama bilgisine sahiptir `x-ms-routing-name=self`.

### <a name="route-production-traffic-manually"></a>Üretim trafiğini el ile yönlendirin

Otomatik Trafik yönlendirmeye ek olarak, App Service istekleri belirli bir yuvaya yönlendirebilir. Bu, kullanıcılarınızın beta uygulamanızı kabul edebilir veya devre dışı bırakmak istediğinizde faydalıdır. Üretim trafiğini el ile yönlendirmek için `x-ms-routing-name` sorgu parametresini kullanırsınız.

Kullanıcıların beta uygulamanızı geri almasına izin vermek için, örneğin bu bağlantıyı Web sayfanıza yerleştirebilirsiniz:

```HTML
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

`x-ms-routing-name=self` dize, üretim yuvasını belirtir. İstemci tarayıcısı bağlantıya eriştiğinde, üretim yuvasına yönlendirilir. Sonraki tüm istekler, oturumu üretim yuvasına sabiteden `x-ms-routing-name=self` tanımlama bilgisine sahiptir.

Kullanıcıların beta uygulamanızı kabul etmesine izin vermek için, aynı sorgu parametresini üretim dışı yuva adı olarak ayarlayın. Bir örneği aşağıda verilmiştir:

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

Varsayılan olarak, yeni yuvalara gri olarak gösterilen `0%`bir yönlendirme kuralı verilir. Bu değeri açıkça `0%` (siyah metin olarak gösterilmiştir) olarak ayarlarsanız, kullanıcılarınız hazırlama yuvasına `x-ms-routing-name` sorgu parametresini kullanarak el ile erişebilir. Ancak, yönlendirme yüzdesi 0 olarak ayarlandığı için bunlar otomatik olarak yuvaya yönlendirilmez. Bu, iç ekiplerin yuvalarda değişiklikleri test kurmasına izin verirken, hazırlama yuvalarınızı genel olarak "gizleyebileceğiniz", gelişmiş bir senaryodur.

<a name="Delete"></a>

## <a name="delete-a-slot"></a>Yuva silme

Uygulamanızın kaynak sayfasına gidin. > > **Genel Bakış ' ı** *silmek için\<yuvasını* > **dağıtım yuvaları** seçin. Komut çubuğunda **Sil** ' i seçin.  

![Dağıtım yuvasını silme](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>PowerShell ile otomatikleştirme

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure PowerShell, Azure 'u, Azure App Service dağıtım yuvalarını yönetme desteği dahil olmak üzere Windows PowerShell aracılığıyla yönetmek için cmdlet 'ler sağlayan bir modüldür.

Azure PowerShell yükleme ve yapılandırma hakkında bilgi edinmek ve Azure aboneliğinizle Azure PowerShell kimlik doğrulaması yapmak için bkz. [Microsoft Azure PowerShell yükleme ve yapılandırma](/powershell/azure/overview).  

---
### <a name="create-a-web-app"></a>Web uygulaması oluşturma
```powershell
New-AzWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

---
### <a name="create-a-slot"></a>Yuva oluşturma
```powershell
New-AzWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

---
### <a name="initiate-a-swap-with-a-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-the-source-slot"></a>Önizleme ile bir değiştirme başlatın (çok aşamalı takas) ve hedef yuva yapılandırmasını kaynak yuvaya uygulayın
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

---
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-the-source-slot-configuration"></a>Bekleyen bir değiştirmeyi iptal et (gözden geçireni değiştirme) ve kaynak yuva yapılandırmasını geri yükleme
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

---
### <a name="swap-deployment-slots"></a>Takas dağıtım Yuvaları
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Etkinlik günlüğündeki değiştirme olaylarını izleme
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-a-slot"></a>Yuva silme
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

## <a name="automate-with-arm-templates"></a>ARM şablonlarıyla otomatikleştirin

[ARM şablonları](https://docs.microsoft.com/azure/azure-resource-manager/template-deployment-overview) , Azure kaynaklarının dağıtımını ve yapılandırmasını otomatik hale getirmek için kullanılan BILDIRIM temelli JSON dosyalarıdır. ARM şablonları kullanarak yuvaları takas etmek için, *Microsoft. Web/Sites/yuvaları* ve *Microsoft. Web/Sites* kaynaklarında iki özellik ayarlayacaksınız:

- `buildVersion`: Bu, yuvada dağıtılan uygulamanın geçerli sürümünü temsil eden bir dize özelliğidir. Örneğin: "v1", "1.0.0.1" veya "2019-09-20T11:53:25.2887393-07:00".
- `targetBuildVersion`: Bu, yuvanın ne `buildVersion` gerektiğini belirten bir dize özelliğidir. TargetBuildVersion geçerli `buildVersion`eşit değilse, bu işlem, belirtilen `buildVersion`sahip olan yuvayı bularak değiştirme işlemini tetikler.

### <a name="example-arm-template"></a>Örnek ARM şablonu

Aşağıdaki ARM şablonu, hazırlama yuvasının `buildVersion` güncelleştirecek ve üretim yuvasında `targetBuildVersion` ayarlayacaktır. Bu, iki yuvaları takas eder. Şablon zaten "hazırlama" adlı bir tepsisle oluşturulmuş bir WebApp olduğunu varsayar.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "my_site_name": {
            "defaultValue": "SwapAPIDemo",
            "type": "String"
        },
        "sites_buildVersion": {
            "defaultValue": "v1",
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Web/sites/slots",
            "apiVersion": "2018-02-01",
            "name": "[concat(parameters('my_site_name'), '/staging')]",
            "location": "East US",
            "kind": "app",
            "properties": {
                "buildVersion": "[parameters('sites_buildVersion')]"
            }
        },
        {
            "type": "Microsoft.Web/sites",
            "apiVersion": "2018-02-01",
            "name": "[parameters('my_site_name')]",
            "location": "East US",
            "kind": "app",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites/slots', parameters('my_site_name'), 'staging')]"
            ],
            "properties": {
                "targetBuildVersion": "[parameters('sites_buildVersion')]"
            }
        }        
    ]
}
```

Bu ARM şablonu, tekrar tekrar yürütülebileceğini ve yuvaların aynı durumunun ortaya çıkarıdempotent anlamına gelir. İlk yürütmeden sonra, `targetBuildVersion` geçerli `buildVersion`eşleşecek, bu nedenle bir takas tetiklenmeyecektir.

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-the-cli"></a>CLı ile otomatikleştirin

Dağıtım yuvaları için [Azure CLI](https://github.com/Azure/azure-cli) komutları için, bkz. [az WebApp Deployment slot](/cli/azure/webapp/deployment/slot).

## <a name="troubleshoot-swaps"></a>Takas sorunlarını giderme

[Yuva değiştirme](#AboutConfiguration)sırasında herhangi bir hata oluşursa, bu, *D:\home\logfiles\eventlog.xml*dosyasına kaydedilir. Ayrıca uygulamaya özgü hata günlüğüne kaydedilir.

Bazı yaygın değiştirme hataları aşağıda verilmiştir:

- Uygulama köküne yönelik bir HTTP isteği zaman aşımına uğradı. Değiştirme işlemi her HTTP isteği için 90 saniye bekler ve 5 kez yeniden dener. Tüm denemeler zaman aşımına uğradıysanız değiştirme işlemi durdurulur.

- Uygulama içeriği yerel önbellek için belirtilen yerel disk kotasını aştığında yerel önbellek başlatma başarısız olabilir. Daha fazla bilgi için bkz. [yerel önbelleğe genel bakış](overview-local-cache.md).

- [Özel ısınma](#Warm-up)sırasında http istekleri dahili olarak yapılır (dış URL 'ye geçmeden). *Web. config*DOSYASıNDAKI belirli URL yeniden yazma kurallarıyla başarısız olabilir. Örneğin, etki alanı adlarını yeniden yönlendirme veya HTTPS zorlama kuralları, uygulama koduna ulaşmasını önler. Bu sorunu geçici olarak çözmek için, aşağıdaki iki koşulu ekleyerek yeniden yazma kurallarınızı değiştirin:

    ```xml
    <conditions>
      <add input="{WARMUP_REQUEST}" pattern="1" negate="true" />
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Özel bir ısınma olmadan, URL yeniden yazma kuralları yine de HTTP isteklerini engelliyor olabilir. Bu sorunu geçici olarak çözmek için, aşağıdaki koşulu ekleyerek yeniden yazma kurallarınızı değiştirin:

    ```xml
    <conditions>
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Bazı [IP kısıtlama kuralları](app-service-ip-restrictions.md) değiştirme IŞLEMININ uygulamanıza http istekleri göndermesini engelleyebilir. `10.` ve `100.` ile başlayan IPv4 adresi aralıkları, dağıtımınız için dahili olarak kullanılır. Uygulamanıza bağlanmasına izin vermeniz gerekir.

- Yuva değiştirildikten sonra, uygulama beklenmeyen yeniden başlatmalar ile karşılaşabilir. Bunun nedeni, bir değiştirme işleminden sonra, ana bilgisayar adı bağlama yapılandırması eşitlemeden sonra yeniden başlatmalara neden olmaz. Ancak, bazı temel depolama olayları (örneğin, depolama birimi yük devretme işlemleri), bu tutarsızlıkları algılayabilir ve tüm çalışan süreçlerini yeniden başlamaya zorlayabilir. Bu tür yeniden başlatma türlerini en aza indirmek için, *Tüm yuvalarda* [`WEBSITE_ADD_SITENAME_BINDINGS_IN_APPHOST_CONFIG=1` uygulama ayarını](https://github.com/projectkudu/kudu/wiki/Configurable-settings#disable-the-generation-of-bindings-in-applicationhostconfig) ayarlayın. Ancak, bu uygulama ayarı Windows Communication Foundation (WCF) *uygulamalarıyla çalışmaz.*

## <a name="next-steps"></a>Sonraki adımlar
[Üretim dışı yuvalara erişimi engelleyin](app-service-ip-restrictions.md)
