---
title: Azure Data Factory maliyetlerini planlayın ve yönetin
description: Bu makalede Azure Data Factory maliyetlerini nasıl planlayabileceğinizi ve yönetebileceğiniz açıklanmaktadır
author: shirleywangmsft
ms.author: shwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/14/2020
ms.openlocfilehash: d89275acdb8e13a792ea3166d874fabf911bb686
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100372901"
---
# <a name="plan-and-manage-costs-for-azure-data-factory"></a>Azure Data Factory maliyetlerini planlayın ve yönetin

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory, bulut ölçeği için derlenmiş bir sunucusuz ve elastik veri tümleştirme hizmetidir.  Bu, yoğun yük için plan yapmanız gereken sabit boyutlu bir işlem olmadığı anlamına gelir; işlem başına isteğe bağlı olarak ne kadar kaynak ayrılacağını belirtmeniz gerekir. Bu, ETL işlemlerini çok daha ölçeklenebilir bir şekilde tasarlamanıza olanak sağlar. Ayrıca, ADF, tüketim tabanlı bir planda faturalandırılır ve bu, yalnızca kullandığınız kadar ödemenizi gösterir.

Bu makalede, Azure Data Factory maliyetlerini nasıl planlayabileceğinizi ve yönetebileceğiniz açıklanır.

*   İlk olarak, ETL projesinin başlangıcında, kavram kanıtı yapın ve maliyetleri tahmin etmek için işlem hattı başına tüketim ve Fiyatlandırma Hesaplayıcı birleşimini kullanın.
*   İşlem hatlarınızı üretime dağıttıktan sonra, maliyet yönetimi özelliklerini kullanarak bütçeleri ayarlayabilir ve maliyetleri izleyebilirsiniz. Ayrıca Tahmini maliyetleri gözden geçirebilir ve harcama eğilimlerini belirleyebilirsiniz.
*   Ayrıca, hangi işlem hatlarını ve hangi etkinliklerin costliest olduğunu anlamak ve maliyet azaltma adaylarını belirlemek için işlem hattı başına tüketimi ve etkinlik başına tüketim bilgilerini görüntüleyebilirsiniz.

## <a name="estimate-costs-using-pipeline-and-activity-run-consumption-and-pricing-calculator"></a>İşlem hattı ve etkinlik çalıştırma tüketimini ve fiyatlandırma hesaplayıcısını kullanarak maliyetleri tahmin etme

Azure Data Factory ' de ETL iş yükünüzü çalıştırmanın maliyetinin bir tahminini almak için [ADF Fiyatlandırma Hesaplayıcı](https://azure.microsoft.com/pricing/calculator/?service=data-factory) ' yı kullanabilirsiniz.  Hesaplayıcı 'ı kullanmak için, etkinlik çalıştırmaları sayısı, veri tümleştirme birimi saati sayısı, veri akışı için kullanılan işlem türü, çekirdek sayısı, örnek sayısı, yürütme süresi vb. gibi ayrıntıları da yazmanız gerekir.

Fiyatlandırma Hesaplayıcı için sık sorulan sorulardan biri, giriş olarak hangi değerlerin kullanılması gerektiğine yöneliktir.  Kavram kanıtı aşamasında, çeşitli ADF ölçümlerine yönelik tüketimi anlamak için örnek veri kümeleri kullanarak deneme çalıştırmaları gerçekleştirebilirsiniz.  Örnek veri kümesinin tüketimine bağlı olarak, tam veri kümesi ve işlem kullanım zamanlaması için tüketimi de etkinleştirebilirsiniz.

> [!NOTE]
> Aşağıdaki örneklerde kullanılan fiyatlar kuramsal değildir ve gerçek fiyatlandırmaya yönelik değildir.

Örneğin, AWS S3 'ten Azure Data Lake Gen2 'e günlük 1 TB veri taşımanız gerektiğini varsayalım.  Veri alma aktarım hızını ölçmek ve ilgili fatura tüketimini anlamak için 100 GB 'lık veri taşımaya yönelik POC 'yi gerçekleştirebilirsiniz.

Örnek kopyalama etkinliği çalıştırma ayrıntısı aşağıda verilmiştir (gerçek mesafğiniz, belirli veri kümenizin şekline, ağ hızlarına, S3 hesabındaki çıkış limitlerine, ADLS 2. ve diğer etkenlere göre değişir).

![S3 kopya çalıştırması](media/plan-manage-costs/s3-copy-run-details.png)

