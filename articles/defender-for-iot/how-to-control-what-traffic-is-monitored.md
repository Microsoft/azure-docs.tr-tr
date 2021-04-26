---
title: Hangi trafiğin izleneceğini denetleme
description: Sensörler BT ve trafik için derin paket algılamayı otomatik olarak gerçekleştirir ve cihaz öznitelikleri ve ağ davranışı gibi ağ cihazlarıyla ilgili bilgileri çözer. Her algılayıcının algıladığı trafik türünü denetlemek için çeşitli araçlar mevcuttur.
ms.date: 12/07/2020
ms.topic: how-to
ms.openlocfilehash: b80ef4a4a1fca7383da80fda6fdf42831913f2bd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784450"
---
# <a name="control-what-traffic-is-monitored"></a>Hangi trafiğin izleneceğini denetleme

Sensörler BT ve trafik için derin paket algılamayı otomatik olarak gerçekleştirir ve cihaz öznitelikleri ve davranış gibi ağ cihazlarıyla ilgili bilgileri çözer. Her algılayıcının algıladığı trafik türünü denetlemek için çeşitli araçlar mevcuttur.

## <a name="learning-and-smart-it-learning-modes"></a>Öğrenme ve akıllı BT öğrenme modları

Öğrenme modu, sensörize ağınızın olağan etkinliklerini öğrenmesini sağlar. Ağınızda bulunan aygıtlar, ağda algılanan protokollerdir, belirli cihazlar arasında dosya aktarımları ve daha fazlasını yapar. Bu etkinlik, ağ temelinizi olur.

Öğrenme modu, yüklemeden sonra otomatik olarak etkinleştirilir ve devre dışı bırakılıncaya kadar etkin olmaya devam eder. Yaklaşık öğrenme modu süresi, ağ boyutuna ve karmaşıklığına bağlı olarak iki ila altı hafta arasındadır. Bu dönemden sonra, öğrenme modu devre dışı bırakıldığında, algılanan tüm yeni etkinlikler uyarı tetikleyecektir. İlke altyapısı, öğrenilen taban çizgisinden sapmaları bulduğunda uyarılar tetiklenir.

Öğrenme süresi tamamlandıktan ve öğrenimi modu devre dışı bırakıldıktan sonra, algılayıcı, DNS ve HTTP istekleri gibi normal BT etkinliğinin sonucu olan olağandışı yüksek düzeyde temel bir değişikliği algılayabilir. Etkinlik belirleyici olmayan BT davranışı olarak adlandırılır. Davranış, gereksiz ilke ihlali uyarılarını ve sistem bildirimlerini de tetikleyebilir. Bu uyarıların ve bildirimlerin miktarını azaltmak için **Smart It Learning** işlevini etkinleştirebilirsiniz.

Akıllı BT öğrenimi etkinleştirildiğinde, algılayıcı, belirli uyarı senaryolarına göre belirleyici olmayan BT davranışı üreten ağ trafiğini izler.

Algılayıcı bu trafiği yedi gün boyunca izler. Yedi gün içinde, belirleyici olmayan BT trafiği algılarsa, trafiği başka yedi gün içinde izlemeye devam eder. Trafik tam yedi gün boyunca algılanmadığında, bu senaryo için akıllı BT öğrenimi devre dışıdır. Bu senaryo için algılanan yeni trafik, yalnızca uyarılar ve bildirimler oluşturur.

Akıllı BT öğrenimi ile çalışma, gürültülü BT senaryolarından kaynaklanan gereksiz uyarıların ve bildirimlerin sayısını azaltmanıza yardımcı olur.

Sensör şirket içi Yönetim Konsolu tarafından denetleniyorsa öğrenme modlarını devre dışı bırakabilirsiniz. Bu gibi durumlarda, öğrenme modu yalnızca yönetim konsolundan devre dışı bırakılabilir.

Öğrenme özellikleri (öğrenme ve akıllı BT öğrenimi) varsayılan olarak etkindir.

Öğrenimi etkinleştirmek veya devre dışı bırakmak için:

- **Sistem ayarları** ' nı seçin ve **öğrenimi** ve **akıllı BT öğrenimi** seçeneklerini değiştirin.

