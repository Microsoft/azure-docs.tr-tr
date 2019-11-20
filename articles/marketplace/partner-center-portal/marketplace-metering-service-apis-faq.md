---
title: Market ölçüm hizmeti API 'Leri-SSS | Azure Marketi
description: Azure Marketi 'nde SaaS teklifinin kullanımını yayma.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: c4f51adbcaa5e5b750169f53a1333544365fd4f3
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825490"
---
# <a name="marketplace-metering-service-apis---faq"></a>Market ölçüm hizmeti API’leri - SSS

Azure kullanıcısı ölçülen faturalandırma içeren bir SaaS hizmetine abone olduktan sonra, müşteri tarafından kullanılan her faturalandırma boyutu için tüketimi izleyebilirsiniz. Tüketim, müşteri tarafından seçilen dönem için ayarlanan dahil edilen miktarları aşarsa, hizmetiniz kullanım olaylarını Microsoft 'a yayacaktır.

## <a name="emit-usage-events"></a>Kullanım olaylarını yayma

>[!Note]
>Bu bölüm yalnızca SaaS teklifleri için geçerlidir. Bu, planlardan en az birinin teklifi yayınlama sırasında tanımlanan ölçüm hizmeti boyutlarına sahip olması durumunda geçerlidir.

![Kullanım olaylarını yayma](media/isv-emits-usage-event.png)

Kullanım olaylarını yaymada API sözleşmesi hakkında bilgi için bkz. [SaaS Batch kullanımı olay API 'si](./marketplace-metering-service-apis.md#batch-usage-event) .

### <a name="how-often-is-it-expected-to-emit-usage"></a>Ne sıklıkta kullanım yayma bekleniyor?

İdeal olarak, yalnızca önceki saat içinde kullanım için kullanım süresi boyunca, son saat için kullanımı her saat için bir süre yayma bekliyorduk.

### <a name="what-is-the-maximum-delay-between-the-time-an-event-occurs-and-the-time-a-usage-event-is-emitted-to-microsoft"></a>Bir olayın gerçekleştiği zaman arasındaki en uzun gecikme ve kullanım olayının Microsoft 'a nasıl yayıldığını nelerdir?

En ideal olarak, kullanım olayı, son saat içinde gerçekleşen olaylar için saatte bir yayınlanır. Ancak gecikme beklenmektedir. İzin verilen en fazla gecikme 24 saattir, bu da kullanım olaylarının kabul edilmemesini sağlayacak.

Örneğin, bir gün içinde kullanım olayı 1 PM 'de gerçekleşirse, bu olayla ilişkili kullanım olayını oluşturmak için bir sonraki gün 1 PM 'ye kadar olur. Bu, sistem yayma kullanımının bir süre içinde olduğu durumlarda, daha sonra kullanım gerçekleştiği zaman aralığı için kullanım olayını kurtarabilir ve daha sonra bir uygunluk kaybı olmadan gönderebilir.

### <a name="what-happens-when-you-send-more-than-one-usage-event-on-the-same-hour"></a>Aynı saat içinde birden fazla kullanım olayı gönderdiğinizde ne olur?

Saat aralığı için yalnızca bir kullanım olayı kabul edilir. Saat aralığı 0 ' dan başlar ve dakika 59 ' de sona erer.  Aynı saat aralığı için birden fazla kullanım olayı yayıldıysanız, sonraki kullanım olayları yineleme olarak bırakılır.

### <a name="what-happens-when-you-emit-usage-for-a-saas-subscription-that-has-been-unsubscribed-already"></a>Zaten abone olunan bir SaaS aboneliği için kullanım yaydığınızda ne olur?

Bir SaaS aboneliği silindikten sonra Market platformuna yayılan kullanım olayları kabul edilmez.

### <a name="can-you-get-a-list-of-all-saas-subscriptions-including-active-and-unsubscribed-subscriptions"></a>Etkin ve aboneliği kaldırma abonelikleri dahil olmak üzere tüm SaaS aboneliklerinin bir listesini alabilir misiniz?

Evet, `GET /saas/subscriptions` API 'sini çağırdığınızda tüm SaaS aboneliklerinin bir listesini içerir. Her bir SaaS aboneliğine yönelik yanıttaki durum alanı, aboneliğin etkin veya aboneliği kaldırma olduğunu yakalar. Liste abonelikleri çağrısı, zaman içinde en fazla 100 abonelik döndürür.

## <a name="next-steps"></a>Sonraki adımlar

- Daha fazla bilgi için bkz. [Market ölçüm hizmeti API 'leri](./marketplace-metering-service-apis.md) .
