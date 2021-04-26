---
title: Azure Sertifikalı cihaz portalındaki bileşenler özelliğini kullanma
description: Cihazınızı doğru bir şekilde anlatmak için cihaz ayrıntıları bölümünün bileşenler özelliğinin en iyi şekilde kullanılması ile ilgili kılavuz
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: how-to
ms.date: 03/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 220a6c2107063734201064115898611c20cab650
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304469"
---
# <a name="add-components-on-the-portal"></a>Portala bileşen ekleme

Sertifika projenize [cihaz ayrıntılarını Ekleme öğreticisini](tutorial-02-adding-device-details.md) tamamlarken, cihazınızın donanım belirtimlerini açıklamanız gerekecektir. Bunu yapmak için, kullanıcılar cihazınızı oluşturan birden çok, ayrı donanım ürününü ( **Bileşenler** olarak adlandırılır) vurgulayabilir. Bu, ek donanımla birlikte gelen cihazları daha iyi yükseltebilmenizi ve müşterilerin kataloğu bu özelliklere göre arayarak doğru ürünü bulmasına olanak tanır.

## <a name="prerequisites"></a>Önkoşullar

- Oturumunuz açık olmalıdır ve [Azure Sertifikalı cihaz portalında](https://certify.azure.com)cihazınızda oluşturulmuş bir projeniz olmalıdır. Daha fazla bilgi için [öğreticiyi](tutorial-01-creating-your-project.md)görüntüleyin.

## <a name="how-to-add-components"></a>Bileşen ekleme

Sertifika için gönderilen her proje, **müşterinin hazırlamış** olduğu bir ürün bileşeni içerir (birçok durumda bütünsel ürününün kendisini temsil eder). Müşterinin hazırlamış olduğu ürün bileşen türünün fark düzeyini daha iyi anlamak için, [sertifika Sözlüğümüzü](./resources-glossary.md)görüntüleyin. Tüm ek bileşenler, cihazınızı doğru bir şekilde yakalamak için dahil etme kararına sahiptir.

1. `Add a component`Ürün Ayrıntıları sekmesinde öğesini seçin.

    ![Bileşen Bağlantısı Ekle](./media/images/add-a-component-link.png)

1. Bileşen için ilgili form alanlarını doldurun.

    ![Bileşen ayrıntıları bölümü](./media/images/component-details-section.png)

1. `Save Product Details`Sayfanın altındaki düğmeyi kullanarak bilgilerinizi kaydedin:  

    ![Ürün ayrıntılarını Kaydet düğmesi](./media/images/save-product-details-button.png)

1. Bileşeninizi kaydettikten sonra, desteklediği donanım yeteneklerini daha ayrıntılı bir şekilde uyarlayabilirsiniz. `Edit`Bileşen adına göre bağlantıyı seçin.  

    ![Bileşen düğmesini Düzenle](./media/images/component-edit.png)

1. Uygun durumlarda ilgili donanım yetenek bilgilerini sağlayın.  

    ![Düzenlenebilir bileşen bölümlerinin görüntüsü](./media/images/component-selection-area.png)  

    Düzenlenebilir bileşen alanları (yukarıda gösterilen) şunları içerir:

    - **Genel**: işlemciler ve güvenli donanımlar gibi donanım ayrıntıları
    - **Bağlantı**: bağlantı seçenekleri, protokoller ve radyo (ler) ve GIO gibi arabirimler
    - **Hızlandırıcılar**: GPU ve VPU gibi donanım ivmesini belirtme
    - **Algılayıcılar**: GPS ve titreşim gibi kullanılabilir sensörler belirtme
    - **Ek özellikler**: cihazla ilgili olarak fiziksel boyutlar ve depolama/pil bilgileri gibi ek bilgiler

1. `Save Product Details`Ürün Ayrıntıları sayfasının alt kısmında öğesini seçin.

## <a name="component-use-requirements-and-recommendations"></a>Bileşen kullanım gereksinimleri ve önerileri

Kaç bileşen ekleneceğini ya da kullanılacak bileşen türünü içeren sorularınız olabilir. Aşağıda, sertifikalarınızla ilgili birkaç örnek senaryo ve bileşenler özelliğinin nasıl kullanılacağı örnekleri verilmiştir.

| Ürün Türü                                       | Hayır. Bileşenler | Bileşen 1/ek türü      | Bileşenler 2 +/ek türü                    |
|----------------------------------------------------|------------|----------------------------------|--------------------------------------------------|
| Tamamlanmış ürün                                   | 1          | Müşteriye Ready ürün, ayrı | Yok                                              |
| **Çıkarılabilir çevre birimleri** Ile tamamlanmış ürün | 2 veya daha fazla  | Müşteriye Ready ürün, ayrı | Çevre birimi/ayrı veya tümleşik              |
| **Tümleşik bileşen (ler)** i olan ürün tamamlandı  | 2 veya daha fazla  | Müşteriye Ready ürün, ayrı | Uygun tür/ayrı veya Tümleşik seçeneğini belirleyin |
| Solution-Ready Dev Kit                             | 2 veya daha fazla  | Müşteriye Ready ürün, ayrı | Uygun tür/ayrı veya Tümleşik seçeneğini belirleyin |

## <a name="example-component-usage"></a>Örnek bileşen kullanımı

Aşağıda, contoso olarak adlandırılan bir OEM 'nin, ' nin, Falcon adlı ürünleri onaylamak için bileşenler özelliğini nasıl kullanacağı örnekleri verilmiştir.

1. Falcon, daha büyük bir ürünle tümleştirilebilen, tam bir tek başına cihazdır.
    1. Hayır. bileşenler: 1
    1. Bileşen cihaz türü: müşterinin Ready ürünü
    1. Ek türü: ayrık

     ![Müşterinin Ready ürününün görüntüsü](./media/images/customer-ready-product.png)

1. Falcon, ıNC Elektroniden Falcon ile bağlanan tümleşik bir çevrebirim kamera modülünü içeren bir cihazdır.
    1. Hayır. bileşenler: 2
    1. Bileşen cihaz türü: müşterinin hazırlanma ürünü, çevre birimi
    1. Ek türü: ayrık, tümleşik
    
    > [!Note]
    > Çevrebirim bileşeni, kaldırılabilir olmadığından tümleşik olarak değerlendirilir.

     ![Çevresel örnek bileşen görüntüsü](./media/images/peripheral.png)

1. Falcon, şirket Espressıo 'dan yerleşik bir işlemci Apollo52 kullanan ve bir ARM64 mimarisine sahip olan, ıNC Elektronideki tümleşik sistem modülünü içeren bir cihazdır.
    1. Hayır. bileşenler: 2
    1. Bileşen cihaz türü: müşterinin hazırlı ürünü, sistem modülünde sistem
    1. Ek türü: ayrık, tümleşik

    > [!Note]
    > Çevrebirim bileşeni, kaldırılabilir olmadığından tümleşik olarak değerlendirilir. SoM bileşeni de işlemci bilgilerini içerir.

     ![Modül örneği bileşenindeki sistem görüntüsü ](./media/images/system-on-module.png)

## <a name="additional-tips"></a>Ek ipuçları

Bileşen kullanım ilkeniz hakkında daha fazla açıklığa kavuşturuyoruz. Uygun bileşen kullanımı hakkında sorularınız varsa, ekibimize başvurun [iotcert@microsoft.com](mailto:iotcert@microsoft.com) ve yardım almak için mutluyuz!

1. Projenin **yalnızca** bir müşterinin hazırlamış ürün bileşeni içermesi gerekir. İki bağımsız cihazdan oluşan bir projeyi sertifikalandırdıysanız bu cihazların ayrı ayrı sertifikalı olması gerekir.
1. Cihazınızın yeteneklerini potansiyel müşterilere yükseltmek için öncelikle bileşenleri kullanmanız (veya kullanmamalıdır) gerekir.
1. Cihazınızın incelenmesi sırasında, Azure Sertifika ekibi yalnızca en az bir müşterinin izin verilen ürün bileşeni için gerekli olacaktır. Ancak, Ayrıntılar açık değilse veya eksik (örneğin, bir müşterinin sağladığı ürün türü için bileşen üreticisi sağlanmamışsa), bileşen bilgilerinde düzenlemeler isteyebiliriz.

## <a name="next-steps"></a>Sonraki adımlar

Artık bileşenlerimizi kullanmaya hazır olduğunuza göre artık cihaz ayrıntılarınızı tamamlamaya veya daha fazla açıklık için projenizi düzenlemenize hazırsınız demektir.

- [Öğretici: cihaz ayrıntıları ekleme](tutorial-02-adding-device-details.md)
- [Yayınlanan cihazınızı düzenleniyor](how-to-edit-published-device.md)