:::image type="content" source="media/concept-learning-modes/toggle-options-for-learning-and-smart-it-learning.png" alt-text="Sistem ayarları geçiş ekranı.":::

## <a name="configure-subnets"></a>Alt ağları yapılandırma

Alt ağ konfigürasyonları cihaz eşlemesindeki cihazları nasıl göreceğinizi etkiler.

Varsayılan olarak, algılayıcı alt ağ kurulumunuzu bulur ve **alt ağ yapılandırması** iletişim kutusunu bu bilgilerle doldurur.

OT cihazlarda odaklanmayı etkinleştirmek için, BT cihazları otomatik olarak cihaz eşlemesindeki alt ağ tarafından toplanır. Her alt ağ, bir BT alt ağına ve geriye "detaya gitme" özelliği için etkileşimli daraltma/genişletme özelliği de dahil olmak üzere haritada tek bir varlık olarak sunulur.

Alt ağlarla çalışırken, OT alt ağlarını tanımlayacak olan ICS alt ağlarını seçin. Daha sonra harita görünümünü OT ve ICS ağlarında odahale getirebilirsiniz ve en düşük BT ağı öğelerinin sunumuna daraltabilirsiniz. Bu çaba, Haritada gösterilen cihazların toplam sayısını azaltır ve OT ve ICS ağ öğelerinin net bir resmini sağlar.

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/expand-network-option.png" alt-text="Bir ağa filtrelemeyi gösteren ekran görüntüsü.":::

Bulunan verileri dışarı aktarıp el ile değiştirerek ve ardından el ile tanımladığınız alt ağların listesini geri alarak, yapılandırmayı değiştirebilir veya alt ağ bilgilerini el ile değiştirebilirsiniz. Dışarı ve içeri aktarma hakkında daha fazla bilgi için bkz. [cihaz bilgilerini Içeri aktarma](how-to-import-device-information.md).

Ortak aralıkları iç aralıklar olarak kullanan ortamlar gibi bazı durumlarda, **Internet etkinliğini algılama** seçeneğini belirleyerek sensöre tüm alt ağları iç alt ağ olarak çözmelerini söyleyebilirsiniz. Bu seçeneği belirlediğinizde:

- Genel IP adresleri yerel adres olarak kabul edilir.

- Yetkisiz internet etkinliği hakkında, dış adreslerde alınan bildirimleri ve uyarıları azaltan bir uyarı gönderilmez.

Alt ağları yapılandırmak için:

1. Yan menüde **sistem ayarları**' nı seçin.

2. **Sistem ayarı** penceresinde **alt ağlar**' ı seçin.

   :::image type="content" source="media/how-to-control-what-traffic-is-monitored/edit-subnets-configuration-screen.png" alt-text="Alt ağ yapılandırma ekranını gösteren ekran görüntüsü."::: 

3. Yeni cihazlar bulunduğunda alt ağları otomatik olarak eklemek için **Otomatik alt ağların öğrenimini** izleyin.

4. Tüm alt ağları iç alt ağ olarak çözümlemek için **Internet etkinliğini Algımayın**' i seçin.

5. **Ağ ekle** ' yi seçin ve her alt ağ için aşağıdaki parametreleri tanımlayın:

    - Alt ağ IP adresi.
    - Alt ağ maskesi adresi.
    - Alt ağ adı. Her bir alt ağa kolayca tanımlayabilmeniz için anlamlı bir ad yazmanız önerilir, böylece BT ve OT ağları arasında ayrım yapabilirsiniz. Ad en fazla 60 karakter olabilir.

6. Bu alt ağı bir OT alt ağı olarak işaretlemek için, **ICS alt ağını** seçin.

7. Haritayı süre düzeyine göre düzenleme sırasında alt ağı ayrı olarak sunmak için, **ayrılmış**' i seçin.

8. Bir alt ağı silmek için öğesini seçin :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/delete-icon.png" border="false"::: .

9. Tüm alt ağları silmek için **Tümünü Temizle**' yi seçin.

