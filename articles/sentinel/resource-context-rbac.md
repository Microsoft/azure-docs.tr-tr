---
title: Kaynağa göre Azure Sentinel verilerine erişimi yönetme | Microsoft Docs
description: Bu makalede, Azure Sentinel verilerine erişimi bir kullanıcının erişebileceği kaynaklara göre yönetebileceğiniz açıklanır. Erişimi kaynağa göre yönetmek, tüm Azure Sentinel deneyimi olmadan yalnızca belirli verilere erişim sağlamanıza olanak sağlar. Bu yöntem, kaynak bağlamı RBAC olarak da bilinir.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: bagol
ms.openlocfilehash: fc1246d079760fd86513840aebbffa34d192f8ed
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105044184"
---
# <a name="manage-access-to-azure-sentinel-data-by-resource"></a>Kaynağa göre Azure Sentinel verilerine erişimi yönetme

Genellikle, bir Azure Sentinel çalışma alanına erişimi olan kullanıcılar ayrıca güvenlik içeriği de dahil olmak üzere tüm çalışma alanı verilerine erişebilir. Yöneticiler, ekiblerindeki erişim gereksinimlerine bağlı olarak Azure Sentinel 'deki belirli özelliklere erişimi yapılandırmak için [Azure rollerini](roles.md) kullanabilir.

Ancak, yalnızca Azure Sentinel çalışma alanınızdaki belirli verilere erişmesi gereken ancak tüm Azure Sentinel ortamına erişiminizin olmaması gereken bazı kullanıcılarınız olabilir. Örneğin, sahip oldukları sunucuların Windows olay verilerine erişimi olan güvenlik dışı işlemler (SOC olmayan) ekibi sağlamak isteyebilirsiniz.

Böyle durumlarda, rol tabanlı erişim denetimi 'ni (RBAC), kullanıcılarınıza izin verilen kaynaklara göre, Azure Sentinel çalışma alanına veya belirli Azure Sentinel özelliklerine erişim sağlamak yerine yapılandırmanızı öneririz. Bu yöntem, **kaynak BAĞLAMı RBAC**'yi ayarlama olarak da bilinir.

Kullanıcıların Azure Sentinel çalışma alanı yerine erişebileceği kaynaklar aracılığıyla Azure Sentinel verilerine erişimi olduğunda, aşağıdaki yöntemleri kullanarak günlükleri ve çalışma kitaplarını görüntüleyebilirler:

- **Kaynak aracılığıyla** bir Azure sanal makinesi gibi. Yalnızca belirli bir kaynağa ait günlükleri ve çalışma kitaplarını görüntülemek için bu yöntemi kullanın.

- **Azure Izleyici aracılığıyla**. Birden fazla kaynağa ve/veya kaynak grubuna yayılan sorgular oluşturmak istediğinizde bu yöntemi kullanın. Azure Izleyici 'de günlüklere ve çalışma kitaplarına gidildiğinde, kapsamınızı bir veya daha fazla belirli kaynak grubu veya kaynak olarak tanımlayın.

