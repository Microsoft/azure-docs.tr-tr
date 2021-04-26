---
title: Azure Traffic Manager kaynak günlüğünü etkinleştirme
description: Traffic Manager profiliniz için kaynak günlüğünü etkinleştirmeyi ve sonuç olarak oluşturulan günlük dosyalarına erişmeyi öğrenin.
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2019
ms.author: duau
ms.openlocfilehash: 4cf3709574e2055f40759fd2d7026c93ac9db098
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102608028"
---
# <a name="enable-resource-logging-in-azure-traffic-manager"></a>Azure Traffic Manager kaynak günlüğünü etkinleştirme

Bu makalede, bir Traffic Manager profili için tanılama kaynak günlüklerinin toplanması ve günlük verilerinin nasıl etkinleştirileceği açıklanır.

Azure Traffic Manager kaynak günlükleri, Traffic Manager profili kaynağının davranışına ilişkin öngörüler sağlayabilir. Örneğin, tek tek yoklamaların bir uç noktada zaman aşımına uğradığını anlamak için profilin günlük verilerini kullanabilirsiniz.

## <a name="enable-resource-logging"></a>Kaynak günlüğünü etkinleştirme

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure Cloud Shell](https://shell.azure.com/powershell)izleyen komutları veya bilgisayarınızdan PowerShell 'i çalıştırarak çalıştırabilirsiniz. Azure Cloud Shell, ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. PowerShell 'i bilgisayarınızdan çalıştırırsanız, Azure PowerShell Module, 1.0.0 veya sonraki bir sürümü gerekir. `Get-Module -ListAvailable Az`Yüklü sürümü bulmak için ' i çalıştırabilirsiniz. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure PowerShell Modülü yükleme](/powershell/azure/install-az-ps). PowerShell 'i yerel olarak çalıştırıyorsanız `Login-AzAccount` Azure 'da oturum açmak için çalıştırmanız da gerekir.

1. **Traffic Manager profilini alın:**

    Kaynak günlüğünü etkinleştirmek için bir Traffic Manager profili KIMLIĞI gerekir. [Get-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile)ile kaynak günlüğünü etkinleştirmek istediğiniz Traffic Manager profilini alın. Çıktı, Traffic Manager profilinin KIMLIK bilgilerini içerir.

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name <TrafficManagerprofilename> -ResourceGroupName <resourcegroupname>
    ```

2. **Traffic Manager profili için kaynak günlüğünü etkinleştirin:**

    Önceki adımda [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting)ile elde edilen kimliği kullanarak Traffic Manager profili için kaynak günlüğünü etkinleştirin. Aşağıdaki komut Traffic Manager profili için ayrıntılı günlükleri belirtilen Azure depolama hesabına depolar. 

      ```azurepowershell-interactive
    Set-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId> -StorageAccountId <storageAccountId> -Enabled $true
      ``` 
3. **Tanılama ayarlarını doğrulayın:**

      [Get-AzDiagnosticSetting](/powershell/module/az.monitor/get-azdiagnosticsetting)kullanarak Traffic Manager profili için tanılama ayarlarını doğrulayın. Aşağıdaki komut, bir kaynak için günlüğe kaydedilen kategorileri görüntüler.

     ```azurepowershell-interactive
     Get-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId>
     ```  
      Traffic Manager profili kaynağıyla ilişkili tüm günlük kategorilerinin etkinleştirildiğinden emin olun. Ayrıca, depolama hesabının doğru şekilde ayarlandığını doğrulayın.

## <a name="access-log-files"></a>Günlük dosyalarına erişin
1. [Azure portalında](https://portal.azure.com) oturum açın. 
1. Portalda Azure depolama hesabınıza gidin.
2. Azure depolama hesabınızın **genel bakış** sayfasında, **Hizmetler** ' in altında **Bloblar**' ı seçin.
3. **Kapsayıcılar** için, **Öngörüler-logs-probehealthkara sevents**' i seçin ve dosyadaki PT1H.jsaşağı gidin ve bu günlük dosyasının bir kopyasını indirip kaydetmek için **İndir** ' e tıklayın.

    ![Traffic Manager profilinizin günlük dosyalarına blob depolamadan erişin](./media/traffic-manager-logs/traffic-manager-logs.png)


## <a name="traffic-manager-log-schema"></a>Traffic Manager log şeması

Azure Izleyici aracılığıyla kullanılabilen tüm kaynak günlükleri, her bir hizmetin kendi olayları için benzersiz özellikler yaymasını sağlayan ortak bir üst düzey şemayı paylaşır. Üst düzey kaynak günlükleri şeması için bkz. [Azure Kaynak günlükleri Için desteklenen hizmetler, şemalar ve Kategoriler](../azure-monitor/essentials/resource-logs-schema.md).

Aşağıdaki tabloda, Azure Traffic Manager profili kaynağına özgü Günlükler şeması yer almaktadır.

|Alan Adı|Alan Türü|Tanım|Örnek|
|----|----|---|---|
|Uçnoktaadı|Dize|Sistem durumu kaydedilirken Traffic Manager uç noktasının adı.|*myPrimaryEndpoint*|
|Durum|Dize|Araştırılan Traffic Manager uç noktasının sistem durumu. Durum **yukarı** veya **aşağı** olabilir.|**Yukarı**|
|||||

## <a name="next-steps"></a>Sonraki adımlar

* [Traffic Manager izleme](traffic-manager-monitoring.md) hakkında daha fazla bilgi edinin