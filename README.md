# Introduction

Sigma is an integrated development environment for logical theories that
extend the Suggested Upper Merged Ontology (SUMO).  There is a public installation
with read-only functions enabled linked from http://www.ontologyportal.org

> **Warning**
> Please read these notes thoroughly if you want to do a native install not
> with a container.  Most installation issues result from not
> carefully following the instructions.

You can follow the steps below to do a manual installation on linux or Mac. This
procedure assumes that you start from your home directory and are happy with
having directories created there. The sed command below attempts to modify
`~/.sigmakee/KBs/config.xml` to conform to your local paths.  If your paths
differ, then you may need to edit your config.xml manually. If you are running
tomcat on vagrant or another VM, you may need to change the port value from 8080.
If you are running on a server, rather than your localhost you'll need to set
the hostname parameter in your config.xml file. E will only work if your $TMPDIR
is set correctly.  No particular version of tomcat is required. If you load a
different version of tomcat, be sure to change `$CATALINA_HOME` and your paths to
conform to the version. If you use a different mirror or version you'll need to
change the wget commend below and Oracle Java appears to now have a key embedded
in their URL that will change every time. Change "theuser" below to your user name.

If your installation isn't working and you're getting funny "null"s in your paths
try opening permissions on your `$SIGMA_HOME`, `$CATALINA_HOME` and `$SIGMA_SRC` directories.

After installing, recommended reading is the Sigma manual
https://github.com/ontologyportal/sigmakee/blob/master/doc/manual/SigmaManual.pdf
There is a video on installing Sigma, as well as many others about related tools at
https://www.youtube.com/playlist?list=PLpBQIgki3izeUmFD8c65INdmxRNjjxOzP

## Container-Based installation

First, install docker if you don't have it already

```sh
sudo apt-get update
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
sudo apt-get update
sudo apt-get install docker-ce
```

Then get the docker image and run it

Pull with
```sh
sudo docker pull apease/sigmakee2018:latest
```

Run with
```sh
sudo docker run -it -d -p 8080:8080 --name trial04 apease/sigmakee2018:latest "./sigmastart.sh"
```
Access from a browser with http://localhost:8080/sigma/login.html . Use admin for username and admin for password


## Vagrant Virtual Machine installation

> **Warning**
> Vagrant build is not maintained.
Original vagrant files can be found in [attic](attic)

## Build a New Docker Image

Please note that the Docker container version of Sigma is several years out of date.  A native install of Sigma is
recommended.

To build a new docker container follow these steps where `$SIGMA_SRC` is your sigmakee git repo path.
First, download jdk-8u171-linux-x64.rpm from http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
Note that if you don't download that exact version, you'll need to edit sigmastart.sh so that the filename
matches.  You'll also need to make changes to track the latest Tomcat, in the bashrc and Dockerfile

```sh
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
sudo apt-get update
sudo apt-get install docker-ce
mkdir images
cd images
cp $SIGMA_SRC/docker/* .
sudo docker build -t sigmakee2018:latest .
```

to push the image to dockerhub

```sh
>:~/images$ sudo docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username (apease):
Password:
Login Succeeded

>:~/images$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                    NAMES
1f7d0cef7874        51a041125329        "./sigmastart.sh"   56 minutes ago      Up 56 minutes       0.0.0.0:4000->8080/tcp   trial11
>:~/images$ sudo docker tag 51a041125329 apease/sigmakee2018:latest
>:~/images$ sudo docker push apease/sigmakee2018:latest
The push refers to a repository [docker.io/apease/sigmakee2018]
ab5e94769be7: Pushed
1c15947f83dc: Pushed
c2007c9776df: Pushed
829ef5b0378d: Pushed
6fd852e99bda: Pushed
85c7d96adccb: Pushed
6b2f14c09222: Pushed
9a9a3d9cc4bc: Pushed
38c81b36edfb: Pushed
bcc97fbfc9e1: Pushed
latest: digest: sha256:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx size: 2417
```


## System preparation on Linux

Make sure that throughout the commands below that you replace "theuser" with your desired user name

