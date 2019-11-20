---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: 3cd5f59a3dcf3afcd26d16874e7dc77ca0a7e844
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71841680"
---
### <a name="install-via-composer"></a>Besteci aracılığıyla Install
1. Projenizin kökünde **besteci. JSON** adlı bir dosya oluşturun ve bu dosyaya aşağıdaki kodu ekleyin:
   
    ```json
    {
      "require": {
        "microsoft/azure-storage": "*"
      }
    }
    ```
2. Proje kökünde **[besteci. phar][composer-phar]** 'yi indirin.
3. Bir komut istemi açın ve proje kökünde aşağıdaki komutu yürütün
   
    ```
    php composer.phar install
    ```

Alternatif olarak, kaynak kodu kopyalamak için GitHub 'daki [Azure Storage php Istemci kitaplığına][php-sdk-github] gidin.

[php-sdk-github]: https://github.com/Azure/azure-storage-php
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: https://github.com/Azure/azure-sdk-for-php
[composer-phar]: http://getcomposer.org/composer.phar
