---
title: Grup lisansı atama sorunlarını giderme-Azure Active Directory | Microsoft Docs
description: Azure Active Directory grup tabanlı lisanslama kullanırken lisans atama sorunlarını belirleme ve çözme
services: active-directory
keywords: Azure AD lisanslama
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ddfc4bf7ed3bdf214a44a5dfe03259d32b2f3f94
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74025688"
---
# <a name="identify-and-resolve-license-assignment-problems-for-a-group-in-azure-active-directory"></a>Azure Active Directory bir grup için lisans atama sorunlarını tanımla ve çözümle

Azure Active Directory (Azure AD) içindeki grup tabanlı lisanslama, Kullanıcı kavramını bir lisanslama hata durumunda sunmaktadır. Bu makalede, kullanıcıların bu durumda nasıl bitebileceği nedenleri açıkladık.

Lisansları, grup tabanlı lisanslama kullanmadan, bireysel kullanıcılara doğrudan atadığınızda, atama işlemi başarısız olabilir. Örneğin, bir kullanıcı sisteminde PowerShell cmdlet 'ini `Set-MsolUserLicense` yürüttüğünüzde, cmdlet 'i iş mantığı ile ilgili birçok nedenden dolayı başarısız olabilir. Örneğin, yeterli sayıda lisans veya aynı anda atanmayan iki hizmet planı arasında bir çakışma olabilir. Sorun hemen size geri bildirilir.

Grup tabanlı lisanslama kullanırken aynı hatalar oluşabilir, ancak Azure AD hizmeti lisansları atarken arka planda gerçekleşirler. Bu nedenle, hatalar size anında iletilenmelidir. Bunun yerine, kullanıcı nesnesine kaydedilir ve sonra yönetim portalı aracılığıyla raporlanır. Kullanıcıya lisans vermek için özgün amaç hiçbir zaman kaybedilmez, ancak gelecekteki araştırma ve çözümleme için bir hata durumunda kaydedilir.

## <a name="find-license-assignment-errors"></a>Lisans atama hatalarını bulma

### <a name="to-find-users-in-an-error-state-in-a-group"></a>Bir gruptaki bir hata durumundaki kullanıcıları bulmak için

1. Grubu genel bakış sayfasında açın ve **lisanslar**' ı seçin. Bir hata durumunda herhangi bir kullanıcı varsa bir bildirim görüntülenir.

   ![Grup ve hata bildirimleri iletisi](./media/licensing-groups-resolve-problems/group-error-notification.png)

1. Etkilenen tüm kullanıcıların listesini açmak için bildirimi seçin. Daha fazla ayrıntı görmek için her bir kullanıcıyı tek tek seçebilirsiniz.

   ![Grup lisanslama hata durumundaki kullanıcıların listesi](./media/licensing-groups-resolve-problems/list-of-users-with-errors.png)

1. En az bir hata içeren tüm grupları bulmak için **Azure Active Directory** dikey penceresinde **lisanslar**' ı seçin ve ardından **genel bakış**' ı seçin. Gruplar ilgilenmeniz gerektiğinde bir bilgi kutusu görüntülenir.

   ![Hata durumundaki gruplar hakkında genel bakış ve bilgiler](./media/licensing-groups-resolve-problems/group-errors-widget.png)

1. Hata içeren tüm grupların listesini görmek için kutuyu seçin. Daha fazla ayrıntı için her bir grubu seçebilirsiniz.

   ![Hata içeren grupların genel bakış ve listesi](./media/licensing-groups-resolve-problems/list-of-groups-with-errors.png)

Aşağıdaki bölümlerde olası her bir sorunun açıklaması ve bunu çözme yöntemi verilmektedir.

## <a name="not-enough-licenses"></a>Yeterli lisans yok

**Sorun:** Grupta belirtilen ürünlerden biri için yeterli kullanılabilir lisans yok. Ürün için daha fazla lisans satın almanız veya diğer kullanıcılardan veya gruplardan kullanılmayan lisanslar boşaltmanız gerekir.

Kaç lisans kullanılabildiğini görmek için, **tüm ürünleri** > **Azure Active Directory** > **Lisansları** ' na gidin.

