---
author: IEvangelist
ms.author: dapine
ms.date: 10/02/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: a6f399e19cadf3d6ce9edfaecb3d904e62c498aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96006891"
---
Kapsayıcı aşağıdaki yapılandırma ayarlarına sahiptir:

|Gerekli|Ayar|Amaç|
|--|--|--|
|Yes|[ApiKey](#apikey-configuration-setting)|Fatura bilgilerini izler.|
|No|[ApplicationInsights](#applicationinsights-setting)|, Kapsayıcınıza [Azure Application Insights](/azure/application-insights) telemetri desteğinin eklenmesini sağlar.|
|Yes|[Faturalandırma](#billing-configuration-setting)|Azure üzerindeki hizmet kaynağının uç nokta URI 'sini belirtir.|
|Yes|[Sözleşmesi](#eula-setting)| Kapsayıcının lisansını kabul ettiğinizi gösterir.|
|No|[Fluentd](#fluentd-settings)|Günlük ve isteğe bağlı olarak ölçüm verilerini Floentd sunucusuna yazar.|
|No|HTTP proxy 'Si|Giden istekler oluşturmak için bir HTTP proxy yapılandırır.|
|No|[Günlüğe kaydetme](#logging-settings)|, Kapsayıcınız için ASP.NET Core günlük desteği sağlar. |
|No|[Kullan](#mount-settings)|Ana bilgisayardan kapsayıcıya ve kapsayıcıdan ana bilgisayara geri veri okur ve yazar.|