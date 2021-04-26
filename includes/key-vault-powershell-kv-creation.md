---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: d19c656946817b06cd620d8a48073bed8299af7d
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502271"
---
Önceki adımdan kaynak grubunda bir Key Vault oluşturmak için Azure PowerShell [New-Azkeykasası](/powershell/module/az.keyvault/new-azkeyvault) cmdlet 'ini kullanın. Bazı bilgileri sağlamanız gerekir:

- Anahtar Kasası adı: yalnızca rakam (0-9), harf (A-z, A-Z) ve kısa çizgi (-) içerebilen 3 ile 24 karakter arasında bir dize

  > [!Important]
  > Her Anahtar Kasası benzersiz bir ada sahip olmalıdır. -Unique-keykasa-adı> <aşağıdaki örneklerde anahtar kasanızın adıyla değiştirin.

- Kaynak grubu adı: **Myresourcegroup**.
- Konum: **EastUS**.

```azurepowershell-interactive
New-AzKeyVault -Name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "East US"
```

Bu cmdlet’in çıktısı, yeni oluşturulan anahtar kasasının özelliklerini gösterir. Aşağıda listelenen iki özelliği not edin:

- **Kasa adı**: Yukarıdaki--name parametresine verdiğiniz addır.
- **Kasa URI 'si**: örnekte bu https://- &lt; Unique-keykasasına-Name &gt; . Vault.Azure.net/. REST API'si aracılığıyla kasanızı kullanan uygulamaların bu URI'yi kullanması gerekir.

Bu noktada Azure hesabınız, bu yeni anahtar kasasında herhangi bir işlemi gerçekleştirmeye yetkili olan tek hesaptır.
