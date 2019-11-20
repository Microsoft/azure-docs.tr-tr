---
title: Azure CDN gerçek zamanlı uyarılar | Microsoft Docs
description: Microsoft Azure cdn'de gerçek zamanlı uyarılar. Gerçek zamanlı uyarılar, performansı CDN profilinizde uç noktaları ile ilgili bildirimler sağlar.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 1e85b809-e1a9-4473-b835-69d1b4ed3393
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 4b8cbc27757cf6c321ea4b3c27720a129aa27c1b
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593471"
---
# <a name="real-time-alerts-in-microsoft-azure-cdn"></a>Microsoft Azure cdn'de gerçek zamanlı uyarılar
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Genel Bakış
Bu belgede, Microsoft Azure cdn'de gerçek zamanlı uyarılar açıklanmaktadır. Bu işlev, performansı CDN profilinizde uç noktaları ile ilgili gerçek zamanlı bildirimler sağlar.  E-posta veya HTTP göre uyarılar ayarlayabilirsiniz:

* Bant genişliği
* Durum kodları
* Önbellek durumları
* Bağlantılar

## <a name="creating-a-real-time-alert"></a>Gerçek zamanlı uyarı oluşturuluyor
1. İçinde [Azure portalında](https://portal.azure.com), CDN profilinize gidin.
   
    ![CDN profili](./media/cdn-real-time-alerts/cdn-profile-blade.png)
1. CDN profili dikey penceresinden tıklayın **Yönet** düğmesi.
   
    ![CDN profili Yönet düğmesi](./media/cdn-real-time-alerts/cdn-manage-btn.png)
   
    CDN yönetim portalına açılır.
3. Üzerine **Analytics** sekmesine ve ardından üzerine **gerçek zamanlı istatistikleri** açılır öğesi.  Tıklayarak **gerçek zamanlı uyarılar**.
   
    ![CDN yönetim portalına](./media/cdn-real-time-alerts/cdn-premium-portal.png)
   
    Mevcut uyarı yapılandırmalarını (varsa) listesi görüntülenir.
4. Tıklayın **ekleme uyarı** düğmesi.
   
    ![Uyarı düğmesi ekleme](./media/cdn-real-time-alerts/cdn-add-alert.png)
   
    Yeni bir uyarı oluşturmak için bir form görüntülenir.
   
    ![Yeni uyarı formu](./media/cdn-real-time-alerts/cdn-new-alert.png)
5. Bu uyarı etkin olmasını istiyorsanız tıkladığınızda **Kaydet**, kontrol **etkin uyarı** onay kutusu.
6. İçinde bir uyarı için açıklayıcı bir ad girin **adı** alan.
7. İçinde **medya türü** açılır menüsünde, select **HTTP büyük nesne**.
   
    ![Seçili HTTP büyük nesne medya türüyle](./media/cdn-real-time-alerts/cdn-http-large.png)
   
   > [!IMPORTANT]
   > Seçmelisiniz **HTTP büyük nesne** olarak **medya türü**.  Diğer seçeneklerden tarafından kullanılmaz **verizon'dan Azure CDN**.  Seçilecek hatası **HTTP büyük nesne** hiçbir zaman tetiklenmesi uyarıyı neden olur.
   > 
   > 
8. Oluşturma bir **ifade** seçerek izlemek için bir **ölçüm**, **işleci**, ve **tetikleyen değer**.
   
   * İçin **ölçüm**, izlenen istediğiniz koşul türünü seçin.  **Bant genişliği MB/sn** saniye başına megabit cinsinden bant genişliği kullanım miktarı.  **Toplam Bağlantı** edge sunucularımızı eşzamanlı HTTP bağlantı noktasının numarasıdır.  Çeşitli önbellek durumları ve durum kodları tanımları için bkz: [Azure CDN önbelleğe durum kodları](/previous-versions/azure/mt759237(v=azure.100)) ve [Azure CDN HTTP durum kodları](/previous-versions/azure/mt759238(v=azure.100))
   * **İşleç** ölçüm tetikleyicisi değeri arasındaki bir ilişki kurar matematik işlecidir.
   * **Tetikleyen değer** bildirim gönderilmeden önce karşılanması gereken eşik değeri şudur:.
     
     Aşağıdaki örnekte, 404 durum kodları sayısı 25 ' büyük olduğunda bir bildirim gönderilir oluşturulan ifadeyi belirtir.
     
     ![Gerçek zamanlı uyarı örnek ifade](./media/cdn-real-time-alerts/cdn-expression.png)
9. İçin **aralığı**, değerlendirilen ifade ne sıklıkta istediğiniz girin.
10. İçinde **üzerinde bildirim** açılır, ifade true olduğunda bildirim almak istediğiniz zaman seçin.
    
    * **Koşul başlangıç** belirten bir koşul algılandığında bir bildirim gönderilir.
    * **Koşul son** belirtilen koşulun artık algılandığında bildirim gönderileceğini gösterir. Bu bildirim, yalnızca belirtilen koşulu oluştuğunu sistem izleme ağımız saptadıktan sonra tetiklenebilir.
    * **Sürekli** bildirim her zaman ağ sistem izleme belirtilen koşulu algılar gönderileceğini gösterir. Ağ izleme sistemi belirli koşula aralığı başına yalnızca bir kez denetler aklınızda bulundurun.
    * **Koşul başlangıç ve bitiş** belirten bir bildirim ilk kez belirtilen koşulu algılanır ve bir kez daha, koşul artık algılandığında gönderilir.
1. E-posta ile bildirim almak istiyorsanız işaretleyin **e-postayla bildirim** onay kutusu.  
    
    ![E-posta formu tarafından bildir](./media/cdn-real-time-alerts/cdn-notify-email.png)
    
    İçinde **için** bildirimleri istediğiniz gönderdiğiniz e-posta adresi girin. İçin **konu** ve **gövdesi**varsayılan bırakabilir veya kullanarak iletiyi özelleştirebilir **kullanılabilir anahtar sözcükler** uyarı verileri dinamik olarak eklemek için liste olduğunda ileti gönderilir.
    
    > [!NOTE]
    > E-posta bildirimi tıklayarak sınayabilirsiniz **Test bildirim** uyarı yapılandırması kaydedildikten sonra ancak yalnızca düğme.
    > 
    > 
12. Bir web sunucusuna gönderilecek bildirimler istiyorsanız işaretleyin **HTTP Post ile bildirim** onay kutusu.
    
    ![HTTP Post formunda bildir](./media/cdn-real-time-alerts/cdn-notify-http.png)
    
    İçinde **Url** , HTTP iletisi istediğiniz gönderilen URL'yi girin. İçinde **üstbilgileri** metin isteğinde gönderilecek HTTP üst bilgilerini girin.  İçin **gövdesi**, kullanılarak ileti özelleştirebilir **kullanılabilir anahtar sözcükler** ileti gönderildiğinde uyarı verileri dinamik olarak eklemek için liste.  **Üstbilgileri** ve **gövdesi** varsayılan olarak aşağıdaki örneğe benzer bir XML yükünü:
    
    ```
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">
        <![CDATA[Expression=Status Code : 404 per second > 25&Metric=Status Code : 404 per second&CurrentValue=[CurrentValue]&NotificationCondition=Condition Start]]>
    </string>
    ```
    
    > [!NOTE]
    > HTTP Post bildirime tıklayarak sınayabilirsiniz **Test bildirim** uyarı yapılandırması kaydedildikten sonra ancak yalnızca düğme.
    > 
    > 
13. Tıklayın **Kaydet** uyarı yapılandırmanızı kaydetmek için düğme.  İşaretlediyseniz **etkin uyarı** 5. adımda Uyarınız artık etkindir.

## <a name="next-steps"></a>Sonraki Adımlar
* Analiz [Azure cdn'de gerçek zamanlı İstatistikler](cdn-real-time-stats.md)
* İle daha derine inin [Gelişmiş HTTP raporları](cdn-advanced-http-reports.md)
* Analiz [kullanım desenleri](cdn-analyze-usage-patterns.md)

