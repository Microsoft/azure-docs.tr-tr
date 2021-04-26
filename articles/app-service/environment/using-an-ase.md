---
title: App Service Ortamı kullanma ve yönetme
description: App Service Ortamı uygulama oluşturmayı, yayımlamayı ve ölçeklendirmeyi öğrenin. Bu makaledeki tüm ortak görevleri bulun.
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 9/22/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: a7fa9ece3728214fad31f0bae769e1e50206df7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100594046"
---
# <a name="use-an-app-service-environment"></a>App Service Ortamını kullanma

App Service Ortamı (Ao), bir müşterinin Azure sanal ağ örneğindeki bir alt ağa Azure App Service bir dağıtımdır. Bir asa aşağıdakilerden oluşur:

- **Ön uçlar**: http veya HTTPS bir App Service ortamı sonlandırıldığında
- **Çalışanlar**: uygulamalarınızı barındıran kaynaklar
- **Veritabanı**: ortamı tanımlayan bilgileri barındırır
- **Depolama**: müşteri tarafından yayımlanan uygulamaları barındırmak için kullanılır

Uygulama erişimi için bir dış veya iç sanal IP (VIP) ile Ao 'ı dağıtabilirsiniz. Dış VIP içeren bir dağıtıma genellikle *dış Ao* denir. İç VIP ile bir dağıtım iç yük dengeleyici (ıLB) kullandığından *ıLB Ao* adı verilir. ILB Ao hakkında daha fazla bilgi edinmek için bkz. [ILB Ai oluşturma ve kullanma][MakeILBASE].

## <a name="create-an-app-in-an-ase"></a>AS 'de uygulama oluşturma

Ao 'da bir uygulama oluşturmak için, normalde bir uygulama oluştururken, ancak birkaç küçük farklılık ile aynı süreci kullanırsınız. Yeni bir App Service planı oluşturduğunuzda:

- Uygulamanızın dağıtılacağı coğrafi bir konum seçmek yerine konumunuz olarak bir AO seçin.
- Ao 'da oluşturulan tüm App Service planları yalnızca yalıtılmış bir fiyatlandırma katmanında yer alabilir.

Ao 'a sahip değilseniz, [App Service ortamı oluşturma][MakeExternalASE]bölümündeki yönergeleri izleyerek bir tane oluşturabilirsiniz.

AS 'de bir uygulama oluşturmak için:

1.   >    >  **Web uygulaması** Web ve mobil kaynak oluştur ' u seçin.

1. Uygulama için bir ad girin. Ao 'da zaten bir App Service planı seçtiyseniz, uygulamanın etki alanı adı Ao 'nun etki alanı adını yansıtır:

    ![Uygulama adı seçimi][1]

1. Bir abonelik seçin.

1. Yeni bir kaynak grubu için bir ad girin veya **var olanı kullan** ' ı seçin ve açılan listeden birini seçin.

1. İşletim sisteminizi seçin.

1. AŞIRDE mevcut bir App Service planı seçin veya aşağıdaki adımları izleyerek yeni bir tane oluşturun:

    a. Azure portal sol taraftaki menüden **bir kaynak oluştur > Web uygulaması**' nı seçin.

    b. Aboneliği seçin.

    c. Kaynak grubunu seçin veya oluşturun.

    d. Web uygulamanızın adını girin.

    e. **Kod** veya **dockercontainer** seçin.

    f. Çalışma zamanı yığını seçin.

    örneğin: **Linux** veya **Windows**'u seçin. 

    h. **Bölge** açılır listesinden Ao 'nizi seçin. 

    i. Yeni bir App Service planı seçin veya oluşturun. Yeni bir App Service planı oluşturuyorsanız uygun **yalıtılmış** SKU boyutunu seçin.

    ![Yalıtılmış fiyatlandırma katmanları][2]

    > [!NOTE]
    > Linux uygulamaları ve Windows uygulamaları aynı App Service planında olamaz, ancak aynı App Service Ortamı olabilir.
    >

