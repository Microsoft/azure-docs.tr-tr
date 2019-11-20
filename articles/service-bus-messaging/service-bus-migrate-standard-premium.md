---
title: Azure Service Bus ad alanlarını geçirme-Standart-Premium
description: Mevcut Azure Service Bus standart ad alanlarının Premium 'a geçirilmesine izin verme kılavuzu
services: service-bus-messaging
documentationcenter: ''
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2019
ms.author: aschhab
ms.openlocfilehash: 610c3aa486b48b2d29df48d98e93b37cfec4854c
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790373"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-the-premium-tier"></a>Mevcut Azure Service Bus standart ad alanlarını Premium katmanına geçirin
Daha önce, Azure Service Bus ad alanları yalnızca Standart katmanda sunulur. Ad alanları, düşük aktarım hızı ve geliştirici ortamları için iyileştirilmiş çok kiracılı kurulumlardır. Premium katmanı, öngörülebilir gecikme ve sabit bir fiyata daha fazla verimlilik için ad alanı başına adanmış kaynaklar sunar. Premium katman, ek kurumsal özellikler gerektiren yüksek aktarım hızı ve üretim ortamları için iyileştirilmiştir.

Bu makalede, mevcut standart katman ad alanlarının Premium katmana nasıl geçirileceği açıklanır.  

>[!WARNING]
> Geçiş, Premium katmana Yükseltilecek Service Bus standart ad alanları için tasarlanmıştır. Geçiş Aracı, eski sürüme yükseltmeyi desteklemez.

Aklınızda bazı noktaları: 
- Bu geçişin, mevcut gönderici ve alıcı uygulamalarının **kod veya yapılandırmada herhangi bir değişiklik gerektirmeyeceği**anlamına gelir. Var olan bağlantı dizesi otomatik olarak yeni Premium ad alanına işaret eder.
- Geçişin başarılı olması için **Premium** ad alanının içinde **hiç varlık olmaması** gerekir. 
- Standart ad alanındaki tüm **varlıklar** , geçiş işlemi sırasında Premium ad alanına **kopyalanır** . 
- Geçiş, Premium katmandaki **mesajlaşma birimi başına 1.000 varlıklarını** destekler. Kaç tane mesajlaşma birimine ihtiyacınız olduğunu belirlemek için, geçerli standart ad alanı üzerinde sahip olduğunuz varlıkların sayısıyla başlayın. 
- **Temel katmandan** **Premier katmana**doğrudan geçiş yapamazsınız, ancak temel bir sonraki adımda standart ' dan Premium ' a geçiş yaparak bunu dolaylı olarak yapabilirsiniz.

## <a name="migration-steps"></a>Geçiş adımları
Bazı koşullar Geçiş işlemiyle ilişkilidir. Hata olasılığını azaltmak için aşağıdaki adımları tanıyın. Bu adımlar, geçiş sürecini özetler ve adım adım ayrıntılar aşağıdaki bölümlerde listelenmiştir.

1. Yeni bir Premium ad alanı oluşturun.
1. Standart ve Premium ad alanlarını birbirleriyle eşleştirin.
1. Standart olan varlıkları Premium ad alanına eşitleyin (kopya).
1. Geçişi yürütün.
1. Ad alanının geçiş sonrası adını kullanarak standart ad alanındaki varlıkları boşaltın.
1. Standart ad alanını silin.

>[!IMPORTANT]
> Geçiş gerçekleştirildikten sonra, eski standart ad alanına erişin ve kuyrukları ve abonelikleri boşaltın. İletiler kapatıldıktan sonra, alıcı uygulamalar tarafından işlenmek üzere yeni Premium ad alanına gönderilebilir. Kuyruklar ve abonelikler kapatıldıktan sonra, eski standart ad alanını silmenizi öneririz.

### <a name="migrate-by-using-the-azure-cli-or-powershell"></a>Azure CLı veya PowerShell kullanarak geçirme

Azure CLı veya PowerShell aracını kullanarak Service Bus standart ad alanınızı Premium 'a geçirmek için aşağıdaki adımları izleyin.

1. Yeni bir Service Bus Premium ad alanı oluşturun. [Azure Resource Manager şablonlarına](service-bus-resource-manager-namespace.md) başvurabilir veya [Azure Portal kullanabilirsiniz](service-bus-create-namespace-portal.md). **Servicebussku** parametresi için **Premium** ' u seçtiğinizden emin olun.

