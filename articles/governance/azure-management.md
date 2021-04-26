---
title: Azure yönetimine genel bakış-Azure Idare
description: Azure Yönetim Araçları 'ndaki içeriklere yönelik bağlantılarla Azure uygulamaları ve kaynakları için yönetim bölümlerine genel bakış.
ms.date: 02/05/2021
ms.topic: overview
ms.openlocfilehash: 754fe496b790a7ee32eb840109a60a04ee66ef97
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99593934"
---
# <a name="what-are-the-azure-management-areas"></a>Azure Yönetim bölgeleri nelerdir?

Azure 'da idare, Azure yönetiminin bir yönüdür. Bu makalede, Azure 'da kaynaklarınızı dağıtmaya ve korumaya yönelik farklı yönetim bölgeleri ele alınmaktadır.

Yönetim, iş uygulamalarınızı ve onları destekleyen kaynaklarınızı korumak için gereken görevleri ve süreçleri ifade eder. Azure 'da, tüm yönetimi sağlamak için birlikte çalışan birçok hizmet ve araç vardır. Bu hizmetler yalnızca Azure 'daki kaynaklar ve ayrıca diğer bulutlarda ve şirket içinde değildir. Farklı araçları ve bunların birlikte nasıl çalıştığını anlamak, tüm yönetim ortamlarını tasarlamanın ilk adımıdır.

Aşağıdaki diyagramda herhangi bir uygulamayı veya kaynağı korumak için gereken farklı yönetim alanları gösterilmektedir. Bu farklı bölgeler yaşam döngüsü olarak düşünülebilir. Her alan, bir kaynağın kullanım ömrü boyunca sürekli olarak her bir şekilde gereklidir. Bu kaynak yaşam döngüsü ilk dağıtım ile başlar, devam eden işlem ve son kullanımdan kalkmıştır.

:::image type="complex" source="../monitoring/media/management-overview/management-capabilities.png" alt-text="Azure 'da disiplindeki yönetim hatlarının diyagramı." border="false":::
   Azure 'da yönetim ve Idare desteği sunan hizmetlerin tekerleğinin içeriğini geçirme, güvenli, koruma, Izleme, yapılandırma ve yönetme öğelerini gösteren diyagram. Güvende, alt öğeler olarak güvenlik yönetimi ve tehdit koruması vardır. Koruma, alt öğeler olarak yedekleme ve olağanüstü durum kurtarma içerir. Monitörde uygulama, Infra ve ağ izleme, alt öğe olarak Log Analytics ve Tanılamalar vardır. Yapılandırma, güncelleştirme yönetimi, otomasyon ve komut dosyasını alt öğe olarak yapılandırın. Ve, alt öğeler olarak Ilke yönetimi ve maliyet yönetimi içerir.
:::image-end:::

Tek bir Azure hizmeti belirli bir yönetim alanının gereksinimlerini tamamen doldurmıyor. Bunun yerine, her biri birlikte çalışan çeşitli hizmetler tarafından gerçekleştirilir. Application Insights gibi bazı hizmetler, Web uygulamaları için hedeflenen izleme işlevlerini sağlar. Azure Izleyici günlükleri gibi diğerleri, diğer hizmetler için yönetim verilerini depolar. Bu özellik farklı hizmetler tarafından toplanan farklı türlerdeki verileri analiz etmenizi sağlar.

Aşağıdaki bölümlerde farklı yönetim alanları kısaca açıklanır ve ilgili temel Azure hizmetlerindeki ayrıntılı içeriklere yönelik bağlantılar sunulur.

## <a name="monitor"></a>İzleyici

İzleme, kaynaklarınızın performansını, sistem durumunu ve kullanılabilirliğini denetlemek için veri toplama ve çözümleme işlemidir. Etkili bir izleme stratejisi, bileşenlerin çalışmasını anlamanıza ve kullanım süresini bildirimlerle artırmaya yardımcı olur. [Azure uygulamalarını ve kaynaklarını izlerken](../azure-monitor/overview.md)kullanılan farklı Hizmetleri kapsamakta olan izlemeye genel bakış konusunu okuyun.