Hangi kullanıcıların ve grupların lisans tükettiğini görmek için bir ürün seçin. **Lisanslı kullanıcılar**altında, lisanslarına doğrudan veya bir veya daha fazla grup aracılığıyla atanmış olan tüm kullanıcıların listesini görürsünüz. **Lisanslı gruplar**altında, bu ürünlerin atandığı tüm grupları görürsünüz.

**PowerShell:** PowerShell cmdlet 'leri bu hatayı _Countihlale_bildirir.

## <a name="conflicting-service-plans"></a>Çakışan hizmet planları

**Sorun:** Grupta belirtilen ürünlerden biri, farklı bir ürün aracılığıyla kullanıcıya zaten atanmış olan başka bir hizmet planıyla çakışan bir hizmet planı içeriyor. Bazı hizmet planları, başka bir ilgili hizmet planıyla aynı kullanıcıya atanamazlar şekilde yapılandırılır.

Aşağıdaki örneği inceleyin. Bir kullanıcının, tüm planlar etkinken doğrudan atanmış Office 365 Enterprise *E1* lisansına sahip olması gerekir. Kullanıcı, kendisine atanmış Office 365 Enterprise *E3* ürünü olan bir gruba eklenmiştir. E3 ürünü, E1 'a eklenen planlarla çakışmayan hizmet planları içerir, bu nedenle Grup lisans ataması "çakışan hizmet planları" hatası ile başarısız olur. Bu örnekte, çakışan hizmet planları şunlardır:

- SharePoint Online (plan 2), SharePoint Online (plan 1) ile çakışıyor.
- Exchange Online (plan 2), Exchange Online (plan 1) ile çakışıyor.

Bu çakışmayı gidermek için planları iki adet devre dışı bırakmanız gerekir. Kullanıcıya doğrudan atanan E1 lisansını devre dışı bırakabilirsiniz. Ya da, tüm grup lisansı atamasını değiştirmeniz ve E3 lisansındaki planları devre dışı bırakmanız gerekir. Alternatif olarak, E3 lisansı bağlamında yedekli ise E1 lisansını Kullanıcı tarafından kaldırmaya karar verebilirsiniz.

Çakışan Ürün lisanslarını çözme kararı, her zaman yöneticiye aittir. Azure AD, lisans çakışmalarını otomatik olarak çözer.

**PowerShell:** PowerShell cmdlet 'leri bu hatayı _değişken Uıallyexclusiveihlale_bildirir.

## <a name="other-products-depend-on-this-license"></a>Diğer ürünler bu lisansa bağımlıdır

**Sorun:** Grupta belirtilen ürünlerden biri başka bir üründe çalışmak üzere başka bir hizmet planı için etkinleştirilmesi gereken bir hizmet planı içeriyor. Bu hata, Azure AD temel alınan hizmet planını kaldırmaya çalıştığında oluşur. Örneğin, kullanıcıyı gruptan kaldırdığınızda bu durum ortaya çıkabilir.

Bu sorunu çözmek için, gerekli planın başka bir yöntem aracılığıyla kullanıcılara atanmış olduğundan veya bağımlı hizmetlerin bu kullanıcılar için devre dışı bırakıldığından emin olmanız gerekir. Bunu yaptıktan sonra, Grup lisansını bu kullanıcılardan düzgün bir şekilde kaldırabilirsiniz.

**PowerShell:** PowerShell cmdlet 'leri bu hatayı _Dependencyihlale_bildirir.

## <a name="usage-location-isnt-allowed"></a>Kullanım konumuna izin verilmiyor

**Sorun:** Bazı Microsoft Hizmetleri, yerel yasalar ve yönetmelikler nedeniyle tüm konumlarda kullanılamaz. Bir kullanıcıya lisans atayabilmeniz için önce Kullanıcı için **Kullanım konumu** özelliğini belirtmeniz gerekir. Konumu, Azure portal **kullanıcı** > **profili** > **ayarları** bölümünde belirtebilirsiniz.

Azure AD, kullanım konumu desteklenmeyen bir kullanıcıya bir grup lisansı atamayı denediğinde, başarısız olur ve kullanıcıya bir hata kaydeder.

