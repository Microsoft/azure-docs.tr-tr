---
title: Azure IoT Hub için cihaz yapılandırması en iyi yöntemleri | Microsoft Docs
description: IoT cihazlarını ölçeklendirmede bulunan yinelenen ve karmaşık görevleri en aza indirmek için otomatik cihaz yönetimini kullanmaya yönelik en iyi yöntemler hakkında bilgi edinin.
author: chrisgre
ms.author: chrisgre
ms.date: 06/28/2019
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: a3b70af71c2ce19835ac2ef8fc8ceed79ca5fe1a
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889524"
---
# <a name="best-practices-for-device-configuration-within-an-iot-solution"></a>IoT çözümünde cihaz yapılandırması için en iyi uygulamalar

Azure IoT Hub otomatik cihaz yönetimi, büyük cihaz filliklerini tümüyle yaşam döngülerine göre yönetmenin çok sayıda yinelenen ve karmaşık görevi otomatikleştirir. Bu makalede, IoT çözümünü geliştirme ve çalıştırma konusunda yer alan çeşitli roller için en iyi uygulamaların birçoğu tanımlanmaktadır.

* **IoT donanım üreticisi/tümleştirici:** IoT donanım üreticileri, tümleştiricileri çeşitli üreticilerin donanımını veya diğer tedarikçiler tarafından üretilen veya tümleştirilmiş bir IoT dağıtımı için donanım sağlayan tedarikçilerden oluşur. Üretici yazılımı, katıştırılmış işletim sistemleri ve ekli yazılımların geliştirilmesi ve tümleştirilmesine dahil değildir.

* **IoT çözüm geliştiricisi:** IoT çözümünün geliştirilmesi genellikle çözüm geliştiricisi tarafından yapılır. Bu geliştirici, bu etkinlikte bir şirket içi ekibin veya bir sistem tümleştirici 'nin bir parçası olabilir. IoT çözüm geliştiricisi, IoT çözümünün çeşitli bileşenlerini sıfırdan geliştirebilir, çeşitli standart veya açık kaynaklı bileşenleri tümleştirebilir veya bir [IoT çözüm hızlandırıcıyı](/azure/iot-accelerators/)özelleştirebilir.

* **IoT çözüm operatörü:** IoT çözümü dağıtıldıktan sonra uzun süreli işlemler, izleme, yükseltmeler ve bakım gerektirir. Bu görevler, bilgi teknolojisi uzmanları, donanım işlemleri ve bakım ekipleri ve genel IoT altyapısının doğru davranışını izleyen etki alanı uzmanlarından oluşan bir şirket içi ekip tarafından yapılabilir.

## <a name="understand-automatic-device-management-for-configuring-iot-devices-at-scale"></a>IoT cihazlarını ölçekli olarak yapılandırmak için otomatik cihaz yönetimini anlama

Otomatik cihaz yönetimi, bulut ve cihazlar arasında istenen ve bildirilen durumları eşzamanlı hale getirmek için [cihaz](iot-hub-devguide-device-twins.md) ikiklerini ve [Modül](iot-hub-devguide-module-twins.md) iklerini içerir. [Otomatik cihaz yapılandırması](iot-hub-auto-device-config.md) büyük sayıda TWINS kümesini otomatik olarak güncelleştirir ve ilerleme ve uyumluluğu özetler. Aşağıdaki üst düzey adımlarda otomatik cihaz yönetiminin nasıl geliştirildiğini ve kullanıldığını anlatmaktadır:

* **IoT donanım üreticisi/tümleştiricisi** , Device [TWINS](iot-hub-devguide-device-twins.md)kullanarak eklenmiş bir uygulama içinde cihaz yönetim özellikleri uygular. Bu özellikler üretici yazılımı güncelleştirmeleri, yazılım yükleme ve güncelleştirme ve ayar yönetimine dahil olabilir.

* **IoT çözüm geliştiricisi** cihaz yönetim işlemlerinin yönetim katmanını ve [cihaz](iot-hub-devguide-device-twins.md) iklerini ve [otomatik cihaz yapılandırmasını](iot-hub-auto-device-config.md)uygular. Çözüm, cihaz yönetimi görevlerini gerçekleştirmek için bir operatör arabirimi tanımlamayı içermelidir.

* **IoT çözüm işletmeni** , cihaz yönetimi görevlerini gerçekleştirmek için IoT çözümünü, özellikle de cihazları gruplamak, bellenim güncelleştirmeleri gibi yapılandırma değişiklikleri başlatmak, ilerlemeyi izlemek ve ortaya çıkan sorunları gidermek için kullanır.

## <a name="iot-hardware-manufacturerintegrator"></a>IoT donanım üreticisi/tümleştirici

