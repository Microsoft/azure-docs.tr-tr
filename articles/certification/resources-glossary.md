---
title: Azure Sertifikalı cihaz programı sözlüğü
description: Azure Sertifikalı cihaz programında kullanılan yaygın koşulların listesi
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: conceptual
ms.date: 03/03/2021
ms.custom: template-concept
ms.openlocfilehash: f58c29c6bd2c22f37d2285ac6e93c6f54e47c4e0
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305098"
---
# <a name="azure-certified-device-program-glossary"></a>Azure Sertifikalı cihaz programı sözlüğü

Bu kılavuz, Azure Sertifikalı cihaz programında ve portalında yaygın olarak kullanılan koşulların tanımlarını sağlar. Sertifika sürecini açıklama için bu sözlüğü inceleyin. Kolaylık olması için bu sözlük, hakkında sorularınız olabilecek önemli sertifika kavramlarıyla sınıflandırılır.

## <a name="device-class"></a>Cihaz sınıfı

Sertifika projenizi oluştururken bir cihaz sınıfı belirtmeniz istenir. Cihaz sınıfı, cihazınızı en iyi şekilde temsil eden form faktörüne veya sınıflandırmasına başvurur.

- **Ağ geçidi**

    IoT ağı üzerinden gönderilen verileri işleyen bir cihaz.

- **Algılayıcısı**

    Bir ortamdaki değişiklikleri algılayan ve yanıtlayan ve değişiklikleri işlemek için ağ geçitlerine bağlanan bir cihaz.

- **Diğer**

    Diğer ' i seçerseniz, kendi kelimelerinizle cihaz sınıfınız için bir açıklama ekleyin. Zaman içinde, özellikle iş ortaklarımızın geri bildirimini izlemeye devam ediyoruz gibi bu listeye yeni değerler eklemeye devam edebiliriz.

## <a name="device-type"></a>Cihaz Türü

Sertifika işlemi sırasında iki cihaz türünden birini de seçmeniz istenir.

- **Tamamlanmış ürün**

    Çözüm için hazırlık ve üretim dağıtımı için hazırlanma bir cihaz. Genellikle bellenim ve işletim sistemi ile tamamlanmış bir form faktörü içinde. Bunlar, kullanım için değişiklik gerektirmeyen ek özelleştirmeler veya özel cihazlar gerektiren genel amaçlı cihazlardır.
- **Çözüm-Ready Dev Kit**

    Genellikle tamamlanmış form faktöründe değil, kolayca prototip yazma için ideal donanım ve yazılım içeren bir geliştirme seti. Genellikle hızlı prototipleme özelliğini etkinleştirmek için örnek kod ve öğreticiler içerir.

## <a name="component-type"></a>Bileşen türü

Cihaz ayrıntıları bölümünde, bileşen türüne göre bileşenleri listeleyerek cihazınızı açıklayacaksınız. [Burada](./how-to-using-the-components-feature.md)bileşenler hakkında daha fazla rehberlik görüntüleyebilirsiniz.

- **Müşteriye Ready ürünü**

    Genel veya birincil cihazın bileşen temsili. Bu, daha fazla geliştirme yapmadan cihazın bir sınıflandırması olan **tamamlanmış ürünlerden** farklıdır. Tamamlanmış bir ürün, müşterinin hazırlamış olduğu bir ürün bileşeni içerir.
- **Geliştirme panosu**

    Kolay özelleştirme için mikro işlemciye sahip tümleşik veya çıkarılabilir bir Pano.
- **Çevre birimi**

    Ürüne tümleşik veya çıkarılabilir bir ekleme (örneğin, aksesuar). Bunlar genellikle ana cihaza bağlanan, ancak cihaz birincil işlevlerine katkıda bulunmayan cihazlardır. Bunun yerine, ek işlevler sağlar. Bellek, RAM, depolama, sabit diskler ve CPU 'Lar çevresel cihazlar olarak kabul edilmez (Bunun yerine, müşterinin hazırlandığı ürün bileşeninin ek özellikleri altında listelenmelidir).
- **Modül üzerinde sistem**  

    Bir sistem işlevini tek bir modülde tümleştiren Pano düzeyinde devre.

## <a name="component-attachment-method"></a>Bileşen eki yöntemi

Bileşen eki yöntemi, müşteriye bileşenin genel ürünle nasıl tümleştirildiği hakkında bilgi veren başka bir bileşen ayrıntısı.

- **Tümleşik**
 
    Bir cihaz bileşeni ürünün ana kasadığı zaman bir cihaz bileşeni olduğunda başvurur. Bu en yaygın olarak cihazdan kaldırılamayan bir çevresel bileşen türü anlamına gelir.  
    Örnek: bir ağ geçidi Kasası içinde tümleşik bir sıcaklık algılayıcısı.

- **Arasında**

    Bir bileşenin ürünün ana **kasadığı** ne zaman bir parçası olmadığı anlamına gelir.  
    Örnek: cihaza bağlanması gereken bir dış sıcaklık algılayıcısı.


## <a name="next-steps"></a>Sonraki adımlar

Bu sözlük, portala projenizde sertifika sertifikalarken size kılavuzluk eder. Şimdi projenize başlamaya hazırsınız!
- [Öğretici: projenizi oluşturma](./tutorial-01-creating-your-project.md)
