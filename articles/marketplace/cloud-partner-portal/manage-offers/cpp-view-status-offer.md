---
title: Market tekliflerinin durumunu görüntüleyin | Azure Marketi
description: Bulut İş Ortağı Portalı kullanarak Azure ve AppSource marketlerine yönelik tekliflerin durumunu görüntüleyin
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: pabutler
ms.openlocfilehash: e58670d37e01bb6e453b73e42a87e42e890d10d8
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826695"
---
# <a name="view-the-publishing-status-of-azure-marketplace-and-appsource-offers"></a>Azure Marketi ve AppSource tekliflerinin yayımlama durumunu görüntüleyin

Bir teklif oluşturduktan ve özellikle yayımlama sürecinde, Bulut İş Ortağı Portalı teklifinizin durumunu görüntüleyebilirsiniz.  Genel yayımlama durumu, portalın [**tüm teklifleri**](../portal-tour/cpp-all-offers-page.md) ve [**onay**](../portal-tour/cpp-approvals-page.md) sayfalarında kullanılabilir.  Her teklif için aşağıdaki durum göstergelerden biri görüntülenmelidir.  

|            Durum              |   Açıklama                                                           |
|            ------              |   -----------                                                           |
| **-**                          | Teklif oluşturuldu ancak yayımlama işlemi başlatılmamış.            |
| **Yayımlama devam ediyor**        | Teklif, yayımlama sürecinin adımları aracılığıyla kendi yolunda çalışmaktadır.   |
| **Yayımlama başarısız oldu**             | Microsoft tarafından doğrulama veya İnceleme sırasında kritik bir sorun bulundu. |
| **Yayımlama iptal edildi**           | Yayımcı, teklif yayımlama işlemini iptal etti.  Bu durum Market 'te mevcut bir teklifi listelemez. | 
| **Yayımcının oturumu kapatma bekleniyor** | Teklif Microsoft tarafından incelendi ve şimdi yayımcı tarafından nihai bir doğrulamayı bekler. |
| **Listede kaldırma**                   | Market 'te daha önce yayınlanmış bir teklif kaldırılmıştır.      | 
|  |  |


## <a name="publishing-status-details"></a>Yayımlama durumu ayrıntıları 

**Yeni teklif** sayfasının **durum** sekmesinde yayımlama sürecinde geçen bir teklifin durumu hakkında daha fazla ayrıntı bulunur.  Bu sayfada, bu teklif türü için tüm yayımlama adımları listelenir.  *Numara ve belirli adımların, teklif türleri arasında genellikle farklı olduğunu unutmayın.*  Bu sayfada, Microsoft doğrulama ve gözden geçirme adımları tarafından oluşturulan tüm bekleyen sorunlar da belirtilir ve bu, yayımlama işleminin devam edebilmesi için genellikle yayımcı tarafından eylem gerektirir.  Örneğin, aşağıdaki görüntüde yeni bir sanal makine teklifinin **durum** sekmesi gösterilmektedir. 

![VM teklifinin durum sekmesi](./media/vm-offer-pub-steps1.png)

Danışmanlık hizmeti için bir sonraki örnek **durum** sekmesi, müşteri adayı yönetimi ayarlarında bildirilen bir hatayı gösterir.  Danışmanlık Hizmetleri için lider yönetimi gerekli olduğundan, yayımlamadan önce bu hata düzeltilmelidir.

![Danışmanlık hizmeti 'nin hata gösteren durum sekmesi](./media/consulting-service-error.png)

Bir Azure uygulamasının son örnek durumu kritik bir Microsoft gözden geçirme sorunu gösterir.  Bu İnceleme sorunu hakkında ayrıntılı bilgi içeren Azure DevOps öğesinin etkin bir bağlantısını içerir.  Daha fazla bilgi için bkz. [Azure Uygulama teklifini yayımlama](cpp-publish-offer.md).

![İnceleme sorununu gösteren Azure uygulaması durum sekmesi](../azure-applications/media/status-tab-ms-review.png)


## <a name="next-steps"></a>Sonraki adımlar

Bekleyen sorunları düzeltmek veya teklif ayarlarını güncelleştirmek için [bir teklifi güncelleştirmeniz](./cpp-update-offer.md)gerekir. 
