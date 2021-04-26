---
title: Azure Active Directory kullanıcılarını HDInsight kümesiyle eşitler
description: Kimliği doğrulanmış kullanıcıları Azure Active Directory bir HDInsight kümesine eşitler.
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/21/2019
ms.openlocfilehash: 2a753a33e9ddf16cc277ab10c1f91049a6382066
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871954"
---
# <a name="synchronize-azure-active-directory-users-to-an-hdinsight-cluster"></a>Azure Active Directory kullanıcılarını HDInsight kümesine eşitleme

[Kurumsal güvenlik paketi (ESP) Içeren HDInsight kümeleri](./domain-joined/hdinsight-security-overview.md) , Azure Active Directory (Azure AD) kullanıcılarıyla güçlü kimlik doğrulaması ve *Azure rol tabanlı erişim denetımı (Azure RBAC)* ilkelerini kullanabilir. Azure AD 'ye kullanıcılar ve gruplar eklerken, kümenize erişmesi gereken kullanıcıları da eşzamanlı hale getirebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Daha önce yapmadıysanız, [Kurumsal güvenlik paketi bir HDInsight kümesi oluşturun](./domain-joined/apache-domain-joined-configure-using-azure-adds.md).

## <a name="add-new-azure-ad-users"></a>Yeni Azure AD kullanıcıları ekleme

Konaklarınızı görüntülemek için, ambarı Web Kullanıcı arabirimini açın. Her düğüm, yeni katılımsız yükseltme ayarlarıyla güncelleştirilir.

