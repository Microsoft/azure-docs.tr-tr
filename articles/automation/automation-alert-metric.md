---
title: Azure Otomasyonu runbook'ları ile ölçüm uyarıları izleme
description: Bu makalede, Azure Otomasyonu runbook'ları dışına ölçümlere göre izleme yoluyla gösterilmektedir.
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 11/01/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 142fb84624c2b0d3d92868aae5794792ed90b577
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478022"
---
# <a name="monitoring-runbooks-with-metric-alerts"></a>Ölçüm uyarıları ile runbook'ları izleme

Bu makalede, runbook'ların tamamlanma durumu temelinde uyarılar oluşturma hakkında bilgi edinin.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[https://portal.azure.com](https://portal.azure.com ) adresinden Azure'da oturum açın

## <a name="create-alert"></a>Uyarı oluşturma

Uyarılar izlemek için bir koşul ve bu koşul karşılandığında gerçekleştirilecek bir eylem tanımlamanızı sağlar.

Azure portalında, Otomasyon hesabınıza gidin. Altında **izleme**seçin **uyarılar** tıklatıp **+ yeni uyarı kuralı**. Kapsam hedef için Otomasyon hesabınıza zaten tanımlandı.

### <a name="configure-alert-criteria"></a>Uyarı ölçütleri yapılandırma

1. Tıklayın **+ Ölçüt Ekle**. Seçin **ölçümleri** için **sinyal türü**ve **toplam iş** tablosundan.

2. **Sinyal mantığını yapılandırma** sayfa, uyarıyı tetikleyen mantığını burada tanımlarsınız. İki boyutlarla sunulur geçmiş grafiğinin altında **Runbook adı** ve **durumu**. Sonuçları filtrelemek için kullanılan bir ölçüm için farklı özellikleri boyutlarıdır. İçin **Runbook adı**, uyarı veya uyarı tüm runbook'ları boş bırakın istediğiniz runbook'u seçin. İçin **durumu**, bir durum için izlemek istediğiniz açılır listeden seçin. Açılan menüde görüntülenen runbook adını ve durumunu geçen hafta içinde çalışan sahip işler için değerler.

   Bir durum veya açılır listede gösterilmeyen runbook uyarı isterseniz **\+** boyut yanında. Bu eylem, o boyut için yakın zamanda yayılan edilmemiş bir özel değer girin olanak tanıyan bir iletişim kutusu açılır. Bir özellik için mevcut olmayan bir değer girerseniz, uyarıyı tetikleyen olmaz.

   > [!NOTE]
   > İçin bir ad uygulamazsanız **RunbookName** boyut, gizli bir sistem runbook'ları içeren durumu ölçütlerini karşılayan tüm runbook'ları varsa, bir uyarı alırsınız.

3. Altında **uyarı mantığı**, koşul ve, uyarı için eşiğin tanımlayın. Koşulunuzu tanımlanan önizlemesini altında gösterilir.

4. Altında **göre Evaluated**, sorgu için zaman aralığını seçin ve bu sorgunun hangi sıklıkta güncelleştirileceğini çalıştı. Örneğin, seçtiğiniz **son 5 dakika üzerinden** için **süresi** ve **her 1 dakikada** için **sıklığı**, uyarı numarayı arar son 5 dakika boyunca ölçütlerinizi karşılayan runbook. Bu sorgu dakikada çalıştırılır ve kendi tanımladığınız Uyarı ölçütleri artık bulunamıyor sonra 5 dakika penceresinde uyarıyı çözümler. Tamamladığınızda **Bitti**’ye tıklayın.

   ![Uyarı için bir kaynak seçin](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-alert-details"></a>Uyarı ayrıntılarını tanımlama

1. **2. Uyarı ayrıntılarını tanımlayın**, uyarıya kolay bir ad verip bir açıklama ekleyin. Ayarlama **önem derecesi** uyarı koşulunuzu eşleştirilecek. 0 ile 5 arasında değişen beş önem dereceleri vardır. Uyarılar kabul edilir aynı bağımsız önem derecesi iş mantığınızı eşleştirilecek önem eşleşebilir.

1. Bölümünün altında tamamlandıktan sonra Kuralı etkinleştirmek olanak tanıyan bir düğme vardır. Varsayılan kurallar oluşturma sırasında etkinleştirilir. Hayır'ı seçin, uyarı oluşturabilir ve alanında oluşturulur bir **devre dışı bırakılmış** durumu. Gelen **kuralları** sayfa Azure İzleyici'de, onu seçin ve tıklayın **etkinleştirme** hazır olduğunuzda, uyarıyı etkinleştirmek için.

### <a name="define-the-action-to-take"></a>Gerçekleştirilecek eylemi tanımlayın

1. **3. Eylem grubunu tanımlama** bölümünden **+ Yeni eylem grubu**’na tıklayın. Bir eylem grubu arasında birden fazla uyarı kullanabileceğiniz Eylemler grubudur. Bunlar içerebilir ancak e-posta bildirimleri, runbook'ları, Web kancaları ve çok daha fazlası için sınırlı değildir. Eylem grupları hakkında daha fazla bilgi edinmek için bkz. [Eylem grupları oluşturma ve yönetme](../azure-monitor/platform/action-groups.md)

1. **Eylem grubu adı** kutusuna bir kolay ad bir de kısa ad yazın. Bu eylem grubu kullanılarak bildirim gönderildiğinde tam grup adı yerine kısa ad kullanılır.

1. İçinde **eylemleri** bölümüne **eylem türü**seçin **e-posta/SMS/anında iletme/ses**.

1. **E-posta/SMS/Anında İletme/Ses** sayfasında buna bir ad verin. **E-posta** onay kutusunu işaretleyin ve kullanılması için geçerli bir e-posta adresi girin.

   ![E-posta eylem grubunu yapılandırma](./media/automation-alert-activity-log/add-action-group.png)

1. **E-posta/SMS/Anında İletme/Ses** sayfasında **Tamam**’a tıklayarak sayfayı kapatın ve **Eylem grubu ekle** sayfasında **Tamam**’a tıklayarak bu sayfayı da kapatın. Bu sayfada belirtilen adı olarak kaydedilmiş **eylem adı**.

1. İşlem tamamlandığında **Kaydet**’e tıklayın. Bu eylem, bir runbook'un belirli bir durumla Tamamlandı olduğunda uyarı kuralı oluşturur.

> [!NOTE]
> Bir e-posta adresi için bir eylem grubu eklerken adresi için bir eylem grubu eklenmiş belirten bir bildirim e-posta gönderilir.

## <a name="notification"></a>Bildirim

Uyarı ölçütleri karşılandığında eylem grubu tanımlanan eylem çalıştırır. Bu makaledeki örnekte, bir e-posta gönderilir. Aşağıdaki görüntüde, uyarıyı tetikleyen sonra aldığınız e-posta örneğidir:

![E-posta uyarısı](./media/automation-alert-activity-log/alert-email.png)

Ölçüm artık tanımlanan eşiği dışında olduğunda, uyarıyı devre dışı bırakılır ve tanımladığı eylem eylem grubu çalıştırır. Bir e-posta eylem türü seçtiyseniz çözüldükten belirten bir çözüm e-posta gönderilir.

## <a name="next-steps"></a>Sonraki adımlar

Otomasyon hesabınızda alertings tümleştirebilirsiniz diğer yollar hakkında bilgi edinmek için aşağıdaki makaleye geçin.

> [!div class="nextstepaction"]
> [Azure Otomasyonu runbook'u tetiklemek için bir uyarı kullanın](automation-create-alert-triggered-runbook.md)
