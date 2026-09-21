# PowerShell bloque le script

🇬🇧 [English version](README_POWERSHELL.md)

Deux mécanismes différents peuvent bloquer un script téléchargé. Suis les étapes dans l'ordre.

## 1. Débloquer le fichier téléchargé

Windows marque tout fichier venu d'Internet (« Mark of the Web »). PowerShell refuse alors de l'exécuter avec un message du genre « n'est pas signé numériquement ».

- Clic droit sur le `.ps1` → **Propriétés** → coche **Débloquer** en bas de l'onglet Général → **OK**.
- Ou en PowerShell, depuis le dossier du script :
  ```powershell
  Unblock-File .\NomDuScript.ps1
  ```
- Si le script vient d'une archive `.zip`, débloque le `.zip` **avant** de l'extraire, sinon tous les fichiers extraits héritent du blocage. Pour débloquer un dossier déjà extrait :
  ```powershell
  Get-ChildItem -Recurse | Unblock-File
  ```

## 2. Autoriser l'exécution de scripts

Si le message est « l'exécution de scripts est désactivée sur ce système », c'est la politique d'exécution qui est en cause. Vérifie-la d'abord :

```powershell
Get-ExecutionPolicy -List
```

Puis autorise les scripts pour ton compte uniquement, sans droits administrateur :

```powershell
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned
```

Confirme avec `O` (ou `Y`). Avec `RemoteSigned`, les scripts locaux s'exécutent normalement, et ceux téléchargés doivent avoir été débloqués (étape 1).

## 3. Ou lancer le script une seule fois, sans rien modifier

```powershell
powershell -ExecutionPolicy Bypass -File .\NomDuScript.ps1
```

Le contournement ne vaut que pour cette exécution et ne change aucun réglage du système.

## 4. Lancer en administrateur si le script l'exige

Clic droit sur PowerShell (ou Windows Terminal) → **Exécuter en tant qu'administrateur**, puis `cd` vers le dossier du script avant de le lancer. Un script qui touche à la sécurité, aux pilotes ou au réseau refusera de tourner sans ces droits, même une fois débloqué.

## Si ça bloque encore

- Dans `Get-ExecutionPolicy -List`, si `MachinePolicy` ou `UserPolicy` n'est pas `Undefined`, la politique est imposée par une stratégie de groupe (PC professionnel ou scolaire) : tu ne peux pas la changer toi-même, il faut passer par l'administrateur du poste.
- Si SmartScreen ou Microsoft Defender affiche un avertissement, clique sur **Informations complémentaires** puis **Exécuter quand même**. Si Defender met le fichier en quarantaine, vérifie d'abord que tu l'as téléchargé depuis le bon dépôt.
- Double-cliquer sur un `.ps1` l'ouvre dans le Bloc-notes au lieu de l'exécuter : lance-le depuis une console PowerShell, ou par clic droit → **Exécuter avec PowerShell**.

> Lis toujours un script avant de l'exécuter, surtout avec `-ExecutionPolicy Bypass`.
