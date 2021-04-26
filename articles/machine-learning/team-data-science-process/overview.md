---
title: Team Data Science Process nedir?
description: Tahmine dayalı analiz çözümleri ve akıllı uygulamalar sunmak için bir veri bilimi yöntemi sağlar.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: overview
ms.date: 11/17/2020
ms.author: tdsp
ms.custom: previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4a341d33c957af8fe7423bda80f1d34a7f59cdd6
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94738008"
---
# <a name="what-is-the-team-data-science-process"></a>Team Data Science Process nedir?

Ekip veri bilimi süreci (TDSP), tahmine dayalı analiz çözümlerini ve akıllı uygulamaları verimli bir şekilde sunmak için çevik, yinelemeli bir veri bilimi yöntemleridir. TDSP, takım rollerinin en iyi şekilde nasıl çalıştığını önererek ekip işbirliğini ve öğrenilmesine yardımcı olur. TDSP, veri bilimi girişimlerinin başarıyla uygulanmasına yardımcı olmak için Microsoft ve diğer sektör liderlerinden en iyi uygulamaları ve yapıları içerir. Hedef, şirketlerin kendi analiz programlarının avantajlarını tam olarak hayata geçirmesine yardımcı olmaktır.

Bu makalede, TDSP ve ana bileşenlerine ilişkin bir genel bakış sunulmaktadır. Burada, farklı araç türleriyle uygulanabilecek işlemin genel bir açıklamasını sunuyoruz. Projenin yaşam döngüsüne dahil edilen proje görevlerinin ve rollerinin daha ayrıntılı bir açıklaması, ek bağlantılı konular bölümünde verilmiştir. Takımlarımızda TDSP 'yi uygulamak için kullandığımız belirli bir Microsoft araçları ve altyapısı kümesini kullanarak TDSP 'nin nasıl uygulanacağı hakkında yönergeler de sağlanmaktadır.

## <a name="key-components-of-the-tdsp"></a>TDSP 'nin önemli bileşenleri

TDSP aşağıdaki önemli bileşenlere sahiptir:

- **Veri bilimi yaşam döngüsü** tanımı
- **Standartlaştırılmış bir proje yapısı**
- Veri bilimi projeleri için önerilen **altyapı ve kaynaklar**
- Proje yürütmesi için önerilen **Araçlar ve yardımcı programlar**


## <a name="data-science-lifecycle"></a>Veri bilimi yaşam döngüsü

Ekip veri bilimi Işlemi (TDSP), veri bilimi projelerinizin geliştirilmesini yapılandırmak için bir yaşam döngüsü sağlar. Yaşam döngüsü, başarılı projelerin izlediği tam adımları özetler.

