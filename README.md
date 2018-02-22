# WriteUp WhereIsRicky

Voici mes solutions pour les challenges de _*RickdiculouslyEasy*_

## Pour Commencer

Ces challenges sont d'un niveau débutant.
Je pars du principe que vous connaissez tous et toutes les commandes courantes comme
[ls](http://man7.org/linux/man-pages/man1/ls.1.html),
 [cat](http://man7.org/linux/man-pages/man1/cat.1.html),
 [file](http://man7.org/linux/man-pages/man1/file.1.html),
 [tail](http://man7.org/linux/man-pages/man1/tail.1.html),
 [strings](http://man7.org/linux/man-pages/man1/strings.1.html),
 [tac](http://man7.org/linux/man-pages/man1/tac.1.html),
 [...](http://man7.org/linux/man-pages/man1/man.1.html)  
Toutefois il est quand même recommandé de connaitre un peu le [python](https://www.python.org/doc/)
et de connaitre les commandes suivantes:  
* [nc](https://linux.die.net/man/1/nc)
* [unicornscan](https://linux.die.net/man/1/unicornscan)
* [hydra](https://tools.kali.org/password-attacks/hydra)
* [ftp](https://linux.die.net/man/1/ftp)
* [dirb](https://tools.kali.org/web-applications/dirb)
* [patator](https://tools.kali.org/password-attacks/patator)


### Conditions préalables
* **Installer [Vmware](https://my.vmware.com/en/web/vmware/info/slug/desktop_end_user_computing/vmware_workstation_pro/14_0) ou
 [Virtualbox](https://www.virtualbox.org/wiki/Downloads)**  
* **Installation de l'[iso](http://pentest01.sansnom.org) dans une VM**   
* **Installation de [Kali](https://www.kali.org/downloads/) ou d'une [image](https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-hyperv-image-download/)**

### Installation

Personnellement j'ai utilisé Virtualbox et j'ai configuré le réseau de l'iso en bidge.
Vous êtes libre de l'utiliser comme bon vous semble comme `NAT Network` ou autres
Si vos êtes comme moi et avait opté pour Virtualbox, n'oublier pas d'installer
le paquet d'extension pour plus de confort et eviter des problemes d'installation !

## Resolution des Challenges
L'adresse de notre cible est indiquer sur la VM qui tourne sur Virtualbox.  
![Adresse](/images/Adresse_cible.png)  

On vas donc commencer par scan un peut tout ca avec la commande :  

```
unicornscan -v -I -r 1000 -p 1-65535 192.168.1.15
```
![cmd_unicornscan](/images/unicornscan_cmd.png)  

On peut voir ici qu'il y a plusieurs point d'entré.  
On vas donc faire un petit nc sur chacune des zone que l'on a trouver :  


```
nc -nv 192.169.1.15 60000
```
![nc_cmd_port_60000](/images/nc_cmd_60000.png)  
Ici on peut voir que ce petit filou de Rick nous a laisser une petite backdoor.  
On vas donc regarder ce que l'on trouve dans cette zone avec la
 commande [ls](http://man7.org/linux/man-pages/man1/ls.1.html).
 On vient de trouver un fichier <span style="color:#cc33ff">FLAG.txt</span>.
 On lance donc un [cat](http://man7.org/linux/man-pages/man1/cat.1.html) dessus et BINGO
 <span style="color:blue">*{Flip the pickle Morty!}*</span>! Ce qui nous fait 10 / 130 points.  


```
nc -nv 192.169.1.15 22222
```
![nc_cmd_port_22222](/images/nc_cmd_22222.png)  

Ici pas de flag. Par contre on peut voir que ce port sert a ce connecter en
 [ssh](https://linux.die.net/man/1/ssh) . Si jamais on trouve des identifiants
 au cours de notre exploration, on vas surement pouvoir utiliser ce port pour ce connecter !  



```
nc -nv 192.169.1.15 13337
```
![nc_cmd_port_13337](/images/nc_cmd_13337.png)  

On a directement un flag qui s'afficher <span style="color:blue">*{TheyFoundMyBackDoorMorty}*</span>!
 ce qui nous fait a present 20 / 130 points.  


Bien maintenant que l'on a fait les <span style="color:red">`unknown`</span>. On vas passer aux classiques.
 Comme dit sur la VM qui run l'iso et sur notre commande [unicornscan](https://linux.die.net/man/1/unicornscan)
 on voit que l'on peut se connecter depuis un brother a l'adresse `https://192.168.1.15:9090`.
 Une fois rendu sur cette page on peut constater que l'on a un autre flag
 <span style="color:blue">*{THERE IS NO ZEUS, IN YOUR FACE!}*</span>. On arrive donc a 30 / 130 points.  
<!---
url link img
-->
![web_home](/images/connection_home.png)


Passons maintenant a la connection ftp. Pour cela on vas utiliser [hydra](https://tools.kali.org/password-attacks/hydra)
 pour brutforce histoire de voir si par megarde il n'y aurait pas un acces. On vas donc lancer la commande :  
```
hydra -V -f -L /usr/share/worldlists/dirb/ltl.txt -P /usr/share/worldlists/dirb/ltl.txt ftp://192.168.1.15
```
<!---
url link img
-->
![hydra_result](/images/hydra_result.png)  
J'ai utilisé mon propre custom file mais il existe plein de fichier plus ou moins gros pour tester ces connexions la.
 Au bout de quelques minutes nous avons trouver un match. Ni une, ni deux, on vas pouvoir voir si l'on trouve quelque chose.
 On lance donc la connexion avec `ftp -p 192.168.1.15`, on se retrouve donc a rentrer les logins et password trouver.
 une fois connecter on lance notre, [ls](http://man7.org/linux/man-pages/man1/ls.1.html) et encore une fois on voit
 un fichier <span style="color:#cc33ff">FLAG.txt</span>.  

![ftp_cmd](/images/ftp_cmd.png)
On vas donc regarder ce qu'il y a dedans avec `get FLAG.txt /dev/tty` et nous voila maintenant en possetion d'un nouveau
 flag <span style="color:blue">*{Whoa this is unexpected}*</span> ce qui nous monte maintenant a 40 / 130 points.  

On vas maintenant commencer a essayer de voir si l'on ne trouve pas certains acces sur le sites. Comme on n'aime pas attendre on vas accelerer les choses avec la commande [dirb](https://tools.kali.org/web-applications/dirb).
 On lance donc la commande suivante :  
```
dirb http://192.168.1.15 /usr/share/worldlists/dirb/big.txt -N 404
```
![dirb_cmd](/images/dirb_cmd.png)  

On a des resultat :
* <span style="color:#cc33ff">cgi-bin/</span>
* <span style="color:#cc33ff">passwords/</span>  
* <span style="color:#cc33ff">robots.txt</span>


`http://192.168.1.15/cgi-bin/` n'est pas tres concluant. on se retrouve avec un
 `Forbidden` et rien dans le code source de la page :/.  
![forbidden_acces](/images/forbidden_acces.png)  
![forbidden_source](/images/forbidden_source.png)  


Essayons maintenant avec la directory `http://192.168.1.15/passwords/`
![passwords](/images/passwords.png)  

Ah plus de chance cette fois avec cette Directory ! On y retrouve deux fichiers:  
* <span style="color:#cc33ff">FLAG.txt</span>.  
* <span style="color:#cc33ff">passwords.html</span>.  

![password_flag](/images/password_flag.png)  

On vas d'abord regarder le contenue du fichier <span style="color:#cc33ff">FLAG.txt</span>
 affin de recupere un notre nouveau flag qui est <span style="color:blue">
 *{Yeah d- just don't do it.}*</span> ce flag etant a 10 nous sommes donc
  maintenant a 50 / 130 points.  

![passwords_passwords](/images/passwords_passwords.png)  
Maintenant allons regarder le fichier `passwords.html`. Il n'y a rien de special
 sur cette page. Par contre si l'on regarde le code source on peut trouver ecrit
 en commantaire la phrase <span style="color:red">`Password: winter`</span>.  
Pas moyen de l'utilisé pour l'instant mais on a recuperer un mot de passe et ca
 c'est toujours cool !


Passons maintenant au `robots.txt`  
![robots](/images/robots.png)  
Super ! Finalement
 on vas pouvoir allez les [cgi](https://en.wikipedia.org/wiki/Common_Gateway_Interface).  
On voit qu'il y a donc deux fichiers :  
* <span style="color:#cc33ff">cgi-bin/root_shell.cgi</span>.  
* <span style="color:#cc33ff">cgi-bin/tracertool.cgi</span>.  

![root_shell_cgi](/images/root_shell_cgi.png)  
On commence donc par l'url du root_shell par ce que les root_shell c'est cool !
Manque de chance Rick est vraiment un *trou du cul* car il n'a pas fini de le
 coder :/.  

On vas donc passer au tracertool. Et la il nous proposer d'entrer une adresse ip
 pour la tracer. Comme on est des personnes serieuse qui ecoute ce que l'on nous
 demande. On vas essayer de voir a tout hasard il ne nous laisserais pas renter
 d'autre commande en rajoutant un <span style="color:red">`;`</span> au debut,
 pour executer une autre commande.  
![tracertool_cgi](/images/tracertool_cgi.png)  

On s'applique donc a lancer la commande suivante :  
```
; ls -la
```
![ls_tracertool_cgi](/images/ls_tracertool_cgi.png)  
Chouette ce n'est pas proteger. On essaye donc :  
```
; cat /etc/passwd
```
![cat_tracertool_cgi](/images/cat_tracertool_cgi.png)  
... On dirais bien qu'il se
 protege contre la commande [cat](http://man7.org/linux/man-pages/man1/cat.1.html).
 On vas donc essayer avec [tac](http://man7.org/linux/man-pages/man1/tac.1.html)!  
 ```
 ; tac /etc/passwd
 ```
 ![tac_tracertool_cgi](/images/tac_tracertool_cgi.png)  

Nickel ! Si vous vous rapellez bien, nous avions trouver un mot de passe avant.
 et bien maintenant que l'on a recuperer la list des users on vas pouvoir essayer
 de faire match ce mot de passe avec l'un des users. Vue qu'il n'y a que trois
 utilisateur j'ai essayer directement a la main et la Summer m'a ouvert son coeur:  
 ```
 ssh -p 22222 Summer@192.168.1.15
 ```
![connection_summer](/images/connection_summer.png)  

Commencons par le commencement, un petit [ls](http://man7.org/linux/man-pages/man1/ls.1.html),
 nous reveles que l'on a un nouveau fichier <span style="color:#cc33ff">FLAG.txt</span>.
 On lance donc notre `tail FLAG.txt` et un nouveau flag pour nous grace au flag
 <span style="color:blue"> *{Yeah d- just don't do it.}*</span> + 10 points.
 Ce qui nous fait monter a 60 / 130 points.  
![ls_tailf_flag](/images/ls_tailf_flag.png)  


Maintenant que nous sommes connecter en [ssh](https://linux.die.net/man/1/ssh)
 on vas pouvoir allez voir ce qu'on Morty est Rick dans leurs Home.
 On vas donc fait la commande suivante avec le User `Summer`:  
```
clear ; cd /home/Morty ; ls -la
```
![ls_morty_home](/imges/ls_morty_home.png)  
On trouve donc deux fichiers:  
* <span style="color:#cc33ff">journal.txt.zip</span>.  
* <span style="color:#cc33ff">Safe_password.jpg</span>.  
On vas essayer de regarder ce que l'on peut faire de ca. On vas donc lancer la
 commande suivante avec le user `Summer`:  

```
cp journal.txt.zip Safe_password.jpg /home/Summer/. ; cp Safe_password.jpg /tmp/.
 ; chown Summer /tmp/Safe_password.jpg                                      
```
On constate que le fichier zip est proteger par un mot de passe. Cohinsidence avec
 le faire qu'il soit accompagner d'un fichier de mot de passe ? Je ne crois pas !
 Mais sait on jamais. On vas la copier pour pouvoir l'ouvrir sur notre ordinateur
 car lire une image sur un terminal n'est pas vraiment lisible pour un humain.
 On lance commande suivante :  
```
scp -P 22222 Summer@192.168.1.15:/tmp/Safe_password.jpg .
```
On ouvre donc le fichier et la >_..._<, rien d'ecrit sur l'image.
![rick_img](/imges/rick_img.png)
On vas donc lancer la commande
```
strings Safe_password.jpg
```
![strings_pwd](/imges/strings_pwd.png)
Genial, Rick a ete malin et sait que Morty est un peut stupide. Du coup il lui a
 clairement ecrit le mot de passe pour
 [unzip](https://linux.die.net/man/1/unzip) le fichier !  
On retourne sur la connexion [ssh](https://linux.die.net/man/1/ssh) et on essaye
 le mot de passe:  
![unzip_txt](/imges/unzip_txt.png)  
Parfait ! Morty nous confits que c'est un mot de passe a Rick et nous avons
 maintenant un nouveau Flag <span style="color:blue">*{131333}*</span> et celui
 la est a 20 points. On en est maintenant a 80 / 130 points.  

Allons faire un tour chez Rick maintenant que Morty nous a reveler tous ces secrets.
 On y retrouve deux Folders:  
* <span style="color:#cc33ff">RICKS_SAFE</span>
* <span style="color:#cc33ff">ThisDoesntContainsAnyFlags</span>  
![unzip_txt](/imges/unzip_txt.png)  

A premiere vue le fichier <span style="color:#cc33ff">NotAFlag.txt</span> n'est
 pas un flag. Par contre le fichier <span style="color:#cc33ff">safe</span> est
 deja plus interessant. On se rend compte que c'est un executable. On veut pouvoir
 faire mumuse avec, alors nous allons le copier dans notre home puis l'executer.
```
cp /home/RickSanchez/RICKS_SAFE/safe /home/Summer/. ; cd ; ./safe
```
![run_safe](/imges/run_safe.png)  

Ca y est ! Vous aussi vous faite la relation avec ce que ce bon vieux Morty a dit ?
 Tres bien reessayons mais avec comme premier arguments 131333.
![decrypt_succes](/imges/decrypt_succes.png)  

On touche au but ! En plus d'avoir trouver le flag
 <span style="color:blue"> *{And Awwwaaaaayyyy We Go!}*</span> qui nous fait avoir
 20 points en plus et donc arriver a 100 / 130 points ; il nous explique meme
 comment se connecter avec RickSanchez en [ssh](https://linux.die.net/man/1/ssh) !  

![Wiki_rick](/imges/Wiki_rick.png)  
Comme Tout le monde le sait le groupe de Rick etait
 <spanstyle="color:red">`The Flesh Curtains`</span>, mais dans le doute on a
 quand meme verifier sur l'internet. Il ne nous reste plus qu'a ecrire un petit
 script.
```
cat > generator_mdp.py
import string
grp_name = ("The", "Flesh", "Curtains")
with open("mdp.txt", 'w') as f:
    for l in string.uppercase:
        for d in string.digits:
            for g in grp_name:
                f.write(l + d + g + '\n')
```
Maintenant qu'on a notre Script on vas generer toutes les combinaisons de mot de
 passe puis essayer de retrouver le mot de passe grace a
 [patator](https://tools.kali.org/password-attacks/patator).
```
python generator_mdp.py ; patator ssh_login user=RickSanchez host=192.168.1.14 port=22222 password=FILE0 0=mdp.txt -x ignore:mesg='Authentication failed.'
```
Et on a notre mot de passe. Essayons de nous connecter en
    [ssh](https://linux.die.net/man/1/ssh) en rentrant ce mot de passe
![ssh_rick_connection](/images/ssh_rick_connection.png)  
```
ssh -p 22222 RickSanchez@192.168.1.15
```
Maintenant que nous sommes OFFICIELLEMENT RickSanchez nous pouvons lancer la
 commande `sudo su` avec le meme mot de passe est ainsi passer root.  
![root_dir](/images/root_dir.png)  

On Retrouve notre dernier flag
 <span style="color:blue">*{Ionic Defibrillator}*</span> pour une valeur de 30
 points ce qui nous amenes a 130 sur 130 points !  
![last_flag](/images/last_flag.png)  
Bravo a toutes et a toute d'avoir reussi ces challenges :) !
