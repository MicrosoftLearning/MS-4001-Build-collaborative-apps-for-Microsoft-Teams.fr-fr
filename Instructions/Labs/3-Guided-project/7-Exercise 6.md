---
lab:
  title: Créer un bot de base
  module: Exercise 6
---

# Exercice 6 : créer un bot de base

## Scénario
Supposons qu’il vous a été demandé d’aider l’équipe du support informatique à créer un bot Teams. Ce bot aura la capacité de récupérer l’abréviation d’un état donné et d’extraire les conditions météorologiques actuelles d’une zone spécifique en fonction de son code postal.

## Tâches de l’exercice
Dans cet exercice, vous allez utiliser le modèle de kit de ressources Teams pour créer un bot Teams simple. Ce bot utilisera la bibliothèque d’IA Teams pour traiter les messages avec les utilisateurs et inclure des interactions à l’aide de cartes adaptatives. Notez que cet exercice n’implique pas d’interactions entre la bibliothèque d’IA Teams et les LLM.

Pour réaliser l’exercice, vous devez effectuer les tâches suivantes :

1. Créez un bot Teams à l’aide du modèle de bot de base du kit de ressources Teams.
1. Intégrez la bibliothèque d’IA Teams.
1. Créez des cartes adaptatives.
1. Gérez les messages.

**Durée estimée :** 15 minutes

## Tâche 1 : créer un bot Teams à l’aide du modèle de kit de ressources Teams

**Objectif** : initialiser un projet de bot Teams de base à l’aide du kit de ressources Teams dans Visual Studio Code.

Utilisez le modèle de bot de commande pour créer un bot :

1. Ouvrez Visual Studio Code.
1. Dans la barre latérale, sélectionnez l’icône **Microsoft Teams** pour ouvrir le panneau **TEAMS TOOLKIT**.
1. Sélectionnez le bouton **Créer une application**.
1. Dans le menu **Nouveau projet**, sélectionnez **Bot**, puis **Bot de base** pour générer un bot simple.
1. Comme langage de programmation, sélectionnez **TypeScript**.
1. Comme **dossier de l’espace de travail**, sélectionnez ou créez un dossier pour stocker vos fichiers projet sur votre ordinateur.
1. Pour **Nom de l’application**, entrez **TypeAheadBot**, puis appuyez sur **Entrée**. Teams Toolkit génère la structure d’une nouvelle application et ouvre le dossier du projet dans Visual Studio Code.
1. Vous pouvez recevoir un message de Visual Studio Code qui vous demande si vous approuvez les auteurs des fichiers dans ce dossier. Sélectionnez le bouton **Oui, je fais confiance aux auteurs** pour continuer.
1. Passez en revue les répertoires et fichiers du projet à l’aide de l’Explorateur dans Visual Studio Code pour vous familiariser avec le code source.

## Tâche 2 : intégrer la bibliothèque d’IA Teams

**Objectif** : ajoutez la bibliothèque d’IA Teams à votre projet pour améliorer les fonctionnalités du bot.