10. Yapılandırılmış alt ağları dışarı aktarmak için **dışarı aktar**' ı seçin. Alt ağ tablosu iş istasyonunuza indirilir.

11. **Kaydet**’i seçin.

### <a name="importing-information"></a>Bilgiler içeri aktarılıyor 

Alt ağ bilgilerini içeri aktarmak için **Içeri aktar** ' ı seçin ve IÇERI aktarılacak CSV dosyasını seçin. Alt ağ bilgileri, içeri aktardığınız bilgilerle güncelleştirilir. Boş bir alanı önemli yaparsanız verilerinizi kaybedersiniz.

## <a name="detection-engines"></a>Algılama altyapıları

Kendi kendine öğrenme Analizi motorları, imzaları güncelleştirme veya kuralları tanımlama ihtiyacını ortadan kaldırır. Altyapılar, kötü amaçlı yazılım, işlemsel sorunlar, protokol ihlalleri ve temel ağ etkinliği sapmaları için OT ağ trafiğini sürekli olarak analiz etmek üzere ICS 'ye özgü davranış analizlerini ve veri bilimi kullanır.

> [!NOTE]
> Tüm güvenlik altyapılarını etkinleştirmenizi öneririz.

Bir altyapı bir sapma algıladığında, bir uyarı tetiklenir. Uyarıları uyarı ekranından veya bir iş ortağı sisteminden görüntüleyebilir ve yönetebilirsiniz.

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/deviation-alert-screen.png" alt-text="Sapmanın algılanmasını gösteren ekran görüntüsü.":::

Uyarıyı tetikleyen altyapının adı uyarı başlığı altında görüntülenir.

### <a name="protocol-violation-engine"></a>Protokol ihlali altyapısı 

Paket yapısı veya alan değerleri protokol belirtimine uymadığında protokol ihlali oluşur.

Örnek senaryo: *"geçersiz MODBUS işlemi (Işlev kodu sıfır)"* uyarısı. Bu uyarı, birincil bir cihazın bir ikincil cihaza 0 işlev koduna sahip bir istek gönderdiğini gösterir. Bu eyleme protokol belirtimine göre izin verilmez ve ikincil cihaz girişi doğru şekilde işleyemeyebilir.

### <a name="policy-violation-engine"></a>İlke ihlali altyapısı

Öğrenilen veya yapılandırılmış ayarlarda tanımlanan taban çizgisi davranışından sapmayla bir ilke ihlali oluşur.

Örnek senaryo: *"YETKISIZ http Kullanıcı Aracısı"* uyarısı. Bu uyarı, ilke tarafından öğrenilen veya onaylanan bir uygulamanın bir cihazda HTTP istemcisi olarak kullanıldığını belirtir. Bu, bu cihazdaki yeni bir Web tarayıcısı veya uygulama olabilir.

### <a name="malware-engine"></a>Kötü amaçlı yazılım altyapısı

Kötü amaçlı yazılım altyapısı kötü amaçlı ağ etkinliğini algılar.

Örnek senaryo: *"şüphesi of Kötü_niyetli Activity (Stuxnet)"* uyarısı. Bu uyarı, sensörin Stuxnet kötü amaçlı yazılımlara ilişkin olarak bilinen şüpheli ağ etkinliğini algıladığını gösterir. Bu kötü amaçlı yazılım endüstriyel denetim ve SCADA ağlarını hedefleyen gelişmiş kalıcı bir tehdittir.

### <a name="anomaly-engine"></a>Anomali altyapısı

Anomali altyapısı ağ davranışında bozukluklar algılar.

Örnek senaryo: *"Iletişim kanalında düzenli davranış"* uyarısı. Bileşen, ağ bağlantılarını inceler ve veri aktarımının düzenli ve döngüsel davranışını bulur. Bu davranış endüstriyel ağlarda yaygındır.

### <a name="operational-engine"></a>İşlemsel altyapı

Işletimsel motor, işlemsel olayları veya hatalı çalışan varlıkları algılar.

Örnek senaryo: *"cihazın bağlantısının kesilmesi (yanıt vermeyen)"* uyarısı. Bu uyarı, bir cihaz önceden tanımlanmış bir süre için herhangi bir istek türüne yanıt vermediğinde tetiklenir. Bu uyarı, bir cihazın kapatılmasını, bağlantısının kesilmesi veya hatalı çalışmasına işaret edebilir.

