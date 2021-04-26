---
title: Azure Izleyici Application Insights klasik kaynağını çalışma alanı tabanlı bir kaynağa geçirme | Microsoft Docs
description: Azure monitörünüzü Application Insights klasik kaynağınızı yeni çalışma alanı tabanlı modele yükseltmek için gereken adımlar hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 5791abe33dee2e62aadb00ae1024338e1e44a900
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100584255"
---
# <a name="migrate-to-workspace-based-application-insights-resources"></a>Çalışma alanı tabanlı Application Insights kaynaklarına geçirin

Bu kılavuz, klasik Application Insights kaynağını çalışma alanı tabanlı bir kaynağa geçirme sürecinde size yol gösterecektir. Çalışma alanı tabanlı kaynaklar Application Insights ve Log Analytics arasında tam tümleştirmeyi destekler. Çalışma alanı tabanlı kaynaklar, uygulama, altyapı ve platform günlüklerini tek bir birleştirilmiş konumda tutarken [Azure izleyici 'nin en son özelliklerine](#new-capabilities) erişmenize olanak sağlayan ortak bir Log Analytics çalışma alanına Application Insights telemetri gönderir.

Çalışma alanı tabanlı kaynaklar, kaynaklarınızın tamamında ortak Azure rol tabanlı erişim denetimi (Azure RBAC) sunar ve uygulamalar arası/çalışma alanı sorgularının gereksinimini ortadan kaldırır.

**Çalışma alanı tabanlı kaynaklar şu anda tüm ticari bölgelerde ve Azure ABD kamu 'da kullanılabilir**

## <a name="new-capabilities"></a>Yeni özellikler

Çalışma alanı tabanlı Application Insights, Azure Izleyici 'nin tüm en son özellikleri ve Log Analytics aşağıdakiler de dahil olmak üzere, şu özelliklerden yararlanmanızı sağlar:

* [Müşteri tarafından yönetilen anahtarlar (CMK)](../logs/customer-managed-keys.md) , yalnızca erişiminiz olan şifreleme anahtarlarıyla verileriniz için bekleyen şifreleme sağlar.
* [Azure özel bağlantı](../logs/private-link-security.md) , Özel uç noktaları kullanarak Azure PaaS hizmetlerini sanal ağınıza güvenli bir şekilde bağlayabilmeniz için izin verir.
* [Profil Oluşturucu Için kendi depolama alanınızı (BYOS) getirin ve Snapshot Debugger](./profiler-bring-your-own-storage.md) , bekleyen şifreleme ilkesi, ömür yönetimi ilkesi ve Application Insights Profiler ve Snapshot Debugger ilişkili tüm veriler için ağ erişimi üzerinde tam denetim sağlar. 
* [Kapasite ayırma katmanları](../logs/manage-cost-storage.md#pricing-model) , Kullandıkça Öde fiyatına kıyasla %25 ' e kadar tasarruf sağlar. 
* Log Analytics akışı alımı aracılığıyla daha hızlı veri alma.

## <a name="migration-process"></a>Geçiş süreci

Çalışma alanı tabanlı bir kaynağa geçiş yaptığınızda, klasik kaynağınızın depolamadan yeni çalışma alanı tabanlı depolamaya veri aktarılmaz. Geçiş yapmak için seçilirse, klasik kaynak verilerinize erişimi korurken yeni verilerin bir Log Analytics çalışma alanına yazıldığı konum değiştirilir. 

Klasik kaynak verileriniz devam eder ve klasik Application Insights kaynağınızın bekletme ayarlarına tabidir. Tüm yeni veri taşıma sonrası geçiş, ilişkili Log Analytics çalışma alanının [bekletme ayarlarına](../logs/manage-cost-storage.md#change-the-data-retention-period) tabi olacaktır, bu da [veri türüne göre farklı saklama ayarlarını](../logs/manage-cost-storage.md#retention-by-data-type)destekler.
Geçiş işlemi **kalıcıdır ve** geri alınamaz. Bir kaynağı çalışma alanı tabanlı Application Insights geçirdiğinizde, her zaman çalışma alanı tabanlı bir kaynak olur. Ancak, geçiş yaptıktan sonra, hedef çalışma alanını gereken sıklıkta değiştirebilirsiniz. 

> [!NOTE]
> Çalışma alanı tabanlı Application Insights kaynakları için veri alımı ve saklama, verilerin bulunduğu [Log Analytics çalışma alanı üzerinden faturalandırılır](../logs/manage-cost-storage.md) . Geçiş işleminden önce klasik Application Insights kaynağına alınan veriler üzerinde 90 günden daha fazla veri bekletme seçtiyseniz, veri saklama bu Application Insights kaynağı üzerinden faturalandırılmaya devam edecektir. Çalışma alanı tabanlı Application Insights kaynakları için faturalandırma hakkında [daha fazla bilgi edinin]( ./pricing.md#workspace-based-application-insights) .

Var olan bir kaynağı geçirmeniz gerekmiyorsa ve bunun yerine yeni bir çalışma alanı tabanlı Application Insights kaynak oluşturmak istiyorsanız, [çalışma alanı tabanlı kaynak oluşturma kılavuzunu](create-workspace-resource.md)kullanın.

## <a name="pre-requisites"></a>Ön koşullar 

- Erişim denetimi modundaki bir Log Analytics çalışma alanı ayarı olarak ayarlanır **`use resource or workspace permissions`** . 

    - Çalışma alanı tabanlı Application Insights kaynakları, adanmış ayara ayarlanmış çalışma alanlarıyla uyumlu değildir **`workspace based permissions`** . Log Analytics çalışma alanı erişim denetimi hakkında daha fazla bilgi edinmek için bkz. [erişim denetimi modu kılavuzunu yapılandırma Log Analytics](../logs/manage-access.md#configure-access-control-mode)

    - Zaten mevcut bir Log Analytics çalışma alanınız yoksa, [Log Analytics çalışma alanı oluşturma belgelerine başvurun](../logs/quick-create-workspace.md).
    
- Çalışma alanı tabanlı kaynaklar için sürekli dışarı aktarma desteklenmez ve devre dışı bırakılmalıdır.
Geçiş işlemi tamamlandıktan sonra, bir depolama hesabına veya Azure Olay Hub 'ına akış olarak veri arşivlemeyi yapılandırmak için [tanılama ayarlarını](../essentials/diagnostic-settings.md) kullanabilirsiniz.  

-   >  Log Analytics çalışma alanınız için genel **kullanım altında ve tahmini maliyet**  >  **veri saklama** ' nın altında bulunan geçerli bekletme ayarlarını denetleyin. Bu ayar, Application Insights kaynağınızı geçirdikten sonra yeni verilerin ne kadar süreyle depolanabileceğini etkiler. Şu anda varsayılan 90 günden daha uzun bir süre için Application Insights verileri depoluuyorsanız ve bu daha büyük saklama süresini saklamak istiyorsanız çalışma alanı bekletme ayarlarınızı ayarlamanız gerekebilir.

## <a name="migrate-your-resource"></a>Kaynağınızı geçirin

Bu bölüm, klasik Application Insights kaynağını yeni çalışma alanı tabanlı kaynak türüne geçirme sürecinde size yol gösterecektir.

1. Application Insights kaynağınız, sol taraftaki menü çubuğundaki **Yapılandır** başlığı altında **Özellikler** ' i seçin.

    ![Kırmızı kutuda vurgulanan Özellikler](./media/convert-classic-resource/properties.png)

2. **`Migrate to Workspace-based`** öğesini seçin.
    
     ![Kaynak geçir düğmesi](./media/convert-classic-resource/migrate.png)

3. Gelecekteki tüm Application Insights telemetrinin depolanmasını istediğiniz Log Analytics çalışma alanını seçin.

     ![Hede çalışma alanını seçme seçeneği ile Geçiş Sihirbazı Kullanıcı arabirimi](./media/convert-classic-resource/migration.png)
    

Kaynağınız geçirildikten sonra, **genel bakış** bölmesinde ilgili çalışma alanı bilgilerini görürsünüz:

![Çalışma Alanı Adı](./media/create-workspace-resource/workspace-name.png)

Mavi bağlantı metnine tıkladığınızda, yeni Birleşik çalışma alanı sorgu ortamından yararlanabilmeniz için ilgili Log Analytics çalışma alanına gidecektir.

## <a name="understanding-log-queries"></a>Günlük sorgularını anlama

Application Insights deneyiminde Application Insights klasik kaynak sorguları, çalışma kitapları ve günlük tabanlı uyarılarınız için de tam geriye doğru uyumluluk sağlıyoruz. 

[Yeni çalışma alanı tabanlı tablo yapısına/şemasına](apm-tables.md)yönelik sorgular yazmak için öncelikle Log Analytics çalışma alanınıza gitmeniz gerekir. 

Çalışma alanınızdaki Log Analytics kullanıcı arabiriminden doğrudan sorgulama yaptığınızda yalnızca geçiş sonrası geçişi yapılacak verileri görürsünüz. Birleştirilmiş bir sorgu deneyiminde geçiş sonrasında klasik Application Insights verilerinizi + yeni verileri görmek için, geçirilen Application Insights kaynağınızın içinden Günlükler (Analiz) sorgu görünümünü kullanın.

## <a name="programmatic-resource-migration"></a>Programlı kaynak geçişi

### <a name="azure-cli"></a>Azure CLI’si

Azure CLı komutlarına önizleme Application Insights erişmek için öncelikle şunu çalıştırmanız gerekir:

```azurecli
 az extension add -n application-insights
```

Komutu çalıştırmazsanız `az extension add` , şu durumlarda bir hata iletisi görürsünüz: `az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

Artık Application Insights kaynağınız oluşturmak için aşağıdakileri çalıştırabilirsiniz:

```azurecli
az monitor app-insights component update --app
                                         --resource-group
                                         [--ingestion-access {Disabled, Enabled}]
                                         [--kind]
                                         [--query-access {Disabled, Enabled}]
                                         [--retention-time]
                                         [--workspace]
```

#### <a name="example"></a>Örnek

```azurecli
az monitor app-insights component update --app your-app-insights-resource-name -g your_resource_group --workspace "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/test1234/providers/microsoft.operationalinsights/workspaces/test1234555"
```

Bu komutla ilgili tam Azure CLı belgeleri için [Azure CLI belgelerine](/cli/azure/ext/application-insights/monitor/app-insights/component#ext-application-insights-az-monitor-app-insights-component-update)başvurun.

### <a name="azure-powershell"></a>Azure PowerShell

`Update-AzApplicationInsights`PowerShell komutu şu anda klasik Application Insights kaynağını çalışma alanına göre geçirmeyi desteklememektedir. PowerShell ile çalışma alanı tabanlı bir kaynak oluşturmak için aşağıdaki Azure Resource Manager şablonları kullanabilir ve PowerShell ile dağıtabilirsiniz.

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager şablonları

#### <a name="template-file"></a>Şablon dosyası

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string"
        },
        "type": {
            "type": "string"
        },
        "regionId": {
            "type": "string"
        },
        "tagsArray": {
            "type": "object"
        },
        "requestSource": {
            "type": "string"
        },
        "workspaceResourceId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "microsoft.insights/components",
            "location": "[parameters('regionId')]",
            "tags": "[parameters('tagsArray')]",
            "apiVersion": "2020-02-02-preview",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Application_Type": "[parameters('type')]",
                "Flow_Type": "Redfield",
                "Request_Source": "[parameters('requestSource')]",
                "WorkspaceResourceId": "[parameters('workspaceResourceId')]"
            }
        }
    ]
}
```

#### <a name="parameters-file"></a>Parametre dosyası

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "type": {
            "value": "web"
        },
        "name": {
            "value": "customresourcename"
        },
        "regionId": {
            "value": "eastus"
        },
        "tagsArray": {
            "value": {}
        },
        "requestSource": {
            "value": "Custom"
        },
        "workspaceResourceId": {
            "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my_resource_group/providers/microsoft.operationalinsights/workspaces/myworkspacename"
        }
    }
}

```

## <a name="modifying-the-associated-workspace"></a>İlişkili çalışma alanını değiştirme

Çalışma alanı tabanlı bir Application Insights kaynağı oluşturulduktan sonra, ilişkili Log Analytics çalışma alanını değiştirebilirsiniz.

Application Insights kaynak bölmesinde **Özellikler**  >  **çalışma alanını Değiştir**  >  **Log Analytics çalışma alanları**' nı seçin.

## <a name="troubleshooting"></a>Sorun giderme

### <a name="access-mode"></a>Erişim modu

**Hata iletisi:** *Seçili çalışma alanı, çalışma alanı tabanlı erişim moduyla yapılandırıldı. Bazı APM özellikleri etkilenebilir. Başka bir çalışma alanı seçin veya çalışma alanı ayarlarında kaynak tabanlı erişime izin verin. CLı kullanarak bu hatayı geçersiz kılabilirsiniz.* 

Çalışma alanı tabanlı Application Insights kaynağınızın düzgün çalışması için, hedef Log Analytics çalışma alanınızın erişim denetimi modunu **kaynak veya çalışma alanı izinleri** ayarına değiştirmeniz gerekir. Bu ayar, **Özellikler**  >  **erişim denetimi modu** altında Log Analytics çalışma alanı kullanıcı arabiriminde bulunur. Ayrıntılı yönergeler için bkz. [erişim denetimi modu kılavuzunu yapılandırma Log Analytics](../logs/manage-access.md#configure-access-control-mode). Erişim denetimi modiniz özel **çalışma alanı izinleri** ayarı olarak ayarlandıysa, portal geçiş deneyimi aracılığıyla geçiş engellenmeye devam eder.

Erişim denetimi modunu geçerli hedef çalışma alanınızın güvenlik nedenleriyle değiştirememek için, geçiş için kullanmak üzere yeni bir Log Analytics çalışma alanı oluşturmanızı öneririz. 

### <a name="continuous-export"></a>Sürekli dışarı aktarma

**Hata iletisi:** *devam etmeden önce sürekli dışarı aktarmanın devre dışı bırakılması gerekiyor. Geçişten sonra, dışarı aktarma için tanılama ayarlarını kullanın.* 

Eski sürekli dışa aktarma işlevselliği, çalışma alanı tabanlı kaynaklar için desteklenmez. Geçirmeden önce, sürekli dışarı aktarmayı devre dışı bırakmanız gerekir.

1. Application Insights kaynak görünümünden, **yapılandırma** başlığı altında **sürekli dışarı aktarma**' yı seçin.

    ![Sürekli dışa aktarma menü öğesi](./media/convert-classic-resource/continuous-export.png)

2. **Devre dışı bırak** seçeneğini belirleyin.

    ![Sürekli dışarı aktarma devre dışı düğmesi](./media/convert-classic-resource/disable.png)

- Devre dışı bırak ' ı seçtikten sonra, geçiş Kullanıcı arabirimine geri gidebilirsiniz. Sürekli dışarı aktarmayı Düzenle sayfası, ayarlarınızın kaydedilmesini isterse, sürekli dışarı aktarmayı devre dışı bırakma/etkinleştirme ile ilgili olmadığından bu istem için Tamam ' ı seçebilirsiniz.

- Application Insights kaynağınızı çalışma alanı tabanlı olarak başarıyla geçirdikten sonra, sağlamak üzere sürekli dışarı aktarmanın kullanıldığı işlevselliği değiştirmek için tanılama ayarlarını kullanabilirsiniz. Application Insights kaynağınız içinden **tanılama ayarlarını**  >  **Tanılama ayarı Ekle** ' yi seçin. Bir depolama hesabına arşivlemek veya bir Azure Olay Hub 'ına akış yapmak için tüm tabloları veya tabloların bir alt kümesini seçebilirsiniz. Tanılama ayarları hakkında ayrıntılı yönergeler için [Azure izleyici Tanılama ayarları kılavuzuna](../essentials/diagnostic-settings.md)bakın.

### <a name="retention-settings"></a>Bekletme ayarları

**Uyarı iletisi:** *özelleştirilmiş Application Insights saklama ayarlarınız, çalışma alanına gönderilen veriler için uygulanmaz. Bunu ayrı olarak yeniden yapılandırmanız gerekecektir.*

Geçirmeden önce herhangi bir değişiklik yapmanız gerekmez, ancak bu ileti geçerli Application Insights bekletme ayarlarınızın varsayılan 90 günlük bekletme dönemine ayarlamadığında sizi uyarır. Bu uyarı iletisi, yeni verileri geçirmeden ve almadan önce Log Analytics çalışma alanınızın bekletme ayarlarını değiştirmek isteyebileceğiniz anlamına gelir. 

  >  Log Analytics kullanıcı arabiriminden genel **kullanım ve tahmini maliyet**  >  **veri saklama** altında Log Analytics için geçerli bekletme ayarlarınızı kontrol edebilirsiniz. Bu ayar, Application Insights kaynağınızı geçirdikten sonra yeni verilerin ne kadar süreyle depolanabileceğini etkiler.

## <a name="next-steps"></a>Sonraki adımlar

* [Ölçümleri keşfetme](../essentials/metrics-charts.md)
* [Analytics sorguları yazma](../logs/log-query-overview.md)
