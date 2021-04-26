---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: cdcb61249f6cb8f7b60c891dd4899d2e5cedbae6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104611976"
---
:::row:::
    :::column span="3":::
        Konuşma SDK 'Sı, Linux ile kullanıldığında aşağıdaki hedef mimarilerde yalnızca **Ubuntu 16.04/18.04/20.04**, **de, 9/10**, **Red Hat Enterprise Linux (RHEL) 7/8** ve **CentOS 7/8** destekler:
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Linux" src="https://docs.microsoft.com/media/logos/logo_linux-color.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

- C++ geliştirmesi için x86 (DeARM32/Ubuntu), x64, (de/Ubuntu) ve ARM64 (de/Ubuntu)
- Java için x64, ARM32 (de/Ubuntu) ve ARM64 (de/Ubuntu)
- .NET Core için x64, ARM32 (de/Ubuntu) ve ARM64 (de/Ubuntu)
- Python için x64

> [!IMPORTANT]
> Linux ARM64 üzerinde C# için .NET Core 3. x (DotNet-SDK-3. x paketi) gereklidir.

> [!NOTE]
> Konuşma SDK 'sını alp Linux 'ta kullanmak için, bir debir chroot ortamı oluşturun ve bu durumda,, [ınbc](https://wiki.alpinelinux.org/wiki/Running_glibc_programs)Linux wiki ' de

### <a name="system-requirements"></a>Sistem Gereksinimleri

Yerel bir uygulama için konuşma SDK 'Sı kullanır `libMicrosoft.CognitiveServices.Speech.core.so` . Hedef mimarinin (x86, x64) uygulamayla eşleştiğinden emin olun. Linux sürümüne bağlı olarak ek bağımlılıklar de gerekebilir.

- GNU C kitaplığının paylaşılan kitaplıkları (POSIX Iş parçacıkları programlama kitaplığı dahil `libpthreads` )
- OpenSSL kitaplığı ( `libssl.so.1.0.0` veya `libssl.so.1.0.2` )
- ALSA uygulamaları için paylaşılan kitaplık ( `libasound.so.2` )

# <a name="ubuntu-160418042004"></a>[Ubuntu 16.04/18.04/20.04](#tab/ubuntu)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2
```

> [!NOTE]
> Libssl 1.0. x yoksa, bunun yerine libssl 1.1 'yi de yüklemelisiniz.

# <a name="debian-910"></a>[De, 9/10](#tab/debian)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.2 libasound2
```

> [!NOTE]
> Libssl 1.0. x yoksa, bunun yerine libssl 1.1 'yi de yüklemelisiniz.

# <a name="rhel-78-and-centos-78"></a>[RHEL 7/8 ve CentOS 7/8](#tab/rhel-centos)

```Bash
sudo yum update
sudo yum install alsa-lib openssl
```

> [!IMPORTANT]
> - RHEL/CentOS 7 ' de, [konuşma SDK 'sı IÇIN RHEL/CentOS 7](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md)' yi yapılandırma yönergelerini izleyin.
> - RHEL/CentOS 8 ' de, [Linux Için OpenSSL 'yi yapılandırma](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md)yönergelerini izleyin.

---

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
