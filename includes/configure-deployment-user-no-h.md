---
title: include dosyası
description: include dosyası
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/14/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 4e699707db02de07f3d1ebb7d1fa8d0575a10aa3
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836892"
---
FTP ve yerel Git için bir Azure web uygulaması kullanarak dağıtabilirsiniz bir *dağıtım kullanıcısı*. Dağıtım kullanıcı yapılandırdıktan sonra tüm Azure dağıtımlarınız için kullanabilirsiniz. Hesap düzeyinde dağıtım kullanıcı adı ve parola, Azure aboneliği kimlik bilgilerinizden farklıdır. 

Dağıtım kullanıcısı yapılandırma için çalıştırın [az webapp deployment kullanıcı kümesi](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) Azure Cloud shell'de komutu. Değiştirin \<username > ve \<parola > Dağıtım kullanıcı adı ve parola ile. 

- Kullanıcı adı Azure içinde benzersiz olmalıdır ve yerel Git için bildirim içermemelidir ' @' sembolü. 
- Parola en az sekiz karakter uzunluğunda olmalı, şu üç öğeyi sahip olmalıdır: harf, rakam ve semboller. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Parola olarak JSON çıktısını gösterir `null`. `'Conflict'. Details: 409` hatası alırsanız kullanıcı adını değiştirin. `'Bad Request'. Details: 400` hatası alırsanız daha güçlü bir parola kullanın. 

Kullanıcı adı ve web uygulamalarınızı dağıtmak için kullanılacak parolayı kaydedin.
