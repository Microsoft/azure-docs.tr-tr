---
title: Azure IoT Hub cihaz sağlama hizmeti 'nde X. 509.440 sertifikalarını al
description: Cihaz sağlama hizmeti (DPS) örneğiniz ile X. 509.440 sertifikalarını alma
author: wesmc7777
ms.author: wesmc
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: bf8b1e04e11dee4e636826430838a467fe034e3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94951137"
---
# <a name="how-to-roll-x509-device-certificates"></a>X. 509.440 cihaz sertifikalarını alma

IoT çözümünüzün yaşam döngüsü boyunca sertifikaları almanız gerekir. Sıralı sertifikaların temel nedenlerinden ikisi bir güvenlik ihlali ve sertifika süre sonları olacaktır. 

Çalışan sertifikalar, bir ihlal durumunda sisteminizin güvenliğini sağlamaya yardımcı olmak için en iyi güvenlik uygulamasıdır. [Ihlal metodolojinin](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf)bir parçası olarak Microsoft, önleyici ölçülerle birlikte reaktif güvenlik işlemlerine sahip olma gereksinimini ortadan kaldırır. Cihaz sertifikalarınızın, bu güvenlik işlemlerinin bir parçası olarak eklenmesi gerekir. Sertifikalarınızı alacağınız sıklık, çözümünüzün güvenlik ihtiyaçlarına bağlı olarak değişir. Son derece hassas verileri içeren çözümleri olan müşteriler, sertifikaları her iki yılda bir kez sunarken, her gün sertifika alabilir.

Sıralı cihaz sertifikaları, cihazda depolanan sertifikanın ve IoT Hub 'ında güncelleştirilmesini içerir. Daha sonra cihaz, cihaz sağlama hizmeti (DPS) ile normal [sağlama](about-iot-dps.md#provisioning-process) kullanarak IoT Hub ile kendisini yeniden sağlayabilir.


## <a name="obtain-new-certificates"></a>Yeni sertifikalar al

IoT cihazlarınız için yeni sertifikalar almanın birçok yolu vardır. Bunlar cihaz fabrikasından sertifika alma, kendi sertifikalarınızı oluşturma ve üçüncü tarafa sizin için sertifika oluşturmayı yönetme sayılabilir. 

