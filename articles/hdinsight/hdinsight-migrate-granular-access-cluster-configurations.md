---
title: Ayrıntılı rol tabanlı erişim Azure HDInsight küme yapılandırması
description: HDInsight küme yapılandırmalarına yönelik ayrıntılı rol tabanlı erişime geçiş kapsamında gereken değişiklikler hakkında bilgi edinin.
author: tylerfox
ms.author: tyfox
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/20/2020
ms.openlocfilehash: afb30f4648f1649bf6cc6cc6a3bf02f433f49d45
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774941"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Küme yapılandırmaları için ayrıntılı rol tabanlı erişime geçme

Hassas bilgileri almak için daha ayrıntılı rol tabanlı erişimi desteklemeye yönelik bazı önemli değişiklikler sunuyoruz. Bu değişikliklerin bir parçası olarak, [Etkilenen varlıkların/senaryolardan](#am-i-affected-by-these-changes)birini kullanıyorsanız bazı eylemler **3 Eylül 2019 '** de gerekebilir.

## <a name="what-is-changing"></a>Ne değişiyor?

Daha önce, kullanıcılar izin, katkıda bulunan veya okuyucu [Azure rollerinin](../role-based-access-control/rbac-and-directory-admin-roles.md)sahibi olan herkes tarafından kullanılabilir oldukları için, küme kullanıcıları tarafından HDInsight API 'si aracılığıyla elde edilebilir `*/read` . Gizli dizileri, bir kullanıcının rolünden izin verilmelidir daha fazla yükseltilmiş erişim elde etmek için kullanılabilecek değerler olarak tanımlanır. Bunlar, küme ağ geçidi HTTP kimlik bilgileri, depolama hesabı anahtarları ve veritabanı kimlik bilgileri gibi değerleri içerir.

3 Eylül 2019 ' den başlayarak, bu gizli bilgilere erişmek için izin gerekir, bu da `Microsoft.HDInsight/clusters/configurations/action` artık okuyucu rolüne sahip kullanıcılar tarafından erişilemeyeceği anlamına gelir. Bu izne sahip roller katkıda bulunan, sahip ve yeni HDInsight küme Işletmeni rolü (aşağıda daha fazla).

Ayrıca, katkıda bulunan veya sahip 'in yönetim izinleri verilmeden gizli dizileri alabilecek yeni bir [HDInsight küme işletmeni](../role-based-access-control/built-in-roles.md#hdinsight-cluster-operator) rolü sunuyoruz. Özetlemek gerekirse:

| Rol                                  | Virüslü                                                                                       | Ileri git       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Okuyucu                                | -Gizli dizileri de içeren okuma erişimi.                                                                   | -Gizli dizileri **hariç** okuma erişimi | 
| HDInsight küme operatörü<br>(Yeni rol) | Yok                                                                                              | -Gizli dizileri dahil okuma/yazma erişimi         | 
| Katılımcı                           | -Parolalar da dahil olmak üzere okuma/yazma erişimi.<br>-Tüm Azure kaynakları türlerini oluşturun ve yönetin.<br>-Betik eylemlerini yürütün.     | düzeltme sınıfı, |
| Sahip                                 | -Gizlilikler dahil olmak üzere okuma/yazma erişimi.<br>-Tüm kaynaklara tam erişim<br>-Başkalarına erişim yetkisi verin.<br>-Betik eylemlerini yürütün. | düzeltme sınıfı, |

Bir kullanıcıya HDInsight kümesi operatörü rolü atamasını ekleme/yazma erişimi sağlamak üzere bir kullanıcıya ekleme hakkında daha fazla bilgi için, bkz. [bir kullanıcıya HDInsight küme işletmeni rol atamasını ekleme](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user).

## <a name="am-i-affected-by-these-changes"></a>Bu değişikliklerden etkilendim mıyım?

Aşağıdaki varlıklar ve senaryolar etkilenir:

- [API](#api): `/configurations` veya `/configurations/{configurationName}` uç noktalarını kullanan kullanıcılar.
- Visual Studio Code sürüm 1.1.1 veya altındaki [Azure HDInsight araçları](#azure-hdinsight-tools-for-visual-studio-code) .
- [Azure Toolkit for IntelliJ](#azure-toolkit-for-intellij) Version 3.20.0 veya below.
- [Visual Studio 'nun 2.3.9000.1 sürümü için Azure Data Lake ve Stream Analytics araçları](#azure-data-lake-and-stream-analytics-tools-for-visual-studio) .
- [Azure Toolkit for Eclipse](#azure-toolkit-for-eclipse) Version 3.15.0 veya below.
- [.NET için SDK](#sdk-for-net)
    - [Sürüm 1. x veya 2. x](#versions-1x-and-2x): kullanıcılar,, `GetClusterConfigurations` , `GetConnectivitySettings` `ConfigureHttpSettings` `EnableHttp` veya `DisableHttp` yöntemlerini configurationsoperationsextensions sınıfından kullanıyor.
    - [sürüm 3. x ve üst](#versions-3x-and-up): `Get` sınıfından,, `Update` `EnableHttp` veya yöntemlerini kullanan kullanıcılar `DisableHttp` `ConfigurationsOperationsExtensions` .
- [Python Için SDK](#sdk-for-python): `get` sınıfından veya yöntemlerini kullanan kullanıcılar `update` `ConfigurationsOperations` .
- [Java Için SDK](#sdk-for-java): `update` sınıfından veya yöntemlerini kullanan kullanıcılar `get` `ConfigurationsInner` .
- [Go Için SDK](#sdk-for-go): `Get` `Update` struct 'dan veya yöntemlerini kullanan kullanıcılar `ConfigurationsClient` .
- [Az. HDInsight PowerShell](#azhdinsight-powershell) in Version 2.0.0.
Senaryonuza yönelik geçiş adımlarını görmek için aşağıdaki bölümlere bakın (veya yukarıdaki bağlantıları kullanın).

### <a name="api"></a>API

Aşağıdaki API 'Ler değiştirilecek veya kullanım dışı bırakılacak:

- [**Get/configurations/{configurationName}**](/rest/api/hdinsight/hdinsight-cluster#get-configuration) (hassas bilgiler kaldırıldı)
    - Daha önce ayrı yapılandırma türlerini (parolalar dahil) almak için kullanılır.
    - 3 Eylül 2019 ' den itibaren bu API çağrısı artık gizli dizileri olan ayrı yapılandırma türleri döndürüyor. Gizli dizileri de içeren tüm yapılandırmaların elde edilmesi için yeni GÖNDERI/yapılandırma çağrısını kullanın. Yalnızca ağ geçidi ayarlarını almak için yeni POST/getGatewaySettings çağrısını kullanın.
- [**/Configurations al**](/rest/api/hdinsight/hdinsight-cluster#get-configuration) (kullanım dışı)
    - Daha önce tüm yapılandırmaların (gizli diziler dahil) alınması için kullanılır
    - 3 Eylül 2019 ' den itibaren bu API çağrısı kullanım dışı bırakılacak ve artık desteklenmeyecektir. İleri doğru olan tüm yapılandırmaların elde etmek için yeni POST/Configurations çağrısını kullanın. Gizli parametrelere sahip yapılandırmaların elde edilmesi için, GET/configurations/{configurationName} çağrısını kullanın.
- [**Post/configurations/{configurationName}**](/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings) (kullanım dışı)
    - Daha önce Ağ Geçidi kimlik bilgilerini güncelleştirmek için kullanılır.
    - 3 Eylül 2019 ' den itibaren bu API çağrısı kullanım dışı bırakılacak ve artık desteklenmeyecektir. Bunun yerine yeni POST/updateGatewaySettings komutunu kullanın.

Aşağıdaki değiştirme API 'Leri eklendi:</span>

- [**GÖNDERI/yapılandırma**](/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - Gizli dizileri dahil tüm yapılandırmaların elde edilmesi için bu API 'yi kullanın.
- [**POST/getGatewaySettings**](/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - Ağ Geçidi ayarlarını almak için bu API 'YI kullanın.
- [**/UpdateGatewaySettings SONRASı**](/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - Ağ Geçidi ayarlarını (Kullanıcı adı ve/veya parola) güncelleştirmek için bu API 'YI kullanın.

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Visual Studio Code için Azure HDInsight araçları

Sürüm 1.1.1 veya aşağıda kullanıyorsanız, kesintileri önlemek için [Visual Studio Code Için Azure HDInsight araçları 'nın en son sürümüne](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) güncelleştirin.

### <a name="azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ

Sürüm 3.20.0 veya aşağıdaki sürümü kullanıyorsanız kesintileri önlemek için [Azure Toolkit for IntelliJ eklentisinin en son sürümüne](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) güncelleştirin.

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>Visual Studio için Azure Data Lake ve Stream Analytics araçları

Kesintiden kaçınmak üzere [Visual Studio için Azure Data Lake ve Stream Analytics araçları](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview) sürümüne güncelleştirin.  Güncelleştirme hakkında yardım için, [Visual Studio için belgelerimizi, güncelleştirme Data Lake araçları](./hadoop/apache-hadoop-visual-studio-tools-get-started.md#update-data-lake-tools-for-visual-studio)' nı inceleyin.

### <a name="azure-toolkit-for-eclipse"></a>Azure Toolkit for Eclipse

Sürüm 3.15.0 veya aşağıdaki sürümü kullanıyorsanız kesintileri önlemek için [Azure Toolkit for Eclipse en son sürümüne](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) güncelleştirin.

### <a name="sdk-for-net"></a>.NET için SDK

#### <a name="versions-1x-and-2x"></a>Sürüm 1. x ve 2. x

.NET için HDInsight SDK 'sının [2.1.0 sürümüne](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) güncelleştirin. Aşağıdaki değişikliklerden etkilenen bir yöntem kullanıyorsanız, minimum kod değişiklikleri gerekebilir:

- `ClusterOperationsExtensions.GetClusterConfigurations` , artık depolama anahtarları (çekirdek-site) veya HTTP kimlik bilgileri (ağ geçidi) gibi **hassas parametreleri döndürmez** .
    - Gizli parametreler dahil tüm yapılandırmaların alınması için ileri ' yi kullanın `ClusterOperationsExtensions.ListConfigurations` .  ' Reader ' rolüne sahip kullanıcıların bu yöntemi kullanabilediğine unutmayın. Bu, kullanıcıların bir küme için hassas bilgilere erişebileceği ayrıntılı denetim sağlar.
    - Yalnızca HTTP ağ geçidi kimlik bilgilerini almak için kullanın `ClusterOperationsExtensions.GetGatewaySettings` .

- `ClusterOperationsExtensions.GetConnectivitySettings` Artık kullanım dışıdır ve ile değiştirilmiştir `ClusterOperationsExtensions.GetGatewaySettings` .

- `ClusterOperationsExtensions.ConfigureHttpSettings` Artık kullanım dışıdır ve ile değiştirilmiştir `ClusterOperationsExtensions.UpdateGatewaySettings` .

- `ConfigurationsOperationsExtensions.EnableHttp` ve `DisableHttp` artık kullanım dışıdır. HTTP artık her zaman etkindir, bu nedenle bu yöntemlere artık gerek yoktur.

#### <a name="versions-3x-and-up"></a>Sürüm 3. x ve yukarı

.NET için HDInsight SDK 'nın 5.0.0 veya sonraki bir [sürümüyle](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) güncelleştirin. Aşağıdaki değişikliklerden etkilenen bir yöntem kullanıyorsanız, minimum kod değişiklikleri gerekebilir:

- [`ConfigurationOperationsExtensions.Get`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get) , artık depolama anahtarları (çekirdek-site) veya HTTP kimlik bilgileri (ağ geçidi) gibi **hassas parametreleri döndürmez** .
    - Gizli parametreler dahil tüm yapılandırmaların alınması için ileri ' yi kullanın [`ConfigurationOperationsExtensions.List`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list) .' Reader ' rolüne sahip kullanıcıların bu yöntemi kullanabilediğine unutmayın. Bu, kullanıcıların bir küme için hassas bilgilere erişebileceği ayrıntılı denetim sağlar. 
    - Yalnızca HTTP ağ geçidi kimlik bilgilerini almak için kullanın [`ClusterOperationsExtensions.GetGatewaySettings`](/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings) . 
- [`ConfigurationsOperationsExtensions.Update`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update) Artık kullanım dışıdır ve ile değiştirilmiştir [`ClusterOperationsExtensions.UpdateGatewaySettings`](/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings) . 
- [`ConfigurationsOperationsExtensions.EnableHttp`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp) ve [`DisableHttp`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp) artık kullanım dışıdır. HTTP artık her zaman etkindir, bu nedenle bu yöntemlere artık gerek yoktur.

### <a name="sdk-for-python"></a>Python için SDK

Python için HDInsight SDK 'sının [Version 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) veya üzeri sürümüne güncelleştirin. Aşağıdaki değişikliklerden etkilenen bir yöntem kullanıyorsanız, minimum kod değişiklikleri gerekebilir:

- [`ConfigurationsOperations.get`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-) , artık depolama anahtarları (çekirdek-site) veya HTTP kimlik bilgileri (ağ geçidi) gibi **hassas parametreleri döndürmez** .
    - Gizli parametreler dahil tüm yapılandırmaların alınması için ileri ' yi kullanın [`ConfigurationsOperations.list`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) .' Reader ' rolüne sahip kullanıcıların bu yöntemi kullanabilediğine unutmayın. Bu, kullanıcıların bir küme için hassas bilgilere erişebileceği ayrıntılı denetim sağlar. 
    - Yalnızca HTTP ağ geçidi kimlik bilgilerini almak için kullanın [`ClusterOperations.get_gateway_settings`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) .
- [`ConfigurationsOperations.update`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#update-resource-group-name--cluster-name--configuration-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-) Artık kullanım dışıdır ve ile değiştirilmiştir [`ClusterOperations.update_gateway_settings`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-) .

### <a name="sdk-for-java"></a>Java Için SDK

Java için HDInsight SDK 'sının [Version 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/1.0.0/jar) veya üzeri sürümüne güncelleştirin. Aşağıdaki değişikliklerden etkilenen bir yöntem kullanıyorsanız, minimum kod değişiklikleri gerekebilir:

- `ConfigurationsInner.get` , artık depolama anahtarları (çekirdek-site) veya HTTP kimlik bilgileri (ağ geçidi) gibi **hassas parametreleri döndürmez** .
- `ConfigurationsInner.update` Artık kullanım dışıdır.

### <a name="sdk-for-go"></a>Go Için SDK

Go için HDInsight SDK 'sının [Version 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight) veya üzeri sürümüne güncelleştirin. Aşağıdaki değişikliklerden etkilenen bir yöntem kullanıyorsanız, minimum kod değişiklikleri gerekebilir:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.Get) , artık depolama anahtarları (çekirdek-site) veya HTTP kimlik bilgileri (ağ geçidi) gibi **hassas parametreleri döndürmez** .
    - Gizli parametreler dahil tüm yapılandırmaların alınması için ileri ' yi kullanın [`ConfigurationsClient.list`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.List) .' Reader ' rolüne sahip kullanıcıların bu yöntemi kullanabilediğine unutmayın. Bu, kullanıcıların bir küme için hassas bilgilere erişebileceği ayrıntılı denetim sağlar. 
    - Yalnızca HTTP ağ geçidi kimlik bilgilerini almak için kullanın [`ClustersClient.get_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ClustersClient.GetGatewaySettings) .
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.Update) Artık kullanım dışıdır ve ile değiştirilmiştir [`ClustersClient.update_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings) .

### <a name="azhdinsight-powershell"></a>Az. HDInsight PowerShell
Kesintileri önlemek için [az PowerShell Version 2.0.0](https://www.powershellgallery.com/packages/Az) veya üzeri bir sürüme güncelleştirin.  Bu değişikliklerden etkilenen bir yöntem kullanıyorsanız, minimum kod değişiklikleri gerekebilir.
- `Grant-AzHDInsightHttpServicesAccess` Artık kullanım dışıdır ve yeni cmdlet ile değiştirilmiştir `Set-AzHDInsightGatewayCredential` .
- `Get-AzHDInsightJobOutput` , depolama anahtarına ayrıntılı rol tabanlı erişimi destekleyecek şekilde güncelleştirilmiştir.
    - HDInsight Küme Operatörü, Katkıda Bulunan veya Sahip rolleri olan kullanıcılar etkilenmeyecek.
    - Yalnızca okuyucu rolüne sahip kullanıcıların `DefaultStorageAccountKey` parametresini açıkça belirtmesi gerekecektir.
- `Revoke-AzHDInsightHttpServicesAccess` Artık kullanım dışıdır. HTTP artık her zaman etkindir, bu nedenle bu cmdlet artık gerekli değildir.
 Bkz [. az. ](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight) Daha fazla ayrıntı Için HDInsight geçiş kılavuzu.

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>Kullanıcıya HDInsight küme Işletmeni rolü atamasını ekleme

[Sahip](../role-based-access-control/built-in-roles.md#owner) rolüne sahip bir Kullanıcı, gizli HDInsight küme yapılandırma değerlerine (küme ağ geçidi kimlik bilgileri ve depolama hesabı anahtarları gibi) okuma/yazma erişimi olmasını Istediğiniz kullanıcılara [HDInsight küme işletmeni](../role-based-access-control/built-in-roles.md#hdinsight-cluster-operator) rolünü atayabilir.

### <a name="using-the-azure-cli"></a>Azure CLI'yı kullanma

Bu rol atamasını eklemenin en kolay yolu, `az role assignment create` Azure CLI 'deki komutunu kullanmaktır.

> [!NOTE]
> Bu komut, yalnızca bu izinleri verebileceği için sahip rolüne sahip bir kullanıcı tarafından çalıştırılmalıdır. , `--assignee` HDInsight küme işletmeni rolünü atamak istediğiniz kullanıcının hizmet sorumlusu veya e-posta adresi adıdır. Yetersiz izinler hatası alırsanız aşağıdaki SSS bölümüne bakın.

#### <a name="grant-role-at-the-resource-cluster-level"></a>Kaynak (küme) düzeyinde rol verme

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>Kaynak grubu düzeyinde rol verme

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>Abonelik düzeyinde rol verme

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>Azure portalını kullanma

Alternatif olarak, bir kullanıcıya HDInsight küme operatörü rolü atamasını eklemek için Azure portal kullanabilirsiniz. [Azure Portal kullanarak Azure rollerini atamaya](../role-based-access-control/role-assignments-portal.md)yönelik belgelere bakın.

## <a name="faq"></a>SSS

### <a name="why-am-i-seeing-a-403-forbidden-response-after-updating-my-api-requests-andor-tool"></a>API isteklerimi ve/veya aracımı güncelleştirdikten sonra neden 403 (Yasak) yanıtını görüyorum?

Küme yapılandırmalarının artık ayrıntılı rol tabanlı erişim denetimi gerisinde ve `Microsoft.HDInsight/clusters/configurations/*` bunlara erişim izni olması gerekir. Bu izni almak için, yapılandırmalara erişmeye çalışan kullanıcıya veya hizmet sorumlusuna HDInsight küme Işletmeni, katkıda bulunan veya sahip rolü atayın.

### <a name="why-do-i-see-insufficient-privileges-to-complete-the-operation-when-running-the-azure-cli-command-to-assign-the-hdinsight-cluster-operator-role-to-another-user-or-service-principal"></a>HDInsight kümesi operatörü rolünü başka bir kullanıcıya veya hizmet sorumlusuna atamak için Azure CLı komutunu çalıştırırken "işlemi tamamlamaya yetecek ayrıcalıklara" neden görmüyorum?

Sahip rolüne sahip olmanın yanı sıra, komutu yürüten Kullanıcı veya hizmet sorumlusu, atanan kişinin nesne kimliklerini aramak için yeterli Azure AD iznine sahip olmalıdır. Bu ileti, Azure AD izinlerinin yetersiz olduğunu gösterir. `-–assignee`Bağımsız değişkenini ile değiştirmeyi deneyin `–assignee-object-id` ve adı (ya da yönetilen bir kimlik söz konusu olduğunda asıl kimliği) yerine parametre olarak atanan e ' nın nesne kimliğini sağlayın. Daha fazla bilgi için [az role atama oluşturma belgelerinin](/cli/azure/role/assignment#az_role_assignment_create) isteğe bağlı parametreler bölümüne bakın.

Bu hala işe yaramazsa, doğru izinleri almak için Azure AD yöneticinize başvurun.

### <a name="what-will-happen-if-i-take-no-action"></a>Hiçbir işlem gerçekleşdiğimde ne olur?

3 Eylül 2019 ' den başlayarak, `GET /configurations` `POST /configurations/gateway` çağrılar artık herhangi bir bilgi döndürmez ve `GET /configurations/{configurationName}` çağrı artık depolama hesabı anahtarları veya küme parolası gibi hassas parametreleri döndürmez. Aynı, karşılık gelen SDK yöntemleri ve PowerShell cmdlet 'leri için de geçerlidir.

Yukarıda bahsedilen Visual Studio, VSCode, IntelliJ veya çakışan küreler araçlarından birinin daha eski bir sürümünü kullanıyorsanız, güncelleştirene kadar artık çalışmaz.

Daha ayrıntılı bilgi için, senaryonuza yönelik bu belgenin ilgili bölümüne bakın.
