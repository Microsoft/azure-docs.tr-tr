---
title: App Service Ortamını kullanma
description: Yalıtılmış uygulamaları barındırmak için App Service Ortamı nasıl kullanacağınızı öğrenin.
author: ccompy
ms.assetid: 377fce0b-7dea-474a-b64b-7fbe78380554
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: d4cd673b5029d8379a699becd7339a265c787390
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100586411"
---
# <a name="using-an-app-service-environment"></a>App Service Ortamı kullanma

App Service Ortamı (ASE), doğrudan seçtiğiniz bir Azure sanal ağına (VNet) oluşturan Azure App Service tek bir kiracı dağıtımsıdır. Yalnızca bir müşteri tarafından kullanılan bir sistemdir. Ao 'ya dağıtılan uygulamalar, as alt ağına uygulanan ağ özelliklerine tabidir. Bu ağ özelliklerine tabi olması için uygulamalarınızda etkinleştirilmesi gereken ek özellikler yoktur. 

## <a name="create-an-app-in-an-ase"></a>AS 'de uygulama oluşturma

Ao 'da bir uygulama oluşturmak için, normalde bir uygulama oluştururken, ancak birkaç küçük farklılık ile aynı süreci kullanırsınız. Yeni bir App Service planı oluşturduğunuzda:

- Uygulamanızın dağıtılacağı coğrafi bir konum seçmek yerine konumunuz olarak bir AO seçin.
- Ao 'da oluşturulan tüm App Service planları yalnızca yalıtılmış bir v2 fiyatlandırma katmanında yer alabilir.

Ao 'a sahip değilseniz, [App Service ortamı oluşturma][MakeASE]bölümündeki yönergeleri izleyerek bir tane oluşturabilirsiniz.

AS 'de bir uygulama oluşturmak için:

1.   >    >  **Web uygulaması** Web ve mobil kaynak oluştur ' u seçin.

1. Bir abonelik seçin.

1. Yeni bir kaynak grubu için bir ad girin veya **var olanı kullan** ' ı seçin ve açılan listeden birini seçin.

