---
title: Cihaz bilgilerini içeri aktarma
description: IoT sensörleri için Defender yansıtılmış trafiği izler ve analiz eder. Bu gibi durumlarda, önceden algılanan cihazlarda verileri zenginleştirme konusunda bilgi almak isteyebilirsiniz.
ms.date: 12/06/2020
ms.topic: how-to
ms.openlocfilehash: eb82a3e5d360913acfead06adc03194d80f7fccb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104785639"
---
# <a name="import-device-information-to-a-sensor"></a>Cihaz bilgilerini bir sensöre aktar

IoT algılayıcısı için bir Azure Defender, yansıtılmış trafiği izler ve analiz eder. Bazı durumlarda, kuruluşa özgü ağ yapılandırma ilkeleri nedeniyle bazı bilgiler aktarılmayabilir.

Bu gibi durumlarda, önceden algılanan cihazlarda verileri zenginleştirme konusunda bilgi almak isteyebilirsiniz. Algılayıcıların bilgilerini içeri aktarmaya yönelik iki seçenek mevcuttur:

- **Haritada Içeri aktar**: cihaza cihaz adını, türü, grubu veya Purdue katmanını güncelleştirin.

- **İçeri aktarma ayarlarından Içeri aktar**: cihaz işletim SISTEMINI, IP adresini, yama düzeyini veya yetkilendirme durumunu içeri aktarın.

## <a name="import-from-the-map"></a>Eşlemden içeri aktar

Bu bölümde aygıt adları, türleri, grupları ya da cihaz haritasına yönelik Purtıon katmanlarının nasıl içeri aktarılacağı açıklanmaktadır. Bunu haritadan yapabilirsiniz.

İçeri aktarma gereksinimleri şunlardır:

- **Adlar**: en fazla 30 karakter olabilir.

