---
title: Adanmış HSM sorunlarını giderme-Azure ayrılmış HSM | Microsoft Docs
description: Azure adanmış HSM 'ye genel bakış, Azure 'da FIPS 140-2 düzey 3 sertifikasını karşılayan anahtar depolama olanakları sağlar
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.custom: mvc, seodec18
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: 0791f2e8d5119c2087286a24cf83b4259ee9e7af
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105611659"
---
# <a name="troubleshooting-the-azure-dedicated-hsm-service"></a>Azure ayrılmış HSM hizmeti sorunlarını giderme

Azure ayrılmış HSM hizmeti iki ayrı model içerir. İlk olarak, HSM cihazlarının Azure 'daki kayıt ve dağıtım, temel ağ bileşenleriyle birlikte. İkinci olarak, belirli bir iş yükü veya uygulamayla kullanım/tümleştirme hazırlığı için HSM cihazlarının yapılandırması. Thales [Luna 7 HSM](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms) cihazları Azure 'Da doğrudan Thales 'den satın alırken aynı olsa da, Azure 'daki bir kaynaktır ve bazı benzersiz hususlar oluşturulur. Bu konular ve sonuçta ortaya çıkan sorun giderme öngörüleri veya en iyi yöntemleri, önemli bilgilere yüksek görünürlük ve erişim sağlamak için burada belgelenmiştir. Hizmet kullanımda olduktan sonra doğrudan Microsoft veya Thales 'e yönelik destek istekleri aracılığıyla tanımlayıcı bilgilere erişilebilir. 

