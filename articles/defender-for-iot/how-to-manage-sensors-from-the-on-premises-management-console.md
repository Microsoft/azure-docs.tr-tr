---
title: Algılayıcıları şirket içi yönetim konsolundan yönetme
description: Algılayıcı sürümlerini güncelleştirme, sistem ayarlarını sensörlerden iletme ve algılayıcılar üzerinde altyapıları etkinleştirme ve devre dışı bırakma dahil olmak üzere yönetim konsolundan algılayıcıları yönetmeyi öğrenin.
ms.date: 12/07/2020
ms.topic: how-to
ms.openlocfilehash: 4706fbcf58b8e5f5eed6532f9a08f65c7716e07e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104781934"
---
# <a name="manage-sensors-from-the-management-console"></a>Yönetim konsolundan algılayıcıları yönetme

Bu makalede, aşağıdaki gibi yönetim konsolundan algılayıcıların nasıl yönetileceği açıklanmaktadır:

- Sistem ayarlarını sensörlerden gönder

- Algılayıcılar üzerinde altyapıları etkinleştirme ve devre dışı bırakma

- Algılayıcı sürümlerini güncelleştirme

## <a name="push-configurations"></a>Anında iletme yapılandırması

Çeşitli sistem ayarlarını tanımlayabilir ve bunları yönetim konsoluna bağlı sensörlerden otomatik olarak uygulayabilirsiniz. Bu, zamandan tasarruf sağlar ve kurumsal sensörlerinizin genelinde gelişmiş ayarların sağlanmasına yardımcı olur.

Aşağıdaki algılayıcı sistem ayarlarını yönetim konsolundan tanımlayabilirsiniz:

- Posta sunucusu

- SNMP MıB izleme

- Active Directory

- DNS ayarları

- Alt ağlar

- Bağlantı noktası diğer adları

Sistem ayarlarını uygulamak için:

1. Konsolun sol bölmesinde **sistem ayarları**' nı seçin.

2. **Algılayıcıları Yapılandır** bölmesinde seçeneklerden birini belirleyin.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-system-setting-options.png" alt-text="Bir algılayıcı için sistem ayarı seçenekleri.":::

   Aşağıdaki örnekte, kurumsal sensörleriniz için posta sunucusu parametrelerinin nasıl tanımlanacağı açıklanmaktadır.

3. **Posta sunucusu**' nu seçin.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-system-settings-screen.png" alt-text="Sistem ayarları ekranından posta sunucunuzu seçin.":::

4. Sol tarafta bir algılayıcı seçin.

5. Posta sunucusu parametrelerini ayarlayın ve **Çoğalt**' ı seçin. Algılayıcı ağacındaki her öğe, yanındaki onay kutusuyla birlikte görüntülenir.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/check-off-each-sensor.png" alt-text="Sensörlarınız için onay kutularının seçili olduğundan emin olun.":::

6. Algılayıcı ağacında, yapılandırmayı uygulamak istediğiniz öğeleri seçin.

7. **Kaydet**’i seçin.

## <a name="update-versions"></a>Güncelleştirme sürümleri

Aynı anda birden fazla algılayıcıları şirket içi yönetim konsolundan güncelleştirebilirsiniz.

Birkaç sensöri güncelleştirmek için:

1. [Azure Portal](https://portal.azure.com/) gidin.

2. IoT için Azure Defender 'a gidin.

3. **Güncelleştirmeler** sayfasına gidin.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/update-screen.png" alt-text="Güncelleştirmeler Pano görünümünün ekran görüntüsü.":::

4. **Sensör** bölümünden **İndir** ' i seçin ve dosyayı kaydedin.

5. Yönetim konsolunda oturum açın ve **sistem ayarları**' nı seçin.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/admin-system-settings.png" alt-text="Sistem ayarlarını seçmek için yönetim menüsünün ekran görüntüsü.":::

6. Güncelleştirme **algılayıcı altyapısı yapılandırma** bölümünde güncelleştirmek istediğiniz Algılayıcılar ' ı işaretleyin ve ardından **Otomatik Güncelleştirmeler**' i seçin.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensors-select.png" alt-text="Öğrenme modunu ve otomatik güncelleştirmeleri gösteren iki algılayıcı.":::

7. **Değişiklikleri Kaydet**' i seçin.

8. **Sensör sürümü yükseltme** bölmesinde, öğesini seçin :::image type="icon" source="media/how-to-manage-sensors-from-the-on-premises-management-console/plus-icon.png" border="false"::: .

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/display-files.png" alt-text="Dosyaları göstermek için algılayıcı sürümü yükseltme ekranı.":::

9. **Karşıya dosya yükle** iletişim kutusu açılır. **Güncelleştirmeler** sayfasından indirdiğiniz dosyayı karşıya yükleyin.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/upload-file.png" alt-text="Dosyanızı karşıya yüklemek için tarayıcı düğmesini seçin.":::

10. Güncelleştirme işlemi sırasında, her algılayıcının güncelleştirme durumu **site yönetimi** penceresinde görünür.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/progress.png" alt-text="Güncelleştirmelerinizin ilerlemesini gözlemleyin.":::

## <a name="update-threat-intelligence-packages"></a>Tehdit zekası paketlerini güncelleştirme 

Tehdit bilgileri için veri paketi, her bir IoT sürümü için yeni bir Defender ile veya yayınlar arasında gerekliyse sağlanır. Paket, imzaları (kötü amaçlı yazılım imzaları dahil), Ckları ve diğer güvenlik içeriğini içerir. 

Bu dosyayı, IoT portalının **güncelleştirmeler** sayfasından el ile karşıya yükleyebilir ve sensörlerden otomatik olarak güncelleştirebilirsiniz. 

Tehdit bilgileri verilerini güncelleştirmek için: 

1. IoT **güncelleştirmeleri** için Defender sayfasına gidin. 

2. Dosyayı indirin ve kaydedin.

3. Yönetim konsolunda oturum açın. 

4. Yan menüde **sistem ayarları**' nı seçin. 

5. **Algılayıcı altyapısı yapılandırma** bölümünde güncelleştirmeyi alması gereken Sensörlerinizi seçin.  

6. **Tehdit zekası verilerini seçin** bölümünde artı işaretini ( **+** ) seçin. 

7. IoT **güncelleştirmeleri** için Defender sayfasından indirdiğiniz paketi yükleyin.

## <a name="understand-sensor-disconnection-events"></a>Algılayıcı bağlantısının kesilmesi olaylarını anlama

Algılayıcıların atandığı şirket içi yönetim konsolundan bağlantısı kesildiğinde **Site Yöneticisi** penceresi, bağlantı kesme bilgilerini görüntüler. Aşağıdaki algılayıcı bağlantısının kesilmesi bilgileri kullanılabilir:

- "Şirket içi yönetim konsolu sensörden alınan verileri işleyemez."

- "Algılanan süreler. Şirket içi yönetim konsolunun sensörden bağlantısı kesildi. "

- "Algılayıcı, şirket içi yönetim konsolu ile iletişim kurmadı. Ağ bağlantısını veya sertifika doğrulamasını denetleyin. "

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-system-settings-screen.png" alt-text="Bölge 1 görünümünün ekran görüntüsü.":::

Bağlantısı kesilen algılayıcılar hakkında bilgi içeren üçüncü taraflara uyarı gönderebilirsiniz. Daha fazla bilgi için bkz. [algılayıcı hatası uyarılarını iletme](how-to-manage-individual-sensors.md#forward-sensor-failure-alerts).

## <a name="enable-or-disable-sensors"></a>Algılayıcıları etkinleştirme veya devre dışı bırakma

Algılayıcılar, IoT motorları için beş Defender tarafından korunur. Bağlı sensörler için altyapıları etkinleştirebilir veya devre dışı bırakabilirsiniz.

| Altyapı | Description | Örnek senaryo |
|--|--|--|
| Protokol ihlali altyapısı | Paket yapısı veya alan değerleri protokol belirtimine uymadığında protokol ihlali oluşur. | "Geçersiz MODBUS Işlemi (Işlev kodu sıfır)" uyarısı. Bu uyarı, birincil bir cihazın bir ikincil cihaza 0 işlev koduna sahip bir istek gönderdiğini gösterir. Bu, protokol belirtimine göre kullanılamaz ve ikincil cihaz girişi doğru şekilde işleyemeyebilir. |
| İlke ihlali altyapısı | Öğrenilen veya yapılandırılan ilkede tanımlanan taban çizgisi davranışından sapmayla bir ilke ihlali meydana gelir. | "Yetkisiz HTTP Kullanıcı Aracısı" uyarısı. Bu uyarı, ilke tarafından öğrenilen veya onaylanan bir uygulamanın bir cihazda HTTP istemcisi olarak kullanıldığını belirtir. Bu, bu cihazdaki yeni bir Web tarayıcısı veya uygulama olabilir. |
| Kötü amaçlı yazılım altyapısı | Kötü amaçlı yazılım altyapısı kötü amaçlı ağ etkinliğini algılar. | "Şüphesi of Kötü_niyetli Activity (Stuxnet)" uyarısı. Bu uyarı, sensörin, endüstriyel denetim ve SCADA ağlarını hedefleyen gelişmiş kalıcı bir tehdit olan Stuxnet kötü amaçlı yazılımlara ilişkin bilinen şüpheli ağ etkinliğini buldığını gösterir. |
| Anomali altyapısı | Kötü amaçlı yazılım altyapısı ağ davranışında bir anomali algılar. | "Iletişim kanalında düzenli davranış." Bu, ağ bağlantılarını denetleyen ve endüstri ağlarında ortak olan veri iletiminin düzenli veya döngüsel davranışını bulan bir bileşendir. |
| İşlemsel altyapı | Bu altyapı, işlemsel olayları veya hatalı çalışan varlıkları algılar. | `Device is Suspected to be Disconnected (Unresponsive)` daki. Bu uyarı, bir cihaz önceden tanımlanmış bir döneme yönelik isteklere yanıt vermediğinde tetiklenir. Bu, bir cihazın kapatılmasını, bağlantısının kesilmesi veya hatalı bir şekilde olduğunu gösteriyor olabilir.
|

Bağlı sensörlerden altyapıları etkinleştirmek veya devre dışı bırakmak için:

1. Konsolun sol bölmesinde **sistem ayarları**' nı seçin.

2. **Algılayıcı altyapısı yapılandırma** bölümünde altyapılar için **Etkinleştir** veya **devre dışı bırak** ' ı seçin.
         
3. **Değişiklikleri Kaydet**' i seçin.

   Kurumsal sensörlerinizin birinde etkinleştirilmiş altyapıların eşleşmemesi varsa kırmızı bir ünlem işareti görüntülenir. Motor doğrudan sensörden devre dışı bırakılmış olabilir.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/red-exclamation-example.png" alt-text="Etkin altyapıların uyumsuzluğu."::: 

## <a name="define-sensor-backup-schedules"></a>Algılayıcı yedekleme zamanlamalarını tanımlama

, Algılayıcı yedeklemeleri zamanlayabilir ve şirket içi yönetim konsolundan isteğe bağlı algılayıcı yedeklemeleri gerçekleştirebilirsiniz. Bu, sabit sürücü hatalarıyla ve veri kaybına karşı korunmaya yardımcı olur.

- Yedeklenen Özellikler: yapılandırma ve veriler.

- Yedeklenmeyen: PCAP dosyaları ve günlükleri. PCAPs ve günlükleri el ile yedekleyebilir ve geri yükleyebilirsiniz.

Varsayılan olarak, sensör günde 3:00 ' de otomatik olarak yedeklenir. Sensörler için yedekleme zamanlaması özelliği, bu yedeklemeleri toplayıp şirket içi yönetim konsolunda veya bir dış yedekleme sunucusunda depolamanıza olanak tanır. Dosyaların algılayıcılardan şirket içi yönetim konsoluna kopyalanması şifreli bir kanal üzerinden yapılır.

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-backup-schedule-screen.png" alt-text="Algılayıcı yedekleme ekranının görünümü.":::

Varsayılan algılayıcı yedekleme konumu değiştirildiğinde, şirket içi yönetim konsolu, konsolun konuma erişim izni olması şartıyla, dosyaları sensördeki yeni konumdan veya bir dış konuma otomatik olarak alır. 

Algılayıcılar şirket içi yönetim konsoluna kaydedilmediğinde, **algılayıcı yedekleme zamanlaması** iletişim kutusu, hiçbir sensörün yönetilmediğini belirtir.  

Geri yükleme işlemi, dosyaların nerede depolandığına bakılmaksızın aynıdır.

### <a name="backup-storage-for-sensors"></a>Algılayıcılar için yedekleme depolaması

Yedeklenen dosyaların ayrılan en fazla yedekleme alanını aşmaması kaydıyla, her bir yönetilen algılayıcı için en fazla dokuz yedeklemeyi korumak üzere şirket içi yönetim konsolunu kullanabilirsiniz. 

Kullanılabilir alan, çalışmakta olduğunuz yönetim konsolu modeli temel alınarak hesaplanır: 

- **Üretim modeli**: varsayılan depolama 40 GB; sınır 100 GB 'dir. 

- **Orta model**: varsayılan depolama 20 GB; sınır 50 GB 'dir. 

- **Dizüstü bilgisayar modeli**: varsayılan depolama alanı 10 GB; sınır 25 GB 'dir. 

- **İnce model**: varsayılan depolama 2 GB; sınır 4 GB 'tır. 

- **Rugged modeli**: varsayılan depolama alanı 10 GB; sınır 25 GB 'dir. 

Varsayılan ayırma, **algılayıcı yedekleme zamanlaması** iletişim kutusunda görüntülenir. 

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-mail-server-configuration.png" alt-text="Posta sunucusunu Düzenle yapılandırma ekranı.":::

Dış sunucuya yedekleme yaparken depolama sınırı yoktur. Ancak, **algılayıcı yedekleme zamanlaması**  >  **özel yolu** alanında bir üst ayırma sınırı tanımlamanız gerekir. Aşağıdaki sayılar ve karakterler desteklenir: `/, a-z, A-Z, 0-9, and _` . 

Ayırma depolama sınırlarını aşarak aşağıdaki bilgiler verilmiştir:

- Ayrılan depolama alanını aşarsanız, algılayıcı yedeklenmez. 

- Birden fazla algılayıcı yedekliyorsanız, Yönetim Konsolu yönetilen sensörlerle ilgili algılayıcı dosyalarını almaya çalışır.  

- Bir sensörden alma sınırı aşarsa, Yönetim Konsolu sonraki sensörden yedekleme bilgilerini almaya çalışır. 

Korunan yedeklemelerin sayısını aşarsanız, en eski yedeklenen dosya yeni bir dosyaya uyum sağlayacak şekilde silinir.

Algılayıcı yedekleme dosyaları, aşağıdaki biçimde otomatik olarak adlandırılır: `<sensor name>-backup-version-<version>-<date>.tar` . Örneğin: `Sensor_1-backup-version-2.6.0.102-2019-06-24_09:24:55.tar`. 

Algılayıcıları yedeklemek için:

1. **Sistem ayarları** penceresinden **algılayıcı yedeklemesini zamanla** ' yı seçin. Şirket içi yönetim konsolunuzun yönettiği **sensörler, algılayıcı yedekleme zamanlaması** iletişim kutusunda görünür.  

2. **Yedeklemeleri topla** geçiş özelliğini etkinleştirin.  

3. Bir takvim aralığı, tarih ve saat dilimi seçin. Zaman biçimi, 24 saatlik bir saati temel alır. Örneğin, 6:00 PM ' i **18:00** olarak girin. 

4. **Yedekleme depolama alanı ayırma** alanına, yedeklemeleriniz için ayırmak istediğiniz depolama alanını girin. En fazla alanı aşarsanız size bildirilir.

5. **Son sakla** alanında, sürdürmek istediğiniz algılayıcı başına yedeklemelerin sayısını belirtin. Sınır aşıldığında, en eski yedek silinir.  

6. Bir yedekleme konumu seçin:  

   - Şirket içi yönetim konsoluna yedeklemek için **özel yol** değiştirme 'yi devre dışı bırakın. Varsayılan konum `/var/cyberx/sensor-backups` .  

   - Bir dış sunucuya yedeklemek için **özel yol** değiştirmeyi etkinleştirin ve bir konum girin. Aşağıdaki sayılar ve karakterler desteklenir: `/, a-z, A-Z, 0-9, and, _` . 

7. **Kaydet**’i seçin. 

Hemen yedeklemek için: 

- **Şimdi Yedekle**' yi seçin. Şirket içi yönetim konsolu, algılayıcı yedekleme dosyalarını oluşturur ve toplar. 

### <a name="receiving-backup-notifications-for-sensors"></a>Sensörler için yedekleme bildirimleri alınıyor 

**Algılayıcı yedekleme zamanlaması** iletişim kutusu ve yedekleme günlüğü, yedekleme başarıları ve hatalarıyla ilgili bilgileri otomatik olarak listeler.  

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-location.png" alt-text="Sensörlerinizi ve bulundukları yerleri ve tüm ilgili bilgileri görüntüleyin.":::

Şu nedenle hatalarla karşılaşabilirsiniz:    

- Yedekleme dosyası bulunamadı. 

- Bir dosya bulundu, ancak alınamıyor.  

- Bir ağ bağlantı hatası var. 

- Şirket içi yönetim konsoluna yedeklemeyi tamamlamaya yetecek kadar yer yok.  

Bir hata oluştuğunda e-posta bildirimi, syslog güncelleştirmeleri ve sistem bildirimleri gönderebilirsiniz. Bunu yapmak için **sistem bildirimlerinde** bir iletme kuralı oluşturun. 

### <a name="restoring-sensors"></a>Algılayıcılar geri yükleniyor 

Yedeklemeleri şirket içi yönetim konsolundan ve CLı kullanarak geri yükleyebilirsiniz.  

Konsolundan geri yüklemek için: 

- **Algılayıcı sistem** ayarı penceresinde **görüntüyü geri yükle** ' yi seçin.

  :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/restore.png" alt-text="Görüntünüzün bir yedeğini geri yükleyin.":::

  Konsol sonra geri yükleme başarısızlıklarını görüntüler.  

CLı kullanarak geri yüklemek için: 

- Bir yönetim hesabında oturum açın ve girin `$ sudo cyberx-xsense-system-restore` . 

### <a name="save-a-sensor-backup-to-an-external-smb-server"></a>Bir algılayıcı yedeklemesini bir dış SMB sunucusuna kaydetme

Bir SMB sunucusu ayarlamak için, bir dış sürücüye bir algılayıcı yedeklemesi kaydedebilirsiniz: 

1. Dış SMB sunucusunda paylaşılan bir klasör oluşturun. 

2. SMB sunucusuna erişmek için gereken klasör yolunu, Kullanıcı adını ve parolayı alın. 

3. IoT için Defender 'da yedeklemeler için bir dizin oluşturun: 

   `sudo mkdir /<backup_folder_name_on_server>` 

   `sudo chmod 777 /<backup_folder_name_on_server>/` 

4. Fstab Düzenle:  

   `sudo nano /etc/fstab` 

   `add - //<server_IP>/<folder_path> /<backup_folder_name_on_cyberx_server> cifs rw,credentials=/etc/samba/user,vers=3.0,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0` 

5. Paylaşmak üzere kimlik bilgilerini düzenleyin veya oluşturun. SMB sunucusu için kimlik bilgileri şunlardır: 

   `sudo nano /etc/samba/user` 

6. Ekle:  

   `username=<user name>` 

   `password=<password>` 

7. Dizini bağlama: 

   `sudo mount -a` 

8. IoT algılayıcısı için Defender 'daki paylaşılan klasöre bir yedekleme dizini yapılandırın:  

   `sudo nano /var/cyberx/properties/backup.properties` 

9. `Backup.shared_location`Olarak ayarlayın `<backup_folder_name_on_cyberx_server>` .

## <a name="see-also"></a>Ayrıca bkz.

[Algılayıcıları ayrı ayrı yönetme](how-to-manage-individual-sensors.md)