1. **Gözden geçir + oluştur**' u seçin, bilgilerin doğru olduğundan emin olun ve ardından **Oluştur**' u seçin.

## <a name="how-scale-works"></a>Ölçeklendirmenin çalışması

Her App Service uygulaması bir App Service planında çalışır. App Service ortamlar, App Service planlarını ve App Service planlar uygulamaları tutar. Bir uygulamayı ölçeklendirirseniz, aynı plandaki App Service planı ve tüm uygulamaları da ölçeklendirebilirsiniz.

Bir App Service planını ölçeklendirirseniz, gerekli altyapı otomatik olarak eklenir. Altyapı eklenirken, işlemleri ölçeklendirmek için zaman gecikmesi vardır. Sırayla birkaç ölçeklendirme işlemi yaparsanız, ilk altyapı ölçekleme isteği açık olur ve diğerleri kuyruğa alınır. İlk ölçeklendirme işlemi tamamlandığında, diğer altyapı istekleri birlikte çalışır. Altyapı eklendiğinde App Service planları uygun şekilde atanır. Yeni bir App Service planının oluşturulması, ek donanım istediğinde bir ölçeklendirme işlemidir.

Çok kiracılı App Service, bir kaynak havuzu bunu destekleyecek şekilde kullanıma hazır olduğundan ölçekleme anında gerçekleşir. Ao 'da böyle bir arabellek yoktur ve kaynaklar ihtiyaya göre ayrılır.

Bir Ao 'da, bir App Service planı 100 örneğe kadar ölçeklendirebilirsiniz. ATıCı, bu Ao 'nun tüm App Service planlarında en fazla 201 toplam örneğe sahip olabilir.

## <a name="ip-addresses"></a>IP adresleri

App Service, bir uygulamaya adanmış bir IP adresi ayırabilir. Bu özellik, IP tabanlı SSL 'yi yapılandırdıktan sonra, [mevcut özel bır TLS/SSL sertifikasını Azure App Service bağlama][ConfigureSSL]bölümünde açıklandığı gibi kullanılabilir. ILB Ao 'da IP tabanlı SSL için kullanılacak daha fazla IP adresi ekleyemezsiniz.

Dış bir AO ile, uygulamanız için IP tabanlı SSL 'yi çok kiracılı App Service ile aynı şekilde yapılandırabilirsiniz. ASE 'de en fazla 30 IP adresine kadar her zaman bir yedek adres vardır. Her birini kullandığınızda, bir adres her zaman kullanıma hazır olacak şekilde başka bir kez eklenir. Başka bir IP adresi ayırmak için bir zaman gecikmesi gerekir. Bu gecikme, IP adreslerini hızla art arda eklemeyi önler.

## <a name="front-end-scaling"></a>Ön uç ölçeklendirme

App Service planlarınızı ölçeklendirirseniz, çalışanlar onları destekleyecek şekilde otomatik olarak eklenir. Her Ao, iki ön uç ile oluşturulur. Ön uçlar, her 15 App Service plan örneği için bir ön uç hızında otomatik olarak ölçeklendirilir. Örneğin, her biri beş örnek içeren üç App Service planınız varsa, toplam 15 örneğe ve üç ön uça sahip olursunuz. Toplam 30 örneğe ölçeklendirirseniz, dört ön Uçmış olursunuz. Bu model, ölçeği ölçeklendirirken devam eder.

Varsayılan olarak ayrılan ön uçların sayısı, orta düzeyde yük için iyidir. Oranı her beş örnek için bir ön uç kadar az olacak şekilde düşürebilirsiniz. Ön uçların boyutunu da değiştirebilirsiniz. Varsayılan olarak, tek çekirdekdir. Azure portal, onun boyutunu iki veya dört çekirdekli bir şekilde değiştirebilirsiniz.

