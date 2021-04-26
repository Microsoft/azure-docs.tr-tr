---
title: Azure geçişi hakkında SSS
description: Azure geçişi hizmeti hakkında sık sorulan soruların yanıtlarını alın.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: a5db000ad76f23be60d279923a590204c2fcf5e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100377389"
---
# <a name="azure-migrate-common-questions"></a>Azure geçişi: sık sorulan sorular

Bu makalede, Azure geçişi hakkında sık sorulan sorular yanıtlanmaktadır. Bu makaleyi okuduktan sonra sorularınız varsa, bunları [Azure geçişi forumuna](https://aka.ms/AzureMigrateForum)gönderebilirsiniz. Ayrıca, şu makaleleri inceleyebilirsiniz:

- [Azure geçişi](common-questions-appliance.md) gereci hakkında sorular
- [Bulma, değerlendirme ve bağımlılık görselleştirmesiyle](common-questions-discovery-assessment.md) ilgili sorular

## <a name="what-is-azure-migrate"></a>Azure Geçişi nedir?

Azure geçişi, şirket içi uygulamalarınızın ve iş yüklerinizin yanı sıra özel ve genel bulut VM 'lerinin bulmayı, değerlendirmesini ve geçişini izlemek için merkezi bir merkez sağlar. Hub, değerlendirme ve geçiş için Azure geçiş araçları ve üçüncü taraf ISV teklifleri sağlar. [Daha fazla bilgi edinin](migrate-services-overview.md).

## <a name="what-can-i-do-with-azure-migrate"></a>Azure geçişi ile ne yapabilirim?

Azure geçişi 'ni kullanarak şirket içi altyapıyı, uygulamaları ve verileri bulun, değerlendirin ve Azure 'a geçirin. Azure geçişi, şirket içi VMware VM 'Leri, Hyper-V VM 'Leri, fiziksel sunucular, diğer sanallaştırılmış VM 'Ler, veritabanları, Web uygulamaları ve sanal masaüstleri için değerlendirme ve geçişi destekler. 

## <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Azure geçişi ile Azure Site Recovery arasındaki fark nedir?

[Azure geçişi](migrate-services-overview.md) , değerlendirme ve Azure 'a geçiş için merkezi bir merkez sağlar. 

- Azure geçişi 'nin kullanılması, Azure geçiş araçları, diğer Azure hizmetleri ve üçüncü taraf araçlarla birlikte çalışabilirlik ve gelecekteki genişletilebilirlik sağlar.
- Azure geçişi: sunucu geçiş aracı, Azure 'a sunucu geçişi için tasarlanmıştır. Geçiş için en iyi duruma getirilmiştir. Doğrudan geçişle ilgili olmayan kavramlar ve senaryolar hakkında bilgi almanız gerekmez. 
- Bir VM için çoğaltma işlemi başlatıldığında 180 gün boyunca geçiş için bir araç kullanım ücreti yoktur. Geçiş işleminin tamamlanışında size zaman kazandırır. Yalnızca Çoğaltmada kullanılan depolama ve ağ kaynakları için ve test geçişleri sırasında tüketilen işlem ücretleri için ödeme yaparsınız.
- Azure geçişi, Site Recovery tarafından desteklenen tüm geçiş senaryolarını destekler. Ayrıca, VMware VM 'Leri için Azure geçişi aracısız bir geçiş seçeneği sağlar.
- Yalnızca Azure geçişi: sunucu geçiş aracı için yeni geçiş özelliklerini önceliklendiriyoruz. Bu özellikler Site Recovery için hedeflenmemektedir.

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) yalnızca olağanüstü durum kurtarma için kullanılmalıdır.

Azure geçişi: sunucu geçiş aracı bazı şirket içi makinelerin geçiş ve geçiş geçişi için bazı arka uç Site Recovery işlevleri kullanır.

## <a name="i-have-a-project-with-the-previous-classic-experience-of-azure-migrate-how-do-i-start-using-the-new-version"></a>Azure geçişi 'nin önceki klasik deneyimiyle bir projem var. Nasıl yaparım? yeni sürümü kullanmaya başlamak mı gerekiyor?

Klasik Azure geçişi, Şubat 2024 ' de devre dışı bırakılıyor. Şubat 2024 ' den sonra Azure geçişi 'nin klasik sürümü artık desteklenmeyecektir ve klasik projedeki envanter meta verileri silinir. Önceki sürümdeki proje veya bileşenleri yeni sürüme yükseltemezsiniz. [Yeni bir Azure geçişi projesi oluşturmanız](create-manage-projects.md)ve buna [değerlendirme ve geçiş araçları eklemeniz](./create-manage-projects.md) gerekir. Kullanılabilir değerlendirme ve geçiş araçlarının nasıl kullanılacağını anlamak için öğreticilerini kullanın. Klasik bir projeye eklenmiş bir Log Analytics çalışma alanınız varsa, klasik projeyi sildikten sonra onu geçerli sürümün projesine ekleyebilirsiniz.

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Azure geçişi: Sunucu değerlendirmesi ve harıta araç seti arasındaki fark nedir?

