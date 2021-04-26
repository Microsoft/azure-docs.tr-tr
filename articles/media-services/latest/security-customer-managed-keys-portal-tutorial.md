---
title: Müşteri tarafından yönetilen anahtarları veya BYOK portalını kullanma
description: Bu öğreticide, müşteri tarafından yönetilen anahtarları etkinleştirmek veya bir Azure Media Services depolama hesabıyla kendi anahtarınızı (BYOK) getirmek için Azure portal kullanın.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 10/18/2020
ms.openlocfilehash: e34649162c7a30d4ab43aa068d2c864b5c2d90e0
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106282391"
---
# <a name="tutorial-use-the-azure-portal-to-use-customer-managed-keys-or-byok-with-media-services"></a>Öğretici: müşteri tarafından yönetilen anahtarları veya BYOK 'u kullanmak için Azure portal kullanın Media Services

2020-05-01 API 'SI ile müşteri tarafından yönetilen bir RSA anahtarını, sistem tarafından yönetilen kimliği olan Azure Media Services bir hesapla kullanabilirsiniz. Bu öğreticide Azure portal adımları ele alınmaktadır.

Kullanılan hizmetler şunlardır:

- Azure Depolama
- Azure Key Vault
- Azure Media Services

Bu öğreticide, Azure portal için kullanmayı öğreneceksiniz:

> [!div class="checklist"]
> - Bir kaynak grubu oluşturun.
> - Sistem tarafından yönetilen kimliğe sahip bir depolama hesabı oluşturun.
> - Sistem tarafından yönetilen bir kimlikle Media Services hesabı oluşturun.
> - Müşteri tarafından yönetilen bir RSA anahtarı depolamak için bir Anahtar Kasası oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Azure aboneliği.

Azure aboneliğiniz yoksa [ücretsiz bir deneme hesabı oluşturun](https://azure.microsoft.com/free/).

## <a name="system-managed-keys"></a>Sistem tarafından yönetilen anahtarlar

<!-- Create a resource group -->
[!INCLUDE [create a resource group in the portal](./includes/task-create-resource-group-portal.md)]

> [!IMPORTANT]
> Aşağıdaki depolama hesabı oluşturma adımları için Gelişmiş ayarlar ' da sistem tarafından yönetilen anahtar seçimini seçersiniz.

<!-- Create a media services account -->

[!INCLUDE [create a media services account in the portal](./includes/task-create-media-services-account-portal.md)]

<!-- Create a key vault -->

[!INCLUDE [create a key vaultl](./includes/task-create-key-vault-portal.md)]

<!-- Enable CMK BYOK on the account -->
[!INCLUDE [enable CMK](./includes/task-enable-cmk-byok-portal.md)]

> [!IMPORTANT]
> Aşağıdaki depolama şifreleme adımları için, **müşteri tarafından yönetilen anahtar seçimini** seçersiniz.

<!-- Set encryption for storage account -->
[!INCLUDE [Set encryption for storage account](./includes/task-set-storage-encryption-portal.md)]

## <a name="change-the-key"></a>Anahtarı değiştirme

Media Services, anahtarın ne zaman değiştirildiğini otomatik olarak algılar. Isteğe bağlı: Bu işlemi test etmek Için, aynı anahtar için başka bir anahtar sürümü oluşturun. Media Services anahtarın değiştirildiğini algılayabilmelidir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Oluşturduğunuz kaynakları kullanmaya devam edemezseniz ve *faturalandırılmaya devam* etmek istemiyorsanız, bunları silin.

## <a name="next-steps"></a>Sonraki adımlar

Nasıl yapılacağını öğrenmek için sonraki makaleye gidin:
> [!div class="nextstepaction"]
> [URL 'ye göre uzak bir dosya kodlayın ve REST ile videoyu akışla](stream-files-tutorial-with-rest.md)
