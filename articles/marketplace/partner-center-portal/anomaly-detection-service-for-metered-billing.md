---
title: Ölçülen faturalandırma için anomali algılama hizmeti-Microsoft Azure Market
description: Anomali algılamanın nasıl çalıştığını, bildirimlerin ne zaman gönderileceğini ve bunlarla ne yapılacağını açıklar ve destek seçenekleri.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.author: mingshen
author: mingshen-ms
ms.date: 06/10/2020
ms.openlocfilehash: ac3e07c67ca82c2960de1c4341a714b33751bfc1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101092409"
---
# <a name="anomaly-detection-service-for-metered-billing"></a>Tarifeli faturalama için anomali algılama hizmeti

[Market ölçüm hizmeti](marketplace-metering-service-apis-faq.md) , ticari Market programında standart olmayan birimlere göre ücretlendirilen teklifler oluşturmanıza imkan tanır. Tarifeli faturalandırma sayesinde, müşterinizin kullanımı için kullanım olaylarını Microsoft 'a gönderirsiniz ve bu kullanımı temel alarak faturalandırmayı hazırlarız.

Hatalı kullanım verileri, hata, tüketim izlemeinizdeki yanlış yapılandırma veya sahtekarlık gibi çeşitli nedenlerden gelebilir. Hatalı kullanım verileri, müşteri ücretleri ve faturalandırma anlaşmazlıklarına neden olur.

Anomali algılama hizmeti, riski azaltmak için, normal ölçülen faturalandırma davranışını saptamak, ölçülen faturalandırma kullanımını çözümlemek ve en az Kullanıcı müdahalesiyle bozukluklar bulmalarına yönelik makine öğrenimi algoritmalarını uygular.

Ölçümlü faturalandırma kullanımınız için bir anomali algılandığında bilgilendirilirsiniz. Bu, bir anomali gerçek bir sorun olup olmadığını araştırırken bize bildirimde bulunur ve bu durum, müşteri faturalandırma sorununa karşı bir süre önce ele alınması gereken noktada, bu durum üzerinde işlem yapılabilir.

Ölçülen faturalandırmalar, DIB 'ler ve tarifeli faturalama kullanımının eğilimi değişikliklerine ek olarak modelimiz de mevsimsel etkilere yönelik hesaplardır. Ölçülen faturalandırma fazla kullanım verileri aracılığıyla iletileceği için, modelimiz eksik verileri uzun süreler da düzgün bir şekilde işleyebilir.

Aşağıda anomali algılama sonuçlarının örnekleri verilmiştir. Beklenen Aralık, sarı bir bant olarak gösterilir. Kabul edilebilir tarifeli faturalandırma kullanımı, bantta yeşil yıldız olarak gösterilir. Bant dışındaki faturalandırma kullanımı kırmızı nokta olarak görüntülenir.  

Öngörülebilir bir eğilim dışında algılanan bozukluklar:

![Öngörülebilir bir eğilim dışında algılanan bozukluklar gösterilmektedir.](media/anomaly-1.png)

Yinelenen bir döngüsel eğilim dışında algılanan bozukluklar:

![Yinelenen bir döngüsel eğilim dışında algılanan bozukluklar gösterilmektedir.](media/anomaly-2.png)

Yukarı doğru bir eğilim halinde algılanan bozukluklar:

![, Yukarı doğru bir eğilime göre algılanan bozukluklar gösterilmektedir.](media/anomaly-3.png)

## <a name="how-anomaly-detection-service-works"></a>Anomali algılama hizmeti nasıl kullanılır

Anomali algılama, ölçülen tüm faturalandırma kullanımı için otomatik olarak etkinleştirilir. Kullanım olaylarını Microsoft 'a gönderdiğinizde anomali algılama hizmeti, geçmiş kullanım verilerine göre beklenen değerler modeli oluşturur. Bu model haftalık olarak çalışır.

Anomali algılama işlevleri, ölçüm ve müşteri başına düzeyindedir. Bu, her müşteriye ait her bir ölçerin bu ölçerin bu müşterinin son kullanım düzenine göre eğitilen bir modele sahip olacağı anlamına gelir.

