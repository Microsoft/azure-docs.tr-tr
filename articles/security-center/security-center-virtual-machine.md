---
title: Azure Güvenlik Merkezi ve Azure Sanal Makineleri | Microsoft Belgeleri
description: Bu belge Azure Güvenlik Merkezi’nin Azure Sanal Makinelerinizi nasıl koruduğunu anlamanıza yardımcı olur.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5fe5a12c-5d25-430c-9d47-df9438b1d7c5
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/24/2017
ms.author: yurid
ms.openlocfilehash: 481dbff90ee5368e1581502e612cb5d1993ac89e
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200735"
---
# <a name="azure-security-center-and-azure-virtual-machines"></a>Azure Güvenlik Merkezi ve Azure Sanal Makineler
[Azure Güvenlik Merkezi](https://azure.microsoft.com/services/security-center/), tehditleri önlemenize, algılamanıza ve yanıtlamanıza yardımcı olur. Aboneliklerinizde, tümleşik güvenlik izleme ve ilke yönetimi sağlar; normal koşullarda gözden kaçabilecek tehditleri algılamaya yardımcı olur ve güvenlik çözümlerinin geniş ekosistemiyle çalışır.

Bu makalede Güvenlik Merkezi’nin Azure Sanal Makinelerinizi (VM) güvenli hale getirmeye nasıl yardımcı olduğu gösterilmektedir.

## <a name="why-use-security-center"></a>Güvenlik Merkezi neden kullanılır?
Güvenlik Merkezi, sanal makinenizin güvenlik ayarlarını görüntüleme olanağı sağlayarak Azure’da sanal makine verilerini korumanıza yardımcı olur. Güvenlik Merkezi VM’lerinizi koruduğunda aşağıdaki özellikler kullanılabilir:

* Önerilen yapılandırma kuralları ile birlikte İşletim Sistemi (OS) güvenlik ayarları
* Sistem güvenliği ve eksik olan kritik güncelleştirmeler
* Uç nokta koruması önerileri
* Disk şifreleme doğrulaması
* Güvenlik açığı değerlendirme ve düzeltme
* Tehdit algılama

Güvenlik Merkezi, Azure VM’lerinizi korumaya yardımcı olmasına ek olarak Cloud Services, Uygulama Hizmetleri, Sanal Ağlar ve daha fazlasına yönelik güvenlik izleme ve yönetimi sağlar. 

> [!NOTE]
> Azure Güvenlik Merkezi hakkında daha fazla bilgi edinmek için bkz. [Azure Güvenlik Merkezi’ne Giriş](security-center-intro.md).
> 
> 

## <a name="prerequisites"></a>Önkoşullar
Azure Güvenlik Merkezi’ni kullanmaya başlamak için aşağıdakileri bilmeniz ve göz önünde bulundurmanız gerekir:

* Bir Microsoft Azure aboneliğiniz olmalıdır. Güvenlik Merkezi’nin ücretsiz ve standart katmanları hakkında daha fazla bilgi için bkz. [Güvenlik Merkezi Fiyatlandırması](https://azure.microsoft.com/pricing/details/security-center/).
* Güvenlik Merkezi için benimsediğiniz seçeneği planlayın ve planlama ile çalışma konuları hakkında daha fazla bilgi için [Azure Güvenlik Merkezi planlama ve işlemler kılavuzuna](security-center-planning-and-operations-guide.md) bakın.
* İşletim sistemi desteklenebilirliği ile ilgili daha fazla bilgi için bkz. [Azure Güvenlik Merkezi hakkında sık sorulan sorular (SSS)](security-center-faq.md). 

## <a name="set-security-policy"></a>Güvenlik ilkesi ayarlama
Azure Güvenlik Merkezi’nin yapılandırdığınız güvenlik ilkesini temel alarak oluşturulan öneriler ve uyarılar sağlamak üzere gereken bilgileri toplayabilmesi için veri toplama özelliği etkinleştirilmelidir. Aşağıdaki çizimde **Veri toplama** özelliği **Açık** olarak görülmektedir.

Güvenlik ilkesi, belirtilen abonelik veya kaynak grubundaki kaynaklar için önerilen denetimler kümesini tanımlar. Güvenlik ilkesini etkinleştirmeden önce veri toplamayı etkinleştirmeniz gerekir; Güvenlik Merkezi, verilerinizin güvenlik durumunu değerlendirmek, güvenlik önerileri sağlamak ve sizi tehditlere karşı uyarmak için sanal makinelerinizden veri toplar. Güvenlik Merkezi'nde şirketinizin güvenlik gereksinimlerine veya uygulamaların türüne ya da her abonelikteki verilerin duyarlılığına göre Azure abonelikleriniz veya kaynak grupları için ilkeler tanımlarsınız. 

![Güvenlik ilkesi](./media/security-center-virtual-machine/security-center-virtual-machine-fig1.png)

> [!NOTE]
> Kullanılabilen her bir **Önleme ilkesi** hakkında daha fazla bilgi için [Güvenlik ilkeleri ayarlama](tutorial-security-policy.md) makalesine bakın.

## <a name="manage-security-recommendations"></a>Güvenlik önerilerini yönetme
Güvenlik Merkezi, Azure kaynaklarınızın güvenlik durumunu analiz eder. Güvenlik Merkezi olası güvenlik açıklarını belirlediğinde öneriler oluşturur. Gerekli denetimlerin yapılandırılması işlemi boyunca öneriler size rehberlik eder.

Bir güvenlik ilkesi tanımladıktan sonra, Güvenlik Merkezi olası güvenlik açıklarını tanımlamak için kaynaklarınızın güvenlik durumunu analiz eder. Öneriler her satırın belirli bir öneriyi temsil ettiği bir tablo biçiminde gösterilir. Aşağıdaki tabloda Azure VM’lerinize yönelik önerilerin bazı örnekleri ve uygulamanız durumunda her birinin neler yapabileceği gösterilmektedir. Bir öneriyi seçtiğinizde, öneriyi Güvenlik Merkezi’nde nasıl uygulayacağınızı gösteren bilgiler sunulur.

| Öneri | Açıklama |
| --- | --- |
| Abonelikler için veri toplamayı etkinleştirin |Her bir aboneliğiniz ve aboneliklerinizdeki tüm sanal makinelerine (VM) için güvenlik ilkesinde veri toplamayı etkinleştirmenizi önerir. |
| İşletim sistemi güvenlik açıklarını düzeltin |İşletim sistemi yapılandırmalarınızı önerilen yapılandırma kurallarına uygun hale getirmenizi önerir; örneğin, parolaların kaydedilmesine izin verme. |
| Sistem güncelleştirmelerini uygulayın |VM’lerinize eksik sistem güvenliği güncelleştirmelerini ve kritik güncelleştirmeleri dağıtmanızı önerir. |
| Sistem güncelleştirmelerinden sonra yeniden başlatın |Sistem güncelleştirmelerini uygulama işlemini tamamlamak için VM’yi yeniden başlatmanızı önerir. |
| Endpoint Protection yükle |VM’lere (yalnızca Windows VM’leri) kötü amaçlı yazılımdan koruma programları sağlamanızı önerir. |
| VM Aracısını etkinleştirme |Hangi VM’lerin VM Aracısı gerektirdiğini görmenizi sağlar. Düzeltme eki tarama, temel tarama ve kötü amaçlı yazılımdan koruma programları sağlamak üzere VM’lere VM Aracısı yüklenmelidir. VM Aracısı, Azure Marketi’nden dağıtılan VM’ler için varsayılan olarak yüklüdür. [VM Aracısı ve Uzantılar – 2. Kısım](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) makalesinde VM Aracısının nasıl yüklendiğine ilişkin bilgiler verilmektedir. |
| Disk şifrelemesi uygulayın |Azure Disk Şifrelemesi kullanarak VM’nizi şifrelemenizi önerir (Windows ve Linux VM’leri). Şifreleme hem işletim sistemi hem de VM’nizin üzerindeki veri birimleri için önerilir. |
| Güvenlik açığı değerlendirmesi yüklü değil |Sanal makinenize bir güvenlik açığı değerlendirme çözümü yüklemenizi önerir. |
| Güvenlik açıklarını düzeltin |VM’nize yüklü güvenlik açığı değerlendirme çözümü tarafından algılanan sistem ve uygulama güvenlik açıklarını görmenizi sağlar. |

> [!NOTE]
> Öneriler hakkında daha fazla bilgi için [Güvenlik önerilerini yönetme](security-center-recommendations.md) makalesine bakın.
> 
> 

## <a name="monitor-security-health"></a>Güvenlik durumunu izleme
Bir aboneliğin kaynakları için [güvenlik ilkelerini](tutorial-security-policy.md) etkinleştirmenizin ardından, Güvenlik Merkezi olası güvenlik açıklarını tanımlamak amacıyla kaynaklarınızın güvenliğini analiz eder.  **Kaynak güvenlik durumu** dikey penceresinde herhangi bir sorunun yanı sıra kaynaklarınızın güvenlik durumunu da görüntüleyebilirsiniz. **Kaynak güvenliği** sistem durumu kutucuğundaki **Sanal makineler**’e tıkladığınızda **Sanal makineler** dikey penceresi VM’nize yönelik önerilerle birlikte açılır. 

![Güvenlik durumu](./media/security-center-virtual-machine/security-center-virtual-machine-fig2.png)

## <a name="manage-and-respond-to-security-alerts"></a>Güvenlik uyarılarını yönetme ve yanıtlama
Güvenlik Merkezi, gerçek tehditleri algılamak ve hatalı pozitif sonuçları azaltmak için Azure kaynaklarınızdan, ağınızdan ve güvenlik duvarı ve uç nokta koruma çözümleri gibi bağlı iş ortağı çözümlerinden günlük verilerini otomatik olarak toplar, çözümler ve tümleştirir. Güvenlik Merkezi, sorunu hızlıca araştırıp olası saldırıları düzeltmeye yönelik öneriler sağlamanıza yardımcı olmak amacıyla, çeşitli [algılama özelliklerinden](security-center-alerts-overview.md#detect-threats) yararlanarak öncelik sırasına koyulmuş güvenlik uyarıları oluşturabilir.

![Güvenlik uyarıları](./media/security-center-virtual-machine/security-center-virtual-machine-fig3.png)

Uyarıyı tetikleyen olay(lar) ve saldırıyı düzeltmek için (varsa) hangi adımları atmanız gerektiği hakkında daha fazla bilgi edinmek için bir güvenlik uyarısı seçin. Güvenlik uyarıları, türe ve tarihe göre gruplandırılır.

## <a name="see-also"></a>Ayrıca bkz.
Güvenlik Merkezi hakkında daha fazla bilgi edinmek için şunlara bakın:

* [Azure Güvenlik Merkezi'nde güvenlik ilkelerini ayarlama](tutorial-security-policy.md) -- Azure abonelikleriniz ve kaynak gruplarınız için güvenlik ilkelerini yapılandırma hakkında bilgi edinin.
* [Azure Güvenlik Merkezi'nde güvenlik uyarılarını yönetme ve yanıtlama](security-center-managing-and-responding-alerts.md) -- Güvenlik uyarılarını yönetme ve yanıtlama hakkında bilgi edinin.
* [Azure Güvenlik Merkezi ile ilgili SSS](security-center-faq.md) -- Hizmeti kullanımı ile ilgili sık sorulan soruları bulabilirsiniz.

