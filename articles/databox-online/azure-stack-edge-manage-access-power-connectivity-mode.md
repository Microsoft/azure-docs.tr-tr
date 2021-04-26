---
title: Azure Stack Edge Pro cihaz erişimi, güç ve bağlantı modu | Microsoft Docs
description: Azure 'a veri aktarmaya yardımcı olan Azure Stack Edge Pro cihazı için erişim, güç ve bağlantı modunun nasıl yönetileceğini açıklar
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 240d3872536e6974d7f65eed22dace6816844e9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200201"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-stack-edge-pro"></a>Azure Stack Edge Pro için erişim, güç ve bağlantı modunu yönetme

Bu makalede Azure Stack Edge Pro için erişim, güç ve bağlantı modunun nasıl yönetileceği açıklanmaktadır. Bu işlemler yerel Web Kullanıcı arabirimi veya Azure portal aracılığıyla gerçekleştirilir.

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Cihaz erişimini yönetme
> * Bağlantı modunu Yönet
> * Güç yönetimi


## <a name="manage-device-access"></a>Cihaz erişimini yönetme

Azure Stack Edge Pro cihazınıza erişim, bir cihaz parolasının kullanımı ile denetlenir. Parolayı yerel Web Kullanıcı arabirimi aracılığıyla değiştirebilirsiniz. Ayrıca Azure portal cihaz parolasını sıfırlayabilirsiniz.

### <a name="change-device-password"></a>Cihaz parolasını değiştirme

Cihaz parolasını değiştirmek için yerel kullanıcı arabirimindeki adımları izleyin.

1. Yerel Web Kullanıcı arabiriminde **bakım > parola değiştirme**' ye gidin.
2. Geçerli parolayı ve ardından yeni parolayı girin. Sağlanan parola 8 ile 16 karakter arasında olmalıdır. Parola şu karakterlerden 3 ' i içermelidir: büyük harf, küçük harf, sayısal ve özel karakterler. Yeni parolayı onaylayın.

    ![Parolayı değiştir](media/azure-stack-edge-manage-access-power-connectivity-mode/change-password-1.png)

3. **Parolayı Değiştir**' i seçin.
 
### <a name="reset-device-password"></a>Cihaz parolasını sıfırla

Sıfırlama iş akışı, kullanıcının eski parolayı geri çekemesini gerektirmez ve parola kaybolduğunda faydalıdır. Bu iş akışı Azure portal gerçekleştirilir.

1. Azure portal, **genel bakış > yönetici parolasını sıfırla**' ya gidin.

    ![Parola sıfırlama](media/azure-stack-edge-manage-access-power-connectivity-mode/reset-password-1.png)


