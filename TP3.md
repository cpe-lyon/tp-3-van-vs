**TP3**

**Exercice 1 : Utilisateurs, groupes et permissions**

1. **Utilisez la commande groupadd pour créer deux groupes dev et infra**

```consol
    User@localhost:~$ sudo groupadd dev
    User@localhost:~$ sudo groupadd infra
```

2. **Créez ensuite 4 utilisateurs aliace, bob, charlie, dave avec la commande useradd, en demandant la création de leur dossier personnel et avec bash pour shell**

'''consol
    User@localhost:~$ sudo useradd -m alice
    User@localhost:~$ sudo usermod --shell /bin/bash alice
'''

'''consol
    User@localhost:~$ sudo useradd -m bob
    User@localhost:~$ sudo usermod --shell /bin/bash bob
'''

'''consol
    User@localhost:~$ sudo useradd -m charlie
    sudo usermod --shell /bin/bash charlie
'''

'''consol
    User@localhost:~$ sudo useradd -m dave
    User@localhost:~$ sudo usermod --shell /bin/bash dave
'''

3. **Ajoutez les utilisateurs dans les groupes créés :**
    **- alice, bob, dave dans dev**
    **- bob, charlie, dave dans infra**

    User@localhost:~$ sudo usermod -a -G dev alice
    User@localhost:~$ sudo usermod -a -G dev bob
    User@localhost:~$ sudo usermod -a -G dev dave

    User@localhost:~$ sudo usermod -a -G infra bob
    User@localhost:~$ sudo usermod -a -G infra charlie
    User@localhost:~$ sudo usermod -a -G infra dave

4. **Donnez deux moyens d’afficher les membres de infra**

    User@localhost:~$ getent group infra
    User@localhost:~$ grep infra /etc/group
    infra:x:1003:dave

5. **Faites de dev le groupe propriétaire des répertoires /home/alice et /home/bob et de infra le groupe propriétaire de /home/charlie et /home/dave**

    User@localhost:~$ sudo chown :dev home/alice/
    User@localhost:~$ sudo chown :dev home/bob/
    User@localhost:~$ sudo chown :infra home/charlie/
    User@localhost:~$ sudo chown :infra home/dave/

6. **Remplacez le groupe primaire des utilisateurs :**
    **- dev pour alice et bob**
    **- infra pour charlie et dave**

    User@localhost:~$ sudo urermod -g dev alice
    User@localhost:~$ sudo usermod -g dev bob
    User@localhost:~$ sudo usermod -g infra charlie
    User@localhost:~$ sudo usermod -g infra dave

    User@localhost:/home$ id alice
    uid=1002(alice) gid=1002(dev) groups=1002(dev)
    User@localhost:/home$ id bob
    uid=1003(bob) gid=1002(dev) groups=1002(dev)
    User@localhost:/home$ id charlie
    uid=1004(charlie) gid=1003(infra) groups=1003(infra)
    User@localhost:/home$ id dave
    uid=1005(dave) gid=1003(infra) groups=1003(infra)


7. **Créez deux répertoires /home/dev et /home/infra pour le contenu commun aux membres de chaque groupe, et mettez en place les permissions leur permettant d’écrire dans ces dossiers.**

    User@localhost:~$ sudo mkdir dev /home/dev
    User@localhost:~$ sudo chown :dev /home/dev
    User@localhost:~$ sudo chmod g+w /home/dev

    User@localhost:~$ sudo mkdir dev /home/infra
    User@localhost:~$ sudo cjown :infra /home/infra
    User@localhost:~$ sudo chmod g+w /home/infra
    User@localhost:~$ ls -l
    drwxrwxr-x 2 root    dev       6 Sep 29 06:23 dev
    drwxrwxr-x 2 root    infra     6 Sep 26 19:57 infra

8. **Comment faire pour que, dans ces dossiers, seul le propriétaire d’un fichier ait le droit de renommer ou supprimer ce fichier ?**

    User@localhost:~$ sudo chmod g+t /home/dev
    User@localhost:~$ sudo chmod g+t /home/infra

9. **Pouvez-vous ouvrir une session en tant que alice ? Pourquoi ?**

    User@localhost:~$ su alice

Non, car il est inactif et ne possède pas de mot de passe.

10. **Activez le compte de l’utilisateur alice et vérifiez que vous pouvez désormais vous connecter avec son compte.**

    User@localhost:~$ sudo passwd alice 
    New password:
    Retype new password:
    passwd: password updated successfully

    User@localhost:~$ su alice
    Password:
    alice@localhost:/home/User$

