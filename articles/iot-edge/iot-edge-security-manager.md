---
title: Güvenlik Yöneticisi yazılım - Azure IOT Edge cihazları nasıl koruduğunu öğrenin | Microsoft Docs
description: IOT Edge cihaz güvenlik tutum sergilemek ve güvenlik hizmetleri bütünlüğünü yönetir.
services: iot-edge
keywords: Güvenlik, güvenli öğesi, kuşatma, TEE, IOT Edge
author: eustacea
manager: philmea
ms.author: eustacea
ms.date: 08/30/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.openlocfilehash: f137070cb8a62f2c11f9e2688b5c7db47c1b866f
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208205"
---
# <a name="azure-iot-edge-security-manager"></a>Azure IOT Edge Güvenlik Yöneticisi

Azure IOT Edge güvenlik IOT Edge cihazı ve tüm bileşenlerini güvenli Silikon donanım özetleyen tarafından korunması için iyi sınırlanmış güvenlik çekirdek yöneticisidir. Bu, güvenlik sağlamlaştırma için odak noktasıdır ve özgün ekipman üreticilerine (OEM) teknoloji tümleştirme noktası sağlar.

![Azure IOT Edge Güvenlik Yöneticisi](media/edge-security-manager/iot-edge-security-manager.png)

IOT Edge Güvenlik Yöneticisi'ni IOT Edge cihazı ve tüm yazılım devralınan işlemler bütünlüğünü korumak amaçlar. Güvenlik Yöneticisi, IoT Edge çalışma zamanını önyüklemek ve devam eden işlemleri izlemek için, güven donanımının temel alınan donanım kökünden (varsa) güven geçişi yapar.  IoT Edge Güvenlik Yöneticisi, mümkün olan en yüksek güvenlik önlemlerinin sağlanmasına yardımcı olmak üzere güvenli bir Silicon Hardware (varsa) ile birlikte çalışır.  

IoT Edge Güvenlik Yöneticisi sorumlulukları şunlardır, ancak bunlarla sınırlı değildir:

* Güvenli ve Azure IOT Edge cihazı önyükleme ölçülür.
* Cihaz kimlik sağlama ve güven geçişin uygun olduğunda.
* Ana bilgisayar ve cihaz sağlama hizmeti gibi bulut hizmetlerinin bileşenleri cihaz koruyun.
* Benzersiz kimliklerle IoT Edge modülleri güvenli bir şekilde sağlayın.
* Ağ geçidi cihaz donanım kök güven notary Hizmetleri aracılığıyla.
* IOT Edge çalışma zamanı operace bütünlüğünü izler.

IOT Edge Güvenlik Yöneticisi üç bileşenleri içerir:

* IOT Edge güvenlik arka plan programı.
* Donanım güvenlik modülü platform Soyutlama Katmanı (HSM PAL).
* İsteğe bağlıdır, ancak donanım Silikon kök güveni veya HSM kesinlikle önerilir.

## <a name="the-iot-edge-security-daemon"></a>IOT Edge güvenlik arka plan programı

IoT Edge güvenlik arka plan programı, IoT Edge Security Manager 'ın mantıksal işlemlerinden sorumludur. IoT Edge cihazının güvenilir bilgi işlem tabanının önemli bir bölümünü temsil eder. 

### <a name="design-principles"></a>Tasarım ilkeleri

IoT Edge güvenlik arka plan programı iki temel ilkeden daha sonra çalışır: işlemsel bütünlüğü en üst düzeye çıkarın ve oluşan şişirmeyi ve dalgalanma

#### <a name="maximize-operational-integrity"></a>İşletimsel bütünlüğü en üst düzeye çıkarın

IoT Edge güvenlik arka plan programı, belirli bir güven donanımı kökünün savunma yeteneği dahilinde mümkün olan en yüksek bütünlüğü ile çalışır. Uygun Tümleştirmesi ile güven donanım kök ölçer ve güvenlik arka plan programı statik ve izinsiz kullanıma karşı dayanıklılık zamanında izler.

Fiziksel erişim, IoT cihazlarına yönelik her zaman bir tehdittir. Güvenin donanım kökü, IoT Edge güvenlik arka plan 'un bütünlüğünü erteleme bölümünde önemli bir rol oynar.  Güvenin donanım kökü iki değişken olarak gelir:

* Gizli dizileri ve şifreleme anahtarları gibi hassas bilgilerin korunması için güvenli öğeleri.
* Gizli dizileri koruması için güvenli enclaves anahtarları ve ölçüm ve faturalandırma gibi hassas iş yükleri gibi.

Güvenin donanım kökünü kullanmak için iki tür yürütme ortamı vardır:

* Hassas bilgileri korumak için güvenli öğelerin kullanımına dayanan standart ya da zengin yürütme ortamı (REE).
* Hassas bilgileri korumak ve yazılım yürütmeye koruma sunmak için güvenli şifreleme teknolojisini kullanan güvenilir yürütme ortamı (t).

