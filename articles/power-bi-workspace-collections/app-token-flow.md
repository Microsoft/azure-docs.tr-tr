---
title: Kimlik doğrulama ve yetkilendirme ile Power BI çalışma alanı koleksiyonları | Microsoft Docs
description: Kimlik doğrulaması ve Power BI çalışma alanı koleksiyonları ile yetkilendirme.
services: power-bi-workspace-collections
author: rkarlin
ms.author: rkarlin
ms.service: power-bi-embedded
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: 713c56904769c133272db4fb65f8b596ab66804b
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672497"
---
# <a name="authenticating-and-authorizing-with-power-bi-workspace-collections"></a>Kimlik doğrulama ve yetkilendirme ile Power BI çalışma alanı koleksiyonları

Power BI çalışma alanı koleksiyonları kullanım **anahtarları** ve **uygulama belirteçlerini** kimlik doğrulaması ve yetkilendirme, açık son kullanıcı kimlik doğrulaması yerine. Bu modelde, uygulamanız kimlik doğrulaması ve yetkilendirme son kullanıcılarınız için yönetir. Gerektiğinde uygulamanızı oluşturur ve istenen rapor oluşturulacak hizmetimiz söyleyin uygulama belirteçlerini gönderir. Bu tasarım, hala ancak kullanıcı kimlik doğrulaması ve yetkilendirme için Azure Active Directory kullanmak için uygulamanızı gerektirmez.

> [!IMPORTANT]
> Power BI Çalışma Alanı Koleksiyonları kullanım dışı bırakılmıştır ve Haziran 2018'e kadar veya anlaşmanızda belirtilen süre boyunca kullanılabilecektir. Uygulamanızda kesinti yaşanmaması için Power BI Embedded'a geçirmeyi planlamanız önerilir. Verilerinizi Power BI Embedded'a nasıl taşıyacağınızı öğrenmek için bkz. [Power BI Çalışma Alanı Koleksiyonları'nı Power BI Embedded'a geçirme](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="two-ways-to-authenticate"></a>Kimlik doğrulaması yapmak için iki yol

**Anahtar** -tüm Power BI çalışma alanı koleksiyonları REST API çağrıları için tuşlarını kullanabilirsiniz. Anahtarları bulunabilir **Microsoft Azure Portal'da** seçerek **tüm ayarlar** ardından **erişim anahtarları**. Eğer bir parola olarak ise anahtarınızı her zaman değerlendirin. Bu anahtarları bir REST API üzerinde belirli bir çalışma alanı koleksiyonu çağrısı yapmak için izinlere sahip.

Bir anahtar REST çağrısı kullanmak için aşağıdaki yetkilendirme üst bilgisi ekleyin:

    Authorization: AppKey {your key}

**Uygulama belirteci** -uygulama belirteçlerini katıştırma tüm istekler için kullanılır. İstemci tarafı çalıştırılmak üzere tasarlanmışlardır. Belirteç sona erme süresini ayarlamak için en iyi yöntem ve tek bir rapor ile sınırlıdır.

Uygulama, JWT (JSON Web belirteci) anahtarlarınızdan birini tarafından imzalanmış belirteçleridir.

Uygulama belirtecinizi aşağıdaki talep içerebilir:

| İste | Açıklama |    
| --- | --- |
| **ver** |Uygulama belirteci sürümü. 0.2.0 geçerli sürümüdür. |
| **aud** |Amaçladığınız alıcının belirtecin. Power BI çalışma alanı koleksiyonları'nı kullanın: *https:\//analysis.windows.net/powerbi/api*. |
| **ISS** |Belirteci veren uygulamayı gösteren bir dize. |
| **type** |Oluşturulan uygulama Belirtecin türü. Geçerli olan tek desteklenen tür **ekleme**. |
| **WCN** |Çalışma alanı koleksiyon adı belirteci için yayımlanmaktadır. |
| **WID** |Çalışma alanı kimliği belirteç için yayımlanmaktadır. |
| **RID** |Belirteç kodu veren bildirin. |
| **Kullanıcı adı** (isteğe bağlı) |RLS ile birlikte kullanıldığında, kullanıcı adı, ve RLS kurallarını uygularken kullanıcının belirlemeye yardımcı olabilecek bir dizedir. |
| **rolleri** (isteğe bağlı) |Satır düzeyi güvenlik kurallarını uygularken seçilecek rolleri içeren bir dize. Birden fazla rol geçirilirse, bir barındırma dizisi olarak geçirilmelidir. |
| **SCP** (isteğe bağlı) |İzin kapsamları içeren bir dize. Birden fazla rol geçirilirse, bir barındırma dizisi olarak geçirilmelidir. |
| **exp** (isteğe bağlı) |Hangi belirteç süre sonu zamanı gösterir. Değer Unix zaman damgası geçirilmelidir. |
| **NBF** (isteğe bağlı) |Geçerli belirteç içinde başladığı saati belirtir. Değer Unix zaman damgası geçirilmelidir. |