//mdp = alice

11. **Comment obtenir l’uid et le gid de alice ?**

A l'aide de la commande id

    User@localhost:~$ id alice
    uid=1002(alice) gid=1002(dev) groupes=1002(dev)

12. **Quelle commande permet de retrouver l’utilisateur dont l’uid est 1003 ?**

    User@localhost:~$ id 1003
    uid=1003(bob) gid=1002(dev) groupes=1002(dev)

13. **Quel est l’id du groupe dev ?**

    User@localhost:~$ grep ^dev /etc/group

Donc l'id du groupe dev est 1002

14. **Quel groupe a pour gid 1002 ? ( Rien n’empêche d’avoir un groupe dont le nom serait 1002...)**

    User@localhost:~$ getent group 1002

Donc le groupe qui a pour gid 1002 est dev

15. **Retirez l’utilisateur charlie du groupe infra. Que se passe-t-il ? Expliquez.**

    User@localhost:~$ sudo gpasswd -d charlie infra
    Removing user charlie from group infra
    User@localhost:~$ id charlie
    uid=1004(charlie) gid=1003(infra) groups=1003(infra)

L'utilisteur charlie ne peut pas être retiré du groupe infra, car c'est son groupe primaire.

16. **Modifiez le compte de dave de sorte que :**
    **— il expire au 1er juin 2021**
    **— il faut changer de mot de passe avant 90 jours**
    **— il faut attendre 5 jours pour modifier un mot de passe**
    **— l’utilisateur est averti 14 jours avant l’expiration de son mot de passe**
    **— le compte sera bloqué 30 jours après expiration du mot de passe**

//man chage

    User@localhost:~$ sudo chage -E 2021-06-01 -M 90 -m 6 -W 14 -I 30 dave 
    User@localhost:~$ sudo chage -l dave
    Last password change                                    : Sep 26, 2022
    Password expires                                        : Dec 25, 2022
    Password inactive                                       : Jan 24, 2023
    Account expires                                         : Jun 01, 2021
    Minimum number of days between password change          : 5
    Maximum number of days between password change          : 90
    Number of days of warning before password expires       : 14

17. **Quel est l’interpréteur de commandes (Shell) de l’utilisateur root ?**

    User@localhost:~$ echo $SHELL
    /bin/bash

18. **Si vous regardez la liste des comptes présents sur la machine, vous verrez qu’il en existe un nommé nobody. A quoi correspond-il ?**

    User@localhost:~$ grep nobody /etc/passwd
    nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin

nobody est le nom conventionnel d'un compte d'utilisateur à qui aucun fichier n'appartient, qui n'est dans aucun groupe qui a des privilèges et dont les seules possibilités sont celles que tous les "autres utilisateurs" ont.

19. **Par défaut, combien de temps la commande sudo conserve-t-elle votre mot de passe en mémoire ?**

Le mot de passe est mémorisé par défaut pour une durée de 15 minutes

**Quelle commande permet de forcer sudo à oublier votre mot de passe ?**

    User@localhost:~$ sudo -k

**Exercice 2. Gestion des permissions**

1. **Dans votre $HOME, créez un dossier test, et dans ce dossier un fichier fichier contenant quelques lignes de texte. Quels sont les droits sur test et fichier ?**

    User@localhost:~$ mkdir test
    User@localhost:~$ touche fichier
    User@localhost:~$ nano fichier
    "Bonjour Van"

Les droits sur test sont : 
lecture, écriture et exécution pour l'utilisateur propriétaire et pour le groupe propriétaire du dossier
lecture et exécution pour tous les autres utilisateurs

Les droits sur fichier.txt sont :
lecture, ecriture pour l'utilisateur propriétaire
lecture pour le groupe propriétaire et tous les autres utilisateurs

2. **Retirez tous les droits sur ce fichier (même pour vous), puis essayez de le modifier et de l’afficher en tant que root. Conclusion ?**

    User@localhost:~$ chmod 000 fichier
    User@localhost:~$ sudo nano fichier
    "Hello"
    User@localhost:~$ sudo cat fichier
    Bonjour Van
    Hello

Donc j'arrivas à modifier le ficher et l'afficher en tant que root.

3. **Redonnez vous les droits en écriture et exécution sur fichier puis exécutez la commande echo "echo Hello" > fichier. On a vu lors des TP précédents que cette commande remplace le contenu d’un fichier s’il existe déjà. Que peut-on dire au sujet des droits ?**

    User@localhost:~/test$ chmod u+wr fichier
    User@localhost:~/test$ echo "echo Hello" > fichier

