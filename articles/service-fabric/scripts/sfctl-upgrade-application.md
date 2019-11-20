---
title: Service Fabric CLI Betik Örneği - Bir küme üzerinde uygulama güncelleştirme
description: Service Fabric CLI Betik Örneği - Uygulamayı yeni bir sürümle güncelleştirme. Bu örnek ayrıca dağıtılmış bir uygulamayı yeni bitlerle yükseltir.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: ''
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 12/06/2017
ms.author: atsenthi
ms.custom: ''
ms.openlocfilehash: e925f4f7b4a3da2d298413feccc94ea742e47f6b
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69034714"
---
# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>Service Fabric kümesine uygulama sertifikası ekleme

Bu örnek betik, mevcut bir uygulamanın yeni bir sürümünü karşıya yükler ve sonra dağıtılmış bir uygulamayı yeni bitlerle yükseltir.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>Örnek betik

[!code-sh[main](../../../cli_scripts/service-fabric/upgrade-application/upgrade-application.sh "Upload and update an application on a Service Fabric cluster")]

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için bkz. [Service Fabric CLI belgeleri](../service-fabric-cli.md).

Azure Service Fabric’e yönelik ek Service Fabric CLI örnekleri [Service Fabric CLI örnekleri](../samples-cli.md)’nde bulunabilir.