1. Uygulama için bir ad girin. Ao 'da zaten bir App Service planı seçtiyseniz, uygulamanın etki alanı adı Ao 'nun etki alanı adını yansıtır:

    ![AS 'de uygulama oluşturma][1]

1. Yayımlama türü, yığın ve Işletim sisteminizi seçin.

1.  Bölge seçin. Burada, bir önceden var olan v3 App Service Ortamı seçmeniz gerekir.  Uygulama oluşturma sırasında ASEv3 yapamazsınız 

1. AŞIRDE mevcut bir App Service planı seçin veya yeni bir tane oluşturun. Yeni bir uygulama oluşturuyorsanız App Service planınız için istediğiniz boyutu seçin. Uygulamanız için seçebileceğiniz tek SKU, yalıtılmış bir v2 fiyatlandırma SKU 'SU olabilir.

    ![Yalıtılmış v2 fiyatlandırma katmanları][2]

    > [!NOTE]
    > Linux uygulamaları ve Windows uygulamaları aynı App Service planında olamaz, ancak aynı App Service Ortamı olabilir.
    >

1. **İleri ' yi seçin: izleme** uygulamanızda uygulama öngörülerini etkinleştirmek istiyorsanız, oluşturma akışı sırasında burada yapabilirsiniz. 

1.  **İleri ' yi seçin: Etiketler** uygulamaya istediğiniz etiketleri ekleyin  

1. **Gözden geçir + oluştur**' u seçin, bilgilerin doğru olduğundan emin olun ve ardından **Oluştur**' u seçin.

## <a name="how-scale-works"></a>Ölçeklendirmenin çalışması

Her App Service uygulaması bir App Service planında çalışır. App Service ortamlar, App Service planlarını ve App Service planlar uygulamaları tutar. Bir uygulamayı ölçeklendirirseniz, aynı plandaki App Service planı ve tüm uygulamaları da ölçeklendirebilirsiniz.

Bir App Service planını ölçeklendirirseniz, gerekli altyapı otomatik olarak eklenir. Altyapı eklenirken, işlemleri ölçeklendirmek için zaman gecikmesi vardır. Bir App Service planını ölçeklendirirseniz, aynı işletim sistemini ve boyutu isteyen diğer ölçeklendirme işlemleri, ilki tamamlanana kadar bekler. Ölçeklendirme ölçeği işlemi tamamlandıktan sonra, tüm sıraya alınmış istekler aynı anda işlenir. Bir boyut ve işletim sistemi üzerindeki bir ölçeklendirme işlemi, diğer boyut ve işletim sistemi birleşimlerinin ölçeklendirilmesini engellemez. Örneğin, bir Windows I2v2 App Service planını ölçeklendirdiyseniz, söz konusu Ao 'da Windows I2v2 ölçeklendirmeye yönelik diğer tüm istekler, tamamlanana kadar sıraya alınır.   

Çok kiracılı App Service, bir kaynak havuzu bunu destekleyecek şekilde kullanıma hazır olduğundan ölçekleme anında gerçekleşir. Ao 'da böyle bir arabellek yoktur ve kaynaklar ihtiyaya göre ayrılır.

## <a name="app-access"></a>Uygulama erişimi

Bir Ao 'da, uygulama oluşturma için kullanılan etki alanı soneki olur *. &lt; asename &gt; . appserviceenvironment.net*. Ao 'niz _My-Ai_ olarak adlandırılmışsa ve _contoso_ ADLı bir uygulamayı bu Ao 'da barındırdıysanız, bu URL 'lerden ulaşabilirsiniz:

- contoso.my-ase.appserviceenvironment.net
- contoso.scm.my-ase.appserviceenvironment.net

ATıCı oluşturma hakkında daha fazla bilgi için bkz. [Create a App Service ortamı][MakeASE].

SCM URL 'SI, kudu konsoluna erişmek veya Web Dağıtımı kullanarak uygulamanızı yayımlamak için kullanılır. Kudu Konsolu hakkında daha fazla bilgi için bkz. [Azure App Service kudu konsolu][Kudu]. Kudu konsolu, hata ayıklama, dosyaları karşıya yükleme, dosyaları düzenlemeyle ve çok daha fazlası için bir Web Kullanıcı arabirimi sağlar.

### <a name="dns-configuration"></a>DNS yapılandırması 

ATıCı, gelen trafik için özel uç noktaları kullanır. Azure DNS özel bölgelerle otomatik olarak yapılandırılmamıştır. Kendi DNS sunucunuzu kullanmak istiyorsanız, aşağıdaki kayıtları eklemeniz gerekir:

1. &lt;alname. appserviceenvironment.NET için bir bölge oluşturun &gt;
1. Bu bölgede, Ao özel uç noktanız tarafından kullanılan gelen IP adresine * işaret eden bir kayıt oluşturun
1. Bu bölgede, Ao özel uç noktanız tarafından kullanılan gelen IP adresine @ işaret eden bir kayıt oluşturun
1. &lt;Ao Name &gt; . appserviceenvironment.net adlı SCM adlı bir bölge oluşturun
1. SCM bölgesinde, Ao özel uç noktanız tarafından kullanılan IP adresine işaret eden bir kayıt oluşturun

Azure DNS özel bölgelerde DNS 'yi yapılandırmak için:

1. . appserviceenvironment.net adlı bir Azure DNS özel bölge oluşturun <ASE name>
1. Bu bölgede * ıLB IP adresine işaret eden bir kayıt oluşturun
1. Bu bölgede @ adresli ıLB IP adresine işaret eden bir kayıt oluşturma
1. Bu bölgede, ıLB IP adresine *. SCM 'yi işaret eden bir kayıt oluşturun

Ao varsayılan etki alanı son ekinin DNS ayarları, uygulamalarınızı yalnızca bu adlara göre erişilebilir olacak şekilde kısıtlamayın. Bir Ao 'da uygulamalarınızda herhangi bir doğrulama yapmadan özel bir etki alanı adı ayarlayabilirsiniz. Daha sonra *contoso.net* adlı bir bölge oluşturmak istiyorsanız bunu yapabilirsiniz ve gelen IP adresine işaret edebilirsiniz. Özel etki alanı adı, uygulama istekleri için geçerlidir ancak SCM sitesi için değildir. SCM sitesi yalnızca *&lt; appname. SCM adresinde kullanılabilir &gt; . &lt; asename &gt; . appserviceenvironment.net*. 

## <a name="publishing"></a>Yayımlama

Çok kiracılı App Service olduğu gibi, bu yöntemlerle yayımlayabilirsiniz:

- Web dağıtımı
- Sürekli tümleştirme (CI)
- Kudu konsoluna sürükleme ve bırakma
- Visual Studio, tutulma veya IntelliJ fıkrı gibi bir IDE

Ao ile, yayımlama uç noktaları yalnızca özel uç nokta tarafından kullanılan gelen adresle kullanılabilir. Özel uç nokta adresine ağ erişiminiz yoksa, o Ao 'da herhangi bir uygulama yayımlayamazsınız.  Ides 'in doğrudan yayımlayabilmesi için ıLB 'ye ağ erişimi de olmalıdır.

Ek değişiklikler olmadan GitHub ve Azure DevOps gibi internet tabanlı CI sistemleri bir ıLB Ao ile çalışmaz, çünkü yayımlama uç noktası internet 'e erişilebilir değildir. ILB asa 'yı içeren sanal ağa şirket içinde barındırılan bir yayın Aracısı yükleyerek Azure DevOps 'dan bir ıLB Ao 'a yayımlamayı etkinleştirebilirsiniz. 

## <a name="storage"></a>Depolama

ATıCı, Ao 'daki tüm uygulamalar için 1 TB depolama alanına sahiptir. Yalıtılmış fiyatlandırma SKU 'sunda bir App Service planı 250 GB 'lik bir sınıra sahiptir. Bir Ao 'da, App Service plan başına 1 TB sınırına kadar 250 GB depolama alanı eklenir. Yalnızca dörtten fazla App Service planınıza sahip olabilirsiniz ancak 1 TB sınırının ötesinde daha fazla depolama alanı eklenmez.

## <a name="logging"></a>Günlüğe Kaydetme

Aşirinizi Azure depolama, Azure Event Hubs veya Log Analytics ile ilgili günlükleri göndermek için Azure Izleyici ile tümleştirebilirsiniz. Bu öğeler bugün günlüğe kaydedilir:

| Olanını | İleti |
|---------|----------|
| Ao uygun değil | Belirtilen Ao, geçersiz bir sanal ağ yapılandırması nedeniyle uygun değil. Sağlıksız durum devam ederse Ao askıya alınır. Burada tanımlanan yönergelerin izlendiğinden emin olun: https://docs.microsoft.com/azure/app-service/environment/network-info . |
| Ao alt ağında neredeyse yer kalmadı | Belirtilen Ao, neredeyse boş alan olmayan bir alt ağda. {0}Kalan adresler var. Bu adresler tükendiğinde Ao, ölçeklendirmeyecektir.  |
| ATıCı toplam örnek sınırına yaklaşıyor | Belirtilen Ao, Ao 'nun toplam örnek sınırına yaklaşıyor. Şu anda {0} en fazla 201 örnek App Service plan örnekleri içerir. |
| ATıCı bir bağımlılığa ulaşamıyor | Belirtilen Ao, ulaşılamıyor {0} .  Burada tanımlanan yönergelerin izlendiğinden emin olun: https://docs.microsoft.com/azure/app-service/environment/network-info . |
| ATıCı askıya alındı | Belirtilen Ao, askıya alındı. Ao askıya alma, bir hesap kısaltıcı veya geçersiz bir sanal ağ yapılandırmasından kaynaklanıyor olabilir. Ana neden çözümleyin ve trafiğe hizmet vermeye devam etmek için Ao 'yu sürdürün. |
| ALUPGRADE yükseltmesi başlatıldı | Belirtilen asa 'ya bir platform yükseltmesi başladı. Ölçeklendirme işlemlerinde gecikme süreleri bekliyor. |
| ALUPGRADE yükseltmesi tamamlandı | Belirtilen asa 'ya platform yükseltmesi bitti. |
| Ölçeklendirme işlemleri başlatıldı | App Service planı ( {0} ) ölçeklendirmeye başladı. İstenen durum: {1} I {2} çalışanları.
| Ölçeklendirme işlemleri tamamlandı | App Service planı ( {0} ) ölçeklendirmeyi bitirdi. Geçerli durum: {1} I {2} çalışanları. |
| Ölçeklendirme işlemleri başarısız oldu | App Service planı ( {0} ) ölçeklendirilemedi. Geçerli durum: {1} I {2} çalışanları. |

AŞIRDE günlüğe kaydetmeyi etkinleştirmek için:

1. Portalda **Tanılama ayarları**' na gidin.
1. **Tanılama ayarı ekle**’yi seçin.
1. Günlük tümleştirmesi için bir ad sağlayın.
1. İstediğiniz günlük hedeflerini seçin ve yapılandırın.
1. **Appserviceenvironmentplatformlogs**' u seçin.

![ATıCı tanılama günlüğü ayarları][4]

Log Analytics ile tümleştirirseniz, asa portalından **Günlükler** seçerek ve **Appserviceenvironmentplatformlogs**'a yönelik bir sorgu oluşturarak günlükleri görebilirsiniz. Günlükler yalnızca asa 'nın onu tetikleyecek bir olayı varsa yayılır. ASA 'nın böyle bir olayı yoksa, hiçbir günlük olmayacaktır. Log Analytics çalışma alanınızdaki bir günlük örneğini hızlıca görmek için, AŞIRINIZDEKI App Service planlarından biriyle bir ölçeklendirme işlemi gerçekleştirin. Ardından, bu günlükleri görmek için **Appserviceenvironmentplatformlogs** 'a yönelik bir sorgu çalıştırabilirsiniz. 

**Uyarı oluşturma**

Günlüklerinizi karşılaştırarak bir uyarı oluşturmak için [Azure izleyici 'yi kullanarak günlük uyarılarını oluşturma, görüntüleme ve yönetme](../../azure-monitor/alerts/alerts-log.md)bölümündeki yönergeleri izleyin. Kısaca:

* AS portalınızdaki uyarılar sayfasını açın
* **Yeni uyarı kuralı** Seç
* Log Analytics çalışma alanınız olacak kaynağı seçin
* "AppServiceEnvironmentPlatformLogs | gibi bir sorgu kullanmak için koşullarınızı özel bir günlük araması ile ayarlayın ResultDescription "ölçeklendirmeye başladı" veya istediğiniz herhangi bir şey vardır. Eşiği uygun şekilde ayarlayın. 
* İstediğiniz bir eylem grubu ekleyin veya oluşturun. Eylem grubu, bir e-posta veya SMS iletisi gönderme gibi uyarıya yanıtı tanımladığınız yerdir
* Uyarınızı adlandırın ve kaydedin.

## <a name="internal-encryption"></a>İç şifreleme

App Service Ortamı, iç bileşenleri veya sistem içindeki iletişimi görgeçirebileceğiniz bir siyah kutu sistemi olarak çalışır. Daha yüksek işleme sağlamak için, şifreleme, iç bileşenler arasında varsayılan olarak etkinleştirilmemiştir. Trafiğin izlenmesi veya erişilmesi için tamamen erişilemez olduğu için sistem güvenlidir. Veri yolunun uçtan uca şifrelemeye tamamen şifrelenmesini gerektiren bir uyumluluk gereksinimine sahipseniz, bunu ASE **yapılandırma** Kullanıcı arabiriminde etkinleştirebilirsiniz.

![İç şifrelemeyi etkinleştir][5]

Bu, Ao 'da ön uçlar ve çalışanlar arasında iç ağ trafiğini şifreler, disk belleği şifreleyin ve ayrıca çalışan disklerini şifreler. InternalEncryption clusterSetting etkinleştirildikten sonra, sistem Performanslarınızın bir etkisi olabilir. InternalEncryption 'i etkinleştirmek için değişiklik yaptığınızda, Ao 'niz değişiklik tamamen yayılana kadar kararsız bir durumda olacaktır. Ao 'da kaç örneğe sahip olduğunuza bağlı olarak, değişikliğin tamamının yayılması birkaç saat sürebilir. Bu işlemi kullanırken bir AO 'da etkinleştirememeniz önemle tavsiye ederiz. Bunu etkin bir şekilde kullanılan bir AO 'da etkinleştirmeniz gerekiyorsa, işlem tamamlanana kadar trafiği bir yedekleme ortamına eklemeniz önemle tavsiye ederiz.

## <a name="upgrade-preference"></a>Yükseltme tercihi

Birden çok ASE varsa, bazı ASE 'lerin başkalarından önce yükseltilmesini isteyebilirsiniz. ATıCı **HostingEnvironment Kaynak Yöneticisi** nesnesi Içinde, **upgradepreference** için bir değer ayarlayabilirsiniz. **Upgradepreference** ayarı bir şablon, ARMClient veya kullanılarak yapılandırılabilir https://resources.azure.com . Olası üç değer şunlardır:

- **Hiçbiri**: Azure, Ao 'nizi belirli bir toplu iş olmadan yükseltecektir. Bu varsayılan değerdir.
- **Erken**: Ao 'niz App Service yükseltmelerin ilk yarısında yükseltilir.
- **Geç**: Ao 'niz App Service yükseltmelerinin ikinci yarısında yükseltilir.

Yükseltme tercihinizi yapılandırmak için ATıCı **yapılandırma** Kullanıcı arabirimine gidin. 

"Erken" Alarınızın "geç" Atoklarından önce yükseltilemeyeceğinden, **yükseltme tercihleri** özelliği birden çok ASE sahip olduğunuzda en mantıklı şekilde yararlanacaktır. Birden çok ASE 'niz varsa, geliştirme ve test Alarınızı "erken" ve üretim Alarınızın "geç" olacak şekilde ayarlamanız gerekir.

## <a name="delete-an-ase"></a>AS 'yi silme

ATıCı 'yi silmek için:

1. **App Service ortamı** bölmesinin en üstünde **Sil** ' i seçin.

1. Silmek istediğinizi onaylamak için ATıCı 'nizin adını girin. Bir AI 'yi sildiğinizde, içindeki tüm içeriği de silersiniz.

    ![ATıCı silme][3]

1. **Tamam**’ı seçin.

<!--Image references-->
[1]: ./media/using/using-appcreate.png
[2]: ./media/using/using-appcreate-skus.png
[3]: ./media/using/using-delete.png
[4]: ./media/using/using-logsetup.png
[4]: ./media/using/using-logs.png
[5]: ./media/using/using-configuration.png

<!--Links-->
[Intro]: ./overview.md
[MakeASE]: ./creation.md
[ASENetwork]: ./networking.md
[UsingASE]: ./using.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/alerts/alerts-log.md
