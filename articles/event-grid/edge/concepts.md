---
title: Kavramlar-Azure Event Grid IoT Edge | Microsoft Docs
description: IoT Edge Event Grid kavramları.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 73309e10e88c11e639e6ac6fd3bb061e1b5c685b
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992554"
---
# <a name="event-grid-concepts"></a>Event Grid kavramları

Bu makalede Azure Event Grid temel kavramları açıklanmaktadır.

## <a name="events"></a>Etkinlikler

Bir olay, sistemde gerçekleşen bir şeyi tam olarak açıklayan en düşük bilgi miktarıdır. Her olayda, olayın kaynağı, olayın gerçekleştiği süre ve benzersiz tanımlayıcı gibi yaygın bilgiler bulunur. Her olay, yalnızca belirli olay türüyle ilgili belirli bilgilere de sahiptir. 1 MB 'a kadar olan bir olay desteği şu anda önizleme aşamasındadır.

Bir olaya dahil olan özellikler için bkz. [Azure Event Grid olay şeması](event-schemas.md).

## <a name="publishers"></a>Yayımcılar

Yayımcı, Event Grid olayları gönderilmeye karar veren kullanıcı veya kuruluştur. Kendi uygulamanızdan olayları yayımlayabilirsiniz.

## <a name="event-sources"></a>Olay kaynakları

Olay kaynağı, olayın gerçekleştiği yerdir. Her olay kaynağı bir veya daha fazla olay türü ile ilgilidir. Örneğin, Azure depolama, blob tarafından oluşturulan olaylar için olay kaynağıdır. Uygulamanız, tanımladığınız özel olaylara yönelik olay kaynağıdır. Olay kaynakları, Event Grid olayları göndermekten sorumludur.

## <a name="topics"></a>Konular

Olay Kılavuzu konusu, kaynağın olayları gönderdiği bir uç nokta sağlar. Yayımcı, olay Kılavuzu konusunu oluşturur ve bir olay kaynağının bir konu veya birden fazla konu ihtiyacı olup olmadığına karar verir. İlgili olayların toplanması için bir konu kullanılır. Belirli olay türlerine yanıt vermek için, aboneler hangi konuların abone olacağına karar verir.

Uygulamanızı tasarlarken, kaç tane konu oluşturmak gerektiğine karar verme esnekliği vardır. Büyük çözümler için ilgili olayların her kategorisi için özel bir konu oluşturun. Örneğin, Kullanıcı hesaplarını ve işleme emirlerini değiştirmekle ilgili olayları gönderen bir uygulamayı düşünün. Herhangi bir olay işleyicisi, olayların kategorilerinin her ikisini de istemektedir. İki özel konu oluşturun ve olay işleyicilerinin bu kişilerin ilgilendiğiniz birine abone olmasına izin verin. Küçük çözümler için tüm olayları tek bir konuya göndermenizi tercih edebilirsiniz. Olay aboneleri istedikleri olay türlerini filtreleyebilir.

Event Grid konuları yönetme hakkında [REST API](api.md) bakın.

## <a name="event-subscriptions"></a>Olay abonelikleri

Abonelik, almak istediğiniz bir konu üzerinde hangi olayların Event Grid söyler. Aboneliği oluştururken, olayı işlemek için bir uç nokta sağlarsınız. Uç noktaya gönderilen olaylara filtre uygulayabilirsiniz. 

Event Grid abonelikleri yönetme hakkında [REST API](api.md) bakın.

## <a name="event-handlers"></a>Olay işleyicileri

Bir Event Grid perspektifinden olay işleyicisi, olayın gönderildiği yerdir. İşleyici, olayı işlemek için daha fazla işlem gerçekleştirir. Event Grid çeşitli işleyici türlerini destekler. Desteklenen bir Azure hizmetini veya kendi web kancasını işleyici olarak kullanabilirsiniz. İşleyicinin türüne bağlı olarak, Event Grid olayın teslimini güvence altına almak için farklı mekanizmalardan oluşur. Hedef olay işleyicisi bir HTTP Web kancası ise, işleyici `200 – OK`durum kodu döndürdüğünde olay yeniden denenir. Edge hub 'ı için, olay herhangi bir özel durum olmadan teslim edildiğinde, başarılı olarak kabul edilir.

## <a name="security"></a>Güvenlik

Event Grid konularda abone olma ve konuları yayımlama konusunda güvenlik sağlar. Daha fazla bilgi için bkz. [güvenlik ve kimlik doğrulaması Event Grid](security-authentication.md).

## <a name="event-delivery"></a>Olay teslimi

Event Grid, abonenin uç noktası tarafından bir olayın alındığını doğrulayamıyorsa olayı yeniden dağıtır. Daha fazla bilgi için bkz. [ileti teslimi Event Grid ve yeniden deneme](delivery-retry.md).

## <a name="batching"></a>Toplu İşleme

Özel bir konu kullanıldığında, olaylar her zaman bir dizide yayımlanmalıdır. Düşük aktarım hızı senaryolarında, dizi yalnızca bir değere sahip olur. Yüksek hacimli kullanım durumları için, daha yüksek bir verimlilik elde etmek üzere yayımlama başına birkaç olayı toplu olarak toplu yapmanızı öneririz. Toplu işlemler 1 MB 'a kadar olabilir. Her olay hala 1 MB 'tan (Önizleme) büyük olmamalıdır.