Sertifikalar bir kök CA sertifikasından [yaprak sertifikaya](concepts-x509-attestation.md#end-entity-leaf-certificate)bir güven zinciri oluşturmak için birbirleriyle imzalanır. İmza sertifikası, güven zincirinin sonunda yaprak sertifikayı imzalamak için kullanılan sertifikadır. İmza sertifikası bir kök CA sertifikası veya güven zincirindeki bir ara sertifika olabilir. Daha fazla bilgi için bkz. [X. 509.440 sertifikaları](concepts-x509-attestation.md#x509-certificates).
 
İmza sertifikası almanın iki farklı yolu vardır. Üretim sistemleri için önerilen ilk yöntem, bir kök sertifika yetkilisinden (CA) bir imzalama sertifikası satın alınabilecektir. Bu şekilde, güvenlik güvenliği güvenilen bir kaynağa bağlanır. 

İkinci yöntem, OpenSSL gibi bir araç kullanarak kendi X. 509.440 sertifikalarınızı oluşturmaktır. Bu yaklaşım, X. 509.440 sertifikalarını test etmek için idealdir, ancak güvenlik konusunda birkaç güvence sağlar. Bu yaklaşımı, kendi CA sağlayıcınız olarak davranmaya hazırlanmadığınız sürece yalnızca test için kullanmanızı öneririz.
 

## <a name="roll-the-certificate-on-the-device"></a>Sertifikayı cihazda topla

Bir cihazdaki sertifikaların her zaman bir [donanım güvenlik modülü (HSM)](concepts-service.md#hardware-security-module)gibi güvenli bir yerde depolanması gerekir. Cihaz sertifikalarını alma yöntemi, nasıl oluşturulduğuna ve ilk yerinde cihazlara nasıl yüklenediğinize bağlıdır. 

Bir üçüncü taraftan sertifikalarınızı aldıysanız, sertifikalarını nasıl ele alacağına bakmanız gerekir. İşlem, bu kendileriyle birlikte düzenize dahil edilebilir veya sundukları ayrı bir hizmet olabilir. 

Kendi cihaz sertifikalarınızı yönetiyorsanız, sertifikaları güncelleştirmek için kendi işlem hattınızı oluşturmanız gerekecektir. Hem eski hem de yeni yaprak sertifikalarının aynı ortak ada (CN) sahip olduğundan emin olun. Aynı CN 'ye sahip olunarak cihaz, yinelenen bir kayıt kaydı oluşturmadan kendisini yeniden temin edebilir. 


## <a name="roll-the-certificate-in-the-iot-hub"></a>Sertifikayı IoT Hub 'ında alma

Cihaz sertifikası bir IoT Hub 'ına el ile eklenebilir. Sertifika, cihaz sağlama hizmeti örneği kullanılarak otomatik olarak da yapılabilir. Bu makalede, bir cihaz sağlama hizmeti örneğinin otomatik sağlamayı desteklemek için kullanıldığını varsayacağız.

Bir cihaz başlangıçta otomatik sağlama aracılığıyla sağlandığında, önyüklenir ve sağlama hizmeti ile iletişim kurar. Sağlama Hizmeti, kimlik bilgileri olarak cihazın yaprak sertifikasını kullanarak bir IoT Hub 'ında cihaz kimliği oluşturmadan önce kimlik denetimi gerçekleştirerek yanıt verir. Sağlama hizmeti daha sonra cihaza hangi IoT Hub 'ının atandığını bildirir ve cihaz daha sonra IoT Hub 'ına kimlik doğrulaması ve bağlantı kurmak için onun yaprak sertifikasını kullanır. 

Cihaza yeni bir yaprak sertifikası alındıktan sonra, bağlanmak için yeni bir sertifika kullandığından, artık IoT Hub 'ına bağlanamaz. IoT Hub, cihazı yalnızca eski sertifikaya sahip olarak tanır. Cihazın bağlantı denemesinin sonucu "yetkisiz" bir bağlantı hatası olacaktır. Bu hatayı çözmek için cihazın yeni yaprak sertifikası için hesap için kayıt girişini güncelleştirmeniz gerekir. Ardından, sağlama hizmeti cihaz yeniden sağlandığında gereken cihaz kayıt defteri bilgilerini IoT Hub güncelleştirebilir. 

Bu bağlantı hatasının olası bir istisnası, sağlama hizmeti 'nde cihazınız için bir [kayıt grubu](concepts-service.md#enrollment-group) oluşturduğunuz bir senaryodur. Bu durumda, cihazın sertifika güven zincirinde kök veya ara sertifikaları görmüyorsanız, yeni sertifika kayıt grubunda tanımlanan güven zincirinin bir parçasıysa cihaz tanınır... Bu senaryo bir güvenlik ihlalinin yeniden eylemi olarak ortaya çıkarsa, grupta ihlal edilen belirli cihaz sertifikalarına en azından izin vermemelisiniz. Daha fazla bilgi için bkz. [bir kayıt grubundaki belirli cihazlara Izin verme](./how-to-revoke-device-access-portal.md#disallow-specific-devices-in-an-enrollment-group).

Toplu sertifikaların kayıt girişlerini güncelleştirme, kayıtları **Yönet** sayfasında gerçekleştirilir. Bu sayfaya erişmek için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com) oturum açın ve cihazınız için kayıt girişi olan IoT Hub cihaz sağlama hizmeti örneğine gidin.

2. **Kayıtları yönetme**'ye tıklayın.

    ![Kayıtları yönetme](./media/how-to-roll-certificates/manage-enrollments-portal.png)


Kayıt girişinin güncelleştirilmesini nasıl işleyeceğinizi, bireysel kayıtları veya grup kayıtlarını kullandığınıza bağlı olarak değişir. Ayrıca Önerilen yordamlar, bir güvenlik ihlali veya sertifika süre sonu nedeniyle sertifika isteme yönteminize bağlı olarak farklılık gösterir. Aşağıdaki bölümlerde bu güncelleştirmelerin nasıl işleneceği açıklanır.


## <a name="individual-enrollments-and-security-breaches"></a>Bireysel kayıtlar ve güvenlik ihlalleri

Bir güvenlik ihlaline yanıt olarak sertifika alıyorsanız, geçerli sertifikayı hemen silen aşağıdaki yaklaşımı kullanmanız gerekir:

1. **Ayrı** kayıtlar ' a tıklayın ve LISTEDEKI kayıt kimliği girişine tıklayın. 

2. **Geçerli sertifikayı Sil** düğmesine tıklayın ve ardından kayıt girişi için karşıya yüklenecek yeni sertifikayı seçmek için klasör simgesine tıklayın. İşiniz bittiğinde **Kaydet** ' e tıklayın.

    Her ikisi de tehlikeye girerse, birincil ve ikincil sertifika için bu adımların tamamlanması gerekir.

    ![Bireysel kayıtları bir güvenlik ihlali ile yönetme](./media/how-to-roll-certificates/manage-individual-enrollments-portal.png)

3. Güvenliği aşılan sertifika, sağlama hizmetinden kaldırıldıktan sonra, bu sertifika için bir cihaz kaydı olduğu sürece bu sertifika, IoT Hub 'ına cihaz bağlantısı kurmak için yine de kullanılabilir. Bu iki yolu ele alabilirsiniz: 

    İlk yöntem, IoT Hub 'ınıza el ile gitmeniz ve güvenliği aşılmış sertifikayla ilişkili cihaz kaydını hemen kaldırmanız olacaktır. Daha sonra cihaz güncelleştirilmiş bir sertifikayla yeniden sağlama yaparken yeni bir cihaz kaydı oluşturulur.     

    ![IoT Hub cihaz kaydını kaldır](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    İkinci yöntem, cihazı aynı IoT Hub 'ına yeniden sağlamak için yeniden sağlama desteğini kullanmaktır. Bu yaklaşım, IoT Hub 'ındaki cihaz kaydı için sertifikayı değiştirmek üzere kullanılabilir. Daha fazla bilgi için bkz. [cihazları yeniden sağlama](how-to-reprovision.md).

## <a name="individual-enrollments-and-certificate-expiration"></a>Bireysel kayıtlar ve sertifika süre sonu

Sertifika süre sonlarını işlemek için bir sertifika alıyorsanız, sağlama girişiminde bulunan cihazların kapalı kalma süresini azaltmak için ikincil sertifika yapılandırmasını aşağıdaki şekilde kullanmanız gerekir.

Daha sonra ikincil sertifika süre sonu yaklaştığında ve alınması gerektiğinde, birincil yapılandırmayı kullanarak döndürebilirsiniz. Bu şekilde birincil ve ikincil Sertifikalar arasında döndürme, sağlamaya çalışan cihazların kapalı kalma süresini azaltır.


1. **Ayrı** kayıtlar ' a tıklayın ve LISTEDEKI kayıt kimliği girişine tıklayın. 

2. **Ikincil sertifika** ' ya tıklayın, ardından kayıt girişi için karşıya yüklenecek yeni sertifikayı seçmek için klasör simgesine tıklayın. **Kaydet**’e tıklayın.

    ![İkincil sertifika süre sonunu kullanarak bireysel kayıtları yönetme](./media/how-to-roll-certificates/manage-individual-enrollments-secondary-portal.png)

3. Daha sonra birincil sertifikanın süresi dolduğunda, **geçerli sertifikayı Sil** düğmesine tıklayarak geri dönüp ilgili birincil sertifikayı silin.

## <a name="enrollment-groups-and-security-breaches"></a>Kayıt grupları ve güvenlik ihlaller

Bir güvenlik ihlaline yanıt olarak bir grup kaydını güncelleştirmek için, geçerli kök CA 'yı veya ara sertifikayı hemen silecek aşağıdaki yaklaşımlardan birini kullanmanız gerekir.

#### <a name="update-compromised-root-ca-certificates"></a>Güvenliği aşılmış kök CA sertifikalarını güncelleştirme

1. Cihaz sağlama hizmeti örneğiniz için **Sertifikalar** sekmesine tıklayın.

2. Listede güvenliği aşılmış sertifikaya tıklayın ve sonra **Sil** düğmesine tıklayın. Sertifika adını girip **Tamam**' a tıklayarak silmeyi onaylayın. Tüm güvenliği aşılmış sertifikalar için bu işlemi tekrarlayın.

    ![Kök CA sertifikasını Sil](./media/how-to-roll-certificates/delete-root-cert.png)

3. Yeni kök CA sertifikaları eklemek ve doğrulamak için [DOĞRULANMıŞ CA sertifikalarını yapılandırma](how-to-verify-certificates.md) bölümünde özetlenen adımları izleyin.

4. Cihaz sağlama hizmeti örneğinizin kayıtları **Yönet** sekmesine tıklayın ve **kayıt grupları** listesine tıklayın. Listede kayıt grubunuz adına tıklayın.

5. **CA sertifikası**' na tıklayın ve yenı kök CA sertifikanızı seçin. Daha sonra **Kaydet**'e tıklayın. 

    ![Güvenliği aşılmış bir sertifika için yeni kök CA sertifikasını seçin](./media/how-to-roll-certificates/select-new-root-cert.png)

6. Güvenliği aşılan sertifika, sağlama hizmetinden kaldırıldıktan sonra, cihaz kayıtları mevcut olduğu sürece bu sertifika IoT Hub 'ına cihaz bağlantısı kurmak için yine de kullanılabilir. Bu iki yolu ele alabilirsiniz: 

    İlk yöntem, IoT Hub 'ınıza el ile gitmeniz ve güvenliği aşılmış sertifikayla ilişkili cihaz kaydını hemen kaldırmanız olacaktır. Ardından, cihazlarınız güncelleştirilmiş sertifikalarla yeniden sağladığınızda, her biri için yeni bir cihaz kaydı oluşturulur.     

    ![IoT Hub cihaz kaydını kaldır](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    İkinci yöntem, cihazlarınızı aynı IoT Hub 'ına yeniden sağlamak için yeniden sağlama desteğini kullanmaktır. Bu yaklaşım, IoT Hub 'ında cihaz kayıtları için sertifikaları değiştirmek üzere kullanılabilir. Daha fazla bilgi için bkz. [cihazları yeniden sağlama](how-to-reprovision.md).



#### <a name="update-compromised-intermediate-certificates"></a>Güvenliği aşılmış ara sertifikaları güncelleştirme

1. **Kayıt grupları**' na tıklayın ve ardından listedeki Grup adına tıklayın. 

2. **Ara Sertifika**' a tıklayın ve **geçerli sertifikayı silin**. Kayıt grubuna yüklenecek yeni ara sertifikaya gitmek için klasör simgesine tıklayın. İşiniz bittiğinde **Kaydet** ' e tıklayın. Her ikisi de tehlikeye girerse, hem birincil hem de ikincil sertifika için bu adımların tamamlanması gerekir.

    Bu yeni ara sertifika, sağlama hizmetine zaten eklenmiş olan doğrulanmış bir kök CA sertifikası tarafından imzalanmalıdır. Daha fazla bilgi için bkz. [X. 509.440 sertifikaları](concepts-x509-attestation.md#x509-certificates).

    ![Güvenliği aşılmış bir ara için bireysel kayıtları yönetme](./media/how-to-roll-certificates/enrollment-group-delete-intermediate-cert.png)


3. Güvenliği aşılan sertifika, sağlama hizmetinden kaldırıldıktan sonra, cihaz kayıtları mevcut olduğu sürece bu sertifika IoT Hub 'ına cihaz bağlantısı kurmak için yine de kullanılabilir. Bu iki yolu ele alabilirsiniz: 

    İlk yöntem, IoT Hub 'ınıza el ile gitmeniz ve güvenliği aşılmış sertifikayla ilişkili cihaz kaydını hemen kaldırmanız olacaktır. Ardından, cihazlarınız güncelleştirilmiş sertifikalarla yeniden sağladığınızda, her biri için yeni bir cihaz kaydı oluşturulur.     

    ![IoT Hub cihaz kaydını kaldır](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    İkinci yöntem, cihazlarınızı aynı IoT Hub 'ına yeniden sağlamak için yeniden sağlama desteğini kullanmaktır. Bu yaklaşım, IoT Hub 'ında cihaz kayıtları için sertifikaları değiştirmek üzere kullanılabilir. Daha fazla bilgi için bkz. [cihazları yeniden sağlama](how-to-reprovision.md).


## <a name="enrollment-groups-and-certificate-expiration"></a>Kayıt grupları ve sertifika süre sonu

Sertifika süre sonlarını işlemek için bir sertifika alıyorsanız, cihazların sağlanması denenmeye kapalı kalma süresi olmadığından emin olmak için ikincil sertifika yapılandırmasını aşağıdaki şekilde kullanmanız gerekir.

Daha sonra ikincil sertifika süre sonu yaklaştığında ve alınması gerektiğinde, birincil yapılandırmayı kullanarak döndürebilirsiniz. Birincil ve ikincil Sertifikalar arasında bu şekilde döndürme olanağı sağlamaya çalışan cihazlar için kapalı kalma süresi olmamasını sağlar. 

#### <a name="update-expiring-root-ca-certificates"></a>Süresi dolan kök CA sertifikalarını Güncelleştir

1. Yeni kök CA sertifikaları eklemek ve doğrulamak için [DOĞRULANMıŞ CA sertifikalarını yapılandırma](how-to-verify-certificates.md) bölümünde özetlenen adımları izleyin.

2. Cihaz sağlama hizmeti örneğinizin kayıtları **Yönet** sekmesine tıklayın ve **kayıt grupları** listesine tıklayın. Listede kayıt grubunuz adına tıklayın.

3. **CA sertifikası**' na tıklayın ve **İkincil sertifika** YAPıLANDıRMASı altında yeni kök CA sertifikanızı seçin. Daha sonra **Kaydet**'e tıklayın. 

    ![Süre sonu için yeni kök CA sertifikasını seçin](./media/how-to-roll-certificates/select-new-root-secondary-cert.png)

4. Daha sonra birincil sertifikanın süresi dolduğunda, cihaz sağlama hizmeti örneğiniz için **Sertifikalar** sekmesine tıklayın. Listedeki zaman aşımına uğradı sertifikasına tıklayın ve sonra **Sil** düğmesine tıklayın. Sertifika adını girerek silmeyi onaylayın ve **Tamam**' a tıklayın.

    ![Kök CA sertifikasını Sil](./media/how-to-roll-certificates/delete-root-cert.png)



#### <a name="update-expiring-intermediate-certificates"></a>Süresi dolan ara sertifikaları güncelleştir


1. **Kayıt grupları**' na tıklayın ve listede Grup adına tıklayın. 

2. **Ikincil sertifika** ' ya tıklayın, ardından kayıt girişi için karşıya yüklenecek yeni sertifikayı seçmek için klasör simgesine tıklayın. **Kaydet**’e tıklayın.

    Bu yeni ara sertifika, sağlama hizmetine zaten eklenmiş olan doğrulanmış bir kök CA sertifikası tarafından imzalanmalıdır. Daha fazla bilgi için bkz. [X. 509.440 sertifikaları](concepts-x509-attestation.md#x509-certificates).

   ![İkincil sertifikayı kullanarak kayıt gruplarını yönetme süresi doluyor](./media/how-to-roll-certificates/manage-enrollment-group-secondary-portal.png)

3. Daha sonra birincil sertifikanın süresi dolduğunda, **geçerli sertifikayı Sil** düğmesine tıklayarak geri dönüp ilgili birincil sertifikayı silin.


## <a name="reprovision-the-device"></a>Cihazı yeniden sağlama

Sertifika hem cihaz hem de cihaz sağlama hizmeti üzerinde alındıktan sonra cihaz, cihaz sağlama hizmeti ile iletişim kurarak kendisini yeniden sağlayabilir. 

Cihazları yeniden sağlamak için programlamanın kolay bir yolu, cihazın IoT Hub 'ına bağlanmaya çalışırken bir "yetkisiz" hata alırsa sağlama akışı üzerinden gezinmek üzere cihazı sağlama hizmeti ile iletişim kurmak üzere programlayacaktır.

Bu, hem eski hem de yeni sertifikaların kısa bir çakışma için geçerli olması ve cihazların IoT Hub bağlantı bilgilerini güncelleştirmek için sağlama hizmeti aracılığıyla yeniden kaydolmasını sağlamak üzere IoT Hub 'ı kullanır. Her cihaz komutları farklı şekilde işleyebildiğinden, komut çağrıldığında ne yapılacağını bilmeniz için cihazınızı programlayabilirsiniz. Cihazınızı IoT Hub aracılığıyla komut için kullanabileceğiniz çeşitli yollar vardır ve işlemi başlatmak için [doğrudan yöntemlerin](../iot-hub/iot-hub-devguide-direct-methods.md) veya [işlerin](../iot-hub/iot-hub-devguide-jobs.md) kullanılmasını öneririz.

Yeniden sağlama işlemi tamamlandıktan sonra, cihazlar yeni sertifikalarını kullanarak IoT Hub bağlanabiliyor.


## <a name="disallow-certificates"></a>Sertifikalara izin verme

Bir güvenlik ihlaline yanıt olarak, bir cihaz sertifikasına izin vermemelisiniz. Bir cihaz sertifikasına izin vermemek için, hedef cihaz/sertifika için kayıt girişini devre dışı bırakın. Daha fazla bilgi için bkz. [kayıt yönetme](how-to-revoke-device-access-portal.md) makalesindeki cihazlara izin vermeme.

Devre dışı bırakılmış bir kayıt girişinin bir parçası olarak bir sertifika eklendikten sonra, başka bir kayıt girişinin bir parçası olarak etkinleştirilse bile bu sertifikaları kullanarak IoT Hub 'ına kaydolma girişimleri başarısız olur.
 



## <a name="next-steps"></a>Sonraki adımlar

- Cihaz sağlama hizmeti 'nde X. 509.440 sertifikaları hakkında daha fazla bilgi edinmek için bkz [. x. 509.440 sertifika kanıtlama](concepts-x509-attestation.md) 
- Azure IoT Hub cihaz sağlama hizmeti ile X. 509.952 CA sertifikaları için birlikte kullanma hakkında bilgi edinmek için bkz. [sertifikaları doğrulama](how-to-verify-certificates.md)
- Portal 'ın bir kayıt grubu oluşturmak üzere nasıl kullanılacağı hakkında bilgi edinmek için bkz. [Azure Portal cihaz kayıtlarını yönetme](how-to-manage-enrollments.md).