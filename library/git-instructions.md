# Setting up Git for submissions


1. Install Git on your system (https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

1. Sign up for GitHub (https://github.com/).

1. Go to https://education.github.com/ and sign up for the Student Developer Pack to get unlimited private repositories. You are a "student" and you want an "individual account". Once you have completed these first steps, you are then ready to create your private GitHub repository for this class.

1. Locally on your machine, clone this ROM repository: `git clone git@github.com:sdobnik/aics-project.git`. This will create a copy of the repository on your own computer.

1. On the GitHub website, log in and create a **private** remote repository called *aics-project*. Make sure that the box Initialize this repository with a README is **unticked**. Ignore Quick setup instructions on the following page.

1. Add me (*sdobnik*), Mehdi (*mmehdig*) and Wafia (*ASIREM16*) as a collaborator for this repository (check out settings on the repo website).

1. Back in the terminal, set the origin of the repository that you cloned from me to be your remote repository that you just made. Change USERNAME below to your username. This tells git which remote repository to push your changes to when you `git push`. Enter the following command `git remote set-url origin https://github.com/USERNAME/aics-project.git`.

1. Now you are ready to start working on your repository using the standard procedure: `git pull`, `git add lab1`, `git commit -am "Project draft."`, `git push` (sometimes `git push remote origin`).

1. Check that the files have been updated in your remote github repository by navigating to https://github.com/USERNAME/aics-project (change USERNAME to your username)



## Submitting your work

1. Let us know though GUL submission when you would like to submit your project, inclusing a commit id, e.g. `c310619433ab7210fd1106d6061a3abb6aecdac2` which you get by typing `git log`.

1. We will provide comments either through GUL or in the `README.md` here. Try to keep all information in these two places (rather than random email messages) as this will be helpful to keep track of ideas when it comes times to wrote the report.

1. Note that GitHub cannot host files more than 100 MB. If you try to push a file larger than this, GitHub will complain.


Th guide has been adapted from https://github.com/rlbarter/stat215a
