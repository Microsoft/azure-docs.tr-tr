---
title: Cihaz paketleri ve dolaylı olarak bağlı cihazlar için sertifika
titleSuffix: Azure Certified
description: Bkz. sertifika için dolaylı olarak bağlı bir cihaz gönderme.
author: cbroad
ms.author: cbroad
ms.date: 02/23/2021
ms.topic: how-to
ms.service: certification
ms.openlocfilehash: 3a4fd2838c0ddf6d7d03d68f105fc59471b77dea
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304486"
---
# <a name="device-bundles-and-indirectly-connected-devices"></a>Cihaz paketleri ve dolaylı olarak bağlı cihazlar

Bir cihaz, SaaS veya PaaS teklifleri, gönderim portalı (ve cihaz kataloğu) aracılığıyla Azure ile etkileşime geçen cihazları desteklemek için https://certify.azure.com/) ( https://devicecatalog.azure.com) paket oluşturma ve bağımlılıklar kavramlarını etkinleştirin ve bu cihaz birleşimlerini yükseltmek ve etkinleştirmek Için Azure Sertifikalı cihaz programımızın erişimini etkinleştirin.

Sizin için sunulan ürün hattına ve hizmetlere bağlı olarak, koşulunuzda şu adımların bir birleşimi gerekebilir:


![Proje bağımlılıkları oluştur](./media/indirect-connected-device/picture-1.png )
## <a name="sensors-and-indirect-devices"></a>Sensörler ve dolaylı cihazlar
Birçok algılayıcı, cihazın Azure 'a bağlanmasını gerektirir. Ayrıca, algılayıcı cihazla çalışacak birden fazla uyumlu cihazınız olabilir. **Bu senaryolara uyum sağlamak için, önce bu cihazları kullanarak bilgi geçiren algılayıcıyı doğrulamadan önce cihazları sertifikalandırmalısınız.**

Gönderim birleşimlerinin örnek matrisi ![ gönderme örneği](./media/indirect-connected-device/picture-2.png )

