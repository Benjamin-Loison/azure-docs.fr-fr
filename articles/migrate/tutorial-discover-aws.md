---
title: Découvrir des instances AWS avec l’outil de découverte et d’évaluation d’Azure Migrate
description: Apprenez à découvrir des instances AWS avec l’outil de découverte et d’évaluation d’Azure Migrate.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 03/11/2021
ms.custom: mvc
ms.openlocfilehash: 625a11c42db4971dfa5e9c355354987178afbb07
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128611318"
---
# <a name="tutorial-discover-aws-instances-with-azure-migrate-discovery-and-assessment"></a>Découvrir des instances AWS avec Azure Migrate : découverte et évaluation

Dans le cadre de votre migration vers Azure, vous devez découvrir vos serveurs avant de les évaluer en vue de leur migration.

Ce tutoriel vous montre comment découvrir des instances AWS (Amazon Web Services) avec l’outil de découverte et d’évaluation d’Azure Migrate, à l’aide d’une appliance Azure Migrate légère. Vous allez déployer l’appliance comme un serveur physique afin de découvrir de façon continue les métadonnées relatives aux machines et aux performances.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Configurer un compte Azure
> * Préparer les instances AWS pour leur découverte
> * Crée un projet.
> * Configurer l’appliance Azure Migrate
> * Démarrer la découverte en continu

> [!NOTE]
> Les tutoriels enseignent le moyen le plus rapide de tester un scénario et d’utiliser les options par défaut.  

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Avant de commencer ce tutoriel, vérifiez les prérequis.

**Prérequis** | **Détails**
--- | ---
**Appliance** | Vous avez besoin d’une machine virtuelle EC2 sur laquelle exécuter l’appliance Azure Migrate. La machine doit disposer des éléments suivants :<br/><br/> - Windows Server 2016, installé<br/> _L’exécution de l’appliance sur un ordinateur doté de Windows Server 2019 n’est pas prise en charge_.<br/><br/> - 16 Go de RAM, 8 processeurs virtuels, environ 80 Go de stockage sur disque et un commutateur virtuel externe.<br/><br/> - Une adresse IP statique ou dynamique, avec un accès à Internet, directement ou via un proxy.
**Instances Windows** | Autorisez les connexions entrantes sur le port WinRM 5985 (HTTP) pour que l’appliance puisse tirer (pull) les métadonnées de configuration et de performances.
**Instances Linux** | Autorisez les connexions entrantes via le port 22 (TCP).<br/><br/> Pour ne pas que la découverte échoue, les instances doivent utiliser `bash` comme interpréteur de commandes par défaut.

## <a name="prepare-an-azure-user-account"></a>Préparer un compte de stockage Azure

Pour créer un projet et inscrire l’appliance Azure Migrate, vous avez besoin d’un compte avec :

* Des autorisations de niveau Contributeur ou Propriétaire sur un abonnement Azure
* Des autorisations permettant d’inscrire des applications Azure Active Directory (AAD).

Si vous venez de créer un compte Azure gratuit, vous êtes le propriétaire de votre abonnement. Si vous n’êtes pas le propriétaire de l’abonnement, demandez à celui-ci de vous attribuer les autorisations de la façon suivante :

1. Dans le portail Azure, recherchez « abonnements », puis sous **Services**, sélectionnez **Abonnements**.

    ![Zone de recherche pour rechercher l’abonnement Azure](./media/tutorial-discover-aws/search-subscription.png)

2. Dans la page **Abonnements**, sélectionnez l’abonnement dans lequel vous souhaitez créer un projet.
3. Dans l’abonnement, sélectionnez **Contrôle d’accès (IAM)**  > **Vérifier l’accès**.
4. Dans **Vérifier l’accès**, recherchez le compte d’utilisateur correspondant.
5. Dans **Ajouter une attribution de rôle**, cliquez sur **Ajouter**.

    ![Rechercher un compte d’utilisateur pour vérifier l’accès et attribuer un rôle](./media/tutorial-discover-aws/azure-account-access.png)