create user theuser
```sh
sudo useradd theuser
```
add password for theuser
```sh
sudo passwd theuser
```
add to sudoers file
```sh
sudo usermod -aG sudo theuser
```
switch over to the new user
```sh
su theuser
cd /home
sudo mkdir theuser
sudo chown theuser theuser
cd theuser
```
make sure you're running bash (and answer /bin/bash)
```sh
chsh
```
install unzip
```sh
sudo apt-get install unzip
sudo apt-get update
```

may need to create a .bashrc
```sh
touch .bashrc
```
handy to add stuff to .bashrc
```sh
echo "alias dir='ls --color=auto --format=vertical -la'" >> .bashrc
echo "export HISTSIZE=10000 HISTFILESIZE=100000" >> .bashrc
echo "export JAVA_HOME=/home/theuser/Programs/jdk1.8.0_112" >> .bashrc
```

load the definitions into your environment
```sh
source .bashrc
```

then you can add the last one
```sh
echo "export PATH=$PATH:$JAVA_HOME/bin" >> .bashrc
source .bashrc
```

I've only tested on Oracle JDK 1.8. If you want to use OpenJDK or a version of Java other than 1.8 do so at your own risk.
You may need to download Java and set your
JAVA_HOME http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html .  First,

```sh
mkdir /home/theuser/Programs
cd Programs
```

The following command line version may work but you may need to update the name of the jdk zipfile

```sh
wget --no-check-certificate -c --header "Cookie: oraclelicense=accept-securebackup-cookie"
  https://download.oracle.com/otn-pub/java/jdk/14.0.1+7/664493ef4a6946b186ff29eb326336a2/jdk-14.0.1_linux-x64_bin.tar.gz
  gunzip jdk-14.0.1_linux-x64_bin.tar.gz
```

but you also may need to go to the web site, accept the license, then copy the download link
into this command.  Then you need two commands to install the new Java (check that the paths conform
to the java version you downloaded) -

```sh
sudo update-alternatives --install "/usr/bin/java" "java" "/home/theuser/Programs/jdk1.8.0_version/bin/java" 1
sudo update-alternatives --set java /home/theuser/Programs/jdk1.8.0_version/bin/java
```

Verify that it's installed correctly with

```sh
java -version
```
You should see something like -

```
java version "1.8.0_241"
Java(TM) SE Runtime Environment (build 1.8.0_241-b07)
Java HotSpot(TM) 64-Bit Server VM (build 25.241-b07, mixed mode)
```

Verify that you see the same Java version when you startup Apache Tomcat that you do when you
run java -version .  It's also a good idea to run javac -version to verify that you have installed
the full JDK and not just te JRE.

On AWS it helps to be reminded of which server you're on.  I use machine size as a reminder with

