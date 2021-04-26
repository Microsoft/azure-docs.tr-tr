---
title: Azure DevOps Services Team Data Science Işlemiyle veri bilimi kodunu test etme
description: Team Data Science Process ve Azure DevOps Services ile Azure 'da, uya yetişkin gelir tahmini veri kümesiyle Azure 'da veri bilimi kodu testi
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=weig, previous-ms.author=weig
ms.openlocfilehash: 66853b580ba1b619bf4cc995e05bd3cfaff07cfb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102565878"
---
# <a name="data-science-code-testing-on-azure-with-the-team-data-science-process-and-azure-devops-services"></a>Team Data Science Işlemi ve Azure DevOps Services Azure 'da veri bilimi kodu testi
Bu makale, bir veri bilimi iş akışındaki kodu test etmek için ön yönergeleri sağlar. Bu tür testler, veri bilimcilerinin, kodunuzun kalitesini ve beklenen sonucunu denetlemek için sistematik ve verimli bir yol sunar. Kod testinin nasıl yapılacağını göstermek için daha önce yayımladığımız [UCI yetişkin gelir veri kümesini kullanan](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) bir ekip veri bilimi IŞLEMI (TDSP) projesi kullanıyoruz. 

## <a name="introduction-on-code-testing"></a>Kod testi ile ilgili giriş
"Birim testi", yazılım geliştirme için bir longbir uygulamadır. Ancak veri bilimi için, "birim testi" ne anlama geldiğini ve bir veri bilimi yaşam döngüsünün farklı aşamaları için kodu test etmeyi nasıl test etmemelisiniz, örneğin:

* Veri hazırlama
* Veri kalitesi incelemesi
* Modelleme
* Model dağıtımı 

Bu makale, "birim testi" terimini "kod testi" ile değiştirir. Bu, bir veri bilimi yaşam döngüsünün belirli bir adımından kodun beklenildiği gibi sonuçlar üretmesine yardımcı olan işlevler olarak test eder. Testi yazan kişi, işlevin sonucuna bağlı olarak "beklendiği gibi" ne olduğunu tanımlar. Örneğin, veri kalitesi denetimi veya modelleme.

Bu makalede, yararlı kaynaklar olarak başvurular sağlanmaktadır.

## <a name="azure-devops-for-the-testing-framework"></a>Test çerçevesi için Azure DevOps
Bu makalede, Azure DevOps kullanılarak testlerin nasıl gerçekleştirileceği ve otomatikleştirdiği açıklanır. Alternatif araçları kullanmaya karar verebilirsiniz. Ayrıca, Azure DevOps ve yapı aracılarını kullanarak otomatik bir derlemeyi nasıl ayarlayacağız de göstereceğiz. Derleme aracıları için Azure veri bilimi sanal makinelerini (DSVMs) kullanacağız.

## <a name="flow-of-code-testing"></a>Kod testi akışı
Bir veri bilimi projesindeki test kodunun genel iş akışı şuna benzer: 

![Kod testi akış grafiği](./media/code-test/test-flow-chart.PNG)

    
## <a name="detailed-steps"></a>Ayrıntılı adımlar

Derleme Aracısı ve Azure DevOps kullanarak kod testini ve otomatikleştirilmiş bir derlemeyi ayarlamak ve çalıştırmak için aşağıdaki adımları kullanın:

