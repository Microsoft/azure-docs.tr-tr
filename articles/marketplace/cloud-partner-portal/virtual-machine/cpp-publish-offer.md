---
title: Azure Marketi 'nde bir sanal makine teklifi yayımlama
description: Mevcut bir sanal makine teklifini Azure Marketi üzerine yayımlamak için gereken adımları listeler.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 08/17/2018
ms.author: pabutler
ms.openlocfilehash: 1b07f3f3edab47f8f75835dffd4cc3f89f17ab63
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824404"
---
# <a name="publish-a-virtual-machine-offer"></a>Bir sanal makine teklifi yayımlama

 Son adım, portalda teklif tanımladıktan ve ilişkili teknik varlıkları oluşturduktan sonra, teklifi yayımlama için gönderirsiniz. Aşağıdaki diyagramda yayımlama işlemindeki "canlı çalış" ile ana adımlar gösterilmektedir:

![Sanal Makine teklifi için yayımlama adımları](./media/publishvm_013.png)

Aşağıdaki tabloda bu adımlar açıklanmakta ve tamamlanması için en fazla bir süre tahmini sunulmaktadır:
<!-- we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Yayımlama adımı**           | **Işınızda**    | **Açıklama**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Önkoşulları doğrulama         | 15 dk   | Teklif bilgileri ve teklif ayarları onaylanır.                        |
| Sınama sürücüsü doğrulaması (isteğe bağlı) | 2 saat | Test sürücüsünü etkinleştirmeyi seçtiyseniz, Microsoft test sürücüsü yapılandırmasını, dağıtımını ve çoğaltmasını seçili bölgeler aracılığıyla doğrular. |
| Sertifika                  | 3 gün | Teklif, Azure Sertifika ekibi tarafından çözümlenir. Bu adım, virüsler, kötü amaçlı yazılım, güvenlik uyumluluğu ve güvenlik sorunları için taramalar gerçekleştirir. Sorun bulunursa geri bildirim sağlanır. |
| Sağlama                   | 4 gün   | VM teklifi market üretim sistemlerinde çoğaltılır.               |
| Paketleme ve lider oluşturma kaydı | \< 1 saat  | Teklifin teknik varlıkları müşteri kullanımı için paketlenmiştir ve müşteri adayı sistemleri yapılandırılır ve ayarlanır. |
|  Yayımcı oturumu kapatma             |  -        | Teklifin etkin olmadan önce son yayımcı incelemesi ve onayı. Teklifinizi, tüm gereksinimlerinizi karşıladığından emin olmak için seçili aboneliklerde (teklif bilgileri adımlarında) dağıtabilirsiniz.  |
| Sağlama                   | 4 gün | Son VM teklifi market üretim sistemleri ve bölgelerinde çoğaltılır. | 
| Canlı                           | 4 gün | VM teklifi serbest bırakılır, gerekli bölgelere çoğaltılır ve herkese açık hale getirilir. |
|  |  |

Bu işlemin tamamlanabilmesi için 16 güne kadar izin verin.  Bu yayımlama adımlarını tamamladıktan sonra, sanal makine teklifiniz [Microsoft Azure Market](https://azuremarketplace.microsoft.com/marketplace/)listelenecektir. 