Güvenli kuşanın donanım kökü olarak kullanıldığı cihazlarda, IoT Edge güvenlik cini içindeki hassas mantık kuşatma içinde olmalıdır.  Güvenlik arka plan programının hassas olmayan kısımları t 'nin dışında olabilir.  Herhangi bir durumda, özgün tasarım üreticileri (ODM) ve orijinal ekipman üreticileri (OEM), önyükleme ve çalışma zamanında IoT Edge güvenlik arka plan 'un bütünlüğünü ölçmek ve savunmak için HSM 'lerden güven genişletmelidir.

#### <a name="minimize-bloat-and-churn"></a>Şişirme en aza indirmek ve değişim sıklığı

IoT Edge güvenlik arka plan programı için başka bir temel ilke, karmaşıklığı en aza indirmektir.  En yüksek güven düzeyi için IoT Edge güvenlik arka plan programı, güvenilir bir şekilde cihaz donanımı köküne sahiptir ve yerel kod olarak çalışır.  Bu gerçekçi türler, bazı senaryolarda zor olabilen güvenli güncelleştirme yollarının (işletim sistemi tarafından sunulan güncelleştirme mekanizmalarının aksine) donanım kökü aracılığıyla Daemon yazılımını güncelleştirmek için yaygındır.  IoT cihazları için güvenlik yenilemesi önerilse de, aşırı güncelleştirme gereksinimleri veya büyük güncelleştirme yükleri tehdit yüzeyini birçok şekilde genişletebilirler.  İşletimsel kullanılabilirliği en üst düzeye çıkarmak için güncelleştirmeleri atlanıyor verilebilir veya kök güven donanım çok büyük bir güncelleştirme yükü işlemek için kısıtlanmış.  Bu nedenle, IoT Edge güvenlik arka plan programının tasarımı, parmak izini ve güvenilir bilgi işlem temelini korumak ve güncelleştirme gereksinimlerini en aza indirmek için kısa bir işlemdir.

### <a name="architecture-of-iot-edge-security-daemon"></a>IOT Edge güvenlik arka plan programı mimarisi

![Azure IOT Edge güvenlik arka plan programı](media/edge-security-manager/iot-edge-security-daemon.png)

IoT Edge güvenlik arka plan programı, güvenlik sağlamlaştırma için herhangi bir güven teknolojisinin kullanılabilir donanım kökünden yararlanır.  Ayrıca, donanım teknolojileri güvenilir yürütme ortamları sundukları zaman standart/zengin yürütme ortamı (REE) ve güvenilir bir yürütme ortamı (t) arasında bölünmüş bir işleme de olanak tanır. Role özgü arabirimler, IoT Edge cihazının ve işlemlerinin bütünlüğünü güvence altına almak için IoT Edge ana bileşenlerini etkinleştirir.

#### <a name="cloud-interface"></a>Bulut arabirimi

