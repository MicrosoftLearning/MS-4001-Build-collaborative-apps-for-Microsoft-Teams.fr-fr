---
lab:
  title: Créer un bot
  module: Exercise 4
---

# Exercice 4 : Créer un bot

## Scénario

Supposons que l’équipe de support informatique que vous assistez reçoit un volume élevé de requêtes courantes et répétitives des employés de l’organisation. Ces requêtes impliquent souvent des problèmes simples tels que les réinitialisations de mot de passe, les instructions d’installation de logiciels et la résolution d’erreurs courantes.

Pour simplifier le processus et réduire la charge de travail de votre équipe, vous décidez de créer un bot capable de gérer ces requêtes courantes dans Microsoft Teams.

Vous décidez d’ajouter au bot une commande initiale nommée « resetPassword ». Lorsqu’un utilisateur tape cette commande, le bot répond avec des instructions pas à pas lui expliquant comment réinitialiser son mot de passe. Cela permet aux utilisateurs de résoudre leur problème sans avoir à contacter l’équipe du support informatique directement, ce qui libère votre équipe pour gérer des problèmes plus complexes.

Outre la commande « resetPassword », vous envisagez d’ajouter d’autres commandes pour gérer d’autres requêtes courantes, transformant ainsi le bot en outil complet en libre-service pour les employés de l’organisation.

## Tâches de l’exercice

Pour réaliser l’exercice, vous devez effectuer les tâches suivantes :

1. Créer le bot à l’aide de Teams Toolkit
2. Configurer le manifeste
3. Créer une carte adaptative
4. Gérer la commande

**Durée prévue :** 17 minutes

## Tâche 1 : Créer un bot à l’aide de Teams Toolkit

Utilisez le modèle de bot de commande pour créer un bot :

1. Ouvrez Visual Studio Code.
1. Dans la barre latérale, sélectionnez l’icône **Microsoft Teams** pour ouvrir le panneau **TEAMS TOOLKIT**.
1. Cliquez sur le bouton **Créer une application**.
1. Dans le menu **New Project**, sélectionnez **Bot**, puis **Chat Command** pour générer un bot de commande.
1. Comme langage de programmation, sélectionnez **TypeScript**.
1. Comme **dossier de l’espace de travail**, sélectionnez ou créez un dossier pour stocker vos fichiers projet sur votre ordinateur.
1. Comme **nom d’application**, entrez **SupportCommandBot**, puis appuyez sur **Entrée**. Teams Toolkit génère la structure d’une nouvelle application et ouvre le dossier du projet dans Visual Studio Code.
1. Vous pouvez recevoir un message de Visual Studio Code qui vous demande si vous approuvez les auteurs des fichiers dans ce dossier. Sélectionnez le bouton **Oui, je fais confiance aux auteurs** pour continuer.
1. Passez en revue les répertoires et fichiers du projet à l’aide de l’Explorateur dans Visual Studio Code pour vous familiariser avec le code source.

## Tâche 2 : Configurer le manifeste

Définissez une commande `ResetPassword` dans le manifeste de l’application :

1. Ouvrez le fichier `manifest.json` à partir du dossier `appPackage`.
2. Dans l’objet `bots`, recherchez `commandLists`.  Il existe actuellement une seule commande nommée `helloWorld`.
3. Remplacez `commands` par le code suivant, de façon à ce qu’il inclut une nouvelle commande `ResetPassword` comme suit :

    ```typescript
            "commands": [
                {
                    "title": "helloWorld",
                    "description": "A helloworld command to send a welcome message"
                },
                {
                    "title": "resetPassword",
                    "description": "Request instructions to reset your password"
                }
            ]
    ```

## Tâche 3 : Créer une carte adaptative

Créez une carte adaptative à envoyer en réponse à la commande `ResetPassword` :

