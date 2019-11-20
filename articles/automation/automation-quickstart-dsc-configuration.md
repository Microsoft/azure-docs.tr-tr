---
title: Azure hızlı başlangıç-DSC ile VM yapılandırma | Microsoft Docs
description: Istenen durum yapılandırması olan bir Linux sanal makinesinde lamba yığını yapılandırma
services: automation
ms.service: automation
ms.subservice: dsc
keywords: DSC, yapılandırma, Otomasyon
author: KrisBash
ms.author: krbash
ms.date: 11/06/2018
ms.topic: quickstart
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: d5eca6ffce8390b2f8b4deae589d43f40897756d
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243469"
---
# <a name="configure-a-virtual-machine-with-desired-state-configuration"></a>Istenen durum yapılandırması ile bir sanal makine yapılandırın

Istenen durum yapılandırması 'nı (DSC) etkinleştirerek Windows ve Linux sunucularınızın yapılandırmalarını yönetebilir ve izleyebilirsiniz. İstenen yapılandırmadan alınan yapılandırmalar tanımlanabilir veya otomatik olarak düzeltilebilir. Bu hızlı başlangıç, bir Linux sanal makinesini ekleme ve DSC ile bir lamba yığını dağıtma adımlarını uygulayarak yapılır.

## <a name="prerequisites"></a>Prerequisites

Bu hızlı başlangıcı tamamlayabilmeniz için şunlar gerekir:

* Bir Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).
* Bir Azure Otomasyonu hesabı. Azure Otomasyonu farklı çalıştır hesabı oluşturma yönergeleri için bkz. [Azure farklı çalıştır hesabı](automation-sec-configure-azure-runas-account.md).
* Red Hat Enterprise Linux, CentOS veya Oracle Linux çalıştıran bir Azure Resource Manager VM (klasik değil). VM oluşturma yönergeleri için, [Azure Portal Ilk Linux sanal makinenizi oluşturma](../virtual-machines/linux/quick-create-portal.md) bölümüne bakın.

## <a name="sign-in-to-azure"></a>Azure 'da oturum açın
@No__t-0 ' da Azure 'da oturum açın

## <a name="onboard-a-virtual-machine"></a>Sanal makine ekleme
Bir makineyi eklemek ve Istenen durum yapılandırmasını etkinleştirmek için birçok farklı yöntem vardır. Bu hızlı başlangıçta Otomasyon hesabı aracılığıyla ekleme ele alınmaktadır. [Ekleme](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding) makalesini okuyarak makinelerinizi Istenen durum yapılandırmasına eklemek için farklı yöntemler hakkında daha fazla bilgi edinebilirsiniz.

1. Azure portal sol bölmede **Otomasyon hesapları**' nı seçin. Sol bölmede görünür değilse, **tüm hizmetler** ' e tıklayın ve sonuçta elde edilen görünümde arama yapın.
1. Listede bir Otomasyon hesabı seçin.
1. Otomasyon hesabının sol bölmesinde **Durum Yapılandırması (DSC)** öğesini seçin.
2. **Ekle** ' ye tıklayarak VM Seç sayfasını açın.
3. DSC 'yi etkinleştirmek istediğiniz sanal makineyi bulun. Belirli bir sanal makineyi bulmak için arama alanını ve filtre seçeneklerini kullanabilirsiniz.
4. Sanal makineye tıklayın ve ardından **Bağlan** ' ı seçin.
5. Sanal makine için uygun DSC ayarlarını seçin. Zaten bir yapılandırma hazırladıysanız, bunu *düğüm yapılandırma adı*olarak belirtebilirsiniz. [Yapılandırma modunu](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaConfig) makinenin yapılandırma davranışını denetlemek için ayarlayabilirsiniz.
6. **Tamam**’a tıklayın.

