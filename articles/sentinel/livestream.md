---
title: Tehditleri algılamak için Azure Sentinel 'de Livestream 'i kullanma | Microsoft Docs
description: Bu makalede, verileri izlemek için Azure Sentinel 'de Livestream 'in nasıl kullanılacağı açıklanır.
services: sentinel
documentationcenter: na
author: cabailey
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: cabailey
ms.openlocfilehash: d1d048507946850192c1c6f8ca9024c6f9ddf8ca
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515128"
---
# <a name="use-hunting-livestream-in-azure-sentinel-to-detect-threats"></a>Tehditleri algılamak için Azure Sentinel 'de Livestream 'i kullanma

> [!IMPORTANT]
> Azure Sentinel 'de Livestream 'i Şu anda genel önizleme aşamasındadır.
> Bu özellik, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Yeni oluşturulan sorguları olay meydana geldiğinde test etmenize olanak sağlayan etkileşimli oturumlar oluşturmak, bir eşleşme bulunduğunda oturumlardan bildirimler almak ve gerekirse araştırmalar başlatmak için Livestream 'i kullanın. Herhangi bir Log Analytics sorgusunu kullanarak hızlı bir şekilde canlı akış oturumu oluşturabilirsiniz.

- **Yeni oluşturulan sorguları olay gerçekleştiğinde sına**
    
    Olaylara etkin olarak uygulanan geçerli kurallara herhangi bir çakışma olmadan sorgu test edebilir ve ayarlayabilirsiniz. Bu yeni sorguların beklendiği gibi çalıştığını doğruladıktan sonra, bir uyarıya oturumu destekleyen bir seçenek belirleyerek bunları özel uyarı kurallarına kolayca yükseltebilirsiniz.

- **Tehditler gerçekleştiğinde bildirim alın**
    
    Tehdit veri akışlarını toplanmış günlük verileriyle karşılaştırabilir ve bir eşleşme gerçekleştiğinde bilgilendirilirsiniz. Tehdit veri akışları, olası veya geçerli tehditlerle ilgili verilerin devam eden akışlardır ve bu nedenle bildirim, kuruluşunuzun potansiyel bir tehdidi olduğunu gösterebilir. Özel bir uyarı kuralını korumanın fazla olması gerekmeden olası bir sorun hakkında bildirim almak istediğinizde özel bir uyarı kuralı yerine bir Livestream oturumu oluşturun.

- **Araştırmamaları Başlat**
    
    Konak veya Kullanıcı gibi bir varlıkla ilgili etkin bir araştırma varsa, günlük verilerinde söz konusu varlık üzerinde olduğu gibi belirli (veya herhangi bir) etkinlik görüntüleyebilirsiniz. Bu etkinlik gerçekleştiğinde size bildirilmesini sağlayabilirsiniz.


## <a name="create-a-livestream-session"></a>Livestream oturumu oluşturma

Mevcut bir arama sorgusundan Livestream oturumu oluşturabilir veya oturumunuzu sıfırdan oluşturabilirsiniz.

1. Azure portal, **Sentinel** > **tehdit yönetimi** ** > arama**' ya gidin.

2. Bir hunme sorgusundan canlı akış oturumu oluşturmak için:
    
    1. **Sorgular** sekmesinden kullanılacak arama sorgusunu bulun.
    2. Sorguya sağ tıklayın ve **Livestream 'e Ekle**' yi seçin. Örneğin:
    
    > [!div class="mx-imgBorder"]
    > Azure Sentinel arama sorgusundan Livestream oturumu oluşturma ![](./media/livestream/livestream-from-query.png)

3. Sıfırdan bir Livestream oturumu oluşturmak için: 
    
    1. **Livestream** sekmesini seçin
    2. **Livestream 'e git**' i seçin.
    
4. **Livestream** dikey penceresinde:
    
    - Bir sorgudan Livestream 'i başlattıysanız, sorguyu gözden geçirin ve yapmak istediğiniz değişiklikleri yapın.
    - Sıfırdan Livestream 'i başlattıysanız sorgunuzu oluşturun. 

5. Komut çubuğundan **oynat** ' ı seçin.
    
    Komut çubuğunun altındaki durum çubuğu, Livestream 'in çalışıp çalışmadığını veya duraklatılmadığını gösterir. Aşağıdaki örnekte, oturum çalışıyor:
    
    > [!div class="mx-imgBorder"]
    > Azure Sentinel arama](./media/livestream/livestream-session.png) ![Livestream oturumu oluşturma

6. Komut çubuğundan **Kaydet** ' i seçin.
    
    **Duraklat**' ı seçmezseniz, Azure Portal oturumunuz kapatılana kadar oturum çalışmaya devam eder.

## <a name="view-your-livestream-sessions"></a>Livestream oturumlarınızı görüntüleyin

1. Azure portal, > **tehdit yönetimi** ** >  > ** **Livestream** sekmesi ' **ne gidin.**

2. Görüntülemek veya düzenlemek istediğiniz Livestream oturumunu seçin. Örneğin:
    
    > [!div class="mx-imgBorder"]
    > Azure Sentinel arama sorgusundan Livestream oturumu oluşturma ![](./media/livestream/livestream-tab.png)
    
    Seçtiğiniz Livestream oturumunuz açılarak oynatma, duraklatma, düzenleme gibi bir şekilde açılır.

## <a name="receive-notifications-when-new-events-occur"></a>Yeni olaylar gerçekleştiğinde bildirim alın

Yeni olaylar için Livestream bildirimleri Azure portal bildirimleri kullandığından, Azure portal her kullanışınızda bu bildirimleri görürsünüz. Örneğin:

![Livestream için Azure portal bildirimi](./media/livestream/notification.png)

**Livestream** dikey penceresini açmak için bildirimi seçin.
 
## <a name="elevate-a-livestream-session-to-an-alert"></a>Canlı akış oturumunu bir uyarıya yükseltme

İlgili Livestream oturumunda komut çubuğundan **uyarıya Yükselt** ' i seçerek, bir canlı akış oturumunu yeni bir uyarıya yükseltebilirsiniz:

> [!div class="mx-imgBorder"]
> Livestream oturumunu bir uyarıya yükseltmek ![](./media/livestream/elevate-to-alert.png)

Bu eylem, Livestream oturumuyla ilişkili sorguyla önceden doldurulan kural oluşturma Sihirbazı ' nı açar.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Azure Sentinel 'de Livestream 'i nasıl kullanacağınızı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:


- [Tehditler için proaktif araya](hunting.md)
- [Otomatik arama kampanyalarını çalıştırmak için not defterlerini kullanın](notebooks.md)
