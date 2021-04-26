---
title: Dışarı aktarma düzeninde Azure Data Box geri göndermeye yönelik öğretici | Microsoft Docs
description: Dışarı aktarma sırası tamamlandıktan sonra Azure Data Box Microsoft 'a nasıl teslim alabileceğinizi öğrenin
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 02/03/2021
ms.author: alkohli
ms.openlocfilehash: 6c2eed9aac614b0765582d5cf373caf50323ef7a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99537365"
---
# <a name="tutorial-return-azure-data-box"></a>Öğretici: dönüş Azure Data Box

Bu öğreticide, Azure Data Box nasıl geri dönebileceğinizi ve cihazın Azure verilerinde alındıktan sonra verilerin nasıl silinmeyeceği açıklanır.

Bu öğreticide şu gibi konular hakkında bilgi edineceksiniz:

> [!div class="checklist"]
>
> * Ön koşullar
> * Göndermeye hazırlama
> * Data Box'ı Microsoft'a gönderme
> * Data Box'tan verileri silme

## <a name="prerequisites"></a>Ön koşullar

Başlamadan önce şunlardan emin olun:

* [Öğreticiyi tamamladınız: Azure Data Box verileri kopyalama](data-box-deploy-export-copy-data.md).
* Kopyalama işleri tamamlandı. Kopyalama işleri devam ediyorsa Göndermeye hazırlama işi gerçekleştirilemez.

## <a name="prepare-to-ship"></a>Göndermeye hazırlama

[!INCLUDE [data-box-export-prepare-to-ship](../../includes/data-box-export-prepare-to-ship.md)]

Sonraki adımlar cihazın iade edildiği bölgeye göre belirlenir.

## <a name="ship-data-box-back"></a>Data Box'ı geri gönderme

Cihazdan veri kopyasının tamamlandığından ve **göndermeye hazırlama** çalıştırmasının başarılı olduğundan emin olun. Cihazı gönderdiğiniz bölgeye bağlı olarak yordam farklıdır.

## <a name="us-canada-europe"></a>[ABD, Kanada, Avrupa](#tab/in-us-canada-europe)

Cihazı ABD, Kanada veya Avrupa’da iade ediyorsanız aşağıdaki adımları uygulayın.

1. Cihazın kapalı olduğundan ve kabloların çıkartılmış olduğundan emin olun. 
2. Cihaz ile beraber sağlanan güç kablosunu sararak emniyetli şekilde cihazın arkasına yerleştirin.
3. Sevkiyat etiketinin E-mürekkep görüntüsüne görüntülendiğinden emin olun ve taşıyıcla bir toplama işlemi zamanlayın. Etiket hasar görmüşse veya kaybolursa ya da E-mürekkep ekranda görüntülenmiyorsa, Microsoft Desteği başvurun. Destek ' i öneriyorsa, genel bakış ' a giderek Azure portal **> sevkiyat etiketini yükleyebilirsiniz** . Sevkiyat etiketini indirin ve cihaza afıt yapıştırın. 
4. Cihazı iade ediyorsanız UPS ile bir toplama zamanı ayarlayın. Teslim alma planlamak için:

   - Yerel UPS şubesini (ülkeye/bölgeye özel ücretsiz hatlar) arayın.
   - Görüşme esnasında, E-ink ekranında veya basılı etiketinizde gördüğünüz iade izleme numarasını bildirin. İzleme numarasını belirtmezseniz, UPS teslimat sırasında ek bir ücret talep edecektir.
   - Teslim alma planlarken herhangi bir sorunla karşılaşırsanız veya ek ücret ödemeniz istenirse Azure Data Box İşlemleri ile iletişime geçin. [adbops@microsoft.com](mailto:adbops@microsoft.com) adresine e-posta gönderin.

    Teslim alma zamanlama yerine Data Box’ı en yakın teslim etme yerine bırakabilirsiniz.
4. Data Box nakliyeciniz tarafından toplandıktan ve tarandıktan sonra, portaldaki sipariş durumu **Toplandı** olarak güncelleştirilir. Ayrıca bir takip numarası da görüntülenir.

