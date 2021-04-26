---
title: Azure Data Factory için Azure Özel Bağlantı
description: Azure özel bağlantısının Azure Data Factory nasıl çalıştığı hakkında bilgi edinin.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/01/2020
ms.openlocfilehash: d572509b3b2b3a0fc69f207d005984ee07ce34a4
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076828"
---
# <a name="azure-private-link-for-azure-data-factory"></a>Azure Data Factory için Azure Özel Bağlantı

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Azure özel bağlantısı ' nı kullanarak, özel bir uç nokta aracılığıyla Azure 'daki çeşitli platformlar hizmet (PaaS) dağıtımlarını bağlayabilirsiniz. Özel uç nokta, belirli bir sanal ağ veya alt ağ içinde yer alan özel IP adresidir. Özel bağlantı işlevselliğini destekleyen PaaS dağıtımlarının bir listesi için bkz. [özel bağlantı belgeleri](../private-link/index.yml). 

## <a name="secure-communication-between-customer-networks-and-azure-data-factory"></a>Müşteri ağları ve Azure Data Factory arasında güvenli iletişim 
Buluttaki ağınızı mantıksal gösterimi olarak bir Azure sanal ağı ayarlayabilirsiniz. Bunun yapılması aşağıdaki avantajları sağlar:
* Azure kaynaklarınızın genel ağlardaki saldırılardan korunmasına yardımcı olursunuz.
* Ağlarla Data Factory'nin birbiriyle güvenle iletişim kurmasını sağlarsınız. 

Bir İnternet Protokolü güvenliği (IPsec) VPN (siteler arası) bağlantısı veya Azure ExpressRoute (özel eşleme) bağlantısı ayarlayarak sanal ağınıza şirket içi ağını da bağlayabilirsiniz. 

Şirket içi bir makineye veya sanal ağdaki bir sanal makineye şirket içinde barındırılan bir tümleştirme çalışma zamanı da yükleyebilirsiniz. Bunun yapılması şunları sağlar:
* Bulut veri deposuyla özel ağdaki bir veri deposu arasında kopyalama etkinlikleri çalıştırabilirsiniz.
* Şirket içi ağındaki veya Azure sanal ağındaki işlem kaynaklarına dönüştürme etkinlikleri gönderebilirsiniz. 

Aşağıdaki tabloda gösterildiği gibi Azure Data Factory ve müşteri sanal ağı arasında birkaç iletişim kanalı gereklidir:

| Etki alanı | Bağlantı noktası | Description |
| ---------- | -------- | --------------- |
| `adf.azure.com` | 443 | Data Factory yazma ve izleme için gereken bir denetim düzlemi. |
| `*.{region}.datafactory.azure.net` | 443 | Şirket içinde barındırılan tümleştirme çalışma zamanı tarafından Data Factory hizmetine bağlanmak için gereklidir. |
| `*.servicebus.windows.net` | 443 | Etkileşimli yazma için şirket içinde barındırılan tümleştirme çalışma zamanı için gereklidir. |
| `download.microsoft.com` | 443 | Güncelleştirmeleri indirmek için şirket içinde barındırılan tümleştirme çalışma zamanı için gereklidir. |

Azure Data Factory için özel bağlantı desteğiyle şunları yapabilirsiniz:
* Sanal ağınızda özel bir uç nokta oluşturun.
* Belirli bir Data Factory örneğine özel bağlantıyı etkinleştirin. 

Azure Data Factory hizmetine yönelik iletişimler özel bağlantı üzerinden gidip güvenli özel bağlantı sağlanmasına yardımcı olur. 

![Azure Data Factory mimarisine yönelik özel bağlantı diyagramı.](./media/data-factory-private-link/private-link-architecture.png)

Önceki iletişim kanallarının her biri için özel bağlantı hizmetinin etkinleştirilmesi aşağıdaki işlevleri sunar:
- **Desteklenen**:
   - Tüm giden iletişimleri engelleseniz bile, sanal ağınızda veri fabrikasını yazabilir ve izleyebilirsiniz.
   - Şirket içinde barındırılan tümleştirme çalışma zamanı ve Azure Data Factory hizmeti arasındaki komut iletişimleri, özel bir ağ ortamında güvenli bir şekilde gerçekleştirilebilir. Şirket içinde barındırılan tümleştirme çalışma zamanı ve Azure Data Factory hizmeti arasındaki trafik özel bağlantı aracılığıyla gider. 
- **Şu anda desteklenmiyor**:
   - Sağ barındırılan bir tümleştirme çalışma zamanı kullanan etkileşimli yazma, test bağlantısı, klasör listesi ve tablo listesi, şema al ve veri önizleme gibi özel bağlantı üzerinden ilerler.
   - Otomatik güncelleştirme 'yi etkinleştirirseniz otomatik olarak barındırılan tümleştirme çalışma zamanının yeni sürümü, Microsoft Indirme merkezi 'nden otomatik olarak indirilebilir, bu sırada desteklenmez.

   > [!NOTE]
   > Şu anda desteklenmeyen işlevler için, sanal ağda veya şirket güvenlik duvarında daha önce bahsedilen etki alanını ve bağlantı noktasını yapılandırmanız gerekir. 

   > [!NOTE]
   > Özel uç nokta aracılığıyla Azure Data Factory bağlantı yalnızca Data Factory 'de şirket içinde barındırılan tümleştirme çalışma zamanı için geçerlidir. SYNAPSE içinde desteklenmez.