1. Visual Studio Desktop uygulamasında bir proje oluşturun:

    ![Visual Studio 'da "yeni proje oluştur" ekranı](./media/code-test/create_project.PNG)

   Projenizi oluşturduktan sonra sağ bölmede Çözüm Gezgini görürsünüz:
    
    ![Proje oluşturma adımları](./media/code-test/create_python_project_in_vs.PNG)

    ![Çözüm Gezgini](./media/code-test/solution_explorer_in_vs.PNG)

1. Proje kodunuzu Azure DevOps proje kod deposuna akışla: 

    ![Proje kod deposu](./media/code-test/create_repo.PNG)

1. Veri alma, özellik Mühendisliği ve etiket sütunları oluşturma gibi bazı veri hazırlama işleri yapmayı istediğinizi varsayalım. Kodunuzun, beklediğinizi sonuçları ürettirken emin olmak istiyorsunuz. Veri işleme kodunun düzgün çalışıp çalışmadığını test etmek için kullanabileceğiniz bazı kodlar aşağıda verilmiştir:

    * Sütun adlarının doğru olup olmadığını denetleyin:
    
      ![Eşleşen sütun adları için kod](./media/code-test/check_column_names.PNG)

    * Yanıt düzeylerinin doğru olup olmadığını denetleyin:

      ![Eşleşen düzeyler için kod](./media/code-test/check_response_levels.PNG)

    * Yanıt yüzdesinin makul olduğunu kontrol edin:

      ![Yanıt yüzdesi kodu](./media/code-test/check_response_percentage.PNG)

    * Verilerdeki her bir sütunun eksik oranını denetleyin:
    
      ![Eksik hız için kod](./media/code-test/check_missing_rate.PNG)


1. Veri işleme ve özellik Mühendisliği işini tamamladıktan sonra iyi bir model eğitimişseniz, eğitilen modelin yeni veri kümelerini doğru bir şekilde puanlayıp elde ettiğinizden emin olun. Etiket değerlerinin tahmin düzeylerini ve dağıtımını denetlemek için aşağıdaki iki testi kullanabilirsiniz:

    * Tahmin düzeylerini denetle:
    
      ![Tahmin düzeylerini denetlemek için kod](./media/code-test/check_prediction_levels.PNG)

    * Tahmin değerlerinin dağıtımını kontrol edin:

      ![Tahmin değerlerini denetlemek için kod](./media/code-test/check_prediction_values.PNG)

1. Tüm test işlevlerini birlikte **test_funcs. Kopyala** adlı bir Python betiğine yerleştirin:

    ![Test işlevleri için Python betiği](./media/code-test/create_file_test_func.PNG)


1. Test kodları hazırlandıktan sonra, Visual Studio 'da test ortamı ayarlayabilirsiniz.

   **Test1.py** adlı bir Python dosyası oluşturun. Bu dosyada, yapmak istediğiniz tüm testleri içeren bir sınıf oluşturun. Aşağıdaki örnek, hazırlanan altı testi göstermektedir:
    
    ![Bir sınıftaki testlerin listesi ile Python dosyası](./media/code-test/create_file_test1_class.PNG)

1. Bu testler, sınıfınızın adından sonra **CodeTEST. testCase** 'i yerleştirirseniz otomatik olarak bulunabilir. Sağ bölmedeki test Gezgini ' ni açın ve **Tümünü Çalıştır**' ı seçin. Tüm testler sırayla çalışır ve testin başarılı olup olmadığını bildirir.

    ![Testleri çalıştırma](./media/code-test/run_tests.PNG)

1. Git komutlarını kullanarak kodunuzu proje deposuna iade edin. En son çalışmanız Azure DevOps 'da kısa bir süre içinde yansıtılır.

    ![Kodu iade etmek için Git komutları](./media/code-test/git_check_in.PNG)

    ![Azure DevOps 'da en son çalışan](./media/code-test/git_check_in_most_recent_work.PNG)

1. Azure DevOps 'da otomatik derleme ve test ayarlama:

    a. Proje deposunda, **Oluştur ve Yayınla**' yı seçin ve ardından yeni bir yapı işlemi oluşturmak Için **+ Yeni** ' yi seçin.

    ![Yeni derleme işlemi başlatma seçimleri](./media/code-test/create_new_build.PNG)

    b. Kaynak kodu konumunuzu, proje adınızı, deponuzu ve şube bilgilerini seçmek için istemleri izleyin.
    
    ![Kaynak, ad, depo ve dal bilgileri](./media/code-test/fill_in_build_info.PNG)

    c. Bir şablon seçin. Python proje şablonu olmadığından, **boş işlem**' i seçerek başlayın. 

    ![Şablonların listesi ve "boş işlem" düğmesi](./media/code-test/start_empty_process_template.PNG)

    d. Derlemeyi adlandırın ve aracıyı seçin. Derleme işlemini gerçekleştirmek için DSVM kullanmak istiyorsanız, varsayılan olarak bu seçeneği belirleyebilirsiniz. Aracıları ayarlama hakkında daha fazla bilgi için bkz. [derleme ve yayın aracıları](/azure/devops/pipelines/agents/agents).
    
    ![Yapı ve aracı seçimleri](./media/code-test/select_agent.PNG)

    e. **+** Bu derleme aşamasına yönelik bir görev eklemek için sol bölmede öğesini seçin. Tüm denetimleri tamamlaması için Python betiğini **test1.py** çalıştıracağız, bu görev Python kodunu çalıştırmak Için bir PowerShell komutu kullanıyor.
    
    ![PowerShell seçiliyken "görev ekleme" bölmesi](./media/code-test/add_task_powershell.PNG)

    f. PowerShell ayrıntılarında, PowerShell 'in adı ve sürümü gibi gerekli bilgileri girin. Tür olarak **satır Içi betiği** seçin. 
    
    **Satır Içi betik** altındaki kutuya **Python test1.py** yazabilirsiniz. Ortam değişkeninin Python için doğru şekilde ayarlandığından emin olun. Python 'un farklı bir sürümüne veya çekirdeğe ihtiyacınız varsa, yolu şekilde gösterildiği gibi açıkça belirtebilirsiniz: 
    
    ![PowerShell ayrıntıları](./media/code-test/powershell_scripts.PNG)

    örneğin: Derleme ardışık düzeni işlemini gerçekleştirmek için **& kuyruğu kaydet** ' i seçin.

    !["& kuyruğunu kaydet" düğmesi](./media/code-test/save_and_queue_build_definition.PNG)

Artık kod deposuna her yeni bir işleme gönderildiğinde, derleme işlemi otomatik olarak başlatılır. Herhangi bir dalı tanımlayabilirsiniz. İşlem, kodda tanımlanan her şeyin düzgün çalıştığından emin olmak için aracı makinesinde **test1.py** dosyasını çalıştırır. 

Uyarılar doğru ayarlandıysa, derleme bittiğinde e-posta ile bildirilir. Ayrıca, Azure DevOps 'daki yapı durumunu da denetleyebilirsiniz. Başarısız olursa, yapı ayrıntılarını denetleyebilir ve hangi parçanın bozuk olduğunu bulabilirsiniz.

![Derleme başarısı e-posta bildirimi](./media/code-test/email_build_succeed.PNG)

![Derleme başarısı için Azure DevOps bildirimi](./media/code-test/vs_online_build_succeed.PNG)

## <a name="next-steps"></a>Sonraki adımlar
* Veri bilimi senaryoları için birim testlerinin somut örnekleri için [UCI gelir tahmin deposuna](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) bakın.
* Yukarıdaki ana hattı ve örnekleri, kendi veri bilimi projelerinizde UCı gelir tahmin senaryosundan izleyin.

## <a name="references"></a>Başvurular
* [Team Data Science Process](./index.yml)
* [Visual Studio test araçları](https://www.visualstudio.com/vs/features/testing-tools/)
* [Azure DevOps sınama kaynakları](https://www.visualstudio.com/team-services/)
* [Veri Bilimi Sanal Makineleri](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)