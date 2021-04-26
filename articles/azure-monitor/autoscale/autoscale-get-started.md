---
title: Azure 'da otomatik ölçeklendirme ile çalışmaya başlama
description: Azure 'da kaynak Web uygulamanızı, bulut hizmetinizi, sanal makineyi veya sanal makine ölçek kümesini ölçeklendirmeyi öğrenin.
ms.topic: conceptual
ms.date: 07/07/2017
ms.subservice: autoscale
ms.openlocfilehash: edc58ed4af3475a45804e3833424bec79d50ff89
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105641542"
---
# <a name="get-started-with-autoscale-in-azure"></a>Azure'da otomatik ölçeklendirmeyi kullanmaya başlama
Bu makalede, Microsoft Azure portal kaynağınız için otomatik ölçeklendirme ayarlarınızı nasıl ayarlayabileceğinizi açıklar.

Azure Izleyici otomatik ölçeklendirme yalnızca [Sanal Makine Ölçek Kümeleri](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service-Web Apps](https://azure.microsoft.com/services/app-service/web/)ve [API Management Hizmetleri](../../api-management/api-management-key-concepts.md)için geçerlidir.

## <a name="discover-the-autoscale-settings-in-your-subscription"></a>Aboneliğinizdeki otomatik ölçeklendirme ayarlarını bulma

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4u7ts]

Azure Izleyici 'de otomatik ölçeklendirme için geçerli olan tüm kaynakları bulabilirsiniz. Adım adım yönergeler için aşağıdaki adımları kullanın:

1. Azure portal açın [.][1]
1. Sol bölmedeki Azure Izleyici simgesine tıklayın.
  ![Azure Izleyici 'yi açın][2]
1. Otomatik ölçeklendirme için geçerli olan tüm kaynakları görüntülemek için otomatik **ölçeklendirme** ' a tıklayın.
  ![Azure Izleyici 'de otomatik ölçeklendirmeyi bulma][3]

Belirli bir kaynak grubundaki kaynakları, belirli kaynak türlerini veya belirli bir kaynağı seçmek için listenin üst kısmındaki Filtre bölmesini kullanabilirsiniz.

Her kaynak için geçerli örnek sayısını ve otomatik ölçeklendirme durumunu bulacaksınız. Otomatik ölçeklendirme durumu şu olabilir:

- **Yapılandırılmadı**: Bu kaynak Için otomatik ölçeklendirmeyi henüz etkinleştirmediyseniz.
- **Etkin**: Bu kaynak Için otomatik ölçeklendirmeyi etkinleştirdiniz.
- **Devre dışı**: Bu kaynak Için otomatik ölçeklendirmeyi devre dışı bırakmış olabilirsiniz.

## <a name="create-your-first-autoscale-setting"></a>İlk otomatik ölçeklendirme ayarınızı oluşturma

Şimdi ilk otomatik ölçeklendirme ayarınızı oluşturmak için basit bir adım adım izlenecek yol ile başlayalım.

1. Azure Izleyici 'de **Otomatik ölçeklendirme** dikey penceresini açın ve ölçeklendirmek istediğiniz kaynağı seçin. (Aşağıdaki adımlarda bir Web uygulamasıyla ilişkili bir App Service planı kullanılır. [İlk ASP.NET Web uygulamanızı, 5 dakikada Azure 'da oluşturabilirsiniz.][4])
1. Geçerli örnek sayısının 1 olduğunu unutmayın. **Otomatik ölçeklendirmeyi etkinleştir**' e tıklayın.
  ![Yeni Web uygulaması için ölçek ayarı][5]
1. Ölçek ayarı için bir ad girin ve ardından **Kural Ekle**' ye tıklayın. Sağ tarafta bağlam bölmesi olarak açan ölçek kuralı seçeneklerine dikkat edin. Bu, varsayılan olarak, kaynağın CPU yüzdesi yüzde 70 ' ü aşarsa, örnek sayınız 1 ' i ölçeklendirmeye yönelik seçeneği ayarlar. Varsayılan değerlerinde bırakın ve **Ekle**' ye tıklayın.
  ![Web uygulaması için ölçek ayarı oluşturma][6]