1. Dans Visual Code, appuyez sur ``Ctrl + ` `` pour ouvrir le terminal.
1. Dans le terminal, exécutez la commande suivante pour installer la bibliothèque d’IA Teams et les packages Axios nécessaires. Le package Axios est un client HTTP basé sur les promesses que nous utiliserons dans cet exercice pour appeler des API web.
   ```shell
   npm install @microsoft/teams-ai axios --save
   ```
1. Créez un fichier `app.ts` dans le répertoire racine du projet. Ajoutez le code suivant au fichier pour définir et exporter l’objet `app`.
   ``` typescript
   // See https://aka.ms/teams-ai-library to learn more about the Teams AI library.
   import { Application } from "@microsoft/teams-ai";
   
   const app = new Application();
   
   export default app;
   ```
1. Ouvrez le fichier `index.ts` dans le répertoire racine du projet. Remplacez la référence à `TeamsBot` par l’objet `app`. Le fichier final de `index.ts` peut être référencé dans [index.ts](../../../Allfiles/Labs/Guided-Exercise6/index.ts).
   1. Remplacez la section *La boîte de dialogue principale de ce bot.* par le code suivant :
      
      Insérer un extrait de code
      ``` typescript
      import app from "./app";
      ``` 
      Supprimer l’extrait de code
      ``` typescript
      // This bot's main dialog.
      import { TeamsBot } from "./teamsBot";
      ```
   1. Supprimez l’extrait de code *Créer le bot qui gérera les messages entrants.*
      ``` typescript
      // Create the bot that will handle incoming messages.
      const bot = new TeamsBot();
      ```
   1. Modifiez l’extrait de code *Écouter les requêtes entrantes.* pour utiliser l’objet `app` pour répondre aux messages.
      ``` typescript
      // Listen for incoming requests.
      server.post("/api/messages", async (req, res) => {
        await adapter.process(req, res, async (context) => {
          await app.run(context); //replace bot with app object
        });
      });
      ```

## Tâche 3 : créer des cartes adaptatives

**Objectif** : concevez et implémentez des cartes adaptatives pour les interactions de données statiques et dynamiques au sein du bot.

1. Créez un dossier nommé `cards` dans le répertoire racine du projet.
1. Dans le dossier `cards`, créez un fichier nommé `staticSearchCard.ts` avec le contenu suivant. Le fichier final de `staticSearchCard.ts` peut être référencé dans [staticSearchCard.ts](../../../Allfiles/Labs/Guided-Exercise6/staticSearchCard.ts).
   ```typescript
   import { Attachment, CardFactory } from 'botbuilder';

    export function createStaticSearchCard(): Attachment {
        return CardFactory.adaptiveCard({
            $schema: 'http://adaptivecards.io/schemas/adaptive-card.json',
            version: '1.2',
            type: 'AdaptiveCard',
            body: [
                {
                    text: 'Please search for the list of state abbreviations from static list.',
                    wrap: true,
                    type: 'TextBlock'
                },
                {
                    columns: [
                        {
                            width: 'stretch',
                            items: [
                                {
                                    choices:
                                        [
                                            { title: 'Alabama', value: 'AL' },
                                            { title: 'Alaska', value: 'AK' },
                                            { title: 'Arizona', value: 'AZ' },
                                            { title: 'Arkansas', value: 'AR' },
                                            { title: 'California', value: 'CA' },
                                            { title: 'Colorado', value: 'CO' },
                                            { title: 'Connecticut', value: 'CT' },
                                            { title: 'Delaware', value: 'DE' },
                                            { title: 'Florida', value: 'FL' },
                                            { title: 'Georgia', value: 'GA' },
                                            { title: 'Hawaii', value: 'HI' },
                                            { title: 'Idaho', value: 'ID' },
                                            { title: 'Illinois', value: 'IL' },
                                            { title: 'Indiana', value: 'IN' },
                                            { title: 'Iowa', value: 'IA' },
                                            { title: 'Kansas', value: 'KS' },
                                            { title: 'Kentucky', value: 'KY' },
                                            { title: 'Louisiana', value: 'LA' },
                                            { title: 'Maine', value: 'ME' },
                                            { title: 'Maryland', value: 'MD' },
                                            { title: 'Massachusetts', value: 'MA' },
                                            { title: 'Michigan', value: 'MI' },
                                            { title: 'Minnesota', value: 'MN' },
                                            { title: 'Mississippi', value: 'MS' },
                                            { title: 'Missouri', value: 'MO' },
                                            { title: 'Montana', value: 'MT' },
                                            { title: 'Nebraska', value: 'NE' },
                                            { title: 'Nevada', value: 'NV' },
                                            { title: 'New Hampshire', value: 'NH' },
                                            { title: 'New Jersey', value: 'NJ' },
                                            { title: 'New Mexico', value: 'NM' },
                                            { title: 'New York', value: 'NY' },
                                            { title: 'North Carolina', value: 'NC' },
                                            { title: 'North Dakota', value: 'ND' },
                                            { title: 'Ohio', value: 'OH' },
                                            { title: 'Oklahoma', value: 'OK' },
                                            { title: 'Oregon', value: 'OR' },
                                            { title: 'Pennsylvania', value: 'PA' },
                                            { title: 'Rhode Island', value: 'RI' },
                                            { title: 'South Carolina', value: 'SC' },
                                            { title: 'South Dakota', value: 'SD' },
                                            { title: 'Tennessee', value: 'TN' },
                                            { title: 'Texas', value: 'TX' },
                                            { title: 'Utah', value: 'UT' },
                                            { title: 'Vermont', value: 'VT' },
                                            { title: 'Virginia', value: 'VA' },
                                            { title: 'Washington', value: 'WA' },
                                            { title: 'West Virginia', value: 'WV' },
                                            { title: 'Wisconsin', value: 'WI' },
                                            { title: 'Wyoming', value: 'WY' }
                                        ],
                                    style: 'filtered',
                                    placeholder: 'Search for a state abbreviation',
                                    id: 'choiceSelect',
                                    type: 'Input.ChoiceSet'
                                }
                            ],
                            type: 'Column'
                        }
                    ],
                    type: 'ColumnSet'
                }
            ],
            actions: [
                {
                    id: 'staticSubmit',
                    type: 'Action.Submit',
                    title: 'Submit',
                    data: {
                        verb: 'StaticSubmit'
                    }
                }
            ]
        });
    }
   ```

1. Dans le dossier `cards`, créez un fichier nommé `dynamicSearchCard.ts` avec le contenu suivant. Le fichier final de `dynamicSearchCard.ts` peut être référencé dans [dynamicSearchCard.ts](../../../Allfiles/Labs/Guided-Exercise6/dynamicSearchCard.ts).
   ```typescript
   import { Attachment, CardFactory } from 'botbuilder';

   export function createDynamicSearchCard(): Attachment {
        return CardFactory.adaptiveCard({
            $schema: 'http://adaptivecards.io/schemas/adaptive-card.json',
            version: '1.6',
            type: 'AdaptiveCard',
            body: [
                {
                    text: 'Please search for temperature using ZIP Code.',
                    wrap: true,
                    type: 'TextBlock'
                },
                {
                    columns: [
                        {
                            width: 'stretch',
                            items: [
                                {
                                    choices: [],
                                    'choices.data': {
                                        type: 'Data.Query',
                                        dataset: 'locations',
                                        value: 'value'
                                    },
                                    id: 'choiceSelect',
                                    type: 'Input.ChoiceSet',
                                    placeholder: 'ZIP Code',
                                    label: 'ZIP Code search',
                                    isRequired: false,
                                    errorMessage: 'There was an error test.',
                                    isMultiSelect: false,
                                    style: 'filtered'                                
                                }
                            ],
                            type: 'Column'
                        }
                    ],
                    type: 'ColumnSet'
                }
            ],
            actions: [
                {
                    id: 'submitdynamic',
                    type: 'Action.Submit',
                    title: 'Submit',
                    data: {
                        verb: 'DynamicSubmit'
                    }
                }
            ]
        });
    }

   ```

## Tâche 4 : gérer les messages

**Objectif** : développez la capacité du bot à répondre aux entrées utilisateur et à interagir via des cartes adaptatives.

Dans cette étape, nous allons ajouter la fonctionnalité de réponse aux messages à l’objet `app`.

1. Ouvrez le fichier `app.ts` .
1. Sous le code qui crée l’objet d’application `const app = new Application();`, ajoutez le code suivant pour gérer les différents messages.
    ``` typescript
    interface SubmitData {
        choiceSelect?: string;
    }

    app.message(/static/i, async (context, _state) => {
        const attachment = createStaticSearchCard();
        await context.sendActivity({ attachments: [attachment] });
    });

    app.message(/dynamic/i, async (context, _state) => {
        const attachment = createDynamicSearchCard();
        await context.sendActivity({ attachments: [attachment] });
    });

    // Listen for query from dynamic search card
    app.adaptiveCards.search('locations', async (context: TurnContext, state: TurnState, query) => {
        // Format search results
        const locations: AdaptiveCardSearchResult[] = [];
        // Execute query
        const searchQuery = query.parameters['queryText'] ?? '';
        if (searchQuery.length < 4) {
            return locations;
        }   

        const response = await axios.get(
            `https://zip-api.eu/api/v1/codes/postal_code=US-${searchQuery}*`
        );

        interface DataObject {
            state: string;
            place_name: string;
            postal_code: string;
            lat:string
            lng:string
        };

        //response data is an array of objects or an object
        response.data = Array.isArray(response.data) ? response.data : [response.data];
        response.data.forEach((obj: DataObject) => {
            const result: AdaptiveCardSearchResult = {
                title: `${obj.postal_code} - ${obj.place_name}, ${obj.state}`,
                value: `${obj.postal_code}|${obj.place_name}|${obj.lat}|${obj.lng}`
            };
            locations.push(result);
        });

        // Return search results
        return locations;
    });

    // Listen for submit buttons
    app.adaptiveCards.actionSubmit('DynamicSubmit', async (context, _state, data: SubmitData) => {
        let [postalCode, placeName, lat, lon] = data.choiceSelect?.split('|') ?? [];
        await context.sendActivity(`The dynamically selected place is: ${placeName}`);
        const weatherLocation = await axios.get(
            `https://api.weather.gov/points/${lat},${lon}`
        );
        const forcast = await axios.get(weatherLocation.data.properties.forecast);
        await context.sendActivity(`The weather in ${placeName}: ${forcast.data.properties.periods[0].detailedForecast}`);
        });

        app.adaptiveCards.actionSubmit('StaticSubmit', async (context, _state, data: SubmitData) => {
        await context.sendActivity(`The statically selected option is: ${data.choiceSelect}`);
        });

        // Listen for ANY message to be received. MUST BE AFTER ANY OTHER HANDLERS
        app.activity(ActivityTypes.Message, async (context, _state) => {
        await context.sendActivity(`Try saying "static search" or "dynamic search".`);
    });
    ```

1. En haut du fichier `app.ts`, mettez à jour les références associées comme suit. Le fichier final de `app.ts` peut être référencé dans [app.ts](../../../Allfiles/Labs/Guided-Exercise6/app.ts).
    
    Mis à jour
    ```` typescript
    import { ActivityTypes, TurnContext } from "botbuilder";
    import { createStaticSearchCard } from "./cards/staticSearchCard";
    import { createDynamicSearchCard } from "./cards/dynamicSearchCard";
    import axios from "axios";
    // See https://aka.ms/teams-ai-library to learn more about the Teams AI library.
    import { Application, TurnState, AdaptiveCardSearchResult } from "@microsoft/teams-ai";
    ````
    Précédent
    ```` typescript
    // See https://aka.ms/teams-ai-library to learn more about the Teams AI library.
    import { Application } from "@microsoft/teams-ai";
    ````    

## Vérifier votre travail

Exécutez votre application localement afin de tester sa fonctionnalité :

1. Ouvrez le panneau **TEAMS TOOLKIT**. Dans le menu **DEVELOPMENT**, sélectionnez **Preview Your Teams App** (ou utilisez la clé `F5`), puis sélectionnez **Debug in Teams ()** avec le navigateur de votre choix.  
1. Teams Toolkit approvisionnera et exécutera votre application localement dans un navigateur.
1. Dans la boîte de dialogue d’installation de l’application dans le navigateur, sélectionnez **Ajouter** pour installer votre application Teams.  Teams ouvre une conversation avec votre bot installé.
1. Dans la boîte de dialogue du message, entrez `static search` et appuyez sur Entrée. Le bot retourne une carte adaptative.
1. Dans la zone d’entrée, sélectionnez ou entrez un nom d’état, puis sélectionnez le bouton **Envoyer**. Le bot retourne l’abréviation de ce nom d’état. ![Capture d’écran de la recherche statique de la carte adaptative](../../media/static-search.png)
1. Dans la boîte de dialogue du message, entrez `dynamic search` et appuyez sur Entrée.
1. Dans la boîte de dialogue d’entrée, entrez un code postal américain, puis sélectionnez le bouton **Envoyer**. Le bot retourne la météo actuelle dans cette zone. ![Capture d’écran de la recherche dynamique de la carte adaptative](../../media/dynamic-search.png)