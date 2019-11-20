---
title: Microsoft Power BI Çalışma Alanı Koleksiyonları'nı kullanmaya başlama
description: Power BI Çalışma Alanı Koleksiyonları, uygulama geliştiricilerin kendi uygulamalarına etkileşimli Power BI raporları eklemelerini sağlayan bir Azure hizmetidir.
services: power-bi-workspace-collections
ms.service: power-bi-embedded
author: rkarlin
ms.author: rkarlin
ms.topic: conceptual
ms.workload: powerbi
ms.date: 09/25/2017
ms.openlocfilehash: 2b3381166e40f6d9586306e351b5f3ea77cc2ad8
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67669133"
---
# <a name="get-started-with-microsoft-power-bi-workspace-collections"></a>Microsoft Power BI Çalışma Alanı Koleksiyonları'nı kullanmaya başlama

**Power BI Çalışma Alanı Koleksiyonları**, uygulama geliştiricilerin kendi uygulamalarına etkileşimli Power BI raporları eklemelerini sağlayan bir Azure hizmetidir. **Power BI Çalışma Alanı Koleksiyonları** kullanıcıların oturum açma yöntemini yeniden tasarlamaya veya değiştirmeye gerek kalmadan var olan uygulamalarla birlikte çalışır.

> [!IMPORTANT]
> Power BI Çalışma Alanı Koleksiyonları kullanım dışı bırakılmıştır ve Haziran 2018'e kadar veya anlaşmanızda belirtilen süre boyunca kullanılabilecektir. Uygulamanızda kesinti yaşanmaması için Power BI Embedded'a geçirmeyi planlamanız önerilir. Verilerinizi Power BI Embedded'a nasıl taşıyacağınızı öğrenmek için bkz. [Power BI Çalışma Alanı Koleksiyonları'nı Power BI Embedded'a geçirme](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

