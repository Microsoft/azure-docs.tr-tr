---
title: Anahtar-değer çiftlerini bir noktadan zaman al
titleSuffix: Azure App Configuration
description: Azure Uygulama yapılandırmasında, anahtar değerlerinde yapılan değişikliklerin kaydını tutan zaman içindeki noktadan noktaya görüntüleri kullanarak eski anahtar-değer çiftlerini alın.
services: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 08/05/2020
ms.openlocfilehash: fa2dbb11b3b8b9afd90c7f6fe3ffe77e2e57c4e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96929906"
---
# <a name="point-in-time-snapshot"></a>Belirli bir noktanın anlık görüntüsü

Azure Uygulama yapılandırması, anahtar değerlerinde yapılan değişikliklerin kaydını tutar. Bu kayıt, anahtar-değer değişikliklerinin zaman çizelgesini sağlar. Herhangi bir anahtar-değer geçmişini yeniden oluşturabilir ve geçmiş değerini anahtar geçmişi dönemi içinde herhangi bir anda (ücretsiz katman depoları için 7 gün veya Standart katman depoları için 30 gün) sağlayabilirsiniz. Bu özelliği kullanarak geri "zaman gezme" ve eski bir anahtar değeri alabilirsiniz. Örneğin, uygulamayı önceki yapılandırmaya geri almak için en son dağıtımdan önce kullanılan yapılandırma ayarlarını kurtarabilirsiniz.

## <a name="key-value-retrieval"></a>Anahtar-değer alma

Azure portal veya CLı 'yi, geçmiş anahtar değerlerini almak için kullanabilirsiniz. Azure CLı 'de, `az appconfig revision list` gerekli değerleri almak için uygun parametreler ekleyerek öğesini kullanın.  Mağaza adını ( `--name <app-config-store-name>` ) ya da bir bağlantı dizesini () kullanarak Azure uygulama yapılandırma örneğini belirtin `--connection-string <your-connection-string>` . Zaman içinde belirli bir nokta ( `--datetime` ) belirterek ve döndürülecek en fazla öğe sayısını () belirterek çıktıyı kısıtlayın `--top` .

Azure CLı yerel olarak yüklü değilse, isteğe bağlı olarak [Azure Cloud Shell](../cloud-shell/overview.md)kullanabilirsiniz.

Anahtar değerinizdeki tüm kayıtlı değişiklikleri alın.

```azurecli
az appconfig revision list --name <your-app-config-store-name>.
```

Anahtar ve Etiketler için kaydedilen tüm değişiklikleri alın `environment` `test` `prod` .

```azurecli
az appconfig revision list --name <your-app-config-store-name> --key environment --label test,prod
```

Hiyerarşik anahtar alanındaki tüm kayıtlı değişiklikleri alın `environment:prod` .

```azurecli
az appconfig revision list --name <your-app-config-store-name> --key environment:prod:* 
```

Belirli bir noktadaki anahtar için kaydedilen tüm değişiklikleri alın `color` .

```azurecli
az appconfig revision list --connection-string <your-app-config-connection-string> --key color --datetime "2019-05-01T11:24:12Z" 
```

Anahtar değerleriniz için kaydedilen son 10 değişikliği alın ve yalnızca `key` ,, `label` ve `last_modified` zaman damgası değerlerini döndürün.

```azurecli-interactive
az appconfig revision list --name <your-app-config-store-name> --top 10 --fields key label last_modified
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [ASP.NET Core web uygulaması oluşturma](./quickstart-aspnet-core-app.md)