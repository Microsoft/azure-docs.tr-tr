---
title: Azure Iletişim Hizmetleri Kullanıcı arabirimi çerçevesine genel bakış
titleSuffix: An Azure Communication Services conceptual document
description: Azure Communication Services kullanıcı arabirimi çerçevesi hakkında bilgi edinin
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 4ab1a157cdf3ef5017b227cd090379dcab91997e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105931565"
---
# <a name="azure-communication-services-ui-framework"></a>Azure Iletişim Hizmetleri Kullanıcı arabirimi çerçevesi

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

:::image type="content" source="../media/ui-framework/pre-and-custom-composite.png" alt-text="Önceden oluşturulmuş ve özel bileşik siteler arasındaki karşılaştırma":::

Azure Iletişim Hizmetleri Kullanıcı ARABIRIMI çerçevesi, modern iletişim kullanıcı deneyimleri oluşturmanızı kolaylaştırır. Bu, uygulamalarınıza sunabilmenizi sağlayan bir üretim için Ready Kullanıcı arabirimi bileşenleri kitaplığı sağlar:

- **Bileşik bileşenler** -bu bileşenler, yaygın iletişim senaryolarını uygulayan bir çift anahtar çözümdür. Uygulamalarına hızlı bir şekilde video çağırma veya sohbet deneyimleri ekleyebilirsiniz. Bileşik siteler, temel bileşenler kullanılarak oluşturulan açık kaynaklı bileşenlerdir.
- **Temel bileşenler** -bu bileşenler, özel iletişim deneyimi oluşturmanıza olanak sağlayan açık kaynaklı yapı taşlarıdır. Bileşenler, derleme deneyimleri için birleştirilebilecek hem çağırma hem de sohbet özelliği için sunulur. 