Azure Izleyici 'de kaynak bağlamı RBAC 'yi etkinleştirin. Daha fazla bilgi için bkz. [Azure izleyici 'de günlük verilerine ve çalışma alanlarına erişimi yönetme](../azure-monitor/logs/manage-access.md).

> [!NOTE]
> Verileriniz syslog, CEF veya AAD verileri gibi bir Azure kaynağı veya özel bir toplayıcı tarafından toplanan veriler değilse, verileri tanımlamak ve erişimi etkinleştirmek için kullanılan kaynak KIMLIĞINI el ile yapılandırmanız gerekir.
>
> Daha fazla bilgi için bkz. [kaynak BAĞLAMı RBAC 'Yi açıkça yapılandırma](#explicitly-configure-resource-context-rbac).
>
## <a name="scenarios-for-resource-context-rbac"></a>Kaynak bağlamı RBAC senaryoları

Aşağıdaki tabloda, Resource-Context RBAC en çok yararlı olan senaryolar vurgulanmıştır. SOC takımları ve SOC olmayan takımlar arasındaki erişim gereksinimlerindeki farklılıkları göz önünde edin.

| Gereksinim türü |SOC ekibi  |SOC olmayan takım  |
|---------|---------|---------|
|**İzinler**     | Tüm çalışma alanı        |   Yalnızca belirli kaynaklar      |
|**Veri erişimi**     |  Çalışma alanındaki tüm veriler       | Yalnızca ekibin erişim yetkisine sahip olduğu kaynaklara ait veriler        |
|**Deneyim**     |  Büyük olasılıkla kullanıcıya atanan [işlevsel izinlerle](roles.md) sınırlı olan tam Azure Sentinel deneyimi       |  Yalnızca sorgu ve çalışma kitaplarını günlüğe kaydet       |
|     |         |         |

Takımınız, yukarıdaki tabloda açıklanan SOC olmayan ekibe benzer erişim gereksinimlerine sahipse, kaynak bağlamı RBAC kuruluşunuz için iyi bir çözüm olabilir.

## <a name="alternative-methods-for-implementing-resource-context-rbac"></a>Kaynak bağlamı RBAC uygulamak için alternatif Yöntemler

Kuruluşunuzda gereken izinlere bağlı olarak, kaynak bağlamı RBAC kullanımı tam çözüm sağlamayabilir.

Aşağıdaki listede, veri erişimi için diğer çözümlerin gereksinimlerinize daha iyi uyum bileceği senaryolar açıklanmaktadır:

|Senaryo  |Çözüm  |
|---------|---------|
|**Bir yan kuruluş, tam bir Azure Sentinel deneyimi gerektiren BIR SOC ekibine sahiptir**.     |  Bu durumda, veri izinlerinizi ayırmak için bir çoklu çalışma alanı mimarisi kullanın. <br><br>Daha fazla bilgi için bkz. <br>- [Çalışma alanları ve kiracılar arasında Azure Sentinel 'i genişletme](extend-sentinel-across-workspaces-tenants.md)<br>    - [Aynı anda birçok çalışma alanındaki olaylarla çalışma](multiple-workspace-view.md)          |
|**Belirli bir olay türüne erişim sağlamak istiyorsunuz**.     |  Örneğin, tüm sistemlerde Windows güvenlik olaylarına erişim sağlayan bir Windows Yöneticisi sağlayın. <br><br>Bu gibi durumlarda, her tablo için izinleri tanımlamak üzere [tablo DÜZEYI RBAC](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043) kullanın.       |
| **Erişimi kaynağa bağlı değil daha ayrıntılı bir düzeye veya bir olaydaki alanların yalnızca bir alt kümesine sınırlayın**   |   Örneğin, bir kullanıcının yan kuruluşuna bağlı olarak Office 365 günlüklerine erişimi sınırlandırmak isteyebilirsiniz. <br><br>Bu durumda, [Power BI panoları ve raporları](../azure-monitor/visualize/powerbi.md)ile yerleşik tümleştirmeyi kullanarak verilere erişim sağlayın.      |
| | |

## <a name="explicitly-configure-resource-context-rbac"></a>Kaynak bağlamı RBAC 'yi açıkça yapılandırın

Kaynak bağlamı RBAC 'yi yapılandırmak istiyorsanız aşağıdaki adımları kullanın, ancak verileriniz bir Azure kaynağı değildir.

Örneğin, Azure 'da olmayan Azure Kaynak çalışma alanınızdaki veriler syslog, CEF veya AAD verileri veya özel bir toplayıcı tarafından toplanan verileri içerir.

**Kaynak BAĞLAMı RBAC 'yi açıkça yapılandırmak için**:

1. Azure Izleyici 'de [Resource-Context RBAC](../azure-monitor/logs/manage-access.md) 'i etkinleştirdiğinizden emin olun. 

1. Tüm Azure Sentinel ortamı olmadan kaynaklarınıza erişmesi gereken her Kullanıcı ekibi için [bir kaynak grubu oluşturun](../azure-resource-manager/management/manage-resource-groups-portal.md) .

    Takım üyelerinin her biri için [günlük okuyucu izinleri](../azure-monitor/logs/manage-access.md#resource-permissions) atayın.

1. Oluşturduğunuz kaynak ekip gruplarına kaynak atayın ve olayları ilgili kaynak kimlikleriyle etiketleyin.

    Azure kaynakları Azure Sentinel 'e veri göndermişse, günlük kayıtları otomatik olarak veri kaynağının kaynak KIMLIĞIYLE etiketlenir.

    > [!TIP]
    > Erişim verdiğiniz kaynakları, amaç için oluşturulan belirli bir kaynak grubunun altına göre gruplanmasını öneririz.
    >
    > Yapamıyorum, takımınızın yalnızca erişmesini istediğiniz kaynaklara ait günlük okuyucusu izinlerine sahip olduğundan emin olun.
    >

    Kaynak kimlikleri hakkında daha fazla bilgi için bkz.

    - [Günlük iletme ile kaynak kimlikleri](#resource-ids-with-log-forwarding)
    - [Logstash koleksiyonuyla kaynak kimlikleri](#resource-ids-with-logstash-collection)
    - [Log Analytics API koleksiyonuyla kaynak kimlikleri](#resource-ids-with-the-log-analytics-api-collection)

### <a name="resource-ids-with-log-forwarding"></a>Günlük iletme ile kaynak kimlikleri

Olaylar [ortak olay biçimi (CEF)](connect-common-event-format.md) veya [Syslog](connect-syslog.md)kullanılarak toplandığında, günlük iletme birden çok kaynak sisteminden olayları toplamak için kullanılır.

Örneğin, bir CEF veya Syslog iletme sanal makinesi Syslog olayları Gönderen kaynakları dinler ve bunları Azure Sentinel 'e ilettiğinde, günlük iletme VM kaynak KIMLIĞI, ilettikleri tüm olaylara atanır.

Birden çok ekibiniz varsa, her ayrı takım için olayları işlerken ayrı günlük iletme VM 'lerinin bulunduğundan emin olun.

Örneğin, VM 'lerinizi ayırmak A ekibine ait Syslog olaylarının A toplayıcı VM 'Si kullanılarak toplanmasını sağlar.

> [!TIP]
> - Günlük ileticisi olarak AWS gibi bir şirket içi VM veya başka bir bulut VM kullanırken, [Azure Arc](../azure-arc/servers/overview.md)'ı uygulayarak BIR kaynak kimliğine sahip olduğundan emin olun.
> - Günlük iletme VM ortamınızı ölçeklendirmek için, CEF ve sylog günlüklerinizi toplamak üzere bir [VM Ölçek kümesi](https://techcommunity.microsoft.com/t5/azure-sentinel/scaling-up-syslog-cef-collection/ba-p/1185854) oluşturmayı düşünün.


### <a name="resource-ids-with-logstash-collection"></a>Logstash koleksiyonuyla kaynak kimlikleri

Verilerinizi Azure Sentinel [Logstash çıkış eklentisini](connect-logstash.md)kullanarak topluyorsanız, özel toplayıcılarınızı çıkışınızda kaynak kimliğini içerecek şekilde yapılandırmak için **azure_resource_id** alanını kullanın.

Kaynak bağlamı RBAC kullanıyorsanız ve API tarafından toplanan olayların belirli kullanıcılar tarafından kullanılabilir olmasını istiyorsanız, [kullanıcılarınız için oluşturduğunuz](#explicitly-configure-resource-context-rbac)kaynak grubunun kaynak kimliğini kullanın.

Örneğin, aşağıdaki kod bir örnek Logstash yapılandırma dosyası göstermektedir:

``` ruby
 input {
     beats {
         port => "5044"
     }
 }
 filter {
 }
 output {
     microsoft-logstash-output-azure-loganalytics {
       workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
       workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
       custom_log_table_name => "tableName"
       azure_resource_id => "/subscriptions/wvvu95a2-99u4-uanb-hlbg-2vatvgqtyk7b/resourceGroups/contosotest" # <your resource ID>   
     }
 }
```

> [!TIP]
> `output`Farklı olaylara uygulanan etiketleri ayırt etmek için birden çok bölüm eklemek isteyebilirsiniz.
>
### <a name="resource-ids-with-the-log-analytics-api-collection"></a>Log Analytics API koleksiyonuyla kaynak kimlikleri

[Log Analytics veri TOPLAYıCı API](../azure-monitor/logs/data-collector-api.md)'sini kullanarak TOPLARKEN, http [*x-MS-azureresourceıd*](../azure-monitor/logs/data-collector-api.md#request-headers) istek üst bilgisini kullanarak kaynak kimliği olan olaylara atayabilirsiniz.

Kaynak bağlamı RBAC kullanıyorsanız ve API tarafından toplanan olayların belirli kullanıcılar tarafından kullanılabilir olmasını istiyorsanız, [kullanıcılarınız için oluşturduğunuz](#explicitly-configure-resource-context-rbac)kaynak grubunun kaynak kimliğini kullanın.


## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için bkz. [Azure Sentinel izinleri](roles.md).