1. [Azure Portal](https://portal.azure.com), ESP kümeniz Ile ILIŞKILI Azure AD dizinine gidin.

2. Sol taraftaki menüden **tüm kullanıcılar** ' ı seçin ve ardından **Yeni Kullanıcı**' yı seçin.

    :::image type="content" source="./media/hdinsight-sync-aad-users-to-cluster/users-and-groups-new.png" alt-text="Azure portal kullanıcılar ve gruplar":::

3. Yeni Kullanıcı formunu doldurun. Küme tabanlı izinleri atamak için oluşturduğunuz grupları seçin. Bu örnekte, Yeni Kullanıcı atayabilmeniz için "HiveUsers" adlı bir grup oluşturun. Bir ESP kümesi oluşturmak için [örnek yönergeler](./domain-joined/apache-domain-joined-configure-using-azure-adds.md) , ve olmak üzere iki grup ekleme içerir `HiveUsers` `AAD DC Administrators` .

    :::image type="content" source="./media/hdinsight-sync-aad-users-to-cluster/hdinsight-new-user-form.png" alt-text="Azure portal Kullanıcı bölmesi grupları seçin":::

4. **Oluştur**’u seçin.

## <a name="use-the-apache-ambari-rest-api-to-synchronize-users"></a>Kullanıcıları eşleştirmek için Apache ambarı REST API kullanma

Küme oluşturma işlemi sırasında belirtilen kullanıcı grupları o anda eşitlenir. Kullanıcı eşitleme işlemi saatte bir olmak üzere otomatik olarak gerçekleşir. Kullanıcıları hemen eşitlemesini veya küme oluşturma sırasında belirtilen gruplardan farklı bir grubu eşitlemesini sağlamak için, ambarı REST API kullanın.

Aşağıdaki yöntem REST API ambarı ile GÖNDERI kullanır. Daha fazla bilgi için bkz. [Apache ambarı REST API kullanarak HDInsight kümelerini yönetme](hdinsight-hadoop-manage-ambari-rest-api.md).

1. Kümenize bağlanmak için [SSH komutunu](hdinsight-hadoop-linux-use-ssh-unix.md) kullanın. Aşağıdaki komutu, `CLUSTERNAME` kümenizin adıyla değiştirerek düzenleyin ve ardından şu komutu girin:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Kimlik doğrulamasından sonra aşağıdaki komutu girin:

    ```bash
    curl -u admin:PASSWORD -sS -H "X-Requested-By: ambari" \
    -X POST -d '{"Event": {"specs": [{"principal_type": "groups", "sync_type": "existing"}]}}' \
    "https://CLUSTERNAME.azurehdinsight.net/api/v1/ldap_sync_events"
    ```

    Yanıt aşağıdaki gibi görünmelidir:

    ```json
    {
      "resources" : [
        {
          "href" : "http://<ACTIVE-HEADNODE-NAME>.<YOUR DOMAIN>.com:8080/api/v1/ldap_sync_events/1",
          "Event" : {
            "id" : 1
          }
        }
      ]
    }
    ```

1. Eşitleme durumunu görmek için yeni bir `curl` komut yürütün:

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/ldap_sync_events/1
    ```

    Yanıt aşağıdaki gibi görünmelidir:

    ```json
    {
      "href" : "http://<ACTIVE-HEADNODE-NAME>.YOURDOMAIN.com:8080/api/v1/ldap_sync_events/1",
      "Event" : {
        "id" : 1,
        "specs" : [
          {
            "sync_type" : "existing",
            "principal_type" : "groups"
          }
        ],
        "status" : "COMPLETE",
        "status_detail" : "Completed LDAP sync.",
        "summary" : {
          "groups" : {
            "created" : 0,
            "removed" : 0,
            "updated" : 0
          },
          "memberships" : {
            "created" : 1,
            "removed" : 0
          },
          "users" : {
            "created" : 1,
            "removed" : 0,
            "skipped" : 0,
            "updated" : 0
          }
        },
        "sync_time" : {
          "end" : 1497994072182,
          "start" : 1497994071100
        }
      }
    }
    ```

1. Bu sonuç, durumun **tamamlandığını**, yeni bir kullanıcının oluşturulduğunu ve kullanıcıya bir üyelik atandığını gösterir. Bu örnekte, Kullanıcı Azure AD 'de aynı gruba eklendiğinden, Kullanıcı "HiveUsers" ile eşitlenen LDAP grubuna atanır.

    > [!NOTE]  
    > Önceki yöntem, yalnızca küme oluşturma sırasında etki alanı ayarlarının **Erişim Kullanıcı grubu** özelliğinde BELIRTILEN Azure AD gruplarını eşitler. Daha fazla bilgi için bkz.  [HDInsight kümesi oluşturma](./domain-joined/apache-domain-joined-configure-using-azure-adds.md).

## <a name="verify-the-newly-added-azure-ad-user"></a>Yeni eklenen Azure AD kullanıcısını doğrulama

Yeni Azure AD kullanıcısının eklendiğini doğrulamak için [Apache ambarı Web Kullanıcı arabirimini](hdinsight-hadoop-manage-ambari.md) açın. ' A giderek, ambarı Web Kullanıcı arabirimine erişin **`https://CLUSTERNAME.azurehdinsight.net`** . Küme Yöneticisi Kullanıcı adı ve parolasını girin.

1. Ambarı panosundan **yönetici** menüsünde **ambarı Yönet** ' i seçin.

    :::image type="content" source="./media/hdinsight-sync-aad-users-to-cluster/manage-apache-ambari.png" alt-text="Apache ambarı panosu ambarı yönetme":::

2. Sayfanın sol tarafındaki **Kullanıcı + Grup Yönetimi** menü grubu altında **Kullanıcılar** ' ı seçin.

    :::image type="content" source="./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-menu-item.png" alt-text="HDInsight kullanıcıları ve grupları menüsü":::

3. Yeni kullanıcının kullanıcı tablosu içinde listelenmesi gerekir. Türü yerine olarak ayarlanır `LDAP`  `Local` .

    :::image type="content" source="./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-page.png" alt-text="HDInsight AAD kullanıcıları sayfasına genel bakış":::

## <a name="log-in-to-ambari-as-the-new-user"></a>Yeni Kullanıcı olarak ambarı 'nda oturum açın

Yeni Kullanıcı (veya başka bir etki alanı kullanıcısı), ambarı 'nda oturum açtığında, tam Azure AD Kullanıcı adı ve etki alanı kimlik bilgilerini kullanır.  Ambarı, Azure AD 'de kullanıcının görünen adı olan bir kullanıcı diğer adı görüntüler.
Yeni örnek Kullanıcı Kullanıcı adına sahiptir `hiveuser3@contoso.com` . Bu yeni Kullanıcı, ambarı 'nda farklı şekilde gösterilir, `hiveuser3` ancak Kullanıcı ambarı 'nda olarak oturum açar `hiveuser3@contoso.com` .

## <a name="see-also"></a>Ayrıca bkz.

* [HDInsight 'ta ESP ile Apache Hive ilkelerini yapılandırma](./domain-joined/apache-domain-joined-run-hive.md)
* [HDInsight kümelerini ESP ile yönetme](./domain-joined/apache-domain-joined-manage.md)
* [Kullanıcıları Apache ambarı 'na yetkilendir](hdinsight-authorize-users-to-ambari.md)