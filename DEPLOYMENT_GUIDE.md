# Deployment Guide for Smart Learning Planner

Your backend codebase is already fully structured for production hosting (proper `process.env.PORT` handling, clear start scripts, and structured environment variables like `MONGO_URI` and `CLIENT_ORIGIN`). 

Here are the step-by-step instructions to push your code to GitHub and host the backend on Northflank.

## Phase 1: Push To GitHub

If you haven't initialized version control yet, follow these steps in your terminal at the root directory of your project (`smart-learning-planner/`):

1. **Initialize Git & Commit:**
   ```bash
   git init
   git add .
   git commit -m "Initial commit for Smart Learning Planner"
   ```

2. **Create a GitHub Repository:**
   - Go to [GitHub](https://github.com/new).
   - Create a new repository called `smart-learning-planner` (can be public or private). 
   - **Do not** initialize it with a README, .gitignore, or license (leave it blank).

3. **Link and Push:**
   Back in your terminal, run the commands GitHub gives you under "…or push an existing repository from the command line", which usually look like:
   ```bash
   git remote add origin https://github.com/<your-username>/smart-learning-planner.git
   git branch -M main
   git push -u origin main
   ```

---

## Phase 2: Deploy Backend on Northflank

1. **Sign Up / Log In to Northflank:**
   - Go to [Northflank](https://northflank.com/) and create a free account.
   - Link your GitHub account when prompted.

2. **Create a New Project:**
   - Click **Create new project**.
   - Name it `smart-learning-planner`.
   - Select the free tier region available.

3. **Create a New Service:**
   - Inside your project, click **Create new service**.
   - Type: **Combined** (Build and deployment).
   - Name the service `backend-service`.

4. **Connect Repository:**
   - Source: **Git**.
   - Select your newly created `smart-learning-planner` GitHub repository.
   - Branch: `main`.

5. **Configure Build Settings (Crucial for Monorepos):**
   - Since your backend is in a subfolder, you need to tell Northflank where to find it.
   - **Build Type**: `Buildpack` or `Nixpacks` (It will automatically detect Node.js).
   - **Working Directory / Build Context**: change this to `/backend`.
   - Ensure the command it uses to start is `npm start` (this will run your `node src/server.js` script).

6. **Add Environment Variables:**
   Scroll down to the Environment variables section. You must add the following variables mirroring your local `.env`, but updated for production:

   - `NODE_ENV` = `production`
   - `MONGO_URI` = `mongodb+srv://...` (Your MongoDB connection string)
   - `JWT_ACCESS_SECRET` = `(generate a strong random string)`
   - `JWT_REFRESH_SECRET` = `(generate a strong random string)`
   - `CLIENT_ORIGIN` = `*` (Temporarily set this to `*` just to test. Later, once your frontend is hosted on Vercel or Netlify, change this to the deployed frontend URL, e.g., `https://smart-planner.vercel.app`).

7. **Network Settings:**
   - Under **Networking / Ports**, click "Add port".
   - Set the port to `5000` (Northflank will automatically set `PORT=5000` in the environment and map it to an external URL).
   - Check **Public** to ensure the internet can reach your API.
   - Protocol: **HTTP** / **HTTPS**.

8. **Deploy:**
   - Save and Deploy.
   - Northflank will now pull your code, run `npm install` inside the `backend` folder, and launch `npm start`.

9. **Verify:**
   - Once it shows "Running", check the **Logs** tab to verify you see `[db] Connected to MongoDB` and `[server] Listening on port 5000`.
   - Click the public `.northflank.app` URL provided in the top right corner. You can test it by adding `/api/health` to the end of the URL. It should return a success message.

## Next Steps
Once your backend is successfully hosted and you have the live API URL, you will go into your frontend, update the base API URL to point to this new Northflank URL, and then deploy your frontend (e.g., to Vercel or Netlify)!