6. Dans **Ajouter une attribution de rôle**, sélectionnez le rôle Contributeur ou Propriétaire, puis sélectionnez le compte (azmigrateuser dans notre exemple). Ensuite, cliquez sur **Enregistrer**.

    ![Ouvre la page Ajouter une attribution de rôle pour attribuer un rôle au compte.](./media/tutorial-discover-aws/assign-role.png)

1. Pour inscrire l’appliance, votre compte Azure doit disposer d’**autorisations pour inscrire des applications AAD**.
1. Dans le portail Azure, accédez à **Azure Active Directory** > **Utilisateurs** > **Paramètres utilisateur**.
1. Dans **Paramètres utilisateur**, vérifiez que les utilisateurs Azure AD peuvent inscrire des applications (défini sur **Oui** par défaut).

    ![Vérifier dans les paramètres utilisateur que les utilisateurs peuvent inscrire des applications Active Directory](./media/tutorial-discover-aws/register-apps.png)

1. Si les paramètres « Inscriptions d’applications » ont la valeur « Non », demandez au locataire ou à l’administrateur général d’affecter l’autorisation nécessaire. L’administrateur général/le locataire peuvent également attribuer le rôle **Développeur d’applications** à un compte pour permettre l’inscription d’une application AAD. [En savoir plus](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)

## <a name="prepare-aws-instances"></a>Préparer des instances AWS

Configurez un compte que l’appliance peut utiliser pour accéder aux instances AWS.

