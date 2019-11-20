---
title: Azure Application teklifini yayımlama | Azure Marketi
description: Azure Market 'te Azure uygulaması teklifi yayımlama işlemini ve adımlarını açıklar.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: pabutler
ms.openlocfilehash: 0005760a16f5109ca3555df5c5c5137facc84c40
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826154"
---
# <a name="publish-azure-application-offer"></a>Azure uygulama teklifini yayımlama

**Yeni teklif** sayfasında bilgileri sağlayarak bir teklif oluşturduktan sonra teklifi yayımlayabilirsiniz. Yayımlama işlemini başlatmak için **Yayımla** ' yı seçin.

Aşağıdaki diyagramda "canlı çalış" teklifinin bir teklifiyle ilgili yayımlama işlemindeki ana adımlar gösterilmektedir.

![Teklif yayımlama adımları](./media/offer-publishing-steps.png)


## <a name="detailed-description-of-publishing-steps"></a>Yayımlama adımlarının ayrıntılı açıklaması

Aşağıdaki tabloda, her bir yayımlama adımı listelenmektedir ve açıklanmaktadır ve her adımın tamamlanışında bir süre tahmini sağlanır.  "Gün" cinsinden tahminler, hafta sonlarını ve tatilleri dışarıda bırakmak için iş günü olarak tanımlanmıştır.

|  **Yayımlama adımı**           | **Işınızda**    | **Açıklama**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Önkoşulları doğrulama         | < 15 dk    | Teklif bilgileri ve teklif ayarları onaylanır.                        |
| Etkileyen gelir ayarlarını doğrulama | < 15 dk  | Teklif için Azure Kaynak kullanımı atısyonu denetlenir.             |
| Sertifika                  | < 1 gün     | Teklif, Azure Sertifika ekibi tarafından çözümlenir. Teklif, virüsler, kötü amaçlı yazılım, güvenlik uyumluluğu ve güvenlik sorunları için taranır. Teklif, tüm uygunluk ölçütlerini karşılayıp karşılamadığını görmek üzere denetlenir. Daha fazla bilgi için bkz. [Önkoşullar](./cpp-prerequisites.md). Sorun bulunursa geri bildirim sağlanır. |
| Sınama sürücüsü doğrulaması          | < 2 saat   | Seçim Bir test sürücüsü varsa, Microsoft, dağıtılabileceğini ve çoğaltılamadığını doğrular.  |
| Paketleme ve lider oluşturma kaydı | 1 saatten az  | Teklifin teknik varlıkları müşteri kullanımı için paketlenmiştir ve lider sistemleri yapılandırılır ve dağıtılır. |
|  Yayımcı oturumu kapatma             |  El ile    | Teklifin etkin olmadan önce son yayımcı incelemesi ve onayı. Teklif artık önizleme için kullanılabilir.  Teklifinizi, tüm gereksinimlerinizi karşıladığından emin olmak için seçili aboneliklerde (teklif bilgileri adımlarında) dağıtabilirsiniz.  Teklifi doğruladıktan sonra teklifinizin bir sonraki adıma gidebilmesi için **canlı git** ' i seçin. |
| Microsoft incelemesi                | 7-14 gün | Microsoft holistik, Azure uygulamanızı gözden geçirir ve sorunlar keşfedildiğinde size e-posta ile gönderir.  Bu adımın uzunluğu, uygulamanın karmaşıklığına, kapsanmamış sorunların ve bunlara ne kadar süre yanıt verdiğinize bağlıdır.  |
| Canlı                           | < 1 gün | Teklif serbest bırakılır, belirtilen bölgelere çoğaltılır ve herkese açık hale getirilir. |
|   |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|   |

Yayımlama işlemini Bulut İş Ortağı Portalı teklifinizin **durum** sekmesinde izleyebilirsiniz.

![Bir Azure uygulaması teklifinin durum sekmesi](./media/offer-status-tab.png)

Yayımlama sürecini tamamladıktan sonra teklifiniz [Microsoft Azure Market uygulama kategorisinde](https://azuremarketplace.microsoft.com/marketplace/apps/)listelenir.

>[!Note]
>Bulut çözümü sağlayıcıları (CSP) iş ortağı kanalı kabul etme artık kullanılabilir.  Teklifinizi Microsoft CSP iş ortağı kanalları aracılığıyla pazarlama hakkında daha fazla bilgi için lütfen bkz. [bulut çözümü sağlayıcıları](../../cloud-solution-providers.md) .

## <a name="errors-and-review-feedback"></a>Hatalar ve gözden geçirme geri bildirimi

Teklifinizin Yayımlanma durumunu görüntülemenin yanı sıra, **durum** sekmesi de bir sorunla karşılaşılması gereken tüm yayımlama adımlarında hata iletilerini ve geri bildirimleri görüntüler.  Sorun kritik ise yayımlama iptal edilir.  Ardından, bildirilen sorunları düzeltmeniz ve teklifi yeniden yayımlamanız gerekir.  **Microsoft gözden geçirme** adımı teklifinizin ve ilişkili teknik varlıklarının (özellikle de Azure Resource Manager şablonu) kapsamlı bir incelemesini temsil ettiğinden, sorunlar genellikle çekme ISTEğI (PR) bağlantıları olarak sunulur.  Bu PR 'ler nasıl görüntüleneceği ve yanıtlanmasına ilişkin bir açıklama, bkz. [İnceleme geri bildirimini işleme](./cpp-handling-review-feedback.md).


## <a name="next-steps"></a>Sonraki adımlar

Yayımlama adımlarında bir veya daha fazla hatayla karşılaşırsanız, bunları düzeltmeniz ve teklifinizi yeniden yayımlamanız gerekir.  **Microsoft gözden geçirme** adımında kritik sorunlarla karşılaşılırsa, Microsoft gözden geçirme ekibinin Azure DevOps deposuna erişerek [Gözden geçirme geri bildirimini işlemeniz](./cpp-handling-review-feedback.md) gerekir.

Azure uygulaması başarıyla yayımlandıktan sonra, değişen iş veya teknik gereksinimleri yansıtmak için [Mevcut teklifi güncelleştirebilirsiniz](./cpp-update-existing-offer.md) . 
