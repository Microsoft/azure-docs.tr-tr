---
title: Metin Okuma sık sorulan sorular
titleSuffix: Azure Cognitive Services
description: Metin Okuma hizmeti hakkında sık sorulan soruların yanıtlarını alın.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: panosper
ms.openlocfilehash: 67284225299641a71050342056ad7ca91bf39740
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96353723"
---
# <a name="text-to-speech-frequently-asked-questions"></a>Metin Okuma sık sorulan sorular

Bu SSS 'de sorularınıza yanıt bulamazsanız, [diğer destek seçeneklerine](../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext%253fcontext%253d%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)göz atın.

## <a name="general"></a>Genel

**S: standart bir ses modeli ve özel bir ses modeli arasındaki fark nedir?**

Y **: Standart** ses modeli ( _ses yazı tipi_ de denir) Microsoft 'a ait veriler kullanılarak eğitilmiş ve buluta zaten dağıtılmıştı. Bir ortalama modeli uyarlamak ve hoparlörün ses stilinin Timbre ve ifadesini aktarmak ya da Kullanıcı tarafından hazırlanan eğitim verilerine göre tam, yeni bir model eğmek için özel bir ses modeli kullanabilirsiniz. Günümüzde, daha fazla müşteri, botları için tek bir tür ve markalı ses istiyor. Özel ses oluşturma platformu, bu seçeneğe yönelik doğru seçimdir.

**S: standart bir ses modeli kullanmak istersem nereden başlayabilirim?**

Y **: 45**' 80 den fazla standart ses MODELI, http istekleri aracılığıyla kullanılabilir. İlk olarak bir [abonelik anahtarı](./overview.md#try-the-speech-service-for-free)alın. Önceden dağıtılan ses modellerine REST çağrıları yapmak için [REST API](./overview.md#reference-docs)bakın.

**S: özelleştirilmiş bir ses modeli kullanmak istersem, API standart sesler için kullanılan ile aynı mı?**

Y **: özel bir ses** modeli oluşturulup dağıtıldığında, modelinize yönelik benzersiz bir uç nokta alırsınız. Uygulamalarınızdaki seslendirmeyi kullanmak için, uç noktasını HTTP istekleriniz içinde belirtmeniz gerekir. Metin Okuma hizmeti için REST API kullanılabilen işlevler, Özel uç noktanız için kullanılabilir. [Özel uç noktanızı oluşturma ve kullanma](./how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint)hakkında bilgi edinin.

**S: kendi kendinize özel ses modelleri oluşturmak için eğitim verilerini hazırlıyorum mıyım?**

Y **: Evet**, eğitim verilerini özel bir ses modeli için hazırlamanız gerekir.

Özelleştirilmiş bir ses modeli oluşturmak için bir konuşma verileri koleksiyonu gerekir. Bu koleksiyon, konuşma kayıtlarının ses dosyalarından bir kümesinden ve her ses dosyasının dökümünden oluşan bir metin dosyasına sahiptir. Dijital sesinizin sonucu eğitim verilerinizin kalitesinden büyük ölçüde yararlanır. İyi bir metin okuma sesi oluşturmak için, kayıtların yüksek kaliteli, duran bir mikrofonla sessiz bir odada yapılması önemlidir. Mükemmel bir dijital ses oluşturmak için tutarlı bir birim, konuşma hızı ve konuşma sıklığı, hatta büyük bir deyişle, önemli bir dijital ses oluşturmak için önemlidir. Bir kayıt Studio 'ya seslerin kaydedilmesini kesinlikle öneririz.

Şu anda çevrimiçi kayıt desteği sağlamayız veya herhangi bir kayıt Studio önerisi sunuyoruz. Biçim gereksinimi için bkz. [kayıtları hazırlama ve dökümü](./how-to-custom-voice-create-voice.md)oluşturma.

**S: özel ses eğitimine yönelik konuşma verilerini kaydetmek için hangi betikleri kullanmalıyım?**

Y **: ses** kaydı için betikleri sınırlandırmıyoruz. Konuşmayı kaydetmek için kendi komut dosyalarınızı kullanabilirsiniz. Konuşma verilerinizde yeterli miktarda fonetik kapsama sahip olduğunuzdan emin olun. Özel bir sesi eğitebilmeniz için, 50 farklı cümleler (yaklaşık 3-5 dakikalık konuşma) olabilecek küçük bir konuşma verileri hacminde başlayabilirsiniz. Sağladığınız veriler daha fazla olduğunda sesinizin daha doğal olması gerekir. 2.000 ' den fazla cümle (yaklaşık 3-4 saat konuşma) için kayıtlar sağladığınızda tam bir ses yazı tipi eğitmenize başlayabilirsiniz. Yüksek kaliteli, tam bir ses almak için 6.000 ' den fazla cümle (yaklaşık 8-10 saat konuşma) kayıtlarını hazırlamanız gerekir.

Betikleri kayıt için hazırlamanıza yardımcı olacak ek hizmetler sağlıyoruz. Sorgular için [özel sesli müşteri desteğiyle](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) iletişim kurun.

**S: varsayılan değerden daha yüksek eşzamanlılık mi yoksa portalda teklif mi gerekiyor?**

Y **: modelinizi** 20 eşzamanlı istek artışlarına göre ölçeklendirebilirsiniz. Daha yüksek ölçeklendirmeyle ilgili sorgular için [özel sesli müşteri desteğiyle](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) iletişim kurun.

**S: modelimi indirebilir ve yerel olarak çalıştırabilir miyim?**

Y **: modeller** yerel olarak indirilemez ve yürütülemez.

**S: isteklerim kısıtlandı mı?**

Y **: bkz**. [konuşma Hizmetleri kotaları ve limitleri](speech-services-quotas-and-limits.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Sorun giderme](troubleshooting.md)
- [Sürüm notları](releasenotes.md)