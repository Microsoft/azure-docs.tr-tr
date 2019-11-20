---
title: Azure PowerShell Örnekleri - Service Fabric | Microsoft Docs
description: Azure PowerShell Örnekleri - Service Fabric
services: service-fabric
documentationcenter: service-fabric
author: athinanthny
manager: chackdan
editor: ''
tags: ''
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: service-fabric
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: service-fabric
ms.date: 11/29/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: cae88e142c3bca15e837db7f084eef68434a78ca
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903262"
---
# <a name="azure-powershell-samples"></a>Azure PowerShell örnekleri

Aşağıdaki tablo, Service Fabric kümelerini, uygulamalarını ve hizmetlerini oluşturup yöneten PowerShell betiği örneklerinin bağlantılarını içerir.

[!INCLUDE [links to azure CLI and service fabric CLI](../../includes/service-fabric-powershell.md)]

| | |
|-|-|
| **Küme oluşturma** ||
| [Küme oluşturma (Azure)](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)| Bir Azure Service Fabric kümesi oluşturur. |
| **Kümeyi, düğümleri ve altyapıyı yönetme** ||
| [Uygulama sertifikası ekleme](./scripts/service-fabric-powershell-add-application-certificate.md)| Key Vault için bir x509 sertifikası oluşturur ve bunu kümenizdeki bir sanal makine ölçek kümesine dağıtır. |
| [Küme sanal makinelerinde RDP bağlantı noktası aralığını güncelleştirme](./scripts/service-fabric-powershell-change-rdp-port-range.md)|Dağıtılan bir kümedeki küme düğümü sanal makinelerinde RDP bağlantı noktası aralığını değiştirir.|
| [Küme düğümü sanal makineleri için yönetici kullanıcı adını ve parolasını güncelleştirme](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) | Küme düğümü sanal makineleri için yönetici kullanıcı adını ve parolasını güncelleştirir. |
| [Yük dengeleyicide bağlantı noktası açma](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) | Belirli bir bağlantı noktasında gelen trafiğe izin vermek için Azure yük dengeleyicide bir uygulama bağlantı noktasını açın. |
| [Gelen ağ güvenlik grubu kuralı oluşturma](./scripts/service-fabric-powershell-add-nsg-rule.md) | Belirli bir bağlantı noktası üzerindeki kümeye gelen trafiğe izin vermek için bir gelen ağ güvenlik grubu kuralı oluşturun. |
| **Uygulamaları yönetme** ||
| [Uygulama dağıtma](./scripts/service-fabric-powershell-deploy-application.md)| Kümeye bir uygulama dağıtın.|
| [Uygulama yükseltme](./scripts/service-fabric-powershell-upgrade-application.md)| Uygulamayı yükseltin.|
| [Uygulamayı kaldırma](./scripts/service-fabric-powershell-remove-application.md)| Kümeden bir uygulama kaldırın.|
