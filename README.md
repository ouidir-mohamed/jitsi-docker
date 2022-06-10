# Auto-hébergement de JITSI - Docker


## Démarrage rapide

Ces instructions vous permettront d'obtenir une copie du projet opérationnel sur votre machine locale à des fins de développement et de test. 

### Conditions préalables

Docker desktop

Git Bash 

Visual Studio Code 



### Installation

1.Clonner cette répo puis ouvrir avec Visual Studio Code . 


#### Utilisez la CMD de visual studio code

2.Créez un fichier .env en copiant et en ajustant env.example :

> p env.example .env

3.Définissez des mots de passe forts dans les options de la section de sécurité du fichier .env en exécutant le script bash suivant

> ./gen-passwords.sh

4.Créer les répertoires CONFIG requis
-Pour LINUX

> mkdir -p ~/.jitsi-meet-cfg/{web,transcripts,prosody/config,prosody/prosody-plugins-custom,jicofo,jvb,jigasi,jibri}

-Pour WINDOWS

> echo web,transcripts,prosody/config,prosody/prosody-plugins-custom,jicofo,jvb,jigasi,jibri | % { mkdir "~/.jitsi-meet-cfg/$_" }




5.Modifier le fichier .env   
Il faut obtenir  l'IP du serveur local   
Pour Windows: on tappe la commande "ipconfig"  
Pour Linux: on tappe la commande "ip address"  

Dans le fichier .env on modifie la valeur de paramétre DOCKER_HOST_ADDRESS par celle du local 

6.Exécutez 

> docker-compose up -d

#### Interface Web 

[Accédez à l'interface utilisateur Web à l'adresse](https://localhost:8443)


7.L'activation d la transcription  
Pour activer la transcription il est nécessaire d'installer Jigasi, et créer un abonnement [Google Speech To Text](https://console.cloud.google.com/freetrial/signup/tos?hl=fr).  

Aprés avoir créer un abonnement Google speech To Text et le téléchargement de fichier Json on décommente les paramétres suivants dans le fichier .env   

GC_PROJECT_ID=  
GC_PRIVATE_KEY_ID=  
GC_PRIVATE_KEY=  
GC_CLIENT_EMAIL=  
GC_CLIENT_ID=  
GC_CLIENT_CERT_URL=  

On les remplassant avec les paramétres obtenues depuis le fichier Json

On décommente aussi le paramétre   
ENABLE_TRANSCRIPTIONS=1  

Pour l'installation de jigasi exécutez la commande suivante:

> docker-compose -f docker-compose.yml -f jigasi.yml up

Maintenant vous pouvez utiliser la transcription en cliquant sur le boutton CC pendant une visioconférence  

8.Remplasser le service Google Speech To Text par Vosk Server  

Vosk Server est un server open source de la transcription similaire a Google Speech To Text, on peut l'installer sur notre machine.  
Pour l'installation d'une instance de Vosk Server on tappe les commande suivantes: 
   
> docker pull alphacep/kaldi-vosk-server
> docker run -d -p 2700:2700 alphacep/kaldi-fr:latest  
  
Une fois le serveur est installé on doit ajouter un fichier de configuration pour Jigasi afin de redirigé le service de transcription vers le serveur Vosk

Pour Windows on ouvre le répertoire %HOMEPATH%/.jitsi-meet-cfg/jigasi
Pour Linux cd ~/.jitsi-meet-cfg/jigasi    

On ajoute un fichier sous le nom "custom-sip-communicator.properties"  
On ajoute les lignes suivantes a ce fichier:  
    
org.jitsi.jigasi.transcription.customService=org.jitsi.jigasi.transcription.VoskTranscriptionService  
org.jitsi.jigasi.transcription.vosk.websocket_url=ws://{votre IP locale}:2700  
  
On redémare tout les services de Jiti on exécutant la commande suivante:  
> docker-compose -f docker-compose.yml -f jigasi.yml up


  




