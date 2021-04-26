---
title: Tek başına kümeyi Temizleme
description: Bu öğreticide, tek başına Service Fabric kümeniz için AWS veya Azure kaynaklarını silmeyi öğrenin.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: 0d46e9068a311594f779411c3ccee2b408febb3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91842895"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Öğretici: Tek başına kümenizi temizleme

Tek başına kümeler Service Fabric, size Service Fabric barındırmak için kendi ortamınızı seçme seçeneği sunar. Bu öğretici serisinde, AWS veya Azure 'da barındırılan tek başına bir küme oluşturacak ve bu uygulamaya bir uygulama dağıtırsınız.

Bu öğretici, bir serinin dördüncü bölümüdür. Öğreticinin bu bölümünde, Service Fabric kümenizi barındırmak için oluşturduğunuz AWS veya Azure kaynaklarının nasıl silineceği gösterilir.

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Service Fabric kümeyi kaldırma
> * AWS veya Azure kaynaklarınızı silme

## <a name="remove-a-service-fabric-cluster"></a>Service Fabric kümeyi kaldırma

1. Service Fabric yüklemek için kullandığınız sanal makineye RDP.
2. PowerShell’i açın.
3. Dizini, ikinci öğreticide ayıklanan klasöre geçirin.
4. Service Fabric kümesini kaldırmak için aşağıdaki komutu çalıştırın:

  ```powershell
  .\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
  ```

5. `Y`İstendiğinde girin. Başarılı olduysa, çıktılarınız aşağıdaki gibi görünür (kendi IP adreslerinizle birlikte):

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

## <a name="delete-aws-resources"></a>AWS kaynaklarını silme

1. AWS hesabınızda oturum açın.
2. EC2 Konsolu'na gidin.
3. Öğreticinin birinci bölümünde oluşturduğunuz üç düğümü seçin.
4. **Eylemler**  >  **örnek durumunu**  >  **Sonlandır**' ı seçin.

## <a name="delete-azure-resources"></a>Azure kaynaklarını silme

1. Azure portalında oturum açın.
2. **Sanal makineler** bölümüne gidin.
3. Öğreticinin bir parçası olarak oluşturduğunuz üç düğüm için onay kutularını seçin.
4. **Sil**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, önceki adımlarda oluşturduğunuz kaynakları silmeyi öğrendiniz.

> [!div class="checklist"]
> * Kaynaklarınızı temizleme

> [!div class="nextstepaction"]
> [Başa dön](service-fabric-tutorial-standalone-create-infrastructure.md)