En modifiant les droits en écriture et exécution sur fichier on peut modifier le fichier sans les droits root. 

4. **Essayez d’exécuter le fichier. Est-ce que cela fonctionne ? Et avec sudo ? Expliquez.**

    User@localhost:~/test$ ./fichier
    bash: ./fichier: Permission denied

 Je n'ai pas le droit d'exécuter le fichier malgré les modifications des droits en écriture et exécution car je n'ai pas le droit en lecture.
Donc si on donne le droit en lecture sur fichier

    User@localhost:~/test$ chmod u+r fichier
    User@localhost:~/test$ ./fichier
    Hello

5. **Placez-vous dans le répertoire test, et retirez-vous le droit en lecture pour ce répertoire. Listez le contenu du répertoire, puis exécutez ou affichez le contenu du fichier fichier. Qu’en déduisez-vous ?**

    User@localhost:~/test$ chmod u-r ../test
    User@localhost:~/test$ ls
    ls: cannot open directory '.': Permission denied

    User@localhost:~/test$ ./fichier
    Hello
    User@localhost:~/test$ cat fichier
    echo Hello

Je n'ai pas la permission de lister le contenu du répertoire, mais je peux l'exécuter et afficher son contenu.

**Rétablissez le droit en lecture sur test.**

    User@localhost:~$ chmod u+r test 

6. **Créez dans test un fichier nouveau ainsi qu’un répertoire sstest. Retirez au fichier nouveau et au répertoire test le droit en écriture. Tentez de modifier le fichier nouveau. Rétablissez ensuite le droit en écriture au répertoire test. Tentez de modifier le fichier nouveau, puis de le supprimer. Que pouvezvous déduire de toutes ces manipulations ?**

    User@localhost:~/test$ touch nouveau
    User@localhost:~/test$ mkdir sstest
    User@localhost:~/test$ chmod ug-w nouveau
    User@localhost:~$ chmod ug-w test

    User@localhost:~/test$ echo "bonjour Van" > nouveau
    bash: ./nouveau: Permission denied

    User@localhost:~$ chmod ug+w test
    User@localhost:~/test$ echo "bonjour Van" > nouveau
    bash: ./nouveau: Permission denied

Donc retirant le droit en écriture sur le fichier nouveau et sur le répertoire test, je n'ai pas le droit de modifier le fichier nouveau, si je rétabli le droit en écriture au répertoire test, je n'ai toujours pas l'autorisation de modifier le fichier nouveau mais par contre je peux le supprimer.

7. **Positionnez vous dans votre répertoire personnel, puis retirez le droit en exécution du répertoire test. Tentez de créer, supprimer, ou modifier un fichier dans le répertoire test, de vous y déplacer, d’en lister le contenu, etc…Qu’en déduisez vous quant au sens du droit en exécution pour les répertoires ?**

     User@localhost:~$ chmod -x test
     User@localhost:~$ touch test/fichier1
     touch: cannot touch 'test/fichier1': Permission denied

     User@localhost:~$ rm test/fichier
     rm: cannot remove 'test/fichier': Permission denied
    
     User@localhost:~$ echo "Bonjour tout le monde" > test/fichier
     -bash: test/fichier: Permission denied

En retirant le droit en exécution du répertoire test, je n'ai plus le droit de créer, supprimer, ou modifier un fichier, me déplacer et d'en lister le contenu. 

8. **Rétablissez le droit en exécution du répertoire test. Positionnez vous dans ce répertoire et retirez lui à nouveau le droit d’exécution. Essayez de créer, supprimer et modifier un fichier dans le répertoire test, de vous déplacer dans sstest, de lister son contenu. Qu’en concluez-vous quant à l’influence des droits que l’on possède sur le répertoire courant ? Peut-on retourner dans le répertoire parent avec ”cd ..” ? Pouvez-vous donner une explication ?**

     User@localhost:~$ chmod +x test
     User@localhost:~$ cd test
     User@localhost:~/test$ chmod -x ../test

     User@localhost:~/test$ touch van
     touch: cannot touch 'van': Permission denied

     User@localhost:~/test$ rm fichier
     rm: cannot remove 'fichier': Permission denied

     User@localhost:~/test$ echo "Bonjour tout le monde" > fichier
     -bash: fichier: Permission denied

     User@localhost:~/test$ cd sstest
     -bash: cd: sstest: Permission denied

     User@localhost:~/test$ ls sstest
     ls: cannot access 'sstest': Permission denied

     User@localhost:~/test$ ls
     ls: cannot open directory '.': Permission denied

     User@localhost:~/test$ cd ..
     User@localhost:~$

