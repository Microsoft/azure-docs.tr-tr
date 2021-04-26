---
title: Azure Logic Apps ile tümleşen bir işlev oluşturma
description: Azure Logic Apps ve Azure bilişsel hizmetler ile tümleşen bir işlev oluşturun. Sonuçta elde edilen iş akışı, Tweet yaklaşımları e-posta bildirimleri gönderir.
author: craigshoemaker
ms.assetid: 60495cc5-1638-4bf0-8174-52786d227734
ms.topic: tutorial
ms.date: 04/10/2021
ms.author: cshoe
ms.custom: devx-track-csharp, mvc, cc996988-fb4f-47
ms.openlocfilehash: 3517835859de82117de07ad67cdf8027960ab777
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388685"
---
# <a name="tutorial-create-a-function-to-integrate-with-azure-logic-apps"></a>Öğretici: Azure Logic Apps ile tümleştirilecek bir işlev oluşturma

Azure İşlevleri, Logic Apps Tasarımcısı'nda Azure Logic Apps ile tümleşir. Bu tümleştirme, Işlevlerin bilgi işlem gücünü diğer Azure ve üçüncü taraf hizmetlerle birlikte kullanmanıza olanak sağlar.

Bu öğreticide, Twitter etkinliğini çözümlemek için bir iş akışı oluşturma gösterilmektedir. Kavalar değerlendirildiğinde, bu iş akışı, olumlu sentiler algılandığında bildirim gönderir.

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * Bir Bilişsel Hizmetler API Kaynağı oluşturun.
> * Tweet duyarlılığını kategorilere ayıran bir işlev oluşturun.
> * Twitter’a bağlanan bir mantıksal uygulama oluşturun.
> * Mantıksal uygulamaya duyarlılık algılama özelliğini ekleyin.
> * Mantıksal uygulamayı işleve bağlayın.
> * İşlevden alınan yanıta göre bir e-posta gönderin.

## <a name="prerequisites"></a>Önkoşullar

