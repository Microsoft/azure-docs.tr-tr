---
title: Yaklaşım Analizi kapsayıcı örneğini doğrulama
titleSuffix: Azure Cognitive Services
description: Yaklaşım Analizi kapsayıcı örneğini doğrulamayı öğrenin.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: d0a5e09f1d697c2f5f842edfb06be4b13229d095
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96009935"
---
### <a name="verify-the-sentiment-analysis-container-instance"></a>Yaklaşım Analizi kapsayıcı örneğini doğrulama

1. **Genel bakış** sekmesini SEÇIN ve IP adresini kopyalayın.
1. Yeni bir tarayıcı sekmesi açın ve IP adresini girin. Örneğin, girin `http://<IP-address>:5000 (http://55.55.55.55:5000` ). Kapsayıcının ana sayfası görüntülenir ve bu da kapsayıcının çalıştığını bilmenizi sağlar.

    ![Çalıştığını doğrulamak için kapsayıcı giriş sayfasını görüntüleme](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Kapsayıcının Swagger sayfasına gitmek için **hizmet API 'Si açıklaması** bağlantısını seçin.

1. **Post** API 'lerinden birini seçin ve **dene**' yi seçin. Parametreler, bu örnek girişi içeren görüntülenir:

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "1",
          "text": "Hello world. This is some input text that I love."
        },
        {
          "language": "fr",
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "language": "es",
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        }
      ]
    }
    ```

1. Girişi aşağıdaki JSON içeriğiyle değiştirin:

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "7",
          "text": "I was fortunate to attend the KubeCon Conference in Barcelona, it is one of the best conferences I have ever attended. Great people, great sessions and I thoroughly enjoyed it!"
        }
      ]
    }
    ```

1. **Showstats** öğesini olarak ayarlayın `true` .

1. Metnin yaklaşımını belirlemek için **Yürüt** ' ü seçin.

    Kapsayıcıda paketlenmiş model, 0 ile 1 arasında değişen bir puan üretir; burada 0 negatif yaklaşım ve 1 pozitif yaklaşım olur.

    Döndürülen JSON yanıtı, güncelleştirilmiş metin girişi için yaklaşım içerir:

    ```json
    {
      "documents": [
      {
        "id": "7",
        "score": 0.9826303720474243,
        "statistics": {
          "charactersCount": 176,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 1,
        "validDocumentsCount": 1,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 1
      }
    }
    ```

Artık `id` Yanıt yükünün JSON verilerinin belgesini özgün istek yükü belgesiyle ilişkilendirebiliriz `id` . Daha büyük bir yaklaşım, çok fazla `0.98` pozitif bir yaklaşım olduğunu gösterir.