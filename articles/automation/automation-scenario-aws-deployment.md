---
title: Azure Otomasyonu runbook ile Amazon Web Services VM dağıtma
description: Bu makalede, bir Amazon Web Services VM 'nin oluşturulmasını otomatikleştirme konusu açıklanmaktadır.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 05c06d368dca03e1cb7f07c064f7899300f05cf7
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106169020"
---
# <a name="deploy-an-amazon-web-services-vm-with-a-runbook"></a>Runbook ile Amazon Web Services VM dağıtma

Bu makalede, Amazon Web Service (AWS) aboneliğinizde bir sanal makine sağlamak için Azure Otomasyonu 'Nu nasıl kullanabileceğinizi öğrenirsiniz ve bu VM 'ye, AWS 'nin VM 'yi "etiketleme" olarak başvurduğu özel bir ad verebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Bir Azure Otomasyonu hesabınız ve bir Amazon Web Services (AWS) aboneliğine sahip olmanız gerekir. Azure Otomasyonu hesabı ayarlama ve AWS aboneliği kimlik bilgilerinizle yapılandırma hakkında daha fazla bilgi için, [Amazon Web Services kimlik doğrulamasını yapılandırma](automation-config-aws-account.md)konusunu gözden geçirin. Bu hesaba aşağıdaki bölümlerde başvuru yaptığınız için, bu hesap, devam etmeden önce AWS abonelik kimlik bilgilerinizle oluşturulmalıdır veya güncelleştirilir.

## <a name="deploy-amazon-web-services-powershell-module"></a>Amazon Web Services PowerShell modülünü dağıtma

VM sağlama runbook 'u, işini yapmak için AWS PowerShell modülünü kullanır. AWS abonelik kimlik bilgilerinizle yapılandırılmış Otomasyon hesabınıza modülünü eklemek için aşağıdaki adımları kullanın.  

