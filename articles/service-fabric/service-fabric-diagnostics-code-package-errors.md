---
title: Service Fabric kullanarak yaygın kod paketi hatalarını tanılama Microsoft Docs
description: Azure Service Fabric ortak kod paketi hatalarını nasıl giderebileceğinizi öğrenin
services: service-fabric
documentationcenter: .net
author: grzuber
manager: gkhanna
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/09/2019
ms.author: grzuber
ms.openlocfilehash: 320a55e8b14648b1d7e256855582ab31846a63cf
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249214"
---
# <a name="diagnose-common-code-package-errors-by-using-service-fabric"></a>Service Fabric kullanarak ortak kod paketi hatalarını tanılayın

Bu makalede, bir kod paketinin beklenmedik bir şekilde sonlandırılması için ne anlama geldiğini açıklanmaktadır. Sorun giderme adımlarıyla birlikte yaygın hata kodlarının olası nedenleriyle ilgili öngörüler sağlar.

## <a name="when-does-a-process-or-container-terminate-unexpectedly"></a>Bir işlem veya kapsayıcı beklenmedik bir şekilde sona erdiğinde

Azure Service Fabric bir kod paketini başlatma isteği aldığında, uygulama ve hizmet bildirimlerinde ayarlanan seçeneklere göre ortamı yerel sistemde hazırlamaya başlar. Bu hazırlıklar, ağ uç noktalarını veya kaynaklarını ayırma, güvenlik duvarı kurallarını yapılandırma veya kaynak idare kısıtlamalarını ayarlama içerebilir. 

Ortam düzgün yapılandırıldıktan sonra, Service Fabric kod paketini getirmeye çalışır. İşletim sistemi veya kapsayıcı çalışma zamanı, işlemin veya kapsayıcının başarıyla etkinleştirildiğini bildirirse, bu adım başarılı olarak kabul edilir. Etkinleştirme başarısız olursa, SFX ' de aşağıdakine benzer bir sistem durumu iletisi görmeniz gerekir:

```
There was an error during CodePackage activation. Service host failed to activate. Error: 0xXXXXXXXX
```

Kod paketi başarıyla etkinleştirildikten sonra, Service Fabric ömrünü izlemeye başlar. Bu noktada, bir işlem veya kapsayıcı bir dizi nedenden dolayı dilediğiniz zaman sonlandırılabilir. Örneğin, bir DLL 'yi başlamayabilir veya işletim sisteminde Masaüstü yığın alanı tükeniyor olabilir. Kod paketiniz sonlandırılırsa, SFX ' de aşağıdaki sistem durumu iletisini görmeniz gerekir:

```
The process/container terminated with exit code: XXXXXXXX. Please look at your application logs/dump or debug your code package for more details. For information about common termination errors, please visit https://aka.ms/service-fabric-termination-errors
```

Bu sistem durumu iletisindeki çıkış kodu, işlemin veya kapsayıcının neden sonlandırıldığına ilişkin sağladığı tek Clue 'dir. Yığın herhangi bir düzeyi tarafından oluşturulabilir. Örneğin, bu çıkış kodu bir işletim sistemi hatası veya .NET sorunuyla ilişkili olabilir veya kodunuz tarafından oluşturulmuş olabilir. Sonlandırma çıkış kodlarının ve olası çözümlerin kaynağını tanılamak için bir başlangıç noktası olarak bu makaleyi kullanın. Ancak bunların yaygın senaryolar için genel çözümler olduğunu ve gördüğünüz hata için uygulanmayabileceğini aklınızda bulundurun.

## <a name="how-can-i-tell-if-service-fabric-terminated-my-code-package"></a>Service Fabric kod paketmi sonlandırdığını nasıl anlayabilirim?

Service Fabric, kod paketinizi pek çok nedenden dolayı sonlandırmaktan sorumlu olabilir. Örneğin, Yük Dengeleme amaçları için kod paketini başka bir düğüme yerleştirmeyi kararı verebilir. Aşağıdaki tabloda yer alan çıkış kodlarından herhangi birini görürseniz Service Fabric, kod paketinizi sonlandırdığını doğrulayabilirsiniz.

>[!NOTE]
> İşlem veya Kapsayıcınız aşağıdaki tablodaki kodlardan farklı bir çıkış koduyla sonlandığında Service Fabric sonlandırmaktan sorumlu değildir.

Çıkış kodu | Açıklama
--------- | -----------
7147 | Service Fabric, bir CTRL + C sinyali göndererek işlemi veya kapsayıcıyı düzgün şekilde kapatmadığını gösterir.
7148 | Service Fabric işlemi veya kapsayıcıyı sonlandırdığını gösterir. Bazen, bu hata kodu, bir CTRL + C sinyali gönderdikten sonra işlemin veya kapsayıcının zamanında yanıt vermediğini ve sonlandırılması gerekiyordu.


## <a name="other-common-error-codes-and-their-potential-fixes"></a>Diğer yaygın hata kodları ve olası düzeltmeleri

Çıkış kodu | Onaltılık değer | Kısa açıklama | Kök neden | Olası çözüm
--------- | --------- | ----------------- | ---------- | -------------
3221225794 | 0xc0000142 | STATUS_DLL_INIT_FAILED | Bu hata bazen makinede Masaüstü yığın alanı tükenmekte olduğu anlamına gelir. Bu neden özellikle, düğümde çalışan uygulamanıza ait çok sayıda işlem varsa olabilir. | Programınız CTRL + C sinyallerine yanıt vermek üzere derlenmediyse, küme bildiriminde **EnableActivateNoWindow** ayarını etkinleştirebilirsiniz. Bu ayarı etkinleştirmek, kod paketinizin GUI penceresi olmadan çalışacağı ve CTRL + C sinyalleri almamayacak anlamına gelir. Bu eylem Ayrıca her bir işlemin kullandığı Masaüstü yığın alanı miktarını azaltır. Kod paketinizin CTRL + C sinyalleri alması gerekiyorsa, düğümünüz masaüstü yığınının boyutunu artırabilirsiniz.
3762504530 | 0xe0434352 | Yok | Bu değer, yönetilen koddan (.NET) işlenmeyen bir özel durum için hata kodunu temsil eder. | Bu çıkış kodu, uygulamanızın işlenmemiş kalan ve işlemi sonlandıran bir özel durum yaptığını gösterir. Bu hatayı neyin tetiklediğini belirleyen ilk adım olarak uygulamanızın günlüklerinde ve döküm dosyalarında hata ayıklayın.

## <a name="next-steps"></a>Sonraki adımlar

* [Diğer yaygın senaryoları tanılama](service-fabric-diagnostics-common-scenarios.md)hakkında daha fazla bilgi edinin.
* Azure izleyici günlüklerine yönelik daha ayrıntılı bir genel bakış ve [Azure izleyicisine genel bakış](../operations-management-suite/operations-management-suite-overview.md)' ı okuyarak neler sundukları.
* Algılama ve tanılama konusunda yardım için Azure Izleyici günlükleri [uyarısı](../log-analytics/log-analytics-alerts.md) hakkında daha fazla bilgi edinin.
* [Günlük araması ve](../log-analytics/log-analytics-log-searches.md) Azure izleyici günlüklerinin bir parçası olarak sunulan özellikleri sorgulama hakkında bilgi edinin.