1. Dans `src`/`adaptiveCards`, créez un fichier nommé `resetPassword.json`.
2. Ajoutez le contenu suivant au fichier et enregistrez-le :

   ```json
        {
            "type": "AdaptiveCard",
            "body": [
                {
                    "type": "TextBlock",
                    "size": "Medium",
                    "weight": "Bolder",
                    "text": "Reset Password Instructions"
                },
                {
                    "type": "TextBlock",
                    "text": "1. Navigate to the login page and select 'Forgot Password'.",
                    "wrap": true
                },
                {
                    "type": "TextBlock",
                    "text": "2. Enter your email or username and select 'Submit'.",
                    "wrap": true
                },
                {
                    "type": "TextBlock",
                    "text": "3. Check your email for a password reset link and select it.",
                    "wrap": true
                },
                {
                    "type": "TextBlock",
                    "text": "4. Enter and confirm your new password, then select 'Save'.",
                    "wrap": true
                },
                {
                    "type": "TextBlock",
                    "text": "5. Log in with your new password.",
                    "wrap": true
                }
            ],
            "actions": [
                {
                    "type": "Action.OpenUrl",
                    "title": "Go to Login Page",
                    "url": "https://www.adaptivecards.io/designer/"
                }
            ],
            "$schema": "http://adaptivecards.io/schemas/adaptive-card.json",
            "version": "1.4"
        }
   ```

## Tâche 4 : Gérer la commande

Ensuite, gérez la commande dans le code source du bot à l’aide de la classe `TeamsFxBotCommandHandler`.  Importez `resetPasswordCard` à partir du fichier json et affichez-le lorsque la commande est appelée :

1. Dans le dossier `src`, créez un fichier nommé `resetPasswordCommandHandler.ts`.
2. Ajoutez les instructions d’importation suivantes au fichier, y compris une instruction pour importer la carte `resetPassword` brute que vous avez créée :

   ```typescript
   import { Activity, CardFactory, MessageFactory, TurnContext } from "botbuilder";
    import { CommandMessage, TeamsFxBotCommandHandler, TriggerPatterns } from "@microsoft/teamsfx";
    import { AdaptiveCards } from "@microsoft/adaptivecards-tools";
    import rawResetPasswordCard from "./adaptiveCards/resetPassword.json";
   ```
3. Sous les instructions d’importation, ajoutez le code suivant pour implémenter le gestionnaire de commandes, puis enregistrez le fichier :

   ```typescript
       export class ResetPasswordCommandHandler implements TeamsFxBotCommandHandler {
          triggerPatterns: TriggerPatterns = "resetPassword";
        
          async handleCommandReceived(
            context: TurnContext,
            message: CommandMessage
          ): Promise<string | Partial<Activity> | void> {
            console.log(`App received message: ${message.text}`);
        
            const resetPasswordCard = AdaptiveCards.declareWithoutData(rawResetPasswordCard).render();
            await context.sendActivity({ attachments: [CardFactory.adaptiveCard(resetPasswordCard)] });
          }
        }
   ```

## Tâche 5 : Inscrire la nouvelle commande

Chaque nouvelle commande doit être configurée dans le `ConversationBot`, qui alimente le flux conversationnel du modèle de bot de commandes.

1. Accédez au fichier `src/internal/initialize.ts`.
2. Ajoutez l’instruction d’importation suivante sur la ligne 2 :

    `import { ResetPasswordCommandHandler } from "../resetPasswordCommandHandler";`
3. À la ligne 20, mettez à jour le tableau `commands` de la propriété `command` afin d’inclure une déclaration d’initialisation du nouveau gestionnaire : `new ResetPasswordCommandHandler()`.  L’objet `command` mis à jour doit être le suivant :

   ```json
   command: {    enabled: true,    commands: [new HelloWorldCommandHandler(), new ResetPasswordCommandHandler()],  },
    ```

## Tâche 6 : basculer vers ngrok à partir du tunnel dev (facultatif)

Si votre environnement de développement ne prend pas en charge le tunnel dev Teams Toolkit, vous pouvez remplacer le tunnel dev par ngrok.

