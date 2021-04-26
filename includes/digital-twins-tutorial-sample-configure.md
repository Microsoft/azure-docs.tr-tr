---
author: baanders
description: Azure dijital TWINS öğreticileri için dosya ekleme-örnek projeyi yapılandırma
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 67a2799a93141ad84f458642d8499a58784cc19c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103463905"
---
## <a name="configure-the-sample-project"></a>Örnek projeyi yapılandırma

Ardından, Azure dijital TWINS örneğinizle etkileşime girebilen bir örnek istemci uygulaması ayarlayın.

Daha önce [*Azure dijital TWINS uçtan uca örnekleri*](/samples/azure-samples/digital-twins-samples/digital-twins-samples) (ve henüz yapmadıysanız) için makinenizde daha önce indirdiğiniz dosyaya gidin.

Klasörün içindeyken _AdtSampleApp_ adresine gidin. Visual Studio 2019 ' de _**AdtE2ESample. sln**_ 'yi açın. 

Visual Studio 'da, dosyayı Düzenle penceresinde açmak için dosya _üzerinde Sampleclientapp > **appsettings.js**_ seçin. Bu, projeyi çalıştırmak için gerekli yapılandırma değişkenlerine sahip bir önceden ayarlanmış JSON dosyası işlevi görür.

Dosya gövdesinde, `instanceUrl` Azure Digital TWINS örneğiniz *ana bilgisayar adı URL 'siyle* değiştirin (aşağıda gösterildiği gibi, *ana bilgisayar adının* önüne **_https://_** ekleyerek).

```json
{
  "instanceUrl": "https://<your-Azure-Digital-Twins-instance-hostName>"
}
```

Dosyayı kaydedin ve kapatın. 

Sonra, *Sampleclientapp*' i oluştururken dosyayı çıkış dizinine kopyalamak üzere *appsettings.js* yapılandırın. Bunu yapmak için dosya *üzerindeappsettings.js* sağ seçin ve **Özellikler**' i seçin. **Özellikler** denetçisinde, *Çıkış Dizinine Kopyala* özelliğini arayın. Daha önceden bu değere ayarlanmamışsa değeri **kopyalamak** için değiştirin.

:::image type="content" source="../articles/digital-twins/media/includes/copy-config.png" alt-text="Vurgulanan appsettings.jsiçeren Çözüm Gezgini bölmesini ve ' çıkış dizinine Kopyala ' özelliği olan Özellikler bölmesini &quot;daha yeniyse kopyala&quot; olarak gösteren Visual Studio penceresinden alıntı" border="false" lightbox="../articles/digital-twins/media/includes/copy-config.png":::

Öğreticide kullanmaya devam etmek için _**AdtE2ESample**_ projesini Visual Studio 'da açık tutun.

[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](digital-twins-local-credentials-outer.md)]