Sunucu değerlendirmesi, geçiş hazırlığı ve Azure 'a geçiş için iş yüklerinin değerlendirmesi hakkında değerlendirme sağlar. [Microsoft değerlendirme ve planlama (eşleme) araç seti](https://www.microsoft.com/download/details.aspx?id=7826) , Windows istemci ve sunucu işletim sistemlerinin daha yeni sürümleri için geçiş planlaması ve yazılım kullanımı izleme dahil diğer görevlerle yardımcı olur. Bu senaryolar için harıta araç setini kullanmaya devam edin.

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Sunucu değerlendirmesi ve Site Recovery Dağıtım Planlayıcısı arasındaki fark nedir?

Sunucu değerlendirmesi bir geçiş planlama aracıdır. Site Recovery Dağıtım Planlayıcısı bir olağanüstü durum kurtarma planlama aracıdır.

Ne yapmak istediğinize göre aracınızı seçin:

- **Azure 'da şirket içi geçiş planlama**: Şirket Içi sunucularınızı Azure 'a geçirmeyi planlıyorsanız, geçiş planlaması Için Sunucu değerlendirmesi kullanın. Sunucu değerlendirmesi değerlendirir şirket içi iş yükleri ve geçiş yapmanıza yardımcı olacak yönergeler ve araçlar sağlar. Geçiş planı gerçekleştirildikten sonra, makineleri Azure 'a geçirmek için Azure geçişi: sunucu geçişi gibi araçları kullanabilirsiniz.
- **Azure 'da olağanüstü durum kurtarmayı planlayın**: Şirket içinden Azure 'a olağanüstü durum kurtarma ayarlamayı Site Recovery için Site Recovery dağıtım planlayıcısı kullanın. Dağıtım Planlayıcısı, olağanüstü durum kurtarma amacıyla Şirket içi ortamınızın derinlemesine ve Site Recovery özgü bir değerlendirmesini sağlar. Çoğaltma ve yük devretme gibi olağanüstü durum kurtarma ile ilgili öneriler sağlar.

## <a name="how-does-server-migration-work-with-site-recovery"></a>Sunucu geçişi Site Recovery ile nasıl çalışır?

- Şirket içi VMware VM 'lerinin *aracısız* geçişini gerçekleştirmek Için Azure geçişi: sunucu geçişi ' ni kullanıyorsanız, geçiş Işlemi Azure geçişi 'ne yereldir ve Site Recovery kullanılmaz.
- VMware VM 'lerinin *aracı tabanlı* geçişini gerçekleştirmek Için Azure geçişi: sunucu geçişi kullanıyorsanız veya Hyper-V VM 'lerini veya fiziksel sunucuları geçirirseniz, Azure geçişi: sunucu geçişi Azure Site Recovery çoğaltma altyapısını kullanır.

## <a name="which-geographies-are-supported"></a>Hangi geographıes destekleniyor?

[Genel](migrate-support-matrix.md#supported-geographies-public-cloud) ve [kamu bulutları](migrate-support-matrix.md#supported-geographies-azure-government) için desteklenen coğrafyaları inceleyin.

## <a name="how-do-i-get-started"></a>Nasıl kullanmaya başlayabilirim?

İhtiyacınız olan aracı belirleyip, ardından aracı bir Azure geçişi projesine ekleyin. 

Bir ISV aracı veya Movere eklemek için:

1. Bir lisans edinerek veya ücretsiz deneme için, araç ilkesine uygun olarak kaydolun. Araçlar için lisanslama, ISV veya araç lisanslama modeline göre belirlenir.
2. Her araçta Azure geçişi 'ne bağlanma seçeneği vardır. Aracı Azure geçişi ile bağlamak için araç yönergelerini ve belgeleri izleyin.

Geçiş yolculuğunu Azure geçişi projesi içinden, Azure 'da ve diğer araçlarda izleyebilirsiniz.

## <a name="how-do-i-delete-a-project"></a>Nasıl yaparım? bir proje silinsin mi?

[Projeyi silmeyi](how-to-delete-project.md)öğrenin. 

## <a name="next-steps"></a>Sonraki adımlar

[Azure geçişi 'ne genel bakış](migrate-services-overview.md)konusunu okuyun.