[Net DM](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), [kdd](https://wikipedia.org/wiki/Data_mining#Process) veya kuruluşunuzun kendi özel süreci gibi başka bir veri bilimi yaşam döngüsü kullanıyorsanız, görev tabanlı TDSP 'yi bu geliştirme ömürleri bağlamında kullanmaya devam edebilirsiniz. Yüksek düzeyde bu farklı yöntemler çok yaygın bir şekilde yapılır. 

Bu yaşam döngüsü, akıllı uygulamaların bir parçası olarak gönderilen veri bilimi projeleri için tasarlanmıştır. Bu uygulamalar, tahmine dayalı analiz için makine öğrenimi veya yapay zeka modelleri dağıtır. Araştırmacı veri bilimi projeleri veya improvised Analytics projeleri, bu işlemi kullanmanın da avantajlarından yararlanabilir. Ancak bu gibi durumlarda açıklanan adımların bazıları gerekmeyebilir.    

Yaşam döngüsü, projelerin tipik olarak yürütülen ana aşamaları özetler, genellikle yinelemeli olarak:

* **İş anlama**
* **Veri alma ve anlama**
* **Modelleme**
* **Dağıtım**

**Team Data Science işlem yaşam döngüsünün** görsel bir gösterimi aşağıda verilmiştir. 

![Diyagramda, iş anlama, veri alımı/anlama, modelleme ve dağıtım dahil olmak üzere veri bilimi yaşam döngüsü gösterilmektedir.](./media/overview/tdsp-lifecycle2.png) 

TDSP yaşam döngüsünün her aşamasına yönelik hedefler, görevler ve belge yapıtları, [Team Data Science işlem yaşam döngüsü](lifecycle.md) konusunda açıklanmaktadır. Bu görevler ve yapıtlar proje rolleriyle ilişkilendirilir:

- Çözüm mimarı
- Proje yöneticisi
- Veri mühendisi
- Veri bilimcisi
- Uygulama geliştiricisi
- Proje lideri 

Aşağıdaki diyagramda, bu roller (dikey eksende) için yaşam döngüsünün (yatay eksende) her bir aşamasında ilişkili görevlerin (mavi renkli) ve yapıtların (yeşil renkte) bir ızgara görünümü sunulmaktadır. 

[![TDSP-roller-ve-görevler](./media/overview/tdsp-tasks-by-roles.png)](./media/overview/tdsp-tasks-by-roles.png#lightbox)

## <a name="standardized-project-structure"></a>Standartlaştırılmış proje yapısı

Tüm projelerin bir dizin yapısını paylaşmasını ve proje belgeleri için şablon kullanmasını sağlamak, takım üyelerinin projelerle ilgili bilgileri bulmasını kolaylaştırır. Tüm kod ve belgeler, Ekip işbirliğini sağlamak için git, TFS veya alt sürüm gibi bir sürüm denetim sisteminde (VC) depolanır. Jira, R, ve Azure DevOps gibi çevik bir proje izleme sisteminde bulunan görevleri ve özellikleri izlemek, kodun tek tek özellikler için daha yakından izlenmesine olanak tanır. Bu tür izleme ekiplerin daha iyi maliyet tahminleri almasına de olanak sağlar. TDSP, sürüm oluşturma, bilgi güvenliği ve işbirliği için VC üzerinde her proje için ayrı bir depo oluşturulmasını önerir. Tüm projelerin standartlaştırılmış yapısı, kuruluş genelinde kurumsal bilgi oluşturmaya yardımcı olur.

Klasör yapısına ve gerekli belgelere standart konumlarda şablonlar sağlıyoruz. Bu klasör yapısı, veri araştırması ve özellik ayıklama için kod içeren dosyaları düzenler ve bu model yinelemelerini kaydeder. Bu şablonlar, takım üyelerinin diğerlerinin yaptığı işleri anlamalarına ve takımlara yeni üyeler eklemesine daha kolay hale getirir. Belge şablonlarını markın biçiminde görüntülemek ve güncelleştirmek kolaydır. Her bir proje için önemli sorulara denetim listeleri sağlamak üzere şablonlar kullanın ve bu da sorunun iyi tanımlandığını ve teslim edilebilirler beklenen kaliteyi karşıladığından emin olur. Örnekler arasında şunlar yer almaktadır:

- projenin iş sorununu ve kapsamını belgeleyici bir proje
- ham verilerin yapısını ve istatistiklerini belgelemek için veri raporları
- türetilmiş özellikleri belgelemek için rapor Modelleme
- ROC eğrileri veya MSE gibi model performans ölçümleri


[![TDSP-dizinler](./media/overview/tdsp-dir-structure.png)](./media/overview/tdsp-dir-structure.png#lightbox)

Dizin yapısı [GitHub](https://github.com/Azure/Azure-TDSP-ProjectTemplate)'dan kopyalanabilir.

## <a name="infrastructure-and-resources-for-data-science-projects"></a>Veri bilimi projeleri için altyapı ve kaynaklar  

TDSP, paylaşılan analizler ve depolama altyapısını yönetmeye yönelik öneriler sağlar:

- veri kümelerini depolamak için bulut dosya sistemleri 
- veritabanlarında
- büyük veri (SQL veya Spark) kümeleri 
- Machine Learning hizmeti 

Ham ve işlenen veri kümelerinin depolandığı analiz ve depolama altyapısı, bulutta veya şirket içinde olabilir. Bu altyapı, tekrarlanabilir analizler sunar. Ayrıca, tutarsızlıklara ve gereksiz altyapı maliyetlerine neden olabilecek yinelemeyi de önler. Araçlar, paylaşılan kaynakları sağlamak, onları izlemek ve her bir ekip üyesinin bu kaynaklara güvenli bir şekilde bağlanmasına izin vermek için sağlanır. Ayrıca, proje üyelerinin tutarlı bir işlem ortamı oluşturması için de iyi bir uygulamadır. Daha sonra, farklı takım üyeleri denemeleri çoğaltabilir ve doğrulayabilir.

İşte birden fazla proje üzerinde çalışan ve çeşitli bulut Analizi altyapı bileşenlerini paylaşan bir takım örneği.

[![TDSP-altyapı](./media/overview/tdsp-analytics-infra.png)](./media/overview/tdsp-analytics-infra.png#lightbox) 


## <a name="tools-and-utilities-for-project-execution"></a>Proje yürütmesi için araçlar ve yardımcı programlar

Çoğu kuruluşta işlemlere giriş yapmak zor. Veri bilimi sürecini ve yaşam döngüsünü uygulamak için sağlanan araçlar, üzerindeki engelleri azaltmaya ve bunların tutarlılığını artırmaya yardımcı olur. TDSP, bir ekip içinde TDSP benimseme başlatmaya yönelik ilk araç ve komut dosyası kümesi sağlar. Veri araştırması ve temel modelleme gibi veri bilimi yaşam döngüsünün bazı yaygın görevlerinin otomatikleştirilmesine de yardımcı olur. Kişilerin, ekip paylaşılan kod deposunda paylaşılan araçlar ve yardımcı programlar katkıda bulunmak için iyi tanımlanmış bir yapı mevcuttur. Bu kaynaklar daha sonra ekip veya kuruluş içindeki diğer projelere göre yararlanılabilir olabilir.  Microsoft, hem açık kaynaklı (Python, R, ONNX, hem de genel derin öğrenme çerçevelerini) hem de Microsoft 'un kendi araçlarını (Oto ml) destekleyen [Azure Machine Learning](../index.yml) içinde kapsamlı araçlar sağlar.


## <a name="next-steps"></a>Sonraki adımlar

[Ekip veri bilimi işlemi: roller ve görevler](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/roles-tasks.md) Bu işlemi standartlaştırın bir veri bilimi ekibi için anahtar personeli rollerini ve bunlarla ilişkili görevleri özetler. 
