class: inverse, center, middle

# Docker
### FOSSCOMM 2016
#### Κωστής Καραντίας - @gtklocker

---

# Γιατί Docker;

Πόσες φορές έχετε έρθει αντιμέτωποι με αυτό;
```
$ pip install psycopg2
Downloading/unpacking http://pypi.python.org/packages/source/p/psycopg2/psycopg2
-2.4.tar.gz#md5=24f4368e2cfdc1a2b03282ddda814160
  Downloading psycopg2-2.4.tar.gz (607Kb): 607Kb downloaded
  Running setup.py egg_info for package from http://pypi.python.org/packages/sou
rce/p/psycopg2/psycopg2-2.4.tar.gz#md5=24f4368e2cfdc1a2b03282ddda814160
    Error: pg_config executable not found.

    Please add the directory containing pg_config to the PATH
    or specify the full executable path with the option:

        python setup.py build_ext --pg-config /path/to/pg_config build ...

    or with the pg_config option in 'setup.cfg'.
    Complete output from command python setup.py egg_info:
    running egg_info
```

---

# Γιατί Docker;

Για να φτάσετε σε μια τέτοια λύση;

![](img/stack-overflow-solution.png)

---

class: center, middle

![](img/wisdom-of-the-ancients.png)

---

# Γιατί Docker;

- Και πόσες ώρες έχετε ξοδέψει μονάχα για να τρέξετε ένα νεο codebase;
- Από το 2010 το *Vagrant* έλυσε αυτό το πρόβλημα.
    - Κάθε εφαρμογή έχει ένα πολύ συγκεκριμένο περιβάλλον το οποίο καθορίζεται αυστηρά (OS, dependencies, network, ...).
    - Αρκεί πλεον ένα `vagrant up` για να τρέξουν όλα αυτόματα και να μπορεί ο developer να αφοσιωθεί στο να αναπτύξει την εφαρμογή του.
    - Φεύγει ο χαμένος χρόνος που θα χρειαζόταν κανονικά για να τρέξει την εφαρμογή τοπικά.
- Είναι η πλεον διαδεδομένη λύση.
- Αρχικά το Vagrant χρησιμοποιούσε VMs για να κάνει όλα αυτά.
- Πλεον χρησιμοποιει και Docker containers! 😀

---

# Γιατί Docker containers;

- Έχουν μικρότερο overhead από ένα VM. 🍔
- Ξεκινάνε πιο γρήγορα από ένα VM. 🛫
- Είναι πιο γρήγοροι από ένα VM. 👌

---

# Docker containers

- Από τι αποτελείται ένας container;
    - Κυρίως ένα filesystem που λέμε image, σαν snapshot ενός VM.
    - Σε έναν container έχουμε συνήθως το userspace που χρειαζόμαστε και μια εφαρμογή.
    - Πχ. θα μπορούσαμε να έχουμε έναν container για nginx, έναν container για bitcoind
- Υπάρχουν έτοιμα images για οτιδήποτε μπορείτε να φανταστείτε στο Docker Hub (hub.docker.com).
    - Το userspace οποιασδήποτε διανομής θέλετε (Ubuntu, Debian, Arch, ...)
    - Όποια γνωστή εφαρμογή μπορείτε να σκεφτείτε.
    - Θα μιλήσουμε περισσότερο για images αργότερα.

---

# Baby steps

Θα τρέξουμε ένα container με το image του Ubuntu με το `docker run`.

```
➜  ~ docker run -it ubuntu /bin/bash
root@ba97fbda8ad1:/# ls
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
root@ba97fbda8ad1:/# apt-get update
Ign http://archive.ubuntu.com trusty InRelease
Get:1 http://archive.ubuntu.com trusty-updates InRelease [65.9 kB]
Get:2 http://archive.ubuntu.com trusty-security InRelease [65.9 kB]
Hit http://archive.ubuntu.com trusty Release.gpg
Hit http://archive.ubuntu.com trusty Release
Get:3 http://archive.ubuntu.com trusty-updates/main Sources [341 kB]
...
```

---

# Baby steps

- Το `ubuntu` είναι το όνομα του image που θα έχει ο container μας.
- Κάθε container έχει πρόσβαση στο δίκτυο.
- Σε κάθε container γίνεται αssign ένα id κι ένα όνομα.

