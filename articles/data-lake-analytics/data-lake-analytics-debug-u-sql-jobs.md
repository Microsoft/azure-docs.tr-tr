---
title: Azure Data Lake C# U-SQL işleri için hata ayıklama kodu
description: Bu makalede, Visual Studio için Azure Data Lake Araçları kullanarak U-SQL başarısız olan bir köşeyi nasıl ayıklayacağınız açıklanır.
services: data-lake-analytics
ms.service: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.topic: conceptual
ms.date: 11/30/2017
ms.openlocfilehash: 72239fc1679d2ebbfd9c9b5be6b79b58efb760cb
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71315810"
---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>Başarısız U-SQL C# işleri için Kullanıcı tanımlı kodda hata ayıkla

U-SQL, kullanarak C#bir genişletilebilirlik modeli sağlar. U-SQL komut dosyalarında, işlevleri çağırmak C# ve SQL benzeri bildirime dayalı dilin desteklemediği analitik işlevler gerçekleştirmek kolaydır. U-SQL genişletilebilirliği hakkında daha fazla bilgi edinmek için, bkz. [u-SQL programlama kılavuzu](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#use-user-defined-functions-udf). 

Uygulamada, herhangi bir kod hata ayıklama gerektirebilir, ancak bulut üzerinde sınırlı günlük dosyaları olan özel kodla dağıtılmış bir işin hata ayıklaması zor olabilir. [Visual Studio için Azure Data Lake araçları](https://aka.ms/adltoolsvs) , özel kodunuzda oluşan hatalarda daha kolay hata ayıklamanıza yardımcı olan **başarısız köşe hata ayıklaması**adlı bir özellik sağlar. U-SQL işi başarısız olduğunda, hizmet hata durumunu korur ve araç, hata ayıklama için bulut hatası ortamını yerel makineye indirmenize yardımcı olur. Yerel indirme, tüm giriş verileri ve Kullanıcı kodları dahil olmak üzere tüm bulut ortamını yakalar.

Aşağıdaki videoda Visual Studio için Azure Data Lake Araçları 'de başarısız köşe hata ayıklaması gösterilmektedir.

> [!VIDEO https://www.youtube.com/embed/3enkNvprfm4]
>

> [!IMPORTANT]
> Visual Studio, bu özelliği kullanmak için aşağıdaki iki güncelleştirmeyi gerektirir: [Microsoft Visual C++ 2015 yeniden dağıtılabilir güncelleştirme 3](https://www.microsoft.com/en-us/download/details.aspx?id=53840) ve [Windows Için evrensel C çalışma zamanı](https://www.microsoft.com/download/details.aspx?id=50410).
>

## <a name="download-failed-vertex-to-local-machine"></a>Başarısız köşeyi yerel makineye indir

Visual Studio için Azure Data Lake Araçları içinde başarısız bir işi açtığınızda, hata sekmesinde ayrıntılı hata iletileriyle birlikte sarı bir uyarı çubuğu görürsünüz.

1. Tüm gerekli kaynakları ve giriş akışlarını indirmek için **İndir** ' e tıklayın. İndirme tamamlanmazsa, **yeniden dene**' ye tıklayın.

2. Yükleme tamamlandıktan sonra, yerel bir hata ayıklama ortamı oluşturmak için **Aç** ' a tıklayın. Yeni bir hata ayıklama çözümü açılacak ve Visual Studio 'da açık bir çözümünüz varsa, lütfen hata ayıklamadan önce kaydettiğinizden ve kapandığınızdan emin olun.

![Azure Data Lake Analytics U-SQL hata ayıklama Visual Studio indirme köşesi](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

## <a name="configure-the-debugging-environment"></a>Hata ayıklama ortamını yapılandırma

> [!NOTE]
> Hata ayıklamadan önce, özel durum ayarları penceresinde **ortak dil çalışma zamanı özel durumlarını** denetlediğinizden emin olun (**Ctrl + Alt + E**).

![Azure Data Lake Analytics U-SQL hata ayıklama Visual Studio ayarı](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

Yeni başlatılan Visual Studio örneğinde, Kullanıcı tanımlı C# kaynak kodu olabilir veya bulmayabilir:

1. [Çözüm içinde kaynak kodumu bulabiliyorum](#source-code-is-included-in-debugging-solution)

2. [Çözümde Kaynak kodumu bulamıyorum](#source-code-is-not-included-in-debugging-solution)

### <a name="source-code-is-included-in-debugging-solution"></a>Kaynak kodu hata ayıklama çözümüne dahildir

C# Kaynak kodun yakalandığı iki durum vardır:

1. Kullanıcı kodu, arka plan kod dosyasında tanımlanır (genellikle bir U- `Script.usql.cs` SQL projesinde adlandırılır).

2. Kullanıcı kodu U-SQL uygulaması C# için sınıf kitaplığı projesinde tanımlanır ve **hata ayıklama bilgileri**ile derleme olarak kaydedilir.

Kaynak kodu çözüme içeri aktarıldıysa, sorunu gidermek için Visual Studio hata ayıklama araçları 'nı (izleme, değişkenler vb.) kullanabilirsiniz:

1. Hata ayıklamaya başlamak için **F5**'e basın. Kod bir özel durum tarafından durduruluncaya kadar çalışır.

2. Kaynak kodu dosyasını açın ve kesme noktalarını ayarlayın ve ardından adım adım hata ayıklama için **F5** tuşuna basın.

    ![Azure Data Lake Analytics U-SQL hata ayıklama özel durumu](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

### <a name="source-code-is-not-included-in-debugging-solution"></a>Kaynak kodu hata ayıklama çözümüne dahil değildir

Kullanıcı kodu, arka plan kod dosyasına dahil edilmediyseniz veya derlemeyi **hata ayıklama bilgileri**ile kaydetmediyseniz, kaynak kodu hata ayıklama çözümüne otomatik olarak dahil edilmez. Bu durumda, kaynak kodunuzu eklemek için ek adımlara ihtiyacınız vardır:

1. **' VertexDebug ' öğesine sağ tıklayın > mevcut projeyi > ekleyin...** derleme kaynak kodunu bulun ve projeyi hata ayıklama çözümüne ekleyin.

    ![Azure Data Lake Analytics U-SQL hata ayıklama proje ekleme](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. **Failedvertexdebughost** projesi için proje klasörü yolunu alın. 

3. **Eklenen derleme kaynak kodu projesi > Özellikler**' e sağ tıklayın, soldaki **derleme** sekmesini seçin ve sonra da \bin\Debug Ile biten kopyalanmış yolu **Çıkış > çıkış yolu**olarak yapıştırın. Son çıkış yolu gibidir `<DataLakeTemp path>\fd91dd21-776e-4729-a78b-81ad85a4fba6\loiu0t1y.mfo\FailedVertexDebug\FailedVertexDebugHost\bin\Debug\`.

    ![Azure Data Lake Analytics U-SQL hata ayıklama pdb yolunu ayarla](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

Bu ayarlardan sonra, **F5** ve kesme noktalarıyla hata ayıklamaya başlayın. Sorunu gidermek için Visual Studio hata ayıklama araçları 'nı (izleme, değişkenler vb.) de kullanabilirsiniz.

> [!NOTE]
> Güncelleştirilmiş. pdb dosyalarını oluşturmak için kodu değiştirdikten sonra derleme kaynak kodu projesini yeniden derleyin.

## <a name="resubmit-the-job"></a>İşi yeniden gönderin

Hata ayıkladıktan sonra, proje başarıyla tamamlanırsa çıkış penceresinde aşağıdaki ileti görüntülenir:

    The Program 'LocalVertexHost.exe' has exited with code 0 (0x0).

![Azure Data Lake Analytics U-SQL hata ayıklama başarılı](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

Başarısız işi yeniden göndermek için:

1. Arka plan kod çözümleri olan işler için C# kodu arka plan kod kaynak dosyasına (genellikle `Script.usql.cs`) kopyalayın.

2. Derlemeleri olan işler için, hata ayıklama çözümünde derleme kaynak kodu projesine sağ tıklayın ve güncelleştirilmiş. dll derlemelerini Azure Data Lake kataloğunuza kaydedin.

3. U-SQL işini yeniden gönderin.

## <a name="next-steps"></a>Sonraki adımlar

- [U-SQL programlama kılavuzu](data-lake-analytics-u-sql-programmability-guide.md)
- [Azure Data Lake Analytics işleri için U-SQL Kullanıcı tanımlı işleçler geliştirme](data-lake-analytics-u-sql-develop-user-defined-operators.md)
- [Yerel çalıştırma ve Azure Data Lake U-SQL SDK’sını kullanarak U-SQL işlerini test etme ve hatalarını ayıklama](data-lake-analytics-data-lake-tools-local-run.md)
- [Olağan dışı yinelenen bir işte sorun giderme](data-lake-analytics-data-lake-tools-debug-recurring-job.md)
