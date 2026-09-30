# Deploying the Rufiji flood mapper

Everything here is free. You need three accounts: Google (for Colab), GitHub, and Hugging Face.

- The **model files** are stored on the Hugging Face Hub. Model repositories are free; only Spaces that run
  apps are paid, so you don't need a Space.
- The **code** lives on GitHub.
- The **app** runs on Streamlit Community Cloud, which reads your code from GitHub and downloads the model
  from Hugging Face.

```
Colab notebook ──(export)──> Hugging Face Hub: model.pt, config.json, demo images
GitHub repo: app code ──> Streamlit Community Cloud: the running app ──> downloads the model
```

## Step 1: Export the model from Colab

1. On Hugging Face, click your profile picture → **Settings → Access Tokens → Create new token**,
   choose **Write**, and copy it.
2. Open your notebook in Colab. The trained models must be in memory, so either continue a session where you ran
   Sections 1–15, or select **Runtime → Run all** first.
3. Add a new cell at the end and paste in `tools/colab_export_model.py`. The model name is already set to
   `jovinvincent10/rufiji-flood-unet`.
4. Run the cell. Allow Google Drive access when asked, then paste your token into the box that appears.
5. Open https://huggingface.co/jovinvincent10/rufiji-flood-unet. You should see `model.pt`, `config.json`,
   `README.md` and a `demo` folder. Check the repository is **public** (Settings on that page).

## Step 2: Put the code on GitHub

1. Create a free account at [github.com](https://github.com/signup).
2. Select **New repository**, name it `rufiji-flood-mapper`, make it **Public**, and don't add a README
   (the project already has one).
3. On the empty repository page, select **uploading an existing file**, drag in everything from this folder
   **including the hidden `.streamlit` folder**, and select **Commit changes**.
   (On Mac, press Cmd+Shift+. in Finder to show hidden folders; on Windows, enable View → Hidden items.)
   If dragging a folder doesn't work, create the file on GitHub instead: **Add file → Create new file**, type
   `.streamlit/config.toml` as the name, and paste in the contents.
4. In `README.md` and `LICENSE`, replace `[Your name]` and `[Institution]`. You can edit files directly on
   GitHub with the pencil icon.

## Step 3: Deploy on Streamlit Community Cloud

1. Go to [share.streamlit.io](https://share.streamlit.io) and select **Continue with GitHub**. Allow access.
2. Select **Create app → Deploy a public app from GitHub**.
3. Fill in:
   - **Repository:** `YOUR-GITHUB-USERNAME/rufiji-flood-mapper`
   - **Branch:** `main`
   - **Main file path:** `app.py`
   - **App URL:** choose a name, for example `rufiji-flood-mapper` → `rufiji-flood-mapper.streamlit.app`
4. Open **Advanced settings**, choose **Python 3.11**, and in **Secrets** paste:
   ```toml
   FLOOD_MODEL_REPO = "jovinvincent10/rufiji-flood-unet"
   ```
   (The app already uses this by default; setting it here makes it easy to change later.)
5. Select **Deploy**. The first build installs PyTorch and takes about 5–10 minutes.

From now on, every change you commit on GitHub updates the app automatically.

## Step 4: Test it

1. Open your app link, choose **Example: Rufiji floods, April 2024**, and select **Map floods**.
   The first run downloads the model (a few seconds) and takes about a minute.
2. Check the numbers look sensible and the map sits on the Rufiji River.
3. Add the app link to the top of `README.md`, and share it. Anyone can use it without an account.

## If something goes wrong

Open the app, then **Manage app** (bottom right) to see the logs.

| Problem | What to do |
|---|---|
| "The model could not be loaded" | Check the model repository on Hugging Face is public and its name matches `FLOOD_MODEL_REPO` exactly. |
| Build fails while installing `torch` | Delete the first line of `requirements.txt` (`--extra-index-url ...`) and commit. The build takes longer but uses the standard PyTorch package. |
| "This app has gone over its resource limits" | Use a smaller area or turn off the uncertainty map, then **Reboot app** from Manage app. |
| The app is asleep | Free apps sleep after several days without visitors. Select **Yes, get this app back up**; it wakes in a minute or two. |
| The example button does nothing useful | Make sure the export in Step 1 created the `demo` folder; it needs the Rufiji data from Section 14 in memory. |

## Other hosting

The included `Dockerfile` runs the same app on any service that hosts Docker containers, including a paid
Hugging Face Space (Docker SDK, port 8501), if you ever need more memory or speed.
