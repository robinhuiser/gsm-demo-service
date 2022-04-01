# gsm-demo-service

Demo service using Git Submodule to store generated (code-first) OAS 3.x files.

## Initial setup (one time only)

~~~bash
# Add central OAS repo as Git Submodule
# This is a one time setup
$ git submodule add \
   https://github.com/robinhuiser/gsm-demo-oas-repo.git

# Let's commit this change to the repo
$ git add .gitmodules
$ git add ./gsm-demo-oas-repo
$ git commit -m "Added git submodule"
$ git push -u origin main
~~~

## Do a release / build

A pipeline needs to checkout the Git repository for the component AND submodules, see:

~~~bash
# Check out the component (service) repo
$ git clone https://github.com/robinhuiser/gsm-demo-service.git 

# You will notice the ./gsm-demo-oas-repo is empty...
# Let's update and init the submodule with the commit ID present in the .gitsubmodules file
$ git submodule update --init --recursive
~~~

Next, we can "release" a new API spec for our component - see the steps below what a pipeline typically would do for you:

~~~bash
# Fetch the latest and greatest OAS updates from the submodule
# This assures we commit against main (for demo now)
$ cd ./gsm-demo-oas-repo
$ git pull origin main
$ git checkout main
$ cd ..

## -- Now we have in the project's .gitsubmodules file an updated 
## -- Commit ID of the latest version

# Let's "build" our project to generate a new OAS
$ PROJECT_NAME=$(basename `git rev-parse --show-toplevel`)
$ PROJECT_TAG=$(git rev-parse --short HEAD)
$ echo "New OAS spec" > ./gsm-demo-oas-repo/${PROJECT_NAME}-${PROJECT_TAG}.spec

# If the build was successful, let's commit the change to the Git submodule
$ cd ./gsm-demo-oas-repo
$ git add ${PROJECT_NAME}-${PROJECT_TAG}.spec
$ git commit -m "Added OAS spec for ${PROJECT_NAME}"
$ git push -u origin main
$ cd ..

# Now we push our component repo changes 
# which contains a pointer to the just updated submodule repo
$ git add ./gsm-demo-oas-repo
$ git commit -m "Updated submodule gsm-demo-oas-repo"
$ git push -u origin main
~~~

That's all folks!
