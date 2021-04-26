---
title: Algılayıcınızı etkinleştirme ve ayarlama
description: Bu makalede, bir algılayıcı konsolunun nasıl oturum açılacağını ve etkinleştirileceği açıklanır.
ms.date: 1/12/2021
ms.topic: how-to
ms.openlocfilehash: b040a87c2eae36e6049b4e1d0cf51ecb2cfde108
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385127"
---
# <a name="activate-and-set-up-your-sensor"></a>Algılayıcınızı etkinleştirme ve ayarlama

Bu makalede, bir algılayıcıyı etkinleştirme ve ilk kurulum gerçekleştirme açıklanır.

Yönetici kullanıcılar ilk kez oturum açarken ve etkinleştirme yönetimi gerektiğinde etkinleştirme işlemi gerçekleştirebilir. Kurulum, sensörin optimum olarak algılanması ve uyarı vermesi için yapılandırılmasını sağlar.

Güvenlik analistleri ve salt okuma kullanıcıları bir algılayıcıyı etkinleştiremez veya yeni bir parola oluşturabilir.

## <a name="sign-in-and-activation-for-administrator-users"></a>Yönetici kullanıcılar için oturum açma ve etkinleştirme

İlk kez oturum açan yöneticiler, algılayıcı ekleme sırasında indirilen etkinleştirme ve parola kurtarma dosyalarına erişimi olduğunu doğrulamalıdır. Aksi takdirde, IoT portalında Azure Defender 'da bu dosyaları oluşturmak için Azure Güvenlik Yöneticisi, abonelik katılımcısı veya abonelik sahibi izinlerine sahip olmaları gerekir.

### <a name="first-time-sign-in-and-activation-checklist"></a>İlk kez oturum açma ve etkinleştirme denetim listesi

Algılayıcı konsolunda oturum açmadan önce yönetici kullanıcıların erişim sahibi olması gerekir:

- Yükleme sırasında tanımlanan algılayıcı IP adresi.

- Algılayıcı için Kullanıcı oturum açma kimlik bilgileri. Algılayıcı için bir ISO indirdiyseniz, yükleme sırasında aldığınız varsayılan kimlik bilgilerini kullanın. Etkinleştirmeden sonra yeni bir *yönetici* Kullanıcı oluşturmanızı öneririz.

- İlk parola. Bir okla önceden yapılandırılmış bir algılayıcı satın aldıysanız, ilk kez oturum açarken bir parola oluşturmanız gerekir.

- Bu sensörle ilişkili etkinleştirme dosyası. Dosya, IoT portalı için Defender 'da algılayıcı ekleme sırasında oluşturulmuştur ve indirilir.

- Şirketinizin gerektirdiği bir SSL/TLS CA tarafından imzalanan sertifika.

### <a name="about-activation-files"></a>Etkinleştirme dosyaları hakkında

Sensörizin belirli bir yönetim modunda IoT için Azure Defender eklendi:

| Mod türü | Açıklama |
|--|--|
| **Buluta bağlı mod** | Algılayıcıyı algılayan bilgiler algılayıcı konsolunda görüntülenir. Uyarı bilgileri de IoT Hub 'ı aracılığıyla dağıtılır ve Azure Sentinel gibi diğer Azure hizmetleriyle paylaşılabilir. |
| **Yerel olarak bağlı mod** | Algılayıcıyı algılayan bilgiler algılayıcı konsolunda görüntülenir. Algılama bilgileri, algılayıcı kendisine bağlıysa şirket içi yönetim konsolu ile de paylaşılır. |

Yerel olarak bağlı veya buluta bağlı bir etkinleştirme dosyası, ekleme sırasında bu algılayıcı için üretildi ve indirilir. Etkinleştirme dosyası, sensör yönetim modu için yönergeler içerir. *Dağıttığınız her sensöre benzersiz bir etkinleştirme dosyası yüklenmelidir.*  İlk kez oturum açtığınızda, bu algılayıcı için ilgili etkinleştirme dosyasını karşıya yüklemeniz gerekir.

:::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-activation-file-download-button.png" alt-text="IoT portalı için Azure Defender, algılayıcısı ekleme.":::

### <a name="about-certificates"></a>Sertifikalar hakkında

Algılayıcı yüklemesinin ardından, yerel olarak imzalanan bir sertifika oluşturulur ve algılayıcı konsoluna erişim için kullanılır. Yönetici konsolunda ilk kez oturum açtıktan sonra, bu kullanıcıdan bir SSL/TLS sertifikası girmesi istenir.

İki güvenlik düzeyi mevcuttur:

- CA imzalı sertifikayı karşıya yükleyerek kuruluşunuz tarafından istenen belirli sertifika ve şifreleme gereksinimlerini karşılayın.
- Yönetim Konsolu ve bağlı algılayıcılar arasında doğrulamaya izin verin. Doğrulama, bir sertifika iptal listesi ve sertifika sona erme tarihine göre değerlendirilir. *Doğrulama başarısız olursa, Yönetim Konsolu ile algılayıcı arasındaki iletişim durdurulur ve konsolda bir doğrulama hatası görüntülenir.* Bu seçenek, yüklemeden sonra varsayılan olarak etkinleştirilmiştir.  

Konsolu aşağıdaki sertifika türlerini destekler:

- Özel ve Kurumsal anahtar altyapısı (özel PKI)

- Ortak anahtar altyapısı (genel PKI)

- Gereç üzerinde yerel olarak oluşturuldu (yerel olarak kendinden imzalı) 

  > [!IMPORTANT]
  > Varsayılan otomatik olarak imzalanan sertifikayı kullanmanızı öneririz. Sertifika güvenli değil ve yalnızca test ortamları için kullanılmalıdır. Sertifikanın sahibi doğrulanamaz ve sisteminizin güvenliği korunabilir olamaz. Bu seçeneği, üretim ağları için hiçbir şekilde kullanmayın.

### <a name="sign-in-and-activate-the-sensor"></a>Oturum açın ve algılayıcıyı etkinleştirin

Oturum açmak ve etkinleştirmek için:

1. Yükleme sırasında tanımlanan IP 'yi kullanarak tarayıcınızdan algılayıcı konsoluna gidin. Oturum açma iletişim kutusu açılır.

    :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-sensor-log-in-screen.png" alt-text="IoT algılayıcısı için Azure Defender.":::

