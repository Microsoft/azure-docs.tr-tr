---
title: Bileşik kaynakları kullanarak Azure Otomasyonu durum yapılandırması 'nda (DSC) DSC yapılandırmaları oluşturma
description: Azure Otomasyonu durum yapılandırması 'nda (DSC) bileşik kaynakları kullanarak yapılandırma oluşturma hakkında bilgi edinin
keywords: PowerShell DSC, istenen durum yapılandırması, PowerShell DSC Azure, bileşik kaynaklar
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 08/21/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d9d16d9d9967af5bf8f9e13406e13d5637325f52
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243361"
---
# <a name="composing-dsc-configurations-in-azure-automation-state-configuration-dsc-using-composite-resources"></a>Bileşik kaynakları kullanarak Azure Otomasyonu durum yapılandırması 'nda (DSC) DSC yapılandırmaları oluşturma

Bir kaynağın, tek bir istenen durum yapılandırması (DSC) yapılandırmasıyla yönetilmesi gerektiğinde en iyi yol [bileşik kaynakları](/powershell/scripting/dsc/resources/authoringresourcecomposite)kullanmaktır. Bileşik kaynak, başka bir yapılandırma içinde DSC kaynağı olarak kullanılan iç içe ve parametreli bir yapılandırmadır. Bu, temel alınan bileşik kaynakların (parametreli yapılandırmaların) tek tek yönetilmesine ve oluşturulmasına izin verirken karmaşık yapılandırmaların oluşturulmasına olanak sağlar.

Azure Otomasyonu [bileşik kaynakların içeri ve derlemesini](automation-dsc-compile.md#compiling-configurations-in-azure-automation-that-contain-composite-resources)sunar.
Bileşik kaynaklar Otomasyon hesabınıza alındıktan sonra, **Durum Yapılandırması (DSC)** sayfasında **yapılandırma deneyimi oluştur** ' u kullanabilirsiniz.

## <a name="composing-a-configuration-from-composite-resources"></a>Bileşik kaynaklardan yapılandırma oluşturma

Azure portal bileşik kaynaklardan yapılan bir yapılandırmayı atamadan önce oluşturmanız gerekir. Bu işlem, **yapılandırmalar** veya **derlenen yapılandırmalar** SEKMELERINDE **Durum Yapılandırması (DSC)** sayfasında **yapılandırma oluştur** kullanılarak yapılabilir.

1. [Azure Portal](https://portal.azure.com)’ında oturum açın.
1. Sol tarafta **tüm kaynaklar** ' a ve ardından Otomasyon hesabınızın adına tıklayın.
1. **Otomasyon hesabı** sayfasında, **yapılandırma yönetimi**altında **Durum Yapılandırması (DSC)** öğesini seçin.
1. **Durum Yapılandırması (DSC)** sayfasında, **yapılandırmalar** veya **derlenen yapılandırmalar** sekmesine tıklayın ve ardından sayfanın en üstündeki menüde **yapılandırma oluştur** ' a tıklayın.
1. **Temel bilgiler** adımında yeni yapılandırma adını (gerekli) belirtin ve yeni yapılandırmanıza eklemek istediğiniz her bileşik kaynağın satırındaki herhangi bir yere tıklayın ve ardından **İleri** ' ye tıklayın veya **kaynak kodu** adımına tıklayın. Aşağıdaki adımlar için **Psexecutionpolicy** ve **RenameAndDomainJoin** bileşik kaynaklarını seçtik.
   @no__t-yapılandırma oluşturma sayfasının temel kavramları adımının 0 ekran görüntüsü @ no__t-1
1. **Kaynak kodu** adımı, seçili bileşik kaynakların oluşturulan yapılandırmasının nasıl göründüğünü gösterir. Tüm parametrelerin birleştirilmesini ve bunların bileşik kaynağa nasıl geçtiğini görebilirsiniz. Yeni kaynak kodu gözden geçirmeyi tamamladığınızda, **İleri** ' ye tıklayın veya **Parametreler** adımına tıklayın.
   yapılandırma oluşturma sayfasının kaynak kodu adımının ![Ekran görüntüsü @ no__t-1
1. **Parametreler** adımında, her bileşik kaynağın sağlandığı parametre, sağlanabilmeleri için sunulur. Bir parametrenin açıklaması varsa, parametre alanının yanında görüntülenir. Bir alan **PSCredential** türü parametre ise, yapılandırılacak açılan liste geçerli Otomasyon hesabındaki **kimlik bilgisi** nesnelerinin bir listesini sağlar. Bir **+ kimlik bilgisi ekle** seçeneği de mevcuttur. Tüm gerekli parametreler sağlandıktan sonra **Kaydet ve derle**' ye tıklayın.
   yapılandırma oluşturma sayfasının parametre adımının ![Ekran görüntüsü @ no__t-1

Yeni yapılandırma kaydedildikten sonra, derleme için gönderilir. Derleme işinin durumu, içeri aktarılan tüm yapılandırmalar gibi görüntülenebilir. Daha fazla bilgi için bkz. [derleme Işini görüntüleme](automation-dsc-getting-started.md#viewing-a-compilation-job).

Derleme başarıyla tamamlandığında, yeni yapılandırma **derlenen yapılandırmalar** sekmesinde görünür. Bu sekmede görünür olduktan sonra, [bir düğümü farklı bir düğüm yapılandırmasına yeniden atama](automation-dsc-getting-started.md#reassigning-a-node-to-a-different-node-configuration)içindeki adımlar kullanılarak yönetilen bir düğüme atanabilir.

## <a name="next-steps"></a>Sonraki adımlar

- Başlamak için bkz. [Azure Otomasyonu durum yapılandırması ile çalışmaya](automation-dsc-getting-started.md) başlama
- Düğümlerin nasıl ekleneceğini öğrenmek için bkz. [Azure Otomasyonu durum yapılandırmasına göre yönetim için makineleri ekleme](automation-dsc-onboarding.md)
- Hedef düğümlere atayabilmeniz için DSC yapılandırmalarını derleme hakkında bilgi edinmek için bkz. [Azure Otomasyonu durum yapılandırmasında yapılandırmaları derleme](automation-dsc-compile.md)
- PowerShell cmdlet başvurusu için bkz. [Azure Otomasyonu durum yapılandırması cmdlet 'leri](/powershell/module/azurerm.automation/#automation)
- Fiyatlandırma bilgileri için bkz. [Azure Otomasyonu durum yapılandırması fiyatlandırması](https://azure.microsoft.com/pricing/details/automation/)
- Azure Otomasyonu durum yapılandırması 'nı sürekli bir dağıtım ardışık düzeninde kullanmaya ilişkin bir örnek görmek için bkz. [Azure Otomasyonu durum yapılandırması ve Chocolatey kullanarak sürekli dağıtım](automation-dsc-cd-chocolatey.md)
