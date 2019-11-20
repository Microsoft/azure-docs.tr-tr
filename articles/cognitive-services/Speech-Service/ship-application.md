---
title: Konuşma SDK 'Sı ile uygulama geliştirme-konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Desteklenen platformlarda konuşma SDK 'sını kullanan bir uygulamayı dağıtmayı öğrenin.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: jhakulin
ms.custom: seodec18
ms.openlocfilehash: 0bcfd40510352abc9e64782255d3c8349b8ad87e
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072414"
---
# <a name="ship-an-application"></a>Uygulama teslim etme

Gözlemleyin [Speech SDK'sı lisans](https://aka.ms/csspeech/license201809), hem de [üçüncü taraf yazılım bildirimleri](https://csspeechstorage.blob.core.windows.net/drop/1.0.0/ThirdPartyNotices.html) Azure Bilişsel hizmetler konuşma SDK dağıttığınızda. Ayrıca, gözden [Microsoft gizlilik bildirimi](https://aka.ms/csspeech/privacy).

Platforma bağlı olarak, uygulamanızı çalıştırmak için farklı bağımlılıklara mevcut.

## <a name="windows"></a>Windows

Bilişsel hizmetler konuşma SDK'sı, Windows 10 ve Windows Server 2016 üzerinde test edilir.

Bilişsel hizmetler konuşma SDK 'Sı, sistemde [Visual C++ Studio 2019 için Microsoft Visual Redistributable](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) gerektirir. En son sürümü için yükleyicileri indirebileceğiniz `Microsoft Visual C++ Redistributable for Visual Studio 2019` burada:

- [Win32](https://aka.ms/vs/16/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/16/release/vc_redist.x64.exe)

Uygulamanız, yönetilen kod kullanıyorsa `.NET Framework 4.6.1` veya daha sonra hedef makinede gereklidir.

Mikrofon girişi için Media Foundation kitaplıkları yüklü olması gerekir. Bu kitaplıklar, Windows 10 ve Windows Server 2016'ya bir parçasıdır. Mikrofon ses giriş cihazını kullanılmayan sürece bu kitaplıklar Speech SDK'sı kullanmak mümkündür.

Gerekli dosyaları Speech SDK'sı, uygulamanızın aynı dizinde dağıtılabilir. Bu şekilde uygulamanızı kitaplıkları doğrudan erişebilirsiniz. Uygulamanızı eşleşen doğru sürümünü (Win32/x64) seçtiğinizden emin olun.

| Ad | İşlev |
| :--- | :------- |
| `Microsoft.CognitiveServices.Speech.core.dll`   | Yerel ve yönetilen dağıtım için gerekli core SDK'sı |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | Yönetilen dağıtım için gerekli                      |

> [!NOTE]
> Sürümden itibaren 1.3.0 (önceki sürümlerde sevk edilen) dosya `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` artık gerekli değildir. İşlevselliği artık çekirdek SDK 'sında tümleşiktir.

> [!NOTE]
> Windows Forms App (.NET Framework) C# projesi için, kitaplıkların projenizin dağıtım ayarlarına eklendiğinden emin olun. `Properties -> Publish Section`altında bunu kontrol edebilirsiniz. `Application Files` düğmesine tıklayın ve aşağı kaydırma listesinden karşılık gelen kitaplıkları bulun. Değerin `Included`olarak ayarlandığından emin olun. Proje yayımlandığında/dağıtıldığında, Visual Studio dosyayı içerecektir.

## <a name="linux"></a>Linux

Konuşma SDK 'Sı Şu anda Ubuntu 16,04, Ubuntu 18,04 ve demı 9 dağıtımlarını desteklemektedir.
Yerel bir uygulama için Speech SDK'sı kitaplığı göndermeye gerek `libMicrosoft.CognitiveServices.Speech.core.so`.
Uygulamanızı eşleşen sürümünü (x86, x64) seçtiğinizden emin olun. Linux sürümüne göre aşağıdaki bağımlılıkları içerecek şekilde gerekebilir:

- GNU C Kitaplığı'nın paylaşılan kitaplıklar (iş parçacıkları POSIX programlama kitaplığı dahil olmak üzere `libpthreads`)
- OpenSSL kitaplığı (`libssl.so.1.0.0` veya `libssl.so.1.0.2`)
- ALSA uygulamalar için paylaşılan kitaplığı (`libasound.so.2`)

Ubuntu 'da, GNU C kitaplıklarının varsayılan olarak zaten yüklü olması gerekir. Son üç şu komutları kullanarak yüklenebilir:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

DEYON 9 ' da şu paketleri yükler:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.2 libasound2
```

## <a name="next-steps"></a>Sonraki adımlar

- [Konuşma deneme aboneliğinizi alın](https://azure.microsoft.com/try/cognitive-services/)
- [C# ' de Konuşma tanıma öğrenin](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
