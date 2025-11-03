
# Full-Stack Node.js & MongoDB Docker Project

This repository contains a full-stack application template utilizing Docker Compose to orchestrate a Node.js (Express) application, a MongoDB database, and a Mongo Express UI, complete with persistent volume storage.

This project sets up a multi-container environment perfect for development. It includes:

  * A **Node.js / Express backend** (`my-app`) that serves a profile page and provides a simple API.
  * A **MongoDB database** (`mongodb`) to store user data.
  * A **Mongo Express web UI** (`mongo-express`) to easily view and manage the database.
  * A **Named Docker Volume** (`mongo-data`) to ensure your database data persists even when the containers are stopped.

## 📦 Services Overview

The `docker-compose.yml` file defines and launches three services:

| Service | Image | Host Port | Description |
| :--- | :--- | :--- | :--- |
| **`my-app`** | Custom (`Dockerfile`) | `3002` | The Node.js (Express) application. Serves the `index.html` frontend and provides API endpoints. |
| **`mongodb`** | `mongo` | `27017` | The MongoDB database. Data is stored in the `mongo-data` volume. |
| **`mongo-express`** | `mongo-express` | `8081` | A web-based admin interface to manage the `mongodb` service. |

## 📋 Prerequisites

  * [Docker](https://www.docker.com/get-started)
  * [Docker Compose](https://docs.docker.com/compose/install/) (Included with Docker Desktop)

## 🚀 How to Run

### 1\. File Structure

Your `docker-compose.yml` file is configured to build the Node.js app from a directory named `./app`. When you clone this repository, all application files (`Dockerfile`, `server.js`, `package.json`, etc.) should be inside that folder.

Your project structure **must** look like this:

```
.
├── docker-compose.yml
├── README.md
└── app/
    ├── Dockerfile
    ├── index.html
    ├── package.json
    ├── package-lock.json
    ├── server.js
    └── images/
        └── profile-1.jpg
```

*(Note: The `server.js` and `index.html` reference an `images/profile-1.jpg`. Make sure that file and directory exist inside `/app` for the profile picture to load correctly.)*

### 2\. CRITICAL: Update `server.js` Connection

The provided `server.js` file is set to connect to `mongoUrlLocal`. This **will not work** inside Docker Compose. You must update it to use the service name `mongodb`.

1.  Open `app/server.js`.

2.  Find the `app.post('/update-profile', ...)` function.

3.  Change this line:

      * **Find:** `MongoClient.connect(mongoUrlLocal, mongoClientOptions, function (err, client) {`
      * **Replace with:** `MongoClient.connect(mongoUrlDockerCompose, mongoClientOptions, function (err, client) {`

4.  Find the `app.get('/get-profile', ...)` function.

5.  Change this line:

      * **Find:** `MongoClient.connect(mongoUrlLocal, mongoClientOptions, function (err, client) {`
      * **Replace with:** `MongoClient.connect(mongoUrlDockerCompose, mongoClientOptions, function (err, client) {`

The variable `mongoUrlDockerCompose` (`"mongodb://admin:password@mongodb"`) is already defined in `server.js` and is the correct one for this setup.

### 3\. Build and Run the Stack

From the root directory (where your `docker-compose.yml` file is), run the following command:

```bash
docker-compose up -d --build
```

  * `--build`: Forces Docker to build a fresh image for `my-app` from your `Dockerfile`.
  * `-d`: Runs the containers in detached mode (in the background).

-----

## 🚦 Accessing Your Services

Once the containers are running, you can access all parts of your stack:

  * ### **Node.js Application**

      * **URL:** `http://localhost:3002`
      * **Description:** This is the main user profile web page served by your Express app.

  * ### **Mongo Express (Database UI)**

      * **URL:** `http://localhost:8081`
      * **Login (from `docker-compose.yml`):**
          * **Username:** `user`
          * **Password:** `user`

  * ### **MongoDB (Direct Connection)**

      * **URL:** `mongodb://admin:password@localhost:27017`
      * **Description:** Use this connection string to connect directly to your database from an external client like MongoDB Compass.

## Managing the Environment

  * **To stop all services:**

    ```bash
    docker-compose down
    ```

  * **To stop services AND delete the database volume (WARNING: all data will be lost):**

    ```bash
    docker-compose down -v
    ```