```
➜  ~ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
a8c81857edd9        ubuntu              "/bin/bash"         6 seconds ago       Up 6 seconds                            sad_carson
```

---

# Χρήσιμες λειτουργίες.red[\*]

- `docker pause $name`: παγώνει την εκτέλεση του container
- `docker unpause $name`
- `docker stop $name`: τερματίζει την εκτέλεση του container
    - Στέλνει SIGTERM στο process του container.
    - Με `docker ps -a` μπορούμε να δούμε όλους τους containers, ακόμα και αυτούς που είναι stopped.
- `docker kill $name`: σκοτώνει τον container
    - Στέλνει SIGKILL στο process του container.
- `docker start $name`: ξαναξεκινάει έναν stopped container
- `docker logs $name`
- `docker attach $name`: μας συνδέει με το command που τρέχει ο container μας (αν πχ. το container τρέχει bash μας βάζει στο bash prompt)
- `docker rm $name`: διαγράφει έναν σταματημένο container (υπάρχει και το `rm -f` για running containers)

.footnote[.red[\*]Όπου `$name` εννοούμε είτε το όνομα είτε το id του container.]

---

class: inverse, center, middle

# Demo

---

# Images

Ας εγκαταστήσουμε nodejs στον container μας.
```
root@a8c81857edd9:/# apt-get update
[...]
root@a8c81857edd9:/# apt-get install nodejs
Reading package lists... Done
[...]
Setting up libc-ares2:amd64 (1.10.0-2) ...
Setting up libv8-3.14.5 (3.14.5.8-5ubuntu2) ...
Setting up nodejs (0.10.25~dfsg2-2ubuntu1) ...
update-alternatives: using /usr/bin/nodejs to provide /usr/bin/js (js) in auto mode
Processing triggers for libc-bin (2.19-0ubuntu6.6) ...
root@a8c81857edd9:/# nodejs --version
v0.10.25
```

---

# Images

- Από τη στιγμή που θα σβήσουμε τον container μας το nodejs θα χαθεί.
- Οι αλλαγές που κάνουμε δεν αποθηκεύονται στο image (ubuntu image στην περίπτωση μας).
- Μπορούμε να κρατήσουμε τις αλλαγές μας σε ένα καινούριο image με `docker commit $name`.

```
➜  ~ docker commit sad_carson
sha256:641c3875ed8b1da49904d47aee0f0a6ad6928e41aa2220d3b192d32a91687b9a
➜  ~ docker images
REPOSITORY                    TAG                 IMAGE ID            CREATED              SIZE
<none>                        <none>              641c3875ed8b        About a minute ago   235.5 MB
ubuntu                        latest              2a274e3405ec        9 months ago         188.3 MB
```

---

# Images

Και να τρέξουμε έναν container με αυτό το image.

```
➜  ~ docker run 641c3875ed8b nodejs --version
v0.10.25
```

### GREAT SUCCESS! 👏

Πάνω σε αυτό τον container και να συνεχίσουμε να κάνουμε commit και να δημιουργούμε νεα images.

---

# Images

- Τα images έχουν συνήθως όνομα που έχει την εξής μορφή: `user/name:tag`. (μπορείτε να σκεφτείτε το tag σαν έκδοση)
    - Το κομμάτι `user/name` λέγεται και repository.
- Μπορείτε να ορίσετε το δικό σας όνομα στο docker commit σαν έξτρα παράμετρο.

```
➜  ~ docker commit sad_carson gtklocker/nodejs:v0.10.25
sha256:eafb3beaebe865c3267469c115889c2cd3c49b7115063ef1c93a089ee7ef7bdd
➜  ~ docker images
REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
gtklocker/nodejs              v0.10.25            eafb3beaebe8        3 seconds ago       235.5 MB
➜  ~ docker run gtklocker/nodejs:v0.10.25 nodejs --version
v0.10.25
```

---

# Images

- Μπορείτε να σβήσετε images με `docker rmi $image`.

---

# Docker Hub

---

# docker pull/push

---

# AUFS/COW

---

# Networking

---

# Links

---

# Volumes

---
