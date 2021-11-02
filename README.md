# api-client-generator

Scripts to generate OpenAPI clients using [@openapitools/openapi-generator-cli](https://www.npmjs.com/package/@openapitools/openapi-generator-cli) and publish them to NPM.

## Usage

### Clone the repository
`git clone https://github.com/Student-Management-System/api-client-generator`

### Populate openapi.json
The OpenAPI specification of each API is expected to be stored inside of **\<folder>/openapi.json**.  
You can paste the specification there manually or use the following script:  
```
sh fetch-openapi <folder> <url>

Examples:
sh fetch-openapi student-mgmt http://localhost:3000/api-json
sh fetch-openapi exercise-submitter-server https://jenkins-2.sse.uni-hildesheim.de/view/Teaching/job/Teaching_exercise-submitter-server/lastSuccessfulBuild/artifact/target/openapi.json
```  

### Generate a client
Run the following script, which will generate the client, install dependencies and build it. Additionally, the README.md inside the dist folder will be overwritten with the README.md from **\<folder>**. 
```
sh generate <folder>

Example:
sh generate student-mgmt
```

### Publish to NPM
1. `cd` into **\<folder>/generated/dist**
2. Inspect **package.json** inside this folder and perform necessary changes (i.e. version number).
3. Login with `npm login`
4. Publish with `npm publish --access public`
