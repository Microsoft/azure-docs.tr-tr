---
title: include dosyası
description: include dosyası
services: functions
author: ggailey777
manager: cfowler
ms.service: azure-functions
ms.topic: include
ms.date: 05/17/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: c2fff707dcaafac69efcad3dbf33446a7b797396
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608414"
---
### <a name="azure-storage-sdk-version-in-functions-1x"></a>Azure depolama SDK'sı sürümü işlevlerde 1.x

Azure depolama SDK'sı sürümünü 7.2.1 işlevlerde 1.x, depolama Tetikleyicileri ve bağlamaları kullanın ([WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) NuGet paketi). Farklı bir depolama SDK'sı sürümü başvurmak ve bir depolama SDK'sı türe, işlev imzasında bağlama, İşlevler çalışma zamanı bu türe bağlanamaz bildirebilir. Çözüm, proje başvurularınızın emin olmaktır [WindowsAzure.Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1).
