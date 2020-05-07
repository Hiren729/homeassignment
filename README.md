# Home Assignment

## How to setup separate Production and Testing environments by integrating Github-Jenkins-Docker.

```
NOTE: It is assumed that you know how to install Jenkins, Docker, Git. 
You already have a Github Repository and a fair idea on Linux OS to be able to understand this setup.
```
## Understanding the problem statement

So what is our requirement ?
Basically, we want changes made by the developer on the feature branch in the local repo to be automatically uploaded on github and simultaneously put on the testing environment. 
If the quality is assured by QA team we want it to be deployed on the Production Environment automatically.

## SETUP

This setup can be explained as divided into two parts:

* Continuous Development ( which involves use of Git and Github ) 
* Continuous Integration ( which involves use of the tool Jenkins )

To automate the process, use of Docker containers with httpd image has been done to launch webserver environments quickly

In Github, we create a webhook which allows Github to inform Jenkins if there is a difference that occurs between origin(Github) and local repo versions of the files.

![Job1 config](/images/2.jpg)
![Job1 config](/images/2.jpg)

Coming to  the automated Integration part, directories are created on the Base OS where the Docker containers are installed.
The various required services are started ( i.e. Jenkins, Docker).

## Creation of Jenkin jobs

### First, we setup the first Job which is to copy the changes in the feature branch on Github.
Changes are downloaded to Developercode project and a new test environment is set up using docker httpd image. 

```
NOTE : It is recommended to create a new testing environment
```

![Developercode config](444.jpg)
![Developercode config](445.jpg)
![Developercode config](446.jpg)

```

* To copy all the files to test folder linked/mounted to the testing environment:

sudo cp -rvf * /developerchanges

* To remove old test environment (if any) :

if sudo docker ps -a | grep testingenv
then
sudo docker rm -f testingenv
else
echo "no env exists"
fi

* To run new test server using httpd os image:

sudo docker run -dit -p 8085:80 -v /developerchanges:/usr/local/apache2/htdocs --name testingenv httpd
```
### Moving on to the next job, which is to detect changes to master branch and download the files to its workspace which are then transferred to the production environment. This job is Downstream to the QA job and executes only after that in the pieline  

![Mastercode config](450.jpg)
![Mastercode config](451.jpg)
![Mastercode config](452.jpg)

```
* To copy all the files to productionchanges folder linked/mounted to the docker Production environment:

sudo cp -rvf * /productionchanges

* To run new Production env using httpd docker OS:

if sudo docker ps | grep productionenv
then
echo "already running"
else
sudo docker run -itd -p 8084:80 -v /productionchanges:/usr/local/apache2/htdocs/ --name productionenv httpd
fi
```

### Now the nextjob is to merge the developer code to the master code. This is a very challenging step as it involves lots of conceptual knowledge to implement this.

![QA config](C:\Users\KIIT\Pictures\Home_Assignment\447.jpg)
![QA config](448.jpg)
![QA config](449.jpg)


## To put in a nutshell !

We assume that the developer is maintaining branches from the last checkpoint (the code already tested) so the developer creates a feature branch and commits to the changes made
and pushes it to github maintaining two branches of the same code. 

![Commit and Push](443.jpg)

As soon as the jobs are done running we can view the changes through the browser to the outside world (Client/ Public facing).  

![Job1 config](453.jpg)
![Job1 config](454.jpg)
![Job1 config](455.jpg)

### Key Tips :

* We can create a public IP with ngrok to make the production page visible to the outside world :
./ngrok http 8080

![ngrok config](ngrok.jpg)

* Use of local hooks can help in faster pushing of files to the CVCS or Github

```
gedit .git/hooks/<file_name>
```
* Saving the above created file name within __**" "**__ should be keept in mind whie using Windows Notepad


### Production server updated:
![Job1 config](/images/15.jpg)
