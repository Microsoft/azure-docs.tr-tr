---
title: Azure DevTest Labs, Azure işlem hatları sürekli tümleştirme ve teslim komut zinciriyle tümleştirmeyi | Microsoft Docs
description: Azure DevTest Labs, Azure işlem hatları sürekli tümleştirme ve teslim işlem hattı tümleştirme hakkında bilgi edinin
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: a26df85e-2a00-462b-aac1-dd3539532569
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2019
ms.author: spelluru
ms.openlocfilehash: 20ba297d22e26aa8c7e20db300173f12582d257e
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/09/2019
ms.locfileid: "71224478"
---
# <a name="integrate-azure-devtest-labs-into-your-azure-pipelines-cicd-pipeline"></a>Azure DevTest Labs Azure Pipelines CI/CD işlem hattınızla tümleştirin

Azure Pipelines sürekli tümleştirme ve sürekli teslim (CI/CD) derleme ve yayın işlem hatlarınızı Azure DevTest Labs tümleştirmek için *Azure DevTest Labs görevleri* uzantısını kullanabilirsiniz. Uzantı aşağıdakiler de dahil olmak üzere çeşitli görevleri yüklüyor: 

- Sanal makine (VM) oluşturma
- VM'den özel görüntü oluşturma
- VM silme 

Bu görevler, örneğin belirli bir test görevi için bir *altın görüntü* VM 'sini hızlı bir şekilde dağıtmayı ve test tamamlandığında VM 'yi silmeyi kolaylaştırır.

Bu makalede, bir VM oluşturup dağıtmak, özel bir görüntü oluşturmak ve ardından VM 'yi tek bir yayın işlem hattı olarak silmek için Azure DevTest Labs görevlerinin nasıl kullanılacağı gösterilmektedir. Görevleri genellikle kendi özel derlemeniz, test etmeniz ve dağıtım hatlarında tek tek gerçekleştirirsiniz.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="prerequisites"></a>Önkoşullar

