---
title: Otomatik ayarlama e-posta bildirimleri nasıl yapılır Kılavuzu
description: Azure SQL veritabanı otomatik sorgu ayarlama için e-posta bildirimlerini etkinleştirin.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 06/03/2019
ms.openlocfilehash: a373a28a180b2a6c72f6a291b9d1437a2e88d9ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96500963"
---
# <a name="email-notifications-for-automatic-tuning"></a>Otomatik ayarlama için e-posta bildirimleri
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]


Azure SQL veritabanı ayarlama önerileri Azure SQL veritabanı [otomatik ayarlama](automatic-tuning-overview.md)tarafından oluşturulmuştur. Bu çözüm, dizin oluşturma, dizin silme ve sorgu yürütme planlarının iyileştirmesiyle ilgili her bir veritabanı için özelleştirilmiş ayarlama önerileri sağlayan veritabanlarının iş yüklerini sürekli olarak izler ve çözümler.

Azure SQL veritabanı otomatik ayarlama önerileri, [Azure portal](database-advisor-find-recommendations-portal.md) [REST API](/rest/api/sql/databaserecommendedactions/listbydatabaseadvisor) çağrılarıyla veya [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) ve [PowerShell](/powershell/module/az.sql/get-azsqldatabaserecommendedaction) komutları kullanılarak görüntülenebilir. Bu makale, otomatik ayarlama önerilerini almak için bir PowerShell betiği kullanmayı temel alır.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager modülü Azure SQL veritabanı tarafından hala desteklenmektedir, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. Bu cmdlet 'ler için bkz. [Azurerd. SQL](/powershell/module/AzureRM.Sql/). Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri önemli ölçüde aynıdır.

## <a name="automate-email-notifications-for-automatic-tuning-recommendations"></a>Otomatik ayarlama önerileri için e-posta bildirimlerini otomatikleştirin