* Etkin bir [Twitter](https://twitter.com/) hesabı.
* Bir [Outlook.com](https://outlook.com/) hesabı (bildirim göndermek için).

> [!NOTE]
> Gmail bağlayıcısını kullanmak istiyorsanız, yalnızca G-Suite iş hesapları bu bağlayıcıyı mantıksal uygulamalarda kısıtlama olmadan kullanabilir. Gmail tüketicisi hesabınız varsa, Gmail bağlayıcısını yalnızca belirli Google-onaylanan uygulamalar ve hizmetlerle kullanabilir ya da [Gmail bağlayıcısında kimlik doğrulaması için kullanmak üzere bir Google istemci uygulaması oluşturabilirsiniz](/connectors/gmail/#authentication-and-bring-your-own-application). <br><br>Daha fazla bilgi için, bkz. [Azure Logic Apps Google bağlayıcıları Için veri güvenliği ve gizlilik ilkeleri](../connectors/connectors-google-data-security-privacy-policy.md).

## <a name="create-text-analytics-resource"></a>Metin Analizi kaynağı oluşturma

Bilişsel Hizmetler API'leri Azure’da tek kaynaklar halinde kullanılabilir. Metin Analizi API'si, deftere nakledilen kavalerin yaklaşımını algılamak için kullanın.

1. [Azure portalında](https://portal.azure.com/) oturum açın.

1. Azure portalının sol üst köşesinde bulunan **Kaynak oluştur** öğesini seçin.

1. _Kategoriler_ altında **AI + Machine Learning** ' yi seçin.

1. _Metin analizi_ altında **Oluştur**' u seçin.

1. _Metin analizi oluştur_ ekranına aşağıdaki değerleri girin.

    | Ayar | Değer | Açıklamalar |
    | ------- | ----- | ------- |
    | Abonelik | Azure abonelik adınız | |
    | Kaynak grubu | **Tweet-Sentiment** adlı yeni bir kaynak grubu oluşturma-öğretici | Daha sonra bu öğreticide oluşturulan tüm kaynakları kaldırmak için bu kaynak grubunu silersiniz. |
    | Region | Size en yakın bölgeyi seçin | |
    | Name | **Doldurulabilir Etsentimentapp** | |
    | Fiyatlandırma katmanı | **Ücretsiz F0** seçin | |

1. **Gözden geçir ve oluştur**’u seçin.

1. **Oluştur**’u seçin.

1. Dağıtım tamamlandıktan sonra **Kaynağa Git**' i seçin.

## <a name="get-text-analytics-settings"></a>Metin Analizi ayarlarını al

Metin Analizi kaynağı oluşturulduğunda, birkaç ayarı kopyalayıp daha sonra kullanılmak üzere kaydederek bunları ayarlayabilirsiniz.

1. **Anahtarlar ve uç nokta** seçin.

1. Giriş kutusunun sonundaki simgeye tıklayarak **anahtar 1** ' i kopyalayın.

1. Değeri bir metin düzenleyicisine yapıştırın.

1. Giriş kutusunun sonundaki simgeye tıklayarak **uç noktayı** kopyalayın.

1. Değeri bir metin düzenleyicisine yapıştırın.

## <a name="create-the-function-app"></a>İşlev uygulaması oluşturma

1. Üst arama kutusundan **işlev uygulaması**' nı arayıp seçin.

1. **Oluştur**’u seçin.

1. Aşağıdaki değerleri girin.

    | Ayar | Önerilen Değer | Açıklamalar |
    | ------- | ----- | ------- |
    | Abonelik | Azure abonelik adınız | |
    | Kaynak grubu | **Tweet Sentiment-öğretici** | Bu öğreticide aynı kaynak grubu adını kullanın. |
    | İşlev Uygulamasının adı | **Doldurulabilir Etsentimentapi** + benzersiz bir sonek | İşlev uygulaması adları genel olarak benzersizdir. Geçerli karakterler şunlardır: `a-z` (büyük/küçük harf duyarsız), `0-9` ve `-`. |
    | Yayımlama | **Kod** | |
    | Çalışma zamanı yığını | **.NET** | Sizin için belirtilen işlev kodu C# ' dir. |
    | Sürüm | En son sürüm numarasını seçin | |
    | Region | Size en yakın bölgeyi seçin | |

1. **Gözden geçir ve oluştur**’u seçin.

1. **Oluştur**’u seçin.

1. Dağıtım tamamlandıktan sonra **Kaynağa Git**' i seçin.

## <a name="create-an-http-triggered-function"></a>HTTP ile tetiklenen işlev oluşturma  

1. _İşlevler_ penceresinin sol menüsünde **işlevler**' i seçin.

1. Üstteki menüden **Ekle** ' yi seçin ve aşağıdaki değerleri girin.

    | Ayar | Değer | Açıklamalar |
    | ------- | ----- | ------- |
    | Geliştirme ortamı | **Portalda geliştirme** | |
    | Şablon | **HTTP tetikleyicisi** | |
    | Yeni Işlev | **Kavatimentişlevi** | Bu, işlevinizin adıdır. |
    | Yetkilendirme düzeyi | **İşlev** | |

1. **Ekle** düğmesini seçin.

1. **Kod + test** düğmesini seçin.

1. Kod Düzenleyicisi penceresinde aşağıdaki kodu yapıştırın.

    ```csharp
    #r "Newtonsoft.Json"
    
    using System;
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
    
        string requestBody = String.Empty;
        using (StreamReader streamReader =  new  StreamReader(req.Body))
        {
            requestBody = await streamReader.ReadToEndAsync();
        }
    
        dynamic score = JsonConvert.DeserializeObject(requestBody);
        string value = "Positive";
    
        if(score < .3)
        {
            value = "Negative";
        }
        else if (score < .6) 
        {
            value = "Neutral";
        }
    
        return requestBody != null
            ? (ActionResult)new OkObjectResult(value)
           : new BadRequestObjectResult("Pass a sentiment score in the request body.");
    }
    ```

    Değer için bir kategori adı döndüren işleve bir yaklaşım puanı geçirilir.

1. Değişikliklerinizi kaydetmek için araç çubuğundaki **Kaydet** düğmesini seçin.

    > [!NOTE]
    > İşlevi test etmek için üstteki menüden **Test/Çalıştır** ' ı seçin. _Giriş_ sekmesinde, gövde girişi kutusuna bir değer girin `0.9` ve sonra  **Çalıştır**' ı seçin. _Çıkış_ bölümündeki _http yanıt Içeriği_ kutusunda bir _pozitif_ değer döndürüldüğünden emin olun.

Ardından, Azure Işlevleri, Twitter ve bilişsel hizmetler API 'SI ile tümleşen bir mantıksal uygulama oluşturun.

## <a name="create-a-logic-app"></a>Mantıksal uygulama oluşturma

1. Üst arama kutusundan **Logic Apps** arayıp seçin.

1. **Add (Ekle)** seçeneğini belirleyin.

1. **Tüketim** ' i seçin ve aşağıdaki değerleri girin.

    | Ayar | Önerilen Değer |
    | ------- | --------------- |
    | Abonelik | Azure abonelik adınız |
    | Kaynak grubu | **Tweet Sentiment-öğretici** |
    | Mantıksal uygulama adı | **Doldurulabilir Etsentimentapp** |
    | Region | Size en yakın bölgeyi, tercihen önceki adımlarda seçtiğiniz bölgeyi seçin. |

    Tüm diğer ayarlar için varsayılan değerleri kabul edin.

1. **Gözden geçir ve oluştur**’u seçin.

1. **Oluştur**’u seçin.

1. Dağıtım tamamlandıktan sonra **Kaynağa Git**' i seçin.

1. **Boş mantıksal uygulama** düğmesini seçin.

    :::image type="content" source="media/functions-twitter-email/blank-logic-app-button.png" alt-text="Boş mantıksal uygulama düğmesi":::

1. İlerleme durumunu kaydetmek için araç çubuğundaki **Kaydet** düğmesini seçin.

Şimdi uygulamanıza hizmet ve tetikleyici eklemek için Logic Apps tasarımcısını kullanabilirsiniz.

## <a name="connect-to-twitter"></a>Twitter’a Bağlanma

Uygulamanızın yeni çetler için yoklama yapabilmesi için Twitter 'a bir bağlantı oluşturun.

1. Üstteki arama kutusunda **Twitter** araması yapın.

1. **Twitter** simgesini seçin.

1. **Yeni bir tweet gönderildiğinde** tetikleyicisini seçin.

1. Bağlantıyı kurmak için aşağıdaki değerleri girin.

    | Ayar |  Değer |
    | ------- | ---------------- |
    | Bağlantı adı | **Mydallı bir bağlantı** |
    | Kimlik Doğrulama Türü | **Varsayılan paylaşılan uygulamayı kullan** |

1. **Oturum aç**'ı seçin.

1. Twitter 'da oturum açmayı tamamlamaya yönelik açılan penceredeki yönergeleri izleyin.

1. Ardından, _Yeni bir tweet_ gönderildiğinde kutusuna aşağıdaki değerleri girin.

    | Ayar | Değer |
    | ------- | ----- |
    | Arama metni | **#my-Twitter-öğretici** |
    | Öğeleri ne şekilde denetlemek istiyorsunuz? | metin kutusunda **15** ve <br> Açılan menüdeki **dakika** |

1. İlerleme durumunu kaydetmek için araç çubuğundaki **Kaydet** düğmesini seçin.

Daha sonra, toplanan kavalerin yaklaşımını saptamak için metin analizinin bağlantısını yapın.

## <a name="add-text-analytics-sentiment-detection"></a>Metin Analizi yaklaşım algılaması ekleme

1. **Yeni adım**'ı seçin.

1. Arama kutusunda **metin analizi** arayın.

1. **Metin analizi** simgesini seçin.

1. Yaklaşımı **Algıla** ' yı seçin ve aşağıdaki değerleri girin.

    | Ayar | Değer |
    | ------- | ----- |
    | Bağlantı adı | **TextAnalyticsConnection** |
    | Hesap anahtarı | Daha önce ayrılan Metin Analizi hesap anahtarını yapıştırın. |
    | Site URL 'SI | Daha önce ayrılan Metin Analizi uç noktaya yapıştırın. |

1. **Oluştur**’u seçin.

1. _Yeni parametre Ekle_ kutusunun içine tıklayın ve açılır pencerede görünen **Belgeler** ' in yanındaki kutuyu işaretleyin.

1. _Belge kimliği-1_ metin kutusunun içine tıklayarak dinamik içerik açılır penceresini açın.

1. _Dinamik içerik_ arama kutusunda **kimlik**' i arayın ve **Tweet ID**' ye tıklayın.

1. _Belge metni-1_ metin kutusuna tıklayarak dinamik içerik açılır penceresini açın.

1. _Dinamik içerik_ arama kutusunda, **metin** araması yapın ve **Tweet metin**' e tıklayın.

1. **Eylem seçin** alanına **Metin Analizi** yazın ve sonra **Duyarlılığı algıla** eylemine tıklayın.

1. İlerleme durumunu kaydetmek için araç çubuğundaki **Kaydet** düğmesini seçin.

Yaklaşım _algılama_ kutusu aşağıdaki ekran görüntüsüne benzer şekilde görünmelidir.

:::image type="content" source="media/functions-twitter-email/detect-sentiment.png" alt-text="Yaklaşım ayarlarını algılama":::

## <a name="connect-sentiment-output-to-function-endpoint"></a>Yaklaşım çıkışını işlev uç noktasına bağlama

1. **Yeni adım**'ı seçin.

1. Arama kutusunda **Azure işlevleri** araması yapın.

1. **Azure işlevleri** simgesini seçin.

1. Arama kutusunda işlev adınızı arayın. Yukarıdaki kılavuzunuzu izlediyseniz, işlev adınız, **doldurulabilir Etsentimentapi** ile başlar.

1. İşlev simgesini seçin.

1. **Arası doldurulabilir işlev** öğesini seçin.

1. _Istek gövdesi_ kutusunun içine tıklayın ve açılır pencereden yaklaşım Puanını _Algıla_  öğesini seçin.

1. İlerleme durumunu kaydetmek için araç çubuğundaki **Kaydet** düğmesini seçin.

## <a name="add-conditional-step"></a>Koşullu adım Ekle

1. **Eylem Ekle** düğmesini seçin.

1. _Denetim_ kutusunun içine tıklayın ve açılır penceredeki **denetimi** arayın ve seçin.

1. **Koşul** seçin.

1. _Değer seçin_ kutusunun içine tıklayın ve açılır penceredeki _doldurulabilir işlev_ **gövdesi** öğesini seçin.

1. _Değer seçin_ kutusuna **pozitif** girin.

1. İlerleme durumunu kaydetmek için araç çubuğundaki **Kaydet** düğmesini seçin.

## <a name="add-email-notifications"></a>E-posta bildirimleri ekleme

1. _Doğru_ kutusunda **Eylem Ekle** düğmesini seçin.

1. Metin kutusunda **Office 365 Outlook 'u** arayın ve seçin.

1. **Gönder** ' i arayın ve metin kutusunda **e-posta gönder** ' i seçin.

1. **Oturum aç** düğmesini seçin.

1. Office 365 Outlook 'ta oturum açmayı tamamlamaya yönelik açılan penceredeki yönergeleri izleyin.

1. _Kutuya e_ -posta adresinizi girin.

1. _Konu_ kutusunun içine tıklayın ve sonra da _doldurulabilir işlevi_ altındaki **Body** öğesine tıklayın. Listede _gövde_ öğesi gösterilmemişse, seçenekler listesini genişletmek için **daha fazla gör** bağlantısına tıklayın.

1. _Konu_ içindeki _gövde_ öğesinden sonra, Tweet metnini girin **:**.

1. _Tweet From: Text öğesinden_ sonra, kutuya yeniden tıklayın ve _Yeni bir tweet ne zaman postalanan_ seçenekler listesinden **Kullanıcı adı** ' nı seçin.

1. _Gövde_ kutusunun içine tıklayın ve _Yeni bir tweet nakledildiğinde_ seçenekler listesi altında **Tweet metin** ' i seçin. Listede _Tweet metin_ öğesi gösterilmemişse, seçenekler listesini genişletmek için **daha fazla gör** bağlantısına tıklayın.

1. İlerleme durumunu kaydetmek için araç çubuğundaki **Kaydet** düğmesini seçin.

E-posta kutusu artık bu ekran görüntüsüne benzer şekilde görünmelidir.

:::image type="content" source="media/functions-twitter-email/email-notification.png" alt-text="E-posta bildirimi":::

## <a name="run-the-workflow"></a>İş akışını çalıştırma

1. Twitter hesabınızdan şu metni Tweet: **#my-Twitter-öğreticisini beğendiniz**.

1. Logic Apps Tasarımcısına dönün ve **Çalıştır** düğmesini seçin.

1. İş akışından bir ileti için e-postanızı kontrol edin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğreticide oluşturulan tüm Azure hizmetlerini ve hesaplarını temizlemek için kaynak grubunu silin.

1. En üstteki arama kutusunda **kaynak gruplarını** arayın.

1. **Tweet-Sentiment-öğreticisi**' ni seçin.

1. **Kaynak grubunu sil** ' i seçin

1. Metin kutusuna **Tweet-Sentiment-öğreticisi** girin.

1. **Sil** düğmesini seçin.

İsteğe bağlı olarak, Twitter hesabınıza dönmek ve akışınızdan tüm test çlerinizi silmek isteyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure İşlevleri'ni kullanarak sunucusuz bir API oluşturma](functions-create-serverless-api.md)
