---
title: Palo Alto tümleştirmesi
description: IoT için Defender, sürekli ICS tehdit izleme platformunu Palo Alto 'nın yeni nesil güvenlik duvarları ile tümleştirerek kritik tehditleri engellemeyi, daha hızlı ve daha verimli hale getirin.
ms.date: 1/17/2021
ms.topic: article
ms.openlocfilehash: 49c00f9ad7cc1980a8690e35ed19351e20417c6a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104783940"
---
# <a name="about-the-palo-alto-integration"></a>Palo Alto tümleştirmesi hakkında

IoT için Defender, sürekli ICS tehdit izleme platformunu Palo Alto 'nın yeni nesil güvenlik duvarları ile tümleştirerek kritik tehditleri engellemeyi, daha hızlı ve daha verimli hale getirin.

Aşağıdaki tümleştirme türleri kullanılabilir:

- Otomatik engelleme seçeneği: IoT-Palo Alto tümleştirmesi için doğrudan Defender

- Merkezi yönetim sistemine engelleme önerileri gönderme: Defender IoT-Panorama tümleştirmesi için

## <a name="configure-immediate-blocking-by-specified-palo-alto-firewall"></a>Belirtilen Palo Alto güvenlik duvarıyla anında engellemeyi yapılandırma

Kötü amaçlı yazılımlara ilişkin uyarılar gibi kritik durumlarda otomatik engellemeyi etkinleştirebilirsiniz. Bu, Defender 'da engelleme komutunu doğrudan belirli bir Palo Alto güvenlik duvarına gönderen IoT için yapılandırarak yapılır.

IoT için Defender kritik bir tehdit belirlediğinde, virüslü kaynağı engelleme seçeneğini içeren bir uyarı gönderir. Uyarı ayrıntılarında **blok kaynağı** seçeneğinin belirlenmesi, belirtilen Palo Alto güvenlik duvarına engelleme komutunu gönderen iletme kuralını etkinleştirir.

Yapılandırmak için:

1. Sol bölmede, **iletme**' yi seçin.

   :::image type="content" source="media/integration-paloalto/forwarding.png" alt-text="İletme uyarısı ekranı.":::

1. **Iletme kuralı oluştur**' u seçin.

   :::image type="content" source="media/integration-paloalto/forward-rule.png" alt-text="Iletme kuralı oluştur":::

1. Palo Alto NGFW Iletme kuralını yapılandırmak için:  
 
   - Standart kural parametrelerini tanımlayın ve **Eylemler** açılan kutusunda, **Palo Alto NGFW 'a gönder**' i seçin.
   
   :::image type="content" source="media/integration-paloalto/edit.png" alt-text="Iletme kuralınızı düzenleyin.":::

1. Eylemler bölmesinde, aşağıdaki parametreleri ayarlayın:

   - **Ana bilgisayar**: NGFW sunucusu IP adresini girin.
   - **Bağlantı noktası**: NGFW sunucu bağlantı noktasını girin.
   - **Kullanıcı adı**: NGFW sunucu Kullanıcı adını girin.
   - **Parola**: NGFW sunucu parolasını girin.
   - **Yapılandırma**: Palo Alto güvenlik duvarı tarafından şüpheli kaynakların engellenmesini sağlamak için aşağıdaki seçenekleri ayarlayın:
     - **Geçersiz işlev kodlarını engelle**: protokol IHLALLERI-ICS protokol belirtimini ihlal eden alan değeri geçersiz (olası yararlanma).
     - **YETKISIZ PLC programlama/bellenim güncelleştirmelerini engelleyin**: yetkisiz PLC değişiklikleri.
     - **Block YETKILENDIRILMEMIŞ PLC stop**: PLC Stop (kesinti süresi).
     - **Kötü amaçlı yazılımdan ilişkili uyarıları engelleyin**: Endüstriyel kötü amaçlı yazılım girişimlerini engelleme (Triton, NotPetya, vb.). **Otomatik engelleme** seçeneğini belirleyebilirsiniz. Bu durumda, engelleme otomatik olarak ve hemen yürütülür.
     - **Yetkisiz taramayı engelle**: yetkisiz tarama (olası keşif).
     