1. Geçiş komutlarını basitleştirmek için aşağıdaki ortam değişkenlerini ayarlayın.
   ```azurecli
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the standard namespace>
   ```

    >[!IMPORTANT]
    > Geçiş sonrası diğer adı/adı (post_migration_dns_name), eski standart ad alanı geçişine erişmek için kullanılacaktır. Kuyrukları ve abonelikleri boşaltmak için bunu kullanın ve ardından ad alanını silin.

1. Aşağıdaki komutu kullanarak standart ve Premium ad alanlarını eşleştirin ve eşitlemeyi başlatın:

    ```azurecli
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```


1. Aşağıdaki komutu kullanarak geçişin durumunu denetleyin:
    ```azurecli
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    Aşağıdaki değerleri gördüğünüzde geçiş tamamlanmış olarak kabul edilir:
    * MigrationState = "etkin"
    * pendingReplicationsOperationsCount = 0
    * provisioningState = "başarılı"

    Bu komut, geçiş yapılandırmasını da görüntüler. Değerlerin doğru ayarlandığından emin olmak için denetleyin. Ayrıca, tüm sıraların ve konuların oluşturulduğundan ve standart ad alanında var olan özellikleri ile eşleştiğinden emin olmak için portalda Premium ad alanını kontrol edin.

1. Aşağıdaki tamamlanmış komutu yürüterek geçişi yürütün:
   ```azurecli
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-by-using-the-azure-portal"></a>Azure portal kullanarak geçirin

Azure portal kullanılarak geçiş, komutları kullanılarak geçişle aynı mantıksal akışa sahiptir. Azure portal kullanarak geçiş yapmak için aşağıdaki adımları izleyin.

1. Sol bölmedeki **Gezinti** menüsünde, **Premium 'a geçir**' i seçin. Sonraki sayfaya devam etmek için **Başlarken** düğmesine tıklayın.
    ![geçiş giriş sayfası][]

1. **Kurulumu**tamamladıktan sonra.
   ![kurulum ad alanı][]
   1. Varolan standart ad alanını içine geçirmek için Premium ad alanını oluşturun ve atayın.
        ![kurulum ad alanı-Premium ad alanı oluşturma][]
   1. **Geçiş sonrası bir ad**seçin. Geçiş tamamlandıktan sonra bu adı standart ad alanına erişmek için kullanacaksınız.
        ![kurulum ad alanı-geçiş sonrası adı seçin][]
   1. Devam etmek için **' ileri '** seçeneğini belirleyin.
1. Standart ve Premium ad alanları arasında varlıkları eşitleyin.
    ![kurulum ad alanı-eşitleme varlıkları-Başlat][]

   1. Varlıkları eşitlemeye başlamak için **Eşitlemeyi Başlat** ' ı seçin.
   1. Eşitlemeyi onaylamak ve başlatmak için iletişim kutusunda **Evet** ' i seçin.
   1. Eşitleme tamamlanana kadar bekleyin. Durum çubuğunda durum kullanılabilir.
        ![kurulum ad alanı-eşitleme varlıkları-ilerleme][]
        >[!IMPORTANT]
        > Herhangi bir nedenle geçiş işlemini iptal etmeniz gerekirse, lütfen bu belgenin SSS bölümünde yer alarak iptal akışını gözden geçirin.
   1. Eşitleme tamamlandıktan sonra sayfanın alt kısmındaki **İleri** ' yi seçin.

1. Özet sayfasındaki değişiklikleri gözden geçirin. Ad alanlarını değiştirmek ve geçişi gerçekleştirmek için **geçişi Tamam** ' ı seçin.
    ![anahtar ad alanı-anahtar menü][]  
    Geçiş tamamlandığında onay sayfası görüntülenir.
    ![anahtar ad alanı-başarılı][]

## <a name="caveats"></a>Uyarılar

Azure Service Bus standart katmanı tarafından sunulan özelliklerden bazıları Azure Service Bus Premium katmanı tarafından desteklenmez. Premium katman öngörülebilir aktarım hızı ve gecikme süresi için adanmış kaynaklar sağladığından bu, tasarıma göre yapılır.

Premium tarafından desteklenmeyen özelliklerin listesi ve bunların hafifletme düzeyi 

