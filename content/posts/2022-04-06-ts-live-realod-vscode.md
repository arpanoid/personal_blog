---
title: "Debug express typescript projects in VS Code with watch and live reload"
date: 2022-04-06T15:25:33-04:00
---

## VS Code set up to live reload individual typescript based express apps within single repo

With the advent of micro services, Many set ups have emerged to take an advantage of individual app development and deployment while sharing code between the apps. There are more than one way to approach it but the `monorepo` setup is the most common and most widely adopted solution to manage individually versioned apps into a single repository. 

For many valid reasons which are beyond the scope of this post, we've opted to not use any fancy tools like `lerna` or `nx` and instead opted for following set up to manage multiple apps/projects within single shared package.json.

```
root 
|___project1
|    -- README.md
|    -- index.ts
|    -- tsconfig.json    
|___project2
|    -- README.md
|    -- index.ts
|    -- tsconfig.json    
|__ utils
|    -- index.ts
│ -- README.md
│ -- index.ts  
| --base-tsconfig.json
| --package.json
```

Our requirements to locally debug the apps were
* Ability to debug and live reload individual projects. If `project1` is being debugged, vs code debug should only build `project1` related dependencies and reload the process upon changes in its dependency tree. 
* Issues in the project outside of the dependency tree of the project being run on

So far, there are many solutions available online to live reload the node/express typescript apps but none of it satisfied the above requirements. This is the solutions I've opted for which is working great so far.

### Pre-requisite
The app should be able to start/stop standalone. Each project's `index.ts` must have a way to start express app. This is what we have in our `index.ts` for each project. 

```
export const startApp = () => {
  const port = 8015;
  app.listen(port, function () {
    console.log(`Application running at http://localhost:${port} - Boot Time: ${Date.now() - st} ms`);
  });
};

if (process.env.RUN_LOCAL === '1') {
  startApp();
}
```

Now if the `RUN_LOCAL` is set then it will bring up the app locally. When it is deployed to cloud, this env is not set but instead a separate handler is exported which will be used by serverless setups.

### Add a task in task.json

* Add a task to watch typescript changes for each project. This can be done with vscode's inbuild `tsc-watch`

```
    {
      "label": "tscWatch_project1",
      "type": "typescript",
      "tsconfig": "./project1/tsconfig.json",
      "problemMatcher": ["$tsc-watch"],
      "option": "watch"
    },
```

### Add a new config in launch.json

* Add a new launch config to attach debugger to the process. For this, I am using `nodemon` to compile and live reload app once the ts changes are successfully compiled by `tsc-watch`.

```
    {
      "name": "project1",
      "type": "node",
      "request": "launch",
      "runtimeExecutable": "${workspaceFolder}/node_modules/.bin/nodemon",
      "program": "${workspaceFolder}/project1/index.ts",
      "outFiles": ["${workspaceFolder}/dist/**/*.js"],
      "args": ["-l", "${workspaceFolder}/project1/"],
      "restart": true,
      "console": "integratedTerminal",
      "sourceMaps": true,
      "internalConsoleOptions": "neverOpen",
      "preLaunchTask": "tscWatch_project1",
      "env": {
        "RUN_LOCAL": "1",
      },
      "outputCapture": "std"
    },
```