2. Yeni parolayı girip onaylayın. Sağlanan parola 8 ile 16 karakter arasında olmalıdır. Parola şu karakterlerden 3 ' i içermelidir: büyük harf, küçük harf, sayısal ve özel karakterler. **Sıfırla**’yı seçin.

    ![Parolayı Sıfırla 2](media/azure-stack-edge-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>Kaynak erişimini yönetme

Azure Stack Edge/Data Box Gateway, IoT Hub ve Azure depolama kaynağını oluşturmak için, bir kaynak grubu düzeyinde katkıda bulunan veya daha yüksek izinlere sahip olmanız gerekir. Ayrıca, kaydedilecek ilgili kaynak sağlayıcılarının da olması gerekir. Etkinleştirme anahtarı ve kimlik bilgilerini içeren tüm işlemler için Microsoft Graph API 'SI izinleri de gereklidir. Bunlar aşağıdaki bölümlerde açıklanmıştır. 

### <a name="manage-microsoft-graph-api-permissions"></a>Microsoft Graph API izinlerini yönetme

Azure Stack Edge Pro cihazı için etkinleştirme anahtarını oluştururken veya kimlik bilgileri gerektiren işlemler gerçekleştirirken, Azure Active Directory Graph API izinlerinizin olması gerekir. Kimlik bilgileri gerektiren işlemler şunlar olabilir:

-  İlişkili bir depolama hesabıyla bir paylaşma oluşturuluyor.
-  Cihazdaki paylaşımlara erişebilen bir Kullanıcı oluşturma.

`User`Active Directory kiracısında yapabilmeniz için bir erişiminizin olması gerekir `Read all directory objects` . Bir Konuk Kullanıcı, izinleri olmadığı için bu kullanıcı olamaz `Read all directory objects` . Bir konuğunuzda, bir etkinleştirme anahtarı oluşturma, Azure Stack Edge Pro cihazınızda bir paylaşımın oluşturulması, bir kullanıcının oluşturulması, sınır hesaplama rolü yapılandırması, cihaz parolasının tümünün başarısız olması gibi işlemler başarısız olur.

API Microsoft Graph için kullanıcılara erişim sağlama hakkında daha fazla bilgi için, bkz. [Microsoft Graph izinleri başvurusu](/graph/permissions-reference).

### <a name="register-resource-providers"></a>Kaynak sağlayıcılarını kaydetme

Azure 'da bir kaynak sağlamak için (Azure Resource Manager modelinde), bu kaynağın oluşturulmasını destekleyen bir kaynak sağlayıcısına ihtiyacınız vardır. Örneğin, bir sanal makine sağlamak için abonelikte bir ' Microsoft. COMPUTE ' kaynak sağlayıcısına sahip olmanız gerekir.
 
Kaynak sağlayıcıları abonelik düzeyinde kaydedilir. Varsayılan olarak tüm yeni Azure abonelikleri sık kullanılan kaynak sağlayıcıları listesiyle önceden kaydedilir. ' Microsoft. DataBoxEdge ' için kaynak sağlayıcısı bu listeye dahil değildir.

Bu kaynakların kaynak sağlayıcıları zaten kayıtlı olduğu sürece, kullanıcıların sahip oldukları kaynak grupları dahilinde ' Microsoft. DataBoxEdge ' gibi kaynaklar oluşturabilmeleri için abonelik düzeyine erişim izni vermeniz gerekmez.

Herhangi bir kaynak oluşturmayı denemeden önce, kaynak sağlayıcının abonelikte kayıtlı olduğundan emin olun. Kaynak sağlayıcısı kayıtlı değilse, yeni kaynağı oluşturan kullanıcının abonelik düzeyinde gerekli kaynak sağlayıcısını kaydetmek için yeterli haklara sahip olduğundan emin olmanız gerekir. Bunu yapmadıysanız, şu hatayı görürsünüz:

*Abonelikte \<Subscription name> kaynak sağlayıcıları kaydetme izni yok: Microsoft. DataBoxEdge.*


Geçerli abonelikteki kayıtlı kaynak sağlayıcılarının bir listesini almak için aşağıdaki komutu çalıştırın:

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

Azure Stack Edge Pro cihazı için `Microsoft.DataBoxEdge` kayıtlı olmalıdır. Kaydolmak için `Microsoft.DataBoxEdge` , abonelik Yöneticisi aşağıdaki komutu çalıştırmalıdır:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

Kaynak sağlayıcısını kaydetme hakkında daha fazla bilgi için bkz. [kaynak sağlayıcısı kaydı hatalarını giderme](../azure-resource-manager/templates/error-register-resource-provider.md).

## <a name="manage-connectivity-mode"></a>Bağlantı modunu Yönet

Varsayılan tam bağlı moddan ayrı olarak, cihazınız kısmen bağlı veya tamamen bağlantısı kesik modda da çalıştırılabilir. Bu modların her biri aşağıda gösterildiği gibi açıklanmıştır:

- **Tam bağlantı** -bu, cihazın işlem yaptığı normal varsayılan moddur. Verilerin hem bulut yüklemesi hem de indirilmesi bu modda etkindir. Cihazı yönetmek için Azure portal veya yerel Web Kullanıcı arabirimini kullanabilirsiniz.

- **Kısmen bağlı** – bu modda cihaz, hiçbir paylaşma verisini karşıya yükleyebilir veya indiremez, ancak Azure Portal aracılığıyla yönetilebilir.

    Bu mod genellikle ölçülen uydu ağı kullanılırken kullanılır ve amaç ağ bant genişliği tüketimini en aza indirmektir. Cihaz izleme işlemleri için en az ağ tüketimi hala meydana gelebilir.

- **Bağlı** değil – bu modda, cihazın bulutla bağlantısı tamamen kesilir ve hem bulut karşıya yüklemeleri hem de İndirmeleri devre dışı bırakılır. Cihaz yalnızca yerel Web Kullanıcı arabirimi aracılığıyla yönetilebilir.

    Bu mod genellikle cihazınızı çevrimdışına almak istediğinizde kullanılır.

Cihaz modunu değiştirmek için şu adımları izleyin:

1. Cihazınızın yerel Web Kullanıcı arabiriminde, **yapılandırma > bulut ayarları**' na gidin.
2. Açılan listeden, cihazı çalıştırmak istediğiniz modu seçin. **Tam bağlı**, **kısmen bağlı** ve **tamamen bağlantısı kesilen**' ı seçebilirsiniz. Cihazı kısmen bağlantısı kesik modda çalıştırmak için **Azure Portal yönetimini** etkinleştirin.

    ![Bağlantı modu](media/azure-stack-edge-manage-access-power-connectivity-mode/connectivity-mode.png)
 
## <a name="manage-power"></a>Güç yönetimi

Fiziksel cihazınızı yerel web kullanıcı arabirimini kullanarak kapatabilir ya da yeniden başlatabilirsiniz. Cihazı yeniden başlatmadan önce veri sunucusundaki paylaşımları sonra da cihazı çevrimdışına almanız önerilir. Bu eylem veri bozulması olasılığını en aza indirir.

1. Yerel Web Kullanıcı arabiriminde **bakım > güç ayarları**' na gidin.
2. Ne yapmak istediğinize bağlı olarak, **kapanıyor** ' u veya **Yeniden Başlat** ' ı seçin.

    ![Güç ayarları](media/azure-stack-edge-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Onaylamanız istendiğinde, devam etmek için **Evet** ' i seçin.

> [!NOTE]
> Fiziksel cihazı kapatırsanız, açmak için cihazda güç düğmesine gönderim yapmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- [Paylaşımları yönetmeyi](azure-stack-edge-manage-shares.md)öğrenin.
