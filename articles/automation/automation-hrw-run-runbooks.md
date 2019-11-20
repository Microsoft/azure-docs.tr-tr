---
title: Azure Otomasyonu karma Runbook Worker üzerinde runbook 'ları çalıştırma
description: Bu makale, karma Runbook Worker rolüyle yerel veri merkezinizdeki veya bulut sağlayıcınızdaki makinelerde runbook çalıştırmaya ilişkin bilgiler sağlar.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 01/29/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9c7084954fe58351a6f9af40552714faa34685ad
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73887055"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Karma Runbook Worker üzerinde runbook çalıştırma

Azure Otomasyonu 'nda çalışan runbook 'ların yapısında ve karma Runbook Worker üzerinde çalışan runbook 'larda farklılık yoktur. En çok büyük olasılıkla kullandığınız runbook 'lar farklı olabilir. Bu fark, karma bir runbook worker 'ı hedefleyen runbook 'ların genellikle yerel bilgisayardaki kaynakları, dağıtıldığı Yerel ortamdaki kaynaklara göre yönetmesidir. Azure Otomasyonu 'ndaki runbook 'lar genellikle Azure bulutundaki kaynakları yönetir.

Bir karma runbook çalışanı üzerinde çalıştırılacak runbook 'ları yazdığınızda, karma çalışanı barındıran makinede runbook 'ları düzenlemeniz ve test etmeniz gerekir. Konak makinede, yerel kaynakları yönetmeniz ve erişmeniz için ihtiyacınız olan tüm PowerShell modülleri ve ağ erişimi vardır. Karma çalışan makinesinde bir runbook test edildikten sonra, karma çalışanında çalışmak için kullanılabilir olduğu Azure Otomasyonu ortamına yükleyebilirsiniz. Linux üzerinde Windows için yerel sistem hesabı veya özel bir Kullanıcı `nxautomation` hesabı altında çalışan işlerin bilmemiz önemlidir. Linux 'ta bu, `nxautomation` hesabının modüllerinizi depoladığınız konuma erişiminin olduğundan emin olmanız gerektiği anlamına gelir. [Install-Module](/powershell/module/powershellget/install-module) cmdlet 'ini kullandığınızda, `nxautomation` hesabının erişimi olduğunu onaylamak Için **allusers** öğesini `-Scope` parametresi olarak belirtin.

