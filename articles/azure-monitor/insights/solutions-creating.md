---
title: Azure 'da bir yönetim çözümü oluşturun | Microsoft Docs
description: Yönetim çözümleri, Azure 'da müşterilerin Log Analytics çalışma alanına ekleyebilmeleri için paketlenmiş yönetim senaryolarını içerir.  Bu makalede, kendi ortamınızda kullanılmak üzere yönetim çözümlerini nasıl oluşturabileceğiniz veya müşterileriniz için kullanılabilir hale getirdiğiniz hakkında ayrıntılar sağlanmaktadır.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2017
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 97472a65af6eb2c5c2da93d93f38450cc021f680
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555295"
---
# <a name="design-and-build-a-management-solution-in-azure-preview"></a>Azure 'da bir yönetim çözümü tasarlama ve oluşturma (Önizleme)
> [!NOTE]
> Bu, şu anda önizleme aşamasında olan Azure 'da yönetim çözümleri oluşturmaya yönelik bir belgedir. Aşağıda açıklanan tüm şemalarla değişiklik yapılır.

[Yönetim çözümleri]( solutions.md) , müşterilerin Log Analytics çalışma alanına ekleyebilmeleri için paketlenmiş yönetim senaryoları sağlar.  Bu makalede, en yaygın gereksinimlere uygun bir yönetim çözümü tasarlamak ve oluşturmak için temel bir işlem sunulmaktadır.  Yönetim çözümleri oluşturmaya yeni başladıysanız, bu işlemi bir başlangıç noktası olarak kullanabilir ve ardından gereksinimleriniz geliştikçe daha karmaşık çözümler için kavramlardan yararlanabilirsiniz.

## <a name="what-is-a-management-solution"></a>Yönetim çözümü nedir?

Yönetim çözümleri, belirli bir yönetim senaryosuna ulaşmak için birlikte çalışan Azure kaynaklarını içerir.  Çözüm yüklendiğinde içerdikleri kaynakların nasıl yükleneceğine ve yapılandırılacağına ilişkin ayrıntıları içeren [Kaynak Yönetim Şablonları](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md) olarak uygulanır.

Temel strateji, Azure ortamınızda bireysel bileşenleri oluşturarak yönetim çözümünüzü başlatmaya başlamadır.  İşlevselliği düzgün şekilde çalışmaya başladıktan sonra bunları bir [yönetim çözümü dosyasına]( solutions-solution-file.md)paketlemeyi başlatabilirsiniz. 


## <a name="design-your-solution"></a>Çözümünüzü tasarlayın
Bir yönetim çözümü için en yaygın model aşağıdaki diyagramda gösterilmiştir.  Bu düzendeki farklı bileşenler aşağıda açıklanmaktadır.

![Yönetim çözümüne genel bakış](media/solutions-creating/solution-overview.png)


### <a name="data-sources"></a>Veri kaynakları
Bir çözüm tasarlamanın ilk adımı, Log Analytics deposundan gereken verileri belirliyor.  Bu veriler bir [veri kaynağı](../../azure-monitor/platform/agent-data-sources.md) veya [başka bir çözüm]( solutions.md)tarafından toplanabilir veya çözümünüzün onu toplama işlemini sağlaması gerekebilir.

[Log Analytics veri kaynaklarında](../../azure-monitor/platform/agent-data-sources.md)açıklandığı gibi Log Analytics deposunda toplanabilecek birçok farklı veri kaynağı vardır.  Bu, Windows olay günlüğü 'ndeki olayları içerir veya hem Windows hem de Linux istemcilerinin performans sayaçlarına ek olarak Syslog tarafından oluşturulmuştur.  Ayrıca, Azure Izleyici tarafından toplanan Azure kaynaklarından veri toplayabilirsiniz.  

Kullanılabilir veri kaynaklarından herhangi biri üzerinden erişilemeyen verilere ihtiyacınız varsa, REST API çağırabilen herhangi bir istemciden Log Analytics deposuna veri yazmanıza olanak sağlayan [http veri TOPLAYıCı API](../../azure-monitor/platform/data-collector-api.md) 'sini kullanabilirsiniz.  Bir yönetim çözümünde özel veri toplamanın en yaygın yolu, Azure [Otomasyonu 'Nda](../../automation/automation-runbook-types.md) Azure veya dış kaynaklardan gerekli verileri toplayan ve depoya yazmak Için VERI Toplayıcı API 'sini kullanan bir runbook oluşturmaktır.  

### <a name="log-searches"></a>Günlük aramaları
[Günlük aramaları](../../azure-monitor/log-query/log-query-overview.md) Log Analytics deposundaki verileri ayıklamak ve analiz etmek için kullanılır.  Bunlar, kullanıcının depodaki verilerin geçici analizini gerçekleştirmesine izin vermenin yanı sıra görünümler ve uyarılar tarafından kullanılır.  

