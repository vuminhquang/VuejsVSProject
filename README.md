VuejsVSProject

Howto create it?
# Way 1: using js project
- From VS Studio, create a js Vuejs Project
- Delete everything inside, keep file njsproj only
- Using Vite to create the project code
  - npm init vite@latest <project-name> -- --template vue
  - Move everything in the just create folder <project-name> into our js Vuejs Project folder
  - Configuring vite.config.js for host, port and watch
- Create Dockerfile and docker-compose

Attention: do not run directly, run through docker-compose instead


# Way 2: Using SPAProxy (ASP.NET project instead of js project)
- Reference: https://github.com/johanndev/asp_vite, but have some changes
- ASP.NET Core Angular Project, now call the project folder as root folder
- In the ClientApp Folder, save the contents of aspnetcore-https.js (or copy the file to somewhere outside of the ClientApp folder).
- In root folder:
  - `npm init vite ClientApp`
  - using vuejs
  - confirm override
- Restore aspnetcore-https.js to the ClientApp directory.
- Replace the contents of vite.config.ts with:
```
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import { readFileSync } from 'fs'
import { join } from 'path';

const baseFolder =
  process.env.APPDATA !== undefined && process.env.APPDATA !== ''
    ? `${process.env.APPDATA}/ASP.NET/https`
    : `${process.env.HOME}/.aspnet/https`;

const certificateName = process.env.npm_package_name

const certFilePath = join(baseFolder, `${certificateName}.pem`);
const keyFilePath = join(baseFolder, `${certificateName}.key`);

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue()],
  server: {
    https: {
      key: readFileSync(keyFilePath),
      cert: readFileSync(certFilePath)
    },
    port: 5002,
    strictPort: true,
    proxy: {
      '/api': {
        target: 'https://localhost:5001/',
        changeOrigin: true,
        secure: false
      }
    }
  }
})
```
- Add new start scripts to `package.json`:
```
"scripts": {
	"prestart": "node ./aspnetcore-https.js", // <--- ADD
	"start": "vite --debug",				  // <--- ADD
	"dev": "vite",
	"build": "vue-tsc --noEmit && vite build",
	"serve": "vite preview"
}
```
- In `launchSettings.json`, change the applicationUrl port to 5001
- Edit file `csproj` to change listening port of the SPAProxy to 5002 (the port in vite.config.ts)
- Run `npm install` in the `ClientApp` folder

Project can now start