Oran veya ön uç boyutlarının değiştirilmesinin bir ücreti vardır. Daha fazla bilgi için bkz. [Azure App Service fiyatlandırması][Pricing]. AP 'nizin yük kapasitesini artırmak istiyorsanız, ölçek oranını ayarlamadan önce, ilk olarak iki çekirdekli ön uçları ölçeklendirerek daha fazla geliştirme sağlayacaksınız. Ön uçlarınızın çekirdek boyutunu değiştirmek AP 'nizin yükseltmesine neden olur ve normal iş saatleri dışında yapılmalıdır.

Ön uç kaynakları ASE için HTTP/HTTPS uç noktasıdır. Varsayılan ön uç yapılandırmasıyla, ön uç başına bellek kullanımı yaklaşık olarak yüzde 60 ' dir. Ön uçlarınızın ölçeklendirilmesi için birincil neden, genellikle HTTPS trafiği tarafından yönetilen CPU kullanımlarıdır.

## <a name="app-access"></a>Uygulama erişimi

Bir dış ate, uygulama oluşturma için kullanılan etki alanı son eki *. &lt; asename &gt; . p.azurewebsites.net*. Ao 'niz _External-Ao_ olarak adlandırılmışsa ve _contoso_ ADLı bir uygulamayı bu Ao 'da barındırdıysanız, bu URL 'lerden ulaşabilirsiniz:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

Dış AIN oluşturma hakkında daha fazla bilgi için bkz. [Create a App Service ortamı][MakeExternalASE].

Bir ıLB Ao 'da, uygulama oluşturma için kullanılan etki alanı soneki olur *. &lt; asename &gt; . appserviceenvironment.net*. Ao 'niz _ILB-Ao_ olarak adlandırılmışsa ve _contoso_ ADLı bir uygulamayı bu Ao 'da barındırdıysanız, bu URL 'lerden ulaşabilirsiniz:

- contoso.ilb-ase.appserviceenvironment.net
- contoso.scm.ilb-ase.appserviceenvironment.net

ILB ATıCı oluşturma hakkında daha fazla bilgi için bkz. [ILB Ai oluşturma ve kullanma][MakeILBASE].

SCM URL 'SI, kudu konsoluna erişmek veya Web Dağıtımı kullanarak uygulamanızı yayımlamak için kullanılır. Kudu Konsolu hakkında daha fazla bilgi için bkz. [Azure App Service kudu konsolu][Kudu]. Kudu konsolu, hata ayıklama, dosyaları karşıya yükleme, dosyaları düzenlemeyle ve çok daha fazlası için bir Web Kullanıcı arabirimi sağlar.

### <a name="dns-configuration"></a>DNS yapılandırması 

Bir dış Ai kullandığınızda, Ao uygulamanızda yapılan uygulamalar Azure DNS kaydedilir. Uygulamalarınızın genel kullanıma açık olması için bir dış Ao 'da ek adım yoktur. ILB Ao ile kendi DNS 'nizi yönetmeniz gerekir. Bunu kendi DNS sunucunuzda veya Azure DNS özel bölgelerle yapabilirsiniz.

Kendi DNS sunucunuzdaki DNS 'yi ıLB Ao 'ınızla yapılandırmak için:

1. &lt;alname. appserviceenvironment.NET için bir bölge oluşturun &gt;
1. Bu bölgede * ıLB IP adresine işaret eden bir kayıt oluşturun
1. Bu bölgede @ adresli ıLB IP adresine işaret eden bir kayıt oluşturma
1. &lt;Ao Name &gt; . appserviceenvironment.net adlı SCM adlı bir bölge oluşturun
1. SCM bölgesinde * ıLB IP adresine işaret eden bir kayıt oluşturun

Azure DNS özel bölgelerde DNS 'yi yapılandırmak için:

1. &lt;Ao Name. appserviceenvironment.net adlı bir Azure DNS özel bölge oluşturun &gt;
1. Bu bölgede * ıLB IP adresine işaret eden bir kayıt oluşturun
1. Bu bölgede @ adresli ıLB IP adresine işaret eden bir kayıt oluşturma
1. Bu bölgede, ıLB IP adresine *. SCM 'yi işaret eden bir kayıt oluşturun

