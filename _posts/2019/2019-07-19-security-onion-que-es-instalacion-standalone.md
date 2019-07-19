---
layout: post
title:	"Security Onion: Qué es e instalación Standalone"
date:	2019-07-19 03:00:00
categories:
    - blog
tags:
    - BlueTeam
    - Monitoring
    - SecurityOnion
---

La semana pasada di una charla en la Hack & Beers de Vigo y estuve hablando sobre Security Onion. Esto me animó a crear una serie de posts sobre el tema y comenzar de una vez por todas con este blog.

Security Onion es una distribución de linux gratuita, que ofrece múltiples herramientas de monitorización de red. El siguiente gráfico muestra, a alto nivel, cuáles son los servicios que nos ayudarán con dicha monitorización.


## Reduzir o tamanho

Digamos que uma partição sua está sendo menos utilizada que outra e que você quer tirar um pouco de espaço dessa determinada partição e colocar na outra. Pois bem, não esqueça de adaptar os endereços aqui para seu cenário, uma breve explicação:

* `/particao`: endereço no qual a partição que você está usando está montada
* `VolumeGroup`: acredito que o nome já é autoexplicativo
* `LogicalVolume01` e `LogicalVolume02`: os nomes também já se explicam, mas a numeração se refere a que o 01 é o volume lógico que irá diminuir de tamanho, enquanto o 02 o que irá aumentar
* `XXG`: nova quantidade de gigas da partição que teve espaço diminuído

Primeiro, desmonte a partição:

~~~
# umount /particao
~~~

Então, repare o sistema de arquivos:

~~~
# fsck -f /dev/VolumeGroup/LogicalVolume01
~~~

Agora de fato redimensione:

~~~
# resize2fs /dev/VolumeGroup/LogicalVolume01 XXG
~~~

E então, reduza o tamanho do volume lógico para seu novo espaço:

~~~
# lvreduce -L XXG /dev/VolumeGroup/LogicalVolume01
~~~

Agora monte sua partição numa boa:

~~~
# mount /particao
~~~

## Aumentar tamanho

Depois que passei por alguns apertos passei a deixar alguns gigas "sobrando" quando ia pensar no esquemas de partições, justamente por essa facilidade da adição em relação à redução. Pois bem, tendo em vista que já se tem o espaço sobrando, como o procedimento acima mostra como fazer, basta seguir:

~~~
# lvextend -L +10G /dev/mapper/main-root
# resize2fs /dev/mapper/main-root
~~~

E pronto. Agora cheque o tamanho dos discos com `df -h` e seja feliz! :)

Até!

## Links

~~~
[0]: {{ site.url }}{{ site.baseurl }}blog/archlinux-lvm-luks/
~~~

[0]: {{ site.baseurl }}blog/archlinux-lvm-luks/
