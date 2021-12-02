## Local SonarQube Analysis

1. Install Docker & install docker-composer by brew


    brew install docker
    
    brew install docker-compose


2. Install Docker's latest official version and SignUp to Your Docker account.

3. Create a docker-compose.sonar.yml file:

```text
version: '3'
services:
  sonarqube:
    container_name: sonarqube
    image: sonarqube:latest
    ports:
      - '9000:9000'
      - '9092:9092'
```

You can start and stop SonarCube by these commands:

    # Start it ...
    docker-compose -f docker-compose.sonar.yml up -d
    
    # Stop it ...
    docker-compose -f docker-compose.sonar.yml down


You can reach the admin site on http://localhost:9000 You can also open the admin panel directly on this URL http://localhost:9000/dashboard use admin/admin to login. After the first login it will ask password changing.

**Note that the local SonarQube instance uses in-memory data storage**. So if you restart the Docker container it will lose the settings and user details you added to its settings.


## Run SonarQube Analysis

You should create a file named sonar-project.properties in your project's root directory. Its content can be something similar:

```text
sonar.projectKey=secure-typescript-boilerplate
sonar.projectName=secure-typescript-boilerplate
sonar.projectVersion=1.0
sonar.language=ts
sonar.sources=src
sonar.sourceEncoding=UTF-8
sonar.exclusions=src/**/*.test.ts
sonar.test.inclusions=src/**/*.test.ts
sonar.coverage.exclusions=src/**/*.test.ts,src/**/*.mock.ts,node_modules/*,coverage/lcov-report/*
sonar.typescript.lcov.reportPaths=coverage/lcov.info
sonar.testExecutionReportPaths=coverage/test-reporter.xml
```

Now, you need to install the SonarQube scanner. It is an NPM package you can easily add to your project:

    npm install --save-dev sonarqube-scanner 

You should create a new JavaScript file in your's project root. Its name should be sonar-project.js Its content can be something like this:

```javascript
const sonarqubeScanner = require('sonarqube-scanner');

sonarqubeScanner(
  {
    serverUrl: process.env.SONAR_SERVER || 'http://localhost:9000',
    token: process.env.SONAR_TOKEN || '',
    options: {}
  },
  () => {
    console.log('>> Sonar analysis is done!');
  }
);
```

Now you can run **sonarqube-scanner** with `node sonar-project.js`, which will submit our code sonar server. The local server is used by default, but this can be overridden via environment variables. Go on and test it to make sure it works.

 
You also can install sonarqube-verify. It starts the code analysis and then checks its progress every few seconds. On completion, it either succeeds or fails based on the analysis result.

It can be helpful if you create a command for this in the project's package.json:

```text
{
  ...
  "scripts": {
    "sonar": "sonarqube-verify"
  }
}
```

Now You can run the analysis:


```bash
# Generate the test coverage report
npm test

# Run the analysis (remember to start SonarQube)
npm run sonar

# Visit http://localhost:9000/projects to see the result 
# login with admin/admin
```