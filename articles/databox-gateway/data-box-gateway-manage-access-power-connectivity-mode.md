---
title: Cihaz erişimi, güç ve bağlantı modunu Azure Data Box Gateway
description: Azure 'a veri aktarmaya yardımcı olan Azure Data Box Gateway cihaz için erişim, güç ve bağlantı modunun nasıl yönetileceğini açıklar
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: how-to
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: c4e2894d193309c169adbea96491e0754d479a8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98786817"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-gateway"></a>Azure Data Box Gateway için erişimi, güç ve bağlantı modunu yönetme

Bu makalede, Azure Data Box Gateway erişim, güç ve bağlantı modunun nasıl yönetileceği açıklanır. Bu işlemler yerel Web Kullanıcı arabirimi veya Azure portal aracılığıyla gerçekleştirilir. 

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
>
> * Cihaz erişimini yönetme
> * Bağlantı modunu Yönet
> * Güç yönetimi

## <a name="manage-device-access"></a>Cihaz erişimini yönetme

Data Box Gateway cihazınıza erişim, bir cihaz parolasının kullanımı ile denetlenir. Parolayı yerel Web Kullanıcı arabirimi aracılığıyla değiştirebilirsiniz. Ayrıca Azure portal cihaz parolasını sıfırlayabilirsiniz.

### <a name="change-device-password"></a>Cihaz parolasını değiştirme

Cihaz parolasını değiştirmek için yerel kullanıcı arabirimindeki adımları izleyin.

1. Yerel Web Kullanıcı arabiriminde **bakım > parola değiştirme**' ye gidin.
2. Geçerli parolayı ve ardından yeni parolayı girin. Sağlanan parola 8 ile 16 karakter arasında olmalıdır. Parola şu karakterlerden 3 ' i içermelidir: büyük harf, küçük harf, sayısal ve özel karakterler. Yeni parolayı onaylayın.

    ![Parolayı değiştir](media/data-box-gateway-manage-access-power-connectivity-mode/change-password-1.png)

3. **Parolayı Değiştir**' e tıklayın.
 
### <a name="reset-device-password"></a>Cihaz parolasını sıfırla

Sıfırlama iş akışı, kullanıcının eski parolayı geri çekemesini gerektirmez ve parola kaybolduğunda faydalıdır. Bu iş akışı Azure portal gerçekleştirilir.

1. Azure portal, **genel bakış > yönetici parolasını sıfırla**' ya gidin.

    ![Parola sıfırlama](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-1.png)

 
