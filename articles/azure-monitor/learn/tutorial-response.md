---
title: Azure Log Analytics Uyarılarıyla olaylara yanıt verme | Microsoft Docs
description: Bu öğretici, çalışma alanınızdaki önemli bilgileri belirlemek ve sorunları önceden bildirmek veya bunları düzeltme girişiminde bulunmak amacıyla eylemler çağırmak için Log Analytics ile uyarı kullanmayı anlamanıza yardımcı olur.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: tutorial
author: MGoedtel
ms.author: magoedte
ms.date: 10/05/2018
ms.custom: mvc
ms.openlocfilehash: a30b7569c14aaccb40b357cc5acb82b0e32d2895
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72894649"
---
# <a name="respond-to-events-with-azure-monitor-alerts"></a>Azure İzleyici Uyarıları ile olaylara yanıt verme
Azure İzleyici'deki uyarılar, Log Analytics deponuzdaki önemli bilgileri belirleyebilir. Bunlar düzenli aralıklarla otomatik olarak günlük aramaları çalıştıran uyarı kuralları tarafından oluşturulur. Günlük aramasının sonuçları belirli ölçütlerle eşleşirse bir uyarı kaydı oluşturulur ve kayıt otomatik bir yanıt gerçekleştirmek için yapılandırılabilir.  Bu öğretici, [Log Analytics verilerinin panolarını oluşturma ve paylaşma](tutorial-logs-dashboards.md) öğreticisinin devamı niteliğindedir.   

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Uyarı kuralı oluşturma
> * E-posta bildirimi göndermek için Eylem Grubu yapılandırma

Bu öğreticideki örneği tamamlamak için [Log Analytics çalışma alanına bağlı](../../azure-monitor/learn/quick-collect-azurevm.md) mevcut bir sanal makinenizin olması gerekir.  