Algılayıcıyı onaylamak için, ayrı bir cihaz gerekir:
1.  İlk olarak, [cihazı onaylayın](https://certify.azure.com) ve Azure Sertifikalı cihaz kataloğuna yayımlayın
    - Birden çok, uyumlu geçiş cihazunuz varsa (Yukarıdaki örnekte olduğu gibi), sertifikaları sertifika için ayrı olarak ve kataloğa da yayımlayın
2.  Cihaz üzerinden bağlı sensörle, sertifika için sensör gönder
    * "Cihaz ayrıntıları&quot; bölümünün &quot;bağımlılıklar&quot; sekmesinde aşağıdaki değerleri ayarlayın
        * Bağımlılık türü = &quot;donanım ağ geçidi&quot;
        * Bağımlılık URL = &quot;cihaz kataloğunda cihaza URL bağlantısı&quot;
        * Test sırasında kullanılır = &quot;Evet&quot;
        * Cihaz kataloğunda ürün açıklamasını görebilecek bir kullanıcıya sağlanması gereken, müşteriye yönelik herhangi bir yorum ekleyin. (örnek: &quot;Azure 'a bağlanmak için algılayıcılar için seri 100 cihazları gereklidir")

3.  Bu cihaz için isteğe bağlı olarak eklenmesini istediğiniz daha fazla cihaz varsa, "+ ek bağımlılık Ekle" seçeneğini belirleyebilirsiniz. Ardından aynı kılavuza uyun ve test sırasında kullanılmadığını unutmayın. Müşteriye yönelik açıklamalarda, müşterilerinizin bu algılayıcı ile ilişkili diğer cihazların (test sırasında kullanılan cihaza alternatif olarak) bulunduğunu unutmayın.

![Alternatif metin](./media/indirect-connected-device/picture-3.png "Donanım bağımlılığı türü")

## <a name="paas-and-saas-offerings"></a>PaaS ve SaaS teklifleri
Ürün portföyünüzün bir parçası olarak, sizin onaylayabilmeniz gereken cihazlara sahip olabilirsiniz, ancak cihazınız şirketinizde veya diğer üçüncü taraf şirketlerinizden başka hizmetler de gerektirir. Bu bağımlılığı eklemek için aşağıdaki adımları izleyin:
1. Cihazınız için gönderim sürecini başlatın
2. "Bağımlılıklar" sekmesinde aşağıdaki değerleri ayarlayın
    - Bağımlılık türü = "yazılım hizmeti"
    - Hizmet adı = "[ürününüzün adı]"
    - Bağımlılık URL = "hizmeti açıklayan bir ürün sayfasına URL bağlantısı"
    - Azure Sertifikalı cihaz kataloğunda ürün açıklamasını sunan bir kullanıcıya sağlanması gereken müşterilere yönelik yorumlarınızı ekleyin
3. Bu cihaz için isteğe bağlı olarak eklenmesini istediğiniz başka yazılım, hizmet veya donanım bağımlılıkları varsa, "+ ek bağımlılık Ekle" seçeneğini belirleyip aynı Kılavuzu izleyebilirsiniz.

![Yazılım bağımlılığı türü](./media/indirect-connected-device/picture-4.png )

## <a name="bundled-products"></a>Paketlenmiş ürünler
Paketlenmiş ürün listeleri, tek bir ürün listesinde paketin bir parçası olarak satılacak başka bileşenlere sahip bir cihazın başarılı bir şekilde sertifikasıdır. Sıcaklık algılayıcısı ve kamera algılayıcısı (#1) gibi ek bileşenleri içeren bir cihaz gönderebilirsiniz veya bir geçiş cihazı (#2) içeren bir dokunma algılayıcısı gönderebilirsiniz. "Bileşen" özelliği sayesinde, listelemesine birden çok bileşen ekleyebilme olanağınız vardır.

Bunu yapmak istiyorsanız, ürün listesi görüntüsünü, bu ürünün diğer bileşenleriyle geldiğini gösterecek şekilde biçimlendirirsiniz.  Ayrıca, paketlerinizin onaylamak için ek hizmetler gerekiyorsa, bunları hizmetler bağımlılığı aracılığıyla belirlemeniz gerekir.
Paketlenmiş ürünlerin örnek matrisi

![Paket Gönderimi örneği](./media/indirect-connected-device/picture-5.png )

Azure Sertifikalı cihaz portalındaki bileşen işlevselliğinin nasıl kullanılacağına ilişkin daha ayrıntılı bir açıklama için [Yardım belgelerimize](./how-to-using-the-components-feature.md)bakın. 

Bir cihaz aynı üründe ayrı bir algılayıcı içeren bir geçiş cihazı ise, geçiş cihazını yansıtmak için bir bileşen oluşturun ve algılayıcısı yansıtmak için başka bir bileşen oluşturun. Bileşenler, cihaz ayrıntıları bölümünün Ürün Ayrıntıları sekmesinde projenize eklenebilir:

![Bileşenler ekleme](./media/indirect-connected-device/picture-6.png )

Geçiş cihazı için, bileşen türünü müşterinin hazırlı bir ürün olarak ayarlayın ve diğer alanları ürününüz için uygun olarak girin. Örnek:

![Bileşen ayrıntıları](./media/indirect-connected-device/picture-7.png )

Algılayıcı için, ikinci bir bileşen ekleyin, bileşen türünü çevre birimi ve ek yöntemi olarak ayrı olarak ayarlar. Örnek:

![İkinci bileşen ayrıntıları](./media/indirect-connected-device/picture-8.png )

Algılayıcı bileşeni oluşturulduktan sonra, ayrıntıları düzenleyin, algılayıcılar sekmesine gidin ve ardından algılayıcı ayrıntılarını ekleyin. Örnek:

![Algılayıcı ayrıntıları](./media/indirect-connected-device/picture-9.png )

Proje ayrıntılarınızı tamamlayıp cihazınızı sertifika için normal olarak gönderme.