Bu Kullanıcı arabirimi SDK 'Ları, [Microsoft 'un akıcı tasarım dilini](https://developer.microsoft.com/fluentui/) ve varlıklarını kullanır. Akıcı Kullanıcı arabirimi, Microsoft ürünlerinde test edilen kullanıcı arabirimi çerçevesi için temel bir katman sağlar.

## <a name="differentiating-components-and-composites"></a>**Bileşenleri ve Birleşik siteleri farklılaştırın**

**Temel bileşenler** temel Azure Iletişim Hizmetleri SDK 'larının üzerine kurulmuştur ve temel SDK 'ları başlatma, video işleme ve kullanıcı denetimlerini, video oluşturma/kapatma gibi temel eylemleri uygular. Önceden oluşturulmuş, üretime hazır iletişim bileşenleri kullanarak kendi özel düzen deneyimlerinizi oluşturmak için bu **temel bileşenleri** kullanabilirsiniz.

:::image type="content" source="../media/ui-framework/component-overview.png" alt-text="UI çerçevesi için bileşene genel bakış":::

**Bileşik bileşenler** , daha kapsamlı iletişim deneyimleri oluşturmak için birden çok **temel bileşeni** birleştirir. Bu üst düzey bileşenler, sıfırdan oluşturma görevi olmadan tam bir iletişim deneyimini bırakmak için mevcut bir uygulamayla kolayca tümleştirilebilir. Geliştiriciler, kendi uygulamalarına istenen çevreleyen deneyim ve akışın oluşturulmasına ve iletişim karmaşıklığını Birleşik bileşenlere bırakmasına odaklanabilirler.

:::image type="content" source="../media/ui-framework/composite-overview.png" alt-text="UI çerçevesi için bileşik genel bakış":::

## <a name="what-ui-framework-is-best-for-my-project"></a>Projem için en iyi kullanıcı arabirimi çerçevesi nedir?

Bu gereksinimleri anlamak, doğru SDK 'yı seçmenize yardımcı olur:

- **Ne kadar özelleştirmeye ihtiyacınız var?** Azure Communication Core SDK 'Ları bir UX içermez ve istediğiniz UX 'i oluşturabilmeniz için tasarlanmıştır. UI çerçevesi bileşenleri, Kullanıcı arabirimi varlıklarını azaltılmış özelleştirme maliyetine sağlar.
- **Toplantı özelliklerine ihtiyacınız var mı?** Toplantı sisteminde, bulanık arka plan ve kabarık el gibi çekirdek Azure Iletişim Hizmetleri SDK 'lerinde Şu anda bulunmayan bazı benzersiz yetenekler bulunur.
- **Hangi platformları hedefliyorsanız?** Farklı platformlar farklı yeteneklere sahiptir.

Değiştirilen [Kullanıcı arabirimi SDK](ui-sdk-features.md)'lerinde özellik kullanılabilirliği hakkındaki ayrıntılar burada mevcuttur, ancak temel denge aşağıda özetlenmiştir.

|SDK/SDK|Uygulama karmaşıklığı|   Özelleştirme yeteneği|  Events| Sohbet| [Takımlar birlikte çalışma](./../teams-interop.md)
|---|---|---|---|---|---|---|
|Bileşik bileşenler|Düşük|Düşük|✔|✔|✕
|Temel bileşenler|Orta|Orta|✔|✔|✕
|Çekirdek SDK 'Ları|Yüksek|Yüksek|✔|✔ |✔

## <a name="cost"></a>Maliyet

Azure UI çerçevelerinin kullanımı, ek Azure maliyeti veya ölçümü içermez. Yalnızca temel hizmetin kullanımı için, aynı çağırma, sohbet ve PSTN ölçümlerini kullanarak ödeme yaparsınız.

## <a name="supported-use-cases"></a>Desteklenen kullanım örnekleri

Events

- Grup KIMLIĞIYLE Azure Communication Services çağrısına katılarak

'İ

- Iş parçacığı KIMLIĞIYLE Azure Iletişim Hizmetleri sohbetine katılın

## <a name="supported-identities"></a>Desteklenen kimlikler:

UI çerçevesini başlatmak ve hizmette kimlik doğrulamak için bir Azure Iletişim Hizmetleri kimliği gereklidir. Kimlik doğrulaması hakkında daha fazla bilgi için bkz. [kimlik doğrulama](../authentication.md) ve [erişim belirteçleri](../../quickstarts/access-tokens.md)


## <a name="recommended-architecture"></a>Önerilen mimari 

:::image type="content" source="../media/ui-framework/framework-architecture.png" alt-text="İstemci-sunucu mimarisine sahip UI çerçevesi önerilen mimari ":::

Bileşik ve temel bileşenler, Azure Communication Services erişim belirteci kullanılarak başlatılır. Erişim belirteçleri, yönettiğiniz güvenilen bir hizmet aracılığıyla Azure Iletişim hizmetlerinden temin olmalıdır. Daha fazla bilgi için bkz. [hızlı başlangıç: erişim belirteçleri oluşturma](../../quickstarts/access-tokens.md) ve [Güvenilen hizmet öğreticisi](../../tutorials/trusted-service-tutorial.md) .

Bu SDK 'lar, katılabilecekleri çağrı veya sohbet için de bağlam gerektirir. Kullanıcı erişim belirteçlerine benzer şekilde, bu bağlam kendi güvenilen hizmetiniz aracılığıyla istemcilere dağıtılabilir. Aşağıdaki listede, işlem gerçekleştirmeniz gereken başlatma ve kaynak yönetimi işlevleri özetlenmektedir.

| Contoso sorumlulukları                                 | UI çerçevesi sorumlulukları                         |
|----------------------------------------------------------|-----------------------------------------------------------------|
| Azure 'dan erişim belirteci sağlama                    | Bileşenleri başlatmak için verilen erişim belirtecini geçir        |
| Yenileme işlevi sağla                                 | Geliştirici tarafından sunulan işlevi kullanarak erişim belirtecini Yenile          |
| Arama veya sohbet için JOIN bilgilerini al/geçir          | Bileşenleri başlatmak için çağrı ve sohbet bilgilerini geçirin |
| Herhangi bir özel veri modeli için Kullanıcı bilgilerini alma/geçirme | Özel veri modelini işlemek için bileşenlere geçirin          |
