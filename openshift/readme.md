```
$ oc new-app https://github.com/jankleinert/concession-kiosk-backend --name backend -e MONGODB_USER=concession -e MONGODB_PASSWORD=hello1234 -e DATABASE_SERVICE_NAME=mongodb -e MONGODB_DATABASE=concession
$ oc new-app https://github.com/jankleinert/concession-kiosk-frontend --name frontend -e COMPONENT_BACKEND_HOST=backend -e COMPONENT_BACKEND_PORT=8080
$ oc process -f template.yaml -p MONGODB_USER=concession -p MONGODB_DATABASE=concession -p MONGODB_PASSWORD=hello1234 -p NAMESPACE=kiosk | oc apply -f - 
$ oc import-image registry.access.redhat.com/rhscl/mongodb-36-rhel7 --confirm
```