- **Tür** veya **takip eden katman**: **cihaz özellikleri** iletişim kutusunda görünen seçenekleri kullanın. (Cihaza sağ tıklayın ve **özellikleri görüntüle**' yi seçin.)

- **Cihaz grubu**: en fazla 30 karakterden oluşan yeni bir grup oluşturun. 

> [!NOTE]
> Çakışmaları önlemek için, bir sensörden başka bir sensöre dışarı aktardığınız verileri içeri aktarmayın.

İçeri aktarmak için:

1. Yan menüde **cihazlar**' ı seçin.

2. **Cihazlar** penceresinin sağ üst köşesinde öğesini seçin :::image type="icon" source="media/how-to-import-device-information/file-icon.png" border="false"::: .

   :::image type="content" source="media/how-to-import-device-information/device-window-v2.png" alt-text="Cihaz penceresinin ekran görüntüsü.":::

3. **Cihazları dışarı aktar**' ı seçin. İçe aktarılmış dosyada çok sayıda bilgi görüntülenir. Bu bilgiler, cihazın kullandığı protokolleri ve cihaz yetkilendirme durumunu içerir.

   :::image type="content" source="media/how-to-import-device-information/sample-exported-file.png" alt-text="İçe aktarılmış dosyadaki bilgiler.":::

4. CSV dosyasında yalnızca cihaz adını, türü, grubu ve takip tarihi katmanını değiştirin. Ardından dosyayı kaydedin. 

   Verilen dosyada gösterilen büyük küçük harf standartlarını kullanın. Örneğin, takip eden katman için ilk harfi büyük harfle kullanın.

5. **Cihaz** penceresindeki **Içeri/dışarı aktar** açılan menüsünden **cihazları içeri aktar**' ı seçin.

   :::image type="content" source="media/how-to-import-device-information/import-assets-v2.png" alt-text="Cihazları cihaz penceresi aracılığıyla içeri aktarın.":::

6. **Cihazları Içeri aktar** ' ı seçin ve içeri aktarmak istediğiniz CSV dosyasını seçin. İçeri aktarma durumu iletileri, **cihazları Içeri aktar** iletişim kutusu kapatılana kadar ekranda görüntülenir.

## <a name="import-from-import-settings"></a>İçeri aktarma ayarlarından içeri aktar

Bu bölümde cihaz IP adresi, işletim sistemi, düzeltme eki düzeyi veya yetkilendirme durumunun cihaz haritasına nasıl içeri aktarılacağı açıklanmaktadır. Bunu **Içeri aktarma ayarları** iletişim kutusundan yapabilirsiniz.

IP adresi, işletim sistemi ve düzeltme eki düzeyini içeri aktarmak için:

1. [Yardım Merkezi](https://cyberx-labs.zendesk.com/hc/en-us) 'nden [devices_info_2.2.8 ve up.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) dosyasını indirin ve bilgileri aşağıdaki gibi girin:

   - **IP adresi**: Cihaz IP adresini girin.

   - **Işletim sistemi**: açılan listeden seçim yapın.

   - **Son güncelleştirme**: yyyy-aa-gg biçimini kullanın.

     :::image type="content" source="media/how-to-import-device-information/last-update-screen.png" alt-text="Seçenekler ekranı.":::

2. Yan menüde **Içeri aktarma ayarları**' nı seçin.

   :::image type="content" source="media/how-to-import-device-information/import-settings-screen-v2.png" alt-text="Ayarlarınızı içeri aktarın.":::

3. Gerekli yapılandırmayı karşıya yüklemek için, **cihaz bilgileri** bölümünde, hazırladığınız CSV dosyasını **Ekle** ve karşıya yükle ' yi seçin.

Yetkilendirme durumunu içeri aktarmak için:

1. IoT yardım merkezi için Defender 'dan [authorized_devices.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) dosyasını indirip kaydedin. Dosyayı bir CSV olarak kaydettiğinizden emin olun.

2. Bilgileri şu şekilde girin:

   - **IP adresi**: cihazın IP adresi.

   - **Ad**: yetkili cihaz adı. Adların doğru olduğundan emin olun. İçeri aktarılan listedeki cihazlara verilen adlara, cihaz eşlemesinde gösterilen adların üzerine yazılır.

     :::image type="content" source="media/how-to-import-device-information/device-map-file.png" alt-text="İçeri aktarılan cihaz listesine sahip Excel dosyaları.":::

3. Yan menüde **Içeri aktarma ayarları**' nı seçin.

4. **Yetkili cihazlar** bölümünde, kaydettiğiniz CSV dosyasını **Ekle** ve karşıya yükle ' yi seçin.

Bilgiler içeri aktarıldığında, bu listede görünmeyen tüm cihazların yetkisiz cihazları hakkında uyarılar alırsınız.

## <a name="import-device-information-to-the-sensor"></a>Cihaz bilgilerini sensöre aktar

Algılayıcı, yansıtılmış trafiği izler ve analiz eder. Bazı durumlarda, kuruluşa özgü ağ yapılandırma ilkeleri nedeniyle bazı bilgiler aktarılmayabilir.

Bu durumlarda, zaten algılanan cihazlarda verileri zenginleştiren cihaz bilgilerine aktarmak isteyebilirsiniz. Algılayıcıların bilgilerini içeri aktarmaya yönelik iki seçenek mevcuttur:

- **Haritada Içeri aktar**: cihaza cihaz adını, türü, grubu veya Purdue katmanını güncelleştirin.

- **İçeri aktarma ayarlarından Içeri aktar**: cihaz işletim SISTEMINI, IP adresini, yama düzeyini veya yetkilendirme durumunu içeri aktarın.

### <a name="import-from-the-map"></a>Eşlemden içeri aktar

Bu bölümde aygıt adları, türleri, grupları ya da cihaz haritasına yönelik Purtıon katmanlarının nasıl içeri aktarılacağı açıklanmaktadır. Bunu haritadan yapabilirsiniz.

İçeri aktarma gereksinimleri şunlardır:

- **Adlar**: en fazla 30 karakter olabilir.

- **Tür** veya **takip eden katman**: **cihaz özellikleri** iletişim kutusunda görünen seçenekleri kullanın. (Cihaza sağ tıklayın ve **özellikleri görüntüle**' yi seçin.)

- **Cihaz grubu**: en fazla 30 karakterden oluşan yeni bir grup oluşturun. 

> [!NOTE]
> Çakışmaları önlemek için, bir sensörden başka bir sensöre dışarı aktardığınız verileri içeri aktarmayın.

İçeri aktarmak için:

1. Yan menüde **cihazlar**' ı seçin.

2. **Cihazlar** penceresinin sağ üst köşesinde öğesini seçin :::image type="icon" source="media/how-to-import-device-information/file-icon.png" border="false"::: .

   :::image type="content" source="media/how-to-import-device-information/device-window-v2.png" alt-text="Cihazınızı içinden seçebileceğiniz pencere.":::

3. **Cihazları dışarı aktar**' ı seçin. İçe aktarılmış dosyada çok sayıda bilgi görüntülenir. Örnekler, cihazın kullandığı protokolleri ve cihaz yetkilendirme durumunu içerir.

   :::image type="content" source="media/how-to-import-device-information/sample-exported-file.png" alt-text="İçe aktarılmış dosyadaki bilgiler.":::

4. CSV dosyasında yalnızca cihaz adını, türü, grubu ve takip tarihi katmanını değiştirin. Ardından dosyayı kaydedin. 

   Verilen dosyada gösterilen büyük küçük harf standartlarını kullanın. Örneğin, takip eden katman için ilk harfi büyük harfle kullanın.

5. **Cihaz** penceresindeki **Içeri/dışarı aktar** açılan menüsünden **cihazları içeri aktar**' ı seçin.

   :::image type="content" source="media/how-to-import-device-information/import-assets-v2.png" alt-text="Cihazlarınızı içeri aktarın.":::

6. **Cihazları Içeri aktar** ' ı seçin ve içeri aktarmak istediğiniz CSV dosyasını seçin. İçeri aktarma durumu iletileri, **cihazları Içeri aktar** iletişim kutusu kapatılana kadar ekranda görüntülenir.

### <a name="import-from-import-settings"></a>İçeri aktarma ayarlarından içeri aktar 

Bu bölümde cihaz IP adresi, işletim sistemi, düzeltme eki düzeyi veya yetkilendirme durumunun cihaz haritasına nasıl içeri aktarılacağı açıklanmaktadır. Bunu **Içeri aktarma ayarları** iletişim kutusundan yapabilirsiniz.

IP adresi, işletim sistemi ve düzeltme eki düzeyini içeri aktarmak için:

1. [Yardım Merkezi](https://cyberx-labs.zendesk.com/hc/en-us) 'nden [devices_info_2.2.8 ve up.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) dosyasını indirin ve bilgileri aşağıdaki gibi girin:

   - **IP adresi**: cihazın IP adresi.

   - **Işletim sistemi**: açılan listeden seçim yapın.

   - **Son güncelleştirme tarihi**: yyyy-aa-gg biçimini kullanın.

    :::image type="content" source="media/how-to-import-device-information/last-update-screen.png" alt-text="Ekrandaki içerik.":::

2. Yan menüde **Içeri aktarma ayarları**' nı seçin.

   :::image type="content" source="media/how-to-import-device-information/import-settings-screen-v2.png" alt-text="Ayarları içeri aktarma ekranını doldurun.":::

3. Gerekli yapılandırmayı karşıya yüklemek için, **cihaz bilgileri** bölümünde, hazırladığınız CSV dosyasını **Ekle** ve karşıya yükle ' yi seçin.

Yetkilendirme durumunu içeri aktarmak için:

1. IoT yardım merkezi için Defender 'dan [authorized_devices-examples.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) dosyasını indirip kaydedin. Dosyayı bir CSV olarak kaydettiğinizden emin olun.

2. Bilgileri şu şekilde girin:

   - **IP adresi**: cihazın IP adresi.

   - **Ad**: yetkili cihaz adı. Adların doğru olduğunu doğrulayın. İçeri aktarılan listedeki cihazlara verilen adlara, cihaz eşlemesinde gösterilen adların üzerine yazılır.

     :::image type="content" source="media/how-to-import-device-information/device-map-file.png" alt-text="Cihaz eşlemesine içeri aktarma listesi.":::

3. Yan menüde **Içeri aktarma ayarları**' nı seçin.

4. **Yetkili cihazlar** bölümünde, kaydettiğiniz CSV dosyasını **Ekle** ve karşıya yükle ' yi seçin.

Bilgiler içeri aktarıldığında, bu listede görünmeyen tüm cihazların yetkisiz cihazları hakkında uyarılar alırsınız.

## <a name="see-also"></a>Ayrıca bkz.

[Hangi trafiğin izleneceğini denetleme](how-to-control-what-traffic-is-monitored.md)

[Bir cihaz envanterinde algılayıcı algılamalarını araştırma](how-to-investigate-sensor-detections-in-a-device-inventory.md)
