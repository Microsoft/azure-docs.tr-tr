---
title: Azure üretim ağı yönetimi-Microsoft Azure
description: Bu makalede, Microsoft 'un Azure veri merkezlerinin güvenliğini sağlamak için Azure üretim ağını nasıl yönetip çalıştığı açıklanır.
services: security
documentationcenter: n
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2019
ms.author: terrylan
ms.openlocfilehash: d41fe409b4a44a4c2af3670d76dd3a83a300feae
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727125"
---
# <a name="management-and-operation-of-the-azure-production-network"></a>Azure üretim ağının yönetimi ve işlemi    
Bu makalede, Microsoft 'un Azure veri merkezlerinin güvenliğini sağlamak için Azure üretim ağını nasıl yönetip çalıştığı açıklanır.

## <a name="monitor-log-and-report"></a>İzleme, günlüğe kaydetme ve rapor

Azure üretim ağının yönetimi ve işlemi, Azure ve Azure SQL veritabanı 'nın operasyon takımları arasındaki eşgüdümlü bir çabadır. Takımlar, ortamda çeşitli sistem ve uygulama performansı izleme araçları kullanır. Ve ağ cihazlarını, sunucuları, hizmetleri ve uygulama süreçlerini izlemek için uygun araçları kullanırlar.

Azure ortamında çalışan hizmetlerin güvenli yürütülmesini sağlamak için, işlemler ekipleri aşağıdaki eylemler dahil olmak üzere birden çok izleme, günlüğe kaydetme ve raporlama düzeyi uygular:

- Öncelikle Microsoft Monitoring Agent (MMA), yapı denetleyicisi (FC) ve kök işletim sistemi (OS) dahil olmak üzere birçok yerden izleme ve tanılama günlüğü bilgilerini toplar ve günlük dosyalarına yazar. Aracı sonunda bilgilerin daha önceden yapılandırılmış bir Azure depolama hesabına daha önce bir bit alt kümesini gönderir. Ayrıca, serbest duran izleme ve tanılama hizmeti çeşitli izleme ve tanılama günlüğü verilerini okur ve bilgileri özetler. İzleme ve tanılama hizmeti, bilgileri tümleşik bir günlüğe yazar. Azure, Azure izleme sisteminin bir uzantısı olan özel olarak oluşturulmuş Azure Güvenlik izlemesini kullanır. Bu, platformun çeşitli noktalarından güvenlik için ilgili olayları gözlemleyecek, çözümleyen ve rapor veren bileşenlere sahiptir.

- Azure SQL veritabanı Windows Fabric platformu, Azure SQL veritabanı için yönetim, dağıtım, geliştirme ve operasyonel fazla bakış hizmetleri sağlar. Platform dağıtılmış, çok adımlı Dağıtım Hizmetleri, sistem durumu izleme, otomatik onarımlar ve hizmet sürümü uyumluluğu sunar. Aşağıdaki hizmetleri sağlar:

   - Yüksek uygunlukta geliştirme ortamıyla hizmet modelleme özellikleri (veri merkezi kümeleri pahalıdır ve scarce).
   - Hizmet önyüklemesi ve bakım için tek tıklamayla dağıtım ve yükseltme iş akışları.
   - Kendi kendini onaran otomatik onarma iş akışlarıyla sistem durumu raporlaması.
   - Dağıtılmış bir sistemin düğümleri genelinde gerçek zamanlı izleme, uyarı verme ve hata ayıklama olanakları.
   - Dağıtılmış kök nedeni Analizi ve hizmet öngörüleri için işlemsel verilerin ve ölçümlerin merkezi olarak toplanması.
   - Dağıtım, değişiklik yönetimi ve izleme için işlemsel araç.
   - Azure SQL veritabanı Windows Fabric platform ve İzleme betikleri sürekli olarak çalışır ve gerçek zamanlı olarak izlenir.

Herhangi bir sorun oluşursa, Azure olay önceliklendirme ekibi tarafından izlenen olay yanıtı süreci etkinleştirilir. Uygun Azure destek personeli, olaya yanıt verecek şekilde bildirilir. Sorun izleme ve çözümleme, merkezi bir bilet oluşturma sisteminde belgelenir ve yönetilir. Sistem çalışma süresi ölçümleri, açıklanamayan sözleşme (NDA) ve istek üzerine kullanılabilir.

## <a name="corporate-network-and-multi-factor-access-to-production"></a>Üretime yönelik kurumsal ağ ve çok faktörlü erişim
Şirket ağı kullanıcı tabanı, Azure destek personelini içerir. Şirket ağı dahili kurumsal işlevleri destekler ve Azure müşteri desteği için kullanılan iç uygulamalara erişim içerir. Şirket ağı hem mantıksal hem de fiziksel olarak Azure üretim ağından ayrılır. Azure personeli, Azure iş istasyonlarını ve dizüstü bilgisayarları kullanarak şirket ağına erişir. Şirket ağ kaynaklarına erişmek için tüm kullanıcıların Kullanıcı adı ve parola dahil Azure Active Directory (Azure AD) hesabına sahip olması gerekir. Şirket ağı erişimi, tüm Microsoft personeli, yüklenicileri ve satıcılarına verilen ve Microsoft Bilgi teknolojisi tarafından yönetilen Azure AD hesaplarını kullanır. Benzersiz kullanıcı tanımlayıcıları, personeli Microsoft 'un iş durumlarına göre birbirinden ayırt edebilir.

İç Azure uygulamalarına erişim Active Directory Federasyon Hizmetleri (AD FS) (AD FS) ile kimlik doğrulaması aracılığıyla denetlenir. AD FS, güvenli bir belirteç ve kullanıcı talepleri uygulayarak kurumsal ağ kullanıcılarının kimlik doğrulamasını sağlayan Microsoft Information Technology tarafından barındırılan bir hizmettir. AD FS, iç Azure uygulamalarının Microsoft kurumsal Active Directory etki alanında kullanıcılara kimlik doğrulaması yapmasına olanak sağlar. Şirket ağ ortamından üretim ağına erişmek için kullanıcıların Multi-Factor Authentication kullanarak kimlik doğrulaması yapması gerekir.

## <a name="next-steps"></a>Sonraki adımlar
Microsoft 'un Azure altyapısını güvenli hale getirmeye yönelik daha fazla bilgi edinmek için bkz.:

- [Azure tesisler, şirket içi ve fiziksel güvenlik](physical-security.md)
- [Azure altyapı kullanılabilirliği](infrastructure-availability.md)
- [Azure Information System bileşenleri ve sınırları](infrastructure-components.md)
- [Azure ağ mimarisi](infrastructure-network.md)
- [Azure üretim ağı](production-network.md)
- [Azure SQL veritabanı güvenlik özellikleri](infrastructure-sql.md)
- [Azure altyapı izleme](infrastructure-monitoring.md)
- [Azure altyapı bütünlüğü](infrastructure-integrity.md)
- [Azure müşteri verileri koruması](protection-customer-data.md)