Örnek uygulama belirteci şuna benzer:

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ2ZXIiOiIwLjIuMCIsInR5cGUiOiJlbWJlZCIsIndjbiI6Ikd1eUluQUN1YmUiLCJ3aWQiOiJkNGZlMWViMS0yNzEwLTRhNDctODQ3Yy0xNzZhOTU0NWRhZDgiLCJyaWQiOiIyNWMwZDQwYi1kZTY1LTQxZDItOTMyYy0wZjE2ODc2ZTNiOWQiLCJzY3AiOiJSZXBvcnQuUmVhZCIsImlzcyI6IlBvd2VyQklTREsiLCJhdWQiOiJodHRwczovL2FuYWx5c2lzLndpbmRvd3MubmV0L3Bvd2VyYmkvYXBpIiwiZXhwIjoxNDg4NTAyNDM2LCJuYmYiOjE0ODg0OTg4MzZ9.v1znUaXMrD1AdMz6YjywhJQGY7MWjdCR3SmUSwWwIiI
```

Kodu çözülen, şunun gibi görünür:

```
Header

{
    typ: "JWT",
    alg: "HS256:
}

Body

{
  "ver": "0.2.0",
  "wcn": "SupportDemo",
  "wid": "ca675b19-6c3c-4003-8808-1c7ddc6bd809",
  "rid": "96241f0f-abae-4ea9-a065-93b428eddb17",
  "iss": "PowerBISDK",
  "aud": "https://analysis.windows.net/powerbi/api",
  "exp": 1360047056,
  "nbf": 1360043456
}

```

Uygulama belirteçleri oluşturulmasını kolaylaştırmak SDK'lar içinde kullanılabilen yöntemler vardır. Örneğin, .NET için bakabilirsiniz [Microsoft.PowerBI.Security.PowerBIToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken) sınıfı ve [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN) yöntemleri.

.NET SDK için başvurabilirsiniz [kapsamları](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.scopes).

## <a name="scopes"></a>Kapsamlar

Ekleme belirteçlerinin kullanırken, erişim izni verdiğiniz kaynak kullanımını kısıtlamak isteyebilirsiniz. Bu nedenle, kapsamlı izinlere sahip bir belirteç oluşturabilirsiniz.

Power BI çalışma alanı koleksiyonları için kullanılabilir kapsamları aşağıda verilmiştir.

|`Scope`|Açıklama|
|---|---|
|Dataset.Read|Belirtilen veri okuma izni sağlar.|
|Dataset.Write|Belirtilen veri kümesi için yazma izni sağlar.|
|Dataset.ReadWrite|Okuma ve yazma için belirtilen veri kümesi izni sağlar.|
|Report.Read|Belirtilen rapor görüntüleme izni sağlar.|
|Report.ReadWrite|Görüntüleme ve belirtilen rapor düzenleme izni sağlar.|
|Workspace.Report.Create|Belirtilen çalışma alanı içinde yeni bir rapor oluşturma izni sağlar.|
|Workspace.Report.Copy|Belirtilen çalışma alanı içinde var olan bir raporu kopyalama izni sağlar.|

Aşağıdaki gibi kapsamlar arasında bir boşluk kullanarak birden çok kapsam sağlayabilirsiniz.

```csharp
string scopes = "Dataset.Read Workspace.Report.Create";
```

**Gerekli talep - kapsamları**

SCP: {scopesClaim} scopesClaim bir dize veya bir çalışma alanı kaynakları (rapor, veri kümesi, vb.) için verilen izinler belirterek bir dize dizisi olabilir

Kodu çözülen bir belirteç tanımlanan kapsamlar ile benzer şekilde görünür:

```
Header