2. Yeni parolayı girip onaylayın. Sağlanan parola 8 ile 16 karakter arasında olmalıdır. Parola şu karakterlerden 3 ' i içermelidir: büyük harf, küçük harf, sayısal ve özel karakterler. **Sıfırla**' ya tıklayın.

    ![Parolayı Sıfırla 2](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>Kaynak erişimini yönetme

Azure Data Box Gateway, IoT Hub ve Azure depolama kaynağınızı oluşturmak için, bir kaynak grubu düzeyinde katkıda bulunan veya daha yüksek izinlere sahip olmanız gerekir. Ayrıca, kaydedilecek ilgili kaynak sağlayıcılarının da olması gerekir. Etkinleştirme anahtarı ve kimlik bilgilerini içeren tüm işlemler için Azure Active Directory Graph API izinleri de gereklidir. Bunlar aşağıdaki bölümlerde açıklanmıştır.

### <a name="manage-microsoft-graph-api-permissions"></a>Microsoft Graph API izinlerini yönetme

Cihaz için etkinleştirme anahtarını oluştururken veya kimlik bilgileri gerektiren herhangi bir işlem gerçekleştirirken, API Microsoft Graph için izinlerinizin olması gerekir. Kimlik bilgileri gerektiren işlemler şunlar olabilir:

-  İlişkili bir depolama hesabıyla bir paylaşma oluşturuluyor.
-  Cihazdaki paylaşımlara erişebilen bir Kullanıcı oluşturma.

Yapabilmeniz için `User` Active Directory kiracısında erişiminizin olması gerekir `Read all directory objects` . Konuk kullanıcının izinleri yoktur `Read all directory objects` . Bir konuğsanız, etkinleştirme anahtarı oluşturma, cihazınızda bir paylaşma oluşturma ve Kullanıcı oluşturma işlemleri başarısız olur.

API Microsoft Graph için kullanıcılara erişim sağlama hakkında daha fazla bilgi için, bkz. [Microsoft Graph izinleri başvurusu](/graph/permissions-reference).

### <a name="register-resource-providers"></a>Kaynak sağlayıcılarını kaydetme

Azure 'da bir kaynak sağlamak için (Azure Resource Manager modelinde), bu kaynağın oluşturulmasını destekleyen bir kaynak sağlayıcısına ihtiyacınız vardır. Örneğin, bir sanal makine sağlamak için abonelikte bir ' Microsoft. COMPUTE ' kaynak sağlayıcısına sahip olmanız gerekir.
 
Kaynak sağlayıcıları abonelik düzeyinde kaydedilir. Varsayılan olarak tüm yeni Azure abonelikleri sık kullanılan kaynak sağlayıcıları listesiyle önceden kaydedilir. ' Microsoft. DataBoxEdge ' için kaynak sağlayıcısı bu listeye dahil değildir.

Bu kaynakların kaynak sağlayıcıları zaten kayıtlı olduğu sürece, kullanıcıların sahip oldukları kaynak grupları içinde ' Microsoft. DataBoxEdge ' gibi kaynaklar oluşturabilmeleri için abonelik düzeyinde erişim izinleri vermeniz gerekmez.

Herhangi bir kaynak oluşturmayı denemeden önce, kaynak sağlayıcının abonelikte kayıtlı olduğundan emin olun. Kaynak sağlayıcısı kayıtlı değilse, yeni kaynağı oluşturan kullanıcının abonelik düzeyinde gerekli kaynak sağlayıcısını kaydetmek için yeterli haklara sahip olduğundan emin olmanız gerekir. Bunu yapmadıysanız, şu hatayı görürsünüz:

*Abonelikte \<Subscription name> kaynak sağlayıcıları kaydetme izni yok: Microsoft. DataBoxEdge.*


Geçerli abonelikteki kayıtlı kaynak sağlayıcılarının bir listesini almak için aşağıdaki komutu çalıştırın:

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

Data Box Gateway bir cihaz için `Microsoft.DataBoxEdge` kaydedilmelidir. Kaydolmak için `Microsoft.DataBoxEdge` Abonelik Yöneticisi aşağıdaki komutu çalıştırmalıdır:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

Kaynak sağlayıcısını kaydetme hakkında daha fazla bilgi için bkz. [kaynak sağlayıcısı kaydı hatalarını giderme](../azure-resource-manager/templates/error-register-resource-provider.md).

## <a name="manage-connectivity-mode"></a>Bağlantı modunu Yönet

Varsayılan normal moddan ayrı olarak, cihazınız kısmen bağlantısı kesik veya bağlantısı kesik modda da çalıştırılabilir:

- **Kısmen bağlantısı kesik** – bu modda cihaz, paylaşımlara veri indiremedi. Ancak, Azure portal aracılığıyla yönetilebilir.

    Bu mod genellikle tarifeli bir uydu ağı sırasında ağ bant genişliği tüketimini en aza indirmek için kullanılır. Cihaz izleme işlemleri için en az ağ tüketimi hala meydana gelebilir.

- **Bağlı** değil – bu modda cihazın bulutla bağlantısı tamamen kesilir ve hem bulut karşıya yükleme hem de indirme işlemleri devre dışıdır. Cihaz yalnızca yerel Web Kullanıcı arabirimi aracılığıyla yönetilebilir.

    Bu mod genellikle cihazınızı çevrimdışına almak istediğinizde kullanılır.

Cihaz modunu değiştirmek için şu adımları izleyin:

1. Cihazınızın yerel Web Kullanıcı arabiriminde, **yapılandırma > bulut ayarları**' na gidin.
2. **Bulutu karşıya yüklemeyi ve indirmeyi** devre dışı bırakın.
3. Cihazı kısmen bağlantısı kesik modda çalıştırmak için **Azure Portal yönetimini** etkinleştirin.

    ![Bağlantı modu](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-1.png)
 
4. Cihazı bağlantısı kesik modda çalıştırmak için **Azure Portal yönetimini** devre dışı bırakın. Artık cihaz yalnızca yerel Web Kullanıcı arabirimi aracılığıyla yönetilebilir.

    ![Bağlantı modu 2](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-2.png)

## <a name="manage-power"></a>Güç yönetimi

Sanal cihazınızı yerel web kullanıcı arabirimini kullanarak kapatabilir ya da yeniden başlatabilirsiniz. Cihazı yeniden başlatmadan önce, konak üzerinde ve sonra da cihazdan çevrimdışı olan paylaşımları yapmanız önerilir. Bu eylem veri bozulması olasılığını en aza indirir.

1. Yerel Web Kullanıcı arabiriminde **bakım > güç ayarları**' na gidin.
2. Ne yapmak istediğinize bağlı olarak, **kapalı** veya **yeniden başlatma** ' ya tıklayın.

    ![Güç ayarları](media/data-box-gateway-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Onay istendiğinde, devam etmek için **Evet** ' e tıklayın.

> [!NOTE]
> Sanal cihazı kapatıyorsanız, cihazı hiper yönetici yönetimi aracılığıyla başlatmanız gerekecektir.