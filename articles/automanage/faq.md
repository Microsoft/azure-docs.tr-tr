---
title: Sanal makineler için Azure oto yönetimi SSS
description: Sanal makineler için Azure oto yönetimi hakkında sık sorulan soruların yanıtları.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: f5a9ff7661fda372631d1bb912b1c137b37c7e07
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363368"
---
# <a name="frequently-asked-questions-for-azure-automanage-for-vms"></a>VM 'Ler için Azure oto yönetimi hakkında sık sorulan sorular

Bu makalede, [sanal makineler Için Azure oto yönetimi](automanage-virtual-machines.md)hakkında en yaygın soruların yanıtları sağlanmaktadır.

Azure sorununuz bu makalede giderilmemişse, [MSDN ve Stack Overflow](https://azure.microsoft.com/support/forums/)Azure forumlarını ziyaret edin. Sorununuzu bu forumlara gönderebilir veya [ @AzureSupport Twitter 'da](https://twitter.com/AzureSupport)ilan edebilirsiniz. Ayrıca, bir Azure destek isteği de gönderebilirsiniz. Destek isteği göndermek için [Azure Desteği sayfasında](https://azure.microsoft.com/support/options/) **Destek Al**' ı seçin.


## <a name="azure-automanage-for-virtual-machines"></a>Sanal makineler için Azure oto yönetimi

**Azure oto yönetimi 'ni etkinleştirmek için gereken tüm Önkoşullar nelerdir?**

Aşağıda, Azure oto yönetimi 'ni etkinleştirme önkoşulları verilmiştir:
- Desteklenen [Windows Server sürümleri](automanage-windows-server.md#supported-windows-server-versions) ve [Linux kaldırmalar](automanage-linux.md#supported-linux-distributions-and-versions)
- VM 'Ler desteklenen bir bölgede olmalıdır
- Kullanıcının doğru izinleri olması gerekir
- Yalnızca ölçek olmayan küme VM 'Leri
- Oto yönetimi şu anda korumalı alan aboneliklerini desteklemiyor

**Oto yönetimi etkinleştirmek için hangi Azure RBAC izni gerekir?**

Mevcut bir oto Yönet hesabı olan bir VM 'de, oto Yönet 'i etkinleştirirseniz, VM 'nin bulunduğu kaynak grubuna katkıda bulunan rolü gerekir.

Etkinleştirme sırasında yeni bir bir bir bir bir bir bir bir bir bir bir bir bir bir oto Yönet hesabı kullanıyorsanız, aboneliğe sahip veya katılımcı + kullanıcı erişimi Yöneticisi rolüne sahip olmanız gerekir.


**Hangi bölgeler destekleniyor?**

Desteklenen bölgelerin tam listesine [buradan](./automanage-virtual-machines.md#supported-regions)ulaşabilirsiniz.


**Azure otomatik yönetimi hangi özellikleri otomatikleştirin?**

Oto yönetimi, [burada](automanage-virtual-machines.md)LISTELENEN hizmetlerin VM yaşam döngüsü boyunca kaydeder, yapılandırır ve izler.

**Azure, Azure Arc özellikli VM 'lerle çalışır mi?**

Şu anda oto yönetimi, Arc özellikli VM 'Leri desteklemiyor.

**Azure oto yönetimi 'nde konfigürasyonları özelleştirebilir miyim?**

Müşteriler, Yapılandırma tercihleri aracılığıyla Azure Backup bekletme gibi belirli hizmetlere yönelik ayarları özelleştirebilir. Değiştirilebilen ayarların tam listesi için belgelerimize [bakın.](automanage-virtual-machines.md#customizing-an-environment-using-preferences)


**Azure, hem Linux hem de Windows VM 'leriyle çalışır mı?**

Evet, desteklenen [Windows Server sürümleri](automanage-windows-server.md#supported-windows-server-versions) ve [Linux destekleri](automanage-linux.md#supported-linux-distributions-and-versions)bölümüne bakın.


**Yalnızca bir VM kümesi üzerinde seçmeli bir şekilde yedekleme uygulayabilir miyim?**

Seçilen yeni ve mevcut VM 'Lerde tıklama ve işaret basitliği ile, oto yönetimi etkinleştirilebilir. Aynı zamanda, oto yönetimi de devre dışı bırakılabilir.


**Azure, sanal makine ölçek kümesindeki VM 'Leri destekler mi?**

Hayır, Azure oto yönetimi şu anda bir sanal makine ölçek kümesindeki VM 'Leri desteklemiyor.


**Azure 'un maliyeti ne kadar sürer?**

Azure oto yönetimi, genel önizleme aşamasında ek ücret ödemeden kullanılabilir. Azure Backup gibi bağlı Azure kaynakları maliyet doğuracaktır.


**Azure ilkesi aracılığıyla bir oto Yönet uygulayabilir miyim?**

Evet, otomatik olarak tanımlanan kapsamdaki tüm VM 'lere otomatik Yönet uygulayacak yerleşik bir ilkemiz var. Ayrıca, ortam yapılandırmasını (DevTest veya üretim), sizin de oto Yönet hesabınızla birlikte belirtmeniz gerekir. [Burada](virtual-machines-policy-enable.md)Azure Ilkesi aracılığıyla oto yönetimi etkinleştirme hakkında daha fazla bilgi edinin.


**Bir oto Yönet hesabı nedir?**

Otomatikmanage hesabı, güvenlik bağlamını sağlayan bir MSI (Yönetilen Hizmet Kimliği) veya otomatik işlemlerin gerçekleştirileceği kimliği sağlar.


**Oto yönetimi etkinleştirilirken, seçili VM 'Lerin yanı sıra ek VM 'Leri etkilerler.**

VM 'niz mevcut bir Log Analytics çalışma alanına bağlıysa, bu çözümleri uygulamak için bu çalışma alanını yeniden kullanacağız: Değişiklik İzleme, envanter ve Güncelleştirme Yönetimi. Bu çalışma alanına bağlı tüm VM 'Ler, bu çözümlerin etkinleştirilmesini sağlar.


**VM 'nin ortamını değiştirebilir miyim?**

Şu anda, bu VM için, bu sanal makine için, oto yönetimini devre dışı bırakmanız ve ardından, istenen ortam ve tercihlerle birlikte, yeniden etkinleştirmeniz gerekir.


**VM 'im, Güncelleştirme Yönetimi gibi bir hizmet için zaten yapılandırılmışsa otomatik yönetilecek yeniden yapılandırılacak mi?**
Hayır, tekrar Yönet bunu yeniden yapılandırmayacak. Bu hizmetle ilişkili kaynakları DRFT için izlemeye başlayacağız.


**Neden sanal makinelerimin, oto Yönet portalında başarısız bir durumu var?**

Durumu *başarısız* olarak görürseniz, birkaç farklı şekilde dağıtımda sorun gidermeye bakabilirsiniz:
* **Kaynak grupları**' na gidin, kaynak grubunuzu seçin, **dağıtımlar** ' a tıklayın ve hata ayrıntılarıyla birlikte *başarısız* durumuna bakın.
* **Abonelikler**' e gidin, kaynak grubunuzu seçin, **dağıtımlar** ' a tıklayın ve hata ayrıntılarıyla birlikte *başarısız* durumuna bakın.
* Ayrıca, "yapılandırma profili atamaları oluşturma veya güncelleştirme" için bir giriş içeren bir sanal makinenin etkinlik günlüğünü de ziyaret edebilirsiniz. Bu, dağıtımınız hakkında daha fazla ayrıntı de içerebilir.

**Oto yönetimi için nasıl sorun giderme desteği alabilirim?**

[Teknik destek talebi bileti](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)oluşturabilirsiniz. **Hizmet** seçeneği için *izleme ve yönetim* bölümünde, ara ' yı arayın *ve seçin.*


## <a name="next-steps"></a>Sonraki adımlar

Azure portal sanal makineler için oto yönetimini etkinleştirmeyi deneyin.

> [!div class="nextstepaction"]
> [Azure portal sanal makineler için oto yönetimini etkinleştir](quick-create-virtual-machines-portal.md)