1. Şimdi ilk ölçek kuralınızı oluşturdunuz. UX en iyi uygulamaları önerdiğine ve "kuralda en az bir ölçeğe sahip olmanız önerilir." Bunun için:

    a. **Kural ekle**'ye tıklayın.

    b. **İşleci** **küçüktür olarak ayarlayın**.

    c. **Eşiği** **20** olarak ayarlayın.

    d. **Sayıyı sayısını azaltmak** için **işlemi** ayarlayın.

   Artık CPU kullanımına göre ölçeklendirilmesi/ölçeklendirmeye yönelik bir ölçek ayarınız olmalıdır.
   ![CPU 'ya göre ölçeklendirin][8]
1. **Kaydet**’e tıklayın.

Tebrikler! Artık, Web uygulamanızı CPU kullanımına göre otomatik olarak ölçeklendirmek için ilk ölçek ayarınızı başarıyla oluşturdunuz.

> [!NOTE]
> Aynı adımlar, bir sanal makine ölçek kümesi veya bulut hizmeti rolü ile çalışmaya başlamak için geçerlidir.

## <a name="other-considerations"></a>Diğer önemli noktalar
### <a name="scale-based-on-a-schedule"></a>Zamanlamaya göre ölçeklendirin
CPU 'ya göre Ölçeklendirmeye ek olarak, ölçeklendirmenizi haftanın belirli günlerinde farklı şekilde ayarlayabilirsiniz.

1. **Ölçek koşulu Ekle**' ye tıklayın.
1. Ölçek modunu ve kuralları ayarlamak, varsayılan koşulla aynıdır.
1. Zamanlama için **belirli günleri Yinele** ' yi seçin.
1. Ölçek koşulunun uygulanması gereken günleri ve başlangıç/bitiş saatini seçin.

![Zamanlamayı zamanlamaya göre ölçeklendirin][9]
### <a name="scale-differently-on-specific-dates"></a>Belirli tarihlerde farklı ölçeklendirin
CPU 'ya göre Ölçeklendirmeye ek olarak, ölçeklendirmenizi belirli tarihler için farklı şekilde ayarlayabilirsiniz.

1. **Ölçek koşulu Ekle**' ye tıklayın.
1. Ölçek modunu ve kuralları ayarlamak, varsayılan koşulla aynıdır.
1. Zamanlama için **Başlangıç/bitiş tarihlerini belirt** ' i seçin.
1. Ölçek koşulunun uygulanması gereken başlangıç/bitiş tarihlerini ve başlangıç/bitiş saatini seçin.

![Tarihlere göre ölçeği Ölçeklendir][10]

### <a name="view-the-scale-history-of-your-resource"></a>Kaynağınızın ölçek geçmişini görüntüleyin
Kaynağınız yukarı veya aşağı ölçeklendirildiğinde, etkinlik günlüğüne bir olay kaydedilir. **Çalışma geçmişi** sekmesine geçerek, son 24 saat için kaynağınızın ölçek geçmişini görüntüleyebilirsiniz.

![Çalıştırma geçmişi][11]

Tüm ölçek geçmişini (90 güne kadar) görüntülemek istiyorsanız, **daha fazla ayrıntı görmek için buraya tıklayın ' ı** seçin. Etkinlik günlüğü açılır ve kaynak ve kategorim için otomatik ölçeklendirme önceden seçilir.

### <a name="view-the-scale-definition-of-your-resource"></a>Kaynağınızın ölçek tanımını görüntüleyin
Otomatik ölçeklendirme bir Azure Resource Manager kaynağıdır. **JSON sekmesine geçerek** , JSON 'da ölçek tanımını görüntüleyebilirsiniz.

![Ölçek tanımı][12]

Gerekirse JSON 'da doğrudan değişiklik yapabilirsiniz. Bu değişiklikler, kaydettikten sonra yansıtılır.

### <a name="disable-autoscale-and-manually-scale-your-instances"></a>Otomatik ölçeklendirmeyi devre dışı bırakma ve örneklerinizi el ile ölçeklendirme
Geçerli ölçek ayarınızı devre dışı bırakmak ve kaynağınızı el ile ölçeklendirmek istediğiniz zamanlar olabilir.

Üstteki **Otomatik ölçeklendirmeyi devre dışı bırak** düğmesine tıklayın.
![Otomatik ölçeklendirmeyi devre dışı bırak][13]

> [!NOTE]
> Bu seçenek yapılandırmanızı devre dışı bırakır. Ancak otomatik ölçeklendirmeyi yeniden etkinleştirdikten sonra geri dönebilirsiniz.

Artık ölçeklendirmek istediğiniz örneklerin sayısını el ile ayarlayabilirsiniz.

![El ile ölçek ayarla][14]

**Otomatik ölçeklendirmeyi etkinleştir** ' i ve sonra **Kaydet**' e tıklayarak her zaman otomatik ölçeklendirmeyi geri dönebilirsiniz.

### <a name="cool-down-period-effects"></a>Cool-ın period etkileri

Otomatik ölçeklendirme, örneklerin ölçeğini hızlı, tekrarlanmış ve aşağı ölçeklendirerek "flaşmaya" engel olmak için soğuk bir nokta kullanır.  Daha fazla bilgi için bkz. [Otomatik ölçeklendirme değerlendirme adımları](autoscale-understanding-settings.md#autoscale-evaluation).  Otomatik ölçeklendirme altyapısının nasıl izleneceği ve anlaşılmasına ilişkin diğer değerli bilgiler, [Otomatik ölçeklendirme En Iyi uygulamalarında](autoscale-best-practices.md#choose-the-thresholds-carefully-for-all-metric-types) bulunabilir ve sırasıyla [Otomatik ölçeklendirme sorunları giderilir](autoscale-troubleshoot.md) . 

## <a name="route-traffic-to-healthy-instances-app-service"></a>Trafiği sağlıklı örneklere yönlendir (App Service)

<a id="health-check-path"></a>

Azure Web uygulamanız birden çok örneğe ölçeklenirse, App Service sağlıklı örneklere trafiği yönlendirmek için örneklerinizi üzerinde sistem durumu denetimleri gerçekleştirebilir. Daha fazla bilgi edinmek için [App Service sistem durumu denetiminde bu makaleye](../../app-service/monitor-instances-health-check.md)bakın.

## <a name="moving-autoscale-to-a-different-region"></a>Otomatik ölçeklendirmeyi farklı bir bölgeye taşıma
Bu bölümde, Azure otomatik ölçeklendirmeyi aynı abonelik ve kaynak grubu altındaki başka bir bölgeye nasıl taşıyacağınız açıklanmaktadır. Otomatik ölçeklendirme ayarlarını taşımak için REST API kullanabilirsiniz.
### <a name="prerequisite"></a>Önkoşul
1. Aboneliğin ve kaynak grubunun kullanılabilir olduğundan ve hem kaynak hem de hedef bölgelerin ayrıntılarının aynı olduğundan emin olun.
1. Azure otomatik ölçeklendirme 'nın, [taşımak Istediğiniz Azure bölgesinde](https://azure.microsoft.com/global-infrastructure/services/?products=monitor&regions=all)kullanılabilir olduğundan emin olun.

### <a name="move"></a>Taşı
Yeni ortamda bir otomatik ölçeklendirme ayarı oluşturmak için [REST API](/rest/api/monitor/autoscalesettings/createorupdate) kullanın. Hedef bölgede oluşturulan otomatik ölçeklendirme ayarı, kaynak bölgedeki otomatik ölçeklendirme ayarının bir kopyası olacaktır.

Kaynak bölgedeki otomatik ölçeklendirme ayarıyla ilişkilendirmede oluşturulan [Tanılama ayarları](../essentials/diagnostic-settings.md) taşınamaz. Tekrar satış ayarları oluşturulduktan sonra, hedef bölgede tanılama ayarlarını yeniden oluşturmanız gerekir. 

### <a name="learn-more-about-moving-resources-across-azure-regions"></a>Azure bölgelerindeki kaynakları taşıma hakkında daha fazla bilgi edinin
Azure 'da bölgeler ve olağanüstü durum kurtarma arasında kaynakları taşıma hakkında daha fazla bilgi edinmek için bkz. [kaynakları yeni bir kaynak grubuna veya aboneliğe taşıma](../../azure-resource-manager/management/move-resource-group-and-subscription.md)

## <a name="next-steps"></a>Sonraki adımlar
- [Aboneliğinizdeki tüm otomatik ölçeklendirme motoru işlemlerini izlemek için bir etkinlik günlüğü uyarısı oluşturun](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Aboneliğinizde başarısız olan tüm otomatik ölçeklendirme ölçeği Ölçeklendirmesi/genişleme işlemlerini izlemek için bir etkinlik günlüğü uyarısı oluşturun](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)


<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/autoscale-get-started/azure-monitor-launch.png
[3]: ./media/autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: ../../app-service/quickstart-dotnetcore.md
[5]: ./media/autoscale-get-started/scale-setting-new-web-app.png
[6]: ./media/autoscale-get-started/create-scale-setting-web-app.png
[7]: ./media/autoscale-get-started/scale-in-recommendation.png
[8]: ./media/autoscale-get-started/scale-based-on-cpu.png
[9]: ./media/autoscale-get-started/scale-condition-schedule.png
[10]: ./media/autoscale-get-started/scale-condition-dates.png
[11]: ./media/autoscale-get-started/scale-history.png
[12]: ./media/autoscale-get-started/scale-definition-json.png
[13]: ./media/autoscale-get-started/disable-autoscale.png
[14]: ./media/autoscale-get-started/set-manualscale.png
