---
title: 'Öğretici: Service Fabric tek başına kümesini temizleme - Azure Service Fabric | Microsoft Docs'
description: Bu öğreticide, tek başına kümenizi nasıl temizleyeceğinizi öğrenirsiniz
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/22/2019
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: bebe3a2dc83b651e713ee80d7b11068b13096e04
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385167"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Öğretici: Tek başına kümenizi Temizleme

Service Fabric tek başına kümeleri, kendi ortamınızı seçme ve Service Fabric’in benimsediği "her işletim sistemi, her bulut" yaklaşımının bir parçası olarak bir küme oluşturma seçeneği sunar. Bu öğretici serisinde, AWS veya Azure üzerinde barındırılan bir tek başına küme oluşturur ve bu kümeye bir uygulama yüklersiniz.

Bu öğretici, bir serinin dördüncü bölümüdür. Öğreticinin bu bölümü, Service Fabric kümenizi barındırmak için oluşturduğunuz AWS veya Azure kaynaklarını nasıl temizleyekullanabileceğinizi gösterir.

Serinin dördüncü kısmında öğrenecekleriniz:

> [!div class="checklist"]
> * Service Fabric kümesini temizleme
> * AWS veya Azure kaynaklarınızı temizleyin

## <a name="clean-up-service-fabric-cluster"></a>Service Fabric kümesini temizleme

1. Service Fabric yüklemek için kullandığınız sanal makineye RDP.
2. PowerShell’i açın.
3. Dizini, ikinci öğreticide ayıklanan klasöre geçirin.
4. Service Fabric kümesini kaldırmak için aşağıdaki komutu çalıştırın:

  ```powershell
  .\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
  ```

5. `Y` İstendiğinde, Çıktınıza, kendi IP adreslerinizin yerini alarak aşağıdaki gibi görünür:

  ```powershell
  Best Practices Analyzer completed successfully.
  Removing configuration from machine 172.31.21.141
  Removing configuration from machine 172.31.27.1
  Removing configuration from machine 172.31.20.163
  Configuration removed from machine 172.31.21.141
  Configuration removed from machine 172.31.27.1
  Configuration removed from machine 172.31.20.163
  The cluster is successfully removed.
  ```

## <a name="clean-up-aws-resources"></a>AWS kaynaklarını temizleme

1. AWS hesabınızda oturum açın.
2. EC2 Konsolu'na gidin.
3. Öğreticinin birinci bölümünde oluşturduğunuz üç düğümü seçin.
4. **Eylemler** > **örneği**durumSonlandır > ' a tıklayın.

## <a name="clean-up-azure-resources"></a>Azure kaynaklarını Temizleme

1. Azure Portal’da oturum açın.
2. **Sanal makineler** bölümüne gidin.
3. Öğreticinin bir parçası olarak oluşturduğunuz üç düğüm için onay kutularını seçin.
4. **Sil**' e tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

Serinin dördüncü bölümünde, önceki adımlarda oluşturulan kaynaklarınızı nasıl temizleyeceğinizi öğrendiniz.

> [!div class="checklist"]
> * Kaynaklarınızı temizleme

> [!div class="nextstepaction"]
> [Başa dön](service-fabric-tutorial-standalone-create-infrastructure.md)