1. Algılayıcı yüklemesi sırasında tanımlanan kimlik bilgilerini girin veya **parola kurtarma** seçeneğini belirleyin. Bir okla önceden yapılandırılmış bir algılayıcı satın aldıysanız, önce bir parola oluşturun. Parola kurtarma hakkında daha fazla bilgi için bkz. [ilk oturum açma sırasında parola hatasını araştırın](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md#investigate-password-failure-at-initial-sign-in).

1. Oturum açtıktan sonra, **etkinleştirme** iletişim kutusu açılır. **Karşıya yükle** ' yi seçin ve algılayıcı ekleme sırasında indirdiğiniz etkinleştirme dosyasına gidin.

   :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/activation-upload-screen-with-upload-button.png" alt-text="Karşıya yükle ' yi seçin ve etkinleştirme dosyasına gidin.":::

1. Etkinleştirmeden önce algılayıcı ağ yapılandırmasını değiştirmek istiyorsanız, **algılayıcı ağ yapılandırması** bağlantısını seçin. Bkz. etkinleştirmeden [önce algılayıcı ağ yapılandırmasını güncelleştirme](#update-sensor-network-configuration-before-activation).

1. Hüküm ve koşulları kabul edin.

1. **Etkinleştir**' i seçin. SSL/TLS sertifikası iletişim kutusu açılır.

1. Bir sertifika adı tanımlayın.
1. CRT ve anahtar dosyalarını karşıya yükleyin.
1. Bir parola girin ve gerekirse bir ped dosyası yükleyin.
1. **İleri**’yi seçin. Doğrulama ekranı açılır. Varsayılan olarak, Yönetim Konsolu ve bağlı algılayıcılar arasındaki doğrulama etkindir.
1. Doğrulamayı devre dışı bırakmak için **sistem genelinde doğrulama etkinleştir** ' i kapatın. Doğrulamayı etkinleştirmenizi öneririz.
1. **Kaydet**’i seçin.  

CA imzalı sertifikayı karşıya yükledikten sonra ekranınızı yenilemeniz gerekebilir.

Yeni bir sertifika yükleme, desteklenen sertifika parametreleri ve CLı sertifikası komutlarıyla çalışma hakkında daha fazla bilgi için bkz. [tek algılayıcıları yönetme](how-to-manage-individual-sensors.md).

#### <a name="update-sensor-network-configuration-before-activation"></a>Etkinleştirmeden önce algılayıcı ağ yapılandırmasını Güncelleştir  

Algılayıcı ağı yapılandırma parametreleri, yazılım yüklemesi sırasında veya önceden yapılandırılmış bir algılayıcı satın aldığınızda tanımlanmıştır. Aşağıdaki parametreler tanımlandı:

- IP Adresi
- DNS
- Varsayılan ağ geçidi
- Alt ağ maskesi
- Konak adı

Algılayıcıyı etkinleştirmeden önce bu bilgileri güncelleştirmek isteyebilirsiniz. Örneğin, ok ile tanımlanan önceden yapılandırılmış parametreleri değiştirmeniz gerekebilir. Algılayıcıyı etkinleştirmeden önce proxy ayarlarını da tanımlayabilirsiniz.

Algılayıcı ağı yapılandırma parametrelerini güncelleştirmek için:

1. **Etkinleştirme** iletişim kutusunu, **algılayıcı ağ yapılandırması** bağlantısını seçin.

   :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/editable-network-configuration-screen-v2.png" alt-text="Algılayıcı ağ yapılandırması.":::

2. Yükleme sırasında tanımlanan parametreler görüntülenir. Proxy 'yi tanımlama seçeneği de kullanılabilir. Gereken ayarları güncelleştirin ve **Kaydet**' i seçin.

### <a name="subsequent-sign-ins"></a>Sonraki oturum açma işlemleri

İlk kez etkinleştirmenin ardından, IoT algılayıcı konsolu için Azure Defender, etkinleştirme dosyası gerekmeden oturum açtıktan sonra açılır. Yalnızca oturum açma kimlik bilgileriniz gereklidir.

Oturum açtıktan sonra IoT için Azure Defender konsolu açılır.

:::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-log-in-screen-dashboard-v2.png" alt-text="IoT konsolu için Azure Defender.":::

## <a name="initial-setup-and-learning-for-administrators"></a>İlk kurulum ve öğrenme (Yöneticiler için)

İlk oturum açma işleminden sonra IoT için Azure Defender algılayıcısı, ağınızı otomatik olarak izlemeye başlar. Ağ aygıtları cihaz Haritası ve cihaz envanteri bölümlerinde görünür. IoT için Azure Defender, ağınızda oluşan tüm güvenlik ve işletimsel olaylar hakkında sizi algılamaya ve sizi uyaracak. Daha sonra, algılanan bilgilere göre raporlar ve sorgular oluşturabilirsiniz.

Başlangıçta bu etkinlik öğrenme modunda yürütülür, bu da sensörize ağınızın olağan etkinliklerini öğrenmesini sağlar. Örneğin, algılayıcı ağınızda bulunan cihazları öğreniyor, ağda algılanan protokoller ve belirli aygıtlar arasında gerçekleşen dosya aktarımları. Bu etkinlik ağınızın temel etkinliği haline gelir.

### <a name="review-and-update-basic-system-settings"></a>Temel sistem ayarlarını gözden geçirme ve güncelleştirme

Sensörin optimum olarak algılanması ve uyarı verecek şekilde yapılandırıldığından emin olmak için sensör sistem ayarlarını gözden geçirin.

Algılayıcının sistem ayarlarını tanımlayın. Örnek:

- ICS (veya IoT) ve ayrılmış alt ağları tanımlayın.

- Siteye özgü protokoller için bağlantı noktası diğer adlarını tanımlayın.

- Kullanımdaki VLAN 'Ları ve adları tanımlayın.

- DHCP kullanılıyorsa, meşru DHCP aralıklarını tanımlayın.

- Active Directory ve posta sunucusu ile tümleştirmeyi uygun şekilde tanımlayın.

### <a name="disable-learning-mode"></a>Öğrenme modunu devre dışı bırak

Sistem ayarlarını ayarladıktan sonra, sistem algılamaları 'nın ağ etkinliğinizi doğru şekilde yansıttığından, IoT algılayıcısı için Azure Defender 'ın öğrenme modunda çalışmasına izin verebilirsiniz.

Öğrenme modunun, ağ boyutunuza ve karmaşıklığına bağlı olarak 2 ile 6 hafta arasında çalışması gerekir. Öğrenme modunu devre dışı bıraktıktan sonra, taban çizgisi etkinlikinizden farklı olan herhangi bir etkinlik bir uyarı tetikleyecektir.

Öğrenme modunu devre dışı bırakmak için:

- **Sistem ayarları** ' nı seçin ve **öğrenme** seçeneğini kapatın.

## <a name="first-time-sign-in-for-security-analysts-and-read-only-users"></a>Güvenlik analistleri ve salt okuma kullanıcıları için ilk kez oturum açma

Oturum açmadan önce şunları doğrulayın:

- Algılayıcı IP adresi.
- Yöneticinizin sağladaki oturum açma kimlik bilgileri.

## <a name="console-tools-overview"></a>Konsol araçları: genel bakış

Konsol araçlarına taraftaki menüden erişin.

**Gezinti** 

| Pencere | Simge | Açıklama |
| -----------|--|--|
| Pano | :::image type="icon" source="media/concept-sensor-console-overview/dashboard-icon-azure.png" border="false"::: | Ağın güvenlik durumunun sezgisel bir anlık görüntüsünü görüntüleyin. |
| Cihaz Haritası | :::image type="icon" source="media/concept-sensor-console-overview/asset-map-icon-azure.png" border="false"::: | Bir haritadaki ağ cihazlarını, cihaz bağlantılarını ve cihaz özelliklerini görüntüleyin. Ağınızı göstermek için çeşitli yakınlaştırmaları, vurgu ve filtreleme seçenekleri mevcuttur. |
| Cihaz envanteri | :::image type="icon" source="media/concept-sensor-console-overview/asset-inventory-icon-azure.png" border="false":::  | Cihaz envanteri, bu sensörin algıladığı cihaz özniteliklerinin bir listesini görüntüler. Seçenekler kullanılabilir: <br /> -Tablo alanlarına göre bilgileri sıralayın veya filtreleyin ve filtrelenmiş bilgilerin görüntülendiğini görün. <br /> -Bilgileri bir CSV dosyasına dışarı aktarın. <br /> -Windows kayıt defteri ayrıntılarını içeri aktarın.|
| Uyarılar | :::image type="icon" source="media/concept-sensor-console-overview/alerts-icon-azure.png" border="false"::: | İlke ihlalleri oluştuğunda uyarı görüntüle, taban çizgisi davranışından sapmalar oluşur veya ağdaki herhangi bir şüpheli etkinlik türü algılanır. |
| Raporlar | :::image type="icon" source="media/concept-sensor-console-overview/reports-icon-azure.png" border="false"::: | Veri madenciliği sorgularını temel alan raporları görüntüleyin. |

**Çözümlemeleri**

| Pencere| Simge | Açıklama |
|---|---|---|
| Olay zaman çizelgesi | :::image type="icon" source="media/concept-sensor-console-overview/event-timeline-icon-azure.png" border="false"::: | Uyarılar, ağ olayları (bilgilendirici) ve Kullanıcı oturumu açma ve Kullanıcı silmeleri gibi kullanıcı işlemleri hakkında bilgi içeren bir zaman çizelgesi görüntüleyin.|

**Gezinti**

| Pencere | Simge | Açıklama |
|---|---|---|
| Veri araştırma | :::image type="icon" source="media/concept-sensor-console-overview/data-mining-icon-azure.png" border="false"::: | Çeşitli katmanlarda ağınızın cihazları hakkında kapsamlı ve ayrıntılı bilgiler oluşturun. |
| Araştırma | :::image type="icon" source="media/concept-sensor-console-overview/trends-and-statistics-icon-azure.jpg" border="false"::: | Eğilimleri ve istatistikleri geniş bir pencere öğesi aralığında görüntüleyin. |
| Risk değerlendirmesi | :::image type="icon" source="media/concept-sensor-console-overview/vulnerabilities-icon-azure.png" border="false"::: | **Güvenlik açıkları** penceresini görüntüleyin. |

**Yönetici**

| Pencere | Simge | Açıklama |
|---|---|---|
| Kullanıcılar | :::image type="icon" source="media/concept-sensor-console-overview/users-icon-azure.png" border="false"::: | Farklı erişim düzeylerine sahip kullanıcıları ve rolleri tanımlayın. |
| İletildiğinde | :::image type="icon" source="media/concept-sensor-console-overview/forwarding-icon-azure.png" border="false"::: | IoT için Defender ile tümleştirme, e-posta adresleri, Web kancası sunucuları ve daha fazlası için, uyarı bilgilerini iş ortaklarına ve iç kaynaklara (örneğin, Azure Sentinel) iletin. <br /> Ayrıntılar için bkz. [uyarı bilgilerini ilet](how-to-forward-alert-information-to-partners.md) . |
| Sistem ayarları | :::image type="icon" source="media/concept-sensor-console-overview/system-settings-icon-azure.png" border="false"::: | Sistem ayarlarını yapılandırın. Örneğin, DHCP ayarlarını tanımlayın, posta sunucusu ayrıntılarını sağlayın veya bağlantı noktası diğer adları oluşturun. |
| İçeri aktarma ayarları | :::image type="icon" source="media/concept-sensor-console-overview/import-settings-icon-azure.png" border="false"::: | **Içeri aktarma ayarları** penceresini görüntüleyin. Bir cihaz bilgilerinde el ile değişiklikler yapabilirsiniz.<br /> Ayrıntılar için bkz. [cihaz bilgilerini Içeri aktarma](how-to-import-device-information.md) . |

**Destek**

| Pencere| Simge | Açıklama |
|----|---|---|
| Destek | :::image type="icon" source="media/concept-sensor-console-overview/support-icon-azure.png" border="false"::: | Yardım için [Microsoft desteği](https://support.microsoft.com/) başvurun. |

## <a name="see-also"></a>Ayrıca bkz.

[Bir algılayıcı ekleme](getting-started.md#onboard-a-sensor)

[Algılayıcı etkinleştirme dosyalarını Yönet](how-to-manage-individual-sensors.md#manage-sensor-activation-files)

[Hangi trafiğin izleneceğini denetleme](how-to-control-what-traffic-is-monitored.md)
