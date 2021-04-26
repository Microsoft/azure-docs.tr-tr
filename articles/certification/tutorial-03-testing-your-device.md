---
title: Azure Sertifikalı cihaz programı-öğretici-cihazınızı test etme
description: Azure Sertifikalı cihaz portalında AICS hizmeti ile cihazınızı test etmek için adım adım kılavuz
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial
ms.openlocfilehash: ef82d44ef44189c0430ba9789baf3279fbe49a9c
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310963"
---
# <a name="tutorial-test-and-submit-your-device"></a>Öğretici: cihazınızı test etme ve gönderme

Sertifika işleminin bir sonraki ana aşaması (cihaz ayrıntılarınızı eklemeden önce tamamlanabilir) cihazınızın test edilmesini içerir. Portal aracılığıyla, sertifika gereksinimlerinize göre cihaz performansınızı göstermek için Azure IoT sertifika hizmeti 'ni (AICS) kullanacaksınız. Sınama aşamasını başarıyla geçirdikten sonra, cihazınızı Son Gözden geçirme ve Azure Sertifika ekibi tarafından onay için gönderebilirsiniz!

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Cihaz sağlama hizmeti 'ni (DPS) kullanarak cihazınızı IoT Hub bağlama
> * Cihazınızı seçtiğiniz sertifika programınıza göre test edin
> * Cihazınızı Azure Sertifika ekibi tarafından gözden geçirilmek üzere gönderme

## <a name="prerequisites"></a>Önkoşullar