Model, geriye dönük güven aralıkları oluşturarak işe yarar. Zaman serisi tahmini, bir eğilim tahmin parçasını ve mevsimsellik parçasını içeren genelleştirilmiş bir eklenebilir modeldir. Model bir gerileme görevi olarak formüllenmiş olduğundan, uzun süreler eksik verileri düzgün bir şekilde işleyebilir. Bir gözlemlik, tahmin edilen güven aralıklarının dışında kalırsa, gözlemlenen faturalandırmayla ilgili geçmiş desenlerine göre açıklanamayan ve bu nedenle bir anomali olabilir.

## <a name="anomaly-detection-notification"></a>Anomali algılama bildirimi

Iş Ortağı Merkezi 'nde aykırları değerlendirebilir, yönetebilir ve kabul edebilirsiniz. Nasıl yapılacağını öğrenmek için bkz. [Iş Ortağı Merkezi 'nde tarifeli faturalandırma Bozuklularını yönetme](../anomaly-detection.md).

Müşterilerinizin tarifeli kullanım için fazla ücretlendirilmediğinden emin olmak için, algılanan anomali sorunlar olup olmadığını araştırmanız gerekir. Bu durumda, Iş Ortağı Merkezi 'nde yanlış kullanımı kabul edebilirsiniz.

Algılanan anormalilerin normal kullanım olup olmadığını onaylamanız önerilir. Bunu yaptığınızda size sağladığımız anomali verileri iyileştirecaksınız. Anomali potansiyel olarak yüksek bir mali riski gösteriyorsa, kullanımı onaylamak için sizinle iletişim kurabiliriz.

## <a name="when-and-how-to-get-support"></a>Ne zaman ve nasıl destek alınır?

Müşteriye daha fazla ücret gönderilmesini sağlayan yanlış bir tarifeli kullanım yaptıysanız, müşteri tarafından bildirilen kullanım için bir fatura başlatmaz veya size bu kullanım için ödeme yapmayacaktır. Eksik raporlamadan kaynaklanan gelir kaybına katlanmanız gerekir.

Aşağıdaki durumlardan biri geçerliyse, Iş ortağı merkezindeki kullanım miktarını, müşterileriniz için bir para iadesi veya faturalama ayarlamasına neden olacak şekilde ayarlayabilirsiniz:

- Bulduğumuz anormalilerin gerçek bir sorun olduğunu doğrulamıştır ve yanlış kullanım müşterinin sayısını açığa atmıştır.
- Bizim için yanlış kullanım gönderdik ve yanlış kullanım müşterinin borçlandırılmasını sağlayabilir.

Tarifeli faturalandırma anomali ile ilgili bir destek bileti göndermek için:

1. İş hesabınızla iş [Ortağı Merkezi](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) ' nde oturum açın.
1. Sayfanın sağ üst kısmındaki menüde, **destek** simgesini seçin. **Yardım ve destek** bölmesi sayfanın sağ tarafında görünür.
1. Ticari Market hakkında yardım almak için **ticari Market**' i seçin.
   ![Destek bölmesini gösterir.](../media/support/commercial-marketplace-support-pane.png)
1. **Sorun Özeti** kutusunda, **ticari Market > tarifeli faturalandırma** girin.
1. **Sorun türü** kutusunda aşağıdakilerden birini seçin:
    - **Ticari Market > tarifeli faturalandırma > Azure uygulamaları teklifi için yanlış kullanım gönderildi**
    - **Ticari Market >, SaaS teklifi için yanlış kullanım > ölçülen faturalandırmaya gönderildi**
1. **Sonraki adımda** **çözümleri gözden geçir**' i seçin.
1. Önerilen belgeleri gözden geçirin, varsa veya bir destek bileti göndermek için **Sorun ayrıntıları sağla** ' yı seçin.

Daha fazla yayımcı desteği seçeneği için bkz. [Iş Ortağı Merkezi 'nde ticari Market programı desteği](../support.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Market ölçüm hizmeti API 'si](marketplace-metering-service-apis.md)hakkında bilgi edinin.
- [Iş Ortağı Merkezi 'nde ölçülen faturalandırma bozuklularını yönetme](../anomaly-detection.md)
