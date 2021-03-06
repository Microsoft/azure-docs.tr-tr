---
title: AS v1 'de bir uygulamayı ölçeklendirme
description: App Service Ortamı bir uygulamayı ölçekleme. Bu belge yalnızca eski v1 Ao kullanan müşteriler için sağlanır.
author: ccompy
ms.assetid: 78eb1e49-4fcd-49e7-b3c7-f1906f0f22e3
ms.topic: article
ms.date: 10/17/2016
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 0e665ec27da0a898e754817f946b965ac7360fda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86220567"
---
# <a name="scaling-apps-in-an-app-service-environment-v1"></a>App Service Ortamı v1 'de Uygulamaları ölçeklendirme
Azure App Service, genellikle ölçeklenebilmeniz gereken üç şey vardır:

* Fiyatlandırma planı
* çalışan boyutu 
* örnek sayısı.

Bir Ao 'da fiyatlandırma planını seçme veya değiştirme gereksinimi yoktur.  Özellikler açısından, zaten Premium fiyatlandırma yetenek düzeyindedir.  

ATıCı Yöneticisi, çalışan boyutlarına göre her çalışan havuzu için kullanılacak işlem kaynağının boyutunu atayabilir.  Bu, istenirse, P4 işlem kaynakları ve çalışan Havuzu 2 olan çalışan havuzu 1 ' i, varsa P1 işlem kaynakları ile sahip olabileceği anlamına gelir.  Bunların boyut düzeninde olması gerekmez.  Boyutlara ve bunların fiyatlandırmasına ilişkin ayrıntılar için bu belgeyi [fiyatlandırma Azure App Service][AppServicePricing]buraya bakın.  Bu, Web uygulamaları için ölçeklendirme seçeneklerini ve bir App Service Ortamı App Service planlarını şu şekilde bırakır:

* çalışan havuzu seçimi
* örnek sayısı

Her iki öğenin de değiştirilmesi, AKEN barındırılan App Service planlarınız için gösterilen uygun Kullanıcı arabirimi aracılığıyla yapılır.  

![Ölçek hizmeti planının ve çalışan havuzu hizmet planının ayrıntılarının nerede görüntüleneceği gösteren ekran görüntüsü.][1]

ASP 'nizin içinde bulunduğu çalışan havuzundaki kullanılabilir işlem kaynakları sayısının ötesinde ASP 'nizi ölçeklendirebilirsiniz.  Bu çalışan havuzunda işlem kaynaklarına ihtiyacınız varsa, ATıCı yöneticinizden bunları eklemesi gerekir.  ATıCı 'nizi yeniden yapılandırma hakkında daha fazla bilgi için şu bilgileri okuyun: [App Service ortamını yapılandırma][HowtoConfigureASE].  Ayrıca, zamanlamaya veya ölçümlere göre kapasite eklemek için ATıCı otomatik ölçeklendirme özelliklerinden de yararlanmak isteyebilirsiniz.  Ao ortamı için otomatik ölçeklendirmeyi yapılandırma hakkında daha fazla bilgi edinmek için bkz. [Otomatik ölçeklendirmeyi bir App Service ortamı için yapılandırma][ASEAutoscale].

Farklı çalışan havuzlarından işlem kaynaklarını kullanarak birden çok App Service planı oluşturabilir veya aynı çalışan havuzunu kullanabilirsiniz.  Örneğin, çalışan havuzu 1 ' de (10) kullanılabilir işlem kaynağınız varsa, (6) işlem kaynaklarını kullanarak bir App Service planı oluşturmayı ve (4) işlem kaynaklarını kullanan ikinci bir App Service planını oluşturmayı tercih edebilirsiniz.

### <a name="scaling-the-number-of-instances"></a>Örnek sayısını ölçeklendirme
Web uygulamanızı bir App Service Ortamı ilk kez oluşturduğunuzda 1 örnek ile başlar.  Daha sonra, uygulamanız için ek işlem kaynakları sağlamak üzere ek örneklere ölçeklendirebilirsiniz.   

