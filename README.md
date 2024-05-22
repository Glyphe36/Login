## Page de connexion pour PageGitHub.

Cela permet de cree une page de connexion a une page html static git hub sans serveur ni sql.
La methode fonctionne aussi sur dorpbox ou sur AWS amazon s3

## Configuration

0. Creation d'une page de connexion `index.html`.
0. Charger la sur votre navigateur, puis entrer le mot de passe que vous desirer
0. Un message d'erreur vas s'afficher dans le placeholder, ainsi que des caractere dans l'url copier les apres #
0. Cree un dossier avec ces caractere ex:`1a3c9560cc42ab96f5711bd7100abfd18a274892` puis cree un fichier`index.hml`. 
0. Vous pouvez cree votre contenu dans ce `index.html` puis le charger sur votre github

Voici la structure final:

```
- index.html
- style.css
- 1a3c9560cc42ab96f5711bd7100abfd18a274892      <-- SHA1 hash de votre password               
  \ - index.html                                <-- Votre contenu securiser
```

## Personnalisation

html:
```
<input id="password" type="password" placeholder="Mot de passe" /> <br />
<button id="loginbutton" type="button">Enter</button>
<p id="wrongPassword" style="display: none">Mot de passe incorecte</p>
```
 
## script obligatoire:
```
<script type="text/javascript" src="https://code.jquery.com/jquery-1.12.0.min.js"></script>
<script type="text/javascript" src="https://rawcdn.githack.com/chrisveness/crypto/7067ee62f18c76dd4a9d372a00e647205460b62b/sha1.js"></script>
<script type="text/javascript">
    "use strict";
    function loadPage(pwd) {
        var hash = pwd;
        hash = Sha1.hash(pwd);
        var url = hash + "/index.html";
        $.ajax({
            url: url,
            dataType: "html",
            success: function (data) {
                window.location = url;
            },
            error: function (xhr, ajaxOptions, thrownError) {
                parent.location.hash = hash;
                //$("#wrongPassword").show();
                $("#password").attr("placeholder", "wrong password");
                $("#password").val("");
            }
        });
    }
    $("#loginbutton").on("click", function () {
        loadPage($("#password").val());
    });
    $("#password").keypress(function (e) {
        if (e.which == 13) {
            loadPage($("#password").val());
        }
    });
    $("#password").focus();
</script>
```

## Description du script:

0.`use strict` indique au navigateur web d'utiliser le mode "strict" pour interpréter le code JavaScript, ce qui permet de détecter les erreurs de syntaxe et de sémantique plus facilement.
0.La fonction `loadPage(pwd)` est appelée lorsque l'utilisateur soumet le formulaire de connexion. Elle prend le mot de passe saisi par l'utilisateur en tant que paramètre pwd.
0.Dans la fonction `loadPage(pwd)`, le mot de passe est d'abord haché en utilisant la fonction `Sha1.hash(pwd)` pour obtenir une chaîne de caractères hashée.
0.Ensuite, la chaîne de caractères hashée est utilisée pour construire l'URL de la page d'accueil en ajoutant /index.html à la fin.
0.La fonction `$.ajax({...})` est utilisée pour envoyer une requête HTTP GET à l'URL de la page d'accueil construite précédemment.
0.Si la requête HTTP GET réussit, la fonction `success(data)` est appelée et la page d'accueil est chargée en utilisant `window.location = url;`.
0.Si la requête HTTP GET échoue (par exemple, si le mot de passe saisi est incorrect), la fonction `error(xhr, ajaxOptions, thrownError)` est appelée et l'attribut placeholder de l'élément input du mot de passe est mis à jour pour afficher `"wrong password"` (mot de passe incorrect). L'élément input du mot de passe est également vidé en utilisant `$("#password").val("");`.
0.En dehors de la fonction `loadPage(pwd)`, l'événement click de l'élément button avec l'ID loginbutton est géré en utilisant `$("#loginbutton").on("click", function () {...});`. Lorsque l'utilisateur clique sur le bouton "Se connecter", la fonction `loadPage($("#password").val());` est appelée pour soumettre le formulaire de connexion.
0.L'événement keypress de l'élément input du mot de passe est également géré en utilisant `$("#password").keypress(function (e) {...});`. Lorsque l'utilisateur appuie sur la touche "Entrée" dans le champ du mot de passe, la fonction `loadPage($("#password").val());` est appelée pour soumettre le formulaire de connexion.
0.Enfin, l'élément input du mot de passe est mis en focus en utilisant `$("#password").focus();` pour que l'utilisateur puisse saisir son mot de passe plus facilement.

## Securiter

0. Si votre service d'hébergement propose une liste d'annuaires, un visiteur peut contourner la protection.
0. Il n'y a pas de protection contre l'attaque par la force brutale. Choisissez un mot de passe très long et difficile à deviner. 
