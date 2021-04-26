---
title: Azure HDInsight 'ta etki alanı hesapları için SSH erişimini yönetme
description: HDInsight 'ta Azure AD hesapları için SSH erişimini yönetme adımları.
ms.service: hdinsight
ms.topic: how-to
ms.date: 02/14/2020
ms.openlocfilehash: 3fab06f5e269f311f798c096a6465c8c6ce75fc2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946745"
---
# <a name="manage-ssh-access-for-domain-accounts-in-azure-hdinsight"></a>Azure HDInsight 'ta etki alanı hesapları için SSH erişimini yönetme

Güvenli kümeler üzerinde, varsayılan olarak, [Azure AD DS](../../active-directory-domain-services/overview.md) tüm etki alanı kullanıcılarının, baş ve kenar düğümlerine [SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) kullanmasına izin verilir. Bu kullanıcılar, sudoers grubunun bir parçası değildir ve kök erişimi almaz. Küme oluşturma sırasında oluşturulan SSH kullanıcısının kök erişimi olur.

## <a name="manage-access"></a>Erişimi yönetme

Belirli kullanıcılar veya gruplar için SSH erişimini değiştirmek üzere `/etc/ssh/sshd_config` düğümlerin her birinde güncelleştirin.

1. Kümenize bağlanmak için [SSH komutunu](../hdinsight-hadoop-linux-use-ssh-unix.md) kullanın. CLUSTERNAME öğesini kümenizin adıyla değiştirerek aşağıdaki komutu düzenleyin ve ardından şu komutu girin:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. `ssh_confi`G dosyasını açın.

    ```bash
    sudo nano /etc/ssh/sshd_config
    ```

1. `sshd_config`Dosyayı istediğiniz gibi değiştirin. Kullanıcıları belirli gruplar ile kısıtladığınızda, yerel hesaplar bu düğüme SSH kullanamaz. Aşağıda yalnızca söz dizimi örneği verilmiştir:

    ```bash
    AllowUsers useralias1 useralias2

    AllowGroups groupname1 groupname2
    ```

    Ardından Değişiklikleri Kaydet: **CTRL + X**, **Y**, **ENTER**.

1. SSHD 'yi yeniden başlatın.

    ```bash
    sudo systemctl restart sshd
    ```

1. Her düğüm için yukarıdaki adımları tekrarlayın.

## <a name="ssh-authentication-log"></a>SSH kimlik doğrulaması günlüğü

SSH kimlik doğrulaması günlüğü içine yazılır `/var/log/auth.log` . Yerel veya etki alanı hesapları için SSH aracılığıyla herhangi bir oturum açma hatası görürseniz, hatalarda hata ayıklamak için günlüğe gitmeniz gerekir. Genellikle bu sorun belirli kullanıcı hesaplarıyla ilişkili olabilir ve genellikle diğer Kullanıcı hesaplarını veya SSH 'yi varsayılan SSH kullanıcısı (yerel hesap) kullanarak denemek ve ardından bir kinit denemesi yapmak iyi bir uygulamadır.

## <a name="ssh-debug-log"></a>SSH hata ayıklama günlüğü

Ayrıntılı günlüğe kaydetmeyi etkinleştirmek için seçeneğiyle yeniden başlatmanız gerekir `sshd` `-d` . Benzer `/usr/sbin/sshd -d` `sshd` şekilde, ana SSH arka plan programını durdurmanız gerekmiyorsa, özel bir bağlantı noktasında da (2222 gibi) çalıştırabilirsiniz. `-v`Daha fazla günlük (hataların istemci tarafı görünümü) almak IÇIN SSH istemcisiyle seçeneğini de kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight kümelerini Kurumsal Güvenlik Paketi ile yönetme](./apache-domain-joined-manage.md)
* [SSH kullanarak HDInsight 'a (Apache Hadoop) bağlanın](../hdinsight-hadoop-linux-use-ssh-unix.md).
