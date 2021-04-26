---
title: Azure IoT Edge Güvenlik Yöneticisi-Azure IoT Edge
description: IoT Edge cihaz güvenliği desteklemeyle ve güvenlik hizmetlerinin bütünlüğünü yönetir.
services: iot-edge
keywords: Güvenlik, güvenli öğe, şifreleme, t IoT Edge
author: eustacea
manager: philmea
ms.author: eustacea
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: ed73c114c015ce38fefa02d2b71e27e8f85dca39
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028681"
---
# <a name="azure-iot-edge-security-manager"></a>Azure IoT Edge Güvenlik Yöneticisi

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Azure IoT Edge Güvenlik Yöneticisi, güvenli Silicon Hardware 'i soyutlayarak IoT Edge cihazı ve tüm bileşenlerini korumak için iyi bir güvenlik çekirdedir. Güvenlik Yöneticisi, güvenlik sağlamlaştırma için odak noktasıdır ve özgün ekipman üreticilerine (OEM) teknoloji tümleştirme noktası sağlar.

![Azure IoT Edge Güvenlik Yöneticisi](media/edge-security-manager/iot-edge-security-manager.png)

IoT Edge cihazının bütünlüğünü ve tüm devralınmış yazılım işlemlerini savunmak için Güvenlik Yöneticisi amaçlar IoT Edge. Güvenlik Yöneticisi, IoT Edge çalışma zamanını önyüklemek ve devam eden işlemleri izlemek için, güven donanımının temel alınan donanım kökünden (varsa) güven geçişi yapar.  IoT Edge Güvenlik Yöneticisi, mümkün olan en yüksek güvenlik önlemlerinin sağlanmasına yardımcı olmak üzere güvenli bir Silicon Hardware (varsa) ile birlikte çalışır.  

IoT Edge Güvenlik Yöneticisi sorumlulukları şunlardır, ancak bunlarla sınırlı değildir:

* Azure IoT Edge cihazının güvenli ve ölçülmüş önyüklemesi.
* Cihaz kimliği sağlama ve uygun olduğunda güvenin geçişi.
* Cihaz sağlama hizmeti gibi bulut hizmetlerinin cihaz bileşenlerini barındırın ve koruyun.
* Benzersiz kimliklerle IoT Edge modülleri güvenli bir şekilde sağlayın.
* Nosel hizmetler aracılığıyla cihazın donanım köküne güvenin.
* Çalışma zamanında IoT Edge işlemlerinin bütünlüğünü izleyin.

IoT Edge Güvenlik Yöneticisi üç bileşeni içerir:

* IoT Edge güvenlik cini.
* Donanım güvenlik modülü platformu soyutlama katmanı (HSM PAL).
* İsteğe bağlı, ancak önerilen donanım Silicon Trust veya HSM 'nin kökü.

## <a name="the-iot-edge-security-daemon"></a>IoT Edge güvenlik arka plan programı

IoT Edge güvenlik arka plan programı, IoT Edge Security Manager 'ın mantıksal işlemlerinden sorumludur. IoT Edge cihazının güvenilir bilgi işlem tabanının önemli bir bölümünü temsil eder.

### <a name="design-principles"></a>Tasarım ilkeleri

IoT Edge güvenlik arka plan programı iki temel ilkeden daha sonra çalışır: işlemsel bütünlüğü en üst düzeye çıkarın ve oluşan şişirmeyi ve dalgalanma

#### <a name="maximize-operational-integrity"></a>İşlemsel bütünlüğü büyüt

IoT Edge güvenlik arka plan programı, belirli bir güven donanımı kökünün savunma yeteneği dahilinde mümkün olan en yüksek bütünlüğü ile çalışır. Uygun tümleştirmeyle, güven donanımının kökü, güvenlik cini statik olarak ve çalışma zamanında yeniden izinsiz değişiklik yapmak için ölçer ve izler.

Fiziksel erişim, IoT cihazlarına yönelik her zaman bir tehdittir. Güvenin donanım kökü, IoT Edge güvenlik arka plan 'un bütünlüğünü erteleme bölümünde önemli bir rol oynar.  Güvenin donanım kökü iki değişken olarak gelir:

* gizli bilgiler ve şifreleme anahtarları gibi hassas bilgilerin korunması için güvenli öğeler.
* anahtarlar gibi gizli dizileri ve ölçüm ve faturalandırma gibi hassas iş yüklerinin korunması için güvenli şifreleme.

Güvenin donanım kökünü kullanmak için iki tür yürütme ortamı vardır:

* Hassas bilgileri korumak için güvenli öğelerin kullanımına dayanan standart ya da zengin yürütme ortamı (REE).
* Hassas bilgileri korumak ve yazılım yürütmeye koruma sunmak için güvenli şifreleme teknolojisini kullanan güvenilir yürütme ortamı (t).