> [!WARNING]
> Bağlı bir hizmet oluşturduğunuzda, kimlik bilgilerinizin bir Azure Anahtar Kasası 'nda depolandığından emin olun. Aksi takdirde, Azure Data Factory özel bağlantıyı etkinleştirdiğinizde kimlik bilgileri çalışmayacaktır.

## <a name="dns-changes-for-private-endpoints"></a>Özel uç noktalar için DNS değişiklikleri
Özel bir uç nokta oluşturduğunuzda, Data Factory için DNS CNAME kaynak kaydı, ' Privatelink ' önekine sahip bir alt etki alanındaki diğer ada güncelleştirilir. Varsayılan olarak, Özel uç noktalar için DNS A kaynak kayıtları ile ' Privatelink ' alt etki alanına karşılık gelen [Özel BIR DNS bölgesi](../dns/private-dns-overview.md)de oluşturacağız.

Veri Fabrikası uç noktası URL 'sini VNet dışından özel uç noktayla çözümlediğinizde, Data Factory hizmetinin genel uç noktasına dönüşür. Özel uç noktayı barındıran VNet 'ten çözümlendiğinde, depolama uç noktası URL 'SI özel uç noktanın IP adresine çözümlenir.

Yukarıdaki gösterilen örnek için, Özel uç noktayı barındıran VNet dışından çözümlendiğinde ' DataFactoryA ' Data Factory DNS kaynak kayıtları şu şekilde olur:

| Ad | Tür | Değer |
| ---------- | -------- | --------------- |
| DataFactoryA. {Region}. DataFactory. Azure. net | CNAME   | DataFactoryA. {Region}. Privatelink. DataFactory. Azure. net |
| DataFactoryA. {Region}. Privatelink. DataFactory. Azure. net | CNAME   | < Data Factory hizmeti genel uç noktası > |
| < Data Factory hizmeti genel uç noktası >  | A | < Data Factory hizmeti genel IP adresi > |

Özel uç noktasını barındıran VNet 'te çözümlendiğinde, DataFactoryA için DNS kaynak kayıtları şu şekilde olur:

| Ad | Tür | Değer |
| ---------- | -------- | --------------- |
| DataFactoryA. {Region}. DataFactory. Azure. net | CNAME   | DataFactoryA. {Region}. Privatelink. DataFactory. Azure. net |
| DataFactoryA. {Region}. Privatelink. DataFactory. Azure. net   | A | Özel uç nokta IP adresi < > |

Ağınızda özel bir DNS sunucusu kullanıyorsanız, istemciler Data Factory uç noktası için FQDN 'yi özel uç nokta IP adresine çözümleyebilmelidir. DNS sunucunuzu özel bağlantı alt etki alanınızı, sanal ağın özel DNS bölgesine devretmek veya ' DataFactoryA ' için bir kayıt yapılandırmak üzere yapılandırmalısınız. Özel uç nokta IP adresine sahip {Region}. Privatelink. DataFactory. Azure. net '.

Kendi DNS sunucunuzu özel uç noktaları destekleyecek şekilde yapılandırma hakkında daha fazla bilgi için aşağıdaki makalelere bakın:
- [Azure sanal ağlarındaki kaynaklar için ad çözümlemesi](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [Özel uç noktalar için DNS yapılandırması](../private-link/private-endpoint-overview.md#dns-configuration)


## <a name="set-up-private-link-for-azure-data-factory"></a>Azure Data Factory için özel bağlantı ayarlama
[Azure Portal](../private-link/create-private-endpoint-portal.md)kullanarak özel uç noktalar oluşturabilirsiniz.

Şirket içinde barındırılan tümleştirme çalışma zamanından ortak uç nokta veya özel uç nokta aracılığıyla Azure Data Factory bağlanıp bağlanamayacağını seçebilirsiniz. 

![Şirket içinde barındırılan Integration Runtime ortak erişimini engelleme ekran görüntüsü.](./media/data-factory-private-link/disable-public-access-shir.png)


Ayrıca, Azure portal Azure Data Factory 'nize giderek aşağıda gösterildiği gibi özel bir uç nokta oluşturabilirsiniz:

![Özel uç nokta oluşturmak için "özel uç nokta bağlantıları" bölmesinin ekran görüntüsü.](./media/data-factory-private-link/create-private-endpoint.png)

**Kaynak** adımında, **kaynak türü** olarak **Microsoft. DataFactory/Factory** ' yi seçin. Şirket içinde barındırılan tümleştirme çalışma zamanı ve Azure Data Factory hizmeti arasındaki komut iletişimleri için özel uç nokta oluşturmak istiyorsanız, **hedef alt kaynak** olarak **DataFactory** ' yi seçin.

![Kaynak seçme için "özel uç nokta bağlantıları" bölmesinin ekran görüntüsü.](./media/data-factory-private-link/private-endpoint-resource.png)

> [!NOTE]
> Ortak ağ erişimini devre dışı bırakmak yalnızca şirket içinde barındırılan tümleştirme çalışma zamanı için geçerlidir, Azure Integration Runtime ve SQL Server Integration Services (SSIS) Integration Runtime.

Sanal ağınızda veri fabrikasını yazmak ve izlemek için özel uç nokta oluşturmak istiyorsanız, **hedef alt kaynak** olarak **Portal** ' ı seçin.

> [!NOTE]
> Portal için özel uç nokta oluşturduktan sonra, Azure Data Factory portalına ortak bir ağ üzerinden erişmeye devam edebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Data Factory UI kullanarak veri fabrikası oluşturma](quickstart-create-data-factory-portal.md)
- [Azure Data Factory'ye giriş](introduction.md)
- [Azure Data Factory 'de görsel yazma](author-visually.md)
