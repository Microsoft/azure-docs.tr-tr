---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 09/03/2020
ms.author: msmbaldwin
ms.openlocfilehash: 59359d37fe347c8568c7944f75accdbc04cddb93
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967136"
---
1. `az group create`Bir kaynak grubu oluşturmak için komutunu kullanın:

    ```azurecli
    az group create --name KeyVault-PythonQS-rg --location eastus
    ```

    "Eastus" öğesini tercih ediyorsanız, sizin için bir konum için "" olarak değiştirebilirsiniz.

1. `az keyvault create`Anahtar Kasası oluşturmak için kullanın:

    ```azurecli
    az keyvault create --name <your-unique-keyvault-name> --resource-group KeyVault-PythonQS-rg
    ```

    `<your-unique-keyvault-name>`Tüm Azure genelinde benzersiz olan bir adla değiştirin. Genellikle kişisel veya şirket adınızı diğer sayı ve tanımlayıcılarla birlikte kullanırsınız. 

1. Koda Key Vault adını sağlayan bir ortam değişkeni oluşturun:

    ```bash
    export KEY_VAULT_NAME=<your-unique-keyvault-name>
    ```