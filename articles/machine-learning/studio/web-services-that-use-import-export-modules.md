---
title: Web hizmetlerinde verileri içeri/dışarı aktarma
titleSuffix: ML Studio (classic) - Azure
description: Verileri Içeri aktarma ve veri alma modüllerini bir Web hizmetinden veri göndermek ve almak için nasıl kullanacağınızı öğrenin.
services: machine-learning
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: 3a7ac351-ebd3-43a1-8c5d-18223903d08e
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 0ae545fd3ecafda74b90a6a4694dd6f506fb44b1
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838810"
---
# <a name="deploy-azure-machine-learning-studio-classic-web-services-that-use-data-import-and-data-export-modules"></a>Veri Içeri aktarma ve veri dışa aktarma modülleri kullanan Azure Machine Learning Studio (klasik) Web hizmetlerini dağıtma

Tahmine dayalı bir deneme oluşturduğunuzda, genellikle bir Web hizmeti girişi ve çıkışı eklersiniz. Denemeyi dağıttığınızda, müşteriler girişler ve çıktılar aracılığıyla Web hizmetinden veri gönderebilir ve alabilir. Bazı uygulamalarda, bir tüketicinin verileri bir veri akışından bulunabilir veya Azure Blob depolama gibi bir dış veri kaynağında yer alabilir. Bu durumlarda, Web hizmeti girişleri ve çıkışları kullanılarak okuma ve yazma verilerine gerek kalmaz. Bunun yerine, veri kaynağı alma modülünü kullanarak veri kaynağından veri okumak için Batch yürütme hizmeti 'ni (BES) kullanabilir ve veri dışa aktarma modülünü kullanarak Puanlama sonuçlarını farklı bir veri konumuna yazabilir.

Verileri Içeri aktarma ve dışarı aktarma modülleri, HTTP, Hive sorgusu, Azure SQL veritabanı, Azure Tablo depolama, Azure Blob depolama, veri akışı sağlama veya şirket içi SQL veritabanı gibi çeşitli veri konumlarına okuma ve yazma işlemi yapabilir.

Bu konu, "örnek 5: eğitme, test, Ikili sınıflandırma için değerlendirme: yetişkin veri kümesi" örneğini kullanır ve veri kümesinin, censusdata adlı bir Azure SQL tablosuna zaten yüklendiğini varsayar.

## <a name="create-the-training-experiment"></a>Eğitim deneme deneyimini oluşturma
"Örnek 5: eğitme, test, Ikili sınıflandırma için değerlendirme: yetişkin veri kümesi" örneğini açtığınızda, örnek yetişkinlere yönelik gelir Ikili sınıflandırma veri kümesini kullanır. Tuvaldeki deneme aşağıdaki resme benzer şekilde görünür:

![Denemenin ilk yapılandırması.](./media/web-services-that-use-import-export-modules/initial-look-of-experiment.png)

Azure SQL tablosundan verileri okumak için:

1. Veri kümesi modülünü silin.
2. Bileşenler arama kutusuna içeri aktar yazın.
3. Sonuçlar listesinden, deneme tuvaline bir *veri Içeri aktarma* modülü ekleyin.
4. *Veri alma* modülünün çıkışı, *Temizleme eksik veri* modülünün girişi.
5. Özellikler bölmesinde, **veri kaynağı** açılan menüsünde **Azure SQL veritabanı** ' nı seçin.
6. **Veritabanı sunucusu adı**, **veritabanı adı**, **Kullanıcı adı**ve **parola** alanlarına veritabanınız için uygun bilgileri girin.
7. Veritabanı sorgusu alanına aşağıdaki sorguyu girin.

     [Age] öğesini seçin,

        [workclass],
        [fnlwgt],
        [education],
        [education-num],
        [marital-status],
        [occupation],
        [relationship],
        [race],
        [sex],
        [capital-gain],
        [capital-loss],
        [hours-per-week],
        [native-country],
        [income]
     dbo. censusdata; öğesinden