1. **Gönder**’i seçin.

Şüpheli kaynağı engellemek için: 

1. **Uyarılar** bölmesinde, Palo Alto tümleştirmesi ile ilgili uyarıyı seçin. **Uyarı ayrıntıları** iletişim kutusu görüntülenir.
   
   :::image type="content" source="media/integration-paloalto/unauthorized.png" alt-text="Uyarı ayrıntıları":::

1. Şüpheli kaynağı otomatik olarak engellemek için, **kaynağı engelle**' yi seçin. **Lütfen onaylayın** iletişim kutusu görüntülenir.

   :::image type="content" source="media/integration-paloalto/please.png" alt-text="Lütfen kaldırma ekranında engellemeyi onaylayın.":::

1. **Lütfen onaylayın** Iletişim kutusunda **Tamam**' ı seçin. Şüpheli kaynak, Palo Alto güvenlik duvarıyla engellenmiştir.

## <a name="sending-blocking-policies-to-palo-alto-panorama"></a>Palo Alto Panorama 'e engelleme ilkeleri gönderme

IoT ve Palo Alto Networks için Defender, Palo Alto Networks NMS, Panorama ' de otomatik olarak yeni ilkeler oluşturan raf dışı bir tümleştirmeye sahiptir. Bu tümleştirme, Panorama Yöneticisi tarafından onay gerektirir ve otomatik engellemeye izin vermez.

Tümleştirme, aşağıdaki olaylara yöneliktir:

- **YETKISIZ PLC değişiklikler:** Bir cihazın el ile yapılan bir güncelleştirme mantığı veya bellenimine yönelik bir güncelleştirme. Bu, meşru bir etkinliği veya cihazı tehlikeye atabilir. Bir uzaktan erişim Truva atı (RAT) veya parametreler gibi kötü amaçlı kod (örneğin, dönen türbin gibi) güvenli olmayan bir şekilde çalışacak şekilde, bu uzlaşmaya yol açabilir.

- **Protokol ihlali:** Protokol belirtimini ihlal eden bir paket yapısı veya alan değeri. Bu, yanlış yapılandırılmış bir uygulamayı veya cihazın güvenliğini tehlikeye atabilir. Örneğin, hedef cihazda bir arabellek taşması koşuluna neden olur.

- **PLC Durdur:** Cihazın çalışmayı durdurmasına neden olan bir komut, PLC tarafından denetlenen fiziksel işleme karşı risdir.

- **ICS ağında bulunan endüstriyel kötü amaçlı yazılım:** ÜÇ aylık ve Industroyer gibi yerel protokollerini kullanarak ICS cihazlarını işleyen kötü amaçlı yazılım. IoT için Defender, Ayrıca, (Conficker, WannaCry ve NotPetya gibi) daha önce ICS ve SCADA ortamına taşınabilecek BT kötü amaçlı yazılımları algılar.

- **Kötü amaçlı yazılım taranıyor:** Bir saldırı öncesi aşamada sistem yapılandırmasıyla ilgili verileri toplayacak keşif araçları. Örneğin, Havex Truva atı, Windows tabanlı SCADA Sistemleri tarafından ICS cihazlarıyla iletişim kurmak için kullanılan standart bir protokol olan OPC kullanan cihazlar için endüstriyel ağları tarar.

## <a name="the-process"></a>İşlem

IoT için Defender, önceden yapılandırılmış bir kullanım durumu algıladığında, uyarıya **kaynak engelle** düğmesi eklenir. Ardından, **Six** kullanıcısı **blok kaynak** düğmesini seçtiğinde IoT için Defender, önceden tanımlanmış iletme kuralını göndererek Panorama üzerinde ilkeler oluşturur.

