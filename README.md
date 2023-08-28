- This is a new feature
- This feature #4
- This should be version 2.5.0
- This should be version 2.6.0
- This will fail
- Event bridge is not needed



how many services?
language: go
02 dockerfiles
view documentation
no special instruction
code located in GitHub

Devops
 auth
 mysql
 ui
 weather

  code---> image ---> application

actions
 create a Jenkins pipeline job
 configure weekhoob with the application repository
 scan the code for vulnerabilities
 build images
   auth
   mysql
   ui
   weather
 publish images to image registry
 update the helm charts
 notification on slack
