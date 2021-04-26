---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 8c77efe9e3e301573b032d1dc1dd32bb4a5ab1a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103417759"
---
Sıkıştırılmış ses işleme, [GStreamer](https://gstreamer.freedesktop.org)kullanılarak uygulanır. Lisanslama nedeniyle, GStreamer ikili dosyaları derlenmez ve konuşma SDK 'Sı ile bağlantılı değildir. Geliştiricilerin çeşitli bağımlılıklar ve eklentiler yüklemeleri, bkz. [Windows 'A yükleme](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c) veya [Linux 'a yükleme](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c). Konuşma SDK 'sının çalışma zamanında ikilileri yükleyebilmesi için GStreamer ikililerinin sistem yolunda olması gerekir. Örneğin, Windows 'ta, konuşma SDK 'Sı `libgstreamer-1.0-0.dll` `gstreamer-1.0-0.dll` çalışma zamanı sırasında veya (en son GStreamer) bulup, bu, GStreamer ikili dosyalarının sistem yolunda olduğu anlamına gelir.

