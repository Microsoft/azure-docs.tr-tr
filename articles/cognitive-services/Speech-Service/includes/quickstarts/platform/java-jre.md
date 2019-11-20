---
title: 'Hızlı başlangıç: Java için konuşma SDK (Windows, Linux, macOS) Platform Kurulumu-konuşma hizmeti'
titleSuffix: Azure Cognitive Services
description: Bu kılavuzu kullanarak, Java 'yı (Windows, Linux, macOS) konuşma Hizmetleri SDK 'sını kullanmaya yönelik platformunuzu ayarlayın.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/11/2019
ms.author: erhopf
ms.openlocfilehash: 2814327bdc434dbdae5644bd40b09d0506b21df9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502439"
---
Bu kılavuzda, 64 bit Java 8 JRE için [konuşma SDK 'sının](~/articles/cognitive-services/speech-service/speech-sdk.md) nasıl yükleneceği gösterilmektedir.

> [!NOTE]
> Konuşma Cihazları SDK’sı ve Roobo cihazı için bkz. [Konuşma Cihazları SDK’sı](~/articles/cognitive-services/speech-service/speech-devices-sdk.md).

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Desteklenen işletim sistemleri

- Java konuşma SDK 'Sı paketi, bu işletim sistemleri için kullanılabilir:
  - Windows: yalnızca 64 bit
  - Mac: macOS X sürüm 10,13 veya üzeri
  - Linux: 64-bit yalnızca Ubuntu 16,04, Ubuntu 18,04 veya detem 9

## <a name="prerequisites"></a>Önkoşullar

- [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) veya [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

- [Tutulma Java IDE](https://www.eclipse.org/downloads/) (Java 'nın zaten yüklü olması gerekir)
- Desteklenen Linux platformları, belirli kitaplıkların yüklü olmasını gerektirir (Güvenli Yuva Katmanı desteği için`libssl` ve ses desteği için `libasound2`). Bu kitaplıkların doğru sürümlerini yüklemek için gereken komutlar için aşağıdaki dağıtıma bakın.

  - Ubuntu 'da gerekli paketleri yüklemek için aşağıdaki komutları çalıştırın:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.0 libasound2
        ```

  - 9\. yıl 'da, gerekli paketleri yüklemek için aşağıdaki komutları çalıştırın:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.2 libasound2
        ```

- Windows 'ta, platformunuz için [Visual Studio C++ 2019 için Microsoft Visual yeniden dağıtılabilir](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) gereklidir. Bunu ilk kez yüklemek, bu kılavuza devam etmeden önce Windows 'u yeniden başlatmanızı gerektirebilir.

## <a name="create-an-eclipse-project-and-install-the-speech-sdk"></a>Bir tutulma projesi oluşturma ve konuşma SDK 'sını yüklemesi

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [windows](../quickstart-list.md)]
