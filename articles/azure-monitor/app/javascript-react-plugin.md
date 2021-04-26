---
title: JavaScript SDK 'Sı için Application Insights eklentisi
description: Application Insights JavaScript SDK 'Sı için tepki verme eklentisini yükleyip kullanma.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 8b24ee9f620b50056ceb466ef5bdc38da97682a9
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490752"
---
# <a name="react-plugin-for-application-insights-javascript-sdk"></a>JavaScript SDK 'Sı için Application Insights eklentisi

Application Insights JavaScript SDK 'Sı için bir eklentisine yanıt verin, şunları sunar:

- Rota değişikliklerinin izlenmesi
- BT bileşenleri kullanım istatistiklerini tepki verme

## <a name="getting-started"></a>Kullanmaya başlama

NPM paketini Install:

```bash

npm install @microsoft/applicationinsights-react-js

```

## <a name="basic-usage"></a>Temel kullanım

Application Insights bir bağlantı başlatın:

```javascript
// AppInsights.js
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactPlugin } from '@microsoft/applicationinsights-react-js';
import { createBrowserHistory } from 'history';

const browserHistory = createBrowserHistory({ basename: '' });
const reactPlugin = new ReactPlugin();
const appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [reactPlugin],
        extensionConfig: {
          [reactPlugin.identifier]: { history: browserHistory }
        }
    }
});
appInsights.loadAppInsights();
export { reactPlugin, appInsights };
```

Üzerinde Application Insights etkinleştirmek için bileşeninizi daha yüksek sıralı bileşen işleviyle sarın:

```javascript
import React from 'react';
import { withAITracking } from '@microsoft/applicationinsights-react-js';
import { reactPlugin, appInsights } from './AppInsights';

// To instrument various React components usage tracking, apply the `withAITracking` higher-order
// component function.

class MyComponent extends React.Component {
    ...
}

// withAITracking takes 4 parameters ( reactPlugin, Component, ComponentName, className) 
// the first two are required and the other two are optional.

export default withAITracking(reactPlugin, MyComponent);
```

## <a name="configuration"></a>Yapılandırma