### <a name="express-entities"></a>İfade varlıkları

   Herhangi bir ileti verisi depolamaya meyen Express varlıkları Premium 'da desteklenmez. Adanmış kaynaklar, verilerin kalıcı olmasını sağlarken, tüm kurumsal mesajlaşma sistemlerinde beklenildiği için önemli ölçüde üretilen iş geliştirmesi sağlamıştır.
   
   Geçiş sırasında standart ad alanındaki Express varlıklarınız Premium ad alanı üzerinde Express olmayan bir varlık olarak oluşturulur.
   
   Azure Resource Manager (ARM) şablonları kullanıyorsanız, otomatik iş akışlarınızın hatasız yürütülmesi için dağıtım yapılandırmasından ' enableExpress ' bayrağını kaldırtığınızdan emin olun.

### <a name="partitioned-entities"></a>Bölümlenen varlıklar

   Bölümlenmiş varlıklar, çok kiracılı bir kurulumda daha iyi kullanılabilirlik sağlamak için Standart katmanda desteklendi. Premium katmanda ad alanı başına kullanılabilir adanmış kaynakların sağlanması sayesinde artık bu gerekli değildir.
   
   Geçiş sırasında standart ad alanındaki bölümlenmiş bir varlık, bir bölümlenmemiş varlık olarak Premium ad alanında oluşturulur.
   
   ARM şablonunuz belirli bir sıra veya konu için ' Enablebölümlendirme ' değerini ' true ' olarak ayarlarsa, aracı tarafından yok sayılır.

## <a name="faqs"></a>SSS

### <a name="what-happens-when-the-migration-is-committed"></a>Geçiş taahhüt edildiğinde ne olur?

Geçiş gerçekleştirildikten sonra, standart ad alanına işaret eden bağlantı dizesi Premium ad alanına işaret eder.

Gönderen ve alıcı uygulamaları standart ad alanı bağlantısını keser ve Premium ad alanına otomatik olarak yeniden bağlanır.

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>Premium geçiş için standart geçişten sonra ne yapmam gerekiyor?

Standart-Premium geçiş, konular, abonelikler ve filtreler gibi varlık meta verilerinin standart ad alanından Premium ad alanına kopyalanmasını sağlar. Standart ad alanına kaydedilen ileti verileri standart ad alanından Premium ad alanına kopyalanmaz.

Standart ad alanında, geçiş işlemi devam ederken gönderilen ve kaydedilen bazı iletiler bulunabilir. Bu iletileri standart ad alanından el ile boşaltın ve Premium ad alanına el ile gönderin. İletileri el ile boşaltmak için, geçiş komutlarında belirttiğiniz geçiş sonrası DNS adını kullanarak bir konsol uygulaması veya standart ad alanı varlıklarını boşaltacak bir betik kullanın. Alıcılar tarafından işlenebilmeleri için bu iletileri Premium ad alanına gönderin.

İletiler kapatıldıktan sonra standart ad alanını silin.

>[!IMPORTANT]
> Standart ad alanından gelen iletiler boşaldıktan sonra standart ad alanını silin. Başlangıçta standart ad alanına başvuruda bulunulan bağlantı dizesi artık Premium ad alanına başvurduğundan bu önemlidir. Artık standart ad alanı gerekmez. Geçirdiğiniz standart ad alanını silmek, daha sonra karışıklığı azaltmaya yardımcı olur.

### <a name="how-much-downtime-do-i-expect"></a>Ne kadar kesinti süresi bekledim?
Geçiş işlemi, uygulamalar için beklenen kesinti süresini azaltmaya yöneliktir. Kapalı kalma süresi, gönderen ve alıcı uygulamalarının yeni Premium ad alanına işaret etmek için kullandığı bağlantı dizesi kullanılarak azaltılır.

Uygulamanın yaşadığı kapalı kalma süresi, DNS girişinin Premium ad alanına işaret etmek üzere güncelleştirilmesi için gereken süre ile sınırlıdır. Kesinti süresi yaklaşık 5 dakikadır.

### <a name="do-i-have-to-make-any-configuration-changes-while-doing-the-migration"></a>Geçiş yaparken herhangi bir yapılandırma değişikliği yapmam gerekir mi?
Hayır, geçiş yapmak için gereken kod veya yapılandırma değişikliği yok. Gönderen ve alıcı uygulamalarının standart ad alanına erişmek için kullandığı bağlantı dizesi, Premium ad alanı için bir diğer ad olarak görev yapacak şekilde otomatik olarak eşlenir.

