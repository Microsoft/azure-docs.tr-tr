---
title: Azure CLı-Azure Time Series Insights Gen2 kullanarak Azure Time Series Insights Gen2 ortamı oluşturma | Microsoft Docs
description: Azure CLı kullanarak Azure Time Series Insights Gen2 'de ortam ayarlamayı öğrenin.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 07e7f21bd706d9f83d2813b0ab491b01fbc53672
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867574"
---
# <a name="create-an-azure-time-series-insights-gen2-environment-using-the-azure-cli"></a>Azure CLı kullanarak Azure Time Series Insights Gen2 ortamı oluşturma

Bu belge, yeni bir Time Series Insights Gen2 ortamı oluşturma konusunda size kılavuzluk eder.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Önkoşullar

* Ortamınızın [soğuk deposu](./concepts-storage.md#cold-store)Için bir Azure depolama hesabı oluşturun. Bu hesap, geçmiş veriler için uzun süreli saklama ve analiz için tasarlanmıştır.

> [!NOTE]
> Kodunuzda, `mytsicoldstore` soğuk depolama hesabınız için benzersiz bir adla değiştirin.

İlk olarak, depolama hesabı oluşturun:

```azurecli-interactive
storage=mytsicoldstore
rg=-my-resource-group-name
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv
```

## <a name="creating-the-environment"></a>Ortam oluşturma

Depolama hesabı oluşturulduğuna ve adı ve Yönetim anahtarı değişkenlere atandığına göre, Azure Time Series Insights ortamını oluşturmak için aşağıdaki komutu çalıştırın:

> [!NOTE]
> Kodunuzda, senaryonuza yönelik benzersiz adlarla aşağıdakini değiştirin:
>
> * `my-tsi-env` Ortam adınızla.
> * `my-ts-id-prop` Zaman serisi ID özelliğinin adıyla birlikte.

> [!IMPORTANT]
> Ortamınızın zaman serisi KIMLIĞI, bir veritabanı bölümü anahtarı gibidir. Zaman serisi KIMLIĞI, zaman serisi modelinize yönelik birincil anahtar olarak da davranır.
>
> Daha fazla bilgi için bkz [. bir zaman SERISI kimliği seçmek Için en iyi uygulamalar.](./how-to-select-tsid.md)

```azurecli-interactive
az tsi environment gen2 create --name "my-tsi-env" --location eastus2 --resource-group $rg --sku name="L1" capacity=1 --time-series-id-properties name=my-ts-id-prop type=String --warm-store-configuration data-retention=P7D --storage-configuration account-name=$storage management-key=$key
```

## <a name="remove-an-azure-time-series-insights-environment"></a>Azure Time Series Insights ortamını kaldırma

Time Series Insights ortamı gibi tek bir kaynağı silmek veya bir kaynak grubunu ve tüm Time Series Insights ortamları dahil olmak üzere tüm kaynaklarını silmek için Azure CLı 'yi kullanabilirsiniz.

[Time Series Insights ortamlarını silmek](/cli/azure/tsi/environment#az_tsi_environment_delete)için şu komutu çalıştırın:

```azurecli-interactive
az tsi environment delete --name "my-tsi-env" --resource-group $rg
```

[Depolama hesabını silmek](/cli/azure/storage/account#az_storage_account_delete)için şu komutu çalıştırın:

```azurecli-interactive
az storage account delete --name $storage --resource-group $rg
```

[Bir kaynak grubunu](/cli/azure/group#az_group_delete) ve tüm kaynaklarını silmek için aşağıdaki komutu çalıştırın:

```azurecli-interactive
az group delete --name $rg
```

## <a name="next-steps"></a>Sonraki adımlar

* Azure Time Series Insights Gen2 ortamınız için [akış alma olay kaynakları](./concepts-streaming-ingestion-event-sources.md) hakkında bilgi edinin.
* [IoT Hub](./how-to-ingest-data-iot-hub.md) nasıl bağlanacağınızı öğrenin
