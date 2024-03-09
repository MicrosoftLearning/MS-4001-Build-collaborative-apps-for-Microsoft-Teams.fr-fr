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

1. Dans Visual Studio Code, accédez à **Teams Toolkit** dans la barre latérale.
2. Dans Teams Toolkit, dans le menu **Development**, sélectionnez **Create a New App**.
3. Dans le menu **New Project**, sélectionnez **Bot**, puis **Chat Command** pour générer un bot de commande.
4. Comme langage de programmation, sélectionnez **TypeScript**.
5. Comme **dossier de l’espace de travail**, sélectionnez ou créez un dossier pour stocker vos fichiers projet sur votre ordinateur.
6. Comme **nom d’application**, entrez **SupportCommandBot**, puis appuyez sur **Entrée**.  Teams Toolkit crée et génère des modèles de votre projet de bot.
7. Passez en revue les répertoires et fichiers du projet à l’aide de l’Explorateur dans Visual Studio Code pour vous familiariser avec le code source.

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
3. Sur la ligne 20, mettez à jour le tableau `commands` de la propriété `command` en incluant une instruction pour initialiser le nouveau gestionnaire : `new ResetPasswordCommandHandler().  The updated `L’objet « command » mis à jour doit se présenter comme ceci :

   ```json
   command: {    enabled: true,    commands: [new HelloWorldCommandHandler(), new ResetPasswordCommandHandler()],  },
    ```

## Vérifier votre travail

Exécutez votre application localement afin de tester sa fonctionnalité :

1. Dans le menu **LIFECYCLE** , sélectionnez **Preview Your Teams App** (ou utilisez la clé `F5`), puis sélectionnez **Debug in Teams ()** avec le navigateur de votre choix.  
2. Teams Toolkit approvisionnera et exécutera votre application localement dans un navigateur.
3. Dans la boîte de dialogue d’installation de l’application dans le navigateur, sélectionnez **Ajouter** pour installer votre application Teams.  Teams ouvre une conversation avec votre bot installé.
4. Entrez ou sélectionnez la commande `resetPassword`.
5. Vérifiez que le bot répond avec une carte adaptative contenant des instructions de réinitialisation de mot de passe.
