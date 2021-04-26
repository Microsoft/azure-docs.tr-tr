---
title: JavaScript SDK Application Insights için angular eklentisi
description: Application Insights JavaScript SDK 'Sı için angular eklentisini yükleyip kullanma.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: lagayhar
ms.openlocfilehash: 7ac83d0c43026b431370fab1d8c49aec1adf6659
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312731"
---
# <a name="angular-plugin-for-application-insights-javascript-sdk"></a>JavaScript SDK Application Insights için angular eklentisi

Application Insights JavaScript SDK 'Sı için angular eklentisi şunları sunar:

- Yönlendirici değişikliklerinin izlenmesi
- Yakalanamayan özel durumları izleme

> [!WARNING]
> Angular eklentisi ECMAScript 3 (ES3) uyumlu DEĞILDIR.

## <a name="getting-started"></a>Kullanmaya başlama

NPM paketini Install:

```bash
npm install @microsoft/applicationinsights-angularplugin-js
```

## <a name="basic-usage"></a>Temel kullanım

Uygulamanızdaki giriş bileşeninde bir Application Insights örneği ayarlayın:

```js
import { Component } from '@angular/core';
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { AngularPlugin } from '@microsoft/applicationinsights-angularplugin-js';
import { Router } from '@angular/router';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
    constructor(
        private router: Router
    ){
        var angularPlugin = new AngularPlugin();
        const appInsights = new ApplicationInsights({ config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [angularPlugin],
        extensionConfig: {
            [angularPlugin.identifier]: { router: this.router }
        }
        } });
        appInsights.loadAppInsights();
    }
}
```

Yakalanamayan özel durumları izlemek için `app.module.ts` :

```js
import { ApplicationinsightsAngularpluginErrorService } from '@microsoft/applicationinsights-angularplugin-js';

@NgModule({
  ...
  providers: [
    {
      provide: ErrorHandler,
      useClass: ApplicationinsightsAngularpluginErrorService
    }
  ]
  ...
})
export class AppModule { }
```

## <a name="next-steps"></a>Sonraki adımlar

- JavaScript SDK 'Sı hakkında daha fazla bilgi edinmek için [Application Insights JAVASCRIPT SDK belgelerine](javascript.md) bakın.
- [GitHub 'da angular eklentisi](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-angularplugin-js)