İlke yalnızca Panorama Yöneticisi tarafından ağdaki ilgili NGFW 'ya gönderim yapıldığında uygulanır.

BT ağlarında, dinamik IP adresleri olabilir. Bu nedenle, bu alt ağlarda ilke IP adresini değil FQDN 'yi (DNS adı) temel almalıdır. IoT için Defender, ters arama gerçekleştirir ve dinamik IP adresi olan cihazları, her yapılandırılan saat sayısıyla FQDN (DNS adı) olarak eşleştirir.

Ayrıca, IoT için Defender, IoT için Defender tarafından oluşturulan yeni bir ilkenin onay bekliyor olduğunu bildirmek üzere ilgili Panorama kullanıcısına bir e-posta gönderir. Aşağıdaki şekilde, Defender for IoT-Panorama tümleştirme mimarisi sunulmaktadır.

:::image type="content" source="media/integration-paloalto/structure.png" alt-text="Six-Panorama tümleştirme mimarisi":::

## <a name="create-panorama-blocking-policies-in-defender-for-iot-configuration"></a>Defender 'da IoT yapılandırması için Panorama engelleme ilkeleri oluşturma

### <a name="to-configure-dns-lookup"></a>DNS aramasını yapılandırmak için

1. Sol bölmede **sistem ayarları**' nı seçin.

1. **Sistem ayarları** bölmesinde **DNS ayarları** ' nı seçin :::image type="icon" source="media/integration-paloalto/settings.png"::: .

   :::image type="content" source="media/integration-paloalto/configuration.png" alt-text="DNS ayarlarını yapılandırın.":::

1. **DNS ayarlarını Düzenle** iletişim kutusunda aşağıdaki parametreleri ayarlayın:

   - **Durum**: DNS Çözümleyicisinin durumu.

   - **DNS sunucusu adresi**: IP adresini veya ağ DNS sunucusunun FQDN 'sini girin.
   - **DNS sunucusu bağlantı noktası**: DNS sunucusunu sorgulamak için kullanılan bağlantı noktasını girin.
   - **Alt ağlar**: dinamik IP adresi alt ağ aralığını ayarlayın. IoT için Defender 'ın, DNS sunucusundaki IP adreslerini geçerli FQDN adlarıyla eşleşecek şekilde geri çevirdiği Aralık.
   - **Ters arama zamanla**: zamanlama seçeneklerini aşağıdaki gibi tanımlayın:
     - Belirli zamanlarda: her gün tersine aramanın ne zaman gerçekleştirileceğini belirtin.
     - Sabit aralıklar (saat) ile: ters aramayı gerçekleştirme sıklığını ayarlayın.
   - **Etiket sayısı**: bir IoT Için Defender 'ı, ağ IP adreslerini cihaz FQDN 'leri ile otomatik olarak çözümlemeyi bildirin. <br />DNS FQDN çözümlemesini yapılandırmak için, görüntülenecek etki alanı etiketlerinin sayısını ekleyin. Soldan sağa en fazla 30 karakter görüntülenir.
1. **Kaydet**' i seçin.
1. DNS ayarlarınızın doğru olduğundan emin olmak için **arama testi**' ni seçin. Test, DNS sunucusu IP adresi ve DNS sunucusu bağlantı noktasının doğru şekilde ayarlanmış olmasını sağlar.

### <a name="to-configure-a-forwarding-rule-to-blocks-suspected-traffic-with-the-palo-alto-firewall"></a>Bir Iletme kuralını, Palo Alto güvenlik duvarıyla şüpheli trafiği bloğuyla yapılandırmak için

1. Sol bölmede, **iletme**' yi seçin. Iletme bölmesi görünür.

   :::image type="content" source="media/integration-paloalto/forward.png" alt-text="İletme ekranı.":::

1. **İletme** bölmesinde, **iletme kuralı oluştur**' u seçin.

   :::image type="content" source="media/integration-paloalto/forward-rule.png" alt-text="Iletme kuralı oluştur":::

