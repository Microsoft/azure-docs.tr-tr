---
title: Azure konuşma CLı
titleSuffix: Azure Cognitive Services
description: Konuşma CLı, konuşma hizmetinin herhangi bir kod yazmadan kullanılmasına yönelik bir komut satırı aracıdır. Konuşma CLı, en az kurulum gerektirir ve kullanım durumlarınızın karşılanıp karşılanmadığını görmek için konuşma hizmetinin temel özellikleriyle denemeye hemen başlamak kolaydır.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/13/2021
ms.author: trbye
ms.custom: devx-track-azurecli
ms.openlocfilehash: ddba62b37c200ade87dbb51042fe0cb084a9ef9a
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106131"
---
# <a name="what-is-the-speech-cli"></a>Konuşma CLI’si nedir?

Konuşma CLı, konuşma hizmetinin herhangi bir kod yazmadan kullanılmasına yönelik bir komut satırı aracıdır. Konuşma CLı, en az kurulum gerektirir ve kullanım durumlarınızın karşılanıp karşılanmadığını görmek için konuşma hizmetinin temel özellikleriyle denemeye hemen başlamak kolaydır. Dakikalar içinde, bir dosya dizininden Batch konuşma tanıma gibi basit test iş akışlarını çalıştırabilir veya bir dosyadaki dizeler koleksiyonunda metinden konuşmaya dönüştürebilirsiniz. Basit iş akışlarının ötesinde, konuşma CLı, üretime hazırdır ve otomatikleştirilmiş veya kabuk betikleri kullanılarak daha büyük süreçler çalıştırmak için ölçeklendirilebilir `.bat` .

Konuşma SDK 'sında çoğu özellik konuşma CLı 'da bulunur ve konuşma CLı 'de bazı gelişmiş özellikler ve özelleştirmeler basitleştirilmiştir. Konuşma CLı veya konuşma SDK 'sının ne zaman kullanılacağına karar vermek için aşağıdaki kılavuzu göz önünde bulundurun.

Şu durumlarda konuşma CLı 'Yı kullan:
* Minimum kurulum ve kod olmadan konuşma hizmeti özellikleriyle denemeler yapmak istiyorsunuz
* Konuşma hizmetini kullanarak bir üretim uygulaması için görece basit gereksinimleriniz vardır

Şu durumlarda konuşma SDK 'sını kullan:
* Konuşma hizmeti işlevlerini belirli bir dil veya platform içinde (örneğin, C#, Python, C++) bütünleştirmek istiyorsunuz
* Gelişmiş hizmet istekleri gerektirebilecek karmaşık gereksinimleriniz var veya yanıt akışı dahil özel davranış geliştirmiş olabilirsiniz

## <a name="core-features"></a>Temel Özellikler

* Konuşma tanıma-konuşma 'yı ses dosyalarından veya doğrudan bir mikrofondan metne dönüştürün ya da kayıtlı bir konuşma yapın.

* Konuşma senşi-metin dosyalarından giriş veya doğrudan komut satırından giriş kullanarak metin okumayı dönüştürür. [SSML yapılandırmalarının](speech-synthesis-markup.md)ve [Standart ya da sinir seslerinden](speech-synthesis-markup.md#neural-and-custom-voices)yararlanarak konuşma çıkış özelliklerini özelleştirin.

* Konuşma çevirisi-bir kaynak dildeki sesi bir hedef dilde metin veya sese çevirin.

* Azure işlem kaynaklarında çalıştırın-kullanarak bir Azure uzaktan işlem kaynağında çalıştırmak için konuşma CLı komutları gönderin `spx webjob` .

## <a name="get-started"></a>başlarken

Konuşma CLı 'yı kullanmaya başlamak için [hızlı başlangıç](spx-basics.md)bölümüne bakın. Bu makalede, bazı temel komutların nasıl çalıştırılacağı ve ayrıca konuşmayı metne dönüştürme ve metinden konuşmaya yönelik toplu işlemler çalıştırmak için biraz daha gelişmiş komutlar gösterilmektedir. Temel bilgiler makalesini okuduktan sonra bazı özel komutlar yazmaya başlamak veya basit konuşma hizmeti işlemlerini otomatikleştirmek için söz dizimi hakkında yeterli bilgiye sahip olmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- [Konuşma CLI hızlı başlangıç](spx-basics.md) ile çalışmaya başlama
- [Veri deponuzi yapılandırma](./spx-data-store-configuration.md)
- [Konuşma CLI ile Batch işlemlerini çalıştırmayı](./spx-batch-operations.md) öğrenin