Bu sorunu çözmek için, Kullanıcı tarafından desteklenmeyen konumlardan lisanslı gruptan kullanıcıları kaldırın. Alternatif olarak, geçerli kullanım konumu değerleri gerçek Kullanıcı konumunu temsil ediyorsa, lisansları bir sonraki sefer doğru şekilde atanacak şekilde (yeni konum destekleniyorsa) bu şekilde değişiklik yapabilirsiniz.

**PowerShell:** PowerShell cmdlet 'leri bu hatayı _ProhibitedInUsageLocationViolation_olarak bildirir.

> [!NOTE]
> Azure AD grup lisansları atarken, belirtilen kullanım konumu olmayan tüm kullanıcılar dizinin konumunu alır. Yöneticiler, yerel yasalar ve yönetmeliklerle uyumlu olması için grup tabanlı lisanslama kullanmadan önce kullanıcılar üzerinde doğru kullanım konumu değerlerini ayarlamanızı öneririz.

## <a name="duplicate-proxy-addresses"></a>Yinelenen proxy adresleri

Exchange Online kullanıyorsanız, kiracınızdaki bazı kullanıcılar aynı ara sunucu adresi değeriyle yanlış yapılandırılmış olabilir. Grup tabanlı lisanslama böyle bir kullanıcıya lisans atamayı denediğinde, başarısız olur ve "proxy adresi zaten kullanılıyor" olarak gösterilir.