Bulut arabirimi, IoT Edge güvenlik arka plan programı, bulut Tebrikler gibi bulut hizmetlerine güvenlik yenilemesi gibi cihaz güvenliğine erişmesini sağlar.  Örneğin, IoT Edge güvenlik arka plan programı Şu anda bu arabirimi kullanarak cihaz kimliği yaşam döngüsü yönetimi için Azure IoT Hub [cihaz sağlama hizmeti](https://docs.microsoft.com/azure/iot-dps/) 'ne erişin.  

#### <a name="management-api"></a>Yönetim API'si

IoT Edge güvenlik arka plan programı, bir IoT Edge modülünü oluştururken/başlatırken/kaldırırken IoT Edge Aracısı tarafından çağrılan bir yönetim API 'SI sunar. Güvenlik arka plan programı, tüm etkin modüller için "kayıtlar" depolar. Bu kayıtları bir modülün kimlik modülü bazı özelliklerine eşlenir. Bu özelliklerin birkaç örnek kapsayıcı ya da docker kapsayıcının içeriğinin karma çalışan işlemin işlem tanımlayıcısını (PID) verilmiştir.

Bu özellikler, arayanın bir eylem gerçekleştirme yetkisi olduğunu doğrulamak için iş yükü API 'SI (aşağıda açıklanmıştır) tarafından kullanılır.

Yönetim API 'si, yalnızca IoT Edge aracılarından çağrılabilir olan ayrıcalıklı bir API 'dir.  IOT Edge güvenlik daemon bootstraps ve IOT Edge Aracısı'nı başlatır olduğundan, IOT Edge Aracısı ile değiştirilmemiş olduğunu Kanıtlanmamış sonra IOT Edge aracısı için örtük bir kaydı oluşturabilirsiniz. İş yükü API 'sinin kullandığı kanıtlama işlemi aynı zamanda yönetim API 'sine erişimi yalnızca IoT Edge aracısına kısıtlar.

#### <a name="container-api"></a>Kapsayıcı API

Kapsayıcı API 'SI, Moby veya Docker gibi modül yönetimi için kullanılan kapsayıcı sistemle etkileşime girer.

#### <a name="workload-api"></a>İş yükü API

İş yükü API 'sine tüm modüller erişebilir. Bir HSM 'nin imzalı belirteci veya x509 sertifikası ile karşılık gelen güven paketini bir modüle, kimlik kanıtı sağlar. Güven paketi, modüllerin güveneceği diğer tüm sunucular için CA sertifikaları içerir.

IoT Edge güvenlik arka plan programı, bu API 'yi korumak için bir kanıtlama işlemi kullanır. Bir modül bu API 'yi çağırdığında, güvenlik cini kimlik için bir kayıt bulmaya çalışır. Başarılı olursa, modül ölçmek için kayıt özelliklerini kullanır. Ölçüm işleminin sonucu kayıtla eşleşiyorsa, yeni bir kimlik kanıtı oluşturulur. Karşılık gelen CA sertifikaları (güven paketi) modüle döndürülür.  Modül bir sunucuyu başlatın veya diğer modüller, IOT Hub'ına bağlanmak için bu sertifikayı kullanır. İmzalanmış belirteç veya sertifika süre sonu yaklaştığında, bu, modülün yeni bir sertifika istemesi için sorumluluğundadır. 

### <a name="integration-and-maintenance"></a>Tümleştirme ve Bakım

Microsoft'un ana kod tabanı [GitHub üzerinde IOT Edge güvenlik daemon](https://github.com/Azure/iotedge/tree/master/edgelet).

#### <a name="installation-and-updates"></a>Yükleme ve güncelleştirme

IoT Edge güvenlik arka plan programının yüklenmesi ve güncelleştirmeleri, işletim sisteminin paket yönetim sistemi üzerinden yönetilir. Donanım köküne sahip IoT Edge cihazlar, güvenli önyükleme ve güncelleştirme yönetimi sistemleri aracılığıyla yaşam döngüsünü yöneterek arka plan 'un bütünlüğünden daha fazla sağlamlaştırma sağlamalıdır. Cihaz üreticileri bu avenilgili cihaz özelliklerine göre araştırmalıdır.

#### <a name="versioning"></a>Sürüm oluşturma

IOT Edge çalışma zamanı izler ve IOT Edge güvenlik daemon sürümü bildiriyor. Sürüm, IoT Edge Agent modülünün bildirdiği özelliğin *Runtime. platform. Version* özniteliği olarak bildirilir.

### <a name="hardware-security-module-platform-abstraction-layer-hsm-pal"></a>Donanım güvenlik modülü platform Soyutlama Katmanı (HSM PAL)

Tüm kök geliştirici veya IOT Edge kullanıcıdan miktarı yalıtmak için güven donanım HSM PAL soyutlar.  Uygulama programlama arabirimi (API) ve Trans-etki alanı iletişim yordamlarının bir birleşimini içerir, örneğin standart bir yürütme ortamı ile güvenli bir şifreleme arasındaki iletişim.  HSM PAL gerçek uygulaması belirli güvenli donanımı bağlıdır. Varlığı neredeyse tüm güvenli Silicon Hardware kullanımına izin vermez.

## <a name="secure-silicon-root-of-trust-hardware"></a>Silikon kök güven donanım güvenliğini sağlama

Güvenli Silikon, IOT Edge cihaz donanım içinde yer işareti güven için gereklidir.  Silikon güvenli, Güvenilir Platform Modülü (TPM), katıştırılmış güvenli öğesi (eSE), ARM TrustZone, Intel SGX ve özel güvenli Silikon teknolojileri dahil etmek için çeşitli gelir.  IoT cihazlarının fiziksel erişilebilirliği ile ilişkili tehditler verildiğinde cihazlarda güvenli bir güvenlik listesi kökünün kullanılması önerilir.

## <a name="iot-edge-security-manager-integration-and-maintenance"></a>IOT Edge Güvenlik Yöneticisi tümleştirmesi ve bakımı

IoT Edge Güvenlik Yöneticisi, özel sağlamlaştırma için Azure IoT Edge platformunun güvenliğini ve bütünlüğünü savunmaya yönelik bileşenleri belirleyip yalıtmak üzere amaçlar. Cihaz üreticileri gibi üçüncü taraflar, cihaz donanımlarıyla kullanılabilen özel güvenlik özelliklerini kullanmalıdır.  Azure IoT Güvenlik Yöneticisi 'nin Linux ve Windows platformlarındaki Güvenilir Platform Modülü (TPM) ile nasıl sağlamlaştıralınacağını gösteren bağlantılar için sonraki adımlar bölümüne bakın. Bu örnekler, yazılım veya sanal TPMs kullanır, ancak Ayrık TPM cihazlarını kullanarak doğrudan uygulanır.  

## <a name="next-steps"></a>Sonraki adımlar

Blogu okuyun [akıllı uç güvenliğini sağlama](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

[Linux sanal makinesinde sanal TPM ile bir IoT Edge cihazı](how-to-auto-provision-simulated-device-linux.md)oluşturun ve sağlayın.

[Windows üzerinde sanal BIR TPM ile IoT Edge bir cihaz](how-to-auto-provision-simulated-device-windows.md)oluşturun ve sağlayın.
