---
title: Cihaz erişimi, güç ve bağlantı modu Azure Data Box Edge | Microsoft Docs
description: Azure 'a veri aktarmaya yardımcı olan Azure Data Box Edge cihaz için erişim, güç ve bağlantı modunun nasıl yönetileceğini açıklar
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 7ce4b9dda853e63e427757317abc2f7c878ba3a4
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68253152"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-edge"></a>Azure Data Box Edge için erişimi, güç ve bağlantı modunu yönetme

Bu makalede, Azure Data Box Edge erişim, güç ve bağlantı modunun nasıl yönetileceği açıklanır. Bu işlemler yerel Web Kullanıcı arabirimi veya Azure portal aracılığıyla gerçekleştirilir.

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Cihaz erişimini yönetme
> * Bağlantı modunu Yönet
> * Güç yönetimi


## <a name="manage-device-access"></a>Cihaz erişimini yönetme

Data Box Edge cihazınıza erişim, bir cihaz parolasının kullanımı ile denetlenir. Parolayı yerel Web Kullanıcı arabirimi aracılığıyla değiştirebilirsiniz. Ayrıca Azure portal cihaz parolasını sıfırlayabilirsiniz.

### <a name="change-device-password"></a>Cihaz parolasını değiştirme

Cihaz parolasını değiştirmek için yerel kullanıcı arabirimindeki adımları izleyin.

1. Yerel Web Kullanıcı arabiriminde **bakım > parola değiştirme**' ye gidin.
2. Geçerli parolayı ve ardından yeni parolayı girin. Sağlanan parola 8 ile 16 karakter arasında olmalıdır. Parola şu karakterlerden 3 ' i içermelidir: büyük harf, küçük harf, sayısal ve özel karakterler. Yeni parolayı onaylayın.

    ![Parolayı Değiştir](media/data-box-edge-manage-access-power-connectivity-mode/change-password-1.png)

3. **Parolayı Değiştir**' i seçin.
 
### <a name="reset-device-password"></a>Cihaz parolasını sıfırla

Sıfırlama iş akışı, kullanıcının eski parolayı geri çekemesini gerektirmez ve parola kaybolduğunda faydalıdır. Bu iş akışı Azure portal gerçekleştirilir.

1. Azure portal, **genel bakış > Yönetici parolasını sıfırla**' ya gidin.

    ![Parola sıfırla](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-1.png)


