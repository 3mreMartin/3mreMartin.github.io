---
title: "Azure VM üzerindeki Geçici Diskler Ne İşe Yarar ?"
classes: wide
author_profile: true
categories:
  - Azure
tags:
  - Azure Compute
---

Azure üzerinde oluşturmuş olduğunuz Windows yada Linux her sanal makinaya otomatik olarak bir Sistem Diski ve bir de Temporary (Geçici) disk atanır. Temporary diskler Linux üzerinde varsayılan olarak /dev/sdb1, Windows üzerinde ise “D” drive olarak set edilir. 

Windows üzerindeki D drive’nın bazı durumlarda yanlış amaçlar için kullanıldığıda olur .Bu durum gelende sanal makinamız restart olduktan sonra datalarımız kaybolunca anlaşılır. :) Azure üzerinde bulunan sanal makinanızın Temporary diskin içerisine göz attığınızda bu durum için size uyaran DATALOSS_WARNING_README adında bir text file’da  göreceksiniz.

<img src="https://github.com/martinemre/martinemre.github.io/blob/main/assets/images/azure-vm-temp-disks.png?raw=true" width="100%" height="100%" />

Temporary diskler sanal makinanızın üzerinde koştuğu fiziksel host’lar (hypervisor) üzerinde bulunan  Non persistent  yani kalıcı olmayan disklerdir. Kalıcı olmayan çünkü üzerine yazılan veri sanal makinanın restart olması vs. gibi durumlarda silinir. Sistem diskleri ve sonradan eklemiş olduğunuz data diskleri ise Persistent disk yani kalıcı diskler olarak bilinirler. Persistent diskler Azure Storage içerisinde barındırılırlar.

Non persistent diskler direk olarak fiziksel makine üzerinde bulunduklarından Persistent disklere göre daha yüksek IOPS daha düşük latency’ye sahiptirler. Yüksek throughput gerektiren TempDB operasyonları için  Temporary diski SSD olan olan (D, D2 ve G Serisi) sanal makinaların  D:\ drive’ını kullanabilirsiniz. Altını çizerek tekrardan söylüyorum sadece ve sadece TempDB için :)

<img src="https://github.com/martinemre/martinemre.github.io/blob/main/assets/images/azure-vm-temp-disks-2.png?raw=true" width="100%" height="100%" />

Farklı sebeplerden dolayı (Donanımsal Arızalar vs.) sanal makinanızın farklı bir host üzerine taşınması gerekebilir bu durumda Azure Storage içerisindeki OS diskiniz kullanılarak sanal makinanız tekrardan yeni host üzerinde oluşturulur. Temporary disk içerisindeki verileriniz ise silinerek sanal makinanıza yeni bir temporary disk atanır. Ayrıca sanal makinanın resize edilmesi yada restart edilmesi sonucuda  temporary diskiniz içerisindeki verileriniz kaybolur. Temporary disk boyunları sanal makinanızın size’ına bağlı olarak değişir ve ekstra bir ücreti yoktur.

## Peki nedir bu Geçici Diskin amacı ? 

Temporary Diskler sanal ram belleği olarak bilinen pagefile.sys dosyasını barındırmak için kullanıırlar. 

<img src="https://github.com/martinemre/martinemre.github.io/blob/main/assets/images/azure-vm-temp-disks-3.png?raw=true" width="100%" height="100%" />

Dilerseniz Temporary diskinize farklı bir harf atayabilirsiniz ayrıca ARM template kullanarak farklı harf atamalarına sahip sanal makinalar oluşturabilirsiniz.

Daha fazlası için:
https://docs.microsoft.com/en-us/azure/virtual-machines/windows/change-drive-letter
https://github.com/perktime/MoveAzureTempDrive
