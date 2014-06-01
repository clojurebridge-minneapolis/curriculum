# Module 8: Putting Your Application Online

## What is deployment?

Your app is live. Great! But right now, it runs on your local machine only. If you want others to see and use it, you'll need to deploy it. The quickest way to deploy your app is to push it to Heroku using Git.

There are two ways you can experiment with deployment:
1. You just completed Module-7 and you are building the chat app from the ground up
1. Cloning the latest chat version from github

### You just completed Module-7 and you are building the chat app from the ground up

There are a few changes we need to make since the <a href="outline/web-app-notes.md#store-and-display-messages" target="_chat">Module-7 changes to the chat app</a> to get ready for Heroku.

1. We need to specify a newer Leiningen version (in ```project.clj```):
   ```:min-lein-version "2.3.4"```
1. We need to move the dependency on Java servlets (in ```project.clj```) from the :dev
   profile to the general ```:dependencies``` (so it will work in ```:dev``` and ```:prod```:
   ```[javax.servlet/servlet-api "2.5"]```
1. We added one more style improvement in [handler.clj](https://github.com/clojurebridge-minneapolis/chat/blob/master/src/awesome/handler.clj):
   We wrapped the ```map``` function with this hiccup ```[:div.well (map ...)]```
1. We need to add a file called ```Profile``` which tells Heroku how to run the application.
   You can create this file in a terminal window like this:
   ```echo "web: lein with-profile production trampoline ring server-headless" > Procfile```

The Procfile tells Heroku that:
* we will run a web application
* using Leiningen and
* run it with the production profile (that's detailed in project.clj)
* once Leiningen has set everything up it should "trampoline" to the web application (as Leiningen won't be needed any more)
* the Leiningen command is ```ring server-headless``` which will run the application just like
  ```ring server``` except it won't try to open the URL in a web browser (because there isn't
  a web browser on Heroku -- just the server).

Now the chat application is ready to go, but we need to check it into
git so Heroku can use it. Let's create a new github repository on your
account to store the chat application. (NOTE: While this isn't strictly necessary
to deploy to Heroku it makes it easier to transfer these skills to
another project). Here are the steps to check the chat application into git:

1. Log in to github: https://github.com/login
1. Click the **+** next to your username on the upper right and choose *New Repository*.
1. Choose a name for your repository... One easy idea is **chat-NICKNAME**  where NICKNAME is
   your github userid.
1. Check the box: *Initialize this repository with a README*
1. Add .gitignore (select **Clojure**)
1. Add a license (select **MIT License**)

Now let's add files to the new git repo. In a terminal window (be sure to put the actual directory
where the chat application is in place of DIRECTORY and your github username in place of NICKNAME):
````
$ cd DIRECTORY
$ git clone git@github.com:NICKNAME/chat-NICKNAME
$ git add .
$ git status
````

Here ```git status``` should show you all the files you are about commit to the repo.
This gives you a chance to add any files you might have missed, remove any files you
don't want to commit (or add them to your .gitignore). You can actually make the commit like this:

````
$ git commit -a -m 'first commit'
````

Now all of the chat application files have been checked into git. Now let's push
all that code up to github!
````
$ git push
````

Now you should be able to see your repo on github at https://github.com/NICKNAME/chat-NICKNAME

Since git has been set up you can skip the next section and go directly to **Deploying to Heroku**

### Cloning the latest chat version from github

Cloning the latest version of the chat application is a great way to test
publishing an application to Heroku. In this way you can get all the code
and it should "Just Work(TM)". We'll start by making the parent directory
for the chat application and then we'll clone it:
````
$ mkdir -p ~/src/github/clojurebridge-minneapolis
$ cd ~/src/github/clojurebridge-minneapolis
$ git clone https://github.com/clojurebridge-minneapolis/chat.git
$ cd chat
$ ls
````

The ```ls``` command should show you all the files in the chat directory
(including project.clj, Procfile, etc...). Now you are ready to Deploy to Heroku...

# Deploying to Heroku

Now that git has been setup for the chat application we need to initialize
it for Heroku and give it a name.  Below you will see chat-NICKNAME, but you can
substitute your github username for NICKNAME. In a terminal window where your chat
application is located do this:
````
heroku apps:create chat-NICKNAME
git push heroku master
heroku open
````

When you run ```git push heroku master``` all the files that have been
checked into git will be sent to Heroku and Heroku will run
the application (based on the instructions in Procfile).

The ```heroku open``` will simply open the URL for your application
in a browser window. You can also open the URL by hand: http://chat-NICKNAME.herokuapp.com/

*FYI:* See what ```handler.clj``` looks like now by viewing the current branch
[master](https://github.com/clojurebridge-minneapolis/chat/blob/master/src/awesome/handler.clj)

### Next Step:

Yay! Your application is now live on the web!

Back to [**Module 7:** More Functions](functions2.md)