### <a name="what-happens-when-i-abort-the-migration"></a>Geçişi iptal ediyorum ne olur?
Geçiş, `Abort` komutu kullanılarak veya Azure portal kullanılarak iptal edilebilir. 

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace
```

#### <a name="azure-portal"></a>Azure portalı

![Iptal akışı-eşitleme][]
![Iptal akışı iptali-iptali Tamam][]

Geçiş işlemi iptal edildiğinde, varlıkları (konular, abonelikler ve filtreler) standartdan Premium ad alanına kopyalama sürecini durdurur ve eşlemeyi keser.

Bağlantı dizesi Premium ad alanını işaret etmek üzere güncelleştirilmedi. Mevcut uygulamalarınız, geçiş işlemine başlamadan önce olduğu gibi çalışmaya devam eder.

Ancak, Premium ad alanındaki varlıkları silmez veya Premium ad alanını silmez. Geçiş ile ilerlemeye karar verdiyseniz varlıkları el ile silin.

>[!IMPORTANT]
> Geçişi durdurmaya karar verirseniz, kaynaklar için ücretlendirilmemek üzere, geçiş için sağladığınız Premium ad alanını silin.

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>İletileri boşaltmak istemiyorum. Ne yapmalıyım?

Gönderen uygulamalar tarafından gönderilen ve geçiş gerçekleşirken ve geçiş kaydedilmeden önce standart ad alanındaki depolamaya işlenmiş iletiler olabilir.

Geçiş sırasında gerçek ileti verisi/yükü standart konumundan Premium ad alanına kopyalanmaz. İletilerin el ile boşaltılır ve Premium ad alanına gönderilmesi gerekir.

Ancak, planlı bir bakım/temizlik penceresi sırasında geçiş yapabilir ve iletileri el ile boşaltmasını ve göndermek istemiyorsanız, aşağıdaki adımları izleyin:

1. Gönderen uygulamalarını durdurun. Alıcı uygulamaları şu anda standart ad alanındaki iletileri işleyecek ve kuyruğu boşaltacak.
1. Standart ad alanındaki kuyruklar ve abonelikler boş olduktan sonra, standart geçiş ad alanına geçişi yürütmek için daha önce açıklanan yordamı izleyin.
1. Geçiş işlemi tamamlandıktan sonra gönderen uygulamalarını yeniden başlatabilirsiniz.
1. Gönderenler ve alıcılar artık Premium ad alanıyla otomatik olarak bağlanır.

    >[!NOTE]
    > Geçiş için alıcı uygulamalarını durdurmanız gerekmez.
    >
    > Geçiş işlemi tamamlandıktan sonra, alıcı uygulamaların standart ad alanı bağlantısını keser ve Premium ad alanına otomatik olarak bağlanır.

## <a name="next-steps"></a>Sonraki adımlar

* [Standart ve Premium mesajlaşma arasındaki farklılıklar](./service-bus-premium-messaging.md)hakkında daha fazla bilgi edinin.
* [Service Bus Premium Için yüksek kullanılabilirlik ve coğrafi olağanüstü durum kurtarma yönleri](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium)hakkında bilgi edinin.

[Geçiş giriş sayfası]: ./media/service-bus-standard-premium-migration/1.png
[Kurulum ad alanı]: ./media/service-bus-standard-premium-migration/2.png
[Kurulum ad alanı-Premium ad alanı oluşturma]: ./media/service-bus-standard-premium-migration/3.png
[Kurulum ad alanı-geçiş sonrası adı seçme]: ./media/service-bus-standard-premium-migration/4.png
[Kurulum ad alanı-eşitleme varlıkları-Başlat]: ./media/service-bus-standard-premium-migration/5.png
[Kurulum ad alanı-eşitleme varlıkları-ilerleme]: ./media/service-bus-standard-premium-migration/8.png
[Ad alanı Değiştir-anahtar menüsü]: ./media/service-bus-standard-premium-migration/9.png
[Ad alanı değiştirme-başarılı]: ./media/service-bus-standard-premium-migration/12.png

[Akışı durdur-eşitlemeyi iptal et]: ./media/service-bus-standard-premium-migration/abort1.png
[Akışı durdur-iptali Tamam]: ./media/service-bus-standard-premium-migration/abort3.png
