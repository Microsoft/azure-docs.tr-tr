---
title: Cihaz güvenliği için sertifikalar-Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge cihazları, modülleri ve yaprak cihazları doğrulamak ve aralarında güvenli bağlantılar oluşturmak için sertifika kullanır.
author: stevebus
manager: philmea
ms.author: stevebus
ms.date: 08/12/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: mqtt
ms.openlocfilehash: 6c54f935ed05451df5922ff1a3d564c775703700
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029361"
---
# <a name="understand-how-azure-iot-edge-uses-certificates"></a>Azure IoT Edge sertifikaları nasıl kullandığını anlayın

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

IoT Edge sertifikalar, modüller ve aşağı akış IoT cihazları tarafından, [IoT Edge merkezi](iot-edge-runtime.md#iot-edge-hub) çalışma zamanı modülünün kimliğini ve yasallığını doğrulamak için kullanılır. Bu doğrulamalar, çalışma zamanı, modüller ve IoT cihazları arasında TLS (Aktarım Katmanı Güvenliği) güvenli bağlantısını etkinleştirir. IoT Hub benzer şekilde, IoT Edge IoT aşağı akış (veya yaprak) cihazlarından ve IoT Edge modüllerden güvenli ve şifreli bir bağlantı gerektirir. Güvenli bir TLS bağlantısı kurmak için IoT Edge hub modülü, istemcilerin kimliğini doğrulayabilmesi için bir sunucu sertifika zinciri sunar.

>[!NOTE]
>Bu makalede, bir IoT Edge cihazdaki farklı bileşenler arasında veya IoT Edge bir cihaz ile herhangi bir yaprak cihaz arasında bağlantıları güvenli hale getirmek için kullanılan sertifikalar hakkında bilgi sağlanır. Ayrıca, IoT Hub için IoT Edge cihazınızı doğrulamak üzere sertifikaları da kullanabilirsiniz. Bu kimlik doğrulama sertifikaları farklıdır ve bu makalede ele alınmamalıdır. Cihazınızı Sertifikalarla kimlik doğrulama hakkında daha fazla bilgi için bkz. [X. 509.440 sertifikalarını kullanarak bir IoT Edge cihazı oluşturma ve sağlama](how-to-auto-provision-x509-certs.md).

Bu makalede, IoT Edge sertifikalarının üretim, geliştirme ve test senaryolarında nasıl çalıştığı açıklanmaktadır.

## <a name="iot-edge-certificates"></a>IoT Edge sertifikaları

IoT Edge cihazda sertifika ayarlamaya yönelik iki yaygın senaryo vardır. Bazen bir üretici tarafından oluşturulan genel bir cihazı satın alarak, bir cihazın son kullanıcısı veya operatörü, sertifikaları kendileri yönetir. Diğer zamanlarda, üretici, operatör için özel bir cihaz oluşturmak ve cihazı teslim etmeden önce bazı ilk sertifika imzalama yapmak için sözleşme altında çalışmaktadır. IoT Edge sertifikası tasarımı her iki senaryoyu de hesaba getirmeye çalışır.

Aşağıdaki şekilde sertifikaların kullanımı IoT Edge gösterilmektedir. Kök CA sertifikası ile cihaz CA sertifikası arasında, dahil edilen varlıkların sayısına bağlı olarak sıfır, bir veya çok sayıda ara imza sertifikası olabilir. Burada bir durum gösteririz.

![Tipik sertifika ilişkilerinin diyagramı](./media/iot-edge-certs/edgeCerts-general.png)

<!--1.1-->
:::moniker range="iotedge-2018-06"

> [!NOTE]
> Şu anda libiothsm içindeki bir sınırlama 1 Ocak 2038 tarihinde veya sonrasında sona ermekte olan sertifikaların kullanılmasını engelliyor. Bu sınırlama, cihaz CA sertifikası, güven paketindeki tüm sertifikalar ve X. 509.440 sağlama yöntemleri için kullanılan cihaz KIMLIĞI sertifikaları için geçerlidir.

:::moniker-end

### <a name="certificate-authority"></a>Sertifika yetkilisi

Sertifika yetkilisi veya kısaca ' CA ', dijital sertifikalar veren bir varlıktır. Sertifika yetkilisi, sertifikanın sahibi ve alıcısı arasında güvenilir bir üçüncü taraf görevi görür. Dijital bir sertifika, bir ortak anahtarın sahipliğini sertifika alıcısı tarafından sertifikalandırdır. Sertifika güven zinciri, başlangıçta yetkili tarafından verilen tüm sertifikalarda güvenin temeli olan bir kök sertifika vererek işe yarar. Daha sonra, sahip ek ara Sertifikalar (' yaprak ' sertifikaları) vermek için kök sertifikayı kullanabilir.

### <a name="root-ca-certificate"></a>Kök CA sertifikası

Kök CA sertifikası, tüm işlemin güvenin köküdür. Üretim senaryolarında, bu CA sertifikası genellikle Baltimore, Verisign veya DigiCert gibi güvenilir bir ticari sertifika yetkilisinden satın alınır. IoT Edge cihazlarınıza bağlanan cihazlar üzerinde tamamen denetim sahibi olmanız gerekir. kurumsal düzeyde bir sertifika yetkilisi kullanmak mümkündür. Her iki durumda da, IoT Edge hub 'ından tüm sertifika zinciri kendisine kaydolur, bu nedenle yaprak IoT cihazlarının kök sertifikaya güvenmesi gerekir. Kök CA sertifikasını Güvenilen kök sertifika yetkilisi deposunda saklayabilir ya da uygulama kodunuzda sertifika ayrıntılarını sağlayabilirsiniz.

### <a name="intermediate-certificates"></a>Ara sertifikalar

Güvenli cihaz oluşturmak için tipik bir üretim sürecinde kök CA sertifikaları, genellikle, sızıntı veya pozlama riski nedeniyle genellikle doğrudan kullanılır. Kök CA sertifikası bir veya daha fazla ara CA sertifikasını oluşturur ve dijital olarak imzalar. Yalnızca bir tane olabilir veya bu ara sertifikaların zinciri olabilir. Ara sertifikalar zinciri gerektiren senaryolar şunlardır:

* Bir üretici içindeki departmanlar hiyerarşisi.

* Birden çok şirket, bir cihazın üretimine dahil değildir.

* Bir müşteri, bir kök CA satın alıp bu müşterinin adına yaptıkları cihazları imzalamak üzere üretici için bir imza sertifikası türetiliyor.

Her durumda üretici, son cihaza yerleştirilmiş cihaz CA sertifikasını imzalamak için bu zincirin sonunda bir ara CA sertifikası kullanır. Genellikle, bu ara sertifikalar üretim tesisnde yakından korunuyor. Bunlar, hem fiziksel hem de elektronik kullanımı için katı işlemlere sahiptir.

### <a name="device-ca-certificate"></a>Cihaz CA sertifikası

Cihaz CA sertifikası, işlemdeki son ara CA sertifikası tarafından oluşturulur ve imzalanır. Bu sertifika, tercihen bir donanım güvenlik modülü (HSM) gibi güvenli depolamada IoT Edge cihazın kendisinde yüklüdür. Ayrıca, bir cihaz CA sertifikası IoT Edge cihazı benzersiz şekilde tanımlar. Cihaz CA sertifikası diğer sertifikaları imzalayabilirler.

### <a name="iot-edge-workload-ca"></a>IoT Edge Iş yükü CA

[IoT Edge Güvenlik Yöneticisi](iot-edge-security-manager.md) , ilk kez IoT Edge ilk başladığında işlemin "operatör" tarafındaki Iş yükü CA sertifikasını oluşturur. Bu sertifika, cihaz CA sertifikası tarafından oluşturulur ve imzalanır. Yalnızca başka bir ara imza sertifikası olan bu sertifika, IoT Edge çalışma zamanı tarafından kullanılan diğer sertifikaları oluşturmak ve imzalamak için kullanılır. Günümüzde, birincil olarak aşağıdaki bölümde ele alınan IoT Edge hub sunucu sertifikasıdır, ancak gelecekte IoT Edge bileşenlerinin kimliğini doğrulamak için diğer sertifikalar bulunabilir.

### <a name="iot-edge-hub-server-certificate"></a>IoT Edge hub sunucusu sertifikası

IoT Edge hub sunucusu sertifikası, IoT Edge tarafından gerekli olan TLS bağlantısı kurulurken kimlik doğrulama için yaprak cihazlara ve modüllere sunulan gerçek sertifikadır. Bu sertifika, yaprak IoT cihazının güveneceği kök CA sertifikasına kadar oluşturmak için kullanılan imzalama sertifikalarının tam zincirini gösterir. IoT Edge tarafından oluşturulduğunda, bu IoT Edge hub sertifikasının ortak adı (CN), daha küçük harfe dönüştürüldükten sonra yapılandırma dosyasındaki ' hostname ' özelliğine ayarlanır. Bu yapılandırma, IoT Edge ile yaygın bir karışıklık kaynağıdır.

## <a name="production-implications"></a>Üretim etkileri

Makul bir soru, "Neden IoT Edge ' iş yükü CA 'sı ek sertifikası gerekiyor? IoT Edge hub sunucusu sertifikasını doğrudan oluşturmak için cihaz CA sertifikası kullanılamıyor mu? ". Teknik olarak, bu olabilir. Ancak, bu "iş yükü" ara sertifikasının amacı, cihaz üreticisi ve cihaz işletmeni arasındaki kaygıları birbirinden ayırır. Bir IoT Edge cihazının satıldığı veya bir müşteriden diğerine aktarıldığı bir senaryoyu düşünün. Büyük olasılıkla üretici tarafından sağlanmış cihaz CA sertifikasının sabit olmasını isteyebilirsiniz. Ancak, cihaz işlemine özgü "iş yükü" sertifikaları, yeni dağıtım için temizlenmeli ve yeniden oluşturulmalıdır.

Üretim ve işlem işlemleri ayrıldığından, üretim cihazlarını hazırlarken aşağıdaki etkileri göz önünde bulundurun:

* Sertifika tabanlı tüm işlemler ile, kök CA sertifikası ve tüm ara CA sertifikaları, bir IoT Edge cihazı kullanıma alma işlemi sırasında güvenli hale getirilmeli ve izlenmelidir. IoT Edge cihaz üreticisi, ara sertifikalarının uygun şekilde depolanması ve kullanımı için güçlü işlemlere sahip olmalıdır. Buna ek olarak, cihaz CA sertifikasının cihaz kendisinde mümkün olduğunca güvenli depolama olarak tutulması gerekir, tercihen bir donanım güvenlik modülüdür.

* IoT Edge hub sunucusu sertifikası, bağlanan istemci cihazlarına ve modüllerine IoT Edge hub tarafından sunulur. Cihaz CA sertifikasının ortak adı (CN **), IoT Edge** cihazında yapılandırma dosyasında kullanılacak "ana bilgisayar adı" ile aynı olmamalıdır. İstemciler tarafından IoT Edge bağlanmak için kullanılan ad (örneğin, bağlantı dizesinin GatewayHostName parametresi veya MQTT içindeki CONNECT komutu aracılığıyla), cihaz CA sertifikasında kullanılan ortak adla aynı **olamaz** . Bu kısıtlama, IoT Edge hub 'ının istemci tarafından doğrulama için tüm sertifika zincirini sunmasıdır. IoT Edge hub sunucusu sertifikası ve cihaz CA sertifikası her ikisi de aynı CN 'ye sahip ise, bir doğrulama döngüsünde alırsınız ve sertifika geçersiz kılar.

* Cihaz CA sertifikası son IoT Edge sertifikalarını oluşturmak için IoT Edge güvenlik arka plan programı tarafından kullanıldığından, bunun kendisi bir imza sertifikası olması gerekir, yani sertifika imzalama becerileri vardır. Cihaz CA sertifikasına "v3 temel kısıtlamalar CA 'sı: true" uygulanması, gerekli anahtar kullanımı özelliklerini otomatik olarak ayarlar.

>[!Tip]
> Bir geliştirme/test senaryosunda "kullanışlı betiklerimizi" (sonraki bölüme bakın) kullanarak IoT Edge kurulumu gerçekleştirdiyseniz ve yapılandırma dosyasında ana bilgisayar adına yaptığınız şekilde cihaz CA sertifikası oluştururken aynı ana bilgisayar adını kullandıysanız, neden çalıştığını merak ediyor olabilirsiniz. Geliştirici deneyimini basitleştirecek bir çabayla, kullanışlı betikler, komut dosyasına geçirdiğiniz adın sonuna bir ". ca" ekler. Bu nedenle, örneğin, yapılandırma dosyasındaki betiklerin ve ana bilgisayar adının her ikisi için "mygateway" kullandıysanız, cihaz CA sertifikası için CN olarak kullanılmadan önce mygateway.ca ' ye açılır.

## <a name="devtest-implications"></a>Geliştirme ve test etkileri

Microsoft, geliştirme ve test senaryolarını kolaylaştırmak için, saydam ağ geçidi senaryosunda IoT Edge için uygun olan üretim dışı sertifikaları oluşturmaya yönelik bir dizi [kullanışlı betik](https://github.com/Azure/iotedge/tree/master/tools/CACertificates) sağlar. Betiklerin nasıl çalıştığı hakkında örnekler için bkz. [IoT Edge cihaz özelliklerini test etmek için tanıtım sertifikaları oluşturma](how-to-create-test-certificates.md).

>[!Tip]
> IoT cihaz SDK 'sını kullanan cihaz IoT "yaprak" cihazlarınızı ve uygulamalarınızı IoT Edge aracılığıyla bağlamak için, isteğe bağlı GatewayHostName parametresini cihazın bağlantı dizesinin sonuna eklemeniz gerekir. Sınır hub sunucusu sertifikası oluşturulduğunda, yapılandırma dosyasından ana bilgisayar adının düşük küçük bir sürümünü temel alır, bu nedenle, eşleşecek adların ve TLS sertifikası doğrulamasının başarılı olması için, daha küçük bir durumda GatewayHostName parametresini girmeniz gerekir.

## <a name="example-of-iot-edge-certificate-hierarchy"></a>IoT Edge sertifika hiyerarşisi örneği

Bu sertifika yolunun bir örneğini görmek için, aşağıdaki ekran görüntüsü, bir saydam ağ geçidi olarak ayarlanan çalışan bir IoT Edge cihazından oluşur. OpenSSL IoT Edge hub 'ına bağlanmak, sertifikaları doğrulamak ve bunların dökümünü almak için kullanılır.

![Her düzeyde sertifika hiyerarşisinin ekran görüntüsü](./media/iot-edge-certs/iotedge-cert-chain.png)

Ekran görüntüsünde temsil edilen sertifika derinliği hiyerarşisini görebilirsiniz:

| Kök CA sertifikası         | Yalnızca Azure IoT Hub CA sertifika sınaması                                                                           |
|-----------------------------|-----------------------------------------------------------------------------------------------------------|
| Ara CA sertifikası | Yalnızca Azure IoT Hub ara sertifika sınaması                                                                 |
| Cihaz CA sertifikası       | iotgateway.ca ("iotgateway", < ağ geçidi ana bilgisayar adı olarak, kullanışlı betiklerine > geçirildi)   |
| İş yükü CA sertifikası     | iotedge iş yükü CA                                                                                       |
| IoT Edge hub sunucusu sertifikası | iotedgegw. Local (yapılandırma dosyasındaki ' hostname ' ile eşleşir)                                            |

## <a name="next-steps"></a>Sonraki adımlar

[Azure IoT Edge modüllerini anlama](iot-edge-modules.md)

[IoT Edge cihazını saydam ağ geçidi olarak davranacak şekilde yapılandırma](how-to-create-transparent-gateway.md)