### <a name="enable-and-disable-engines"></a>Altyapıları etkinleştirme ve devre dışı bırakma

Bir ilke altyapısını devre dışı bıraktığınızda, altyapının ürettiği bilgiler sensörde kullanılamaz. Örneğin, anomali altyapısını devre dışı bıraktığınızda, ağ bozuklumlarında uyarılar almazsınız. Bir iletme kuralı oluşturduysanız, altyapının öğrenmediği anormallikleri gönderilmez. Bir ilke altyapısını etkinleştirmek veya devre dışı bırakmak için, belirli bir altyapı için **etkin** veya **devre dışı** seçeneğini belirleyin.

Genel puan, **sistem ayarları** ekranının sağ alt köşesinde görüntülenir. Puan, tehdit koruması motorları aracılığıyla etkinleştirilen kullanılabilir korumanın yüzdesini gösterir. Her altyapının kullanılabilir korumanın yüzde 20 ' si vardır.

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/protection-score-screen.png" alt-text="Puan gösteren ekran görüntüsü.":::

## <a name="configure-dhcp-address-ranges"></a>DHCP adres aralıklarını yapılandırma

Ağınız hem statik hem de dinamik IP adreslerinden oluşabilir. Genellikle statik adresler, bu ağlarda, anahtarlar ve yönlendiriciler gibi hisler, denetleyiciler ve ağ altyapısı cihazları aracılığıyla yer alır. Dinamik IP ayırması genellikle dizüstü bilgisayarlar, PC 'Ler, akıllı telefonlar ve diğer taşınabilir ekipmanların (Wi-Fi veya LAN fiziksel bağlantıları farklı konumlarda kullanarak) Konuk ağlarda uygulanır.

Dinamik ağlarla çalışıyorsanız, yeni IP adresleri atandığında gerçekleşen IP adresi değişikliklerini işleyeceğiz. Bunu, DHCP adres aralıklarını tanımlayarak yapabilirsiniz.

Örneğin, bir DHCP sunucusu IP adresleri atarken değişiklikler oluşabilir.

Her sensörde dinamik IP adreslerini tanımlamak, IP adresi değişiklikleri örneklerinde kapsamlı ve şeffaf destek sunar. Bu, her benzersiz cihaz için kapsamlı raporlama sağlar.

Algılayıcı konsolu, cihazla ilişkili en güncel IP adresini sunar ve hangi cihazların dinamik olduğunu gösterir. Örnek:

- Veri araştırma raporu ve cihaz envanteri raporu, IP adresi değişikliklerinden bağımsız olarak cihazdan öğrenilen tüm etkinlikleri tek bir varlık olarak birleştirir. Bu raporlar, hangi adreslerin DHCP adresleri olarak tanımlandığını gösterir.

  :::image type="content" source="media/how-to-control-what-traffic-is-monitored/populated-device-inventory-screen-v2.png" alt-text="Cihaz envanterini gösteren ekran görüntüsü.":::

- **Cihaz özellikleri** penceresi, CIHAZıN bir DHCP aygıtı olarak tanımlanıp tanımlanmadığını gösterir.

DHCP adres aralığı ayarlamak için:

1.  Yan menüde, **sistem ayarları** penceresinden **DHCP aralıkları** ' nı seçin.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/dhcp-address-range-screen.png" alt-text="DHCP aralıklarının seçimini gösteren ekran görüntüsü.":::

2.  **Ve değerlerini** ayarlayarak yeni bir Aralık tanımlayın. 

3.  İsteğe bağlı: en fazla 256 karakter olan Aralık adını tanımlayın.

4.  Aralıkları bir CSV dosyasına aktarmak için **dışarı aktar**' ı seçin.

5. Bir CSV dosyasından el ile birden çok Aralık eklemek için **Içeri aktar** ' ı seçin ve ardından dosyayı seçin.

    > [!NOTE]
    > Bir CSV dosyasından içe aktardığınız aralıklar var olan Aralık ayarlarının üzerine yazar.

