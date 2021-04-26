---
title: Depolama için Azure Defender'ı yapılandırma
titleSuffix: Azure Storage
description: Hesap etkinliğindeki anormallikleri algılamak ve hesabınıza erişmek için olası zararlı girişimler hakkında bildirim almak için Azure Defender 'ı depolama için yapılandırın.
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: tamram
ms.reviewer: ozgun
ms.openlocfilehash: e2f044ab267365885260b031638572846184bc83
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063194"
---
# <a name="configure-azure-defender-for-storage"></a>Depolama için Azure Defender'ı yapılandırma

Depolama için Azure Defender, depolama hesaplarına erişmeye ve güvenliğini ihlal etmeye yönelik olağan dışı ve zararlı olabilecek girişimleri algılayan ek bir güvenlik zekası katmanı sağlar. Bu koruma katmanı, güvenlik uzmanı olmadan veya güvenlik izleme sistemlerinizi yönetmeden tehditleri ele almanıza olanak tanır.

Etkinlikte anomali oluştuğunda güvenlik uyarıları tetiklenir. Bu güvenlik uyarıları [Azure Güvenlik Merkezi](https://azure.microsoft.com/services/security-center/)ile tümleşiktir ve ayrıca, şüpheli etkinliklerin ayrıntıları ve tehditleri İnceleme ve düzeltme önerileri ile abonelik yöneticilerine e-posta yoluyla da gönderilir.

Hizmet, blob depolamaya ve tehdit algılama için Azure dosyalarına okuma, yazma ve silme isteklerinin kaynak günlüklerini günlüğe kaydeder. Azure Defender 'daki uyarıları araştırmak için Depolama Analizi günlüğünü kullanarak ilgili depolama etkinliğini görüntüleyebilirsiniz. Daha fazla bilgi için, [Azure Portal depolama hesabını izlemek](./manage-storage-analytics-logs.md#configure-logging)için bkz. **oturum açma yapılandırma** .

## <a name="availability"></a>Kullanılabilirlik

Depolama için Azure Defender şu anda Blob depolama, Azure Dosyalar ve Azure Data Lake Storage 2. Nesil için kullanılabilir. Azure Defender’ı destekleyen hesap türleri arasında genel amaçlı v2, blok blobu ve Blob depolama hesapları yer alır. Tüm genel bulutlarda ve ABD kamu bulutlarında kullanılabilen Depolama için Azure Defender diğer bağımsız ve Azure Kamu bulut bölgelerinde kullanılamaz.

Data Lake Storage için etkinleştirilen hiyerarşik ad alanlarına sahip hesaplar, hem Azure Blob depolama API’leri hem de Data Lake Storage API’leri kullanılarak gerçekleştirilen işlemleri destekler. Azure dosya paylaşımları, SMB üzerinden gerçekleştirilen işlemleri destekler.

Ücretsiz 30 günlük deneme sürümü de dahil olmak üzere fiyatlandırma ayrıntıları için bkz. [Azure Güvenlik Merkezi fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/security-center/).

Aşağıdaki listede, depolama için Azure Defender 'ın kullanılabilirliği özetlenmektedir:

- Yayın durumu:
  - [BLOB depolama](https://azure.microsoft.com/services/storage/blobs/) (genel kullanılabilirlik)
  - [Azure dosyaları](../files/storage-files-introduction.md) (genel kullanılabilirlik)
  - Azure Data Lake Storage 2. (genel kullanılabilirlik)
- Larının<br>
    Ticari bulutlar ✔<br>
    ✔ US Gov<br>
    ✘ Çin gov, diğer gov

## <a name="set-up-azure-defender"></a>Azure Defender 'ı ayarlama

Azure Defender 'ı depolama için aşağıdaki bölümlerde açıklanan çeşitli yollarla yapılandırabilirsiniz.

### <a name="azure-security-center"></a>[Azure Güvenlik Merkezi](#tab/azure-security-center)

Azure Defender, Azure Güvenlik Merkezi 'Nde yerleşik olarak bulunur. Azure Defender ' ı aboneliğinizde etkinleştirdiğinizde Azure Storage için Azure Defender, tüm depolama hesaplarınız için otomatik olarak etkinleştirilir. Belirli bir abonelik kapsamındaki depolama hesaplarınız için Azure Defender 'ı aşağıdaki gibi etkinleştirebilir veya devre dışı bırakabilirsiniz:

1. Azure portal **Azure Güvenlik Merkezi 'ni** başlatın [](https://portal.azure.com).
1. Ana menüden **Yönetim** altında, **fiyatlandırma & ayarları**' nı seçin.
1. Azure Defender 'ı etkinleştirmek veya devre dışı bırakmak istediğiniz aboneliği seçin.
1. Abonelik için Azure Defender 'ı etkinleştirmek üzere **Azure Defender '** ı seçin.
1. **Kaynak türüne göre Azure Defender planı seç**' in altında, **depolama** satırını bulun ve **plan** sütununda **etkin** ' i seçin.
1. Yaptığınız değişiklikleri kaydedin.

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-security-center.png" alt-text="Güvenlik Merkezi 'nde depolama için Azure Defender 'ı nasıl etkinleştireceğinizi gösteren ekran görüntüsü":::

Azure Defender artık bu abonelikteki tüm depolama hesapları için etkinleştirilmiştir.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com/)başlatın.
1. Depolama hesabınıza gidin. **Ayarlar** bölümünden **Gelişmiş güvenlik**’i seçin.
1. **Depolama için Azure Defender’ı etkinleştir** seçeneğini belirleyin.

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-portal.png" alt-text="Azure depolama hesabı için Azure Defender 'ı nasıl etkinleştireceğinizi gösteren ekran görüntüsü":::

Azure Defender artık bu depolama hesabı için etkinleştirilmiştir.

### <a name="template"></a>[Şablon](#tab/template)

Azure Defender etkinleştirilmiş bir Azure depolama hesabı dağıtmak için Azure Resource Manager şablonu kullanın. Daha fazla bilgi için bkz. [Gelişmiş tehdit koruması olan depolama hesabı](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="azure-policy"></a>[Azure İlkesi](#tab/azure-policy)

Belirli bir abonelik veya kaynak grubu altındaki depolama hesapları genelinde Azure Defender 'ı etkinleştirmek için Azure Ilkesi kullanın.

1. Azure **Ilke tanımları** sayfasını başlatın.
1. **Depolama hesapları üzerinde Azure Defender 'ı dağıtma** ilkesini arayın.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy-definitions.png" alt-text="Depolama hesapları için Azure Defender 'ı etkinleştirmek üzere ilke uygulama":::

1. Bir Azure aboneliği veya kaynak grubu seçin.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy2.png" alt-text="İlke kapsamı için abonelik veya kaynak grubu seçin ":::

1. İlkeyi atayın.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy1.png" alt-text="Depolama için Azure Defender 'ı etkinleştirmek üzere ilke atama":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell ile bir depolama hesabı için Azure Defender 'ı etkinleştirmek üzere öncelikle [az. Security](https://www.powershellgallery.com/packages/Az.Security) modülünü yüklediğinizden emin olun. Sonra, [Enable-AzSecurityAdvancedThreatProtection](/powershell/module/az.security/enable-azsecurityadvancedthreatprotection) komutunu çağırın. Açılı ayraçlar içindeki değerleri kendi değerlerinizle değiştirmeyi unutmayın:

```azurepowershell
Enable-AzSecurityAdvancedThreatProtection -ResourceId "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/"
```

PowerShell ile bir depolama hesabı için Azure Defender ayarını kontrol etmek için [Get-AzSecurityAdvancedThreatProtection](/powershell/module/az.security/get-azsecurityadvancedthreatprotection) komutunu çağırın. Açılı ayraçlar içindeki değerleri kendi değerlerinizle değiştirmeyi unutmayın:

```azurepowershell
Get-AzSecurityAdvancedThreatProtection -ResourceId "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/"
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLı ile bir depolama hesabı için Azure Defender 'ı etkinleştirmek üzere [az Security ATP Storage Update](/cli/azure/security/atp/storage#az_security_atp_storage_update) komutunu çağırın. Açılı ayraçlar içindeki değerleri kendi değerlerinizle değiştirmeyi unutmayın:

```azurecli
az security atp storage update \
    --resource-group <resource-group> \
    --storage-account <storage-account> \
    --is-enabled true
```

Azure CLı ile bir depolama hesabı için Azure Defender ayarını denetlemek için [az Security ATP Storage Show](/cli/azure/security/atp/storage#az_security_atp_storage_show) komutunu çağırın. Açılı ayraçlar içindeki değerleri kendi değerlerinizle değiştirmeyi unutmayın:

```azurecli
az security atp storage show \
    --resource-group <resource-group> \
    --storage-account <storage-account>
```

---

## <a name="explore-security-anomalies"></a>Güvenlik anomalilerini araştırma

Depolama etkinliğinde anomaliler oluştuğunda güvenlik olayı hakkında bilgi içeren bir e-posta bildirimi alırsınız. Olayın ayrıntılarında şunlar yer alır:

- Anomalinin yapısı
- Depolama hesabı adı
- Olayın zamanı
- Depolama türü
- Olası nedenler
- İnceleme adımları
- Düzeltme adımları

Bu e-posta, olası nedenlere ilişkin ayrıntıların yanı sıra olası tehdidi inceleyip ortadan kaldırmaya yönelik önerilen eylemleri de içerir.

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert-email.png" alt-text="Depolama için Azure Defender uyarı e-postası":::

Azure Güvenlik Merkezi 'nin [güvenlik uyarıları kutucuğunda](../../security-center/security-center-managing-and-responding-alerts.md)geçerli güvenlik uyarılarınızı gözden geçirebilir ve yönetebilirsiniz. Belirli bir uyarıya tıkladığınızda, mevcut tehdidin incelenmesine ve ileride ortaya çıkabilecek tehditleri ele almaya yönelik ayrıntılar ve eylemler sunulur.

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert.png" alt-text="Depolama için Azure Defender uyarısı":::

## <a name="security-alerts"></a>Güvenlik uyarıları

Uyarılar, depolama hesaplarına erişmeye veya açıktan yararlanmaya yönelik olağan dışı ve potansiyel olarak zararlı denemelere göre oluşturulur. Azure depolama için uyarıların listesi için bkz. [Azure Storage uyarıları](../../security-center/alerts-reference.md#alerts-azurestorage).

## <a name="next-steps"></a>Sonraki adımlar

- [Depolama için Azure Defender'a giriş](../../security-center/defender-for-storage-introduction.md)
- [Azure Güvenlik Merkezi](../../security-center/security-center-introduction.md)
- [Azure depolama hesaplarında Günlükler](/rest/api/storageservices/About-Storage-Analytics-Logging)
