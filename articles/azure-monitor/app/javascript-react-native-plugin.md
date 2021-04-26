---
title: JavaScript SDK Application Insights için yerel yanıt verme eklentisi
description: Application Insights JavaScript SDK 'Sı için yerel yanıt verme eklentisini yükleyip kullanma.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 0c122a21fc7149e9943825cafbed77069b7919f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100593587"
---
# <a name="native-react-plugin-for-application-insights-javascript-sdk"></a>JavaScript SDK Application Insights için yerel yanıt verme eklentisi

Application Insights JavaScript SDK 'Sı için yerel yanıt verme eklentisi cihaz bilgilerini toplar, varsayılan olarak bu eklenti otomatik olarak toplar:

- **Benzersiz CIHAZ kimliği** (yükleme kimliği olarak da bilinir.)
- **Cihaz modeli adı** (iPhone X, Samsung Galaxy katlama, Huawei P30 Pro vb.)
- **Cihaz türü** (örneğin, ahize, tablet vb.)

## <a name="requirements"></a>Gereksinimler

Bir sürüm >= 2.0.0 kullanmanız gerekir `@microsoft/applicationinsights-web` . Bu eklenti yalnızca, yanıt verme yerel uygulamalarında çalışır. Bu [, Fuar çerçevesini kullanan uygulamalarla](https://docs.expo.io/)çalışmaz, bu nedenle Işlem, yerel uygulama oluştur ile çalışmaz.

## <a name="getting-started"></a>Kullanmaya başlama

Yanıt verme [-yerel-cihaz-bilgi](https://www.npmjs.com/package/react-native-device-info) paketini yükleyip bağlayın. `react-native-device-info`Uygulamanızı kullanarak en son cihaz adlarını toplamak için paketi güncel tutun.

```zsh

npm install --save @microsoft/applicationinsights-react-native @microsoft/applicationinsights-web
npm install --save react-native-device-info
react-native link react-native-device-info

```

## <a name="initializing-the-plugin"></a>Eklenti başlatılıyor

Bu eklentiyi kullanmak için, eklentiyi oluşturmanız ve `extension` var olan Application Insights örneğiniz için bir olarak eklemeniz gerekir.

```typescript
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactNativePlugin } from '@microsoft/applicationinsights-react-native';

var RNPlugin = new ReactNativePlugin();
var appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [RNPlugin]
    }
});
appInsights.loadAppInsights();

```

## <a name="next-steps"></a>Sonraki adımlar

- JavaScript SDK 'Sı hakkında daha fazla bilgi edinmek için [Application Insights JAVASCRIPT SDK belgelerine](javascript.md)bakın.
- Kusto sorgu dili hakkında bilgi edinmek ve Log Analytics verileri sorgulamak için [günlük sorgusuna genel bakış](../../azure-monitor/logs/log-query-overview.md)bölümüne bakın.
