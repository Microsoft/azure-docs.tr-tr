---
title: IoT yüklemesi için Defender
description: IoT için Azure Defender için bir algılayıcı ve şirket içi yönetim konsolu yüklemeyi öğrenin.
ms.date: 4/20/2021
ms.topic: how-to
ms.openlocfilehash: e8366a3408e64d95e6c4d50e3ddef84309b4e8e5
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829742"
---
# <a name="defender-for-iot-installation"></a>IoT yüklemesi için Defender

Bu makalede IoT için Azure Defender 'ın aşağıdaki öğelerinin nasıl yükleneceği açıklanmaktadır:

- **Algılayıcı**: bir IoT algılayıcısı için Defender, pasif (aracısız) Izleme kullanarak ICS ağ trafiğini toplar. Pasif ve kesintisiz bir şekilde, sensörlerden, OT ve IoT ağları ve cihazları üzerinde sıfır etkisi vardır. Algılayıcı bir SPAN bağlantı noktasına veya ağa DOKUNMASıNA bağlanır ve ağınızı izlemeye hemen başlar. Algılamalar, algılayıcı konsolunda görünür. Burada, bunları bir ağ eşlemesinde, cihaz envanterinde ve kapsamlı bir rapor yelpazğunda görüntüleyebilir, araştırabilir ve çözümleyebilirsiniz. Risk değerlendirmesi raporlarını, veri araştırma sorgularını ve saldırı vektörlerini örnek olarak içerir. [IoT algılayıcı Kullanıcı Kılavuzu (doğrudan indirme) Için Defender](./getting-started.md)'daki algılayıcı özellikleri hakkında daha fazla bilgi edinin.

- Şirket içi **Yönetim Konsolu**: şirket içi yönetim konsolu cihaz yönetimi, risk yönetimi ve güvenlik açığı yönetimini gerçekleştirmenize olanak tanır. Ayrıca, kuruluşunuz genelinde tehdit izlemeyi ve olay yanıtını yürütmek için de kullanabilirsiniz. Algılayıcıların dağıtıldığı tesislerde algılanan tüm ağ cihazlarının, anahtar IoT ve OT risk göstergelerinin ve uyarıların birleştirilmiş bir görünümünü sağlar. Şirket içi yönetim konsolunu kullanarak, AIR-gapped ağlardaki algılayıcıları görüntüleyin ve yönetin.

Bu makalede aşağıdaki yükleme bilgileri ele alınmaktadır:

  - **Donanım:** Dell ve HPE fiziksel gereç ayrıntıları.

  - **Yazılım:** Algılayıcı ve şirket içi yönetim konsolu yazılım yüklemesi.

  - **Sanal gereçler:** Sanal makine ayrıntıları ve yazılım yükleme.

Yükleme sonrasında, sensörinizi ağınıza bağlayın.

## <a name="about-defender-for-iot-appliances"></a>IoT gereçlerine yönelik Defender hakkında 

Aşağıdaki bölümlerde IoT algılayıcı gereçlerine yönelik Defender ve IoT şirket içi yönetim konsolu için Defender gereci hakkında bilgi sağlanmaktadır.

### <a name="physical-appliances"></a>Fiziksel gereçler

IoT gereç algılayıcısı için Defender, bir SPAN bağlantı noktasına veya ağa DOKUNMASıNA bağlanır ve pasif (aracısız) izleme kullanarak ICS ağ trafiğini toplamaya başlar. Bu işlem, ağ ve cihazlarda, veri yoluna yerleştirilmediğinden ve cihazların etkin bir şekilde taranmadığı için sıfır etkiye sahiptir.

Aşağıdaki raf bağlama gereçlerine erişilebilir:

| **Dağıtım türü** | **Kurumsal** | **Kurumsal** | **SMB** | **Çizgi** |
|--|--|--|--|--|
| **Modelleme** | HPE ProLiant DL360 | Dell PowerEdge R340 XL | HPE ProLiant DL20 | HPE ProLiant DL20 |
| **İzleme bağlantı noktaları** | en fazla 15 RJ45 veya 8 OPT | 9 RJ45 veya 6 OPT 'a kadar | en fazla 8 RJ45 veya 6 OPT | 4 RJ45 |
| **Maksimum bant genişliği\*** | 3 GB/sn | 1 GB/sn | 1 GB/sn | 100 MB/sn |
| **En fazla korumalı cihaz** | 30.000 | 10,000 | 15.000 | 1.000 |

* En yüksek bant genişliği kapasitesi, protokol dağıtımına bağlı olarak farklılık gösterebilir.

### <a name="virtual-appliances"></a>Sanal gereçler

Aşağıdaki sanal gereçler mevcuttur:

| **Dağıtım türü** | **Kurumsal** | **SMB** | **Çizgi** |
|--|--|--|--|
| **Açıklama** | Kurumsal dağıtımlar için Sanal Gereç | SMB dağıtımları için Sanal Gereç | Hat dağıtımları için Sanal Gereç |
| **Maksimum bant genişliği\*** | 150 MB/sn | 15 MB/sn | 3 MB/sn |
| **En fazla korumalı cihaz** | 3.000 | 300 | 100 |
| **Dağıtım türü** | Kurumsal | SMB | Çizgi |
| **Açıklama** | Kurumsal dağıtımlar için Sanal Gereç | SMB dağıtımları için Sanal Gereç | Hat dağıtımları için Sanal Gereç |

* En yüksek bant genişliği kapasitesi, protokol dağıtımına bağlı olarak farklılık gösterebilir.

### <a name="hardware-specifications-for-the-on-premises-management-console"></a>Şirket içi yönetim konsolu için donanım belirtimleri

 | Öğe | Açıklama |
 |----|--|
 **Açıklama** | Çok katmanlı bir mimaride, şirket içi yönetim konsolu coğrafi olarak dağıtılan sitelerde görünürlük ve denetim sağlar. Sıems, Bilet oluşturma sistemleri, yeni nesil güvenlik duvarları, güvenli uzaktan erişim platformları ve IoT ICS kötü amaçlı yazılım korumalı alanı için Defender dahil olmak üzere SOC güvenlik yığınları ile tümleşir. |
 **Dağıtım türü** | Kurumsal |
 **Gereç türü**  | Dell R340, VM |
 **Yönetilen algılayıcı sayısı** | Sınırsız |

## <a name="prepare-for-the-installation"></a>Yüklemeye hazırlanma

### <a name="access-the-iso-installation-image"></a>ISO yükleme görüntüsüne erişin

Yükleme görüntüsüne IoT portalı için Defender 'dan erişilebilir.

Dosyaya erişmek için:

1. IoT hesabınız için Defender ' da oturum açın.

1. **Ağ algılayıcısı** veya **Şirket içi yönetim konsolu** sayfasına gidin ve indirilecek bir sürüm seçin.

### <a name="install-from-dvd"></a>DVD 'den yüklensin

Yüklemeden önce şunları doğrulayın:

- USB bağlayıcısıyla taşınabilir bir DVD sürücüsü.

- Bir ISO yükleyicisi görüntüsü.

Yüklemek için:

1. Görüntüyü bir DVD 'ye yazın veya bir anahtar üzerinde disk hazırlayın. Bilgisayarınıza taşınabilir bir DVD sürücüsü bağlayın, ISO görüntüsünü sağ tıklatın ve **diske yaz**' ı seçin.

1. DVD veya diski bir anahtara bağlayın ve gereci bir anahtardaki DVD 'den veya diskten önyüklenecek şekilde yapılandırın.

### <a name="install-from-disk-on-a-key"></a>Bir anahtardaki diskten yüklemesi

Yüklemeden önce şunları doğrulayın:

  - Rufus yüklendi.
  
  - USB sürüm 3,0 ve üzeri ile anahtardaki bir disk. En küçük boyut 4 GB 'tır.

  - Bir ISO yükleyicisi görüntü dosyası.

Bu işlemde bir anahtardaki disk silinir.

Bir anahtara disk hazırlamak için:

1. Rufus ' i çalıştırın ve **ALGıLAYıCı ISO**' yı seçin.

1. Bir anahtardaki diski ön panele bağlayın.

1. Sunucunun BIOS 'UNU USB 'den önyüklenecek şekilde ayarlayın.