6. **Kaydet**’i seçin.

## <a name="configure-dns-servers-for-reverse-lookup-resolution"></a>Geriye doğru arama çözümlemesi için DNS sunucularını yapılandırma

Cihaz zenginleştirme işlemini iyileştirmek için, birden çok DNS sunucusunu carryout ters aramalar için yapılandırabilirsiniz. Ağ alt ağlarında algılanan IP adresleriyle ilişkili ana bilgisayar adlarını veya FQDN 'leri çözebilirsiniz. Örneğin, bir algılayıcı bir IP adresi belirlerse, ana bilgisayar adını çözümlemek için birden çok DNS sunucusu sorgulayabilir.

Tüm CıDR biçimleri desteklenir.

Ana bilgisayar adı cihaz envanterinde, cihaz eşlemesinde ve raporlarda görüntülenir.

Geriye doğru arama çözümleme zamanlamalarını, her 12 saatte bir belirli saatlik aralıklar için zamanlayabilirsiniz. Ya da belirli bir zaman zamanlayabilirsiniz.

DNS sunucularını tanımlamak için:

1. **Sistem ayarları** ' nı seçin ve **DNS ayarları**' nı seçin.

2. **DNS sunucusu Ekle**' yi seçin.

    :::image type="content" source="media/how-to-enrich-asset-information/dns-reverse-lookup-configuration-screen.png" alt-text="DNS sunucusu ekle seçimini gösteren ekran görüntüsü.":::

3. **Ters DNS aramasını zamanla** alanında şunlardan birini seçin:

    - Aralıklar (saat başına).
  
    - Belirli bir saat. Avrupa biçimlendirmesi kullanın. Örneğin, **2:30**' yi değil **14:30** kullanın.

4. **DNS sunucusu adresi** ALANıNA DNS IP adresini girin.

5. **DNS sunucusu bağlantı noktası** alanına DNS bağlantı noktasını girin.

6. Ağ IP adreslerini cihaz FQDN 'leri olarak çözün. **Etiket sayısı** alanında, görüntülenecek etki alanı etiketlerinin sayısını ekleyin. Soldan sağa en fazla 30 karakter görüntülenir.

7. **Alt ağlar** alanında, DNS sunucusunun sorgulamasını istediğiniz alt ağları girin.

8. Ters aramayı başlatmak istiyorsanız, geçişi **Etkinleştir** ' i seçin.

### <a name="test-the-dns-configuration"></a>DNS yapılandırmasını test etme 

Bir test cihazı kullanarak, tanımladığınız ayarların düzgün çalıştığını doğrulayın:

1. **DNS arama** geçişi ' ni etkinleştirin.

2. **Test**'i seçin.

3. **Sunucu Için DNS geriye doğru arama testi** iletişim kutusu Için **arama adresi** ' nde bir adres girin.

    :::image type="content" source="media/how-to-enrich-asset-information/dns-reverse-lookup-test-screen.png" alt-text="Arama adres alanını gösteren ekran görüntüsü.":::

4. **Test**'i seçin.

## <a name="configure-windows-endpoint-monitoring"></a>Windows uç nokta Izlemeyi yapılandırma

Windows uç nokta Izleme özelliği sayesinde IoT için Azure Defender 'ı, Windows sistemlerini seçmeli olarak araştırmayı yapılandırabilirsiniz. Bu, hizmet paketi düzeyleri gibi cihazlarınızla ilgili daha odaklanmış ve doğru bilgiler sağlar.

Belirli aralıklar ve konaklarla yoklama yapılandırabilir ve bunu yalnızca istenen sıklıkta gerçekleştirilecek şekilde yapılandırabilirsiniz. Microsoft 'un Windows sistemlerini yönetmek için standart komut dosyası dili olan Windows Yönetim Araçları (WMI) kullanarak Seçmeli yoklama gerçekleştirirsiniz.

