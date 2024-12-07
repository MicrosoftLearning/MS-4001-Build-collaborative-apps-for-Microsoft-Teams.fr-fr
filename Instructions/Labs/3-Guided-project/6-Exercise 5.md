---
lab:
  title: Créer un bot avec l’IA
  module: Exercise 4
---

# Exercice 4 : créer un bot avec l’IA

## Scénario

Imaginez que vous êtes membre de l’équipe du support informatique. Vous réalisez que la compilation du rapport hebdomadaire est un processus très mécanique et chronophage. Vous souhaitez créer un bot IA dans MS Teams. En discutant simplement du travail et des tâches hebdomadaires de la semaine à venir avec le bot dans une conversation, il peut générer un rapport hebdomadaire bien mis en forme. Cela pourrait améliorer considérablement l’efficacité du travail.

## Tâches de l’exercice

Pour réaliser l’exercice, vous devez effectuer les tâches suivantes :

1. Créer un bot à l’aide de la bibliothèque d’IA Teams
1. Se connecter au service OpenAI
1. Implémenter la fonctionnalité de code
1. Mettre à jour les invites

**Durée estimée :** 20 minutes

## Prérequis

Pour exécuter le modèle de bot de conversation IA sur votre ordinateur de développement local, vous devez non seulement répondre aux exigences de ressources mentionnées dans le labo précédent, mais aussi disposer d’un compte OpenAI. Il peut s’agir d’un compte [OpenAI](https://platform.openai.com/) ou [Azure OpenAI](https://aka.ms/oai/access).

## Tâche 1 : créer un bot à l’aide de la bibliothèque d’IA Teams

**Objectif :** configurez un nouveau projet de bot à l’aide de la bibliothèque d’IA Teams et familiarisez-vous avec la structure et les fichiers du projet.  

Utilisez le modèle de bot de conversation IA pour créer un bot :

1. Ouvrez Visual Studio Code.
1. Dans la barre latérale, sélectionnez l’icône **Microsoft Teams** pour ouvrir le panneau **TEAMS TOOLKIT**.
1. Sélectionnez le bouton **Créer une application**.
1. Dans le menu **Nouveau projet**, sélectionnez **Copilot personnalisé**, puis **Chatbot IA de base** pour générer un bot de commande.
1. Comme langage de programmation, sélectionnez **TypeScript**.
1. Pour **Service pour le grand modèle de langage (LLM)**, choisissez **Azure OpenAI** ou **OpenAI** en fonction de votre compte LLM.
1. Appuyez d’abord sur **Entrée** pour ignorer le paramètre de grand modèle de langage (LLM). Vous configurez la clé OpenAI dans l’étape suivante.
1. Comme **dossier de l’espace de travail**, sélectionnez ou créez un dossier pour stocker vos fichiers projet sur votre ordinateur.
1. Pour **Nom de l’application**, entrez **WeeklyReportChatBot**, puis appuyez sur **Entrée**. Teams Toolkit génère la structure d’une nouvelle application et ouvre le dossier du projet dans Visual Studio Code.
1. Vous pouvez recevoir un message de Visual Studio Code qui vous demande si vous approuvez les auteurs des fichiers dans ce dossier. Sélectionnez le bouton **Oui, je fais confiance aux auteurs** pour continuer.
1. Passez en revue les répertoires et fichiers du projet à l’aide de l’Explorateur dans Visual Studio Code pour vous familiariser avec le code source.

## Tâche 2 : se connecter au service OpenAI

**Objectif :** configurez votre bot pour vous connecter à un service OpenAI, via OpenAI directement ou à l’aide d’Azure OpenAI, en configurant les clés d’API et les points de terminaison nécessaires.  

### Utiliser un compte OpenAI
1. Ouvrez le fichier `.env.local.user` à partir du dossier `env`.
1. Dans le fichier *env/.env.local.user*, renseignez votre clé OpenAI `SECRET_OPENAI_API_KEY=<your-key>`.
1. Ouvrez le fichier `app.ts` à partir du dossier `src`.
1. (Facultatif) Étant donné que cette démonstration utilise les fonctionnalités de planification du modèle, gpt-4 offre une amélioration significative par rapport à la sortie gpt-3.5 par défaut. Dans le fichier *src/config.ts*, mettez à jour la valeur de propriété *openAIModelName* de `"gpt-3.5-turbo"` vers `"gpt-4"` ou `"gpt-4-turbo"`.

### Utiliser Azure OpenAI
1. Ouvrez le fichier `.env.local.user` à partir du dossier `env`.
1. Dans le fichier *env/.env.local.user*, renseignez votre clé Azure OpenAI `SECRET_AZURE_OPENAI_API_KEY=<azure-openai-api-key>`, votre point de terminaison Azure OpenAI `AZURE_OPENAI_ENDPOINT=<azure-openai-endpoint>` et le nom du déploiement Azure OpenAI `AZURE_OPENAI_DEPLOYMENT_NAME=<azure-openai-deployment-name>`.

## Tâche 3 : implémenter la fonctionnalité de code

**Objectif :** développez les principales fonctionnalités de votre bot en modifiant le fichier app.ts, en ajoutant les importations nécessaires et en implémentant la logique de réponse pour gérer les interactions utilisateur.  

1. Ouvrez le fichier *src/app/app.ts*. Nous allons modifier ce fichier selon les étapes suivantes. Le fichier final peut être référencé dans [app.ts](../../../Allfiles/Labs/Guided-Exercise5/app.ts).
1. Ajoutez l’importation `TurnContext` depuis `botbuilder` 
    ```typescript
    import { MemoryStorage, TurnContext } from "botbuilder";
    ```
1. Ajoutez les importations `DefaultConversationState` et `TurnState` depuis `@microsoft/teams-ai`
    ```typescript
    // See https://aka.ms/teams-ai-library to learn more about the Teams AI library.
    import { Application, ActionPlanner, OpenAIModel, PromptManager, DefaultConversationState, TurnState } from "@microsoft/teams-ai";
    ```
1. Dans le fichier *src/app.ts* avant la section *Créer des composants IA*, ajoutez l’interface `ProjectInformation` et la définition `ApplicationTurnState`.
    ```typescript
    // Register project information item related handlers
    interface ProjectInformation {
      projectName: string;
      tasksAccomplished: string;
      tasksComing: string;
      blockingIssues: string;
    }

    // Strongly type the applications turn state
    interface ConversationState extends DefaultConversationState {
      greeted: boolean;
      projectInformation: ProjectInformation;
    }
    type ApplicationTurnState = TurnState<ConversationState>;

    // Create AI components
    ```
1. Dans le fichier *src/app.ts* après la section *Définir le stockage et l’application*, ajoutez la réponse du bot aux messages.
    ```typescript
    // List for /reset command and then delete the conversation state
    app.message('/reset', async (context: TurnContext, state: ApplicationTurnState) => {
      state.deleteConversationState();
      await context.sendActivity("Your project information has been cleared.");
    });

    // Define the method for updating project information
    app.ai.action("updateProjectInformation", async (context: TurnContext, state: ApplicationTurnState, parameters: ProjectInformation) => {
      const conversation = ensureStateInitialized(state);
      if (parameters){
        if (parameters.projectName) {
          conversation.projectInformation.projectName = parameters.projectName;
        }
        if (parameters.tasksAccomplished) {
          conversation.projectInformation.tasksAccomplished = parameters.tasksAccomplished;
        } 
        if (parameters.tasksComing) {
          conversation.projectInformation.tasksComing = parameters.tasksComing;
        }
        if (parameters.blockingIssues) {
          conversation.projectInformation.blockingIssues = parameters.blockingIssues;
        }
        return `Project information was updated. Think about your next action`;
      }
    });

    // This method is used to make sure that the conversation state is initialized.
    function ensureStateInitialized(state: ApplicationTurnState): ConversationState {
      if (state.conversation.projectInformation == undefined) {
        state.conversation.projectInformation = {
          projectName: "",
          tasksAccomplished: "",
          tasksComing: "",
          blockingIssues: "",
        };
      }
      return state.conversation;
    }
    ```

## Tâche 4 : mises à jour des invites

**Objectif :** affinez les invites conversationnelles du bot et configurer l’appel de fonction pour améliorer la qualité et l’efficacité de l’interaction dans la génération de rapports hebdomadaires.

1. Mettez à jour le fichier `skprompt.txt` dans le dossier *src/prompts/chat*.  Le fichier final peut être référencé dans [skprompt.txt](../../../Allfiles/Labs/Guided-Exercise5/skprompt.txt).
    ```txt
    You are a Teams Bot. Here is how you will act.
    Team Bot will adopt an encouraging and positive tone in all its interactions. This will be reflected in the creation of status report emails, ensuring that they are not only informative but also boost morale and foster a joyous team spirit. The language used will be engaging and supportive, aiming to excite and inspire the team while maintaining a professional undercurrent appropriate for the communication between a project manager and their team and stakeholders in a professional corporate setting. The Teams Bot will always ask for information from the user when it is not provided. 
    # Teams Bot will ask for the following project information to make status report emails:
    1. project name
    2. tasks accomplished this week
    3. tasks coming next week
    4. blocking issues

    # Status report task description like SCRUM style summary

    # Then, the users will type in the parameters and the bot will make the email.

    # For the first time, users are informed that they can clear the entered ProjectInformation with /reset command.

    # THE SUMMARY MUST BE:
    - G RATED
    - WORKPLACE / FAMILY SAFE
    NO SEXISM, RACISM OR OTHER BIAS/BIGOTRY.

    project information:
    {{$conversation.projectInfomation}}

    Typescript Interfaces:
    interface ProjectInformation {
        projectName: string;
        tasksAccomplished: string;
        tasksComing: string;
        blockingIssues: string;
    }
    ```
1. Mettez à jour les paramètres `max_tokens` et `temperature` dans le fichier `src/prompts/chat/config.json`. Ajoutez également un paramètre de nœud d’augmentation. Le résultat final est le suivant :  Il peut être référencé dans [config.json](../../../Allfiles/Labs/Guided-Exercise5/config.json).
    ```json
    {
      "schema": 1,
      "description": "AI Bot",
      "type": "completion",
      "completion": {
        "max_tokens": 2500,
        "temperature": 0.1,
        "top_p": 0.0,
        "presence_penalty": 0.6,
        "frequency_penalty": 0.0
      },
      "augmentation": {
        "augmentation_type": "monologue"
      }
    }
    ```
1. Créez un dossier nommé `actions.json` dans le dossier *src/prompts/chat*. Le contenu du fichier est le suivant. Ce fichier définit les méthodes à utiliser par l’IA. Il peut être référencé dans [actions.json](../../../Allfiles/Labs/Guided-Exercise5/actions.json).
    ```json
    [
        {
            "name": "updateProjectInformation",
            "description": "updates the information for the existing project",
            "parameters": {
                "type": "object",
                "properties": {
                    "projectName": {
                        "type": "string",
                        "description": "The name of the project"
                    },
                    "tasksAccomplished": {
                        "type": "string",
                        "description": "tasks that have been accomplished"
                    },
                    "tasksComing": {
                        "type": "string",
                        "description": "tasks that are coming up"
                    },
                    "blockingIssues": {
                        "type": "string",
                        "description": "any blocking issues"
                    }
                }
            }
        }
    ]
    ```

## Vérifier votre travail

Exécutez votre application localement afin de tester sa fonctionnalité :

1. Ouvrez le panneau **TEAMS TOOLKIT**. Dans le menu **DEVELOPMENT**, sélectionnez **Preview Your Teams App** (ou utilisez la clé `F5`), puis sélectionnez **Debug in Teams ()** avec le navigateur de votre choix.  
2. Teams Toolkit approvisionnera et exécutera votre application localement dans un navigateur.
3. Dans la boîte de dialogue d’installation de l’application dans le navigateur, sélectionnez **Ajouter** pour installer votre application Teams.  Teams ouvre une conversation avec votre bot installé.
4. Après avoir entré des informations de salutation, suivez les invites pour entrer le nom du projet, les tâches terminées, les tâches inachevées et les tâches bloquantes. Le bot IA génère ensuite un rapport hebdomadaire similaire à la capture d’écran ci-dessous.
5. Vérifiez que le bot répond avec la bonne réponse comme suit. ![Capture d’écran du rapport hebdomadaire généré par l’IA](../../media/ai-weekly-report.png)
