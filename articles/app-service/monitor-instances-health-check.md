---
title: App Service örneklerinin sistem durumunu izleme
description: Sistem durumu denetimini kullanarak App Service örneklerinin sistem durumunu izlemeyi öğrenin.
keywords: Azure App Service, Web uygulaması, sistem durumu denetimi, yönlendirme trafiği, sağlıklı örnekler, yol, izleme,
author: msangapu-msft
ms.topic: article
ms.date: 12/03/2020
ms.author: msangapu
ms.openlocfilehash: e9d92c60e74ac9106246ccd445afaca926065e5f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871206"
---
# <a name="monitor-app-service-instances-using-health-check"></a>Sistem durumu denetimini kullanarak App Service örnekleri izleme

![Sistem durumu denetimi hatası][2]

Bu makale, App Service örnekleri izlemek için Azure portal sistem durumu denetimini kullanır. Sistem durumu denetimi, sağlıksız örnekleri kaldırarak uygulamanızın kullanılabilirliğini artırır. [App Service planınız](./overview-hosting-plans.md) , sistem durumu denetimini kullanmak için iki veya daha fazla örneğe ölçeklendirmelidir. Sistem durumu denetim yolu, uygulamanızın kritik bileşenlerini denetlemelidir. Örneğin, uygulamanız bir veritabanına ve bir mesajlaşma sistemine bağımlıysa, sistem durumu denetimi uç noktasının bu bileşenlere bağlanması gerekir. Uygulama kritik bir bileşene bağlanamıyorsa, uygulamanın sağlıksız olduğunu göstermek için yol 500 düzeyinde bir yanıt kodu döndürmelidir.

## <a name="what-app-service-does-with-health-checks"></a>Durum denetimleri ile ne App Service

- Uygulamanızda bir yol verildiğinde, sistem durumu denetimi App Service uygulamanızın tüm örneklerinde bu yola 1 dakikalık aralıklarda ping atar.
- Bir örnek iki veya daha fazla istekten sonra 200-299 (dahil) arasında bir durum kodu ile yanıt vermezse veya ping işlemine yanıt vermezse, sistem sağlıksız olduğunu belirler ve kaldırır.
- Kaldırma işleminden sonra sistem durumu denetimi sağlıksız örneğe ping vermeye devam eder. Yanıt başarıyla yanıtlanmaya devam ederse, App Service arka plandaki VM 'yi sağlıklı bir duruma döndürmek için bir çabayla yeniden başlatır.
- Bir örnek bir saat için sağlıksız kalırsa, yeni örnekle birlikte değişir.
- Ayrıca, ölçeklendirirken veya dışarı App Service, yeni örneklerin hazır olmasını sağlamak için sistem durumu denetim yoluna ping atar.

> [!NOTE]
> Sistem durumu denetimi 302 yeniden yönlendirmeyi takip etmez. En fazla bir örnek, App Service plan başına günde en fazla üç örnek olacak şekilde saat başına değiştirilmeyecektir.
>

## <a name="enable-health-check"></a>Sistem durumu denetimini etkinleştir

![Azure portalında sistem durumu denetimi gezintisi][3]

- Sistem durumu denetimini etkinleştirmek için Azure portal göz atın ve App Service uygulamanızı seçin.
- **İzleme** altında **sistem durumu denetimi**' ni seçin.
- **Etkinleştir** ' i seçin ve uygulamanızda, veya gibi GEÇERLI bir URL yolu sağlayın `/health` `/api/health` .
- **Kaydet**’e tıklayın.

> [!CAUTION]
> Sistem durumu denetimi yapılandırma değişiklikleri uygulamanızı yeniden başlatın. Üretim uygulamalarına etkisini en aza indirmek için, [hazırlama yuvaları yapılandırmanızı](deploy-staging-slots.md) ve üretime değiştirmeyi öneririz.
>

### <a name="configuration"></a>Yapılandırma

Sistem durumu denetimi seçeneklerini yapılandırmanın yanı sıra, aşağıdaki [uygulama ayarlarını](configure-common.md)da yapılandırabilirsiniz:

