---
title: içerme dosyası
description: içerme dosyası
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 10/02/2019
ms.custom: include file
ms.openlocfilehash: f67f271ee02e38e168ddfaa84ed1309321cf8702
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949123"
---
> [!NOTE]
> Çok parçalı istekler genellikle üç parça gerektirir:
> * Bir **Content-Type** üst bilgisi:
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * Bir **içerik-değerlendirme**:
>   * `form-data; name="metadata"`
> * Karşıya yüklenecek dosya içeriği
>
> **Içerik türü** ve **Content-Disposition** , kullanım senaryosuna göre değişir.

Çok parçalı istekler, bir REST istemcisi veya C# [Postman](https://docs.microsoft.com/azure/digital-twins/how-to-configure-postman#make-a-multipart-post-request)gibi bir araç aracılığıyla programlı bir şekilde yapılabilir. REST istemci araçları karmaşık çok parçalı istekler için farklı destek düzeylerine sahip olabilir. Yapılandırma ayarları ayrıca araç ve araç arasında biraz farklılık gösterebilir. İhtiyaçlarınıza en uygun aracı doğrulayın.

> [!IMPORTANT]
> Azure Digital TWINS yönetim API 'Lerine yapılan çok parçalı isteklerin genellikle iki bölümü vardır:
> * **Content-Type** ve/veya **Content-Disposition** tarafından belirtilen blob meta verileri (ÖRNEĞIN, ilişkili bir MIME türü)
> * Karşıya yüklenecek bir dosyanın yapılandırılmamış içeriğini içeren blob içeriği
>
> **Düzeltme Eki** istekleri için iki bölümden hiçbiri gerekli değildir. **Post** veya Create işlemleri için her ikisi de gereklidir.

[Doluluk hızlı başlangıç kaynak kodu](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/api/update.cs) , Azure C# Digital TWINS yönetim API 'lerinde çok parçalı isteklerin nasıl yapılacağını gösteren tüm örnekleri içerir.