- Pour les **serveurs Windows**, configurez un compte d’utilisateur local sur tous les serveurs Windows que vous voulez inclure dans la découverte. Ajoutez le compte d’utilisateur aux groupes suivants : Utilisateurs de gestion à distance, Utilisateurs de l’Analyseur de performances et Utilisateurs du journal de performances.
 - Pour les **serveurs Linux**, vous devez disposer d’un compte racine sur les serveurs Linux que vous voulez découvrir. Pour obtenir une alternative, reportez-vous aux instructions fournies dans la [matrice de prise en charge](migrate-support-matrix-physical.md#physical-server-requirements).
- Azure Migrate utilise l’authentification par mot de passe lors de la découverte des instances AWS. Les instances AWS ne prennent pas en charge l’authentification par mot de passe par défaut. Avant de pouvoir découvrir l’instance, vous devez activer l’authentification par mot de passe.
    - Pour les serveurs Windows, autorisez le port WinRM 5985 (HTTP). Cela permet les appels WMI distants.
    - Pour les serveurs Linux :
        1. Connectez-vous à chaque machine Linux.
        2. Ouvrez le fichier sshd_config : vi /etc/ssh/sshd_config
        3. Dans le fichier, localisez la ligne **PasswordAuthentication**, puis remplacez la valeur par **Oui**.
        4. Enregistrez le fichier et fermez-le. Redémarrez le service ssh.
    - Si vous vous servez d’un utilisateur racine pour découvrir vos serveurs Linux, assurez-vous que la connexion racine est autorisée sur les serveurs.
        1. Connectez-vous à chaque machine Linux.
        2. Ouvrez le fichier sshd_config : vi /etc/ssh/sshd_config
        3. Dans le fichier, repérez la ligne **PermitRootLogin**, puis remplacez la valeur par **yes**.
        4. Enregistrez le fichier et fermez-le. Redémarrez le service ssh.

## <a name="set-up-a-project"></a>Configuration d’un projet

Configurez un nouveau projet.

1. Dans le portail Azure, sélectionnez **Tous les services**, puis recherchez **Azure Migrate**.
2. Sous **Services**, sélectionnez **Azure Migrate**.
3. Dans **Vue d’ensemble**, sélectionnez **Créer un projet**.
5. Dans **Créer un projet**, sélectionnez votre abonnement Azure et votre groupe de ressources. Créez un groupe de ressources si vous n’en avez pas.
6. Dans **Détails du projet**, spécifiez le nom du projet ainsi que la zone géographique où vous souhaitez le créer. Passez en revue les zones géographiques prises en charge pour les clouds [publics](migrate-support-matrix.md#supported-geographies-public-cloud) et du [secteur public](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Zones pour le nom et la région du projet](./media/tutorial-discover-aws/new-project.png)

7. Sélectionnez **Create** (Créer).
8. Attendez quelques minutes, le temps nécessaire au déploiement du projet. L’outil **Azure Migrate : découverte et évaluation** est ajouté par défaut au nouveau projet.

![Page montrant l’outil Server Assessment ajouté par défaut](./media/tutorial-discover-aws/added-tool.png)

> [!NOTE]
> Si vous avez déjà créé un projet, vous pouvez utiliser le même projet pour inscrire des appliances supplémentaires afin de découvrir et d’évaluer un plus grand nombre de serveurs.[En savoir plus](create-manage-projects.md#find-a-project)

## <a name="set-up-the-appliance"></a>Configurer l’appliance

L’appliance Azure Migrate est une appliance légère utilisée par Azure Migrate : découverte et évaluation pour effectuer les opérations suivantes :

- Découvrir les serveurs locaux.
- Envoyer des métadonnées et des données de performances concernant les serveurs découverts à Azure Migrate : découverte et évaluation.

[En savoir plus](migrate-appliance.md) sur les appliances d’Azure Migrate.

Pour configurer l’appliance, vous devez :

1. Fournissez un nom d’appliance et générez une clé de projet sur le portail.
1. Télécharger un fichier compressé avec le script du programme d’installation Azure Migrate à partir du portail Azure.
1. Extraire le contenu du fichier compressé. Lancer la console PowerShell avec des privilèges administratifs.
1. Exécuter le script PowerShell pour lancer l’application web de l’appliance.
1. Configurez l’appliance pour la première fois, puis inscrivez-la auprès du projet en utilisant la clé de projet.

### <a name="1-generate-the-project-key"></a>1. Générer la clé de projet

1. Dans **Objectifs de migration** > **Serveurs** > **Azure Migrate : découverte et évaluation**, sélectionnez **Découvrir**.
2. Dans **Découvrir des serveurs** > **Vos serveurs sont-ils virtualisés ?** , sélectionnez **Physiques ou autres (AWS, GCP, Xen, etc.)** .
3. Dans **1 : Générer une clé de projet**, attribuez un nom à l’appliance Azure Migrate que vous allez configurer pour la découverte des serveurs virtuels ou physiques. Le nom doit être alphanumérique et comporter 14 caractères au maximum.
1. Cliquez sur **Générer une clé** pour lancer la création des ressources Azure nécessaires. Ne fermez pas la page Découvrir des serveurs pendant la création de ressources.
1. Une fois les ressources Azure créées, une **clé de projet** est générée.
1. Copiez la clé car vous en aurez besoin pour terminer l'inscription de l'appliance lors de sa configuration.

### <a name="2-download-the-installer-script"></a>2. Télécharger le script du programme d’installation

Dans **2 : Télécharger l’appliance Azure Migrate**, cliquez sur **Télécharger**.

### <a name="verify-security"></a>Vérifier la sécurité

Vérifiez que le fichier compressé est sécurisé avant de le déployer.

1. Sur l’ordinateur où vous avez téléchargé le fichier, ouvrez une fenêtre de commande d’administrateur.
2. Exécutez la commande suivante pour générer le code de hachage du fichier compressé :
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemple d’utilisation du cloud public : ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public.zip SHA256 ```
    - Exemple d’utilisation du cloud du secteur public : ```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256 ```
3.  Vérifiez les toutes dernières versions de l’appliance et les valeurs de hachage :
    - Pour le cloud public :

        **Scénario** | **Téléchargement** _ | _ *Valeur de hachage**
        --- | --- | ---
        Physique (85 Mo) | [Version la plus récente](https://go.microsoft.com/fwlink/?linkid=2140334) | 207157bab39303dca1c2b93562d6f1deaa05aa7c992f480138e17977641163fb

    - Pour Azure Government :

        **Scénario** | **Téléchargement** _ | _ *Valeur de hachage**
        --- | --- | ---
        Physique (85 Mo) | [Version la plus récente](https://go.microsoft.com/fwlink/?linkid=2140338) | ca67e8dbe21d113ca93bfe94c1003ab7faba50472cb03972d642be8a466f78ce
 

### <a name="3-run-the-azure-migrate-installer-script"></a>3. Exécuter le script du programme d’installation Azure Migrate
Le script du programme d’installation effectue les opérations suivantes :

- Installe des agents et une application web pour la découverte et l’évaluation des serveurs physiques.
- Installe des rôles Windows, notamment le service d’activation Windows, IIS et PowerShell ISE.
- Télécharge et installe un module réinscriptible IIS.
- Met à jour une clé de Registre (HKLM) avec les détails de paramètres persistants pour Azure Migrate.
- Crée les fichiers suivants sous le chemin :
    - **Fichiers de configuration** : %Programdata%\Microsoft Azure\Config
    - **Fichiers journaux** : %Programdata%\Microsoft Azure\Logs

Exécutez le script comme suit :

1. Extrayez le fichier compressé dans un dossier sur le serveur qui hébergera l’appliance.  Veillez à ne pas exécuter le script sur un ordinateur d’une appliance Azure Migrate existante.
2. Lancez PowerShell sur le serveur ci-dessus avec un privilège administratif (élevé).
3. Remplacez le répertoire PowerShell par le dossier dans lequel le contenu a été extrait du fichier compressé téléchargé.
4. Exécutez le script nommé **AzureMigrateInstaller.ps1** via la commande suivante :

   - Pour le cloud public : 

     `PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1`

   - Pour Azure Government : 

     `PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1`

     Une fois son exécution terminé, le script lance l’application web de l’appliance.

Si vous rencontrez des problèmes, vous pouvez accéder aux journaux de script dans C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>horodatage</em>.log pour les résoudre.

### <a name="verify-appliance-access-to-azure"></a>Vérifier l’accès de l’appliance à Azure

Vérifiez que l’appliance peut se connecter aux URL Azure pour les clouds [publics](migrate-appliance.md#public-cloud-urls) et du [secteur public](migrate-appliance.md#government-cloud-urls).

### <a name="4-configure-the-appliance"></a>4. Configurer l’appliance

Configurez l’appliance pour la première fois.

1. Ouvrez un navigateur sur une machine qui peut se connecter à l’appliance, puis ouvrez l’URL de l’application web de l’appliance : **https://*nom ou adresse IP de l’appliance* : 44368**.

   Vous pouvez aussi ouvrir l’application à partir du bureau en cliquant sur le raccourci de l’application.
2. Acceptez les **termes du contrat de licence** et lisez les informations relatives aux tiers.
1. Dans l’application web > **Configurer les prérequis**, procédez comme suit :
    - **Connectivité** : L’application vérifie que le serveur a accès à Internet. Si le serveur utilise un proxy :
        - Cliquez sur **Configurer le proxy**, puis spécifiez l’adresse du proxy (sous la forme http://ProxyIPAddress ou http://ProxyFQDN) et le port d’écoute.
        - Spécifiez les informations d’identification si le proxy nécessite une authentification.
        - Seuls les proxys HTTP sont pris en charge.
        - Si vous avez ajouté des détails de proxy ou désactivé le proxy et/ou l’authentification, cliquez sur **Enregistrer** pour relancer la vérification de la connectivité.
    - **Synchronisation de l’heure** : L’heure est vérifiée. L’heure de l’appliance doit être synchronisée avec l’heure Internet pour que la découverte de serveur fonctionne correctement.
    - **Installer les mises à jour** : Azure Migrate: Discovery and assessment vérifie que les dernières mises à jour sont installées sur l’appliance. Une fois la vérification terminée, vous pouvez cliquer sur **Afficher les services de l’appliance** pour voir l’état et les versions des composants s’exécutant sur l’appliance.

### <a name="register-the-appliance-with-azure-migrate"></a>Inscrire l’appliance auprès d’Azure Migrate

1. Collez la **clé de projet** copiée à partir du portail. Si vous n’avez pas la clé, accédez à **Azure Migrate : Discovery and Assessment > Découvrir > Gérer les appliances existantes**, sélectionnez le nom d’appliance que vous avez indiqué au moment de générer la clé, puis copiez la clé correspondante.
1. Vous aurez besoin d’un code d’appareil pour vous authentifier auprès d’Azure. Le fait de cliquer sur **Connexion** ouvre une boîte de dialogue modale comprenant le code de l’appareil, comme celle affichée ci-dessous.

    ![Boîte de dialogue modale indiquant le code de l’appareil](./media/tutorial-discover-vmware/device-code.png)

1. Cliquez sur **Copier le code et se connecter** pour copier le code de l’appareil et ouvrir une invite de connexion Azure dans un nouvel onglet de navigateur. S’il n’apparaît pas, vérifiez que vous avez désactivé le bloqueur de fenêtres publicitaires dans le navigateur.
1. Sous le nouvel onglet, collez le code de l’appareil, puis connectez-vous avec votre nom d’utilisateur et votre mot de passe Azure.
   
   La connexion avec un code PIN n’est pas prise en charge.
3. Si vous avez fermé accidentellement l’onglet Connexion avant de vous être connecté, vous devrez actualiser l’onglet Appliance Configuration Manager pour réactiver le bouton de connexion.
1. Une fois connecté, revenez à l’onglet précédent, c’est-à-dire, l’onglet Appliance Configuration Manager.
4. Si le compte d’utilisateur Azure utilisé pour la connexion dispose des [autorisations](./tutorial-discover-physical.md) adéquates sur les ressources Azure créées au moment de la génération de la clé, l’inscription de l’appliance est lancée.
1. Une fois l'appliance inscrite, vous pouvez consulter les détails de l'inscription en cliquant sur **Afficher les détails**.


## <a name="start-continuous-discovery"></a>Démarrer la découverte en continu

À présent, connectez-vous à partir de l’appliance aux serveurs physiques à découvrir, puis démarrez la découverte.

1. À l’**Étape 1 : Fournir des informations d’identification pour la découverte des serveurs physiques ou virtuels Windows et Linux**, cliquez sur **Ajouter des informations d’identification**.
1. Pour le serveur Windows, sélectionnez le type de source **Windows Server**, spécifiez un nom convivial pour les informations d’identification, puis ajoutez le nom d’utilisateur et le mot de passe. Cliquez sur **Save**(Enregistrer).
1. Si vous utilisez l’authentification par mot de passe pour le serveur Linux, sélectionnez le type de source **Serveur Linux (basé sur un mot de passe)** , spécifiez un nom convivial pour les informations d’identification, puis ajoutez le nom d’utilisateur et le mot de passe. Cliquez sur **Save**(Enregistrer).
1. Si vous utilisez l’authentification par clé SSH pour le serveur Linux, vous pouvez sélectionner le type de source **Serveur Linux (par clé SSH)** , spécifier un nom convivial pour les informations d’identification, ajouter le nom d’utilisateur, puis rechercher et sélectionner le fichier de clé privée SSH. Cliquez sur **Save**(Enregistrer).

    * Azure Migrate prend en charge la clé privée SSH générée par la commande ssh-keygen avec les algorithmes RSA, DSA, ECDSA et ed25519.
    * Azure Migrate ne prend pas en charge la clé SSH basée sur une phrase secrète. Utilisez une clé SSH sans phrase secrète.
    * Actuellement, Azure Migrate ne prend pas en charge le fichier de clé privée SSH généré par PuTTy.
    * Azure Migrate prend en charge le format OpenSSH du fichier de clé privée SSH, comme indiqué ci-dessous :
    
    ![Format de clé privée SSH pris en charge](./media/tutorial-discover-physical/key-format.png)


1. Si vous souhaitez ajouter plusieurs informations d’identification à la fois, cliquez sur **Ajouter** pour enregistrer et ajouter d’autres informations d'identification. Plusieurs informations d’identification sont prises en charge pour la découverte de serveurs physiques.
1. Dans **Étape 2 : Fournir les détails du serveur physique ou virtuel**, cliquez sur **Ajouter une source de découverte** pour spécifier l’**adresse IP/nom de domaine complet** du serveur ainsi que le nom convivial des informations d’identification pour se connecter au serveur.
1. Vous pouvez soit **ajouter un seul élément** à la fois, soit **ajouter plusieurs éléments** simultanément. Il existe également une option permettant de fournir les détails du serveur via **Importer au format CSV**.


    - Si vous choisissez **Ajouter un seul élément**, vous pouvez choisir le type de système d’exploitation, attribuer un nom convivial aux informations d’identification, ajouter l’**adresse IP/nom de domaine complet** du serveur, puis cliquer sur **Enregistrer**.
    - Si vous choisissez **Ajouter plusieurs éléments**, vous pouvez ajouter plusieurs enregistrements à la fois en spécifiant l’**adresse IP/nom de domaine complet** du serveur avec le nom convivial des informations d’identification dans la zone de texte. Vérifiez les enregistrements ajoutés, puis cliquez sur **Enregistrer**.
    - Si vous choisissez **Importer au format CSV** _(sélectionné par défaut)_ , vous pouvez télécharger un fichier de modèle CSV, compléter le fichier avec l’**adresse IP/nom de domaine complet** du serveur et le nom convivial des informations d’identification. Importez ensuite le fichier dans l’appliance, **vérifiez** les enregistrements dans le fichier, puis cliquez sur **Enregistrer**.

1. Dès que vous cliquez sur Enregistrer, l’appliance tente de valider la connexion aux serveurs ajoutés et affiche l’**état de validation** de chaque serveur dans le tableau.
    - Si la validation échoue pour un serveur, examinez l’erreur en cliquant sur **Échec de la validation** dans la colonne État du tableau. Corrigez le problème et recommencez la validation.
    - Pour supprimer un serveur, cliquez sur **Supprimer**.
1. Vous pouvez **revalider** la connectivité aux serveurs à tout moment avant de lancer la découverte.
1. Cliquez sur **Démarrer la découverte** pour lancer la découverte des serveurs validés. Une fois la découverte lancée, vous pouvez vérifier l’état de la découverte de chaque serveur dans le tableau.


Ceci démarre la découverte. Il faut environ deux minutes par serveur pour que les métadonnées du serveur détecté s’affichent sur le portail Azure.

## <a name="verify-servers-in-the-portal"></a>Vérifier les serveurs dans le portail

Une fois la découverte terminée, vous pouvez vérifier que les serveurs apparaissent dans le portail.

1. Ouvrez le tableau de bord Azure Migrate.
2. Dans la page **Azure Migrate - Windows, Linux et serveurs SQL** > **Azure Migrate : Discovery and assessment**, cliquez sur l’icône qui affiche le nombre de **Serveurs découverts**.

## <a name="next-steps"></a>Étapes suivantes

- [Évaluer des serveurs physiques](tutorial-migrate-aws-virtual-machines.md) en vue de leur migration vers des machines virtuelles Azure
- [Passer en revue les données](migrate-appliance.md#collected-data---physical) que l’appliance collecte pendant la découverte
