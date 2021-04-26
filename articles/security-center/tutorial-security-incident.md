---
title: Uyarı yanıtı öğreticisi-Azure Güvenlik Merkezi
description: Bu öğreticide, güvenlik uyarılarını önceliklendirme ve bir uyarının kök neden & kapsamını belirleme hakkında bilgi edineceksiniz.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 181e3695-cbb8-4b4e-96e9-c4396754862f
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 41f96202bffc0169be9705cd7e67c1eedbecbc05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102453453"
---
# <a name="tutorial-triage-investigate-and-respond-to-security-alerts"></a>Öğretici: Güvenlik uyarılarını önceliklendirme, araştırma ve yanıtlama
Güvenlik Merkezi, bulut kaynaklarınızda potansiyel olabilecek kötü amaçlı etkinlikler hakkında sizi uyarmak için gelişmiş analiz ve tehdit bilgilerini kullanarak hibrit bulut iş yüklerinizi sürekli çözümler. Ayrıca, uyarıları diğer güvenlik ürünleri ve hizmetlerinden Güvenlik Merkezi 'ne tümleştirebilirsiniz. Bir uyarı oluşturulduktan sonra olası güvenlik sorununu araştırmak ve düzeltmek için Swift eylemi gerekir. 

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * Güvenlik uyarılarını önceliklendirme
> * Kök nedenin belirlenmesi için bir güvenlik uyarısı araştırın
> * Güvenlik uyarısına yanıt verme ve bu temel nedeni azaltma

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar
Bu öğreticide ele alınan özellikler arasında ilerlemek için Azure Defender 'ın etkinleştirilmiş olması gerekir. Azure Defender 'ı ücretsiz olarak deneyebilirsiniz. Daha fazla bilgi için bkz. [fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/security-center/). [Güvenlik Merkezi](security-center-get-started.md) 'ni kullanmaya başlama hızlı başlangıç, nasıl yükselteceğiniz konusunda size yol gösterir.


## <a name="triage-security-alerts"></a>Güvenlik uyarılarını önceliklendirme
Güvenlik Merkezi, tüm güvenlik uyarılarının birleşik bir görünümünü sağlar. Güvenlik uyarıları, algılanan etkinliğin önem derecesine göre derecelendirilir. 

Uyarıları **güvenlik uyarıları** sayfasından önceliklendirin:

:::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Azure Güvenlik Merkezi 'nin güvenlik uyarıları listesi":::

Öncelikle hangi uyarının araştırılacağını belirlemek için ortamınızdaki etkin güvenlik uyarılarını gözden geçirmek için bu sayfayı kullanın.

