---
title: Azure Güvenlik Merkezi 'nin tümleşik iş yükü korumalarını etkinleştirin
description: Azure Defender 'ın Azure Güvenlik Merkezi 'nin korumalarını karma ve çoklu bulut kaynaklarınıza genişletmelerine nasıl imkan sağladığını öğrenin
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 02/24/2021
ms.openlocfilehash: 7124014821c79fa37aa04da8909e3b4ac3bcb4fb
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106492503"
---
# <a name="quickstart-enable-azure-defender"></a>Hızlı başlangıç: Azure Defender 'ı etkinleştirme

Azure Defender 'ın avantajları hakkında bilgi edinmek için bkz. [Security Center Ücretsiz vs Azure Defender etkin](security-center-pricing.md).

## <a name="prerequisites"></a>Önkoşullar

Güvenlik Merkezi hızlı başlangıç ve öğreticiler amacına yönelik olarak Azure Defender 'ı etkinleştirmeniz gerekir. 

Azure Defender ile tüm Azure aboneliğini koruyabilirsiniz ve korumalar, aboneliğin içindeki tüm kaynaklar tarafından devralınır.

Ücretsiz 30 günlük deneme sürümü mevcuttur. Seçtiğiniz para birimindeki ve bölgenize göre fiyatlandırma ayrıntıları için bkz. [Güvenlik Merkezi fiyatlandırması](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="enable-azure-defender-from-the-azure-portal"></a>Azure portal Azure Defender 'ı etkinleştirin

Tehdit koruması özellikleri dahil tüm güvenlik merkezi özelliklerini etkinleştirmek için, ilgili iş yüklerini içeren abonelikte Azure Defender 'ı etkinleştirmeniz gerekir. Çalışma alanı düzeyinde etkinleştirmek, Azure kaynakları için tam zamanında VM erişimi, uyarlamalı uygulama denetimleri ve ağ algılamalarını etkinleştirmez. Ayrıca, çalışma alanı düzeyinde kullanılabilen tek Azure Defender planları, sunucular için Azure Defender ve makinelerde SQL Server 'lar için Azure Defender ' dir.

- **Depolama hesapları Için Azure Defender 'ı** abonelik düzeyinde ya da kaynak düzeyinde etkinleştirebilirsiniz
- **SQL Için Azure Defender 'ı** abonelik düzeyinde veya kaynak düzeyinde etkinleştirebilirsiniz
- **MariaDB/MySQL/PostgreSQL Için Azure veritabanı** için tehdit korumasını yalnızca kaynak düzeyinde etkinleştirebilirsiniz

### <a name="to-enable-azure-defender-on-your-subscriptions-and-workspaces"></a>Azure Defender 'ı aboneliklerinizde ve çalışma alanlarınızdan etkinleştirmek için:

- Azure Defender 'ı bir abonelikte etkinleştirmek için:

    1. Güvenlik Merkezi 'nin ana menüsünde, **fiyatlandırma & ayarları**' nı seçin.
    1. Korumak istediğiniz aboneliği veya çalışma alanını seçin.
    1. Yükseltmek için **Azure Defender '** ı seçin.
    1. **Kaydet**’i seçin.

    > [!TIP]
    > Azure Defender 'daki her planın ayrı olarak fiyatlandırıldığını ve tek tek açık veya kapalı olduğunu fark edeceksiniz. Örneğin, ilişkili bir Azure App Service planına sahip olmayan aboneliklerde App Service için Azure Defender 'ı devre dışı bırakmak isteyebilirsiniz. 

    :::image type="content" source="./media/security-center-pricing/pricing-tier-page.png" alt-text="Portalda Güvenlik Merkezi 'nin fiyatlandırma sayfası":::

- Azure Defender 'ı birden çok abonelik veya çalışma alanı üzerinde etkinleştirmek için:

    1. Güvenlik Merkezi 'nin kenar çubuğundan **Başlarken**' i seçin.

        **Yükseltme** sekmesi, ekleme için uygun olan abonelikleri ve çalışma alanlarını listeler.

        :::image type="content" source="./media/enable-azure-defender/get-started-upgrade-tab.png" alt-text="Başlarken sayfasının yükseltme sekmesi"::: 

    1. Azure Defender 'ı **etkinleştirmek için abonelikleri ve çalışma alanlarını seçin** listesinde, Yükseltilecek abonelikleri ve çalışma alanlarını seçin ve Azure Defender 'ı etkinleştirmek için **Yükselt** ' i seçin.

       - Deneme yanılma uygun olmayan abonelikler ve çalışma alanları ' nı seçerseniz, bir sonraki adım bunları yükseltir ve ücretler başlatılır.
       - Ücretsiz deneme için uygun olan bir çalışma alanı seçerseniz, bir sonraki adım bir denemeye başlar.

        :::image type="content" source="./media/enable-azure-defender/upgrade-selected-workspaces-and-subscriptions.png" alt-text="Başlangıç sayfasından tüm seçili çalışma alanlarını ve abonelikleri yükseltin":::


## <a name="next-steps"></a>Sonraki adımlar

Azure Defender 'ı etkinleştirmiş olduğunuza göre, [Azure Güvenlik Merkezi 'nde otomatik sağlama aracıları ve uzantıları](security-center-enable-data-collection.md)'nda açıklanan gerekli aracılar ve uzantılar tarafından otomatik veri toplamayı etkinleştirin.