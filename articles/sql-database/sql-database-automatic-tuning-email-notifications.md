---
title: Otomatik ayarlama e-posta bildirimleri nasıl yapılır Kılavuzu
description: Azure SQL veritabanı otomatik sorgu ayarlama için e-posta bildirimlerini etkinleştirin.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 06/03/2019
ms.openlocfilehash: b48c37a6e607d121416ebae4d74e58f39670b79a
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821918"
---
# <a name="email-notifications-for-automatic-tuning"></a>Otomatik ayarlama için e-posta bildirimleri

SQL veritabanı ayarlama önerileri, Azure SQL veritabanı [otomatik ayarlama](sql-database-automatic-tuning.md)tarafından oluşturulmuştur. Bu çözüm, dizin oluşturma, dizin silme ve sorgu yürütme planlarının iyileştirmesiyle ilgili her bir veritabanı için özelleştirilmiş ayarlama önerileri sağlayan SQL veritabanlarının iş yüklerini sürekli olarak izler ve çözümler.

SQL veritabanı otomatik ayarlama önerileri, [Azure portal](sql-database-advisor-portal.md) [REST API](https://docs.microsoft.com/rest/api/sql/databaserecommendedactions/listbydatabaseadvisor) çağrılarıyla ALıNAN veya [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) ve [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction) komutları kullanılarak görüntülenebilir. Bu makale, otomatik ayarlama önerilerini almak için bir PowerShell betiği kullanmayı temel alır.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager modülü Azure SQL veritabanı tarafından hala desteklenmektedir, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. Bu cmdlet 'ler için bkz. [Azurerd. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri önemli ölçüde aynıdır.

## <a name="automate-email-notifications-for-automatic-tuning-recommendations"></a>Otomatik ayarlama önerileri için e-posta bildirimlerini otomatikleştirin

Aşağıdaki çözüm otomatik ayarlama önerilerini içeren e-posta bildirimlerinin gönderilmesini otomatikleştirir. Açıklanan çözüm, [Azure Otomasyonu 'nu](https://docs.microsoft.com/azure/automation/automation-intro)kullanarak ayarlama önerilerini almak Için bir PowerShell betiğinin otomatikleştirilmesini ve [Microsoft Flow](https://flow.microsoft.com)kullanarak e-posta teslimi işinin planlanmasını içerir.

## <a name="create-azure-automation-account"></a>Azure Otomasyonu hesabı oluşturma

Azure Otomasyonu 'nu kullanmak için ilk adım bir Otomasyon hesabı oluşturmak ve PowerShell betiğinin yürütülmesi için kullanılacak Azure kaynaklarıyla yapılandırmaktır. Azure Otomasyonu ve özellikleri hakkında daha fazla bilgi edinmek için bkz. [Azure Otomasyonu ile çalışmaya](https://docs.microsoft.com/azure/automation/automation-offering-get-started)başlama.

Marketten Otomasyon uygulaması seçme ve yapılandırma yöntemi aracılığıyla Azure Otomasyonu hesabı oluşturmak için aşağıdaki adımları izleyin:

- Azure portal oturum açın
- Sol üst köşedeki " **+ kaynak oluştur**" seçeneğine tıklayın
- "**Otomasyon**" araması yapın (ENTER tuşuna basın)
- Arama sonuçlarında Otomasyon uygulamasına tıklayın

![Azure Otomasyonu ekleme](./media/sql-database-automatic-tuning-email-notifications/howto-email-01.png)

- "Otomasyon hesabı oluşturma" bölmesinin içindeyken "**Oluştur**" düğmesine tıklayın.
- Gerekli bilgileri doldur: Bu Otomasyon hesabı için bir ad girin, PowerShell betiği yürütmesi için kullanılacak Azure abonelik KIMLIĞINIZI ve Azure kaynaklarınızı seçin
- "**Azure farklı çalıştır hesabı oluştur**" seçeneği Için, PowerShell betiğinin Azure Otomasyonu yardımıyla çalıştığı hesap türünü yapılandırmak için **Evet** ' i seçin. Hesap türleri hakkında daha fazla bilgi edinmek için bkz. [Farklı Çalıştır hesabı](https://docs.microsoft.com/azure/automation/automation-create-runas-account)
- **Oluştur** 'a tıklayarak Otomasyon hesabı oluşturmayı sonuçlandırma

> [!TIP]
> Azure Otomasyonu hesap adınızı, abonelik KIMLIĞINIZI ve kaynaklarınızı (örneğin, kopyalama-Not defteri), otomasyon uygulamasını oluştururken tam olarak girildiği şekilde kaydedin. Bu bilgilere daha sonra ihtiyacınız vardır.
>

Aynı otomasyonu oluşturmak istediğiniz birkaç Azure aboneliğiniz varsa, bu işlemi diğer abonelikleriniz için yinelemeniz gerekir.

## <a name="update-azure-automation-modules"></a>Azure Otomasyonu modüllerini güncelleştirme

Otomatik ayarlama önerilerini almak için PowerShell betiği, Azure modül Sürüm 4 ve üzeri için [Get-AzResource](https://docs.microsoft.com/powershell/module/az.Resources/Get-azResource) ve [Get-AzSqlDatabaseRecommendedAction](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlDatabaseRecommendedAction) komutlarını kullanır.

- Azure modüllerinizin güncelleştirilmesi gerekiyorsa, bkz. [Azure Otomasyonu 'Nda az Module support](../automation/az-modules.md).

## <a name="create-azure-automation-runbook"></a>Azure Otomasyonu runbook 'U oluşturma

Sonraki adımda, Azure Otomasyonu 'nda ayarlama önerilerini almak için PowerShell betiğinin bulunduğu bir runbook oluşturulur.

Yeni bir Azure Otomasyonu runbook 'u oluşturmak için aşağıdaki adımları izleyin:

- Önceki adımda oluşturduğunuz Azure Otomasyonu hesabına erişin
- Otomasyon hesabı bölmesinde, PowerShell betiği ile yeni bir Azure Otomasyonu runbook 'u oluşturmak için sol taraftaki "**runbook 'lar**" menü öğesine tıklayın. Otomasyon Runbook 'ları oluşturma hakkında daha fazla bilgi için bkz. [yeni runbook oluşturma](../automation/manage-runbooks.md#create-a-runbook).
- Yeni bir runbook eklemek için " **+ runbook Ekle**" menü seçeneğine tıklayın ve ardından "**hızlı Oluştur – yeni runbook oluştur**" düğmesine tıklayın.
- Runbook bölmesinde, runbook 'unun adını yazın (Bu örneğin, "**Automatictunıngemaılautomation**" kullanılır), runbook 'un türünü **PowerShell** olarak seçin ve bu runbook 'un amacını betimleyen bir açıklama yazın.
- Yeni bir runbook oluşturmayı bitirmeden **Oluştur** düğmesine tıklayın

![Azure Otomasyonu runbook 'u ekleme](./media/sql-database-automatic-tuning-email-notifications/howto-email-03.png)

Oluşturulan runbook 'un içine bir PowerShell betiği yüklemek için şu adımları izleyin:

- "**PowerShell runbook 'Unu Düzenle**" bölmesinde, menü ağacında "**runbook 'lar**" ı seçin ve Runbook 'unun adını görene kadar görünümü genişletin (Bu örnekte "**Automatictunıngemailautomation**"). Bu runbook 'u seçin.
- "PowerShell runbook 'Unu Düzenle" öğesinin ilk satırında (1 numaralı numaradan itibaren), aşağıdaki PowerShell betik kodunu kopyalayın-yapıştırın. Bu PowerShell betiği, başlangıç olarak olduğu gibi sunulmaktadır. Komut dosyasını gereksinimlerinize uygun şekilde değiştirin.

Belirtilen PowerShell betiğinin üst bilgisinde `<SUBSCRIPTION_ID_WITH_DATABASES>` Azure abonelik KIMLIĞINIZ ile değiştirmeniz gerekir. Azure abonelik KIMLIĞINIZI alma hakkında bilgi edinmek için bkz. [Azure ABONELIK GUID 'Nizi alma](https://blogs.msdn.microsoft.com/mschray/20../../getting-your-azure-subscription-guid-new-portal/).

Birkaç abonelik söz konusu olduğunda, komut dosyasının üstbilgisindeki "$subscriptions" özelliğine virgülle ayrılmış olarak ekleyebilirsiniz.

```powershell
# PowerShell script to retrieve Azure SQL Database Automatic tuning recommendations.
#
# Provided “as-is” with no implied warranties or support.
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

                # Loop through all Automatic tuning recommendation types
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

![Azure Otomasyonu ile otomatik ayarlama önerilerini görüntüleme](./media/sql-database-automatic-tuning-email-notifications/howto-email-04.png)

PowerShell betiğini gereksinimlerinize göre özelleştirerek içeriği ayarlamayı doğrulayın.

Yukarıdaki adımlarla otomatik ayarlama önerilerini almaya yönelik PowerShell betiği Azure Automation 'a yüklenir. Bir sonraki adım, e-posta teslimi işinin otomatikleştirilmesi ve planlanmmdır.

## <a name="automate-the-email-jobs-with-microsoft-flow"></a>Microsoft Flow ile e-posta işlerini otomatikleştirin

Çözümü tamamlamaya yönelik son adım olarak, üç eylemden (iş) oluşan Microsoft Flow bir Otomasyon akışı oluşturun:

1. "**Azure Otomasyonu-oluşturma işi**" – Azure Otomasyonu runbook 'un içindeki otomatik ayarlama önerilerini almak üzere PowerShell betiğini yürütmek için kullanılır
2. "**Azure Otomasyonu-iş çıktısını alma**" – yürütülen PowerShell betiğindeki çıktıyı almak için kullanılır
3. "**Office 365 Outlook – e-posta gönder**" – e-posta göndermek için kullanılır. E-postalar, akışı oluşturan kişinin Office 365 hesabı kullanılarak gönderilir.

Microsoft Flow özellikleri hakkında daha fazla bilgi edinmek için bkz. [Microsoft Flow kullanmaya](https://docs.microsoft.com/flow/getting-started)başlama.

Bu adım için önkoşul, [Microsoft Flow](https://flow.microsoft.com) hesabına kaydolup oturum açmanızı sağlar. Çözüm içindeyken, **Yeni bir akış**ayarlamak için aşağıdaki adımları izleyin:

- "**Akışlarım**" menü öğesine erişin
- Akışlarım içinde sayfanın en üstündeki " **+ boş oluştur**" bağlantısını seçin
- Sayfanın alt kısmındaki "**yüzlerce bağlayıcı ve tetikleyicide arama**" bağlantısına tıklayın
- Arama alanında "**yinelenme**" yazın ve e-posta teslimi işinin çalışmasını zamanlamak için arama sonuçlarından "**zamanlama-yinelenme**" yı seçin.
- Sıklık alanındaki yineleme bölmesinde, bu akışın yürütülmesi için zamanlama sıklığını seçin (her dakika, saat, gün, hafta vb.).

Sonraki adım, yeni oluşturulan yinelenen akışa üç iş eklemektir (oluşturma, çıkış alma ve e-posta gönderme). Akışa gerekli işleri eklemeyi tamamlamak için şu adımları izleyin:

1. Ayarlama önerilerini almak için PowerShell betiğini yürütmek üzere eylem Oluştur

   - " **+ Yeni adım**", ardından "**Eylem Ekle**" öğesini seçerek yineleme akışı bölmesi içinde
   - Arama alanına "**Otomasyon**" yazın ve arama sonuçlarından "**Azure Otomasyonu – oluşturma işi**" ni seçin
   - İş oluştur bölmesinde, iş özelliklerini yapılandırın. Bu yapılandırma için, **daha önce** **Otomasyon hesabı bölmesine**kaydettiğiniz Azure abonelik kimliğiniz, kaynak grubunuz ve otomasyon hesabınızın ayrıntılarına ihtiyacınız olacaktır. Bu bölümde kullanılabilen seçenekler hakkında daha fazla bilgi edinmek için bkz. [Azure Otomasyonu-oluşturma işi](https://docs.microsoft.com/connectors/azureautomation/#create-job).
   - "**Akışı kaydet**" i tıklayarak bu eylemi oluşturmayı tamamlamayı doldurun

2. Yürütülen PowerShell betiğindeki çıktıyı almak için eylem oluşturma

   - " **+ Yeni adım**", ardından "**Eylem Ekle**" öğesini seçerek yineleme akışı bölmesi içinde
   - Arama konumunda "**Otomasyon**" yazın ve arama sonuçlarından "**Azure Otomasyonu – iş çıktısını al**" seçeneğini belirleyin. Bu bölümde kullanılabilen seçenekler hakkında daha fazla bilgi edinmek için bkz. [Azure Otomasyonu – iş çıktısını alma](https://docs.microsoft.com/connectors/azureautomation/#get-job-output).
   - Gerekli alanları doldur (önceki işi oluşturmaya benzer)-Azure abonelik KIMLIĞINIZI, kaynak grubunuzu ve otomasyon hesabınızı (Otomasyon hesabı bölmesine girildiği gibi) doldurun
   - "**Dinamik içerik**" menüsünün görünmesi Için "**iş kimliği**" alanının içini tıklatın. Bu menü içinden "**Iş kimliği**" seçeneğini belirleyin.
   - "**Akışı kaydet**" i tıklayarak bu eylemi oluşturmayı tamamlamayı doldurun

3. Office 365 tümleştirmesi kullanarak e-posta göndermek için eylem oluşturma

   - " **+ Yeni adım**", ardından "**Eylem Ekle**" öğesini seçerek yineleme akışı bölmesi içinde
   - Aranacak "**bir e-posta gönder**" yazın ve arama sonuçlarından "**Office 365 Outlook – e-posta gönder**" seçeneğini belirleyin
   - Bildirim e-postasını göndermeniz gereken e-posta adresini "**to**" alanına yazın
   - E-postanız konusunun "**Konu**" alanına yazın, örneğin "otomatik ayarlama önerileri e-posta bildirimi"
   - "**Dinamik içerik**" menüsünün görünmesi Için "**gövde**" alanının içini tıklatın. Bu menü içinden, "**iş çıktısını al**" bölümünün altında "**içerik**" i seçin
   - "**Akışı kaydet**" i tıklayarak bu eylemi oluşturmayı tamamlamayı doldurun

> [!TIP]
> Farklı alıcılara otomatik e-postalar göndermek için ayrı akışlar oluşturun. Bu ek akışlarda "Kime" alanındaki alıcının e-posta adresini ve "konu" alanındaki e-posta konu satırını değiştirin. Özelleştirilmiş PowerShell betikleri (örneğin, Azure abonelik KIMLIĞI gibi) ile Azure Otomasyonu 'nda yeni runbook 'lar oluşturma otomatik senaryolar için daha fazla özelleştirme imkanı sunar. Örneğin, otomatik ayarlama sırasında ayrı alıcıları e-postayla gönderme ayrı abonelikler için öneriler.
>

E-posta teslim işi iş akışını yapılandırmak için yukarıdaki sonucuna yönelik adımlar gereklidir. Oluşturulan üç eylemden oluşan tüm akış aşağıdaki görüntüde gösterilmiştir.

![Otomatik ayarlama e-posta bildirimleri akışını görüntüle](./media/sql-database-automatic-tuning-email-notifications/howto-email-05.png)

Akışı test etmek için akış bölmesinin içindeki sağ üst köşedeki "**Şimdi Çalıştır**" düğmesine tıklayın.

Gönderilen e-posta bildirimlerinin başarısını gösteren otomatikleştirilmiş işlerin çalıştırılmasına ilişkin istatistikler, Akış Analizi bölmesinden görünebilir.

![Otomatik ayarlama e-posta bildirimleri için çalışan akış](./media/sql-database-automatic-tuning-email-notifications/howto-email-06.png)

Flow Analytics, iş yürütmelerinin başarısını izlemek için yararlıdır ve sorun giderme için gerekliyse.  Sorun giderme durumunda, Azure Otomasyonu uygulaması aracılığıyla erişilebilen PowerShell betiği yürütme günlüğünü de incelemek isteyebilirsiniz.

Otomatik e-postanın son çıktısı, bu çözümü oluşturup çalıştırdıktan sonra alınan aşağıdaki e-postaya benzer:

![Otomatik ayarlama e-posta bildirimlerinin örnek e-posta çıkışı](./media/sql-database-automatic-tuning-email-notifications/howto-email-07.png)

PowerShell betiğini ayarlayarak, otomatik e-postanın çıktısını ve biçimlendirmesini gereksinimlerinize göre ayarlayabilirsiniz.

Özel senaryolarınıza bağlı olarak, belirli bir ayarlama olayına ve birden çok alıcıya, birden çok aboneliğe ve veritabanına göre e-posta bildirimleri oluşturmak için çözümü daha fazla özelleştirebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- Otomatik ayarlama 'nın veritabanı performansını artırmanıza nasıl yardımcı olduğu hakkında daha fazla bilgi için bkz. [Azure SQL veritabanı 'Nda otomatik ayarlama](sql-database-automatic-tuning.md).
- Azure SQL veritabanı 'nda iş yükünüzü yönetmek üzere otomatik ayarlamayı etkinleştirmek için bkz. [otomatik ayarlamayı etkinleştirme](sql-database-automatic-tuning-enable.md).
- Otomatik ayarlama önerilerini el ile gözden geçirmek ve uygulamak için bkz. [performans önerilerini bulma ve uygulama](sql-database-advisor-portal.md).