Güvenlik uyarılarını önceliklendirme sırasında, uyarıları daha yüksek önem derecesine sahip uyarılara göre uyarı önem derecesine göre önceliklendirin. [Uyarıların nasıl sınıflandırıldığı](security-center-alerts-overview.md#how-are-alerts-classified)konusunda uyarılar önem derecesi hakkında daha fazla bilgi edinin.

> [!TIP]
> Azure Güvenlik Merkezi 'ni, Azure Sentinel dahil en popüler SıEM çözümlerine bağlayabilirsiniz ve istediğiniz araçtan uyarıları kullanabilirsiniz. [SıEM, SOAR veya BT hizmet yönetimi çözümüne akış uyarıları](export-to-siem.md)hakkında daha fazla bilgi edinin.


## <a name="investigate-a-security-alert"></a>Güvenlik uyarısını araştırın

Önce hangi uyarının araştırılacağını karardığınızda:

1. İstenen uyarıyı seçin.
1. Uyarı Genel Bakış sayfasında, ilk olarak araştırılacağı kaynağı seçin.
1. Sol bölmeden, güvenlik uyarısıyla ilgili üst düzey bilgileri gösteren araştırmanıza başlayın.

    :::image type="content" source="./media/tutorial-security-incident/alert-details-left-pane.png" alt-text="Uyarı Ayrıntıları sayfasının, üst düzey bilgileri vurgulayan sol bölmesi":::

    Bu bölme şunları gösterir:
    - Uyarı önem derecesi, durum ve etkinlik süresi
    - Algılanan kesin etkinliği açıklayan açıklama
    - Etkilenen kaynaklar
    - MITRE ATT&CK matrisinde etkinliğin zincir hedefini Sonlandır

1. Şüpheli etkinliği araştırmanıza yardımcı olabilecek daha ayrıntılı bilgiler için **Uyarı ayrıntıları** sekmesini inceleyin.

1. Bu sayfadaki bilgileri gözden geçirdikten sonra bir Yanıt ile devam etmeniz yeterli olabilir. Daha fazla ayrıntıya ihtiyacınız varsa:

    - Algılanan etkinliğin hatalı pozitif olup olmadığını doğrulamak için kaynak sahibine başvurun.
    - Saldırıya uğrayan kaynaklar tarafından oluşturulan ham günlükleri araştırın

## <a name="respond-to-a-security-alert"></a>Güvenlik uyarısına yanıt verme
Bir uyarıyı araştırdıktan ve kapsamını belirledikten sonra, Azure Güvenlik Merkezi 'nin içinden güvenlik uyarısına yanıt verebilirsiniz:

1.  Önerilen yanıtları görmek için **eylem al** sekmesini açın.

    :::image type="content" source="./media/tutorial-security-incident/alert-details-take-action.png" alt-text="Güvenlik uyarıları eylem al sekmesi" lightbox="./media/tutorial-security-incident/alert-details-take-action.png":::

1.  Sorunu azaltmak için gereken el ile araştırma adımları için **tehdit riskini azaltma** bölümünü gözden geçirin.
1.  Kaynaklarınızın güvenliğini sağlamak ve bu türden gelecek saldırıları engellemek için, **gelecekteki saldırıları engelleme** bölümündeki güvenlik önerilerini düzeltin.
1.  Otomatikleştirilmiş yanıt adımlarıyla bir mantıksal uygulamayı tetiklemek için **otomatik yanıtı Tetikle** bölümünü kullanın.
1.  Algılanan etkinlik kötü amaçlı *değilse* , **benzer Uyarıları Gizle** bölümünü kullanarak bu türden gelecek uyarıları gizleyebilirsiniz.

1.  Uyarı araştırmasını tamamlayıp uygun şekilde yanıtladığınızda, durumu **kapatıldı** olarak değiştirin.

    :::image type="content" source="./media/tutorial-security-incident/set-status-dismissed.png" alt-text="Uyarının durumunu ayarlama":::

    Bu, uyarıyı ana uyarılar listesinden kaldırır. **Kapatılan** durum ile tüm uyarıları görüntülemek için uyarılar listesi sayfasından filtreyi kullanabilirsiniz.

1.  Microsoft 'a uyarı hakkında geri bildirim sağlamanızı öneriyoruz:
    1. Uyarı **yararlı** veya **faydalı** olarak işaretleniyor.
    1. Bir neden seçin ve açıklama ekleyin.

        :::image type="content" source="./media/tutorial-security-incident/alert-feedback.png" alt-text="Bir uyarının yararlılığı hakkında Microsoft 'a geri bildirim sağlama":::

    > [!TIP]
    > Algoritmalarınızı geliştirmek ve daha iyi güvenlik uyarıları sağlamak için geri bildirimlerinizi gözden geçiririz.

## <a name="end-the-tutorial"></a>Öğreticiyi sonlandırın

Bu koleksiyondaki diğer hızlı başlangıçlar ve öğreticiler bu hızlı başlangıcı temel alır. Sonraki hızlı başlangıçlarla ve öğreticilerle çalışmaya devam etmeyi planlıyorsanız, otomatik sağlamayı ve Azure Defender 'ı devre dışı bırakın. 

Devam etmeyi planlamıyorsanız veya bu özelliklerden birini devre dışı bırakmak istiyorsanız:

1. Güvenlik Merkezi ana menüsüne dönüp **fiyatlandırma ve ayarlar**' ı seçin.
1. Uygun aboneliği seçin.
1. Düşürme için **Azure Defender kapalı**' yı seçin.
1. Otomatik sağlamayı devre dışı bırakmak için, **veri toplama** sayfasını açın ve **otomatik sağlamayı** **kapalı** olarak ayarlayın.
1. **Kaydet**’i seçin.

>[!NOTE]
> Otomatik sağlamayı devre dışı bırakmak, zaten aracısına sahip olan Azure VM 'lerinden Log Analytics aracısını kaldırmaz. Otomatik sağlamanın devre dışı bırakılması, kaynaklarınızın güvenliğinin izlenmesini kısıtlar.
>

## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, bir güvenlik uyarısını yanıtlarken kullanılacak güvenlik merkezi özellikleri hakkında bilgi edindiniz. İlgili malzemeler için bkz:

- [Key Vault için Azure Defender uyarılarına yanıt verme](defender-for-key-vault-usage.md)
- [Güvenlik uyarıları - başvuru kılavuzu](alerts-reference.md)
- [Azure Defender'a giriş](azure-defender.md)
