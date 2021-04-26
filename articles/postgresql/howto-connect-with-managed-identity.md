---
title: Yönetilen kimlik ile bağlanma-PostgreSQL için Azure veritabanı-tek sunucu
description: PostgreSQL için Azure veritabanı ile kimlik doğrulaması için yönetilen kimliği kullanarak bağlanma ve kimlik doğrulama hakkında bilgi edinin
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 05/19/2020
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: beb5930e98a5c5498349dc3aa773423ee5d281bd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792480"
---
# <a name="connect-with-managed-identity-to-azure-database-for-postgresql"></a>Yönetilen Kimlik ile PostgreSQL için Azure Veritabanı'na bağlanma

Bu makalede, bir Azure sanal makinesi (VM) için bir Azure veritabanına PostgreSQL sunucusuna erişmek üzere Kullanıcı tarafından atanan bir kimliğin nasıl kullanılacağı gösterilir. Yönetilen Hizmet Kimlikleri Azure tarafından otomatik olarak yönetilir kodunuza kimlik bilgileri girmenize gerek kalmadan Azure AD kimlik doğrulamasını destekleyen hizmetlerde kimlik doğrulaması yapmanıza olanak tanır. 

Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:
- VM 'nize PostgreSQL için Azure veritabanı sunucusuna erişim izni verme
- Veritabanında, VM 'nin Kullanıcı tarafından atanan kimliğini temsil eden bir kullanıcı oluşturun
- VM kimliğini kullanarak bir erişim belirteci alın ve bunu bir PostgreSQL için Azure veritabanı sunucusu için sorgulamak üzere kullanın
- C# örnek uygulamasında belirteç alımı uygulama

## <a name="prerequisites"></a>Önkoşullar

