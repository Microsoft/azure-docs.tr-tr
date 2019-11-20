---
title: Hizmet sorumluları ile Azure Analysis Services görevleri otomatikleştirin | Microsoft Docs
description: Azure Analysis Services yönetim görevlerini otomatikleştirmek için bir hizmet sorumlusu oluşturmayı öğrenin.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5078fcc1ba5c581aca475025b286d0319d6024a6
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73572599"
---
# <a name="automation-with-service-principals"></a>Hizmet sorumlularıyla otomasyon

Hizmet sorumluları, katılımsız kaynak ve hizmet düzeyinde işlemler gerçekleştirmek için kiracınızın içinde oluşturduğunuz bir Azure Active Directory uygulama kaynağıdır. Bunlar, bir uygulama KIMLIĞI ve parolası ya da sertifikası olan benzersiz bir *Kullanıcı kimliği* türüdür. Bir hizmet sorumlusu yalnızca atandığı roller ve izinler tarafından tanımlanan görevleri gerçekleştirmek için gerekli izinlere sahiptir. 

Analysis Services, hizmet sorumluları, ortak görevleri otomatikleştirmek için Azure Otomasyonu, PowerShell katılımsız modu, özel istemci uygulamaları ve Web uygulamaları ile birlikte kullanılır. Örneğin, sunucuları sağlama, modelleri dağıtma, veri yenileme, ölçek artırma/azaltma ve duraklatma/devam etmeyi hizmet sorumluları kullanılarak otomatik hale getirilebilir. İzinler, normal Azure AD UPN hesaplarına benzer şekilde rol üyeliğiyle, hizmet sorumlularına atanır.

Analysis Services Ayrıca, hizmet sorumlularını kullanan yönetilen kimlikler tarafından gerçekleştirilen işlemleri destekler. Daha fazla bilgi edinmek için bkz. Azure [kaynakları Için Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/overview.md) ve Azure [ad kimlik doğrulamasını destekleyen Azure hizmetleri](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-analysis-services).

## <a name="create-service-principals"></a>Hizmet sorumlusu oluşturma
 
Hizmet sorumluları Azure portal veya PowerShell kullanarak oluşturulabilir. Daha fazla bilgi için bkz:

[Hizmet sorumlusu oluşturma-Azure portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[Hizmet sorumlusu oluşturma - PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="store-credential-and-certificate-assets-in-azure-automation"></a>Azure Otomasyonu 'nda kimlik bilgilerini ve sertifika varlıklarını depolayın

Hizmet sorumlusu kimlik bilgileri ve sertifikaları, runbook işlemleri için Azure Otomasyonu 'nda güvenli bir şekilde depolanabilir. Daha fazla bilgi için bkz:

[Azure Automation 'Da kimlik bilgisi varlıkları](../automation/automation-credentials.md)   
[Azure Otomasyonu'ndaki sertifika varlıkları](../automation/automation-certificates.md)

## <a name="add-service-principals-to-server-admin-role"></a>Sunucu Yöneticisi rolüne hizmet sorumluları ekleme

Analysis Services sunucusu yönetim işlemleri için bir hizmet sorumlusu kullanabilmeniz için, bunu sunucu yöneticileri rolüne eklemeniz gerekir. Daha fazla bilgi için bkz. [Sunucu Yöneticisi rolüne hizmet sorumlusu ekleme](analysis-services-addservprinc-admins.md).

## <a name="service-principals-in-connection-strings"></a>Bağlantı dizelerinde hizmet sorumluları

Hizmet sorumlusu uygulama kimliği ve parolası ya da sertifikası, bir UPN ile aynı bağlantı dizelerinde kullanılabilir.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

#### <a name="a-nameazmodule-using-azanalysisservices-module"></a>Az. AnalysisServices modülünü kullanarak <a name="azmodule" />

[Az. AnalysisServices](/powershell/module/az.analysisservices) modülüyle kaynak yönetimi işlemleri için bir hizmet sorumlusu kullanırken `Connect-AzAccount` cmdlet 'ini kullanın. 

Aşağıdaki örnekte, AppID ve parola, salt okuma çoğaltmaları ve ölçeği artırma/genişletme işlemleri için denetim düzlemi işlemlerini gerçekleştirmek üzere kullanılır:

```powershell
Param (
        [Parameter(Mandatory=$true)] [String] $AppId,
        [Parameter(Mandatory=$true)] [String] $PlainPWord,
        [Parameter(Mandatory=$true)] [String] $TenantId
       )
$PWord = ConvertTo-SecureString -String $PlainPWord -AsPlainText -Force
$Credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AppId, $PWord

# Connect using Az module
Connect-AzAccount -Credential $Credential -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx"

# Syncronize a database for query scale out
Sync-AzAnalysisServicesInstance -Instance "asazure://westus.asazure.windows.net/testsvr" -Database "testdb"

# Scale up the server to an S1, set 2 read-only replicas, and remove the primary from the query pool. The new replicas will hydrate from the synchronized data.
Set-AzAnalysisServicesServer -Name "testsvr" -ResourceGroupName "testRG" -Sku "S1" -ReadonlyReplicaCount 2 -DefaultConnectionMode Readonly
```

#### <a name="using-sqlserver-module"></a>SQLServer modülünü kullanma

Aşağıdaki örnekte, bir model veritabanı yenileme işlemi gerçekleştirmek için AppID ve bir parola kullanılır:

```powershell
Param (
        [Parameter(Mandatory=$true)] [String] $AppId,
        [Parameter(Mandatory=$true)] [String] $PlainPWord,
        [Parameter(Mandatory=$true)] [String] $TenantId
       )
$PWord = ConvertTo-SecureString -String $PlainPWord -AsPlainText -Force

$Credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AppId, $PWord

Invoke-ProcessTable -Server "asazure://westcentralus.asazure.windows.net/myserver" -TableName "MyTable" -Database "MyDb" -RefreshType "Full" -ServicePrincipal -ApplicationId $AppId -TenantId $TenantId -Credential $Credential
```

### <a name="amo-and-adomd"></a>AMO ve ADOMD 

İstemci uygulamaları ve Web Apps ile bağlanırken, [amo ve ADOMD istemci kitaplıkları](analysis-services-data-providers.md) sürüm 15.0.2 ve bağlantı dizelerindeki NuGet destek hizmeti sorumlularını kullanarak şu sözdizimini kullanarak daha fazla yüklenebilir paketler: `app:AppID` ve parola veya @no_ _t_2_ .`cert:thumbprint` 

Aşağıdaki örnekte `appID` ve bir `password` model veritabanı yenileme işlemi gerçekleştirmek için kullanılır:

```csharp
string appId = "xxx";
string authKey = "yyy";
string connString = $"Provider=MSOLAP;Data Source=asazure://westus.asazure.windows.net/<servername>;User ID=app:{appId};Password={authKey};";
Server server = new Server();
server.Connect(connString);
Database db = server.Databases.FindByName("adventureworks");
Table tbl = db.Model.Tables.Find("DimDate");
tbl.RequestRefresh(RefreshType.Full);
db.Model.SaveChanges();
```

## <a name="next-steps"></a>Sonraki adımlar
[Azure PowerShell  Ile oturum açın](https://docs.microsoft.com/powershell/azure/authenticate-azureps)  
[Sunucu Yöneticisi rolüne hizmet sorumlusu ekleme](analysis-services-addservprinc-admins.md)   
