---
title: Power BI Çalışma Alanı Koleksiyonları ile satır düzeyinde güvenlik
description: Power BI çalışma alanı koleksiyonları ile satır düzeyi güvenlik hakkındaki ayrıntıları
services: power-bi-workspace-collections
ms.service: power-bi-embedded
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: 2a26cc7573abb970dc58c6f7c327dfbc659cb646
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672492"
---
# <a name="row-level-security-with-power-bi-workspace-collections"></a>Power BI Çalışma Alanı Koleksiyonları ile satır düzeyinde güvenlik

Satır düzeyi güvenlik (RLS), bir rapora veya aynı rapordaki farklı verileri kullanmak birden çok farklı kullanıcılar için veri kümesi içinde belirli verilere kullanıcı erişimini kısıtlamak için kullanılabilir. Power BI çalışma alanı koleksiyonları ile RLS yapılandırılmış veri kümelerini destekler.

![Power BI çalışma alanı koleksiyonları içinde satır düzeyi güvenlik akışı](media/row-level-security/flow-1.png)

> [!IMPORTANT]
> Power BI Çalışma Alanı Koleksiyonları kullanım dışı bırakılmıştır ve Haziran 2018'e kadar veya anlaşmanızda belirtilen süre boyunca kullanılabilecektir. Uygulamanızda kesinti yaşanmaması için Power BI Embedded'a geçirmeyi planlamanız önerilir. Verilerinizi Power BI Embedded'a nasıl taşıyacağınızı öğrenmek için bkz. [Power BI Çalışma Alanı Koleksiyonları'nı Power BI Embedded'a geçirme](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

RLS'den faydalanmak için üç ana kavramı anlamak önemlidir; Kullanıcılar, roller ve kurallar. Her bir daha yakından göz atalım:

**Kullanıcılar** – bunlar olan raporları görüntüleyen gerçek son. Power BI çalışma alanı koleksiyonları'nda, kullanıcıların bir uygulama belirteci içindeki kullanıcı adı özelliği tarafından tanımlanır.

**Rolleri** – kullanıcılar rollere aittir. Bir rol, kural için bir kapsayıcıdır ve bir şey "Satış Yöneticisi" veya "Satış Temsilcisi" gibi adlandırılabilir Power BI çalışma alanı koleksiyonları'nda, kullanıcıların bir uygulama belirteci rolleri özelliği tarafından tanımlanır.

**Kuralları** : roller kurallara sahiptir ve bu kurallar verilere uygulanacak giderek gerçek filtrelerdir. Bu kadar basit olabilir "Country = USA" ya da daha dinamik.

### <a name="example"></a>Örnek

