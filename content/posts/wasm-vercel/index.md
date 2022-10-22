+++ 
draft = false
date = 2022-09-13
title = "Deploying a WASM-Powered React App on Vercel"
description = ""
slug = ""
authors = []
tags = ["vercel","wasm","rust","cicd"]
categories = []
externalLink = ""
series = []
+++
### _A step-by-step guide to getting your WASM-react app out into the world!_

![1.png](images/1.png)

Over the past few weeks, I have been building a React app supercharged by functions written in Rust. Now, first things first, a little background…

# What Is Web Assembly?

According to webassembly.org, it is defined as the following

_"WebAssembly (abbreviated Wasm) is a binary instruction format for a stack-based virtual machine. Wasm is designed as a portable compilation target for programming languages, enabling deployment on the web for client and server applications."_

In short, you can compile your high-performance code in statically compiled languages like C++, Rust, or Go into a web assembly format. It is highly portable and is expected to execute at native speeds.

Needless to say, I was very excited about this relatively new technology. Therefore, I created a React app that uses web assembly code as a “pseudo-backend.” Although, in my case, one might say it is an overkill, it is a great learning opportunity nevertheless.

Now let’s get started with the guide.

## Directory Structure
'''
src/
└── components
    ├── ComposePrompt
    ├── Controls
    ├── Edge
    ├── Node
    └── styles
wasm-parser/
└── src/
'''

As you might expect, we have two different subpackages within the project:

The package created by the create-react-app. It is managed by npm and there will be an associated package.json.
The library was created and managed by cargo — wasm-parser. In this directory, the Rust code that needs to be compiled into web assembly can be specified.

# Calling WASM Functions on React
Various detailed articles explain how you can call web assembly compiled functions from javascript and vice-versa. In any case, I will summarise the broad details.

## Cargo library — `wasm-parser`

```
#[wasm_bindgen]
pub fn generate_configuration(node_data: String, edge_data: String) -> String {
    let nodes: Vec<Node> = parse_nodes(&node_data);
    let edges: Vec<Edge> = parse_edges(&edge_data);
    let docker_compose_obj = generate_dockercompose_yml(nodes, edges);
    let output_string = serde_yaml::to_string(&docker_compose_obj).unwrap();
    return output_string;
}
```

The function generate_configuration is called from the React client. The macro used before the function — wasm_bindgen — enables high-level interaction between JS- and WASM-compiled code.

## Compiling to WASM
The Rust library can be compiled into WASM using a tool called wasm-pack. The build command can be directly added to the npm package.json file for convenience.

```
"scripts": {
    ...    
    "build:wasm": "cd wasm-parser && ~/.cargo/bin/wasm-pack build --target web --out-dir ./wasm-build"
}
```

Furthermore, the compiled package needs to be specified as a dependency of the node package.

```
"dependencies": {
    ...
    "wasm-parser": "file:./wasm-parser/wasm-build"
}
```

## React client

On the client side, the WASM-compiled function can be easily called by importing it from the dependency that was added to the package.json file earlier.

```

// WASM modules
import init, { generate_configuration } from "wasm-parser";
// Calling the function 
init().then(() => {
    const rusty_str = generate_configuration(stringifiedNodes, stringifiedEdges);
});
```

# Deploying to Vercel
Now for the fun part of deploying your app for everyone to enjoy it. For this example, I have used Vercel as the platform to set up a continuous deployment pipeline and subsequently deploy a production build of my app. To avoid vendor lock-in, the same procedure can be followed to deploy it on other platforms.

## Create a Vercel account

The first step is to create an account on Vercel and link it to GitHub. This ensures that the latest changes are reflected in the deployed version. Note: This is not necessarily the best option in production, but it suffices to do so for a hobby project.
Add your GitHub project from the generated list of repositories on Vercel.
Select the template based on the framework that you based your front-end client on. In my case, I used the create-react-app template.

## Modify the build step

We need to override the default build step to build and link the WASM-compiled code to your React client. To facilitate and standardise the build step, I created a custom bash script that can be run to complete the production build. I will explain each step below:

## Install Rust

Vercel uses Amazon Linux 2 as a base image for its environments. Rustup must be installed in the environment before we can compile to WASM.

```
echo "Installing Rustup..."
# Install Rustup (compiler)
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y
# Adding binaries to path
source "$HOME/.cargo/env"
```

## Install wasm-pack

```
echo "Installing wasm-pack..."
# Install wasm-pack
curl https://rustwasm.github.io/wasm-pack/installer/init.sh -sSf | sh -s -- -y
```

## Build the WASM package

```
echo "Building wasm-parser..."
# Build wasm-parser 
npm run build:wasm
```

## Complete the production build

```
echo "Build static frontend client..."
# Build static html for the react client
npm run build
```

# Voilà! You are Done.

If the build logs are clean and you don’t have any errors, your WASM-powered React app should now be deployed!

## Want to Connect?

Thank you for reading my article. You can also find me on LinkedIn and my work on GitHub.