> [!NOTE]
> - Tek seferde yalnızca bir tarama çalıştırabilirsiniz.
> - Etki alanı veya yerel yönetici ayrıcalıklarına sahip kullanıcılarla en iyi sonuçları elde edersiniz.
> - WMI yapılandırmasına başlamadan önce, UDP bağlantı noktası 135 ve tüm TCP bağlantı noktalarını 1024 ' i kullanarak sensörden taranan alt ağa giden trafiği açan bir güvenlik duvarı kuralı yapılandırın.

Araştırma tamamlandığında, bir günlüğü dışarı aktarma seçeneğinde tüm yoklama girişimlerini içeren bir günlük dosyası bulunur. Günlük, araştırılan tüm IP adreslerini içerir. Günlük, her IP adresi için başarı ve başarısızlık bilgilerini gösterir. Ayrıca, özel durumdan türetilmiş bir boş dize olan bir hata kodu da vardır. Yalnızca son günlüğün taranması sistemde tutulur.

Zamanlanmış taramalar veya el ile taramalar yapabilirsiniz. Bir tarama tamamlandığında sonuçları bir CSV dosyasında görüntüleyebilirsiniz.

**Önkoşullar**

UDP bağlantı noktası 135 ve tüm TCP bağlantı noktalarını 1024 ' i kullanarak sensörden taranan alt ağa giden trafiği açan bir güvenlik duvarı kuralı yapılandırın.

Otomatik tarama yapılandırmak için:

1. Yan menüde **sistem ayarları**' nı seçin.

2. **Windows uç nokta izleme** ' yi seçin :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-icon-v2.png" border="false"::: .

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-screen-v2.png" alt-text="Windows uç nokta Izlemenin seçimini gösteren ekran görüntüsü.":::

3. **Tarama zamanlaması** bölmesinde, seçenekleri aşağıdaki gibi yapılandırın:

      - **Sabit aralıklar (saat) ile**: tarama zamanlamasını saat cinsinden aralıklara göre ayarlayın.

      - **Belirli zamanlarda**: tarama zamanlamasını belirli saatlere göre ayarlayın ve **taramayı kaydet**' i seçin.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/schedule-a-scan-screen-v2.png" alt-text="Taramayı Kaydet düğmesini gösteren ekran görüntüsü.":::

4. Tarama aralığını tanımlamak için **tarama aralıklarını ayarla**' yı seçin.

5. IP adresi aralığını ayarlayın ve Kullanıcı ve parolanızı ekleyin.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/edit-scan-range-screen.png" alt-text="Kullanıcı ve parola eklemeyi gösteren ekran görüntüsü.":::

6. Bir IP aralığını taramadan dışlamak için aralığın yanındaki **devre dışı bırak** ' ı seçin.

7. Bir aralığı kaldırmak için aralığın ileri ' yi seçin :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/remove-scan-icon.png" border="false"::: .

8. **Kaydet**’i seçin. **Tarama aralıklarını Düzenle yapılandırma** iletişim kutusu kapanır ve Aralık sayısı **tarama aralıkları** bölmesinde görünür.

El ile tarama gerçekleştirmek için:

1. Yan menüde **sistem ayarları**' nı seçin.

2. **Windows uç nokta izleme** ' yi seçin :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-icon-v2.png" border="false"::: .

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-screen-v2.png" alt-text="Windows uç nokta Izleme kurulum ekranını gösteren ekran görüntüsü.":::

3. **Eylemler** bölmesinde **Taramayı Başlat**' ı seçin. **Eylemler** bölmesinde bir durum çubuğu görünür ve tarama işleminin ilerleme durumunu gösterir.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/started-scan-screen-v2.png" alt-text="Taramayı Başlat düğmesini gösteren ekran görüntüsü.":::

Tarama sonuçlarını görüntülemek için:

1. Tarama tamamlandığında, **Eylemler** bölmesinde, **tarama sonuçlarını görüntüle**' yi seçin. Tarama sonuçlarıyla birlikte CSV dosyası bilgisayarınıza indirilir.

## <a name="see-also"></a>Ayrıca bkz.

[Bir cihaz envanterinde](how-to-investigate-sensor-detections-in-a-device-inventory.md) 
 algılayıcı algılamalarını araştırın [Cihaz eşlemesindeki algılayıcı algılamalarını araştırın](how-to-work-with-the-sensor-device-map.md)
