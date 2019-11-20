---
title: Hizmet sağlayıcılarını görüntüleme ve yönetme
description: Müşteriler, hizmet sağlayıcıları, hizmet sağlayıcısı teklifleri ve Temsilcili kaynaklar hakkındaki bilgileri görüntülemek için Azure portal hizmet sağlayıcıları sayfasını kullanabilir.
ms.date: 11/15/2019
ms.topic: overview
ms.openlocfilehash: 12ddafe1d1e425ed1c1f1188be1377a584e03936
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74169656"
---
# <a name="view-and-manage-service-providers"></a>Hizmet sağlayıcılarını görüntüleme ve yönetme

Müşteriler, hizmet sağlayıcıları ve hizmet sağlayıcı teklifleri hakkındaki bilgileri görüntülemek, [Azure tarafından atanan kaynak yönetimi](../concepts/azure-delegated-resource-management.md)aracılığıyla belirli kaynakları devretmek ve ek hizmet sağlayıcısı teklifleri için alışveriş yapmak üzere [Azure Portal](https://portal.azure.com) **hizmet sağlayıcıları** sayfasını kullanabilir. Burada hizmet sağlayıcılarına ve müşterilere başvuracağız. birden çok kiracıyı yöneten kuruluşlar, yönetim deneyimlerini birleştirmek için aynı süreci kullanabilir.

Azure portal **hizmet sağlayıcıları** sayfasına erişmek için, müşteri **tüm hizmetleri**seçip **hizmet sağlayıcıları** arayabilir ve bunu seçebilirsiniz. Ayrıca, Azure portal üst kısmındaki arama kutusuna "hizmet sağlayıcıları" girerek da bulabilir.

**Hizmet sağlayıcılar** sayfasının yalnızca, Azure tarafından atanan kaynak yönetimi aracılığıyla Müşterinin aboneliklerine veya kaynak gruplarına erişimi olan hizmet sağlayıcılarıyla ilgili bilgileri gösterdiğini aklınızda bulundurun. Müşteri, müşterinin kaynaklarına erişmek için Azure tarafından atanan kaynak yönetimini kullanmayan ek hizmet sağlayıcılarıyla çalışıyorsa, bu hizmet sağlayıcıları hakkındaki bilgiler burada gösterilmez.

> [!NOTE]
> Servis sağlayıcılar, Azure portal **müşterilerime** giderek müşterileri hakkındaki bilgileri görüntüleyebilir. Daha fazla bilgi için bkz. [müşterileri ve Temsilcili kaynakları görüntüleme ve yönetme](view-manage-customers.md).

## <a name="view-service-provider-details"></a>Hizmet sağlayıcı ayrıntılarını görüntüle

Müşterinin üzerinde çalıştığı hizmet sağlayıcıları hakkındaki bilgileri görüntülemek için, **hizmet sağlayıcılar** sayfasının sol tarafında **sağlayıcı teklifleri** ' ni seçebilir.

Müşteri, her bir servis sağlayıcısı teklifi için hizmet sağlayıcının adını ve onunla ilişkili teklifi, ekleme işlemi sırasında müşterinin girdiği adı görür.

**Temsilciler** sütununda, müşteri, bu teklif için hizmet sağlayıcısına kaç abonelik ve/veya kaynak grubu atanmış olduğunu görür. Hizmet sağlayıcısı, bu abonelikleri ve/veya kaynak gruplarını teklifte belirtilen erişim düzeylerine göre erişebilecek ve yönetebilecektir.

## <a name="delegate-resources"></a>Kaynakları devretmek

Bir hizmet sağlayıcının bir müşterinin kaynaklarına erişebilmesi ve bunları yönetebilmesi için önce bunların temsilci olarak oluşturulması gerekir. Bir müşteri bir teklifi kabul ettiğinde ancak henüz hiç kaynak temsilcisi yoksa, **sağlayıcı teklifleri** bölümünün en üstünde bir not görür. Bu, müşterinin, servis sağlayıcı müşterinin kaynaklarına erişmeden önce eylemde bulunan bir işlem yapması gerektiğini bilmesini sağlar.

Abonelikler veya kaynak grupları atamak için:

1. Hizmet sağlayıcı, teklif ve ad içeren satırın kutusunu işaretleyin. Ardından ekranın üst kısmındaki **kaynakları devretmek** ' ı seçin.
1. **Kaynak temsilcisi** sayfasının **Teklif Ayrıntıları** bölümünde, hizmet sağlayıcısı ve teklifiyle ilgili ayrıntıları gözden geçirin. Teklifin rol atamalarını gözden geçirmek için, **Seçili teklifin ayrıntılarını görmek üzere buraya tıklayın ' ı**seçin.
1. **Temsilci** bölümünde, **temsilci abonelikleri** veya **temsilci kaynak grupları**' nı seçin.
1. Bu teklif için temsilci seçmek istediğiniz abonelikleri ve/veya kaynak gruplarını seçin ve ardından **Ekle**' yi seçin.
1. Bu hizmet sağlayıcısına seçtiğiniz kaynaklara erişim izni vermek istediğinizi onaylamak için sayfanın altındaki onay kutusunu seçin ve ardından **temsilci**' i seçin.

## <a name="add-or-remove-service-provider-offers"></a>Hizmet sağlayıcı tekliflerini ekleme veya kaldırma

Müşteri, **teklif Ekle**' ye tıklayarak **sağlayıcı teklifleri** sayfasından yeni bir servis sağlayıcısı teklifi ekleyebilir. Hizmet sağlayıcısı bu müşteri için bir teklif yayımlamalıdır. Müşteri daha sonra **özel teklifler** ekranından bu teklifi seçip **Oluştur**' u seçin.

Müşteri bir servis sağlayıcısı teklifini kaldırmak isterse, bu teklif için satırda çöp kutusu simgesini seçebilir. Silme işlemini onayladıktan sonra, söz konusu hizmet sağlayıcının artık bu teklif için daha önce atanmış olan müşteri kaynaklarına erişimi olmayacaktır.

## <a name="update-service-provider-offers"></a>Hizmet sağlayıcı tekliflerini güncelleştirme

Bir müşteri teklif ekledikten sonra, hizmet sağlayıcı aynı teklifin güncelleştirilmiş bir sürümünü Azure Marketi 'ne yayımlayabilir. Örneğin, yeni bir rol tanımı eklemek isteyebilir. Teklifin yeni bir sürümü yayımlandıysa, **sağlayıcı** teklif sayfasında bir "güncelleştirme" simgesi gösterir. Müşteri, teklifin geçerli sürümü ile yeni bir arasındaki farkları görmek için bu simgeyi seçebilir.

 ![Teklifi Güncelleştir simgesi](../media/update-offer.jpg)

Değişiklikleri gözden geçirdikten sonra müşteri yeni sürüme güncelleştirmeyi tercih edebilir. Bu yapıldıktan sonra, yeni sürümde belirtilen yetkilendirmeler ve diğer ayarlar, bu teklif için temsilci atanmış abonelikler ve/veya kaynak grupları için de geçerlidir.

## <a name="view-delegations"></a>Temsilcileri görüntüle

Temsilci atama, bir müşterinin Temsilcili kaynaklar için hizmet sağlayıcısına izin veren rol atamalarını temsil eder. Bu bilgileri görüntülemek için, **hizmet sağlayıcıları** sayfasının sol tarafındaki **Temsilciler** ' ı seçin.

Sayfanın üst kısmındaki filtreler, temsilcilerinizi, belirli müşterilere, tekliflere veya anahtar sözcüklere göre filtrelemenize ve gruplandırmasına olanak tanır.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure ışıklı kullanım](../overview.md)hakkında daha fazla bilgi edinin.
- Hizmet sağlayıcılarının **müşterilerime** Azure Portal müşterileri nasıl [görüntüleyip yönetebileceğini](view-manage-customers.md) öğrenin.