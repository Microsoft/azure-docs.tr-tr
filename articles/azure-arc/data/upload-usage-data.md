---
title: Kullanım verilerini Azure Izleyici 'ye yükleme
description: Kullanım Azure Arc etkin veri Hizmetleri verilerini Azure Izleyici 'ye yükleme
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: 0c72eda59f375c70274b17796ca53614ef95505b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "104669517"
---
# <a name="upload-usage-data-to-azure-monitor"></a>Kullanım verilerini Azure Izleyici 'ye yükleme

Kullanım bilgilerini düzenli aralıklarla dışarı aktarabilirsiniz. Bu bilgilerin dışarı ve karşıya yüklenmesi, Azure 'da veri denetleyicisi, SQL yönetilen örneği ve PostgreSQL hiper ölçek sunucu grubu kaynaklarını oluşturur ve güncelleştirir.

> [!NOTE] 
> Önizleme dönemi boyunca, Azure Arc etkin veri hizmetlerini kullanma maliyeti yoktur.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]


> [!NOTE]
> Kullanım verilerini karşıya yüklemeden önce Azure Arc veri denetleyicisini oluşturduktan sonra en az 24 saat bekleyin.

## <a name="create-service-principal-and-assign-roles"></a>Hizmet sorumlusu oluşturma ve rol atama

Devam etmeden önce, gerekli hizmet sorumlusunu oluşturup uygun bir role atadığınızdan emin olun. Ayrıntılar için bkz.
* [Hizmet sorumlusu oluşturun](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal).
* [Hizmet sorumlusuna roller atama](upload-metrics-and-logs-to-azure-monitor.md#assign-roles-to-the-service-principal)

## <a name="upload-usage-data"></a>Kullanım verilerini karşıya yükleme

Envanter ve kaynak kullanımı gibi kullanım bilgileri aşağıdaki iki adımlı şekilde Azure 'a yüklenebilir:

1. Veri denetleyicisinde oturum açın. Komut isteminde değerleri girin. 

   ```console
   azdata login
   ```

1. Aşağıdaki gibi kullanım verilerini komutunu kullanarak dışarı aktarın `azdata arc dc export` :

   ```console
   azdata arc dc export --type usage --path usage.json
   ```
 
   Bu komut `usage.json` , veri denetleyicisinde oluşturulan SQL yönetilen örnekler ve PostgreSQL hiper ölçek örnekleri vb. gibi tüm Azure Arc etkin veri kaynaklarını içeren bir dosya oluşturur.

2. Komutu kullanarak kullanım verilerini karşıya yükleme ```azdata upload```

   ```console
   azdata arc dc upload --path usage.json
   ```

## <a name="automating-uploads-optional"></a>Karşıya Yüklemeleri Otomatikleştirme (isteğe bağlı)

Ölçümleri ve günlükleri Zamanlanmış olarak yüklemek isterseniz, bir komut dosyası oluşturup birkaç dakikada bir Zamanlayıcı üzerinde çalıştırabilirsiniz. Bir Linux kabuğu betiği kullanarak karşıya yüklemeyi otomatikleştirme örneği aşağıda verilmiştir.

En sevdiğiniz metin/kod düzenleyicide, dosyaya aşağıdaki betiği ekleyin ve `.sh` (Linux/Mac) veya, ya da gibi bir betik yürütülebilir dosyası olarak kaydedin `.cmd` `.bat` `.ps1` .

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

Betik dosyasını çalıştırılabilir hale getirme

```console
chmod +x myuploadscript.sh
```

Betiği her 20 dakikada bir çalıştırın:

```console
watch -n 1200 ./myuploadscript.sh
```

Ayrıca, cron veya Windows Görev Zamanlayıcı gibi bir iş zamanlayıcısını ya da ansız, Pupevcil hayvan veya Chef gibi bir Orchestrator 'ı kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

[Ölçümleri ve günlükleri Azure Izleyici 'ye yükleme](upload-metrics.md)

[Günlükleri Azure Izleyici 'ye yükleme](upload-logs.md)

[Faturalama verilerini Azure 'a yükleyin ve Azure portal görüntüleyin](view-billing-data-in-azure.md)

[Azure Arc Data Controller kaynağını Azure portal görüntüle](view-data-controller-in-azure-portal.md)
