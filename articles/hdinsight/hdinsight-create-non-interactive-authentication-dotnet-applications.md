---
title: Etkileşimli olmayan kimlik doğrulaması .NET uygulaması-Azure HDInsight
description: Azure HDInsight 'ta etkileşimli olmayan kimlik doğrulama Microsoft .NET uygulamaları oluşturmayı öğrenin.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: 0781d9fd58e079517b3f3dc8fba06fb448a8fa19
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494927"
---
# <a name="create-a-non-interactive-authentication-net-hdinsight-application"></a>Etkileşimli olmayan bir kimlik doğrulama .NET HDInsight uygulaması oluşturma
Microsoft .NET Azure HDInsight uygulamanızı uygulamanın kendi kimliği (etkileşimli olmayan) altında ya da uygulamanın oturum açan kullanıcısının kimliği altında (etkileşimli) çalıştırabilirsiniz. Bu makalede, Azure 'a bağlanmak ve HDInsight 'ı yönetmek için etkileşimli olmayan bir kimlik doğrulama .NET uygulaması oluşturma işlemlerinin nasıl yapılacağı gösterilir. Etkileşimli uygulamanın bir örneği için bkz. [Azure HDInsight 'A bağlanma](hdinsight-administer-use-dotnet-sdk.md#connect-to-azure-hdinsight). 

Etkileşimli olmayan .NET uygulamanızdan şunları yapmanız gerekir:

* Azure abonelik kiracı KIMLIĞINIZ ( *DIZIN kimliği*olarak da bilinir). Bkz. [KIRACı kimliği Al](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
* Azure Active Directory (Azure AD) uygulama istemci KIMLIĞI. Bkz. [Azure Active Directory uygulama oluşturma](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) ve [uygulama kimliği edinme](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
* Azure AD uygulama gizli anahtarı. Bkz. [uygulama kimlik doğrulama anahtarını al](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).

## <a name="prerequisites"></a>Önkoşullar
* An HDInsight küme. Kullanmaya başlama [öğreticisine](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster)bakın.

## <a name="assign-a-role-to-the-azure-ad-application"></a>Azure AD uygulamasına bir rol atama
Eylemleri gerçekleştirmek için izin vermek üzere Azure AD uygulamanıza bir [rol](../role-based-access-control/built-in-roles.md)atayın. Kapsamı, abonelik, kaynak grubu veya kaynak düzeyinde ayarlayabilirsiniz. İzinler, daha düşük kapsam düzeylerine devralınır. (Örneğin, bir kaynak grubu için okuyucu rolüne bir uygulama eklemek, uygulamanın kaynak grubunu ve içindeki kaynakları okuyabileceği anlamına gelir.) Bu makalede, kapsamı kaynak grubu düzeyinde ayarlarsınız. Daha fazla bilgi için bkz. [Azure abonelik kaynaklarınıza erişimi yönetmek için rol atamalarını kullanma](../role-based-access-control/role-assignments-portal.md).

**Azure AD uygulamasına sahip rolünü eklemek için**

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Soldaki menüden **Kaynak grupları**'nı seçin.
3. Bu makalede daha sonra Hive sorgunuzu çalıştıracağınız HDInsight kümesine sahip kaynak grubunu seçin. Çok sayıda kaynak grubunuz varsa, istediğiniz birini bulmak için filtreyi kullanabilirsiniz.
4. Kaynak grubu menüsünde, **erişim denetimi (IAM)** seçeneğini belirleyin.
5. Geçerli rol atamalarını görmek için **rol atamaları** sekmesini seçin.
6. Sayfanın üst kısmında **rol ataması Ekle**' yi seçin.
7. Azure AD uygulamanıza sahip rolünü eklemek için yönergeleri izleyin. Rolü başarıyla ekledikten sonra uygulama, sahip rolü altında listelenir. 

## <a name="develop-an-hdinsight-client-application"></a>HDInsight istemci uygulaması geliştirme

1. Bir C# konsol uygulaması oluşturun.
2. Aşağıdaki [NuGet](https://www.nuget.org/) paketlerini ekleyin:

        Install-Package Microsoft.Azure.Common.Authentication -Pre
        Install-Package Microsoft.Azure.Management.HDInsight -Pre
        Install-Package Microsoft.Azure.Management.Resources -Pre

3. Aşağıdaki kodu çalıştırın:

    ```csharp
    using System;
    using System.Security;
    using Microsoft.Azure;
    using Microsoft.Azure.Common.Authentication;
    using Microsoft.Azure.Common.Authentication.Factories;
    using Microsoft.Azure.Common.Authentication.Models;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.HDInsight;
    
    namespace CreateHDICluster
    {
        internal class Program
        {
            private static HDInsightManagementClient _hdiManagementClient;
    
            private static Guid SubscriptionId = new Guid("<Enter your Azure subscription ID>");
            private static string tenantID = "<Enter your tenant ID (also called directory ID)>";
            private static string applicationID = "<Enter your application ID>";
            private static string secretKey = "<Enter the application secret key>";
    
            private static void Main(string[] args)
            {
                var key = new SecureString();
                foreach (char c in secretKey) { key.AppendChar(c); }
    
                var tokenCreds = GetTokenCloudCredentials(tenantID, applicationID, key);
                var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
    
                var resourceManagementClient = new ResourceManagementClient(subCloudCredentials);
                resourceManagementClient.Providers.Register("Microsoft.HDInsight");
    
                _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
    
                var results = _hdiManagementClient.Clusters.List();
                foreach (var name in results.Clusters)
                {
                    Console.WriteLine("Cluster Name: " + name.Name);
                    Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
                    Console.WriteLine("\t Cluster location: " + name.Location);
                    Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
                }
                Console.WriteLine("Press Enter to continue");
                Console.ReadLine();
            }
    
            /// Get the access token for a service principal and provided key.          
            public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
            {
                var authFactory = new AuthenticationFactory();
                var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
                var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
                var accessToken =
                    authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
    
                return new TokenCloudCredentials(accessToken);
            }
    
            public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
            {
                return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
            }
        }
    }
    ```


## <a name="next-steps"></a>Sonraki adımlar
* [Azure portal Azure Active Directory bir uygulama ve hizmet sorumlusu oluşturun](../active-directory/develop/howto-create-service-principal-portal.md).
* [Azure Resource Manager ile hizmet sorumlusu için kimlik doğrulaması](../active-directory/develop/howto-authenticate-service-principal-powershell.md)yapmayı öğrenin.
* [Azure rol tabanlı Access Control (RBAC)](../role-based-access-control/role-assignments-portal.md)hakkında bilgi edinin.
