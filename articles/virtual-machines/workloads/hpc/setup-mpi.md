---
title: Configurer l’interface de passage de messages pour (MPI) HPC - Machines virtuelles Azure | Microsoft Docs
description: Découvrez comment configurer MPI pour HPC sur Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 04/16/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: ca3559d262420ee6c7ba935a0986340d2d475d8d
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129533191"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>Configurer l’interface de transmission de messages pour HPC

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

[L’interface MPI (Message Passing Interface)](https://en.wikipedia.org/wiki/Message_Passing_Interface) est une bibliothèque ouverte et une norme de facto pour la parallélisation de la mémoire distribuée. Elle est couramment utilisée dans de nombreuses charges de travail HPC. Les charges de travail HPC sur les machines virtuelles [RDMA](../../sizes-hpc.md#rdma-capable-instances) [Série H](../../sizes-hpc.md) et [Série N](../../sizes-gpu.md) peuvent utiliser MPI pour communiquer sur le réseau InfiniBand à faible latence et à bande passante élevée.
- Les tailles des machines virtuelles SR-IOV activées sur Azure permettent d’utiliser presque tous les qualificateurs de MPI avec Mellanox OFED.
- Sur les machines virtuelles non compatibles SR-IOV, les implémentations MPI prises en charge utilisent l’interface Microsoft Network Direct (ND) pour la communication entre les machines virtuelles. Par conséquent, seules les versions Microsoft MPI (MS-MPI) 2012 R2 ou ultérieures et Intel MPI 5.x sont supportées. Les versions ultérieures (2017, 2018) de la bibliothèque runtime MPI Intel peuvent ne pas être compatibles avec les pilotes Azure RDMA.

Pour les [machines virtuelles compatibles RDMA](../../sizes-hpc.md#rdma-capable-instances), des [images de machine virtuelle CentOS-HPC](configure.md#centos-hpc-vm-images) versions 7.6 et ultérieures conviennent. Ces images de machine virtuelle sont optimisées et préchargées avec les pilotes OFED pour RDMA ainsi que les diverses bibliothèques MPI et packages de calcul scientifique couramment utilisés. elles constituent le moyen le plus simple de commencer.

Les exemples de cette procédure illustrent la syntaxe RHEL/CentOS, mais les étapes sont générales et peuvent être utilisées pour tout système d’exploitation compatible, par exemple Ubuntu (16.04, 18.04, 19.04, 20.04) et SLES (12 SP4 et 15). Vous trouverez plus d’exemples sur la configuration d’autres implémentations MPI sur d’autres distributions dans le [référentiel azhpc-images](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mpis.sh).

> [!NOTE]
> L’exécution de travaux MPI sur des machines virtuelles compatibles SR-IOV avec certaines bibliothèques MPI (par exemple Platform MPI) peut nécessiter l’installation de clés de partition (p-keys) sur un locataire pour l’isolation et la sécurité. Suivez les étapes de la section [Découvrir les clés de partition](#discover-partition-keys) pour en savoir plus sur la détermination des valeurs des p-keys et leur configuration correcte pour un travail MPI avec la bibliothèque MPI.

> [!NOTE]
> Les extraits de code ci-dessous sont des exemples. Nous vous recommandons d’utiliser les dernières versions stables des packages ou de faire référence au [référentiel azhpc-images](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mpis.sh).

## <a name="choosing-mpi-library"></a>Choix de la bibliothèque MPI
Si une application HPC recommande une bibliothèque MPI particulière, essayez d’abord cette version. Si vous disposez d’une certaine flexibilité quant au choix de votre MPI et que vous souhaitez obtenir des performances optimales, essayez HPC-X. Dans l’ensemble, l’interface MPI HPC-X offre les meilleures performances en utilisant l’infrastructure UCX pour l’interface InfiniBand et tire parti de toutes les capacités matérielles et logicielles de Mellanox InfiniBand. De plus, HPC-X et OpenMPI sont compatibles avec l’ABI. Vous pouvez donc exécuter dynamiquement une application HPC avec HPC-X qui a été créée avec OpenMPI. De même, Intel MPI, MVAPICH et MPICH sont compatibles avec l’ABI.

La figure suivante illustre l’architecture des bibliothèques MPI les plus courantes.

![Architecture des bibliothèques MPI les plus courantes](./media/mpi-architecture.png)

## <a name="ucx"></a>UCX

[Unified Communication X (UCX)](https://github.com/openucx/ucx) est un framework d’API de communication pour HPC. Il est optimisé pour la communication MPI sur InfiniBand et fonctionne avec de nombreuses implémentations MPI telles que OpenMPI et MPICH.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

> [!NOTE]
> Les versions récentes d’UCX ont résolu un [problème](https://github.com/openucx/ucx/pull/5965) pour que l’interface InfiniBand appropriée soit choisie en présence de plusieurs interfaces NIC. Pour plus d’informations, consultez [Résolution des problèmes connus avec les machines virtuelles HPC et GPU](hb-hc-known-issues.md#accelerated-networking-on-hb-hc-hbv2-and-ndv2) sur l’exécution de l’interface de passage de messages sur InfiniBand quand les performances réseau accélérées sont activées sur la machine virtuelle.

## <a name="hpc-x"></a>HPC-X

Le [kit d’outils logiciels HPC-X](https://www.mellanox.com/products/hpc-x-toolkit) contient UCX et HCOLL et peut être créé sur UCX.

```bash
HPCX_VERSION="v2.6.0"
HPCX_DOWNLOAD_URL=https://azhpcstor.blob.core.windows.net/azhpc-images-store/hpcx-v2.6.0-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
wget --retry-connrefused --tries=3 --waitretry=5 $HPCX_DOWNLOAD_URL
tar -xvf hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
mv hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64 ${INSTALL_PREFIX}
HPCX_PATH=${INSTALL_PREFIX}/hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64
```

La commande suivante illustre certains arguments mpirun recommandés pour HPC-X et OpenMPI.
```bash
mpirun -n $NPROCS --hostfile $HOSTFILE --map-by ppr:$NUMBER_PROCESSES_PER_NUMA:numa:pe=$NUMBER_THREADS_PER_PROCESS -report-bindings $MPI_EXECUTABLE
```
où :

|Paramètre|Description                                        |
|---------|---------------------------------------------------|
|`NPROCS`   |Spécifie le nombre de processus MPI. Par exemple : `-n 16`.|
|`$HOSTFILE`|Spécifie un fichier contenant le nom d’hôte ou l’adresse IP pour indiquer l’emplacement où s’exécuteront les processus MPI. Par exemple : `--hostfile hosts`.|
|`$NUMBER_PROCESSES_PER_NUMA`   |Spécifie le nombre de processus MPI qui s’exécuteront dans chaque domaine NUMA. Par exemple, pour spécifier quatre processus MPI par NUMA, vous utilisez `--map-by ppr:4:numa:pe=1`.|
|`$NUMBER_THREADS_PER_PROCESS`  |Spécifie le nombre de threads par processus MPI. Par exemple, pour spécifier un processus MPI et quatre threads par NUMA, vous utilisez `--map-by ppr:1:numa:pe=4`.|
|`-report-bindings` |Imprime le mappage des processus MPI aux cœurs, ce qui est utile pour vérifier que l’épinglage de votre processus MPI est correct.|
|`$MPI_EXECUTABLE`  |Spécifie le fichier exécutable MPI généré avec la liaison dans les bibliothèques MPI. Les wrappers de compilateur MPI effectuent cette opération automatiquement. Par exemple, `mpicc` ou `mpif90`.|

Voici un exemple d’exécution du micropoint de référence de la latence OSU :
```bash
${HPCX_PATH}mpirun -np 2 --map-by ppr:2:node -x UCX_TLS=rc ${HPCX_PATH}/ompi/tests/osu-micro-benchmarks-5.3.2/osu_latency
```

### <a name="optimizing-mpi-collectives"></a>Optimisation des collectifs MPI

Les primitives de communication de collectifs MPI fournissent un moyen flexible et portable d’implémenter des opérations de communication de groupe. Elles sont largement utilisées dans plusieurs applications scientifiques parallèles et ont un impact significatif sur les performances globales d’application. Reportez-vous à l'[article TechCommunity](https://techcommunity.microsoft.com/t5/azure-compute/optimizing-mpi-collective-communication-using-hpc-x-on-azurehpc/ba-p/1356740) pour plus d’informations sur les paramètres de configuration afin d’optimiser les performances de communication collective à l’aide de HPC-X et de la bibliothèque HCOLL pour la communication collective.

Par exemple, si vous soupçonnez votre application MPI étroitement couplée d’effectuer une quantité excessive de communications collectives, vous pouvez essayer d’activer les hiérarchies collectives (HCOLL). Pour activer ces fonctionnalités, utilisez les paramètres suivants.
```bash
-mca coll_hcoll_enable 1 -x HCOLL_MAIN_IB=<MLX device>:<Port>
```

> [!NOTE] 
> Avec HPC-X 2.7.4+, il peut être nécessaire de passer explicitement LD_LIBRARY_PATH si la version UCX sur MOFED est différente de celle de HPC-X.

## <a name="openmpi"></a>OpenMPI

Installez UCX comme décrit ci-dessus. HCOLL fait partie du [kit d’outils logiciels HPC-X](https://www.mellanox.com/products/hpc-x-toolkit) et ne nécessite pas d’installation spéciale.

Vous pouvez installer OpenMPI à partir des packages disponibles dans le référentiel.

```bash
sudo yum install –y openmpi
```

Nous vous recommandons de créer une dernière version stable d’OpenMPI avec UCX.

```bash
OMPI_VERSION="4.0.3"
OMPI_DOWNLOAD_URL=https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-${OMPI_VERSION}.tar.gz
wget --retry-connrefused --tries=3 --waitretry=5 $OMPI_DOWNLOAD_URL
tar -xvf openmpi-${OMPI_VERSION}.tar.gz
cd openmpi-${OMPI_VERSION}
./configure --prefix=${INSTALL_PREFIX}/openmpi-${OMPI_VERSION} --with-ucx=${UCX_PATH} --with-hcoll=${HCOLL_PATH} --enable-mpirun-prefix-by-default --with-platform=contrib/platform/mellanox/optimized && make -j$(nproc) && make install
```

Pour des performances optimales, exécutez OpenMPI avec `ucx` et `hcoll`. Consultez également l’exemple avec [HPC-X](#hpc-x).

```bash
${INSTALL_PREFIX}/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

Vérifiez votre clé de partition comme indiqué ci-dessus.

## <a name="intel-mpi"></a>Intel MPI

Téléchargez la version [Intel MPI](https://software.intel.com/mpi-library/choose-download) de votre choix. La version 2019 d’Intel MPI est passée de l’infrastructure Open Fabrics Alliance (OFA) à l’infrastructure Open Fabrics Interfaces (OFI) et prend actuellement en charge libfabric. Il existe deux fournisseurs pour la prise en charge InfiniBand : mlx et verbs.
Modifiez la variable d’environnement I_MPI_FABRICS selon la version.
- Intel MPI 2019 et 2021 : utilisez `I_MPI_FABRICS=shm:ofi`, `I_MPI_OFI_PROVIDER=mlx`. Le fournisseur `mlx` utilise UCX. Il a été noté que l’utilisation de verbes était instable et moins performante. Consultez l’[article TechCommunity](https://techcommunity.microsoft.com/t5/azure-compute/intelmpi-2019-on-azure-hpc-clusters/ba-p/1403149) pour plus de détails.
- Intel MPI 2018 : utilisez `I_MPI_FABRICS=shm:ofa`
- Intel MPI 2016 : utilisez `I_MPI_DAPL_PROVIDER=ofa-v2-ib0`

Voici quelques suggestions d’arguments mpirun pour MPI 2019 update 5+.
```bash
export FI_PROVIDER=mlx
export I_MPI_DEBUG=5
export I_MPI_PIN_DOMAIN=numa

mpirun -n $NPROCS -f $HOSTFILE $MPI_EXECUTABLE
```
où :

|Paramètre|Description                                        |
|---------|---------------------------------------------------|
|`FI_PROVIDER`  |Spécifie le fournisseur libfabric à utiliser, ce qui aura un impact sur l’API, le protocole et le réseau utilisés. « verbs » est une autre option, mais « mlx » offre généralement de meilleures performances.|
|`I_MPI_DEBUG`|Spécifie le niveau de sortie de débogage supplémentaire, ce qui peut fournir des détails sur l’emplacement d’épinglage des processus, ainsi que sur le protocole et le réseau utilisés.|
|`I_MPI_PIN_DOMAIN` |Spécifie la façon dont vous souhaitez épingler vos processus. Par exemple, vous pouvez épingler au niveau des cœurs, sockets ou domaines NUMA. Dans cet exemple, vous définissez cette variable d’environnement sur « numa », ce qui signifie que les processus seront épinglés au niveau des domaines de nœud NUMA.|

### <a name="optimizing-mpi-collectives"></a>Optimisation des collectifs MPI

Vous pouvez essayer d’autres options, en particulier si les opérations collectives prennent beaucoup de temps. Intel MPI 2019 update 5+ prend en charge le fournisseur mlx et utilise le framework UCX pour communiquer avec InfiniBand. Il prend également en charge HCOLL.
```bash
export FI_PROVIDER=mlx
export I_MPI_COLL_EXTERNAL=1
```

### <a name="non-sr-iov-vms"></a>Machines virtuelles non SR-IOV

Pour les machines virtuelles non SR-IOV, voici un exemple de téléchargement de la [version d’évaluation gratuite](https://registrationcenter.intel.com/en/forms/?productid=1740) du runtime 5.x :
```bash
wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
```
Pour les étapes d’installation, consultez le [Guide d’installation de la bibliothèque Intel MPI](https://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html).
Vous pouvez aussi activer ptrace pour les processus non-racine et non-débogueur (nécessaire pour les versions les plus récentes d’Intel MPI).
```bash
echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
```

### <a name="suse-linux"></a>SUSE Linux
Pour les versions d’images de machines virtuelles SUSE Linux Enterprise Server - SLES 12 SP3 pour HPC, SLES 12 SP3 pour HPC (Premium), SLES 12 SP1 pour HPC, SLES 12 SP1 pour HPC (Premium), SLES 12 SP4 et SLES 15, les pilotes RDMA sont installés et les packages Intel MPI sont distribués sur la machine virtuelle. Installez Intel MPI en exécutant la commande suivante :
```bash
sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
```

## <a name="mvapich"></a>MVAPICH

Voici un exemple de construction de MVAPICH2. Notez que des versions plus récentes que celle utilisée ci-dessous peuvent être disponibles.
```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=${INSTALL_PREFIX}
make -j 8 && make install
```

Voici un exemple d’exécution du micropoint de référence de la latence OSU :
```bash
${INSTALL_PREFIX}/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

La liste suivante contient plusieurs arguments `mpirun` recommandés.
```bash
export MV2_CPU_BINDING_POLICY=scatter
export MV2_CPU_BINDING_LEVEL=numanode
export MV2_SHOW_CPU_BINDING=1
export MV2_SHOW_HCA_BINDING=1

mpirun -n $NPROCS -f $HOSTFILE $MPI_EXECUTABLE
```
où :

|Paramètre|Description                                        |
|---------|---------------------------------------------------|
|`MV2_CPU_BINDING_POLICY`   |Spécifie la stratégie de liaison à utiliser, ce qui aura une incidence sur la façon dont les processus sont épinglés aux ID des cœurs. Dans ce cas, vous spécifiez « scatter » afin que les processus soient répartis uniformément entre les domaines NUMA.|
|`MV2_CPU_BINDING_LEVEL`|Spécifie où épingler les processus. Dans ce cas, vous l’avez défini sur « numanode », ce qui signifie que les processus sont épinglés aux unités de domaines NUMA.|
|`MV2_SHOW_CPU_BINDING` |Spécifie si vous souhaitez obtenir des informations de débogage sur l’emplacement où les processus sont épinglés.|
|`MV2_SHOW_HCA_BINDING` |Spécifie si vous souhaitez obtenir des informations de débogage sur l’adaptateur de canal hôte (HCA) utilisé pour chaque processus.|

## <a name="platform-mpi"></a>Platform MPI

Installez les packages requis pour Platform MPI Community Edition.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

Suivez le processus d’installation.

Les commandes suivantes sont des exemples d’exécution de pingpong et allreduce de MPI à l’aide de Platform MPI sur des machines virtuelles HBv3 à l’aide d’images de machine virtuelle CentOS-HPC 7.6, 7.8, et 8.1.

```bash
/opt/ibm/platform_mpi/bin/mpirun -hostlist 10.0.0.8:1,10.0.0.9:1 -np 2 -e MPI_IB_PKEY=0x800a  -ibv  /home/jijos/mpi-benchmarks/IMB-MPI1 pingpong
/opt/ibm/platform_mpi/bin/mpirun -hostlist 10.0.0.8:120,10.0.0.9:120 -np 240 -e MPI_IB_PKEY=0x800a  -ibv  /home/jijos/mpi-benchmarks/IMB-MPI1 allreduce -npmin 240
```


## <a name="mpich"></a>MPICH

Installez UCX comme décrit ci-dessus. Créez MPICH.

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=${UCX_PATH} --prefix=${INSTALL_PREFIX} --with-device=ch4:ucx
make -j 8 && make install
```

Exécution de MPICH.

```bash
${INSTALL_PREFIX}/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

Vérifiez votre clé de partition comme indiqué ci-dessus.

## <a name="osu-mpi-benchmarks"></a>OSU MPI Benchmarks

Téléchargez [OSU MPI Benchmarks](http://mvapich.cse.ohio-state.edu/benchmarks/) et décompressez le fichier.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

Créez des bancs d’essai à l’aide d’une bibliothèque MPI particulière :

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

MPI Benchmarks se trouve dans le dossier `mpi/`.


## <a name="discover-partition-keys"></a>Découvrir les clés de partition

Découvrez les clés de partition (p-keys) pour communiquer avec d’autres machines virtuelles au sein du même locataire (groupe à haute disponibilité ou groupe de machines virtuelles identiques).

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

La plus grande des deux est la clé de locataire, qui doit être utilisée avec MPI. Exemple : Si les éléments suivants sont les p-keys, 0x800b doit être utilisé avec MPI.

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

Utiliser une partition autre que de la clé de partition par défaut (0x7fff). UCX requiert l’effacement de l’octet le plus significatif de la p-key. Par exemple, définissez UCX_IB_PKEY comme étant 0x000b pour 0x800b.

Notez également que tant que le locataire (groupe à haute disponibilité ou groupe de machines virtuelles identiques) existe, les PKEY restent les mêmes. Cela est vrai même lorsque les nœuds sont ajoutés ou supprimés. Les nouveaux locataires obtiennent des PKEYs différents.


## <a name="set-up-user-limits-for-mpi"></a>Configurez un nombre limite d’utilisateurs pour MPI

Configurez un nombre limite d’utilisateurs pour MPI.

```bash
cat << EOF | sudo tee -a /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```

## <a name="set-up-ssh-keys-for-mpi"></a>Configurez les clés SSH pour MPI

Configurer les clés SSH pour les types de MPI qui en ont besoin.

```bash
ssh-keygen -f /home/$USER/.ssh/id_rsa -t rsa -N ''
cat << EOF > /home/$USER/.ssh/config
Host *
    StrictHostKeyChecking no
EOF
cat /home/$USER/.ssh/id_rsa.pub >> /home/$USER/.ssh/authorized_keys
chmod 600 /home/$USER/.ssh/authorized_keys
chmod 644 /home/$USER/.ssh/config
```

La syntaxe ci-dessus suppose un répertoire de base partagé, sinon le répertoire .ssh doit être copié dans chaque nœud.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les machines virtuelles [Série H](../../sizes-hpc.md) et [Série N](../../sizes-gpu.md) avec [InfiniBand](../../sizes-hpc.md#rdma-capable-instances)
- Consultez la [Présentation de la série HBv3](hbv3-series-overview.md) et la [Présentation de la série HC](hc-series-overview.md).
- Lisez [Placement optimal du processus d’interface de passage de messages pour les machines virtuelles de la série HB](https://techcommunity.microsoft.com/t5/azure-global/optimal-mpi-process-placement-for-azure-hb-series-vms/ba-p/2450663).
- Consultez les dernières annonces, des exemples de charge de travail HPC et les résultats des performances sur les [blogs de la communauté Azure Compute Tech](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Pour une vision plus globale de l’architecture d’exécution des charges de travail HPC, consultez [Calcul haute performance (HPC) sur Azure](/azure/architecture/topics/high-performance-computing/).
