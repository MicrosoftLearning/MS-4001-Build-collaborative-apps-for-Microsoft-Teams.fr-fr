# Exercice 3 : Exécuter votre application Teams

Dans cet exercice, vous exécuterez l’application Teams localement.

## Tâche 1 : Se connecter à Microsoft 365

1. Dans Visual Studio Code, ouvrez **Teams Toolkit** et sélectionnez **Se connecter à Microsoft 365**.
    :::image type="content" source="../../media/sign-in.png" alt-text="Capture d’écran du bouton de connexion dans Teams Toolkit pour M365":::
2. Dans la boîte de dialogue contextuelle, sélectionnez **Se connecter**. Vous êtes redirigé vers un navigateur pour vous connecter à l'aide de vos identifiants de compte Microsoft 365.  Entrez vos informations d'identification d'administrateur.
3. Lorsque vous êtes connecté avec succès, fermez le navigateur et revenez à Visual Studio Code. Un message *activé pour le chargement indépendant* s’affiche sous votre compte sur Teams Toolkit pour indiquer que votre locataire est configuré correctement.
    :::image type="content" source="../../media/sideload-enabled.png" alt-text="Capture d’écran de la note de chargement indépendant activée dans Teams Toolkit.":::

## Tâche 2 : Exécutez l’application localement.

Exécutons le projet et voyons à quoi il ressemblera dans Microsoft Teams.

1. Dans la barre latérale, sélectionnez l’icône **Débogage** pour ouvrir le panneau **EXÉCUTER ET DÉBOGUER**.
2. Dans le panneau **EXÉCUTER ET DÉBOGUER**, sélectionnez **Chrome** ou **Edge** dans la zone de liste déroulante comme navigateur que vous souhaitez utiliser pour déboguer votre bot.  Sélectionnez ensuite le bouton **Lecture** (ou sélectionnez la touche **F5**) pour démarrer la session de débogage.
3. Teams Toolkit effectue la vérification des conditions préalables (ce qui peut prendre une minute ou deux).
4. Ensuite, Teams Toolkit ouvre Microsoft Teams dans le navigateur que vous avez sélectionné.
5. Connectez-vous avec le même compte **Microsoft 365** que celui que vous avez utilisé pour vous connecter à Teams Toolkit.
6. Teams affiche ensuite une option permettant d’installer votre application. Sélectionnez **Ajouter** pour afficher un aperçu de l’application.
7. Lorsque vous affichez un aperçu, le nom de votre application est suffixe avec le *débogage local*. Le suffixe indique que l’application fonctionne localement et n’a pas été déployée.
    :::image type="content" source="../../media/add-teams-app.png" alt-text="Capture d’écran de la boîte de dialogue pour l’installation d’une application Teams.":::
8. Pour en savoir plus sur la structure du projet et sur l’utilisation et la personnalisation de votre application, ouvrez le fichier **README.md** sous le dossier du projet.

Vous avez maintenant créé une application Teams à partir d’un exemple et l’avez exécutée localement.
