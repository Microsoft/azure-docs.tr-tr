---
title: Özel SKU 'Lar ve planlar | Azure Marketi
description: Teklif kullanılabilirliğini yönetmek için özel SKU 'Ları kullanma.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: pabutler
ms.openlocfilehash: eb6eac5eafaeea239bfaf9cf2aface3db659dd57
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818829"
---
<a name="private-skus-and-plans"></a>Özel SKU 'Lar ve planlar
============

Özel SKU 'Lar, SKU 'ların belirli müşterilerle kullanılabilirliğini kısıtlamanıza olanak sağlar. Bir SKU özel olarak işaretlendiğinde, [Azure Marketi](https://azuremarketplace.microsoft.com) ve [Azure Portal](https://portal.azure.com)dahil olmak üzere herhangi bir genel katalogda kullanılamaz. Azure portal, yalnızca SKU 'ya erişimi olan müşteriler tarafından görüntülenebilir. Ayrıca, bunlara özel tekliflere erişiminin olması istenir.

>[!NOTE]
>Genel SKU 'larınız ile herhangi bir çakışmayı önlemek için özel SKU 'Ların yeni benzersiz SKU/plan kimlikleri olmalıdır.

Aşağıdaki senaryoları işlemek için özel SKU 'Ları kullanabilirsiniz:

1.  Yalnızca belirli müşteriler için herkese açık ve herkese açık bir şekilde kullanılabilir olmasını istediğiniz yazılımları yayımlayın.
2.  Belirli müşteriler için özelleştirilmiş bir fiyata ortak yazılımın çeşitlemelerini yayımlayın.
3.  Ortak yazılımın çeşitlemelerini özelleştirilmiş bir açıklama ve koşullarla yayımlayın (yeni teklif aracılığıyla).

Yalnızca fiyatı değiştirmek istiyorsanız, aynı teklifte başka bir SKU 'dan diskleri yeniden kullanabilirsiniz. Özel SKU 'Lar ile, diskleri SKU 'Lar arasında yeniden göndermeniz gerekmez.

<a name="mark-a-sku-private"></a>SKU 'SU özel olarak işaretle
---------------------

Bir SKU 'YU özel olarak işaretlemek için, SKU 'nun özel olup olmadığını soran seçeneği değiştirin:

![SKU 'YU özel olarak işaretleme](./media/cloud-partner-portal-publish-virtual-machine/markingskuprivate.png)

Başka bir SKU 'daki diskleri yeniden kullanabilir ve fiyatlandırmayı veya açıklamayı değiştirebilirsiniz. Diskleri yeniden kullanmak için "Bu SKU 'nun görüntüleri ortak bir SKU 'dan yeniden kullanma" isteminde yanıt olarak **Evet** ' i seçin.

SKU özel olarak işaretlenmişse ve teklifin yeniden kullanılabilen diskler içeren başka SKU 'Lar varsa, SKU 'nun diskleri başka bir SKU 'dan yeniden kullandığı belirtmeniz gerekir. Ayrıca, özel SKU için hedef kitleyi belirtmeniz gerekir.

>[!NOTE]
>Yayımlandıktan sonra ortak bir SKU özel hale getirilebilir.

<a name="select-an-image"></a>Görüntü seçin
------------------

Özel SKU için yeni diskler sağlayabilir veya başka bir SKU 'da zaten sağlanmış olan disklerin aynısını kullanabilirsiniz. Bu, yalnızca fiyatlandırmayı veya açıklamayı değiştirir. Diskleri yeniden kullanmak için, "Bu SKU görüntüsünü ortak bir SKU 'dan yeniden kullan" isteminde yanıt olarak **Evet** ' i seçin.

![Görüntü yeniden kullanımını belirtin](./media/cloud-partner-portal-publish-virtual-machine/selectimage1.png)

SKU 'nun görüntüleri yeniden yeniden sağladığını doğruladıktan sonra, görüntüler için kaynak veya *temel* SKU 'yu seçin:

![Görüntü seçin](./media/cloud-partner-portal-publish-virtual-machine/selectimage2.png)

Teklifi yayımladığınızda, seçilen SKU 'daki görüntüler özel ücretler/koşullarla özel SKU KIMLIĞI altında kullanılabilir hale getirilir. Özel SKU yalnızca hedeflenen hedef kitle tarafından görülebilir.

Görüntü güncelleştirmeleri için yalnızca temel SKU 'nun görüntüsünü güncelleştirmeniz gerekir. Arka planda, özel SKU 'nun görüntüsü de otomatik olarak güncelleştirilir. Benzer şekilde, arka plandaki SKU 'dan görüntüyü silerseniz görüntü de özel SKU 'dan da kaldırılır.

<a name="restricting-the-audience"></a>Hedef kitleyi kısıtlama
------------------------

Özel teklifler, yalnızca hedeflenen kullanıcılar tarafından bulunabilir ve dağıtılabilir.
Şu anda, abonelik kimliklerini kullanan kullanıcıları hedeflemeyi destekliyoruz.

Bu abonelikler, 10 ' a **kadar abonelik için**bir el ile giriş formu aracılığıyla ya da **20.000 aboneliğe kadar**izin veren bir CSV dosyası karşıya yüklenebilir.

Kısıtlanmış hedef kitle için el ile giriş:

![İzleyiciyi el ile kısıtlama](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience1.png)

Kısıtlanmış hedef kitle için CSV yüklemesi:

![İzleyiciyi kısıtlamak için CSV kullanma](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience2.png)

Örnek CSV dosyası içeriği:

            Type,Id,Description
            SubscriptionId,7738d703-3135-4e8d-8b81-1e70379abd9d,Private Customer

El ile girişten CSV karşıya yükleme görünümüne veya CSV 'den el ile girişe geçtiğinizde, SKU 'ya erişimi olan eski abonelik kimlikleri listesi korunmaz. Bir uyarı görüntülenir ve listenin yalnızca teklif kaydedilirken üzerine yazılır.

<a name="managing-private-audiences"></a>Özel izleyicileri yönetme
-------------------------

**Tüm teklifi yeniden yayımlamadan hedef kitleyi güncelleştirmek için, istediğiniz hedef kitle değişikliklerini (Kullanıcı arabirimi veya API kullanarak) yapmanız ve "özel Izleyicileri Eşitle" eylemini başlatmanız gerekir.**

Hedef kitleniz 10 veya daha az abonelik ise, CPP Kullanıcı arabirimini kullanarak tamamen yönetebilirsiniz.

Hedef kitleniz 10 ' dan fazla abonelikdeyse, CPP Kullanıcı arabirimine veya API kullanarak karşıya yükleyebileceğiniz bir CSV dosyası kullanarak bunu yönetebilirsiniz.

API kullanıyorsanız ve bir CSV dosyası sürdürmek istemiyorsanız, aşağıdaki yönergeler temelinde izleyiciyi doğrudan API kullanarak yönetebilirsiniz.

> [!NOTE]
> Özel teklifinizin bir kitlesini eklemek için Azure abonelik KIMLIĞI (planlar ve SKU 'Lar) veya kiracı KIMLIĞI (yalnızca planlar) kullanın.

###  <a name="managing-subscriptions-with-the-api"></a>API ile abonelikleri yönetme

API 'yi kullanarak bir CSV 'yi karşıya yükleyebilir veya doğrudan kitlelerinizi yönetebilir (CSV kullanmadan). Genellikle teklifinizin alınması, `restrictedAudience` nesnesini güncelleştirmeniz ve ardından bu değişiklikleri, hedef kitle üyelerini eklemek veya kaldırmak için teklifinize geri göndermeniz yeterlidir.

Hedef kitle listenizi programlı olarak güncelleştirme:

1. [Teklif verilerinizi alın](cloud-partner-portal-api-retrieve-specific-offer.md) :

    ```
    GET https://cloudpartner.azure.com/api/publishers//offers/?api-version=2017-10-31&includeAllPricing=true
    ```

2. Bu JPath sorgusunu kullanarak teklifin her SKU 'sunda kısıtlı hedef kitle nesneleri bulun:

    ```
    $.definition.plans[*].restrictedAudience
    ```
3. Teklifiniz için kısıtlı hedef kitle nesnelerini güncelleştirin.

    **İlk olarak CSV dosyasından özel teklifiniz için abonelik listesini karşıya yüklediyseniz:**

    *Kısıttedaudience* nesneniz şuna benzeyecektir.
    ```
    "restrictedAudience": {
                  "uploadedCsvUri": "{SasUrl}"
    }
    ```

    Her kısıtlanmış hedef kitle nesnesi için:

    a. `restrictedAudience.uploadedCsvUri`içeriğini indirin. İçerik yalnızca üst bilgileri olan bir CSV dosyasıdır. Örneğin:

        type,id,description
        subscriptionId,541a269f-3df2-486e-8fe3-c8f9dcf28205,sub1
        subscriptionId,c0da499c-25ec-4e4b-a42a-6e75635253b9,sub2

    b. İndirilen CSV dosyasına gereken abonelikleri gerekli şekilde ekleyin veya silin.

    c. Güncelleştirilmiş CSV dosyasını [Azure Blob depolama](../../storage/blobs/storage-blobs-overview.md) veya [OneDrive](https://onedrive.live.com)gibi bir konuma yükleyin ve dosyanıza salt okunurdur bir bağlantı oluşturun. Bu, yeni bir *Sasurl*'niz olacaktır.

    d. `restrictedAudience.uploadedCsvUri` anahtarını yeni *Sasurl*'ınızla güncelleştirin.

    **Bulut İş Ortağı Portalı özel teklifiniz için özgün Aboneliklerin listesini el ile girdiyseniz:**

    *Kısıttedaudience* nesneniz şuna benzer şekilde görünür:

    ```
    "restrictedAudience": {
        "manualEntries": [{
            "type": "subscriptionId",
            "id": "541a269f-3df2-486e-8fe3-c8f9dcf28205",
            "description": "sub1"
            }, {
            "type": "subscriptionId",
            "id": "c0da499c-25ec-4e4b-a42a-6e75635253b9",
            "description": "sub2"
            }
        ]}
    ```

    a. Her kısıtlanmış hedef kitle nesnesi için, `restrictedAudience.manualEntries` listesinde girdileri gerektiği gibi ekleyin veya silin.

4. Özel teklifinizin her SKU 'SU için tüm *Kısıttedaudıence* nesnelerinin güncelleştirilmesi bittiğinde [teklifi güncelleştirin](cloud-partner-portal-api-creating-offer.md):

    ```
    PUT https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31
    ```
    Bu şekilde, güncelleştirilmiş hedef kitle listeniz artık etkindir.

<a name="previewing-private-offers"></a>Özel teklifleri Önizleme
-------------------------

Önizleme/hazırlama adımı sırasında yalnızca teklif düzeyi önizleme abonelikleri SKU 'ya erişebilecek. Bu test aşamasında, teklifin hedef müşterileriniz için görüneceği şekilde önizlemesini yapabilirsiniz.

Hazırlanan tekliflere erişmek için teklif düzeyi önizleme abonelikleri:

![Abonelik kimliklerini Önizle](./media/cloud-partner-portal-publish-virtual-machine/previewoffer1.png)

Teklif canlı olduktan sonra, yalnızca kısıtlı izleyici abonelikleri (el ile girdi veya CSV aracılığıyla girilen), özel SKU 'YU görüntüleyebilir ve dağıtabilir. Doğrulama amacıyla özel SKU 'nun **kısıtlı kitlelerine her zaman kendi aboneliklerinizi dahil** etmenizi öneririz.

>[!NOTE]
>Hata ayıklama amacıyla, Microsoft destek ve mühendislik ekiplerinin de bu özel tekliflere erişimi olur.
