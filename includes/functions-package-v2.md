---
title: include dosyası
description: include dosyası
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2020
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: bcc55156ca1d03614a4ff9767d6cf3f2603c06ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96025610"
---
Aşağıdaki yöntemleri kullanarak tercih ettiğiniz geliştirme ortamınızda destek ekleyin.

| Geliştirme ortamı  | Uygulama türü      | Destek eklemek için |
|--------------------------|-----------------------|----------------|
| Visual Studio            | C# sınıf kitaplığı      | [NuGet paketini yükler](../articles/azure-functions/functions-bindings-register.md#vs) |
| Visual Studio Code       | Temel [araçlara](../articles/azure-functions/functions-run-local.md) göre | [Uzantı paketini Kaydet](../articles/azure-functions/functions-bindings-register.md#extension-bundles)<br><br>[Azure Araçları uzantısının](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) yüklenmesi önerilir. |
| Diğer herhangi bir düzenleyici/IDE     | Temel [araçlara](../articles/azure-functions/functions-run-local.md) göre | [Uzantı paketini Kaydet](../articles/azure-functions/functions-bindings-register.md#extension-bundles) |
| Azure Portalı             | Yalnızca portalda çevrimiçi | Bağlama eklerken yüklenir<br /><br /> İşlev uygulamanızı yeniden yayımlamak zorunda kalmadan mevcut bağlama uzantılarını güncelleştirmek için bkz. [uzantılarınızı güncelleştirme](../articles/azure-functions/functions-bindings-register.md) . |