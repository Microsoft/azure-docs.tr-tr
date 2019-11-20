---
title: Azure Backup Sunucusu v2 sessiz yüklemesi
description: Azure Backup Sunucusu v2 'yi sessizce yüklemek için bir PowerShell betiği kullanın. Bu tür bir yüklemeye katılımsız yükleme de denir.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 1539089e713bcf8e959707c6ff4a608f062a7c00
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172238"
---
# <a name="run-an-unattended-installation-of-azure-backup-server"></a>Katılımsız Azure Backup Sunucusu yüklemesi çalıştırma

Azure Backup Sunucusu katılımsız bir yüklemesini çalıştırmayı öğrenin.

Azure Backup Sunucusu v1 yüklüyorsanız bu adımlar uygulanmaz.

## <a name="install-backup-server"></a>Yedekleme sunucusunu yükler

1. Azure Backup Sunucusu v2 veya üstünü barındıran sunucuda bir metin dosyası oluşturun. (Dosyayı Not defteri 'nde veya başka bir metin düzenleyicisinde oluşturabilirsiniz.) Dosyayı MABSSetup. ini olarak kaydedin.

2. Aşağıdaki kodu MABSSetup. ini dosyasına yapıştırın. Köşeli ayracın içindeki metni (\< \>) ortamınızdaki değerlerle değiştirin. Aşağıdaki metin bir örnektir:

   ```text
   [OPTIONS]
   UserName=administrator
   CompanyName=<Microsoft Corporation>
   SQLMachineName=localhost
   SQLInstanceName=<SQL instance name>
   SQLMachineUserName=administrator
   SQLMachinePassword=<admin password>
   SQLMachineDomainName=<machine domain>
   ReportingMachineName=localhost
   ReportingInstanceName=<reporting instance name>
   SqlAccountPassword=<admin password>
   ReportingMachineUserName=<username>
   ReportingMachinePassword=<reporting admin password>
   ReportingMachineDomainName=<domain>
   VaultCredentialFilePath=<vault credential full path and complete name>
   SecurityPassphrase=<passphrase>
   PassphraseSaveLocation=<passphrase save location>
   UseExistingSQL=<1/0 use or do not use existing SQL>
   ```

3. Dosyayı kaydedin. Sonra, yükleme sunucusundaki yükseltilmiş bir komut isteminde şu komutu girin:

   ```cmd
   start /wait <cdlayout path>/Setup.exe /i  /f <.ini file path>/setup.ini /L <log path>/setup.log
   ```

Yükleme için bu bayrakları kullanabilirsiniz:</br>
**/f**:. ini dosya yolu</br>
**/l**: günlük yolu</br>
**/i**: yükleme yolu</br>
**/x**: yolu kaldır</br>

## <a name="next-steps"></a>Sonraki adımlar

Yedekleme sunucusunu yükledikten sonra, sunucunuzu nasıl hazırlayacağınızı veya bir iş yükünü korumaya nasıl başlayacağınızı öğrenin.

- [Yedekleme sunucusu iş yüklerini hazırlama](backup-azure-microsoft-azure-backup.md)
- [Bir VMware sunucusunu yedeklemek için yedekleme sunucusu kullanma](backup-azure-backup-server-vmware.md)
- [Yedekleme sunucusunu kullanarak yedekleme SQL Server](backup-azure-sql-mabs.md)
- [Yedekleme sunucusuna Modern Yedekleme Alanı Ekle](backup-mabs-add-storage.md)