Herhangi bir görünüm veya uyarı tarafından kullanılmasa bile, Kullanıcı için yararlı olacağını düşündüğünüz sorguları tanımlamanız gerekir.  Bunlar, portalda kayıtlı aramalar olarak kullanılabilir ve bunları özel görünüminizdeki bir [sorgu görselleştirmesi bölümü listesine](../../azure-monitor/platform/view-designer-parts.md#list-of-queries-part) dahil edebilirsiniz.

### <a name="alerts"></a>Uyarılar
[Log Analytics uyarılar](../../azure-monitor/platform/alerts-overview.md) depodaki verilere karşı [günlük aramalarıyla](#log-searches) ilgili sorunları belirler.  Kullanıcıya bildirilir veya yanıt olarak bir eylemi otomatik olarak çalıştırır. Uygulamanız için farklı uyarı koşulları belirlemeniz ve ilgili uyarı kurallarını çözüm dosyanıza eklemeniz gerekir.

Sorun potansiyel olarak bir otomatik işlemle düzeltilemezse, bu düzeltmeyi gerçekleştirmek için genellikle Azure Otomasyonu 'nda bir runbook oluşturacaksınız.  Çoğu Azure hizmeti, runbook 'un bu işlevleri gerçekleştirmek için yararlanması gereken [cmdlet](/powershell/azure/overview) 'lerle yönetilebilir.

Çözümünüz bir uyarıya yanıt olarak dış işlevsellik gerektiriyorsa, bir [Web kancası yanıtı](../../azure-monitor/platform/alerts-metric.md)kullanabilirsiniz.  Bu, uyarıdan bilgi gönderen bir dış Web hizmetini çağırabilmeniz için izin verir.

### <a name="views"></a>Görünümler
Log Analytics görünümler Log Analytics deposundan verileri görselleştirmek için kullanılır.  Her çözüm, genellikle kullanıcının ana panosunda görüntülenen bir [kutucuğa](../../azure-monitor/platform/view-designer-tiles.md) sahip tek bir görünüm içerir.  Görünüm, kullanıcıya toplanan verilerin farklı görselleştirmelerini sağlamak için herhangi bir sayıda [görselleştirme bölümü](../../azure-monitor/platform/view-designer-parts.md) içerebilir.

Daha sonra çözüm dosyanıza eklenmek üzere dışarı aktarabilebilmeniz için [Görünüm tasarımcısını kullanarak özel görünümler oluşturursunuz](../../azure-monitor/platform/view-designer.md) .  


## <a name="create-solution-file"></a>Çözüm dosyası oluştur
Çözümünüzün bir parçası olacak bileşenleri yapılandırdıktan ve test edildikten sonra [çözüm dosyanızı oluşturabilirsiniz]( solutions-solution-file.md).  Çözüm bileşenlerini, dosyadaki diğer kaynaklarla ilişkiler içeren bir [çözüm kaynağı]( solutions-solution-file.md#solution-resource) içeren bir [Kaynak Yöneticisi şablonunda](../../azure-resource-manager/resource-group-authoring-templates.md) uygulayacaksınız.  


## <a name="test-your-solution"></a>Çözümünüzü test etme
Çözümünüzü geliştirirken, çalışma alanınıza yüklemeniz ve test etmeniz gerekir.  Bunu, [Kaynak Yöneticisi şablonları test etmek ve yüklemek](../../azure-resource-manager/resource-group-template-deploy.md)için kullanılabilir yöntemlerden herhangi birini kullanarak yapabilirsiniz.

## <a name="publish-your-solution"></a>Çözümünüzü yayımlayın
Çözümünüzü tamamladıktan ve test edildikten sonra, aşağıdaki kaynaklardan biri aracılığıyla müşteriler tarafından kullanılabilir hale getirebilirsiniz.

- **Azure hızlı başlangıç şablonları**.  [Azure hızlı başlangıç şablonları](https://azure.microsoft.com/resources/templates/) , GitHub aracılığıyla topluluk tarafından katkıda bulunulan Kaynak Yöneticisi şablonlar kümesidir.  [Katkı kılavuzundaki](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE)bilgileri izleyerek çözümünüzü kullanılabilir hale getirebilirsiniz.
- **Azure Marketi**.  [Azure Marketi](https://azuremarketplace.microsoft.com/marketplace/) , çözümünüzü diğer geliştiriciler, ISV 'ler ve BT uzmanlarına dağıtmanıza ve satmanıza olanak tanır.  Çözümünüzü Azure Marketi 'nde nasıl yayımlayacağınızı [ve Azure Marketi 'nde teklif yayımlama ve yönetme](../../marketplace/marketplace-publishers-guide.md)hakkında bilgi edinebilirsiniz.



## <a name="next-steps"></a>Sonraki adımlar
* Yönetim çözümünüz için [bir çözüm dosyası oluşturmayı]( solutions-solution-file.md) öğrenin.
* [Yazma Azure Resource Manager şablonlarının](../../azure-resource-manager/resource-group-authoring-templates.md)ayrıntılarını öğrenin.
* Farklı Kaynak Yöneticisi şablonlarının örnekleri için [Azure hızlı başlangıç şablonlarında](https://azure.microsoft.com/documentation/templates) arama yapın.