AŞIRIN yeterli kapasitesi varsa, bu oldukça basittir.  Ölçeği genişletmek istediğiniz siteleri tutan App Service planına gidin ve ölçek ' i seçin.  Bu, ASP için ölçeği el ile ayarlayabileceğiniz veya ASP 'niz için otomatik ölçeklendirme kurallarını yapılandırabileceğiniz Kullanıcı arabirimini açar.  Uygulamanızı el ile ölçeklendirmek için * ***ölçeğini** _ ile _ * arasında _el ile * * girdiğim bir örnek sayısı_ ayarlamanız yeterlidir.  Buradan kaydırıcıyı istediğiniz miktara sürükleyin ya da kaydırıcının yanındaki kutuya girin.  

![ASP 'nizin ölçeğini ayarlayabileceğiniz veya ASP 'niz için otomatik ölçeklendirme kurallarını yapılandırabileceğiniz yeri gösteren ekran görüntüsü.][2] 

Ao 'da bir ASP için otomatik ölçeklendirme kuralları, normal şekilde çalıştıkları gibi çalışır.  _*_Ölçek ölçütü_*_ altında ***CPU yüzdesi** _ ' i seçebilir ve CPU yüzdesine göre ASP 'niz için otomatik ölçeklendirme kuralları oluşturabilir veya _ *_zamanlama ve performans kuralları_* * kullanarak daha karmaşık kurallar oluşturabilirsiniz.  Otomatik ölçeklendirmeyi yapılandırma hakkında daha ayrıntılı bilgileri görmek için, burada [Azure App Service bir uygulamayı ölçeklendirin][AppScale]. 

### <a name="worker-pool-selection"></a>Çalışan havuzu seçimi
Daha önce belirtildiği gibi, çalışan havuzu seçimine ASP kullanıcı arabiriminden erişilir.  Ölçeklendirmek istediğiniz ASP için dikey pencereyi açın ve çalışan havuzu ' nu seçin.  App Service Ortamı yapılandırdığınız tüm çalışan havuzları görüntülenir.  Yalnızca bir çalışan havuzunuz varsa, yalnızca bir havuzu listelenmiş olarak görürsünüz.  ASP 'nizin hangi çalışan havuzunu değiştirmek için, App Service planınızın taşınmasını istediğiniz çalışan havuzunu seçmeniz yeterlidir.  

![ASP 'nizin hangi çalışan havuzunu değiştirebileceğinize ilişkin konumu gösteren ekran görüntüsü.][3]

ASP 'nizi bir çalışan havuzundan diğerine taşımadan önce ASP 'niz için yeterli kapasiteye sahip olduğunuzdan emin olmak önemlidir.  Çalışan havuzları listesinde, yalnızca listelenen çalışan havuzu adı değil, söz konusu çalışan havuzunda kaç tane çalışan olduğunu da görebilirsiniz.  App Service planınızı içerecek yeterli sayıda örnek olduğundan emin olun.  Taşımak istediğiniz çalışan havuzunda daha fazla bilgi işlem kaynağına ihtiyacınız varsa, açı yöneticinizden bunları eklemesini sağlayın.  

> [!NOTE]
> Bir ASP 'nin bir çalışan havuzundan taşınması, o ASP 'deki uygulamaların soğuk başlamasına neden olur.  Bu, uygulamanız yeni işlem kaynaklarında soğuk olarak başlatıldığından isteklerin yavaş çalışmasına neden olabilir.  Azure App Service ' deki [uygulamanın ısınma özelliği][AppWarmup] kullanılarak soğuk başlatmaya kaçınılabilir.  Makalede açıklanan uygulama başlatma modülü, uygulamalar yeni işlem kaynaklarında soğuk başladığında başlatma işlemi de çağrıldığı için de soğuk başlangıçları için de kullanılır. 
> 
> 

## <a name="getting-started"></a>Kullanmaya başlama
App Service ortamları ile çalışmaya başlamak için bkz. [nasıl App Service ortamı oluşturma][HowtoCreateASE]

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-aspblade.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-manualscale.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-sizescale.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[ScaleWebapp]: ../manage-scale-up.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoConfigureASE]: app-service-web-configure-an-app-service-environment.md
[CreateWebappinASE]: app-service-web-how-to-create-a-web-app-in-an-ase.md
[Appserviceplans]: ../overview-hosting-plans.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[AppScale]: ../manage-scale-up.md
[AppWarmup]: https://ruslany.net/2015/09/how-to-warm-up-azure-web-app-during-deployment-slots-swap/
