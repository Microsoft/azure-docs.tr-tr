---
title: Taşıma-Portal
description: Azure portal kullanarak Cloud Services (genişletilmiş destek) geçirme
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: tanmaygore
ms.author: tagore
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: 79889b08baa80dc67b30ae445004e37d9f9fe295
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286957"
---
# <a name="migrate-to-cloud-services-extended-support-using-the-azure-portal"></a>Azure portal kullanarak Cloud Services (genişletilmiş destek) öğesine geçirin

Bu makalede, [Cloud Services (klasik)](../cloud-services/cloud-services-choose-me.md) ' den Cloud Services ' e [(genişletilmiş destek)](overview.md)geçiş yapmak için Azure Portal nasıl kullanılacağı gösterilmektedir.

> [!IMPORTANT]
> Geçiş aracını kullanarak Cloud Services (klasik) Cloud Services (genişletilmiş destek) ' e geçiş Şu anda genel önizlemededir. Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="before-you-begin"></a>Başlamadan önce

**Abonelik için yönetici olduğunuzdan emin olun.**

Bu geçişi gerçekleştirmek için, [Azure Portal](https://portal.azure.com)abonelik için bir ortak yönetici olarak eklenmeli.

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. **Hub** menüsünde, **abonelik**' ı seçin. Bunu görmüyorsanız, **tüm hizmetler**' i seçin.
3. Uygun abonelik girişini bulun ve ardından **rol alanım** ' a bakın. Coadministrator için, değer *Hesap Yöneticisi* olmalıdır.

Ortak yönetici ekleyemediğinizde, abonelik için bir hizmet yöneticisiyle veya [ortak yönetici](../role-based-access-control/classic-administrators.md) ile iletişim kurun.

**Geçiş kaynak sağlayıcısına kaydolun**

1. `Microsoft.ClassicInfrastructureMigrate` `Cloud Services` [Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types#register-resource-provider-1)kullanarak Microsoft. COMPUTE ad alanı altında geçiş kaynak sağlayıcısı ve önizleme özelliğiyle kaydolun.  
1. Kaydın tamamlanmasını beklemek için beş dakika bekleyin, sonra onay durumunu kontrol edin. 

## <a name="migrate-your-cloud-service-resources"></a>Bulut hizmeti kaynaklarınızı geçirin

1. [Cloud Services (klasik) Portal dikey penceresine](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.classicCompute%2FdomainNames)gidin. 
2. Geçirmek istediğiniz bulut hizmetini seçin.
3. **ARM 'ye geçir** dikey penceresini seçin.

    > [!NOTE]
    > Bir sanal ağ (klasik) içinde bulunan bir Cloud Services (klasik) geçiriyorsanız, sanal ağ (klasik) dikey penceresini taşımanızı isteyen bir başlık iletisi görüntülenir.
    > Hem sanal ağ (klasik) dikey penceresine, hem de içindeki sanal ağın (klasik) ve Cloud Services (klasik) dağıtımlarınızın geçişini tamamlayacaksınız.
    > :::image type="content" source="media/in-place-migration-portal-2.png" alt-text="Görüntü, Azure portal bir sanal ağın klasik olarak taşınmasını gösterir.":::
 

4. Geçişi doğrulayın. 

    Doğrulama başarılı olursa, tüm dağıtımlar desteklenir ve hazırlanmaya hazırdır.  

    :::image type="content" source="media/in-place-migration-portal-1.png" alt-text="Görüntü, Azure portal ARM dikey penceresine geçiş gösterir.":::

    Doğrulama başarısız olursa, desteklenmeyen senaryoların bir listesi görüntülenir ve geçişin devam edebilmesi için düzeltilmesi gerekir. 

    :::image type="content" source="media/in-place-migration-portal-3.png" alt-text="Görüntüde Azure portal doğrulama hatası gösterilir.":::

5. Geçişe hazırlanın.

    Hazırlama başarılı olursa, geçiş işlemi işlemeye hazırlar.
    
    :::image type="content" source="media/in-place-migration-portal-4.png" alt-text="Görüntü Azure portal doğrulama geçişini gösterir.":::

    Hazırlama başarısız olursa, hatayı gözden geçirin, sorunları çözün ve hazırlama işlemini yeniden deneyin. 

    :::image type="content" source="media/in-place-migration-portal-5.png" alt-text="Görüntü doğrulama hatası hatasını gösterir.":::

      Hazırlandıktan sonra, bir sanal ağdaki tüm Cloud Services, hem Cloud Services (klasik) hem de Cloud Services (genişletilmiş destek) Azure portal dikey pencere kullanılarak okuma işlemleri için kullanılabilir. Bulut hizmeti (genişletilmiş destek) dağıtımı artık, geçişi sonuçlandırmadan önce düzgün bir şekilde çalıştığından emin olmak için test edilebilir. 
 
    :::image type="content" source="media/in-place-migration-portal-6.png" alt-text="Görüntü, Portal dikey penceresinde API 'Leri test etmeyi gösterir.":::

6.  **(Isteğe bağlı)** Geçişi iptal edin. 
    
    Geçiş işlemini yapmayı seçerseniz, önceki adımları geri almak için **Durdur** düğmesini kullanın. Cloud Services (klasik) dağıtımı, tüm CRUD işlemleri için açılır.

    :::image type="content" source="media/in-place-migration-portal-7.png" alt-text="Görüntü doğrulama geçişini gösterir.":::

    İptal başarısız olursa **Iptali yeniden dene**' yi seçin. Yeniden deneme sorunu gidermelidir. Aksi takdirde, desteğe başvurun. 
 
    :::image type="content" source="media/in-place-migration-portal-8.png" alt-text="Görüntü doğrulama hatası hata iletisini gösterir.":::

7.  Geçiş yürütün.

    >[!IMPORTANT]
    > Geçişe işlemeden sonra geri alma seçeneği yoktur. 
    
    "Yes" yazarak geçişi onaylayın ve işleyin. Geçiş artık tamamlanmıştır. Geçirilen Cloud Services (genişletilmiş destek) dağıtımı tüm işlemler için açıldı. 

## <a name="next-steps"></a>Sonraki adımlar
Dağıtım dosyalarındaki değişiklikleri, otomasyonu ve yeni Cloud Services (genişletilmiş destek) dağıtımınızın diğer özniteliklerini görmek için [geçiş sonrası değişiklikler](in-place-migration-overview.md#post-migration-changes) bölümünü gözden geçirin. 
