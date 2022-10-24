+++
title = "Projects"
slug = "projects"
+++
<br>

# Composed

![composed](images/composed.png)

A simple, and an elegant web-based GUI for docker-compose. Create your microservice powered systems diagramettically and let `Composed` generate the docker-compose configuration files.

## Tech Stack
![react](images/structure.png) ![rust](images/rust.png)

- React-Flow is the chosen front-end framework.
- A rust based WASM library is used to convert user defined diagrams to `docker-compose.yaml` files.
- The app is deployed on Vercel using custom build scripts to accomodate the usage of WASM libraries.

#### [Demo](https://always-composed.vercel.app/)
#### [Code](https://github.com/mukkund1996/composed)

# CTRL+C - CTRL+V

![copypaste](images/copypaste.gif)

A progressive web app to enable cross-platform copying and pasting of text.

## Tech Stack
![react](images/structure.png) ![node](images/node.png) ![mongo](images/mongo.png)

- React is used as the frontend framework of choice.
- An express app based on NodeJs is used to create a REST API web server to perform CRUD operations on the database.
- A NoSQL Mongo database is used to store the text snippets defined by the user.
- Heroku is used as PaaS. The client (static html files) and the webserver is served by a single server. 

#### [Demo](http://ctrl-c-ctrl-v.herokuapp.com)
#### [Code](mukkund1996/copy-paste)
