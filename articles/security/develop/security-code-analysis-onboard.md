---
title: Microsoft Güvenlik kodu analizi ekleme Kılavuzu
description: Microsoft Güvenlik kodu çözümleme uzantısı 'nı ekleme ve yüklemeyi öğrenin. Önkoşulları inceleyin ve ek kaynakları görüntüleyin.
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 03/22/2021
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: c827dc81c1ef1ab03dd6c9178a609b512e59ef15
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801223"
---
# <a name="onboarding-and-installing"></a>Ekleme ve yükleme

> [!Note]
> 1 Mart 2022 ' den itibaren geçerli olmak üzere Microsoft Güvenlik kodu Analizi (MSCA) uzantısı kullanımdan kaldırılacak. Var olan MSCA müşterileri, 1 Mart 2022 ' e kadar MSCA erişimini koruacaktır. Azure DevOps 'daki alternatif seçenekler için lütfen [OWASP kaynak kodu çözümleme araçlarına](https://owasp.org/www-community/Source_Code_Analysis_Tools) bakın. GitHub 'a geçirmeyi planlayan müşteriler için [GitHub gelişmiş güvenlik](https://docs.github.com/github/getting-started-with-github/about-github-advanced-security)' i kullanıma alabilirsiniz.

Microsoft Güvenlik kodu çözümlemesi ile çalışmaya başlama önkoşulları:

- Aşağıdaki bölümde açıklandığı gibi uygun bir Microsoft Birleştirilmiş Destek teklif.
- Azure DevOps organizasyonu.
- Azure DevOps kuruluşuna uzantı yüklemek için izin.
- Bulutta barındırılan bir Azure DevOps işlem hattı ile eşitlenebilir kaynak kodu.

## <a name="onboarding-the-microsoft-security-code-analysis-extension"></a>Microsoft Güvenlik kodu çözümleme uzantısını ekleme

### <a name="interested-in-purchasing-the-microsoft-security-code-analysis-extension"></a>Microsoft Güvenlik kodu analiz uzantısı 'nı satın alma ile ilgileniyor musunuz?

Aşağıdaki destek tekliflerinden birine sahipseniz, uzantıya erişim sağlamak için mevcut saatleri satın almak veya değiştirmek üzere teknik hesap yöneticinize başvurun:

- Birleşik destek Gelişmiş katmanı
- Birleşik destek performans katmanı
- Geliştiriciler için Premier Destek
- Iş ortakları için Premier Destek
- Enterprise için Premier Destek

Yukarıda belirtilen destek anlaşmalarından birine sahip değilseniz, bu uzantıyı Iş ortaklarımızın birinden satın alabilirsiniz.

**Sonraki Adımlar:**

Yukarıdaki nitelikleri karşıladıysanız, Microsoft Güvenlik kodu analizi uzantısını satın almak için aşağıdaki listeden bir iş ortağıyla iletişim kurun. Aksi takdirde, [Microsoft Güvenlik kodu çözümleme desteği](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request)'ne başvurun.

>**Ortaklarınıza**

- Bölgeler – Iletişim ayrıntıları: cloudsupport@zones.com
- Worsöyleyin – Iletişim ayrıntıları: info@wortell.nl
- Logicalsıs – Iletişim ayrıntıları: logicalisleads@us.logicalis.com

### <a name="become-a-partner"></a>İş Ortağı Olun

Microsoft Güvenlik kodu çözümleme ekibi, iş ortakları için bir Premier Destek iş ortağı sözleşmesi eklemek istiyor. İş ortakları, uzantıyı satın almak isteyen müşterilere satarak Azure DevOps müşterilerinin daha güvenli bir şekilde geliştirilebilme sağlanmasına yardımcı olur, ancak Microsoft ile kurumsal destek sözleşmenize gerek kalmaz. İlgilendiğiniz iş ortakları [buradan](http://www.microsoftpartnersupport.com/msrd/opin)kayıt yapabilir.

## <a name="installing-the-microsoft-security-code-analysis-extension"></a>Microsoft Güvenlik kodu analizi uzantısını yükleme

1. Uzantı Azure DevOps kuruluşunuzla paylaşıldıktan sonra Azure DevOps kuruluş sayfanıza gidin. Böyle bir sayfanın örnek URL 'SI `https://dev.azure.com/contoso` .
1. Adınızın yanındaki sağ üst köşede bulunan alışveriş çantası simgesini seçin ve ardından **Uzantıları Yönet**' i seçin.
1. **Paylaşılan**' ı seçin.
1. Microsoft Güvenlik kodu analiz uzantısı ' nı seçin, ardından **Install**' ı seçin.
1. Açılan listeden, uzantıyı yüklemek için Azure DevOps organizasyonunu seçin.
1. **Yükle**'yi seçin. Yükleme tamamlandıktan sonra, uzantısını kullanmaya başlayabilirsiniz.

>[!NOTE]
> Uzantıyı yükleme erişiminiz olmasa bile, yükleme adımlarıyla devam edin. Yükleme işlemi sırasında Azure DevOps kuruluş yöneticinizden erişim isteğinde bulabilirsiniz.

Uzantıyı yükledikten sonra, güvenli geliştirme derleme görevleri görünür ve Azure Pipelines eklemek için kullanılabilir.

## <a name="adding-specific-build-tasks-to-your-azure-devops-pipeline"></a>Azure DevOps işlem hattınızla belirli derleme görevleri ekleme

1. Azure DevOps kuruluşunuzda, takım projenizi açın.
1. İşlem **hatları**  >  **derlemeleri** seçin.
1. Uzantı derleme görevlerini eklemek istediğiniz işlem hattını seçin:
   - Yeni işlem hattı: **Yeni** ' yi seçin ve yeni bir işlem hattı oluşturmak için ayrıntılı adımları izleyin.
   - İşlem hattını Düzenle: mevcut bir işlem hattını seçin ve ardından işlem hattını düzenlemeye başlamak için **Düzenle** ' yi seçin.
1. ' İ seçin **+** ve **Görevler Ekle** bölmesine gidin.
1. Listeden ya da arama kutusunu kullanarak eklemek istediğiniz derleme görevini bulun. **Add (Ekle)** seçeneğini belirleyin.
1. Görev için gereken parametreleri belirtin.
1. Yeni bir derlemeyi kuyruğa al.
   >[!NOTE]
   >Dosya ve klasör yolları, kaynak deponuzdaki köke göre değişir. Çıkış dosyalarını ve klasörlerini parametre olarak belirtirseniz, bunlar derleme aracısında tanımlamış olduğumuz ortak konumla değiştirilmiştir.

> [!TIP]
>
> - Derlemeden sonra bir analiz çalıştırmak için, derlemenize ait derleme yapıtlarını Yayımla adımını tamamladıktan sonra Microsoft Güvenlik kodu çözümleme derleme görevlerini koyun. Bu şekilde, derlemeniz statik analiz araçlarını çalıştırmadan önce, sonuçları bitirebilirler ve gönderebilir.
> - Güvenli Geliştirme derleme görevleri için her zaman **hata durumunda devam et '** i seçin. Bir araç başarısız olsa da, diğerleri çalıştırılabilir. Araçlar arasında bağımlılık yoktur.
> - Microsoft Güvenlik kodu Analizi derleme görevleri yalnızca bir araç başarıyla çalışamazsa başarısız olur. Ancak bir araç, koddaki sorunları tanımlarsa bile başarılı olur. Analiz sonrası derleme görevini kullanarak, bir araç koddaki sorunları belirlediğinde, derlemenizi başarısız olarak yapılandırabilirsiniz.
> - Bir yayın işlem hattı aracılığıyla çalıştırıldığında bazı Azure DevOps derleme görevleri desteklenmez. Daha belirgin olarak, Azure DevOps bir yayın işlem hattı içinden yapıtları yayınlayan görevleri desteklemez.
> - Azure DevOps ekip derlemesinde parametre olarak belirtebileceğiniz önceden tanımlanmış değişkenlerin bir listesi için bkz. [Azure DevOps derleme değişkenleri](/azure/devops/pipelines/build/variables?tabs=batch).

## <a name="next-steps"></a>Sonraki adımlar

Yapı görevlerini yapılandırma hakkında daha fazla bilgi için bkz. [yapılandırma kılavuzumuzu](security-code-analysis-customize.md) veya [YAML yapılandırma kılavuzu](yaml-configuration.md).

Uzantı ve sunulan araçlar hakkında daha fazla sorunuz varsa, [SSS sayfamıza](security-code-analysis-faq.md)göz atın.