---
title: Sunucu parametrelerini Yapılandırma-Azure portal-PostgreSQL için Azure veritabanı-tek sunucu
description: Bu makalede, Azure portal aracılığıyla PostgreSQL için Azure veritabanı 'nda Postgres parametrelerinin nasıl yapılandırılacağı açıklanır.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 02/28/2018
ms.openlocfilehash: 2e9cecc03c1240d625fa8f6bbdcd633ada978082
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604218"
---
# <a name="configure-server-parameters-in-azure-database-for-postgresql---single-server-via-the-azure-portal"></a>PostgreSQL için Azure veritabanı 'nda sunucu parametrelerini Yapılandırma-Azure portal aracılığıyla tek sunucu 
Azure portal aracılığıyla bir PostgreSQL için Azure veritabanı sunucusu için yapılandırma parametrelerini listeleyebilir, gösterebilir ve güncelleştirebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar
Bu nasıl yapılır kılavuzunda adım adım yönergeler için şunlar gerekir:
- [PostgreSQL için Azure veritabanı sunucusu](quickstart-create-server-database-portal.md)

## <a name="viewing-and-editing-parameters"></a>Parametreleri görüntüleme ve görüntüleme
1. [Azure portalını](https://portal.azure.com) açın.

2. PostgreSQL için Azure Veritabanı sunucunuzu seçin.

3. **Ayarlar** bölümünde **sunucu parametreleri**' ni seçin. Sayfa, parametrelerin, değerlerinin ve açıklamalarının listesini gösterir.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png" alt-text="Parametreler için genel bakış sayfası":::

4. Client_min_messages gibi numaralandırılmış tür parametrelerinin olası değerlerini görmek için **açılan** düğmeyi seçin.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png" alt-text="Açılan listesini numaralandır":::

5. Cpu_index_tuple_cost gibi sayısal parametrelerin olası değerlerinin aralığını görmek için **ı** (bilgi) düğmesini seçin veya üzerine gelin.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/4-information-button.png" alt-text="bilgi düğmesi":::

6. Gerekirse, belirli bir parametreye daraltmak için **arama kutusunu** kullanın. Arama, parametrelerin adı ve açıklamasıdır.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/5-search.png" alt-text="Arama sonuçları":::

7. Ayarlamak istediğiniz parametre değerlerini değiştirin. Bir oturumda yaptığınız tüm değişiklikler mor renkle vurgulanır. Değerleri değiştirdikten sonra **Kaydet**' i seçebilirsiniz. Ya da değişikliklerinizi **atabilirsiniz** .
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png" alt-text="Değişiklikleri kaydetme veya atma":::

8. Parametreler için yeni değerler kaydettiyseniz, **Tümünü Sıfırla**' yı seçerek her şeyi varsayılan değerlere geri döndürebilirsiniz.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png" alt-text="Tümünü Varsayılana sıfırla":::

## <a name="next-steps"></a>Sonraki adımlar
Şunları öğrenin:
- [PostgreSQL için Azure veritabanı 'nda sunucu parametrelerine genel bakış](concepts-servers.md)
- [Azure CLı kullanarak parametreleri yapılandırma](howto-configure-server-parameters-using-cli.md)