Donc en retirant le droit d'exécution du répertoire test, on ne peut plus créer, supprimer et modifier un fichier dans répertoire test, ni de se déplacer dans sstest, de lister son contenu. Cependant on peut bien retourner dans le répertoire parent avec "cd ..". Donc on peut dire que naviquer via cd dépend des droit du dossier dans lequel on veut naviquer.

9. **Rétablissez le droit en exécution du répertoire test. Attribuez au fichier fichier les droits suffisants pour qu’une autre personne de votre groupe puisse y accéder en lecture, mais pas en écriture.**

     User@localhost:~$ chmod +x test
     User@localhost:~$ chmod g+r fichier

10. **Définissez un umask très restrictif qui interdit à quiconque à part vous l’accès en lecture ou en écriture, ainsi que la traversée de vos répertoires. Testez sur un nouveau fichier et un nouveau répertoire.**

     User@localhost:~$ mkdir NewFolder
     User@localhost:~/NewFolder$ umask go-rwx
     User@localhost:~/NewFolder$ mkdir interdit
     User@localhost:~/NewFolder$ touch NewFile
     User@localhost:~/NewFolder$ ls -l
     drwx------ 2 User User 6 Sep 29 11:47 interdit
     -rw------- 1 User User 0 Sep 29 11:47 NewFile

11. **Définissez un umask très permissif qui autorise tout le monde à lire vos fichiers et traverser vos répertoires, mais n’autorise que vous à écrire. Testez sur un nouveau fichier et un nouveau répertoire.**

     User@localhost:~$ mkdir NouveauDossier
     User@localhost:~/NouveauDossier$ umask 022
     User@localhost:~/NouveauDossier$ mkdir Van
     User@localhost:~/NouveauDossier$ touch nouveau
     User@localhost:~/NouveauDossier$ ls -l
     -rw-r--r-- 1 User User 0 Sep 29 12:16 nouveau
     drwxr-xr-x 2 User User 6 Sep 29 12:16 Van

12. **Définissez un umask équilibré qui vous autorise un accès complet et autorise un accès en lecture aux membres de votre groupe. Testez sur un nouveau fichier et un nouveau répertoire.**

     User@localhost:~$ mkdir folder
     User@localhost:~/folder$ umask 027
     User@localhost:~/folder$ mkdir dossier
     User@localhost:~/folder$ touch fichier
     User@localhost:~/folder$ ls -l
     drwxr-x--- 2 User User 6 Sep 29 12:29 dossier
     -rw-r----- 1 User User 0 Sep 29 12:29 fichier


13. **Transcrivez les commandes suivantes de la notation classique à la notation octale ou vice-versa (vous pourrez vous aider de la commande stat pour valider vos réponses) :**
    **- chmod u=rx,g=wx,o=r fic**
    **- chmod uo+w,g-rx fic en sachant que les droits initiaux de fic sont r--r-x---**
    **- chmod 653 fic en sachant que les droits initiaux de fic sont 711**
    **- chmod u+x,g=w,o-r fic en sachant que les droits initiaux de fic sont r--r-x---**

- chmod u=rx,g=wx,o=r fic
  'chmod 534 fic'
- chmod uo+w,g-rx fic en sachant que les droits initiaux de fic sont r--r-x---
  'chmod 602 fic'
- chmod 653 fic en sachant que les droits initiaux de fic sont 711
  'chmod u-x, g+r, o+w fic'
- chmod u+x,g=w,o-r fic en sachant que les droits initiaux de fic sont r--r-x---
  'chmod 520 fic'

14. **Affichez les droits sur le programme passwd. Que remarquez-vous ? En affichant les droits du fichier /etc/passwd, pouvez-vous justifier les permissions sur le programme passwd ?**

     User@localhost:~$ ls -l /etc/passwd
     -rw-r--r-- 1 root root 1957 Sep 26 09:30 /etc/passwd

Tous les utilisateurs ont le droit de lecture, mais seulement l'utilisateur propriétaire a le droit d'écriture. Ce programme appartient à root, donc seulement root a le droit de l'exécuter.

**Pour les plus rapides :**
15. **Access Control Lists (ACL) : suivez le tutoriel de cette page : https://doc.ubuntu-fr.org/acl.**

16. **Quotas disques : suivez le tutoriel de cette page : https://doc.ubuntu-fr.org/quota**