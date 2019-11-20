---
title: Azure IoT Edge modüller
description: IoT Edge modülü, uygulama ve hizmet yayımcıları için Azure Marketi 'nde sunulur.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, IoT Edge module offer
author: qianw211
manager: pabutler
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 09/22/2018
ms.author: pabutler
ms.openlocfilehash: db501c34a4418bc7b08b19105a9b28585288f877
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823060"
---
# <a name="iot-edge-modules"></a>IoT Edge modülleri

[Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) platformu Azure bulutu tarafından desteklenmektedir.  Bu platform, kullanıcıların doğrudan IoT cihazlarında çalışacak bulut iş yüklerini dağıtmasına olanak sağlar.  IoT Edge bir modül çevrimdışı iş yüklerini çalıştırabilir ve veri analizini yerel olarak gerçekleştirebilir. Bu teklif türü, bant genişliğini kaydetmeye, yerel ve hassas verilerin korunmasına yardımcı olur ve düşük Gecikmeli yanıt süresi sağlar.  Artık önceden oluşturulmuş bu iş yüklerinden faydalanmak için seçenekleriniz vardır. Bu aşamada, Microsoft 'tan yalnızca birkaç birinci taraf çözüm kullanıma sunulmuştur.  Kendi özel IoT çözümlerinizi oluşturmak için zaman ve kaynakları yatırım yapmanız gerekiyordu.

[Azure Marketi 'ndeki IoT Edge modüllerine](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1)giriş yaparak artık yayımcıların IoT hedef kitlesi için çözümlerini kullanıma sunma ve satma amacıyla tek bir hedef sunuyoruz. IoT geliştiricileri, son olarak çözüm geliştirmeyi hızlandırmaya yönelik yetenekler bulabilir ve satın alabilir.  

## <a name="key-benefits-of-iot-edge-modules-in-azure-marketplace"></a>Azure Marketi 'nde IoT Edge modüllerinin önemli avantajları:

| **Yayımcılar için**    | **Müşteriler için (IoT geliştiricileri)**  |
| :------------------- | :-------------------|
| IoT Edge çözümleri derlemek ve dağıtmak isteyen milyonlarca geliştiricilere ulaşın.  | Güvenli ve test edilmiş bileşenleri kullanma güveniyle bir IoT Edge çözümü oluşturun. |
| Bir kez yayımlayın ve kapsayıcıları destekleyen tüm IoT Edge donanımları üzerinde çalıştırın. | Belirli gereksinimlere göre 1. ve 3. taraf IoT Edge modüllerini bularak ve dağıtarak pazarlamaya yönelik süreyi azaltın. |
| Esnek faturalandırma seçenekleriyle monetize <ul> <li> Ücretsiz ve kendi lisansınızı getirin (KLG). </li> </ul> | Satın alımları, fatura modellerinizi tercih ettiğiniz şekilde yapın. <ul> <li> Ücretsiz ve kendi lisansınızı getirin (KLG). </li> </ul> |

## <a name="what-is-an-iot-edge-module"></a>IoT Edge modülü nedir?

Azure IoT Edge, çalışma mantığını uç üzerinde modüller biçiminde dağıtmanıza ve yönetmenize olanak tanır. Azure IoT Edge modüller, IoT Edge tarafından yönetilen en düşük hesaplama birimleridir ve Microsoft Hizmetleri (Azure Stream Analytics gibi), 3. taraf hizmetleri veya kendi çözüme özgü kodunuzla bulunabilir. IoT Edge modüller hakkında daha fazla bilgi edinmek için bkz. [Azure IoT Edge modülleri anlama](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules).

**Kapsayıcı teklif türü ve IoT Edge modülü teklif türü arasındaki fark nedir?**

IoT Edge modülü teklif türü, bir IoT Edge cihazında çalışan belirli bir kapsayıcı türüdür. IoT Edge bağlamında çalıştırılacak varsayılan yapılandırma ayarlarıyla birlikte gelir ve isteğe bağlı olarak IoT Edge çalışma zamanına tümleştirilecek IoT Edge modülü SDK 'sını kullanır.

## <a name="publishing-your-iot-edge-module"></a>IoT Edge modülünüzü yayımlama

**Doğru storefront seçme**

IoT Edge modüller yalnızca Azure Marketi 'Nde yayımlanır, AppSource uygulanmaz.  İzleme farklılıkları ve hedef kitleler hakkında daha fazla bilgi için bkz. [çözümünüz için yayımlama seçeneğini belirleme](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type).
 
**Faturalandırma seçenekleri**

Market şu anda **ücretsiz** olarak destekler ve IoT Edge modüller Için **kendi LISANSıNı getir (KLG)** faturalandırma seçenekleridir.
 
**Yayımlama seçenekleri**

Her durumda IoT Edge modüller **Transact** yayımlama seçeneğini seçmelidir.  Yayımlama seçenekleri hakkında daha fazla bilgi için bkz. [yayımlama seçeneği seçme](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) .  

## <a name="eligibility-criteria"></a>Uygunluk Ölçütleri

Microsoft Azure Market sözleşmelerinin ve ilkelerin tüm koşulları IoT Edge modül teklifleri için geçerlidir.  Ayrıca, IoT Edge modüller için önkoşulları ve teknik gereksinimleri vardır.  

**Önkoşulların önkoşulları**

Azure Marketi 'Nde bir IoT Edge modülünü yayımlamak için aşağıdaki önkoşulları karşılamanız gerekir:

- Bulut İş Ortağı Portalı (CPP) erişimi. Daha fazla bilgi için bkz. [Azure Marketi ve AppSource Yayımlama Kılavuzu](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- IoT Edge modülünüzü bir Azure Container Registry barındırın. 
- IoT Edge modülünüzün meta verileri (ayrıntılı olmayan liste) için hazırlayın: 
    - Bir başlık
    - Bir açıklama (HTML biçiminde)
    - Logo resmi (PNG biçimi ve 40x40px, 90x90px, 115x115px, 255x115px dahil)
    - Kullanım Koşulları ve Gizlilik ilkesi
    - Varsayılan modül yapılandırması (Route, ikizi istenen özellikler, createOptions, ortam değişkenleri)
    - Belgeler
    - Destek kişileri

**Teknik gereksinimler**

Azure Market 'te sertifikalı ve yayımlanmış olması için bir IoT Edge modülü için birincil teknik gereksinimler, [IoT Edge modülü teknik varlıklarınızı hazırlama](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-technical-assets)bölümünde ayrıntılı olarak açıklanmıştır.  

## <a name="documentation-and-resources"></a>Belgeler ve kaynaklar

[IoT Edge bir modül teklifi oluşturun](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-offer) --bulut Yayımlama Portalı ile yeni bir IoT Edge modülü teklifi yayımlama adımları.

## <a name="next-steps"></a>Sonraki adımlar

Daha önce yapmadıysanız,

- Market 'e [kaydolun](https://azuremarketplace.microsoft.com/sell) .

Kaydolduysanız ve yeni bir teklif oluşturuyor ya da mevcut bir teklif üzerinde çalışıyorsanız,

- Teklifinizin oluşturulması veya tamamlanabilmesi için [bulut iş ortağı portalı](https://cloudpartner.azure.com/) oturum açın.
- Bir IoT Edge modülü teklifinin nasıl yayımlanacağı hakkında bilgi için bkz. [IoT Edge modüle yönelik yayımlamaya genel bakış](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-offer-process-parts) .
