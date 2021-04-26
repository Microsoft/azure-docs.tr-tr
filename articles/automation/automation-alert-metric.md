---
title: Azure Otomasyonu runbook 'larını ölçüm uyarıları ile izleme
description: Bu makalede, runbook tamamlanma durumuna göre bir ölçüm uyarısının nasıl ayarlanacağı açıklanır.
services: automation
ms.date: 08/10/2020
ms.topic: article
ms.openlocfilehash: 3a52824a7030d78647d8a664819f439d92d7296d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100593959"
---
# <a name="monitor-runbooks-with-metric-alerts"></a>Ölçüm uyarıları ile runbook’ları izleme

Bu makalede, runbook tamamlanma durumuna göre [ölçüm uyarısı](../azure-monitor/alerts/alerts-metric-overview.md) oluşturmayı öğreneceksiniz.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[Azure portalda](https://portal.azure.com) oturum açma

## <a name="create-alert"></a>Uyarı oluşturma

Uyarılar, izlemek için bir koşul ve bu koşul karşılandığında gerçekleştirilecek bir eylem tanımlamanızı sağlar.

1. **Tüm hizmetler**' e tıklayıp **Otomasyon hesapları**' nı arayıp seçerek Azure Otomasyonu hizmetini Azure Portal başlatın.

2. Otomasyon hesapları listenizde, uyarı oluşturmak istediğiniz hesabı seçin. 

3. **İzleme** altında **Uyarılar** ' ı seçin ve ardından **+ Yeni uyarı kuralı**' nı seçin. Hedefin kapsamı zaten tanımlanmış ve otomasyon hesabınızla ilişkili.

### <a name="configure-alert-criteria"></a>Uyarı ölçütlerini yapılandırma

1. **Koşul Seç**' e tıklayın. **Sinyal türü** için **ölçümler** ' i seçin ve listeden **toplam iş** ' ı seçin.

2. **Sinyal mantığını Yapılandır** sayfası, uyarıyı tetikleyen mantığı tanımladığınız yerdir. Geçmiş grafiğinde, **Runbook adı** ve **durum** olmak üzere iki boyut sunulur. Boyutlar, sonuçları filtrelemek için kullanılabilen bir ölçüm için farklı özelliklerdir. **Runbook adı** için, uyarı vermek istediğiniz runbook 'u seçin veya tüm runbook 'larda uyarı almak için boş bırakın. **Durum** için, izlemek istediğiniz açılan listeden bir durum seçin. Açılan listede görünen Runbook adı ve durum değerleri yalnızca geçen hafta çalıştırılan işler içindir.

   Açılan listede görünmeyen bir durum veya Runbook üzerinde uyarı vermek istiyorsanız, boyutun yanındaki **özel değer Ekle** seçeneğine tıklayın. Bu eylem, bu boyut için yakın zamanda yayılmayan özel bir değer belirtmenizi sağlayan bir iletişim kutusu açar. Bir özellik için mevcut olmayan bir değer girerseniz, uyarılarınız tetiklenmez.

   > [!NOTE]
   > **Runbook adı** boyutu için bir ad belirtmezseniz, gizli sistem runbook 'larını içeren durum ölçütlerine uyan runbook 'lar varsa, bir uyarı alırsınız.

    Örneğin, bir runbook _başarısız_ **durumu döndürdüğünde** uyarı vermek için, runbook adını belirtmenin yanı sıra özel boyut değerini ekleme **başarısız oldu**.

    :::image type="content" source="./media/automation-alert-metric/specify-dimension-custom-value.png" alt-text="Özel boyut değeri belirtin" border="false":::

3. **Uyarı mantığı** altında, uyarınızın koşulunu ve eşiğini tanımlayın. Tanımlanmış koşullarınızın önizlemesi aşağıda gösterilmiştir.

4. **Temelinde değerlendirilen** altında sorgu için TimeSpan değerini ve bu sorgunun ne sıklıkta çalıştırılmasını istediğinizi seçin. Örneğin, **Dönem** için **son 5 dakika boyunca** ve **Sıklık** için **1 dakikada** bir seçerseniz, uyarı son 5 dakika içinde ölçütlerinizi karşılayan runbook sayısını arar. Bu sorgu her dakikada çalıştırılır ve tanımladığınız uyarı ölçütleri 5 dakikalık bir pencerede artık bulunamazsa, uyarı kendisini çözer. İşiniz bittiğinde **bitti**' ye tıklayın.

   ![Uyarı için bir kaynak seçin](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-the-action-to-take"></a>Gerçekleştirilecek eylemi tanımlayın

1. **Eylem grubu** altında **eylem grubu belirt**' i seçin. Bir eylem grubu, birden fazla uyarı genelinde kullanabileceğiniz bir eylem grubudur. Bunlar, e-posta bildirimleri, runbook 'lar, Web kancaları ve çok daha fazlasını içerebilir ancak bunlarla sınırlı değildir. Eylem grupları ve bir e-posta bildirimi gönderen bir adım hakkında daha fazla bilgi edinmek için bkz. [eylem grupları oluşturma ve yönetme](../azure-monitor/alerts/action-groups.md).

### <a name="define-alert-details"></a>Uyarı ayrıntılarını tanımlama

1. **Uyarı kuralı ayrıntıları**' nın altında, uyarıya kolay bir ad ve açıklama sağlayın. **Önem derecesini** uyarı koşulunuz ile eşleşecek şekilde ayarlayın. 0 ile 5 arasında beş önem derecesi vardır. Uyarılar önem derecesine göre aynı olarak değerlendirilir, iş mantığınızla eşleşecek şekilde önem derecesini de eşleştirebilirsiniz.

1. **Oluşturma sırasında uyarı kuralını etkinleştir** seçeneği için **Hayır** ' ı seçmediğiniz takdirde, varsayılan olarak kurallar oluşturma sırasında etkinleştirilir. Devre dışı bırakılmış durumda oluşturulan uyarılar için, bunları daha sonra hazırsanız etkinleştirebilirsiniz. Değişikliklerinizi kaydetmek için **Uyarı kuralı oluştur** ' u seçin.

## <a name="receive-notification"></a>Bildirim al

Uyarı ölçütleri karşılandığında, eylem grubu tanımlanan eylemi çalıştırır. Bu makalenin örneğinde, bir e-posta gönderilir. Aşağıdaki görüntü, uyarı tetiklendikten sonra aldığınız e-postanın bir örneğidir:

![E-posta uyarısı](./media/automation-alert-activity-log/alert-email.png)

Ölçüm artık tanımlanan eşik dışında olmadığında, uyarı devre dışı bırakılır ve eylem grubu tanımlanan eylemi çalıştırır. E-posta eylemi türü seçilirse, Çözümlenmiş olduğunu bildiren bir çözüm e-postası gönderilir.

## <a name="next-steps"></a>Sonraki adımlar

* Daha fazla bilgi için bkz. [Azure Otomasyonu runbook 'u tetiklemek için uyarı kullanma](automation-create-alert-triggered-runbook.md).