## <a name="dell-poweredger340xl-installation"></a>Dell PowerEdgeR340XL yüklemesi

Yazılımı Dell gerecine yüklemeden önce, gerecin BIOS yapılandırmasını ayarlamanız gerekir:

  - [Dell PowerEdge R340 ön paneli](#dell-poweredge-r340-front-panel) ve [Dell PowerEdge R340 Back panel](#dell-poweredge-r340-back-panel) , sürücüler ve bağlantı noktaları gibi yükleme için gereken bilgilerin yanı sıra ön ve arka panellerin açıklamasını içerir.

  - [Dell BIOS yapılandırması](#dell-bios-configuration) , Dell gereç yönetimi arabirimine bağlanma ve BIOS 'u yapılandırma hakkında bilgi sağlar.

  - [Yazılım yükleme (Dell R340)](#software-installation-dell-r340) , IoT algılayıcı yazılımını Defender 'ı yüklemek için gereken yordamı açıklar.

### <a name="dell-poweredge-r340xl-requirements"></a>Dell PowerEdge R340XL gereksinimleri 

Dell PowerEdge R340XL gerecini yüklemek için şunlar gerekir:

- Dell Uzaktan erişim denetleyicisi (Idrac) için Kurumsal Lisans

- BIOS yapılandırması XML

- Sunucu üretici yazılımı sürümleri:

  - BIOS sürümü 2.1.6

  - Idrac sürümü 3.23.23.23

### <a name="dell-poweredge-r340-front-panel"></a>Dell PowerEdge R340 ön paneli

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-front-panel.jpg" alt-text="Dell PowerEdge R340 ön paneli.":::

 1. Sol Denetim bölmesi 
 1. Optik sürücü (isteğe bağlı) 
 1. Sağ Denetim Masası 
 1. Bilgi etiketi 
 1. Sürücüler  

### <a name="dell-poweredge-r340-back-panel"></a>Dell PowerEdge R340 geri paneli

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-back-panel.jpg" alt-text="Dell PowerEdge R340 geri paneli.":::

1. Seri bağlantı noktası 
1. NIC bağlantı noktası (GB 1) 
1. NIC bağlantı noktası (GB 1) 
1. Yarım yükseklikte PCIe 
1. Tam yükseklikte PCIe genişletme kartı yuvası 
1. Güç kaynağı birimi 1 
1. Güç kaynağı birimi 2 
1. Sistem kimliği 
1. Sistem durumu göstergesi kablosu bağlantı noktası (CMA) düğmesi 
1. USB 3,0 bağlantı noktası (2) 
1. iDRAC9 adanmış ağ bağlantı noktası 
1. VGA bağlantı noktası 

### <a name="dell-bios-configuration"></a>Dell BIOS yapılandırması

Dell gerecini yazılımla çalışacak şekilde ayarlamak için Dell BIOS yapılandırması gerekir.

BIOS yapılandırması önceden tanımlanmış bir yapılandırma aracılığıyla gerçekleştirilir. Dosyaya [Yardım Merkezi](https://help.cyberx-labs.com/)'nden erişilebilir.

Yapılandırma dosyasını Dell gereçine aktarın. Yapılandırma dosyasını kullanmadan önce, Dell gereci ve yönetim bilgisayarı arasındaki iletişimi oluşturmanız gerekir.

Dell gereci, yaşam döngüsü denetleyicisi (LC) ile tümleşik bir Idrac tarafından yönetilir. LC, her Dell PowerEdge sunucusuna katıştırılır ve Dell PowerEdge gereçlerinizi dağıtmanıza, güncelleştirmenize, izlemenize ve sürdürmenize yardımcı olan işlevler sağlar.

Dell gereci ile yönetim bilgisayarı arasında iletişim kurmak için, aynı alt ağdaki Idrac IP adresini ve yönetim bilgisayarının IP adresini tanımlamanız gerekir.

Bağlantı oluşturulduğunda BIOS yapılandırılabilir.

Dell BIOS 'UNU yapılandırmak için:

1. [Idrac IP adresini yapılandırma](#configure-idrac-ip-address)

1. [BIOS yapılandırma dosyasını içeri aktarma](#import-the-bios-configuration-file)

#### <a name="configure-idrac-ip-address"></a>Idrac IP adresini yapılandır

1. Algılayıcısı güçlendirin.

1. İşletim sistemi zaten yüklüyse, BIOS yapılandırmasını girmek için F2 tuşunu seçin.

1. **Idrac ayarları**' nı seçin.

1. **Ağ**' ı seçin.

   > [!NOTE]
   > Yükleme sırasında, aşağıdaki adımlarda bahsedilen varsayılan Idrac IP adresini ve parolasını yapılandırmanız gerekir. Yükleme sonrasında, bu tanımları değiştirirsiniz.

1. Statik IPv4 adresini **10.100.100.250** olarak değiştirin.

1. Statik alt ağ maskesini **255.255.255.0** olarak değiştirin.

   :::image type="content" source="media/tutorial-install-components/idrac-network-settings-screen-v2.png" alt-text="Statik alt ağ maskesini gösteren ekran görüntüsü.":::

1. **Arka**  >  **son**' u seçin.

#### <a name="import-the-bios-configuration-file"></a>BIOS yapılandırma dosyasını içeri aktarma

Bu makalede yapılandırma dosyası kullanılarak BIOS 'un nasıl yapılandırılacağı açıklanır.

1. **Idrac** bağlantı noktasına **10.100.100.200** statik önceden yapılandırılmış IP adresine sahip bir bilgisayar takın.

   :::image type="content" source="media/tutorial-install-components/idrac-port.png" alt-text="Önceden yapılandırılmış IP adresi bağlantı noktasının ekran görüntüsü.":::

1. Bir tarayıcı açın ve Idrac Web arabirimine bağlanmak için **10.100.100.250** girin.

1. Dell varsayılan yönetici ayrıcalıklarıyla oturum açın:

   - Kullanıcı adı: **kök**

   - Parola: **Calvin**

1. Gereç kimlik bilgileri şunlardır:

   - Kullanıcı adı: **XXX**

   - Parola: **XXX**

     Sunucu profilini içeri aktarma işlemi başlatıldı.

     > [!NOTE]
     > Dosyayı içeri aktarmadan önce şunları yaptığınızdan emin olun:
     > - Şu anda Idrac 'ye bağlı olan tek kullanıcı olursunuz.
     > - Sistem BIOS menüsünde değil.

1. **Yapılandırma**  >  **sunucusu yapılandırma profiline** gidin. Aşağıdaki parametreleri ayarlayın:

   :::image type="content" source="media/tutorial-install-components/configuration-screen.png" alt-text="Sunucu profilinizin yapılandırmasını gösteren ekran görüntüsü.":::

   | Parametre | Yapılandırma |
   |--|--|
   | Konum türü | **Yerel**' i seçin. |
   | Dosya yolu | **Dosya Seç** ' i seçin ve yapılandırma XML dosyasını ekleyin. |
   | Bileşenleri içeri aktar | **BIOS, NIC, RAID '** i seçin. |
   | En fazla bekleme süresi | **20 dakika** seçin. |

1. **İçeri aktar**'ı seçin.

1. İşlemi izlemek için **bakım**  >  **işi kuyruğu**' na gidin.

   :::image type="content" source="media/tutorial-install-components/view-the-job-queue.png" alt-text="Iş kuyruğunu gösteren ekran görüntüsü.":::

#### <a name="manually-configuring-bios"></a>BIOS 'U el ile yapılandırma 

Şu durumlarda gereç BIOS 'UNU el ile yapılandırmanız gerekir:

- Gerecinizi ok 'ten satın almadınız.

- Bir gereç var, ancak XML yapılandırma dosyasına erişiminiz yok.

BIOS 'a erişduktan sonra **cihaz ayarları**' na gidin.

El ile yapılandırmak için:

1. Bir klavye ve ekran kullanarak gereç BIOS 'UNA doğrudan erişin veya Idrac kullanın.

   - Gereç, IoT gereci için bir Defender değilse, bir tarayıcı açın ve daha önce yapılandırılan IP adresine gidin. Dell varsayılan yönetici ayrıcalıklarıyla oturum açın. Parola için Kullanıcı adı ve **Calvin** **kökünü** kullanın.

   - Gereç, IoT gereci için bir Defender ise Kullanıcı adı için **XXX** ve parola için **XXX** kullanarak oturum açın.

1. BIOS 'a erişduktan sonra **cihaz ayarları**' na gidin.

1. **TÜMLEŞIK RAID denetleyicisi 1: Dell PERC \<PERC H330 Adapter\> Configuration yardımcı programını** seçerek RAID denetimli yapılandırmayı seçin.

1. **Yapılandırma yönetimi**' ni seçin.

1. **Sanal disk oluştur**' u seçin.

1. **RAID düzeyi seç** alanında **RAID5**' yi seçin. **Sanal disk adı** alanında **kök** girin ve **fiziksel diskler**' i seçin.

1. **Tümünü işaretle** ' yi seçin ve sonra **Değişiklikleri Uygula** ' yı seçin

1. **Tamam**'ı seçin.

1. Aşağı kaydırın ve **sanal disk oluştur**' u seçin.

1. **Onayla** onay kutusunu seçin ve **Evet**' i seçin.

1. **Tamam**’ı seçin.

1. Ana ekrana dönün ve **SISTEM BIOS**' u seçin.

1. **Önyükleme ayarları**' nı seçin.

1. **Önyükleme modu** seçeneği için **BIOS**' u seçin.

1. **Geri**'yi seçin ve ardından **son** ' u seçerek BIOS ayarlarından çıkın.

### <a name="software-installation-dell-r340"></a>Yazılım yükleme (Dell R340)

Yükleme işlemi yaklaşık 20 dakika sürer. Yükleme sonrasında, sistem birkaç kez yeniden başlatılır.

Yüklemek için:

1. Sürüm medyasının, aşağıdaki yollarla bir gereç ile bağlandığından emin olun:

   - Dış CD 'yi veya diski bir anahtarla bir anahtara bağlayın.

   - Idrac kullanarak ISO görüntüsünü bağlayın. Idrac 'de oturum açtıktan sonra sanal konsolu seçin ve ardından **sanal medya**' yı seçin.

1. **CD/DVD eşleme** bölümünde **Dosya Seç**' i seçin.

1. Açılan iletişim kutusundan bu sürüm için sürüm ISO görüntü dosyasını seçin.

1. **Cihazı eşle** düğmesini seçin.

   :::image type="content" source="media/tutorial-install-components/mapped-device-on-virtual-media-screen-v2.png" alt-text="Eşlenmiş bir cihazı gösteren ekran görüntüsü.":::

1. Medya bağlandı. **Kapat**’ı seçin.

1. Gereci başlatın. Idrac 'yi kullanırken, **Tüketil denetim** düğmesini seçerek sunucuları yeniden başlatabilirsiniz. Ardından, **klavye makrolarında**, CTRL + ALT + DELETE dizisini başlatacak olan **Uygula** düğmesini seçin.

1. **İngilizce**'yi seçin.

1. **Algılayıcı-yayın- \<version\> kuruluş**' u seçin.

   :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Sensör sürümünüzü ve kuruluş türünü seçin.":::   

1. Gereç profilini ve ağ özelliklerini tanımlayın:

   :::image type="content" source="media/tutorial-install-components/appliance-profile-screen-v2.png" alt-text="Gereç profilini ve ağ özelliklerini gösteren ekran görüntüsü.":::   

   | Parametre | Yapılandırma |
   |--|--|
   | **Donanım profili** | **Kuruluşunuzdaki** |
   | **Yönetim arabirimi** | **eno1** |
   | **Ağ parametreleri (müşteri tarafından verilir)** | - |
   |**Yönetim ağı IP adresi:** | - |
   | **alt ağ maskesi:** | - |
   | **Gereç konak adı:** | - |
   | **BKZ** | - |
   | **Varsayılan ağ geçidi IP adresi:** | - |
   | **Giriş arabirimleri:** |  Sistem sizin için giriş arabirimleri listesini oluşturur. Giriş arabirimlerini yansıtmak için, listede sunulan tüm öğeleri bir virgül ayırıcısıyla kopyalayın. Köprü arabirimini yapılandırmak zorunda değilsiniz. Bu seçenek yalnızca özel kullanım durumları için kullanılır. |

1. 10 dakika sonra, iki kimlik bilgileri kümesi görüntülenir. Biri bir **Six** kullanıcısına, diğeri ise **destek** kullanıcısına yöneliktir.  

1. Gereç KIMLIĞI ve parolaları 'nı kaydedin. Bu kimlik bilgilerine, ilk kez kullandığınızda platforma erişmeniz gerekir.

1. Devam etmek için **ENTER** ' u seçin.

## <a name="hpe-proliant-dl20-installation"></a>HPE ProLiant DL20 yüklemesi

Bu makalede, aşağıdaki adımları içeren HPE ProLiant DL20 yükleme işlemi açıklanmaktadır:

  - Uzaktan erişimi etkinleştirin ve varsayılan yönetici parolasını güncelleştirin.
  - BIOS ve RAID ayarlarını yapılandırın.
  - Yazılımı yükler.

### <a name="about-the-installation"></a>Yükleme hakkında

  - Kurumsal ve SMB Araçları yüklenebilir. Yükleme işlemi, dizi yapılandırması hariç her iki gereç türü için aynıdır.
  - Varsayılan bir yönetici kullanıcı sağlanır. Ağ yapılandırma işlemi sırasında parolayı değiştirmenizi öneririz.
  - Ağ yapılandırma işlemi sırasında, ağ bağlantı noktası 1 ' de ILO bağlantı noktasını yapılandıracaksınız.
  - Yükleme işlemi yaklaşık 20 dakika sürer. Yükleme sonrasında, sistem birkaç kez yeniden başlatılır.

### <a name="hpe-proliant-dl20-front-panel"></a>HPE ProLiant DL20 ön paneli

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-front-panel-v2.png" alt-text="HPE ProLiant DL20 ön paneli.":::

### <a name="hpe-proliant-dl20-back-panel"></a>HPE ProLiant DL20 Back paneli

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-back-panel-v2.png" alt-text="HPE ProLiant DL20 'ın geri paneli.":::

### <a name="enable-remote-access-and-update-the-password"></a>Uzaktan erişimi etkinleştirme ve parolayı güncelleştirme

Ağ seçeneklerini ayarlamak ve varsayılan parolayı güncelleştirmek için aşağıdaki yordamı kullanın.

Parolayı etkinleştirmek ve güncelleştirmek için:

1. HP gerecine bir ekran ve klavye bağlayın, gereci açın ve **F9** tuşuna basın.

    :::image type="content" source="media/tutorial-install-components/hpe-proliant-screen-v2.png" alt-text="HPE ProLiant penceresini gösteren ekran görüntüsü.":::

1. **Sistem yardımcı programları**  >  **sistem yapılandırması**  >  **ILO 5 yapılandırma yardımcı programı**  >  **ağ seçenekleri**' ne gidin.

    :::image type="content" source="media/tutorial-install-components/system-configuration-window-v2.png" alt-text="Sistem yapılandırma penceresini gösteren ekran görüntüsü.":::

    1.  **Ağ arabirimi bağdaştırıcısı** alanından **paylaşılan ağ bağlantı noktası-Lom** öğesini seçin.
    
    1.  DHCP 'yi devre dışı bırakın.
    
    1.  IP adresi, alt ağ maskesi ve ağ geçidi IP adresini girin.

1. **F10: Kaydet**' i seçin.

1. **ILO 5 yapılandırma yardımcı programına** dönmek için **ESC** 'Yi seçin ve ardından **Kullanıcı yönetimi**'ni seçin.

1. **Kullanıcı Düzenle/kaldır**' ı seçin. Yönetici varsayılan kullanıcı tanımlı ' dır. 

1. Varsayılan parolayı değiştirin ve **F10: Kaydet**' i seçin.

### <a name="configure-the-hpe-bios"></a>HPE BIOS 'UNU yapılandırma

Aşağıdaki yordamda, kurumsal ve SMB gereçlerinin HPE BIOS 'unun nasıl yapılandırılacağı açıklanmaktadır.

HPE BIOS 'UNU yapılandırmak için:

1. **Sistem yardımcı programları**  >  **sistem yapılandırması**  >  **BIOS/platform yapılandırması (rbsu)** seçeneğini belirleyin.

1. **BIOS/platform yapılandırması (RBSU)** formunda **önyükleme seçenekleri**' ni seçin.

1. **Önyükleme modunu** **Eski BIOS moduna** değiştirin ve ardından **F10: Kaydet**' i seçin.

1. **Sistem yapılandırma** formunu kapatmak Için **ESC tuşuna** iki kez seçin.

#### <a name="for-the-enterprise-appliance"></a>Kurumsal gereç için

1. **Gömülü RAID 1: HPE akıllı dizi P408i-bir SR gen 10**  >  **dizi yapılandırması**  >  **dizisi oluştur**' u seçin.

1. **Dizi oluştur** formunda, tüm seçenekleri seçin. **Kurumsal** gereç için üç seçenek mevcuttur.

#### <a name="for-the-smb-appliance"></a>SMB gereci için

1. **Gömülü RAID 1: HPE akıllı dizi P208i-bir SR gen 10**  >  **dizi yapılandırması**  >  **dizisi oluştur**' u seçin.

1. **Sonraki forma devam et**' i seçin.

1. **RAID düzeyini ayarla** formunda, kurumsal dağıtımlar Için düzeyi **RAID 5** ve SMB dağıtımları için **RAID 1** olarak ayarlayın.

1. **Sonraki forma devam et**' i seçin.

1. **Mantıksal sürücü etiketi** formunda, **mantıksal sürücü 1** girin.

1. **Değişiklikleri Gönder**' i seçin.

1. **Gönder** formunda, **ana menüye dön**' ü seçin.

1. **F10: Kaydet** ' i seçin ve ardından **ESC** tuşuna iki kez basın.

1. **Sistem yardımcı programları** penceresinde, **bir kerelik önyükleme menüsü**' nü seçin.

1. **Tek seferlik önyükleme menüsü** formunda **Eski BIOS One-Time önyükleme menüsü**' nü seçin.

1. Eski ve **önyükleme geçersiz kılma** pencerelerinin **önyüklemesi** görüntülenir. Önyükleme geçersiz kılma seçeneğini belirleyin; Örneğin, bir CD-ROM, USB, HDD veya UEFı kabuğu.

    :::image type="content" source="media/tutorial-install-components/boot-override-window-one-v2.png" alt-text="İlk önyükleme geçersiz kılma penceresini gösteren ekran görüntüsü.":::

    :::image type="content" source="media/tutorial-install-components/boot-override-window-two-v2.png" alt-text="İkinci önyükleme geçersiz kılma penceresini gösteren ekran görüntüsü.":::
### <a name="software-installation-hpe-proliant-dl20-appliance"></a>Yazılım yükleme (HPE ProLiant DL20 gereci)

Yükleme işlemi yaklaşık 20 dakika sürer. Yükleme sonrasında, sistem birkaç kez yeniden başlatılır.

Yazılımı yüklemek için:

1. Ekran ve klavyeyi gereç 'ya bağlayın ve ardından CLı 'ya bağlanın.

1. IoT portalı için Defender 'daki **güncelleştirmeler** SAYFASıNDAN indirdiğiniz ISO görüntüsüyle anahtardaki BIR dış CD veya diski bağlayın.

1. Gereci başlatın.

1. **İngilizce**'yi seçin.

    :::image type="content" source="media/tutorial-install-components/select-english-screen.png" alt-text="CLı penceresinde Ingilizce seçimi.":::

1. **Algılayıcı-yayın- <version> kuruluş**' u seçin.

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Sürüm seçme ekranının ekran görüntüsü.":::

1. Yükleme sihirbazında, donanım profilini ve ağ özelliklerini tanımlayın:

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="Yükleme Sihirbazı 'nı gösteren ekran görüntüsü.":::

    | Parametre | Yapılandırma |
    | ----------| ------------- |
    | **Donanım profili** | SMB dağıtımları için **Kurumsal** veya **Office** ' i seçin. |
    | **Yönetim arabirimi** | **eno2** |
    | **Varsayılan ağ parametreleri (genellikle parametreler müşteri tarafından sağlanır)** | **Yönetim ağı IP adresi:** <br/> <br/>**Gereç konak adı:** <br/>**BKZ** <br/>**Varsayılan ağ geçidi IP adresi:**|
    | **Giriş arabirimleri:** | Sistem sizin için giriş arabirimleri listesini oluşturur.<br/><br/>Giriş arabirimlerini yansıtmak için, listede sunulan tüm öğeleri bir virgül ayırıcısıyla kopyalayın: **eno5, eno3, eno1, eno6, eno4**<br/><br/>**HPE DL20 için: eno1, enp1s0f4u4 (ILO arabirimlerini) listeleme**<br/><br/>**Köprü**: köprü arabirimini yapılandırmaya gerek yoktur. Bu seçenek yalnızca özel kullanım durumları için kullanılır. Devam etmek için **Enter** tuşuna basın. |

1. 10 dakika sonra, iki kimlik bilgileri kümesi görüntülenir. Biri bir **Six** kullanıcısına, diğeri ise **destek** kullanıcısına yöneliktir.

1. Gereç KIMLIĞINI ve parolalarını kaydedin. Platforma ilk kez erişmek için kimlik bilgileri gerekir.

1. Devam etmek için **ENTER** ' u seçin.

## <a name="hpe-proliant-dl360-installation"></a>HPE ProLiant DL360 yüklemesi

  - Varsayılan bir yönetici kullanıcı sağlanır. Ağ yapılandırması sırasında parolayı değiştirmenizi öneririz.

  - Ağ yapılandırması sırasında ILO bağlantı noktasını yapılandıracaksınız.

  - Yükleme işlemi yaklaşık 20 dakika sürer. Yükleme sonrasında, sistem birkaç kez yeniden başlatılır.

### <a name="hpe-proliant-dl360-front-panel"></a>HPE ProLiant DL360 ön paneli

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-front-panel.png" alt-text="HPE ProLiant DL360 ön paneli.":::

### <a name="hpe-proliant-dl360-back-panel"></a>HPE ProLiant DL360 Back paneli

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-back-panel.png" alt-text="HPE ProLiant DL360 Back paneli.":::

### <a name="enable-remote-access-and-update-the-password"></a>Uzaktan erişimi etkinleştirme ve parolayı güncelleştirme

HPE ProLiant DL20 yüklemesi için önceki bölümlere bakın:

  - "Uzaktan erişimi etkinleştir ve parolayı Güncelleştir"

  - "HPE BIOS 'U yapılandırma"

Kuruluş yapılandırması aynıdır.

> [!Note]
> Dizi formunda, tüm seçenekleri seçdiğinizi doğrulayın.

### <a name="ilo-remote-installation-from-a-virtual-drive"></a>ILO uzaktan yükleme (sanal sürücüden)

Bu yordamda, bir sanal sürücüden ILO yüklemesi açıklanmaktadır.

Yüklemek için:

1. ILO konsolunda oturum açın ve sonra sunucular ekranına sağ tıklayın.

1. **HTML5 konsolunu** seçin.

1. Konsolunda, CD simgesini seçin ve CD/DVD seçeneğini belirleyin.

1. **Yerel ISO dosyası**' nı seçin.

1. İletişim kutusunda ilgili ISO dosyasını seçin.

1. Sol simgesine gidin, **Güç**' i ve **sıfırlama** Seç ' i seçin.

1. Gereç yeniden başlatılır ve algılayıcı yükleme işlemini çalıştırır.

### <a name="software-installation-hpe-dl360"></a>Yazılım yükleme (HPE DL360)

Yükleme işlemi yaklaşık 20 dakika sürer. Yükleme sonrasında, sistem birkaç kez yeniden başlatılır.

Yüklemek için:

1. Ekran ve klavyeyi gereç 'ya bağlayın ve ardından CLı 'ya bağlanın.

1. IoT portalı için Defender 'daki **güncelleştirmeler** SAYFASıNDAN indirdiğiniz ISO görüntüsünü içeren bir anahtara harici CD veya disk bağlayın.

1. Gereci başlatın.

1. **İngilizce**'yi seçin.

1. **Algılayıcı-yayın- <version> kuruluş**' u seçin.

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Sürümün seçildiğinin gösterildiği ekran görüntüsü.":::

1. Yükleme sihirbazında gereç profilini ve ağ özelliklerini tanımlayın.

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="Yükleme Sihirbazı 'nı gösteren ekran görüntüsü.":::

    | Parametre | Yapılandırma |
    | ----------| ------------- |
    | **Donanım profili** | **Şirket**' i seçin. |
    | **Yönetim arabirimi** | **eno2** |
    | **Varsayılan ağ parametreleri (müşteri tarafından verilir)** | **Yönetim ağı IP adresi:** <br/>**alt ağ maskesi:** <br/>**Gereç konak adı:** <br/>**BKZ** <br/>**Varsayılan ağ geçidi IP adresi:**|
    | **Giriş arabirimleri:**  | Sistem sizin için giriş arabirimlerinin bir listesini oluşturur.<br/><br/>Giriş arabirimlerini yansıtmak için, listede sunulan tüm öğeleri bir virgül ayırıcısıyla kopyalayın.<br/><br/> Köprü arabirimini yapılandırmanız gerekmez. Bu seçenek yalnızca özel kullanım durumları için kullanılır. |

1. 10 dakika sonra, iki kimlik bilgileri kümesi görüntülenir. Biri bir **Six** kullanıcısına, diğeri ise **destek** kullanıcısına yöneliktir.

1. Gereç KIMLIĞINI ve parolalarını kaydedin. Platforma ilk kez erişmek için bu kimlik bilgilerine ihtiyacınız olacak.

1. Devam etmek için **ENTER** ' u seçin.

## <a name="sensor-installation-for-the-virtual-appliance"></a>Sanal Gereç için algılayıcı yüklemesi

Aşağıdaki mimarilerde IoT için Defender algılayıcısı için sanal makineyi dağıtabilirsiniz:


| Mimari | Belirtimler | Kullanım | Yorumlar |
|---|---|---|---|
| **Kurumsal** | CPU: 8<br/>Bellek: 32G RAM<br/>HDD: 1800 GB | Üretim ortamı | Varsayılan ve en yaygın |
| **Küçük Işletme** | CPU: 4 <br/>Bellek: 8G RAM<br/>HDD: 500 GB | Test veya küçük üretim ortamları | -  |
| **Office** | CPU: 4<br/>Bellek: 8G RAM<br/>HDD: 100 GB | Küçük test ortamları | -  |

### <a name="prerequisites"></a>Önkoşullar

Şirket içi yönetim konsolu hem VMware hem de Hyper-V dağıtım seçeneklerini destekler. Yüklemeye başlamadan önce aşağıdaki öğelere sahip olduğunuzdan emin olun:

  - VMware (ESXi 5,5 veya üzeri) veya Hyper-V Hiper Yöneticisi (Windows 10 Pro veya Enterprise) yüklü ve çalışır durumda

  - Sanal makine için kullanılabilir donanım kaynakları

  - IoT algılayıcısı için Azure Defender ISO yükleme dosyası

Hiper yöneticinin çalıştığından emin olun.

### <a name="create-the-virtual-machine-esxi"></a>Sanal makineyi (ESXi) oluşturma

1. ESXi 'de oturum açın, ilgili **veri deposunu** seçin ve **veri deposu tarayıcısı**' nı seçin.

1. Resmi **karşıya yükleyin** ve **Kapat**' ı seçin.

1. **Sanal makineler**' e gidin ve ardından **VM oluştur/kaydol**' u seçin.

1. **Yeni sanal makine oluştur**' u seçin ve ardından **İleri**' yi seçin.

1. Bir algılayıcı adı ekleyin ve şunları seçin:

   - Uyumluluk: **&lt; en son ESXi &gt; sürümü**

   - Konuk işletim sistemi ailesi: **Linux**

   - Konuk işletim sistemi sürümü: **Ubuntu Linux (64-bit)**

1. **İleri**’yi seçin.

1. İlgili veri deposunu seçin ve **İleri ' yi** seçin.

1. Sanal donanım parametrelerini gereken mimariye göre değiştirin.

1. **CD/DVD sürücüsü 1** Için, **veri deposu ISO dosyası** ' nı seçin ve daha önce karşıya yüklediğiniz ISO dosyasını seçin.

1. **Sonraki** > **Son** seçeneğini belirleyin.

### <a name="create-the-virtual-machine-hyper-v"></a>Sanal makineyi (Hyper-V) oluşturma

Bu yordam, Hyper-V kullanarak bir sanal makinenin nasıl oluşturulduğunu açıklar.

Bir sanal makine oluşturmak için:

1. Hyper-V Yöneticisi 'nde sanal disk oluşturun.

1. **Format = VHDX** seçin.

1. **Tür = dinamik genişleyen** öğesini seçin.

1. VHD için ad ve konum girin.

1. Gerekli boyutu girin (mimariye göre).   

1. Özeti gözden geçirin ve **son**' u seçin.

1. **Eylemler** menüsünde Yeni bir sanal makine oluşturun.

1. Sanal makine için bir ad girin.

1. **Kuşak** üretimi 1 belirtin ' i seçin  >  .

1. Bellek ayırmayı (mimariye göre) belirtin ve dinamik bellek onay kutusunu seçin.

1. Ağ bağdaştırıcısını sunucu ağ topolojinize göre yapılandırın.

1. Daha önce oluşturulan VHDX 'i sanal makineye bağlayın.

1. Özeti gözden geçirin ve **son**' u seçin.

1. Yeni sanal makineye sağ tıklayın ve **Ayarlar**' ı seçin.

1. **Donanım Ekle** ' yi seçin ve yeni bir ağ bağdaştırıcısı ekleyin.

1. Algılayıcı yönetim ağına bağlanacak sanal anahtarı seçin.

1. CPU kaynaklarını ayırır (mimariye göre).

1. Yönetim konsolunun ISO görüntüsünü bir sanal DVD sürücüsüne bağlayın.

1. Sanal makineyi başlatın.

2. **Eylemler** menüsünde, yazılım yüklemesine devam etmek için **Bağlan** ' ı seçin.

### <a name="software-installation-esxi-and-hyper-v"></a>Yazılım yükleme (ESXi ve Hyper-V)

Bu bölümde ESXi ve Hyper-V yazılım yüklemesi açıklanmaktadır.

Yüklemek için:

1. Sanal makine konsolunu açın.

1. VM ISO görüntüsünden başlayacaktır ve dil seçimi ekranı görünür. **İngilizce**'yi seçin.

1. Gerekli mimariyi seçin.

1. Gereç profilini ve ağ özelliklerini tanımlayın:

    | Parametre | Yapılandırma |
    | ----------| ------------- |
    | **Donanım profili** | &lt;gerekli mimari&gt; |
    | **Yönetim arabirimi** | **ens192** |
    | **Ağ parametreleri (müşteri tarafından verilir)** | **Yönetim ağı IP adresi:** <br/>**alt ağ maskesi:** <br/>**Gereç konak adı:** <br/>**BKZ** <br/>**Varsayılan ağ geçidi:** <br/>**Giriş arabirimleri:**|
    | **Köprü arabirimleri:** | Köprü arabirimini yapılandırmaya gerek yoktur. Bu seçenek yalnızca özel kullanım durumlarına yöneliktir. |

1. Ayarları kabul etmek için **Y** girin.

1. Oturum açma kimlik bilgileri otomatik olarak oluşturulup sunulur. Oturum açma ve yönetim için gerekli olduklarından, Kullanıcı adını ve parolayı güvenli bir yere kopyalayın.

      - **Destek**: Kullanıcı yönetimi için yönetici kullanıcı.

      - **Six**: gerecle erişmek için kökün eşdeğeri.

1. Gereç yeniden başlatılır.

1. Yönetim konsoluna daha önce yapılandırılan IP adresi aracılığıyla erişin: `https://ip_address` .

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="Yönetim konsoluna erişimi gösteren ekran görüntüsü.":::

## <a name="on-premises-management-console-installation"></a>Şirket içi yönetim konsolu yüklemesi

Yazılımı gereç üzerine yüklemeden önce, gerecin BIOS yapılandırmasını ayarlamanız gerekir:

### <a name="bios-configuration"></a>BIOS yapılandırması

Gereci BIOS 'UNU yapılandırmak için:

1. [Uzaktan erişimi etkinleştirin ve parolayı güncelleştirin](#enable-remote-access-and-update-the-password).

1. [BIOS 'U yapılandırın](#configure-the-hpe-bios).

### <a name="software-installation"></a>Yazılım yükleme

Yükleme işlemi yaklaşık 20 dakika sürer. Yükleme sonrasında, sistem birkaç kez yeniden başlatılır. 

Yükleme işlemi sırasında, ikincil bir NIC ekleyebilirsiniz. Yükleme sırasında ikincil NIC yüklememeyi seçerseniz, daha sonra [İkincil BIR NIC ekleyebilirsiniz](#add-a-secondary-nic) . 

Yazılımı yüklemek için:

1. Yükleme işlemi için tercih ettiğiniz dili seçin.

   :::image type="content" source="media/tutorial-install-components/on-prem-language-select.png" alt-text="Yükleme işlemi için tercih ettiğiniz dili seçin.":::     

1. **Yönetim-yayın- \<version\> \<deployment type\>** seçeneğini belirleyin.

   :::image type="content" source="media/tutorial-install-components/on-prem-install-screen.png" alt-text="Sürümünüzü seçin.":::   

1. Yükleme sihirbazında, ağ özelliklerini tanımlayın:

   :::image type="content" source="media/tutorial-install-components/on-prem-first-steps-install.png" alt-text="Gereç profilini gösteren ekran görüntüsü.":::   

   | Parametre | Yapılandırma |
   |--|--|
   | **Yönetim ağı arabirimini yapılandırma** | Dell için: **eth0, eth1** <br /> HP için: **enu1, enu2** <br /> veya <br />**olası değer** |
   | **Yönetim ağı IP adresini yapılandır:** | **Müşteri tarafından sunulan IP adresi** |
   | **alt ağ maskesini yapılandırma:** | **Müşteri tarafından sunulan IP adresi** |
   | **DNS yapılandırma:** | **Müşteri tarafından sunulan IP adresi** |
   | **Varsayılan ağ geçidi IP adresini yapılandır:** | **Müşteri tarafından sunulan IP adresi** |
   
1. **(Isteğe bağlı)** İkincil bir ağ arabirim kartı (NIC) yüklemek isterseniz, aşağıdaki gereç profilini ve ağ özelliklerini tanımlayın:

    :::image type="content" source="media/tutorial-install-components/on-prem-secondary-nic-install.png" alt-text="Ikincil NIC yüklemesi sorularını gösteren ekran görüntüsü.":::

   | Parametre | Yapılandırma |
   |--|--|
   | **algılayıcı izleme arabirimini yapılandırma (Isteğe bağlı):** | **eth1** veya **olası değer** |
   | **algılayıcı izleme arabirimi için bir IP adresi yapılandırın:** | **Müşteri tarafından sunulan IP adresi** |
   | **algılayıcı izleme arabirimi için bir alt ağ maskesi yapılandırın:** | **Müşteri tarafından sunulan IP adresi** |

1. Ayarları kabul edin ve yazarak devam edin `Y` . 

1. 10 dakika sonra, iki kimlik bilgileri kümesi görüntülenir. Biri bir **Six** kullanıcısına, diğeri ise **destek** kullanıcısına yöneliktir.

   :::image type="content" source="media/tutorial-install-components/credentials-screen.png" alt-text="Bu kimlik bilgilerini yeniden sunulmayacak şekilde kopyalayın.":::  

   Kullanıcı adlarını ve parolaları kaydedin, ilk kez kullandığınızda platforma erişmek için bu kimlik bilgilerine ihtiyacınız vardır.

1. Devam etmek için **ENTER** ' u seçin.

Gerecinizde fiziksel bağlantı noktasını bulma hakkında daha fazla bilgi için, bkz. [bağlantı noktasını bulma](#find-your-port).

### <a name="add-a-secondary-nic"></a>İkincil NIC ekleme

İkincil bir NIC ekleyerek şirket içi yönetim konsolunuzun güvenliğini geliştirebilirsiniz. İkincil NIC ekleyerek kullanıcılarınız için ayrılmış bir ağ geçidinizin olur ve diğeri yönlendirilen ağlar için bir ağ geçidinin yapılandırmasını destekleyecektir. İkinci NIC, bir IP adresi aralığı içindeki tüm eklenmiş sensörlerden ayrılmıştır.

Her iki NIC 'de Kullanıcı arabirimi (UI) etkinleştirilmiştir. Yönlendirme gerekli olmadığında, Kullanıcı arabirimi tarafından desteklenen tüm özellikler ikincil NIC 'de kullanılabilir olacaktır. Yüksek kullanılabilirlik ikincil NIC üzerinde çalışır.

İkincil NIC dağıtmayacak seçeneğini belirlerseniz, tüm özellikler birincil NIC üzerinden kullanılabilir olacaktır. 

Şirket içi yönetim konsolunuzu zaten yapılandırdıysanız ve şirket içi yönetim konsoluna ikincil bir NIC eklemek istiyorsanız aşağıdaki adımları kullanın:

1. Ağ yeniden yapılandırma komutunu kullanın:

    ```bash
    sudo cyberx-management-network-reconfigure
    ```

1. Aşağıdaki sorulara aşağıdaki yanıtları girin:

    :::image type="content" source="media/tutorial-install-components/network-reconfig-command.png" alt-text="Gerecinizi yapılandırmak için aşağıdaki yanıtları girin.":::

    | Parametreler | Girilecek yanıt |
    |--|--|
    | **Yönetim ağı IP adresi** | `N` |
    | **Alt ağ maskesi** | `N` |
    | **DNS** | `N` |
    | **Varsayılan ağ geçidi IP adresi** | `N` |
    | **Algılayıcı izleme arabirimi (Isteğe bağlı. Algılayıcılar farklı bir ağ kesiminde olduğunda geçerlidir. Daha fazla bilgi için bkz. yükleme yönergeleri.**| `Y`, **olası bir değer seçin** |
    | **Algılayıcı izleme arabirimi için bir IP adresi (sensörlerden erişilebilir)** | `Y`, **Müşteri tarafından sunulan IP adresi**|
    | **Algılayıcı izleme arabirimi (sensörlerden erişilebilir) için bir alt ağ maskesi** | `Y`, **Müşteri tarafından sunulan IP adresi** |
    | **Konak adı** | **Müşteri tarafından sağlanıyor** |

1. Tüm seçimleri gözden geçirin ve `Y` değişiklikleri kabul etmek için girin. Sistem yeniden başlatılır.

### <a name="find-your-port"></a>Bağlantı noktanızın bulun

Cihazınızdaki fiziksel bağlantı noktasını bulmakta sorun yaşıyorsanız, aşağıdaki komutu kullanarak şunları yapabilirsiniz:

```bash
sudo ethtool -p <port value> <time-in-seconds>
```

Bu komut, belirtilen süre boyunca bağlantı noktasındaki ışığın yanıp sönmesine neden olur. Örneğin, girme `sudo ethtool -p eno1 120` 2 dakikalık eno1 Flash bağlantı noktasını, gerecinizin arkasında bulunan bağlantı noktasını bulmanıza olanak tanıyacak şekilde alır. 

## <a name="virtual-appliance-on-premises-management-console-installation"></a>Sanal Gereç: şirket içi yönetim konsolu yüklemesi

Şirket içi yönetim konsolu VM aşağıdaki mimarilerin kullanılmasını destekler:

| Mimari | Belirtimler | Kullanım | 
|--|--|--|
| Kurumsal <br/>(Varsayılan ve en yaygın) | CPU: 8 <br/>Bellek: 32G RAM<br/> HDD: 1,8 TB | Büyük üretim ortamları | 
| Kurumsal | CPU: 4 <br/> Bellek: 8G RAM<br/> HDD: 500 GB | Büyük üretim ortamları |
| Kurumsal | CPU: 4 <br/>Bellek: 8G RAM <br/> HDD: 100 GB | Küçük test ortamları | 
   
### <a name="prerequisites"></a>Önkoşullar

Şirket içi yönetim konsolu hem VMware hem de Hyper-V dağıtım seçeneklerini destekler. Yüklemeye başlamadan önce aşağıdakileri doğrulayın:

- VMware (ESXi 5,5 veya üzeri) veya Hyper-V Hiper Yöneticisi (Windows 10 Pro veya Enterprise) yüklü ve çalışır durumda.

- Donanım kaynakları, sanal makine için kullanılabilir.

- Şirket içi yönetim konsolu için ISO yükleme dosyasına sahipsiniz.
    
- Hiper yönetici çalışıyor.

### <a name="create-the-virtual-machine-esxi"></a>Sanal makineyi (ESXi) oluşturma

Bir sanal makineye (ESXi) oluştur:

1. ESXi 'de oturum açın, ilgili **veri deposunu** seçin ve **veri deposu tarayıcısı**' nı seçin.

1. Resmi karşıya yükleyin ve **Kapat**' ı seçin.

1. **Sanal makinelere** gidin.

1. **VM oluştur/kaydol**' u seçin.

1. **Yeni sanal makine oluştur** ' u seçin ve **İleri ' yi** seçin.

1. Bir algılayıcı adı ekleyin ve şunları seçin:

   - Kları \<latest ESXi version>

   - Konuk işletim sistemi ailesi: Linux

   - Konuk işletim sistemi sürümü: Ubuntu Linux (64-bit)

1. **İleri**’yi seçin.

1. İlgili veri deposunu seçin ve **İleri ' yi** seçin.

1. Sanal donanım parametrelerini gereken mimariye göre değiştirin.

1. **CD/DVD sürücüsü 1** Için, **veri deposu ISO dosyası** ' nı seçin ve daha önce karşıya yüklediğiniz ISO dosyasını seçin.

1. **Sonraki** > **Son** seçeneğini belirleyin.

### <a name="create-the-virtual-machine-hyper-v"></a>Sanal makineyi (Hyper-V) oluşturma

Hyper-V kullanarak bir sanal makine oluşturmak için:

1. Hyper-V Yöneticisi 'nde sanal disk oluşturun.

1. **VHDX** biçimini seçin.

1. **İleri**’yi seçin.

1. **Dinamik genişleyen** türü seçin.

1. **İleri**’yi seçin.

1. VHD için ad ve konum girin.

1. **İleri**’yi seçin.

1. Gerekli boyutu girin (mimariye göre).

1. **İleri**’yi seçin.

1. Özeti gözden geçirin ve **son**' u seçin.

1. **Eylemler** menüsünde Yeni bir sanal makine oluşturun.

1. **İleri**’yi seçin.

1. Sanal makine için bir ad girin.

1. **İleri**’yi seçin.

1. **Oluştur** ' u seçin ve **1. nesil** olarak ayarlayın.

1. **İleri**’yi seçin.

1. Bellek ayırmayı (mimariye göre) belirtin ve dinamik bellek onay kutusunu seçin.

1. **İleri**’yi seçin.

1. Ağ bağdaştırıcısını sunucu ağ topolojinize göre yapılandırın.

1. **İleri**’yi seçin.

1. Daha önce oluşturulan VHDX 'i sanal makineye bağlayın.

1. **İleri**’yi seçin.

1. Özeti gözden geçirin ve **son**' u seçin.

1. Yeni sanal makineye sağ tıklayın ve ardından **Ayarlar**' ı seçin.

1. **Donanım Ekle** ' yi seçin ve **ağ bağdaştırıcısı** için yeni bir bağdaştırıcı ekleyin.

1. **Sanal anahtar** için, algılayıcı yönetim ağına bağlanacak anahtarı seçin.

1. CPU kaynaklarını ayırır (mimariye göre).

1. Yönetim konsolunun ISO görüntüsünü bir sanal DVD sürücüsüne bağlayın.

1. Sanal makineyi başlatın.

1. **Eylemler** menüsünde, yazılım yüklemesine devam etmek için **Bağlan** ' ı seçin.

### <a name="software-installation-esxi-and-hyper-v"></a>Yazılım yükleme (ESXi ve Hyper-V)

Sanal makineyi başlatmak, yükleme işlemini ISO görüntüsünden başlatacak.

Yazılımı yüklemek için:

1. **İngilizce**'yi seçin.

1. Dağıtımınız için gerekli mimariyi seçin.

1. Algılayıcı yönetimi ağı için ağ arabirimini tanımlayın: arabirim, IP, alt ağ, DNS sunucusu ve varsayılan ağ geçidi.

1. Oturum açma kimlik bilgileri otomatik olarak oluşturulur. Kullanıcı adını ve parolaları kaydedin, ilk kez kullandığınızda platforma erişmek için bu kimlik bilgilerine ihtiyacınız vardır.

   Gereç daha sonra yeniden başlatılır.

1. Yönetim konsoluna daha önce yapılandırılan IP adresi aracılığıyla erişin: `<https://ip_address>` .

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-management-console-sign-in-screen.png" alt-text="Yönetim konsolunun oturum açma ekranını gösteren ekran görüntüsü.":::

## <a name="post-installation-validation"></a>Yükleme sonrası doğrulama

Fiziksel bir gerecin yüklenmesini doğrulamak için birçok test gerçekleştirmeniz gerekir. Aynı doğrulama işlemi, tüm gereç türleri için geçerlidir.

GUI veya CLı kullanarak doğrulama gerçekleştirin. Doğrulama Kullanıcı **desteği** ve Kullanıcı **Six** için kullanılabilir.

Yükleme sonrası doğrulama aşağıdaki testleri içermelidir:

  - **Sanity testi**: sistemin çalıştığını doğrulayın.

  - **Sürüm**: sürümün doğru olduğundan emin olun.

  - **ıconfig**: yükleme işlemi sırasında yapılandırılan tüm giriş arabirimlerinin çalıştığını doğrulayın.

### <a name="checking-system-health-by-using-the-gui"></a>GUI kullanarak sistem durumunu denetleme

:::image type="content" source="media/tutorial-install-components/system-health-check-screen.png" alt-text="Sistem durumu denetimini gösteren ekran görüntüsü.":::

#### <a name="sanity"></a>Sağlamlık

- **Gereç**: gereç sağlamlık denetimini çalıştırır. CLı komutunu kullanarak aynı denetimi yapabilirsiniz `system-sanity` .

- **Sürüm**: gereç sürümünü görüntüler.

- **Ağ özellikleri**: algılayıcı ağ parametrelerini görüntüler.

#### <a name="redis"></a>Redis

- **Bellek**: bellek kullanımının genel resmi sağlar, örneğin ne kadar bellek kullanılmıştı ve ne kadar kaldı.

- **En uzun anahtar**: kapsamlı bellek kullanımına neden olabilecek en uzun anahtarları görüntüler.

#### <a name="system"></a>Sistem

- **Çekirdek günlüğü**: Ana günlüğün son 500 satırını sağlar ve tüm sistem günlüğünü dışa aktarmadan son günlük satırlarını görüntülemenize olanak tanır.

- **Görev Yöneticisi**: işlem tablosunda görünen görevleri aşağıdaki katmanlara çevirir: 
  
  - Kalıcı katman (Redsıs) 
  - Nakit katmanı (SQL)

- **Ağ istatistikleri**: ağ istatistiklerinizi görüntüler.

- **Üst**: işlem tablosunu gösterir. Bu, çalışan sistemin dinamik gerçek zamanlı bir görünümünü sağlayan bir Linux komutu.

- **Yedekleme belleği denetimi**: yedekleme belleğinin durumunu sağlar ve aşağıdakileri kontrol edin:
  - Yedekleme klasörünün konumu 
  - Yedekleme klasörünün boyutu
  - Yedekleme klasörünün sınırlamaları
  - Son yedekleme gerçekleşti
  - Ek yedekleme dosyaları için ne kadar alan var

- **ıconfig**: gereç için fiziksel arabirimlerin parametrelerini görüntüler.

- **Six ir**: altı saniyelik testleri kullanarak ağ trafiğini ve bant genişliğini görüntüler.

- **Core 'Daki hatalar, GNLK**: Çekirdek günlük dosyasındaki hataları görüntüler.

Araca erişmek için:

1. **Destek** kullanıcısı kimlik bilgileriyle sensörde oturum açın.

1. Sistem **ayarları** penceresinden **sistem istatistiklerini** seçin.

    :::image type="icon" source="media/tutorial-install-components/system-statistics-icon.png" border="false":::

### <a name="checking-system-health-by-using-the-cli"></a>CLı kullanarak sistem durumu denetleniyor

**Test 1: Sanity**

Sistemin çalışır ve çalışıyor olduğundan emin olun:

1. Linux terminali (örneğin, PuTTY) ve Kullanıcı **desteği** ile CLI 'ya bağlanın.

1. `system sanity` yazın.

1. Tüm hizmetlerin yeşil (çalışıyor) olup olmadığını denetleyin.

    :::image type="content" source="media/tutorial-install-components/support-screen.png" alt-text="Çalışan hizmetleri gösteren ekran görüntüsü.":::

1. **SISTEMIN çalışır olduğunu doğrulayın! (üretim)** En altta görünür.

**Test 2: sürüm denetimi**

Doğru sürümün kullanıldığını doğrulayın:

1. Linux terminali (örneğin, PuTTY) ve Kullanıcı **desteği** ile CLI 'ya bağlanın.

1. `system version` yazın.

1. Doğru sürümün göründüğünden emin olun.

**Test 3: ağ doğrulama**

Yükleme işlemi sırasında yapılandırılan tüm giriş arabirimlerinin çalıştığını doğrulayın:

1. Linux terminali (örneğin, PuTTY) ve Kullanıcı **desteği** ile CLI 'ya bağlanın.

1. Girin `network list` (Linux komutunun eşdeğeri `ifconfig` ).

1. Gerekli giriş arabirimlerinin göründüğünü doğrulayın. Örneğin, iki adet dört bakır NIC yüklüyse, listede 10 arabirim olmalıdır.

    :::image type="content" source="media/tutorial-install-components/interface-list-screen.png" alt-text="Arabirimlerin listesini gösteren ekran görüntüsü.":::

**Test 4: Kullanıcı arabirimine yönetim erişimi**

Konsol Web GUI 'ye erişebildiğinizi doğrulayın:

1. Ethernet kablosu olan bir dizüstü bilgisayarı yönetim bağlantı noktasına (**GB1**) bağlayın.

1. Dizüstü bilgisayar NIC adresini gereç ile aynı aralıkta olacak şekilde tanımlayın.

    :::image type="content" source="media/tutorial-install-components/access-to-ui.png" alt-text="Kullanıcı arabirimine yönetim erişimini gösteren ekran görüntüsü.":::

1. Bağlantıyı doğrulamak için gereç 'nin IP adresine ping gönderin (varsayılan: 10.100.10.1).

1. Dizüstü bilgisayarda Chrome tarayıcısını açın ve gerecin IP adresini girin.

1. **Bağlantınızda özel pencere olmadığından** , **Gelişmiş** ' i seçin ve devam edin.

1. IoT için Defender oturum açma ekranı göründüğünde test başarılı olur.

   :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="Yönetim konsoluna erişimi gösteren ekran görüntüsü.":::

## <a name="troubleshooting"></a>Sorun giderme

### <a name="you-cant-connect-by-using-a-web-interface"></a>Web arabirimi kullanarak bağlanamazsınız

1. Bağlanmaya çalıştığınız bilgisayarın gereç ile aynı ağ üzerinde olduğundan emin olun.

1. GUI ağının yönetim bağlantı noktasına bağlı olduğunu doğrulayın.

1. Gereç IP adresine ping gönderin. Ping işlemi yoksa:

   1. Bir izleyiciyi ve klavyeyi gereç ile bağlayın.

   1. Oturum açmak için **destek** kullanıcısını ve parolayı kullanın.

   1. `network list`GEÇERLI IP adresini görmek için komutunu kullanın.

      :::image type="content" source="media/tutorial-install-components/network-list.png" alt-text="Ağ listesini gösteren ekran görüntüsü.":::

1. Ağ parametreleri yanlış yapılandırılmış ise, bunları değiştirmek için aşağıdaki yordamı kullanın:

   1. Komutunu kullanın `network edit-settings` .

   1. Yönetim ağı IP adresini değiştirmek için **Y**' yi seçin.

   1. Alt ağ maskesini değiştirmek için **Y**' yi seçin.

   1. DNS 'yi değiştirmek için **Y**'yi seçin.

   1. Varsayılan ağ geçidi IP adresini değiştirmek için **Y**' yi seçin.

   1. Giriş arabirimi değişikliği için (yalnızca algılayıcı), **N**' yi seçin.

   1. Ayarları uygulamak için **Y**' yi seçin.

1. Yeniden başlattıktan sonra, destek Kullanıcı kimlik bilgileriyle bağlanın ve `network list` parametrelerin değiştirildiğini doğrulamak için komutunu kullanın.

1. PING ve GUI 'den yeniden bağlanmayı deneyin.

### <a name="the-appliance-isnt-responding"></a>Gereç yanıt vermiyor

1. Bir izleyiciyi bir izleyici ve klavye bağlayın veya CLı 'ya uzaktan bağlanmak için PuTTY kullanın.

1. Oturum açmak için **destek** kullanıcısının kimlik bilgilerini kullanın.

1. Komutunu kullanın `system sanity` ve tüm işlemlerin çalıştığını denetleyin.

    :::image type="content" source="media/tutorial-install-components/system-sanity-screen.png" alt-text="Sistem sağlamlık komutunu gösteren ekran görüntüsü.":::

Diğer herhangi bir sorun için [Microsoft desteği](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)başvurun.

## <a name="appendix-a-mirroring-port-on-vswitch-esxi"></a>Ek A: vSwitch üzerinde (ESXi) bağlantı noktası yansıtma

### <a name="configure-a-span-port-on-an-existing-vswitch"></a>Mevcut bir vSwitch üzerinde bir SPAN bağlantı noktası yapılandırma

Bir vSwitch, yansıtma özelliklerine sahip değildir, ancak bir yuva bağlantı noktası uygulamak için geçici çözüm kullanabilirsiniz.

Bir SPAN bağlantı noktasını yapılandırmak için:

1. VSwitch özelliklerini açın.

1. **Add (Ekle)** seçeneğini belirleyin.

1. Sonraki **sanal makineyi** seçin  >  .

1. Ağ etiketi **yayılma ağı** ekleyin, **VLAN ID**  >  **All**' ı seçin ve ardından **İleri**' yi seçin.

1. **Son**'u seçin.

1. **Yayılma ağ** > **Düzenle*' yi seçin.

1. **Güvenlik**' i seçin ve **karışık mod** ilkesinin **kabul** modu olarak ayarlandığını doğrulayın.

1. **Tamam**' ı seçin ve ardından vSwitch özelliklerini kapatmak için **Kapat** ' ı seçin.

1. **Xsense VM** özelliklerini açın.

1. **Ağ bağdaştırıcısı 2** için **yayılma** ağını seçin.

1. **Tamam**’ı seçin.

1. Sensöre bağlanın ve yansıtmanın çalıştığını doğrulayın.

## <a name="appendix-b-access-sensors-from-the-on-premises-management-console"></a>Ek B: şirket içi yönetim konsolundan sensörlerden erişin

Algılayıcıya doğrudan Kullanıcı erişimini engelleyerek sistem güvenliğini geliştirebilirsiniz. Bunun yerine, kullanıcıların algılayıcıya şirket içi yönetim konsolundan tek bir güvenlik duvarı kuralıyla erişmesine izin vermek için proxy Tüneli kullanın. Bu teknik, algılayıcı ötesinde ağ ortamına yetkisiz erişim olasılığını daraltır. Sensörde oturum açarken kullanıcının deneyimi aynı kalır.

:::image type="content" source="media/tutorial-install-components/sensor-system-graph.png" alt-text="Sensöre erişimi gösteren ekran görüntüsü.":::

Tünelleme özelliğini etkinleştirmek için:

1. **Six** ile şirket içi YÖNETIM konsolunun CLI veya Kullanıcı kimlik bilgilerini **destekleme** ' de oturum açın.

1. `sudo cyberx-management-tunnel-enable` yazın.

1. **Enter** tuşunu seçin.

1. `--port 10000` yazın.

### <a name="next-steps"></a>Sonraki adımlar

[Ağınızı kurma](how-to-set-up-your-network.md)