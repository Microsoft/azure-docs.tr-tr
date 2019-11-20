---
title: Ayrıntılı rol tabanlı erişim Azure HDInsight küme yapılandırması
description: HDInsight küme yapılandırmalarına yönelik ayrıntılı rol tabanlı erişime geçiş kapsamında gereken değişiklikler hakkında bilgi edinin.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/22/2019
ms.openlocfilehash: ea8e1565a5ebe4e5cb40049fbfcb329feb83bdda
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498198"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Küme yapılandırmaları için ayrıntılı rol tabanlı erişime geçme

Hassas bilgileri almak için daha ayrıntılı rol tabanlı erişimi desteklemeye yönelik bazı önemli değişiklikler sunuyoruz. Bu değişikliklerin bir parçası olarak, [Etkilenen varlıkların/senaryolardan](#am-i-affected-by-these-changes)birini kullanıyorsanız bazı eylemler **3 Eylül 2019 '** de gerekebilir.

## <a name="what-is-changing"></a>Ne değişiyor?

Daha önce, kullanıcılar `*/read` izni olan herkes tarafından kullanılabilir oldukları için sahip, katkıda bulunan veya Reader [RBAC rollerinin](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)bulunduğu küme kullanıcıları tarafından HDInsight API 'si aracılığıyla elde edilebilir. Gizli dizileri, bir kullanıcının rolünden izin verilmelidir daha fazla yükseltilmiş erişim elde etmek için kullanılabilecek değerler olarak tanımlanır. Bunlar, küme ağ geçidi HTTP kimlik bilgileri, depolama hesabı anahtarları ve veritabanı kimlik bilgileri gibi değerleri içerir.

3 Eylül 2019 ' den başlayarak, bu gizli bilgilere erişmek `Microsoft.HDInsight/clusters/configurations/action` iznini gerektirir, yani artık okuyucu rolüne sahip kullanıcılar tarafından erişilemez. Bu izne sahip roller katkıda bulunan, sahip ve yeni HDInsight küme Işletmeni rolü (aşağıda daha fazla).

Ayrıca, katkıda bulunan veya sahip 'in yönetim izinleri verilmeden gizli dizileri alabilecek yeni bir [HDInsight küme işletmeni](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) rolü sunuyoruz. Özetlemek için:

| Rol                                  | Virüslü                                                                                       | Ileri git       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Okuyucu                                | -Gizli dizileri de içeren okuma erişimi                                                                   | -Gizli dizileri **hariç** okuma erişimi |           |   |   |
| HDInsight küme operatörü<br>(Yeni rol) | Yok                                                                                              | -Gizli dizileri dahil okuma/yazma erişimi         |   |   |
| Katılımcı                           | -Gizli dizileri dahil okuma/yazma erişimi<br>-Tüm Azure kaynakları türlerini oluşturun ve yönetin.     | Değişiklik yok |
| Sahip                                 | -Gizlilikler dahil olmak üzere okuma/yazma erişimi<br>-Tüm kaynaklara tam erişim<br>-Başkalarına erişim yetkisi verme | Değişiklik yok |

Bir kullanıcıya HDInsight kümesi operatörü rolü atamasını ekleme/yazma erişimi sağlamak üzere bir kullanıcıya ekleme hakkında daha fazla bilgi için, bkz. [bir kullanıcıya HDInsight küme işletmeni rol atamasını ekleme](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user).

## <a name="am-i-affected-by-these-changes"></a>Bu değişikliklerden etkilendim mıyım?

Aşağıdaki varlıklar ve senaryolar etkilenir:

- [API](#api): `/configurations` veya `/configurations/{configurationName}` uç noktalarını kullanan kullanıcılar.
- Visual Studio Code sürüm 1.1.1 veya altındaki [Azure HDInsight araçları](#azure-hdinsight-tools-for-visual-studio-code) .
- [Azure Toolkit for IntelliJ](#azure-toolkit-for-intellij) Version 3.20.0 veya below.
- [Visual Studio 'nun 2.3.9000.1 sürümü için Azure Data Lake ve Stream Analytics araçları](#azure-data-lake-and-stream-analytics-tools-for-visual-studio) .
- [Azure Toolkit for Eclipse](#azure-toolkit-for-eclipse) Version 3.15.0 veya below.
- [.NET için SDK](#sdk-for-net)
    - [Sürüm 1. x veya 2. x](#versions-1x-and-2x): `GetClusterConfigurations`, `GetConnectivitySettings`, `ConfigureHttpSettings`, `EnableHttp` veya `DisableHttp` yöntemlerini kullanan kullanıcılar ConfigurationsOperationsExtensions sınıfından.
    - [sürüm 3. x ve yukarı](#versions-3x-and-up): `ConfigurationsOperationsExtensions` sınıfından `Get`, `Update`, `EnableHttp`veya `DisableHttp` yöntemlerini kullanan kullanıcılar.
- [Python Için SDK](#sdk-for-python): `ConfigurationsOperations` sınıfından `get` veya `update` yöntemlerini kullanan kullanıcılar.
- [Java Için SDK](#sdk-for-java): `ConfigurationsInner` sınıfından `update` veya `get` yöntemlerini kullanan kullanıcılar.
- [Go Için SDK](#sdk-for-go): `ConfigurationsClient` struct 'dan `Get` veya `Update` yöntemlerini kullanan kullanıcılar.
- [Az. HDInsight PowerShell](#azhdinsight-powershell) in Version 2.0.0.
Senaryonuzun geçiş adımlarını görmek için aşağıdaki bölümlere bakın (ya da yukarıdaki bağlantıları kullanın).

### <a name="api"></a>API

Aşağıdaki API 'Ler değiştirilecek veya kullanım dışı bırakılacak:

- [**Get/configurations/{configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (hassas bilgiler kaldırıldı)
    - Daha önce ayrı yapılandırma türlerini (parolalar dahil) almak için kullanılır.
    - 3 Eylül 2019 ' den itibaren bu API çağrısı artık gizli dizileri olan ayrı yapılandırma türleri döndürüyor. Gizli dizileri de içeren tüm yapılandırmaların elde edilmesi için yeni GÖNDERI/yapılandırma çağrısını kullanın. Yalnızca ağ geçidi ayarlarını almak için yeni POST/getGatewaySettings çağrısını kullanın.
- [ **/Configurations al**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (kullanım dışı)
    - Daha önce tüm yapılandırmaların (gizli diziler dahil) alınması için kullanılır
    - 3 Eylül 2019 ' den itibaren bu API çağrısı kullanım dışı bırakılacak ve artık desteklenmeyecektir. İleri doğru olan tüm yapılandırmaların elde etmek için yeni POST/Configurations çağrısını kullanın. Gizli parametrelere sahip yapılandırmaların elde edilmesi için, GET/configurations/{configurationName} çağrısını kullanın.
- [**Post/configurations/{configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings) (kullanım dışı)
    - Daha önce Ağ Geçidi kimlik bilgilerini güncelleştirmek için kullanılır.
    - 3 Eylül 2019 ' den itibaren bu API çağrısı kullanım dışı bırakılacak ve artık desteklenmeyecektir. Bunun yerine yeni POST/updateGatewaySettings komutunu kullanın.

Aşağıdaki değiştirme API 'Leri eklendi:</span>

- [**GÖNDERI/yapılandırma**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - Gizli dizileri dahil tüm yapılandırmaların elde edilmesi için bu API 'yi kullanın.
- [**POST/getGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - Ağ Geçidi ayarlarını almak için bu API 'YI kullanın.
- [ **/UpdateGatewaySettings SONRASı**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - Ağ Geçidi ayarlarını (Kullanıcı adı ve/veya parola) güncelleştirmek için bu API 'YI kullanın.

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Visual Studio Code için Azure HDInsight araçları

Sürüm 1.1.1 veya aşağıda kullanıyorsanız, kesintileri önlemek için [Visual Studio Code Için Azure HDInsight araçları 'nın en son sürümüne](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) güncelleştirin.

### <a name="azure-toolkit-for-intellij"></a>IntelliJ için Azure Araç Takımı

Sürüm 3.20.0 veya aşağıdaki sürümü kullanıyorsanız kesintileri önlemek için [Azure Toolkit for IntelliJ eklentisinin en son sürümüne](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) güncelleştirin.

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>Visual Studio için Azure Data Lake ve Stream Analytics araçları

Kesintiden kaçınmak üzere [Visual Studio için Azure Data Lake ve Stream Analytics araçları](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview) sürümüne güncelleştirin.  Güncelleştirme hakkında yardım için, [Visual Studio için belgelerimizi, güncelleştirme Data Lake araçları](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-visual-studio-tools-get-started#update-data-lake-tools-for-visual-studio)' nı inceleyin.

### <a name="azure-toolkit-for-eclipse"></a>Eclipse için Azure Araç Seti

Sürüm 3.15.0 veya aşağıdaki sürümü kullanıyorsanız kesintileri önlemek için [Azure Toolkit for Eclipse en son sürümüne](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) güncelleştirin.

### <a name="sdk-for-net"></a>.NET için SDK

#### <a name="versions-1x-and-2x"></a>Sürüm 1. x ve 2. x

.NET için HDInsight SDK 'sının [2.1.0 sürümüne](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) güncelleştirin. Aşağıdaki değişikliklerden etkilenen bir yöntem kullanıyorsanız, minimum kod değişiklikleri gerekebilir:

- `ClusterOperationsExtensions.GetClusterConfigurations` artık depolama anahtarları (çekirdek-site) veya HTTP kimlik bilgileri (Gateway) gibi **hassas parametreleri döndürmez** .
    - Gizli parametreler dahil tüm yapılandırmaların alınması için `ClusterOperationsExtensions.ListConfigurations` kullanın.  ' Reader ' rolüne sahip kullanıcıların bu yöntemi kullanabilediğine unutmayın. Bu, kullanıcıların bir küme için hassas bilgilere erişebileceği ayrıntılı denetim sağlar.
    - Yalnızca HTTP ağ geçidi kimlik bilgilerini almak için `ClusterOperationsExtensions.GetGatewaySettings`kullanın.

- `ClusterOperationsExtensions.GetConnectivitySettings` artık kullanım dışıdır ve `ClusterOperationsExtensions.GetGatewaySettings`ile değiştirilmiştir.

- `ClusterOperationsExtensions.ConfigureHttpSettings` artık kullanım dışıdır ve `ClusterOperationsExtensions.UpdateGatewaySettings`ile değiştirilmiştir.

- `ConfigurationsOperationsExtensions.EnableHttp` ve `DisableHttp` artık kullanım dışıdır. HTTP artık her zaman etkindir, bu nedenle bu yöntemlere artık gerek yoktur.

#### <a name="versions-3x-and-up"></a>Sürüm 3. x ve yukarı

.NET için HDInsight SDK 'nın 5.0.0 veya sonraki bir [sürümüyle](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) güncelleştirin. Aşağıdaki değişikliklerden etkilenen bir yöntem kullanıyorsanız, minimum kod değişiklikleri gerekebilir:

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet) artık depolama anahtarları (çekirdek-site) veya http kimlik bilgileri (Gateway) gibi **hassas parametreleri döndürmez** .
    - Gizli parametreler dahil tüm yapılandırmaların alınması için [`ConfigurationOperationsExtensions.List`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list?view=azure-dotnet) kullanın.  ' Reader ' rolüne sahip kullanıcıların bu yöntemi kullanabilediğine unutmayın. Bu, kullanıcıların bir küme için hassas bilgilere erişebileceği ayrıntılı denetim sağlar. 
    - Yalnızca HTTP ağ geçidi kimlik bilgilerini almak için [`ClusterOperationsExtensions.GetGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings?view=azure-dotnet)kullanın. 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet) artık kullanım dışıdır ve [`ClusterOperationsExtensions.UpdateGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings?view=azure-dotnet)ile değiştirilmiştir. 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet) ve [`DisableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) artık kullanım dışıdır. HTTP artık her zaman etkindir, bu nedenle bu yöntemlere artık gerek yoktur.

### <a name="sdk-for-python"></a>Python için SDK

Python için HDInsight SDK 'sının [Version 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) veya üzeri sürümüne güncelleştirin. Aşağıdaki değişikliklerden etkilenen bir yöntem kullanıyorsanız, minimum kod değişiklikleri gerekebilir:

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-) artık depolama anahtarları (çekirdek-site) veya http kimlik bilgileri (Gateway) gibi **hassas parametreleri döndürmez** .
    - Gizli parametreler dahil tüm yapılandırmaların alınması için [`ConfigurationsOperations.list`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) kullanın.  ' Reader ' rolüne sahip kullanıcıların bu yöntemi kullanabilediğine unutmayın. Bu, kullanıcıların bir küme için hassas bilgilere erişebileceği ayrıntılı denetim sağlar. 
    - Yalnızca HTTP ağ geçidi kimlik bilgilerini almak için [`ClusterOperations.get_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-)kullanın.
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#update-resource-group-name--cluster-name--configuration-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-) artık kullanım dışıdır ve [`ClusterOperations.update_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-)ile değiştirilmiştir.

### <a name="sdk-for-java"></a>Java Için SDK

Java için HDInsight SDK 'sının [Version 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/1.0.0/jar) veya üzeri sürümüne güncelleştirin. Aşağıdaki değişikliklerden etkilenen bir yöntem kullanıyorsanız, minimum kod değişiklikleri gerekebilir:

- [`ConfigurationsInner.get`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.get) artık depolama anahtarları (çekirdek-site) veya http kimlik bilgileri (Gateway) gibi **hassas parametreleri döndürmez** .
    - Gizli parametreler dahil tüm yapılandırmaların alınması için [`ConfigurationsInner.list`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.configurationsinner.list?view=azure-java-stable) kullanın.  ' Reader ' rolüne sahip kullanıcıların bu yöntemi kullanabilediğine unutmayın. Bu, kullanıcıların bir küme için hassas bilgilere erişebileceği ayrıntılı denetim sağlar. 
    - Yalnızca HTTP ağ geçidi kimlik bilgilerini almak için [`ClustersInner.getGatewaySettings`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.getgatewaysettings?view=azure-java-stable)kullanın.
- [`ConfigurationsInner.update`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.update) artık kullanım dışıdır ve [`ClustersInner.updateGatewaySettings`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.updategatewaysettings?view=azure-java-stable)ile değiştirilmiştir.

### <a name="sdk-for-go"></a>Go Için SDK

Go için HDInsight SDK 'sının [Version 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) veya üzeri sürümüne güncelleştirin. Aşağıdaki değişikliklerden etkilenen bir yöntem kullanıyorsanız, minimum kod değişiklikleri gerekebilir:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get) artık depolama anahtarları (çekirdek-site) veya http kimlik bilgileri (Gateway) gibi **hassas parametreleri döndürmez** .
    - Gizli parametreler dahil tüm yapılandırmaların alınması için [`ConfigurationsClient.list`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List) kullanın.  ' Reader ' rolüne sahip kullanıcıların bu yöntemi kullanabilediğine unutmayın. Bu, kullanıcıların bir küme için hassas bilgilere erişebileceği ayrıntılı denetim sağlar. 
    - Yalnızca HTTP ağ geçidi kimlik bilgilerini almak için [`ClustersClient.get_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings)kullanın.
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update) artık kullanım dışıdır ve [`ClustersClient.update_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings)ile değiştirilmiştir.

### <a name="azhdinsight-powershell"></a>Az. HDInsight PowerShell
Kesintileri önlemek için [az PowerShell Version 2.0.0](https://www.powershellgallery.com/packages/Az) veya üzeri bir sürüme güncelleştirin.  Bu değişikliklerden etkilenen bir yöntem kullanıyorsanız, minimum kod değişiklikleri gerekebilir.
- `Grant-AzHDInsightHttpServicesAccess` artık kullanım dışıdır ve yeni `Set-AzHDInsightGatewayCredential` cmdlet 'i ile değiştirilmiştir.
- `Get-AzHDInsightJobOutput`, depolama anahtarına ayrıntılı rol tabanlı erişimi destekleyecek şekilde güncelleştirilmiştir.
    - HDInsight Küme Operatörü, Katkıda Bulunan veya Sahip rolleri olan kullanıcılar etkilenmeyecek.
    - Yalnızca okuyucu rolüne sahip kullanıcıların `DefaultStorageAccountKey` parametresini açıkça belirtmesi gerekecektir.
- `Revoke-AzHDInsightHttpServicesAccess` artık kullanım dışıdır. HTTP artık her zaman etkindir, bu nedenle bu cmdlet artık gerekli değildir.
 Bkz [. az. ](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight)Daha fazla ayrıntı Için HDInsight geçiş kılavuzu.

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>Kullanıcıya HDInsight küme Işletmeni rolü atamasını ekleme

[Sahip](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) rolüne sahip bir Kullanıcı, gizli HDInsight küme yapılandırma değerlerine (küme ağ geçidi kimlik bilgileri ve depolama hesabı anahtarları gibi) okuma/yazma erişimi olmasını Istediğiniz kullanıcılara [HDInsight küme işletmeni](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) rolünü atayabilir.

### <a name="using-the-azure-cli"></a>Azure CLI kullanma

Bu rol atamasını eklemenin en kolay yolu, Azure CLı 'de `az role assignment create` komutu kullanmaktır.

> [!NOTE]
> Bu komut, yalnızca bu izinleri verebileceği için sahip rolüne sahip bir kullanıcı tarafından çalıştırılmalıdır. `--assignee`, HDInsight küme Işletmeni rolünü atamak istediğiniz kullanıcının hizmet sorumlusu veya e-posta adresi adıdır. Yetersiz izinler hatası alırsanız aşağıdaki SSS bölümüne bakın.

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

Alternatif olarak, bir kullanıcıya HDInsight küme operatörü rolü atamasını eklemek için Azure portal kullanabilirsiniz. [RBAC kullanarak Azure kaynaklarına erişimi yönetme ve Azure Portal bir rol ataması ekleme](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment)hakkında bilgi için belgelere bakın.

## <a name="faq"></a>SSS

### <a name="why-am-i-seeing-a-403-forbidden-response-after-updating-my-api-requests-andor-tool"></a>API isteklerimi ve/veya araçlarından güncelleştirdikten sonra neden 403 (yasak) yanıtını görüyorum?

Küme yapılandırmalarına artık ayrıntılı rol tabanlı erişim denetimi sonrası ve bunlara erişmek için `Microsoft.HDInsight/clusters/configurations/*` izinlerinin olması gerekir. Bu izni almak için, yapılandırmalara erişmeye çalışan kullanıcıya veya hizmet sorumlusuna HDInsight küme Işletmeni, katkıda bulunan veya sahip rolü atayın.

### <a name="why-do-i-see-insufficient-privileges-to-complete-the-operation-when-running-the-azure-cli-command-to-assign-the-hdinsight-cluster-operator-role-to-another-user-or-service-principal"></a>HDInsight kümesi operatörü rolünü başka bir kullanıcıya veya hizmet sorumlusuna atamak için Azure CLı komutunu çalıştırırken "işlemi tamamlamaya yetecek ayrıcalıklara" neden görmüyorum?

Sahip rolüne sahip olmanın yanı sıra, komutu yürüten Kullanıcı veya hizmet sorumlusu, atanan kişinin nesne kimliklerini aramak için yeterli AAD iznine sahip olmalıdır. Bu ileti, AAD izinlerinin yetersiz olduğunu gösterir. `-–assignee` bağımsız değişkenini `–assignee-object-id` ile değiştirmeyi deneyin ve adı (ya da yönetilen bir kimlik olması durumunda asıl KIMLIĞI) yerine parametre olarak atanan ' ın nesne KIMLIĞINI girin. Daha fazla bilgi için [az role atama oluşturma belgelerinin](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) isteğe bağlı parametreler bölümüne bakın.

Bu hala işe yaramazsa, doğru izinleri almak için AAD yöneticinize başvurun.

### <a name="what-will-happen-if-i-take-no-action"></a>Hiçbir işlem gerçekleşdiğimde ne olur?

3 Eylül 2019 ' den başlayarak `GET /configurations` ve `POST /configurations/gateway` çağrılar artık herhangi bir bilgi döndürmez ve `GET /configurations/{configurationName}` çağrısı artık depolama hesabı anahtarları veya küme parolası gibi hassas parametreleri döndürmez. Aynı, karşılık gelen SDK yöntemleri ve PowerShell cmdlet 'leri için de geçerlidir.

Yukarıda bahsedilen Visual Studio, VSCode, IntelliJ veya çakışan küreler araçlarından birinin daha eski bir sürümünü kullanıyorsanız, güncelleştirene kadar artık çalışmaz.

Daha ayrıntılı bilgi için, senaryonuza yönelik bu belgenin ilgili bölümüne bakın.