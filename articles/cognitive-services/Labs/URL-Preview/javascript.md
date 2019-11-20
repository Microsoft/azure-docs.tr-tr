---
title: "Hızlı Başlangıç: Proje URL 'SI önizlemesi, JavaScript"
titlesuffix: Azure Cognitive Services
description: JavaScript ile Bing URL Önizleme API'sini kullanmaya hızlıca başlamak için örnek betik.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: quickstart
ms.date: 03/16/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: ec8a7ceef8cd938984b8eedc8397f6f67294c990
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706922"
---
# <a name="quickstart-url-preview-in-javascript"></a>Hızlı Başlangıç: JavaScript 'te URL önizlemesi 

Aşağıdaki tek sayfalı uygulamada JavaScript kullanılarak SwiftKey sitesinin URL Önizlemesi oluşturulmaktadır: https://swiftkey.com/en. 

## <a name="prerequisites"></a>Önkoşullar

[Bilişsel Hizmetler Laboratuvarları](https://labs.cognitive.microsoft.com/en-us/project-url-preview) ücretsiz denemesi için erişim anahtarı alın

## <a name="code-scenario"></a>Kod senaryosu
Aşağıdaki JavaScript örneğinde kullanıcının önizleme yapmak istediği URL'yi girdiği bir metin kutusu giriş nesnesi bulunmaktadır.  Kullanıcı **Preview** (Önizleme) düğmesine tıkladığında onclick metodu `getPreview` yönlendirmesiyle kodun **UrlPreview** uç noktasına bir Web isteği göndermesini sağlar.

Kod bir *XMLHttpRequest* oluşturur, *Ocp-Apim-Subscription-Key* üst bilgisini ve anahtarı ekler ve isteği gönderir.  Yanıtı işlemek için bir zaman uyumsuz olay ekler.

Yanıt başarıyla döndürülürse işleyici yanıtın JSON metnini sayfanın `demo` paragrafına atar. Diğer yanıt öğeleri görüntülenmek üzere aşağıdaki paragraflara ayarlanır.

**Ham JSON yanıtı**

```
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile typing",
  "url": "https://swiftkey.com/en",
  "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https://swiftkey.com/images/og/default.jpg"
  }
}

```

**Çalışan tanıtım**

![JavaScript URL Önizlemesi örneği](./media/java-script-demo.png)

## <a name="running-the-application"></a>Uygulamayı çalıştırma

Uygulamayı çalıştırmak için:

1. `YOUR-SUBSCRIPTION-KEY` değerini, aboneliğiniz için geçerli olan bir erişim anahtarı ile değiştirin.
2. HTML kodunu ve betiği .html uzantısıyla bir dosyaya kaydedin.
3. Web sayfasını bir tarayıcıda çalıştırın.
4. Var olan URL'yi kullanın veya metin kutusuna farklı bir URL girin.
5. **Preview** (Önizleme) düğmesine tıklayın.

**Kaynak kodu:**

```
<!DOCTYPE html>

<html lang="en" xmlns="http://www.w3.org/1999/xhtml">
<head>
    <meta charset="utf-8" />
    <title>urlPreview Demo</title>
</head>
<body>
    <h3>URL Preview Demo</h3>

    Page to preview: <input type="url" id="myURL" value="https://swiftkey.com/en">
    <button onclick="getPreview()">Preview</button>

    <p id="demo"></p>
    <br />
    <p id="jsonDesc"></p>
    <p><a id="familyFriendly"></a></p>
    <a id="contentUrl"></a>
    <p />
    <img id="jsonImage" />

    <script>

        var BING_ENDPOINT = "https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search"; 
        var key = "YOUR-SUBSCRIPTION-KEY";
        var xhr;

        function getPreview() {
            xhr = new XMLHttpRequest();

            var queryUrl = BING_ENDPOINT + "?q=" +
                encodeURIComponent(document.getElementById("myURL").value);
            xhr.open('GET', queryUrl, true);
            xhr.setRequestHeader("Ocp-Apim-Subscription-Key", key);

            xhr.send();

            xhr.addEventListener("readystatechange", processRequest, false);
        }

        function processRequest(e) {

            if (xhr.readyState == 4 && xhr.status == 200) {
                document.getElementById("demo").innerHTML = xhr.responseText;
                var obj = JSON.parse(xhr.responseText);
                document.getElementById("jsonDesc").innerHTML = obj.description;
                document.getElementById("familyFriendly").innerHTML = "Family Friendly: " + obj.isFamilyFriendly;
                document.getElementById("contentUrl").innerHTML = obj.url;
                document.getElementById("contentUrl").href = obj.url;
                document.getElementById("jsonImage").width = 350;
                document.getElementById("jsonImage").src = obj.primaryImageOfPage.contentUrl;

            }

        }

    </script>

</body>
</html>

```

## <a name="next-steps"></a>Sonraki adımlar
- [C# hızlı başlangıcı](csharp.md)
- [Java hızlı başlangıcı](java-quickstart.md)
- [Node hızlı başlangıcı](node-quickstart.md)
- [Python hızlı başlangıcı](python-quickstart.md)