| Name    | Varsayılan | Açıklama                                                                                                    |
|---------|---------|----------------------------------------------------------------------------------------------------------------|
| geçmiş | null    | Yönlendirici geçmişini tepki verin. Daha fazla bilgi için bkz. [tepki verme yönlendiricisi paketi belgeleri](https://reactrouter.com/web/api/history). Geçmiş nesnesine bileşenler dışında nasıl erişebileceğinizi öğrenmek için bkz. [tepki verme yönlendiricisi hakkında SSS](https://github.com/ReactTraining/react-router/blob/master/FAQ.md#how-do-i-access-the-history-object-outside-of-components)    |

### <a name="react-components-usage-tracking"></a>BT bileşenleri kullanım izlemeyi tepki verme

Çeşitli tepki verme bileşenleri kullanım izlemesini işaretlemek için, `withAITracking` daha yüksek sıralı bileşen işlevini uygulayın.

Olay aracılığıyla olaydan geçen süreyi ölçecek `ComponentDidMount` `ComponentWillUnmount` . Ancak, bu işlemi daha doğru yapmak için kullanıcının boşta olduğu süreyi çıkarır `React Component Engaged Time = ComponentWillUnmount timestamp - ComponentDidMount timestamp - idle time` .

Bu ölçümü Azure portal görmek için Application Insights kaynağına gitmeniz, "ölçümler" sekmesini seçmeniz ve boş grafikleri "özel ölçüm adı ile bir uygulama süresi (saniye)" öğesini görüntüleyecek şekilde yapılandırmak için, ölçümünüzün toplama (Sum, Ort, vb.) öğesini seçin ve bölme Uygula "bileşen adı" ' nı seçin.

![Özel ölçüyü görüntüleyen grafiğin ekran görüntüsü "bileşen ile bağlı süre (saniye)", "bileşen adı" olarak bölünür](./media/javascript-react-plugin/chart.png)

Ayrıca, gereksinimlerinize göre rapor ve görselleştirmeler oluşturmak için Application Insights verileri bölmek üzere özel sorgular çalıştırabilirsiniz. Azure portal Application Insights kaynağına gidin, genel bakış sekmesinin üst menüsünden "analiz" öğesini seçin ve sorgunuzu çalıştırın.

![Özel Ölçüm sorgu sonuçlarının ekran görüntüsü.](./media/javascript-react-plugin/query.png)

> [!NOTE]
> Yeni özel ölçümlerin Azure portalında görünmesi 10 dakikaya kadar sürebilir.

## <a name="using-react-hooks"></a>Tepki kancalarını kullanma

Yanıt verme [kancaları](https://reactjs.org/docs/hooks-reference.html) , sınıf tabanlı tepki verme bileşenlerine bağlı olmadan bir tepki verme uygulamasındaki durum ve yaşam döngüsü yönetimine yaklaşımlar. Application Insights tepki veren eklentisi, daha yüksek sıralı bileşen yaklaşımına benzer bir şekilde çalışan çok sayıda kanca tümleştirmelerini sağlar.

### <a name="using-react-context"></a>Tepki verme bağlamı kullanma

Application Insights 'e yönelik yanıt verme kancaları, bunun için bir içeren bir en boy [bağlamı](https://reactjs.org/docs/context.html) kullanmak üzere tasarlanmıştır. Bağlam kullanmak için Application Insights yukarıda olarak başlatın ve bağlam nesnesini içeri aktarın:

```javascript
import React from "react";
import { AppInsightsContext } from "@microsoft/applicationinsights-react-js";
import { reactPlugin } from "./AppInsights";

const App = () => {
    return (
        <AppInsightsContext.Provider value={reactPlugin}>
            /* your application here */
        </AppInsightsContext.Provider>
    );
};
```

Bu bağlam sağlayıcısı `useContext` , Application Insights tüm alt bileşenleri içindeki bir kanca olarak kullanılabilir hale getirir.

```javascript
import React from "react";
import { useAppInsightsContext } from "@microsoft/applicationinsights-react-js";

const MyComponent = () => {
    const appInsights = useAppInsightsContext();
    
    appInsights.trackMetric("Component 'MyComponent' is in use");
    
    return (
        <h1>My Component</h1>
    );
}
export default MyComponent;
```

### `useTrackMetric`

`useTrackMetric`Kanca, `withAITracking` bileşen yapısına ek bir bileşen eklemeden daha yüksek sıralı bileşenin işlevselliğini çoğaltır. Kanca iki bağımsız değişken alır, ilk olarak Application Insights örneğidir (kanca içinden elde edilebilir `useAppInsightsContext` ) ve izleme bileşeni için bir tanımlayıcı (örneğin adı).

```javascript
import React from "react";
import { useAppInsightsContext, useTrackMetric } from "@microsoft/applicationinsights-react-js";

const MyComponent = () => {
    const appInsights = useAppInsightsContext();
    const trackComponent = useTrackMetric(appInsights, "MyComponent");
    
    return (
        <h1 onHover={trackComponent} onClick={trackComponent}>My Component</h1>
    );
}
export default MyComponent;
```

Daha yüksek sıralı bileşen gibi çalışır, ancak bir bileşen yaşam döngüsü yerine kancalar yaşam döngüsü olaylarına yanıt verir. Belirli etkileşimler üzerinde çalışması gereken bir sorun varsa, kanca Kullanıcı olaylarına açıkça sağlanması gerekir.

### `useTrackEvent`

Kanca, bir `useTrackEvent` uygulamanın izlenmesi gerekebilecek, düğme tıklama veya DIĞER API çağrısı gibi özel olayları izlemek için kullanılır. Dört bağımsız değişken alır:
-   Application Insights örneği (üstünden elde edilebilir `useAppInsightsContext` ).
-   Olayın adı.
-   İzlenmesi gereken değişiklikleri kapsülleyen olay veri nesnesi.
-   başlatma sırasında çağrıyı çağırmayı atlamak için skipFirstRun (isteğe bağlı) bayrağı `trackEvent` . Varsayılan değer `true` olarak ayarlanmıştır.

```javascript
import React, { useState, useEffect } from "react";
import { useAppInsightsContext, useTrackEvent } from "@microsoft/applicationinsights-react-js";

const MyComponent = () => {
    const appInsights = useAppInsightsContext();
    const [cart, setCart] = useState([]);
    const trackCheckout = useTrackEvent(appInsights, "Checkout", cart);
    const trackCartUpdate = useTrackEvent(appInsights, "Cart Updated", cart);
    useEffect(() => {
        trackCartUpdate({ cartCount: cart.length });
    }, [cart]);
    
    const performCheckout = () => {
        trackCheckout();
        // submit data
    };
    
    return (
        <div>
            <ul>
                <li>Product 1 <button onClick={() => setCart([...cart, "Product 1"])}>Add to Cart</button></li>
                <li>Product 2 <button onClick={() => setCart([...cart, "Product 2"])}>Add to Cart</button></li>
                <li>Product 3 <button onClick={() => setCart([...cart, "Product 3"])}>Add to Cart</button></li>
                <li>Product 4 <button onClick={() => setCart([...cart, "Product 4"])}>Add to Cart</button></li>
            </ul>
            <button onClick={performCheckout}>Checkout</button>
        </div>
    );
}

export default MyComponent;
```

Kanca kullanıldığında, Application Insights ' de depolandığında olaya ek veri eklemek için bir veri yükü sağlayabilirsiniz.

## <a name="react-error-boundaries"></a>Hata sınırlarına tepki verme

[Hata sınırları](https://reactjs.org/docs/error-boundaries.html) , yanıt veren bir uygulama içinde oluştuğunda bir özel durumu düzgün bir şekilde işlemek için bir yol sağlar ve bu tür bir hata oluştuğunda özel durumun günlüğe alınması gerekir. Application Insights için tepki sağlayan eklenti, hata oluştuğunda otomatik olarak günlüğe kaydedilecek bir hata sınır bileşeni sağlar.

```javascript
import React from "react";
import { reactPlugin } from "./AppInsights";
import { AppInsightsErrorBoundary } from "@microsoft/applicationinsights-react-js";

const App = () => {
    return (
        <AppInsightsErrorBoundary onError={() => <h1>I believe something went wrong</h1>} appInsights={reactPlugin}>
            /* app here */
        </AppInsightsErrorBoundary>
    );
};
```

, `AppInsightsErrorBoundary` Kendisine iki props geçirilmesini gerektirir, `ReactPlugin` uygulama için oluşturulan örnek ve bir hata oluştuğunda işlenecek bir bileşen. İşlenmemiş bir hata oluştuğunda, `trackException` hata sınırına verilen bilgilerle çağrılır ve `onError` bileşen görüntülenir.

## <a name="sample-app"></a>Örnek uygulama

[Application Insights tepki verme tanıtımı](https://github.com/Azure-Samples/application-insights-react-demo)' ne göz atın.

## <a name="next-steps"></a>Sonraki adımlar

- JavaScript SDK 'Sı hakkında daha fazla bilgi edinmek için [Application Insights JAVASCRIPT SDK belgelerine](javascript.md)bakın.
- Kusto sorgu dili hakkında bilgi edinmek ve Log Analytics verileri sorgulamak için [günlük sorgusuna genel bakış](../../azure-monitor/logs/log-query-overview.md)bölümüne bakın.