1. Pour installer ngrok, effectuez les étapes suivantes :
   1. Accédez au [site web de ngrok](https://ngrok.com/) et ouvrez un compte.
   1. Téléchargez l’exécutable ngrok pour votre système d’exploitation.
   1. Extrayez le fichier téléchargé dans le répertoire de votre choix.
   1. Pour l’environnement Windows, ajoutez le répertoire où se trouve `ngrok.exe` à la variable d’environnement PATH du système. 
      ```powershell
      setx PATH "$Env:path;<ngrok_full_path>"
      ```
      _Dans l’environnement PowerShell, remplacez `<ngrok_full_path>` par le chemin d’accès à `ngrok.exe`._
      > Pour appliquer cette modification de variable d’environnement, vous devez redémarrer les terminaux et **Visual Studio Code** pour le projet actif.

   1. Ouvrez un terminal ou une invite de commandes et exécutez la commande suivante pour authentifier votre compte ngrok :
      ```shell
      ngrok config add-authtoken <your_auth_token>
      ```
      _Remplacez `<your_auth_token>` par le jeton d’authentification fourni sur le site web de ngrok._
   1. Pour démarrer un tunnel sur le port 3978, exécutez la commande suivante :
      ```shell
      ngrok http 3978
      ```
   1. Ngrok génère une URL de transfert que vous pouvez utiliser pour accéder à votre application à partir d’Internet.
      ```shell
      Forwarding      http://<random_string>.ngrok-free.app -> http://localhost:3978
      ```
   1. Cliquez sur `Ctrl + C` pour déconnecter le tunnel ngrok.
1. Accédez au dossier `.vscode`, puis ouvrez le fichier `task.json`. Mettez à jour la tâche `Start local tunnel` :
   ```json
    {
        "label": "Start local tunnel",
        "type": "shell",
        "command": "ngrok http 3978 --log=stdout --log-format=logfmt",
        "isBackground": true,
        "problemMatcher": {
            "pattern": [
                {
                    "regexp": "^.*$",
                    "file": 0,
                    "location": 1,
                    "message": 2
                }
            ],
            "background": {
                "activeOnStart": true,
                "beginsPattern": "starting web service",
                "endsPattern": "started tunnel|failed to reconnect session"
            }
        }
    }
   ```
1. Accédez au fichier `teamsapp.local.yml` dans le dossier racine. Ajoutez l’action suivante à la première étape du cycle de vie d’approvisionnement
   - Windows
     ```yml
     provision:
       - uses: script
         with:
           shell: powershell
           run: |
                for ($i = 1; $i -le 10; $i++) {
                    $endpoint = (Invoke-WebRequest -Uri "http://localhost:4040/api/tunnels" | Select-String -Pattern 'https://[a-zA-Z0-9 -\.]*\.ngrok-free\.app').Matches.Value
                    if ($endpoint) {
                        break
                    }
                    sleep 10
                }
                if (-not $endpoint) {
                    echo "ERROR: Failed to find tunnel endpoint after 10 attempts."
                    exit 1
                } else {
                    echo "::set-teamsfx-env BOT_ENDPOINT=$endpoint"
                    echo "::set-teamsfx-env BOT_DOMAIN=$($endpoint.Substring(8))"
                }
     ```
   - Linux et macOS
     ```yml
     provision:
        - uses: script
            with:
            run: |
                for i in {1..10}; do
                    endpoint=$(curl -s localhost:4040/api/tunnels | grep -o 'https://[a-zA-Z0-9 -\.]*\.ngrok-free\.app')
                    if [ -n "$endpoint" ]; then
                        break
                    fi
                    sleep 10
                done
                if [ -z "$endpoint" ]; then
                    echo "ERROR: Failed to find tunnel endpoint after 10 attempts."
                    exit 1
                else
                    echo "::set-teamsfx-env BOT_ENDPOINT=$endpoint"
                    echo "::set-teamsfx-env BOT_DOMAIN=${endpoint:8}"
                fi
     ```
     
## Vérifier votre travail

Exécutez votre application localement afin de tester sa fonctionnalité :

1. Ouvrez le panneau **TEAMS TOOLKIT**. Dans le menu **DEVELOPMENT**, sélectionnez **Preview Your Teams App** (ou utilisez la clé `F5`), puis sélectionnez **Debug in Teams ()** avec le navigateur de votre choix.  
2. Teams Toolkit approvisionnera et exécutera votre application localement dans un navigateur.
3. Dans la boîte de dialogue d’installation de l’application dans le navigateur, sélectionnez **Ajouter** pour installer votre application Teams.  Teams ouvre une conversation avec votre bot installé.
4. Entrez ou sélectionnez la commande `resetPassword`.
5. Vérifiez que le bot répond avec une carte adaptative contenant des instructions de réinitialisation de mot de passe.
