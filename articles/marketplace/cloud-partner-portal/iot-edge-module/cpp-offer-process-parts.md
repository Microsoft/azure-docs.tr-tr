---
title: Azure IoT Edge modül teklifi yayımlamaya genel bakış | Azure Marketi
description: Azure Marketi 'nde IoT Edge modülü teklifini yayımlama sürecine genel bakış.
services: Azure, Marketplace, Cloud Partner Portal
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: pabutler
ms.openlocfilehash: 97df9a61d15e0d90e81f42cef327aea23873ffa0
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73814334"
---
# <a name="iot-edge-module-offer-publishing-overview"></a>IoT Edge modül teklifi yayımlamaya genel bakış

<table> <tr> <td>Bu bölümde, Microsoft <a href="https://azuremarketplace.microsoft.com">Azure Marketi</a>'ne yeni bir Azure IoT Edge modülü teklifinin nasıl yayımlanacağı açıklanmaktadır. IoT Edge modülü, bir IoT Edge cihazında çalıştırılmak üzere yapılan Docker ile uyumlu bir kapsayıcıdır. Azure IoT Edge modüller, IoT Edge tarafından yönetilen en küçük hesaplama birimidir ve Azure hizmetleri veya özel çözüm kodu içerebilir. </td> <td><img src="./media/iotedge-icon1.png"  alt="Azure IoT Edge module icon" /></td> </tr> </table>

## <a name="publishing-process"></a>Yayımlama işlemi

IoT Edge modülü teklifinin yayımlanması için üst düzey adımlar şunlardır:

1. Teklifi oluşturma<br> Teklif hakkında ayrıntılı bilgi sağlayın. Bu bilgiler şunları içerir: teklif açıklaması, pazarlama malzemeleri, destek bilgileri ve varlık belirtimleri.

2. İşletme ve teknik varlıkları oluşturma<br> İlişkili çözüme yönelik iş varlıklarını (yasal belgeler ve pazarlama malzemeleri) ve teknik varlıkları (bir Azure Container Registry barındırılan IoT Edge modül görüntüleri) oluşturun.

3. SKU 'YU oluşturma<br> Teklifle ilişkili SKU 'ları oluşturun. Yayımlamayı planladığınız her görüntü için benzersiz bir SKU gereklidir.

4. Teklifi onaylama ve yayımlama <br>Teklif ve teknik varlıklar tamamlandıktan sonra teklifi gönderebilirsiniz. Bu gönderim, yayımlama işlemini başlatır. Bu işlem sırasında çözüm test edildi, onaylanır, onaylandı ve sonra Azure Marketi 'nde "canlı gidiyor".

## <a name="parts-of-an-offer"></a>Teklifin parçaları

Aşağıdaki makaleler bir IoT Edge modülü teklifinin temel kısımlarını kapsar.

- [Önkoşullar](./cpp-prerequisites.md) <br>Bu makalede, IoT Edge bir modül teklifi oluşturabilmeniz veya yayımlayabilmeniz için teknik ve iş gereksinimleri listelenmektedir.
- [IoT Edge modülü teknik varlıklarını hazırlayın](./cpp-create-technical-assets.md) <br>Bu makalede, teknik varlıkların bir IoT Edge modülü için nasıl hazırlanacağı açıklanmaktadır. Bu varlıkların, IoT Edge modülünün Azure Marketi 'nde yayımlanabilmesi için gereken tüm teknik kriterleri karşılaması gerekir.
- [IoT Edge modülü teklifi oluşturma](./cpp-create-offer.md) <br>Bu makalede, [bulut iş ortağı portalı](https://cloudpartner.azure.com)kullanarak yeni bir IoT Edge modülü teklif girişi oluşturmak için gereken adımlar listelenmektedir.
- [IoT Edge modülü teklifi yayınlama](./cpp-publish-offer.md)<br> Bu makalede, teklifi Azure Marketi 'nde yayımlama için nasıl göndereceğiniz açıklanır.

## <a name="next-steps"></a>Sonraki adımlar

Microsoft Azure Market IoT Edge modülünü yayımlamaya yönelik [Teknik ve iş gereksinimlerini](./cpp-prerequisites.md) gözden geçirin.