## <a name="sign-in-to-azure-portal"></a>Azure portalında oturum açın
[https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın. 

## <a name="create-alerts"></a>Uyarı oluşturma
Uyarılar, Azure İzleyici'deki uyarı kuralları tarafından oluşturulur ve kaydedilmiş sorguları veya özel günlük aramalarını düzenli aralıklarla otomatik olarak çalıştırabilir.  Belirli performans ölçümleri temelinde veya belirli bir zaman aralığında bir olay sayısı oluşturulduğunda, bir olay olmadığında ya da belirli olaylar oluşturulduğunda uyarılar oluşturabilirsiniz.  Örneğin uyarılar ortalama CPU kullanımı belirli bir eşiği aştığında, eksik bir güncelleştirme algılandığında veya belirli bir Windows hizmetinin veya Linux daemon'unun çalışmadığının algılanması üzerine bir olay oluşturulduğunda bunu size bildirmek için kullanılabilir.  Günlük aramasının sonuçları belirli ölçütlerle eşleşiyorsa bir uyarı oluşturulur. Kural daha sonra otomatik olarak, size bir uyarıyı bildirmek veya başka bir işlemi çağırmak gibi bir veya daha fazla eylemi çalıştırabilir. 

Aşağıdaki örnekte, [Veri görselleştirme öğreticisinde](tutorial-logs-dashboards.md) kaydedilen *Azure VM'leri - İşlemci Kullanımı* sorgusuna dayanarak bir metrik ölçüm uyarı kuralı oluşturacaksınız.  Yüzde 90'lık bir eşiği aşan her sanal makine için bir uyarı oluşturulur.  

1. Azure portalında **Tüm hizmetler**’e tıklayın. Kaynak listesinde **Log Analytics** yazın. Yazmaya başladığınızda liste, girişinize göre filtrelenir. **Log Analytics**’i seçin.
2. Sol bölmede, **Uyarılar**'ı seçin ve ardından yeni bir uyarı oluşturmak için sayfanın üst kısmındaki **Yeni Uyarı Kuralı**'na tıklayın.<br><br> ![Yeni uyarı kuralı oluşturma](./media/tutorial-response/alert-rule-02.png)<br>
3. İlk adımda, **Uyarı Oluştur** bölümünde, bu günlük tabanlı bir uyarı sinyali olduğundan kaynak olarak Log Analytics çalışma alanınızı seçeceksiniz.  Birden fazla aboneliğiniz varsa aşağı açılan listeden daha önce oluşturulan VM'yi ve Log Analytics çalışma alanını içeren bir **Abonelik** seçerek sonuçları filtreleyin.  **Kaynak Türü**'nü aşağı açılan listeden **Log Analytics**'i seçerek filtreleyin.  Son olarak, **Kaynak** **DefaultLAWorkspace**'i ve sonra **Bitti**'yi seçin.<br><br> ![Uyarı oluşturma 1. adım görevi](./media/tutorial-response/alert-rule-03.png)<br>
4. **Uyarı Ölçütleri** bölümünde, kaydettiğimiz sorguyu seçmek için **Ölçüt Ekle**'yi tıklatın ve uyarı kuralının uyacağı mantığı belirtin.  **Sinyal mantığını yapılandır** bölmesinde listeden *Azure VM'leri - İşlemci Kullanımı*'nı seçin.  Bölme, uyarının yapılandırma ayarlarını göstermek için güncelleşir.  Üstte, seçilen sinyal için son 30 dakikanın sonuçları ve arama sorgusunun kendisi gösterilir.  
5. Uyarıyı aşağıdaki bilgilerle yapılandırın:  
   a. Aşağı açılan **Tetikleyici** listesinden **Metrik ölçüm**'ü seçin.  Bir metrik ölçümü, sorgudaki belirttiğimiz eşiği aşan her nesne için bir uyarı oluşturacaktır.  
   b. **Koşul** için **Büyüktür**'ü seçin ve **Eşik** için **90**'ı girin.  
   c. Uyarı Tetikleyicisi bölümünde **Art arda ihlaller**'i ve seçin ve aşağı açılan listeden **Büyüktür**'ü seçip 3 değerini girin.  
   d. Değerlendirme ölçütü bölümünde **Süre** değerini **30** dakikaya ayarlayın. Kural beş dakikada bir çalışır ve geçerli saatten önceki otuz dakika içinde oluşturulan kayıtları döndürür.  Sürenin daha uzun tutulması veri gecikme süresini artırabilir ve uyarının hiç tetiklenmediği hatalı negatif durumlarından kaçınmak için sorgunun veri döndürmesini sağlar.  
6. **Bitti**'ye tıklayarak uyarı kuralını tamamlayın.<br><br> ![Uyarı sinyalini yapılandırma](./media/tutorial-response/alert-signal-logic-02.png)<br> 
7. Şimdi ikinci adıma geçiyoruz. Uyarınız için **Uyarı kuralı adı** alanına **CPU yüzdesi 90'dan büyük** gibi bir ad girin.  Uyarının ayrıntılarını veren bir **Açıklama** girin ve sağlanan seçeneklerden **Önem düzeyi** için **Kritik (Önem derecesi 0)** değerini belirleyin.<br><br> ![Uyarı ayrıntılarını yapılandırma](./media/tutorial-response/alert-signal-logic-04.png)<br>
8. Oluşturmadan hemen sonra uyarı kuralını etkinleştirmek için, **Oluşturulduktan sonra kuralı etkinleştir** için varsayılan değeri kabul edin.
9. Üçüncü ve son adım olarak, bir uyarı tetiklendiğinde aynı eylemlerin yapılmasını sağlayan tanımladığınız her kural için kullanılabilecek bir **Eylem Grubu** belirtin.  Yeni bir eylem grubunu aşağıdaki bilgilerle yapılandırın:  
   a. **Yeni eylem grubu**'nu seçerseniz **Eylem grubu ekle** bölmesi görünür.  
   b. **Eylem grubu adı** olarak **BT İşlemleri - Bildirim** gibi bir ad ve **btisl-b** gibi bir **Kısa ad** belirtin.  
   c. **Abonelik** ve **Kaynak grubu** varsayılan değerlerinin doğru olduğundan emin olun. Değilse, aşağı açılan listeden doğru olanı seçin.   
   d. Eylemler bölümünde, eylem için **E-posta Gönder** gibi bir ad belirtin ve **Eylem Türü** altında aşağı açılan listeden **E-posta/SMS/Gönderme/Sesli** seçeneğini belirleyin. **E-posta/SMS/Gönderme/Sesli** özellikler bölmesi, ek bilgi sağlamak için sağa doğru açılır.  
   e. **E-posta/SMS/Gönderme/Sesli** bölmesinde **E-posta**'yı etkinleştirin ve iletiyi teslim etmek için geçerli bir e-posta SMTP adresi sağlayın.  
   f. Değişikliklerinizi kaydetmek için **Tamam**’a tıklayın.<br><br> 

    ![Yeni eylem grubu oluşturma](./media/tutorial-response/action-group-properties-01.png)

10. Eylem grubunu tamamlamak için **Tamam**'a tıklayın. 
11. Uyarı kuralını tamamlamak için **Uyarı kuralı oluştur**'a tıklayın. Hemen çalıştırılmaya başlar.<br><br> ![Yeni uyarı kuralı oluşturmayı tamamlama](./media/tutorial-response/alert-rule-01.png)<br> 

## <a name="view-your-alerts-in-azure-portal"></a>Uyarılarınızı Azure portalda görüntüleme
Bir uyarı oluşturduğunuza göre artık Azure uyarılarını tek bir bölmede görebilir ve Azure abonelikleriniz genelinde tüm uyarı kurallarınızı yönetebilirsiniz. Tüm uyarı kurallarını (etkin veya devre dışı) listeler ve hedef kaynaklarına, kaynak gruplarına, kural adına veya duruma göre sıralanabilir. Tetiklenen tüm uyarıların toplu bir özeti ve toplam yapılandırılmış/etkin uyarı kuralı sayısı da bulunur.<br><br> ![Azure Alerts durum sayfası](./media/tutorial-response/azure-alerts-02.png)  

Uyarı tetiklendiğinde tablo koşulu ve seçilen zaman aralığı içinde (varsayılan altı saattir) koşulun kaç kez oluştuğunu yansıtır.  Gelen kutunuzda, sorun çıkaran sanal makineyi ve burada arama sorgusuyla en iyi eşleşen sonuçları gösteren, aşağıdaki örneğe benzer bir e-posta olmalıdır.<br><br> ![Uyarı e-posta eylemi örneği](./media/tutorial-response/azure-alert-email-notification-01.png)

## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, uyarı kurallarının zamanlanan aralıklarda günlük aramaları çalıştırdığında ve belirli bir ölçütle eşleştiğinde proaktif şekilde bir sorunu nasıl belirleyebildiğini ve yanıtlayabildiğini öğrendiniz.

Önceden oluşturulmuş Log Analytics betik örneklerini görmek için bu bağlantıyı izleyin.  

> [!div class="nextstepaction"]
> [Log Analytics betik örnekleri](../../azure-monitor/platform/powershell-samples.md)
