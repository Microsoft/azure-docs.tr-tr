---
title: Microsoft uç noktası Configuration Manager Yapılandırma-Azure
description: Windows sanal masaüstü 'nde Windows 10 Enterprise çoklu oturumuna yazılım güncelleştirmeleri dağıtmak için Microsoft uç nokta Configuration Manager yapılandırma.
author: Heidilohr
ms.topic: how-to
ms.date: 06/12/2020
ms.author: helohr
ms.reviewer: v-cawood; clemr
manager: femila
ms.openlocfilehash: fe2da5c60c06e5617d130940af4909c52272d551
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448177"
---
# <a name="configure-microsoft-endpoint-configuration-manager"></a>Microsoft uç noktası Configuration Manager yapılandırma

Bu makalede, Windows 10 Enterprise çoklu oturum çalıştıran bir Windows sanal masaüstü konağına güncelleştirmeleri otomatik olarak uygulamak için Microsoft uç nokta Configuration Manager nasıl yapılandırılacağı açıklanır.

## <a name="prerequisites"></a>Önkoşullar

Bu ayarı yapılandırmak için aşağıdaki işlemleri yapmanız gerekir:

   - Microsoft uç noktası Configuration Manager aracısını sanal makinelerinize yüklediğinizden emin olun.
   - Microsoft uç noktası Configuration Manager sürümünüzün en azından şube düzeyi 1906 olduğundan emin olun. En iyi sonuçlar için, şube düzeyi 1910 veya üstünü kullanın.

## <a name="configure-the-software-update-point"></a>Yazılım güncelleştirme noktasını yapılandırma

Windows 10 Enterprise çoklu oturum güncelleştirmelerini almak için Windows Server, sürüm 1903 ve üstünü Microsoft uç nokta Configuration Manager içinde bir ürün olarak etkinleştirmeniz gerekir. Bu ürün ayarı, sistemlerinize güncelleştirmeleri dağıtmak için Windows Server Update hizmetini kullanıyorsanız de geçerlidir.

Güncelleştirmeleri almak için:

1. Microsoft uç noktası Configuration Manager açın ve **siteler**' i seçin.
2. **Site bileşenlerini Yapılandır**' ı seçin.
3. Açılır menüden **yazılım güncelleştirme noktası ' nı** seçin.
4. **Ürünler** sekmesini seçin.
5. **Windows Server, sürüm 1903 ve üstünü** belirten onay kutusunu seçin.
6. **Yazılım kitaplığı**'na  >  **genel bakış**  >  **yazılım** güncelleştirmeleri  >  **tüm yazılım güncelleştirmelerini** güncelleştirir ve **yazılım güncelleştirmelerini eşitlemeyi** seçin.
7.   >    >  Değişikliklerinizin kaydedildiğinden emin olmak için Program Files Microsoft Configuration Manager **logs** içindeki wsyncmgr. log dosyasını kontrol edin. Güncelleştirmelerin eşitlenmesi birkaç dakika sürebilir.

## <a name="create-a-query-based-collection"></a>Sorgu tabanlı koleksiyon oluşturma

Windows 10 Enterprise çoklu oturum sanal makinelerinin bir koleksiyonunu oluşturmak için, belirli işletim sistemi SKU 'sunu tanımlamak üzere sorgu tabanlı bir koleksiyon kullanılabilir.

Bir koleksiyon oluşturmak için:

1. **Varlıkları ve uyumluluğu** seçin.
2. **Genel bakış**  >  **Cihaz Koleksiyonları** ' na gidin ve **Cihaz Koleksiyonları** ' na sağ tıklayın ve açılan menüden **cihaz koleksiyonu oluştur** ' u seçin.
3. Açılan menünün **genel** sekmesinde, **ad** alanına koleksiyonunuzu açıklayan bir ad girin. **Açıklama** alanında, koleksiyonun ne olduğunu açıklayan ek bilgiler verebilirsiniz. **Koleksiyonu sınırlandırma**' de, koleksiyon sorgusuna hangi makineleri dahil olduğunuzu tanımlayın.
4. **Üyelik kuralları** sekmesinde, **Kural Ekle**' yi seçip **sorgu kuralı**' nı seçerek sorgunuz için bir kural ekleyin.
5. **Sorgu kuralı özellikleri**' nde kuralınız için bir ad girin ve ardından **sorgu bildirisini Düzenle**' yi seçerek kuralın parametrelerini tanımlayın.
6. **Sorgu Ifadesini göster**' i seçin.
7. İfadesinde aşağıdaki dizeyi girin:

    ```syntax
    select
    SMS_R_SYSTEM.ResourceID,SMS_R_SYSTEM.ResourceType,SMS_R_SYSTEM.Name,SMS_R_SYSTEM.SMSUniqueIdentifier,SMS_R_SYSTEM.ResourceDomainORWorkgroup,SMS_R_SYSTEM.Client
    from SMS_R_System inner join SMS_G_System_OPERATING_SYSTEM on
    SMS_G_System_OPERATING_SYSTEM.ResourceId = SMS_R_System.ResourceId where
    SMS_G_System_OPERATING_SYSTEM.OperatingSystemSKU = 175
    ```

8. Koleksiyonu oluşturmak için **Tamam ' ı** seçin.
9. Koleksiyonu başarıyla oluşturup **oluşturduğunuzu denetlemek için varlıklar ve uyum**  >  **genel bakış**  >  **Cihaz Koleksiyonları**' na gidin.