## <a name="australia"></a>[Avustralya](#tab/in-australia)

Avustralya’daki Azure veri merkezlerinde ek güvenlik bildirimi uygulanır. Gelen tüm sevkiyatların önceden bildirilmesi gerekir. Avustralya’da gönderme için aşağıdaki adımları uygulayın.

1. Cihazı iade etmek üzere göndermek için, kullanılan orijinal kutuyu saklayın.
2. Cihaza veri kopyalamanın tamamlandığından ve **göndermeye hazırlama** işleminin başarılı olduğundan emin olun.
3. Cihazı kapatın ve kabloları çıkarın.
4. Cihaz ile beraber sağlanan güç kablosunu sararak emniyetli şekilde cihazın arkasına yerleştirin.
5. [DHL Bağlantısında](https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference) çevrimiçi olarak bir teslim alma rezervasyonu yapın.

## <a name="japan"></a>[Japonya](#tab/in-japan)

1. Cihazı iade etmek üzere göndermek için, kullanılan orijinal kutuyu saklayın.
2. Cihazı kapatın ve kabloları çıkarın.
3. Cihaz ile beraber sağlanan güç kablosunu sararak emniyetli şekilde cihazın arkasına yerleştirin.
4. Sevk mektubuna, gönderici bilgisi olarak şirket adınızı ve adres bilgilerinizi yazın.
5. Aşağıdaki e-posta şablonunu kullanarak Quantium Solutions’a e-posta gönderin.

    * Japan Post Chakubarai sevk mektubu gönderilmediyse veya kaybolduysa, gönderdiğiniz e-postada bunu da bildirin. Quantium Solutions Japan, teslim alma esnasında Japan Post’un sevk mektubunu da getirmesini ister.
    * Birden fazla siparişiniz varsa her birinin ayrı olarak teslim alınması için e-posta gönderin.

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Azure Data Box｜Job name： 
    Body: 
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
    a. 08：00-13：00 
    b. 13：00-15：00 
    c. 15：00-17：00 
    d. 17：00-19：00 
    ```

6. Teslim alma rezervasyonu yaptıktan sonra Quantium Solutions’tan e-posta onay iletisi alırsınız. E-posta onay iletisi, Chakubarai sevk mektubuna yönelik bilgileri de içerir.

Gerekirse, aşağıdaki bilgilerden Quantium Solutions Desteği (Japonca) ile iletişime geçebilirsiniz: 

* E-posta: Customerservice.JP@quantiumsolutions.com
* Telefon：03-5755-0150

## <a name="singapore"></a>[Singapur](#tab/in-singapore)

1. Cihazı iade etmek üzere göndermek için, kullanılan orijinal kutuyu saklayın.
2. İzleme numarasını (Data Box yerel web kullanıcı arabiriminin Göndermeye Hazırlama sayfasında başvuru numarası olarak gösterilir) not alın. Bu numara, göndermeye hazırlama adımı başarıyla tamamlandıktan sonra oluşturulur. Bu sayfadaki gönderim etiketini indirin ve kutunun üzerine yapıştırın.
3. Cihazı kapatın ve kabloları çıkarın.
4. Cihaz ile beraber sağlanan güç kablosunu sararak emniyetli şekilde cihazın arkasına yerleştirin. 
5. Aşağıdaki e-posta şablonuna takip numarasını ekleyin ve SingPost Müşteri Hizmetleri'ne gönderin.

    ```
    To: kadcustcare@singpost.com
    Subject: Microsoft Azure Pickup - OrderName 
    Body: 
        1. Requestor name  
        2. Requestor contact number
        3. Requestor collection address
        4. Preferred collection date
    ```

   > [!NOTE]
   > İş gününde alınan rezervasyon istekleri için:
   >
   > * 15:00’ten önce, teslim alma sonraki iş gününde 09:00 ile 13:00 arasında gerçekleştirilir.
   > * 15:00’ten sonra, teslim alma sonraki iş gününde 14:00 ile 18:00 arasında gerçekleştirilir.  

## <a name="south-africa"></a>[Güney Afrika](#tab/in-sa)

1. İade sevk irsaliyesi için cihazı paketetmek üzere kullanılan orijinal kutuyu koruyun.
2. Cihazın yerel Web Kullanıcı arabiriminde gösterilen başvuru numarasını (Waybill Number) göz önünde kalın. Bu sayı, **göndermeye hazırlama çalıştırması** başarılı olduktan sonra görüntülenir.
3. Cihazın yerel Web Kullanıcı arabiriminde bulunan ve sevkiyat paketine yapıştırılmış sevkiyat etiketini indirin ve yazdırın.
4. DHL ile bir toplama kitabı sağlamak için aşağıdaki seçeneklerden birini seçin:

    * Müşteri hizmeti iletişim merkezini, **+ 27 (0) 11 9213600**' de 2:00 PM 'den önce çağırın, 1. seçeneği belirleyin ve ardından Waybill numarasını belirtin.
    * Aşağıdaki şablonu kullanarak [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) adresine e-posta gönderin:

    ```output
    To: Priority.Support@dhl.com
    Subject: Pickup request for Microsoft Azure
    Body: Need pick up for the below shipment
      *  DHL tracking number (reference number/waybill number)
      *  Requested pickup date: yyyy/mm/dd;time:HH MM
    ```

    * Bunun yerine, paketi en yakın DHL hizmet noktasında bırakabilirsiniz.

5. Herhangi bir sorunla karşılaşırsanız, karşılaştığınız sorunların ayrıntıları ile e-posta gönderin [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) ve Waybill numarasını Konu: satırına koyun. +27(0)119213902 numaralı telefonu da arayabilirsiniz.

## <a name="hong-kong"></a>[Hong Kong](#tab/in-hk)

1. İade gönderimi için cihazı orijinal kutusunda paketleyin.
2. Cihazın yerel Web Kullanıcı arabiriminde gösterilen başvuru numarasını (ters sevkiyat için izleme numarası) göz önünde bulunmak. Bu sayı, **göndermeye hazırlama çalıştırması** başarılı olduktan sonra görüntülenir.
3. Cihazın yerel Web Kullanıcı arabiriminde bulunan ve sevkiyat paketine yapıştırılmış sevkiyat etiketini indirin ve yazdırın.
4. Cihaz ile beraber sağlanan güç kablosunu sararak emniyetli şekilde cihazın arkasına yerleştirin.
5. Çalışma saatleri içinde (Pazartesi - Cuma saat 09:00 ile 18:00 arası) **(852) 2318 1213** numaralı telefondan **Quantium Solutions** bilgi hattını arayın.  
6. Teslim almayı planlamak için iade gönderimi etiketinde Microsoft Azure teslim alma ifadesini, referans numarasını ve takip numarasını (barkodun üzerinde) belirtin.
7. Teslim alma zamanlaması için sözlü teyit alırsınız. Courier koleksiyona ulaşmazsa, alternatif düzenlemeler için Quantium çözüm hattını çağırın.
8. Quantium'la teslim alma rezervasyonunu yaptıktan sonra, aşağıdaki şablonu kullanarak onayı [Microsoft Data Box Operations Asia](mailto:adbo@microsoft.com) ile paylaşın:

    ```output
    To: adbo@microsoft.com
    Subject: Microsoft Data Box Job: [order name] has completed copy
    Body:
    We have confirmed the pickup details with Quantium.

       * Requestor name:
       * Requestor contact number:
       * Pickup Date:  
       * Pickup time:
    ```

[adbo@microsoft.com](mailto:adbo@microsoft.com)Konu başlığına ve karşılaşılan sorunla ilgili olarak iş adını sağlayan tüm sorunlar, e-posta Data Box Işlem Asya işlemleri arasında gelmeniz gerekir.

## <a name="united-arab-emirates"></a>[Birleşik Arap Emirlikleri](#tab/in-uae)

1. Cihazı iade etmek üzere göndermek için, kullanılan orijinal kutuyu saklayın.
2. Cihaza veri kopyalamanın tamamlandığından ve **göndermeye hazırlama** adımının başarıyla tamamlandığından emin olun.
3. Cihaz yerel Web Kullanıcı arabiriminin **göndermeye hazırlama** sayfasındaki başvuru numarasını dikkate alın.
4. Cihazın gücünü kapatın ve kabloları kaldırın. Cihaz ile beraber sağlanan güç kablosunu sararak emniyetli şekilde cihazın arkasına yerleştirin.
6. İade gönderimi için cihazı orijinal kutusunda paketleyin.
7. Veri merkezinde geri geldiğinde paketi tanımlamak için kullanılacak bir KIMLIK almak için e-posta [Azure Data Box işlemleri](mailto:adbops@microsoft.com) .
8. Bu KIMLIĞI, açık adresin yanına doğru görünür olacak şekilde, yazdırılmış sevkiyat etiketi üzerine yazın.  
9. [DHL Express UAE](https://mydhl.express.dhl/ae/en/home.html#/schedulePickupTab)zamanlamaya göre bir çekme planı çevrimiçi olarak kitap oluşturma  >  .
   - Waybill Number alanındaki cihaz yerel Web Kullanıcı arabiriminin **göndermeye hazırlama** sayfasından başvuru numarasını girin.
   - Kayıtlar, 9:00:30 ' dan (2:00 yani, FIN ve genel Tatiller hariç) 6 ' dan sonra kabul edilir.
   - Çekme istekleri, müşterinin kapanış zamanından en az 90 dakika önce yerleştirilmelidir.
10. DHL kayıt aracı ile ilgili herhangi bir sorun yaşıyorsanız, aşağıdaki yöntemlerden herhangi birini kullanarak DHL 'ye başvurabilirsiniz:
    - Çağrı 04-2924545.
    - Sorun ayrıntıları ile e-posta gönderin [ecom.ae@dhl.com](mailto:ecom.ae@dhl.com) ve Waybill numarasını Konu: satırına koyun.
    - 600 567567 adresinden DHL müşteri desteğini çağırın.

## <a name="self-managed"></a>[Kullanıcı Yönetimli](#tab/in-selfmanaged)

Japonya, Singapur, Kore, Hindistan, Güney Afrika, Birleşik Krallık, Batı Avrupa veya Avustralya 'da Data Box kullanıyorsanız ve sipariş oluşturma sırasında kendi kendine yönetilen sevkiyat seçeneğini belirlediyseniz, bu yönergeleri izleyin.

1. Bu adım başarıyla tamamlandıktan sonra Data Box yerel web kullanıcı arabiriminin Göndermeye Hazırlama sayfasında gösterilen Yetkilendirme kodunu not alın.
2. Cihazı kapatın ve kabloları çıkarın. Cihaz ile beraber sağlanan güç kablosunu sararak emniyetli şekilde cihazın arkasına yerleştirin.
3. Cihazı döndürmeye hazırsanız aşağıdaki şablonu kullanarak Azure Data Box Işlemler ekibine bir e-posta gönderin.
    
    ```
    To: adbops@microsoft.com 
    Subject: Request for Azure Data Box drop-off for order: ‘orderName’ 
    Body: 
        1. Order name  
        2. Authorization code available after Prepare to Ship has completed [Yes/No]  
        3. Contact name of the person dropping off. You will need to display a Government approved ID during the drop off.
    ```

---

## <a name="erasure-of-data-from-data-box"></a>Data Box'tan verileri silme

Cihaz Azure veri merkezine ulaştığında, Data Box [NıST SP 800-88 düzeltme 1 yönergelerine](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)göre disketlerinin verilerini siler.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, aşağıdaki gibi konular hakkında bilgi edindiniz:

> [!div class="checklist"]
> * Önkoşullar
> * Göndermeye hazırlama
> * Data Box'ı Microsoft'a gönderme
> * Data Box'tan verileri silme

Data Box yönetme hakkında bilgi edinmek için sonraki makaleye ilerleyin.

> [!div class="nextstepaction"]
> [Azure portal aracılığıyla Data Box yönetme](./data-box-portal-admin.md)