İşlem [hattı çalıştırma düzeyinde tüketim izlemeyi](#monitor-consumption-at-pipeline-run-level)kullanarak, karşılık gelen veri taşıma ölçümü tüketim miktarları ' nı görebilirsiniz:

![S3 kopya ardışık düzen tüketimi](media/plan-manage-costs/s3-copy-pipeline-consumption.png)

Bu nedenle, tüm ay için günde 1 TB taşımak için gereken toplam DIU-saat sayısı şunlardır:

1,2667 (DIU-saat) * (1 TB/100 GB) * 30 (bir ayda gün) = 380 DIU-saat

Artık Aylık faturanızı tahmin etmek için, 30 etkinlik çalıştırması ve 380 DIU-saat ' i ADF fiyatlandırma hesaplayıcıya takabilirsiniz:

![S3 Copy Fiyatlandırma Hesaplayıcı](media/plan-manage-costs/s3-copy-pricing-calculator.png)

## <a name="use-budgets-and-cost-alerts"></a>Bütçe ve maliyet uyarılarını kullanma

Maliyetleri yönetmek için [bütçeler](../cost-management-billing/costs/tutorial-acm-create-budgets.md) oluşturabilir ve paydaşları harcama anormallikleri ve fazla harcama riskleri ile ilgili otomatik olarak bilgilendiren uyarılar oluşturabilirsiniz.  Uyarılar, bütçe ve maliyet eşiklerine kıyasla harcamaya göre belirlenir.  Bir bütçe oluşturduğunuzda, kaynak KIMLIĞI ve ölçüm adı gibi ek filtreler ekleyerek bunu abonelik düzeyinde ya da daha düşük bir ayrıntı düzeyinde yapabilirsiniz.  Ancak bir fabrika içindeki bireysel işlem hatları için bütçeler oluşturamazsınız.

## <a name="monitor-costs-at-factory-level"></a>Fabrika düzeyinde maliyetleri izleme

Azure Data Factory kullanmaya başladığınızda, Azure portal [Maliyet Analizi](../cost-management-billing/costs/quick-acm-cost-analysis.md) bölmesinde tahakkuk eden maliyetleri görebilirsiniz.

1. [Maliyet analizini](../cost-management-billing/costs/quick-acm-cost-analysis.md)görüntülemek Için, **maliyet yönetimi + faturalama** penceresini açın, menüden **maliyet yönetimi** ' ni seçin ve ardından **Açık maliyet analizi**' ni seçin.
2. Varsayılan görünüm, geçerli ayın birikmiş maliyetlerini gösterir.  Farklı bir zaman aralığına ve günlük veya aylık gibi farklı bir ayrıntı düzeyine geçiş yapabilirsiniz.
3. Azure Data Factory gibi tek bir hizmetin maliyetlerini daraltmak için, **Filtre Ekle** ' yi ve ardından **hizmet adı**' nı seçin.  Ardından listeden **Azure Data Factory v2** 'yi seçin.
4. Belirli bir fabrika örneği ve belirli ADF ölçüm ayrıntı düzeyi için maliyeti çözümlemek üzere ek filtreler ekleyebilirsiniz.

   ![Maliyet analizi](media/plan-manage-costs/cost-analysis.png)

## <a name="monitor-consumption-at-pipeline-run-level"></a>İşlem hattı çalıştırma düzeyinde tüketimi izleyin

İşlem hattınızda sahip olduğunuz etkinlik türlerine, ne kadar veri taşıdığınız ve dönüştürmekte olduğunuza ve dönüştürmenin karmaşıklığına bağlı olarak, bir işlem hattının yürütülmesi Azure Data Factory farklı faturalandırma ölçümlerini alır.

Azure Data Factory kullanıcı deneyiminde bireysel işlem hattı çalıştırmaları için farklı ölçümler için tüketim miktarını görüntüleyebilirsiniz. İzleme deneyimini açmak için [Azure Portal](https://portal.azure.com/)Veri Fabrikası dikey penceresinde **izleyici & Yönet** kutucuğunu seçin. Zaten ADF UX kullanıyorsanız sol kenar çubuğundaki **izleyici** simgesine tıklayın. Varsayılan izleme görünümü, işlem hattı çalıştırmaları listesidir.

İşlem hattı adının yanındaki **Tüketim** düğmesine tıklamak, işlem hattınızdaki tüm etkinliklerde toplanan işlem hattı çalıştırmasıyla ilgili tüketimin gösterildiği bir açılır pencere görüntüler.

![İşlem hattı çalıştırma tüketimi](media/plan-manage-costs/pipeline-run-consumption.png)

![Ardışık düzen tüketim ayrıntıları](media/plan-manage-costs/pipeline-consumption-details.png)

İşlem hattı çalıştırma tüketim görünümü, belirli bir işlem hattı çalışmasının her ADF ölçümü için tüketilen miktarı gösterir, ancak size faturalandırılan miktar, kullandığınız Azure hesabının türüne ve kullanılan para birimi türüne bağlı olduğundan, ücretlendirilen fiili fiyatı göstermez.  Desteklenen hesap türlerinin tam listesini görüntülemek için, bkz. [Maliyet Yönetimi verilerini anlama](../cost-management-billing/costs/understand-cost-mgt-data.md).

## <a name="monitor-consumption-at-activity-run-level"></a>Etkinlik çalıştırma düzeyinde tüketimi izleyin
İşlem hattı çalıştırma düzeyinde toplanmış tüketimi anladığınızda, daha fazla ayrıntıya gitme ve ardışık düzen içindeki en pahalı etkinlik olduğunu belirlemeniz gereken senaryolar vardır.

Etkinlik çalıştırma düzeyinde tüketimi görmek için data factory **yazarıza & Monitor** Kullanıcı arabirimine gidin. İşlem hattı çalıştırmalarının bir listesini gördüğünüz **izleyici** sekmesinden, işlem hattı çalıştırmasında etkinlik çalıştırmaları listesine erişmek için işlem **hattı adı** bağlantısına tıklayın.  Etkinlik adının yanındaki **Çıkış** düğmesine tıklayın ve JSON çıkışında **bilmi Duration** özelliğini arayın:

Bir kopyalama etkinliği çalıştırmasında bir örnek aşağıda verilmiştir:

![Çıkışı Kopyala](media/plan-manage-costs/copy-output.png)

Aşağıda, bir eşleme veri akışı etkinliğinin çalıştırıldığı bir örnek verilmiştir:

![Veri akışı çıkışı](media/plan-manage-costs/dataflow-output.png)

## <a name="next-steps"></a>Sonraki adımlar

Fiyatlandırmanın Azure Data Factory nasıl çalıştığı hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Azure Data Factory fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/data-factory/ssis/)
- [Örnekler aracılığıyla Azure Data Factory anlama](./pricing-concepts.md)
- [Fiyatlandırma Hesaplayıcı Azure Data Factory](https://azure.microsoft.com/pricing/calculator/?service=data-factory)