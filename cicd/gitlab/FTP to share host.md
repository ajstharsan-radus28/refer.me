For **static website hosted on GitLab**, and need to **automatically deploy it to a shared hosting server via FTP**,
the easiest way is to use **GitLab CI/CD with an FTP deployment job**.

---

## ✅ Overview: Simple Steps

1. 📁 Store your static website files in your GitLab repo.
2. 🛠️ Set up GitLab CI (`.gitlab-ci.yml`) to deploy via FTP.
3. 🔐 Store your FTP credentials in GitLab **CI/CD Variables**.
4. 🚀 On every push (e.g. to `main`), it uploads to your FTP server.

---

## 🧱 Example: `.gitlab-ci.yml` File

Create a `.gitlab-ci.yml` file in your repo root:

```yaml
stages:
  - deploy

deploy-via-ftp:
  stage: deploy
  image: alpine:latest
  before_script:
    - apk add --no-cache lftp
  script:
    - lftp -c "set ftp:list-options -a;
              open -u $FTP_USERNAME,$FTP_PASSWORD $FTP_HOST;
              mirror -R --delete --verbose ./public/ $FTP_DIR"
  only:
    - main
```
**CAUTION : the --delete directive may remove existing files in your host which doesn't exist in repo ***

### 🔍 What this does:

* Installs `lftp` (a lightweight FTP client)
* Connects to your server using credentials
* Uploads everything from `./public/` to your shared hosting folder via FTP
* Deletes removed files to keep it in sync
* Only runs on `main` branch

---

## 🔐 Add Your FTP Credentials to GitLab

1. Go to your GitLab project
2. Click **Settings > CI/CD > Variables**
3. Add the following variables:

| Key            | Value                     |
| -------------- | ------------------------- |
| `FTP_USERNAME` | your FTP username         |
| `FTP_PASSWORD` | your FTP password         |
| `FTP_HOST`     | ftp.yourdomain.com        |
| `FTP_DIR`      | path on server (e.g. `/`) |

✅ Mark them as **protected** and **masked** if appropriate.

---

## 🧪 Folder Structure

Make sure your repo looks something like:

```
.gitlab-ci.yml
/public
  ├── index.html
  ├── style.css
  └── ...
```

If your static files are in a different folder (e.g., `dist/` or `build/`), update the `./public/` path in the CI script accordingly.

---

## 🎉 That's It!

Now every time you `git push` to `main`, GitLab will:

* Run the FTP deployment
* Sync your static site to your hosting

---

## 💡 Optional Improvements

* ✅ Add branch filters (e.g., deploy only on `production`)
* ✅ Add `.lftpignore` if you want to ignore certain files

---

Let me know:

* What build folder you're using (e.g., `public/`, `build/`, etc.)
* If you'd like to deploy only on tag or release

I’ll customize the config for you!
