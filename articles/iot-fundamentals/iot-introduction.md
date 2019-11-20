---
title: Azure Nesnelerin İnterneti 'ye giriş (IoT)
description: IoT kullanımını göstermeye yardımcı olan örnekler de dahil olmak üzere Azure IoT ve IoT hizmetlerinin temellerini açıklayan giriş.
author: robinsh
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 10/11/2018
ms.author: robinsh
ms.openlocfilehash: ce82a2cc4cc936d2e0a7a8b82cbc0ed7e5c6eb52
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048641"
---
# <a name="what-is-azure-internet-of-things-iot"></a>Azure Nesnelerin İnterneti (IoT) nedir?

Azure Nesnelerin İnterneti (IoT), Microsoft tarafından yönetilen ve milyarlarca IoT varlığını bağlayan, izleyen ve denetleyen bulut hizmetlerinden oluşan bir koleksiyondur. Daha basit bir deyişle, bir IoT çözümü bulutta çalışan ve birbiriyle iletişim halinde olan bir veya daha çok IoT cihazından ve arka uç hizmetinden oluşur. 

Bu makalede IoT 'nin temelleri açıklanmakta, kullanım durumları hakkında konuşuyor ve kullanılabilir sekiz ayrı hizmet açıklanır. Nelerin kullanılabilir olduğunu anlayarak, senaryonuzu tasarlamaya yardımcı olmaya ne kadar yakından bakmak istediğinizi anlayabilirsiniz.

## <a name="introduction"></a>Giriş

IoT çözümünün ana kısımları şunlardır: cihazlar, arka uç hizmetleri ve ikisi arasındaki iletişim. 

### <a name="iot-devices"></a>IoT cihazları

Cihazlar genellikle internet 'e bağlanan sensörler eklenmiş bir devre panodan oluşur. Birçok cihaz bir Wi-Fi yongası aracılığıyla iletişim kurar. IoT cihazlarının bazı örnekleri aşağıda verilmiştir:

* uzak yağ pompa üzerinde basınç sensörleri
* Hava koşuldaki bir birimdeki sıcaklık ve nem sensörleri
* Asansör içindeki ivyeniler
* odadaki varlık algılayıcıları

Prototip oluşturma için sık kullanılan iki cihaz, Microsoft ve Raspberry PI cihazlarından oluşan temel MX yonga IoT devkit ' tir. MX yonga devkit, sıcaklık, basınç, nem ve bir jroscope ve hızlandırma, bir manyetik tometre ve bir Wi-Fi yongası için yerleşik olarak bulunan sensör içerir. Raspberry PI, birçok farklı algılayıcı eklemek için kullanabileceğiniz bir IoT cihazsudur ve bu sayede senaryonuz için tam olarak ihtiyacınız olanları seçebilirsiniz. 