{
    typ: "JWT",
    alg: "HS256:
}

Body

{
  "ver": "0.2.0",
  "wcn": "SupportDemo",
  "wid": "ca675b19-6c3c-4003-8808-1c7ddc6bd809",
  "rid": "96241f0f-abae-4ea9-a065-93b428eddb17",
  "scp": "Report.Read",
  "iss": "PowerBISDK",
  "aud": "https://analysis.windows.net/powerbi/api",
  "exp": 1360047056,
  "nbf": 1360043456
}

```

### <a name="operations-and-scopes"></a>İşlemler ve kapsamları

|Çalışma|Hedef kaynak|Belirteç izinleri|
|---|---|---|
|Bir veri kümesini temel alan yeni bir rapor (bellek içi) oluşturun.|Veri kümesi|Dataset.Read|
|(Bellek içi) bir veri kümesini temel alan yeni bir rapor oluşturun ve raporu kaydedin.|Veri kümesi|* Dataset.Read<br>* Workspace.Report.Create|
|Görüntüleyin ve (bellek içi) var olan bir raporu keşfedin/düzenleyin. Report.Read Dataset.Read anlamına gelir. Report.Read izin vermez düzenlemeleri kaydediliyor.|Rapor|Report.Read|
|Düzenle ve var olan bir raporu kaydedin.|Rapor|Report.ReadWrite|
|(Farklı Kaydet) bir raporun bir kopyasını kaydedin.|Rapor|* Report.Read<br>* Workspace.Report.Copy|

## <a name="heres-how-the-flow-works"></a>İşte akışı nasıl çalışır?
1. API anahtarları, uygulamaya kopyalayın. Anahtarlarını alabilirsiniz **Azure portalında**.
   
    ![Azure portalında API anahtarlarını bulmak nereye](media/get-started-sample/azure-portal.png)
1. Belirteç talep onaylar ve sona erme zamanına sahip.
   
    ![Uygulama belirteci akışı - belirteç talep onaylar.](media/get-started-sample/token-2.png)
1. Bir API erişim anahtarı belirteci imzalayan.
   
    ![Uygulama belirteci akışı - belirteci imzalı](media/get-started-sample/token-3.png)
1. Bir raporu görüntülemek için kullanıcı istekleri'ni kullanın.
   
    ![Uygulama belirteci akışı - bir raporu görüntülemek için kullanıcı istekleri](media/get-started-sample/token-4.png)
1. Belirteç, bir API erişim anahtarı doğrulanır.
   
   ![Uygulama belirteci akışı - belirteç doğrulandı](media/get-started-sample/token-5.png)
1. Power BI çalışma alanı koleksiyonları, kullanıcıya bir rapor gönderir.
   
   ![Uygulama belirteci akışı - hizmet gönderme raporu kullanıcıya](media/get-started-sample/token-6.png)

Sonra **Power BI çalışma alanı koleksiyonları** rapor kullanıcı, kullanıcı için bir özel uygulamanızda raporu görüntüleyebilirsiniz. Örneğin, içeri aktardığınız [çözümleme satış verileri PBIX örnek](https://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix), örnek web uygulamasını şöyle görünmelidir:

![Uygulamaya bir rapor örneği](media/get-started-sample/sample-web-app.png)

## <a name="see-also"></a>Ayrıca Bkz.

[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN)  
[Microsoft Power BI çalışma alanı koleksiyonları örnek ile kullanmaya başlama](get-started-sample.md)  
[Microsoft Power BI çalışma alanı koleksiyonları senaryoları](scenarios.md)  
[Microsoft Power BI çalışma alanı koleksiyonları ile çalışmaya başlama](get-started.md)  
[Power BI-CSharp Git deposu](https://github.com/Microsoft/PowerBI-CSharp)

Başka sorunuz mu var? [Power BI Topluluğu'nu deneyin](https://community.powerbi.com/)
