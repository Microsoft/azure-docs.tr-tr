---
title: Hareket ve yayma olaylarını algılama-Azure
description: Bu hızlı başlangıçta, program aracılığıyla doğrudan yöntemleri çağırarak hareket ve yayma olaylarını algılamak için IoT Edge canlı video analizlerinin nasıl kullanılacağı gösterilmektedir.
ms.topic: quickstart
ms.date: 08/10/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: 09a17712287c73fa165b4bec77682831a51edfa3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98060618"
---
# <a name="quickstart-detect-motion-and-emit-events"></a>Hızlı başlangıç: hareket ve yayma olaylarını Algıla

Bu hızlı başlangıç, IoT Edge üzerinde canlı video analizlerini kullanmaya başlamanıza yönelik adımlarda size yol gösterir. IoT Edge cihazı olarak bir Azure sanal makinesi ve canlı video akış simülasyonu kullanır. Kurulum adımlarını tamamladıktan sonra, bu akıştaki tüm hareketleri algılayan ve raporlayan bir medya grafiğiyle sanal bir canlı video akışı çalıştırabileceksiniz. Aşağıdaki diyagramda, bu medya grafiğinin grafik temsili gösterilmektedir.

![Hareket algılamayı temel alan canlı video analizi](./media/analyze-live-video/motion-detection.svg) 

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/detect-motion-emit-events-quickstart/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/detect-motion-emit-events-quickstart/python/header.md)]
::: zone-end

## <a name="prerequisites"></a>Önkoşullar

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/detect-motion-emit-events-quickstart/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/detect-motion-emit-events-quickstart/python/prerequisites.md)]
::: zone-end

## <a name="review-the-sample-video"></a>Örnek videoyu gözden geçirin

::: zone pivot="programming-language-csharp"
[!INCLUDE [review-sample-video](includes/detect-motion-emit-events-quickstart/csharp/review-sample-video.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [review-sample-video](includes/detect-motion-emit-events-quickstart/python/review-sample-video.md)]
::: zone-end


## <a name="set-up-azure-resources"></a>Azure kaynakları ayarlama

::: zone pivot="programming-language-csharp"
[!INCLUDE [setup-azure-resources](includes/detect-motion-emit-events-quickstart/csharp/setup-azure-resources.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [setup-azure-resources](includes/detect-motion-emit-events-quickstart/python/setup-azure-resources.md)]
::: zone-end

## <a name="set-up-your-development-environment"></a>Geliştirme ortamınızı kurma

::: zone pivot="programming-language-csharp"
[!INCLUDE [setup-development-environment](includes/detect-motion-emit-events-quickstart/csharp/setup-development-environment.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [setup-development-environment](includes/detect-motion-emit-events-quickstart/python/setup-development-environment.md)]
::: zone-end

## <a name="examine-the-sample-files"></a>Örnek dosyaları inceleyin

::: zone pivot="programming-language-csharp"
[!INCLUDE [examine-sample-files](includes/detect-motion-emit-events-quickstart/csharp/examine-sample-files.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [examine-sample-files](includes/detect-motion-emit-events-quickstart/python/examine-sample-files.md)]
::: zone-end

## <a name="generate-and-deploy-the-deployment-manifest"></a>Dağıtım bildirimini oluşturma ve dağıtma

::: zone pivot="programming-language-csharp"
[!INCLUDE [generate-deploy-deployment-manifest](includes/detect-motion-emit-events-quickstart/csharp/generate-deploy-deployment-manifest.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [generate-deploy-deployment-manifest](includes/detect-motion-emit-events-quickstart/python/generate-deploy-deployment-manifest.md)]
::: zone-end

## <a name="prepare-to-monitor-events"></a>Olayları izlemeye hazırlanma

::: zone pivot="programming-language-csharp"
[!INCLUDE [prepare-monitor-events](includes/detect-motion-emit-events-quickstart/csharp/prepare-monitor-events.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prepare-monitor-events](includes/detect-motion-emit-events-quickstart/python/prepare-monitor-events.md)]
::: zone-end

## <a name="run-the-sample-program"></a>Örnek programı çalıştırma

::: zone pivot="programming-language-csharp"
[!INCLUDE [run-sample-program](includes/detect-motion-emit-events-quickstart/csharp/run-sample-program.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [run-sample-program](includes/detect-motion-emit-events-quickstart/python/run-sample-program.md)]
::: zone-end

## <a name="interpret-results"></a>Sonuçları yorumlama

::: zone pivot="programming-language-csharp"
[!INCLUDE [interpret-results](includes/detect-motion-emit-events-quickstart/csharp/interpret-results.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [interpret-results](includes/detect-motion-emit-events-quickstart/python/interpret-results.md)]
::: zone-end

## <a name="clean-up-resources"></a>Kaynakları temizleme

Diğer hızlı başlangıçlarını denemek istiyorsanız, oluşturduğunuz kaynakları saklamanız gerekir. Aksi takdirde, Azure portal kaynak gruplarınıza gidin, bu hızlı başlangıcı çalıştırdığınız kaynak grubunu seçin ve ardından tüm kaynakları silin.

## <a name="next-steps"></a>Sonraki adımlar

Canlı video akışındaki bir nesneyi algılama gibi diğer hızlı başlangıçlarını çalıştırın.        
