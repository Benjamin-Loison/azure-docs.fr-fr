---
title: Création et téléchargement d'un disque dur virtuel SUSE Linux dans Azure
description: Apprenez à créer et à télécharger un disque dur virtuel (VHD) Azure contenant un système d'exploitation SUSE Linux.
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/01/2020
ms.author: danis
ms.openlocfilehash: 30246166650cddc1c89fa3a2ca377c163fb5847b
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697749"
---
# <a name="prepare-a-sles-or-opensuse-leap-virtual-machine-for-azure"></a>Préparation d’une machine virtuelle SLES ou openSUSE Leap pour Azure

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Groupes identiques flexibles **S’applique à :** :heavy_check_mark: Groupes identiques uniformes

Cet article suppose que vous avez déjà installé un système d’exploitation SUSE ou openSUSE Leap Linux sur un disque dur virtuel. Il existe de multiples outils dédiés à la création de fichiers .vhd, comme la solution de virtualisation Hyper-V. Pour obtenir des instructions, consultez la page [Installation du rôle Hyper-V et configuration d'une machine virtuelle](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

## <a name="sles--opensuse-leap-installation-notes"></a>Notes d’installation SLES/openSUSE Leap
* Consultez également les [Notes générales d’installation sous Linux](create-upload-generic.md#general-linux-installation-notes) pour obtenir d’autres conseils sur la préparation de Linux pour Azure.
* Azure ne prend pas en charge le format VHDX, seulement le **VHD fixe**.  Vous pouvez convertir le disque au format VHD à l'aide de Hyper-V Manager ou de l’applet de commande convert-vhd.
* Lors de l’installation du système Linux, il est recommandé d’utiliser les partitions standard plutôt que LVM (qui est souvent le choix par défaut pour de nombreuses installations). Ceci permettra d'éviter les conflits de noms avec des machines virtuelles clonées, notamment si un disque de système d'exploitation doit être relié à une autre machine virtuelle pour la dépanner. La technique [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) ou [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) peut être utilisée sur les disques de données, le cas échéant.
* Ne configurez pas une partition d'échange sur le disque du système d'exploitation. L'agent Linux est configurable pour créer un fichier d'échange sur le disque de ressources temporaire.  Les étapes ci-dessous fournissent plus d’informations à ce sujet.
* Tous les VDH sur Azure doivent avoir une taille virtuelle alignée à 1 Mo. Lors de la conversion d’un disque brut vers VDH, vous devez vous assurer que la taille du disque brut est un multiple de 1 Mo avant la conversion. Pour plus d’informations, consultez [Notes d’installation sur Linux](create-upload-generic.md#general-linux-installation-notes).

## <a name="use-suse-studio"></a>Utilisation de SUSE Studio
[SUSE Studio](https://studioexpress.opensuse.org/) peut facilement créer et gérer vos images SLES et openSUSE Leap pour Azure et Hyper-V. Cette approche est recommandée pour personnaliser vos propres images SLES et openSUSE Leap.

Pour vous éviter de créer votre propre disque dur virtuel, SUSE publie également des images BYOS (« 	Apportez votre propre abonnement ») pour SLES sur [VM Depot](https://www.microsoft.com/research/wp-content/uploads/2016/04/using-and-contributing-vms-to-vm-depot.pdf).

## <a name="prepare-suse-linux-enterprise-server-for-azure"></a>Préparation de SUSE Linux Enterprise Server pour Azure
1. Dans le panneau central de Hyper-V Manager, sélectionnez la machine virtuelle.
2. Cliquez sur **Connect** pour ouvrir la fenêtre de la machine virtuelle.
3. Enregistrez votre système SUSE Linux Enterprise pour lui permettre de télécharger les mises à jour et d’installer des packages.
4. Mettez à jour le système avec les derniers correctifs :

    ```console
    # sudo zypper update
    ```
    
5. Installer l’agent Linux Azure et cloud-init

    ```console
    # SUSEConnect -p sle-module-public-cloud/15.2/x86_64  (SLES 15 SP2)
    # sudo zypper refresh
    # sudo zypper install python-azure-agent
    # sudo zypper install cloud-init
    ```

6. Activer le lancement de waagent et cloud-init au démarrage

    ```console
    # sudo chkconfig waagent on
    # systemctl enable cloud-init-local.service
    # systemctl enable cloud-init.service
    # systemctl enable cloud-config.service
    # systemctl enable cloud-final.service
    # systemctl daemon-reload
    # cloud-init clean
    ```

7. Mise à jour de waagent et configuration de cloud-init

    ```console
    # sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    # sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf

    # sudo sh -c 'printf "datasource:\n  Azure:" > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg'
    # sudo sh -c 'printf "reporting:\n  logging:\n    type: log\n  telemetry:\n    type: hyperv" > /etc/cloud/cloud.cfg.d/10-azure-kvp.cfg'
    ```

8. Modifiez le fichier /etc/default/grub pour vous assurer que les journaux de la console sont envoyés au port série, puis mettez à jour le fichier de configuration principal avec grub2-mkconfig -o /boot/grub2/grub.cfg

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```
    Ceci permet d'assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour l'assistance d'Azure.
    
9. Vérifier que le fichier/etc/fstab référence le disque à l’aide de son UUID (by-uuid)
         
10. Modifiez les règles udev pour éviter la génération de règles statiques pour les interfaces Ethernet. Ces règles peuvent causer des problèmes lors du clonage d’une machine virtuelle dans Microsoft Azure ou Hyper-V :

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```
   
11. Il est recommandé de modifier le fichier « /etc/sysconfig/network/dhcp » et le paramètre `DHCLIENT_SET_HOSTNAME` comme suit :

    ```config
    DHCLIENT_SET_HOSTNAME="no"
    ```

12. Sous « /etc/sudoers », commentez ou supprimez les lignes suivantes (si elles sont présentes) :

    ```text
    Defaults targetpw   # ask for the password of the target user i.e. root
    ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
    ```

13. Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu. C'est généralement le cas par défaut.

14. Permutez la configuration.
 
    Ne créez pas d’espace d’échange sur le disque du système d’exploitation.

    Auparavant, l’agent Linux Azure était utilisé pour configurer automatiquement un espace d’échange à l’aide du disque de ressources local attaché à la machine virtuelle après le provisionnement de cette dernière sur Azure. Toutefois, cela est désormais géré par cloud-init, et vous **ne devez pas** utiliser l’agent Linux pour formater le disque de ressources. Créez le fichier d’échange et modifiez les paramètres suivants dans `/etc/waagent.conf` de manière appropriée :

    ```console
    # sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    # sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
    ```

    Si vous souhaitez monter, formater et créer un échange, vous avez deux possibilités :
    * Transmettez ces paramètres dans une configuration cloud-init chaque fois que vous créez une machine virtuelle.
    * Utilisez une directive cloud-init intégrée à l’image qui effectuera cette opération chaque fois que la machine virtuelle sera créée :

        ```console
        cat > /etc/cloud/cloud.cfg.d/00-azure-swap.cfg << EOF
        #cloud-config
        # Generated by Azure cloud image build
        disk_setup:
          ephemeral0:
            table_type: mbr
            layout: [66, [33, 82]]
            overwrite: True
        fs_setup:
          - device: ephemeral0.1
            filesystem: ext4
          - device: ephemeral0.2
            filesystem: swap
        mounts:
          - ["ephemeral0.1", "/mnt"]
          - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
        EOF
        ```

15. Exécutez les commandes suivantes pour annuler le déploiement de la machine virtuelle et préparer son déploiement sur Azure :

    ```console
    # sudo rm -rf /var/lib/waagent/
    # sudo rm -f /var/log/waagent.log

    # waagent -force -deprovision+user
    # rm -f ~/.bash_history
    

    # export HISTSIZE=0

    # logout
    ```
16. Cliquez sur **Action -> Arrêter** dans le Gestionnaire Hyper-V. Votre disque dur virtuel Linux est alors prêt pour le téléchargement dans Azure.

---
## <a name="prepare-opensuse-152"></a>Préparation d’openSUSE 15.2+
1. Dans le panneau central de Hyper-V Manager, sélectionnez la machine virtuelle.
2. Cliquez sur **Connect** pour ouvrir la fenêtre de la machine virtuelle.
3. Sur l’interpréteur de commandes, exécutez la commande`zypper lr`. Si cette commande renvoie un résultat semblable à ce qui suit, alors les référentiels sont configurés comme prévu, aucune modification n'est nécessaire (notez que les numéros de version peuvent varier) :

   | # | Alias                 | Nom                  | activé | Actualiser
   | - | :-------------------- | :-------------------- | :------ | :------
   | 1 | Cloud:Tools_15.2      | Cloud:Tools_15.2      | Oui     | Oui
   | 2 | openSUSE_15.2_OSS     | openSUSE_15.2_OSS     | Oui     | Oui
   | 3 | openSUSE_15.2_Updates | openSUSE_15.2_Updates | Oui     | Oui

    Si la commande renvoie « Aucun référentiel défini... », utilisez les commandes suivantes pour ajouter ces référentiels :

    ```console
    # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_15.2 Cloud:Tools_15.2
    # sudo zypper ar -f https://download.opensuse.org/distribution/15.2/repo/oss openSUSE_15.2_OSS
    # sudo zypper ar -f http://download.opensuse.org/update/15.2 openSUSE_15.2_Updates
    ```

    Vous pouvez alors réexécuter la commande `zypper lr` pour vérifier que ces référentiels ont été ajoutés. Si un des référentiels de mise à jour concernés n'est pas activé, exécutez la commande suivante :

    ```console
    # sudo zypper mr -e [NUMBER OF REPOSITORY]
    ```

4. Mettez à jour le noyau vers la dernière version disponible :

    ```console
    # sudo zypper up kernel-default
    ```

    Ou, pour mettre à jour le système avec les derniers correctifs :

    ```console
    # sudo zypper update
    ```

5. Installez l'agent Linux Azure.

    ```console
    # sudo zypper install WALinuxAgent
    ```

6. Modifiez la ligne de démarrage du noyau dans votre configuration grub pour y inclure les paramètres de noyau supplémentaires pour Azure. Pour cela, ouvrez le fichier « /boot/grub/menu.lst » dans un éditeur de texte et vérifiez que le noyau par défaut comprend les paramètres suivants :

    ```config-grub
     console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

   Ceci permet d'assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour l'assistance d'Azure. Supprimez également les paramètres suivants de la ligne de démarrage du noyau (s'ils sont présents) :

    ```config-grub
     libata.atapi_enabled=0 reserve=0x1f0,0x8
    ```

7. Il est recommandé de modifier le fichier « /etc/sysconfig/network/dhcp » et le paramètre `DHCLIENT_SET_HOSTNAME` comme suit :

    ```config
     DHCLIENT_SET_HOSTNAME="no"
    ```

8. **Important :** Sous « /etc/sudoers », commentez ou supprimez les lignes suivantes (si elles sont présentes) :

    ```text
    Defaults targetpw   # ask for the password of the target user i.e. root
    ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
    ```

9. Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu.  C'est généralement le cas par défaut.
10. Ne créez pas d'espace swap sur le disque du système d'exploitation.

    L'agent Linux Azure peut configurer automatiquement un espace swap à l'aide du disque local de ressources connecté à la machine virtuelle après déploiement sur Azure. Notez que le disque de ressources local est un disque *temporaire* et qu'il peut être vidé lors de l'annulation de l'approvisionnement de la machine virtuelle. Après avoir installé l'agent Linux Azure (voir l'étape précédente), modifiez les paramètres suivants dans le fichier /etc/waagent.conf :

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

11. Exécutez les commandes suivantes pour annuler le déploiement de la machine virtuelle et préparer son déploiement sur Azure :

    ```console
    # sudo waagent -force -deprovision
    # export HISTSIZE=0
    # logout
    ```

12. Vérifiez que l'agent Linux Azure est exécuté au démarrage :

    ```console
    # sudo systemctl enable waagent.service
    ```

13. Cliquez sur **Action -> Arrêter** dans le Gestionnaire Hyper-V. Votre disque dur virtuel Linux est alors prêt pour le téléchargement dans Azure.

## <a name="next-steps"></a>Étapes suivantes
Vous êtes maintenant prêt à utiliser votre disque dur virtuel SUSE Linux pour créer des machines virtuelles dans Azure. S’il s’agit de votre premier chargement du fichier .vhd sur Azure, consultez [Créer une machine virtuelle Linux à partir d’un disque personnalisé](upload-vhd.md#option-1-upload-a-vhd).