Linux üzerinde PowerShell hakkında daha fazla bilgi için bkz. [Windows dışı platformlarda PowerShell Için bilinen sorunlar](https://docs.microsoft.com/powershell/scripting/whats-new/known-issues-ps6?view=powershell-6#known-issues-for-powershell-on-non-windows-platforms).

## <a name="starting-a-runbook-on-hybrid-runbook-worker"></a>Karma runbook çalışanında runbook başlatma

[Azure Otomasyonu 'Nda runbook başlatma,](automation-starting-a-runbook.md) runbook başlatmaya yönelik farklı yöntemler açıklar. Karma Runbook Worker, karma Runbook Worker grubunun adını belirtebileceğiniz bir **runon** seçeneği ekler. Bir grup belirtildiğinde, runbook alınır ve o gruptaki çalışanlarından biri tarafından çalıştırılır. Bu seçenek belirtilmezse, Azure Otomasyonu 'nda normal olarak çalıştırılır.

Azure portal bir runbook 'u başlattığınızda, **Azure** veya **hibrit Worker**' ı seçebileceğiniz bir **Çalıştır** seçeneği sunulur. **Karma çalışanı**seçerseniz, grubu bir açılan listeden seçebilirsiniz.

**Runon** parametresini kullanın. Windows PowerShell kullanarak MyHybridGroup adlı bir karma Runbook Worker grubunda test-runbook adlı bir runbook 'u başlatmak için aşağıdaki komutu kullanabilirsiniz.

```azurepowershell-interactive
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> **Runon** parametresi, Microsoft Azure PowerShell sürüm 0.9.1 ' deki **Start-AzureAutomationRunbook** cmdlet 'ine eklendi. Daha önce yüklenmiş bir [sürüm varsa en son sürümü indirmeniz](https://azure.microsoft.com/downloads/) gerekir. Bu sürümü yalnızca runbook 'u PowerShell 'den başlatduyduğunuz bir iş istasyonuna yüklemeniz gerekir. Runbook 'ları bu bilgisayardan başlatmayı düşünmüyorsanız, çalışan bilgisayara yüklemeniz gerekmez "

## <a name="runbook-permissions"></a>Runbook izinleri

Karma Runbook Worker üzerinde çalışan runbook 'lar, Azure 'da olmayan kaynaklara eriştiği için genellikle Azure kaynaklarında kimlik doğrulaması yapan runbook 'lar için kullanılan yöntemi kullanamaz. Runbook yerel kaynaklara kendi kimlik doğrulamasını sağlayabilir ya da [Azure kaynakları için yönetilen kimlikleri](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)kullanarak kimlik doğrulamasını yapılandırabilir. Ayrıca, tüm runbook 'lar için bir kullanıcı bağlamı sağlamak üzere bir RunAs hesabı da belirtebilirsiniz.

### <a name="runbook-authentication"></a>Runbook kimlik doğrulaması

Varsayılan olarak, runbook 'lar Windows için yerel sistem hesabı ve şirket içi bilgisayarda Linux için `nxautomation` özel bir kullanıcı hesabı bağlamında çalışır ve bu nedenle, eriştikleri kaynaklara kendi kimlik doğrulamasını sağlamaları gerekir.

Farklı kaynaklarda kimlik doğrulayabilmeniz için, runbook 'larınızda kimlik bilgilerini belirtmenize izin veren cmdlet 'lerle birlikte [kimlik](automation-credentials.md) bilgilerini ve [sertifika](automation-certificates.md) varlıklarını kullanabilirsiniz. Aşağıdaki örnek bir runbook 'un bilgisayarı yeniden başlatan bir bölümünü gösterir. Bir kimlik bilgisi kıymetine ait kimlik bilgilerini ve bir değişken varlıkından bilgisayarın adını alır ve ardından bu değerleri Restart-Computer cmdlet 'i ile kullanır.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

Ayrıca, [PSCredential ortak parametresiyle](/powershell/module/psworkflow/about/about_workflowcommonparameters)belirtilen kimlik bilgileriyle başka bir bilgisayarda kod blokları çalıştırmanıza olanak tanıyan [InlineScript](automation-powershell-workflow.md#inlinescript)de kullanabilirsiniz.

### <a name="runas-account"></a>RunAs hesabı

Karma Runbook Worker varsayılan olarak Windows için yerel sistem 'i ve Linux 'un runbook 'ları yürütmesi için `nxautomation` özel bir kullanıcı hesabı kullanır. Runbook 'ların yerel kaynaklara kendi kimlik doğrulamasını sağlaması yerine, karma çalışan grubu için bir **runas** hesabı belirtebilirsiniz. Sertifika depoları dahil olmak üzere yerel kaynaklara erişimi olan ve tüm runbook 'lar gruptaki bir karma runbook çalışanı üzerinde çalışırken bu kimlik bilgileri altında çalışan bir [kimlik bilgisi varlığı](automation-credentials.md) belirtirsiniz.

Kimlik bilgisinin Kullanıcı adı aşağıdaki biçimlerden birinde olmalıdır:

* ın
* username@domain
* Kullanıcı adı (Şirket içi bilgisayarda yerel hesaplar için)

Karma çalışanı grubu için bir RunAs hesabı belirtmek için aşağıdaki yordamı kullanın:

1. Yerel kaynaklara erişimi olan bir [kimlik bilgisi varlığı](automation-credentials.md) oluşturun.
2. Azure portal Otomasyon hesabını açın.
3. **Karma çalışanı grupları** kutucuğunu seçin ve ardından grubu seçin.
4. **Tüm ayarlar** ' ı ve ardından **karma çalışan grubu ayarları**' nı seçin.
5. **Farklı Çalıştır** öğesini **varsayılan** olarak **özel**olarak değiştirin.
6. Kimlik bilgilerini seçin ve **Kaydet**' e tıklayın.

### <a name="managed-identities-for-azure-resources"></a>Azure kaynakları için Yönetilen kimlikler

Azure sanal makineleri üzerinde çalışan karma runbook çalışanları, Azure kaynakları için kimlik doğrulaması yapmak üzere Azure kaynakları için Yönetilen kimlikler kullanabilir. Farklı Çalıştır hesapları üzerinden Azure kaynakları için Yönetilen kimlikler kullanmanın birçok avantajı vardır.

* Farklı Çalıştır sertifikasını dışarı aktarmaya ve sonra karma Runbook Worker 'a aktarmaya gerek yok
* Farklı Çalıştır hesabı tarafından kullanılan sertifikayı yenilemeye gerek yok
* Runbook kodunuzda farklı çalıştır bağlantı nesnesini işlemeye gerek yoktur

Karma runbook çalışanında Azure kaynakları için yönetilen bir kimlik kullanmak üzere aşağıdaki adımları gerçekleştirmeniz gerekir:

1. Azure VM oluşturma
2. [SANAL makinenizde Azure kaynakları için Yönetilen kimlikler yapılandırma](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)
3. [VM 'nize bir kaynak grubuna erişim Izni verin Kaynak Yöneticisi](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager) öğreticisi-Windows-VM-Access-ARM. MD # Get-a-Access-Token------VM-------------------------------Resource-Manager
4. [Windows karma Runbook Worker](automation-windows-hrw-install.md#installing-the-windows-hybrid-runbook-worker) 'ı sanal makineye yükler.

Yukarıdaki adımlar tamamlandıktan sonra, Azure kaynaklarında kimlik doğrulaması yapmak için Runbook 'ta `Connect-AzureRmAccount -Identity` kullanabilirsiniz. Bu yapılandırma, farklı çalıştır hesabı kullanma gereksinimini azaltır ve farklı çalıştır hesabı için sertifikayı yönetir.

```powershell
# Connect to Azure using the Managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
Connect-AzureRmAccount -Identity

# Get all VM names from the subscription
Get-AzureRmVm | Select Name
```

> [!NOTE]
> `Connect-AzureRMAccount -Identity`, sistem tarafından atanan bir kimlik ve tek bir kullanıcı tarafından atanan kimlik kullanılarak karma Runbook Worker için işe yarar. HRW üzerinde birden çok kullanıcı tarafından atanan kimlik kullanmanız gerekiyorsa, belirli kullanıcı tarafından atanan kimliği seçmek için `-AccountId` parametresini belirtmeniz gerekir.

### <a name="runas-script"></a>Otomasyon farklı çalıştır hesabı

Azure 'da kaynak dağıtmaya yönelik Otomatikleştirilmiş derleme işleminizin bir parçası olarak, dağıtım dizinizdeki bir görevi veya bir adım kümesini desteklemek için şirket içi sistemlere erişim gerekebilir. Farklı Çalıştır hesabını kullanarak Azure 'da kimlik doğrulamasını desteklemek için, farklı çalıştır hesabı sertifikasını yüklemeniz gerekir.

Aşağıdaki PowerShell runbook 'u **dışarı aktarma-Runascercertificate Meditohybridworker**, Azure Otomasyonu hesabınızdan farklı çalıştır sertifikasını dışarı aktarır ve aynı hesaba bağlı bir karma çalışan üzerinde yerel makine sertifika deposuna içeri aktarır. Bu adım tamamlandıktan sonra, çalışanın farklı çalıştır hesabını kullanarak Azure 'da başarıyla kimlik doğrulayabildiğini doğrular.

```azurepowershell-interactive
<#PSScriptInfo
.VERSION 1.0
.GUID 3a796b9a-623d-499d-86c8-c249f10a6986
.AUTHOR Azure Automation Team
.COMPANYNAME Microsoft
.COPYRIGHT
.TAGS Azure Automation
.LICENSEURI
.PROJECTURI
.ICONURI
.EXTERNALMODULEDEPENDENCIES
.REQUIREDSCRIPTS
.EXTERNALSCRIPTDEPENDENCIES
.RELEASENOTES
#>

<#
.SYNOPSIS
Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.

.DESCRIPTION
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.
Run this runbook in the hybrid worker where you want the certificate installed.
This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running in the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
AUTHOR: Azure Automation Team
LASTEDIT: 2016.10.13
#>

# Generate the password used for this certificate
Add-Type -AssemblyName System.Web -ErrorAction SilentlyContinue | Out-Null
$Password = [System.Web.Security.Membership]::GeneratePassword(25, 10)

# Stop on errors
$ErrorActionPreference = 'stop'

# Get the management certificate that will be used to make calls into Azure Service Management resources
$RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"

# location to store temporary certificate in the Automation service host
$CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"

# Save the certificate
$Cert = $RunAsCert.Export("pfx",$Password)
Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose

Write-Output ("Importing certificate into $env:computername local machine root store from " + $CertPath)
$SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

# Test that authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzureRmContext -SubscriptionId $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm Azure Resource Manager calls are working
Get-AzureRmAutomationAccount | Select-Object AutomationAccountName
```

> [!IMPORTANT]
> **Add-azurermaccount** artık **Connect-azurermaccount**için bir diğer addır. Kitaplık öğelerinizi ararken, **Connect-AzureRMAccount**' u görmüyorsanız **Add-azurermaccount**komutunu kullanabilir veya bir Otomasyon hesabınızda modüllerinizi güncelleştirebilirsiniz.

*Export-Runascercertificate Meditohybridworker* runbook 'unu `.ps1` uzantılı bilgisayarınıza kaydedin. Otomasyon hesabınıza aktarın ve Runbook 'u düzenleyerek `$Password` değişkeninin değerini kendi parolanızla değiştirin. Runbook 'u yayımlayın ve sonra çalıştırın. Çalıştırılacak karma çalışanı grubunu hedefleyin ve farklı çalıştır hesabını kullanarak runbook 'ların kimliğini doğrular. İş akışı, sertifikayı yerel makine deposuna aktarma girişimini raporlar ve birden çok satır ile izler. Bu davranış, aboneliğinizde kaç tane Otomasyon hesabı tanımladığınıza ve kimlik doğrulamasının başarılı olmasına bağlıdır.

## <a name="job-behavior"></a>İş davranışı

İşler, karma runbook çalışanları üzerinde, Azure korumalı alanlar üzerinde çalıştıklarında olduklarından biraz farklı şekilde işlenir. Bir temel fark, karma runbook çalışanları üzerinde iş süresi sınırının olmaması olabilir. Azure korumalı alanında çalıştırılan runbook 'lar, [dengeli bir paylaşımdan](automation-runbook-execution.md#fair-share)dolayı 3 saat ile sınırlıdır. Uzun süre çalışan bir runbook için olası yeniden başlatma açısından dayanıklı olduğundan emin olmak istersiniz. Örneğin, karma çalışanı barındıran makine yeniden başlatılır. Karma çalışanı konak makinesi yeniden başlatılırsa, çalışan tüm runbook işleri baştan başlatılır veya PowerShell Iş akışı runbook 'ları için son denetim noktasından başlatılır. Bir runbook işi 3 kereden fazla yeniden başlatıldıktan sonra askıya alınır.

## <a name="run-only-signed-runbooks"></a>Yalnızca imzalı runbook 'Ları Çalıştır

Karma runbook çalışanları, bazı yapılandırmalarla yalnızca imzalı runbook 'ları çalıştıracak şekilde yapılandırılabilir. Aşağıdaki bölümde, karma runbook çalışanlarınızın imzalı [Windows karma Runbook Worker](#windows-hybrid-runbook-worker) ve [Linux karma Runbook Worker](#linux-hybrid-runbook-worker) çalıştıracak şekilde nasıl ayarlanacağı açıklanır

> [!NOTE]
> Karma Runbook Worker 'ı yalnızca imzalı runbook 'ları çalıştıracak şekilde yapılandırdıktan sonra, imzalanmamış runbook 'lar çalışan üzerinde **yürütülemeyebilir.**

### <a name="windows-hybrid-runbook-worker"></a>Windows karma Runbook Worker

#### <a name="create-signing-certificate"></a>Imza sertifikası oluştur

Aşağıdaki örnek, runbook 'ları imzalamak için kullanılabilen, kendinden imzalı bir sertifika oluşturur. Örnek, sertifikayı oluşturur ve dışarı aktarır. Sertifika, karma runbook çalışanlarına daha sonra aktarılır. Parmak izi de döndürülür, bu değer daha sonra sertifikaya başvurmak için kullanılır.

```powershell
# Create a self-signed certificate that can be used for code signing
$SigningCert = New-SelfSignedCertificate -CertStoreLocation cert:\LocalMachine\my `
                                        -Subject "CN=contoso.com" `
                                        -KeyAlgorithm RSA `
                                        -KeyLength 2048 `
                                        -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
                                        -KeyExportPolicy Exportable `
                                        -KeyUsage DigitalSignature `
                                        -Type CodeSigningCert


# Export the certificate so that it can be imported to the hybrid workers
Export-Certificate -Cert $SigningCert -FilePath .\hybridworkersigningcertificate.cer

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Retrieve the thumbprint for later use
$SigningCert.Thumbprint
```

#### <a name="configure-the-hybrid-runbook-workers"></a>Karma runbook çalışanlarını yapılandırma

Oluşturulan sertifikayı bir gruptaki her karma runbook çalışanına kopyalayın. Sertifikayı içeri aktarmak ve karma çalışanı runbook 'larda imza doğrulamasını kullanacak şekilde yapılandırmak için aşağıdaki betiği çalıştırın.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

#### <a name="sign-your-runbooks-using-the-certificate"></a>Sertifikayı kullanarak runbook 'larınızı imzalama

Yalnızca imzalı runbook 'ları kullanacak şekilde yapılandırılmış karma runbook çalışanları sayesinde, karma Runbook Worker 'da kullanılacak runbook 'ları imzalamanız gerekir. Runbook 'larınızı imzalamak için aşağıdaki örnek PowerShell 'i kullanın.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Runbook imzalanmışsa Otomasyon hesabınıza aktarılmalıdır ve imza bloğu ile yayımlanmalıdır. Runbook 'ların nasıl içeri aktarılacağını öğrenmek için bkz. bir [dosyadaki runbook 'U Azure Otomasyonu 'Na aktarma](manage-runbooks.md#import-a-runbook).

### <a name="linux-hybrid-runbook-worker"></a>Linux karma Runbook Worker

Linux hibrit Runbook Worker 'daki runbook 'ları imzalamak için karma Runbook Worker 'ın makinede [GPG](https://gnupg.org/index.html) yürütülebilir dosyası olması gerekir.

#### <a name="create-a-gpg-keyring-and-keypair"></a>GPG kimlik anahtarlığı ve KeyPair oluşturma

Kimlik anahtarlığı ve KeyPair oluşturmak için karma Runbook Worker hesabını `nxautomation`kullanmanız gerekir.

`nxautomation` hesabı olarak oturum açmak için `sudo` kullanın.

```bash
sudo su – nxautomation
```

`nxautomation` hesabı 'nı kullanarak GPG KeyPair oluşturun.

```bash
sudo gpg --generate-key
```

GPG, KeyPair oluşturma adımlarında size kılavuzluk eder. Anahtarın üretilmesi için bir ad, e-posta adresi, sona erme saati, parola girmeniz ve makinede yeterli entropi beklemeniz gerekir.

GPG dizini sudo ile oluşturulduğundan, sahibini `nxautomation`olarak değiştirmeniz gerekir.

Sahibini değiştirmek için aşağıdaki komutu çalıştırın.

```bash
sudo chown -R nxautomation ~/.gnupg
```

#### <a name="make-the-keyring-available-the-hybrid-runbook-worker"></a>Kimlik anahtarlığı karma Runbook Worker 'ı kullanılabilir hale getirme

Kimlik anahtarlığı oluşturulduktan sonra karma Runbook Worker için kullanılabilir hale getirmeniz gerekir. `/var/opt/microsoft/omsagent/state/automationworker/diy/worker.conf` ayarlar dosyasını, bölümünde aşağıdaki örneği içerecek şekilde değiştirin `[worker-optional]`

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

#### <a name="verify-signature-validation-is-on"></a>İmza doğrulamasının açık olduğunu doğrulama

Makinede imza doğrulaması devre dışıysa, açmanız gerekir. İmza doğrulamasını etkinleştirmek için aşağıdaki komutu çalıştırın. `<LogAnalyticsworkspaceId>` çalışma alanı KIMLIĞINIZLE değiştirme.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

#### <a name="sign-a-runbook"></a>Runbook 'u imzala

İmza doğrulama yapılandırıldıktan sonra, bir runbook 'u imzalamak için aşağıdaki komutu kullanabilirsiniz:

```bash
gpg –-clear-sign <runbook name>
```

İmzalı runbook 'un adı `<runbook name>.asc`olacaktır.

İmzalı runbook artık Azure Otomasyonu 'na yüklenebilir ve normal bir runbook gibi yürütülenebilir.

## <a name="next-steps"></a>Sonraki adımlar

* Bir runbook 'u başlatmak için kullanılabilecek farklı yöntemler hakkında daha fazla bilgi edinmek için bkz. [Azure Otomasyonu 'Nda runbook başlatma](automation-starting-a-runbook.md).
* Metinsel düzenleyiciyi kullanarak Azure Otomasyonu 'nda PowerShell runbook 'larla çalışmanın farklı yollarını anlamak için bkz. [Azure Otomasyonu 'Nda runbook 'U düzenleme](automation-edit-textual-runbook.md)
* Runbook 'larınız başarıyla tamamlanmadığından, [runbook yürütme hatalarında](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails)sorun giderme kılavuzunu gözden geçirin.
* Dil başvurusu ve öğrenme modülleri de dahil olmak üzere PowerShell hakkında daha fazla bilgi için [PowerShell belgelerine](https://docs.microsoft.com/powershell/scripting/overview)bakın.
