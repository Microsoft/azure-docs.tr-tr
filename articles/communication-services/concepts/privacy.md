---
title: Azure Iletişim Hizmetleri için bölge kullanılabilirliği ve veri yerleşimi
description: Azure Iletişim hizmetlerinde veri yerleşimi ve gizlilikle ilgili önemli konularda bilgi edinin
author: chpalm
manager: anvalent
services: azure-communication-services
ms.author: chpalm
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: beb8b81710cf4728259a8eb1df920cd74efce3ce
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728139"
---
# <a name="region-availability-and-data-residency"></a>Bölge kullanılabilirliği ve veri yerleşikliği

Azure Iletişim Hizmetleri, müşterilerimizin gizlilik ve kişisel veri gereksinimlerini karşıladığından yardımcı olmaya kararlıdır. Uygulama kullanan insanlarla doğrudan ilişki ile Iletişim hizmetlerini kullanan bir geliştirici olarak, büyük olasılıkla kendi verilerinin bir denetleyicisidir. Azure Iletişim Hizmetleri bu verileri sizin adınıza depolarken ve şifrelerken, büyük olasılıkla bu verilerin bir işlemcisi olabilir. Bu sayfada hizmetin verileri nasıl sakladığı ve bu verileri nasıl tanımlayabilmeniz, dışarı aktarılacağı ve silebilmeniz özetlenmektedir.

## <a name="data-residency"></a>Veri yerleşimi

Bir Iletişim Hizmetleri kaynağı oluştururken bir **Coğrafya** (Azure veri merkezi değil) belirtirsiniz. Iletişim Hizmetleri tarafından REST 'te depolanan tüm veriler, Iletişim Hizmetleri tarafından dahili olarak seçilen bir veri merkezinde bu coğrafya 'da tutulur. Veriler başka coğrafi bölgelerde aktarım gösterebilir veya işlenebilir. Bu genel uç noktalar, son kullanıcılara konumlarına bakılmaksızın yüksek performanslı ve düşük gecikme süresine sahip bir deneyim sağlamak için gereklidir.

## <a name="data-residency-and-events"></a>Veri yerleşimi ve olaylar

Azure Iletişim Hizmetleri ile yapılandırılmış tüm Event Grid sistem konuları, genel bir konumda oluşturulur. Güvenilir teslimi desteklemek için, genel bir Event Grid sistem konusu, olay verilerini herhangi bir Microsoft veri merkezinde saklayabilir. Azure Iletişim Hizmetleri ile Event Grid yapılandırdığınızda, olay verilerinizi denetiminizin altındaki bir Azure kaynağı olan Event Grid olarak sunuyoruz. Azure Iletişim Hizmetleri Azure Event Grid kullanacak şekilde yapılandırılabilirken, Event Grid kaynağınızın ve içinde depolanan verilerin yönetilmesi sizin sorumluluğunuzdadır.

## <a name="relating-humans-to-azure-communication-services-identities"></a>İnsanların Azure Iletişim Hizmetleri kimlikleriyle ilişkilendirilmesi

Uygulamanız insan kullanıcıları ve Iletişim hizmeti kimlikleri arasındaki ilişkiyi yönetir. Bir insan kullanıcısının verilerini silmek istediğinizde, Kullanıcı için bağıntılı tüm Iletişim hizmeti kimliklerini içeren verileri silmeniz gerekir.

İki Iletişim hizmeti verisi kategorisi vardır:
- **API verileri.** Bu veriler, sohbet API 'Leri aracılığıyla yönetilen bir örnek olan Iletişim hizmeti API 'Leri tarafından oluşturulur ve yönetilir.
- **Azure Izleyici günlükleri** Bu veriler hizmet tarafından oluşturulur ve Azure Izleyici veri platformu üzerinden yönetilir. Bu veriler, Iletişim Hizmetleri kullanımınızı anlamanıza yardımcı olacak telemetri ve ölçümleri içerir. Bu, Iletişim hizmeti API 'Leri tarafından yönetilmez.