Güvenli kuşanın donanım kökü olarak kullanıldığı cihazlarda, IoT Edge güvenlik cini içindeki hassas mantık kuşatma içinde olmalıdır.  Güvenlik arka plan programının hassas olmayan kısımları t 'nin dışında olabilir.  Herhangi bir durumda, özgün tasarım üreticileri (ODM) ve orijinal ekipman üreticileri (OEM), önyükleme ve çalışma zamanında IoT Edge güvenlik arka plan 'un bütünlüğünü ölçmek ve savunmak için HSM 'lerden güven genişletmelidir.

#### <a name="minimize-bloat-and-churn"></a>Oluşan şişirmeyi ve karmaşıklığı en aza indir

IoT Edge güvenlik arka plan programı için başka bir temel ilke, karmaşıklığı en aza indirmektir.  En yüksek güven düzeyi için IoT Edge güvenlik arka plan programı, güvenilir bir şekilde cihaz donanımı köküne sahiptir ve yerel kod olarak çalışır.  Bu gerçekçi türler, bazı senaryolarda zor olabilen güvenli güncelleştirme yollarının (işletim sistemi tarafından sunulan güncelleştirme mekanizmalarının aksine) donanım kökü aracılığıyla Daemon yazılımını güncelleştirmek için yaygındır.  IoT cihazları için güvenlik yenilemesi önerilse de, aşırı güncelleştirme gereksinimleri veya büyük güncelleştirme yükleri tehdit yüzeyini birçok şekilde genişletebilirler.  Örnek, işlemsel kullanılabilirliği veya güven donanımının kökünü en üst düzeye çıkarmak için güncelleştirmelerin atlanmasını, büyük güncelleştirme yüklerini işlemeye yönelik olarak çok kısıtlamalı  Bu nedenle, IoT Edge güvenlik arka plan programının tasarımı, parmak izini ve güvenilir bilgi işlem temelini korumak ve güncelleştirme gereksinimlerini en aza indirmek için kısa bir işlemdir.

### <a name="architecture-of-iot-edge-security-daemon"></a>IoT Edge güvenlik cini mimarisi

![Azure IoT Edge güvenlik cini](media/edge-security-manager/iot-edge-security-daemon.png)

IoT Edge güvenlik arka plan programı, güvenlik sağlamlaştırma için herhangi bir güven teknolojisinin kullanılabilir donanım kökünden yararlanır.  Ayrıca, donanım teknolojileri güvenilir yürütme ortamları sundukları zaman standart/zengin yürütme ortamı (REE) ve güvenilir bir yürütme ortamı (t) arasında bölünmüş bir işleme de olanak tanır. Role özgü arabirimler, IoT Edge cihazının ve işlemlerinin bütünlüğünü güvence altına almak için IoT Edge ana bileşenlerini etkinleştirir.

#### <a name="cloud-interface"></a>Bulut arabirimi

Bulut arabirimi, IoT Edge güvenlik arka plan programı, bulut Tebrikler gibi bulut hizmetlerine güvenlik yenilemesi gibi cihaz güvenliğine erişmesini sağlar.  Örneğin, IoT Edge güvenlik arka plan programı Şu anda bu arabirimi kullanarak cihaz kimliği yaşam döngüsü yönetimi için Azure IoT Hub [cihaz sağlama hizmeti](../iot-dps/index.yml) 'ne erişin.  

#### <a name="management-api"></a>Yönetim API'si

IoT Edge güvenlik arka plan programı, bir IoT Edge modülünü oluştururken/başlatırken/kaldırırken IoT Edge Aracısı tarafından çağrılan bir yönetim API 'SI sunar. Güvenlik arka plan programı, tüm etkin modüller için "kayıtlar" depolar. Bu kayıtlar modülün kimliğini modülün bazı özellikleriyle eşler. Örnekler için, bu modül özellikleri kapsayıcıda çalışan işlemin işlem tanımlayıcısını (PID) ve Docker kapsayıcısının içeriklerinin karmasını içerir.

Bu özellikler, çağıranın bir eylem için yetkilendirildiğini doğrulamak üzere iş yükü API 'SI (aşağıda açıklanmıştır) tarafından kullanılır.

Yönetim API 'si, yalnızca IoT Edge aracılarından çağrılabilir olan ayrıcalıklı bir API 'dir.  IoT Edge güvenlik cini önyükleme ve IoT Edge aracıyı başlattığı için, IoT Edge aracısının kurcalanmadığını doğrular ve IoT Edge Aracısı için örtülü bir kayıt oluşturabilir. İş yükü API 'sinin kullandığı kanıtlama işlemi aynı zamanda yönetim API 'sine erişimi yalnızca IoT Edge aracısına kısıtlar.

#### <a name="container-api"></a>Kapsayıcı API 'SI

Kapsayıcı API 'SI, Moby veya Docker gibi modül yönetimi için kullanılan kapsayıcı sistemle etkileşime girer.

#### <a name="workload-api"></a>İş yükü API 'SI