## <a name="configure"></a>Yapılandırma

Yapılandırma, kaynakların ilk dağıtımını ve yapılandırmasını ve devam eden bakım sayısını ifade eder.
Bu görevlerin otomasyonu, artıklığı ortadan kaldırmanıza, zaman ve çabalarınızı en aza indirmenizi ve doğruluğu ve verimliliğin artırılmasını sağlar. [Azure Otomasyonu](../automation/automation-intro.md), yapılandırma görevlerini otomatikleştirmek için toplu hizmetler sunar. Runbook 'lar süreç otomasyonu 'nu işlerken yapılandırma ve güncelleştirme yönetimi de yapılandırmayı yönetir.

## <a name="govern"></a>İdare

İdare, Azure’daki uygulama ve kaynaklarınız üzerindeki denetimi sürdürmenize yönelik mekanizmalar ve süreçler sağlar. Girişimlerinizi planlama ve stratejik öncelikleri belirleme de bu kapsama dahildir.
Azure’da İdare, temelde iki hizmet ile uygulanır. [Azure ilkesi](./policy/overview.md) , kaynaklarınız için kuralları zorlamak üzere ilke tanımları oluşturmanıza, atamanıza ve yönetmenize olanak sağlar.
Bu özellik, bu kaynakları kurumsal standartlarınızla uyumlu tutar.
[Azure maliyet yönetimi](../cost-management-billing/cost-management-billing-overview.md) , Azure kaynaklarınız ve diğer bulut sağlayıcıları için bulut kullanımını ve harcamalarınızı izlemenize olanak sağlar.

## <a name="secure"></a>Güvenliği Sağlama

Kaynaklarınızın ve verilerinizin güvenliğini yönetin. Bir güvenlik programı tehditleri değerlendirmek, verileri toplamak ve analiz etmek ve uygulamalarınızın ve kaynaklarınızın uyumluluğunu içerir. Güvenlik izleme ve tehdit analizi, [Azure Güvenlik Merkezi](../security-center/security-center-introduction.md)tarafından sağlanır ve karma bulut iş yükleri arasında Birleşik güvenlik yönetimi ve Gelişmiş tehdit koruması içerir. Azure kaynaklarını güvenli hale getirme hakkında kapsamlı bilgi ve yönergeler için bkz. [Azure güvenliğine giriş](../security/fundamentals/overview.md) .

## <a name="protect"></a>Koruma

Koruma, sizin denetiminizin ötesinde olan kesintilerle birlikte uygulamalarınızın ve verilerinizin kullanılabilir tutulması anlamına gelir. Azure’da Koruma, iki hizmetle sağlanır. [Azure Backup](../backup/backup-overview.md), hem buluttaki hem de şirket içindeki verilerinize yönelik yedekleme ve kurtarma sağlar. [Azure Site Recovery](../site-recovery/site-recovery-overview.md) olağanüstü durum sırasında iş sürekliliği ve anında kurtarma sağlar.

## <a name="migrate"></a>Geçiş

Geçiş, şirket içinde çalışan mevcut iş yüklerini Azure bulut ortamına geçirmeyi ifade eder.
[Azure geçişi](../migrate/migrate-services-overview.md) , şirket içi sanal makinelerin Azure 'a geçiş uygunluğunu değerlendirmenize yardımcı olan bir hizmettir. Azure Site Recovery sanal makineleri [Şirket içinden](../site-recovery/migrate-tutorial-on-premises-azure.md) veya [Amazon Web Services](../site-recovery/migrate-tutorial-aws-azure.md)geçirir. [Azure veritabanı geçişi](../dms/dms-overview.md) , veritabanı kaynaklarını Azure veri platformları 'na geçirmeye yardımcı olur.

## <a name="next-steps"></a>Sonraki Adımlar

Azure Idare hakkında daha fazla bilgi edinmek için şu makalelere bakın:

- Bkz. [Azure idare hub 'ı](./index.yml).
- Bkz [. Azure Için bulut benimseme çerçevesinde idare](/azure/cloud-adoption-framework/govern/)