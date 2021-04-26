---
title: Anomali algılayıcı multivariate API 'sini kullanmaya yönelik tahmine dayalı bakım mimarisi
titleSuffix: Azure Cognitive Services
description: Anomali algılayıcısının kullanımı için başvuru mimarisi, tahmine dayalı bakım için zaman serisi verilerinize anomali algılama uygular.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: anomali algılama, makine öğrenimi, algoritmalar
ms.openlocfilehash: 4d379cfe01dcbd88531b98a32b45e0b30f6adeef
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315692"
---
# <a name="predictive-maintenance-solution-with-anomaly-detector-multivariate"></a>Anomali algılayıcısının çok sayıda kullanımı ile tahmine dayalı bakım çözümü

Birçok farklı sektör, riskleri azaltmak ve cihazlarından verileri işlemeye yönelik eyleme dönüştürülebilir Öngörüler kazanmak için tahmine dayalı bakım çözümleri gerektirir. Tahmine dayalı bakım, çevrimiçi izleme gerçekleştirerek ekipman koşulunu değerlendirir. Amaç, ekipmanın düşmediği veya bölünemediği bir şekilde bakım gerçekleştirmektir.

Ekipman içindeki her bir bileşen, örneğin titreşim, yön ve döndürme gibi düzinelerce sinyal üretebildikleri için donanımların sistem durumunu izlemek zor olabilir.  Bu sinyaller örtük bir ilişkiye sahip olduğunda ve izlenmesi ve çözümlenmesi gerektiğinde daha da karmaşık olabilir. Bu sinyaller için farklı kurallar tanımlama ve bunların birbirleriyle el ile ilişkilendirilmesi maliyetli olabilir. Anomali algılayıcısının çok değişen özelliği şunları sağlar:

* Birlikte izlenecek birden çok bağıntılı sinyal ve bunlar arasındaki şirketler arasındaki ilişkiler modelde hesaba katılmaz.
* Yakalanan her bir anomali içinde, farklı sinyallerin katkı derecesi anomali açıklama ve olay kök nedeni analizine yardımcı olabilir.
* Multivarianomali algılama modeli, denetimli bir şekilde oluşturulmuştur. Modeller, farklı ekipman türleri için özel olarak eğitilmiş olabilir.

Burada, anomali algılayıcısının çok sayıda olması temelinde tahmine dayalı bakım çözümü için bir başvuru mimarisi sağlıyoruz.

## <a name="reference-architecture"></a>Başvuru mimarisi

[![Bir endüstriyel ekipman parçası ile kenarda toplanan algılayıcı verilerinde başlayan mimari diyagram, anomali algılayıcısı çalıştırıldıktan sonra oluşturulan bir olay uyarısının son çıktısına işleme/analiz işlem hattını izler. ](../media/multivariate-architecture/multivariate-architecture.png)](../media/multivariate-architecture/multivariate-architecture.png#lightbox)

Yukarıdaki mimaride, algılayıcı verilerinden gelen akış olayları Azure Data Lake depolanır ve sonra bir zaman serisi biçimine dönüştürülecek bir veri dönüştürme modülü tarafından işlenir. Bu arada, akış olayı eğitilen modelle gerçek zamanlı algılamayı tetikler. Genel olarak, bu mimarideki *köprü hizmeti* gibi çok sayıda model yaşam döngüsünü yönetmek için bir modül olacaktır.

**Model eğitimi**: bir bileşen veya ekipmanın bozuklulığını saptamak için anomali algılayıcı çok değişkenli 'i kullanmadan önce. Bu varlık tarafından oluşturulan belirli sinyallere (zaman serisi) bir model eğmemiz gerekiyor. *Köprü hizmeti* , geçmiş verileri getirecek ve anomali algılayıcısı 'na bir eğitim işi gönderecek ve model kimliğini *model meta* depolamada tutacaktır.

**Model doğrulaması**: belirli bir modelin eğitim süresi eğitim verileri hacmine göre değiştirilebilir. *Köprü hizmeti* , model durumunu ve tanılama bilgilerini düzenli olarak sorgulayabilir. Model kalitesini doğrulamak, çevrimiçi geçirmeden önce gerekli olabilir. Senaryoda Etiketler varsa, bu Etiketler Model kalitesini doğrulamak için kullanılabilir. Aksi takdirde, model kalitesini değerlendirmek için tanılama bilgileri kullanılabilir ve ayrıca eğitilen modelle birlikte geçmiş verilerde algılama gerçekleştirebilir ve bu da modelin geçerliliğini geri test etmek için sonucu değerlendirebilirsiniz.

**Model çıkarımı**: çevrimiçi algılama geçerli modelle gerçekleştirilecek ve sonuç kimliği *çıkarım tablosunda* depolanabilir. Eğitim süreci ve çıkarım işlemi zaman uyumsuz bir şekilde yapılır. Genel olarak, bir algılama görevi Saniyeler içinde tamamlanabilir. Algılama için kullanılan sinyaller, eğitim için kullanılmış olanlarla aynı olmalıdır. Örneğin, eğitim için titreşimi, yönlendirmeyi ve döndürmeyi kullanacaksanız, algılama bölümünde üç sinyal giriş olarak eklenmelidir.

**Olay uyarısı** Algılama sonuçları, sonuç kimlikleriyle sorgulanabilir. Her sonuç, her bir anomali önem derecesine ve katkı derecesine sahiptir. Katkı derecesi, bu anomali neden olduğunu ve hangi sinyalin bu olaya neden olduğunu anlamak için kullanılabilir. Bakım işlerini yapmak için alan mühendislerine gönderilecek uyarılar ve bildirimler oluşturmak için önem derecesine göre farklı eşikler ayarlanabilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Hızlı başlangıç](../quickstarts/client-libraries-multivariate.md).
- [En Iyi uygulamalar](../concepts/best-practices-multivariate.md): Bu makale, çok değişkenli API 'ler ile kullanmak için önerilen desenlerle ilgilidir.