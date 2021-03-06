---
title: Azure Otomasyonu ile Azure Analysis Services modellerini yenileme | Microsoft Docs
description: Bu makalede, Azure Otomasyonu kullanılarak Azure Analysis Services için model yenilemelerinin nasıl değiştirileceği açıklanır.
author: chrislound
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: chlound
ms.openlocfilehash: 7c801511b6f24cf5ef04d55bb195e3a4c62d7b6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96491256"
---
# <a name="refresh-with-azure-automation"></a>Azure Otomasyonu ile yenileme

Azure Otomasyonu ve PowerShell runbook 'Larını kullanarak Azure Analysis tablolu modellerinizde otomatik veri yenileme işlemleri gerçekleştirebilirsiniz.  

Bu makaledeki örnek, [SqlServer PowerShell modülünü](/powershell/module/sqlserver/?view=sqlserver-ps&preserve-view=true)kullanır. Bir modelin yenilenmesini gösteren örnek bir PowerShell runbook 'u, bu makalenin ilerleyen kısımlarında verilmiştir.  

## <a name="authentication"></a>Kimlik Doğrulaması

Tüm çağrıların kimliği geçerli bir Azure Active Directory (OAuth 2) belirteciyle doğrulanmalıdır.  Bu makaledeki örnek, Azure Analysis Services kimlik doğrulaması yapmak için bir hizmet sorumlusu (SPN) kullanır. Daha fazla bilgi için bkz. [Azure Portal kullanarak hizmet sorumlusu oluşturma](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="prerequisites"></a>Önkoşullar

> [!IMPORTANT]
> Aşağıdaki örnek, Azure Analysis Services güvenlik duvarının devre dışı bırakıldığını varsayar. Bir Güvenlik Duvarı etkinse, istek başlatıcısının genel IP adresi bir güvenlik duvarı kuralına dahil olmalıdır.

### <a name="install-sqlserver-modules-from-powershell-gallery"></a>PowerShell galerisinden SqlServer modüllerini yükler.

1. Azure Otomasyonu hesabınızda **modüller**' e ve ardından **Galeriye gözatıp**' ye tıklayın.

2. Ara çubuğunda, **SqlServer** için arama yapın.

    ![Modül ara](./media/analysis-services-refresh-azure-automation/1.png)

3. SqlServer ' ı seçin ve **Içeri aktar**' a tıklayın.
 
    ![Modül içeri aktar](./media/analysis-services-refresh-azure-automation/2.png)

4. **Tamam**'a tıklayın.
 
### <a name="create-a-service-principal-spn"></a>Hizmet sorumlusu oluşturma (SPN)

Hizmet sorumlusu oluşturma hakkında bilgi edinmek için, bkz. [Azure Portal kullanarak hizmet sorumlusu oluşturma](../active-directory/develop/howto-create-service-principal-portal.md).

### <a name="configure-permissions-in-azure-analysis-services"></a>Azure Analysis Services izinlerini yapılandırma
 
Oluşturduğunuz hizmet sorumlusu sunucuda Sunucu Yöneticisi izinlerine sahip olmalıdır. Daha fazla bilgi için bkz. [Sunucu Yöneticisi rolüne hizmet sorumlusu ekleme](analysis-services-addservprinc-admins.md).

## <a name="design-the-azure-automation-runbook"></a>Azure Otomasyonu runbook 'Unu tasarlama

1. Otomasyon hesabında, hizmet sorumlusunu güvenli bir şekilde depolamak için kullanılacak bir **kimlik bilgileri** kaynağı oluşturun.

    !["Kimlik bilgisi ekle" eylemi seçiliyken "kimlik bilgileri" sayfasını gösteren ekran görüntüsü.](./media/analysis-services-refresh-azure-automation/6.png)

2. Kimlik bilgisinin ayrıntılarını girin. **Kullanıcı adı** alanına hizmet sorumlusu uygulama kimliği (AppID) girin ve ardından **parola** alanına hizmet sorumlusu parolasını girin.

    ![Kimlik bilgisi oluştur](./media/analysis-services-refresh-azure-automation/7.png)

3. Otomasyon Runbook 'Unu içeri aktarın.

    !["Runbook 'u Içeri aktar" eylemi seçiliyken "runbook 'Lar" sayfasını gösteren ekran görüntüsü.](./media/analysis-services-refresh-azure-automation/8.png)

4. [Refresh-Model.ps1](#sample-powershell-runbook) dosyasına gidip bir **ad** ve **Açıklama** girin ve ardından **Oluştur**' a tıklayın.

    > [!NOTE]
    > Refresh-Model.ps1 adlı bir dosya oluşturmak ve Runbook 'a aktarmak için yerel makineye kaydetmek üzere bu belgenin altındaki [örnek PowerShell runbook](#sample-powershell-runbook) ' dan betiği kullanın.

    ![Runbook 'U içeri aktar](./media/analysis-services-refresh-azure-automation/9.png)

5. Runbook oluşturulduğunda, otomatik olarak düzenleme moduna geçer.  **Yayımla**’yı seçin.

    ![Runbook 'U Yayımla](./media/analysis-services-refresh-azure-automation/10.png)

    > [!NOTE]
    > Daha önce oluşturulan kimlik bilgisi kaynağı, **Get-AutomationPSCredential** komutu kullanılarak runbook tarafından alınır.  Bu komut daha sonra Azure Analysis Services kimlik doğrulamasını gerçekleştirmek için **Invoke-ProcessASADatabase** PowerShell komutuna geçirilir.

6. **Başlat**' a tıklayarak runbook 'u test edin.

    !["Başlat" eylemi seçiliyken "genel bakış" sayfasını gösteren ekran görüntüsü.](./media/analysis-services-refresh-azure-automation/11.png)

7. **DatabaseName**, **Analysisserver** ve **RefreshType** parametrelerini doldurun ve ardından **Tamam**' a tıklayın. Runbook el ile çalıştırıldığında **Webkancaverisi** parametresi gerekli değildir.

    ![Runbook 'U başlatma](./media/analysis-services-refresh-azure-automation/12.png)

Runbook başarıyla yürütülürse, aşağıdakine benzer bir çıktı alırsınız:

![Başarılı çalıştırma](./media/analysis-services-refresh-azure-automation/13.png)

## <a name="use-a-self-contained-azure-automation-runbook"></a>Kendi içinde Azure Otomasyonu runbook 'U kullanma

Runbook, Azure Analysis Services modeli yenilemeyi zamanlanan bir şekilde tetikleyebilir.

Bu, aşağıdaki gibi yapılandırılabilir:

1. Otomasyon Runbook 'unda **zamanlamalar**' a ve ardından **bir zamanlama ekleyin**.
 
    ![Zamanlama Oluştur](./media/analysis-services-refresh-azure-automation/14.png)

2. **Zamanla**  >  **Yeni bir zamanlama oluştur ' a** tıklayın ve ardından ayrıntıları girin.

    ![Zamanlamayı Yapılandır](./media/analysis-services-refresh-azure-automation/15.png)

3. **Oluştur**’a tıklayın.

4. Zamanlamanın parametrelerini girin. Bunlar runbook 'un her tetiklenilişinde kullanılacaktır. **Web kancası verileri** parametresi, bir zamanlama aracılığıyla çalışırken boş bırakılmalıdır.

    ![Parametreleri yapılandırma](./media/analysis-services-refresh-azure-automation/16.png)

5. **Tamam**'a tıklayın.

## <a name="consume-with-data-factory"></a>Data Factory kullanma

Azure Data Factory kullanarak runbook 'u kullanmak için önce runbook için bir **Web kancası** oluşturun. Web **kancası** , bir Azure Data Factory Web etkinliği aracılığıyla ÇAĞRıLABILECEK bir URL sağlar.

> [!IMPORTANT]
> **Web kancası** oluşturmak için Runbook 'un durumu **yayımlanmalıdır**.

1. Otomasyon Runbook 'unda **Web kancaları**' na ve ardından **Web kancası Ekle**' ye tıklayın.

   ![Web kancası Ekle](./media/analysis-services-refresh-azure-automation/17.png)

2. Web kancasına bir ad ve süre sonu verin.  Ad yalnızca Otomasyon Runbook 'Unun içindeki Web kancasını tanımlar, URL 'nin bir parçasını oluşturmaz.

   >[!CAUTION]
   >Bir kez daha geri ulaştığınızdan, Sihirbazı kapatmadan önce URL 'YI kopyalamadiğinizden emin olun.
    
   ![Web kancasını yapılandırma](./media/analysis-services-refresh-azure-automation/18.png)

    Web kancası parametreleri boş kalabilir.  Azure Data Factory Web etkinliğini yapılandırırken parametreler, Web çağrısının gövdesine geçirilebilir.

3. Data Factory, bir **Web etkinliği** yapılandırın

### <a name="example"></a>Örnek

   ![Örnek Web etkinliği](./media/analysis-services-refresh-azure-automation/19.png)

**URL** , Web kancasından oluşturulan URL 'dir.

**Gövde** , aşağıdaki özellikleri içermesi gereken bir JSON belgesidir:


|Özellik  |Değer  |
|---------|---------|
|**AnalysisServicesDatabase**     |Azure Analysis Services veritabanının adı <br/> Örnek: AdventureWorksDB         |
|**AnalysisServicesServer**     |Azure Analysis Services sunucu adı. <br/> Örnek: https: \/ /westus.asazure.Windows.net/Servers/MyServer/models/AdventureWorks/         |
|**DatabaseRefreshType**     |Gerçekleştirilecek yenileme türü. <br/> Örnek: tam         |

Örnek JSON gövdesi:

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

Bu parametreler runbook PowerShell betiği içinde tanımlanmıştır.  Web etkinliği yürütüldüğünde, geçirilen JSON yükü Web KANCASı VERILERI olur.

Bu, seri durumdan çıkarılan ve PowerShell parametresi olarak depolanarak Invoke-ProcesASDatabase PowerShell komutu tarafından kullanılır.

![Seri durumdan çıkarılan Web kancası](./media/analysis-services-refresh-azure-automation/20.png)

## <a name="use-a-hybrid-worker-with-azure-analysis-services"></a>Karma çalışanı Azure Analysis Services ile kullanma

Statik bir genel IP adresine sahip bir Azure sanal makinesi, Azure Otomasyon Karma Çalışanı olarak kullanılabilir.  Bu genel IP adresi daha sonra Azure Analysis Services güvenlik duvarına eklenebilir.

> [!IMPORTANT]
> Sanal makine genel IP adresinin statik olarak yapılandırıldığından emin olun.
>
>Azure Otomasyonu karma çalışanları yapılandırma hakkında daha fazla bilgi için bkz. [karma Runbook Worker yüklemesi](../automation/automation-hybrid-runbook-worker.md#hybrid-runbook-worker-installation).

Karma çalışan yapılandırıldıktan sonra [Data Factory](#consume-with-data-factory)kullanma bölümünde açıklandığı gibi bir Web kancası oluşturun.  Buradaki tek fark,   >  Web kancasını yapılandırırken **karma çalışanı Çalıştır seçeneğini seçmedir** .

Karma çalışanı kullanan örnek Web kancası:

![Örnek karma çalışanı Web kancası](./media/analysis-services-refresh-azure-automation/21.png)

## <a name="sample-powershell-runbook"></a>Örnek PowerShell runbook 'U

Aşağıdaki kod parçacığı, bir PowerShell runbook 'U kullanarak Azure Analysis Services modeli yenilemenin nasıl gerçekleştiribir örneğidir.

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData,

    [Parameter (Mandatory = $false)]
    [String] $DatabaseName,
    [Parameter (Mandatory = $false)]
    [String] $AnalysisServer,
    [Parameter (Mandatory = $false)]
    [String] $RefreshType
)

$_Credential = Get-AutomationPSCredential -Name "ServicePrincipal"

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData)
{ 
    # Retrieve AAS details from Webhook request body
    $atmParameters = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    Write-Output "CredentialName: $($atmParameters.CredentialName)"
    Write-Output "AnalysisServicesDatabaseName: $($atmParameters.AnalysisServicesDatabaseName)"
    Write-Output "AnalysisServicesServer: $($atmParameters.AnalysisServicesServer)"
    Write-Output "DatabaseRefreshType: $($atmParameters.DatabaseRefreshType)"
    
    $_databaseName = $atmParameters.AnalysisServicesDatabaseName
    $_analysisServer = $atmParameters.AnalysisServicesServer
    $_refreshType = $atmParameters.DatabaseRefreshType
 
    Invoke-ProcessASDatabase -DatabaseName $_databaseName -RefreshType $_refreshType -Server $_analysisServer -ServicePrincipal -Credential $_credential
}
else 
{
    Invoke-ProcessASDatabase -DatabaseName $DatabaseName -RefreshType $RefreshType -Server $AnalysisServer -ServicePrincipal -Credential $_Credential
}
```


## <a name="next-steps"></a>Sonraki adımlar

[Örnekler](analysis-services-samples.md)  
[REST API](/rest/api/analysisservices/servers)