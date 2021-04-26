---
title: Azure geçişi gereç dağıtımı ve bulma sorunlarını giderme
description: Gereç dağıtımı ve sunucu bulma ile ilgili yardım alın.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 995914fab0e7112327ebf6ab8e32fb67181f481e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608927"
---
# <a name="troubleshoot-the-azure-migrate-appliance-and-discovery"></a>Azure geçişi Gereç ve bulma sorunlarını giderme

Bu makale, [Azure geçiş](migrate-services-overview.md) gereci dağıtma ve şirket içi sunucuları bulmaya yönelik gereç kullanma konularında sorunları gidermenize yardımcı olur.

## <a name="whats-supported"></a>Neler desteklenir?

Gereç destek gereksinimlerini [gözden geçirin](migrate-appliance.md) .

## <a name="invalid-ovf-manifest-entry"></a>"Geçersiz OVF manifest girdisi"

"Belirtilen bildirim dosyası geçersiz: geçersiz OVF manifest entry" hatasını alırsanız şunları yapın:

1. Azure geçişi gereç OVA dosyasının karma değerini denetleyerek doğru şekilde indirildiğini doğrulayın. [Daha fazla bilgi edinin](./tutorial-discover-vmware.md). Karma değeri eşleşmiyorsa, OVA dosyasını yeniden indirin ve dağıtımı yeniden deneyin.
2. Dağıtım hala başarısız olursa ve OVF dosyasını dağıtmak için VMware vSphere istemcisini kullanıyorsanız, vSphere Web istemcisi aracılığıyla dağıtmayı deneyin. Dağıtım hala başarısız olursa, farklı bir Web tarayıcısı kullanmayı deneyin.
3. VSphere Web istemcisini kullanıyorsanız ve vCenter Server 6,5 veya 6,7 ' de dağıtmaya çalışıyorsanız, OVA 'yı doğrudan ESXi konağına dağıtmayı deneyin:
   - Web istemcisi (https://<*ana BILGISAYAR IP adresi*>/UI) Ile ESXi konağına doğrudan (vCenter Server yerine) bağlanın.
   - **Ev**  >  **envanterinde**,   >  **ovf şablonu**' nu Dağıt ' ı seçin. OVA 'ya gidin ve dağıtımı doldurun.
4. Dağıtım hala başarısız olursa Azure geçiş desteği 'ne başvurun.

## <a name="cant-connect-to-the-internet"></a>İnternet 'e bağlanılamıyor

Gereç sunucusu bir proxy 'nin arkasındaysa bu durum oluşabilir.

- Ara sunucuya gerekiyorsa yetkilendirme kimlik bilgilerini sağlamaya dikkat edin.
- Giden bağlantıyı denetlemek için URL tabanlı bir güvenlik duvarı proxy 'si kullanıyorsanız, [Bu URL 'leri](migrate-appliance.md#url-access) bir allowlist öğesine ekleyin.
- İnternet 'e bağlanmak için bir kesintiye uğratan ara sunucu kullanıyorsanız, [Bu adımları](./migrate-appliance.md)kullanarak proxy sertifikasını gereç üzerine aktarın.

## <a name="cant-sign-into-azure-from-the-appliance-web-app"></a>Azure 'da gereç Web uygulamasından oturum açılamıyor

Azure 'da oturum açmak için yanlış Azure hesabı kullanıyorsanız, "Üzgünüz, ancak oturumunuzu açarken sorun yaşıyoruz" hatası görüntülenir. Bu hata birkaç nedenden dolayı oluşur:

- Kamu Bulutu için gereç Web uygulamasında oturum açarsanız, kamu bulutu portalının Kullanıcı hesabı kimlik bilgilerini kullanarak.
- Özel bulut portalının Kullanıcı hesabı kimlik bilgilerini kullanarak kamu bulutu için gereç Web uygulamasında oturum açarsanız.

Doğru kimlik bilgilerini kullandığınızdan emin olun.

## <a name="datetime-synchronization-error"></a>Tarih/saat eşitleme hatası

Tarih ve saat eşitleme (802) ile ilgili bir hata, sunucu saatinin beş dakikadan uzun bir süredir geçerli zamandan eşitlenmemiş olabileceğini gösterir. Toplayıcı sunucusundaki saat saatini Şu anki saatle eşleşecek şekilde değiştirin:

1. Sunucuda bir yönetici komut istemi açın.
2. Saat dilimini denetlemek için **w32tm/tz komutunu** çalıştırın.
3. Saati eşitleme için **w32tm/resync** komutunu çalıştırın.

## <a name="unabletoconnecttoserver"></a>"UnableToConnectToServer"

Bu bağlantı hatası alırsanız, vCenter Server *ServerName*. com: 9443 öğesine bağlanamadıysanız. Hata ayrıntıları, `https://\*servername*.com:9443/sdk` iletiyi kabul edebilecek bir uç nokta dinleme olmadığını gösterir.

- En son gereç sürümünü çalıştırıp çalıştırmadığını denetleyin. Değilseniz, gereci [en son sürüme](./migrate-appliance.md)yükseltin.
- Sorun hala en son sürümde gerçekleşirse, Gereç belirtilen vCenter Server adını çözemeyebilir veya belirtilen bağlantı noktası yanlış olabilir. Varsayılan olarak, bağlantı noktası belirtilmemişse, toplayıcı 443 numaralı bağlantı noktası numarasına bağlanmayı dener.

    1. Gereci *ServerName*. com ' a ping yapın.
    2. 1. adım başarısız olursa, IP adresini kullanarak vCenter Server 'a bağlanmayı deneyin.
    3. VCenter Server bağlanmak için doğru bağlantı noktası numarasını belirler.
    4. VCenter Server çalışır olduğunu doğrulayın.

## <a name="error-6005260039-appliance-might-not-be-registered"></a>Hata 60052/60039: gereç kaydettirilmemiş olabilir

- Hata 60052, "gereci kaydetmek için kullanılan Azure hesabının izinleri yetersizse," gereç projeye başarıyla kaydedilmemiş olabilir "hatası oluşur.
    - Gereci kaydetmek için kullanılan Azure Kullanıcı hesabının abonelikte en az katkıda bulunan izinleri olduğundan emin olun.
    - Gerekli Azure rolleri ve izinleri hakkında [daha fazla bilgi edinin](./migrate-appliance.md#appliance---vmware) .
- Hata 60039, "gereç projeye başarıyla kaydedilmemiş olabilir", kayıt başarısız olursa, gerecin kayıt işlemi için kullanılan proje bulunamadığı için kayıt başarısız olursa meydana gelebilir.
    - Azure portal ve projenin kaynak grubunda mevcut olup olmadığını kontrol edin.
    - Proje yoksa, kaynak grubunuzda yeni bir proje oluşturun ve gereci yeniden kaydedin. Yeni bir proje oluşturmayı [öğrenin](./create-manage-projects.md#create-a-project-for-the-first-time) .

## <a name="error-6003060031-key-vault-management-operation-failed"></a>Hata 60030/60031: Key Vault Yönetimi işlemi başarısız oldu

60030 veya 60031 hatası alırsanız, "bir Azure Key Vault yönetim işlemi başarısız oldu", şunları yapın:

- Gereci kaydetmek için kullanılan Azure Kullanıcı hesabının abonelikte en az katkıda bulunan izinleri olduğundan emin olun.
- Hesabın hata iletisinde belirtilen anahtar kasasına erişimi olduğundan emin olun ve işlemi yeniden deneyin.
- Sorun devam ederse, Microsoft desteğine başvurun.
- Gerekli Azure rolleri ve izinleri hakkında [daha fazla bilgi edinin](./migrate-appliance.md#appliance---vmware) .

## <a name="error-60028-discovery-couldnt-be-initiated"></a>Hata 60028: bulma başlatılamadı

Hata 60028: "bir hata nedeniyle bulma başlatılamadı. Belirtilen konaklar veya kümeler listesi için işlem başarısız oldu "sunucu bilgilerine erişirken veya alırken bir sorun nedeniyle hatada listelenen konaklarda bulma işleminin başlatılamayacağını gösterir. Ana bilgisayarların geri kalanı başarıyla eklendi.

- Hatada listelenen Konakları, **ana bilgisayar Ekle** seçeneğini kullanarak yeniden ekleyin.
- Doğrulama hatası varsa, hataları onarmak için düzeltme kılavuzunu gözden geçirin ve sonra **bulmayı Kaydet ve Başlat** seçeneğini tekrar deneyin.

## <a name="error-60025-azure-ad-operation-failed"></a>Hata 60025: Azure AD işlemi başarısız oldu

Hata 60025: "Azure AD işlemi başarısız oldu. Azure AD uygulaması oluşturulurken veya güncelleştirilirken oluşan hata oluştu "bulmayı başlatmak için kullanılan Azure Kullanıcı hesabı gereci kaydetmek için kullanılan hesaptan farklı olduğunda gerçekleşir. Aşağıdakilerden birini yapın:

- Keşfi başlatan kullanıcı hesabının gereci kaydetmek için kullanılan ile aynı olduğundan emin olun.
- Bulma işleminin başarısız olduğu Kullanıcı hesabına Azure Active Directory Uygulama erişim izinleri sağlayın.
- Proje için önceden oluşturulan kaynak grubunu silin. Yeniden başlamak için başka bir kaynak grubu oluşturun.
- Azure Active Directory Uygulama izinleri hakkında [daha fazla bilgi edinin](./migrate-appliance.md#appliance---vmware) .

## <a name="error-50004-cant-connect-to-host-or-cluster"></a>Hata 50004: konağa veya kümeye bağlanılamıyor

Hata 50004: "sunucu adı çözümlenemediği için bir konağa veya kümeye bağlanılamıyor. WinRM hata kodu: gereç için Azure DNS hizmeti, verdiğiniz küme veya ana bilgisayar adını çözümleyemezse, 0x803381B9 "gerçekleşebilir.

- Kümede bu hatayı görürseniz, küme FQDN 'SI.
- Ayrıca, bir kümedeki konaklar için bu hatayı görebilirsiniz. Bu, gerecin kümeye bağlanabildiğini, ancak kümenin FQDN olmayan ana bilgisayar adlarını döndürdüğünü gösterir. Bu hatayı çözmek için, IP adresi ve ana bilgisayar adlarının eşlemesini ekleyerek gereç üzerindeki Hosts dosyasını güncelleştirin:
    1. Not defteri 'Ni yönetici olarak açın.
    2. C:\Windows\System32\Drivers\etc\hosts dosyasını açın.
    3. IP adresini ve ana bilgisayar adını bir satıra ekleyin. Bu hatayı gördüğünüz her bir konak veya küme için tekrarlayın.
    4. Hosts dosyasını kaydedin ve kapatın.
    5. Gereç Yönetimi uygulamasını kullanarak gerecin konaklara bağlanıp bağlanamayacağını denetleyin. 30 dakika sonra, Azure portal bu konaklar için en son bilgileri görmeniz gerekir.

## <a name="error-60001-unable-to-connect-to-server"></a>Hata 60001: sunucuya bağlanılamıyor

- Gerecden sunucuya bağlantı olduğundan emin olun
- Bir Linux sunucusu ise, aşağıdaki adımları kullanarak parola tabanlı kimlik doğrulamasının etkinleştirildiğinden emin olun:
    1. Linux sunucusunda oturum açın ve ' VI/etc/ssh/sshd_config ' komutunu kullanarak SSH yapılandırma dosyasını açın.
    2. "Passwordaduthentication" seçeneğini Evet olarak ayarlayın. Dosyayı kaydedin.
    3. SSH hizmetini "Service SSHD restart" çalıştırarak yeniden başlatın
- Bir Windows Server ise, bağlantı noktası 5985 ' nin uzak WMI çağrılarına izin vermek için açık olduğundan emin olun.
- Bir GCP Linux sunucusu keşfederken ve bir kök Kullanıcı kullanıyorsanız, kök oturum açma için varsayılan ayarı değiştirmek üzere aşağıdaki komutları kullanın
    1. Linux sunucusunda oturum açın ve ' VI/etc/ssh/sshd_config ' komutunu kullanarak SSH yapılandırma dosyasını açın.
    2. "PermitRootLogin" seçeneğini Evet olarak ayarlayın.
    3. SSH hizmetini "Service SSHD restart" çalıştırarak yeniden başlatın

## <a name="error-no-suitable-authentication-method-found"></a>Hata: uygun bir kimlik doğrulama yöntemi bulunamadı

Aşağıdaki adımları kullanarak Linux sunucusunda parola tabanlı kimlik doğrulamasının etkinleştirildiğinden emin olun:
    1. Linux sunucusunda oturum açın ve ' VI/etc/ssh/sshd_config ' komutunu kullanarak SSH yapılandırma dosyasını açın.
    2. "Passwordaduthentication" seçeneğini Evet olarak ayarlayın. Dosyayı kaydedin.
    3. SSH hizmetini "Service SSHD restart" çalıştırarak yeniden başlatın

## <a name="discovered-servers-not-in-portal"></a>Bulunan sunucular portalda yok

Bulma durumu "devam ediyor" ise, ancak portalda sunucu görmediyse, birkaç dakika bekleyin:

- VMware üzerindeki bir sunucu için 15 dakika sürer.
- Hyper-V bulma 'daki sunucular için eklenen her konak için iki dakika sürer.

Beklerseniz ve durum değişmezse, **sunucular** sekmesinde **Yenile** ' yi seçin. Bu, Azure geçişi 'nde bulunan sunucu sayısını göstermelidir: bulma ve değerlendirme ve Azure geçişi: sunucu geçişi.

Bu işe yaramazsa ve VMware sunucularını keşfederken:

- Belirttiğiniz vCenter hesabının, en az bir sunucuya erişimi olan izinlerin doğru şekilde ayarlandığını doğrulayın.
- VCenter hesabının vCenter VM klasör düzeyinde erişimi varsa Azure geçişi, VMware üzerindeki sunucuları bulamaz. Kapsam bulma hakkında [daha fazla bilgi edinin](set-discovery-scope.md) .

## <a name="server-data-not-in-portal"></a>Sunucu verileri portalda yok

Bulunan sunucular portalda görünmezse veya sunucu verileri güncel değilse, birkaç dakika bekleyin. Keşfedilen sunucu yapılandırma verilerinde yapılan değişikliklerin portalda gösterilmesi 30 dakika kadar sürer. Yazılım envanteri verileri değişikliklerinin görünmesi birkaç saat sürebilir. Bu süreden sonra veri yoksa yenilemeyi şu şekilde deneyin

1. **Windows, Linux ve SQL Server sunucuları**  >  **Azure geçişi: bulma ve değerlendirme** için **genel bakış**' ı seçin.
2. **Yönet** altında **Aracı durumu**' yi seçin.
3. **Aracıyı Yenile**' yi seçin.
4. Yenileme işleminin tamamlanmasını bekleyin. Şimdi güncel bilgileri görmeniz gerekir.

## <a name="deleted-servers-appear-in-portal"></a>Portal 'da silinen sunucular görüntülenir

Sunucuları silerseniz ve portalda hala görünüyorsa 30 dakika bekleyin. Hala görünüyorsa, yukarıda açıklandığı gibi yenileyin.

## <a name="discovered-software-inventory-and-sql-server-instances-and-databases-not-in-portal"></a>Keşfedilen yazılım envanteri ve SQL Server örnekleri ve veritabanları portalda yok

Gereç üzerinde bulmayı başlattıktan sonra, portalda envanter verilerinin gösterilmesi 24 saate kadar sürebilir.

Gereç Yapılandırma Yöneticisi 'nde Windows kimlik doğrulaması veya SQL Server kimlik doğrulama kimlik bilgileri sağlamadıysanız, gerecin ilgili SQL Server örneklerine bağlanmak için onları kullanabilmesi adına kimlik bilgilerini ekleyin.

Bağlantı kurulduktan sonra, Gereç SQL Server örneklerinin ve veritabanlarının yapılandırma ve performans verilerini toplar. SQL Server yapılandırma verileri her 24 saatte bir güncelleştirilir ve performans verileri her 30 saniyede yakalanır. Bu nedenle, SQL Server örneğin özelliklerinde ve veritabanı durumu, uyumluluk düzeyi vb. gibi veritabanlarının her türlü değişikliği portalda güncelleştirmek 24 saate kadar sürebilir.

## <a name="sql-server-instance-is-showing-up-in-not-connected-state-on-portal"></a>SQL Server örnek, portalda "bağlı değil" durumunda gösteriliyor

SQL Server örnekleri ve veritabanlarının bulunması sırasında karşılaşılan sorunları görüntülemek için, projenizdeki ' bulunan sunucular ' sayfasında bulunan bağlantı durumu sütununda "bağlı değil" durumuna tıklayın.

Tamamen bulunmayan veya bağlı olmayan SQL örnekleri içeren sunucuların üzerinde değerlendirme oluşturmak, hazır olma durumunun "bilinmiyor" olarak işaretlenmesine neden olabilir.

## <a name="i-do-not-see-performance-data-for-some-network-adapters-on-my-physical-servers"></a>Fiziksel sunucularım üzerinde bazı ağ bağdaştırıcılarının performans verilerini görmüyorum

Fiziksel sunucuda Hyper-V Sanallaştırması etkinleştirilmişse bu durum oluşabilir. Bir ürün boşluğu nedeniyle, ağ aktarım hızı bulunan sanal ağ bağdaştırıcılarında yakalanır.

## <a name="error-the-file-uploaded-is-not-in-the-expected-format"></a>Hata: Yüklenen dosya beklenen biçimde değil

Bazı araçların bölgesel ayarları, sınırlayıcı olarak noktalı virgül kullanan CSV dosyaları oluşturur. Ayarların sınırlayıcı olarak virgül kullandığından emin olun.

## <a name="i-imported-a-csv-but-i-see-discovery-is-in-progress"></a>CSV’yi içeri aktardım ancak “Keşif sürüyor” yazısıyla karşılaşıyorum

Bu durum, CSV yüklemeniz doğrulama hatası nedeniyle başarısız olduysa görüntülenir. CSV’yi içeri aktarmayı yeniden deneyin. Önceki karşıya yüklemenin hata raporunu indirebilir ve hataları gidermek için düzeltme kılavuzunu izleyebilirsiniz. Hata raporu, ' sunucuları bul ' sayfasındaki ' Içeri aktarma ayrıntıları ' bölümünden indirilebilir.

## <a name="do-not-see-software-inventory-details-even-after-updating-guest-credentials"></a>Konuk kimlik bilgilerini güncelleştirdikten sonra bile yazılım envanteri ayrıntılarını görmeyin

Yazılım envanteri bulma her 24 saatte bir çalışır. Ayrıntıları hemen görmek isterseniz aşağıdaki gibi yenileyin. Hayır öğesine bağlı olarak bu işlem birkaç dakika sürebilir. bulunan sunucular.

1. **Windows, Linux ve SQL Server sunucuları**  >  **Azure geçişi: bulma ve değerlendirme** için **genel bakış**' ı seçin.
2. **Yönet** altında **Aracı durumu**' yi seçin.
3. **Aracıyı Yenile**' yi seçin.
4. Yenileme işleminin tamamlanmasını bekleyin. Şimdi güncel bilgileri görmeniz gerekir.

## <a name="unable-to-export-software-inventory"></a>Yazılım envanteri dışarı aktarılamıyor

Portalda envanterden indirilen kullanıcının abonelik üzerinde katkıda bulunan ayrıcalıklara sahip olduğundan emin olun.

## <a name="no-suitable-authentication-method-found-to-complete-authentication-publickey"></a>Kimlik doğrulamasını tamamlamaya uygun bir kimlik doğrulama yöntemi bulunamadı (PublicKey)

Anahtar tabanlı kimlik doğrulaması çalışmayacak, parola kimlik doğrulamasını kullanacak.

## <a name="common-app-discovery-errors"></a>Ortak uygulama bulma hataları

Azure geçişi, Azure geçişi: bulma ve değerlendirme kullanılarak yazılım envanteri bulmayı destekler. Uygulama bulma Şu anda yalnızca VMware için destekleniyor. Uygulama bulmayı ayarlamaya yönelik gereksinimler ve adımlar hakkında [daha fazla bilgi edinin](how-to-discover-applications.md) .

Tipik uygulama bulma hataları tabloda özetlenir.

| **Hata** | **Neden** | **Eylem** |
|--|--|--|
| 9000: VMware araç durumu algılanamıyor. | VMware araçları yüklenmemiş veya bozuk olabilir. | VMware araçlarının sunucuda yüklü olduğundan ve çalıştığından emin olun. |
| 9001: VMware araçları yüklü değil. | VMware araçları yüklenmemiş veya bozuk olabilir. | VMware araçlarının sunucuda yüklü olduğundan ve çalıştığından emin olun. |
| 9002: VMware araçları çalışmıyor. | VMware araçları yüklenmemiş veya bozuk olabilir. | VMware araçlarının sunucuda yüklü olduğundan ve çalıştığından emin olun. |
| 9003: işletim sistemi türü, Konuk sunucu keşfi için desteklenmiyor. | Sunucu üzerinde çalışan işletim sistemi Windows veya Linux değildir. | Desteklenen işletim sistemi türleri yalnızca Windows ve Linux. Sunucu gerçekten Windows veya Linux ise vCenter Server ' de belirtilen işletim sistemi türünü denetleyin. |
| 9004: sunucu çalışmıyor. | Sunucu kapalı. | Sunucunun açık olduğundan emin olun. |
| 9005: işletim sistemi türü, Konuk sunucu keşfi için desteklenmiyor. | Konuk sunucu keşfi için işletim sistemi türü desteklenmiyor. | Desteklenen işletim sistemi türleri yalnızca Windows ve Linux. |
| 9006: meta veri dosyasının konuğdan indirileceği URL boş. | Bu durum, bulma Aracısı beklendiği gibi çalışmıyorsa gerçekleşebilir. | Sorun otomatik olarak in24 saat çözümlenmelidir. Sorun devam ederse, Microsoft Desteği başvurun. |
| 9007: Konuk sunucuda bulma görevini çalıştıran işlem bulunamadı. | Keşif Aracısı düzgün çalışmıyorsa bu durum oluşabilir. | Sorun, 24 saat içinde otomatik olarak çözümlenmelidir. Sorun devam ederse, Microsoft Desteği başvurun. |
| 9008: Konuk sunucu işlemi durumu alınamıyor. | Sorun, bir iç hata nedeniyle oluşabilir. | Sorun, 24 saat içinde otomatik olarak çözümlenmelidir. Sorun devam ederse, Microsoft Desteği başvurun. |
| 9009: Windows UAC, sunucuda bulma görevi yürütmeyi engelledi. | Sunucu üzerindeki Windows Kullanıcı hesabı denetimi (UAC) ayarları kısıtlayıcıdır ve yüklü yazılım envanterinin keşfedilmesi önlenir. | Sunucudaki ' Kullanıcı hesabı denetimi ' ayarları ' nda, UAC ayarını alt iki düzeyden birinde olacak şekilde yapılandırın. |
| 9010: sunucu kapatılmış. | Sunucu kapalı. | Sunucunun açık olduğundan emin olun. |
| 9011: bulunan meta veri dosyası Konuk sunucu dosya sisteminde bulunamadı. | Sorun, bir iç hata nedeniyle oluşabilir. | Sorun, 24 saat içinde otomatik olarak çözümlenmelidir. Sorun devam ederse, Microsoft Desteği başvurun. |
| 9012: bulunan meta veri dosyası boş. | Sorun, bir iç hata nedeniyle oluşabilir. | Sorun, 24 saat içinde otomatik olarak çözümlenmelidir. Sorun devam ederse, Microsoft Desteği başvurun. |
| 9013: her oturum açma işlemi için yeni bir geçici profil oluşturulur. | VMware 'deki sunucuda her oturum açma işlemi için yeni bir geçici profil oluşturulur. | Bir çözüm için Microsoft Desteği başvurun. |
| 9014: Konuk sunucu dosya sisteminden meta veriler alınamıyor. | ESXi konağına bağlantı yok | Gerecin sunucuyu çalıştıran ESXi konağında 443 numaralı bağlantı noktasına bağlanabildiğinden emin olun |
| 9015: vCenter Kullanıcı hesabında Konuk Işlemler rolü etkin değil | Konuk Işlemler rolü vCenter Kullanıcı hesabında etkin değil. | VCenter Kullanıcı hesabında Konuk Işlemleri rolünün etkinleştirildiğinden emin olun. |
| 9016: Konuk işlem Aracısı güncel olmadığından bulunamıyor. | VMware araçları düzgün yüklenmemiş veya güncel değil. | VMware araçlarının düzgün bir şekilde yüklendiğinden ve güncel olduğundan emin olun. |
| 9017: sunucuda bulunan meta verileri içeren dosya bulunamadı. | Sorun, bir iç hata nedeniyle oluşabilir. | Bir çözüm için Microsoft Desteği başvurun. |
| 9018: PowerShell, Konuk sunucularda yüklü değil. | PowerShell, Konuk sunucuda kullanılamıyor. | PowerShell 'i Konuk sunucuya yükler. |
| 9019: Konuk sunucu işlem hatalarından dolayı bulunamıyor. | VMware Konuk işlemi sunucuda başarısız oldu. | Sunucu kimlik bilgilerinin geçerli olduğundan ve konuk sunucu kimlik bilgilerinde belirtilen kullanıcı adının UPN biçiminde olduğundan emin olun. |
| 9020: dosya oluşturma izni reddedildi. | Kullanıcı veya grup ilkesiyle ilişkili rol, kullanıcının klasörde dosya oluşturmasını kısıtlıyor | Belirtilen Konuk kullanıcının klasöründeki dosya için oluşturma iznine sahip olup olmadığını denetleyin. Bkz **. Azure geçişi: klasörün** adı için bulma ve değerlendirme. |
| 9021: sistem geçici yolunda dosya oluşturulamıyor. | VMware aracı, kullanıcılar geçici yolu yerine sistem geçici yolunu raporlar. | VMware aracı sürümünüzü 10287 ' dan (NGC/VI Istemci biçimi) yükseltin. |
| 9022: WMI nesnesine erişim reddedildi. | Kullanıcı veya grup ilkesiyle ilişkili rol, kullanıcının WMI nesnesine erişimini kısıtlıyor. | Lütfen Microsoft Desteği başvurun. |
| 9023: sistem kök ortam değişkeni değeri boş olduğundan PowerShell çalıştırılamıyor. | Konuk sunucu için SystemRoot ortam değişkeninin değeri boş. | Bir çözüm için Microsoft Desteği başvurun. |
| 9024: GEÇICI ortam değişkeni değeri boş olduğu için bulunamıyor. | Konuk sunucu için GEÇICI ortam değişkeninin değeri boş. | Lütfen Microsoft Desteği başvurun. |
| 9025: PowerShell, Konuk sunucularda bozuk. | PowerShell, Konuk sunucuda bozuk. | PowerShell 'i Konuk sunucuya yeniden yükleyin ve PowerShell 'in Konuk sunucuda çalıştırılabildiğini doğrulayın. |
| 9026: sunucuda konuk işlemleri çalıştırılamıyor. | Sunucu durumu, Konuk işlemlerin sunucuda çalıştırılmasına izin vermiyor. | Bir çözüm için Microsoft Desteği başvurun. |
| 9027: Konuk işlem Aracısı sunucuda çalışmıyor. | Sanal sunucu içinde çalışan konuk işlem aracısıyla iletişim kurulamadı. | Bir çözüm için Microsoft Desteği başvurun. |
| 9028: sunucuda yetersiz disk depolaması nedeniyle dosya oluşturulamıyor. | Diskte yeterli alan yok. | Sunucunun disk depolamada yeterli kullanılabilir alan olduğundan emin olun. |
| 9029: belirtilen Konuk sunucu kimlik bilgilerinde PowerShell 'e erişim yok. | PowerShell 'e erişim kullanıcı için kullanılamaz. | Gereçte eklenen kullanıcının PowerShell 'in Konuk sunucuda erişebildiğinden emin olun. |
| 9030: ESXi konağının bağlantısı kesildiğinden keşfedilen meta veriler toplanamıyor. | ESXi ana bilgisayarı bağlantısı kesik durumda. | Sunucuyu çalıştıran ESXi konağının bağlı olduğundan emin olun. |
| 9031: ESXi Konağı yanıt vermediğinden keşfedilen meta veriler toplanamıyor. | Uzak ana bilgisayar geçersiz durumda. | Sunucuyu çalıştıran ESXi konağının çalıştığından ve bağlı olduğundan emin olun. |
| 9032: bir iç hata nedeniyle bulunamıyor. | Sorun, bir iç hata nedeniyle oluşabilir. | Bir çözüm için Microsoft Desteği başvurun. |
| 9033: sunucu Kullanıcı adı geçersiz karakterler içerdiğinden bulunamıyor. | Kullanıcı adında geçersiz karakterler algılandı. | Geçersiz karakter olmadığından emin olmak için sunucu kimlik bilgisini yeniden sağlayın. |
| 9034: belirtilen Kullanıcı adı UPN biçiminde değil. | Kullanıcı adı UPN biçiminde değil. | Kullanıcı adının Kullanıcı asıl adı (UPN) biçiminde olduğundan emin olun. |
| 9035: PowerShell dil modu ' Full Language ' olarak ayarlı olmadığından bulunamıyor. | Konuk sunucudaki PowerShell için dil modu tam dil olarak ayarlanmadı. | PowerShell dil modunun ' Full Language ' olarak ayarlandığından emin olun. |
| 9037: sunucu yanıt süresi çok yüksek olduğu için veri toplama geçici olarak duraklatıldı. | Keşfedilen sunucunun yanıt vermesi çok uzun sürüyor | Eylem gerekmiyor. Yazılım envanteri bulma ve bağımlılık Analizi (aracısız) için 3 saat boyunca 24 saat içinde yeniden deneme denenecek. |
| 10000: işletim sistemi türü desteklenmiyor. | Sunucu üzerinde çalışan işletim sistemi Windows veya Linux değildir. | Desteklenen işletim sistemi türleri yalnızca Windows ve Linux. |
| 10001: sunucu bulma betiği gereç üzerinde bulunamadı. | Bulma beklendiği gibi çalışmıyor. | Bir çözüm için Microsoft Desteği başvurun. |
| 10002: bulma görevi zamanında tamamlanmadı. | Bulma Aracısı beklendiği gibi çalışmıyor. | Sorun, 24 saat içinde otomatik olarak çözümlenmelidir. Sorun devam ederse, Microsoft Desteği başvurun. |
| 10003: bulma görevinin yürütüldüğü işleme bir hatayla çıkıldı. | Bulma görevinin yürütüldüğü işleme bir hatayla çıkıldı. | Sorun, 24 saat içinde otomatik olarak çözümlenmelidir. Sorun devam ederse lütfen Microsoft Desteği başvurun. |
| 10004: Konuk işletim sistemi türü için kimlik bilgisi sağlanmadı. | Bu işletim sistemi türünde sunuculara erişim kimlik bilgileri, Azure geçiş gereci 'nda sağlanmadı. | Gereç üzerindeki sunucular için kimlik bilgileri ekleme |
| 10005: girilen kimlik bilgileri geçerli değil. | Gereç için sunucu erişimi için belirtilen kimlik bilgileri yanlış. | Gereçte sunulan kimlik bilgilerini güncelleştirin ve kimlik bilgilerini kullanarak sunucuya erişilebilir olduğundan emin olun. |
| 10006: Konuk işletim sistemi türü kimlik bilgisi deposu tarafından desteklenmiyor. | Sunucu üzerinde çalışan işletim sistemi Windows veya Linux değildir. | Desteklenen işletim sistemi türleri yalnızca Windows ve Linux. |
| 10007: keşfedilen meta veriler işlenemiyor. | JSON seri durumdan çıkarılmaya çalışılırken hata oluştu. | Bir çözüm için Microsoft Desteği başvurun. |
| 10008: sunucuda bir dosya oluşturulamıyor. | Sorun bir iç hata nedeniyle oluşabilir. | Bir çözüm için Microsoft Desteği başvurun. |
| 10009: keşfedilen meta veriler sunucudaki bir dosyaya yazılamıyor. | Sorun, bir iç hata nedeniyle oluşabilir. | Bir çözüm için Microsoft Desteği başvurun. |

## <a name="common-sql-server-instances-and-database-discovery-errors"></a>Ortak SQL Server örnekleri ve veritabanı bulma hataları

Azure geçişi, Azure geçişi: bulma ve değerlendirme kullanılarak şirket içi makinelerde çalışan SQL Server örneklerinin ve veritabanlarının bulunmasını destekler. SQL bulma Şu anda yalnızca VMware için destekleniyor. Başlamak için [bulma](tutorial-discover-vmware.md) öğreticisine bakın.

Tipik SQL bulma hataları tabloda özetlenmiştir.

| **Hata** | **Neden** | **Eylem** |
|--|--|--|
|30000: Bu SQL Server ilişkili kimlik bilgileri çalışmadı.|El ile ilişkili kimlik bilgileri geçersiz veya otomatik olarak ilişkilendirilen kimlik bilgileri artık SQL Server erişemez.|Gereç üzerinde SQL Server kimlik bilgileri ekleyin ve sonraki SQL bulma döngüsüne veya yenilemeyi zorlamaya kadar bekleyin.|
|30001: gereç SQL Server bağlantı kurulamıyor.|1. gereç SQL Server için ağ üzerinde bir görüş satırı yok.<br/>2. SQL Server ile gereç arasındaki bağlantıyı engelleyen güvenlik duvarı.|1. SQL Server gereç 'tan erişilebilir hale getirin.<br/>2. gerecden gelen bağlantılara SQL Server izin verin.|
|30003: sertifika güvenilir değil.|SQL Server çalıştıran bilgisayarda güvenilir bir sertifika yüklü değil.|Lütfen sunucuda güvenilir bir sertifika ayarlayın. [Daha fazla bilgi edinin](https://go.microsoft.com/fwlink/?linkid=2153616)|
|30004: Izinler yetersiz.|SQL Server örneklerini taramak için gereken izinlerin bulunmaması nedeniyle bu hata oluşabilir. |SQL Server örnekleri ve veritabanlarını bulmak için gereç üzerinde sunulan kimlik bilgileri/hesaba sysadmin rolü verin. [Daha fazla bilgi edinin](https://go.microsoft.com/fwlink/?linkid=2153511)|
|30005: SQL Server oturum açma, varsayılan ana veritabanı ile ilgili bir sorun nedeniyle bağlanamadı.|Veritabanı geçersiz ya da oturum açma, veritabanında bağlanma iznine sahip değil.|Varsayılan veritabanını ana veritabanına ayarlamak için ALTER LOGıN kullanın.<br/>SQL Server örnekleri ve veritabanlarını bulmak için gereç üzerinde sunulan kimlik bilgileri/hesaba sysadmin rolü verin. [Daha fazla bilgi edinin](https://go.microsoft.com/fwlink/?linkid=2153615)|
|30006: SQL Server oturum açma, Windows kimlik doğrulamasıyla kullanılamaz.|1. oturum açma SQL Server bir oturum açma olabilir, ancak sunucu yalnızca Windows kimlik doğrulamasını kabul eder.<br/>2. SQL Server kimlik doğrulaması kullanarak bağlanmaya çalışıyorsunuz, ancak kullanılan oturum açma SQL Server yok.<br/>3. oturum açma Windows kimlik doğrulaması kullanabilir, ancak oturum açma tanınmayan bir Windows sorumlusuyla yapılır. Tanınmayan bir Windows sorumlusu, oturum açmanın Windows tarafından doğrulanamadığı anlamına gelir. Bunun nedeni, Windows oturumunun güvenilmeyen bir etki alanından olması olabilir.|SQL Server kimlik doğrulaması kullanarak bağlanmaya çalışıyorsanız, SQL Server karma kimlik doğrulama modunda yapılandırıldığını ve SQL Server oturum açma var olduğunu doğrulayın.<br/>Windows kimlik doğrulamasını kullanarak bağlanmaya çalışıyorsanız doğru etki alanında doğru şekilde oturum açtığınızı doğrulayın. [Daha fazla bilgi edinin](https://go.microsoft.com/fwlink/?linkid=2153421)|
|30007: parola zaman aşımına uğradı.|Hesabın parolasının süresi doldu.|SQL Server oturum açma parolasının süresi doldu, parolayı yeniden ayarlayıp/veya parola sona erme tarihini uzatamazsınız. [Daha fazla bilgi edinin](https://go.microsoft.com/fwlink/?linkid=2153419)|
|30008: parolanın değiştirilmesi gerekiyor.|Hesabın parolasının değiştirilmesi gerekiyor.|SQL Server bulma için belirtilen kimlik bilgisinin parolasını değiştirin. [Daha fazla bilgi edinin](https://go.microsoft.com/fwlink/?linkid=2153318)|
|30009: bir iç hata oluştu.|SQL Server örnekleri ve veritabanları bulunurken iç hata oluştu. |Sorun devam ederse lütfen Microsoft desteği 'ne başvurun.|
|30010: veritabanı bulunamadı.|Seçili sunucu örneğinden herhangi bir veritabanı bulunamadı.|SQL veritabanlarını bulmak için gereçte belirtilen kimlik bilgilerine/hesaba sysadmin rolü verin.|
|30011: bir SQL örneği veya veritabanı değerlendirilirken bir iç hata oluştu.|Değerlendirme gerçekleştirilirken iç hata oluştu.|Sorun devam ederse lütfen Microsoft desteği 'ne başvurun.|
|30012: SQL bağlantısı başarısız oldu.|1. sunucudaki güvenlik duvarı bağlantıyı reddetti.<br/>2. SQL Server Browser hizmeti (sqlbrowser) başlatılmadı.<br/>3. sunucu büyük olasılıkla başlatılmadığı için SQL Server istemci isteğine yanıt vermedi.<br/>4. SQL Server istemcisi sunucuya bağlanamıyor. Sunucu uzak bağlantıları kabul edecek şekilde yapılandırılmadığı için bu hata oluşabilir.<br/>5. SQL Server istemcisi sunucuya bağlanamıyor. İstemci sunucunun adını çözümleyemediği veya sunucu adı yanlış olduğu için hata oluşabilir.<br/>6. TCP veya adlandırılmış kanal protokolleri etkin değildir.<br/>7. belirtilen SQL Server örneği adı geçerli değil.|Bağlantı sorununu gidermek için lütfen [Bu](https://go.microsoft.com/fwlink/?linkid=2153317) etkileşimli kullanıcı kılavuzunu kullanın. Lütfen hizmette güncelleştirilecek verilerin kılavuzunu tamamladıktan sonra 24 saat bekleyin. Sorun devam ederse lütfen Microsoft desteği 'ne başvurun.|
|30013: SQL Server örneğiyle bağlantı kurulurken bir hata oluştu.|1. SQL Server adı gereç tarafından çözümlenemiyor.<br/>2. SQL Server uzak bağlantılara izin vermez.|SQL Server 'ı gerecten ping yapabiliyorsanız, bu sorunun otomatik olarak çözümlendiğini denetlemek için lütfen 24 saat bekleyin. Değilse, lütfen Microsoft desteği 'ne başvurun. [Daha fazla bilgi edinin](https://go.microsoft.com/fwlink/?linkid=2153316)|
|30014: Kullanıcı adı veya parola geçersiz.| Bu hata, hatalı bir parola veya Kullanıcı adı içeren bir kimlik doğrulama hatası nedeniyle oluşabilir.|Lütfen geçerli bir Kullanıcı adı ve parola ile kimlik bilgisi girin. [Daha fazla bilgi edinin](https://go.microsoft.com/fwlink/?linkid=2153315)|
|30015: SQL örneği bulunurken bir iç hata oluştu.|SQL örneği bulunurken bir iç hata oluştu.|Sorun devam ederse lütfen Microsoft desteği 'ne başvurun.|
|30016: '% Instance; ' örneğine olan bağlantı bir zaman aşımı nedeniyle başarısız oldu.| Bu, sunucuda güvenlik duvarı bağlantıyı reddederse meydana gelebilir.|SQL Server güvenlik duvarının bağlantıları kabul edecek şekilde yapılandırılıp yapılandırılmadığını doğrulayın. Hata devam ederse, lütfen Microsoft desteğine başvurun. [Daha fazla bilgi edinin](https://go.microsoft.com/fwlink/?linkid=2153611)|
|30017: iç hata oluştu.|İşlenmeyen özel durum.|Sorun devam ederse lütfen Microsoft desteği 'ne başvurun.|
|30018: iç hata oluştu.|SQL örneğinin geçici DB boyutu, dosya boyutu gibi veriler toplanırken bir iç hata oluştu.|Lütfen 24 saat bekleyin ve sorun devam ederse Microsoft desteği 'ne başvurun.|
|30019: bir iç hata oluştu.|Bir veritabanının veya örneğin bellek kullanımı gibi performans ölçümleri toplanırken bir iç hata oluştu.|Lütfen 24 saat bekleyin ve sorun devam ederse Microsoft desteği 'ne başvurun.|

## <a name="next-steps"></a>Sonraki adımlar

[VMware](how-to-set-up-appliance-vmware.md), [Hyper-V](how-to-set-up-appliance-hyper-v.md)veya [fiziksel sunucular](how-to-set-up-appliance-physical.md)için bir gereç ayarlayın.