Ao varsayılan etki alanı son ekinin DNS ayarları, uygulamalarınızı yalnızca bu adlar tarafından erişilebilir olarak kısıtlayamaz. Bir ıLB Ao 'da uygulamalarınızda herhangi bir doğrulama yapmadan özel bir etki alanı adı ayarlayabilirsiniz. Daha sonra *contoso.net* adlı bir bölge oluşturmak istiyorsanız bunu yapabilirsiniz ve ıLB IP adresine işaret edebilirsiniz. Özel etki alanı adı, uygulama istekleri için geçerlidir ancak SCM sitesi için değildir. SCM sitesi yalnızca *&lt; appname. SCM adresinde kullanılabilir &gt; . &lt; asename &gt; . appserviceenvironment.net*. 

Adlı bölge *. &lt; asename &gt; . appserviceenvironment.net* , genel olarak benzersizdir. 2019 tarihinden önce, müşteriler ıLB Ao 'nun etki alanı sonekini belirleyebildi. Etki alanı soneki için *. contoso.com* kullanmak istiyorsanız, bunu yapabilir ve SCM sitesini de kapsayabileceksiniz. Bu modelde olduğu gibi sorunlar oluştu; varsayılan SSL sertifikasını yönetme, SCM sitesiyle çoklu oturum açma olmaması ve bir joker karakter sertifikası kullanma gereksinimi. ILB ATıCı varsayılan sertifika yükseltme işlemi de karışıklığa ve uygulamanın yeniden başlatılmasına neden oldu. Bu sorunları gidermek için ıLB Ao davranışı, Ao 'nun adına ve Microsoft 'un sonekine sahip olan bir etki alanı sonekini kullanacak şekilde değiştirilmiştir. ILB ASE davranışında yapılan değişiklik yalnızca 2019 ' den sonra gerçekleştirilen ıLB ASE 'yi etkiler. Önceden var olan ıLB 'ler, ASE 'nin varsayılan sertifikasını ve DNS yapılandırmalarını yine de yönetmelidir.

## <a name="publishing"></a>Yayımlama

Çok kiracılı App Service olduğu gibi, bu yöntemlerle yayımlayabilirsiniz:

- Web dağıtımı
- FTP
- Sürekli tümleştirme (CI)
- Kudu konsoluna sürükleme ve bırakma
- Visual Studio, tutulma veya IntelliJ fıkrı gibi bir IDE

Bu yayımlama seçeneklerinin tümü bir dış Ao ile aynı şekilde çalışır. Daha fazla bilgi için bkz. [Azure App Service dağıtım][AppDeploy].

ILB Ao ile, yayımlama uç noktaları yalnızca ıLB ile kullanılabilir. ILB, sanal ağdaki Ao alt ağında özel bir IP üzerinde yer alır. ILB 'ye ağ erişiminiz yoksa, o Ao 'da herhangi bir uygulama yayımlayamazsınız. [BIR ıLB Ai oluşturma ve kullanma][MakeILBASE]bölümünde belirtildiği gibi, sistemdeki uygulamalar için DNS 'yi yapılandırmanız gerekir. Bu gereksinim SCM uç noktasını içerir. Uç noktalar düzgün tanımlanmamışsa, yayımlayamazsınız. Ides 'in doğrudan yayımlayabilmesi için ıLB 'ye ağ erişimi de olmalıdır.

Ek değişiklikler olmadan GitHub ve Azure DevOps gibi internet tabanlı CI sistemleri bir ıLB Ao ile çalışmaz, çünkü yayımlama uç noktası internet 'e erişilebilir değildir. ILB asa 'yı içeren sanal ağa şirket içinde barındırılan bir yayın Aracısı yükleyerek Azure DevOps 'dan bir ıLB Ao 'a yayımlamayı etkinleştirebilirsiniz. Alternatif olarak, Dropbox gibi bir çekme modeli kullanan bir CI sistemini de kullanabilirsiniz.