| Uygulama ayarı adı | İzin verilen değerler | Description |
|-|-|-|
|`WEBSITE_HEALTHCHECK_MAXPINGFAILURES` | 2 - 10 | En fazla ping başarısızlığı sayısı. Örneğin, olarak ayarlandığında `2` , örneklerinizin başarısız ping işlemleri sonrasında kaldırılacak `2` . Ayrıca, ölçeği büyütme veya küçültme sırasında, yeni örneklerin hazır olmasını sağlamak için sistem durumu denetim yoluna ping App Service. |
|`WEBSITE_HEALTHCHECK_MAXUNHEALTYWORKERPERCENT` | 0 - 100 | Sağlıklı olmayan örneklere engel olmak için örneklerin yarısını hariç tutulamayacak. Örneğin, bir App Service planı dört örneğe ölçeklenirse ve üçü uygun değilse, en fazla iki durum hariç tutulur. Diğer iki örnek (bir sağlıklı ve bir sağlıksız) istekleri almaya devam edecektir. Tüm örneklerin sağlıksız olduğu en kötü durum senaryosunda, hiçbiri dışlanacaktır. Bu davranışı geçersiz kılmak için, uygulama ayarını ve arasında bir değere `0` ayarlayın `100` . Daha yüksek bir değer, daha sağlıksız örneklerin kaldırılabileceği anlamına gelir (varsayılan değer 50 ' dir). |

#### <a name="authentication-and-security"></a>Kimlik doğrulama ve güvenlik

Sistem durumu denetimi App Service kimlik doğrulaması ve yetkilendirme özellikleriyle tümleştirilir. Bu güvenlik özellikleri etkinse ek ayar yapmanız gerekmez. Ancak, kendi kimlik doğrulama sisteminizi kullanıyorsanız, sistem durumu denetimi yolu anonim erişime izin vermelidir. Site **yalnızca http 'yi** etkinleştirmişse, sistem durumu denetımı Isteği http **s** aracılığıyla gönderilir.

Büyük Kurumsal Geliştirme ekiplerinin genellikle sunulan API 'Ler için güvenlik gereksinimlerine uyması gerekir. Sistem durumu denetimi uç noktasının güvenliğini sağlamak için öncelikle, uygulama erişimini kısıtlamak üzere [IP kısıtlamaları](app-service-ip-restrictions.md#set-an-ip-address-based-rule), [istemci sertifikaları](app-service-ip-restrictions.md#set-an-ip-address-based-rule)veya bir sanal ağ gibi özellikleri kullanmanız gerekir. Gelen istek eşleşmelerini isteyerek sistem durumu denetimi uç noktasının güvenliğini sağlayabilirsiniz `User-Agent` `HealthCheck/1.0` . İstek önceki güvenlik özellikleri tarafından zaten güvenli hale getirildiğinden User-Agent taklit olamaz.

## <a name="monitoring"></a>İzleme

Uygulamanızın sistem durumu Denetim yolunu sağladıktan sonra, Azure Izleyici 'yi kullanarak sitenizin sistem durumunu izleyebilirsiniz. Portaldaki **sistem durumu denetimi** dikey penceresinde, üst araç çubuğundan **ölçümler** ' e tıklayın. Bu, sitenin geçmiş sistem durumunu görebileceğiniz ve yeni bir uyarı kuralı oluşturabileceğiniz yeni bir dikey pencere açar. Sitelerinizi izleme hakkında daha fazla bilgi için [bkz. Azure izleyici Kılavuzu](web-sites-monitor.md).

## <a name="limitations"></a>Sınırlamalar

Premium Işlevler sitelerinde sistem durumu denetimi etkinleştirilmemelidir. Premium Işlevlerinin hızlı ölçeklendirilmesi nedeniyle, sistem durumu denetimi istekleri HTTP trafiğinden gereksiz dalgalanmalara neden olabilir. Premium Işlevlerin ölçeklendirme kararlarını bilgilendirmek için kullanılan kendi iç sistem durumu araştırmaları vardır.

## <a name="next-steps"></a>Sonraki adımlar
- [Aboneliğinizdeki tüm otomatik ölçeklendirme motoru işlemlerini izlemek için bir etkinlik günlüğü uyarısı oluşturun](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Aboneliğinizde başarısız olan tüm otomatik ölçeklendirme ölçeği Ölçeklendirmesi/genişleme işlemlerini izlemek için bir etkinlik günlüğü uyarısı oluşturun](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

[1]: ./media/app-service-monitor-instances-health-check/health-check-success-diagram.png
[2]: ./media/app-service-monitor-instances-health-check/health-check-failure-diagram.png
[3]: ./media/app-service-monitor-instances-health-check/azure-portal-navigation-health-check.png