## <a name="api-data"></a>API verileri

### <a name="identities"></a>Kimlikler

Azure Iletişim Hizmetleri bir kimlik dizinini tutar, silme işlemini kaldırmak için [Deleteıdentity](/rest/api/communication/communicationidentity/delete) API 'sini kullanın. Bir kimlik silindiğinde tüm ilişkili erişim belirteçleri iptal edilir ve sohbet iletileri silinir. Kimlik kaldırma hakkında daha fazla bilgi için [Bu sayfaya bakın](../quickstarts/access-tokens.md).

- Deleteıdentity

### <a name="azure-resource-manager"></a>Azure Resource Manager

Iletişim hizmetleriyle Azure portal veya Azure Resource Manager API 'Leri kullanarak kişisel veri oluşturabilir. [Azure Resource Manager sistemlerindeki kişisel verileri yönetmeyi öğrenmek için bu sayfayı kullanın.](../../azure-resource-manager/management/resource-manager-personal-data.md)

### <a name="telephone-number-management"></a>Telefon numarası yönetimi

Azure Iletişim Hizmetleri, bir Iletişim Hizmetleri kaynağıyla ilişkili telefon numaralarının dizinini tutar. Telefon numaralarını almak ve silmek için [telefon numarası yönetim API 'lerini](/rest/api/communication/phonenumberadministration) kullanın:

- `Get All Phone Numbers`
- `Release Phone Number`

### <a name="chat"></a>Sohbet

Sohbet iş parçacıkları ve iletiler açıkça silinene kadar tutulur. Katılımcıları olmayan tamamen boşta iş parçacığı 30 gün sonra otomatik olarak silinir. İletileri almak, listelemek, güncelleştirmek ve silmek için [sohbet API 'lerini](/rest/api/communication/chat/chatthread) kullanın.

- `Get Thread`
- `Get Message`
- `List Messages`
- `Update Message`
- `Delete Thread`
- `Delete Message`

### <a name="sms"></a>SMS

Gönderilen ve alınan SMS iletileri, hizmet tarafından ephemersel olarak işlenir ve korunmaz.

### <a name="pstn-voice-calling"></a>PSTN sesli çağırma

Ses ve video iletişimi, hizmet tarafından ephemersel olarak işlenir ve kaynakta Azure Izleyici günlükleri dışında hiçbir veri korunmaz.

### <a name="internet-voice-and-video-calling"></a>Internet ses ve video çağırma

Ses ve video iletişimi, hizmet tarafından ephemersel olarak işlenir ve kaynakta Azure Izleyici günlükleri dışında hiçbir veri korunmaz.

## <a name="azure-monitor-and-log-analytics"></a>Azure Izleyici ve Log Analytics

Azure Iletişim Hizmetleri, işlemsel sistem durumunu ve hizmetin kullanımını anlamak için Azure Izleyici günlüğe kaydetme verilerine akış oluşturacak. Bu günlüklerin bazıları, Iletişim hizmeti kimliklerini ve telefon numaralarını alan verileri olarak içerir. Potansiyel olarak kişisel verileri silmek için [Bu yordamları Azure izleyici 'de kullanın](../../azure-monitor/logs/personal-data-mgmt.md). Ayrıca [, Azure izleyici için varsayılan saklama süresini](../../azure-monitor/logs/manage-cost-storage.md)yapılandırmak isteyebilirsiniz.

## <a name="additional-resources"></a>Ek kaynaklar

- [GDPR ve CCPA için Azure veri konu Istekleri](/microsoft-365/compliance/gdpr-dsr-azure)
- [Microsoft Güven Merkezi](https://www.microsoft.com/trust-center/privacy/data-location)
- [Azure etkileşimli harita-müşteri Verilerim nerede?](https://azuredatacentermap.azurewebsites.net/)