Bu makalenin kalan bölümünde RLS yazma ve sonra katıştırılmış uygulama içinde tüketmesi örneği sunuyoruz. Örneğimizde [Retail Analysis Sample](https://go.microsoft.com/fwlink/?LinkID=780547) PBIX dosyası.

![Örnek satış raporu](media/row-level-security/scenario-2.png)

Retail Analysis sample bir perakende zincirindeki tüm mağazaların satış gösterir. Hangi bölge ne olursa olsun, Yöneticisi'ni açar ve raporu görüntüleyen RLS, bunlar aynı verileri görür. Üst düzey yönetimi, her bir district manager yalnızca Yöneticiler mağazaların satış görmeniz gerekir ve bunu yapmak için RLS kullanabilir miyiz belirledi.

RLS, Power BI Desktop'ta yazılmıştır. Biz veri kümesini ve raporu açtığımızda şema görmek için diyagram görünümüne geçiş yapabilirsiniz:

![Power BI Desktop'ta modeli diyagramı](media/row-level-security/diagram-view-3.png)

Bu şemada dikkat gereken bazı noktalar şunlardır:

* Gibi tüm ölçüler **Total Sales**, depolanan **satış** Olgu Tablosu.
* Dört ek ilgili boyut tablolarının vardır: **Öğe**, **zaman**, **Store**, ve **bölge**.
* Filtreler bir tablodan diğerine akabilir çizgilerindeki oklar ilişki gösterir. Örneğin, bir filtre yerleştirildiği **Time [Date]** , geçerli şemada, yalnızca değerler filtreler **satış** tablo. Tüm ilişki oklar sales tablosunu işaret olduğundan başka tablo yoktur bu filtreden etkilenecek.
* **Bölge** tablo için her bir district manager olan gösterir:
  
  ![Bölge tablo satırları](media/row-level-security/district-table-4.png)

Biz bir filtre uygularsanız, bu şemaya göre **District Manager** bölge tablodaki sütun ve filtre raporu görüntüleyen kullanıcıyla eşleştiğinde, filtresini ayrıca filtreler aşağı **Store** ve  **Satış** tablolarını da yalnızca Yöneticisi belirli ilgili bölge yöneticisine ait verileri gösterecek.

Bunu şu şekilde yapabilirsiniz:

1. Modelleme sekmesinde, **Rolleri Yönet**.  
   ![Modelleme Şerit düğmesi rolleri yönetme](media/row-level-security/modeling-tab-5.png)
2. Adlı yeni bir rol oluşturmak **Manager**.  
   ![Power BI Desktop uygulamasında rolleri oluşturma](media/row-level-security/manager-role-6.png)
3. İçinde **bölge** tablo aşağıdaki DAX deyimini girin: **[District Manager] = USERNAME()**  
   ![Roldeki tablosu için DAX filtresi ifadesi](media/row-level-security/manager-role-7.png)
4. Kuralların çalıştığından emin olmak için **modelleme** sekmesinde **rol olarak görüntüle**ve ardından aşağıdakileri girin:  
   ![Rol olarak görüntüler](media/row-level-security/view-as-roles-8.png)

   Olarak imzalı olup olmadığını veriler raporları gösterilir **Andrew Ma**.

Buradaki gibi şekilde filtre uygulayarak filtreler tüm kayıtlar aşağı **bölge**, **Store**, ve **satış** tablolar. Ancak, arasındaki ilişkilerdeki filtre yönü nedeniyle **satış** ve **zaman**, **satış** ve **öğesi**ve **Öğesi** ve **zaman** tabloları aşağı filtrelenmez.

![Diyagram görünümü ile vurgulanmış ilişkisi](media/row-level-security/diagram-view-9.png)

Bu gereksinim için Tamam olabilir, ancak bunlar herhangi bir vergiyi yoksa öğeleri görmek için yöneticileri de istemiyorsanız, biz çift yönlü çapraz filtreleme ilişkisi için Aç ve güvenlik filtrelerini her iki yönde akmasını. Bu arasındaki ilişkiyi düzenleyerek yapılabilir **satış** ve **öğesi**, şöyle:

![Çapraz filtre yönü ilişki](media/row-level-security/edit-relationship-10.png)

Şimdi, filtreleri de satış tablosundan akış **öğesi** tablosu:

![İlişkisi diyagram Görünümü'nde filtre yönü simgesine](media/row-level-security/diagram-view-11.png)

> [!NOTE]
> Verileriniz için DirectQuery modu kullanıyorsanız, bu iki seçenek belirleyerek çift yönlü çapraz filtrelemeyi etkinleştirmek gerekir:

1. **Dosya** -> **seçenekler ve ayarlar** -> **Önizleme özellikleri** -> **çapraz DirectQuery için her iki yönde de filtreleme etkinleştir** .
2. **Dosya** -> **seçenekler ve ayarlar** -> **DirectQuery** -> **DirectQuerymodundaKısıtlanmamışölçüizin**.

Çift yönlü çapraz filtreleme hakkında daha fazla bilgi için indirme [çift yönlü çapraz filtreleme SQL Server Analysis Services 2016 ve Power BI Desktop'ta](https://download.microsoft.com/download/2/7/8/2782DF95-3E0D-40CD-BFC8-749A2882E109/Bidirectional%20cross-filtering%20in%20Analysis%20Services%202016%20and%20Power%20BI.docx) teknik incelemesi.

Bu Power BI Desktop uygulamasında yapmanız gereken tüm iş odaklı serimiz, ancak bir daha fazla parça RLS yapmak için yapılması gereken çalışmanın iş Power BI Embedded içinde tanımladığımız kuralları vardır. Kullanıcıların kimlik doğrulaması ve yetkilendirmesi uygulama tarafından gerçekleştirilir ve uygulama belirteçleri, belirli bir Power BI Embedded raporu, kullanıcı erişimi vermek için kullanılır. Power BI Embedded kullanıcınız kim belirli bilgilere sahip değil. RLS'nin çalışması için uygulama belirtecinin bir parçası bazı ek bağlam geçirmeniz gerekir:

* **Kullanıcı adı** (isteğe bağlı) – RLS ile kullanılan bu RLS kurallarını uygularken kullanıcının belirlemenize yardımcı olması için kullanılan bir dizedir. Satır düzeyi güvenlik ile Power BI Embedded ' ı kullanma
* **rolleri** – satır düzeyi güvenlik kurallarını uygularken seçilecek rolleri içeren bir dize. Birden fazla rol geçirilirse, bir dize dizisi olarak geçirilmelidir.

Kullanarak belirteci oluşturma [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN) yöntemi. Username özelliği varsa, en az bir değer de rollerinde geçmesi gerekir.

Örneğin, EmbedSample değiştirebilir. DashboardController satırı 55 yazılım güncelleştirilemedi

    var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id);

-

    var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id, "Andrew Ma", ["Manager"]);'

Tam uygulama belirteci aşağıdaki gibi görünür:

![JSON web belirteci örneği](media/row-level-security/app-token-string-12.png)

Birisi bu raporu görüntülemek için uygulamamız için oturum açtığında artık tüm parçaları ile birlikte, bunlar görmek için satır düzeyi güvenlik tarafından tanımlanan izin verilen veri görürler.

![Uygulamada gösterilen rapor](media/row-level-security/dashboard-13.png)

## <a name="see-also"></a>Ayrıca bkz.

[Güç ile satır düzeyi güvenlik (RLS)](https://powerbi.microsoft.com/documentation/powerbi-admin-rls/)  
[Power BI Çalışma Alanı Koleksiyonları ile kimlik doğrulaması ve yetkilendirme](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[JavaScript Örnek Ekleme](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Başka sorunuz mu var? [Power BI Topluluğu'nu deneyin](https://community.powerbi.com/)
