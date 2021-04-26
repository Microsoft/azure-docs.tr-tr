---
title: Azure haritalar 'ın kimlik doğrulama ayrıntılarını görüntüleme
description: Azure haritalar 'ın kimlik doğrulama ayrıntılarını görüntülemek için Azure portal kullanın.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: a6ffcbf5a8c36958dd3ea74de4d826fe25a1139c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87126473"
---
Azure haritalar hesabı kimlik doğrulama ayrıntılarını Azure portal görüntüleyebilirsiniz. Burada, hesabınızda, **Ayarlar** menüsünde **kimlik doğrulaması**' nı seçin.

![Kimlik doğrulaması ayrıntıları](../media/how-to-manage-authentication/how-to-view-auth.png)

Azure haritalar hesabı oluşturulduktan sonra, `x-ms-client-id` Azure Portal kimlik doğrulaması ayrıntıları sayfasında Azure Maps değeri mevcuttur. Bu değer, REST API istekleri için kullanılacak hesabı temsil eder. Bu değer, uygulama yapılandırmasında depolanmalıdır ve Azure Maps ile Azure AD kimlik doğrulaması kullanılırken http istekleri yapılmadan önce alınmalıdır.