![DSC 'ye bir Azure VM ekleme](./media/automation-quickstart-dsc-configuration/dsc-onboard-azure-vm.png)

Istenen durum yapılandırma uzantısı sanal makineye dağıtıldığında, *bağlantı görüntülenir.*

## <a name="import-modules"></a>Modülleri içeri aktar

Modüller DSC kaynakları içerir ve [PowerShell Galerisi](https://www.powershellgallery.com)birçok bulunabilir. Yapılandırlarınızda kullanılan kaynakların, derlenmeden önce Otomasyon hesabına aktarılması gerekir. Bu öğreticide, **NX** adlı modül gereklidir.

1. Otomasyon hesabının sol bölmesinde, **modüller Galerisi** ' ni (paylaşılan kaynaklar altında) seçin.
1. Adının bir bölümünü yazarak içeri aktarmak istediğiniz modülü arayın: *NX*
1. İçeri aktarmak istediğiniz modüle tıklayın
1. **Içeri aktar** ' a tıklayın

![DSC modülünü içeri aktarma](./media/automation-quickstart-dsc-configuration/dsc-import-module-nx.png)

## <a name="import-the-configuration"></a>Yapılandırmayı içeri aktarma

Bu hızlı başlangıç, makinede Apache HTTP Server, MySQL ve PHP 'yi yapılandıran bir DSC yapılandırması kullanır.

DSC yapılandırması hakkında daha fazla bilgi için bkz. [DSC yapılandırması](https://docs.microsoft.com/powershell/scripting/dsc/configurations/configurations).

Bir metin düzenleyicisinde aşağıdakini yazın ve `LAMPServer.ps1` olarak yerel olarak kaydedin.

```powershell-interactive
configuration LAMPServer {
   Import-DSCResource -module "nx"

   Node localhost {

        $requiredPackages = @("httpd","mod_ssl","php","php-mysql","mariadb","mariadb-server")
        $enabledServices = @("httpd","mariadb")

        #Ensure packages are installed
        ForEach ($package in $requiredPackages){
            nxPackage $Package{
                Ensure = "Present"
                Name = $Package
                PackageManager = "yum"
            }
        }

        #Ensure daemons are enabled
        ForEach ($service in $enabledServices){
            nxService $service{
                Enabled = $true
                Name = $service
                Controller = "SystemD"
                State = "running"
            }
        }
   }
}
```

Yapılandırmayı içeri aktarmak için:

1. Otomasyon hesabının sol bölmesinde **Durum Yapılandırması (DSC)** öğesini seçin ve ardından **yapılandırmalar** sekmesine tıklayın.
2. **+ Ekle** 'ye tıklayın
3. Önceki adımda kaydettiğiniz *yapılandırma dosyasını* seçin
4. **Tamam**’a tıklayın.

## <a name="compile-a-configuration"></a>Yapılandırma derle

DSC yapılandırmaları bir düğüme atanmadan önce bir düğüm yapılandırmasına (MOF belgesi) derlenmelidir. Derleme yapılandırmayı doğrular ve parametre değerlerinin girişine izin verir. Yapılandırma derleme hakkında daha fazla bilgi edinmek için bkz. [Azure 'Da yapılandırmaları derleme Automation DSC](https://docs.microsoft.com/azure/automation/automation-dsc-compile)

Yapılandırmayı derlemek için:

1. Otomasyon hesabının sol bölmesinde **Durum Yapılandırması (DSC)** öğesini seçin ve ardından **yapılandırmalar** sekmesine tıklayın.
1. Önceki adımda içeri aktardığınız yapılandırmayı "LAMPServer" olarak seçin
1. Menü seçeneklerinde **Derle** ' ye ve ardından **Evet** ' e tıklayın.
1. Yapılandırma görünümünde, sıraya alınmış yeni bir *derleme işi* görürsünüz. İş başarıyla tamamlandığında, bir sonraki adıma geçmeye hazırlanın. Herhangi bir başarısızlık varsa, Ayrıntılar için derleme işine tıklayabilirsiniz.

## <a name="assign-a-node-configuration"></a>Düğüm yapılandırması atama

Derlenen *düğüm yapılandırması* , DSC düğümlerine atanabilir. Atama, yapılandırmayı makineye uygular ve bu yapılandırmadan herhangi bir drara için izleyicileri (veya otomatik olarak düzeltir).

1. Otomasyon hesabının sol bölmesinde, * * durum yapılandırması (DSC) öğesini seçin ve ardından **düğümler** sekmesine tıklayın.
1. Yapılandırma atamak istediğiniz düğümü seçin
1. **Düğüm yapılandırması ata** ' ya tıklayın
1. Atanacak *düğüm yapılandırmasını*seçin  - **lampserver. localhost** -atamak ve **Tamam 'a** tıklayın
1. Derlenen yapılandırma artık düğüme atanır ve düğüm durumu *Beklemede*olarak değişir. Sonraki düzenli denetiminde, düğüm yapılandırmayı alır, uygular ve durumu geri bildirir. Düğümün ayarlarına bağlı olarak, düğümün yapılandırmayı alması 30 dakika kadar sürebilir. Anında denetimi zorlamak için Linux sanal makinesinde aşağıdaki komutu yerel olarak çalıştırabilirsiniz: `sudo /opt/microsoft/dsc/Scripts/PerformRequiredConfigurationChecks.py`

![Düğüm yapılandırması atama](./media/automation-quickstart-dsc-configuration/dsc-assign-node-configuration.png)

## <a name="viewing-node-status"></a>Düğüm durumunu görüntüleme

Tüm yönetilen düğümlerin durumu durum **yapılandırması 'nda (DSC)** ve ardından Otomasyon hesabındaki **düğümler** sekmesinde bulunabilir. Görüntülenecek durum, düğüm yapılandırması veya ad aramasına göre filtre uygulayabilirsiniz.

![DSC düğüm durumu](./media/automation-quickstart-dsc-configuration/dsc-node-status.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir Linux sanal makinesini DSC 'ye eklendi, lamba yığınına yönelik bir yapılandırma oluşturdunuz ve sanal makineye dağıttınız. Sürekli dağıtımı etkinleştirmek üzere Automation DSC nasıl kullanabileceğinizi öğrenmek için, makaleye devam edin:

> [!div class="nextstepaction"]
> [DSC ve Chocolatey kullanarak sanal makineye sürekli dağıtım](./automation-dsc-cd-chocolatey.md)

* PowerShell Istenen durum yapılandırması hakkında daha fazla bilgi edinmek için bkz. [PowerShell Istenen durum yapılandırmasına genel bakış](https://docs.microsoft.com/powershell/scripting/dsc/overview/overview).
* PowerShell 'den Automation DSC yönetme hakkında daha fazla bilgi için bkz. [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.automation/)
* DSC raporlarını raporlama ve uyarma için Azure Izleyici günlüklerine iletme hakkında bilgi edinmek için bkz. [DSC raporlamasını Azure izleyici günlüklerine iletme](https://docs.microsoft.com/azure/automation/automation-dsc-diagnostics) 

