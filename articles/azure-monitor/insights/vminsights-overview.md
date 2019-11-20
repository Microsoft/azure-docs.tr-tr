---
title: Azure İzleyici VM'ler (Önizleme) nedir? | Microsoft Belgeleri
description: VM'ler için Azure İzleyici bir sistem durumunu ve uygulama bileşenleri ve diğer kaynaklarla ilgili bağımlılıkları otomatik olarak keşfetme yanı sıra Azure VM'nin işletim sistemi izleme performansını birleştirir ve iletişimi eşleyen bir Azure İzleyici özelliğidir. Bunlar arasında. Bu makalede, genel bir bakış sağlar.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 11/14/2019
ms.openlocfilehash: 84ebf92f795dfe1a21570d782bf24fad27f50eda
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74109075"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>Azure İzleyici VM'ler (Önizleme) nedir?

Azure sanal makinelerinizi (VM) sanal makineler için Azure İzleyici izler ve uygun ölçekte sanal makine ölçek kümeleri. İşlemlerini ve diğer kaynaklarla dış işlemlere olan bağımlılıklarını izleyerek Windows ve Linux VM'lerinizin performansını ve sistem durumunu analiz eder. 

Şirket içinde veya başka bir bulut sağlayıcısında barındırılan VM 'Ler için performans ve uygulama bağımlılıklarını izleme desteği içerir. Aşağıdaki temel özellikler ayrıntılı öngörüler sunar:

- **Önceden tanımlanmış popüler performans grafikleri**: Konuk VM işletim sisteminden çekirdek performans ölçümlerini görüntüleyin.

- **Bağımlılık Haritası**: çeşitli kaynak gruplarında ve Aboneliklerde VM'den birbirine bağlı bileşenlerle görüntüler.  

>[!NOTE]
>Kısa süre önce, genel önizleme müşterilerimizden aldığımız geri bildirimlere göre sistem durumu özelliğine yaptığımız [değişiklikleri duyuruyoruz](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) . Yaptığımız değişikliklerin sayısı verildiğinde, yeni müşterilerin sistem durumu özelliğini sunmayı durduracağız. Mevcut müşteriler sistem durumu özelliğini kullanmaya devam edebilir. Daha fazla ayrıntı için lütfen [genel kullanım hakkında SSS bölümüne](vminsights-ga-release-faq.md)bakın.  

Azure Izleyici günlükleri ile tümleştirme, güçlü toplama ve filtreleme sağlar ve zaman içinde veri eğilimlerini analiz edebilir. Bu kapsamlı iş yükü izleme, Azure Izleyici veya tek başına Hizmet Eşlemesi ile elde alınamaz.  

Bu verileri doğrudan sanal makineden tek bir VM 'de görüntüleyebilir veya Azure Izleyici 'yi, görünümün Azure Kaynak bağlamı veya çalışma alanı-bağlam modlarını desteklediği, VM 'nizin toplanmış bir görünümünü sunmak için kullanabilirsiniz. Daha fazla bilgi için bkz. [erişim modlarına genel bakış](../platform/design-logs-deployment.md#access-mode).

![Azure portalında sanal makine ınsights perspektifi](./media/vminsights-overview/vminsights-azmon-directvm.png)

VM'ler için Azure İzleyici, önemli uygulamaların tahmin edilebilir performansını ve kullanılabilirliğini sunabilir. Performans sorunlarını ve ağ sorunlarını belirler. VM'ler için Azure İzleyici, bir sorunun diğer bağımlılıklarla ilgili olup olmadığını anlamanıza da yardımcı olabilir.  

## <a name="data-usage"></a>Veri kullanımı

VM'ler için Azure İzleyici'yi dağıttığınızda, sanal makineleriniz tarafından toplanan verileri alınır ve Azure İzleyici'de depolanan. Toplanan performans ve bağımlılık verileri bir Log Analytics çalışma alanında depolanır. Yayımlanan fiyatlandırmaya göre [Azure fiyatlandırma sayfasını İzleyici](https://azure.microsoft.com/pricing/details/monitor/), VM'ler için Azure İzleyici için faturalandırılır:

- Alınan ve depolanan veriler.
- Oluşturulan uyarı kuralları.
- Gönderilen bildirimleri. 

Günlük boyutu performans sayaçlarının dize uzunluklarına göre farklılık gösterir ve VM 'ye ayrılan mantıksal disk ve ağ bağdaştırıcısı sayısıyla artabilir. Zaten bir çalışma alanı varsa ve bu sayaçları toplamak, yinelenen herhangi bir ücret uygulanır. Hizmet eşlemesi zaten kullanıyorsanız, gördüğünüz tek değişiklik, Azure İzleyici gönderilen ek bağlantı verilerdir.

## <a name="next-steps"></a>Sonraki adımlar

Sanal makinelerinizi izlemenize yardımcı yöntemler ve gereksinimleri hakkında bilgilere [VM'ler için Azure İzleyici'ı Dağıtma](vminsights-enable-overview.md).
