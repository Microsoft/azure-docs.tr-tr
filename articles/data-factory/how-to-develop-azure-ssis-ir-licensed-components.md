---
title: 'Azure-SSIS tümleştirme çalışma zamanı için lisanslı bileşenleri yükler '
description: ISV 'nın Azure-SSIS tümleştirme çalışma zamanı için ücretli veya lisanslı özel bileşenleri nasıl geliştirebileceğinizi ve yükleyebileceğinizi öğrenin
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: f1f8a017153d95beed4979b6059383a41cd6a972
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73673589"
---
# <a name="install-paid-or-licensed-custom-components-for-the-azure-ssis-integration-runtime"></a>Azure-SSIS tümleştirme çalışma zamanı için ücretli veya lisanslı özel bileşenleri yükler

Bu makalede, bir ISV 'nın Azure-SSIS tümleştirme çalışma zamanı 'nda Azure 'da çalışan SQL Server Integration Services (SSIS) paketleri için ücretli veya lisanslı özel bileşenleri nasıl geliştirebileceğiniz ve yükleyebileceği açıklanır.

## <a name="the-problem"></a>Sorun

Azure-SSIS tümleştirme çalışma zamanının doğası, özel bileşenlerin şirket içi yüklemesi için kullanılan tipik lisans yöntemlerinin yetersiz olması için çeşitli güçlükler sunmaktadır. Sonuç olarak, Azure-SSIS IR farklı bir yaklaşım gerektirir.

-   Azure-SSIS IR düğümleri geçici olur ve herhangi bir zamanda ayrılabilir veya serbest bırakılır. Örneğin, maliyeti yönetmek için düğümleri başlatabilir veya durdurabilirsiniz, çeşitli düğüm boyutları arasında ölçeği değiştirebilir veya azaltabilirsiniz. Sonuç olarak, MAC adresi veya CPU KIMLIĞI gibi makineye özgü bilgileri kullanarak bir üçüncü taraf bileşen lisansını belirli bir düğüme bağlamak artık önemli değildir.

-   Ayrıca, düğüm sayısının herhangi bir zamanda küçültülmesi veya genişlebilmesi için Azure-SSIS IR da ölçeğini ölçeklendirebilirsiniz.

## <a name="the-solution"></a>Çözüm

Önceki bölümde açıklanan geleneksel lisans yöntemlerinin sınırlamaları nedeniyle Azure-SSIS IR yeni bir çözüm sağlar. Bu çözüm, üçüncü taraf bileşenlerinin lisans bağlaması ve doğrulanması için Windows ortam değişkenlerini ve SSIS sistem değişkenlerini kullanır. ISV 'Ler, küme KIMLIĞI ve küme düğümü sayısı gibi Azure-SSIS IR için benzersiz ve kalıcı bilgiler almak üzere bu değişkenleri kullanabilir. Bu bilgiyle, ISV 'Ler, bileşenlerinin lisansını *bir küme olarak*bir Azure-SSIS IR bağlayabilirler. Bu bağlama, müşteriler başlatıldığında veya durdurulduğunda, ölçek büyütme veya küçültme, ölçeği büyütme veya küçültme ya da Azure-SSIS IR her türlü şekilde yeniden yapılandırma gibi değişmeyen bir KIMLIK kullanır.

Aşağıdaki diyagramda, bu yeni değişkenleri kullanan üçüncü taraf bileşenleri için tipik yükleme, etkinleştirme ve lisans bağlama ve doğrulama akışları gösterilmektedir:

![Lisanslı bileşenleri yükleme](media/how-to-configure-azure-ssis-ir-licensed-components/licensed-component-installation.png)

## <a name="instructions"></a>Yönergeler
1. ISV 'Ler, lisanslı bileşenlerini çeşitli SKU 'Lara veya katmanlara sunabilir (örneğin, tek düğüm, en fazla 5 düğüm, en fazla 10 düğüm vb.). ISV, müşteriler bir ürün satın alırken buna karşılık gelen ürün anahtarını sağlar. ISV, bir ISV kurulum betiği ve ilişkili dosyaları içeren bir Azure Storage blob kapsayıcısı da sağlayabilir. Müşteriler bu dosyaları kendi depolama kapsayıcılarına kopyalayabilir ve kendi ürün anahtarıyla değiştirebilir (örneğin, `IsvSetup.exe -pid xxxx-xxxx-xxxx`çalıştırarak). Müşteriler daha sonra kapsayıcının SAS URI 'sini parametre olarak Azure-SSIS IR sağlayabilir veya yeniden yapılandırabilir. Daha fazla bilgi için bkz. [Azure-SSIS tümleştirmesi çalışma zamanı için özel kurulum](how-to-configure-azure-ssis-ir-custom-setup.md).

2. Azure-SSIS IR sağlandığında veya yeniden yapılandırdığınızda, ISV kurulumu Windows ortam değişkenlerini sorgulamak için her düğümde çalışır, `SSIS_CLUSTERID` ve `SSIS_CLUSTERNODECOUNT`. Ardından Azure-SSIS IR, bir etkinleştirme anahtarı oluşturmak için, lisanslı ürün için küme KIMLIĞINI ve ürün anahtarını ISV etkinleştirme sunucusuna gönderir.

3. Etkinleştirme anahtarını aldıktan sonra, ISV kurulumu anahtarı her düğümde yerel olarak saklayabilir (örneğin, kayıt defterinde).

4. Müşteriler, Azure-SSIS IR bir düğümünde ISV 'nin lisanslı bileşenini kullanan bir paket çalıştırdığında, paket yerel olarak depolanmış etkinleştirme anahtarını okur ve düğümün küme KIMLIĞIYLE karşılaştırarak doğrular. Paket, isteğe bağlı olarak, küme düğümü sayısını ISV etkinleştirme sunucusuna rapor edebilir.

    Aşağıda, etkinleştirme anahtarını doğrulayan ve küme düğümü sayısını raporlayan bir kod örneği verilmiştir:

    ```csharp
    public override DTSExecResult Validate(Connections, VariableDispenser, IDTSComponentEvents componentEvents, IDTSLogging log) 
                                                                                                                               
    {                                                                                                                             
                                                                                                                               
    Variables vars = null;                                                                                                        
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterID");                                                                           
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterNodeCount");                                                                    
                                                                                                                               
    variableDispenser.GetVariables(ref vars);                                                                                     
                                                                                                                               
    // Validate Activation Key with ClusterID                                                                                     
                                                                                                                               
    // Report on ClusterNodeCount                                                                                                 
                                                                                                                               
    vars.Unlock();                                                                                                                
                                                                                                                               
    return base.Validate(connections, variableDispenser, componentEvents, log);                                                   
                                                                                                                               
    }
    ```

## <a name="isv-partners"></a>ISV iş ortakları

Bu blog [Enterprise Edition, özel kurulum ve SSIS için üçüncü taraf GENIŞLETILEBILIRLIĞI ADF](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Enterprise-Edition-Custom-Setup-and-3rd-Party-Extensibility-for/ba-p/388360)'nin sonundaki Azure-SSIS IR için bileşenlerini ve uzantılarını UYARLAYAN ISV iş ortaklarının bir listesini bulabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

-   [Azure-SSIS tümleştirme çalışma zamanı için özel kurulum](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Azure-SSIS Integration Runtime Enterprise sürümü](how-to-configure-azure-ssis-ir-enterprise-edition.md)
