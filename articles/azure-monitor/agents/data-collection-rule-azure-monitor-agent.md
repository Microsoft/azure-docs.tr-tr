---
title: Azure Izleyici Aracısı için veri toplamayı yapılandırma (Önizleme)
description: Azure Izleyici aracısını kullanarak sanal makinelerden veri toplamak için bir veri toplama kuralının nasıl oluşturulacağını açıklar.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/16/2021
ms.openlocfilehash: 884f048ac099cfc6b799fe266172a0eecef3db6f
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478378"
---
# <a name="configure-data-collection-for-the-azure-monitor-agent-preview"></a>Azure Izleyici Aracısı için veri toplamayı yapılandırma (Önizleme)

Veri toplama kuralları (DCR), Azure Izleyici 'ye gelen verileri tanımlar ve nereye gönderileceğini belirtir. Bu makalede, Azure Izleyici Aracısı kullanılarak sanal makinelerden veri toplamak için bir veri toplama kuralının nasıl oluşturulacağı açıklanır.

Veri toplama kurallarının tamamen açıklaması için bkz. [Azure izleyici 'de veri toplama kuralları (Önizleme)](data-collection-rule-overview.md).

> [!NOTE]
> Bu makalede, şu anda önizleme aşamasında olan Azure Izleyici Aracısı ile sanal makineler için verilerin nasıl yapılandırılacağı açıklanır. Genel olarak kullanılabilen aracıların açıklaması ve verilerin toplanması için nasıl kullanılacağı hakkında daha fazla bilgi için bkz. [Azure izleyici aracılarına genel bakış](agents-overview.md) .

## <a name="data-collection-rule-associations"></a>Veri toplama kuralı ilişkilendirmeleri

Bir sanal makineye bir DCR uygulamak için, sanal makine için bir ilişki oluşturursunuz. Bir sanal makinenin birden çok DTU ile ilişkilendirmesi olabilir ve bir DCR ilişkili birden fazla sanal makine olabilir. Bu, her biri belirli bir gereksinimle eşleşen bir dizi DTU tanımlamanızı ve bunları yalnızca uygulandıkları sanal makinelere uygulamanızı sağlar. 

Örneğin, bir iş kolu uygulaması çalıştıran bir sanal makine kümesi ve SQL Server çalıştıran bir ortam düşünün. Tüm sanal makineler için geçerli olan bir varsayılan veri toplama kuralınız ve özel olarak iş kolu uygulaması ve SQL Server için veri toplayacak ayrı veri toplama kuralları olabilir. Sanal makineler için veri toplama kurallarına ait ilişkilendirmeler Aşağıdaki diyagrama benzer şekilde görünür.

![Diyagram, iş kolu uygulaması ve SQL Server için merkezi-ı t-varsayılan ve s için lob-uygulaması adlı veri toplama kurallarıyla ilişkili SQL Server sanal makineleri gösterir.](media/data-collection-rule-azure-monitor-agent/associations.png)



## <a name="create-rule-and-association-in-azure-portal"></a>Azure portal kural ve ilişkilendirme oluşturma

Bir veri toplama kuralı oluşturmak ve aboneliğinizdeki sanal makineleri bu kurala ilişkilendirmek için Azure portal kullanabilirsiniz. Azure Izleyici Aracısı otomatik olarak yüklenir ve henüz yüklenmemiş sanal makineler için yönetilen bir kimlik oluşturulur.

> [!IMPORTANT]
> Şu anda, veri toplama kuralının Kullanıcı tarafından atanan yönetilen kimliğe sahip bir sanal makinede yönetilen bir kimlik oluşturduğunu, Kullanıcı tarafından atanan kimliğin devre dışı bırakıldığını belirten bilinen bir sorun vardır.

Azure portal **Azure izleyici** menüsünde, **Ayarlar** bölümünden **veri toplama kuralları** ' nı seçin. Yeni bir veri toplama kuralı ve ataması eklemek için **Ekle** ' ye tıklayın.

[![Veri toplama kuralları](media/data-collection-rule-azure-monitor-agent/data-collection-rules.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rules.png#lightbox)

Yeni bir kural ve ilişkilendirme kümesi oluşturmak için **Ekle** ' ye tıklayın. Bir **kural adı** girip bir **abonelik** ve **kaynak grubu** belirtin. Bu, DCR 'in nerede oluşturulacağını belirtir. Sanal makineler ve ilişkilendirmeleri, Kiracıdaki herhangi bir abonelikte veya kaynak grubunda bulunabilir.

[![Veri toplama kuralı temelleri](media/data-collection-rule-azure-monitor-agent/data-collection-rule-basics.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-basics.png#lightbox)

**Sanal makineler** sekmesinde, veri toplama kuralı uygulanmış olması gereken sanal makineleri ekleyin. Ortamınızdaki hem Azure sanal makineleri hem de Azure Arc etkin sunucularının listelenmesi gerekir. Azure Izleyici Aracısı, henüz yüklenmemiş sanal makinelere yüklenir.

[![Veri toplama kuralı sanal makineleri](media/data-collection-rule-azure-monitor-agent/data-collection-rule-virtual-machines.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-virtual-machines.png#lightbox)

**Toplama ve teslim** sekmesinde veri kaynağı **Ekle** ' ye tıklayarak veri kaynağı ve hedef kümesi ekleyin. Bir **veri kaynağı türü** seçin ve bu seçilecek ayrıntılar görüntülenir. Performans sayaçları için, önceden tanımlanmış bir nesne kümesi ve örnekleme hızı arasından seçim yapabilirsiniz. Olaylar için, bir dizi günlük veya tesis ve önem düzeyi arasından seçim yapabilirsiniz. 

[![Veri kaynağı temel](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-basic.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-basic.png#lightbox)


[Şu anda desteklenen veri kaynaklarından](azure-monitor-agent-overview.md#data-sources-and-destinations) diğer günlükleri ve performans sayaçlarını belirtmek veya XPath sorgularını kullanarak olayları filtrelemek için **özel**' i seçin. Ardından, toplanacak belirli değerler için bir [XPath ](https://www.w3schools.com/xml/xpath_syntax.asp) belirtebilirsiniz. Örnekler için bkz. [örnek DCR](data-collection-rule-overview.md#sample-data-collection-rule) .

[![Veri kaynağı özel](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-custom.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-custom.png#lightbox)

**Hedef** sekmesinde, veri kaynağı için bir veya daha fazla hedef ekleyin. Windows olay ve Syslog veri kaynakları yalnızca Azure Izleyici günlüklerine gönderebilir. Performans sayaçları, hem Azure Izleyici ölçümleri hem de Azure Izleyici günlüklerine gönderebilir.

[![Hedef](media/data-collection-rule-azure-monitor-agent/data-collection-rule-destination.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-destination.png#lightbox)

Veri toplama kuralının ayrıntılarını ve VM kümesiyle ilişkilendirmeyi gözden geçirmek için **veri kaynağı Ekle** ' ye tıklayın ve ardından **+ Oluştur ' u gözden geçirin** . Oluşturmak için **Oluştur** ' a tıklayın.

> [!NOTE]
> Veri toplama kuralı ve ilişkilendirmeleri oluşturulduktan sonra, verilerin hedeflere gönderilmesi 5 dakikaya kadar sürebilir.

## <a name="limit-data-collection-with-custom-xpath-queries"></a>Özel XPath sorgularıyla veri toplamayı sınırlayın
Log Analytics çalışma alanında toplanan veriler için ücretlendirildiğiniz için, yalnızca gerekli verileri toplamalısınız. Azure portal temel yapılandırmayı kullanarak, yalnızca toplanacak olayları filtrelemek için sınırlı bir becerisine sahip olursunuz. Uygulama ve sistem günlükleri için bu, belirli bir önem derecesine sahip tüm günlüklerle kaydedilir. Güvenlik günlükleri için tüm denetim başarısı veya tüm denetim hatası günlükleri budur.

Ek filtre belirtmek için özel yapılandırma kullanmanız ve istemediğiniz olayları filtreleyen bir XPath belirtmeniz gerekir. XPath girdileri biçiminde yazılır `LogName!XPathQuery` . Örneğin, olay KIMLIĞI 1035 olan uygulama olay günlüğünden yalnızca olayları geri döndürmek isteyebilirsiniz. Bu olaylar için XPathQuery olacaktır `*[System[EventID=1035]]` . Olayları uygulama olay günlüğünden almak istediğinizden dolayı XPath `Application!*[System[EventID=1035]]`

Windows olay günlüğü tarafından desteklenen XPath 'teki sınırlamaların listesi için bkz. [xpath 1,0 sınırlamaları](/windows/win32/wes/consuming-events#xpath-10-limitations) .

> [!TIP]
> `Get-WinEvent` `FilterXPath` Bir xpathquery 'nin geçerliliğini sınamak Için parametresiyle PowerShell cmdlet 'ini kullanın. Aşağıdaki betik bir örnek gösterir.
> 
> ```powershell
> $XPath = '*[System[EventID=1035]]'
> Get-WinEvent -LogName 'Application' -FilterXPath $XPath
> ```
>
> - Olaylar döndürülürse, sorgu geçerli olur.
> - *Belirtilen seçim ölçütleriyle eşleşen hiçbir olay bulunamadıysanız* iletiyi alırsanız. sorgu geçerli olabilir, ancak yerel makinede eşleşen olay yok.
> - *Belirtilen sorgu geçersiz olduğunda* iletiyi alırsanız sorgu söz dizimi geçersizdir. 

Aşağıdaki tabloda özel bir XPath kullanılarak olayların filtrelenmesi için örnekler gösterilmektedir.

| Açıklama |  XPath |
|:---|:---|
| Yalnızca olay KIMLIĞI = 4648 olan sistem olaylarını topla |  `System!*[System[EventID=4648]]`
| Yalnızca olay KIMLIĞI = 4648 olan sistem olaylarını ve consent.exe işlem adını toplayın | `Security!*[System[(EventID=4648)]] and *[EventData[Data[@Name='ProcessName']='C:\Windows\System32\consent.exe']]` |
| Olay KIMLIĞI = 6 dışında tüm kritik, hata, uyarı ve bilgi olaylarını sistem olay günlüğünden toplayın (sürücü yüklendi) |  `System!*[System[(Level=1 or Level=2 or Level=3) and (EventID != 6)]]` |
| Olay KIMLIĞI 4624 (başarılı oturum açma) dışında tüm başarı ve başarısızlık güvenlik olaylarını toplayın |  `Security!*[System[(band(Keywords,13510798882111488)) and (EventID != 4624)]]` |


## <a name="create-rule-and-association-using-rest-api"></a>REST API kullanarak kural ve ilişkilendirme oluşturma

REST API kullanarak bir veri toplama kuralı ve ilişkilendirmeleri oluşturmak için aşağıdaki adımları izleyin.

1. [Örnek DCR](data-collection-rule-overview.md#sample-data-collection-rule)' de gösterilen JSON BIÇIMINI kullanarak DCR dosyasını el ile oluşturun.

2. [REST API](/rest/api/monitor/datacollectionrules/create#examples)kullanarak kural oluşturun.

3. [REST API](/rest/api/monitor/datacollectionruleassociations/create#examples)kullanarak, her bir sanal makine için veri toplama kuralına bir ilişki oluşturun.


## <a name="create-association-using-resource-manager-template"></a>Kaynak Yöneticisi şablonu kullanarak ilişkilendirme oluşturma

Bir Azure sanal makinesi veya Azure Arc etkin sunucusu arasında Kaynak Yöneticisi şablonu kullanarak bir ilişki oluşturabilirsiniz. Örnek şablonlar için bkz. [Azure izleyici 'de veri toplama kuralları için Kaynak Yöneticisi şablon örnekleri](./resource-manager-data-collection-rules.md) .



## <a name="next-steps"></a>Sonraki adımlar

- [Azure Izleyici Aracısı](azure-monitor-agent-overview.md)hakkında daha fazla bilgi edinin.
- [Veri toplama kuralları](data-collection-rule-overview.md)hakkında daha fazla bilgi edinin.
