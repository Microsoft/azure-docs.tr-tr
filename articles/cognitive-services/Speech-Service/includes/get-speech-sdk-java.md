---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 6897bf9b4ccce71048af88a3108e3d87d17a375d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102434591"
---
:::row:::
    :::column span="3":::
        Android için Java SDK 'Sı, gerekli kitaplıkları ve gerekli Android izinlerini içeren bir <a href="https://developer.android.com/studio/projects/android-library" target="_blank">AAR (Android kitaplığı) </a>olarak paketlenmiştir. Paket olarak bir Maven deposunda barındırılır `https://csspeechstorage.blob.core.windows.net/maven/` `com.microsoft.cognitiveservices.speech:client-sdk:1.15.0` .
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Java" src="https://docs.microsoft.com/media/logos/logo_java.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

Android Studio projenizden paketi kullanmak için aşağıdaki değişiklikleri yapın:

1. Proje düzeyi *Build. Gradle* dosyasında aşağıdakileri `repositories` bölümüne ekleyin:
  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

2. Modül düzeyi *Build. Gradle* dosyasında, bölümüne aşağıdakini ekleyin `dependencies` :
  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.15.0'
  ```

Java SDK 'sı Ayrıca [konuşma cihazları SDK 'sının](../speech-devices-sdk.md)bir parçasıdır.

#### <a name="additional-resources"></a>Ek kaynaklar

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android" target="_blank">Android 'e özgü Java hızlı başlangıç kaynak kodu </a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre" target="_blank">Java çalışma zamanı (JRE) hızlı başlangıç kaynak kodu </a>
