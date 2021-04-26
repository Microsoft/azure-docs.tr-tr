---
title: Tüm Anahtar Kasası nesnelerinde geçici silme özelliğini etkinleştirme-Azure Key Vault | Microsoft Docs
description: Tüm Anahtar kasaları için geçici silme benimsemek ve çakışma hatalarından kaçınmak için uygulama ve yönetim değişiklikleri yapmak üzere bu belgeyi kullanın.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: mbaldwin
ms.openlocfilehash: 7e1b2ee95864affa6e5e72e1f8354767dc95bdb1
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753335"
---
# <a name="soft-delete-will-be-enabled-on-all-key-vaults"></a>Geçici silme, tüm anahtar kasaları üzerinde etkinleştirilecek

> [!WARNING]
> Son değişiklik: geçici silme işlemini devre dışı bırakma özelliği yakında kullanım dışı bırakılacak. Azure Key Vault kullanıcılar ve Yöneticiler, anahtar kasalarında doğrudan geçici silme olanağı sağlamalıdır.
>
> Azure Key Vault yönetilen HSM için, geçici silme varsayılan olarak etkindir ve devre dışı bırakılamaz.

Gizli dizi koruması olmayan bir anahtar kasasından gizli dizi silindiğinde, gizli dizi kalıcı olarak silinir. Kullanıcılar, Anahtar Kasası oluşturma sırasında şu anda geçici silme işlemini devre dışı bırakabilirsiniz. Ancak, Microsoft, gizli dizileri Kullanıcı tarafından yanlışlıkla veya kötü amaçlı olarak silinmeye karşı korumak için tüm anahtar kasalarında kısa süre önce geçici silme korumasını etkinleştirecektir. Kullanıcılar artık geçici silme işlemini kapatamaz veya devre dışı bırakır.

:::image type="content" source="../media/softdeletediagram.png" alt-text="Yumuşak silme koruması olmadan bir anahtar kasasının geçici silme koruması ile nasıl silineceğini gösteren diyagram.":::

Geçici silme işleviyle ilgili tüm ayrıntılar için bkz. [Azure Key Vault geçici görünüm](soft-delete-overview.md).

## <a name="can-my-application-work-with-soft-delete-enabled"></a>Uygulamamın geçici silme etkin bir şekilde çalışmasına mi?

> [!Important] 
> Anahtar kasaları için geçici silme özelliğini açmadan önce aşağıdaki bilgileri dikkatle gözden geçirin.

Anahtar Kasası adları genel olarak benzersizdir. Bir anahtar kasasında depolanan gizli dizi adları da benzersizdir. Bir anahtar kasasının adını veya geçici olarak silinen durumunda bulunan Anahtar Kasası nesnesini yeniden kullanamazsınız. 

Örneğin, uygulamanız "kasa A" adlı bir Anahtar Kasası oluşturup daha sonra "kasa A" öğesini silerse, Anahtar Kasası geçici olarak silinen duruma taşınır. Uygulamanız, Anahtar Kasası geçici olarak silinen durumdan temizlenene kadar "kasa A" adlı başka bir anahtar kasasını yeniden oluşturamaz. 

Ayrıca, uygulamanız `test key` "kasa a" içinde adlı bir anahtar oluşturursa ve daha sonra bu anahtarı silerse, `test key` `test key` nesne geçici olarak silinen durumdan temizlenene kadar uygulamanız "kasa a" içinde adlı yeni bir anahtar oluşturamayacak. 

Bir Anahtar Kasası nesnesini silmeye ve geçici olarak silinen durumdan çıkmadan aynı adla yeniden oluşturmaya çalışılıyor, çakışma hatalarına neden olabilir. Bu hatalar uygulamalarınızın veya otomasyonunun başarısız olmasına neden olabilir. Aşağıdaki gerekli uygulama ve yönetim değişikliklerini yapmadan önce dev ekibine başvurun. 

### <a name="application-changes"></a>Uygulama değişiklikleri

Uygulamanız, geçici silme özelliğinin etkinleştirilmediğini ve silinen gizli dizi ya da Anahtar Kasası adlarının hemen yeniden kullanım için kullanılabilir olduğunu varsaydıysa, uygulama mantığınıza aşağıdaki değişiklikleri yapmanız gerekir.

1. Özgün anahtar kasasını veya parolayı silin.
1. Geçici silme durumunda anahtar kasasını veya gizli anahtarı temizleyin.
1. Temizleme işleminin tamamlanmasını bekleyin. Hemen yeniden oluşturma bir çakışmaya neden olabilirler.
1. Anahtar kasasını aynı adla yeniden oluşturun.
1. Oluşturma işlemi yine de bir ad çakışması hatasıyla sonuçlanırsa, anahtar kasasını yeniden oluşturmayı tekrar deneyin. Azure DNS kayıtların en kötü durum senaryosunda güncelleştirilmesi 10 dakika sürebilir.

### <a name="administration-changes"></a>Yönetim değişiklikleri

Gizli dizileri kalıcı olarak silme erişimi gerektiren güvenlik ilkelerine bu gizli dizileri ve anahtar kasasını temizlemek için daha fazla erişim ilkesi izni verilmesi gerekir.

Önemli kasalarınızda, geçici silme özelliğinin kapalı olduğunu belirten tüm Azure ilkelerini devre dışı bırakın. Bu sorunu, ortamınıza uygulanan Azure ilkelerini denetleyen bir yöneticiye iletmek zorunda kalabilirsiniz. Bu ilke devre dışı bırakılmadıysa, uygulanan ilkenin kapsamında yeni anahtar kasaları oluşturma özelliğini kaybedebilirsiniz.