2. Yeni parolayı girip onaylayın. Sağlanan parola 8 ile 16 karakter arasında olmalıdır. Parola şu karakterlerden 3 ' i içermelidir: büyük harf, küçük harf, sayısal ve özel karakterler. **Sıfırla**' yı seçin.

    ![Parola sıfırla](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>Kaynak erişimini yönetme

Data Box Edge/Data Box Gateway, IoT Hub ve Azure depolama kaynağını oluşturmak için, bir kaynak grubu düzeyinde katkıda bulunan veya daha yüksek izinlere sahip olmanız gerekir. Ayrıca, kaydedilecek ilgili kaynak sağlayıcılarının da olması gerekir. Etkinleştirme anahtarı ve kimlik bilgilerini içeren tüm işlemler için Azure Active Directory Graph API izinleri de gereklidir. Bunlar aşağıdaki bölümlerde açıklanmıştır.

### <a name="manage-microsoft-azure-active-directory-graph-api-permissions"></a>Microsoft Azure Active Directory Graph API izinlerini yönetme

Data Box Edge cihaz için etkinleştirme anahtarını oluştururken veya kimlik bilgileri gerektiren herhangi bir işlem gerçekleştirirken, Azure Active Directory Graph API izinlerinizin olması gerekir. Kimlik bilgileri gerektiren işlemler şunlar olabilir:

-  İlişkili bir depolama hesabıyla bir paylaşma oluşturuluyor.
-  Cihazdaki paylaşımlara erişebilen bir Kullanıcı oluşturma.

Active Directory kiracısında `User` `Read all directory objects`yapabilmeniz için bir erişiminizin olması gerekir. Bir Konuk Kullanıcı, izinleri olmadığı için `Read all directory objects`bu kullanıcı olamaz. Bir konuğunuzda, bir etkinleştirme anahtarı oluşturma, Data Box Edge cihazınızda bir paylaşımın oluşturulması, Kullanıcı oluşturma, sınır hesaplama rolü yapılandırması, cihaz parolasını sıfırlama işlemi başarısız olur.

Azure Active Directory Graph API kullanıcılara erişim sağlama hakkında daha fazla bilgi için bkz. [Yöneticiler, kullanıcılar ve Konuk kullanıcılar Için varsayılan erişim](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).

### <a name="register-resource-providers"></a>Kaynak sağlayıcılarını kaydetme

Azure 'da bir kaynak sağlamak için (Azure Resource Manager modelinde), bu kaynağın oluşturulmasını destekleyen bir kaynak sağlayıcısına ihtiyacınız vardır. Örneğin, bir sanal makine sağlamak için abonelikte bir ' Microsoft. COMPUTE ' kaynak sağlayıcısına sahip olmanız gerekir.
 
Kaynak sağlayıcıları abonelik düzeyinde kaydedilir. Varsayılan olarak tüm yeni Azure abonelikleri sık kullanılan kaynak sağlayıcıları listesiyle önceden kaydedilir. ' Microsoft. DataBoxEdge ' için kaynak sağlayıcısı bu listeye dahil değildir.

Bu kaynaklara yönelik kaynak sağlayıcıları zaten olduğu sürece, kullanıcıların sahip oldukları kaynak grupları dahilinde ' Microsoft. DataBoxEdge ' gibi kaynaklar oluşturabilmeleri için abonelik düzeyine erişim izni vermeniz gerekmez. kaydedilmeyecek.

Herhangi bir kaynak oluşturmayı denemeden önce, kaynak sağlayıcının abonelikte kayıtlı olduğundan emin olun. Kaynak sağlayıcısı kayıtlı değilse, yeni kaynağı oluşturan kullanıcının abonelik düzeyinde gerekli kaynak sağlayıcısını kaydetmek için yeterli haklara sahip olduğundan emin olmanız gerekir. Bunu yapmadıysanız, şu hatayı görürsünüz:

*> Abonelik \<abonelik adı kaynak sağlayıcıları kaydetme iznine sahip değil: Microsoft. DataBoxEdge.*


Geçerli abonelikteki kayıtlı kaynak sağlayıcılarının bir listesini almak için aşağıdaki komutu çalıştırın:

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

Data Box Edge cihaz `Microsoft.DataBoxEdge` için kaydedilmelidir. Kaydolmak `Microsoft.DataBoxEdge`için, abonelik Yöneticisi aşağıdaki komutu çalıştırmalıdır:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

Kaynak sağlayıcısını kaydetme hakkında daha fazla bilgi için bkz. [kaynak sağlayıcısı kaydı hatalarını giderme](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="manage-connectivity-mode"></a>Bağlantı modunu Yönet

Varsayılan tam bağlı moddan ayrı olarak, cihazınız kısmen bağlı veya tamamen bağlantısı kesik modda da çalıştırılabilir. Bu modların her biri aşağıda gösterildiği gibi açıklanmıştır:

- **Tam bağlantı** -bu, cihazın işlem yaptığı normal varsayılan moddur. Verilerin hem bulut yüklemesi hem de indirilmesi bu modda etkindir. Cihazı yönetmek için Azure portal veya yerel Web Kullanıcı arabirimini kullanabilirsiniz.

- **Kısmen bağlantısı kesik** – bu modda cihaz, hiçbir paylaşma verisini karşıya yükleyemiyor veya indiremez, ancak Azure Portal aracılığıyla yönetilebilir.

    Bu mod genellikle ölçülen uydu ağı kullanılırken kullanılır ve amaç ağ bant genişliği tüketimini en aza indirmektir. Cihaz izleme işlemleri için en az ağ tüketimi hala meydana gelebilir.

- **Bağlı** değil – bu modda, cihazın bulutla bağlantısı tamamen kesilir ve hem bulut karşıya yüklemeleri hem de İndirmeleri devre dışı bırakılır. Cihaz yalnızca yerel Web Kullanıcı arabirimi aracılığıyla yönetilebilir.

    Bu mod genellikle cihazınızı çevrimdışına almak istediğinizde kullanılır.

Cihaz modunu değiştirmek için şu adımları izleyin:

1. Cihazınızın yerel Web Kullanıcı arabiriminde, **yapılandırma > bulut ayarları**' na gidin.
2. Açılan listeden, cihazı çalıştırmak istediğiniz modu seçin. **Tam bağlı**, **kısmen bağlı**ve **tamamen bağlantısı kesilen**' ı seçebilirsiniz. Cihazı kısmen bağlantısı kesik modda çalıştırmak için **Azure Portal yönetimini**etkinleştirin.

    ![Bağlantı modu](media/data-box-edge-manage-access-power-connectivity-mode/connectivity-mode.png)
 
## <a name="manage-power"></a>Güç yönetimi

Yerel Web Kullanıcı arabirimini kullanarak fiziksel cihazınızı kapatabilir veya yeniden başlatabilirsiniz. Yeniden başlatmadan önce, verileri veri sunucusunda ve ardından cihazda çevrimdışı duruma getirin. Bu eylem, herhangi bir veri bozulması olasılığını en aza indirir.

1. Yerel Web Kullanıcı arabiriminde **bakım > Güç ayarları**' na gidin.
2. Ne yapmak istediğinize bağlı olarak, **kapanıyor** ' u veya **Yeniden Başlat** ' ı seçin.

    ![Güç ayarları](media/data-box-edge-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Onaylamanız istendiğinde, devam etmek için **Evet** ' i seçin.

> [!NOTE]
> Fiziksel cihazı kapatırsanız, açmak için cihazda güç düğmesine gönderim yapmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- [Paylaşımları yönetmeyi](data-box-edge-manage-shares.md)öğrenin.