1. Web tarayıcınızı açın ve [PowerShell Galerisi](https://www.powershellgallery.com/packages/AWSPowerShell/) gidin ve **Azure Otomasyonu 'na dağıt düğmesine** tıklayın.<br><br> ![AWS PS modülü Içeri aktarma](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. Azure oturum açma sayfasına götürülürsünüz ve kimlik doğrulamasından sonra, Azure portal yönlendirilecektir ve aşağıdaki sayfayla karşılaşırsınız:<br><br> ![Modül sayfasını içeri aktar](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Kullanılacak Otomasyon hesabını seçin ve dağıtımı başlatmak için **Tamam** ' a tıklayın.

   > [!NOTE]
   > Azure Otomasyonu bir PowerShell modülünü içeri aktardığında cmdlet 'leri ayıklar. Otomasyon modülün içeri ve cmdlet 'leri ayıklayarak tamamen bitene kadar etkinlikler görünmez. Bu işlem birkaç dakika sürebilir.  
   > <br>

1. Azure portalında, Otomasyon hesabınızı açın.
2. **Varlıklar** kutucuğuna tıklayın ve varlıklar bölmesinde **modüller**' i seçin.
3. Modüller sayfasında, **Awspowershell** modülünü listede görürsünüz.

## <a name="create-aws-deploy-vm-runbook"></a>AWS dağıtım VM runbook 'u oluşturma

AWS PowerShell modülü dağıtıldıktan sonra, bir PowerShell betiği kullanarak AWS 'de bir sanal makine sağlamayı otomatik hale getirmek için bir runbook 'u yazabilirsiniz. Aşağıdaki adımlarda, yerel PowerShell betiğini Azure Otomasyonu 'nda nasıl kullanabileceğiniz gösterilmektedir.  

> [!NOTE]
> Bu betikle ilgili daha fazla seçenek ve bilgi için lütfen [PowerShell Galerisi](https://www.powershellgallery.com/packages/New-AwsVM/)ziyaret edin.
> 

1. PowerShell komut New-AwsVM bir PowerShell oturumu açarak ve aşağıdaki komutu yazarak PowerShell Galerisi indirin:<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. Azure portal Otomasyon hesabınızı açın ve **Işlem Otomasyonu** altında **runbook 'lar** ' ı seçin.  
3. Runbook 'Lar sayfasında, **runbook Ekle**' yi seçin.
4. Runbook Ekle bölmesinde **hızlı oluştur** ' u seçerek yeni bir runbook oluşturun.
5. Runbook özellikleri bölmesinde, runbook 'larınız için bir ad yazın.
6. **Runbook türü** aşağı açılan listesinden **PowerShell**' i seçin ve ardından **Oluştur**' a tıklayın.<br><br> ![Runbook oluştur bölmesi](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
7. PowerShell runbook 'Unu Düzenle sayfası göründüğünde, PowerShell betiğini kopyalayıp Runbook yazma tuvaline yapıştırın.<br><br> ![Runbook PowerShell betiği](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    Örnek PowerShell betiği ile çalışırken aşağıdakileri aklınızda edin:

    * Runbook, bir dizi varsayılan parametre değeri içerir. Tüm varsayılan değerleri değerlendirin ve gerektiğinde güncelleştirin.
    * AWS kimlik bilgilerinizi öğesinden farklı adlı bir kimlik bilgisi varlığı olarak depoladıysanız `AWScred` , satır 57 ' de betiği uygun şekilde eşleşecek şekilde güncelleştirmeniz gerekir.  
    * PowerShell 'de AWS CLı komutlarıyla çalışırken, özellikle bu örnek runbook ile, AWS bölgesini belirtmeniz gerekir. Aksi takdirde, cmdlet 'ler başarısız olur. Daha ayrıntılı bilgi için AWS 'yi görüntüle for PowerShell for PowerShell belgesinde [AWS bölgesini belirtin](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) .  

8. AWS aboneliğinizden görüntü adlarının listesini almak için PowerShell ıSE ' yi başlatın ve AWS PowerShell modülünü içeri aktarın. İle ıSE ortamınızda değiştirerek AWS 'ye karşı kimlik doğrulaması `Get-AutomationPSCredential` yapın `AWScred = Get-Credential` . Bu bildirimde kimlik bilgileriniz istenir ve parola için Kullanıcı adı ve gizli erişim anahtarınız için erişim anahtarı KIMLIĞINIZ sağlayabilirsiniz. 

      ```powershell
        #Sample to get the AWS VM available images
        #Please provide the path where you have downloaded the AWS PowerShell module
        Import-Module AWSPowerShell
        $AwsRegion = "us-west-2"
        $AwsCred = Get-Credential
        $AwsAccessKeyId = $AwsCred.UserName
        $AwsSecretKey = $AwsCred.GetNetworkCredential().Password
   
        # Set up the environment to access AWS
        Set-AwsCredentials -AccessKey $AwsAccessKeyId -SecretKey $AwsSecretKey -StoreAs AWSProfile
        Set-DefaultAWSRegion -Region $AwsRegion
   
        Get-EC2ImageByName -ProfileName AWSProfile
      ```
        
    Aşağıdaki çıktı döndürülür:<br><br>
   ![AWS görüntülerini al](./media/automation-scenario-aws-deployment/powershell-ise-output.png)<br>  
9. Görüntü adlarından birini, runbook 'ta olduğu gibi bir Otomasyon değişkeninde kopyalayıp yapıştırın `$InstanceType` . Bu örnekte, ücretsiz AWS katmanlı aboneliğini kullanıyorsunuz, runbook örneğinizden **T2. Micro** kullanıyorsunuz.  
10. Runbook 'u kaydedin, ardından **Yayımla** ' ya tıklayarak runbook 'u yayımlayın ve istendiğinde **Evet** ' e tıklayın.

### <a name="test-the-aws-vm-runbook"></a>AWS VM runbook 'unu test etme

1. Runbook 'un `AWScred` AWS 'ye göre kimlik doğrulaması için çağrılan bir varlık oluşturduğunu doğrulayın veya betiği kimlik bilgileri varlık adınızla başvuracak şekilde güncelleştirin.    
2. Yeni runbook 'unuzu doğrulayın ve tüm parametre değerlerinin güncelleştirildiğinden emin olun.
AWS PowerShell modülünün Azure Otomasyonu 'na aktarıldığından emin olun.  
3. Azure Otomasyonu ' nda, **günlük ayrıntılı kayıtlarını** ayarlayın ve isteğe bağlı olarak ilerleme kayıtlarını Runbook işlem **günlüğü ve Izleme** altında **olarak** **günlüğe kaydedin** .<br><br> ![Runbook günlüğü ve Izleme ](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png) .  
4. Runbook 'u başlatmak için **Başlat** ' a tıklayın, ardından runbook 'u Başlat bölmesi açıldığında **Tamam** ' a tıklayın.
5. Runbook 'U Başlat bölmesinde bir VM adı belirtin. Betikte önceden yapılandırdığınız diğer parametrelerin varsayılan değerlerini kabul edin. Runbook işini başlatmak için **Tamam** ' ı tıklatın.<br><br> ![New-AwsVM runbook 'u Başlat](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
6. Oluşturduğunuz runbook işi için bir Iş bölmesi açılır. Bu bölmeyi kapatın.
7. Runbook Iş bölmesinden **Tüm Günlükler** ' i seçerek işin ilerlemesini görüntüleyebilir ve çıkış akışlarını görüntüleyebilirsiniz.<br><br> ![Akış çıkışı](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
8. VM 'nin sağlandığını doğrulamak için, şu anda oturum açmadıysanız AWS yönetim konsolunda oturum açın.<br><br> ![AWS konsolu dağıtılan VM](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>Sonraki adımlar
 
* Hangi runbook 'ların desteklendiğini öğrenmek için bkz.[Azure Otomasyonu runbook türleri](automation-runbook-types.md).
* Runbook 'larla çalışmak için bkz. [Azure Otomasyonu 'nda runbook 'Ları yönetme](manage-runbooks.md).
* PowerShell ayrıntıları için bkz. [PowerShell belgeleri](/powershell/scripting/overview).
* Betik desteği için bkz. [Azure Otomasyonu 'Nda yerel PowerShell betiği desteği](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
* PowerShell cmdlet başvurusu için bkz. [az. Automation](/powershell/module/az.automation).
