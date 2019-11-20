---
title: Cihaz güvenliği - Azure IOT Edge için sertifika | Microsoft Docs
description: Azure IOT Edge cihazları, modülleri ve yaprak cihazlarıyla doğrulamak ve onları arasında güvenli bağlantılar kurmak için kullandığı sertifika.
author: stevebus
manager: philmea
ms.author: stevebus
ms.date: 09/13/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0aa70e591c7aac977fe13ed638f8ee56b88e4bd1
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982903"
---
# <a name="azure-iot-edge-certificate-usage-detail"></a>Azure IOT Edge sertifika kullanım ayrıntısı

IoT Edge sertifikalar, bağlantı kurdukları [IoT Edge hub](iot-edge-runtime.md#iot-edge-hub) çalışma zamanı modülünün kimliğini ve yasallığını doğrulamak için modüller ve aşağı akış IoT cihazları için kullanılır. Bu doğrulamaları TLS (Aktarım Katmanı Güvenliği) arasında güvenli bir bağlantı çalışma zamanı, modülleri ve IOT cihazları etkinleştirin. IOT Hub kendisi gibi IOT Edge güvenli gerektirir ve aşağı yönde IOT bağlantısından şifreli (veya yaprak) cihazlar ve IOT Edge modülleri. Güvenli bir TLS bağlantısı kurmak için IoT Edge hub modülü, istemcilerin kimliğini doğrulayabilmesi için bir sunucu sertifika zinciri sunar.

Bu makalede, IoT Edge sertifikalarının üretim, geliştirme ve test senaryolarında nasıl çalıştığı açıklanmaktadır. Farklı (bash ve Powershell) komut dosyalarını olsa da, Linux ve Windows arasında aynı kavramlardır.

## <a name="iot-edge-certificates"></a>IoT Edge sertifikaları

Genellikle, üreticiler IoT Edge cihazının son kullanıcıları değildir. Bazen, son kullanıcı ya da operatör arasındaki tek ilişki, üretici tarafından oluşturulan bir genel cihazı satın alır. Diğer zamanlarda üretici, operatör adına özel bir cihaz oluşturmak için sözleşme altında çalışmaktadır. IOT Edge sertifika tasarım, her iki senaryoyu dikkate dener.

Aşağıdaki şekilde, sertifikaların IOT Edge'nın kullanımı gösterilmektedir. Kök CA sertifikası ile cihaz CA sertifikası arasında, dahil edilen varlıkların sayısına bağlı olarak sıfır, bir veya çok sayıda ara imza sertifikası olabilir. Burada bir durum gösteririz.

![Tipik bir sertifika ilişkileri diyagramı](./media/iot-edge-certs/edgeCerts-general.png)

### <a name="certificate-authority"></a>Sertifika yetkilisi

Sertifika yetkilisi veya kısaca, ' CA' Dijital sertifikaları veren bir varlıktır. Bir sertifika yetkilisi, sahibi ve sertifika alıcısı arasında güvenilir bir üçüncü taraf olarak görev yapar. Bir dijital sertifika ortak anahtar sertifika alıcısı tarafından sahipliğini onaylar. Başlangıçta yetkilisi tarafından verilen tüm sertifikaları güvende temeli bir kök sertifika vererek sertifika güven zinciri çalışır. Ardından, sahibi, kök sertifika ek ara sertifikaları ('yaprak' Sertifikalar) kullanabilirsiniz.

### <a name="root-ca-certificate"></a>Kök CA sertifikası

Bir kök CA sertifikasını güven tüm işlemin köküdür. Üretim senaryolarında, bu CA sertifikası genellikle Baltimore, Verisign veya DigiCert gibi güvenilir bir ticari sertifika yetkilisinden satın alınır. IoT Edge cihazlarınıza bağlanan cihazlar üzerinde tamamen denetim sahibi olmanız gerekir. kurumsal düzeyde bir sertifika yetkilisi kullanmak mümkündür. Her iki durumda da, IoT Edge hub 'ından tüm sertifika zinciri kendisine kaydolur, böylece yaprak IoT cihazlarının kök sertifikaya güvenmesi gerekir. Kök CA sertifikasını Güvenilen kök sertifika yetkilisi deposunda saklayabilir ya da uygulama kodunuzda sertifika ayrıntılarını sağlayabilirsiniz.

### <a name="intermediate-certificates"></a>Ara sertifikalar

Tipik bir üretim sürecinin güvenli cihazlar oluşturmak için bir kök CA sertifikaları nadiren doğrudan, öncelikle riskini sızıntısını veya nedeniyle kullanılır. Kök CA sertifikası bir veya daha fazla ara CA sertifikasını oluşturur ve dijital olarak imzalar. Yalnızca olabilir bir ya da bu Ara sertifikaları zincirine olabilir. Ara sertifikaları zincirine gerektiren senaryolar şunlardır:

* Bir üretici anomaly hiyerarşisi.

* Bir cihazın seri olarak üretim dahil birden çok şirket.

* Müşterinin adıma yaptıkları cihazları imzalamak için bir kök CA satın alma ve imzalama sertifikası üretici türetme bir müşteri.

Her iki durumda da üretici uç cihazda yerleştirilen cihaz CA sertifikasını imzalamak için bu zincir sonunda bir ara CA sertifikasını kullanır. Genellikle, bu Ara sertifikaları üretim tesisindeki yakından korumalı. Bunlar, hem fiziksel hem de kullanımları elektronik katı süreçler geçeriz.

### <a name="device-ca-certificate"></a>Cihaz CA sertifikası

Cihaz CA sertifikası oluşturulan ve işlem son ara CA sertifikası tarafından imzalanmış. Bu sertifika, tercihen bir donanım güvenlik modülü (HSM) gibi güvenli depolamada IoT Edge cihazın kendisinde yüklüdür. Ayrıca, bir cihaz CA sertifikası, bir IOT Edge cihazı benzersiz olarak tanımlar. IoT Edge için, cihaz CA sertifikası diğer sertifikalar verebilir. Örneğin, cihaz CA sertifikası, cihazların kimlik doğrulaması için [Azure IoT cihaz sağlama hizmeti](../iot-dps/about-iot-dps.md)'nde kullanılan yaprak cihaz sertifikalarını yayınlar.

### <a name="iot-edge-workload-ca"></a>IOT Edge iş yükü CA

[IoT Edge Güvenlik Yöneticisi](iot-edge-security-manager.md) , ilk kez IoT Edge ilk başladığında işlemin "operatör" tarafındaki Iş yükü CA sertifikasını oluşturur. Bu sertifika oluşturulan ve "cihaz CA sertifikası" tarafından imzalanmış. Başka bir ara imzalama sertifikası olduğundan, bu sertifika, oluşturmak ve IOT Edge çalışma zamanı tarafından kullanılan sertifikaları imzalamak için kullanılır. Günümüzde, birincil olarak aşağıdaki bölümde ele alınan IoT Edge hub sunucu sertifikasıdır, ancak gelecekte IoT Edge bileşenlerinin kimliğini doğrulamak için diğer sertifikalar bulunabilir.

### <a name="iot-edge-hub-server-certificate"></a>IoT Edge hub sunucusu sertifikası

IoT Edge hub sunucusu sertifikası, IoT Edge tarafından gerekli olan TLS bağlantısı kurulurken kimlik doğrulama için yaprak cihazlara ve modüllere sunulan gerçek sertifikadır. Bu sertifikayı yaprak IOT cihaz güvenmelidir kök CA sertifikasını kadar oluşturmak için kullanılan imzalama sertifikalarının, tam zincir sunar. IoT Edge Güvenlik Yöneticisi tarafından oluşturulduğunda, bu IoT Edge hub sertifikasının ortak adı (CN), küçük harfe dönüştürüldükten sonra config. YAML dosyasındaki ' hostname ' özelliğine ayarlanır. Bu, IOT Edge ile karışıklık ortak bir kaynaktır.

## <a name="production-implications"></a>Üretim uygulamaları

Makul bir soru olabilir "neden IOT Edge gerekmez 'iş yükü CA' ek sertifika? IoT Edge hub sunucusu sertifikasını doğrudan oluşturmak için cihaz CA sertifikası kullanılamıyor mu? ". Teknik olarak verebilir. Ancak, bu "iş yükü" Ara Sertifika amacı cihaz üreticisi ve cihaz işleci arasında kaygıları ayırmaktır. Burada bir IOT Edge cihazı satılan veya başka bir müşteriden aktarılan bir senaryo düşünün. Büyük olasılıkla sabit olmasını üreticisi tarafından sağlanan cihaz CA sertifikası istersiniz. Ancak, cihazın bir işlem için belirli bir "iş yükü" sertifikaları sonlandırılana ve yeni dağıtım için yeniden.

Üretim ve işlem işlemleri ayrıldığından, üretim cihazlarını hazırlarken aşağıdaki etkileri göz önünde bulundurun:

* Tüm sertifika tabanlı işlem ile kök CA sertifikasını ve tüm ara CA sertifikalarının güvenli ve izlenen tüm IOT Edge cihazı alma işlemi sırasında. IOT Edge cihaz üreticisi, uygun depolama ve bunların Ara sertifikaların kullanımını güçlü işlemler olması gerekir. Ayrıca, cihaz CA sertifikası cihaz üzerinde tercihen bir donanım güvenlik modülü mümkün olduğunca güvenli depolama alanı olarak tutulmalıdır.

* IoT Edge hub sunucusu sertifikası, bağlanan istemci cihazlarına ve modüllerine IoT Edge hub tarafından sunulur. Cihaz CA sertifikasının ortak adı (CN), IoT Edge cihazında config. YAML içinde kullanılacak "ana bilgisayar adı" ile aynı olmamalıdır. İstemciler tarafından IoT Edge bağlanmak için kullanılan ad (örneğin, bağlantı dizesinin GatewayHostName parametresi veya MQTT içindeki CONNECT komutu aracılığıyla), cihaz CA sertifikasında kullanılan ortak adla aynı **olamaz** . Bu kısıtlama, IoT Edge hub 'ının istemci tarafından doğrulama için tüm sertifika zincirini sunmasıdır. IoT Edge hub sunucusu sertifikası ve cihaz CA sertifikası her ikisi de aynı CN 'ye sahip ise, bir doğrulama döngüsünde alırsınız ve sertifika geçersiz kılar.

* Cihaz CA sertifikası son IoT Edge sertifikalarını oluşturmak için IoT Edge güvenlik arka plan programı tarafından kullanıldığından, bunun kendisi bir imza sertifikası olması gerekir, yani sertifika imzalama becerileri vardır. Cihaz CA sertifikasına "v3 temel kısıtlamalar CA 'sı: true" uygulanması, gerekli anahtar kullanımı özelliklerini otomatik olarak ayarlar.

>[!Tip]
> Zaten IOT Edge kurulumunu bizim "kullanışlı betik" kullanarak geliştirme/test senaryosunda saydam bir ağ geçidi olarak incelediğinize varsa (sonraki bölüme bakın) ve aynı konak adını config.yaml içinde konak adı için yaptığınız gibi cihaz CA sertifikasını oluştururken kullandığınız , size neden kadar işe yaradığını merak ediyor olabilirsiniz. Kolaylık betikleri geliştirilmiştir Geliştirici deneyimini basitleştirmek için komut dosyasına geçirmek adının sonuna üzerinde ".ca" ekler. Her iki cihaz adınızı betikler ve config.yaml ana bilgisayar adı için "mygateway" kullandıysanız, bu nedenle, örneğin, önceki mygateway.ca cihaz CA sertifikası için CN kullanılmadan önce kapatılacak.

## <a name="devtest-implications"></a>Geliştirme ve Test uygulamaları

Microsoft geliştirme sürecini kolaylaştırır ve test senaryoları için bir dizi sağlar [kolaylık betikleri](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) saydam bir ağ geçidi senaryosunda IOT Edge için uygun olmayan üretim sertifikası oluşturmak için. Komut dosyalarını nasıl örnekler için bkz [saydam bir ağ geçidi olarak görev yapacak bir IOT Edge cihazı yapılandırma](how-to-create-transparent-gateway.md).

Bu betikler, bu makalede açıklanan sertifika zinciri yapısına uygun sertifikaları oluşturur. Aşağıdaki komutlar, "kök CA sertifikasını" ve tek "ara CA sertifikası" oluşturur.

```bash
./certGen.sh create_root_and_intermediate 
```

```Powershell
New-CACertsCertChain rsa 
```

Benzer şekilde, bu komutlar "Cihaz CA sertifika" oluşturun.

```bash
./certGen.sh create_edge_device_ca_certificate "<gateway device name>"
```

```Powershell
New-CACertsEdgeDeviceCA "<gateway device name>"
```

* Bu betiklerin geçirildiği **\> \<ağ geçidi cihaz adı** config. YAML içindeki "hostname" parametresiyle aynı olmamalıdır. Betikler, bir kullanıcının her iki yerde de aynı adı kullanarak IoT Edge ayarlaması durumunda ad çarpışmasını engellemek için  **\<ağ\> geçidi cihaz adına** ". ca" dizesi ekleyerek herhangi bir sorunu önlemenize yardımcı olur. Ancak, aynı adı kullanmaktan kaçınmak iyi bir uygulamadır.

>[!Tip]
> Cihaz IOT "yaprak" cihazlarınızdan ve IOT Edge üzerinden bizim IOT cihaz SDK'sını kullanan uygulamaları bağlamak için isteğe bağlı bir GatewayHostName parametre cihazın bağlantı dizesinin sonuyla açın eklemeniz gerekir. Edge hub'ı sunucu sertifikası oluşturulduğunda, ana bilgisayar adını config.yaml küçük harfleri bir sürümünü temel alır, bu nedenle, eşleşme ve TLS sertifika doğrulama işleminin başarılı olması için adları için GatewayHostName parametresi küçük girmeniz gerekir.

## <a name="example-of-iot-edge-certificate-hierarchy"></a>IOT Edge sertifika hiyerarşisi örneği

Bu sertifika yolu örneği göstermek için aşağıdaki ekran görüntüsünde çalışan bir IOT Edge cihazı saydam bir ağ geçidi olarak ayarlama arasındadır. OpenSSL IoT Edge hub 'ına bağlanmak, sertifikaları doğrulamak ve bunların dökümünü almak için kullanılır.

![Sertifika hiyerarşisi her düzeyde ekran görüntüsü](./media/iot-edge-certs/iotedge-cert-chain.png)

Ekran görüntüsünde gösterilen sertifika derinliği hiyerarşisini görebilirsiniz:

| Kök CA sertifikası         | Azure IOT hub'ı CA sertifikası yalnızca sınama                                                                           |
|-----------------------------|-----------------------------------------------------------------------------------------------------------|
| Ara CA sertifikası | Azure IOT hub'ı ara sertifika yalnızca sınama                                                                 |
| Cihaz CA sertifikası       | iotgateway.CA ("iotgateway" geçirildi < ağ geçidi ana bilgisayar adı > kolaylık betikleri)      |
| İş yükü CA sertifikası     | iotedge iş yükü ca                                                                                       |
| IoT Edge hub sunucusu sertifikası | iotedgegw.local ('hostname' config.yaml eşleşir)                                                |

## <a name="next-steps"></a>Sonraki adımlar

[Azure IOT Edge modüllerini anlama](iot-edge-modules.md)

[Saydam bir ağ geçidi olarak görev yapacak bir IOT Edge cihazı yapılandırma](how-to-create-transparent-gateway.md)