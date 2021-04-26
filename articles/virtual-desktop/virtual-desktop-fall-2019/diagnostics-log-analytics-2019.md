---
title: Windows sanal masaüstü (klasik) tanılama günlüğü Analizi-Azure
description: Log Analytics 'i Windows sanal masaüstü (klasik) tanılama özelliği ile kullanma.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 9cfa50e13756692295c84b02d02dd71228b16eb9
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445014"
---
# <a name="use-log-analytics-for-the-diagnostics-feature-in-windows-virtual-desktop-classic"></a>Windows sanal masaüstündeki (klasik) tanılama özelliği için Log Analytics kullanma

>[!IMPORTANT]
>Bu içerik, Windows sanal masaüstü nesneleri Azure Resource Manager desteklemeyen Windows sanal masaüstü (klasik) için geçerlidir. Azure Resource Manager Windows sanal masaüstü nesnelerini yönetmeye çalışıyorsanız, [Bu makaleye](../diagnostics-log-analytics.md)bakın.

Windows sanal masaüstü, yöneticinin sorunları tek bir arabirim üzerinden belirlemesine izin veren bir tanılama özelliği sunar. Bu özellik, her biri Windows sanal masaüstü rolü atanan hizmeti kullandığında tanılama bilgilerini günlüğe kaydeder. Her günlük, etkinlikte hangi Windows sanal masaüstü rolünün söz konusu olduğunu, oturum sırasında görünen tüm hata iletilerini, kiracı bilgilerini ve Kullanıcı bilgilerini içerir. Tanılama özelliği, hem Kullanıcı hem de yönetim eylemleri için etkinlik günlükleri oluşturur. Her etkinlik günlüğü üç ana kategoride yer düşer:

- Akış aboneliği etkinlikleri: bir Kullanıcı, Microsoft Uzak Masaüstü uygulamalar aracılığıyla akışına bağlanmayı denediğinde.
- Bağlantı etkinlikleri: bir Kullanıcı, Microsoft Uzak Masaüstü uygulamalar aracılığıyla masaüstüne veya RemoteApp 'e bağlanmayı denediğinde.
- Yönetim etkinlikleri: bir yönetici, sistemde konak havuzları oluşturma, kullanıcıları uygulama gruplarına atama ve rol atamaları oluşturma gibi yönetim işlemlerini gerçekleştirdiğinde.

Tanılama rolü hizmeti Windows sanal masaüstü 'nün bir parçası olduğundan, Windows sanal masaüstüne ulaşmayacak bağlantılar tanılama sonuçlarında gösterilmez. Kullanıcı ağ bağlantısı sorunları yaşanıyorsa Windows sanal masaüstü bağlantısı sorunları oluşabilir.

## <a name="why-you-should-use-log-analytics"></a>Neden Log Analytics kullanmalısınız?

Azure istemcisinde, tek kullanıcılı sorun gidermeyi aşan tanılama verilerini çözümlemek için Log Analytics kullanmanızı öneririz. VM performans sayaçlarını Log Analytics içine çekebilmeniz için, dağıtımınız için bilgi toplamak üzere bir aracınız vardır.

## <a name="before-you-get-started"></a>Başlamadan önce

Tanılama özelliğiyle Log Analytics kullanabilmeniz için önce [bir çalışma alanı oluşturmanız](../../azure-monitor/vm/quick-collect-windows-computer.md#create-a-workspace)gerekir.

Çalışma alanınızı oluşturduktan sonra, aşağıdaki bilgileri almak için [Windows bilgisayarları Azure 'A bağlama](../../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key) bölümündeki yönergeleri izleyin:

- Çalışma alanı KIMLIĞI
- Çalışma alanınızın birincil anahtarı

Bu bilgilere kurulum sürecinde daha sonra ihtiyacınız olacak.

## <a name="push-diagnostics-data-to-your-workspace"></a>Tanılama verilerini çalışma alanınıza gönderin

Windows sanal masaüstü kiracınızdan tanılama verilerini, çalışma alanınız için Log Analytics gönderebilirsiniz. Bu özelliği, çalışma alanınızı kiracınıza bağlayarak kiracınızı ilk kez oluşturduğunuzda veya daha sonra mevcut bir kiracı ile ayarlarsanız ayarlayabilirsiniz.

Yeni kiracınızı ayarlarken kiracınızı Log Analytics çalışma alanınıza bağlamak için aşağıdaki cmdlet 'i çalıştırarak Windows sanal masaüstü 'nde TenantCreator Kullanıcı hesabınızla oturum açın:

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com
```

Yeni bir kiracı yerine var olan bir kiracıyı bağlayacaksanız bunun yerine bu cmdlet 'i çalıştırın:

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String>
```

Log Analytics bağlamak istediğiniz her kiracı için bu cmdlet 'leri çalıştırmanız gerekir.

>[!NOTE]
>Kiracı oluştururken Log Analytics çalışma alanını bağlamak istemiyorsanız `New-RdsTenant` cmdlet 'ini çalıştırın.

## <a name="cadence-for-sending-diagnostic-events"></a>Tanılama olaylarını göndermek için temposunda

Tanılama olayları tamamlandığında Log Analytics gönderilir.

## <a name="example-queries"></a>Örnek sorgular

Aşağıdaki örnek sorgularda, tanılama özelliğinin sisteminizdeki en sık etkinlikler için nasıl rapor oluşturduğu gösterilmektedir:

Bu ilk örnek, desteklenen uzak masaüstü istemcilerine sahip kullanıcılar tarafından başlatılan bağlantı etkinliklerini gösterir:

```powershell
WVDActivityV1_CL

| where Type_s == "Connection"

| join kind=leftouter (

    WVDErrorV1_CL

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g

    ) on $left.Id_g  == $right.ActivityId_g 

| join  kind=leftouter (

    WVDCheckpointV1_CL

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g

    ) on $left.Id_g  == $right.ActivityId_g

|project-away ActivityId_g, ActivityId_g1
```

Bu sonraki örnek sorgu, kiracılardaki yöneticilere göre yönetim etkinliklerini gösterir:

```powershell
WVDActivityV1_CL

| where Type_s == "Management"

| join kind=leftouter (

    WVDErrorV1_CL

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g

    ) on $left.Id_g  == $right.ActivityId_g 

| join  kind=leftouter (

    WVDCheckpointV1_CL

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g

    ) on $left.Id_g  == $right.ActivityId_g

|project-away ActivityId_g, ActivityId_g1
```

## <a name="stop-sending-data-to-log-analytics"></a>Log Analytics veri göndermeyi durdur

Mevcut bir kiracıdan Log Analytics veri göndermeyi durdurmak için aşağıdaki cmdlet 'i çalıştırın ve boş dizeleri ayarlayın:

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String>
```

Verilerin gönderilmesini durdurmak istediğiniz her kiracı için bu cmdlet 'i çalıştırmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Tanılama özelliğinin sizin için tanımlayabilen yaygın hata senaryolarını gözden geçirmek için bkz. [sorunları tanımlama ve tanılama](diagnostics-role-service-2019.md#common-error-scenarios).