İş yükü API 'sine tüm modüller erişebilir. Bir HSM 'nin imzalı belirteci veya x509 sertifikası ile karşılık gelen güven paketini bir modüle, kimlik kanıtı sağlar. Güven paketi, modüllerin güveneceği diğer tüm sunucular için CA sertifikaları içerir.

IoT Edge güvenlik arka plan programı, bu API 'yi korumak için bir kanıtlama işlemi kullanır. Bir modül bu API 'yi çağırdığında, güvenlik cini kimlik için bir kayıt bulmaya çalışır. Başarılı olursa, modülü ölçmek için kaydın özelliklerini kullanır. Ölçüm işleminin sonucu kayıtla eşleşiyorsa, yeni bir kimlik kanıtı oluşturulur. Karşılık gelen CA sertifikaları (güven paketi) modüle döndürülür.  Modül IoT Hub, diğer modüllere bağlanmak veya bir sunucu başlatmak için bu sertifikayı kullanır. İmzalanmış belirteç veya sertifika süre sonu yaklaştığında, bu, modülün yeni bir sertifika istemesi için sorumluluğundadır.

### <a name="integration-and-maintenance"></a>Tümleştirme ve bakım

Microsoft, [GitHub üzerinde IoT Edge güvenlik arka plan programı](https://github.com/Azure/iotedge/tree/master/edgelet)için ana kod temelini saklar.

#### <a name="installation-and-updates"></a>Yükleme ve güncelleştirmeler

IoT Edge güvenlik arka plan programının yüklenmesi ve güncelleştirmeleri, işletim sisteminin paket yönetim sistemi üzerinden yönetilir. Donanım köküne sahip IoT Edge cihazlar, güvenli önyükleme ve güncelleştirme yönetimi sistemleri aracılığıyla yaşam döngüsünü yöneterek arka plan 'un bütünlüğünden daha fazla sağlamlaştırma sağlamalıdır. Cihaz üreticileri bu avenilgili cihaz özelliklerine göre araştırmalıdır.

#### <a name="versioning"></a>Sürüm Oluşturma

IoT Edge Runtime, IoT Edge güvenlik arka plan programının sürümünü izler ve raporlar. Sürüm, IoT Edge Agent modülünün bildirdiği özelliğin *Runtime. platform. Version* özniteliği olarak bildirilir.

### <a name="hardware-security-module-platform-abstraction-layer-hsm-pal"></a>Donanım güvenlik modülü platformu soyutlama katmanı (HSM PAL)

HSM PAL, IoT Edge geliştiriciyi veya kullanıcısını karmaşıklıklarından yalıtmak için tüm güven donanımının kökünü soyutlar.  Uygulama programlama arabirimi (API) ve Trans-etki alanı iletişim yordamlarının bir birleşimini içerir, örneğin standart bir yürütme ortamı ile güvenli bir şifreleme arasındaki iletişim.  HSM PAL gerçek uygulama kullanımda olan güvenli donanıma bağlıdır. Varlığı neredeyse tüm güvenli Silicon Hardware kullanımına izin vermez.

## <a name="secure-silicon-root-of-trust-hardware"></a>Güven donanımının güvenli Silicon root 'ı

Güvenli Silicon, IoT Edge cihaz donanımının içindeki güveni bağlamak için gereklidir.  Güvenli Silicon Güvenilir Platform Modülü (TPM), katıştırılmış güvenli öğe (eSE), ARM TrustZone, Intel SGX ve özel güvenli Silicon Technologies dahil olmak üzere çeşitli şekillerde sunulur.  IoT cihazlarının fiziksel erişilebilirliği ile ilişkili tehditler verildiğinde cihazlarda güvenli bir güvenlik listesi kökünün kullanılması önerilir.

## <a name="iot-edge-security-manager-integration-and-maintenance"></a>IoT Edge Security Manager tümleştirmesi ve bakım

IoT Edge Güvenlik Yöneticisi, özel sağlamlaştırma için Azure IoT Edge platformunun güvenliğini ve bütünlüğünü savunmaya yönelik bileşenleri belirleyip yalıtmak üzere amaçlar. Cihaz üreticileri gibi üçüncü taraflar, cihaz donanımlarıyla kullanılabilen özel güvenlik özelliklerini kullanmalıdır.  

Yazılım veya sanal TPMs kullanarak Azure IoT Güvenlik Yöneticisi 'ni Güvenilir Platform Modülü (TPM) ile nasıl sağlamlaştıracağınızı öğrenin:  

[Linux sanal makinesinde sanal TPM ile bir IoT Edge cihazı](how-to-auto-provision-simulated-device-linux.md)oluşturun ve sağlayın.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
[Windows üzerinde sanal BIR TPM ile IoT Edge bir cihaz](how-to-auto-provision-simulated-device-windows.md)oluşturun ve sağlayın.
:::moniker-end

## <a name="next-steps"></a>Sonraki adımlar

[Akıllı kenarından güvenli hale](https://azure.microsoft.com/blog/securing-the-intelligent-edge/)getirmek için blogu okuyun.