8. Deneme tuvalinin alt kısmındaki **Çalıştır**' a tıklayın.

## <a name="create-the-predictive-experiment"></a>Tahmine dayalı deneme oluşturma
Daha sonra, Web hizmetinizi dağıttığınız tahmine dayalı deney 'yi ayarlarsınız.

1. Deneme tuvalinin alt kısmındaki **Web hizmeti ayarla** ' yı tıklatın ve tahmine **dayalı Web hizmeti ' ni seçin [önerilir]** .
2. *Web hizmeti giriş* ve *Web hizmeti çıkış modüllerini* tahmine dayalı deneyden kaldırın.
3. Bileşenler arama kutusuna dışarı aktar yazın.
4. Sonuçlar listesinden, deneme tuvaline bir *veri dışa aktarma* modülü ekleyin.
5. *Puan modeli* modülünün çıkışı, *verileri dışarı aktarma* modülünün girişi.
6. Özellikler bölmesinde, veri hedefi açılan menüsünde **Azure SQL veritabanı** ' nı seçin.
7. **Veritabanı sunucusu adı**, **veritabanı adı**, **sunucu Kullanıcı hesabı adı**ve **sunucu Kullanıcı hesabı parolası** alanlarında, veritabanınız için uygun bilgileri girin.
8. **Kaydedilecek sütunların virgülle ayrılmış listesi** alanında, puanlanmış Etiketler yazın.
9. **Veri tablosu adı alanına**dbo yazın. ScoredLabels. Tablo yoksa, deneme çalıştırıldığında veya Web hizmeti çağrıldığında oluşturulur.
10. **DataTable sütunları alanının virgülle ayrılmış listesinde** ScoredLabels yazın.