Bir ILB ASE’deki uygulamalar için yayımlama uç noktaları, ILB ASE oluşturulurken kullanılan etki alanını kullanır. Uygulamanın yayımlama profilinde ve uygulamanın Portal bölmesinde ( **genel bakış**  >  **temelleri** ' nde ve ayrıca **özelliklerinde**) görebilirsiniz.

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

Log Analytics ile tümleştirirseniz, asa portalından **Günlükler** seçerek ve **Appserviceenvironmentplatformlogs**'a yönelik bir sorgu oluşturarak günlükleri görebilirsiniz. Günlükler yalnızca asa 'nın onu tetikleyecek bir olayı varsa yayılır. Asa 'nın böyle bir olayı yoksa, herhangi bir günlük olmayacaktır. Log Analytics çalışma alanınızdaki bir günlük örneğini hızlıca görmek için, AŞIRINIZDEKI App Service planlarından biriyle bir ölçeklendirme işlemi gerçekleştirin. Ardından, bu günlükleri görmek için **Appserviceenvironmentplatformlogs** 'a yönelik bir sorgu çalıştırabilirsiniz. 

**Uyarı oluşturma**

Günlüklerinizi karşılaştırarak bir uyarı oluşturmak için [Azure izleyici 'yi kullanarak günlük uyarılarını oluşturma, görüntüleme ve yönetme](../../azure-monitor/alerts/alerts-log.md)bölümündeki yönergeleri izleyin. Kısaca:

* AS portalınızdaki uyarılar sayfasını açın
* **Yeni uyarı kuralı** Seç
* Log Analytics çalışma alanınız olacak kaynağı seçin
* "AppServiceEnvironmentPlatformLogs | gibi bir sorgu kullanmak için koşullarınızı özel bir günlük araması ile ayarlayın ResultDescription "ölçeklendirmeye başladı" veya istediğiniz herhangi bir şey vardır. Eşiği uygun şekilde ayarlayın. 
* İstediğiniz bir eylem grubu ekleyin veya oluşturun. Eylem grubu, bir e-posta veya SMS iletisi gönderme gibi uyarıya yanıtı tanımladığınız yerdir
* Uyarınızı adlandırın ve kaydedin.

## <a name="upgrade-preference"></a>Yükseltme tercihi

Birden çok ASE varsa, bazı ASE 'lerin başkalarından önce yükseltilmesini isteyebilirsiniz. ATıCı **HostingEnvironment Kaynak Yöneticisi** nesnesi Içinde, **upgradepreference** için bir değer ayarlayabilirsiniz. **Upgradepreference** ayarı bir şablon, ARMClient veya kullanılarak yapılandırılabilir https://resources.azure.com . Olası üç değer şunlardır:

- **Hiçbiri**: Azure, Ao 'nizi belirli bir toplu iş olmadan yükseltecektir. Bu varsayılan değerdir.
- **Erken**: Ao 'niz App Service yükseltmelerin ilk yarısında yükseltilir.
- **Geç**: Ao 'niz App Service yükseltmelerinin ikinci yarısında yükseltilir.

Kullanıyorsanız https://resources.azure.com , **upgradepreferences** değerini ayarlamak için şu adımları izleyin:

1. Resources.azure.com adresine gidin ve Azure hesabınızla oturum açın.
1. Kaynakları aboneliklerle abonelikler \/ \[ abonelik adı \] \/ ResourceGroups \/ \[ kaynak grubu adı \] \/ sağlayıcılar \/ Microsoft. Web \/ hostingenvironments \/ \[ Ao adı \] .
1. Üstteki **oku/yaz** seçeneğini belirleyin.
1. **Düzenle**'yi seçin.
1. **Yükseltme tercihini** , istediğiniz üç değerden hangisinin hangisi olduğuna göre ayarlayın.
1. **Düzeltme Eki** seçin.

![Kaynak Azure com görüntülemesi][5]

"Erken" Alarınızın "geç" Atoklarından önce yükseltilemeyeceğinden, **yükseltme tercihleri** özelliği birden çok ASE sahip olduğunuzda en mantıklı şekilde yararlanacaktır. Birden çok ASE 'niz varsa, geliştirme ve test Alarınızı "erken" ve üretim Alarınızın "geç" olacak şekilde ayarlamanız gerekir.

## <a name="pricing"></a>Fiyatlandırma

*Yalıtılmış* olarak adlandırılan fiyatlandırma SKU 'Su yalnızca ASE kullanımı içindir. ASA 'da barındırılan tüm App Service planları yalıtılmış fiyatlandırma SKU 'sunda. App Service planlarına yönelik yalıtılmış ücretler bölgeye göre değişiklik gösterebilir.

App Service planlarınızın fiyatına ek olarak, ASE 'nin kendisi için düz bir ücret vardır. Düz hız asa 'larınızın boyutuyla değişmez. Her 15 App Service plan örneği için bir ek ön ucun varsayılan ölçek hızında Ao altyapısı için ödeme yapar.

Her 15 App Service plan örneği için bir ön ucun varsayılan ölçek oranı yeterince hızlı değilse, ön uçların eklenme oranını veya ön uçların boyutunu ayarlayabilirsiniz. Oran veya boyut ayarlarını ayarlarken, varsayılan olarak eklenmemelidir ön uç çekirdekleri için ödeme yaparsınız.

Örneğin, ölçek oranını 10 olarak ayarlarsanız App Service planlarınızda her 10 örnek için bir ön uç eklenir. Düz ücret, her 15 örnek için bir ön ucun ölçek oranını içerir. 10 ' un ölçek oranıyla 10 App Service plan örnekleri için eklenen üçüncü ön uç için ücret ödersiniz. Otomatik olarak eklendiğinden, 15 örneğe ulaştığınızda bu ücret için ödeme yapmanız gerekmez.

Ön uçların boyutunu iki çekirdeğe ayarlarsanız ancak oranı ayarlamazsanız, ek çekirdekler için ödeme yaparsınız. İki ön uç ile bir ASE oluşturulur. bu nedenle, otomatik ölçeklendirme eşiğinin altına bile, boyutu iki çekirdekli ön uçları artırdıysanız iki ek çekirdek için ödeme yaparsınız.

Daha fazla bilgi için bkz. [Azure App Service fiyatlandırması][Pricing].

## <a name="delete-an-ase"></a>AS 'yi silme

ATıCı 'yi silmek için:

1. **App Service ortamı** bölmesinin en üstünde **Sil** ' i seçin.

1. Silmek istediğinizi onaylamak için ATıCı 'nizin adını girin. Bir AI 'yi sildiğinizde, içindeki tüm içeriği de silersiniz.

    ![ATıCı silme][3]

1. **Tamam**’ı seçin.

## <a name="ase-cli"></a>ATıCı CLı

Ao 'da yönetilecek komut satırı özellikleri vardır.  Az CLI komutları aşağıda belirtilmiştir.

```azurecli
C:\>az appservice ase --help

Group
    az appservice ase : Manage App Service Environments v2.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    create         : Create app service environment.
    delete         : Delete app service environment.
    list           : List app service environments.
    list-addresses : List VIPs associated with an app service environment.
    list-plans     : List app service plans associated with an app service environment.
    show           : Show details of an app service environment.
    update         : Update app service environment.

For more specific examples, use: az find "az appservice ase"
```



<!--Image references-->
[1]: ./media/using_an_app_service_environment/usingase-appcreate.png
[2]: ./media/using_an_app_service_environment/usingase-pricingtiers.png
[3]: ./media/using_an_app_service_environment/usingase-delete.png
[4]: ./media/using_an_app_service_environment/usingase-logsetup.png
[4]: ./media/using_an_app_service_environment/usingase-logs.png
[5]: ./media/using_an_app_service_environment/usingase-upgradepref.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/alerts/alerts-log.md
