---
title: Azure Stream Analytics Önizleme özellikleri
description: Bu makalede, şu anda önizleme aşamasında olan Azure Stream Analytics özellikleri listelenmektedir
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/16/2021
ms.openlocfilehash: 55745c022038fa85f5b114f2bc347ed7292665eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104589659"
---
# <a name="azure-stream-analytics-preview-features"></a>Azure Stream Analytics Önizleme özellikleri

Bu makale, şu anda Azure Stream Analytics için Önizlemedeki tüm özellikleri özetler. Bir üretim ortamında Önizleme özelliklerinin kullanılması önerilmez.

## <a name="authenticate-to-sql-database-output-with-managed-identities-preview"></a>Yönetilen kimlikler ile SQL veritabanı çıktısına kimlik doğrulama (Önizleme)

Azure Stream Analytics, Azure SQL veritabanı çıkış havuzları için [yönetilen kimlik kimlik doğrulamasını](../active-directory/managed-identities-azure-resources/overview.md) destekler. Yönetilen kimlikler, Kullanıcı tabanlı kimlik doğrulama yöntemlerinin, parola değişiklikleri nedeniyle yeniden kimlik doğrulaması ihtiyacı gibi sınırlamaları ortadan kaldırır. 

## <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Azure Machine Learning tarafından yönetilen özel ML modelleriyle gerçek zamanlı yüksek performans Puanlama

Azure Stream Analytics, Azure Machine Learning tarafından yönetilen ve Azure Kubernetes Service (AKS) veya Azure Container Instances (acı) ' de barındırılan ve kod yazmanızı gerektirmeyen bir iş akışı kullanarak yüksek performanslı ve gerçek zamanlı Machine Learning Puanlama destekler. Önizleme için [kaydolun](https://aka.ms/asapreview1)

## <a name="c-custom-de-serializers"></a>C# özel de serileştiriciler
Geliştiriciler, verileri prototip, XML veya özel bir biçimde işlemek için Azure Stream Analytics gücünden yararlanabilir. C# ' de [özel seri hale getiriciler](custom-deserializer-examples.md) uygulayabilirsiniz ve bu, Azure Stream Analytics tarafından alınan olayların serileştirilmek için kullanılabilir.

## <a name="extensibility-with-c-custom-code"></a>C# özel kodlu genişletilebilirlik

Bulutta veya IoT Edge Stream Analytics modülleri oluşturan geliştiriciler özel C# işlevlerini yazabilir veya yeniden kullanabilir ve [Kullanıcı tanımlı işlevler](stream-analytics-edge-csharp-udf-methods.md)aracılığıyla doğrudan sorgu içinde çağırabilir.

## <a name="debug-query-steps-in-visual-studio"></a>Visual Studio 'da sorgu adımlarında hata ayıkla

Visual Studio için Azure Stream Analytics araçları 'nda yerel test yaparken, veri diyagramında ara satır kümesini kolayca önizleyebilirsiniz. 


## <a name="live-data-testing-in-visual-studio"></a>Visual Studio 'da canlı veri testi

Azure Stream Analytics için Visual Studio Araçları, Olay Hub 'ı veya IoT Hub gibi bulut kaynaklarından canlı etkinlik akışlarına karşı sorguları test etmenizi sağlayan yerel test özelliğini geliştirir. [Visual Studio için Azure Stream Analytics araçları 'nı kullanarak canlı verileri yerel olarak test](stream-analytics-live-data-local-testing.md)etme hakkında bilgi edinin.

## <a name="visual-studio-code-for-azure-stream-analytics"></a>Azure Stream Analytics için Visual Studio Code

Azure Stream Analytics işleri Visual Studio Code olarak yazılabilir. [Vs Code kullanmaya başlama Öğreticimizi](./quick-create-visual-studio-code.md)inceleyin.

## <a name="local-testing-with-live-data-in-visual-studio-code"></a>Visual Studio Code içindeki canlı verilerle yerel test

İşi Azure 'a göndermeden önce, sorgularınızı yerel makinenizde canlı verilere karşı test edebilirsiniz. Her test yinelemesi ortalama olarak iki ile üç saniyeden az sürer ve çok verimli bir geliştirme süreci elde edilir.