Son Web hizmetini çağıran bir uygulama yazdığınızda, çalışma zamanında farklı bir giriş sorgusu veya hedef tablosu belirtmek isteyebilirsiniz. Bu giriş ve çıkışları yapılandırmak için Web hizmeti parametreleri özelliğini kullanarak *veri modülünü Içeri aktarma* veri *kaynağı* özelliğini ve *verileri dışarı aktar* veri hedefi özelliğini ayarlayın.  Web hizmeti parametreleri hakkında daha fazla bilgi için Cortana Intelligence ve blog Machine Learning [Azure Machine Learning Studio Web hizmeti parametreleri girişine](https://blogs.technet.microsoft.com/machinelearning/2014/11/25/azureml-web-service-parameters/) bakın.

İçeri aktarma sorgusu ve hedef tablo için Web hizmeti parametrelerini yapılandırmak için:

1. *Veri Içeri aktarma* modülünün Özellikler bölmesinde, **veritabanı sorgu** alanının sağ üst kısmındaki simgeye tıklayın ve **Web hizmeti parametresi olarak ayarla**' yı seçin.
2. *Veri dışarı aktarma* modülünün Özellikler bölmesinde, **veri tablosu adı** alanının sağ üst kısmındaki simgeye tıklayın ve **Web hizmeti parametresi olarak ayarla**' yı seçin.
3. *Veri modülünü dışarı aktar* Özellikler bölmesinin en altında, **Web hizmeti parametreleri** bölümünde veritabanı sorgusu ' na tıklayın ve sorguyu yeniden adlandırın.
4. **Veri tablosu adı** ' na tıklayın ve bu **tabloyu**yeniden adlandırın.

İşiniz bittiğinde, denemenizin aşağıdaki görüntüye benzer olması gerekir:

![Denemenin nihai görünümü.](./media/web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

Artık denemeyi bir Web hizmeti olarak dağıtabilirsiniz.

## <a name="deploy-the-web-service"></a>Web hizmetini dağıtma
Klasik veya yeni bir Web hizmetine dağıtım yapabilirsiniz.

### <a name="deploy-a-classic-web-service"></a>Klasik bir Web hizmeti dağıtma
Klasik bir Web hizmeti olarak dağıtmak ve kullanmak üzere bir uygulama oluşturmak için:

1. Deneme tuvalinin alt kısmındaki Çalıştır ' a tıklayın.
2. Çalıştırma tamamlandığında **Web Hizmeti Dağıt** ' a tıklayın ve **Web Hizmeti Dağıt [klasik]** seçeneğini belirleyin.
3. Web hizmeti panosunda API anahtarınızı bulun. Daha sonra kullanmak üzere kopyalayın ve kaydedin.
4. **Varsayılan uç nokta** tablosunda, **Batch EXECUTION** bağlantısına tıklayarak API yardım sayfasını açın.
5. C# Visual Studio 'da bir konsol uygulaması oluşturun: **Yeni** > **Project** > **Visual C#**  > **Windows Klasik Masaüstü** > **konsol uygulaması (.NET Framework)** .
6. API Yardım sayfasında, sayfanın altındaki **örnek kod** bölümünü bulun.
7. C# Örnek kodu kopyalayıp program.cs dosyanıza yapıştırın ve BLOB depolama alanına yapılan tüm başvuruları kaldırın.
8. *Apikey* değişkeninin değerini daha önce kaydedilen API anahtarıyla güncelleştirin.
9. İstek bildirimini bulun ve *verileri içeri* aktarma ve *veri verme* modüllerine geçirilen Web hizmeti parametrelerinin değerlerini güncelleştirin. Bu durumda, özgün sorguyu kullanırsınız, ancak yeni bir tablo adı tanımlarsınız.

        var request = new BatchExecutionRequest()
        {
            GlobalParameters = new Dictionary<string, string>() {
                { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable2" },
            }
        };
10. Uygulamayı çalıştırın.

Çalıştırma tamamlandığında, veritabanına Puanlama sonuçlarını içeren yeni bir tablo eklenir.

### <a name="deploy-a-new-web-service"></a>Yeni bir Web hizmeti dağıtma

> [!NOTE]
> Yeni bir Web hizmeti dağıtmak için, Web hizmetini dağıttığınız abonelikte yeterli izinlere sahip olmanız gerekir. Daha fazla bilgi için, [Azure Machine Learning Web Hizmetleri portalını kullanarak bir Web hizmetini yönetme](manage-new-webservice.md)makalesine bakın.

Yeni bir Web hizmeti olarak dağıtmak ve kullanmak üzere bir uygulama oluşturmak için:

1. Deneme tuvalinin alt kısmındaki **Çalıştır**' a tıklayın.
2. Çalıştırma tamamlandığında **Web Hizmeti Dağıt** ' a tıklayın ve **Web Hizmeti Dağıt [yeni]** seçeneğini belirleyin.
3. Deneme dağıtımı sayfasında, Web hizmetiniz için bir ad girin ve bir fiyatlandırma planı seçin ve **Dağıt**' a tıklayın.
4. **Hızlı başlangıç** sayfasında, **tüketme**' ye tıklayın.
5. **Örnek kod** bölümünde **Batch**' e tıklayın.
6. C# Visual Studio 'da bir konsol uygulaması oluşturun: **Yeni** > **Project** > **Visual C#**  > **Windows Klasik Masaüstü** > **konsol uygulaması (.NET Framework)** .
7. C# Örnek kodu kopyalayıp program.cs dosyanıza yapıştırın.
8. *Apikey* değişkeninin değerini **temel tüketim bilgisi** bölümünde bulunan **birincil anahtarla** güncelleştirin.
9. *ScoreRequest* bildirimini bulun ve *verileri Içeri* aktarma ve *veri verme* modüllerine geçirilen Web hizmeti parametrelerinin değerlerini güncelleştirin. Bu durumda, özgün sorguyu kullanırsınız, ancak yeni bir tablo adı tanımlarsınız.

        var scoreRequest = new
        {
            Inputs = new Dictionary<string, StringTable>()
            {
            },
            GlobalParameters = new Dictionary<string, string>() {
                { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable3" },
            }
        };
10. Uygulamayı çalıştırın.