- Oturumunuz açık olmalıdır ve [Azure Sertifikalı cihaz portalında](https://certify.azure.com)cihazınızda oluşturulmuş bir projeniz olmalıdır. Daha fazla bilgi için [öğreticiyi](tutorial-01-creating-your-project.md)görüntüleyin.
- Seçim Cihazınızı hazırlamanızı ve sertifika gereksinimlerine göre performansını el ile doğrulamanızı öneririz. Bunun nedeni, farklı cihaz kodu veya sertifika programı ile yeniden test etmek istediğinizde, yeni bir proje oluşturmanız gerekecektir.

## <a name="connecting-your-device-using-dps"></a>DPS kullanarak cihazınızı bağlama

Tüm sertifikalı cihazlar, DPS kullanarak IoT Hub bağlanma yeteneğini göstermek için gereklidir. Aşağıdaki adımlarda, portalda test için cihazınızı başarıyla nasıl bağlayacağız.

1. Test aşamasına başlamak için, `Connect & test` Proje Özeti sayfasında bağlantıyı seçin:  

    ![Bağlanma ve test bağlantısı](./media/images/connect-and-test-link.png)

1. Seçili sertifika (ler) i ' ne bağlı olarak, ' & test Bağlan ' sayfasında gerekli testleri görürsünüz. Doğru sertifika programı için uygulama olduğunuzdan emin olmak için bunları gözden geçirin.  

    ![Bağlan ve sına sayfası](./media/images/connect-and-test.png)

1. Cihaz sağlama hizmeti 'ni (DPS) kullanarak cihazınızı IoT Hub bağlayın. DPS, simetrik anahtarlar, X. 509.440 sertifikası ve bir Güvenilir Platform Modülü (TPM) bağlantı seçeneklerini destekler. Bu, tüm sertifikalar için gereklidir.

    - *Cihazınızı DPS 'e Azure IoT Hub bağlama hakkında daha fazla bilgi için bkz. [sağlama cihazlarına genel bakış](../iot-dps/about-iot-dps.md "Cihaz sağlama hizmetine genel bakış").*
    
1. Simetrik anahtarlar kullanıyorsanız, DPS 'yi sağlanan DPS KIMLIK kapsamı, cihaz KIMLIĞI, kimlik doğrulama anahtarı ve DPS uç noktasıyla yapılandırmanız istenir. Aksi takdirde, sizden X. 509.440 sertifikası veya onay anahtarı girmeniz istenir.

1. Cihazınızı DPS ile yapılandırdıktan sonra sayfanın alt kısmındaki düğmeye tıklayarak bağlantıyı onaylayın `Connect` . Başarılı bağlantı kurulduğunda, düğmeye tıklayarak test aşamasına geçebilirsiniz `Next` .  

    ![Bağlanma ve test bağlı](./media/images/connected.png)

## <a name="testing-your-device"></a>Cihazınızı test etme

Cihazınızı ACS 'ye başarıyla bağladıktan sonra, artık için uyguladığınız sertifika programına özgü sertifika testlerini çalıştırmaya hazırsınız demektir.

1. **Azure Sertifikalı cihaz sertifikası için**: ' cihaz özelliği Seç ' sekmesinde, cihazınızda hangi testlerin çalıştırılmasını istediğinizi inceleyip seçersiniz.
1. **Iot Tak ve kullan sertifikası için**: cihaz modelinizde bildirdiğiniz test sırasında denetlenecek parametreleri dikkatle gözden geçirin.
1. **Sınır yönetimli sertifika için**: bağlantı gösterilmesi dışında başka bir adım gerekmez.
1. Belirtilen sertifika programı için gerekli hazırlıkları tamamladıktan sonra `Next` ' test ' aşamasına devam etmek için ' ı seçin.
1. `Run tests`Cihaz Ile AıCS çalıştırmaya başlamak için sayfada öğesini seçin.
1. Testleri geçirdiğini belirten bir bildirim aldıktan sonra `Finish` Özet sayfanıza geri dönmek için öğesini seçin.

![Test geçildi](./media/images/test-pass.png)

7. Daha fazla sorunuz varsa veya ACS ile ilgili sorun giderme yardımı gerekiyorsa, sorun giderme kılavuzumuzu ziyaret edin.

> [!NOTE]
> Cihazınızın el ile gözden geçirilmesi için gönderilmesi gerekmeden IoT Tak ve Kullan ve Edge için çevrimiçi sertifika sürecini tamamlayabileceksiniz, ancak Otomasyon hizmetimizde test edilmiş olan daha fazla cihaz doğrulaması için bir Azure Sertifikalı cihaz ekibi üyesi tarafından bağlantı kurulacaksınız.

## <a name="submitting-your-device-for-review"></a>Cihazınızı gözden geçirme için gönderme

' Cihaz ayrıntıları ' bölümündeki tüm zorunlu alanları tamamladıktan ve ' Connect & test ' işleminde otomatik testi başarıyla geçirdikten sonra, Azure Sertifikalı cihaz ekibine sertifika incelemesi için hazır olduğunu bildirebilirsiniz.

1. `Submit for review`Proje Özeti sayfasında seçin:  

    ![İnceleme ve onaylama bağlantısı](./media/images/review-and-certify.png)

1. Açılır pencerede Gönderiminizi onaylayın. Bir cihaz gönderildikten sonra, düzen istenene kadar tüm cihaz ayrıntıları salt okunurdur. (Bkz. [yayımlamadan sonra cihaz bilgilerinizi düzenleme](./how-to-edit-published-device.md).)  

    ![Sertifika incelemesi Başlat iletişim kutusu](./media/images/start-certification-review.png)

1. Proje gönderildikten sonra proje özet sayfası projenin `Under Certification Review` Azure Sertifika ekibi tarafından olduğunu belirtir:  

    ![Inceleme altında](./media/images/review-and-certify-under-review.png)

1. 5-7 iş günü içinde, Azure Sertifika ekibinden, cihaz gönderiminizin durumuyla ilgili olarak şirket profilinizde belirtilen adrese bir e-posta yanıtı gönderilmesini bekler.

    - Onaylanan gönderim  
        Projeniz incelendikten ve onaylandıktan sonra bir e-posta alırsınız. E-postada, Azure Sertifikalı cihaz rozet, rozet Kullanım yönergeleri ve cihazınızın sertifikalı olduğu iletiyi nasıl artırılmasıyla ilgili diğer bilgiler dahil olmak üzere bir dizi dosya dahil edilir. Tebrikler!

    - Gönderim bekleniyor  
        Projeniz onaylanmamışsa, proje ayrıntılarına değişiklik yapıp, sonra cihazı sertifika için yeniden gönderebilirsiniz. Projenin neden onaylanmamış ve sertifika için yeniden gönderme adımları hakkında bilgi içeren bir e-posta gönderilir.

## <a name="next-steps"></a>Sonraki adımlar

Tebrikler! Cihazınız artık tüm testleri başarıyla geçti ve Azure Sertifikalı cihaz programı aracılığıyla onaylandı. Artık cihazınızı Azure Sertifikalı cihaz kataloğumuza yayımlayabilirsiniz. böylece müşteriler, Azure ile performanslarını güvenle satın alabilir.
> [!div class="nextstepaction"]
> [Öğretici: cihazınızı yayımlama](tutorial-04-publishing-your-device.md)

