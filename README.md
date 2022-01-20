VuejsVSProject

Howto create it?
- From VS Studio, create a js Vuejs Project
- Delete everything inside, keep file njsproj only
- Using Vite to create the project code
  - npm init vite@latest <project-name> -- --template vue
  - Move everything in the just create folder <project-name> into our js Vuejs Project folder
  - Configuring vite.config.js for host, port and watch
- Create Dockerfile and docker-compose

Attention: do not run directly, run through docker-compose instead