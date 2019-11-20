---
title: Modern Okuyucu SDK başvurusu
titleSuffix: Azure Cognitive Services
description: Modern Okuyucu SDK 'Sı, tam ekran okuyucuyu Web uygulamanızla tümleştirmenize olanak tanıyan bir JavaScript kitaplığıdır.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 09244b634fa2603a7dc92af3c78d171f8d6bd9df
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903104"
---
# <a name="immersive-reader-sdk-reference-guide"></a>Modern Okuyucu SDK 'Sı başvuru kılavuzu

Modern Okuyucu SDK 'Sı, tam ekran okuyucuyu Web uygulamanızla tümleştirmenize olanak tanıyan bir JavaScript kitaplığıdır.

## <a name="functions"></a>İşlevler

SDK işlevleri kullanıma sunar:

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

## <a name="launchasync"></a>launchAsync

Web uygulamanızdaki bir `iframe` içinde tam ekran okuyucu başlatır.

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<HTMLDivElement>;
```

### <a name="parameters"></a>Parametreler

| Ad | Tür | Açıklama |
| ---- | ---- |------------ |
| `token` | string | Azure AD kimlik doğrulama belirteci. Bkz. [Azure AD kimlik doğrulaması nasıl yapılır](./azure-active-directory-authentication.md). |
| `subdomain` | string | Azure 'daki tam ekran okuyucu kaynağınızın özel alt etki alanı. Bkz. [Azure AD kimlik doğrulaması nasıl yapılır](./azure-active-directory-authentication.md). |
| `content` | [İçerik](#content) | Tam ekran okuyucu 'da gösterilecek içeriği içeren nesne. |
| `options` | [Seçenekler](#options) | Modern okuyucunun belirli davranışlarını yapılandırmaya yönelik seçenekler. İsteğe bağlı. |

### <a name="returns"></a>Döndürdüğü

Derinlikli okuyucu yüklendiğinde çözümlenen bir `Promise<HTMLDivElement>`döndürür. `Promise`, yalnızca alt öğesi, tam ekran okuyucu sayfasını içeren bir `iframe` öğesi olan bir `div` öğesine çözümlenir.

### <a name="exceptions"></a>Özel Durumlar

Tam ekran okuyucu yüklenemezse, döndürülen `Promise` bir [`Error`](#error) nesnesiyle reddedilir. Daha fazla bilgi için bkz. [hata kodları](#error-codes).

## <a name="close"></a>kapat

Tam ekran okuyucuyu kapatır.

Bu işlev için bir örnek kullanım örneği, [Seçenekler](#options)içinde ```hideExitButton: true``` ayarlanarak çıkış düğmesinin gizlenmesidir. Ardından, farklı bir düğme (örneğin, bir mobil üstbilginin geri oku) tıklandığında bu ```close``` işlevi çağırabilir.

```typescript
close(): void;
```

## <a name="renderbuttons"></a>renderButtons

Bu işlev, belgenin tam ekran okuyucusu düğme öğelerini stiller ve güncelleştirir. ```options.elements``` sağlanmışsa, bu işlev ```options.elements```içindeki düğmeleri işleyebilir. Aksi halde, düğmeler, belgenin ```immersive-reader-button```sınıfına sahip olan öğeleri içinde işlenir.

Bu işlev, pencere yüklendiğinde SDK tarafından otomatik olarak çağırılır.

Daha fazla işleme seçeneği için bkz. [Isteğe bağlı öznitelikler](#optional-attributes) .

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

### <a name="parameters"></a>Parametreler

| Ad | Tür | Açıklama |
| ---- | ---- |------------ |
| `options` | [RenderButtonsOptions](#renderbuttonsoptions) | RenderButtons işlevinin belirli davranışlarını yapılandırmaya yönelik seçenekler. İsteğe bağlı. |

## <a name="types"></a>Türler

### <a name="content"></a>İçerik

Tam ekran okuyucu 'da gösterilecek içeriği içerir.

```typescript
{
    title?: string;    // Title text shown at the top of the Immersive Reader (optional)
    chunks: Chunk[];   // Array of chunks
}
```

### <a name="chunk"></a>Öbek

Tam ekran okuyucu Içeriğine geçirilecek tek bir veri öbeği.

```typescript
{
    content: string;        // Plain text string
    lang?: string;          // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
    mimeType?: string;      // MIME type of the content (optional). Currently 'text/plain', 'application/mathml+xml', and 'text/html' are supported. Defaults to 'text/plain' if not specified.
}
```

#### <a name="supported-mime-types"></a>Desteklenen MIME türleri

| MIME türü | Açıklama |
| --------- | ----------- |
| metin/düz | Düz metin. |
| metin/html | HTML içeriği. [Daha fazla bilgi edinin](#html-support)|
| Application/MathML + XML | Matematik biçimlendirme dili (MathML). [Daha fazla bilgi edinin](https://developer.mozilla.org/en-US/docs/Web/MathML).
| application/vnd. openxmlformats-officedocument. WordprocessingML. Document | Microsoft Word. docx biçim belgesi.

### <a name="html-support"></a>HTML desteği
| HTML | Desteklenen Içerik |
| --------- | ----------- |
| Yazı tipi stilleri | Kalın, Italik, altı çizili, kod, üstü çizili, üst simge, alt simge |
| Sırasız listeler | Disk, daire, kare |
| Sıralı listeler | Ondalık, büyük Alfa, alt Alfa, büyük Latin, alt roman |
| Lerinin | Çok Yakında |

Desteklenmeyen Etiketler comparably işlenecek. Görüntüler ve tablolar şu anda desteklenmiyor.

### <a name="options"></a>Seçenekler

Modern okuyucunun belirli davranışlarını yapılandıran özellikler içerir.

```typescript
{
    uiLang?: string;           // Language of the UI, e.g. en, es-ES (optional). Defaults to browser language if not specified.
    timeout?: number;          // Duration (in milliseconds) before launchAsync fails with a timeout error (default is 15000 ms).
    uiZIndex?: number;         // Z-index of the iframe that will be created (default is 1000)
    useWebview?: boolean;      // Use a webview tag instead of an iframe, for compatibility with Chrome Apps (default is false).
    onExit?: () => any;        // Executes when the Immersive Reader exits
    customDomain?: string;     // Reserved for internal use. Custom domain where the Immersive Reader webapp is hosted (default is null).
    allowFullscreen?: boolean; // The ability to toggle fullscreen (default is true).
    hideExitButton?: boolean;  // Whether or not to hide the Immersive Reader's exit button arrow (default is false). This should only be true if there is an alternative mechanism provided to exit the Immersive Reader (e.g a mobile toolbar's back arrow).
}
```

### <a name="renderbuttonsoptions"></a>RenderButtonsOptions

Tam ekran okuyucu düğmelerini işleme seçenekleri.

```typescript
{
    elements: HTMLDivElement[];    // Elements to render the Immersive Reader buttons in
}
```

### <a name="error"></a>Hata

Hata hakkındaki bilgileri içerir.

```typescript
{
    code: string;    // One of a set of error codes
    message: string; // Human-readable representation of the error
}
```

#### <a name="error-codes"></a>Hata kodları

| Kod | Açıklama |
| ---- | ----------- |
| BadArgument | Sağlanan bağımsız değişken geçersiz, Ayrıntılar için bkz. `message`. |
| Aş | Tam ekran okuyucusu belirtilen zaman aşımı süresi içinde yüklenemedi. |
| Tokenaşımına uğradı | Sağlanan belirtecin geçerliliği zaman aşımına uğradı. |
| Sürecek | Çağrı hızı sınırı aşıldı. |

## <a name="launching-the-immersive-reader"></a>Modern okuyucu başlatılıyor

SDK, tam ekran okuyucuyu başlatmaya yönelik düğme için varsayılan stil sağlar. Bu stillendirme özelliğini etkinleştirmek için `immersive-reader-button` Class özniteliğini kullanın.

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>İsteğe bağlı öznitelikler

Düğmenin genel görünümünü yapılandırmak için aşağıdaki öznitelikleri kullanın.

| Öznitelik | Açıklama |
| --------- | ----------- |
| `data-button-style` | Düğmenin stilini ayarlar. `icon`, `text`veya `iconAndText`olabilir. Varsayılan olarak `icon`olur. |
| `data-locale` | Yerel ayarı ayarlar. Örneğin, `en-US` veya `fr-FR`. Varsayılan olarak Ingilizce `en`. |
| `data-icon-px-size` | Simgenin boyutunu piksel cinsinden ayarlar. Varsayılan değer 20 px olur. |

## <a name="browser-support"></a>Tarayıcı desteği

Tam ekran okuyucu ile en iyi deneyim için aşağıdaki tarayıcıların en son sürümlerini kullanın.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>Sonraki adımlar

* [GitHub 'Da modern Okuyucu SDK 'sını](https://github.com/microsoft/immersive-reader-sdk) keşfet
* [Hızlı başlangıç: tam ekran okuyucuyu (C#) başlatan bir Web uygulaması oluşturma](./quickstart.md)
