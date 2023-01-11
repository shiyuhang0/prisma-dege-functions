# Prisma Edge functions

> Modified from [prisma-edge-functions](https://github.com/ruheni/prisma-edge-functions). Thanks for their excellent work.

Edge functions are serverless functions at The Edge. They are written in JavaScript or TypeScript and run on a global network. It allows running server-side code geographically close to end users and enjoy lower latency.

This sample application shows how you can work with TiDB Cloud and edge functions.


## Set up TiDB Cloud

1. Go to [TiDB Cloud](https://tidbcloud.com/) and create a serverless tier cluster.
2. Navigate to your serverless tier cluster's dashboard.
3. Click **Connect** to set a password, you can also build the connect string with this pattern `mysql://USER:PASSWORD@HOST:PORT/edge_function?sslaccept=strict`.

![tidbcloud-connect.png](img/connect.png)

4. Connect to your serverless tier cluster and create a database named edge_function. This application will build on this database.

<details>
  <summary><h2>Deploy on Local</h2></summary>

1. Fork this repository and clone it to your local machine.
    
    ```
    git clone https://github.com/${user_name}/prisma-edge-functions.git
    cd prisma-edge-functions
    ```
   
2. Set DATABASE_URL environment variables.

    ```
    export DATABASE_URL=mysql://<User>:<Password>@<Endpoint>:<Port>/edge_function?sslaccept=strict
    ```

3. apply prisma migrate and seed.

    ```
    npx prisma migrate deploy
    npx prisma db seed
    ```
   
4. [Set up Prisma Data Proxy](#how-to-set-up-prisma-data-proxy)

5. Replace the `DATABASE_URL` with the connection string you got from Prisma Data Platform.

   ```
   export DATABASE_URL=DATABASE_URL=prisma://aws-us-east-1.prisma-data.com/?api_key=•••••••••••••••••
   ```

6. Generate Prisma Client that will connect through the Prisma Data Proxy using HTTP.

   ```
   npx prisma generate --data-proxy
   ```
   
7. Start the app.

    ```
    npm run dev
    ```

🎉 Mission Completes.

The app is now running, navigate to http://localhost:3000/ in your browser to explore it.

</details>

<details>
  <summary><h2>Deploy on Vercel</h2></summary>

The Deploy button will take you through Vercel's project creation flow. Vercel will help to clone this job to your own GitHub repository and automatically deploy it.

[![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https%3A%2F%2Fgithub.com%2Fshiyuhang0%2Fprisma-edge-functions&env=MIGRATE_DATABASE_URL,DATABASE_URL&envDescription=TiDB%20Cloud%20and%20prisma%20data%20proxy%20connect%20string%20)

1. Click the **Deploy** button.
2. Click **GitHub** button and authenticate GitHub account.
3. Select your **Git Scope** and fill in **Repository Name** for your own GitHub repository.
4. Click **Create** to create the git repository.
5. [Set up Prisma Data Proxy](#how-to-set-up-prisma-data-proxy) with the GitHub repository you create in step 4.
6. Enter the environment variables:
   - `MIGRATE_DATABASE_URL`: the tidb cloud connection string.
   - `DATABASE_URL`: the Prisma Data Proxy connection string.
7. Click **Deploy**.

🎉 Mission Completes.

Now wait for the deployment to complete, then you can view your site on the default domain generated by Vercel.

</details>

## How to set up Prisma Data Proxy

Edge environments only support HTTP-based connections. Therefore, we can not connect to TiDB Cloud which use long-lived TCP connections directly.

The Prisma Data Proxy is a proxy server for your database that allows you to interact with your database over HTTP, manages a connection pool, and ensures database connections are reused. It allows database access from Edge runtimes such as Vercel Edge Functions, Netlify Edge Functions and Cloudflare Workers.

1. sign in to the [Prisma Data Platform](https://cloud.prisma.io/) and click **New Project**.
2. Fill in the **Connection string** with this pattern `mysql://USER:PASSWORD@HOST:PORT/edge_function?sslaccept=strict`. You can find the connection information in your [TiDB Cloud console](https://tidbcloud.com/console/clusters).
3. Leave the **Static IPs** as disabled because TiDB Cloud Serverless Tier is accessible from any IP address.
4. Select a Data Proxy region that is geographically close to your TiDB Cloud cluster location. Then click **Create project**.

   ![prisma_project.png](img/prisma_project.png)

5. Fill in the repository in the Get Started page. Then click **Link Prisma schema**.
6. Click **Create a new connection string** and you will get a new connection string that starts with `prisma://.` Copy this connection string and save it for later.

   ![prisma_start.png](img/prisma_start.png)

7. Click **Skip and continue to Data Platform** to go to the Data Platform.