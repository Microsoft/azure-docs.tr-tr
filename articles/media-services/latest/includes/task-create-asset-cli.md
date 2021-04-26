---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: CLI, devx-track-azurecli
ms.openlocfilehash: e16e3dc6788fb30a87a78fd6d6077087f461cfa7
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107513166"
---
<!--Create a media services asset CLI-->

Aşağıdaki Azure CLı komutu yeni bir Media Services varlığı oluşturur. Değerlerini `assetName` `amsAccountName` ve `resourceGroup` Şu anda çalıştığınız değerlerle değiştirin.  

```azurecli
az ams asset create -n assetName -a amsAccountName -g resourceGroup
```