**Microsoft Power BI Çalışma Alanı Koleksiyonları** kaynakları [Azure Resource Manager API'leri](https://msdn.microsoft.com/library/mt712306.aspx) aracılığıyla sağlanır. Bu örnekte sağlanan kaynak **Power BI Çalışma Alanı Koleksiyonu**'dur.

![Power BI Çalışma Alanı Koleksiyonları'nın genel akışı](media/get-started/introduction.png)

## <a name="create-a-workspace-collection"></a>Çalışma alanı koleksiyonu oluşturma

**Çalışma Alanı Koleksiyonu**, uygulamanıza eklenecek içerik için üst düzey Azure kaynağı ve kapsayıcıdır. **Çalışma Alanı Koleksiyonu** iki şekilde oluşturulabilir:

* Azure portalını el ile kullanarak
* Azure Resource Manager API'lerini program aracılığıyla kullanarak

Şimdi Azure portalını kullanarak **Çalışma Alanı Koleksiyonu** oluşturma adımlarını inceleyelim.

1. **Azure portalı** açıp oturum açın: [https://portal.azure.com](https://portal.azure.com).
2. Üst panelde **+ Yeni**'yi seçin.
   
   ![Azure portalındaki +Yeni seçeneği](media/get-started/create-workspace-1.png)
3. **Veri ve Analiz** bölümünde **Power BI Çalışma Alanı Koleksiyonu**'nu seçin.
4. Başlangıç iletisinde Power BI Çalışma Alanı Koleksiyonu aboneliğiniz varsa en altta bulunan **Çalışma alanı koleksiyonu oluşturun**'u seçin.

5. **Çalışma Alanı Koleksiyonu** alanına gerekli bilgileri girin.
   
   ![Çalışma alanı koleksiyonu oluşturma](media/get-started/create-workspace-2.png)
1. **Oluştur**’u seçin.

**Çalışma Alanı Koleksiyonu**'nun sağlanması birkaç dakika sürer. İşlem tamamlandığında **Çalışma Alanı Koleksiyonu** sayfası açılır.

   ![Azure portalında çalışma alanı koleksiyonu](media/get-started/create-workspace-3.png)

**Oluşturma** sayfası çalışma alanlarını oluşturan ve bunlara içerik dağıtan API'leri çağırmak için size gereken bilgileri içerir.

<a name="view-access-keys"/>

## <a name="view-power-bi-api-access-keys"></a>Power BI API’si erişim anahtarlarını görüntüleme

Power BI API’lerini çağırmanın en önemli parçalarından biri **Erişim Anahtarları**’dır. Bunlar API isteklerinizin kimlik doğrulaması için kullanılan **uygulama belirteçlerini** oluşturmak için kullanılır. **Erişim Anahtarları** bilgilerinizi görüntülemek için **Ayarlar**'da **Erişim Anahtarları**'na tıklayın. **Uygulama belirteçleri** hakkında daha fazla bilgi için bkz. [Power BI Çalışma Alanı Koleksiyonları ile kimlik doğrulama ve yetkilendirme](app-token-flow.md).

   ![Azure portalındaki Çalışma Alanı Koleksiyonu içindeki erişim anahtarları](media/get-started/access-keys.png)

İki anahtarınız olduğunu fark edeceksiniz.

   ![Erişim anahtarları içinde iki anahtar](media/get-started/access-keys-2.png)

Bu anahtarları kopyalayın ve uygulamanızda güvenli bir şekilde depolayın. **Çalışma Alanı Koleksiyonu**'nuzdaki tüm içeriği erişim imkanı sağlayacağından bu anahtarları bir parolaymış gibi ele almanız önemlidir.

İki anahtar listelenmekle birlikte, belirli bir seferde yalnızca bir anahtar gerekir. İkinci anahtar, hizmete erişimi kesintiye uğratmadan anahtarları dönemsel olarak yeniden oluşturabilmeniz için verilir.

Artık uygulamanız için bir Power BI örneğine ve **Erişim Anahtarları**’na sahip olduğunuza göre, kendi uygulamanıza bir rapor aktarabilirsiniz. Bir raporu içeri aktarmayı öğrenmeden önce, sonraki bölümde bir uygulamaya eklemek için Power BI veri kümeleri ve raporları oluşturma açıklanır.

## <a name="working-with-workspaces"></a>Çalışma alanları ile çalışma

Çalışma alanı koleksiyonunuzu oluşturduktan sonra, raporlarınızı ve veri kümelerinizi barındıracak bir çalışma alanı oluşturmanız gerekir. Bir çalışma alanı oluşturmak için [Post Workspace REST API'sini](https://msdn.microsoft.com/library/azure/mt711503.aspx) kullanmanız gerekir.

## <a name="create-power-bi-datasets-and-reports-to-embed-into-an-app-using-power-bi-desktop"></a>Power BI Desktop kullanarak bir uygulamaya eklemek için Power BI veri kümeleri ve raporları oluşturma

Artık, uygulamanız için Power BI örneğini oluşturduğunuza ve**Erişim Anahtarları**'na sahip olduğunuza göre, eklemek istediğiniz Power BI veri kümelerini ve raporlarını oluşturmanız gerekir. Veri kümeleri ve raporlar **Power BI Desktop** kullanarak oluşturulabilir. [Power BI Desktop’u ücretsiz olarak](https://go.microsoft.com/fwlink/?LinkId=521662) indirebilirsiniz. Hızlı bir şekilde kullanmaya başlamak için [Retail Analysis Sample PBIX](https://go.microsoft.com/fwlink/?LinkID=780547)’i de indirebilirsiniz.

> [!NOTE]
> **Power BI Desktop** kullanma hakkında daha fazla bilgi için bkz. [Power BI Desktop kullanmaya başlama](https://powerbi.microsoft.com/guided-learning/powerbi-learning-0-2-get-started-power-bi-desktop).

**Power BI Desktop** ile, verilerin bir kopyasını **Power BI Desktop**’a aktararak ya da **DirectQuery** kullanarak doğrudan veri kaynağına bağlayarak veri kaynağınıza bağlanırsınız.

**İçeri Aktarma** ve **DirectQuery**.arasındaki farklar burada yer almaktadır.

| İçeri Aktarma | DirectQuery |
| --- | --- |
| Tablolar, sütunlar *ve veriler* **Power BI Desktop**’a aktarılır veya kopyalanır. Görselleştirmelerle çalışırken, **Power BI Desktop** verilerin bir kopyasını sorgular. Temel alınan verilerde meydana gelen değişiklikleri görmek için, tam, güncel veri kümesini tekrar yenilemeli ya da içeri aktarmalısınız. |Yalnızca *tablolar ve sütunlar* ve **Power BI Desktop**’a aktarılır veya kopyalanır. Görselleştirmelerle çalışırken, **Power BI Desktop** temel alınan veri kaynağını sorgular, bu da her zaman güncel verileri görüntülediğiniz anlamına gelir. |

Veri kaynağına bağlanma hakkında daha fazla bilgi için bkz. [Veri kaynağına bağlanma](connect-datasource.md)

Çalışmanızı **Power BI Desktop**’a kaydettikten sonra, PBIX dosyası oluşturulur. Bu dosya raporunuzu içerir. Ayrıca, verileri içeri aktarırsanız PBIX tüm veri kümesini içerir ya da **DirectQuery** kullanırsanız PBIX yalnızca veri kümesi şemasını içerir. [Power BI İçeri Aktarma API’si](https://msdn.microsoft.com/library/mt711504.aspx) kullanarak PBIX’i program kullanarak çalışma alanınıza dağıtırsınız.

> [!NOTE]
> **Power BI Çalışma Alanı Koleksiyonları**, veri kümenizin işaret ettiği sunucuyu ve veri tabanını değiştirmek ve veri kümesinin veri tabanınıza bağlanmak için kullanacağı bir hizmet hesabı kimlik bilgisi ayarlamak üzere ek API'lere sahiptir. Bkz. e [Post SetAllConnections](https://msdn.microsoft.com/library/mt711505.aspx) ve [Patch Gateway Datasource](https://msdn.microsoft.com/library/mt711498.aspx).

## <a name="create-power-bi-datasets-and-reports-using-apis"></a>API kullanarak Power BI veri kümeleri ve raporları oluşturma

### <a name="datasets"></a>Veri kümeleri

REST API kullanarak Power BI Çalışma Alanı Koleksiyonları içinde veri kümeleri oluşturabilirsiniz. Daha sonra verileri veri kümenize gönderebilirsiniz. Bu, Power BI Desktop olmadan verilerinizle çalışmanıza olanak sağlar. Daha fazla bilgi için bkz. [Veri Kümeleri Gönderme](https://msdn.microsoft.com/library/azure/mt778875.aspx).

### <a name="reports"></a>Raporlar

JavaScript API kullanarak doğrudan uygulamanızda bir veri kümesinden rapor oluşturabilirsiniz. Daha fazla bilgi için bkz. [Power BI Çalışma Alanı Koleksiyonları'nda bir veri kümesinden yeni bir rapor oluşturma](create-report-from-dataset.md).

## <a name="see-also"></a>Ayrıca Bkz.

[Bir örnek ile kullanmaya başlama](get-started-sample.md)  
[Power BI Çalışma Alanı Koleksiyonları ile kimlik doğrulaması ve yetkilendirme](app-token-flow.md)  
[Rapor ekleme](embed-report.md)  
[Power BI Çalışma Alanı Koleksiyonları'nda bir veri kümesinden yeni bir rapor oluşturma](create-report-from-dataset.md)
[Raporları kaydetme](save-reports.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[JavaScript Örnek Ekleme](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Başka sorunuz mu var? [Power BI Topluluğu'nu deneyin](https://community.powerbi.com/)