> [!NOTE]
> Yeni dağıtılan bir HSM cihazında herhangi bir yapılandırma gerçekleştirilmeden önce bu, ilgili düzeltme ekleriyle güncelleştirilmeleri gerektiğini not edilmelidir. Belirli bir gerekli düzeltme, sistemin yeniden başlatma sırasında yanıt vermeyen bir sorunu ele veren Thales destek portalında [KB0019789](https://supportportal.gemalto.com/csm?id=kb_article_view&sys_kb_id=19a81c8bdb9a1fc8d298728dae96197d&sysparm_article=KB0019789) .

## <a name="hsm-registration"></a>HSM kaydı

Adanmış HSM, bulutta donanım kaynakları sunarken kullanım için ücretsiz olarak kullanılamaz ve bu nedenle korunması gereken değerli bir kaynaktır. Bu nedenle, kullanarak e-posta aracılığıyla bir allowlısıting işlemi kullanıyoruz HSMrequest@microsoft.com . 

### <a name="getting-access-to-dedicated-hsm"></a>Adanmış HSM 'ye erişim sağlama

Önce kendinize hangi kullanım örneklerinin [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview) veya [Azure tarafından yönetilen HSM](https://docs.microsoft.com/azure/key-vault/managed-hsm/overview)tarafından giderilemediğini sorun. Daha sonra yalnızca adanmış HSM 'nin anahtar depolama gereksinimlerinize uygun olduğunu düşünmenize ve erişim istemek için e-posta adresine inirsiniz HSMrequest@microsoft.com . Uygulamanızın ve kullanım durumlarınızın yanı sıra, HSM 'leri istediğiniz bölgeleri ve aradığınız MSMS hacmini ana hatlarıyla yapın. Örneğin, hesap yöneticisi veya bulut çözümü mimarı gibi bir Microsoft temsilcisiyle çalışıyorsanız, bunları herhangi bir isteğe dahil edin.

## <a name="hsm-provisioning"></a>HSM sağlama

Azure 'da bir HSM cihazının sağlanması, CLı veya PowerShell aracılığıyla yapılabilir. Hizmet için kaydolurken, örnek bir ARM şablonu sağlanır ve ilk özelleştirme için yardım verilecek. 

### <a name="hsm-deployment-failure-information"></a>HSM dağıtım hatası bilgileri

Adanmış HSM, dağıtım için CLı ve PowerShell 'i destekler, böylece portal tabanlı hata bilgileri sınırlıdır ve ayrıntılı değildir. Kaynak Gezgini kullanarak daha iyi bilgi bulabilirsiniz. Portal giriş sayfasında bu bir simge bulunur ve daha ayrıntılı hata bilgileri mevcuttur. Bu bilgiler, dağıtım sorunlarıyla ilgili bir destek isteği oluştururken içine yapıştırılmadıysanız bir Lota yardımcı olur.

![Hata bilgileri](./media/troubleshoot/failure-information.png)

### <a name="hsm-subnet-delegation"></a>HSM alt ağı temsili
Dağıtım hatalarının bir nedeni, HSMs 'nin sağlanacağı müşteri tanımlı alt ağ için uygun temsilciyi ayarlamaya yöneliktir. Bu temsilcinin sanal ağın bir parçası ve dağıtım için alt ağ önkoşulları ve öğreticilerde daha fazla ayrıntı bulabilirsiniz.

![Alt ağ temsili](./media/troubleshoot/subnet-delegation.png)

### <a name="hsm-deployment-race-condition"></a>HSM dağıtımı yarış durumu

Dağıtımda sunulan standart ARM şablonunda HSM ve ExpressRoute ağ geçidi ile ilgili kaynaklar bulunur. Ağ kaynakları, başarılı bir HSM dağıtımına yönelik bir bağımlılıktır ve zamanlama önemli olabilir.  Bazen, bağımlılık sorunlarıyla ilgili dağıtım başarısızlıklarını gördük ve dağıtımı yeniden çalıştırmak genellikle sorunu çözer. Aksi takdirde, kaynakları silip yeniden dağıtım genellikle başarılı olur. Bunu yaptıktan ve sorun oluşmaya devam ettikten sonra, Azure portal "Azure kurulumunu yapılandırma sorunları" sorun türünü seçmek için bir destek isteği yükseltin.

### <a name="hsm-deployment-using-terraform"></a>Terrayform kullanan HSM dağıtımı

Bazı müşteriler, bu hizmete kaydolurken sağlanan ARM şablonları yerine bir Otomasyon ortamı olarak Terkform kullanmıştı. HSMs bu şekilde dağıtılamaz, ancak bağımlı ağ kaynakları olabilir. Terkform, yalnızca HSM dağıtımına sahip olan minimal bir ARM şablonuna çağrı yapan bir modüle sahiptir.  Bu durumda, gerekli ExpressRoute ağ geçidi gibi ağ kaynaklarının HSM 'leri dağıtılmadan önce tam olarak dağıtıldığından emin olmak için dikkatli olunması gerekir. Aşağıdaki CLı komutu, tamamlanmış dağıtımı test etmek ve gerektiğinde tümleşik hale eklemek için kullanılabilir. Özel adlandırmanın açılı ayraç yer tutucuları yerine koyun. "ProvisioningState başarılı oldu" sonucunu aramanız gerekir

```azurecli
az resource show --ids /subscriptions/<subid>/resourceGroups/<myresourcegroup>/providers/Microsoft.Network/virtualNetworkGateways/<myergateway>
```

### <a name="deployment-failure-based-on-quota"></a>Kota temelinde dağıtım hatası
Her bölge için 2 HSM/damga ve 4 HSM 'yi aşarsanız dağıtımlar başarısız olabilir. Bu durumdan kaçınmak için, yeniden dağıtım yapmadan önce başarısız olan dağıtımlardan kaynakları silmiş olduğunuzdan emin olun. Kaynakları denetlemek için aşağıdaki "Nasıl yaparım? See HSMs" öğesine bakın. Özellikle bir koruma gibi bu kotayı aşmanız gerektiğini düşünüyorsanız, lütfen ayrıntılara e-posta gönderin HSMrequest@microsoft.com .

### <a name="deployment-failure-based-on-capacity"></a>Kapasiteye dayalı dağıtım hatası
Belirli bir damga veya bölge dolduğunda, diğer bir deyişle, neredeyse tüm ücretsiz HSM 'ler sağlandığında, bu dağıtım hatalarına neden olabilir. Her damga, müşteriler için kullanılabilir 12 HSM 'ye sahiptir ve bu, bölge başına 24 anlamına gelir. Her bir damgada 2 yedek ve 1 test cihazı vardır. Sınır olduğunu düşünüyorsanız, HSMrequest@microsoft.com belirli damgaların Fill düzeyi hakkında bilgi için e-posta gönderin.

###  <a name="how-do-i-see-hsms-when-provisioned"></a>Nasıl yaparım?, sağlanan HSM 'leri görmek mi istiyorsunuz?
Ayrılmış HSM 'nin allowlistelenmiş bir hizmet olması nedeniyle, Azure portal bir "gizli tür" olarak kabul edilir. HSM kaynaklarını görmek için aşağıda gösterildiği gibi "gizli türleri göster" onay kutusunu denetlemeniz gerekir. NIC kaynağı her zaman HSM 'yi izler ve bağlanmak için SSH kullanılmadan önce HSM 'nin IP adresini bulmak için iyi bir yerdir.

![Gizli türleri göster denetimini vurgulayan ekran görüntüsü](./media/troubleshoot/hsm-provisioned.png)

## <a name="networking-resources"></a>Ağ kaynakları

Adanmış HSM dağıtımı, ağ kaynaklarına bir bağımlılığa ve farkında olmak üzere bazı bazı ilgili bazı sınırlamaları vardır.

### <a name="provisioning-expressroute"></a>ExpressRoute sağlama

Adanmış HSM, ExpressRoute ağ geçidini, müşterinin özel IP adresi alanı ve bir Azure veri merkezinde fiziksel HSM arasındaki iletişim için "tünel" olarak kullanır.  VNET başına bir ağ geçidi kısıtlaması olduğunu düşündüğünde, ExpressRoute aracılığıyla şirket içi kaynaklarıyla bağlantı gerektiren müşterilerin o bağlantı için başka bir VNET kullanması gerekir.  

### <a name="hsm-private-ip-address"></a>HSM özel IP adresi

Adanmış HSM için sağlanan örnek şablonlar, HSM IP 'nin belirli bir alt ağ aralığından otomatik olarak alınacağını varsayar. ARM şablonunda bir "NetworkInterfaces" özniteliği aracılığıyla HSM için açık bir IP adresi belirtebilirsiniz. 

![Adanmış HSM için örnek şablonu gösteren ekran görüntüsü.](./media/troubleshoot/private-ip-address.png)

## <a name="hsm-initialization"></a>HSM başlatması

Başlatma, kullanım için yeni bir HSM veya yeniden kullanım için mevcut bir HSM hazırlar. Nesneleri oluşturabilmeniz veya depolayabilmeniz, istemcilerin bağlanmasına izin vermeniz veya şifreleme işlemleri gerçekleştirebilmesi için HSM 'nin başlatılması tamamlanmış olmalıdır.

### <a name="lost-credentials"></a>Kayıp kimlik bilgileri

Kabuk Yöneticisi parolasının kaybolması, HSM anahtar malzemesinin kaybedilmesine neden olur. HSM 'yi sıfırlamak için bir destek isteği yapılmalıdır.
HSM 'yi başlatırken, kimlik bilgilerini güvenli bir şekilde depolayın. Kabuğun ve HSM kimlik bilgilerinin, şirketinizin ilkelerine uygun şekilde tutulması gerekir.

### <a name="failed-logins"></a>Başarısız oturum açma işlemleri

HSM 'lerde hatalı kimlik bilgilerinin sağlanması bozucu sonuçlara sahip olabilir. HSM rollerinin varsayılan davranışları aşağıda verilmiştir.

| Rol | Eşik (Deneb sayısı) | Çok sayıda hatalı oturum açma denemesi sonucu | Kurtarma |
|--|--|--|--|
| HSM | 3 |  HSM sıfır (tüm HSM nesne kimlikleri ve tüm bölümler kayboluyor)  |  HSM yeniden başlatılmalıdır. İçerikler, yedeklemeden geri yüklenebilir. | 
| Bölüm | 10 |  Bölüm sıfırlama işlemi. |  Bölüm yeniden başlatılmalıdır. İçerik, yedekten geri yüklenebilir. |  
| Denetim | 10 | Özelliğini | 10 dakika sonra otomatik olarak açıldı. |  
| Şifre müdürü | 10 (azaltılabilir) | HSM ilkesi 15: Enable bölüm PIN 'inin sıfırlanması 1 (etkin) olarak ayarlandıysa, CO ve CU rolleri kilitlenir.<br>HSM ilkesi 15: Enable bölüm PIN 'inin sıfırlanması 0 (devre dışı) olarak ayarlandıysa, CO ve CU rolleri kalıcı olarak kilitlenir ve bölüm içerikleri artık erişilebilir değildir. Bu varsayılan ayardır. | ORTAK rolün kilidi açık olmalıdır ve bu nedenle bölüm tarafından kimlik bilgileri sıfırlanarak kullanılarak `role resetpw -name co` .<br>Bölüm yeniden başlatılmalıdır ve bir yedekleme aygıtından anahtar malzeme geri yüklendi. |  

## <a name="hsm-configuration"></a>HSM yapılandırması 

Aşağıdaki öğeler, yapılandırma hatalarının yaygın olduğu veya çağırma açısından önemli bir etkiye sahip olduğu durumdur:

### <a name="hsm-documentation-and-software"></a>HSM belgeleri ve yazılımı
[Thales Luna 7 HSM](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms) cihazları için yazılım ve belgeler Microsoft 'tan edinilemez ve doğrudan Thales 'den indirilmelidir. Kayıt işlemi sırasında alınan Thales müşteri KIMLIĞI kullanılarak kayıt gereklidir. Microsoft tarafından sağlandığı şekilde cihazların yazılım sürümü 7,2 ve bellenim sürümü 7.0.3 vardır. 2020 Thales 'in başlarında belgeleri herkese açıktır ve [burada](https://thalesdocs.com/gphsm/luna/7.2/docs/network/Content/Home_network.htm)bulunabilir.  

### <a name="hsm-networking-configuration"></a>HSM ağ yapılandırması

HSM içindeki ağı yapılandırırken dikkatli olun.  HSM 'nin, ExpressRoute ağ geçidi aracılığıyla bir müşterinin özel IP adresi alanından doğrudan HSM 'ye bağlantısı vardır.  Bu iletişim kanalı yalnızca müşteri iletişimine yöneliktir ve Microsoft hiç erişime sahip değildir. HSM bu ağ yolunun etkilenmesine benzer bir şekilde yapılandırılırsa, bu, HSM ile olan tüm iletişimin kaldırıldığı anlamına gelir.  Bu durumda tek seçenek, cihazın sıfırlanması için Azure portal aracılığıyla bir Microsoft destek isteği yükseltmektir. Bu sıfırlama yordamı, HSM 'yi ilk durumuna geri ayarlar ve tüm yapılandırma ve anahtar malzemeler kaybedilir.  Yapılandırma yeniden oluşturulmalıdır ve cihaz, HA grubuna katıldığında, anahtar malzemesini çoğaltacaktır.  

### <a name="hsm-device-reboot"></a>HSM cihazının yeniden başlatılması

Bazı yapılandırma değişiklikleri, HSM 'nin güç ışığını veya yeniden başlatılmasını gerektirir. Azure 'da HSM 'nin Microsoft testi, yeniden başlatmanın yanıt vermemesine neden olduğunu belirledi. Bu, sabit yeniden başlatma isteğinde bulunan Azure portal bir destek isteğinin oluşturulması ve bir Azure veri merkezinde el ile gerçekleştirilen bir işlem olduğunu düşünmeye 48 saat kadar sürebilir.  Bu durumdan kaçınmak için, Thales 'ten doğrudan yeniden başlatma düzeltme ekini dağıttığınızdan emin olun. Sistemin yeniden başlatma sırasında yanıt vermediği bir sorun için önerilen bir düzeltme eki için Thales Luna 7 HSM 7,2 Indirmelerinde [KB0019789](https://supportportal.gemalto.com/csm?sys_kb_id=d66911e2db4ffbc0d298728dae9619b0&id=kb_article_view&sysparm_rank=1&sysparm_tsqueryId=d568c35bdb9a4850d6b31f3b4b96199e&sysparm_article=KB0019789) adresine bakın (örneğin, Indirmek Için [Thales müşteri destek portalına](https://supportportal.thalesgroup.com/csm) kaydolmanız gerekir).

### <a name="ntls-certificates-out-of-sync"></a>NTLS sertifikaları eşitlenmemiş
Bir sertifikanın süresi dolarsa veya yapılandırma güncelleştirmeleriyle üzerine yazıldığında, istemci bir HSM bağlantısını kaybedebilir. Sertifika değişimi istemci yapılandırması her HSM ile yeniden uygulanmalıdır.
Geçersiz sertifikayla oturum açan örnek:

> NTLS [8508]: bilgi: 0: gelen bağlantı isteği...: 192.168.50.2/59415 NTLS [8508]: SSLAccept hata iletisi: hata: 14094418: SSL yordamları: ssl3_read_bytes uyarı bilinmeyen CA NTLS [8508]: SSL kabul etme sırasında hata (RC_SSL_ERROR) NTLS [8508]: INFO: 0xc0000711: istemciyle güvenli kanal kurulamadı: 192.168.50.2/59415: RC_SSL_FAILED_HANDSHAKE NTLS [8508]: bilgi: 0: NTLS Istemci "bilinmeyen konak adı" bağlantı örneği kaldırıldı: 192.168.50.2/59415

### <a name="failed-tcp-communication"></a>Başarısız TCP Iletişimi

Luna Istemci yüklemesinden HSM 'ye iletişimin en düşük TCP bağlantı noktası 1792 ' de olması gerekir. Ortamda herhangi bir ağ yapılandırması değiştiği için bunu göz önünde bulundurun.

### <a name="failed-ha-group-member-doesnt-recover"></a>Başarısız HA grubu üyesi kurtarılamadı

Başarısız bir HA grubu üyesi kurtarılmazsa, hagroup Recover komutunu kullanarak Luna istemcisinden el ile kurtarılması gerekir.
Otomatik kurtarmayı etkinleştirmek için bir HA grubu için yeniden deneme sayısı yapılandırmak gerekir. Varsayılan olarak, bir HA grubu, kurtarıldığında bir HA üyesini gruba kurtarmaya çalışır.

### <a name="ha-group-doesnt-sync"></a>HA grubu eşitlenmedi

Üye bölümlerinin aynı kopyalama etki alanına sahip olmadığı durumlarda, ha Synchronize komutu şunları görüntüler: Uyarı: Synchronize başarısız olabilir.  Yuva 0 ve yuva 1 ' deki Üyeler özel anahtar kopyalama için çakışan ayarlara sahiptir.
Doğru kopyalama etki alanına sahip yeni bir bölüm, HA grubuna eklenmelidir, ardından yanlış yapılandırılmış bölüm kaldırılır.

## <a name="hsm-deprovisioning"></a>HSM sağlamasını kaldırma 

Yalnızca bir HSM ile tam olarak bitmemişse, Microsoft bunu sıfırlar ve ücretsiz bir havuza geri döndürür. 

### <a name="how-to-delete-an-hsm-resource"></a>Bir HSM kaynağını silme

HSM 'nin "sıfırlama" durumunda olmadığı durumlar için Azure kaynağı silinemez.  Bu nedenle, bir kaynak olarak silinmeye çalışılırken tüm anahtar materyalinin silinmesi gerekir. En hızlı sıfırlama yolu, HSM Yönetici parolasının yanlış 3 kez alınacağını (Not: Bu, Gereç düzeyi Yöneticisi değil, HSM yöneticisini ifade eder). Luna kabuğu, `hsm -factoryreset` sıfırlama komutuna sahiptir ancak yalnızca seri bağlantı noktasında konsol aracılığıyla yürütülebilir ve müşterilerin buna erişimi yoktur.

## <a name="next-steps"></a>Sonraki adımlar

Bu makale, HSM dağıtım yaşam döngüsü genelinde sorunlar yaşamış veya sorun giderme ya da dikkatli bir dikkat gerektiren alanlara Öngörüler sağlamıştır. Bu makalede, gereksiz gecikmelerin ve kaçınmanın önlenmesine ve ilgili eklemelere veya değişikliklere sahip olmanız durumunda Microsoft ile bir destek talebi oluşturup bize bilgi vermemize yardımcı olur. 