> [!TIP]
> Yinelenen bir proxy adresi olup olmadığını görmek için, Exchange Online 'da aşağıdaki PowerShell cmdlet 'ini yürütün:
> ```
> Get-Recipient -ResultSize unlimited | where {$_.EmailAddresses -match "user@contoso.onmicrosoft.com"} | fL Name, RecipientType,emailaddresses
> ```
> Bu sorun hakkında daha fazla bilgi için bkz. [Exchange Online 'da "proxy adresi zaten kullanılıyor" hata iletisi](https://support.microsoft.com/help/3042584/-proxy-address-address-is-already-being-used-error-message-in-exchange-online). Bu makalede, [uzak PowerShell kullanarak Exchange Online 'a bağlanma](https://technet.microsoft.com/library/jj984289.aspx)hakkında bilgiler de yer almaktadır.

Etkilenen kullanıcılar için tüm proxy adres sorunlarını çözdükten sonra, lisansların artık uygulanabilir olduğundan emin olmak için Grup üzerinde lisans işlemeyi zorladığınızdan emin olun.

## <a name="azure-ad-mail-and-proxyaddresses-attribute-change"></a>Azure AD mail ve ProxyAddresses öznitelik değişikliği

**Sorun:** Bir kullanıcı veya grup üzerinde lisans atamasını güncelleştirirken, bazı kullanıcıların Azure AD postası ve ProxyAddresses özniteliğinin değiştirildiğini görebilirsiniz.

Bir kullanıcının lisans atamasını güncelleştirme, proxy adresi hesaplamasının tetiklenmesi ve bu da kullanıcı özniteliklerinin değişmesine neden olabilir. Değişikliğin tam nedenini anlamak ve sorunu çözmek için, [proxyAddresses özniteliğinin Azure AD 'de nasıl doldurulduğuna](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad)ilişkin bu makaleye bakın.

## <a name="licenseassignmentattributeconcurrencyexception-in-audit-logs"></a>Denetim günlüklerinde LicenseAssignmentAttributeConcurrencyException

**Sorun:** Kullanıcı, denetim günlüklerinde lisans ataması için LicenseAssignmentAttributeConcurrencyException sahiptir.
Grup tabanlı lisanslama, bir kullanıcıya aynı lisansın eşzamanlı lisans atamasını işlemeye çalıştığında, bu özel durum kullanıcıya kaydedilir. Bu genellikle bir kullanıcı aynı atanmış lisansa sahip birden fazla grubun üyesi olduğunda gerçekleşir. AZure AD Kullanıcı lisansını işlemeyi yeniden deneyecek ve sorunu çözmeyecektir. Müşterinin bu sorunu çözmesi için gerekli bir işlem yoktur.

## <a name="more-than-one-product-license-assigned-to-a-group"></a>Bir gruba birden fazla ürün lisansı atandı

Bir gruba birden fazla ürün lisansı atayabilirsiniz. Örneğin, kullanıcılar için tüm dahil edilen hizmetleri kolayca etkinleştirmek üzere bir gruba Office 365 Kurumsal E3 ve Enterprise Mobility + Security atayabilirsiniz.

Azure AD, grupta belirtilen tüm lisansları her kullanıcıya atamaya çalışır. İş mantığı sorunları nedeniyle Azure AD ürünlerden birini atamayacağından, gruptaki diğer lisansları atamaz. Örnek olarak, tümü için yeterli lisans olmaması veya Kullanıcı üzerinde etkinleştirilen diğer hizmetlerle ilgili çakışmalar varsa.

Atanmayan kullanıcıları görebilir ve bu sorundan etkilenen ürünleri kontrol edebilirsiniz.

## <a name="when-a-licensed-group-is-deleted"></a>Lisanslı bir grup silindiğinde

Grubu silebilmeniz için önce bir gruba atanmış tüm lisansları kaldırmanız gerekir. Ancak, gruptaki tüm kullanıcılardan lisansların kaldırılması zaman alabilir. Bir gruptan lisans atamalarını kaldırırken, kullanıcıya bağımlı bir lisans varsa veya lisans kaldırma işlemini yasaklayan bir ara sunucu adresi çakışması sorunu varsa, bu hata olabilir. Bir kullanıcının, Grup silme nedeniyle kaldırılmakta olan bir lisansa bağlı bir lisansı varsa, kullanıcıya lisans ataması devralınan ' dan doğrudan öğesine dönüştürülür.

Örneğin, Skype Kurumsal hizmet planı 'nın etkin olduğu Office 365 E3/E5 atanmış bir grubu düşünün. Ayrıca, grubun birkaç üyesinin doğrudan atanmış sesli konferans lisansları olduğunu da düşünün. Grup silindiğinde, grup tabanlı lisanslama, Office 365 E3/E5 'i tüm kullanıcılardan kaldırmaya çalışır. Ses konferansı Skype Kurumsal 'e bağlı olduğundan, ses konferansı atanmış tüm kullanıcılar için grup tabanlı lisanslama, Office 365 E3/E5 lisanslarını doğrudan lisans atamasını dönüştürür.

## <a name="manage-licenses-for-products-with-prerequisites"></a>Önkoşulları olan ürünler için lisansları yönetme

Sahip olabileceğiniz bazı Microsoft çevrimiçi ürünleri *Eklentiler*. Eklentiler, bir lisans atanmadan önce bir kullanıcı veya grup için bir önkoşul hizmeti planının etkinleştirilmesini gerektirir. Grup tabanlı lisanslamayla, sistem hem önkoşul hem de eklenti hizmeti planlarının aynı grupta bulunmasını gerektirir. Bu, gruba eklenen tüm kullanıcıların tam olarak çalışan ürünü alabildiğini sağlamak için yapılır. Aşağıdaki örneği ele alalım:

Microsoft çalışma alanı analizi bir eklenti ürünüdür. Aynı ada sahip tek bir hizmet planı içerir. Aşağıdaki ön koşullardan biri de atandığında, bu hizmet planını yalnızca bir kullanıcıya veya gruba atayabiliriz:

- Exchange Online (plan 1)
- Exchange Online (plan 2)

Bu ürünü bir gruba atamaya çalışırsam, Portal bir bildirim iletisi döndürür. Öğe ayrıntılarını seçmemiz halinde aşağıdaki hata iletisi görüntülenir:

  "Lisans işlemi başarısız oldu. Bağımlı bir hizmeti eklemeden veya kaldırmadan önce grubun gerekli hizmetlere sahip olduğundan emin olun. **Microsoft çalışma alanı Analizi hizmeti, Exchange Online (plan 2) özelliğinin de etkinleştirilmesini gerektirir.** "

Bu eklenti lisansını bir gruba atamak için, grubun önkoşul hizmet planını da içerdiğinden emin olunması gerekir. Örneğin, zaten tam Office 365 E3 ürününü içeren mevcut bir grubu güncelleştirebilir ve ardından eklenti ürününü bu ürüne ekleyebilirsiniz.

Ayrıca, eklentinin çalışmasını sağlamak için gereken en az sayıda ürünü içeren tek başına bir grup oluşturmak da mümkündür. Bu, eklenti ürünü için yalnızca seçili kullanıcıları lisanslamak üzere kullanılabilir. Önceki örneğe bağlı olarak, aşağıdaki ürünleri aynı gruba atarsınız:

- Office 365 Enterprise E3 yalnızca Exchange Online (plan 2) hizmet planı etkin
- Microsoft çalışma alanı Analizi

Artık bu gruba eklenen tüm kullanıcılar, E3 ürününün bir lisansını ve çalışma alanı analizi ürününün bir lisansını kullanır. Aynı zamanda, bu kullanıcılar, bu kullanıcılara tam E3 ürünü sağlayan başka bir grubun üyeleri olabilir ve bu ürün için yalnızca bir lisans tüketir.

> [!TIP]
> Her bir önkoşul hizmeti planı için birden çok grup oluşturabilirsiniz. Örneğin, kullanıcılarınız için hem Office 365 Enterprise E1 hem de Office 365 Enterprise E3 kullanıyorsanız, Microsoft çalışma alanı analizi 'ni lisanslamak için iki grup oluşturabilirsiniz: bir önkoşul olarak E1 ve E3 kullanan diğeri. Bu, ek lisanslar kullanmadan E1 ve E3 kullanıcılarına eklentiyi dağıtmanıza imkan tanır.

## <a name="force-group-license-processing-to-resolve-errors"></a>Hataları çözmek için Grup lisans işlemesini zorla

Hataları çözmek için hangi adımlara götürülürsünüz bağlı olarak, Kullanıcı durumunu güncelleştirmek için bir grubun işlenmesini el ile tetiklemeniz gerekebilir.

Örneğin, kullanıcılardan doğrudan lisans atamalarını kaldırarak bazı lisanslar oluşturduysanız, daha önce tüm Kullanıcı üyelerini tamamen lisanslayamayan grupların işlenmesini tetiklemeniz gerekir. Bir grubu yeniden işlemek için Grup bölmesine gidin, **lisanslar**' ı açın ve sonra araç çubuğunda **yeniden işle** düğmesini seçin.

## <a name="force-user-license-processing-to-resolve-errors"></a>Hataları çözümlemek için Kullanıcı Lisans işlemesini zorla

Hataları çözmek için hangi adımlara götürülürsünüz bağlı olarak, Kullanıcı durumunu güncelleştirmek için bir kullanıcının işlenmesini el ile tetiklemeniz gerekebilir.

Örneğin, etkilenen bir kullanıcı için yinelenen ara sunucu adresi sorununu çözdükten sonra, Kullanıcı işlemesini tetiklemeniz gerekir. Bir kullanıcıyı yeniden işlemek için, Kullanıcı bölmesine gidin, **lisanslar**' ı açın ve sonra araç çubuğunda **yeniden işle** düğmesini seçin.

## <a name="next-steps"></a>Sonraki adımlar

Gruplar aracılığıyla lisans yönetimine yönelik diğer senaryolar hakkında daha fazla bilgi için aşağıdakilere bakın:

* [Azure Active Directory 'de grup tabanlı lisanslama nedir?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Azure Active Directory'de gruba lisans atama](licensing-groups-assign.md)
* [Azure Active Directory'de tek tek lisanslı kullanıcıları grup tabanlı lisanslamaya geçirme](licensing-groups-migrate-users.md)
* [Kullanıcılar Azure Active Directory'de Grup tabanlı lisanslama kullanarak ürün lisansları arasında geçirme](licensing-groups-change-licenses.md)
* [Azure Active Directory grup tabanlı lisanslamayla ilgili ek senaryolar](licensing-group-advanced.md)
* [Azure Active Directory'de Grup tabanlı lisanslama için PowerShell örnekleri](licensing-ps-examples.md)