- Azure kaynakları için yönetilen kimlikler özelliği hakkında bilgi sahibi değilseniz bu [genel bakışı](../../articles/active-directory/managed-identities-azure-resources/overview.md) inceleyin. Azure hesabınız yoksa, devam etmeden önce [ücretsiz bir hesaba kaydolun](https://azure.microsoft.com/free/).
- Gerekli kaynak oluşturma ve rol yönetimini yapmak için hesabınızın uygun kapsamda (aboneliğiniz veya kaynak grubunuz) "sahip" izinleri olması gerekir. Rol atama ile ilgili yardıma ihtiyacınız varsa, [Azure abonelik kaynaklarınıza erişimi yönetmek Için Azure rolleri atama](../../articles/role-based-access-control/role-assignments-portal.md)konusuna bakın.
- Yönetilen kimlik kullanarak veritabanınıza erişmek için kullanmak istediğiniz bir Azure VM 'si (örneğin, Ubuntu Linux çalıştıran) gerekir
- [Azure AD kimlik doğrulaması](howto-configure-sign-in-aad-authentication.md) yapılandırılmış bir PostgreSQL veritabanı sunucusu Için Azure veritabanı gerekir
- C# örneğini izlemek için ilk olarak [c# ile bağlanma](connect-csharp.md) kılavuzunu tamamlayın

## <a name="creating-a-user-assigned-managed-identity-for-your-vm"></a>VM 'niz için Kullanıcı tarafından atanan yönetilen kimlik oluşturma

[Az Identity Create](/cli/azure/identity#az_identity_create) komutunu kullanarak aboneliğinizde bir kimlik oluşturun. Sanal makinenizin üzerinde çalıştığı aynı kaynak grubunu veya farklı bir tane kullanabilirsiniz.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myManagedIdentity
```

Aşağıdaki adımlarda kimliği yapılandırmak için, kimliğin kaynak KIMLIĞINI ve istemci KIMLIĞINI değişkenlerde depolamak üzere [az Identity Show](/cli/azure/identity#az_identity_show) komutunu kullanın.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myManagedIdentity --query id --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show --resource-group myResourceGroup --name myManagedIdentity --query clientId --output tsv)
```

Artık Kullanıcı tarafından atanan kimliği [az VM Identity Assign](/cli/azure/vm/identity#az_vm_identity_assign) komutuyla sanal makineye atayabiliriz:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myVM --identities $resourceID
```

Kurulumu bitirebilmeniz için, sonraki birkaç adımda ihtiyacınız olacak Istemci KIMLIĞI değerini gösterin:

```bash
echo $clientID
```

## <a name="creating-a-postgresql-user-for-your-managed-identity"></a>Yönetilen Kimliğiniz için PostgreSQL kullanıcısı oluşturma

Şimdi, PostgreSQL veritabanınıza Azure AD Yönetici kullanıcısı olarak bağlanın ve aşağıdaki SQL deyimlerini çalıştırın:

```sql
SET aad_validate_oids_in_tenant = off;
CREATE ROLE myuser WITH LOGIN PASSWORD 'CLIENT_ID' IN ROLE azure_ad_user;
```

Yönetilen kimliğin artık Kullanıcı adı ile kimlik doğrulaması yapılırken erişimi vardır `myuser` (değiştirme adıyla değiştirin).

## <a name="retrieving-the-access-token-from-azure-instance-metadata-service"></a>Azure örnek meta verileri hizmetinden erişim belirteci alınıyor

Uygulamanız artık Azure örnek meta veri hizmetinden bir erişim belirteci alabilir ve veritabanını kimlik doğrulaması için kullanabilir.

Bu belirteç alımı, aşağıdaki parametreler için bir HTTP isteği yapılarak `http://169.254.169.254/metadata/identity/oauth2/token` ve geçirerek yapılır:

* `api-version` = `2018-02-01`
* `resource` = `https://ossrdbms-aad.database.windows.net`
* `client_id` = `CLIENT_ID` (daha önce aldığınız)

Bir alan içeren bir JSON sonucunu geri alacaksınız `access_token` . bu uzun metin değeri, veritabanına bağlanırken parola olarak kullanmanız gereken yönetilen kimlik erişim belirtecidir.

Sınama amacıyla, kabukta aşağıdaki komutları çalıştırabilirsiniz. , `curl` `jq` Ve istemcisinin yüklü olması gerektiğini aklınızda bulabilirsiniz `psql` .

```bash
# Retrieve the access token
export PGPASSWORD=`curl -s 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fossrdbms-aad.database.windows.net&client_id=CLIENT_ID' -H Metadata:true | jq -r .access_token`

# Connect to the database
psql -h SERVER --user USER@SERVER DBNAME
```

Artık daha önce yapılandırdığınız veritabanına bağlısınız.

## <a name="connecting-using-managed-identity-in-c"></a>C 'de yönetilen kimlik kullanarak bağlanma #

Bu bölüm, VM 'nin Kullanıcı tarafından atanan yönetilen kimliğini kullanarak bir erişim belirtecinin nasıl alınacağını ve bunu PostgreSQL için Azure veritabanı 'nı çağırmak üzere nasıl kullanacağınızı gösterir. PostgreSQL için Azure veritabanı, Azure AD kimlik doğrulamasını yerel olarak destekleyerek Azure kaynakları için Yönetilen kimlikler kullanılarak alınan erişim belirteçlerini doğrudan kabul edebilir. PostgreSQL bağlantısı oluştururken, erişim belirtecini parola alanına geçirirsiniz.

Bir erişim belirteci kullanarak PostgreSQL bağlantısını açmaya yönelik bir .NET kod örneği aşağıda verilmiştir. Bu kod, VM 'nin Kullanıcı tarafından atanan yönetilen kimliğin uç noktasına erişmek için VM 'de çalıştırılmalıdır. .NET Framework 4,6 veya üzeri ya da .NET Core 2,2 veya üzeri, erişim belirteci metodunu kullanmak için gereklidir. Konak, Kullanıcı, VERITABANı ve CLIENT_ID değerlerini değiştirin.

```csharp
using System;
using System.Net;
using System.IO;
using System.Collections;
using System.Collections.Generic;
using System.Text.Json;
using System.Text.Json.Serialization;
using Npgsql;

namespace Driver
{
    class Script
    {
        // Obtain connection string information from the portal
        //
        private static string Host = "HOST";
        private static string User = "USER";
        private static string Database = "DATABASE";
        private static string ClientId = "CLIENT_ID";

        static void Main(string[] args)
        {
            //
            // Get an access token for PostgreSQL.
            //
            Console.Out.WriteLine("Getting access token from Azure Instance Metadata service...");

            // Azure AD resource ID for Azure Database for PostgreSQL is https://ossrdbms-aad.database.windows.net/
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fossrdbms-aad.database.windows.net&client_id=" + ClientId);
            request.Headers["Metadata"] = "true";
            request.Method = "GET";
            string accessToken = null;

            try
            {
                // Call managed identities for Azure resources endpoint.
                HttpWebResponse response = (HttpWebResponse)request.GetResponse();

                // Pipe response Stream to a StreamReader and extract access token.
                StreamReader streamResponse = new StreamReader(response.GetResponseStream());
                string stringResponse = streamResponse.ReadToEnd();
                var list = JsonSerializer.Deserialize<Dictionary<string, string>>(stringResponse);
                accessToken = list["access_token"];
            }
            catch (Exception e)
            {
                Console.Out.WriteLine("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
                System.Environment.Exit(1);
            }

            //
            // Open a connection to the PostgreSQL server using the access token.
            //
            string connString =
                String.Format(
                    "Server={0}; User Id={1}; Database={2}; Port={3}; Password={4};SSLMode=Prefer",
                    Host,
                    User,
                    Database,
                    5432,
                    accessToken);

            using (var conn = new NpgsqlConnection(connString))
            {
                Console.Out.WriteLine("Opening connection using access token...");
                conn.Open();

                using (var command = new NpgsqlCommand("SELECT version()", conn))
                {

                    var reader = command.ExecuteReader();
                    while (reader.Read())
                    {
                        Console.WriteLine("\nConnected!\n\nPostgres version: {0}", reader.GetString(0));
                    }
                }
            }
        }
    }
}
```

Çalıştırıldığında, bu komut aşağıdakine benzer bir çıktı verecektir:

```
Getting access token from Azure Instance Metadata service...
Opening connection using access token...

Connected!

Postgres version: PostgreSQL 11.6, compiled by Visual C++ build 1800, 64-bit
```

## <a name="next-steps"></a>Sonraki adımlar

* [PostgreSQL Için Azure veritabanı ile Azure Active Directory kimlik doğrulaması](concepts-aad-authentication.md) için genel kavramları gözden geçirin