- [Azure DevOps](https://dev.azure.com) kuruluşunuza kaydolun veya oturum açın ve kuruluşta [bir proje oluşturun](/vsts/organizations/projects/create-project) . 
  
- Visual Studio Market Azure DevTest Labs görevleri uzantısını yükler.
  
  1. Git [Azure DevTest Labs görevlerini](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
  1. **Ücretsiz Al**seçeneğini belirleyin.
  1. Açılan listeden Azure DevOps kuruluşunuzu seçin ve ardından **Install**' ı seçin. 
  
## <a name="create-the-template-to-build-an-azure-vm"></a>Azure VM oluşturmak için şablon oluşturma 

Bu bölümde, isteğe bağlı olarak bir Azure VM oluşturmak için kullandığınız Azure Resource Manager şablonunun nasıl oluşturulacağı açıklanmaktadır.

1. Aboneliğinizde bir Kaynak Yöneticisi şablonu oluşturmak için, [Kaynak Yöneticisi şablonu kullanma](devtest-lab-use-resource-manager-template.md)bölümündeki yordamı izleyin.
   
1. Resource Manager şablonu oluşturmadan önce ekleme [WinRM yapıt](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm) VM oluşturma işleminin parçası olarak. *Hedef makinelerde* *Azure dosya kopyalama* ve PowerShell gibi dağıtım görevlerinin WinRM erişimi olmalıdır. WinRM yapıtı, VM 'nin tam etki alanı adı (FQDN) olması gereken bir parametre olarak ana bilgisayar adı gerektirir. 
   
   > [!NOTE]
   > WinRM paylaşılan bir IP adresi ile kullandığınızda, bir dış bağlantı noktası WinRM bağlantı noktasına eşlemek için bir NAT kuralı eklemeniz gerekir. VM 'yi genel bir IP adresi ile oluşturursanız, NAT kuralına gerek kalmaz.
   
   
1. Şablonu, *Createvmtemplate. JSON*adlı bir dosya olarak bilgisayarınıza kaydedin.
   
1. Şablonu kaynak denetim sisteminize iade edin.

## <a name="create-a-script-to-get-vm-properties"></a>VM özelliklerini almak için betik oluşturma

Sürüm ardışık düzeninde *hedef makinelerde* *Azure dosya kopyası* veya PowerShell gibi görev adımlarını çalıştırdığınızda aşağıdaki betik, bir uygulamayı bir VM 'ye dağıtmanız için gereken değerleri toplar. Bu görevleri genellikle uygulamanızı bir Azure VM 'ye dağıtmak için kullanacaksınız. Görevler, VM kaynak grubu adı, IP adresi ve FQDN gibi değerler gerektirir.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Betik dosyasını oluşturmak için:

1. Bir metin düzenleyicisinde açın ve aşağıdaki komut dosyası içine yapıştırın.
   
   ```powershell
   Param( [string] $labVmId)

   $labVmComputeId = (Get-AzResource -Id $labVmId).Properties.ComputeId

   # Get lab VM resource group name
   $labVmRgName = (Get-AzResource -Id $labVmComputeId).ResourceGroupName

   # Get the lab VM Name
   $labVmName = (Get-AzResource -Id $labVmId).Name

   # Get lab VM public IP address
   $labVMIpAddress = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
                   -Name $labVmName).IpAddress

   # Get lab VM FQDN
   $labVMFqdn = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
              -Name $labVmName).DnsSettings.Fqdn

   # Set a variable labVmRgName to store the lab VM resource group name
   Write-Host "##vso[task.setvariable variable=labVmRgName;]$labVmRgName"

   # Set a variable labVMIpAddress to store the lab VM Ip address
   Write-Host "##vso[task.setvariable variable=labVMIpAddress;]$labVMIpAddress"

   # Set a variable labVMFqdn to store the lab VM FQDN name
   Write-Host "##vso[task.setvariable variable=labVMFqdn;]$labVMFqdn"
   ```

1. Dosyayı *GetLabVMParams. ps1*gibi bir adla kaydedin ve kaynak denetim sisteminize iade edin. 

## <a name="create-a-release-pipeline-in-azure-pipelines"></a>Azure Pipelines’da yayın işlem hattı oluşturma

Yeni bir yayın işlem hattı oluşturmak için:

1. Azure DevOps proje sayfasından sol gezinmede işlem **hattı** > **yayınları** ' nı seçin.
1. **Yeni Işlem hattı**' nı seçin.
1. **Şablon seçin**altında aşağı kaydırın ve **boş iş**' ı seçin ve ardından **Uygula**' yı seçin.

### <a name="add-and-set-variables"></a>Değişken ekleme ve ayarlama

İşlem hattı görevleri, Azure portal Kaynak Yöneticisi şablonu oluşturduğunuzda VM 'ye atadığınız değerleri kullanır. 

Değerler için değişken eklemek için: 

1. İşlem hattı sayfasında, **değişkenler** sekmesini seçin.
   
1. Her değişken için, **Ekle** ' yi seçin ve adı ve değeri girin:
   
   |Name|Value|
   |---|---|
   |*vmName*|Kaynak Yöneticisi şablonunda atadığınız VM adı|
   |*Nitelen*|VM 'ye erişim için Kullanıcı adı|
   |*Parola*|Kullanıcı adı için parola. Parolayı gizlemek ve korumak için kilit simgesini seçin.

### <a name="create-a-devtest-labs-vm"></a>DevTest Labs VM oluşturma

Sonraki adımda, gelecekteki dağıtımlar için kullanılacak altın görüntü sanal makinesi oluşturulur. VM 'yi, *Azure DEVTEST Labs VM oluştur* görevi kullanarak Azure DevTest Labs örneğiniz içinde oluşturursunuz.

1. Yayın ardışık düzen işlem **hattı** sekmesinde, aşama **görevleri**' ni görüntülemek için **1. aşamada** köprü metni seçin ve ardından **Aracı işi**' nin **+** yanındaki artı işaretini seçin. 
   
1. **Görev Ekle**altında **VM oluştur Azure DevTest Labs**seçin ve **Ekle**' yi seçin. 
   
1. Sol bölmede **Azure DEVTEST Labs VM oluştur** ' u seçin. 

1. Sağ bölmede, formu aşağıdaki gibi doldurun:
   
   |Alan|Value|
   |---|---|
   |**Azure RM aboneliği**|**Kullanılabilir Azure hizmeti bağlantılarından** veya açılan menüdeki **kullanılabilir Azure aboneliklerinden** bir hizmet bağlantısı veya abonelik seçin ve gerekirse **Yetkilendir** ' i seçin.<br /><br />**Not:** Azure aboneliğinize daha kısıtlı bir izin bağlantısı oluşturma hakkında bilgi için bkz. [Azure Resource Manager hizmet uç noktası](/azure/devops/pipelines/library/service-endpoints#sep-azure-rm).|
   |**Laboratuvar adı**|Laboratuvar VM 'sinin oluşturulacağı mevcut bir laboratuvarın adını seçin.|
   |**Şablon adı**|Kaynak kodu deponuza kaydettiğiniz şablon dosyasının tam yolunu ve adını girin. Yolu basitleştirmek için yerleşik özellikleri kullanabilirsiniz, örneğin:<br /><br />`$(System.DefaultWorkingDirectory)/Templates/CreateVMTemplate.json`|
   |**Şablon parametreleri**|Daha önce tanımladığınız değişkenlerin parametrelerini girin:<br /><br />`-newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)`|
   |**Çıkış değişkenleri** > **Laboratuvar VM kimliği**|Oluşturulan laboratuvar VM KIMLIĞI için değişkeni girin. Varsayılan **Labvmıd**kullanırsanız, sonraki görevlerdeki değişkenine *$ (labvmıd)* olarak başvurabilirsiniz.<br /><br />Varsayılan dışında bir ad oluşturabilirsiniz, ancak sonraki görevlerde doğru adı kullanmayı unutmayın. Laboratuvar VM KIMLIĞINI aşağıdaki biçimde yazabilirsiniz:<br /><br />`/subscriptions/{subscription Id}/resourceGroups/{resource group Name}/providers/Microsoft.DevTestLab/labs/{lab name}/virtualMachines/{vmName}`|

### <a name="collect-the-details-of-the-devtest-labs-vm"></a>DevTest Labs VM 'sinin ayrıntılarını toplayın

DevTest Labs sanal makinenin ayrıntıları toplamak için daha önce oluşturduğunuz komut dosyasını çalıştırın. 

1. Yayın ardışık düzen işlem **hattı** sekmesinde, aşama **görevleri**' ni görüntülemek için **1. aşamada** köprü metni seçin ve ardından **Aracı işi**' nin **+** yanındaki artı işaretini seçin. 
   
1. **Görev Ekle**' nin altında **Azure PowerShell**' ı seçin ve **Ekle**' yi seçin. 
   
1. Azure PowerShell **betiği seçin: Sol** bölmedeki FilePath. 
   
1. Sağ bölmede, formu aşağıdaki gibi doldurun:
   
   |Alan|Value|
   |---|---|
   |**Azure bağlantı türü**|**Azure Resource Manager**seçin.|
   |**Azure Aboneliği**|Hizmet bağlantınızı veya aboneliğinizi seçin.| 
   |**Betik türü**|**Betik dosyası yolunu**seçin.|
   |**Betik yolu**|Kaynak kodu deponuza kaydettiğiniz PowerShell betiğinin tam yolunu ve adını girin. Yolu basitleştirmek için yerleşik özellikleri kullanabilirsiniz, örneğin:<br /><br />`$(System.DefaultWorkingDirectory/Scripts/GetLabVMParams.ps1`|
   |**Betik bağımsız değişkenleri**|Önceki görev tarafından doldurulmuş olan *Labvmıd* değişkeninin adını girin, örneğin:<br /><br />`-labVmId '$(labVMId)'`|

Komut dosyası gerekli değerleri toplar ve bunları yayın işlem hattının içindeki ortam değişkenlerine depolar, böylece sonraki adımlarda bunlara kolayca başvurabilirsiniz.

### <a name="create-a-vm-image-from-the-devtest-labs-vm"></a>DevTest Labs VM 'den bir VM görüntüsü oluşturma

Sonraki görev, Azure DevTest Labs Örneğinizde yeni dağıtılan VM 'nin bir görüntüsünü oluşturmaktır. Ardından, bir geliştirme görevi yürütme veya bazı testler çalıştırmak istediğiniz zaman isteğe bağlı olarak VM kopyaları oluşturmak için görüntüyü kullanabilirsiniz. 

1. Yayın ardışık düzen işlem **hattı** sekmesinde, aşama **görevleri**' ni görüntülemek için **1. aşamada** köprü metni seçin ve ardından **Aracı işi**' nin **+** yanındaki artı işaretini seçin. 
   
1. **Görev Ekle**' nin altında **Azure DevTest Labs özel görüntü oluştur**' u seçin ve **Ekle**' yi seçin. 
   
1. Görev aşağıdaki gibi yapılandırın:
   
   |Alan|Value|
   |---|---|
   |**Azure RM aboneliği**|Hizmet bağlantınızı veya aboneliğinizi seçin.|
   |**Laboratuvar adı**|Görüntünün oluşturulacağı mevcut bir laboratuvarın adını seçin.|
   |**Özel görüntü adı**|Özel görüntü için bir ad girin.|
   |**Açıklama** seçim|Doğru görüntüyü daha sonra seçmenizi kolaylaştırmak için bir açıklama girin.|
   |**Kaynak laboratuvar VM** > **kaynağı Laboratuvarı VM kimliği**|Labvmıd değişkeninin varsayılan adını değiştirdiyseniz buraya girin. Varsayılan değer **$(labVMId)** .|
   |**Çıkış değişkenleri** > **özel görüntü kimliği**|Gerekirse değişkenin varsayılan adını düzenleyebilirsiniz.|
   
### <a name="deploy-your-app-to-the-devtest-labs-vm-optional"></a>Uygulamanızı DevTest Labs sanal makinesine dağıtma (isteğe bağlı)

Uygulamanızı yeni DevTest Labs sanal makinesine dağıtmak için görev ekleyebilirsiniz. Uygulamayı dağıtmak için genellikle kullandığınız görevler, hedef makinelerde *Azure dosya kopyalama* ve *PowerShell '* tir.

Bu görevlerin parametreleri için ihtiyaç duyduğunuz VM bilgileri, yayın ardışık düzeninde bulunan **Labvmrgname**, **labvmı adresi**ve **labvmfqdn** adlı üç yapılandırma değişkenine depolanır. Yalnızca DevTest Labs VM ve özel bir görüntü için bir uygulamayı dağıtmadan oluşturmayla denemek istiyorsanız, bu adımı atlayabilirsiniz.

### <a name="delete-the-vm"></a>VM’yi silin

Son görev, Azure DevTest Labs örneğinize dağıttığınız sanal makineyi silmektir. Geliştirme görevleri yürütmek ya da gereken dağıtılmış VM üzerinde testler sonra normalde VM siler. 

1. Yayın ardışık düzen işlem **hattı** sekmesinde, aşama **görevleri**' ni görüntülemek için **1. aşamada** köprü metni seçin ve ardından **Aracı işi**' nin **+** yanındaki artı işaretini seçin. 
   
1. **Görev Ekle**' nin altında, **VM 'yi Sil Azure DevTest Labs**seçin ve **Ekle**' yi seçin. 
   
1. Görev aşağıdaki gibi yapılandırın:
   
   - **Azure RM aboneliği**altında hizmet bağlantınızı veya aboneliğinizi seçin. 
   - **Laboratuvar VM 'si kimliği**Için, labvmıd değişkeninin varsayılan adını değiştirdiyseniz buraya girin. Varsayılan değer **$(labVMId)** .
   
### <a name="save-the-release-pipeline"></a>Yayın ardışık düzenini kaydetme

Yeni sürüm ardışık düzenini kaydetmek için:

1. Yayın ardışık düzeni sayfasında **Yeni yayın işlem hattı** adı ' nı seçin ve işlem hattı için yeni bir ad girin. 
   
1. Sağ üst köşedeki **Kaydet** simgesini seçin. 

## <a name="create-and-run-a-release"></a>Yayın oluşturma ve çalıştırma

Yeni işlem hattını kullanarak bir yayın oluşturmak ve çalıştırmak için:

1. Yayın ardışık düzeni sayfasında sağ üstteki **yayın oluştur** ' u seçin. 
   
1. **Yapıtlar**altında, en son yapıyı seçin ve ardından **Oluştur**' u seçin.
   
1. Her sürüm aşamasında, sanal makine oluşturma, görüntü oluşturma ve VM silme işlemini görüntülemek için Azure portal DevTest Labs örneğinizin görünümünü yenileyin.

Özel görüntüyü, ihtiyacınız olduğunda VM 'Ler oluşturmak için kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
- Bilgi edinmek için nasıl [Resource Manager şablonları ile çoklu VM ortamları oluşturma](devtest-lab-create-environment-from-arm.md).
- DevTest Labs Otomasyon için daha fazla hızlı başlangıç Resource Manager şablonları keşfedin [genel DevTest Labs GitHub deposunu](https://github.com/Azure/azure-quickstart-templates).
- Gerekirse, bkz. [Azure DevOps sorun giderme](https://docs.microsoft.com/azure/devops/pipelines/troubleshooting) sayfası.
 