Kuruluşunuz yasal uyumluluk gereksinimlerine tabidir ve Silinen anahtar kasalarının ve parolaların uzun bir süre için kurtarılabilir bir durumda kalmasına izin vermediyse, kuruluşunuzun standartlarını karşılamak için geçici silme bekletme süresini ayarlamanız gerekir. Saklama süresini en son 7 ile 90 gün arasında yapılandırabilirsiniz.

## <a name="procedures"></a>Yordamlar

### <a name="audit-your-key-vaults-to-check-if-soft-delete-is-enabled"></a>Geçici silme özelliğinin etkin olup olmadığını denetlemek için anahtar kasalarınızı denetleme

1. Azure portalında oturum açın.
1. **Azure ilkesi** araması yapın.
1. **Tanımlar**' ı seçin.
1. **Kategori** altında, filtrede **Key Vault** ' yi seçin.
1. Key Vault, **geçici silme etkinleştirilmiş ilkesine sahip olmalıdır** .
1. **Ata**’yı seçin.
1. Kapsamını aboneliğiniz olarak ayarlayın.
1. İlke efektinin **Denetim** olarak ayarlandığından emin olun.
1. **Gözden geçir + Oluştur**’u seçin. Ortamınızın tam taramasının tamamlanması 24 saate kadar sürebilir.
1. **Azure ilke** bölmesinde, **Uyumluluk**' i seçin.
1. Uyguladığınız ilkeyi seçin.

Artık hangi anahtar kasalarının etkin (uyumlu kaynaklar) olarak filtreleneceğini ve hangi anahtar kasalarının yazılım için etkin silme özelliğinin (uyumlu olmayan kaynaklar) olduğunu görebilirsiniz.

### <a name="turn-on-soft-delete-for-an-existing-key-vault"></a>Mevcut bir Anahtar Kasası için geçici silmeyi aç

1. Azure portalında oturum açın.
1. Anahtar kasanızı arayın.
1. **Ayarlar** altında **Özellikler** ' i seçin.
1. **Geçici silme** altında **Bu kasanın ve onun nesnelerinin kurtarılmasını etkinleştir** seçeneğini belirleyin.
1. Geçici silme için saklama süresini ayarlayın.
1. **Kaydet**’i seçin.

### <a name="grant-purge-access-policy-permissions-to-a-security-principal"></a>Güvenlik sorumlusu için Temizleme erişim ilkesi izinleri verme

1. Azure portalında oturum açın.
1. Anahtar kasanızı arayın.
1. **Ayarlar** altında **erişim ilkeleri** ' ni seçin.
1. Erişim vermek istediğiniz hizmet sorumlusu ' nı seçin.
1. **Ayrıcalıklı işlemleri** görene kadar, **anahtar**, **gizli** ve **sertifika izinleri** altındaki her bir açılan menüde geçiş yapın. **Temizleme** iznini seçin.

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

### <a name="does-this-change-affect-me"></a>Bu değişiklik beni etkiler mi?

Zaten geçici silme özelliği açıksa veya aynı ada sahip Anahtar Kasası nesnelerini silip yeniden oluşturmazsanız, büyük olasılıkla anahtar kasasının davranışında herhangi bir değişiklik fark edilmezsiniz.

Aynı adlandırma kurallarına göre Anahtar Kasası nesnelerini silen ve yeniden oluşturan bir uygulamanız varsa, beklenen davranışı korumak için uygulama mantığınızdaki değişiklikleri yapmanız gerekir. Bu makaledeki [uygulama değişiklikleri](#application-changes) bölümüne bakın.

### <a name="how-do-i-benefit-from-this-change"></a>Bu değişiklikten faydalanmak Nasıl yaparım? misiniz?

Geçici silme koruması, kuruluşunuza yanlışlıkla veya kötü amaçlı silme için başka bir koruma katmanı sağlar. Bir Anahtar Kasası Yöneticisi olarak, erişimi hem kurtarma izinleri hem de Temizleme izinleri ile kısıtlayabilirsiniz.

Kullanıcı yanlışlıkla bir Anahtar Kasası veya gizli dizi silerse, gizli dizi veya anahtar kasasını kalıcı olarak sildikleri riski oluşturmadan gizli dizi erişimini kurtarmak için onlara erişim izinleri verebilirsiniz. Bu self servis işlemi ortamınızdaki kapalı kalma süresini en aza indirir ve sırlarınızın kullanılabilirliğini garanti eder.

### <a name="how-do-i-find-out-if-i-need-to-take-action"></a>Nasıl yaparım?, eyleme ihtiyacım olup olmadığını öğrenmek mi istiyorsunuz?

Bu makaledeki [geçici silme özelliğinin etkin olup olmadığını denetlemek için anahtar kasalarınızı denetleme](#audit-your-key-vaults-to-check-if-soft-delete-is-enabled) bölümündeki adımları izleyin. Bu değişiklik, geçici silme özelliği açık olmayan tüm anahtar kasasını etkiler.

### <a name="what-action-do-i-need-to-take"></a>Ne yapmam gerekir?

Uygulama mantığınıza değişiklik yapmanız gerekmiyorsa, tüm anahtar kasalarınızda geçici silme özelliğini açın.

### <a name="when-do-i-need-to-take-action"></a>Ne zaman işlem yapmam gerekir?

Uygulamalarınızın etkilenmemesini sağlamak için, anahtar kasalarınızda mümkün olan en kısa sürede geçici silme özelliğini açın.

## <a name="next-steps"></a>Sonraki adımlar

- Bu değişiklik hakkında sorularınız hakkında bizimle iletişim kurun [akvsoftdelete@microsoft.com](mailto:akvsoftdelete@microsoft.com) .
- [Geçici silme genel bakışını](soft-delete-overview.md)okuyun.