Kullanılabilir IoT cihazları hakkında daha fazla bilgi için, [IoT için sertifikalı cihazların](https://catalog.azureiotsolutions.com/alldevices)en büyük kataloğunu inceleyin.

[IoT cihaz SDK 'ları](../iot-hub/iot-hub-devguide-sdks.md) , cihazlarınız üzerinde çalışan uygulamalar oluşturmanıza olanak sağlayarak, ihtiyacınız olan görevleri gerçekleştirebilirler. SDK 'lar ile IoT Hub 'ınıza telemetri gönderebilir, IoT Hub ileti ve güncelleştirme alabilir ve bu şekilde devam edebilirsiniz.

### <a name="communication"></a>İletişim

Cihazınız arka uç hizmetleriyle her iki yönde de iletişim kurabilir. Cihazın arka uç çözümüyle iletişim kurabilme yollarına dair bazı örnekler aşağıda verilmiştir.

#### <a name="examples"></a>Örnekler 

* Cihazınız, bir mobil soğutma kamyonından beş dakikada bir IoT Hub sıcaklık gönderebilir. 

* Arka uç hizmeti, bir sorunun tanılanmasına yardımcı olmak için cihazın telemetri göndermesini daha sık sorabilir. 

* Cihazınız, algılayıcılarından okunan değerlere göre uyarı gönderebilir. Örneğin, bir kimyasal tesisde bir toplu iş yeniden aktöri izliyorsanız, sıcaklıklar belirli bir değeri aştığında bir uyarı göndermek isteyebilirsiniz.

* Cihazınız, insan işleçlerine göre görüntülenmek üzere bir panoya bilgi gönderebilir. Örneğin, bir iyileşme içindeki bir denetim odası her bir kanalın sıcaklığını ve basıncını ve bu kanal üzerinden akan birimin, operatörlerin bunu izlemesini sağlar. 

Bu görevler ve daha fazlası [IoT cihaz SDK 'ları](../iot-hub/iot-hub-devguide-sdks.md)kullanılarak uygulanabilir.

#### <a name="connection-considerations"></a>Bağlantı konuları

IoT çözümlerinde görülen en büyük zorluk genellikle cihazları güvenli ve güvenilir bir şekilde bağlamaktır. Bunun nedeni, IoT cihazlarının tarayıcılar ve mobil uygulamalar gibi diğer istemcilerle karşılaştırıldığında farklı özelliklere sahip olmasından kaynaklanır. IoT cihazları şu özelliklere sahiptir:

* Genellikle insan kullanıcısı bulunmayan katıştırılmış sistemlerdir (bir telefonun aksine).

* Fiziksel erişimin pahalı olduğu uzak konumlarda dağıtılabilir.

* Yalnızca çözüm arka ucu aracılığıyla erişilebilir. Cihazla etkileşime geçmek için başka bir yol yoktur.

* Sınırlı güç ve işleme kaynaklarına sahip olabilir.

* Aralıklı, yavaş veya pahalı bir ağ bağlantısına sahip olabilir.

* Mülkiyete ait, özel veya sektöre özgü uygulama protokolleri kullanması gerekebilir.

### <a name="back-end-services"></a>Arka uç hizmetleri 

Bir arka uç hizmetinin sağlayasun, bazı işlevlerden bazıları aşağıda verilmiştir.

* Cihazlarınızdan ölçeğe göre telemetri alma ve bu verileri işleme ve depolamayı belirleme.

* Gerçek zamanlı veya olgu sonrasında öngörüleri sağlamak için Telemetriyi analiz etme.

* Buluttan belirli bir cihaza komut gönderme. 

* Cihazları hazırlama ve altyapınıza hangi cihazların bağlanabilbileceği kontrol.

* Cihazlarınızın durumunu denetleyin ve etkinliklerini izleyin.

Örneğin, tahmine dayalı bakım senaryosunda, bulut arka ucu geçmiş telemetri depolar. Çözüm, bu verileri kullanarak belirli pompalardaki anormal davranışları gerçek bir soruna neden olmadan önce tanımlar. Veri analizi kullanarak, önleyici çözümün düzeltici bir eylem gerçekleştirmek için cihaza yeniden komut göndermek olduğunu belirleyebilir. Bu işlem, cihaz ile bulut arasında çözüm verimliliğini önemli ölçüde artıran otomatik bir geri bildirim döngüsü oluşturur.

## <a name="an-iot-example"></a>IoT örneği

İşte milyonlarca dolar tasarruf etmek için bir şirketin IoT 'yi nasıl kullandığını gösteren bir örnek. 

Yüzlerce binlerce Cows ile çok büyük bir katgeçiş mevcuttur. Bu çok sayıda Cows 'yi izlemek ve bunların nasıl gittiğini öğrenmek için büyük bir işlem ve etrafında çok fazla yol olması gerekir. Bir veritabanına yazılacak bir arka uç hizmetine GPS koordinatları ve sıcaklık gibi bilgileri gönderen her tek Cow için sensörler iliştirildi.

Ardından, gelen verileri tarayan ve her bir inek için verileri çözümleyerek aşağıdakiler gibi soruları denetlemek üzere analitik bir hizmet vardır:

* İnek sıcaklık çalıştırıyor mu? İnek ne kadar sıcaklık çalıştırıyor? Bir günden daha uzun bir süredir ise, GPS koordinatlarını alın ve Cow 'ı bulun ve uygunsa, antibıos ile değerlendirin. 

* Bir günden daha uzun bir yerde inek mu var? Bu durumda, GPS koordinatlarını alın ve Cow 'yu bulun. Bir Cliff ile birlikte İnek ınred mi? İnek 'ın yardıma ihtiyacı var mı? 

Bu IoT çözümünü uygulamak, şirketin ineklerin 'yi hızlı bir şekilde denetlemesini ve harcamasını ve bu kişilerin hayvanlar üzerinde denetim altına almak için harcayacağı süreyi aşmasını ve çok miktarda tasarruf etmesini sağlar. Şirketlerin IoT kullanma hakkında daha fazla gerçek yaşam örneği için bkz. [IoT Için Microsoft Teknik olay incelemeleri](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured). 

## <a name="iot-services"></a>IoT Hizmetleri

Azure 'da çok sayıda IoT ile ilgili hizmet vardır ve bunlardan hangisini kullanmak istediğinizi anlamak kafa karıştırıcı olabilir. Bazıları IoT Central ve IoT Çözüm Hızlandırıcıları gibi, kendi çözümünüzü oluşturmanıza ve hızlıca çalışmaya başlamanıza yardımcı olacak şablonlar sağlar. Ayrıca, kullanılabilir diğer hizmetleri kullanarak kendi çözümlerinizi tamamen geliştirebilirsiniz; hepsi, ne kadar yardım istediğinize ve ne kadar denetime bağlıdır. Burada, kullanılabilir hizmetlerin yanı sıra bunları için kullanabileceğiniz bir listesi verilmiştir.

1. [**IoT Central**](../iot-central/core/overview-iot-central.md): Bu, IoT çözümlerinin oluşturulmasını kolaylaştıran ve IoT yönetim işlemlerinin ve geliştirmenin yükünü ve maliyetini azaltmaya yardımcı olan bir IoT uygulama platformudur. Başlamak için, cihaz türü için bir şablon seçin ve cihaz işleçlerinin kullanacağı temel bir IoT Central uygulaması oluşturun ve test edin. IoT Central uygulama aynı zamanda cihazları izlemenizi ve yeni cihazlar sağlamanıza olanak sağlar. Bu hizmet, derin hizmet özelleştirmesi gerektirmeyen kolay çözümler içindir.

2. [**IoT Çözüm Hızlandırıcıları**](/azure/iot-suite): Bu, IoT çözümünün geliştirilmesini hızlandırmak Için kullanabileceğiniz PaaS çözümlerinin bir koleksiyonudur. Bir sunulan IoT çözümüyle başlayıp bu çözümü gereksinimlerinize göre tamamen özelleştirebilirsiniz. Görselleştirmeyi özelleştirmek için arka ucu ve JavaScript yeteneklerini özelleştirmek üzere Java veya .NET becerileri gerekir. 

3. [**IoT Hub**](/azure/iot-hub/): Bu hizmet, cihazlarınızdan IoT Hub 'ına bağlanmanızı ve milyarlarca IoT cihazını izlemenizi ve denetlemenizi sağlar. Bu, özellikle IoT cihazlarınız ile arka uç arasında çift yönlü iletişim gerekiyorsa yararlıdır. Bu, IoT Central ve IoT çözüm hızlandırıcılarına yönelik temel hizmettir. 

4. [**Cihaz sağlama hizmeti IoT Hub**](/azure/iot-dps/): Bu, IoT Hub 'ınıza cihaz sağlamak için kullanabileceğiniz IoT Hub yardımcı hizmettir. Bu hizmetle, bunları tek tek sağlamak yerine milyonlarca cihazı hızlıca sağlayabilirsiniz. 

5. [**IoT Edge**](/azure/iot-edge/): bu hizmet IoT Hub en üstünde oluşturulur. Bulut yerine IoT cihazlarındaki verileri analiz etmek için kullanılabilir. İş yükünüzün parçalarını kenar altına taşıyarak buluta daha az ileti gönderilmesi gerekir. 

6. [**Azure dijital TWINS**](../digital-twins/index.yml): Bu hizmet, fiziksel ortamın kapsamlı modellerini oluşturmanızı sağlar. Kişiler, boşluklar ve cihazlar arasındaki ilişkileri ve etkileşimleri modelleyebilirsiniz. Örneğin, bir fabrika için bakım ihtiyaçlarını tahmin edebilir, elektrik Kılavuzu için gerçek zamanlı enerji gereksinimlerini çözümleyebilir veya bir ofis için kullanılabilir alan kullanımını iyileştirebilirsiniz.

7. [**Time Series Insights**](/azure/time-series-insights): Bu hizmet, IoT cihazları tarafından oluşturulan büyük miktarlarda zaman serisi verilerini depolamanızı, görselleştirmenizi ve sorgulamanızı sağlar. Bu hizmeti IoT Hub kullanabilirsiniz. 

8. [**Azure haritalar**](/azure/azure-maps): Bu hizmet, Web ve mobil uygulamalara coğrafi bilgiler sağlar. Tüm REST API 'lerin yanı sıra hem Apple hem de Windows cihazları için masaüstü veya mobil uygulamalarda çalışan esnek uygulamalar oluşturmak için kullanılabilen bir Web tabanlı JavaScript denetimi vardır.

## <a name="next-steps"></a>Sonraki adımlar

Bazı gerçek iş durumlarında ve kullanılan mimaride, [Microsoft Azure IoT teknik olay incelemeleri](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured)' ne bakın.

IoT DevKit ile deneyebileceğiniz bazı örnek projelerde [IoT devkit proje kataloğuna](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)bakın. 

Farklı hizmetlerin ve bunların nasıl kullanıldıkları hakkında daha kapsamlı bir açıklama için bkz. [Azure IoT Hizmetleri ve teknolojileri](iot-services-and-technologies.md).

IoT mimarisinin ayrıntılı incelemesi için bkz. [Microsoft Azure IoT Başvuru Mimarisi](https://aka.ms/iotrefarchitecture).