1. Palo Alto Panorama Iletme kuralını yapılandırmak için:

   Standart kural parametrelerini tanımlayın ve **Eylemler** açılan kutusundan, **Palo Alto Panorama 'e gönder**' i seçin. Eylem ayrıntıları bölmesi görüntülenir.

   :::image type="content" source="media/integration-paloalto/details.png" alt-text="Eylem Seç":::

1. Eylemler bölmesinde, aşağıdaki parametreleri ayarlayın:

   - **Ana bilgisayar**: Panorama sunucusu IP adresini girin.

   - **Bağlantı noktası**: Panorama sunucu bağlantı noktasını girin.
   - **Kullanıcı adı**: Panorama sunucusu kullanıcı adını girin.
   - **Parola**: Panorama sunucu parolasını girin.
   - **Rapor adresi**: engellemenin nasıl yürütüleceğini aşağıda gösterildiği gibi tanımlayın:
   
     - **IP adresine göre**: her zaman, IP adresine göre Panorama üzerinde engelleme ilkeleri oluşturur.
     
     - **FQDN veya IP adresi ile**: varsa, IP adresi tarafından, varsa, FQDN 'ye dayalı olarak Panorama üzerinde engelleme ilkeleri oluşturur.
     
   - **E-posta**: ilke bildirimi e-postası için e-posta adresini ayarlayın
     > [!NOTE]
     > IoT için Defender 'da bir posta sunucusu yapılandırdığınızdan emin olun. E-posta adresi girilmemişse, IoT için Defender bir bildirim e-postası göndermez.
   - **Uyarı algılama sonrasında BIR DNS araması gerçekleştir (onay kutusu)**: rapor adresinde FQDN veya IP adresi seçeneği ayarlandığında. Bu onay kutusu varsayılan olarak seçilidir. Yalnızca IP adresi ayarlandıysa, bu seçenek devre dışı bırakılır.
   - **Yapılandırma**: Palo Alto Panorama tarafından şüpheli kaynakların engellenmesini sağlamak için aşağıdaki seçenekleri ayarlayın:
   
     - **Geçersiz işlev kodlarını engelle**: protokol IHLALLERI-ICS, protokol belirtimi (olası yararlanma) Ihlal eden geçersiz alan değeri.
     
     - **YETKISIZ PLC programlama/bellenim güncelleştirmelerini engelleyin**: yetkisiz PLC değişiklikleri.
     
     - **Block YETKILENDIRILMEMIŞ PLC stop**: PLC Stop (kesinti süresi).
     
     - **Kötü amaçlı yazılımdan ilişkili uyarıları engelleyin**: Endüstriyel kötü amaçlı yazılım girişimlerini engelleme (Triton, NotPetya, vb.). **Otomatik engelleme** seçeneğini belirleyebilirsiniz. Bu durumda, engelleme otomatik olarak ve hemen yürütülür.
     
     - **Yetkisiz taramayı engelle**: yetkisiz tarama (olası keşif).
     
1. **Gönder**’i seçin.

### <a name="to-block-the-suspicious-source"></a>Şüpheli kaynağı engellemek için

1. **Uyarılar** bölmesinde, Palo Alto tümleştirmesi ile ilgili uyarıyı seçin. **Uyarı ayrıntıları** iletişim kutusu görüntülenir.

   :::image type="content" source="media/integration-paloalto/unauthorized.png" alt-text="Uyarı ayrıntıları":::        

1. Şüpheli kaynağı otomatik olarak engellemek için, **kaynağı engelle**' yi seçin.

1. **Lütfen onaylayın** Iletişim kutusunda Tamam ' ı seçin **.**

   :::image type="content" source="media/integration-paloalto/please.png" alt-text="Confirm":::

## <a name="next-steps"></a>Sonraki adımlar

[Uyarı bilgilerini iletmeyi](how-to-forward-alert-information-to-partners.md)öğrenin.