```sh
export HOST_TYPE=`curl http://169.254.169.254/latest/meta-data/instance-type`
PS1="$HOST_TYPE:"PS1
```
or on Vagrant, something like

```sh
PS1=Vagrant:$PS1
```

## Linux Installation

```sh
mkdir workspace
mkdir Programs
cd Programs
wget 'https://archive.apache.org/dist/tomcat/tomcat-8/v8.5.23/bin/apache-tomcat-8.5.23.zip'
wget 'http://wordnetcode.princeton.edu/3.0/WordNet-3.0.tar.gz'
wget 'http://wwwlehre.dhbw-stuttgart.de/~sschulz/WORK/E_DOWNLOAD/V_2.0/E.tgz'
tar -xvzf E.tgz
unzip apache-tomcat-8.5.23.zip
rm apache-tomcat-8.5.23.zip
cd ~/Programs/apache-tomcat-8.5.23/bin
chmod 777 *
cd ../webapps
chmod 777 *
cd ~/workspace/
sudo apt-get install git
git clone https://github.com/ontologyportal/sigmakee
git clone https://github.com/ontologyportal/sumo
git clone https://github.com/ontologyportal/TPTP-ANTLR
git clone https://github.com/ontologyportal/SigmaUtils
cd ~
mkdir .sigmakee
cd .sigmakee
mkdir KBs
cp -R ~/workspace/sumo/* KBs
me="$(whoami)"
cp ~/workspace/sigmakee/config.xml ~/.sigmakee/KBs
sed -i "s/theuser/$me/g" KBs/config.xml
cd ~/Programs
gunzip WordNet-3.0.tar.gz
tar -xvf WordNet-3.0.tar
cp WordNet-3.0/dict/* ~/.sigmakee/KBs/WordNetMappings/
cd ~/Programs/E
sudo apt-get install make
sudo apt-get install gcc
./configure
make
make install
cd ~
sudo apt-get install graphviz
echo "export SIGMA_HOME=~/.sigmakee" >> .bashrc
echo "export SIGMA_SRC=~/workspace/sigmakee" >> .bashrc
echo "export ONTOLOGYPORTAL_GIT=~/workspace" >> .bashrc
echo "export CATALINA_OPTS=\"$CATALINA_OPTS -Xmx10g\"" >> .bashrc
echo "export CATALINA_HOME=~/Programs/apache-tomcat-8.5.23" >> .bashrc
source .bashrc
cd ~/workspace/sigmakee
sudo add-apt-repository universe
sudo apt-get update
sudo apt-get install ant
ant
```

To test run

```sh
java  -Xmx10g -classpath /home/theuser/workspace/sigmakee/build/sigmakee.jar:/home/theuser/workspace/sigmakee/build/lib/* \
    com.articulate.sigma.KB -c Object Transaction
```

Start Tomcat with
```sh
$CATALINA_HOME/bin/startup.sh
```
Point your browser at http://localhost:8080/sigma/login.html


Debugging

- If login.html redirects you to init.jsp that means the system is still initializing. Wait a minute or two and try
again.
- If you are on mac and getting errors related to not finding jars when running com.articulate.sigma.KB, copy all jars
from /home/theuser/workspace/sigmakee/build/lib/ to /Library/Java/Extensions


# Vampire

If you want to use Vampire instead of or in addition to E, follow these instructions.
You may need to install the Zlib library if you don't have it already installed
```sh
sudo apt-get install libz-dev
```
and also possibly

```sh
sudo apt-get install g++
```
then execute the following:

```sh
cd $ONTOLOGYPORTAL_GIT
git clone https://github.com/vprover/vampire
cd vampire
make vampire_rel
mv vampire_rel_master* vampire
```

You'll then need to edit your config.xml file to point to the vampire executable.  Add the line

```xml
  <preference name="vampire" value="/home/theuser/workspace/vampire/vampire" />
```
editing the path to conform to your system


## MacOS install notes

See INSTALL.MacOS


## jEdit Integration (optional)

If you install jEdit (see http://jedit.org) and configure Sigma properly, you can click on a source
file and line number for a statement in the Sigma Browse page and be taken to
the editor, open on that line.  By default, the edit feature will make use of your $ONTOLOGYPORTAL_GIT
environment variable, and try to open the file in that location in the "sumo" module. Recommended practice
is to edit .kif files in your local Git repository and then copy them to the .sigmakee/KBs directory. If
you wish to edit in a different location, either a different repository or a different directory altogether,
the you can set the editDir configuration variable in your config.xml file, for example

```xml
  <preference name="editDir" value="/home/user/workspace/myproject" />
```
If you wish to install jEdit so that there's no path to it, for example on a shared machine as a user-specific
program, you'll need to set a path in config.xml, for example

```xml
  <preference name="jedit" value="/home/user/jedit/jedit" />
```

## jUnit testing on the command line

```sh
java  -Xmx8g -classpath \
  /home/theuser/workspace/sigmakee/build/sigmakee.jar:/home/theuser/workspace/sigmakee/build/lib/* \
  org.junit.runner.JUnitCore com.articulate.sigma.UnitTestSuite
```

one test method at a time can be run with help from the SingleJUnitTestRunner class,
for example

```sh
java -Xmx4g -classpath /home/apease/workspace/sigmakee/build/classes: \
  /home/apease/workspace/sigmakee/build/lib/* com.articulate.sigma.SingleJUnitTestRunner \
  com.articulate.sigma.KbIntegrationTest#testIsChildOf3
```
You will have to edit the resources files that correspond to config.xml to conform to your
paths.  They are in test/integration/java/resources/config*.xml and test/unit/java/resources/config*.xml

## RESTful Interface

see the SigmaRest project - https://github.com/ontologyportal/SigmaRest


## Python Interface

make sure you have python3

```sh
python --version
```

make sure you have pip or install with

```sh
sudo apt install python3-pip
```

install the py4j module

```sh
pip3 install py4j
```

Compile SigmaKEE then run with

```sh
java -Xmx7g -cp $SIGMA_SRC/build/classes:$SIGMA_SRC/build/lib/* com.articulate.sigma.KBmanager -p
```

then start python

```sh
user@user-machine:~/workspace/sigmakee$ python3
Python 3.8.2 (default, Jul 16 2020, 14:00:26)
[GCC 9.3.0] on linux

Type "help", "copyright", "credits" or "license" for more information.
>>> from py4j.java_gateway import JavaGateway
>>> gateway = JavaGateway()
>>> sigma_app = gateway.entry_point
>>> print(sigma_app.getTerms())

set([u'-1', u'-3', u'-6', u'-7235', u'.5', u'<=>', u'=>', u'AAA-Rating', u'AAM', u'AAV', u'ABPFn', u'ABTest', u'ACPowerSource', ...
```

Look at com.articulate.sigma.KBmanager.pythonServer() to expose the API of more classes
than just com.articulate.sigma.KB


## Account Management

> **Warning**
> Please note this is not working as of Apr, 2020.

Create the account database with

```sh
java -Xmx5G -cp $SIGMA_SRC/build/classes:$SIGMA_SRC/build/lib/* com.articulate.sigma.PasswordService -c
```

Then create the administrator account and password

```sh
java -Xmx5G -cp $SIGMA_SRC/build/classes:$SIGMA_SRC/build/lib/* com.articulate.sigma.PasswordService -a
```

You can use Sigma without being administrator, but you'll have limited use of its functionality.

You'll also need to set a few parameters in your config.xml file

```xml
  <preference name="dbUser" value="sa" />
  <preference name="loadFresh" value="false" />
```

To handle the account registration feature, you'll need to have an email account and supply the
password in the .bashrc file where your Sigma installation is runnning.  Gmail might be convenient
for this.  Change the password "my_pass" to your password on Gmail (or other service that you specify)

```sh
export SIGMA_EMAIL_PASS="my_pass"
export SIGMA_EMAIL_SERVER="smtp.gmail.com"
```

There are three types of user roles: "guest", "user" and "admin".  Guests are users who have not
registered.  They can access read-only functions that are not computationally expensive.
Registered users are granted access to computationally more expensive features.  Admin users
have access to all Sigma functions.  Currently, this control is hard coded into the JSP pages
that will check for user roles.  At some point in the future this may be changed to a more flexible
scheme of access rights driven from a file or database mapping roles to allowed functions.

You'll need to start the database server with

```sh
 java -jar h2-1.4.197.jar -webAllowOthers -tcpAllowOthers
```
and you'll need to change JDBCstring in PasswordService.java to your path instead of /home/apease
and recompile


## Old Installation Notes

Install via script from source on Linux or Mac OS with
```sh
bash <(curl -L https://raw.githubusercontent.com/ontologyportal/sigmakee/master/install.sh)
```
Note that you need to enter the entire statement above, including calling "bash".

Users should also see

https://sourceforge.net/p/sigmakee/wiki/required_data_files/
Mac instructions - https://sourceforge.net/p/sigmakee/wiki/Sigma%20Setup%20on%20Mac/ 
Ubuntu - https://sourceforge.net/p/sigmakee/wiki/Setting%20up%20Sigma%20on%20Ubuntu/

## User Interface

There's not enough documentation on Sigma so I'm starting a bit here in preparation for a real
manual.

Sigma has a number of functions controlled through its JSP-based interface.  

* [AddConstituent.jsp](web/jsp/AddConstituent.jsp)
  - adds a constituent SUO-KIF file to a knowledge base.  Accessible only to
    admin users.  It just responds to a command from another page and has no UI of its own.
    Redirects back to KBs.jsp when done.

* [AllPictures.jsp](web/jsp/AllPictures.jsp)
  - shows all the pictures linked to a term at once.  Accessible to all users.

* [ApproveUser.jsp](web/jsp/ApproveUser.jsp)
  - handles approving a new user.  Accessible only to admin users. Redirects
  to KB.jsp once acknowledge.

* [AskTell.jsp](web/jsp/AskTell.jsp)
  - interface to the local theorem provers. Accessible only to admin users. This function
  has not been well maintained and the interfaces to SystemOnTPTP and LEO-II may be out of date.

* [BrowseBody.jsp](web/jsp/BrowseBody.jsp)
  - shows terms, axioms, lexicon links, etc

* [BrowseExtra.jsp](web/jsp/BrowseExtra.jsp)
  - includes Prelude.jsp and Postlude.jsp

* [BrowseHeader.jsp](web/jsp/BrowseHeader.jsp)
  - primary ontology browser controls including term and word search, as well as
  the menu for selecting natural language and formal language

* [Browse.jsp](web/jsp/Browse.jsp)
  - top level browsing JSP that includes Prelude.jsp, BrowseHeader.jsp, BrowseBody.jsp
  and Postlude.jsp.  Really just a shell for the included JSPs

* [CCheck.jsp](web/jsp/CCheck.jsp)
  - interface to KBmanager.initiateCCheck() that initiates consistency checking of a KB.
  Handles selection of which theorem prover to use, and several parameters.  Includes Prelude.jsp
  and Postlude.jsp

* [CELT.jsp](web/jsp/CELT.jsp)
  - Obsolete.  Handles invocation of the Controlled English to Logic Translation system,
  which is now superceded by the Semantic Rewriting approach in the sigmanlp project.

* [CreateUser.jsp](web/jsp/CreateUser.jsp)
  - handles a request from a user to create an account.  Creates a guest account
  and sends mail to the moderator for approval.  Has no UI.

* [Diag.jsp](web/jsp/Diag.jsp)
  - Interface to run tests in Diagnostics.java.  Depends on Prelude.jsp and Postlude.jsp.
  Accessible for "admin" and "user" but not unregistered guest users

* [EditFile.jsp](web/jsp/EditFile.jsp)
  - Obsolete

* [EditStmt.jsp](web/jsp/EditStmt.jsp)
  - Obsolete

* [Graph.jsp](web/jsp/Graph.jsp)
  - Create graphs of binary relationships as a graphical view and as indented text.
  Relies on Prelude.jsp and Postlude.jsp

* [InferenceTestSuite.jsp](web/jsp/InferenceTestSuite.jsp)
  - run a suite of inference tests with the prover and parameters selected.
  Depends on Prelude.jsp and Postlude.jsp.  Accessible only for admin users.

* [init.jsp](web/jsp/init.jsp)
  - A status page with periodic automatic refresh to catch requests to Sigma during the
  process of initialization.

* [InstFiller.jsp](web/jsp/InstFiller.jsp)
  - Page to allow simple editing of ground formulas.  Deprecated.  Accessible only
  for admin users. Depends on Prelude.jsp and Postlude.jsp.

* [Intersect.jsp](web/jsp/Intersect.jsp)
  - Find appearances of two or more terms in the same axiom.  Depends on
  Prelude.jsp and Postlude.jsp.

* [KBs.jsp](web/jsp/KBs.jsp)
  - Main entry point for creating or selecting a knowledge base.  Some functions are
  available for "admin" or "user" roles but not "guest". Depends on Prelude.jsp and Postlude.jsp.

* [login.html](web/jsp/login.html)
  - login page for Sigma that handles existing accounts and new account registration.
  Existing accounts are dispatched to login.jsp and registrations are dispatched to
  Registration.jsp

* [login.jsp](web/jsp/login.jsp)
  - handle the login process by using PasswordService.jsp to validate a login against
  an H2 database of account info.  This page has no UI.

* [Manifest.jsp](web/jsp/Manifest.jsp)
  - UI for handling loading and saving KB constituents including saving in various
  exportable formats, such as OWL. Depends on Prelude.jsp and Postlude.jsp. Most functionality
  is limited to admin users.

* [Mapping.jsp](web/jsp/Mapping.jsp)
  - Use some simple string matching approaches to suggest equivalences between terms
  in two files.  Depends on Prelude.jsp and Postlude.jsp. Access limited to admin users.

* [MiscUtilities.jsp](web/jsp/MiscUtilities.jsp)
  - Depends on Prelude.jsp and Postlude.jsp. Most functionality
  is limited to admin users. Little utilities to generate dot graph files and OWL versions of
  the Open Multilingual Wordnet content.

* [ModeratorApproval.jsp](web/jsp/ModeratorApproval.jsp)
  - Functionality is limited to admin users. Dispatches to ApproveUser.jsp
  when user clicks "ok".

* [OMW.jsp](web/jsp/OMW.jsp)
  - Display results from the many languages in Open Multilingual Wordnet linked to
  WordNet synsets. Depends on Prelude.jsp and Postlude.jsp.

* [OWL.jsp](web/jsp/OWL.jsp)
  - Display all the axioms for a given term in OWL format, if expressible in that
  language.

* [Postlude.jsp](web/jsp/Postlude.jsp)
  - Encapsulates footing information displayed on most pages.

* [Prelude.jsp](web/jsp/Prelude.jsp)
  - Encapsulates header information displayed on most pages.

* [ProcessFile.jsp](web/jsp/ProcessFile.jsp)
  - No UI. Called from MiscUtilities.jsp to generate KIF from other
  formats. Calls DocGen.dataFileToKifFile() to do the real work. Depends on Prelude.jsp and
  Postlude.jsp. Access limited to admin users.

* [Properties.jsp](web/jsp/Properties.jsp)
  - An interface for setting many of Sigma's parameters.  Some of these are
  also accessible from the browsing interface, such as the language for the natural
  language paraphrases. Depends on Prelude.jsp and Postlude.jsp. Access limited to admin users.

* [Register.jsp](web/jsp/Register.jsp)
  - An interface to allow people to submit a request for registration and
  priviledges beyond that of unregistered guest users.  Calls on CreateUser.jsp

* [Save.jsp](web/jsp/Save.jsp)
  - not used

* [SimpleBrowseBody.jsp](web/jsp/SimpleBrowseBody.jsp)
  - Analogue to BrowseBody.jsp but showing only simple axioms in a simple
  and non-technical language

* [SimpleBrowseHeader.jsp](web/jsp/SimpleBrowseHeader.jsp)
  - Analogue to BrowseHeader.jsp for simple axioms

* [SimpleBrowse.jsp](web/jsp/SimpleBrowse.jsp)
  - Analogue to Browse.jsp for simple axioms

* [SystemOnTPTP.jsp](web/jsp/SystemOnTPTP.jsp)
  - Interface to the SystemOnTPTP system hosted as U. Miami that collects
  dozens of theorem provers with a common programmatic interface.  This code has not been
  maintained so use at your own risk. Depends on Prelude.jsp and Postlude.jsp. Access
  limited to admin users.

* [TreeView.jsp](web/jsp/TreeView.jsp)
  - A simple tree view of the taxonomic structure of the ontology with collapsable
  nodes.  Uses the SimpleBrowse.jsp code to display axioms for any node in the taxonomy.

* [WNDiag.jsp](web/jsp/WNDiag.jsp)
  - Diagnostics for WordNet. Depends on Prelude.jsp and Postlude.jsp. Accessible
  for "admin" and "user" but not unregistered guest users.

* [WordNet.jsp](web/jsp/WordNet.jsp)
  - show synsets and SUMO-WordNet mappings for a word. Depends on Prelude.jsp
  and Postlude.jsp.

* [WordSenseFile.jsp](web/jsp/WordSenseFile.jsp)
  - show word sense disambiguation and sentiment analysis results for a
  file of text.  Calls WordNet.wn.sumoFileDisplay() for the real work. Depends on
  Prelude.jsp and Postlude.jsp.

* [WordSense.jsp](web/jsp/WordSense.jsp)
  - show word sense disambiguation and sentiment analysis results for a
  sentence.  Calls WordNet.wn.sumoSentenceDisplay() for the real work. Depends on
  Prelude.jsp and Postlude.jsp.
