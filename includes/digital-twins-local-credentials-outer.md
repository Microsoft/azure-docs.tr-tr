---
author: baanders
description: Azure dijital TWINS örnekleri 'nde DefaultAzureCredential için yerel kimlik doğrulamasını ayarlamaya yönelik dosya ekleme-giriş
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: dcef8030c47e771e5cd771dac09b5f96e3d38abd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102473697"
---
### <a name="set-up-local-azure-credentials"></a>Yerel Azure kimlik bilgilerini ayarlama

Bu örnek [](/dotnet/api/azure.identity.defaultazurecredential) `Azure.Identity` , yerel makinenizde çalıştırdığınızda Azure dijital TWINS örneğiyle kullanıcıların kimliğini doğrulamak için DefaultAzureCredential (kitaplığın parçası) kullanır. Bir istemci uygulamasının Azure dijital TWINS ile kimlik doğrulaması yapabilme yolları hakkında daha fazla bilgi için bkz. [*nasıl yapılır: yazma uygulaması kimlik doğrulaması kodu*](../articles/digital-twins/how-to-authenticate-client.md).

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](digital-twins-local-credentials-inner.md)]