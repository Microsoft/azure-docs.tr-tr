---
title: Geliştirici Portalında pencere öğeleri uygulama
titleSuffix: Azure API Management
description: Dış API 'lerden veri tüketen ve API Management Geliştirici Portalında görüntüleyen pencere öğelerinin nasıl uygulanacağını öğrenin.
author: dlepow
ms.author: apimpm
ms.date: 04/15/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: 4bda136a1abe8f9ffb443973731ebbe13b56ac3b
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741852"
---
# <a name="implement-widgets-in-the-developer-portal"></a>Geliştirici Portalında pencere öğeleri uygulama

Bu öğreticide, dış bir API 'den veri tüketen ve API Management Geliştirici Portalında görüntüleyen bir pencere öğesi uygulayacağınızı görürsünüz.

Pencere öğesi, örnek [konferans API](https://conferenceapi.azurewebsites.net/?format=json)'sindeki oturum açıklamalarını alacaktır. Oturum tanımlayıcısı, belirlenen pencere öğesi Düzenleyicisi aracılığıyla ayarlanır.

Geliştirme sürecinde size yardımcı olmak için, `examples` API Management geliştirici portalı [GitHub deposunun](https://github.com/Azure/api-management-developer-portal/)klasöründe bulunan tamamlanmış pencere öğesine bakın: `/examples/widgets/conference-session` .

:::image type="content" source="media/developer-portal-implement-widgets/widget-published.png" alt-text="Yayımlanan pencere öğesinin ekran görüntüsü":::

## <a name="prerequisites"></a>Önkoşullar

* Geliştirici portalının en son sürümü için [yerel bir ortam](developer-portal-self-host.md#step-1-set-up-local-environment) ayarlayın.

* [Paperbits pencere öğesi anatomisi öğesini](https://paperbits.io/wiki/widget-anatomy)anlamalısınız.


## <a name="copy-the-scaffold"></a>Yapı iskelesi Kopyala

`widget` `/scaffolds` Yeni pencere öğesini oluşturmak için başlangıç noktası olarak klasörden bir yapı iskelesi kullanın.

1. Klasörünü klasörüne kopyalayın `/scaffolds/widget` `/community/widgets` .
1. Klasörü olarak yeniden adlandırın `conference-session` .

## <a name="rename-exported-module-classes"></a>Aktarılmış modül sınıflarını yeniden adlandır

Bu dosyalardaki öneki değiştirerek, aktarılmış modül sınıflarını yeniden adlandırın `Widget` `ConferenceSession` :

- `widget.design.module.ts`

- `widget.publish.module.ts`

- `widget.runtime.module.ts`
    
Örneğin, `widget.design.module.ts` dosyasında, `WidgetDesignModule` olarak değiştirin `ConferenceSessionDesignModule` :
    
```typescript
export class WidgetDesignModule implements IInjectorModule {
```
kullanıcısı 
    
```typescript
export class ConferenceSessionDesignModule implements IInjectorModule {
```
    
   
## <a name="register-the-widget"></a>Pencere öğesini kaydet

İlgili dosyalara aşağıdaki satırları ekleyerek pencere öğesinin modüllerini portalın kök modüllerine kaydedin:

1. `src/apim.design.module.ts` -Tasarım zamanı bağımlılıklarını kaydeden bir modül.

   ```typescript
   import { ConferenceSessionDesignModule } from "../community/widgets/conference-session/widget.design.module";
   
   ...
   injector.bindModule(new ConferenceSessionDesignModule());
   ```
1. `src/apim.publish.module.ts` -Yayımlama zamanı bağımlılıklarını kaydeden bir modül.

   ```typescript
   import { ConferenceSessionPublishModule } from "../community/widgets/conference-session/widget.publish.module";

    ...

    injector.bindModule(new ConferenceSessionPublishModule());
    ```

1. `src/apim.runtime.module.ts` -çalışma zamanı bağımlılıkları.

    ```typescript
    import { ConferenceSessionRuntimeModule } from "../community/widgets/conference-session/widget.runtime.module";

    ...

    injector.bindModule(new ConferenceSessionRuntimeModule());
    ```

## <a name="place-the-widget-in-the-portal"></a>Pencere öğesini portala yerleştirme

Artık çoğaltılan scafkatlamayı takıp Geliştirici Portalında kullanabilirsiniz.

1. `npm start` komutunu çalıştırın.

1. Uygulama yüklendiğinde yeni pencere öğesini bir sayfaya yerleştirin. Bu adı, `Your widget` `Community` pencere öğesi seçicisindeki kategoride bulabilirsiniz.

    :::image type="content" source="media/developer-portal-implement-widgets/widget-selector.png" alt-text="Pencere öğesi seçicinin ekran görüntüsü":::

1. **CTRL** + **s** (veya  + MacOS üzerinde ⌘**S** ) tuşlarına basarak sayfayı kaydedin.

    > [!NOTE]
    > Tasarım zamanında, **CTRL** (veya **⌘**) tuşunu basılı tutarak Web sitesiyle etkileşim kurmaya devam edebilirsiniz.

## <a name="add-custom-properties"></a>Özel özellikler ekleme

Pencere öğesinin oturum açıklamalarını getirmesi için oturum tanımlayıcısının farkında olması gerekir. `Session ID`Özelliği ilgili arabirimlere ve sınıflara ekleyin:

Pencere öğesinin oturum açıklamasını getirmesi için oturum tanımlayıcısının farkında olması gerekir. Oturum KIMLIĞI özelliğini ilgili arabirimler ve sınıflar öğesine ekleyin:

1. `widgetContract.ts` -pencere öğesi yapılandırmasının nasıl kalıcı olduğunu tanımlayan veri anlaşması (veri katmanı).

    ```typescript
    export interface WidgetContract extends Contract {
        sessionNumber: string;
    }
    ```

1. `widgetModel.ts` -Model (iş katmanı)-sistemdeki pencere öğesinin birincil temsili. Bu, düzenleyiciler tarafından güncelleştirilir ve sunu katmanı tarafından işlenir.

    ```typescript
    export class WidgetModel {
        public sessionNumber: string;
    }
    ```

1. `ko/widgetViewModel.ts` -ViewModel (sunum katmanı)-geliştirici portalının HTML şablonuyla oluşturduğu bir UI çerçevesine özgü nesne.

    > [!NOTE]
    > Bu dosyadaki herhangi bir şeyi değiştirmeniz gerekmez.

## <a name="configure-binders"></a>Ciltleri yapılandırma

`sessionNumber`Veri kaynağından pencere öğesi sunumuna akışını etkinleştirin. `ModelBinder`Ve varlıklarını düzenleyin `ViewModelBinder` :

1. `widgetModelBinder.ts` Sözleşmede açıklanan verileri kullanarak modeli hazırlamaya yardımcı olur.

    ```typescript
    export class WidgetModelBinder implements IModelBinder<WidgetModel> {
        public async contractToModel(contract: WidgetContract): Promise<WidgetModel> {
            model.sessionNumber = contract.sessionNumber || "107"; // 107 is the default session id
            ...
        }
    
        public modelToContract(model: WidgetModel): Contract {
            const contract: WidgetContract = {
                sessionNumber: model.sessionNumber
                ...
            };
            ...
        }
    }
    ```

1. `ko/widgetViewModelBinder.ts` geliştirici portalının belirli bir kullanıcı arabirimi çerçevesinde modeli (ViewModel olarak) sunması gerektiğini bilir.

    ```typescript
    ...
    public async updateViewModel(model: WidgetModel, viewModel: WidgetViewModel): Promise<void> {
            viewModel.runtimeConfig(JSON.stringify({
                sessionNumber: model.sessionNumber
            }));
        }
    }
    ...
    ```

## <a name="adjust-design-time-widget-template"></a>Tasarım zamanı pencere öğesi şablonunu ayarla

Her bir kapsamın bileşenleri bağımsız olarak çalışır. Ayrı bağımlılık ekleme kapsayıcıları, kendi yapılandırması, yaşam döngüsü vb. vardır. Farklı kullanıcı arabirimi çerçeveleri (Bu örnekte, bunun altını gizleme JS) tarafından da güç olabilir.

Tasarım zamanı perspektifinden, tüm çalışma zamanı bileşenleri yalnızca belirli özniteliklere ve/veya içeriğe sahip bir HTML etiketidir. Gerekirse yapılandırma, düz işaretlerle geçirilir. Bu örnekte olduğu gibi basit durumlarda, parametresi özniteliğinde geçirilir. Yapılandırma daha karmaşıksa, belirlenen bir yapılandırma sağlayıcısı tarafından getirilen gerekli ayarların tanımlayıcısını kullanabilirsiniz (örneğin, `ISettingsProvider` ).

1. Dosyayı güncelleştirin `ko/widgetView.html` :

    ```html
    <widget-runtime data-bind="attr: { params: runtimeConfig }"></widget-runtime>
    ```

    Geliştirici portalı `attr` bağlamayı *Tasarım zamanı* veya *Yayımlama zamanında* çalıştırdığında, elde edilen HTML şu şekilde olur:

    ```html
    <widget-runtime params="{ sessionNumber: 107 }"></widget-runtime>
    ```

    Sonra, çalışma zamanında `widget-runtime` bileşen `sessionNumber` onu okuyup başlatma kodunda kullanır (aşağıya bakın).

1. `widgetHandlers.ts`Oluşturma sırasında oturum kimliğini atamak için dosyayı güncelleştirin:

    ```typescript
    ...
    createModel: async () => {
        var model = new ConferenceSessionModel();
        model.sessionNumber = "107";
            return model;
        }
    ...
    ```

## <a name="revise-runtime-view-model"></a>Çalışma zamanı görünüm modelini gözden geçir

Çalışma zamanı bileşenleri, Web sitesinin kendisinde çalışan koddur. Örneğin, API Management geliştirici portalında, dinamik bileşenlerin arkasındaki tüm betikler (örneğin, *API ayrıntıları*, *API konsolu*), kod örnek oluşturma, istek gönderme gibi işlemleri işleme, vb.

Çalışma Zamanı bileşeninizin görünüm modelinde aşağıdaki yöntem ve özelliklere sahip olması gerekir:

- `sessionNumber` `Param` Dışarıdan geçirilen (tasarım zamanında oluşturulan biçimlendirme) bir bileşen giriş parametresi olarak kullanılan Özellik (dekoratör ile işaretlenmiş). önceki adıma bakın.
- `sessionDescription`Özelliği pencere öğesi şablonuna bağımlıdır ( `widget-runtime.html` Bu makalenin devamındaki bölümüne bakın).
- `initialize` `OnMounted` Pencere öğesi oluşturulduktan sonra ve tüm parametreleri atandıktan sonra çağrılan Yöntem (dekoratör ile). `sessionNumber`Kullanarak API 'yi okumak ve çağırmak iyi bir yerdir `HttpClient` . , `HttpClient` IOC (denetimin INVERSION) kapsayıcısının sonuna eklenen bir bağımlılıkdır.

- İlk olarak, geliştirici portalı pencere öğesini oluşturur ve tüm parametrelerini atar. Ardından `initialize` yöntemini çağırır.

    ```typescript
    ...
    import * as ko from "knockout";
    import { Component, RuntimeComponent, OnMounted, OnDestroyed, Param } from "@paperbits/common/ko/decorators";
    import { HttpClient, HttpRequest } from "@paperbits/common/http";
    ...

    export class WidgetRuntime {
        public readonly sessionDescription: ko.Observable<string>;

        constructor(private readonly httpClient: HttpClient) {
            ...
            this.sessionNumber = ko.observable();
            this.sessionDescription = ko.observable();
            ...
        }

        @Param()
        public readonly sessionNumber: ko.Observable<string>;

        @OnMounted()
        public async initialize(): Promise<void> {
            ...
            const sessionNumber = this.sessionNumber();

            const request: HttpRequest = {
                url: `https://conferenceapi.azurewebsites.net/session/${sessionNumber}`,
                method: "GET"
            };

            const response = await this.httpClient.send<string>(request);
            const sessionDescription = response.toText();
    
            this.sessionDescription(sessionDescription);
            ...
        }
        ...
    }
    ```

## <a name="tweak-the-widget-template"></a>Pencere öğesi şablonunu ince ayar

Oturum açıklamasını göstermek için pencere öğesini güncelleştirin.

`markdown` `text` Açıklamayı işlemek için dosyada bir paragraf etiketi ve bir (veya) bağlama kullanın `ko/runtime/widget-runtime.html` :

```html
<p data-bind="markdown: sessionDescription"></p>
```

## <a name="add-the-widget-editor"></a>Pencere öğesi düzenleyicisini ekleme

Pencere öğesi artık oturumun açıklamasını getirecek şekilde yapılandırılmıştır `107` . `107`Kodda varsayılan oturum olarak belirtilir. Her şeyin doğru olduğunu kontrol etmek için, `npm start` Geliştirici Portalı 'nın sayfada açıklamayı gösterdiğini doğrulayın ve onaylayın.

Şimdi, kullanıcının bir pencere öğesi Düzenleyicisi aracılığıyla oturum KIMLIĞINI ayarlamaya izin vermek için bu adımları uygulayın:

1. Dosyayı güncelleştirin `ko/widgetEditorViewModel.ts` :

    ```typescript
    export class WidgetEditor implements WidgetEditor<WidgetModel> {
        public readonly sessionNumber: ko.Observable<string>;

        constructor() {
            this.sessionNumber = ko.observable();
        }

        @Param()
        public model: WidgetModel;

        @Event()
        public onChange: (model: WidgetModel) => void;

        @OnMounted()
        public async initialize(): Promise<void> {
            this.sessionNumber(this.model.sessionNumber);
            this.sessionNumber.subscribe(this.applyChanges);
        }

        private applyChanges(): void {
            this.model.sessionNumber = this.sessionNumber();
            this.onChange(this.model);
        }
    }
    ```

    Düzenleyici görünüm modeli, daha önce gördüğünüz yaklaşımı kullanır, ancak ile donatılmış yeni bir özellik vardır `onChange` `@Event()` . Modeldeki değişikliklere yönelik değişiklikleri (Bu durumda bir İçerik Düzenleyicisi) bilgilendirmek için geri aramayı bilgilendirir.

1. Dosyayı güncelleştirin `ko/widgetEditorView.html` :

    ```html
    <input type="text" class="form-control" data-bind="textInput: sessionNumber" />
    ```

1. `npm start` komutunu yeniden çalıştırın. `sessionNumber`Pencere öğesi Düzenleyicisi 'nde değişiklik yapabilmelisiniz. KIMLIĞI olarak değiştirin `108` , değişiklikleri kaydedin ve tarayıcının sekmesini yenileyin. Sorunlarla karşılaşıyorsanız, pencere öğesini sayfaya yeniden eklemeniz gerekebilir.

    :::image type="content" source="media/developer-portal-implement-widgets/widget-editor.png" alt-text="Pencere öğesi düzenleyicisinin ekran görüntüsü":::

## <a name="rename-the-widget"></a>Pencere öğesini yeniden adlandırma

Dosyadaki pencere öğesi adını değiştirin `constants.ts` :

```typescript
...
export const widgetName = "conference-session";
export const widgetDisplayName = "Conference session";
...
```

> [!NOTE]
> Pencere öğesine depoya katkıda bulunsanız, `widgetName` klasör adıyla aynı olması gerekir ve görünen ad (küçük harfler ve boşluklar, kısa çizgilerle değiştirilmelidir) ile aynı olmalıdır. Kategori kalmalıdır `Community` .

## <a name="next-steps"></a>Sonraki adımlar


Geliştirici portalı hakkında daha fazla bilgi edinin:

- [Azure API Management geliştirici portalına genel bakış](api-management-howto-developer-portal.md)

- [Pencere öğelerinin katkıda bulunma](developer-portal-widget-contribution-guidelines.md) -topluluk katkılarına hoş geldiniz ve teşvik ediyoruz.

- Topluluk tarafından katkıda bulunulan pencere öğelerini nasıl kullanacağınızı öğrenmek için bkz. [topluluk pencere öğelerini kullanma](developer-portal-use-community-widgets.md) .