Aşağıda, donanım üreticileri ve tümleşik yazılım geliştirme ile ilgili tümleştirmelerde en iyi yöntemler verilmiştir:

* **[Cihaz TWINS](iot-hub-devguide-device-twins.md)'i Uygula:** Cihaz TWINS, istenen yapılandırmayı buluttan eşitlemeye ve geçerli yapılandırma ve cihaz özelliklerini raporlamaya olanak tanır. Katıştırılmış uygulamalarda cihaz ikizlerini uygulamanın en iyi yolu [Azure IoT SDK 'ları](https://github.com/Azure/azure-iot-sdks)kullanmaktır. Cihaz İkileri yapılandırma için en iyi seçenektir:

    * İki yönlü iletişimi destekler.
    * Hem bağlı hem de bağlantısı kesilen cihaz durumlarına izin verin.
    * Nihai tutarlılık ilkesini izleyin.
    * Bulutta tamamen ilişkiiyor.

* Cihaz **yönetimi için cihaz Ikizi yapısı:** Cihaz ikizi, cihaz yönetimi özellikleri mantıksal olarak bölümler halinde gruplandırılarak yapılandırılmalıdır. Bunun yapılması, ikizi 'in diğer bölümlerini etkilemeden yapılandırma değişikliklerinin yalıtılmasını sağlayacaktır. Örneğin, üretici yazılımı için istenen özellikler içinde bir bölüm, yazılım için başka bir bölüm ve ağ ayarları için üçüncü bir bölüm oluşturun. 

* **Cihaz yönetimi için yararlı olan rapor cihaz öznitelikleri:** Fiziksel cihaz oluşturma ve model, üretici yazılımı, işletim sistemi, seri numarası ve diğer tanımlayıcılar gibi öznitelikler, raporlama ve yapılandırma değişikliklerini hedeflemek için yararlıdır.

* **Raporlama durumu ve ilerleme durumunun ana durumlarını tanımlayın:** Üst düzey durumlar, işlecine raporlanabilmesi için numaralandırılmalıdır. Örneğin, bir bellenim güncelleştirmesi durumu geçerli, karşıdan yükleme, uygulama, devam ediyor ve hata olarak raporlayabilir. Her durum hakkında daha fazla bilgi için ek alanlar tanımlayın.

## <a name="iot-solution-developer"></a>IoT çözüm geliştiricisi

Aşağıda, Azure 'a dayalı sistemler oluşturan IoT çözüm geliştiricileri için en iyi yöntemler verilmiştir:

* **[Cihaz TWINS](iot-hub-devguide-device-twins.md)'i Uygula:** Cihaz TWINS, istenen yapılandırmayı buluttan eşitlemeye ve geçerli yapılandırma ve cihaz özelliklerini raporlamaya olanak tanır. Bulut çözümleri uygulamalarında cihaz ikizlerini uygulamanın en iyi yolu [Azure IoT SDK 'ları](https://github.com/Azure/azure-iot-sdks)kullanmaktır. Cihaz İkileri yapılandırma için en iyi seçenektir:

    * İki yönlü iletişimi destekler.
    * Hem bağlı hem de bağlantısı kesilen cihaz durumlarına izin verin.
    * Nihai tutarlılık ilkesini izleyin.
    * Bulutta tamamen ilişkiiyor.

* **Cihaz ikizi etiketlerini kullanarak cihazları düzenleyin:** Çözüm, işlecin, Canary gibi çeşitli dağıtım stratejilerine göre kalite halkalarını veya diğer cihaz kümelerini tanımlamasına izin vermelidir. Cihaz organizasyonu, cihaz ikizi etiketleri ve [sorguları](iot-hub-devguide-query-language.md)kullanılarak çözümünüz dahilinde uygulanabilir. Cihaz organizasyonu, yapılandırma için güvenli ve doğru bir şekilde izin vermek üzere gereklidir.

* **[Otomatik cihaz yapılandırmasını](iot-hub-auto-device-config.md)Uygula:** Otomatik cihaz yapılandırmaları, cihaz TWINS aracılığıyla büyük IoT cihazı kümelerinde yapılandırma değişikliklerini dağıtır ve izler.

   Otomatik cihaz yapılandırması, cihaz ikizi etiketlerinde veya bildirilen özelliklerde bir sorgu olan **hedef koşul** aracılığıyla cihaz ikimleri kümelerini hedef alır. **Hedef içerik** , hedeflenen cihaz TWINS 'i içinde ayarlanacak olan istenen özellikler kümesidir. Hedef içerik IoT donanım üreticisi/tümleştiricisi tarafından tanımlanan cihaz ikizi yapısıyla hizalanmalıdır. **Ölçümler** , Device ikizi raporlanan Özellikler üzerindeki sorgulardır ve ayrıca IoT donanım üreticisi/tümleştiricisi tarafından tanımlanan Device ikizi yapısıyla de hizalanmalıdır.

   Otomatik cihaz yapılandırmaları yapılandırma oluşturulduktan kısa bir süre sonra ve ardından beş dakikalık aralıklarla ilk kez çalışır. Ayrıca, cihaz ikizi okuma ve güncelleştirme için [azaltma limitlerini](iot-hub-devguide-quotas-throttling.md) hiçbir zaman aşmayacak bir hızda cihaz ikizi işlemleri gerçekleştirmeye IoT Hub de faydalanır.

* **[Cihaz sağlama hizmetini](../iot-dps/how-to-manage-enrollments.md)kullanın:** çözüm geliştiricileri, cihaz ikizi etiketlerini yeni cihazlara atamak Için cihaz sağlama hizmeti 'ni kullanmalıdır. bu şekilde, bu etikete sahip TWINS 'e hedeflenmiş **otomatik cihaz yapılandırmalarına** göre otomatik olarak yapılandırılır. 

## <a name="iot-solution-operator"></a>IoT çözüm operatörü

Aşağıda, Azure üzerinde oluşturulmuş bir IoT çözümü kullanan IoT çözüm işleçleri için en iyi yöntemler verilmiştir:

* **Cihazları yönetim Için Düzenle:** IoT çözümü, Canary gibi çeşitli dağıtım stratejilerine göre kalite halkaları veya diğer cihaz kümelerinin oluşturulmasını tanımlamalıdır veya buna izin verir. Cihaz kümeleri, yapılandırma değişikliklerini almak ve diğer ölçekte cihaz yönetimi işlemlerini gerçekleştirmek için kullanılacaktır.

* **Aşamalı kullanıma alma kullanarak yapılandırma değişiklikleri gerçekleştirin:**  Aşamalı bir işlem, bir işlecin, bir dizi IoT cihazında değişiklikleri dağıttığı genel bir işlemdir. Amaç, geniş ölçekli büyük değişiklikler yapma riskini azaltmak için değişiklikler yavaş yavaş yapılır.  Operatör, bir [otomatik cihaz yapılandırması](iot-hub-auto-device-config.md) oluşturmak için çözümün arabirimini kullanmalıdır ve hedefleme koşulu bir ilk cihaz kümesini (örneğin, Kanarya grubu) hedeflemelidir. Sonra operatör ilk cihaz kümesindeki yapılandırma değişikliğini doğrulamalıdır.

   Doğrulama tamamlandıktan sonra operatör, otomatik cihaz yapılandırmasını daha büyük bir cihaz kümesi içerecek şekilde güncelleştirir. Operatör Ayrıca yapılandırmanın önceliğini, bu cihazlara yönelik olarak şu anda hedeflenen diğer yapılandırmalardan daha yüksek olacak şekilde ayarlamış olmalıdır. Kullanıma alma, otomatik cihaz yapılandırması tarafından bildirilen ölçümler kullanılarak izlenebilir.

* **Hata veya yanlış yapılandırma durumunda geri alma Işlemleri gerçekleştirin:**  Hatalara veya hata yapılandırmasına neden olan bir otomatik cihaz yapılandırması, cihazların hedefleme koşulunu artık karşılamaz şekilde **hedefleme koşulu** değiştirilerek geri alınabilir. Daha düşük önceliğe sahip başka bir otomatik cihaz yapılandırmasının hala bu cihazlar için hedeflediğinden emin olun. Ölçümleri görüntüleyerek geri almanın başarılı olduğunu doğrulayın: geri alma yapılandırması artık hedeflenmemiş cihazların durumunu göstermemelidir ve ikinci yapılandırmanın ölçümleri artık hedeflenmiş cihazların sayısını içermelidir.

## <a name="next-steps"></a>Sonraki adımlar

* Cihaz ikizlerini [anlama ve IoT Hub cihaz TWINS kullanma](iot-hub-devguide-device-twins.md)hakkında bilgi edinin.

* [IoT cihazlarını yapılandırma ve izleme](iot-hub-auto-device-config.md)bölümünde otomatik cihaz yapılandırması oluşturma, güncelleştirme veya silme adımlarını gözden geçir.

* Cihaz TWINS ve otomatik cihaz yapılandırması kullanarak bir bellenim güncelleştirme modelini uygulama [öğretici: bir cihaz üretici yazılımı güncelleştirme Işlemi uygulayın](tutorial-firmware-update.md).
