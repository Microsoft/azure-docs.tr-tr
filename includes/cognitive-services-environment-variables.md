---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 367ae27a6afec803d2e3f98f54bdcf852330ddc6
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274643"
---
## <a name="configure-an-environment-variable-for-authentication"></a>Kimlik doğrulaması için bir ortam değişkeni yapılandırma

Uygulamaların kullandıkları bilişsel hizmetlere erişiminin kimliğini doğrulaması gerekir. Kimlik doğrulaması yapmak için, Azure kaynaklarınızın anahtarlarını depolamak üzere bir ortam değişkeni oluşturmanızı öneririz. 

Anahtarınızı aldıktan sonra, uygulamayı çalıştıran yerel makinede yeni bir ortam değişkenine yazın. Ortam değişkenini ayarlamak için bir konsol penceresi açın ve işletim sisteminizin yönergelerini izleyin. Kaynağınızın `your-key` anahtarlarından biriyle değiştirin.

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx COGNITIVE_SERVICE_KEY "your-key"
```

Ortam değişkenini ekledikten sonra, konsol penceresi de dahil olmak üzere ortam değişkenini okumak için gereken tüm çalışan programları yeniden başlatmanız gerekebilir. Örneğin, düzenleyiciniz olarak Visual Studio kullanıyorsanız, örneği çalıştırmadan önce Visual Studio 'Yu yeniden başlatın.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

Ortam değişkenini ekledikten sonra değişiklikleri uygulamak için konsol pencerenizden `source ~/.bashrc` çalıştırın.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

.bash_profile dosyanızı düzenleyin ve ortam değişkenini ekleyin:

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

Ortam değişkenini ekledikten sonra değişiklikleri uygulamak için konsol pencerenizden `source .bash_profile` çalıştırın.

***