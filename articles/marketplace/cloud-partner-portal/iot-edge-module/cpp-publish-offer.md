---
title: Azure IoT Edge modül teklifini Yayımla | Azure Marketi
description: IoT Edge modül teklifini yayımlama.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pabutler
ms.openlocfilehash: 2310d7a7cad16009bbb58469190a77eedb0619f8
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73813827"
---
# <a name="publish-iot-edge-module-offer"></a>IoT Edge modülü teklifini yayımlama

 **Yeni teklif** sayfasında bilgileri sağlayarak yeni bir teklif oluşturduktan sonra teklifi yayımlayabilirsiniz. Yayımlama işlemini başlatmak için **Yayımla** ' yı seçin.

Aşağıdaki diyagramda "canlı çalış" teklifinin bir teklifiyle ilgili yayımlama işlemindeki ana adımlar gösterilmektedir.

![IoT Edge modülü teklifi için yayımlama adımları](./media/iot-edge-module-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>Yayımlama adımlarının ayrıntılı açıklaması

Aşağıdaki tabloda her bir adımı tamamlaması için bir süre Tahmini (maksimum) ile birlikte her bir yayımlama adımı açıklanmaktadır.
<!-- P2: we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Yayımlama adımı**           | **Işınızda**    | **Açıklama**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Önkoşulları doğrulama         | 15 dk   | Teklif bilgileri ve teklif ayarları onaylanır.                        |
| Sertifika                  | 2 hafta | Teklif, Azure Sertifika ekibi tarafından çözümlenir. Bu adım, virüsler, kötü amaçlı yazılım, güvenlik uyumluluğu ve güvenlik sorunları için taramalar gerçekleştirir. Ayrıca, bu IoT Edge modülü teklifinin tüm uygunluk ölçütlerini karşıladığını doğrular (bkz. [Önkoşullar](./cpp-prerequisites.md) ve [Teknik varlıklarınızı hazırlama](./cpp-create-technical-assets.md)). Sorun bulunursa geri bildirim sağlanır. |
| Paketleme | 1 saat  | Teklifin teknik varlıkları müşteri kullanımı için paketlenmiştir ve müşteri adayı sistemleri yapılandırılır ve ayarlanır. |
|  Yayımcının oturumu Kapat             |  -        | Teklifin etkin olmadan önce son yayımcı incelemesi ve onayı. Teklifinizi, tüm gereksinimlerinizi karşıladığından emin olmak için seçili aboneliklerde (teklif bilgileri adımlarında) dağıtabilirsiniz.  Teklifinizin bir sonraki adıma gidebilmesi için **canlı git** ' i seçin. |
| Paketleme                 | 1 saat | Son teklif, market üretim sistemleri ve bölgelerinde çoğaltılır. | 
| Canlı                           | 4 gün |Teklif serbest bırakılır, gerekli bölgelere çoğaltılır ve herkese açık hale getirilir. |

Yayımlama işleminin tamamlanabilmesi için 10 adede kadar iş gününe izin verin ve teklif serbest bırakılır. Yayımlama işlemini tamamladıktan sonra, IoT Edge modülü teklifiniz [Microsoft Azure Market](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)listelenecektir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Marketi 'nde mevcut bir IoT Edge modülü teklifini güncelleştirme](./cpp-update-existing-offer.md)