Aşağıdaki çözüm otomatik ayarlama önerilerini içeren e-posta bildirimlerinin gönderilmesini otomatikleştirir. Açıklanan çözüm, [Azure Otomasyonu 'nu](../../automation/automation-intro.md)kullanarak ayarlama önerilerini almak Için bir PowerShell betiğinin otomatikleştirilmesini ve [Microsoft Flow](https://flow.microsoft.com)kullanarak e-posta teslimi işinin planlanmasını içerir.

## <a name="create-azure-automation-account"></a>Azure Otomasyonu hesabı oluşturma

Azure Otomasyonu 'nu kullanmak için ilk adım bir Otomasyon hesabı oluşturmak ve PowerShell betiğinin yürütülmesi için kullanılacak Azure kaynaklarıyla yapılandırmaktır. Azure Otomasyonu ve özellikleri hakkında daha fazla bilgi edinmek için bkz. [Azure Otomasyonu ile çalışmaya](../../automation/index.yml)başlama.

Azure Marketi 'nden bir Otomasyon uygulaması seçme ve yapılandırma yöntemi aracılığıyla bir Azure Otomasyonu hesabı oluşturmak için aşağıdaki adımları izleyin:

1. Azure portalında oturum açın.
1. Sol üst köşedeki "**+ kaynak oluştur**" seçeneğine tıklayın.
1. "**Otomasyon**" araması yapın (ENTER tuşuna basın).
1. Arama sonuçlarında Otomasyon uygulamasına tıklayın.

    ![Azure Otomasyonu ekleme](./media/automatic-tuning-email-notifications-configure/howto-email-01.png)

1. "Otomasyon hesabı oluşturma" bölmesinin içindeyken "**Oluştur**" düğmesine tıklayın.
1. Gerekli bilgileri doldur: Bu Otomasyon hesabı için bir ad girin, PowerShell betiği yürütmesi için kullanılacak Azure abonelik KIMLIĞINIZI ve Azure kaynaklarınızı seçin.
1. "**Azure farklı çalıştır hesabı oluştur**" seçeneği Için, PowerShell betiğinin Azure Otomasyonu yardımıyla çalıştığı hesap türünü yapılandırmak için **Evet** ' i seçin. Hesap türleri hakkında daha fazla bilgi için bkz. [Farklı Çalıştır hesabı](../../automation/manage-runas-account.md).
1. **Oluştur**'a tıklayarak Otomasyon hesabı oluşturmayı sonuçlandırma.

> [!TIP]
> Azure Otomasyonu hesap adınızı, abonelik KIMLIĞINIZI ve kaynaklarınızı (örneğin, kopyalama-Not defteri), otomasyon uygulamasını oluştururken tam olarak girildiği şekilde kaydedin. Bu bilgilere daha sonra ihtiyacınız vardır.

Aynı otomasyonu oluşturmak istediğiniz birkaç Azure aboneliğiniz varsa, bu işlemi diğer abonelikleriniz için yinelemeniz gerekir.

## <a name="update-azure-automation-modules"></a>Azure Otomasyonu modüllerini güncelleştirme

Otomatik ayarlama önerilerini almak için PowerShell betiği, Azure modül Sürüm 4 ve üzeri için [Get-AzResource](/powershell/module/az.Resources/Get-azResource) ve [Get-AzSqlDatabaseRecommendedAction](/powershell/module/az.Sql/Get-azSqlDatabaseRecommendedAction) komutlarını kullanır.

- Azure modüllerinizin güncelleştirilmesi gerekiyorsa, bkz. [Azure Otomasyonu 'Nda az Module support](../../automation/shared-resources/modules.md).

## <a name="create-azure-automation-runbook"></a>Azure Otomasyonu runbook 'u oluşturma

Sonraki adımda, Azure Otomasyonu 'nda ayarlama önerilerini almak için PowerShell betiğinin bulunduğu bir runbook oluşturulur.

Yeni bir Azure Otomasyonu runbook 'u oluşturmak için aşağıdaki adımları izleyin:

1. Önceki adımda oluşturduğunuz Azure Otomasyonu hesabına erişin.
1. Otomasyon hesabı bölmesinde, PowerShell betiği ile yeni bir Azure Otomasyonu runbook 'u oluşturmak için sol taraftaki "**runbook 'lar**" menü öğesine tıklayın. Otomasyon Runbook 'ları oluşturma hakkında daha fazla bilgi için bkz. [yeni runbook oluşturma](../../automation/manage-runbooks.md#create-a-runbook).
1. Yeni bir runbook eklemek için "**+ runbook Ekle**" menü seçeneğine tıklayın ve ardından "**hızlı Oluştur – yeni runbook oluştur**" düğmesine tıklayın.
1. Runbook bölmesinde, runbook 'unun adını yazın (Bu örneğin, "**Automatictunıngemaılautomation**" kullanılır), runbook 'un türünü **PowerShell** olarak seçin ve bu runbook 'un amacını betimleyen bir açıklama yazın.
1. Yeni bir runbook oluşturmayı bitirmeden **Oluştur** düğmesine tıklayın.

    ![Azure Otomasyonu runbook 'u ekleme](./media/automatic-tuning-email-notifications-configure/howto-email-03.png)

Oluşturulan runbook 'un içine bir PowerShell betiği yüklemek için şu adımları izleyin:

1. "**PowerShell runbook 'Unu Düzenle**" bölmesinde, menü ağacında "**runbook 'lar**" ı seçin ve Runbook 'unun adını görene kadar görünümü genişletin (Bu örnekte "**Automatictunıngemailautomation**"). Bu runbook 'u seçin.
1. "PowerShell runbook 'Unu Düzenle" öğesinin ilk satırında (1 numaralı numaradan itibaren), aşağıdaki PowerShell betik kodunu kopyalayın-yapıştırın. Bu PowerShell betiği, başlangıç olarak olduğu gibi sunulmaktadır. Komut dosyasını gereksinimlerinize uygun şekilde değiştirin.

Belirtilen PowerShell betiğinin üst bilgisinde `<SUBSCRIPTION_ID_WITH_DATABASES>` Azure ABONELIK Kimliğiniz ile değiştirmeniz gerekir. Azure abonelik KIMLIĞINIZI alma hakkında bilgi edinmek için bkz. [Azure ABONELIK GUID 'Nizi alma](/archive/blogs/mschray/getting-your-azure-subscription-guid-new-portal).

Birkaç abonelik söz konusu olduğunda, komut dosyasının üstbilgisindeki "$subscriptions" özelliğine virgülle ayrılmış olarak ekleyebilirsiniz.

```powershell
# PowerShell script to retrieve Azure SQL Database automatic tuning recommendations.
#
# Provided "as-is" with no implied warranties or support.
# The script is released to the public domain.
#
# Replace <SUBSCRIPTION_ID_WITH_DATABASES> in the header with your Azure subscription ID.
#
# Microsoft Azure SQL Database team, 2018-01-22.

# Set subscriptions : IMPORTANT – REPLACE <SUBSCRIPTION_ID_WITH_DATABASES> WITH YOUR SUBSCRIPTION ID
$subscriptions = ("<SUBSCRIPTION_ID_WITH_DATABASES>", "<SECOND_SUBSCRIPTION_ID_WITH_DATABASES>", "<THIRD_SUBSCRIPTION_ID_WITH_DATABASES>")

# Get credentials
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Define the resource types
$resourceTypes = ("Microsoft.Sql/servers/databases")
$advisors = ("CreateIndex", "DropIndex");
$results = @()

# Loop through all subscriptions
foreach($subscriptionId in $subscriptions) {
    Select-AzSubscription -SubscriptionId $subscriptionId
    $rgs = Get-AzResourceGroup

    # Loop through all resource groups
    foreach($rg in $rgs) {
        $rgname = $rg.ResourceGroupName;

        # Loop through all resource types
        foreach($resourceType in $resourceTypes) {
            $resources = Get-AzResource -ResourceGroupName $rgname -ResourceType $resourceType

            # Loop through all databases
            # Extract resource groups, servers and databases
            foreach ($resource in $resources) {
                $resourceId = $resource.ResourceId
                if ($resourceId -match ".*RESOURCEGROUPS/(?<content>.*)/PROVIDERS.*") {
                    $ResourceGroupName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*SERVERS/(?<content>.*)/DATABASES.*") {
                    $ServerName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*/DATABASES/(?<content>.*)") {
                    $DatabaseName = $matches['content']
                } else {
                    continue
                }

                # Skip if master
                if ($DatabaseName -eq "master") {
                    continue
                }

                # Loop through all automatic tuning recommendation types
                foreach ($advisor in $advisors) {
                    $recs = Get-AzSqlDatabaseRecommendedAction -ResourceGroupName $ResourceGroupName -ServerName $ServerName  -DatabaseName $DatabaseName -AdvisorName $advisor
                    foreach ($r in $recs) {
                        if ($r.State.CurrentValue -eq "Active") {
                            $object = New-Object -TypeName PSObject
                            $object | Add-Member -Name 'SubscriptionId' -MemberType Noteproperty -Value $subscriptionId
                            $object | Add-Member -Name 'ResourceGroupName' -MemberType Noteproperty -Value $r.ResourceGroupName
                            $object | Add-Member -Name 'ServerName' -MemberType Noteproperty -Value $r.ServerName
                            $object | Add-Member -Name 'DatabaseName' -MemberType Noteproperty -Value $r.DatabaseName
                            $object | Add-Member -Name 'Script' -MemberType Noteproperty -Value $r.ImplementationDetails.Script
                            $results += $object
                        }
                    }
                }
            }
        }
    }
}

# Format and output results for the email
$table = $results | Format-List
Write-Output $table
```

Betiği kaydetmek için sağ üst köşedeki "**Kaydet**" düğmesine tıklayın. Betikten memnun olduğunuzda, bu runbook 'u yayımlamak için "**Yayımla**" düğmesine tıklayın.

Ana runbook bölmesinde, "**Başlat**" düğmesine tıklayarak betiği **Test** edebilirsiniz. Yürütülen betiğin sonuçlarını görüntülemek için "**Çıkış**" düğmesine tıklayın. Bu çıktı, e-postanız içeriğine gidiyor. Komut dosyasındaki örnek çıktı aşağıdaki ekran görüntüsünde görülebilir.

![Azure Otomasyonu ile otomatik ayarlama önerilerini görüntüleme](./media/automatic-tuning-email-notifications-configure/howto-email-04.png)

PowerShell betiğini gereksinimlerinize göre özelleştirerek içeriği ayarlamayı doğrulayın.

Yukarıdaki adımlarla otomatik ayarlama önerilerini almaya yönelik PowerShell betiği Azure Automation 'a yüklenir. Bir sonraki adım, e-posta teslimi işinin otomatikleştirilmesi ve planlanmmdır.

## <a name="automate-the-email-jobs-with-microsoft-flow"></a>Microsoft Flow ile e-posta işlerini otomatikleştirin

Çözümü tamamlamaya yönelik son adım olarak, üç eylemden (iş) oluşan Microsoft Flow bir Otomasyon akışı oluşturun:

- "**Azure Otomasyonu-oluşturma işi**" – Azure Otomasyonu runbook 'un içindeki otomatik ayarlama önerilerini almak üzere PowerShell betiğini yürütmek için kullanılır.
- "**Azure Otomasyonu-iş çıktısını al**" – yürütülen PowerShell betiğindeki çıktıyı almak için kullanılır.
- "**Office 365 Outlook – e-posta gönder**" – e-posta göndermek için kullanılır. E-postalar, akışı oluşturan kişinin iş veya okul hesabı kullanılarak gönderilir.

Microsoft Flow özellikleri hakkında daha fazla bilgi edinmek için bkz. [Microsoft Flow kullanmaya](/flow/getting-started)başlama.

Bu adım için önkoşul, bir [Microsoft Flow](https://flow.microsoft.com) hesabına kaydolup oturum açmasını sağlar. Çözüm içindeyken, **Yeni bir akış** ayarlamak için aşağıdaki adımları izleyin:

1. "**Akışlarım**" menü öğesine erişin.
1. Akışlarım içinde sayfanın en üstündeki "**+ boş oluştur**" bağlantısını seçin.
1. Sayfanın alt kısmındaki "**yüzlerce bağlayıcı ve tetikleyici ara**" bağlantısına tıklayın.
1. Arama alanında "**yinelenme**" yazın ve e-posta teslimi işinin çalışmasını zamanlamak için arama sonuçlarından "**zamanlama-yinelenme**" yı seçin.
1. Sıklık alanındaki yineleme bölmesinde, bu akışın yürütülmesi için zamanlama sıklığını seçin (her dakika, saat, gün, hafta vb.).

Sonraki adım, yeni oluşturulan yinelenen akışa üç iş eklemektir (oluşturma, çıkış alma ve e-posta gönderme). Akışa gerekli işleri eklemeyi tamamlamak için şu adımları izleyin:

1. Ayarlama önerilerini almak için PowerShell betiğini yürütmek üzere eylem Oluştur

   - "**+ Yeni adım**", ardından "**Eylem Ekle**" öğesini seçerek yinelenme akışı bölmesini seçin.
   - Arama alanına "**Otomasyon**" yazın ve arama sonuçlarından "**Azure Otomasyonu – oluşturma işi**" ni seçin.
   - İş oluştur bölmesinde, iş özelliklerini yapılandırın. Bu yapılandırma için, **daha önce** **Otomasyon hesabı bölmesine** kaydettiğiniz Azure abonelik kimliğiniz, kaynak grubunuz ve otomasyon hesabınızın ayrıntılarına ihtiyacınız olacaktır. Bu bölümde kullanılabilen seçenekler hakkında daha fazla bilgi edinmek için bkz. [Azure Otomasyonu-oluşturma işi](/connectors/azureautomation/#create-job).
   - "**Akışı kaydet**" i tıklayarak bu eylemi oluşturmayı tamamen doldurun.

2. Yürütülen PowerShell betiğinden çıktıyı almak için bir eylem oluşturun

   - "**+ Yeni adım**", ardından "**Eylem Ekle**" öğesini seçerek yineleme akışı bölmesi içinde
   - Arama alanına "**Otomasyon**" yazın ve arama sonuçlarından "**Azure Otomasyonu – iş çıktısını al**" seçeneğini belirleyin. Bu bölümde kullanılabilen seçenekler hakkında daha fazla bilgi edinmek için bkz. [Azure Otomasyonu – iş çıktısını alma](/connectors/azureautomation/#get-job-output).
   - Gerekli alanları doldur (önceki işi oluşturmaya benzer)-Azure abonelik KIMLIĞINIZI, kaynak grubunuzu ve otomasyon hesabınızı (Otomasyon hesabı bölmesine girildiği gibi) doldurun.
   - "**Dinamik içerik**" menüsünün görünmesi Için "**iş kimliği**" alanının içini tıklatın. Bu menü içinden "**Iş kimliği**" seçeneğini belirleyin.
   - "**Akışı kaydet**" i tıklayarak bu eylemi oluşturmayı tamamen doldurun.

3. Office 365 tümleştirmesi kullanarak e-posta göndermek için bir eylem oluşturma

   - "**+ Yeni adım**", ardından "**Eylem Ekle**" öğesini seçerek yinelenme akışı bölmesini seçin.
   - Arama alanına "**e-posta gönder**" yazın ve arama sonuçlarından "**Office 365 Outlook – e-posta gönder**" seçeneğini belirleyin.
   - Bildirim e-postasını göndermeniz gereken e-posta adresini "**to**" alanına yazın.
   - E-postanız konusunun "**Konu**" alanına yazın, örneğin "otomatik ayarlama önerileri e-posta bildirimi".
   - "**Dinamik içerik**" menüsünün görünmesi Için "**gövde**" alanının içini tıklatın. Bu menü içinden, "**iş çıktısını al**" bölümünün altında "**içerik**" i seçin.
   - "**Akışı kaydet**" i tıklayarak bu eylemi oluşturmayı tamamen doldurun.

> [!TIP]
> Farklı alıcılara otomatik e-postalar göndermek için ayrı akışlar oluşturun. Bu ek akışlarda "Kime" alanındaki alıcının e-posta adresini ve "konu" alanındaki e-posta konu satırını değiştirin. Özelleştirilmiş PowerShell betikleri (örneğin, Azure abonelik KIMLIĞI gibi) ile Azure Otomasyonu 'nda yeni runbook 'lar oluşturmak, örneğin ayrı abonelikler için otomatik ayarlama önerilerinde ayrı alıcılar göndermek gibi otomatik senaryolar için daha fazla özelleştirme imkanı sunar.

E-posta teslim işi iş akışını yapılandırmak için yukarıdaki sonucuna yönelik adımlar gereklidir. Oluşturulan üç eylemden oluşan tüm akış aşağıdaki görüntüde gösterilmiştir.

![Otomatik ayarlama e-posta bildirimleri akışını görüntüle](./media/automatic-tuning-email-notifications-configure/howto-email-05.png)

Akışı test etmek için akış bölmesinin içindeki sağ üst köşedeki "**Şimdi Çalıştır**" düğmesine tıklayın.

Gönderilen e-posta bildirimlerinin başarısını gösteren otomatikleştirilmiş işlerin çalıştırılmasına ilişkin istatistikler, Akış Analizi bölmesinden görünebilir.

![Otomatik ayarlama e-posta bildirimleri için çalışan akış](./media/automatic-tuning-email-notifications-configure/howto-email-06.png)

Flow Analytics bölmesi, iş yürütmelerinin başarısını izlemek ve sorun giderme için gerekliyse yararlıdır.  Sorun giderme durumunda, Azure Otomasyonu uygulaması aracılığıyla erişilebilen PowerShell betiği yürütme günlüğünü de incelemek isteyebilirsiniz.

Otomatik e-postanın son çıktısı, bu çözümü oluşturup çalıştırdıktan sonra alınan aşağıdaki e-postaya benzer:

![Otomatik ayarlama e-posta bildirimlerinin örnek e-posta çıkışı](./media/automatic-tuning-email-notifications-configure/howto-email-07.png)

PowerShell betiğini ayarlayarak, otomatik e-postanın çıktısını ve biçimlendirmesini gereksinimlerinize göre ayarlayabilirsiniz.

Özel senaryolarınıza bağlı olarak, belirli bir ayarlama olayına ve birden çok alıcıya, birden çok aboneliğe ve veritabanına göre e-posta bildirimleri oluşturmak için çözümü daha fazla özelleştirebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- Otomatik ayarlama 'nın veritabanı performansını artırmanıza nasıl yardımcı olduğu hakkında daha fazla bilgi için bkz. [Azure SQL veritabanı 'Nda otomatik ayarlama](automatic-tuning-overview.md).
- Azure SQL veritabanı 'nda iş yükünüzü yönetmek üzere otomatik ayarlamayı etkinleştirmek için bkz. [otomatik ayarlamayı etkinleştirme](automatic-tuning-enable.md).
- Otomatik ayarlama önerilerini el ile gözden geçirmek ve uygulamak için bkz. [performans önerilerini bulma ve uygulama](database-advisor-find-recommendations-portal.md).