# Installing Packages
When you run a command to install a node package, it will create a "package-lock.json" file + a "node_modules" folder/directory

#### In short, the package-lock.json file lists all the details about our project's dependencies. The package-lock.json file should be checked into git, along with package.json (ENSURE node_modules DIRECTORY IS [gitignored](https://sebhastian.com/git-ignore-node_modules/#:%7E:text=The%20node_modules%2F%20folder%20is%20the,t%20write%20to%20the%20repository.)). 

We should always avoid editing the package-lock.json this file directly. We modify it indirectly via commands like npm install.